---
layout: post
categories: blog
title: UE5) 런타임 텍스쳐 불러오기 및 내보내기
date: 2023-06-15
tags: [UE5,UnrealEngine]
toc:  true
---

런타임 중에 텍스쳐를 불러오거나 내보내보자.

## 불러오기
```
UKismetRenderingLibrary::ImportFileAsTexture2D(world,FilePath);
```

## 내보내기(모듈 "ImageWriteQueue" 필요)
<script src="https://gist.github.com/bu30808/5e0a91af52423a90a54130a9b5c299e9.js"></script>

절대 불러오자마자 내보내지 말 것.
불러오자마자 내보내면 아직 텍스쳐가 준비단계라 내보내기가 실패함.
굳이 필요하다면 불러 온 뒤, 딜레이를 주고 내보낼 것.
혹은 틱으로 체크하는 방법이 있음.
<script src="https://gist.github.com/bu30808/b161f3e86ec2159c7cf9ad1a311a4e07.js"></script>

