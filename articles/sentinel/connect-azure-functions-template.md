---
title: Uso de Azure Functions para conectar Azure Sentinel a un origen de datos | Microsoft Docs
description: Vea cómo configurar conectores de datos que usan Azure Functions para obtener datos de orígenes de datos en Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/07/2021
ms.author: yelevin
ms.openlocfilehash: f776c39a5a1dadde2e6ee01fe211e0769e5e06eb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260968"
---
# <a name="use-azure-functions-to-connect-azure-sentinel-to-your-data-source"></a>Uso de Azure Functions para conectar Azure Sentinel a un origen de datos

Utilice [Azure Functions](/azure/azure-functions/functions-overview), junto con varios lenguajes de programación, como [PowerShell](../azure-functions/functions-reference-powershell.md) o Python, para crear un conector sin servidor para los puntos de conexión de la API REST de los orígenes de datos compatibles. Las aplicaciones de funciones de Azure Functions le permiten conectar Azure Sentinel a la API REST de su origen de datos para recopilar los registros.

En este artículo, se explica cómo configurar Azure Sentinel para usar aplicaciones de funciones de Azure Functions. Es posible que deba configurar también el sistema de origen. Encontrará vínculos a información específica del proveedor y del producto en la página de cada conector de datos del portal, o bien en la sección de su servicio de la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).




> [!NOTE]
> - Una vez ingeridos en Azure Sentinel, los datos se almacenan en la ubicación geográfica del área de trabajo donde ejecuta Azure Sentinel.
>
>     Para la retención a largo plazo, es posible que también desee almacenar datos en Azure Data Explorer. Para obtener más información, consulte [Integración con Azure Data Explorer para conservar registros a largo plazo](store-logs-in-azure-data-explorer.md).
>
> - El uso de Azure Functions para ingerir datos en Azure Sentinel puede dar lugar a costos adicionales por la ingesta de datos. Para obtener más información, consulte la página de [precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de que tiene los siguientes permisos y credenciales antes de usar Azure Functions para conectar Azure Sentinel al origen de datos y recopilar los registros en Azure Sentinel:

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Debe tener permisos de lectura y escritura en Azure Functions para crear una aplicación de funciones. [Obtenga más información sobre Azure Functions](../azure-functions/index.yml).

- También necesitará credenciales para acceder a la API del producto: un nombre de usuario y una contraseña, un token, una clave o alguna otra combinación. Puede que necesite también otra información de la API, como el URI de un punto de conexión.

    Para obtener más información, consulte la documentación del servicio al que se conecta y la sección de su servicio en la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).

## <a name="configure-and-connect-your-data-source"></a>Configuración y conexión del origen de datos

> [!NOTE]
> - Puede almacenar de forma segura los tokens o las claves de autorización del área de trabajo y de la API en Azure Key Vault. Azure Key Vault proporciona un mecanismo seguro para almacenar y recuperar valores de clave. [Siga estas instrucciones](../app-service/app-service-key-vault-references.md) para usar Azure Key Vault con una aplicación de funciones de Azure Functions.
>
> - Algunos conectores de datos dependen de un analizador basado en una [función de Kusto](/azure/data-explorer/kusto/query/functions/user-defined-functions) para funcionar según lo previsto. Consulte la sección de su servicio en la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md), donde encontrará vínculos a las instrucciones para crear la función y el alias de Kusto.


### <a name="step-1---get-your-source-systems-api-credentials"></a>PASO 1: Obtención de las credenciales de la API del sistema de origen

Siga las instrucciones del sistema de origen para obtener sus **credenciales de API, claves de autorización o tokens**. Cópielos y péguelos en un archivo de texto para usarlos más adelante.

Encontrará información detallada sobre las credenciales exactas que necesitará y vínculos a las instrucciones de su producto para buscarlas o crearlas en la página del conector de datos del portal y en la sección de su servicio en la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).

Es posible que también tenga que configurar el registro u otras opciones en el sistema de origen. Encontrará las instrucciones pertinentes junto con las del párrafo anterior.
### <a name="step-2---deploy-the-connector-and-the-associated-azure-function-app"></a>PASO 2: Implementación del conector y la aplicación de funciones de Azure Functions asociada

#### <a name="choose-a-deployment-option"></a>Elección de una opción de implementación

# <a name="azure-resource-manager-arm-template"></a>[Plantilla de Azure Resource Manager (ARM)](#tab/ARM)

Este método proporciona una implementación automatizada del conector basado en Azure Functions mediante una plantilla de ARM.

