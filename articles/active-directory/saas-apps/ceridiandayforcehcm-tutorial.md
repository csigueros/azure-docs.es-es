---
title: 'Tutorial: Integración de Azure Active Directory con Ceridian Dayforce HCM | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2a66feb85a9873a34470cfccc6360f9e24612f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739798"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integración de Azure Active Directory con Ceridian Dayforce HCM

En este tutorial, aprenderá a integrar Ceridian Dayforce HCM con Azure Active Directory. Al integrar Ceridian Dayforce HCM con Azure AD, puede:

* Controlar quién tiene acceso a Ceridian Dayforce HCM en Azure AD.
* Permitir que los usuarios inicien sesión automáticamente en Ceridian Dayforce HCM con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Ceridian Dayforce HCM.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Ceridian Dayforce HCM admite inicio de sesión único iniciado por **SP**.

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Incorporación de Ceridian Dayforce HCM desde la galería

Para configurar la integración de Ceridian Dayforce HCM en Azure AD, es preciso agregar Ceridian Dayforce HCM desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Ceridian Dayforce HCM** en el cuadro de búsqueda.
1. Seleccione **Ceridian Dayforce HCM** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Configuración y prueba del inicio de sesión único de Azure AD para Ceridian Dayforce HCM

Configure y pruebe el inicio de sesión único de Azure AD con Ceridian Dayforce HCM mediante un usuario de prueba denominado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Ceridian Dayforce HCM.

Para configurar y probar el inicio de sesión único de Azure AD con Ceridian Dayforce HCM, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único de Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-sso)** : para configurar el inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Ceridian Dayforce HCM](#create-ceridian-dayforce-hcm-test-user)** : para tener un homólogo de B.Simon en Ceridian Dayforce HCM que esté vinculado a la representación de este usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD 

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Ceridian Dayforce HCM**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. En el cuadro de texto **URL de inicio de sesión** , escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación de Ceridian Dayforce HCM.

    | Entorno | URL |
    | :-- | :-- |
    | Para producción | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Para pruebas | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. En el cuadro de texto **Identificador**, escriba la dirección URL con el siguiente patrón: .

    | Entorno | URL |
    | :-- | :-- |
    | Para producción | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Para pruebas | `https://fs-test.dayforcehcm.com/sp` |

    c. En el cuadro de texto **URL de respuesta** , escriba la dirección URL que se usa en Azure AD para exponer la respuesta.

    | Entorno | URL |
    | :-- | :-- |
    | Para producción | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Para pruebas | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte de cliente de Ceridian Dayforce HCM](https://www.ceridian.com/support) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. La aplicación Ceridian Dayforce HCM espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Atributos de usuario**.

    ![Captura de pantalla que muestra User Attributes (Atributos de usuario) con el icono de edición seleccionado.](common/edit-attribute.png)

6. En la sección **Notificaciones del usuario** del cuadro de diálogo **Atributos de usuario**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:

    | Nombre | Atributo de origen|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    ![Captura de pantalla que muestra User claims (Reclamaciones de usuario) con la opción Add new claim (Agregar nueva reclamación).](common/new-save-attribute.png)

    ![Captura de pantalla que muestra Manage user claims (Administrar reclamaciones de usuario) para escribir los valores que se describen.](common/new-attribute-details.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. Deje **Espacio de nombres** en blanco.

    d. Seleccione **Atributo** como origen.

    e. En la lista **Atributo de origen**, seleccione el atributo de usuario que quiere usar en su implementación. Por ejemplo, si quiere usar EmployeeID como identificador de usuario único y ha almacenado el valor del atributo en ExtensionAttribute2, seleccione user.extensionattribute2.

    f. Haga clic en **Aceptar**.

    g. Haga clic en **Save**(Guardar).

7. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar **XML de metadatos** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/metadataxml.png)

8. En la sección **Set up Ceridian Dayforce HCM** (Configurar Ceridian Dayforce HCM), copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá a B.Simon usar el inicio de sesión único de Azure concediéndole acceso a Ceridian Dayforce HCM.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Ceridian Dayforce HCM**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Configuración del inicio de sesión único de Ceridian Dayforce HCM

Para configurar el inicio de sesión único en **Ceridian Dayforce HCM**, debe enviar el **XML de metadatos** descargado y las direcciones URL apropiadas copiadas de Azure Portal al [equipo de soporte técnico de Ceridian Dayforce HCM](https://www.ceridian.com/support). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Creación de un usuario de prueba de Ceridian Dayforce HCM

En esta sección, creará un usuario llamado Britta Simon en Ceridian Dayforce HCM. Trabaje con el [equipo de soporte técnico de Ceridian Dayforce HCM](https://www.ceridian.com/support) para agregar los usuarios a la plataforma de Ceridian Dayforce HCM. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones. 

* Haga clic en **Probar esta aplicación** en Azure Portal. Esta acción le redirigirá a la dirección URL de inicio de sesión de Ceridian Dayforce HCM, desde donde podrá comenzar el flujo de inicio de sesión. 

* Acceda directamente a la dirección URL de inicio de sesión de Ceridian Dayforce HCM y ponga en marcha el flujo de inicio de sesión desde allí.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Ceridian Dayforce HCM en Aplicaciones, se le redirigirá a la URL de inicio de sesión de Ceridian Dayforce HCM. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Ceridian Dayforce HCM, puede aplicar el control de sesión, que protege frente a la filtración e infiltración de la información confidencial de la organización en tiempo real. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).