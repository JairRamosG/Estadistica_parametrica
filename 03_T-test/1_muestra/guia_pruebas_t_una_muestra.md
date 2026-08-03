# Guía de Referencia: Pruebas t de Una Muestra

## ¿Qué es?

La prueba t de una muestra es una herramienta **estadística paramétrica** que determina si la media de una muestra proviene de una población con una media conocida. Compara el promedio observado contra un valor de referencia usando la **distribución t de Student** en vez de la distribución normal estándar.

**Pregunta central:** ¿La media de mi muestra es significativamente diferente (o mayor/menor) que un valor conocido de la población?

**Qué NO hace:**
- No compara dos muestras entre sí (para eso está la prueba t de dos muestras)
- No asume que los datos son normales (pero SÍ requiere que la distribución muestral de la media sea normal)
- No reemplaza el juicio experto sobre si el tamaño del efecto es práctico
- **No prueban causalidad** — solo describen si hay diferencia estadísticamente significativa

### La diferencia clave con la prueba Z

| Característica | Prueba Z | Prueba t |
|---|---|---|
| **σ (desv. estándar poblacional)** | Conocida | **Desconocida** |
| **Desviación que se usa** | σ (poblacional) | **s (muestral)** |
| **Distribución** | Normal estándar | **t de Student** |
| **Grados de libertad** | No aplica | **n - 1** |

> **En la práctica real, casi siempre usás la prueba t.** La prueba Z requiere conocer σ de la población, algo que rara vez está disponible en proyectos reales. La prueba t es la herramienta por defecto cuando trabajás con datos del mundo real.

### Las dos variantes

- **Unilateral (one-tailed):** Pregunta si la media es **mayor** (o **menor**) que el valor de referencia
- **Bilateral (two-tailed):** Pregunta si la media es **diferente** (puede ser mayor o menor)

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás comparar una media contra un valor conocido?
│
│   ├── ¿Conocés la desviación estándar de la población (σ)?
│   │   ├── SÍ → Podés usar Prueba Z
│   │   └── NO → Usá Prueba t (t-test)
│   │
│   ├── ¿La distribución es normal o n > 30?
│   │   ├── SÍ (o n grande por CLT) → Podés usar Prueba t
│   │   └── NO y n chico → Considerá pruebas no paramétricas (Wilcoxon)
│   │
│   ├── ¿Querés saber si es MAYOR / MENOR?
│   │   └── Prueba UNILATERAL (one-tailed)
│   │
│   └── ¿Querés saber si es DIFERENTE (sin dirección)?
│       └── Prueba BILATERAL (two-tailed)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| Comparar media contra valor teórico (σ conocida) | Prueba Z |
| Comparar media contra valor teórico (σ **desconocida**) | **Prueba t** |
| Comparar si mejoró / empeoró (dirección conocida) | Unilateral |
| Comparar si cambió (sin dirección conocida) | Bilateral |
| σ desconocida pero n > 30 | Prueba t (más precisa que Z aproximada) |
| σ desconocida y n < 30 | Prueba t de Student |
| Comparar dos muestras entre sí | Prueba t de dos muestras |
| Datos no normales y n chico | Prueba de Wilcoxon |

### ¿Por qué importa unilateral vs bilateral?

- **Unilateral** es más potente para detectar un cambio en una dirección específica, pero NO puede detectar cambios en la dirección opuesta
- **Bilateral** es más conservadora (requiere más evidencia) pero detecta cambios en cualquier dirección
- **Elegí la dirección ANTES de ver los datos** — si elegís después, el p-valor no es válido

---

## Comparación entre pruebas

### Comparación directa

| Aspecto | Unilateral (One-tailed) | Bilateral (Two-tailed) |
|---|---|---|
| **Pregunta** | ¿Es MAYOR/MENOR? | ¿Es DIFERENTE? |
| **H₁** | μ > μ₀ o μ < μ₀ | μ ≠ μ₀ |
| **Valor crítico (α=0.05)** | t = ±1.660 (df=99) | t = ±1.984 (df=99) |
| **Requiere** | Conocer la dirección del cambio | Solo saber que hay cambio |
| **Potencia** | Mayor (más fácil rechazar H₀) | Menor (requiere más evidencia) |
| **Riesgo** | No detecta cambios en la otra dirección | Ninguno (detecta ambos lados) |
| **Uso típico** | "¿Mejoró?" "¿Empeoró?" | ¿Cambió? |
| **P-valor** | Una cola | Dos colas |

