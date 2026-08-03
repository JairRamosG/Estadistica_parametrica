# Guía de Referencia: Pruebas de Homocedasticidad

## ¿Qué es?

Las pruebas de homocedasticidad son herramientas **estadísticas y gráficas** que determinan si la varianza de un grupo de datos es constante a través de los niveles de una variable independiente (o en diferentes grupos/condiciones). Incluyen métodos visuales (gráficos de residuos, gráficos de escala-localización) como pruebas formales (Levene, Bartlett, Breusch-Pagan, Goldfeld-Quandt).

**Pregunta central:** ¿La varianza es constante entre grupos o a lo largo de los valores predichos?

**Qué NO hacen:**
- No prueban si los datos son normales
- No eligen entre pruebas paramétricas y no paramétricas (las pruebas no paramétricas no requieren homocedasticidad)
- No reemplazan el juicio experto ni la inspección visual
- No prueban causalidad — solo describen la igualdad de varianzas
- No son robustas ante tamaños de muestra muy pequeños (pueden fallar con pocos datos)

> **⚠️ INSIGHT CLAVE**: Las pruebas de homocedasticidad se usan cuando:
> 1. Los datos **SON normales** y necesitás elegir entre t-test vs Welch, ANOVA vs Welch's ANOVA
> 2. Validás supuestos de modelos de regresión lineal
> 3. Necesitás entender diferencias de varianza para insights de negocio
> 
> **NO se usan para elegir entre paramétrico y no paramétrico cuando los datos NO son normales** (porque las pruebas no paramétricas no requieren homocedasticidad).

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás verificar homocedasticidad?
│
├── ¿Por qué la necesitás?
│   │
│   ├── Para elegir entre t-test y Welch's t-test
│   │   └── Verificar homocedasticidad ANTES de elegir
│   │   (Solo si los datos SON normales)
│   │
│   ├── Para validar supuestos de regresión lineal
│   │   └── Breusch-Pagan o Goldfeld-Quandt en residuos
│   │
│   ├── Para ANOVA
│   │   └── Levene o Bartlett antes de ANOVA clásica
│   │
│   ├── Para detectar cambios en producción (data drift)
│   │   └── Comparar varianzas entre ventanas de tiempo
│   │
│   └── Para EDA / exploración
│       └── Gráficos de residuos + boxplots
│
├── ¿Los datos son normales?
│   │
│   ├── SÍ → Bartlett (más potente) o Levene
│   └── NO → Levene (no asume normalidad)
│
├── ¿Es un problema de regresión?
│   │
│   ├── SÍ → Breusch-Pagan o Goldfeld-Quandt
│   └── NO → Levene o Bartlett
│
└── ¿Querés solo una idea visual?
    └── Boxplot por grupo + gráfico de residuos
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| EDA rápido, ver varianzas | Boxplot por grupo |
| Comparar varianzas entre grupos (datos normales) | Bartlett |
| Comparar varianzas entre grupos (datos no normales) | Levene |
| Validar homocedasticidad en regresión | Breusch-Pagan |
| Detectar heterocedasticidad en regresión | Goldfeld-Quandt |
| Detectar drift en varianza (producción) | Levene + comparación de ventanas |
| No sabés cuál usar | Levene + gráfico de residuos |

### ¿Por qué combinar métodos gráficos y estadísticos?

- **Los gráficos muestran EL PATRÓN** de la heterocedasticidad (¿es en forma de embudo? ¿varía por grupo?)
- **Las pruebas estadísticas dan un NÚMERO** (p-valor) para tomar una decisión
- **Ninguno solo es suficiente**: un boxplot puede engañar con muestras pequeñas, y un p-valor no te dice el TIPO de heterocedasticidad
- **El método sugerido es combinar ambos**: mirá el gráfico, después confirmá con la prueba

---

## Comparación entre pruebas

### Comparación directa

| Aspecto | Levene | Bartlett | Breusch-Pagan | Goldfeld-Quandt |
|---|---|---|---|---|
| **Tipo** | Estadística formal | Estadística formal | Estadística formal | Estadística formal |
| **Mide** | Igualdad de varianzas | Igualdad de varianzas | Relación varianza-var. indep. | Relación varianza-var. indep. |
| **Requiere normalidad** | ❌ No | ✅ Sí | ⚠️ Parcialmente | ⚠️ Parcialmente |
| **Requiere regresión** | ❌ No | ❌ No | ✅ Sí | ✅ Sí |
| **Sensible a n** | Moderadamente | Muy sensible | Moderadamente | Moderadamente |
| **Potencia (n < 30)** | Moderada | Baja | Baja | Baja |
| **Potencia (n > 100)** | Alta | **Muy alta** | Alta | Alta |
| **Detecta qué** | Diferencias de varianza | Diferencias de varianza | Heterocedasticidad lineal | Heterocedasticidad en colas |
| **Da p-valor** | ✅ | ✅ | ✅ | ✅ |
| **Robustez** | **Alta** | Baja | Moderada | Moderada |
| **Velocidad** | Rápido | Rápido | Rápido | Rápido |
| **Librería** | scipy / pingouin | scipy | statsmodels | statsmodels |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → homocedasticidad | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar gráficamente | ⚠️ |
| p < 0.05 | Se rechaza H₀ → heterocedasticidad | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de heterocedasticidad | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa cada prueba?

- **Levene**: Calcula la distancia de cada observación a la media (o mediana) de su grupo, y luego compara esas distancias entre grupos con un F-test. No asume normalidad.
- **Bartlett**: Compara las varianzas muestrales usando una transformación logarítmica de las varianzas. Es el más potente PERO asume normalidad estricta.
- **Breusch-Pagan**: Regresa los residuos al cuadrado contra las variables independientes para ver si la varianza varía con los predictores.
- **Goldfeld-Quandt**: Ordena los datos por una variable independiente, divide en dos mitades, y compara las varianzas de las mitades.

---

## Matemáticas detrás de las pruebas

### Levene

#### Fórmula

$$W = \frac{(N - k)}{(k - 1)} \cdot \frac{\sum_{i=1}^{k} n_i (\bar{Z}_{i\cdot} - \bar{Z}_{\cdot\cdot})^2}{\sum_{i=1}^{k} \sum_{j=1}^{n_i} (Z_{ij} - \bar{Z}_{i\cdot})^2}$$

Donde:
- $Z_{ij} = |Y_{ij} - \bar{Y}_{i\cdot}|$ (distancia a la media del grupo)
- O $Z_{ij} = |Y_{ij} - \tilde{Y}_{i\cdot}|$ (distancia a la mediana — más robusto)
- $\bar{Z}_{i\cdot}$ = media de las distancias en el grupo i
- $\bar{Z}_{\cdot\cdot}$ = media global de las distancias
- $N$ = total de observaciones
- $k$ = número de grupos
- $n_i$ = observaciones en el grupo i

#### Proceso

1. Para cada observación, calcular la distancia a la media (o mediana) de su grupo
2. Calcular la media de esas distancias para cada grupo
3. Realizar un ANOVA sobre esas distancias
4. Si las medias de las distancias difieren → las varianzas difieren

#### Ejemplo manual simplificado

```python
# Grupo A: [2, 4, 6] → media = 4, distancias = [2, 0, 2]
# Grupo B: [1, 3, 5, 7] → media = 4, distancias = [3, 1, 1, 3]
# Grupo C: [10, 12, 14] → media = 12, distancias = [2, 0, 2]

# Medias de distancias: A=1.33, B=2.0, C=1.33
# Si B tiene distancias mucho mayores → varianza mayor en B
# Levene testea si estas diferencias son significativas
```

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
3. Usar la razón de verosimilitud para comparar varianzas
4. Aplicar un factor de corrección
5. Comparar con distribución chi-cuadrado

#### Interpretación

| Valor | Interpretación |
|---|---|
| χ² bajo | Las varianzas son similares |
| χ² alto | Las varianzas difieren significativamente |

### Breusch-Pagan

#### Fórmula

$$LM = n \cdot R^2_{\text{residuos}^2}$$

Donde:
- $n$ = número de observaciones
- $R^2$ = coeficiente de determinación de regresionar residuos² contra variables independientes

#### Proceso

1. Ajustar el modelo original y obtener residuos
2. Regresar residuos² contra las variables independientes
3. Calcular el R² de esa regresión
4. LM ~ χ²(k) donde k = número de predictores
5. Si R² es alto → la varianza depende de los predictores → heterocedasticidad

#### Ejemplo manual

```python
# Modelo: y = b0 + b1*x1 + b2*x2 + e
# Paso 1: Obtener residuos e
# Paso 2: Regresar e² ~ c0 + c1*x1 + c2*x2
# Paso 3: Si R² = 0.15 y n = 100 → LM = 15
# Paso 4: Comparar con χ²(2) → p-value
```

### Goldfeld-Quandt

#### Fórmula

$$F = \frac{RSS_2 / (n_2 - k)}{RSS_1 / (n_1 - k)}$$

Donde:
- $RSS_1$ = suma de cuadrados residual de la primera mitad
- $RSS_2$ = suma de cuadrados residual de la segunda mitad
- $n_1, n_2$ = tamaño de cada mitad
- $k$ = número de parámetros

#### Proceso

