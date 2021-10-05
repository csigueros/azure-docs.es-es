---
title: Directivas de retención con duración definida para datos de blobs inmutables
titleSuffix: Azure Storage
description: Las directivas de retención con duración definida almacenan datos de blob en un estado WORM (una sola lectura, varias escrituras) para un intervalo especificado. Puede configurar una directiva de retención con duración definida que tiene como ámbito una versión de blob (versión preliminar) o un contenedor.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ed76c271590b4f5fbc79c7713b70186a8d9eb426
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552073"
---
# <a name="time-based-retention-policies-for-immutable-blob-data"></a>Directivas de retención con duración definida para datos de blobs inmutables

Una directiva de retención con duración definida almacena datos de blob en un formato WORM (una sola lectura, varias escrituras) para un intervalo especificado. Cuando se establece una directiva de retención con duración definida, los clientes pueden crear y leer blobs, pero no pueden modificarlos ni eliminarlos. Una vez transcurrido el intervalo de retención, los blobs se pueden eliminar pero no sobrescribir.

Para obtener más información sobre las directivas de inmutabilidad de Blob Storage, consulte [Almacenamiento de datos críticos para la empresa con almacenamiento inmutable](immutable-storage-overview.md).

## <a name="retention-interval-for-a-time-based-policy"></a>Intervalo de retención para una directiva con duración definida

El intervalo de retención mínimo para una directiva de retención con duración definida es de un día y el máximo es de 146 000 días (400 años).

Cuando configure una directiva de retención con duración definida, los objetos afectados permanecerán en el estado inmutable durante el período de retención *efectivo*. El período de retención efectivo para los objetos es igual a la diferencia entre la hora de creación del blob y el intervalo de retención especificado por el usuario. Debido a que se puede ampliar el intervalo de retención de una directiva, el almacenamiento inmutable usa el valor más reciente del intervalo de retención especificado por el usuario para calcular el período de retención efectivo.

Por ejemplo, supongamos que un usuario crea una directiva de retención de duración definida con un intervalo de retención de cinco años. Un blob existente en ese contenedor, *testblob1*, se ha creado hace un año; por tanto, el período de retención efectivo para *testblob1* es de cuatro años. Cuando se carga un nuevo blob, *testblob2*, en el contenedor, el período de retención efectivo de *testblob2* es de cinco años a partir de su creación.

## <a name="locked-versus-unlocked-policies"></a>Comparación de directivas bloqueadas y desbloqueadas

Cuando se configura por primera vez una directiva de retención con duración definida, la directiva está desbloqueada con fines de prueba. Cuando termine de realizar las pruebas, puede bloquear la directiva para que sea totalmente compatible con SEC 17a-4(f) y otro tipo de cumplimiento normativo.

Tanto las directivas bloqueadas como las desbloqueadas protegen contra eliminaciones y sobrescrituras. Sin embargo, puede acortar o ampliar el período de retención para modificar una directiva desbloqueada. Las directivas desbloqueadas también se pueden eliminar.

No es posible eliminar una directiva de retención con duración definida que esté bloqueada. Puede ampliar el período de retención, pero no puede acortarlo. Puede aumentar hasta cinco veces el período de retención efectivo durante la vigencia de una directiva bloqueada que esté definida en el nivel de contenedor. En el caso de una directiva configurada para una versión de blob, no hay ningún límite en el número de aumentos del período efectivo.

> [!IMPORTANT]
> Una directiva de retención con duración definida debe estar bloqueada para que el blob esté en estado inmutable (protegido frente a escritura y eliminación) y, por consiguiente, se cumplan SEC 17a-4(f) y otras regulaciones. Microsoft recomienda bloquear la directiva en un período razonable, normalmente antes de 24 horas. Si bien el estado desbloqueado proporciona protección de inmutabilidad, no se recomienda utilizarlo para otros fines que no sean las pruebas a corto plazo.

## <a name="time-based-retention-policy-scope"></a>Ámbito de las directivas de retención con duración definida

Se puede configurar una directiva de retención con duración definida en cualquiera de los ámbitos siguientes:

- Directiva en el nivel de versión (versión preliminar): se puede configurar una directiva de retención con duración definida para aplicarla en una versión de blob para la administración pormenorizada de datos confidenciales. Puede aplicar la directiva a una versión individual o configurar una directiva predeterminada para un contenedor que se aplicará de manera predeterminada a todos los blobs cargados en ese contenedor.
- Directiva en el nivel de contenedor: una directiva de retención de duración definida que está configurada en el nivel de contenedor se aplica a todos los objetos de ese contenedor. Los objetos individuales no se pueden configurar con sus propias directivas de inmutabilidad.

