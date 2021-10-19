---
title: Guía del desarrollador de Azure Key Vault
description: Los desarrolladores pueden usar Azure Key Vault para administrar las claves criptográficas en el entorno de Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c1b4b6437b14039284fb938405fa7069b0e239c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667561"
---
# <a name="azure-key-vault-developers-guide"></a>Guía del desarrollador de Azure Key Vault

Azure Key Vault le permite acceder de forma segura a información confidencial desde sus aplicaciones:

- Las claves, los secretos y los certificados estarán protegidos sin que tenga que escribir el código manualmente, y puede usar estos elementos fácilmente en sus aplicaciones.
- Permite a los clientes poseer y administrar sus propios secretos, claves y certificados, para poder así concentrarse en proporcionar las características de software principales. De este modo, las aplicaciones no serán responsables de las claves, secretos y certificados del inquilino de sus clientes.
- La aplicación puede usar claves para la firma y el cifrado, pero también mantiene la opción de administrar de forma externa las claves de la aplicación. Para más información, consulte [Acerca de las claves](../keys/about-keys.md).
- Puede administrar credenciales, como contraseñas, claves de acceso y tokens de SAS almacenándolas en Key Vault como secretos. Para más información, consulte [Acerca de los secretos](../secrets/about-secrets.md).
- Administrar certificados. Para más información, consulte [Acerca de los certificados](../certificates/about-certificates.md).

Para obtener información general sobre Azure Key Vault, consulte [Acerca de Azure Key Vault](overview.md).

## <a name="public-previews"></a>Versiones preliminares públicas

Periódicamente se publica una versión preliminar pública de una nueva característica de Key Vault. Pruebe las características de la versión preliminar públicas y díganos lo que piensa a través de nuestra dirección de correo electrónico para enviar comentarios, azurekeyvault@microsoft.com.

## <a name="create-and-manage-key-vaults"></a>Creación y administración de almacenes de claves

Igual que sucede con otros servicios de Azure, Key Vault se administra mediante [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Azure Resource Manager es el servicio de implementación y administración para Azure. Puede usarlo para crear, actualizar y eliminar recursos en su cuenta de Azure. 

El [control de acceso basado en roles (RBAC) de Azure](../../role-based-access-control/overview.md) controla el acceso a la capa de administración, también conocida como [plano de administración](security-features.md#managing-administrative-access-to-key-vault). El plano de administración se usa en Key Vault para crear y administrar almacenes de claves y sus atributos, incluidas las directivas de acceso. Puede usar el *plano de datos* para administrar claves, certificados y secretos. 

Asimismo, puede usar el rol de colaborador de Key Vault predefinido para conceder acceso de administración a Key Vault.     

### <a name="apis-and-sdks-for-key-vault-management"></a>API y SDK para la administración del almacén de claves

| Azure CLI | PowerShell | API DE REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault)<br>[Guía de inicio rápido](quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Guía de inicio rápido](quick-create-powershell.md)|[Referencia](/rest/api/keyvault/)|[Referencia](/azure/templates/microsoft.keyvault/vaults)<br>[Guía de inicio rápido](./vault-create-template.md)|[Referencia](/dotnet/api/microsoft.azure.management.keyvault)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referencia](/java/api/com.microsoft.azure.management.keyvault)|[Referencia](/javascript/api/@azure/arm-keyvault)|

Consulte las [Bibliotecas de cliente](client-libraries.md) para ver los paquetes de instalación y el código fuente.

## <a name="authenticate-to-key-vault-in-code"></a>Autenticación en Key Vault mediante el código

Key Vault usa la autenticación de Azure Active Directory (Azure AD), que requiere una entidad de seguridad de Azure AD para conceder acceso. Una entidad de seguridad de Azure AD puede ser un usuario, una entidad de servicio de aplicación, una [identidad administrada para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md) o un grupo de cualquier tipo.

### <a name="authentication-best-practices"></a>Procedimientos recomendados de autenticación

