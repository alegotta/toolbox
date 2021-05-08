# Vi IMproved (VIM)

## Saving

|Key|Result|
|:--:|:--:|
|`:w [filename]`|Save (optionally copy to other file)|
|`:q`|Exit|
|`:wq`|Exit and save|
|`:q!`|Exit and confirm not to save|

## Operating Modes

|Key|Result|
|:--:|:--:|
|`ESC`|Normal mode (read only)|
|`i`|Insert mode (at current character)|
|`v`|Visual mode|

## Editing

Syntax: `[repetition]<operator>[movement]`

#### Operators

|Operator|Result|
|:--|:--:|
|`x`|Delete characters|
|`d`|Delete words|
|`:`|Jump to line|

#### Movements

|Movement|Result|
|:--:|:--:|
|`0`|Start of the line|
|`$`|End of the line|
|`d`|Entire line|
|`w`|Start of next word|
|`e`|End of current word|
|`gg`|Start of file|
|`G`|End of file|

### History

|Operator|Result|
|:--|:--:|
|`u`|Undo last change|
|`U`|Undo ALL changes on current line|
|`Ctrl-r`|Redo (undo last undo)|

### Copy/Paste

In visual mode:

|Operator|Result|
|:--:|:--:|
|`v`|Highlight word by word|
|`V`|Highlight line by line|
|`Ctrl+v`|Highlight by columns|
|`y`|Copy text|
|`p`|Paste text|



## Search and Replace

To **search**: `/<search_pattern>`. Then use `n` to search forwards, or `N` to search backwards.

To **replace**: `:[prefix]s/search/replace[/suffix]`.

|Symbol|Usage|Description|
|:--:|:--:|:--:|
|`%`|Prefix|Replace in whole file|
|`A,B`|Prefix|Replace from line A to line B (`$` for last line)|
|`A~B`|Prefix|Replace from line A to end of file, one line every *B*|
|`g`|Suffix|Replace all occurrences in the current line (not just the first one)|
|`c`|Suffix|Ask for confirmation|

--

#### Sources

- `vimtutor`