1. Ordenar los datos por una variable independiente
2. Dividir en dos mitades (eliminando un centro opcional)
3. Ajustar el mismo modelo a cada mitad
4. Calcular la razón F de las varianzas residuales
5. Si F es significativamente diferente de 1 → heterocedasticidad

#### Interpretación

| F | Interpretación |
|---|---|
| F ≈ 1 | Homocedasticidad |
| F >> 1 | Varianza crece con la variable |
| F << 1 | Varianza decrece con la variable |

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Comparar varianzas entre grupos**

> ¿La variable `ingresos` tiene la misma varianza en diferentes grupos de edad?

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from scipy import stats

datos = pd.read_csv('dataset.csv')

# Boxplot por grupo
plt.figure(figsize=(10, 5))
sns.boxplot(x='grupo_edad', y='ingresos', data=datos)
plt.title('Distribución de Ingresos por Grupo de Edad')
plt.xticks(rotation=45)
plt.show()

# Levene test
grupos = [datos[datos['grupo_edad'] == g]['ingresos'].dropna() 
          for g in datos['grupo_edad'].unique()]

stat, p = stats.levene(*grupos)
print(f"Levene: stat={stat:.4f}, p={p:.6f}")
if p >= 0.05:
    print("✅ Homocedasticidad (varianzas iguales)")
else:
    print("❌ Heterocedasticidad (varianzas diferentes)")
```

**Ejemplo 2: Explorar varianzas en múltiples variables**

> ¿Qué variables tienen varianzas iguales entre grupos?

```python
# Verificar homocedasticidad para todas las columnas numéricas
for column in datos.select_dtypes(include=[np.number]).columns[:5]:
    if 'grupo' in datos.columns:
        grupos = [datos[datos['grupo'] == g][column].dropna() 
                  for g in datos['grupo'].unique() if len(datos[datos['grupo'] == g]) > 1]
        
        if len(grupos) >= 2:
            stat, p = stats.levene(*grupos)
            estado = "✅ Homocedasticidad" if p >= 0.05 else "❌ Heterocedasticidad"
            print(f"{column}: stat={stat:.4f}, p={p:.6f} → {estado}")
```

**Ejemplo 3: Visualización con gráfico de residuos**

> ¿Los residuos muestran patrones de varianza?

```python
import statsmodels.api as sm

# Modelo simple
X = sm.add_constant(datos['edad'])
modelo = sm.OLS(datos['ingresos'], X).fit()
residuos = modelo.resid
valores_predichos = modelo.fittedvalues

# Gráfico de residuos vs predichos
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

axes[0].scatter(valores_predichos, residuos, alpha=0.5, s=20)
axes[0].axhline(y=0, color='red', linestyle='--')
axes[0].set_xlabel('Valores Predichos')
axes[0].set_ylabel('Residuos')
axes[0].set_title('Residuos vs Valores Predichos')

# Gráfico de escala-localización
residuos_estandarizados = residuos / residuos.std()
axes[1].scatter(valores_predichos, np.sqrt(np.abs(residuos_estandarizados)), alpha=0.5, s=20)
axes[1].set_xlabel('Valores Predichos')
axes[1].set_ylabel('√|Residuos Estandarizados|')
axes[1].set_title('Gráfico de Escala-Localización')

plt.tight_layout()
plt.show()
```

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 4: Verificar si la imputación preservó la varianza**

> ¿La imputación de valores faltantes cambió la varianza por grupo?

```python
from sklearn.impute import SimpleImputer

# Antes de imputar
print("ANTES de imputar:")
for g in datos['grupo'].unique():
    subset = datos[datos['grupo'] == g]['variable']
    print(f"  Grupo {g}: var={subset.var():.4f}, n={len(subset.dropna())}")

# Imputar con mediana
imputer = SimpleImputer(strategy='median')
datos['variable_imputada'] = imputer.fit_transform(datos[['variable']])

# Después de imputar
print("\nDESPUÉS de imputar:")
for g in datos['grupo'].unique():
    subset = datos[datos['grupo'] == g]['variable_imputada']
    print(f"  Grupo {g}: var={subset.var():.4f}, n={len(subset)}")

# Levene test
grupos_antes = [datos[datos['grupo'] == g]['variable'].dropna() 
                for g in datos['grupo'].unique()]
grupos_despues = [datos[datos['grupo'] == g]['variable_imputada'] 
                  for g in datos['grupo'].unique()]

stat_antes, p_antes = stats.levene(*grupos_antes)
stat_despues, p_despues = stats.levene(*grupos_despues)

print(f"\nLevene ANTES: stat={stat_antes:.4f}, p={p_antes:.6f}")
print(f"Levene DESPUÉS: stat={stat_despues:.4f}, p={p_despues:.6f}")

if abs(p_antes - p_despues) < 0.05:
    print("✅ La imputación preservó la homocedasticidad")
else:
    print("⚠️ La imputación afectó la homocedasticidad")
```

**Ejemplo 5: Decidir entre t-test y Welch's t-test**

> ¿Debo usar t-test clásico o Welch's t-test?

```python
from scipy.stats import ttest_ind

# Datos de dos grupos
grupo_a = np.random.normal(10, 2, 30)  # var = 4
grupo_b = np.random.normal(12, 3, 35)  # var = 9

# Verificar normalidad primero (PREREQUISITO para usar t-test)
W_a, p_a = stats.shapiro(grupo_a)
W_b, p_b = stats.shapiro(grupo_b)
print(f"Normalidad Grupo A: W={W_a:.4f}, p={p_a:.6f}")
print(f"Normalidad Grupo B: W={W_b:.4f}, p={p_b:.6f}")

# Si ambos son normales, verificar homocedasticidad
if p_a >= 0.05 and p_b >= 0.05:
    stat, p_levene = stats.levene(grupo_a, grupo_b)
    print(f"\nLevene: stat={stat:.4f}, p={p_levene:.6f}")
    
    if p_levene >= 0.05:
        # Homocedasticidad → t-test clásico
        t_stat, p_ttest = ttest_ind(grupo_a, grupo_b, equal_var=True)
        print(f"t-test clásico: t={t_stat:.4f}, p={p_ttest:.6f}")
    else:
        # Heterocedasticidad → Welch's t-test
        t_stat, p_welch = ttest_ind(grupo_a, grupo_b, equal_var=False)
        print(f"Welch's t-test: t={t_stat:.4f}, p={p_welch:.6f}")
else:
    print("⚠️ Datos no normales → usar Mann-Whitney U (no requiere homocedasticidad)")
```

---

### 3. En Feature Engineering

**Ejemplo 6: Evaluar homocedasticidad de features creadas**

> ¿La feature `ratio_ingreso_gasto` tiene varianza constante entre grupos?

```python
# Crear feature
datos['ratio'] = datos['ingreso'] / (datos['gasto'] + 1)

# Verificar homocedasticidad por grupo
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Boxplot por grupo
sns.boxplot(x='grupo', y='ratio', data=datos, ax=axes[0])
axes[0].set_title('Boxplot por Grupo')

# Gráfico de residuos
X = sm.add_constant(pd.get_dummies(datos['grupo'], drop_first=True))
modelo = sm.OLS(datos['ratio'], X).fit()
residuos = modelo.resid

axes[1].scatter(modelo.fittedvalues, residuos, alpha=0.5, s=20)
axes[1].axhline(y=0, color='red', linestyle='--')
axes[1].set_title('Residuos vs Predichos')

# Levene test
grupos = [datos[datos['grupo'] == g]['ratio'].dropna() 
          for g in datos['grupo'].unique()]
stat, p = stats.levene(*grupos)

axes[2].text(0.5, 0.5, f'Levene\nstat = {stat:.4f}\np = {p:.6f}', 
             ha='center', va='center', fontsize=12, transform=axes[2].transAxes)
axes[2].axis('off')
axes[2].set_title('Prueba Estadística')

plt.tight_layout()
plt.show()
```

**Ejemplo 7: Comparar varianzas de features entre train y test**

> ¿Las features tienen la misma varianza en train y test?

```python
from scipy.stats import levene

# Separar train y test
train = datos.sample(frac=0.8, random_state=42)
test = datos.drop(train.index)

# Levene test entre train y test para cada feature
for col in ['ingreso', 'gasto', 'ratio']:
    stat, p = levene(train[col].dropna(), test[col].dropna())
    estado = "✅ Homocedasticidad" if p >= 0.05 else "⚠️ Heterocedasticidad"
    print(f"{col}: stat={stat:.4f}, p={p:.6f} → {estado}")
```

---

### 4. En Selección de Modelos

**Ejemplo 8: Validar homocedasticidad de residuos en regresión**

> ¿Los residuos de mi modelo de regresión tienen varianza constante?

```python
from sklearn.linear_model import LinearRegression

# Entrenar modelo
X = datos[['ingreso', 'edad']]
y = datos['gasto']

modelo = LinearRegression()
modelo.fit(X, y)
residuos = y - modelo.predict(X)
predichos = modelo.predict(X)

# Verificar homocedasticidad
fig, axes = plt.subplots(2, 2, figsize=(12, 10))

# Residuos vs predichos
axes[0, 0].scatter(predichos, residuos, alpha=0.5, s=20)
axes[0, 0].axhline(y=0, color='red', linestyle='--')
axes[0, 0].set_xlabel('Valores Predichos')
axes[0, 0].set_ylabel('Residuos')
axes[0, 0].set_title('Residuos vs Predichos')

