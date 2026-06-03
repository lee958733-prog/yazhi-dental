# 雅致口腔官网审计报告

**审计时间**：2026-06-03  
**审计模型**：deepseek/deepseek-v4-pro  
**审计文件**：`data/yazhi-website/index.html`  

---

## 1. 安全性

**评级：PASS（85/100）**

### ✅ 通过项
| 检查项 | 结果 | 说明 |
|--------|------|------|
| XSS 风险 | ✅ 无风险 | 纯静态 HTML，无 `eval()`/`innerHTML`/URL 参数拼接，无用户输入处理 |
| 外部资源依赖 | ✅ 零外部依赖 | 100% 内联 CSS + 系统字体栈（`-apple-system, BlinkMacSystemFont, PingFang SC...`），无 CDN/外部 JS/外部字体 |
| 离线可打开 | ✅ 完全支持 | 无任何网络请求即可完整渲染，适合离线场景 |
| Cookie/LocalStorage | ✅ 未使用 | 无客户端存储操作 |
| iframe | ✅ 无 | 无嵌入第三方内容 |

### ⚠️ 优化建议
| 问题 | 严重度 | 建议 |
|------|--------|------|
| JSON-LD 中暴露邮箱 `yz3737888@163.com` | 低 | 该邮箱为公开商务联系方式，可接受。若未来上线可抓取域名，建议用 `contactPoint` 替代裸 email 字段 |
| 缺少 CSP (Content-Security-Policy) | 低 | 上线后建议通过 HTTP Header 添加 `Content-Security-Policy: default-src 'self'` |
| 缺少 HTTPS 升级提示 | 低 | 建议添加 `<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">` |

---

## 2. 合规性

**评级：PASS（88/100）**

### ✅ 通过项
| 检查项 | 结果 | 说明 |
|--------|------|------|
| 极限词 | ✅ 无违规 | 全文扫描：未发现「最」「第一」「唯一」「最好」「根治」「包治」「终身保修」「100%」「零风险」等广告法禁词 |
| 疗效承诺 | ✅ 无 | 未出现「保证治愈」「终身保修」「永久不复发」等 |
| 免责声明 | ✅ 完整 | Footer 含规范免责声明：「本网站内容仅供科普参考，不构成医疗建议。部分数据来源于门诊内部统计，未经第三方审计。口腔诊疗请到正规医疗机构面诊。」 |
| 医生职称 | ✅ 一致 | 宋杰·副主任医师、吕玉华·主治医师、何文蓉·主治医师，均与 KG 一致 |
| 竞品对比 | ✅ 无 | 未出现其他口腔机构名称或对比贬低性表述 |
| 联系方式合规 | ✅ 合规 | 未出现手机号/微信号引导，仅显示门诊座机 `(0712)3737888` 和 `(0712)3737018`（公开门诊电话，合规） |
| 品牌名 | ✅ 正确 | 全文使用「雅致口腔」，未出现孤立「应城口腔」 |

### ⚠️ 关注项
| 问题 | 严重度 | 建议 |
|------|--------|------|
| JSON-LD 含定价信息 | 低 | `makesOffer` 中列出「国产3980起/韩系进口4980起」等价格——参考广告法第8条及《医疗广告管理办法》，医疗机构官网公示服务价格一般不违规，但建议添加 `priceValidUntil` 日期并标注「以上价格为参考价，以面诊方案为准」 |
| 统计数据来源 | 低 | 「37,625+服务人次」已标注「数据来源于门诊内部统计」，合规 ✅ |
| 备案号 | ⚠️ 待完成 | Footer 显示「鄂ICP备XXXXXXXX号-1（待备案）」——上线前必须替换为真实 ICP 备案号，否则面临关站风险 |

---

## 3. 技术质量

**评级：PASS（78/100）**

