# Datos

Este proyecto usa el conjunto de datos **SECOP II** (contratación pública en Colombia, 2024), publicado por Colombia Compra Eficiente en el portal de Datos Abiertos Colombia.

Fuente: https://www.datos.gov.co/Gastos-Gubernamentales/SECOP-2-2024/7myp-7jxp/about_data

## Descarga

1. Entra al link de arriba.
2. Click en "Export" (o el ícono de descarga) y elige el formato CSV.
3. Guarda el archivo en esta carpeta (`data/`) para que los notebooks lo encuentren en una ruta relativa.

## Por qué no está incluido en el repo

El dataset pesa cerca de 200 MB, por lo que no se sube directamente a GitHub. Para reproducir el análisis, descárgalo desde el link y colócalo aquí antes de correr los notebooks.

## Contenido esperado

- ~900.000 registros de contratos electrónicos
- Variables usadas en el análisis: sector, rama, tipo de contrato, modalidad de contratación, entidad centralizada, valor del contrato, duración, fechas de firma/inicio/fin, estado BPIN, entre otras (ver README principal para el detalle completo).
