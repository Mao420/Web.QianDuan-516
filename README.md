# 毛国玲学习成果展示网站

一个基于 Next.js 构建的个人学习成果展示网站，集成了 GitHub 项目展示、WakaTime 编程时间统计和 QAnything AI 助手功能。

## 🌟 项目简介

本项目是一个现代化的个人学习成果展示平台，旨在全面展示 Web 前端开发技术的学习历程和项目成果。网站采用响应式设计，支持深色模式，提供优雅的用户体验。

### 主要特性

- 📊 **GitHub 项目展示**: 动态展示个人 GitHub 仓库信息
- ⏱️ **WakaTime 统计集成**: 实时显示编程时间和技术栈分析
- 🤖 **AI 助手集成**: 嵌入 QAnything 智能助手，提供即时帮助
- 🎨 **现代化 UI**: 使用 Tailwind CSS 构建美观协调的界面
- 📱 **响应式设计**: 完美适配各种设备屏幕
- 🌙 **深色模式支持**: 自动适应系统主题偏好

## 🛠️ 技术栈

- **前端框架**: Next.js 15.3.4
- **UI 库**: React 19.0.0
- **样式框架**: Tailwind CSS 4
- **字体**: Geist Sans & Geist Mono
- **部署**: 支持 Vercel、Netlify 等平台
- **数据源**: GitHub API、WakaTime API (通过 Cloudflare Workers)
- **AI 集成**: QAnything iframe 嵌入

## 📁 项目结构

```
my-next-app/
├── app/
│   ├── components/
│   │   ├── Footer.js          # 页脚组件，包含 WakaTime 统计
│   │   └── GitHubProjects.js  # GitHub 项目展示组件
│   ├── globals.css            # 全局样式
│   ├── layout.js              # 根布局组件
│   ├── page.js                # 主页面组件
│   └── favicon.ico            # 网站图标
├── public/                    # 静态资源目录
│   ├── next.svg
│   ├── vercel.svg
│   └── ...
├── package.json               # 项目依赖配置
├── next.config.mjs           # Next.js 配置
├── tailwind.config.js        # Tailwind CSS 配置
├── postcss.config.mjs        # PostCSS 配置
└── README.md                 # 项目说明文档
```

## 🔗 QAnything AI 助手集成

### 集成路径选择

本项目选择了 **iframe 嵌入方式** 来集成 QAnything AI 助手，原因如下：

#### 选择原因

1. **简单易用**: 通过 script 标签直接嵌入，无需复杂的 API 调用
2. **官方支持**: 使用 QAnything 官方提供的 iframe 解决方案
3. **功能完整**: 保留完整的 AI 助手交互界面和功能
4. **维护成本低**: 无需自行维护 AI 对话逻辑和界面
5. **安全性好**: 通过 iframe 隔离，避免安全风险

#### 实现细节

```javascript
// 在 app/page.js 中使用 Next.js Script 组件
<Script
  src="https://ai.youdao.com/saas/qanything/js/agent-iframe-min.js"
  id="qanything-iframe"
  data-agent-src="https://ai.youdao.com/saas/qanything/#/bots/81D6457A9AD5435E/share"
  data-default-open="false"
  data-drag="false"
  data-open-icon="https://download.ydstatic.com/ead/icon-qanything-iframe-btn.png"
  data-close-icon="https://download.ydstatic.com/ead/icon-qanything-iframe-btn.png"
  defer
/>
```

#### 配置参数说明

- `data-agent-src`: AI 助手的访问地址
- `data-default-open`: 是否默认打开 (设置为 false)
- `data-drag`: 是否允许拖拽 (设置为 false)
- `data-open-icon`: 打开按钮图标
- `data-close-icon`: 关闭按钮图标
- `defer`: 延迟加载，提升页面性能

### QAnything 运行截图

![QAnything AI 助手界面](./展示图片/ai助手运行截图.png)

*QAnything AI 助手嵌入效果展示 - 右下角浮动按钮，点击后展开对话界面*

## 📊 WakaTime API 集成

### 集成方法

本项目通过 **Cloudflare Workers** 作为中间层来获取 WakaTime 数据：

#### 架构设计

```
Next.js 应用 → Cloudflare Workers → WakaTime API → 数据展示
```

#### Cloudflare Workers 配置

- **Workers URL**: `https://falling-hat-c99c.3420808767.workers.dev`
- **预览 URL**: `*-falling-hat-c99c.3420808767.workers.dev`

#### 实现步骤

1. **创建 Cloudflare Workers**
   ```javascript
   // workers.js 示例
   export default {
     async fetch(request, env, ctx) {
       const wakaTimeApiKey = env.WAKATIME_API_KEY;
       const response = await fetch('https://wakatime.com/api/v1/users/current/stats/last_7_days', {
         headers: {
           'Authorization': `Bearer ${wakaTimeApiKey}`,
           'Content-Type': 'application/json'
         }
       });
       
       const data = await response.json();
       return new Response(JSON.stringify(data), {
         headers: {
           'Content-Type': 'application/json',
           'Access-Control-Allow-Origin': '*'
         }
       });
     }
   };
   ```

2. **前端数据获取**
   ```javascript
   // 在 Footer.js 组件中
   const fetchWakaTimeData = async () => {
     try {
       const response = await fetch('https://falling-hat-c99c.3420808767.workers.dev');
       const data = await response.json();
       setWakaTimeData(data);
     } catch (error) {
       console.error('获取 WakaTime 数据失败:', error);
     }
   };
   ```

#### 数据展示内容

