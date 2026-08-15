# MotionSites Prompts — 动效 UI 提示词资料库

A curated, offline-first catalog of **546 motion-driven UI prompts** — landing pages, hero scenes, agency showcases, dashboards, and more. Browse, search, copy, and export every prompt locally. No login, no third-party tracking, no paywall.

## 🚀 在线浏览（Live Site）

👉 **GitHub Pages：** https://chenjun198711.github.io/MotionSites_jasson/

每次 push 到 `main` 分支，GitHub Actions 会自动重新构建并部署到上面的地址。公网可直接访问全部 546 条提示词、完整正文与本地预览（图片 / 循环视频），打开即用。

> **Update 2026-08-15**：卡片预览覆盖率已从 ~69%（375 / 546）提升到 **99.6%**（544 / 546）—— 仅剩 2 条付费概念条目无效果图。

---

## 📊 当前数据

| 指标 | 数值 |
| --- | --- |
| 提示词总数 | **546**（MotionSites 407 + Community 139） |
| 有媒体预览 | **544**（366 静态动图 + 178 循环视频） |
| 有完整正文 | **544** |
| 无媒体（概念卡） | 2（`celestia-hero`、`motionz-premium-landing-page`，均为付费条目） |

### 本仓库较原版的主要更新

- **新增 42 条 YilaiAI 提示词**（含正文，视频效果留空）
- **补全 169 个卡片预览资源**：
  - 44 条匹配自 [ui.yilai-ai.com](https://ui.yilai-ai.com) 公开库（sites / sections / apps）
  - 123 条 Community 条目匹配自 [superdesign.dev](https://superdesign.dev) 公开提示词库（117 图 + 6 视频）
  - 2 条 MotionSites 首屏（`slam-dunk-hero`、`wisa-space-hero`）从前端静态资源中恢复
- **覆盖统计**：images 366 + videos 178 = 544（此前仅有 375）

## ✨ Features

- **Multi-dimensional filters**：分类、类型、来源（MotionSites / Community）、媒体格式，外加 Top-9 分类 chips，支持对标题 / 描述 / 来源仓库 / 文件路径的组合搜索
- **Progressive loading**：先拉 `catalog-lite.json` 渲染 chips 计数，再拉 `catalog-meta.json` 渲染卡片，卡片媒体由 `IntersectionObserver` 懒加载（进视口才真正加载）
- **Keyboard-first**：`/` 聚焦搜索、`Esc` 关闭弹窗、`G` 切换紧凑布局
- **Spotlight search**：搜索时高亮最匹配卡片
- **卡片媒体**：webp / png / gif / jpg 静态图 + mp4 / webm 循环视频，全部本地化，无外部网络调用
- **Modal 详情**：复制提示词、导出 .md、无正文条目展示元数据面板（来源 / 许可证 / 原文链接）

![Catalog preview](docs/catalog_preview.png)

## 🖥 Run it locally

The catalog ships as `index.html` + `ms_script.js` + a `data/` directory of progressive JSON files. Serve with any static server (no build step needed to use the catalog):

```bash
git clone https://github.com/chenjun198711/MotionSites_jasson.git
cd MotionSites_jasson
python -m http.server 8000
# 或
npx serve .
# 然后访问 http://localhost:8000
```

> 直接用 `file://` 打开 `index.html` 不行——浏览器会拦截 `fetch()`，需要用本地静态服务器。

## 🚀 Deploy

### GitHub Pages（当前使用，自动部署）

仓库内已包含 [`.github/workflows/pages.yml`](.github/workflows/pages.yml)：push 到 `main` 后自动执行

```yaml
checkout → configure-pages → upload-pages-artifact → deploy-pages
```

在仓库 **Settings → Pages** 选择 **Source: GitHub Actions** 即可启用，部署完成后访问：

```
https://<owner>.github.io/<repo>/
```

### Cloudflare Pages（备选）

仓库同时保留 `wrangler.toml`、`_headers`、`_redirects` 与 `deploy/` 下的 PowerShell 脚本：

```powershell
# Direct Upload 方式（跳过 >25 MiB 的文件）
.\deploy\deploy-wrangler.ps1
```

## 📁 File layout

```
.
|-- index.html              # ~33 KB shell，24 个内联骨架卡片
|-- ms_script.js            # 渐进渲染 / 过滤 / 弹窗逻辑
|-- ms_template.html        # HTML 骨架模板
|-- wrangler.toml           # Cloudflare Pages 配置（备选）
|-- _headers                # 缓存 + 安全响应头（Cloudflare）
|-- _redirects              # 静态路由（未使用）
|-- .github/workflows/pages.yml   # GitHub Pages 自动部署
|-- data/
|   |-- ms_prompts_merged.json    # 源数据：标题 / 分类 / 媒体引用（546 条）
|   |-- ms_prompts_with_text.json # 源数据：完整正文（544 条）
|   |-- catalog-lite.json         # 1.7 KB - 计数 + Top-9 分类
|   |-- catalog-meta.json         # 462 KB - 除正文外的全部字段
|   |-- catalog-text-index.json   # id -> catalog-text/<id>.txt 映射
|   |-- catalog-text.json         # 4.9 MB - id -> 正文（预热缓存）
|   `-- catalog-text/             # 544 个 .txt，按 id 懒加载
|-- assets/previews/              # webp / png / gif / mp4 / webm 本地媒体
|-- scripts/
|   |-- build.js                  # 源 JSON -> 渐进 JSON + index.html
|   `-- lib/catalog-utils.js      # 排序等共享工具
|-- docs/                         # README 截图
|-- CHANGELOG.md
|-- CONTRIBUTING.md
`-- LICENSE                       # MIT
```

## 🔨 Rebuild `index.html` from source

```bash
node scripts/build.js
# -> Records=546 complete=544 images=366 videos=178 concepts=2 motionsites=407 community=139
# -> Wrote catalog-lite.json  1.7 KB  (gzip 0.8 KB)
# -> Wrote catalog-meta.json  461.6 KB  (gzip 89.8 KB)
# -> Wrote catalog-text.json  4986.3 KB  (gzip 1623.1 KB)
# -> Wrote catalog-text/  544 files
# -> Wrote index.html bytes 33247
```

To update the catalog:

1. 编辑 `data/ms_prompts_merged.json`（新增记录）与 `data/ms_prompts_with_text.json`（对应正文）
2. 将预览资源放入 `assets/previews/<id>.{webp|mp4|gif|...}`，在 merged 中设置 `local_rel`
3. 运行 `node scripts/build.js`，提交 `data/catalog-*.json`、`data/catalog-text/` 与 `index.html`

构建脚本幂等（按 `id` 去重）；任何 `local_rel` 超过 24 MiB 的记录会自动降级为概念卡，避免线上被宿主替换成错误页。

## 📚 Data provenance

| 字段 | 来源 |
| --- | --- |
| 标题 / 描述 / 分类 / 类型 | `motionsites.ai` 公开目录（Supabase 匿名读取） |
| MotionSites 正文 | `motionsites.ai` 公开 API + `nomaan5541/motionsites-prompt-collection`（MIT）等社区仓库 |
| YilaiAI 提示词（42 条） | YilaiAI 模板库 `ui.yilai-ai.com` |
| Community（139 条） | [`superdesigndev/superdesign-prompts`](https://github.com/superdesigndev/superdesign-prompts)（CC0-1.0） |
| 预览媒体 | 全部本地化（`assets/previews/`）：`ui.yilai-ai.com`、`superdesign.dev`、`motionsites.ai` CDN 下载后打包 |

> 未绕过 `motionsites.ai` 的付费墙：仅 2 条付费条目（`celestia-hero`、`motionz-premium-landing-page`）无法获取媒体，渲染为概念卡。

## 🧪 Tests

```bash
node --test scripts/tests/*.test.js
```

覆盖排序、分类归一化、导入边界、媒体懒加载与目录审计等逻辑。注意：`import-liro` / `import-giglianepefrei` 两组用例依赖本地 `sources/` 数据目录，若已清理该目录会跳过填充。

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the file format, build flow, and the path to your first PR.

## License

[MIT](LICENSE) — free to use, modify, and redistribute. Each individual prompt body retains its original author's copyright; this repository only archives and indexes them.
