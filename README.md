# 네이버 쇼핑 리뷰 데이터 감성 분석

## 프로젝트 개요
- 네이버 쇼핑 리뷰 데이터를 활용하여 감성 분석(긍정,부정 예측)을 진행했습니다.

## 프로젝트 배경 및 목적
- 소비자가 자사의 제품 혹은 서비스에 대하여 긍정적인지 부정적인지 알게 된다면 기업에 매출 증대에 도움이 될 것입니다.  
따라서 소비자의 진짜 마음을 알 수 있는 감성분석(긍부정예측)을 진행했습니다.

## 데이터 정보

<!-- ![image](https://user-images.githubusercontent.com/60374463/198240396-01987243-9bb4-4d99-b668-88b66d7e9ba9.png)-->
<img width=700 alt="image1" src="https://user-images.githubusercontent.com/60374463/198240396-01987243-9bb4-4d99-b668-88b66d7e9ba9.png">

- 출처 : https://github.com/bab2min/corpus/tree/master/sentiment
- 행 : 20만개
- 열 : 2개

## 프로젝트 과정

<!-- ![image](https://user-images.githubusercontent.com/60374463/198240597-8407e86e-d796-4edc-ace4-60056c844387.png)-->
<img width=700 alt="image1" src="https://user-images.githubusercontent.com/60374463/198240597-8407e86e-d796-4edc-ace4-60056c844387.png">

#### [전처리 과정.ipynb](sentiment_text_preprocessing.ipynb)
### 1. 데이터 전처리 과정
- 중복 데이터 제거
- 결측치 제거
- 학습/테스트 데이터 분리
  - 학습 : 149931개
  -  테스트 : 49977개
### 2. 텍스트 전처리 과정
1. 띄어쓰기/맞춤법 보정
   - py-hanspell 라이브러리를 사용하여 먼저 띄어쓰기와 맞춤법을 보정합니다.
   - 위에서 처리하지 못한 텍스트는 pykospacing 라이브러리를 사용하여 띄어쓰기만 보정합니다.
2. 정규표현식 사용하여 문자 제거
    - 영어, 특수문자 제거
3. 불용어 처리 및 토큰화
    - 인터넷에서 수집한 불용어를 정의하여 사용합니다.
    - konlpy 라이브러리의 형태소 분석기를 사용하여 토큰화를 진행합니다.
      - 형태소 분석기 : okt, komoran, hannanum, mecab 사용
        - kkma의 경우 고질적인 메모리 문제로 에러가 발생하여 사용하지 않았습니다.

#### [모델링 과정.ipynb](sentiment_modeling.ipynb)
### 3. 모델링 과정
1. 타겟데이터 생성합니다.
- score 컬럼의 1,2를 부정으로 4,5를 긍정으로 판단하여 각각 0과 1의 값으로 변환합니다.
2. 빈도수가 낮은 단어 제거합니다.
3. konlpy의 여러 형태소 분석기의 소요시간과 정확도를 비교합니다.

<!-- ![image](https://user-images.githubusercontent.com/60374463/198240846-c44a7110-bf3e-43a9-bfb7-9657fb266520.png)-->
<img width=700 alt="image1" src="https://user-images.githubusercontent.com/60374463/198240846-c44a7110-bf3e-43a9-bfb7-9657fb266520.png">

<!-- - okt : 소요시간 : 약 31분, 정확도 : 89.4%
- komoran : 소요시간 : 6분, 정확도 : 84.4%
- hannanum : 소요시간 : 52분, 정확도 : 83.4%
- mecab : 소요시간 : 1분, 정확도 87.3%
-->

- 소요시간과 정확도를 기준으로 mecab 형태소 분석기를 최종 형태소 분석기로 정합니다.
  - okt의 경우 정확도는 mecab보다 높았지만 소요시간이 오래걸렸기 때문에 okt보다 적게걸린 mecab 형태소 분석기를 선정했습니다.
4. LSTM을 사용하여 모델링 구축 및 하이퍼 파라미터 튜닝

## 모델링
LSTM 모델을 사용했으며 하이퍼파라미터 튜닝을 통해서 성능개선을 진행했습니다.

- 기존의 RNN의 장기의존성 문제를 개선한 LSTM 모델을 사용
- 장기의존성 문제 : 시퀀스가 길어질수록 앞에 등장한 정보를 잘 유지하지 못하는 문제

### 성능 개선
- 튜닝 전 정확도 : **87.3%** -> 튜닝 후 정확도 : **88.6%**

### 감성분석 결과

<!-- ![image](https://user-images.githubusercontent.com/60374463/198241238-57006326-8a66-4061-b498-89a02b788ee6.png)-->
<img width=700 alt="image1" src="https://user-images.githubusercontent.com/60374463/198241238-57006326-8a66-4061-b498-89a02b788ee6.png">

## 회고
### 잘한점/만족한점
1. konlpy의 여러 형태소분석기를 비교하여 진행한점
2. 최종 모델이 88.6%라는 만족스러운 정확도의 성능을 보였다는 점

### 아쉬운 점

<!--
![image](https://user-images.githubusercontent.com/60374463/198241481-0816f8f1-4d6f-4058-b926-b38002da1047.png)
<img width=700 alt="image1" src="https://user-images.githubusercontent.com/60374463/198241481-0816f8f1-4d6f-4058-b926-b38002da1047.png">
-->
1. 과적합을 개선하지 못한 점
2. 다양한 감정에 대하여 고려하지 못한 점
   - 긍정 : 기쁨, 행복, 흥미 등등
   - 부정 : 분노, 놀람, 혐오 등등
3. 사전학습모델을 사용하지 못한 점
    - KoGPT
    - KoBERT