### t vs Z vs Wilcoxon

| Característica | Prueba Z | Prueba t | Wilcoxon |
|---|---|---|---|
| **σ conocida** | SÍ | No importa | No importa |
| **Normalidad requerida** | SÍ (o n grande) | SÍ (o n grande) | NO |
| **Distribución** | Normal estándar | t de Student | Rangos |
| **Tamaño mínimo** | n > 30 preferente | Cualquier n | Cualquier n |
| **Robustez** | Baja | Media | Alta |
| **Potencia con datos normales** | Alta | Alta | Menor que t |
| **Uso típico** | σ conocida, n grande | σ desconocida (caso real) | Datos no normales |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → no hay evidencia de diferencia | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar el contexto y el tamaño del efecto | ⚠️ |
| p < 0.05 | Se rechaza H₀ → hay diferencia significativa | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de diferencia | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa cada tipo?

- **Unilateral**: Solo mirás una cola de la distribución. Si el t calculado está en la cola correspondiente a H₁, rechazás H₀. Es como apostar a un caballo específico.
- **Bilateral**: Mirás ambas colas. Si el t calculado está en cualquiera de las dos colas extremas, rechazás H₀. Es como apostar a que el resultado será extremo (para cualquier lado).

---

## Matemáticas detrás de la prueba

### Estadístico t

#### Fórmula

$$t = \frac{\bar{x} - \mu}{s / \sqrt{n}}$$

Donde:
- $\bar{x}$ = media de la muestra
- $\mu$ = media de la población (valor de referencia)
- $s$ = desviación estándar de la **muestra** (NO poblacional)
- $n$ = tamaño de la muestra
- $s / \sqrt{n}$ = error estándar de la media (estimado)

> **Nota:** La fórmula es idéntica a la de la prueba Z, pero reemplazamos σ (poblacional) por s (muestral). Esa es la ÚNICA diferencia matemática.

#### Grados de libertad

$$df = n - 1$$

Los grados de libertad determinan la forma de la distribución t. A mayor df, más se parece a la distribución normal:
- df = 5 → colas pesadas (muy diferentes de la normal)
- df = 30 → similar a la normal
- df = 100 → prácticamente idéntica a la normal

#### Proceso

1. Definir la hipótesis nula (H₀: μ = μ₀) y alternativa (H₁)
2. Calcular la media de la muestra ($\bar{x}$)
3. Calcular la desviación estándar de la muestra ($s$)
4. Calcular el error estándar ($s / \sqrt{n}$)
5. Calcular t: cuántas desviaciones estándar se aleja $\bar{x}$ de $\mu$
6. Comparar t con el valor crítico o calcular el p-valor

#### Ejemplo manual simplificado

```python
# Datos del problema: μ = 0.86, n = 100, x_barra = 0.8458, s = 0.0338
# t = (0.8458 - 0.86) / (0.0338 / √100)
# t = -0.0142 / 0.00338
# t = -4.19
```

### Tamaño del efecto (Cohen's d)

#### Fórmula

$$d = \frac{\bar{x} - \mu}{s}$$

