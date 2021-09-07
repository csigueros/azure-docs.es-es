---
title: Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)
titleSuffix: Azure Storage
description: Aprenda a configurar una directiva de inmutabilidad limitada a un contenedor (versión preliminar). Las directivas de inmutabilidad proporcionan compatibilidad con WORM (escribir una vez, leer muchas) en Blob Storage, donde los datos se almacenan en un formato que no se puede borrar ni modificar.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 45d44df64754234c99571b13059d02372cd26622
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781160"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)

El almacenamiento inmutable de Azure Blob Storage permite a los usuarios almacenar datos críticos para la empresa en estado WORM. Mientras los datos se encuentran en estado WORM, no se pueden modificar ni eliminar durante un intervalo especificado por el usuario. Con la configuración de directivas de inmutabilidad para los datos de blobs, puede impedir que sus datos se sobrescriban y eliminen. Las directivas de inmutabilidad incluyen directivas de retención con duración definida y suspensiones legales. Para más información sobre las directivas de inmutabilidad de Blob Storage, consulte [Almacenamiento de datos críticos para la empresa con almacenamiento inmutable](immutable-storage-overview.md).

Una directiva de inmutabilidad puede estar limitada a una determinada versión de blob (versión preliminar) o un contenedor. En este artículo se describe cómo configurar una directiva de inmutabilidad en el nivel de versión. Para aprender a configurar directivas de inmutabilidad en el nivel de contenedor, consulte [Configuración de directivas de inmutabilidad para contenedores](immutable-policy-configure-container-scope.md).

La configuración de una directiva de inmutabilidad en el nivel de versión es un proceso de dos pasos:

