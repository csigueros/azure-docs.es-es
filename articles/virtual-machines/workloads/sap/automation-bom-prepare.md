---
title: Preparación de una lista de materiales para la automatización
description: Preparación de una lista de materiales (L. MAT) de SAP completa para su uso con el marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 772b7c5cb972523cb701778951b45608241b0ee7
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725318"
---
# <a name="prepare-sap-bom"></a>Preparación de L. MAT de SAP

El [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md) usa una lista de materiales (L. MAT). La lista de materiales ayuda a configurar los sistemas SAP. 

El repositorio de GitHub del marco de automatización contiene un conjunto de [listas de materiales de ejemplo](https://github.com/Azure/sap-hana/tree/main/deploy/ansible/BOM-catalog) que puede usar para empezar. También se pueden crear listas de materiales para otras bases de datos y aplicaciones de SAP. 

Si desea generar una lista de materiales que incluya vínculos permanentes, [siga los pasos para crear este tipo de L. MAT](#permalinks).

> [!NOTE]
> En esta guía se tratan temas de implementación avanzada. Para obtener una explicación básica de cómo implementar el marco de automatización, consulte la [guía de introducción](automation-get-started.md).

## <a name="prerequisites"></a>Requisitos previos

- [Obtenga, descargue y prepare el soporte de instalación de SAP y los archivos relacionados](automation-bom-get-files.md), en caso de que aún no lo haya hecho.
    - Aplicación SAP (DB) o soporte físico HANA en una cuenta de Azure Storage.
- Un editor YAML para trabajar con el archivo L. MAT.
- Plantillas de instalación de aplicaciones para: 
    - Servicios centrales de SAP (SCS)
    - El servidor de aplicaciones principal de SAP (PAS)
    - El servidor de aplicaciones adicional de SAP (AAS)
- Descargue los archivos de pila necesarios en la carpeta que creó para [adquirir el soporte físico de SAP](automation-bom-get-files.md#acquire-media). Para más información, consulte la [guía paso a paso básica para la preparación de L. MAT](automation-bom-prepare.md).
- Una copia del [manifiesto de la cesta de descarga de SAP](automation-bom-get-files.md#get-download-basket-manifest) (`DownloadBasket.json`), descargada en la carpeta [ que creó para adquirir el soporte físico de SAP](automation-bom-get-files.md#acquire-media).
    - Una instalación de la [utilidad Postman](https://www.postman.com/downloads/).
- Una suscripción a Azure. Si aún no tiene una suscripción a Azure, [cree una cuenta gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una cuenta de SAP con permisos para trabajar con la base de datos que desea usar.
- Un sistema que ejecute comandos de tipo Linux para [validar la lista de materiales](#validate-bom). Instale los comandos `yamllint` y `ansible-lint` en el sistema.

## <a name="scripted-creation-process"></a>Proceso de creación mediante scripts

Este proceso automatiza los mismos pasos que el [proceso manual de creación de una lista de materiales](#manual-creation-process). Antes de usar este proceso consulte las [limitaciones del script](#script-limitations).

1. Vaya a la carpeta de archivos de la pila.

    ```bash
    cd stackfiles
    ```

1. Ejecute el script de generación de listas de materiales. Reemplace la ruta de acceso de ejemplo por la ruta de acceso correcta a la carpeta utilities. Por ejemplo:

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/deploy/scripts/generate_bom.sh >../bom.yml
    ```

1. Para el parámetro del producto (`product`), escriba el nombre del producto de SAP. Por ejemplo, `SAP_S4HANA_1809_SP4`. Si no escribe ningún valor, el script intenta determinar el nombre del archivo XML de la pila.

1. Abra el archivo `bom.yml` generado para examinarlo.

1. Consulte de la sección de plantillas (`templates`). Asegúrese de que los valores `file` y `override_target_location` sean correctos. Si fuera necesario, edite y agregue comentarios en esas líneas. Por ejemplo:

    ```yml
    templates:
      # - name:     "S4HANA_2020_ISS_v001 ini file"
      #   file:     S4HANA_2020_ISS_v001.inifile.params
      #   override_target_location: "{{ target_media_location }}/config"
    ```

1. Consulte la sección de archivos de la pila (`stackfiles`). Asegúrese de que los nombres de elemento y los archivos son correctos. Si fuera necesario, edite esas líneas.

## <a name="script-limitations"></a>Limitaciones del script

El [proceso de creación con una lista de materiales con scripts](#scripted-creation-process) tiene las siguientes limitaciones.

La generación de scripts tiene una dependencia codificada de forma rígida en HANA2. Edite el archivo de lista de materiales manualmente para que coincida con el nombre de dependencia requerido. Por ejemplo:

```yml
dependencies:
  - name: "HANA2"
```

No hay valores predeterminados para los parámetros del soporte físico `override_target_filename:`, `override_target_location` y `version:`. Edite manualmente el archivo de lista de materiales para cambiar estos parámetros. Por ejemplo:

```yml
   - name:     SAPCAR
     archive:  SAPCAR_1320-80000935.EXE
     override_target_filename: SAPCAR.EXE

   - name: "SWPM20SP07"
     archive: "SWPM20SP07_2-80003424.SAR"
     override_target_filename: SWPM.SAR
     sapurl: "https://softwaredownloads.sap.com/file/0020000001812632020"
```

El script solo genera entradas para los archivos del soporte físico que identifica SAP Maintenance Planner. Esta limitación se produce porque procesa el archivo `.xsl` de la pila. Si agrega archivos a la cesta de descarga por separado, como por ejemplo, a través de SAP Launchpad, debe [agregarlos a la lista de materiales manualmente](#manual-creation-process).

## <a name="manual-creation-process"></a>Proceso manual de creación

Para crear una lista de materiales se puede usar el siguiente proceso manual. Otra opción es usar el [proceso de creación con scripts](#scripted-creation-process) para realizar los mismos pasos.

1. Abra la carpeta de descargas que creó para [adquirir el soporte físico de SAP](automation-bom-get-files.md#acquire-media)

1. Cree un archivo YAML vacío denominado `bom.yml`.

1. Abra `bom.yml` en un editor.

1. Agregue un encabezado de L. MAT con nombres para la compilación y el destino. El valor `name` debe ser el mismo que el nombre de la carpeta L. MAT de la cuenta de almacenamiento. Por ejemplo:

    ```yml
    name:    'S4HANA_2020_ISS_v001'
    target:  'ABAP PLATFORM 2020'
    ```

1. Agregue una sección de valores predeterminados con la ubicación de destino. Use la ruta de acceso a la carpeta en el servidor de destino donde desea copiar los archivos de instalación. Normalmente, use `{{ target_media_location }}` como se indica a continuación:

    ```yml
    defaults:
      target_location: "{{ target_media_location }}/download_basket"
    ```

1. Agregue una sección de identificadores de producto. Estos valores se rellenan más adelante como parte de la preparación de la plantilla. Por ejemplo:

    ```yml
    product_ids:
      scs:
      db:
      pas:
      aas:
      web:
    ```

1. Agregue una sección de materiales para especificar la lista de materiales necesarios. Agregue dependencias a otras listas de materiales de esta sección. Por ejemplo:

    ```yml
    materials:
    dependencies:
        - name:     HANA2
    ```

1. Obtenga una lista de los soportes físicos que se incluirán en la lista de materiales.

    1. Abra la hoja de cálculo de la cesta de descarga. Este archivo se representa como XML.

    1. Dé formato al contenido XML para que sea legible, en caso de que sea necesario.

    1. En todos los elementos de la cesta de descarga, anote los datos `String` y `Number`. Los datos `String` proporcionan el nombre de archivo (por ejemplo, `igshelper_17-10010245.sar`) y una descripción sencilla (por ejemplo, `SAP IGS Fonts and Textures`). Registrará los datos `Number` después de cada entrada en la lista de materiales. 

1. Agregue la lista de soportes físicos a `bom.yml`. El orden de estos elementos no importa, pero es posible que desee agrupar los elementos relacionados para facilitar su legibilidad. Agregue `SAPCAR` por separado, aunque la cesta de descarga de SAP contenga esta utilidad. Por ejemplo:

    ```yml
    media:
        - name:     SAPCAR
          archive:  SAPCAR_1320-80000935.EXE
    
        name: "SAP IGS Fonts and Textures"
          archive: "igshelper_17-10010245.sar"
          # 61489
    
        <...>
    ```

1. Opcionalmente, si necesita reemplazar la ubicación del soporte físico de destino, agregue el parámetro `override_target_location` a un elemento de soporte físico. Por ejemplo, `override_target_location: "{{ target_media_location }}/config"`.

1. Agregue una sección de plantillas en blanco.

    ```yml
    templates:
    ```

1. Cree una sección de archivos de la pila. Por ejemplo:

    ```yml
    stackfiles:
      - name: Download Basket JSON Manifest
         file: downloadbasket.json
    
      - name: Download Basket Spreadsheet
         file: MP_Excel_2001017452_20201030_SWC.xls
    ```

1. Guarde los cambios en `bom.yml`.

### <a name="permalinks"></a>Vínculos permanentes

Puede generar automáticamente una lista de materiales básica que funcione. Sin embargo, la lista de materiales no crea direcciones URL permanentes (vínculos permanentes) al soporte físico de SAP de forma predeterminada. Si desea crear vínculos permanentes, debe realizar más pasos antes de [adquirir el soporte físico de SAP](automation-bom-get-files.md#acquire-media). 

> [!NOTE]
> La generación manual de una lista de materiales de SAP completa con vínculos permanentes tarda aproximadamente el doble que la [preparación manual de una lista de materiales básica](#manual-creation-process). 

Para generar una lista de materiales con vínculos permanentes:

1. Abra `DownloadBasket.json` en un editor.

1. En cada resultado, anote el contenido de la línea `Value`. Por ejemplo:

    ```json
         "Value": "0020000000703122018|SP_B|SAP IGS Fonts and Textures|61489|1|20201023150931|0"
    ```

1. Copie los valores primero y cuarto separados por barras verticales.

    1. El primer valor es el número de archivo. Por ejemplo, `0020000000703122018`.

    1. El cuarto valor es el número que usará para hacer que coincida con la lista de soportes físicos. Por ejemplo, `61489`.

    1. Opcionalmente, copie el segundo valor, que indica el tipo de archivo. Por ejemplo, `SP_B` en el caso de archivos binarios de kernel, `SPAT` en el caso de archivos binarios que no son de kernel y `CD` para exportaciones de base de datos.

1. Use el cuarto valor como clave para hacer coincidir la cesta de descarga con la lista de soportes físicos. Haga coincidir los valores (por ejemplo, `61489`) con los valores que agregó como comentarios a los elementos de soporte físico (por ejemplo, `# 61489`).

1. Para cada entrada que coincida en `bom.yml`, agregue un nuevo valor para la dirección URL de SAP. Para la dirección URL, use `https://softwaredownloads.sap.com/file/` más el tercer valor para ese elemento (por ejemplo, `0020000000703122018`). Por ejemplo:

    ```yml
    - name: "SAP IGS Fonts and Textures"
      archive: "igshelper_17-10010245.sar"
      sapurl: "https://softwaredownloads.sap.com/file/0020000000703122018"
    ```

## <a name="example-bom-file"></a>Archivo de lista de materiales de ejemplo

El ejemplo siguiente es una pequeña parte de un archivo de lista de materiales de ejemplo para S/4HANA 1909 SP2. 

Puede encontrar varios archivos de lista de materiales completos y utilizables en el [repositorio de GitHub](https://github.com/Azure/sap-hana).

```yml
step|BOM Content

---

name:    'S4HANA_2020_ISS_v001'
target:  'ABAP PLATFORM 2020'

defaults:
  target_location: "{{ target_media_location }}/download_basket"

product_ids:
  scs:
  db:
  pas:
  aas:
  web:

materials:
dependencies:
    - name:     HANA2

media:
    - name:     SAPCAR
      archive:  SAPCAR_1320-80000935.EXE

    - name:     SWPM
      archive:  SWPM20SP06_6-80003424.SAR

    - name:     SAP IGS HELPER
      archive:  igshelper_17-10010245.sar

    - name:     SAP HR 6.08
      archive:  SAP_HR608.SAR

    - name:     S4COREOP 104
      archive:  S4COREOP104.SAR

templates:
    - name:     "S4HANA_2020_ISS_v001 ini file"
      file:     S4HANA_2020_ISS_v001.inifile.params
      override_target_location: "{{ target_media_location }}/config"

stackfiles:
    - name: Download Basket JSON Manifest
      file: downloadbasket.json
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Spreadsheet
      file: MP_Excel_2001017452_20201030_SWC.xls
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Plan doc
      file: MP_Plan_2001017452_20201030_.pdf
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Stack text
      file: MP_Stack_2001017452_20201030_.txt
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Stack XML
      file: MP_Stack_2001017452_20201030_.xml
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket permalinks
      file: myDownloadBasketFiles.txt
      override_target_location: "{{ target_media_location }}/config"
```

## <a name="validate-bom"></a>Validación de L. MAT

La estructura de la lista de materiales se puede validar desde cualquier sistema operativo que ejecute comandos de tipo Linux. En el caso de Windows, utilice el subsistema de Windows para Linux (WSL). Otra opción es ejecutar la validación desde el implementador, en caso de que haya una copia del archivo de lista de materiales en él.


1. Ejecute el script de validación `check_bom.sh` desde el directorio que contiene la lista de materiales. Por ejemplo:

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/deploy/scripts/check_bom.sh bom.yml
    ```

1. Revise el resultado. 

### <a name="successful-validation"></a>Validación correcta

Una validación correcta muestra la siguiente salida. Ya ha instalado los comandos `yamllint` y `ansible-lint` en los [requisitos previos](#prerequisites). 

```output
... yamllint [ok]
... ansible-lint [ok]
... bom structure [ok]
```

### <a name="unsuccessful-validation"></a>Validación incorrecta

Una validación incorrecta contiene información del error. Por ejemplo:

```output
../documentation/ansible/system-design-deployment/examples/S4HANA_2020_ISS_v001/bom_with_errors.yml
  178:16    error    too many spaces after colon  (colons)
  179:16    error    too many spaces after colon  (colons)
  180:16    error    too many spaces after colon  (colons)
    
... yamllint [errors]
... ansible-lint [ok]
  - Expected to find key 'defaults' in 'bom' (Check name: S4HANA_2020_ISS_v001)
  - Unexpected key 'default in 'bom' (Check name: S4HANA_2020_ISS_v001)
  - Unexpected key 'overide_target_location in 'bom.materials.stackfiles' (Check name: Download Basket Stack text)
... bom structure [errors]
```

## <a name="upload-your-bom"></a>Carga de la lista de materiales

Para usar la lista de materiales con vínculos permanentes:

1. [Valide la lista de materiales](#validate-bom).

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En **Servicios de Azure,** seleccione **Grupos de recursos**. O bien, escriba `resource groups` en la barra de búsqueda. 

1. Seleccione el grupo de recursos de la biblioteca de SAP.

1. En la página del grupo de recursos, seleccione la cuenta de almacenamiento `saplib` en la tabla **Resources** (Recursos).

1. En el menú de la página de la cuenta de almacenamiento, en **Almacenamiento de datos**, seleccione **Contenedores**.

1. Seleccione el contenedor `sap bits`.

1. En la página del contenedor, cargue los archivos y las herramientas.

    1. Seleccione el botón **Cargar**.

    1. Seleccione **Seleccionar un archivo**.

    1. Vaya al directorio de descarga que creó anteriormente.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Procedimiento para generar la lista de materiales de una aplicación SAP](automation-bom-templates-db.md)
