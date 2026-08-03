# Guía de Referencia: Pruebas Z de Dos Muestras

## ¿Qué es?

La prueba Z de dos muestras es una herramienta **estadística paramétrica** que determina si las medias de dos poblaciones son significativamente diferentes entre sí. Compara los promedios observados de dos muestras independientes usando la distribución normal estándar, asumiendo que las desviaciones estándar de ambas poblaciones son conocidas.

**Pregunta central:** ¿La media de mi primera muestra es significativamente diferente (o mayor/menor) que la media de mi segunda muestra?

**Qué NO hace:**
- No compara una media contra un valor teórico (para eso está la prueba Z de una muestra)
- No asume que los datos son normales (pero SÍ requiere que las distribuciones muestrales de las medias sean normales)
- No reemplaza el juicio experto sobre si el tamaño del efecto es práctico
- **No prueban causalidad** — solo describen si hay diferencia estadísticamente significativa

### Las dos variantes

- **Unilateral (one-tailed):** Pregunta si la media de un grupo es **mayor** (o **menor**) que la del otro
- **Bilateral (two-tailed):** Pregunta si las medias son **diferentes** (puede ser mayor o menor)

### Diferencia clave con la prueba Z de una muestra

| Aspecto | Prueba Z de una muestra | Prueba Z de dos muestras |
|---|---|---|
| **Compara** | Una media vs. un valor teórico | Dos medias entre sí |
| **Parámetros conocidos** | σ (una desviación estándar) | σ₁ y σ₂ (dos desviaciones estándar) |
| **Hipótesis** | H₀: μ = μ₀ | H₀: μ₁ = μ₂ |
| **Fórmula Z** | z = (x̄ - μ) / (σ/√n) | z = (x̄₁ - x̄₂) / √(σ₁²/n₁ + σ₂²/n₂) |
| **Uso típico** | ¿El nuevo modelo es mejor que el actual? | ¿El modelo A es mejor que el modelo B? |

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás comparar las medias de DOS grupos?
│
│   ├── ¿Conocés las desviaciones estándar de AMBAS poblaciones (σ₁ y σ₂)?
│   │   ├── SÍ → Podés usar Prueba Z de dos muestras
│   │   └── NO → Usá Prueba t de dos muestras (t-test)
│   │
│   ├── ¿Las distribuciones son normales o n > 30 en ambos grupos?
│   │   ├── SÍ (o n grande por CLT) → Podés usar Prueba Z
│   │   └── NO y n chico → Considerá pruebas no paramétricas
│   │
│   ├── ¿Las muestras son independientes?
│   │   ├── SÍ → Prueba Z de dos muestras independientes
│   │   └── NO (muestras pareadas) → Usá prueba t pareada
│   │
│   ├── ¿Querés saber si uno es MAYOR / MENOR?
│   │   └── Prueba UNILATERAL (one-tailed)
│   │
│   └── ¿Querés saber si son DIFERENTES (sin dirección)?
│       └── Prueba BILATERAL (two-tailed)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| Comparar dos medias (σ₁ y σ₂ conocidas) | Prueba Z de dos muestras |
| Comparar si uno mejoró / empeoró vs. otro | Unilateral |
| Comparar si son diferentes (sin dirección) | Bilateral |
| σ desconocida pero n > 30 | Prueba Z de dos muestras (aproximada) |
| σ desconocida y n < 30 | Prueba t de dos muestras |
| Comparar una media contra un valor teórico | Prueba Z de una muestra |
| Comparar dos medias con muestras pareadas | Prueba t pareada |

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
| **H₁** | μ₁ > μ₂ o μ₁ < μ₂ | μ₁ ≠ μ₂ |
| **Valor crítico (α=0.05)** | z = ±1.645 | z = ±1.96 |
| **Requiere** | Conocer la dirección del cambio | Solo saber que hay cambio |
| **Potencia** | Mayor (más fácil rechazar H₀) | Menor (requiere más evidencia) |
| **Riesgo** | No detecta cambios en la otra dirección | Ninguno (detecta ambos lados) |
| **Uso típico** | "¿Mejoró?" "¿Empeoró?" | ¿Cambió? |
| **P-valor** | Una cola | Dos colas |

### Comparación con otras pruebas

