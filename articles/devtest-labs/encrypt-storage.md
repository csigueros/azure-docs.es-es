---
title: Cifrado de una cuenta de almacenamiento de Azure usada por un laboratorio
description: Aprenda a configurar el cifrado de un almacenamiento de Azure usado por un laboratorio en Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 337cd45b20b9fe52bb487a3b36bde905541f840b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552592"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Cifrado de un almacenamiento de Azure usado por un laboratorio en Azure DevTest Labs
Cada laboratorio creado en Azure DevTest Labs se crea con una cuenta de almacenamiento de Azure asociada. La cuenta de almacenamiento se utiliza para lo siguiente: 

- Almacenar documentos de [fórmula](devtest-lab-manage-formulas.md) que se pueden utilizar para crear máquinas virtuales.
- Almacenar resultados de artefacto que incluyen registros de implementación y extensión generados al aplicar artefactos. 
- [Cargar discos duros virtuales (VHD) para crear imágenes personalizadas en el laboratorio.](devtest-lab-create-template.md)
- Almacenar en caché los [artefactos](add-artifact-vm.md) usados con frecuencia y las [plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md) para una recuperación más rápida durante la creación de máquinas virtuales o entornos.

> [!NOTE]
> La información anterior es fundamental para que el laboratorio funcione. Se almacena durante la vida del laboratorio (y los recursos de laboratorio) a menos que se elimine explícitamente. La eliminación manual de estos recursos puede provocar errores en la creación de máquinas virtuales de laboratorio y/o que las fórmulas se dañen para un uso futuro. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Localización de la cuenta de almacenamiento y visualización de su contenido

1. En la página principal del laboratorio, seleccione el **grupo de recursos** en la página **Información general**. Debería ver la página **Grupo de recursos** del grupo de recursos que contiene el laboratorio. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Selección del grupo de recursos en la página Información general":::
1. Seleccione la cuenta de almacenamiento de Azure del laboratorio. La convención de nomenclatura para la cuenta de almacenamiento de laboratorio es: `a<labNameWithoutInvalidCharacters><4-digit number>`. Por ejemplo, si el nombre del laboratorio es `contosolab`, el nombre de la cuenta de almacenamiento podría ser `acontosolab7576`. 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Selección de la cuenta de almacenamiento en el grupo de recursos del laboratorio":::
3. En la página **Cuenta de almacenamiento**, seleccione **Explorador de Storage (vista previa)** en el menú de la izquierda y luego seleccione **CONTENEDORES DE BLOB** para buscar el contenido relacionado con el laboratorio pertinente. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Explorador de Storage (versión preliminar)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Cifrado de la cuenta de almacenamiento de laboratorio
Azure Storage cifra automáticamente los datos al guardarlos en la nube. El cifrado de Azure Storage protege los datos y le ayuda a satisfacer los requisitos de cumplimiento normativo y seguridad de la organización. Para más información, consulte [Cifrado de Azure Storage para datos en reposo](../storage/common/storage-service-encryption.md).

Los datos de una cuenta de almacenamiento de laboratorio se cifran con una **clave administrada por Microsoft**. Puede confiar en las claves administradas por Microsoft para el cifrado de los datos, o puede administrar el cifrado con sus propias claves. Si decide administrar el cifrado con sus propias claves para la cuenta de almacenamiento del laboratorio, puede especificar una **clave administrada por el cliente** con Azure Key Vault que se usará para cifrar y descifrar los datos en el almacenamiento de blobs y en Azure Files. Para más información sobre las claves administradas por el cliente, consulte [Uso de claves administradas por el cliente con Azure Key Vault para administrar el cifrado de Azure Storage](../storage/common/customer-managed-keys-overview.md).

Para aprender a configurar claves administradas por el cliente para el cifrado de Azure Storage, consulte los siguientes artículos: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [CLI de Azure](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Administración del ciclo de vida de Azure Blob Storage
Como ya se mencionó, la información almacenada en la cuenta de almacenamiento del laboratorio es fundamental para que el laboratorio funcione sin errores. A menos que se eliminen explícitamente, estos datos seguirán estando en la cuenta de almacenamiento del laboratorio durante la vida de máquinas virtuales de laboratorio específicas, en función del tipo de los datos.

### <a name="uploaded-vhds"></a>VHD cargados
Estos VHD se usan para crear imágenes personalizadas. Al quitarlos, ya no será posible crear imágenes personalizadas a partir de estos VHD.

### <a name="artifacts-cache"></a>Caché de artefactos
Estas memorias caché se volverán a crear cada vez que se apliquen artefactos. Se actualizarán con el contenido más reciente de los respectivos repositorios a los que se hace referencia. Por lo tanto, si elimina esta información para guardar los gastos relacionados con el almacenamiento, el alivio será temporal.

### <a name="azure-resource-manager-template-cache"></a>Caché de plantilla de Azure Resource Manager
Estas memorias caché se volverán a crear cada vez que los repositorios de plantilla basados en Azure Resource Manager se conecten y activen en el laboratorio. Se actualizarán con el contenido más reciente de los respectivos repositorios a los que se hace referencia. Por lo tanto, si elimina esta información para guardar los gastos relacionados con el almacenamiento, el alivio será temporal.

### <a name="formulas"></a>Fórmulas
Estos documentos se usan para admitir la opción para crear fórmulas a partir de máquinas virtuales existentes y para crear máquinas virtuales a partir de fórmulas. La eliminación de estos documentos de fórmula puede provocar errores mientras se realizan las operaciones siguientes:

- Creación de una fórmula a partir de una máquina virtual de laboratorio existente
- Creación o actualización de fórmulas 
- Creación de una máquina virtual a partir de una fórmula.

### <a name="artifact-results"></a>Resultados de artefacto
A medida que se aplican los artefactos, el tamaño de los resultados de artefacto respectivos puede aumentar con el tiempo en función del número y el tipo de artefactos que se ejecutan en las máquinas virtuales de laboratorio. Por lo tanto, como propietario de un laboratorio, puede que desee controlar el ciclo de vida de tales documentos. Para más información, consulte [Administración del ciclo de vida de Azure Blob Storage](../storage/blobs/lifecycle-management-overview.md).

> [!IMPORTANT]
> Se recomienda que realice este paso para reducir los gastos asociados con la cuenta de Azure Storage. 

Por ejemplo, la siguiente regla se usa para establecer una regla de expiración de noventa días específicamente para los resultados de artefacto. Garantiza que los resultados de artefacto anteriores se reciclan de la cuenta de almacenamiento con regularidad.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes
Para aprender a configurar claves administradas por el cliente para el cifrado de Azure Storage, consulte los siguientes artículos: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [CLI de Azure](../storage/common/customer-managed-keys-configure-key-vault.md)