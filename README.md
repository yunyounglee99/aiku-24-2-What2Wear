# 오뭐입(What2Wear)

📢 2024년 2 [AIKU](https://github.com/AIKU-Official) 활동으로 진행한 프로젝트입니다

## 소개

패션 도메인에서 단일 아이템의 디테일을 인식하고, 어울림 학습을 통해 하나의 단일 아이템이 아닌, 함께 입을 수 있는 코디를 이해하는 인공지능 모델 개발 프로젝트입니다. 이 프로젝트에서는 어울림을 학습하는 모델과 이를 활용한 새로운 형태의 추천 시스템을 제안합니다.

## 방법론

### Generate outfit(modified) vector

<img width="910" alt="image 2" src="https://github.com/user-attachments/assets/e77bdda7-71cc-4377-88db-8b28f9d1e419">


- 학습을 위해 배경을 흰색으로 날린(By. SAM) 상의, 하의, 신발을 포함한 전신샷 코디이미지를 준비했습니다.
- 코디 이미지를 적절한 비율로 상의, 하의, 신발로 crop하여 코디 내의 단일 이미지를 image encoder가 잘 이해할 수 있도록 했습니다.

<img width="686" alt="image 3" src="https://github.com/user-attachments/assets/32db773f-5f94-4921-89df-e010a7a1d365">


1. 크롭된 코디 이미지(상의, 하의, 신발)를 각각 Image Encoder(FASHION CLIP)을 사용해서 512차원으로 인코딩하였습니다.
2. 각 카테고리 별 인코딩된 벡터들을 concatenate하여 (3, 512)차원의 outfit vector를 만듭니다.
3. outfit vector에서 랜덤한 두개의 카테고리를 지정해(ex. [상의, 하의] 또는 [상의, 신발]등..) 랜덤한 데이터에서 뽑아서 변경하여 modified vector를 만듭니다.
4. outfit vector는 명확한 어울림을 학습할 ‘정답 벡터’, modified vector는 어울림의 정도를 학습할 벡터로 사용할 것입니다.

### Training

<img width="707" alt="image 4" src="https://github.com/user-attachments/assets/ffc65012-6aaf-4d3a-b2a9-f4c99715f5f6">


- 학습은 단일 latent space에서 어울림을 학습합니다
    - outfit vector와 modified vector 모두 같은 latent space에서 동일한 어울림을 학습합니다.
    - 학습할 때에는 각각 카테고리 조합별로 contrastive loss를 수행합니다.  (상의, 하의), (하의, 신발), (상의, 신발) 총 3번의 loss를 수행한 후 이에 대한 평균을 구합니다.
    - outfit vector는 명확한 어울림을 학습하기 위한 데이터로, 각각의 유사도가 최대일 때, loss가 최소가 되도록 하였습니다.
    - modified vector는 적당한 어울림을 학습하기 위한 데이터로, 특정 threshold 이상일때는 어울림, 즉 loss를 최대한 갖지 않도록 하고, 특정 threshold 이하일때는 어울리지 않음, 즉, 큰 loss를 갖도록 하였습니다.
    - 두 벡터에서의 loss를 합한 것이 최종적인 loss가 됩니다.
- overfitting을 방지하기 위해 dropout과 layernorm을 사용했습니다.

## 환경 설정

google colab

## 사용 방법

input 이미지와 어울리는 의류를 모델이 찾을 수 있는 이미지 데이터베이스를 준비합니다.

## 예시 결과


<img width="414" alt="image" src="https://github.com/user-attachments/assets/d473a5f4-57ea-418c-8a7c-cf8293b860b2">

<img width="363" alt="image" src="https://github.com/user-attachments/assets/205b7957-676e-41f3-94b3-b8dde1e3455f">


## 팀원

- [이윤영](https://github.com/yunyounglee99): 프로젝트 총괄 모델 개발
- [박진태](외부 인원) : 모델 파인튜닝 및 데이터 전처리
- [박승우](외부 인원) : 데이터 수집


