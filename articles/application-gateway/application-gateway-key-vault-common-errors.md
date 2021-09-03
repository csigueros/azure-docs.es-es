---
title: Errores de Key Vault comunes en Application Gateway
titleSuffix: Azure Application Gateway
description: Este artículo ayuda a identificar las incidencias relacionadas con Key Vault y a resolverlas para que realizar las operaciones de Application Gateway sin problemas.
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 9be1caf74a7eeaa225c2025767eeef86053c17a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748800"
---
# <a name="common-key-vault-errors-in-application-gateway"></a>Errores de Key Vault comunes en Application Gateway

Esta guía de solución de problemas le ayudará a comprender los detalles de los códigos de error de Key Vault, su causa y el recurso Key Vault asociado que está causando el problema. También incluye una guía paso a paso para la resolución de estos errores de configuración.

> [!NOTE]
> Los registros de diagnósticos de Key Vault en Application Gateway se generan cada intervalo de cuatro horas. Por lo tanto, en algunos casos, es posible que tenga que esperar a que los registros se actualicen si el diagnóstico sigue mostrando el error después de haber corregido la configuración.

> [!TIP]
> Se recomienda usar un identificador secreto sin versión. De este modo, Application Gateway girará automáticamente el certificado si hay una versión más reciente disponible en Key Vault.  Un ejemplo de URI de secreto sin versión `https://myvault.vault.azure.net/secrets/mysecret/`.

### <a name="list-of-error-codes-and-their-details"></a>Lista de códigos de error y sus detalles

[comment]: # (Código de error 1)
#### <a name="1-error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>**1) Código de error:** UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**Descripción:** la identidad administrada asignada por el usuario no tiene el permiso OBTENER. 

**Resolución:** configure la directiva de acceso de Key Vault para conceder a la identidad administrada asignada por el usuario permisos OBTENER en secretos. 
1. Vaya a la instancia de Key Vault vinculada en Azure Portal.
2. Abra la hoja Directivas de acceso.
3. Seleccione "Directiva de acceso al almacén" para Modelo de permisos.
4. Seleccione el permiso "Obtener" en Secreto para la identidad administrada asignada por el usuario determinada.
5. Guardado de la configuración


:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text="La identidad asignada por el usuario no tiene el permiso Obtener en Key Vault.":::

Para obtener una guía completa sobre la directiva de acceso de Key Vault, consulte este [artículo](../key-vault/general/assign-access-policy-portal.md).
</br></br>



[comment]: # (Código de error 2)
#### <a name="2-error-code-secretdisabled"></a>**2) Código de error:** SecretDisabled 

**Descripción:** El certificado asociado se ha deshabilitado en Key Vault. 

**Resolución:** Vuelva a habilitar la versión del certificado que está actualmente en uso para Application Gateway.
1. Vaya a la instancia de Key Vault vinculada en Azure Portal.
2. Abra la hoja Certificados.
3. Haga clic en el nombre del certificado necesario y luego en la versión deshabilitada.
4. Use el botón de alternancia de la página de administración para habilitar esa versión del certificado.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="Vuelva a habilitar un secreto.":::
</br></br>


[comment]: # (Código de error 3)
#### <a name="3-error-code-secretdeletedfromkeyvault"></a>**3) Código de error:** SecretDeletedFromKeyVault 

**Descripción:** el certificado asociado se ha eliminado de Key Vault. 

**Resolución:** el objeto de certificado eliminado dentro de Key Vault se puede restaurar mediante su característica de recuperación de eliminación temporal. Para recuperar un certificado eliminado: 
1. Vaya a la instancia de Key Vault vinculada en Azure Portal.
2. Abra la hoja Certificados.
3. Use la pestaña "Certificados eliminados administrados" para recuperar un certificado eliminado.

Por otro lado, si un objeto de certificado se elimina permanentemente, deberá crear un nuevo certificado y actualizar Application Gateway con los detalles del nuevo certificado. Al realizar la configuración a través de la CLI de Azure o Azure PowerShell, se recomienda usar un URI de identificador de secreto sin versión para permitir que las instancias recuperen una versión renovada del certificado, si existe.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Recuperación de un certificado eliminado en Key Vault.":::
</br></br>


[comment]: # (Código de error 4)
#### <a name="4-error-code-userassignedmanagedidentitynotfound"></a>**4) Código de error:** UserAssignedManagedIdentityNotFound 

**Descripción:** la identidad administrada asignada por el usuario se ha eliminado. 

**Resolución:** siga la guía a continuación para resolver este problema.
1. Vuelva a crear una identidad administrada con el mismo nombre que se usó anteriormente y en el mismo grupo de recursos. Puede consultar los registros de actividad de recursos para obtener más información. 
2. Una vez creada, asigne a esa nueva identidad administrada el rol Lector, como mínimo, en Application Gateway - Access Control (IAM).
3. Por último, vaya al recurso de Key Vault que desee y establezca sus directivas de acceso para conceder permisos de secreto obtener para esta nueva identidad administrada. 

[Más información](./key-vault-certs.md#how-integration-works)
</br></br>

[comment]: # (Código de error 5)
#### <a name="5-error-code-keyvaulthasrestrictedaccess"></a>**5) Código de error:** KeyVaultHasRestrictedAccess

**Descripción:** configuración de red restringida para Key Vault. 

**Resolución:** esta incidencia se produce al habilitar el firewall de Key Vault para el acceso restringido. Todavía puede configurar Application Gateway en una red restringida de Key Vault de la siguiente manera.
1. Vaya a la hoja Redes de Key Vault.
2. Elija el punto de conexión privado y las redes seleccionadas en la pestaña "Firewall y redes virtuales".
3. Finalmente, seleccione "Sí" para permitir que los servicios de confianza omitan el firewall de Key Vault.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Key Vault ha restringido el acceso.":::
</br></br>


[comment]: # (Código de error 6)
#### <a name="6-error-code-keyvaultsoftdeleted"></a>**6) Código de error:** KeyVaultSoftDeleted 

**Descripción:** la instancia de Key Vault asociada se encuentra en estado de eliminación temporal. 

**Resolución:** recuperar una instancia de Key Vault eliminada temporalmente es bastante sencillo. En Azure Portal, vaya a la página del servicio Key Vault.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Búsqueda del servicio Key Vault.":::
</br></br>
Haga clic en la pestaña Almacenes eliminados administrados. Desde aquí, puede encontrar el recurso de Key Vault y recuperarlo.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="Recupere una instancia de Key Vault eliminada mediante la eliminación temporal.":::
</br></br>


[comment]: # (Código de error 7)
#### <a name="7-error-code-customerkeyvaultsubscriptiondisabled"></a>**7) Código de error:** CustomerKeyVaultSubscriptionDisabled 

**Descripción:** la suscripción para Key Vault está deshabilitada. 

**Resolución:** la suscripción de Azure puede deshabilitarse por diversos motivos. Consulte la guía para [Reactivación de una suscripción deshabilitada de Azure](../cost-management-billing/manage/subscription-disabled.md) y lleve a cabo las acciones necesarias.
</br></br>



