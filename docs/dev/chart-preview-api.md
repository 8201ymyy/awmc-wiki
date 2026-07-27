---
apiBaseUrl: https://v.wmc.pub
chartPreviewApiBaseUrl: https://v.wmc.pub
---

# 🎵 谱面评价与分析 API

谱面预览平台的公开 REST API，支持谱面查询、评论、评分、成绩提交与排行榜等功能。

::: tip 平台地址
- **Base URL**: `{{ $frontmatter.chartPreviewApiBaseUrl }}/api/v1/`
- **源码仓库**: [Pimeng/Maimai-Chart-Preview](https://github.com/Pimeng/Maimai-Chart-Preview)
:::

::: warning 🔐 鉴权方式
业务请求须在请求头携带：

`Authorization: Bearer <api-key>`

获取方式：登录 https://v.wmc.pub → 顶部导航栏钥匙图标 → `/api-keys` 页面 → 新建 API Key。
:::

::: danger ⚠️ 注意
API Key **仅在创建时显示一次**，丢失需重新创建。
:::

---

## 获取 API Key

1. 登录网站 (https://v.wmc.pub)
2. 点击顶部导航栏钥匙图标，进入 `/api-keys` 页面
3. 点击「新建」，输入名称，创建后复制保存

---

## 接口列表

### 1. 谱面接口

#### 1.1 获取谱面列表

<ApiDemo
  :options="[
    {
      title: '获取谱面列表',
      method: 'GET',
      path: '/api/v1/charts',
      description: '获取谱面列表，支持分页、按类型/难度筛选、关键词搜索与排序。',
      params: [
        { name: 'page', type: 'integer', required: '选填', desc: '页码，默认 1', value: '1' },
        { name: 'limit', type: 'integer', required: '选填', desc: '每页数量，默认 20，最大 50', value: '20' },
        { name: 'kind', type: 'string', required: '选填', desc: 'standard 或 dx', value: 'dx' },
        { name: 'difficulty', type: 'integer', required: '选填', desc: '难度 1-6', value: '5' },
        { name: 'q', type: 'string', required: '选填', desc: '搜索关键词（标题/艺术家）', value: '' },
        { name: 'sort', type: 'string', required: '选填', desc: '排序：newest / views / rating', value: 'newest' }
      ],
      response: {
        items: [
          {
            chartKey: '123:dx:4',
            songId: 123,
            title: '曲名',
            artist: '艺术家',
            kind: 'dx',
            difficulty: 4,
            levelLabel: 'MASTER',
            views: 1234,
            ratingAverage: 4.2,
            ratingCount: 56,
            commentCount: 12,
            correctionCount: 3
          }
        ],
        total: 100,
        page: 1,
        limit: 20
      }
    }
  ]"
/>

---

#### 1.2 获取谱面详情

**:chartKey 格式**: `{songId}:{kind}:{difficulty}`，例如 `123:dx:4`

<ApiDemo
  :options="[
    {
      title: '获取谱面详情',
      method: 'GET',
      path: '/api/v1/charts/:chartKey',
      description: '获取单个谱面的详细信息，包含配置分析数据。',
      params: [
        { name: 'chartKey', type: 'string', required: '必填', desc: '谱面 Key，格式：{songId}:{kind}:{difficulty}', value: '123:dx:4' }
      ],
      response: {
        chartKey: '123:dx:4',
        songId: 123,
        title: '曲名',
        artist: '艺术家',
        kind: 'dx',
        difficulty: 4,
        levelLabel: 'MASTER',
        views: 1234,
        ratingAverage: 4.2,
        ratingCount: 56,
        commentCount: 12,
        correctionCount: 3,
        configAnalysis: {}
      }
    }
  ]"
/>

---

### 2. 评论接口

#### 2.1 获取谱面评论

