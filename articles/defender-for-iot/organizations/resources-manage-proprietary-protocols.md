---
title: Administración de protocolos propietarios (Horizon)
description: Horizon de Defender para IoT ofrece un entorno de desarrollo abierto (ODE) que se usa para proteger los dispositivos IoT e ICS que ejecutan protocolos propietarios.
ms.date: 12/12/2020
ms.topic: reference
ms.openlocfilehash: cf38c79d3703fd1beb3a402587b7d388e89ce14e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114673841"
---
# <a name="defender-for-iot-horizon"></a>Horizon de Defender para IoT

Horizon de Defender para IoT incluye un entorno de desarrollo abierto (ODE) que se usa para proteger los dispositivos IoT e ICS que ejecutan protocolos propietarios.

Horizon proporciona:

  - Compatibilidad total e ilimitada con los protocolos propietarios y personalizados comunes, y con aquellos protocolos que se desvían de cualquier estándar. 
  - Un nuevo nivel de flexibilidad y ámbito para el desarrollo de DPI.
  - Una herramienta que amplía exponencialmente la visibilidad y el control sin necesidad de actualizar a nuevas versiones.
  - La seguridad de permitir el desarrollo propietario sin divulgar información confidencial.

Use el SDK de Horizon para diseñar complementos de disección que descodifiquen el tráfico de red para que se pueda procesar mediante los programas de análisis de red automatizados de Defender para IoT.

Las herramientas de disección de protocolos se desarrollan como complementos externos y se integran con una amplia gama de servicios de Defender para IoT, como los servicios que proporcionan funcionalidades de supervisión, alertas e informes.

Póngase en contacto con <ms-horizon-support@microsoft.com> para más información sobre cómo trabajar con el SDK de Open Development Environment (ODE) y crear complementos de protocolo.

Una vez desarrollado el complemento, puede usar la consola web de Horizon para:

  - Cargar el complemento
  - Habilitar y deshabilitar complementos  
  - Supervisar y depurar el complemento para evaluar el rendimiento
  - Crear alertas personalizadas basadas en protocolos propietarios. Mostrarlas en la consola y reenviarlas a los proveedores asociados. 

    :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-plugin.png" alt-text="Carga del complemento de Horizon."::: 

Esta característica está disponible para los usuarios de soporte técnico, Cyberx o administradores.

Para iniciar sesión en la consola de Horizon:

1. Inicie sesión en el sensor mediante la CLI.
2. En el archivo `/var/cyberx/properties/horizon.properties`, cambie la propiedad `ui.enabled` a `true` (`horizon.properties:ui.enabled=true`).
3. Inicie sesión en la consola del sensor. 
4. Seleccione **Horizon** en el menú principal. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-from-the-menu.png" alt-text="Selección de Horizon en el menú principal.":::  

La consola de Horizon muestra los complementos de infraestructura que proporciona Defender para IoT y cualquier otro complemento que haya creado y cargado. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/infrastructure.png" alt-text="Captura de pantalla de la infraestructura de Horizon.":::

## <a name="upload-plugins"></a>Carga de complementos

Después de crear y probar el complemento de disección propietario, puede cargarlo y supervisarlo desde la consola de Horizon.

Para cargar:

1. Seleccione **UPLOAD** (Cargar) desde la consola.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/upload-a-plugin.png" alt-text="Selección de la carga del complemento.":::

2. Arrastre o busque el complemento. Si se produce un error en la carga, se presentará un mensaje de error.

Póngase en contacto con <ms-horizon-support@microsoft.com> para más información sobre cómo trabajar con el SDK de Open Development Environment (ODE) y crear complementos de protocolo.

## <a name="enable-and-disable-plugins"></a>Habilitación y deshabilitación de complementos

Use el botón de alternancia para habilitar y deshabilitar los complementos. Cuando se deshabilitan, el tráfico ya no se supervisa.

No se pueden deshabilitar los complementos de infraestructura.

## <a name="monitor-plugin-performance"></a>Supervisión del rendimiento del complemento 

La ventana Overview (Información general) de la consola de Horizon proporciona información básica acerca de los complementos que ha cargado y permite deshabilitarlos y habilitarlos.

:::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-overview.png" alt-text="Captura de pantalla de la página de información general de Horizon."::: 

