---
title: API de Horizon
description: En esta guía se describen los métodos de Horizon de uso común.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018529"
---
# <a name="horizon-api"></a>API de Horizon 

En esta guía se describen los métodos de Horizon de uso común.

### <a name="getting-more-information"></a>Más información

Para obtener más información sobre el uso de Horizon y la plataforma Defender para IoT, consulte la siguiente información:

- En el caso del SDK de Horizon Open Development Environment (ODE), póngase en contacto con el representante de Defender para IoT.
- Para obtener información de soporte técnico y solución de problemas, póngase en contacto con <support@cyberx-labs.com>.

- Para acceder a la guía de usuario de Defender para IoT desde la consola de Defender para IoT, seleccione :::image type="icon" source="media/references-horizon-api/profile.png"::: y luego **Download User Guide** (Descargar guía de usuario).


## `horizon::protocol::BaseParser`

Resumen de todos los complementos. Consta de dos métodos:

- Para el procesamiento de filtros de complementos definidos en un nivel superior. De este modo, Horizon sabe cómo comunicarse con el analizador.
- Para el procesamiento de los datos en sí.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

La primera función a la que se llama en el complemento crea una instancia del analizador para que Horizon la reconozca y la registre.

### <a name="parameters"></a>Parámetros 

Ninguno.

### <a name="return-value"></a>Valor devuelto

shared_ptr a la instancia del analizador.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Se llama a esta función en cada complemento registrado en un nivel superior. 

En la mayoría de los casos estará vacía. Inicie una excepción para que Horizon sepa que se ha producido un error.

### <a name="parameters"></a>Parámetros 

- Mapa que contiene la estructura de dissect_as, tal como se ha definido en el archivo config.json de otro complemento que se quiere registrar en un nivel superior.

### <a name="return-value"></a>Valor devuelto 

Matriz de uint64_t, que es el registro procesado en un tipo de uint64_t. Esto significa que, en el mapa, hay una lista de puertos cuyos valores son de uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Función principal. En concreto, la lógica del complemento cada vez que un nuevo paquete llega al analizador. Se llama a esta función, todo lo relacionado con el procesamiento de paquetes debe hacerse aquí.

### <a name="considerations"></a>Consideraciones

El complemento debe ser seguro para subprocesos, ya que se puede llamar a esta función desde diferentes subprocesos. Un buen enfoque sería definir todo en la pila.

### <a name="parameters"></a>Parámetros

- Unidad de control del SDK responsable del almacenamiento de los datos y la creación de objetos relacionados con el SDK, como ILayer y los campos.
- Asistente para leer los datos del paquete sin formato. Ya está establecido con el orden de bytes definido en config.json.

### <a name="return-value"></a>Valor devuelto 

Resultado del procesamiento. Puede ser *Success* (correcto), *Malformed* (con formato incorrecto) o *Sanity* (comprobación).

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Marca el procesamiento como error de comprobación, lo que significa que el paquete no ha sido reconocido por el protocolo actual y Horizon debe pasarlo a otro analizador, si hay alguno registrado en los mismos filtros.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Constructor

### <a name="parameters"></a>Parámetros 

- Define el código de error usado por Horizon para el registro, tal como se ha definido en config.json.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Resultado con formato incorrecto, indica que el paquete ya se ha reconocido como protocolo, pero que se ha producido algún error en la validación (bits reservados activos o falta algún campo).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Constructor

### <a name="parameters"></a>Parámetros  

- Código de error, tal como se ha definido en config.json.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Notifica a Horizon que el procesamiento ha sido correcto. Si es correcto, el paquete se ha aceptado, los datos pertenecen al usuario y se han extraído todos.

## `horizon::protocol::SuccessResult()`

Constructor. Se ha creado un resultado básico correcto. Esto significa que no se conoce la dirección ni ningún otro metadato relacionado con el paquete.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Constructor.

### <a name="parameters"></a>Parámetros 

- Dirección del paquete, si se ha identificado. Los valores pueden ser *REQUEST* o *RESPONSE*.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Constructor.

### <a name="parameters"></a>Parámetros

- Dirección del paquete, si se ha identificado; puede ser *REQUEST* o *RESPONSE*.
- Advertencias. No se producen errores en estos eventos, pero se notifica a Horizon.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Constructor.

### <a name="parameters"></a>Parámetros 

-  Advertencias. No se producen errores en estos eventos, pero se notifica a Horizon.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Convierte una referencia basada en cadena en un nombre de campo (por ejemplo, function_code) para HorizonID.

### <a name="parameters"></a>Parámetros 

