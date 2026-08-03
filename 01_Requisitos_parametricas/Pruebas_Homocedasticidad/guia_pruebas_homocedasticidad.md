# Guía de Referencia: Pruebas de Homocedasticidad

## ¿Qué es?

La homocedasticidad es el supuesto de que la **varianza es constante** entre grupos o niveles de una variable independiente. La prueba de Bartlett determina si dos o más grupos tienen varianzas estadísticamente iguales.

**Pregunta central:** ¿La varianza es la misma en todos los grupos?

**Qué NO hace:**
- No prueba si los datos son normales
- No reemplaza la inspección visual (boxplots)
- No prueban causalidad — solo describe igualdad de varianzas

> **Cuándo importa:** Cuando querés usar pruebas paramétricas como ANOVA o t-test, necesitás que las varianzas sean iguales entre grupos. Si no lo son, considerá Welch's ANOVA o pruebas no paramétricas.

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás verificar homocedasticidad?
│
├── ¿Para qué?
│   │
│   ├── ANOVA o t-test clásico
│   │   └── SÍ → verificar antes de elegir la prueba
│   │
│   ├── Comparar varianzas entre grupos (EDA)
│   │   └── SÍ → boxplot + Bartlett
│   │
│   └── Pruebas no paramétricas (Kruskal-Wallis, Mann-Whitney)
│       └── NO → esas pruebas NO requieren homocedasticidad
│
└── ¿Los datos son normales?
    │
    ├── SÍ → Bartlett (más potente)
    └── NO → Levene (no asume normalidad)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| Verificar varianzas entre grupos (datos normales) | **Bartlett** |
| Verificar varianzas entre grupos (datos no normales) | Levene |
| EDA rápido, ver varianzas visualmente | Boxplot por grupo |
| No sabés si tus datos son normales | Boxplot + Levene |

---

## Comparación entre pruebas

| Aspecto | Bartlett | Levene |
|---|---|---|
| **Requiere normalidad** | Sí | No |
| **Potencia (datos normales)** | **Muy alta** | Moderada |
| **Robustez** | Baja (sensible a no normalidad) | **Alta** |
| **Librería** | `scipy.stats` | `scipy.stats` |
| **Da p-valor** | Sí | Sí |

**¿Cuál elegir?** Si tus datos son normales, usá Bartlett (es más potente). Si no sos seguro de la normalidad, usá Levene.

---

## Matemáticas detrás de la prueba

### Bartlett

#### Fórmula

$$\chi^2 = \frac{(N - k) \ln(S_p^2) - \sum_{i=1}^{k} (n_i - 1) \ln(S_i^2)}{1 + \frac{1}{3(k-1)} \left( \sum_{i=1}^{k} \frac{1}{n_i - 1} - \frac{1}{N - k} \right)}$$

Donde:
- $S_p^2$ = varianza pooled (combinada)
- $S_i^2$ = varianza del grupo i
- $N$ = total de observaciones
- $k$ = número de grupos

#### Proceso

1. Calcular la varianza de cada grupo
2. Calcular la varianza pooled
3. Aplicar la razón de verosimilitud con transformación logarítmica
4. Comparar con distribución chi-cuadrado

#### Interpretación

| Valor | Interpretación |
|---|---|
| χ² bajo | Las varianzas son similares |
| χ² alto | Las varianzas difieren significativamente |

---

## Ejemplos por fase del proyecto

### 1. Carga de datos y exploración

> ¿Tengo homocedasticidad entre grupos?

```python
import pandas as pd

df = pd.read_csv('datos_homocedasticidad.csv')
df
```

El dataset tiene 3 grupos (Grupo 1, Grupo 2, Grupo 3) y una variable continua (Valor), con 900 filas.

### 2. Visualización con boxplots

> ¿Las varianzas se ven iguales entre grupos?