Se recomienda usar la identidad administrada para las aplicaciones implementadas en Azure. Si usa servicios de Azure que no admitan identidades administradas o si las aplicaciones se implementan de forma local, la [entidad de servicio con un certificado](../../active-directory/develop/howto-create-service-principal-portal.md) es una alternativa posible. En ese escenario, el certificado se debe almacenar en Key Vault y se debe rotar a menudo.

Use una entidad de servicio con un secreto para entornos de desarrollo y pruebas. Use una entidad de seguridad de usuario para el desarrollo local y Azure Cloud Shell.

Se recomiendan estas entidades de seguridad en cada entorno:
- **Entorno de producción**: identidad administrada o entidad de servicio con un certificado.
- **Entornos de desarrollo y pruebas**: identidad administrada, entidad de servicio con un certificado o entidad de servicio con un secreto.
- **Desarrollo local**: entidad de seguridad de usuario o entidad de servicio con un secreto.

### <a name="azure-identity-client-libraries"></a>Bibliotecas cliente de Azure Identity

Los escenarios de autenticación anteriores son compatibles con la *biblioteca cliente de Azure Identity* y se integran con los SDK de Key Vault. Puede usar la biblioteca cliente de Azure Identity en entornos y plataformas sin cambiar el código. La biblioteca recupera automáticamente los tokens de autenticación de los usuarios que han iniciado sesión en el usuario de Azure a través de la CLI de Azure, Visual Studio, Visual Studio Code y otros medios. 

Para obtener más información acerca de la biblioteca cliente de Azure Identity, consulte:

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[SDK .NET de Azure Identity](/dotnet/api/overview/azure/identity-readme)|[SDK Python de Azure Identity](/python/api/overview/azure/identity-readme)|[SDK Java de Azure Identity](/java/api/overview/azure/identity-readme)|[SDK JavaScript de Azure Identity](/javascript/api/overview/azure/identity-readme)|     

> [!Note]
> Se recomienda la [biblioteca de autenticación de aplicaciones](/dotnet/api/overview/azure/service-to-service-authentication) para la versión 3 del SDK de .NET de Key Vault, pero ahora está en desuso. Para migrar al SDK de .NET de Key Vault, versión 4, siga las [instrucciones de migración de AppAuthentication a Azure.Identity](/dotnet/api/overview/azure/app-auth-migration).

