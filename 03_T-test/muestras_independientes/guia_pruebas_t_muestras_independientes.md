# Guía de Referencia: Pruebas t de Dos Muestras Independientes

## ¿Qué es?

La prueba t de dos muestras independientes es una herramienta **estadística paramétrica** que determina si las medias de dos grupos independientes son significativamente diferentes. Compara los promedios observados de dos poblaciones separadas usando la **distribución t de Student**.

**Pregunta central:** ¿Las medias de dos grupos independientes son significativamente diferentes (o uno es mayor/menor que el otro)?

**Qué NO hace:**
- No compara una media contra un valor de referencia (para eso está la prueba t de una muestra)
- No compara mediciones pareadas o relacionadas (para eso está la prueba t pareada)
- No asume que los datos son normales (pero SÍ requiere que las distribuciones muestrales de las medias sean normales)
- No reemplaza el juicio experto sobre si el tamaño del efecto es práctico
- **No prueban causalidad** — solo describen si hay diferencia estadísticamente significativa

### La diferencia clave con la prueba t de una muestra

| Característica | Prueba t de una muestra | Prueba t de dos muestras independientes |
|---|---|---|
| **¿Qué compara?** | Una media contra un valor conocido | **Dos medias entre sí** |
| **Datos requeridos** | Una muestra + valor teórico | **Dos muestras independientes** |
| **Grados de libertad** | n - 1 | **Welch's approximation (compleja)** |
| **Hipótesis típica** | H₀: μ = μ₀ | **H₀: μ₁ = μ₂** |
| **Caso de uso** | ¿Supera el modelo X el umbral? | **¿Es mejor el diseño B que el diseño A?** |

> **En la práctica real, la prueba t de dos muestras es la herramienta por defecto para comparar dos grupos.** Es el fundamento de los tests A/B, que están en todos lados: marketing, medicina, UX, fábricas.

### Las dos variantes

- **Unilateral (one-tailed):** Pregunta si un grupo es **mayor** (o **menor**) que el otro
- **Bilateral (two-tailed):** Pregunta si los grupos son **diferentes** (puede ser mayor o menor)

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás comparar dos grupos?
│
├── ¿Los datos son INDEPENDIENTES (sin relación entre observaciones)?
│   ├── SÍ → Prueba t de dos muestras INDEPENDIENTES
│   └── NO (son pareados/relacionados) → Prueba t pareada (paired t-test)
│
├── ¿Ambos grupos son NORMALES o n > 30?
│   ├── SÍ (o n grande por CLT) → Prueba t de muestras independientes
│   └── NO y n chico → Considerar Mann-Whitney U (prueba no paramétrica)
│
├── ¿Querés saber si el grupo B es MAYOR que el A?
│   └── Prueba UNILATERAL derecha (H₁: μ_B > μ_A)
│
├── ¿Querés saber si el grupo B es MENOR que el A?
│   └── Prueba UNILATERAL izquierda (H₁: μ_B < μ_A)
│
└── ¿Querés saber si son DIFERENTES (sin dirección)?
    └── Prueba BILATERAL (H₁: μ_B ≠ μ_A)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| Comparar dos grupos independientes (σ desconocida) | **Prueba t de muestras independientes** |
| Comparar mediciones antes/despues del mismo sujeto | Prueba t pareada |
| Comparar media contra valor teórico | Prueba t de una muestra |
| Dos grupos independientes pero datos no normales y n chico | Mann-Whitney U |
| Comparar si mejoró / empeoró (dirección conocida) | Unilateral |
| Comparar si cambió (sin dirección conocida) | Bilateral |
| Tamaños de muestra muy diferentes | La prueba t de Welch maneja esto bien |

### ¿Por qué importa unilateral vs bilateral?

- **Unilateral** es más potente para detectar un cambio en una dirección específica, pero NO puede detectar cambios en la dirección opuesta
- **Bilateral** es más conservadora (requiere más evidencia) pero detecta cambios en cualquier dirección
- **Elegí la dirección ANTES de ver los datos** — si elegís después, el p-valor no es válido

---

## Comparación entre pruebas

### Comparación directa: tipos de prueba t

| Aspecto | Una Muestra | Dos Muestras Independientes | Dos Muestras Pareadas |
|---|---|---|---|
| **Pregunta** | ¿La media difiere de un valor? | ¿Las medias de dos grupos difieren? | ¿Cambiaron las mediciones? |
| **H₀** | μ = μ₀ | μ₁ = μ₂ | μ_d = 0 |
| **Datos** | 1 muestra + valor teórico | 2 grupos independientes | Mediciones pareadas |
| **Grados de libertad** | n - 1 | Welch's (compleja) | n - 1 |
| **Tamaño del efecto** | d = (x̄ - μ) / s | d = (x̄₁ - x̄₂) / s_pooled | d = x̄_d / s_d |
| **Uso típico** | ¿Supera el umbral? | ¿Es mejor el diseño B? | ¿Mejoró después del tratamiento? |

