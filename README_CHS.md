以下是关于如何使用 caddy-gitea 插件为 Gitea 启用类似 GitHub Pages 功能的翻译和详细说明：
caddy-gitea
这是一个为 Gitea 设计的 Caddy v2 插件，允许你在 Gitea 中使用类似 GitHub Pages 的功能，并且提供更多功能。使用此插件还需要为你的 Gitea 主机设置一个通配符 CNAME。
目前，带有 .md 扩展名的 Markdown 文件也会自动生成为 HTML。
目录
caddy-gitea
开始使用
Caddy 配置
DNS 配置
Gitea 配置
gitea-pages 仓库
任何仓库（可配置允许的分支/标签/提交）
任何仓库（暴露所有分支/标签/提交）
构建 Caddy
开始使用
Caddy 配置
以下是一个 Caddyfile 配置示例，它创建了一个监听在 :3000 端口的 Web 服务器，该服务器将通过 agiteatoken 令牌与 https://yourgitea.yourdomain.com 上的 Gitea 交互。agiteatoken 应该是 Gitea 中具有仓库读取权限的令牌。
caddyfile复制
{
        order gitea before file_server
}
:3000
gitea {
        server https://yourgitea.yourdomain.com
        token agiteatoken
        domain pages.yourdomain.com # 这是可选的
}
DNS 配置
此配置需要使用通配符域名。因此，你需要将 *.pages.yourdomain.com 设置为 CNAME，指向运行 Caddy 的服务器（这不一定需要是与 Gitea 相同的服务器）。
根据下面的 Gitea 配置，你可以通过以下方式访问你的页面：
http://org.pages.yourdomain.com:3000/repo/file.html（org 是组织或用户名）
http://org.pages.yourdomain.com:3000/repo/file.html?ref=abranch（org 是组织或用户名）
http://repo.org.pages.yourdomain.com:3000/file.html
http://branch.repo.org.pages.yourdomain.com:3000/file.html
http://org.pages.yourdomain.com:3000/（如果你创建了一个 gitea-pages 仓库，它将在根目录下提供服务）
Gitea 配置
有多种选项可以将你的仓库作为页面公开，你可以同时使用这些选项。
创建一个带有 gitea-pages 分支和 gitea-pages 主题的 gitea-pages 仓库。
在任何你选择的仓库中添加一个 gitea-pages 分支和 gitea-pages 主题。
为你的仓库添加一个 gitea-pages-allowall 主题（最简单，但安全性较低）。
gitea-pages 仓库
例如，我们将使用 yourorg 组织。
在 yourorg 组织中创建一个 gitea-pages 仓库。
为这个 gitea-pages 仓库添加一个 gitea-pages 主题（用于选择加入你的仓库）。
在这个 gitea-pages 仓库中创建一个 gitea-pages 分支。
将你的内容放入这个分支中（例如 file.html）。
现在，你的内容将可通过以下地址访问：http://yourorg.pages.yourdomain.com:3000/file.html
任何仓库（可配置允许的分支/标签/提交）
例如，我们将使用 yourorg 组织中的 yourrepo 仓库，其中 master 分支中有 file.html，dev 分支中有 otherfile.html。master 分支是默认分支。
为 yourrepo 仓库添加一个 gitea-pages 主题（用于选择加入你的仓库）。
在这个 yourrepo 仓库中创建一个 gitea-pages 分支。
在 yourrepo 仓库的 gitea-pages 分支中放置一个 gitea-pages.toml 文件（关于文件内容的更多信息见下文）。
gitea-pages.toml 文件将包含你允许公开的 Git 引用（分支/标签/提交）。要允许所有内容，请使用以下示例：
toml复制
allowedrefs=["*"]
要仅允许 main 和 dev：
toml复制
allowedrefs=["main","dev"]
master 分支中的 file.html 现在可通过以下地址访问：http://yourorg.pages.yourdomain.com:3000/yourrepo/file.html
master 分支中的 file.html 现在可通过以下地址访问：http://yourrepo.yourorg.pages.yourdomain.com:3000/file.html
dev 分支中的 otherfile.html 现在可通过以下地址访问：http://yourorg.pages.yourdomain.com:3000/yourrepo/file.html?ref=dev
dev 分支中的 otherfile.html 现在可通过以下地址访问：http://dev.yourrepo.yourorg.pages.yourdomain.com:3000/file.html
任何仓库（暴露所有分支/标签/提交）
例如，我们将使用 yourorg 组织中的 yourrepo 仓库，其中 master 分支中有 file.html，dev 分支中有 otherfile.html。master 分支是默认分支。
为 yourrepo 仓库添加一个 gitea-pages-allowall 主题（用于选择加入你的仓库）。
master 分支中的 file.html 现在可通过以下地址访问：http://yourorg.pages.yourdomain.com:3000/yourrepo/file.html
master 分支中的 file.html 现在可通过以下地址访问：http://yourrepo.yourorg.pages.yourdomain.com:3000/file.html
dev 分支中的 otherfile.html 现在可通过以下地址访问：http://yourorg.pages.yourdomain.com:3000/yourrepo/file.html?ref=dev
dev 分支中的 otherfile.html 现在可通过以下地址访问：http://dev.yourrepo.yourorg.pages.yourdomain.com:3000/file.html
构建 Caddy
由于这是一个第三方插件，你需要自行构建 Caddy（或者使用预编译的二进制文件）。要构建带有此插件的 Caddy，你需要安装 Go 1.19。
bash复制
go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest # 这将把 xcaddy 安装到 ~/go/bin
~/go/bin/xcaddy build --with github.com/42wim/caddy-gitea@v0.0.4
通过以上步骤，你可以为 Gitea 启用类似 GitHub Pages 的功能，通过 Caddy 插件实现静态页面的托管和访问。
