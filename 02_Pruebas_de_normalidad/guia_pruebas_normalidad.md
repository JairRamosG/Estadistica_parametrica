# Guía de Referencia: Pruebas de Normalidad

## ¿Qué es?

Las pruebas de normalidad son herramientas **estadísticas y gráficas** que determinan si una distribución de datos se ajusta a una distribución normal (gaussiana). Incluyen tanto métodos visuales (histogramas, gráficos Q-Q) como pruebas formales (Kolmogorov-Smirnov, Shapiro-Wilk).

**Pregunta central:** ¿Mis datos provienen de una distribución normal?

**Qué NO hacen:**
- No confirman que los datos **son** normales (solo no rechazan la hipótesis de normalidad)
- No son robustas ante tamaños de muestra muy grandes (detectan desviaciones mínimas)
- No reemplazan el juicio experto ni la inspección visual
- **No prueban causalidad** — solo describen la forma de la distribución

---

## Cuándo usarla

### Flujo de decisión

```
¿Necesitás verificar normalidad?
│
├── ¿Por qué la necesitás?
│   │
│   ├── Para elegir entre pruebas paramétricas y no paramétricas
│   │   └── Verificar normalidad ANTES de elegir el test
│   │
│   ├── Para validación de supuestos de un modelo
│   │   └── Verificar residuos, errores, etc.
│   │
│   ├── Para detectar cambios en producción (data drift)
│   │   └── Shapiro-Wilk como detector de drift
│   │
│   └── Para EDA / exploración
│       └── Histogramas + Q-Q plots son suficientes
│
├── ¿Cuántos datos tenés?
│   │
│   ├── n < 50   → Shapiro-Wilk (más sensible)
│   ├── 50 < n < 5000 → Ambos (KS + Shapiro-Wilk)
│   └── n > 5000 → Solo inspección visual (KS y Shapiro son demasiado sensibles)
│
└── ¿Querés solo una idea visual?
    └── Histograma + Q-Q plot (rápido y efectivo)
```

### Regla práctica

| Situación | Herramienta |
|---|---|
| EDA rápido, ver la forma | Histograma + KDE |
| Verificar normalidad formalmente | Shapiro-Wilk (n < 5000) |
| Comparar contra una distribución teórica | Kolmogorov-Smirnov |
| Datos muy grandes (n > 5000) | Solo Q-Q plot + histograma |
| Detectar drift en producción | Shapiro-Wilk + comparación de distribuciones |
| Validar residuos de modelos | Q-Q plot + Shapiro-Wilk |
| No sabés cuál usar | Shapiro-Wilk + Q-Q plot (combinados) |

### ¿Por qué combinar métodos gráficos y estadísticos?

- **Los gráficos muestran LA FORMA** de la desviación (¿es sesgo? ¿colas pesadas? ¿bimodal?)
- **Las pruebas estadísticas dan un NÚMERO** (p-valor) para tomar una decisión
- **Ninguno solo es suficiente**: un histograma puede engañar, y un p-valor no te dice POR QUÉ no es normal
- **El método sugerido es combinar ambos**: mirá el gráfico, después confirmá con la prueba

---

## Comparación entre pruebas

### Comparación directa

| Aspecto | Histograma | Q-Q Plot | Kolmogorov-Smirnov | Shapiro-Wilk |
|---|---|---|---|---|
| **Tipo** | Visual | Visual | Estadística formal | Estadística formal |
| **Mide** | Forma de la distribución | Ajuste a normal teórica | Máxima distancia CDF | Correlación con cuantiles normales |
| **Requiere** | Solo datos | Solo datos | Datos + distribución teórica | Solo datos |
| **Sensible a n** | No | No | Muy sensible (n grandes) | Moderadamente sensible |
| **Detecta qué** | Cualquier desviación | Cualquier desviación | Cualquier desviación | Cualquier desviación |
| **Da p-valor** | ❌ | ❌ | ✅ | ✅ |
| **Intervalos de confianza** | ❌ | ✅ (pingouin) | ❌ | ❌ |
| **Potencia (n < 500)** | N/A | N/A | Baja | **Alta** |
| **Potencia (n > 5000)** | N/A | N/A | Alta (demasiado) | Alta (demasiado) |
| **Velocidad** | Rápido | Rápido | Rápido | Rápido |
| **Librería** | seaborn | statsmodels / pingouin | scipy | scipy |

### Interpretación del p-valor

| p-valor | Decisión | Emoji |
|---|---|---|
| p ≥ 0.10 | No se rechaza H₀ → posible normalidad | ✅ |
| 0.05 ≤ p < 0.10 | Zona gris → revisar gráficamente | ⚠️ |
| p < 0.05 | Se rechaza H₀ → NO es normal | ❌ |
| p < 0.01 | Se rechaza H₀ → fuerte evidencia de no normalidad | ❌❌ |
| p < 0.001 | Se rechaza H₀ → evidencia muy fuerte | ❌❌❌ |

### ¿Qué significa cada prueba?

- **Kolmogorov-Smirnov**: Compara la función de distribución empírica (CDF) de tus datos con la CDF teórica de la normal. El estadístico D mide la **máxima distancia** entre ambas curvas.
- **Shapiro-Wilk**: Mide la **correlación** entre tus datos ordenados y los cuantiles teóricos de una normal. El estadístico W varía entre 0 y 1 (más cercano a 1 = más normal).

---

## Matemáticas detrás de las pruebas

### Kolmogorov-Smirnov

#### Fórmula

$$D = \max_x |F_n(x) - F(x)|$$

Donde:
- $F_n(x)$ = función de distribución empírica (CDF de tus datos)
- $F(x)$ = función de distribución teórica (CDF de la normal)
- $D$ = máxima distancia vertical entre ambas curvas

#### Proceso

1. Ordenar los datos de menor a mayor
2. Construir la CDF empírica (escalón)
3. Calcular la CDF teórica de la normal para cada punto
4. Encontrar la máxima diferencia absoluta entre ambas
5. Comparar D con el valor crítico de la distribución K-S

#### Ejemplo manual simplificado

```python
# Datos: [1.2, 1.5, 1.8, 2.1, 2.5]
# CDF empírica en cada punto: [0.2, 0.4, 0.6, 0.8, 1.0]
# CDF normal teórica:         [0.12, 0.21, 0.36, 0.58, 0.79]
# Diferencias:                [0.08, 0.19, 0.24, 0.22, 0.21]
# D = 0.24 (máxima diferencia)
```

### Shapiro-Wilk

#### Fórmula

$$W = \frac{(\sum_{i=1}^{n} a_i x_{(i)})^2}{\sum_{i=1}^{n} (x_i - \bar{x})^2}$$

Donde:
- $x_{(i)}$ = datos ordenados
- $a_i$ = coeficientes generados a partir de los cuantiles de una normal
- $\bar{x}$ = media de los datos

#### Proceso