1. En el portal de Azure Sentinel, seleccione **Data connectors** (Conectores de datos). Seleccione el conector basado en Azure Functions en la lista y elija **Open connector page** (Abrir página del conector).

1. En **Configuration** (Configuración), copie el **identificador del área de trabajo** y la **clave principal** de Azure Sentinel y péguelos aparte.

1. Seleccione **Implementar en Azure** (es posible que deba desplazarse hacia abajo para encontrar el botón).

1. Aparecerá la pantalla **Implementación personalizada**.
    - Seleccione una **suscripción**, un **grupo de recursos** y una **región** donde implementar la aplicación de funciones.

    - Escriba las credenciales de la API, las claves de autorización o los tokens que guardó en el [paso 1](#step-1---get-your-source-systems-api-credentials) anterior.

    - Especifique el **id. del área de trabajo** y la **clave del área de trabajo**  (clave principal) de Azure Sentinel que copió y pegó anteriormente.

        > [!NOTE]
        > Si utiliza secretos de Azure Key Vault para cualquiera de los valores anteriores, use el esquema `@Microsoft.KeyVault(SecretUri={Security Identifier})` en lugar de los valores de cadena. Consulte la documentación de Key Vault para obtener información más detallada.

    - Rellene los demás campos del formulario en la pantalla **Implementación personalizada**. Consulte la página del conector de datos en el portal o la sección del servicio en la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).

    - Seleccione **Revisar + crear**. Una vez completada la validación, seleccione **Crear**.

# <a name="manual-deployment-with-powershell"></a>[Implementación manual con PowerShell](#tab/MPS)

Utilice las siguientes instrucciones paso a paso para implementar manualmente conectores basados en Azure Functions que usen funciones de PowerShell.

1. En el portal de Azure Sentinel, seleccione **Data connectors** (Conectores de datos). Seleccione el conector basado en Azure Functions en la lista y elija **Open connector page** (Abrir página del conector).

1. En **Configuration** (Configuración), copie el **identificador del área de trabajo** y la **clave principal** de Azure Sentinel y péguelos aparte.

1. **Creación de una aplicación de funciones**
    1. En Azure Portal, busque y seleccione **Aplicación de funciones**.

    1. En la página **Aplicación de funciones**, seleccione **Crear**. Se abre el asistente **Crear aplicación de funciones**.

    1. En la pestaña **Básica**:
        - Seleccione una **suscripción** y un **grupo de recursos**.
        - Dele un nombre a la aplicación de funciones.
        - Establezca **Pila del entorno en tiempo de ejecución** en *PowerShell Core*.
        - Seleccione el número de versión correspondiente.
        - Seleccione la **región** donde desea realizar la implementación y elija **Siguiente: Hospedaje**.

    1. En la pestaña **Hospedaje**:
        - Elija la **cuenta de almacenamiento** que quiere usar o cree una nueva.
        - Seleccione *Consumo (sin servidor)* como **Tipo de plan**.

    1. Realice cualquier otro cambio de configuración que necesite y, a continuación, seleccione **Revisar y crear**.

    1. Espere a que aparezca el mensaje "Validación superada" y, después, seleccione **Crear**.

    1. Cuando finalice la implementación, seleccione **Ir al recurso**.

1. **Importe el código de la aplicación de funciones**
    1. En la aplicación de funciones recién creada, seleccione **Funciones** en el menú de navegación.

    1. En la página **Funciones**, seleccione **+ Agregar**.
    
    1. En el panel **Agregar función**, seleccione el **desencadenador Temporizador**.

    1. Escriba un nombre único para la función y una expresión *cron* para especificar la programación. El intervalo predeterminado es de 5 minutos.

    1. Cuando se haya creado la función, seleccione **Código y prueba** en el panel izquierdo.

    1. Descargue el código de la aplicación de funciones proporcionado por el proveedor del sistema de origen y cópielo y péguelo en el editor de *run.ps1* de **Aplicación de funciones**, reemplazando el valor predeterminado. Puede encontrar el vínculo de descarga en la página del conector o en la sección del servicio de la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).

    1. Seleccione **Guardar**.

