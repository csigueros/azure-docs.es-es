---
title: Conceptos sobre laboratorios en Azure Lab Services | Microsoft Docs
description: Conozca los conceptos básicos de Lab Services y cómo este puede facilitar la creación y administración de laboratorios.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 59e0fdb572f4a27658afdb42f270c67d76b5fcd0
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181281"
---
# <a name="labs-concepts"></a>Conceptos de laboratorios

La lista siguiente contiene las definiciones y los conceptos clave de Lab Services:

## <a name="quota"></a>Quota

Cuota es el límite de tiempo (en horas) que puede establecer un formador para que un alumno use una VM de laboratorio. Se puede establecer en 0, o en un número específico de horas. Si la cuota se establece en 0, el alumno solo podrá usar la máquina virtual cuando se ejecute una programación o cuando un formador active manualmente la máquina virtual para el alumno.  

Las horas de cuota se contabilizan cuando el alumno inicia la máquina virtual de laboratorio por su cuenta.  Si un formador inicia manualmente la VM de laboratorio de un alumno, no se usan las horas de cuota de ese alumno.

## <a name="schedules"></a>Programaciones

Las programaciones son los períodos de tiempo que un formador puede crear para la clase, de modo que las VM de los alumnos estén disponibles para el horario de clase.  Las programaciones pueden ser para una sola vez o periódicas.  Las horas de cuota no se utilizan cuando se ejecuta una programación.

Hay tres tipos de programaciones: Estándar, Solo inicio y Solo detención.

- **Estándar**.  Esta programación iniciará las VM de todos los alumnos a la hora de inicio especificada y las apagará a la hora de detención especificada.
- **Solo inicio**.   Esta programación iniciará las VM de todos los alumnos a la hora especificada.  Las VM no se detendrán hasta que cada alumno detenga su VM a través del portal de Azure Lab Services o se realice una programación de solo detención.
- **Solo detención**.  Esta programación detendrá las VM de todos los alumnos a la hora especificada.  

## <a name="template-virtual-machine"></a>Máquina virtual de plantilla

En un laboratorio, una máquina virtual de plantilla es una imagen de máquina virtual base a partir de la que se crean las máquinas virtuales de todos los usuarios. Los instructores y creadores de laboratorios configuran la máquina virtual de plantilla con el software que ellos desean proporcionar a los asistentes al curso para que realicen los laboratorios. Cuando publica una máquina virtual de plantilla, Azure Lab Services crea o actualiza las máquinas virtuales del laboratorio según esta máquina virtual de plantilla.

## <a name="user-profiles"></a>Perfiles de usuario

En este artículo se describen distintos perfiles de usuario de Azure Lab Services.

### <a name="lab-account-owner"></a>Propietario de la cuenta del laboratorio

Por lo general, un administrador de TI de los recursos de nube de la organización, que es el propietario de la suscripción de Azure, actúa como propietario de la cuenta del laboratorio y realiza las siguientes tareas:

- Configura una cuenta de laboratorio para la organización.
- Administra y configura las directivas en todos los laboratorios.
- Concede permisos a personas de la organización para crear un laboratorio en la cuenta del laboratorio.

### <a name="educator"></a>Profesor

Normalmente, usuarios como un profesor o un instructor en línea, crean laboratorios en una cuenta de laboratorio. Un profesor realiza las siguientes tareas:

- Crea un laboratorio educativo.
- Crea máquinas virtuales en el laboratorio.
- Instala el software adecuado en máquinas virtuales.
- Especifica quién puede obtener acceso al laboratorio.
- Proporciona vínculos de registro en el laboratorio para los alumnos.

### <a name="student"></a>Estudiante

El alumno hace las siguientes tareas:

- Usa el vínculo de registro al laboratorio que el usuario recibe de un creador de laboratorio para registrarse.
- Se conecta a una máquina virtual del laboratorio y la usa para realizar trabajos de clase, tareas y proyectos.

## <a name="next-steps"></a>Pasos siguientes

Introducción a la configuración de una cuenta de laboratorio necesaria para crear un laboratorio educativo mediante Azure Lab Services:

- [Configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md)
