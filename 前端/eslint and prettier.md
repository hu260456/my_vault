#### eslint 预定义标准

##### standard

- 字符串使用单引号
- 无分号
- 关键字后加空格
- 函数名后加空格
- 禁用 `==`
- 不允许一行以上的换行
- ...

#### vscode 配置项

- setting.json

```JSON
// 保存的时候 ESLint 自动修复错误
// 不是所有的错误都能自动修复
"editor.codeActionsOnSave": {
	"source.fixAll": true
}

// 关闭保存时自动格式化
"editor.formatOnSava": false
```

#### eslint 配置

- .eslintrc.cjs

```javascript
/* eslint-env node */
require('@rushstack/eslint-patch/modern-module-resolution')
module.exports = {
  root: true,
  extends: [
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/eslint-config-prettier/skip-formatting'
  ],
  parserOptions: {
    ecmaVersion: 'latest'
  },
  rules: {
 
    // 此 prettier 和 prittier插件 冲突，需禁用插件
    // 和保存时自动格式化功能冲突，需设置 vscode
    'prettier/prettier': [
      'warn',
      {
        // 使用单引号
        singleQuote: true,
        // 不使用分号
        semi: false,
        // 每行宽度至多 80 字符
        printWidth: 80,
        // 无尾后逗号
        taillingComma: 'none',
        // 不限制换行符号
        endOfLine: 'auto'
      }
    ],
 
    // 设置 vue 组件名称由多单词组成
    'vue/multi-word-component-names': [
      'warn',
      {
        // 设置 index 名称可以例外
        ignores: ['index']
      }
    ],

    // 关闭 props 解构时的校验
    // 'vue/no-setup-props-destructure': ['off'],

    // 使用未定义变量的提示
    'no-undef': 'error'
  },
  // 配置全局变量
  // 可以决解有时候 vscode 误报的变量未定义
  globals: {
	  ElMessage: 'readonly';
  }
}
```
