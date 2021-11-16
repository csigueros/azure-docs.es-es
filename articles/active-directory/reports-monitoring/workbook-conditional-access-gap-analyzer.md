---
title: Libro del analizador de brechas en el acceso condicional en Azure AD | Microsoft Docs
description: Aprenda a usar el libro del analizador de brechas en el acceso condicional.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd62dbd1fc279e2ed26472b1c0a0464b56be92c0
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990532"
---
# <a name="conditional-access-gap-analyzer-workbook"></a>Libro del analizador de brechas en el acceso condicional

En Azure AD, el acceso a los recursos se puede proteger mediante la configuración de directivas de acceso condicional.
Los administradores de TI quieren tener la certeza de que las directivas de acceso condicional funcionan según lo previsto para asegurarse de que los recursos están protegidos correctamente. Con el libro del analizador de brechas en el acceso condicional, puede detectar brechas en la implementación del acceso condicional.  

En este artículo se proporciona información general sobre este libro.


## <a name="description"></a>Descripción

![Categoría del libro](./media/workbook-conditional-access-gap-analyzer/workbook-category.png)

Los administradores de TI quieren tener la certeza de que solo puedan acceder a los recursos las personas que deben hacerlo. El acceso de Azure AD sirve de ayuda para lograr este objetivo.  

El libro del analizador de brechas en el acceso condicional ayuda a comprobar que las directivas de acceso condicional funcionan según lo previsto.

**En este libro:**

- Se resaltan los inicios de sesión de usuario a los que no se ha aplicado ninguna directiva de acceso condicional. 
- Permite asegurarse de que no haya usuarios, aplicaciones ni ubicaciones que se hayan excluido involuntariamente de las directivas de acceso condicional.  

 

## <a name="sections"></a>Secciones


El libro tiene cuatro secciones:  

- Usuarios que inician sesión mediante la autenticación heredada 

- Número de inicios de sesión por aplicaciones que no se ven afectados por las directivas de acceso condicional 

- Eventos de inicio de sesión de alto riesgo que omiten las directivas de acceso condicional 

- Número de inicios de sesión por ubicación que no han resultado afectados por las directivas de acceso condicional 


![Cobertura del acceso condicional por ubicación](./media/workbook-conditional-access-gap-analyzer/conditianal-access-by-location.png)

Cada una de estas tendencias ofrece un desglose de los inicios de sesión a nivel de usuario, con el fin de que pueda ver qué usuarios por escenario omiten el acceso condicional. 

## <a name="filters"></a>Filtros

Este libro admite la configuración de un filtro de intervalo de tiempo.

![Filtro de intervalo de tiempo](./media/workbook-conditional-access-gap-analyzer/time-range.png)



## <a name="best-practices"></a>Procedimientos recomendados

Use libro para asegurarse de que el inquilino está configurado según los siguientes procedimientos recomendados de acceso condicional:  

- Bloquear todos los inicios de sesión con autenticación heredada 

- Aplicar al menos una directiva de acceso condicional a cada aplicación 

- Bloquear todos los inicios de sesión de alto riesgo  

- Bloquear los inicios de sesión desde ubicaciones que no son de confianza  

 





## <a name="next-steps"></a>Pasos siguientes

- [Uso de libros de Azure AD](howto-use-azure-monitor-workbooks.md)