| Aspecto | Z dos muestras | Z una muestra | t dos muestras |
|---|---|---|---|
| **Grupos comparados** | 2 independientes | 1 vs. valor teórico | 2 independientes |
| **σ conocida** | σ₁ y σ₂ | σ | No |
| **Fórmula Z/t** | (x̄₁-x̄₂)/√(σ₁²/n₁+σ₂²/n₂) | (x̄-μ)/(σ/√n) | (x̄₁-x̄₂)/√(s₁²/n₁+s₂²/n₂) |
| **Distribución** | Normal Z | Normal Z | Student t |
| **Libertad** | No aplica | No aplica | n₁+n₂-2 |
| **Potencia** | Mayor que t | Mayor que t | Menor que Z |
| **Uso típico** | σ conocida, n grande | σ conocida, n grande | σ desconocida |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → no hay evidencia de diferencia | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar el contexto y el tamaño del efecto | ⚠️ |
| p < 0.05 | Se rechaza H₀ → hay diferencia significativa | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de diferencia | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa cada tipo?

- **Unilateral**: Solo mirás una cola de la distribución. Si el z calculado está en la cola correspondiente a H₁, rechazás H₀. Es como apostar a que un caballo es mejor que otro.
- **Bilateral**: Mirás ambas colas. Si el z calculado está en cualquiera de las dos colas extremas, rechazás H₀. Es como apostar que los caballos son diferentes (sin importar cuál es mejor).

---

## Matemáticas detrás de la prueba

### Estadístico Z

#### Fórmula para dos muestras

$$z = \frac{\bar{x_1} - \bar{x_2}}{\sqrt{\frac{\sigma_1^2}{n_1} + \frac{\sigma_2^2}{n_2}}}$$

Donde:
- $\bar{x_1}$ = media de la muestra 1
- $\bar{x_2}$ = media de la muestra 2
- $\sigma_1$ = desviación estándar de la población 1 (conocida)
- $\sigma_2$ = desviación estándar de la población 2 (conocida)
- $n_1$ = tamaño de la muestra 1
- $n_2$ = tamaño de la muestra 2
- $\sqrt{\frac{\sigma_1^2}{n_1} + \frac{\sigma_2^2}{n_2}}$ = error estándar de la diferencia de medias

#### Proceso

1. Definir la hipótesis nula (H₀: μ₁ = μ₂) y alternativa (H₁)
2. Calcular las medias de ambas muestras ($\bar{x_1}$ y $\bar{x_2}$)
3. Calcular el error estándar de la diferencia ($\sqrt{\frac{\sigma_1^2}{n_1} + \frac{\sigma_2^2}{n_2}}$)
4. Calcular z: cuántas desviaciones estándar se aleja la diferencia de medias de 0
5. Comparar z con el valor crítico o calcular el p-valor

#### Ejemplo manual simplificado

```python
# Datos del problema: σ₁ = 0.8, σ₂ = 0.7, n₁ = n₂ = 100
# x̄₁ = 7.386, x̄₂ = 6.866
# z = (7.386 - 6.866) / √(0.8²/100 + 0.7²/100)
# z = 0.52 / √(0.0064 + 0.0049)
# z = 0.52 / √0.0113
# z = 0.52 / 0.1063
# z = 4.89
```

### Tamaño del efecto (Cohen's d para dos muestras)

#### Fórmula

$$d = \frac{\bar{x_1} - \bar{x_2}}{s_{pool}}$$

Donde:
- $d$ = tamaño del efecto (Cohen's d)
- $\bar{x_1}$ = media de la muestra 1
- $\bar{x_2}$ = media de la muestra 2
- $s_{pool}$ = desviación estándar agrupada (pooled standard deviation)

La desviación estándar agrupada se calcula como:

$$s_{pool} = \sqrt{\frac{(n_1-1)s_1^2 + (n_2-1)s_2^2}{n_1 + n_2 - 2}}$$

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

Para dos muestras con tamaño igual (n₁ = n₂ = n):

$$n = \left( \frac{z_\alpha + z_\beta}{d} \right)^2 \times 2$$

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
    nobs1=n,           # Tamaño de la muestra 1
    alpha=alpha,
    ratio=1,           # ratio = n₂/n₁ (1 = tamaños iguales)
    alternative='larger' # 'larger', 'smaller', o 'two-sided'
)
```

**Nota importante sobre `ratio`:**
- `ratio=1` significa que n₁ = n₂ (tamaños iguales)
- `ratio=2` significa que n₂ = 2 × n₁ (la segunda muestra es el doble)
- En la prueba de una muestra, se usaba `ratio=0`

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Comparar dos estrategias de segmentación (Unilateral)**

> Una empresa de e-commerce prueba dos estrategias de segmentación de clientes. ¿La segmentación 1 tiene mejor tasa de conversión que la segmentación 2?

```python
import pandas as pd
import numpy as np
from scipy.stats import norm, shapiro
from statsmodels.stats.power import NormalIndPower

