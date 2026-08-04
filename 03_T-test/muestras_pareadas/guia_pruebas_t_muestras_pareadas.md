# Guía de Referencia: Pruebas t de Dos Muestras Pareadas

## ¿Qué es?

La prueba t de muestras pareadas es una herramienta **estadística paramétrica** que determina si existe una diferencia significativa entre las medias de dos mediciones tomadas en los **mismos sujetos** (o en pares relacionados). En vez de comparar dos grupos independientes, compara las **diferencias** dentro de cada par usando la **distribución t de Student**.

**Pregunta central:** ¿La media de las diferencias entre las dos mediciones es significativamente distinta de cero?

**Qué NO hace:**
- No compara dos muestras independientes entre sí (para eso está la prueba t de dos muestras independientes)
- No asume que los datos individuales son normales (pero SÍ requiere que las **diferencias** sean normales)
- No reemplaza el juicio experto sobre si el tamaño del efecto es práctico
- **No prueban causalidad** — solo describen si hay diferencia estadísticamente significativa entre las dos mediciones

### La diferencia clave con la prueba t de muestras independientes

| Característica | Muestras Independientes | **Muestras Pareadas** |
|---|---|---|
| **Sujetos** | Dos grupos DISTINTOS | **Los MISMOS sujetos** (o pares relacionados) |
| **Mediciones** | Una por grupo | **Dos por sujeto** (antes/después, test/retest) |
| **Se analiza** | Diferencia entre medias de grupos | **Diferencias dentro de cada par** |
| **Normalidad de** | Cada grupo individualmente | **Las diferencias** (d = x₂ - x₁) |
| **Grados de libertad** | n₁ + n₂ - 2 (o Welch) | **n_pairs - 1** |
| **Potencia** | Menor | **Mayor** (si los pares están correlacionados) |

> **En la práctica real, la prueba pareada es MÁS POTENTE que la independiente cuando los pares están correlacionados.** Si medís lo mismo dos veces en los mismos sujetos, la variabilidad individual se cancela parcialmente, haciendo más fácil detectar una diferencia real. Si podés elegir entre ambos diseños, el pareado es más eficiente.

### Los tres escenarios típicos

- **Antes/Después (Before/After):** Se mide el mismo sujeto antes y después de una intervención
- **Test/Retest:** Se aplica la misma prueba dos veces para evaluar consistencia
- **Pares relacionados:** Se emparejan sujetos por características similares y se compara cada par

---

## Cuándo usarla

### Flujo de decisión

