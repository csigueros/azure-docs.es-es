---
title: 'Inicio rápido: Configuración de Azure Attestation con Azure Portal'
description: En este inicio rápido aprenderá a configurar un proveedor de atestación mediante Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 2465e8eba55fcf459a1084a555d5bd3c91118822
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738658"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Inicio rápido: Configuración de Azure Attestation con Azure Portal

Siga este inicio rápido para empezar a trabajar con Azure Attestation. Aprenda a administrar un proveedor de atestación, un firmante de directivas y una directiva mediante Azure Portal.

## <a name="prerequisites"></a>Prerrequisitos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="attestation-provider"></a>Proveedor de atestación

En esta sección creará un proveedor de atestación y lo configurará con directivas sin firmar o firmadas. También aprenderá a ver y eliminar el proveedor de atestación.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Creación y configuración del proveedor con directivas sin firmar

1. Vaya al menú de Azure Portal o a la página principal y seleccione **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **attestation**.
1. En la lista de resultados, seleccione **Microsoft Azure Attestation**.
1. En la página **Microsoft Azure Attestation**, seleccione **Create** (Crear).
1. En la página **Create attestation provider** (Crear proveedor de atestación), proporcione las siguientes entradas:

   - **Suscripción**: Elija una suscripción.
   - **Grupo de recursos**: seleccione un grupo de recursos existente o **Crear nuevo** y escriba un nombre para el grupo de recursos.
   - **Nombre**: Escriba un nombre único.
   - **Ubicación**: Elija una ubicación.
   - **Policy signer certificates file** (Archivo de firmantes de directivas de atestación): no cargue el archivo de firmantes de directivas de atestación para configurar el proveedor con directivas sin firmar.

1. Después de proporcionar las entradas necesarias, seleccione **Revisar y crear**.
1. Si hay problemas de validación, corríjalos y seleccione **Crear**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Creación y configuración del proveedor con directivas firmadas

1. Vaya al menú de Azure Portal o a la página principal y seleccione **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **attestation**.
1. En la lista de resultados, seleccione **Microsoft Azure Attestation**.
1. En la página **Microsoft Azure Attestation**, seleccione **Create** (Crear).
1. En la página **Create attestation provider** (Crear proveedor de atestación), proporcione la siguiente información:

   - **Suscripción**: Elija una suscripción.
   - **Grupo de recursos**: seleccione un grupo de recursos existente o **Crear nuevo** y escriba un nombre para el grupo de recursos.
   - **Nombre**: Escriba un nombre único.
   - **Ubicación**: Elija una ubicación.
   - **Policy signer certificates file** (Archivo de firmantes de directivas de atestación): cargue el archivo de firmantes de directivas de atestación para configurar el proveedor con directivas firmadas. [Consulte los ejemplos de certificado de firma de directivas](./policy-signer-examples.md).

1. Después de proporcionar las entradas necesarias, seleccione **Revisar y crear**.
1. Si hay problemas de validación, corríjalos y seleccione **Crear**.

### <a name="view-the-attestation-provider"></a>Visualización del proveedor de atestación

1. Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1. En el cuadro de filtro, escriba el nombre del proveedor de atestación y selecciónelo.

### <a name="delete-the-attestation-provider"></a>Eliminación del proveedor de atestación

Hay dos maneras de eliminar el proveedor de atestación. Puede:

1. Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1. En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1. Marque la casilla y seleccione **Eliminar**.
1. Escriba **sí** y seleccione **Eliminar**.

También puede:

1. Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1. En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1. Seleccione el proveedor de atestación y vaya a la página de información general.
1. Seleccione **Eliminar** en la barra de menús y, después, **Sí**.

## <a name="attestation-policy-signers"></a>Firmantes de directivas de atestación

Siga los pasos de esta sección para ver, agregar y eliminar firmantes de directivas de atestación.

### <a name="view-the-policy-signer-certificates"></a>Visualización de los firmantes de directivas de atestación

1. Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1. En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1. Seleccione el proveedor de atestación y vaya a la página de información general.
1. Seleccione **Policy signer certificates** (Firmantes de directivas de atestación) en el menú de recursos del lado izquierdo de la ventana o del panel inferior. Si ve un mensaje para seleccionar el certificado para la autenticación, haga clic en Cancelar para continuar.
1. Seleccione **Download policy signer certificates** (Descargar firmantes de directivas de atestación). El botón estará deshabilitado para los proveedores de atestación creados sin el requisito de firma de directivas.
1. El archivo de texto descargado tendrá todos los certificados en formato JWS.
1. Compruebe el número de certificados y los certificados descargados.

### <a name="add-the-policy-signer-certificate"></a>Incorporación de un firmante de directivas de atestación