Donde:
- $d$ = tamaño del efecto (Cohen's d)
- $\bar{x}$ = media de la muestra
- $\mu$ = media de la población
- $s$ = desviación estándar de la **muestra**

> **Nota:** En la prueba t, usamos s (muestral) en vez de σ (poblacional) para calcular Cohen's d.

#### Interpretación de d

| Valor de d | Interpretación |
|---|---|
| |d| < 0.2 | Efecto pequeño / despreciable |
| 0.2 ≤ |d| < 0.5 | Efecto pequeño |
| 0.5 ≤ |d| < 0.8 | Efecto mediano |
| |d| ≥ 0.8 | Efecto grande |

### Potencia de la prueba

La potencia es la probabilidad de rechazar correctamente H₀ cuando es falsa (1 - β).

#### Cálculo de tamaño de muestra con statsmodels

```python
from statsmodels.stats.power import TTestPower

# Para t-test SE USA TTestPower, NO NormalIndPower
analisis = TTestPower()
n = analisis.solve_power(
    effect_size=d,
    power=potencia,
    alpha=alpha,
    alternative='two-sided'  # o 'larger', 'smaller'
)
```

> **IMPORTANTE:** Para t-test se usa `TTestPower`, NO `NormalIndPower`. Este último es exclusivo para la prueba Z.

#### Cálculo de potencia posterior con Pingouin

```python
from pingouin import ttest

# Pingouin calcula la potencia automáticamente
resultado = ttest(x=muestra, y=mu, alternative='two-sided')
potencia = resultado['power'].values[0]
```

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Verificar si una métrica supera un umbral (Unilateral)**

> Un sistema de streaming tiene un desempeño promedio de 0.75. ¿El nuevo sistema es mejor?

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro

# Datos del nuevo sistema
df = pd.read_csv('dataset_ztest_unilateral.csv')
print(f"Media del nuevo sistema: {df['metrica'].mean():.4f}")
print(f"Media del sistema actual: 0.75")
```

**Ejemplo 2: Verificar si un modelo mantuvo su desempeño (Bilateral)**

> Un modelo de diagnóstico cardíaco tiene F1=0.86. ¿El modelo modificado es diferente?

```python
df_bilateral = pd.read_csv('dataset_ztest_bilateral.csv')
print(f"Media del nuevo modelo: {df_bilateral['f1_score'].mean():.4f}")
print(f"Media del modelo original: 0.86")
```

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 3: Verificar normalidad antes de la prueba t**

```python
from scipy.stats import shapiro

# Verificar normalidad de los datos
W, p_shapiro = shapiro(df['metrica'])
print(f"Shapiro-Wilk: W = {W:.4f}, p = {p_shapiro:.6f}")

if p_shapiro >= 0.05:
    print("✅ Los datos son consistentes con una distribución normal")
else:
    print("❌ Los datos NO son normales — considerar pruebas no paramétricas")
```

**Ejemplo 4: Verificar normalidad en datos bilaterales**

```python
W, p_shapiro = shapiro(df_bilateral['f1_score'])
print(f"Shapiro-Wilk: W = {W:.4f}, p = {p_shapiro:.6f}")

if p_shapiro >= 0.05:
    print("✅ Los datos son consistentes con una distribución normal")
else:
    print("❌ Los datos NO son normales")
```

---

### 3. En Feature Engineering

**Ejemplo 5: Calcular tamaño del efecto esperado (Unilateral)**

> Queremos detectar un incremento de al menos 0.04 en el desempeño.

```python
# Parámetros del problema
mu = 0.75           # Media de la población (sistema actual)
incremento = 0.04   # Incremento que queremos detectar

# Para la prueba t, necesitamos estimar s (desv. estándar muestral)
# Asumimos s ≈ 0.05 basado en datos anteriores
s_estimada = 0.05

# Tamaño del efecto
d = incremento / s_estimada
print(f"Tamaño del efecto (d): {d}")  # 0.8 → efecto grande
```

**Ejemplo 6: Calcular tamaño del efecto esperado (Bilateral)**

> Esperamos una reducción máxima de 0.01 en el desempeño.

```python
# Parámetros del problema
mu = 0.86           # Media de la población (modelo actual)
reduccion = 0.01    # Reducción que queremos detectar

# Para la prueba t, estimamos s
s_estimada = 0.03

# Tamaño del efecto
x_esperado = mu - reduccion
d = (x_esperado - mu) / s_estimada
print(f"Tamaño del efecto (d): {d}")  # -0.333 → efecto pequeño
# El signo indica dirección, la magnitud es lo importante
```

---

### 4. En Selección de Modelos

**Ejemplo 7: Calcular tamaño de muestra para el test (Unilateral)**

> Queremos potencia de 0.8 y tamaño del efecto de 0.8.

```python
from statsmodels.stats.power import TTestPower

# Parámetros
effect_size = 0.8   # Tamaño del efecto
power = 0.8         # Potencia deseada
alpha = 0.05        # Nivel de significancia

# Instancia de TTestPower (NO NormalIndPower)
analisis = TTestPower()

# Cálculo del tamaño de muestra
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='larger'  # Unilateral derecho
)
print(f"Tamaño mínimo de muestra: {n:.2f}")  # ~9.66 → necesita 10
```

**Ejemplo 8: Calcular tamaño de muestra para el test (Bilateral)**

> Queremos potencia de 0.9 y tamaño del efecto de 0.333.

```python
from statsmodels.stats.power import TTestPower

