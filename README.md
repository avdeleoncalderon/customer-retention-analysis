# Análisis de Retención de Clientes — Model Fitness 🏋️

## Objetivo del proyecto

Model Fitness, una cadena de gimnasios, necesitaba **anticiparse a la cancelación de membresías** antes de que ocurriera. Sin visibilidad sobre qué clientes estaban en riesgo, el equipo de marketing operaba de forma reactiva y perdía clientes que podrían haberse retenido con intervenciones tempranas.

**Pregunta de negocio:** ¿Qué factores predicen la cancelación de un cliente, y cómo podemos segmentar a los usuarios para diseñar estrategias de retención personalizadas?

---

## Fuente de datos

**Dataset:** Proporcionado por TripleTen como parte del sprint de Machine Learning del bootcamp de Análisis de Datos. Los datos simulan registros reales de un gimnasio; no contienen información personal identificable.

**Estructura:** 2 archivos CSV (`gym_churn_us.csv`) — datos del mes actual de cada cliente y datos de comportamiento del mes anterior.

**Tamaño:** ~4,000 registros de clientes.

**Variables incluidas:**

| Variable | Descripción |
|---|---|
| `Churn` | Variable objetivo: 1 = canceló, 0 = permaneció |
| `gender` | Género del cliente |
| `Near_Location` | Vive o trabaja cerca del gimnasio |
| `Partner` | Trabaja en empresa asociada al gimnasio |
| `Promo_friends` | Se inscribió por referido de un amigo |
| `Phone` | Proporcionó número de teléfono |
| `Age` | Edad del cliente |
| `Lifetime` | Meses desde que se inscribió por primera vez |
| `Contract_period` | Duración del contrato: 1, 3, 6 o 12 meses |
| `Month_to_end_contract` | Meses restantes para que expire el contrato |
| `Group_visits` | Participa en sesiones grupales |
| `Avg_class_frequency_total` | Promedio de visitas/semana en toda su historia |
| `Avg_class_frequency_current_month` | Promedio de visitas/semana en el mes actual |
| `Avg_additional_charges_total` | Gasto total en servicios adicionales (cafetería, masajes, etc.) |

---

## Proceso

### 1. Limpieza y preparación de datos
- Verificación de valores nulos y duplicados → el dataset estaba limpio, sin registros faltantes.
- Validación de tipos de datos y rangos (ej. edades entre 18 y 65, periodos de contrato solo con valores permitidos: 1, 3, 6, 12).
- Sin necesidad de imputación; se documentaron las suposiciones sobre valores en 0 para visitas (clientes inactivos ese mes, no datos perdidos).

### 2. Análisis exploratorio (EDA)
- Distribuciones de variables numéricas segmentadas por `Churn = 0` vs `Churn = 1`.
- Matrices de correlación para identificar multicolinealidad (hallazgo clave: `month_to_end_contract` y `contract_period` tienen correlación de 0.90).
- Tasas de cancelación por subgrupo (por tipo de contrato, cercanía, participación en grupales).

### 3. Modelado predictivo
- **Modelos entrenados:** Regresión Logística y Random Forest (Bosque Aleatorio).
- **División:** 80% entrenamiento / 20% prueba, con estratificación por `Churn`.
- **Métricas evaluadas:** Accuracy, Precision, Recall y AUC-ROC.

| Modelo | Accuracy | Precision | Recall | AUC |
|---|---|---|---|---|
| Regresión Logística | 85% | 0.83 | 0.80 | 0.91 |
| Random Forest | 84% | 0.82 | 0.81 | 0.92 |

### 4. Segmentación con clustering (K-Means)
- Se normalizaron las variables antes del clustering.
- El dendrograma sugirió **4 clústeres óptimos**.
- Cada clúster fue caracterizado por comportamiento de uso, duración de contrato y tasa de cancelación.

---

## Resultados e insights clave

**1. El contrato corto es el mayor predictor de cancelación.**
Los clientes con contratos de 1 mes cancelan a una tasa significativamente mayor que quienes tienen contratos de 6 o 12 meses. Extender contratos desde el inicio reduce el riesgo.

**2. La frecuencia de visitas distingue a los que se quedan de los que se van.**
Los clientes que cancelan visitan el gimnasio con menor frecuencia en el mes previo a su cancelación. Esto crea una ventana de intervención: una caída en visitas es una señal de alerta accionable.

