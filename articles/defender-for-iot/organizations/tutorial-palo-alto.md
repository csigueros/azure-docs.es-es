---
title: Integración de Palo Alto con Azure Defender para IoT
description: Defender para IoT ha integrado su plataforma continua de supervisión de amenazas de ICS con los firewalls de última generación de Palo Alto para permitir el bloqueo de amenazas críticas, de forma más rápida y eficaz.
ms.date: 09/26/2021
ms.topic: tutorial
ms.openlocfilehash: 6579c69d5ab789c97972a81f00dc56d19ce1f9f8
ms.sourcegitcommit: 149815030568fb4d4dd2e2025a18dc64fc190d07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129062040"
---
# <a name="tutorial-integrate-palo-alto-with-azure-defender-for-iot"></a>Tutorial: Integración de Palo Alto con Azure Defender para IoT

Este tutorial le ayudará a aprender a integrar Palo Alto con Azure Defender para IoT.

Defender para IoT ha integrado su plataforma continua de supervisión de amenazas de ICS con los firewalls de última generación de Palo Alto para permitir el bloqueo de amenazas críticas, de forma más rápida y eficaz.

Están disponibles los siguientes tipos de integración:

- Opción de bloqueo automático: integración directa de Defender para IoT con Palo Alto

- Envío de recomendaciones de bloqueo al sistema de administración central: integración de Defender para IoT con Panorama

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Configuración del bloqueo inmediato mediante un firewall de Palo Alto especificado
> - Creación de directivas de bloqueo de Panorama en Defender para IoT

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

### <a name="panorama-permissions"></a>Permisos de Panorama

- Confirmación del administrador de Panorama para permitir el bloqueo automático.

## <a name="configure-immediate-blocking-by-a-specified-palo-alto-firewall"></a>Configuración del bloqueo inmediato mediante un firewall de Palo Alto especificado

En algunos casos, como las alertas relacionadas con malware, puede habilitar el bloqueo automático. Las reglas de reenvío de Defender para IoT se usan para enviar un comando de bloqueo directamente a un firewall de Palo Alto específico.

Cuando Defender para IoT identifica una amenaza crítica, envía una alerta que incluye una opción de bloqueo del origen infectado. Si se selecciona **Block Source** (Bloquear origen) en los detalles de la alerta, se activa la regla de reenvío que envía el comando de bloqueo al firewall de Palo Alto especificado.

**Para configurar el bloqueo inmediato**:

1. En el panel izquierdo, seleccione **Reenvío**.

1. Seleccione **Create Forwarding Rule** (crear una regla de reenvío).

    :::image type="content" source="media/tutorial-palo-alto/forwarding.png" alt-text="Captura de la pantalla de alertas de reenvío.":::

1. En el menú desplegable Acciones, seleccione **Send to Palo Alto NGFW** (Enviar a Palo Alto NGFW).

   :::image type="content" source="media/tutorial-palo-alto/forward-rule.png" alt-text="Captura de la pantalla para crear regla de reenvío.":::

1. En el panel Actions (Acciones), establezca los parámetros siguientes:

   - **Host**: escriba la dirección IP del servidor NGFW.
   - **Puerto**: escriba el puerto del servidor NGFW.
   - **Nombre de usuario**: escriba el nombre de usuario del servidor NGFW.
   - **Contraseña**: escriba la contraseña del servidor NGFW.
   - **Configurar**: Configure las siguientes opciones para permitir el bloqueo de los orígenes sospechosos mediante el firewall de Palo Alto:
     - **Block illegal function codes** (Bloquear códigos de función ilegales): infracciones de protocolo, valor de campo no válido que infringe la especificación del protocolo ICS (posible ataque).
     - **Block unauthorized PLC programming / firmware updates** (Bloquear las actualizaciones de firmware/programación de PLC no autorizadas): cambios de PLC no autorizados.
     - **Block unauthorized PLC stop** (Bloquear detención de PLC no autorizada): detención de PLC (tiempo de inactividad).
     - **Block malware-related alerts** (Bloquear las alertas relacionadas con malware): bloqueo de los intentos de malware industrial (TRITON, NotPetya, etc.). Puede seleccionar la opción de **Automatic blocking** (Bloqueo automático). En ese caso, el bloqueo se ejecuta automáticamente y de inmediato.
     - **Block unauthorized scanning** (Bloquear el análisis no autorizado): análisis no autorizado (reconocimiento potencial).

    :::image type="content" source="media/tutorial-palo-alto/edit.png" alt-text="Captura de la pantalla Editar regla de reenvío.":::

1. Seleccione **Submit** (Enviar).

A continuación, deberá bloquear cualquier origen sospechoso.

**Para bloquear un origen sospechoso**:

1. Vaya al panel **Alertas**, seleccione la alerta relacionada con la integración de Palo Alto.