### t independiente vs Mann-Whitney

| Característica | Prueba t de Welch | Mann-Whitney U |
|---|---|---|
| **Normalidad requerida** | SÍ (o n grande) | NO |
| **Distribución** | t de Student (Welch) | Rangos |
| **Robustez** | Media | Alta |
| **Potencia con datos normales** | Alta | Menor que t |
| **Uso típico** | Datos normales, n grande | Datos no normales, n chico |
| **Hipótesis** | Diferencia de medias | Diferencia de distribuciones |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → no hay evidencia de diferencia | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar el contexto y el tamaño del efecto | ⚠️ |
| p < 0.05 | Se rechaza H₀ → hay diferencia significativa | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de diferencia | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

---

## Matemáticas detrás de la prueba

### Estadístico t

#### Fórmula (para muestras de igual o diferente tamaño)

$$t = \frac{\bar{x}_B - \bar{x}_A}{\sqrt{\frac{s_B^2}{n_B} + \frac{s_A^2}{n_A}}}$$

Donde:
- $\bar{x}_B$ = media de la muestra B (nuevo tratamiento)
- $\bar{x}_A$ = media de la muestra A (control/referencia)
- $s_B$ = desviación estándar de la muestra B
- $s_A$ = desviación estándar de la muestra A
- $n_B$ = tamaño de la muestra B
- $n_A$ = tamaño de la muestra A
- $\sqrt{\frac{s_B^2}{n_B} + \frac{s_A^2}{n_A}}$ = error estándar de la diferencia de medias

> **Nota:** La fórmula usa las desviaciones estándar de cada muestra por separado, NO una desviación estándar combinada. Esto es lo que hace que sea "Welch's t-test" — no asume varianzas iguales.

#### Grados de libertad (Welch-Satterthwaite approximation)

$$df = \frac{\left(\frac{s_A^2}{n_A} + \frac{s_B^2}{n_B}\right)^2}{\frac{\left(\frac{s_A^2}{n_A}\right)^2}{n_A - 1} + \frac{\left(\frac{s_B^2}{n_B}\right)^2}{n_B - 1}}$$

Los grados de libertad en Welch's t-test son siempre **menores** que n₁ + n₂ - 2 (la fórmula clásica que asume varianzas iguales). Esto es más conservador pero más correcto cuando las varianzas no son iguales.

- Si n_A = n_B y s_A ≈ s_B → df ≈ n_A + n_B - 2 (casi idéntico al clásico)
- Si n_A ≠ n_B o s_A ≠ s_B → df < n_A + n_B - 2 (más conservador)

#### Proceso

1. Definir la hipótesis nula (H₀: μ_A = μ_B) y alternativa (H₁)
2. Calcular las medias de ambas muestras ($\bar{x}_A$, $\bar{x}_B$)
3. Calcular las desviaciones estándar de ambas muestras ($s_A$, $s_B$)
4. Calcular el error estándar de la diferencia
5. Calcular t: cuántas desviaciones estándar se aleja la diferencia de medias de cero
6. Comparar t con el valor crítico o calcular el p-valor

#### Ejemplo manual simplificado

```python
# Datos del problema A/B test:
# A: x̄_A = 826.98, s_A = 18.95, n_A = 1200
# B: x̄_B = 831.67, s_B = 17.56, n_B = 1100
# t = (831.67 - 826.98) / √(17.56²/1100 + 18.95²/1200)
# t = 4.69 / √(0.2802 + 0.2990)
# t = 4.69 / √0.5792
# t = 4.69 / 0.761
# t ≈ 6.16
```

### Tamaño del efecto (Cohen's d)

#### Fórmula

$$d = \frac{\bar{x}_B - \bar{x}_A}{s_{pooled}}$$

Donde $s_{pooled}$ es la desviación estándar agrupada (pooled):

$$s_{pooled} = \sqrt{\frac{(n_A - 1) \cdot s_A^2 + (n_B - 1) \cdot s_B^2}{n_A + n_B - 2}}$$

> **Nota:** En la práctica, Pingouin calcula Cohen's d automáticamente. La fórmula anterior es para entender qué hace por detrás.

#### Interpretación de d

| Valor de d | Interpretación |
|---|---|
| |d| < 0.2 | Efecto pequeño / despreciable |
| 0.2 ≤ |d| < 0.5 | Efecto pequeño |
| 0.5 ≤ |d| < 0.8 | Efecto mediano |
| |d| ≥ 0.8 | Efecto grande |

> **IMPORTANTE:** Con muestras muy grandes, un Cohen's d pequeño (ej: 0.25) puede producir un p-valor extremadamente bajo. Esto NO significa que el efecto sea grande — significa que tenés suficiente poder estadístico para detectar diferencias pequeñas.

### Potencia de la prueba

La potencia es la probabilidad de rechazar correctamente H₀ cuando es falsa (1 - β).

#### Cálculo de tamaño de muestra con statsmodels