1. **Configure la aplicación de funciones**
    1. En la página de la aplicación de funciones, seleccione **Configuración** en la sección **Configuración** del menú de navegación.

    1. En la pestaña **Configuración de la aplicación**, seleccione **+ Nueva configuración de la aplicación**.

    1. Agregue la configuración de aplicación prescrita para su producto individualmente, con sus respectivos valores de cadena, que distinguen mayúsculas de minúsculas. Consulte la página del conector de datos o la sección de su producto en la sección del servicio de la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).

        > [!TIP]
        > Si procede, use la opción *logAnalyticsUri* de la aplicación para invalidar el punto de conexión de la API de Log Analytics si está usando una nube dedicada. Por ejemplo, si usa la nube pública, deje el valor vacío; para el entorno de nube de Azure GovUS, especifique el valor con el siguiente formato: `https://<CustomerId>.ods.opinsights.azure.us`.
        >

# <a name="manual-deployment-with-python"></a>[Implementación manual con Python](#tab/MPY)

Utilice las siguientes instrucciones paso a paso para implementar manualmente conectores basados en Azure Functions que usen funciones de Python. Este tipo de implementación requiere Visual Studio Code.

1. En el portal de Azure Sentinel, seleccione **Data connectors** (Conectores de datos). Seleccione el conector basado en Azure Functions en la lista y elija **Open connector page** (Abrir página del conector).

1. En **Configuration** (Configuración), copie el **identificador del área de trabajo** y la **clave principal** de Azure Sentinel y péguelos aparte.

1. **Implemente una aplicación de funciones**

    > [!NOTE]
    > Tendrá que preparar [Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md) (VS Code) para desarrollar funciones de Azure Functions.

    1. Descargue el archivo de la aplicación de funciones de Azure Functions usando el vínculo proporcionado en la página del conector de datos y en la sección del servicio en la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md). Extraiga el archivo en su equipo de desarrollo local.

    1. Inicie VS Code. En la barra de menús, seleccione **Archivo > Abrir carpeta**.

    1. Seleccione la carpeta de nivel superior de los archivos extraídos del archivo.

    1. Elija el icono de Azure en la barra de actividad de VS Code (a la izquierda). A continuación, en el área **Azure: Functions**, elija el botón **Deploy to function app** (Implementar en aplicación de funciones). 

        > [!NOTE]
        > Si aún no ha iniciado sesión, elija el icono de Azure en la barra de actividades. En el área **Azure: Functions**, elija **Iniciar sesión en Azure**.  
        > Si ya había iniciado sesión, vaya a la siguiente sección.

    1. Escriba la siguiente información cuando se le indique:
        - **Seleccionar carpeta**: elija una carpeta del área de trabajo o busque una que contenga la aplicación de funciones.
        - **Seleccione la suscripción**: elija la suscripción que desee usar.
        - Seleccione **Create new Function App in Azure** (Crear nueva aplicación de funciones en Azure). No elija la opción **Avanzadas**.
        - **Escriba un nombre único global para la aplicación de funciones**: dele un nombre a la aplicación de funciones que sea válido en una ruta de acceso URL. El nombre que elija se validará para confirmar que es único en Azure Functions.
        - **Seleccione un entorno de ejecución**: elija *Python 3.8.*

    1. Se inicia la implementación. Una vez que se haya creado la aplicación de función se mostrará una notificación y se aplicará el paquete de implementación.

    1. Vuelva a la página de la aplicación de funciones para configurarla.

1. **Configure la aplicación de funciones**
    1. En la página de la aplicación de funciones, seleccione **Configuración** en la sección **Configuración** del menú de navegación.

    1. En la pestaña **Configuración de la aplicación**, seleccione **+ Nueva configuración de la aplicación**.

    1. Agregue la configuración de aplicación prescrita para su producto individualmente, con sus respectivos valores de cadena, que distinguen mayúsculas de minúsculas. Consulte la página del conector de datos o la sección de su servicio en la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md) para ver los valores de configuración de la aplicación que debe agregar.

        - Si procede, use la opción *logAnalyticsUri* de la aplicación para invalidar el punto de conexión de la API de Log Analytics si está usando una nube dedicada. Por ejemplo, si usa la nube pública, deje el valor vacío; para el entorno de nube de Azure GovUS, especifique el valor con el siguiente formato: `https://<CustomerId>.ods.opinsights.azure.us`.

---

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, en *CustomLogs*, en las tablas enumeradas en la sección para el servicio de la página [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).

Para consultar los datos, escriba uno de esos nombres de tabla (o el alias de función de Kusto pertinente) en la ventana de consulta.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunas consultas de ejemplo útiles.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics. 

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Azure Sentinel a su origen de datos mediante conectores basados en Azure Functions. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](quickstart-get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use libros](tutorial-monitor-your-data.md) para supervisar los datos.