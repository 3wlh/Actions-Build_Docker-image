# Docker
## 导入镜像命令
#### 从文件加载镜像
```
docker load -i myimage.tar 
```
#### 标准输入加载镜像
```
cat myimage.tar | docker load
```

## 导出镜像命令
#### 保存单个镜像到文件
```
docker save -o myimage.tar myimage:latest
```
#### 保存多个镜像到文件
```
docker save -o myimage.tar myimage:latest anotherimage:latest
```
#### tar 压缩镜像
```
docker save <镜像名:标签> | tar -czf myimage.tar.gz -
```
#### gzip 压缩镜像
```
docker save <镜像名:标签> | gzip > myimage.tar.gz
```