```python
from statsmodels.stats.power import TTestIndPower

# Para t-test de muestras INDEPENDIENTES se usa TTestIndPower, NO TTestPower
analisis = TTestIndPower()
n = analisis.solve_power(
    effect_size=d,
    power=potencia,
    alpha=alpha,
    ratio=1,                    # Proporción n2/n1 (1 = tamaños iguales)
    alternative='two-sided'     # o 'larger', 'smaller'
)
```

> **IMPORTANTE:** Para t-test de muestras independientes se usa `TTestIndPower`, NO `TTestPower` (que es para una muestra) ni `NormalIndPower` (que es para la prueba Z).

#### Parámetro `ratio`

El parámetro `ratio` indica la proporción n₂/n₁:
- `ratio=1` → ambas muestras tienen el mismo tamaño
- `ratio=0.5` → la segunda muestra es la mitad que la primera
- `ratio=2` → la segunda muestra es el doble que la primera

> **No es requisito que el tamaño de las muestras sea igual**, pero se sugiere que tengan valores similares para maximizar la potencia.

#### Cálculo de potencia posterior con Pingouin

```python
from pingouin import ttest

# Pingouin calcula la potencia automáticamente para muestras independientes
resultado = ttest(x=grupo_B, y=grupo_A, paired=False, alternative='two-sided')
potencia = resultado['power'].values[0]
```

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Comparar dos grupos de un test A/B (Unilateral)**

> Somos Científicos de Datos en una tienda online. El equipo de marketing desarrolló una nueva versión de la página del producto (versión B). ¿Incrementa el gasto promedio por usuario?

```python
import pandas as pd
import numpy as np

# Cargar datos de ambos grupos
dfA = pd.read_csv('gasto_promedio_A.csv')  # Versión actual
dfB = pd.read_csv('gasto_promedio_B.csv')  # Nueva versión

# Estadísticas descriptivas
print(f"Grupo A: n={len(dfA)}, media={dfA['gasto_promedio'].mean():.2f}, "
      f"desv_est={dfA['gasto_promedio'].std():.2f}")
print(f"Grupo B: n={len(dfB)}, media={dfB['gasto_promedio'].mean():.2f}, "
      f"desv_est={dfB['gasto_promedio'].std():.2f}")
```

**Ejemplo 2: Comparar desempeño de dos modelos (Bilateral)**

> Tenemos dos modelos de diagnóstico. ¿Sus F1-scores son diferentes?

```python
# Datos de dos modelos
modelo_A = pd.read_csv('modelo_A_scores.csv')
modelo_B = pd.read_csv('modelo_B_scores.csv')

print(f"Modelo A: media={modelo_A['f1'].mean():.4f}")
print(f"Modelo B: media={modelo_B['f1'].mean():.4f}")
```

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 3: Verificar normalidad en AMBOS grupos**

```python
from scipy.stats import shapiro

# Verificar normalidad en cada grupo por separado
W_A, p_A = shapiro(dfA['gasto_promedio'])
W_B, p_B = shapiro(dfB['gasto_promedio'])

print(f"Grupo A: Shapiro-Wilk p = {p_A:.4f}")
print(f"Grupo B: Shapiro-Wilk p = {p_B:.4f}")

if p_A >= 0.05 and p_B >= 0.05:
    print("✅ Ambos grupos son normales, t-test válido")
elif p_A >= 0.05 or p_B >= 0.05:
    print("⚠️ Un grupo no es normal — considerar Mann-Whitney")
else:
    print("❌ Ningún grupo es normal, usar Mann-Whitney U")
```

**Ejemplo 4: Verificar igualdad de varianzas (opcional)**

```python
from scipy.stats import levenest

# Test de Levene para igualdad de varianzas
stat, p_levene = levene(dfA['gasto_promedio'], dfB['gasto_promedio'])
print(f"Levene test: p = {p_levene:.4f}")

if p_levene >= 0.05:
    print("✅ Varianzas iguales (podrías usar t-test clásico)")
else:
    print("⚠️ Varianzas diferentes → usar Welch's t-test (el default de Pingouin)")
```

---

### 3. En Feature Engineering

**Ejemplo 5: Calcular tamaño del efecto esperado (Unilateral)**

> Queremos detectar si el sitio B incrementa el gasto promedio en al menos 10 USD.

```python
# Parámetros del problema
gasto_A = 827        # Gasto promedio del sitio A (referencia)
incremento = 10      # Incremento que queremos detectar

# Estimamos s basado en datos anteriores
s_estimada = 18

# Tamaño del efecto
d = incremento / s_estimada
print(f"Tamaño del efecto (d): {d:.4f}")  # 0.556 → efecto mediano
```

**Ejemplo 6: Calcular tamaño del efecto esperado (Bilateral)**

> Esperamos que la diferencia máxima entre los sitios sea de 5 USD.

```python
# Parámetros
diferencia = 5
s_estimada = 18

# Tamaño del efecto
d = diferencia / s_estimada
print(f"Tamaño del efecto (d): {d:.4f}")  # 0.278 → efecto pequeño
```

---

