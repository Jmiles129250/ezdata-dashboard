# 物联网数据监测平台（EzData2 Dashboard）

一个基于 [M5Stack EzData2](https://ezdata2.m5stack.com) 的实时数据可视化面板，单文件 HTML + 静态托管，支持部署到 GitHub Pages。

## 部署后访问

**访客**：直接打开 `https://<user>.github.io/ezdata-dashboard/` 查看管理员配置的字段实时数据。

**管理员**：在网址后追加 `?admin=1`，输入管理员口令后可增删字段、修改阈值。管理员会话仅本次有效，关闭页面自动失效。

## 字段配置

字段配置硬编码在 `index.html` 的 `DEFAULT_CONFIG.groups` 中（部署后对所有人生效）。管理员可在 `#admin` 后台修改本地浏览器的配置；如需对所有人生效，请修改 `index.html` 并推送到 GitHub。

字段结构：
```js
{
  id: 'f-xxx',
  name: '显示名',
  unit: '单位',
  dataToken: 'EzData2 share token',  // 在 my.m5stack.com → Data → 右键数据点 → Share
  icon: 'thermometer',               // lucide 图标
  color: '#FF9F0A',
  min: -10, max: 60, warn: [15, 35]
}
```

## 管理员口令

- 口令哈希硬编码在 `index.html` 的 `ADMIN_TOKEN_HASH`（SHA-256）
- 原口令不会出现在仓库或部署文件里
- 修改口令：换一个新口令 → 计算 SHA-256 → 替换 `ADMIN_TOKEN_HASH`

```bash
echo -n 'your-new-password' | sha256sum
```

## 本地开发

直接用浏览器打开 `index.html` 即可（部分浏览器对 file:// 的 `crypto.subtle` 有限制，建议用本地服务器）：

```bash
python -m http.server 8080
# 然后访问 http://localhost:8080/?admin=1
```

## 数据来源

所有数据通过 EzData2 v2 公开 HTTP API 拉取：

- `GET /api/v2/shareItemData/{dataToken}` — 单字段实时值
- `GET /api/v2/dataHistory?id={dataToken}&dataToken={dataToken}&sourceId=` — 历史趋势

接口 CORS 开放，浏览器直连，不需要后端。

## 设备

实测硬件：M5Stack StickS3 + SHT30 温湿度传感器，上报周期 5 分钟。