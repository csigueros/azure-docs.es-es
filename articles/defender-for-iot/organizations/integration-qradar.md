---
title: Integración de QRadar
description: Configure la integración de soluciones de Defender para IoT con QRadar.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 26808482ad9fabd528d2ec7e0d846c301c2c2ebf
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342424"
---
# <a name="about-the-qradar-integration"></a>Acerca de la integración de QRadar

Defender para IoT es la única plataforma de ciberseguridad para ICS e IoT creada por expertos del equipo azul con experiencia en la defensa de una infraestructura nacional crítica y la única plataforma con análisis de amenazas patentados y compatibles con ICS y aprendizaje automático.

Defender para IoT ha integrado su plataforma de supervisión continua de amenazas de ICS con IBM QRadar. 

Estas son algunas de las ventajas de la integración:

- La capacidad de reenviar las alertas de Azure Defender para IoT a IBM QRadar para una gobernanza y supervisión unificadas de TI y de la seguridad de OT.

- La capacidad de obtener una vista general en los entornos de TI y OT, lo que permite detectar y responder a ataques en varias fases que a menudo cruzan los límites de TI y OT.

- La integración con los flujos de trabajo existentes del centro de operaciones de seguridad.

## <a name="configuring-syslog-listener-for-qradar"></a>Configuración del cliente de escucha de Syslog para QRadar

Para configurar el cliente de escucha de Syslog para que funcione con QRadar:

1. Inicie sesión en QRadar.