# Datos de las dos estrategias
df = pd.read_csv('z_test_dos_muestras.csv')
print(f"Media Segmentación 1: {df['segm1'].mean():.4f}")
print(f"Media Segmentación 2: {df['segm2'].mean():.4f}")
print(f"Diferencia: {df['segm1'].mean() - df['segm2'].mean():.4f}")
```

**Ejemplo 2: Comparar dos modelos (Bilateral)**

> Dos modelos de diagnóstico cardíaco tienen F1 scores diferentes. ¿Son estadísticamente diferentes?

```python
# Supongamos datos de dos modelos
df_modelo_a = pd.read_csv('modelo_a_f1.csv')
df_modelo_b = pd.read_csv('modelo_b_f1.csv')
print(f"Media Modelo A: {df_modelo_a['f1_score'].mean():.4f}")
print(f"Media Modelo B: {df_modelo_b['f1_score'].mean():.4f}")
```

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 3: Verificar normalidad en ambas muestras**

```python
from scipy.stats import shapiro

# Verificar normalidad de ambas muestras
W1, p_shapiro1 = shapiro(df['segm1'])
W2, p_shapiro2 = shapiro(df['segm2'])

print(f"Shapiro-Wilk Muestra 1: W = {W1:.4f}, p = {p_shapiro1:.6f}")
print(f"Shapiro-Wilk Muestra 2: W = {W2:.4f}, p = {p_shapiro2:.6f}")

if p_shapiro1 >= 0.05 and p_shapiro2 >= 0.05:
    print("✅ Ambas muestras son consistentes con distribución normal")
else:
    print("❌ Al menos una muestra NO es normal — considerar pruebas no paramétricas")
```

**Ejemplo 4: Verificar normalidad en datos bilaterales**

```python
W1, p_shapiro1 = shapiro(df_modelo_a['f1_score'])
W2, p_shapiro2 = shapiro(df_modelo_b['f1_score'])

print(f"Shapiro-Wilk Modelo A: p = {p_shapiro1:.6f}")
print(f"Shapiro-Wilk Modelo B: p = {p_shapiro2:.6f}")

if p_shapiro1 >= 0.05 and p_shapiro2 >= 0.05:
    print("✅ Ambos modelos tienen distribución normal")
else:
    print("❌ Normalidad no verificada")
```

---

### 3. En Feature Engineering

**Ejemplo 5: Calcular tamaño del efecto esperado (Unilateral)**

> Queremos detectar si la segmentación 1 es al menos 0.5 puntos mejor que la segmentación 2.

```python
# Parámetros del problema
sigma1 = 0.8   # Desviación estándar conocida de la población 1
sigma2 = 0.7   # Desviación estándar conocida de la población 2
diferencia_esperada = 0.5  # Diferencia que queremos detectar

# Tamaño del efecto (usando desviación agrupada aproximada)
s_pool = np.sqrt((sigma1**2 + sigma2**2) / 2)
d = diferencia_esperada / s_pool
print(f"Tamaño del efecto (d): {d}")  # ~0.65 → efecto mediano
```

**Ejemplo 6: Calcular tamaño del efecto esperado (Bilateral)**

> Esperamos que los modelos difieran en al menos 0.3 puntos de F1.

```python
# Parámetros del problema
sigma1 = 0.05   # Desviación del Modelo A
sigma2 = 0.06   # Desviación del Modelo B
diferencia_esperada = 0.3  # Diferencia que queremos detectar

