# Guía de Referencia: ANOVA Multifactorial (Two-Way ANOVA)

## ¿Qué es?

La ANOVA multifactorial es una herramienta **estadística paramétrica** que determina si existen diferencias estadísticamente significativas entre las medias de **tres o más grupos** considerando **dos o más factores simultáneamente**. A diferencia de la ANOVA de un factor, permite evaluar no solo los efectos principales de cada factor, sino también si **existe interacción** entre ellos.

**Pregunta central:** ¿Las medias de los grupos dependen del factor A, del factor B, o de la combinación de ambos?

**Qué NO hace:**
- No evalúa más de dos factores de forma estándar (para 3+ factores se usa ANOVA factorial de更高 orden)
- No asume que los datos son normales (pero SÍ requiere normalidad y homocedasticidad)
- No identifica QUÉ grupos son diferentes (para eso se usan pruebas post-hoc)
- No reemplaza el juicio experto sobre si el tamaño del efecto es práctico
- **No prueban causalidad** — solo describen si hay diferencia estadísticamente significativa

### Por qué ANOVA multifactorial y no múltiples ANOVA de un factor

Si evaluáramos cada factor por separado con ANOVA de un factor, perderíamos información sobre las interacciones y acumularíamos errores tipo I. La ANOVA multifactorial evalúa todo simultáneamente y controla el error global.

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás comparar medias considerando 2 o más factores?
│
│   ├── ¿Los factores son independientes?
│   │   ├── SÍ → ANOVA factorial (dos vías o más)
│   │   └── NO (mediciones repetidas) → ANOVA de medidas repetidas / mixed ANOVA
│   │
│   ├── ¿Los datos son normales en cada subgrupo?
│   │   ├── SÍ → Podés usar ANOVA multifactorial
│   │   └── NO → Considerá pruebas no paramétricas
│   │
│   ├── ¿Las varianzas son iguales entre subgrupos?
│   │   ├── SÍ (homocedasticidad) → ANOVA estándar
│   │   └── NO → Welch's ANOVA o transformaciones
│   │
│   └── ¿Querés saber si hay INTERACCIÓN entre factores?
│       └── SÍ → ANOVA multifactorial (la interacción es el punto clave)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| 2 factores independientes, quiero ver interacción | ANOVA multifactorial (dos vías) |
| 1 factor, 3+ grupos | ANOVA de 1 factor |
| Factores con mediciones repetidas | ANOVA de medidas repetidas |
| Datos no normales, 2 factores | Friedman (no paramétrica) |
| ANOVA significativo, querés saber qué grupo difiere | Pruebas post-hoc (Tukey HSD) |

### ¿Por qué importa la interacción?

La interacción entre factores es el concepto MÁS importante en ANOVA multifactorial:

- **Sin interacción**: El efecto de un factor es el mismo en todos los niveles del otro factor. Los factores actúan independientemente.
- **Con interacción**: El efecto de un factor CAMBIA dependiendo del nivel del otro factor. Los factores NO son independientes.

**Ejemplo del notebook**: La campaña B tiene tasas de conversión más altas que A, PERO esta diferencia es MUCHO mayor en la franja de la tarde. Eso es una interacción campaña × franja horaria.

---

## Comparación entre pruebas

### Comparación directa

| Aspecto | ANOVA de 1 factor | ANOVA multifactorial (2 factores) |
|---|---|---|
| **Factores evaluados** | 1 | 2 (o más) |
| **Hipótesis a probar** | 1 par H₀/H₁ | 3+ pares H₀/H₁ |
| **Efectos evaluados** | Solo efecto principal | Efecto principal 1 + Efecto principal 2 + Interacción |
| **Rechaza H₀ cuando** | Al menos un grupo difiere | Al menos un efecto es significativo |
| **Identifica qué grupo** | No (necesitás post-hoc) | No (necesitás post-hoc) |
| **Tamaño del efecto** | η² / f de Cohen | η² parcial / f de Cohen por efecto |
| **Complejidad** | Simple | Mayor (más hipótesis, más supuestos) |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → no hay evidencia de efecto | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar el contexto y el tamaño del efecto | ⚠️ |
| p < 0.05 | Se rechaza H₀ → hay efecto significativo | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de efecto | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa cada tipo de efecto?

- **Efecto principal del factor A**: Las medias difieren entre los niveles del factor A, PROMEDIANDO sobre todos los niveles del factor B.
- **Efecto principal del factor B**: Las medias difieren entre los niveles del factor B, PROMEDIANDO sobre todos los niveles del factor A.
- **Interacción A × B**: El efecto del factor A CAMBIA dependiendo del nivel del factor B (o viceversa). Si hay interacción significativa, los efectos principales deben interpretarse con cuidado.

---

## Matemáticas detrás de la prueba

### La lógica detrás de ANOVA multifactorial

ANOVA multifactorial particiona la variación total en múltiples fuentes:

1. **Variación por factor A (SS_A)**: Diferencias entre niveles del factor A
2. **Variación por factor B (SS_B)**: Diferencias entre niveles del factor B
3. **Variación por interacción A×B (SS_A×B)**: Efecto combinado de A y B
4. **Variación residual (SS_error)**: Variación no explicada por los factores

