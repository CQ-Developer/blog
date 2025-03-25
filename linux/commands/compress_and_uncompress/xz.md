# xz

compress files, xz will replace file it-self by default.

## Syntax

```shell
xz [options...] [files...]
```

## Options

| option    | function                                                                  |
|-----------|---------------------------------------------------------------------------|
| -0 ... -9 | fastest ... best, default is 6                                            |
| -c        | write to stdout                                                           |
| -k        | don't delete input files                                                  |
| -z        | compress files                                                            |
| -d        | decompress files                                                          |
| -e        | use more cup time to improve compression ratio                            |
| -T {num}  | how many thread to use, default is 1, 0 means as many  as processor cores |
| -t        | test compressed file                                                      |
| -v        | extra info, support twice                                                 |

## Samples

```shell
# demo.txt -> demo.txt.xz
xz demo.txt

# demo.txt -> abc.txt.xz
xz -c demo.txt > abc.txt.xz
```

```shell
# demo.txt.zx -> demo.txt
xz -d demo.txt.xz

# demo.txt.xz -> abc.txt
xz -dc demo.txt.xz > abc.txt
```

```shell
# test if ok
xz -t demo.txt.xz

# test with double verbose
xz -tvv demo.txt.xz
```

## Related

- `unxz` alias `xz -d`
- `xzcat` like `cat`
- `xzless` like `less`
- `xzmore` like `more`
- `xzgrep` like `grep`
