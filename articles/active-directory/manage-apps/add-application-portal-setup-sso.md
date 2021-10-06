---
title: 'Inicio rápido: Habilitación del inicio de sesión único para una aplicación empresarial'
titleSuffix: Azure AD
description: Habilite el inicio de sesión único para una aplicación empresarial en Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/21/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: b13c7ac86ec8d21f143f4069cf1d0c777bde4bda
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058367"
---
# <a name="quickstart-enable-single-sign-on-for-an-enterprise-application-in-azure-active-directory"></a>Inicio rápido: Habilitación del inicio de sesión único para una aplicación empresarial en Azure Active Directory

En este inicio rápido, usará el centro de administración de Azure Active Directory para habilitar el inicio de sesión único de una aplicación empresarial agregada al inquilino de Azure Active Directory (Azure AD). Después de configurar el inicio de sesión único, los usuarios podrán iniciar sesión con sus credenciales de Azure AD. 

Azure AD incluye una galería que contiene miles de aplicaciones previamente integradas que usan el inicio de sesión único. En este inicio rápido, se usa la aplicación empresarial llamada **Azure AD SAML Toolkit** como ejemplo, pero los conceptos se aplican a la mayoría de las [aplicaciones empresariales de la galería](../saas-apps/tutorial-list.md) preconfiguradas.

Para probar los pasos de este inicio rápido, se recomienda usar un entorno que no sea de producción.

## <a name="prerequisites"></a>Requisitos previos

Para configurar el inicio de sesión único, se necesita lo siguiente:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Finalización de los pasos de [Inicio rápido: Creación y asignación de una cuenta de usuario](add-application-portal-assign-users.md).

## <a name="enable-single-sign-on"></a>Habilitar el inicio de sesión único

Para habilitar el inicio de sesión único para una aplicación:

1. Vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con uno de los roles enumerados en los requisitos previos.
1. En el menú de la izquierda, seleccione **Aplicaciones empresariales**. Se abre el panel **Todas las aplicaciones**, en el que se ve una lista de las aplicaciones que hay en su inquilino de Azure AD. Busque y seleccione la aplicación que desea utilizar. Por ejemplo, **Azure AD SAML Toolkit 1**.
1. En la sección **Administrar** del menú de la izquierda, seleccione **Inicio de sesión único** para abrir el panel **Inicio de sesión único**.
1. Seleccione **SAML** para abrir la página de configuración del inicio de sesión único. Una vez configurada la aplicación, los usuarios pueden iniciar sesión en ella con sus credenciales del inquilino de Azure AD.
1. El proceso para configurar una aplicación para que use Azure AD para el inicio de sesión único basado en SAML varía en función de la aplicación. Para cualquiera de las aplicaciones empresariales de la galería, use el vínculo para encontrar información sobre los pasos necesarios para configurar la aplicación. Los pasos para **Azure AD SAML Toolkit** se enumeran en este inicio rápido.

    :::image type="content" source="media/add-application-portal-setup-sso/saml-configuration.png" alt-text="Configure el inicio de sesión único para una aplicación empresarial.":::

1. En la sección **Configurar Azure AD SAML Toolkit 1**, registre los valores de las propiedades de **dirección URL de inicio de sesión**, **identificador de Azure AD** y **dirección URL de cierre de sesión** que se usarán más adelante.

## <a name="configure-single-sign-on-in-the-tenant"></a>Configuración del inicio de sesión único en el inquilino

Agregue valores de dirección URL de inicio de sesión y respuesta y descargue un certificado para comenzar la configuración del inicio de sesión único en Azure AD.

Para configurar el inicio de sesión único en Azure AD:

1. En Azure Portal, seleccione **Editar** en la sección **Configuración básica de SAML** en el panel **Configurar inicio de sesión único**. 
1. Para la **dirección URL de respuesta (URL del Servicio de consumidor de aserciones)** , escriba `https://samltoolkit.azurewebsites.net/SAML/Consume`.
1. Para la **dirección URL de inicio de sesión**, escriba `https://samltoolkit.azurewebsites.net/`.
1. Seleccione **Guardar**.
1. En la sección **Certificado de firma de SAML**, seleccione **Descargar** para **Certificado (sin procesar)** para descargar el certificado de firma de SAML y guardarlo para su uso posterior.

