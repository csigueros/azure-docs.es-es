---
title: Integración con productos de seguridad de Azure
description: En este artículo se describe cómo conectar los servicios de seguridad de Azure y Azure Purview para obtener experiencias de seguridad enriquecidas.
author: aashishr
ms.author: aashishr
ms.service: purview
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 7369f64f16724eb5c660b54a3d14a30046a41685
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894890"
---
# <a name="integrate-azure-purview-with-azure-security-products"></a>Integración de Azure Purview con productos de seguridad de Azure

En este documento se explican los pasos necesarios para conectar una cuenta de Azure Purview con varios productos de seguridad de Azure para enriquecer las experiencias de seguridad con clasificaciones de datos y etiquetas de confidencialidad.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud
Azure Purview proporciona información detallada sobre la confidencialidad de los datos. Esto hace que sea útil para los equipos de seguridad que usan Microsoft Defender for Cloud para administrar la posición de seguridad de la organización y proteger sus cargas de trabajo frente a amenazas. Los recursos de datos siguen siendo un objetivo favorito para los actores malintencionados, por lo que es fundamental que los equipos de seguridad identifiquen, prioricen y protejan los recursos de datos confidenciales en sus entornos de nube. La integración con Azure Purview amplía la visibilidad de la capa de datos, lo que permite a los equipos de seguridad priorizar los recursos que contienen datos confidenciales.

Para aprovechar este [enriquecimiento de Microsoft Defender for Cloud](../security-center/information-protection.md), no se requiere ningún paso adicional en Azure Purview. Comience a explorar los enriquecimientos de seguridad en la [página Inventario](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/25) de Microsoft Defender for Cloud, donde puede ver la lista de orígenes de datos con clasificaciones y etiquetas de confidencialidad.

### <a name="supported-data-sources"></a>Orígenes de datos admitidos
La integración admite orígenes de datos de Azure y AWS; los datos confidenciales detectados en estos recursos se comparten con Microsoft Defender for Cloud:
- [Azure Blob Storage](./register-scan-azure-blob-storage-source.md)
- [Azure Cosmos DB](./register-scan-azure-cosmos-database.md)
- [Azure Data Explorer](./register-scan-azure-data-explorer.md)
- [Azure Data Lake Storage Gen1](./register-scan-adls-gen1.md)
- [Azure Data Lake Storage Gen2](./register-scan-adls-gen2.md)
- [Archivos de Azure](./register-scan-azure-files-storage-source.md)
- [Azure Database for MySQL](./register-scan-azure-mysql-database.md)
- [Azure Database para PostgreSQL](./register-scan-azure-postgresql.md)
- [Instancia administrada de Azure SQL Database](./register-scan-azure-sql-database-managed-instance.md)
- [Grupo de SQL dedicado de Azure (antes denominado SQL DW)](./register-scan-azure-synapse-analytics.md)
- [Azure SQL Database](./register-scan-azure-sql-database.md)
- [Azure Synapse Analytics (área de trabajo)](./register-scan-synapse-workspace.md)
- [Amazon S3](./register-scan-amazon-s3.md)

### <a name="known-issues"></a>Problemas conocidos
1. La información sobre la confidencialidad de los datos no se comparte actualmente para los orígenes hospedados en máquinas virtuales, como SAP, Erwin y Teradata.
2. La información sobre la confidencialidad de los datos no se comparte actualmente para Amazon RDS.
3. La información sobre la confidencialidad de los datos no se comparte actualmente para los orígenes de datos de PaaS de Azure registrados mediante una cadena de conexión. 
5. La anulación del registro del origen de datos en Azure Purview no elimina el enriquecimiento de la confidencialidad de los datos en Microsoft Defender for Cloud.
6. Tras la eliminación de la cuenta de Azure Purview, se mantendrá el enriquecimiento de la confidencialidad de los datos durante 30 días en Microsoft Defender for Cloud.
7. Las clasificaciones personalizadas definidas en el Centro de cumplimiento de Microsoft 365 o en Azure Purview no se comparten con Microsoft Defender for Cloud.

## <a name="faq"></a>Preguntas más frecuentes
### <a name="why-dont-i-see-the-aws-data-source-i-have-scanned-with-azure-purview-in-microsoft-defender-for-cloud"></a>**¿Por qué no veo el origen de datos de AWS que he examinado con Azure Purview en Microsoft Defender for Cloud?**

Los orígenes de datos también deben incorporarse a Microsoft Defender for Cloud. Obtenga más información sobre cómo [conectar sus cuentas de AWS](../security-center/quickstart-onboard-aws.md) y ver los orígenes de datos de AWS en Microsoft Defender for Cloud.

### <a name="why-dont-i-see-sensitivity-labels-in-microsoft-defender-for-cloud"></a>**¿Por qué no veo etiquetas de confidencialidad en Microsoft Defender for Cloud?**

Los recursos deben etiquetarse primero en Azure Purview para que las etiquetas se muestran en Microsoft Defender for Cloud. Compruebe si cumple los [requisitos previos de las etiquetas de confidencialidad](./how-to-automatically-label-your-content.md). Después de analizar los datos, las etiquetas se mostrarán en Azure Purview y, automáticamente, también en Microsoft Defender for Cloud.

## <a name="next-steps"></a>Pasos siguientes
- [Experiencias de Microsoft Defender for Cloud enriquecidas mediante la confidencialidad de Azure Purview](../security-center/information-protection.md)
