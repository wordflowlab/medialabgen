# 影海拾贝 - API接口规范文档

## 1. API概述

### 1.1 基础信息
- **基础URL**: `https://api.yinghaishibe.com/v1`
- **协议**: HTTPS
- **数据格式**: JSON
- **字符编码**: UTF-8
- **API版本**: v1.0.0

### 1.2 认证方式
API使用Bearer Token认证，所有需要认证的请求必须在Header中包含：
```
Authorization: Bearer <your_access_token>
```

### 1.3 请求格式
```http
POST /api/v1/resource
Host: api.yinghaishibe.com
Content-Type: application/json
Authorization: Bearer <token>

{
  "key": "value"
}
```

### 1.4 响应格式
#### 成功响应
```json
{
  "success": true,
  "data": {
    // 响应数据
  },
  "meta": {
    "timestamp": "2024-08-09T10:00:00Z",
    "request_id": "req_abc123"
  }
}
```

#### 错误响应
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "错误描述",
    "details": {
      // 错误详情
    }
  },
  "meta": {
    "timestamp": "2024-08-09T10:00:00Z",
    "request_id": "req_abc123"
  }
}
```

## 2. 认证相关API

### 2.1 用户注册
**POST** `/auth/register`

#### 请求参数
```json
{
  "email": "user@example.com",
  "password": "securePassword123",
  "username": "username123",
  "invite_code": "INVITE123" // 可选
}
```

#### 响应示例
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "username": "username123",
      "created_at": "2024-08-09T10:00:00Z"
    },
    "tokens": {
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
      "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
      "expires_in": 3600
    }
  }
}
```

### 2.2 用户登录
**POST** `/auth/login`

#### 请求参数
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

