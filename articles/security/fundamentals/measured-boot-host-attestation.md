---
title: 'Atestación del host y arranque medido del firmware: seguridad de Azure'
description: Información general técnica sobre la atestación del host y el arranque medido del firmware de Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: 6dac3b8230a0bad9c6492b236eac5dddc1839471
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895785"
---
# <a name="measured-boot-and-host-attestation"></a>Atestación del host y arranque medido
En este artículo se describe cómo Microsoft garantiza la integridad y la seguridad de los hosts mediante la atestación del host y el arranque medido.

## <a name="measured-boot"></a>Arranque medido

El [Módulo de plataforma segura](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) es un componente de auditoría seguro desde el punto de vista criptográfico y a prueba de alteraciones con firmware suministrado por un tercero de confianza. El registro de configuración de arranque contiene medidas encadenadas por hash anotadas en sus Registros de configuración de la plataforma (PCR) de cuando el host realizó por última vez la secuencia de arranque. En la siguiente ilustración se muestra este proceso de registro. Para crear cadenas de hash, se agrega una medida a la que se ha aplicado un algoritmo hash a otra medida con hash, se ejecuta el algoritmo hash en la unión, y así sucesivamente.

![Diagrama que muestra el encadenamiento por hash del servicio de atestación de host.](./media/measured-boot-host-attestation/hash-chaining.png)

La atestación se lleva a cabo cuando un host aporta una prueba de su estado de configuración mediante su registro de configuración de arranque (TCGLog). La falsificación de un registro de arranque es difícil porque el TPM no expone valores de PCR que no sean las operaciones de lectura y extensión. Además, las credenciales proporcionadas por el servicio de atestación del host están selladas para valores específicos de PCR. El uso del encadenamiento por hash hace que sea imposible a nivel computacional suplantar o anular la protección de las credenciales fuera de banda.

## <a name="host-attestation-service"></a>Servicio de atestación del host

El servicio de atestación del host es una medida preventiva que comprueba que las máquinas host sean de confianza antes de que se les permita interactuar con los datos o las cargas de trabajo del cliente. Para efectuar tales comprobaciones, este servicio valida la declaración de cumplimiento (prueba verificable del cumplimiento del host) que envía cada host con una directiva de atestación (definición del estado seguro). La integridad de este sistema está garantizada por una [raíz de confianza](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) que proporciona un TPM.

El servicio de atestación del host está presente en todos los clústeres de Azure dentro de un entorno de bloqueo especializado. El entorno bloqueado incluye otros servicios de equipo selector que participan en el protocolo de arranque de la máquina host. Una infraestructura de clave pública (PKI) actúa como intermediario para validar la procedencia de las solicitudes de atestación y como emisor de identidades (en función de la atestación correcta del host). Las credenciales posteriores a la atestación emitidas al host de atestación están selladas para su identidad. Solo el host solicitante puede quitar el sello a las credenciales y aprovecharlas para obtener permisos incrementales. Esto evita ataques de tipo "man in-middle" y de suplantación de identidad.

Si un host de Azure llega de fábrica con una configuración incorrecta de seguridad o se ha alterado en el centro de datos, su TCGLog contiene indicadores de peligro que el servicio de atestación del host marcó tras la siguiente atestación, lo que produce un error de esta operación. Los errores de atestación impiden que la flota de Azure confíe en el host infractor. Esta prevención bloquea de forma efectiva todas las comunicaciones hacia el host y desde allí y desencadena un flujo de trabajo de incidentes. Para determinar las causas principales y las posibles señales de peligro, se realiza una investigación y un análisis detallado posterior. Solo una vez que se completa el análisis se corrige el host y tiene la oportunidad de unirse a la flota de Azure y ocuparse de las cargas de trabajo de los clientes.

La siguiente es una arquitectura de alto nivel del servicio de atestación del host:

![Diagrama que muestra la arquitectura del servicio de atestación del host.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Medidas de atestación

Estos son ejemplos de las muchas mediciones capturadas hoy.

### <a name="secure-boot-and-secure-boot-keys"></a>Arranque seguro y claves de arranque seguro
Al validar que la base de datos de firmas y los resúmenes de la base de datos de firmas revocadas son correctos, el servicio de atestación del host garantiza que el agente cliente tiene en cuenta el software adecuado. Mediante la validación de las firmas de la base de datos de claves de inscripción de claves públicas y la clave de plataforma pública, el servicio de atestación del host confirma que solo las entidades de confianza tienen permiso para modificar las definiciones del software que se considera de confianza. Por último, al garantizar que el arranque seguro está activo, el servicio de atestación del host valida que se aplican estas definiciones.

### <a name="debug-controls"></a>Controles de depuración
Los depuradores son herramientas eficaces para los desarrolladores. No obstante, el acceso sin restricciones a los comandos de depuración de memoria y otros comandos de depuración podría debilitar la protección de los datos y la integridad del sistema si se proporciona a una entidad que no es de confianza. El servicio de atestación del host garantiza que cualquier tipo de depuración está deshabilitada en las máquinas de producción.

### <a name="code-integrity"></a>Integridad del código
El [arranque seguro](secure-boot.md) de UEFI garantiza que solo se puede ejecutar software de bajo nivel de confianza durante la secuencia de arranque. Sin embargo, las mismas comprobaciones se deben aplicar también en el entorno posterior al arranque a los controladores y otros ejecutables con acceso en modo kernel. Para ello, se usa una directiva de integridad del código (CI) para definir qué controladores, archivos binarios y otros ejecutables se consideran de confianza mediante la especificación de firmas válidas y no válidas. Estas directivas se aplican. Las infracciones de la directiva generan alertas al equipo de respuesta a incidentes de seguridad para que los investiguen.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre lo que se hace para fomentar la seguridad y la integridad de la plataforma, vea:

- [Seguridad de firmware](firmware.md)
- [Integridad del código de plataforma](code-integrity.md)
- [Arranque seguro](secure-boot.md)
- [Proyecto Cerberus](project-cerberus.md)
- [Cifrado en reposo](encryption-atrest.md)
- [Seguridad de Hypervisor](hypervisor.md)