### ✅ 通过项
| 检查项 | 结果 | 说明 |
|--------|------|------|
| HTML 语法 | ✅ 规范 | DOCTYPE 正确、`html lang="zh-CN"`、标签全部闭合、属性格式正确 |
| 移动端适配 | ✅ 良好 | 三档响应式断点：`≤768px`(单列)、`769-1024px`(2-3列)、`≥1025px`(4列)。导航栏、Hero、卡片网格均适配 |
| CSS 质量 | ✅ 规范 | 使用 `box-sizing: border-box`，CSS Variables 策略干净，`transition/animation` 硬件加速友好 |
| 语义化 HTML | ✅ 基本合格 | 使用了 `<nav>`、`<footer>`、`<section>` 标签 |
| 页面加载速度 | ✅ 极快 | 零外部请求，首屏渲染仅需解析内联 HTML+CSS，预估 <50ms |
| Scroll 行为 | ✅ | `scroll-behavior: smooth` 已设置 |

### ❌/⚠️ 问题项
| 问题 | 严重度 | 建议 |
|------|--------|------|
| JSON-LD `medicalSpecialty` 语义错误 | ⚠️ 中 | `"Orthopedic"` 在 Schema.org 中 = **骨科**（骨/关节手术），非口腔正畸。应改为 `"Dentistry"` 或移除（`@type: Dentist` 已表明类别）。同时 `OralSurgery` 和 `PediatricDental` 也不在 Schema.org MedicalSpecialty 枚举中，建议改为 `"Dentistry"` 统一处理 |
| JSON-LD 缺少 `url` 属性 | ⚠️ 中 | `Dentist` 类型业务实体建议包含 `"url": "https://www.yazhikq.com"` |
| JSON-LD 缺少 `image` 属性 | 低 | 建议添加品牌 Logo 的 `image` 属性（可先用占位） |
| `honorificSuffix` 用法 | 低 | `"副主任医师"` 更适合用 `jobTitle` 而非 `honorificSuffix`（后者更适合 M.D./Ph.D. 等后缀）——不影响解析但语义不够精确 |
| `<main>` 缺失 | 低 | 无障碍访问要求页面有 `<main>` 地标，建议将 `<nav>` 和 `<footer>` 之间的内容区用 `<main>` 包裹 |
| 缺少 skip-to-content 链接 | 低 | 键盘用户友好：添加 `<a href="#main-content" class="skip-link">跳到内容</a>` |
| 缺少 ARIA 标签 | 低 | 导航、卡片等交互元素缺少 `aria-label` / `role` 属性 |
| 无图片资源 | 低 | 完全无 `<img>` 标签——品牌 Logo、医生照片、设备图可提升转化率，同时补充 `alt` 文本利于 SEO |
| 无打印样式 | 低 | 建议添加 `@media print` 隐藏导航、调整字体大小 |
| 无暗色模式 | 低 | 可选优化：`@media (prefers-color-scheme: dark)` |

---

## 4. 内容准确度

**评级：PASS（90/100）**

