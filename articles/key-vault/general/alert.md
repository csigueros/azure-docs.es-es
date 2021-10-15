---
title: Configuración de alertas de Azure Key Vault
description: Aprenda a crear alertas para supervisar el estado del almacén de claves.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: d74cb93cd28f3dc02b37a6c29832d9e8412ef098
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458915"
---
# <a name="configure-azure-key-vault-alerts"></a>Configuración de alertas de Azure Key Vault

Una vez que haya empezado a usar Key Vault para almacenar los secretos de producción, es importante supervisar el estado del almacén de claves para asegurarse de que el servicio funciona según lo previsto. 

Cuando empiece a escalar el servicio, aumentará el número de solicitudes que se envían al almacén de claves. Este aumento puede aumentar la latencia de las solicitudes. En casos extremos, puede hacer que las solicitudes se limiten y afecten al rendimiento del servicio. También debe saber si el almacén de claves envía un número inusual de códigos de error, para que pueda resolver rápidamente cualquier problema con una configuración con una directiva de acceso o el firewall. 

En este artículo se muestra cómo configurar alertas a umbrales especificados para que pueda avisar a su equipo para que realice una acción inmediatamente si el almacén de claves tiene un estado incorrecto. Puede configurar alertas que envíen un correo electrónico (preferiblemente a una lista de distribución del equipo), desencadenar una notificación de Azure Event Grid, o llamar a un número de teléfono o enviar un mensaje de texto a este. 

Puede elegir entre estos tipos de alerta:

- Una alerta estática basada en un valor fijo
- Una alerta dinámica que le notifique si una métrica supervisada supera el límite medio del almacén de claves un cierto número de veces en un intervalo de tiempo definido.

> [!IMPORTANT]
> Tenga en cuenta que las alertas configuradas recientemente pueden tardar hasta 10 minutos en empezar a enviar notificaciones. 

Este artículo se centra en las alertas de Key Vault. Para obtener información sobre la información de Key Vault, que combina registros y métricas para proporcionar una solución de supervisión global, consulte [Supervisión de un servicio de Key Vault con información de Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights).

## <a name="configure-an-action-group"></a>Configuración de un grupo de acciones 

Un grupo de acciones es una lista configurable de notificaciones y propiedades. El primer paso para configurar alertas es crear un grupo de acciones y elegir un tipo de alerta:

1. Inicie sesión en Azure Portal.
2. Busque **Alertas** en el cuadro de búsqueda.
3. Seleccione **Administrar acciones**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla en la que se resalta el botón Administrar acciones.](../media/alert-6.png)

4. Seleccione **+ Agregar grupo de acciones**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que resalta el botón para agregar un grupo de acciones](../media/alert-7.png)

5. Elija el valor **Tipo de acción** para el grupo de acciones. En este ejemplo, crearemos una alerta por correo electrónico y SMS. Seleccione **Correo electrónico/SMS/Inserción/Voz**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que resalta las selecciones para agregar un grupo de acciones](../media/alert-8.png)

6. En el cuadro de diálogo, escriba los detalles de correo electrónico y SMS y, a continuación, seleccione **Aceptar**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra las selecciones para agregar un correo electrónico y una alerta de mensaje de S MS](../media/alert-9.png)

## <a name="configure-alert-thresholds"></a>Configuración de los umbrales de alerta 

A continuación, cree una regla y configure los umbrales que desencadenarán una alerta:

1. Seleccione el recurso de Key Vault en Azure Portal y seleccione **Alertas** en **Supervisión**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra la opción de menú Alertas en la sección Supervisión](../media/alert-10.png)

2. Seleccione **Nueva regla de alertas**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla en la que se muestra el botón para agregar una nueva regla de alertas](../media/alert-11.png)

3. Seleccione el ámbito de la regla de alertas. Puede seleccionar un solo almacén o varios. 

   > [!IMPORTANT]
   > Si se seleccionan varios almacenes para el ámbito de las alertas, todos ellos deben estar en la misma región. Tendrá que configurar reglas de alerta independientes para almacenes en regiones diferentes. 

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra cómo se puede seleccionar un almacén.](../media/alert-12.png)

