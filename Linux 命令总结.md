# diff和Patch
## 针对单个文件
```shell
diff -uN a.txt b.txt > b.patch
patch -p0 < b.patch
patch -RE -p0 < b.patch
```

## 针对多个文件
```shell
diff -uNr a b > b.patch
patch -p1 < b.patch
patch -RE -p1 < b.patch
```
