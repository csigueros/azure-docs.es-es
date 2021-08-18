---
title: Importación de la información del dispositivo
description: Los sensores de Defender para IoT supervisan y analizan el tráfico reflejado. En estos casos, se recomienda importar datos para enriquecer la información en los dispositivos que ya se han detectado.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: eb82a3e5d360913acfead06adc03194d80f7fccb
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020265"
---
# <a name="import-device-information-to-a-sensor"></a>Importación de la información del dispositivo al sensor

Un sensor de Azure Defender para IoT supervisa y analiza el tráfico reflejado. En algunos casos, debido a las directivas de configuración de red específicas de la organización, es posible que cierta información no se transmita.

En estos casos, se recomienda importar datos para enriquecer la información en los dispositivos que ya se han detectado. Hay dos opciones disponibles para importar información a los sensores:

- **Importación desde el mapa**: actualice el nombre del dispositivo, el tipo, el grupo o la capa de Purdue en el mapa.

- **Importación desde Importar configuración**: importe el sistema operativo del dispositivo, la dirección IP, el nivel de revisión o el estado de la autorización.

## <a name="import-from-the-map"></a>Importación desde el mapa

En esta sección se describe cómo importar nombres de dispositivos, tipos, grupos o capas de Purdue en el mapa de dispositivos. Este proceso se realiza desde el mapa.

Los siguientes son los requisitos de importación:

- **Nombres**: pueden tener hasta 30 caracteres.

- **Tipo** o **capa de Purdue**: utilice las opciones que aparecen en el cuadro de diálogo **Propiedades del dispositivo**. (Haga clic con el botón derecho en un dispositivo y seleccione **Ver propiedades**).

- **Grupo de dispositivos**: cree un nuevo grupo de hasta 30 caracteres. 

> [!NOTE]
> Para evitar conflictos, no importe los datos que haya exportado de un sensor a otro sensor.

Para realizar la importación, haga lo siguiente:

1. Seleccione **Dispositivos** en el menú lateral.

2. En la esquina superior derecha de la ventana **Dispositivos**, seleccione :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Captura de pantalla de la ventana de dispositivos.":::

3. Seleccione **Exportar dispositivos**. Se muestra una amplia gama de información en el archivo exportado. Esta información incluye los protocolos que el dispositivo usa y el estado de autorización de dicho dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="La información del archivo exportado.":::

4. En el archivo CSV, cambie solo el nombre del dispositivo, el tipo, el grupo y la capa de Purdue. A continuación, guarde el archivo. 

   Use los estándares de uso de mayúsculas que se muestran en el archivo exportado. Por ejemplo, para la capa de Purdue, use siempre mayúscula inicial.

5. En el menú desplegable **Importar/Exportar** de la ventana **Dispositivos**, seleccione **Importar dispositivos**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importación de dispositivos a través de la ventana Dispositivo.":::

6. Seleccione **Importar dispositivos** y seleccione el archivo CSV que quiere importar. Los mensajes de estado de la importación aparecen en la pantalla hasta que se cierra el cuadro de diálogo **Importar dispositivos**.

## <a name="import-from-import-settings"></a>Importación desde Importar configuración

En esta sección se describe cómo importar la dirección IP del dispositivo, el sistema operativo, el nivel de revisión o el estado de la autorización al mapa de dispositivos. Para ello, haga lo siguiente en el cuadro de diálogo **Importar configuración**.

Para importar la dirección IP, el sistema operativo y el nivel de revisión, haga lo siguiente:

