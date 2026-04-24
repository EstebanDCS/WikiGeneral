---
title: Guía Examen Práctico — Sistemas Inteligentes
description: "Guía completa paso a paso para el examen práctico de SI: preprocesado, modelos clásicos y redes neuronales con loan_data.csv."
type: analysis
analysis_type: study-guide
date: 2026-04-24
subject: Sistemas Inteligentes
sources: [Plantilla_PL4.ipynb, P3.1-Clasificación.ipynb, P3.2-Regresión.ipynb, P4.1-Redes neuronales (Regresión).ipynb]
tags: [examen, estudio, machine-learning, universidad, sistemas-inteligentes]
---

# Guía Examen Práctico — Sistemas Inteligentes

> [!tip] TL;DR
> El examen es un notebook con **3 partes** y un dataset ya cargado (`loan_data.csv`).
> Tu trabajo es: limpiar datos → elegir X e Y → entrenar modelos → comparar → redes neuronales.
> Todo el código lo tienes aquí, cópialo y adáptalo.

---

## El dataset del examen: `loan_data.csv`

El dataset viene **pre-cargado** al inicio del examen con este código ya escrito:

```python
import urllib.request
import pandas as pd

urllib.request.urlretrieve(
    "https://raw.githubusercontent.com/AIC-Uniovi/Sistemas-Inteligentes/refs/heads/main/datasets/loan_data.csv",
    "loan_data.csv"
)

data = pd.read_csv("loan_data.csv")
# Preprocessing básico ya hecho:
data = pd.get_dummies(data, columns=["person_education", "person_home_ownership", "loan_intent"], dtype=int)
data["previous_loan_defaults_on_file"] = (data["previous_loan_defaults_on_file"] == "Yes").astype(int)
data["person_age"] = data["person_age"].astype(int)
```

### Columnas principales

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `person_age` | int | Edad |
| `person_gender` | str | Género ("male"/"female") |
| `person_income` | float | Ingresos anuales |
| `person_emp_exp` | float | Años de experiencia laboral |
| `loan_amnt` | float | Cantidad del préstamo |
| `loan_int_rate` | float | Tasa de interés |
| `loan_percent_income` | float | % de ingresos que representa el préstamo |
| `credit_score` | float | Puntuación crediticia |
| `previous_loan_defaults_on_file` | int (0/1) | Si ha tenido impagos previos |
| `loan_status` | int (0/1) | **Variable objetivo** — 1=impagó, 0=pagó |
| `person_education_*` | int (0/1) | One-hot de nivel educativo |
| `person_home_ownership_*` | int (0/1) | One-hot de tipo de vivienda |
| `loan_intent_*` | int (0/1) | One-hot del motivo del préstamo |

> [!warning] Atención
> `loan_status` es el target (lo que queremos predecir). Es **binario** → problema de **clasificación**.

---

## PARTE 1 — Preprocesado (3 puntos)

### 1a. Pasar una columna a binario (0.5 pts)

**¿Qué te van a pedir?** Convertir una columna categórica a 0/1.

```python
# Opción 1: con comparación booleana
data["person_gender"] = (data["person_gender"] == "male").astype(int)

# Opción 2: con map
data["person_gender"] = data["person_gender"].map({"male": 1, "female": 0})

# Opción 3: astype(bool) — ojo, esto convierte cualquier valor no-vacío a True
data["person_gender"] = data["person_gender"].astype(bool)
```

> [!tip] Cuál usar
> Para categorías binarias como "Yes"/"No" o "male"/"female" → usa la **comparación booleana**. Es la más clara y menos propensa a errores.

---

### 1b. Hacer una gráfica (0.5 pts)

**¿Qué te van a pedir?** Un barplot, lineplot, histplot o similar con seaborn.

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Barplot: media de una variable numérica por categoría
sns.barplot(data=data, x="person_age", y="person_income")
plt.show()

# Lineplot
sns.lineplot(data=data, x="loan_amnt", y="loan_int_rate")
plt.show()

# Histplot: distribución de una variable
sns.histplot(data=data, x="credit_score", bins=30)
plt.show()

