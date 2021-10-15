---
title: Solución de errores del proveedor de recursos de Azure NetApp Files | Microsoft Docs
description: Se describen las causas, soluciones y soluciones alternativas para los errores comunes del proveedor de recursos de Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: 2a3e97096109ce89f481b2abca2dc7b5a3a237e6
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407881"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Solución de errores del proveedor de recursos de Azure NetApp Files 

En este artículo se describen los errores comunes del proveedor de recursos de Azure NetApp Files, así como sus causas, soluciones y soluciones alternativas (si las hay).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Errores comunes del proveedor de recursos de Azure NetApp Files

La ***creación de `netAppAccounts` se ha restringido en esta región.***

Esta situación se produce cuando el usuario intenta crear una cuenta de NetApp.

* Causa:   
El proveedor de recursos de Azure para Azure NetApp Files no se ha registrado correctamente. 
 
* Solución:   
Complete todos los pasos descritos en [Registro del proveedor de recursos de NetApp](azure-netapp-files-register.md).

***No se puede cambiar BareMetalTenantId.***  

Este error se produce cuando intenta actualizar un volumen o aplicarle una revisión y la propiedad `BaremetalTenantId` tiene un valor cambiado.

* Causa:   
Intenta actualizar un volumen y la propiedad `BaremetalTenantId` tiene un valor diferente del valor almacenado en Azure.
* Solución:   
No incluya `BaremetalTenantId` en la solicitud de revisión y actualización (put). Como alternativa, asegúrese de que `BaremetalTenantId` sea igual en la solicitud.

***No se puede cambiar ServiceLevel.***  

Este error se produce al intentar actualizar un grupo de capacidad o aplicarle una revisión con un nivel de servicio diferente cuando el grupo de capacidad ya contiene volúmenes.

* Causa:   
Intenta actualizar un nivel de servicio del grupo de capacidad cuando el grupo contiene volúmenes.
* Solución:   
Elimine todos los volúmenes del grupo de capacidad y, luego, cambie el nivel de servicio.
* Solución:   
Cree otro grupo de capacidad y, luego, vuelva a crear los volúmenes en el nuevo grupo de capacidad.

***No se puede cambiar PoolId***  

Este error se produce al intentar actualizar un grupo de capacidad o aplicarle una revisión con una propiedad `PoolId` modificada.

* Causa:   
Intenta actualizar una propiedad `PoolId` del grupo de capacidad. La propiedad `PoolId` es una propiedad de solo lectura y no se puede cambiar.
* Solución:   
No incluya `PoolId` en la solicitud de revisión y actualización (put).  Como alternativa, asegúrese de que `PoolId` sea igual en la solicitud.

***No se puede cambiar CreationToken.***

Este error se produce al intentar cambiar la ruta del archivo (`CreationToken`) después de que el volumen se ha creado. La ruta del archivo (`CreationToken`) se debe establecer cuando se crea el volumen y no se puede cambiar más adelante.

* Causa:   
Intenta cambiar la ruta del archivo (`CreationToken`) después de la creación del volumen, que no es una operación admitida. 
* Solución:   
Si no es necesario cambiar la ruta del archivo, considere la posibilidad de quitar el parámetro de la solicitud para descartar el mensaje de error.
* Solución:   
Si necesita cambiar la ruta del archivo (`CreationToken`), puede crear un volumen con una nueva ruta de archivo y luego migrar los datos al nuevo volumen.

***CreationToken debe tener una longitud de al menos 16 caracteres.***

Este error se produce cuando la ruta del archivo (`CreationToken`) no cumple el requisito de longitud. La longitud de la ruta del archivo debe tener una longitud mínima de un carácter.

* Causa:   
La ruta del archivo está vacía.  Cuando se crea un volumen mediante la API, se requiere un token de creación. Si usa Azure Portal, la ruta del archivo se genera automáticamente.
* Solución:   
Escriba al menos un carácter como ruta del archivo (`CreationToken`).

***No se puede cambiar el nombre de dominio***.

Este error se produce al intentar cambiar el nombre de dominio en Active Directory.