**3. Las sesiones grupales actúan como ancla de retención.**
Los clientes que participan en clases grupales tienen tasas de cancelación notablemente menores, posiblemente por el componente social y el compromiso con horarios fijos.

**4. La cercanía geográfica importa.**
Los clientes que viven o trabajan cerca del gimnasio cancelan menos — la fricción de distancia es un factor real de abandono.

---

## Recomendaciones estratégicas

1. **Incentivos para extender contratos cortos:** Ofrecer descuento del 15–20% al pasar de 1 mes a 6 o 12 meses, especialmente en el momento de inscripción.
2. **Programa de alerta temprana:** Usar el modelo predictivo en producción: si un cliente cae por debajo de su frecuencia histórica de visitas, activar contacto proactivo (mensaje, oferta, llamada).
3. **Campaña de sesiones grupales:** Diseñar una campaña de onboarding que lleve a todos los clientes nuevos a al menos una clase grupal en su primer mes.
4. **Segmentación proactiva:** Al registrar un cliente nuevo, clasificarlo en el clúster más cercano para personalizar la estrategia de retención desde el día uno.

**Si esto fuera una tarea laboral real:** el siguiente paso sería implementar el modelo en el CRM del gimnasio para generar scores de riesgo semanales por cliente, y medir el impacto de las campañas con un experimento A/B.

---

## Cómo ejecutar este proyecto

### Requisitos
```
Python 3.9+
pandas
numpy
matplotlib
seaborn
scikit-learn
scipy
```

Instalar dependencias:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy
```

### Pasos
```bash
# 1. Clona el repositorio
git clone https://github.com/avdeleoncalderon/customer-retention-analysis

# 2. Entra al directorio
cd customer-retention-analysis

