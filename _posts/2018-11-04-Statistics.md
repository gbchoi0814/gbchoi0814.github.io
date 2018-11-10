---
layout: post
title: (매우얕은) 통계학
---

### 통계학의 과제
통계를 통하여 해결하고자 하는 문제는 아래와 같은 것들이 있다. 

***Regression***
- $y=f(x)$형태의 모델임. 
- $\\{(x_i,y_i)\\}_ {i=1}^{n}$에서 $f$를 학습하는 방식임. 
- 회귀는 결국 function estimation. 

***Classification***
- $y=f(x)$형태의 자료에서 $y=0$ 또는 $1$인 형태이므로 회귀모델로 볼 수 있음. 

***Anomaly detection***
- 주어진 입력표본 $\\{x_i\\}_ {i=1}^{n}$에 포함된 비정상적인 값을 발견하는 문제이다. 
- 밀집해있는 자료를 정상으로 간주하고 그 군집으로부터 멀리 떨어진 자료를 비정상으로 간주한다. (Line형태로 군집이 이루어지면 어쩔꺼야? ㅋㅋ) 

***Clustering*** 
- 표본간의 유사도를 측정하는 방법을 적절히 선택하는 것이 중요하다. 
- 이때 그래프라플라시안을 활용하면 좋을듯? smoothness개념 
- hst에서도 전체 smoothness를 정의하는 어떠한 방법이 있으면 좋겠네. Total variation의 개념과도 연결되는것 같다? 

***Dimensionality reduction***
- 입력차원 $\\{x_i\\}_ {i=1}^{n}$의 차원이 매우 클때 그것을 낮은차원의 자료들 $\\{z_i\\}_ {i=1}^{n}$로 바꾸는 방법
- 차원축소를 수행하는 연산이 선형이면 적당한 행렬 ${\bf T}$를 사용하여 $z_i=x_i{\bf T}$와 같은 방식으로 쓸 수 있음. 
- 가령 예를 들어서 $x_i=[1,2,4,5,8]$과 같은 자료가 있다고 하자. 이때 $x_i$의 차원은 $1\times 5$이다. 그러면 ${\bf T}$를 $5\times 2$와 같은 행렬을 잡아와서 $z_i=x_i{\bf T}$와 같이 쓸 수 있는데 이러면 $z_i$의 차원은 $1 \times 2$가 되고 ${\bf T}$는 5차원자료를 2차원으로 줄이는 변환이 된다. 

---

### 통계학의 계파 
위에 제시된 문제를 해결하는데에는 다양한 철학을 가진 학파가 있다. 

***Generative model vs Discriminative model***
- $28\times 28$-pixel 의 이미지자료가 있다고 하자. 따라서 이 경우 ${\bf X}_ {n\times 784}$가 된다. 
- $y_{n\times 1}$이라고 하고, $y \in \\{0,\dots,9\\}$라고 하자. 
- 어쨋든 ${\bf X}$와 $y$를 통하여 conditional pdf $f(y|{\bf X})$를 알 수 있다. 결국 분류문제는 아래식을 풀어서 $\hat{y}$을 구하는 과정으로 이해할 수 있다. 
  \begin{align}
  \hat{y}=\underset{y}{\operatorname{argmax}} f(y|{\bf X})
  \end{align}
- 그런데 위에서 $f(y|{\bf X})$를 최대화하는 대신에 $f({\bf X},y)$를 최대화해도 문제없는데 그 이유는 아래식이 성립하기 때문이다. 
  \begin{align}
  f(y|{\bf X})=\frac{f({\bf X},y)}{f({\bf X})} \propto f({\bf X},y)
  \end{align}
  이처럼 $\hat{y}$는 $f({\bf X},y)$를 최대화 하거나 $f(y|{\bf X})$를 최대화 함으로써 구할 수 있는데 $f({\bf X},y)$를 최대화 하여 구하자는 주의가 *Generative model*을 지지하는 사람들이고  $f(y|{\bf X})$를 최대화 하여 구하자는 사람들은 *Discriminative model*을 지지하는 사람들이다.  
- 일반적으로 $f({\bf X},y)$를 알면 $f(y|{\bf X})$를 쉽게 계산할 수 있지만(아래식참고) 반대는 불가능하므로 $f({\bf X},y)$를 아는게 더 어려운 일이다. 
  \begin{align}
  f(y|{\bf X})=\frac{f({\bf X},y)}{f({\bf X})}=\frac{f({\bf X},y)}{\sum_y f({\bf X},y)}
  \end{align}
- 따라서 더 구하기 힘든함수 $f({\bf X},y)$를 알아내서 $\hat{y}$를 구하는 것보다는 좀 더 구하기 쉬운 함수 $f(y \vert {\bf X})$ 를 알아낸 다음에 $\hat{y}$을 구하는 것이 더 효과적이다. 이것이 SVM을 창시한 뱁닉(Vapnik)의 아이디어이다. 