# Countplot: frecuencia de una categoría
sns.countplot(data=data, x="loan_status")
plt.show()
```

> [!tip] Consejo examen
> Lee bien el enunciado. Si piden "los que tienen edad entre X e Y", **filtra primero** con `.loc[]` y luego grafica ese subconjunto.

```python
# Patrón típico: filtrar + graficar
subconjunto = data.loc[(data["person_age"] > 21) & (data["person_age"] < 40)]
sns.barplot(data=subconjunto, x="person_age", y="person_income")
plt.show()
```

---

### 1c. Preprocesado principal (2 pts)

Esta es la parte más importante. Tiene 3 pasos: **elegir X e Y → train/test split → estandarizar**.

#### Paso 1: Identificar el tipo de problema

> **Clasificación** → la Y es una **categoría** (0/1, "impagó"/"pagó", etc.)
> **Regresión** → la Y es un **número continuo** (precio, tiempo, velocidad, etc.)

Con `loan_data.csv`, `loan_status` es 0/1 → **clasificación**.

#### Paso 2: Elegir X e Y

```python
# Columnas que usarás como entrada (X) — elige las más relevantes
X = data[["person_age", "person_income", "loan_amnt", "loan_int_rate",
          "credit_score", "previous_loan_defaults_on_file"]]

# Variable que quieres predecir (Y)
Y = data["loan_status"]
```

> [!tip] Qué columnas elegir para X
> - Para predecir si alguien paga el préstamo: edad, ingresos, cantidad, interés, credit score, impagos previos.
> - **No incluyas la columna Y en las X.**
> - Puedes incluir las columnas one-hot si quieres (education, home_ownership, loan_intent).

#### Paso 3: Train/Test Split

```python
from sklearn.model_selection import train_test_split

seed = 2533  # ← usa siempre la misma semilla que te den

data_train, data_test = train_test_split(data, test_size=0.3, random_state=seed)

X_train = data_train[["person_age", "person_income", "loan_amnt", "loan_int_rate",
                       "credit_score", "previous_loan_defaults_on_file"]]
Y_train = data_train["loan_status"]

X_test = data_test[["person_age", "person_income", "loan_amnt", "loan_int_rate",
                     "credit_score", "previous_loan_defaults_on_file"]]
Y_test = data_test["loan_status"]
```

> [!warning] Error frecuente en el examen
> En la Plantilla_PL4 hay un error: `X_test = data_train[...]` en vez de `data_test[...]`. Asegúrate de que **X_test viene de data_test**, no de data_train.

#### Paso 4: Estandarizar

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_train_std = scaler.fit_transform(X_train)   # fit + transform sobre train
X_test_std  = scaler.transform(X_test)         # SOLO transform sobre test (¡no fit!)
```

> [!warning] Regla de oro
> El scaler **aprende** (`fit`) solo con los datos de entrenamiento. Con test solo aplica la transformación (`transform`). Si haces `fit_transform` en test estás usando información del futuro → trampa.

---

## PARTE 2 — Modelos (entrenar y comparar)

### El flujo de trabajo para CADA modelo

```
1. Crear el modelo
2. .fit(X_train_std, Y_train)         ← entrenar
3. pred = modelo.predict(X_test_std)  ← predecir
4. Calcular métricas
5. Meter en la tabla comparativa
```

### Modelos de clasificación

```python
from sklearn.dummy import DummyClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.svm import SVC
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn import metrics

seed = 2533

# ── Baselines ──────────────────────────────────────────────────────────────
modelo_random = DummyClassifier(strategy="uniform", random_state=seed)
modelo_zeror  = DummyClassifier(strategy="most_frequent")

# ── Modelos reales ──────────────────────────────────────────────────────────
modelo_arbol  = DecisionTreeClassifier(random_state=seed, max_depth=5)
modelo_knn    = KNeighborsClassifier(n_neighbors=5)
modelo_svm    = SVC(kernel='rbf')
modelo_logit  = LogisticRegression(max_iter=1000)
modelo_forest = RandomForestClassifier(random_state=seed)

# ── Patrón de entrenamiento + métricas ──────────────────────────────────────
def evaluar_clasificacion(nombre, modelo, X_train, Y_train, X_test, Y_test):
    modelo.fit(X_train, Y_train)
    pred = modelo.predict(X_test)
    acc = metrics.accuracy_score(Y_test, pred)
    f1  = metrics.f1_score(Y_test, pred, average="weighted")
    return {"Modelo": nombre, "Accuracy": round(acc, 4), "F1": round(f1, 4)}

resultados = []
for nombre, modelo in [
    ("Random",          modelo_random),
    ("Zero-R",          modelo_zeror),
    ("Árbol Decisión",  modelo_arbol),
    ("KNN (k=5)",       modelo_knn),
    ("SVM RBF",         modelo_svm),
    ("Reg. Logística",  modelo_logit),
    ("Random Forest",   modelo_forest),
]:
    resultados.append(evaluar_clasificacion(nombre, modelo, X_train_std, Y_train, X_test_std, Y_test))

tabla = pd.DataFrame(resultados)
print(tabla.sort_values("F1", ascending=False).to_string(index=False))
```