```python
import matplotlib.pyplot as plt
import seaborn as sns
sns.set_style("whitegrid")

plt.figure(figsize=(8, 4))
sns.boxplot(data=df, x='Grupo', y='Valor', hue='Grupo')
plt.title('Boxplot - Varianzas de los grupos')
plt.tight_layout();
```

**Qué buscar:** Si las cajas y bigotes tienen aproximadamente la misma altura y dispersión entre grupos, hay homocedasticidad. Si una caja es mucho más alta o ancha que las otras, hay heterocedasticidad.

### 3. Prueba de Bartlett

> ¿Las varianzas son estadísticamente iguales?

```python
# 1. Subdividir grupos (crear arreglos de NumPy)
gr1 = df[df['Grupo']=='Grupo 1']['Valor'].to_numpy()
gr2 = df[df['Grupo']=='Grupo 2']['Valor'].to_numpy()
gr3 = df[df['Grupo']=='Grupo 3']['Valor'].to_numpy()

# 2. Aplicar la prueba de Bartlett
from scipy.stats import bartlett

B, pb = bartlett(gr1, gr2, gr3)

# 3. Imprimir resultados
print(f"Prueba de Bartlett para las agrupaciones:")
print(f"  Estadística (B) = {B:.4f}, p = {pb:.4f} {'❌ Rechazar H₀ (p<0.05): heterocedasticidad' if pb < 0.05 else '✅ No se rechaza H₀ (p>=0.05): homocedasticidad'}")
```

---

## Interpretación de resultados

### Salida de scipy.stats.bartlett

```python
B, pb = bartlett(gr1, gr2, gr3)
# B = 15.8224
# pb = 0.0004
```

**Cómo leerla:**
- **B**: estadístico de Bartlett (chi-cuadrado). Más alto = mayor diferencia entre varianzas.
- **pb**: valor p. Probabilidad de observar estas diferencias si las varianzas fueran iguales.

### Reglas de decisión

```
p ≥ 0.05  → ✅ No se rechaza H₀ (homocedasticidad: varianzas iguales)
p < 0.05  → ❌ Se rechaza H₀ (heterocedasticidad: varianzas diferentes)
p < 0.01  → ❌❌ Fuerte evidencia de heterocedasticidad
p < 0.001 → ❌❌❌ Evidencia muy fuerte
```

### ¿Qué reportar?

```python
# Formato completo
print(f"Bartlett: B = {B:.4f}, p = {pb:.6f}")

# Ejemplo:
# "Se realizó la prueba de Bartlett para verificar la homocedasticidad
# entre los tres grupos. Se obtuvo B = 15.8224, p = 0.0004,
# lo cual indica que se rechaza la hipótesis de homocedasticidad
# al nivel de significancia del 5%."
```

### ¿Qué hacer si hay heterocedasticidad?

1. **Usar Welch's ANOVA** en lugar de ANOVA clásico
2. **Usar Welch's t-test** en lugar de t-test clásico
3. **Transformar datos**: log, raíz cuadrada, Box-Cox
4. **Usar métodos no paramétricos**: Kruskal-Wallis, Mann-Whitney (no requieren homocedasticidad)

---

## Errores comunes

### 1. Usar Bartlett sin verificar normalidad

```python
# MAL: Usar Bartlett directamente
B, pb = bartlett(gr1, gr2, gr3)  # ❌ Si los datos no son normales, el resultado es inválido

# BIEN: Verificar normalidad primero
from scipy.stats import shapiro
W1, p1 = shapiro(gr1)
W2, p2 = shapiro(gr2)
W3, p3 = shapiro(gr3)

if p1 >= 0.05 and p2 >= 0.05 and p3 >= 0.05:
    # Datos normales → Bartlett es válido
    B, pb = bartlett(gr1, gr2, gr3)
else:
    # Datos no normales → usar Levene
    from scipy.stats import levene
    stat, p = levene(gr1, gr2, gr3)
```

### 2. Confiar solo en el p-valor sin mirar el boxplot

