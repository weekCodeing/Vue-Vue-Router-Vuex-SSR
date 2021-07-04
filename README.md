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





