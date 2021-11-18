---
title: 'Descripción de los precios de acceso por usuario de Azure Virtual Desktop para el streaming de aplicaciones remotas: Azure'
description: Introducción a las consideraciones de licencias de Azure Virtual Desktop para el streaming de aplicaciones remotas.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 54f164c60a5602bcfe798c98785fca1aa8513279
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292865"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>Descripción de las licencias y los precios de acceso por usuario

En este artículo se explican los requisitos de las licencias para usar Azure Virtual Desktop a fin de hacer streaming de aplicaciones remotas a usuarios externos. En este artículo, obtendrá información de las diferencias entre las licencias de Azure Virtual Desktop para usuarios externos y las de los usuarios internos, los detalles de cómo funcionan los precios de acceso por usuario y cómo obtener licencias de otros productos que planea usar con Azure Virtual Desktop.

## <a name="internal-users-and-external-users"></a>Usuarios internos y externos

En el contexto de proporcionar infraestructura virtualizada con Azure Virtual Desktop, los **usuarios internos** son miembros de su organización, como empleados de una empresa o alumnos de una escuela. Los **usuarios externos** no son miembros de la organización; por ejemplo, puede tratarse de los clientes de una empresa.

>[!NOTE]
>Tenga cuidado de no confundir *usuarios* externos con *identidades* externas. Actualmente, Azure Virtual Desktop no admite identidades externas, incluidas las cuentas de invitado o las identidades de negocio a negocio (B2B). Tanto si atiende a usuarios internos como a usuarios externos con Azure Virtual Desktop, usted mismo deberá crear y administrar las identidades de esos usuarios. Los precios de acceso por usuario no son una manera de habilitar cuentas de usuario invitado con Azure Virtual Desktop. Para más información, consulte las [recomendaciones relativas a la arquitectura](architecture-recs.md).

Las licencias de Azure Virtual Desktop funcionan de manera distinta para los usuarios internos y externos. Considere los siguientes ejemplos:

- Una empresa de fabricación llamada Fabrikam, Inc. puede usar Azure Virtual Desktop para proporcionar a sus propios empleados acceso a estaciones de trabajo virtuales y aplicaciones de línea de negocio. Como Fabrikam atiende a usuarios internos, debe adquirir una de las licencias aptas que aparecen en los [Precios de Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) para cada uno de los empleados que accederán a Azure Virtual Desktop.

- Un proveedor de software llamado Contoso podría usar Azure Virtual Desktop para vender secuencias remotas de la aplicación de productividad de Contoso a los clientes de Contoso (usuarios externos). Como Contoso atiende a usuarios externos, se debe inscribir en los precios de acceso por usuario de Azure Virtual Desktop. Esto permite que Contoso pague los derechos de acceso de Azure Virtual Desktop en nombre de los usuarios externos que se conectan a la implementación de Contoso. Los usuarios no necesitan una licencia independiente como Microsoft 365 para acceder a Azure Virtual Desktop. Contoso todavía necesita crear y administrar identidades para esos usuarios externos.

## <a name="per-user-access-pricing-for-azure-virtual-desktop"></a>Precios de acceso por usuario para Azure Virtual Desktop

Los precios de acceso por usuario le permiten pagar los derechos de acceso de Azure Virtual Desktop en nombre de usuarios externos. Debe inscribirse en los precios de acceso por usuario para crear una implementación compatible para usuarios externos. Para más información, vea [Inscripción en los precios de acceso por usuario](per-user-access-pricing.md).

Se pagan los precios de acceso por usuario a través de la suscripción o suscripciones de Azure inscritas, además de los cargos por máquinas virtuales, almacenamiento y otros servicios de Azure. En cada ciclo de facturación, solo se paga por los usuarios que realmente usaron el servicio. Solo los usuarios que se conectan al menos una vez a Azure Virtual Desktop durante ese mes generan un cargo de acceso.

Hay dos franjas de precios de acceso por usuario de Azure Virtual Desktop. Los cargos se determinan automáticamente en cada ciclo de facturación en función del tipo de [grupos de aplicaciones](../environment-setup.md#app-groups) a los que se conecta un usuario.

- La primera franja de precios se denomina "Aplicaciones". Este precio plano se cobra por cada usuario que accede al menos a un grupo de aplicaciones de RemoteApp y a cero grupos de aplicaciones de escritorio.
- La segunda franja es "Aplicaciones y escritorios". Este precio plano se cobra por cada usuario que accede al menos a un grupo de aplicaciones de escritorio.
- Si un usuario no accede a ningún grupo de aplicaciones, no hay ningún cargo.

Para más información sobre precios, consulte [Precios de Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

Cada franja de precios tiene cargos de acceso por usuario planos. Por ejemplo, un usuario genera en el mismo cargo en su suscripción, independientemente de cuándo o cuántas horas haya usado el servicio durante ese ciclo de facturación.

Azure Virtual Desktop también cobrará a los usuarios con licencias independientes asignadas que les autorizan de otro modo a acceder a Azure Virtual Desktop. Si tiene usuarios internos para los que compra licencias aptas, se recomienda darles acceso a Azure Virtual Desktop a través de una suscripción independiente que no esté inscrita en los precios de acceso por usuario para evitar pagar dos veces por esos usuarios de forma eficaz.

Azure Virtual Desktop emitirá como máximo un cargo de acceso para un usuario determinado en un período de facturación determinado. Por lo tanto, si la implementación concede al usuario Alice el acceso a los recursos de Azure Virtual Desktop en dos suscripciones de Azure diferentes del mismo inquilino, solo la primera suscripción a la que accede Alice generará un cargo por uso.

## <a name="comparing-licensing-options"></a>Comparación de las opciones de licencia

Este es un resumen de los dos tipos de licencias de Azure Virtual Desktop entre los que puede elegir:

- Una licencia de Windows o Microsoft 365 válida:
  - Concede derechos de acceso de Azure Virtual Desktop solo a los usuarios internos.
  - Se paga por adelantado a través de una suscripción.
  - Tiene el mismo coste por usuario cada mes, independientemente del comportamiento del usuario.
  - Incluye derechos a otros productos y servicios de Microsoft.

- Precios de acceso por usuario:
  - Concede derechos de acceso de Azure Virtual Desktop solo a los usuarios externos.
  - Se paga por uso a través de un medidor de Azure.
  - El costo por usuario cada mes depende del comportamiento del usuario.
  - Solo incluye derechos de acceso a Azure Virtual Desktop.

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>Licencias de otros productos y servicios para su uso con Azure Virtual Desktop

La licencia de acceso por usuario de Azure Virtual Desktop no reemplaza por completo una licencia de Windows o Microsoft 365. Las licencias por usuario solo conceden derechos de acceso a Azure Virtual Desktop y no incluyen Microsoft Office, Microsoft 365 Defender ni Impresión universal. Esto significa que si elige una licencia por usuario, deberá conceder licencias por separado de otros productos y servicios para conceder a los usuarios el acceso a ellos en el entorno de Azure Virtual Desktop.

## <a name="next-steps"></a>Pasos siguientes

Ahora que está familiarizado con las opciones de precios de licencias, puede empezar a planear el entorno de Azure Virtual Desktop. Aquí tiene algunos artículos que pueden ayudarle:

- [Inscripción de la suscripción en los precios de acceso por usuario](per-user-access-pricing.md)
- [Estimación de los costes de streaming de aplicaciones por usuario para Azure Virtual Desktop](streaming-costs.md)
- Si está listo para empezar a configurar la primera implementación, empiece a trabajar con nuestros [Tutoriales](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json).