Los registros de auditoría están disponibles en el contenedor para las directivas de retención con duración definida en el nivel de versión y en el nivel de contenedor. Los registros de auditoría no están disponibles para una directiva que tiene como ámbito una versión de blob.

### <a name="version-level-policy-scope-preview"></a>Ámbito de directiva en el nivel de versión (versión preliminar)

Para configurar directivas de retención en el nivel de versión, primero debe habilitar la inmutabilidad en el nivel de versión del contenedor primario. La inmutabilidad en el nivel de versión no se puede deshabilitar una vez habilitada, aunque sí se pueden eliminar las directivas desbloqueadas. Para más información, consulte la sección sobre la [habilitación de la compatibilidad con la inmutabilidad en el nivel de versión de un contenedor](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container).

Puede habilitar la compatibilidad con la inmutabilidad en el nivel de versión al crear un contenedor. Los contenedores existentes también pueden admitir la inmutabilidad en el nivel de versión, pero primero deben pasar por un proceso de migración. Este proceso puede tardar algún tiempo y no se puede revertir. Para más información sobre cómo migrar un contenedor para admitir la inmutabilidad en el nivel de versión, consulte la sección sobre la [migración de un contenedor existente para admitir la inmutabilidad en el nivel de versión](immutable-policy-configure-version-scope.md#migrate-an-existing-container-to-support-version-level-immutability).

Las directivas de retención con duración definida en el nivel de versión requieren que el [control de versiones de blob](versioning-overview.md) esté habilitado para la cuenta de almacenamiento. Para obtener información sobre cómo habilitar el control de versiones de blobs, consulte [Habilitación y administración del control de versiones de blobs](versioning-enable.md). Tenga en cuenta que habilitar el control de versiones puede afectar a la facturación. Para más información, consulte la sección **Precios y facturación** de [Control de versiones de blobs](versioning-overview.md#pricing-and-billing).

Una vez habilitado el control de versiones, cuando se carga por primera vez un blob, esa versión del blob es la versión actual. Cada vez que se sobrescribe el blob, se crea una versión nueva que almacena el estado anterior del blob. Cuando elimina la versión actual de un blob, la versión actual se convierte en una versión actual y se retiene hasta que se elimina de manera explícita. Una versión de blob anterior posee la directiva de retención con duración definida que estaba en vigor cuando la versión actual se convirtió en una versión anterior.

Si hay una directiva predeterminada en vigor para el contenedor, cuando una operación de sobrescritura crea una versión anterior, la versión actual nueva hereda la directiva predeterminada para el contenedor.

Cada versión puede tener configurada solo una directiva de retención con duración definida. Una versión también puede tener configurada una suspensión legal. Para más detalles sobre las configuraciones de directivas de inmutabilidad compatibles en función del ámbito, consulte [Ámbito de las directivas de inmutabilidad](immutable-storage-overview.md#immutability-policy-scope).

Para aprender a configurar las directivas de retención con duración definida en el nivel de versión, consulte [Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)](immutable-policy-configure-version-scope.md).

> [!IMPORTANT]
> Las directivas de retención con duración definida en el nivel de versión están actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
> La inmutabilidad en el nivel de versión puede tardar hasta 30 segundos en habilitarse antes de configurar directivas de retención con duración definida en el nivel de versión.

#### <a name="configure-a-policy-on-the-current-version"></a>Configuración de una directiva en la versión actual

Después de habilitar la compatibilidad con la inmutabilidad en el nivel de versión para un contenedor, tiene la opción de configurar una directiva de retención con duración definida predeterminada para el contenedor. Al configurar una directiva de retención con duración definida predeterminada para el contenedor y, a continuación, cargar un blob, el blob hereda esa directiva de manera predeterminada. También puede optar por invalidar la directiva predeterminada para cualquier blob en la carga mediante la configuración de una directiva personalizada para ese blob.

Si la directiva de retención con duración definida predeterminada para el contenedor está desbloqueada, la versión actual de un blob que hereda la directiva predeterminada también tendrá una directiva desbloqueada. Después de cargar un blob individual, puede acortar o ampliar el período de retención de la directiva en la versión actual del blob, o bien eliminar la versión actual. También puede bloquear la directiva para la versión actual, incluso si la directiva predeterminada en el contenedor permanece desbloqueada.

Si la directiva de retención con duración definida predeterminada para el contenedor está bloqueada, la versión actual de un blob que hereda la directiva predeterminada también tendrá una directiva bloqueada. Sin embargo, si invalida la directiva predeterminada al cargar un blob estableciendo una directiva solo para ese blob, la directiva de ese blob permanecerá desbloqueada hasta que la bloquee explícitamente. Cuando la directiva de la versión actual está bloqueada, puede ampliar el intervalo de retención, pero no puede eliminar la directiva ni acortar el intervalo de retención.

Si no hay ninguna directiva predeterminada configurada para un contenedor, puede cargar un blob con una directiva personalizada o sin ninguna directiva.

Si se modifica la directiva predeterminada en un contenedor, las directivas de los objetos dentro de ese contenedor permanecen sin cambios, incluso si se heredaron de la directiva predeterminada.

En la tabla siguiente se muestran las distintas opciones disponibles para establecer una directiva de retención con duración definida en un blob durante la carga:

| Estado de la directiva predeterminada en el contenedor | Carga de un blob con la directiva predeterminada | Carga de un blob con una directiva personalizada | Carga de un blob sin ninguna directiva |
|--|--|--|--|
| Directiva predeterminada en el contenedor (desbloqueada) | El blob se carga con la directiva predeterminada desbloqueada | El blob se carga con la directiva personalizada desbloqueada | El blob se carga sin ninguna directiva |
| Directiva predeterminada en el contenedor (bloqueada) | El blob se carga con la directiva predeterminada bloqueada | El blob se carga con la directiva personalizada desbloqueada | El blob se carga sin ninguna directiva |
| No hay ninguna directiva predeterminada en el contenedor | N/D | El blob se carga con la directiva personalizada desbloqueada | El blob se carga sin ninguna directiva |

#### <a name="configure-a-policy-on-a-previous-version"></a>Configuración de una directiva en una versión anterior

Cuando el control de versiones está habilitado, una operación de escritura o eliminación en un blob crea una versión anterior nueva de ese blob que guarda el estado del blob antes de la operación. De manera predeterminada, una versión anterior posee la directiva de retención con duración definida que estaba en vigor para la versión actual, si la hubiese, cuando la versión actual se convirtió en una versión anterior. La versión actual nueva hereda la directiva en el contenedor, si la hay.

Si la directiva heredada por una versión anterior está desbloqueada, es posible acortar o ampliar el intervalo de retención, o bien se puede eliminar la directiva. La directiva de una versión anterior también se puede bloquear para esa versión, incluso si la directiva de la versión actual está desbloqueada.

Si la directiva heredada por una versión anterior está bloqueada, el intervalo de retención se puede ampliar. La directiva no se puede eliminar ni se puede acortar el intervalo de retención.

Si no hay ninguna directiva configurada en la versión actual, la versión anterior no hereda ninguna directiva. Puede configurar una directiva personalizada para la versión.

Si se modifica la directiva de una versión actual, las directivas de las versiones anteriores existentes permanecen sin cambios, incluso si la directiva se heredó de una versión actual.

### <a name="container-level-policy-scope"></a>Ámbito de directiva en el nivel de contenedor

Una directiva de retención con duración definida en el nivel de contenedor se aplica a todos los objetos de un contenedor, tanto nuevos como existentes. En el caso de una cuenta con un espacio de nombres jerárquico, una directiva en el nivel de contenedor también se aplica a todos los directorios del contenedor.

Cuando se aplica una directiva de retención con duración definida a un contenedor, todos los blobs existentes pasan al estado WORM inmutable en menos de 30 segundos. Todos los nuevos blobs que se carguen en ese contenedor protegido mediante directiva también pasarán a un estado inmutable. Una vez que todos los blobs estén en un estado inmutable, no se permite ninguna operación de sobrescritura o eliminación en el contenedor inmutable. En el caso de una cuenta con un espacio de nombres jerárquico, no se puede cambiar el nombre de los blobs ni moverlos a un directorio diferente.

Los límites siguientes se aplican a las directivas de retención en el nivel de contenedor:

- En el caso de una cuenta de almacenamiento, el número máximo de contenedores con directivas inmutables de duración definida bloqueadas es 10 000.
- En el caso de un contenedor, el número máximo de modificaciones para ampliar el intervalo de retención de una directiva con duración definida bloqueada es cinco.
- En el caso de un contenedor, se retiene un máximo de siete registros de auditoría de la directiva de retención con duración definida para una directiva bloqueada.

Para información sobre cómo configurar una directiva de retención con duración definida en un contenedor, consulte [Configuración de directivas de inmutabilidad para contenedores](immutable-policy-configure-container-scope.md).

## <a name="allow-protected-append-blobs-writes"></a>Permitir escrituras de blobs en anexos protegidos

Los blobs en anexos se componen de bloques de datos y se optimizan para las operaciones de anexión de datos necesarias en escenarios de auditoría y registro. Por diseño, los blobs en anexos solo permiten agregar nuevos bloques al final del blob. Con independencia de la inmutabilidad, la modificación o eliminación de los bloques existentes en un blob en anexos básicamente no se permite. Para más información sobre los blobs en anexos, vea [Acerca de los blobs en anexos](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Solo las directivas de retención con duración definida tienen una configuración de propiedad **AllowProtectedAppendWrites** que permite escribir bloques nuevos en un blob en anexos al tiempo que se mantiene la protección y el cumplimiento de inmutabilidad. Si se habilita esta configuración, puede crear un blob en anexos directamente en el contenedor protegido por la directiva y, luego, continuar para agregar bloques nuevos de datos al final del blob en anexos con la operación Anexar bloque. Solo se pueden agregar bloques nuevos y los bloques existentes no se pueden modificar ni eliminar. Se sigue aplicando la protección de inmutabilidad de retención de tiempo, lo que impide la eliminación del blob en anexos hasta que haya transcurrido el período de retención vigente. La habilitación de esta configuración no afecta al comportamiento de inmutabilidad de los blobs en bloques ni los blobs en páginas.

Como esta configuración forma parte de una directiva de retención de duración definida, los blobs en anexos todavía pueden permanecer en el estado inmutable durante el período de retención *efectivo*. Como se pueden anexar nuevos datos más allá de la creación inicial del blob en anexos, hay una ligera diferencia en el modo de determinar el período de retención. El período de retención efectivo es la diferencia entre la hora de última modificación del blob en anexos y el intervalo de retención especificado por el usuario. Como sucede al ampliar el intervalo de retención, el almacenamiento inmutable usa el valor más reciente del intervalo de retención especificado por el usuario para calcular el período de retención efectivo.

Por ejemplo, imagine que un usuario crea una directiva de retención de duración definida con la propiedad **AllowProtectedAppendWrites** habilitada y un intervalo de retención de 90 días. En el día de hoy, se crea un blob en anexos, *logblob1*, en el contenedor y se siguen agregando registros nuevos al blob en anexos durante los próximos 10 días; por lo tanto, el período de retención efectivo de *logblob1* es de 100 días a partir de hoy (la hora de la última anexión + 90 días).

Las directivas de retención de duración definida desbloqueadas permiten habilitar y deshabilitar la configuración de propiedad **AllowProtectedAppendWrites** en cualquier momento. Una vez que se ha bloqueado la directiva de retención de duración definida, no se puede cambiar la configuración de propiedad **AllowProtectedAppendWrites**.

## <a name="audit-logging"></a>Registro de auditoría

Cada contenedor con una directiva de retención con duración definida habilitada proporciona un registro de auditoría de directivas. El registro de auditoría incluye hasta siete comandos de retención con duración definida para directivas de retención con duración definida bloqueadas. Las entradas de registro incluyen el identificador de usuario, el tipo de comando, las marcas de tiempo y el intervalo de retención. El registro de auditoría se conserva mientras dure la directiva, de acuerdo con las directrices de regulación SEC 17a-4(f).

El [registro de actividad de Azure](../../azure-monitor/essentials/platform-logs-overview.md) ofrece un registro más completo de todas las actividades del servicio de administración. Los [registros de recursos de Azure](../../azure-monitor/essentials/platform-logs-overview.md) conservan información sobre las operaciones de datos. Es responsabilidad del usuario almacenar dichos registros de forma persistente, ya que podría ser obligatorio por ley o por otros fines.

No se auditan los cambios en las directivas de retención con duración definida en el nivel de versión.

## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre la protección de datos](data-protection-overview.md)
- [Almacenamiento inmutable de los datos críticos para la empresa en Azure Blob Storage](immutable-storage-overview.md)
- [Retenciones legales para datos de blob inmutables](immutable-legal-hold-overview.md)
- [Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)](immutable-policy-configure-version-scope.md)
- [Configuración de directivas de inmutabilidad para contenedores](immutable-policy-configure-container-scope.md)
