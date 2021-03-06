---
layout: post 
title: (믿거나 말거나) 딥러닝 
---

딥러닝에 대해서 잘 모른다. 그래서 공부한다. 공부한 내용을 정리하는데 틀린내용이 있을 수 있다. 그래서 제목은 믿거나 말거나 딥러닝이다. 

### GAN 

- 우리가 관측한 자료 $\\{x_i\\}_ {i=1}^{n}$가 평균이 $500$이고 분산이 $5^2$인 정규분포 생성된다고 가정하자. 

- 제너레이터는 주어진 자료 $\\{x_i\\}_ {i=1}^{n}$로부터 generator's distribution $p_g(x)$를 알고 싶다. 즉 제너레이터의 목적은 
\begin{align}
p_g(x) \approx \frac{1}{\sqrt{5\times 2\pi}}\exp\left(\frac{-(x-500)^2}{2\times 5^2}\right)
\end{align}
가 되도록 $p_g(x)$를 학습하는 것이다. 제너레이터는 이러한 목적을 달성하기 위해서 평균이 0이고 분산이 1인 정규분포 $p_z(z)$에서 노이즈 $\\{z_i\\}_ {i=1}^{n}$를 발생시키고 노이즈로부터 $x_i$를 학습하는 MLP를 쌓는다. 즉 아래를 만족하는 적당한 함수 $G_{\theta_g}(z_i)$를 학습한다.  
\begin{align}
x_i \approx G_{\bf \theta_g}(z_i)
\end{align}
당연히 학습을 잘 했다면 $G_{\bf \theta_g}(z_i)=5\times z_i + 500$이 되도록 학습이 될 것이고 이때 ${\bf \theta_g}=(500,5)$가 된다. 

- 제너레이터와 별도로 디스크리미네이터는 입력으로 $\\{x_i\\}$ 혹은 $\\{G_{\bf \theta_g}(z_i)\\}$를 받는다. 편의상 이러한 자료를 $\\{y_i\\}$라고 하자. 즉 
\begin{align}
\\{y_i\\}:=\\{x_i\\} \cup \\{G_{\bf \theta_g}(z_i)\\}
\end{align}
$\\{x_i\\}$는 총 $n$개 있고, $\\{G(z_i;{\bf \theta_g})\\}$역시 총 $n$개가 있으므로 $\\{y_i\\}$는 $2n$개가 있을 것이다. 디스크리미네이터는 입력이 $\\{x_i\\}$일 경우에는 1을 출력하고 입력이 $\\{G_{\bf \theta_g}(z_i)\\}$일 경우에는 0을 출력하는 함수 $D_{\bf \theta_d}(y_i)$를 학습한다. 따라서 $D_{\bf \theta_d}(y_i)$가 자료 $\\{y_i\\}:=\\{x_i\\} \cup \\{G_{\bf \theta_g}(z_i)\\}$으로부터 파라메터 $\theta_d$를 잘 학습했다면 $D_{\bf \theta_d}(y_i)$는 아래와 같은 성질을 가져야 한다. <br/><br/>
> $y_i \in \\{x_i\\}$이면 $D_{\bf \theta_d}(y_i) \approx 1$이다. <br/><br/>
> $y_i \in \\{G_{\bf \theta_g}(z_i)\\}$이면 $D_{\bf \theta_d}(y_i) \approx 0$이다. 

- 디스크리미네이터의 관점에서 보자. 디스크리미네이터는 1) $D_{\bf \theta_d}(G_{\bf \theta_g}(z_i))$의 값들이 작을 수록 2) $D_{\bf \theta_d}(x_i)$의 값들이 클수록 학습이 잘 되었다고 볼 수 있다. 따라서 디스크리미네이터는 아래식을 만족하는 $\bf \theta_d$를 찾고 싶어한다. 이때 $\bf \theta_g$는 고정된 값이다. 
\begin{align}
\underset{\bf \theta_d}{\operatorname{argmin}}\frac{1}{n}\sum_{i=1}^{n} \left[ \log D_{\bf \theta_d}(x_i) +\log \left(1- D_{\bf \theta_d}(G_{\bf \theta_g}(z_i)) \right) \right] 
\end{align}

- 반면에 제너레이터는 $D_{\bf \theta_d}(G_{\bf \theta_g}(z_i))$값들이 커야지 학습이 잘 된 것이고 볼 수 있다. 즉 제너레이터는 아래식을 만족하는 $\bf \theta_g$를 찾고 싶어한다. 이때 $\bf \theta_d$는 고정된 값이다.  
\begin{align}
\underset{\bf \theta_d}{\operatorname{argmin}}\frac{1}{n}\sum_{i=1}^{n} \log \left(1-D_ {\bf \theta_d}(G_{\bf \theta_g}(z_i))\right)
\end{align}

