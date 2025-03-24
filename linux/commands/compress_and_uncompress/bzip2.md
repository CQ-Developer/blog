# bzip2

compress files, bzip2 will replace file it-self by default.

## Syntax

```shell
bzip2 [options...] <files...>
```

## Options

| option    | function                    |
|-----------|-----------------------------|
| -1 ... -9 | fast ... best               |
| -c        | write to stdout             |
| -z        | compress files, default     |
| -d        | decompress files            |
| -k        | don't delete input file     |
| -f        | force overwrite output file |
| -t        | test compressed file        |
| -v        | extra info                  |

## Samples

```shell
# compress demo.txt to demo.txt.bz2
bzip2 demo.txt

# compress demo.txt to demo.txt.bz2
# don't delete demo.txt
bzip2 -k demo.txt

# compress demo.txt to compressed.txt.bz2
bzip2 -c demo.txt > compressed.txt.bz2
```

```shell
# decompress demo.txt.bz2 to demo.txt
bzip2 -d demo.txt.bz2

# decompress demo.txt.bz2 to demo.txt
# don't delete demo.txt.bz2
bzip2 -dk demo.txt.bz2

# decompress demo.txt.bz2 to decompressed.txt
bzip2 -dc demo.txt.bz2 > decompressed.txt
```

## Related

- `bunzip2` alias `bzip2 -d`
- `zcat` like `cat`
- `zless` like `less`
- `zmore` like `more`
- `zgrep` like `grep`