La idea clave: **si los factores y su interacción no tuvieran efecto**, toda la variación sería residual.

### Estadísticos F (uno por cada efecto)

#### Fórmula para cada efecto

$$F_A = \frac{MS_A}{MS_{error}} = \frac{SS_A / (a-1)}{SS_{error} / (N - a \cdot b)}$$

$$F_B = \frac{MS_B}{MS_{error}} = \frac{SS_B / (b-1)}{SS_{error} / (N - a \cdot b)}$$

$$F_{A \times B} = \frac{MS_{A \times B}}{MS_{error}} = \frac{SS_{A \times B} / ((a-1)(b-1))}{SS_{error} / (N - a \cdot b)}$$

Donde:
- $SS_A, SS_B, SS_{A \times B}$ = sumas de cuadrados para cada efecto
- $a$ = número de niveles del factor A
- $b$ = número de niveles del factor B
- $N$ = número total de observaciones
- $MS$ = media de cuadrados ($SS / gl$)

#### Grados de libertad

- **Factor A**: $df_A = a - 1$
- **Factor B**: $df_B = b - 1$
- **Interacción A×B**: $df_{A \times B} = (a-1)(b-1)$
- **Residual**: $df_{error} = N - a \cdot b$
- **Total**: $df_{total} = N - 1$

#### Proceso

1. Definir los factores y sus niveles
2. Definir las hipótesis para cada efecto (factor A, factor B, interacción)
3. Calcular las medias de cada subgrupo
4. Particionar la variación en sus fuentes
5. Calcular F para cada efecto
6. Comparar cada F con su valor crítico o calcular p-valores
7. Si hay interacción significativa, interpretar con cuidado

#### Ejemplo manual simplificado

```python
# ANOVA 2×3: 2 campañas (A, B) × 3 franjas (mañana, tarde, noche)
# Factor A (campaña): a = 2 niveles
# Factor B (franja): b = 3 niveles
# Total de datos: N = 1200

# Grados de libertad:
# df_campaña = 2 - 1 = 1
# df_franja = 3 - 1 = 2
# df_interacción = (2-1)(3-1) = 2
# df_residual = 1200 - 2*3 = 1194

# SS (del notebook):
# SS_campaña = 703.32
# SS_franja = 57.66
# SS_interacción = 49.96
# SS_residual = 104.67

# MS (SS / df):
# MS_campaña = 703.32 / 1 = 703.32
# MS_franja = 57.66 / 2 = 28.83
# MS_interacción = 49.96 / 2 = 24.98
# MS_residual = 104.67 / 1194 = 0.0877

# F:
# F_campaña = 703.32 / 0.0877 = 8023.29
# F_franja = 28.83 / 0.0877 = 328.89
# F_interacción = 24.98 / 0.0877 = 284.96
```

### Tamaño del efecto

#### Eta cuadrado parcial (η² parcial)

$$\eta^2_{parcial} = \frac{SS_{efecto}}{SS_{efecto} + SS_{error}}$$

**Nota**: En ANOVA multifactorial se usa η² **parcial** porque el η² total no es aditivo (la suma de η² de cada efecto no necesariamente suma 1).

#### Interpretación de η² parcial

| Valor de η² parcial | Interpretación |
|---|---|
| η² < 0.01 | Efecto pequeño / despreciable |
| 0.01 ≤ η² < 0.06 | Efecto pequeño |
| 0.06 ≤ η² < 0.14 | Efecto mediano |
| η² ≥ 0.14 | Efecto grande |

#### F de Cohen (por cada efecto)

$$f = \sqrt{\frac{\eta^2}{1 - \eta^2}}$$

#### Interpretación de f (Cohen)

| Valor de f | Interpretación |
|---|---|
| f ≈ 0.10 | Efecto pequeño |
| f ≈ 0.25 | Efecto mediano |
| f ≈ 0.40 | Efecto grande |

### Potencia de la prueba

La potencia se calcula por separado para cada efecto.

#### Cálculo de tamaño de muestra

```python
from statsmodels.stats.power import FTestAnovaPower

analisis = FTestAnovaPower()

# Para efecto principal del factor A
n_A = analisis.solve_power(
    effect_size=f_A,
    alpha=0.05,
    power=0.8,
    k_groups=a    # Niveles del factor A
)

# Para efecto principal del factor B
n_B = analisis.solve_power(
    effect_size=f_B,
    alpha=0.05,
    power=0.8,
    k_groups=b    # Niveles del factor B
)

# Para interacción A×B
n_AB = analisis.solve_power(
    effect_size=f_AB,
    alpha=0.05,
    power=0.8,
    k_groups=a*b  # Combinación de niveles
)

# Tamaño total necesario = el máximo de los tres
n_total = max(n_A, n_B, n_AB)
```

#### Cálculo de potencia (por cada efecto)

