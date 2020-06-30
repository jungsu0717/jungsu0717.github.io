---
title: "OCR + Flutter + Firebase ML Kit 연동"
date: 2020-06-30 11:48:28 -0400
categories: ocr
---

1) 개요
기존 Tesseract + OpenCV 로 작업 결과 Tesseract 의 학습데이터 인식률 부족과 OpenCV 의 다양한 기능 적용에 어려움이 있어 Firebase ML Kit 을 최종적으로 사용하게 됨 
ML Kit를 사용하면 Google Cloud Vision API , Mobile Vision 및 TensorFlow Lite 와 같은 Google의 ML 기술을 단일 SDK로 통합 하여 앱에 ML 기술을 쉽게 적용 할 수 있음
2) 주요 기능


API	적용 범위	비고
기기별	클라우드
텍스트 인식 (OCR)	




이용 정보


상세 가격

기능	1개월 단위 1,000개당 가격
처음 1,000개	1,001 ~ 5,000,000개	5,000,001 ~ 20,000,000개
텍스트 감지	무료	$1.50	$1.50
클라우드에서 한국어 지원 (단, 기기 단위에서는 라틴어만 제공)


언어 지원 상세정보 (https://cloud.google.com/vision/docs/languages?authuser=0#supported-langs)

얼굴 인식	





바코드 스캔	





이미지 라벨 지정	





객체 감지 및 추적	




랜드마크 인식	





언어 식별	




번역	




스마트 답장	




AutoML 모델을 통한 추론	




커스텀 모델을 통한 추론	




3) 텍스트 인식 (OCR) 예시 (Google Docs)














4) 텍스트 인식 (OCR) 결과


그림 및 도형 테스트





특수문자, 밑줄 등 다양한 효과 테스트





배경 / 기울기 테스트