- Cadena que se va a convertir.

### <a name="return-value"></a>Valor devuelto

- HorizonID creado a partir de la cadena.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Crea una nueva capa para que Horizon sepa que el complemento quiere almacenar algunos datos. Esta es la unidad de almacenamiento base que se debe usar.

### <a name="return-value"></a>Valor devuelto

Referencia a una capa creada para que se le puedan agregar datos.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Obtiene el objeto de administración de campos, que es responsable de crear campos en diferentes objetos, por ejemplo, en ILayer.

### <a name="return-value"></a>Valor devuelto

Referencia al administrador.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Crea un nuevo campo numérico de 64 bits en la capa con el id. solicitado.

### <a name="parameters"></a>Parámetros 

- Capa creada anteriormente.
- HorizonID creado por la macro **HORIZON_FIELD**.
- Valor sin formato que se quiere almacenar.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Crea un nuevo campo de cadena en la capa con el id. solicitado. La memoria se mueve, así que tenga cuidado. No va a poder volver a usar este valor.

### <a name="parameters"></a>Parámetros  

- Capa creada anteriormente.
- HorizonID creado por la macro **HORIZON_FIELD**.
- Valor sin formato que se quiere almacenar.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Crea un nuevo campo de valor sin formato (matriz de bytes) en la capa con el id. solicitado. La memoria se mueve, así que tenga cuidado, no podrá usar de nuevo este valor.

### <a name="parameters"></a>Parámetros

- Capa creada anteriormente.
- HorizonID creado por la macro **HORIZON_FIELD**.
- Valor sin formato que se quiere almacenar.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Crea un campo de valor de matriz (matriz) en la capa del tipo especificado con el id. solicitado.

### <a name="parameters"></a>Parámetros

- Capa creada anteriormente.
- HorizonID creado por la macro **HORIZON_FIELD**.
- Tipo de valores que se van a almacenar dentro de la matriz.

### <a name="return-value"></a>Valor devuelto

Referencia a una matriz a la que se deben anexar valores.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Anexa un nuevo valor entero a la matriz creada anteriormente.

### <a name="parameters"></a>Parámetros

- Matriz creada anteriormente.
- Valor sin formato que se va a almacenar en la matriz.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Anexa un nuevo valor de cadena a la matriz creada anteriormente. La memoria se mueve, así que tenga cuidado, no podrá usar de nuevo este valor.

### <a name="parameters"></a>Parámetros

- Matriz creada anteriormente.
- Valor sin formato que se va a almacenar en la matriz.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Anexa un nuevo valor sin formato a la matriz creada anteriormente. La memoria se mueve, así que tenga cuidado, no podrá usar de nuevo este valor.

### <a name="parameters"></a>Parámetros

- Matriz creada anteriormente.
- Valor sin formato que se va a almacenar en la matriz.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Comprueba que el búfer contenga al menos X bytes.

### <a name="parameters"></a>Parámetros

Número de bytes que deben existir.

### <a name="return-value"></a>Valor devuelto

True si el búfer contiene al menos X bytes. De lo contrario, es `False`.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Lee un valor uint8 (1 byte) del búfer, según el orden de bytes.

### <a name="return-value"></a>Valor devuelto

Valor leído del búfer.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Lee un valor uint16 (2 bytes) del búfer, según el orden de bytes.

### <a name="return-value"></a>Valor devuelto

Valor leído del búfer.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Lee un valor uint32 (4 bytes) del búfer, según el orden de bytes.

### <a name="return-value"></a>Valor devuelto

Valor leído del búfer.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Lee un valor uint64 (8 bytes) del búfer, según el orden de bytes.

### <a name="return-value"></a>Valor devuelto

Valor leído del búfer.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Lee en la memoria asignada previamente, de un tamaño especificado, copia realmente los datos en la región de la memoria.

### <a name="parameters"></a>Parámetros 

- Región de la memoria en la que se van a copiar los datos.
- Tamaño de la región de la memoria, este parámetro también define el número de bytes que se van a copiar.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Lee en una cadena del búfer.

### <a name="parameters"></a>Parámetros 

- Número de bytes que se deben leer.

### <a name="return-value"></a>Valor devuelto

Referencia a la región de la memoria de la cadena.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Indica el número de bytes que quedan en el búfer.

### <a name="return-value"></a>Valor devuelto

Tamaño restante del búfer.

## `void horizon::general::IDataBuffer::skip(size_t)`

Omite X bytes del búfer.

### <a name="parameters"></a>Parámetros

- Número de bytes que se van a omitir.
