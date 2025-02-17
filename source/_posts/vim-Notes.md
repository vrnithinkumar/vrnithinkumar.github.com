---
layout:     post
title: VIM Notes 
date: 2018-05-06 22:55:04
subtitle: "\" All my VIM related notes\""
author:     "Nithin VR"
header-img: "dotnet.jpg"
catalog: true
tags:
	- vim
---
# VIM Notes
Writing down about all my vim tips here. **It will be updated regularly**
## Modes
Vim has different modes to manipulate the text. Normal Insert and Visual are the main modes.

### Normal Mode
Default or natural resting state. All the commands are running there to manipulate the text.

### Insert Mode
Mode to edit add and correct text. Where you can type.
- `Ctrl + h` Delete back one char.
- `Ctrl + w` Delete a word.
- `Ctrl + u` Delete to beginning of the line.
- `Ctrl + [` Change to normal mode.
- `Ctrl + o` Insert Normal mode.
- `Ctrl + r, {register}` is used to paste the in insert mode from register specified.

### Insert Normal Mode
Just one command we can execute from the Insert mode.
`Ctrl + o, zz` will move the current line to the center.

### Visual Mode
Easy to manipulate in character level, line level and rectangular blocks. Many operations and commands will be work similar to Normal mode.
`v` will change in to the visual mode char level.
`V` will change in to the visual mode line level.
`Ctrl + v` will change in to the visual mode block level. But in windows `Ctrl + q` will make it to block visual mode.
`o` will toggle the free end in the visual mode.
                    
## Basic Actions
- `hjkl` - for moving around
- `w` - jump a word.
- `$` - at the end of line
- `^` - beginning of the line.

### Advanced Movements
- ` `. ` - jump to last change position.
- `ma` - Mark the current curser location as "a".
- ` `a` - Jump to mark named as "a".
- `:marks` - List all marks.

### Append
- `a` is used for appending.
- `A` for appending to the end of the line. `$a` is another way to achieve this.

### Insert
- `i` is used for inserting.
- `I` is used for inserting in the first of line. `^i` is equivalent to this.

### Dot to repeat
- `.` will repeats last executed action. Will repeating the last action VIM will consider all changes inside an insert mode to exit as single action. 
- It will repeat every keystroke inside the insert mode.

### Undo
- `u` for undoing. From the moment we enter Insert mode until we return to Normal mode, everything we type (or delete) counts as a single change.

### Delete
- `d`     id used for deleting a character.
- `dd`    will delete the whole line.
- `dw`    will delete a word.
- `daw`   will delete a word including the space around it.
- `diw`   will delete a word not without the space.

### Finding a char using f and t
- `fx` will find the char x in the line to find the next match use `;`
- `,` will use the last character search.

### Indentation 
- `>` is used for indentation.
- `<` left shift
- `=` Auto indentation.
- `>G` will increases the indentation from the current line until the end of the file.

### Yank
- copy from current line to the n'th line
  1. `y20G`      this will yank from current line to 20th line.
  2. `:.,20y`    same with range, `:[range]y[ank] [x]`.
  3. `"[register]y` will copy to the register specified. eg : `"*y` - Will copy to system clipbord(register *).

### Simple increment and Decrement
- `Ctrl + a` will increment the number under the cursor.
- `Ctrl + x` will decrement the number under the cursor.

### Search and Replace
`:[range]s[ubstitute]/{pattern}/{string}/[flags] [count]` format for the search and replace command.

**Flags**
- `c` confirm on each substitution.
- `g` replace all occurrences.
- `i` ignore case for pattern.

`:%s\old\new\g` : will replace the "old" with "new" in all document. 
### AutoComplete in vim
- Auto word completion
- Auto line completion
- Auto file completion
## Registers
Registers are essentially the names memory spaces in VIM to save and re-use the texts. Registers are being accessed by `"`.
- `"ry` - will yank the selected text to register named "r".
- `"rp` - will paste the content in register "r" in normal mode.
- `Ctrl + r` - will paste the data from register "r".
### Common registers
1) `*` - System clipboard.

## Macro

## Sessions
Sessions are used to save the current state of vim and restore it when you needed.
- `:mks` is used to create a session for your vim editor. 
    eg: `:mks ~\vimsessions\bar.vim`
- `:source` is used to restore the session which you saved. 
    eg: `:source ~\vimsessions\bar.vim`

## VIM plugins

### Vim-OrgMode
**Plain List:**
- `<localleader> cl or <CR>` - insert plainlist item below
- `<localleader> cL or <C-S-CR>` - insert plainlist item above

**Checkboxes:**
- `<localleader> cc` - toggle status
- `<localleader> cn or <CR>` - insert checkbox below
- `<localleader> cN or <C-S-CR>` - insert checkbox above

**Dates:**
- `<localleader> sa` - insert date
- `<localleader> si` - insert inactive date

`<localleader>` is `\` for by default.

## Split Screen 
**with the `<C-w>` key** 
- `<Ctrl-w>n`       - `:new` horizontal split (editing a new empty buffer)
- `<Ctrl-w>s`       - `:split` window horizontally (editing current buffer)
- `<Ctrl-w>v`       - `:vsplit` window vertically (editing current buffer)
- `<Ctrl-w>c`       - `:close` window
- `<Ctrl-w>o`       - close all windows, leaving :only the current window open
- `<Ctrl-w>w`       - go to next window
- `<Ctrl-w>p`       - go to previous window
- `<Ctrl-w><Up>`    - go to window above
- `<Ctrl-w><Down>`  - go to window below
- `<Ctrl-w><Left>`  - go to window on left
- `<Ctrl-w><Right>` - go to window on right
- `<C-w> <C-r>`     - To swap the two parts of a split window

### Window size commands
- `Ctrl+W +/-` - increase/decrease height (ex. 20<C-w>+)    
- `Ctrl+W >/<` - increase/decrease width (ex. 30<C-w><)    
- `Ctrl+W _` - set height (ex. 50<C-w>_)    
- `Ctrl+W |` - set width (ex. 50<C-w>|)    
- `Ctrl+W =` - equalize width and height of all windows
**Resizing will happen only by one characters.**
## Buffer 
- `:new` will create a split window with an unnamed buffer. 
- `:badd filename` will add the file to the bufferlist.
- `:enew` will open one in the current window. 
- `:vnew` will open one in a vertically split window. 
- `:tabnew` will open one in a new tab.
- `:bn` will change to next buffer.
- `:bp` will change to previous buffer.
- `:br` will change to starting buffer list.
- `:bf` will change to first buffer.
- `:ls` will list all the buffers.
- `:bd` will Delete the buffer , also we can specify the buffer id too.

## Random hacks
- `:r !date /t` will add the current date.

## Links
1. http://www.rayninfo.co.uk/vimtips.html