1. Ordenar los datos de menor a mayor
2. Calcular los coeficientes $a_i$ (dependen del tamaño de muestra)
3. Calcular el numerador con los datos ordenados y los coeficientes
4. Calcular el denominador (varianza total)
5. W cercano a 1 → los datos se parecen a una normal

#### Interpretación de W

| Valor de W | Interpretación |
|---|---|
| W > 0.97 | Fuerte evidencia de normalidad |
| 0.95 < W < 0.97 | Normalidad aceptable |
| 0.90 < W < 0.95 | Posible desviación de normalidad |
| W < 0.90 | Fuerte evidencia de no normalidad |

---

## Ejemplos por fase del proyecto de datos

### 1. En EDA (Exploratory Data Analysis)

**Ejemplo 1: Verificar distribución de una variable continua**

> ¿La variable `ingresos` tiene distribución normal?

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from scipy import stats

datos = pd.read_csv('dataset.csv')

# Histograma con KDE
plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
sns.histplot(datos['ingresos'], kde=True, bins=30, color='lightblue')
plt.title('Distribución de Ingresos')

# Shapiro-Wilk
W, p = stats.shapiro(datos['ingresos'])
plt.subplot(1, 2, 2)
plt.text(0.5, 0.5, f'Shapiro-Wilk\nW = {W:.4f}\np = {p:.6f}', 
         ha='center', va='center', fontsize=14, transform=plt.gca().transAxes)
plt.axis('off')
plt.title('Prueba de Normalidad')

plt.tight_layout()
plt.show()

print(f"W = {W:.4f}, p = {p:.6f}")
if p >= 0.05:
    print("✅ No se rechaza normalidad (p >= 0.05)")
else:
    print("❌ Se rechaza normalidad (p < 0.05)")
```

**Ejemplo 2: Comparar múltiples distribuciones**

> ¿Qué variables de mi dataset son normales?

```python
from scipy.stats import kstest, shapiro

# Verificar normalidad en todas las columnas numéricas
for column in datos.select_dtypes(include=[np.number]).columns:
    # Shapiro-Wilk
    W, p_shapiro = shapiro(datos[column].dropna())
    
    # Kolmogorov-Smirnov (contra normal estándar)
    D, p_ks = kstest(datos[column].dropna(), 'norm', 
                      args=(datos[column].mean(), datos[column].std()))
    
    estado = "✅ Normal" if p_shapiro >= 0.05 else "❌ No normal"
    print(f"{column}: W={W:.4f} (p={p_shapiro:.4f}) | D={D:.4f} (p={p_ks:.4f}) → {estado}")
```

**Ejemplo 3: Visualización con Q-Q plot**

> ¿Los puntos del Q-Q plot siguen la línea diagonal?

```python
import statsmodels.api as sm
import pingouin as pg

# Q-Q plot básico con statsmodels
fig, ax = plt.subplots(figsize=(5, 5))
sm.qqplot(datos['ingresos'], line='45', alpha=0.5, lw=1, 
          color='blue', markerfacecolor='red', markeredgewidth=0.5, ax=ax)
plt.title('Q-Q Plot: Ingresos')
plt.tight_layout()
plt.show()

# Q-Q plot con intervalo de confianza del 95% (pingouin)
fig, ax = plt.subplots(figsize=(5, 5))
pg.qqplot(datos['ingresos'], dist='norm', confidence=0.95, ax=ax)
ax.set_title('Q-Q con IC 95%: Ingresos')
plt.show()
```

> **Interpretación del Q-Q con IC**: Si los puntos caen dentro de la franja gris, los datos son consistentes con una distribución normal al nivel de confianza del 95%.

---

### 2. En Preprocesamiento y limpieza

**Ejemplo 4: Decidir transformación antes de modelar**

> ¿Necesito transformar `tiempo_respuesta` para que sea normal?

```python
import numpy as np
from scipy.stats import shapiro

# Datos originales
W_orig, p_orig = shapiro(datos['tiempo_respuesta'])
print(f"Original: W={W_orig:.4f}, p={p_orig:.6f}")

# Transformación log
datos['tiempo_log'] = np.log1p(datos['tiempo_respuesta'])
W_log, p_log = shapiro(datos['tiempo_log'])
print(f"Log:      W={W_log:.4f}, p={p_log:.6f}")

# Transformación raíz cuadrada
datos['tiempo_sqrt'] = np.sqrt(datos['tiempo_respuesta'])
W_sqrt, p_sqrt = shapiro(datos['tiempo_sqrt'])
print(f"Sqrt:     W={W_sqrt:.4f}, p={p_sqrt:.6f}")

# Elegir la transformación con mayor p-valor (más cercana a normal)
transformaciones = {'original': p_orig, 'log': p_log, 'sqrt': p_sqrt}
mejor = max(transformaciones, key=transformaciones.get)
print(f"\nMejor transformación: {mejor} (p={transformaciones[mejor]:.6f})")
```

**Ejemplo 5: Verificar normalidad después de imputación**

> ¿La imputación de valores faltantes preservó la distribución?

```python
from sklearn.impute import SimpleImputer

# Antes de imputar
W_antes, p_antes = shapiro(datos['ingresos'].dropna())

# Imputar con mediana
imputer = SimpleImputer(strategy='median')
datos['ingresos_imputado'] = imputer.fit_transform(datos[['ingresos']])

# Después de imputar
W_despues, p_despues = shapiro(datos['ingresos_imputado'])

print(f"Antes:  W={W_antes:.4f}, p={p_antes:.6f}")
print(f"Después: W={W_despues:.4f}, p={p_despues:.6f}")

if abs(p_antes - p_despues) < 0.05:
    print("✅ La imputación preservó la distribución")
else:
    print("⚠️ La imputación cambió la distribución")
```

---

### 3. En Feature Engineering

**Ejemplo 6: Evaluar normalidad de features creadas**

> ¿La feature `ratio_ingreso_gasto` es normal?

```python
# Crear feature
datos['ratio'] = datos['ingreso'] / (datos['gasto'] + 1)

# Verificar normalidad
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Histograma
sns.histplot(datos['ratio'], kde=True, ax=axes[0], color='lightblue')
axes[0].set_title('Histograma')

# Q-Q plot
pg.qqplot(datos['ratio'], dist='norm', confidence=0.95, ax=axes[1])
axes[1].set_title('Q-Q Plot')

# Shapiro-Wilk
W, p = shapiro(datos['ratio'].dropna())
axes[2].text(0.5, 0.5, f'Shapiro-Wilk\nW = {W:.4f}\np = {p:.6f}', 
             ha='center', va='center', fontsize=12, transform=axes[2].transAxes)
axes[2].axis('off')
axes[2].set_title('Prueba Estadística')

plt.tight_layout()
plt.show()
```

**Ejemplo 7: Comparar distribución de features entre train y test**

> ¿Las features tienen la misma distribución en train y test?

```python
from scipy.stats import ks_2samp

# Separar train y test
train = datos.sample(frac=0.8, random_state=42)
test = datos.drop(train.index)

