VimuxIDE
============
This a vim plugin that enables interaction of a script with a interpreter,
such as *Ipython*, using *tmux*. This plugin can be easily extended to other
scripting languages, however, it currently only supports python.

Requirements
------------
tmux > 2.6

Installation
------------
using vim-plug
    Plug 'astrapios/vimuxide'

Keys mapping
-----------
By default, the following mappings are enabled :

* `<C-c>` sends the currently selected lines to tmux
* `<C-g>` sends the current block to tmux
* `<C-b>` sends the current block to tmux and moves cursor to the next block
* `<F5>` saves script if its updated, and runs the entire script

You can disable default mappings :

    let g:vimuxide_default_mappings='0'

In addition, there is a function to execute all cells above the current line
which isn't bound by default, but you can easily bind it with :

    noremap <silent> <C-a> :call RunTmuxPythonAllCellsAbove()<CR>

Options
-------
You can configure the target tmux session/window/pane using:

    let g:vimuxide_tmux_sessionname='0' 
    let g:vimuxide_tmux_windowname='0'
    let g:vimuxide_tmux_panenumber='0'

If these global variables are not defined, the plugin will search for all
open tmux pane names to search for pane with the interpreter.

This scripts relies on temporary files to send text from vim to tmux. To 
allow cell execution queuing, we use a rolling buffer of temporary files. You 
can control the size of the buffer by defining:

    g:vimuxide_n_files
    
Otherwise, it is set to 10 by default.

Specific to python, one can define:

    g:vimuxide_program_title
    g:vimuxide_run_command

`g:vimuxide_program_title` is the tmux pane name to search for the ipython 
session, and `g:vimuxide_run_command` is the ipython command used to run the 
block script (which can be either %load or %run). The default values are 
`g:vimuxide_program_title='python'` and `g:vimuxide_run_command='%run -ni'`.

Usage
-----
Blocks are defined using `g:vimuxide_block_separator`. Otherwise, the plugin
asks for block separator string during runtime. Now just for example, say you
have `g:vimuxide_block_separator='##'`, with the following python script : 

    ##
    import numpy as np
    print 'Hello'                  # (1)
    np.zeros(3)
    ##
    if True:
        print 'Yay !'                # (2)
        print 'Foo'                  # (3)
    ##

If you put your cursor anywhere above the second block separator, the block 
marked with (1), and hit `<C-G>`, the 3 lines in the first cell will be sent 
to tmux. If you hit `<C-B>`, the same will happen but the cursor will move to 
the line after the ## (so that you can run blocks at a time). The first and 
last block_separator in the example is completely optional, so they can be 
removed without altering the plugin behavior.

You can also select lines and hit `<C-C>` to send selection to tmux. The
plugin automatically deindents selected lines so that the first line has no
indentation. So if you select the line marked (2) and (3), the print
statements will be de-indented and sent to tmux and ipython will correctly
run them.