# Escala-localización
residuos_std = residuos / residuos.std()
axes[0, 1].scatter(predichos, np.sqrt(np.abs(residuos_std)), alpha=0.5, s=20)
axes[0, 1].set_xlabel('Valores Predichos')
axes[0, 1].set_ylabel('√|Residuos Estandarizados|')
axes[0, 1].set_title('Escala-Localización')

# Breusch-Pagan
from statsmodels.stats.diagnostic import het_breuschpagan
lm, p_lm, f_stat, p_f = het_breuschpagan(residuos, X)
axes[1, 0].text(0.1, 0.8, f'Breusch-Pagan:', transform=axes[1, 0].transAxes, fontsize=12, fontweight='bold')
axes[1, 0].text(0.1, 0.6, f'LM = {lm:.4f}', transform=axes[1, 0].transAxes)
axes[1, 0].text(0.1, 0.4, f'p = {p_lm:.6f}', transform=axes[1, 0].transAxes)
axes[1, 0].text(0.1, 0.2, f'Resultado: {"✅ Homocedasticidad" if p_lm >= 0.05 else "❌ Heterocedasticidad"}', 
                transform=axes[1, 0].transAxes)
axes[1, 0].axis('off')
axes[1, 0].set_title('Prueba Estadística')

# Goldfeld-Quandt
from statsmodels.stats.diagnostic import het_goldfeldquandt
f_gq, p_gq, ordering = het_goldfeldquandt(residuos, X)
axes[1, 1].text(0.1, 0.8, f'Goldfeld-Quandt:', transform=axes[1, 1].transAxes, fontsize=12, fontweight='bold')
axes[1, 1].text(0.1, 0.6, f'F = {f_gq:.4f}', transform=axes[1, 1].transAxes)
axes[1, 1].text(0.1, 0.4, f'p = {p_gq:.6f}', transform=axes[1, 1].transAxes)
axes[1, 1].text(0.1, 0.2, f'Orden: {ordering}', transform=axes[1, 1].transAxes)
axes[1, 1].axis('off')
axes[1, 1].set_title('Goldfeld-Quandt')

plt.tight_layout()
plt.show()

print(f"\nBreusch-Pagan: LM={lm:.4f}, p={p_lm:.6f}")
print(f"Goldfeld-Quandt: F={f_gq:.4f}, p={p_gq:.6f}")
```

**Ejemplo 9: Comparar homocedasticidad entre modelos**

> ¿Qué modelo tiene residuos con varianza más constante?

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.linear_model import LinearRegression
from statsmodels.stats.diagnostic import het_breuschpagan

modelos = {
    'Lineal': LinearRegression(),
    'Random Forest': RandomForestRegressor(n_estimators=100, random_state=42)
}

for nombre, modelo in modelos.items():
    modelo.fit(X, y)
    residuos = y - modelo.predict(X)
    
    # Breusch-Pagan
    lm, p_lm, _, _ = het_breuschpagan(residuos, X)
    
    print(f"{nombre}:")
    print(f"  Breusch-Pagan: LM={lm:.4f}, p={p_lm:.6f}")
    print(f"  → {'✅ Homocedasticidad' if p_lm >= 0.05 else '❌ Heterocedasticidad'}")
```

---

### 5. En Evaluación post-deploy

**Ejemplo 10: Verificar homocedasticidad en predicciones de producción**

> ¿Las predicciones del modelo tienen varianza constante en producción?

```python
# Simular predicciones de producción
predicciones_produccion = modelo.predict(X_nuevos)

# Verificar homocedasticidad por segmentos
segmentos = pd.qcut(predicciones_produccion, q=4, labels=['Q1', 'Q2', 'Q3', 'Q4'])
residuos_prod = y_nuevos - predicciones_produccion

# Levene test por segmentos
grupos = [residuos_prod[segmentos == s] for s in ['Q1', 'Q2', 'Q3', 'Q4']]
stat, p = stats.levene(*grupos)
print(f"Levene (producción): stat={stat:.4f}, p={p:.6f}")

# Comparar con entrenamiento
residuos_train = y - modelo.predict(X)
segmentos_train = pd.qcut(modelo.predict(X), q=4, labels=['Q1', 'Q2', 'Q3', 'Q4'])
grupos_train = [residuos_train[segmentos_train == s] for s in ['Q1', 'Q2', 'Q3', 'Q4']]
stat_train, p_train = stats.levene(*grupos_train)

print(f"Levene (entrenamiento): stat={stat_train:.4f}, p={p_train:.6f}")
print(f"Producción: {'✅ OK' if p >= 0.05 else '❌ Heterocedasticidad'}")
print(f"Entrenamiento: {'✅ OK' if p_train >= 0.05 else '❌ Heterocedasticidad'}")
```

**Ejemplo 11: Evaluar estabilidad de varianza en métricas**

> ¿La varianza de las métricas de evaluación se mantiene estable?

```python
# Simular métricas de diferentes períodos
metricas_enero = np.random.normal(0.85, 0.02, 30)
metricas_febrero = np.random.normal(0.83, 0.03, 28)
metricas_marzo = np.random.normal(0.84, 0.025, 31)

# Verificar homocedasticidad entre períodos
stat, p = stats.levene(metricas_enero, metricas_febrero, metricas_marzo)
print(f"Levene: stat={stat:.4f}, p={p:.6f}")

if p >= 0.05:
    print("✅ Las métricas tienen varianza estable entre períodos")
else:
    print("⚠️ La varianza de las métricas cambió entre períodos")
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 12: Detectar anomalías usando varianza**

> ¿Los valores actuales tienen la misma varianza que el histórico?

```python
# Datos históricos (últimos 30 días)
datos_historicos = np.random.normal(100, 15, 30 * 24)  # 30 días de horas

# Datos actuales (últimas 24 horas)
datos_actuales = np.random.normal(105, 20, 24)  # Varianza cambió

# Levene test
stat, p = stats.levene(datos_historicos, datos_actuales)
print(f"Levene: stat={stat:.4f}, p={p:.6f}")

if p < 0.05:
    print("⚠️ ALERTA: La varianza cambió significativamente")
    print(f"Varianza histórica: {np.var(datos_historicos):.4f}")
    print(f"Varianza actual: {np.var(datos_actuales):.4f}")
```

**Ejemplo 13: Monitoreo continuo de varianza con ventanas móviles**

> ¿La varianza cambia a lo largo del tiempo?

```python
import pandas as pd

# Simular datos diarios
np.random.seed(42)
fechas = pd.date_range('2024-01-01', periods=90, freq='D')
valores = np.concatenate([
    np.random.normal(100, 10, 30),    # Mes 1: varianza baja
    np.random.normal(100, 15, 30),    # Mes 2: varianza media
    np.random.normal(100, 20, 30)     # Mes 3: varianza alta
])

df = pd.DataFrame({'fecha': fechas, 'valor': valores})

# Ventana móvil de 7 días
resultados = []
for i in range(6, len(df)):
    ventana = df['valor'].iloc[i-6:i+1]
    ventana_anterior = df['valor'].iloc[max(0, i-13):i-5]
    
    if len(ventana_anterior) > 1:
        stat, p = stats.levene(ventana, ventana_anterior)
        resultados.append({
            'fecha': df['fecha'].iloc[i],
            'stat': stat,
            'p': p,
            'var_actual': ventana.var(),
            'var_anterior': ventana_anterior.var()
        })

resultados_df = pd.DataFrame(resultados)
print(resultados_df[resultados_df['p'] < 0.05].head(10))
```

---

### 7. En Validación de supuestos

**Ejemplo 14: Validar supuestos de ANOVA**

> ¿Los grupos tienen la misma varianza (prerequisito para ANOVA)?

```python
from scipy.stats import f_oneway

# Tres grupos con diferentes varianzas
grupo_A = np.random.normal(10, 2, 30)   # var = 4
grupo_B = np.random.normal(12, 2, 30)   # var = 4
grupo_C = np.random.normal(11, 3, 30)   # var = 9

# Verificar homocedasticidad ANTES de ANOVA
stat, p = stats.levene(grupo_A, grupo_B, grupo_C)
print(f"Levene: stat={stat:.4f}, p={p:.6f}")

if p >= 0.05:
    # Homocedasticidad → ANOVA clásico
    F, p_anova = f_oneway(grupo_A, grupo_B, grupo_C)
    print(f"ANOVA: F={F:.4f}, p={p_anova:.6f}")
else:
    # Heterocedasticidad → Welch's ANOVA o Kruskal-Wallis
    from scipy.stats import kruskal
    H, p_kw = kruskal(grupo_A, grupo_B, grupo_C)
    print(f"Kruskal-Wallis: H={H:.4f}, p={p_kw:.6f}")
```

**Ejemplo 15: Verificar homocedasticidad en modelos mixtos**

> ¿Los residuos de un modelo mixto tienen varianza constante?

```python
# Simular datos de panel
np.random.seed(42)
n_sujetos = 20
n_obs = 10

efecto_sujeto = np.random.normal(0, 2, n_sujetos)
residuos = np.random.normal(0, 1, n_sujetos * n_obs) + np.repeat(efecto_sujeto, n_obs)

