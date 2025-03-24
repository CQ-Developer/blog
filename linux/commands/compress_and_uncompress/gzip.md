# gzip

compress files,
gzip will replace file it-self by default.

## Syntax

```shell
gzip [options...] <files...>
```

## Options

| option    | function         |
|-----------|------------------|
| -1 ... -9 | fastest ... best |
| -c        | write to stdout  |
| -d        | uncompress files |
| -l        | list info        |
| -v        | extra info       |

## Samples

```shell
# compress demo.txt to demo.txt.gz
gzip demo.txt

# compress demo.txt to compressed.txt.gz
gzip -c demo.txt > compressed.txt.gz
```

```shell
# decompress demo.txt.gz to demo.txt
gzip -d demo.txt.gz

# decompress demo.txt.gz to decompressed.txt
gzip -dc demo.txt.gz > decompressed.txt
```

```shell
# show demo.txt.gz info
gzip -lv demo.txt.gz

# test demo.txt.gz if ok
gzip -tv demo.txt.gz
```

## Related

- `zcat` like `cat`
- `zless` like `less`
- `zmore` like `more`
- `zgrep` like `grep`
