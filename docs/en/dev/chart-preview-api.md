---
chartPreviewApiBaseUrl: https://v.wmc.pub
---

# Chart Review & Analysis API

Public REST API for the Chart Preview platform, supporting chart queries, comments, ratings, score submissions, and rankings.

::: tip Platform
- **Base URL**: `{{ $frontmatter.chartPreviewApiBaseUrl }}/api/v1/`
- **Source**: [Pimeng/Maimai-Chart-Preview](https://github.com/Pimeng/Maimai-Chart-Preview)
:::

::: warning 🔐 Authentication
All API requests require a Bearer token in the header:

`Authorization: Bearer <api-key>`

To obtain an API Key: log in at https://v.wmc.pub → click the key icon in the top navigation → `/api-keys` page → create a new API Key.
:::

::: danger ⚠️ Note
API Keys are **only displayed once** at creation. If lost, you must create a new one.
:::

---

## Obtaining an API Key

1. Log in at https://v.wmc.pub
2. Click the key icon in the top navigation bar to go to `/api-keys`
3. Click "Create", enter a name, and copy the key after creation

---

## Endpoints

### 1. Charts

#### 1.1 List Charts

<ApiDemo
  :options="[
    {
      title: 'List Charts',
      method: 'GET',
      path: '/api/v1/charts',
      description: 'List charts with pagination, filtering by type/difficulty, keyword search, and sorting.',
      params: [
        { name: 'page', type: 'integer', required: 'Optional', desc: 'Page number, default 1', value: '1' },
        { name: 'limit', type: 'integer', required: 'Optional', desc: 'Items per page, default 20, max 50', value: '20' },
        { name: 'kind', type: 'string', required: 'Optional', desc: 'standard or dx', value: 'dx' },
        { name: 'difficulty', type: 'integer', required: 'Optional', desc: 'Difficulty 1-6', value: '5' },
        { name: 'q', type: 'string', required: 'Optional', desc: 'Search keyword (title/artist)', value: '' },
        { name: 'sort', type: 'string', required: 'Optional', desc: 'Sort: newest / views / rating', value: 'newest' }
      ],
      response: {
        items: [
          {
            chartKey: '123:dx:4',
            songId: 123,
            title: 'Song Title',
            artist: 'Artist',
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

#### 1.2 Get Chart Detail

**:chartKey format**: `{songId}:{kind}:{difficulty}`, e.g. `123:dx:4`

<ApiDemo
  :options="[
    {
      title: 'Get Chart Detail',
      method: 'GET',
      path: '/api/v1/charts/:chartKey',
      description: 'Get detailed information for a single chart, including config analysis data.',
      params: [
        { name: 'chartKey', type: 'string', required: 'Required', desc: 'Chart Key, format: {songId}:{kind}:{difficulty}', value: '123:dx:4' }
      ],
      response: {
        chartKey: '123:dx:4',
        songId: 123,
        title: 'Song Title',
        artist: 'Artist',
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

### 2. Comments

#### 2.1 Get Chart Comments

<ApiDemo
  :options="[
    {
      title: 'Get Chart Comments',
      method: 'GET',
      path: '/api/v1/charts/:chartKey/comments',
      description: 'Get comments for a specific chart, with pagination support.',
      params: [
        { name: 'chartKey', type: 'string', required: 'Required', desc: 'Chart Key, format: {songId}:{kind}:{difficulty}', value: '123:dx:4' },
        { name: 'page', type: 'integer', required: 'Optional', desc: 'Page number', value: '1' },
        { name: 'limit', type: 'integer', required: 'Optional', desc: 'Items per page', value: '15' }
      ],
      response: {
        items: [
          {
            id: 'uuid',
            author: 'username',
            body: 'Comment content',
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

### 3. Ratings

#### 3.1 Get Rating Statistics

<ApiDemo
  :options="[
    {
      title: 'Get Rating Statistics',
      method: 'GET',
      path: '/api/v1/charts/:chartKey/ratings',
      description: 'Get rating statistics for a specific chart, including average score, rating count, and distribution.',
      params: [
        { name: 'chartKey', type: 'string', required: 'Required', desc: 'Chart Key, format: {songId}:{kind}:{difficulty}', value: '123:dx:4' }
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

### 4. Scores

#### 4.1 Get Score Submissions

<ApiDemo
  :options="[
    {
      title: 'Get Score Submissions',
      method: 'GET',
      path: '/api/v1/charts/:chartKey/scores',
      description: 'Get score submission records and statistics for a specific chart.',
      params: [
        { name: 'chartKey', type: 'string', required: 'Required', desc: 'Chart Key, format: {songId}:{kind}:{difficulty}', value: '123:dx:4' },
        { name: 'page', type: 'integer', required: 'Optional', desc: 'Page number', value: '1' },
        { name: 'limit', type: 'integer', required: 'Optional', desc: 'Items per page', value: '15' }
      ],
      response: {
        items: [
          {
            id: 'uuid',
            author: 'username',
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

### 5. Rankings

#### 5.1 Get Rankings

<ApiDemo
  :options="[
    {
      title: 'Get Rankings',
      method: 'GET',
      path: '/api/v1/rankings',
      description: 'Get chart rankings, sortable by views or rating.',
      params: [
        { name: 'sort', type: 'string', required: 'Optional', desc: 'Sort by: views or rating', value: 'rating' },
        { name: 'limit', type: 'integer', required: 'Optional', desc: 'Number of results, default 10, max 20', value: '10' }
      ],
      response: {
        items: [
          {
            chartKey: '123:dx:4',
            title: 'Song Title',
            artist: 'Artist',
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

### 6. Statistics

#### 6.1 Get Global Statistics

<ApiDemo
  :options="[
    {
      title: 'Get Global Statistics',
      method: 'GET',
      path: '/api/v1/stats',
      description: 'Get platform-wide statistics including total views, ratings, comments, charts, and users.',
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

### 7. API Key Management

::: warning 🔐 Login Required
The following endpoints require an active login session (browser session or JWT).
:::

#### 7.1 Create API Key

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: 'Create API Key',
      method: 'POST',
      path: '/api/v1/api-keys',
      paramsIn: 'json',
      description: 'Create a new API Key. Copy and save it immediately after creation — it is only displayed once.',
      params: [
        { name: 'name', type: 'string', required: 'Required', desc: 'API Key name', value: 'My App' }
      ],
      response: {
        id: 'uuid',
        name: 'My App',
        key: 'mk_live_xxxxx...',
        prefix: 'mk_live_xxx',
        createdAt: '2024-01-01T00:00:00Z'
      }
    }
  ]"
/>

#### 7.2 List My API Keys

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: 'List My API Keys',
      method: 'GET',
      path: '/api/v1/api-keys',
      description: 'Get a list of all API Keys created by the current user.',
      response: [
        {
          id: 'uuid',
          name: 'My App',
          prefix: 'mk_live_xxx',
          createdAt: '2024-01-01T00:00:00Z'
        }
      ]
    }
  ]"
/>

#### 7.3 Delete API Key

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: 'Delete API Key',
      method: 'DELETE',
      path: '/api/v1/api-keys/:id',
      description: 'Delete a specific API Key. This action cannot be undone.',
      params: [
        { name: 'id', type: 'string', required: 'Required', desc: 'API Key ID', value: 'uuid' }
      ],
      response: { message: 'Deleted' }
    }
  ]"
/>

---

### 8. Admin Endpoints

::: danger 🔒 Admin Only
The following endpoints are restricted to administrators.
:::

#### 8.1 Get API Usage Statistics

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: 'Get API Usage Statistics',
      method: 'GET',
      path: '/api/v1/admin/api-usage',
      description: 'Get API usage statistics, filterable by time period, user, or API Key.',
      params: [
        { name: 'period', type: 'string', required: 'Optional', desc: 'Time period: day / week / month', value: 'day' },
        { name: 'userId', type: 'string', required: 'Optional', desc: 'Filter by user ID', value: '' },
        { name: 'apiKeyId', type: 'string', required: 'Optional', desc: 'Filter by API Key ID', value: '' }
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
          { userId: 'user123', username: 'User A', requestCount: 2000 }
        ]
      }
    }
  ]"
/>

#### 8.2 Get Usage Trends

<ApiDemo
  baseUrl="https://v.wmc.pub"
  :options="[
    {
      title: 'Get Usage Trends',
      method: 'GET',
      path: '/api/v1/admin/api-usage/trends',
      description: 'Get API usage trend data, aggregated by hour or day.',
      params: [
        { name: 'period', type: 'string', required: 'Optional', desc: 'Time period: day / week / month', value: 'day' },
        { name: 'groupBy', type: 'string', required: 'Optional', desc: 'Aggregation granularity: hour / day', value: 'day' }
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

## Error Responses

All endpoints return a unified error format:

```json
{
  "message": "Error message"
}
```

| Status Code | Description |
|-------------|-------------|
| 401 | Unauthorized or invalid API Key |
| 403 | Insufficient permissions |
| 404 | Resource not found |
| 500 | Internal server error |

---

## Quick Start

```bash
# 1. List charts
curl -H "Authorization: Bearer mk_live_xxx" \
  https://v.wmc.pub/api/v1/charts

# 2. Get chart detail
curl -H "Authorization: Bearer mk_live_xxx" \
  https://v.wmc.pub/api/v1/charts/123:dx:4

# 3. Get comments
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/charts/123:dx:4/comments"

# 4. Get rankings
curl -H "Authorization: Bearer mk_live_xxx" \
  "https://v.wmc.pub/api/v1/rankings?sort=rating&limit=10"
```
