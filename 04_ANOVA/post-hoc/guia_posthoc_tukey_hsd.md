# Guía de Referencia: Pruebas Post-hoc en ANOVA — Tukey HSD

## ¿Qué es?

La prueba de Tukey HSD (Honestly Significant Difference) es una herramienta **estadística post-hoc** que se aplica **después** de una ANOVA significativa para determinar **qué pares de grupos** difieren entre sí. Mientras que la ANOVA solo dice "hay al menos una diferencia", Tukey HSD identifica **dónde están** esas diferencias.

**Pregunta central:** ¿Entre qué pares de grupos específicos existen diferencias estadísticamente significativas?

**Qué NO hace:**
- No reemplaza la ANOVA — se aplica SOLO después de que la ANOVA rechaza H₀
- No prueba hipótesis nuevas — solo explora las diferencias que la ANOVA ya detectó
- No corrige por comparaciones múltiples de forma diferente a otros métodos (usa el rango estudentizado)
- No funciona bien con diseños muy desbalanceados
- **No prueban causalidad** — solo describen qué pares difieren

### ¿Por qué Tukey y no múltiples pruebas t?

Si comparáramos cada par de grupos con pruebas t individuales, acumularíamos errores tipo I. Con 3 grupos son 3 comparaciones; con 6 grupos son 15 comparaciones. Tukey HSD controla el **error familiar** (FWER) manteniendo α global en 0.05.

---

## Cuándo usarla

### Flujo de decisión

```
¿Completaste una ANOVA y rechazaste H₀?
│
│   ├── ¿Tenés 2 grupos?
│   │   └── NO necesitás post-hoc (la ANOVA ya te dice cuál difiere)
│   │
│   ├── ¿Tenés 3+ grupos?
│   │   ├── SÍ → Usá Tukey HSD
│   │   └── NO → No aplica
│   │
│   ├── ¿Querés identificar qué pares de grupos difieren?
│   │   ├── SÍ → Tukey HSD
│   │   └── NO → Solo reportar que hay diferencia (ANOVA)
│   │
│   └── ¿Tenés un ANOVA multifactorial?
│       ├── Efecto principal significativo → Tukey por factor
│       └── Interacción significativa → Tukey por subgrupos (combinaciones)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| ANOVA de 1 factor significativo, 3+ grupos | Tukey HSD |
| ANOVA multifactorial, efecto principal significativo | Tukey por factor |
| ANOVA multifactorial, interacción significativa | Tukey por subgrupos |
| Querés controlar el error familiar estrictamente | Tukey HSD |
| Diseños muy desbalanceados | Games-Howell (alternativa) |
| Comparaciones específicas planeadas (no post-hoc) | Contrastes ortogonales |

### ¿Por qué importa el post-hoc?

- **ANOVA solo dice**: "Hay al menos una diferencia significativa entre los grupos"
- **Tukey HSD dice**: "La diferencia está entre el grupo A y el grupo C, y entre el grupo B y el grupo C"
- Sin post-hoc, no sabés DÓNDE están las diferencias — solo sabés que EXISTEN

---

## Comparación entre métodos post-hoc

### Comparación directa

| Método | Control de error | Uso típico | Desventaja |
|---|---|---|---|
| **Tukey HSD** | FWER (error familiar) | Comparaciones pareadas, diseños balanceados | Menos potente con diseños desbalanceados |
| **Bonferroni** | FWER | Pocas comparaciones planeadas | Muy conservador con muchas comparaciones |
| **Sidak** | FWER | Similar a Bonferroni pero ligeramente menos conservador | Similar a Bonferroni |
| **Games-Howell** | FWER | Diseños desbalanceados, varianzas desiguales | No tan común en software |
| **Dunnett** | FWER | Comparar todos contra un control | Solo compara contra un grupo de referencia |
| **Scheffé** | FWER | Comparaciones complejas, subgrupos | Muy conservador |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza → no hay diferencia significativa entre este par | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → diferencia marginal | ⚠️ |
| p < 0.05 | Se rechaza → diferencia significativa entre este par | ❌ |
| p < 0.01 | Se rechaza → fuerte evidencia de diferencia | ❌❌ |
| p < 0.001 | Se rechaza → evidencia muy fuerte | ❌❌❌ |

### Interpretación del tamaño del efecto (Cohen's d)

| Valor de d | Interpretación |
|---|---|
| |d| < 0.2 | Efecto pequeño / despreciable |
| 0.2 ≤ |d| < 0.5 | Efecto pequeño |
| 0.5 ≤ |d| < 0.8 | Efecto mediano |
| |d| ≥ 0.8 | Efecto grande |

**Importante**: El tamaño del efecto te dice QUÉ TAN grande es la diferencia, no solo si es estadísticamente significativa. Una diferencia puede ser significativa pero trivial, o no significativa pero relevante en la práctica.

---

## Matemáticas detrás de la prueba

### Estadístico de Tukey

#### Fórmula

$$q = \frac{\bar{x}_i - \bar{x}_j}{\sqrt{\frac{MS_{within}}{n}}}$$

Donde:
- $\bar{x}_i, \bar{x}_j$ = medias de los dos grupos comparados
- $MS_{within}$ = media de cuadrados dentro de los grupos (de la ANOVA)
- $n$ = tamaño de muestra por grupo

El estadístico $q$ se compara con la **distribución del rango estudentizado de Tukey**.

#### Cálculo del p-valor

El p-valor se obtiene de la distribución del rango estudentizado con:
- $k$ = número de grupos
- $df_{within}$ = grados de libertad dentro de los grupos

#### Proceso

1. La ANOVA ya se ejecutó y rechazó H₀
2. Para cada par de grupos (i, j):
   a. Calcular la diferencia de medias
   b. Calcular el error estándar usando MS_within
   c. Calcular el estadístico q
   d. Obtener el p-valor de la distribución de Tukey
3. Aplicar corrección por comparaciones múltiples automáticamente

#### Número de comparaciones

Para $k$ grupos, el número de comparaciones es:

$$C = \frac{k(k-1)}{2}$$

Ejemplos:
- 3 grupos → 3 comparaciones
- 4 grupos → 6 comparaciones
- 6 grupos → 15 comparaciones
- 10 grupos → 45 comparaciones

### Tamaño del efecto

Tukey HSD reporta **Cohen's d** para cada comparación:

$$d = \frac{\bar{x}_i - \bar{x}_j}{s_{pooled}}$$

Donde $s_{pooled}$ es la desviación estándar agrupada (pooled).

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Explorar medias antes del post-hoc**

> Un equipo de marketing quiere ver qué campañas de email (A, B, C) tienen diferentes tasas de conversión.

```python
import pandas as pd

