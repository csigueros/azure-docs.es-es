---
title: Libro de informes de operaciones confidenciales en Azure AD | Microsoft Docs
description: Descubra sobre cómo usar el libro de informes de operaciones confidenciales.
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
ms.openlocfilehash: a44aa07539be94aa645c95a6cee544bf92468bd7
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990525"
---
# <a name="sensitive-operations-report-workbook"></a>Libro de informes de operaciones confidenciales

Como administrador de TI, debe ser capaz de identificar los riesgos en su entorno para asegurarse de que puede mantenerlo en buen estado. 

El libro de informes de operaciones confidenciales está pensado para ayudar a identificar la actividad sospechosa en las aplicaciones y entidades de servicio que podría indicar riesgos para su entorno.


En este artículo se proporciona información general sobre este libro.


## <a name="description"></a>Descripción

![Categoría del libro](./media/workbook-sensitive-operations-report/workbook-category.png)

Este libro identifica las operaciones confidenciales recientes que se han realizado en el inquilino y que pueden poner en peligro la entidad de servicio.

Si su organización no está familiarizada con los libros de Azure Monitor, debe integrar los registros de inicio de sesión y auditoría de Azure AD con Azure Monitor antes de acceder al libro. Esto le permite almacenar, consultar y visualizar los registros mediante libros durante dos años como máximo. Solo se almacenarán los eventos de inicio de sesión y auditoría creados después de la integración de Azure Monitor, por lo que el libro no contendrá información detallada antes de esa fecha. Conozca mejor los requisitos previos de los libros de Azure Monitor en Azure Active Directory. Si ha integrado previamente los registros de inicio de sesión y auditoría de Azure AD con Azure Monitor, puede usar el libro para evaluar la información pasada. 
 
 

## <a name="sections"></a>Secciones

Este libro se divide en cuatro secciones:

![Secciones del libro](./media/workbook-sensitive-operations-report/workbook-sections.png)


- **Modificación de métodos de autenticación y credenciales de aplicaciones y entidades de servicio**: este informe marca los actores que han cambiado recientemente muchas credenciales de entidad de servicio, así como el número de credenciales de cada tipo de entidad de servicio que han cambiado.

- **Concesión de nuevos permisos a las entidades de servicio**: este libro también resalta los permisos de OAuth 2.0 concedidos recientemente a las entidades de servicio. 

- **Actualizaciones de pertenencia a grupos y roles de directorio para entidades de servicio**



- **Modificación de la configuración de federación**: este informe subraya cuándo un usuario o una aplicación modifica la configuración de federación de un dominio. Por ejemplo, informa cuando un nuevo objeto TrustedRealm de Servicios de federación de Active Directory (ADFS), como un certificado de firma, se agrega al dominio. La modificación de la configuración de federación de dominios no es algo que sea muy frecuente. 




### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>Modificación de métodos de autenticación y credenciales de aplicaciones y entidades de servicio

Una de las formas más comunes de que los atacantes obtengan persistencia en el entorno es agregar nuevas credenciales a las aplicaciones y entidades de servicio existentes. Las credenciales permiten al atacante autenticarse con la aplicación o la entidad de servicio de destino, de forma que se le concede acceso a todos los recursos para los que tiene permisos.

En esta sección se incluyen los datos siguientes para ayudarle a detectar:

- Todas las credenciales nuevas agregadas a aplicaciones y entidades de servicio, incluido el tipo de credencial.

- Actores principales y la cantidad de modificaciones de credenciales que realizaron.

- Escala de tiempo de todos los cambios de credenciales.



### <a name="new-permissions-granted-to-service-principals"></a>Concesión de nuevos permisos a las entidades de servicio

En los casos en los que el atacante no puede encontrar una entidad de servicio o una aplicación con un conjunto de permisos con privilegios elevados a través de los cuales obtener acceso, a menudo intentará agregar los permisos a otra entidad de servicio o aplicación.

En esta sección se incluye un desglose de las concesiones de permisos de AppOnly a las entidades de servicio existentes. Los administradores deben investigar los casos de permisos elevados excesivos que se conceden, incluidos, entre otros, los de Exchange Online, Microsoft Graph y Azure AD Graph.


### <a name="directory-role-and-group-membership-updates-for-service-principals"></a>Actualizaciones de pertenencia a grupos y roles de directorio para entidades de servicio 

Siguiendo la lógica del atacante de agregar nuevos permisos a las entidades de servicio y las aplicaciones existentes, otra estrategia es agregarlos a los roles o grupos de directorio existentes.

En esta sección se incluye información general de todos los cambios realizados en las pertenencias a entidades de servicio y se deben revisar por si se han producido adiciones a roles y grupos con privilegios elevados.



### <a name="modified-federation-settings"></a>Modificación de la configuración de federación

Otra estrategia común para obtener un punto de apoyo a largo plazo en el entorno es:

- Modificar las confianzas de dominios federados del inquilino.
- Agregar un IDP de SAML adicional controlado por el atacante como origen de autenticación de confianza. 

En esta sección se incluyen los datos siguientes:

- Cambios realizados en las confianzas de federación de dominio existentes

- Adición de nuevos dominios y confianzas


  


## <a name="filters"></a>Filtros

En este párrafo se enumeran los filtros admitidos de cada sección.


### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>Modificación de métodos de autenticación y credenciales de aplicaciones y entidades de servicio

- Intervalo de horas
- Nombre de operación
- Credential:
- Actor
- Excluir actor


### <a name="new-permissions-granted-to-service-principals"></a>Concesión de nuevos permisos a las entidades de servicio

- Intervalo de horas
- Aplicación cliente
- Recurso

### <a name="directory-role-and-group-membership-updates-to-service-principals"></a>Actualizaciones de pertenencia a grupos y roles de directorio para entidades de servicio

- Intervalo de horas
- Operación
- Inicio de un usuario o una aplicación

### <a name="modified-federation-settings"></a>Modificación de la configuración de federación

- Intervalo de horas
- Operación
- Inicio de un usuario o una aplicación




## <a name="best-practices"></a>Procedimientos recomendados


**Use:**
 
- **Modificación de credenciales de aplicaciones y entidades de servicio** para buscar credenciales que se agregan a las entidades de servicio que no se usan con frecuencia en su organización. Use los filtros presentes en esta sección para investigar más a fondo alguno de los actores sospechosos o entidades de servicio que se modificaron.


- **Concesión de nuevos permisos a las entidades de servicio** para buscar permisos amplios o excesivos que se agregan a las entidades de servicio por parte de actores que pueden estar en peligro.  

- **Modificación de la configuración de federación** para confirmar que el dominio o la dirección URL de destino agregados o modificados proceden del comportamiento de un administrador legítimo. Las acciones que modifican o agregan confianzas de federación de dominio son poco frecuentes y deben tratarse con la máxima fidelidad para que se investiguen lo antes posible.





## <a name="next-steps"></a>Pasos siguientes

- [Uso de libros de Azure AD](howto-use-azure-monitor-workbooks.md)