```
¿Tenés DOS mediciones en los MISMOS sujetos (o pares)?
│
│   ├── SÍ → Es un diseño pareado
│   │   │
│   │   ├── ¿Las DIFERENCIAS entre pares son normales?
│   │   │   ├── SÍ (o n grande por CLT) → Prueba t de MUESTRAS PAREADAS
│   │   │   └── NO y n chico → Considerar Prueba de Wilcoxon para muestras pareadas
│   │   │
│   │   ├── ¿Querés saber si HAY DIFERENCIA (sin dirección)?
│   │   │   └── Prueba BILATERAL (two-sided)
│   │   │
│   │   └── ¿Querés saber si MEJORÓ / EMPEORÓ (dirección)?
│   │       └── Prueba UNILATERAL (one-tailed)
│   │
│   └── NO → Son muestras independientes
│       └── Usá la prueba t de dos muestras independientes
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| Mismos sujetos medidos antes y después | **Prueba t pareada** |
| Dos grupos completamente distintos | Prueba t independiente |
| Diferencias normales, n ≥ 5 | **Prueba t pareada** |
| Diferencias no normales, n chico | Wilcoxon pareado |
| ¿Mejoró después de la intervención? | Unilateral pareado |
| ¿Cambiaron las mediciones? | Bilateral pareado |

### ¿Por qué importa paired vs independiente?

- **Pareada** controla la variabilidad individual: cada sujeto es su propio control, así que las diferencias entre sujetos no contaminan el resultado
- **Independiente** asume que los grupos son comparables en todo excepto la intervención
- **Si los pares están altamente correlacionados, la prueba pareada es MUCHO más potente** — la variabilidad dentro de cada par es menor que la variabilidad entre sujetos
- **Elegí el diseño ANTES de recolectar datos** — cambiar de pareado a independiente después es un error metodológico

---

## Comparación entre pruebas

### Comparación directa: pareada vs independiente

| Aspecto | **Muestras Pareadas** | Muestras Independientes |
|---|---|---|
| **Diseño** | Mismos sujetos, 2 mediciones | 2 grupos distintos |
| **Analiza** | Diferencias internas (d = x₂ - x₁) | Diferencia entre medias de grupos |
| **H₀** | μ_d = 0 (la media de diferencias es cero) | μ₁ = μ₂ (las medias de los grupos son iguales) |
| **H₁** | μ_d ≠ 0 (o μ_d > 0 / μ_d < 0) | μ₁ ≠ μ₂ (o μ₁ > μ₂ / μ₁ < μ₂) |
| **Grados de libertad** | n_pairs - 1 | n₁ + n₂ - 2 (o Welch) |
| **Normalidad de** | Las **diferencias** | Cada **grupo** por separado |
| **Potencia** | **Mayor** (si correlación alta) | Menor |
| **Tamaño de muestra** | n pares (más eficiente) | 2n total (menos eficiente) |
| **Uso típico** | Antes/después, pre/post | Dos tratamientos, dos grupos |

### Pareada vs una muestra vs Wilcoxon

| Característica | **Pareada** | Una Muestra | Wilcoxon Pareado |
|---|---|---|---|
| **Comparación** | Dos mediciones en mismos sujetos | Una media contra un valor teórico | Diferencias (no paramétrica) |
| **Normalidad requerida** | SÍ (de las diferencias) | SÍ (de los datos) | NO |
| **Distribución** | t de Student | t de Student | Rangos |
| **Potencia con datos normales** | Alta | Alta | Menor que t |
| **Robustez** | Media | Media | Alta |
| **Uso típico** | Antes/después | Comparar contra un estándar | Diferencias no normales |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → no hay evidencia de diferencia | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar el contexto y el tamaño del efecto | ⚠️ |
| p < 0.05 | Se rechaza H₀ → hay diferencia significativa | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de diferencia | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa cada tipo en el contexto pareado?

- **Unilateral**: Solo mirás una cola. Si la media de diferencias está en la dirección esperada (por ejemplo, positiva = mejoró), rechazás H₀. Es como apostar a que la intervención mejoró.
- **Bilateral**: Mirás ambas colas. Si la media de diferencias es suficientemente distinta de cero (para cualquier lado), rechazás H₀. Es como preguntar si cambió, sin importar la dirección.

---

## Matemáticas detrás de la prueba

### Estadístico t

#### Fórmula

$$t = \frac{\bar{x_d} - \mu_d}{s_d / \sqrt{n}}$$

Donde:
- $\bar{x_d}$ = media de las **diferencias** entre pares (d = x_after - x_before)
- $\mu_d$ = media de diferencias bajo H₀ (típicamente 0)
- $s_d$ = desviación estándar de las **diferencias**
- $n$ = número de pares
- $s_d / \sqrt{n}$ = error estándar de la media de diferencias

> **Nota clave:** La prueba pareada convierte el problema de "dos muestras" en "una muestra de diferencias". Calculamos d = x_after - x_before para cada par y luego aplicamos una prueba t de UNA MUESTRA sobre esas diferencias.

#### Grados de libertad

$$df = n_{pairs} - 1$$

Los grados de libertad son más simples que en muestras independientes:
- No necesitás ajuste de Welch
- df = número de pares - 1
- A mayor n de pares, más se parece a la distribución normal

#### Proceso

1. Definir la hipótesis nula (H₀: x̄_d = 0) y alternativa (H₁: x̄_d ≠ 0)
2. Calcular la diferencia para cada par: dᵢ = error_{i_{después}} - error_{i_{antes}}
3. Calcular la media de las diferencias ($\bar{x_d}$)
4. Calcular la desviación estándar de las diferencias ($s_d$)
5. Calcular el error estándar ($s_d / \sqrt{n}$)
6. Calcular t: cuántas desviaciones estándar se aleja $\bar{x_d}$ de 0
7. Comparar t con el valor crítico o calcular el p-valor

---

## Ejemplo principal: Modelo de predicción de precios de viviendas

### Contexto del problema

> Construimos un primer modelo de Machine Learning para predecir el precio de viviendas. Medimos el desempeño como:
>
> $$error = |y_{pred} - y|$$
>
> Construimos una versión mejorada usando técnicas de escalamiento de datos. **Queremos comparar los errores por vivienda antes y después del cambio para determinar si el nuevo modelo funciona mejor o no.**

### ¿Por qué es una prueba pareada?

Por cada una de las 1000 viviendas tenemos los errores de predicción **con uno y otro modelo**. Se trata de la misma muestra medida dos veces.

---

## Ejemplo paso a paso

### Paso 1: Definir el problema del negocio

> **El nuevo modelo: ¿genera mejores o peores predicciones del precio de las viviendas en comparación con el modelo anterior?**

### Paso 2: Redactar el problema como un problema de Ciencia de Datos

> **¿Existe una diferencia significativa en los errores de predicción antes y después de aplicar técnicas de escalamiento al modelo de Machine Learning?**

### Paso 3: Definir H₀ y H₁

Sean:

$$d_i = error_{i_{después}} - error_{i_{antes}}$$

la diferencia en los errores de predicción en el precio de la vivienda $i$ antes y después de implementar la técnica de escalamiento.

Y sea:

$$\bar{x_d} = \frac{\sum_i d_i}{n}$$

el promedio de dichas diferencias.

Entonces:

- **H₀**: no hay diferencias significativas en el promedio de diferencias de errores antes y después: **x̄_d = 0**
- **H₁**: hay diferencias significativas en el promedio de diferencias de errores antes y después: **x̄_d ≠ 0**

> **Nota:** Esta es una prueba **BILATERAL** (two-sided). No asumimos dirección antes de ver los datos.

### Paso 4: Definir α

Asumiremos un nivel de significancia **α = 0.05** (bilateral). Aún no dibujaremos la distribución t pues esta depende de los grados de libertad.

### Paso 5: Calcular potencia y tamaño de muestra

Usamos `TTestPower` de `statsmodels`, el módulo especialmente diseñado para pruebas t-test de una muestra o pareadas:

```python
from statsmodels.stats.power import TTestPower

