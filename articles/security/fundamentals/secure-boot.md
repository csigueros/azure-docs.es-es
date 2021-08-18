---
title: 'Arranque seguro de firmware: seguridad de Azure'
description: Información general técnica sobre el arranque seguro de firmware de Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e14bd525f2f81cfdf0a61f29df919f5579f0be17
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893949"
---
# <a name="secure-boot"></a>Arranque seguro

Arranque seguro es una característica de [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) que requiere que todos los componentes de software y firmware de bajo nivel se comprueben antes de que se carguen. Durante el arranque, Arranque seguro de UEFI comprueba la firma de cada parte del software de arranque, lo que incluye controladores de firmware UEFI (también conocidos como ROM de opciones), las aplicaciones de Extensible Firmware Interface (EFI) y los controladores y archivos binarios del sistema operativo. Si las firmas son válidas o el fabricante de equipos originales (OEM) confía en ellas, la máquina arranca y el firmware proporciona el control al sistema operativo.

## <a name="components-and-process"></a>Componentes y proceso

Arranque seguro se basa en estos componentes críticos:

- Clave de plataforma (PK): establece confianza entre el propietario de la plataforma (Microsoft) y el firmware. La mitad pública es PKpub, mientras que la mitad privada es PKpriv.
- Base de datos de claves de inscripción de claves (KEK): establece confianza entre el sistema operativo y el firmware de la plataforma. La mitad pública es KEKpub, mientras que la mitad privada es KEKpriv.
- Base de datos de firmas (db): contiene los códigos hash de los firmantes de confianza (claves públicas y certificados) de los módulos del código de firmware y software autorizados para interactuar con el firmware de la plataforma.
- Base de datos de firmas revocadas (dbx): contiene los códigos hash revocados de los módulos de código que se han identificado como malintencionados, vulnerables, en peligro o que no son de confianza. Si un hash está en la base de datos de firmas y en la base de datos de firmas revocadas, tiene prioridad esta última.

En la siguiente ilustración y proceso se explican cómo se actualizan estos componentes:

![Diagrama que muestra los componentes de Arranque seguro.](./media/secure-boot/secure-boot.png)

El fabricante de equipos originales almacena los códigos hash de Arranque seguro en la RAM no volátil (NV-RAM) de la máquina en el momento de la fabricación.

1. La base de datos de firmas (db) se rellena con los firmantes o los hash de imagen de las aplicaciones UEFI, los cargadores del sistema operativo (como el cargador de sistema operativo de Microsoft o el administrador de arranque) y los controladores de UEFI de confianza.
2. La base de datos de firmas revocadas (dbx) se rellena con códigos hash de módulos que ya no son de confianza.
3. La base de datos de claves de inscripción de claves (KEK) se rellena con claves de firma que se pueden utilizar para actualizar la base de datos de firmas y la base de datos de firmas revocadas. Las bases de datos se pueden editar mediante actualizaciones que se firman con la clave correcta, o bien a través de actualizaciones realizadas por un usuario autorizado presente físicamente mediante menús de firmware.
4. Después de que se hayan agregado las bases de datos db, dbx y KEK, y de que la validación del firmware y de las pruebas haya finalizado, el OEM bloquea el firmware, para que no pueda editarse, y genera una clave de plataforma (PK), que se puede usar para firmar las actualizaciones del KEK o para desactivar Arranque seguro.

Durante cada fase del proceso de arranque, se calculan los códigos hash del firmware, cargador de arranque, sistema operativo, controladores de kernel y otros artefactos de la cadena de arranque, y se comparan con los valores aceptables. No se permite cargar firmware y software que se detecte que no son de confianza. Por consiguiente, se pueden bloquear la inyección de malware de bajo nivel o los ataques de malware de prearranque.

## <a name="secure-boot-on-the-azure-fleet"></a>Arranque seguro en la flota de Azure
En la actualidad, todos los equipos que se incorporan e implementan en la flota de proceso de Azure para hospedar las cargas de trabajo de los clientes vienen de fábrica con Arranque seguro habilitado. Los procesos y las herramientas de destino están en marcha durante todas las fases de la creación del hardware y en la canalización de integración para asegurarse de que la habilitación de Arranque seguro no se revierta accidentalmente ni de forma malintencionada.

La validación de que los códigos hash db y dbx son correctos garantiza que:

- El cargador de arranque está presente en una de las entradas de la base de datos.
- La firma del cargador es válida.
- El host arranca con software de confianza.

 Mediante la validación de las firmas de KEKpub y PKpub podemos confirmar que solo las entidades de confianza tienen permiso para modificar las definiciones de qué software se considera de confianza. Por último, al garantizar que el arranque seguro está activo, es posible validar que se aplican estas definiciones.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre lo que se hace para fomentar la seguridad y la integridad de la plataforma, vea:

- [Seguridad de firmware](firmware.md)
- [Integridad del código de plataforma](code-integrity.md)
- [Atestación de host y de arranque medido](measured-boot-host-attestation.md)
- [Proyecto Cerberus](project-cerberus.md)
- [Cifrado en reposo](encryption-atrest.md)
- [Seguridad de Hypervisor](hypervisor.md)