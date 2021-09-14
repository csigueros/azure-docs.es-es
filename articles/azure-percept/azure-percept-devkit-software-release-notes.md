---
title: Notas de la versión del software Azure Percept DK
description: Información sobre los cambios realizados en el software Azure Percept DK.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: f957ad88d89b9d31ab8e073214e765dbbdb13094
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224925"
---
# <a name="azure-percept-dk-software-release-notes"></a>Notas de la versión del software Azure Percept DK

En esta página se proporciona información sobre los cambios y las correcciones de cada versión de firmware y sistema operativo de Azure Percept DK.

Para descargar las imágenes de actualización, consulte [Versiones del software Azure Percept DK para realizar actualizaciones a través de USB](./software-releases-usb-cable-updates.md) o [Versiones de software Azure Percept DK para la actualización OTA](./software-releases-over-the-air-updates.md).

## <a name="july-2107-release"></a>Versión de julio (2107)

> [!IMPORTANT]
> Debido a un cambio de firma de código, el paquete OTA (Over-The-Air) para esta versión solo es compatible con las instancias de Azure Percept DK que ejecutan la versión 2106. En el caso de los usuarios de Azure Percept DK que ejecutan la versión de lanzamiento de software anterior, Microsoft recomienda realizar una actualización por cable USB o una actualización OTA a la versión 2106 antes de actualizar a la versión 2107.

- Wi-Fi:
  - Protección de la seguridad para garantizar que el punto de acceso Wi-Fi se cierra una vez completada la instalación.
  - Se ha corregido un problema en que la inserción del botón **Instalar** en el kit de desarrollo podía provocar que el punto de acceso Wi-Fi del kit de desarrollo no se sincronizara con el servicio web de experiencia de configuración.
  - Se han mejorado las reglas iptables de punto de acceso Wi-Fi para que sean más resistentes y se han quitado las reglas innecesarias.
  - Se ha corregido un problema por el que varias Wi-Fi conectadas no se priorizarían correctamente.
- Experiencia de instalación:
  - Se ha agregado localización para las regiones admitidas y se ha actualizado el texto para mejorar la legibilidad.
  - Se ha corregido un problema por el que la experiencia de configuración a veces se atascaba mientras se cargaba una página.
- Redes generales:
  - Se han resuelto problemas en que IPv6 no obtenía una concesión DHCP válida.
- Sistema operativo:
  - Revisiones de seguridad.

## <a name="june-2106-release"></a>Versión de junio (2106)

- Se ha actualizado el mecanismo de comprobación de imágenes para el agente de OTA.
- Mejoras en la interfaz de usuario y correcciones de errores en la experiencia de configuración.

## <a name="may-2105-release"></a>Versión de mayo (2105)

- Actualizaciones de seguridad para el sistema operativo CBL-Mariner.

## <a name="april-2104-release"></a>Versión de abril (2104)

- Se ha corregido un problema de rotación de registros que podía provocar que se llenase el almacenamiento de Azure Percept DK.
- Se ha habilitado el aprovisionamiento basado en TPM en Azure en la experiencia de configuración.
- Se ha agregado un tiempo de espera automático a la experiencia de configuración y al punto de acceso Wi-Fi. Después de 30 minutos o después de finalizar la configuración.
- El SSID del punto de acceso Wi-Fi ha cambiado de "**scz-[xxx]** " a "**apd-[xxx]** ".

## <a name="next-steps"></a>Pasos siguientes

- [Procedimiento para determinar una estrategia de actualización](./how-to-determine-your-update-strategy.md)
