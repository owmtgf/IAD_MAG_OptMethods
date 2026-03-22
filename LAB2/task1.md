# Вариант 4-4

**Калягин Дмитрий, Аверьянова Мария**

**ИАД-25**

--------------------------------------------------------------

## Задача оптимизации
$$f(x) = \frac{p}{2} \Vert Ax - b \Vert ^2 + \sum_{i=1}^m \log \bigg( 2 \cosh \big(u_i^\top (x - a)\big)\bigg),$$


$$
a \in \R^n, b \in \R^m, A \in \R^{m \times n}, u_i \in \R^n, p \ge 0, m=\frac{n}{5} \\
x \in \R^n - переменная~оптимизации, \Vert \cdot \Vert - евклидова~норма
$$

**Введенные ограничения:**
$$
q^\top x \le \eta
$$

--------------------------------------------------------------

## 1. Проверка множества допустимых точек

Множество $C \subset \R^n$ называется выпуклым, если $\forall  x_1, x_2 \in C$ и $\forall \lambda \in [0, 1]$:

$$
x_{\lambda} = \lambda x_1 + (1 - \lambda)x_2 \in C
$$

То есть, если взять любые две точки и рассматриваемого множеста, то весь отрезок между ними так же лежит в этом множестве.

---

Учитывая новое ограничение, нужно проверить:
$$
q^\top x_{\lambda} \le \eta
$$

Пусть
$$
q^\top x_1 \le \eta \\
q^\top x_2 \le \eta
$$

Возьмем
$$
x_{\lambda} = \lambda x_1 + (1 - \lambda) x_2
$$

Тогда
$$
q^\top x_{\lambda} = q^\top \bigg( \lambda x_1 + (1 - \lambda) x_2 \bigg) \\
= \lambda q^\top x_1 + (1 - \lambda) q^\top x_2
$$

Так как 
$$
q^\top x_1 \le \eta, \quad q^\top x_2 \le \eta
$$

Можем оценить полученное выражение
$$
\lambda q^\top x_1 + (1 - \lambda) q^\top x_2 \le \lambda \eta + (1 - \lambda) \eta \\
= \eta \quad \Longrightarrow \quad q^\top x_{\lambda} \le \eta
$$
$\square$

Следовательно множество допустимых точек:
$$
C = \{ x \in \R^n \, | \, q^\top x \le \eta \}
$$
является выпуклым множеством.
