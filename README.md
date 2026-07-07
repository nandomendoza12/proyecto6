# 📊 Predicción y Estrategia de Retención de Clientes (Análisis de Churn)

Análisis exploratorio de datos de una empresa de telecomunicaciones para identificar qué factores están más asociados al abandono de clientes (churn), como base para priorizar acciones de retención. El análisis combina limpieza y exploración en Python con un dashboard interactivo en Power BI.

![Dashboard de retención de clientes](https://github.com/nandomendoza12/proyecto6/blob/main/Imagen%20de%20dasborh%20del%20proyecto.png)

---

## 🎯 Objetivo de negocio

El negocio quiere reducir la tasa de cancelación de clientes. Este análisis identifica qué variables (tipo de contrato, servicio de internet, método de pago, facturación, antigüedad) están más asociadas al abandono, para orientar campañas de retención hacia los segmentos de mayor riesgo y estimar el impacto económico del churn.

**Preguntas que responde este análisis:**
- ¿Cuál es la tasa de churn actual y su impacto económico estimado?
- ¿Qué tipo de contrato, método de pago y tipo de facturación se asocian con mayor abandono?
- ¿El tiempo de permanencia (tenure) y el cargo mensual influyen en el churn?
- ¿Qué segmento de clientes tiene mayor riesgo de abandonar?

---

## 🗂️ Dataset

- **Fuente:** Telco Customer Churn (dataset público, tipo Kaggle/IBM sample)
- **Registros originales:** 7,043 filas × 21 columnas
- **Variable objetivo:** `Churn` (`Yes` / `No`)

**Diccionario de variables clave:**

| Columna | Descripción |
|---|---|
| `Churn` | Indica si el cliente abandonó (`Yes`) o permanece (`No`) |
| `Contract` | Tipo de contrato: Month-to-month, One year, Two year |
| `tenure` | Meses de antigüedad como cliente |
| `MonthlyCharges` | Cargo mensual del cliente |
| `TotalCharges` | Cargo total acumulado (venía como texto, se convirtió a numérico) |
| `InternetService` | Tipo de servicio de internet: DSL, Fiber optic, No |
| `PaymentMethod` | Método de pago utilizado |
| `PaperlessBilling` | Indica si el cliente usa facturación electrónica (`Yes`/`No`) |
| `Partner` | Indica si el cliente tiene pareja (`Yes`/`No`) |
| `SeniorCitizen` | Indica si el cliente es adulto mayor (0/1) |

---

## 🧹 Limpieza y transformación de datos

Realizado en `notebook/churn_analisis.ipynb` con Python (pandas).

| Paso | Detalle |
|---|---|
| Valores nulos | `TotalCharges` venía como texto; al convertirla a numérico con `pd.to_numeric(..., errors='coerce')` aparecieron **11 valores nulos**, correspondientes a clientes con `tenure = 0`. Se eliminaron esas 11 filas (< 0.2% del total) |
| Estandarización de texto | La columna `Churn` se limpió con `.str.strip().str.lower()` para evitar inconsistencias de mayúsculas/espacios |
| Variable binaria | Se creó `Churn_Binario` (1 = abandonó, 0 = se mantiene) a partir de `Churn` |
| Resultado final | **7,032 filas**, sin valores nulos restantes |

---

## 📈 Hallazgos principales

### KPIs generales (dashboard)

| Métrica | Valor |
|---|---|
| Clientes analizados | 7,032 |
| Tasa de abandono | **27%** |
| Costo promedio (por cliente perdido) | $893.30 |
| Ingresos perdidos al año (estimado) | $1.67 millones |

**Fórmulas DAX (Power BI):**

```dax
Ingresos_perdidos_anual =
SUMX(
    FILTER(Churn_Clientes_Modificado, Churn_Clientes_Modificado[Churn_Binario] = 1),
    Churn_Clientes_Modificado[MonthlyCharges] * 12
)

Costo_promedio_churn =
DIVIDE([Ingresos_perdidos_anual], Churn_Clientes_Modificado[Clientes_Churn])
```

`Ingresos_perdidos_anual` anualiza el cargo mensual (`MonthlyCharges` × 12) de cada cliente que hizo churn y los suma, estimando los ingresos recurrentes que la empresa deja de percibir en un año por los clientes perdidos. `Costo_promedio_churn` divide ese total entre el número de clientes con churn (`Clientes_Churn`), dando el costo promedio anualizado por cliente perdido.

### Hallazgos por segmento

1. **Antigüedad (tenure):** el abandono es marcadamente mayor en el grupo de **0-12 meses** y decrece de forma consistente en cada grupo posterior (13-24, 25-48, 49-60, 61+ meses). Los clientes nuevos son el segmento de mayor riesgo.
2. **Tipo de contrato:** los clientes con contrato **mes a mes (Month-to-month)** muestran mayor abandono frente a contratos de uno o dos años (filtro disponible en el dashboard).
3. **Facturación electrónica:** los clientes con `PaperlessBilling = Yes` muestran una proporción de abandono notablemente mayor que los que reciben factura física (`No`).
4. **Método de pago:** **Electronic check** es el método de pago con mayor abandono, muy por encima de Mailed check, Bank transfer y Credit card (automáticos).
5. **Tipo de internet:** **Fiber optic** es el servicio de internet con mayor número de clientes y también el que muestra mayor proporción de abandono frente a DSL o sin internet.
6. **Relación cargo mensual / antigüedad:** el gráfico de dispersión (`MonthlyCharges_per_Tenure` vs. `tenure`) muestra que el cargo relativo por antigüedad cae rápidamente durante los primeros ~20 meses para todos los clientes, y los clientes que abandonan (`Churn_Binario = 1`) se concentran más en las zonas de tenure bajo con valores más altos de este ratio.
7. **Partner:** los clientes sin pareja (`Partner = No`) representan una proporción de churn mayor que los que sí tienen pareja.

---

## 💡 Recomendaciones de negocio

- Priorizar campañas de retención en clientes con **menos de 12 meses de antigüedad**, que es el segmento de mayor riesgo según el dashboard.
- Incentivar la migración de contratos **mes a mes** hacia planes anuales, ofreciendo descuentos o beneficios por permanencia.
- Investigar la fricción del pago con **cheque electrónico** y migrar clientes hacia métodos de pago automático.
- Revisar si la **facturación electrónica** está asociada a un peor seguimiento o experiencia post-venta, dado que correlaciona con mayor abandono.
- Cuantificar el retorno de inversión de retener al segmento de **fibra óptica**, dado su alto volumen y alta tasa de abandono.

---

## 🔧 Feature engineering (variables creadas)

| Variable | Descripción | ¿Dónde se usó? |
|---|---|---|
| `MonthlyCharges_per_Tenure` | Cargo mensual dividido por la antigüedad (+1 para evitar división por cero) | Gráfico de dispersión en el dashboard de Power BI |
| `Tenure_Group` | Antigüedad agrupada en 5 bins: 0-12, 13-24, 25-48, 49-60, 61+ meses | Gráfico "Abandono por grupo de tenencia" en el dashboard |
| `Has_Additional_Services` | Indica si el cliente tiene al menos un servicio adicional | Aún no analizada — ver "Próximos pasos" |

---

## 📌 Próximos pasos

- [ ] Analizar `Has_Additional_Services` vs. `Churn` (creada en Python pero aún sin visualizar).
- [ ] Cuantificar en Python las tasas de churn exactas por categoría (`groupby('Contract')['Churn_Binario'].mean()`, etc.) para respaldar con números los hallazgos visuales del dashboard.
- [ ] Aplicar una prueba de chi-cuadrado entre `Contract` / `PaymentMethod` / `PaperlessBilling` y `Churn` para validar significancia estadística.
- [ ] Publicar el dashboard de Power BI en línea (Power BI Service) y enlazarlo aquí.

---

## 📊 Dashboard

El dashboard interactivo en Power BI **"Predicción y Estrategia de Retención de Clientes"** incluye:

- KPIs generales: número de clientes, tasa de abandono, costo promedio e ingresos perdidos al año.
- Filtro por tipo de contrato (Month-to-month / One year / Two year).
- Abandono por grupo de tenencia (`Tenure_Group`).
- Abandono por tipo de pago y por facturación electrónica.
- Distribución de clientes por tipo de servicio de internet.
- Dispersión de `MonthlyCharges_per_Tenure` vs. `tenure`, coloreada por `Churn_Binario`.
- Segmentación de abandono por `Partner`.

- Archivo: [`dashboard/retencion_clientes.pbix`](dashboard/retencion_clientes.pbix)
- → *Si lo publicas: [Ver dashboard en línea](enlace-a-power-bi-service)*

---

## 🛠️ Herramientas utilizadas

- **Python** (pandas, numpy, matplotlib, seaborn) — limpieza y análisis exploratorio
- **Power BI** — visualización, KPIs y dashboard interactivo
- **Jupyter Notebook** — documentación del proceso

---

## 📁 Estructura del repositorio

```
proyecto6/
├── data/               # dataset original y procesado
├── notebook/           # limpieza y análisis exploratorio en Python
├── dashboard/          # archivo .pbix e imagen de preview
├── requirements.txt
└── README.md
```

---

## 🚀 Cómo reproducir este análisis

```bash
git clone https://github.com/nandomendoza12/proyecto6.git
cd proyecto6
pip install -r requirements.txt
jupyter notebook notebook/churn_analisis.ipynb
```

---

## 👤 Autor

**Nando Mendoza** · Analista de Datos Jr.
→ [LinkedIn](https://linkedin.com/in/tu-usuario) · → [Email](mailto:tucorreo@ejemplo.com)