df1 = pd.read_csv('campañas_tasas_conversion.csv')

# Medias por campaña
print("Medias por campaña:")
print(df1.groupby('campaña')['tasa_conversión'].mean())
# campaña
# A    3.11
# B    3.31
# C    2.96
```

**Ejemplo 2: Verificar si la ANOVA es significativa primero**

```python
from pingouin import anova

resultados_anova = anova(
    data=df1,
    dv='tasa_conversión',
    between='campaña',
    detailed=True,
    effsize='n2'
)
print(resultados_anova)
# Si p < 0.05 → proceder con Tukey HSD
```

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 3: Verificar que los grupos están balanceados**

```python
# Conteo por grupo
conteos = df1['campaña'].value_counts()
print(conteos)
# Todos los grupos deben tener n similar para Tukey óptimo

# Si hay desbalanceo severo, considerar Games-Howell
```

**Ejemplo 4: Visualizar las diferencias antes del post-hoc**

```python
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(6, 4))
sns.boxplot(data=df1, x='campaña', y='tasa_conversión')
plt.title("Distribución por campaña")
plt.ylabel("Tasa de conversión (%)")
plt.show()
```

---

### 3. En Feature Engineering

**Ejemplo 5: Calcular cuántas comparaciones se harán**

```python
k = 3  # Número de grupos (A, B, C)
comparaciones = k * (k - 1) / 2
print(f"Con {k} grupos, se harán {int(comparaciones)} comparaciones")
# Con 3 grupos, se harán 3 comparaciones
```

**Ejemplo 6: Calcular tamaño de efecto esperado para el post-hoc**

```python
# Si esperamos una diferencia de 0.2 entre medias
# y la desviación estándar agrupada es 0.5
d_esperado = 0.2 / 0.5
print(f"Cohen's d esperado: {d_esperado:.2f}")  # 0.4 → efecto mediano
```

---

### 4. En Selección de Modelos

**Ejemplo 7: Decidir entre Tukey y otras pruebas**

```python
# Si los grupos están balanceados y las varianzas son iguales → Tukey HSD
# Si hay desbalanceo o varianzas desiguales → Games-Howell
# Si querés comparar contra un control → Dunnett
# Si son pocas comparaciones planeadas → Bonferroni
```

**Ejemplo 8: Calcular poder para el post-hoc**

```python
from statsmodels.stats.power import TTestIndPower

