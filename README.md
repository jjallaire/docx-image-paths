# docx-image-paths

This repository demonstrates an issue with docx output whereby images must exist prior to calling pandoc (or they are substituted with their description). This is likely by design but does interfere w/ some more dynamic rendering pipelines (described below).

Consider the following files:

```
elephant.png
subdir/doc.md
```

Where the content of `subdir/doc.md` is:

```
![](../elephant.png)
```

Rendering to docx as follows gives a warning:

```bash
$ pandoc subdir/doc.md --to docx --output doc.docx --lua-filter filter.lua
[WARNING] Could not fetch resource ../elephant.png: replacing image with description
[ Para [ Span ( "" , [ "image" ] , [] ) [] ] ]
```

The `filter.lua` prints the AST. Note that the image itself has been fully removed from the AST and replaced by a `Span` with class `image`.

Note that this does not occur for other compound file formats like pdf and epub.

In the case of our application, we are appending together a bunch of .md files (which could be in various sub-directories) and have a Lua filter that fixes up all of the paths so that they are relative to the working directory where pandoc is called from. However, in the case of docx our filter never sees the path so fails to include the image.
