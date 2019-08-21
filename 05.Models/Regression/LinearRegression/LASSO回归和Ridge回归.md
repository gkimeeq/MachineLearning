在[线性回归](https://www.cnblogs.com/Ooman/p/11343779.html)中讲述了原理，为了防止过拟合经常会加入正则化项。常用的正则化有L1正则化和L2正则化。

**1.LASSO回归**

加入L1正则化项的线性回归就叫LASSO回归。L1正则化项即是参数的L1范数，通俗点说，就是参数向量各个分量取绝对值的加和，即，对于$\theta=(\theta_0, \theta_1, \cdots, \theta_n)^T$参数向量，L1正则化项为：

$$
\left \| \theta \right \|_1 = \sum_{j=0}^n | \theta_j |
$$

通常会加入一个系数$\lambda$来调节正则化项的权重，因此LASSO回归的目标函数（损失函数）为：

$$
J(\theta) = \frac{1}{2}\sum_{i=1}^m(h(x^{(i)})-y^{(i)})^2 + \lambda \sum_{j=0}^n | \theta_j | = \frac{1}{2}\left(X\theta-Y\right)^T\left(X\theta-Y\right) + \lambda\left \| \theta \right \|_1
$$

LASSOS回归可以使得一些特征的系数为零（即某些$\theta_j$为零），即得到稀疏解。

由于$|\theta_j|$求不了导，所以在实际应用中，可以寻求近似解。对于函数$f(x;\alpha) = x + \frac{1}{\alpha}\log(1+\exp(-\alpha x)), x\ge 0$，绝对值可以近似表示为：

$$
\begin{aligned}
|x| &\approx f(x;\alpha) + f(-x;\alpha)\\
& = x + \frac{1}{\alpha}\log(1+\exp(-\alpha x)) - x + \frac{1}{\alpha}\log(1+\exp(\alpha x))\\
& = \frac{1}{\alpha}\left(\log(1+\exp(-\alpha x)) + \log(1 + \exp(\alpha x))\right)\\
\end{aligned}
$$

因此，$|x|$的梯度和二阶导可以通过上面的近似表达式求得：

$$
\begin{aligned}
\nabla |x| &\approx \frac{1}{\alpha}\left( \frac{-\alpha \exp(-\alpha x)}{1+\exp(-\alpha x)} + \frac{\alpha \exp(\alpha x)}{1+\exp(\alpha x)} \right)\\
&=\frac{\exp(\alpha x)}{1+\exp(\alpha x)} - \frac{\exp(-\alpha x)}{1+\exp(-\alpha x)}\\
&=\left( 1 - \frac{1}{1+\exp(\alpha x)} \right) - \left( 1 - \frac{1}{1+\exp(-\alpha x)} \right)\\
& = \frac{1}{1+\exp(-\alpha x)} - \frac{1}{1+\exp(\alpha x)}\\
\nabla^2 |x| &\approx \nabla( \frac{1}{1+\exp(-\alpha x)} - \frac{1}{1+\exp(\alpha x)})\\
&=\frac{\alpha\exp(-\alpha x)}{(1+\exp(-\alpha x))^2} +  \frac{\alpha \exp(\alpha x)}{(1+\exp(\alpha x))^2}\\
&=\frac{\alpha\frac{1}{\exp(\alpha x)}}{(1+\frac{1}{\exp(\alpha x)})^2} + \frac{\alpha \exp(\alpha x)}{(1+\exp(\alpha x))^2}\\
&=\frac{\alpha\exp(\alpha x)}{(1+\exp(\alpha x))^2} +  \frac{\alpha \exp(\alpha x)}{(1+\exp(\alpha x))^2}\\
&=\frac{2\alpha \exp(\alpha x)}{(1+\exp(\alpha x))^2}
\end{aligned}
$$

对于一般的问题，$\alpha$一般取$10^6$。

利用近似梯度，可得到目标函数的一次导数为：

$$
\frac{\partial J(\theta)}{\partial \theta} \approx X^TXθ−X^TY + \frac{\lambda}{1+\exp(-\alpha \theta)} - \frac{\lambda}{1+\exp(\alpha \theta)}
$$

显然令导数为零也不好求得$\theta$的值，所以一般都是用坐标轴下降法和最小角回归法来求解。

**2.Ridge回归**

Ridge回归就是加入L2正则化项的线性回归。L2正则化项即是参数的L2范数，对于$\theta=(\theta_0, \theta_1, \cdots, \theta_n)^T$参数向量，L2正则化项为：

$$
\left \| \theta \right \|_2^2 = \sum_{j=0}^n \theta_j^2
$$

Ridge回归的目标函数（损失函数）为：

$$
J(\theta) = \frac{1}{2}\sum_{i=1}^m(h(x^{(i)})-y^{(i)})^2 + \lambda \sum_{j=0}^n \theta_j^2 = \frac{1}{2}\left(X\theta-Y\right)^T\left(X\theta-Y\right) + \frac{1}{2}\lambda\left \| \theta \right \|_2^2
$$

式中第二个$\frac{1]{2}$是为了后面计算方便加入。Ridge回归不抛弃任何特征的系数（即$\theta_j$不为零），使得回归系数变小，相对比较稳定，但与LASSO回归比则保留的特征比较多。

Ridge回归的目标函数的一次导为：

$$
\begin{aligned}
\frac{\partial J(\theta)}{\partial \theta} &= \frac{\partial}{\partial \theta}\left(\frac{1}{2}\left(X\theta-Y\right)^T\left(X\theta-Y\right) + \frac{1}{2}\lambda\left \| \theta \right \|_2^2\right)\\
&=X^TXθ−X^TY +  \frac{\partial}{\partial \theta}\left( \frac{1}{2}\lambda\left \| \theta \right \|_2^2 \right)\\
&=X^TXθ−X^TY +  \frac{\partial}{\partial \theta}\left( \frac{1}{2}\lambda \theta^T\theta \right)\\
&=X^TXθ−X^TY +  \lambda \theta
\end{aligned}
$$

令导数为零，可求得$\theta$的值：

$$
\theta = (X^TX+\lambda I)^{-1}X^TY
$$

可见得到的结果与[线性回归](https://www.cnblogs.com/Ooman/p/11343779.html)里描述的解析解中加入扰动结果一致。