1. Para bloquear automáticamente el origen sospechoso, seleccione **Block Source** (Bloquear origen). Aparecerá el cuadro de diálogo **Confirme**.

    :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="Captura de pantalla del botón Block Source (Bloquear origen) para bloquear el origen no autorizado.":::

1. Seleccione **Aceptar**.

El firewall de Palo Alto bloquea ahora el origen sospechoso.

## <a name="create-panorama-blocking-policies-in-defender-for-iot"></a>Creación de directivas de bloqueo de Panorama en Defender para IoT

La integración de Defender para IoT y Palo Alto Networks crea automáticamente nuevas directivas en Palo Alto Networks NMS y Panorama.

En esta tabla se muestran los incidentes para los que está pensada esta integración:

| Tipo de incidente | Descripción |
|--|--|
|**Cambios de PLC no autorizados** | Una actualización de la lógica de escalera o del firmware de un dispositivo. Esto puede representar una actividad legítima o un intento de poner en peligro el dispositivo. Por ejemplo, un código malintencionado, como un troyano de acceso remoto (RAT), o parámetros que provocan que un proceso físico, como el giro de una turbina, funcione de forma no segura. |
|**Infracción del protocolo** | Una estructura de paquetes o valor de campo que infringe la especificación del protocolo. Esto puede representar una aplicación mal configurada o un intento malintencionado de poner en peligro el dispositivo. Por ejemplo, provocando una condición de desbordamiento de búfer en el dispositivo de destino. |
|**Detención de PLC** | un comando que hace que el dispositivo deje de funcionar, con lo que se pone en riesgo el proceso físico controlado por ese PLC. |
|**Malware industrial detectado en la red de ICS** | malware que manipula dispositivos ICS mediante sus protocolos nativos, como TRITON e Industroyer. Defender para IoT también detecta el malware de TI que se ha trasladado lateralmente al entorno ICS y SCADA. Por ejemplo, Conficker, WannaCry y NotPetya. |
|**Detección de malware** | herramientas de reconocimiento que recopilan datos acerca de la configuración del sistema en una fase previa al ataque. Por ejemplo, el troyano Havex examina las redes industriales en busca de dispositivos que usen OPC, que es un protocolo estándar que usan los sistemas SCADA basados en Windows para comunicarse con dispositivos ICS. |

Cuando Defender para IoT detecta un caso de uso preconfigurado, se agrega a la alerta el botón **Block Source** (Bloquear origen). Después, cuando el usuario CyberX selecciona el botón **Block source** (Bloquear origen), Defender para IoT crea directivas en Panorama mediante el envío de la regla de reenvío predefinida.

La directiva solo se aplica cuando el administrador de Panorama la envía al NGFW pertinente de la red.

En redes de TI, puede haber direcciones IP dinámicas. Por lo tanto, para esas subredes, la directiva se debe basar en el nombre de dominio completo (nombre DNS) y no en la dirección IP. Defender para IoT realiza la búsqueda inversa y hace coincidir los dispositivos con dirección IP dinámica con su nombre de dominio completo (nombre DNS) cada cierto número de horas que se puede configurar.

Además, Defender para IoT envía un correo electrónico al usuario de Panorama pertinente para notificar que una nueva directiva creada por Defender para IoT está esperando la aprobación. En la ilustración siguiente se muestra la arquitectura de integración de Defender para IoT con Panorama.

:::image type="content" source="media/tutorial-palo-alto/structure.png" alt-text="Captura de pantalla de la arquitectura de integración de CyberX-Panorama.":::

El primer paso para crear directivas de bloqueo de Panorama en Defender para IoT es configurar la búsqueda de DNS.

**Para configurar la búsqueda de DNS**:

1. En el panel izquierdo, seleccione **Configuración del sistema**.

1. Seleccione el botón **Configuración DNS** :::image type="icon" source="media/tutorial-palo-alto/settings.png":::.

1. En el cuadro de diálogo **Editar configuración DNS**, establezca los siguientes parámetros:

   - **Estado**: estado del solucionador DNS.

   - **Dirección del servidor DNS**: escriba la dirección IP o el FQDN del servidor DNS de la red.
   - **Puerto del servidor DNS**: escriba el puerto usado para consultar el servidor DNS.
   - **Subredes**: establezca el intervalo de subredes de las direcciones IP dinámicas. El intervalo que Defender para IoT recorre a la inversa, busca su dirección IP en el servidor DNS para que coincida con su nombre FQDN actual.
   - **Programación de la búsqueda inversa**: defina las opciones de programación de la siguiente manera:
     - Por horas específicas: especifique cuándo se debe realizar la búsqueda inversa diariamente.
     - Por intervalos fijos (en horas): establezca la frecuencia para realizar la búsqueda inversa.
   - **Número de etiquetas**: indique a Defender para IoT que resuelva automáticamente las direcciones IP de red en los FQDN de dispositivo. <br />Para configurar la resolución de FQDN de DNS, agregue el número de etiquetas de dominio que desea mostrar. Se muestran hasta 30 caracteres de izquierda a derecha.

    :::image type="content" source="media/tutorial-palo-alto/configuration.png" alt-text="Captura de la pantalla para configurar las opciones de DNS.":::