Para ver tutoriales sobre la autenticación en Key Vault en las aplicaciones, consulte:
- [Uso de Azure Key Vault con una máquina virtual en .NET](./tutorial-net-virtual-machine.md)
- [Uso de Azure Key Vault con una máquina virtual en Python](./tutorial-python-virtual-machine.md)
- [Tutorial: Uso de una identidad administrada para conectar Key Vault a una aplicación web de Azure en .NET](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Administración de claves, certificados y secretos

El plano de datos controla el acceso a claves, certificados y secretos. Puede usar directivas de acceso del almacén local o RBAC de Azure para el control de acceso a través del plano de datos.

### <a name="apis-and-sdks-for-keys"></a>API y SDK para claves

| Azure CLI | PowerShell | API DE REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/key)<br>[Guía de inicio rápido](../keys/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Guía de inicio rápido](../keys/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#key-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Guía de inicio rápido](../keys/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.keys)<br>[Guía de inicio rápido](../keys/quick-create-net.md)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Guía de inicio rápido](../keys/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Guía de inicio rápido](../keys/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-keys/)<br>[Guía de inicio rápido](../keys/quick-create-node.md)|

### <a name="apis-and-sdks-for-certificates"></a>API y SDK para certificados

| Azure CLI | PowerShell | API DE REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/certificate)<br>[Guía de inicio rápido](../certificates/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Guía de inicio rápido](../certificates/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#certificate-operations)|N/D|[Referencia](/dotnet/api/azure.security.keyvault.certificates)<br>[Guía de inicio rápido](../certificates/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-certificates-readme)<br>[Guía de inicio rápido](../certificates/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Guía de inicio rápido](../certificates/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-certificates/)<br>[Guía de inicio rápido](../certificates/quick-create-node.md)|

### <a name="apis-and-sdks-for-secrets"></a>API y SDK para secretos

| Azure CLI | PowerShell | API DE REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/secret)<br>[Guía de inicio rápido](../secrets/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Guía de inicio rápido](../secrets/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#secret-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Guía de inicio rápido](../secrets/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.secrets)<br>[Guía de inicio rápido](../secrets/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-secrets-readme)<br>[Guía de inicio rápido](../secrets/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Guía de inicio rápido](../secrets/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-secrets/)<br>[Guía de inicio rápido](../secrets/quick-create-node.md)|

### <a name="usage-of-secrets"></a>Uso de secretos
Use Azure Key Vault para almacenar solo secretos para su aplicación. Los ejemplos de secretos que se deben almacenar en Key Vault incluyen:

- Secretos de aplicación cliente
- Cadenas de conexión
- Contraseñas
- Claves de acceso compartidas
- Claves SSH

Asimismo, cualquier información relacionada con los secretos, como los nombres de usuario o los id. de aplicaciones, se puede almacenar como una etiqueta en el secreto. Para realizar cualquier otra configuración de información confidencial, debe usar [Azure App Configuration](../../azure-app-configuration/overview.md).
 
### <a name="references"></a>Referencias 

Consulte las [Bibliotecas de cliente](client-libraries.md) para ver los paquetes de instalación y el código fuente.

Para obtener más información sobre la seguridad del plano de datos de Key Vault, consulte [Características de seguridad de Azure Key Vault](security-features.md).

## <a name="use-key-vault-in-applications"></a>Uso de Key Vault en aplicaciones

Para aprovechar las características más recientes de Key Vault, se recomienda usar los SDK de Key Vault disponibles para usar secretos, certificados y claves en la aplicación. Los SDK de Key Vault y la API de REST se actualizan a medida que se presentan nuevas características para el producto; recuerde que siguen los procedimientos recomendados y las instrucciones.

En escenarios básicos, existen otras bibliotecas y soluciones de integración para un uso simplificado, con soporte proporcionado por asociados de Microsoft o comunidades de código abierto.

Para los certificados, puede usar:

- La extensión de máquina virtual (VM) de Key Vault, que proporciona la actualización automática de los certificados almacenados en una instancia de Azure Key Vault. Para más información, consulte: 
  - [Extensión de máquina virtual de Key Vault para Windows](../../virtual-machines/extensions/key-vault-windows.md)
  - [Extensión de máquina virtual de Key Vault para Linux](../../virtual-machines/extensions/key-vault-linux.md)
  - [Extensión de máquina virtual de Key Vault para servidores habilitados para Azure Arc](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
- Integración con Azure App Service, que puede importar y actualizar automáticamente certificados desde Key Vault. Para obtener más información, consulte [Importación de un certificado desde Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault).

Para los secretos, puede usar:

- Secretos de Key Vault con la configuración de la aplicación de App Service. Para más información, consulte [Uso de referencias de Key Vault para App Service y Azure Functions](../../app-service/app-service-key-vault-references.md).
- Secretos de Key Vault con el servicio App Configuration para aplicaciones hospedadas en VM de Azure. Para obtener más información, consulte [Configuración de aplicaciones con App Configuration y Key Vault](/samples/azure/azure-sdk-for-net/app-secrets-configuration/).

## <a name="code-examples"></a>Ejemplos de código

Para obtener ejemplos completos del uso de Key Vault con aplicaciones, consulte [Ejemplos de código de Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault). 

## <a name="task-specific-guidance"></a>Guía específica de tareas

Los artículos y escenarios siguientes proporcionan instrucciones específicas de tarea sobre cómo trabajar con Azure Key Vault:

- Para acceder al almacén de claves, es preciso que la aplicación cliente pueda acceder a varios puntos de conexión para diversas funcionalidades. Consulte [Acceso a Key Vault detrás de un firewall](access-behind-firewall.md). 
- Una aplicación en la nube que se ejecuta en una VM de Azure necesita un certificado. ¿Cómo se obtiene este certificado en esta VM? Consulte [Extensión de máquina virtual de Key Vault para Windows](../../virtual-machines/extensions/key-vault-windows.md) o [Extensión de máquina virtual de Key Vault para Linux](../../virtual-machines/extensions/key-vault-linux.md).
- Para asignar una directiva de acceso mediante el CLI de Azure, PowerShell o Azure Portal, consulte [Asignación de una directiva de acceso de Key Vault](assign-access-policy.md). 
- Para obtener instrucciones sobre el uso y ciclo de vida de un almacén de claves y varios objetos del almacén de claves con la opción de eliminación automática habilitada, consulte [Administración de recuperación de Azure Key Vault con eliminación automática y protección ante purgas](./key-vault-recovery.md).
- Cuando necesite pasar un valor seguro (como una contraseña) como parámetro durante la implementación, puede almacenar ese valor como secreto en un almacén de claves y hacer referencia al valor en otras plantillas de Resource Manager. Consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](../../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="integration-with-key-vault"></a>Integración con Key Vault

Los siguientes servicios y escenarios usan o se integran con Key Vault:

- El [cifrado en reposo](../../security/fundamentals/encryption-atrest.md) permite la codificación (cifrado) de datos cuando se conserva. Las claves de cifrado de datos se cifran a menudo con la clave de cifrado de claves en Azure Key Vault para limitar aún más el acceso.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) le permite administrar su propia clave de inquilino. Por ejemplo, en lugar de que Microsoft administre su clave de inquilino (la opción predeterminada), puede administrar su propia clave de inquilino para cumplir con las normas específicas que se aplican a su organización. Administrar su propia clave de inquilino también se denomina *bring your own key* (BYOK).
- [Azure Private Link](private-link-service.md) le permite acceder a los servicios de Azure (por ejemplo, Azure Key Vault, Azure Storage y Azure Cosmos DB) y a los servicios de asociados o clientes hospedados de Azure mediante un punto de conexión privado de la red virtual.
- La integración de Key Vault con [Azure Event Grid](../../event-grid/event-schema-key-vault.md) permite a los usuarios recibir una notificación cuando cambia el estado de un secreto almacenado en Key Vault. Puede distribuir la nueva versión de los secretos en las aplicaciones, o cambiar los secretos que estén a punto de expirar para evitar interrupciones.
- Puede proteger los secretos de [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) de accesos no deseados en Key Vault.
- Use secretos almacenados en Key Vault para [conectarse a Azure Storage desde Azure Databricks](./integrate-databricks-blob-storage.md).
- Configure y ejecute el proveedor de Azure Key Vault correspondiente al [controlador Secrets Store CSI](./key-vault-integrate-kubernetes.md) en Kubernetes. 

## <a name="key-vault-overviews-and-concepts"></a>Conceptos y datos globales de Key Vault

Para obtener información acerca de cómo:

- Usar una característica que permite la recuperación de objetos eliminados tanto si dicha eliminación ha sido accidental como intencionada, consulte [Comportamiento de eliminación temporal de Key Vault](soft-delete-overview.md).
- Los conceptos básicos de limitación y obtención de un enfoque para su aplicación, consulte la [Guía de limitación de Azure Key Vault](overview-throttling.md).
- Las relaciones entre regiones y áreas de seguridad, consulte los [Mundos de seguridad y límites geográficos de Azure Key Vault](overview-security-worlds.md).

## <a name="social"></a>Redes sociales

- [Blog de Key Vault](/archive/blogs/kv/)
- [Preguntas y respuestas de Microsoft](https://docs.microsoft.com/answers/products/)
- [Stack Overflow para Key Vault](https://stackoverflow.com/questions/tagged/azure-keyvault)