### 4. En Selección de Modelos

**Ejemplo 7: Calcular tamaño de muestra para el test A/B (Unilateral)**

> Queremos potencia de 0.95 y tamaño del efecto de 0.8 (grande).

```python
from statsmodels.stats.power import TTestIndPower
import math

# Parámetros
effect_size = 0.8   # Tamaño del efecto (grande)
power = 0.95        # Potencia deseada
alpha = 0.05        # Nivel de significancia
ratio = 1           # Muestras iguales

# Instancia de TTestIndPower (NO TTestPower)
analisis = TTestIndPower()

# Cálculo del tamaño de muestra
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    ratio=ratio,
    alternative='larger'  # Unilateral derecho
)
print(f"Tamaño mínimo de cada muestra: {math.ceil(n)}")  # ~35
```

**Ejemplo 8: Calcular tamaño de muestra con ratio desigual**

> Queremos que la muestra B tenga el doble de observaciones que A.

```python
# Parámetros con ratio desigual
effect_size = 0.5   # Efecto mediano
power = 0.8         # Potencia deseada
alpha = 0.05
ratio = 2           # n_B = 2 * n_A

analisis = TTestIndPower()
n = analisis.solve_power(
    effect_size=effect_size,
    alpha=alpha,
    power=power,
    ratio=ratio,
    alternative='two-sided'
)
print(f"Tamaño mínimo de muestra A: {math.ceil(n)}")
print(f"Tamaño mínimo de muestra B: {math.ceil(n * ratio)}")
```

---

### 5. En Evaluación post-deploy

**Ejemplo 9: Aplicar la prueba t (Unilateral derecha)**

```python
from pingouin import ttest

# Prueba t unilateral derecha: ¿B es mayor que A?
resultado = ttest(
    x=dfB['gasto_promedio'].values,     # Grupo de interés (B)
    y=dfA['gasto_promedio'].values,     # Grupo de referencia (A)
    paired=False,                        # Muestras independientes
    alternative='greater'                # Unilateral derecho
)

print(f"t = {resultado['T'].values[0]:.4f}")
print(f"p = {resultado['p-val'].values[0]:.6f}")
print(f"CI95% = {resultado['CI95%'].values[0]}")
print(f"¿Se rechaza H₀? {'SÍ' if resultado['p-val'].values[0] < 0.05 else 'NO'}")
```

**Ejemplo 10: Aplicar la prueba t (Bilateral)**

```python
from pingouin import ttest

# Prueba t bilateral: ¿A y B son diferentes?
resultado = ttest(
    x=dfB['gasto_promedio'].values,
    y=dfA['gasto_promedio'].values,
    paired=False,
    alternative='two-sided'
)

print(f"t = {resultado['T'].values[0]:.4f}")
print(f"p = {resultado['p-val'].values[0]:.6f}")
print(f"CI95% = {resultado['CI95%'].values[0]}")
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 11: Evaluar tamaño del efecto posterior a la prueba**

```python
import numpy as np

# Tamaño del efecto con los datos reales
d_observado = resultado['cohen-d'].values[0]
print(f'Tamaño del efecto (d) observado: {d_observado:.4f}')

# Interpretación
if abs(d_observado) >= 0.8:
    print("→ Efecto grande")
elif abs(d_observado) >= 0.5:
    print("→ Efecto mediano")
elif abs(d_observado) >= 0.2:
    print("→ Efecto pequeño")
else:
    print("→ Efecto despreciable")
```

**Ejemplo 12: Evaluar factor de Bayes**

```python
bf10 = resultado['BF10'].values[0]
print(f'Factor de Bayes (BF10): {bf10:.2f}')

# Interpretación del BF10
if bf10 >= 100:
    print("→ Evidencia décisiva a favor de H₁")
elif bf10 >= 30:
    print("→ Evidencia muy fuerte a favor de H₁")
elif bf10 >= 10:
    print("→ Evidencia fuerte a favor de H₁")
elif bf10 >= 3:
    print("→ Evidencia moderada a favor de H₁")
elif bf10 >= 1:
    print("→ Evidencia débil a favor de H₁")
else:
    print("→ Evidencia a favor de H₀")
```

---

### 7. En Validación de supuestos

**Ejemplo 13: Calcular potencia posterior**

```python
from pingouin import ttest

# Pingouin calcula la potencia automáticamente
resultado = ttest(
    x=dfB['gasto_promedio'].values,
    y=dfA['gasto_promedio'].values,
    paired=False,
    alternative='greater'
)
potencia = resultado['power'].values[0]
print(f'Potencia actualizada: {potencia:.6f}')
# Resultado: potencia ≈ 0.999996 (prácticamente 100%)
```

**Ejemplo 14: Visualizar la distribución t y la zona de rechazo**

```python
import matplotlib.pyplot as plt
from scipy.stats import t
import numpy as np

# Grados de libertad y valor de t
dof = resultado['dof'].values[0]
tval = resultado['T'].values[0]
alpha = 0.05

