## MATLAB 绘图
> http://cda.psych.uiuc.edu/matlab_pdf/splines.pdf
### Knots
MATLAB 生成 clamped b-spline knots 的函数
```matlab
augknt([1, 1.5, 1.8, 2.6, 3], 4)
% t = [1, 1, 1, 1, 1.5, 1.8, 2.6, 3, 3, 3, 3];
```

### Basis Function
MATLAB 根据 采样频率计算 Basis Function 的值
> **spcol ( knots, degree+1, $\tau$ )**
> 其中，$\tau$ 是要采样的点列

```matlab
B = spcol([0, 0, 0, linspace(0,1,20), 1, 1, 1], 4, linspace(0,1,100)); 
plot(B);
```
![](https://i.stack.imgur.com/k13wV.png)

### B-Spline
生成 B-Spline 结构体，并<mark style="background: #ABF7F7A6;">画图</mark> 
```matlab
f = spmak(knots, ctrl_pts);
fnplt(f);			% plot
res = fnplt(f);		% get result
```

```matlab
% Evaluate a function
v = fnval(f,x);		% v = f(x)
```










