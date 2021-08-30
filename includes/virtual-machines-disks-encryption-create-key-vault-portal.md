---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3e05dd70ab0919ca4429cee15ecfc9d6ac9ee627
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122262414"
---
La configuración de claves administradas por el cliente para los discos requerirá la creación de recursos en un orden determinado, si lo hace por primera vez. En primer lugar, tendrá que crear y configurar una instancia de Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>Configuración de Azure Key Vault

1. Inicie sesión en el [Portal de Azure](https://aka.ms/diskencryptionupdates).
1. Busque y seleccione **Key Vaults**.

    [![Captura de pantalla de Azure Portal con el cuadro de diálogo de búsqueda expandido.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > La instancia de Azure Key Vault, el conjunto de cifrado de disco, la VM, los discos y las instantáneas deben estar en la misma región y suscripción para que la implementación se realice correctamente.

1. Seleccione **+Crear** para crear una instancia de Key Vault.
1. Cree un nuevo grupo de recursos.
1. Escriba un nombre de almacén de claves, seleccione una región y seleccione un plan de tarifa.

    > [!NOTE]
    > Al crear la instancia de Key Vault, debe habilitar la eliminación temporal y la protección de purgas. La eliminación temporal garantiza que la instancia de Key Vault conserva una clave eliminada durante un período de retención determinado (valor predeterminado de 90 días). La protección de purgas garantiza que una clave eliminada no se puede eliminar permanentemente hasta que transcurra el período de retención. Esta configuración le protege contra la pérdida de datos debido a la eliminación accidental. Estos valores son obligatorios cuando se usa una instancia de Key Vault para cifrar discos administrados.

1. Seleccione **Revisar y crear**, compruebe las opciones y, a continuación, seleccione **Crear**.

    ![Captura de pantalla de la experiencia de creación de Azure Key Vault. Muestra los valores concretos que ha creado](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Una vez que el almacén de claves termine de implementarse, selecciónelo.
1. En **Configuración**, seleccione **Claves**.
1. Seleccione **Generar o importar**.

    ![Captura de pantalla del panel de configuración de recursos de Key Vault. Muestra el botón Generar/importar en la configuración.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Deje **Tipo de clave** establecido en **RSA** y **Tamaño de la clave RSA** establecido en **2048**.
1. Rellene las selecciones restantes como desee y, a continuación, seleccione **Crear**.

    ![Captura de pantalla del panel "Crear una clave" que aparece cuando se selecciona el botón "Generar/importar"](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

### <a name="add-an-azure-rbac-role"></a>Adición de un rol RBAC de Azure

Ahora que ha creado el almacén de Azure Key Vault y una clave, debe agregar un rol RBAC de Azure para poder usar la instancia de Azure Key Vault con el conjunto de cifrado de disco.

1. Seleccione **Control de acceso (IAM)** y agregue un rol.
1. Agregue los roles **Administrador de Key Vault**, **Propietario** o **Colaborador**.

## <a name="set-up-your-disk-encryption-set"></a>Configuración del conjunto de cifrado de disco

1. Busque **conjuntos de cifrado de disco** y seleccione la opción.
1. En el panel **Conjuntos de cifrado de disco**, seleccione **+Crear**.

    ![Captura de pantalla de la pantalla principal del portal de cifrado de discos. Resaltado del botón Agregar](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Seleccione el grupo de recursos, asigne un nombre al conjunto de cifrado y seleccione la misma región que el almacén de claves.
1. En **Tipo de cifrado**, seleccione **Cifrado en reposo con una clave administrada por el cliente**.

    > [!NOTE]
    > Una vez que cree un conjunto de cifrado de disco con un tipo de cifrado en particular, no se puede cambiar. Si desea usar otro tipo de cifrado, debe crear un nuevo conjunto de cifrado de disco.

1. Seleccione **Hacer clic para seleccionar una clave**.
1. Seleccione el almacén de claves y la clave que creó anteriormente, así como la versión.
1. Haga clic en **Seleccionar**.
1. Seleccione **Revisar y crear** y, a continuación, **Crear**.

    ![Captura de pantalla del panel de creación del cifrado de disco. Se muestra la suscripción, el grupo de recursos, el nombre del conjunto de cifrado de disco, la región y el selector de claves y de almacenes de claves.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)