```python
analisis = FTestAnovaPower()

# Potencia para cada efecto
potencia_A = analisis.power(
    effect_size=f_A,
    nobs=N,
    alpha=0.05,
    k_groups=a
)

potencia_B = analisis.power(
    effect_size=f_B,
    nobs=N,
    alpha=0.05,
    k_groups=b
)

potencia_AB = analisis.power(
    effect_size=f_AB,
    nobs=N,
    alpha=0.05,
    k_groups=a*b
)
```

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Explorar conteos por subgrupo**

> Un equipo de marketing evalúa si campañas de email (A, B) y franjas horarias (mañana, tarde, noche) afectan la tasa de conversión.

```python
import pandas as pd

datos = pd.read_csv('anova_multifactorial_2x3.csv')

# Conteo por subgrupo
conteos = datos.groupby(['campaña', 'franja']).count()
print(conteos)
# A  mañana    200
#    noche     200
#    tarde     200
# B  mañana    200
#    noche     200
#    tarde     200
```

**Ejemplo 2: Explorar medias por factor**

```python
# Media por campaña (efecto principal 1)
print("Media por campaña:")
print(datos.groupby('campaña')['tasa_conversion'].mean())

# Media por franja (efecto principal 2)
print("\nMedia por franja:")
print(datos.groupby('franja')['tasa_conversion'].mean())

# Media por subgrupo (para ver interacciones)
print("\nMedia por subgrupo:")
print(datos.groupby(['campaña', 'franja'])['tasa_conversion'].mean())
```

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 3: Verificar normalidad en cada subgrupo**

```python
from scipy.stats import shapiro

campanas = ['A', 'B']
franjas = ['mañana', 'tarde', 'noche']

for campana in campanas:
    for franja in franjas:
        subgrupo = datos.loc[
            (datos['campaña']==campana) & (datos['franja']==franja),
            'tasa_conversion'
        ]
        W, p = shapiro(subgrupo)
        print(f"Grupo {campana}-{franja}: p = {p:.4f} {'✅' if p >= 0.05 else '❌'}")
```

**Ejemplo 4: Verificar homocedasticidad**

```python
from scipy.stats import bartlett

# Crear lista de todos los subgrupos
grupos = []
for campana in campanas:
    for franja in franjas:
        subgrupo = datos.loc[
            (datos['campaña']==campana) & (datos['franja']==franja),
            'tasa_conversion'
        ].to_numpy()
        grupos.append(subgrupo)

# Prueba de Bartlett
B, p_bartlett = bartlett(*grupos)
print(f"Bartlett: p = {p_bartlett:.4f} {'✅' if p_bartlett >= 0.05 else '❌'}")
```

---

### 3. En Feature Engineering

**Ejemplo 5: Calcular tamaño de muestra para cada efecto**

> Diseñamos un ANOVA 2×3 con efecto esperado grande (f = 0.4), α = 0.05, potencia = 0.9.

```python
from statsmodels.stats.power import FTestAnovaPower
import math

analisis = FTestAnovaPower()
f_esperado = 0.4
alpha = 0.05
power = 0.9

a = 2  # Niveles de campaña (A, B)
b = 3  # Niveles de franja (mañana, tarde, noche)

# Tamaño por cada efecto
n_A = analisis.solve_power(effect_size=f_esperado, alpha=alpha, power=power, k_groups=a)
n_B = analisis.solve_power(effect_size=f_esperado, alpha=alpha, power=power, k_groups=b)
n_AB = analisis.solve_power(effect_size=f_esperado, alpha=alpha, power=power, k_groups=a*b)

print(f"Factor A (campaña, {a} niveles): n = {math.ceil(n_A)}")
print(f"Factor B (franja, {b} niveles): n = {math.ceil(n_B)}")
print(f"Interacción A×B ({a*b} subgrupos): n = {math.ceil(n_AB)}")

# El total necesario es el máximo
n_total = max(n_A, n_B, n_AB)
n_por_subgrupo = math.ceil(n_total / (a * b))
print(f"\nTotal necesario: {math.ceil(n_total)} observaciones")
print(f"Por subgrupo: {n_por_subgrupo} observaciones")
```

**Ejemplo 6: Calcular η² esperado a partir de f de Cohen**

```python
import numpy as np

f_esperado = 0.4
eta_cuadrado = (f_esperado**2) / (1 + f_esperado**2)
print(f"η² esperado: {eta_cuadrado:.4f}")  # ~0.138 → efecto mediano-grande
```

---

### 4. En Selección de Modelos

**Ejemplo 7: Comparar poder entre diferentes diseños**

```python
analisis = FTestAnovaPower()

# Comparar 2×2 vs 2×3 vs 3×3
for a, b in [(2, 2), (2, 3), (3, 3)]:
    n = analisis.solve_power(effect_size=0.25, alpha=0.05, power=0.8, k_groups=a*b)
    print(f"Diseño {a}×{b} ({a*b} subgrupos): n = {math.ceil(n)} total, {math.ceil(n/(a*b))} por subgrupo")
# Diseño 2×2 (4 subgrupos): n = 128 total, 32 por subgrupo
# Diseño 2×3 (6 subgrupos): n = 109 total, 19 por subgrupo
# Diseño 3×3 (9 subgrupos): n = 97 total, 11 por subgrupo
```

