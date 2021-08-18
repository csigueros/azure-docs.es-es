---
title: Cifrado de Azure Data Lake Storage Gen1 | Microsoft Docs
description: El cifrado en Azure Data Lake Storage Gen1 le ayuda a proteger sus datos, implementar directivas de seguridad de empresa y satisfacer los requisitos de cumplimiento normativo. En este artículo se proporciona información general sobre el diseño y se discuten algunos de los aspectos técnicos de la implementación.
services: data-lake-store
documentationcenter: ''
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: dda173278f2bb788b0768fc891b951793224bf77
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113006165"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Cifrado de datos en Azure Data Lake Storage Gen1

El cifrado en Azure Data Lake Storage Gen1 le ayuda a proteger sus datos, implementar directivas de seguridad de empresa y satisfacer los requisitos de cumplimiento normativo. En este artículo se proporciona información general sobre el diseño y se discuten algunos de los aspectos técnicos de la implementación.

Data Lake Storage Gen1 admite el cifrado de datos tanto en reposo como en tránsito. En el caso de datos en reposo, Data Lake Storage Gen1 admite el cifrado transparente "activado de forma predeterminada". Con más detalle, esto significa:

* **Activado de forma predeterminada**: cuando se crea una cuenta de Data Lake Storage Gen1, la configuración predeterminada habilita el cifrado. Por lo tanto, los datos que se almacenan en Data Lake Storage Gen1 siempre se cifran antes de almacenarlos en un medio persistente. Este es el comportamiento para todos los datos y no se puede cambiar después de crear una cuenta.
* **Transparente**: Data Lake Storage Gen1 cifra automáticamente los datos antes de guardarlos y los descifra antes de recuperarlos. Un administrador configura y administra el cifrado de cada instancia de Data Lake Storage Gen1. No se realizan cambios en las API de acceso a datos. Por lo tanto, no se requiere ningún cambio en las aplicaciones y los servicios que interactúan con Data Lake Storage Gen1 a causa del cifrado.

Los datos en tránsito (también conocidos como datos en movimiento) también se cifran siempre en Data Lake Storage Gen1. Además de que los datos se cifran antes de almacenarse en un medio persistente, también se protegen cuando están en tránsito mediante HTTPS. HTTPS es el único protocolo admitido para las interfaces de REST de Data Lake Storage Gen1. En el diagrama siguiente se muestra cómo se cifran los datos en Data Lake Storage Gen1:

