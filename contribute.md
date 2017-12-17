---
layout: page
title: 贡献
site_nav_category: contribute
site_nav_category_order: 400
is_site_nav_category: true
---

**本页仅从官方直接翻译过来，页面均指向原文。如果要为文档新增、修改内容（不是指翻译结果）请提交到官方的 GitHub Repo，本 Repo 仅从官方拉取内容上的改动。对于翻译问题，您仍可以在本 Repo 上提出 Issues 或 Pull Request 进行修正，更多详情请跳到 GitHub：[fython/kotlin-guides-cn](https://github.com/fython/kotlin-guides-cn)**

我们欢迎且十分支持为这个网站作出贡献！

要为这个网站作出贡献，小修改请自由地创建 Pull Request。对于更大的贡献我们推荐先于 [issue tracker](https://github.com/android/kotlin-guides/issues) 中提出 Issue 进行讨论。

Pull requests 应当设定目标为 [GitHub repo](https://github.com/android/kotlin-guides) 的 `master` 分支。每几周 [更新改动](changelog.html) 将会被升级且所有改动都会定期地推送到 `gh-pages` 分支。

**我们期待你的所有贡献！**


## 开发

在改动过程你可以在你的计算机本地运行网站。

### 安装 Ruby 和 Bundler

确保你已经安装了 Ruby 和 [Bundler](http://bundler.io/)。

    gem install bundler

### 一次性设置

    bundle install --path vendor/bundle

_注意: 如果你在 Mac OS 平台上，这会在安装 nokogiri 时失败，先运行 `brew unlink xz`，安装它然后再 `brew link xz`。_

### 运行网站

    bundle exec jekyll serve

在你的浏览器中打开 [http://127.0.0.1:4000/kotlin-guides/](http://127.0.0.1:4000/kotlin-guides/)。
