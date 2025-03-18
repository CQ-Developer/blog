# unzip

uncompress zip archive

## Syntax

```shell
unzip [-Z] [options...] archive[.zip] [file...] [-x file...] [-d dir]
```

## Options

| option      | function              |
|-------------|-----------------------|
| -Z          | show info             |
| -P password | decrypt with password |
| -d dir      | uncompress to dir     |

## Sample

```shell
# uncompress data.zip to current dir
unzip data
```

```shell
# uncompress data.zip to dir
unzip data -d dir
```

```shell
# uncompress multi zip file
# parts.z01
# parts.z02
# parts.z03
# parts.z04
# parts.zip
zip parts.zip -s 0 --out total.zip
unzip total.zip
```