#### 响应示例
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "username": "username123"
    },
    "tokens": {
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
      "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
      "expires_in": 3600
    }
  }
}
```

### 2.3 刷新令牌
**POST** `/auth/refresh`

#### 请求参数
```json
{
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGc..."
}
```

#### 响应示例
```json
{
  "success": true,
  "data": {
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
    "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
    "expires_in": 3600
  }
}
```

### 2.4 退出登录
**POST** `/auth/logout`

需要认证：是

#### 响应示例
```json
{
  "success": true,
  "data": {
    "message": "Successfully logged out"
  }
}
```

## 3. 用户相关API

### 3.1 获取用户信息
**GET** `/user/profile`

需要认证：是

#### 响应示例
```json
{
  "success": true,
  "data": {
    "id": "user_123",
    "email": "user@example.com",
    "username": "username123",
    "avatar_url": "https://cdn.yinghaishibe.com/avatars/user_123.jpg",
    "subscription": {
      "tier": "professional",
      "credits": 450,
      "expires_at": "2024-12-31T23:59:59Z"
    },
    "stats": {
      "total_projects": 25,
      "total_videos": 150,
      "storage_used": 2147483648
    },
    "created_at": "2024-01-01T00:00:00Z"
  }
}
```

### 3.2 更新用户信息
**PUT** `/user/profile`

需要认证：是

#### 请求参数
```json
{
  "username": "new_username",
  "avatar_url": "https://example.com/avatar.jpg",
  "preferences": {
    "theme": "dark",
    "language": "zh-CN",
    "email_notifications": true
  }
}
```

## 4. 项目管理API

### 4.1 获取项目列表
**GET** `/projects`

需要认证：是

#### 查询参数
- `page`: 页码（默认：1）
- `limit`: 每页数量（默认：20，最大：100）
- `sort`: 排序字段（created_at, updated_at, title）
- `order`: 排序方向（asc, desc）
- `search`: 搜索关键词
- `tags`: 标签过滤（逗号分隔）

#### 响应示例
```json
{
  "success": true,
  "data": {
    "projects": [
      {
        "id": "proj_123",
        "title": "TED演讲总结",
        "description": "收集和整理TED演讲内容",
        "tags": ["教育", "科技", "英语"],
        "video_count": 12,
        "is_public": false,
        "created_at": "2024-08-01T10:00:00Z",
        "updated_at": "2024-08-09T10:00:00Z"
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 5,
      "total_items": 98,
      "items_per_page": 20
    }
  }
}
```

### 4.2 创建项目
**POST** `/projects`

需要认证：是

#### 请求参数
```json
{
  "title": "新项目名称",
  "description": "项目描述",
  "tags": ["标签1", "标签2"],
  "is_public": false
}
```

### 4.3 获取项目详情
**GET** `/projects/{project_id}`

需要认证：是

#### 响应示例
```json
{
  "success": true,
  "data": {
    "id": "proj_123",
    "title": "TED演讲总结",
    "description": "收集和整理TED演讲内容",
    "tags": ["教育", "科技", "英语"],
    "is_public": false,
    "video_sources": [
      {
        "id": "video_456",
        "platform": "youtube",
        "video_id": "dQw4w9WgXcQ",
        "title": "How AI will change everything",
        "duration": 900,
        "thumbnail_url": "https://i.ytimg.com/vi/dQw4w9WgXcQ/maxresdefault.jpg",
        "status": "completed"
      }
    ],
    "generated_content": [
      {
        "id": "content_789",
        "type": "article",
        "format": "markdown",
        "title": "AI改变一切：深度解析",
        "created_at": "2024-08-09T10:00:00Z"
      }
    ],
    "stats": {
      "total_videos": 12,
      "total_articles": 8,
      "total_mindmaps": 5,
      "total_ppts": 3
    },
    "created_at": "2024-08-01T10:00:00Z",
    "updated_at": "2024-08-09T10:00:00Z"
  }
}
```

### 4.4 更新项目
**PUT** `/projects/{project_id}`

需要认证：是

### 4.5 删除项目
**DELETE** `/projects/{project_id}`

需要认证：是

## 5. 视频处理API

### 5.1 解析视频URL
**POST** `/videos/parse`

需要认证：是

#### 请求参数
```json
{
  "url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
  "options": {
    "extract_subtitles": true,
    "preferred_language": "zh-CN",
    "extract_keyframes": false
  }
}
```

#### 响应示例
```json
{
  "success": true,
  "data": {
    "platform": "youtube",
    "video_id": "dQw4w9WgXcQ",
    "title": "Video Title",
    "description": "Video description...",
    "duration": 215,
    "thumbnail_url": "https://i.ytimg.com/vi/dQw4w9WgXcQ/maxresdefault.jpg",
    "author": "Channel Name",
    "published_at": "2024-01-01T00:00:00Z",
    "view_count": 1000000,
    "available_subtitles": ["en", "zh-CN", "ja"],
    "tags": ["tag1", "tag2"]
  }
}
```

### 5.2 添加视频到项目
**POST** `/projects/{project_id}/videos`

需要认证：是

#### 请求参数
```json
{
  "video_url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
  "auto_process": true,
  "processing_options": {
    "extract_subtitle": true,
    "generate_summary": true,
    "generate_article": false,
    "subtitle_language": "zh-CN"
  }
}
```

### 5.3 开始提取任务
**POST** `/videos/{video_id}/extract`

需要认证：是

#### 请求参数
```json
{
  "tasks": [
    {
      "type": "subtitle",
      "options": {
        "language": "zh-CN",
        "format": "srt"
      }
    },
    {
      "type": "audio",
      "options": {
        "format": "mp3",
        "quality": "high"
      }
    },
    {
      "type": "keyframes",
      "options": {
        "interval": 30,
        "format": "jpg"
      }
    }
  ]
}
```

#### 响应示例
```json
{
  "success": true,
  "data": {
    "task_id": "task_abc123",
    "status": "processing",
    "tasks": [
      {
        "id": "subtask_1",
        "type": "subtitle",
        "status": "pending"
      },
      {
        "id": "subtask_2",
        "type": "audio",
        "status": "pending"
      }
    ],
    "estimated_time": 120
  }
}
```

### 5.4 获取任务状态
**GET** `/tasks/{task_id}`

需要认证：是

#### 响应示例
```json
{
  "success": true,
  "data": {
    "id": "task_abc123",
    "status": "processing",
    "progress": 65,
    "current_step": "Extracting audio...",
    "subtasks": [
      {
        "id": "subtask_1",
        "type": "subtitle",
        "status": "completed",
        "result": {
          "file_url": "https://cdn.yinghaishibe.com/subtitles/sub_123.srt",
          "language": "zh-CN",
          "duration": 215
        }
      },
      {
        "id": "subtask_2",
        "type": "audio",
        "status": "processing",
        "progress": 30
      }
    ],
    "started_at": "2024-08-09T10:00:00Z",
    "estimated_completion": "2024-08-09T10:02:00Z"
  }
}
```

## 6. 内容生成API

### 6.1 生成文章
**POST** `/generate/article`

需要认证：是

#### 请求参数
```json
{
  "video_id": "video_456",
  "template_id": "template_123", // 可选
  "options": {
    "style": "professional", // casual, professional, academic
    "length": "medium", // short(500), medium(1000), long(2000+)
    "tone": "informative", // informative, persuasive, entertaining
    "include_sections": ["introduction", "main_points", "conclusion", "references"],
    "language": "zh-CN",
    "ai_model": "gpt-4" // gpt-4, claude-3
  },
  "custom_prompt": "请重点分析视频中提到的技术趋势..." // 可选
}
```

#### 响应示例
```json
{
  "success": true,
  "data": {
    "task_id": "gen_task_789",
    "status": "processing",
    "estimated_time": 30
  }
}
```

### 6.2 生成PPT
**POST** `/generate/ppt`

需要认证：是

#### 请求参数
```json
{
  "video_id": "video_456",
  "template_id": "ppt_template_456",
  "options": {
    "slides_count": 10,
    "include_images": true,
    "design_theme": "professional_blue",
    "language": "zh-CN",
    "format": "pptx" // pptx, pdf, google_slides
  }
}
```

### 6.3 生成思维导图
**POST** `/generate/mindmap`

需要认证：是

#### 请求参数
```json
{
  "video_id": "video_456",
  "options": {
    "structure": "radial", // radial, tree, org_chart
    "max_depth": 4,
    "include_timestamps": true,
    "color_scheme": "rainbow",
    "format": "xmind" // xmind, png, svg, json
  }
}
```

### 6.4 生成摘要
**POST** `/generate/summary`

需要认证：是

#### 请求参数
```json
{
  "video_id": "video_456",
  "options": {
    "type": "bullet_points", // paragraph, bullet_points, timeline
    "length": 150, // 字数
    "focus_areas": ["key_points", "conclusions", "action_items"],
    "language": "zh-CN"
  }
}
```

### 6.5 获取生成结果
**GET** `/generate/{task_id}/result`

需要认证：是

#### 响应示例
```json
{
  "success": true,
  "data": {
    "id": "gen_task_789",
    "type": "article",
    "status": "completed",
    "result": {
      "title": "AI如何改变世界：深度解析",
      "content": "# AI如何改变世界\n\n## 引言\n...",
      "format": "markdown",
      "metadata": {
        "word_count": 1523,
        "reading_time": 6,
        "language": "zh-CN",
        "keywords": ["AI", "技术", "未来"]
      },
      "download_url": "https://cdn.yinghaishibe.com/articles/article_789.md",
      "preview_url": "https://app.yinghaishibe.com/preview/article_789"
    },
    "ai_usage": {
      "model": "gpt-4",
      "tokens_used": 2500,
      "cost": 0.075
    },
    "created_at": "2024-08-09T10:00:00Z"
  }
}
```

## 7. 模板管理API

### 7.1 获取模板列表
**GET** `/templates`

需要认证：是

#### 查询参数
- `type`: 模板类型（article, ppt, mindmap）
- `category`: 分类
- `is_public`: 是否公开
- `sort`: 排序字段（usage_count, created_at, rating）

#### 响应示例
```json
{
  "success": true,
  "data": {
    "templates": [
      {
        "id": "template_123",
        "name": "学术论文模板",
        "type": "article",
        "category": "academic",
        "description": "适用于学术研究内容的文章模板",
        "preview_image": "https://cdn.yinghaishibe.com/templates/preview_123.jpg",
        "is_public": true,
        "usage_count": 1523,
        "rating": 4.8,
        "author": {
          "id": "user_456",
          "username": "academic_writer"
        },
        "created_at": "2024-07-01T00:00:00Z"
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 3,
      "total_items": 45
    }
  }
}
```

### 7.2 创建模板
**POST** `/templates`

需要认证：是

#### 请求参数
```json
{
  "name": "我的文章模板",
  "type": "article",
  "category": "business",
  "description": "商业分析文章模板",
  "is_public": false,
  "config": {
    "structure": {
      "sections": [
        {
          "name": "executive_summary",
          "title": "执行摘要",
          "required": true,
          "max_words": 200
        },
        {
          "name": "market_analysis",
          "title": "市场分析",
          "required": true,
          "include_charts": true
        }
      ]
    },
    "style": {
      "tone": "professional",
      "perspective": "third_person",
      "citation_style": "apa"
    },
    "formatting": {
      "font": "Arial",
      "font_size": 12,
      "line_spacing": 1.5
    }
  }
}
```

### 7.3 使用模板
**POST** `/templates/{template_id}/use`

需要认证：是

#### 请求参数
```json
{
  "video_id": "video_456",
  "customizations": {
    "title": "自定义标题",
    "additional_sections": ["custom_analysis"],
    "variables": {
      "company_name": "示例公司",
      "report_date": "2024-08-09"
    }
  }
}
```

## 8. 文件管理API

### 8.1 上传文件
**POST** `/files/upload`

需要认证：是

#### 请求格式
- Content-Type: multipart/form-data
- 最大文件大小：500MB（视频），10MB（其他）

#### 表单字段
- `file`: 文件内容
- `type`: 文件类型（video, image, document）
- `project_id`: 关联的项目ID（可选）

#### 响应示例
```json
{
  "success": true,
  "data": {
    "file_id": "file_abc123",
    "filename": "video.mp4",
    "size": 104857600,
    "mime_type": "video/mp4",
    "url": "https://cdn.yinghaishibe.com/files/file_abc123.mp4",
    "thumbnail_url": "https://cdn.yinghaishibe.com/thumbnails/file_abc123.jpg",
    "uploaded_at": "2024-08-09T10:00:00Z"
  }
}
```

### 8.2 获取文件信息
**GET** `/files/{file_id}`

需要认证：是

### 8.3 删除文件
**DELETE** `/files/{file_id}`

需要认证：是

## 9. 订阅和计费API

### 9.1 获取订阅计划
**GET** `/subscription/plans`

#### 响应示例
```json
{
  "success": true,
  "data": {
    "plans": [
      {
        "id": "plan_free",
        "name": "免费版",
        "price": 0,
        "currency": "CNY",
        "period": "monthly",
        "features": {
          "videos_per_month": 5,
          "storage_gb": 1,
          "ai_credits": 100,
          "export_formats": ["markdown", "txt"],
          "support_level": "community"
        }
      },
      {
        "id": "plan_pro",
        "name": "专业版",
        "price": 99,
        "currency": "CNY",
        "period": "monthly",
        "features": {
          "videos_per_month": 200,
          "storage_gb": 50,
          "ai_credits": 5000,
          "export_formats": ["all"],
          "support_level": "priority",
          "api_access": true,
          "custom_templates": true
        }
      }
    ]
  }
}
```

### 9.2 订阅升级
**POST** `/subscription/upgrade`

需要认证：是

#### 请求参数
```json
{
  "plan_id": "plan_pro",
  "payment_method": "alipay", // alipay, wechat, card
  "period": "yearly", // monthly, yearly
  "coupon_code": "DISCOUNT20" // 可选
}
```

### 9.3 获取使用情况
**GET** `/subscription/usage`

需要认证：是

#### 响应示例
```json
{
  "success": true,
  "data": {
    "current_period": {
      "start": "2024-08-01T00:00:00Z",
      "end": "2024-08-31T23:59:59Z"
    },
    "usage": {
      "videos_processed": 45,
      "videos_limit": 200,
      "storage_used_gb": 12.5,
      "storage_limit_gb": 50,
      "ai_credits_used": 1250,
      "ai_credits_limit": 5000
    },
    "history": [
      {
        "date": "2024-08-09",
        "videos": 3,
        "storage_mb": 450,
        "ai_credits": 125
      }
    ]
  }
}
```

## 10. Webhook API

### 10.1 注册Webhook
**POST** `/webhooks`

需要认证：是

#### 请求参数
```json
{
  "url": "https://your-server.com/webhook",
  "events": [
    "task.completed",
    "task.failed",
    "video.processed",
    "content.generated"
  ],
  "secret": "your_webhook_secret",
  "is_active": true
}
```

### 10.2 Webhook事件格式

#### 任务完成事件
```json
{
  "event": "task.completed",
  "timestamp": "2024-08-09T10:00:00Z",
  "data": {
    "task_id": "task_123",
    "type": "video_extraction",
    "video_id": "video_456",
    "project_id": "proj_789",
    "result": {
      // 任务结果数据
    }
  },
  "signature": "sha256=abcdef..." // HMAC-SHA256签名
}
```

## 11. 错误码说明

### 11.1 通用错误码

| 错误码 | HTTP状态码 | 说明 |
|--------|------------|------|
| AUTH_REQUIRED | 401 | 需要认证 |
| AUTH_INVALID | 401 | 认证无效 |
| AUTH_EXPIRED | 401 | 认证已过期 |
| PERMISSION_DENIED | 403 | 权限不足 |
| NOT_FOUND | 404 | 资源不存在 |
| METHOD_NOT_ALLOWED | 405 | 方法不允许 |
| VALIDATION_ERROR | 422 | 参数验证失败 |
| RATE_LIMIT_EXCEEDED | 429 | 请求频率超限 |
| SERVER_ERROR | 500 | 服务器内部错误 |
| SERVICE_UNAVAILABLE | 503 | 服务暂时不可用 |

### 11.2 业务错误码

| 错误码 | 说明 |
|--------|------|
| VIDEO_PARSE_FAILED | 视频解析失败 |
| VIDEO_NOT_SUPPORTED | 不支持的视频平台 |
| VIDEO_PRIVATE | 视频为私密视频 |
| VIDEO_DELETED | 视频已被删除 |
| SUBTITLE_NOT_AVAILABLE | 字幕不可用 |
| QUOTA_EXCEEDED | 配额已用完 |
| PAYMENT_REQUIRED | 需要付费 |
| TEMPLATE_NOT_FOUND | 模板不存在 |
| AI_SERVICE_ERROR | AI服务错误 |
| FILE_TOO_LARGE | 文件过大 |
| INVALID_FILE_FORMAT | 无效的文件格式 |

## 12. 限流规则

### 12.1 全局限流
- 每个API密钥：1000请求/小时
- 每个IP地址：100请求/分钟

### 12.2 接口级限流

| 接口类型 | 限流规则 |
|----------|----------|
| 认证接口 | 10次/分钟 |
| 视频解析 | 30次/分钟 |
| 内容生成 | 10次/分钟 |
| 文件上传 | 5次/分钟 |
| Webhook | 100次/分钟 |

### 12.3 限流响应头
```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1628505600
```

## 13. SDK支持

### 13.1 官方SDK
- JavaScript/TypeScript: `npm install @yinghaishibe/sdk`
- Python: `pip install yinghaishibe`
- Go: `go get github.com/yinghaishibe/sdk-go`

### 13.2 SDK使用示例

#### JavaScript
```javascript
import { YingHaiShiBe } from '@yinghaishibe/sdk';

const client = new YingHaiShiBe({
  apiKey: 'your_api_key',
  baseUrl: 'https://api.yinghaishibe.com/v1' // 可选
});

// 解析视频
const video = await client.videos.parse({
  url: 'https://www.youtube.com/watch?v=dQw4w9WgXcQ'
});

// 生成文章
const article = await client.generate.article({
  videoId: video.id,
  options: {
    style: 'professional',
    length: 'medium'
  }
});

// 监听任务进度
client.on('task:progress', (data) => {
  console.log(`Progress: ${data.progress}%`);
});
```

#### Python
```python
from yinghaishibe import Client

client = Client(api_key='your_api_key')

# 解析视频
video = client.videos.parse(
    url='https://www.youtube.com/watch?v=dQw4w9WgXcQ'
)

# 生成文章
article = client.generate.article(
    video_id=video.id,
    options={
        'style': 'professional',
        'length': 'medium'
    }
)

# 异步处理
import asyncio

async def process_video():
    async with client.async_session() as session:
        task = await session.videos.extract(video.id, tasks=['subtitle'])
        result = await task.wait_for_completion()
        return result

asyncio.run(process_video())
```

## 14. 最佳实践

### 14.1 认证管理
- 定期刷新访问令牌
- 安全存储API密钥
- 使用环境变量管理密钥
- 实施令牌轮换策略

### 14.2 错误处理
```javascript
try {
  const result = await client.videos.parse({ url });
} catch (error) {
  if (error.code === 'RATE_LIMIT_EXCEEDED') {
    // 等待后重试
    await sleep(error.retryAfter);
    return retry();
  } else if (error.code === 'VIDEO_PRIVATE') {
    // 处理私密视频
    console.log('视频为私密状态');
  } else {
    // 其他错误
    console.error('未知错误:', error);
  }
}
```

### 14.3 批量处理
```javascript
// 使用批量API减少请求次数
const results = await client.batch([
  { method: 'POST', path: '/videos/parse', body: { url: url1 } },
  { method: 'POST', path: '/videos/parse', body: { url: url2 } },
  { method: 'POST', path: '/videos/parse', body: { url: url3 } }
]);
```

### 14.4 Webhook验证
```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const hmac = crypto.createHmac('sha256', secret);
  const digest = 'sha256=' + hmac.update(payload).digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(digest)
  );
}
```

## 15. 更新日志

### v1.0.0 (2024-08-09)
- 初始版本发布
- 支持YouTube、Bilibili、TED视频解析
- 文章、PPT、思维导图生成功能
- 模板系统
- Webhook支持

### 即将推出
- GraphQL API支持
- 更多视频平台支持
- 实时协作功能
- 批量处理优化
- AI模型自定义

---

**文档版本**: v1.0.0  
**最后更新**: 2024-08-09  
**API状态页面**: https://status.yinghaishibe.com  
**开发者论坛**: https://forum.yinghaishibe.com  
**技术支持**: support@yinghaishibe.com