# Tamaño del efecto
s_pool = np.sqrt((sigma1**2 + sigma2**2) / 2)
d = diferencia_esperada / s_pool
print(f"Tamaño del efecto (d): {d}")  # ~5.45 → efecto muy grande
```

---

### 4. En Selección de Modelos

**Ejemplo 7: Calcular tamaño de muestra para el test (Unilateral)**

> Queremos potencia de 0.95 y tamaño del efecto de 0.8, con muestras iguales.

```python
from statsmodels.stats.power import NormalIndPower

# Parámetros
effect_size = 0.8   # Tamaño del efecto
power = 0.95         # Potencia deseada
alpha = 0.05        # Nivel de significancia
ratio = 1           # Muestras iguales (n1 = n2)

# Instancia de NormalIndPower
analisis = NormalIndPower()

# Cálculo del tamaño de muestra
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='larger',  # Unilateral derecho
    ratio=ratio
)
print(f"Tamaño mínimo de cada muestra: {n:.2f}")  # ~33.82 → necesita 34 por muestra
```

**Ejemplo 8: Calcular tamaño de muestra para el test (Bilateral)**

> Queremos potencia de 0.9 y tamaño del efecto de 0.5.

```python
from statsmodels.stats.power import NormalIndPower

# Parámetros
effect_size = 0.5   # Tamaño del efecto
power = 0.9         # Potencia deseada
alpha = 0.05        # Nivel de significancia
ratio = 1           # Muestras iguales

# Instancia de NormalIndPower
analisis = NormalIndPower()

# Cálculo del tamaño de muestra
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='two-sided',  # Bilateral
    ratio=ratio
)
print(f"Tamaño mínimo de cada muestra: {n:.2f}")  # ~52.54 → necesita 53 por muestra
```

---

### 5. En Evaluación post-deploy

**Ejemplo 9: Aplicar la prueba Z de dos muestras (Unilateral)**

```python
from scipy.stats import norm
import numpy as np

# Parámetros de las poblaciones (supuestos conocidos)
sigma1 = 0.8
sigma2 = 0.7

# Datos de las muestras
n1 = n2 = len(df)
x1 = np.mean(df['segm1'])
x2 = np.mean(df['segm2'])

# Calcular z
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)

# Calcular p (unilateral derecho: p = 1 - P(Z <= z))
p = 1 - norm.cdf(z)

print(f'z: {z}')
print(f'p: {p}')
# Resultado: z ≈ 4.89, p ≈ 4.97e-07
```

**Ejemplo 10: Aplicar la prueba Z de dos muestras (Bilateral)**

```python
from scipy.stats import norm
import numpy as np

# Parámetros de las poblaciones
sigma1 = 0.05
sigma2 = 0.06

# Datos de las muestras
n1 = len(df_modelo_a)
n2 = len(df_modelo_b)
x1 = np.mean(df_modelo_a['f1_score'])
x2 = np.mean(df_modelo_b['f1_score'])

# Calcular z
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)

# Calcular p (bilateral)
p = 2 * (1 - norm.cdf(abs(z)))

print(f'z: {z}')
print(f'p: {p}')
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 11: Evaluar tamaño del efecto posterior a la prueba (Unilateral)**

```python
from pingouin import compute_effsize

# Tamaño del efecto con datos reales
d = compute_effsize(df['segm1'], df['segm2'], eftype='cohen')
print(f'Tamaño del efecto (d) actualizado: {d:.4f}')
# Resultado: d ≈ 0.71 → efecto mediano-grande
```

**Ejemplo 12: Evaluar tamaño del efecto posterior a la prueba (Bilateral)**

```python
from pingouin import compute_effsize

# Tamaño del efecto con datos reales
d = compute_effsize(df_modelo_a['f1_score'], df_modelo_b['f1_score'], eftype='cohen')
print(f'Tamaño del efecto (d) actualizado: {d:.4f}')
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
    ratio=1,           # ratio = 1 para dos muestras iguales
    alternative='larger'
)
print(f'Potencia actualizada: {potencia:.4f}')
# Resultado: potencia ≈ 0.9996
```

**Ejemplo 14: Calcular potencia posterior (Bilateral)**

```python
from statsmodels.stats.power import NormalIndPower

n = len(df_modelo_a)
alpha = 0.05

analisis = NormalIndPower()

potencia = analisis.power(
    effect_size=abs(d),
    nobs1=n,
    alpha=alpha,
    ratio=1,           # ratio = 1 para dos muestras iguales
    alternative='two-sided'
)
print(f'Potencia actualizada: {potencia:.4f}')
```