# Parámetros
effect_size = abs(0.333)  # Valor absoluto del tamaño del efecto
power = 0.9               # Potencia deseada
alpha = 0.05              # Nivel de significancia

# Instancia de TTestPower
analisis = TTestPower()

# Cálculo del tamaño de muestra
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='two-sided'  # Bilateral
)
print(f"Tamaño mínimo de muestra: {n:.2f}")  # ~94.57 → necesita 95
```

---

### 5. En Evaluación post-deploy

**Ejemplo 9: Aplicar la prueba t (Unilateral)**

```python
from pingouin import ttest

# Prueba t unilateral derecha
resultado = ttest(
    x=df['metrica'].values,
    y=0.75,                    # Media de la población
    paired='False',
    alternative='greater'      # Unilateral derecho
)

t_stat = resultado['T'].values[0]
p_val = resultado['p-val'].values[0]

print(f't: {t_stat:.4f}')
print(f'p: {p_val:.6f}')
```

**Ejemplo 10: Aplicar la prueba t (Bilateral)**

```python
from pingouin import ttest

# Prueba t bilateral
resultado = ttest(
    x=df_bilateral['f1_score'].values,
    y=0.86,                     # Media de la población
    paired='False',
    alternative='two-sided'     # Bilateral
)

t_stat = resultado['T'].values[0]
p_val = resultado['p-val'].values[0]

print(f't: {t_stat:.4f}')
print(f'p: {p_val:.6f}')
# Resultado: t ≈ -4.17, p ≈ 6.53e-05
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 11: Evaluar tamaño del efecto posterior a la prueba (Unilateral)**

```python
import numpy as np

# Tamaño del efecto actualizado con los datos reales
s = np.std(df['metrica'])
d = (np.mean(df['metrica']) - 0.75) / s
print(f'Tamaño del efecto (d) actualizado: {d:.4f}')
# Resultado: d ≈ 1.14 → efecto grande
```

**Ejemplo 12: Evaluar tamaño del efecto posterior a la prueba (Bilateral)**

```python
import numpy as np

# Tamaño del efecto actualizado con los datos reales
s = np.std(df_bilateral['f1_score'])
d = (np.mean(df_bilateral['f1_score']) - 0.86) / s
print(f'Tamaño del efecto (d) actualizado: {d:.4f}')
# Resultado: d ≈ -0.47 → efecto pequeño-medio (reducción)
```

---

### 7. En Validación de supuestos

**Ejemplo 13: Calcular potencia posterior (Unilateral)**

```python
from pingouin import ttest

# Pingouin calcula la potencia automáticamente
resultado = ttest(
    x=df['metrica'].values,
    y=0.75,
    paired='False',
    alternative='greater'
)
potencia = resultado['power'].values[0]
print(f'Potencia actualizada: {potencia:.4f}')
# Resultado: potencia ≈ 0.9896
```

**Ejemplo 14: Calcular potencia posterior (Bilateral)**

```python
from pingouin import ttest

# Pingouin calcula la potencia automáticamente
resultado = ttest(
    x=df_bilateral['f1_score'].values,
    y=0.86,
    paired='False',
    alternative='two-sided'
)
potencia = resultado['power'].values[0]
print(f'Potencia actualizada: {potencia:.4f}')
# Resultado: potencia ≈ 0.9849
```

