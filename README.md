# 🚀 Score Riesgo Crediticio & Ajuste de Optimizacion

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![XGBoost](https://img.shields.io/badge/Model-XGBoost-orange.svg)](https://xgboost.readthedocs.io/)
[![DuckDB](https://img.shields.io/badge/Data_Engine-DuckDB-yellow.svg)](https://duckdb.org/)
[![Kaggle](https://img.shields.io/badge/Dataset-Kaggle-blue.svg)](https://www.kaggle.com/competitions/home-credit-default-risk/data)


## 💡 Introducción:
Tradicionalmente, los modelos de riesgo bancario se han basado en una visión **estática y retrospectiva**, evaluando la solvencia del cliente a través de su historial acumulado sin considerar cambios recientes en su salud financiera. 

Este proyecto rompe con dicho enfoque al implementar una arquitectura de **"Early Warning Signals"**. A diferencia de los modelos tradicionales, nuestro motor no solo predice la probabilidad de impago, sino que **monitorea activamente la velocidad de deterioro** de los clientes mediante series temporales de comportamiento de pago. Al combinar 4 fuentes de datos transaccionales con restricciones de monotonicidad regulatoria y optimización de rentabilidad (P&L), pasamos de una simple clasificación de riesgo a una **estrategia de negocio orientada a la maximización de utilidades**.

---

## 📊 Arquitectura del Proyecto
El modelo procesa de forma integrada 4 datasets clave para capturar la realidad financiera del cliente:
1. `application_train.csv`: Datos demográficos y financieros base.
2. `bureau.csv`: Historial crediticio externo y niveles de deuda.
3. `installments_payments.csv`: Comportamiento de pago detallado (atrasos y cumplimiento).
4. `previous_application.csv`: Experiencia previa con la institución.

---

## 🛠 Ingeniería de Características (Diferenciador Técnico)
Más allá de utilizar las variables crudas, implementamos lógica de negocio crítica:

### 📖 Glosario de Variables Predictoras (Feature Store)
| Variable | Traducción / Descripción | Impacto en el Modelo |
| :--- | :--- | :--- |
| `DELTA_ATRASO` | **Delta de Atraso** | Diferencia entre el atraso actual y el histórico. Indica deterioro. |
| `RATIO_APALANCAMIENTO` | **Ratio de Apalancamiento** | Deuda total sobre ingresos. Mide la saturación financiera. |
| `RATIO_CARGA_CUOTA` | **Ratio de Carga de Cuota** | Porcentaje del ingreso mensual destinado a pagar la cuota. |
| `CUMPLIMIENTO_RECIENTE` | **Cumplimiento Reciente (3M)** | Porcentaje de la cuota realmente pagada en los últimos 3 meses. |

---

## 📈 Resultados Finales

### 1. Desempeño Estadístico
* **Gini**: `0.5007`
* **KS Statistic**: `0.3731` @ Umbral `49.9%`

<img width="848" height="541" alt="image" src="https://github.com/user-attachments/assets/c45b3ff8-87b6-4fd6-898f-3588a10840c2" />


### 2. Optimización Financiera (Profit & Loss)
* **Beneficio Neto Maximizado**: `$3,107,153,318.17`
* **Capital Salvado (Fraude Evitado)**: `$1,051,806,027.60`

<img width="881" height="541" alt="image" src="https://github.com/user-attachments/assets/cf75c33c-c8e1-4fed-a02d-211652abdd35" />


### 3. Impacto Operativo

🎯 INTERPRETACIÓN DE LA MATRIZ DE CONFUSIÓN

  Bajo la política de rechazar a clientes con Riesgo > 50.0%:
  
  ✅ Sanos Aprobados (TN): 39,412 -> Clientes que generan ingresos por intereses.
  
  ❌ Fraude Evitado (TP): 3,346 -> Morosos bloqueados. Capital protegido.
  
  ⚠️ Falsos Positivos (FP): 17,126 -> Buenos clientes rechazados (Costo de oportunidad).
  
  🩸 Falsos Negativos (FN): 1,619 -> Morosos que se filtraron (Pérdida real de LGD).

---

## 🧠 Explicabilidad (SHAP)
Utilizamos **SHAP** para asegurar la transparencia, evitando el fenómeno de "caja negra":

<img width="760" height="435" alt="image" src="https://github.com/user-attachments/assets/533576c4-17ad-49a0-b14e-52a0837db4e1" />


**Hallazgo:** La combinación de variables de comportamiento (`DELTA_ATRASO`) junto con restricciones de **monotonicidad** garantiza que el modelo actúe conforme a la lógica financiera bancaria, haciendo que las decisiones sean auditables y explicables.