* Causa:   
Intenta actualizar la propiedad de nombre de dominio.
* Solución:    
Ninguno. No se puede cambiar el nombre de dominio.
* Solución:   
Elimine todos los volúmenes mediante la configuración de Active Directory. A continuación, elimine la configuración de Active Directory y vuelva a crear los volúmenes.

***Error de valor duplicado del objeto ExportPolicy.Rules[RuleIndex].***

Este error se produce cuando la directiva de exportación no está definida con un índice único. Al definir directivas de exportación, todas las reglas de directiva de exportación deben tener un índice único entre 1 y 5.

* Causa:   
La directiva de exportación definida no cumple el requisito de reglas de directivas de exportación. Debe tener como mínimo una regla de directiva de exportación y como máximo cinco reglas de directiva de exportación.
* Solución:   
Asegúrese de que el índice no esté en uso y que se encuentre en el rango de 1 a 5.
* Solución:   
Use un índice diferente para la regla que está intentando establecer.

***Error {action} {resourceTypeName}***

Este error se muestra cuando otro control de errores no ha podido gestionar el error al realizar una acción en un recurso.   Incluye el texto "Error". El elemento `{action}` puede ser (`getting`, `creating`, `updating` o `deleting`).  `{resourceTypeName}` es el valor de `resourceTypeName` (por ejemplo, `netAppAccount`, `capacityPool`, `volume`, etc.).

* Causa:   
Este error es una excepción no controlada de la que se desconoce la causa.
* Solución:   
Póngase en contacto con el Centro de soporte técnico de Azure para informar del motivo detallado en los registros.
* Solución:   
Ninguno.

***El nombre de la ruta del archivo solo puede contener letras, números y guiones ("-").***

Este error se produce cuando la ruta del archivo contiene caracteres no admitidos, por ejemplo, un punto ("."), una coma (","), un carácter de subrayado ("_") o un signo de dólar ("$").

* Causa:   
La ruta del archivo contiene caracteres no admitidos, por ejemplo, un punto ("."), una coma (","), un carácter de subrayado ("_") o un signo de dólar ("$").
* Solución:   
Quite los caracteres que no son letras alfabéticas, números o guiones ("-") de la ruta de acceso de archivo especificado.
* Solución:   
Puede reemplazar un carácter de subrayado por un guion o usar mayúsculas y minúsculas en lugar de espacios para indicar el comienzo de nuevas palabras.  Por ejemplo, use "NuevoVolumen" en lugar de "nuevo volumen".

***No se puede cambiar FileSystemId.***

Este error se produce al intentar cambiar `FileSystemId`.  El cambio de `FileSystemdId` no es una operación admitida. 

* Causa:   
El id. del sistema de archivos se establece en el momento de creación del volumen. Posteriormente, `FileSystemId` no se puede cambiar.
* Solución:   
No incluya `FileSystemId` en una solicitud de revisión y actualización (put).  Como alternativa, asegúrese de que `FileSystemId` sea igual en la solicitud.

***ActiveDirectory con id.: "{string}" no existe.***

La parte `{string}` es el valor especificado en la propiedad `ActiveDirectoryId` de la conexión de Active Directory.

* Causa:   
Cuando creó una cuenta con la configuración de Active Directory, especificó un valor para `ActiveDirectoryId` que se supone que está vacío.
* Solución:   
No incluya `ActiveDirectoryId` en la solicitud de creación (put).

***Versión de API no válida.***

La versión de API no se ha enviado o contiene un valor no válido.

* Causa:   
El valor del parámetro de consulta `api-version` contiene un valor no válido.
* Solución:   
Use el valor correcto de la versión de API.  El proveedor de recursos admite muchas versiones de API. El valor está en el formato aaaa-mm-dd.

***Se recibió un valor no válido "{value}" de {1}.***

Este mensaje indica un error en los campos de `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv3` y `Nfsv4`.

* Causa:   
Hubo un error en la solicitud de validación de entrada para al menos uno de los siguientes campos: `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv`3 y `Nfsv4`.
* Solución:   
Asegúrese de establecer todos los parámetros obligatorios y no conflictivos en la línea de comandos. Por ejemplo, no se pueden establecer los parámetros `UnixReadOnly` y `UnixReadWrite` al mismo tiempo.
* Solución:   
Vea la solución anterior.

