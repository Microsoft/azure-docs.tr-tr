---
title: Öğretici-Azure IoT Hub X. 509.440 test sertifikaları oluşturmak için OpenSSL kullanma | Microsoft Docs
description: Öğretici-Azure IoT Hub için CA ve cihaz sertifikaları oluşturmak için OpenSSL kullanma
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378304"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Öğretici: test sertifikaları oluşturmak için OpenSSL kullanma

, Güvenilir bir sertifika yetkilisinden X. 509.440 sertifikaları satın almanıza rağmen kendi test sertifikası hiyerarşinizi oluşturmak veya otomatik olarak imzalanan sertifikalar kullanmak, IoT Hub cihaz kimlik doğrulamasını test etmek için yeterlidir. Aşağıdaki örnek, bir sertifika yetkilisi (CA), bir alt CA ve bir cihaz sertifikası oluşturmak için [OpenSSL](https://www.openssl.org/) ve [OpenSSL tanıtım rehberini](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) kullanır. Örnek daha sonra alt CA 'yı ve cihaz sertifikasını bir sertifika hiyerarşisine imzalar. Bu yalnızca örnek amaçlıdır.

## <a name="step-1---create-the-root-ca-directory-structure"></a>1. adım-kök CA dizin yapısını oluşturma

Sertifika yetkilisi için bir dizin yapısı oluşturun.

* **Sertifikalar** dizini yeni sertifikaları depolar.
* **DB** dizini, sertifika veritabanı için kullanılır.
* **Özel** DIZIN, CA özel anahtarını depolar.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>2. adım-kök CA yapılandırma dosyası oluşturma

CA oluşturmadan önce, bir yapılandırma dosyası oluşturun ve bunu `rootca.conf` RootCA dizinine kaydedin.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>3. adım-kök CA oluşturma

İlk olarak, anahtarı ve sertifika imzalama isteği 'ni (CSR) RootCA dizininde oluşturun.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Ardından, kendinden imzalı bir CA sertifikası oluşturun. Otomatik imzalama, test amacıyla uygundur. Komut satırında ca_ext yapılandırma dosyası uzantılarını belirtin. Bu, sertifikanın bir kök CA için olduğunu gösterir ve sertifikaları ve sertifika iptal listelerini (CRL 'Ler) imzalamak için kullanılabilir. Sertifikayı imzalayın ve veritabanına yürütün.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>4. adım-alt CA dizin yapısını oluşturma

Alt CA için bir dizin yapısı oluşturun.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>5. adım-alt CA yapılandırma dosyası oluşturma

Bir yapılandırma dosyası oluşturun ve dizine alt CA. conf olarak kaydedin `subca` .

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>6. adım-alt CA oluşturma

`rootca/db/serial`Alt CA sertifikası için dosyada yeni bir seri numarası oluşturun.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Her alt CA sertifikası ve oluşturduğunuz her cihaz sertifikası için yeni bir seri numarası oluşturmanız gerekir. Farklı sertifikalar aynı seri numarasına sahip olamaz.

Bu örnek, bir alt veya kayıt CA 'sı oluşturmayı gösterir. Sertifikaları imzalamak için kök CA 'yı kullanabilmeniz için bir alt CA oluşturulması kesinlikle gerekli değildir. Ancak alt CA 'nın bulunması, kök CA 'nın çevrimdışı tutulduğu ve alt CA 'Ların istemci sertifikaları verdiği gerçek dünya sertifikası hiyerarşilerini taklit ediyor.

Anahtar ve sertifika imzalama isteği (CSR) oluşturmak için yapılandırma dosyasını kullanın.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

CSR 'yi kök CA 'ya gönderin ve alt CA sertifikasını vermek ve imzalamak için kök CA 'yı kullanın. Komut satırında uzantılar anahtarı için sub_ca_ext belirtin. Uzantılar, sertifikanın sertifikaları ve sertifika iptal listelerini (CRL 'Ler) imzalayaabileceği bir CA için olduğunu gösterir. İstendiğinde, sertifikayı imzalayın ve veritabanına yürütün.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>7. adım-elinde bulunan kanıtları gösterir

