# VPS 剩余价值计算器（Cloudflare Workers）

一个可直接部署到 **Cloudflare Workers** 的单文件应用：
- 计算 VPS 套餐剩余价值（按剩余天数）
- 支持月付 / 年付
- 支持实时汇率换算（USD/CNY/HKD/EUR/JPY/SGD）
- 现代化深色玻璃拟态 UI

---

## 设计思路（参考市面产品）
调研了 AWS / GCP / Azure / DigitalOcean 等计算器后，提炼了这些通用设计点：

1. **输入区 + 结果区分栏**（降低认知负担）
2. **核心数字大字展示**（剩余天数、剩余价值）
3. **汇率与细节信息分层显示**（主结果优先）
4. **实时性提示**（当前汇率）

本项目采用更偏「面板化 + 玻璃拟态」风格，适配桌面与移动端。

---

## 功能说明

### 1) 剩余价值计算

核心公式：

```text
剩余比例 = 剩余天数 / 套餐总天数
剩余价值(原币种) = 总价 × 剩余比例
```

其中：
- 套餐总天数优先使用「开始日期 ~ 到期日期」真实天数
- 当日期缺失时，可按月付30天/年付365天兜底

### 2) 实时汇率换算

通过 Worker 后端接口 `/api/rate` 拉取汇率（上游：`open.er-api.com`），前端只请求本域接口。

- `GET /api/rate?from=USD&to=CNY`
- 返回 `{ ok, from, to, rate }`

并在 Worker 端做了内存缓存（10分钟）以减少重复请求。

---

## 本地运行

```bash
npm install
npm run dev
```

打开 Wrangler 提示的本地地址即可。

---

## 部署到 Cloudflare Workers

### 1) 登录 Cloudflare

```bash
npx wrangler login
```

### 2) 部署

```bash
npm run deploy
```

部署成功后会得到一个 `*.workers.dev` 域名。

---

## 项目结构

```text
.
├── src/
│   └── index.js        # Worker + 页面 + API
├── package.json
├── wrangler.toml
└── README.md
```

---

## 可扩展建议

- 增加更多币种（按需扩展下拉列表）
- 增加「折价率」参数（用于二手 VPS 转让折损）
- 增加套餐模板（RackNerd/Vultr/DO/甲骨文等）
- 增加历史汇率走势图（可接入图表库）

---

## License

MIT