- **总编程时间**: 显示累计编程时长和日均时长
- **编程语言分布**: 各种编程语言的使用占比
- **开发工具统计**: 不同编辑器的使用情况
- **项目时间分配**: 各个项目的时间投入比例

### WakaTime 统计截图

![WakaTime 统计界面](./展示图片/wakatme集成截图截图 .png)

*WakaTime 编程时间统计展示 - 包含语言分布、工具使用和项目时间等数据*

## 🎨 设计理念

### 色彩搭配

- **主色调**: 蓝色系 (#3B82F6) - 代表技术和专业
- **辅助色**: 紫色系 (#8B5CF6) - 增加视觉层次
- **强调色**: 绿色系 (#10B981) - 突出重要信息
- **中性色**: 灰色系 - 保证内容可读性

### 布局设计

- **响应式网格**: 使用 CSS Grid 和 Flexbox
- **卡片式布局**: 信息模块化，层次清晰
- **渐变背景**: 增加视觉深度和现代感
- **毛玻璃效果**: 使用 backdrop-blur 增强层次感

## 🚀 快速开始

### 环境要求

- Node.js 18.0 或更高版本
- npm 或 yarn 包管理器

### 安装步骤

1. **克隆项目**
   ```bash
   git clone https://github.com/Mao420/Web.QianDuan-516.git
   cd my-next-app
   ```

2. **安装依赖**
   ```bash
   npm install
   # 或
   yarn install
   ```

3. **启动开发服务器**
   ```bash
   npm run dev
   # 或
   yarn dev
   ```

4. **访问应用**
   
   打开浏览器访问 [http://localhost:3000](http://localhost:3000)

### 构建部署

1. **构建生产版本**
   ```bash
   npm run build
   # 或
   yarn build
   ```

2. **启动生产服务器**
   ```bash
   npm start
   # 或
   yarn start
   ```

3. **部署到 Vercel**
   ```bash
   npm install -g vercel
   vercel
   ```

## 📝 旧作业整合方式

### 整合策略

本项目作为一个展示平台，将之前的学习作业和项目进行了系统性整合：

1. **项目归档**: 将所有作业项目统一存放在 `Web.QianDuan-516` 仓库中
2. **分类展示**: 按照技术栈和项目类型进行分类展示
3. **进度追踪**: 通过 WakaTime 统计展示学习进度
4. **技能展示**: 在技术栈标签中体现掌握的技术

### 展示内容

- **HTML/CSS 基础项目**: 静态页面设计作业
- **JavaScript 交互项目**: 动态效果和逻辑实现
- **框架学习项目**: React、Vue 等框架练习
- **工具使用经验**: Git、构建工具等使用心得

## 🔧 配置说明

### 环境变量

创建 `.env.local` 文件（可选）：

```env
# GitHub API Token (可选，用于提高 API 限制)
GITHUB_TOKEN=your_github_token

# WakaTime API Key (在 Cloudflare Workers 中配置)
WAKATIME_API_KEY=your_wakatime_api_key
```

### 自定义配置

1. **修改 GitHub 仓库信息**
   
   在 `app/components/GitHubProjects.js` 中修改 `repoInfo` 对象：
   ```javascript
   const repoInfo = {
     owner: 'your-username',
     repo: 'your-repo-name',
     url: 'https://github.com/your-username/your-repo-name',
     description: '你的仓库描述'
   };
   ```

2. **更新 WakaTime Workers URL**
   
   在 `app/components/Footer.js` 中修改 API 地址：
   ```javascript
   const wakaTimeApiUrl = 'https://your-workers-url.workers.dev';
   ```

3. **自定义 AI 助手**
   
   在 `app/page.js` 中修改 QAnything 配置：
   ```javascript
   data-agent-src="https://ai.youdao.com/saas/qanything/#/bots/YOUR_BOT_ID/share"
   ```

## 🐛 故障排除

### 常见问题

1. **AI 助手无法加载**
   - 检查网络连接
   - 确认 QAnything 服务状态
   - 验证 bot ID 是否正确

2. **WakaTime 数据显示异常**
   - 检查 Cloudflare Workers 状态
   - 验证 API Key 配置
   - 查看浏览器控制台错误信息

3. **样式显示问题**
   - 清除浏览器缓存
   - 检查 Tailwind CSS 配置
   - 确认 CSS 文件正确加载

### 调试技巧

```bash
# 查看详细错误信息
npm run dev -- --turbo

# 检查构建问题
npm run build -- --debug

# 分析包大小
npm run analyze
```

## 📈 性能优化

### 已实现的优化

- **代码分割**: 使用 Next.js 自动代码分割
- **图片优化**: 使用 Next.js Image 组件
- **字体优化**: 使用 Google Fonts 优化加载
- **懒加载**: Script 组件使用 defer 属性
- **缓存策略**: 静态资源缓存配置

### 进一步优化建议

- 实现 Service Worker 离线缓存
- 使用 CDN 加速静态资源
- 优化 API 请求频率
- 实现骨架屏加载效果

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request 来改进这个项目！

### 开发流程

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

- [Next.js](https://nextjs.org/) - React 框架
- [Tailwind CSS](https://tailwindcss.com/) - CSS 框架
- [WakaTime](https://wakatime.com/) - 编程时间统计
- [QAnything](https://qanything.ai/) - AI 助手服务
- [Cloudflare Workers](https://workers.cloudflare.com/) - 边缘计算平台
- [Vercel](https://vercel.com/) - 部署平台

---

**项目作者**: 毛国玲  
**创建时间**: 2025年  
**最后更新**: 2025年6月  

如有问题或建议，欢迎通过 GitHub Issues 联系我！
