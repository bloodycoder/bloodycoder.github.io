# AGENTS.md

这个仓库是个人 Hugo 日记站。目标是：作者只写日记 Markdown，构建和发布交给 GitHub Actions。

## 作者日常只需要做什么

1. 在 `content/posts/` 新建或修改 `.md` 文件。
2. 写完后把 Markdown 推到 GitHub：
   - 可以用 GitHub 网页编辑器直接提交；或
   - 本地 `git add content/posts/xxx.md && git commit -m "Add diary" && git push`。
3. 不要手动运行 `hugo`。
4. 不要手动修改、提交 `docs/` 里的生成文件。

> 注意：完全自动发布的前提是 Markdown 已经进入 GitHub 仓库。也就是说仍然需要一次“保存到 GitHub”的动作；之后编译、发布都由 workflow 完成。

## 日记文件约定

推荐放在：

```text
content/posts/YYYY_MM_DD.md
```

推荐 Front Matter 使用 TOML：

```toml
+++
title = "今天的标题"
date = "2026-06-17"
description = "一句话摘要，可选"
tags = ["diary"]
+++

{{< toc >}}

正文从这里开始。
```

写作规则：

- 正文使用普通 Markdown。
- 图片放到 `static/images/` 或 `static/img/`，文章里用绝对路径引用，例如：`![](/images/example.png)`。
- 如果不想显示目录，可以删除 `{{< toc >}}`。
- 公开日记不要写密码、token、身份证号、私钥等敏感信息。

## 自动化发布

仓库使用 `.github/workflows/hugo-pages.yml`：

- 当 `content/`、`static/`、`layouts/`、`themes/`、`config.toml` 等发生变化并 push 到 `master` 或 `main` 时，自动构建 Hugo。
- Pull Request 只做构建检查，不发布。
- 成功构建后发布到 GitHub Pages。

GitHub Pages 需要在仓库设置中使用 GitHub Actions 作为发布来源：

```text
Settings -> Pages -> Build and deployment -> Source -> GitHub Actions
```

通常只需要设置一次。

## 给以后维护者 / AI Agent 的规则

- 优先保持作者体验简单：作者只关心 `content/posts/*.md`。
- 不要要求作者手动编译 Hugo 或手动提交 `docs/` 产物。
- `docs/` 是 Hugo 生成目录；除非在修复历史部署模式，否则不要把它当作源文件编辑。
- 修改自动化时优先编辑 `.github/workflows/hugo-pages.yml`。
- 修改主题或 Hugo 版本后，必须确认 workflow 仍能构建。
- 不要删除 `themes/` 子模块配置，checkout 时需要 `submodules: recursive`。
- 保持 `config.toml` 的 `publishDir = "docs"`，因为 workflow 当前上传 `./docs` 作为 Pages artifact。

## 本地预览（可选）

本地预览不是日常必需。如果想预览，需要先安装 Hugo，然后运行：

```bash
hugo server -D
```

如果只是在写日记，可以跳过本地预览，直接提交 Markdown，让 GitHub Actions 构建。
