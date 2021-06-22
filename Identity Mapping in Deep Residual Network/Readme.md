# Identity Mapping in Deep Residual Networks Testing in CIFAR-10

# Summary

- 논문은 깊은 신경망 에서 **Shortcut Connection 및 Activation 함수의 형태가 Identity Mapping**일 때 성능이 좋다고 주장함
- 이는 Shortcut connection에 의해 전달되는 **input이 Clean 해야 Forward/Backward Propagation 시 Directly**하게 **다른 Layer로 전달**될 수 있기 때문임
- 이 실험에서는 **Shortcut 및 Forward 순서의 변형**을 준 다양한 모델들을 생성하여 Identity Mapping을 수행하는 **Original Block과의 성능 차이를 측정**함
- 실험 결과 **Shortcut 변형 부분에서는 Convolution shortcut**이 가장 낮은 Test-Error를 보였고 **Forward Process 변형 부분에서는 Full Pre-Activation**이 가장 낮은 Test-Error를 보임

# Model Overview

![Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled.png](Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled.png)

# Result

### Shortcut Transformation Result

- **Conv Shortcut Block** > Constant Scaled Block > **Original Block** > Shortcut Gating Block > Dropout Block > Exclusive Gating 순 성능 좋음
    - **논문과 다른 실험 결과**
        - 이는 실험에 **ResNet-20이 논문의 ResNet-110에 비해 충분히 깊지 않아** 이러한 결과가 나온 것으로 추정 됨
        - Original Block의 **수렴 속도가 가장 빠름**
        - Original Block의 성능 지표**(8.56%)**가 3번째 이긴 해도 **1(8.43%), 2위(8.5)와 Marginal 한 차이라고 보기 힘듦**

### Forward Process Transformation Result

- **Full Pre-Activation** > ReLu Only Pre-Activation > **Original Process** > BN After Addition > ReLu Before Addition 순으로 성능 높았음 (Test Error 역순)
    - 논문의 결론대로 **Full Pre-Activation 방식으로 Forward 하는 것이 가장 좋은 성능**을 보였음
        1. (Full Pre-Activation, ReLu Only Pre-Activation) > (Original Process, BN After Addition)
            - 좌항의 기법들은 shortcut이 activation에 의해 변형되지 않고 clean하게 다음 layer로 전달됨. 따라서 이는 **activation이 identity mapping을 수행하는 것이 최적임을 증명함**
        2. ReLu Before Addition 최하인 이유
            - 이 기법도 shortcut이 ReLu의 영향을 받지 않아 identity mapping 하지만, Addition 전에 ReLU 기법을 적용할 경우 **Residual Function 값이 음수일 때 output이 0이 된다. 이에 따라 학습에 어려움**을 겪음
        3. Full Pre-Activation > ReLu Only Pre-Activation
            - 전자의 경우 **BN이 Shortcut과 Residual Function 모두에 적용되어 Normalized에 의한 학습 성능 향상 효과**를 볼 수 있음
            - 후자의 경우 BN이 Residual Function만 적용 됨
        4. Full Pre-Activation > Original Process
            - Train Error에서 Full Pre-Activation이 Original Process에 비해 높았으나, Test Error는 더 낮았음
            - 이는 논문의 **Full Pre-Activation의 Overffiting 완화 효과를 증명**함
        5. Full Pre-Activation은 forward transfomation 뿐 아니라 **다른 어떤 shortcut transformation 보다도 성능이 높음**

# Result Visualization

### Summary Table

![Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%201.png](Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%201.png)

### Test Error Graph

![Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%202.png](Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%202.png)

### Train Error Graph

![Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%203.png](Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%203.png)

# Appendix

### **Conv Shortcut & Full Pre-Activation Testing**

- ResNet-20에서 가장 성능이 좋았던 Conv Shortcut Block에 Full Pre-Activation의 Forward 방식을 적용하여 학습 시켜 봄

    ![Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%204.png](Identity%20Mapping%20in%20Deep%20Residual%20Networks%20Testing%2050c3404c7111413ca80dfe375f0a2c09/Untitled%204.png)

- Convolution Shortcut & Full Pre-Activation 실험 결과
    - Original Block, Convolution Block 보다는 성능이 좋고 **(8.16 < 8.56, 8.43)**
    - Identity Shortcut & Full Pre-Activation 보다는 성능이 낮다 **(8.16 > 8.08)**
- 의미
    - **Convolution Block의 Forward 구조**를 **Pre-Activation 기법**으로 하는 것이 **좋음**
    - 단, **Pre-Activation 기법**의 **Shortcut Connection**을 **Convolution으로 적용**하는 것은 **Identity 보다 나쁨**
    - Convolution Block과 Full Pre-Activation의 **시너지 효과를 기대했으나 결과는 그렇지 못함**.
        - Pre-Activation이 Conv에 주는 이익 < Conv가 Pre-Activation에 주는 손해
