---
title: Configuración de claves administradas por el cliente en Azure Sentinel | Microsoft Docs
description: Aprenda a configurar claves administradas por el cliente (CMK) en Azure Sentinel.
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
ms.date: 07/01/2021
ms.author: yelevin
ms.openlocfilehash: d9f6ce8b85aae451fbab318b85f50fb0da7919a6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541304"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configuración de claves administradas por el cliente en Azure Sentinel

En este artículo se proporciona información general y pasos para configurar una [clave administrada por el cliente (CMK)](../azure-monitor/logs/customer-managed-keys.md) para Azure Sentinel. Una CMK permite proporcionar a todos los datos almacenados en Azure Sentinel (ya cifrados por Microsoft en todos los recursos de almacenamiento pertinentes) una capa adicional de protección con una clave de cifrado creada por el usuario y propiedad de este almacenada en su instancia de [Azure Key Vault](../key-vault/general/overview.md).

## <a name="prerequisites"></a>Requisitos previos

- La funcionalidad de CMK requiere un clúster dedicado de Log Analytics con al menos un nivel de compromiso de 500 GB/día. Se pueden vincular varias áreas de trabajo al mismo clúster dedicado; todas compartirán la misma clave administrada por el cliente.

- Después de completar los pasos de esta guía y antes de usar el área de trabajo, para la confirmación de incorporación, póngase en contacto con el [grupo de productos de Azure Sentinel](mailto:azuresentinelCMK@microsoft.com).

