+++
title = "Vir"
template = "page.html"
date = 2024-07-22
[extra]
summary = "A terminal-based modal text editor"
+++
This is a terminal modal text editor, similar to Vi(m).

[(GitHub Link)](https://github.com/DylanBulfin/vir)

[(YouTube Video Demo)](https://youtu.be/RQ8O1kJQ5WQ)

It has the following features:
* Three modes: Insert, Normal, and Visual
    * Normal allows actions like 'rc' to replace the character under the cursor with 'c',
    or 'x' to delete the current character, and many more
    * Visual allows you to select a range and act on it (delete, copy, change, etc.)
* Customizeable keybindings via a `config.toml`
