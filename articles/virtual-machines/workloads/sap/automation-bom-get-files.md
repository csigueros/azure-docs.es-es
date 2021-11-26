---
title: Obtención de elementos multimedia de SAP para la lista de materiales
description: Cómo descargar elementos multimedia de SAP para usarlos en la lista de materiales (BOM) para el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 85680b151a108e3ee9854b4d531669ba1995bf7d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725355"
---
# <a name="acquire-media-for-bom-creation"></a>Adquisición de elementos multimedia para la creación de la lista de materiales

El [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) usa una lista de materiales (BOM). Para crear la lista de materiales, debe buscar y descargar los soportes de instalación de SAP pertinentes. Después, debe cargar estos archivos multimedia en la cuenta de almacenamiento de Azure.

> [!NOTE]
> En esta guía se tratan temas de implementación avanzada. Para obtener una explicación básica de cómo implementar el marco de automatización, consulte la [guía de introducción](automation-get-started.md).

Esta guía es para las configuraciones que usa la aplicación SAP (DB) o las bases de datos de HANA.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de SAP con permisos para descargar el software de SAP y acceder a Maintenance Planner.
- Una instalación del [administrador de descargas de SAP](https://support.sap.com/en/my-support/software-downloads.html).
- Información sobre el sistema SAP:
    - Nombre de usuario y contraseña de la cuenta SAP
    - El producto del sistema SAP que se va a implementar (como **S/4HANA**)
    - Identificador del sistema SAP (SID de SAP)
    - Todos los requisitos de los paquetes de idioma
    - Sistema operativo (SO) que se usará en la infraestructura de la aplicación
- Suscripción a Azure. Si aún no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="acquire-media"></a>Adquisición de elementos multimedia

Para preparar la [descarga del soporte de instalación de SAP](#download-media):

1. En el equipo, cree un directorio único para las descargas de SAP de la pila. Por ejemplo, `~/Downloads/S4HANA_1909_SP2/`.

1. Inicie sesión en [SAP ONE Support Launchpad](https://launchpad.support.sap.com/).

1. Borre la cesta de descarga.

    1. Vaya a **Descargas de software**.

    1. Seleccione **Download Basket** (Cesta de descarga).

    1. Seleccione todos los elementos de la cesta.

    1. Seleccione **X** para eliminar todos los elementos de la cesta.

1. Agregue la utilidad SAPCAR a la cesta de descarga.

    1. En la barra de búsqueda, asegúrese de que el tipo de búsqueda esté establecido en **Descargas**.

    1. Escriba `SAPCAR` en la barra de búsqueda y seleccione **Buscar**.

    1. En la tabla **Items Available to Download** (Elementos disponibles para la descarga), seleccione la fila de **SAPCAR** con **Maintenance Software Component** (Componente de software de mantenimiento). Este paso filtra las descargas disponibles para la versión más reciente de la utilidad.

    1. Asegúrese de que el menú desplegable de la tabla muestra el tipo de sistema operativo correcto. Por ejemplo, `LINUX ON X86_64 64BIT`.

    1. Active la casilla situada junto al nombre de archivo del ejecutable de SAPCAR. Por ejemplo, `SAPCAR_1320-80000935.EXE`.

    1. Seleccione el icono del carro de la compra para agregar la selección a la cesta de descarga.

1. Inicie sesión en [Maintenance Planner](https://support.sap.com/en/alm/solution-manager/processes-72/maintenance-planner.html).

1. Diseñe el sistema SAP. Por ejemplo, si va a usar **S/4HANA**:

    1. Seleccione el plan para **SAP S/4HANA**.

    1. Opcionalmente, cambie el nombre del plan de mantenimiento.

    1. Seleccione **Install New S4HANA System** (Instalar nuevo sistema S4HANA). 
    
    1. Seleccione **Siguiente**.

    1. En **Install a New System** (Instalar nuevo sistema), escriba el identificador de seguridad que va a usar.

    1. En **Versión de destino**, seleccione su versión de destino de SAP. Por ejemplo, **SAP S/4HANA 2020**.

    1. En **Target Stack** (Pila de destino), seleccione la pila de destino. Por ejemplo, **Initial Shipment Stack** (Pila de envío inicial).

    1. Si es necesario, seleccione las **Target Product Instances** (Instancias de producto de destino).

    1. Seleccione **Siguiente**.

1. Diseñe la implementación conjunta.

    1. Seleccione **Co-Deployed with Backend** (Implementado conjuntamente con el back-end).

    1. En **Versión de destino**, seleccione su versión de destino para la implementación conjunta. Por ejemplo, **SAP FIORI FOR SAP S/4HANA 2020**.

    1. En **Target Stack** (Pila de destino), seleccione la pila de destino de la implementación conjunta. Por ejemplo, **Initial Shipment Stack** (Pila de envío inicial).

    1. Seleccione **Siguiente**.

1. Seleccione **Continue Planning** (Continuar planeamiento). Si va a usar un *nuevo sistema*, seleccione **Next** (Siguiente). Si va a utilizar un *sistema existente*, efectúe los siguientes cambios:

    1. En los **archivos dependientes de OS/DB**, seleccione **Linux on x86_64 64bit**.

    1. Seleccione **Confirm Selection** (Confirmar selección).

    1. Seleccione **Next** (Siguiente).

1. Opcionalmente, en **Select Stack Independent Files** (Seleccionar archivos independientes de la pila), configure la opción para bases de datos que no son de ABAP. Puede elegir expandir la base de datos y anular la selección de los archivos de idioma no necesarios.

1. Seleccione **Next** (Siguiente).

1. Descargue los archivos XML de la pila en el directorio de descarga de la pila que creó anteriormente.

    1. Seleccione **Push to Download Basket** (Insertar en la cesta de descarga).

    1. Seleccione **Additional Downloads** (Descargas adicionales).

    1. Seleccione **Download Stack Text File** (Descargar archivo de texto de la pila).

    1. Seleccione **Download PDF** (Descargar PDF).

    1. Seleccione **Exportar a Excel**.

    1. Vuelva a la cesta de descarga en SAP Launchpad. Es posible que tenga que actualizar la página para ver las nuevas selecciones.

    1. Seleccione el icono de **T** para descargar un archivo con las direcciones URL para la cesta de descargas.

## <a name="get-download-basket-manifest"></a>Obtención del manifiesto de la cesta de descarga

> [!IMPORTANT]
> Siga estos pasos solo si desea ejecutar la generación de BOM con scripts. Debe realizar estas acciones antes de ejecutar SAP Download Manager. Si no desea ejecutar la generación de BOM con scripts, [pase a la sección siguiente](#download-media).

Para obtener el archivo JSON del manifiesto de la cesta de descarga de SAP (`DownloadBasket.json`):

1. Abra la utilidad **Postman**.

1. Agregue una nueva solicitud seleccionando el botón de signo más ( **+** ) en la pestaña del área de trabajo. Se abre una nueva página con la solicitud.

1. En la pestaña **Params**, establezca el tipo de solicitud en `GET`.

1. En la dirección URL de la solicitud, escriba `https://tech.support.sap.com:443/odata/svt/swdcuisrv/DownloadContentSet?_MODE=BASKET_CONTENT&_VERSION=3.1.2&$format=json`.

1. Seleccione la pestaña **Autorización**.

1. En **Type** (Tipo), seleccione **Basic Auth** (Autenticación básica).

1. En **Username** (Nombre de usuario), escriba su nombre de usuario de SAP.

1. En **Password** (Contraseña), escriba una contraseña de SAP.

1. Seleccione la pestaña **Encabezados**.

1. Desactive las casillas Accept-Encoding y User-Agent.

1. Seleccione el botón **Enviar**.

1. En la pestaña **Body** (Cuerpo), asegúrese de que selecciona la vista **Raw** (Sin formato).

1. Copie el cuerpo de la respuesta JSON sin formato. Guarde la respuesta en el directorio de descarga de la pila.

## <a name="download-media"></a>Descargar medios

Para descargar los soportes de instalación de SAP:

1. En el equipo, ejecute SAP Download Manager.

1. Inicie sesión en SAP Download Manager.

1. Acceda a la cesta de descarga de SAP.

1. Establezca el directorio de descarga en el directorio de descarga de la pila que ha creado. Por ejemplo, ``~/Downloads/S4HANA_1909_SP2/`.

1. Descargue todos los archivos de la cesta de descarga en este directorio.

> [!NOTE]
> El archivo de texto que contiene las direcciones URL de descarga de SAP siempre es `myDownloadBasketFiles.txt`. Sin embargo, este archivo es específico de la aplicación o base de datos. Debe mantener este archivo con las otras descargas de esta sección concreta para su uso en secciones posteriores.

## <a name="upload-media"></a>Carga de soportes físicos

Para cargar los archivos multimedia y de la pila de SAP en la cuenta de almacenamiento de Azure:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En **Servicios de Azure,** seleccione **Grupos de recursos**. O bien, escriba `resource groups` en la barra de búsqueda. 

1. Seleccione el grupo de recursos de la biblioteca de SAP.

1. En la página del grupo de recursos, seleccione la cuenta de almacenamiento `saplib` en la tabla **Resources** (Recursos).

1. En el menú de la página de la cuenta de almacenamiento, en **Almacenamiento de datos**, seleccione **Contenedores**.

1. Seleccione el contenedor `sapbits`.

1. En la página del contenedor, cargue los archivos y las herramientas.

    1. Seleccione el botón **Cargar**.

    1. Seleccione **Seleccionar un archivo**.

    1. Vaya al directorio [ donde descargó los elementos multimedia de SAP anteriormente](#download-media).

    1. Seleccione todos los archivos de almacenamiento. Estos nombres de archivo se parecen a `*.SAR`, `*.RAR`, `*.ZIP` y `SAPCAR*.EXE`.

    1. Seleccione **Avanzado** para que se muestren las opciones avanzadas.

    1. En **Upload Directory** (Directorio de carga), escriba `archives`.

1. Cargue los archivos de la pila.

    1. Seleccione el botón **Cargar**.

    1. Seleccione **Seleccionar un archivo**.

    1. Vaya al directorio de descarga que [creó en la sección anterior](#acquire-media).

    1. Seleccione todos los archivos de la pila. Estos nombres de archivo se parecen a `MP_*.(xml|xls|pdf|txt)`.

    1. Seleccione **Avanzado** para que se muestren las opciones avanzadas.

    1. En **Upload Directory** (Directorio de carga), escriba `boms/<Stack_Version>/stackfiles` donde `<Stack_Version>` es una combinación de la información del producto. Por ejemplo, `S4HANA_2020_ISS_v001` indica que el tipo de producto es `S4HANA`, la versión del producto es `2020`, el service pack es `ISS` para el envío del software inicial y la pila es `v001`.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Preparación de BOM](automation-bom-prepare.md)