# Distribución t para los "dof" obtenidos
t_vals = np.linspace(-7, 7, 1000)
pdf_vals = t.pdf(t_vals, dof)

# Punto crítico para alfa unilateral derecha
t_critico_der = t.ppf(1 - alpha, dof)

# Graficar
plt.figure(figsize=(12, 6))
plt.plot(t_vals, pdf_vals, label=f"Distribución t con {int(dof)} df", color='blue')

# Zona de rechazo derecha
t_rechazo_der = np.linspace(t_critico_der, 7, 100)
plt.fill_between(t_rechazo_der, t.pdf(t_rechazo_der, dof), color='blue', alpha=0.4)

# Estadística t observada
plt.axvline(tval, color='red', linestyle='--', label=f't observado = {tval:.2f}')

plt.title('Prueba t muestras independientes: distribución y zona de rechazo')
plt.xlabel('Valor t')
plt.ylabel('Densidad')
plt.legend()
plt.grid(True)
plt.show()
```

---

## Interpretación de resultados

### Salida de Pingouin (la más completa)

```python
from pingouin import ttest

resultado = ttest(x=grupo_B, y=grupo_A, paired=False, alternative='greater')
print(resultado)
```

Pingouin devuelve una tabla con:

| Campo | Descripción | Ejemplo |
|---|---|---|
| **T** | Estadístico t calculado | 6.15 |
| **dof** | Grados de libertad (Welch's) | 2297.73 |
| **alternative** | Tipo de prueba | greater |
| **p-val** | P-valor | 4.55e-10 |
| **CI95%** | Intervalo de confianza 95% | [3.43, inf] |
| **cohen-d** | Tamaño del efecto | 0.256 |
| **BF10** | Factor de Bayes | 1.175e+07 |
| **power** | Potencia de la prueba | 0.999996 |

### Cómo leer cada campo

**Estadístico t:**
- **t positivo**: el grupo x tiene una media MAYOR que el grupo y
- **t negativo**: el grupo x tiene una media MENOR que el grupo y
- **|t| > valor crítico**: diferencia significativa al nivel de significancia dado

**P-valor:**
- p < 0.05 → se rechaza H₀ (diferencia significativa)
- p ≥ 0.05 → no se rechaza H₀ (no hay evidencia de diferencia)

**Intervalo de confianza 95%:**
- En unilateral derecho: [límite inferior, inf] → el valor real está por encima del límite
- En bilateral: [límite inferior, límite superior] → el valor real está entre ambos

**Cohen's d:**
- d = 0.256 → efecto pequeño (según la escala de Cohen)
- Con muestras grandes, un efecto pequeño puede ser estadísticamente significativo

**Factor de Bayes (BF10):**
- BF10 = 1.175e+07 → evidencia DECISIVA a favor de H₁
- Cuanto mayor el BF10, más fuerte la evidencia contra H₀

**Potencia:**
- potencia = 0.999996 → prácticamente 100% de probabilidad de detectar el efecto real

### Reglas de decisión

```
UNILATERAL DERECHO (α = 0.05):
t > t_crítico   → ❌ Se rechaza H₀ (el grupo x es mayor)
t ≤ t_crítico   → ✅ No se rechaza H₀

BILATERAL (α = 0.05):
|t| > t_crítico → ❌ Se rechaza H₀ (diferencia significativa)
|t| ≤ t_crítico → ✅ No se rechaza H₀
```

### ¿Qué reportar?

```python
# Formato completo (Unilateral derecho)
print(f"Prueba t de dos muestras independientes (unilateral derecha):")
print(f"  H₀: μ_A = μ_B (ambos sitios generan el mismo gasto)")
print(f"  H₁: μ_B > μ_A (el sitio B genera mayor gasto)")
print(f"  α = 0.05")
print(f"  Grupo A: n={nA}, x̄={xA:.2f}, s={sA:.2f}")
print(f"  Grupo B: n={nB}, x̄={xB:.2f}, s={sB:.2f}")
print(f"  t = {t_stat:.4f}")
print(f"  df = {dof:.2f}")
print(f"  p = {p_val:.2e}")
print(f"  CI95% = [{ci_low:.2f}, inf]")
print(f"  Cohen's d = {d:.4f} (efecto {'grande' if abs(d) >= 0.8 else 'mediano' if abs(d) >= 0.5 else 'pequeño'})")
print(f"  BF10 = {bf10:.2e}")
print(f"  Potencia = {potencia:.6f}")
```

### ¿Qué hacer según el resultado?

1. **Rechazás H₀**: Hay evidencia estadística suficiente
   - Reportar el p-valor, el tamaño del efecto y la potencia
   - Evaluar si el tamaño del efecto es prácticamente relevante
   - **Caso del A/B test**: implementar el diseño B si el efecto es relevante para el negocio
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

### 1. Usar TTestPower en vez de TTestIndPower

```python
# MAL: usar TTestPower (es para una muestra)
from statsmodels.stats.power import TTestPower
analisis = TTestPower()
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                         alternative='two-sided')  # ❌ Es para una muestra

