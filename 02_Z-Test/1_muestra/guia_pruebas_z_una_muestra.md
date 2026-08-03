# Guía de Referencia: Pruebas Z de Una Muestra

## ¿Qué es?

La prueba Z de una muestra es una herramienta **estadística paramétrica** que determina si la media de una muestra proviene de una población con una media conocida. Compara el promedio observado contra un valor de referencia usando la distribución normal estándar.

**Pregunta central:** ¿La media de mi muestra es significativamente diferente (o mayor/menor) que un valor conocido de la población?

**Qué NO hace:**
- No compara dos muestras entre sí (para eso está la prueba Z de dos muestras)
- No asume que los datos son normales (pero SÍ requiere que la distribución muestral de la media sea normal)
- No reemplaza el juicio experto sobre si el tamaño del efecto es práctico
- **No prueban causalidad** — solo describen si hay diferencia estadísticamente significativa

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
│   │   ├── SÍ (o n grande por CLT) → Podés usar Prueba Z
│   │   └── NO y n chico → Considerá pruebas no paramétricas
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
| Comparar si mejoró / empeoró (dirección conocida) | Unilateral |
| Comparar si cambió (sin dirección conocida) | Bilateral |
| σ desconocida pero n > 30 | Prueba Z (aproximada) |
| σ desconocida y n < 30 | Prueba t de Student |
| Comparar dos muestras entre sí | Prueba Z de dos muestras |

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
| **Valor crítico (α=0.05)** | z = ±1.645 | z = ±1.96 |
| **Requiere** | Conocer la dirección del cambio | Solo saber que hay cambio |
| **Potencia** | Mayor (más fácil rechazar H₀) | Menor (requiere más evidencia) |
| **Riesgo** | No detecta cambios en la otra dirección | Ninguno (detecta ambos lados) |
| **Uso típico** | "¿Mejoró?" "¿Empeoró?" | ¿Cambió? |
| **P-valor** | Una cola | Dos colas |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → no hay evidencia de diferencia | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar el contexto y el tamaño del efecto | ⚠️ |
| p < 0.05 | Se rechaza H₀ → hay diferencia significativa | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de diferencia | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa cada tipo?

- **Unilateral**: Solo mirás una cola de la distribución. Si el z calculado está en la cola correspondiente a H₁, rechazás H₀. Es como apostar a un caballo específico.
- **Bilateral**: Mirás ambas colas. Si el z calculado está en cualquiera de las dos colas extremas, rechazás H₀. Es como apostar a que el resultado será extremo (para cualquier lado).

---

## Matemáticas detrás de la prueba

### Estadístico Z

#### Fórmula

$$z = \frac{\bar{x} - \mu}{\sigma / \sqrt{n}}$$

Donde:
- $\bar{x}$ = media de la muestra
- $\mu$ = media de la población (valor de referencia)
- $\sigma$ = desviación estándar de la población (conocida)
- $n$ = tamaño de la muestra
- $\sigma / \sqrt{n}$ = error estándar de la media

#### Proceso

1. Definir la hipótesis nula (H₀: μ = μ₀) y alternativa (H₁)
2. Calcular la media de la muestra ($\bar{x}$)
3. Calcular el error estándar ($\sigma / \sqrt{n}$)
4. Calcular z: cuántas desviaciones estándar se aleja $\bar{x}$ de $\mu$
5. Comparar z con el valor crítico o calcular el p-valor

#### Ejemplo manual simplificado

```python
# Datos del problema: μ = 0.75, σ = 0.05, n = 12, x_barra = 0.807
# z = (0.807 - 0.75) / (0.05 / √12)
# z = 0.057 / 0.0144
# z = 3.94
```

### Tamaño del efecto (Cohen's d)

#### Fórmula

$$d = \frac{\bar{x} - \mu}{\sigma}$$

