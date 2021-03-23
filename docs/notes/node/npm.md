## 配置~/.npmrc 修改

``` sh
registry=https://registry.npm.taobao.org/
@namespace:registry=http://xxxx:port/repository/xxxx
```

设置命令
``` sh
npm config set registry https://registry.npm.taobao.org/
npm config set @baas/ui:registry http://nexus.hyperchain.cn/repository/npm/
```