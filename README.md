# Vue-Vue-Router-Vuex-SSR
Vue核心技术 Vue+Vue-Router+Vuex+SSR实战

vue+webpack工程流搭建

vue+vue-router+vuex项目架构

webpack4升级

1. 版本变化
2. 配置变化
3. 插件变化

## vue-loader配置

```
// vue-loader.config.js
const docsLoader = require.resolve('./doc-loader')
module.exports = (isDev) => {
	return {
		preserveWhitepace: true,
		extractCSS: !isDev,
		cssModules: {},
		// hotReload: false, // 根据环境变量生成
		loaders: {
			'docs': docsLoader,
		},
		preLoader: {
		},
	}
}
```

```
module.exports = (isDev) => {
	return {
		preserveWhitepace: true,
		extractCSS: !isDev,
		cssModules: {},
	}
}
```

## css module配置

```
module.exports = (isDev) => {
	return {
		preserveWhitepace: true,
		extractcss: !isDev,
		cssModules: {
			localIdentName: '[path]-[name]-[hash:base64:5]',
			camelCass: true
		}
	}
}
```

## 安装使用eslint和editorconfig以及precommit

```
npm i eslint eslint-config-standard eslint-plugin-standard eslint-plugin-promise eslint-plugin-import eslint-plugin-node
```

创建`.eslintrc`

```
{
	"extends": "standard"
}
```

```
npm i eslint-plugin-html
```

```
// package.json

"script": {
	"clean": "rimraf dist",
	"lint": "eslint --ext .js --ext .jsx --ext .vue client/",
	"build": "npm run clean && npm run build:client",
	//自动修复
	"lint-fix": "eslint --fix --ext .js --ext .jsx --ext .vue client/"
	...
}
```

```
npm i eslint-loader babel-eslint
```