1. Descargue el archivo [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) del [Centro de ayuda](https://cyberx-labs.zendesk.com/hc/en-us) y escriba la información de la siguiente manera:

   - **Dirección IP**: escriba la dirección IP del dispositivo.

   - **Sistema operativo**: seleccione uno de la lista desplegable.

   - **Última actualización**: use el formato AAAA-MM-DD.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Pantalla de opciones.":::

2. En el menú lateral, seleccione **Importar configuración**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importación de la configuración.":::

3. Para cargar la configuración necesaria, en la sección **Información del dispositivo**, seleccione **Agregar** y cargue el archivo CSV que preparó.

Para importar el estado de autorización, haga lo siguiente:

1. Descargue el archivo [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) del centro de ayuda de Defender para IoT y guárdelo. Compruebe que ha guardado el archivo en formato CSV.

2. Escriba la información de la siguiente manera:

   - **Dirección IP**: la dirección IP del dispositivo.

   - **Nombre**: el nombre del dispositivo autorizado. Asegúrese de que los nombres sean correctos. Los nombres asignados a los dispositivos de la lista importada sobrescriben los nombres que se muestran en el mapa de dispositivos.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Archivos de Excel con la lista de dispositivos importados.":::

3. En el menú lateral, seleccione **Importar configuración**.

4. En la sección **Dispositivos autorizados**, seleccione **Agregar** y cargue el archivo CSV que guardó.

Cuando se importa la información, recibe alertas sobre dispositivos no autorizados para todos aquellos que no aparecen en esta lista.

## <a name="import-device-information-to-the-sensor"></a>Importación de la información del dispositivo al sensor

El sensor supervisa y analiza el tráfico reflejado. En algunos casos, debido a las directivas de configuración de red específicas de la organización, es posible que cierta información no se transmita.

En estos casos, se recomienda importar datos para enriquecer la información del dispositivo en los dispositivos que ya se han detectado. Hay dos opciones disponibles para importar información a los sensores:

- **Importación desde el mapa**: actualice el nombre del dispositivo, el tipo, el grupo o la capa de Purdue en el mapa.

- **Importación desde Importar configuración**: importe el sistema operativo del dispositivo, la dirección IP, el nivel de revisión o el estado de la autorización.

### <a name="import-from-the-map"></a>Importación desde el mapa

En esta sección se describe cómo importar nombres de dispositivos, tipos, grupos o capas de Purdue en el mapa de dispositivos. Este proceso se realiza desde el mapa.

Los siguientes son los requisitos de importación:

- **Nombres**: pueden tener hasta 30 caracteres.

- **Tipo** o **capa de Purdue**: utilice las opciones que aparecen en el cuadro de diálogo **Propiedades del dispositivo**. (Haga clic con el botón derecho en un dispositivo y seleccione **Ver propiedades**).

- **Grupo de dispositivos**: cree un nuevo grupo de hasta 30 caracteres. 

> [!NOTE]
> Para evitar conflictos, no importe los datos que haya exportado de un sensor a otro sensor.

Para realizar la importación, haga lo siguiente:

1. Seleccione **Dispositivos** en el menú lateral.

2. En la esquina superior derecha de la ventana **Dispositivos**, seleccione :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Ventana para elegir el dispositivo.":::

3. Seleccione **Exportar dispositivos**. Se muestra una amplia gama de información en el archivo exportado. Por ejemplo, los protocolos que el dispositivo usa y el estado de autorización de dicho dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="La información del archivo exportado.":::

4. En el archivo CSV, solo cambie el nombre del dispositivo, el tipo, el grupo y la capa de Purdue. A continuación, guarde el archivo. 

   Use los estándares de uso de mayúsculas que se muestran en el archivo exportado. Por ejemplo, para la capa de Purdue, use siempre mayúscula inicial.

5. En el menú desplegable **Importar/Exportar** de la ventana **Dispositivos**, seleccione **Importar dispositivos**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importación de dispositivos.":::

6. Seleccione **Importar dispositivos** y seleccione el archivo CSV que quiere importar. Los mensajes de estado de la importación aparecen en la pantalla hasta que se cierra el cuadro de diálogo **Importar dispositivos**.

### <a name="import-from-import-settings"></a>Importación desde Importar configuración 

En esta sección se describe cómo importar la dirección IP del dispositivo, el sistema operativo, el nivel de revisión o el estado de la autorización al mapa de dispositivos. Para ello, haga lo siguiente en el cuadro de diálogo **Importar configuración**.

Para importar la dirección IP, el sistema operativo y el nivel de revisión, haga lo siguiente:

1. Descargue el archivo [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) del [Centro de ayuda](https://cyberx-labs.zendesk.com/hc/en-us) y escriba la información de la siguiente manera:

   - **Dirección IP**: la dirección IP del dispositivo.

   - **Sistema operativo**: seleccione uno de la lista desplegable.

   - **Fecha de la última actualización**: use el formato AAAA-MM-DD.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Contenido de la pantalla.":::

2. En el menú lateral, seleccione **Importar configuración**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Rellene la pantalla Importar configuración.":::

3. Para cargar la configuración necesaria, en la sección **Información del dispositivo**, seleccione **Agregar** y cargue el archivo CSV que ha preparado.

Para importar el estado de autorización, haga lo siguiente:

1. Descargue el archivo [authorized_devices - examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) del centro de ayuda de Defender para IoT y guárdelo. Compruebe que ha guardado el archivo en formato CSV.

2. Escriba la información de la siguiente manera:

   - **Dirección IP**: la dirección IP del dispositivo.

   - **Nombre**: el nombre del dispositivo autorizado. Compruebe que los nombres sean correctos. Los nombres asignados a los dispositivos de la lista importada sobrescriben los nombres que se muestran en el mapa de dispositivos.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="La lista de importación al mapa de dispositivos.":::

3. En el menú lateral, seleccione **Importar configuración**.

4. En la sección **Dispositivos autorizados**, seleccione **Agregar** y cargue el archivo CSV que guardó.

Cuando se importa la información, recibe alertas sobre dispositivos no autorizados para todos aquellos que no aparecen en esta lista.

## <a name="see-also"></a>Consulte también

[Control del tráfico que se supervisa](how-to-control-what-traffic-is-monitored.md)

[Investigación de las detecciones de sensores en un inventario de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
