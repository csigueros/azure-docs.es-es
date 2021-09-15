---
title: Creación de una cuenta en Azure Portal
description: Aprenda a crear una cuenta de Azure Batch en Azure Portal para ejecutar cargas de trabajo en paralelo a gran escala en la nube.
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 037ada644f60eabf498c59047513f4ad8292f239
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123308702"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creación de una cuenta de Batch con Azure Portal

En este tema se muestra cómo crear una [cuenta de Azure Batch](accounts.md) en [Azure Portal](https://portal.azure.com) y cómo seleccionar las propiedades de cuenta más adecuadas para el escenario de proceso. También aprenderá dónde encontrar importantes propiedades de cuenta, como teclas de acceso y direcciones URL de la cuenta.

Para obtener información sobre los escenarios y las cuentas de Batch, vea [Flujo de trabajo y recursos del servicio Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Crear una cuenta de Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En la página principal, seleccione **Crear un recurso**.

1. En el cuadro de búsqueda escriba **Batch Service**. Seleccione **Batch Service** en los resultados y luego **Crear**.

1. Especifique los siguientes detalles.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Captura de la pantalla Nueva cuenta de Batch.":::

    a. **Suscripción**: en la que se crea la cuenta de Batch. Si tiene una sola suscripción, se selecciona de forma predeterminada.

    b. **Grupo de recursos**: seleccione un grupo de recursos existente para la nueva cuenta de Batch (también se puede crear uno nuevo).

    c. **Nombre de cuenta**: el nombre que elija debe ser único en la región de Azure en la que se crea cuenta (consulte **Ubicación** a continuación). El nombre de la cuenta solo puede contener caracteres en minúsculas o números, y su longitud debe oscilar entre 3 y 24 caracteres.

    d. **Ubicación**: región de Azure en la que se va a crear la cuenta de Batch. Solo se muestran como opciones las regiones admitidas por su suscripción y grupo de recursos.

    e. **Cuenta de almacenamiento**: [cuenta de Azure Storage](accounts.md#azure-storage-accounts) opcional que se asocia a la cuenta de Batch. Se puede seleccionar una existente o crear una nueva. Para que el rendimiento sea óptimo, se recomienda una cuenta de almacenamiento de uso general v2.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Captura de pantalla de las opciones para crear una cuenta de almacenamiento.":::

1. Si quiere, seleccione **Opciones avanzadas** para especificar **Tipo de identidad**, **Acceso de red pública** o **Modo de asignación de grupo**. En la mayoría de los escenarios basta con las opciones predeterminadas.

1. Seleccione **Revisar y crear** y luego **Crear** para crear la cuenta.

## <a name="view-batch-account-properties"></a>Visualización de propiedades de la cuenta de Batch

Una vez creada la cuenta, selecciónela para acceder a su configuración y propiedades. Con las opciones del menú izquierdo puede acceder a todas las propiedades y a la configuración de la cuenta.

> [!NOTE]
> El nombre de la cuenta de Batch es su identificador y no se puede cambiar. Si necesita cambiar el nombre de una cuenta de Batch, debe eliminar la cuenta y crear una nueva con el nombre que quiera.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Captura de pantalla de la página de la cuenta de Batch en Azure Portal.":::

al desarrollar una aplicación con las [API de Batch](batch-apis-tools.md#azure-accounts-for-batch-development), se necesita una dirección URL de la cuenta y una clave para acceder a los recursos de Batch. (Batch también admite la autenticación de Azure Active Directory.) Para ver la información de acceso de la cuenta de Batch, seleccione **Claves**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Captura de pantalla de las claves de la cuenta de Batch en Azure Portal.":::

Para ver el nombre y las claves de la cuenta de almacenamiento asociada con la cuenta de Batch, seleccione **Cuenta de almacenamiento**.

Para ver las [cuotas de recursos](batch-quota-limit.md) que se aplican a la cuenta de Batch, seleccione **Cuotas**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuración adicional para el modo de suscripción de usuario

Si decide crear una cuenta de Batch en el modo de suscripción de usuario, antes, realice los siguientes pasos adicionales.

> [!IMPORTANT]
> El usuario que crea la cuenta de Batch en modo de suscripción de usuario debe tener asignado el rol de colaborador o propietario para la suscripción en la que se creará la cuenta de Batch.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Procedimiento para permitir que Azure Batch acceda a la suscripción (operación única)

Al crear la primera cuenta de Batch en el modo de suscripción de usuario, tiene que registrar la suscripción con Batch. (si ya lo ha hecho, vaya a la sección siguiente).

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Todos los servicios** > **Suscripciones** y seleccione la suscripción que desea usar para la cuenta de Batch.

1. En la página **Suscripción**, seleccione **Proveedores de recursos** y busque **Microsoft.Batch**. Compruebe que el proveedor de recursos **Microsoft.Batch** está registrado en la suscripción. Si no es así, seleccione el vínculo **Registrar** situado junto a la parte superior de la pantalla.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Captura de pantalla que muestra el proveedor de recursos Microsoft.Batch.":::

1. Vuelva a la página **Suscripción** y, a continuación, seleccione **Control de acceso (IAM)** .

1. Asigne a Batch API el rol **Colaborador** o **Propietario**. Para localizar esta cuenta, busque **Microsoft Azure Batch**. El identificador de aplicación de esta cuenta es **ddbf3205-c6bd-46ae-8127-60eb93363864**.

   Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="create-a-key-vault"></a>Creación de un almacén de claves

En el modo de suscripción de usuario, se requiere una instancia de [Azure Key Vault](../key-vault/general/overview.md). Key Vault debe estar en la misma región y suscripción que la cuenta de Batch que se va a crear.

1. En la página principal de [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **Key Vault**. Seleccione **Key Vault** en los resultados y luego **Crear**.
1. En la página **Crear almacén de claves**, escriba un nombre para el almacén de claves y cree un nuevo grupo de recursos en la región que quiere para la cuenta de Batch. Deje los valores predeterminados para el resto de la configuración y, a continuación, seleccione **Crear**.

Al crear la cuenta de Batch en el modo de suscripción de usuario, especifique **Suscripción de usuario** como modo de asignación de grupo, seleccione el almacén de claves que ha creado y active la casilla para conceder acceso a Azure Batch a él.

Si prefiere conceder acceso al almacén de claves manualmente, vaya a la sección **Directivas de acceso** de dicho almacén y seleccione **Agregar directiva de acceso**. Seleccione el vínculo situado junto a **Seleccionar la entidad de seguridad** y busque **Microsoft Azure Batch** (id. de aplicación **ddbf3205-c6bd-46ae-8127-60eb93363864**). Seleccione esa entidad de seguridad y luego configure los **Permisos de secretos** mediante el menú desplegable. A Azure Batch se le deben conceder los permisos mínimos **Get**, **List**, **Set** y **Delete**. En el caso de [almacenes de claves con la eliminación temporal habilitada](../key-vault/general/soft-delete-overview.md), también se le debe conceder el permiso de **recuperación** a Azure Batch.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Captura de pantalla de las selecciones de Permisos de secretos de Azure Batch":::

Seleccione **Agregar** y luego asegúrese de que las casillas **Azure Virtual Machines para la implementación** y **Azure Resource Manager para la implementación de plantillas** del recurso vinculado **Key Vault** están activadas. Seleccione **Guardar** para confirmar los cambios.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Captura de la pantalla Directiva de acceso.":::

### <a name="configure-subscription-quotas"></a>Configuración de cuotas de suscripción

En el caso de las cuentas de Batch de suscripción de usuario, las [cuotas de núcleos](batch-quota-limit.md) deben establecerse manualmente. Las cuotas de núcleos de Batch estándar no se aplican a las cuentas de modo de suscripción de usuario, y sí se usan y aplican las [cuotas de la suscripción](../azure-resource-manager/management/azure-subscription-service-limits.md) para los núcleos de proceso regionales, los núcleos de proceso por serie y otros recursos.

1. En [Azure Portal](https://portal.azure.com), seleccione la cuenta de Batch del modo de suscripción del usuario para mostrar sus propiedades y configuración.
1. En el menú izquierdo, seleccione **Cuotas** para ver y configurar las cuotas de núcleos asociadas a su cuenta de Batch.

## <a name="other-batch-account-management-options"></a>Otras opciones de administración de la cuenta de Batch

Además de usar Azure Portal, las cuentas de Batch se pueden crear y administrar con las siguientes herramientas:

- [Cmdlets de PowerShell de Batch](batch-powershell-cmdlets-get-started.md)
- [CLI de Azure](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre el [Flujo de trabajo y recursos del servicio Batch](batch-service-workflow-features.md), como grupos, nodos, trabajos y tareas.
- Para conocer los aspectos básicos del desarrollo de una aplicación habilitada para Batch, consulte la [biblioteca de cliente de Batch para .NET](quick-run-dotnet.md) o [Python](quick-run-python.md). Estos inicios rápidos le guían con una aplicación de ejemplo que usa el servicio Batch para ejecutar una carga de trabajo en varios nodos de proceso y Azure Storage para el almacenamiento provisional y la recuperación de archivos de la carga de trabajo.