1. En el panel izquierdo, seleccione **Admin** > **Data Sources** (Admin > Orígenes de datos).

   [:::image type="content" source="media/integration-qradar/log.png" alt-text="Seleccionar orígenes de registro en las opciones disponibles.":::](media/integration-qradar/log.png#lightbox)

1. En la ventana Data Sources (Orígenes de datos), seleccione **Log Sources** (Orígenes de registro).

   [:::image type="content" source="media/integration-qradar/modal.png" alt-text="Después de seleccionar Syslog, se abre la ventana de modo.":::](media/integration-qradar/modal.png#lightbox)

1. En la ventana **Modal**, seleccione **Add** (Agregar).

   :::image type="content" source="media/integration-qradar/source.png" alt-text="Para agregar un origen de registro rellene los campos adecuados.":::

1. En el cuadro de diálogo **Add a log source** (Agregar un origen de registro), establezca los siguientes parámetros:

   - **Log Source Name** (Nombre de origen de registro): `<XSense Name>`
   
   - **Log Source Description** (Descripción de origen de registro): `<XSense Name>`
   
   - **Log Source Type** (Tipo de origen de registro): `Universal LEEF`

   - **Protocol Configuration** (Configuración de protocolo): `Syslog`
   
   - **Log Source Identifier** (Identificador de origen de registro): `<XSenseName>`
   
   > [!NOTE]
   > El nombre del identificador de origen del registro no debe incluir espacios en blanco. Se recomienda reemplazar todos los espacios en blanco por caracteres de subrayado.

1. Seleccione **Save** (Guardar).

1. Seleccione **Deploy Changes** (Implementar cambios).

:::image type="content" source="media/integration-qradar/deploy.png" alt-text="Captura de pantalla de la vista Deploy Changes (Implementar cambios)":::

## <a name="deploying-defender-for-iot-platform-qid"></a>Implementación del QID de la plataforma Defender para IoT

QID es un identificador de eventos en QRadar. Todos los informes de la plataforma Defender para IoT se etiquetan en el mismo evento (Alerta de XSense).

**Para implementar el QID de Xsense**:

1. Inicie sesión en la consola de QRadar.

1. Cree un archivo llamado `xsense_qids`.

1. En ese archivo, utilice el comando siguiente: `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. Ejecute: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. Aparece un mensaje que indica que QID se ha implementado.

## <a name="setting-up-qradar-forwarding-rules"></a>Configuración de las reglas de reenvío de QRadar

En el dispositivo Defender para IoT, configure una regla de reenvío de QRadar. Asigne la regla en la consola de administración local.

**Para definir las notificaciones de QRadar en el dispositivo Defender para IoT**:

1. En el menú lateral, seleccione **Forwarding** (Reenvío).

   :::image type="content" source="media/integration-qradar/create.png" alt-text="Crear una regla de reenvío":::

1. En Action (Acción), elija **QRadar**.

1. Configure la dirección IP de QRadar y la zona horaria.

1. Seleccione **Submit** (Enviar).

**Para asignar notificaciones a QRadar en el Administrador central**:

1. En el menú lateral, seleccione **Forwarding** (Reenvío).

1. En la interfaz gráfica de usuario de Qradar, en QRadar, seleccione **Log Activity** Actividad de registro.

1. Seleccione **Add Filter** (Agregar) y establezca los siguientes parámetros:
   - Parámetro: `Log Sources [Indexed]`
   - Operador: `Equals`
   - Grupo de origen de registro: `Other`
   - Origen de registro: `<Xsense Name>`

1. Haga doble clic en un informe desconocido desde XSense.

1. Seleccione **Map Event** (Asignar evento).

1. En la página Modal Log Source Event (Evento de origen de registro modal), seleccione los siguientes elementos:
   - High-Level Category - Suspicious Activity + Low-Level Category - Unknown Suspicious Event + Log (Categoría de alto nivel: actividad sospechosa + Categoría de bajo nivel: evento sospechoso desconocido + Registro)
   - Source Type - any (Tipo de origen: cualquiera)

1. Seleccione **Search**.

1. En los resultados, elija la línea en la que aparezca el nombre XSense y seleccione **OK** (Aceptar).

A partir de este momento, todos los informes de XSense se etiquetarán como alertas de XSense.

## <a name="adding-custom-fields-to-alerts"></a>Adición de campos personalizados a alertas

**Para agregar campos personalizados a alertas**:

1. Seleccione **Extract Property** (Extraer propiedad).

1. Seleccione **Regex Based** (Basado en Regex).

1. Configure los campos siguientes:
   - Nueva propiedad: _elija cualquiera en la lista siguiente_
      - Xsense Alert Description (Descripción de alerta de Xsense)
      - Xsense Alert ID (Identificador de alerta de Xsense)
      - Xsense Alert Score (Puntuación de alerta de Xsense)
      - Xsense Alert Title (Título de alerta de Xsense)
      - Xsense Destination Name (Nombre de destino de Xsense)
      - Xsense Direct Redirect (Redirección directa de Xsense)
      - Xsense Sender IP (IP de emisor de Xsense)
      - Xsense Sender Name (Nombre de emisor de Xsense)
      - Xsense Alert Engine (Motor de alertas de Xsense)
      - Xsense Source Device Name (Nombre de dispositivo de origen de Xsense)
   - Marque **Optimize Parsing** (Optimizar análisis)
   - Tipo de campo: `AlphaNumeric`
   - Marque **Enabled** (Habilitado)
   - Log Source Type (Tipo de origen de registro): `Universal LEAF`
   - Origen de registro: `<Xsense Name>`
   - Nombre de evento (ya debe estar establecido en XSense Alert [Alerta de XSense])
   - Grupo de captura: 1
   - Regex:
      - Xsense Alert Description RegEx (Regex de descripción de alerta de Xsense): `msg=(.*)(?=\t)`
      - Xsense Alert ID RegEx (Regex de identificador de alerta de Xsense): `alertId=(.*)(?=\t)`
      - Xsense Alert Score RegEx (Regex de puntuación de alerta de Xsense): `Detected score=(.*)(?=\t)`
      - Xsense Alert Title RegEx (Regex de título de alerta de Xsense): `title=(.*)(?=\t)`
      - Xsense Destination Name RegEx (Regex de nombre de destino de Xsense): `dstName=(.*)(?=\t)`
      - Xsense Direct Redirect RegEx (Regex de redirección directa de Xsense): `rta=(.*)(?=\t)`
      - Xsense Sender IP RegEx (Regex de IP de emisor de Xsense): `reporter=(.*)(?=\t)`
      - Xsense Sender Name RegEx (Regex de nombre de emisor de Xsense): `senderName=(.*)(?=\t)`
      - Xsense Alert Engine RegEx (Regex de motor de alertas de Xsense): `engine =(.*)(?=\t)`
      - Xsense Source Device Name RegEx (Regex de nombre de dispositivo de origen de Xsense): `src`

## <a name="defining-defender-for-iot-appliance-name"></a>Definición del nombre del dispositivo Defender para IoT

El nombre de la plataforma se puede cambiar en cualquier momento.

Al crear sitios y asignar dispositivos a zonas en la consola de administración local, es preciso asignar un nombre significativo a cada dispositivo. Por ejemplo, "Unidad 2 de LP de motocicletas" significa que este dispositivo protege la unidad 2 de la línea de producción de motocicletas. 

Es importante elegir un nombre significativo para el dispositivo, ya que ese nombre se pasa a los registros. Al revisar los registros, cada alerta tiene un sensor asociado. El nombre del sensor permite identificar qué sensor está relacionado con cada alerta.

**Para cambiar el nombre del dispositivo**:

1. En el menú lateral, seleccione el nombre del dispositivo actual. Aparece el cuadro de diálogo **Edit management console configuration** (Editar la configuración de la consola de administración).

   :::image type="content" source="media/integration-qradar/edit-management-console.png" alt-text="Cambie el nombre de la consola.":::

1. Escriba un nombre en el campo Name (Nombre) y seleccione **Save** (Guardar).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [reenviar información de alertas](how-to-forward-alert-information-to-partners.md).
