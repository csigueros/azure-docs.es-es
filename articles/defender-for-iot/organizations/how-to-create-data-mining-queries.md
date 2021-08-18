---
title: Creación de informes de minería de datos
description: Genere información exhaustiva y detallada sobre los dispositivos de red en varias capas como protocolos, versiones de firmware o comandos de programación.
ms.date: 01/20/2021
ms.topic: how-to
ms.openlocfilehash: 99754959e7a3a08b4d763b85b0b9315476969774
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020618"
---
# <a name="sensor-data-mining-queries"></a>Consultas de minería de datos del sensor

## <a name="about-sensor-data-mining-queries"></a>Acerca de las consultas de minería de datos del sensor

Las herramientas de minería de datos permiten generar información exhaustiva y detallada sobres los dispositivos de red en varias capas. Por ejemplo, puede crear una consulta basada en:

- Períodos de tiempo

- Conexiones a Internet

- Puertos

- Protocolos

- Versiones de firmware

- Comandos de programación

- Inactividad del dispositivo

Puede ajustar el informe en función de los filtros. Por ejemplo, puede consultar una subred específica en la que se ha actualizado el firmware.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Lista de dispositivos activos.":::

Existen varias herramientas disponibles para administrar consultas. Por ejemplo, puede exportar y guardar.

> [!NOTE]
> Los administradores y los analistas de seguridad tienen acceso a las opciones de minería de datos.

### <a name="dynamic-updates"></a>Actualizaciones dinámicas

Las consultas de minería de datos que se crean se actualizan dinámicamente cada vez que se abren. Por ejemplo:

- Si crea un informe para las versiones de firmware en los dispositivos el 1 de junio y abre el informe de nuevo el 10 de junio, este informe se actualizará con la información precisa para el 10 de junio.

- Si crea un informe para detectar nuevos dispositivos descubiertos en los últimos 30 días el 1 de junio y abre el informe el 30 de junio, se mostrarán los resultados de los últimos 30 días.

### <a name="data-mining-use-cases"></a>Casos de uso de minería de datos

Puede usar consultas para controlar una amplia gama de necesidades de seguridad para varios equipos de seguridad:

- **Respuesta a los incidentes SOC**: genere un informe en tiempo real para ayudar a gestionar la respuesta inmediata a los incidentes. Por ejemplo, la minería de datos puede generar un informe sobre una lista de dispositivos que pueden requerir una revisión.

- **Análisis forense**: genere un informe basado en datos históricos de informes de investigación.

- **Integridad de red de TI**: genere un informe que ayude a mejorar la seguridad general de la red. Por ejemplo, se puede generar un informe que muestre los dispositivos con credenciales de autenticación poco seguras.

- **Visibilidad**: genere un informe que abarque todos los elementos de consulta para ver todos los parámetros de línea de base de la red.

## <a name="data-mining-storage"></a>Almacenamiento de la minería de datos

La información de minería de datos se guarda y almacena continuamente, excepto cuando se elimina un dispositivo. Los resultados de minería de datos se pueden exportar y almacenar externamente en un servidor seguro. Además, el sensor realiza copias de seguridad diarias automáticas para garantizar la continuidad del sistema y la conservación de los datos.


## <a name="predefined-data-mining-queries"></a>Consultas de minería de datos predefinidas

Están disponibles las siguientes consultas predefinidas. Estas consultas se generan en tiempo real.

- **CVE**: una lista de dispositivos detectados con vulnerabilidades conocidas en las últimas 24 horas.

- **Excluded CVEs** (CVE excluidas): una lista de todas las CVE que se excluyeron manualmente. Para obtener resultados más precisos en los informes de VA y los vectores de ataque, puede personalizar la lista de CVE manualmente incluyendo y excluyendo CVE.

- **Internet activity** (Actividad de Internet): dispositivos que están conectados a Internet.

- **Nonactive devices** (Dispositivos no activos): dispositivos que no han establecido comunicación durante los últimos siete días.

- **Dispositivos activos**: dispositivos de red activos en las últimas 24 horas.

- **Acceso remoto**: dispositivos que se comunican a través de protocolos de sesión remota.

- **Programming commands** (Comandos de programación): dispositivos que envían programación industrial.

Se puede acceder a estos informes automáticamente desde la pantalla **Informes**, donde los usuarios de solo lectura y otros usuarios pueden verlos. Los usuarios de solo lectura no pueden acceder a los informes de minería de datos.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Pantalla de minería de datos.":::

