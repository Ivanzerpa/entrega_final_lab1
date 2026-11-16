# Labo 1 2026 – Analista Sr – Predicción final

Workflow basado en `z739_final_senior` (cátedra), con las modificaciones del experimento **666128**.

## Entrega final

| | |
|---|---|
| Experimento | **666128** |
| Archivo enviado | `KA666128_s50_10000.csv` (10.000 envíos) |
| Ganancia pública | 452.248 (promedio 9.500–11.500 envíos: 449.0) |
| Semilla primigenia | 123443 |

## Contenido del repositorio

| Archivo | Para qué |
|---|---|
| `739_final_senior-666128.ipynb` | Notebook que genera la predicción final, con las salidas de la corrida |
| `PARAM.yml` | Parámetros exactos de la corrida 666128 |
| `BO_log.txt` | Resultado de la optimización bayesiana |
| `impo.txt` | Importancia de variables del modelo final |
| `KA666128_s50_10000.csv` | Archivo enviado a Kaggle |

## Cambios respecto de `z739_final_senior`

* Semilla primigenia 123443.
* Data drifting `rank_cero_fijo` (en lugar de `deflacion`), con `set.seed` previo.
* Canaritos: ratio 0.75, desvíos 0; entrenamiento 202101–202104, validación 202106.
* Training strategy: se excluyen **202003–202006** del training de la bayesiana y del final train.
* Optimización bayesiana: 50 iteraciones, 8 hiperparámetros (se agregan `lambda_l2`, `bagging_fraction`, `min_data_in_leaf`), árboles y hojas entre 4 y 128, **semillerío de 50 LightGBM por punto** (AUC de la probabilidad promedio).
* Final train con hiperparámetros aplicados vía `trafoValue` y **semillerío de 50**.

## Cómo replicar

1. Crear una VM con el template de la cátedra (runtime R, ≥ 256 GB de RAM; se usó 512 GB / 12 vCPU).
2. Verificar que existan:
   * el dataset en `/content/datasets/analistasr_competencia_2026.csv.gz`
   * el bucket montado en `/content/buckets/b1/`
3. Abrir `739_final_senior-666128.ipynb` con runtime **R** y ejecutar todas las celdas en orden.
4. Los resultados quedan en `/content/buckets/b1/exp/WF666128/`. El archivo final es `kaggle/KA666128_s50_10000.csv`.

**Tiempo aproximado:** ~11 horas (preprocesamiento ~30 min, bayesiana ~10 h, final train y scoring ~1 h).

**Si la VM se interrumpe:** volver a correr la notebook desde el principio con el mismo número de experimento. La bayesiana retoma desde `HT.RDATA` y el final train saltea los modelos ya guardados en `modelos/`.

## Nota de reproducibilidad

La corrida original no tenía fijada la semilla antes del drifting (`frank(ties.method = "random")`), por lo que el conjunto de variables que sobrevive a los canaritos podía variar entre sesiones (la corrida final usó 309 variables). Después de la corrida se agregó `set.seed` antes del drifting, de modo que corridas sucesivas den siempre el mismo resultado. Las salidas que se ven en la notebook son de la corrida original, sin esa semilla; por eso una réplica puede diferir levemente en la cantidad de variables y en la ganancia.

## Resumen de experimentos

| Exp | Cambio principal | Promedio público 9.500–11.500 |
|---|---|---|
| 666123 | base: rank_cero_fijo, semillerío en BO | 428.8 |
| 666124 | 8 hiperparámetros, árboles/hojas hasta 128 | 448.8 |
| 666125 | deflación + canaritos 0.75/0 | 437.9 |
| 666126 | rank_cero_fijo + canaritos 0.75/0 | 448.3 |
| 666127 | + promedio/ratioavg, sin foto_mes/ID | 423.5 |
| **666128** | **666126 sin 202003–202006, semillerío 50** | **449.0** |
| 666129 | sin 202003–202008, bayesiana acotada | 441.5 |