### Métricas de clasificación

| Métrica | Función | Mejor valor | ¿Cuándo usarla? |
|---------|---------|-------------|-----------------|
| **Accuracy** | `metrics.accuracy_score(Y_test, pred)` | 1.0 | Clases balanceadas |
| **F1 Score** | `metrics.f1_score(Y_test, pred, average="weighted")` | 1.0 | Clases desbalanceadas |
| Precision | `metrics.precision_score(Y_test, pred)` | 1.0 | Minimizar falsos positivos |
| Recall | `metrics.recall_score(Y_test, pred)` | 1.0 | Minimizar falsos negativos |

> [!tip] En el examen con loan_data
> `loan_status` puede estar desbalanceado (hay más préstamos pagados que impagados). Usa **F1 weighted** para elegir el mejor modelo.

---

### Modelos de regresión (si la Y fuera numérica)

```python
from sklearn.dummy import DummyRegressor
from sklearn.linear_model import LinearRegression
from sklearn.tree import DecisionTreeRegressor
from sklearn.neighbors import KNeighborsRegressor
from sklearn.svm import SVR
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

# ── Patrón de entrenamiento + métricas ──────────────────────────────────────
def evaluar_regresion(nombre, modelo, X_train, Y_train, X_test, Y_test):
    modelo.fit(X_train, Y_train)
    pred = modelo.predict(X_test)
    return {
        "Modelo": nombre,
        "MAE":    round(mean_absolute_error(Y_test, pred), 4),
        "MSE":    round(mean_squared_error(Y_test, pred), 4),
        "R²":     round(r2_score(Y_test, pred), 4),
    }

resultados = []
for nombre, modelo in [
    ("Baseline (media)", DummyRegressor(strategy="mean")),
    ("Lineal",           LinearRegression()),
    ("KNN",              KNeighborsRegressor()),
    ("Árbol",            DecisionTreeRegressor(random_state=seed)),
    ("SVR",              SVR()),
]:
    resultados.append(evaluar_regresion(nombre, modelo, X_train_std, Y_train, X_test_std, Y_test))

tabla = pd.DataFrame(resultados)
print(tabla.sort_values("R²", ascending=False).to_string(index=False))
```

### Métricas de regresión

| Métrica | Función | Mejor valor | Interpretación |
|---------|---------|-------------|----------------|
| **MAE** | `mean_absolute_error(Y_test, pred)` | 0 (más bajo mejor) | Error medio en las mismas unidades que Y |
| **MSE** | `mean_squared_error(Y_test, pred)` | 0 (más bajo mejor) | Penaliza errores grandes al cuadrar |
| **R²** | `r2_score(Y_test, pred)` | 1.0 (más alto mejor) | % de varianza explicada (0.9 = "explica el 90%") |

---

### Cómo elegir el mejor modelo

**Para clasificación:** El que tenga **mayor F1** en test. Si hay empate, elige el más simple (KNN > SVM > Red Neuronal).

**Para regresión:** El que tenga **mayor R²** y menor MAE en test. Si un modelo tiene R²=0.97 en train pero R²=0.80 en test → hay overfitting → descártalo.

> [!warning] Trampa del overfitting
> Árbol de decisión sin `max_depth` tiende a sobreajustar brutalmente: R² train ≈ 1.0, R² test mucho peor. Ponle siempre `max_depth=5` o similar.

---

## PARTE 3 — Redes Neuronales

### Imports necesarios

```python
import os, random
import numpy as np
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Input
from tensorflow.keras.optimizers import Adam

# Fijar semillas para reproducibilidad
seed = 2533
os.environ['PYTHONHASHSEED'] = str(seed)
random.seed(seed)
np.random.seed(seed)
tf.random.set_seed(seed)
```

---

### Red 1 — Red lineal simple (sin funciones de activación)