# Dividir por sujetos para verificar homocedasticidad
grupos = [residuos[i*n_obs:(i+1)*n_obs] for i in range(n_sujetos)]

stat, p = stats.levene(*grupos)
print(f"Residuos modelo mixto: stat={stat:.4f}, p={p:.6f}")

if p >= 0.05:
    print("✅ Varianza constante entre sujetos")
else:
    print("⚠️ Varianza diferente entre sujetos → considerar pesos o transformaciones")
```

---

## CASOS ESPECIALES: Data Drift con Homocedasticidad

### ¿Por qué usar homocedasticidad para drift?

Las pruebas de homocedasticidad son útiles para detectar data drift porque:
1. **Detectan cambios en la dispersión** de los datos (no solo en la media)
2. **Capturan heterocedasticidad emergente** que puede indicar cambios en el proceso generador
3. **Son sensibles a cambios en la forma** de la distribución a través del tiempo
4. **Pueden automatizarse** fácilmente en pipelines de monitoreo de varianza

### Ejemplo 1: Monitoreo de varianza de features en producción

```python
import numpy as np
from scipy.stats import levene
import pandas as pd

def monitorar_varianza(datos_entrenamiento, datos_produccion, feature, alpha=0.05):
    """
    Detecta drift en la varianza usando Levene test.
    
    Returns: dict con resultados del drift detection
    """
    # Levene test
    stat, p = levene(datos_entrenamiento, datos_produccion)
    
    # Calcular ratio de varianzas
    var_train = np.var(datos_entrenamiento)
    var_prod = np.var(datos_produccion)
    ratio_var = var_prod / var_train if var_train > 0 else np.inf
    
    # Detectar drift
    drift_detectado = p < alpha
    
    return {
        'feature': feature,
        'stat_levene': stat,
        'p_value': p,
        'var_train': var_train,
        'var_prod': var_prod,
        'ratio_varianzas': ratio_var,
        'drift': drift_detectado
    }

# Ejemplo de uso
np.random.seed(42)
datos_train = np.random.normal(100, 15, 1000)
datos_prod = np.random.normal(100, 20, 500)  # Misma media, varianza diferente

resultado = monitorar_varianza(datos_train, datos_prod, 'ingreso')
print(f"Feature: {resultado['feature']}")
print(f"Var Train: {resultado['var_train']:.4f}, Var Prod: {resultado['var_prod']:.4f}")
print(f"Ratio: {resultado['ratio_varianzas']:.4f}")
print(f"Levene: stat={resultado['stat_levene']:.4f}, p={resultado['p_value']:.6f}")
print(f"Drift: {'⚠️ SÍ' if resultado['drift'] else '✅ NO'}")
```

### Ejemplo 2: Detección de drift en varianza entre ventanas de tiempo

```python
def comparar_varianzas_ventanas(ventana_anterior, ventana_actual):
    """
    Compara la varianza de dos ventanas de tiempo usando Levene test.
    """
    stat, p = levene(ventana_anterior, ventana_actual)
    
    var1 = np.var(ventana_anterior)
    var2 = np.var(ventana_actual)
    cambio_varianza = ((var2 - var1) / var1) * 100
    
    return {
        'stat_levene': stat,
        'p_value': p,
        'var_anterior': var1,
        'var_actual': var2,
        'cambio_porcentual': cambio_varianza,
        'drift': p < 0.05
    }

# Simular series de tiempo con cambio de varianza
np.random.seed(42)
datos = np.concatenate([
    np.random.normal(100, 10, 180),  # 6 meses con varianza baja
    np.random.normal(100, 18, 180)   # 6 meses con varianza alta
])

# Comparar ventanas de 30 días
semana1 = datos[0:30]
semana2 = datos[30:60]
semana_ultima = datos[-30:]
semana_penultima = datos[-60:-30]

print("Comparación temprana (mes 1 vs mes 2):")
r1 = comparar_varianzas_ventanas(semana1, semana2)
print(f"  Var1: {r1['var_anterior']:.4f}, Var2: {r1['var_actual']:.4f}")
print(f"  Cambio: {r1['cambio_porcentual']:.2f}%")
print(f"  Drift: {'⚠️ SÍ' if r1['drift'] else '✅ NO'}")

print("\nComparación tardía (mes 5 vs mes 6):")
r2 = comparar_varianzas_ventanas(semana_penultima, semana_ultima)
print(f"  Var1: {r2['var_anterior']:.4f}, Var2: {r2['var_actual']:.4f}")
print(f"  Cambio: {r2['cambio_porcentual']:.2f}%")
print(f"  Drift: {'⚠️ SÍ' if r2['drift'] else '✅ NO'}")
```

### Ejemplo 3: Sistema de alertas automáticas para drift de varianza

```python
import pandas as pd
import numpy as np
from scipy.stats import levene, shapiro
from datetime import datetime, timedelta

class VarianceDriftDetector:
    def __init__(self, umbral_alpha=0.05, umbral_ratio=1.5):
        self.umbral_alpha = umbral_alpha
        self.umbral_ratio = umbral_ratio
        self.historial = []
        self.alertas = []
    
    def registrar_datos(self, fecha, datos):
        """Registra datos de un período."""
        self.historial.append({
            'fecha': fecha,
            'datos': datos
        })
    
    def verificar_drift(self, ventana_dias=7):
        """
        Verifica drift en la varianza comparando los últimos N días con el histórico.
        """
        if len(self.historial) < 2:
            return None
        
        # Obtener ventana reciente
        ventana_reciente = np.concatenate([
            h['datos'] for h in self.historial[-ventana_dias:]
        ])
        
        # Obtener histórico (antes de la ventana reciente)
        historico = np.concatenate([
            h['datos'] for h in self.historial[:-ventana_dias]
        ])
        
        if len(historico) == 0 or len(ventana_reciente) == 0:
            return None
        
        # Levene test
        stat, p = levene(historico, ventana_reciente)
        
        # Calcular varianzas
        var_hist = np.var(historico)
        var_rec = np.var(ventana_reciente)
        ratio = var_rec / var_hist if var_hist > 0 else np.inf
        
        # Determinar alerta
        alerta = {
            'fecha': datetime.now(),
            'stat_levene': stat,
            'p_value': p,
            'var_historico': var_hist,
            'var_reciente': var_rec,
            'ratio_varianzas': ratio,
            'alerta_drift': p < self.umbral_alpha,
            'alerta_ratio': ratio > self.umbral_ratio or ratio < 1/self.umbral_ratio
        }
        
        if alerta['alerta_drift'] or alerta['alerta_ratio']:
            self.alertas.append(alerta)
        
        return alerta

# Ejemplo de uso
detector = VarianceDriftDetector()

# Simular 30 días de datos
np.random.seed(42)
for i in range(30):
    fecha = datetime.now() - timedelta(days=30-i)
    
    if i < 20:
        # Datos con varianza normal
        datos = np.random.normal(100, 15, 100)
    else:
        # Drift: cambio en varianza
        datos = np.random.normal(100, 25, 100)
    
    detector.registrar_datos(fecha, datos)

# Verificar drift
alerta = detector.verificar_drift(ventana_dias=7)
if alerta:
    print(f"Fecha: {alerta['fecha']}")
    print(f"Var histórico: {alerta['var_historico']:.4f}")
    print(f"Var reciente: {alerta['var_reciente']:.4f}")
    print(f"Ratio: {alerta['ratio_varianzas']:.4f}")
    print(f"Levene p-value: {alerta['p_value']:.6f}")
    print(f"Alerta drift: {'⚠️ SÍ' if alerta['alerta_drift'] else '✅ NO'}")
    print(f"Alerta ratio: {'⚠️ SÍ' if alerta['alerta_ratio'] else '✅ NO'}")
```

### Ejemplo 4: Monitoreo por feature con dashboard de varianza

```python
import pandas as pd
import numpy as np
from scipy.stats import levene
import matplotlib.pyplot as plt
import seaborn as sns