# BIEN: usar TTestIndPower (es para dos muestras independientes)
from statsmodels.stats.power import TTestIndPower
analisis = TTestIndPower()
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                         ratio=1, alternative='two-sided')  # ✅ Es para dos muestras
```

### 2. Usar paired=True cuando las muestras son independientes

```python
# MAL: decir que las muestras son pareadas cuando no lo son
resultado = ttest(x=dfB['gasto'], y=dfA['gasto'], paired=True)  # ❌

# BIEN: indicar que son independientes
resultado = ttest(x=dfB['gasto'], y=dfA['gasto'], paired=False)  # ✅
```

### 3. Elegir bilateral o unilateral después de ver los datos

```python
# MAL: mirar los datos y después elegir la dirección
if np.mean(dfB) > np.mean(dfA):
    resultado = ttest(x=dfB['gasto'], y=dfA['gasto'], paired=False, alternative='greater')
else:
    resultado = ttest(x=dfB['gasto'], y=dfA['gasto'], paired=False, alternative='two-sided')

# BIEN: elegir la dirección ANTES de ver los datos
# "Quiero saber si el sitio B es MEJOR" → unilateral derecho
resultado = ttest(x=dfB['gasto'], y=dfA['gasto'], paired=False, alternative='greater')  # ✅
```

### 4. Ignorar la verificación de normalidad en AMBOS grupos

```python
# MAL: verificar normalidad solo en un grupo
W, p = shapiro(dfA['gasto'])  # ❌ ¿Y el grupo B?

# BIEN: verificar normalidad en cada grupo por separado
W_A, p_A = shapiro(dfA['gasto'])
W_B, p_B = shapiro(dfB['gasto'])
if p_A >= 0.05 and p_B >= 0.05:
    print("✅ Ambos grupos normales, t-test válido")
```

### 5. Confundir p-valor con tamaño del efecto

```python
# MAL: "p = 0.00000000045, ¡el efecto es ENORME!"
# El p-valor NO dice qué tan GRANDE es el efecto

# BIEN: reportar p-valor Y tamaño del efecto
print(f"p = {p_val:.2e} → {'Significativo' if p_val < 0.05 else 'No significativo'}")
print(f"d = {d:.4f} → {'Grande' if abs(d) >= 0.8 else 'Mediano' if abs(d) >= 0.5 else 'Pequeño'}")
# Ejemplo: p = 4.55e-10 (muy significativo), d = 0.256 (efecto pequeño)
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

### 7. No redondear el tamaño de muestra al entero superior

```python
# MAL: usar el tamaño de muestra como float
n = analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                         ratio=1, alternative='larger')
# n = 34.52 → usar 34.52 como tamaño de muestra ❌

# BIEN: redondear al entero superior
import math
n = math.ceil(analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.8,
                                   ratio=1, alternative='larger'))
# n = 35 ✅
```

### 8. Asumir que las muestras deben ser del mismo tamaño

