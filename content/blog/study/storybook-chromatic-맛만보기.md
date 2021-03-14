---
title: storybook chromatic 맛만보기
date: 2021-03-14 19:03:51
category: study
thumbnail: { thumbnailSrc }
draft: false
---

storybook chromatic 써보기

## 설치

![](https://i.imgur.com/dk0OCtB.png)

대충만든 TodoList 입니다.
깃주소는 https://github.com/soso01/storybook-study
크로매틱은 https://www.chromatic.com/builds?appId=604de062d6ac070021fbc1e4

크로매틱 연결은 사이트가서 프로젝트 생성 -> 패키지 설치 + 퍼블리시
```
npm install --save-dev chromatic
npx chromatic --project-token={토큰}
```
생각보다 좀 느리다.


## 기능들
![](https://i.imgur.com/4lf7YJX.png)

스토리북 배포, UI Test, UI Review를 제공.

### 스토리북 배포
![](https://i.imgur.com/e2Z2Y8k.png)
![](https://i.imgur.com/hvtfVGI.png)

빌드 상세로 가서 아래 View storybook 들어가면 해당 빌드의 스토리북 페이지를 확인할 수 있음.
자동으로 스토리북 페이지가 배포 되고 이전 버전의 빌드들도 다 확인할 수 있어서 너무 좋다.
이 기능만 있었어도 크로매틱 무조건 씀

### UI 테스트
![](https://i.imgur.com/FVASS2I.png)
delete 버튼에 색깔만 변경한 상태인데 두 버전의 UI 스냅샷을 비교해서 변경된 부분을 찾아준다.

### UI 리뷰
![](https://i.imgur.com/842IBmO.png)
크로메틱 프로젝트를 깃헙 레포와 연동하면 해당 레포의 PR을 가져온다.
위에 UI 테스트 비교화면과 리뷰 기능을 제공함.

![](https://i.imgur.com/T26dwDb.png)
깃헙 PR 페이지에서도 이렇게 크로매틱 승인 여부가 뜬다.
