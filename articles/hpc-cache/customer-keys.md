---
title: Uso de claves administradas por el cliente para cifrar datos en Azure HPC Cache
description: Cómo usar Azure Key Vault con Azure HPC Cache para controlar el acceso con claves de cifrado en lugar de usar las claves de cifrado administradas por Microsoft predeterminadas
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/15/2021
ms.author: v-erkel
ms.openlocfilehash: dbb2834d6d51f2555da05863c606b4678b9a17b7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772290"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Uso de claves de cifrado administradas por el cliente para Azure HPC Cache

Puede usar Azure Key Vault para controlar la titularidad de las claves usadas para cifrar los datos en Azure HPC Cache. En este artículo se explica cómo usar claves administradas por el cliente para el cifrado de datos en caché.

> [!NOTE]
> Todos los datos almacenados en Azure, incluidos los discos de caché, se cifran en reposo mediante claves administradas por Microsoft de forma predeterminada. Solo tiene que seguir los pasos de este artículo si desea administrar las claves usadas para cifrar los datos.

Azure HPC Cache también está protegida por el [cifrado del host de la máquina virtual](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) en los discos administrados que contienen los datos almacenados en caché, incluso si agrega una clave de cliente para los discos de caché. La adición de una clave administrada por el cliente para el cifrado doble proporciona un nivel de seguridad adicional a los clientes con necesidades de alta seguridad. Consulte el artículo [Cifrado del lado servidor de Azure Disk Storage](../virtual-machines/disk-encryption.md) para más información.

Hay tres pasos para habilitar el cifrado de claves administradas por el cliente para Azure HPC Cache:

