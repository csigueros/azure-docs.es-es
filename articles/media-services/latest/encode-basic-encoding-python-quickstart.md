---
title: Inicio rápido sobre codificación básica de Media Services con Python
description: En esta guía de inicio rápido se muestra cómo realizar una codificación básica con Python y Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 7/2/2021
ms.author: inhenkel
ms.openlocfilehash: a0534bc562d20f96cc1c12a8b4dbe0adfc8f9282
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284408"
---
# <a name="media-services-basic-encoding-with-python"></a>Codificación básica de Media Services con Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Introducción

En esta guía de inicio rápido se muestra cómo realizar una codificación básica con Python y Azure Media Services. Se utiliza la API de Media Services v3 2020-05-01.

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Cree un grupo de recursos para utilizarlo con esta guía de inicio rápido.
- Cree una cuenta de Media Services v3.
- Obtenga la clave de la cuenta de almacenamiento.
- Cree una entidad de servicio y una clave.

## <a name="get-the-sample"></a>Obtención del ejemplo

Cree una bifurcación y clone el ejemplo que se encuentra en el [repositorio de ejemplos de Python](https://github.com/Azure-Samples/media-services-v3-python). En esta guía de inicio rápido, trabajaremos con el ejemplo BasicEncoding.

## <a name="create-the-env-file"></a>Creación del archivo .env

Obtenga los valores de la cuenta para crear un archivo *.env*. Es correcto, guárdelo sin nombre, solo la extensión.  Use *sample.env* como plantilla y, a continuación, guarde el archivo *.env* en la carpeta BasicEncoder del clon local.

## <a name="use-python-virtual-environments"></a>Uso de entornos virtuales de Python
Para los ejemplos, se recomienda crear y activar un entorno virtual de Python mediante los pasos siguientes:

1. Abra la carpeta de ejemplos en VSCode u otro editor.
2. Creación del entorno virtual

    ``` bash
      # py -3 uses the global python interpreter. You can also use python -m venv .venv.
      py -3 -m venv .venv
    ```

   Este comando ejecuta el módulo venv de Python y crea un entorno virtual en una carpeta denominada .venv.

3. Active el entorno virtual:

    ``` bash
      .venv\scripts\activate
    ```

  Un entorno virtual es una carpeta de un proyecto que aísla una copia de un intérprete de Python concreto. Una vez activado ese entorno (algo que Visual Studio Code hace automáticamente), al ejecutar pip install se instala una biblioteca únicamente en ese entorno. Al ejecutar el código de Python, este se ejecuta en el contexto exacto del entorno con versiones específicas de cada biblioteca. Y al ejecutar pip freeze, obtendrá la lista exacta de esas bibliotecas (en muchos de los ejemplos se crea un archivo requirements.txt para las bibliotecas necesarias y, luego, se usa pip install -r requirements.txt. Normalmente, se necesita un archivo de requisitos al implementar código en Azure).

## <a name="set-up"></a>Configurar

Instalación y [Configuración de un entorno de desarrollo de Python local para Azure](/azure/developer/python/configure-local-development-environment)

Instale la biblioteca azure-identity para Python. Este módulo es necesario para la autenticación de Azure Active Directory. Consulte los detalles en [Biblioteca cliente de Identidad de Azure para Python](/python/api/overview/azure/identity-readme#environment-variables).

  ``` bash
  pip install azure-identity
  ```

Instalación del SDK de Python para [Azure Media Services](/python/api/overview/azure/media-services)

La página Pypi para el SDK de Python para Media Services con los detalles de la versión más reciente se encuentra en - [azure-mgmt-media](https://pypi.org/project/azure-mgmt-media/).

  ``` bash
  pip install azure-mgmt-media
  ```

Instalación del [SDK de Azure Storage para Python](https://pypi.org/project/azure-storage-blob/)

  ``` bash
  pip install azure-storage-blob
  ```

Opcionalmente, puede instalar TODOS los requisitos de un ejemplo determinado mediante el archivo "requirements.txt" de la carpeta de ejemplos.

  ``` bash
  pip install -r requirements.txt
  ```

## <a name="try-the-code"></a>Prueba del código

El código siguiente se ha comentado con detalle.  Puede usar el script completo o alguna de sus partes para su propio script.

En este ejemplo, se genera un número aleatorio para asignar nombres a los elementos, de forma que pueda identificarlos como un grupo que se creó al ejecutar el script.  El número aleatorio es opcional y se puede quitar cuando haya terminado de probar el script.

No estamos usando la dirección URL de SAS para el recurso de entrada en este ejemplo.

[!code-python[Main](../../../media-services-v3-python/BasicEncoding/basic-encoding.py)]

## <a name="delete-resources"></a>Eliminar recursos

Cuando haya terminado con la guía de inicio rápido, elimine los recursos creados en el grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes

Familiarícese con el [SDK de Media Services para Python](/python/api/azure-mgmt-media/).

## <a name="resources"></a>Recursos

- Consulte la [API de administración](/python/api/overview/azure/mediaservices/management) de Azure Media Services.
- Aprenda a usar las [API de Storage con Python](/azure/developer/python/azure-sdk-example-storage-use?tabs=cmd).
- Más información sobre la [biblioteca cliente de identidades de Azure para Python](/python/api/overview/azure/identity-readme#environment-variables).
- Más información sobre [Azure Media Services v3](./media-services-overview.md).
- Más información sobre los [SDK de Azure para Python](/azure/developer/python).
- Más información sobre los [patrones de uso de los SDK de Azure para Python](/azure/developer/python/azure-sdk-library-usage-patterns)
- Busque más SDK de Azure para Python en el [índice del SDK de Azure para Python](/azure/developer/python/azure-sdk-library-package-index).
- [Referencia del SDK de Azure Storage Blob para Python](/python/api/azure-storage-blob/).