analisis = TTestIndPower()

# Para detectar un Cohen's d de 0.5 con n=200 por grupo
potencia = analisis.power(
    effect_size=0.5,
    nobs1=200,
    ratio=1,  # Mismo tamaño en ambos grupos
    alpha=0.05
)
print(f"Potencia para detectar d=0.5: {potencia:.4f}")
```

---

### 5. En Evaluación post-deploy

**Ejemplo 9: Aplicar Tukey HSD para ANOVA de 1 factor**

```python
from pingouin import pairwise_tukey

resultados = pairwise_tukey(
    data=df1,
    dv='tasa_conversión',
    between='campaña',
    effsize='cohen'  # Cohen's d para tamaño del efecto
)
print(resultados)
```

**Ejemplo 10: Interpretar la tabla de resultados**

```python
# Columnas de la tabla:
# - A, B: los dos grupos comparados
# - mean(A), mean(B): medias de cada grupo
# - diff: diferencia de medias (mean(A) - mean(B))
# - se: error estándar
# - T: estadístico de Tukey
# - p-tukey: valor p corregido
# - cohen: tamaño del efecto (Cohen's d)

for idx, row in resultados.iterrows():
    significativo = "❌" if row['p-tukey'] < 0.05 else "✅"
    print(f"{row['A']} vs {row['B']}: "
          f"diff={row['diff']:.4f}, p={row['p-tukey']:.4f}, "
          f"d={row['cohen']:.4f} {significativo}")
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 11: Filtrar solo comparaciones significativas**

```python
# Filtrar p < 0.05
resultados_significativos = resultados[resultados['p-tukey'] < 0.05]
print(resultados_significativos[['A', 'B', 'diff', 'p-tukey', 'cohen']])
```

**Ejemplo 12: Ordenar por tamaño del efecto**

```python
# Ordenar por magnitud del efecto (valor absoluto)
resultados['cohen_abs'] = resultados['cohen'].abs()
resultados_ordenados = resultados.sort_values('cohen_abs', ascending=False)
print(resultados_ordenados[['A', 'B', 'cohen', 'p-tukey']])
# Las comparaciones con mayor efecto aparecen primero
```

---

### 7. En Validación de supuestos

**Ejemplo 13: Tukey HSD para ANOVA multifactorial — efecto principal**

```python
# Para el factor "franja" en un ANOVA 2×3
from pingouin import pairwise_tukey

resultados_franja = pairwise_tukey(
    data=df2,
    dv='tasa_conversion',
    between='franja',  # Solo un factor a la vez
    effsize='cohen'
)
print(resultados_franja)
# mañana vs noche: p = 0.00004, d = -0.36
# mañana vs tarde: p < 0.0001, d = -0.63
# noche vs tarde: p < 0.0001, d = -0.29
```

**Ejemplo 14: Tukey HSD para interacciones — crear subgrupos**

```python
# Para probar interacciones, crear una variable combinada
df2['grupo'] = df2['campaña'] + ' - ' + df2['franja']

# Verificar subgrupos
print(df2['grupo'].value_counts())
# A - mañana    200
# A - tarde     200
# A - noche     200
# B - mañana    200
# B - tarde     200
# B - noche     200

# Ahora sí aplicar Tukey sobre los subgrupos
resultados_interaccion = pairwise_tukey(
    data=df2,
    dv='tasa_conversion',
    between='grupo',  # La variable combinada
    effsize='cohen'
)
print(resultados_interaccion)
# 15 comparaciones (6 grupos × 5 / 2)
```