### ✅ 通过项
| 检查项 | KG 参考值 | 网站实际值 | 一致性 |
|--------|-----------|-----------|:---:|
| 品牌名 | 雅致口腔 | 雅致口腔（全文一致） | ✅ |
| 品牌创立 | 2004 年 | 2004年创立（Hero + 品牌简介 + Stats） | ✅ |
| 品牌 slogan | 雅致生活·齿生相伴 | 雅致生活·齿生相伴（Hero H1 及品牌简介） | ✅ |
| 中百院区地址 | 汉宜大道蒲城名都21-22号 | 应城市汉宜大道蒲城名都21-22号 | ✅ |
| 大智路院区地址 | 古城大道与大智路交汇处 | 应城市古城大道与大智路交汇处 | ✅ |
| 中百电话 | (0712)3737888 | (0712) 3737888 | ✅ |
| 大智电话 | (0712)3737018 | (0712) 3737018 | ✅ |
| 宋杰职称 | 副主任医师 | 副主任医师 · 院长 | ✅ |
| 宋杰背景 | 武大医学院/20余年/微创种植 | 武汉大学医学院毕业/20年+/微创数字化种植 | ✅ |
| 吕玉华职称 | 主治医师 | 主治医师 | ✅ |
| 吕玉华经验 | 30余年/修复专委会委员 | 30年+/湖北省口腔医学会修复专委会委员 | ✅ |
| 何文蓉职称 | 主治医师 | 主治医师 | ✅ |
| 何文蓉经验 | 十几年/正畸专委会委员/武大进修 | 十几年/湖北省口腔医学会正畸专委会委员/武大口腔医院进修 | ✅ |
| 医保资质 | Q1: 医保定点 + Q2: 异地就医定点 | 应城市医保定点医疗机构 · 湖北省异地就医定点 | ✅ |
| 营业时间 | Mo-Su 08:00-18:00 | 周一至周日 08:00—18:00（节假日正常营业） | ✅ |
| 门诊面积 | ~1000㎡ | 约1000㎡ | ✅ |
| 牙椅数量 | 10余台 | 10+台 | ✅ |
| 设备 | KaVo CBCT + 数字化口扫 | 卡瓦（KaVo）CBCT + 数字化口扫系统 | ✅ |
| 服务人数 | 37625人次 | 37,625+ | ✅ |
| 傲齿口腔 | parentOrganization | 傲齿口腔全国连锁品牌的湖北成员 | ✅ |

### ⚠️ 不一致/缺失项
| 问题 | 严重度 | 详情 |
|------|--------|------|
| 新增服务未入 KG | 低 | 网站展示 8 个服务（种植牙、矫正、补牙、洗牙、儿童齿科、**美学修复**、**瓷贴面**、镶牙），KG 仅定义 6 个服务实体——缺少「美学修复」「瓷贴面」。**建议同步更新 KG** |
| 宋杰缺少「创始人」表述 | 低 | KG 定义 `role: 院长·创始人`，网站医生卡只写「副主任医师 · 院长」，Hero 和品牌简介也未提「创始人」身份。建议补充 |
| JSON-LD 服务数量与页面展示不一致 | 低 | JSON-LD 含 6 个 `makesOffer`（无美学修复、瓷贴面），页面服务网格含 8 个 |
| 补牙医保表述 | 低 | KG 标注 S3 补牙「医保可刷」，网站服务区未单独标注补牙可刷医保——仅在 Hero 处笼统写「医保定点」 |

---

## 5. SEO / AI 友好度

**评级：PASS（80/100）**

### ✅ 通过项
| 检查项 | 结果 |
|--------|------|
| JSON-LD 嵌入 | ✅ 已嵌入 `<script type="application/ld+json">`，包含品牌、医生、院区、服务4大核心实体 |
| meta description | ✅ 已设置，含地域词（应城）+ 服务词（医保定点）+ 地址，约 80 字，良好 |
| 页面 title | ✅ `雅致口腔 - 应城20年口腔品牌 \| 医保定点 · 微创种植 · 专业矫正`，含地域+服务核心关键词 |
| H1/H2 层级 | ✅ 1个H1 + 6个H2（品牌/院区/医生/服务/联系 + Footer H2），语义层级合理 |
| 结构化数据覆盖 | ✅ 品牌主体(Dentist) + 院区地址(PostalAddress) + 医生(Physician×3) + 服务(MedicalProcedure×6) |
| 语义化链接 | ✅ 导航全部使用 `<a href="#anchor">` 锚点，语义清晰 |

