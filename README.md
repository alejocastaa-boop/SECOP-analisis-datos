Minería de datos aplicada a la contratación pública en Colombia: análisis del SECOP II
Descripción

Análisis de más de 900.000 contratos públicos registrados en el SECOP II, buscando identificar patrones estructurales en la contratación estatal, caracterizar perfiles institucionales mediante clustering, y detectar anomalías administrativas o jurídicas que podrían orientar auditorías.

El trabajo se desarrolló en tres entregas a lo largo del semestre: limpieza y reducción de dimensionalidad, incrustación no lineal y clusterización, y detección de anomalías con reglas de asociación.

Preguntas de investigación


¿Se puede segmentar el comportamiento institucional del Estado sin depender solo del monto de los contratos?
¿Hay patrones de estacionalidad temporal o de flexibilización jurídica en la operación del Estado?
¿Los algoritmos de detección de anomalías identifican irregularidades que las auditorías convencionales no detectan?


Estructura del repositorio

secop-analisis-datos/
├── README.md
├── data/
│   └── README.md
├── 01_limpieza_reduccion/
├── 02_incrustacion_clusterizacion/
└── 03_deteccion_anomalias/

Datos

Dataset: SECOP II - Contratos Electrónicos, publicado por Colombia Compra Eficiente en el portal de Datos Abiertos Colombia.

https://www.datos.gov.co/Estadisticas-Nacionales/SECOP-II-Contratos-Electronicos/jbjy-vk9h/explore

902.040 registros iniciales, 18 variables (4 categóricas nominales, 10 binarias, 4 cuantitativas). Después de filtrar outliers con Z-score robusto quedaron 889.087 registros.

El dataset pesa cerca de 200 MB y no está incluido en el repositorio. Instrucciones de descarga en data/README.md.

Corte 1 — Limpieza y reducción de dimensionalidad

No había datos faltantes en las variables seleccionadas; se eliminaron los registros con "No Definido" en pago_adelantado. Variables binarias codificadas 0/1, y las nominales (sector, rama, tipo_contrato, modalidad_contratacion) con One-Hot Encoding.

Se validó el tamaño muestral: la variable más exigente (valor_contrato, CV=3.59) requería mínimo 855.078 registros, y el dataset los supera.

Se probó PCA sobre las 4 variables cuantitativas pero se decidió mantenerlas en su escala original, porque la varianza queda repartida casi por igual entre los 4 componentes (entre 20.8% y 29.6% cada uno) y ninguno domina lo suficiente como para justificar la reducción.

Para las variables categóricas se aplicó MCA, reteniendo 35 componentes que explican el 66.88% de la varianza. sector y tipo_contrato concentran más del 80% de la contribución.

Corte 2 — Incrustación no lineal, clusterización y distribución de clases

Se aplicó Spectral Embedding (sobre una muestra de 5.000 registros) para proyectar el espacio de MCA en 3 dimensiones, capturando relaciones no lineales entre contratos.

Se compararon 6 algoritmos de clustering (K-Means, Agglomerative Ward/Single, Bisecting K-Means, Clique, Spectral Clustering). Se eligió K-Means con k=4, validado con índice ARI (K-Means vs. Bisecting: 0.975; K-Means vs. Spectral: 0.936) y silueta de 0.633.

ClústerParticipaciónPerfil061.7%Generalista — Salud, Protección, Deportes e Inclusión115.3%Servicio Público y Medio Ambiente, baja tasa de prórrogas29.5%Educación Nacional y Trabajo313.5%Proyectos de inversión estructurada, Servicio Público

Los Clústeres 1 y 3 concentran cerca del 61% de proyectos ligados al Banco de Proyectos de Inversión (BPIN).

Corte 3 — Detección de anomalías y reglas de asociación

Se probaron cuatro algoritmos de detección de anomalías (Envolvente Convexa, KDE, ECOD, One-Class SVM) sobre una muestra de 8.000 registros. Los casos más claros fueron contratos de mínima cuantía extendidos casi un año con prórrogas sucesivas, contratación bajo régimen especial en el sector Salud, y registros administrativos vacíos (valor $0, duración 0 días).

Con reglas de asociación (Apriori/FP-Growth) por clúster apareció un patrón de "gasto de cierre" en el Clúster 0: contratos de bajo valor, corta duración, contratación directa y prestación de servicios, con confianza de hasta 77.9%, lo que sugiere agotamiento presupuestal en el último trimestre del año fiscal. También se encontró una asociación fuerte entre la modalidad de régimen especial y el sector Salud en los Clústeres 1, 2 y 3.

Esto podría usarse para diseñar auditorías estratificadas: monitoreo de contratos del Clúster 0 que empiezan a adoptar atributos de larga duración, y filtros automáticos para depurar registros anómalos antes de análisis macroeconómicos.

Limitaciones: el análisis depende de la calidad del registro original en SECOP II, que tiene vacíos y errores de digitación. La discretización en cuartiles para Apriori también sacrifica granularidad. Como trabajo futuro quedaría un análisis de grafos por NIT de contratistas y modelos de anomalías basados en autoencoders.

Herramientas

Python, Pandas, Scikit-learn (PCA, Spectral Embedding, K-Means, One-Class SVM), MCA, Plotly, mlxtend (Apriori/FP-Growth). Estadística: V de Cramér, ANOVA, índice de Rand Ajustado.
