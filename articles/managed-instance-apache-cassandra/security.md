---
title: Información general sobre seguridad en Azure Managed Instance for Apache Cassandra
description: Conozca los procedimientos recomendados de seguridad de las bases de datos y las características principales que ofrece Azure Managed Instance for Apache Cassandra. Ayudan a evitar y detectar vulneraciones en bases de datos, así como a responder a estos incidentes.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 96b03347adcabdd5bd25d4f57c5ea80946832610
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723330"
---
# <a name="security-in-azure-managed-instance-for-apache-cassandra---overview"></a>Información general sobre seguridad en Azure Managed Instance for Apache Cassandra

En este artículo se describen los procedimientos recomendados de seguridad de bases de datos y las principales características que ofrece Azure Managed Instance for Apache Cassandra para ayudarle a evitar y detectar vulneraciones en bases de datos, así como a responder a estos incidentes.

## <a name="how-do-i-secure-my-database"></a>¿Cómo puedo proteger mi base de datos?

La seguridad de los datos constituye una responsabilidad compartida entre el cliente y el proveedor de base de datos. Dependiendo del proveedor de base de datos que elija, puede variar el nivel de responsabilidad que asumir. Si elige una solución local, debe proporcionar todos los elementos: desde la protección de extremo a extremo hasta la seguridad física del hardware, lo que no es tarea fácil. Si elige un servicio administrado como Azure Managed Instance for Apache Cassandra, los motivos de preocupación se reducen. 

## <a name="how-does-azure-managed-instance-secure-my-database"></a>Forma en que Azure Managed Instance protege las bases de datos


|Requisito de seguridad|Enfoque de seguridad de Azure Managed Instance for Apache Cassandra|
|---|---|
|Seguridad de las redes| Los recursos de Azure Managed Instance for Apache Cassandra se hospedan en un inquilino de Microsoft, con tarjetas de interfaz de red (NIC) para cada recurso que se inserta exclusivamente en redes virtuales mediante IP privadas. No hay ninguna dirección IP pública expuesta con este servicio.|
|Copias de seguridad en línea automatizadas|Se realiza una copia de seguridad de los centros de datos de Azure Managed Instance for Apache Cassandra cada 4 horas, que se conserva durante dos días. Las copias de seguridad se mantienen en cuentas de almacenamiento locales.|
|Restauración de los datos eliminados|Las copias de seguridad automatizadas en línea se pueden utilizar para recuperar los datos que puede haber eliminado accidentalmente. Puede hacer una copia de seguridad de los datos en cualquier punto aproximadamente dos días después de haberlos eliminado.|
|HTTPS/SSL/TLS y cifrado de disco | En Azure Managed Instance for Apache Cassandra, todos los datos se cifran en reposo. Se aplican el cifrado SSL de servidor (TLS 1.2) y el cifrado de nodo a nodo. El SSL de cliente es una configuración opcional. Se admiten claves administradas por el cliente para cifrar datos en disco; consulte el artículo [aquí](customer-managed-keys.md) para obtener más información. |
|Supervisión de los ataques|Azure Managed Instance for Apache Cassandra está integrado con [Azure Monitor](../azure-monitor/overview.md). Mediante el uso de registros de actividad y de auditoría, puede supervisar su cuenta para detectar actividad normal y anómala. Puede ver qué operaciones se realizaron en los recursos, quién inició la operación, cuándo se produjo, el estado de la operación y muchas más tareas.|
|Respuesta a ataques|Una vez que se ha puesto en contacto con el equipo de asistencia técnica de Azure para informar de un posible ataque, se inicia un proceso de respuesta a incidentes de cinco pasos. El objetivo de dicho proceso es restaurar las operaciones y la seguridad de los servicios a su estado normal lo antes posible después de que se detecte un problema y se inicie una investigación.|
|Servidores revisados|Como una base de datos administrada, Azure Managed Instance for Apache Cassandra elimina la necesidad de administrar y aplicar revisiones a servidores, que se hace automáticamente.|
|Certificaciones| Para obtener la lista más actualizada de certificaciones, consulte el [sitio de cumplimiento general de Azure](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/).


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las certificaciones de Microsoft, visite el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/).
