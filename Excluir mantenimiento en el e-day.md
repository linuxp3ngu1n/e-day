
Ejemplo de exclusión de mantenimiento

En el siguiente ejemplo, se evita el mantenimiento durante los cuatro días que abarcan desde el Black Friday hasta el Cyber Monday, el período de ventas de mayor volumen del año para muchas empresas minoristas. En este ejemplo, se muestra cómo evitar que tenga lugar un período de mantenimiento desde el Black Friday 2021 (26 de noviembre de 2021) hasta el Cyber Monday 2021 (29 de noviembre de 2021) desde la medianoche del Este (UTC-5) hasta las 23:59:59 Pacífico (UTC-8).


**gcloud container clusters update sample-cluster \
    --add-maintenance-exclusion-name black-friday \
    --add-maintenance-exclusion-start 2021-11-26T00:00:00-05:00 \
    --add-maintenance-exclusion-end 2021-11-29T23:59:59-08:00 \
    --add-maintenance-exclusion-scope no_upgrades**

https://cloud.google.com/kubernetes-engine/docs/how-to/maintenance-windows-and-exclusions#example-maintenance-exclusions
