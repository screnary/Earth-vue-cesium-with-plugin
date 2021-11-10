# vue-cesium-new

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


###  安装本地插件
```shell
#在项目根目录运行下面的命令：
npm install --save-dev file:/full/path/to/your/plugin
vue invoke <your-plugin-name>

#每次插件修改后，你需要重复这个步骤。

#卸载插件：
npm uninstall vue-cli-plugin-axios
```

其实不用这样做，直接在node_modules 中的源码中修改就好
```shell
vue add vue-cli-plugin-cesium
```
安装cesium 1.68
