# Keyboard Navigation

This is aimed at either newcomers in IT or people with only basics navigation
knowledge. If you are a `VIM` expert, you probably don't want to read this.

## Generic

Those works in multiple software but not everywhere.

**CTRL+Arrow**:
- left/right: navigate in lines very quickly, the same way just pressing
  left/right arrow would do,"jumping" over words instead of characters.
- top/down: doesn't bring much value here.

**Home/End**: Abuse this. It helps you quickly navigating to the very start or
the very end of a line. Combine with `CTRL+Arrow` above, it's allowing you to
very quickly place your cursor at 70% of a line: press `End`, press `CTRL+Left`
a few times and voila.

## Text editors / IDE

**SHIFT+Arrow**: Select the next character from your cursor position, left or
right depending of the arrow you pressed. If you pressed top/down arrow, it
will select what's between your current cursor position up to the same cursor
position (column) on the next/previous line.

**SHIFT+CTRL+Arrow**: Same as above, but instead of selecting one character at a
time, it selects one "word" at a time when using right/left arrows. I don't
recommend using top/down arrows here, it behaves differently from one editor
to another (in a text editor, it will behaves close to just using `SHIFT+Arrow`,
while it will do multiline selection in VSCode).

**SHIFT+Home/End**: Select the line from your cursor position up to the very end
or very start of the current line, depending if left or right arrow was pressed.

**SHIFT+PgUp/PgDn**: Select lines above/below, starting from your cursor
position.

**ALT+Arrow**: Move the current line up or down. Very useful to quickly
reorder lines without copy/pasting. top/down arrows are not useful here.

**CTRL+C > CTRL+V**: If you put your cursor at the very end of a LOC, without
selecting anything and press `CTRL+C` followed by `CTRL+V`, it will copy/paste
the current line. Very useful to duplicate lines quickly (it's the fastest way
to duplicate a line).

**CTRL+D**: When you have selected something (like a word), pressing `CTRL+D`
will select the next occurrence of that same word. You can press multiple times
`CTRL+D` to select more occurrences. Once you have selected all the occurrences
you want, you can just start typing to replace all the selected occurrences at
once. Very useful to rename variables for instance.

## Shell

**CTRL+K**: Deletes what is after the cursor on the current line. Typically
useful when you copied a line and wants to get rid of part of it.

## Nano

**CTRL+K**: Cut the cursor's line. Typically useful to get rid of lines quickly,
or to move lines. It can be done multiple time back to back to cut multiple
lines. Use `CTRL+U` to paste the line(s) you just cut.

**CTRL+U**: Paste the line(s) you cut with `CTRL+K`

> [!TIP]
> Nano has a built-in spell checker, just press `CTRL+T` to loop through errors,
> it conveniently suggest replacements. I don't use it, but know it exists.
