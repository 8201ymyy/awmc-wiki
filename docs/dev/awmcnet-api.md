# AWMCNET Bot API

AWMCNET 是 QueryBot 的成绩镜像与查询服务。本文面向需要把成绩同步到 AWMCNET 的
Bot、查分器和服务端开发者。

::: tip 服务地址
基准地址：`https://net.wmc.pub`

接口使用 `Bot-Token` 鉴权。该令牌只应放在服务端，不能下发到用户端或写入公开日志。
:::

## `POST /api/bot/sync`

同步玩家资料和成绩。首次同步会按 QQ 创建不公开的临时玩家，之后可通过对应的
`QQ号@qq.com` 认领到论坛账号。

### 请求头

```http
Bot-Token: <shared-secret>
Content-Type: application/json
```

### 请求字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `qq` | integer | 必填，QQ 号，范围 `10000`～`999999999999` |
| `nickname` | string | 可选，玩家昵称 |
| `source` | string | 可选，数据来源，如 `sega`、`divingfish` |
| `rating` | integer | 可选，当前 Rating |
| `old_rating` / `new_rating` | integer | 可选，旧版本 / 新版本 Rating |
| `play_count` | integer | 可选，游玩次数 |
| `records` | array | 成绩数组，单次最多 10000 条 |
| `full_snapshot` | boolean | 全量快照时设为 `true`；增量同步保持默认 `false` |
| `snapshot_id` | string | 分块快照的标识，最多 64 个字符 |
| `snapshot_final` | boolean | 分块快照的最后一块设为 `true` |

## 分块提交全量快照

一次性成绩过多可能导致请求超时。此时把同一个全量快照拆成多个请求：

1. 每一块都设置 `full_snapshot: true`，并复用同一个 `snapshot_id`。
2. 中间块省略 `snapshot_final` 或设置为 `false`，服务端返回 `status: "partial"`。
3. 最后一块设置 `snapshot_final: true`，服务端返回 `status: "ok"`，并清理本次快照中不存在的旧成绩。
4. 请求超时后，用相同的 `snapshot_id` 重试同一块；重复提交是幂等的。
5. 未完成的快照不能混用另一个 `snapshot_id`，否则返回 HTTP `409`。

分块到达后，成绩会立即写入，但旧成绩只在最后一块到达时删除。因此上传未完成期间，
查询结果可能暂时包含上一份快照中的成绩。

### 中间块示例

```bash
curl -X POST 'https://net.wmc.pub/api/bot/sync' \
  -H 'Bot-Token: <shared-secret>' \
  -H 'Content-Type: application/json' \
  -d '{
    "qq": 123456789,
    "source": "sega",
    "full_snapshot": true,
    "snapshot_id": "qq-123456789-20260825-01",
    "snapshot_final": false,
    "records": [
      {
        "song_id": 1,
        "title": "Example Song",
        "type": "DX",
        "level_index": 3,
        "achievements": 100.1234,
        "dxScore": 2500,
        "fc": "fc",
        "fs": "fs"
      }
    ]
  }'
```

### 最后一块示例

请求体结构与中间块相同，只需继续使用相同的 `snapshot_id`，并设置：

```json
{
  "full_snapshot": true,
  "snapshot_id": "qq-123456789-20260825-01",
  "snapshot_final": true,
  "records": [
    {"song_id": 2, "level_index": 3, "achievements": 99.8765}
  ]
}
```

最后一块不能提交空的有效快照；至少需要在本次快照的任意一块中提交一条有效成绩。

### 响应

```json
{
  "status": "partial",
  "snapshot_id": "qq-123456789-20260825-01",
  "imported": 1,
  "updated": 0,
  "skipped": 0,
  "stored_records": 1,
  "errors": []
}
```

`imported`、`updated`、`skipped` 和 `errors` 反映当前请求这一块；`stored_records` 是
玩家当前已存储的成绩数量。最后一块以及同一 `snapshot_id` 的重复请求返回
`status: "ok"`。

## `GET /api/bot/player/{qq}`

读取 AWMCNET 已合并的玩家资料、成绩和 B50/B15。需要同样的 `Bot-Token` 请求头。

## `GET /api/bot/player/{qq}/trend`

读取玩家 Rating 趋势。可用 `days` 查询 1～365 天，默认 30 天。

::: warning 数据来源
AWMCNET 接口只接收统一成绩数据，不接收 SGWCMAID、街机 UID 或第三方查分器 Token。
:::
