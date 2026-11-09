# Experimento #14 – Grupo A

Mitacek Denise / Roldán Guillermo

## 1. Objetivo y planteo

Comparar tres métodos de boosting de LightGBM (`gbdt`, `rf` y `dart`) en la ganancia de Kaggle sobre 202109, dándole a cada método su mejor oportunidad.

- **Cada corrida hace una BO completa** (confirmado por la cátedra): no se reutilizan hiperparámetros de otra corrida ni se combinan resultados de experimentos anteriores.
- **Cada método tiene su propio espacio de búsqueda**: los hiperparámetros y los rangos \<desde, hasta> pueden ser distintos entre métodos (también confirmado por la cátedra).
- **Punto de partida del espacio**: lo aprendido en la Tarea para el Hogar 04 (ver *Regularización y optimización de LightGBM*): conviene optimizar `num_leaves`, `lambda_l1`, `lambda_l2` y el par `learning_rate` / `num_iterations`; con menos peso, `feature_fraction` y `min_data_in_leaf`.
- **Orden de trabajo**: primero se ajusta `gbdt` (9500 → 9502 → 9504) hasta que la BO no quede limitada por sus bordes; después `rf` y `dart` con el mismo protocolo.

## 2. Configuración común

Todas las corridas usan el notebook `z635_WorkFlow_01_senior` sin cambios fuera de lo indicado en cada corrida.

| Elemento | Valor |
| --- | --- |
| Dataset | `analistasr_competencia_2026.csv.gz` |
| Semillas primigenias | 194479/196817 |
| Preprocesamiento | CA MachineLearning · DR deflación · FE histórico (tendencia, ventana 6) · FE con RF (20 árboles, 16 hojas) · canaritos (ratio 0,2) |
| Validación de la BO | AUC sobre 202107 (`validate`) |
| Submuestreo en la BO | `training_pct` 0,2 de los CONTINUA |
| BO | mlrMBO, 8 hiperparámetros, 32 puntos iniciales + 50 iteraciones = 82 evaluaciones |
| Modelo final | mejores HP de la BO; `min_data_in_leaf` dividido por 0,2; semillerio de 10 semillas |
| Evaluación | Kaggle `labo-1-virtual-2026-analista-sr`, leaderboard público, 9 cortes de 8.000 a 12.000 envíos (paso 500) |

**Cómo comparamos en Kaggle:** la curva de ganancia en los 9 cortes, el mejor corte, el rango de ganancias (mínima y máxima) y en cuántos cortes una corrida supera a la otra. Por indicación de la cátedra, los 9 cortes no se resumen en un único valor por corrida.

## 3. Registro de corridas

Cada corrida cambia una sola cosa respecto de la anterior, para poder atribuirle el efecto.

| Exp. | Método | Qué cambió | Meses | Espacio de la BO | BO (min) | Modelo final (min) | Estado |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 9525 | rf | Mismo notebook que 9510 con otra semilla primigenia (196871) | Todos | Igual que 9510 | 242,2 | 142,9 | Corrida |
| 9523 | rf | Mismo notebook que 9510 con otra semilla primigenia (196853) | Todos | Igual que 9510 | 344,3 | 334,5 | Corrida |
| 9521 | rf | Mismo notebook que 9510 con otra semilla primigenia (196837) | Todos | Igual que 9510 | 229,3 | 92,8 | Corrida |
| 9520 | rf | Mismo notebook que 9510 con otra semilla primigenia (476279) | Todos | Igual que 9510 | 331,9 | 258,5 | Corrida |
| 9519 | rf | Mismo notebook que 9510 con otra semilla primigenia (196831) | Todos | Igual que 9510 | 275,7 | 174,5 | Corrida |
| 9516 | rf | Mismo notebook que 9510 con otra semilla primigenia | Todos | Igual que 9510 | — | — | Terminando; se suma cuando estén sus resultados |
| 9517 | rf | Mismo notebook que 9510 con otra semilla primigenia (196817) | Todos | Igual que 9510 | 438,7 | 145,5 | Corrida |
| 9518 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (907369) | Todos | Igual que 9506 | 555,7 | 184,0 | Corrida |
| 9515 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (196871) | Todos | Igual que 9506 | 356,1 | 217,7 | Corrida |
| 9514 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (476279) | Todos | Igual que 9506 | 687,8 | No registrado | Corrida |
| 9513 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (196853) | Todos | Igual que 9506 | 495,2 | 181,9 | Corrida |
| 9512 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (349603); corre su propia BO | Todos | Igual que 9506 | 576,4 | 81,0 | Corrida |
| 9511 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (196837) | Todos | Igual que 9506 | 617,6 | 338,7 | Corrida |
| 9510 | rf | Límites de `rf` ampliados donde el óptimo quedó en el borde (sección 9) | Todos | `rf` ampliado | 257,8 | 243,2 | Corrida · representante de `rf` |
| 9509 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (196831); corrida por el compañero | Todos | Igual que 9506 | 780,7 | 564,1 | Corrida |
| 9508 | rf | Primer método alternativo, con su propio espacio (sección 8) | Todos (igual que 9506) | Propio de `rf` | 94,9 | 111,6 | Corrida |
| 9507 | gbdt | Mismo notebook que 9506 con otra semilla primigenia (196817); corrida por el compañero | Todos | Igual que 9506 | 738,5 | 492,1 | Corrida |
| 9506 | gbdt | Vuelven todos los meses (pandemia y 2019) sobre el espacio de 9504 (sección 7) | training 201901–202105 (29 meses); final\_train 201901–202107 (31 meses) | Igual que 9504 | 393,4 | 290,6 | Corrida · representante de `gbdt` |
| 9505 | dart | Intento de dart que no prosperó | — | — | — | — | Fallida, sin resultados |
| 9504 | gbdt | Límites de la BO ampliados donde el óptimo quedó en el borde (sección 6) | Grupo A (igual que 9502) | Ampliado | 266,6 | 116,5 | Corrida |
| 9503 | dart | Intento de dart que no prosperó | — | — | — | — | Fallida, sin resultados |
| 9502 | gbdt | Meses de la propuesta del Grupo A (sin pandemia ni primer semestre de 2019) | training 18 meses (201907–202105, sin 202003–202007); final\_train 18 meses (201909–202107, mismo hueco) | Igual que 9500 | 127,2 | 80,0 | Corrida |
| 9501 | dart | Único dart completo, con su propio espacio de 10 hiperparámetros (sección 11); semilla 196817 | Todos | Propio de dart | 243,8 | No registrado | Corrida · representante de dart |
| 9500 | gbdt | Base: BO con 8 hiperparámetros y rangos de la tarea, ampliados | training 201901–202105 (29 meses); final\_train 201901–202107 (31 meses) | Base | 184,2 | 141,6 | Corrida |

