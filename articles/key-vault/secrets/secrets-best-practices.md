---
title: 'Procedimientos recomendados para la administración de secretos: Azure Key Vault | Microsoft Docs'
description: Más información sobre los procedimientos recomendados para la administración de secretos de Azure Key Vault.
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 4735fcfd28830c8469ddfbed7b521a507b3ca521
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534672"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Procedimientos recomendados para la administración de secretos en Key Vault

Azure Key Vault permite almacenar de forma segura credenciales de servicio o de aplicación como contraseñas y claves de acceso como secretos. Todos los secretos del almacén de claves se cifran con una clave de software. Cuando se usa Key Vault, ya no es necesario almacenar información de seguridad en las aplicaciones. No tener que almacenar información de seguridad en las aplicaciones elimina la necesidad de que esta información sea parte del código.

Ejemplos de secretos que se deben almacenar en Key Vault:

- Secretos de aplicación cliente
- Cadenas de conexión
- Contraseñas
- Claves de acceso (Redis Cache, Azure Event Hubs, Azure Cosmos DB)
- Claves SSH

Cualquier otra información confidencial, como las direcciones IP, los nombres de servicio y otras opciones de configuración, debe almacenarse en [Azure App Configuration](../../azure-app-configuration/overview.md) en lugar de en Key Vault.

Cada almacén de claves individual define los límites de seguridad de los secretos. En el caso de un único almacén de claves por aplicación, por región y por entorno, se recomienda proporcionar aislamiento granular de secretos en la aplicación.

Para más información sobre los procedimientos recomendados para Key Vault, consulte [Procedimientos recomendados para utilizar Key Vault](../general/best-practices.md).

## <a name="configuration-and-storing"></a>Configuración y almacenamiento 

Almacene las partes dinámicas de las credenciales, que se generan durante la rotación, como valores. Algunos ejemplos son los secretos, las contraseñas y las claves de acceso de las aplicaciones cliente. Los identificadores y atributos estáticos relacionados, como los nombres de usuario, los identificadores de aplicación y las direcciones URL de servicios, deben almacenarse como etiquetas de secretos y copiarse en la nueva versión de un secreto durante la rotación.

Para más información sobre los secretos, consulte [Acerca de los secretos de Azure Key Vault](about-secrets.md).

## <a name="secrets-rotation"></a>Cambio de secretos
Los secretos se almacenan a menudo en la memoria de la aplicación como variables de entorno o valores de configuración durante todo el ciclo de vida de la aplicación, lo que los hace vulnerables a una exposición no deseada. Dado que los secretos son susceptibles a la pérdida de datos o a la exposición, es importante rotarlos a menudo, como mínimo cada 60 días.

Para más información sobre el proceso de rotación de secretos, consulte [Automatización de la rotación de un secreto para recursos que tienen dos conjuntos de credenciales de autenticación](tutorial-rotation-dual.md). 

## <a name="access-and-network-isolation"></a>Acceso y aislamiento de red

Puede reducir la exposición de los almacenes si especifica las direcciones IP que tienen acceso a ellos. Configure el firewall para permitir que solo la aplicación y los servicios relacionados puedan acceder a los secretos del almacén, a fin de reducir la posibilidad de que los atacantes puedan acceder también.

Para más información sobre la seguridad de red, consulte [Configuración de las redes de Azure Key Vault](../general/how-to-azure-key-vault-network-security.md).

Asimismo, las aplicaciones deben seguir el acceso con privilegios mínimos, con acceso solo a la lectura de secretos. El acceso a los secretos se puede controlar con directivas de acceso o con el control de acceso basado en rol de Azure. 

Para obtener más información sobre el control de acceso en Azure Key Vault, consulte los documentos siguientes:
- [Acceso a las claves, los certificados y los secretos de Key Vault con un control de acceso basado en rol de Azure](../general/rbac-guide.md)
- [Asignación de una directiva de acceso de Key Vault](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>Límites del servicio y almacenamiento en caché
Key Vault se creó originalmente con los límites especificados en [Límites del servicio Azure Key Vault](../general/service-limits.md). Para maximizar las tasas de rendimiento, estos son dos procedimientos recomendados:
- Almacenar en caché los secretos de la aplicación durante al menos ocho horas.
- Implementar la lógica de reintento de retroceso exponencial para controlar los escenarios en los que se superan los límites del servicio.

Para más información sobre la guía de limitaciones, consulte [Guía de las limitaciones de Azure Key Vault](../general/overview-throttling.md).

## <a name="monitoring"></a>Supervisión
Para supervisar el acceso a los secretos y su ciclo de vida, active el registro de Key Vault. Use [Azure Monitor](../../azure-monitor/overview.md) para supervisar todas las actividades de secretos de todos los almacenes en un solo lugar. También puede usar [Azure Event Grid](../../event-grid/overview.md) para supervisar el ciclo de vida de los secretos, ya que se integra fácilmente con Azure Logic Apps y Azure Functions.

Para más información, consulte:
- [Azure Key Vault como origen de Event Grid](../../event-grid/event-schema-key-vault.md?tabs=event-grid-event-schema.md)
- [Registro de Azure Key Vault](../general/logging.md)
- [Supervisión y alertas de Azure Key Vault](../general/alert.md)

## <a name="backup-and-purge-protection"></a>Copia de seguridad y protección de purga
Active la [protección de purga](../general/soft-delete-overview.md#purge-protection) para protegerse frente a la eliminación forzada del secreto. Realice copias de seguridad periódicas del almacén al actualizar, eliminar o crear secretos dentro de él.

- Para información sobre el comando de copia de seguridad de Azure PowerShell, consulte [Secreto de copia de seguridad](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret).
- Para información sobre el comando de copia de seguridad de la CLI de Azure, consulte [Secreto de copia de seguridad](/cli/azure/keyvault/secret#az_keyvault_secret_backup).

## <a name="learn-more"></a>Más información
- [Acerca de los secretos de Azure Key Vault](about-secrets.md)
- [Procedimientos recomendados para utilizar Key Vault](../general/best-practices.md)
