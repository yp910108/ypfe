# x.y.z
```
z：修复小的 bug
y：新增的功能，保证兼容以前的版本
x：大版本升级，不保证兼容
x.y.z：表示确切的版本号
1.2.*：表示自动升级 z 位，不会升级 y 位，等同于 ~1.2.0
2.x：表示自动升级 y、z 位，不会升级 x 位，等同于 ^2.0.0
```

# npx 作用
- 避免安装全局模块
- 调用项目内部安装的模块

# npx cmd & cmd
- npx cmd 寻找的路径是 node_modules => 全局 bin  => npm
- 在 package.json 的 scripts 中指定的 cmd，寻找的路径是 node_modules => 全局 bin
- 不在 package.json 的 scripts 中指定的 cmd，只在全局 bin 中查找

# npm
```sh
npm whoami
npm config get registry
npm config set registry http://registry.npm.taobao.org/
npm adduser --registry https://registry.npmjs.org/
npm publish --registry https://registry.npmjs.org/
npm --publish --tag fix --registry http://localhost:4873
```

# npm link
> 参考：https://zhuanlan.zhihu.com/p/372989254

> 参考：https://zhuanlan.zhihu.com/p/469010320

```sh
npm link
npm link dragonslayer

npm unlink
npm unlink dragonslayer

# 在 react hooks 中解决不同 react 的方法，在 dragonslayer 文件夹中执行
npm link ../ts-with-react/node_modules/react
```

# yalc
```
# 在 y-ui 中执行
yalc publish
yalc publish --no-scripts
yalc publish --no-scripts --changed
yalc publish --no-scripts --replace

# 在 test-vue 中执行
yalc add @yp910108/y-ui
yalc add @yp910108/y-ui --link
yalc add @yp910108/y-ui --pure
yalc add @yp910108/y-ui --dev

yalc update

yalc remove @yp910108/y-ui
yalc remove --all

# 任意目录执行
yalc installations show
yalc installations clean @yp910108/y-ui
```