**Ejemplo 15: Depurar y filtrar resultados de interacción**

```python
# Filtrar solo significativos
resultados_sig = resultados_interaccion[resultados_interaccion['p-tukey'] < 0.05]

# Seleccionar columnas relevantes
resultados_f = resultados_sig[['A', 'B', 'cohen']]

# Ordenar por tamaño del efecto
resultados_f = resultados_f.sort_values('cohen', ascending=True)
print(resultados_f)
# Las combinaciones con mayor efecto aparecen primero
```

---

## Interpretación de resultados

### Tabla de Tukey HSD típica (1 factor)

```
       A    B   mean(A)   mean(B)      diff        se         T       p-tukey      cohen
0      A    B    3.110     3.312    -0.201    0.064    -3.127    5.26e-03      -0.312
1      A    C    3.110     2.964     0.146    0.065     2.258    6.26e-02       0.216
2      B    C    3.312     2.964     0.347    0.064     5.387    3.08e-07       0.552
```

**Cómo leerlo:**
- **Fila 0 (A vs B)**: p = 0.005 < 0.05 → diferencia significativa. B es mayor que A (diff negativo). Efecto pequeño (d = -0.31).
- **Fila 1 (A vs C)**: p = 0.063 > 0.05 → NO hay diferencia significativa.
- **Fila 2 (B vs C)**: p < 0.001 → diferencia muy significativa. B es mayor que C. Efecto mediano (d = 0.55).

### Tabla de Tukey HSD típica (interacción)

```
             A           B      cohen
4   A - mañana   B - tarde   -7.245
8    A - noche   B - tarde   -7.056
11   A - tarde   B - tarde   -6.905
3   A - mañana   B - noche   -5.270
...
```

**Cómo leerlo:**
- Solo aparecen comparaciones significativas (p < 0.05)
- El tamaño del efecto es negativo: B-something tiene media MAYOR que A-something
- Los efectos más grandes están entre A-mañana vs B-tarde (d = -7.24)

### Reglas de decisión

```
Para CADA comparación en la tabla:
p-tukey < 0.05  → ❌ Diferencia significativa entre este par
p-tukey ≥ 0.05  → ✅ No hay diferencia significativa

Interpretar el tamaño del efecto:
|d| < 0.2  → Diferencia trivial
0.2 ≤ |d| < 0.5 → Diferencia pequeña
0.5 ≤ |d| < 0.8 → Diferencia mediana
|d| ≥ 0.8  → Diferencia grande
```

### ¿Qué reportar?

```python
# Formato completo para ANOVA de 1 factor
print("Prueba post-hoc de Tukey HSD:")
print(f"  Factor: campaña (k = 3)")
print(f"  Comparaciones realizadas: {int(k*(k-1)/2)}")
print(f"\nResultados significativos (p < 0.05):")

for idx, row in resultados_significativos.iterrows():
    print(f"  {row['A']} vs {row['B']}: "
          f"diff = {row['diff']:.4f}, "
          f"p = {row['p-tukey']:.4f}, "
          f"d = {row['cohen']:.4f}")

print(f"\nConclusión:")
print(f"  La campaña B tiene tasas de conversión significativamente mayores "
      f"que A (d = -0.31) y C (d = 0.55)")
print(f"  No hay diferencia significativa entre A y C (p = 0.063)")
```

### ¿Qué hacer según el resultado?

1. **Solo hay diferencias entre algunos pares**: Reportar qué pares difieren y el tamaño del efecto
2. **Todos los pares son diferentes**: Reportar el orden de las medias (cuál es mayor/menor)
3. **Ningún par es diferente**: La ANOVA pudo haber sido significativa por combinaciones de diferencias pequeñas
4. **Los tamaños del efecto son pequeños**: Aunque sea significativo, la diferencia puede no ser prácticamente relevante

---

## Errores comunes

