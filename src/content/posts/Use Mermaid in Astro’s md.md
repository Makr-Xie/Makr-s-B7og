---
  title: Use Mermaid in Astro’s md
  pubDate: 2024-07-12
  categories: ["笔记"]
  description: ""
---

今天下午随手用Mermaid画了个图，想放到Blog上，却发现astro里的markdown不支持mermaid

花了好些时间，走了些弯路，总归是在本地解决了。可惜不知道为何Vercel部署的时候Playright出了些问题不能部署，我在local跑都没问题但放到Vercel上就出毛病了，很烦。

生气了不做了，等以后哪天心情好了且想起这事儿了再看吧...

不过还是记录一下方法：

1. 更新下astro

```tsx
# Upgrade astro
npx @astrojs/upgrade
```

2. install两个包

```tsx
npm install rehype-mermaid
npm install rehypeShiki
```

3. 之后去往astro.config.js
  
4. 首先import
    
```tsx
import { defineConfig } from 'astro/config'
...
import { rehypeShiki } from '@astrojs/markdown-remark'
import rehypeMermaid from 'rehype-mermaid'
...
```
    
5. 之后再defineConfig里的markdown项加一个rehypePlugins
    
```tsx
// https://astro.build/config
export default defineConfig({
  markdown: {
    rehypePlugins: [
      rehypeMermaid,
      rehypeShiki,
    ],
    syntaxHighlight: false,
  },
})
```