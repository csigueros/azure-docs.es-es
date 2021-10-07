---
title: Integración de Qradar con Azure Defender para IoT
description: En este tutorial, aprenderá a integrar Qradar con Azure Defender para IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: ec55a652b59f7d45e01ef22e62bd046473ef8559
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839233"
---
# <a name="tutorial-integrate-qradar-with-azure-defender-for-iot"></a>Tutorial: Integración de Qradar con Azure Defender para IoT

Este tutorial le ayudará a aprender a integrar y usar QRadar con Azure Defender para IoT.

Defender para IoT ofrece la única plataforma de ICS y ciberseguridad de IoT con tecnología patentada de análisis de amenazas y aprendizaje automático compatible con ICS.

Defender para IoT ha integrado su plataforma de supervisión continua de amenazas de ICS con IBM QRadar.

Estas son algunas de las ventajas de la integración:

- La capacidad de reenviar las alertas de Azure Defender para IoT a IBM QRadar para una gobernanza y supervisión unificadas de TI y de la seguridad de OT.

- La capacidad de obtener información general de los entornos de TI y OT. Permite detectar y responder a ataques en varias fases que a menudo cruzan los límites de TI y OT.

- La integración con los flujos de trabajo existentes del centro de operaciones de seguridad.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración del cliente de escucha de Syslog para QRadar
> * Implementación del QID de la plataforma de Defender para IoT
> * Configuración de las reglas de reenvío de QRadar
> * Asignación de notificaciones a QRadar en la consola de administración
> * Adición de campos personalizados a las alertas

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

No hay requisitos previos para este tutorial.

## <a name="configure-syslog-listener-for-qradar"></a>Configuración del cliente de escucha de Syslog para QRadar

**Para configurar el cliente de escucha de Syslog para que funcione con QRadar**:

1. Inicie sesión en QRadar.

1. En el panel izquierdo, seleccione **Admin** > **Data Sources** (Admin > Orígenes de datos).