***Frequentis vs Bayesian***
- Frequentist는 $\theta$를 확률변수로 보지 않는다. (fixed paramater라고 생각함.)
- Bayesian은 $\theta$를 확률변수라고 생각한다. 
- 따라서 Frequentist는 $\theta$의 값을 MLE와 같은 방법으로 구하려고 하지만 Bayesian는 $\theta$의 (posterior) distribution 관심이 있다. 

---

### (선형) 모델링하는 방법
- $\\{(x_i,y_i)\\}_ {i=1}^{n}$을 관측하였다고 하자. 우리가 풀고 싶은 문제는
\begin{align}
y_i=f(x_i)+\epsilon_i
\end{align}
로 표현했을때 어색하지 않은 underlying function $f(x)$를 찾아내는 것이다. 이러한 $f(x)$를 찾으면 모델이 만들어 지고 결국 새로운 관측치 $x_{i^* }$에 대한 prediction을 할 수 있다. 

- 가장 기본적인 모델인 1차원 회귀식으로 부터 시작하자. 
\begin{align}
y_i=f(x_i)+\epsilon_i, ~~~ f(x)=\beta_0+x\beta_1
\end{align}
위의 모델은 디자인매트릭스를 $f(x)$를 설명하기 위한 basis를 $[1,x]$로 선택하였다. 

- 위의 모델을 좀 더 개신시켜 basis를 $[1,\cos x, \sin x, \cos 2x, \sin 2x, \dots, ]$와 같이 확장해볼 수도 있을 것이다. 이때 각각의 basis는 오소고날하다. 

- 위의 2가지 방법은 각각 어떠한 가정을 내포한다. 가령 
''$f(x)$는 $x$에 선형변환으로 만들어질 수 있다. (즉 $f(x)=\beta_0+\beta_1x$)'' 
라든가 하는식으로 말이다. 이말은 
\begin{align}
y_i=f(x_i)+\epsilon, 
\end{align}
와 같은 모델에서 $f$가 어떠한 형태를 가질것인지 미리 알고 있다고 생각한다는 말과 같다. 이처럼 $f$가 어떤 모양인지 미리 알고 접근하는 방법을 파라메트릭 모델이라고 한다. 그리고 보통 $f$의 모양을 결정하는 과정(즉 적절한 basis를 선택하는 과정)을 모델링이라고 한다. 

- $f$를 어떻게 모델링할지 감이 안 올 수도 있다. 즉 자료를 봤는데 선형의 모양을 가지는지 어떤지 감을 못잡겠는 경우이다. 이것을 바꾸어 말하면 $\\{y_i\\}$가 $\\{x_i\\}$의 어떤 space에 있는지 감을 못 잡겠다는 뜻이다. 혹은 모델링이 귀찮을 수도 있다. 이럴 경우 $f(x)$가 $x$의 어떤 특정스페이스 $\cal A$의 부분공간에 존재한다고 가정하고 그 특정스페이스 $\cal A$를 expansion할 수 있는 베이시스를 선택하여 문제를 풀 수 있다. 가령 예를들면
''$f(x)$가 어떤 공간에 있는지 모르겠는데 최소한 비숍스페이스의 부분공간에 있는것 같아'' 
라고 생각한다면 웨이블릿 베이시스를 선택하여 모델링 하는 것이다. 

- 보통위와 같은 접근법은 무한대의 basis를 활용한다. 많은 수학자들이 
"이런식으로 무한개의 basis를 활용하면 특정공간에 있는 어떠한 함수도 표현할 수 있어요~"
라는 식의 증명을 많이 해놓았는데 이러한 증명결과들을 적극적으로 활용하는 셈이다. 요렇게 $f$를 표현하는데 무한개의 basis를 활용하는 모델링을 semi-parametric이라고 한다. 

- 커널모델을 사용하여 자료를 표현하는 경우는 어떤가? 커널모델은 $f(x)$를 아래와 같이 가정하는 것으로 이해할 수 있다. 
\begin{align}
f(x)=\theta_1K_h(x,x_i)+\dots+\theta_n K_h(x,x_n)
\end{align}
특이한것은 $f(x)$를 설계할때 관측자료 $x_i$를 사용하였다는 점이다. 퓨리에 변환이 $f(x)$를 가정하는 방식
\begin{align}
f(x)=\sum_{k =0}^{\infty}\theta_k+e^{i\omega kx}
\end{align}
과 비교하여 보면 퓨리에변환은 $f(x)$를 표현하는데 무한대에 가까운 숫자의 basis를 사용하였지만 입력자료 $x_i$를 basis에 활용하지는 않았다. 이처럼 입력값 $x_i$를 직접 basis를 설계하는데 활용하는 방식을 non-parametric이라고 한다. 