El ***intervalo IP {0} a {1} de VLAN {2} ya está en uso***

Este error se produce porque los registros internos de los intervalos IP usados tienen un conflicto con la dirección IP recién asignada.

* Causa:   
La dirección IP asignada para la creación del volumen ya está registrada.
La razón puede ser una creación anterior de un volumen con error.
* Solución:   
Póngase en contacto con el Centro de soporte técnico de Azure.

***Falta un valor para "{property}".***

Este error indica que falta una propiedad necesaria de la solicitud. La cadena {property} contiene el nombre de la propiedad que falta.

* Causa:   
Hubo un error en la solicitud de validación de entrada para al menos una de las propiedades.
* Solución:   
Asegúrese de establecer todas las propiedades requeridas y no conflictivas en la solicitud, especialmente la propiedad del mensaje de error.

***No se puede cambiar MountTargets.***

Este error se produce cuando un usuario intenta actualizar la propiedad MountTargets del volumen o aplicarle una revisión.

* Causa:   
Intenta actualizar la propiedad `MountTargets` del volumen. No se puede cambiar esta propiedad.
* Solución:   
No incluya `MountTargets` en una solicitud de revisión y actualización (put).  Como alternativa, asegúrese de que `MountTargets` sea igual en la solicitud.

***El nombre ya está en uso.***

Este error indica que el nombre del recurso ya está en uso.

* Causa:   
Intenta crear un recurso con un nombre que se usa en un recurso existente.
* Solución:   
Use un nombre único al crear el recurso.

***La ruta del archivo ya está en uso.***

Este error indica que la ruta del archivo del volumen ya está en uso.

* Causa:   
Intenta crear un volumen con una ruta de archivo igual a la del volumen existente.
* Solución:   
Use una ruta de archivo única al crear el volumen.

***Nombre demasiado largo.***

Este error indica que el nombre del recurso no cumple el requisito de longitud mínima.

* Causa:   
El nombre del recurso es demasiado largo.
* Solución:   
Use un nombre más corto para el recurso.

***Ruta del archivo demasiado larga.***

Este error indica que la ruta del archivo del volumen no cumple el requisito de longitud máxima.

* Causa:   
La ruta del archivo del volumen es demasiado larga.
* Solución:   
Use una más corta.

***Nombre demasiado corto.***

Este error indica que el nombre del recurso no cumple el requisito de longitud mínima.

* Causa:   
El nombre del recurso es demasiado corto.
* Solución:   
Use un nombre más largo para el recurso.

***Ruta del archivo demasiado corta.***

Este error indica que la ruta del archivo del volumen no cumple el requisito de longitud mínima.

* Causa:   
La ruta del archivo del volumen es demasiado corta.
* Solución:   
Aumente su longitud.

***API de Azure NetApp Files inaccesible.***

La API de Azure depende de la API de Azure NetApp Files para administrar volúmenes. Este error indica un problema con la conexión de la API.

* Causa:   
La API subyacente no responde, lo que resulta en un error interno. Es probable que el error sea temporal.
* Solución:   
Es probable que el problema sea temporal. La solicitud debería realizarse correctamente después de un tiempo.
* Solución:   
Ninguno. La API subyacente es esencial para la administración de volúmenes.

***No se encontró ningún id. de resultado de operación para "{0}".***

Este error indica que un error interno impide la finalización de la operación.

* Causa:   
Hubo un error interno que impidió la finalización de la operación.
* Solución:   
Es probable que el error sea temporal. Espere unos minutos y pruebe otra vez. Si el problema persiste, cree una incidencia para que el soporte técnico investigue el problema.
* Solución:   
Espere unos minutos y compruebe si el problema persiste.

***No se permite la combinación de los tipos de protocolo CIFS y NFS***

Este error se produce al intentar crear un volumen y existen los tipos de protocolo CIFS (SMB) y NFS en las propiedades del volumen.

