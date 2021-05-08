# PDF

Dependencies: `gs`, `graphicsmagick`

Global alias: `alias gpdf='gs -sDEVICE=pdfwrite -dNOPAUSE -dBATCH -dSAFER'`

## Merge multiple files

```bash
gpdf -o out.pdf in1.ps in2.pdf in3.pdf
``` 

## Explode

```bash
gpdf -o %d.pdf filein1.ps filein2.pdf filein3.pdf
``` 

It produces 1.pdf, 2.pdf, ...

To have **prefix zeros** (i.e. 01.pdf, 02.pdf, ...), adjust the string specifier to `%02d`. The syntax is the same as C's printf.

**From images:**

```bash
gm convert 1.jpg 2.jpg 3.jpg out.pdf
```

## Filtering

### Extract pages

#### Simple range

```bash
gpdf -dFirstPage=3 -dLastPage=3 -o out.pdf in.pdf
```

#### More complex ranges

```bash
gpdf -sPageList=1,3, -o out.pdf in.pdf
```

**Possibilities**:

- `even` / `odd`
- `1,3`: page one and three
- `1-3`: pages from one to three
- `3-`: pages from three till the end

### Remove images

```bash
gpdf -dFILTERIMAGE -o out.pdf in.pdf
```

### Remove annotations

Annotations have different types: /Stamp, /Squiggly, /Underline, /Link, /Text, /Highlight, /Ink, /FreeText, /StrikeOut and /stamp_dict.

To remove **all annotations**:

```bash
gpdf -dShowAnnots=false -o out.pdf in.pdf
```

To keep **only some types**:

```bash
gpdf -c "/ShowAnnotTypes [/Text /UnderLine]" -f -o out.pdf in.pdf
```

## Conversions

### Optimize size

The following will adapt all images to 150dpi and speedup the opening process (particularly of large files):

```bash
gpdf -dPDFSETTINGS=/ebook -dDetectDuplicateImages -dFastWebView -o out.pdf in.pdf
```

### Convert to PDF/A

```bash
gpdf -DPFA=3 -sColorConversionStrategy=DeviceRGB -dCompatibilityLevel=1.7 -o out.pdf in.pdf
```

If incompatible features are found, ghostscript will fall back to normal PDF v1.7. The `dPDFACompatibilityPolicy` flag controls this behaviour.

Note that PDF/A v.4 is still unsupported.

--

#### Sources

- https://www.ghostscript.com/doc/current/Use.htm
- https://www.ghostscript.com/doc/VectorDevices.htm#PDFWRITE
- https://flukylogs.blogspot.com/2012/08/gs-ghostscript-cheat-sheet.html
