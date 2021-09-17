---
title: Implementación de OpenShift Container Platform 4.x en Azure
description: Implementación de OpenShift Container Platform 4.x en Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f1e37e401bd44c4e6077e9a69dbc6ad72f8eaa0d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693963"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Implementación de OpenShift Container Platform 4.x en Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Conjuntos de escalado flexibles 

Ahora se admite la implementación de OpenShift Container Platform (OCP) 4.2 en Azure a través del modelo de infraestructura aprovisionada por el instalador (IPI).  La página de aterrizaje para probar OpenShift 4 es [try.openshift.com](https://try.openshift.com/). Para instalar OCP 4.2 en Azure, visite la página de [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned).  Se necesitan credenciales de Red Hat para acceder a este sitio.


## <a name="notes"></a>Notas 

 - Se necesita una entidad de servicio (SP) de Azure Active Directory (AAD) para instalar y ejecutar OCP 4.x en Azure.
     - Se debe conceder a la entidad de servicio el permiso de API **Application.ReadWrite.OwnedBy** para Azure Active Directory Graph.
     - Un Administrador de inquilinos de AAD debe conceder consentimiento de administrador para que este permiso de API surta efecto.
     - Se debe conceder a la entidad de servicio los roles **Colaborador** y **Administrador de acceso de usuario** para la suscripción.
 - El modelo de instalación de OCP 4.x es diferente de 3.x y no hay ninguna plantilla Azure Resource Manager disponible para implementar OCP 4.x en Azure
 - Si se producen problemas durante el proceso de instalación, póngase en contacto con la compañía adecuada (Microsoft o Red Hat).

| Descripción del problema | Punto de contacto |
|-------------------|---------------|
| Problemas específicos de Azure (AAD, SP, suscripción de Azure, etc.)                              | Microsoft |
| Problemas específicos de OpenShift (problemas de instalación/errores, suscripción de Red Hat, etc.) |  Red Hat  |




## <a name="next-steps"></a>Pasos siguientes

- [Introducción a OpenShift Container Platform](https://docs.openshift.com)
