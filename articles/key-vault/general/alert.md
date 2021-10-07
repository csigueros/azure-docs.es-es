---
title: Alertas de Azure Key Vault
description: Cree un panel para supervisar el estado de su almacén de claves y configurar alertas.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 4c1f63bdc13822b7eb48dc5410a990dc75f3453e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060789"
---
# <a name="alerting-for-azure-key-vault"></a>Alertas de Azure Key Vault

## <a name="overview"></a>Información general

Una vez que haya empezado a usar Key Vault para almacenar los secretos de producción, es importante supervisar el estado del almacén de claves para asegurarse de que el servicio funciona según lo previsto. Cuando empiece a escalar el servicio, aumentará el número de solicitudes que se envían al almacén de claves. Es probable que esto aumente la latencia de las solicitudes y, en casos extremos, puede hacer que las solicitudes se limiten, lo cual afectará al rendimiento del servicio. También debe recibir una alerta si el almacén de claves envía un número inusual de códigos de error, para que pueda recibir una notificación rápida de cualquier problema de configuración con la directiva de acceso o el firewall. En este documento se tratarán los siguientes temas:

+ Métricas básicas de Key Vault para supervisar
+ Configuración de las métricas y creación de un panel
+ Creación de alertas en los umbrales especificados

Azure Monitor para Key Vault combina los registros y las métricas para proporcionar una solución de supervisión global. [Obtenga más información acerca de Azure Monitor para Key Vault aquí](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights).

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Configuración de alertas en Key Vault 

En esta sección se muestra cómo configurar alertas en el almacén de claves para que pueda avisar a su equipo para que realice una acción inmediatamente si el almacén de claves tiene un estado incorrecto. Puede configurar alertas que envíen un correo electrónico, preferiblemente a una lista de distribución del equipo, desencadenar una notificación de Event Grid, o llamar a un número de teléfono o enviar un mensaje de texto a este. También puede elegir alertas estáticas basadas en un valor fijo o una alerta dinámica que le avise si una métrica supervisada supera el límite medio del almacén de claves un cierto número de veces en un intervalo de tiempo definido. 

> [!IMPORTANT]
> Tenga en cuenta que las alertas configuradas recientemente pueden tardar hasta 10 minutos en empezar a enviar notificaciones. 

### <a name="configure-an-action-group"></a>Configuración de un grupo de acciones 

Un grupo de acciones es una lista configurable de notificaciones y propiedades.

1. Inicie sesión en Azure Portal.
2. Busque **Alertas** en el cuadro de búsqueda.
3. Seleccione **Administrar acciones**.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla en la que se resalta el botón Administrar acciones.](../media/alert-6.png)

4. Seleccione **+ Agregar grupo de acciones**.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla en la que se resalta el botón + Agregar grupo de acciones.](../media/alert-7.png)

5. Elija el **tipo de acción** para el grupo de acciones. En este ejemplo, vamos a crear una alerta por correo electrónico.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla en la que se resaltan los campos necesarios para agregar un grupo de acciones.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra los campos que se necesitan para agregar una alerta por correo electrónico o mensaje SMS.](../media/alert-9.png)

6. Haga clic en **Aceptar** en la parte inferior de la página. Ha creado un grupo de acciones correctamente. 

Ahora que ha configurado un grupo de acciones, vamos a configurar los umbrales de alerta del almacén de claves. 

### <a name="configure-alert-thresholds"></a>Configuración de los umbrales de alerta 

1. Seleccione el recurso de Key Vault en Azure Portal y seleccione **Alertas** en **Supervisión**.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra la opción de menú Alertas en la sección Supervisión.](../media/alert-10.png)

2. Seleccione **Nueva regla de alertas**.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra el botón + Nueva regla de alertas.](../media/alert-11.png)

3. Seleccione el ámbito de la regla de alertas. Puede seleccionar un solo almacén o varios. 

