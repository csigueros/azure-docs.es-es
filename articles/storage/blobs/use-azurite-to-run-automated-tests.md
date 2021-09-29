---
title: Ejecución de pruebas automatizadas mediante Azurite
titleSuffix: Azure Storage
description: Obtenga información sobre cómo escribir pruebas automatizadas en puntos de conexión privados para Azure Blob Storage mediante Azurite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: 21a0d2157cdb8b6b2c31520596327b3b433ef93a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674554"
---
# <a name="run-automated-tests-by-using-azurite"></a>Ejecución de pruebas automatizadas mediante Azurite

Obtenga información sobre cómo escribir pruebas automatizadas en puntos de conexión privados para Azure Blob Storage mediante el emulador de almacenamiento de Azurite.

## <a name="run-tests-on-your-local-machine"></a>Ejecución de pruebas en el equipo local

1. Instale la última versión de [Python](https://www.python.org/).

1. Instale el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

1. Instale y ejecute [Azurite](../common/storage-use-azurite.md):

   **Opción 1:** Utilice npm para instalar y luego ejecutar Azurite localmente.

   ```bash
   # Install Azurite
   npm install -g azurite

   # Create an Azurite directory
   mkdir c:\azurite

   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Opción 2:** Use Docker para ejecutar Azurite.

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. En el Explorador de Azure Storage, seleccione **Vincular un emulador local**.

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Captura de pantalla del Explorador de Azure Storage conectándose al origen de Azure Storage.":::

1. Proporcione un **nombre para mostrar** y un número de **puerto de blobs** para conectar Azurite y utilizar el Explorador de Azure Storage para administrar el almacenamiento local de blobs.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Captura de pantalla del Explorador de Azure Storage vinculándose a un emulador local.":::

1. Creación de un entorno de Python virtual

   ```bash
   python -m venv .venv
   ```

1. Cree un contenedor e inicialice las variables de entorno. Utilice un archivo [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/latest/how-to/writing_plugins.html#conftest-py-plugins) para generar pruebas. Este es un ejemplo de un archivo conftest.py:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > El valor mostrado para `AZURE_STORAGE_CONNECTION_STRING` es el valor predeterminado de Azurite, no es una clave privada.

1. Instale las dependencias enumeradas en un archivo [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt).

   ```bash
   pip install -r requirements.txt
   ```

1. Ejecute las pruebas:

   ```bash
   python -m pytest ./tests
   ```

Después de ejecutar las pruebas, puede ver los archivos en Azurite Blob Storage mediante el Explorador de Azure Storage.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Captura de pantalla del Explorador de Azure Storage mostrando los archivos generados por las pruebas.":::

## <a name="run-tests-on-azure-pipelines"></a>Ejecución de pruebas en Azure Pipelines

Después de ejecutar las pruebas localmente, asegúrese de que se superan en [Azure Pipelines](/azure/devops/pipelines). Use una imagen de Azurite de Docker como agente hospedado en Azure, o use npm para instalar Azurite. En el siguiente ejemplo de Azure Pipelines se usa npm para instalar Azurite.

```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'

- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Después de ejecutar las pruebas de Azure Pipelines, debería ver una salida similar a la siguiente:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Captura de pantalla de los resultados de la prueba de Azure Pipelines.":::

## <a name="next-steps"></a>Pasos siguientes

- [Uso del emulador Azurite para el desarrollo local de Azure Storage](../common/storage-use-azurite.md)
- [Ejemplo: Uso de Azurite para ejecutar pruebas de Blob Storage en la canalización DevOps de Azure](https://github.com/Azure-Samples/automated-testing-with-azurite)