---

## Interpretación de resultados

### Salida del cálculo de z

```python
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)
# z = 4.89 (unilateral) o z = -2.15 (bilateral)
```

**Cómo leerlo:**
- **z positivo**: la media de la muestra 1 está POR ARRIBA de la media de la muestra 2
- **z negativo**: la media de la muestra 1 está POR ABAJO de la media de la muestra 2
- **|z| > 1.96** (bilateral) o **|z| > 1.645** (unilateral): diferencia significativa al nivel de 0.05

### Salida del cálculo de p

```python
# Unilateral derecho
p = 1 - norm.cdf(z)

# Unilateral izquierdo
p = norm.cdf(z)

# Bilateral
p = 2 * (1 - norm.cdf(abs(z)))
```

### Reglas de decisión

```
UNILATERAL DERECHO (α = 0.05):
z > 1.645  → ❌ Se rechaza H₀ (μ₁ es mayor que μ₂)
z ≤ 1.645  → ✅ No se rechaza H₀

UNILATERAL IZQUIERDO (α = 0.05):
z < -1.645 → ❌ Se rechaza H₀ (μ₁ es menor que μ₂)
z ≥ -1.645 → ✅ No se rechaza H₀

BILATERAL (α = 0.05):
z > 1.96   → ❌ Se rechaza H₀ (diferencia significativa)
z < -1.96  → ❌ Se rechaza H₀ (diferencia significativa)
-1.96 ≤ z ≤ 1.96 → ✅ No se rechaza H₀
```

### ¿Qué reportar?

```python
# Formato completo (Unilateral)
print(f"Prueba Z de dos muestras unilateral:")
print(f"  σ₁ = {sigma1}, σ₂ = {sigma2}")
print(f"  n₁ = {n1}, n₂ = {n2}")
print(f"  x̄₁ = {x1:.4f}, x̄₂ = {x2:.4f}")
print(f"  z = {z:.4f}")
print(f"  p = {p:.6f}")
print(f"  d = {d:.4f}")
print(f"  Potencia = {potencia:.4f}")
```

```python
# Formato completo (Bilateral)
print(f"Prueba Z de dos muestras bilateral:")
print(f"  σ₁ = {sigma1}, σ₂ = {sigma2}")
print(f"  n₁ = {n1}, n₂ = {n2}")
print(f"  x̄₁ = {x1:.4f}, x̄₂ = {x2:.4f}")
print(f"  z = {z:.4f}")
print(f"  p = {p:.6f}")
print(f"  d = {d:.4f}")
print(f"  Potencia = {potencia:.4f}")
```

### ¿Qué hacer según el resultado?

1. **Rechazás H₀**: Hay evidencia estadística suficiente de diferencia
   - Reportar el p-valor, el tamaño del efecto y la potencia
   - Evaluar si el tamaño del efecto es prácticamente relevante
2. **No rechazás H₀**: No hay evidencia suficiente de diferencia
   - NO significa que las medias sean iguales
   - Verificar si la potencia fue suficiente
   - Verificar si el tamaño de muestra fue adecuado
3. **Zona gris (0.05 < p < 0.10)**:
   - Considerar el contexto del negocio
   - Reportar el intervalo de confianza
   - Replicar con más datos si es posible

---

## Errores comunes

### 1. Usar prueba Z de una muestra cuando tenés dos grupos

```python
# MAL: tratar los dos grupos como uno solo
todos_los_datos = pd.concat([df['segm1'], df['segm2']])
z = (np.mean(todos_los_datos) - valor_teorico) / (sigma / np.sqrt(len(todos_los_datos)))  # ❌

# BIEN: usar prueba Z de dos muestras
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)  # ✅
```

### 2. Usar σ desconocida (usar la desviación de la muestra)

```python
# MAL: usar la desviación estándar de la muestra cuando no conocés σ
s1 = df['segm1'].std()
s2 = df['segm2'].std()
z = (x1 - x2) / np.sqrt(s1**2/n1 + s2**2/n2)  # ❌ Debería ser t-test

# BIEN: si σ es desconocida, usar t-test de dos muestras
from scipy.stats import ttest_ind
t_stat, p = ttest_ind(df['segm1'], df['segm2'])
```

