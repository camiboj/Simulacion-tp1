## Ejercicio 3
**Aplicando el algoritmo de Aceptación y rechazo se pide:**

**a) Generar 100.000 número aleatorios con distribución Normal de media 15 y desvío estándar 3**
Para cumplir el objetivo dado generamos una normal estandard $$F_X(t)$$ que luego desplazamos y expandimos para obtener una normal de media 15 y desvío 3. El planteo fue generar los valores positivos de $$F_X(t)$$ y luego multiplicar la mitad de ellos por $$-1$$, obteniendo la generación del resto de la curva. Para ello proponemos una distribución exponencial $$F_Y(t)$$ con $$\lambda = 1$$, mientras que queremos generar $$F_X(t)$$ que es la distribución normal estándar tal que:
$$F_Y(t) = e^{-y} \mathbb{1} \{0 < t < \infty\}$$
$$F_X(t) = \frac{e^{\frac{-x^2}{2}}}{\sqrt{2\pi}} \mathbb{1} \{0 < t < \infty\}$$

1. Econtramos una cota superior $$c$$, tal que, $$c >= F_X(t)/F_Y(t)$$ y llegamos a $$c = 0.65$$
2. Luego, sabiendo el valor de $$c$$, generamos un número al azar de $$F_Y(t)$$ con $$t$$ generado por `exp_glc(lambda)` que lo llamaremos $$n$$
3. Generamos un número al azar entre $$0$$ y $$1$$ que lo llamaremos $$r$$
4. Si $$r <= p(t) = \frac{F_X(a)}{c*F_Y(a)}$$ entonces a es el valor generado, sino volvemos a 2
```python
import random
import math
import numpy as np

N = 100000
c = 0.65

def exp_glc(lamda=1):
  return [-math.log(np.random.rand()) / lamda for n in range(N*2)]
  
def fy(y):
  return math.exp(-y)

def fx(x):
  return math.exp(-(x)**2/2) / math.sqrt(2*math.pi)

def p(t):
  return fx(t)/(c*fy(t))

# Paso 1: Generamos muestras de la variable exponencial Y, media 1
def acc_rej(mu, var):
  t = exp_glc()
  z = []
  malos = []
  for n in t:
    r = np.random.rand()
    if r < p(n): # acepto t(n) con prob. p(n)
      r2 = np.random.rand()
      sign = 1 if r2 < 0.5 else -1 # con prob 0.5 lo dejo positivo, sino neg
      z.append(sign * n * var + mu)
      
  return z

z = acc_rej(15, 3)
muestras = (N*2)
aceptadas = len(z)
# me quedo con los N que fueron pedidos
z = z[0:N]
```

**b) Realizar un histograma de frecuencias relativas con todos los valores obtenidos.**
```
valores = np.array(z)
rango = (valores.max() - valores.min()) / (1+np.log2(valores.shape[0])) 
cant_divisiones = (valores.max() - valores.min()) / rango
cant_divisiones = 100
plt.hist(valores, bins=int(cant_divisiones), color='#ff000055', density=True)

plt.show()
```
![Test espectral dos dimensiones](https://github.com/camiboj/Simulacion-tp1/blob/master/ej3b.png?raw=true)

**c) Comparar, en el mismo gráfico, el histograma realizado en el punto anterior con la función de densidad de probabilidad brindada por Matlab, Octave o Python**
```
import scipy.stats as sp
valores = np.array(z)
plt.hist(valores, bins=int(cant_divisiones), color='#ff000055', density=True)

normal_control_group_x = np.arange(5,25,0.1)
normal_control_group_y = sp.norm.pdf(normal_control_group_x,15,3)
plt.plot(normal_control_group_x, normal_control_group_y,color='#0000ff55')
plt.show()
```
![Test espectral dos dimensiones](https://github.com/camiboj/Simulacion-tp1/blob/master/ej3c.png?raw=true)

**d) Calcular la media y la varianza de la distribución obtenida y compararlos con los valores teóricos.**
```
u = np.mean(valores)
var = np.var(valores)
print(f"La muestra obtenida por aceptación y rechazo tiene una media: {u} y una varianza: {var}")
```

```
La muestra obtenida por aceptación y rechazo tiene una media: 14.99637036972024 y una varianza: 9.043158249854605
```
Por lo tanto podemos concluir que los valor son muy cercanos a los teróricos esperados (media $$15$$ y varianza $$9$$)