```python
# MAL: "p = 0.06, ¡homocedasticidad!"
# BIEN: mirar el boxplot PRIMERO, después confirmar con Bartlett
plt.figure(figsize=(8, 4))
sns.boxplot(data=df, x='Grupo', y='Valor', hue='Grupo')
plt.title('Verificar visualmente antes de la prueba')
plt.show()

# Después sí, la prueba estadística
B, pb = bartlett(gr1, gr2, gr3)
```

### 3. Olvidar que Bartlett asume normalidad

```python
# MAL: "Bartlett no rechazó, entonces las varianzas son iguales"
# (sin verificar normalidad → resultado puede ser falso)

# BIEN: reportar el supuesto
print("Nota: Bartlett asume normalidad de los datos.")
print(f"Normalidad verificada con Shapiro-Wilk: W={W1:.4f}, p={p1:.4f}")
```

### 4. Usar Bartlett para elegir entre paramétrico y no paramétrico

```python
# MAL: "Si Bartlett rechaza, uso Mann-Whitney"
# (Mann-Whitney NO requiere homocedasticidad)

# BIEN: "Si los datos son normales Y Bartlett no rechaza → ANOVA clásico"
#        "Si los datos son normales Y Bartlett rechaza → Welch's ANOVA"
#        "Si los datos NO son normales → Kruskal-Wallis (sin importar Bartlett)"
```

---

## Código rápido de referencia

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import bartlett, shapiro

# ============================================
# CARGAR DATOS
# ============================================

df = pd.read_csv('datos_homocedasticidad.csv')

# ============================================
# SUBDIVIDIR GRUPOS
# ============================================

gr1 = df[df['Grupo']=='Grupo 1']['Valor'].to_numpy()
gr2 = df[df['Grupo']=='Grupo 2']['Valor'].to_numpy()
gr3 = df[df['Grupo']=='Grupo 3']['Valor'].to_numpy()

# ============================================
# BOXPLOT (inspección visual)
# ============================================

sns.set_style("whitegrid")
plt.figure(figsize=(8, 4))
sns.boxplot(data=df, x='Grupo', y='Valor', hue='Grupo')
plt.title('Boxplot - Varianzas de los grupos')
plt.tight_layout()
plt.show()

# ============================================
# PRUEBA DE BARTLETT
# ============================================

B, pb = bartlett(gr1, gr2, gr3)
print(f"Prueba de Bartlett para las agrupaciones:")
print(f"  Estadística (B) = {B:.4f}, p = {pb:.4f} {'❌ Rechazar H₀ (p<0.05): heterocedasticidad' if pb < 0.05 else '✅ No se rechaza H₀ (p>=0.05): homocedasticidad'}")

# ============================================
# VERIFICACIÓN DE NORMALIDAD (prerequisito)
# ============================================

for i, (gr, nombre) in enumerate([(gr1, 'Grupo 1'), (gr2, 'Grupo 2'), (gr3, 'Grupo 3')]):
    W, p = shapiro(gr)
    print(f"{nombre}: W = {W:.4f}, p = {p:.4f}")
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | Cargar el dataset | `pd.read_csv()` |
| 2 | Subdividir en grupos | Filtrado por columna + `.to_numpy()` |
| 3 | Visualizar varianzas | `sns.boxplot()` |
| 4 | ¿Las cajas se ven similares? | Inspección visual |
| 5 | Verificar normalidad (prerequisito) | `scipy.stats.shapiro` |
| 6 | Ejecutar Bartlett | `scipy.stats.bartlett` |
| 7 | Interpretar p-valor | p ≥ 0.05 → homocedasticidad |
| 8 | Reportar: método, B, p, n, conclusión | Formato completo |

---

## Referencias

- Bartlett, M. S. (1937). Properties of sufficiency and statistical tests. *Proceedings of the Royal Society of London. Series A*, 160(901), 268-282.
- scipy.stats.bartlett: https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.bartlett.html
- scipy.stats.levene: https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.levene.html