Semillas primigenias: 194479 en 9500 a 9510; 196817 en 9501 (`dart`), 9507 y 9517; 196831 en 9509 y 9519; 196837 en 9511 y 9521; 196853 en 9513 y 9523; 196871 en 9515 y 9525; 476279 en 9514 y 9520; 349603 en 9512 y 907369 en 9518. Siete semillas se repiten en `gbdt` y en `rf`, lo que permite comparar los dos métodos con la misma semilla. Las corridas se repartieron entre dos máquinas, así que los tiempos no se comparan entre máquinas distintas.

BO y modelo final = tiempos que registró cada corrida en `PARAM.yml` (`tiempo_HT_min`, `tiempo_FT_min`). Son solo una referencia general: no cubren toda la corrida (quedan afuera la lectura de datos y el preprocesamiento) y en varias corridas el proceso se cortó y se reanudó. Sin registro: el modelo final de 9501 y el de 9514.

Espacio base (9500 y 9502), en la escala en que busca la BO:

| Hiperparámetro | Desde | Hasta | Transformación | Rango real |
| --- | --- | --- | --- | --- |
| `num_iterations` | 7 | 11 | round(2^x) | 128 a 2.048 |
| `learning_rate` | −6,64 | −3,32 | 2^x | 0,010 a 0,100 |
| `num_leaves` | 4 | 8 | round(2^x) | 16 a 256 |
| `lambda_l1` | 0 | 15 | — | 0 a 15 |
| `lambda_l2` | 10 | 300 | — | 10 a 300 |
| `feature_fraction` | 0,2 | 0,8 | — | 0,2 a 0,8 |
| `min_data_in_leaf` | 5 | 12 | round(2^x) | 32 a 4.096 |
| `min_sum_hessian_in_leaf` | −10 | 10 | 2^x | 0,001 a 1.024 |

## 4. Resultados 9500, 9502 y 9504

9506, 9507, 9509 y 9512 son el mismo notebook con cuatro semillas distintas, y dan curvas distintas: en un mismo corte, la ganancia varía entre 3 y 22 puntos según la semilla. Esas diferencias son del mismo tamaño que las que separan a las corridas que cambiaron meses o espacio de la BO: con una sola semilla por corrida, no se distinguen del azar. El mejor corte de todas las corridas de `gbdt` lo tiene 9512 (466,2 en 9.500).

&#91;embedded content: Kaggle, leaderboard público · 9500 a 9506 (semilla 194479), 9507 (196817), 9509 (196831) y 9512 (349603), todas gbdt · 9 cortes de 8.000 a 12.000 envíos\]

| Métrica | 9500 | 9502 | 9504 | 9506 | 9507 | 9509 | 9512 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Meses · espacio · semilla | Todos · base · 194479 | Sin pandemia · base · 194479 | Sin pandemia · ampliado · 194479 | Todos · ampliado · 194479 | Todos · ampliado · 196817 | Todos · ampliado · 196831 | Todos · ampliado · 349603 |
| AUC en 202107 (mejor de la BO) | 0,939989 (iter. 48) | 0,938976 (iter. 42) | 0,939034 (iter. 14) | 0,939886 (iter. 25) | 0,940340 (iter. 16) | 0,941063 (iter. 33) | 0,940688 (iter. 42) |
| Ganancia mínima – máxima (9 cortes) | 437,2 – 463,2 | 439,4 – 455,7 | 433,0 – 456,7 | 436,2 – 462,7 | 432,2 – 453,1 | 421,7 – 451,8 | 437,0 – 466,2 |
| Mejor corte | 463,248 (8.500) | 455,664 (10.000) | 456,664 (9.000) | 462,748 (9.000) | 453,081 (9.500) | 451,831 (10.500) | 466,248 (9.500) |
| Tiempo BO | 184,2 min | 127,2 min | 266,6 min | 393,4 min | 738,5 min | 780,7 min | 576,4 min |
| Tiempo modelo final (10 semillas) | 141,6 min | 80,0 min | 116,5 min | 290,6 min | 492,1 min | 564,1 min | 81,0 min |

Los tiempos salen de `PARAM.yml` (`tiempo_HT_min`, `tiempo_FT_min`), salvo la BO de 9500, que no tenía esa celda: ahí se sumó `exec.time` del `BO_log`.

Mejores hiperparámetros, ya transformados:

| Hiperparámetro | 9500 | 9502 | 9504 | 9506 | 9507 | 9509 | 9512 | Espacio 9500/9502 | Espacio 9504 a 9512 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `num_iterations` | 1.701 | 1.554 | 2.389 | 3.451 | 3.224 | 4.080 | 3.931 | 128 a 2.048 | 512 a 4.096 |
| `learning_rate` | 0,0101 | 0,0100 | 0,0095 | 0,0068 | 0,0073 | 0,0051 | 0,0055 | 0,010 a 0,100 | 0,0025 a 0,025 |
| `num_leaves` | 220 | 221 | 148 | 265 | 199 | 191 | 173 | 16 a 256 | 64 a 1.024 |
| `lambda_l1` | 5,29 | 1,30 | 4,03 | 0,10 | 7,31 | 0,36 | 3,13 | 0 a 15 | 0 a 10 |
| `lambda_l2` | 11,1 | 11,4 | 0,81 | 27,6 | 2,19 | 23,1 | 5,43 | 10 a 300 | 0 a 150 |
| `feature_fraction` | 0,200 | 0,201 | 0,225 | 0,182 | 0,266 | 0,187 | 0,165 | 0,2 a 0,8 | 0,05 a 0,5 |
| `min_data_in_leaf` (BO / final) | 115 / 575 | 137 / 685 | 173 / 865 | 51 / 255 | 573 / 2.865 | 217 / 1.085 | 86 / 430 | 32 a 4.096 | 16 a 1.024 |
| `min_sum_hessian_in_leaf` | 0,0013 | 9,27 | 1,13 | 0,97 | 0,026 | 0,012 | 0,0016 | 0,001 a 1.024 | 0,001 a 1.024 |

## 5. Hallazgos

**Con una sola semilla por corrida, las diferencias entre corridas de `gbdt` no se distinguen del azar.** Cambiar solo la semilla (9506 → 9507) movió la ganancia tanto como cambiar los meses o el espacio de la BO. La cátedra confirmó que se pueden usar todos los meses; 9506 representa a `gbdt`.

### Efecto de los meses (9500 → 9502)