# KS test entre train y test para cada feature
for col in ['ingreso', 'gasto', 'ratio']:
    D, p = ks_2samp(train[col].dropna(), test[col].dropna())
    estado = "✅ Distribuciones similares" if p >= 0.05 else "⚠️ Posible drift"
    print(f"{col}: D={D:.4f}, p={p:.6f} → {estado}")
```

---

### 4. En Selección de Modelos

**Ejemplo 8: Validar normalidad de residuos**

> ¿Los residuos de mi modelo de regresión son normales?

```python
from sklearn.linear_model import LinearRegression

# Entrenar modelo
X = datos[['ingreso', 'edad']]
y = datos['gasto']

modelo = LinearRegression()
modelo.fit(X, y)
residuos = y - modelo.predict(X)

# Verificar normalidad de residuos
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Histograma de residuos
sns.histplot(residuos, kde=True, ax=axes[0], color='lightblue')
axes[0].set_title('Distribución de Residuos')
axes[0].axvline(x=0, color='red', linestyle='--', alpha=0.5)

# Q-Q plot de residuos
pg.qqplot(residuos, dist='norm', confidence=0.95, ax=axes[1])
axes[1].set_title('Q-Q Plot: Residuos')

# Shapiro-Wilk
W, p = shapiro(residuos)
axes[2].text(0.5, 0.5, f'Shapiro-Wilk\nW = {W:.4f}\np = {p:.6f}', 
             ha='center', va='center', fontsize=12, transform=axes[2].transAxes)
axes[2].axis('off')
axes[2].set_title('Prueba Estadística')

plt.tight_layout()
plt.show()

if p >= 0.05:
    print("✅ Los residuos son normales → supuesto de regresión lineal cumplido")
else:
    print("❌ Los residuos NO son normales → considerar modelos no lineales o transformaciones")
```

**Ejemplo 9: Comparar normalidad entre modelos**

> ¿Qué modelo tiene residuos más normales?

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.linear_model import LinearRegression

modelos = {
    'Lineal': LinearRegression(),
    'Random Forest': RandomForestRegressor(n_estimators=100, random_state=42)
}

for nombre, modelo in modelos.items():
    modelo.fit(X, y)
    residuos = y - modelo.predict(X)
    W, p = shapiro(residuos)
    print(f"{nombre}: W={W:.4f}, p={p:.6f} → {'✅ Normal' if p >= 0.05 else '❌ No normal'}")
```

---

### 5. En Evaluación post-deploy

**Ejemplo 10: Verificar normalidad de predicciones**

> ¿Las predicciones del modelo en producción son normales?

```python
# Simular predicciones de producción
predicciones_produccion = modelo.predict(X_nuevos)

# Verificar normalidad
W, p = shapiro(predicciones_produccion)
print(f"Predicciones producción: W={W:.4f}, p={p:.6f}")

# Comparar con distribución de entrenamiento
W_train, p_train = shapiro(modelo.predict(X))
print(f"Predicciones entrenamiento: W={W_train:.4f}, p={p_train:.6f}")

# KS test para comparar distribuciones
D, p_ks = ks_2samp(modelo.predict(X), predicciones_produccion)
print(f"KS test (train vs prod): D={D:.4f}, p={p_ks:.6f}")
```

**Ejemplo 11: Evaluar estabilidad de métricas**

> ¿Las métricas de evaluación se mantienen estables?

```python
# Simular métricas de diferentes períodos
metricas_enero = np.random.normal(0.85, 0.02, 30)
metricas_febrero = np.random.normal(0.83, 0.03, 28)

# Verificar normalidad de cada período
W_ene, p_ene = shapiro(metricas_enero)
W_feb, p_feb = shapiro(metricas_febrero)

print(f"Enero: W={W_ene:.4f}, p={p_ene:.6f}")
print(f"Febrero: W={W_feb:.4f}, p={p_feb:.6f}")

# KS test entre períodos
D, p_ks = ks_2samp(metricas_enero, metricas_febrero)
print(f"KS test: D={D:.4f}, p={p_ks:.6f}")
```

---

### 6. En Monitoreo y detección de anomalías

**Ejemplo 12: Detectar anomalías usando normalidad**

> ¿Los valores actuales son consistentes con la distribución histórica?

```python
# Datos históricos (últimos 30 días)
datos_historicos = np.random.normal(100, 15, 30 * 24)  # 30 días de horas

# Datos actuales (últimas 24 horas)
datos_actuales = np.random.normal(105, 20, 24)  # Posible shift

# Verificar normalidad histórica
W_hist, p_hist = shapiro(datos_historicos[:5000])  # Shapiro limita a 5000
print(f"Histórico: W={W_hist:.4f}, p={p_hist:.6f}")

# KS test entre histórico y actual
D, p_ks = ks_2samp(datos_historicos, datos_actuales)
print(f"KS test: D={D:.4f}, p={p_ks:.6f}")

if p_ks < 0.05:
    print("⚠️ ALERTA: Posible drift en la distribución")
```

**Ejemplo 13: Monitoreo continuo con ventanas móviles**

> ¿La distribución cambia a lo largo del tiempo?

```python
import pandas as pd

# Simular datos diarios
np.random.seed(42)
fechas = pd.date_range('2024-01-01', periods=90, freq='D')
valores = np.concatenate([
    np.random.normal(100, 10, 30),    # Mes 1: normal
    np.random.normal(105, 12, 30),    # Mes 2: slight shift
    np.random.normal(110, 15, 30)     # Mes 3: más shift
])

df = pd.DataFrame({'fecha': fechas, 'valor': valores})

# Ventana móvil de 7 días
resultados = []
for i in range(6, len(df)):
    ventana = df['valor'].iloc[i-6:i+1]
    W, p = shapiro(ventana)
    resultados.append({
        'fecha': df['fecha'].iloc[i],
        'W': W,
        'p': p,
        'normal': p >= 0.05
    })

resultados_df = pd.DataFrame(resultados)
print(resultados_df[resultados_df['normal'] == False].head(10))
```

---

### 7. En Validación de supuestos

**Ejemplo 14: Validar supuestos de ANOVA**

> ¿Los residuos del ANOVA son normales?

```python
from scipy.stats import f_oneway

# Tres grupos
grupo_A = np.random.normal(10, 2, 30)
grupo_B = np.random.normal(12, 2, 30)
grupo_C = np.random.normal(11, 2, 30)

# ANOVA
F, p_anova = f_oneway(grupo_A, grupo_B, grupo_C)
print(f"ANOVA: F={F:.4f}, p={p_anova:.6f}")

# Verificar normalidad de cada grupo
for i, grupo in enumerate([grupo_A, grupo_B, grupo_C], 1):
    W, p = shapiro(grupo)
    print(f"Grupo {i}: W={W:.4f}, p={p:.6f} → {'✅ Normal' if p >= 0.05 else '❌ No normal'}")
```

**Ejemplo 15: Verificar normalidad de residuos en modelos mixtos**

> ¿Los residuos de un modelo mixto son normales?

