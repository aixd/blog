---
slug: docusaurus-dd
title: First Step to Docusaurus
tags: [docusaurus]
---
今天从 Hugo 转向 Docusaurus, 聊聊 Docusaurus 的配置方案.

<!-- truncate -->

## 顶栏
顶栏的配置在 `docusaurus.config.ts` 文件中, 
```ts
themeConfig: {
    // Replace with your project's social card
    image: 'img/docusaurus-social-card.jpg',
    navbar: {
        title: 'My Site',
        logo: {
        alt: 'My Site Logo',
        src: 'img/logo.svg',
        },
        items: [
        {
            type: 'docSidebar',
            sidebarId: 'tutorialSidebar',
            position: 'left',
            label: 'Tutorial',
        },
        {to: '/blog', label: 'Blog', position: 'left'},
        {
            href: 'https://github.com/facebook/docusaurus',
            label: 'GitHub',
            position: 'right',
        },
        ],
    },
    // snip
}
```

懒得改了, forget it

