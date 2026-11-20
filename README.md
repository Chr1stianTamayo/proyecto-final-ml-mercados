# Sistema inteligente de análisis y predicción de mercados financieros

Proyecto final de **Fundamentos del Aprendizaje Automático** (profesor Felipe Grijalva).
Autor: Christian Tamayo.

Todo el proyecto está en una sola libreta: [`Proyecto_Final_ML_Mercados.ipynb`](Proyecto_Final_ML_Mercados.ipynb); [`Proyecto_Final_ML_Mercados.pdf`](Proyecto_Final_ML_Mercados.pdf) es la misma libreta ejecutada, exportada a PDF.

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Chr1stianTamayo/proyecto-final-ml-mercados/blob/main/Proyecto_Final_ML_Mercados.ipynb)

## Qué hace

Predice, para cinco acciones tecnológicas (ADBE, CRM, MU, ORCL, TXN), si el día siguiente tendrá un **movimiento grande**: un retorno logarítmico superior al 1 % en valor absoluto, en cualquier dirección. Es una herramienta de gestión de riesgo: anticipa cuánto se moverá el precio, no hacia dónde. Combina dos modalidades:

- **Serie de tiempo:** rezagos causales de precio y volumen (momentum, volatilidad, RSI, MACD, bandas de Bollinger).
- **Texto:** titulares de noticias de [FNSPID](https://huggingface.co/datasets/Zihan1004/FNSPID) convertidos en *embeddings* con [FinBERT](https://huggingface.co/ProsusAI/finbert).

Compara una regresión logística con red elástica frente a un *gradient boosting*, con 10 pliegues cronológicos, prueba de hipótesis pareada y evaluación en un conjunto de prueba ciego (2022–2023), apartado antes de explorar los datos. Antes de optimizar evalúa un *baseline* simple (regresión logística por defecto) y dos referencias que no aprenden nada, y contrasta los modelos contra ellos. Incluye una ablación que mide si las noticias aportan información adicional a los precios, intervalos por remuestreo de bloques en la prueba ciega y un diagrama de calibración.

Una primera formulación del proyecto intentaba predecir la dirección del retorno; no superó la referencia sin habilidad y se documenta en las limitaciones de la libreta.

## Cómo ejecutarla

### Google Colab (recomendado)

1. Abrir la libreta en Colab con [este enlace](https://colab.research.google.com/github/Chr1stianTamayo/proyecto-final-ml-mercados/blob/main/Proyecto_Final_ML_Mercados.ipynb).
2. `Entorno de ejecución → Cambiar tipo de entorno → GPU` (acelera FinBERT; sin GPU también funciona, más lento).
3. `Entorno de ejecución → Ejecutar todas`.

La primera celda instala lo que Colab no trae (`yfinance`, `optuna`, `wordcloud`, `nltk`).

### Local

```bash
git clone https://github.com/Chr1stianTamayo/proyecto-final-ml-mercados.git
```

```bash
cd proyecto-final-ml-mercados
```

```bash
pip install -r requirements.txt
```

```bash
jupyter notebook Proyecto_Final_ML_Mercados.ipynb
```

## Datos

No hay que descargar nada a mano. La libreta obtiene:

| Fuente | Qué | Cómo |
|---|---|---|
| Yahoo Finance (`yfinance`) | Precios diarios ajustados 2014–2023 | Descarga directa |
| FNSPID (Hugging Face) | Titulares de noticias por acción | Búsqueda binaria con peticiones HTTP parciales: baja unos 250 MB en lugar del archivo completo de 23 GB |
| `ProsusAI/finbert` | Modelo de lenguaje preentrenado | `transformers` |

Los resultados intermedios se guardan en `datos/` y se reutilizan en ejecuciones posteriores. Para forzar una descarga nueva, borrar esa carpeta.

## Estructura de la libreta

1. Introducción
2. Datos y análisis exploratorio
3. Extracción de características
4. Particionado y *pipelines*
5. Selección de características
6. Optimización de hiperparámetros
7. Comparación estadística
8. Visualización con t-SNE
9. Conclusiones
10. Referencias

## Garantías contra la fuga de información

- El período de prueba se aparta antes del análisis exploratorio.
- Las variables de precio se verifican con una prueba de causalidad (truncar el futuro no cambia el pasado).
- Una noticia solo se usa después del cierre de la primera sesión posterior a su fecha de publicación.
- Todo paso con parámetros aprendidos vive en un `Pipeline` de scikit-learn y se ajusta solo con entrenamiento.
- La validación es cronológica, por fechas únicas, con una brecha de 5 sesiones.

## Licencias de los datos

FNSPID y FinBERT se distribuyen para uso no comercial. Este repositorio no redistribuye sus datos: la libreta los descarga de sus fuentes originales.
