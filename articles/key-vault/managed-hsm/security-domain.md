---
title: Acerca del dominio de seguridad de HSM administrado de Azure
description: Información general del dominio de seguridad de HSM administrado, un conjunto de credenciales básicas necesarias para recuperar un HSM administrado
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.openlocfilehash: b309b4e9189c551ed40e0dac1ad45de47f7632d6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443563"
---
# <a name="about-the-managed-hsm-security-domain"></a>Acerca del dominio de seguridad de HSM administrado

El dominio de seguridad de HSM administrado (SD) es un conjunto de credenciales básicas necesarias para recuperar un HSM administrado en caso de desastre. El dominio de seguridad se genera en el hardware de HSM administrado y en los enclaves del software de servicio y representa la "propiedad" del HSM.

Cada HSM administrado debe tener un dominio de seguridad para funcionar. Cuando se solicita un nuevo HSM administrado, se aprovisiona, pero no se activa hasta que se descarga el dominio de seguridad. Cuando un HSM administrado está en estado aprovisionado, pero no activado, hay dos maneras de activarlo:
- Descargar el dominio de seguridad es el método predeterminado, y permite almacenar el dominio de seguridad de forma segura para usarlo con otro HSM administrado o para recuperarse de un desastre total.
- Cargar un dominio de seguridad existente que ya tenga, lo que le permitirá crear varias instancias de HSM administrado que compartan el mismo dominio de seguridad.

## <a name="download-your-security-domain"></a>Descarga del dominio de seguridad

Cuando se aprovisiona un HSM administrado, pero no se activa, al descargar el dominio de seguridad se capturan las credenciales básicas necesarias para recuperarse de una pérdida completa de todo el hardware. Para descargar el dominio de seguridad, debe crear al menos tres pares de claves RSA (10 como máximo) y enviar estas claves públicas al servicio al solicitar la descarga del dominio de seguridad. También debe especificar el número mínimo de claves necesarias (cuórum) para descifrar el dominio de seguridad en el futuro. El HSM administrado inicializará el dominio de seguridad y lo cifrará con las claves públicas que proporcione mediante el [algoritmo de uso compartido de secretos de Shamir](https://dl.acm.org/doi/10.1145/359168.359176). El blob del dominio de seguridad que descargue solo se puede descifrar si hay al menos un cuórum de claves privadas disponibles; para garantizar la seguridad del dominio de seguridad, debe proteger las claves privadas. Una vez finalizada la descarga, el HSM administrado estará en estado activado y listo para su uso.  

> [!IMPORTANT]
> Para una recuperación ante desastres completa, debe tener el dominio de seguridad, el cuórum de las claves privadas que se usaron para protegerlo y una copia de seguridad completa del HSM. Si pierde el dominio de seguridad o un número suficiente de claves RSA (clave privada) para perder el cuórum y no hay ninguna instancia en ejecución del HSM administrado, no será posible la recuperación ante desastres.

## <a name="upload-a-security-domain"></a>Carga de un dominio de seguridad

Cuando se aprovisiona un HSM administrado, pero no se activa, puede iniciar un proceso de recuperación del dominio de seguridad. El HSM administrado generará un par de claves RSA y devolverá la clave pública. Después, puede cargar el dominio de seguridad en el HSM administrado. Antes de la carga, el cliente (la CLI de Azure o PowerShell) deberá proporcionarle el número mínimo de cuórums de claves privadas que usó al descargar el dominio de seguridad. El cliente descifrará el dominio de seguridad mediante este cuórum y lo volverá a cifrar con la clave pública que descargó cuando solicitó la recuperación. Una vez finalizada la carga, el HSM administrado estará en estado activado.

## <a name="backup-and-restore-behavior"></a>Tareas de copia de seguridad y restauración

Las copias de seguridad (copia de seguridad completa o copia de seguridad de una sola clave) solo se pueden restaurar correctamente si el HSM administrado de origen (donde se creó la copia de seguridad) y el HSM administrado de destino (donde se restaurará la copia de seguridad) comparten el mismo dominio de seguridad. De esta manera, un dominio de seguridad también define un límite criptográfico para cada HSM administrado.

## <a name="next-steps"></a>Pasos siguientes

- Lea una [introducción a HSM administrado](overview.md).
- Aprenda sobre la [administración de HSM administrado con la CLI de Azure](key-management.md).
- Revise los [procedimientos recomendados de HSM administrado](best-practices.md).
