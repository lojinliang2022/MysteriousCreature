# MAX — Athlete · Model · Creator

MAX 个人品牌站的静态导出版本。中英双语单页站点,深色主题,面向模特拍摄、品牌与汽车代言、外贸商务接待三类合作洽谈。

坐标:广州 · 佛山。

## 项目结构

```
.
├── index.html          # 全站唯一页面,CSS 与 JS 全部内联
├── fa/                 # Font Awesome(本地托管)
│   ├── css/
│   │   └── fontawesome-all.min.css
│   └── webfonts/
│       ├── fa-brands-400.woff2
│       └── fa-solid-900.woff2
└── images/
    ├── football-field.jpg   # 首屏滚动帧 ×4
    ├── football-30.jpg
    ├── football-60.jpg
    ├── football-90.jpg
    ├── wechat-qr.jpg        # 联系区二维码
    └── wechat-profile.jpg   # 微信按钮弹层名片图
```

没有构建步骤,没有依赖安装,没有打包产物。`index.html` 就是最终交付物,改完直接生效。

## 本地预览

用任意静态服务器起在项目根目录即可:

```bash
npx serve .
# 或
python3 -m http.server 8000
```

直接双击用 `file://` 打开也能看,但字体与部分资源的加载行为跟线上会有出入,调试建议还是走本地服务器。

## 页面结构

单页六段式,导航锚点跳转:

| 段落 | 内容 |
| --- | --- |
| Hero | 首屏足球滚动帧动画 |
| 01 — About | 个人介绍与数据(在华年数 / 身高 / 体重 / 粉丝量) |
| 02 — Portfolio | 作品集,支持 ALL / FITNESS / FASHION / AUTO 分类筛选 |
| 03 — Services | 模特拍摄、品牌与汽车代言、外贸商务接待 |
| 04 — On camera | 电动车测评视频,横向滚动卡片,跳转 YouTube |
| 05 — Next | 在华国际社群点评平台(筹备中,收集 waitlist) |
| 06 — Contact | 邮件与微信联系方式 |

## 首屏滚动动画

Hero 区叠了 4 张足球场照片,监听 `window.scrollY`,在首屏 85% 高度内把滚动进度映射成 0→3 的帧位置,相邻两帧做透明度交叉淡入,并用 smoothstep(`frac*frac*(3-2*frac)`)缓动,滚动时看起来像镜头连续推移。

实现上用 `requestAnimationFrame` 加 `ticking` 标志节流,每帧最多算一次;首帧 `football-field.jpg` 用 `<link rel="preload">` 配 `fetchpriority="high"` 预加载,避免首屏白底闪一下。

改动这段动画时注意:4 张图的 id 是 `heroF0`–`heroF3`,顺序写死在 JS 的 `frames` 数组里,增删图片要同步改数组和 HTML。

## 联系区微信弹层

联系区三个圆形按钮里,Instagram 和 TikTok 是普通外链,微信那个是 `<button id="wechatBtn">`:点击把 `#wechatModal` 的 `display` 切成 `flex`,全屏遮罩上展示 `images/wechat-profile.jpg`;点遮罩任意位置关闭。

弹层默认 `display:none` 写在行内样式上,JS 直接改 `style.display`,没有额外的类名或状态管理。

## 设计规范

| 项 | 值 |
| --- | --- |
| 背景 | `#0e0e0e` |
| 文字 | `#f2f2ee` |
| 强调色 | `#CFFF04`(荧光黄绿,用于 hover、选中态、`::selection`) |
| 标题字体 | Anton |
| 正文字体 | Archivo |
| 中文字体 | Noto Sans SC |

滚动入场动画统一走 `[data-reveal]` 属性,元素进入视口时加 `.in` 类触发。

## 外部依赖

站点并非完全自包含,断网或以下服务不可用时会掉资源:

- **Google Fonts** — Anton / Archivo / Noto Sans SC 三套字体从 `fonts.googleapis.com` 加载。中国大陆访问可能较慢,若要提速可考虑把字体一并本地化到仓库里。
- **腾讯云 CloudBase CDN** — 作品集与测评封面等图片托管在 `636c-cloud1-...tcb.qcloud.la`,不在本仓库内。仓库里的 `images/` 只放了首屏那 4 张足球帧。

Font Awesome 已经本地托管在 `fa/`,不走 CDN。

需要注意的是,`fa/css/fontawesome-all.min.css` 里声明了 Brands、Regular、Solid 三套 `@font-face`,每套都列了 eot / woff2 / woff / ttf / svg 五种格式,但 `fa/webfonts/` 实际只放了 `fa-brands-400.woff2` 和 `fa-solid-900.woff2`。现在页面只用 `fab` 品牌图标(Instagram / TikTok / WeChat / YouTube),brands 的 woff2 在,现代浏览器会跳过它不支持的格式直接取 woff2,所以不会有 404。但如果之后用到 `far` 系列图标,得先把 `fa-regular-400.woff2` 补进 `fa/webfonts/`。

## 待补占位图

页面里还有 3 处虚线框占位符(`class="ph"`),目前显示的是提示文字而不是图片。补图时把对应的 `<div class="ph">` 换成 `<img>` 即可:

| 位置 | 待补文件 | 用途 |
| --- | --- | --- |
| `index.html:188` | `images/breather.jpg` | 训练格言通栏大图 |
| `index.html:230` | `images/service-trade.jpg` | 商务接待配图 |
| `index.html:275` | `images/ev-nio.jpg` | NIO 换电测评视频封面 |

## 部署

纯静态站,任何静态托管都能直接用,把仓库根目录当站点根目录发布即可,无需构建命令。

本地开发目录带有 `.wrangler/`(未提交),即本站原先走的是 Cloudflare Pages。用 Pages 部署时构建命令留空,输出目录填 `/`。

## 许可

未设置开源许可。站内图片、文案与个人形象素材均为 MAX 本人所有,保留所有权利。
