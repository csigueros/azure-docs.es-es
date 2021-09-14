---
title: Archivo de inclusión
description: archivo de inclusión
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 400e8de6b8d8481cccd6d2b3bcb25620f956f823
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423296"
---
Para proteger los datos de los recursos compartidos de archivos de Azure contra la pérdida o daños de los datos, todos los recursos compartidos de los archivos de Azure almacenan varias copias de cada archivo a medida que se escriben. En función de los requisitos de la carga de trabajo, puede seleccionar niveles adicionales de redundancia. Actualmente, Azure Files admite las siguientes opciones de redundancia de datos:

- **Almacenamiento con redundancia local (LRS)** : con LRS, todos los archivos se almacenan tres veces dentro de un clúster de almacenamiento de Azure. Esto protege contra la pérdida de datos debido a errores de hardware, como una unidad de disco incorrecta. No obstante, si se produce un desastre como un incendio o una inundación en el centro de datos, es posible que todas las réplicas de una cuenta de almacenamiento con LRS se pierdan o no se puedan recuperar.
- **Almacenamiento con redundancia de zona (ZRS)** : con ZRS, se almacenan tres copias de cada archivo, aunque estas copias están aisladas físicamente en tres clústeres de almacenamiento distintos de diferentes *zonas de disponibilidad* de Azure. Las zonas de disponibilidad son ubicaciones físicas exclusivas dentro de una región de Azure. Cada zona consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes. No se acepta la escritura en el almacenamiento hasta que se escribe en los clústeres de almacenamiento en las tres zonas de disponibilidad. 
- **Almacenamiento con redundancia geográfica (GRS)** : con GRS, hay dos regiones, una primaria y una secundaria. Los archivos se almacenan tres veces dentro de un clúster de almacenamiento de Azure de la región primaria. Las escrituras se replican de forma asincrónica en una región secundaria definida por Microsoft. GRS proporciona seis copias de los datos distribuidas entre dos regiones de Azure. En caso de un desastre importante, como la pérdida permanente de una región de Azure debido a una catástrofe natural o a otro evento similar, Microsoft realiza una conmutación por error y el servidor secundario se convierte en el primario y atiende a todas las operaciones. Dado que la replicación entre las regiones principal y secundaria es asincrónica, en caso de que se produzca un desastre importante, se perderán los datos que todavía no se hayan replicado en la región secundaria. También puede realizar una conmutación por error manual de una cuenta de almacenamiento con redundancia geográfica.
- **Almacenamiento con redundancia de zona geográfica (GZRS)** : puede imaginarse GZRS como si fuera ZRS, pero con redundancia geográfica. Con GZRS, los archivos se almacenan tres veces en tres clústeres de almacenamiento distintos de la región primaria. Todas las escrituras se replican de forma asincrónica en una región secundaria definida por Microsoft. El proceso de conmutación por error de GZRS funciona igual que en GRS.

Los recursos compartidos de archivos estándar de Azure de hasta 5 TiB admiten los cuatro tipos de redundancia. Los recursos compartidos de archivos estándar de más de 5 TiB solo admiten LRS y ZRS. Los recursos compartidos de archivos premium de Azure solo admiten LRS y ZRS.

Las cuentas de almacenamiento de uso general versión 2 (GPv2) proporcionan dos opciones de redundancia adicionales que no son compatibles con Azure Files: almacenamiento con redundancia geográfica con acceso de lectura, que a menudo se conoce como RA-GRS, y almacenamiento con redundancia de zona geográfica accesible. se conoce como RA-GZRS. Puede aprovisionar recursos compartidos de archivos de Azure en cuentas de almacenamiento con estas opciones establecidas, pero Azure Files no admite la lectura desde la región secundaria. Los recursos compartidos de archivos de Azure implementados en cuentas de almacenamiento con redundancia geográfica o con redundancia de zona geográfica con acceso de lectura se facturarán como almacenamiento con redundancia geográfica o con redundancia de zona geográfica, respectivamente.