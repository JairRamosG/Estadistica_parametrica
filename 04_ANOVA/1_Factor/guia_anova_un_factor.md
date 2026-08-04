# Guía de Referencia: ANOVA de Un Factor (One-Way ANOVA)

## ¿Qué es?

La ANOVA de un factor es una herramienta **estadística paramétrica** que determina si existen diferencias estadísticamente significativas entre las medias de **tres o más grupos independientes**. Compara la variación entre los grupos contra la variación dentro de los grupos usando la distribución F de Snedecor.

**Pregunta central:** ¿Las medias de todos los grupos son iguales, o al menos un grupo es diferente?

**Qué NO hace:**
- No compara solo dos grupos entre sí (para eso está la prueba t de dos muestras)
- No asume que los datos son normales (pero SÍ requiere normalidad en cada grupo y homocedasticidad)
- No identifica QUÉ grupos son diferentes (para eso se usan pruebas post-hoc como Tukey)
- No reemplaza el juicio experto sobre si el tamaño del efecto es práctico
- **No prueban causalidad** — solo describen si hay diferencia estadísticamente significativa

### Por qué ANOVA y no múltiples pruebas t

Si comparáramos cada par de grupos con pruebas t, acumularíamos errores tipo I. Con 3 grupos serían 3 comparaciones; con 10 grupos serían 45. ANOVA controla el error tipo I global en un solo paso.

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás comparar medias de 3 o más grupos?
│
│   ├── ¿Los grupos son independientes?
│   │   ├── SÍ → ANOVA de 1 factor (One-Way ANOVA)
│   │   └── NO (mediciones repetidas) → ANOVA de medidas repetidas
│   │
│   ├── ¿Los datos son normales en CADA grupo?
│   │   ├── SÍ → Podés usar ANOVA
│   │   └── NO → Considerá Kruskal-Wallis (no paramétrica)
│   │
│   ├── ¿Las varianzas son iguales entre grupos?
│   │   ├── SÍ (homocedasticidad) → ANOVA estándar
│   │   └── NO (heterocedasticidad) → Welch's ANOVA
│   │
│   └── ¿Querés saber QUÉ grupos son diferentes?
│       └── Después de ANOVA, usar pruebas post-hoc (Tukey HSD)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| Comparar medias de 3+ grupos independientes | ANOVA de 1 factor |
| Comparar solo 2 grupos | Prueba t de dos muestras |
| Datos no normales, 3+ grupos | Kruskal-Wallis |
| Varianzas desiguales | Welch's ANOVA |
| Medidas repetidas (mismo sujeto) | ANOVA de medidas repetidas |
| ANOVA significativo, querés saber qué grupo difiere | Tukey HSD (post-hoc) |

### ¿Por qué importa la normalidad y la homocedasticidad?

- **Normalidad**: ANOVA asume que los datos en CADA grupo siguen una distribución normal. Si no se cumple, el p-valor puede ser inexacto.
- **Homocedasticidad**: ANOVA asume que las varianzas de todos los grupos son iguales. Si un grupo tiene mucha más varianza que otro, el test puede ser engañoso.
- Si alguno de estos supuestos se viola gravemente, considerá pruebas no paramétricas (Kruskal-Wallis) o Welch's ANOVA.

---

## Comparación entre pruebas

### Comparación directa

| Aspecto | Prueba t (2 muestras) | ANOVA de 1 factor |
|---|---|---|
| **Grupos comparados** | 2 | 3 o más |
| **Distribución** | t de Student | F de Snedecor |
| **Siempre unilateral** | No (puede ser bilateral) | Sí (siempre unilateral derecha) |
| **Rechaza H₀ cuando** | Las medias de 2 grupos difieren | Al menos 1 grupo tiene media diferente |
| **Identifica qué grupo** | Sí (comparas 2 directamente) | No (necesitás post-hoc) |
| **Tamaño del efecto** | Cohen's d | Eta cuadrado (η²) / Cohen's f |
| **Potencia** | NormalIndPower | FTestAnovaPower |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → no hay evidencia de diferencia entre grupos | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar el contexto y el tamaño del efecto | ⚠️ |
| p < 0.05 | Se rechaza H₀ → al menos un grupo difiere significativamente | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de diferencia | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa el resultado?

