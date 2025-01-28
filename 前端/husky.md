> ==husky 是一个 git hooks 工具，用来确保提交的代码是符合规范==

#### 初始化本地仓库 和 husky

```shell
# 初始化本地仓库
cd <项目目录>
git init
pnpm dlx husky-init
pnpm install
```

#### 配置 pre-commit

```shell
. "$(dirname --"$0")/_/husky.sh"

# 只要一提交代码，就触发脚本 pnpm lint 来检查代码规范
# 如果出现问题，会提交失败
# 默认是全量检查
# 如果有历史问题，必须把问题解决了才能提交
pnpm lint
```

#### 暂存区校验

- 安装 lint-staged

```shell
pnpm i -D lint-staged
```

- package.json 添加配置项

```json
{
...,
"scripts": {
	...,
	"lint-staged": "lint-staged"
}
"lint-staged": {
	"*.{js,ts,vue}": [
		"eslint --fix"
	]
}
}
```

- 配置 pre-commit

```shell
. "$(dirname --"$0")/_/husky.sh"

# 只会检查暂存区新添加的代码
pnpm lint-staged
```