* Causa:   
Los tipos de protocolo CIFS (SMB) y NFS se usan en las propiedades del volumen.
* Solución:   
Quite uno de los tipos de protocolo.
* Solución:   
Deje la propiedad de tipo de protocolo vacía o con un valor null.

***El número de elementos: {value} del objeto: ExportPolicy.Rules[RuleIndex] está fuera del intervalo mínimo-máximo.***

Este error se produce cuando las reglas de directivas de exportación no cumplen el requisito de rango mínimo o máximo. Si define la directiva de exportación, esta debe tener una regla de directiva de exportación como mínimo y cinco reglas de directiva de exportación como máximo.

* Causa:   
La directiva de exportación definida no cumple el rango necesario.
* Solución:   
Asegúrese de que el índice no esté en uso y que se encuentre en el rango de 1 a 5.
* Solución:   
No es obligatorio usar directivas de exportación en los volúmenes. Puede omitir la directiva de exportación por completo si no necesita usar reglas de directiva de exportación.

***Solo se permite una instancia de Active Directory***

Este error se produce al intentar crear una configuración de Active Directory y ya existe una para la suscripción en la región. El error también puede producirse al intentar crear más de una configuración de Active Directory.

* Causa:   
Intenta crear (no actualizar) una instancia de Active Directory, pero ya existe una.
* Solución:   
Si la configuración de Active Directory no está en uso, primero puede eliminar la configuración existente y, luego, volver a intentar la operación de creación.
* Solución:   
Ninguno. Solo se permite una instancia de Active Directory.

***Operación "{operation}" no admitida.***

Este error indica que la operación no está disponible para la suscripción o el recurso activos.

* Causa:   
La operación no está disponible para la suscripción o recurso.
* Solución:   
Asegúrese de que la operación se haya especificado correctamente y que esté disponible para el recurso y la suscripción que esté usando.

***No se puede cambiar OwnerId***

Este error se produce al intentar cambiar la propiedad `OwnerId` del volumen. El cambio de `OwnerId` no es una operación admitida. 

* Causa:   
La propiedad `OwnerId` se establece cuando se crea el volumen. Posteriormente, no se puede cambiar.
* Solución:   
No incluya `OwnerId` en una solicitud de revisión y actualización (put). Como alternativa, asegúrese de que `OwnerId` sea igual en la solicitud.

***No se encontró el grupo principal***

Este error se produce cuando se intenta crear un volumen y no se encuentra el grupo de capacidad en el que se está creando.

* Causa:   
No se encuentra el grupo de capacidad en el que se está creando el volumen.
* Solución:   
Lo más probable es que el grupo no se haya creado totalmente o que ya se haya eliminado en el momento de la creación del volumen.

***No se admite la operación de revisión para este tipo de recurso.***

Este error se produce al intentar cambiar el destino de montaje o instantánea.

* Causa:   
El destino de montaje se define en el momento de su creación y no se puede modificar posteriormente.
Las instantáneas no contienen propiedades que se puedan cambiar.
* Solución:   
Ninguno. Esos recursos no tienen ninguna propiedad que se pueda cambiar.

***El tamaño del grupo es demasiado pequeño para el tamaño total del volumen.***

Este error se produce cuando se actualiza el tamaño del grupo de capacidad y es menor que el valor `usedBytes` total de todos los volúmenes de ese grupo de capacidad.  Este error también puede producirse cuando se crea un volumen o se cambia el tamaño de un volumen existente y el nuevo tamaño del volumen supera el espacio disponible en el grupo de capacidad.

* Causa:   
Intenta actualizar el grupo de capacidad con un tamaño menor que usedBytes en todos los volúmenes del grupo de capacidad.  O bien, intenta crear un volumen que es mayor que el espacio disponible en el grupo de capacidad.  O también, intenta cambiar el tamaño de un volumen y el nuevo tamaño supera el espacio disponible en el grupo de capacidad.
* Solución:   
Establezca el tamaño del grupo de capacidad en un valor mayor o cree un volumen de menor tamaño.
* Solución:   
Quite suficientes volúmenes para que el tamaño del grupo de capacidad pueda actualizarse con este tamaño.

***La propiedad Location de la instantánea debe ser la misma que la del volumen***