```python
# Simular datos de panel
np.random.seed(42)
n_sujetos = 20
n_obs = 10

efecto_sujeto = np.random.normal(0, 2, n_sujetos)
residuos = np.random.normal(0, 1, n_sujetos * n_obs) + np.repeat(efecto_sujeto, n_obs)

# Verificar normalidad
W, p = shapiro(residuos)
print(f"Residuos modelo mixto: W={W:.4f}, p={p:.6f}")
```

---

## CASOS ESPECIALES: Data Drift con Shapiro-Wilk

### ¿Por qué Shapiro-Wilk para drift?

Shapiro-Wilk es ideal para detectar data drift porque:
1. **Es sensible a desviaciones** de la distribución (no solo media/varianza)
2. **Detecta cambios en la forma** de la distribución, no solo en los momentos
3. **Funciona bien con muestras pequeñas** (comparado con KS)
4. **Puede automatizarse** fácilmente en pipelines de monitoreo

### Ejemplo 1: Monitoreo de distribución de features en producción

```python
import numpy as np
from scipy.stats import shapiro
import pandas as pd

def monitorar_drift_shapiro(datos_entrenamiento, datos_produccion, feature, alpha=0.05):
    """
    Detecta drift usando Shapiro-Wilk comparando distribuciones.
    
    Returns: dict con resultados del drift detection
    """
    # Shapiro-Wilk en datos de entrenamiento
    W_train, p_train = shapiro(datos_entrenamiento)
    
    # Shapiro-Wilk en datos de producción
    W_prod, p_prod = shapiro(datos_produccion)
    
    # KS test para comparar directamente las distribuciones
    from scipy.stats import ks_2samp
    D, p_ks = ks_2samp(datos_entrenamiento, datos_produccion)
    
    # Detectar drift
    drift_detectado = p_ks < alpha
    
    return {
        'feature': feature,
        'W_train': W_train,
        'p_train': p_train,
        'W_prod': W_prod,
        'p_prod': p_prod,
        'D_ks': D,
        'p_ks': p_ks,
        'drift': drift_detectado
    }

# Ejemplo de uso
np.random.seed(42)
datos_train = np.random.normal(100, 15, 1000)
datos_prod = np.random.normal(105, 18, 500)  # Leve shift

resultado = monitorar_drift_shapiro(datos_train, datos_prod, 'ingreso')
print(f"Feature: {resultado['feature']}")
print(f"Train: W={resultado['W_train']:.4f}, p={resultado['p_train']:.6f}")
print(f"Prod:  W={resultado['W_prod']:.4f}, p={resultado['p_prod']:.6f}")
print(f"KS test: D={resultado['D_ks']:.4f}, p={resultado['p_ks']:.6f}")
print(f"Drift: {'⚠️ SÍ' if resultado['drift'] else '✅ NO'}")
```

### Ejemplo 2: Detección de drift entre ventanas de tiempo

```python
def comparar_ventanas(datos, ventana_anterior, ventana_actual):
    """
    Compara dos ventanas de tiempo usando Shapiro-Wilk y KS.
    """
    W1, p1 = shapiro(ventana_anterior)
    W2, p2 = shapiro(ventana_actual)
    
    D, p_ks = ks_2samp(ventana_anterior, ventana_actual)
    
    # Calcular cambio en la distribución
    cambio_W = abs(W1 - W2)
    
    return {
        'W_ventana1': W1,
        'W_ventana2': W2,
        'cambio_W': cambio_W,
        'D_ks': D,
        'p_ks': p_ks,
        'drift': p_ks < 0.05
    }

# Simular series de tiempo
np.random.seed(42)
datos = np.random.normal(100, 15, 365)  # Un año de datos

# Comparar semana 1 vs semana 2
semana1 = datos[0:7]
semana2 = datos[7:14]

resultado = comparar_ventanas(datos, semana1, semana2)
print(f"Semana 1: W={resultado['W_ventana1']:.4f}")
print(f"Semana 2: W={resultado['W_ventana2']:.4f}")
print(f"Cambio en W: {resultado['cambio_W']:.4f}")
print(f"KS test p-value: {resultado['p_ks']:.6f}")
```

### Ejemplo 3: Sistema de alertas automáticas para drift

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, ks_2samp
from datetime import datetime, timedelta