1. En la ventana Data Sources (Orígenes de datos), seleccione **Log Sources** (Orígenes de registro).

   [:::image type="content" source="media/tutorial-qradar/log.png" alt-text="Captura de pantalla de la selección de orígenes de registro entre las opciones disponibles.":::](media/tutorial-qradar/log.png#lightbox)

1. En la ventana **Modal**, seleccione **Add** (Agregar).

    [:::image type="content" source="media/tutorial-qradar/modal.png" alt-text="Captura de pantalla después de seleccionar Syslog y se abre la ventana modal.":::](media/tutorial-qradar/modal.png#lightbox)

1. En el cuadro de diálogo **Add a log source** (Agregar un origen de registro), establezca los siguientes parámetros:

    :::image type="content" source="media/tutorial-qradar/source.png" alt-text="Captura de pantalla de la adición de un origen de registro en la que se rellenan los campos adecuados.":::

   - **Log Source Name** (Nombre de origen de registro): `<Sensor name>`

   - **Log Source Description** (Descripción de origen de registro): `<Sensor name>`

   - **Log Source Type** (Tipo de origen de registro): `Universal LEEF`

   - **Protocol Configuration** (Configuración de protocolo): `Syslog`

   - **Log Source Identifier** (Identificador de origen de registro): `<Sensor name>`

   > [!NOTE]
   > El nombre del identificador de origen del registro no debe incluir espacios en blanco. Se recomienda reemplazar todos los espacios en blanco por caracteres de subrayado.

1. Seleccione **Save** (Guardar).

1. Seleccione **Deploy Changes** (Implementar cambios).

   :::image type="content" source="media/tutorial-qradar/deploy.png" alt-text="Captura de pantalla de la vista Deploy Changes (Implementar cambios)":::

## <a name="deploy-defender-for-iot-platform-qid"></a>Implementación del QID de la plataforma de Defender para IoT

QID es un identificador de eventos en QRadar. Todos los informes de la plataforma de Defender para IoT se etiquetan en el mismo evento (Alerta de sensor).

**Para implementar el QID de la plataforma de Defender para IoT**:

1. Inicie sesión en la consola de QRadar.

1. Cree un archivo llamado `xsense_qids`.

1. En ese archivo, utilice el comando siguiente: `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. Ejecute: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. Aparece un mensaje que indica que QID se ha implementado.

## <a name="setup-qradar-forwarding-rules"></a>Configuración de las reglas de reenvío de QRadar

Para que la integración funcione, deberá configurar una regla de reenvío de Qradar en el dispositivo de Defender para IoT.

**Para definir las notificaciones de QRadar en el dispositivo Defender para IoT**:

1. En el menú lateral, seleccione **Forwarding** (Reenvío).

1. Seleccione **Create Forwarding Rule** (crear una regla de reenvío).

1. En Action (Acción), elija **QRadar**.

    :::image type="content" source="media/tutorial-qradar/create.png" alt-text="Captura de pantalla de la ventana para crear una regla de reenvío.":::

1. Configure la dirección IP de QRadar y la zona horaria.

1. Seleccione **Submit** (Enviar).

## <a name="map-notifications-to-qradar"></a>Asignación de notificaciones a QRadar

A continuación, se debe asignar la regla en la consola de administración local.

**Para asignar las notificaciones a QRadar**:

1. Inicie sesión en la consola de administración.

1. Seleccione **Forwarding** (Reenvío) en el panel izquierdo.

1. En la interfaz gráfica de usuario de Qradar, en QRadar, seleccione **Log Activity** Actividad de registro.

1. Seleccione **Add Filter** (Agregar) y establezca los siguientes parámetros:
   - Parámetro: `Log Sources [Indexed]`
   - Operador: `Equals`
   - Grupo de origen de registro: `Other`
   - Origen de registro: `<Xsense Name>`

1. Haga doble clic en un informe desconocido del sensor.

1. Seleccione **Map Event** (Asignar evento).

1. En la página Modal Log Source Event (Evento de origen de registro modal), seleccione los siguientes elementos:
   - High-Level Category - Suspicious Activity + Low-Level Category - Unknown Suspicious Event + Log (Categoría de alto nivel: actividad sospechosa + Categoría de bajo nivel: evento sospechoso desconocido + Registro)
   - Source Type - any (Tipo de origen: cualquiera)

1. Seleccione **Search**.

1. En los resultados, elija la línea en la que aparezca el nombre XSense y seleccione **OK** (Aceptar).

Todos los informes del sensor ahora se etiquetan como Alertas de sensor.

## <a name="add-custom-fields-to-the-alerts"></a>Adición de campos personalizados a las alertas

**Para agregar campos personalizados a alertas**:

1. Seleccione **Extract Property** (Extraer propiedad).

1. Seleccione **Regex Based** (Basado en Regex).

1. Configure los campos siguientes:
   - Nueva propiedad: _elija cualquiera en la lista siguiente_
      - Sensor Alert Description (Descripción de la alerta de sensor)
      - Sensor Alert ID (Identificador de la alerta de sensor)
      - Sensor Alert Score (Puntuación de la alerta de sensor)
      - Sensor Alert Title (Título de la alerta de sensor)
      - Sensor Destination Name (Nombre del destino del sensor)
      - Sensor Direct Redirect (Redirección directa del sensor)
      - Sensor Sender IP (Dirección IP del remitente del sensor)
      - Sensor Sender Name (Nombre del remitente del sensor)
      - Sensor Alert Engine (Motor de alertas del sensor)
      - Sensor Source Device Name (Nombre de dispositivo de origen del sensor)
   - Marque **Optimize Parsing** (Optimizar análisis)
   - Tipo de campo: `AlphaNumeric`
   - Marque **Enabled** (Habilitado)
   - Log Source Type (Tipo de origen de registro): `Universal LEAF`
   - Origen de registro: `<Sensor Name>`
   - Nombre de evento (ya debe estar establecido en Alerta de sensor)
   - Grupo de captura: 1
   - Regex:
      - Expresión regular de la descripción de la alerta de sensor: `msg=(.*)(?=\t)`
      - Expresión regular del identificador de la alerta de sensor: `alertId=(.*)(?=\t)`
      - Expresión regular de la puntuación de la alerta de sensor: `Detected score=(.*)(?=\t)`
      - Expresión regular del título de la alerta de sensor: `title=(.*)(?=\t)`
      - Expresión regular del nombre del destino del sensor: `dstName=(.*)(?=\t)`
      - Expresión regular de la redirección directa del sensor: `rta=(.*)(?=\t)`
      - Expresión regular de la dirección IP del remitente del sensor: `reporter=(.*)(?=\t)`
      - Expresión regular del nombre del remitente del sensor: `senderName=(.*)(?=\t)`
      - Expresión regular del motor de alertas del sensor: `engine =(.*)(?=\t)`
      - Expresión regular del nombre de dispositivo de origen del sensor: `src`

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a empezar a trabajar con la integración de QRadar. Continúe para obtener información sobre cómo [integrar ServiceNow con Azure Defender para IoT](tutorial-servicenow.md).

> [!div class="nextstepaction"]
> [Integración de ServiceNow con Azure Defender para IoT](tutorial-servicenow.md)