Este error se produce cuando se crea una instantánea con una ubicación que no es igual a la del volumen que posee la instantánea.

* Causa:   
La propiedad Location de la instantánea no tiene un valor válido.
* Solución:   
Establezca una cadena válida en la propiedad Location.

***El nombre de {resourceType} debe ser igual que el nombre del identificador de recurso.***

Este error se produce cuando se crea un recurso y se rellena la propiedad Name con un valor que no es igual al de la propiedad Name de `resourceId`.

* Causa:   
Valor no válido en la propiedad Name al crear un recurso.
* Solución:   
Deje la propiedad Name vacía o permita que use el mismo valor que la propiedad Name (entre la última barra diagonal inversa "/" y el signo de interrogación "?") de `resourceId`.

***Tipo de protocolo {valor} desconocido***

Este error se produce cuando se crea un volumen con un tipo de protocolo desconocido.  Los valores válidos son "NFSv3", "NFSv4" y "CIFS".

* Causa:   
Intenta establecer un valor no válido en la propiedad `protocolType` del volumen.
* Solución:   
Establezca una cadena válida en `protocolType`.
* Solución:   
Establezca `protocolType` como null.

***No se pueden cambiar los tipos de protocolo***

Este error se produce al intentar actualizar el valor de `ProtocolType` de un volumen o aplicarle una revisión.  El cambio de ProtocolType no es una operación admitida.

* Causa:   
La propiedad `ProtocolType` se establece cuando se crea el volumen.  No se puede actualizar.
* Solución:   
Ninguno.
* Solución:   
Cree otro volumen con nuevos tipos de protocolo.

***La creación del recurso de tipo {resourceType} ha superado la cuota de recursos de {quota} del tipo {resourceType} por {parentResourceType}. El número actual de recursos es {currentCount}. Elimine algunos recursos de este tipo antes de crear otro.***

Este error se produce al intentar crear un recurso (`NetAppAccount`, `CapacityPool`, `Volume` o `Snapshot`), pero la cuota ha llegado a su límite.

* Causa:   
Intenta crear un recurso, pero se ha alcanzado el límite de cuota (ejemplo: `NetAppAccounts` por suscripción `CapacityPools` por `NetAppAccount`).
* Solución:   
Aumente el límite de cuota.
* Solución:   
Elimine los recursos no usados del mismo tipo y vuelva a crearlos.

***Recibió un valor para la propiedad de solo lectura "{propertyName}".***

Este error se produce cuando se define un valor para una propiedad que no se puede modificar. Por ejemplo, no se puede cambiar el id. del volumen.

* Causa:   
Intenta modificar un parámetro (por ejemplo, el identificador de volumen) que no se puede cambiar.
* Solución:   
No modifique un valor de la propiedad.

***No se encontró {resource} solicitado.***

Este error se produce cuando se intenta hacer referencia a un recurso que no existe, por ejemplo, un volumen o una instantánea. Es posible que el recurso se haya eliminado o tenga un nombre de recurso con errores ortográficos.

* Causa:   
Intenta hacer referencia a un recurso que no existe (por ejemplo, un volumen o una instantánea) que ya se ha eliminado o que tiene el nombre mal escrito.
* Solución:   
Compruebe la solicitud para ver si tiene errores de ortografía y asegurarse de que se hace referencia a ella correctamente.
* Solución:   
Consulte la sección Solución anterior.

***El nivel de servicio "{volumeServiceLevel}" es superior al valor de "{poolServiceLevel}" principal***

Este error se produce cuando se crea o actualiza un volumen y se establece el nivel de servicio en un nivel superior al del grupo de capacidad que lo contiene.

* Causa:   
Intenta crear o actualizar un volumen con un nivel de servicio en una posición más alta que la del grupo de capacidad principal.
* Solución:   
Establezca el nivel de servicio en una posición igual o menor que la del grupo de capacidad principal.
* Solución:   
Cree el volumen en otro grupo de capacidad con un nivel de servicio correcto. Otra opción es eliminar todos los volúmenes del grupo de capacidad y establecer el nivel de servicio del grupo de capacidad en una posición más alta.