<ApiDemo
  :options="[
    {
      title: '获取谱面评论',
      method: 'GET',
      path: '/api/v1/charts/:chartKey/comments',
      description: '获取指定谱面的评论列表，支持分页。',
      params: [
        { name: 'chartKey', type: 'string', required: '必填', desc: '谱面 Key，格式：{songId}:{kind}:{difficulty}', value: '123:dx:4' },
        { name: 'page', type: 'integer', required: '选填', desc: '页码', value: '1' },
        { name: 'limit', type: 'integer', required: '选填', desc: '每页数量', value: '15' }
      ],
      response: {
        items: [
          {
            id: 'uuid',
            author: '用户名',
            body: '评论内容',
            rating: 4.5,
            likeCount: 5,
            createdAt: '2024-01-01T00:00:00Z'
          }
        ],
        total: 20,
        page: 1,
        limit: 15
      }
    }
  ]"
/>

---

### 3. 评分接口

#### 3.1 获取评分统计

<ApiDemo
  :options="[
    {
      title: '获取评分统计',
      method: 'GET',
      path: '/api/v1/charts/:chartKey/ratings',
      description: '获取指定谱面的评分统计数据，包括均分、评分人数与评分分布。',
      params: [
        { name: 'chartKey', type: 'string', required: '必填', desc: '谱面 Key，格式：{songId}:{kind}:{difficulty}', value: '123:dx:4' }
      ],
      response: {
        chartKey: '123:dx:4',
        ratingAverage: 4.2,
        ratingCount: 56,
        distribution: {
          '5.0': 10,
          '4.5': 20,
          '4.0': 15
        }
      }
    }
  ]"
/>

---

### 4. 成绩接口

#### 4.1 获取成绩提交

<ApiDemo
  :options="[
    {
      title: '获取成绩提交',
      method: 'GET',
      path: '/api/v1/charts/:chartKey/scores',
      description: '获取指定谱面的成绩提交记录与统计信息。',
      params: [
        { name: 'chartKey', type: 'string', required: '必填', desc: '谱面 Key，格式：{songId}:{kind}:{difficulty}', value: '123:dx:4' },
        { name: 'page', type: 'integer', required: '选填', desc: '页码', value: '1' },
        { name: 'limit', type: 'integer', required: '选填', desc: '每页数量', value: '15' }
      ],
      response: {
        items: [
          {
            id: 'uuid',
            author: '用户名',
            difficulty: 'MASTER',
            achievement: 98.5,
            rating: 15200,
            createdAt: '2024-01-01T00:00:00Z'
          }
        ],
        stats: {
          submissionCount: 50,
          averageAchievement: 95.2,
          bestAchievement: 100.5,
          averageRating: 14800
        },
        total: 50,
        page: 1,
        limit: 15
      }
    }
  ]"
/>

---

### 5. 排行榜接口

#### 5.1 获取排行榜

<ApiDemo
  :options="[
    {
      title: '获取排行榜',
      method: 'GET',
      path: '/api/v1/rankings',
      description: '获取谱面排行榜，可按浏览量或评分排序。',
      params: [
        { name: 'sort', type: 'string', required: '选填', desc: '排序方式：views 或 rating', value: 'rating' },
        { name: 'limit', type: 'integer', required: '选填', desc: '数量，默认 10，最大 20', value: '10' }
      ],
      response: {
        items: [
          {
            chartKey: '123:dx:4',
            title: '曲名',
            artist: '艺术家',
            kind: 'dx',
            difficulty: 4,
            views: 1234,
            ratingAverage: 4.2,
            ratingCount: 56
          }
        ]
      }
    }
  ]"
/>

---

### 6. 统计接口

#### 6.1 获取全局统计

<ApiDemo
  :options="[
    {
      title: '获取全局统计',
      method: 'GET',
      path: '/api/v1/stats',
      description: '获取平台全局统计数据，包括总浏览量、评分数、评论数、谱面数与用户数。',
      response: {
        totalViews: 100000,
        totalRatings: 5000,
        totalComments: 2000,
        totalCharts: 500,
        totalUsers: 1000
      }
    }
  ]"
/>

---

### 7. API Key 管理接口

::: warning 🔐 需登录
以下接口需要通过网页登录后调用（浏览器 Session 或 JWT）。
:::

