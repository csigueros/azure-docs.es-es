---
title: Implementación continua para Azure Functions
description: Use las características de implementación continua de Azure App Service para publicar las funciones.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 0018f1c74708035206a8a2bb9a540f00f98f43a4
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321036"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementación continua para Azure Functions

Puede usar Azure Functions para implementar el código de forma continua mediante la [integración del control de código fuente](functions-deployment-technologies.md#source-control). La integración del control de código fuente habilita un flujo de trabajo en el que una actualización de código desencadena la implementación en Azure. Si no está familiarizado con Azure Functions, consulte [Introducción a Azure Functions](functions-overview.md) para comenzar.

La implementación continua es una buena opción para los proyectos donde se integran contribuciones diversas y frecuentes. Al usar la implementación continua, se mantiene un único origen de confianza para el código, lo que permite a los equipos colaborar fácilmente. La implementación continua en Azure Functions se puede configurar desde las siguientes ubicaciones de código fuente:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

La unidad de implementación de las funciones en Azure es la aplicación de funciones. Todas las funciones de una aplicación de funciones se implementan a la vez. Tras habilitar la implementación continua, el acceso al código de función en Azure Portal se configura como de *solo lectura*, ya que el origen de confianza está establecido en otro lugar.

## <a name="requirements-for-continuous-deployment"></a>Requisitos de implementación continua

Para que la implementación continua se realice correctamente, la estructura de directorios debe ser compatible con la estructura básica de carpetas que espera Azure Functions.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Todavía no se admite la implementación continua para las aplicaciones de Linux que se ejecutan en un plan de consumo. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configuración de la implementación continua

Para configurar la implementación continua de una aplicación de funciones existente, siga estos pasos. Estos pasos muestran la integración con un repositorio de GitHub, aunque estos mismos pasos son válidos en Azure Repos o en otros repositorios de código fuente.

1. En la aplicación de funciones de [Azure Portal](https://portal.azure.com), seleccione **Centro de implementación**, **GitHub** y, a continuación, **Autorizar**. Si ya ha autorizado GitHub, seleccione **Continuar** y omita el paso siguiente. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Centro de implementación de Azure App Service":::

3. En GitHub, seleccione **Autorizar AzureAppService**.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Autorizar Azure App Service":::

    Escriba la contraseña de GitHub y, a continuación, seleccione **Continuar**.

4. Seleccione uno de los siguientes proveedores de compilación:

    * **Servicio de compilación de App Service**: opción ideal si no se necesita una compilación o se necesita una compilación genérica.
    * **Azure Pipelines (versión preliminar)** : opción ideal si se necesita más control sobre la compilación. Este proveedor está actualmente en versión preliminar.

    Seleccione **Continuar**.

5. Configure información específica de la opción de control de origen que haya indicado. En GitHub, debe escribir o seleccionar valores de **Organización**, **Repositorio** y **Rama**. Los valores se basan en la ubicación del código. Después, seleccione **Continuar**.

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="Configuración de GitHub":::

6. Revise todos los detalles y seleccione **Finalizar** para completar la configuración de la implementación.

Cuando finalice el proceso, todo el código del origen especificado se implementará en la aplicación. Hecho esto, los cambios en el origen de implementación desencadenarán una implementación de dichos cambios en la aplicación de funciones en Azure.

> [!NOTE]
> Después de configurar la integración continua, ya no podrá modificar los archivos de origen en el portal de Functions. Si originalmente ha publicado el código desde el equipo local, es posible que tenga que cambiar la configuración `WEBSITE_RUN_FROM_PACKAGE` de la aplicación de funciones a un valor de `0`. 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