### 3. Elegir bilateral o unilateral después de ver los datos

```python
# MAL: mirar los datos y después elegir la dirección
x1 = np.mean(df['segm1'])
x2 = np.mean(df['segm2'])
if x1 > x2:
    # "Ah, ¡segmentación 1 es mayor! Uso unilateral" ❌
    p = 1 - norm.cdf(z)
else:
    # "Son parejos, uso bilateral" ❌
    p = 2 * (1 - norm.cdf(abs(z)))

# BIEN: elegir la dirección ANTES de ver los datos
# "Quiero saber si la segmentación 1 es MEJOR" → unilateral derecho
p = 1 - norm.cdf(z)  # ✅ Elegido antes de recolectar datos
```

### 4. Ignorar la verificación de normalidad en ambas muestras

```python
# MAL: aplicar Z-test sin verificar normalidad en ninguna muestra
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)  # ❌ ¿Y si no son normales?

# BIEN: verificar normalidad en AMBAS muestras
from scipy.stats import shapiro
W1, p1 = shapiro(df['segm1'])
W2, p2 = shapiro(df['segm2'])
if p1 >= 0.05 and p2 >= 0.05:
    print("✅ Ambas muestras normales, Z-test válido")
    # Proceder con Z-test
else:
    print("❌ Normalidad no verificada, considerar alternativas")
```

### 5. Confundir p-valor con tamaño del efecto

```python
# MAL: "p = 0.001, ¡el efecto es enorme!"
# El p-valor NO dice qué tan GRANDE es la diferencia

# BIEN: reportar p-valor Y tamaño del efecto
print(f"p = {p:.6f} → {'Significativo' if p < 0.05 else 'No significativo'}")
print(f"d = {d:.4f} → {'Grande' if abs(d) >= 0.8 else 'Mediano' if abs(d) >= 0.5 else 'Pequeño'}")
```

### 6. No reportar la potencia

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

### 7. Usar bilateral cuando la pregunta es direccional

```python
# MAL: preguntar "¿segmentación 1 es mejor?" pero usar bilateral
# La pregunta es direccional → debería ser unilateral
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)
p = 2 * (1 - norm.cdf(abs(z)))  # ❌ No detecta solo mejoras

# BIEN: preguntar "¿es mejor?" → unilateral derecho
p = 1 - norm.cdf(z)  # ✅ Detecta solo mejoras
```

### 8. No redondear el tamaño de muestra al entero superior

```python
# MAL: usar el tamaño de muestra como float
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.95,
                         alternative='larger', ratio=1)
# n = 33.82 → usar 33.82 como tamaño de muestra ❌

# BIEN: redondear al entero superior
import math
n = math.ceil(analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.95,
                                    alternative='larger', ratio=1))
# n = 34 ✅
```

### 9. Usar ratio incorrecto en NormalIndPower