- **Se rechaza H₀**: Al menos un grupo tiene una media estadísticamente diferente a los demás. NO significa que todos sean diferentes — solo que hay al menos una diferencia.
- **No se rechaza H₀**: No hay evidencia suficiente para decir que las medias difieren. NO significa que las medias sean iguales.
- **Post-hoc**: Si rechazás H₀, necesitás pruebas adicionales (como Tukey HSD) para identificar qué pares de grupos son los que difieren.

---

## Matemáticas detrás de la prueba

### La lógica detrás de ANOVA

ANOVA particiona la variación total de los datos en dos fuentes:

1. **Variación entre grupos (SS_between)**: Cuánto se desvían las medias de cada grupo de la media general
2. **Variación dentro de los grupos (SS_within)**: Cuánto se desvían los datos individuales de la media de su propio grupo

La idea clave: **si las medias de los grupos fueran iguales**, la variación entre grupos sería pequeña comparada con la variación dentro de los grupos.

### Estadístico F

#### Fórmula

$$F = \frac{\text{Varianza entre grupos}}{\text{Varianza dentro de los grupos}} = \frac{MS_{between}}{MS_{within}} = \frac{SS_{between} / (k-1)}{SS_{within} / (N-k)}$$

Donde:
- $SS_{between}$ = suma de cuadrados entre grupos
- $SS_{within}$ = suma de cuadrados dentro de los grupos
- $k$ = número de grupos
- $N$ = número total de observaciones
- $MS_{between}$ = media de cuadrados entre grupos ($SS_{between} / (k-1)$)
- $MS_{within}$ = media de cuadrados dentro de los grupos ($SS_{within} / (N-k)$)

#### Grados de libertad

- **Intergrupo**: $df_{between} = k - 1$
- **Intragrupo**: $df_{within} = N - k$
- **Total**: $df_{total} = N - 1$

#### Proceso

1. Definir la hipótesis nula (H₀: μ₁ = μ₂ = ... = μₖ) y alternativa (H₁: al menos una media difiere)
2. Calcular las medias de cada grupo
3. Calcular la media general (todas las observaciones)
4. Calcular SS_between: qué tanto se desvían las medias de grupo de la media general
5. Calcular SS_within: qué tanto se desvían los datos individuales de la media de su grupo
6. Calcular F: la razón entre las dos varianzas
7. Comparar F con el valor crítico o calcular el p-valor

#### Ejemplo manual simplificado

```python
# Supongamos 3 grupos con estas medias:
# Grupo A: media = 3.11, n = 203
# Grupo B: media = 3.31, n = 205
# Grupo C: media = 2.96, n = 202
# Media general = 3.13

# SS_between = Σ n_i * (media_i - media_general)²
# SS_between = 203*(3.11-3.13)² + 205*(3.31-3.13)² + 202*(2.96-3.13)²
# SS_between ≈ 12.39

# SS_within = Σ Σ (x_ij - media_grupo)²
# SS_within ≈ 256.80

# F = (12.39 / (3-1)) / (256.80 / (610-3))
# F = 6.19 / 0.42
# F ≈ 14.64
```

### Tamaño del efecto

#### Eta cuadrado (η²)

$$\eta^2 = \frac{SS_{between}}{SS_{total}} = \frac{SS_{between}}{SS_{between} + SS_{within}}$$

Donde:
- $\eta^2$ = proporción de la varianza total explicada por el factor
- Valores de 0 a 1

#### Interpretación de η²

| Valor de η² | Interpretación |
|---|---|
| η² < 0.01 | Efecto pequeño / despreciable |
| 0.01 ≤ η² < 0.06 | Efecto pequeño |
| 0.06 ≤ η² < 0.14 | Efecto mediano |
| η² ≥ 0.14 | Efecto grande |

#### F de Cohen (f)

$$f = \sqrt{\frac{\eta^2}{1 - \eta^2}}$$

Donde:
- $f$ = tamaño del efecto normalizado
- Si $\eta^2 \rightarrow 0$ entonces $f \rightarrow 0$ (efecto pequeño)
- Si $\eta^2 \rightarrow 1$ entonces $f \rightarrow \text{grande}$ (efecto grande)

#### Interpretación de f (Cohen)

| Valor de f | Interpretación |
|---|---|
| f ≈ 0.10 | Efecto pequeño |
| f ≈ 0.25 | Efecto mediano |
| f ≈ 0.40 | Efecto grande |

### Potencia de la prueba

La potencia es la probabilidad de rechazar correctamente H₀ cuando es falsa (1 - β).

#### Cálculo de tamaño de muestra

