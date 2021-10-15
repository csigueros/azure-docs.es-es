---
title: Errores de Key Vault comunes en Application Gateway
titleSuffix: Azure Application Gateway
description: En este artículo se identifican los problemas relacionados con el almacén de claves y se ofrecen soluciones para resolverlos y así poder realizar operaciones de Application Gateway sin problemas.
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: cfcacf84b5a35862c3ef1423ed58ff4d5d7f3142
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272327"
---
# <a name="common-key-vault-errors-in-azure-application-gateway"></a>Errores comunes del almacén de claves en Azure Application Gateway

En este artículo encontrará información para comprender los detalles de los códigos de error de la bóveda de claves que puede encontrar, incluida la causa de estos errores. Este artículo también contiene los pasos para resolver estos errores de configuración.

> [!TIP]
> Use un identificador secreto que no especifique una versión. De este modo, Azure Application Gateway cambiará automáticamente el certificado si hay una versión más reciente disponible en Azure Key Vault. Un ejemplo de un URI de secreto sin una versión es: `https://myvault.vault.azure.net/secrets/mysecret/`.

## <a name="list-of-error-codes-and-their-details"></a>Lista de códigos de error y sus detalles

En las secciones siguientes se indican varios errores que pueden producirse. Puede encontrar los detalles en Azure Advisor y usar este artículo de solución de problemas para solucionar esos errores. Para obtener más información, consulte [Creación de alertas de Azure Advisor para nuevas recomendaciones mediante Azure Portal](../advisor/advisor-alerts-portal.md).

> [!NOTE]
> Azure Application Gateway genera registros para el diagnóstico del almacén de claves cada cuatro horas. Si el diagnóstico continúa mostrando el error después de que haya corregido la configuración, es posible que deba esperar a que se actualicen los registros.

[comment]: # (Código de error 1)
### <a name="error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>Código de error: UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**Descripción:** la identidad administrada asignada por el usuario no tiene el permiso "Obtener". 

**Resolución:** configure la directiva de acceso de Key Vault para conceder a la identidad administrada asignada por el usuario este permiso para los secretos. 
1. Vaya al almacén de claves vinculado en Azure Portal.
1. Abra el panel **Directivas de acceso**.
1. Seleccione **Directiva de acceso al almacén** en **Modelo de permisos**.
1. En **Operaciones de administración de secretos**, seleccione el permiso **Obtener**.
1. Seleccione **Guardar**.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text=" Captura de pantalla que muestra cómo resolver el error de la opción Obtener permiso.":::

Para obtener más información, consulte [Asignación de una directiva de acceso de Key Vault mediante Azure Portal](../key-vault/general/assign-access-policy-portal.md).

[comment]: # (Código de error 2)
### <a name="error-code-secretdisabled"></a>Código de error: SecretDisabled 

**Descripción:** el certificado asociado se ha deshabilitado en Key Vault. 

**Resolución:** Vuelva a habilitar la versión del certificado que está actualmente en uso para Application Gateway.
1. Vaya al almacén de claves vinculado en Azure Portal.
1. Abra el panel **Certificados**.
1. Haga clic en el nombre del certificado necesario y luego en la versión deshabilitada.
1. Use el botón de alternancia de la página de administración para habilitar esa versión del certificado.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="Captura de pantalla que muestra cómo volver a habilitar un secreto.":::

[comment]: # (Código de error 3)
### <a name="error-code-secretdeletedfromkeyvault"></a>Código de error: SecretDeletedFromKeyVault 

**Descripción:** el certificado asociado se ha eliminado de Key Vault. 

**Resolución**: debe recuperar el certificado eliminado: 
1. Vaya al almacén de claves vinculado en Azure Portal.
1. Abra el panel **Certificados**.
1. Use la pestaña **Certificados eliminados administrados** para recuperar un certificado eliminado.