4. Seleccione los umbrales que definen la lógica de las alertas y, a continuación, seleccione **Agregar**. El equipo de Key Vault recomienda configurar los siguientes umbrales: 

    + La disponibilidad de Key Vault baja por debajo del 100 % (umbral estático).
    + La latencia de Key Vault es superior a 500 ms (umbral estático). 
    + La saturación general del almacén es mayor que el 75 % (umbral estático). 
    + La saturación total del almacén supera el promedio (umbral dinámico)
    + Los códigos de error totales son mayores que el promedio (umbral dinámico). 

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra dónde se seleccionan las condiciones para las alertas.](../media/alert-13.png)

### <a name="example-configure-a-static-alert-threshold-for-latency"></a>Ejemplo: Configuración de un umbral de alerta estático para la latencia

1. Seleccione **Latencia general de la API de servicio** como el nombre de la señal

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra la selección de un nombre de señal](../media/alert-14.png)

1. Utilice los siguientes parámetros de configuración:

   + Establezca **Umbral** en **Estático**. 
   + Establezca el **operador** en **Mayor que**.
   + Establezca el **tipo de agregación** en **Media**.
   + Establezca **Valor de umbral** en **500**.
   + Establezca **Granularidad de agregación (período)** en **5 minutos**.
   + Establezca **Frecuencia de evaluación** en **Cada minuto**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra la lógica configurada para un umbral de alerta estática](../media/alert-15.png)

1. Seleccione **Listo**.  

### <a name="example-configure-a-dynamic-alert-threshold-for-vault-saturation"></a>Ejemplo: Configuración de un umbral de alerta dinámico para la saturación del almacén 

Si usa una alerta dinámica, podrá ver los datos históricos del almacén de claves que ha seleccionado. El área azul representa el uso medio del almacén de claves. El área roja muestra los picos que habrían desencadenado una alerta, siempre que se cumplan otros criterios de la configuración de la alerta. Los puntos rojos muestran instancias de infracciones en las que se han cumplido los criterios de la alerta durante la ventana de tiempo agregada. 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra un gráfico de la saturación total del almacén.](../media/alert-16.png)

Puede establecer una alerta para que se active después de un determinado número de infracciones en un período de tiempo establecido. Si no desea incluir datos pasados, hay una opción para excluirlos en la configuración avanzada. 

1. Utilice los siguientes parámetros de configuración:

   + Establezca **Umbral** en **Dinámico**. 
   + Establezca el **operador** en **Mayor que**.
   + Establezca el **tipo de agregación** en **Media**.
   + Establezca **Sensibilidad del umbral** en **Intermedio**.
   + Establezca **Granularidad de agregación (período)** en **5 minutos**.
   + Establezca **Frecuencia de evaluación** en **Cada minuto**.
   + Configure **Opciones avanzadas** (opcional). 

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra la lógica configurada para un umbral de alerta dinámica](../media/alert-17.png)

1. Seleccione **Listo**.

1. Seleccione **Agregar** para agregar el grupo de acciones que ha configurado.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra el botón para agregar un grupo de acciones](../media/alert-18.png)

1. En los detalles de la alerta, habilite la alerta y asigne una gravedad.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra dónde habilitar la alerta y asignar una gravedad.](../media/alert-19.png)

1. Cree la alerta. 

### <a name="example-email-alert"></a>Ejemplo de alerta por correo electrónico 

Si ha seguido todos los pasos anteriores, recibirá alertas por correo electrónico cuando el almacén de claves cumpla los criterios de alerta que ha configurado. La siguiente alerta por correo electrónico es un ejemplo. 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla en la que se resalta la información necesaria para configurar una alerta por correo electrónico.](../media/alert-20.png)

## <a name="next-steps"></a>Pasos siguientes

Use las herramientas que ha configurado en este artículo para supervisar activamente el estado de su almacén de claves.

- [Supervisión de Key Vault](monitor-key-vault.md)
- [Supervisión de la referencia de los datos de Key Vault](monitor-key-vault-reference.md)