#### 7.1 创建 API Key

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: '创建 API Key',
      method: 'POST',
      path: '/api/v1/api-keys',
      paramsIn: 'json',
      description: '创建新的 API Key。创建后请立即复制保存，Key 仅显示一次。',
      params: [
        { name: 'name', type: 'string', required: '必填', desc: 'API Key 名称', value: '我的应用' }
      ],
      response: {
        id: 'uuid',
        name: '我的应用',
        key: 'mk_live_xxxxx...',
        prefix: 'mk_live_xxx',
        createdAt: '2024-01-01T00:00:00Z'
      }
    }
  ]"
/>

#### 7.2 列出我的 API Key

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: '列出我的 API Key',
      method: 'GET',
      path: '/api/v1/api-keys',
      description: '获取当前用户已创建的所有 API Key 列表。',
      response: [
        {
          id: 'uuid',
          name: '我的应用',
          prefix: 'mk_live_xxx',
          createdAt: '2024-01-01T00:00:00Z'
        }
      ]
    }
  ]"
/>

#### 7.3 删除 API Key

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: '删除 API Key',
      method: 'DELETE',
      path: '/api/v1/api-keys/:id',
      description: '删除指定的 API Key，删除后不可恢复。',
      params: [
        { name: 'id', type: 'string', required: '必填', desc: 'API Key ID', value: 'uuid' }
      ],
      response: { message: '已删除' }
    }
  ]"
/>

---

### 8. 管理员接口

::: danger 🔒 需管理员权限
以下接口仅限管理员调用。
:::

#### 8.1 获取 API 使用统计

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: '获取 API 使用统计',
      method: 'GET',
      path: '/api/v1/admin/api-usage',
      description: '获取 API 使用统计，支持按时间段、用户或 API Key 筛选。',
      params: [
        { name: 'period', type: 'string', required: '选填', desc: '统计周期：day / week / month', value: 'day' },
        { name: 'userId', type: 'string', required: '选填', desc: '按用户 ID 筛选', value: '' },
        { name: 'apiKeyId', type: 'string', required: '选填', desc: '按 API Key ID 筛选', value: '' }
      ],
      response: {
        summary: {
          totalRequests: 10000,
          uniqueUsers: 50,
          uniqueApiKeys: 80,
          averageResponseTime: 45
        },
        byEndpoint: [
          { endpoint: '/api/v1/charts', count: 5000, averageResponseTime: 32 }
        ],
        byUser: [
          { userId: 'user123', username: '用户A', requestCount: 2000 }
        ]
      }
    }
  ]"
/>

#### 8.2 获取调用趋势

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: '获取调用趋势',
      method: 'GET',
      path: '/api/v1/admin/api-usage/trends',
      description: '获取 API 调用趋势数据，按小时或天聚合。',
      params: [
        { name: 'period', type: 'string', required: '选填', desc: '统计周期：day / week / month', value: 'day' },
        { name: 'groupBy', type: 'string', required: '选填', desc: '聚合粒度：hour / day', value: 'day' }
      ],
      response: {
        trends: [
          { timestamp: '2024-01-01', requests: 150, uniqueUsers: 10, averageResponseTime: 42 }
        ]
      }
    }
  ]"
/>

---

## 错误响应

所有接口在出错时返回统一格式：

```json
{
  "message": "错误信息"
}
```

| 状态码 | 说明 |
|--------|------|
| 401 | 未认证或 API Key 无效 |
| 403 | 权限不足 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

---

## 快速开始

```bash
# 1. 获取谱面列表
curl -H "Authorization: Bearer mk_live_xxx" \
  https://v.wmc.pub/api/v1/charts

# 2. 获取谱面详情
curl -H "Authorization: Bearer mk_live_xxx" \
  https://v.wmc.pub/api/v1/charts/123:dx:4

# 3. 获取评论
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/charts/123:dx:4/comments"

# 4. 获取排行榜
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/rankings?sort=rating&limit=10"
```
