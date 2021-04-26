---
title: Azure Stack Edge Pro GPU 'SU ile sertifika kullanma | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazındaki sertifikaların kullanımını açıklar ve bu, hangi türleri ve cihazınıza sertifika yükleme işleminin nasıl yapılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 534870e6bd67b7aa5273289f3154a794a2b9bd22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519019"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazındaki sertifikaları kullanma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede Azure Stack Edge Pro cihazınıza yüklenebilen sertifika türleri açıklanmaktadır. Makalede ayrıca, her sertifika türünün ayrıntıları ve sona erme tarihini yüklemek ve tanımlamak için olan yordamlar da yer alır.  

## <a name="about-certificates"></a>Sertifikalar hakkında

Bir sertifika, güvenilir bir üçüncü taraf (örneğin, bir **sertifika yetkilisi**) tarafından **imzalanmış** (doğrulanmış) bir **ortak anahtar** ve bir varlık (örneğin, etki alanı adı) arasında bir bağlantı sağlar.  Bir sertifika, güvenilir ortak şifreleme anahtarları dağıtmanın kolay bir yolunu sağlar. Sertifikalar bundan sonra iletişiminizin güvenilir olmasını ve doğru sunucuya şifreli bilgiler gönderdiğinizden emin olmanızı sağlar. 

Azure Stack Edge Pro cihazınız ilk yapılandırıldığında otomatik olarak imzalanan sertifikalar otomatik olarak oluşturulur. İsteğe bağlı olarak, kendi sertifikalarınızı getirebilirsiniz. Kendi sertifikalarınızı getirmeyi planlıyorsanız izlemeniz gereken yönergeler vardır.

## <a name="types-of-certificates"></a>Sertifika türleri

Azure Stack Edge Pro cihazınızda kullanılan çeşitli sertifika türleri şunlardır: 
- İmzalama sertifikaları
    - Kök CA
    - Orta

- Uç nokta sertifikaları
    - Düğüm sertifikası
    - Yerel UI sertifikaları
    - Azure Resource Manager sertifikaları
    - BLOB depolama sertifikaları
    - IoT cihaz sertifikaları
    <!--- WiFi certificates
    - VPN certificates-->

- Şifreleme sertifikaları
    - Oturum sertifikalarını destekleme

Bu sertifikaların her biri, aşağıdaki bölümlerde ayrıntılı olarak açıklanmıştır.

## <a name="signing-chain-certificates"></a>İmzalama zinciri sertifikaları

Bunlar, sertifikaları veya imza sertifika yetkilisini imzalayan yetkilinin sertifikalardır. 

### <a name="types"></a>Türler

Bu sertifikalar kök sertifikalar veya ara sertifikalar olabilir. Kök sertifikalar her zaman otomatik olarak imzalanır (veya kendisi tarafından imzalanır). Ara sertifikalar kendinden imzalı değildir ve imzalama yetkilisi tarafından imzalanır.

### <a name="caveats"></a>Uyarılar

- Kök sertifikalar, imzalama zinciri sertifikaları olmalıdır.
- Kök sertifikalar aşağıdaki biçimde cihazınıza yüklenebilir: 
    - **Der** : Bunlar bir `.cer` dosya uzantısı olarak kullanılabilir.
    - **Base-64 kodlamalı** – bunlar `.cer` dosya uzantısı olarak kullanılabilir.
    - **P7b** : Bu biçim yalnızca kök ve ara sertifikaları içeren imzalama zinciri sertifikaları için kullanılır.
- Başka herhangi bir sertifika yüklemeden önce imzalama zinciri sertifikaları her zaman yüklenir.


## <a name="node-certificates"></a>Düğüm sertifikaları

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Cihazdaki tüm düğümler sürekli olarak birbirleriyle iletişim kuruyor ve bu nedenle bir güven ilişkisine sahip olması gerekir. Düğüm sertifikaları, bu güveni oluşturmak için bir yol sağlar. Ayrıca, https üzerinden uzak bir PowerShell oturumu kullanarak cihaz düğümüne bağlandığınızda, düğüm sertifikaları da oynar.

### <a name="caveats"></a>Uyarılar

