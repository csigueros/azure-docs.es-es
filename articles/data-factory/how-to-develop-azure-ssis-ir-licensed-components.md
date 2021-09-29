---
title: Instalación de componentes con licencia para Azure SSIS Integration Runtime
description: Más información sobre cómo un ISV puede desarrollar e instalar componentes personalizados de pago o con licencia en un entorno de ejecución para la integración de SSIS en Azure
ms.service: data-factory
ms.subservice: integration-services
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: 71991798306296c7df16ee7d39678a25f263629f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831233"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalación de componentes personalizados de pago o con licencia para la instancia de Integration Runtime para la integración de SSIS en Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se describe cómo un ISV desarrolla e instala componentes personalizados de pago o con licencia en un entorno de ejecución para la integración de SSIS en Azure.

## <a name="the-problem"></a>El problema.

La naturaleza de la instancia de Integration Runtime para la integración de SSIS en Azure presenta varios retos, que hacen que los métodos de licencia típicos que se usan para la instalación local de componentes personalizados sea insuficiente. Por consiguiente, la instancia de Integration Runtime para la integración de SSIS en Azure requiere un enfoque diferente.

-   Los nodos de la instancia de Integration Runtime para la integración de SSIS en Azure son volátiles y se pueden asignar o liberar en cualquier momento. Por ejemplo, puede iniciar o detener nodos para administrar los costos o escalar vertical y horizontalmente a través de varios tamaños de nodo. Como resultado, ya no es viable enlazar una licencia de componentes de terceros a un nodo concreto mediante la información específica de la máquina, como dirección MAC o el identificador de CPU.

-   También puede escalar la instancia de Integration Runtime para la integración de SSIS en Azure vertical u horizontalmente para que el número de nodos se reduzca o aumente en cualquier momento.

## <a name="the-solution"></a>La solución

Debido a las limitaciones de los métodos tradicionales de licencias que se describen en la sección anterior, la instancia de Integration Runtime para la integración de SSIS en Azure ofrece una nueva solución. Esta solución utiliza variables de entorno de Windows y variables del sistema de SSIS para el enlace y la validación de licencias de componentes de terceros. Los ISV pueden usar estas variables para obtener información duradera y única de la instancia de Integration Runtime para la integración de SSIS en Azure, como el identificador del clúster y el número de nodos del clúster. Con esta información, los ISV pueden luego enlazar la licencia del componente a una instancia de Integration Runtime para la integración de SSIS en Azure *como clúster*. Este enlace utiliza un identificador que no cambia cuando los clientes inician o detienen, escalan vertical u horizontalmente, reducen vertical u horizontalmente o reconfiguran la instancia de Integration Runtime para la integración de SSIS en Azure de algún modo.

En el siguiente diagrama se muestra la instalación típica, la activación y el enlace de licencia, así como los flujos de validación de los componentes de terceros que utilizan estas variables nuevas:

:::image type="content" source="media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png" alt-text="Instalación de componentes con licencia":::

## <a name="instructions"></a>Instrucciones
1. Los ISV pueden ofrecer sus componentes con licencia en varios SKU o niveles (por ejemplo, nodo único, hasta 5 nodos, hasta 10 nodos, etc.). El ISV proporciona la clave de producto correspondiente al cliente durante la compra. El ISV puede proporcionar también un contenedor de blobs de Azure Storage con un script de instalación de ISV y los archivos asociados. Los clientes puedan copiar estos archivos en su propio contenedor de almacenamiento y modificarlos con su propia clave de producto (por ejemplo, al ejecutar `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Los clientes pueden aprovisionar o volver a configurar la instancia de Integration Runtime para la integración de SSIS en Azure con el URI de SAS de su contenedor como parámetro. Para más información, consulte [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure).

2. Cuando la instancia de Integration Runtime para la integración de SSIS en Azure se aprovisiona o se vuelve a configurar, el programa de instalación de ISV se ejecuta en todos los nodos para consultar las variables de entorno de Windows, `SSIS_CLUSTERID` y `SSIS_CLUSTERNODECOUNT`. A continuación, la instancia de Integration Runtime para la integración de SSIS en AzureIS envía el identificador del clúster y la clave de producto del producto con licencia para que el servidor de activación de ISV genere una clave de activación.

3. Después de recibir la clave de activación, el programa de instalación de ISV puede almacenar la clave localmente en cada nodo (por ejemplo, en el registro).

4. Cuando los clientes ejecutan un paquete que usa componentes con licencia de ISV en un nodo dela instancia de Integration Runtime para la integración de SSIS en Azure, el paquete lee la clave de activación almacenada localmente y la compara con el identificador de clúster del nodo. El paquete también puede notificar el número de nodos de clúster para el servidor de activación de ISV (opcional).

    Este es un ejemplo de código que valida la clave de activación y notifica el número de nodos de clúster:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>Partners de ISV

Puede encontrar una lista de partners de ISV que han adaptado sus componentes y extensiones a la instancia de Integration Runtime para la integración de SSIS en Azure al final de esta entrada de blog: [Enterprise Edition, Custom Setup, and 3rd Party Extensibility for SSIS in ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360) (Enterprise Edition, instalación personalizada y extensibilidad de terceros para SSIS en ADF).

## <a name="next-steps"></a>Pasos siguientes

-   [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure)

-   [Enterprise Edition of the Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md) (Edición Enterprise para la instancia de Integration Runtime para la integración de SSIS en Azure)
