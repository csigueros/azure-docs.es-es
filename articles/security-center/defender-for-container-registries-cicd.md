---
title: Analizador de vulnerabilidades de Azure Defender para imágenes de contenedor en flujos de trabajo de CI/CD
description: Obtenga información sobre el uso de Azure Defender en registros de contenedor para examinar imágenes de contenedores en flujos de trabajo de CI/CD.
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eb7309f067c350eac0d9455767b137377caf588b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736307"
---
# <a name="identify-vulnerable-container-images-in-your-cicd-workflows"></a>Identificación de imágenes de contenedor vulnerables en los flujos de trabajo de CI/CD

En esta página se explica cómo examinar las imágenes de contenedores basadas en Azure Container Registry con el analizador de vulnerabilidades integrado cuando se han creado como parte de los flujos de trabajo de GitHub.

Para configurar el analizador, deberá habilitar **Azure Defender para registros de contenedor** y la integración de CI/CD. Cuando los flujos de trabajo de CI/CD insertan imágenes en los registros, puede ver los resultados del examen de registros y un resumen de los resultados del examen de CI/CD.

Los resultados de los exámenes de CI/CD son un enriquecimiento de los resultados del examen de registros existentes de Qualys. El análisis de CI/CD de Azure Defender cuenta con la tecnología de [Aqua Trivy](https://github.com/aquasecurity/trivy).

Obtendrá información sobre la rastreabilidad como, por ejemplo, el flujo de trabajo y la dirección URL de ejecución de GitHub, para ayudar a identificar los flujos de trabajo que dan como resultado imágenes vulnerables.

> [!TIP]
> Las vulnerabilidades identificadas en un examen del registro pueden diferir de los resultados de los exámenes de CI/CD. Un motivo de estas diferencias es que el examen de registros es [continuo](defender-for-container-registries-introduction.md#when-are-images-scanned), mientras que el análisis de CI/CD se produce inmediatamente antes de que el flujo de trabajo inserte la imagen en el registro.

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:| **Esta integración de CI/CD está en versión preliminar.**<br>Se recomienda experimentar con él solo en flujos de trabajo que no sean de producción.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Precios:|**Azure Defender para registros de contenedor** se factura como se indica en la [página de precios de Security Center](https://azure.microsoft.com/pricing/details/security-center/).|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government y Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>Prerrequisitos

Para examinar las imágenes a medida que se insertan mediante flujos de trabajo de CI/CD en los registros, debe tener **Azure Defender para registros de contenedor** habilitado en la suscripción. 

## <a name="set-up-vulnerability-scanning-of-your-cicd-workflows"></a>Configuración del examen de vulnerabilidades de los flujos de trabajo de CI/CD

Para habilitar exámenes de vulnerabilidades de imágenes en los flujos de trabajo de GitHub:

[Paso 1. Habilitación de la integración de CI/CD en Security Center](#step-1-enable-the-cicd-integration-in-security-center)

[Paso 2. Adición de las líneas necesarias al flujo de trabajo de GitHub](#step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan)

### <a name="step-1-enable-the-cicd-integration-in-security-center"></a>Paso 1. Habilitación de la integración de CI/CD en Security Center

1. En la barra lateral de Security Center, seleccione **Precios y configuración**.
1. Seleccione la suscripción correspondiente.
1. En la barra lateral de la página de configuración de esa suscripción, seleccione **Integraciones**.
1. En el panel que aparece, seleccione una cuenta de Application Insights para insertar los resultados del examen de CI/CD del flujo de trabajo.
1. Copie el token de autenticación y la cadena de conexión en el flujo de trabajo de GitHub.

    :::image type="content" source="./media/defender-for-container-registries-cicd/enable-cicd-integration.png" alt-text="Habilitación de la integración de CI/CD para exámenes de vulnerabilidades de imágenes de contenedores en los flujos de trabajo de GitHub" lightbox="./media/defender-for-container-registries-cicd/enable-cicd-integration.png":::

    > [!IMPORTANT]
    > El token de autenticación y la cadena de conexión se usan para correlacionar los datos de telemetría de seguridad ingeridos con los recursos de la suscripción. Si usa valores no válidos para estos parámetros, se descartará la telemetría.

### <a name="step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan"></a>Paso 2. Adición de las líneas necesarias al flujo de trabajo de GitHub y realización de un examen

1. En el flujo de trabajo de GitHub, habilite el examen de CI/CD de la siguiente manera:

    > [!TIP]
    > Se recomienda crear dos secretos en el repositorio para hacer referencia en el archivo YAML como se indica a continuación. A los secretos se les pueden asignar nombres según sus propias convenciones de nomenclatura. En este ejemplo, se hace referencia a los secretos como **AZ_APPINSIGHTS_CONNECTION_STRING** y **AZ_SUBSCRIPTION_TOKEN**.


    ```yml
    - run: |
      echo "github.sha=$GITHUB_SHA"
      docker build -t githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - uses: Azure/container-scan@v0 
      name: Scan image for vulnerabilities
      id: container-scan
      continue-on-error: true
      with:
        image-name: githubdemo1.azurecr.io/k8sdemo:${{ github.sha }} 
    
    - name: Push Docker image - githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
      run: |
      docker push githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - name: Post logs to appinsights
      uses: Azure/publish-security-assessments@v0
      with: 
        scan-results-path: ${{ steps.container-scan.outputs.scan-report-path }}
        connection-string: ${{ secrets.AZ_APPINSIGHTS_CONNECTION_STRING }}
        subscription-token: ${{ secrets.AZ_SUBSCRIPTION_TOKEN }} 
    ```

1. Ejecute el flujo de trabajo que insertará la imagen en el registro de contenedor seleccionado. Una vez que la imagen se inserta en el registro, se ejecuta un examen del registro y puede ver los resultados del examen de CI/CD junto con los resultados del examen del registro en Azure Security Center.

1. [Vea los resultados del examen de CI/CD](#view-cicd-scan-results).

## <a name="view-cicd-scan-results"></a>Visualización de los resultados del examen de CI/CD

1. Para ver los resultados, vaya a la página **Recomendaciones**. Si se encontraron problemas, verá la recomendación **Las vulnerabilidades de las imágenes de Azure Container Registry deben corregirse**.

    ![Recomendación para corregir problemas](media/monitor-container-security/acr-finding.png)

1. Seleccione la recomendación. 

    Se abre la página de detalles de la recomendación con información adicional. Esta información incluye la lista de registros con imágenes vulnerables ("recursos afectados") y los pasos de corrección. 

1. Abra la lista de **recursos afectados** y seleccione un registro incorrecto para ver los repositorios que tienen imágenes vulnerables.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-registry.png" alt-text="Selección de un registro incorrecto":::

    Se abre la página Detalles del registro con la lista de repositorios afectados.

1. Seleccione un repositorio específico para ver los repositorios que contienen imágenes vulnerables.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-repository.png" alt-text="Selección de un repositorio incorrecto":::

    Se abre la página Detalles del repositorio. Muestra las imágenes vulnerables junto con una evaluación de la gravedad de las conclusiones.

1. Seleccione una imagen específica para ver las vulnerabilidades.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-image.png" alt-text="Selección de una imagen incorrecta":::

    Se abre la lista de resultados de la imagen seleccionada.

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-scan-results.png" alt-text="Resultados del examen de imágenes":::

1. Para más información sobre qué flujo de trabajo de GitHub está insertando estas imágenes vulnerables, seleccione la burbuja de información:

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-findings.png" alt-text="Resultados de CI/CD sobre determinadas ramas y confirmaciones de GitHub":::



## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre Azure Defender](azure-defender.md)