---

## Interpretación de resultados

### Salida del cálculo de t

```python
t = (x_barra - mu) / (s / np.sqrt(n))
# t = -4.17 (bilateral) para el ejemplo del modelo cardíaco
```

**Cómo leerlo:**
- **t positivo**: la media de la muestra está POR ARRIBA de la media de la población
- **t negativo**: la media de la muestra está POR ABAJO de la media de la población
- **|t| > valor crítico**: diferencia significativa al nivel de significancia dado

### Salida de Pingouin (la más completa)

```python
from pingouin import ttest

resultado = ttest(x=muestra, y=0.86, paired='False', alternative='two-sided')
print(resultado)
```

Pingouin devuelve una tabla con:

| Campo | Descripción | Ejemplo |
|---|---|---|
| **T** | Estadístico t calculado | -4.17 |
| **dof** | Grados de libertad (n-1) | 99 |
| **alternative** | Tipo de prueba | two-sided |
| **p-val** | P-valor | 0.000065 |
| **CI95%** | Intervalo de confianza 95% | [0.84, 0.85] |
| **cohen-d** | Tamaño del efecto | 0.417 |
| **BF10** | Factor de Bayes | 273.3 |
| **power** | Potencia de la prueba | 0.985 |

### Reglas de decisión

```
UNILATERAL (α = 0.05):
t > t_crítico   → ❌ Se rechaza H₀ (la media es mayor)
t ≤ t_crítico   → ✅ No se rechaza H₀

BILATERAL (α = 0.05):
|t| > t_crítico → ❌ Se rechaza H₀ (diferencia significativa)
|t| ≤ t_crítico → ✅ No se rechaza H₀
```

### ¿Qué reportar?

```python
# Formato completo (Bilateral)
print(f"Prueba t bilateral:")
print(f"  μ₀ = {mu}, n = {n}")
print(f"  x̄ = {x_barra:.4f}")
print(f"  s = {s:.4f}")
print(f"  t = {t:.4f}")
print(f"  df = {n - 1}")
print(f"  p = {p:.6f}")
print(f"  CI95% = [{ci_low:.4f}, {ci_high:.4f}]")
print(f"  d = {d:.4f}")
print(f"  Potencia = {potencia:.4f}")
```

### ¿Qué hacer según el resultado?

1. **Rechazás H₀**: Hay evidencia estadística suficiente
   - Reportar el p-valor, el tamaño del efecto y la potencia
   - Evaluar si el tamaño del efecto es prácticamente relevante
2. **No rechazás H₀**: No hay evidencia suficiente
   - NO significa que H₀ sea verdadera
   - Verificar si la potencia fue suficiente
   - Verificar si el tamaño de muestra fue adecuado
3. **Zona gris (0.05 < p < 0.10)**:
   - Considerar el contexto del negocio
   - Reportar el intervalo de confianza
   - Replicar con más datos si es posible

---

## Errores comunes

### 1. Usar NormalIndPower en vez de TTestPower

```python
# MAL: usar NormalIndPower para t-test
from statsmodels.stats.power import NormalIndPower
analisis = NormalIndPower()
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                         alternative='two-sided', ratio=0)  # ❌ Es para Z-test

# BIEN: usar TTestPower para t-test
from statsmodels.stats.power import TTestPower
analisis = TTestPower()
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                         alternative='two-sided')  # ✅ Es para t-test
```

### 2. Elegir bilateral o unilateral después de ver los datos

```python
# MAL: mirar los datos y después elegir la dirección
x_barra = np.mean(df)
if x_barra > mu:
    # "Ah, ¡es mayor! Uso unilateral" ❌
    resultado = ttest(x=df['f1_score'].values, y=0.86, alternative='greater')
else:
    # "Es menor, uso bilateral" ❌
    resultado = ttest(x=df['f1_score'].values, y=0.86, alternative='two-sided')

# BIEN: elegir la dirección ANTES de ver los datos
# "Quiero saber si el nuevo sistema es MEJOR" → unilateral derecho
resultado = ttest(x=df['f1_score'].values, y=0.86, alternative='greater')  # ✅
```