- Con 18 meses en lugar de 29 (training), la BO perdió 0,001 de AUC y 9502 perdió con 9500 en 7 de los 9 cortes.
- La curva de 9502 es más plana y su pico se corre de 8.500 a 10.000 envíos; solo gana en 11.000 (+0,6, un empate) y 12.000 (+9,4).
- Con una semilla y el leaderboard público, lo firme es la forma de la curva y los cortes ganados, no la diferencia en puntos.
- Menos datos también bajaron los tiempos: −31% en la BO y −44% en el modelo final.

### La BO quedó apretada contra los bordes del espacio

En las dos corridas el óptimo cae sobre el límite de varios hiperparámetros: la BO no pudo explorar del otro lado, así que no sabemos si el óptimo real está más allá. Rangos de las 10 mejores iteraciones de cada BO:

| Hiperparámetro | Límite | Top 10 en 9500 | Top 10 en 9502 | Lectura |
| --- | --- | --- | --- | --- |
| `learning_rate` | piso 0,0100 | 0,0100 a 0,0146 | 0,0100 a 0,0126 | En el piso: pide aprender más lento |
| `feature_fraction` | piso 0,20 | 0,200 a 0,216 | 0,200 a 0,286 | En el piso: pide ver menos variables por árbol |
| `lambda_l2` | piso 10 | 10,9 a 32,6 | 11,4 a 139,7 | El mejor, pegado al piso en ambas |
| `num_leaves` | techo 256 | 92 a 254 | 120 a 254 | Mejor en \~220; varias iteraciones en 253–254 |
| `num_iterations` | techo 2.048 | 1.570 a 2.038 | 1.137 a 2.045 | Cerca del techo, coherente con `learning_rate` en el piso |
| `lambda_l1` | piso 0 | 2,1 a 6,8 | 0,01 a 1,60 | Interior en 9500; en 9502 se va a 0 |
| `min_data_in_leaf` | piso 32 | 49 a 476 | 32 a 691 | Interior; en 9502 dos del top tocan el piso |
| `min_sum_hessian_in_leaf` | 0,001 a 1.024 | 0,001 a 3,9 | 0,018 a 59 | Disperso: casi no influye |

Conclusiones para el espacio de 9504:

- `learning_rate` y `num_iterations` van juntos: si baja la tasa hacen falta más árboles, así que hay que ampliar los dos a la vez.
- `feature_fraction` bajo y `lambda_l2` bajo coinciden con la tarea ("cuanto menor, algo mejor" en `feature_fraction`), pero acá el `lambda_l2` óptimo es mucho menor que el 100–200 de la tarea: con árboles de \~220 hojas y tasa baja, la regularización la están haciendo otros parámetros.
- `min_sum_hessian_in_leaf` no muestra efecto; se mantiene en la BO para no cambiar más de una cosa por corrida.

### 9504: con el espacio ampliado, el óptimo quedó adentro

Se cumplió el criterio de la sección 6: ningún hiperparámetro del mejor punto quedó pegado a un borde que se pueda correr. El único en un extremo es `lambda_l2` (0,81), pero 0 es su mínimo posible: el modelo no pide L2.

| Hiperparámetro | Espacio 9504 | Top 10 en 9504 | Lectura |
| --- | --- | --- | --- |
| `learning_rate` | 0,0025 a 0,025 | 0,0066 a 0,0105 | Interior; el piso anterior (0,010) ya estaba casi en el óptimo |
| `num_iterations` | 512 a 4.096 | 1.504 a 3.293 | Interior |
| `num_leaves` | 64 a 1.024 | 70 a 451 | Interior y plano: el mejor bajó de \~220 a 148 sin cambiar el AUC |
| `feature_fraction` | 0,05 a 0,5 | 0,116 a 0,261 | Interior |
| `lambda_l2` | 0 a 150 | 0,1 a 41,7 | Junto a 0: sin L2 |
| `lambda_l1` | 0 a 10 | 1,4 a 5,5 | Interior |
| `min_data_in_leaf` | 16 a 1.024 | 60 a 640 | Interior |
| `min_sum_hessian_in_leaf` | 0,001 a 1.024 | 0,004 a 1,13 | Disperso: sin efecto en las tres corridas |

- **Lo que se fija es `num_iterations` × `learning_rate`, no cada uno.** En el top 10 de 9504 el producto da entre 18 y 26 en 9 de 10 casos (14 a 24 en 9500 y 9502). Bajar la tasa solo pidió más árboles.
- **El AUC está en una meseta.** Las 20 mejores evaluaciones de 9504 caben en 0,00068 de AUC (0,93836 a 0,93903), menos que el ruido de hasta 0,001 que vimos entre folds en la tarea.
- **Mismo AUC, Kaggle no mejora y cuesta el doble.** +0,00006 de AUC, gana solo en 2 de 9 cortes y la BO tardó +110%.

### Meses, espacio de la BO y semilla

Las corridas de `gbdt` permiten aislar tres efectos: meses, espacio de la BO y semilla. Cada fila compara dos corridas que difieren en una sola cosa:

| Comparación | Qué cambia | Cortes ganados | Diferencia por corte (puntos) |
| --- | --- | --- | --- |
| 9500 vs 9502 | Meses (espacio base) | 9500 gana 7 de 9 | −9,4 a +12,8 |
| 9506 vs 9504 | Meses (espacio ampliado) | 9506 gana 6 de 9 | −6,7 a +19,1 |
| 9504 vs 9502 | Espacio (sin pandemia) | 9504 gana 2 de 9 | −13,8 a +9,7 |
| 9506 vs 9500 | Espacio (todos los meses) | 9506 gana 4 de 9 | −9,2 a +6,9 |
| 9506 vs 9507 | Solo la semilla | 9506 gana 6 de 9 | −8,4 a +22,1 |
| 9506 vs 9509 | Solo la semilla | 9506 gana 9 de 9 | +2,4 a +18,8 |
| 9507 vs 9509 | Solo la semilla | 9507 gana 6 de 9 | −7,1 a +14,0 |
| 9512 vs 9506 | Solo la semilla | 9512 gana 5 de 9 | −15,8 a +13,2 |
| 9512 vs 9507 | Solo la semilla | 9512 gana 6 de 9 | −6,7 a +17,0 |
| 9512 vs 9509 | Solo la semilla | 9512 gana 7 de 9 | −0,6 a +18,3 |

