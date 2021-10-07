---
title: 'Guía de solución de problemas: Azure DNS'
description: En esta ruta de aprendizaje, obtendrá información sobre la solución de problemas comunes con Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: e4621b73c8b71ba3bb4b42801de5e306cfa3562e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573112"
---
# <a name="azure-dns-troubleshooting-guide"></a>Guía de solución de problemas de Azure DNS

En este artículo se proporciona información para solucionar problemas para preguntas que se plantean con frecuencia sobre Azure DNS.

Si estos pasos no resuelven el problema, también puede buscar o publicar su incidencia en nuestra [página de preguntas y respuestas de Microsoft Q&A para obtener soporte técnico de la comunidad](/answers/topics/azure-virtual-network.html). O bien, puede abrir una solicitud de soporte técnico de Azure.


## <a name="i-cant-create-a-dns-zone"></a>No puedo crear una zona DNS

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes:

1.  Revise los registros de auditoría de Azure DNS para determinar el motivo del error.
2.  Cada nombre de zona DNS debe ser único dentro de su grupo de recursos. Es decir, dos zonas DNS con el mismo nombre no pueden compartir un grupo de recursos. Intente usar otro nombre de zona u otro grupo de recursos.
3.  Es posible que vea el mensaje "Se alcanzó o superó el número máximo de zonas en la suscripción {ID de la suscripción}". Use otra suscripción de Azure, elimine algunas zonas o póngase en contacto con el soporte técnico de Azure para aumentar el límite de su suscripción.
4.  Es posible que vea el mensaje "La zona '{nombre de la zona}' no está disponible". Este error significa que Azure DNS no pudo asignar servidores de nombres para esta zona DNS. Pruebe a usar otro nombre de zona. O bien, si es el propietario del nombre de dominio, puede ponerse en contacto con el soporte técnico de Azure para que le asignen servidores de nombres.


### <a name="recommended-articles"></a>Artículos recomendados

