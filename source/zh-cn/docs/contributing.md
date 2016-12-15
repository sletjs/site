title: 贡献
---
## 开发

我们非常欢迎您加入 Slet 的开发，这份文件将帮助您了解开发流程。

### 开始之前

请遵守以下准则：

- 遵守 [Google JavaScript 代码风格](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)。
- 使用 2 个空格缩排。
- 不要把逗号放在最前面。

### 工作流程

1. Fork [sletjs/slet]
2. 把库（repository）复制到电脑上，并安装所依赖的插件。

    {% code %}
    $ git clone https://github.com/<username>/slet.git
    $ cd slet
    $ npm install
    $ git submodule update --init
    {% endcode %}

3. 新增一个功能分支。

    {% code %}
    $ git checkout -b new_feature
    {% endcode %}

4. 开始开发。
5. 推送（push）分支。

    {% code %}
    $ git push origin new_feature
    {% endcode %}

6. 建立一个新的合并申请（pull request）并描述变动。

### 注意事项

- 不要修改 `package.json` 的版本号。
- 只有在测试通过的情况下您的合并申请才会被批准，在提交前别忘了进行测试。

    {% code %}
    $ npm test
    {% endcode %}

## 更新文档

Slet 文档开放源代码，您可以在 [Sletjs/site] 找到源代码。

### 工作流程

1. Fork [Sletjs/site]
2. 把库（repository）复制到电脑上，并安装所依赖的插件。

    {% code %}
    $ git clone https://github.com/<username>/site.git
    $ cd site
    $ npm install
    {% endcode %}

3. 开始编辑文件，您可以通过服务器预览变动。

    {% code %}
    $ Slet server
    {% endcode %}

4. 推送（push）分支。
5. 建立一个新的合并申请（pull request）并描述变动。

### 翻译

1. 在 `source` 资料夹中建立一个新的语言资料夹（全小写）。
2. 把 `source` 资料夹中相关的文件（Markdown 和模板文件）复制到新的语言资料夹中。
3. 在 `source/_data/language.yml` 中新增语言。
4. 将 `en.yml` 复制到 `themes/navy/languages`中并命名为语言名称（全小写）。

## 反馈问题

当您在使用 Slet 时遇到问题，您可以尝试在 [问题解答](troubleshooting.html) 中寻找解答，或是在 [GitHub](https://github.com/sletjs/slet/issues) 或 [Google Group](https://groups.google.com/group/slet) 上提问。如果你没有找答案，请在 Github 报告它。

1. 在 [调试模式](commands.html#调试模式) 中重现问题。
2. 运行 `slet version` 并检查版本信息。
3. 把调试信息和版本信息都贴到 GitHub。

[Sletjs/Slet]: https://github.com/sletjs/slet
[Sletjs/site]: https://github.com/sletjs/site
