# Вариант 4-4

**Калягин Дмитрий, Аверьянова Мария**

**ИАД-25**

--------------------------------------------------------------

## Задача оптимизации
$$f(x) = \frac{p}{2} ||Ax - b||^2 + \sum_{i=1}^m \log ( 2 \cosh (u_i^\top (x - a))),$$


$$
a \in \R^n, b \in \R^m, A \in \R^{m \times n}, u_i \in \R^n, p \ge 0, m=\frac{n}{5} \\
x \in \R^n - переменная~оптимизации, ||\cdot|| - евклидова~норма
$$

--------------------------------------------------------------

## 1.0 Интерпретация

### 1.0.1 Параметры

| Символ | Смысл                |
| ------ | -------------------- |
| $x \in \R^n$ | вектор переменных (неизвестный) |
| $a \in \R^n$ | вектор-ориентир (известный) |
| $b \in \R^m$ | целевой вектор (известный) |
| $A \in \R^{m \times n}$ | матрица преобразований (известная) |
| $u_i \in \R^n$ | набор векторов-проекторов (известные) |
| $p \ge 0$ | коэффициент вклада |

---

### 1.0.2 Левая часть функции

$$
f_1(x) = \frac{p}{2}||Ax-b||^2
$$
Часть с квадратом евклидовой нормы
$$
||Ax-b||^2 = \sum_{i=1}^m (Ax-b)_i^2
$$
можно интерпретировать как сумму квадратов ошибок Squared Error (SE): 
$$
\sum_i (prediction_i - target_i)^2
$$

$\frac{p}{2}$ - коэффициент вклада SE в функцию потерь
- Если $p$ большой $\rightarrow$ важно совпадать с $b$
- Если $p$ маленький $\rightarrow$ можно отклоняться
- $\frac{1}{2}$ - вероятно, просто удобство для производной

---

### 1.0.3 Правая часть функции

$$
f_2(x) = \sum_{i=1}^m \log ( 2 \cosh (u_i^\top (x - a)))
$$

- Есть фиксированный вектор $a$
- Штрафуем отклонение $x$ от $a$

**Внутренняя часть выражения:**
$$
u_i^\top (x-a)
$$
\- это проекция $(x-a)$ на направление $u_i$. То есть штраф накладывается на разные направления.

Это число говорит насколько $x$ направлен вдоль $u$; если менять $x$ в направлении, перпендикулярном $u$,
это значение не меняется.

**Внешняя часть выражения:**
$$
\log (2 \cosh(t))
$$
где
$$
\cosh(t) = \frac{e^t + e^{-t}}{2}
$$ 
\- гиперболический косинус.

Поведение $\log (2 \cosh(t))$ около $t=0$:

$$
\cosh (t) \approx 1 + t^2 / 2, \\ \log (2 \cosh (t)) \approx \log (2 + t^2) \approx \frac {t^2}{2} + \mathrm{const}
$$
То есть функция ведёт себя как квадратичная L2 регуляризация.

Поведение $\log (2 \cosh(t))$ около $t=\infty$:

$$
\cosh (t) \approx \frac {e^{|t|}}{2}, \\ 
\log (2 \cosh (t)) \approx |t|
$$

То есть далеко от нуля функция ведет себя как линейная L1 регуляризация.

Отсюда следует:
- если $x$ близко к $a$ вдоль $u_i \rightarrow$ квадратичный штраф
- если далеко $\rightarrow$ линейный штраф

То есть мы хотим чтобы $x$ был близко к $a$, но не настолько жестко как при L2.

### 1.0.4 Итоговая интепретация

$$
f(x) =
\underbrace{\frac{p}{2}||Ax-b||^2}_{data\ fitting}
+
\underbrace{\sum_i \log(2\cosh(u_i^\top(x-a)))}_{regularization}
$$

То есть функция означает:
- $Ax \approx b$ - чтобы преобразованный матричным оператором $x$ стремился к $b$
- но чтобы $x$ не слишком уходил от $a$

--------------------------------------------------------------

## 1.1 Проверка функции на выпуклость

Функция $f(x)$ называется выпуклой (convex), если:
$$f(\lambda x + (1 − \lambda) y) \leq \lambda f(x) + (1 − \lambda) f(y), \qquad 0 \leq \lambda \leq 1$$