**Ejemplo 8: Calcular tamaño de muestra para un diseño específico**

```python
# Diseño 2×3 con diferentes tamaños del efecto
for f in [0.10, 0.25, 0.40]:
    n = analisis.solve_power(effect_size=f, alpha=0.05, power=0.8, k_groups=6)
    print(f"f = {f:.2f} ({'pequeño' if f < 0.15 else 'mediano' if f < 0.35 else 'grande'}): "
          f"n = {math.ceil(n)} total, {math.ceil(n/6)} por subgrupo")
# f = 0.10 (pequeño): n = 1284 total, 214 por subgrupo
# f = 0.25 (mediano): n = 109 total, 19 por subgrupo
# f = 0.40 (grande): n = 46 total, 8 por subgrupo
```

---

### 5. En Evaluación post-deploy

**Ejemplo 9: Aplicar la ANOVA multifactorial**

```python
from pingouin import anova

resultados = anova(
    data=datos,
    dv='tasa_conversion',
    between=['campaña', 'franja'],  # Lista de factores
    effsize='n2',
    detailed=True
)
print(resultados)
```

**Ejemplo 10: Interpretar la tabla de resultados**

```python
# La tabla de pingouin muestra:
# - Source: campaña, franja, campaña*franja, Residual
# - SS: sumas de cuadrados para cada fuente
# - DF: grados de libertad
# - MS: medias de cuadrados
# - F: estadística de prueba para cada efecto
# - p-unc: valor p para cada efecto
# - n2: eta cuadrado (tamaño del efecto)

for idx in range(3):  # Excluir Residual
    fuente = resultados.loc[idx, 'Source']
    f_stat = resultados.loc[idx, 'F']
    p_valor = resultados.loc[idx, 'p-unc']
    n2 = resultados.loc[idx, 'n2']
    print(f"{fuente}: F = {f_stat:.4f}, p = {p_valor:.2e}, η² = {n2:.4f}")
```

**Ejemplo 11: Visualizar efectos principales e interacciones**

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Efecto principal de campaña
plt.figure(figsize=(6, 4))
sns.boxplot(data=datos, x='campaña', y='tasa_conversion')
plt.title("Efecto principal: Campaña")
plt.show()

# Efecto principal de franja
plt.figure(figsize=(6, 4))
sns.boxplot(data=datos, x='franja', y='tasa_conversion')
plt.title("Efecto principal: Franja horaria")
plt.show()

# Gráfico de interacción
medias = datos.groupby(['campaña', 'franja'])['tasa_conversion'].mean().reset_index()
plt.figure(figsize=(8, 5))
sns.pointplot(
    data=medias,
    x='franja',
    y='tasa_conversion',
    hue='campaña',
    markers=['o', 's'],
    linestyles=['-', '--']
)
plt.title("Interacción: Campaña × Franja horaria")
plt.ylabel("Tasa de conversión (%)")
plt.show()
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 12: Calcular η² parcial y f de Cohen por efecto**

```python
import numpy as np

resultados['f-cohen'] = np.sqrt(resultados['n2'] / (1 - resultados['n2']))
print(resultados[['Source', 'n2', 'f-cohen']])
```

**Ejemplo 13: Calcular potencia por cada efecto**

```python
analisis = FTestAnovaPower()
N = len(datos)

potencias = []
for idx in range(3):
    f_cohen = resultados.loc[idx, 'f-cohen']
    k = resultados.loc[idx, 'DF'] + 1  # k = df + 1
    
    potencia = analisis.power(
        effect_size=f_cohen,
        nobs=N,
        alpha=0.05,
        k_groups=k
    )
    potencias.append(potencia)

resultados['potencia'] = potencias + [None]
print(resultados[['Source', 'f-cohen', 'potencia']])
```

---

### 7. En Validación de supuestos

**Ejemplo 14: Prueba post-hoc de Tukey (si hay efectos significativos)**

```python
from pingouin import pairwise_tukey

# Solo si se rechazó H₀ para algún efecto
for idx in range(3):
    fuente = resultados.loc[idx, 'Source']
    p = resultados.loc[idx, 'p-unc']
    
    if p < 0.05:
        print(f"\nPost-hoc para {fuente}:")
        if fuente == 'campaña':
            tukey = pairwise_tukey(dv='tasa_conversion', between='campaña', data=datos)
        elif fuente == 'franja':
            tukey = pairwise_tukey(dv='tasa_conversion', between='franja', data=datos)
        else:
            # Para interacciones, crear variable combinada
            datos['subgrupo'] = datos['campaña'] + '-' + datos['franja']
            tukey = pairwise_tukey(dv='tasa_conversion', between='subgrupo', data=datos)
        print(tukey)
```

**Ejemplo 15: Verificar supuestos completos**

