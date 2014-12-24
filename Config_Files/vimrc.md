Link - https://github.com/Shougo/neocomplete.vim

* File list
```
in .vim
drwxrwxr-x  2 alex alex  4096 Sep 21 21:20 after
drwxrwxr-x  5 alex alex  4096 Sep 21 21:09 autoload
drwxrwxr-x  3 alex alex  4096 Sep 21 21:03 bundle
drwxr-xr-x  2 alex alex  4096 Jun  7  2014 doc
drwxrwxr-x  7 alex alex  4096 Sep 21 20:49 neocomplete.vim
drwxr-xr-x  3 alex alex  4096 Sep 21 21:12 plugin

in .vim/plugin
drwxrwxr-x  2 alex alex   4096 Sep 21 21:08 neocomplete
-rw-rw-r--  1 alex alex   2724 Sep 21 21:08 neocomplete.vim
```
syntax on
"colorscheme evening
colorscheme koehler

setlocal tabstop=4
setlocal softtabstop=4
setlocal shiftwidth=4
setlocal textwidth=80
"setlocal smarttab
setlocal expandtab

filetype plugin indent on

map <F6> :!python %<CR>
map <F7> :!ruby %<CR>

filetype plugin on
let g:neocomplete#enable_at_startup = 1
let g:neocomplete#sources#syntax#min_keyword_length = 3
```
