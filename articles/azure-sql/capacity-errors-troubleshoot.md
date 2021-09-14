---
title: Resolución de errores de capacidad con recursos de Azure SQL
description: Obtenga información sobre cómo resolver posibles errores de capacidad al intentar implementar o escalar recursos de Azure SQL Database o Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 09/03/2021
ms.custom: references_regions
ms.openlocfilehash: 885be735055eaf65d67466694e65d6cf7fdf00da
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481589"
---
# <a name="resolve-capacity-errors-with-azure-sql-database-or-azure-sql-managed-instance"></a>Resolución de errores de capacidad con Azure SQL Database o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

En este artículo, aprenderá a resolver errores de capacidad al implementar recursos de Azure SQL Database o Azure SQL Managed Instance. 

## <a name="exceeded-quota"></a>Cuota superada 

Si encuentra alguno de los siguientes errores al intentar implementar el recurso de Azure SQL, [solicite aumentar la cuota](database/quota-increase-request.md): 

- `Server quota limit has been reached for this location. Please select a different location with lower server count.`
- `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx.`
- Durante una operación de escalado, es posible que vea el siguiente error:    
  `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx. `. 

## <a name="subscription-access"></a>Acceso a la suscripción

Es posible que la suscripción no tenga acceso para crear un servidor en la región seleccionada si la suscripción no se ha registrado con el proveedor de recursos (RP) de SQL.  

Si ve los siguientes errores, [registre la suscripción con el proveedor de recursos de SQL](#register-with-sql-rp):
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`


## <a name="enable-region"></a>Habilitar región 

Es posible que la suscripción no tenga acceso para crear un servidor en la región seleccionada si esa región no se ha habilitado. Para resolver este problema, presente una [solicitud de soporte técnico para habilitar una región específica](database/quota-increase-request.md#region) para la suscripción. 

Si ve los siguientes errores, abra una incidencia de soporte técnico para habilitar una región específica: 
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`



## <a name="register-with-sql-rp"></a>Registro con el proveedor de recursos de SQL

Para implementar recursos de Azure SQL, registre la suscripción con el proveedor de recursos (RP) de SQL. 

Puede registrar la suscripción mediante Azure Portal, la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/install-az-ps). 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para registrar la suscripción en Azure Portal, siga estos pasos: 

1. Abra Azure Portal y vaya a **Todos los servicios**.
1. Vaya a **Suscripciones** y seleccione la suscripción que le interese.
1. En la página **Suscripciones**, en **Configuración**, seleccione **Proveedores de recursos**.
1. Escriba **sql** en el filtro para mostrar las extensiones relacionadas con SQL.
1. Seleccione **Registrar**, **Volver a registrar** o **Anular registro** para el proveedor **Microsoft.Sql**, según la acción que quiera realizar.

   ![Modificación del proveedor](./media/capacity-errors-troubleshoot/register-with-sql-rp.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/bash)

Para registrar la suscripción mediante la [CLI de Azure](/cli/azure/install-azure-cli), ejecute este cmdlet:

```azurecli-interactive
# Register the SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMac 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Para registrar la suscripción mediante [Azure PowerShell](/powershell/azure/install-az-ps), ejecute este cmdlet: 

```powershell-interactive
# Register the SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.Sql

```

---

## <a name="additional-provisioning-issues"></a>Otros problemas de aprovisionamiento

Si sigue experimentando problemas de aprovisionamiento, abra una solicitud de acceso a una **región** en el tema de soporte técnico de SQL Database y especifique las DTU o los núcleos virtuales que desea consumir en Azure SQL Database o Azure SQL Managed Instance. 

## <a name="azure-program-regions"></a>Regiones de programas de Azure 

Las ofertas de programas de Azure (Pase para Azure, Imagine, Azure for Students, MPN, BizSpark, BizSpark Plus, Microsoft for Startups y ofertas de patrocinio, suscripciones de Visual Studio y MSDN) tienen acceso a un conjunto limitado de regiones. 

Si la suscripción forma parte de una oferta de un programa de Azure y desea solicitar acceso a cualquiera de las siguientes regiones, considere la posibilidad de usar una región alternativa en su lugar: 

_Centro de Australia, Centro de Australia 2, Sudeste de Australia, Sudeste de Brasil, Este de Canadá, Este de China, Norte de China, Norte de China 2, Sur de Francia, Norte de Alemania, Oeste de Japón, JIO India Central, Jio India Occidental, Sur de Corea del Sur, Oeste de Noruega, Oeste de Sudáfrica, Sur de la India, Oeste de Suiza, Centro de Emiratos Árabes Unidos , Oeste de Reino Unido, US DoD (centro), US DoD (este), US Gov Arizona, US Gov Texas, Centro-oeste de EE. UU., Oeste de la India._ 

## <a name="next-steps"></a>Pasos siguientes

Una vez que se envía la solicitud, esta será revisada. Recibirá una respuesta en función de la información que haya proporcionado en el formulario.

Para más información sobre otros límites de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