# Definir tamaño del efecto, potencia de la prueba y alpha
d = 0.8
power = 0.9
alpha = 0.05

# Instancia de TTestPower (NO TTestIndPower)
analisis = TTestPower()

# Cálculo del tamaño de la muestra
n = analisis.solve_power(
    effect_size=d,
    alpha=alpha,
    power=power,
    alternative='two-sided'  # bilateral
)
print(f"Tamaño de la muestra: {n}")
# Tamaño de la muestra: 18.446225469774834
```

> **Necesitamos recolectar al menos 19 muestras pareadas** para tener una potencia de 0.9 y un tamaño del efecto esperado de 0.8, con un nivel de significancia de 0.05.

### Paso 6: Recolectar y preparar los datos

Supondremos que ya hemos recolectado los datos. Estos se encuentran en el dataset `errores_prediccion_antes_despues.csv`:

```python
import pandas as pd

datos = pd.read_csv('errores_prediccion_antes_despues.csv')
datos
```

| | errores_antes | errores_despues |
|---|---|---|
| 0 | 27483.57 | 23684.86 |
| 1 | 24308.68 | 21459.41 |
| 2 | 28238.44 | 27119.18 |
| 3 | 32615.15 | 32909.02 |
| 4 | 23829.23 | 21432.79 |
| ... | ... | ... |
| 999 | 27862.91 | 28352.72 |

Para la prueba pareada no se analizan los errores antes o después sino la **diferencia** entre ambos:

```python
datos["diferencia"] = datos["errores_despues"] - datos["errores_antes"]
datos
```

| | errores_antes | errores_despues | diferencia |
|---|---|---|---|
| 0 | 27483.57 | 23684.86 | -3798.71 |
| 1 | 24308.68 | 21459.41 | -2849.27 |
| 2 | 28238.44 | 27119.18 | -1119.26 |
| 3 | 32615.15 | 32909.02 | 293.87 |
| 4 | 23829.23 | 21432.79 | -2396.45 |
| ... | ... | ... | ... |
| 999 | 27862.91 | 28352.72 | 489.81 |

Calculemos las estadísticas de la columna de diferencias:

```python
import numpy as np