# 3. Abre el notebook
jupyter notebook "Customer Retention Analysis.ipynb"
```

> El dataset (`gym_churn_us.csv`) está incluido en la carpeta `datasets/`. No se requiere descarga adicional.

---

## Herramientas y tecnologías

![Python](https://img.shields.io/badge/python-357ebd?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/pandas-%23357ebd.svg?style=for-the-badge&logo=pandas&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-357ebd?style=for-the-badge)
![scikit-learn](https://img.shields.io/badge/scikit--learn-%23357ebd.svg?style=for-the-badge&logo=scikit-learn&logoColor=white)

**Técnicas:** Limpieza de datos · EDA · Regresión Logística · Random Forest · K-Means Clustering · Visualización de datos

---

## Sobre el autor

**Ari Vladimir** — Ingeniero mecánico en transición a Data Analytics.
- 🔗 [LinkedIn](https://www.linkedin.com/in/ari-vladimir/)
- 🌐 [Portafolio](https://avdeleoncalderon.github.io/)
- 📧 av.deleoncalderon@gmail.com

---

# Customer Retention Analysis — Model Fitness 🏋️

## Project Objective

Model Fitness, a gym chain, needed to **anticipate membership cancellations** before they happened. Without visibility into which customers were at risk, the marketing team operated reactively and lost customers who could have been retained with early interventions.

**Business question:** What factors predict a customer's cancellation, and how can we segment users to design personalized retention strategies?

---

## Data Source

**Dataset:** Provided by TripleTen as part of the Machine Learning sprint in the Data Analysis bootcamp. The data simulates real gym records; it contains no personally identifiable information.

**Structure:** 2 CSV files (`gym_churn_us.csv`) — current month data for each customer and previous month behavioral data.

**Size:** ~4,000 customer records.

**Variables included:**

| Variable | Description |
|---|---|
| `Churn` | Target variable: 1 = canceled, 0 = stayed |
| `gender` | Customer's gender |
| `Near_Location` | Lives or works near the gym |
| `Partner` | Works at a company partnered with the gym |
| `Promo_friends` | Signed up through a friend referral |
| `Phone` | Provided phone number |
| `Age` | Customer's age |
| `Lifetime` | Months since first signing up |
| `Contract_period` | Contract length: 1, 3, 6, or 12 months |
| `Month_to_end_contract` | Months remaining until contract expires |
| `Group_visits` | Participates in group sessions |
| `Avg_class_frequency_total` | Average visits/week across entire history |
| `Avg_class_frequency_current_month` | Average visits/week in current month |
| `Avg_additional_charges_total` | Total spending on additional services (cafeteria, massages, etc.) |

---

## Process

### 1. Data Cleaning and Preparation
- Checked for null values and duplicates → the dataset was clean, with no missing records.
- Validated data types and ranges (e.g., ages between 18 and 65, contract periods only allowed values: 1, 3, 6, 12).
- No imputation needed; documented assumptions about zeros in visit counts (inactive customers that month, not missing data).

### 2. Exploratory Data Analysis (EDA)
- Distributions of numerical variables segmented by `Churn = 0` vs `Churn = 1`.
- Correlation matrices to identify multicollinearity (key finding: `month_to_end_contract` and `contract_period` have 0.90 correlation).
- Cancellation rates by subgroup (by contract type, proximity, group participation).

### 3. Predictive Modeling
- **Models trained:** Logistic Regression and Random Forest.
- **Split:** 80% training / 20% test, stratified by `Churn`.
- **Metrics evaluated:** Accuracy, Precision, Recall, and AUC-ROC.

| Model | Accuracy | Precision | Recall | AUC |
|---|---|---|---|---|
| Logistic Regression | 85% | 0.83 | 0.80 | 0.91 |
| Random Forest | 84% | 0.82 | 0.81 | 0.92 |

### 4. Segmentation with Clustering (K-Means)
- Variables were normalized before clustering.
- The dendrogram suggested **4 optimal clusters**.
- Each cluster was characterized by usage behavior, contract length, and cancellation rate.

---

## Results and Key Insights

**1. Short contract is the strongest predictor of cancellation.**
Customers with 1-month contracts cancel at a significantly higher rate than those with 6 or 12-month contracts. Extending contracts from the start reduces risk.

**2. Visit frequency distinguishes those who stay from those who leave.**
Customers who cancel visit the gym less frequently in the month prior to cancellation. This creates an intervention window: a drop in visits is an actionable warning sign.

**3. Group sessions act as a retention anchor.**
Customers who participate in group classes have notably lower cancellation rates, possibly due to the social component and commitment to fixed schedules.

**4. Geographic proximity matters.**
Customers who live or work near the gym cancel less — distance friction is a real churn factor.

---

## Strategic Recommendations

1. **Incentives to extend short contracts:** Offer a 15–20% discount when moving from 1 month to 6 or 12 months, especially at sign-up time.
2. **Early warning program:** Deploy the predictive model in production — if a customer falls below their historical visit frequency, trigger proactive outreach (message, offer, call).
3. **Group session campaign:** Design an onboarding campaign that gets every new customer to at least one group class in their first month.
4. **Proactive segmentation:** When a new customer registers, classify them into the nearest cluster to personalize retention strategy from day one.

**If this were a real job task:** the next step would be to implement the model in the gym's CRM to generate weekly risk scores per customer, and measure campaign impact with an A/B test.

---

## How to Run This Project

### Requirements
```
Python 3.9+
pandas
numpy
matplotlib
seaborn
scikit-learn
scipy
```

Install dependencies:
```
pip install pandas numpy matplotlib seaborn scikit-learn scipy


# 1. Clone the repository
git clone https://github.com/avdeleoncalderon/customer-retention-analysis

# 2. Enter the directory
cd customer-retention-analysis

# 3. Open the notebook
jupyter notebook "Customer Retention Analysis.ipynb"

```

> The dataset (gym_churn_us.csv) is included in the datasets/ folder. No additional download required.

---

## Tools and Technologies

![Python](https://img.shields.io/badge/python-357ebd?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/pandas-%23357ebd.svg?style=for-the-badge&logo=pandas&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-357ebd?style=for-the-badge)
![scikit-learn](https://img.shields.io/badge/scikit--learn-%23357ebd.svg?style=for-the-badge&logo=scikit-learn&logoColor=white)

**Techniques:** Data Cleaning · EDA · Logistic Regression · Random Forest · K-Means Clustering · Data Visualization


---

## About the Author

**Ari Vladimir** — Mechanical Engineer transitioning to Data Analytics.
- 🔗 [LinkedIn](https://www.linkedin.com/in/ari-vladimir/)
- 🌐 [Portafolio](https://avdeleoncalderon.github.io/)
- 📧 av.deleoncalderon@gmail.com