- 우리가 원하는 것은 <br/><br/>
***"굉장히 성능이 좋은 디스크리미네이터조차도 구분하기가 쉽지않은 가짜 샘플을 만드는 제너레이너를 찾는 것"***<br/><br/>
이다. 이안굿펠로우는 요걸 달성하기 위해서 아래를 구하면 된다고 주장한다.     
\begin{align}
\underset{G_{\bf \theta_g}}{\operatorname{argmin}} \max_{D_{\bf \theta_d}} V(D_{\bf \theta_d},G_{\bf \theta_g})
\end{align}
여기에서  
\begin{align}
V(D_{\bf \theta_d},G_{\bf \theta_g})=E_{x \sim p_{data}}[\log D_{\bf \theta_d}(x)] + E_{z \sim p_z(z)}[\log (1-D_{\bf \theta_d}(G_{\bf \theta_g}(z))]
\end{align}
이다. 보면 알겠지만 <br/><br/>
1) $\frac{1}{n} \sum_{i=1}^{n}\log D_{\bf \theta_d}(x_i)$는 $E_{x \sim p_{data}}[\log D_{\bf \theta_d}(x)]$ 의 샘플버전이고<br/><br/>
2) $\frac{1}{n} \sum_{i=1}^{n} \log \left(1- D_{\bf \theta_d}(G_{\bf \theta_g}(z_i)) \right)$는 $ E_{z \sim p_z(z)}[\log (1-D_{\bf \theta_d}(G_{\bf \theta_g}(z))]$의 샘플버전이다. 따라서 이안굿펠로우의 주장은 매우 타당하다. 

- 즉 <br/><br/>
1) $\underset{\bf \theta_d}{\operatorname{argmin}}\frac{1}{n}\sum_{i=1}^{n} \left[ \log D_{\bf \theta_d}(x_i) +\log \left(1- D_{\bf \theta_d}(G_{\bf \theta_g}(z_i)) \right) \right]$ <br/><br/>
2) $\underset{\bf \theta_d}{\operatorname{argmin}}\frac{1}{n}\sum_{i=1}^{n} \log \left(1-D_ {\bf \theta_d}(G_{\bf \theta_g}(z_i))\right)$ <br/><br/>
를 최소화하는 $\bf \theta_d$, $\bf \theta_g$를 반복적으로 찾아가면서 업데이트하면 쉽게 문제를 해결할 수 있다. 

### BEGAN 
- BEGAN을 이해하기 전에 Wasserstein-디스턴스를 먼저 이해해야 한다. Wasserstein-디스턴스는 두 분포함수 $F_{X}(x)$와 $G_{Y}(x)$가 얼마나 유사한지를 측정하는 함수이다. 여기에서 분포함수 $F_X(x)$란 확률변수 $X$가 정의되었을 경우 $F_X(x)=P(X\leq x)$를 만족하는 $\mathbb{R} \rightarrow [0,1]$인 함수이다. 참고로 임의의 확률변수 $X$에 대한 분포함수는 항상 존재한다(확률분포함수는 항상 존재하지는 않음).  

- 각설하고 $X \sim N(0,1)$이고 $Y \sim N(300,5)$이라고 하자. 확률변수 $X$와 $Y$에 의해서 유도된 분포함수를 $F_X(x)$, $F_Y(y)$라고 하자. 두분포함수 $F_X(x)$, $F_Y(y)$의 와썰스테인-디스턴스는 아래와 같이 정의된다. 
\begin{align}
W(F_X(x),F_Y(y)):= \inf_ {F_{XY}} E_{F_{XY}}\|X-Y\|.  
\end{align}
여기에서 $F_{X,Y}(x,y)$는 확률벡터 $c(X,Y)$의 결합확률 밀도함수이다. $F_{X,Y}(x,y)$는 다양한 형태를 가질수 있다. 왜냐하면 $c(X,Y)$의 분포가 다양한 모양을 가질 수 있기 때문이다. 만약에 $X$와 $Y$가 독립이면 
\begin{align}
c(X,Y) \sim N\left(\begin{bmatrix} 0 \\\\ 300 \end{bmatrix}, \begin{bmatrix}1 & 0 \\\\ 0 & 25 \end{bmatrix} \right)
\end{align}
이 될 것이다. 독립이 아니면 $\rho$값에 따라서 아래와 같은 형태를 가질것이다. 
\begin{align}
c(X,Y) \sim N\left(\begin{bmatrix} 0 \\\\ 300 \end{bmatrix}, \begin{bmatrix}1 & 5\rho \\\\ 5\rho & 25 \end{bmatrix} \right)
\end{align}
따라서 $