```python
def verificar_supuestos multifactorial(datos, factor_cols, valor_col):
    """Verifica normalidad y homocedasticidad para ANOVA multifactorial"""
    
    # Crear subgrupos
    niveles = datos[factor_cols[0]].unique()
    for col in factor_cols[1:]:
        niveles = [(n, nivel) for n in niveles for nivel in datos[col].unique()]
    
    grupos = []
    for combo in niveles:
        if isinstance(combo, tuple):
            mask = True
            for col, nivel in zip(factor_cols, combo):
                mask = mask & (datos[col] == nivel)
        else:
            mask = datos[factor_cols[0]] == combo
        grupos.append(datos.loc[mask, valor_col].to_numpy())
    
    # 1. Normalidad por subgrupo
    print("=== Normalidad (Shapiro-Wilk) ===")
    todos_normales = True
    for i, grupo in enumerate(grupos):
        W, p = shapiro(grupo)
        estado = "✅" if p >= 0.05 else "❌"
        print(f"  Subgrupo {i}: p = {p:.4f} {estado}")
        if p < 0.05:
            todos_normales = False
    
    # 2. Homocedasticidad
    print("\n=== Homocedasticidad (Bartlett) ===")
    B, p_bartlett = bartlett(*grupos)
    estado = "✅" if p_bartlett >= 0.05 else "❌"
    print(f"  Bartlett: p = {p_bartlett:.4f} {estado}")
    
    return todos_normales, p_bartlett >= 0.05

# Uso
normal, homo = verificar_supuestos multifactorial(
    datos, ['campaña', 'franja'], 'tasa_conversion'
)
```

---

## Interpretación de resultados

### Tabla de resultados típica

```
             Source          SS    DF          MS            F          p-unc        n2
0           campaña     703.32     1      703.32      8023.29     0.00e+00      0.768
1            franja      57.66     2       28.83       328.89     1.66e-114    0.063
2  campaña * franja      49.96     2       24.98       284.96     6.64e-102    0.055
3          Residual     104.67    1194        0.09         NaN          NaN       NaN
```

**Cómo leerlo:**
- **Cada fila** es un efecto (factor o interacción)
- **F grande** (mucho mayor que 1) → evidencia de que ese efecto es real
- **p-unc < 0.05** → se rechaza H₀ para ese efecto
- **n2** → proporción de varianza explicada por ese efecto

### Reglas de decisión

```
Para CADA efecto (factor A, factor B, interacción A×B):
F > F_crítico  → ❌ Se rechaza H₀ (ese efecto es significativo)
F ≤ F_crítico  → ✅ No se rechaza H₀

O equivalentemente:
p < 0.05  → ❌ Se rechaza H₀
p ≥ 0.05  → ✅ No se rechaza H₀
```

### ¿Qué reportar?

```python
print("ANOVA Multifactorial (2×3):")
print(f"  Factores: campaña (2 niveles), franja (3 niveles)")
print(f"  N total = {len(datos)}\n")

for idx in range(3):
    fuente = resultados.loc[idx, 'Source']
    f_stat = resultados.loc[idx, 'F']
    p_valor = resultados.loc[idx, 'p-unc']
    n2 = resultados.loc[idx, 'n2']
    f_cohen = resultados.loc[idx, 'f-cohen']
    potencia = resultados.loc[idx, 'potencia']
    
    print(f"  {fuente}:")
    print(f"    F = {f_stat:.4f}, p = {p_valor:.2e}")
    print(f"    η² = {n2:.4f}, f = {f_cohen:.4f}")
    print(f"    Potencia = {potencia:.4f}")
    print(f"    {'❌ Significativo' if p_valor < 0.05 else '✅ No significativo'}")
```

### ¿Qué hacer según el resultado?

1. **Interacción significativa**: PRIORIZAR la interpretación de la interacción sobre los efectos principales
   - Graficar la interacción (pointplot o lineplot)
   - Analizar qué combinaciones de niveles son diferentes
   - Usar post-hoc por subgrupo

2. **Solo efectos principales significativos** (sin interacción): Interpretar cada factor por separado
   - Reportar cuáles niveles difieren
   - Usar post-hoc por factor

3. **Ningún efecto significativo**: No hay evidencia de diferencias
   - Verificar potencia y tamaño de muestra
   - Verificar supuestos

---

## Errores comunes

### 1. No graficar la interacción antes de interpretar

```python
# MAL: interpretar solo los efectos principales sin ver la interacción
# Si hay interacción significativa, los efectos principales pueden ser engañosos

# BIEN: graficar la interacción primero
medias = datos.groupby(['campaña', 'franja'])['tasa_conversion'].mean().reset_index()
sns.pointplot(data=medias, x='franja', y='tasa_conversion', hue='campaña')
plt.title("Interacción: Campaña × Franja")
plt.show()
# Si las líneas NO son paralelas → hay interacción
```

### 2. No verificar normalidad en CADA subgrupo

```python
# MAL: verificar normalidad solo por factor
shapiro(datos.loc[datos['campaña']=='A', 'tasa_conversion'])  # ❌ No es suficiente

# BIEN: verificar en cada subgrupo (combinación de factores)
for campana in ['A', 'B']:
    for franja in ['mañana', 'tarde', 'noche']:
        subgrupo = datos.loc[(datos['campaña']==campana) & (datos['franja']==franja), 'tasa_conversion']
        W, p = shapiro(subgrupo)
        print(f"{campana}-{franja}: p = {p:.4f}")
```

