---
title: Extender Azure DevTest Labs mediante Azure Functions | Microsoft Docs
description: Obtenga información sobre cómo extender Azure DevTest Labs mediante Azure Functions.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e8593453457bf7a077cf555e8d4f35b0c4b8f599
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300550"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Uso de Azure Functions para ampliar DevTest Labs
Puede usar Azure Functions para admitir escenarios adicionales más allá de los que DevTest Labs ya admite. Azure Functions se puede usar para ampliar la funcionalidad integrada del servicio para satisfacer las necesidades específicas de su empresa. En la lista siguiente se proporcionan algunos de los escenarios posibles. En este artículo se muestra cómo implementar uno de estos escenarios de ejemplo.

- Aprovisionamiento de un resumen de nivel superior de máquinas virtuales (VM) en el laboratorio
- [Configuración de un laboratorio para usar una puerta de enlace de Escritorio remoto](configure-lab-remote-desktop-gateway.md)
- Informes de cumplimiento en la página de soporte interno
- Habilitación de los usuarios para completar las operaciones que requieren mayores permisos en la suscripción
- [Inicio de flujos de trabajo basados en eventos de DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Información general
[Azure Functions](../azure-functions/functions-overview.md) es una plataforma informática sin servidor en Azure. El uso de Azure Functions en una solución con DevTest Labs nos permite aumentar las características existentes con nuestro propio código personalizado. Para más información sobre Azure Functions, consulte la [documentación de Azure functions](../azure-functions/functions-overview.md). Para ilustrar cómo Azure Functions puede ayudar a cumplir sus requisitos o escenarios completos en DevTest Labs, en este artículo se ofrece un ejemplo de cómo proporcionar un resumen de nivel superior de las máquinas virtuales en el laboratorio como se indica a continuación:

**Escenario o requisito de ejemplo**: los usuarios pueden ver los detalles de todas las máquinas virtuales en un laboratorio, incluidos el sistema operativo, el propietario y cualquier artefacto aplicado.  Además, si el artefacto **Aplicar actualizaciones de Windows** no se ha aplicado recientemente, hay una manera sencilla de aplicarlo.

Para completar el escenario, usará dos funciones, como se describe en el diagrama siguiente:  

![Flujo general](./media/extend-devtest-labs-azure-functions/flow.png)

El código fuente de estas funciones de ejemplo se encuentra en el [repositorio de GitHub de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (están disponibles tanto las implementaciones de C# como las de PowerShell ).

- **UpdateInternalSupportPage**: esta función consulta DevTest Labs y actualiza la página de soporte interno directamente con detalles sobre las máquinas virtuales.
- **ApplyWindowsUpdateArtifact**: en el caso de una máquina virtual de un laboratorio, esta función aplica el artefacto de **Windows Update**.

## <a name="how-it-works"></a>Funcionamiento
Cuando los usuarios seleccionan la página **Soporte interno** en DevTest Labs, tienen una página rellenada previamente con información sobre las máquinas virtuales, los propietarios de laboratorio y los contactos de soporte técnico.  

Al elegir el botón **Select here to refresh** (Seleccionar aquí para actualizar), la página llama a la primera función de Azure: **UpdateInternalSupportPage**. La función consulta DevTest Labs para obtener información y luego vuelve a escribir la página **Soporte interno** con la nueva información.

Hay una acción adicional que se puede llevar a cabo. Para todas las máquinas virtuales en las que los artefactos de Windows Update no se hayan aplicado recientemente, habrá un botón para aplicar actualizaciones de Windows a la máquina virtual. Al seleccionar el botón ***Ejecutar Windows Update** para una máquina virtual, la página llama a la segunda función de Azure: **ApplyWindowsUpdateArtifact**. Esta función comprueba si la máquina virtual se está ejecutando y, en caso afirmativo, aplica el artefacto [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) directamente.

## <a name="step-by-step-walkthrough"></a>Tutorial paso a paso
En esta sección se proporcionan instrucciones paso a paso para configurar los recursos de Azure necesarios para actualizar la página **Soporte interno**. En este tutorial se proporciona un ejemplo de extensión de DevTest Labs. Puede usar este patrón para otros escenarios.

### <a name="step-1-create-a-service-principal"></a>Paso 1: Creación de una entidad de servicio 
El primer paso es obtener una entidad de servicio con permisos para la suscripción que contiene el laboratorio. La entidad de servicio debe usar la autenticación basada en contraseña. Se puede hacer con la [CLI de Azure](/cli/azure/create-an-azure-service-principal-azure-cli), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps), o [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md). Si ya tiene una entidad de servicio para usar, puede omitir este paso.

Anote el **identificador de la aplicación**, **clave** y el **identificador de inquilino** de la entidad de servicio. Los necesitará más adelante en este tutorial. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Paso 2: Descarga del ejemplo y apertura en Visual Studio 2019
Descargue una copia del [ejemplo de Azure Functions en C#](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) localmente (ya sea mediante la clonación del repositorio o la descarga del repositorio desde [aquí](https://github.com/Azure/azure-devtestlab/archive/master.zip)).  

1. Abra la solución de ejemplo con Visual Studio 2019.  
1. Instale la carga de trabajo **Desarrollo de Azure** para Visual Studio si aún no lo tiene instalado. Se puede instalar mediante el elemento de menú **Herramientas** -> **Obtener herramientas y características**.

    ![Carga de trabajo Desarrollo de Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Compile la solución. Seleccione **Compilar** y luego el elemento de menú **Compilar solución**.

### <a name="step-3-deploy-the-sample-to-azure"></a>Paso 3: Implementación del ejemplo en Azure
En Visual Studio, en la ventana **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **AzureFunctions** y, a continuación, seleccione **Publicar**. Siga el Asistente para completar la publicación en una aplicación de funciones de Azure nueva o existente. Para información detallada sobre el desarrollo y la implementación de funciones mediante Visual Studio, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md).

![Cuadro de diálogo Publicar](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Paso 4: Recopilación de la configuración de la aplicación
Una vez publicadas las funciones, debe obtener las direcciones URL de estas funciones desde Azure Portal. 

1. Acceda a [Azure Portal](https://portal.azure.com). 
1. Busque la aplicación de funciones.
1. En la página **Instancias de Function App**, seleccione la función. 
1. Seleccione **Obtener la dirección URL de la función** como se muestra en la siguiente imagen. 

    ![Direcciones URL de las funciones de Azure](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Copie y guarde la URL. Repita estos pasos con la otra función de Azure. 

También necesitará información adicional sobre la entidad de servicio, como el identificador de la aplicación, la clave y el identificador del inquilino.


### <a name="step-5--update-application-settings"></a>Paso 5: Actualización de la configuración de la aplicación
En Visual Studio, después de publicar la función de Azure, seleccione **Editar configuración de Azure App Service** en **Acciones**. Actualice la siguiente configuración de la aplicación (remoto):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (el valor predeterminado es 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Configuración de la aplicación](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Paso 6: Prueba de la función de Azure
El último paso de este tutorial es probar la función de Azure.  

1. Vaya a la función **UpdateInternalSupportPage** en la aplicación de función creada en el paso 3. 
1. Seleccione **Probar** en el lado derecho de la página. 
1. Introduzca las propiedades de la ruta (LABNAME, RESOURCEGROUPNAME y SUBSCRIPTIONID).
1. Seleccione **Ejecutar** para ejecutar la función.  

    Esta función actualizará la página de soporte técnico interno del laboratorio especificado. También incluye un botón para que los usuarios llamen directamente a la función la próxima vez.

    ![Probar función](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Pasos siguientes
Azure Functions puede ayudar a ampliar la funcionalidad de DevTest Labs más allá de lo que ya está integrada y ayudar a los clientes a cumplir sus requisitos únicos para sus equipos. Este patrón se puede ampliar y expandir aún más para abarcar mucho más.  Para obtener más sobre DevTest Labs, consulte los siguientes artículos: 

- [Arquitectura de referencia empresarial para DevTest Labs](devtest-lab-reference-architecture.md)
- [Preguntas más frecuentes](devtest-lab-faq.yml)
- [Escalado vertical de DevTest Labs](devtest-lab-guidance-scale.md)
- [Automatización de DevTest Labs con PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