1. Seleccione **SAVE** (GUARDAR).

Para asegurarse de que la configuración de DNS es correcta, seleccione **Lookup Test** (Búsqueda de prueba). La prueba garantiza que la dirección IP del servidor DNS y el puerto del servidor DNS estén configurados correctamente.

## <a name="block-suspicious-traffic-with-the-palo-alto-firewall"></a>Bloqueo del tráfico sospechoso con el firewall de Palo Alto

El tráfico sospechoso deberá bloquearse con el firewall de Palo Alto. Puede bloquear el tráfico sospechoso mediante el uso de reglas de reenvío en Defender para IoT.

**Para bloquear el tráfico sospechoso con el firewall de Palo Alto mediante una regla de reenvío de Defender para IoT**:

1. En el panel izquierdo, seleccione **Reenvío**.

1. Seleccione **Create Forwarding Rule** (crear una regla de reenvío).

1. En el menú desplegable **Acciones**, seleccione **Send to Palo Alto Panorama** (Enviar a Palo Alto Panorama).

1. En el panel Actions (Acciones), establezca los parámetros siguientes:

   - **Host**: escriba la dirección IP del servidor de Panorama.

   - **Puerto**: escriba el puerto del servidor de Panorama.

   - **Nombre de usuario**: escriba el nombre de usuario del servidor de Panorama.

   - **Contraseña**: escriba la contraseña del servidor de Panorama.

   - **Dirección de informe**: defina cómo se ejecuta el bloqueo como se indica a continuación:

     - **Por dirección IP**: siempre crea directivas de bloqueo en Panorama basadas en la dirección IP.

     - **Por FQDN o dirección IP**: crea directivas de bloqueo en Panorama según el FQDN si existe; de lo contrario, según la dirección IP.

   - **Correo electrónico**: establezca la dirección de correo electrónico para el correo electrónico de notificación de directiva.

    > [!NOTE]
    > Asegúrese de que ha configurado un servidor de correo en Defender para IoT. Si no se especifica ninguna dirección de correo electrónico, Defender for IoT no envía ningún correo electrónico de notificación.

   - **Ejecutar una búsqueda de DNS tras la detección de la alerta (casilla)** : cuando se establece la opción FQDN o dirección IP en la dirección del informe. Esta casilla está activada de forma predeterminada. Si solo se establece la dirección IP, esta opción está deshabilitada.

   - **Configurar**: Configure las siguientes opciones para permitir el bloqueo de los orígenes sospechosos mediante Palo Alto Panorama:

     - **Block illegal function codes** (Bloquear códigos de función ilegales): infracciones de protocolo, valor de campo no válido que infringe la especificación del protocolo ICS (posible ataque).

     - **Block unauthorized PLC programming / firmware updates** (Bloquear las actualizaciones de firmware/programación de PLC no autorizadas): cambios de PLC no autorizados.

     - **Block unauthorized PLC stop** (Bloquear detención de PLC no autorizada): detención de PLC (tiempo de inactividad).

     - **Block malware-related alerts** (Bloquear las alertas relacionadas con malware): bloqueo de los intentos de malware industrial (TRITON, NotPetya, etc.). Puede seleccionar la opción de **Automatic blocking** (Bloqueo automático). En ese caso, el bloqueo se ejecuta automáticamente y de inmediato.

     - **Block unauthorized scanning** (Bloquear el análisis no autorizado): análisis no autorizado (reconocimiento potencial).

    :::image type="content" source="media/tutorial-palo-alto/details.png" alt-text="Captura de la pantalla Seleccionar acción.":::

1. Seleccione **Submit** (Enviar).

A continuación, deberá bloquear el origen sospechoso.

**Para bloquear el origen sospechoso**:

1. En el panel de **alertas**, seleccione la alerta relacionada con la integración de Palo Alto. Aparecerá el cuadro de diálogo **Alert Details** (Detalles de alerta).

  :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="Captura de la pantalla de alertas, seleccione la relacionada con Palo Alto y, a continuación, seleccione Block Source (Bloquear origen).":::

1. Para bloquear automáticamente el origen sospechoso, seleccione **Block Source** (Bloquear origen).

1. Seleccione **Aceptar**.

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-step"></a>Paso siguiente

En este tutorial, ha aprendido a empezar a trabajar con la integración de Palo Alto.

> [!div class="nextstepaction"]
> [Botón de pasos siguientes](tutorial-splunk.md)