```python
# MAL: forzar que las muestras sean del mismo tamaño
# (recortar datos o descartar observaciones)

# BIEN: aceptar tamaños diferentes y usar Welch's t-test
print(f"Grupo A: {len(dfA)} observaciones")
print(f"Grupo B: {len(dfB)} observaciones")
# Welch's t-test maneja tamaños diferentes correctamente
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, levene, t
from statsmodels.stats.power import TTestIndPower
from pingouin import ttest
import matplotlib.pyplot as plt
import math

# ============================================
# FLUJO COMPLETO: Prueba t de dos muestras
# independientes (Test A/B)
# ============================================

# ---- PASO 1: Definir el problema del negocio ----
# Ejemplo: ¿El diseño del sitio B incrementa el gasto promedio
# con respecto al sitio A?

# ---- PASO 2: Redactar como problema de Ciencia de Datos ----
# ¿El diseño del sitio web B incrementa el gasto promedio con
# respecto al sitio A desde el punto de vista estadístico?

# ---- PASO 3: Definir H₀ y H₁ ----
# H₀: μ_A = μ_B (ambos sitios generan el mismo gasto)
# H₁: μ_B > μ_A (el sitio B genera mayor gasto)
# → Unilateral derecho

# ---- PASO 4: Definir α ----
alpha = 0.05

# ---- PASO 5: Calcular potencia y tamaño de muestra ----
d = 0.8             # Tamaño del efecto esperado (grande)
power = 0.95        # Potencia deseada
ratio = 1           # Muestras iguales

analisis = TTestIndPower()  # ← TTestIndPower para muestras independientes
n_minimo = analisis.solve_power(
    effect_size=d,
    alpha=alpha,
    power=power,
    ratio=ratio,
    alternative='larger'  # Unilateral derecho
)
print(f"Tamaño mínimo de cada muestra: {math.ceil(n_minimo)}")

# ---- PASO 6: Recolectar y preparar datos ----
dfA = pd.read_csv('gasto_promedio_A.csv')
dfB = pd.read_csv('gasto_promedio_B.csv')

# Estadísticas descriptivas
nA = len(dfA)
xA = np.mean(dfA['gasto_promedio'])
sA = np.std(dfA['gasto_promedio'])

nB = len(dfB)
xB = np.mean(dfB['gasto_promedio'])
sB = np.std(dfB['gasto_promedio'])

print(f"\nEstadísticas de las muestras:")
print(f"  Grupo A: n={nA}, x̄={xA:.2f}, s={sA:.2f}")
print(f"  Grupo B: n={nB}, x̄={xB:.2f}, s={sB:.2f}")

# Verificar normalidad en AMBOS grupos
W_A, p_A = shapiro(dfA['gasto_promedio'])
W_B, p_B = shapiro(dfB['gasto_promedio'])
print(f"\nNormalidad (Shapiro-Wilk):")
print(f"  Grupo A: p = {p_A:.4f}")
print(f"  Grupo B: p = {p_B:.4f}")

if p_A >= 0.05 and p_B >= 0.05:
    print("✅ Ambos grupos normales, proceder con t-test")
else:
    print("⚠️ Al menos un grupo no es normal, considerar Mann-Whitney")

# ---- PASO 7: Aplicar la prueba estadística ----
resultado = ttest(
    x=dfB['gasto_promedio'].values,     # Grupo de interés
    y=dfA['gasto_promedio'].values,     # Grupo de referencia
    paired=False,                        # Muestras independientes
    alternative='greater'                # Unilateral derecho
)

print(f"\nResultados de la prueba t:")
print(f"  t = {resultado['T'].values[0]:.4f}")
print(f"  df = {resultado['dof'].values[0]:.2f}")
print(f"  p = {resultado['p-val'].values[0]:.2e}")
print(f"  CI95% = {resultado['CI95%'].values[0]}")
print(f"  ¿Se rechaza H₀? {'SÍ' if resultado['p-val'].values[0] < alpha else 'NO'}")

# ---- PASO 8: Evaluar tamaño del efecto y potencia ----
d_observado = resultado['cohen-d'].values[0]
potencia_observada = resultado['power'].values[0]
bf10 = resultado['BF10'].values[0]

print(f"\nTamaño del efecto y potencia:")
print(f"  Cohen's d = {d_observado:.4f}")
print(f"  Potencia = {potencia_observada:.6f}")
print(f"  BF10 = {bf10:.2e}")

# ---- PASO 9: Visualizar (opcional) ----
dof = resultado['dof'].values[0]
tval = resultado['T'].values[0]

t_vals = np.linspace(-7, 7, 1000)
pdf_vals = t.pdf(t_vals, dof)
t_critico = t.ppf(1 - alpha, dof)

plt.figure(figsize=(12, 6))
plt.plot(t_vals, pdf_vals, label=f"Distribución t con {int(dof)} df", color='blue')
t_rechazo = np.linspace(t_critico, 7, 100)
plt.fill_between(t_rechazo, t.pdf(t_rechazo, dof), color='blue', alpha=0.4)
plt.axvline(tval, color='red', linestyle='--', label=f't observado = {tval:.2f}')
plt.title('Prueba t: distribución y zona de rechazo (unilateral derecha)')
plt.xlabel('Valor t')
plt.ylabel('Densidad')
plt.legend()
plt.grid(True)
plt.show()

# ---- CONCLUSIÓN ----
if resultado['p-val'].values[0] < alpha:
    print(f"\n❌ Se rechaza H₀: El sitio B genera un gasto SIGNIFICATIVAMENTE MAYOR")
    print(f"   Media A: {xA:.2f} USD vs Media B: {xB:.2f} USD")
    print(f"   Diferencia: {xB - xA:.2f} USD")
    print(f"   Tamaño del efecto: {d_observado:.4f} (efecto pequeño)")
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
│   │   └── NO → Prueba t de una muestra
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
├── Comparar dos grupos independientes
│   │
│   ├── ¿Los datos son normales o n grande?
│   │   ├── SÍ → Prueba t de dos muestras INDEPENDIENTES
│   │   │       (Welch's t-test)
│   │   └── NO → Mann-Whitney U
│   │
│   ├── ¿Querés saber si B es MAYOR que A?
│   │   └── t unilateral derecho
│   │       (H₁: μ_B > μ_A)
│   │
│   ├── ¿Querés saber si B es MENOR que A?
│   │   └── t unilateral izquierdo
│   │       (H₁: μ_B < μ_A)
│   │
│   └── ¿Querés saber si son DIFERENTES?
│       └── t bilateral
│           (H₁: μ_B ≠ μ_A)
│
├── Comparar mediciones pareadas/relacionadas
│   └── Prueba t pareada (paired t-test)
│
├── ¿Tenés datos no normales con n chico?
│   └── Usá Mann-Whitney U
│
├── ¿El tamaño del efecto importa?
│   └── SIEMPRE reportar Cohen's d junto con el p-valor
│
└── ¿No sabés cuál usar?
    └── ¿Comparás dos grupos independientes?
        ├── SÍ → Prueba t de dos muestras independientes
        └── NO → ¿Comparás una media contra un valor?
            ├── SÍ → Prueba t de una muestra
            └── NO → Considerar otras pruebas
```

