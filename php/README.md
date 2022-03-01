## 打包公共镜像

```
docker login
docker build -t yourusername/php:7.4.7-fpm-alpine -f Dockerfile
docker push yourusername/php:7.4.7-fpm-alpine
```

## 打包 私有景象
```
docker login yourdomain.com
docker build -t yourdomain.com/yourusername/php:7.4.7-fpm-alpine -f Dockerfile
docker push yourdomain.com/yourusername/php:7.4.7-fpm-alpine
```

ref: [dnmp](https://github.com/yeszao/dnmp)