---
title: Archivo de inclusión
description: archivo de inclusión
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 07/22/2021
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 938a5858efe79affe904d8d07a05270f3071ab72
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114659744"
---
#### <a name="process-automation"></a>Automatización de procesos

| Recurso | Límite |Notas|
| --- | --- |---|
| Cantidad máxima de trabajos nuevos que se puede enviar cada 30 segundos por cuenta de Automation (trabajos no programados) |100 |Si se alcanza este límite, se producirá un error en las siguientes solicitudes para crear un trabajo. El cliente recibe una respuesta de error.|
| Cantidad máxima de trabajos en ejecución simultáneos en la misma instancia de tiempo por cuenta de Automation (trabajos no programados) |200 |Si se alcanza este límite, se producirá un error en las siguientes solicitudes para crear un trabajo. El cliente recibe una respuesta de error.|
| Tamaño máximo de almacenamiento de metadatos de trabajo para un período sucesivo de 30 días | 10 GB (aproximadamente, 4 millones de trabajos)|Si se alcanza este límite, se producirá un error en las siguientes solicitudes para crear un trabajo. |
| Límite de flujo de trabajo máximo|1 MiB|Una sola secuencia no puede mayor que 1 MiB.|
| Cantidad máxima de módulos que se puede importar cada 30 segundos por cuenta de Automation |5 ||
| Tamaño máximo de un módulo |100 MB ||
| Tamaño máximo de un archivo de configuración de nodo | 1 MB | Se aplica a la configuración de estado |
| Tiempo de ejecución de trabajos, nivel Gratis |500 minutos por suscripción por mes del calendario ||
| Cantidad máxima de espacio en disco permitida por espacio aislado<sup>1</sup> |1 GB |Se aplica solo a los espacios aislados de Azure.|
| Cantidad máxima de memoria que se asigna a un espacio aislado<sup>1</sup> |400 MB |Se aplica solo a los espacios aislados de Azure.|
| Número máximo de sockets de red permitido por espacio aislado<sup>1</sup> |1,000 |Se aplica solo a los espacios aislados de Azure.|
| Tiempo de ejecución máximo permitido por runbook <sup>1</sup> |3 horas |Se aplica solo a los espacios aislados de Azure.|
| Número máximo de cuentas de Automation en una suscripción |Sin límite ||
| Número máximo de instancias de Hybrid Runbook Worker del sistema por cuenta de Automation|4\.000||
| Número máximo de instancias de Hybrid Runbook Worker del usuario por cuenta de Automation|4\.000||
|Número máximo de trabajos simultáneos que se pueden ejecutar en un único Hybrid Runbook Worker|50 ||
| Tamaño máximo de los parámetros de un trabajo de runbook   | 512 kilobytes||
| Parámetros máximos de runbook   | 50|Si alcanza el límite de 50 parámetros, puede pasar una cadena JSON o XML a un parámetro y analizarlo con el runbook.|
| Tamaño máximo de carga útil de webhook |  512 kilobytes|
| Máximo de días que se conservan los datos de trabajo|30 días|
| Tamaño máximo del estado de flujo de trabajo de PowerShell |5 MB| Se aplica a los runbooks del flujo de trabajo de PowerShell al aplicar el punto de control del flujo de trabajo.|
| Número máximo de etiquetas que admite una cuenta de Automation|15||

<sup>1</sup>Un espacio aislado es un entorno compartido que puede usarse en varios trabajos. Los trabajos con el mismo espacio aislado están sujetos a las limitaciones de recursos de dicho espacio.

#### <a name="change-tracking-and-inventory"></a>Change Tracking e Inventario

En la siguiente tabla se muestran los límites del elemento sometido a seguimiento por máquina para el seguimiento de cambios.

| **Recurso** | **Límite**| **Notas** |
|---|---|---|
|Archivo|500||
|Tamaño de archivo|5 MB||
|Registro|250||
|Software de Windows|250|No se incluye el costo de las actualizaciones de software.|
|Paquetes Linux|1250||
|Servicios|250||
|Demonio|250||

#### <a name="update-management"></a>Administración de actualizaciones

En la tabla siguiente se muestran los límites de la administración de actualizaciones.

| **Recurso** | **Límite**| **Notas** |
|---|---|---|
|Número de máquinas por implementación de actualizaciones|1000||
|Número de grupos dinámicos por implementación de actualizaciones. |500 ||