```python
from statsmodels.stats.power import FTestAnovaPower

analisis = FTestAnovaPower()
n = analisis.solve_power(
    effect_size=f,        # Tamaño del efecto (Cohen's f)
    alpha=0.05,           # Nivel de significancia
    power=0.8,            # Potencia deseada
    k_groups=3            # Número de grupos
)
```

#### Cálculo de potencia

```python
analisis = FTestAnovaPower()
potencia = analisis.power(
    effect_size=f_cohen,  # Tamaño del efecto observado
    nobs=N,               # Número total de observaciones
    alpha=0.05,           # Nivel de significancia
    k_groups=3            # Número de grupos
)
```

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Explorar las medias por grupo**

> Un equipo de marketing quiere ver si tres campañas de email tienen diferentes tasas de conversión.

```python
import pandas as pd

# Cargar datos
datos = pd.read_csv('campañas_tasas_conversion.csv')

# Ver las medias por campaña
medias = datos.groupby('campaña')['tasa_conversión'].mean()
print(medias)
# campaña
# A    3.11
# B    3.31
# C    2.96
```

**Ejemplo 2: Contar observaciones por grupo**

```python
conteos = datos['campaña'].value_counts()
print(conteos)
# B    205
# A    203
# C    202
```

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 3: Verificar normalidad en cada grupo**

```python
from scipy.stats import shapiro

niveles = datos['campaña'].unique()
for nivel in niveles:
    grupo = datos.loc[datos['campaña']==nivel, 'tasa_conversión']
    W, p_shapiro = shapiro(grupo)
    print(f"Grupo {nivel}: W = {W:.4f}, p = {p_shapiro:.4f}")
    if p_shapiro >= 0.05:
        print(f"  ✅ Normalidad verificada")
    else:
        print(f"  ❌ Normalidad NO verificada")
# Grupo A: W = 0.997, p = 0.6054 → ✅
# Grupo B: W = 0.996, p = 0.3254 → ✅
# Grupo C: W = 0.996, p = 0.2272 → ✅
```

**Ejemplo 4: Verificar homocedasticidad (varianzas iguales)**

```python
from scipy.stats import bartlett

# Extraer grupos como arreglos de NumPy
grupos = []
for nivel in niveles:
    grupo = datos.loc[datos['campaña']==nivel, 'tasa_conversión'].to_numpy()
    grupos.append(grupo)

# Prueba de Bartlett
B, p_bartlett = bartlett(*grupos)
print(f"Bartlett: B = {B:.4f}, p = {p_bartlett:.4f}")
if p_bartlett >= 0.05:
    print("✅ Homocedasticidad verificada (varianzas iguales)")
else:
    print("❌ Heterocedasticidad (varianzas desiguales) — considerar Welch's ANOVA")
# Bartlett: B = 4.35, p = 0.1125 → ✅
```

---

### 3. En Feature Engineering

**Ejemplo 5: Calcular tamaño del efecto esperado (diseño del experimento)**

> Esperamos un tamaño del efecto grande (f = 0.4) con 3 grupos.

```python
from statsmodels.stats.power import FTestAnovaPower

# Parámetros del diseño
f_esperado = 0.4      # Tamaño del efecto (grande)
alpha = 0.05          # Nivel de significancia
power = 0.9           # Potencia deseada
k_grupos = 3          # Número de campañas

# Calcular tamaño de muestra por grupo
analisis = FTestAnovaPower()
n_por_grupo = analisis.solve_power(
    effect_size=f_esperado,
    alpha=alpha,
    power=power,
    k_groups=k_grupos
)
print(f"Tamaño mínimo por grupo: {n_por_grupo:.2f}")  # ~82.16
print(f"Necesitás recolectar al menos {int(n_por_grupo) + 1} observaciones por grupo")
```

**Ejemplo 6: Calcular tamaño de efecto a partir de η² esperado**

```python
import numpy as np

# Si esperamos que el factor explique el 10% de la varianza
eta_cuadrado = 0.10
f_cohen = np.sqrt(eta_cuadrado / (1 - eta_cuadrado))
print(f"Tamaño del efecto (f): {f_cohen:.4f}")  # 0.333 → efecto mediano-grande
```

---

### 4. En Selección de Modelos

**Ejemplo 7: Calcular tamaño de muestra con parámetros específicos**

> Queremos potencia de 0.8, tamaño del efecto mediano (f = 0.25), y 4 grupos.

