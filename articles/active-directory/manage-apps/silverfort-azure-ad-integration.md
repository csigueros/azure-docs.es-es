---
title: Protección del acceso híbrido con Azure AD y Silverfort
description: En este tutorial, obtendrá información sobre cómo integrar Silverfort con Azure AD para el acceso híbrido seguro.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/13/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429b00a03aaf39fa5eb5657253ee6ee51190049
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274565"
---
# <a name="tutorial-configure-silverfort-with-azure-active-directory-for-secure-hybrid-access"></a>Tutorial: Configuración de Silverfort con Azure Active Directory para el acceso híbrido seguro

En este tutorial, obtendrá información sobre cómo integrar Silverfort con Azure Active Directory (Azure AD). [Silverfort](https://www.silverfort.com/) usa una tecnología innovadora sin agentes y sin proxy para conectar todos los recursos locales y en la nube a Azure AD. Esta solución permite a las organizaciones aplicar la protección de identidades, la visibilidad y la experiencia del usuario en todos los entornos de Azure AD. Habilita la supervisión universal basada en riesgos y la evaluación de la actividad de autenticación para entornos locales y en la nube y evita de forma proactiva las amenazas.  

Silverfort puede conectar sin problemas cualquier tipo de recurso a Azure AD, como si fuera una aplicación web moderna. Por ejemplo:

- Aplicaciones heredadas e internas

- Escritorio remoto y Secure Shell (SSH)

- Herramientas de línea de comandos y otro acceso de administrador

- Recursos compartidos de archivos y bases de datos

- Infraestructura y sistemas industriales

Estos recursos **con puente** aparecen como aplicaciones normales en Azure AD y se pueden proteger con el acceso condicional, el inicio de sesión único (SSO), la autenticación multifactor, la auditoría y mucho más.

Esta solución combina todos los recursos corporativos y las plataformas de administración de identidad y acceso (IAM) de terceros. Por ejemplo, Active Directory, Servicios de federación de Active Directory (ADFS) y Servicio de autenticación remota telefónica de usuario (RADIUS) en Azure AD, incluidos entornos híbridos y de varias nubes.

## <a name="scenario-description"></a>Descripción del escenario

En esta guía, configurará y probará el puente de Azure AD de Silverfort en el inquilino de Azure AD.

Una vez configurado, puede crear directivas de autenticación de Silverfort que unan las solicitudes de autenticación de varios orígenes de identidad con Azure AD para SSO. Una vez unida una aplicación, se puede administrar en Azure AD.

En el diagrama siguiente se muestran los componentes incluidos en la solución y la secuencia de autenticación orquestada por Silverfort.

![Imagen que muestra el diagrama de la arquitectura](./media/silverfort-azure-ad-integration/silverfort-architecture-diagram.png)

| Paso | Descripción|
|:---------|:------------|
| 1. | El usuario envía la solicitud de autenticación al proveedor de identidades (IdP) original a través de protocolos como Kerberos, SAML, NTLM, OIDC y LDAP.|
| 2. | La respuesta se enruta tal y como está a Silverfort para que se valide y se compruebe el estado de autenticación.|
| 3. | Silverfort proporciona visibilidad, detección y puentes a Azure AD.|
| 4. | Si la aplicación se configura como **con puente**, la decisión de autenticación se pasa a Azure AD. Azure AD evalúa las directivas de acceso condicional y valida la autenticación.|
| 5. | La respuesta del estado de autenticación se libera y Silverfront la envía tal y como está al IdP. |
| 6.| El IdP concede o deniega el acceso al recurso.|
| 7. | El usuario recibe una notificación si se concede o se deniega la solicitud de acceso. |

## <a name="prerequisites"></a>Requisitos previos

Para configurar el inicio de sesión único de una aplicación que haya agregado a su inquilino de Azure AD, necesitará lo siguiente:

- Una cuenta de Azure con una suscripción activa. Puede crear una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Uno de los siguientes roles en la cuenta de Azure: Administrador de empresa, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.

- Una aplicación que admita el inicio de sesión único y que se haya configurado previamente y agregado ya a la galería de Azure AD. La aplicación de Silverfort de la galería de Azure AD ya está preconfigurada. Deberá agregarla como una aplicación empresarial desde la galería.

## <a name="onboard-with-silverfort"></a>Incorporación con Silverfort

Para implementar Silverfort en su inquilino o infraestructura, [póngase en contacto con Silverfort](https://www.silverfort.com/). Instale la aplicación de escritorio de Silverfort en las estaciones de trabajo pertinentes.

## <a name="configure-silverfort-and-create-a-policy"></a>Configuración de Silverfort y creación de una directiva

1. Desde un explorador, inicie sesión en la **consola de administración de Silverfort**.

2. En el menú principal, vaya a **Settings** (Configuración) y, a continuación, desplácese hasta **Azure AD Bridge Connector** (Conector de puente de Azure AD) en la sección "General" (General). Confirme el id. de inquilino y, a continuación, seleccione **Authorize** (Autorizar).

   ![Imagen que muestra el conector de puente de Azure AD](./media/silverfort-azure-ad-integration/azure-ad-bridge-connector.png)

   ![Imagen que muestra la confirmación del registro](./media/silverfort-azure-ad-integration/grant-permission.png)

3. Se muestra una confirmación de registro en una nueva pestaña. Cierre esta pestaña.

   ![Imagen que muestra el registro completado](./media/silverfort-azure-ad-integration/registration-completed.png)

4. En la página **Settings** (Configuración), seleccione **Save changes** (Guardar cambios).

   ![Imagen que muestra el adaptador de Azure AD](./media/silverfort-azure-ad-integration/silverfort-azure-ad-adapter.png)

    Inicie sesión en la consola de Azure AD. Verá la aplicación **Adaptador de Silverfort para Azure AD** registrada como una aplicación empresarial.

   ![Imagen que muestra la aplicación empresarial](./media/silverfort-azure-ad-integration/enterprise-application.png)

5. En la consola de administración de Silverfot, vaya a la página **Policies** (Directivas) y seleccione **Create Policy** (Crear directiva).

6. Aparecerá el diálogo **New Policy** (Nueva directiva). Escriba un valor en el campo **Policy Name** (Nombre de directiva) que indique el nombre de la aplicación que se creará en Azure. Por ejemplo, si va a agregar varios servidores o aplicaciones en esta directiva, asígnele un nombre para reflejar los recursos cubiertos por la directiva. En el ejemplo, se creará una directiva para el servidor *SL-APP1*.

   ![Imagen que muestra la definición de la directiva](./media/silverfort-azure-ad-integration/define-policy.png)

7. Seleccione el tipo adecuado de autenticación y el protocolo en los campos **Authentication** (Autenticación) y **Protocol** (Protocolo).

8. En el campo **Users and Groups** (Usuarios y grupos), seleccione el icono de edición para configurar los usuarios que se verán afectados por la directiva. La autenticación de estos usuarios se unirá a Azure AD.

   ![Imagen que muestra los usuarios y grupos](./media/silverfort-azure-ad-integration/user-groups.png)

9. Busque y seleccione usuarios, grupos u unidades organizativas (UO).

   ![Imagen que muestra la búsqueda de usuarios](./media/silverfort-azure-ad-integration/search-users.png)

   Los usuarios seleccionados aparecerán en el cuadro SELECCIONADO.

   ![Imagen que muestra el usuario seleccionado](./media/silverfort-azure-ad-integration/select-user.png)

10. Seleccione el valor de **Source** (Origen) al que se aplicará la directiva. En este ejemplo, se selecciona el valor *All Devices* (Todos los dispositivos).

    ![Imagen que muestra el origen](./media/silverfort-azure-ad-integration/source.png)

11. Establezca el valor de **Destination** (Destino) en *SL-App1*. Puede seleccionar el botón de edición para cambiar o agregar más recursos o grupos de recursos (opcional).

    ![Imagen que muestra el destino](./media/silverfort-azure-ad-integration/destination.png)

12. Seleccione la acción para **AZURE AD BRIDGE** (PUENTE DE AZURE AD).

    ![Imagen que muestra cómo guardar el puente de Azure AD](./media/silverfort-azure-ad-integration/save-azure-ad-bridge.png)

13. Seleccione **SAVE** (GUARDAR) para guardar la directiva nueva. Se le pedirá que la habilite o la active.

    ![Imagen que muestra el estado de cambio](./media/silverfort-azure-ad-integration/change-status.png)

    La directiva aparecerá en la página "Policies" (Directivas), en la sección "Azure AD Bridge" (Puente de Azure AD):

    ![Imagen que muestra cómo agregar una directiva](./media/silverfort-azure-ad-integration/add-policy.png)

14. Vuelva a la consola de Azure AD y vaya a **Aplicaciones empresariales**. Ahora debería aparecer la nueva aplicación de Silverfort. Esta aplicación ahora se puede incluir en las [directivas de CA](../authentication/tutorial-enable-azure-mfa.md?bc=/azure/active-directory/conditional-access/breadcrumb/toc.json&toc=/azure/active-directory/conditional-access/toc.json%23create-a-conditional-access-policy).

## <a name="next-steps"></a>Pasos siguientes

- [Adaptador de Azure AD de Silverfort](https://azuremarketplace.microsoft.com/marketplace/apps/aad.silverfortazureadadapter?tab=overview)

- [Recursos de Silverfort](https://www.silverfort.com/resources/)