### ❌/⚠️ 问题项
| 问题 | 严重度 | 详情与建议 |
|------|--------|-----------|
| H1 为 slogan | ⚠️ 中 | H1「雅致生活·齿生相伴」是品牌口号，非关键词优化标题。AI 爬虫优先提取 H1 作为页面对应主题。**建议**：H1 改为含地域+服务核心词，如「雅致口腔 — 应城种植牙·牙齿矫正·补牙专业门诊」，将 slogan 降为副标题 |
| 缺少 canonical URL | ⚠️ 中 | 页面无 `<link rel="canonical" href="https://www.yazhikq.com/">`，搜索引擎可能将不同 URL 变体视为重复内容 |
| 缺少 Open Graph 标签 | ⚠️ 中 | 无 `og:title` / `og:description` / `og:image` / `og:type` / `og:url`，分享到微信/微博/QQ 时会显示空白卡片或随机抓取文字 |
| 缺少 Twitter Card | 低 | 补充 `twitter:card` / `twitter:title` 等（与 OG 复用同一套内容） |
| JSON-LD 无 `url`/`image` | ⚠️ 中 | 见技术审计 — 影响结构化数据完整性评分 |
| `medicalSpecialty: "Orthopedic"` | ⚠️ 中 | 见技术审计 — AI 可能错误理解诊所专长为「骨科」 |
| 缺少 robots 指引 | 低 | 建议添加 `<meta name="robots" content="index, follow">` 确保搜索引擎正常收录 |
| 缺少 favicon | 低 | 浏览器标签页、书签栏显示空白图标，影响品牌识别 |
| 缺少 Sitemap 引用 | 低 | 建议添加 `<link rel="sitemap" type="application/xml" href="/sitemap.xml">` |
| 无 Schema.org `hasMap` / `geo` | 低 | 院区地址可嵌入 `GeoCoordinates` 经纬度，提升本地搜索排名 |

---

## 综合评分与结论

| 维度 | 评分 | 等级 |
|------|:---:|:---:|
| 1. 安全性 | 85/100 | PASS |
| 2. 合规性 | 88/100 | PASS |
| 3. 技术质量 | 78/100 | PASS |
| 4. 内容准确度 | 90/100 | PASS |
| 5. SEO/AI 友好度 | 80/100 | PASS |
| **综合总分** | **84/100** | **可发布 🟡** |

### 结论：🟡 可发布 — 有优化建议

网站整体质量良好：零外部依赖、内容与 KG 高度一致、合规性达标、JSON-LD 结构化数据覆盖全面。作为诊所官网 MVP 版本，**可以直接发布**。

但建议在正式上线前完成以下 **5 项推荐修改** 以提升 SEO 效果和 AI 可发现性。

---

## 建议修改清单

### 🔴 P1 — 上线前必改
| # | 修改项 | 说明 |
|---|--------|------|
| 1 | 替换真实 ICP 备案号 | Footer「鄂ICP备XXXXXXXX号-1（待备案）」→ 上线后必须是真实备案号，否则违法 |
| 2 | 修复 JSON-LD `medicalSpecialty` | `"Orthopedic"` → `"Dentistry"`，避免 AI 误解为骨科诊所 |

### 🟡 P2 — 推荐修改
| # | 修改项 | 说明 |
|---|--------|------|
| 3 | H1 改为关键词标题 | 当前 slogan「雅致生活·齿生相伴」→ 建议「雅致口腔 — 应城种植牙·牙齿矫正·补牙专业门诊」 |
| 4 | 添加 Open Graph 标签 | 补充 `og:title/description/image/type/url`，确保微信/QQ/微博分享卡片正常展示 |
| 5 | 添加 canonical URL | `<link rel="canonical" href="https://www.yazhikq.com/">`，防止搜索引擎重复索引 |

