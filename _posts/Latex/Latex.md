# Latex

## 参考文档

https://katex.org/docs/supported.html

## 简介



## 语法



## 基本用法

### markdown插入Latex公式

```Plain
$$$$
```

### 矩阵

- matrix 

```LaTeX
\begin{bmatrix}
1&2\\
3&4\\
\end{bmatrix}
```

$$\begin{bmatrix} 1&2\\ 3&4\\ \end{bmatrix}$$

- Pmatrix

```LaTeX
\begin{pmatrix}
1&2\\
3&4\\
\end{pmatrix}
```

$$\begin{pmatrix} 1&2\\ 3&4\\ \end{pmatrix}$$

- Vmatrix

```LaTeX
\begin{vmatrix}
1&2\\
3&4\\
\end{vmatrix}
```

$$\begin{vmatrix} 1&2\\ 3&4\\ \end{vmatrix}$$

- Bmatrix

```LaTeX
\begin{Bmatrix}
1&2\\
3&4\\
\end{Bmatrix}
```

$$\begin{Bmatrix} 1&2\\ 3&4\\ \end{Bmatrix}$$

### 公式

```LaTeX
\begin{equation}
e = mc^2
\end{equation}
```

$$\begin{equation} e = mc^2 \end{equation}$$

```LaTeX
\tag{hi} x+y^{2x}
```

$$\tag{hi} x+y^{2x}$$

### 方程组

```LaTeX
\begin{cases}
a_1x+b_1y+c_1z=d_1\\
a_2x+b_2y+c_2z=d_2\\
a_3x+b_3y+c_3z=d_3
\end{cases}
```

$$\begin{cases} a_1x+b_1y+c_1z=d_1\\ a_2x+b_2y+c_2z=d_2\\ a_3x+b_3y+c_3z=d_3 \end{cases}$$

```LaTeX
x = \begin{cases}
a &\text{if } b \\
c &\text{if } d\\
\end{cases}
```

$$x = \begin{cases} a &\text{if } b \\ c &\text{if } d\\ \end{cases}$$

### 求和

```LaTeX
\sum_{
\begin{subarray}{l}
   i\in\Lambda\\
   0<j<n
\end{subarray}}
```

$$\sum_{ \begin{subarray}{l}   i\in\Lambda\\   0

```LaTeX
\sum_{\substack{0<i<m\\0<j<n}}
```

$$\sum_{\substack{0

### 字符



- *Θ* `\varTheta`
- *Σ* `\varSigma`
- *Π* `\varPi`
- *Φ* `\varPhi`
- *ϕ* `\phi`
- *ε* `\varepsilon`
- *φ* `\varphi`
- *α* `\alpha`
- *β* `\beta`
- *γ* `\gamma`
- *δ* `\delta`
- *θ* `\theta`
- *μ* `\mu`
- *π* `\pi`
- R `\Reals`

1. ### 常用符号

- $$a'$$: `a'`
- $$\tilde{a}$$: `\tilde{a}`
- $$\bar{y}$$: `\bar{y}`

### 上下标

- $$x_n$$ `x_n`
- $$e^x$$ `e^x`
- $$_u^o$$`_u^o`
- 连分数
- 对齐方程