---

## Código rápido de referencia

```python
import numpy as np
from scipy.stats import shapiro, t
from statsmodels.stats.power import TTestIndPower
from pingouin import ttest
import math

# ============================================
# PRUEBA t DE DOS MUESTRAS INDEPENDIENTES
# UNILATERAL DERECHA
# ============================================
# H₀: μ_A = μ_B,  H₁: μ_B > μ_A

# Datos
grupo_A = dfA['gasto_promedio'].values
grupo_B = dfB['gasto_promedio'].values

# Verificar normalidad en AMBOS grupos
W_A, p_A = shapiro(grupo_A)
W_B, p_B = shapiro(grupo_B)

# Prueba t unilateral derecha con Pingouin
resultado = ttest(x=grupo_B, y=grupo_A, paired=False, alternative='greater')

t_stat = resultado['T'].values[0]
p_val = resultado['p-val'].values[0]
d = resultado['cohen-d'].values[0]
potencia = resultado['power'].values[0]

print(f"t = {t_stat:.4f}, p = {p_val:.2e}, d = {d:.4f}, power = {potencia:.6f}")

# ============================================
# PRUEBA t DE DOS MUESTRAS INDEPENDIENTES
# BILATERAL
# ============================================
# H₀: μ_A = μ_B,  H₁: μ_B ≠ μ_A

resultado = ttest(x=grupo_B, y=grupo_A, paired=False, alternative='two-sided')

t_stat = resultado['T'].values[0]
p_val = resultado['p-val'].values[0]
d = resultado['cohen-d'].values[0]
potencia = resultado['power'].values[0]

print(f"t = {t_stat:.4f}, p = {p_val:.2e}, d = {d:.4f}, power = {potencia:.6f}")

# ============================================
# CÁLCULO DE TAMAÑO DE MUESTRA
# ============================================
analisis = TTestIndPower()  # ← TTestIndPower, NO TTestPower

# Unilateral
n_uni = math.ceil(analisis.solve_power(effect_size=0.8, alpha=0.05, power=0.95,
                                       ratio=1, alternative='larger'))

# Bilateral
n_bi = math.ceil(analisis.solve_power(effect_size=0.5, alpha=0.05, power=0.8,
                                      ratio=1, alternative='two-sided'))

# ============================================
# VALORES CRÍTICOS (distribución t)
# ============================================
df = resultado['dof'].values[0]
t_critico_uni = t.ppf(1 - 0.05, df)      # Unilateral derecho
t_critico_bi = t.ppf(1 - 0.025, df)       # Bilateral
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Definir el problema del negocio | Reunión con stakeholders |
| 2 | Redactar como problema de Ciencia de Datos | Formulación clara |
| 3 | Definir H₀ y H₁ (elegir dirección ANTES de ver datos) | Conocimiento del dominio |
| 4 | Verificar que las muestras son INDEPENDIENTES | Diseño del experimento |
| 5 | Verificar normalidad de AMBOS grupos | `scipy.stats.shapiro` (en cada grupo) |
| 6 | Definir α (nivel de significancia) | Convención del dominio (0.05 típico) |
| 7 | Definir potencia deseada (1-β) | 0.8 o 0.95 típico |
| 8 | Calcular tamaño del efecto esperado | Cohen's d |
| 9 | Calcular tamaño de muestra mínimo | `statsmodels.TTestIndPower.solve_power` |
| 10 | Definir ratio de tamaños de muestra | `ratio` en TTestIndPower (1 = iguales) |
| 11 | Recolectar datos (≥ n mínimo) | Experimento / medición |
| 12 | Calcular estadístico t | `pingouin.ttest` (con `paired=False`) |
| 13 | Calcular p-valor | `pingouin.ttest` (viene incluido) |
| 14 | Comparar p con α | Decisión |
| 15 | Calcular tamaño del efecto observado | Cohen's d (viene en Pingouin) |
| 16 | Calcular potencia posterior | `pingouin.ttest` (viene incluido) |
| 17 | Evaluar factor de Bayes (BF10) | `pingouin.ttest` (viene incluido) |
| 18 | Visualizar distribución t y zona de rechazo | `matplotlib` + `scipy.stats.t` |
| 19 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lección 7: T-Test de Dos Muestras Independientes.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- Welch, B. L. (1947). The generalization of "Student's" problem when several different population variances are involved. Biometrika, 34(1/2), 28-35.
- statsmodels Documentation. TTestIndPower — Power analysis for two-sample independent t-test.
- Pingouin Documentation. `pingouin.ttest` — T-test for independent samples.
- SciPy Documentation. `scipy.stats.t` — Student's t continuous random variable.
- Vallat, R. (2018). Pingouin: statistics in Python. Journal of Open Source Software, 4(42), 1704.