```python
from statsmodels.stats.power import FTestAnovaPower
import math

analisis = FTestAnovaPower()

n = analisis.solve_power(
    effect_size=0.25,    # Efecto mediano
    alpha=0.05,
    power=0.8,
    k_groups=4           # 4 campañas
)
print(f"Tamaño mínimo por grupo: {math.ceil(n)}")  # ~128
```

**Ejemplo 8: Comparar poder entre diferentes números de grupos**

```python
analisis = FTestAnovaPower()

for k in [3, 4, 5, 6]:
    n = analisis.solve_power(effect_size=0.25, alpha=0.05, power=0.8, k_groups=k)
    print(f"Con {k} grupos: n = {math.ceil(n)} por grupo")
# Con 3 grupos: n = 128 por grupo
# Con 4 grupos: n = 97 por grupo
# Con 5 grupos: n = 80 por grupo
# Con 6 grupos: n = 70 por grupo
```

---

### 5. En Evaluación post-deploy

**Ejemplo 9: Aplicar la prueba ANOVA**

```python
from pingouin import anova

resultados = anova(
    data=datos,
    dv='tasa_conversión',      # Variable numérica (observaciones)
    between='campaña',          # Columna con el factor
    detailed=True,              # Resultados detallados
    effsize='n2',               # Calcular eta cuadrado
)
print(resultados)
```

**Ejemplo 10: Interpretar la tabla de resultados**

```python
# La tabla de pingouin muestra:
# - Source: campaña (intergrupo) y Within (intragrupo)
# - SS: sumas de cuadrados
# - DF: grados de libertad
# - MS: medias de cuadrados (varianzas)
# - F: estadística de prueba
# - p-unc: valor p
# - n2: eta cuadrado (tamaño del efecto)

f_stat = resultados.loc[0, 'F']
p_valor = resultados.loc[0, 'p-unc']
n2 = resultados.loc[0, 'n2']

print(f"Estadística F: {f_stat:.4f}")
print(f"Valor p: {p_valor:.6f}")
print(f"Eta cuadrado: {n2:.4f}")
```

**Ejemplo 11: Visualizar la distribución F**

```python
import matplotlib.pyplot as plt
from scipy.stats import f
import numpy as np

# Grados de libertad
dof_inter = resultados.loc[0, 'DF']
dof_intra = resultados.loc[1, 'DF']
f_val = resultados.loc[0, 'F']
alpha = 0.05

# Distribución F
f_vals = np.linspace(0, 20, 1000)
pdf_vals = f.pdf(f_vals, dof_inter, dof_intra)

# Punto crítico (siempre unilateral derecho)
f_critico = f.ppf(1 - alpha, dof_inter, dof_intra)

# Graficar
plt.figure(figsize=(15, 6))
plt.plot(f_vals, pdf_vals, label=f"F({int(dof_inter)}, {int(dof_intra)})", color='blue')

# Zona de rechazo
f_rechazo = np.linspace(f_critico, 20, 100)
plt.fill_between(f_rechazo, f.pdf(f_rechazo, dof_inter, dof_intra), color='blue', alpha=0.4)

# Estadística observada
plt.axvline(f_val, color='red', linestyle='--', label=f'F observado = {f_val:.2f}')

plt.title('ANOVA de 1 factor: distribución F y zona de rechazo')
plt.xlabel('Valor F')
plt.ylabel('Densidad')
plt.legend()
plt.grid(True)
plt.show()
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 12: Calcular tamaño del efecto observado (η² y f de Cohen)**

```python
import numpy as np

n2 = resultados.loc[0, 'n2']
f_cohen = np.sqrt(n2 / (1 - n2))

print(f"Eta cuadrado (η²): {n2:.4f}")
print(f"F de Cohen: {f_cohen:.4f}")
# η² = 0.046 → efecto pequeño-mediano
# f = 0.22 → efecto pequeño-mediano
```

**Ejemplo 13: Calcular potencia posterior**

```python
from statsmodels.stats.power import FTestAnovaPower

analisis = FTestAnovaPower()

potencia = analisis.power(
    effect_size=f_cohen,
    nobs=len(datos),
    alpha=0.05,
    k_groups=3
)
print(f"Potencia actualizada: {potencia:.4f}")
# Potencia = 0.9990 → prácticamente 100%
```

---

### 7. En Validación de supuestos

**Ejemplo 14: Prueba post-hoc de Tukey (si ANOVA es significativo)**

```python
from pingouin import pairwise_tukey

# Solo si se rechazó H₀ en ANOVA
if p_valor < 0.05:
    tukey = pairwise_tukey(
        dv='tasa_conversión',
        between='campaña',
        data=datos
    )
    print(tukey)
    # Muestra qué pares de grupos son significativamente diferentes