| Application | Nombre del complemento que ha cargado. |
|--|--|
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/toggle-icon.png" border="false"::: | Activar o desactivar el complemento. El sensor no controlará el tráfico del protocolo definido en el complemento al desactivar el complemento. |
| Hora | Hora en la que se analizaron los datos por última vez. Se actualiza cada cinco segundos. |
| PPS | Número de paquetes por segundo. |
| Ancho de banda | Ancho de banda promedio detectado en los últimos cinco segundos. |
| Malforms (Formato incorrecto) | Se utilizan validaciones de formato incorrecto después de que el protocolo se haya validado de forma positiva. Si se produce un error al procesar los paquetes basados en el protocolo, se devuelve una respuesta de error.<br/> <br />Esta columna indica el número de errores de formato incorrecto en los últimos cinco segundos. |
| Advertencias | Los paquetes coinciden con la estructura y la especificación, pero hay un comportamiento inesperado según la configuración de advertencias del complemento. |
| Errors | Número de paquetes con errores en las validaciones básicas del protocolo sobre la coincidencia del paquete con las definiciones del protocolo.  El número que se muestra aquí indica que se han detectado n errores en los últimos cinco segundos. |
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/monitor-icon.png" border="false"::: | Revise los detalles sobre formato incorrecto y las advertencias detectadas para el complemento. |

### <a name="plugin-performance-details"></a>Detalles de rendimiento del complemento

Puede supervisar el rendimiento del complemento en tiempo real mediante el análisis del número de elementos con formato incorrecto y las advertencias detectadas para el complemento. Hay una opción disponible para inmovilizar la pantalla y exportarla para una posterior investigación.

:::image type="content" source="media/how-to-manage-proprietary-protocols/snmp-monitor.png" alt-text="Captura de pantalla de la información general de supervisión de SNMP.":::

### <a name="horizon-logs"></a>Registros de Horizon

La información de disección de Horizon está disponible para su exportación en los detalles de disección, los registros de disección y los registros de exportaciones.

:::image type="content" source="media/how-to-manage-proprietary-protocols/export-logs-details.png" alt-text="Detalles de disección de los registros de exportación.":::

## <a name="trigger-horizon-alerts"></a>Desencadenamiento de alertas de Horizon 

Mejore la administración de alertas en la empresa mediante el desencadenamiento de alertas personalizadas para cualquier protocolo basado en la disección del tráfico del marco de Horizon. 

Estas alertas se pueden usar para comunicar información:  

  - Sobre las detecciones de tráfico basadas en protocolos y protocolos subyacentes en un complemento propietario de Horizon.

  - Sobre una combinación de campos de protocolo de todas las capas del protocolo. Por ejemplo, en un entorno que ejecute MODBUS, puede que desee generar una alerta cuando el sensor detecte un comando de escritura en un registro de memoria en una dirección IP específica y un destino Ethernet, o una alerta cuando se realiza cualquier tipo de acceso a un dirección IP específica.

Las alertas se desencadenan cuando se cumplen las condiciones de la regla de alertas de Horizon.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/custom-alert-rules.png" alt-text="Reglas personalizadas de ejemplo para Horizon.":::

Además, el uso de las alertas personalizadas de Horizon le permite escribir sus propios títulos y mensajes de alerta. Los campos y valores de protocolo resueltos se pueden insertar también en el texto del mensaje de alerta.

El uso de la mensajería y activación de alertas personalizadas y basadas en condiciones ayuda a identificar la actividad de red específica y a actualizar de forma eficaz a los equipos operativos, de TI y de seguridad.

### <a name="working-with-horizon-alerts"></a>Uso de las alertas de Horizon

Las alertas generadas por las reglas de alertas personalizadas de Horizon se muestran en la ventana de alertas de la consola de administración y el sensor y en los sistemas asociados integrados cuando se usan reglas de reenvío. 

Las alertas generadas por Horizon se pueden confirmar o silenciar. La opción de aprendizaje no está disponible para las alertas personalizadas, ya que los eventos de alerta no se pueden aprender en la línea de base de directivas.

La información de las alertas se reenvía a los proveedores asociados cuando se usan reglas de reenvío.

La gravedad de las alertas personalizadas de Horizon es crítica.

Las alertas personalizadas de Horizon incluyen texto estático en la sección **Manage this Event** (Administrar este evento) que indica que la alerta fue generada por el equipo de seguridad de la organización.