![Diagrama de cifrado de datos en Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Configuración del cifrado con Data Lake Storage Gen1

El cifrado para Data Lake Storage Gen1 se configura durante la creación de la cuenta y siempre está habilitado de forma predeterminada. Puede administrar las claves por su cuenta o dejar que Data Lake Storage Gen1 las administre en su nombre (esta es la opción predeterminada).

Para más información, consulte [Introducción](./data-lake-store-get-started-portal.md).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Funcionamiento del cifrado en Data Lake Storage Gen1

A continuación se explica cómo administrar las claves de cifrado maestras y se describen los tres tipos diferentes de claves que puede usar en el cifrado de datos de Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Claves de cifrado maestras

Data Lake Storage Gen1 proporciona dos modos de administración de claves de cifrado maestras (MEK). Por ahora, suponga que la clave de cifrado maestra es la clave de nivel superior. Se necesita acceso a la clave de cifrado maestra para descifrar cualquier dato almacenado en Data Lake Storage Gen1.

Los dos modos para administrar la clave de cifrado maestra son los siguientes:

*   Claves administradas por el servicio
*   Claves administradas por el cliente

En ambos modos, la clave de cifrado maestra está protegida al estar almacenada en Azure Key Vault. Key Vault es un servicio de Azure totalmente administrado y enormemente seguro que se puede utilizar para proteger las claves criptográficas. Para más información, consulte [Key Vault](https://azure.microsoft.com/services/key-vault).

Esta es una breve comparación de las funcionalidades que proporcionan ambos modos de administración de las MEK.

| Pregunta | Claves administradas por el servicio | Claves administradas por el cliente |
| -------- | -------------------- | --------------------- |
|¿Cómo se almacenan los datos?|Siempre se cifran antes de almacenarse.|Siempre se cifran antes de almacenarse.|
|¿Dónde se almacena la clave de cifrado maestra?|Key Vault|Key Vault|
|¿Hay claves de cifrado almacenadas sin cifrar fuera de Key Vault? |No|No|
|¿Se puede recuperar la clave de cifrado maestra mediante Key Vault?|No. Después de que la clave de cifrado maestra se almacena en Key Vault, solo se puede usar para el cifrado y el descifrado.|No. Después de que la clave de cifrado maestra se almacena en Key Vault, solo se puede usar para el cifrado y el descifrado.|
|¿Quién posee la instancia de Key Vault y la clave de cifrado maestra?|Servicio de Data Lake Storage Gen1|Usted es el propietario de la instancia de Key Vault, que pertenece a su propia suscripción de Azure. La clave de cifrado maestra de Key Vault se puede administrar mediante software o hardware.|
|¿Puede revocar el acceso a la clave de cifrado maestra para el servicio Data Lake Storage Gen1?|No|Sí. Puede administrar listas de control de acceso en Key Vault y eliminar entradas de control de acceso a la identidad de servicio para el servicio Data Lake Storage Gen1.|
|¿Puede eliminar permanentemente la clave de cifrado maestra?|No|Sí. Si elimina la clave de cifrado maestra de Key Vault, nadie podrá cifrar los datos de la cuenta de Data Lake Storage Gen1, incluido el servicio Data Lake Storage Gen1. <br><br> Si ha realizado copia de seguridad explícita de la clave de cifrado maestra antes de eliminarla de Key Vault, se puede restaurar y entonces se pueden recuperar los datos. Sin embargo, si no lo ha hecho, los datos de la cuenta de Data Lake Storage Gen1 nunca se podrán cifrar después.|


Aparte de esta diferencia sobre quién administra la clave de cifrado maestra y la instancia de Key Vault en la que reside, el resto del diseño es igual en ambos modos.

Al elegir el modo de las claves de cifrado maestras, es importante recordar lo siguiente:

*   Puede elegir si se usarán claves administradas por el cliente o claves administradas por el servicio al aprovisionar una cuenta de Data Lake Storage Gen1.
*   Después de aprovisionar una cuenta de Data Lake Storage Gen1, no se puede cambiar el modo.

### <a name="encryption-and-decryption-of-data"></a>Cifrado y descifrado de datos

Son tres los tipos de claves que se usan en el diseño del cifrado de datos. En la tabla siguiente se proporciona un resumen:

| Clave                   | Abreviatura | Asociada a | Ubicación de almacenamiento                             | Tipo       | Notas                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Clave de cifrado maestra | MEK          | Cuenta de Data Lake Storage Gen1 | Key Vault                              | Asimétrica | Puede administrarla Data Lake Storage Gen1 o usted.                                                              |
| Clave de cifrado de datos   | DEK          | Cuenta de Data Lake Storage Gen1 | Almacenamiento persistente, administrada por el servicio Data Lake Storage Gen1 | Simétrica  | La DEK se cifra mediante la clave de cifrado maestra. La DEK cifrada es lo que se almacena en el medio persistente. |
| Clave de cifrado de bloque  | BEK          | Un bloque de datos | None                                         | Simétrica  | La clave BEK se obtiene de la clave de cifrado de datos y del bloque de datos.                                                      |

En el siguiente diagrama, se ilustra este concepto:

![Claves de cifrado de datos](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritmo para descifrar un archivo:
1.  Compruebe si la clave DEK de la cuenta de Data Lake Storage Gen1 está almacenada en la caché y lista para su uso.
    - Si no es así, lea la clave DEK cifrada del almacenamiento persistente y envíela a Key Vault para descifrarla. Almacene en caché la clave DEK descifrada en memoria. Ahora está lista para su uso.
2.  Para cada bloque de datos del archivo:
    - Lea el bloque de datos cifrado del almacenamiento persistente.
    - Genere la clave BEK con la clave DEK y el bloque de datos cifrado.
    - Use la clave BEK para descifrar los datos.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritmo para cifrar un archivo:
1.  Compruebe si la clave DEK de la cuenta de Data Lake Storage Gen1 está almacenada en la caché y lista para su uso.
    - Si no es así, lea la clave DEK cifrada del almacenamiento persistente y envíela a Key Vault para descifrarla. Almacene en caché la clave DEK descifrada en memoria. Ahora está lista para su uso.
2.  Generar una clave BEK única para el bloque de datos con la clave DEK.
3.  Cifre el bloque de datos con la clave BEK mediante el cifrado AES-256.
4.  Almacene el bloque de datos cifrado en el almacenamiento persistente.

> [!NOTE] 
> La clave de cifrado siempre se almacena cifrada por MEK, ya sea en un soporte duradero como en la memoria caché.

## <a name="key-rotation"></a>Rotación de claves

Cuando se usan claves administradas por el cliente, puede rotar la clave MEK. Para aprender a configurar una cuenta de Data Lake Storage Gen1 con claves administradas por el cliente, consulte [Introducción](./data-lake-store-get-started-portal.md).

### <a name="prerequisites"></a>Requisitos previos

Cuando configuró la cuenta de Data Lake Storage Gen1, eligió usar sus propias claves. Esta opción no se puede cambiar una vez creada la cuenta. En los siguientes pasos se supone que usa claves administradas por el cliente (es decir, ha elegido sus propias claves de Key Vault).

Tenga en cuenta que si usa las opciones predeterminadas para el cifrado, los datos siempre se cifran mediante claves administradas con Data Lake Storage Gen1. En esta opción, no tiene la posibilidad de rotar claves, ya que se administran en Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Rotación de la clave MEK en Data Lake Storage Gen1

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Vaya a la instancia de Key Vault que almacena las claves asociadas con la cuenta de Data Lake Storage Gen1. Seleccione **Claves**.

    ![Captura de pantalla de Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Seleccione la clave asociada con su cuenta de Data Lake Storage Gen1 y cree una nueva versión de esta clave. Tenga en cuenta que Data Lake Storage Gen1 solo admite actualmente la rotación de claves a una nueva versión de clave. No admite la rotación a una clave diferente.

   ![Captura de pantalla de la ventana Claves, donde se resalta Nueva versión](./media/data-lake-store-encryption/keynewversion.png)

4. Vaya a la cuenta de almacenamiento de Data Lake Storage Gen1 y seleccione **Cifrado**.

   ![Captura de pantalla de la ventana de la cuenta de almacenamiento de Data Lake Storage Gen1, con Cifrado resaltado](./media/data-lake-store-encryption/select-encryption.png)

5. Un mensaje le notifica que hay una nueva versión de la clave. Haga clic en **Rotar clave** para actualizar la clave a la nueva versión.

   ![Captura de pantalla de la ventana Data Lake Storage Gen1 con el mensaje y Rotar clave resaltado](./media/data-lake-store-encryption/rotatekey.png)

Esta operación tardará menos de dos minutos y no hay ningún tiempo de inactividad previsto debido a la rotación de claves. Una vez completada la operación, la nueva versión de la clave está en uso.

> [!IMPORTANT]
> Una vez completada la operación de rotación de claves, la versión anterior de la clave ya no se usa de forma activa para cifrar datos nuevos. Puede haber casos en los que se necesite la clave antigua para el acceso a los datos más antiguos. Para permitir la lectura de estos datos más antiguos, no elimine la clave antigua