1.  Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1.  En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1.  Seleccione el proveedor de atestación y vaya a la página de información general.
1.  Seleccione **Policy signer certificates** (Firmantes de directivas de atestación) en el menú de recursos del lado izquierdo de la ventana o del panel inferior. Si ve un mensaje para seleccionar el certificado para la autenticación, haga clic en Cancelar para continuar.
1.  Seleccione **Add** (Agregar) en el menú superior. El botón estará deshabilitado para los proveedores de atestación creados sin el requisito de firma de directivas.
1.  Cargue el archivo del firmante de directivas de atestación y seleccione **Add** (Agregar). [Consulte los ejemplos de certificado de firma de directivas](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Eliminación del firmante de directivas de atestación

1.  Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1.  En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1.  Seleccione el proveedor de atestación y vaya a la página de información general.
1.  Seleccione **Policy signer certificates** (Firmantes de directivas de atestación) en el menú de recursos del lado izquierdo de la ventana o del panel inferior. Si ve un mensaje para seleccionar el certificado para la autenticación, haga clic en Cancelar para continuar.
1.  Seleccione **Delete** (Eliminar) en el menú superior. El botón estará deshabilitado para los proveedores de atestación creados sin el requisito de firma de directivas.
1.  Cargue el archivo del firmante de directivas de atestación y seleccione **Delete** (Eliminar). [Consulte los ejemplos de certificado de firma de directivas](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Directiva de atestación

En esta sección se describe cómo ver una directiva de atestación y cómo configurar las directivas que se crearon con y sin un requisito de firma de directiva.

### <a name="view-an-attestation-policy"></a>Visualización de una directiva de atestación

1.  Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1.  En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1.  Seleccione el proveedor de atestación y vaya a la página de información general.
1.  Seleccione **Directiva** en el menú de recursos del lado izquierdo de la ventana o en el panel inferior. Si ve un mensaje para seleccionar el certificado para la autenticación, haga clic en Cancelar para continuar.
1.  Seleccione el valor que prefiera para **Tipo de atestación** y visualice el campo **Directiva actual**.

### <a name="configure-an-attestation-policy"></a>Configuración de la directiva de atestación

Siga estos pasos para cargar una directiva en formato JWT o de texto si el proveedor de atestación se creó sin requisito de firma de directivas.

1. Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1. En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1. Seleccione el proveedor de atestación y vaya a la página de información general.
1. Seleccione **Directiva** en el menú de recursos del lado izquierdo de la ventana o en el panel inferior. Si ve un mensaje para seleccionar el certificado para la autenticación, haga clic en Cancelar para continuar.
1. En el menú superior, seleccione **Configurar**.
1. En **Policy Format** (Formato de la directiva), seleccione **JWT** o **Texto**.

   Si el proveedor de atestación se creó sin requisito de firma de directivas, el usuario puede cargar una directiva en formato **JWT** o **Texto**.

      - Si elige el formato JWT, cargue el archivo de directiva con el contenido de esta en formato **JWT sin firmar o firmado** y seleccione **Guardar**. [Consulte ejemplos de directivas](./policy-examples.md).
      - Si elige el formato de texto, cargue el archivo de directiva con el contenido en formato **Texto** o escriba el contenido de la directiva en el área de texto y seleccione **Guardar**. [Consulte ejemplos de directivas](./policy-examples.md).

   En la opción de carga de archivos, la vista previa de la directiva se muestra en formato de texto y no es editable.

1. Seleccione **Actualizar** en el menú superior para ver la directiva configurada.


Si el proveedor de atestación se creó con requisito de firma de directivas, siga estos pasos para cargar una directiva en formato JWT.

1.  Vaya al menú de Azure Portal o a la página principal y seleccione **Todos los recursos**.
1.  En el cuadro de filtro, escriba el nombre del proveedor de atestación.
1.  Seleccione el proveedor de atestación y vaya a la página de información general.
1.  Seleccione **Directiva** en el menú de recursos del lado izquierdo de la ventana o en el panel inferior.
1.  En el menú superior, seleccione **Configurar**.
1.  Cargue el archivo de directiva en **formato de JWT firmado** y seleccione **Guardar**. [Consulte ejemplos de directivas](./policy-examples.md).

    Si el proveedor de atestación se creó con un requisito de firma de directivas, el usuario solo puede cargar la directiva en **formato JWT firmado**.

    En la opción de carga de archivos, la vista previa de la directiva se muestra en formato de texto y no es editable.
    
1.  Seleccione **Actualizar** para ver la directiva configurada.

## <a name="next-steps"></a>Pasos siguientes

- [Creación y firma de una directiva de atestación](author-sign-policy.md)
- [Atestación de un enclave SGX con ejemplos de código](/samples/browse/?expanded=azure&terms=attestation)

