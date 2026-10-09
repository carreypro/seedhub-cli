# SeedHub CLI 🎬

搜索 [SeedHub](https://www.seedhub.cc/) 影视资源，自动提取夸克网盘等下载链接。

> SeedHub 是一个中文影视资源聚合站，收录来自各大网盘和磁力链接的电影、电视剧、动漫资源。

## 功能

- 🔍 按关键词搜索电影、电视剧、动漫
- 🔗 自动提取详情页下载链接
- 🚀 夸克网盘链接自动解析为可直接访问的 URL
- 📦 同时支持：百度网盘、阿里云盘、UC网盘、迅雷、磁力链接、ED2K
- 🛡️ 自动绕过 Cloudflare 防护
- 🎯 简洁的命令行输出

## 安装

```bash
git clone https://github.com/CaliCastle/seedhub-cli.git
cd seedhub-cli
pip install -r requirements.txt
```

### 依赖

- Python 3.8+
- [cloudscraper](https://github.com/VeNoMouS/cloudscraper)（处理 Cloudflare JS Challenge）

## 使用方法

### 搜索影视

```bash
python seedhub.py search "怪奇物语"
```

输出：

```
🔍 搜索: 怪奇物语

找到 5 个结果:

1. 怪奇物语 第五季 Stranger Things Season 5
   📅 2025 / 剧集 / 美国 / 英语 / 薇诺娜·瑞德 大卫·哈伯 | ⭐ 豆瓣 9.6
   📌 ID: 119254

2. 怪奇物语 第一季 Stranger Things Season 1
   📅 2016 / 剧集 / 美国 / 英语 / 薇诺娜·瑞德 大卫·哈伯 | ⭐ 豆瓣 8.9
   📌 ID: 1754
...
```

### 获取下载链接

使用搜索结果中的 **ID**：

```bash
python seedhub.py links 119254
```

输出：

```
📽️ 怪奇物语 第五季 Stranger Things Season 5
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔗 夸克网盘 (135个, 已解析10个):
   • 【怪奇物语 全收集】【4K 1080P】
     https://pan.quark.cn/s/f830c8bb0787
   • 【全季4K优化版】已更新完结【内嵌简中】【附1-4季】
     https://pan.quark.cn/s/433dc491200f
   ...

📦 百度网盘 (90个):
   • ...

📦 UC网盘 (9个):
   • ...

💡 夸克链接已自动解析为可直接访问的 URL
```

### 参数

```bash
# 限制搜索结果数量
python seedhub.py search "至尊马蒂" --limit 5

# 解析更多夸克链接（默认 10 个，数量越多越慢）
python seedhub.py links 129054 --limit 20
```

## 支持的链接类型

| 类型 | 自动解析 | 说明 |
|------|:---:|------|
| 夸克网盘 | ✅ | 直接输出 `pan.quark.cn` 链接 |
| 百度网盘 | ❌ | 显示资源描述 |
| 阿里云盘 | ❌ | 显示资源描述 |
| UC网盘 | ❌ | 显示资源描述 |
| 磁力链接 | — | 直接输出 magnet: URI |
| 迅雷链接 | — | 直接输出 thunder:// URI |
| ED2K | — | 直接输出 ed2k:// URI |

> 夸克链接会自动跟进 SeedHub 的跳转页，解析出实际的 `pan.quark.cn` 地址。其他网盘类型显示资源描述，可访问 SeedHub 页面手动获取。

## 作为 Python 库使用

```python
from seedhub import search, get_links

# 搜索
results = search("怪奇物语")
for r in results:
    print(f"{r['title']} (ID: {r['id']}) ⭐ {r['rating']}")

# 获取链接
links = get_links("119254")
for item in links.get("quark_resolved", []):
    print(f"🔗 {item['url']}")
```

## 工作原理

1. 使用 [cloudscraper](https://github.com/VeNoMouS/cloudscraper) 绕过 Cloudflare JS Challenge
2. **搜索**：解析 `seedhub.cc/s/{关键词}/` 页面的影片卡片
3. **链接提取**：访问 `seedhub.cc/movies/{id}/`，通过 `data-link` 属性分类提取各网盘链接
4. **夸克解析**：跟进 `/link_start/?redirect_to=pan_id_XXX` 跳转，从目标页提取实际夸克链接

## 注意事项

- **搜索建议**：使用中文片名效果最好，纯英文可能搜不到
- **Cloudflare**：首次请求可能需要 5-10 秒完成 JS Challenge
- **请求频率**：请合理使用，不要频繁大量请求
- **夸克解析**：每个夸克链接需要一次额外的 HTTP 请求，`--limit` 控制解析数量

## 免责声明

本工具仅供个人学习和研究使用。不托管、不分发、不提供任何版权内容，仅聚合 SeedHub 上公开可访问的链接。请遵守所在地区的法律法规。

## 给 AI Agent 使用

如果你想让 AI Agent（如 OpenClaw、Claude Code 等）使用这个工具，请参考 [SKILL.md](./SKILL.md)，里面包含了 Agent 所需的安装方式、命令格式和使用流程。

## 开源协议

MIT
