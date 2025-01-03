- <https://blog.csdn.net/flysnownet/article/details/117515213>
- https://blog.csdn.net/weixin_44048054/article/details/126057999
- https://zhuanlan.zhihu.com/p/558562290
- https://www.jb51.net/program/2937439ve.htm
- https://api.onlyoffice.com/docs/docs-api/usage-api/config

### 运行 OnlyOffice

```sh
# 运行 docker 容器
docker container run --name onlyoffice -e JWT_ENABLED=false -p 8701:80 -d onlyoffice/documentserver


# 重启服务
docker exec -it f36d9a6a7b63 /bin/bash

supervisorctl restart all

```

### 代码示例

```vue
<template>
  <div id="test"></div>
</template>

<script setup lang="ts">
const script = document.createElement("script");

script.src = "http://198.19.249.3:8701/web-apps/apps/api/documents/api.js";

script.onload = () => {
  new window.DocsAPI.DocEditor("test", {
    width: 1200,
    height: 800,
    documentType: "cell",
    document: {
      fileType: "xls",
      key: "456",
      url: "http://198.19.249.3:5173/1.xls",
      title: "1.xls",
    },
  });
};

document.head.appendChild(script);
</script>
```

