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

#### 1.3 获取谱面标签摘要

<ApiDemo
  :options="[
    {
      title: '获取谱面标签摘要',
      method: 'GET',
      path: '/api/v1/charts/:chartKey/tags',
      description: '返回谱面的难度分类、雷达轴标签、评估轴标签、回归特征贡献和检测到的谱面模式。适用于 Bot / 轻量查询，过滤低分项并按重要性排序。',
      params: [
        { name: 'chartKey', type: 'string', required: '必填', desc: '谱面 Key，格式：{songId}:{kind}:{difficulty}', value: '417:standard:5' },
        { name: 'radar_threshold', type: 'integer', required: '选填', desc: 'radar/eval score 最低阈值，默认 40，范围 20-60', value: '40' },
        { name: 'feature_threshold', type: 'float', required: '选填', desc: '特征贡献绝对值最低阈值，默认 0.5，范围 0.1-2.0', value: '0.5' }
      ],
      response: {
        chartKey: '417:standard:5',
        title: 'ウミユリ海底譚',
        artist: 'n-buna',
        kind: 'standard',
        difficulty: 5,
        levelLabel: '标准 · MASTER 13',
        tags: {
          difficultyClassification: {
            tag: 'normal',
            label: '正常谱',
            estimatedLevel: 13.1,
            deviation: 0.1
          },
          radarTags: [
            { label: '错位', score: 72 },
            { label: '反手', score: 53 },
            { label: '纵连', score: 47 },
            { label: '定拍', score: 45 }
          ],
          evaluationTags: [
            { label: '高物量', score: 94 },
            { label: '体力谱', score: 76 },
            { label: '键盘谱', score: 57 }
          ],
          features: [
            { label: '平均密度', contribution: 11.79, direction: 'up' },
            { label: '高密度惩罚', contribution: -1.64, direction: 'down' },
            { label: '滑条占比', contribution: 1.18, direction: 'up' }
          ],
          patterns: [
            { label: '错位星星', count: 3, severity: 'high' },
            { label: '交互', count: 1, severity: 'high' },
            { label: '纵连', count: 8, severity: 'mid' },
            { label: '反手/大位移同押', count: 26, severity: 'mid' }
          ]
        }
      }
    }
  ]"
/>

**字段说明**:

| 字段 | 类型 | 说明 |
|------|------|------|
| tags.difficultyClassification | object | 难度分类：tag=normal/water/fake，label=正常谱/水/虚高谱 |
| tags.difficultyClassification.estimatedLevel | float | 回归模型预测的难度值 |
| tags.difficultyClassification.deviation | float | 预测值与官标的偏差（正=虚高，负=偏低）|
| tags.radarTags | array | 雷达轴高分项，score 0-100 |
| tags.evaluationTags | array | 评估轴高分项（体力谱/底力谱/星星谱/键盘谱/高物量）|
| tags.features | array | 回归模型贡献最大的特征，direction=up/down 表示提升/降低难度 |
| tags.features.contribution | float | 该特征对难度值的贡献量（正=加难度，负=减难度）|
| tags.patterns | array | 检测到的谱面模式，severity=high/mid/low |
| tags.patterns.count | int | 该模式出现次数 |
| tags.patterns.severity | string | high=高强度（strength≥5），mid=中等，low=低 |

**radarTags 可能的 label**：交互、散打、扫键、绝赞段、转圈、大位移、定拍、拆弹、爆发、纵连、跳拍、错位、一笔画、反手

**evaluationTags 可能的 label**：体力谱、底力谱、星星谱、键盘谱、高物量

**features 可能的 label**：平均密度、高密度惩罚、峰值密度、滑条占比、触摸占比、交叉手、同位连打、跳拍节奏、一笔画、偏移、爆发段、转圈、横扫

**patterns 可能的 label**：错位星星、交互、纵连、跳拍、一笔画、反手/大位移同押、定拍、绝赞段、爆发、扫键、转圈、触摸拆分、散打、拆弹

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

# 3. 获取谱面标签（Bot 用）
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/charts/417:standard:5/tags"

# 4. 自定义阈值（仅显示高分项）
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/charts/417:standard:5/tags?radar_threshold=60&feature_threshold=1.0"

# 5. 获取评论
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/charts/123:dx:4/comments"

# 6. 获取排行榜
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/rankings?sort=rating&limit=10"
```
