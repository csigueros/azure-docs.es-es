---
title: Revise las alertas en Azure Stack Edge
description: Describe las alertas que se producen en dispositivos de Azure Stack Edge.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/14/2021
ms.author: alkohli
ms.openlocfilehash: 6db661698c0e7ee2ca19cd7ef17a5397b174667d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047948"
---
# <a name="review-alerts-on-azure-stack-edge"></a>Revise las alertas en Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo ver las alertas e interpretar su gravedad para eventos en dispositivos de Azure Stack Edge. Las alertas generan notificaciones en Azure Portal. El artículo incluye una referencia rápida para alertas de Azure Stack Edge.

## <a name="overview"></a>Información general

La hoja Alertas de un dispositivo de Azure Stack Edge permite revisar las alertas relacionadas con dispositivos de Azure Stack Edge en tiempo real. Desde esta hoja, puede supervisar de forma centralizada los problemas de estado de los dispositivos de Azure Stack Edge y la solución general de Azure Stack Edge de Microsoft Azure.

Lo que se muestra inicialmente es un resumen general de las alertas en cada nivel de gravedad. Puede explorar en profundidad para ver alertas individuales en cada nivel de gravedad.

[ ![Captura de pantalla de la hoja Alertas de un dispositivo de Azure Stack Edge en Azure Portal. Los detalles de la alerta se sobresalen. El elemento de menú Alertas está resaltado.](media/azure-stack-edge-alerts/azure-stack-edge-alerts-summary-and-detail.png) ](media/azure-stack-edge-alerts/azure-stack-edge-alerts-summary-and-detail.png#lightbox)


### <a name="alert-severity-levels"></a>Niveles de gravedad de alerta

Las alertas tienen distintos niveles de gravedad, en función de la repercusión de la situación de la alerta y de la necesidad de una respuesta ante esta. Los niveles de gravedad son:

- **Crítica** – Esta alerta es en respuesta a una condición que está afectando el rendimiento correcto del sistema. Se requiere una acción para asegurar que el servicio de Azure Stack Edge no se vea interrumpido.
- **Advertencia** : esta condición puede convertirse en crítica si no se resuelve. Debe investigar la situación y tomar las acciones necesarias para resolver el problema.
- **Informativo**: esta alerta contiene información que puede ser útil para el seguimiento y la administración del sistema.

### <a name="configure-alert-notifications"></a>Configurar notificaciones de alerta

También puede enviar notificaciones de alertas por correo electrónico para eventos en dispositivos de Azure Stack Edge. Para administrar estas notificaciones de alerta, cree reglas de acción. Las reglas de acción pueden desencadenar o suprimir notificaciones de alerta para eventos de dispositivo dentro de un grupo de recursos, una suscripción de Azure o en un dispositivo. Para obtener más información, consulte [Uso de reglas de acción para administrar notificaciones de alerta](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).

## <a name="alerts-quick-reference"></a>Referencia rápida de alertas

En las tablas siguientes se muestran algunas de las alertas de Azure Stack Edge que puede ejecutar, con descripciones y acciones recomendadas. Las alertas se agrupan en las siguientes categorías:

* [Alertas de conectividad de la nube](#cloud-connectivity-alerts)
* [Alertas de proceso perimetral](#edge-compute-alerts)
* [Alertas de Azure Resource Manager local](#local-azure-resource-manager-arm-alerts)
* [Alertas de rendimiento](#performance-alerts)
* [Alertas de almacenamiento](#storage-alerts)
* [Alertas de seguridad](#security-alerts)
* [Alertas de Key Vault](#key-vault-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de actualización](#update-alerts)
* [Alertas de máquina virtual](#virtual-machine-alerts)

> [!NOTE]
> En las tablas de alertas siguientes, vemos como algunas alertas se desencadenan mediante más de un tipo de evento. Si los eventos tienen diferentes acciones recomendadas, la tabla tiene una entrada de alerta para cada uno de los eventos.

## <a name="cloud-connectivity-alerts"></a>Alertas de conectividad de la nube

Las siguientes alertas se generan cuando se produce un error en la conexión a un dispositivo de Azure Stack Edge o cuando no se detecta ningún latido.

| Texto de la alerta                       | severity | Descripción / Acción recomendada |
|----------------------------------|----------|----------------------------------|
| No se pudo establecer conexión con Azure.  | Crítico | Compruebe la conexión a Internet. En la interfaz de usuario web local del dispositivo, vaya a **Solución de problemas** > **Pruebas de diagnóstico**. Ejecute la prueba de diagnóstico de **Conectividad a Internet**. |
| Se perdió el latido del dispositivo. | Crítico | Si el dispositivo está desconectado, no puede comunicarse con el servicio de Azure. Esto puede deberse a uno de los siguientes motivos:<ul><li>No hay conectividad a Internet.<br>Compruebe la conexión a Internet. En la interfaz de usuario web local del dispositivo, vaya a **Solución de problemas** > **Pruebas de diagnóstico**. Ejecute las pruebas de diagnóstico. Resuelva los problemas notificados.</li><li>El dispositivo está desactivado o en pausa en el hipervisor. Encienda el dispositivo. Para obtener más información, vaya a [Administración de los cables de alimentación](..\databox-gateway\data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).</li><li>El dispositivo podría haberse reiniciado debido a una actualización. Espere unos minutos e intente volver a conectarse.</li></ul>|


## <a name="edge-compute-alerts"></a>Alertas de proceso perimetral

Las siguientes alertas se generan para el proceso perimetral o la tarjeta de aceleración de proceso, que puede ser una unidad de procesamiento gráfico (GPU) o una unidad de procesamiento de visión (VPU) según el modelo del dispositivo.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|El proceso perimetral es incorrecto. |Crítico | Reinicie el dispositivo para resolver el problema. En la interfaz de usuario web local del dispositivo, vaya a **Mantenimiento** > **Configuración de energía** y haga clic en **Reiniciar**.<br>Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El proceso perimetral tuvo un problema con la resolución de nombres. |Crítico |Asegúrese de que el servidor DNS {15} está en línea y es accesible. Si el problema persiste, póngase en contacto con el administrador de red. |
|La configuración de la tarjeta de aceleración de proceso tiene un problema.<sup>*</sup> |Crítico |Hemos detectado una configuración de tarjeta de aceleración de proceso no admitida.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li><li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|La configuración de la tarjeta de aceleración de proceso tiene un problema.<sup>*</sup> |Crítico |Hemos detectado una tarjeta de aceleración de proceso no admitida.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li><li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|La configuración de la tarjeta de aceleración de proceso tiene un problema.<sup>*</sup> |Crítico |Esto puede deberse a una de las siguientes causas:<ol><li>Si la tarjeta es una matriz de puertas programables (FPGA), la imagen no es válida.</li><li>La tarjeta de aceleración de proceso no se ha colocado correctamente.</li><li>Problemas subyacentes con el controlador de aceleración de proceso.</li></ol>Para resolver el problema, vuelva a implementar el módulo de Azure IoT Edge. Una vez resuelto el problema, la alerta desaparece.<br>Si el problema continúa, realice lo siguiente: <ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|La configuración de la tarjeta de aceleración de proceso tiene un problema.<sup>*</sup> |Crítico |Esto se debe a un código de error interno.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|La configuración de la tarjeta de aceleración de proceso tiene un problema.<sup>*</sup> |Crítico |Mientras el módulo de Azure IoT Machine Learning se inicia, es posible que vea este problema transitorio. Espere unos minutos para ver si se resuelve el problema.<br>Si el problema continúa, realice lo siguiente:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|El software del controlador de la tarjeta de aceleración de proceso no se está ejecutando. |Crítico |Esto se debe a un código de error interno.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li><li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|El estado de la tarjeta de aceleración de proceso del dispositivo no es correcto. |Crítico |Esto se debe a un código de error interno.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li><li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|Se está cerrando la tarjeta de aceleración de proceso porque la temperatura de la tarjeta superó el límite operativo. |Crítico |Esto se debe a un código de error interno.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li><li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|El rendimiento de la tarjeta de aceleración de proceso se ha degradado. |Advertencia |Esto puede deberse a que la tarjeta de aceleración de proceso tiene una utilización elevada. Tenga en cuenta la posibilidad de detener o reducir la carga de trabajo del módulo de Azure IoT Machine Learning.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li><li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|La temperatura de la tarjeta de aceleración de proceso está aumentando. |Advertencia |Esto puede deberse a que la tarjeta de aceleración de proceso tiene una utilización elevada. Tenga en cuenta la posibilidad de detener o reducir la carga de trabajo del módulo de Azure IoT Machine Learning.<br>Antes de ponerse en contacto con Soporte técnico de Microsoft, siga estos pasos:<ol><li>En la interfaz de usuario web local, vaya a **Solución de problemas** > **Soporte técnico**.</li><li>Cree y descargue un paquete de soporte técnico.</li><li>[Creación de una solicitud de soporte técnico](azure-stack-edge-contact-microsoft-support.md#create-a-support-request).</li><li>Adjunte el paquete a la solicitud de soporte técnico.</li></ol> |
|El proceso perimetral no pudo acceder a los datos del recurso compartido {16}. |Advertencia |Compruebe que puede acceder al recurso compartido {16}. Si puede acceder al recurso compartido, esto indica un problema con el proceso perimetral. <br> Para resolver el problema, reinicie el dispositivo. En la interfaz de usuario web local del dispositivo, vaya a **Mantenimiento** > **Configuración de energía** y haga clic en **Reiniciar**. <br> Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El proceso perimetral no pudo acceder a los datos del recurso compartido {16}. Esto puede deberse a que el recurso compartido ya no existe. |Advertencia |Si el recurso compartido {16} no existe, reinicie el dispositivo para resolver el problema. En la interfaz de usuario web local del dispositivo, vaya a **Mantenimiento** > **Configuración de energía** y haga clic en **Reiniciar**. <br> Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El Agente de IoT Edge no se está ejecutando. |Advertencia |Reinicie el dispositivo para resolver el problema. En la interfaz de usuario web local del dispositivo, vaya a **Mantenimiento** > **Configuración de energía** y haga clic en **Reiniciar**. <br> Si el problema persiste, [póngase en contacto con el Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El servicio IoT Edge no se está ejecutando. |Advertencia |Reinicie el dispositivo para resolver el problema. En la interfaz de usuario web local del dispositivo, vaya a **Mantenimiento** > **Configuración de energía** y haga clic en **Reiniciar**. <br> Si el problema persiste, [póngase en contacto con el Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El almacenamiento que usa el proceso perimetral se está llenando. | Advertencia | [Póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md) para conocer los pasos siguientes. |
|El módulo del proceso perimetral {20} está desconectado de IoT Edge |Advertencia |Reinicie el dispositivo para resolver el problema. En la interfaz de usuario web local del dispositivo, vaya a **Mantenimiento** > **Configuración de energía** y haga clic en **Reiniciar**. <br> Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Es posible que los módulos del proceso perimetral estén usando un punto de montaje local {15} que no coincide con el que usa un recurso compartido. |Advertencia |Asegúrese de que el punto de montaje local {15} utilizado es el que se asigna al recurso compartido.<ul><li>En la instancia de Azure Portal, vaya a **Recursos compartidos** del recurso de Data Box Edge.</li><li>Seleccione un recurso compartido para ver el punto de montaje local para el módulo del proceso perimetral.</li><li>Asegúrese de que esta ruta de acceso se usa en el módulo e implemente el módulo de nuevo.</li></ol>Reinicie el dispositivo. En la interfaz de usuario web local del dispositivo, vaya a **Mantenimiento** > **Configuración de energía** y haga clic en **Reiniciar**. <br> Si la alerta persiste, [póngase en contacto con el Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |

<sup>*</sup> Esta alerta se desencadena mediante más de un tipo de evento, con diferentes acciones recomendadas.


## <a name="local-azure-resource-manager-arm-alerts"></a>Alertas de Azure Resource Manager (ARM) local

Las siguientes alertas las genera el Azure Resource Manager (ARM) local, que se usa para conectarse a las API locales en dispositivos de Azure Stack Edge.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|El certificado de autenticación de servicio especificado con la huella digital '{0}' no tiene una clave privada |Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se encuentra el certificado con la huella digital '{0}' en la ubicación '{1}' o no se puede acceder a él. |Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se puede conectar con el punto de conexión: '{0}' |Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Error durante la solicitud web: '{0}' |Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Se agotó el tiempo de espera de la solicitud para la dirección URL: '{0}' |Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se puede obtener el token con el punto de conexión de inicio de sesión '{0}' para el recurso '{1}'. |Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Error desconocido. ErrorCode:'{0}'. Detalles: '{1}' | Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se pudo iniciar el servicio de VM en el dispositivo. |Crítico | Si ve esta alerta, [póngase en contacto con el Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El servicio de VM no se está ejecutando en el dispositivo. |Crítico |Si ve esta alerta, [póngase en contacto con el Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |


## <a name="performance-alerts"></a>Alertas de rendimiento

Las siguientes alertas indican problemas de rendimiento relacionados con el almacenamiento o con el uso de CPU, memoria o disco en un dispositivo de Azure Stack Edge.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|El uso de la CPU en el dispositivo superó el umbral durante un período prolongado. |Crítico |Reduzca las cargas de trabajo o los módulos que se ejecutan en el dispositivo. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Las CPU reservadas para las máquinas virtuales del dispositivo superan el umbral configurado. |Crítico |Realice uno de los siguientes pasos:<ol><li>Reduzca la reserva de CPU para las máquinas virtuales que se ejecutan en el dispositivo.</li><li>Quite algunas máquinas virtuales del dispositivo.</li></ol> |
|La memoria que usan las máquinas virtuales del dispositivo supera el umbral configurado. |Crítico |Realice uno de los siguientes pasos:<ol><li>Reduzca la memoria asignada para las máquinas virtuales que se ejecutan en el dispositivo.</li><li>Quite algunas máquinas virtuales del dispositivo.</li></ol> |
|El volumen de datos del dispositivo es de un {0} %. Las escrituras en el dispositivo se están limitando. |Crítico                    |<ol><li>Distribuya la ingesta de datos para utilizar las horas de menos actividad.</li><li>Esto puede deberse a una red lenta. En la interfaz de usuario web local del dispositivo, vaya a **Solución de problemas** > **Pruebas de diagnóstico** y haga clic en **Ejecutar pruebas de diagnóstico**. Resuelva los problemas notificados.</li></ol>Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|La memoria que usan las máquinas virtuales del nodo {0} del dispositivo supera el umbral configurado. |Crítico |El dispositivo intentará equilibrar la carga en otros nodos. Tenga en cuenta la posibilidad de reducir algunas cargas de trabajo de máquinas virtuales desde el dispositivo. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Al dispositivo, casi no le queda espacio de almacenamiento. Si se produce un error en un disco, es posible que no pueda restaurar los datos de este dispositivo. |Crítico |Elimine los datos para liberar capacidad en el dispositivo. |
|El uso de la CPU del nodo {0} del dispositivo superó el umbral durante un período prolongado.<sup>*</sup> |Advertencia |El dispositivo intentará equilibrar la carga en otros nodos. Tenga en cuenta la posibilidad de reducir algunas cargas de trabajo de máquinas virtuales desde el dispositivo. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El uso de la CPU del nodo {0} del dispositivo superó el umbral durante un período prolongado.<sup>*</sup> |Advertencia |Reduzca las cargas de trabajo o los módulos que se ejecutan en el dispositivo. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El nodo {0} del dispositivo usa más memoria de la que se esperaba. |Advertencia |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|Las CPU reservadas para las máquinas virtuales del nodo {0} del dispositivo superan el umbral configurado. |Advertencia |Realice uno de los siguientes pasos:<ol><li>Reduzca la reserva de CPU para las máquinas virtuales que se ejecutan en el dispositivo.</li><li>Quite algunas máquinas virtuales del dispositivo.</li></ol> |
|La memoria que usan las máquinas virtuales del dispositivo supera el umbral configurado. |Advertencia |Realice uno de los siguientes pasos:<ol><li>Reduzca la memoria asignada para las máquinas virtuales que se ejecutan en el dispositivo.</li><li>Quite algunas máquinas virtuales del dispositivo.</li></ol> |
|Hay demasiadas máquinas virtuales activas en el nodo {0} del dispositivo. |Advertencia |El dispositivo intentará equilibrar la carga en otros nodos. Tenga en cuenta la posibilidad de reducir algunas cargas de trabajo de máquinas virtuales desde el dispositivo. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El disco duro virtual {0} está a punto de llegar a la capacidad máxima. |Advertencia |Elimine algunos datos para liberar capacidad. |

<sup>*</sup> Esta alerta se desencadena mediante más de un tipo de evento, con diferentes acciones recomendadas.


## <a name="storage-alerts"></a>Alertas de almacenamiento

Las siguientes alertas son para los problemas que se producen al acceder o cargar datos en Azure Storage.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|No se pudo acceder al volumen {0}.<sup>*</sup> |Crítico |Esto puede ocurrir cuando el volumen no tiene conexión o si demasiadas unidades o servidores se han desconectado o han producido un error. Siga estos pasos.<ol><li>Vuelva a conectar las unidades que faltan y active los servidores que no funcionan.</li><li>Permita que se complete la sincronización.</li><li>Reemplace las unidades que generan error y restaure los datos perdidos de la copia de seguridad.</li></ol> |
|No se pudo acceder al volumen {0}.<sup>*</sup> |Crítico |En la interfaz de usuario web local del dispositivo, vaya a **Solución de problemas** > **Pruebas de diagnóstico** y haga clic en **Ejecutar pruebas de diagnóstico**. Resuelva los problemas notificados.<br>Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se pudo encontrar el volumen {0}.<sup>*</sup> |Crítico |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se pudo encontrar el volumen {0}.<sup>*</sup> |Crítico<br>Advertencia |Expanda el volumen o migre cargas de trabajo a otros volúmenes. |
|Algunos datos de este volumen {0} no son totalmente resistentes. Sigue siendo accesible. |Informativo |Restaure la resistencia de los datos. |
|No se pudieron cargar {0} archivos del recurso compartido {1}. |Crítico |Esto puede deberse a uno de los siguientes motivos:<ol><li>Debido a infracciones de convenciones de nomenclatura y tamaño de Azure Storage. Para más información, vaya a [Convenciones de nomenclatura](../azure-resource-manager/management/resource-name-rules.md).</li><li>Dado que otras aplicaciones fuera del dispositivo modificaron los archivos cargados en la nube.<ul><li>{2} dentro del recurso compartido {1} o</li><li>{3} dentro de la cuenta {4}.</li></ol></ul> |
|No se pudo establecer la conexión con la cuenta de almacenamiento '{0}'.<sup>*</sup> |Crítico |Esto puede deberse a que se han regenerado las claves de acceso de la cuenta de almacenamiento. Si se han vuelto a generar las claves, debe sincronizar las nuevas claves.<br>Para corregir el problema, en Azure Portal vaya a **Recursos compartidos**, seleccione el recurso compartido y actualice las claves de almacenamiento. |
|No se pudo establecer la conexión con la cuenta de almacenamiento '{0}'.<sup>*</sup> |Crítico |Esto puede deberse a problemas de conectividad a Internet. El dispositivo no es capaz de comunicarse con el servicio de cuenta de almacenamiento. En la interfaz de usuario web local del dispositivo, vaya a **Solución de problemas** > **Pruebas de diagnóstico** y haga clic en **Ejecutar pruebas de diagnóstico**. Resuelva los problemas notificados. |
|El dispositivo tiene {0} archivos. Se admite un máximo de {1} archivos. |Crítico |Considere la posibilidad de eliminar algunos archivos del dispositivo. |
|Se detectó un rendimiento bajo desde y hacia el servicio Azure Storage. |Advertencia  |En la interfaz de usuario web local del dispositivo, vaya a **Solución de problemas** > **Pruebas de diagnóstico** y haga clic en **Ejecutar pruebas de diagnóstico**. Resuelva los problemas notificados. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |

<sup>*</sup> Esta alerta se desencadena mediante más de un tipo de evento, con diferentes acciones recomendadas.


## <a name="security-alerts"></a>Alertas de seguridad

Las siguientes alertas indican problemas de acceso relacionados con contraseñas, certificados o claves, o informan de intentos de acceder a un dispositivo de Azure Stack Edge.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|{0} de {1} caduca en {2} días. |Crítico<br>Advertencia |Compruebe el certificado y cargue uno nuevo antes de la fecha de expiración. |
|{0}del tipo {1} no es válido. |Crítico |Compruebe el certificado. Si el certificado no es válido, cargue uno nuevo. |
|Error de rotación de los certificados internos |Crítico |No se pudieron girar los certificados internos. Si los servicios se ven afectados, [póngase en contacto con el Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se pudo iniciar sesión en '{0}'. Número de intentos fallidos: '{1}'. |Crítico<br>Advertencia<br>Informativo |Asegúrese de que ha escrito la contraseña correcta.<br>Un usuario autorizado puede estar intentando conectarse al dispositivo con una contraseña incorrecta. Compruebe que estos intentos provienen de un origen legítimo.<br>Si sigue observando errores de inicio de sesión, póngase en contacto con el administrador de red. |
|La rotación del protector de clave SED en el nodo {0} no se completó a tiempo. |Advertencia |El intento de girar el protector de clave SED al nuevo valor predeterminado no se ha completado a tiempo. Compruebe si los discos físicos y de nodo están en estado correcto. El sistema volverá a intentarlo. |
|Se cambió la contraseña del dispositivo |Informativo |La contraseña del administrador de dispositivos ha cambiado. Se trata de una acción necesaria como parte de la primera vez que se configura el dispositivo o del restablecimiento de contraseña habitual. No se requiere ninguna acción adicional. |
|Se ha habilitado una sesión de soporte técnico. |Informativo |Se trata de una alerta informativa para asegurarse de que los administradores pueden afirmar que la habilitación de la sesión de soporte técnico es legítima. No se requiere ninguna acción. |
|Se ha iniciado una sesión de soporte técnico. |Informativo |Se trata de una alerta informativa para asegurarse de que los administradores pueden afirmar que la sesión de soporte técnico es legítima. No se requiere ninguna acción. |


## <a name="key-vault-alerts"></a>Alertas de Key Vault

Las siguientes alertas están relacionadas con la configuración de Azure Key Vault.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|La instancia de Key Vault no está configurada<sup>*</sup>. |Crítico<br>Advertencia |<ol><li>Compruebe que el almacén de claves no se ha eliminado.</li><li>Asigne los permisos adecuados para que el dispositivo obtenga y establezca los secretos. Para obtener pasos detallados, consulte [Requisitos previos para un recurso de Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Si los secretos se eliminan temporalmente, siga [estos pasos](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates) para recuperar los secretos.</li><li>Actualice los detalles del almacén de claves para borrar la alerta.</li></ol> |
|La instancia de Key Vault no está configurada<sup>*</sup>. |Advertencia |Configure el almacén de claves para el recurso de Azure Stack Edge. Para obtener los pasos detallados, consulte [Creación de un almacén de claves](../key-vault/general/quick-create-portal.md). |
|Se ha eliminado el almacén de claves |Crítico |Si se elimina el almacén de claves y aún no ha transcurrido la duración de la protección de purga de 90 días, siga los pasos para [Recuperar el almacén de claves](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-a-soft-deleted-key-vault). |
|No se han podido recuperar los secretos del almacén de claves |Crítico |<ol><li>Compruebe que el almacén de claves no se ha eliminado.</li><li>Asigne los permisos adecuados para que el dispositivo obtenga y establezca los secretos. Los permisos necesarios están presentes [aquí](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Actualice los detalles del almacén de claves para borrar la alerta.</li></ol> |
|No se pudo obtener acceso al almacén de claves |Crítico |<ol><li>Compruebe que el almacén de claves no se ha eliminado.</li><li>Asigne los permisos adecuados para que el dispositivo obtenga y establezca los secretos. Para obtener más información, consulte los [pasos detallados](azure-stack-edge-gpu-deploy-prep.md#prerequisites).</li><li>Actualice los detalles del almacén de claves para borrar la alerta.</li></ol> |

<sup>*</sup> Esta alerta se desencadena mediante más de un tipo de evento, con diferentes acciones recomendadas.


## <a name="hardware-alerts"></a>Alertas de hardware

Las siguientes alertas indican un problema con un componente de hardware, como un disco físico, una NIC o una unidad de fuente de alimentación, en un dispositivo de Azure Stack Edge.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|Hay un error de {0} en {1}. |Crítico |Esto se debe a que la fuente de alimentación no está conectada correctamente o ha producido un error. Para resolver el problema, siga estos pasos:<ol><li>Asegúrese de que la conexión de la fuente de alimentación es correcta.</li><li>[Póngase en contacto con Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md) para obtener una unidad de fuente de alimentación de reemplazo. |
|No se pudo contactar con {1}. |Crítico      |Si el controlador está desactivado, reinícielo.<br>Asegúrese de que la fuente de alimentación funciona. Para obtener información sobre cómo supervisar los LED de la fuente de alimentación, vaya a https://www.microsoft.com/2.<!--Need new link target. This one goes nowhere--><br>Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|{0} está apagado. |Advertencia |Conecte la fuente de alimentación con una unidad de distribución de energía. |
|Uno o varios componentes del dispositivo no funcionan correctamente. |Crítico |[Póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md) para conocer los pasos siguientes. |
|No se pudo reemplazar {0}. |Advertencia |[Póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md) para conocer los pasos siguientes. |
|Se inició el reemplazo de {0}. |Informativo |No se requiere ninguna acción por parte del usuario. |
|{0} se reemplazó correctamente |Informativo |No se requiere ninguna acción por parte del usuario. |
|{0} está desconectado. |Advertencia |Compruebe que '{0}' está conectado correctamente y que la interfaz de red está activa. |
|{0} ha sufrido un error.<sup>*</sup> |Crítico |El dispositivo debe reemplazarse. [Póngase en contacto con el Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md) para reemplazar el dispositivo. |
|{0} ha sufrido un error.<sup>*</sup> |Crítico |Compruebe que '{0}' está conectado correctamente y que la interfaz de red está activa.<br>En la interfaz de usuario web local del dispositivo, vaya a **Solución de problemas** > **Pruebas de diagnóstico** y haga clic en **Ejecutar pruebas de diagnóstico**. Resuelva los problemas notificados.<br>Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft en [https://aka.ms/getazuresupport](azure-stack-edge-contact-microsoft-support.md). |
|Algunos datos del disco físico de caché {0} del nodo {1} no se pueden leer, lo que nos impide moverlos a unidades de capacidad. |Advertencia |Reemplace el disco físico. |
|El disco físico de caché {0} del nodo {1} produjo un error en algunas lecturas o escrituras, por lo que para proteger los datos los hemos movido a unidades de capacidad. |Advertencia | Reemplace el disco físico. |
|El disco físico {0} del nodo {1} no pudo leer o escribir varias veces en los últimos dos días. Si esto sigue ocurriendo, podría significar que la unidad no funciona correctamente, está dañada o empieza a generar errores. |Advertencia |Si el problema persiste, considere la posibilidad de reemplazar el disco físico. |
|El disco físico {0} del nodo {1} tiene problemas con las lecturas o escrituras. |Advertencia |Si el problema persiste, considere la posibilidad de reemplazar el disco físico. |
|El disco físico {0} del nodo {1} alcanzó el 100 % de su resistencia de escritura calificada y ahora es de solo lectura, lo que significa que no puede realizar más escrituras. |Advertencia |Considere la posibilidad de reemplazar el disco físico. |
|Error en el disco físico {0} del nodo {1}. |Advertencia |Reemplace el disco físico. |
|Errores en el disco físico {0} del nodo {1}.<sup>*</sup> |Advertencia |El disco físico ha encontrado varios bloques defectuosos durante las escrituras de los últimos días. Esto podría significar que la unidad no funciona correctamente, está dañada o empieza a generar errores.<br>Si el problema persiste, considere la posibilidad de reemplazar el disco físico. |
|Errores en el disco físico {0} del nodo {1}.<sup>*</sup> |Advertencia |El disco físico {0} del nodo {1} ha encontrado varios bloques defectuosos durante las escrituras de los últimos días. Esto podría significar que la unidad no funciona correctamente, está dañada o empieza a generar errores.<br>Si el problema persiste, considere la posibilidad de reemplazar el disco físico. |
|Hay problemas en el disco físico {0} del nodo {1}. |Advertencia |Si el problema persiste, considere la posibilidad de reemplazar el disco físico. |
|El disco físico {0} del nodo {1} se está deteriorando. Es posible que pase a ser de solo lectura, lo que significa que no podrá realizar más escrituras cuando alcance el 100 % de su resistencia calificada. |Advertencia |Considere la posibilidad de reemplazar el disco físico. |
|El disco físico {0} del nodo {1} se ejecuta con lentitud. |Advertencia |Si el problema persiste, considere la posibilidad de reemplazar el disco físico. |
|No hay conectividad con el disco físico {0} del nodo {1}. |Advertencia |Compruebe que el disco físico funciona y está conectado correctamente.| 
|{0} no está o ha generado un error. |Crítico |El dispositivo está degradado. El dispositivo se volverá incorrecto si se produce un error en otro disco. [Póngase en contacto con Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md) para obtener una unidad de fuente de alimentación. Reemplace el disco. |
|Es posible que se produzca un error en el disco físico {0} del nodo {1} en breve. |Advertencia |Reemplace el disco físico. |
|Se está realizando una operación de reemplazo de disco. PercentComplete = {0}, Disco = {2}. |Crítico |Se trata de un evento informativo. No es necesario que realice ninguna acción en este momento. |
|Error en el disco físico {0} del nodo {1}. |Advertencia |Reemplace el disco físico. |
|El disco físico {0} del nodo {1} no responde de forma intermitente. |Advertencia |Reemplace el disco físico. |
|El disco físico {0} del nodo {1} no tiene establecido el protector de clave SED predeterminado actual. |Advertencia |El sistema intentará actualizar el protector de clave SED a la versión más reciente. Si el problema persiste, compruebe si la unidad está en estado correcto. |
|Error en el disco físico {0} del nodo {1} al realizar la rotación del protector de clave SED. |Advertencia |El intento de girar el protector de clave SED al nuevo valor predeterminado ha producido un error. Compruebe si el disco físico está en estado correcto. El sistema volverá a intentarlo, si el problema persiste, reemplace la unidad. |
|El disco físico {0} del nodo {1} tiene metadatos no reconocidos. |Crítico |El disco puede contener datos de un bloque de almacenamiento desconocido. Reemplace este disco por un disco compatible con Microsoft para el dispositivo que no contenga ningún dato. |
|El disco físico {0} del nodo {1} ejecuta una versión de firmware no compatible. |Advertencia |[Póngase en contacto con Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|El disco físico {0} del nodo {1} no se admite. |Advertencia |Reemplace el disco físico por el hardware compatible. |
|El sensor de temperatura de la placa base del servidor {0} ha generado una advertencia. |Advertencia |Compruebe la temperatura del nodo. |

<sup>*</sup> Esta alerta se desencadena mediante más de un tipo de evento, con diferentes acciones recomendadas.


## <a name="update-alerts"></a>Alertas de actualización

Las siguientes alertas están relacionadas con las actualizaciones de Microsoft y las actualizaciones de firmware de los componentes de dispositivo físico.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|No se pueden descargar las actualizaciones. Mensaje de error: '{0}'. |Crítico |{0} |
|No se pudieron instalar las actualizaciones. Mensaje de error: '{0}'. |Crítico |Resuelva el error: {0} |
|No se pudieron buscar actualizaciones. Mensaje de error: '{0}'. |Crítico |Resuelva el error: {0} |
|{0} actualizaciones disponibles. |Informativo |Le recomendamos encarecidamente que instale estas actualizaciones. Para más información, consulte [Instalación de actualizaciones](azure-stack-edge-gpu-install-update.md). |
|No se pudo actualizar el firmware del disco. |Crítico |[Póngase en contacto con el servicio de soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md) para conocer los pasos siguientes. |
|No se pudo actualizar el firmware en el disco físico {0} del nodo {1}. |Advertencia |[Póngase en contacto con Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md). |
|No se pudo realizar el progreso porque hay un lanzamiento de firmware en curso. |Advertencia |Compruebe que el estado de todos los espacios de almacenamiento sea correcto y que no haya ningún dominio de error en modo de mantenimiento actualmente. |
|Se canceló el lanzamiento del firmware debido a información de versión ilegible o inesperada después de aplicar la actualización del firmware. |Advertencia |Reinicie el lanzamiento del firmware después de resolver su problema. |
|Se canceló el lanzamiento de firmware porque se produjo un error en la actualización de firmware en demasiados discos físicos. |Advertencia |Reinicie el lanzamiento del firmware después de resolver su problema. |
|Se inició una actualización de firmware del disco. |Informativo |No se requiere ninguna acción por parte del usuario. |
|El firmware del disco se actualizó correctamente. |Informativo |No se requiere ninguna acción por parte del usuario. |
|Hay un lanzamiento de firmware de disco físico en curso. PercentComplete = {0}. |Informativo |Se trata de un evento informativo. No es necesario que realice ninguna acción en este momento. |


## <a name="virtual-machine-alerts"></a>Alertas de máquina virtual

Las siguientes alertas se generan para las máquinas virtuales en un dispositivo de Azure Stack Edge.

|Texto de la alerta |severity |Descripción / Acción recomendada |
|-----------|---------|---------------------------------|
|El estado de la máquina virtual {0} no es correcto. |Advertencia  |Para solucionar los problemas de la máquina virtual, consultehttps://aka.ms/vmtroubleshoot. |
|La máquina virtual {0} no funciona correctamente. |Advertencia  |Para solucionar los problemas de la máquina virtual, consultehttps://aka.ms/vmtroubleshoot. |
|La máquina virtual {0} no se está ejecutando. |Advertencia  |Si el problema persiste, elimine la máquina virtual y vuelva a implementarla.|
|El estado del sistema operativo invitado de la máquina virtual {0} es incorrecto. |Advertencia  |Para solucionar los problemas de la máquina virtual, consultehttps://aka.ms/vmtroubleshoot. |
|La máquina virtual {0} está casi sin memoria. |Advertencia  |Reduzca el uso de memoria en la máquina virtual. |
|La máquina virtual {0} no responde a las solicitudes de host. |Advertencia |Para solucionar los problemas de la máquina virtual, consultehttps://aka.ms/vmtroubleshoot. |


## <a name="next-steps"></a>Pasos siguientes

- [Cree reglas de acción para administrar las notificaciones de alerta](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
- [Use gráficos de métricas](azure-stack-edge-monitor.md#view-metrics).
- [Configure Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md).