- **Los meses parecen sumar en los dos espacios** (7 y 6 de 9 cortes) y ampliar el espacio no suma en ninguno (2 y 4 de 9).
- **Pero la semilla sola mueve lo mismo:** 9506 le gana a 9507 en 6 de 9 cortes, con diferencias de hasta 22 puntos por corte, sin cambiar nada más. Con una corrida por configuración, el efecto de los meses no se puede separar del azar.
- **El óptimo de 9506 queda adentro**, salvo `num_iterations`: con más datos pide más árboles. El mejor usa 3.451 y tres del top 10 pasan de 3.600, cerca del techo de 4.096. `learning_rate` (0,0053 a 0,0091), `feature_fraction` (0,16 a 0,26), `lambda_l2` (7,9 a 46,9) y `min_data_in_leaf` (51 a 440) quedan en el interior; `lambda_l1` del mejor punto es 0,10, junto a su mínimo natural.
- **El AUC sigue en meseta:** las 20 mejores evaluaciones caben en 0,00063 (0,93925 a 0,93989).
- **Es la corrida más cara de las mías:** 393 min de BO y 291 min de modelo final, unas 11 horas y media.

**Decisión:** 9506 es la configuración de `gbdt` (meses y espacio de búsqueda) que se repite con otras semillas, BO incluida (sección 10).

### 9507, 9509 y 9512: misma configuración, otras semillas

9507 es el notebook de 9506 sin cambios, salvo la semilla primigenia (196817). Muestra cuánto mueve la semilla todo el proceso, incluida la BO.

- **La BO llega a otro punto con casi el mismo AUC:** 0,940340 contra 0,939886, pero con `lambda_l1` 7,31 contra 0,10 y `min_data_in_leaf` 573 contra 51. Muchas combinaciones distintas dan casi el mismo AUC.
- **Más AUC no dio más ganancia:** 9507 tiene el AUC más alto de todas las corridas, pero pierde con 9506 en 6 de 9 cortes.
- **Bordes:** en el top 10 de 9507, `lambda_l1` llega a 9,96 (techo 10), `min_data_in_leaf` a 1.017 (techo 1.024) y `num_iterations` a 4.047 (techo 4.096). Con esta semilla la BO prefiere más regularización.
- **Tiempo:** 738,5 min de BO y 492,1 de modelo final, casi el doble que 9506, pero corrió en otra máquina: los tiempos de las corridas impares no se comparan con los de las pares.
- **Consecuencia:** la comparación entre métodos tiene que hacerse con varias semillas (sección 10), mirando la distribución de resultados y no una corrida.

9509 repite lo mismo con la semilla 196831:

- **Otro punto de la BO y el AUC más alto de todas las corridas (0,941063):** `num_iterations` 4.080, `learning_rate` 0,0051, `lambda_l1` 0,36, `min_data_in_leaf` 217.
- **El orden por AUC es el inverso del orden por ganancia:** en AUC, 9509 > 9507 > 9506; en ganancia, 9506 le gana a 9509 en los 9 cortes y 9507 le gana a 9509 en 6 de 9.
- **Borde:** `num_iterations` queda en el techo (4.080 de 4.096; 6 del top 10 pasan de 3.800), con la tasa más baja de las tres semillas: el mismo par `num_iterations` × `learning_rate` de siempre.
- **El AUC sigue en meseta:** las 20 mejores evaluaciones caben en 0,00086 (0,94020 a 0,94106).

9512 repite lo mismo con la semilla 349603:

- **Otro punto de la BO:** AUC 0,940688 con 3.931 árboles, `learning_rate` 0,0055, 173 hojas, `lambda_l1` 3,13 y `min_data_in_leaf` 86.
- **El mejor corte de todas las corridas de `gbdt`** (466,2 en 9.500), pero contra 9506 gana solo en 5 de 9 cortes.
- **Borde:** `num_iterations` otra vez cerca del techo (5 del top 10 pasan de 3.800).
- **El AUC sigue en meseta:** las 20 mejores evaluaciones caben en 0,00066 (0,94003 a 0,94069).

**Lo que dejan las cuatro semillas:** con la misma configuración, la ganancia de un mismo corte varió entre 3 y 22 puntos solo por la semilla, y el orden por AUC (9509 > 9512 > 9507 > 9506) no predijo el orden en Kaggle. Por eso los métodos se comparan con varias semillas y no con una sola corrida.

## 6. Diseño de la corrida 9504

9504 repite 9502 (gbdt, meses del Grupo A, semilla 194479, 50 iteraciones de BO) y cambia solo los límites del espacio: se corre cada borde donde el óptimo quedó pegado y se recorta el lado que la BO nunca eligió.

| Hiperparámetro | 9502 (escala BO) | 9504 (escala BO) | Rango real 9504 | Por qué |
| --- | --- | --- | --- | --- |
| `learning_rate` | −6,64 a −3,32 | −8,64 a −5,32 | 0,0025 a 0,025 | Piso tocado; el top nunca pasó de 0,015 |
| `num_iterations` | 7 a 11 | 9 a 12 | 512 a 4.096 | Techo casi tocado; con tasa más baja hacen falta más árboles; el top nunca bajó de 1.137 |
| `num_leaves` | 4 a 8 | 6 a 10 | 64 a 1.024 | Techo casi tocado; el top nunca bajó de 92 |
| `feature_fraction` | 0,2 a 0,8 | 0,05 a 0,5 | 0,05 a 0,5 | Piso tocado; el top nunca pasó de 0,29 |
| `lambda_l2` | 10 a 300 | 0 a 150 | 0 a 150 | Piso tocado; el top nunca pasó de 140 |
| `lambda_l1` | 0 a 15 | 0 a 10 | 0 a 10 | Ya incluye el 0; el top nunca pasó de 6,8 |
| `min_data_in_leaf` | 5 a 12 | 4 a 10 | 16 a 1.024 | Piso tocado en 9502; el top nunca pasó de 691 |
| `min_sum_hessian_in_leaf` | −10 a 10 | −10 a 10 | 0,001 a 1.024 | Sin efecto visible; no se toca |

**Costo esperado:** más árboles y más hojas hacen cada evaluación más lenta. En el peor caso (4.096 árboles de 1.024 hojas) una iteración puede tardar varias veces lo de 9502 (127 min de BO en total).

**Criterio de éxito:** que ningún hiperparámetro del mejor punto quede pegado a un borde. Si alguno vuelve a quedar en el borde, se amplía otra vez en 9506.

Código que cambia respecto de 9502 (el resto del notebook queda igual, incluido el bloque *Final Training Hyperparameters*, porque las transformaciones no cambian):