```

**Ejemplo 15: Verificar supuestos completos antes del test**

```python
from scipy.stats import shapiro, bartlett

def verificar_supuestos(datos, grupo_col, valor_col):
    """Verifica normalidad y homocedasticidad para ANOVA"""
    niveles = datos[grupo_col].unique()
    
    # 1. Normalidad por grupo
    print("=== Verificación de Normalidad (Shapiro-Wilk) ===")
    todos_normales = True
    for nivel in niveles:
        grupo = datos.loc[datos[grupo_col]==nivel, valor_col]
        W, p = shapiro(grupo)
        estado = "✅" if p >= 0.05 else "❌"
        print(f"  Grupo {nivel}: p = {p:.4f} {estado}")
        if p < 0.05:
            todos_normales = False
    
    # 2. Homocedasticidad
    print("\n=== Verificación de Homocedasticidad (Bartlett) ===")
    grupos = [datos.loc[datos[grupo_col]==nivel, valor_col].to_numpy() for nivel in niveles]
    B, p_bartlett = bartlett(*grupos)
    estado = "✅" if p_bartlett >= 0.05 else "❌"
    print(f"  Bartlett: p = {p_bartlett:.4f} {estado}")
    
    return todos_normales, p_bartlett >= 0.05

# Uso
normal, homo = verificar_supuestos(datos, 'campaña', 'tasa_conversión')
if normal and homo:
    print("\n✅ Todos los supuestos verificados — ANOVA válido")
else:
    print("\n⚠️ Supuestos violados — considerar alternativas")
```

---

## Interpretación de resultados

### Salida del cálculo de F

```python
# F = MS_between / MS_within
# F = 6.19 / 0.42 = 14.64
```

**Cómo leerlo:**
- **F grande** (mucho mayor que 1): la variación entre grupos es grande comparada con la variación dentro → evidencia de que las medias difieren
- **F ≈ 1**: la variación entre grupos es similar a la variación dentro → no hay evidencia de diferencia
- **F siempre positivo**: la distribución F solo toma valores positivos (es unilateral derecha)

### Salida del cálculo de p

```python
# El p-valor se obtiene de la distribución F con los grados de libertad correspondientes
# scipy.stats.f.sf(F, dof_inter, dof_intra)  → función de supervivencia (1 - CDF)
```

### Reglas de decisión

```
ANOVA SIEMPRE ES UNILATERAL DERECHA (α = 0.05):
F > F_crítico  → ❌ Se rechaza H₀ (al menos un grupo difiere)
F ≤ F_crítico  → ✅ No se rechaza H₀

O equivalentemente:
p < 0.05  → ❌ Se rechaza H₀
p ≥ 0.05  → ✅ No se rechaza H₀
```

### ¿Qué reportar?

```python
# Formato completo
print(f"ANOVA de 1 factor:")
print(f"  Factor: campaña (k = {k_grupos})")
print(f"  n total = {len(datos)}")
print(f"  SS_between = {ss_between:.4f}")
print(f"  SS_within = {ss_within:.4f}")
print(f"  F({int(dof_inter)}, {int(dof_intra)}) = {f_stat:.4f}")
print(f"  p = {p_valor:.6f}")
print(f"  η² = {n2:.4f}")
print(f"  f de Cohen = {f_cohen:.4f}")
print(f"  Potencia = {potencia:.4f}")
```

### ¿Qué hacer según el resultado?

1. **Rechazás H₀**: Al menos un grupo tiene media diferente
   - Reportar el p-valor, η², f de Cohen y la potencia
   - Ejecutar pruebas post-hoc (Tukey HSD) para identificar qué grupos difieren
   - Evaluar si el tamaño del efecto es prácticamente relevante
2. **No rechazás H₀**: No hay evidencia suficiente
   - NO significa que las medias sean iguales
   - Verificar si la potencia fue suficiente
   - Verificar si el tamaño de muestra fue adecuado
   - Verificar que los supuestos se cumplieron
3. **Zona gris (0.05 < p < 0.10)**:
   - Considerar el contexto del negocio
   - Reportar el intervalo de confianza
   - Replicar con más datos si es posible

---

## Errores comunes

### 1. Usar ANOVA para comparar solo 2 grupos

```python
# MAL: usar ANOVA para 2 grupos
# Con solo 2 grupos, ANOVA y t-test son equivalentes, pero t-test es más directo
from pingouin import anova
resultados = anova(data=df, dv='valor', between='grupo')  # ❌ Solo 2 grupos

