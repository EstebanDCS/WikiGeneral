---
title: Herramientas Python para ML
type: topic
subject: Sistemas Inteligentes
sources: [p2.1-introduccion-entorno.md, p2.2-preprocesado-practica.md, p2.3-visualizacion-practica.md, p3.1-clasificacion-practica.md, p3.2-regresion-practica.md, p4.1-redes-regresion-practica.md]
tags: [python, pandas, sklearn, keras, tensorflow, matplotlib, seaborn, sistemas-inteligentes, universidad]
---

## Entorno de trabajo

| Herramienta | Para qué |
|------------|---------|
| **VSCode** | Editor principal; extensiones Python y Jupyter |
| **Jupyter Notebook** | Mezcla código + texto; cell by cell execution |
| **Conda** | Gestión de entornos Python; environment `SSII` de la asignatura |

---

## Pandas — Análisis y Manipulación de Datos

```python
import pandas as pd

# Cargar datos
data = pd.read_csv("archivo.csv")
data = pd.read_pickle("archivo.pkl")  # preserva tipos

# Exploración
data.columns          # nombres de columnas
data.dtypes           # tipos de cada columna
data.describe()       # estadísticas básicas
data.isnull().any()   # columnas con NaN
data.head(5)          # primeras 5 filas
len(data)             # número de filas

# Acceso y filtrado
data["col"]                        # columna
data[["col1", "col2"]]             # varias columnas
data.loc[data["col"] == "valor"]   # filtrar filas
data.loc[(cond1) & (cond2)]        # filtro múltiple
data["col"].isin(["a", "b"])       # membership

# Modificación
data["nueva"] = data["col"] + 1    # nueva columna
data = data.drop(columns=["col"])  # eliminar columna
data["col"] = data["col"].astype(int)  # cambiar tipo

# Agrupación
data.groupby("col")["otra"].mean()
data.pivot_table(index=..., columns=..., values=..., aggfunc=...)

# Guardar
data.to_pickle("archivo.pkl")  # recomendado (preserva tipos)
```

**Nota importante:** Las operaciones de pandas **no modifican el DataFrame por defecto** — hay que reasignar (`data = data.drop(...)`) o usar `inplace=True`.

---

## Scikit-learn — Machine Learning Clásico

```python
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler, MinMaxScaler
from sklearn.preprocessing import PolynomialFeatures
from sklearn.dummy import DummyClassifier, DummyRegressor
from sklearn.linear_model import LogisticRegression, LinearRegression
from sklearn.neighbors import KNeighborsClassifier, KNeighborsRegressor
from sklearn.tree import DecisionTreeClassifier, DecisionTreeRegressor
from sklearn.svm import SVC, SVR
from sklearn import metrics
```

### Flujo estándar

```python
# 1. Separar X e Y
X = data[["col1", "col2"]]
Y = data["target"]

# 2. Dividir en train y test
X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.2, random_state=42)

# 3. Estandarizar (parámetros solo de train)
scaler = StandardScaler()
X_train_std = scaler.fit_transform(X_train)
X_test_std = scaler.transform(X_test)   # ← NO fit_transform

# 4. Entrenar
model = LogisticRegression()
model.fit(X_train_std, Y_train)

# 5. Predecir y evaluar
Y_pred = model.predict(X_test_std)
print(metrics.accuracy_score(Y_test, Y_pred))
print(metrics.f1_score(Y_test, Y_pred))
print(metrics.confusion_matrix(Y_test, Y_pred))
```

### Métricas clave

```python
# Clasificación
metrics.accuracy_score(Y_test, Y_pred)
metrics.precision_score(Y_test, Y_pred)
metrics.recall_score(Y_test, Y_pred)
metrics.f1_score(Y_test, Y_pred, average='macro')  # multiclase
metrics.confusion_matrix(Y_test, Y_pred)

# Regresión
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score
mean_absolute_error(Y_test, Y_pred)
r2_score(Y_test, Y_pred)
```

---

## Matplotlib + Seaborn — Visualización

```python
import matplotlib.pyplot as plt
import seaborn as sns
sns.set_style("whitegrid")

# Distribuciones
sns.histplot(data=data, x="col", bins=20)
sns.kdeplot(data=data, x="col", hue="categoria")
sns.countplot(x=data["col_categorica"])

# Relaciones
sns.barplot(data=data, x="cat", y="num")
sns.lineplot(data=data, x="x", y="y")
sns.scatterplot(data=data, x="x", y="y", hue="cat")
sns.regplot(data=data, x="x", y="y")  # con línea de tendencia

# Avanzado
sns.pairplot(data=data, vars=["col1","col2","col3"], hue="cat")
sns.heatmap(pivot_table_data, annot=True)

plt.figure(figsize=(12, 6))
plt.title("Título")
plt.show()
```

---

## TensorFlow / Keras — Redes Neuronales

```python
import tensorflow as tf
from tensorflow import keras

# Crear modelo
model = keras.Sequential([
    keras.layers.Input(shape=(n_features,)),
    keras.layers.Dense(10, activation='tanh'),  # capa oculta
    keras.layers.Dense(1)                        # capa de salida (regresión)
])

# Compilar
model.compile(optimizer='adam', loss='mae', metrics=['mae'])
model.summary()  # ver arquitectura y número de parámetros

# Entrenar
history = model.fit(X_train, Y_train, epochs=100, batch_size=32, validation_split=0.1)

# Predecir
Y_pred = model.predict(X_test)
```

### Activación + Loss según problema

| Problema | Última capa | Loss |
|----------|------------|------|
| Regresión | `None` o `relu` | `mse` o `mae` |
| Clasificación binaria | `sigmoid` | `binary_crossentropy` |
| Multi-clase | `softmax` | `categorical_crossentropy` |
| Multi-etiqueta | `sigmoid` | `binary_crossentropy` |

**Nota:** ejecutar `.fit()` varias veces continúa el entrenamiento. Para reiniciar hay que recrear y compilar el modelo.

---

## Bokeh — Visualización Interactiva

```python
from bokeh.plotting import figure, show, ColumnDataSource
from bokeh.io import output_notebook
output_notebook()

source = ColumnDataSource(data=dict(x=..., y=..., color=...))
p = figure(title="...", tools="hover,box_zoom,reset")
p.circle('x', 'y', color='color', source=source)
show(p)
```

---

## Fuentes

- [[p2.1-introduccion-entorno]]
- [[p2.2-preprocesado-practica]]
- [[p2.3-visualizacion-practica]]
- [[p3.1-clasificacion-practica]]
- [[p3.2-regresion-practica]]
- [[p4.1-redes-regresion-practica]]

## Related Topics

- [[Preprocesado de Datos]]
- [[Visualización de Datos]]
- [[Clasificación]]
- [[Regresión]]
- [[Redes Neuronales]]