def dashboard_varianza(features_train, features_prod, feature_names):
    """
    Crea un dashboard de drift de varianza para múltiples features.
    """
    fig, axes = plt.subplots(len(feature_names), 3, figsize=(15, 4*len(feature_names)))
    
    resultados = []
    
    for i, feature in enumerate(feature_names):
        # Datos
        train = features_train[feature]
        prod = features_prod[feature]
        
        # Levene test
        stat, p = levene(train, prod)
        
        # Calcular varianzas
        var_train = np.var(train)
        var_prod = np.var(prod)
        
        # Boxplot superpuesto
        axes[i, 0].boxplot([train, prod], labels=['Train', 'Prod'])
        axes[i, 0].set_title(f'{feature} - Boxplot')
        axes[i, 0].set_ylabel('Valor')
        
        # Histograma de varianzas (simular ventanas)
        ventanas_train = np.array([np.var(train[j:j+50]) for j in range(0, len(train)-50, 50)])
        ventanas_prod = np.array([np.var(prod[j:j+50]) for j in range(0, len(prod)-50, 50)])
        
        axes[i, 1].hist(ventanas_train, alpha=0.5, label='Train', density=True, bins=15)
        axes[i, 1].hist(ventanas_prod, alpha=0.5, label='Prod', density=True, bins=15)
        axes[i, 1].set_title(f'{feature} - Distribución de Varianzas')
        axes[i, 1].legend()
        
        # Resultados
        drift = "⚠️ DRIFT" if p < 0.05 else "✅ OK"
        axes[i, 2].text(0.1, 0.8, f'Levene: stat={stat:.4f}', transform=axes[i, 2].transAxes)
        axes[i, 2].text(0.1, 0.65, f'p={p:.4f}', transform=axes[i, 2].transAxes)
        axes[i, 2].text(0.1, 0.5, f'Var Train: {var_train:.4f}', transform=axes[i, 2].transAxes)
        axes[i, 2].text(0.1, 0.35, f'Var Prod: {var_prod:.4f}', transform=axes[i, 2].transAxes)
        axes[i, 2].text(0.1, 0.2, f'Estado: {drift}', transform=axes[i, 2].transAxes)
        axes[i, 2].axis('off')
        axes[i, 2].set_title(f'{feature} - Resultados')
        
        resultados.append({
            'feature': feature,
            'stat_levene': stat,
            'p_value': p,
            'var_train': var_train,
            'var_prod': var_prod,
            'drift': p < 0.05
        })
    
    plt.tight_layout()
    plt.show()
    
    return pd.DataFrame(resultados)

# Ejemplo de uso
np.random.seed(42)
features_train = {
    'ingreso': np.random.normal(50000, 10000, 1000),
    'edad': np.random.normal(35, 10, 1000),
    'antiguedad': np.random.exponential(5, 1000)
}

features_prod = {
    'ingreso': np.random.normal(50000, 15000, 500),  # Varianza cambiada
    'edad': np.random.normal(35, 10, 500),
    'antiguedad': np.random.exponential(5, 500)
}

resultados = dashboard_varianza(features_train, features_prod, ['ingreso', 'edad', 'antiguedad'])
print(resultados[['feature', 'stat_levene', 'p_value', 'drift']])
```

### Ejemplo 5: Comparación de varianzas entre regiones/geografías

```python
def comparar_varianzas_geografias(datos_por_region, feature):
    """
    Compara varianzas de una feature entre diferentes regiones.
    """
    resultados = []
    regiones = list(datos_por_region.keys())
    
    for i in range(len(regiones)):
        for j in range(i+1, len(regiones)):
            region1 = regiones[i]
            region2 = regiones[j]
            
            # Levene test entre regiones
            stat, p = levene(datos_por_region[region1][feature], 
                            datos_por_region[region2][feature])
            
            var1 = np.var(datos_por_region[region1][feature])
            var2 = np.var(datos_por_region[region2][feature])
            
            resultados.append({
                'region1': region1,
                'region2': region2,
                'stat_levene': stat,
                'p_value': p,
                'var_region1': var1,
                'var_region2': var2,
                'diferente_varianza': p < 0.05
            })
    
    return pd.DataFrame(resultados)

# Ejemplo de uso
np.random.seed(42)
datos_por_region = {
    'Buenos Aires': np.random.normal(100, 15, 500),
    'Córdoba': np.random.normal(100, 12, 400),
    'Mendoza': np.random.normal(100, 20, 350)
}

resultados = comparar_varianzas_geografias(datos_por_region, 'ingreso')
print(resultados[['region1', 'region2', 'p_value', 'diferente_varianza']])
```

### Ejemplo 6: Detección de drift de varianza en tiempo real con streaming

```python
import numpy as np
from scipy.stats import levene
from collections import deque
from datetime import datetime

class RealTimeVarianceDriftDetector:
    def __init__(self, ventana_size=100, umbral_alpha=0.05):
        self.ventana_size = ventana_size
        self.umbral_alpha = umbral_alpha
        self.buffer = deque(maxlen=ventana_size)
        self.historico = []
        self.alertas = []
    
    def agregar_dato(self, valor):
        """Agrega un nuevo dato al buffer."""
        self.buffer.append(valor)
        
        if len(self.buffer) >= self.ventana_size:
            self._verificar_drift()
    
    def _verificar_drift(self):
        """Verifica drift cuando el buffer está lleno."""
        datos_actuales = list(self.buffer)
        
        if len(self.historico) == 0:
            self.historico = datos_actuales.copy()
            return
        
        # Levene test
        stat, p = levene(self.historico, datos_actuales)
        
        # Calcular varianzas
        var_hist = np.var(self.historico)
        var_act = np.var(datos_actuales)
        
        # Alerta
        if p < self.umbral_alpha:
            alerta = {
                'timestamp': datetime.now(),
                'stat_levene': stat,
                'p_value': p,
                'var_historico': var_hist,
                'var_actual': var_act
            }
            self.alertas.append(alerta)
            print(f"⚠️ DRIFT DE VARIANZA DETECTADO: Levene p={p:.6f}, Ratio={var_act/var_hist:.2f}")
        
        # Actualizar histórico
        self.historico = datos_actuales.copy()

# Ejemplo de uso
detector = RealTimeVarianceDriftDetector(ventana_size=50)

np.random.seed(42)
for i in range(200):
    if i < 100:
        valor = np.random.normal(100, 15)  # Varianza baja
    else:
        valor = np.random.normal(100, 25)  # Varianza alta
    
    detector.agregar_dato(valor)

print(f"\nTotal alertas: {len(detector.alertas)}")
```

### Ejemplo 7: Análisis de drift de varianza por percentiles

```python
def analisis_drift_varianza_percentiles(datos_train, datos_prod, percentiles=[25, 50, 75]):
    """
    Analiza drift en la varianza de diferentes percentiles de la distribución.
    """
    resultados = []
    
    for p in percentiles:
        # Calcular percentiles
        p_train = np.percentile(datos_train, p)
        p_prod = np.percentile(datos_prod, p)
        
        # Calcular varianza local (ventana alrededor del percentil)
        ventana = 50
        idx_train = np.searchsorted(np.sort(datos_train), p_train)
        idx_prod = np.searchsorted(np.sort(datos_prod), p_prod)
        
        var_train_local = np.var(datos_train[max(0, idx_train-ventana):idx_train+ventana])
        var_prod_local = np.var(datos_prod[max(0, idx_prod-ventana):idx_prod+ventana])
        
        resultados.append({
            'percentil': p,
            'valor_train': p_train,
            'valor_prod': p_prod,
            'var_train_local': var_train_local,
            'var_prod_local': var_prod_local
        })
    
    # Levene test general
    stat, p = levene(datos_train, datos_prod)
    
    return {
        'percentiles': pd.DataFrame(resultados),
        'stat_levene': stat,
        'p_value': p,
        'var_train': np.var(datos_train),
        'var_prod': np.var(datos_prod)
    }

# Ejemplo
np.random.seed(42)
train = np.random.lognormal(10, 1, 1000)
prod = np.random.lognormal(10, 1.5, 500)  # Varianza cambiada

resultado = analisis_drift_varianza_percentiles(train, prod)
print("Análisis por percentiles:")
print(resultado['percentiles'])
print(f"\nLevene: stat={resultado['stat_levene']:.4f}, p={resultado['p_value']:.6f}")
print(f"Var Train: {resultado['var_train']:.4f}, Var Prod: {resultado['var_prod']:.4f}")
```

### Ejemplo 8: Validación de homocedasticidad para pipelines de ML

```python
def validar_homocedasticidad_pipeline(X_train, X_test, feature_names, alpha=0.05):
    """
    Valida homocedasticidad en cada paso del pipeline de ML.
    """
    resultados = []
    
    for i, feature in enumerate(feature_names):
        # Verificar homocedasticidad entre train y test
        stat, p = levene(X_train[:, i], X_test[:, i])
        
        # Calcular varianzas
        var_train = np.var(X_train[:, i])
        var_test = np.var(X_test[:, i])
        
        # Determinar si necesita transformación
        necesita_transform = p < alpha
        
        resultados.append({
            'feature': feature,
            'stat_levene': stat,
            'p_value': p,
            'var_train': var_train,
            'var_test': var_test,
            'necesita_transform': necesita_transform
        })
    
    return pd.DataFrame(resultados)

# Ejemplo
from sklearn.datasets import make_regression

X, y = make_regression(n_samples=1000, n_features=5, noise=15, random_state=42)
feature_names = [f'feature_{i}' for i in range(5)]

# Split train/test
from sklearn.model_selection import train_test_split
X_train, X_test = train_test_split(X, test_size=0.2, random_state=42)

