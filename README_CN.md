# [![PongHub](static/band.png)](https://health.ch3nyang.top)

<div align="center">

🌏 [Live Demo](https://health.ch3nyang.top) | 📖 [English](README.md)

</div>

## 简介

PongHub 是一个开源的服务状态监控网站，旨在帮助用户监控和验证服务的可用性。它支持

- 非侵入式监控
- 利用 GitHub Actions 和 GitHub Pages 一键 CI/CD 部署
- 支持单个服务的多端口检查
- 支持状态码匹配和响应体内容正则表达式匹配
- 支持自定义请求体
- 支持自定义检查间隔、重试次数、超时时间等配置

![浏览器截图](static/browser_CN.png)

## 快速开始

1. Star 并 Fork [PongHub](https://github.com/WCY-dt/ponghub)

2. 修改根目录下的 [`config.yaml`](config.yaml) 文件，配置你的服务检查项

3. 修改根目录下的 [`CNAME`](CNAME) 文件，配置你的自定义域名

   > [!TIP]
   > 如果你不需要自定义域名，请删除 `CNAME` 文件

4. 提交修改并推送到你的仓库，GitHub Actions 将自动更新，无需干预

> [!TIP]
> 默认情况下，GitHub Actions 每 30 分钟运行一次。如果你需要更改运行频率，请修改 [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) 文件中的 `cron` 表达式。
> 
> 请不要将频率设置过高，以免触发 GitHub 的限制。

> [!IMPORTANT]
> 如果 GitHub Actions 未正常自动触发，手动触发一次即可。

## 配置说明

配置文件 `config.yaml` 的格式如下：

| 字段              | 类型   | 描述                          | 必填 |
|-----------------|--------|-----------------------------|----|
| `timeout`       | 整数   | 每次请求的超时时间，单位为秒              | 否  |
| `retry`         | 整数   | 请求失败时的重试次数                  | 否  |
| `max_log_days`  | 整数   | 日志保留天数，超过此天数的日志将被删除         | 否  |
| `services`      | 数组   | 服务列表                        | 是  |
| `services.name` | 字符串 | 服务名称                        | 是  |
| `services.health` | 数组 | 健康检查配置列表                    | 否  |
| `services.health.url` | 字符串 | 检查的 URL                     | 是  |
| `services.health.method` | 字符串 | HTTP 方法（`GET`/`POST`/`PUT`） | 否  |
| `services.health.status_code` | 整数 | 期望的 HTTP 状态码（默认 `200`）        | 否  |
| `services.health.response_regex` | 字符串 | 响应体内容的正则表达式匹配               | 否  |
| `services.health.body` | 字符串 | 请求体内容，仅在 `POST` 请求时使用            | 否  |
| `services.api` | 数组 | API 检查配置列表，格式同上 | 否  |

下面是一个示例配置文件：

```yaml
timeout: 5
retry: 2
max_log_days: 30
services:
  - name: "GitHub API"
    health:
      - url: "https://api.github.com"
    api:
      - url: "https://api.github.com/repos/wcy-dt/ponghub"
        method: "GET"
        status_code: 200
        response_regex: "full_name"
  - name: "Ch3nyang's  Websites"
    health:
      - url: "https://example.com/health"
        response_regex: "status"
      - url: "https://example.com/status"
        method: "POST"
        body: '{"key": "value"}'
```

> [!NOTE]
> `health` 和 `api` 至少有一个。这两者在处理上没有区别，是为未来扩展做的预留。

## 免责声明

[PongHub](https://github.com/WCY-dt/ponghub) 仅用于个人学习和研究，不对程序的使用行为或结果负责。请勿将其用于商业用途或非法活动。
