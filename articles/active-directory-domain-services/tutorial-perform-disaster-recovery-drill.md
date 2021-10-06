---
title: 'Tutorial: Realización de un ejercicio de recuperación ante desastres en Azure AD Domain Services | Microsoft Docs'
description: Aprenda a realizar un ejercicio de recuperación ante desastres mediante conjuntos de réplicas en Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 09/22/2021
ms.author: justinha
ms.openlocfilehash: 1816c4615e96a1e88c0a76d2defcb2c6d6686650
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702074"
---
# <a name="tutorial-perform-a-disaster-recovery-drill-using-replica-sets-in-azure-active-directory-domain-services"></a>Tutorial: Realización de un ejercicio de recuperación ante desastres mediante conjuntos de réplicas en Azure Active Directory Domain Services

En este tema se muestra cómo realizar un ejercicio de recuperación ante desastres en Azure AD Domain Services (Azure AD DS) mediante conjuntos de réplicas.  Durante el mismo se simulará que uno de los conjuntos de réplicas se desconecta realizando cambios en las propiedades de red virtual de red para bloquear el acceso del cliente a él.  No es un verdadero simulacro de recuperación ante desastres ya que el conjunto de réplicas no se desconectará. 

El ejercicio de recuperación ante desastres abarcará lo siguiente: 

1. Conexión de una máquina cliente a un conjunto de réplicas determinado. Puede autenticarse en el dominio y realizar consultas LDAP.
1. Se finalizará la conexión del cliente al conjunto de réplicas. Esto ocurrirá mediante la restricción del acceso a la red.
1. A continuación, el cliente establecerá una nueva conexión con el otro conjunto de réplicas. Una vez que esto suceda, el cliente podrá autenticarse en el dominio y realizar consultas LDAP. 
1. El miembro del dominio se reiniciará y un usuario del dominio podrá iniciar sesión después del reinicio.
1. Se eliminarán las restricciones de red y el cliente podrá conectarse al conjunto de réplicas original. 

## <a name="prerequisites"></a>Requisitos previos 

Deben cumplirse los siguientes requisitos para completar el ejercicio de recuperación ante desastres: 

- Una instancia de Azure AD DS activa implementada con al menos una réplica adicional en funcionamiento. El dominio debe tener un estado correcto. 
- Una máquina cliente que se una al dominio hospedado de Azure AD DS.  El cliente debe estar en su propia red virtual, el emparejamiento de red virtual con ambas redes virtuales del conjunto de réplicas debe estar habilitado, y la red virtual debe tener las direcciones IP de todos los controladores de dominio de los conjuntos de réplicas enumerados en DNS. 

## <a name="environment-validation"></a>Validación del entorno 

1. Inicie sesión en la máquina cliente con una cuenta de dominio. 
1. Instale las herramientas de Active Directory Domain Services RSAT. 
1. Inicie una ventana de PowerShell con privilegios elevados.
1. Realice las comprobaciones básicas de validación de dominios: 
   - Ejecute `nslookup [domain]` para asegurarse de que la resolución DNS funciona correctamente. 
   - Ejecute `nltest /dsgetdc:` para que devuelva un estado correcto y diga qué controlador de dominio se está utilizando actualmente.
   - Ejecute `nltest /dclist:` para devolver la lista completa de controladores de dominio del directorio. 
1. Realice la validación básica de controladores de dominio en cada controlador de dominio del directorio (puede obtener la lista completa en la salida de "nltest /dclist:"): 
   - Ejecute `nltest /sc_reset:[domain name]\[domain controller name]` para establecer una conexión segura con el controlador de dominio. 
   - Ejecute `Get-AdDomain` para recuperar la configuración básica del directorio. 

## <a name="perform-the-disaster-recovery-drill"></a>Realización del ejercicio de recuperación ante desastres 

Realizará estas operaciones para cada conjunto de réplicas de la instancia de Azure AD DS. Esto simulará una interrupción para cada conjunto de réplicas. Cuando no se puede acceder a los controladores de dominio, el cliente conmutará por error automáticamente a un controlador de dominio accesible y esta experiencia debe ser fluida para el usuario final o la carga de trabajo. Por lo tanto, es fundamental que las aplicaciones y servicios no apunten a un controlador de dominio específico. 

1. Identifique los controladores de dominio del conjunto de réplicas del que desea simular la desconexión. 
1. En la máquina cliente, conéctese a uno de los controladores de dominio mediante `nltest /sc_reset:[domain]\[domain controller name]`. 
1. En Azure Portal, vaya al emparejamiento de red virtual del cliente y actualice las propiedades para que todo el tráfico entre el cliente y el conjunto de réplicas esté bloqueado. 
   1. Seleccione la red emparejada que desea actualizar. 
   1. Seleccione esta opción para bloquear todo el tráfico de red que entra o sale de la red virtual. 
      ![Captura de pantalla de cómo bloquear el tráfico en Azure Portal](./media/tutorial-perform-disaster-recovery-drill/block-traffic.png)
1. En la máquina cliente, intente restablecer una conexión segura con los controladores de dominio del paso 2 mediante el mismo comando nltest. Estas operaciones deben producir un error ya que se ha bloqueado la conectividad de red. 
1. Ejecute `Get-AdDomain` y `Get-AdForest` para obtener las propiedades básicas del directorio. Estas llamadas se realizarán correctamente porque van automáticamente a uno de los controladores de dominio del otro conjunto de réplicas. 
1. Reinicie el cliente e inicie sesión con la misma cuenta de dominio. Esto muestra que la autenticación sigue funcionando según lo previsto y que los inicios de sesión no se bloquean. 
1. En Azure Portal, vaya al emparejamiento de red virtual del cliente y actualice las propiedades para que todo el tráfico se desbloquee. Esto revierte los cambios realizados en el paso 3. 
1. En la máquina cliente, intente restablecer una conexión segura con los controladores de dominio del paso 2 mediante el mismo comando nltest. Estas operaciones se realizarán correctamente ya que se ha desbloqueado la conectividad de red. 

Estas operaciones muestran que el dominio sigue estando disponible aunque el cliente no pueda acceder a uno de los conjuntos de réplicas. Realice este conjunto de pasos para cada conjunto de réplicas de la instancia de Azure AD DS. 

## <a name="summary"></a>Resumen 

Después de completar estos pasos, verá que los miembros del dominio siguen teniendo acceso al directorio si uno de los conjuntos de réplicas de Azure AD DS no es accesible. Puede simular el mismo comportamiento bloqueando todo el acceso de red para un conjunto de réplicas en lugar de una máquina cliente, pero no es recomendable. No cambiará el comportamiento desde la perspectiva del cliente, pero afectará al estado de la instancia de Azure AD DS hasta que se restaure el acceso a la red. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Validar la conectividad del cliente a los controladores de dominio de un conjunto de réplicas
> * Bloquear el tráfico de red entre el cliente y el conjunto de réplicas
> * Validar la conectividad del cliente a los controladores de dominio de otro conjunto de réplicas

Para más información conceptual, aprenda cómo funcionan los conjuntos de réplicas en Azure AD DS.

> [!div class="nextstepaction"]
> [Conceptos y características de los conjuntos de réplicas][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