resultados = validar_homocedasticidad_pipeline(X_train, X_test, feature_names)
print(resultados[['feature', 'p_value', 'necesita_transform']])
```

### Ejemplo 9: Detección de drift de varianza con múltiples pruebas

```python
def deteccion_drift_varianza_multitest(datos_train, datos_prod, alpha=0.05):
    """
    Usa múltiples pruebas para detectar drift de varianza de forma más robusta.
    """
    from scipy.stats import levene, bartlett, fligner
    
    resultados = {}
    
    # Levene test (robusto)
    stat_l, p_l = levene(datos_train, datos_prod)
    resultados['levene'] = {'stat': stat_l, 'p': p_l}
    
    # Bartlett test (asume normalidad)
    stat_b, p_b = bartlett(datos_train, datos_prod)
    resultados['bartlett'] = {'stat': stat_b, 'p': p_b}
    
    # Fligner-Killeen test (robusto a no normalidad)
    stat_f, p_f = fligner(datos_train, datos_prod)
    resultados['fligner'] = {'stat': stat_f, 'p': p_f}
    
    # Calcular varianzas
    var_train = np.var(datos_train)
    var_prod = np.var(datos_prod)
    ratio = var_prod / var_train if var_train > 0 else np.inf
    
    # Decisión conjunta (voto mayoritario)
    votos_drift = sum([
        p_l < alpha,
        p_b < alpha,
        p_f < alpha
    ])
    
    resultados['decision'] = {
        'votos_drift': votos_drift,
        'drift_detectado': votos_drift >= 2,
        'var_train': var_train,
        'var_prod': var_prod,
        'ratio': ratio
    }
    
    return resultados

# Ejemplo
np.random.seed(42)
train = np.random.normal(100, 15, 1000)
prod = np.random.normal(100, 20, 500)

resultados = deteccion_drift_varianza_multitest(train, prod)
print(f"Levene: p={resultados['levene']['p']:.6f}")
print(f"Bartlett: p={resultados['bartlett']['p']:.6f}")
print(f"Fligner: p={resultados['fligner']['p']:.6f}")
print(f"Ratio varianzas: {resultados['decision']['ratio']:.4f}")
print(f"Decisión: {'⚠️ DRIFT' if resultados['decision']['drift_detectado'] else '✅ OK'}")
```

### Ejemplo 10: Sistema completo de monitoreo de drift de varianza

```python
import pandas as pd
import numpy as np
from scipy.stats import levene, bartlett
from datetime import datetime, timedelta
import json

class SistemaMonitoreoVarianza:
    def __init__(self, config=None):
        self.config = config or {
            'umbral_alpha': 0.05,
            'umbral_ratio': 1.5,
            'min_datos': 30
        }
        self.historial = {}
        self.alertas = []
        self.metricas = []
    
    def registrar_lote(self, fecha, datos, lote_id=None):
        """Registra un lote de datos."""
        if lote_id is None:
            lote_id = len(self.historial)
        
        self.historial[lote_id] = {
            'fecha': fecha,
            'datos': datos,
            'n': len(datos)
        }
        
        # Calcular métricas del lote
        metrica = {
            'lote_id': lote_id,
            'fecha': fecha,
            'n': len(datos),
            'media': np.mean(datos),
            'varianza': np.var(datos),
            'std': np.std(datos),
            'min': np.min(datos),
            'max': np.max(datos)
        }
        
        # Levene test (si hay datos previos)
        if len(self.historial) > 1:
            lote_previo = max(0, lote_id - 1)
            if lote_previo in self.historial:
                stat, p = levene(self.historial[lote_previo]['datos'], datos)
                metrica['levene_stat'] = stat
                metrica['levene_p'] = p
                metrica['homocedasticidad'] = p >= self.config['umbral_alpha']
        
        self.metricas.append(metrica)
        
        return metrica
    
    def verificar_drift(self, lote_actual_id, lote_referencia_id=None):
        """
        Verifica drift en la varianza entre un lote actual y uno de referencia.
        """
        if lote_referencia_id is None:
            # Usar el lote anterior como referencia
            lotes_ordenados = sorted(self.historial.keys())
            idx_actual = lotes_ordenados.index(lote_actual_id)
            if idx_actual == 0:
                return None
            lote_referencia_id = lotes_ordenados[idx_actual - 1]
        
        datos_ref = self.historial[lote_referencia_id]['datos']
        datos_act = self.historial[lote_actual_id]['datos']
        
        # Levene test
        stat, p = levene(datos_ref, datos_act)
        
        # Calcular varianzas
        var_ref = np.var(datos_ref)
        var_act = np.var(datos_act)
        ratio = var_act / var_ref if var_ref > 0 else np.inf
        
        # Determinar alerta
        drift_detectado = p < self.config['umbral_alpha']
        ratio_alerta = ratio > self.config['umbral_ratio'] or ratio < 1/self.config['umbral_ratio']
        
        resultado = {
            'lote_referencia': lote_referencia_id,
            'lote_actual': lote_actual_id,
            'stat_levene': stat,
            'p_value': p,
            'var_referencia': var_ref,
            'var_actual': var_act,
            'ratio_varianzas': ratio,
            'drift_detectado': drift_detectado,
            'ratio_alerta': ratio_alerta,
            'timestamp': datetime.now()
        }
        
        if drift_detectado or ratio_alerta:
            self.alertas.append(resultado)
            print(f"⚠️ ALERTA VARIANZA: Lote {lote_actual_id}")
        
        return resultado
    
    def generar_reporte(self):
        """Genera un reporte del estado del monitoreo."""
        df_metricas = pd.DataFrame(self.metricas)
        
        reporte = {
            'total_lotes': len(self.historial),
            'total_alertas': len(self.alertas),
            'lotes_homocedasticos': df_metricas['homocedasticidad'].sum() if 'homocedasticidad' in df_metricas.columns else 0,
            'metricas_promedio': {
                'media': df_metricas['media'].mean(),
                'varianza': df_metricas['varianza'].mean(),
                'std': df_metricas['std'].mean()
            }
        }
        
        return reporte

# Ejemplo de uso completo
sistema = SistemaMonitoreoVarianza()

# Simular 10 lotes de datos
np.random.seed(42)
for i in range(10):
    fecha = datetime.now() - timedelta(days=10-i)
    
    if i < 7:
        # Datos con varianza normal
        datos = np.random.normal(100, 15, 100)
    else:
        # Drift: cambio en varianza
        datos = np.random.normal(100, 25, 100)
    
    sistema.registrar_lote(fecha, datos)

# Verificar drift para cada lote
for i in range(1, 10):
    sistema.verificar_drift(i)

# Generar reporte
reporte = sistema.generar_reporte()
print("\nReporte del Sistema de Monitoreo de Varianza:")
print(f"Total lotes: {reporte['total_lotes']}")
print(f"Total alertas: {reporte['total_alertas']}")
print(f"Lotes homocedásticos: {reporte['lotes_homocedasticos']}")
```

---

## Interpretación de resultados

### Salida de scipy.stats.levene

```python
stat, p = levene(grupo_a, grupo_b)
# stat = 2.3456
# p = 0.1234
```

**Cómo leerla:**
- **stat**: estadístico de Levene (F de un ANOVA sobre las distancias a la media/mediana)
- **p**: valor p

### Salida de scipy.stats.bartlett

```python
stat, p = bartlett(grupo_a, grupo_b)
# stat = 3.4567
# p = 0.0456
```

**Cómo leerla:**
- **stat**: estadístico de Bartlett (chi-cuadrado transformado)
- **p**: valor p

### Salida de statsmodels.stats.diagnostic.het_breuschpagan

```python
lm, p_lm, f_stat, p_f = het_breuschpagan(residuos, X)
# lm = 4.5678
# p_lm = 0.0321
# f_stat = 2.3456
# p_f = 0.0432
```

**Cómo leerla:**
- **lm**: estadístico LM (n * R²)
- **p_lm**: p-valor del LM
- **f_stat**: estadístico F
- **p_f**: p-valor del F

### Reglas de decisión

```
p ≥ 0.05  → ✅ No se rechaza H₀ (homocedasticidad)
p < 0.05  → ❌ Se rechaza H₀ (heterocedasticidad)
p < 0.01  → ❌❌ Fuerte evidencia de heterocedasticidad
p < 0.001 → ❌❌❌ Evidencia muy fuerte de heterocedasticidad
```

### ¿Qué reportar?

```python
# Formato completo
print(f"Levene: stat = {stat:.4f}, p = {p:.6f}")
print(f"Breusch-Pagan: LM = {lm:.4f}, p = {p_lm:.6f}")

# Ejemplo:
# "Se realizó la prueba de Levene para verificar la homocedasticidad
# entre los grupos A y B. Se obtuvo stat = 2.3456, p = 0.1234,
# lo cual indica que no se rechaza la hipótesis de homocedasticidad
# al nivel de significancia del 5%."
```

### ¿Qué hacer si hay heterocedasticidad?

1. **No apresurarse**: muchos métodos son robustos a heterocedasticidad leve
2. **Usar Welch's t-test** en lugar de t-test clásico
3. **Usar Welch's ANOVA** en lugar de ANOVA clásico
4. **Transformar**: log, raíz cuadrada, Box-Cox
5. **Usar métodos no paramétricos**: Mann-Whitney, Kruskal-Wallis (NO requieren homocedasticidad)
6. **Usar errores estándar robustos**: White, HC0-HC3 en regresión
7. **Reportar**: documentar la heterocedasticidad y su impacto

---

## Errores comunes

### 1. Usar Levene para elegir entre paramétrico y no paramétrico cuando los datos NO son normales

```python
# MAL: "Mis datos no son normales, uso Levene para ver si necesito no paramétrico"
if p_shapiro < 0.05:  # Datos no normales
    stat, p_levene = levene(grupo_a, grupo_b)
    if p_levene < 0.05:
        print("Usar Mann-Whitney")  # ❌ ERROR: Mann-Whitney NO requiere homocedasticidad

