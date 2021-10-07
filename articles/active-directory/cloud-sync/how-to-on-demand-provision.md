---
title: Aprovisionamiento a petición en la sincronización en la nube de Azure AD Connect
description: En este artículo se describe cómo usar la característica de sincronización en la nube de Azure AD Connect para probar los cambios de configuración.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20719182a9b97bc783ef2bdc16ccad327dba79aa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742571"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Aprovisionamiento a petición en la sincronización en la nube de Azure AD Connect

Puede usar la característica de sincronización en la nube de Azure Active Directory (Azure AD) Connect para probar los cambios de configuración aplicando estos cambios a un solo usuario. Este aprovisionamiento a petición ayuda a validar y comprobar que los cambios realizados en la configuración se aplicaron correctamente y que se están sincronizando correctamente con Azure AD.  

> [!IMPORTANT] 
> Cuando se usa el aprovisionamiento a petición, los filtros de ámbito no se aplican al usuario seleccionado. Puede usar el aprovisionamiento a petición en los usuarios que están fuera de las unidades de organización que especificó.

Para obtener información adicional y ver un ejemplo, observe el vídeo siguiente.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mW]

## <a name="validate-a-user"></a>Validación de un usuario
Para usar el aprovisionamiento a petición, siga estos pasos:

1.  En Azure Portal, seleccione **Azure Active Directory**.
2.  Seleccione **Azure AD Connect**.
3.  Seleccione **Manage cloud sync** (Administrar sincronización en la nube).

    ![Captura de pantalla que muestra el vínculo para administrar la sincronización en la nube.](media/how-to-install/install-6.png)
4. En **Configuración**, seleccione su configuración.
5. En **Validar**, seleccione el botón **Aprovisionar un usuario**. 

   ![Captura de pantalla que muestra el botón para aprovisionar un usuario.](media/how-to-on-demand-provision/on-demand-2.png)

6. En la pantalla **Provision on demand** (Aprovisionamiento a petición), escriba el nombre distintivo de un usuario y seleccione el botón **Aprovisionar**.  
 
   ![Captura de pantalla que muestra un nombre de usuario y un botón de aprovisionamiento.](media/how-to-on-demand-provision/on-demand-3.png)
7. Una vez finalizado el aprovisionamiento, aparece una pantalla de éxito con cuatro marcas de verificación verdes. Si hay errores, estos aparecen a la izquierda.

   ![Captura de pantalla que muestra el aprovisionamiento correcto.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Obtención de detalles sobre el aprovisionamiento
Ahora puede consultar la información del usuario y determinar si se han aplicado los cambios realizados en la configuración. En el resto de este artículo se describen las secciones individuales que aparecen en los detalles de un usuario que se ha sincronizado correctamente.

### <a name="import-user"></a>Importación de usuario
En esta sección **Importar usuario** se proporciona información sobre el usuario que se importó desde Active Directory. Este es el aspecto del usuario antes del aprovisionamiento en Azure AD. Seleccione el vínculo **Ver detalles** para mostrar esta información.

![Captura de pantalla del botón para ver los detalles de un usuario importado.](media/how-to-on-demand-provision/on-demand-5.png)

Con esta información, puede ver los distintos atributos que se importaron y sus valores. Si creó una asignación de atributos personalizados, puede ver su valor aquí.

![Captura de pantalla que muestra los detalles del usuario.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Determinación de si el usuario está en el ámbito
En la sección **Determinación de si el usuario está en el ámbito** se proporciona información sobre si el usuario que se importó a Azure AD está en el ámbito. Seleccione el vínculo **Ver detalles** para mostrar esta información.

![Captura de pantalla del botón para ver los detalles del ámbito del usuario.](media/how-to-on-demand-provision/on-demand-7.png)

Con esta información, puede ver si el usuario está en el ámbito.

![Captura de pantalla que muestra los detalles del ámbito del usuario.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Coincidencia de usuario entre el sistema de origen y el de destino
En la sección **Coincidencia de usuario entre el sistema de origen y el de destino** se proporciona información sobre si el usuario ya existe en Azure AD y si debe producirse una combinación en lugar de aprovisionar a un nuevo usuario. Seleccione el vínculo **Ver detalles** para mostrar esta información.

![Captura de pantalla del botón para ver los detalles de un usuario coincidente.](media/how-to-on-demand-provision/on-demand-8.png)

Con esta información, puede ver si se encontró una coincidencia o si se va a crear un nuevo usuario.

![Captura de pantalla que muestra la información del usuario.](media/how-to-on-demand-provision/on-demand-11.png)

Los detalles coincidentes muestran un mensaje con una de las tres operaciones siguientes:
- **Crear**: se crea un usuario en Azure AD.
- **Actualizar**: se actualiza un usuario en función de un cambio realizado en la configuración.
- **Eliminar**: se quita un usuario de Azure AD.

En función del tipo de operación que haya realizado, el mensaje variará.

### <a name="perform-action"></a>Realizar acción
En la sección **Realizar acción** se proporciona información sobre el usuario que se aprovisionó o exportó en Azure AD después de aplicar la configuración. Este es el aspecto del usuario después del aprovisionamiento en Azure AD. Seleccione el vínculo **Ver detalles** para mostrar esta información.

![Captura de pantalla del botón para ver los detalles de una acción realizada.](media/how-to-on-demand-provision/on-demand-9.png)

Con esta información, puede ver los valores de los atributos una vez aplicada la configuración. ¿Tienen un aspecto similar a lo que se importó o son los diferentes? ¿Se aplicó correctamente la configuración?  

Este proceso le permite realizar un seguimiento de la transformación de los atributos mientras se desplaza a través de la nube al inquilino de Azure AD.

![Captura de pantalla que muestra los detalles del atributo del que se hace seguimiento.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es la sincronización en la nube de Azure AD Connect?](what-is-cloud-sync.md)
- [Instalación de la sincronización en la nube de Azure AD Connect](how-to-install.md)
 