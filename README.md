# Score Riesgo Crediticio: Optimización con XGBoost

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![XGBoost](https://img.shields.io/badge/Model-XGBoost-orange.svg)](https://xgboost.readthedocs.io/)
[![DuckDB](https://img.shields.io/badge/Data_Engine-DuckDB-yellow.svg)](https://duckdb.org/)
[![Kaggle](https://img.shields.io/badge/Dataset-Kaggle-blue.svg)](https://www.kaggle.com/competitions/home-credit-default-risk/data)

### 📂 Descripción del Proyecto
Este proyecto simula un entorno de producción bancaria para predecir el default de clientes. Se utilizaron datos de aplicaciones, historial en Buró de Crédito, pagos previos y rechazos internos para construir un modelo de scoring robusto.

### 📊 Origen de los Datos
El dataset utilizado es el **Home Credit Default Risk**, el cual presenta un desafío de clasificación altamente desbalanceado con una estructura relacional de cuatro tablas:

- **application_{train|test}**: Tabla principal con datos de la solicitud.
- **bureau**: Historial del cliente en otras instituciones financieras.
- **previous_application**: Solicitudes previas dentro de la misma entidad.
- **installments_payments**: Historial de pagos detallado.

### 🛠️ Tech Stack
Data Engine: DuckDB (SQL OLAP).

Modelado: XGBoost con monotone_constraints (Restricciones de negocio).

Explicabilidad: SHAP (Valores de Shapley para transparencia).

Validación: Scikit-learn (Métricas KS, Gini y AUC).

### 🏗️ Arquitectura del Sistema y Flujo de Datos

Para este proyecto se implementó una arquitectura basada en el **Medallion Pattern**, asegurando que el procesamiento de datos sea eficiente, escalable y auditable utilizando **DuckDB** para un manejo eficiente de memoria.

### 🔄 Etapas del Pipeline:

1.  **Capa de Ingesta (Bronze):** Extracción de datos crudos desde cuatro fuentes principales.
2.  **Capa de Transformación (Silver):** Creación de **Vistas SQL** donde se ejecutan agregaciones complejas, limpieza de nulos y normalización de registros.
3.  **Capa de Negocio (Gold):** Materialización de la tabla maestra enriquecida con **Ingeniería de Variables** (Ratios de Apalancamiento y Fricción de Pago).
4.  **Capa de Inteligencia:** Entrenamiento e inferencia mediante **XGBoost** con restricciones monotónicas, garantizando que el modelo respete las reglas de negocio bancarias.
5.  **Capa de Explicabilidad:** Auditoría de decisiones mediante valores **SHAP**, permitiendo una interpretación clara de los factores de riesgo para cada cliente.

### 📖 Diccionario de Datos

Para asegurar la transparencia y la interpretabilidad del modelo de scoring, se detalla a continuación el significado de las variables críticas utilizadas, organizadas por su origen y lógica de negocio.

<details>
<summary><b>1. Tabla Principal y Variables Socioeconómicas (Application)</b></summary>

| Variable | Traducción | Descripción Técnica |
| :--- | :--- | :--- |
| **SK_ID_CURR** | ID de Solicitud | Identificador único del préstamo (Primary Key). |
| **TARGET** | Objetivo (Default) | Variable a predecir. 1: Cliente con mora (+90 días), 0: Cliente sano. |
| **AMT_INCOME_TOTAL** | Ingresos Totales | Ingreso anual total declarado por el solicitante. |
| **AMT_CREDIT** | Monto del Crédito | Monto total del préstamo solicitado por el cliente. |
| **AMT_ANNUITY** | Anualidad | Monto de la cuota mensual/anual a pagar. |
| **age_years** | Edad (Años) | Edad calculada del cliente al momento de la solicitud. |
| **years_employed** | Antigüedad Laboral | Tiempo (en años) que el cliente lleva en su empleo actual. |
| **EXT_SOURCE_2 / 3** | Score Externo 2 / 3 | Puntajes de riesgo normalizados provenientes de fuentes externas. |

</details>

<details>
<summary><b>2. Historial de Buró y Pagos (Bureau & Installments)</b></summary>

| Variable | Traducción | Descripción Técnica |
| :--- | :--- | :--- |
| **total_external_debt** | Deuda Externa Total | Suma de todas las deudas vigentes en otras instituciones financieras. |
| **max_external_overdue** | Mora Externa Máxima | Máximo monto que el cliente tiene actualmente vencido en el buró. |
| **active_loan_count** | Créditos Activos | Número de créditos abiertos actualmente en otras entidades. |
| **avg_payment_ratio** | Ratio de Pago | Promedio del monto pagado vs. el monto facturado (Voluntad de pago). |
| **avg_days_late** | Promedio Días de Mora | Diferencia promedio de días entre la fecha de pago real y la programada. |

</details>

<details>
<summary><b>3. Historial Interno (Previous Applications)</b></summary>

| Variable | Traducción | Descripción Técnica |
| :--- | :--- | :--- |
| **total_prev_apps** | Total Solicitudes Previas | Cantidad de veces que el cliente ha solicitado crédito en nuestra entidad. |
| **internal_rejection_rate** | Tasa de Rechazo Interno | Porcentaje de solicitudes previas que fueron rechazadas por la institución. |
| **total_prev_annuity** | Carga de Anualidad Previa | Suma de las cuotas de préstamos anteriores con la misma entidad. |

</details>

<details>
<summary><b>4. Ingeniería de Variables (Features de Negocio / Gold Layer)</b></summary>

| Variable | Concepto | Lógica de Riesgo (Monotone Constraint) |
| :--- | :--- | :--- |
| **RATIO_GLOBAL_LEVERAGE** | Apalancamiento Global | (Deuda + Crédito) / Ingresos. A mayor ratio, mayor riesgo (**+1**). |
| **SCORE_PAYMENT_FRICTION** | Fricción de Pago | Score que penaliza la mora externa y retrasos internos (**+1**). |
| **RATIO_ANNUITY_INCOME** | Carga Financiera | Proporción de la cuota respecto al ingreso mensual (**+1**). |

</details>

### 📊 Resultados y Validación

En esta sección se presentan las métricas de desempeño del modelo XGBoost.

### Curva KS (Kolmogorov-Smirnov)
El modelo alcanzó un estadístico KS de **0.3466**, lo que indica una sólida capacidad de separación entre clientes sanos y morosos.

<img src="./KS.png" width="500">

### Explicabilidad con SHAP
Para garantizar la transparencia del modelo (Explainable AI), se utilizaron valores SHAP para identificar los factores que más influyen en el riesgo. Se observa que los scores externos y el historial de rechazos internos son los predictores más potentes.

<img src="./SHAP.png" width="500">

### 🔝 Importancia de Variables (Gain)
Esta gráfica identifica los predictores con mayor impacto en la reducción de la entropía del modelo. Se observa una dominancia de los scores externos y los ratios de apalancamiento financiero generados mediante ingeniería de variables.

<img src="./Importancia_variables.png" width="500">

### 📉 Matriz de Confusión y Punto de Corte Óptimo
Para determinar el umbral de decisión, se utilizó el **Estadístico KS**, estableciendo un punto de corte de **0.488**. Este umbral permite maximizar la rentabilidad del portafolio al equilibrar la aprobación de clientes sanos y la detección de posibles impagos.

<img src="./MatrizConfusión.png" width="500">


### Impacto de Negocio (Matriz de Confusión)
Al aplicar el **umbral óptimo (0.488)**, los resultados en el set de prueba son:

* **🛡️ Defaults Prevenidos** **3,358** (Verdaderos Positivos): Casos de alto riesgo bloqueados con éxito.
* **✅ Créditos Aprobados** **37,896** (Verdaderos Negativos): Clientes sanos que generan ingresos por intereses.
* **⚠️ Costo de Oportunidad**	**18,642** (Falsos Positivos): Clientes sanos rechazados preventivamente.
* **❌ Riesgo Residual**	**1,607** (Falsos Negativos): Morosos no detectados (Pérdida directa).


### 📉 KPIs de Desempeño Final
| Métrica | Valor |
| :--- | :--- |
| **AUC Score** | 0.7313 |
| **Gini Coefficient** | 0.4632 |
| **Estadístico KS** | 0.3466 |
| **Recall (Tasa de Captura de Morosos)** | 67.6% |
