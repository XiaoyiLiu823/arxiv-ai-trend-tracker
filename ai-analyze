#成功了，爬取arxiv的最新论文，然后整理最近最火热的论文关键词

import requests
from bs4 import BeautifulSoup
import time
import re
from collections import Counter
import matplotlib.pyplot as plt
import json
import pandas as pd
import datetime


def crawl_papers(n=10):
    """爬取最近 n 篇 cs.AI 论文的标题和摘要，并保存到本地"""
    papers = []
    abs_links = []

    url='https://arxiv.org/list/cs.AI/recent'

    my_headers={'User-Agent':("Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
            "AppleWebKit/537.36 (KHTML, like Gecko) "
            "Chrome/120.0.0.0 Safari/537.36"), #用户代理是固定格式
            'Accept-Language':'en-US,en;q=0.9',
            'Accept':"text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8"} #允许也是固定格式

    response=requests.get(url,headers=my_headers,timeout=20)

    soup=BeautifulSoup(response.text,"html.parser")

    for dt in soup.find_all("dt")[:n]:  # 只取前10篇
        a_tag = dt.find("a", title="Abstract")
        if a_tag:
            abs_url = "https://arxiv.org" + a_tag["href"]
            abs_links.append(abs_url)

    print(f"前{n}个摘要页链接:")
    for link in abs_links:
        print(link)
    
    time.sleep(15)

    for link in abs_links:
        resp = requests.get(link, headers=my_headers, timeout=20)
        soup = BeautifulSoup(resp.text, "html.parser")

        # 提取标题
        title = soup.find("h1", class_="title").text.replace("Title:", "").strip()

        # 提取摘要
        abstract = soup.find("blockquote", class_="abstract").text.replace("Abstract:", "").strip()

        papers.append({"title": title, "abstract": abstract})

        print("抓到一篇:", title)
        
        time.sleep(15)  # 符合 arXiv 爬虫规定，每次请求间隔15秒

    print("\n总共抓取:", len(papers), "篇")

    # 保存到本地 JSON
    with open("papers.json", "w", encoding="utf-8") as f:
        json.dump(papers, f, ensure_ascii=False, indent=2)

    return papers

def analyze_keywords(filepath="papers.json", topn=20):
    """读取本地文件，统计关键词"""

    title=f"Top {topn} Keywords (arXiv cs.AI recent)"

    with open(filepath, "r", encoding="utf-8") as f:
        papers = json.load(f)

    # 英文停用词（可以扩展）
    stopwords = set([
        "the","and","of","in","to","for","with","on","a","an","is","are","by","this",
        "that","we","be","as","from","at","using","our","can","which","have","has"
    ])


    # 统计词频
    filtered_words_per_paper = []
    for p in papers:
        text = (p["title"] + " " + p["abstract"]).lower()
        text = re.sub(r"[^a-z\s]", " ", text)
        words = set(text.split())  # 用 set 去重
        filtered = [w for w in words if w not in stopwords and len(w) > 2]
        filtered_words_per_paper.extend(filtered)

    word_counts = Counter(filtered_words_per_paper)
    top_words = word_counts.most_common(topn)

    print("Top 20 高频词：")
    for word, freq in top_words:
        print(word, freq)

    # 可视化
    words, counts = zip(*top_words)
    plt.figure(figsize=(12,6))
    bars=plt.bar(words, counts)
        # 给每个柱子加数字标签
    for bar in bars:
        height = bar.get_height()
        plt.text(bar.get_x() + bar.get_width()/2,   # x 坐标：柱子中间
                 height + 0.3,                      # y 坐标：柱子顶端稍微往上
                 str(height),                       # 显示的内容
                 ha='center', va='bottom')          # 水平居中，垂直对齐
    
    plt.xticks(rotation=45)
    plt.title(title)
    plt.show()

    return top_words

def save_keywords_to_excel(top_words, excel_file="keywords.xlsx"):
    """把已有的 top_words 保存到 Excel，支持每周更新"""
    today = datetime.date.today().isoformat()
    df_new = pd.DataFrame(top_words, columns=["keyword", "count"])
    df_new.insert(0, "date", today)

    try:
        df_existing = pd.read_excel(excel_file)
        df_all = pd.concat([df_existing, df_new], ignore_index=True)
    except FileNotFoundError:
        df_all = df_new

    df_all.to_excel(excel_file, index=False)
    print(f"已保存 {len(top_words)} 个关键词到 {excel_file}，日期 {today}")

crawl_papers(50)
# 先分析关键词，得到 top_words
top_words = analyze_keywords()

# 再保存到 Excel
save_keywords_to_excel(top_words, "keywords.xlsx")