# BIEN: usar t-test para 2 grupos
from scipy.stats import ttest_ind
t, p = ttest_ind(grupo_a, grupo_b)  # ✅ Más directo
```

### 2. No verificar normalidad antes del test

```python
# MAL: aplicar ANOVA sin verificar normalidad
from pingouin import anova
resultados = anova(data=datos, dv='tasa', between='campaña')  # ❌ ¿Y si no es normal?

# BIEN: verificar normalidad primero
from scipy.stats import shapiro
for nivel in datos['campaña'].unique():
    grupo = datos.loc[datos['campaña']==nivel, 'tasa']
    W, p = shapiro(grupo)
    if p < 0.05:
        print(f"❌ Grupo {nivel} NO es normal — considerar Kruskal-Wallis")
        break
# Si todos son normales, proceder con ANOVA
```

### 3. No verificar homocedasticidad

```python
# MAL: asumir que las varianzas son iguales
# Si un grupo tiene mucha más varianza, ANOVA puede ser engañoso

# BIEN: verificar con Bartlett o Levene
from scipy.stats import bartlett
grupos = [datos.loc[datos['campaña']==n, 'tasa'].to_numpy() for n in datos['campaña'].unique()]
B, p = bartlett(*grupos)
if p < 0.05:
    print("❌ Heterocedasticidad — usar Welch's ANOVA")
```

### 4. No hacer pruebas post-hoc después de rechazar H₀

```python
# MAL: rechazar H₀ y concluir "los grupos son diferentes"
# ANOVA solo dice QUE HAY diferencia, no CUÁLES grupos difieren

# BIEN: hacer Tukey HSD para identificar qué pares difieren
from pingouin import pairwise_tukey
tukey = pairwise_tukey(dv='tasa', between='campaña', data=datos)
print(tukey)
# Muestra qué pares son significativamente diferentes
```

### 5. Confundir p-valor con tamaño del efecto

```python
# MAL: "p = 0.0000006, ¡el efecto es enorme!"
# El p-valor NO dice qué tan GRANDE es la diferencia

# BIEN: reportar p-valor Y tamaño del efecto
print(f"p = {p_valor:.6f} → {'Significativo' if p_valor < 0.05 else 'No significativo'}")
print(f"η² = {n2:.4f} → {'Grande' if n2 >= 0.14 else 'Mediano' if n2 >= 0.06 else 'Pequeño'}")
print(f"f = {f_cohen:.4f}")
```

### 6. Usar ANOVA con datos de medidas repetidas

```python
# MAL: usar ANOVA de 1 factor con el mismo sujeto medido múltiples veces
# Los datos no son independientes → resultado inválido

# BIEN: usar ANOVA de medidas repetidas o mixed ANOVA
from pingouin import rm_anova
resultados = rm_anova(data=datos, dv='valor', within='tiempo', subject='sujeto')
```

### 7. No redondear el tamaño de muestra al entero superior

```python
# MAL: usar el tamaño de muestra como float
n = analisis.solve_power(effect_size=0.4, alpha=0.05, power=0.9, k_groups=3)
# n = 82.16 → usar 82.16 como tamaño de muestra ❌

# BIEN: redondear al entero superior
import math
n = math.ceil(analisis.solve_power(effect_size=0.4, alpha=0.05, power=0.9, k_groups=3))
# n = 83 ✅
```

### 8. Interpretar "no rechazar H₀" como "las medias son iguales"

```python
# MAL: "p = 0.08, por tanto las medias son iguales"
# No rechazar H₀ NO significa que H₀ sea verdadera

# BIEN: reportar correctamente
if p_valor >= 0.05:
    print("No se rechaza H₀: no hay evidencia suficiente de diferencia")
    print("Esto NO implica que las medias sean iguales")
    print("Verificar si la potencia fue suficiente para detectar un efecto")
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, bartlett, f
from pingouin import anova, pairwise_tukey
from statsmodels.stats.power import FTestAnovaPower
import matplotlib.pyplot as plt
import math

# ============================================
# FLUJO COMPLETO: ANOVA de 1 factor
# ============================================

# ---- PASO 1: Definir el problema del negocio ----
# Ejemplo: ¿Las tres campañas de email marketing tienen diferentes tasas de conversión?

# ---- PASO 2: Redactar como problema de Ciencia de Datos ----
# ¿Existen diferencias estadísticamente significativas en las tasas medias de conversión?

