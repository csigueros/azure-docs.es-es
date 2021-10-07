---
title: 'Módulo de función ABAP de extracción de metadatos en SAP R3: Azure Purview'
description: En este artículo se describen los pasos para implementar el módulo de función de ABAP en el servidor SAP.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: f235e4b293c47c9d2833732fa6333a350a1fd272
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210352"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Implementación del módulo de función de ABAP de extracción de metadatos para la familia de puentes de SAP R3

En este artículo, se describen los pasos para implementar el módulo de función de ABAP en el servidor SAP.

## <a name="overview"></a>Información general

El puente SAP Business Suite 4 HANA (S/4HANA), ECC y R/3 ERP se puede usar para extraer metadatos del servidor SAP. Para ello se coloca el módulo de función de ABAP en el servidor SAP. El puente puede acceder a este módulo de función de forma remota para consultar y descargar (como un archivo de texto) los metadatos que contiene el servidor SAP.

Cuando se ejecuta, el puente hace lo siguiente:

1. Importa los metadatos desde un archivo existente ya descargado localmente de una ejecución de puente anterior.

2. Invoca la API del módulo ABAP, espera la descarga y, luego, importa los metadatos desde ese archivo.

En este documento se detallan los pasos necesarios para implementar este módulo.

> [!Note]
> Las siguientes instrucciones se compilaron en función de la GUI de SAP v.7.2

## <a name="deployment-of-the-module"></a>Implementación del módulo

### <a name="create-a-package"></a>Crear un paquete

Este paso es opcional y se puede usar un paquete existente.

1. Inicie sesión en el servidor SAP S/4HANA o SAP ECC y abra el **navegador de objetos** (transacción SE80).

2. Seleccione la opción **Package** (Paquete) de la lista y escriba el nombre del nuevo paquete (por ejemplo, Z\_MITI). Luego, presione el botón **Display** (Mostrar).

3. Seleccione **Yes** (Sí) en la ventana **Create Package** (Crear paquete). Como resultado, se abre una ventana **Package Builder: Create Package** (Compilador de paquetes: Crear paquete). Escriba un valor en el campo **Short Description** (Descripción corta) y seleccione el icono **Continue** (Continuar).

4. Seleccione **Own Requests** (Solicitudes propias) en la ventana **Prompt for local Workbench request** (Pedir solicitud de Workbench local). Seleccione la solicitud **development** (desarrollo).

### <a name="create-a-function-group"></a>Creación de un grupo de función

En el navegador de objetos, seleccione **Function Group** (Grupo de función) en la lista y escriba su nombre en el campo de entrada siguiente (por ejemplo, Z \_MITI\_FGROUP). Seleccione el icono **View** (Ver).

1. En la ventana **Create Object** (Crear objeto), seleccione **Yes** (Sí) para crear un grupo de funciones.

2. Especifique una descripción adecuada en el campo **Short text** (Texto breve) y presione el botón **Save**(Guardar).

3. Elija el paquete que se preparó en el paso anterior **Create a Package** (Crear un paquete) y seleccione **Save** (Guardar).

4. Confirme la solicitud presionando el icono **Continue** (Continuar).

5. Active este grupo de funciones.

### <a name="create-the-abap-function-module"></a>Creación del módulo de función de ABAP

1. Una vez creado el grupo de funciones, selecciónelo.

2. Seleccione y mantenga presionado (o haga clic con el botón derecho) el nombre del grupo de funciones en el explorador del repositorio y seleccione **Crear** y **Módulo de función**.

3. En el campo **Function Module** (Módulo de función), escriba `Z_MITI_DOWNLOAD`. Rellene la entrada de **Short text** (Texto corto) con la descripción adecuada.

Cuando se haya creado el módulo, especifique la información siguiente:

1. Vaya a la pestaña **Attributes** (Atributos).

2. Seleccione **Processing Type** => **Remote-Enabled Function Module** (Tipo de procesamiento => Módulo de función habilitado para remoto).

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Opción de registro de orígenes: Módulo de función habilitado para remoto" border="true":::

3. Vaya a la pestaña **Source code** (Código fuente). Hay dos maneras de implementar código para la función:

   a. En el menú principal, cargue el archivo de texto [Z\_MITI\_DOWNLOAD](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap). Para ello, seleccione **Utilities**, **More Utilities**, **Upload/Download** y **Upload** (Utilidades > Más utilidades > Cargar/Descargar > Cargar).

   b. También puede abrir el archivo, copiar su contenido y pegarlo en el área **Source code** (Código fuente).

4. Vaya a la pestaña **Import** (Importar) y cree los siguientes parámetros:

   a.  P\_AREA TYPE DD02L-TABNAME (opcional = true)

   b.  *P\_LOCAL\_PATH TYPE STRING* (opcional = true)

   c.  *P\_LANGUAGE TYPE L001TAB-DATA DEFAULT \'E\'*

   d.  *ROWSKIPS TYPE SO\_INT DEFAULT 0*

   e.  *ROWCOUNT TYPE SO\_INT DEFAULT 0*

   > [!Note]
   > Elija el **valor de paso** de todos ellos.

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Opción de registro de orígenes: Importar parámetros" border="true":::

5. Vaya a la pestaña "Tables" (Tablas) y defina lo siguiente:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Opciones de registro de orígenes: pestaña Tablas" border="true":::

6. Vaya a la pestaña **Exceptions** (Excepciones) y defina la siguiente excepción: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Opciones de registro de orígenes: pestaña Excepciones" border="true":::

7. Guarde la función (presione Ctrl+S o elija **Function Module** > **Save** [Módulo de función > Guardar] en el menú principal).

8. Seleccione el icono **Activar** en la barra de herramientas (Ctrl+F3) y seleccione el botón **Continuar** en la ventana del cuadro de diálogo. Si se le solicita, debe seleccionar las inclusiones generadas que se activarán junto con el módulo de función principal.

### <a name="testing-the-function"></a>Prueba de la función

Una vez completados todos los pasos anteriores, siga los que se indican a continuación para probar la función:

1. Abra el módulo de función Z\_MITI\_DOWNLOAD.

2. Elija **Function Module** > **Test** > **Test Function Module** (Módulo de función > Probar > Probar módulo de función) en el menú principal, o presione F8.

3. Escriba una ruta de acceso a la carpeta del sistema de archivos local en el parámetro P P\_LOCAL\_PATH y presione el icono **Execute** (Ejecutar) en la barra de herramientas (o presione F8).

4. Coloque el nombre del área de interés en el campo P\_AREA si se debe descargar o actualizar un archivo con metadatos. Cuando la función termine, la carpeta que se indica en el parámetro P\_LOCAL\_PATH debe contener varios archivos con metadatos. Los nombres de los archivos imitan las áreas que se pueden especificar en el campo P\_AREA.

La función terminará de ejecutarse y los metadatos se descargarán mucho más rápido en el caso de que se inicie en la máquina con la conexión de red de alta velocidad con el servidor SAP S/4HANA o ECC.

## <a name="next-steps"></a>Pasos siguientes

- [Registro y examen de un origen de SAP ECC](register-scan-sapecc-source.md)
- [Registro y examen de un origen de SAP S/4HANA](register-scan-saps4hana-source.md)
