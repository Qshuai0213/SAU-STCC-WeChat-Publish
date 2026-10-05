---
name: sau-wechat-publish
description: SAU 创意IT俱乐部公众号排版 Skill。为用户生成格式化公众号文章 HTML。根据文章内容气质选择配色。触发词：排一篇推送、公众号排版、SAU排版、排一篇、公众号文章。用户提供文章内容 + 排版者姓名，AI 自动生成粘贴到微信编辑器即用的格式化 HTML。支持反复对话修改。
---

# SAU 创意IT俱乐部 公众号排版

## 触发条件

用户说以下任意一句话时触发：
- "排一篇推送" / "排一篇 XXX"
- "公众号排版"
- "SAU 排版"
- "帮我生成公众号文章"
- "排一篇" + 内容

## ⚠️ 核心铁律

### 1. 内容不可自我发挥

动手排版之前，必须确保**用户明确提供了文档内容**。
AI 不得自行撰写、补全、推测任何正文内容。
如果用户没给文档，应当追问，而不是自己编一段。

### 2. 内容不可改

用户提供的文档内容，**一个字不能改，顺序不能调**。
只能做样式编排：调整字距、颜色、背景、装饰、段落间距等视觉表现。
不得自行补充、删减、改写、总结、润色任何原文。

## 工作流

### 1. 询问必要信息（仅首次）

首次排一篇新文章时，询问：
1. **排版者姓名** — 必须向用户询问，不能从文档内容推断
2. **是否有特殊要求** — 比如要加卡片、突出某段、不要某些装饰

已知不必问：
- 底部 GIF 固定
- 审核人已写好

**反复修改时不再重复询问** — 排版人和内容一次确定后，后续迭代直接用已有的。
需要排下一篇新文章时再从头问一次。

### 2. 生成 HTML

- 根据内容和体裁自行设计外壳，没有固定模板
- **保留内容原貌**：标题层级、段落顺序、标点符号全部原封不动
- 所有样式用 inline CSS（微信编辑器兼容）
- 文章宽度 max-width: 677px，居中，背景 #f5f5f5，内容区白底

生成完正文 HTML 后，在底部追加上固定落款（见下方）。
保存到工作区：`wechat-{主题}-{日期}.html`

### 3. 交付与迭代

用户提出修改 → 调整 HTML → 重新生成直到满意

## 固定落款（生成 HTML 后自动拼接在底部）

正文设计完成后，在 `</section></section></body></html>` 之前插入以下内容：

```html
<!-- ===== 落款卡片 ===== -->
<section style="display:flex; justify-content:center; margin:25px 0 15px;">
  <section style="width:90%; background:rgba(255,255,255,0.67); border:1.8px solid #175ADF; border-radius:12px; padding:15px; box-sizing:border-box;">
    <p style="font-size:14px; color:#333; line-height:1.8; text-align:center; margin:0;">图文 | 创意IT俱乐部</p>
    <p style="font-size:14px; color:#333; line-height:1.8; text-align:center; margin:3px 0 0;">排版 | {排版者姓名}</p>
    <p style="font-size:14px; color:#333; line-height:1.8; text-align:center; margin:3px 0 0;">初审初校 | 罗振</p>
    <p style="font-size:14px; color:#333; line-height:1.8; text-align:center; margin:3px 0 0;">复审复校 | 滕一平</p>
    <p style="font-size:14px; color:#333; line-height:1.8; text-align:center; margin:3px 0 0;">终审终校 | 马宁</p>
  </section>
</section>

<!-- ===== 底部 GIF（读取 assets/bottom.gif 实时 base64 嵌入） ===== -->
<section style="text-align:center; margin-top:15px;">
  <img src="data:image/gif;base64,{底部GIF_base64}" style="width:100%; max-width:100%; display:block; margin:0 auto;" />
</section>
```

- `{排版者姓名}` → 替换为用户提供的名字
- `{底部GIF_base64}` → 读取 `assets/bottom.gif` 文件，实时计算 base64 后填入。`assets/bottom.gif` 是 skill 自带的固定 GIF 资源文件。

## 资产文件路径指引

`assets/bottom.gif` 是 skill 自带的固定 GIF 文件。
读取时注意：

- 如果知道当前 skill 目录路径，用相对路径读取
- 否则尝试以下路径（按优先级）：
  1. `~/.agents/skills/sau-wechat-publish/assets/bottom.gif`
  2. `$env:USERPROFILE/.agents/skills/sau-wechat-publish/assets/bottom.gif`

可以用 `python3 -c "import os; print(os.path.expanduser('~/.agents/skills/sau-wechat-publish/assets/bottom.gif'))"` 确认路径。

## 文档图片处理规则

如果用户提供的文档（docx/word）中有嵌入的图片：
1. 用 `python-docx` 库提取 `word/media/` 目录下的图片文件
2. 每个图片转为 base64 字符串，用 `<img src="data:image/png;base64,...">` 内嵌到 HTML 正文对应位置
3. 根据文档中图片的 `rId` 关系确定图片在正文中的位置，不要随意放置

## 设计参考

详见 `references/style-guide.md`，按文章气质选色搭配，简约大气即可。

进阶组件与避坑手法（可选）：见 `references/design-notes.md`，含微信安全手法清单、层级编号体系、荧光笔/拍立得等已验证组件，以及排版任务的校验流程。
