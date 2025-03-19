# zip

compress files or dir

## Syntax

```shell
zip [options...] [zipfile [path...]] [-i | -x pattern...]
```

## Options
| option       | function                        |
|---------------|---------------------------------|
| -q            | quiet                           |
| -m            | delete after zip                |
| -r            | recursive add sub dir and files |
| -0            | store files without compression |
| -1 ... -9     | fastest ... best                |
| -i pattern... | include                         |
| -x pattern... | exclude                         |
| -s size       | split archive of size           |
| -P password   | encrypt with password           |

## Samples

```shell
# compress all content in current dir to mydata.zip
zip mydata *
```

```shell
# compress dir to mydata.zip
zip mydata -r dir
```

```shell
# delete after compression
zip mydata -r dir -m
```

```shell
# compress .txt file in dir
zip mydata -r dir -i "*.txt"
```
