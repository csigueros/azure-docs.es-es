---
title: Integración de Palo Alto
description: Defender para IoT ha integrado su plataforma continua de supervisión de amenazas de ICS con los firewalls de última generación de Palo Alto para permitir el bloqueo de amenazas críticas, de forma más rápida y eficaz.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018882"
---
# <a name="about-the-palo-alto-integration"></a>Acerca de la integración de Palo Alto

Defender para IoT ha integrado su plataforma continua de supervisión de amenazas de ICS con los firewalls de última generación de Palo Alto para permitir el bloqueo de amenazas críticas, de forma más rápida y eficaz.

Están disponibles los siguientes tipos de integración:

- Opción de bloqueo automático: integración directa de Defender para IoT con Palo Alto

- Envío de recomendaciones de bloqueo al sistema de administración central: integración de Defender para IoT con Panorama

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Configuración del bloqueo inmediato mediante el firewall de Palo Alto especificado

En casos críticos, como las alertas relacionadas con malware, puede habilitar el bloqueo automático. Esto se hace configurando una regla de reenvío en Defender para IoT que envía el comando de bloqueo directamente a un firewall de Palo Alto específico.

Cuando Defender para IoT identifica una amenaza crítica, envía una alerta que incluye una opción de bloqueo del origen infectado. Si se selecciona **Block Source** (Bloquear origen) en los detalles de la alerta, se activa la regla de reenvío que envía el comando de bloqueo al firewall de Palo Alto especificado.

Para configurar:

1. En el panel izquierdo, seleccione **Reenvío**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="La pantalla de alerta de reenvío.":::

1. Seleccione **Create Forwarding Rule** (crear una regla de reenvío).

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Crear una regla de reenvío":::

1. Para configurar la regla de reenvío de Palo Alto NGFW:  
 
   - Defina los parámetros de regla estándar y, en el cuadro desplegable **Actions** (Acciones), seleccione **Send to Palo Alto NGFW** (Enviar a Palo Alto NGFW).
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Edición de la regla de reenvío.":::

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
     
1. Seleccione **Submit** (Enviar).

Para bloquear el origen sospechoso: 

1. En el panel de **alertas**, seleccione la alerta relacionada con la integración de Palo Alto. Aparecerá el cuadro de diálogo **Alert Details** (Detalles de alerta).
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Detalles de alerta":::

1. Para bloquear automáticamente el origen sospechoso, seleccione **Block Source** (Bloquear origen). Aparecerá el cuadro de diálogo **Confirme**.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirmación del bloqueo en la pantalla Confirme.":::

1. En el cuadro de diálogo **Confirme**, seleccione **Aceptar**. El firewall de Palo Alto bloquea el origen sospechoso.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Envío de directivas de bloqueo a Palo Alto Panorama

Defender para IoT y Palo Alto Networks ofrecen una integración inmediata que crea automáticamente nuevas directivas en Palo Alto Networks NMS, Panorama. Esta integración requiere la confirmación por parte del administrador de Panorama y no permite el bloqueo automático.

La integración está pensada para los siguientes incidentes:

- **Cambios de PLC no autorizados:** una actualización de la lógica de escalera o del firmware de un PLC. Esto puede representar una actividad legítima o un intento de poner el dispositivo en riesgo. Esto podría producirse mediante la inserción de código malintencionado, como un troyano de acceso remoto (RAT), o con parámetros que provocan que un proceso físico, como el giro de una turbina, funcione de forma no segura.

- **Infracción del protocolo:** una estructura de paquetes o valor de campo que infringe la especificación del protocolo. Esto puede representar una aplicación mal configurada o un intento malintencionado de poner en peligro el dispositivo. Por ejemplo, provocando una condición de desbordamiento de búfer en el dispositivo de destino.

- **Detención de PLC:** un comando que hace que el dispositivo deje de funcionar, con lo que se pone en riesgo el proceso físico controlado por ese PLC.

- **Malware industrial detectado en la red de ICS:** malware que manipula dispositivos ICS mediante sus protocolos nativos, como TRITON e Industroyer. Defender para IoT también detecta el malware de TI que se ha trasladado lateralmente al entorno ICS y SCADA, como Conficker, WannaCry y NotPetya.

- **Detección de malware:** herramientas de reconocimiento que recopilan datos acerca de la configuración del sistema en una fase previa al ataque. Por ejemplo, el troyano Havex examina las redes industriales en busca de dispositivos que usen OPC, que es un protocolo estándar que usan los sistemas SCADA basados en Windows para comunicarse con dispositivos ICS.

## <a name="the-process"></a>El proceso

Cuando Defender para IoT detecta un caso de uso preconfigurado, se agrega a la alerta el botón **Block Source** (Bloquear origen). Después, cuando el usuario **CyberX** selecciona el botón **Block source** (Bloquear origen), Defender para IoT crea directivas en Panorama mediante el envío de la regla de reenvío predefinida.

La directiva solo se aplica cuando el administrador de Panorama la envía al NGFW pertinente de la red.

