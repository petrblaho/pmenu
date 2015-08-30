# pmenu
Dynamic menu like dmenu for terminal written in Python without dependencies with optional sorting by usage and application launcher pmenu-run.

## Requirements

Python 3.3.

## Basic Usage

    usage: pipe menu items to stdin or pass them with as positional arguments

    positional arguments:
      item                  menu item text

    optional arguments:
      -h, --help            show this help message and exit
      -n NAME, --name NAME  name of the usage cache
      -p PROMPT, --prompt PROMPT
                            prompt text

## Keyboard Shortcuts

- `Ctrl-C`, `Ctrl-G`, `Ctrl-[`, `Escape`: quit without selecting a match
- `Ctrl-H`, `Backspace`: delete the character before the cursor
- `Ctrl-J`, `Ctrl-M`, `Enter`: quit and output the selected item
- `Ctrl-N`: select the next match
- `Ctrl-P`: select the previous match
- `Ctrl-U`: delete the entire line
- `Ctrl-W`: delete the word before the cursor

## Usage Examples

Display some arbitrary menu items:

    echo -e "foo\nbar\nbaz" | pmenu
    pmenu foo bar baz
    echo -e "foo\nbar" | pmenu baz qux

Pick some file from current directory:

    find -maxdepth 3 -type f ! -path "./.git/*" ! -path "./.svn/*" -printf '%P\n' | LC_COLLATE=C sort | pmenu

Pick some file from current directory for editing from VIM by Ctrl-P (ala CtrlP):

    function! Pmenu()
      let mru_name = fnamemodify(getcwd(), ":t")
      if isdirectory("./.git")
        let filelist_command = "git ls-files"
        if !empty(expand('%'))
          let filelist_command .= " | grep -vF " . shellescape(expand("%:."), 1)
        endif
      else
        let filelist_command = "find -maxdepth 3 -type f " . shellescape("./" . expand("%:."), 1) . " ! -path '*/.git/*' ! -path '*/.svn/*' -printf '%P\n' | LC_COLLATE=C sort"
      endif
      let selected_paths = split(system(filelist_command . " | pmenu -n " . shellescape(mru_name, 1)), '\n') . " -p " . shellescape(mru_name . "> ", 1)
      if !empty(selected_paths)
        execute ":edit " . selected_paths[0]
      endif
      redraw!
    endfunction
    nnoremap <silent> <Leader>o :call Pmenu()<CR>
    vnoremap <silent> <Leader>o :call Pmenu()<CR>

## pmenu-run

The script `pmenu-run` is an example of application launcher built with `pmenu` similar to `dmenu_run`, `gmrun` and `bashrun`. It scans for the aplication commands in \*.desktop files, detects if they are intended for terminal and automatically run them attached or detached to the it.

Bind some desktop shortcut to one the following commands depending of what terminal emulator you use:

    xterm -T run -e pmenu-run
    urxvt -title run -e bash -i -c "pmenu-run; :"

`pmenu-run` passes all provided options to `pmenu`. This could be used to add more items to application launcher, like `pmenu-run command1 command2 command3`.

## Alternatives

- [dmenu](http://tools.suckless.org/dmenu/), C, X11
- [dmenu2](https://bitbucket.org/melek/dmenu2), C, X11
- [rofi](https://github.com/DaveDavenport/rofi) C, X11
- [slmenu](https://bitbucket.org/rafaelgg/slmenu), C, terminal
- [percol](https://github.com/mooz/percol), Python, terminal
- [fzf](https://github.com/junegunn/fzf), Go, terminal
- [Pick](https://github.com/thoughtbot/pick), C, terminal
- [Selecta](https://github.com/garybernhardt/selecta), Ruby, terminal
- [PathPicker](https://facebook.github.io/PathPicker/), Python, terminal
- [happyfinder](https://github.com/hugows/hf), Go, terminal
- [Heatseeker](https://github.com/rschmitt/heatseeker), Rust, terminal
- [CtrlP](http://kien.github.io/ctrlp.vim/), Vimscript, Vim
- [Unite](https://github.com/Shougo/unite.vim), Vimscript, Vim
- [FuzzyFinder](http://www.vim.org/scripts/script.php?script_id=1984), Vimscript, Vim

### dmenu Wrappers

- [Yeganesh](http://dmwit.com/yeganesh/), Haskel
- [yegonesh](https://github.com/klowner/yegonesh), Go
- [xboomx](https://github.com/victorhaggqvist/xboomx), Python

### Application Launchers

- [dmenu\_run](http://tools.suckless.org/dmenu/), Shell, X11
- [gmrun](http://sourceforge.net/projects/gmrun/), C, X11
- [bashrun](http://bashrun.sourceforge.net/), Bash, terminal
- [bashrun2](http://henning-bekel.de/bashrun2/), Bash, terminal
- [xboomx](https://github.com/victorhaggqvist/xboomx), Python, X11
