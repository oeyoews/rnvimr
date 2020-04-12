# rnvimr

Rnvimr is a NeoVim plugin that allows you to use Ranger in a floating window.

Different than other Ranger vim-plugins, rnvimr gives you full control over Ranger. It uses [RPC](https://neovim.io/doc/user/api.html#RPC) to communicate with Ranger.

**Since rnvimr requires RPC, this plugin does not support vim for now.**

<p align="center">
  <img width="1080px" src="https://user-images.githubusercontent.com/17562139/74416173-b0aa8600-4e7f-11ea-83b5-31c07c384af1.gif">
</p>

> [vimade](https://github.com/TaDaa/vimade) was used to fade interactive windows.

## Requirements

1. [ranger](https://github.com/ranger/ranger)
2. [pynvim](https://github.com/neovim/pynvim)
3. [neovim](https://github.com/neovim/neovim)
4. python3
5. [ueberzug](https://github.com/seebye/ueberzug) (optional, Ranger is required at least [b58954](https://github.com/Ranger/Ranger/commit/b58954d4258bc204c38f635e5209e6c1e2bce743), if you don't use tmux, ueberzug is required at least [d86eeb](https://github.com/seebye/ueberzug/commit/d86eeb303b9468226884425853472287a794d9dd))

## Features

- Replaces the built-in Netrw as a default file explorer
- Calibrated preview images for ueberzug
- Attach file automatically when toggling Ranger
- Runs [RPC](https://neovim.io/doc/user/api.html#RPC) inside Ranger to communicate with NeoVim
- Automatically adjusts floating window after resizing NeoVim
- Fully customizable layouts for floating window
- Better experience for Pager view in ranger

## Installation

Install rnvimr with your favorite plugin manager! Example for [Vim-plug](https://github.com/junegunn/vim-plug):

```vim
Plug 'kevinhwang91/rnvimr', {'do': 'make sync'}
```

> If you want rnvimr to use the default (vanilla) Ranger configuration, please using `make install` instead of `make sync`.

## Usage

Use `:RnvimrToggle` to create a Ranger process, and if one exists, `:RnvimrToggle` simply shows or hides the floating window.

Use `:RnvimrResize` to cycle the preset layouts.

`:RnvimrSync` will synchronize your personal Ranger configuration and plugins with rnvimr this time.

**Note:** if your Ranger config changes, you will have to run `:RnvimrSync` in order to use your updated Ranger configuration with rnvimr

`Enter` to open a file in Ranger. Rnvimr also supports `CTRL-T`/`CTRL-X`/`CTRL-V` key bindings that allow you to open up file in a new tab, a new horizontal split, or in a new vertical split.

Pressing `q` in Ranger simply hides the floating window. Ranger will attach the file of the current buffer in the next toggle event.

Synchronize the scroll line of pager view with neovim:
<p align="center">
  <img width="960px" src="https://user-images.githubusercontent.com/17562139/79063322-8a646380-7cd3-11ea-9184-189b134b5eef.gif">
</p>

### Example configuration

#### Minimal configuration

```vim
" Synchronize all Ranger's configuration and plugins with rnvimr
Plug 'kevinhwang91/rnvimr', {'do': 'make sync'}

tnoremap <silent> <M-i> <C-\><C-n>:RnvimrResize<CR>
nnoremap <silent> <M-o> :RnvimrToggle<CR>
tnoremap <silent> <M-o> <C-\><C-n>:RnvimrToggle<CR>
```

#### Advanced configuration

```vim
" Synchronize all Ranger's configuration and plugins with rnvimr
Plug 'kevinhwang91/rnvimr', {'do': 'make sync'}

" Make Ranger replace netrw and be the file explorer
let g:rnvimr_ex_enable = 1

" Set up only two columns in miller mode and enable vcs
let g:rnvimr_ranger_cmd = 'ranger --cmd="set vcs_aware false" ' .
            \'--cmd="set column_ratios 1,1" --cmd="set vcs_aware true"'

nnoremap <silent> <M-o> :RnvimrToggle<CR>
tnoremap <silent> <M-o> <C-\><C-n>:RnvimrToggle<CR>

" Resize floating window by all preset layouts
tnoremap <silent> <M-i> <C-\><C-n>:RnvimrResize<CR>

" Resize floating window by special preset layouts
tnoremap <silent> <M-l> <C-\><C-n>:RnvimrResize 1,8,9,11,5<CR>

" Resize floating window by single preset layout
tnoremap <silent> <M-y> <C-\><C-n>:RnvimrResize 6<CR>

" Customize the initial layout
let g:rnvimr_layout = { 'relative': 'editor',
            \ 'width': float2nr(round(0.6 * &columns)),
            \ 'height': float2nr(round(0.6 * &lines)),
            \ 'col': float2nr(round(0.2 * &columns)),
            \ 'row': float2nr(round(0.2 * &lines)),
            \ 'style': 'minimal' }

" Customize multiple preset layouts
" '{}' represents the initial layout
let g:rnvimr_presets = [
            \ {},
            \ {'width': 0.700, 'height': 0.700},
            \ {'width': 0.800, 'height': 0.800},
            \ {'width': 0.950, 'height': 0.950}
            \ {'width': 0.500, 'height': 0.500, 'col': 0, 'row': 0},
            \ {'width': 0.500, 'height': 0.500, 'col': 0, 'row': 0.5},
            \ {'width': 0.500, 'height': 0.500, 'col': 0.5, 'row': 0},
            \ {'width': 0.500, 'height': 0.500, 'col': 0.5, 'row': 0.5},
            \ {'width': 0.500, 'height': 1.000, 'col': 0, 'row': 0},
            \ {'width': 0.500, 'height': 1.000, 'col': 0.5, 'row': 0},
            \ {'width': 1.000, 'height': 0.500, 'col': 0, 'row': 0},
            \ {'width': 1.000, 'height': 0.500, 'col': 0, 'row': 0.5}]
```

For more information, please refer to `:help rnvimr`,
because I don't want to maintain two documents with the same contents :).

## FAQ

- Couldn't open some special type of files by using `Enter` or `l` in Ranger.

1. The behavior of openning the file in Ranger depends on `rifle.conf`. Press `r` to make sure that the `${VISUAL:-$EDITOR} -- "$@"` is the best candidate in Ranger.
2. If the case 1 is false, change the code in `rifle.conf` like that:

```diff
-!mime ^text, label editor, ext xml|json|csv|tex|py|pl|rb|js|sh|php = ${VISUAL:-$EDITOR} -- "$@"
+!mime ^text, label editor, ext xml|json|csv|tex|py|pl|rb|js|sh|php|your_file_type = ${VISUAL:-$EDITOR} -- "$@"
```

3. execute`:RnvimrSync` to synchronize the `rifle.conf` just modified with rnvimr.

## License

The project is licensed under a BSD-3-clause license. See [LICENSE](./LICENSE) file for details.