# ---- PASO 3: Definir H₀ y H₁ ----
# H₀: μ_A = μ_B = μ_C (las medias son iguales)
# H₁: Al menos una media difiere
k_grupos = 3

# ---- PASO 4: Definir α ----
alpha = 0.05

# ---- PASO 5: Calcular potencia y tamaño de muestra ----
f_esperado = 0.4      # Tamaño del efecto (grande)
power = 0.9

analisis = FTestAnovaPower()
n_por_grupo = analisis.solve_power(
    effect_size=f_esperado,
    alpha=alpha,
    power=power,
    k_groups=k_grupos
)
print(f"Tamaño mínimo por grupo: {math.ceil(n_por_grupo)}")

# ---- PASO 6: Recolectar y preparar datos ----
datos = pd.read_csv('campañas_tasas_conversion.csv')
print(f"\nMedias por campaña:")
print(datos.groupby('campaña')['tasa_conversión'].mean())

# Verificar normalidad
print(f"\nVerificación de normalidad:")
for nivel in datos['campaña'].unique():
    grupo = datos.loc[datos['campaña']==nivel, 'tasa_conversión']
    W, p_shapiro = shapiro(grupo)
    print(f"  Grupo {nivel}: p = {p_shapiro:.4f} {'✅' if p_shapiro >= 0.05 else '❌'}")

# Verificar homocedasticidad
grupos = [datos.loc[datos['campaña']==n, 'tasa_conversión'].to_numpy() 
          for n in datos['campaña'].unique()]
B, p_bartlett = bartlett(*grupos)
print(f"\nBartlett: p = {p_bartlett:.4f} {'✅' if p_bartlett >= 0.05 else '❌'}")

# ---- PASO 7: Aplicar la prueba estadística ----
resultados = anova(
    data=datos,
    dv='tasa_conversión',
    between='campaña',
    detailed=True,
    effsize='n2'
)

f_stat = resultados.loc[0, 'F']
p_valor = resultados.loc[0, 'p-unc']
n2 = resultados.loc[0, 'n2']
dof_inter = resultados.loc[0, 'DF']
dof_intra = resultados.loc[1, 'DF']

print(f"\nResultados de ANOVA:")
print(f"  F({int(dof_inter)}, {int(dof_intra)}) = {f_stat:.4f}")
print(f"  p = {p_valor:.6f}")
print(f"  η² = {n2:.4f}")
print(f"  ¿Se rechaza H₀? {'SÍ' if p_valor < alpha else 'NO'}")

# ---- PASO 8: Post-hoc si se rechazó H₀ ----
if p_valor < alpha:
    print("\nPrueba post-hoc de Tukey:")
    tukey = pairwise_tukey(dv='tasa_conversión', between='campaña', data=datos)
    print(tukey)

# ---- PASO 9: Evaluar tamaño del efecto y potencia ----
f_cohen = np.sqrt(n2 / (1 - n2))

potencia = analisis.power(
    effect_size=f_cohen,
    nobs=len(datos),
    alpha=alpha,
    k_groups=k_grupos
)

print(f"\nTamaño del efecto:")
print(f"  η² = {n2:.4f} ({'Grande' if n2 >= 0.14 else 'Mediano' if n2 >= 0.06 else 'Pequeño'})")
print(f"  f de Cohen = {f_cohen:.4f}")
print(f"  Potencia = {potencia:.4f}")

# ---- CONCLUSIÓN ----
if p_valor < alpha:
    print(f"\n✅ Se rechaza H₀: Existe al menos una diferencia significativa entre campañas")
    print(f"   Potencia: {potencia:.4f} — resultado confiable")
else:
    print(f"\n✅ No se rechaza H₀: No hay evidencia de diferencia significativa")
```

---

## Resumen: cuándo usar cada prueba

```
¿Qué querés hacer?
│
├── Comparar medias de 3 o más grupos
│   │
│   ├── ¿Los grupos son independientes?
│   │   ├── SÍ → ANOVA de 1 factor
│   │   └── NO → ANOVA de medidas repetidas
│   │
│   ├── ¿Los datos son normales en cada grupo?
│   │   ├── SÍ → ANOVA estándar
│   │   └── NO → Kruskal-Wallis (no paramétrica)
│   │
│   ├── ¿Las varianzas son iguales?
│   │   ├── SÍ → ANOVA estándar
│   │   └── NO → Welch's ANOVA
│   │
│   └── ¿Rechazaste H₀ y querés saber qué grupo difiere?
│       └── Prueba post-hoc de Tukey HSD
│
├── Comparar solo 2 grupos
│   └── Prueba t de dos muestras (más directo)
│
├── El tamaño del efecto importa
│   └── SIEMPRE reportar η² y f de Cohen junto con el p-valor
│
└── ¿No sabés cuál usar?
    ├── Si tenés 2 grupos → t-test
    ├── Si tenés 3+ grupos independientes → ANOVA
    └── Si tenés medidas repetidas → ANOVA de medidas repetidas
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
# ANOVA DE 1 FACTOR
# ============================================
# H₀: μ₁ = μ₂ = ... = μₖ
# H₁: Al menos una media difiere

