---
title: 'Descripción de los costes totales de implementación de Azure Virtual Desktop: Azure'
description: Cómo calcular el coste total de la implementación de Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 93383fa4b681dd7f0768f5ea30c24e64e9dcb2b9
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123036597"
---
# <a name="understanding-total-azure-virtual-desktop-deployment-costs"></a>Descripción de los costes totales de implementación de Azure Virtual Desktop

Los costes de Azure Virtual Desktop proceden de dos orígenes: el consumo subyacente de recursos de Azure y las licencias. Los costes de Azure Virtual Desktop se cobran a la organización propietaria de la implementación de Azure Virtual Desktop, no a los usuarios finales que acceden a los recursos de implementación. Algunos cargos de licencia se deben pagar por adelantado. Otras licencias y los cargos de consumo de recursos subyacentes se basan en medidores que realizan un seguimiento del uso.

En este artículo, explicaremos los costes de consumo y licencias, y cómo calcular los costes del servicio antes de implementar Azure Virtual Desktop mediante la Calculadora de precios de Azure. En este artículo también se incluyen instrucciones sobre cómo usar Azure Cost Management para ver los costes después de implementar Azure Virtual Desktop.

>[!NOTE]
>El cliente que paga por la implementación de Azure Virtual Desktop es responsable de controlar los costes y la administración de los recursos durante la vigencia de la implementación. Si el propietario ya no necesita los recursos conectados a su implementación de Azure Virtual Desktop, debe asegurarse de que esos recursos se quitan correctamente. Para más información, consulte [Administración de recursos de Azure con Azure Portal](../../azure-resource-manager/management/manage-resources-portal.md).

## <a name="consumption-costs"></a>Costes de consumo

Los costes de consumo son la suma de todos los cargos de uso de los recursos de Azure para los usuarios que acceden a un grupo de hosts de Azure Virtual Desktop. Estos cargos proceden de las máquinas virtuales (VM) del host de sesión dentro de los grupos hosts, incluidos los recursos compartidos por otros productos en Azure y los sistemas de administración de identidades que requieren la ejecución de infraestructura adicional para mantener el servicio disponible, como un controlador de dominio para Active Directory Domain Services (AD DS).

### <a name="session-host-vm-costs"></a>Costes de máquina virtual del host de sesión

En Azure Virtual Desktop, las máquinas virtuales del host de sesión usan los tres servicios de Azure siguientes:

- Máquinas virtuales (proceso)
- Storage para discos administrados (incluido el almacenamiento del sistema operativo por máquina virtual y los discos de datos para escritorios personales)
- Ancho de banda (redes)

Estos cargos se pueden ver en el nivel de grupo de recursos de Azure donde se asignan los recursos específicos del grupo de hosts, incluidas las máquinas virtuales del host de sesión. Si uno o varios grupos de host también están configurados para usar el servicio Log Analytics de pago para enviar datos de máquina virtual a la característica opcional de conclusiones de Azure Virtual Desktop, en la factura también se le cobrará por Log Analytics para los grupos de recursos de Azure correspondientes. Puede consultar [Supervisión de los cálculos de costes y precios de Windows Virtual Desktop](../azure-monitor-costs.md) para más información.

De los tres costes por uso del host de sesión de máquina virtual principal que se enumeran al principio de esta sección, el proceso suele ser el más elevado. Para mitigar los costes de proceso y optimizar la demanda de recursos con disponibilidad, muchos clientes eligen [escalar los hosts de sesión automáticamente](../set-up-scaling-script.md).

### <a name="domain-controller-costs-for-active-directories"></a>Costes del controlador de dominio para instancias de Active Directory

Las máquinas virtuales del controlador de dominio usan los cuatro servicios de Azure siguientes como mínimo:

- Máquinas virtuales (proceso)
- Storage para discos administrados (incluido el almacenamiento del sistema operativo por máquina virtual y los discos de datos para escritorios personales)
- Ancho de banda (redes)
- Redes virtuales