- Düğüm sertifikası, `.pfx` verilebilen bir özel anahtarla biçimde sağlanmalıdır. 
- 1 joker karakter düğümü sertifikası veya 4 ayrı düğüm sertifikası oluşturabilir ve karşıya yükleyebilirsiniz. 
- DNS etki alanı değişirse, ancak cihaz adı değişmezse düğüm sertifikası değiştirilmelidir. Kendi düğüm sertifikanızı getiriyoruz, cihazın seri numarasını değiştiremezsiniz, yalnızca etki alanı adını değiştirebilirsiniz.
- Düğüm sertifikası oluştururken size rehberlik etmek için aşağıdaki tabloyu kullanın.
   
    |Tür |Konu adı (SN)  |Konu alternatif adı (SAN)  |Konu adı örneği |
    |---------|---------|---------|---------|
    |Düğüm|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Uç nokta sertifikaları

Cihazın sunduğu uç noktalar için, güvenilen iletişim için bir sertifika gerekir. Uç nokta sertifikaları, REST API 'Leri aracılığıyla Azure Resource Manager ve BLOB depolamaya erişirken gerekli olanları içerir. 

Kendi imzalı bir sertifikayı aldığınızda, sertifikanın karşılık gelen imzalama zinciri de gereklidir. Cihazdaki imzalama zinciri, Azure Resource Manager ve BLOB sertifikaları için, istemci makinesinde karşılık gelen sertifikalara da kimlik doğrulaması ve cihazla iletişim kurmak için ihtiyaç duyarsınız.

### <a name="caveats"></a>Uyarılar

- Uç nokta sertifikalarının `.pfx` bir özel anahtarla biçimde olması gerekir. İmzalama zinciri DER biçiminde ( `.cer` dosya uzantısı) olmalıdır. 
- Kendi uç nokta sertifikalarınızı aldığınızda bunlar tek tek sertifika veya çok etki alanı sertifikaları olabilir. 
- İmzalama zincirinde çalışıyorsanız, bir uç nokta sertifikasını karşıya yüklemeden önce imzalama zinciri sertifikası karşıya yüklenmelidir.
- Cihaz adı veya DNS etki alanı adları değiştirilirse bu sertifikaların değiştirilmesi gerekir.
- Joker uç nokta sertifikası kullanılabilir.
- Uç nokta sertifikalarının özellikleri, tipik bir SSL sertifikası ile benzerdir. 
- Bir uç nokta sertifikası oluştururken aşağıdaki tabloyu kullanın:

    |Tür |Konu adı (SN)  |Konu alternatif adı (SAN)  |Konu adı örneği |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob depolama|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Her iki uç nokta için birden çok SAN tek sertifika|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Yerel UI sertifikaları

Cihazınızın yerel Web Kullanıcı arabirimine bir tarayıcı aracılığıyla erişebilirsiniz. Bu iletişimin güvenli olduğundan emin olmak için kendi sertifikanızı karşıya yükleyebilirsiniz. 

### <a name="caveats"></a>Uyarılar

- Yerel Kullanıcı arabirimi sertifikası, `.pfx` verilebilecektir özel bir anahtara sahip bir biçimde de karşıya yüklenir.
- Yerel Kullanıcı arabirimi sertifikasını karşıya yükledikten sonra, tarayıcıyı yeniden başlatmanız ve Önbelleği temizlemeniz gerekir. Tarayıcınızla ilgili özel yönergelere bakın.

    |Tür |Konu adı (SN)  |Konu alternatif adı (SAN)  |Konu adı örneği |
    |---------|---------|---------|---------|
    |Yerel Kullanıcı arabirimi| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge cihaz sertifikaları

Azure Stack Edge Pro cihazınız Ayrıca, kendisine bağlı bir IoT Edge cihaz tarafından etkinleştirilen bir IoT cihazdır. Bu IoT Edge cihaz ile buna bağlanabilecek aşağı akış cihazları arasındaki güvenli iletişim için IoT Edge sertifikaları da yükleyebilirsiniz. 

Cihaz ile yalnızca işlem senaryosunu kullanmak istiyorsanız, cihazda kullanılabilecek otomatik olarak imzalanan sertifikalar vardır. Azure Stack Edge Pro cihazı, ancak aşağı akış cihazlarına bağlandıysa, kendi sertifikalarınızı almanız gerekir.

Bu güven ilişkisini etkinleştirmek için yüklemeniz gereken üç IoT Edge sertifika vardır:

- **Kök sertifika yetkilisi veya sahip sertifika yetkilisi**
- **Cihaz sertifika yetkilisi** 
- **Cihaz anahtarı sertifikası**

### <a name="caveats"></a>Uyarılar

- IoT Edge sertifikaları biçimde karşıya yüklenir `.pem` . 