# Cargar datos
datos = pd.read_csv('datos.csv')

# Verificar normalidad
for nivel in datos['factor'].unique():
    grupo = datos.loc[datos['factor']==nivel, 'valor']
    W, p = shapiro(grupo)
    print(f"Grupo {nivel}: p = {p:.4f} {'✅' if p >= 0.05 else '❌'}")

# Verificar homocedasticidad
grupos = [datos.loc[datos['factor']==n, 'valor'].to_numpy() for n in datos['factor'].unique()]
B, p_bartlett = bartlett(*grupos)
print(f"Bartlett: p = {p_bartlett:.4f} {'✅' if p_bartlett >= 0.05 else '❌'}")

# Aplicar ANOVA
resultados = anova(
    data=datos,
    dv='valor',
    between='factor',
    detailed=True,
    effsize='n2'
)

# Extraer resultados
f_stat = resultados.loc[0, 'F']
p_valor = resultados.loc[0, 'p-unc']
n2 = resultados.loc[0, 'n2']
f_cohen = np.sqrt(n2 / (1 - n2))

print(f"F = {f_stat:.4f}, p = {p_valor:.6f}, η² = {n2:.4f}, f = {f_cohen:.4f}")

# Post-hoc si es significativo
if p_valor < 0.05:
    tukey = pairwise_tukey(dv='valor', between='factor', data=datos)
    print(tukey)

# ============================================
# CÁLCULO DE TAMAÑO DE MUESTRA
# ============================================
analisis = FTestAnovaPower()

n = analisis.solve_power(
    effect_size=0.4,    # f de Cohen (grande)
    alpha=0.05,
    power=0.9,
    k_groups=3
)
print(f"Tamaño mínimo por grupo: {int(n) + 1}")

# ============================================
# CÁLCULO DE POTENCIA POSTERIOR
# ============================================
potencia = analisis.power(
    effect_size=f_cohen,
    nobs=len(datos),
    alpha=0.05,
    k_groups=3
)
print(f"Potencia: {potencia:.4f}")
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Definir el problema del negocio | Reunión con stakeholders |
| 2 | Redactar como problema de Ciencia de Datos | Formulación clara |
| 3 | Definir H₀ y H₁ | Conocimiento del dominio |
| 4 | Verificar que tenés 3+ grupos independientes | Diseño experimental |
| 5 | Verificar normalidad en CADA grupo | `scipy.stats.shapiro` |
| 6 | Verificar homocedasticidad | `scipy.stats.bartlett` |
| 7 | Definir α (nivel de significancia) | Convención del dominio (0.05 típico) |
| 8 | Definir potencia deseada (1-β) | 0.8 o 0.9 típico |
| 9 | Calcular tamaño del efecto esperado | Cohen's f |
| 10 | Calcular tamaño de muestra mínimo | `statsmodels.FTestAnovaPower.solve_power` |
| 11 | Recolectar datos (≥ n mínimo por grupo) | Experimento / medición |
| 12 | Aplicar ANOVA | `pingouin.anova` |
| 13 | Comparar p con α | Decisión |
| 14 | Si rechazás H₀: pruebas post-hoc | `pingouin.pairwise_tukey` |
| 15 | Calcular η² y f de Cohen observados | Fórmula manual |
| 16 | Calcular potencia posterior | `statsmodels.FTestAnovaPower.power` |
| 17 | Reportar resultados completos | Formato estándar |

---

## Referencias

- Codificando Bits. (2024). Estadística Inferencial: Fundamentos. Lección 9: ANOVA de 1 Factor.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- statsmodels Documentation. FTestAnovaPower — Power analysis for ANOVA.
- Pingouin Documentation. `pingouin.anova` — One-way ANOVA.
- SciPy Documentation. `scipy.stats.f` — F continuous random variable.