### 3. No usar η² parcial

```python
# MAL: usar η² total en ANOVA multifactorial
# El η² total no es aditivo y puede ser confuso

# BIEN: usar η² parcial (que es lo que pingouin calcula por defecto)
resultados = anova(data=datos, dv='tasa', between=['factor1', 'factor2'], effsize='n2')
# n2 en pingouin es η² parcial
```

### 4. No calcular potencia por cada efecto

```python
# MAL: asumir que la potencia es la misma para todos los efectos
# La potencia depende del tamaño del efecto y los grados de libertad de CADA efecto

# BIEN: calcular potencia por cada efecto
for idx in range(3):
    f_cohen = resultados.loc[idx, 'f-cohen']
    k = resultados.loc[idx, 'DF'] + 1
    potencia = analisis.power(effect_size=f_cohen, nobs=N, alpha=0.05, k_groups=k)
    print(f"Potencia para {resultados.loc[idx, 'Source']}: {potencia:.4f}")
```

### 5. No hacer post-hoc después de rechazar H₀

```python
# MAL: rechazar H₀ y concluir "hay diferencias"
# Necesitás saber QUÉ niveles o QUÉ subgrupos difieren

# BIEN: hacer post-hoc
from pingouin import pairwise_tukey
tukey = pairwise_tukey(dv='tasa_conversion', between='campaña', data=datos)
print(tukey)
```

### 6. Ignorar el tamaño del efecto cuando p es significativo

```python
# MAL: "p = 0.001, ¡el efecto es enorme!"
# Un p-valor pequeño con una muestra grande puede tener un efecto pequeño

# BIEN: reportar p Y η² Y f de Cohen
print(f"p = {p:.2e} → Significativo")
print(f"η² = {n2:.4f} → {'Grande' if n2 >= 0.14 else 'Mediano' if n2 >= 0.06 else 'Pequeño'}")
```

### 7. No平衡ar el diseño (mismo número por subgrupo)

