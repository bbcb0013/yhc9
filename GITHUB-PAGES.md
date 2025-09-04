# GitHub Pages 部署提示（Vite + React）

1) 如果仓库名不是 `username.github.io`，请修改 Vite 的 base：
   - 临时命令：`vite build --base=./ --outDir dist/static`
   - 或在 `vite.config.ts` 加入：`export default defineConfig({ base: './', plugins: [...] })`

2) 部署方式 A（本地构建再提交）：
   - 本地执行 `pnpm i && pnpm build`
   - 将 `dist/static` 中的文件推送到 `gh-pages` 分支或仓库根，并在 Settings -> Pages 指向该分支

3) 部署方式 B（GitHub Actions 自动构建）：
   - 参考下面 workflow，替换 `REPO_NAME` 为你的仓库名，将产物 `dist/static` 发布到 `gh-pages` 分支。

```yaml
name: Deploy Vite to GH Pages
on:
  push:
    branches: [ main ]
permissions:
  contents: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
        with:
          version: 9
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm build
      - name: Copy SPA 404 fallback
        run: cp dist/static/index.html dist/static/404.html
      - name: Deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: dist/static
          publish_branch: gh-pages
```
