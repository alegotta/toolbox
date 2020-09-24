# Files Management

### Analyze space usage

`ncdu <path>`

Data may be saved to disk:

```bash
ncdu <path> -o <out_file>
ncdu -f <out_file>
``` 

### Find duplicates

`jdupes -r <path>`

### Search in file content

`grep <string> <path>`

String options:

- `-E`: specify regular expression
- `-i`: match case insensitively
- `-w`: match whole words only
- `-x`: match whole lines only
- `-r`: search recursively

Output options:

- `-n`: show line number
- `-H` / `-h`: show / hide file path
- `-o`: show only the part matching the string (by default, show whole string)
- `-L` / `-l`: print files that do NOT / do match
- `-c`: print number of matching lines for all files
- `-B <num>`: print *num* lines before the match
- `-A <num>`: print *num* lines after the match

### Locate file

```
sudo updatedb
locate <file>
```

--

#### Sources

Manpages and help menus of `ncdu`, ``jdupes`, `grep` and `locate`.