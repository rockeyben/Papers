# Neural Attack相关文献整理

## Problem Setting

给定$x$，找到$x^{'}$，使得$(x, x^{'})$之间尽量接近，同时分类器$C(.)$的输出$C(x) \ne C(x^{'})$

三种方式选定让神经网络预测错误的类$C(x^{'})$
- Average Case: 随机在别的类别中挑选
- Best Case: 对别的所有类别全部尝试一次，挑出最容易fake的
- Worst Case: 对别的所有类别全部尝试一次，挑出最难fake的

三种metric判断$(x, x^{'})$之间是否接近。$L_p$范数

- $L_0$ 距离：pixel值不相等的个数
- $L_2$ 距离: pixel值的MSE
- $L_{\infty}$ 距离: pixel值的最大改变量

