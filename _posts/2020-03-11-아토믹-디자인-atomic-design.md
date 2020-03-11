---
date: '2020-03-11 10:48:51'
layout: post
title: 아토믹 디자인 (Atomic Design)
subtitle: UI를 레고 블럭 처럼 조립해서 만들기
description: 아토믹 디자인 (Atomic Design) | UI를 레고 블럭 처럼 조립해서 만들기
image: /assets/img/uploads/atomic-design-process.png
category: dev
tags:
  - dev
author: jake
paginate: false
---
## 아토믹 디자인(Atomic Design) 이란?

디자인 시스템을 만드는 하나의 방법론으로, UI를 구성하는 단위를 레고 블럭 같 작은 단위로 구성하여 조합해서 사용하는 것을 말한다. 기본 개념으로 크게 ATOMS, MOLCULES, ORGANISMS, TEMPLATE, PAGES가 있다.

### ATOMS (원자)

![atoms](/assets/img/uploads/atoms-form-elements.png)

원자는 가장 **기본적인** 구성요소이다. 가장 작은 단위이며 고유한 속성을 지니고 있다. HTML 태그의 label, input, button등이 포함된다.

### Molecules (분자)

![molecules](/assets/img/uploads/molecule-search-form.png)

분자는 원자들이 조합된 결합체이고 UI 요소의 단순한 그룹이다. **하나의 목적**을 가진다. label, input, button을 결합하여 검색 양식 분자를 만들 수 있다. 이 원자들을 결합하면 목적이 생긴다. label은 input을 정의하고 button을 클릭하면 양식이 제출된다. 그 결과, 검색 기능이 필요한 곳이라면 **재사용**이 가능하고, **독립적**인 구성품이 된다.

### Organisms (조직유기체)

![organisms](/assets/img/uploads/organism-header.png)

유기체는 **원자와 분자들이 조합된 결합체**이며, 비교적 복잡한 UI 요소의 그룹이다. 이 유기체들은 인터페이스의 개별적인 부분을 형성한다. 분자에서 좀 더 정교한 유기체로 발전하는 것은 중요하다. 유기체들은 더 작고 단순한 요소들을 실제로 보여주고, 계속해서 사용할 수 있는 뚜렷한 패턴으로 작용한다. 예를들어 쇼핑몰의 제품 목록 그리드가 있다.

### Templates (템플릿)

![templates](/assets/img/uploads/template.png)

템플릿은 간단히 말해 **페이지의 골격**이다.구성 요소를 레이아웃에 배치하고 설계의 기본 내용 구조를 명확하게 설명하는 페이지 레벨이다. 이전의 예시를 바탕으로 , 헤더 유기체를 가져다가 홈페이지 템플릿에 적용할 수 있다.

### Pages (페이지)

![pages](/assets/img/uploads/page.png)

페이지는 완성된 레이아웃에 데이터를 넣어 페이지를 완성하여, 실제로 사용자들이 사용하고 경험하는 단계이다. 실제 데이터를 적용할 때 해당 패턴이 어떻게 유지되는지 살펴볼 수 있다. 제대로 동작하지 않는다면 분자들, 유기체들을 다시 수정하고 재 조합할 수 있다.

## 결

![summary](/assets/img/uploads/1_fsiixik0shyrjkuurvlcya.png)

요약을 해보자면 현재 프론트엔드 개발에서 많이 사용하는 리액트, 뷰와 같은 컴포넌트 기반 프로그래밍에 적합한 디자인 패턴이다. 아토믹 디자인이란 개념은 없었어도 이러한 방식으로 개발을 해왔을 것이라 생각된다.

그러나 개발에서 5단계로 나누어서 작업한다는건 조금 까다로울 수 있는데, Line Entry에서는 5단계를 3단계(ATOMS , MODULES(Molecules + Organisms), TEMPLATE(Templates + Pages))로 줄여서 사용한다고 한다. 

## 출처

- [Atomic Design Methodology](https://atomicdesign.bradfrost.com/chapter-2/)
- [아토믹 디자인 패턴으로 디자이너와 협업하기](https://speakerdeck.com/feel5ny/atomig-paeteoneuro-dijaineowa-hyeobeobhagi)
- [Line Entry의 Atomic Design 적용기](https://www.youtube.com/watch?v=33yj-Q5v8mQ)