```r
PARAM$experimento <- 9504

# lower/upper en la escala de la BO; al lado, el rango real que prueba LightGBM
PARAM$hipeparametertuning$hs <- makeParamSet(
  makeNumericParam("num_iterations", lower= 9.0, upper= 12.0, trafo= function(x) round(2^x) ),   # 512 a 4096 arboles
  makeNumericParam("learning_rate", lower= -8.64, upper= -5.32, trafo= function(x) 2^x ),        # 0.0025 a 0.025
  makeNumericParam("num_leaves", lower= 6.0, upper= 10.0, trafo= function(x) round(2^x) ),        # 64 a 1024 hojas
  makeNumericParam("lambda_l1", lower= 0.0, upper= 10.0),                                         # 0 a 10 (sin trafo)
  makeNumericParam("lambda_l2", lower= 0.0, upper= 150.0),                                        # 0 a 150 (sin trafo)
  makeNumericParam("feature_fraction", lower= 0.05, upper= 0.5),                                  # 5% a 50% de las variables (sin trafo)
  makeNumericParam("min_data_in_leaf", lower= 4.0, upper= 10.0, trafo= function(x) round(2^x) ),  # 16 a 1024 en la BO (80 a 5120 en el modelo final, /0.2)
  makeNumericParam("min_sum_hessian_in_leaf", lower= -10.0, upper= 10.0, trafo= function(x) 2^x)  # 0.001 a 1024
)
```

## 7. Corrida 9506: 9504 con todos los meses

9506 repite 9504 (mismo espacio, semilla y 50 iteraciones) y solo vuelve a poner los meses que sacamos: la pandemia (202003–202007) y 2019 completo. Comparar 9504 con 9506 mide el efecto de los meses con el espacio ampliado, igual que 9500 contra 9502 lo midió con el espacio base.

- **Validez de los meses:** la cátedra confirmó que se pueden usar todos los meses, así que 9506 puede representar a `gbdt`.
- **Tiempo estimado:** con todos los meses, 9500 tardó 1,45 veces más en la BO y 1,77 veces más en el modelo final que 9502. Aplicado a 9504 daba unos 390 min de BO y 205 min de modelo final; el real fue 393 y 291 min.

Cambios respecto de 9504 (tres celdas; el resto queda igual):

```r
PARAM$experimento <- 9506

# 9.7.2.1 Training Strategy: todos los meses hasta 202105 (29 meses)
PARAM$trainingstrategy$training <- c(
  202105, 202104, 202103, 202102, 202101,
  202012, 202011, 202010, 202009, 202008, 202007, 202006, 202005, 202004, 202003, 202002, 202001,
  201912, 201911, 201910, 201909, 201908, 201907, 201906, 201905, 201904, 201903, 201902, 201901
)

# Final Training Dataset: todos los meses hasta 202107 (31 meses)
PARAM$trainingstrategy$final_train <- c(
  202107, 202106, 202105, 202104, 202103, 202102, 202101,
  202012, 202011, 202010, 202009, 202008, 202007, 202006, 202005, 202004, 202003, 202002, 202001,
  201912, 201911, 201910, 201909, 201908, 201907, 201906, 201905, 201904, 201903, 201902, 201901
)
```

## 8. Propuesta de la corrida 9508: `rf`

Con `gbdt` cerrado, 9508 pasa a `rf` con todo lo demás igual a 9506 (todos los meses, semilla 194479, 50 iteraciones de BO, 10 semillas en el modelo final). Tiene también 8 hiperparámetros, así que la BO hace las mismas 82 evaluaciones que en `gbdt`.

| Hiperparámetro | Escala BO | Rango real | Por qué |
| --- | --- | --- | --- |
| `num_iterations` | 7 a 10 | 128 a 1.024 | En `rf` los árboles son independientes y más árboles no sobreajustan: solo estabilizan. El techo limita el tiempo |
| `num_leaves` | 6 a 11 | 64 a 2.048 | `rf` necesita árboles grandes (poco sesgo); combinar muchos árboles distintos baja la varianza |
| `feature_fraction_bynode` | 0,05 a 0,5 | 0,05 a 0,5 | Es el *mtry*: variables sorteadas en cada corte. `gbdt` prefirió 0,12 a 0,26 por árbol |
| `bagging_fraction` | 0,2 a 0,9 | 0,2 a 0,9 | Fracción de registros de cada árbol; el FE de la cátedra usa 0,632 (bootstrap) |
| `lambda_l1` | 0 a 10 | 0 a 10 | Igual que 9504 |
| `lambda_l2` | 0 a 150 | 0 a 150 | Igual que 9504 |
| `min_data_in_leaf` | 4 a 10 | 16 a 1.024 | Igual que 9504 |
| `min_sum_hessian_in_leaf` | −10 a 10 | 0,001 a 1.024 | Igual que 9504 |

Fijos en `rf`: `bagging_freq = 1` (LightGBM exige bagging en cada árbol) y `feature_fraction = 1` (el azar en columnas lo da `feature_fraction_bynode`). `learning_rate` no se usa: los árboles se combinan con el mismo peso, no se encadenan.

Cambios respecto de 9504 (tres celdas):

```r
PARAM$experimento <- 9508

# parametros fijos del LightGBM
PARAM$lgbm$param_fijos <- list(
  boosting= "rf",
  objective= "binary",
  metric= "auc",
  first_metric_only= TRUE,
  boost_from_average= TRUE,
  feature_pre_filter= FALSE,
  verbosity= -100,
  force_row_wise= TRUE,
  seed= PARAM$semilla_primigenia,
  extra_trees= FALSE,
  max_depth= -1L,
  min_gain_to_split= 0.0,
  bagging_freq= 1,          # rf exige bagging en cada arbol
  feature_fraction= 1.0,    # el azar en columnas lo da feature_fraction_bynode
  pos_bagging_fraction= 1.0,
  neg_bagging_fraction= 1.0,
  is_unbalance= FALSE,
  scale_pos_weight= 1.0,
  max_bin= 31,
  early_stopping_rounds= 0
)

# lower/upper en la escala de la BO; al lado, el rango real que prueba LightGBM
PARAM$hipeparametertuning$hs <- makeParamSet(
  makeNumericParam("num_iterations", lower= 7.0, upper= 10.0, trafo= function(x) round(2^x) ),   # 128 a 1024 arboles
  makeNumericParam("num_leaves", lower= 6.0, upper= 11.0, trafo= function(x) round(2^x) ),       # 64 a 2048 hojas
  makeNumericParam("feature_fraction_bynode", lower= 0.05, upper= 0.5),                           # 5% a 50% de las variables en cada corte
  makeNumericParam("bagging_fraction", lower= 0.2, upper= 0.9),                                   # 20% a 90% de los registros por arbol
  makeNumericParam("lambda_l1", lower= 0.0, upper= 10.0),                                         # 0 a 10
  makeNumericParam("lambda_l2", lower= 0.0, upper= 150.0),                                        # 0 a 150
  makeNumericParam("min_data_in_leaf", lower= 4.0, upper= 10.0, trafo= function(x) round(2^x) ),  # 16 a 1024 en la BO (80 a 5120 en el modelo final, /0.2)
  makeNumericParam("min_sum_hessian_in_leaf", lower= -10.0, upper= 10.0, trafo= function(x) 2^x)  # 0.001 a 1024
)

# Final Training Hyperparameters: reemplaza las lineas de x$...
# (se borran learning_rate y feature_fraction, que rf no optimiza)
param_final$num_iterations <- round( 2^x$num_iterations )
param_final$num_leaves <- round( 2^x$num_leaves )
param_final$feature_fraction_bynode <- x$feature_fraction_bynode
param_final$bagging_fraction <- x$bagging_fraction
param_final$lambda_l1 <- x$lambda_l1
param_final$lambda_l2 <- x$lambda_l2
param_final$min_data_in_leaf <- round( round(2^x$min_data_in_leaf) /
  PARAM$trainingstrategy$training_pct )
param_final$min_sum_hessian_in_leaf <- 2^x$min_sum_hessian_in_leaf
```