## <a name="create-a-data-mining-query"></a>Creación de una consulta de minería de datos

Para crear un informe de minería de datos:

1. Seleccione **Minería de datos** en el menú lateral. Los informes sugeridos predefinidos aparecen automáticamente.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Seleccione Minería de datos en el panel lateral.":::

2. Seleccione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Seleccione **Nuevo informe** y defina el informe.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Cree un informe rellenando esta pantalla.":::

   Los siguientes parámetros están disponibles:

   - Proporcione un nombre y una descripción del informe.

   - En el caso de las categorías, seleccione:

      - **Categorías (todo)** para ver todos los resultados del informe sobre todos los dispositivos de la red.

      - **Genérico** para elegir categorías estándar.

   - Seleccione los parámetros de informe específicos que le interesen.

   - Elija un criterio de ordenación (**Ordenar por**). Ordene los resultados en función de la actividad o categoría.

   - Seleccione **Save to Report Pages** (Guardar en las páginas de informe) para guardar los resultados del informe como un informe al que se puede acceder desde la página **Informes**. Esto permitirá que los usuarios de solo lectura ejecuten el informe que ha creado.

   - Seleccione **Filtros (agregar)** para agregar más filtros. Se admiten solicitudes con comodín.

   - Especifique un grupo de dispositivos (definido en el mapa del dispositivo).

   - Especifique una dirección IP.

   - Especifique un puerto.

   - Especifique una dirección MAC.

4. Seleccione **Guardar**. Los resultados del informe se abren en la página **Minería de datos**.

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Resultados del informe como se muestran en la página Minería de datos.":::

### <a name="manage-data-mining-reports"></a>Administración de informes de minería de datos

En la tabla siguiente se describen las opciones de administración para la minería de datos:

| Imagen de icono | Descripción |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Editar los parámetros de informe. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Exportar como PDF. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Exportar como CSV. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Mostrar información adicional, como la fecha de la última modificación. Use esta característica para crear una instantánea del resultado de la consulta. Es posible que tenga que hacerlo para llevar a cabo una investigación más detallada con los jefes de equipo o analistas de SOC, por ejemplo. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Mostrar en la página **Informes** u ocultar en la página **Informes**. :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Ocultar o mostrar los informes."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Elimine el informe. |

#### <a name="create-customized-directories"></a>Creación de directorios personalizados 

Puede organizar la amplia información de las consultas de minería de datos creando directorios para las categorías. Por ejemplo, puede crear directorios para protocolos o ubicaciones.

Para crear un directorio nuevo:

1. Seleccione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: para agregar un directorio nuevo.

2. Seleccione **New Directory** (Nuevo directorio) para mostrar el formulario de directorio nuevo.

3. Asigne un nombre al directorio nuevo.

4. Arrastre los informes necesarios al directorio nuevo. En cualquier momento, puede volver a arrastrar el informe a la vista principal.

5. Haga clic con el botón derecho en el nuevo directorio para abrirlo, editarlo o eliminarlo.

#### <a name="create-snapshots-of-report-results"></a>Creación de instantáneas de los resultados del informe

Es posible que tenga que guardar determinados resultados de la consulta para llevar a cabo una investigación más detallada. Por ejemplo, puede que necesite compartir los resultados con varios equipos de seguridad.

Las instantáneas se guardan en los resultados del informe y no generan consultas dinámicas.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Instantáneas.":::

Para crear una instantánea:

1. Abra el informe requerido.

2. Seleccione el icono de información :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false":::.

3. Seleccione **Take New** (Crear nueva).

4. Escriba un nombre para la instantánea y seleccione **Guardar**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Realizar una instantánea.":::

#### <a name="customize-the-cve-list"></a>Personalización de la lista de CVE

Puede personalizar manualmente la lista de CVE de la siguiente manera:

  - Incluir o excluir CVE

  - Cambiar la puntuación de CVE

Para realizar cambios manuales en el informe de CVE:

1.  Seleccione **Minería de datos** en el menú lateral.

2. Seleccione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: en la esquina superior izquierda de la ventana **Minería de datos**. Seleccione **Nuevo informe**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Crear un informe nuevo.":::

3. En el panel izquierdo, seleccione una de las opciones siguientes:

   - **Known Vulnerabilities** (Vulnerabilidades conocidas): selecciona ambas opciones y presenta los resultados en las dos tablas del informe, una con CVE y la otra con las CVE excluidas.

   - **CVE**: seleccione esta opción para presentar una lista de todas las CVE.

   - **Excluded CVEs** (CVE excluidas): Seleccione esta opción para presentar una lista de todas las CVE excluidas.

