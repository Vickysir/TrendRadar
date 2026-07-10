# Fork 分支工作流说明

本仓库采用三分支策略：上游同步、个人开发、网页部署完全分离。

## 分支职责

| 分支 | 用途 | 是否手动改 |
|------|------|-----------|
| `master` | 仅跟踪上游 [sansan0/TrendRadar](https://github.com/sansan0/TrendRadar) | 只用于同步上游 |
| `main` | Fork 开发主分支：配置、关键词、Actions 爬虫 | 日常改这里 |
| `gh-pages` | **仅** GitHub Pages 静态站点（`index.html` + `.nojekyll`） | **不要手动改**，由 Actions 自动更新 |

## 架构示意

```
upstream/master ──merge──► master ──merge──► main ──爬虫──► gh-pages ──► GitHub Pages
                              ↑                  │
                              └── 仅同步上游 ─────┘
```

## 日常开发

```bash
git checkout main
git checkout -b cursor/my-feature-2d62
# ... 改 config、加功能 ...
git checkout main && git merge cursor/my-feature-2d62
git push origin main
```

## 同步上游更新

```bash
git fetch upstream
git checkout master && git merge upstream/master && git push origin master

git checkout main && git merge master
# 解决冲突（注意保留 crawler.yml 末尾的 gh-pages 发布步骤）
git push origin main
```

## GitHub 仓库设置（一次性）

1. **Settings → General → Default branch** → `main`
2. **Settings → Pages** → Source: **Deploy from a branch**
   - Branch: **`gh-pages`**
   - Folder: **`/ (root)`**
3. **Actions** → 启用 Fork 工作流 → 在 `main` 分支运行 **Get Hot News**

## 访问地址

https://vickysir.github.io/TrendRadar/

爬虫在 `main` 分支运行后，会自动把报告推到 `gh-pages`，Pages 从该分支发布。

## 配置文件

- `config/config.yaml` — 主配置
- `config/frequency_words.txt` — 关键词（末尾有自定义词组）
- `config/timeline.yaml` — 调度时间线

## 注意事项

- **不要**在 `gh-pages` 手动开发，`main` 也不要长期保留 `index.html` 提交
- `main` 分支已跳过上游 Actions 的 7 天试用期检查
- 同步上游时若 `crawler.yml` 冲突，保留 `peaceiris/actions-gh-pages` 发布步骤
