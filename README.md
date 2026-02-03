# Theo Simple Garden（交接说明）

交接日期：2026-02-03  
交接人：OpenClaw agent（main）  
接手人：另一个 openclawd

## 1. 项目目标（最终达成版本）

为 Theo 做一个“打开就心情好”的小页面，包含：

- 简易账号/密码校验（不需要真正安全登录，接受弱保护）
- 甜甜问候 + 小游戏（收集心心、长按充能）
- 积分/奖励文案与动画反馈
- 手机端可用（避免双击放大、点不中等问题）

## 2. 上线地址与仓库

- GitHub Pages：<https://xcuicui.github.io/theo-simple-garden/>
- Repo：<https://github.com/xcuicui/theo-simple-garden>

> 说明：此前尝试过 Vercel/Next.js（登录弹窗/内置浏览器/网络兼容问题较多），最终选择 GitHub Pages 静态托管以最大化可用性。

## 3. 本地路径

- Workspace：`/Users/evie/.openclaw/workspace`
- 项目目录：`/Users/evie/.openclaw/workspace/theo-simple`
- 核心文件：`index.html`

## 4. 登录方式（前端“密码门”）

- 打开页面先出现登录卡片
- 登录成功后 `localStorage.setItem('theo_gate_ok','1')`，同一设备后续免登录
- 账号/密码不以明文出现：使用 `crypto.subtle.digest('SHA-256')` 计算输入哈希并与代码内哈希比对

> 注意：这是“混淆/门槛”，不是安全措施。懂的人仍可离线暴力猜解。用户明确接受该强度。

## 5. 功能概览

### 5.1 随机问候

- 进入后展示甜甜随机问候
- 落款：— Evie

### 5.2 今日签到

- 按钮：今日签到 +7
- 分数影响“奖励贴纸”解锁（全局分数体系）

### 5.3 小游戏 1：收集小心心

- 点击开始后生成一局心心
- **频率限制**：每小时最多玩 **5 次**（滚动 60 分钟窗口）
- **得分**：
  - 每击中一个：+1（同时增加“今日小心心积分”）
  - 本局全部击中：额外 +5，并弹出甜甜提示
- **展示**：小游戏上方统计栏
  - 今日小心心积分
  - 本小时剩余 X 次
- **动画反馈**：
  - +1：积分数字“心动砰砰”小动画
  - +5：更大“砰砰”动画
- 视觉：心心采用 **SVG mask**（避免 emoji 渲染导致颜色不生效），颜色为深蓝到浅蓝随机

### 5.4 小游戏 2：长按充能

- 按住按钮充能，松手加分（+1～+10）
- 更适合手机触控，减少点不中/双击放大等问题

## 6. 关键数据存储（localStorage keys）

- 登录态：`theo_gate_ok`
- 全局分数（贴纸用）：`theo_score`
- 贴纸解锁：`theo_unlocked`
- 签到日期：`theo_daily_bonus_day`
- 小心心小游戏“每小时 5 次”窗口：`theo_heartgame_plays`（timestamp 数组，保留 60min 内）
- 小心心小游戏“今日积分”：`${YYYY-MM-DD}_heartgame_points`

## 7. 可调参数位置（接手后常改）

都在 `index.html` 内：

- 每局心心数量：`const count = 10;`
- 出现间隔：`setTimeout(spawnHeart, i*220)`
- 上浮速度：`const dur = 1900 + Math.random() * 1300;`（越小越快）
- 每小时可玩次数：`remainingPlays()` 内写死 5
- 颜色：`const blueColors = [...]`
- 完美通关奖励：命中后 `+5` 与甜甜 toast 文案

## 8. 已知注意事项

1) **“不明文密码”≠安全**：哈希可被拷贝并暴力猜；这是用户接受的“弱保护”。
2) **GitHub Pages 生效延迟**：push 后通常 30～120 秒更新；建议用户刷新。
3) 若仍有手机端命中率问题：可将玩法改成“单个大目标点击+换位置”，命中率会更高。