# BIEN: "Los datos no son normales →直接 usar Mann-Whitney (no necesito Levene)"
if p_shapiro < 0.05:  # Datos no normales
    stat_mw, p_mw = mannwhitneyu(grupo_a, grupo_b)
    print(f"Mann-Whitney: stat={stat_mw:.4f}, p={p_mw:.6f}")
```

> **⚠️ INSIGHT CLAVE**: Las pruebas no paramétricas (Mann-Whitney, Kruskal-Wallis) NO requieren homocedasticidad. Solo necesitás verificar homocedasticidad cuando vas a usar pruebas paramétricas (t-test, ANOVA).

### 2. Olvidar verificar normalidad antes de usar Bartlett

```python
# MAL: Usar Bartlett sin verificar normalidad
stat, p = bartlett(grupo_a, grupo_b)  # ❌ Si los datos no son normales, el resultado es inválido

# BIEN: Verificar normalidad primero
W_a, p_a = shapiro(grupo_a)
W_b, p_b = shapiro(grupo_b)

if p_a >= 0.05 and p_b >= 0.05:
    # Datos normales → Bartlett es válido
    stat, p = bartlett(grupo_a, grupo_b)
    print(f"Bartlett: stat={stat:.4f}, p={p:.6f}")
else:
    # Datos no normales → usar Levene
    stat, p = levene(grupo_a, grupo_b)
    print(f"Levene: stat={stat:.4f}, p={p:.6f}")
```

### 3. Ignorar el tamaño de muestra

```python
# MAL: confiar en Levene con n muy pequeño
grupo_a = np.random.normal(10, 2, 5)
grupo_b = np.random.normal(12, 3, 6)
stat, p = levene(grupo_a, grupo_b)  # ❌ Muy poca potencia

# BIEN: verificar que hay suficientes datos
if len(grupo_a) >= 20 and len(grupo_b) >= 20:
    stat, p = levene(grupo_a, grupo_b)
    print(f"Levene: stat={stat:.4f}, p={p:.6f}")
else:
    print("⚠️ Muy poca muestra para Levene (necesario n >= 20 por grupo)")
```

### 4. No combinar métodos gráficos y estadísticos

```python
# MAL: solo mirar el p-valor
stat, p = levene(grupo_a, grupo_b)
print(f"p = {p:.6f}")  # ❌ No te dice el TIPO de heterocedasticidad

# BIEN: combinar métodos
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Boxplot
axes[0].boxplot([grupo_a, grupo_b], labels=['A', 'B'])
axes[0].set_title('Boxplot')

# Residuos
# (si es regresión)
axes[1].scatter(predichos, residuos, alpha=0.5)
axes[1].axhline(y=0, color='red', linestyle='--')
axes[1].set_title('Residuos vs Predichos')

# Levene
stat, p = levene(grupo_a, grupo_b)
axes[2].text(0.5, 0.5, f'Levene\nstat={stat:.4f}\np={p:.6f}', 
             ha='center', va='center', transform=axes[2].transAxes)
axes[2].axis('off')
axes[2].set_title('Prueba Estadística')

plt.tight_layout()
plt.show()
```

### 5. Usar Goldfeld-Quandt sin ordenar los datos correctamente

```python
# MAL: Goldfeld-Quandt sin especificar la variable de ordenamiento
f_stat, p, _ = het_goldfeldquandt(residuos, X)  # ❌ Puede ordenar mal

# BIEN: especificar la variable de ordenamiento
f_stat, p, ordering = het_goldfeldquandt(residuos, X, idx=0, drop=0.2)
print(f"Goldfeld-Quandt: F={f_stat:.4f}, p={p:.6f}, ordering={ordering}")
```

### 6. Confundir p-valor alto con "las varianzas son iguales"

```python
# MAL: "p = 0.9, ¡excelente homocedasticidad!"
# BIEN: "p = 0.9 indica que no hay evidencia para rechazar la homocedasticidad,
#        pero esto no confirma que las varianzas sean exactamente iguales"
```

### 7. No reportar el método usado

```python
# MAL: "las varianzas son iguales"
# BIEN: "según la prueba de Levene (stat = 2.346, p = 0.123),
#        no se rechaza la hipótesis de homocedasticidad al nivel del 5%"
```

### 8. Usar Breusch-Pagan sin verificar que el modelo es lineal

```python
# MAL: Breusch-Pagan en un modelo no lineal
from sklearn.ensemble import RandomForestRegressor
modelo = RandomForestRegressor()
modelo.fit(X, y)
residuos = y - modelo.predict(X)
lm, p, _, _ = het_breuschpagan(residuos, X)  # ❌ No es válido para modelos no lineales

# BIEN: solo usar en modelos lineales
from sklearn.linear_model import LinearRegression
modelo = LinearRegression()
modelo.fit(X, y)
residuos = y - modelo.predict(X)
lm, p, _, _ = het_breuschpagan(residuos, X)  # ✅ Válido
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy import stats
from scipy.stats import levene, bartlett, shapiro
import statsmodels.api as sm
from statsmodels.stats.diagnostic import het_breuschpagan, het_goldfeldquandt
import seaborn as sns
import matplotlib.pyplot as plt

# ============================================
# FLUJO COMPLETO: Análisis de homocedasticidad
# ============================================

# 1. Cargar datos
datos = pd.read_csv('tu_dataset.csv')

# 2. Seleccionar variable y grupo
variable = 'tu_variable'
grupo_col = 'tu_grupo'

# 3. Exploración visual
fig, axes = plt.subplots(2, 2, figsize=(12, 10))

# Boxplot por grupo
sns.boxplot(x=grupo_col, y=variable, data=datos, ax=axes[0, 0])
axes[0, 0].set_title('Boxplot por Grupo')

# Violin plot
sns.violinplot(x=grupo_col, y=variable, data=datos, ax=axes[0, 1])
axes[0, 1].set_title('Violin Plot por Grupo')

# Histograma por grupo
for g in datos[grupo_col].unique():
    subset = datos[datos[grupo_col] == g][variable].dropna()
    axes[1, 0].hist(subset, alpha=0.5, label=f'Grupo {g}', density=True, bins=20)
axes[1, 0].set_title('Histograma por Grupo')
axes[1, 0].legend()

# Gráfico de densidad
for g in datos[grupo_col].unique():
    subset = datos[datos[grupo_col] == g][variable].dropna()
    subset.plot.kde(ax=axes[1, 1], label=f'Grupo {g}')
axes[1, 1].set_title('Densidad por Grupo')
axes[1, 1].legend()

plt.tight_layout()
plt.show()

# 4. Verificar normalidad (PREREQUISITO para elegir entre Levene y Bartlett)
print("=" * 50)
print("VERIFICACIÓN DE NORMALIDAD")
print("=" * 50)

es_normal = True
for g in datos[grupo_col].unique():
    subset = datos[datos[grupo_col] == g][variable].dropna()
    W, p = shapiro(subset[:5000])  # Shapiro limita a 5000
    print(f"Grupo {g}: W={W:.4f}, p={p:.6f} → {'✅ Normal' if p >= 0.05 else '❌ No normal'}")
    if p < 0.05:
        es_normal = False

# 5. Pruebas de homocedasticidad
print("\n" + "=" * 50)
print("PRUEBAS DE HOMOCEDASTICIDAD")
print("=" * 50)

# Preparar grupos
grupos = [datos[datos[grupo_col] == g][variable].dropna() 
          for g in datos[grupo_col].unique() if len(datos[datos[grupo_col] == g]) > 1]

# Levene (siempre válido)
stat, p = levene(*grupos)
print(f"\nLevene:")
print(f"  stat = {stat:.4f}")
print(f"  p = {p:.6f}")
print(f"  Resultado: {'✅ Homocedasticidad' if p >= 0.05 else '❌ Heterocedasticidad'}")

# Bartlett (solo si datos son normales)
if es_normal:
    stat_b, p_b = bartlett(*grupos)
    print(f"\nBartlett:")
    print(f"  stat = {stat_b:.4f}")
    print(f"  p = {p_b:.6f}")
    print(f"  Resultado: {'✅ Homocedasticidad' if p_b >= 0.05 else '❌ Heterocedasticidad'}")
else:
    print("\nBartlett: No aplica (datos no son normales)")

