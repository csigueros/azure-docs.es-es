---
title: Implementación de la herramienta de etiquetado de ejemplo de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Obtenga información sobre las distintas formas en que puede implementar la herramienta de etiquetado de ejemplo de Form Recognizer para que lo ayude con el aprendizaje supervisado.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/02/2021
ms.author: lajanuar
ms.openlocfilehash: e0555050b6457cc25a3d66d902d7de9232247ae3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326603"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implementación de la herramienta de etiquetado de ejemplo

La herramienta de etiquetado de ejemplo de Form Recognizer es una aplicación que proporciona una interfaz de usuario simple (IU), que se puede usar para etiquetar manualmente los formularios (documentos) de cara al aprendizaje supervisado. En este artículo, se proporcionan vínculos e instrucciones que le enseñarán a:

* [Ejecutar la herramienta de etiquetado de ejemplo localmente](#run-the-sample-labeling-tool-locally)
* [Implementar la herramienta de etiquetado de ejemplo en una instancia de Azure Container Instances (ACI)](#deploy-with-azure-container-instances-aci)
* [Usar y contribuir a OCR Form Labeling Tool de código abierto](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Ejecución de la herramienta de etiquetado de ejemplo de manera local

La manera más rápida de empezar a etiquetar los datos es ejecutar la herramienta de etiquetado de ejemplo de manera local. En el siguiente inicio rápido, usará la API REST de Form Recognizer con la herramienta de etiquetado de ejemplo para entrenar un modelo personalizado con datos etiquetados manualmente.

* [Introducción a Azure Form Recognizer](label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Implementación con Azure Container Instances (ACI)

Antes de comenzar, es importante tener en cuenta que hay dos maneras de implementar la herramienta de etiquetado de ejemplo en una instancia de Azure Container Instances (ACI). Ambas opciones se usan para ejecutar la herramienta de etiquetado de ejemplo con ACI:

* [Uso de Azure Portal](#azure-portal)
* [Uso de la CLI de Azure](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Siga estos pasos para crear un recurso mediante Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/signin/index/).
2. Seleccione **Crear un recurso**.
3. Luego, seleccione **Aplicación web**.

   > [!div class="mx-imgBorder"]
   > ![Selección de Aplicación web](./media/quickstarts/create-web-app.png)

4. En primer lugar, asegúrese de que la pestaña **Aspectos básicos** está seleccionada. Ahora, tendrá que proporcionar algo de información:

   > [!div class="mx-imgBorder"]
   > ![Selección de Aspectos básicos](./media/quickstarts/select-basics.png)
   * Suscripción: seleccione una suscripción de Azure existente.
   * Grupo de recursos: puede usar un grupo de recursos existente o crear uno para este proyecto. Se recomienda la segunda opción.
   * Nombre: asigne un nombre a la aplicación web.
   * Publicar: seleccione **Contenedor de Docker**.
   * Sistema operativo: seleccione **Linux**.
   * Región: elija una región que sea significativa para usted.
   * Linux Plan (Plan de Linux): seleccione un plan de tarifa para su servicio de aplicación.

   > [!div class="mx-imgBorder"]
   > ![Configuración de la aplicación web](./media/quickstarts/select-docker.png)

5. A continuación, seleccione la pestaña **Docker**.

   > [!div class="mx-imgBorder"]
   > ![Selección de Docker](./media/quickstarts/select-docker.png)

6. Ahora vamos a configurar el contenedor de Docker. Todos los campos son obligatorios a menos que se indique lo contrario:
<!-- markdownlint-disable MD025 -->

* Opciones: seleccione **Contenedor único**.
* Origen de imagen: seleccione **Registro privado**.
* URL de servidor: establézcala en `https://mcr.microsoft.com`.
* Nombre de usuario (opcional): cree un nombre de usuario.
* Contraseña (opcional): cree una contraseña segura que recuerde.
* Imagen y etiqueta: establézcalo en `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1`.
* Implementación continua: establezca esta opción en **Activado** si quiere recibir actualizaciones automáticas cuando el equipo de desarrollo realice cambios en la herramienta de etiquetado de ejemplo.
* Comando de inicio: establézcalo en `./run.sh eula=accept`.

> [!div class="mx-imgBorder"]
> ![Configurar Docker](./media/quickstarts/configure-docker.png)

* A continuación, seleccione **Revisar + crear** y, luego, **Crear** para implementar la aplicación web. Cuando haya finalizado, puede acceder a la aplicación web en la dirección URL proporcionada en la pestaña **Información general** del recurso.

### <a name="continuous-deployment"></a>Implementación continua

Después de crear la aplicación web, puede habilitar la opción de implementación continua:

* En el panel izquierdo, elija **Configuración del contenedor**.
* En la ventana principal, vaya a Implementación continua y alterne entre los botones **Activar** y **Desactivar** para establecer sus preferencias:

:::image type="content" source="media/label-tool/continuous-deployment.png" alt-text="Captura de pantalla: resumen de la configuración del contenedor para la implementación continua." lightbox="media/label-tool/continuous-deployment-bigger.png":::

> [!NOTE]
> Al crear la aplicación web, también puede configurar la autorización o autenticación. Esto no es necesario para comenzar.

> [!IMPORTANT]
> Es posible que tenga que habilitar TLS para la aplicación web para verla en su dirección `https`. Siga las instrucciones que se indican en [Habilitación de un punto de conexión de TLS](../../container-instances/container-instances-container-group-ssl.md) para configurar un contenedor de tipo sidecar que habilite TLS/SSL para la aplicación web.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

Como alternativa al uso de Azure Portal, puede crear un recurso mediante la CLI de Azure. Antes de continuar, deberá instalar la [CLI de Azure](/cli/azure/install-azure-cli). Si ya está trabajando con la CLI de Azure, puede omitir este paso.

Hay algunas cosas que necesita saber sobre este comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` genera un nombre DNS aleatorio.
* En este ejemplo se da por hecho que tiene un grupo de recursos que puede usar para crear un recurso. Reemplace `<resource_group_name>` por un grupo de recursos válido asociado a su suscripción.
* Deberá especificar dónde desea crear el recurso. Reemplace `<region name>` por la región deseada para la aplicación web.
* Este comando acepta automáticamente el CLUF.

En la CLI de Azure, ejecute este comando para crear un recurso de aplicación web para la herramienta de etiquetado de ejemplo:

<!-- markdownlint-disable MD024 -->

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1 \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

### <a name="connect-to-azure-ad-for-authorization"></a>Conexión a Azure AD para la autorización

Se recomienda conectar la aplicación web a Azure Active Directory (Azure AD). Esta conexión garantiza que solo los usuarios con credenciales válidas pueden iniciar sesión en la aplicación web y usarla. Siga las instrucciones que se indican en [Configuración de la aplicación de App Service](../../app-service/configure-authentication-provider-aad.md) para conectarse a Azure Active Directory.

## <a name="open-source-on-github"></a>Código fuente en Docker

OCR Form Labeling Tool también está disponible como proyecto de código abierto en GitHub. La herramienta es una aplicación web compilada mediante React + Redux y está escrita en TypeScript. Para más información o para contribuir, consulte [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Pasos siguientes

Use el inicio rápido [Entrenamiento con etiquetas](label-tool.md) para aprender a usar la herramienta para etiquetar manualmente los datos de entrenamiento y realizar aprendizaje supervisado.