### 3. Ignorar la verificación de normalidad

```python
# MAL: aplicar t-test sin verificar normalidad
from pingouin import ttest
resultado = ttest(x=df['f1_score'].values, y=0.86)  # ❌ ¿Y si no es normal?

# BIEN: verificar normalidad primero
from scipy.stats import shapiro
W, p_shapiro = shapiro(df['f1_score'])
if p_shapiro >= 0.05:
    print("✅ Datos normales, t-test válido")
    resultado = ttest(x=df['f1_score'].values, y=0.86)
else:
    print("❌ Datos no normales, considerar Wilcoxon")
```

### 4. Confundir p-valor con tamaño del efecto

```python
# MAL: "p = 0.001, ¡el efecto es enorme!"
# El p-valor NO dice qué tan GRANDE es el efecto

# BIEN: reportar p-valor Y tamaño del efecto
print(f"p = {p:.6f} → {'Significativo' if p < 0.05 else 'No significativo'}")
print(f"d = {d:.4f} → {'Grande' if abs(d) >= 0.8 else 'Mediano' if abs(d) >= 0.5 else 'Pequeño'}")
```

### 5. No reportar la potencia

```python
# MAL: "rechazamos H₀" sin reportar potencia
# Puede ser que la potencia fuera muy baja y el resultado sea poco confiable

# BIEN: reportar potencia junto con el resultado
print(f"Potencia de la prueba: {potencia:.4f}")
if potencia >= 0.8:
    print("✅ Potencia adecuada — el resultado es confiable")
else:
    print("⚠️ Potencia baja — considerar aumentar el tamaño de muestra")
```

### 6. Usar bilateral cuando la pregunta es direccional

```python
# MAL: preguntar "¿mejoró?" pero usar bilateral
# La pregunta es direccional → debería ser unilateral
resultado = ttest(x=df['f1_score'].values, y=0.86, alternative='two-sided')  # ❌

# BIEN: preguntar "¿mejoró?" → unilateral derecho
resultado = ttest(x=df['f1_score'].values, y=0.86, alternative='greater')  # ✅
```

### 7. No redondear el tamaño de muestra al entero superior

```python
# MAL: usar el tamaño de muestra como float
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                         alternative='two-sided')
# n = 9.66 → usar 9.66 como tamaño de muestra ❌

# BIEN: redondear al entero superior
import math
n = math.ceil(analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                                   alternative='two-sided'))
# n = 10 ✅
```

### 8. Usar scipy.stats.norm en vez de scipy.stats.t