```python
# MAL: tener 200 observaciones en un subgrupo y 50 en otro
# Diseños desbalanceados requieren Type III SS y son más complejos

# BIEN: tener el mismo número de observaciones por subgrupo
conteos = datos.groupby(['campaña', 'franja']).count()
print(conteos)  # Todos deben tener el mismo conteo
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, bartlett
from pingouin import anova, pairwise_tukey
from statsmodels.stats.power import FTestAnovaPower
import seaborn as sns
import matplotlib.pyplot as plt
import math

# ============================================
# FLUJO COMPLETO: ANOVA Multifactorial (2×3)
# ============================================

# ---- PASO 1: Definir el problema del negocio ----
# ¿La tasa de conversión depende del tipo de campaña, de la franja horaria,
# o de una combinación de ambos?

# ---- PASO 2: Redactar como problema de Ciencia de Datos ----
# 1. ¿Hay diferencias significativas entre tipos de campaña?
# 2. ¿Hay diferencias significativas entre franjas horarias?
# 3. ¿La campaña y la franja horaria tienen efecto combinado?

# ---- PASO 3: Definir H₀ y H₁ ----
# Factor A (campaña): H₀: μ_A1 = μ_A2, H₁: μ_A1 ≠ μ_A2
# Factor B (franja): H₀: μ_B1 = μ_B2 = μ_B3, H₁: al menos una difiere
# Interacción: H₀: no hay interacción, H₁: hay interacción

# ---- PASO 4: Definir α ----
alpha = 0.05

# ---- PASO 5: Calcular potencia y tamaño de muestra ----
a = 2  # Niveles de campaña
b = 3  # Niveles de franja
f_esperado = 0.4
power = 0.9

analisis = FTestAnovaPower()

n_A = analisis.solve_power(effect_size=f_esperado, alpha=alpha, power=power, k_groups=a)
n_B = analisis.solve_power(effect_size=f_esperado, alpha=alpha, power=power, k_groups=b)
n_AB = analisis.solve_power(effect_size=f_esperado, alpha=alpha, power=power, k_groups=a*b)

n_total = max(n_A, n_B, n_AB)
n_por_subgrupo = math.ceil(n_total / (a * b))

print(f"Tamaño necesario por subgrupo: {n_por_subgrupo}")
print(f"Total necesario: {math.ceil(n_total)}")

# ---- PASO 6: Recolectar y preparar datos ----
datos = pd.read_csv('anova_multifactorial_2x3.csv')
print(f"\nMedias por subgrupo:")
print(datos.groupby(['campaña', 'franja'])['tasa_conversion'].mean())

# Verificar normalidad
print(f"\nVerificación de normalidad:")
for campana in ['A', 'B']:
    for franja in ['mañana', 'tarde', 'noche']:
        subgrupo = datos.loc[
            (datos['campaña']==campana) & (datos['franja']==franja),
            'tasa_conversion'
        ]
        W, p = shapiro(subgrupo)
        print(f"  {campana}-{franja}: p = {p:.4f} {'✅' if p >= 0.05 else '❌'}")

# Verificar homocedasticidad
grupos = []
for campana in ['A', 'B']:
    for franja in ['mañana', 'tarde', 'noche']:
        subgrupo = datos.loc[
            (datos['campaña']==campana) & (datos['franja']==franja),
            'tasa_conversion'
        ].to_numpy()
        grupos.append(subgrupo)

B, p_bartlett = bartlett(*grupos)
print(f"\nBartlett: p = {p_bartlett:.4f} {'✅' if p_bartlett >= 0.05 else '❌'}")

# ---- PASO 7: Gráficos exploratorios ----
# Efecto principal de campaña
plt.figure(figsize=(6, 4))
sns.boxplot(data=datos, x='campaña', y='tasa_conversion')
plt.title("Efecto principal: Campaña")
plt.show()

# Efecto principal de franja
plt.figure(figsize=(6, 4))
sns.boxplot(data=datos, x='franja', y='tasa_conversion')
plt.title("Efecto principal: Franja horaria")
plt.show()

# Interacción
medias = datos.groupby(['campaña', 'franja'])['tasa_conversion'].mean().reset_index()
plt.figure(figsize=(8, 5))
sns.pointplot(data=medias, x='franja', y='tasa_conversion', hue='campaña')
plt.title("Interacción: Campaña × Franja")
plt.ylabel("Tasa de conversión (%)")
plt.show()

# ---- PASO 8: Aplicar la prueba estadística ----
resultados = anova(
    data=datos,
    dv='tasa_conversion',
    between=['campaña', 'franja'],
    effsize='n2',
    detailed=True
)

# Calcular f de Cohen
resultados['f-cohen'] = np.sqrt(resultados['n2'] / (1 - resultados['n2']))

# Calcular potencia por cada efecto
N = len(datos)
potencias = []
for idx in range(3):
    f_cohen = resultados.loc[idx, 'f-cohen']
    k = resultados.loc[idx, 'DF'] + 1
    potencia = analisis.power(effect_size=f_cohen, nobs=N, alpha=alpha, k_groups=k)
    potencias.append(potencia)
resultados['potencia'] = potencias + [None]

print("\nResultados de ANOVA:")
print(resultados)

# ---- PASO 9: Interpretar resultados ----
print("\n=== Interpretación ===")
for idx in range(3):
    fuente = resultados.loc[idx, 'Source']
    p = resultados.loc[idx, 'p-unc']
    n2 = resultados.loc[idx, 'n2']
    f_cohen = resultados.loc[idx, 'f-cohen']
    potencia = resultados.loc[idx, 'potencia']
    
    print(f"\n{fuente}:")
    print(f"  p = {p:.2e} → {'❌ Significativo' if p < 0.05 else '✅ No significativo'}")
    print(f"  η² = {n2:.4f} → {'Grande' if n2 >= 0.14 else 'Mediano' if n2 >= 0.06 else 'Pequeño'}")
    print(f"  f = {f_cohen:.4f}")
    print(f"  Potencia = {potencia:.4f}")

# ---- PASO 10: Post-hoc si es necesario ----
# Para efectos significativos
for idx in range(3):
    fuente = resultados.loc[idx, 'Source']
    p = resultados.loc[idx, 'p-unc']
    
    if p < 0.05:
        print(f"\nPost-hoc para {fuente}:")
        if fuente == 'campaña':
            tukey = pairwise_tukey(dv='tasa_conversion', between='campaña', data=datos)
        elif fuente == 'franja':
            tukey = pairwise_tukey(dv='tasa_conversion', between='franja', data=datos)
        else:
            datos['subgrupo'] = datos['campaña'] + '-' + datos['franja']
            tukey = pairwise_tukey(dv='tasa_conversion', between='subgrupo', data=datos)
        print(tukey)
```

---

## Resumen: cuándo usar cada prueba

```
¿Qué querés hacer?
│
├── Comparar medias considerando 2+ factores
│   │
│   ├── ¿Los factores son independientes?
│   │   ├── SÍ → ANOVA multifactorial
│   │   └── NO → ANOVA de medidas repetidas / mixed
│   │
│   ├── ¿Querés ver si hay INTERACCIÓN?
│   │   ├── SÍ → ANOVA multifactorial (el punto clave)
│   │   └── NO → ANOVA de 1 factor por cada factor
│   │
│   └── ¿Los datos son normales?
│       ├── SÍ → ANOVA multifactorial estándar
│       └── NO → Pruebas no paramétricas
│
├── Comparar solo 1 factor con 3+ grupos
│   └── ANOVA de 1 factor
│
├── Comparar solo 2 grupos
│   └── Prueba t de dos muestras
│
└── ¿No sabés cuál usar?
    ├── 2 factores + quiero ver interacción → ANOVA multifactorial
    ├── 1 factor + 3+ grupos → ANOVA de 1 factor
    └── Medidas repetidas → ANOVA de medidas repetidas
```

---

## Código rápido de referencia

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, bartlett
from pingouin import anova, pairwise_tukey
from statsmodels.stats.power import FTestAnovaPower