### 1. Aplicar Tukey sin que la ANOVA sea significativa

```python
# MAL: aplicar Tukey directamente sin verificar la ANOVA
resultados = pairwise_tukey(data=df, dv='valor', between='grupo')
# ❌ La ANOVA pudo no ser significativa

# BIEN: verificar ANOVA primero
resultados_anova = anova(data=df, dv='valor', between='grupo')
if resultados_anova.loc[0, 'p-unc'] < 0.05:
    # Ahora sí aplicar Tukey
    resultados = pairwise_tukey(data=df, dv='valor', between='grupo')
```

### 2. Usar Tukey para comparaciones planeadas (no post-hoc)

```python
# MAL: usar Tukey para comparaciones que planeaste ANTES de ver los datos
# Si planeaste comparar A vs B y A vs C, usá contrastes ortogonales o Bonferroni

# BIEN: usar Tukey solo cuando la ANOVA es significativa y no sabés qué pares difieren
```

### 3. No interpretar el tamaño del efecto

```python
# MAL: "p = 0.001, ¡la diferencia es enorme!"
# El p-valor NO dice qué tan GRANDE es la diferencia

# BIEN: reportar p Y tamaño del efecto
print(f"p = {p:.4f} → Significativo")
print(f"d = {d:.4f} → {'Grande' if abs(d) >= 0.8 else 'Mediano' if abs(d) >= 0.5 else 'Pequeño'}")
```

### 4. No filtrar ni ordenar los resultados

```python
# MAL: presentar las 15 comparaciones sin filtrar
print(resultados)  # ❌ Mucha información, difícil de interpretar

# BIEN: filtrar significativos y ordenar por efecto
resultados_f = resultados[resultados['p-tukey'] < 0.05]
resultados_f = resultados_f.sort_values('cohen', ascending=True)
print(resultados_f[['A', 'B', 'cohen']])
```

### 5. Olvidar que para interacciones hay que crear subgrupos

```python
# MAL: intentar usar pairwise_tukey con dos factores directamente
resultados = pairwise_tukey(data=df, dv='valor', between=['factor1', 'factor2'])
# ❌ pairwise_tukey solo acepta UN factor

# BIEN: crear variable combinada primero
df['grupo'] = df['factor1'] + '-' + df['factor2']
resultados = pairwise_tukey(data=df, dv='valor', between='grupo')
```

### 6. No reportar el número de comparaciones

```python
# MAL: "encontramos 3 diferencias significativas" sin contexto
# ¿De cuántas comparaciones totales? ¿3 de 3 o 3 de 45?

# BIEN: reportar el contexto completo
k = 3  # número de grupos
comparaciones = int(k * (k-1) / 2)
print(f"Se realizaron {comparaciones} comparaciones")
print(f"De estas, 2 fueron significativas")
```

### 7. Usar Tukey con diseños desbalanceados sin precaución

