# Transfer Learning (전이학습)
 - 비슷한 역할을 하는 사전의 학습된 좋은 모델을 재활용하는 개념
  
<br><br>
 
 [활용 모델 : VGG ]
 - keras Application (제공모델)
 - Models.Sequential로 접근가능 <br>
  (다른 모델의 경우 별도 Functional API 활용 필요)
 - imageNet(1000가지의 이미지 클래스를 분류해놓은 데이터) 를 활용한 다진분류 모델
 - padding : same으로 설정되어있기 때문에 conv할때는 줄지 않고, 맥스풀링할 때 크기가 줄어듬
  
<br><br> 
 [전이학습 종류] 

1. Feature Extraction (일반적으로 사용 잘안함)
   - CNN 재사용
   - DNN 생성

2. Fine Tuning (미세조정)
   - CNN Layer Parameter (추가학습 가능)
       1) 일부 parameter - 뒤쪽 레이어 일부 재학습시킴
       2) 전체 parameter - 보통 전체 파라미터 학습을 현실적으로 많이 사용함

   - DNN 생성 후 연결



    > filter를 거듭 할수록 한 셀에서 볼 수 있는 영역은 넓어 진다. <br>
        앞쪽 레이어는 대부분의 사물들을 아주 크게 확대해서 부분적으로 보니까<br>
        비교적 공통적인 특징을 학습하게 된다.  <br>
        반면, 뒷쪽 필터들은 레이어가 깊어지며 원본이미지의 넓은 특징을 학습하게 되니까<br>
        개별 클래스의 구체적인 특징을 학습하게 된다 <br><br>
        그렇기 때문에 일반적으로 별반 차이가 없는 앞쪽 레이어는 효율적으로 freezing시키고, <br> 구체적으로 학습하는 뒤쪽 영역을 재학습 시킴 <br><br> 하지만 일반적으로는 전체 재학습을 가장 많이 사용하며, 성능도 좋다.<br><br>
        이미 학습되어있는 모델은 로컬미니멀에 이미 가까이 가 있는 상태여서 <br>
        전이 학습시킬 땐 매우 작은 learning_rate 값을 줘야한다! <br>
        그렇지 않으면 오차함수 그래프를 발산해 버리는, 성능이 오히려 안 좋아지는 현상이 발생 할 수 있다.<br>

<br><br>

[활용 방법]
 - 이진분류를 위해 CNN 단계까지만 사용하고, 기존 DNN Classification 제거
 - forward로 데이터를 흘려만 보낼거라, 파라미터가 학습되어 변경되지 않음
 - (150,150,3) → VGG16 흘려보내면 →  (4,4,512) (feature map)로 나옴
 - 결국 학습된 파라미터를 사용해서 이미지를 피처맵으로 바꿔주는 용도
 - layer 개수 : 16개
   - 3개는 DNN layer라 제외
   - 파라미터가 있는 layer만 카운팅 : 13개
  
<br>

[ISSUE]
 1. Input data shape ( default : 224 * 224) → reshape 필요  <br>
    ```input_shape = (150,150,3)```
 2. DNN제거 <br>
   ```include_top = False ```<br>
   (DNN 단계 : top , CNN 단계 : bottom)
     

    ```python
    From tensorflow.keras.applications import VGG16
    VGG16(weights= 'imagenet', #imagenet을 학습한 파라미터 사용
    include_top = False,
    input_shape = (150,150,3)
    ```

<br><br>

------------
참고 <br>
[TensorFlow Hub : 학습모델저장소](https://www.tensorflow.org/hub?hl=ko)
