---
title: "帝国 CMS v8 原生 Sitemap.xml 生成方案（无需插件）"
date: 2026-01-03T20:35:00+08:00
slug: "empirecms-v8-native-sitemap-generator"
description: "提供一套帝国 CMS v8 原生 Sitemap.xml 生成方案，通过 PHP 脚本生成 sitemap index 与子 sitemap，无需依赖任何插件，稳定可控。"
summary: "提供一套帝国 CMS v8 原生 Sitemap.xml 生成方案，通过 PHP 脚本生成 sitemap index 与子 sitemap，无需依赖任何插件，稳定可控。"
categories:
  - 工作
tags:
  - 帝国CMS
  - Sitemap
  - SEO
  - PHP
  - 搜索引擎
aliases:
  - /about-work/366.html
---

新建文件/e/sitemap.php

````php
<?php
/**
 * EmpireCMS v8.x 通用 Sitemap Index 生成器（最终稳定版）
 *
 * 生成文件：
 *  - /sitemap.xml               (index)
 *  - /sitemap-xxx.xml           (子 sitemap)
 *
 * 使用方法：
 * 1. 文件放在：/e/sitemap.php
 * 2. 修改【Sitemap 配置区】
 * 3. 浏览器访问一次即可生成
 */


define('EmpireCMSAdmin', true);
require_once(__DIR__ . '/class/connect.php');
require_once(__DIR__ . '/class/db_sql.php');
require_once(__DIR__ . '/class/functions.php');

$link   = db_connect();
$empire = new mysqlquery();
/* ================= 基础配置 ================= */

// 站点根目录
$root = $_SERVER['DOCUMENT_ROOT'];


// $siteurl = rtrim($public_r['newsurl'], '/') . '/';
// 站点绝对 URL（不依赖 EmpireCMS 配置，最稳）
$siteurl = (isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] === 'on' ? 'https' : 'http') . '://' . $_SERVER['HTTP_HOST']. '/';

// URL 最大条数（单个 sitemap）
$max_items = 1000;

/* ================= Sitemap 配置区（只改这里） ================= */
/*
 * 说明：
 * - key：子 sitemap 名称（sitemap-key.xml）
 * - table：数据表（不含前缀）
 * - where：SQL 条件
 * - tables：多模型聚合（可选）
 */

$sitemaps = [

    // 内容页（新闻 / 问答 等）
    'news' => [
        'file'       => $root . '/sitemap-news.xml',
        'table'      => 'ecms_news',
        'where'      => 'classid IN (2,5,6)',   // ← 改成你的栏目 ID
        'changefreq' => 'weekly',
        'priority'   => '0.7'
    ],

    
];

/* ================= sitemap index ================= */

$index_xml  = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
$index_xml .= "<sitemapindex xmlns=\"http://www.sitemaps.org/schemas/sitemap/0.9\">\n";

/* ================= 生成子 sitemap ================= */
foreach ($sitemaps as $key => $cfg) {

    $xml  = '<?xml version="1.0" encoding="UTF-8"?>' . "\n";
    $xml .= '<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">' . "\n";

    /* ---------- 单表 sitemap ---------- */
    if (isset($cfg['table'])) {

        $sql = $empire->query("
            SELECT titleurl, newstime
            FROM {$dbtbpre}{$cfg['table']}
            WHERE {$cfg['where']}
            ORDER BY newstime DESC
            LIMIT {$max_items}
        ");

        while ($r = $empire->fetch($sql)) {
            append_url(
                $xml,
                $r['titleurl'],
                $r['newstime'],
                $siteurl,
                $cfg['changefreq'],
                $cfg['priority']
            );
        }
    }

    /* ---------- 多模型聚合 sitemap（2026） ---------- */
    if (isset($cfg['tables'])) {

        foreach ($cfg['tables'] as $t) {

            $sql = $empire->query("
                SELECT titleurl, newstime
                FROM {$dbtbpre}{$t['table']}
                WHERE {$t['where']}
                ORDER BY newstime DESC
                LIMIT {$max_items}
            ");

            while ($r = $empire->fetch($sql)) {
                append_url(
                    $xml,
                    $r['titleurl'],
                    $r['newstime'],
                    $siteurl,
                    $cfg['changefreq'],
                    $cfg['priority']
                );
            }
        }
    }

    $xml .= "</urlset>";
    file_put_contents($cfg['file'], $xml);

    // 写入 index
    $index_xml .= "  <sitemap>\n";
    $index_xml .= "    <loc>{$siteurl}sitemap-{$key}.xml</loc>\n";
    $index_xml .= "    <lastmod>" . date('Y-m-d') . "</lastmod>\n";
    $index_xml .= "  </sitemap>\n";
}

$index_xml .= "</sitemapindex>";
file_put_contents($root . '/sitemap.xml', $index_xml);

/* ================= 清理 ================= */

db_close();
$empire = null;

/* ================= 浏览器提示 ================= */

header('Content-Type: text/plain; charset=utf-8');
echo "Sitemap Index 生成完成：\n";
echo $siteurl . "sitemap.xml\n";

/* ================= 工具函数 ================= */

function append_url(&$xml, $rawUrl, $newstime, $siteurl, $changefreq, $priority)
{
    if (empty($rawUrl)) return;

    $url = trim($rawUrl);
    if (strpos($url, 'http') !== 0) {
        $url = $siteurl . ltrim($url, '/');
    }

    $lastmod = date('Y-m-d', intval($newstime));

    $xml .= "  <url>\n";
    $xml .= "    <loc>" . htmlspecialchars($url, ENT_QUOTES) . "</loc>\n";
    $xml .= "    <lastmod>{$lastmod}</lastmod>\n";
    $xml .= "    <changefreq>{$changefreq}</changefreq>\n";
    $xml .= "    <priority>{$priority}</priority>\n";
    $xml .= "  </url>\n";
}
````

这里一定要改成你自己对应的栏目ID

> 'where'      => 'classid IN (2,5,6)',

每次审核文章，发布新文章后需要访问/e/sitemap.php

你的 EmpireCMS Sitemap 体系已经是：

- 原生脚本
- 无插件
- sitemap index + 子 sitemap

这套方案稳定、可控、可长期维护，非常适合内容站、资讯站和老帝国 CMS 站点使用。

后续如果文章量上万，可以再按栏目或时间拆分 sitemap，
但对大多数站点来说，这一版已经足够。