```python
# MAL: usar ratio=0 para dos muestras (eso es para una muestra)
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.95,
                         alternative='larger', ratio=0)  # ❌

# BIEN: usar ratio=1 para dos muestras iguales
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.95,
                         alternative='larger', ratio=1)  # ✅
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy.stats import norm, shapiro
from statsmodels.stats.power import NormalIndPower
from pingouin import compute_effsize
import matplotlib.pyplot as plt
import math

# ============================================
# FLUJO COMPLETO: Prueba Z de dos muestras
# ============================================

# ---- PASO 1: Definir el problema del negocio ----
# Ejemplo: ¿La segmentación 1 tiene mejor tasa de conversión que la segmentación 2?

# ---- PASO 2: Redactar como problema de Ciencia de Datos ----
# ¿El modelo de segmentación 1 tiene una mejor tasa de conversión que la del modelo
# de segmentación 2 desde el punto de vista estadístico?

# ---- PASO 3: Definir H₀ y H₁ ----
sigma1 = 0.8    # Desviación estándar conocida de la población 1
sigma2 = 0.7    # Desviación estándar conocida de la población 2
# H₀: μ₁ = μ₂ (ambas segmentaciones tienen las mismas tasas)
# H₁: μ₁ > μ₂ (la segmentación 1 es mejor)

# ---- PASO 4: Definir α ----
alpha = 0.05

# ---- PASO 5: Calcular potencia y tamaño de muestra ----
effect_size = 0.8  # Tamaño del efecto esperado
power = 0.95       # Potencia deseada
ratio = 1          # Muestras iguales (n1 = n2)

analisis = NormalIndPower()
n_minimo = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    alternative='larger',
    ratio=ratio
)
print(f"Tamaño mínimo de cada muestra: {math.ceil(n_minimo)}")

# ---- PASO 6: Recolectar y preparar datos ----
df = pd.read_csv('z_test_dos_muestras.csv')
print(f"Media Segmentación 1: {df['segm1'].mean():.4f}")
print(f"Media Segmentación 2: {df['segm2'].mean():.4f}")

# Verificar normalidad en ambas muestras
W1, p_shapiro1 = shapiro(df['segm1'])
W2, p_shapiro2 = shapiro(df['segm2'])
print(f"Normalidad Muestra 1 (Shapiro-Wilk): p = {p_shapiro1:.4f}")
print(f"Normalidad Muestra 2 (Shapiro-Wilk): p = {p_shapiro2:.4f}")

# ---- PASO 7: Aplicar la prueba estadística ----
n1 = n2 = len(df)
x1 = np.mean(df['segm1'])
x2 = np.mean(df['segm2'])

# Calcular z
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)

# Calcular p (unilateral derecho)
p = 1 - norm.cdf(z)

print(f"\nResultados de la prueba Z de dos muestras:")
print(f"  z = {z:.4f}")
print(f"  p = {p:.6f}")
print(f"  ¿Se rechaza H₀? {'SÍ' if p < alpha else 'NO'}")

# ---- PASO 8: Evaluar tamaño del efecto y potencia ----
d = compute_effsize(df['segm1'], df['segm2'], eftype='cohen')
print(f"\nTamaño del efecto (d): {d:.4f}")

potencia = analisis.power(
    effect_size=d,
    nobs1=n1,
    alpha=alpha,
    ratio=ratio,
    alternative='larger'
)
print(f"Potencia actualizada: {potencia:.4f}")

# ---- CONCLUSIÓN ----
if p < alpha:
    print(f"\n✅ Se rechaza H₀: La segmentación 1 es estadísticamente mejor")
    print(f"   Media Segmentación 1: {x1:.4f} vs. Media Segmentación 2: {x2:.4f}")
    print(f"   Tamaño del efecto: {d:.4f} ({'Grande' if abs(d) >= 0.8 else 'Mediano' if abs(d) >= 0.5 else 'Pequeño'})")
else:
    print(f"\n✅ No se rechaza H₀: No hay evidencia de diferencia significativa")
```

---

## Resumen: cuándo usar cada prueba

```
¿Qué querés hacer?
│
├── Comparar DOS medias entre sí
│   │
│   ├── ¿Conocés σ₁ y σ₂ de ambas poblaciones?
│   │   ├── SÍ → Prueba Z de dos muestras
│   │   └── NO → Prueba t de dos muestras
│   │
│   ├── ¿Las muestras son independientes?
│   │   ├── SÍ → Prueba Z/t de dos muestras independientes
│   │   └── NO (pareadas) → Prueba t pareada
│   │
│   ├── ¿Querés saber si uno es MAYOR?
│   │   └── Z dos muestras unilateral derecho
│   │       (H₁: μ₁ > μ₂)
│   │
│   ├── ¿Querés saber si uno es MENOR?
│   │   └── Z dos muestras unilateral izquierdo
│   │       (H₁: μ₁ < μ₂)
│   │
│   └── ¿Querés saber si son DIFERENTES?
│       └── Z dos muestras bilateral
│           (H₁: μ₁ ≠ μ₂)
│
├── Comparar UNA media contra un valor teórico
│   └── Usá Prueba Z de UNA muestra
│
├── ¿Tenés datos de streaming/metrics?
│   └── Verificar normalidad ANTES de la prueba Z
│
├── ¿El tamaño del efecto importa?
│   └── SIEMPRE reportar Cohen's d junto con el p-valor
│
└── ¿No sabés cuál usar?
    └── Si la pregunta es "¿A es mejor que B?" → Unilateral
        Si la pregunta es "¿son diferentes?" → Bilateral
```