```python
def crear_red_lineal(n_entradas, n_salida, learning_rate=0.01):
    model = Sequential()
    model.add(Input(shape=(n_entradas,)))
    model.add(Dense(n_salida, name="output"))  # sin activation → lineal
    
    # Para CLASIFICACIÓN BINARIA: n_salida=1, loss='binary_crossentropy', activation='sigmoid'
    # Para REGRESIÓN: n_salida=1, loss='mean_absolute_error'
    model.compile(optimizer=Adam(learning_rate=learning_rate),
                  loss='mean_absolute_error')   # ← cambiar según problema
    return model

# Crear y entrenar
model_1 = crear_red_lineal(n_entradas=X_train_std.shape[1], n_salida=1, learning_rate=0.01)
model_1.summary()  # muestra arquitectura

history_1 = model_1.fit(
    X_train_std, Y_train,
    validation_split=0.2,   # 20% del train va a validación
    batch_size=64,
    epochs=100,
    verbose=0               # verbose=2 muestra una línea por epoch
)
```

---

### Red 2 — Red con capas ocultas (no lineal)

```python
def crear_red_no_lineal(n_entradas, n_salida, learning_rate=0.01):
    model = Sequential()
    model.add(Input(shape=(n_entradas,)))
    model.add(Dense(64, activation='relu', name="capa_oculta_1"))   # capa oculta con ReLU
    model.add(Dense(32, activation='relu', name="capa_oculta_2"))   # segunda capa oculta
    model.add(Dense(n_salida, name="output"))
    
    model.compile(optimizer=Adam(learning_rate=learning_rate),
                  loss='mean_absolute_error')
    return model
```

---

### Red 3 — Red para clasificación binaria

```python
def crear_red_clasificacion(n_entradas, learning_rate=0.001):
    model = Sequential()
    model.add(Input(shape=(n_entradas,)))
    model.add(Dense(64, activation='relu'))
    model.add(Dense(32, activation='relu'))
    model.add(Dense(1, activation='sigmoid'))  # sigmoid → salida entre 0 y 1 (probabilidad)
    
    model.compile(
        optimizer=Adam(learning_rate=learning_rate),
        loss='binary_crossentropy',         # ← para clasificación binaria
        metrics=['accuracy']
    )
    return model

model_clf = crear_red_clasificacion(n_entradas=X_train_std.shape[1])
history_clf = model_clf.fit(
    X_train_std, Y_train,
    validation_split=0.2,
    batch_size=64,
    epochs=50,
    verbose=0
)

# Evaluar
pred_proba = model_clf.predict(X_test_std)           # probabilidades (entre 0 y 1)
pred_clase = (pred_proba > 0.5).astype(int).flatten() # umbral 0.5 → clase 0 o 1

acc = metrics.accuracy_score(Y_test, pred_clase)
f1  = metrics.f1_score(Y_test, pred_clase, average="weighted")
print(f"Accuracy: {acc:.4f} | F1: {f1:.4f}")
```

---

### Visualizar la evolución del entrenamiento

```python
def plot_loss_history(history, titulo="Evolución del entrenamiento"):
    import matplotlib.pyplot as plt
    import seaborn as sns
    
    loss     = history.history['loss']
    val_loss = history.history.get('val_loss', [])
    epochs   = range(1, len(loss) + 1)
    
    data_plot = pd.DataFrame({'Epoch': epochs, 'Loss': loss, 'Tipo': 'Train'})
    if val_loss:
        val_df = pd.DataFrame({'Epoch': epochs, 'Loss': val_loss, 'Tipo': 'Validation'})
        data_plot = pd.concat([data_plot, val_df])
    
    plt.figure(figsize=(10, 4))
    sns.lineplot(data=data_plot, x="Epoch", y="Loss", hue="Tipo")
    plt.title(titulo)
    plt.show()

plot_loss_history(history_1, "Red 1 — Lineal")
```

---

### Tabla comparativa final (Parte 3)

```python
# Para REGRESIÓN
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

def evaluar_red_regresion(nombre, model, X_test, Y_test):
    pred = model.predict(X_test).flatten()
    return {
        "Modelo": nombre,
        "MAE":    round(mean_absolute_error(Y_test, pred), 4),
        "MSE":    round(mean_squared_error(Y_test, pred), 4),
        "R²":     round(r2_score(Y_test, pred), 4),
    }

resultados_redes = []
for nombre, model in [
    ("Red 1 — Lineal",         model_1),
    ("Red 2 — No lineal (ReLU)", model_2),
    ("Red 3 — Clasificación",   model_3),
]:
    resultados_redes.append(evaluar_red_regresion(nombre, model, X_test_std, Y_test))

pd.DataFrame(resultados_redes)
```

---

## Funciones de activación — cuándo usar cada una

