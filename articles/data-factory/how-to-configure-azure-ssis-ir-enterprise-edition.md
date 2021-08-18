---
title: Aprovisionamiento de Enterprise Edition para una instancia de Integration Runtime para la integración de SSIS en Azure
description: En este artículo se describen las características y la configuración de Enterprise Edition para una instancia de Integration Runtime para la integración de SSIS en Azure, y cómo aprovisionarlo.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 82b004d6bf99528d45f7420777a5999f371babeb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738201"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Aprovisionamiento de Enterprise Edition para una instancia de Integration Runtime para la integración de SSIS en Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La edición Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure permite usar las siguientes características avanzadas y Premium:
-   Componentes de Change Data Capture (CDC)
-   Conectores de Oracle, Teradata y SAP BW
-   Conectores y transformaciones de SQL Server Analysis Services (SSAS) y Azure Analysis Services (AAS)
-   Transformaciones de Agrupación aproximada y Búsqueda aproximada
-   Transformaciones de Extracción de términos y Búsqueda de términos

Algunas de estas características requieren la instalación de componentes adicionales para personalizar la instancia de Integration Runtime para la integración de SSIS en Azure. Para más información acerca de cómo instalar componentes adicionales, consulte [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure).

## <a name="enterprise-features"></a>Características de Enterprise Edition

| **Características de Enterprise Edition** | **Descripciones** |
|---|---|
| Componentes de CDC | CDC Source, Control Task y Splitter Transformation están preinstalados en la edición Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure. Para conectar con Oracle, también es preciso instalar Diseñador CDC y Servicio en otro equipo. |
| Conectores de Oracle | Oracle Connection Manager, Source y Destination, están preinstalados en la edición Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure. También es preciso instalar el controlador Oracle Call Interface (OCI) y, si es necesario, configurar Oracle Transport Network Substrate (TNS) en la instancia de Integration Runtime para la integración de SSIS en Azure. Para más información, consulte [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure). |
| Conectores de Teradata | Debe instalar Teradata Connection Manager, Source y Destination, así como la API de Teradata Parallel Transporter (TPT) API y el controlador de ODBC de Teradata en la edición Enterprise de la instancia de Integration Runtime para la integración de SSIS en Azure. Para más información, consulte [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure). |
| Conectores SAP BW | SAP BW Connection Manager, Source y Destination, están preinstalados en la edición Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure. También debe instalar el controlador de SAP BW en la instancia de Integration Runtime para la integración de SSIS en Azure. Estos conectores admiten SAP BW 7.0 o las versiones anteriores. Para conectarse a las versiones posteriores de SAP BW u otros productos de SAP, puede adquirir e instalar conectores SAP de ISV de terceros en la instancia de Integration Runtime para la integración de SSIS en Azure. Para más información acerca de cómo instalar componentes adicionales, consulte [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure). |
| Componentes de Analysis Services               | Data Mining Model Training Destination, Dimension Processing Destination y Partition Processing Destination, así como la Data Mining Query Transformation están preinstalados en la edición Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure. Todos estos componentes admiten SQL Server Analysis Services (SSAS), pero el destino de procesamiento de particiones es el único compatible con Azure Analysis Services (AAS). Para conectarse a SSAS, también es preciso [configurar las credenciales de autenticación de Windows en SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Además de estos componentes, Analysis Services Execute DDL Task, Analysis Services Processing Task y Data Mining Query Task también están preinstalado en las ediciones Standard/Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure. |
| Transformaciones de Agrupación aproximada y Búsqueda aproximada  | Las transformaciones de Agrupación aproximada y Búsqueda aproximada están preinstaladas en la edición Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure. Estos componentes admiten SQL Server y Azure SQL Database para almacenar datos de referencia. |
| Transformaciones de Extracción de términos y Búsqueda de términos | Las transformaciones de Extracción de términos y Búsqueda de términos están preinstaladas en la edición Enterprise Edition de la instancia de Integration Runtime para la integración de SSIS en Azure. Estos componentes admiten SQL Server y Azure SQL Database para almacenar datos de referencia. |

## <a name="instructions"></a>Instructions

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Descargue e instale [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Al aprovisionar o volver a configurar la instancia de Integration Runtime para la integración de SSIS en Azure con PowerShell, ejecute `Set-AzDataFactoryV2IntegrationRuntime` con **Enterprise** como valor del parámetro **Edition** antes de iniciar la instancia de Integration Runtime para la integración de SSIS en Azure. Este es un script de ejemplo:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Pasos siguientes

-   [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Instalación personalizada de la instancia de Integration Runtime para la integración de SSIS en Azure)

-   [How to develop paid or licensed custom components for the Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md) (Desarrollo de componentes personalizados de pago o con licencia para el entorno de ejecución de integración de Azure-SSIS)