Ojo: si queda la línea `param_final$learning_rate <- 2^x$learning_rate`, `x` no tiene esa columna y LightGBM recibe un valor vacío. Hay que borrarla.

### Resultado de 9508

`rf` quedó muy por debajo de `gbdt` en los 9 cortes: entre 60 y 83 puntos menos en cada corte, y AUC 0,930666 contra 0,939886.

&#91;embedded content: Kaggle, leaderboard público · 9506 (gbdt), 9508 y 9510 (rf) · 9 cortes de 8.000 a 12.000 envíos · semilla 194479\]

| Métrica | 9506 `gbdt` | 9508 `rf` |
| --- | --- | --- |
| AUC en 202107 (mejor de la BO) | 0,939886 (iter. 25) | 0,930666 (iter. 18) |
| Mejor corte | 462,748 (9.000) | 385,248 (8.000) |
| Tiempo BO | 393,4 min | 94,9 min |
| Tiempo modelo final (10 semillas) | 290,6 min | 111,6 min |

Mejores hiperparámetros de 9508 y rango de las 10 mejores evaluaciones:

| Hiperparámetro | Espacio 9508 | Mejor | Top 10 | Lectura |
| --- | --- | --- | --- | --- |
| `num_leaves` | 64 a 2.048 | 2.039 | 1.764 a 2.048 | **En el techo**: 15 de las 20 mejores pasan de 1.900 |
| `min_data_in_leaf` (BO / final) | 16 a 1.024 | 58 / 290 | 17 a 58 | Cerca del piso (16) |
| `lambda_l1` | 0 a 10 | 0,20 | 0,02 a 0,70 | Junto a 0: `rf` no pide L1 |
| `lambda_l2` | 0 a 150 | 0,02 | 0,006 a 0,26 | Junto a 0: `rf` no pide L2 |
| `num_iterations` | 128 a 1.024 | 944 | 132 a 944 | Casi no influye: con 244 árboles el AUC es 0,93050 |
| `feature_fraction_bynode` | 0,05 a 0,5 | 0,252 | 0,21 a 0,50 | Interior |
| `bagging_fraction` | 0,2 a 0,9 | 0,593 | 0,45 a 0,64 | Interior, cerca del 0,632 clásico |
| `min_sum_hessian_in_leaf` | 0,001 a 1.024 | 0,053 | 0,004 a 1,98 | Disperso: sin efecto |

- **`rf` pide árboles completos, sin freno:** máximo de hojas, hojas chicas y sin regularización. Es lo esperable en un random forest, que baja la varianza combinando muchos árboles muy profundos.
- **La curva de `rf` baja con los envíos:** su mejor corte es el más chico (8.000). Ordena bien a los primeros clientes pero no tan bien a los siguientes.
- **Antes de comparar, falta abrir el espacio:** con `num_leaves` en el techo y `min_data_in_leaf` cerca del piso, la BO de `rf` no pudo llegar a su óptimo. Por eso 9510 repite `rf` con límites ampliados, igual que se hizo con `gbdt` en 9504.

## 9. Corrida 9510: `rf` con límites ampliados

9510 repite 9508 (mismos meses, semilla, 50 iteraciones de BO y modelo final) y solo cambia los límites: abre los dos bordes donde quedó el óptimo y achica los rangos que la BO nunca usó.

| Hiperparámetro | 9508 (escala BO) | 9510 (escala BO) | Rango real 9510 | Por qué |
| --- | --- | --- | --- | --- |
| `num_leaves` | 6 a 11 | 8 a 12 | 256 a 4.096 | Techo tocado; el top 20 nunca bajó de 1.296 |
| `min_data_in_leaf` | 4 a 10 | 2 a 8 | 4 a 256 | Cerca del piso; el top 20 nunca pasó de 110 |
| `lambda_l1` | 0 a 10 | 0 a 2 | 0 a 2 | El top 20 nunca pasó de 1,6 |
| `lambda_l2` | 0 a 150 | 0 a 5 | 0 a 5 | El top 20 nunca pasó de 0,26; las pruebas con 5 o más dieron AUC de 0,926 o menos |
| `feature_fraction_bynode` | 0,05 a 0,5 | 0,05 a 0,6 | 0,05 a 0,6 | El mejor está en 0,25, pero una del top 10 llegó a 0,496: se abre el techo por las dudas |
| `num_iterations` | 7 a 10 | 7 a 10 | 128 a 1.024 | Sin cambios: el top 20 va de 132 a 1.022 árboles con casi el mismo AUC; más árboles solo suman tiempo |
| `bagging_fraction` | 0,2 a 0,9 | 0,2 a 0,9 | 0,2 a 0,9 | Interior |
| `min_sum_hessian_in_leaf` | −10 a 10 | −10 a 10 | 0,001 a 1.024 | Sin efecto visible; no se toca |

**Costo esperado:** el tiempo de cada árbol crece con las hojas. La mejor evaluación de 9508 (944 árboles de 2.039 hojas) tardó 277 s; con hasta 4.096 hojas, la BO (95 min en 9508) y el modelo final (112 min) pueden duplicarse.

**Criterio de éxito:** que `num_leaves` y `min_data_in_leaf` queden adentro del espacio. Si `num_leaves` vuelve a tocar el techo, se puede seguir abriendo, pero con cuidado por el tiempo.

**Espacio en disco:** con 1.024 árboles de 4.096 hojas, cada archivo de `modelos/` puede pesar el doble que en 9508. Revisar el tamaño de `WF9508/modelos` antes de correr.

Cambios respecto de 9508 (el resto del notebook queda igual):