1. En primer lugar, habilite la compatibilidad con la inmutabilidad en el nivel de versión en un contenedor nuevo o existente. Para más información, consulte [Habilitación de la compatibilidad con la inmutabilidad en el nivel de versión de un contenedor](#enable-support-for-version-level-immutability-on-a-container).
1. A continuación, configure una directiva de retención con duración definida o una suspensión legal que se aplique a una o varias versiones de blob de ese contenedor.

> [!IMPORTANT]
> Las directivas de inmutabilidad en el nivel de versión están actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="prerequisites"></a>Requisitos previos

Para configurar directivas de retención con duración definida en el nivel de versión, el control de versiones de blob debe estar habilitado para la cuenta de almacenamiento. Para obtener información sobre cómo habilitar el control de versiones de blobs, consulte [Habilitación y administración del control de versiones de blobs](versioning-enable.md).

Para información sobre las configuraciones de cuentas de almacenamiento admitidas para las directivas de inmutabilidad en el nivel de versión, consulte [Configuraciones de cuenta admitidas](immutable-storage-overview.md#supported-account-configurations).

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>Habilitación de la compatibilidad con la inmutabilidad en el nivel de versión de un contenedor

Para poder aplicar una directiva de retención con duración definida a una versión de blob, debe habilitar la compatibilidad con la inmutabilidad en el nivel de versión. Tanto los contenedores nuevos como los existentes se pueden configurar para admitir la inmutabilidad en el nivel de versión. Sin embargo, el contenedor existente debe someterse a un proceso de migración para habilitar la compatibilidad.

Tenga en cuenta que la habilitación de la compatibilidad con la inmutabilidad en el nivel de versión para un contenedor no convierte los datos de ese contenedor en inmutables. También debe configurar una directiva de inmutabilidad predeterminada para el contenedor o una directiva de inmutabilidad para una versión de blob específica.

### <a name="enable-version-level-immutability-for-a-new-container"></a>Habilitación de la inmutabilidad en el nivel de versión para un nuevo contenedor

Para usar una directiva de inmutabilidad en el nivel de versión, primero debe habilitar explícitamente la compatibilidad con WORM en el nivel de versión en el contenedor. Puede hacerlo al crear el contenedor o al agregar una directiva de inmutabilidad en el nivel de versión a un contenedor existente.

Para crear un contenedor que admita la inmutabilidad en el nivel de versión en Azure Portal, siga estos pasos:

1. En Azure Portal, vaya a la página **Contenedores** de la cuenta de almacenamiento y seleccione **Agregar**.
1. En el cuadro de diálogo **Nuevo contenedor**, proporcione un nombre para el contenedor y expanda la sección **Avanzado**.
1. Seleccione **Enable version-level immutability support** (Habilitar compatibilidad con la inmutabilidad en el nivel de versión) para habilitar esta funcionalidad para el contenedor.

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="Captura de pantalla que muestra cómo crear un contenedor con la inmutabilidad en el nivel de versión habilitada":::

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>Migración de un contenedor existente para admitir la inmutabilidad en el nivel de versión

Para configurar directivas de inmutabilidad en el nivel de versión para un contenedor existente, debe migrar el contenedor para que admita el almacenamiento inmutable en el nivel de versión. La migración del contenedor puede tardar algún tiempo y no se puede revertir.

Un contenedor existente debe migrarse independientemente de si tiene configurada una directiva de retención con duración definida en el nivel de contenedor. Si el contenedor ya tiene una suspensión legal en el nivel de contenedor, no se puede migrar hasta que se retire esta.

Para migrar un contenedor para que admita el almacenamiento inmutable en el nivel de versión en Azure Portal, siga estos pasos:

1. Desplácese hasta el contenedor deseado.
1. Seleccione el botón **Más** a la derecha y, luego, **Directiva de acceso**.
1. En **Almacenamiento de blobs inmutable**, seleccione **Agregar directiva**.
1. En el campo **Tipo de directiva**, elija *Retención con duración definida* y especifique el período de retención.
1. Seleccione **Enable version-level immutability** (Habilitar inmutabilidad en el nivel de versión).
1. Seleccione **Aceptar** para iniciar la migración.

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="Captura de pantalla que muestra cómo migrar un contenedor existente para admitir la inmutabilidad en el nivel de versión":::

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>Configuración de una directiva de retención con duración definida en un contenedor

Después de que un contenedor se ha habilitado para la inmutabilidad en el nivel de versión, puede especificar una directiva predeterminada de retención con duración definida en el nivel de versión para el contenedor. La directiva predeterminada se aplica a todas las versiones de blob del contenedor, a menos que invalide la directiva para versiones individuales.

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>Configuración de una directiva predeterminada de retención con duración definida en un contenedor

Para aplicar una directiva de inmutabilidad predeterminada en el nivel de versión a un contenedor en Azure Portal, siga estos pasos:

1. En Azure Portal, vaya a la página **Contenedores** y busque el contenedor al que quiere aplicar la directiva.
1. Seleccione el botón **Más** a la derecha del nombre del contenedor y elija **Directiva de acceso**.
1. En el cuadro de diálogo **Directiva de acceso**, en la sección **Almacenamiento de blobs inmutable**, elija **Agregar directiva**.
1. Seleccione **Time-based retention policy** (Directiva de retención con duración definida) y especifique el intervalo de retención.
1. Si lo desea, seleccione **Permitir anexiones protegidas adicionales** para permitir operaciones de escritura en blobs en anexos que estén protegidos por una directiva de inmutabilidad. Para más información, consulte [Permitir escrituras de blobs en anexos protegidos](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).
1. Seleccione **Aceptar** para aplicar la directiva predeterminada al contenedor.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="Captura de pantalla que muestra cómo configurar una directiva predeterminada de retención en el nivel de versión para un contenedor":::

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>Determinación del ámbito de una directiva de retención en un contenedor

Para determinar el ámbito de una directiva de retención con duración definida en Azure Portal, siga estos pasos:

1. Desplácese hasta el contenedor deseado.
1. Seleccione el botón **Más** a la derecha y, luego, **Directiva de acceso**.
1. En **Almacenamiento de blobs inmutable**, busque el campo **Ámbito**. Si el contenedor está configurado con una directiva predeterminada de retención en el nivel de versión, el ámbito se establece en *Versión*, como se muestra en la siguiente imagen:

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="Captura de pantalla que muestra la directiva predeterminada de retención en el nivel de versión configurada para el contenedor":::

1. Si el contenedor está configurado con una directiva de retención en el nivel de versión, el ámbito se establece en *Contenedor*, como se muestra en la siguiente imagen:

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="Captura de pantalla que muestra la directiva predeterminada de retención en el nivel de contenedor configurada para el contenedor":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>Configuración de una directiva de retención con duración definida en una versión existente

Las directivas de retención con duración definida mantienen los datos de blob en un estado WORM durante un intervalo especificado. Para más información sobre las directivas de retención con duración definida, consulte [Directivas de retención con duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md).

Tiene tres opciones para configurar una directiva de retención con duración definida para una versión de blob:

- Opción 1: Puede configurar una directiva predeterminada que esté limitada al contenedor y que se aplique a todos los objetos que contiene de forma predeterminada. Los objetos del contenedor heredarán la directiva predeterminada a menos que la invalide explícitamente mediante la configuración de una directiva para una versión de blob determinada. Para más información, consulte [Configuración de una directiva predeterminada de retención con duración definida en un contenedor](#configure-a-default-time-based-retention-policy-on-a-container).
- Opción 2: Puede configurar una directiva para la versión actual del blob. Esta directiva puede invalidar una directiva predeterminada configurada para el contenedor, si existe una y se desbloquea. De forma predeterminada, las versiones anteriores que se crean después de configurar la directiva heredarán la directiva de la versión actual del blob. Para más información, consulte [Configuración de una directiva de retención para la versión actual de un blob](#configure-a-retention-policy-on-the-current-version-of-a-blob).
- Opción 3: Puede configurar una directiva para una versión anterior de un blob. Esta directiva puede invalidar una directiva predeterminada configurada para la versión anterior, si existe una y se desbloquea. Para más información, consulte [Configuración de una directiva de retención para la versión anterior de un blob](#configure-a-retention-policy-on-a-previous-version-of-a-blob).

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>Configuración de una directiva de retención para la versión actual de un blob

Al desplazarse hacia un contenedor, en Azure Portal se muestra una lista de blobs. Cada blob mostrado representa la versión actual del blob. Para más información sobre el control de versiones de blobs, consulte [Control de versiones de blobs](versioning-overview.md).

Para configurar una directiva de retención con duración definida para la versión actual de un blob, siga estos pasos:

1. Vaya al contenedor que contiene el blob de destino.
1. Seleccione el botón **Más** a la derecha del nombre del blob y elija **Directiva de acceso**. Si ya se ha configurado una directiva de retención con duración definida para la versión anterior, aparece en el cuadro de diálogo **Directiva de acceso**.
1. En el cuadro de diálogo **Directiva de acceso**, en la sección **Immutable blob versions** (Versiones de blob inmutables), elija **Agregar directiva**.
1. Seleccione **Time-based retention policy** (Directiva de retención con duración definida) y especifique el intervalo de retención.
1. Seleccione **Aceptar** para aplicar la directiva a la versión actual del blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="Captura de pantalla que muestra cómo configurar una directiva de retención para la versión actual de un blob":::

Puede ver las propiedades de un blob para determinar si hay una directiva habilitada para la versión actual. Seleccione el blob y, luego, vaya a la pestaña **Información general** y busque la propiedad **Version-level immutability policy** (Directiva de inmutabilidad en el nivel de versión). Si hay una directiva habilitada, la propiedad **Período de retención** mostrará la fecha y hora de expiración de la directiva. Tenga en cuenta que una directiva puede estar configurada para la versión actual o puede heredarse del contenedor primario del blob si hay una directiva predeterminada vigente.

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="Captura de pantalla que muestra las propiedades de la directiva de inmutabilidad en la versión del blob en Azure Portal":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>Configuración de una directiva de retención para una versión anterior de un blob

También puede configurar una directiva de retención con duración definida para una versión anterior de un blob. Una versión anterior siempre es inmutable, ya que no se puede modificar. Sin embargo, se puede eliminar una versión anterior. Una directiva de retención con duración definida protege contra la eliminación mientras está vigente.

Para configurar una directiva de retención con duración definida para una versión anterior de un blob, siga estos pasos:

1. Vaya al contenedor que contiene el blob de destino.
1. Seleccione el blob y, luego, vaya a la pestaña **Versiones**.
1. Busque la versión de destino y, luego, seleccione el botón **Más** y elija **Directiva de acceso**. Si ya se ha configurado una directiva de retención con duración definida para la versión anterior, aparece en el cuadro de diálogo **Directiva de acceso**.
1. En el cuadro de diálogo **Directiva de acceso**, en la sección **Immutable blob versions** (Versiones de blob inmutables), elija **Agregar directiva**.
1. Seleccione **Time-based retention policy** (Directiva de retención con duración definida) y especifique el intervalo de retención.
1. Seleccione **Aceptar** para aplicar la directiva a la versión actual del blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="Captura de pantalla que muestra cómo configurar una directiva de retención para una versión de blob anterior en Azure Portal":::

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>Configuración de una directiva de retención con duración definida al cargar un blob

Al usar Azure Portal para cargar un blob en un contenedor que admite la inmutabilidad en el nivel de versión, tiene varias opciones para configurar una directiva de retención con duración definida para el nuevo blob:

- Opción 1: Si hay una directiva de retención predeterminada configurada para el contenedor, puede cargar el blob con la directiva del contenedor. Esta opción se selecciona de forma predeterminada cuando hay una directiva de retención en el contenedor.
- Opción 2: Si hay una directiva de retención predeterminada configurada para el contenedor, puede optar por invalidarla; para ello, puede definir una directiva de retención personalizada para el nuevo blob o cargar el blob sin directiva.
- Opción 3: Si no hay ninguna directiva predeterminada configurada para el contenedor, puede cargar el blob con una directiva personalizada o sin directiva.

Para configurar una directiva de retención con duración definida al cargar un blob, siga estos pasos:

1. Vaya al contenedor deseado y seleccione **Cargar**.
1. En el cuadro de diálogo **Cargar blob**, expanda la sección **Avanzado**.
1. Configure la directiva de retención con duración definida para el nuevo blob en el campo **Directiva de retención**. Si hay una directiva predeterminada configurada para el contenedor, esa directiva se selecciona de forma predeterminada. También puede especificar una directiva personalizada para el blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="Captura de pantalla que muestra las opciones para configurar la directiva de retención para la carga de blobs en Azure Portal":::

## <a name="modify-an-unlocked-retention-policy"></a>Modificación de una directiva de retención desbloqueada

Puede modificar una directiva de retención con duración definida desbloqueada para acortar o alargar el intervalo de retención. Las directivas desbloqueadas también se pueden eliminar. La edición o eliminación de una directiva de retención con duración definida para una versión de blob no afecta a las directivas vigentes para cualquier otra versión. Si hay una directiva de retención con duración definida predeterminada vigente para el contenedor, la versión del blob con la directiva modificada o eliminada ya no se heredará del contenedor.

Para modificar una directiva de retención con duración definida desbloqueada en Azure Portal, siga estos pasos:

1. Busque la versión de destino, que puede ser la versión actual o una versión anterior de un blob. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. En la sección **Versiones de blob inmutables**, busque la directiva desbloqueada ya existente. Seleccione el botón **Más** y, luego, **Editar** en el menú.

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="Captura de pantalla que muestra cómo editar una directiva de retención con duración definida existente en Azure Portal":::

1. Proporcione la nueva fecha y hora de expiración de la directiva.

Para eliminar una directiva desbloqueada, siga los pasos del 1 al 4 y seleccione **Eliminar** en el menú.

## <a name="lock-a-time-based-retention-policy"></a>Bloqueo de una directiva de retención con duración definida

Cuando haya terminado de probar una directiva de retención con duración definida, puede bloquearla. Una directiva bloqueada cumple los requisitos de SEC 17a-4(f) y de otras normas. Puede ampliar el intervalo de retención de una directiva bloqueada hasta cinco veces, pero no acortarlo.

Una vez bloqueada una directiva, no se puede eliminar. Sin embargo, puede eliminar el blob una vez expirado el intervalo de retención.

Para bloquear una directiva, siga estos pasos:

1. Busque la versión de destino, que puede ser la versión actual o una versión anterior de un blob. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. En la sección **Versiones de blob inmutables**, busque la directiva desbloqueada ya existente. Seleccione el botón **Más** y, luego, **Bloquear directiva** en el menú.
1. Confirme que desea bloquear la directiva.

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="Captura de pantalla que muestra cómo bloquear una directiva de retención con duración definida en Azure Portal":::

## <a name="configure-or-clear-a-legal-hold"></a>Configuración o borrado de una suspensión legal

Una suspensión legal almacena datos inmutables hasta que se borra explícitamente. Para más información sobre las directivas de suspensión legal, consulte [Suspensiones legales para datos de blobs inmutables](immutable-legal-hold-overview.md).

Para configurar una suspensión legal en una versión de blob, siga estos pasos:

1. Busque la versión de destino, que puede ser la versión actual o una versión anterior de un blob. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. En la sección **Versiones de blob inmutables**, seleccione **Agregar directiva**.
1. Elija **Suspensión legal** como tipo de directiva y seleccione **Aceptar** para aplicarla.

En la siguiente imagen se muestra una versión actual de un blob que tiene configuradas una directiva de retención con duración definida y una suspensión legal.

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="Captura de pantalla que muestra la suspensión legal configurada para la versión del blob":::

Para borrar una suspensión legal, vaya al cuadro de diálogo **Directiva de acceso**, seleccione el botón **Más** y elija **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- [Almacenamiento inmutable de los datos críticos para la empresa en Azure Blob Storage](immutable-storage-overview.md)
- [Directivas de retención con duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md)
- [Retenciones legales para datos de blob inmutables](immutable-legal-hold-overview.md)
