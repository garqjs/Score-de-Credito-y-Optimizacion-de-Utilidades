# 🚀 Optimización de Riesgo y Maximizacion de Utilidades mediante IA Explicable

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![XGBoost](https://img.shields.io/badge/Model-XGBoost-orange.svg)](https://xgboost.readthedocs.io/)
[![DuckDB](https://img.shields.io/badge/Data_Engine-DuckDB-yellow.svg)](https://duckdb.org/)
[![Kaggle](https://img.shields.io/badge/Dataset-Kaggle-blue.svg)](https://www.kaggle.com/competitions/home-credit-default-risk/data)


## 💡 Introducción:
Tradicionalmente, los modelos de riesgo bancario se han basado en una visión **estática y retrospectiva**, evaluando la solvencia del cliente a través de su historial acumulado sin considerar cambios recientes en su salud financiera. 

Este proyecto rompe con dicho enfoque al implementar una arquitectura de **"Early Warning Signals"**. A diferencia de los modelos tradicionales, nuestro motor no solo predice la probabilidad de impago, sino que **monitorea activamente la velocidad de deterioro** de los clientes mediante series temporales de comportamiento de pago. Al combinar 4 fuentes de datos transaccionales con restricciones de monotonicidad regulatoria y optimización de rentabilidad (P&L), pasamos de una simple clasificación de riesgo a una **estrategia de negocio orientada a la maximización de utilidades**.

---

## 📊 Arquitectura del Proyecto y Data Pipeline
El modelo integra 4 fuentes de datos transaccionales para capturar una visión 360° del cliente:

* **`application_train.csv`**: Datos demográficos y financieros base.
* **`bureau.csv`**: Historial crediticio externo y niveles de deuda consolidada.
* **`installments_payments.csv`**: Comportamiento detallado de pagos (cronología de atrasos).
* **`previous_application.csv`**: Experiencia previa y tasa de aprobación interna.

---

## 🛠️ Feature Store: Ingeniería de Variables Críticas
Más allá de los datos crudos, el diferenciador técnico radica en la creación de variables con lógica de negocio:

| Variable | Traducción / Descripción | Impacto Estratégico |
| :--- | :--- | :--- |
| **DELTA_ATRASO** | Delta de Atraso | Mide la aceleración del deterioro (Atraso actual vs. Histórico). |
| **RATIO_APALANCAMIENTO** | Ratio de Apalancamiento | Deuda total / Ingresos. Detecta saturación financiera. |
| **RATIO_CARGA_CUOTA** | Ratio de Carga de Cuota | % del ingreso mensual destinado al servicio de la deuda. |
| **CUMPLIMIENTO_RECENT** | Cumplimiento (3M) | % de cuota realmente pagada en el último trimestre. |


## 📈 Resultados Finales

### Desempeño Estadístico
* **Gini**: `0.5007`
* **KS Statistic**: `0.3731`
* **Umbral de Corte**: `49.9%`

<img width="848" height="541" alt="image" src="https://github.com/user-attachments/assets/c45b3ff8-87b6-4fd6-898f-3588a10840c2" />


### Optimización Financiera (P&L)
El modelo no solo busca precisión, busca **rentabilidad**. Proyección sobre la cartera de validación:

| Concepto | Monto (MXN/USD) |
| :--- | :--- |
| **[+] Intereses Ganados (Sanos)** | + $3,731,499,888.53 |
| **[-] Capital Perdido (LGD)** | - $580,213,567.80 |
| 🚀 **BENEFICIO NETO MAXIMIZADO** | **$3,151,286,320.73** |

> **🛡️ Capital Salvado (Fraude/Default Evitado):** $1,083,715,240.50  
> **📉 Costo de Oportunidad (Falsos Positivos):** $1,398,544,589.92

   <img width="881" height="541" alt="image" src="https://github.com/user-attachments/assets/cf75c33c-c8e1-4fed-a02d-211652abdd35" />


## 🎯 Impacto Operativo (Matriz de Confusión)
Bajo la política de riesgo de **Umbral > 50%**:

* ✅ **Sanos Aprobados (TN):** 39,412 (Generadores de flujo).
* ❌ **Default Evitado (TP):** 3,346 (Capital protegido).
* ⚠️ **Falsos Positivos (FP):** 17,126 (Clientes rechazados por precaución).
* 🩸 **Falsos Negativos (FN):** 1,619 (Fugas de riesgo - LGD).

  <img width="720" height="541" alt="image" src="https://github.com/user-attachments/assets/769e561d-e0df-4b98-9303-83eb0fd42929" />


---

## 🧠 Explicabilidad del Modelo (SHAP Values)

Para garantizar que el modelo sea **auditable y libre de sesgos**, implementamos **SHAP (Shapley Additive Explanations)**. Esta técnica de Game Theory permite descomponer la predicción del modelo y entender exactamente cómo cada variable suma o resta al riesgo final del cliente.

### 🔍 Glosario de Impacto SHAP

| Variable | Interpretación del Valor SHAP | Lógica de Riesgo Bancario |
| :--- | :--- | :--- |
| **EXT_SOURCE 1, 2, 3** | Impacto dominante y negativo. | El puntaje externo sigue siendo el filtro principal de solvencia. |
| **Años de Empleado** | Valores bajos (azul) desplazan el riesgo a la derecha. | La falta de antigüedad laboral es un driver crítico de inestabilidad. |
| **Tasa Rechazo Interno** | Puntos rojos a la derecha aumentan el riesgo. | Clientes con historial de rechazos previos tienen mayor propensión al default. |
| **Promedio Días Atraso Hist.** | Concentración roja en la zona de riesgo. | El comportamiento de pago pasado es el mejor predictor del futuro. |
| **Edad (Años)** | Dispersión variada. | Permite segmentar el riesgo por etapas de vida y estabilidad patrimonial. |
| **Monto Crédito** | Puntos rojos desplazados del centro. | Valida la necesidad de monitorear el sobreendeudamiento. |
| **Flag Own Car / Gender** | Impactos marginales pero consistentes. | Actúan como variables de control para el perfil sociodemográfico. |

<img width="769" height="555" alt="image" src="https://github.com/user-attachments/assets/121bb815-709c-4d81-b5ef-72175b94f3c4" />

### 🏆 Hallazgos Clave de Explicabilidad

1. **El Peso del Historial Interno:** La aparición de `tasa_rechazo_interno` y `promedio_dias_atraso_hist` entre las variables de mayor impacto demuestra que el modelo ahora prioriza la **lealtad y el comportamiento del cliente** con nuestra propia institución, permitiendo una cobranza más preventiva.
2. **Estabilidad vs. Oportunidad:** El factor `años_empleado` muestra una frontera clara; el modelo penaliza severamente la volatilidad laboral, lo que sugiere que nuestra política de "Early Warning" debe enfocarse en clientes con menos de 2 años en su puesto actual.
3. **Refinamiento del P&L:** Al entender cómo la `edad` y la `tasa_rechazo` afectan el Score, podemos ajustar las tasas de interés de forma personalizada (Risk-Based Pricing), maximizando el margen en segmentos identificados como "estables" por el análisis SHAP.


## 🏁 Conclusión: Del Diagnóstico de IA a la Rentabilidad Bancaria

La integración de una arquitectura de **Early Warning Signals (EWS)** con un modelo explicable de 10 variables ha transformado la gestión de riesgo de este portafolio. No solo hemos construido un clasificador preciso, sino una herramienta de decisión estratégica.

### 💎 El Vínculo SHAP - P&L (Profit & Loss)

El análisis de **Explicabilidad (SHAP)** revela que el éxito financiero de **$3,151 millones en Beneficio Neto** no es accidental, sino el resultado de tres pilares operativos:

1. **Blindaje de Capital ($1,083M Salvados):** Gracias a la alta importancia de variables como `Tasa de Rechazo Interno` y `Promedio de Días de Atraso`, el modelo identificó con éxito a los perfiles de alto riesgo (TP), evitando pérdidas por LGD que los modelos estáticos tradicionales habrían ignorado.
2. **Maximización de Ingresos ($3,731M Generados):** Al validar mediante SHAP que los clientes con altos puntajes en `EXT_SOURCE` y estabilidad en `Años de Empleado` tienen un riesgo residual mínimo, la institución pudo aprobar con confianza la colocación de capital en segmentos altamente rentables.
3. **Eficiencia en la Política de Riesgo:** El umbral de **49.9%** fue calibrado para equilibrar el costo de oportunidad de los Falsos Positivos con la seguridad del capital, logrando un **Gini de 0.5007** que garantiza una separación de clases robusta y auditable.

### 🚀 Visión de Negocio Final

Este proyecto demuestra que la **Ciencia de Datos** aplicada a la banca no debe ser una "caja negra". Al alinear los **Drivers de Contactabilidad** y comportamiento crediticio con las metas de rentabilidad, pasamos de un enfoque defensivo a uno ofensivo, donde cada punto de probabilidad predicho tiene un valor monetario real. 

La implementación de este modelo asegura una cartera **sana, auditable ante reguladores y, sobre todo, altamente competitiva** en un entorno financiero dinámico.

---

## 🛠️ Retos Técnicos y Soluciones

Durante el desarrollo de este pipeline, se enfrentaron desafíos críticos que requirieron soluciones de arquitectura avanzada:

1. **Gestión de la Complejidad de Datos (4 Fuentes):** - **Reto:** Unificar datos demográficos, historial de Bureau y cronología de pagos sin generar "Data Leakage" (fuga de datos).
   - **Solución:** Se diseñó un proceso de agregación temporal en **DuckDB** que asegura que el modelo solo aprenda de eventos pasados respecto a la fecha de solicitud del crédito.

2. **Decisiones Contra-intuitivas:**
   - **Reto:** Evitar que el modelo asignara menor riesgo a clientes con mayor deuda (anomalías estadísticas).
   - **Solución:** Implementación de **Restricciones Monotónicas** en XGBoost. Esto garantiza que el Score respete la lógica económica (a mayor deuda, mayor riesgo), haciendo al modelo auditable ante reguladores financieros.

3. **Interpretación de "Caja Negra":**
   - **Reto:** Explicar por qué se rechazó un crédito específico.
   - **Solución:** Integración de **SHAP Values** a nivel individual. Cada predicción cuenta con "Reason Codes" claros que identifican qué variables (como el Delta de Atraso o el Ratio de Apalancamiento) influyeron en el resultado.