class DriftDetector:
    def __init__(self, umbral_alpha=0.05, umbral_W=0.95):
        self.umbral_alpha = umbral_alpha
        self.umbral_W = umbral_W
        self.historial = []
    
    def registrar_datos(self, fecha, datos):
        """Registra datos de un período."""
        self.historial.append({
            'fecha': fecha,
            'datos': datos
        })
    
    def verificar_drift(self, ventana_dias=7):
        """
        Verifica drift comparando los últimos N días con el histórico.
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
        
        # Shapiro-Wilk
        W_hist, p_hist = shapiro(historico[:5000])  # Límite de scipy
        W_rec, p_rec = shapiro(ventana_reciente[:5000])
        
        # KS test
        D, p_ks = ks_2samp(historico, ventana_reciente)
        
        # Determinar alerta
        alerta = {
            'fecha': datetime.now(),
            'W_historico': W_hist,
            'p_historico': p_hist,
            'W_reciente': W_rec,
            'p_reciente': p_rec,
            'D_ks': D,
            'p_ks': p_ks,
            'alerta_drift': p_ks < self.umbral_alpha,
            'alerta_distribucion': W_rec < self.umbral_W
        }
        
        return alerta

# Ejemplo de uso
detector = DriftDetector()

# Simular 30 días de datos
np.random.seed(42)
for i in range(30):
    fecha = datetime.now() - timedelta(days=30-i)
    
    if i < 20:
        # Datos normales
        datos = np.random.normal(100, 15, 100)
    else:
        # Drift: cambio en distribución
        datos = np.random.normal(108, 20, 100)
    
    detector.registrar_datos(fecha, datos)

# Verificar drift
alerta = detector.verificar_drift(ventana_dias=7)
if alerta:
    print(f"Fecha: {alerta['fecha']}")
    print(f"W histórico: {alerta['W_historico']:.4f}")
    print(f"W reciente: {alerta['W_reciente']:.4f}")
    print(f"KS p-value: {alerta['p_ks']:.6f}")
    print(f"Alerta drift: {'⚠️ SÍ' if alerta['alerta_drift'] else '✅ NO'}")
    print(f"Alerta distribución: {'⚠️ SÍ' if alerta['alerta_distribucion'] else '✅ NO'}")
```

### Ejemplo 4: Monitoreo por feature con dashboard

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, ks_2samp
import matplotlib.pyplot as plt
import seaborn as sns

def dashboard_drift(features_train, features_prod, feature_names):
    """
    Crea un dashboard de drift para múltiples features.
    
    features_train: dict con features de entrenamiento
    features_prod: dict con features de producción
    feature_names: lista de nombres de features
    """
    fig, axes = plt.subplots(len(feature_names), 3, figsize=(15, 4*len(feature_names)))
    
    resultados = []
    
    for i, feature in enumerate(feature_names):
        # Datos
        train = features_train[feature]
        prod = features_prod[feature]
        
        # Shapiro-Wilk
        W_train, p_train = shapiro(train[:5000])
        W_prod, p_prod = shapiro(prod[:5000])
        
        # KS test
        D, p_ks = ks_2samp(train, prod)
        
        # Histograma superpuesto
        axes[i, 0].hist(train, alpha=0.5, label='Train', density=True, bins=30)
        axes[i, 0].hist(prod, alpha=0.5, label='Prod', density=True, bins=30)
        axes[i, 0].set_title(f'{feature} - Distribución')
        axes[i, 0].legend()
        
        # Q-Q plot
        from scipy import stats
        stats.probplot(prod, dist="norm", plot=axes[i, 1])
        axes[i, 1].set_title(f'{feature} - Q-Q Plot')
        
        # Resultados
        drift = "⚠️ DRIFT" if p_ks < 0.05 else "✅ OK"
        axes[i, 2].text(0.1, 0.8, f'KS test: p={p_ks:.4f}', transform=axes[i, 2].transAxes)
        axes[i, 2].text(0.1, 0.6, f'Shapiro Train: W={W_train:.4f}', transform=axes[i, 2].transAxes)
        axes[i, 2].text(0.1, 0.4, f'Shapiro Prod: W={W_prod:.4f}', transform=axes[i, 2].transAxes)
        axes[i, 2].text(0.1, 0.2, f'Estado: {drift}', transform=axes[i, 2].transAxes)
        axes[i, 2].axis('off')
        
        resultados.append({
            'feature': feature,
            'W_train': W_train,
            'p_train': p_train,
            'W_prod': W_prod,
            'p_prod': p_prod,
            'D_ks': D,
            'p_ks': p_ks,
            'drift': p_ks < 0.05
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
    'ingreso': np.random.normal(52000, 12000, 500),
    'edad': np.random.normal(36, 10, 500),
    'antiguedad': np.random.exponential(6, 500)
}

resultados = dashboard_drift(features_train, features_prod, ['ingreso', 'edad', 'antiguedad'])
print(resultados[['feature', 'D_ks', 'p_ks', 'drift']])
```

### Ejemplo 5: Comparación de distribuciones entre regiones/geografías

```python
def comparar_geografias(datos_por_region, feature):
    """
    Compara distribuciones de una feature entre diferentes regiones.
    """
    resultados = []
    regiones = list(datos_por_region.keys())
    
    for i in range(len(regiones)):
        for j in range(i+1, len(regiones)):
            region1 = regiones[i]
            region2 = regiones[j]
            
            # Shapiro-Wilk para cada región
            W1, p1 = shapiro(datos_por_region[region1][feature][:5000])
            W2, p2 = shapiro(datos_por_region[region2][feature][:5000])
            
            # KS test entre regiones
            D, p_ks = ks_2samp(datos_por_region[region1][feature], 
                               datos_por_region[region2][feature])
            
            resultados.append({
                'region1': region1,
                'region2': region2,
                'W_region1': W1,
                'W_region2': W2,
                'D_ks': D,
                'p_ks': p_ks,
                'diferente': p_ks < 0.05
            })
    
    return pd.DataFrame(resultados)

# Ejemplo de uso
np.random.seed(42)
datos_por_region = {
    'Buenos Aires': np.random.normal(100, 15, 500),
    'Córdoba': np.random.normal(95, 12, 400),
    'Mendoza': np.random.normal(102, 18, 350)
}

resultados = comparar_geografias(datos_por_region, 'ingreso')
print(resultados[['region1', 'region2', 'D_ks', 'p_ks', 'diferente']])
```

### Ejemplo 6: Detección de drift en tiempo real con streaming

```python
import numpy as np
from scipy.stats import shapiro, ks_2samp
from collections import deque
from datetime import datetime

class RealTimeDriftDetector:
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
        
        # Shapiro-Wilk
        W_hist, p_hist = shapiro(self.historico[:5000])
        W_act, p_act = shapiro(datos_actuales[:5000])
        
        # KS test
        D, p_ks = ks_2samp(self.historico, datos_actuales)
        
        # Alerta
        if p_ks < self.umbral_alpha:
            alerta = {
                'timestamp': datetime.now(),
                'W_historico': W_hist,
                'W_actual': W_act,
                'D_ks': D,
                'p_ks': p_ks
            }
            self.alertas.append(alerta)
            print(f"⚠️ DRIFT DETECTADO: KS p={p_ks:.6f}")
        
        # Actualizar histórico
        self.historico = datos_actuales.copy()

# Ejemplo de uso
detector = RealTimeDriftDetector(ventana_size=50)

np.random.seed(42)
for i in range(200):
    if i < 100:
        valor = np.random.normal(100, 15)
    else:
        valor = np.random.normal(110, 20)  # Drift
    
    detector.agregar_dato(valor)

print(f"\nTotal alertas: {len(detector.alertas)}")
```

### Ejemplo 7: Análisis de drift por percentiles

```python
def analisis_drift_percentiles(datos_train, datos_prod, percentiles=[25, 50, 75]):
    """
    Analiza drift en diferentes percentiles de la distribución.
    """
    resultados = []
    
    for p in percentiles:
        p_train = np.percentile(datos_train, p)
        p_prod = np.percentile(datos_prod, p)
        cambio_pct = ((p_prod - p_train) / p_train) * 100
        
        resultados.append({
            'percentil': p,
            'valor_train': p_train,
            'valor_prod': p_prod,
            'cambio_pct': cambio_pct
        })
    
    # Shapiro-Wilk general
    W_train, p_train = shapiro(datos_train[:5000])
    W_prod, p_prod = shapiro(datos_prod[:5000])
    
    return {
        'percentiles': pd.DataFrame(resultados),
        'W_train': W_train,
        'p_train': p_train,
        'W_prod': W_prod,
        'p_prod': p_prod
    }

# Ejemplo
np.random.seed(42)
train = np.random.lognormal(10, 1, 1000)
prod = np.random.lognormal(10.2, 1.1, 500)

resultado = analisis_drift_percentiles(train, prod)
print("Análisis por percentiles:")
print(resultado['percentiles'])
print(f"\nShapiro-Wilk Train: W={resultado['W_train']:.4f}")
print(f"Shapiro-Wilk Prod: W={resultado['W_prod']:.4f}")
```

### Ejemplo 8: Validación de normalidad para pipelines de ML

```python
def validar_normalidad_pipeline(X_train, X_test, feature_names, alpha=0.05):
    """
    Valida normalidad en cada paso del pipeline de ML.
    """
    resultados = []
    
    for i, feature in enumerate(feature_names):
        # Verificar normalidad en train
        W_train, p_train = shapiro(X_train[:, i][:5000])
        
        # Verificar normalidad en test
        W_test, p_test = shapiro(X_test[:, i][:5000])
        
        # KS test entre train y test
        D, p_ks = ks_2samp(X_train[:, i], X_test[:, i])
        
        # Determinar si necesita transformación
        necesita_transform = p_train < alpha or p_test < alpha
        
        resultados.append({
            'feature': feature,
            'W_train': W_train,
            'p_train': p_train,
            'W_test': W_test,
            'p_test': p_test,
            'D_ks': D,
            'p_ks': p_ks,
            'necesita_transform': necesita_transform
        })
    
    return pd.DataFrame(resultados)

# Ejemplo
from sklearn.datasets import make_classification

X, y = make_classification(n_samples=1000, n_features=5, random_state=42)
feature_names = [f'feature_{i}' for i in range(5)]

# Split train/test
from sklearn.model_selection import train_test_split
X_train, X_test = train_test_split(X, test_size=0.2, random_state=42)

resultados = validar_normalidad_pipeline(X_train, X_test, feature_names)
print(resultados[['feature', 'p_train', 'p_test', 'necesita_transform']])
```

### Ejemplo 9: Detección de drift con múltiples pruebas

```python
def deteccion_drift_multitest(datos_train, datos_prod, alpha=0.05):
    """
    Usa múltiples pruebas para detectar drift de forma más robusta.
    """
    from scipy.stats import ks_2samp, anderson, jarque_bera
    
    resultados = {}
    
    # Shapiro-Wilk
    W_train, p_sw_train = shapiro(datos_train[:5000])
    W_prod, p_sw_prod = shapiro(datos_prod[:5000])
    resultados['shapiro_wilk'] = {
        'W_train': W_train, 'p_train': p_sw_train,
        'W_prod': W_prod, 'p_prod': p_sw_prod
    }
    
    # KS test
    D, p_ks = ks_2samp(datos_train, datos_prod)
    resultados['ks_test'] = {'D': D, 'p': p_ks}
    
    # Anderson-Darling
    ad_train = anderson(datos_train, dist='norm')
    ad_prod = anderson(datos_prod, dist='norm')
    resultados['anderson'] = {
        'stat_train': ad_train.statistic,
        'stat_prod': ad_prod.statistic
    }
    
    # Jarque-Bera (para muestras grandes)
    jb_train, p_jb_train = jarque_bera(datos_train)
    jb_prod, p_jb_prod = jarque_bera(datos_prod)
    resultados['jarque_bera'] = {
        'JB_train': jb_train, 'p_train': p_jb_train,
        'JB_prod': jb_prod, 'p_prod': p_jb_prod
    }
    
    # Decisión conjunta (voto mayoritario)
    votos_drift = sum([
        p_ks < alpha,
        p_jb_train < alpha or p_jb_prod < alpha
    ])
    
    resultados['decision'] = {
        'votos_drift': votos_drift,
        'drift_detectado': votos_drift >= 2
    }
    
    return resultados

# Ejemplo
np.random.seed(42)
train = np.random.normal(100, 15, 1000)
prod = np.random.normal(103, 18, 500)

resultados = deteccion_drift_multitest(train, prod)
print(f"Shapiro-Wilk: W_train={resultados['shapiro_wilk']['W_train']:.4f}")
print(f"KS test: p={resultados['ks_test']['p']:.6f}")
print(f"Jarque-Bera: p_train={resultados['jarque_bera']['p_train']:.6f}")
print(f"Decisión: {'⚠️ DRIFT' if resultados['decision']['drift_detectado'] else '✅ OK'}")
```

### Ejemplo 10: Sistema completo de monitoreo de drift

```python
import pandas as pd
import numpy as np
from scipy.stats import shapiro, ks_2samp
from datetime import datetime, timedelta
import json

class SistemaMonitoreoDrift:
    def __init__(self, config=None):
        self.config = config or {
            'umbral_alpha': 0.05,
            'umbral_W': 0.95,
            'ventana_size': 100,
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
            'std': np.std(datos),
            'min': np.min(datos),
            'max': np.max(datos)
        }
        
        # Shapiro-Wilk
        if len(datos) >= self.config['min_datos']:
            W, p = shapiro(datos[:5000])
            metrica['W'] = W
            metrica['p_shapiro'] = p
            metrica['normal'] = p >= self.config['umbral_alpha']
        
        self.metricas.append(metrica)
        
        return metrica
    
    def verificar_drift(self, lote_actual_id, lote_referencia_id=None):
        """
        Verifica drift entre un lote actual y uno de referencia.
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
        
        # KS test
        D, p_ks = ks_2samp(datos_ref, datos_act)
        
        # Shapiro-Wilk
        W_ref, p_ref = shapiro(datos_ref[:5000])
        W_act, p_act = shapiro(datos_act[:5000])
        
        # Determinar alerta
        drift_detectado = p_ks < self.config['umbral_alpha']
        
        resultado = {
            'lote_referencia': lote_referencia_id,
            'lote_actual': lote_actual_id,
            'D_ks': D,
            'p_ks': p_ks,
            'W_referencia': W_ref,
            'W_actual': W_act,
            'p_ref': p_ref,
            'p_act': p_act,
            'drift_detectado': drift_detectado,
            'timestamp': datetime.now()
        }
        
        if drift_detectado:
            self.alertas.append(resultado)
            print(f"⚠️ DRIFT ALERTA: Lote {lote_actual_id}")
        
        return resultado
    
    def generar_reporte(self):
        """Genera un reporte del estado del monitoreo."""
        df_metricas = pd.DataFrame(self.metricas)
        
        reporte = {
            'total_lotes': len(self.historial),
            'total_alertas': len(self.alertas),
            'lotes_normales': df_metricas['normal'].sum() if 'normal' in df_metricas.columns else 0,
            'metricas_promedio': {
                'media': df_metricas['media'].mean(),
                'std': df_metricas['std'].mean(),
                'W_promedio': df_metricas['W'].mean() if 'W' in df_metricas.columns else None
            }
        }
        
        return reporte