n = len(datos)
xd = np.mean(datos['diferencia'])
sd = np.std(datos['diferencia'])

print('Columna diferencias:')
print(f"\tTamaño de la muestra (n): {n}")
print(f"\tMedia (xd): {xd}")
print(f"\tDesviación estándar (sd): {sd}")
```

```
Columna diferencias:
	Tamaño de la muestra (n): 1000
	Media (xd): -1141.6724744983117
	Desviación estándar (sd): 1993.9110505892604
```

La media de las diferencias (después - antes) es **negativa**, lo que sugiere que el nuevo modelo tiene menores errores. Aunque esto tendremos que verificarlo estadísticamente.

### Paso 7: Verificar normalidad de las DIFERENCIAS

Antes de aplicar la prueba t, verificamos la normalidad de la columna `diferencia` (NO de los grupos individuales):

```python
from scipy.stats import shapiro

W1, p_shapiro = shapiro(datos['diferencia'])
print(f"p: {p_shapiro}")
# p: 0.7311929770378104
```

**p > 0.05**, por lo cual no descartamos la hipótesis nula y concluimos que **las diferencias tienen una distribución normal**. La prueba t de muestras pareadas es VÁLIDA.

### Paso 8: Aplicar la prueba estadística

Usamos el módulo `ttest` de la librería Pingouin. Introducimos las columnas después (`x`) y antes (`y`), y fijamos `paired=True`:

```python
from pingouin import ttest

