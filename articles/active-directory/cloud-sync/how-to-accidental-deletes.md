---
title: Eliminaciones accidentales de la sincronización en la nube de Azure AD Connect
description: En este tema se describe cómo usar la característica de eliminación accidental para evitar eliminaciones.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5055b847eab1ee53688abfa308b5969f7526f668
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742669"
---
# <a name="accidental-delete-prevention"></a>Protección contra eliminación accidental

En el documento siguiente se describe la característica de eliminación accidental para la sincronización en la nube de Azure AD Connect.  La característica de eliminación accidental está diseñada para protegerle de los cambios de configuración accidentales y de los cambios en el directorio local que afectarían a un gran número de usuarios y grupos.  Esta característica permite:

- configurar la capacidad de evitar eliminaciones accidentales automáticamente 
- establecer el número de objetos (umbral) más allá del cual la configuración surtirá efecto 
- configurar una dirección de correo electrónico de notificación para que puedan recibir una notificación por correo electrónico cuando el trabajo de sincronización en cuestión se ponga en cuarentena para este escenario 

Para usar esta característica, establezca el umbral en el número de objetos que, de eliminarse, harán que se detenga la sincronización.  Por lo tanto, si se alcanza este número, la sincronización se detendrá y se enviará una notificación al correo electrónico especificado.  Esta notificación le permitirá investigar qué está sucediendo.

Para obtener información adicional y ver un ejemplo, vea el vídeo siguiente.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mV]


## <a name="configure-accidental-delete-prevention"></a>Configuración de la protección contra eliminación accidental
Para usar esta nueva característica, siga los pasos que se indican a continuación.


1.  En Azure Portal, seleccione **Azure Active Directory**.
2.  Seleccione **Azure AD Connect**.
3.  Seleccione **Manage cloud sync** (Administrar sincronización en la nube).
4. En **Configuración**, seleccione su configuración.
5. En **Configuración** rellene lo siguiente:
    - **Correo electrónico de notificación**: correo electrónico que se usará para las notificaciones
    - **Prevent accidental deletions** (Evitar eliminaciones accidentales): active esta casilla para habilitar la característica.
    - **Accidental deletion threshold** (Umbral de eliminación accidental): escriba el número de objetos que, de ser eliminados, harán que se detenga la sincronización y se envíe una notificación.

![Eliminaciones accidentales](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Recuperación de un caso de eliminación accidental
Si encuentra una eliminación accidental, la verá en el estado de la configuración del agente de aprovisionamiento.  El mensaje será el siguiente: **Umbral de eliminaciones superado**.
 
![Estado de la eliminación accidental](media/how-to-accidental-deletes/delete-1.png)

Al hacer clic en **Umbral de eliminaciones superado**, verá la información del estado de la sincronización.  Este proporcionará detalles adicionales. 
 
 ![Estado de sincronización](media/how-to-accidental-deletes/delete-2.png)

Al hacer clic con el botón derecho en los puntos suspensivos, obtendrá las siguientes opciones:
 - View provisioning log (Ver el registro de aprovisionamiento)
 - Ver el agente
 - Permitir eliminaciones

 ![Hacer clic con el botón derecho](media/how-to-accidental-deletes/delete-3.png)

Mediante **View provisioning log** (Ver el registro de aprovisionamiento), puede ver las entradas de **StagedDelete** y revisar la información proporcionada sobre los usuarios que se ha eliminado.
 
 ![Registros de aprovisionamiento](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Permitir eliminaciones

La acción para **Permitir eliminaciones** eliminará los objetos que desencadenó el umbral de eliminaciones accidentales.  Use el siguiente procedimiento para aceptar las eliminaciones.  

1. Haga clic con el botón derecho en los puntos suspensivos y seleccione **Permitir eliminaciones**.
2. Haga clic en **Sí** en la confirmación para permitir la eliminación.
 
 ![Sí, después de la confirmación](media/how-to-accidental-deletes/delete-4.png)

3. Verá una confirmación de que las eliminaciones se aceptaron y el estado volverá a ser correcto en el siguiente ciclo. 
 
 ![Aceptar eliminaciones](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Rechazar eliminaciones

Si no desea permitir las eliminaciones, debe hacer lo siguiente:
- Investigar el origen de las eliminaciones
- Corregir el problema (por ejemplo, la unidad organizativa se había sacado del ámbito accidentalmente y ya se ha vuelto a agregar)
- Ejecutar **Restart sync** (Reiniciar sincronización) en la configuración del agente

## <a name="next-steps"></a>Pasos siguientes 

- [Solución de problemas de sincronización en la nube de Azure AD Connect](how-to-troubleshoot.md)
- [Códigos de error sobre la sincronización en la nube de Azure AD Connect](reference-error-codes.md)
 

