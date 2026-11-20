# 聊天头像替换工具 — 源码包

把聊天截图里「右侧的所有头像」替换成指定新头像，其他内容（文字、气泡、背景、左侧头像）不变。

## 文件说明

| 文件 | 作用 |
|---|---|
| `index.html` | **核心源文件**（单文件自包含，所有功能都在里面） |
| `main.js` | Electron 主进程（打包 exe 用，一般不用改） |
| `package.json` | 打包配置 |
| `icon.png` | 程序图标 |

## 怎么改功能

改功能只改 `index.html` 一个文件。双击它就能在浏览器里直接预览效果，改完刷新即可。

关键函数位置（在 `index.html` 内嵌的 `<script>` 里）：
- `detectRightAvatars(img)` — 右侧头像自动检测算法
- `drawAvatarOn(...)` — 头像替换绘制逻辑
- `draw()` — 画布渲染 + 实时预览

## 怎么打包成 exe（可选）

1. 安装依赖：`npm install`（需要 Node.js，Electron 二进制建议走国内镜像）

   ```bash
   npm install
   export ELECTRON_MIRROR="https://npmmirror.com/mirrors/electron/"
   node node_modules/electron/install.js
   ```

2. 打包单文件 exe：

   ```bash
   export ELECTRON_MIRROR="https://npmmirror.com/mirrors/electron/"
   export ELECTRON_BUILDER_BINARIES_MIRROR="https://npmmirror.com/mirrors/electron-builder-binaries/"
   npx electron-builder --win portable
   ```

   产物在 `dist/头像替换工具.exe`。

## 部署成网页链接（可选）

用 WorkBuddy 的「发布为应用」能力，把 `index.html` 部署成静态网页即可。

## 检测算法要点（当前版本）

右侧头像检测核心思路：
1. 逐行找「贴右边缘 + 宽度合理（12~80px）」的前景行
2. 聚成纵向段
3. 用「高度中位数聚类」剔除宣传图、表格等杂块
4. 过高的粘连段按中位数高度拆分

已知边界：含宣传图/表格/被裁切头像的复杂截图，自动检测仍可能多检出 1~2 个，用户可用界面的「+ 添加 / − 删除」按钮手动修正。
