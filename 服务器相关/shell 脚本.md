#### 自动化部署脚本
```js
// 本地脚本
#!/bin/sh
scp -r dist/ root@yp.aly.cn:/opt/
ssh root@yp.aly.cn "/opt/build.sh"
```
```js
// 远程服务器脚本
#!/bin/sh
cd /opt/
if [ ! -d "bak" ]
then
    mkdir bak
fi
if [ -d "web-manage" ]
then
    time=$(date "+%Y%m%d%H%M%S")
    mv web-manage bak/web-manage${time}
fi
mv dist web-manage
```

