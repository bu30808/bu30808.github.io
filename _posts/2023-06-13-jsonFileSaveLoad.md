---
layout: post
categories: blog
title: UE5) JSON 파일 저장 및 불러오기
date: 2023-06-13
tags: [UE5,UnrealEngine,json]
toc:  true
---

## 저장

레벨에 있는 액터를 Json으로 저장해보도록 하자.

1. 액터에 여러 정보를 묶어 저장해야 하니까, 구조체를 하나 만들자.
<script src="https://gist.github.com/bu30808/963107e4dff9a9bcf51cbbb970769d5b.js"></script>





2. 액터가 하나만 있는것은 아니니까 1번에서 만든 구조체로 배열을 가진 구조체를 만들자.
<script src="https://gist.github.com/bu30808/c6139dfd9a7ccf6a1491cd338ac8e287.js"></script>


3. 이제 위 구조체에 저장할 대상들을 가져오면 된다.
<script src="https://gist.github.com/bu30808/67302c7cb7c2e5d3ca54351d38c60ac0.js"></script>


4. 가져온 대상들을 구조체에 저장하자.
<script src="https://gist.github.com/bu30808/5a2d19b3c597d6926b731ae8e3be29fc.js"></script>

5. 구조체를 Json으로 변환하자.
<script src="https://gist.github.com/bu30808/1a0bf433996cb5b87fcdc07909abd5ff.js"></script>

6. 저장하자.
<script src="https://gist.github.com/bu30808/764f9fc905c2058b7af45e9c1ad94e7a.js"></script>








## 불러오기

1. 저장한 Json파일을 가져오자
<script src="https://gist.github.com/bu30808/c2aec18c9c823a5bb88217f4583c5904.js"></script>

2. 가져온 Json을 구조체로 변환하면 된다.
<script src="https://gist.github.com/bu30808/37f1a33a3ec88a031e50c732ba471700.js"></script>