```python
# MAL: usar la distribución normal para calcular p de un t-test
from scipy.stats import norm
p = 2 * norm.cdf(t)  # ❌ Debería usar la distribución t

# BIEN: usar la distribución t
from scipy.stats import t
df = n - 1
p = 2 * t.cdf(t, df)  # ✅ Distribución t correcta
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, t
from statsmodels.stats.power import TTestPower
from pingouin import ttest
import matplotlib.pyplot as plt
import math

# ============================================
# FLUJO COMPLETO: Prueba t de una muestra
# ============================================

# ---- PASO 1: Definir el problema del negocio ----
# Ejemplo: ¿El modelo modificado mantiene el mismo desempeño?

# ---- PASO 2: Redactar como problema de Ciencia de Datos ----
# ¿El nuevo modelo tiene un desempeño equivalente, estadísticamente, al original?

# ---- PASO 3: Definir H₀ y H₁ ----
mu = 0.86       # Media de la población (modelo original)
# H₀: μ = 0.86 (el nuevo modelo es igual)
# H₁: μ ≠ 0.86 (el nuevo modelo es diferente)

# ---- PASO 4: Definir α ----
alpha = 0.05

# ---- PASO 5: Calcular potencia y tamaño de muestra ----
d = 0.6             # Tamaño del efecto esperado (mediano-grande)
potencia = 0.9      # Potencia deseada

analisis = TTestPower()  # ← TTestPower para t-test
n_minimo = analisis.solve_power(
    effect_size=d,
    alpha=alpha,
    power=potencia,
    alternative='two-sided'  # Bilateral
)
print(f"Tamaño mínimo de muestra: {math.ceil(n_minimo)}")  # ~31

# ---- PASO 6: Recolectar y preparar datos ----
df = pd.read_csv('dataset_ztest_bilateral.csv')
print(f"Media del nuevo modelo: {df['f1_score'].mean():.4f}")
print(f"Media del modelo original: {mu}")

# Verificar normalidad
W, p_shapiro = shapiro(df['f1_score'])
print(f"Normalidad (Shapiro-Wilk): p = {p_shapiro:.4f}")

if p_shapiro >= 0.05:
    print("✅ Datos normales, proceder con t-test")
else:
    print("❌ Datos no normales, considerar Wilcoxon")

# ---- PASO 7: Aplicar la prueba estadística ----
n = len(df)
x_barra = np.mean(df['f1_score'])
s = np.std(df['f1_score'])

print(f"\nEstadísticas de la muestra:")
print(f"  Media (x̄): {x_barra:.4f}")
print(f"  Desv. Estándar (s): {s:.4f}")
print(f"  Tamaño (n): {n}")
print(f"  Grados de libertad (df): {n - 1}")

# Prueba t con Pingouin (todo en una función)
resultado = ttest(
    x=df['f1_score'].values,
    y=mu,
    paired='False',
    alternative='two-sided'
)

print(f"\nResultados de la prueba t bilateral:")
print(f"  t = {resultado['T'].values[0]:.4f}")
print(f"  p = {resultado['p-val'].values[0]:.6f}")
print(f"  CI95% = {resultado['CI95%'].values[0]}")
print(f"  ¿Se rechaza H₀? {'SÍ' if resultado['p-val'].values[0] < alpha else 'NO'}")

# ---- PASO 8: Evaluar tamaño del efecto y potencia ----
d_observado = resultado['cohen-d'].values[0]
potencia_observada = resultado['power'].values[0]

print(f"\nTamaño del efecto (d): {d_observado:.4f}")
print(f"Potencia actualizada: {potencia_observada:.4f}")

# ---- CONCLUSIÓN ----
if resultado['p-val'].values[0] < alpha:
    print(f"\n❌ Se rechaza H₀: El nuevo modelo tiene un desempeño DIFERENTE")
    print(f"   Media observada: {x_barra:.4f} vs. Media esperada: {mu}")
    print(f"   Tamaño del efecto: {d_observado:.4f} ({'Grande' if abs(d_observado) >= 0.8 else 'Mediano' if abs(d_observado) >= 0.5 else 'Pequeño'})")
else:
    print(f"\n✅ No se rechaza H₀: No hay evidencia de diferencia significativa")
```

---

## Resumen: cuándo usar cada prueba

```
¿Qué querés hacer?
│
├── Comparar media contra un valor teórico
│   │
│   ├── ¿Conocés σ de la población?
│   │   ├── SÍ → Prueba Z
│   │   └── NO → Prueba t (t-test)
│   │
│   ├── ¿Querés saber si es MAYOR?
│   │   └── t unilateral derecho
│   │       (H₁: μ > μ₀)
│   │
│   ├── ¿Querés saber si es MENOR?
│   │   └── t unilateral izquierdo
│   │       (H₁: μ < μ₀)
│   │
│   └── ¿Querés saber si es DIFERENTE?
│       └── t bilateral
│           (H₁: μ ≠ μ₀)
│
├── ¿Tenés datos no normales con n chico?
│   └── Usá la prueba de Wilcoxon
│
├── ¿Tenés datos de streaming/metrics?
│   └── Verificar normalidad ANTES de la prueba t
│
├── ¿El tamaño del efecto importa?
│   └── SIEMPRE reportar Cohen's d junto con el p-valor
│
└── ¿No sabés cuál usar?
    └── Si σ es desconocida (casi siempre) → Prueba t
        Si la pregunta es direccional → Unilateral
        Si la pregunta es "¿cambió?" → Bilateral
```

---

## Código rápido de referencia