- Obtenga más información sobre los [precios de clústeres dedicados de Log Analytics](../azure-monitor/logs/logs-dedicated-clusters.md#cluster-pricing-model).

## <a name="considerations"></a>Consideraciones

- La incorporación de un área de trabajo de CMK a Sentinel solo se admite por medio de la API REST y no de Azure Portal. Actualmente, no se admiten las plantillas de Resource Manager (plantillas de ARM) para la incorporación de CMK.

- La funcionalidad de CMK de Azure Sentinel solo se proporciona a las *áreas de trabajo de los clústeres dedicados de Log Analytics* que aún *no se han incorporado a Azure Sentinel*.

- Los siguientes cambios relacionados con CMK *no se admiten* porque serán ineficaces (los datos de Azure Sentinel se seguirán cifrando solo mediante la clave administrada por Microsoft y no con la clave administrada por el cliente):

  - Habilitación de CMK en un área de trabajo que *ya está incorporada* a Azure Sentinel.
  - Habilitación de CMK en un clúster que contiene áreas de trabajo incorporadas a Sentinel.
  - Vinculación de un área de trabajo que no tiene CMK e incorporada a Sentinel a un clúster habilitado para CMK.

- Los siguientes cambios relacionados con CMK *no se admiten* porque pueden dar lugar a un comportamiento indefinido y problemático:

  - Deshabilitación de CMK en un área de trabajo que ya está incorporada a Azure Sentinel.
  - Establecimiento de un área de trabajo habilitada para CMK e incorporada a Sentinel como un área de trabajo que no es de CMK desvinculándola de su clúster dedicado habilitado para CMK.
  - Deshabilitación de CMK en un clúster dedicado de Log Analytics habilitado para CMK.

- Azure Sentinel admite identidades asignadas por el sistema en la configuración de CMK. Por lo tanto, la identidad del clúster dedicado de Log Analytics debe ser de tipo **Asignado por el sistema**. Se recomienda usar la identidad que se asigna automáticamente al clúster de Log Analytics cuando se crea.

- Actualmente *no se admite* el cambio de la clave administrada por el cliente a otra clave (con otro URI). Para cambiar la clave, debe [rotarla](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

- Antes de realizar cambios de CMK en un área de trabajo de producción o en un clúster de Log Analytics, póngase en contacto con el [grupo de productos de Azure Sentinel](mailto:azuresentinelCMK@microsoft.com).

## <a name="how-cmk-works"></a>Funcionamiento de CMK 

La solución Azure Sentinel usa varios recursos y características de almacenamiento para la recopilación de registros, incluido un clúster dedicado de Log Analytics. Como parte de la configuración de CMK de Azure Sentinel, tendrá que configurar los valores de CMK en el clúster dedicado de Log Analytics relacionado. Los datos guardados por Azure Sentinel en recursos de almacenamiento distintos de Log Analytics también se cifrarán mediante la clave administrada por el cliente configurada para el clúster dedicado de Log Analytics.

Consulte la siguiente documentación pertinente adicional:
- [Clave administrada por el cliente de Azure Monitor](../azure-monitor/logs/customer-managed-keys.md)
- [Azure Key Vault](../key-vault/general/overview.md)
- [Clústeres dedicados de registros de Azure Monitor](../azure-monitor/logs/logs-dedicated-clusters.md)

> [!NOTE]
> Si habilita CMK en Azure Sentinel, no se habilita ninguna característica de versión preliminar pública que no admita CMK.

## <a name="enable-cmk"></a>Habilitación de CMK 

Para aprovisionar CMK, siga estos pasos: 

1.  Cree una instancia de Azure Key Vault y genere o importe una clave.

2.  Habilite CMK en el área de trabajo de Log Analytics.

3.  Regístrese en el proveedor de recursos de Cosmos DB.

4.  Agregue una directiva de acceso a la instancia de Azure Key Vault.

5.  Incorpore el área de trabajo en Azure Sentinel por medio de [Onboarding API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx).

### <a name="step-1-create-an-azure-key-vault-and-generate-or-import-a-key"></a>PASO 1: Creación de una instancia de Azure Key Vault y generación o importación de una clave

1.  [Cree un recurso de Azure Key Vault](/azure-stack/user/azure-stack-key-vault-manage-portal) y genere o importe una clave para usarla al cifrar los datos.
    > [!NOTE]
    >  La instancia de Azure Key Vault debe configurarse como recuperable para proteger la clave y el acceso.

1.  [Active las opciones de recuperación:](../key-vault/general/key-vault-recovery.md)

    -   Asegúrese de que la [eliminación temporal](../key-vault/general/soft-delete-overview.md) esté activada.

    -   Active la [protección de purgas](../key-vault/general/soft-delete-overview.md#purge-protection) para protegerse frente a la eliminación forzada del secreto o el almacén incluso después de una eliminación temporal.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>PASO 2: Habilitación de CMK en el área de trabajo de Log Analytics

Siga las instrucciones de [Configuración de la clave administrada por el cliente de Azure Monitor](../azure-monitor/logs/customer-managed-keys.md) para crear un área de trabajo de CMK que se use como área de trabajo de Azure Sentinel en los pasos siguientes.

### <a name="step-3-register-to-the-cosmos-db-resource-provider"></a>PASO 3: Registro en el proveedor de recursos de Cosmos DB

Azure Sentinel funciona con Cosmos DB como recurso de almacenamiento adicional. Asegúrese de registrarse en el proveedor de recursos de Cosmos DB.

Siga la instrucción de Cosmos DB para [registrar el proveedor de recursos de Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) para la suscripción de Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>PASO 4: Adición de una directiva de acceso a la instancia de Azure Key Vault

Asegúrese de agregar acceso desde Cosmos DB a la instancia de Azure Key Vault. Siga la instrucción de Cosmos DB de [agregar una directiva de acceso a la instancia de Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-access-policy) con la entidad de seguridad de Azure Cosmos DB.

### <a name="step-5-onboard-the-workspace-to-azure-sentinel-via-the-onboarding-api"></a>PASO 5: Incorporación del área de trabajo a Azure Sentinel por medio de Onboarding API

Incorpore el área de trabajo en Azure Sentinel por medio de [Onboarding API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revocación o eliminación de la clave de cifrado de claves

En caso de que un usuario revoque la clave de cifrado (CMK), ya sea mediante su eliminación o la eliminación del acceso al clúster dedicado y al proveedor de recursos de Cosmos DB, Azure Sentinel respetará el cambio y se comportará como si los datos ya no estuvieran disponibles en un plazo de una hora. Desde ese momento se evita cualquier operación que use recursos de almacenamiento persistente, como la ingesta de datos, los cambios de configuración persistentes y la creación de incidentes. Los datos almacenados previamente no se eliminan, pero permanecen inaccesibles. Los datos inaccesibles se rigen por la directiva de retención de datos y se purgan conforme a esa directiva.

La única operación posible una vez revocada o eliminada la clave de cifrado es la eliminación de la cuenta.

Si se restaura el acceso después de la revocación, Azure Sentinel restaura el acceso a los datos en una hora.

El acceso a los datos se puede revocar si se deshabilita la clave administrada por el cliente en el almacén de claves o se elimina la directiva de acceso a la clave, tanto para el clúster dedicado de Log Analytics como para Cosmos DB. No se admite la revocación del acceso quitando la clave del clúster dedicado de Log Analytics ni quitando la identidad asociada a ese clúster.

Para más información sobre el funcionamiento de este proceso en Azure Monitor, consulte [Revocación de CMK de Azure Monitor](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## <a name="customer-managed-key-rotation"></a>Rotación de claves administradas por el cliente

Azure Sentinel y Log Analytics admiten la rotación de claves. Cuando un usuario realiza una rotación de claves en Key Vault, Azure Sentinel admite la nueva clave en una hora.

En Key Vault, puede realizar la rotación de claves mediante la creación de una nueva versión de la clave:

![Rotación de claves](./media/customer-managed-keys/key-rotation.png)

Puede deshabilitar la versión anterior de la clave tras 24 horas, o bien después de que los registros de auditoría de Azure Key Vault ya no muestren ninguna actividad que use la versión anterior.

Después de rotar una clave, debe actualizar explícitamente el recurso de clúster dedicado en Log Analytics con la nueva versión de la clave de Azure Key Vault. Para más información, consulte [Rotación de CMK de Azure Monitor](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="replacing-a-customer-managed-key"></a>Reemplazo de una clave administrada por el cliente

Azure Sentinel no admite el reemplazo de una clave administrada por el cliente. En su lugar debe usar la [funcionalidad de rotación de claves](#customer-managed-key-rotation).

## <a name="next-steps"></a>Pasos siguientes
En este documento ha aprendido a configurar una clave administrada por el cliente en Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.
