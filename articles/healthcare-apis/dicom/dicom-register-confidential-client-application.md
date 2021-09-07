---
title: 'Registro de una aplicación cliente confidencial en Azure Active Directory: Azure Healthcare APIs para DICOM'
description: En este artículo se explica cómo registrar una aplicación cliente confidencial en Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f5dd5e5136ac2cd47b637d7b95cb7ba95c3ddaaa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780513"
---
# <a name="register-a-confidential-client-application"></a>Registro de una aplicación cliente confidencial

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial aprenderá a registrar una aplicación cliente confidencial en Azure Active Directory (Azure AD).

## <a name="register-a-new-application"></a>Registro de una nueva aplicación

Un registro de aplicación cliente es una representación de Azure AD de una aplicación que se puede usar para autenticar en nombre de un usuario y solicitar acceso a aplicaciones de recursos. Una aplicación cliente confidencial es una aplicación en la que se puede confiar para almacenar un secreto y presentar dicho secreto cuando se solicitan tokens de acceso. Ejemplos de aplicaciones confidenciales son las aplicaciones de servidor.

Para registrar una nueva aplicación cliente confidencial, consulte los siguientes pasos.

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**.
2. Seleccione la hoja **Registros de aplicaciones**.
3. Seleccione **Nuevo registro**.

   [ ![Registros de aplicaciones de Azure AD.](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. Asigne a la aplicación un nombre para el usuario.

   [ ![Registro de una aplicación de Azure.](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. En **Tipos de cuenta admitidos**, seleccione quién puede usar la aplicación o acceder a la API.
6. (**Opcional**) Proporcione un **URI de redireccionamiento**. Estos detalles se pueden cambiar más adelante, pero, si conoce la dirección URL de respuesta de la aplicación, indíquela ahora.
7. Seleccione **Registrar**.

## <a name="api-permissions"></a>Permisos de API

Ahora que ha registrado su aplicación, debe seleccionar los permisos de API que la aplicación debe solicitar en nombre de los usuarios.

1. Seleccione la hoja **Permisos de API**.

   [ ![Agregación de permisos de API](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. Seleccione **Agregar un permiso**.

   Si usa Azure Healthcare APIs, agregará un permiso al servicio DICOM buscando **Azure API for DICOM** en **API usadas en mi organización**. 

   [ ![Búsqueda de permisos de API](media/dicom-search-apis-permissions.png) ](media/dicom-search-apis-permissions.png#lightbox)

   La búsqueda de "Azure API for DICOM" solo devolverá resultados si ya ha implementado el servicio DICOM en el área de trabajo.

   Si hace referencia a otra aplicación de recursos, seleccione el registro de la aplicación de recursos de la API de DICOM que ha creado antes en **API usadas en mi organización**.

3. Seleccione los ámbitos (permisos) que la aplicación cliente confidencial solicitará en nombre del usuario. Seleccione **user_impersonation** y después **Agregar permisos**.

   [ ![Selección de ámbitos de permisos](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

## <a name="application-secret"></a>Secreto de la aplicación

1. Seleccione **Certificados y secretos** y luego seleccione **Nuevo secreto de cliente**.

   [ ![Certificados y secretos](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. Escriba una **descripción** del secreto de cliente. Seleccione el menú desplegable **Expira** para elegir un período de tiempo de expiración y, a continuación, haga clic en **Agregar**.

   [ ![Descripción del secreto de cliente](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. Una vez creada la cadena secreta de cliente, copie su **valor** y su **Id.** y almacénelos en una ubicación segura.

   [ ![Id. de valor de secreto de cliente](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

   > [!NOTE]
   > La cadena secreta de cliente solo está visible una vez dentro de Azure Portal. Si sale de la página web "Certificados y secretos" y, a continuación, vuelve a ella, la cadena Value se enmascara. Es importante realizar una copia de la cadena secreta de cliente inmediatamente después de generarla. Si no tiene una copia de seguridad del secreto de cliente, repita los pasos anteriores para volver a generarlo.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se le ha guiado por los pasos para registrar una aplicación cliente confidencial en Azure AD. También se le ha guiado por los pasos para agregar permisos de API a Azure API for DICOM. Por último, se le ha mostrado cómo crear un secreto de aplicación. 

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)