Si la implementación de Azure Virtual Desktop se basa en un controlador de dominio para ejecutar Active Directory, debe incluirlo en el coste total de la implementación de Azure Virtual Desktop. Los controladores de dominio que se hospedan en Azure también compartirán los tres servicios de Azure para las máquinas virtuales de host de sesión que se describen en [Costes de máquina virtual del host de sesión](#session-host-vm-costs), ya que una máquina virtual de Azure estándar también debe mantener disponibles las identidades de Active Directory.

Sin embargo, los controladores de dominio tienen algunas diferencias clave con respecto a las máquinas virtuales del host de sesión:

- Los controladores de dominio generarán un cargo adicional de red virtual porque tienen que comunicarse con otros servicios fuera de la implementación.
- El escalado de la disponibilidad del controlador de dominio puede causar problemas porque las implementaciones necesitan que los controladores de dominio estén siempre disponibles.

### <a name="shared-service-costs"></a>Costes de servicio compartido

En función de las características que use la implementación de Azure Virtual Desktop, es posible que también tenga que pagar por Azure Storage por cualquier combinación de las siguientes características opcionales:

- [Asociación de aplicaciones en formato .MSIX](../what-is-app-attach.md)
- [Imágenes personalizadas del sistema operativo](../set-up-customize-master-image.md)
- [Perfiles de FSLogix](../fslogix-containers-azure-files.md)

Estas características usan opciones de almacenamiento de Azure como [Azure Files](../../storage/files/storage-files-introduction.md) y [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-introduction.md), por lo que significa que pueden compartir su almacenamiento con otros servicios de Azure además de Azure Virtual Desktop. Se recomienda crear una cuenta de almacenamiento independiente para el almacenamiento que compre para las características de Azure Virtual Desktop a fin de asegurarse de que puede diferenciar entre sus costes y los costes de otros servicios de Azure que usa.

### <a name="user-access-costs"></a>Costes de acceso de usuarios

Se pagan los costes de acceso de usuarios cada mes por cada usuario que se conecta a aplicaciones o escritorios en la implementación de Azure Virtual Desktop. Para más información sobre los precios de acceso por usuario de Azure Virtual Desktop, consulte [Descripción de las licencias y los precios de acceso por usuario](licensing.md).

## <a name="predicting-costs-before-deploying-azure-virtual-desktop"></a>Predicción de costes antes de implementar Azure Virtual Desktop

Ahora que comprende los conceptos básicos, vamos a empezar el cálculo. Para ello, es necesario calcular tanto el consumo como los costes de acceso de los usuarios.

### <a name="predicting-consumption-costs"></a>Predicción de costes de consumo

Puede usar la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para calcular los costes de consumo de Azure Virtual Desktop antes de crear una implementación. Aquí se muestra cómo predecir los costes de consumo:

1. En la calculadora de precios, seleccione la pestaña **Proceso** para mostrar las opciones de proceso de la Calculadora de precios de Azure.

2. Seleccione **Azure Virtual Desktop**. Debe aparecer el módulo de la calculadora de Azure Virtual Desktop.

3. Escriba los valores de la implementación en los campos para calcular la factura mensual de Azure en función del uso esperado de proceso, almacenamiento y red.

>[!NOTE]
>Actualmente, el módulo de Calculadora de precios de Azure de Azure Virtual Desktop solo puede calcular los costes de consumo de las máquinas virtuales del host de sesión y el almacenamiento adicional agregado de cualquier característica opcional de Azure Virtual Desktop que requiera almacenamiento y que elija implementar. Sin embargo, puede agregar estimaciones para otras características de Azure Virtual Desktop en módulos independientes dentro de la misma página de la calculadora de precios de Azure para obtener una estimación de costes más completa o modular.
>
>Puede agregar módulos adicionales de Calculadora de precios de Azure para calcular el impacto en el costo de otros componentes de la implementación, incluidos, entre otros:
>
>- Controladores de dominios
>- Otras características dependientes del almacenamiento, como imágenes personalizadas del sistema operativo, asociación de aplicaciones MSIX y Azure Monitor

### <a name="predicting-user-access-costs"></a>Predicción de los costes de acceso de usuarios

Los costes de acceso de usuario dependen del número de usuarios que se conectan a la implementación cada mes. Para obtener información sobre cómo calcular los costes totales de acceso de usuarios que puede esperar, consulte [Estimación de los costes de streaming de aplicaciones por usuario para Azure Virtual Desktop](streaming-costs.md).

## <a name="viewing-costs-after-deploying-azure-virtual-desktop"></a>Visualización de los costes después de implementar Azure Virtual Desktop

Una vez que implemente Azure Virtual Desktop, puede usar [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) para ver las facturas. En esta sección se explica cómo buscar los precios de los servicios actuales.

### <a name="viewing-bills-for-consumption-costs"></a>Visualización de las facturas de los costes de consumo

Con los permisos adecuados de [Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md), los usuarios de su organización, como los administradores de facturación, pueden usar las [herramientas de análisis de costes](../../cost-management-billing/costs/cost-analysis-common-uses.md) y buscar las facturas de Azure a través de [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) para realizar un seguimiento de los costes de consumo mensuales de Azure Virtual Desktop en su suscripción o suscripciones de Azure.

### <a name="viewing-bills-for-user-access-costs"></a>Visualización de los costes de acceso de usuarios de las facturas

Los costes de acceso de usuarios aparecerán cada ciclo de facturación en la factura de Azure para cualquier suscripción inscrita, junto con los costes de consumo y otros cargos de Azure.

## <a name="next-steps"></a>Pasos siguientes

Si quiere una idea más clara de cuánto van a costar partes específicas de la implementación, consulte estos artículos:

- [Descripción de las licencias y los precios de acceso por usuario](licensing.md)
- [Estimación de los costos de streaming de aplicaciones por usuario para Azure Virtual Desktop](streaming-costs.md)