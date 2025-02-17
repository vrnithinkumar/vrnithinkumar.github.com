---
layout:     post
title: Find null smart pointer dereferences with the Clang Static Analyzer[GSoC2020]
subtitle: Final Report-Google Summer of Code 2020
date: 2020-08-31 12:00:07
author:     "Nithin"
header-img: "llvm.png"
catalog: true
tags:
    - LLVM
    - Clang
    - GSoC2020
    - Static-Analysis
---
- My GSoC work on GitHub: [Commits](https://github.com/llvm/llvm-project/search?q=author%3Avrnithinkumar+author-date%3A2020-06-01..2020-08-31&unscoped_q=author%3Avrnithinkumar+author-date%3A2020-06-01..2020-08-31&type=Commits)
(Maybe the link changes, if that is the case please search for: “[author:vrnithinkumar author-date:2020-06-01..2020-08-31]” in the repository of the LLVM project)
- My GSoC work on Phabricator: [Reviews](https://reviews.llvm.org/differential/query/UwCbJYKaRcDb/)
- [GSoC Project Page Link](https://summerofcode.withgoogle.com/projects/#5376312975294464)
- [Original report link](https://docs.google.com/document/d/1WZSt45kZUhg0UbOv0HXBhyEYaHrb-G-TpEhj_nU041Q/edit?usp=sharing)
## Abstract
The [Clang Static Analyzer](https://clang-analyzer.llvm.org/) is used to find bugs in the program by analyzing source code without compiling and executing. It uses symbolic computations to find the defects. Analyzer covers a variety of checks targeted at finding security and API usage bugs, dead code, null dereference, division by zero, and other logic errors. The Clang Static Analyzer already has a checker to find the null pointer dereference in code, however it is not sufficient for higher abstractions such as C++ smart pointers or optionals. By explicitly teaching the C++ standard class behaviors we can make the Analyzer to find more bugs related to modern C++ code.
## Goal
Enable Clang Static Analyzer to find the occurrences of null smart pointer dereferences by teaching the observed behaviors of C++ smart pointer classes. Improve the analyzer’s ability to realize the values of the standard smart pointers without having to dig deep into the complex implementation details. We should be able to find more null dereference bugs related to the smart pointers while reducing the number of false positives. Should be able to cover at least one class fully eg. `std::unique_ptr`, and then extend it to `std::shared_ptr` or the `std::optional` if time permits. 
## Summary
Within the GSoC time period, we could not implement the modeling for all smart pointer methods and operators as we planned. Since the problem was more complicated than what we all anticipated. So far the majority of the modeling for std::unique_ptr is  implemented and committed. We were able to find some promising results with that. We found **8** true positive warnings related to smart pointer null dereference in [LLVM](https://github.com/llvm/llvm-project) project and **5** warnings in the [WebKit](https://webkit.org/) project. Even though we were only able to support `std::unique_ptr` so far, we accomplished to build a base modeling for the smart pointer checkers. And this will act as a consolidated foundation for developing checkers for any C++ objects that are passed by value. That is one of the first conscious attempts to do so and we've gained a lot of experience and managed to maintain our integrity - in the sense that the code ended up mostly free of hacks. It could most likely be generalized to modeling the entire C++ standard library. This does not give us the high-level architecture that'll be needed to deal with the scale of the standard library, but we got our low-level basics right. Also this work will be used to add support for checking other smart pointers `std::shard_ptr`, `std::weak_ptr` as well as `std::optionals`. Also it can be used to build a checker for use-after-free errors. 
## Research
### Smart Pointer
A Smart pointer is an abstract data type that simulates a pointer with additional automatic memory management. It manages a dynamically allocated object and ensures the dynamically allocated object is properly cleaned up. Such features are intended to reduce bugs caused by the misuse of pointers while retaining efficiency. Smart pointers typically keep track of the memory they point to, and may also be used to manage other resources, such as network connections and file handles.
### Unique Pointer
A `unique_ptr` is a smart pointer that owns and manages another object through a pointer and disposes that object when the `unique_ptr` goes out of scope. It should be used to own and manage any dynamically allocated object when its ownership is not shared. A `unique_ptr` explicitly prevents copying of its contained pointer (as would happen with the normal assignment), but the move assignment and move constructor can be used to transfer ownership of the contained pointer to another `unique_ptr`.

**Example: 1 Dereferencing default-constructed unique pointer which is null**
```cpp
int foo(bool flag) {
  std::unique_ptr<int> x; // note: Default constructed unique pointer is null
  if (flag) // note: Assuming 'flag' is false;
    return 0; // note: Taking false branch

  return *x;  // warning: Dereferenced smart pointer 'x' is null.
}
```
**Example: 2 Dereferencing a unique pointer after calling release()**
```cpp
int bar() {
  std::unique_ptr<int> x(new int(42)) ;  // valid unique pointer;
  x.release(); // note: smart pointer 'x' become null and the ownership of 
               // the memory is not transferred. And it causes a memory leak 
               // which should be warned.
  return *x;   // warning: Dereferenced smart pointer 'x' is null. 
}
```
Similar to above cases, other possible cases are dereferencing after calling `std::move()`, `reset()` or `reset(nullptr)`, getting and explicitly deleting inner pointer, or swapping with null pointer using `std::swap` ([more examples in Appendix](/2020/08/31/gsoc-report/#Appendix)). Above all cases will result in a crash.
## Design
The basic idea of the checker is to keep a record of raw pointers wrapped inside the smart pointers using a map between smart pointer and corresponding inner pointer. Update the map by enumerating all situations when the smart pointer becomes null, as well as the situations when the smart pointer becomes non-null. For example when a smart pointer is default constructed track that smart pointer as it has a null inner pointer. Then check if any of the tracked pointers dereferenced has a null value. To make the bug report more clear attach additional details along the bug path to provide more detailed information on where the smart pointer becomes null.
## Alternative Solution Considered
Another possible solution considered was manipulating symbolic values inside the smart pointer. The limitation that we run into here is that our memory model ("RegionStore") doesn't currently allow setting a "default" binding to a whole object when it's a part (say, a field) of a bigger object. This means that we have to understand how the smart pointer works internally (which field corresponds to what) to manipulate its symbolic value, which ties us to a specific implementation of the C++ standard library. This might still work for a unique pointer which probably always has exactly one field, but for shared pointers it is not the case and has multiple fields. So it depends on the different implementations of the C++ standard library. It has been decided to not go with this approach since this approach is challenging and potentially a lot of work compared to the first approach.
## Implementation
### Initial Smart Pointer Modeling and Checker
[D81315](https://reviews.llvm.org/D81315): Created a basic implementation. 
- Made a separate checker class for emitting diagnostics. Used the new checker to use `checkPreCall` and put bug reporting logic there.
- Kept all smart pointer related modeling logic in `SmartPtrModeling`. Shared common functionality via a header file shared between the `SmartPtrModeling` and `SmartPtrChecker`. 
- Made a `SmartPtrModeling` as a dependency to `SmartPtrChecker`.
- Introduced a GDM with `MemRegion` as key and `SVal` as value to track the smart pointer and corresponding inner pointer.  
- Also added support to model `unique_ptr` constructor, release and reset methods.
- Used `evalCall` to handle modeling. As part of this enabled constructor support in `evalCall` event with [D82256](https://reviews.llvm.org/D82256). 
- Implemented `checkDeadSymbols` to clean up the `MemRegion` of smart pointers from the program state map when they go out of scope. Keeping the data structures in the program state as minimal as possible so that it would not grow to a great size while analyzing real code and eventually slows down the analysis.

With this patch, the model can emit warnings for cases like use after default constructor, use after release, or use after the reset with a null pointer. Kept the `SmartPtrChecker` under *alpha.cplusplus* package and smart pointer modeling have to be enabled by the `ModelSmartPtrDereference` flag.
### checkRegionChanges for SmartPtrModeling
[D83836](https://reviews.llvm.org/D83836): Implemented `checkRegionChanges` for `SmartPtrModeling`. To improve the accuracy, when a smart pointer is passed by a non-const reference into a function, removed the tracked region data. Since it is not sure what happens to the smart pointer inside the function.
```cpp
int foo() {
  std::unique_ptr<int> P; // note: Default constructed unique pointer is null
  bar(&P); // note: Passing by reference
  return *P; // No warnings
}
```
For example here in the code above, we are passing a default constructed `unique_ptr` ‘P’ to method `bar()`. But it is unknown whether the `unique_ptr` ‘P’ is reset with a valid inner pointer or not inside `bar()`. To avoid false positives we are not producing any warning on dereference of `unique_ptr` ‘P’ after `bar()`.
### Modeling for unique_ptr::swap method
[D83877](https://reviews.llvm.org/D83877): Enabled the `SmartPtrModeling` to handle the swap method for `unique_ptr`. The `swap()` method can be used to exchange ownership of inner pointers between the `unique_ptrs`. So it is possible to make a `unique_ptr` null by swapping with another null `unique_ptr`. With this patch warnings are emitted when a `unique_ptr` is used after swapping with another `unique_ptr` with null as an inner pointer.
### NoteTag for better reporting
[D84600](https://reviews.llvm.org/D84600): With NoteTags added more detailed information on where the smart pointer becomes null in the bug path. Introduced a `getNullDereferenceBugType()` inter-checker API to check if the bug type is interesting.
*After adding NoteTags:*
![alt text](/2020/08/31/gsoc-report/notetag.png "NoteTags")
### Modeling for unque_ptr::get()
[D86029](https://reviews.llvm.org/D86029): Modeled to return tracked inner pointer for the `get()` method. The `get()` method is used to access the inner pointer. When the inner pointer is used with conditional branching or other symbol constraining methods we can use the constraints on the inner pointer to find whether the corresponding `unique_ptr` is null or not. When the inner pointer value for a `unique_ptr` is available from the tracked map we bind that value to the return value of `get()` method. Also made changes to create `conjureSymbolVal` in case of missing inner pointer value for a `unique_ptr` region we are tracking.
*Example:*
![alt text](/2020/08/31/gsoc-report/get.png "Get")
### Modeling for unique_ptr bool conversion 
[D86027](https://reviews.llvm.org/D86027): Modeling the case where `unique_ptr` is explicitly converted to bool. It is a common practice to check if a `unique_ptr` is null or not before accessing it. If the inner pointer value is already tracked and we know the value, we can figure out the corresponding boolean value. And the analyzer will take the branch based on that. Using `SValBuilder::conjureSymbolVal` to create a symbol when there is no symbol tracked yet and we constrain on that symbol to split the Exploded Graph with assuming null and non-null value.
### Adding support for `checkLiveSymbols`
[D86027](https://reviews.llvm.org/D86027): Implemented `checkLiveSymbols` to make sure that we are keeping the symbol alive until the corresponding owner `unique_ptr` is alive to avoid removing the constraints related to that symbol. Also when the `unique_ptr` goes out of scope, we make sure the symbols are cleaned up.
```cpp
void foo() {
    int *RP = new int(12);
    std::unique_ptr<int> P(RP);
    if (P) { // Takes true branch
    }
}
```
For example here in the code above, we have to keep the symbol for `RP` alive since that is tracked as the inner pointer value of `unique_ptr` P. We have to use the constraints on that symbol to decide whether the branching takes the true or false branch. 
### Modeling of move assignment operator (unique_ptr::operator=)
[D86293](https://reviews.llvm.org/D86293): Modeled how the unique_ptr moves the ownership of its managed memory to another `unique_ptr` via `=` operator. With the move assignment operator a `unique_ptr` can be reset with another `unique_ptr` whereas the assigned `unique_ptr` will lose its ownership of its inner pointer and become null. Also it is possible to assign `nullptr` to a `unique_ptr` and reset it to null. Made changes to update the tracked values of both LHS and RHS side `unique_ptr` values of the operator.
### Modeling for unique_ptr move constructor
[D86373](https://reviews.llvm.org/D86373): Similar to the `=` operator, modeled how the `unique_ptr` moves the ownership of its managed memory to another via move constructor. Then tracked the moved unique_ptr’s inner pointer value as null.

## Evaluation
The checker has been evaluated on a number of open-source software projects which use smart pointers extensively([symengine](https://github.com/symengine/symengine), [oatpp](https://github.com/oatpp/oatpp), [zstd](https://github.com/facebook/zstd), [simbody](https://github.com/simbody/simbody), [duckdb](https://github.com/cwida/duckdb), [drogon](https://github.com/an-tao/drogon), [fmt](https://github.com/fmtlib/fmt), [re2](https://github.com/google/re2), [cppcheck](https://github.com/danmar/cppcheck), [faiss](https://github.com/facebookresearch/faiss)). Unfortunately(or fortunately) the checker did not produce any warnings which are not false positive. But we found **8** true positive warnings related to smart pointer null dereference in [LLVM](https://github.com/llvm/llvm-project) project and **5** warnings in the [WebKit](https://webkit.org/) project.

*(Attaching few example warnings)*
**Warnings in LLVM**
*Warning-1: clang/lib/Analysis/Consumed.cpp*
![alt text](/2020/08/31/gsoc-report/llvm_1.png "LLVM Warning-1")
*Warning-2: clang/lib/Lex/Preprocessor.cpp*
![alt text](/2020/08/31/gsoc-report/llvm_2.png "LLVM Warning-2")
*Warning-3: llvm/utils/TableGen/OptParserEmitter.cpp*
![alt text](/2020/08/31/gsoc-report/llvm_3.png "LLVM Warning-3")
**Warnings in WebKit**
*Warning-1:*
![alt text](/2020/08/31/gsoc-report/wk_1.png "WebKit Warning-1")
*Warning-2:*
![alt text](/2020/08/31/gsoc-report/wk_2.png "WebKit Warning-2")
*Warning-3:*
![alt text](/2020/08/31/gsoc-report/wk_3.png "WebKit Warning-3")
*Warning-4:*
![alt text](/2020/08/31/gsoc-report/wk_4.png "WebKit Warning-4")

## Future Work
### Model remaining methods of unique_ptr
So far we covered the important methods related to `unique_ptr`, but still there exist few more methods and operators on `unique_ptr` to cover. Remaining methods are `std::make_unique`, `std::make_unique_for_overwrite`, and `std::swap`. Remaining operators include `operator*`, `operator->`, and all the comparison operators.
### Model other smart pointers
Extend the modeling for `std::shared_ptr`, `std::weak_ptr` and `std::optional`. Right now `SmartPtrModeling` only models most of the `std::unique_ptr`, adding modeling for other smart pointers will make the checker complete.
### CallDescriptionMap support for CXX Constructor and Operator
To enable the checker by default we have to use `CallDescriptionMap` for `evalCall`. Right now we are manually implementing the name matching logic that has been already implemented in `CallDescriptionMap`. But the support for the *Constructor* and *Operator* calls are not supported yet and changes are in review([D81059](https://reviews.llvm.org/D81059), [D80503](https://reviews.llvm.org/D80503)) by [Charusso](https://reviews.llvm.org/p/Charusso).
### Enabling the checker by default
The SmartPtrChecker is under the *alpha.cplusplus* package and smart pointer modeling has to be enabled by the `ModelSmartPtrDereference` flag. Enabling the checker by default will benefit codebases that use smart pointers. 
### Inlined defensive checks
We are using `trackExpressionValue()` to track how an inner pointer expression for a unique_ptr is getting null in the bug report. The `trackExpressionValue()` is suppressing some warnings to avoid the false positives with inlined defensive checks.

For example the code below is a true positive warning.
```cpp
int foo(std::unique_ptr<int> P) {
  if (P) {} // Assuming P is null.
  return *P; // warning: null dereference
}
```
On the other hand, the code below is a false positive warning. We cannot infer `unique_ptr` Q is null in `bar()` based on the check `if(P)` in function call `foo()`. So the warning should be suppressed.
```cpp
void foo(std::unique_ptr<int> P) {
    if (P) {} // Assuming P is null
}

int bar(std::unique_ptr<int> Q) {
    foo(Q);
    return *Q; // warning: null dereference
}
```
Right now we are trusting `trackExpressionValue()` when it suppresses reports. It may occasionally suppress true positive warnings, but it's better than having false positives. 
Below code is an example for a suppressed true positive warning.
```cpp
int *return_null() {return nullptr;}

int foo() {
  std::unique_ptr<int> x(return_null());
  return *x; // no-warning
}
```
We have to investigate more on real world projects and see `trackExpressionValue()` is sufficient for suppressing all false positive warnings related to inlined defensive checks.
### Marking regions as not interesting
Right now there is no API similar to `markInteresting()` for marking the region not interesting in a bug report. With this support, our checker can remove less useful and unwanted notes showing in the report. For example, when a `unique_ptr` is referenced after the `release()` we don't have to show a note tag on the `unique_ptr` constructor unless it is constructed with null.

*Before:*
![alt text](/2020/08/31/gsoc-report/ntbefore.png "Before marking not interesting")
*After: marking P not interesting*
![alt text](/2020/08/31/gsoc-report/ntafter.png "After marking not interesting")
### Communication with MallocChecker
When raw pointers are accessed from `unique_ptr` via `get()` or `release()`, we have to ensure that the raw pointers are tracked via `MallocChecker`. Also, enable SmartPtrModeling to communicate the deallocation to `MallocChecker` when we see the destructor call of the `unique_ptr` and it has a default deleter. Also, communicating with `MallocChecker` could potentially find double-free errors when the same pointer is passed to multiple unique_ptrs or it is also freed independently of the `unique_ptr` ([example](/2020/08/31/gsoc-report/#Double-free-error-example)).

### Add modeling for user-defined custom smart pointers
Many C++ projects have their own custom implementations of smart pointers similar to [boost::shared_ptr](https://www.boost.org/doc/libs/1_61_0/libs/smart_ptr/shared_ptr.htm) or [llvm::IntrusiveRefCntPtr](https://llvm.org/doxygen/classllvm_1_1IntrusiveRefCntPtr.html). If the user can specify the custom smart pointers and methods on it, we could reuse the existing `SmartPtrModeling` for modeling and checking the custom smart pointers.
## How to use
All the changes are in the master. But the checker and modeling are not enabled by default. Checker is under the *alpha.cplusplus* package and smart pointer modeling has to be enabled by the `ModelSmartPtrDereference` flag. 

**Checker and modeling can be enabled explicitly:**
```bash
$scan-build -enable-checker alpha.cplusplus.SmartPtr -analyzer-config cplusplus.SmartPtrModeling:ModelSmartPtrDereference=true clang -c test.cpp
```
(Since `SmartPtrChecker` is depended to `SmartPtrModeling` we don't have to explicitly enable `SmartPtrModeling`)

## Acknowledgment
I want to express my gratitude towards everyone that helped me with this project, but especially to 3 individuals: My mentors, [Artem Dergachev](https://github.com/haoNoQ), [Gábor Horváth](https://github.com/Xazax-hun), and [Valeriy Savchenko](https://github.com/SavchenkoValeriy). With their guidance, I’ve learned a lot about how Clang Static Analyzer works during the summer. I got to skype with them every Monday and received all the help and suggestions. When I got stuck with issues I got immediate help even on the weekends. Also, I received very fast feedback for my review requests. I am also thankful to [Kristóf Umann](https://reviews.llvm.org/p/Szelethus/) for tips and comments on the reviews.

Thank you very much for the support and mentoring.
## Appendix
### Potential bugs with unique_ptr

**A default constructed unique pointer has null value**
```cpp
int foo() {
    std::unique_ptr<int> x; // note: Default constructor produces a null unique pointer
    return *x; // warning: Dereferenced smart pointer 'x' is null.
}
```
**Unique pointer constructed with null value**
```cpp
int foo() {
    std::unique_ptr<int> x(nullptr);  // note: Default constructor produces a null unique pointer
    return *x;  // warning: Dereferenced smart pointer 'x' is null.
}
```
**Unique pointer constructed with move constructor**
```cpp
int foo() {
    std::unique_ptr<int> y(new int(13));  
    std::unique_ptr<int> x(std::move(y)); // note: unique_ptr y is moved to x
    return *y; // warning: Dereferenced smart pointer 'y' is null.
}
```
**release**
```cpp
int foo() {
    std::unique_ptr<int> x(new int(13));  
    x.release(); // note: unique_ptr x is null after release
    return *x; // warning: Dereferenced smart pointer 'x' is null.
}
```
**reset**
```cpp
int foo() {
    std::unique_ptr<int> x(new int(13));  
    x.reset(nullptr); // note: unique_ptr x is null after reset to null
    return *x;  // warning: Dereferenced smart pointer 'x' is null.
}
```
**swap**
```cpp
int foo() {
    std::unique_ptr<int> x(new int(13));
    std::unique_ptr<int> y; 
    x.swap(y); // note: x is null after swapping with null y
    return *x; // warning: Dereferenced smart pointer 'x' is null.
}
```
**get**
```cpp
int foo() {
    std::unique_ptr<int> x;
    if(!x.get())
        return *x; // warning: Dereferenced smart pointer 'x' is null.
    return 0;
}
```
**operator bool**
```cpp
int foo() {
    std::unique_ptr<int> x;
    if(!x)
        return *x; // warning: Dereferenced smart pointer 'x' is null.
    return 0;
}
```
### Double-free error example
```cpp
void foo() {
  int *i = new int(42);
  std::shared_ptr<int> p1(i);
  std::shared_ptr<int> p2(i); // When p2 goes out of scope it will try 
                              // to delete the inner pointer which is already deleted by p1.
}
```
