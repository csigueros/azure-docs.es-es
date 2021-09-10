---
title: Uso de la asignación de UserType con Azure AD Connect Cloud Sync
description: En este artículo se describe cómo asignar el atributo UserType con Cloud Sync.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b06381472549fd7d9b3f3b5dcd222fcd96f4f15
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506212"
---
# <a name="map-usertype-with-cloud-sync"></a>Asignación de UserType con Cloud Sync

Cloud Sync admite la sincronización del atributo **UserType** para objetos User.

De forma predeterminada, el atributo **UserType** no está habilitado para la sincronización porque no hay ningún atributo **UserType** correspondiente en la instancia local de Active Directory. Debe agregar manualmente esta asignación para la sincronización. Antes de realizar este paso, debe tener en cuenta el siguiente comportamiento aplicado por Azure Active Directory (Azure AD):

- Azure AD solo acepta dos valores para el atributo **UserType**: Miembro e Invitado.
- Si el atributo **UserType** no está asignado en Cloud Sync, los usuarios de Azure AD que se crearon mediante la sincronización de directorios tendrían el atributo **UserType** establecido en Miembro.

Antes de agregar una asignación del atributo **UserType**, debe decidir cómo se deriva de Active Directory local. Los siguientes son los métodos más comunes:

 - Designar un atributo de Active Directory local sin usar (p. ej., extensionAttribute1) que se utilizará como atributo de origen. El atributo de Active Directory local designado debería ser de tipo cadena, tener un solo valor y contener el valor Miembro o el valor Invitado.
 - Si elige este enfoque, debe asegurarse de que el atributo designado se rellena con el valor correcto para todos los objetos User existentes en Active Directory local que se sincronizan con Azure AD antes de que se habilite la sincronización del atributo **UserType**.

## <a name="add-the-usertype-mapping"></a>Adición de la asignación de UserType
Para agregar la asignación de **UserType**:

 1. En Azure Portal, seleccione **Azure Active Directory**.
 1. Seleccione **Azure AD Connect**.
 1. Seleccione **Manage cloud sync** (Administrar sincronización en la nube).
 1. En **Configuración**, seleccione su configuración.
 1. En **Administrar atributos**, seleccione **Haga clic para editar las asignaciones**.
 
    ![Captura de pantalla que muestra la edición de las asignaciones de atributos.](media/how-to-map-usertype/usertype-1.png) 

 1. Seleccione **Agregar asignación de atributos**.
 
    ![Captura de pantalla que muestra la adición de una nueva asignación de atributos.](media/how-to-map-usertype/usertype-2.png) 
1. Seleccione el tipo de asignación. Puede realizar la asignación de una de estas tres maneras:
   - Una asignación directa, por ejemplo, desde un atributo de Active Directory.
   - Una expresión, como IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member")
   - Una constante, por ejemplo, convertir todos los objetos de usuario en Invitado.
 
     ![Captura de pantalla que muestra la incorporación de un atributo UserType.](media/how-to-map-usertype/usertype-3.png)

1. En el menú desplegable **Atributo de destino**, seleccione **UserType**.
1. Seleccione **Aplicar** en la parte inferior de la página para crear una asignación para el atributo **UserType** de Azure AD.

## <a name="next-steps"></a>Pasos siguientes 

- [Escritura de expresiones para la asignación de atributos en Azure Active Directory](reference-expressions.md)
- [Configuración de la sincronización en la nube](how-to-configure.md)
