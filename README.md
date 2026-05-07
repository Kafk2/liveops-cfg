# LiveOps-使用说明

一个基于 Web 的游戏活动排期可视化管理工具，支持 GitHub 云同步，用于管理游戏内周期活动的时间配置。

## 功能

- **活动排期管理** — 配置活动的开启时间、持续时长、循环周期
- **时间轴可视化** — 甘特图式展示所有活动的时间分布，支持缩放和拖拽
- **配置编辑** — 表单化编辑活动参数，实时预览排期效果
- **版本管理** — 每次保存自动递增版本号，保留历史版本
- **GitHub 云同步** — 通过 GitHub API 读写配置，支持多人协作
- **更新检测** — 自动检测云端配置变更，提示同步
- **导入/导出** — 支持 CSV 格式的配置导入导出
- **撤销/重做** — Ctrl+Z / Ctrl+Y 操作历史

## 快速开始

### 方式一：本地打开

直接用浏览器打开 `index.html`，通过「导入数据」按钮加载 CSV 配置文件。

### 方式二：本地服务器（推荐）

使用 VS Code 的 Live Server 插件或任意 HTTP 服务器：

```bash
# 使用 Python
python -m http.server 8080

# 使用 Node.js
npx serve .
```

系统会自动从 `data/latest.json` 读取最新版本配置。

### 方式三：GitHub Pages

1. 在仓库 Settings → Pages 中启用 GitHub Pages（main 分支）
2. 访问 `https://<username>.github.io/liveops-cfg/`

## 云同步配置

1. 点击页面上的「云同步设置」按钮
2. 填入 GitHub Personal Access Token（需要 `repo` 权限）
3. 填入仓库 Owner 和 Repo 名称
4. 点击保存

配置完成后：
- 点击「保存到云端」将当前配置推送到 GitHub
- 系统每 30 秒自动检测云端更新

## 数据结构

```
data/
├── latest.json              # 版本指针（指向最新配置文件）
├── settings.json            # 活动元数据、依赖关系、UI 设置
├── schedule.csv             # 根目录备份
├── 6.72/                    # 版本目录
│   ├── schedule_6.72.6_202604281618.csv
│   └── schedule_6.72.7_202604301231.csv
└── 6.70/                    # 历史版本
    └── schedule_6.70.5_202604272039.csv
```

### latest.json

```json
{
  "version": "6.72.7",
  "path": "6.72",
  "scheduleFile": "schedule_6.72.7_202604301231.csv",
  "timestamp": "202604301231",
  "updateTime": "2026/4/30 12:31:31"
}
```

## 容错机制

如果 `latest.json` 指向的文件被删除（如手动清理旧版本），系统会：

1. 检测到指针文件不存在
2. 通过 GitHub API 扫描版本目录，找到实际存在的最新文件
3. 自动修复 `latest.json` 指针
4. 如果以上都失败，回退到根目录 `schedule.csv`

## 技术栈

- 纯 HTML + CSS + JavaScript（无框架依赖）
- GitHub REST API v3（云同步）
- 浏览器 localStorage（本地缓存配置）
