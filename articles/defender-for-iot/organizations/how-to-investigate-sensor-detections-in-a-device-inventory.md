---
title: Obtener información sobre los dispositivos detectados por un sensor específico
description: El inventario de dispositivos muestra una amplia gama de atributos de dispositivo que detecta un sensor.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 10df10a64059e5f5673a9e4ae6f18a09e4c41838
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515280"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Investigación de la detección de todos los sensores de la empresa en un inventario de dispositivos

El inventario de dispositivos muestra una amplia gama de atributos de dispositivo que detecta un sensor. Hay opciones disponibles para:

 - Filtrar la información fácilmente.

 - Exportar información a un archivo CSV.

 - Importar los detalles del registro de Windows.

 - Crear grupos para mostrar en el mapa del dispositivo.

## <a name="view-device-attributes-in-the-device-inventory"></a>Ver los atributos del dispositivo en el inventario de dispositivos

Los siguientes atributos aparecen en la tabla de inventario de dispositivos.

| Parámetro | Descripción |
|--|--|
| Nombre | Nombre del dispositivo tal y como lo ha detectado el sensor o tal y como lo introdujo el usuario. |
| Tipo | Tipo del dispositivo tal como lo ha determinado el sensor o tal y como lo introdujo el usuario. |
| Vendor | Nombre del proveedor del dispositivo, tal y como se define en la dirección MAC. |
| Sistema operativo | Sistema operativo del dispositivo, si se detecta. |
| Firmware version | Firmware del dispositivo, si se detecta. |
| Dirección IP | Dirección IP del dispositivo donde se definió. |
| VLAN | VLAN del dispositivo. Para obtener más información sobre cómo indicar al sensor que detecte redes VLAN, consulte [Definición de nombres de VLAN](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (how-to-define-management-console-network-settings.md#define-vlan-names). |
| Dirección MAC | La dirección MAC del dispositivo. |
| Protocolos | Protocolos que usa el dispositivo. |
| Alertas no confirmadas | Número de alertas no confirmadas asociadas a este dispositivo. |
| Estado de autorización | Estado de autorización, tal y como lo ha definido el usuario:<br />- **True**: El dispositivo se ha autorizado.<br />- **False**: El dispositivo no se ha autorizado. |
| Conocido como escáner | Definido como un dispositivo de análisis de redes por parte del usuario. |
| Dispositivo de programación | Definido como un dispositivo de programación autorizado por el usuario. <br />- **True**: El dispositivo realiza actividades de programación para PLC, RTU y controladores, que son pertinentes para las estaciones de ingeniería. <br />- **False**: El dispositivo no es un dispositivo de programación. |
| Grupos | Grupos en los que participa este dispositivo. |
| Última actividad | Última actividad realizada por el dispositivo. |
| Discovered | Cuándo apareció por primera vez en la red este dispositivo. |

Para ver el inventario de dispositivos:

1. En el panel izquierdo, seleccione **Dispositivos**. El panel **Dispositivos** se abre a la derecha.

2. En el panel **Dispositivos**, seleccione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false":::.

Para ocultar y mostrar columnas, personalice la tabla de inventario de dispositivos:

1. En el menú superior derecho del inventario de dispositivos, seleccione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false":::.

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Pantalla de configuración de inventario de dispositivos.":::

2. En la ventana **Configuración de inventario de dispositivos**, seleccione las columnas que desea mostrar en la tabla de inventario de dispositivos.

3. Cambie la ubicación de las columnas en la tabla mediante el uso de flechas.

4. Seleccione **Guardar**. La ventana **Configuración de inventario de dispositivos** se cierra y la nueva configuración aparece en la tabla.

### <a name="create-temporary-device-inventory-filters"></a>Crear filtros temporales de inventario de dispositivos

Puede establecer un filtro que defina qué información se muestra en la tabla. Por ejemplo, puede decidir que desea ver solo la información del dispositivo PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Aprendizaje de los dispositivos.":::

El filtro no se guarda al salir del inventario.

### <a name="save-device-inventory-filters"></a>Guardar filtros de inventario de dispositivos

Puede guardar un filtro o una combinación de filtros que necesite y volver a aplicarlos en el inventario de dispositivos. Cree filtros más amplios basados en un determinado tipo de dispositivo, o filtros más limitados basados en un tipo y un protocolo específicos.

Los filtros que guarda también se guardan como grupos de mapas de dispositivos. Esta característica proporciona un nivel de granularidad adicional en la visualización de los dispositivos de red en el mapa.

Para crear filtros:

1. Seleccione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: en la columna que desea filtrar.

2. En el cuadro de diálogo **Filtro**, seleccione el tipo de filtro:.

   - **Equals** (es igual a): Valor exacto según el cual se desea filtrar la columna. Por ejemplo, si filtra la columna de protocolo según **Equals** y `value=ICMP`, la columna presentará los dispositivos que solo usen el protocolo ICMP.

   - **Contains** (contiene): Valor que se encuentra entre otros valores de la columna. Por ejemplo, si filtra la columna de protocolo según **Contains** y `value=ICMP`, la columna presentará los dispositivos que solo usen el protocolo ICMP como parte de la lista de protocolos que usa el dispositivo.

3. Para organizar la información de la columna por orden alfabético, seleccione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false":::. Modifique el orden seleccionando las flechas :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: y :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false":::.

4. Para guardar un filtro nuevo, defina el filtro y seleccione **Guardar como**.

5. Para cambiar las definiciones de filtro, cambie las definiciones y seleccione **Guardar cambios**.

Para ver los filtros:

- Abra el panel izquierdo y vea los filtros que ha guardado:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Vea los filtros en el panel izquierdo.":::

### <a name="view-filtered-information-as-a-map-group"></a>Ver información filtrada como grupo de mapas

Al cambiar a la vista de mapa, los dispositivos filtrados se resaltan y se filtran. El grupo de filtros que ha guardado aparece en el menú lateral bajo el grupo **Filtros de inventario de dispositivos**.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Vea los filtros en la vista de mapa.":::

## <a name="learn-windows-registry-details"></a>Obtener información sobre el registro de Windows

Además de reconocer dispositivos OT, puede detectar servidores y estaciones de trabajo de Microsoft Windows. Estos dispositivos también se muestran en el inventario de dispositivos. Después de reconocer los dispositivos, puede enriquecer el inventario de dispositivos con información detallada de Windows, como:

- Versión de Windows instalada

- Aplicaciones instaladas

- Información del nivel de revisión

- Abrir puertos

- Información más sólida sobre las versiones del sistema operativo

Hay dos opciones disponibles para recuperar esta información:

- Sondeo activo mediante exámenes de WMI programados. 

- Encuestas locales mediante la distribución y ejecución de un script en el dispositivo. Al trabajar con scripts locales, se omiten los riesgos de ejecutar el sondeo de WMI en un punto de conexión. También es útil para las redes reguladas con cascadas y elementos unidireccionales.

En este artículo se describe cómo hacer una encuesta local del registro de puntos de conexión de Windows con un script. Esta información se utilizará para generar alertas, notificaciones, informes de minería de datos, evaluaciones de riesgos e informes de vectores de ataque.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Captura de pantalla de minería de datos.":::

Las encuestas son compatibles con los siguientes sistemas operativos Windows:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Antes de empezar

Para trabajar con el script, debe cumplir los siguientes requisitos:

- Se requieren permisos de administrador para ejecutar el script en el dispositivo.

- El sensor ya debe haber reconocido previamente el dispositivo Windows. Esto significa que si el dispositivo ya existe, el script recuperará su información.

- Un sensor está supervisando la red a la que está conectado el equipo Windows.

### <a name="acquire-the-script"></a>Adquirir script

Para recibir el script, [póngase en contacto con el servicio de asistencia al cliente](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>Implementar el script

Puede implementar el script una vez o programar consultas en curso mediante herramientas y métodos de implementación automatizados estándar.

### <a name="about-the-script"></a>Acerca del script

- El script se ejecuta como una utilidad y no como un programa instalado. Ejecutar el script no afecta al punto de conexión.

- Los archivos que genera el script permanecen en la unidad local hasta que se eliminan.

- Los archivos que genera el script se colocan uno junto a otro. No los separe.

- Si vuelve a ejecutar el script en la misma ubicación, estos archivos se sobrescriben.

Para ejecutar el script:  

1. Copie el script en una unidad local y descomprímalo. Aparecen los archivos siguientes:

    - start.bat

    - settings.json

    - data.bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Vista de los archivos en el Explorador de archivos.":::

2. Ejecute el archivo `run.bat`.

3. Una vez sondeado el registro, aparece el archivo de la instantánea de CX con la información del registro.

4. El nombre de archivo indica el nombre del sistema y la fecha y hora de la instantánea. Un ejemplo de nombre de archivo podría ser `CX-snaphot_SystemName_Month_Year_Time`.

### <a name="import-device-details"></a>Importar detalles del dispositivo

La información aprendida en cada punto de conexión se debe importar en el sensor.

Los archivos generados a partir de las consultas pueden colocarse en una carpeta a la que se pueda acceder desde los sensores. Use herramientas y métodos estándar y automatizados para mover los archivos de cada punto de conexión de Windows a la ubicación en la que se van a importar al sensor.

No actualice los nombres de archivo.

Para importar:

1. Seleccione **Importar configuración** en el cuadro de diálogo **Importar configuración de Windows**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importe sus configuraciones de Windows.":::

2. Seleccione **Agregar** y, luego, seleccione todos los archivos (Ctrl + A).

3. Seleccione **Cerrar**. Se importa la información del registro del dispositivo. Si hay algún problema al cargar uno de los archivos, se le informará de que se ha producido un error en la carga de archivos.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="La carga de archivos agregados se ha realizado correctamente.":::

## <a name="view-and-delete-inactive-devices-from-the-inventory"></a>Visualización y eliminación de dispositivos inactivos del inventario

Es posible que le interese ver los dispositivos de la red que están inactivos y eliminarlos.
Los dispositivos pueden quedar inactivos por varias causas:
- Puertos SPAN mal configurados
- Cambios en la cobertura de red
- Desconexión de la red

La eliminación de dispositivos inactivos permite lo siguiente:

- Defender for IoT puede crear una representación más precisa de la actividad de red actual.
- Evaluar mejor los dispositivos confirmados al administrar suscripciones.
- Reducir el desorden en la pantalla.

### <a name="view-inactive-devices"></a>Visualización de los dispositivos inactivos

Puede filtrar el inventario para mostrar los dispositivos que están inactivos:

- durante 7 días o más;
- durante 14 días o más;
- 30 días o más; o
- 90 días o más.

**Para filtrar el inventario:**
  
1. Seleccione el icono de filtro **Visto por última vez** en el inventario.
1. Seleccione una opción de filtro.
1. Seleccione **Aplicar**.

### <a name="delete-inactive-devices"></a>Eliminación de los dispositivos inactivos

Los dispositivos que elimine del inventario se quitarán del mapa y no entrarán en el cálculo al generar informes de Defender para IoT, por ejemplo, informes de minería de datos, evaluación de riesgos y vectores de ataque.

Se le pedirá que registre un motivo para eliminar los dispositivos. Esta información, así como la fecha y hora y el número de dispositivos eliminados, aparece en la escala de tiempo del evento.

**Para eliminar dispositivos del inventario:**
  
1. Seleccione el icono de filtro **Visto por última vez** en el inventario.
1. Seleccione una opción de filtro.
1. Seleccione **Aplicar**.
1. Seleccione **Delete Inactive Devices** (Eliminar dispositivos inactivos).
1. En el cuadro de diálogo de confirmación que se abre, escriba el motivo de la eliminación y seleccione **Eliminar**. Se eliminarán todos los dispositivos detectados dentro del intervalo del filtro. Si elimina un gran número de dispositivos, el proceso de eliminación puede tardar unos minutos.

## <a name="export-device-inventory-information"></a>Exportar la información del inventario de dispositivos

Puede exportar la información de inventario de dispositivos a un archivo de Excel.

Para exportar a un archivo CSV:

- En el menú superior derecho del inventario de dispositivos, seleccione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false":::. Se genera y se descarga el informe CSV.

## <a name="see-also"></a>Consulte también

[Investigación de todas las detecciones de los sensores de empresa en un inventario de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Trabajar con vistas de mapa del sitio](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