| Función | Se usa en | Rango salida | Para qué |
|---------|-----------|-------------|----------|
| **ReLU** | Capas ocultas | [0, ∞) | La más común. Introduce no linealidad. |
| **tanh** | Capas ocultas | [-1, 1] | Similar a ReLU, funciona bien en datos centrados. |
| **sigmoid** | Capa de salida (clasificación binaria) | [0, 1] | Salida como probabilidad. |
| **(ninguna)** | Capa de salida (regresión) | (-∞, ∞) | Salida libre para valores numéricos. |

> [!warning] No pongas activation en la capa de salida si es regresión
> Si pones `sigmoid` en la salida de una red de regresión, la salida solo puede estar entre 0 y 1, y no podrá predecir valores más grandes.

---

## Loss functions — cuándo usar cada una

| Problema | Loss | Código |
|---------|------|--------|
| Regresión | MAE | `loss='mean_absolute_error'` |
| Regresión | MSE | `loss='mean_squared_error'` |
| Clasificación binaria | Cross-entropy binaria | `loss='binary_crossentropy'` |
| Clasificación multiclase | Cross-entropy categórica | `loss='categorical_crossentropy'` |

---

## Resumen: receta completa para el examen

```
1. Carga de datos → ya viene hecho
2. Parte 1:
   a. Filtrar y limpiar según el ejercicio (data.loc[condición])
   b. Gráfica con seaborn
   c. Elegir X e Y → train_test_split → StandardScaler

3. Parte 2:
   a. Crear 5+ modelos (árbol, KNN, SVM, logística, etc.)
   b. Para cada uno: .fit() → .predict() → calcular métricas
   c. Montar tabla comparativa con pd.DataFrame
   d. Escribir cuál es el mejor y POR QUÉ (mayor F1/R² en test, no sobreajusta)

4. Parte 3:
   a. Crear 3 redes con arquitecturas distintas
      - Red 1: solo capa de salida (lineal)
      - Red 2: 1-2 capas ocultas con ReLU
      - Red 3: más neuronas / diferente learning_rate
   b. Para cada una: fit() con validation_split → plot_loss_history → evaluar en test
   c. Tabla comparativa
   d. Elegir la mejor
```

---

## Errores frecuentes que cuestan puntos

| Error | Consecuencia | Solución |
|-------|-------------|----------|
| Hacer `fit_transform` en X_test | Filtración de datos (data leakage) | Solo `transform()` en test |
| X_test = data_train[...] en vez de data_test | Evalúas en entrenamiento | Revisa dos veces el split |
| No estandarizar antes de KNN/SVM | El modelo funciona mal | Siempre estandariza |
| Árbol sin `max_depth` | Overfitting brutal | Añade `max_depth=5` |
| `sigmoid` en salida de red de regresión | Salida limitada a [0,1] | Sin activation en salida de regresión |
| Comparar modelos por accuracy con clases desbalanceadas | Eliges el modelo equivocado | Usa F1 weighted |

---

## Referencia rápida: imports del examen

```python
# Datos
import pandas as pd
import numpy as np

# Gráficas
import matplotlib.pyplot as plt
import seaborn as sns

# Sklearn — preprocesado
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

# Sklearn — modelos clasificación
from sklearn.dummy import DummyClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.svm import SVC
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier

# Sklearn — modelos regresión
from sklearn.dummy import DummyRegressor
from sklearn.linear_model import LinearRegression
from sklearn.tree import DecisionTreeRegressor
from sklearn.neighbors import KNeighborsRegressor
from sklearn.svm import SVR

# Sklearn — métricas clasificación
from sklearn.metrics import accuracy_score, f1_score, precision_score, recall_score

# Sklearn — métricas regresión
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

# Keras — redes neuronales
import os, random, tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Input
from tensorflow.keras.optimizers import Adam
```

---

## Conexiones con el resto del wiki

- [[sistemas-inteligentes/conceptos-clave/Overfitting-y-Underfitting]] — cómo detectar y evitar sobreajuste
- [[sistemas-inteligentes/algoritmos/Clasificacion]] — teoría de clasificación y métricas
- [[sistemas-inteligentes/algoritmos/Regresion]] — teoría de regresión
- [[sistemas-inteligentes/algoritmos/Redes-Neuronales]] — arquitecturas, optimizadores, funciones de activación
- [[sistemas-inteligentes/algoritmos/Validacion-de-Modelos]] — hold-out, K-fold, metavalidación
- [[sistemas-inteligentes/conceptos-clave/Herramientas-Python-ML]] — referencia rápida de pandas/sklearn/keras