# 6. Para regresión: Breusch-Pagan y Goldfeld-Quandt
if 'variable_independiente' in datos.columns:
    print("\n" + "=" * 50)
    print("ANÁLISIS DE REGRESIÓN")
    print("=" * 50)
    
    X = sm.add_constant(datos[['variable_independiente']])
    y = datos[variable]
    
    modelo = sm.OLS(y, X).fit()
    residuos = modelo.resid
    
    # Breusch-Pagan
    lm, p_lm, f_stat, p_f = het_breuschpagan(residuos, X)
    print(f"\nBreusch-Pagan:")
    print(f"  LM = {lm:.4f}")
    print(f"  p = {p_lm:.6f}")
    print(f"  Resultado: {'✅ Homocedasticidad' if p_lm >= 0.05 else '❌ Heterocedasticidad'}")
    
    # Goldfeld-Quandt
    f_gq, p_gq, ordering = het_goldfeldquandt(residuos, X)
    print(f"\nGoldfeld-Quandt:")
    print(f"  F = {f_gq:.4f}")
    print(f"  p = {p_gq:.6f}")
    print(f"  Orden: {ordering}")
    print(f"  Resultado: {'✅ Homocedasticidad' if p_gq >= 0.05 else '❌ Heterocedasticidad'}")
    
    # Gráfico de residuos
    fig, axes = plt.subplots(1, 3, figsize=(15, 4))
    
    axes[0].scatter(modelo.fittedvalues, residuos, alpha=0.5, s=20)
    axes[0].axhline(y=0, color='red', linestyle='--')
    axes[0].set_xlabel('Valores Predichos')
    axes[0].set_ylabel('Residuos')
    axes[0].set_title('Residuos vs Predichos')
    
    residuos_std = residuos / residuos.std()
    axes[1].scatter(modelo.fittedvalues, np.sqrt(np.abs(residuos_std)), alpha=0.5, s=20)
    axes[1].set_xlabel('Valores Predichos')
    axes[1].set_ylabel('√|Residuos Estandarizados|')
    axes[1].set_title('Escala-Localización')
    
    sm.qqplot(residuos, line='45', alpha=0.5, lw=1, ax=axes[2])
    axes[2].set_title('Q-Q Plot: Residuos')
    
    plt.tight_layout()
    plt.show()

# 7. Resumen
print("\n" + "=" * 50)
print("RESUMEN")
print("=" * 50)
print(f"Variable: {variable}")
print(f"Grupo: {grupo_col}")
print(f"n total = {len(datos)}")
print(f"n por grupo: {', '.join([f'{g}: {len(g_)}' for g, g_ in zip(datos[grupo_col].unique(), grupos)])}")
print(f"Normalidad: {'✅ Sí' if es_normal else '❌ No'}")
print(f"Levene p = {p:.6f} → {'✅ Homocedasticidad' if p >= 0.05 else '❌ Heterocedasticidad'}")

# 8. Interpretación
if p >= 0.05:
    print("\n✅ Las varianzas son similares entre grupos")
    if es_normal:
        print("   → Podés usar t-test clásico o ANOVA clásico")
    else:
        print("   → Datos no normales: usar Mann-Whitney o Kruskal-Wallis")
else:
    print("\n❌ Las varianzas son diferentes entre grupos")
    print("   → Usar Welch's t-test o Welch's ANOVA")
    print("   → O transformar los datos")
```

---

## Resumen: cuándo usar cada prueba

```
¿Qué querés hacer?
│
├── EDA rápido / exploración
│   └── Boxplot por grupo + violin plot
│
├── Comparar varianzas entre grupos
│   │
│   ├── Datos normales
│   │   └── Bartlett (más potente) + Levene (verificación)
│   │
│   └── Datos no normales
│       └── Solo Levene (no asume normalidad)
│
├── Validar regresión lineal
│   │
│   ├── Breusch-Pagan (heterocedasticidad lineal)
│   └── Goldfeld-Quandt (heterocedasticidad en colas)
│
├── Elegir entre t-test y Welch
│   │
│   ├── Primero: verificar normalidad (Shapiro-Wilk)
│   │
│   ├── Si son normales: Levene
│   │   ├── p >= 0.05 → t-test clásico
│   │   └── p < 0.05 → Welch's t-test
│   │
│   └── Si NO son normales →直接 usar Mann-Whitney
│       (NO necesitás Levene)
│
├── Elegir entre ANOVA y Welch's ANOVA
│   │
│   ├── Primero: verificar normalidad
│   │
│   ├── Si son normales: Levene
│   │   ├── p >= 0.05 → ANOVA clásico
│   │   └── p < 0.05 → Welch's ANOVA
│   │
│   └── Si NO son normales →直接 usar Kruskal-Wallis
│       (NO necesitás Levene)
│
├── Detectar drift de varianza en producción
│   └── Levene + comparación de ventanas de tiempo
│
├── Monitoreo continuo de varianza
│   └── Levene + ventanas móviles + alertas
│
└── No sabés cuál usar
    └── Levene + boxplot (combinados)
```

---

## Código rápido de referencia

```python
from scipy.stats import levene, bartlett, shapiro
from statsmodels.stats.diagnostic import het_breuschpagan, het_goldfeldquandt
import statsmodels.api as sm
import seaborn as sns
import matplotlib.pyplot as plt

# ============================================
# LEVENE (recomendado, siempre válido)
# ============================================

stat, p = levene(grupo_a, grupo_b)
print(f"Levene: stat = {stat:.4f}, p = {p:.6f}")

# Con mediana (más robusto)
stat, p = levene(grupo_a, grupo_b, center='median')
print(f"Levene (mediana): stat = {stat:.4f}, p = {p:.6f}")

# ============================================
# BARTLETT (solo si datos son normales)
# ============================================

stat, p = bartlett(grupo_a, grupo_b)
print(f"Bartlett: stat = {stat:.4f}, p = {p:.6f}")

# ============================================
# BREUSCH-PAGAN (para regresión)
# ============================================

X = sm.add_constant(datos[['x1', 'x2']])
y = datos['y']
modelo = sm.OLS(y, X).fit()
residuos = modelo.resid

lm, p_lm, f_stat, p_f = het_breuschpagan(residuos, X)
print(f"Breusch-Pagan: LM = {lm:.4f}, p = {p_lm:.6f}")

# ============================================
# GOLDFELD-QUANDT (para regresión)
# ============================================

f_stat, p, ordering = het_goldfeldquandt(residuos, X, idx=0, drop=0.2)
print(f"Goldfeld-Quandt: F = {f_stat:.4f}, p = {p:.6f}")

# ============================================
# BOXPLOT POR GRUPO
# ============================================

plt.figure(figsize=(8, 5))
sns.boxplot(x='grupo', y='variable', data=datos)
plt.title('Boxplot por Grupo')
plt.show()

# ============================================
# VIOLIN PLOT POR GRUPO
# ============================================

plt.figure(figsize=(8, 5))
sns.violinplot(x='grupo', y='variable', data=datos)
plt.title('Violin Plot por Grupo')
plt.show()

# ============================================
# GRÁFICO DE RESIDUOS (para regresión)
# ============================================

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Residuos vs predichos
axes[0].scatter(modelo.fittedvalues, residuos, alpha=0.5, s=20)
axes[0].axhline(y=0, color='red', linestyle='--')
axes[0].set_title('Residuos vs Predichos')

# Escala-localización
residuos_std = residuos / residuos.std()
axes[1].scatter(modelo.fittedvalues, np.sqrt(np.abs(residuos_std)), alpha=0.5, s=20)
axes[1].set_title('Escala-Localización')

# Q-Q plot
sm.qqplot(residuos, line='45', alpha=0.5, lw=1, ax=axes[2])
axes[2].set_title('Q-Q Plot: Residuos')

plt.tight_layout()
plt.show()

# ============================================
# COMBINAR MÉTODOS (recomendado)
# ============================================

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Boxplot
sns.boxplot(x='grupo', y='variable', data=datos, ax=axes[0])
axes[0].set_title('Boxplot por Grupo')

# Violin
sns.violinplot(x='grupo', y='variable', data=datos, ax=axes[1])
axes[1].set_title('Violin Plot')

# Levene
stat, p = levene(*grupos)
axes[2].text(0.5, 0.5, f'Levene\nstat = {stat:.4f}\np = {p:.6f}', 
             ha='center', va='center', fontsize=14, transform=axes[2].transAxes)
axes[2].axis('off')
axes[2].set_title('Prueba Estadística')

plt.tight_layout()
plt.show()
```

---

## Checklist de análisis

| Paso | Acción | Herramienta |
|------|--------|-------------|
| 1 | ¿Qué variable y grupos quiero comparar? | Selección |
| 2 | ¿Cuántos datos tengo por grupo? | `len(grupo)` |
| 3 | Visualizar la distribución por grupo | Boxplot + violin plot |
| 4 | Verificar normalidad por grupo | Shapiro-Wilk |
| 5 | ¿Los datos son normales? | Interpretar Shapiro-Wilk |
| 6 | Ejecutar Levene (siempre válido) | `scipy.stats.levene` |
| 7 | Ejecutar Bartlett (solo si normales) | `scipy.stats.bartlett` |
| 8 | Para regresión: Breusch-Pagan | `statsmodels.stats.diagnostic.het_breuschpagan` |
| 9 | Para regresión: Goldfeld-Quandt | `statsmodels.stats.diagnostic.het_goldfeldquandt` |
| 10 | Interpretar p-valores | p ≥ 0.05 → homocedasticidad |
| 11 | Combinar métodos gráficos + estadísticos | Juicio experto |
| 12 | Reportar: método, valor, p, n, conclusión | Formato completo |

---

## Referencias

- Levene, H. (1960). Robust tests for equality of variances. *Contributions to Probability and Statistics*, 278-292.
- Bartlett, M. S. (1937). Properties of sufficiency and statistical tests. *Proceedings of the Royal Society of London. Series A*, 160(901), 268-282.
- Breusch, T. S., & Pagan, A. R. (1979). A simple test for heteroscedasticity and random coefficient variation. *Econometrica*, 47(5), 1287-1294.
- Goldfeld, S. M., & Quandt, R. E. (1965). Some tests for homoscedasticity. *Journal of the American Statistical Association*, 60(310), 539-547.