# Ejemplo de uso completo
sistema = SistemaMonitoreoDrift()

# Simular 10 lotes de datos
np.random.seed(42)
for i in range(10):
    fecha = datetime.now() - timedelta(days=10-i)
    
    if i < 7:
        # Datos normales
        datos = np.random.normal(100, 15, 100)
    else:
        # Drift
        datos = np.random.normal(108, 20, 100)
    
    sistema.registrar_lote(fecha, datos)

# Verificar drift para cada lote
for i in range(1, 10):
    sistema.verificar_drift(i)

# Generar reporte
reporte = sistema.generar_reporte()
print("\nReporte del Sistema de Monitoreo:")
print(f"Total lotes: {reporte['total_lotes']}")
print(f"Total alertas: {reporte['total_alertas']}")
print(f"Lotes normales: {reporte['lotes_normales']}")
```

---

## Interpretación de resultados

### Salida de scipy.stats.shapiro

```python
W, p = shapiro(datos)
# W = 0.9867
# p = 0.2345
```

**Cómo leerla:**
- **W**: estadístico de Shapiro-Wilk (0 a 1, más cercano a 1 = más normal)
- **p**: valor p (probabilidad de observar estos datos si fueran normales)

### Salida de scipy.stats.kstest

```python
D, p = kstest(datos, 'norm', args=(0, 1))
# D = 0.0834
# p = 0.5678
```

**Cómo leerla:**
- **D**: estadístico de Kolmogorov-Smirnov (máxima distancia entre CDFs)
- **p**: valor p

### Reglas de decisión

```
p ≥ 0.05  → ✅ No se rechaza H₀ (posible normalidad)
p < 0.05  → ❌ Se rechaza H₀ (no es normal)
p < 0.01  → ❌❌ Fuerte evidencia de no normalidad
p < 0.001 → ❌❌❌ Evidencia muy fuerte de no normalidad
```

### ¿Qué reportar?

```python
# Formato completo
print(f"Shapiro-Wilk: W = {W:.4f}, p = {p:.6f}")
print(f"Kolmogorov-Smirnov: D = {D:.4f}, p = {p:.6f}")