> [!IMPORTANT]
> Tenga en cuenta que si se seleccionan varios almacenes para el ámbito de las alertas, todos ellos deben estar en la misma región. Tendrá que configurar reglas de alerta independientes para almacenes en regiones diferentes. 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra cómo se puede seleccionar un almacén.](../media/alert-12.png)

4. Seleccione las condiciones de las alertas. Puede elegir cualquiera de las siguientes señales y definir la lógica de las alertas. El equipo de Key Vault recomienda configurar los siguientes umbrales de alerta. 

    + La disponibilidad de Key Vault cae por debajo del 100 % (umbral estático)
    + La latencia de Key Vault es superior a 500 ms (umbral estático) 
    + La saturación total del almacén es superior al 75 % (umbral estático) 
    + La saturación total del almacén supera el promedio (umbral dinámico)
    + Códigos de error totales superiores al promedio (umbral dinámico) 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra dónde se seleccionan las condiciones para las alertas.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Ejemplo 1: Configuración de un umbral de alerta estático para la latencia

Seleccione **Latencia general de la API de servicio** como el nombre de la señal
> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra el nombre de señal Latencia general de la API de servicio.](../media/alert-14.png)

Consulte los siguientes parámetros de configuración.

+ Establezca el umbral en **Estático**. 
+ Establezca el operador en **Mayor que**.
+ Establezca el tipo de agregación en **Media**.
+ Establezca el valor de umbral en **500**.
+ Establezca el período de agregación en **5 minutos**.
+ Establezca la frecuencia de evaluación en **1 minuto**.
+ Seleccione **Listo**  

> [!div class="mx-imgBorder"]
> ![Captura de pantalla en la que se resalta la lógica de alerta configurada.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Ejemplo 2: Configuración de un umbral de alerta dinámico para la saturación del almacén 

Si usa una alerta dinámica, podrá ver los datos históricos del almacén de claves que ha seleccionado. El área azul representa el uso medio del almacén de claves. El área roja muestra los picos que habrían desencadenado una alerta, siempre que se cumplan otros criterios de la configuración de la alerta. Los puntos rojos muestran instancias de infracciones en las que se han cumplido los criterios de la alerta durante la ventana de tiempo agregada. Puede establecer una alerta para que se active después de un determinado número de infracciones en un período de tiempo establecido. Si no desea incluir datos pasados, hay una opción para excluir los datos antiguos en la configuración avanzada. 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra un gráfico de la saturación total del almacén.](../media/alert-16.png)

Consulte los siguientes parámetros de configuración.

+ Establezca el umbral en **Dinámico**. 
+ Establezca el operador en **Mayor que**.
+ Establezca el tipo de agregación en **Media**.
+ Establezca la sensibilidad del umbral en **Intermedio**.
+ Establezca el período de agregación en **5 minutos**.
+ Establezca la frecuencia de evaluación en **1 minuto**.
+ **Opcional** Configure las opciones avanzadas. 
+ Seleccione **Listo**

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de Azure Portal](../media/alert-17.png)

5. Agregue el grupo de acciones que ha configurado.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra cómo agregar un grupo de acciones.](../media/alert-18.png)

6. Habilite la alerta y asigne una gravedad.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra dónde habilitar la alerta y asignar una gravedad.](../media/alert-19.png)

7. Crear la alerta 

### <a name="example-email-alert"></a>Ejemplo de alerta por correo electrónico 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla en la que se resalta la información necesaria para configurar una alerta por correo electrónico.](../media/alert-20.png)

## <a name="next-steps"></a>Pasos siguientes

Enhorabuena, ha creado correctamente un panel de supervisión y configurado las alertas para el almacén de claves.

Si ha seguido todos los pasos anteriores, debería recibir alertas por correo electrónico cuando el almacén de claves cumpla los criterios de alerta que ha configurado. A continuación se muestra un ejemplo. Use las herramientas que ha configurado en este artículo para supervisar activamente el estado de su almacén de claves.

- [Supervisión de Key Vault](monitor-key-vault.md)
- [Supervisión de la referencia de los datos de Key Vault](monitor-key-vault-reference.md)