***El nombre del servidor SMB no puede tener más de 10 caracteres.***

Este error se produce cuando se crea o actualiza una configuración de Active Directory para una cuenta.

* Causa:   
La longitud del nombre del servidor SMB supera los 10 caracteres.
* Solución:   
Use un nombre de servidor más corto. La longitud máxima es de 10 caracteres.
* Solución:   
Ninguno.  Vea la solución anterior. 

***No se puede cambiar SubnetId.***

Este error se produce al intentar cambiar el valor de `subnetId` después de que se ha creado el volumen.  `SubnetId` se debe establecer cuando se crea el volumen y no se puede cambiar más adelante.

* Causa:   
Intenta cambiar el valor de `subnetId` después de la creación del volumen, y esta operación no se admite. 
* Solución:   
Si no es necesario cambiar el valor de `subnetId`, considere la posibilidad de quitar el parámetro de la solicitud para descartar el mensaje de error.
* Solución:   
Si necesita cambiar el valor de `subnetId`, puede crear un volumen con un nuevo valor de `subnetId` y luego migrar a él los datos.

***SubnetId tiene un formato no válido.***

Este error se produce cuando se intenta crear un volumen pero `subnetId` no es un valor de `resourceId` para una subred.

* Causa:   
Este error se produce cuando se intenta crear un volumen, pero `subnetId` no es un valor de `resourceId` para una subred. 
* Solución:   
Compruebe el valor de `subnetId` para asegurarse de que contiene un elemento `resourceId` para la subred usada.
* Solución:   
Ninguno. Vea la solución anterior. 

***La subred debe tener una delegación "Microsoft. NetApp/Volumes".***

Este error se produce cuando se crea un volumen y la subred seleccionada no se delega en `Microsoft.NetApp/volumes`.

* Causa:   
Ha intentado crear el volumen y ha seleccionado una subred no delegada en `Microsoft.NetApp/volumes`.
* Solución:   
Seleccione otra subred que se delegue en `Microsoft.NetApp/volumes`.
* Solución:   
Agregue una delegación correcta a la subred.

***El tipo de recurso especificado es desconocido o no es aplicable.***

Este error se produce cuando se ha solicitado una comprobación de nombre en un tipo de recurso no aplicable o desconocido.

* Causa:   
Se ha solicitado una comprobación de nombre para un tipo de recurso desconocido o no admitido.
* Solución:   
Compruebe que el recurso para el que realiza la solicitud se admita o no contenga errores ortográficos.
* Solución:   
Vea la solución anterior.

***Error desconocido de Azure NetApp Files.***

La API de Azure depende de la API de Azure NetApp Files para administrar volúmenes. El error indica que hay un problema en la comunicación con la API.

* Causa:   
La API subyacente está enviando un error desconocido. Es probable que el error sea temporal.
* Solución:   
Es probable que el problema sea temporal y la solicitud debería realizarse correctamente después de un tiempo. Si el problema persiste, cree una incidencia de soporte técnico para que el problema se investigue.
* Solución:   
Ninguno. La API subyacente es esencial para la administración de volúmenes.

***Valor recibido para una propiedad desconocida "{propertyName}".***

Este error se produce cuando se proporcionan propiedades no existentes para un recurso, como el volumen, instantánea o destino de montaje.

* Causa:   
La solicitud tiene un conjunto de propiedades que se pueden usar con cada recurso. No se pueden incluir propiedades inexistentes en la solicitud.
* Solución:   
Asegúrese de que todos los nombres de propiedad se hayan escrito correctamente y de que las propiedades estén disponibles para la suscripción y el recurso.
* Solución:   
Reduzca el número de propiedades definidas en la solicitud para eliminar la propiedad que provoca el error.

***No se admite la operación de actualización para este tipo de recurso.***

Solo los volúmenes se pueden actualizar. Este error se produce cuando se intenta realizar una operación de actualización no admitida, por ejemplo, actualizar una instantánea.

* Causa:   
El recurso que intenta actualizar no admite la operación de actualización. Solo los volúmenes permiten la modificación de sus propiedades.
* Solución:   
Ninguno. El recurso que intenta actualizar no admite la operación de actualización. Por lo tanto, no se puede modificar.
* Solución:   
Para un volumen, cree un nuevo recurso con la actualización en contexto y migre los datos.