resultados = ttest(
    x=datos['errores_despues'].values,
    y=datos['errores_antes'].values,  # El segundo arreglo es usualmente el de referencia
    paired=True,  # Prueba t-test pareada
    alternative='two-sided'  # Queremos saber si existen diferencias, sin importar la dirección
)
resultados
```

| | T | dof | alternative | p-val | CI95% | cohen-d | BF10 | power |
|---|---|---|---|---|---|---|---|---|
| T-test | -18.097496 | 999 | two-sided | 1.555944e-63 | [-1265.47, -1017.88] | 0.222383 | 8.091e+59 | 1.0 |

Veamos en detalle la estadística y el valor p:

```python
print(f"Estadística (t, muestras pareadas): {resultados['T'].values[0]}")
print(f"Valor p: {resultados['p-val'].values[0]}")
```

```
Estadística (t, muestras pareadas): -18.097496205142296
Valor p: 1.5559435639841368e-63
```

### Paso 9: Evaluar tamaño del efecto y potencia

Estos valores ya los obtuvimos con Pingouin:

```python
print(f"Tamaño del efecto actualizado: {resultados['cohen-d'].values[0]}")
print(f"Potencia de la prueba actualizada: {resultados['power'].values[0]}")
```

```
Tamaño del efecto actualizado: 0.22238300883198206
Potencia de la prueba actualizada: 0.9999997964762224
```

Según la escala de Cohen:
- 0.2 es un efecto pequeño
- 0.5 es un efecto mediano
- 0.8 es un efecto grande

El tamaño del efecto es **pequeño (d = 0.22)** pero la potencia es **prácticamente 1.0 (0.9999998)**.

### Paso 10: Conclusión

Con un valor p de $1.55 \times 10^{-63}$ (mucho menor que α = 0.05), **rechazamos la hipótesis nula** y nos inclinamos por H₁.

> **Existe una diferencia significativa en los errores de predicción antes y después de aplicar técnicas de escalamiento al modelo de Machine Learning. De hecho, después de aplicar las técnicas el error obtenido en las predicciones es significativamente menor (es decir, el segundo modelo genera mejores predicciones).**

Resumen:
- **t = -18.10**: la media de diferencias es negativa → después es MENOR que antes
- **p = 1.56e-63**: evidencia extremadamente fuerte contra H₀
- **d = 0.22**: efecto pequeño pero real
- **Potencia = 1.0**: probabilidad de detectar esta diferencia es prácticamente 100%

---

## Matemáticas detalladas

### Tamaño del efecto (Cohen's d para muestras pareadas)

#### Fórmula

$$d = \frac{\bar{x_d}}{s_d}$$

Donde:
- $d$ = tamaño del efecto (Cohen's d)
- $\bar{x_d}$ = media de las diferencias
- $s_d$ = desviación estándar de las diferencias

> **Nota:** Este es Cohen's d para muestras pareadas, NO el de muestras independientes. Se calcula sobre las diferencias, no sobre los datos individuales.

#### Interpretación de d

| Valor de d | Interpretación |
|---|---|
| |d| < 0.2 | Efecto pequeño / despreciable |
| 0.2 ≤ |d| < 0.5 | Efecto pequeño |
| 0.5 ≤ |d| < 0.8 | Efecto mediano |
| |d| ≥ 0.8 | Efecto grande |

En nuestro ejemplo: d = 0.22 → **efecto pequeño**, pero estadísticamente significativo (p << 0.05) con potencia ~1.0.

---

## Código completo de referencia

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro
from statsmodels.stats.power import TTestPower
from pingouin import ttest

# ============================================
# FLUJO COMPLETO: Prueba t de muestras pareadas
# ============================================

# ---- PASO 1: Definir el problema del negocio ----
# Modelo ML para predecir precio de viviendas
# error = |y_pred - y|
# Mejoramos el modelo con técnicas de escalamiento
# ¿Los errores disminuyeron?

# ---- PASO 2: Definir H₀ y H₁ ----
# d_i = error_{i_{después}} - error_{i_{antes}}
# H₀: x̄_d = 0 (no hay diferencia en los errores)
# H₁: x̄_d ≠ 0 (hay diferencia) → BILATERAL

# ---- PASO 3: Definir α ----
alpha = 0.05

# ---- PASO 4: Calcular potencia y tamaño de muestra ----
d = 0.8
power = 0.9

analisis = TTestPower()
n = analisis.solve_power(
    effect_size=d,
    alpha=alpha,
    power=power,
    alternative='two-sided'
)
print(f"Tamaño mínimo de pares: {n}")  # 18.45 → necesitamos 19 pares

# ---- PASO 5: Recolectar y preparar datos ----
datos = pd.read_csv('errores_prediccion_antes_despues.csv')
datos["diferencia"] = datos["errores_despues"] - datos["errores_antes"]

n = len(datos)
xd = np.mean(datos['diferencia'])
sd = np.std(datos['diferencia'])

print(f"\nEstadísticas de diferencias:")
print(f"  Tamaño (n): {n}")
print(f"  Media (x̄_d): {xd:.2f}")
print(f"  Desv. estándar (s_d): {sd:.2f}")

# ---- PASO 6: Verificar normalidad de las DIFERENCIAS ----
W1, p_shapiro = shapiro(datos['diferencia'])
print(f"\nShapiro-Wilk (diferencias): p = {p_shapiro:.6f}")

if p_shapiro >= 0.05:
    print("✅ Las diferencias son normales → prueba t es válida")
else:
    print("❌ Las diferencias NO son normales → considerar Wilcoxon")

# ---- PASO 7: Aplicar la prueba estadística ----
resultados = ttest(
    x=datos['errores_despues'].values,
    y=datos['errores_antes'].values,
    paired=True,
    alternative='two-sided'  # BILATERAL
)

print(f"\nResultados de la prueba t pareada:")
print(f"  t = {resultados['T'].values[0]:.4f}")
print(f"  dof = {resultados['dof'].values[0]}")
print(f"  p = {resultados['p-val'].values[0]:.6e}")
print(f"  CI95% = {resultados['CI95%'].values[0]}")
print(f"  cohen-d = {resultados['cohen-d'].values[0]:.4f}")
print(f"  BF10 = {resultados['BF10'].values[0]:.3e}")
print(f"  power = {resultados['power'].values[0]:.4f}")

# ---- PASO 8: Conclusión ----
if resultados['p-val'].values[0] < alpha:
    print(f"\n❌ Se rechaza H₀: Los errores de predicción son significativamente diferentes")
    print(f"   Media de diferencias: {xd:.2f}")
    if xd < 0:
        print(f"   → Los errores DISMINUYERON después de la mejora")
    else:
        print(f"   → Los errores INCREASE después de la mejora")
else:
    print(f"\n✅ No se rechaza H₀: No hay evidencia de diferencia significativa")
```

---

## Errores comunes

