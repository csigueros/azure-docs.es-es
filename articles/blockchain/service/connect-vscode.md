---
title: Uso de Visual Studio Code para conectarse a Azure Blockchain Service
description: Conexión a una red de consorcio de Azure Blockchain Service mediante el kit de desarrollo de Azure Blockchain para la extensión Ethereum en Visual Studio Code
ms.date: 12/04/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 157aaab52c64bcbbf2ae6e378cbb354df56e2464
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "122643530"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Inicio rápido: Uso de Visual Studio Code para conectarse a una red del consorcio de Azure Blockchain Service

En este inicio rápido, se instala y usa la extensión de Visual Studio Code (VS Code) kit de desarrollo de Azure Blockchain para Ethereum para realizar la asociación a un consorcio en Azure Blockchain Service. El kit de desarrollo de Azure Blockchain simplifica la creación, conexión, compilación e implementación de contratos inteligentes en libros de contabilidad de una cadena de bloques de Ethereum.

[!INCLUDE [Retirement note](./includes/retirement.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerrequisitos

* Realizar el tutorial [Quickstart: Creación de un miembro de cadena de bloques mediante Azure Portal](create-member.md) o [Inicio rápido: Creación de un miembro de cadena de bloques de Azure Blockchain Service mediante la CLI de Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Kit de desarrollo de Azure Blockchain para la extensión de Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x o superior](https://nodejs.org)
* [Git 2.10.x o superior](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [CLI de Ganache 6.0.0](https://github.com/trufflesuite/ganache-cli)

En Windows, se requiere un compilador de C++ instalado para el módulo node-gyp. Puede usar las herramientas de MSBuild:

* Si está instalado Visual Studio 2017, configure npm para que use las herramientas de MSBuild con el comando `npm config set msvs_version 2017 -g`
* Si está instalado Visual Studio 2019, establezca la ruta de acceso de las herramientas de MSBuild para npm. Por ejemplo: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* En caso contrario, instale las herramientas independientes de VS Build mediante `npm install --global windows-build-tools` en un shell de comandos *Ejecutar como administrador* con privilegios elevados.

Para más información sobre node-gyp, consulte el [repositorio de node-gyp en GitHub](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Comprobación del entorno del kit de desarrollo de Azure Blockchain

El kit de desarrollo de Azure Blockchain comprueba que se han cumplido los requisitos previos del entorno de desarrollo. Para comprobar el entorno de desarrollo:

En la paleta de comandos de VS Code, elija **Blockchain: mostrar página de bienvenida**.

El kit de desarrollo de Azure Blockchain ejecuta un script de validación que tarda aproximadamente un minuto en completarse. Para ver la salida, seleccione **Terminal > Nuevo terminal**. En la barra de menús de terminal, seleccione la pestaña **Salida** y **Azure Blockchain** en la lista desplegable. Una validación correcta tiene un aspecto similar al de la siguiente imagen:

![Un entorno de desarrollo válido](./media/connect-vscode/valid-environment.png)

 Si falta una herramienta necesaria, en una nueva pestaña denominada **Azure Blockchain Development Kit (versión preliminar)** se muestran las herramientas necesarias con vínculos para descargarlas.

![Aplicaciones obligatorias del kit de desarrollo](./media/connect-vscode/required-apps.png)

Instale los requisitos previos que faltan antes de continuar con el inicio rápido.

## <a name="connect-to-consortium-member"></a>Conexión al miembro de un consorcio

Puede conectarse a los miembros de un consorcio mediante la extensión para VS Code del kit de desarrollo de Azure Blockchain. Una vez conectado a un consorcio, puede crear, compilar e implementar contratos inteligentes en un miembro del consorcio de Azure Blockchain Service.

Si no tiene acceso a un miembro del consorcio de Azure Blockchain Service, complete el requisito previo [Inicio rápido: Creación de un miembro de cadena de bloques mediante Azure Portal](create-member.md) o [Inicio rápido: Creación de un miembro de cadena de bloques de Azure Blockchain Service mediante la CLI de Azure](create-member-cli.md).

1. En el panel Explorador de VS Code, expanda la extensión **Azure Blockchain**.
1. Seleccione **Conectarse a una red**.

   ![Conexión a una red](./media/connect-vscode/connect-consortium.png)

    Si se le solicita la autenticación de Azure, siga las indicaciones para autenticarse con un explorador.
1. Elija **Azure Blockchain Service** en el menú desplegable de la paleta de comandos.
1. Elija la suscripción y el grupo de recursos asociados a su miembro del consorcio de Azure Blockchain Service.
1. Elija su consorcio de la lista.

Los miembros de la cadena de bloques y del consorcio se enumeran en la barra lateral del explorador de VS Code.

![Consorcio mostrado en el explorador](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, se usa la extensión de VS Code Azure Blockchain Development Kit for Ethereum para establecer la asociación con un consorcio en Azure Blockchain Service. Pruebe el siguiente tutorial para usar Azure Blockchain Development Kit for Ethereum para crear, compilar, implementar y ejecutar una función de contrato inteligente mediante una transacción.

> [!div class="nextstepaction"]
> [Creación, compilación e implementación de contratos inteligentes en Azure Blockchain Service](send-transaction.md)