---
title: 'Öğretici: mevcut bir özel etki alanını Azure Spring Cloud ile eşleme'
description: Mevcut bir özel dağıtılmış ad hizmeti (DNS) adını Azure Spring Cloud 'a eşleme
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 493752a3857b80b43668b6bf1b20480604442955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568004"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>Öğretici: mevcut bir özel etki alanını Azure Spring Cloud ile eşleme

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Etki alanı adı hizmeti (DNS), ağ düğümü adlarını ağ genelinde depolamanın bir tekniğidir. Bu öğretici, CNAME kaydı kullanarak www.contoso.com gibi bir etki alanını eşler. Özel etki alanının bir sertifikayla güvenliğini sağlar ve Güvenli Yuva Katmanı (SSL) olarak da bilinen Aktarım Katmanı Güvenliği 'ni (TLS) nasıl zorunlu hale kullanabileceğinizi gösterir. 

Sertifikalar Web trafiğini şifreler. Bu TLS/SSL sertifikaları, Azure Key Vault depolanabilir. 

## <a name="prerequisites"></a>Önkoşullar
* Azure yay bulutuna dağıtılan bir uygulama (bkz. [hızlı başlangıç: Azure Portal kullanarak mevcut bir Azure Spring Cloud uygulamasını başlatma](spring-cloud-quickstart.md)veya mevcut bir uygulamayı kullanma).
* GoDaddy gibi etki alanı sağlayıcısı için DNS kayıt defterine erişimi olan bir etki alanı adı.
* Bir üçüncü taraf sağlayıcıdan özel bir sertifika (otomatik olarak imzalanan sertifikanız). Sertifika, etki alanıyla aynı olmalıdır.
* [Azure Key Vault](../key-vault/general/overview.md) dağıtılan bir örnek

## <a name="keyvault-private-link-considerations"></a>Anahtar Kasası özel bağlantı konuları

