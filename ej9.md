**Realizar un test Chi2 a la distribución empírica implementada en el Ej 6, y analizar el resultado indicando si la distribución puede o no ser aceptada. Considerar un nivel de significación del 1%. En caso que la distribución de probabilidades no pase el test con un nivel de significación del 1% volver a realizarlo con un nivel de significación del 5%.**

En primer lugar, generamos una muestra a partir de la distribucción definida en el ejercicio 6.
```python
from scipy import stats as stats
import matplotlib.mlab as mlab
n = 100000 #tam de la muestra
start = (100017 + 101055 + 97112 + 95457) / 4
mult = 1013904223
inc = 1664525
m = 2**32

ej1_values = [n for n in glc_uniforme(start, mult, inc, m, n)]
discrete_values = [transformar(num) for num in ej1_values]
```
De esta muestra medimos la dispersión de las ocurrencias observadas ($N_i$).
```python
values = ['A', 'B', 'C', 'D']
frecs = []
for i in range(4):
  frecs.append(0)
  for value in discrete_values:
    if value == values[i]:
      frecs[i] += 1
```
Además definimos cuáles eran las ocurrencuas esperadas de la distribución teórica ($np_i$).

```python
expected_values =  [0.1*100000,0.5*100000,0.3*100000,0.1*100000]
```
Luego calculamos el estadístico $D^2$ y junto con el nivel de significatividad (`ns`) determinamos si se rechaza la hipótesis nula o no.

```python
def chiCuadrTest(f_obs, f_exp, ns):
  (s,p) = stats.chisquare(f_obs, f_exp = f_exp)
  #print(f"Con un nivel de significacion de {ns} ", end='')
  print(f"p-value = {p}")
  if p < ns:
    print(f"{p} < {ns}")
    print("Rechazo H0")
  else:
    print(f"{p} > {ns}")
    print("Aceto H0")
ns = 0.01
chiCuadrTest(frecs, expected_values, ns)
ns = 0.05
chiCuadrTest(frecs, expected_values, ns)
```
salida:
```python
p-value = 4.011327428737019e-06
4.011327428737019e-06 < 0.01
Rechazo H0

p-value = 4.011327428737019e-06
4.011327428737019e-06 < 0.05
Rechazo H0
```

Como se puede ver en la salida, p-value toma un valor de $4.011327428737019 * 10^{-6}$  que es menor que el nivel de significación tanto para $0.01$ como para $0.05$ por ende en ambos casos rechazo $H_0$