Por otro lado, si un objeto de certificado se elimina permanentemente, deberá crear un nuevo certificado y actualizar Application Gateway con los detalles del nuevo certificado. Al realizar una configuración mediante la CLI de Azure o Azure PowerShell, use un identificador URI secreto sin una versión. Esta opción permite a las instancias recuperar una versión renovada del certificado, si es que existe.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Captura de pantalla que muestra cómo recuperar un certificado eliminado en Key Vault.":::

[comment]: # (Código de error 4)
### <a name="error-code-userassignedmanagedidentitynotfound"></a>Código de error: UserAssignedManagedIdentityNotFound 

**Descripción:** la identidad administrada asignada por el usuario se ha eliminado. 

**Resolución:** para volver a usar la identidad:
1. Vuelva a crear una identidad administrada con el mismo nombre que se usó anteriormente y en el mismo grupo de recursos. Los registros de actividad de recursos contienen más detalles. 
1. Después de crear la identidad, vaya a **Application Gateway - Access Control (IAM)** . Asigne a la identidad la función **Lector**, como mínimo.
1. Por último, vaya al recurso de Key Vault que quiera y establezca sus directivas de acceso para conceder permisos de secreto **Obtener** para esta nueva identidad administrada. 

Para obtener más información, consulte [Funcionamiento de la integración](./key-vault-certs.md#how-integration-works).

[comment]: # (Código de error 5)
### <a name="error-code-keyvaulthasrestrictedaccess"></a>Código de error: KeyVaultHasRestrictedAccess

**Descripción:** hay una configuración de red restringida para Key Vault. 

**Resolución:** este error se produce al habilitar el firewall de Key Vault para el acceso restringido. Todavía puede configurar Application Gateway en una red restringida de Key Vault si sigue los siguientes pasos:
1. En Key Vault, abra el panel **Redes**.
1. En la pestaña **Firewalls y redes virtuales**, seleccione **Punto de conexión privado y redes seleccionadas**.
1. A continuación, use Virtual Network para agregar la red virtual y la subred de Application Gateway. Durante el proceso, configure también el punto de conexión de servicio "Microsoft.KeyVault" activando la casilla correspondiente.
1. Finalmente, seleccione **Sí** para permitir que los servicios de confianza omitan el firewall de Key Vault.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Captura de pantalla que muestra cómo evitar el error de red restringida.":::

[comment]: # (Código de error 6)
### <a name="error-code-keyvaultsoftdeleted"></a>Código de error: KeyVaultSoftDeleted 

**Descripción:** la instancia de Key Vault asociada se encuentra en estado de eliminación temporal. 

**Resolución:** busque *Almacén de claves* en Azure Portal. En **Servicios**, seleccione **Almacenes de claves**.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Captura de pantalla que muestra cómo buscar el servicio Key Vault.":::

Seleccione **Almacenes eliminados administrados**. En esta opción, puede encontrar el recurso de Key Vault y recuperarlo.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="Captura de pantalla que muestra cómo recuperar un almacén de claves eliminado.":::

[comment]: # (Código de error 7)
### <a name="error-code-customerkeyvaultsubscriptiondisabled"></a>Código de error: CustomerKeyVaultSubscriptionDisabled 

**Descripción:** la suscripción para Key Vault está deshabilitada. 

**Resolución:** la suscripción de Azure puede deshabilitarse por diversos motivos. Para realizar las acciones necesarias para resolver este error, consulte [Reactivación de una suscripción de Azure deshabilitada](../cost-management-billing/manage/subscription-disabled.md).

## <a name="next-steps"></a>Pasos siguientes

Estos artículos de solución de problemas pueden resultarle útiles a medida que use Application Gateway:

- [Información general sobre Resource Health de Azure Application Gateway](resource-health-overview.md)
- [Solución de problemas de afinidad de sesión en Azure Application Gateway](how-to-troubleshoot-application-gateway-session-affinity-issues.md)