***No se puede crear el volumen en un grupo con un estado incorrecto.***

Este error se produce cuando se intenta crear un volumen en un grupo que no está en estado correcto. Lo más probable es que la operación de creación no se haya realizado correctamente por algún motivo.

* Causa:   
El grupo de capacidad que contiene el nuevo volumen tiene un estado de error.
* Solución:   
Compruebe que el grupo de capacidad se ha creado correctamente y que no se encuentra en estado de error.
* Solución:   
Cree un grupo de capacidad y cree el volumen en el nuevo grupo.

***El volumen se está creando y no se puede eliminar en este momento.***

Este error se produce cuando se intenta eliminar un volumen que aún está en proceso de creación.

* Causa:   
Intenta eliminar un volumen que aún se encuentra en proceso de creación.
* Solución:   
Espere a que termine de crearse el volumen y, luego, vuelva a intentar la eliminación.
* Solución:   
Vea la solución anterior.

***El volumen se está eliminando y no se puede eliminar en ese momento.***

Este error se produce cuando se intenta eliminar un volumen que ya está en proceso de eliminación.

* Causa:   
Intenta eliminar un volumen, pero ya se encuentra en proceso de eliminación.
* Solución:   
Espere a que finalice la operación de eliminación actual.
* Solución:   
Vea la solución anterior.

***El volumen se está actualizando y no se puede eliminar en ese momento.***

Este error se produce cuando se intenta eliminar un volumen que se está actualizando.

* Causa:   
Intenta eliminar un volumen que está en proceso de actualización.
* Solución:   
Espere a que termine la operación de actualización y vuelva a intentar la eliminación.
* Solución:   
Vea la solución anterior.

***No se encontró el volumen o no se creó correctamente.***

Este error se produce cuando no se ha podido crear el volumen e intenta cambiar el volumen o crear una instantánea para él.

* Causa:   
El volumen no existe o se ha producido un error en la creación.
* Solución:   
Compruebe que cambia el volumen correcto y que la creación del volumen se ha realizado correctamente. O bien, compruebe que exista el volumen para el que se crea una instantánea.
* Solución:   
Ninguno.  Vea la solución anterior. 

***El token de creación especificado ya existe***

Este error se produce cuando se intenta crear un volumen y se especifica un token de creación (ruta de acceso de exportación) para el que ya existe un volumen.

* Causa:   
El token de creación (ruta de acceso de exportación) que especificó durante la creación del volumen ya está asociado a otro volumen. 
* Solución:   
Elija otro token de creación.  También puede eliminar el otro volumen.

***El token de creación especificado está reservado***

Este error se produce cuando se intenta crear un volumen y se especifica "predeterminado" o "ninguno" como la ruta del archivo (token de creación).

* Causa:    
Intenta crear un volumen y especifica "predeterminado" o "ninguno" como ruta del archivo (token de creación).
* Solución:   
Elija una ruta del archivo diferente (token de creación).
 
***Las credenciales de Active Directory están en uso***

Este error se produce cuando se intenta eliminar la configuración de Active Directory de una cuenta en la que todavía existe al menos un volumen SMB.  El volumen SMB se creó con la configuración de Active Directory que intenta eliminar.

* Causa:   
Intenta eliminar la configuración de Active Directory de una cuenta, pero todavía existe al menos un volumen SMB que se creó inicialmente con ella. 
* Solución:   
En primer lugar, elimine todos los volúmenes SMB que se crearon con la configuración de Active Directory.  A continuación, vuelva a intentar la eliminación de la configuración.

***No se puede modificar la asignación de unidades organizativas si las credenciales están en uso***

Este error se produce cuando se intenta cambiar la unidad organizativa de una configuración de Active Directory, pero todavía existe al menos un volumen SMB.  El volumen SMB se creó con esa configuración de Active Directory que intenta eliminar.

