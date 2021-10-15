---
title: Configuración de características de red para un volumen de Azure NetApp Files | Microsoft Docs
description: Aquí se describen las opciones de las características de red y cómo configurar la opción Características de red para un volumen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.custom: references_regions
ms.author: b-juche
ms.openlocfilehash: 6350084ef916132d7b4d77da00853836daeabccd
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539010"
---
# <a name="configure-network-features-for-an-azure-netapp-files-volume"></a>Configuración de características de red para un volumen de Azure NetApp Files

La funcionalidad **Características de red** está disponible en su versión preliminar pública.  Esta funcionalidad le permite indicar si quiere usar características de red virtual para un volumen de Azure NetApp Files. Con esta funcionalidad, puede configurar la opción en **_Estándar_ *_ o _* _Básica_**. Puede especificar la configuración al crear un nuevo volumen de protocolo dual, SMB o NFS. Consulte [Directrices para el planeamiento de red de Azure NetApp Files](azure-netapp-files-network-topologies.md) para obtener información más detallada sobre las característica de la red.

En este artículo podrá comprender las opciones y verá cómo configurar las características de red.

## <a name="options-for-network-features"></a>Opciones de las características de red 

Hay dos configuraciones disponibles para las características de red: 

* ***Estándar***  
    Esta configuración habilita las características de red virtual para el volumen.  

    Si necesita límites de IP más altos o características de red virtual como [grupos de seguridad de red](../virtual-network/network-security-groups-overview.md), [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md#user-defined) o patrones de conectividad adicionales, debe establecer las **Características de red**  en *Estándar*.

* ***Basic***  
    Esta configuración proporciona límites de IP reducidos (<1000) y ninguna características de red virtual adicional para los volúmenes.

    Debe establecer las **Características de red** en *Básicas* si no necesita características de red virtual.  

## <a name="supported-regions"></a>Regiones admitidas 

Actualmente, la funcionalidad de las características de red se admite en las siguientes regiones: 

* Centro-Norte de EE. UU 

## <a name="considerations"></a>Consideraciones

* Independientemente de la opción de características de red que establezca (*Estándar* o *Básica*), una red virtual de Azure solo puede tener una subred delegada en los archivos de Azure NetApp. Consulte [Delegación de una subred en Azure NetApp Files](azure-netapp-files-delegate-subnet.md#considerations). 
 
* Actualmente, solo puede especificar la configuración de las características de red durante el proceso de creación de un nuevo volumen. Asimismo, no se puede modificar la configuración en volúmenes existentes. 

* Solo puede crear volúmenes con las características de red estándar si la [región de Azure correspondiente admite la funcionalidad de volumen Estándar](#supported-regions). 
    * Si la funcionalidad de volumen Estándar es compatible con la región, el campo Características de red de la página Crear un volumen tiene como valor predeterminado *Estándar*. Puede cambiar esta opción a *Básica* si lo desea. 
    * Si la capacidad de volumen estándar no está disponible para la región, el campo Características de red de la página Crear un volumen tiene como valor predeterminado *Básica* y no se puede modificar la configuración.

* La capacidad de localizar el almacenamiento compatible con el tipo deseado de características de red depende de la red virtual especificada.  Si no puede crear un volumen debido a que sus recursos no son suficientes, puede probar otra red virtual que tenga disponible un almacenamiento compatible.
  
## <a name="register-the-feature"></a>Registrar la característica 

Recuerde que la funcionalidad de características de red se encuentra actualmente en versión preliminar pública. Si usa esta característica por primera vez, debe registrarla primero.

1.  Registre la característica mediante los comandos siguientes:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

2. Compruebe el estado del registro de la característica: 

    > [!NOTE]
    > **RegistrationState** puede estar en el estado `Registering` hasta 60 minutos antes de cambiar a `Registered`. Espere hasta que el estado sea `Registered` antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

También puede usar los comandos de la [CLI de Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` y `az feature show` para registrar la característica y mostrar el estado del registro. 

## <a name="set-the-network-features-option"></a>Establecimiento de la opción Características de red

En esta sección se muestra cómo establecer la opción Características de red. 

1. Durante el proceso de creación de un nuevo volumen de [protocolo doble](azure-netapp-files-create-volumes.md), [SMB](azure-netapp-files-create-volumes-smb.md) o [NFS](create-volumes-dual-protocol.md), puede establecer la opción **Características de red** en **Básicas** o **Estándar** en la pestaña Básica de la pantalla Crear un volumen.

    En la captura de pantalla siguiente se muestra un ejemplo de creación de volúmenes para una región que admite las funcionalidades de características de red estándar: 

    ![Captura de pantalla que muestra la creación de volúmenes para las características de red estándar.](../media/azure-netapp-files/network-features-create-standard.png)

    En la captura de pantalla siguiente se muestra un ejemplo de creación de volúmenes para una región que *no* admite las funcionalidades de características de red estándar: 

    ![Captura de pantalla que muestra la creación de volúmenes para las características de red básicas.](../media/azure-netapp-files/network-features-create-basic.png)

2. Antes de completar el proceso de creación del volumen, puede mostrar la configuración de las características de red especificadas en la pestaña **Revisar y crear** de la pantalla Crear un volumen. Para completar la creación del volumen, haga clic en **Crear**.

    ![Captura de pantalla que muestra la pestaña Revisar y crear de la creación del volumen.](../media/azure-netapp-files/network-features-review-create-tab.png)

3. Puede hacer clic en **Volúmenes** para mostrar la configuración de las características de red de cada volumen:

    [ ![Captura de pantalla que muestra la página Volúmenes que a su vez muestra la configuración de características de red.](../media/azure-netapp-files/network-features-volume-list.png)](../media/azure-netapp-files/network-features-volume-list.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes  

* [Instrucciones para el planeamiento de red de Azure NetApp Files](azure-netapp-files-network-topologies.md)
* [Creación de un volumen de NFS para Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Cree un volumen SMB para Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Creación de un volumen de dos protocolos para Azure NetApp Files](create-volumes-dual-protocol.md) 