## <a name="configure-single-sign-on-in-the-application"></a>Configuración del inicio de sesión único en la aplicación

El uso del inicio de sesión único en la aplicación requiere que registre la cuenta de usuario con la aplicación y que agregue los valores de configuración de SAML que registró anteriormente.

### <a name="register-the-user-account"></a>Registro de la cuenta de usuario

Para registrar una cuenta de usuario con la aplicación:

1. Abra una nueva ventana del explorador y vaya a la dirección URL de inicio de sesión de la aplicación. Para la aplicación **Azure AD SAML Toolkit**, la dirección es `https://samltoolkit.azurewebsites.net`.
1. Seleccione **Registrar** en la esquina superior derecha de la página.

    :::image type="content" source="media/add-application-portal-setup-sso/toolkit-register.png" alt-text="Registre una cuenta de usuario en la aplicación Azure AD SAML Toolkit.":::

1. En **Correo electrónico**, escriba la dirección de correo electrónico del usuario que tendrá acceso a la aplicación. Por ejemplo, en un inicio rápido anterior, se creó la cuenta de usuario que usa la dirección de `contosouser1@contoso.com`. Asegúrese de cambiar `contoso.com` por el dominio del inquilino.
1. Escriba una **contraseña** y confírmela.
1. Seleccione **Registrar**.

### <a name="configure-saml-settings"></a>Configuración de SAML

Para definir la configuración de SAML para la aplicación:

1. Inicie sesión con las credenciales de la cuenta de usuario que ha creado y seleccione **Configuración de SAML** en la esquina superior izquierda de la página.
1. Seleccione **Crear** en el medio de la página.
1. En **Dirección URL de inicio de sesión**, **Identificador de Azure AD** y **Dirección URL de cierre de sesión**, escriba los valores que registró anteriormente.
1. Seleccione **Elegir archivo** para cargar el certificado que descargó anteriormente.
1. Seleccione **Crear**.
1. Copie los valores de la **dirección URL de inicio de sesión iniciado por SP** y la **URL del Servicio de consumidor de aserciones (ACS)** para su uso posterior.

## <a name="update-single-sign-on-values"></a>Actualización de los valores de inicio de sesión único

Use los valores que registró para la **dirección URL de inicio de sesión iniciado por SP** y la **URL del Servicio de consumidor de aserciones (ACS)** para actualizar los valores de inicio de sesión único en el inquilino.

Para actualizar los valores de inicio de sesión único:

1. En Azure Portal, seleccione **Editar** en la sección **Configuración básica de SAML** en el panel **Configurar inicio de sesión único**. 
1. En **Dirección URL de respuesta (URL del Servicio de consumidor de aserciones)** , escriba el valor de la **URL del Servicio de consumidor de aserciones (ACS)** que registró anteriormente.
1. En **URL de inicio de sesión**, escriba el valor de la **dirección URL de inicio de sesión iniciado por SP** que registró anteriormente.
1. Seleccione **Guardar**.

## <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

Puede probar la configuración del inicio de sesión único desde el panel **Configurar inicio de sesión único**.

Para probar el inicio de sesión único:

1. En la sección **Probar el inicio de sesión único con Azure AD SAML Toolkit 1**, en el panel **Configurar inicio de sesión único**, seleccione **Probar**.
1. Inicie sesión en la aplicación con las credenciales de Azure AD de la cuenta de usuario que asignó a la aplicación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto completar el siguiente inicio rápido, mantenga la aplicación empresarial que ha creado. De lo contrario, puede considerar la posibilidad de eliminarla para limpiar el inquilino. Para más información, consulte [Eliminación de una aplicación](delete-application-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a configurar las propiedades de una aplicación empresarial.
> [!div class="nextstepaction"]
> [Configuración de una aplicación](add-application-portal-configure.md)