IoT Edge sertifikaları hakkında daha fazla bilgi için bkz. [Azure IoT Edge sertifika ayrıntıları](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Oturum sertifikalarını destekleme

Azure Stack Edge Pro cihazınızda herhangi bir sorun yaşıyorsanız, bu sorunları gidermek için cihazda bir uzak PowerShell destek oturumu açılabilir. Bu destek oturumu üzerinden güvenli, şifreli bir iletişim sağlamak için bir sertifika yükleyebilirsiniz.

### <a name="caveats"></a>Uyarılar

- `.pfx`Özel anahtara sahip karşılık gelen sertifikanın, şifre çözme Aracı kullanılarak istemci makineye yüklendiğinden emin olun.
- Sertifika için **anahtar kullanımı** alanının **sertifika imzalama** olmadığından emin olun. Bunu doğrulamak için, sertifikaya sağ tıklayın, **Aç** ' ı seçin ve **Ayrıntılar** sekmesinde **anahtar kullanımı**' nı bulun. 


### <a name="caveats"></a>Uyarılar

- Destek oturumu sertifikası, bir uzantıya sahip DER biçimi olarak sağlanmalıdır `.cer` .


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Sertifika Oluştur (isteğe bağlı)

Aşağıdaki bölümde, imzalama zinciri ve uç nokta sertifikaları oluşturma yordamı açıklanmaktadır.

### <a name="certificate-workflow"></a>Sertifika iş akışı

Ortamınızda çalışan cihazların sertifikalarını oluşturmak için tanımlı bir yola sahip olursunuz. BT yöneticiniz tarafından size sunulan sertifikaları kullanabilirsiniz. 

**Yalnızca geliştirme veya test amacıyla, yerel sisteminizde sertifika oluşturmak için Windows PowerShell de kullanabilirsiniz.** İstemci için sertifikaları oluştururken şu yönergeleri izleyin:

1. Aşağıdaki sertifika türlerinden herhangi birini oluşturabilirsiniz:

    - Tek bir tam etki alanı adı (FQDN) ile kullanılmak üzere geçerli tek bir sertifika oluşturun. Örneğin, *mydomain.com*.
    - Ana etki alanı adını ve birden çok alt etki alanını da güvenli hale getirmek için bir joker karakter sertifikası oluşturun. Örneğin, **. mydomain.com*.
    - Tek bir sertifikadaki birden çok etki alanı adını kapsayan bir konu diğer adı (SAN) sertifikası oluşturun. 

2. Kendi sertifikanızı getiriyoruz, imzalama zinciri için bir kök sertifikaya ihtiyacınız olacaktır. [İmzalama zinciri sertifikaları oluşturma](#create-signing-chain-certificate)adımlarına bakın.

3. Daha sonra gereç, blob ve Azure Resource Manager yerel kullanıcı arabirimi için uç nokta sertifikaları oluşturabilirsiniz. Gereç, blob ve Azure Resource Manager için 3 ayrı sertifika oluşturabilir veya tüm 3 uç noktaları için bir sertifika oluşturabilirsiniz. Ayrıntılı adımlar için bkz. [imzalama ve uç nokta sertifikaları oluşturma](#create-signed-endpoint-certificates).

4. 3 ayrı sertifika veya bir sertifika oluşturup oluşturmadığınızda, her sertifika türü için belirtilen kılavuza göre konu adlarını (SN) ve konu diğer adlarını (SAN) belirtin. 

### <a name="create-signing-chain-certificate"></a>İmzalama zinciri sertifikası oluşturma

Bu sertifikaları, yönetici modunda çalışan Windows PowerShell aracılığıyla oluşturun. **Bu şekilde oluşturulan sertifikaların yalnızca geliştirme veya test amacıyla kullanılması gerekir.**

İmzalama zinciri sertifikasının yalnızca bir kez oluşturulması gerekir. Diğer uç nokta sertifikaları, imzalanmak üzere bu sertifikaya başvurur.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>İmzalı uç nokta sertifikaları oluşturma

Bu sertifikaları, yönetici modunda çalışan Windows PowerShell aracılığıyla oluşturun.

Bu örneklerde, ile bir cihaz için uç nokta sertifikaları oluşturulur:
    - Cihaz adı: `DBE-HWDC1T2`
    - DNS etki alanı: `microsoftdatabox.com`

Cihazınız için sertifikalar oluşturmak üzere cihazınızın adı ve DNS etki alanı ile değiştirin.
 
**Blob uç noktası sertifikası**

Kişisel deponuzdaki blob uç noktası için bir sertifika oluşturun.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Uç nokta sertifikası Azure Resource Manager**

Kişisel deponuzdaki Azure Resource Manager uç noktaları için bir sertifika oluşturun.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Cihaz yerel Web Kullanıcı arabirimi sertifikası**

Kişisel deponuzdaki cihazın yerel Web Kullanıcı arabirimi için bir sertifika oluşturun.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Tüm uç noktalar için tek bir çoklu SAN sertifikası**

Kişisel Deponuzdaki tüm uç noktalar için tek bir sertifika oluşturun.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Sertifikalar oluşturulduktan sonra, bir sonraki adım Azure Stack Edge Pro cihazınıza sertifika yükleme


## <a name="upload-certificates"></a>Sertifikaları karşıya yükle 

Cihazınız için oluşturduğunuz sertifikalar, varsayılan olarak istemcinizdeki **Kişisel mağazada** bulunur. Bu sertifikaların, daha sonra cihazınıza yüklenebilen uygun biçim dosyalarına istemcinizi aktarılması gerekir.

1. Kök sertifika, uzantıya sahip DER biçiminde verilmelidir `.cer` . Ayrıntılı adımlar için bkz. [SERTIFIKALARı der biçiminde dışarı aktarma](#export-certificates-as-der-format).
2. Uç nokta sertifikaları, özel anahtarlara sahip *. pfx* dosyaları olarak verilmelidir. Ayrıntılı adımlar için bkz [. sertifikaları özel anahtarlarla *. pfx* dosyası olarak dışarı aktarma](#export-certificates-as-pfx-format-with-private-key). 
3. Kök ve uç nokta sertifikaları, yerel Web Kullanıcı arabirimindeki sertifikalar sayfasında **+ sertifika ekle** seçeneği kullanılarak cihaza yüklenir. 

    1. Önce kök sertifikaları karşıya yükleyin. Yerel Web Kullanıcı arabiriminde, **sertifikalar > + sertifika ekle**' ye gidin.

        ![İmzalama zinciri sertifikası Ekle 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Ardından, uç nokta sertifikalarını karşıya yükleyin. 

        ![İmzalama zinciri sertifikası Ekle 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Sertifika dosyalarını *. pfx* biçiminde seçin ve sertifikayı verdiğinizde sağladığınız parolayı girin. Azure Resource Manager sertifikasının uygulanması birkaç dakika sürebilir.

        İmzalama zinciri önce güncellenmemişse ve uç nokta sertifikalarını karşıya yüklemeye çalışırsanız bir hata alırsınız.

        ![Sertifika uygulama hatası](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Geri dönüp imzalama zinciri sertifikasını karşıya yükleyin ve ardından uç nokta sertifikalarını yükleyin ve uygulayın.

> [!IMPORTANT]
> Cihaz adı veya DNS etki alanı değiştirilirse yeni sertifikaların oluşturulması gerekir. Daha sonra istemci sertifikaları ve cihaz sertifikaları yeni cihaz adı ve DNS etki alanıyla güncelleştirilecektir. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Cihaza erişen istemcideki sertifikaları içeri aktarma

Oluşturduğunuz ve cihazınıza yüklediğiniz sertifikaların, Windows istemcinizdeki (cihaza erişim) uygun sertifika deposuna aktarılması gerekir.

1. DER olarak verdiğiniz kök sertifika, artık istemci sisteminizdeki **Güvenilen kök sertifika yetkililerine** içeri aktarılmalıdır. Ayrıntılı adımlar için bkz. [Sertifikaları güvenilen kök sertifika yetkilileri deposuna aktarma](#import-certificates-as-der-format).

2. Olarak verdiğiniz bitiş noktası sertifikalarının `.pfx` UZANTıLı der olarak aktarılması gerekir `.cer` . Bu `.cer` , daha sonra sisteminizdeki **kişisel sertifika deposuna** aktarılır. Ayrıntılı adımlar için bkz. [sertifikaları Kişisel sertifika deposuna alma](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Sertifikaları DER biçimi olarak içeri aktar

Windows istemcisinde sertifikaları içeri aktarmak için aşağıdaki adımları uygulayın:

1. Dosyaya sağ tıklayın ve **sertifikayı yükler**' i seçin. Bu eylem, sertifika alma Sihirbazı 'nı başlatır.

    ![Sertifikayı içeri aktar 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. **Depo konumu** Için **yerel makine**' yi seçin ve ardından **İleri**' yi seçin.

    ![Sertifikayı içeri aktar 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. **Tüm sertifikaları aşağıdaki depolama alanına yerleştir**' i seçin ve ardından da **Araştır**' ı seçin. 

    - Kişisel mağazaya aktarmak için uzak ana bilgisayarın kişisel deposuna gidin ve ardından **İleri**' yi seçin.

        ![Sertifikayı içeri aktar 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Güvenilen depoya aktarmak için, güvenilen kök sertifika yetkilisine gidin ve ardından **İleri**' yi seçin.

        ![Sertifika içeri aktarma 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. **Son**'u seçin. İçeri aktarma işleminin başarılı olduğu etkiye bir ileti görüntülenir.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Sertifikaları özel anahtarla. pfx biçiminde dışarı aktarma

Bir Windows makinesinde özel anahtarla SSL sertifikasını dışarı aktarmak için aşağıdaki adımları uygulayın. 

> [!IMPORTANT]
> Bu adımları, sertifikayı oluşturmak için kullandığınız makinede gerçekleştirin. 

1. Yerel makine sertifika deposunu başlatmak için *Certlm. msc* dosyasını çalıştırın.

1. **Kişisel** klasöre ve ardından **Sertifikalar**' a çift tıklayın.

    ![Sertifikayı dışarı aktar 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Yedeklemek istediğiniz sertifikaya sağ tıklayın ve **dışarı aktar > tüm görevler ' i seçin...**

    ![Sertifikayı dışarı aktar 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Sertifikanızı bir. pfx dosyasına yedeklemek için sertifika dışarı aktarma Sihirbazı ' nı izleyin.

    ![Sertifika dışarı aktarma 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. **Evet, özel anahtarı dışarı aktar ' ı** seçin.

    ![Sertifikayı dışarı aktar 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Mümkünse, tüm **genişletilmiş özellikleri dışarı aktarın** ve **sertifika gizliliğini etkinleştirmek** için **sertifika yolundaki tüm sertifikaları dahil et**' i seçin. 

    > [!IMPORTANT]
    > **Dışarı aktarma başarılı olursa özel anahtarı sil seçeneğini** seçmeyin.

    ![Sertifikayı dışarı aktar 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Hatırlayacaksınız bir parola girin. Parola’yı onaylayın. Parola özel anahtarı korur.

    ![Sertifikayı dışarı aktar 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Dosyayı bir küme konumuna kaydetmeyi seçin.

    ![Sertifikayı dışarı aktar 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. **Son**'u seçin.

    ![Sertifika dışarı aktarma 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Dışarı aktarma işleminin başarılı olduğunu bir ileti alırsınız. **Tamam**’ı seçin.

    ![Sertifikayı dışarı aktar 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

. Pfx dosya yedeklemesi artık seçtiğiniz konuma kaydedilir ve güvenli tutmanın taşınması veya saklanması için hazırdır.


### <a name="export-certificates-as-der-format"></a>Sertifikaları DER biçimi olarak dışarı aktar

1. Yerel makine sertifika deposunu başlatmak için *Certlm. msc* dosyasını çalıştırın.

1. Kişisel sertifika deposunda kök sertifikayı seçin. Sağ tıklayın ve **dışarı aktar > tüm görevler ' i seçin...**

    ![Sertifika verme DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Sertifika Sihirbazı açılır. Bu biçimi **der kodlu Ikili X. 509.440 (. cer)** olarak seçin. **İleri**’yi seçin.

    ![Sertifika verme DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. . Cer biçim dosyasını dışarı aktarmak istediğiniz konuma gidin ve seçin.

    ![Sertifika verme DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. **Son**'u seçin.

    ![Sertifika dışarı aktarma DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Desteklenen sertifika algoritmaları

 Azure Stack Edge Pro cihazınız ile yalnızca Rivest – Shamir – Adtaman (RSA) sertifikaları desteklenir. Eliptik Eğri dijital Imza algoritması (ECDSA) sertifikaları desteklenmez.

 RSA ortak anahtarı içeren sertifikalara RSA sertifikaları denir. Eliptik Eğri Şifreleme (ECC) ortak anahtarı içeren sertifikalara ECDSA (Eliptik Eğri dijital Imza algoritması) sertifikaları denir. 


## <a name="view-certificate-expiry"></a>Sertifika süre sonunu görüntüleme

Kendi sertifikalarınızı getirirsiniz, sertifikaların genellikle 1 yıl veya 6 ay içinde sona erecektir. Sertifikanızın sona erme tarihini görüntülemek için cihazınızın yerel Web Kullanıcı arabirimindeki **Sertifikalar** sayfasına gidin. Belirli bir sertifikayı seçerseniz, sertifikanıza ait süre sonu tarihini görebilirsiniz.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro cihazınızı dağıtma](azure-stack-edge-gpu-deploy-prep.md)