- 지금까지 살펴본 것은 모두 입력변수가 1차원일 경우에 basis를 선택하는 방법이었다. 즉 
\begin{align}
y_i=f(x_i)+\epsilon_i
\end{align}
와 같은 모형이었다. 입력변수가 2개일 경우는 어떻게 basis를 선택해야 하는가? 예를들어 ${\bf x}$가 $2\times2$ pixel로 이루어 졌다고 하자. 각각의 픽셀값은 ${\bf x}=[x11,x12,x21,x22]$으로 정의된다고 하자. 여기에서 $x11$은 왼쪽위의 pixel이라는 뜻이다. 모형은 아래와 같을 것이다. 
\begin{align}
y_i=f({\bf x}_ i) +\epsilon_i
\end{align}
따라서 2차원인 경우는 기저를 어떻게 잡아야 할까? 보통 아래와 같이 2가지 경우가 있다. 
  
  - 첫번째 방법(파라메트릭): $f(x11)$,$f(x12)$,$f(x21)$,$f(x22)$의 basis를 알고 있을 때 이를 적당히 확장하여 $f({\bf x})$을 만드는 경우 
  - 두번째 방법(넌파라메트릭): $f({\bf x})$의 기저를 바로 찾음. <br/><br/>

- 여기에서 첫번째 방법(1차원 basis를 적당히 확장하는 경우)은 다시 아래와 같은 예가 있을 수 있다. 
  
  - (multiplicative model) 고차원베이시스를 1차원베이시스들의 product로 표현할 수 있다는 관점임.
  - (additive model) 고차원베이시스를 1차원베이시스의 합으로 표현할 수 있다는 관점임. <br/><br/>
  
- 이때 multiplicative model이 골때린다. 이 모델은 쉽게 말하면 교호작용을 고려하는 모델로 볼 수있다. 예를들어 $4$이고 각 차원을 2개의 파라메터(예를들면 $[1,x]$)로 표현하고 있으면 전체 필요한 파라메터 수는 $2^4$이다. 만약에 입력차원이 $10$이라고 하면 각 변수당 2개의 파라메터를 쓸때 $2^{10}=1024$개의 파라메터가 필요하다. 이는 결국 자료가 1024개 필요하다는 의미가 되는데 한마디로 망한 모델이라는 소리이다. 이처럼 입력변수의 차원에 대하여 파라메터의 수가 지수적으로 증가하는 현상을 차원의 저주라고 부른다. 그래서 일반적으로 Multiplicative model보다 Additive model을 고려하는 것이 좋다. 

- 일반적으로 입력변수가 고차원일때에는 파라메트릭모델보다 넌파라메트릭 모델을 고려하는것이 차원의 저주를 피하기에 유리하다. 커널과 같은 경우를 예로 들어보자. $\\{ {\bf x} \\}_ {i=1}^{n}$를 관측하였다면, 
\begin{align}
f({\bf x})=\sum_{i=1}^{n} \theta_i K_h({\bf x},{\bf x}_ i)
\end{align}
와 같이 쓸 수 있다. 이때 
\begin{align}
{\bf x}_ i=[x11_i,x12_i,x21_i,x22_i]
\end{align}
이다. 입력변수의 차원에 상관없이 $n$개의 basis만 가지고서 $f({\bf x})$를 표현 할 수 있다. 

- 커널방법의 또 다른 장점은 $K({\bf x},{\bf x}_ i)$만 잘정의하면 어떠한 입력형태 $\bf x$에서도 동작한다는 것이다. 예를들면 입력 $\bf x$가 문자열, 트리, 그래프등인 경우에도 동작한다. 

- 지금까지 살펴본 모델은 모두 선형모델이다. 선형모델이라는 뜻은 $f(x)$를 아래와 같은 방식으로 표현한다는 의미이다. 
\begin{align}
f(x)=\sum \textup{coef}\times \textup{basis} 
\end{align}
여기에서 coef는 parameter로 이해해도 된다. 왜 우리는 선형모델을 가정할까? 선형모델을 가정하면 최소제곱법과 같은 방법을 활용하여 parameter를 매우 쉬운 연산만으로 구할 수 있다는 장점이 있다. 앞에서 살펴본 커널의 경우도 밴드윗을 $h$로 고정하고 중심도 $x_i$로 고정된 상태에서 $\theta_i$를 구하는것은 선형모델이다. (하지만 만약에 커널모델에서 $h$와 $\theta_i$를 동시에 구해야 한다면 이것은 비선형 방정식을 풀어야한다.)  

---

### (선형모델에서) Basis의 Coef를 구하기 
- 회귀분석류의 대부분의 방식은 본질적으로 2가지 단계로 귀결된다. 

  1. 모델링을 한다. (=basis를 선택한다) 
  2. 각 basis의 coef를 구한다. 
  <br/><br/>
  
- 최소제곱학습법을 활용하여 파라메터를 구하는 방법이 있다. 약점은 디자인매트릭스 $\Psi$