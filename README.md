# Proyecto Final – Machine Learning II  
## Predicción de Vida Útil Remanente (RUL) en Motores (NASA C-MAPSS FD001)

**Curso:** Machine Learning II  
**Tipo de problema:** Aprendizaje supervisado – Regresión  
**Dataset:** NASA C-MAPSS (FD001)  
**Repositorio:** `sebamarinovic/final_ML2`  
**Autores:** [Completar integrantes]  
**Fecha de presentación:** 20 de enero  

---

## 1. Descripción del proyecto

Este proyecto aborda la predicción de la **Vida Útil Remanente** (*Remaining Useful Life, RUL*) de motores a partir de mediciones multivariadas de sensores.  
El objetivo es estimar cuántos ciclos restantes le quedan a un motor antes de fallar, permitiendo **planificación de mantenimiento predictivo**, reducción de fallas no programadas y mejor soporte a la toma de decisiones operacionales.

Se desarrolla un pipeline completo:
- EDA (análisis exploratorio)
- Preprocesamiento e ingeniería de características
- Entrenamiento y ajuste de hiperparámetros
- Evaluación comparativa de **≥3 modelos**
- Análisis crítico e interpretación
- Propuesta conceptual de despliegue

---

## 2. Dataset y formulación del problema

El dataset utilizado corresponde a **NASA C-MAPSS FD001**, ampliamente validado en la literatura para mantenimiento predictivo.  
Contiene series temporales multivariadas de sensores y variables de operación para múltiples motores hasta su falla.

- Train: **20.631 observaciones**, **100 motores**
- Test: **13.096 observaciones**, **100 motores**
- Variables: `engine_id`, `cycle`, `setting_1..3` y `sensor_1..21`
- Target: `RUL` en ciclos (regresión)

### Consideración importante: truncamiento del RUL
El RUL se encuentra truncado a un máximo de **125 ciclos**, lo cual genera una distribución no uniforme y añade dificultad al modelamiento: el modelo debe aprender tanto estados saludables (valores altos y repetidos) como estados cercanos a falla.

---

## 3. Visualizaciones clave (EDA y resultados)

### 3.1 Distribución del RUL en entrenamiento

![Distribución del RUL (train)](rul_distribution_train.png)

**Interpretación:**  
Se observa una concentración significativa de observaciones cercanas al valor máximo (125), consistente con el truncamiento del target. Este patrón introduce una asimetría artificial y refuerza la necesidad de modelos capaces de capturar relaciones no lineales y cambios de régimen (saludable → degradación).

---

### 3.2 Comparación MAE entre modelos

![Comparación MAE modelos](mae_comparison_models.png)

**Interpretación:**  
La comparación del **MAE** muestra que el modelo **Gradient Boosting** obtiene el menor error promedio, superando a Random Forest y Ridge. En un contexto operacional, reducir el error en algunos ciclos puede representar una mejora relevante en la programación de mantenciones.

---

### 3.3 Predicción vs valor real (modelo ganador)

![Predicción vs RUL real – GB](predicted_vs_actual_rul_gb.png)

**Interpretación:**  
El gráfico evidencia una relación cercana a la diagonal, indicando buena correspondencia entre predicción y valor real.  
Se aprecia una mayor dispersión en rangos altos de RUL, lo cual es esperable debido a:
- truncamiento del target
- mayor incertidumbre en etapas tempranas del ciclo de vida del motor

---

### 3.4 Distribución de residuos (modelo ganador)

![Distribución de residuos – GB](residuals_distribution_gb.png)

**Interpretación:**  
Los residuos se distribuyen de forma aproximadamente centrada en cero, lo que sugiere ausencia de sesgos sistemáticos severos. La dispersión observada está alineada con el error global reportado por MAE/RMSE.

---

## 4. Ingeniería de características y preparación de datos

Para capturar la dinámica temporal de degradación, se aplicó una estrategia de **ventanas temporales deslizantes**, transformando series temporales en vectores de características de alta dimensión.

Dimensiones reportadas en el notebook:
- Train final: **14.241 muestras**
- Validación: **3.490 muestras**
- Features por muestra: **720**

> Nota metodológica: la separación de conjuntos se realiza **por motor**, evitando fuga de información y permitiendo una evaluación realista de generalización.

---

## 5. Modelos implementados y tuning

Se compararon tres enfoques de regresión supervisada:

1. **Ridge Regression**  
   Baseline lineal regularizado (controla colinealidad y alta dimensionalidad).

2. **Random Forest Regressor**  
   Ensamble no lineal, robusto, captura interacciones entre variables.

3. **Gradient Boosting Regressor** (**modelo ganador**)  
   Modelo secuencial capaz de capturar relaciones complejas en datos no lineales.

El ajuste de hiperparámetros se realizó mediante **Grid Search + validación cruzada**, priorizando **MAE** como métrica principal por interpretabilidad directa en ciclos.

---

## 6. Resultados cuantitativos (validación)

Métricas obtenidas en conjunto de validación:

| Modelo             | MAE     | RMSE    | R²      |
|--------------------|---------|---------|---------|
| Gradient Boosting  | **10.57** | **14.01** | **0.887** |
| Random Forest      | 11.90   | 15.72   | 0.858   |
| Ridge Regression   | 13.01   | 15.74   | 0.858   |

**Conclusión:**  
El modelo **Gradient Boosting** presenta el mejor desempeño global. La mejora sobre Ridge y Random Forest es consistente con su capacidad para capturar patrones no lineales y señales de degradación en múltiples sensores.

---

## 7. Análisis crítico y limitaciones

Aunque el modelo ganador muestra buen desempeño, se reconocen limitaciones relevantes:

- **Generalización:** puede degradar su desempeño ante condiciones operacionales no presentes en el entrenamiento.
- **Escalabilidad:** la estrategia de ventanas temporales incrementa la dimensionalidad y el costo computacional.
- **Uso responsable:** el modelo debe utilizarse como **apoyo a la decisión**, complementando criterio experto y validaciones en terreno.

---

## 8. Propuesta conceptual de despliegue

En un entorno real, el modelo podría integrarse con este flujo:

1. Adquisición continua de sensores  
2. Preprocesamiento automático + ventanas temporales  
3. Predicción periódica del RUL  
4. Dashboard operacional (estado de salud + tendencia)  
5. Alertas por umbrales (RUL crítico)  
6. Monitoreo del performance en producción + reentrenamiento periódico  

---

## 9. Reproducibilidad / ejecución

- El desarrollo principal está en: **`final_ML2.ipynb`**
- Los datos se encuentran en: **`CMAPSSData.zip`** (incluido en el repo)

Recomendado:
- Ejecutar el notebook de arriba a abajo en Google Colab / Jupyter.
- Mantener un esquema claro de rutas y guardado de figuras.

---

## 10. Estructura del repositorio
.
├── final_ML2.ipynb
├── CMAPSSData.zip
├── README.md
├── rul_distribution_train.png
├── mae_comparison_models.png
├── predicted_vs_actual_rul_gb.png
└── residuals_distribution_gb.png

---

## 11. Conclusiones

Este trabajo demuestra que la predicción de RUL es un problema desafiante pero abordable con aprendizaje supervisado.  
La comparación de modelos evidencia que **Gradient Boosting** ofrece el mejor balance entre precisión y capacidad de explicación del fenómeno, alcanzando un **MAE ≈ 10.6 ciclos** y **R² ≈ 0.887** en validación.  

El proyecto sienta una base sólida para futuras extensiones hacia modelos secuenciales (LSTM/Transformers) e integración en sistemas industriales de monitoreo en tiempo real.