1. Configure una instancia de Azure Key Vault para almacenar las claves.
1. Al crear la instancia de Azure HPC Cache, elija el cifrado de claves administradas por el cliente y especifique el almacén de claves y la clave que se usarán. Opcionalmente, proporcione una identidad administrada para que la caché la use para acceder al almacén de claves.

   En función de las opciones que realice en este paso, es posible que pueda omitir el paso 3. Lea [Elección de una opción de identidad administrada para la memoria caché](#choose-a-managed-identity-option-for-the-cache) para más información.

1. Si usa una **identidad administrada asignada por el sistema** o una **identidad asignada por el usuario que no está configurada con acceso al almacén de claves**: vaya a la caché recién creada y autorícela para acceder al almacén de claves.

   Si la identidad administrada aún no tiene acceso a Azure Key Vault, el cifrado no se configura completamente hasta después de autorizarla desde la caché recién creada (paso 3).

   Si usa una identidad administrada por el sistema, la identidad se crea cuando se crea la memoria caché. Debe pasar la identidad de la caché al almacén de claves para que sea un usuario autorizado después de la creación.

   Puede omitir este paso si asigna una identidad administrada por el usuario que ya tiene acceso al almacén de claves.

Después de crear la caché, no puede cambiar entre claves administradas por el cliente y claves administradas por Microsoft. Sin embargo, si la memoria caché usa claves administradas por el cliente, puede [cambiar](#update-key-settings) la clave de cifrado, la versión de la clave y el almacén de claves según sea necesario.

## <a name="understand-key-vault-and-key-requirements"></a>Descripción de los requisitos del almacén de claves y de la clave

El almacén de claves y la clave deben cumplir con estos requisitos para funcionar con Azure HPC Cache.

Propiedades del almacén de claves:

* **Suscripción**: Use la misma suscripción que se usa para la memoria caché.
* **Región**: El almacén de claves debe estar en la misma región que la instancia de Azure HPC Cache.
* **Plan de tarifa**: El nivel Estándar es suficiente para su uso con Azure HPC Cache.
* **Eliminación temporal**: Azure HPC Cache habilitará la eliminación temporal si aún no está configurada en el almacén de claves.
* **Protección de purga**: La protección de purga debe estar habilitada.
* **Directiva de acceso**: La configuración predeterminada es suficiente.
* **Conectividad de red**: Azure HPC Cache debe poder acceder al almacén de claves con independencia de la configuración del punto de conexión que elija.

Propiedades de la clave:

* **Tipo de clave**: RSA
* **Tamaño de la clave RSA**: 2048
* **Habilitado**: Sí

Permisos de acceso al almacén de claves:

* El usuario que crea la instancia de Azure HPC Cache debe tener permisos equivalentes al [rol de colaborador de Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor). Se necesitan los mismos permisos para configurar y administrar Azure Key Vault.

  Para obtener más información, consulte [Protección del acceso a un almacén de claves](../key-vault/general/security-features.md).

## <a name="choose-a-managed-identity-option-for-the-cache"></a>Elección de una opción de identidad administrada para la memoria caché
<!-- check for cross-references from here and create -->

HPC Cache usa su credencial de identidad administrada para conectarse al almacén de claves.

Azure HPC Cache puede usar dos tipos de identidades administradas:

* Identidad administrada **asignada por el sistema**: una identidad única creada automáticamente para la memoria caché. Esta identidad administrada solo existe mientras HPC Cache exista y no se puede administrar ni modificar directamente.

* Identidad administrada **asignada por el usuario**: una credencial de identidad independiente que se administra por separado de la memoria caché. Puede configurar una identidad administrada asignada por el usuario que tenga exactamente el acceso que quiera y usarla en varias instancias de HPC Cache.

Si no asigna una identidad administrada a la caché al crearla, Azure crea automáticamente una identidad administrada asignada por el sistema para la caché.

Con una identidad administrada asignada por el usuario, puede proporcionar una identidad que ya tenga acceso al almacén de claves. (Por ejemplo, se ha agregado a una directiva de acceso del almacén de claves o tiene un rol Azure RBAC que permite el acceso). Si usa una identidad asignada por el sistema o proporciona una identidad administrada que no tiene acceso, deberá solicitar acceso desde la caché después de la creación. Se trata de un paso manual, que se describe a continuación en el [paso 3](#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed).

* Más información sobre las [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md)

* Información sobre los [conceptos básicos de Azure Key Vault](../key-vault/general/basic-concepts.md)

## <a name="1-set-up-azure-key-vault"></a>1. Configuración de Azure Key Vault

Puede configurar un almacén de claves y una clave antes de crear la memoria caché, o bien hacerlo como parte de la creación de la memoria caché. Asegúrese de que estos recursos cumplan los requisitos descritos [anteriormente](#understand-key-vault-and-key-requirements).

En el momento de la creación de la memoria caché, debe especificar un almacén, una clave y una versión de clave que se usarán para el cifrado de la caché.

Para más detalles, consulte la [documentación de Azure Key Vault](../key-vault/general/overview.md).

> [!NOTE]
> Azure Key Vault debe usar la misma suscripción y estar en la misma región que Azure HPC Cache. Asegúrese de que la región que elija [sea compatible con ambos productos](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Creación de la memoria caché con claves administradas por el cliente habilitadas

Debe especificar el origen de la clave de cifrado al crear su instancia de Azure HPC Cache. Siga las instrucciones de [Creación de una instancia de Azure HPC Cache](hpc-cache-create.md) y especifique el almacén de claves y la clave en la página **Disk encryption keys** (Claves de cifrado de disco). Puede crear un almacén de claves y una clave nuevos durante la creación de la memoria caché.

> [!TIP]
> Si la página **Clave de cifrado de disco** no aparece, asegúrese de que la memoria caché se encuentre en una de las [regiones admitidas](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

![Captura de pantalla de la pantalla de claves de cifrado de disco completada, parte de la interfaz de creación de caché en el portal.](media/customer-keys-populated.png)

El usuario que crea la memoria caché debe tener privilegios iguales a los del [rol colaborador de Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor) o superior.

1. Haga clic en el botón para habilitar las claves administradas de forma privada. Después de cambiar esta configuración, aparecerá la configuración del almacén de claves.

1. Haga clic en **Seleccionar un almacén de claves** para abrir la página de selección de claves. Elija o cree el almacén de claves y la clave para cifrar los datos en los discos de caché.

   Si su instancia de Azure Key Vault no aparece en la lista, compruebe estos requisitos:

   * ¿Se encuentra la memoria caché en la misma suscripción que el almacén de claves?
   * ¿Se encuentra la memoria caché en la misma región que el almacén de claves?
   * ¿Hay conectividad de red entre Azure Portal y el almacén de claves?

1. Después de seleccionar un almacén, seleccione la clave individual en las opciones disponibles o cree una clave nueva. Debe ser una clave RSA de 2048 bits.

1. Especifique la versión de la clave seleccionada. Obtenga más información sobre el control de versiones en la [documentación de Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Estos parámetros son opcionales:

* Active la casilla **Always use current key version** (Usar siempre la versión actual de la clave) si quiere usar la [rotación automática de claves](../virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys).

* Si quiere usar una identidad administrada específica para esta caché, seleccione **Asignada por el usuario** en la sección **Identidades administradas** y seleccione la identidad que quiere usar. Consulte la [documentación sobre las identidades administradas](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para obtener ayuda.

  > [!TIP]
  > Una identidad administrada asignada por el usuario puede simplificar la creación de la caché si pasa una identidad que ya está configurada para acceder al almacén de claves. Con una identidad administrada asignada por el sistema, debe realizar un paso adicional después de crear la caché para autorizar a la identidad asignada por el sistema recién creada a usar el almacén de claves.

  > [!NOTE]
  > No es posible cambiar la identidad asignada tras crear la memoria caché.

Continúe con el resto de las especificaciones y cree la memoria caché como se describe en [Creación de una instancia de Azure HPC Cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache-if-needed"></a>3. Autorización del cifrado de Azure Key Vault desde la memoria caché (si es necesario)
<!-- header is linked from create article, update if changed -->

> [!NOTE]
> Este paso no es necesario si proporcionó una identidad administrada asignada por el usuario con acceso al almacén de claves al crear la memoria caché.

Después de unos minutos, la nueva instancia de Azure HPC Cache aparece en Azure Portal. Vaya a la página **Información general** para autorizarla a acceder a su instancia de Azure Key Vault y habilitar el cifrado de claves administradas por el cliente.

> [!TIP]
> La caché puede aparecer en la lista de recursos antes de que se borren los mensajes de "implementación en curso". Compruebe la lista de recursos al cabo de un minuto o dos en lugar de esperar una notificación de operación correcta.
>
> Debe autorizar el cifrado en un plazo de 90 minutos después de crear la memoria caché. Si no completa este paso, se agotará el tiempo de espera de la caché y se producirá un error. Una caché con errores debe volver a crearse, no se puede corregir.

La memoria caché muestra el estado **Esperando la clave**. Haga clic en el botón **Habilitar cifrado** en la parte superior de la página para autorizar a la memoria caché a acceder al almacén de claves especificado.

![Captura de pantalla de la página de información general de la caché en el portal, con el botón Habilitar cifrado (fila superior) y Estado: Esperando la clave resaltados.](media/waiting-for-key.png)

Haga clic en **Habilitar cifrado** y, a continuación, haga clic en el botón **Sí** para autorizar a la memoria caché a usar la clave de cifrado. Esta acción también habilita la eliminación temporal y protección de purga (si aún no están habilitadas) en el almacén de claves.

![Captura de pantalla de la página de información general de la caché en el portal, con un mensaje de banner en la parte superior que pide al usuario que habilite el cifrado haciendo clic en Sí.](media/enable-keyvault.png)

Después de que la memoria caché solicita acceso al almacén de claves, puede crear y cifrar los discos que almacenan los datos en caché.

Después de autorizar el cifrado, Azure HPC Cache pasa varios minutos más de configuración para crear los discos cifrados y la infraestructura relacionada.

## <a name="update-key-settings"></a>Actualización de la configuración de claves

Puede cambiar el almacén de claves, la clave o la versión de clave de la memoria caché desde Azure Portal. Haga clic en el vínculo configuración **Cifrado** de la memoria caché para abrir la página **Configuración de clave de cliente**.

No se puede cambiar una caché entre claves administradas por el cliente y claves administradas por el sistema.

![Captura de pantalla de la página "Configuración de clave de cliente", a la que se accede al hacer clic en Configuración > Cifrado en la página de caché de Azure Portal.](media/change-key-click.png)

Haga clic en el vínculo **Cambiar clave** y, a continuación, haga clic en **Cambiar el almacén de claves, la clave o la versión** para abrir el selector de claves.

![Captura de pantalla de la página "Seleccionar la clave de Azure Key Vault" con tres selectores desplegables para elegir el almacén de claves, la clave y la versión.](media/select-new-key.png)

Los almacenes de claves de la misma suscripción y la misma región que esta caché se muestran en la lista.

Después de elegir los nuevos valores de clave de cifrado, haga clic en **Seleccionar**. Aparece una página de confirmación con los nuevos valores. Haga clic en **Guardar** para finalizar la selección.

![Captura de pantalla de la página de confirmación con el botón Guardar en la parte superior izquierda.](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Más información sobre las claves administradas por el cliente en Azure

En estos artículos se explica más sobre el uso de Azure Key Vault y las claves administradas por el cliente para cifrar datos en Azure:

* [Información general del cifrado de almacenamiento en Azure](../storage/common/storage-service-encryption.md)
* [Cifrado de discos con claves administradas por el cliente](../virtual-machines/disk-encryption.md#customer-managed-keys): documentación sobre el uso de Azure Key Vault y discos administrados, que es un escenario similar al de Azure HPC Cache.

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya creado la instancia de Azure HPC Cache y haya autorizado el cifrado basado en Key Vault, continúe con la configuración de la memoria caché concediéndole acceso a los orígenes de datos.

* [Incorporación de destinos de almacenamiento](hpc-cache-add-storage.md)