# Ejemplo:
# "Se realizó la prueba de Shapiro-Wilk para verificar la normalidad de
# la variable 'ingresos' (n = 1000). Se obtuvo W = 0.9867, p = 0.2345,
# lo cual indica que no se rechaza la hipótesis nula de normalidad
# al nivel de significancia del 5%."
```

### ¿Qué hacer si los datos NO son normales?

1. **No apresurarse**: muchos métodos son robustos a desviaciones leves
2. **Transformar**: log, raíz cuadrada, Box-Cox
3. **Usar métodos no paramétricos**: Mann-Whitney, Kruskal-Wallis
4. **Aumentar n**: con muestras grandes, CLT puede rescatarte
5. **Reportar**: documentar la desviación y su impacto

---

## Errores comunes

### 1. Concluir normalidad solo con Shapiro-Wilk

```python
# MAL: "Shapiro-Wilk no rechazó, entonces mis datos SON normales"
W, p = shapiro(datos)
if p >= 0.05:
    print("Datos normales")  # ❌ No es tan simple

# BIEN: "Shapiro-Wilk no rechaza la hipótesis de normalidad"
if p >= 0.05:
    print("No se rechaza normalidad (Shapiro-Wilk)")
    print("Pero verificar con Q-Q plot y histograma")
```

### 2. Ignorar el tamaño de muestra

```python
# MAL: confiar en Shapiro-Wirk con n = 5000
W, p = shapiro(datos_grandes)  # p será muy sensible

# BIEN: con n > 5000, solo usar métodos gráficos
if len(datos) > 5000:
    print("Muestra grande: usar Q-Q plot + histograma")
else:
    W, p = shapiro(datos)
    print(f"Shapiro-Wilk: W={W:.4f}, p={p:.6f}")
```

### 3. No combinar métodos gráficos y estadísticos

```python
# MAL: solo mirar el p-valor
W, p = shapiro(datos)
print(f"p = {p:.6f}")  # ❌ No te dice POR QUÉ no es normal

# BIEN: combinar métodos
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Histograma
sns.histplot(datos, kde=True, ax=axes[0])
axes[0].set_title('Histograma')

# Q-Q plot
pg.qqplot(datos, dist='norm', confidence=0.95, ax=axes[1])
axes[1].set_title('Q-Q Plot')

# Shapiro-Wilk
W, p = shapiro(datos)
axes[2].text(0.5, 0.5, f'W={W:.4f}\np={p:.6f}', 
             ha='center', va='center', transform=axes[2].transAxes)
axes[2].axis('off')
axes[2].set_title('Shapiro-Wilk')

plt.tight_layout()
plt.show()
```

### 4. Usar Kolmogorov-Smirnov para normalidad con parámetros estimados

```python
# MAL: KS test con media y std estimados de los datos
D, p = kstest(datos, 'norm', args=(datos.mean(), datos.std()))

# BIEN: KS test contra una distribución teórica conocida
D, p = kstest(datos, 'norm', args=(0, 1))  # Normal estándar teórica
```

### 5. No reportar el método usado

```python
# MAL: "la distribución es normal"
# BIEN: "según la prueba de Shapiro-Wilk (W = 0.987, p = 0.234),
#        no se rechaza la hipótesis de normalidad"
```

### 6. Confundir p-valor alto con "los datos son normales"

```python
# MAL: "p = 0.9, ¡excelente normalidad!"
# BIEN: "p = 0.9 indica que no hay evidencia para rechazar la normalidad,
#        pero esto no confirma que los datos sean normales"
```

### 7. Usar solo una prueba para decidir

```python
# MAL: solo Shapiro-Wilk
W, p = shapiro(datos)
if p < 0.05:
    print("No normal")  # ❌ Falta contexto

# BIEN: combinar pruebas
W_sw, p_sw = shapiro(datos)
D_ks, p_ks = kstest(datos, 'norm', args=(0, 1))

print(f"Shapiro-Wilk: W={W_sw:.4f}, p={p_sw:.6f}")
print(f"Kolmogorov-Smirnov: D={D_ks:.4f}, p={p_ks:.6f}")

# Si ambos rechazan → fuerte evidencia de no normalidad
# Si uno rechaza y otro no → revisar gráficamente
# Si ninguno rechaza → posible normalidad (verificar con gráficos)
```

---

## Flujo completo de código

```python
import pandas as pd
import numpy as np
from scipy import stats
from scipy.stats import shapiro, kstest, ks_2samp
import statsmodels.api as sm
import pingouin as pg
import seaborn as sns
import matplotlib.pyplot as plt

# ============================================
# FLUJO COMPLETO: Análisis de normalidad
# ============================================

# 1. Cargar datos
datos = pd.read_csv('tu_dataset.csv')

# 2. Seleccionar variable a analizar
variable = 'tu_variable'
datos_var = datos[variable].dropna()

# 3. Exploración visual
fig, axes = plt.subplots(2, 2, figsize=(12, 10))

# Histograma con KDE
sns.histplot(datos_var, kde=True, bins=30, color='lightblue', ax=axes[0, 0])
axes[0, 0].set_title('Histograma con KDE')
axes[0, 0].axvline(x=datos_var.mean(), color='red', linestyle='--', alpha=0.7, label='Media')
axes[0, 0].axvline(x=datos_var.median(), color='green', linestyle='--', alpha=0.7, label='Mediana')
axes[0, 0].legend()

# Boxplot
sns.boxplot(data=datos_var, ax=axes[0, 1])
axes[0, 1].set_title('Boxplot')

# Q-Q plot con statsmodels
sm.qqplot(datos_var, line='45', alpha=0.5, lw=1, 
          color='blue', markerfacecolor='red', markeredgewidth=0.5, ax=axes[1, 0])
axes[1, 0].set_title('Q-Q Plot (statsmodels)')

# Q-Q plot con pingouin (intervalos de confianza)
pg.qqplot(datos_var, dist='norm', confidence=0.95, ax=axes[1, 1])
axes[1, 1].set_title('Q-Q Plot con IC 95% (pingouin)')

plt.tight_layout()
plt.show()

# 4. Pruebas estadísticas
print("=" * 50)
print("PRUEBAS DE NORMALIDAD")
print("=" * 50)

