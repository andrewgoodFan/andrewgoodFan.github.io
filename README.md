<!--
 * @Author: Len Van
 * @Date: 2024-04-07 17:47:08
 * @LastEditTime: 2024-04-10 16:10:03
 * @Description: hexo 博客
-->
# 重构hexo博客

## 安装hexo

`npm install hexo`

## 初始化

`npx hexo init blog`

## 使用主题

1. 下载主题插件
2. 新建主题配置文件 `_config.theme.yml`
3. 修改主题配置文件

## 阅读全文配置

1. 下载插件`hexo-excerpt`
2. `_config.yml`配置
```yml
excerpt:
  depth: 5
  excerpt_excludes: []
  more_excludes: []
  hideWholePostExcerpt: true
```
3. 主题配置文件配置
```yml
excerpt_description: true
read_more_btn: true
```

## 部署到github-pages`

1. 安装插件`hexo-deployer-git`
2. `_config.yml`配置
  ```yml
    deploy:
      type: git
      repo: 你的git地址
      branch: 你的分支
  ```
``
