# arxiv-ai-trend-tracker

AI 领域论文关键词趋势监测工具，定期爬取 arxiv AI 分类下的最新论文摘要，提取高频关键词并生成可视化趋势图，用于快速掌握近期 AI 研究热点方向。

## 功能

- 爬取 arxiv Artificial Intelligence 分类下指定时间范围内的论文摘要
- 从摘要中提取有价值的关键词，生成独立的 Excel 记录表
- Excel 按时间追加更新，不覆盖历史数据，可用于长期趋势对比
- 输出 Top 20 高频关键词柱状图，直观呈现当期研究热点分布

## 使用方式

```bash
pip install -r requirements.txt
python main.py
```

运行后自动爬取最近一周的论文数据，输出关键词 Excel 文件与柱状图。

## 输出示例

- `keywords.xlsx`：关键词记录表，按次追加，包含时间、关键词、词频
- `top20_keywords.png`：当期 Top 20 高频关键词柱状图

## 技术栈

Python / requests / pandas / matplotlib

## 开发背景

2025 年中开发，用于系统性追踪 AI 领域研究趋势，将海量论文信息压缩为可快速消化的关键词分布，辅助判断技术方向变化。
