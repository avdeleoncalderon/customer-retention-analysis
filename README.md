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
