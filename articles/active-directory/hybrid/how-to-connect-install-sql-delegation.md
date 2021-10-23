---
title: Instalación de Azure AD Connect mediante permisos de administrador delegados de SQL | Microsoft Docs
description: En este tema se describe una actualización a Azure AD Connect que permite su instalación con una cuenta que solo tiene permisos de propietario de la base de datos (dbo) de SQL.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adb0f9bc887802cfdf51041e2c2b6c3be757c85
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163511"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalación de Azure AD Connect mediante permisos de administrador delegados de SQL
Antes de la última compilación de Azure AD Connect, no se admitía la delegación administrativa al implementar configuraciones que requerían SQL.  Los usuarios que deseaban instalar Azure AD Connect necesitaban tener permisos de administrador del servidor (SA) en el servidor SQL.

Con la versión más reciente de Azure AD Connect, el administrador de SQL puede realizar ahora el aprovisionamiento de la base de datos de manera externa y luego el administrador de Azure AD Connect puede instalarlo con derechos de propietario de la base de datos.

## <a name="before-you-begin"></a>Antes de empezar
Para usar esta característica, debe darse cuenta de que hay varias piezas móviles y cada una de ellas puede tener que ver con un administrador diferente de la organización.  En la tabla siguiente se resumen los roles individuales y sus funciones respectivas en la implementación de Azure AD Connect con esta característica.

|Role|Descripción|
|-----|-----|
|Administrador de dominio o bosque de AD|Crea la cuenta de servicio en el nivel de dominio que se usa en Azure AD Connect para ejecutar el servicio de sincronización.  Para más información sobre las cuentas de servicio, consulte [Cuentas y permisos](reference-connect-accounts-permissions.md).
|Administrador de SQL|Crea la base de datos ADSync y concede acceso de inicio de sesión + dbo al administrador de Azure AD Connect y a la cuenta de servicio creada por el administrador de dominio o bosque.|
Administrador de Azure AD Connect|Instala Azure AD Connect y especifica la cuenta de servicio durante la instalación personalizada.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Pasos para instalar Azure AD Connect con permisos delegados de SQL
Para aprovisionar la base de datos de forma externa e instalar Azure AD Connect con permisos de propietario de base de datos, siga estos pasos.

>[!NOTE]
>Aunque no es necesario, es **muy recomendable** seleccionar la intercalación de Latin1_General_CI_AS al crear la base de datos.


1. Solicite al administrador de SQL que cree la base de datos ADSync con una secuencia de intercalación sin distinción entre mayúsculas y minúsculas **(Latin1_General_CI_AS)**.  El modelo de recuperación, el nivel de compatibilidad y el tipo de contención se actualizan con los valores correctos cuando se instala Azure AD Connect.  Sin embargo, el administrador de SQL debe establecer correctamente la secuencia de intercalación o, de lo contrario, Azure AD Connect bloqueará la instalación.  Para recuperar el permiso SA, debe eliminar y volver a crear la base de datos.
 
   ![Intercalación](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Conceda los siguientes permisos al administrador de Azure AD Connect y a la cuenta de servicio de dominio:
   - Inicio de sesión de SQL 
   - Derechos de **database owner(dbo)** (propietario de la base de datos).
 
   ![Permisos](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect no admite inicios de sesión con una pertenencia anidada.  Esto significa que su cuenta de administrador de Azure AD Connect y la cuenta de servicio de dominio deben vincularse a un inicio de sesión al que se conceden derechos dbo.  Simplemente no puede ser miembro de un grupo asignado a un inicio de sesión con derechos dbo.

3. Envíe un correo electrónico al administrador de Azure AD Connect que indique el nombre del servidor y de la instancia de SQL que se debe usar al instalar Azure AD Connect.

## <a name="additional-information"></a>Información adicional
Cuando se aprovisiona la base de datos, el administrador de Azure AD Connect puede instalar y configurar la sincronización local cuando lo consideren oportuno.

En caso de que el administrador de SQL haya restaurado la base de datos ADSync a partir de una copia de seguridad de Azure AD Connect anterior, tendrá que instalar el nuevo servidor de Azure AD Connect mediante una base de datos existente. Para más información sobre cómo instalar Azure AD Connect con una base de datos existente, consulte [Instalación de Azure AD Connect mediante una base de datos existente ADSync](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Pasos siguientes
- [Introducción a Azure AD Connect mediante la configuración rápida](how-to-connect-install-express.md)
- [Instalación personalizada de Azure AD Connect](how-to-connect-install-custom.md)
- [Instalación de Azure AD Connect mediante una base de datos existente de ADSync](how-to-connect-install-existing-database.md)  
