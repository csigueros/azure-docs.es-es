---
title: Administración de la consola de administración local
description: Obtenga información sobre las opciones de la consola de administración local, como la copia de seguridad y la restauración, la definición del nombre de host y la configuración de un proxy en sensores.
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: b1853b8c22e576bade68823ae206a48c443eb231
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019785"
---
# <a name="manage-the-on-premises-management-console"></a>Administración de la consola de administración local

En este artículo se explican las opciones de la consola de administración local, como la copia de seguridad y la restauración, la descarga del archivo de activación de dispositivos confirmados, la actualización de certificados y la configuración de un proxy en sensores.

Se incorpora la consola de administración local de Azure Portal.

## <a name="upload-an-activation-file"></a>Carga de un archivo de activación

La primera vez que inicia sesión, se descarga un archivo de activación de la consola de administración local. Este archivo contiene los dispositivos agregados confirmados que se definen durante el proceso de incorporación. La lista incluye sensores asociados a varias suscripciones.

Después de la activación inicial, el número de dispositivos supervisados puede superar el número de dispositivos confirmados definidos durante la incorporación. Este evento puede ocurrir, por ejemplo, si conecta más sensores a la consola de administración. Si hay una discrepancia entre el número de dispositivos supervisados y el número de dispositivos confirmados, aparece una advertencia en la consola de administración. Si se produce este evento, debe cargar un nuevo archivo de activación.

Para cargar un archivo de activación:

1. Vaya a la página **Precios** de Azure Defender para IoT.
1. Seleccione la pestaña **Download the activation file for the management console** (Descargar el archivo de activación de la consola de administración). Se descarga el archivo de activación.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Descargue el archivo de activación.":::

1. Seleccione **Configuración del sistema** en la consola de administración.
1. Seleccione **Activación**.
1. Seleccione **Elegir un archivo** y luego el archivo guardado.

## <a name="manage-certificates"></a>Administración de certificados

Después de la instalación de la consola de administración local, se genera un certificado autofirmado local que se usa para acceder a la aplicación web de la consola de administración. Cuando los usuarios administradores inician sesión en la consola de administración por primera vez, se les solicita que proporcionen un certificado SSL/TLS. Para obtener más información acerca de la configuración por primera vez, consulte [Activación y configuración la consola de administración local](how-to-activate-and-set-up-your-on-premises-management-console.md).

En las secciones siguientes se proporciona información sobre la actualización de certificados, el trabajo con comandos de la CLI de certificados y los certificados y parámetros de certificado admitidos.

### <a name="about-certificates"></a>Acerca de los certificados

Azure Defender para IoT usa certificados SSL y TLS para:

- Cumplir con los requisitos de cifrado y certificado específicos solicitados de la organización, cargando el certificado firmado por la entidad de certificación.

- Permitir la validación entre la consola de administración y los sensores conectados, y entre una consola de administración y una consola de administración de alta disponibilidad. La validación se evalúa con una lista de revocación de certificados y la fecha de expiración del certificado. *Si se produce un error en la validación, la comunicación entre la consola de administración y el sensor se detiene y aparece un error de validación en la consola*. Esta opción está habilitada de forma predeterminada después de la instalación.

   Cuando la validación es `ON`, el dispositivo debe ser capaz de establecer la conexión con el servidor CRL definido por el certificado.

No se validan las reglas de reenvío de terceros. Algunos ejemplos son la información de alertas enviada a SYSLOG, Splunk o ServiceNow; y la comunicación con Active Directory.

#### <a name="ssl-certificates"></a>Certificados SSL

El sensor de Defender para IoT y la consola de administración local usan SSL y certificados TLS para las siguientes funciones: 

 - Comunicaciones seguras entre los usuarios y la consola web del dispositivo. 
 
 - Comunicaciones seguras con la API REST en el sensor y en la consola de administración local.
 
 - Comunicaciones seguras entre los sensores y la consola de administración local. 