### <a name="required-permissions"></a>Permisos necesarios

Los usuarios definidos como usuarios de Defender para IoT tienen permisos para crear reglas de alertas personalizadas de Horizon.

### <a name="about-creating-rule-conditions"></a>Acerca de la creación de condiciones de reglas

Las condiciones de reglas describen el tráfico de red que se debe detectar para desencadenar la alerta. Las condiciones de reglas pueden constar de uno o varios conjuntos de campos, operadores y valores. Cree conjuntos de condiciones mediante el uso de **AND**.

Cuando se cumple la condición de la regla o el conjunto de condiciones, se envía la alerta. Se le notificará si la lógica de la condición no es válida.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/and-condition.png" alt-text="Uso de la condición AND para la regla personalizada.":::

También puede crear varias reglas para un protocolo. Esto significa que se desencadenará una alerta para cada regla que haya creado cuando se cumplan las condiciones de la regla.

### <a name="about-titles-and-messages"></a>Acerca de los títulos y mensajes

Los mensajes de alerta pueden contener los caracteres alfanuméricos que especifique, así como las variables de tráfico detectadas. Por ejemplo, puede incluir las direcciones de origen y de destino detectadas en los mensajes de alerta. Se admiten varios idiomas.

### <a name="about-alert-recommendations"></a>Acerca de las recomendaciones de alertas 

Las alertas personalizadas de Horizon incluyen texto estático en la sección **Manage this Event** (Administrar este evento) que indica que la alerta fue generada por el equipo de seguridad de la organización. También puede trabajar con los comentarios de las alertas para mejorar la comunicación entre los usuarios y equipos que lean la alerta. 

## <a name="create-horizon-alert-rules"></a>Creación de reglas de alertas de Horizon

En este artículo se describe cómo crear la regla de alertas.

Para crear alertas personalizadas de Horizon:

1. Haga clic con el botón derecho en un complemento del menú de complementos en la consola de Horizon.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/plugins-menu.png" alt-text="Clic con el botón derecho en un complemento del menú.":::

2. Seleccione **Horizon Custom Alerts** (Alertas personalizadas de Horizon). Se abre la ventana **Rule** (Regla) del complemento que ha seleccionado.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/sample-rule-window.png" alt-text="Se abre la ventana de la regla de ejemplo del complemento.":::

3. Escriba un título en el campo Title (Título).

4. Escriba un mensaje de alerta en el campo Message (Mensaje). Use llaves `{}` para incluir los parámetros de campo detectados en el mensaje. Al escribir el primer corchete, aparecen los campos correspondientes.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/rule-window.png" alt-text="Uso de {} en la ventana de la regla para incluir los campos detectados.":::

5. Defina las condiciones de la alerta.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/define-conditions.png" alt-text="Definir las condiciones de la alerta.":::

6. Seleccione una **Variable**. Las variables representan los campos configurados en el complemento.

7. Seleccione un valor para **Operator** (Operador):

    - Igual a
    
    - No es igual a
    
    - Menor que
    
    - Menor o igual que
    
    - Mayor que
    
    - Mayor o igual que

8. Escriba un número para el campo **Value** (Valor). Si la variable seleccionada es una dirección MAC o una dirección IP, el valor se debe convertir de una dirección con decimales y puntos a formato decimal. Use una herramienta de conversión de direcciones IP; por ejemplo, <https://www.ipaddressguide.com/ip>.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/ip-address-value.png" alt-text="Valor de la dirección IP traducido.":::

9. Seleccione **AND** (Y) para crear un conjunto de condiciones.

10. Seleccione **SAVE** (GUARDAR). La regla se agrega a la sección Rules (Reglas).

### <a name="edit-and-delete-horizon-custom-alert-rules"></a>Edición y eliminación de reglas de alertas personalizadas de Horizon

Use las opciones de edición y eliminación según sea necesario. Ciertas reglas están insertadas y no se pueden editar ni eliminar.

### <a name="create-multiple-rules"></a>Creación de varias reglas

Cuando se crean varias reglas, se desencadenan alertas cuando las condiciones o los conjuntos de condiciones de una regla son válidos.

## <a name="see-also"></a>Consulte también

[Visualización de información de las alertas](how-to-view-information-provided-in-alerts.md)
