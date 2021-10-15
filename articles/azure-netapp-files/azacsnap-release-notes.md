---
title: Notas de la versión de la herramienta Instantánea coherente de aplicaciones de Azure para Azure NetApp Files | Microsoft Docs
description: Ofrece notas de la versión a la herramienta Instantánea de Azure Application Consistent que puede usar con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: phjensen
ms.openlocfilehash: a580a135d2158234c84f3378b024997679ad6017
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778331"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Notas de la versión de la herramienta Azure Application Consistent Snapshot

En esta página se enumeran los cambios importantes realizados en AzAcSnap para proporcionar una nueva funcionalidad o resolver los defectos.

## <a name="aug-2021"></a>Agosto de 2021

### <a name="azacsnap-v502-build_2021082719086---patch-update-to-v501"></a>AzAcSnap v5.0.2 (Build_20210827.19086): actualización de revisión a v5.0.1

AzAcSnap v5.0.2 (Build_20210827.19086) se proporciona como una actualización de revisión a la rama v5.0 con las siguientes correcciones y mejoras:

- Omitir los códigos de salida 255 del comando `ssh`.  En algunos casos, el comando `ssh`, que se usa para comunicarse con el almacenamiento en Azure Large Instance, emitiría un código de salida 255 cuando no hubiera errores o errores de ejecución (consulte "EXIT STATUS" para el comando `man ssh`). Posteriormente, AzAcSnap lo capturaría como un error y lo anularía.  Esta actualización permite hacer una comprobación adicional para validar la ejecución correcta, lo que incluye el análisis de `ssh` STDOUT y STDERR en busca de errores, además de las comprobaciones habituales de código de salida.
- Corregir la comprobación de la ruta de origen hdbuserstore del instalador.  El instalador comprobaría la existencia de un directorio de origen incorrecto para la ruta de origen hdbuserstore para el usuario que ejecuta la instalación. Esto se ha corregido para comprobar `~/.hdb`.  Esto afecta a los sistemas (por ejemplo, Azure Large Instance) en los que hdbuserstore se había preconfigurado para el usuario `root` antes de instalar `azacsnap`.
- El instalador muestra ahora la versión que se instalará o extraerá (si se ejecuta sin argumentos).

Descargue la [versión más reciente](https://aka.ms/azacsnapinstaller) del instalador y revise [cómo empezar](azacsnap-get-started.md).

## <a name="may-2021"></a>Mayo de 2021

### <a name="azacsnap-v501-build-2021052414837---patch-update-to-v50"></a>AzAcSnap v5.0.1 (compilación: 20210524.14837); actualización de revisión a v5.0

AzAcSnap v5.0.1 (compilación: 20210524.14837) se proporciona como una actualización de revisión a la rama v5.0 con las siguientes correcciones y mejoras:

- Se ha mejorado el control del código de salida.  En algunos casos, se emitía un código de salida de 0 (cero) incluso cuando se producía un error de ejecución donde debería haber sido distinto de cero.  Los códigos de salida ahora solo deben ser cero cuando `azacsnap` se ejecuta correctamente hasta el final y un número distinto de cero en caso de error.  Además, el control de errores internos de AzAcSnap se ha ampliado para capturar y emitir el código de salida de los comandos externos (por ejemplo, hdbsql, ssh) ejecutados por AzAcSnap, si son la causa del error.

## <a name="april-2021"></a>Abril de 2021

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0 (compilación: 20210421.6349): versión de disponibilidad general (21 de abril de 2021)

AzAcSnap v5.0 (compilación: 20210421.6349) se ha publicado para disponibilidad general y, para esta compilación, se han realizado las siguientes correcciones y mejoras:

- El tiempo de espera de reintento de hdbsql (para esperar una respuesta de SAP HANA) se establece automáticamente en la mitad del valor de "savePointAbortWaitSeconds" para evitar condiciones de carrera.  El valor de "savePointAbortWaitSeconds" se puede modificar directamente en el archivo de configuración JSON y debe ser de 600 segundos como mínimo.

## <a name="march-2021"></a>Marzo de 2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>Versión preliminar de AzAcSnap v 5.0 (compilación: 20210318.30771)

La versión preliminar de AzAcSnap v 5.0 (compilación: 20210318.30771) se ha publicado con las siguientes correcciones y mejoras:

- Se ha quitado la necesidad de agregar el usuario AZACSNAP a las bases de datos del inquilino de SAP HANA. Consulte la sección [Habilitación de la comunicación con la base datos](azacsnap-installation.md#enable-communication-with-database).
- Corrección para permitir una [restauración](azacsnap-cmd-ref-restore.md) con volúmenes configurados con QoS manual.
- Se ha agregado un control mutex para limitar las conexiones SSH para Azure Large Instance.
- Corrija el instalador para controlar los nombres de ruta de acceso con espacios y otros problemas relacionados.
- Como preparación para la compatibilidad con otros servidores de bases de datos, cambió el parámetro opcional "--hanasid" a "--dbsid".

## <a name="next-steps"></a>Pasos siguientes

- [Primeros pasos con la herramienta Azure Application Consistent Snapshot](azacsnap-get-started.md)