Una vez instalado, el dispositivo genera un certificado autofirmado local para permitir el acceso preliminar a la consola web. El SSL de la empresa y los certificados TLS se pueden instalar con la [`cyberx-xsense-certificate-import`](#cli-commands) herramienta de línea de comandos. 

 > [!NOTE]
 > En el caso de las integraciones y las reglas de reenvío en las que el dispositivo es el cliente y el iniciador de la sesión, se usan certificados específicos y no están relacionados con los certificados del sistema.  
 >
 >En estos casos, los certificados se reciben normalmente del servidor o utilizan el cifrado asimétrico, donde se proporcionará un certificado específico para configurar la integración. 

### <a name="update-certificates"></a>Actualización de certificados

Los usuarios administradores de la consola de administración local pueden actualizar los certificados.

Para actualizar un certificado:  

1. Seleccione **Configuración del sistema**.

1. Seleccione **SSL/TLS Certificates** (Certificados SSL/TLS).
1. Elimine o edite el certificado y agregue uno nuevo.
   
   - Agregue un nombre de certificado.
   
   - Cargue un archivo CRT y un archivo de clave. Escriba una frase de contraseña.
   - Cargue un archivo PEM si es necesario.

Para cambiar la configuración de validación:

1. Active o desactive la opción **Enable Certificate Validation** (Habilitar validación de certificados).

1. Seleccione **Guardar**.

Si la opción está habilitada y se produce un error en la validación, la comunicación entre la consola de administración y el sensor se detiene y aparece un error de validación en la consola.

### <a name="certificate-support"></a>Compatibilidad de certificados

Se admiten los siguientes certificados:

- Infraestructura de clave empresarial y privada (PKI privada)
 
- Infraestructura de clave pública (PKI pública) 

- Generado localmente en el dispositivo (con autofirma local) 

  > [!IMPORTANT]
  > No se recomienda usar certificados autofirmados. Este tipo de conexión no es segura y debe usarse solo para entornos de prueba. Dado que no se puede validar el propietario del certificado y no se puede mantener la seguridad del sistema, los certificados autofirmados nunca deben usarse para las redes de producción.

### <a name="supported-ssl-certificates"></a>Certificados SSL admitidos 

Se admiten los siguientes parámetros: 

**Certificado CRT**

- El archivo de certificado principal para su nombre de dominio

- Algoritmo de firma = SHA256RSA
- Algoritmo hash de firma = SHA256
- Válido desde = fecha pasada válida
- Válido hasta = Fecha futura válida
- Clave pública = RSA de 2048 bits (mínimo) o 4096 bits
- Punto de distribución de CRL = Dirección URL para el archivo .crl
- CN del firmante = Dirección URL, puede ser un certificado comodín; por ejemplo, Sensor.contoso.<span>com o *.contoso.<span>com
- (P)aís del firmante = Definido, por ejemplo, EE. UU.
- Unidad organizativa (UO) del firmante = Definida; por ejemplo, Contoso Labs
- (O)rganización del firmante = Definida; por ejemplo, Contoso Inc

**Archivo de claves**

- El archivo de claves generado al crear el CSR

- RSA de 2048 bits (mínimo) o 4096 bits

 > [!Note]
 > Uso de una longitud de clave de 4096 bits:
 > - El protocolo de enlace SSL al inicio de cada conexión será más lento.  
 > - Hay un aumento en el uso de CPU durante los protocolos de enlace. 

**Cadena de certificados**

- El archivo de certificados intermedios (si existe) proporcionado por la entidad de certificación.

- El certificado de entidad de certificación que emitió el certificado del servidor debe ser el primero en el archivo, seguido de cualquier otro hasta la raíz. 
- La cadena puede incluir atributos de contenedor.

**Frase de contraseña**

- Se admite una clave.

- Se configura al importar el certificado.

Los certificados con otros parámetros podrían funcionar, pero Microsoft no los admite.

#### <a name="encryption-key-artifacts"></a>Artefactos de clave de cifrado

**.pem: archivo de contenedor de certificados**

Los archivos de correo con privacidad mejorada (PEM) eran el tipo de archivo general usado para proteger los mensajes de correo electrónico. En la actualidad, los archivos PEM se utilizan con certificados y usan claves x509 ASN1.  

El archivo de contenedor se define en RFC 1421 a 1424, un formato de contenedor que puede incluir solo el certificado público. Por ejemplo, instalaciones de Apache, un certificado de entidad de certificación, archivos, ETC., SSL o certificados. Puede incluir una cadena de certificados completa, incluida una clave pública, una clave privada y certificados raíz.  

También puede codificar un CSR como el formato PKCS10, que se puede traducir a PEM.

**.cert .cer .crt: archivo de contenedor de certificados**

Un archivo de formato `.pem` o `.der` con una extensión diferente. El Explorador de Windows lo reconoce como certificado. Sin embargo, el Explorador de Windows no reconoce el archivo `.pem` .

**.key: archivo de claves privadas**

Un archivo de claves tiene el mismo formato que un archivo PEM, pero con una extensión diferente. 

#### <a name="other-commonly-available-key-artifacts"></a>Otros artefactos de clave disponibles habitualmente

**.csr: solicitud de firma de certificado**.  

Este archivo se utiliza para enviar a las entidades de certificación. El formato real es PKCS10, que se define en RFC 2986 y puede incluir algunos o todos los detalles clave del certificado solicitado. Por ejemplo, asunto, organización y estado. Es la clave pública del certificado que la CA firma y recibe un certificado de vuelta.  

El certificado devuelto es el certificado público, que incluye la clave pública, pero no la clave privada. 

**.pkcs12 .pfx .p12 : contenedor de contraseñas**. 

Definido originalmente por RSA en los estándares de criptografía de clave pública (PKCS), Microsoft ha mejorado la variante 12 y, posteriormente, se ha enviado como RFC 7292.  

Este formato de contenedor requiere una contraseña que contenga pares de certificados públicos y privados. A diferencia de los archivos `.pem` , este contenedor está totalmente cifrado.  

Puede usar OpenSSL para convertir el archivo en un `.pem` archivo con claves públicas y privadas: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**.der : archivo PEM codificado binario**.

La forma de codificar la sintaxis ASN.1 en binario es a través de un archivo de `.pem` , que es simplemente un archivo `.der` codificado en Base64. 

OpenSSL puede convertir estos archivos en un `.pem`: `openssl x509 -inform der -in to-convert.der -out converted.pem`.  

Windows reconocerá estos archivos como archivos de certificado. De manera predeterminada, Windows exportará los certificados como archivos con formato `.der` con una extensión diferente.

**.crl : lista de revocación de certificados**.  

Las entidades de certificación las producen como una forma de anular la autorización de los certificados antes de su expiración. 

#### <a name="cli-commands"></a>Comandos de la CLI

Use el comando de la CLI `cyberx-xsense-certificate-import` para importar certificados. Para usar esta herramienta, debe cargar los archivos de certificado en el dispositivo con herramientas como WinSCP o Wget.

El comando admite las marcas de entrada siguientes:

- `-h`:  muestra la sintaxis de la ayuda de la línea de comandos.

- `--crt`:  ruta de acceso a un archivo de certificado (extensión .crt).

- `--key`: archivo \*.key. La longitud de la clave debe ser de 2048 bits como mínimo.

- `--chain`:  ruta de acceso a un archivo de cadena de certificados (opcional).

- `--pass`:  frase de contraseña usada para cifrar el certificado (opcional).

- `--passphrase-set`:  valor predeterminado = `False`, sin usar. Se establece en `True` para usar la frase de contraseña anterior, suministrada con el certificado anterior (opcional).

Cuando use el comando de la CLI:

- Compruebe que los archivos de certificado se puedan leer en el dispositivo.

- Compruebe que el nombre de dominio y la dirección IP del certificado coinciden con la configuración que el departamento de TI ha planeado.

### <a name="use-openssl-to-manage-certificates"></a>Uso de OpenSSL para la administración de certificados

Administre los certificados con los siguientes comandos:

| Descripción | Comando de la CLI |
|--|--|
| Generar una nueva clave privada y una solicitud de firma de certificado | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Generar un certificado autofirmado | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Generar una solicitud de firma de certificado (CSR) para una clave privada existente | `openssl req -out CSR.csr -key privateKey.key -new` |
| Generar una solicitud de firma de certificado basada en un certificado existente | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Quitar una frase de contraseña de una clave privada | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Si necesita comprobar la información de un certificado, CSR o clave privada, use estos comandos:

| Descripción | Comando de la CLI |
|--|--|
| Comprobar una solicitud de firma de certificado (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Comprobar una clave privada | `openssl rsa -in privateKey.key -check` |
| Comprobar un certificado | `openssl x509 -in certificate.crt -text -noout`  |

Si recibe un error que le informa de que la clave privada no coincide con el certificado, o que un certificado instalado en un sitio no es de confianza, use estos comandos para corregir el error:

| Descripción | Comando de la CLI |
|--|--|
| Comprobar un hash MD5 de la clave pública para asegurarse de que coincide con lo que se encuentra en un CSR o clave privada | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Para convertir certificados y claves en diferentes formatos para que sean compatibles con determinados tipos de servidores o software, use estos comandos:

| Descripción | Comando de la CLI |
|--|--|
| Convertir un archivo DER (.crt .cer .der) a PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Convertir un archivo PEM a DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Convertir un archivo PKCS#12 (.pfx .p12) que contiene una clave privada y certificados a PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Puede agregar `-nocerts` para que solo se genere la clave privada, o bien agregar `-nokeys` para generar solo los certificados. |
| Convertir un archivo de certificado PEM y una clave privada en PKCS#12 (.pfx .p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>Definición de la configuración de copia seguridad y restauración

La copia de seguridad del sistema de la consola de administración local se realiza de forma automática y diaria. Los datos se guardan en un disco diferente. La ubicación predeterminada es `/var/cyberx/backups`. 

Puede transferir automáticamente este archivo a la red interna. 

> [!NOTE]
> El procedimiento de copia de seguridad y restauración se puede realizar solo en la misma versión. 

Para realizar una copia de seguridad de la máquina de la consola de administración local: 

- Inicie sesión en una cuenta administrativa y escriba `sudo cyberx-management-backup -full`.

Para restaurar el archivo de copia de seguridad más reciente:

- Inicie sesión en una cuenta administrativa y escriba `$ sudo cyberx-management-system-restore`.

Para guardar la copia de seguridad en un servidor SMB externo:

1. Cree una carpeta compartida en el servidor SMB externo.  

   Obtenga la ruta de acceso de la carpeta, el nombre de usuario y la contraseña necesarios para acceder al servidor SMB. 

2. En Defender para IoT, cree un directorio para las copias de seguridad:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Edite fstab:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Edite o cree las credenciales para el servidor SMB que se va a compartir: 

   - `sudo nano /etc/samba/user` 

5. Agregue: 

   - `username=<user name>`

   - `password=<password>` 

6. Monte el directorio: 

   - `sudo mount -a` 

7. Configure un directorio de copia de seguridad en la carpeta compartida en la consola de administración local de Defender para IoT:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Edición del nombre de host

Para editar el nombre de host de la consola de administración configurado en el servidor DNS de la organización:

1. En el panel izquierdo de la consola de administración, seleccione **Configuración del sistema**.  

2. En la sección de redes de la consola, seleccione **Red**. 

3. Escriba el nombre de host configurado en el servidor DNS de la organización. 

4. Seleccione **Guardar**.

## <a name="define-vlan-names"></a>Definición de nombres de VLAN

Los nombres de VLAN no se sincronizan entre el sensor y la consola de administración. Defina nombres idénticos en los componentes.

En el área de redes, seleccione **VLAN** y agregue nombres a los id. de VLAN detectados. Después, seleccione **Guardar**.

## <a name="define-a-proxy-to-sensors"></a>Definición de un proxy para los sensores

Mejore la seguridad del sistema impidiendo el inicio de sesión del usuario directamente en el sensor. En su lugar, use la tunelización de proxy para permitir que los usuarios accedan al sensor desde la consola de administración local con una sola regla de firewall. Esta mejora reduce la posibilidad del acceso no autorizado al entorno de red más allá del sensor.

Use un proxy en entornos en los que no haya conectividad directa a los sensores.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Usuario.":::

El siguiente procedimiento conecta un sensor a la consola de administración local y habilita la tunelización en esa consola:

1. Inicie sesión en la CLI del dispositivo de la consola de administración local con credenciales administrativas.

2. Escriba `sudo cyberx-management-tunnel-enable` y seleccione **Entrar**.

4. Escriba `--port 10000` y seleccione **Entrar**.

## <a name="adjust-system-properties"></a>Ajuste de las propiedades del sistema

Las propiedades del sistema controlan diversas operaciones y configuraciones en la consola de administración. Editarlos o modificarlos podría dañar la operación de la consola de administración. Consulte al [Soporte técnico de Microsoft](https://support.microsoft.com) antes de cambiar la configuración.

Para acceder a las propiedades del sistema: 

1. Inicie sesión en la consola de administración local o el sensor.

2. Seleccione **Configuración del sistema**.

3. Seleccione **Propiedades del sistema** en la sección **General**.

## <a name="change-the-name-of-the-on-premises-management-console"></a>Cambio del nombre de la consola de administración local

Puede cambiar el nombre de la consola de administración local. Los nombres nuevos aparecen en el explorador web de la consola, en varias ventanas de la consola y en los registros de solución de problemas. El nombre predeterminado es **consola de administración**.

Para cambiar el nombre:

1. En la parte inferior del panel izquierdo, seleccione el nombre actual.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Captura de pantalla de la versión de la consola de administración local.":::

2. En el cuadro de diálogo **Edit management console configuration** (Editar la configuración de la consola de administración), escriba el nuevo nombre. El nombre no puede tener más de 25 caracteres.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Captura de pantalla de la edición de la configuración de la plataforma Defender para IoT.":::

3. Seleccione **Guardar**. Se aplica el nuevo nombre.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Captura de pantalla que muestra el nombre cambiado de la consola.":::

## <a name="password-recovery"></a>Recuperación de la contraseña

La recuperación de la contraseña de la consola de administración local está ligada a la suscripción a la que está asociado el dispositivo. No se puede recuperar una contraseña si no se sabe a qué suscripción está asociado un dispositivo.

Para restablecer la contraseña:

1. Vaya a la página de inicio de sesión de la consola de administración local.
1. Seleccione **Recuperación de contraseña**.
1. Copie el identificador único.
1. Vaya a la página **Sites and sensors** (Sitios y sensores) de Defender para IoT y seleccione la pestaña **Recover my password** (Recuperar mi contraseña).
1. Escriba el identificador único y seleccione **Recuperar**. Se descarga el archivo de activación.
1. Vaya a la página **Recuperación de contraseña** y cargue el archivo de activación.
1. Seleccione **Next** (Siguiente).
 
   Ahora tiene el nombre de usuario y una nueva contraseña generada por el sistema.

> [!NOTE]
> El sensor está vinculado a la suscripción a la que se asoció originalmente. Solo puede recuperar la contraseña con la misma suscripción a la que está asociada.

## <a name="update-the-software-version"></a>Actualización de la versión de software

En el procedimiento siguiente se describe cómo actualizar la versión de software de la consola de administración local. El proceso de actualización tarda aproximadamente 30 minutos.

1. Vaya a [Azure Portal](https://portal.azure.com/).

1. Vaya a Defender para IoT.

1. Vaya a la página **Actualizaciones**.

1. Seleccione una versión de la sección de la consola de administración local.

1. Seleccione **Descargar** y guarde el archivo.

1. Inicie sesión en la consola de administración local y seleccione **Configuración del sistema** en el menú lateral.

1. En el panel **Actualización de la versión**, seleccione **Actualizar**.

1. Seleccione el archivo que descargó de la página **Actualizaciones** de Defender para IoT.

## <a name="mail-server-settings"></a>Configuración del servidor de correo

Defina la configuración del servidor de correo SMTP para la consola de administración local.

Para realizar la definición, siga estos pasos:

1. Inicie sesión en la CLI de la administración local con credenciales de administración.
1. Escriba ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Seleccione ENTRAR. Aparecerán las solicitudes siguientes.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. Escriba el nombre del servidor SMTP y el emisor. Luego, seleccione ENTRAR.

## <a name="see-also"></a>Consulte también

[Administración de sensores desde la consola de administración](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Administración de sensores individuales](how-to-manage-individual-sensors.md)