### 1. Usar TTestIndPower en vez de TTestPower

```python
# MAL: usar TTestIndPower para t-test pareado
from statsmodels.stats.power import TTestIndPower
analisis = TTestIndPower()
n = analisis.solve_power(effect_size=0.5, alpha=0.05, power=0.8,
                         alternative='two-sided', ratio=1)  # ❌ Es para independientes

# BIEN: usar TTestPower para t-test pareado
from statsmodels.stats.power import TTestPower
analisis = TTestPower()
n = analisis.solve_power(effect_size=0.5, alpha=0.05, power=0.8,
                         alternative='two-sided')  # ✅ Es para pareadas
```

### 2. Verificar normalidad de los grupos en vez de las diferencias

```python
# MAL: verificar normalidad de cada grupo
from scipy.stats import shapiro
W1, p1 = shapiro(datos['errores_antes'])    # ❌ NO es lo que necesitás
W2, p2 = shapiro(datos['errores_despues'])  # ❌ NO es lo que necesitás

# BIEN: verificar normalidad de las DIFERENCIAS
W1, p_shapiro = shapiro(datos['diferencia'])  # ✅ ESTO es lo que importa
print(f"Normalidad de diferencias: p = {p_shapiro:.6f}")
```

### 3. Usar paired=False cuando las mediciones son pareadas

```python
# MAL: decir que NO son pareadas cuando SÍ lo son
from pingouin import ttest
resultado = ttest(
    x=datos['errores_despues'].values,
    y=datos['errores_antes'].values,
    paired=False,  # ❌ ¡Son los mismos sujetos!
    alternative='two-sided'
)

# BIEN: indicar paired=True
resultado = ttest(
    x=datos['errores_despues'].values,
    y=datos['errores_antes'].values,
    paired=True,   # ✅ Son los mismos sujetos medidos dos veces
    alternative='two-sided'
)
```

### 4. Elegir bilateral o unilateral después de ver los datos

```python
# MAL: mirar los datos y después elegir la dirección
media_diff = datos['diferencia'].mean()
if media_diff < 0:
    # "¡Las diferencias son negativas! Uso 'less'" ❌
    resultado = ttest(x=antes, y=despues, paired=True, alternative='less')
else:
    # "Son positivas, uso bilateral" ❌
    resultado = ttest(x=antes, y=despues, paired=True, alternative='two-sided')

# BIEN: elegir la dirección ANTES de ver los datos
# "Quiero saber si los errores CAMBIARON" → bilateral
resultado = ttest(x=antes, y=despues, paired=True, alternative='two-sided')  # ✅
```

### 5. Confundir p-valor con tamaño del efecto

```python
# MAL: "p < 0.001, ¡el efecto es enorme!"
# El p-valor NO dice qué tan GRANDE es el efecto

# BIEN: reportar p-valor Y tamaño del efecto
print(f"p = {resultados['p-val'].values[0]:.6e} → {'Significativo' if resultados['p-val'].values[0] < 0.05 else 'No significativo'}")
print(f"d = {resultados['cohen-d'].values[0]:.4f} → {'Grande' if abs(resultados['cohen-d'].values[0]) >= 0.8 else 'Mediano' if abs(resultados['cohen-d'].values[0]) >= 0.5 else 'Pequeño'}")
```

### 6. No reportar la potencia

```python
# MAL: "rechazamos H₀" sin reportar potencia
# Puede ser que la potencia fuera muy baja y el resultado sea poco confiable

# BIEN: reportar potencia junto con el resultado
print(f"Potencia de la prueba: {resultados['power'].values[0]:.4f}")
if resultados['power'].values[0] >= 0.8:
    print("✅ Potencia adecuada — el resultado es confiable")
else:
    print("⚠️ Potencia baja — considerar aumentar el tamaño de muestra")
```

### 7. Usar la prueba independiente cuando el diseño es pareado

```python
# MAL: tratar datos pareados como independientes
from pingouin import ttest
resultado = ttest(
    x=datos['errores_despues'].values,
    y=datos['errores_antes'].values,
    paired=False,  # ❌ Pierde potencia al ignorar la correlación
    alternative='two-sided'
)

# BIEN: usar la prueba pareada
resultado = ttest(
    x=datos['errores_despues'].values,
    y=datos['errores_antes'].values,
    paired=True,   # ✅ Aprovecha la correlación entre pares
    alternative='two-sided'
)
```