En redes de TI, puede haber direcciones IP dinámicas. Por lo tanto, para esas subredes, la directiva se debe basar en el nombre de dominio completo (nombre DNS) y no en la dirección IP. Defender para IoT realiza la búsqueda inversa y hace coincidir los dispositivos con dirección IP dinámica con su nombre de dominio completo (nombre DNS) cada cierto número de horas que se puede configurar.

Además, Defender para IoT envía un correo electrónico al usuario de Panorama pertinente para notificar que una nueva directiva creada por Defender para IoT está esperando la aprobación. En la ilustración siguiente se muestra la arquitectura de integración de Defender para IoT con Panorama.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Arquitectura de integración de CyberX con Panorama":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Creación de directivas de bloqueo de Panorama en la configuración de Defender para IoT

### <a name="to-configure-dns-lookup"></a>Para configurar la búsqueda de DNS:

1. En el panel izquierdo, seleccione **Configuración del sistema**.

1. En el panel **Configuración del sistema**, seleccione **Configuración DNS** :::image type="icon" source="media/integration-paloalto/settings.png":::.

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Configuración de los valores de DNS.":::

1. En el cuadro de diálogo **Editar configuración DNS**, establezca los siguientes parámetros:

   - **Estado**: estado del solucionador DNS.

   - **Dirección del servidor DNS**: escriba la dirección IP o el FQDN del servidor DNS de la red.
   - **Puerto del servidor DNS**: escriba el puerto usado para consultar el servidor DNS.
   - **Subredes**: establezca el intervalo de subredes de las direcciones IP dinámicas. El intervalo que Defender para IoT recorre a la inversa, busca su dirección IP en el servidor DNS para que coincida con su nombre FQDN actual.
   - **Programación de la búsqueda inversa**: defina las opciones de programación de la siguiente manera:
     - Por horas específicas: especifique cuándo se debe realizar la búsqueda inversa diariamente.
     - Por intervalos fijos (en horas): establezca la frecuencia para realizar la búsqueda inversa.
   - **Número de etiquetas**: indique a Defender para IoT que resuelva automáticamente las direcciones IP de red en los FQDN de dispositivo. <br />Para configurar la resolución de FQDN de DNS, agregue el número de etiquetas de dominio que desea mostrar. Se muestran hasta 30 caracteres de izquierda a derecha.
1. Seleccione **SAVE** (GUARDAR).
1. Para asegurarse de que la configuración de DNS es correcta, seleccione **Lookup Test** (Búsqueda de prueba). La prueba garantiza que la dirección IP del servidor DNS y el puerto del servidor DNS estén configurados correctamente.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Configuración de una regla de reenvío que bloquee el tráfico sospechoso con el firewall de Palo Alto

1. En el panel izquierdo, seleccione **Reenvío**. Aparece el panel Reenvío.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="La pantalla Reenvío.":::

1. En el panel **Reenvío**, seleccione **Create Forwarding Rule** (Crear regla de reenvío).

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Crear una regla de reenvío":::

1. Para configurar la regla de reenvío de Palo Alto Panorama:

   Defina los parámetros de regla estándar y, en el cuadro desplegable **Actions** (Acciones), seleccione **Send to Palo Alto Panorama** (Enviar a Palo Alto Panorama). Aparece el panel de detalles de la acción.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Acción Select":::

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
   - **Ejecutar una búsqueda DNS tras la detección de alertas (casilla)** : cuando la opción por FQDN o por dirección IP se establece en la dirección del informe. Esta casilla está activada de forma predeterminada. Si solo se establece la dirección IP, esta opción está deshabilitada.
   - **Configurar**: Configure las siguientes opciones para permitir el bloqueo de los orígenes sospechosos mediante Palo Alto Panorama:
   
     - **Block illegal function codes** (Bloquear códigos de función ilegales): infracciones de protocolo, valor de campo no válido que infringe la especificación del protocolo ICS (posible ataque).
     
     - **Block unauthorized PLC programming / firmware updates** (Bloquear las actualizaciones de firmware/programación de PLC no autorizadas): cambios de PLC no autorizados.
     
     - **Block unauthorized PLC stop** (Bloquear detención de PLC no autorizada): detención de PLC (tiempo de inactividad).
     
     - **Block malware-related alerts** (Bloquear las alertas relacionadas con malware): bloqueo de los intentos de malware industrial (TRITON, NotPetya, etc.). Puede seleccionar la opción de **Automatic blocking** (Bloqueo automático). En ese caso, el bloqueo se ejecuta automáticamente y de inmediato.
     
     - **Block unauthorized scanning** (Bloquear el análisis no autorizado): análisis no autorizado (reconocimiento potencial).
     
1. Seleccione **Submit** (Enviar).

### <a name="to-block-the-suspicious-source"></a>Para bloquear el origen sospechoso:

1. En el panel de **alertas**, seleccione la alerta relacionada con la integración de Palo Alto. Aparecerá el cuadro de diálogo **Alert Details** (Detalles de alerta).

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Detalles de alerta":::        

1. Para bloquear automáticamente el origen sospechoso, seleccione **Block Source** (Bloquear origen).

1. En el cuadro de diálogo de **confirmación**, seleccione **OK** (Aceptar).

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirm":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [reenviar información de alertas](how-to-forward-alert-information-to-partners.md).
