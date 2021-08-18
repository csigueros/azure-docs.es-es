---
title: 'Preguntas frecuentes: Azure Monitor para soluciones de SAP | Microsoft Docs'
description: En este artículo, obtendrá respuestas a preguntas frecuentes sobre las soluciones de Azure Monitor para SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/08/2020
ms.author: radeltch
ms.openlocfilehash: dfc28d9ced0ca65b4ebe0e49ade05eb822c59bab
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567639"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Preguntas frecuentes acerca de Azure Monitor para soluciones de SAP (versión preliminar)
## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

En este artículo, se ofrecen respuestas a preguntas frecuentes (P+F) sobre Azure Monitor para soluciones de SAP.  

 - **¿Tengo que pagar por Azure Monitor para soluciones de SAP?**  
No hay ninguna cuota de licencias para Azure Monitor para soluciones de SAP. Sin embargo, es responsables del costo de los componentes del grupo de recursos administrados.  

 - **¿En qué regiones está este servicio disponible para la versión preliminar pública?**  
En el caso de la versión preliminar pública, este servicio estará disponible en el Este de EE. UU. 2, Oeste de EE. UU. 2, Este de EE. UU. y Oeste de Europa.  

 - **¿Es necesario proporcionar permisos para permitir la implementación de un grupo de recursos administrados en mi suscripción?**  
No, no se necesitan permisos explícitos.  

 - **¿Dónde reside la máquina virtual del recopilador?**  
Al implementar Azure Monitor para soluciones de SAP, se recomienda que elija la misma red virtual para el recurso de supervisión que usa el servidor de SAP HANA. Por lo tanto, se recomienda que la máquina virtual del recopilador esté en la misma red virtual que el servidor de SAP HANA. Si usa una base de datos que no es HANA, la máquina virtual del recopilador residirá en la misma red virtual que dicha base de datos.  

 - **¿Qué versiones de HANA se admiten?**  
Se admiten HANA 1.0 SPS 12 (Rev. 120 o superior) y HANA 2.0 SPS03 o superior. 

 - **¿Qué configuraciones de implementación de HANA se admiten?**  
Se admiten las siguientes configuraciones:
   - Nodo único (escalado vertical) y varios nodos (escalado horizontal).  
   - Contenedor de una sola base de datos (HANA 1.0 SPS 12) y varios contenedores de base de datos (HANA 1.0 SPS 12 o HANA 2.0).
   - Conmutación por error de host automática (n+1) y HSR.  

 - **¿Qué versiones de SQL Server se admiten?**  
SQL Server 2012 SP4 o posterior.  

 - **¿Qué configuraciones de SQL Server se admiten?**  
Se admiten las siguientes configuraciones:
   - Instancias independientes predeterminadas o con nombre en una máquina virtual.  
   - Instancias o instancias en clúster de una configuración Always On cuando se usa el nombre virtual del recurso en clúster o el nombre del cliente de escucha AlwaysOn.
   - Actualmente, no se recopilan métricas específicas del clúster ni de AlwaysOn.    
   - Actualmente, no se admite Azure SQL Database (PAAS).  

 - **¿Qué ocurre si elimino accidentalmente el grupo de recursos administrados?**  
El grupo de recursos administrados está bloqueado de forma predeterminada. Por lo tanto, las posibilidades de eliminar accidentalmente el grupo de recursos administrados son minúsculas. Si elimina el grupo de recursos administrados, Azure Monitor para soluciones de SAP dejará de funcionar. Tendrá que implementar un nuevo recurso de Azure Monitor para soluciones de SAP y volver a empezar.  

 - **¿Qué roles necesito en mi suscripción de Azure para implementar el recurso de Azure Monitor para soluciones de SAP?**  
Rol de colaborador.  

 - **¿Cuál es el SLA de este producto?**  
Las versiones preliminares están excluidas de los contratos de nivel de servicio. Lea los términos completos de la licencia mediante la imagen de Marketplace de Azure Monitor para soluciones de SAP.  

 - **¿Puedo supervisar todo el panorama desde esta solución?**  
En la versión preliminar pública, actualmente puede supervisar:
- Base de datos HANA
- La infraestructura subyacente
- El clúster de alta disponibilidad
- Microsoft SQL Server
- Disponibilidad de SAP Netweaver
- Métricas de disponibilidad de la instancia de la aplicación de SAP

 - **¿Este servicio reemplaza a SAP Solution Manager?**  
No. Los clientes pueden seguir usando SAP Solution Manager para la supervisión de procesos empresariales.  

 - **¿Cuál es el valor de este servicio respecto a las soluciones tradicionales, como SAP HANA Cockpit/Studio?**  
Azure Monitor para soluciones de SAP no es específico para bases de datos HANA. Azure Monitor para soluciones de SAP también admite AnyDB.  

- **¿Qué versiones de SAP NetWeaver son compatibles?**  
SAP NetWeaver 7.0, o cualquier versión superior.  

- **¿Qué configuraciones de SAP NetWeaver son compatibles?**  
Admite ABAP, Java y configuraciones de servidor de aplicaciones de SAP NetWeaver de pila doble.

- **¿Por qué hay que desproteger métodos para la supervisión de aplicaciones de SAP NetWeaver?**  
Tanto en SAP 7.3 como en las versiones posteriores, la mayoría de los métodos de servicio web están protegidos de forma predeterminada. Para capturar las métricas de disponibilidad y rendimiento mediante la realización de llamadas a estos métodos, deberá desproteger los métodos siguientes: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic y GetSystemInstancelist.

- **¿Existe algún riesgo si se desprotegen los métodos web de SAPCONTROL?**  
En general, puede desproteger los métodos web de SAPCONTROL sin plantear un riesgo de seguridad como [tal](https://launchpad.support.sap.com/#/notes/1439348). Puede restringir el acceso a métodos web sin protección mediante los puertos de servidor 5XX13 y 5XX14 de sapstartsrv. Para ello, agregue un filtro en la lista de control de acceso de SAP. La [nota de OSS](https://service.sap.com/sap/support/notes/1495075) describe la configuración necesaria. 

- **¿Es necesario reiniciar las instancias de SAP después de realizar configuraciones del sistema para configurar el proveedor de SAP NetWeaver?**  
Sí, una vez que tenga métodos desprotegidos mediante cambios de configuración de SAP, deberá reiniciar los sistemas de SAP correspondientes para asegurarse de que se actualicen los cambios de configuración.  

## <a name="next-steps"></a>Pasos siguientes

Más información sobre Azure Monitor para soluciones de SAP.

> [!div class="nextstepaction"]
> [Azure Monitor para soluciones de SAP](azure-monitor-overview.md)
