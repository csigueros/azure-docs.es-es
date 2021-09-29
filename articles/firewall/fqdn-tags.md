---
title: Información general de las etiquetas FQDN para Azure Firewall
description: Una etiqueta FQDN representa un grupo de nombres de dominio completo (FQDN) asociados con los servicios de Microsoft conocidos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 56f2a02109acd4f76cf5eb3b13dd70c878694f72
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604253"
---
# <a name="fqdn-tags-overview"></a>Información general de las etiquetas FQDN

Una etiqueta FQDN representa un grupo de nombres de dominio completo (FQDN) asociados con los servicios de Microsoft conocidos. Puede usar una etiqueta FQDN en las reglas de una aplicación para permitir que pase el tráfico de red saliente necesario a través del firewall.

Por ejemplo, para permitir manualmente el tráfico de red de Windows Update a través del firewall, deberá crear varias reglas de aplicación de acuerdo con la documentación de Microsoft. Si usa etiquetas FQDN, puede crear una regla de aplicación e incluir la etiqueta **Windows Update** para que el tráfico de red que apunta a los puntos de conexión de Windows Update pueda fluir a través del firewall.

No puede crear sus propias etiquetas FQDN ni especificar qué nombres de dominio completo se incluyen en una etiqueta. Microsoft administra los nombres de dominio completo que abarca la etiqueta FQDN y la actualiza a medida que estos cambian. 

<!--- screenshot of application rule with a FQDN tag.-->

En la siguiente tabla se muestran las etiquetas FQDN actuales que puede usar. Microsoft mantiene estas etiquetas y agregará otras adicionales periódicamente.

## <a name="current-fqdn-tags"></a>Etiquetas FQDN actuales

|Etiqueta FQDN  |Descripción  |
|---------|---------|
|Windows Update     |Permite el acceso saliente a Microsoft Update como se describe en [How to Configure a Firewall for Software Updates](/mem/configmgr/sum/get-started/install-a-software-update-point) (Cómo configurar el firewall para las actualizaciones de software).|
|Diagnósticos de Windows|Permite el acceso saliente a todos los [puntos de conexión de Diagnósticos de Windows](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Permite el acceso saliente a [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Permite el acceso saliente hacia el tráfico de la plataforma de ASE. Esta etiqueta no incluye los puntos de conexión de SQL y Storage específicos del cliente creados por ASE. Dichos puntos de conexión deben habilitarse a través de los [puntos de conexión de servicio](../virtual-network/tutorial-restrict-network-access-to-resources.md) o se deben agregar manualmente.<br><br>Para obtener más información acerca de cómo integrar Azure Firewall con ASE, consulte [Bloqueo de una instancia de App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Permite el acceso saliente a los servicios de Azure Backup.|
|HDInsight de Azure|Permite el acceso saliente hacia el tráfico de la plataforma de HDInsight. Esta etiqueta no incluye tráfico de SQL y Storage específico del cliente de HDInsight. Habilite esta opción mediante [puntos de conexión de servicio](../virtual-network/tutorial-restrict-network-access-to-resources.md) o agréguela manualmente.|
|WindowsVirtualDesktop (WVD)|Permite el tráfico de la plataforma de Windows Virtual Desktop. Esta etiqueta no cubre los puntos de conexión de Service Bus y del almacenamiento específico de la implementación creados por WVD. Además, se requieren las reglas de red de KMS y DNS. Para más información sobre la integración de Azure Firewall con WVD, consulte [Uso de Azure Firewall para proteger las implementaciones de Windows Virtual Desktop](protect-azure-virtual-desktop.md).|
|Azure Kubernetes Service (AKS)|Permite el acceso de salida a AKS. Para más información, consulte [Uso de Azure Firewall para proteger las implementaciones de Azure Kubernetes Service (AKS)](protect-azure-kubernetes-service.md).|

> [!NOTE]
> Al seleccionar Etiqueta FQDN en una regla de aplicación, en el campo protocolo: puerto debe estar seleccionada la opción **https**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo implementar una instancia de Azure Firewall, consulte [Tutorial: Implementación y configuración de Azure Firewall mediante Azure Portal](tutorial-firewall-deploy-portal.md).