Donde:
- $d$ = tamaño del efecto (Cohen's d)
- $\bar{x}$ = media de la muestra
- $\mu$ = media de la población
- $\sigma$ = desviación estándar de la población

#### Interpretación de d

| Valor de d | Interpretación |
|---|---|
| |d| < 0.2 | Efecto pequeño / despreciable |
| 0.2 ≤ |d| < 0.5 | Efecto pequeño |
| 0.5 ≤ |d| < 0.8 | Efecto mediano |
| |d| ≥ 0.8 | Efecto grande |

### Potencia de la prueba

La potencia es la probabilidad de rechazar correctamente H₀ cuando es falsa (1 - β).

#### Cálculo de tamaño de muestra

$$n = \left( \frac{z_\alpha + z_\beta}{d} \right)^2$$

Donde:
- $z_\alpha$ = valor z asociado al nivel de significancia
- $z_\beta$ = valor z asociado a la potencia deseada
- $d$ = tamaño del efecto

#### Cálculo de potencia con statsmodels

```python
from statsmodels.stats.power import NormalIndPower

analisis = NormalIndPower()
potencia = analisis.power(
    effect_size=d,
    nobs1=n,
    alpha=alpha,
    ratio=0,           # Prueba de 1 muestra
    alternative='larger' # 'larger', 'smaller', o 'two-sided'
)
```

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Verificar si una métrica supera un umbral (Unilateral)**

> Un sistema de streaming tiene un desempeño promedio de 0.75. ¿El nuevo sistema es mejor?

```python
import pandas as pd
import numpy as np
from scipy.stats import norm, shapiro
from statsmodels.stats.power import NormalIndPower

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

**Ejemplo 3: Verificar normalidad antes de la prueba Z**

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
sigma = 0.05        # Desviación estándar de la población
incremento = 0.04   # Incremento que queremos detectar

# Tamaño del efecto
d = incremento / sigma
print(f"Tamaño del efecto (d): {d}")  # 0.8 → efecto grande
```

**Ejemplo 6: Calcular tamaño del efecto esperado (Bilateral)**

> Esperamos una reducción máxima de 0.01 en el desempeño.

```python
# Parámetros del problema
mu = 0.86           # Media de la población (modelo actual)
sigma = 0.03        # Desviación estándar de la población
reduccion = 0.01    # Reducción que queremos detectar

# Tamaño del efecto
x_esperado = mu - reduccion
d = (x_esperado - mu) / sigma
print(f"Tamaño del efecto (d): {d}")  # -0.333 → efecto pequeño
# El signo indica dirección, la magnitud es lo importante
```

---

### 4. En Selección de Modelos

**Ejemplo 7: Calcular tamaño de muestra para el test (Unilateral)**

> Queremos potencia de 0.8 y tamaño del efecto de 0.8.

```python
from statsmodels.stats.power import NormalIndPower

# Parámetros
effect_size = 0.8   # Tamaño del efecto
power = 0.8         # Potencia deseada
alpha = 0.05        # Nivel de significancia

# Instancia de NormalIndPower
analisis = NormalIndPower()

# Cálculo del tamaño de muestra
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='larger',  # Unilateral derecho
    ratio=0                # Prueba de 1 muestra
)
print(f"Tamaño mínimo de muestra: {n:.2f}")  # ~9.66 → necesita 10
```

**Ejemplo 8: Calcular tamaño de muestra para el test (Bilateral)**

> Queremos potencia de 0.9 y tamaño del efecto de 0.333.

```python
from statsmodels.stats.power import NormalIndPower

# Parámetros
effect_size = abs(0.333)  # Valor absoluto del tamaño del efecto
power = 0.9               # Potencia deseada
alpha = 0.05              # Nivel de significancia

# Instancia de NormalIndPower
analisis = NormalIndPower()

# Cálculo del tamaño de muestra
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='two-sided',  # Bilateral
    ratio=0                   # Prueba de 1 muestra
)
print(f"Tamaño mínimo de muestra: {n:.2f}")  # ~94.57 → necesita 95
```

---

### 5. En Evaluación post-deploy

**Ejemplo 9: Aplicar la prueba Z (Unilateral)**

```python
from scipy.stats import norm
import numpy as np

# Parámetros de la población y de la muestra
mu = 0.75
sigma = 0.05
n = len(df)
x_barra = np.mean(df)

# Calcular z
z = (x_barra - mu) / (sigma / np.sqrt(n))

# Calcular p (unilateral derecho: p = 1 - P(Z <= z))
p = 1 - norm.cdf(z)

print(f'z: {z}')
print(f'p: {p}')
# Resultado: z ≈ 3.96, p ≈ 3.8e-05
```

**Ejemplo 10: Aplicar la prueba Z (Bilateral)**

```python
from scipy.stats import norm
import numpy as np

# Parámetros de la población y de la muestra
mu = 0.86
sigma = 0.03
n = len(df_bilateral)
x_barra = np.mean(df_bilateral)

# Calcular z
z = (x_barra - mu) / (sigma / np.sqrt(n))

# Calcular p (bilateral: p = 2 * P(Z <= z) si z es negativo)
p = 2 * norm.cdf(z)

print(f'z: {z}')
print(f'p: {p}')
# Resultado: z ≈ -4.73, p ≈ 2.26e-06
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 11: Evaluar tamaño del efecto posterior a la prueba (Unilateral)**

```python
# Tamaño del efecto actualizado con los datos reales
d = (np.mean(df) - mu) / sigma
print(f'Tamaño del efecto (d) actualizado: {d:.4f}')
# Resultado: d ≈ 1.14 → efecto grande
```

**Ejemplo 12: Evaluar tamaño del efecto posterior a la prueba (Bilateral)**

```python
# Tamaño del efecto actualizado con los datos reales
d = (np.mean(df_bilateral) - mu) / sigma
print(f'Tamaño del efecto (d) actualizado: {d:.4f}')
# Resultado: d ≈ -0.47 → efecto pequeño-medio (reducción)
```

---

### 7. En Validación de supuestos

**Ejemplo 13: Calcular potencia posterior (Unilateral)**

```python
from statsmodels.stats.power import NormalIndPower

n = len(df)
alpha = 0.05

analisis = NormalIndPower()

potencia = analisis.power(
    effect_size=d,
    nobs1=n,
    alpha=alpha,
    ratio=0,
    alternative='larger'
)
print(f'Potencia actualizada: {potencia:.4f}')
# Resultado: potencia ≈ 0.9896
```

**Ejemplo 14: Calcular potencia posterior (Bilateral)**

```python
from statsmodels.stats.power import NormalIndPower

n = len(df_bilateral)
alpha = 0.05

analisis = NormalIndPower()

potencia = analisis.power(
    effect_size=abs(d),
    nobs1=n,
    alpha=alpha,
    ratio=0,
    alternative='two-sided'
)
print(f'Potencia actualizada: {potencia:.4f}')
# Resultado: potencia ≈ 0.9972
```

---

## Interpretación de resultados

### Salida del cálculo de z

```python
z = (x_barra - mu) / (sigma / np.sqrt(n))
# z = 3.96 (unilateral) o z = -4.73 (bilateral)
```

**Cómo leerlo:**
- **z positivo**: la media de la muestra está POR ARRIBA de la media de la población
- **z negativo**: la media de la muestra está POR ABAJO de la media de la población
- **|z| > 1.96** (bilateral) o **|z| > 1.645** (unilateral): diferencia significativa al nivel de 0.05

### Salida del cálculo de p

```python
# Unilateral derecho
p = 1 - norm.cdf(z)

# Bilateral
p = 2 * norm.cdf(z)  # cuando z es negativo
# o
p = 2 * (1 - norm.cdf(z))  # cuando z es positivo
```

### Reglas de decisión

```
UNILATERAL (α = 0.05):
z > 1.645  → ❌ Se rechaza H₀ (la media es mayor)
z ≤ 1.645  → ✅ No se rechaza H₀

BILATERAL (α = 0.05):
z > 1.96   → ❌ Se rechaza H₀ (diferencia significativa)
z < -1.96  → ❌ Se rechaza H₀ (diferencia significativa)
-1.96 ≤ z ≤ 1.96 → ✅ No se rechaza H₀
```

### ¿Qué reportar?

```python
# Formato completo (Unilateral)
print(f"Prueba Z unilateral:")
print(f"  μ₀ = {mu}, σ = {sigma}, n = {n}")
print(f"  x̄ = {x_barra:.4f}")
print(f"  z = {z:.4f}")
print(f"  p = {p:.6f}")
print(f"  d = {d:.4f}")
print(f"  Potencia = {potencia:.4f}")
```

```python
# Formato completo (Bilateral)
print(f"Prueba Z bilateral:")
print(f"  μ₀ = {mu}, σ = {sigma}, n = {n}")
print(f"  x̄ = {x_barra:.4f}")
print(f"  z = {z:.4f}")
print(f"  p = {p:.6f}")
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

### 1. Usar prueba Z cuando σ es desconocida

```python
# MAL: usar Z cuando no conocés σ (usar la desv. de la muestra)
sigma_estimada = df['metrica'].std()
z = (x_barra - mu) / (sigma_estimada / np.sqrt(n))  # ❌ Debería ser t-test

# BIEN: si σ es desconocida, usar t-test
from scipy.stats import ttest_1samp
t_stat, p = ttest_1samp(df['metrica'], mu)
```

### 2. Elegir bilateral o unilateral después de ver los datos

```python
# MAL: mirar los datos y después elegir la dirección
x_barra = np.mean(df)
if x_barra > mu:
    # "Ah, ¡es mayor! Uso unilateral" ❌
    p = 1 - norm.cdf(z)
else:
    # "Es menor, uso bilateral" ❌
    p = 2 * norm.cdf(z)

# BIEN: elegir la dirección ANTES de ver los datos
# "Quiero saber si el nuevo sistema es MEJOR" → unilateral derecho
p = 1 - norm.cdf(z)  # ✅ Elegido antes de recolectar datos
```

### 3. Ignorar la verificación de normalidad

```python
# MAL: aplicar Z-test sin verificar normalidad
z = (x_barra - mu) / (sigma / np.sqrt(n))  # ❌ ¿Y si no es normal?

# BIEN: verificar normalidad primero
from scipy.stats import shapiro
W, p_shapiro = shapiro(df['metrica'])
if p_shapiro >= 0.05:
    print("✅ Datos normales, Z-test válido")
    # Proceder con Z-test
else:
    print("❌ Datos no normales, considerar alternativas")
```

### 4. Confundir p-valor con tamaño del efecto

```python
# MAL: "p = 0.001, ¡el efecto es enorme!"
# El p-valor NO dice qué tan GRANDE es el efecto

# BIEN: reportar p-valor Y tamaño del efecto
print(f"p = {p:.6f} → {'Significativo' if p < 0.05 else 'No significativo'}")
print(f"d = {d:.4f} → {'Grande' if abs(d) >= 0.8 else 'Pequeño/Mediano'}")
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
# La pregunta es directional → debería ser unilateral
z = (x_barra - mu) / (sigma / np.sqrt(n))
p = 2 * norm.cdf(z)  # ❌ No detecta solo mejoras

# BIEN: preguntar "¿mejoró?" → unilateral derecho
p = 1 - norm.cdf(z)  # ✅ Detecta solo mejoras
```

### 7. No redondear el tamaño de muestra al entero superior

```python
# MAL: usar el tamaño de muestra como float
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                         alternative='larger', ratio=0)
# n = 9.66 → usar 9.66 como tamaño de muestra ❌

# BIEN: redondear al entero superior
import math
n = math.ceil(analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                                    alternative='larger', ratio=0))
# n = 10 ✅
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy.stats import norm, shapiro
from statsmodels.stats.power import NormalIndPower
import matplotlib.pyplot as plt
import seaborn as sns

# ============================================
# FLUJO COMPLETO: Prueba Z de una muestra
# ============================================

# ---- PASO 1: Definir el problema del negocio ----
# Ejemplo: ¿El nuevo sistema de recomendación es mejor que el actual?

# ---- PASO 2: Redactar como problema de Ciencia de Datos ----
# ¿El nuevo sistema genera mejoras estadísticamente significativas?

# ---- PASO 3: Definir H₀ y H₁ ----
mu = 0.75       # Media de la población (sistema actual)
sigma = 0.05    # Desviación estándar de la población
# H₀: μ = 0.75 (el nuevo sistema es igual)
# H₁: μ > 0.75 (el nuevo sistema es mejor)

# ---- PASO 4: Definir α ----
alpha = 0.05

# ---- PASO 5: Calcular potencia y tamaño de muestra ----
effect_size = 0.8  # d = (0.79 - 0.75) / 0.05
power = 0.8

analisis = NormalIndPower()
n_minimo = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='larger',
    ratio=0
)
print(f"Tamaño mínimo de muestra: {math.ceil(n_minimo)}")

# ---- PASO 6: Recolectar y preparar datos ----
df = pd.read_csv('dataset_ztest_unilateral.csv')
print(f"Media de la muestra: {df['metrica'].mean():.4f}")

# Verificar normalidad
W, p_shapiro = shapiro(df['metrica'])
print(f"Normalidad (Shapiro-Wilk): p = {p_shapiro:.4f}")

# ---- PASO 7: Aplicar la prueba estadística ----
n = len(df)
x_barra = np.mean(df)

# Calcular z
z = (x_barra - mu) / (sigma / np.sqrt(n))

# Calcular p (unilateral derecho)
p = 1 - norm.cdf(z)

print(f"\nResultados de la prueba Z unilateral:")
print(f"  z = {z:.4f}")
print(f"  p = {p:.6f}")
print(f"  ¿Se rechaza H₀? {'SÍ' if p < alpha else 'NO'}")

# ---- PASO 8: Evaluar tamaño del efecto y potencia ----
d = (np.mean(df) - mu) / sigma
print(f"\nTamaño del efecto (d): {d:.4f}")

potencia = analisis.power(
    effect_size=d,
    nobs1=n,
    alpha=alpha,
    ratio=0,
    alternative='larger'
)
print(f"Potencia actualizada: {potencia:.4f}")

# ---- CONCLUSIÓN ----
if p < alpha:
    print(f"\n✅ Se rechaza H₀: El nuevo sistema es estadísticamente mejor")
    print(f"   Media observada: {x_barra:.4f} vs. Media esperada: {mu}")
    print(f"   Tamaño del efecto: {d:.4f} ({'Grande' if abs(d) >= 0.8 else 'Mediano' if abs(d) >= 0.5 else 'Pequeño'})")
else:
    print(f"\n✅ No se rechaza H₀: No hay evidencia de mejora significativa")
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
│   │   └── Z unilateral derecho
│   │       (H₁: μ > μ₀)
│   │
│   ├── ¿Querés saber si es MENOR?
│   │   └── Z unilateral izquierdo
│   │       (H₁: μ < μ₀)
│   │
│   └── ¿Querés saber si es DIFERENTE?
│       └── Z bilateral
│           (H₁: μ ≠ μ₀)
│
├── ¿Tenés datos de streaming/metrics?
│   └── Verificar normalidad ANTES de la prueba Z
│
├── ¿El tamaño del efecto importa?
│   └── SIEMPRE reportar Cohen's d junto con el p-valor
│
└── ¿No sabés cuál usar?
    └── Si la pregunta es direccional → Unilateral
        Si la pregunta es "¿cambió?" → Bilateral
```

---

## Código rápido de referencia

```python
import numpy as np
from scipy.stats import norm, shapiro
from statsmodels.stats.power import NormalIndPower

# ============================================
# PRUEBA Z UNILATERAL DERECHA
# ============================================
# H₀: μ = μ₀,  H₁: μ > μ₀

mu = 0.75
sigma = 0.05
n = len(df)
x_barra = np.mean(df)

# Verificar normalidad
W, p_shapiro = shapiro(df['metrica'])

# Calcular z y p
z = (x_barra - mu) / (sigma / np.sqrt(n))
p = 1 - norm.cdf(z)

print(f"z = {z:.4f}, p = {p:.6f}")

# Tamaño del efecto
d = (x_barra - mu) / sigma

# Potencia
analisis = NormalIndPower()
potencia = analisis.power(effect_size=d, nobs1=n, alpha=0.05, ratio=0, alternative='larger')

# ============================================
# PRUEBA Z BILATERAL
# ============================================
# H₀: μ = μ₀,  H₁: μ ≠ μ₀

mu = 0.86
sigma = 0.03
n = len(df)
x_barra = np.mean(df)

# Verificar normalidad
W, p_shapiro = shapiro(df['f1_score'])

# Calcular z y p
z = (x_barra - mu) / (sigma / np.sqrt(n))
p = 2 * norm.cdf(z)  # cuando z es negativo

print(f"z = {z:.4f}, p = {p:.6f}")

# Tamaño del efecto
d = (x_barra - mu) / sigma

# Potencia
analisis = NormalIndPower()
potencia = analisis.power(effect_size=abs(d), nobs1=n, alpha=0.05, ratio=0, alternative='two-sided')

# ============================================
# CÁLCULO DE TAMAÑO DE MUESTRA
# ============================================
analisis = NormalIndPower()

# Unilateral
n_uni = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                             alternative='larger', ratio=0)

# Bilateral
n_bi = analisis.solve_power(effect_size=0.333, alpha=0.05, power=0.9,
                            alternative='two-sided', ratio=0)

# ============================================
# VALORES CRÍTICOS
# ============================================
z_critico_uni = norm.ppf(1 - 0.05)     # 1.645
z_critico_bi = norm.ppf(1 - 0.025)     # 1.96
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Definir el problema del negocio | Reunión con stakeholders |
| 2 | Redactar como problema de Ciencia de Datos | Formulación clara |
| 3 | Definir H₀ y H₁ | Conocimiento del dominio |
| 4 | Verificar que σ es conocida | Análisis de datos anteriores |
| 5 | Verificar normalidad de los datos | `scipy.stats.shapiro` |
| 6 | Definir α (nivel de significancia) | Convenção del dominio (0.05 típico) |
| 7 | Definir potencia deseada (1-β) | 0.8 o 0.9 típico |
| 8 | Calcular tamaño del efecto esperado | Cohen's d |
| 9 | Calcular tamaño de muestra mínimo | `statsmodels.NormalIndPower.solve_power` |
| 10 | Recolectar datos (≥ n mínimo) | Experimento / medición |
| 11 | Calcular z | Fórmula manual o scipy |
| 12 | Calcular p-valor | `scipy.stats.norm.cdf` |
| 13 | Comparar p con α | Decisión |
| 14 | Calcular tamaño del efecto observado | Cohen's d con datos reales |
| 15 | Calcular potencia posterior | `statsmodels.NormalIndPower.power` |
| 16 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lecciones 10-12.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- statsmodels Documentation. NormalIndPower — Power analysis for a Z-test.
- SciPy Documentation. `scipy.stats.norm` — Normal continuous random variable.
