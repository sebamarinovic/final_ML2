# Proyecto Final – Machine Learning II  
## Predicción de Vida Útil Remanente (RUL) en Motores

**Curso:** Machine Learning II  
**Tipo de problema:** Aprendizaje supervisado – Regresión  
**Dataset:** NASA C-MAPSS (FD001)  
**Autores:** [Completar integrantes]  

---

## 1. Descripción del proyecto

Este proyecto aborda el problema de **predicción de la Vida Útil Remanente (Remaining Useful Life, RUL)** de motores, utilizando datos multivariados de sensores obtenidos durante su operación hasta la falla.  

El objetivo principal es desarrollar y comparar distintos modelos de **Machine Learning supervisado** capaces de estimar el número de ciclos restantes antes de la falla, apoyando la **toma de decisiones en mantenimiento predictivo** y la planificación operacional.

El proyecto sigue una metodología completa que incluye:
- Análisis exploratorio de datos (EDA)
- Preprocesamiento y ingeniería de características
- Entrenamiento y ajuste de modelos
- Evaluación comparativa
- Análisis crítico y propuesta de despliegue

---

## 2. Dataset y problemática

El dataset utilizado corresponde al **NASA C-MAPSS FD001**, ampliamente utilizado en investigación de mantenimiento predictivo. Contiene:

- **100 motores** en entrenamiento y **100 motores** en test
- Series temporales multivariadas de sensores
- Trayectorias completas desde condición saludable hasta falla

La variable objetivo **RUL** se encuentra truncada en un valor máximo de 125 ciclos, lo que genera una distribución no uniforme y añade complejidad al proceso de modelamiento.

---

## 3. Análisis exploratorio y preparación de datos

Durante el EDA se identificaron los siguientes aspectos clave:

- Alta variabilidad en los sensores, asociada a la degradación progresiva
- Variables de configuración con varianza prácticamente nula
- Distribución asimétrica del RUL debido al truncamiento

Para capturar la dinámica temporal del proceso de degradación, se aplicó una estrategia de **ventanas temporales deslizantes**, transformando las series de tiempo en vectores de características de alta dimensión.

### Dimensiones finales:
- Muestras de entrenamiento: **14.241**
- Muestras de validación: **3.490**
- Número de características por muestra: **720**

La separación entre entrenamiento y validación se realizó **por motor**, evitando fuga de información y asegurando una evaluación realista de la generalización.

---

## 4. Modelos implementados

Se entrenaron y compararon tres modelos de regresión supervisada:

1. **Ridge Regression**  
   - Modelo lineal regularizado  
   - Utilizado como baseline

2. **Random Forest Regressor**  
   - Modelo no lineal basado en ensambles  
   - Captura interacciones entre variables

3. **Gradient Boosting Regressor**  
   - Modelo secuencial de boosting  
   - Alta capacidad para modelar patrones complejos de degradación

El ajuste de hiperparámetros se realizó mediante **Grid Search con validación cruzada**.

---

## 5. Métricas y resultados obtenidos
## Resultados y visualizaciones clave

### Distribución del RUL en el conjunto de entrenamiento

![Distribución del RUL](outputs/figures/rul_distribution_train.png)

La distribución del RUL muestra una alta concentración de valores en el máximo (125 ciclos), producto del truncamiento aplicado al dataset. Este comportamiento introduce una asimetría artificial que incrementa la complejidad del problema y refuerza la necesidad de modelos no lineales capaces de distinguir entre estados saludables y etapas avanzadas de degradación.

---

### Comparación del desempeño de modelos (MAE)

![Comparación MAE modelos](outputs/figures/mae_comparison_models.png)

La comparación de MAE evidencia que el modelo **Gradient Boosting** obtiene el menor error promedio en la predicción del RUL, superando a Random Forest y Ridge Regression. Esta mejora es relevante desde el punto de vista operacional, ya que reduce la incertidumbre en la planificación de mantenciones.

Se evaluaron los modelos utilizando métricas apropiadas para regresión:

- **MAE (Error Absoluto Medio)** – métrica principal por su interpretación directa en ciclos  
- RMSE  
- R²  

### Resultados en conjunto de validación:

| Modelo             | MAE   | RMSE  | R²    |
|--------------------|-------|-------|-------|
| Gradient Boosting  | **10.57** | **14.01** | **0.887** |
| Random Forest      | 11.90 | 15.72 | 0.858 |
| Ridge Regression   | 13.01 | 15.74 | 0.858 |

El modelo **Gradient Boosting** presenta el mejor desempeño global, logrando el menor error promedio y la mayor capacidad explicativa.

Desde un punto de vista operativo, una reducción de 2–3 ciclos en el error promedio puede representar una mejora significativa en la planificación de mantenciones.

---

## 6. Análisis crítico

Si bien los resultados son satisfactorios, se identifican algunas limitaciones:

- Sensibilidad a condiciones operacionales no presentes en el conjunto de entrenamiento
- Incremento del costo computacional debido a la alta dimensionalidad
- Necesidad de monitoreo del desempeño en escenarios reales

El modelo debe ser utilizado como **herramienta de apoyo a la decisión**, complementando el criterio experto y no reemplazándolo completamente.

---

## 7. Propuesta de despliegue

En un entorno real, el modelo podría integrarse en un sistema de mantenimiento predictivo con el siguiente flujo:

1. Captura continua de datos de sensores  
2. Preprocesamiento y generación de ventanas temporales  
3. Predicción periódica del RUL  
4. Visualización del estado de salud en dashboards  
5. Alertas tempranas ante umbrales críticos  

Se recomienda implementar estrategias de **reentrenamiento periódico** y monitoreo del desempeño en producción.

---

## 8. Estructura del repositorio

├── notebooks/ # Notebook principal del proyecto
├── src/ # Scripts auxiliares (exportación de gráficos, helpers)
├── data/
│ ├── raw/ # Datos originales (no versionados)
│ └── processed/ # Datos procesados
├── outputs/
│ └── figures/ # Gráficos exportados
└── reports/ # Resúmenes y documentación


---

## 9. Conclusiones

Este proyecto demuestra que la predicción de la vida útil remanente es un problema complejo pero abordable mediante técnicas de **Machine Learning supervisado**.  

La comparación de modelos evidencia que enfoques no lineales, en particular **Gradient Boosting**, ofrecen ventajas claras frente a modelos lineales tradicionales en contextos de alta dimensionalidad y degradación progresiva.

El trabajo sienta una base sólida para futuras extensiones hacia modelos secuenciales y aplicaciones industriales en tiempo real.