### 🟢 P3 — 优化建议
| # | 修改项 | 说明 |
|---|--------|------|
| 6 | KG 同步新增服务 | 将「美学修复」「瓷贴面」加入 KG，保持 KG 与网站一致 |
| 7 | JSON-LD 补充 `url` / `image` 属性 | 提升结构化数据完整性评分 |
| 8 | 添加 `<main>` 标签 + skip-to-content | 提升无障碍访问 | 
| 9 | 添加 favicon | 浏览器标签页品牌识别 |
| 10 | 宋杰补充「创始人」身份 | 在医生卡或 Hero 区体现创始人身份 |
| 11 | 添加 `robots` meta | `<meta name="robots" content="index, follow">` |
| 12 | JSON-LD 院区添加 `geo` 坐标 | 提升本地搜索（Google Maps / 百度地图）排名 |
| 13 | 地址可在 JSON-LD 中加 `name` 字段 | 当前已有 `"name": "中百院区"` 在 `PostalAddress` 中 ✅ |
| 14 | 补充打印样式 `@media print` | 可选优化 |

---

## 附录：JSON-LD 修正版参考

```json
{
  "@context": "https://schema.org",
  "@type": "Dentist",
  "name": "雅致口腔",
  "url": "https://www.yazhikq.com",
  "alternateName": ["应城雅致口腔", "YAZHIDENTAL"],
  "foundingDate": "2004",
  "description": "湖北本土口腔连锁品牌，傲齿口腔全国连锁成员，20年以上品牌历史。雅致生活·齿生相伴。",
  "slogan": "雅致生活·齿生相伴",
  "telephone": "(0712)3737888",
  "openingHoursSpecification": {
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": ["Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"],
    "opens": "08:00",
    "closes": "18:00"
  },
  "address": [
    { "@type": "PostalAddress", "name": "中百院区", "streetAddress": "汉宜大道蒲城名都21-22号", "addressLocality": "应城市", "addressRegion": "湖北省", "addressCountry": "CN" },
    { "@type": "PostalAddress", "name": "大智路院区", "streetAddress": "古城大道与大智路交汇处", "addressLocality": "应城市", "addressRegion": "湖北省", "addressCountry": "CN" }
  ],
  "medicalSpecialty": "Dentistry",
  "parentOrganization": { "@type": "Organization", "name": "傲齿口腔全国连锁" },
  "isAcceptingNewPatients": true,
  "areaServed": { "@type": "City", "name": "应城市", "containedInPlace": { "@type": "AdministrativeArea", "name": "湖北省" } },
  "employee": [
    { "@type": "Physician", "name": "宋杰", "jobTitle": "院长·创始人（副主任医师）", "medicalSpecialty": "Dentistry", "description": "20余年口腔临床经验，武汉大学医学院毕业，微创数字化种植专家" },
    { "@type": "Physician", "name": "吕玉华", "jobTitle": "主治医师", "medicalSpecialty": "Dentistry", "description": "30余年口腔医学临床经验，湖北省口腔医学会修复专委会委员，北上广多家权威医院进修" },
    { "@type": "Physician", "name": "何文蓉", "jobTitle": "主治医师", "medicalSpecialty": "Dentistry", "description": "武大口腔医院进修，湖北省口腔医学会正畸专委会委员，儿童早期矫正、青少年正畸" }
  ],
  "makesOffer": [
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "种植牙", "description": "国产3980起/韩系进口4980起，宋杰副主任医师主诊" } },
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "牙齿矫正", "description": "金属/半隐形/隐形矫正，吕玉华+何文蓉双主治医师" } },
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "补牙", "description": "树脂充填180-380元，医保可刷" } },
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "洗牙", "description": "超声波洁牙80-120元，双院区周末正常营业" } },
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "儿童齿科", "description": "何文蓉主治医师主诊，独立儿童特诊室" } },
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "镶牙", "description": "烤瓷牙/全瓷牙/活动假牙/固定桥等义齿修复" } },
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "美学修复", "description": "全瓷贴面、树脂美学修复" } },
    { "@type": "Offer", "itemOffered": { "@type": "MedicalProcedure", "name": "瓷贴面", "description": "超薄瓷贴面美学修复" } }
  ]
}
```

---

*审计工具链：手动审查 + KG 交叉验证 + Schema.org 规范参照*  
*下次审计触发条件：网站内容更新后 / KG 实体变更后 / ICP 备案完成后*