Artık bir kök CA sertifikasına ve bir alt CA sertifikasına sahip olursunuz. Cihaz sertifikalarını imzalamak için bunlardan birini kullanabilirsiniz. Seçtiğiniz birinin IoT Hub karşıya yüklenmesi gerekir. Aşağıdaki adımlarda, alt CA sertifikasını kullandığınız varsayılır. Alt CA sertifikanızı karşıya yüklemek ve IoT Hub kaydetmek için:

1. Azure portal, ıothub adresine gidin ve **ayarlar > sertifikalar**' ı seçin.

1. Yeni alt CA sertifikanızı eklemek için **Ekle** ' yi seçin.

1. **Sertifika adı** alanına bir görünen ad girin ve daha önce oluşturduğunuz pek sertifika dosyasını seçin.

1. **Kaydet**’i seçin. Sertifikanız, sertifika listesinde **doğrulanmamış** durumuyla gösterilir. Doğrulama işlemi, sertifikaya sahip olduğunuzu kanıtlayacaktır.

   
1. **Sertifika ayrıntıları** iletişim kutusunu görüntülemek için sertifikayı seçin.

1. **Doğrulama kodu oluştur**' u seçin. Daha fazla bilgi için bkz. [CA sertifikasını kanıtlayın](tutorial-x509-prove-possession.md).

1. Doğrulama kodunu panoya kopyalayın. Doğrulama kodunu sertifika konusu olarak ayarlamanız gerekir. Örneğin, doğrulama kodu BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A ise, bunu adım 9 ' da gösterildiği gibi sertifika konusu olarak ekleyin.

1. Özel anahtar oluşturun.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Özel anahtardan bir sertifika imzalama isteği (CSR) oluşturun. Doğrulama kodunu sertifikanızın konusu olarak ekleyin.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. Kök CA Yapılandırma dosyasını ve sertifika kanıtı için CSR 'yi kullanarak bir sertifika oluşturun.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. **Sertifika ayrıntıları** görünümündeki yeni sertifikayı seçin. PED dosyasını bulmak için, Sertifikalar klasörüne gidin.

12. Sertifika karşıya yüklendikten sonra **Doğrula**' yı seçin. CA sertifikası durumunun **doğrulandı** olarak değiştirilmesi gerekir.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>8. adım-IoT Hub cihaz oluşturma

Azure portal IoT Hub gidin ve aşağıdaki değerlerle yeni bir IoT cihaz kimliği oluşturun:

1. Cihaz sertifikalarınızın konu adıyla eşleşen **CIHAZ kimliğini** sağlayın.

1. **X. 509.952 CA imzalı** kimlik doğrulama türünü seçin.

1. **Kaydet**’i seçin.

## <a name="step-9---create-a-client-device-certificate"></a>9. adım-istemci cihaz sertifikası oluşturma

Bir istemci sertifikası oluşturmak için önce bir özel anahtar oluşturmalısınız. Aşağıdaki komut, özel anahtar oluşturmak için OpenSSL 'in nasıl kullanılacağını gösterir. Alt CA dizininde anahtarı oluşturun.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Anahtar için bir sertifika imzalama isteği (CSR) oluşturun. Bir sınama parolası ya da isteğe bağlı bir şirket adı girmeniz gerekmez. Ancak, ortak ad alanına cihaz KIMLIĞINI girmeniz gerekir.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

CSR 'nin beklediğiniz şekilde olduğunu denetleyin.

```bash
openssl req -text -in device.csr -noout
```

Sertifika hiyerarşisinde oturum açmak için CSR 'yi alt CA 'ya gönderin. `client_ext` `-extensions` Anahtarda belirtin. `Basic Constraints`Verilen sertifikada, bu sertifikanın BIR CA için olmadığını gösterdiğine dikkat edin. Birden çok sertifika imzalandıysanız, OpenSSL komutunu kullanarak her sertifikayı oluşturmadan önce seri numarasını güncelleştirdiğinizden emin olun `rand -hex 16 > db/serial` .

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Sonraki Adımlar

Sertifikanızın, cihazınızın IoT Hub kimlik doğrulaması yapamadığını öğrenmek için [sertifika kimlik doğrulamasını test etme](tutorial-x509-test-certificate.md) bölümüne gidin.