```r
PARAM$experimento <- 9510

# lower/upper en la escala de la BO; al lado, el rango real que prueba LightGBM
PARAM$hipeparametertuning$hs <- makeParamSet(
  makeNumericParam("num_iterations", lower= 7.0, upper= 10.0, trafo= function(x) round(2^x) ),   # 128 a 1024 arboles
  makeNumericParam("num_leaves", lower= 8.0, upper= 12.0, trafo= function(x) round(2^x) ),       # 256 a 4096 hojas
  makeNumericParam("feature_fraction_bynode", lower= 0.05, upper= 0.6),                           # 5% a 60% de las variables en cada corte
  makeNumericParam("bagging_fraction", lower= 0.2, upper= 0.9),                                   # 20% a 90% de los registros por arbol
  makeNumericParam("lambda_l1", lower= 0.0, upper= 2.0),                                          # 0 a 2
  makeNumericParam("lambda_l2", lower= 0.0, upper= 5.0),                                          # 0 a 5
  makeNumericParam("min_data_in_leaf", lower= 2.0, upper= 8.0, trafo= function(x) round(2^x) ),   # 4 a 256 en la BO (20 a 1280 en el modelo final, /0.2)
  makeNumericParam("min_sum_hessian_in_leaf", lower= -10.0, upper= 10.0, trafo= function(x) 2^x)  # 0.001 a 1024
)
```

### Resultado de 9510

Ampliar el espacio mejoró `rf` poco: 9510 le gana a 9508 en los 9 cortes, pero por entre 0,2 y 11,8 puntos, y el AUC subió 0,0008. En cada corte sigue entre 51 y 79 puntos debajo de `gbdt` (9506).

| Métrica | 9506 `gbdt` | 9508 `rf` | 9510 `rf` |
| --- | --- | --- | --- |
| AUC en 202107 (mejor de la BO) | 0,939886 | 0,930666 | 0,931454 (iter. 37) |
| Ganancia mínima – máxima (9 cortes) | 436,2 – 462,7 | 374,8 – 385,2 | 380,7 – 389,9 |
| Mejor corte | 462,748 (9.000) | 385,248 (8.000) | 389,915 (9.500) |
| Cortes en que supera a 9506 | — | 0 de 9 | 0 de 9 |
| Tiempo BO | 393,4 min | 94,9 min | 257,8 min |
| Tiempo modelo final (10 semillas) | 290,6 min | 111,6 min | 243,2 min |

| Hiperparámetro | Espacio 9510 | Mejor | Top 10 | Lectura |
| --- | --- | --- | --- | --- |
| `num_leaves` | 256 a 4.096 | 4.092 | 4.064 a 4.094 | **Otra vez en el techo** |
| `bagging_fraction` | 0,2 a 0,9 | 0,855 | 0,72 a 0,90 | Cerca del techo |
| `min_data_in_leaf` (BO / final) | 4 a 256 | 36 / 180 | 21 a 55 | Interior |
| `feature_fraction_bynode` | 0,05 a 0,6 | 0,170 | 0,09 a 0,26 | Interior |
| `num_iterations` | 128 a 1.024 | 938 | 365 a 1.007 | Interior y plano |
| `lambda_l1` / `lambda_l2` | 0 a 2 / 0 a 5 | 0,10 / 0,12 | casi 0 | Sin regularización |

- **La diferencia con `gbdt` es real, no un error del proceso:** aparece igual en el AUC de 202107 (que no depende de Kaggle) y en la ganancia de septiembre, con el mismo notebook, meses y semilla.
- **Es la diferencia esperable entre los métodos:** `rf` combina árboles independientes y solo baja la varianza; `gbdt` encadena árboles que corrigen los errores de los anteriores y también baja el sesgo. Con pocos positivos y señal débil, eso pesa.
- **`rf` pide árboles sin límite**, pero duplicar las hojas solo sumó +0,0008 de AUC y entre 0,2 y 11,8 puntos por corte, y duplicó los tiempos (además, la máquina se trabó o se cortó dos veces).
- **La BO no es exactamente reproducible:** el primer intento de 9510, que se cortó, había elegido 3.970 hojas y `bagging_fraction` 0,70; este eligió 4.092 y 0,855.

**Decisión:** se cierra `rf` con 9510 como representante. Seguir abriendo `num_leaves` no promete acortar una brecha de 51 a 79 puntos por corte y cada corrida ya lleva unas 8 horas.

## 10. Repetición con varias semillas (notebook completo, con BO)

Para medir cuánto varía el resultado por azar, cada método repite el notebook completo, incluida la BO, con otras semillas primigenias. Queda fija la configuración del experimento: meses, espacio de búsqueda, parámetros fijos, iteraciones de BO y semillerio. Los hiperparámetros los elige la BO en cada repetición, porque cambian con la semilla.

1. Copiar el notebook representante del método: 9506 para `gbdt`, 9510 para `rf` y el de `dart` del compañero.
2. Cambiar solo `PARAM$semilla_primigenia` y `PARAM$experimento` (carpeta nueva).
3. Correr todo, incluida la BO, y subir los 9 cortes a Kaggle.
4. Comparar los métodos por cómo se distribuyen las ganancias por corte entre semillas (cortes ganados, rangos), no por una corrida.

Repeticiones corridas. La configuración de cada una se verificó contra su `PARAM.yml` (meses, espacio de búsqueda, parámetros fijos, 50 iteraciones de BO y semillerio de 10): todas son iguales a 9506 (`gbdt`) o a 9510 (`rf`) salvo la semilla. 9516 (`rf`) se suma cuando termine:

| Método | Corridas | AUC en 202107, entre semillas | Ganancia por corte, entre semillas (mín–máx) | Mejor corte de cada corrida |
| --- | --- | --- | --- | --- |
| `gbdt` | 9: 9506, 9507, 9509, 9511, 9512, 9513, 9514, 9515, 9518 | 0,939886 a 0,941388 | 421.748 a 466.248 | Entre 8.500 y 10.500; 9.500 en 4 de las 9 |
| `rf` | 7: 9510, 9517, 9519, 9520, 9521, 9523, 9525 | 0,931454 a 0,932078 | 376.081 a 405.998 | Entre 9.000 y 11.500, sin un corte que domine |
| `dart` | 1: 9501 | 0,940357 | 342.415 a 357.581 | 11.000 |

- **Cuánto mueve la semilla:** dentro de un mismo corte, la distancia entre la mejor y la peor semilla va de 11,6 a 26,8 puntos en `gbdt` y de 8,8 a 28,3 en `rf`.
- **`gbdt` llega a puntos distintos con AUC parecido:** entre las 9 semillas, el mejor punto va de 1.500 a 4.080 árboles, `learning_rate` de 0,004 a 0,0135 y de 173 a 405 hojas. La meseta de AUC que se vio en 9504 se repite en todas.
- **`rf` pide lo mismo en todas las semillas:** en las 7, `num_leaves` queda entre 4.008 y 4.094 (techo 4.096) y `min_data_in_leaf` entre 22 y 43. Lo que se concluyó en las secciones 8 y 9 no depende de la semilla.