Azure Spring Cloud Management IP 'Leri henüz Azure güvenilen Microsoft hizmetlerinin bir parçası değildir. Bu nedenle, Azure Spring Cloud 'ın özel uç nokta bağlantılarıyla korunan bir Key Vault sertifika yüklemesine izin vermek için aşağıdaki IP 'Leri Azure Key Vault güvenlik duvarı 'na eklemeniz gerekir:

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>Sertifikayı içeri aktarma
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Sertifika dosyanızı PFX 'de hazırlama (isteğe bağlı)
PEK ve PFX biçiminde özel sertifika içeri aktarma desteği Azure Key Vault. Sertifika sağlayıcınızdan aldığınız ped dosyası aşağıdaki bölümde çalışmazsa: [sertifikayı Key Vault ' de kaydedin](#save-certificate-in-key-vault), Azure Key Vault için PFX oluşturmak üzere buradaki adımları izleyin.

#### <a name="merge-intermediate-certificates"></a>Ara sertifikaları birleştirme

Sertifika yetkiliniz size sertifika zincirinde birden çok sertifika verirse, sertifikaları sırayla birleştirmeniz gerekir.

Bunu yapmak için, aldığınız her sertifikayı bir metin düzenleyicisinde açın.

Birleştirilmiş sertifika için _mergedcertificate.crt_ adlı bir dosya oluşturun. Bir metin düzenleyicisinde her bir sertifikanın içeriğini bu dosyaya kopyalayın. Sertifikalarınızın sırası, sertifikanızla başlayıp kök sertifika ile sona ererek sertifika zincirindeki sırayla aynı olmalıdır. Aşağıdaki örneğe benzer şekilde görünür:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

#### <a name="export-certificate-to-pfx"></a>Sertifikayı PFX dosyasına aktarma

Birleştirilmiş TLS/SSL sertifikanızı, Sertifika isteğinizin oluşturulduğu özel anahtarla dışarı aktarın.

Sertifika isteğinizi OpenSSL kullanarak oluşturduysanız bir özel anahtar dosyası oluşturduğunuz anlamına gelir. Sertifikanızı PFX dosyasına aktarmak için aşağıdaki komutu çalıştırın. _&lt; Özel anahtar dosyası>_ ve _&lt; birleştirilmiş-sertifika-dosya>_ yer tutucuları özel anahtarınıza ve birleştirilmiş sertifika dosyanıza yönelik yollarla değiştirin.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Sorulduğunda bir dışarı aktarma parolası tanımlayın. Daha sonra Azure Key Vault için TLS/SSL sertifikanızı karşıya yüklerken bu parolayı kullanacaksınız.

Sertifika isteğinizi oluşturmak için IIS veya _Certreq.exe_ kullandıysanız, sertifikayı yerel makinenize yükleyin ve sonra [sertifikayı PFX’e aktarın](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Sertifikayı Key Vault Kaydet
Bir sertifikayı içeri aktarma yordamı, pek veya PFX kodlamalı dosyanın diskte olması gerekir ve özel anahtara sahip olmanız gerekir. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Sertifikanızı anahtar kasasına yüklemek için:
1. Anahtar Kasası örneğinize gidin.
1. Sol gezinti bölmesinde, **Sertifikalar**' a tıklayın.
1. Üstteki menüde **Oluştur/içeri aktar**' a tıklayın.
1. **Sertifika oluşturma yönteminin** altında **sertifika oluştur** iletişim kutusunda öğesini seçin `Import` .
1. **Sertifika dosyasını karşıya yükle** altında, sertifika konumuna gidin ve seçin.
1. **Parola** altında, sertifikanız için özel anahtarı girin.
1. **Oluştur**’a tıklayın.

    ![Sertifikayı içeri aktar 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Anahtar kasanıza Azure Spring Cloud erişimi verme

Sertifikayı içeri aktarmadan önce, anahtar kasanıza Azure Spring Cloud erişimi vermeniz gerekir:
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Anahtar Kasası örneğinize gidin.
1. Sol gezinti bölmesinde, **erişim polie**' ye tıklayın.
1. Üstteki menüde, **erişim Ilkesi Ekle**' ye tıklayın.
1. Bilgileri girin ve **Ekle** düğmesine tıklayın ve ardından erişim Police 'yi **kaydedin** .

| Gizli dizi izni | Sertifika izni | Sorumlu seçin |
|--|--|--|
| Al, Listele | Al, Listele | Azure yay bulutu Domain-Management |

![Sertifikayı içeri aktar 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Azure Spring Cloud 'ın Anahtar Kasası 'na okuma erişimi izni verin, `<key vault resource group>` aşağıdaki komutta ve ' yi değiştirin `<key vault name>` .
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Sertifikayı Azure Spring Cloud 'a aktarma
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Hizmet örneğinize gidin. 
1. Uygulamanızın sol gezinti bölmesinden **TLS/SSL ayarları**' nı seçin.
1. **Key Vault sertifikayı Içeri aktar**' a tıklayın.

    ![Sertifikayı içeri aktarma](./media/custom-dns-tutorial/import-certificate.png)

1. Sertifikanızı başarıyla içeri aktardığınızda, **özel anahtar sertifikaları** listesinde bunu görürsünüz.

    ![Özel anahtar sertifikası](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

İçeri aktarılan sertifikaların listesini göstermek için:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de sertifikayı belirli bir etki alanına bağlamanız gerekir. Bu bölümdeki adımları izleyin: [SSL bağlaması Ekle](#add-ssl-binding).

## <a name="add-custom-domain"></a>Özel etki alanı Ekle
Özel bir DNS adını Azure Spring Cloud ile eşlemek için bir CNAME kaydı kullanabilirsiniz. 

> [!NOTE] 
> Kayıt desteklenmiyor. 

### <a name="create-the-cname-record"></a>CNAME kaydı oluşturma
DNS sağlayıcınıza gidin ve etki alanınızı <service_name>. azuremicroservices.io eşlemek için bir CNAME kaydı ekleyin. Burada <service_name> Azure Spring Cloud örneğinizin adıdır. Joker etki alanı ve alt etki alanı destekliyoruz. CNAME eklendikten sonra, DNS kayıtları sayfası aşağıdaki örneğe benzeyecektir: 

![DNS kayıtları sayfası](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Özel etki alanınızı Azure Spring Cloud App 'e eşleyin
Azure yay bulutu 'nda uygulamanız yoksa [hızlı başlangıç: Azure Portal kullanarak mevcut bir Azure yay bulutu uygulamasını başlatma](./spring-cloud-quickstart.md)' daki yönergeleri izleyin.

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Uygulama sayfasına gidin.

1. **Özel etki alanı**' nı seçin.
2. Ardından **özel etki alanı ekleyin**. 

    ![Özel etki alanı](./media/custom-dns-tutorial/custom-domain.png)

3. Www.contoso.com gibi bir CNAME kaydı eklediğiniz tam etki alanı adını yazın. Ana bilgisayar adı kayıt türünün CNAME (<service_name>. azuremicroservices.io) olarak ayarlandığından emin olun
4. **Ekle** düğmesini etkinleştirmek için **Doğrula** ' ya tıklayın.
5. **Ekle**'ye tıklayın.

    ![Özel etki alanı Ekle](./media/custom-dns-tutorial/add-custom-domain.png)

Bir uygulamanın birden çok etki alanı olabilir, ancak bir etki alanı yalnızca bir uygulamayla eşleşbir şekilde yapılandırılabilir. Özel etki alanınızı uygulamaya başarıyla eşledikten sonra özel etki alanı tablosunda görürsünüz.

![Özel etki alanı tablosu](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Özel etki alanlarının listesini göstermek için:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Özel etki alanınız için **güvenli olmayan** bir etiket, henüz bir SSL sertifikasına bağlanmamış anlamına gelir. Bir tarayıcıdan özel etki alanınızı HTTPS istekleri bir hata veya uyarı alır.

## <a name="add-ssl-binding"></a>SSL bağlaması Ekle

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Özel etki alanı tablosunda, önceki şekilde gösterildiği gibi **SSL bağlaması Ekle** ' yi seçin.  
1. **Sertifikanızı** seçin veya içeri aktarın.
1. **Kaydet**’e tıklayın.

    ![SSL bağlaması Ekle 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

SSL bağlamasını başarıyla ekledikten sonra, etki alanı durumu güvenli olur: **sağlıklı**. 

![SSL bağlaması Ekle 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS'yi zorunlu tutma
Varsayılan olarak, HTTP kullanarak uygulamanıza erişmeye devam edebilir, ancak tüm HTTP isteklerini HTTPS bağlantı noktasına yeniden yönlendirebilirsiniz.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Uygulama sayfanızda, sol gezinti bölmesinde **özel etki alanı**' nı seçin. Ardından, **yalnızca https**'Yi, *true* olarak ayarlayın.

![SSL bağlaması Ekle 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
İşlem tamamlandığında, uygulamanızı işaret eden HTTPS URL 'Lerinden herhangi birine gidin. HTTP URL 'Lerinin çalışmadığına unutmayın.

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Key Vault nedir?](../key-vault/general/overview.md)
* [Sertifikayı içeri aktar](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Azure CLı kullanarak Spring Cloud uygulamanızı başlatın](./spring-cloud-quickstart.md)