### 8. No redondear el tamaño de muestra al entero superior

```python
# MAL: usar el tamaño de muestra como float
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.9,
                         alternative='two-sided')
# n = 18.45 → usar 18.45 como tamaño de muestra ❌

# BIEN: redondear al entero superior
import math
n = math.ceil(analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.9,
                                   alternative='two-sided'))
# n = 19 ✅
```

---

## Resumen: cuándo usar cada prueba

```
¿Qué querés hacer?
│
├── Comparar DOS mediciones en los MISMOS sujetos
│   │
│   ├── ¿Las diferencias son normales?
│   │   ├── SÍ → Prueba t de MUESTRAS PAREADAS
│   │   └── NO y n chico → Wilcoxon pareado
│   │
│   ├── ¿Querés saber si MEJORÓ (dirección)?
│   │   └── Unilateral pareado
│   │       (H₁: μ_d < 0 si d = después - antes)
│   │
│   ├── ¿Querés saber si EMPEORÓ (dirección)?
│   │   └── Unilateral pareado
│   │       (H₁: μ_d > 0 si d = después - antes)
│   │
│   └── ¿Querés saber si CAMBIÓ (sin dirección)?
│       └── Bilateral pareado
│           (H₁: μ_d ≠ 0)
│
├── Comparar DOS grupos INDEPENDIENTES
│   └── Usá la prueba t de dos muestras independientes
│
├── Comparar una media contra un valor teórico
│   └── Usá la prueba t de una muestra
│
├── ¿Tenés datos no normales con n chico?
│   └── Usá la prueba de Wilcoxon
│
├── ¿Los pares están altamente correlacionados?
│   └── La prueba pareada es más potente que la independiente
│
├── ¿El tamaño del efecto importa?
│   └── SIEMPRE reportar Cohen's d junto con el p-valor
│
└── ¿No sabés cuál usar?
    ├── ¿Mismos sujetos dos veces? → Pareada
    ├── ¿Dos grupos distintos? → Independiente
    └── ¿Una media vs un valor? → Una muestra
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Definir el problema del negocio | Reunión con stakeholders |
| 2 | Redactar como problema de Ciencia de Datos | Formulación clara |
| 3 | Verificar que el diseño es pareado (mismos sujetos) | Diseño experimental |
| 4 | Definir H₀ y H₁ (sobre las diferencias) | Conocimiento del dominio |
| 5 | Definir α (nivel de significancia) | Convención del dominio (0.05 típico) |
| 6 | Definir potencia deseada (1-β) | 0.9 típico |
| 7 | Calcular tamaño del efecto esperado | Cohen's d (sobre diferencias) |
| 8 | Calcular tamaño de muestra mínimo | `statsmodels.TTestPower.solve_power` |
| 9 | Recolectar datos (≥ n mínimo) | Experimento / medición |
| 10 | Calcular diferencias: d = después - antes | `pandas` / `numpy` |
| 11 | Verificar normalidad de las DIFERENCIAS | `scipy.stats.shapiro` |
| 12 | Calcular estadístico t | `pingouin.ttest(paired=True)` |
| 13 | Calcular p-valor | `pingouin.ttest` (viene incluido) |
| 14 | Comparar p con α | Decisión |
| 15 | Calcular tamaño del efecto observado | Cohen's d (viene en Pingouin) |
| 16 | Calcular potencia posterior | `pingouin.ttest` (viene incluido) |
| 17 | Evaluar correlación entre pares | `np.corrcoef` |
| 18 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lecciones 10-12.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- statsmodels Documentation. TTestPower — Power analysis for a one-sample t-test.
- Pingouin Documentation. `pingouin.ttest` — Paired samples T-test.
- SciPy Documentation. `scipy.stats.t` — Student's t continuous random variable.
- Vallat, R. (2018). Pingouin: statistics in Python. Journal of Open Source Software, 4(42), 1704.
- Howell, D. C. (2012). Statistical Methods for Psychology (8th ed.). Cengage Learning.
- Field, A. (2013). Discovering Statistics Using IBM SPSS Statistics (4th ed.). SAGE Publications.