Las semillas no tienen que coincidir entre métodos: lo importante es que, dentro de cada método, todas las repeticiones usen la misma configuración.

Costo, como referencia general: cada repetición de `gbdt` o `rf` llevó de varias horas a cerca de un día de máquina, y cada intento de `dart`, alrededor de 40 horas. Los tiempos registrados no representan la corrida completa, así que no se usan para comparar métodos.

## 11. dart: corrida 9501

9501 es la única corrida de `dart` que terminó (semilla 196817, corrida por el compañero). Usa los mismos 29 meses de entrenamiento que 9506, la misma validación (202107), 50 iteraciones de BO y un semillerio de 10, con un espacio propio de 10 hiperparámetros. 9503 y 9505 fueron intentos de `dart` que no prosperaron y no tienen resultados. Por el tiempo de corrida, la cátedra indicó que con un solo `dart` alcanza para sacar conclusiones, así que `dart` no tiene repeticiones por semilla.

Espacio de la BO, mejor punto y lectura (la BO hizo 90 evaluaciones: 40 del diseño inicial, 4 × 10 hiperparámetros, más 50 iteraciones):

| Hiperparámetro | Escala BO | Rango real | Mejor | Lectura |
| --- | --- | --- | --- | --- |
| `num_iterations` | 2 a 10 | 4 a 1.024 | 1.015 | En el techo: 13 de las 20 mejores evaluaciones pasan de 955 |
| `num_leaves` | 2 a 11 | 4 a 2.048 | 162 | Interior |
| `learning_rate` | −7 a −0,1 | 0,0078 a 0,93 | 0,0646 | Interior |
| `feature_fraction` | 0,05 a 0,9 | igual | 0,257 | Interior |
| `min_sum_hessian_in_leaf` | −10 a 10 | 0,001 a 1.024 | 0,0041 | Interior |
| `lambda_l1` | 0 a 15 | igual | 11,36 | Interior |
| `lambda_l2` | 0 a 300 | igual | 43,6 | Interior |
| `min_data_in_leaf` | 5 a 12 | 32 a 4.096 | 160 (800 en el modelo final, dividido por 0,2) | Interior |
| `drop_rate` | 0,05 a 0,2 | igual | 0,141 | Interior |
| `max_drop` | 20 a 50 (entero) | igual | 20 | En el piso: 14 de las 20 mejores evaluaciones quedan en 22 o menos |

Fijo: `skip_drop` = 0,7. BO: 243,8 min (suma de `exec.time`); el notebook no guardó el tiempo del modelo final.

- **AUC alto, ganancia baja:** el AUC en 202107 (0,940357) cae dentro del rango de las 9 semillas de `gbdt` (0,939886 a 0,941388), pero en Kaggle 9501 queda último en los 9 cortes: entre 342.415 y 357.581, con el mejor corte en 11.000.
- **La BO quedó contra dos bordes:** `num_iterations` en el techo y `max_drop` en el piso. Con más árboles o menos árboles descartados por iteración la BO podría haber encontrado otro punto; no se corre otra vez por el tiempo de corrida, así que queda como limitación (sección 13).

## 12. Comparación final entre métodos

Con 9 semillas de `gbdt`, 7 de `rf` y una corrida de `dart`, el orden en Kaggle es el mismo en los 9 cortes: `gbdt`, `rf`, `dart`. Las bandas de los métodos no se tocan en ningún corte.

&#91;embedded content: Planilla Resultados\_experimentos, hoja Resultados Kaggle (leaderboard público) · gbdt 9506 a 9518, rf 9510 a 9525, dart 9501 · 9 cortes de 8.000 a 12.000 envíos\]

| Comparación | Cortes ganados | Diferencia por corte (puntos) |
| --- | --- | --- |
| Peor semilla de `gbdt` contra la mejor de `rf` | `gbdt`, 9 de 9 | 31,5 a 48,4 |
| `gbdt` contra `rf` con la misma semilla (7 pares) | `gbdt`, 9 de 9 en los 7 pares | 31,5 a 79,2 |
| Peor semilla de `rf` contra `dart` | `rf`, 9 de 9 | 25,2 a 33,7 |
| Peor semilla de `gbdt` contra `dart` | `gbdt`, 9 de 9 | 67,9 a 100,2 |

- **La distancia entre métodos supera la variación por semilla:** dentro de un corte, la semilla mueve hasta 26,8 puntos en `gbdt` y 28,3 en `rf`; la distancia más chica entre `gbdt` y `rf` es 31,5.
- **El AUC no ordena los métodos como Kaggle:** por AUC, `dart` (0,940357) queda junto a `gbdt` y `rf` debajo; en Kaggle, `dart` queda último. El AUC de la BO sirve para elegir hiperparámetros dentro de un método, no para comparar métodos.
- **Mejor corte:** entre 8.500 y 10.500 en `gbdt`, entre 9.000 y 11.500 en `rf` y 11.000 en `dart`.

**Conclusión:** `gbdt` es el método a usar. La ventaja sobre `rf` está respaldada por 7 semillas por lado y por la comparación con la misma semilla. La ventaja sobre `dart` se apoya en una sola corrida, con la BO contra dos bordes, así que es más débil; aun así, la distancia (67,9 a 100,2 puntos) es mucho mayor que lo que mueve la semilla en `gbdt`.

## 13. Limitaciones y próximos pasos

- **Leaderboard público:** todas las ganancias son del leaderboard público de Kaggle.
- **Un solo `dart`:** por el tiempo de corrida hay una sola corrida completa, con la BO contra el techo de `num_iterations` y el piso de `max_drop`. La conclusión frente a `dart` es más débil que frente a `rf`.
- **La BO optimiza AUC, no ganancia:** más AUC no garantiza más ganancia en Kaggle, ni dentro de un método (9504 vs 9502) ni entre métodos (`dart` vs `gbdt`).
- **`num_iterations` sale de la BO, sin `best_iter`:** el modelo final usa la misma cantidad de árboles que la BO, aunque entrene con más datos (en la tarea el óptimo final fue cerca del doble).
- **Tiempos registrados:** cubren la BO (y el modelo final cuando la corrida no se cortó), no la corrida completa; sirven solo como referencia general.
- **Cada corrida guarda su propia carpeta:** ninguna debe escribir en la carpeta de otra.
