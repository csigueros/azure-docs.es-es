---
title: Recomendaciones de seguridad para las máquinas virtuales de Azure
description: Aplique estas recomendaciones a las máquinas virtuales de Azure para facilitar el cumplimiento de las obligaciones de seguridad descritas en el modelo de responsabilidad compartida y mejorar la seguridad general de las implementaciones.
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 167d46b96853c372790002bdb0d9609581970426
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690203"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Recomendaciones de seguridad para las máquinas virtuales de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

En este artículo se incluyen recomendaciones de seguridad para Azure Virtual Machines. Estas recomendaciones le ayudarán a cumplir las obligaciones de seguridad descritas en nuestro modelo de responsabilidad compartida. También le ayudarán a mejorar la seguridad general de las soluciones de aplicaciones web. Para más información acerca de lo que hace Microsoft para cumplir sus responsabilidades como proveedor de servicios, consulte [Responsabilidades compartidas de la informática en la nube](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algunas de las recomendaciones de este artículo se pueden seguir automáticamente mediante Azure Security Center. Azure Security Center es la primera línea de defensa de los recursos de Azure. Se encarga de analizar el estado de seguridad de los recursos de Azure para identificar posibles vulnerabilidades de seguridad. Y luego ofrece recomendaciones para solucionar los puntos vulnerables. Para más información, consulte [Recomendaciones de seguridad de Azure Security Center](../security-center/security-center-recommendations.md).

Para más información sobre Azure Security Center, consulte [¿Qué es Azure Security Center?](../security-center/security-center-introduction.md).

## <a name="general"></a>General

| Recomendación | Comentarios | Security Center |
|-|----|--|
| Al crear imágenes de máquina virtual personalizadas, aplique las actualizaciones más recientes. | Antes de crear las imágenes, instale las actualizaciones más recientes tanto del sistema operativo como todas las aplicaciones que formarán parte de la imagen.  | - |
| Mantener actualizadas las máquinas virtuales. | Puede usar la solución [Update Management](../automation/update-management/overview.md) de Azure Automation para administrar las actualizaciones del sistema operativo de los equipos Windows y Linux en Azure. | [Sí](../security-center/asset-inventory.md) |
| Realice una copia de seguridad de las máquinas virtuales. | [Azure Backup](../backup/backup-overview.md) ayuda a proteger los datos de las aplicaciones y tiene unos costos operativos mínimos. Los errores de una aplicación pueden dañar los datos, y los errores humanos pueden crear errores en las aplicaciones. Azure Backup protege las máquinas virtuales que ejecutan Windows y Linux. | - |
| Use varias máquinas virtuales para aumentar la resistencia y la disponibilidad. | Si la máquina virtual ejecuta aplicaciones que deben tener alta disponibilidad, usar varias máquinas virtuales o [conjuntos de disponibilidad](./availability.md). | - |
| Adopte una estrategia de continuidad empresarial y recuperación ante desastres (BCDR). | Azure Site Recovery le permite elegir entre diferentes opciones diseñadas para dar soporte a la continuidad empresarial. Admite diferentes escenarios de replicación y de conmutación por error. Para más información, consulte [Acerca de Site Recovery](../site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Seguridad de los datos

| Recomendación | Comentarios | Security Center |
|-|----|--|
| Cifre los discos del sistema operativo. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) le ayuda a cifrar los discos de máquina virtual IaaS de Windows y Linux. Sin las claves necesarias, el contenido de los discos cifrado no se puede leer. El cifrado de disco protege los datos almacenados contra el acceso no autorizado que, de lo contrario, sería posible si se copiara el disco.| [Sí](../security-center/asset-inventory.md) |
| Cifre los discos de datos. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) le ayuda a cifrar los discos de máquina virtual IaaS de Windows y Linux. Sin las claves necesarias, el contenido de los discos cifrado no se puede leer. El cifrado de disco protege los datos almacenados contra el acceso no autorizado que, de lo contrario, sería posible si se copiara el disco.| -  |
| Limite el software instalado. | Limite el software instalado al estrictamente necesario para aplicar correctamente la solución. Esta guía ayuda a reducir la superficie de la solución expuesta a ataques. | - |
| Use antivirus o antimalware. | En Azure, puede usar software antimalware de proveedores de seguridad como Microsoft, Symantec, Trend Micro y Kaspersky. Este software ayudará a proteger las máquinas virtuales contra archivos malintencionados, adware y otras amenazas. Puede implementar Microsoft Antimalware en función de las cargas de trabajo de las aplicaciones. Microsoft Antimalware solo está disponible para máquinas Windows. Use la configuración de seguridad básica predeterminada o una configuración personalizada avanzada. Para más información, consulte [Microsoft Antimalware para Azure Cloud Services y Virtual Machines](../security/fundamentals/antimalware.md). | - |
| Almacene de forma segura claves y secretos. | Simplifique la administración de los secretos y claves proporcionando a los propietarios de la aplicación una opción segura administrada centralmente. Esta administración reduce el riesgo de que se produzca una pérdida o una amenaza accidental. Azure Key Vault puede almacenar de forma segura claves en módulos de seguridad de hardware (HSM) que tienen la certificación FIPS 140-2 nivel 2. Si necesita usar FIPs 140.2 nivel 3 para almacenar las claves y secretos, puede usar [Azure Dedicated HSM](../dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Administración de identidades y acceso 

| Recomendación | Comentarios | Security Center |
|-|----|--|
| Centralice la autenticación de máquinas virtuales. | Puede centralizar la autenticación de las máquinas virtuales Windows y Linux mediante la [autenticación de Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md). | - |

## <a name="monitoring"></a>Supervisión

| Recomendación | Comentarios | Security Center |
|-|----|--|
| Supervise sus máquinas virtuales. | Puede usar [Azure Monitor para VM](../azure-monitor/vm/vminsights-overview.md) para supervisar el estado de las máquinas virtuales y los conjuntos de escalado de máquinas virtuales de Azure. Los problemas de rendimiento con una máquina virtual pueden provocar la interrupción del servicio, lo que infringe el principio de seguridad de disponibilidad. | - |

## <a name="networking"></a>Redes

| Recomendación | Comentarios | Security Center |
|-|----|--|
| Restrinja del acceso a los puertos de administración. | Los atacantes analizan los intervalos de direcciones IP de la nube pública en busca de puertos de administración abiertos e intentan realizar ataques "sencillos", como contraseñas comunes y puntos vulnerables conocidos sin correcciones. Puede usar el [acceso a máquina virtual Just-In-Time (JIT)](../security-center/security-center-just-in-time.md) para bloquear el tráfico entrante a las máquinas virtuales de Azure, lo que reduce la exposición a ataques y. al mismo tiempo, se proporcionan conexiones sencillas a las máquinas virtuales cuando sean necesarias. | - |
| Límite el acceso a la red. | Los grupos de seguridad de red le permiten restringir el acceso a la red y controlar el número de puntos de conexión expuestos. Para más información, consulte [Creación, modificación o eliminación de un grupo de seguridad de red ](../virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Pasos siguientes

Póngase en contacto con el proveedor de la aplicación para obtener información acerca de otros requisitos de seguridad. Para más información sobre el desarrollo de aplicaciones seguras, consulte [Documentación sobre desarrollo seguro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).