```python
import numpy as np
from scipy.stats import shapiro, t
from statsmodels.stats.power import TTestPower
from pingouin import ttest
import math

# ============================================
# PRUEBA t UNILATERAL DERECHA
# ============================================
# H₀: μ = μ₀,  H₁: μ > μ₀

mu = 0.75
n = len(df)
x_barra = np.mean(df['metrica'])
s = np.std(df['metrica'])

# Verificar normalidad
W, p_shapiro = shapiro(df['metrica'])

# Prueba t unilateral derecha con Pingouin
resultado = ttest(x=df['metrica'].values, y=mu, paired='False', alternative='greater')

t_stat = resultado['T'].values[0]
p_val = resultado['p-val'].values[0]
d = resultado['cohen-d'].values[0]
potencia = resultado['power'].values[0]

print(f"t = {t_stat:.4f}, p = {p_val:.6f}, d = {d:.4f}, power = {potencia:.4f}")

# ============================================
# PRUEBA t BILATERAL
# ============================================
# H₀: μ = μ₀,  H₁: μ ≠ μ₀

mu = 0.86
n = len(df)
x_barra = np.mean(df['f1_score'])
s = np.std(df['f1_score'])

# Verificar normalidad
W, p_shapiro = shapiro(df['f1_score'])

# Prueba t bilateral con Pingouin
resultado = ttest(x=df['f1_score'].values, y=mu, paired='False', alternative='two-sided')

t_stat = resultado['T'].values[0]
p_val = resultado['p-val'].values[0]
d = resultado['cohen-d'].values[0]
potencia = resultado['power'].values[0]

print(f"t = {t_stat:.4f}, p = {p_val:.6f}, d = {d:.4f}, power = {potencia:.4f}")

# ============================================
# CÁLCULO DE TAMAÑO DE MUESTRA
# ============================================
analisis = TTestPower()  # ← TTestPower, NO NormalIndPower

# Unilateral
n_uni = math.ceil(analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                                       alternative='larger'))

# Bilateral
n_bi = math.ceil(analisis.solve_power(effect_size=0.333, alpha=0.05, power=0.9,
                                      alternative='two-sided'))

# ============================================
# VALORES CRÍTICOS (distribución t)
# ============================================
df = n - 1  # Grados de libertad
t_critico_uni = t.ppf(1 - 0.05, df)      # Unilateral derecho
t_critico_bi = t.ppf(1 - 0.025, df)       # Bilateral
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Definir el problema del negocio | Reunión con stakeholders |
| 2 | Redactar como problema de Ciencia de Datos | Formulación clara |
| 3 | Definir H₀ y H₁ | Conocimiento del dominio |
| 4 | Verificar que σ es desconocida | Análisis de datos anteriores |
| 5 | Verificar normalidad de los datos | `scipy.stats.shapiro` |
| 6 | Definir α (nivel de significancia) | Convención del dominio (0.05 típico) |
| 7 | Definir potencia deseada (1-β) | 0.8 o 0.9 típico |
| 8 | Calcular tamaño del efecto esperado | Cohen's d (usando s) |
| 9 | Calcular tamaño de muestra mínimo | `statsmodels.TTestPower.solve_power` |
| 10 | Recolectar datos (≥ n mínimo) | Experimento / medición |
| 11 | Calcular estadístico t | `pingouin.ttest` o fórmula manual |
| 12 | Calcular p-valor | `pingouin.ttest` (viene incluido) |
| 13 | Comparar p con α | Decisión |
| 14 | Calcular tamaño del efecto observado | Cohen's d (viene en Pingouin) |
| 15 | Calcular potencia posterior | `pingouin.ttest` (viene incluido) |
| 16 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lecciones 10-12.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- statsmodels Documentation. TTestPower — Power analysis for a one-sample t-test.
- Pingouin Documentation. `pingouin.ttest` — One-sample T-test.
- SciPy Documentation. `scipy.stats.t` — Student's t continuous random variable.
- Vallat, R. (2018). Pingouin: statistics in Python. Journal of Open Source Software, 4(42), 1704.