---

## Código rápido de referencia

```python
import numpy as np
from scipy.stats import norm, shapiro
from statsmodels.stats.power import NormalIndPower
from pingouin import compute_effsize

# ============================================
# PRUEBA Z DE DOS MUESTRAS UNILATERAL DERECHA
# ============================================
# H₀: μ₁ = μ₂,  H₁: μ₁ > μ₂

sigma1 = 0.8
sigma2 = 0.7
n1 = len(df)
n2 = len(df)
x1 = np.mean(df['segm1'])
x2 = np.mean(df['segm2'])

# Verificar normalidad
W1, p1 = shapiro(df['segm1'])
W2, p2 = shapiro(df['segm2'])

# Calcular z y p
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)
p = 1 - norm.cdf(z)

print(f"z = {z:.4f}, p = {p:.6f}")

# Tamaño del efecto
d = compute_effsize(df['segm1'], df['segm2'], eftype='cohen')

# Potencia
analisis = NormalIndPower()
potencia = analisis.power(effect_size=d, nobs1=n1, alpha=0.05, ratio=1, alternative='larger')

# ============================================
# PRUEBA Z DE DOS MUESTRAS BILATERAL
# ============================================
# H₀: μ₁ = μ₂,  H₁: μ₁ ≠ μ₂

sigma1 = 0.05
sigma2 = 0.06
n1 = len(df_modelo_a)
n2 = len(df_modelo_b)
x1 = np.mean(df_modelo_a['f1_score'])
x2 = np.mean(df_modelo_b['f1_score'])

# Verificar normalidad
W1, p1 = shapiro(df_modelo_a['f1_score'])
W2, p2 = shapiro(df_modelo_b['f1_score'])

# Calcular z y p
z = (x1 - x2) / np.sqrt(sigma1**2/n1 + sigma2**2/n2)
p = 2 * (1 - norm.cdf(abs(z)))

print(f"z = {z:.4f}, p = {p:.6f}")

# Tamaño del efecto
d = compute_effsize(df_modelo_a['f1_score'], df_modelo_b['f1_score'], eftype='cohen')

# Potencia
analisis = NormalIndPower()
potencia = analisis.power(effect_size=abs(d), nobs1=n1, alpha=0.05, ratio=1, alternative='two-sided')

# ============================================
# CÁLCULO DE TAMAÑO DE MUESTRA
# ============================================
analisis = NormalIndPower()

# Unilateral
n_uni = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.95,
                             alternative='larger', ratio=1)

# Bilateral
n_bi = analisis.solve_power(effect_size=0.5, alpha=0.05, power=0.9,
                            alternative='two-sided', ratio=1)

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
| 3 | Definir H₀ y H₁ (μ₁ vs μ₂) | Conocimiento del dominio |
| 4 | Verificar que σ₁ y σ₂ son conocidas | Análisis de datos anteriores |
| 5 | Verificar normalidad en AMBAS muestras | `scipy.stats.shapiro` × 2 |
| 6 | Verificar independencia de muestras | Diseño experimental |
| 7 | Definir α (nivel de significancia) | Convención del dominio (0.05 típico) |
| 8 | Definir potencia deseada (1-β) | 0.8 o 0.9 típico |
| 9 | Definir ratio entre muestras | 1 para tamaños iguales |
| 10 | Calcular tamaño del efecto esperado | Cohen's d para dos muestras |
| 11 | Calcular tamaño de muestra mínimo | `statsmodels.NormalIndPower.solve_power` |
| 12 | Recolectar datos (≥ n mínimo por grupo) | Experimento / medición |
| 13 | Calcular z | Fórmula manual o scipy |
| 14 | Calcular p-valor | `scipy.stats.norm.cdf` |
| 15 | Comparar p con α | Decisión |
| 16 | Calcular tamaño del efecto observado | `pingouin.compute_effsize` |
| 17 | Calcular potencia posterior | `statsmodels.NormalIndPower.power` |
| 18 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lecciones 10-12.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- statsmodels Documentation. NormalIndPower — Power analysis for a Z-test.
- SciPy Documentation. `scipy.stats.norm` — Normal continuous random variable.
- Pingouin Documentation. `compute_effsize` — Effect size computation.