* Causa:   
Intenta cambiar la unidad organizativa de una configuración de Active Directory.  Sin embargo, aún existe al menos un volumen SMB que se creó inicialmente con esa configuración.
* Solución:   
 En primer lugar, elimine todos los volúmenes SMB que se crearon con la configuración de Active Directory.  A continuación, vuelva a intentar la eliminación de la configuración. 

***Actualización de Active Directory ya en curso***

Este error se produce cuando se intenta editar una configuración de Active Directory para la que ya hay una operación de edición en curso.

* Causa:   
Intenta editar una configuración de Active Directory, pero ya hay otra operación de edición en curso.
* Solución:   
Espere a que finalice la operación de edición que se ejecuta actualmente.

***Elimine primero todos los volúmenes con las credenciales seleccionadas***

Este error se produce cuando se intenta eliminar una configuración de Active Directory, pero todavía existe al menos un volumen SMB.  El volumen SMB se creó con la configuración de Active Directory que intenta eliminar.

* Causa:   
Intenta eliminar una configuración de Active Directory, pero todavía existe al menos un volumen SMB que se creó inicialmente con esa configuración.
* Solución:   
En primer lugar, elimine todos los volúmenes SMB que se crearon con la configuración de Active Directory.  A continuación, vuelva a intentar la eliminación de la configuración. 

***No se encontraron credenciales de Active Directory en la región***

Este error se produce cuando se intenta crear un volumen SMB, pero no se ha agregado ninguna configuración de Active Directory a la cuenta en la región.

* Causa:   
Intenta crear un volumen SMB, pero no se ha agregado ninguna configuración de Active Directory a la cuenta. 
* Solución:   
Agregue una configuración de Active Directory a la cuenta antes de crear un volumen SMB.

***No se pudo consultar el servidor DNS. Compruebe que la configuración de red es correcta y que los servidores DNS estén disponibles.***

Este error se produce cuando se intenta crear un volumen SMB, pero no se puede acceder a un servidor DNS (especificado en la configuración de Active Directory). 

* Causa:   
Intenta crear un volumen SMB, pero no se puede acceder a un servidor DNS (especificado en la configuración de Active Directory).
* Solución:   
Revise la configuración de Active Directory y asegúrese de que las direcciones IP del servidor DNS sean correctas y estén accesibles.
Si no hay ningún problema con las direcciones IP del servidor DNS, compruebe que ningún firewall bloquea el acceso.

***Demasiados trabajos simultáneos***

Este error se produce cuando se intenta crear una instantánea cuando ya hay en curso otras tres operaciones de creación de instantáneas para la suscripción.

* Causa:   
Intenta crear una instantánea cuando ya hay otras tres operaciones de creación de instantáneas en curso para la suscripción. 
* Solución:   
Los trabajos de creación de instantáneas suelen tardan unos segundos en completarse.  Espere unos segundos y vuelva a intentar la operación de creación de instantáneas.

***No se pueden generar trabajos adicionales. Espere a que finalicen los trabajos en curso e inténtelo de nuevo***.

Este error puede producirse al intentar crear o eliminar un volumen en determinadas circunstancias.

* Causa:   
Intenta crear o eliminar un volumen en determinadas circunstancias.
* Solución:   
Espere un minuto y vuelva a intentar la operación.

***El volumen ya está realizando la transición entre estados***

Este error puede producirse cuando se intenta eliminar un volumen que está actualmente en estado de transición (es decir, se está creando, actualizando o eliminando).

* Causa:   
Intenta eliminar un volumen que está actualmente en estado de transición.
* Solución:   
Espere a que finalice la operación (transición de estado) en marcha y, luego, vuelva a intentar la operación.

***No se pudo dividir el nuevo volumen desde la instantánea del volumen de origen***

 Este error puede producirse al intentar crear un volumen a partir de una instantánea.  

* Causa:   
Intenta crear un volumen a partir de una instantánea y el volumen termina en un estado de error.
* Solución:   
Elimine el volumen y vuelva a intentar la operación de creación del volumen desde la instantánea.

 
## <a name="next-steps"></a>Pasos siguientes

* [Desarrollo para Azure NetApp Files con la API REST](azure-netapp-files-develop-with-rest-api.md)