# ============================================
# ANOVA MULTIFACTORIAL (2×3)
# ============================================
# H₀_A: μ_A1 = μ_A2 (factor A)
# H₀_B: μ_B1 = μ_B2 = μ_B3 (factor B)
# H₀_AB: no hay interacción A×B

# Cargar datos
datos = pd.read_csv('datos.csv')

# Verificar normalidad en cada subgrupo
for a in datos['factor_a'].unique():
    for b in datos['factor_b'].unique():
        subgrupo = datos.loc[(datos['factor_a']==a) & (datos['factor_b']==b), 'valor']
        W, p = shapiro(subgrupo)
        print(f"{a}-{b}: p = {p:.4f} {'✅' if p >= 0.05 else '❌'}")

# Verificar homocedasticidad
grupos = [datos.loc[(datos['factor_a']==a) & (datos['factor_b']==b), 'valor'].to_numpy()
          for a in datos['factor_a'].unique() for b in datos['factor_b'].unique()]
B, p_bartlett = bartlett(*grupos)
print(f"Bartlett: p = {p_bartlett:.4f} {'✅' if p_bartlett >= 0.05 else '❌'}")

# Aplicar ANOVA multifactorial
resultados = anova(
    data=datos,
    dv='valor',
    between=['factor_a', 'factor_b'],
    effsize='n2',
    detailed=True
)

# Calcular f de Cohen y potencia
resultados['f-cohen'] = np.sqrt(resultados['n2'] / (1 - resultados['n2']))
analisis = FTestAnovaPower()
N = len(datos)

potencias = []
for idx in range(3):
    f_cohen = resultados.loc[idx, 'f-cohen']
    k = resultados.loc[idx, 'DF'] + 1
    potencia = analisis.power(effect_size=f_cohen, nobs=N, alpha=0.05, k_groups=k)
    potencias.append(potencia)
resultados['potencia'] = potencias + [None]

print(resultados)

# ============================================
# CÁLCULO DE TAMAÑO DE MUESTRA
# ============================================
a = 2  # Niveles factor A
b = 3  # Niveles factor B

n_A = analisis.solve_power(effect_size=0.4, alpha=0.05, power=0.9, k_groups=a)
n_B = analisis.solve_power(effect_size=0.4, alpha=0.05, power=0.9, k_groups=b)
n_AB = analisis.solve_power(effect_size=0.4, alpha=0.05, power=0.9, k_groups=a*b)

n_total = max(n_A, n_B, n_AB)
print(f"Total necesario: {int(n_total) + 1}")
print(f"Por subgrupo: {int(n_total / (a*b)) + 1}")

# ============================================
# POST-HOC
# ============================================
from pingouin import pairwise_tukey

# Para factor A
tukey_A = pairwise_tukey(dv='valor', between='factor_a', data=datos)
print(tukey_A)

# Para factor B
tukey_B = pairwise_tukey(dv='valor', between='factor_b', data=datos)
print(tukey_B)

# Para interacción (crear variable combinada)
datos['subgrupo'] = datos['factor_a'] + '-' + datos['factor_b']
tukey_AB = pairwise_tukey(dv='valor', between='subgrupo', data=datos)
print(tukey_AB)
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Definir el problema del negocio | Reunión con stakeholders |
| 2 | Redactar como problema de Ciencia de Datos | Formulación clara (3 preguntas) |
| 3 | Definir factores y sus niveles | Diseño experimental |
| 4 | Definir H₀/H₁ para cada efecto | Conocimiento del dominio |
| 5 | Verificar que los factores son independientes | Diseño del experimento |
| 6 | Verificar normalidad en CADA subgrupo | `scipy.stats.shapiro` |
| 7 | Verificar homocedasticidad | `scipy.stats.bartlett` |
| 8 | Definir α para las 3 pruebas | Convención (0.05 típico) |
| 9 | Definir potencia deseada | 0.8 o 0.9 típico |
| 10 | Calcular tamaño del efecto esperado | Cohen's f |
| 11 | Calcular tamaño de muestra por efecto | `FTestAnovaPower.solve_power` |
| 12 | Tomar el máximo como total necesario | Cálculo |
| 13 | Recolectar datos (≥ n por subgrupo) | Experimento / medición |
| 14 | Graficar efectos principales | `seaborn.boxplot` |
| 15 | Graficar interacciones | `seaborn.pointplot` |
| 16 | Aplicar ANOVA multifactorial | `pingouin.anova` |
| 17 | Evaluar cada p-valor por separado | Decisión |
| 18 | Si hay interacción, interpretar primero | Análisis gráfico |
| 19 | Calcular η² parcial y f de Cohen | Fórmula manual |
| 20 | Calcular potencia por cada efecto | `FTestAnovaPower.power` |
| 21 | Hacer post-hoc si es necesario | `pingouin.pairwise_tukey` |
| 22 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lección 10: ANOVA Multifactorial.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- statsmodels Documentation. FTestAnovaPower — Power analysis for ANOVA.
- Pingouin Documentation. `pingouin.anova` — Factorial ANOVA.
- SciPy Documentation. `scipy.stats.f` — F continuous random variable.