4. Rellene la información de **Nombre** y **Descripción** y seleccione **Guardar**. El nuevo informe aparece en la ventana **Minería de datos**.

5. Para excluir CVE, abra el informe de minería de datos para las CVE. Aparece la lista de todas las CVE.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="Informe de CVE.":::

6. Para habilitar la selección de elementos de la lista, seleccione :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: y seleccione las CVE que quiere personalizar. La barra **Operaciones** aparece en la parte inferior.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Captura de pantalla de la barra Operaciones de Minería de datos.":::

7. Seleccione las CVE que quiere excluir y, posteriormente, seleccione **Eliminar registros**. Las CVE que ha seleccionado no aparecen en la lista de CVE y se mostrarán en la lista de CVE excluidas al generar una.

8. Para incluir las CVE excluidas en la lista de CVE, genere el informe para las CVE excluidas y elimine de esa lista los elementos que quiere incluir de nuevo en la lista de CVE.

9. Seleccione las CVE en las que quiere cambiar la puntuación y, posteriormente, seleccione **Update CVE Score** (Actualizar puntuación de CVE).

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Actualice la puntuación de CVE.":::

10. Escriba la nueva puntuación y seleccione **Aceptar**. La puntuación actualizada aparece en las CVE que ha seleccionado.



## <a name="sensor-reports-based-on-data-mining"></a>Informes de sensores basados en la minería de datos

Los informes regulares, a los que se accede desde la opción **Informes**, son informes de minería de datos predefinidos. No son consultas dinámicas como están disponibles en la minería de datos, sino una representación estática de los resultados de la consulta de minería de datos.

Los resultados de la consulta de minería de datos no están disponibles para los usuarios de solo lectura. Los administradores y los analistas de seguridad que quieren que los usuarios de solo lectura tengan acceso a la información generada por las consultas de minería de datos deben guardar la información como informe.

Los informes reflejan la información generada por los resultados de la consulta de minería de datos. Esto incluye los informes de minería de datos predeterminados, que están disponibles en la vista Informes. El administrador y los analistas de seguridad también pueden generar consultas de minería de datos personalizadas y guardarlas como informes. Estos informes también están disponibles para los usuarios de solo lectura.

Para generar un informe:

1. Seleccione **Informes** en el menú lateral.

2. Elija el informe necesario para mostrar. La elección puede ser de informes de tipo **Personalizado** o **Generado automáticamente**, como **Programming Commands** (Comandos de programación) y **Acceso remoto**.

3. Puede exportar el informe seleccionando uno de los iconos de la parte superior derecha de la pantalla:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Exportar a un archivo PDF.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Exportar a un archivo CSV.

> [!NOTE] 
> El usuario de solo lectura solo puede ver los informes creados para esta categoría de usuarios.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Seleccionar el informe que se generará.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Informe de acceso remoto generado.":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>Informes de la consola de administración local basados en informes de minería de datos

La consola de administración local permite generar informes para cada sensor que está conectado a ella. Los informes se basan en las consultas de minería de datos en el sensor que se realizan.

Se pueden generar los siguientes informes:

- **Active Devices (Last 24 Hours)** (Dispositivos activos (últimas 24 horas)): presenta una lista de dispositivos que muestran actividad de red en un período de 24 horas.

- **Non-Active Devices (Last 7 Days)** (Dispositivos no activos (últimos 7 días)): presenta una lista de dispositivos que no muestran ninguna actividad de red en los últimos siete días.

- **Programming Commands** (Comandos de programación): presenta una lista de dispositivos que han enviado comandos de programación en las últimas 24 horas.

- **Acceso remoto**: presenta una lista de dispositivos a los que se ha accedido desde orígenes remotos en las últimas 24 horas.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Captura de pantalla de la vista de informes.":::

Al elegir el sensor desde la consola de administración local, todos los informes personalizados configurados en ese sensor aparecen en la lista de informes. Para cada sensor, puede generar un informe predeterminado o un informe personalizado configurado en ese sensor.

Para generar un informe:

1. En el panel izquierdo, seleccione **Informes**. Aparecerá la ventana **Informes**.

2. En la lista desplegable **Sensores**, seleccione el sensor para el que quiere generar el informe.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Captura de pantalla de la vista de sensores.":::

3. En la lista desplegable de la derecha, seleccione el informe que quiere generar.

4. Para crear un PDF de los resultados del informe, seleccione :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false":::.