# Shapiro-Wilk
if len(datos_var) <= 5000:
    W, p_shapiro = shapiro(datos_var)
    print(f"\nShapiro-Wilk:")
    print(f"  W = {W:.4f}")
    print(f"  p = {p_shapiro:.6f}")
    print(f"  Resultado: {'✅ No se rechaza H₀' if p_shapiro >= 0.05 else '❌ Se rechaza H₀'}")
else:
    print(f"\nShapiro-Wilk: No aplica (n = {len(datos_var)} > 5000)")

# Kolmogorov-Smirnov (contra normal estándar)
D, p_ks = kstest(datos_var, 'norm', args=(0, 1))
print(f"\nKolmogorov-Smirnov:")
print(f"  D = {D:.4f}")
print(f"  p = {p_ks:.6f}")
print(f"  Resultado: {'✅ No se rechaza H₀' if p_ks >= 0.05 else '❌ Se rechaza H₀'}")

# 5. Resumen
print("\n" + "=" * 50)
print("RESUMEN")
print("=" * 50)
print(f"Variable: {variable}")
print(f"n = {len(datos_var)}")
print(f"Media = {datos_var.mean():.4f}")
print(f"Desvío estándar = {datos_var.std():.4f}")
print(f"Asimetría = {datos_var.skew():.4f}")
print(f"Curtosis = {datos_var.kurtosis():.4f}")

# 6. Interpretación
if len(datos_var) <= 5000:
    if p_shapiro >= 0.05:
        print("\n✅ La variable parece seguir una distribución normal")
        print("   (Shapiro-Wilk no rechaza H₀)")
    else:
        print("\n❌ La variable NO parece seguir una distribución normal")
        print("   (Shapiro-Wilk rechaza H₀)")
        print("   Considerar transformaciones o métodos no paramétricos")
else:
    print("\n⚠️ Muestra muy grande para Shapiro-Wilk")
    print("   Verificar gráficamente con Q-Q plot e histograma")
```

---

## Resumen: cuándo usar cada prueba

```
¿Qué querés hacer?
│
├── EDA rápido / exploración
│   └── Histograma + KDE + Q-Q plot
│
├── Verificar normalidad formalmente
│   │
│   ├── n < 5000
│   │   └── Shapiro-Wilk + Q-Q plot
│   │
│   └── n > 5000
│       └── Solo Q-Q plot + histograma
│
├── Comparar contra distribución teórica
│   └── Kolmogorov-Smirnov
│
├── Detectar drift en producción
│   │
│   ├── Shapiro-Wilk (sensible a cambios)
│   └── KS test (comparar distribuciones)
│
├── Validar residuos de modelos
│   └── Q-Q plot + Shapiro-Wilk
│
├── Elegir entre paramétrico y no paramétrico
│   └── Shapiro-Wilk (n < 5000) + Q-Q plot
│
└── No sabés cuál usar
    └── Combinar: histograma + Q-Q plot + Shapiro-Wilk
```

---

## Código rápido de referencia

```python
from scipy.stats import shapiro, kstest, ks_2samp
import statsmodels.api as sm
import pingouin as pg
import seaborn as sns
import matplotlib.pyplot as plt

# ============================================
# SHAPIRO-WILK (recomendado para n < 5000)
# ============================================

W, p = shapiro(datos)
print(f"Shapiro-Wilk: W = {W:.4f}, p = {p:.6f}")

# ============================================
# KOLMOGOROV-SMIRNOV (contra distribución teórica)
# ============================================

# Contra normal estándar (media=0, std=1)
D, p = kstest(datos, 'norm', args=(0, 1))
print(f"K-S: D = {D:.4f}, p = {p:.6f}")

# Contra normal con media y std específicas
D, p = kstest(datos, 'norm', args=(media, std))

# ============================================
# K-S PARA COMPARAR DOS MUESTRAS
# ============================================

D, p = ks_2samp(muestra1, muestra2)
print(f"K-S (2 muestras): D = {D:.4f}, p = {p:.6f}")

# ============================================
# Q-Q PLOT (statsmodels)
# ============================================

fig, ax = plt.subplots(figsize=(5, 5))
sm.qqplot(datos, line='45', alpha=0.5, lw=1, 
          color='blue', markerfacecolor='red', markeredgewidth=0.5, ax=ax)
plt.title('Q-Q Plot')
plt.tight_layout()
plt.show()

# ============================================
# Q-Q PLOT CON INTERVALO DE CONFIANZA (pingouin)
# ============================================

fig, ax = plt.subplots(figsize=(5, 5))
pg.qqplot(datos, dist='norm', confidence=0.95, ax=ax)
ax.set_title('Q-Q Plot con IC 95%')
plt.show()

# ============================================
# HISTOGRAMA CON KDE
# ============================================

plt.figure(figsize=(8, 5))
sns.histplot(datos, kde=True, bins=30, color='lightblue')
plt.title('Histograma con KDE')
plt.axvline(x=datos.mean(), color='red', linestyle='--', label='Media')
plt.legend()
plt.show()

# ============================================
# COMBINAR MÉTODOS (recomendado)
# ============================================

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

# Histograma
sns.histplot(datos, kde=True, ax=axes[0], color='lightblue')
axes[0].set_title('Histograma')

# Q-Q plot
pg.qqplot(datos, dist='norm', confidence=0.95, ax=axes[1])
axes[1].set_title('Q-Q Plot')

# Shapiro-Wilk
W, p = shapiro(datos)
axes[2].text(0.5, 0.5, f'Shapiro-Wilk\nW = {W:.4f}\np = {p:.6f}', 
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
| 1 | ¿Qué variable quiero analizar? | Selección |
| 2 | ¿Cuántos datos tengo? (n) | `len(datos)` |
| 3 | Visualizar la distribución | Histograma + KDE |
| 4 | Verificar con Q-Q plot | `statsmodels` o `pingouin` |
| 5 | ¿Los puntos siguen la línea diagonal? | Inspección visual |
| 6 | Ejecutar Shapiro-Wilk (si n < 5000) | `scipy.stats.shapiro` |
| 7 | Ejecutar Kolmogorov-Smirnov | `scipy.stats.kstest` |
| 8 | Interpretar p-valores | p ≥ 0.05 → posible normal |
| 9 | Combinar resultados gráficos + estadísticos | Juicio experto |
| 10 | Reportar: método, valor, p, n, conclusión | Formato completo |

---

## Referencias

- Shapiro, S. S., & Wilk, M. B. (1965). An analysis of variance test for normality. *Biometrika*, 52(3/4), 591-611.
- Kolmogorov, A. N. (1933). Sulla determinazione empirica di una legge di distribuzione. *Giornale dell'Istituto Italiano degli Attuari*, 4, 83-91.
- Looney, S. W., & Gulledge, T. R. (1985). Use of the correlation coefficient with normal probability plots. *The American Statistician*, 39(1), 44-48.
- scipy.stats.shapiro: https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.shapiro.html
- scipy.stats.kstest: https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.kstest.html
