---
title: Adición del inicio de sesión único basado en contraseña a una aplicación
description: Agregue el inicio de sesión único basado en contraseña a una aplicación en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 6ed7241e62b037a2d0ebd5303bd18e3c859d93f0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639667"
---
# <a name="add-password-based-single-sign-on-to-an-application-in-azure-active-directory"></a>Adición del inicio de sesión único basado en contraseña a una aplicación en Azure Active Directory

En este artículo, se muestra cómo configurar el inicio de sesión único (SSO) basado en contraseña en Azure Active Directory (Azure AD). Con el inicio de sesión único basado en contraseña, un usuario inicia sesión en la aplicación con un nombre de usuario y una contraseña la primera vez que accede. Después del primer inicio de sesión, Azure AD envía el nombre de usuario y la contraseña a la aplicación. 

El inicio de sesión único basado en contraseña usa el proceso de autenticación existente que proporciona la aplicación. Cuando se habilita el inicio de sesión único basado en contraseña para una aplicación, Azure AD recopila y almacena de forma segura los nombres de usuario y contraseñas de la aplicación. Las credenciales de usuario se almacenan en un estado cifrado en el directorio. Se admite el inicio de sesión único basado en contraseña para cualquier aplicación basada en la nube cuya página de inicio de sesión esté basada en HTML.

Elija el inicio de sesión único basado en contraseña cuando:
- Una aplicación no admita el protocolo SAML de inicio de sesión único.
- Una aplicación se autentica con un nombre de usuario y una contraseña en lugar de con tokens de acceso y encabezados.

La página de configuración para SSO basado en contraseña es sencilla. Incluye solo la dirección URL de la página de inicio de sesión que usa la aplicación. Esta cadena debe ser la página que incluye el campo de entrada del nombre de usuario.

## <a name="prerequisites"></a>Requisitos previos

Para configurar el inicio de sesión único basado en contraseña en el inquilino de Azure AD, necesita lo siguiente:
-   Una cuenta de Azure con una suscripción activa. [Cree su cuenta de forma gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
-   Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
-   Una aplicación que admita el inicio de sesión único basado en contraseña.

## <a name="configure-password-based-single-sign-on"></a>Configuración del inicio de sesión único basado en contraseña

1.  Inicie sesión en [Azure Portal](https://portal.azure.com) con el rol adecuado.
1.  Seleccione **Azure Active Directory** en Servicios de Azure y, a continuación, **Aplicaciones empresariales**.
1.  Busque y seleccione la aplicación a la que desea agregar el inicio de sesión único basado en contraseña.
1.  Seleccione **Inicio de sesión único** y, a continuación, **Basado en contraseña**.
1.  Escriba la dirección URL de la página de inicio de sesión de la aplicación.
1.  Seleccione **Guardar**. 

Azure AD analiza el código HTML de la página de inicio de sesión para los campos de entrada de nombre de usuario y contraseña. Si el intento se realiza correctamente, habrá terminado. El siguiente paso es la [asignación de usuarios o grupos](add-application-portal-assign-users.md) a la aplicación. 

Después de asignar usuarios y grupos, puede proporcionar las credenciales que se usarán para un usuario cuando inicie sesión en la aplicación. 

1. Seleccione **Usuarios y grupos**, active la casilla correspondiente a la fila del usuario o del grupo y, después, seleccione **Actualizar credenciales**. 
1. Escriba el nombre de usuario y la contraseña que se usarán para el usuario o grupo. Si no lo hace, se solicitará a los usuarios que especifiquen ellos mismos las credenciales al inicio.

## <a name="manual-configuration"></a>Configuración manual

Si se produce un error en el intento de análisis de Azure AD, puede configurar el inicio de sesión manualmente.

1. Seleccione **Establecer configuración de inicio de sesión único con contraseña de {nombreDeAplicación}** para mostrar la página **Configurar inicio de sesión**.
1. Seleccione **Detectar campos de inicio de sesión manualmente**. Aparecerán más instrucciones que describen la detección manual de los campos de inicio de sesión.
1. Seleccione **Capturar campos de inicio de sesión**. Se abre una página de estado de la captura en una nueva pestaña, que muestra el mensaje Actualmente la captura de metadatos está en curso.
1. Si aparece la casilla **My Apps Extension Required** (Se requiere la extensión Aplicaciones) en una nueva pestaña, seleccione **Instalar ahora** para instalar la extensión del explorador Extensión de inicio de sesión seguro de mis aplicaciones. (La extensión del explorador requiere Microsoft Edge, Chrome o Firefox). A continuación, instale, inicie y active la extensión, y actualice la página de estado de la captura. La extensión del explorador abre otra pestaña que muestra la dirección URL especificada.
1. En la pestaña con la dirección URL especificada, recorra el proceso de inicio de sesión. Rellene los campos de nombre de usuario y contraseña e intente iniciar sesión. (No tiene que proporcionar la contraseña correcta). Un mensaje le pide que guarde los campos de inicio de sesión capturados.
1. Seleccione **Aceptar**. La extensión del explorador actualiza la página de estado de la captura con el mensaje **Los metadatos se han actualizado para la aplicación**. La pestaña del explorador se cierra.
1. En la página Configurar inicio de sesión de Azure AD, seleccione **Bien. Pude iniciar sesión en la aplicación correctamente**.
1. Seleccione **Aceptar**.

## <a name="next-steps"></a>Pasos siguientes

- [Administración del acceso a aplicaciones](what-is-access-management.md)
