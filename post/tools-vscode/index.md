# VSCODE


## 1. 安装

```shell
sudo snap install code --classic
```

## 2. 配置

1. 打开设置（快捷键`Ctrl + ,`）,里面有两个设置。
2. 【一个】是`USER SETTINGS`（用户设置）也就是全局配置，其他项目也会应用这个配置。
3. 【另一个】是`WORKSPACE SETTINGS`（工作区设置）也就是项目配置，会在当前项目的根路径里创建一个`.vscode/settings.json`文件，然后配置只在当前项目生效。
4. 配置同步，左下角登陆，同步。

## 3. 快捷键

### 3.1. 单个键

1. `End`行尾。
2. `Home`行首；

### 3.2. `Ctrl`加1

1. `Ctrl+/`注释整行或取消注释整行；
2. `Ctrl+D` 选择当前单词的下一个匹配项，并出现多个光标；
3. `Ctrl+End`文件尾；
4. `Ctrl+F2`选中所有的匹配项，并增加光标；
5. `Ctrl+G`跳转到指定行；
6. `Ctrl+Home`文件首；
7. `Ctrl+K+C`注释；
8. `Ctrl+K+U`取消注释；
9. `Ctrl+L`选中当前行。

### 3.3. `Shift`加1

1. `Shift+End`光标到行尾之间选中；
2. `Shift+Home`光标到行首之间选中；

### 3.4. `Alt`加1

1. `Alt+上下箭头`将当前整行上移或下移；

### 3.5. ‵Ctrl`+`Alt`加1

1. `Ctrl+Alt+-`跳回上一个光标位置。

### 3.6. `Ctrl`+`Shift`加1

1. `Ctrl+Shift+上下箭头`在相邻行增加光标；

    ```markdown
    配合`左右箭头`实现框选；
    配合`Shift+End`实现框选；
    配合`End`，光标都移动到行尾；
    配合`Ctrl+C`可粘贴，还可粘贴框复制的内容（行数相同）。
    ```

2. `Ctrl+Shift+K`删除整行；
3. `Ctrl+Shift+L`选中所有的匹配项，并增加光标；

### 3.7. `ALt`+`Shift`加1

1. `Alt+Shift+上下箭头`将当前整行向上复制或向下复制（编辑python,C的时候只是增加光标）
2. `Alt+Shift+I`在选中区域的每一行的行尾添加光标；

## 4. 命令

1. `F1`呼出命令行输入框，直接输入命令。

```yml
render whitespace # 显示空格
sort lines Ascending  # 正向排序
sort lines Descending   # 逆向排序
```

## 5. 通用插件

### 5.1. increment-selection

1. 编辑或是插入递增数字;
2. 首先进入多行编辑，`Ctrl+Alt+I`即可完成递增数字的插入，默认从0开始，如果想改变起始数字，在多行编辑前将第一项改为想要的数字，多行选中后再使用快捷键。

## 6. 应用场景

### 6.1. C/C++

1. 安装插件`C/C++`;
2. 格式化：
  
  ```yml
  Clang_format_fallback Style #预定义样式。在没有找到.clang-format文件情况下使用这里定义的样式。可以设置的值为：Visual Studio、LLVM、【Google】、Chromium、Mozilla、WebKit、none，或者{key: value，…}这样的键值对.
  ```

### 6.2. 访问数据库

1. 安装插件`vscode-database`;
2. 在命令框中输入 SQL 可以看到支持的操作。
3. `Ctrl+Q`直接输入 sql 语句`enter`执行。

### 6.3. 编写前端项目

1. [Vue Vue-cli Eslint 在 vscode 里代码自动格式化](https://www.jianshu.com/p/69bfcb95b8d7)

#### 6.3.1. 相关插件

`禁用beautify`
**注:由于需要同时使用 prettier 和 eslint，而 prettier 的一些规则和 eslint 的一些规则可能存在冲突，例如 prettier 字符串默认是用双引号而 esLint 定义的是单引号的话这样格式化之后就不符合 ESLint 规则了。**
**所以要解决冲突就需要在 Prettier 的规则配置里也配置上和 ESLint 一样的规则，直接覆盖**

##### 6.3.1.1. [Eslint](https://eslint.org/) **\***

###### 6.3.1.1.1. 配置文件

`在node工程目录下（即和package.json平级的目录），不是在文件夹跟目录下`

JavaScript - use .eslintrc.js 文件导出一个包含配置信息的对象。
YAML - 使用 .eslintrc.yaml 或 .eslintrc.yml 定义配置。
JSON - 使用 .eslintrc.json 定义配置信息，JSON 文件中支持 JavaScript 注释。
package.json - 在 package.json 文件中增加一个 eslintConfig 字段，在该字段中定义配置信息。
.eslintrc - 已废弃

如果在同一个目录中有多个配置文件，则它们中间只有一个是有效的，优先级如下：

.eslintrc.js
.eslintrc.yaml
.eslintrc.yml
.eslintrc.json
.eslintrc
package.json

`.eslintrc.js`

```javascript
module.exports = {
  root: true,
  env: {
    node: true
  },
  extends: ['plugin:vue/essential', 'eslint:recommended'],
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    //强制使用单引号
    quotes: ['error', 'single'],    // double
    //强制不使用分号结尾
    semi: ['error', 'never']    //always总是使用分号
    //默认最后有逗号
    "comma-dangle": ["error", "never"]
  },
  parserOptions: {
    parser: 'babel-eslint'
  }
}
```

###### 6.3.1.1.2. 使用命令行

##### 6.3.1.2. [Vetur](https://vuejs.github.io/vetur/) **\***

###### 6.3.1.2.1. 格式化

1. 只支持格式化整个文档。
2. 格式器

    ```yml
    prettier: For css/scss/less/js/ts.
    prettier-eslint: For js. Run prettier and eslint --fix.
    prettyhtml: For html.
    stylus-supremacy: For stylus.
    vscode-typescript: For js/ts. The same js/ts formatter for VS Code.
    ```

3. 设置
   `.prettierrc`为配置文件，配置文件优先于 setting.json.

    ```json
    "vetur.format.enable"    # 格式化开关
    "vetur.format.options.tabSize": 2,
    "vetur.format.options.useTabs": false
    ```

4. `setting.json`

```json
"vetur.format.defaultFormatterOptions": {
  "prettier": {
    "semi": false,
    "singleQuote": true
  }
}
"javascript.format.insertSpaceBeforeFunctionParenthesis": true,
"vetur.format.defaultFormatter.js": "vscode-typescript"
"eslint.validate": [
  "javascript",
  "javascriptreact",
  "html",
    {
      "language": "vue",
      "autoFix": true
    }
]
```

## 7. 扩展

### 7.1. [koroFileHeader]() **\***

1. 用于在文件头部插入注释`Ctrl + Alt + I`。
2. 头部注释会自动创建。
3. 函数注释可使用命令`cursorTip`。
4. 在`preferences`->`settings`->搜索`file Header`->编辑`settings.js`.

### 7.2. [Tabnine](https://tabnine.com/) **\***

闭源的强大的自动补全插件（运用机器学习算法）。

### 7.3. [aiXcode](https://www.aixcoder.com/#/) **\***

免费版的只能500条/日。

### 7.4. [cdr/code-server](https://github.com/cdr/code-server)

### 7.5. [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

1. 可以首先使用 pyenv 创建一个虚拟环境，在 vscode 左下角选择这个虚拟环境。

### 7.6. [Python Docstring Generator]()

1. 函数和方法的注释文档 docstring.
2. `ctrl+shift+2`.