Смысл:
- берём любые точки x и y
- берём точку между ними
- значение функции в середине не больше, чем середина значений

Или:
- если взять любые две точки на графике функции, то отрезок между ними лежит не ниже графика

Если функция convex:
- нет ложных минимумов
- $\nabla f = 0 \rightarrow$ глобальный минимум

Имеем функцию:

$$f(x) = \frac{p}{2} ||Ax - b||^2 + \sum_{i=1}^m \log ( 2 \cosh (u_i^\top (x - a))),$$


$$a \in \R^n, b \in \R^m, A \in \R^{m \times n}, u_i \in \R^n, p \ge 0, m=\frac{n}{5}.$$


### 1.1.1 Частные производные и градиент

Частная производная: 
$\frac {\partial f}{\partial x_i}$
показывает, как меняется $f$ при изменении только $x_i$.

Градиент - это вектор всех частных производных:
$\nabla f(x) = \left(\frac{\partial f}{\partial x_1}, \dots, \frac{\partial f}{\partial x_n}\right)$

### 2. Вторые производные и гессиан

В одной переменной: $f''(x)$ показывает кривизну графика функции

В нескольких переменных берём все вторые производные. Матрица вторых производных называется **гессиан**.

$\nabla^2 f(x) = \left[ \frac{\partial^2 f}{\partial x_i \partial x_j} \right]$

### 3. Связь с выпуклостью

В одной переменной: если $f''(x) \ge 0$ то функция выпуклая (изгиб вверх $\rightarrow$ convex).

В нескольких переменных аналог: если $v^T \nabla^2 f(x) v \ge 0, \quad \forall v$ или $H = \nabla^2 f(x) \succeq 0$

Это означает: гессиан положительно полуопределён.


### 1.1.2 Теоремы

