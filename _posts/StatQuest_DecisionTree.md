
Decision Tree의 분류(Classification)결과 Categories or Numeric 모두 가능하다.
    - Y=남자 or Y=여자
    - $150<=Y<=180$, $Y<150$


1. 서로 다른 노드에서 같은 변수에의해 여러번 사용되어질 수 있는데, 이때 각각의 경우에서 사용되는 Cutoff는 다를 수 있다.
    - 어떤 하나의 노드에서 $X>=100$을 사용했다면, 다른 노드에서는 $X>120$과 같이 다른 Cutoff가 사용될 수 있다.
2. 사용되는 변수의 순서가 각각의 가지에서 다를 수 있다.
    - 왼쪽의 가지에서는 $X_1$이 먼저 분할기준으로 사용되고, $X_2$가 2번째로 사용되었다면,
    - 오른쪽의 가지에서는 $X_2$가 먼저 분할기준으로 사용되고, $X_1$가 2번째로 사용될 수 있다.
3. 당연한 내용이지만, 최종 분류 결과, 즉 Leaf Node들에서의 결과는 서로 다를 필요가 없습니다. (즉, 같을 수 있습니다.)
4. Decision Tree는 매우 직관적(Intuitive)이어서 이해하기 쉽습니다.

Root Node에서 Internal Node(그냥 Node라고도 함)로 Leaf Node(Terminal Node라고도 함)로 트리를 만들어 나간다.

Heart Disease Dataset을 가지고 Decision Tree를 만들어 보자.
데이터셋은 다음과 같이 구성되며, 모두 Yes/No의 Categorical Variables이다.

X변수 : Chest Pain, Good Blood Circulation, Blocked Arteries
Y변수 : Heart Disease

1. 가장먼저 Root Node에는 어떤 변수를 사용하면 좋을지 알아보기 위해, X변수 하나씩만을 가지고 Heart Disease 여부를 잘 구분하는지를 확인한다. 그 결과... (Heart Disease Yes/No)
(7분경 정리된 화면 캡쳐하기)
    - Chest Pain
        - True : 105/39
        - False : 34/125
    - Good Blood Circulation
        - True : 37/127
        - False : 100/33
    - Blocked Arteries (이 경우 결측으로 존재하는 데이터가 있지만 일단은 Skip하고 추후 고려하도록 한다.)
        - True : 92/31
        - False : 34/129

개수를 카운트해보면 각각의 경우에 데이터수가 다른 것으로 나타나는데, 이는 결측이 존재하여 그런 것이다.
**3개의 X변수 모두 100% Yes, 100% No로 분류하는 경우는 없다. 따라서 이 3가지 분류 모두 *Impure*라고 할 수 있다.**

이제 어떤 X변수에 의한 구분이 가장 Best인지를 확인하기 위해 Impurity(불순도)를 계산하고 비교해야한다. Impurity를 계산하는 방법은 여러가지가 있지만, 가장 대표적인 **Gini**를 사용해본다. (명칭이 Gini인 이유는 지니계수라는 것을 논문으로 처음 수치화여 발표한 사람이 Corrado Gini라는 이탈리아사람이기 때문이다.)

Chest Pain의 Gini Impurity를 계산해보면
$$
\begin{align}
Right\,Node's\;\;Gini\,Impurity &= 1 - (the\,probability\,of\,Yes)^2 - (the\,probability\,of\,No)^2  \\
&= 1 - (\frac{105}{105+39})^2 - (\frac{39}{195+39})^2  \\
&= 0.395
\end{align}
$$
$$
\begin{align}
Left\,Node's\;\;Gini\,Impurity &= 1 - (the\,probability\,of\,Yes)^2 - (the\,probability\,of\,No)^2  \\
&= 1 - (\frac{34}{34+125})^2 - (\frac{125}{34+125})^2  \\
&= 0.336
\end{align}
$$

이렇게 Chest Pain의 2개 Child Node에 대하여 Gini Impurity를 계산해보았다. 이제 2개의 Impurity를 합한 Chest Pain의 Total Impurity를 계산해야하는데, Left Child Node의 데이터는 144건이고, Right Child Node의 데이터는 159건이다. 얼핏 생각하면 개수는 대충 비슷하게 분리된 것 같지만, 심각하게 불균형하게 분리된 경우도 존재할 수 있다. 그래서 위에서 계산된 Gini Impurity를 그냥 더하면 안되고, 각 Child Node에 속한 데이터 개수를 기반으로 가중평균(Weighted Average)을 계산해야 한다.
$$
\begin{align}
Total\;Gini\;Impurity\;for\;Chest\;Pain &= Weighted\;Average\;of\;Gini\;Impurities\;for\;the\;Leaf\;Nodes  \\
&= (\frac{144}{144+159})0.395 - (\frac{159}{144+159})0.336  \\
&= 0.364
\end{align}
$$

같은 방식으로 Good Blood Circulation, Blocked Arteries의 경우도 계산해보면 다음과 같다.
$$ 
\begin{align}
Gini\;Impurity\;for\;Chest\;Pain &= 0.364 \\
Gini\;Impurity\;for\;Good\;Blood\;Circulation &= 0.360 \\
Gini\;Impurity\;for\;Blocked\;Arteries &= 0.381 \\
\end{align}
$$

2. Good Blood Circulation의 Gini Impurity가 가장 낮으므로, 심장병(Heart Disease) 환자여부를 가장 잘 분류한다고 볼 수 있다. 따라서 Good Blood Circulation을 Decision Tree의 Root Node로 사용한다.
하지만 나누어진 Child Node에는 여전히 Impure 데이터들이 남아있으며, 이들을 Chest Pain, Blocked Arteries를 통해 분류해나가야 한다.
(11분경 화면)



