---
title: Abrir y guardar archivos con paquetes SSIS implementados en Azure
description: Obtenga información sobre cómo abrir y guardar archivos de forma local y en Azure al aplicar lift-and-shift a los paquetes SSIS que usan los sistemas de archivos locales en SSIS en Azure.
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: data-factory
ms.subservice: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: fe6640336093fd4a47c7f3a220293b720cf66587
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731436"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Abrir y guardar archivos de forma local y en Azure con paquetes SSIS implementados en Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se describe cómo abrir y guardar archivos de forma local y en Azure al aplicar lift-and-shift a los paquetes SSIS que usan los sistemas de archivos locales en SSIS en Azure.

## <a name="save-temporary-files"></a>Guardar los archivos temporales

Si necesita almacenar y procesar archivos temporales durante la ejecución de un paquete único, los paquetes pueden usar la carpeta de trabajo actual (`.`) o la carpeta temporal (`%TEMP%`) de los nodos de Integration Runtime de SSIS de Azure.

## <a name="use-on-premises-file-shares"></a>Usar recursos compartidos de archivos locales

Para seguir usando **recursos compartidos de archivos locales** al elevar y desplazar paquetes que usan sistemas de archivos locales a SSIS de Azure, haga lo siguiente:

1. Transfiera los archivos de los sistemas de archivos locales a recursos compartidos de archivos locales.

2. Una los recursos compartidos de archivos locales a una red virtual de Azure.

3. Una la instancia de Azure SSIS IR a la misma red virtual. Para más información, vea [Unión de un entorno de ejecución para la integración de SSIS en Azure a una red virtual](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Conecte la instancia de Azure SSIS IR al recurso compartido de archivos locales dentro de la misma red virtual mediante la configuración de credenciales de acceso que usan la autenticación de Windows. Para obtener más información, vea [Conexión a datos y a recursos compartidos de archivos con la autenticación de Windows](ssis-azure-connect-with-windows-auth.md).

5. Actualice las rutas de acceso de archivos locales en los paquetes a rutas de acceso UNC que apuntan a los recursos compartidos de archivos locales. Por ejemplo, actualice `C:\abc.txt` a `\\<on-prem-server-name>\<share-name>\abc.txt`.

## <a name="use-azure-file-shares"></a>Usar recursos compartidos de archivos de Azure

Para usar **Azure Files** al elevar y desplazar paquetes que usan sistemas de archivos locales a SSIS de Azure, haga lo siguiente:

1. Transfiera archivos de los sistemas de archivos locales a Azure Files. Para obtener más información, consulte [Azure Files](https://azure.microsoft.com/services/storage/files/).

2. Conecte la instancia de IR de SSIS de Azure a Azure Files mediante la configuración de las credenciales de acceso que usan la autenticación de Windows. Para obtener más información, vea [Conexión a datos y a recursos compartidos de archivos con la autenticación de Windows](ssis-azure-connect-with-windows-auth.md).

3. Actualice las rutas de acceso de archivos locales en los paquetes a rutas de acceso UNC que apuntan a Azure Files. Por ejemplo, actualice `C:\abc.txt` a `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`.

## <a name="next-steps"></a>Pasos siguientes

- Implemente los paquetes. Para más información, consulte [Implementar un proyecto de SSIS en Azure con SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Ejecute los paquetes. Para más información, consulte [Ejecutar paquetes SSIS en Azure con SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Programe los paquetes. Para más información, vea [Programar paquetes SSIS en Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).