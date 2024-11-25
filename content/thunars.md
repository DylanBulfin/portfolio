+++
title = "Thuna.rs"
template = "page.html"
date = 2024-08-11
[extra]
summary = "A terminal UI (TUI) file explorer in Rust"
+++
I wrote a TUI file browser in Rust:

[(GitHub Link)](https://github.com/DylanBulfin/thunars)

[(YouTube Demo Video)](https://www.youtube.com/watch?v=0Z4GG511xQg)

It supports several useful keyboard shortcuts/alternate modes, including:
* Hint mode (displays letters next to each visible entry, which you can type to jump to 
it)
* Search mode (performs a recursive search for a file/directory within the current
directory)
    * Also has [zoxide](https://github.com/ajeetdsouza/zoxide) search mode
* Create/rename/delete/cut/copy/paste files, or open them in VSCode

It also supports file previews, something even many GUI file browsers don't have.
