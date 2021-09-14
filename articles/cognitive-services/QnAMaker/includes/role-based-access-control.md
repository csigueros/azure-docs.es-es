---
title: Archivo de inclusión
description: archivo de inclusión
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: 615346c16fbbb31bcefc53fc68d04c1255ece88c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453889"
---
Se proporcionan los siguientes roles para la colaboración:

|Role|Funcionalidades|Acceso de API|Permisos de API|
|--|--|--|--|
|Propietario|All|Clave de autenticación|All|
|Colaborador|Todas, excepto la capacidad de agregar nuevos miembros a los roles|Clave de autenticación|Todas, excepto la capacidad de agregar nuevos miembros a los roles|
|Leer QnA Maker<br>(lectura)|Exportar/Descargar<br>Prueba|Token de portador|1. Descargar API de KB<br>2. Lista de KB para la API de usuario<br>3. Obtención de los detalles de la base de conocimientos<br>4. Descargar modificaciones<br>Generar respuestas |
|Editor de QnA Maker<br>(lectura/escritura)|Exportar/Descargar<br>Prueba<br>Actualizar KB<br>Exportar KB<br>Importar KB<br>Reemplazar KB<br>Crear una base de conocimiento|Token de portador|1. Crear API de KB<br>2. Actualizar API de KB<br>3. Reemplazar API de KB<br>4. Reemplazar modificaciones<br>5. "Entrenar API" [en el nuevo modelo de servicio v5]|
|Usuario de Cognitive Service<br>(lectura, escritura y publicación)|All|Clave de autenticación|Todo el acceso al recurso de Cognitive Services, excepto la capacidad de: <br>1. Agregar nuevos miembros a roles.<br>2. Crear nuevos recursos.|