* [Registros y zonas DNS](dns-zones-records.md)
* [Creación de una zona DNS](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>No puedo crear un registro de DNS

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes:

1.  Revise los registros de auditoría de Azure DNS para determinar el motivo del error.
2.  ¿El conjunto de registros ya existe?  El DNS de Azure administra los registros mediante los *conjuntos* de registros, que son una colección de registros con el mismo nombre y el mismo tipo. Si ya existe un registro con el mismo nombre y tipo, para agregar otro registro de dicho tipo debe editar el conjunto de registros existente.
3.  ¿Está intentando crear un registro en el vértice de la zona DNS (la "raíz" de la zona)? Si es así, la convención de DNS es usar el carácter ‘@’ como nombre del registro. Tenga en cuenta también que los estándares de DNS no permiten registros CNAME en el vértice de la zona.
4.  ¿Tiene un conflicto de CNAME?  Los estándares de DNS no permiten un registro CNAME con el mismo nombre que un registro de cualquier otro tipo. Si tiene un CNAME existente, la creación de un registro con el mismo nombre, pero de un tipo diferente genera un error.  Del mismo modo, la creación de un CNAME genera un error si el nombre coincide con un registro existente de otro tipo. Elimine el conflicto quitando el otro registro o eligiendo otro nombre de registro.
5.  ¿Ha alcanzado el límite en el número de conjuntos de registros permitido en una zona DNS? En Azure Portal se muestra el número actual y el número máximo de conjuntos de registros, en las propiedades de la zona. Si ha alcanzado este límite, elimine algunos conjuntos de registros o póngase en contacto con el soporte técnico de Azure para aumentar el límite de conjuntos de registros para esta zona y vuelva a intentarlo. 


### <a name="recommended-articles"></a>Artículos recomendados

* [Registros y zonas DNS](dns-zones-records.md)
* [Creación de una zona DNS](./dns-getstarted-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>No puedo resolver mi registro de DNS

La resolución de nombres de DNS es un proceso de varios pasos que puede generar un error por diversos motivos. Los pasos siguientes ayudan a investigar por qué se producen errores en la resolución de DNS para un registro de DNS en una zona hospedada en Azure DNS.

1.  Confirme que los registros de DNS se hayan configurado correctamente en el DNS de Azure. Revise los registros de DNS en Azure Portal y compruebe que el nombre de la zona, el nombre del registro y el tipo de registro son correctos.
2.  Confirme que los registros de DNS se resuelven correctamente en los servidores de nombres de DNS de Azure.
    - Si realiza consultas de DNS del equipo local, puede ver los resultados almacenados en caché que no reflejen el estado actual de los servidores de nombres.  Además, las redes corporativas suelen usan servidores proxy de DNS que impiden que las consultas de DNS se dirijan a servidores de nombres específicos.  Para evitar estos problemas, utilice un servicio de resolución de nombres basado en web, como [digwebinterface](https://digwebinterface.com).
    - Asegúrese de especificar los servidores de nombres correctos para la zona DNS, que se muestran en Azure Portal.
    - Compruebe que el nombre de DNS (tiene que especificar el nombre completo, incluido el nombre de zona) y el tipo de registro son correctos
3.  Confirme que el nombre de dominio del DNS se ha [delegado correctamente a los servidores de nombres de DNS de Azure](dns-domain-delegation.md). Hay un [muchos sitios web de terceros que ofrecen la validación de la delegación de DNS](https://www.bing.com/search?q=dns+check+tool). Se trata de una prueba de delegación de *zona*, por lo que solo se debe escribir el nombre de la zona DNS, no el nombre de registro completo.
4.  Una vez completado lo anterior, el registro de DNS ahora debe resolverse correctamente. Para comprobarlo, puede volver a usar [digwebinterface](https://digwebinterface.com), pero esta vez con la configuración predeterminada del servidor de nombres.


### <a name="recommended-articles"></a>Artículos recomendados

* [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md)


## <a name="unhealthy-dns-zones"></a>Zonas DNS con estado incorrecto

Los errores de configuración pueden hacer que las zonas DNS no tengan un estado correcto. Estos son los escenarios que pueden dar lugar a este comportamiento:

* **Delegación incorrecta**: una zona contiene registros de delegación *NS* que le permiten delegar el tráfico de la zona principal a la secundaria. Si alguno de los registros de *NS* está presente en la zona primaria, se supone que el servidor DNS enmascara otros registros de la delegación, excepto los registros de adherencia. Sin embargo, si la zona contiene otros registros en la delegación, la zona se marcará como incorrecta.

    En la tabla siguiente se proporcionan escenarios y sus resultados de mantenimiento de zona correspondientes, cuando una zona contiene el registro de delegación de NS.

    | Escenario | ¿La zona contiene</br>un registro de delegación de NS? | ¿La zona contiene</br>registros adheridos? | ¿La zona contiene otros</br>registros en la</br>delegación? | Mantenimiento de la zona |
    |----------|-------------------------------------|-----------------------------|--------------------------------------------------|-------------|
    | 1        | No                                  | -                           | -                                                | Healthy     |
    | 2        | Sí                                 | Sí                         | No                                               | Healthy     |
    | 3        | Sí                                 | No                          | No                                               | Healthy     |
    | 4        | Sí                                 | No                          | Sí                                              | Unhealthy (Incorrecto)   |
    | 5        | Sí                                 | Sí                         | Sí                                              | Unhealthy (Incorrecto)   |

    **Recomendación:** elimine todos los registros excepto los registros de adherencia que estén en los registros de delegación en sus zonas.

* **TTL cero**: TTL (tiempo de vida) es una configuración que indica al solucionador de DNS cuánto tiempo debe almacenar en caché una consulta antes de solicitar una nueva. Una vez hecho esto, la información recopilada se almacena en la memoria caché del solucionador recursivo o local durante el TLL, antes de que vuelva a recopilar detalles nuevos y actualizados.

    Si el TTL se establece en 0 en la configuración, puede experimentar uno de los siguientes problemas:

    * Respuesta larga.
    * Aumento del tráfico y el costo de DNS.
    * Propensión a ataques DDoS.

    **Recomendación**: asegúrese de que el valor TTL no esté establecido en *0*. 

## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>¿Cómo puedo especificar el "servicio" y el "protocolo" para un registro SRV?

El DNS de Azure administra los registros de DNS como conjuntos de registros: la colección de registros con el mismo nombre y el mismo tipo. Para un conjunto de registros SRV, el "servicio" y el "protocolo" deben especificarse como parte del nombre del conjunto de registros. Los otros parámetros SRV (la "prioridad", el "peso", el "puerto" y el "destino") se especifican por separado para cada registro del conjunto.

Ejemplo de nombres de registros SRV (nombre de servicio "sip", protocolo "tcp"):

- \_sip.\_tcp (crea un conjunto de registros en el vértice de la zona)
- \_sip.\_tcp.sipservice (crea un conjunto de registros con el nombre "sipservice")

### <a name="recommended-articles"></a>Artículos recomendados

* [Registros y zonas DNS](dns-zones-records.md)
* [Creación de registros y conjuntos de registros de DNS mediante Azure Portal](./dns-getstarted-portal.md)
* [Tipo de registro SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Registros y zonas DNS](dns-zones-records.md)
* Para empezar a usar DNS de Azure, vea cómo [crear una zona DNS](./dns-getstarted-portal.md) y [crear registros DNS](./dns-getstarted-portal.md).
* Para migrar una zona DNS existente, vea cómo [importar y exportar un archivo de zona DNS](dns-import-export.md).