```python
# MAL: usar Tukey con grupos de tamaños muy diferentes
# Grupo A: n=50, Grupo B: n=200, Grupo C: n=30
# Tukey asume igualdad de varianzas y tamaños similares

# BIEN: verificar balance o usar Games-Howell
conteos = df['grupo'].value_counts()
if conteos.max() / conteos.min() > 1.5:
    print("⚠️ Diseño desbalanceado — considerar Games-Howell")
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from pingouin import anova, pairwise_tukey

# ============================================
# FLUJO COMPLETO: Tukey HSD para ANOVA de 1 factor
# ============================================

# ---- PASO 1: Cargar datos ----
df1 = pd.read_csv('campañas_tasas_conversion.csv')
print(f"Grupos: {df1['campaña'].unique()}")
print(f"Total de datos: {len(df1)}")

# ---- PASO 2: Explorar medias ----
print("\nMedias por campaña:")
print(df1.groupby('campaña')['tasa_conversión'].mean())

# ---- PASO 3: Visualizar ----
plt.figure(figsize=(6, 4))
sns.boxplot(data=df1, x='campaña', y='tasa_conversión')
plt.title("Distribución por campaña")
plt.show()

# ---- PASO 4: Aplicar ANOVA ----
resultados_anova = anova(
    data=df1,
    dv='tasa_conversión',
    between='campaña',
    detailed=True,
    effsize='n2'
)
print("\nResultados ANOVA:")
print(resultados_anova)

# ---- PASO 5: Verificar si ANOVA es significativa ----
p_anova = resultados_anova.loc[0, 'p-unc']
if p_anova < 0.05:
    print(f"\n✅ ANOVA significativa (p = {p_anova:.4f}) — proceder con Tukey HSD")
    
    # ---- PASO 6: Aplicar Tukey HSD ----
    resultados_tukey = pairwise_tukey(
        data=df1,
        dv='tasa_conversión',
        between='campaña',
        effsize='cohen'
    )
    
    # ---- PASO 7: Filtrar significativos ----
    resultados_sig = resultados_tukey[resultados_tukey['p-tukey'] < 0.05]
    
    # ---- PASO 8: Ordenar por efecto ----
    resultados_f = resultados_sig[['A', 'B', 'diff', 'p-tukey', 'cohen']]
    resultados_f = resultados_f.sort_values('cohen', ascending=True)
    
    print("\nComparaciones significativas (ordenadas por tamaño del efecto):")
    print(resultados_f)
    
    # ---- PASO 9: Interpretar ----
    print("\n=== Interpretación ===")
    for idx, row in resultados_f.iterrows():
        direccion = "mayor" if row['diff'] < 0 else "menor"
        print(f"  {row['B']} es {direccion} que {row['A']} "
              f"(d = {row['cohen']:.4f})")
else:
    print(f"\n✅ ANOVA no significativa (p = {p_anova:.4f}) — no aplicar Tukey")

# ============================================
# FLUJO COMPLETO: Tukey HSD para ANOVA multifactorial
# ============================================

# ---- PASO 1: Cargar datos ----
df2 = pd.read_csv('anova_multifactorial_2x3.csv')

# ---- PASO 2: Aplicar ANOVA multifactorial ----
resultados_anova = anova(
    data=df2,
    dv='tasa_conversion',
    between=['campaña', 'franja'],
    effsize='n2',
    detailed=True
)
print("\nResultados ANOVA multifactorial:")
print(resultados_anova)

# ---- PASO 3: Para cada efecto significativo, aplicar Tukey ----
for idx in range(3):
    fuente = resultados_anova.loc[idx, 'Source']
    p = resultados_anova.loc[idx, 'p-unc']
    
    if p < 0.05:
        print(f"\n--- Post-hoc para {fuente} (p = {p:.2e}) ---")
        
        if fuente == 'campaña * franja':
            # Para interacciones: crear variable combinada
            df2['grupo'] = df2['campaña'] + ' - ' + df2['franja']
            between_col = 'grupo'
        else:
            between_col = fuente
        
        resultados_tukey = pairwise_tukey(
            data=df2,
            dv='tasa_conversion',
            between=between_col,
            effsize='cohen'
        )
        
        # Filtrar y ordenar
        resultados_sig = resultados_tukey[resultados_tukey['p-tukey'] < 0.05]
        resultados_f = resultados_sig[['A', 'B', 'cohen']]
        resultados_f = resultados_f.sort_values('cohen', ascending=True)
        
        print(f"Comparaciones significativas:")
        print(resultados_f)

# ---- PASO 4: Interpretar interacción ----
print("\n=== Interpretación de interacción ===")
print("La combinación B-tarde genera las mayores tasas de conversión")
print("La campaña A tiene tasas bajas independientemente de la franja")
print("La campaña B tiene tasas que aumentan con la franja (tarde > noche > mañana)")
```

---

## Resumen: cuándo usar cada prueba post-hoc