1. Сумма

    $
    \left \{ \begin{array}{l}
            f - \mathrm{convex} &\\
            g - \mathrm{convex}
    \end{array} \right.
    \Longrightarrow (f + g) - \mathrm{convex}
    $

2. Композиция с линейной функцией

    $
    \left \{ \begin{array}{l}
            \varphi(t) - \mathrm{convex} &\\
            t(x) = a^\top x + b - \mathrm{linear}
    \end{array} \right.
    \Longrightarrow \varphi(t(x)) - \mathrm{convex}
    $

### 1.1.3 Проверка функции на выпуклость

**Первое слагаемое $f(x)$**

$$
||Ax-b||^2
$$

Запишем через скалярное произведение (по определению нормы):

$$
f(x) = (Ax-b)^\top (Ax-b) = x^\top A^\top A x - 2 b^\top A x + b^\top b
$$

Теперь найдём **градиент**:
$$
\nabla (x^\top Bx) = (B + B^\top)x \\
или \\
\nabla (x^\top Bx) = 2Bx, \quad B - \mathrm{symmetric}
$$
т.к. $B = A^\top A$ - симметричная, то
$$
\nabla (x^\top A^\top Ax) = 2 A^\top Ax
$$

Следующее слагаемое
$$
- 2 b^\top A x = -2 (A^\top b)^\top x
$$
т.к. $\nabla (c^\top x) = c$, то
$$
\nabla (-2 (A^\top b)^\top x) = -2 A^\top b
$$

В итоге получаем:
$$
\nabla f(x) = 2 A^\top A x - 2 A^\top b
$$

Теперь **гессиан**:

$$
\nabla^2 f(x) = 2 A^\top A
$$

Получаем:
$$
v^\top A^\top A v = (Av)^\top (Av) = |Av|^2 \ge 0 \\
\quad \rightarrow \quad \\
\nabla^2 f(x) \succeq 0 \\
\quad \rightarrow \quad \\
f(x) = ||Ax-b||^2 - \mathrm{convex}
$$

При $p \ge 0$ получаем что:
$$
\frac {p}{2} ||Ax-b||^2 - \mathrm{convex}
$$

**Второе слагаемое $f(x)$**

Берём функцию одной переменной:
$$
g(t) = \log(2\cosh t)
$$

Производные:

$$
g'(t) = \tanh(t) \\
g''(t) = \frac {1}{\cosh^2(t)}
$$

т.к. $\cosh^2(t) \ge 0$, то
$$
g''(t) \ge 0 \quad \rightarrow \quad g - \mathrm{convex}
$$

Имеем $t(x)$ - линейная функция:
$$
t(x) = u_i^\top(x-a)
$$ 
по теореме о композиции получаем 
$$
\log(2\cosh(u_i^\top(x-a))) - \mathrm{convex}
$$
т.к. сумма выпуклых функций - выпуклая, то 
$$
\sum_i \log(2\cosh(u_i^\top(x-a))) - \mathrm{convex}
$$

**Сумма слагаемых $f(x)$**
$$
\left \{ \begin{array}{l}
        \frac {p}{2} ||Ax-b||^2 - \mathrm{convex} &\\
        \sum_i \log(2\cosh(u_i^\top(x-a))) - \mathrm{convex}
\end{array} \right.
\Longrightarrow f(x) = \frac{p}{2} ||Ax - b||^2 + \sum_{i=1}^m \log ( 2 \cosh (u_i^\top (x - a))) - \mathrm{convex} \quad
$$
$\square$


## 1.2 Градиент и гессиан

### 1.2.1 Градиент
Берём производные по x для каждой части.

1. Квадратичная часть

    $f_1(x) = \frac{p}{2}||Ax-b||^2$

    Градиент:

    $\nabla f_1(x) = p A^\top (Ax-b)$

2. Log-cosh часть

    $f_2(x) = \sum_{i=1}^{m} \log(2\cosh(u_i^\top(x-a)))$

    * Пусть $t_i = u_i^\top(x-a)$
    * Производная: $\frac{d}{dt} \log(2\cosh t) = \tanh(t)$
    * Градиент по x:

    $\nabla \phi_i(x) = \tanh(t_i) u_i = \tanh(u_i^\top(x-a)) u_i$

    * Суммируем по i:

    $\nabla f_2(x) = \sum_{i=1}^{m} \tanh(u_i^\top(x-a)) u_i$

3. Градиент всей функции
    $$
    \boxed{
    \nabla f(x) = p A^\top (Ax-b) + \sum_{i=1}^{m} \tanh(u_i^\top(x-a)) u_i
    }
    $$

### 1.2.2 Гессиан

1. Квадратичная часть

    $\nabla^2 f_1(x) = p A^\top A$

2. Log-cosh часть

    * Для одной функции $\phi_i(x) = \log(2\cosh(u_i^\top(x-a)))$:

    $\nabla^2 \phi_i(x) = g''(t_i) , u_i u_i^\top = \frac{1}{\cosh^2(u_i^\top(x-a))} , u_i u_i^\top$

    * Суммируем по i:

    $\nabla^2 f_2(x) = \sum_{i=1}^{m} \frac{1}{\cosh^2(u_i^\top(x-a))} , u_i u_i^\top$

3. Гессиан всей функции
    $$
    \boxed{
    \nabla^2 f(x) = p A^\top A + \sum_{i=1}^{m} \frac{1}{\cosh^2(u_i^\top(x-a))} u_i u_i^\top
    }
    $$

--------------------------------------------------------------

## 1.3 Оценка выпуклости

### 1.3.1 Липшицевость градиента

Градиент Липшицев с константой L, если:

$$||\nabla f(x) - \nabla f(y)|| \le L ||x-y|| \quad \forall x,y$$

Смысл:
- градиент не меняется слишком резко
- функция не слишком круто изгибается
- L - это максимальная крутизна

Разделим функцию градиента на две части: 
$$\nabla f(x) = p A^\top (Ax-b) + \sum_{i=1}^{m} \tanh(u_i^\top(x-a)) u_i = g_1(x) + g_2(x)$$
$$g_1(x) = p A^\top (Ax-b)$$
$$g_2(x) = \sum_{i=1}^{m} \tanh(u_i^\top(x-a)) u_i$$

**Липшицевость $g_1$:**

$$||g_1(x) - g_2(x)|| = ||(p A^\top (Ax-b)) - (p A^\top (Ay-b))||$$

$$= ||(p A^\top (Ax - Ay))|| = ||(p A^\top A (x - y))||$$
$$\overset {||Bv|| \le ||B|| \, ||v||}{=} ||(p A^\top A)|| \, ||(x - y)|| \longrightarrow L_1 = ||(p A^\top A)||$$

Для симметричной матрицы $||B|| = \lambda_{max}(B), \quad A^\top A - \mathrm{symmetric}$, поэтому $L_1 = p \lambda_{max} (A^\top A)$


**Липшицевость $g_2$:**

$$
g_2(x) - g_2(y) = \sum_i \Big( \tanh(u_i^\top(x-a)) - \tanh( u_i^\top (y-a)) \Big) u_i \textcircled{=}
$$


Обозначим
$$
t_i(x) = u_i^\top(x-a)
$$

тогда

$$
\textcircled{=} \sum_i ( \tanh(t_i(x)) - \tanh(t_i(y))) u_i
$$


Норма

$$
|| \sum_i (\tanh(t_i(x)) - \tanh(t_i(y))) u_i || \overset{нер-во \, треугольника}{\le} \sum_i || \tanh(t_i(x))-\tanh(t_i(y)) || \, ||u_i||
$$

$\tanh(x)'$ - Липшицевая функция с константой 1, значит

$$
||\tanh(x) - \tanh(y)|| \le ||x - y||
$$

Применяя к нашему случаю:

$$
||\tanh(t_i(x)) - \tanh(t_i(y))|| \le ||t_i(x) - t_i(y)||
$$

Поставляем $t_i(x) = u_i^\top(x-a)$
$$
||t_i(x) - t_i(y)|| = ||u_i^\top(x-y)|| \le ||u_i|| \, ||x - y||
$$

Подставляем в сумму
$$
||g_2(x) - g_2(y)|| \le \sum_i ||u_i|| \, ||x-y|| \, ||u_i|| = \sum_i ||u_i||^2 \, ||x-y|| \overset{\sum_i ||u_i||^2 = \mathrm{trace}(U^\top U) \ge \lambda_{max}(U^\top U)}{\le} ||U^\top U|| \, ||x - y|| = \lambda_{max}(U^\top U) \, ||x - y||
$$

$$
\longrightarrow L_2 = \lambda_{max}(U^\top U)
$$


**Суммарная оценка $L$:**

$$
L_1 = p \lambda_{max} (A^\top A) \\
L_2 = \lambda_{max}(U^\top U)
$$
$$
L = L_1 + L_2 \\
L = p \lambda_{max} (A^\top A) + \lambda_{max}(U^\top U)
$$

### 1.3.2 Коэффициент сильной выпуклости

Функция является сильно выпуклой, если

$$
\nabla^2 f(x) \succeq \mu I, \quad \forall x
$$

Гессиан уже был найден:

$$
\nabla^2 f(x) = p A^\top A + \sum_{i=1}^{m} \frac{1}{\cosh^2(u_i^\top(x-a))} u_i u_i^\top
$$

Для правой части

$$
\left \{ \begin{array}{l}
u_i u_i^\top \succeq 0 \\
\mathrm{coeff} \ge 0
\end{array} \right.
\longrightarrow \sum_{i=1}^{m} \frac{1}{\cosh^2(u_i^\top(x-a))} u_i u_i^\top \succeq 0
$$

Тогда
$$
\nabla^2 f(x) \succeq p A^\top A \overset{\big[ A^\top A \succeq \lambda_{\min}(A^\top A)I \big]}{\succeq} p \lambda_{\min}(A^\top A)I
$$
$$
\longrightarrow \mu = p \lambda_{\min}(A^\top A)
$$

Заметим, что по условию $m = \frac {n}{5}$, то есть $m < n \rightarrow$ матрица $A^\top A$ наверняка имеет неполный ранг, значит найдется такой вектор $v \ne 0$ такое, что $Av=0$, тогда

$$
Av=0 \rightarrow \lambda_{min}=0 \rightarrow \mu = 0
$$

Это значит, что $f(x)$ выпуклая, но не обязательно сильно выпуклая.

--------------------------------------------------------------

## 1.4 Условие оптимальности

Для минимизации функции:
$$
\nabla f(x^*) = 0
$$
То есть нужно, чтобы градиент в точке $x^*$ был нулевым вектором.

Подставляем в градиент и получаем:

$$
p A^\top (A x^∗ − b) + \sum_{i=1}^{m} \tanh (u_i^\top ​(x^∗ − a)) u_i​ = 0
$$

Точка $x^*$ существует и уникальна, если функция сильно выпуклая ($\mu > 0$)