```
¿Qué querés hacer?
│
├── Identificar qué pares de grupos difieren después de ANOVA
│   │
│   ├── ¿Tenés un diseño balanceado?
│   │   ├── SÍ → Tukey HSD
│   │   └── NO → Games-Howell
│   │
│   ├── ¿Querés comparar todos contra todos?
│   │   └── Tukey HSD
│   │
│   └── ¿Querés comparar todos contra un control?
│       └── Dunnett
│
├── Comparaciones planeadas (no post-hoc)
│   │
│   ├── ¿Son pocas comparaciones?
│   │   └── Bonferroni
│   │
│   └── ¿Son comparaciones ortogonales?
│       └── Contrastes ortogonales
│
├── ANOVA multifactorial
│   │
│   ├── Efecto principal significativo
│   │   └── Tukey por ese factor
│   │
│   └── Interacción significativa
│       └── Tukey por subgrupos (crear variable combinada)
│
└── ¿No sabés cuál usar?
    └── Tukey HSD (el más común y versátil)
```

---

## Código rápido de referencia

```python
import pandas as pd
from pingouin import anova, pairwise_tukey

# ============================================
# TUKEY HSD PARA ANOVA DE 1 FACTOR
# ============================================

# Cargar datos
df = pd.read_csv('datos.csv')

# ANOVA primero
resultados_anova = anova(data=df, dv='valor', between='factor')

# Solo si ANOVA es significativa
if resultados_anova.loc[0, 'p-unc'] < 0.05:
    # Tukey HSD
    resultados = pairwise_tukey(
        data=df,
        dv='valor',
        between='factor',
        effsize='cohen'
    )
    
    # Filtrar significativos
    resultados_sig = resultados[resultados['p-tukey'] < 0.05]
    
    # Ordenar por efecto
    resultados_f = resultados_sig[['A', 'B', 'cohen']]
    resultados_f = resultados_f.sort_values('cohen', ascending=True)
    print(resultados_f)

# ============================================
# TUKEY HSD PARA ANOVA MULTIFACTORIAL
# ============================================

# ANOVA multifactorial
resultados_anova = anova(
    data=df,
    dv='valor',
    between=['factor1', 'factor2'],
    effsize='n2',
    detailed=True
)

# Para cada efecto significativo
for idx in range(3):
    fuente = resultados_anova.loc[idx, 'Source']
    p = resultados_anova.loc[idx, 'p-unc']
    
    if p < 0.05:
        if fuente == 'factor1 * factor2':
            # Interacción: crear variable combinada
            df['grupo'] = df['factor1'] + '-' + df['factor2']
            between = 'grupo'
        else:
            between = fuente
        
        resultados = pairwise_tukey(data=df, dv='valor', between=between, effsize='cohen')
        print(f"\n{fuente}:")
        print(resultados[resultados['p-tukey'] < 0.05][['A', 'B', 'cohen']])

# ============================================
# ALTERNATIVAS A TUKEY
# ============================================

# Games-Howell (para diseños desbalanceados)
# from pingouin import pairwise_gameshowell
# resultados = pairwise_gameshowell(data=df, dv='valor', between='factor')

# Dunnett (comparar contra un control)
# from pingouin import pairwise_dunnett
# resultados = pairwise_dunnett(data=df, dv='valor', between='factor', control='control')

# Bonferroni (comparaciones planeadas)
# from pingouin import pairwise_tests
# resultados = pairwise_tests(data=df, dv='valor', between='factor', padjust='bonferroni')
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Aplicar ANOVA y verificar que es significativa | `pingouin.anova` |
| 2 | Verificar número de grupos (3+ para post-hoc) | Conteo |
| 3 | Verificar balance de diseño | `value_counts()` |
| 4 | Visualizar diferencias | `seaborn.boxplot` |
| 5 | Seleccionar método post-hoc | Tukey (balanceado), Games-Howell (desbalanceado) |
| 6 | Aplicar Tukey HSD | `pingouin.pairwise_tukey` |
| 7 | Filtrar comparaciones significativas (p < 0.05) | Filtrado booleano |
| 8 | Ordenar por tamaño del efecto | `sort_values()` |
| 9 | Interpretar Cohen's d | Tabla de interpretación |
| 10 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lección 11: Pruebas Post-hoc.
- Tukey, J. W. (1949). Comparing individual means in the analysis of variance. Biometrics, 5(2), 99-114.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- Pingouin Documentation. `pingouin.pairwise_tukey` — Tukey's Honestly Significant Difference test.
- SciPy Documentation. `scipy.stats.tukey_hsd` — Tukey's honestly significant difference test.
