---
title: TLS/SSL sertifikaları ekleme ve yönetme
description: Azure App Service 'de ücretsiz bir sertifika oluşturun, App Service sertifikayı içeri aktarın, Key Vault sertifikasını içeri aktarın veya App Service sertifikası satın alın.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1e05435f364cc30b351275439a04caff47c35512
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871804"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Azure App Service'de TLS/SSL sertifikası ekleme

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu makalede, App Service bir özel sertifika veya ortak sertifika oluşturma, karşıya yükleme veya içeri aktarma işlemlerinin nasıl yapılacağı gösterilir. 

Sertifika, App Service uygulamanıza veya [işlev uygulamanıza](../azure-functions/index.yml)eklendikten sonra [özel bir DNS adının güvenliğini](configure-ssl-bindings.md) sağlayabilir veya [uygulama kodunuzda kullanabilirsiniz](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Bir uygulamaya yüklenen bir sertifika, uygulamanın kaynak grubuna ve bölge birleşimine (dahili olarak bir *Web alanı* olarak adlandırılır) bağlanan bir dağıtım biriminde depolanır. Bu, sertifikayı aynı kaynak grubu ve bölge kombinasyonuna ait diğer uygulamalar için erişilebilir hale getirir. 

Aşağıdaki tabloda App Service sertifika eklemek için sahip olduğunuz seçenekler listelenmektedir:

|Seçenek|Açıklama|
|-|-|
| App Service yönetilen ücretsiz sertifika oluşturma (Önizleme) | Yalnızca App Service [özel etki](app-service-web-tutorial-custom-domain.md) alanınızı güvenli hale getirmeniz gerekiyorsa, ücretsiz ve kullanımı kolay olan özel bir sertifika. |
| App Service sertifikası satın alma | Azure tarafından yönetilen özel bir sertifika. Otomatik sertifika yönetiminin basitliğini ve yenileme ve dışa aktarma seçeneklerinin esnekliğini birleştirir. |
| Key Vault bir sertifikayı içeri aktar | [PKCS12 sertifikalarınızı](https://wikipedia.org/wiki/PKCS_12)yönetmek için [Azure Key Vault](../key-vault/index.yml) kullanıyorsanız faydalıdır. Bkz. [özel sertifika gereksinimleri](#private-certificate-requirements). |
| Özel bir sertifikayı karşıya yükle | Bir üçüncü taraf sağlayıcıdan zaten özel bir sertifikanız varsa, bu sertifikaya yükleyebilirsiniz. Bkz. [özel sertifika gereksinimleri](#private-certificate-requirements). |
| Ortak sertifikayı karşıya yükle | Ortak sertifikalar özel etki alanlarını güvenli hale getirmek için kullanılmaz, ancak uzak kaynaklara erişmesi gerekiyorsa bunları kodunuza yükleyebilirsiniz. |

## <a name="prerequisites"></a>Önkoşullar

- [App Service uygulaması oluşturun](./index.yml).
- Özel bir sertifika için [App Service tüm gereksinimleri karşıladığından](#private-certificate-requirements)emin olun.
- **Yalnızca ücretsiz sertifika**:
    - Sertifika istediğiniz etki alanını App Service için eşleyin. Bilgi için bkz. [öğretici: mevcut bir özel DNS adını Azure App Service eşleme](app-service-web-tutorial-custom-domain.md).
    - Kök etki alanı (contoso.com gibi) için, uygulamanızın yapılandırılmış [IP kısıtlamalarına](app-service-ip-restrictions.md) sahip olmadığından emin olun. Hem sertifika oluşturma hem de bir kök etki alanı için düzenli yenileme, uygulamanıza internet 'ten erişilebilmesine bağlıdır.

## <a name="private-certificate-requirements"></a>Özel sertifika gereksinimleri

[Ücretsiz App Service yönetilen sertifika](#create-a-free-managed-certificate-preview) ve [App Service sertifikası](#import-an-app-service-certificate) App Service gereksinimlerini zaten karşılamış. App Service bir özel sertifikayı karşıya yüklemeyi veya aktarmayı seçerseniz, sertifikanızın aşağıdaki gereksinimleri karşılaması gerekir:

* [Parola KORUMALı pfx dosyası](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)olarak verildiğinde, Üçlü DES kullanılarak şifrelenir.
* En az 2048 bit uzunluğunda özel anahtar içermesi
* Sertifika zincirindeki tüm ara sertifikaları içermesi

Bir TLS bağlamasındaki özel bir etki alanının güvenliğini sağlamak için, sertifikanın ek gereksinimleri vardır:

* Sunucu kimlik doğrulaması için bir [genişletilmiş anahtar kullanımı](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) IÇERIR (OID = 1.3.6.1.5.5.7.3.1)
* Güvenilir bir sertifika yetkilisi tarafından imzalanması

> [!NOTE]
> **Eliptik Eğri Şifrelemesi (ECC) sertifikaları**, App Service ile birlikte çalışabilir ancak bu makalenin konusu değildir. ECC sertifikaları oluşturmaya ilişkin tam adımlar için sertifika yetkilinizle birlikte çalışın.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>Ücretsiz yönetilen sertifika oluşturma (Önizleme)

> [!NOTE]
> Ücretsiz bir yönetilen sertifika oluşturmadan önce, uygulamanız için [önkoşulları karşıladığınızdan](#prerequisites) emin olun.

App Service yönetilen ücretsiz sertifika, App Service özel DNS adınızı güvenli hale getirmek için bir açılan anahtar çözümüdür. Bu, App Service tarafından yönetilen ve otomatik olarak yenilenen tam işlevli bir TLS/SSL sertifikasıdır. Ücretsiz sertifika aşağıdaki sınırlamalara sahiptir:

- Joker sertifikaları desteklemez.
- Dışarı aktarılabilir değil.
- App Service Ortamı (Ao) üzerinde desteklenmez.
- , Traffic Manager ile tümleştirilmiş kök etki alanları ile desteklenmez.

> [!NOTE]
> Ücretsiz sertifika, DigiCert tarafından verilir. Bazı üst düzey etki alanları için, şu değere sahip bir [CAA etki alanı kaydı](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) oluşturarak bir sertifika veren olarak DigiCert 'e açıkça izin vermeniz gerekir: `0 issue digicert.com` .
> 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden **uygulama hizmetleri**' ni seçin  >  **\<app-name>** .

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları**  >  **özel anahtar sertifikaları (. pfx)**  >  **App Service yönetilen sertifika oluştur**' u seçin.

![App Service 'de ücretsiz sertifika oluşturma](./media/configure-ssl-certificate/create-free-cert.png)

İçin ücretsiz bir sertifika oluşturmak üzere özel etki alanını seçin ve **Oluştur**' u seçin. Desteklenen her bir özel etki alanı için yalnızca bir sertifika oluşturabilirsiniz.

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz.

![Ücretsiz sertifika oluşturma tamamlandı](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de bir sertifika bağlaması oluşturmanız gerekir. [Bağlama oluşturma](configure-ssl-bindings.md#create-binding)bölümündeki adımları izleyin.
>

## <a name="import-an-app-service-certificate"></a>App Service Sertifikası içeri aktarma

Azure 'dan bir App Service Sertifikası satın alırsanız Azure aşağıdaki görevleri yönetir:

- GoDaddy adresinden satın alma sürecini ele alır.
- Sertifika için etki alanı doğrulaması gerçekleştirir.
- [Azure Key Vault](../key-vault/general/overview.md)' de sertifikayı korur.
- Sertifika yenilemeyi yönetir (bkz. [Sertifikayı Yenile](#renew-certificate)).
- Sertifikayı App Service uygulamalardaki içeri aktarılan kopyalarla otomatik olarak eşitler.

Bir App Service sertifikası satın almak için, [Başlangıç sertifikası siparişi](#start-certificate-order)' ne gidin.

Zaten çalışan bir App Service sertifikanız varsa şunları yapabilirsiniz:

- [Sertifikayı App Service Içine aktarın](#import-certificate-into-app-service).
- [Sertifikayı](#manage-app-service-certificates), yenileme, yeniden anahtarlama ve dışarı aktarma gibi yönetin.
> [!NOTE]
> App Service sertifikaları Şu anda Azure Ulusal bulutlarında desteklenmez.

### <a name="start-certificate-order"></a>Sertifika sırasını Başlat

<a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service sertifikası oluştur sayfasında</a>bir App Service sertifikası siparişi başlatın.

![App Service sertifikası satın almayı Başlat](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Sertifikayı yapılandırmanıza yardımcı olması için aşağıdaki tabloyu kullanın. Tamamladığınızda **Oluştur**’a tıklayın.

| Ayar | Açıklama |
|-|-|
| Ad | App Service sertifikanız için kolay bir ad. |
| Naked etki alanı konak adı | Kök etki alanını burada belirtin. Verilen sertifika hem kök etki alanının hem *de* alt etki alanının güvenliğini sağlar `www` . Verilen sertifikada, ortak ad alanı kök etki alanını içerir ve konu alternatif adı alanı `www` etki alanını içerir. Yalnızca herhangi bir alt etki alanının güvenliğini sağlamak için burada alt etki alanının tam etki alanı adını belirtin (örneğin, `mysubdomain.contoso.com` ).|
| Abonelik | Sertifikayı içerecek abonelik. |
| Kaynak grubu | Sertifikayı içerecek kaynak grubu. Örneğin, yeni bir kaynak grubu kullanabilir veya App Service uygulamanız ile aynı kaynak grubunu seçebilirsiniz. |
| Sertifika SKU 'SU | Oluşturulacak sertifikanın türünü, standart bir sertifika veya [Joker bir sertifika](https://wikipedia.org/wiki/Wildcard_certificate)olduğunu belirler. |
| Yasal koşullar | Yasal koşulları kabul ettiğinizi onaylamak için tıklayın. Sertifikalar GoDaddy adresinden elde edilir. |

> [!NOTE]
> Azure 'dan satın alınan App Service sertifikaları, GoDaddy tarafından verilir. Bazı üst düzey etki alanları için, şu değere sahip bir [CAA etki alanı kaydı](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) oluşturarak GoDaddy bir sertifika veren olarak açıkça izin vermeniz gerekir: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Azure Key Vault içinde depola

Sertifika satın alma işlemi tamamlandıktan sonra, bu sertifikayı kullanmaya başlayabilmeniz için yapmanız gereken birkaç adım daha vardır. 

[App Service sertifikaları](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından **sertifika yapılandırması**  >  **Adım 1: depolama**' ya tıklayın.

![App Service sertifikası Key Vault depolamayı yapılandırma](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) , bulut uygulamaları ve Hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olan bir Azure hizmetidir. Bu, App Service sertifikaları için tercih edilen depolardır.

**Key Vault durum** sayfasında, yeni bir kasa oluşturmak veya mevcut bir kasayı seçmek Için **Key Vault deposu** ' na tıklayın. Yeni bir kasa oluşturmayı seçerseniz, kasayı yapılandırmanıza ve Oluştur ' a tıklaetmenize yardımcı olması için aşağıdaki tabloyu kullanın. App Service uygulamanızla aynı abonelik ve kaynak grubu içinde yeni Key Vault oluşturun.

| Ayar | Açıklama |
|-|-|
| Ad | Alfasayısal karakterler ve tireler için oluşan benzersiz bir ad. |
| Kaynak grubu | Öneri olarak, App Service sertifikanız ile aynı kaynak grubunu seçin. |
| Konum | App Service uygulamanızla aynı konumu seçin. |
| Fiyatlandırma katmanı | Bilgi için bkz. [Azure Key Vault fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/key-vault/). |
| Erişim ilkeleri| , Kasa kaynaklarına yönelik olan uygulamaları ve izin verilen erişimi tanımlar. Daha sonra, [Key Vault erişim Ilkesi atama](../key-vault/general/assign-access-policy-portal.md)içindeki adımları izleyerek yapılandırabilirsiniz. |
| Sanal ağ erişimi | Belirli Azure sanal ağlarına kasa erişimini kısıtlayın. Daha sonra yapılandırma [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](../key-vault/general/network-security.md) içindeki adımları izleyerek yapılandırabilirsiniz |

Kasayı seçtikten sonra **Key Vault deposu** sayfasını kapatın. **1. Adım: Store** seçeneğinde başarılı olması için yeşil bir onay işareti gösterilmelidir. Sonraki adımda sayfayı açık tutun.

### <a name="verify-domain-ownership"></a>Etki alanı sahipliğini doğrulama

Son adımda kullandığınız **sertifika yapılandırma** sayfasından **2. Adım: doğrulama**' ya tıklayın.

![App Service sertifikası için etki alanını doğrulama](./media/configure-ssl-certificate/verify-domain.png)

**App Service doğrulaması**' nı seçin. Etki alanını Web uygulamanızla eşleştirdikten (bkz. [Önkoşullar](#prerequisites)), zaten doğrulandı. Bu adımı bitirebilmesi için **Doğrula** 'ya tıklamanız yeterlidir. İleti **sertifikası etki alanı doğrulanana** kadar **Yenile** düğmesine tıklayın.

> [!NOTE]
> Dört tür etki alanı doğrulama yöntemi desteklenir: 
> 
> - **App Service** -etki alanı aynı abonelikte bir App Service uygulamasına zaten eşlendiğinde en kullanışlı seçenektir. App Service uygulamasının etki alanı sahipliğini zaten doğruladığından emin olma özelliğinden yararlanır.
> - **Etki alanı** - [Azure 'dan satın aldığınız App Service etki alanını](manage-custom-dns-buy-domain.md)doğrulayın. Azure, doğrulama TXT kaydını sizin için otomatik olarak ekler ve işlemi tamamlar.
> - **Posta** -etki alanı yöneticisine bir e-posta göndererek etki alanını doğrulayın. Seçeneğini belirlediğinizde yönergeler sağlanır.
> - **El ile** -bir HTML sayfası (yalnızca **Standart** SERTIFIKA) veya bir DNS TXT kaydı kullanarak etki alanını doğrulayın. Seçeneğini belirlediğinizde yönergeler sağlanır.

### <a name="import-certificate-into-app-service"></a>Sertifikayı App Service içeri aktar

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden **uygulama hizmetleri**' ni seçin  >  **\<app-name>** .

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları**  >  **özel anahtar sertifikaları (. pfx)**  >  **içeri aktarma App Service sertifikası** seçin.

![App Service App Service sertifikayı içeri aktar](./media/configure-ssl-certificate/import-app-service-cert.png)

Yeni satın aldığınız sertifikayı seçin ve **Tamam**' ı seçin.

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz.

![App Service sertifikası alma tamamlandı](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de bir sertifika bağlaması oluşturmanız gerekir. [Bağlama oluşturma](configure-ssl-bindings.md#create-binding)bölümündeki adımları izleyin.
>

## <a name="import-a-certificate-from-key-vault"></a>Key Vault bir sertifikayı içeri aktar

Sertifikalarınızı yönetmek için Azure Key Vault kullanıyorsanız, [gereksinimleri karşılayan](#private-certificate-requirements)sürece Key Vault bir PKCS12 sertifikasını App Service 'e aktarabilirsiniz.

### <a name="authorize-app-service-to-read-from-the-vault"></a>App Service kasadan okumak üzere yetkilendir
Varsayılan olarak App Service kaynak sağlayıcısı Key Vault erişimine sahip değildir. Bir sertifika dağıtımı için Key Vault kullanabilmeniz için, [anahtar kasasında kaynak sağlayıcısına okuma erişimi yetkisi](../key-vault/general/assign-access-policy-cli.md)vermeniz gerekir. 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  , App Service için kaynak sağlayıcısı hizmet asıl adıdır ve tüm Azure abonelikleri için aynıdır. Azure Kamu bulut ortamı için `6a02c803-dafd-4136-b4c3-5a6f318b4714` kaynak sağlayıcısı hizmet sorumlusu adı olarak kullanın.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Kasanızdan bir sertifikayı uygulamanıza aktarma

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden **uygulama hizmetleri**' ni seçin  >  **\<app-name>** .

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları**  >  **özel anahtar sertifikaları (. pfx)**  >  **Key Vault sertifikayı içeri aktar**' ı seçin.

![App Service Key Vault sertifikayı içeri aktar](./media/configure-ssl-certificate/import-key-vault-cert.png)

Sertifikayı seçmenize yardımcı olması için aşağıdaki tabloyu kullanın.

| Ayar | Açıklama |
|-|-|
| Abonelik | Key Vault ait olduğu abonelik. |
| Key Vault | İçeri aktarmak istediğiniz sertifikayı içeren kasa. |
| Sertifika | Kasadaki PKCS12 sertifikaları listesinden seçim yapın. Kasadaki tüm PKCS12 sertifikaları, parmak izleriyle birlikte listelenir, ancak App Service tümünde desteklenmez. |

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz. İçeri aktarma işlemi bir hatayla başarısız olursa, sertifika [App Service gereksinimlerini](#private-certificate-requirements)karşılamıyor.

![Key Vault sertifikası alma tamamlandı](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Sertifikanızı yeni bir sertifikayla Key Vault güncelleştirirseniz, App Service sertifikayı 24 saat içinde otomatik olarak eşitler.

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de bir sertifika bağlaması oluşturmanız gerekir. [Bağlama oluşturma](configure-ssl-bindings.md#create-binding)bölümündeki adımları izleyin.
>

## <a name="upload-a-private-certificate"></a>Özel bir sertifikayı karşıya yükle

Sertifika sağlayıcınızdan bir sertifika edindikten sonra, bu bölümdeki adımları izleyerek App Service için hazırlayın.

### <a name="merge-intermediate-certificates"></a>Ara sertifikaları birleştirme

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

### <a name="export-certificate-to-pfx"></a>Sertifikayı PFX dosyasına aktarma

Birleştirilmiş TLS/SSL sertifikanızı, Sertifika isteğinizin oluşturulduğu özel anahtarla dışarı aktarın.

Sertifika isteğinizi OpenSSL kullanarak oluşturduysanız bir özel anahtar dosyası oluşturduğunuz anlamına gelir. Sertifikanızı PFX dosyasına aktarmak için aşağıdaki komutu çalıştırın. _&lt; Özel anahtar dosyası>_ ve _&lt; birleştirilmiş-sertifika-dosya>_ yer tutucuları özel anahtarınıza ve birleştirilmiş sertifika dosyanıza yönelik yollarla değiştirin.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Sorulduğunda bir dışarı aktarma parolası tanımlayın. Daha sonra App Service için TLS/SSL sertifikanızı karşıya yüklerken bu parolayı kullanacaksınız.

Sertifika isteğinizi oluşturmak için IIS veya _Certreq.exe_ kullandıysanız, sertifikayı yerel makinenize yükleyin ve sonra [sertifikayı PFX’e aktarın](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Sertifikayı App Service karşıya yükle

Artık App Service sertifikayı karşıya yükleyin.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden **uygulama hizmetleri**' ni seçin  >  **\<app-name>** .

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları**  >  **özel anahtar sertifikaları (. pfx)**  >  **karşıya yükleme sertifikası**' nı seçin.

![App Service özel sertifikayı karşıya yükle](./media/configure-ssl-certificate/upload-private-cert.png)

**PFX Sertifika Dosyası**’nda PFX dosyanızı seçin. **Sertifika parolası** alanına PFX dosyasını dışa aktardığınızda oluşturduğunuz parolayı yazın. İşiniz bittiğinde **karşıya yükle**' ye tıklayın. 

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz.

![Karşıya yükleme sertifikası tamamlandı](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de bir sertifika bağlaması oluşturmanız gerekir. [Bağlama oluşturma](configure-ssl-bindings.md#create-binding)bölümündeki adımları izleyin.
>

## <a name="upload-a-public-certificate"></a>Ortak sertifikayı karşıya yükle

Ortak Sertifikalar *. cer* biçiminde desteklenir. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden **uygulama hizmetleri**' ni seçin  >  **\<app-name>** .

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları**  >  **genel Sertifikalar (. cer)**  >  **ortak anahtar sertifikasını karşıya yükle**' ye tıklayın.

**Ad** alanına sertifika için bir ad yazın. **Cer sertifika dosyası**' nda, cer dosyanızı seçin.

**Karşıya Yükle**'ye tıklayın.

![App Service ortak sertifikayı karşıya yükle](./media/configure-ssl-certificate/upload-public-cert.png)

Sertifika karşıya yüklendikten sonra, sertifika parmak izini kopyalayın ve [sertifikayı erişilebilir yapın](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>App Service sertifikalarını yönetme

Bu bölümde, [App Service sertifikasını Içeri aktarma](#import-an-app-service-certificate)bölümünde satın aldığınız App Service sertifikasını nasıl yöneteceğiniz gösterilmektedir.

- [Sertifikayı yeniden anahtarlama](#rekey-certificate)
- [Sertifikayı Yenile](#renew-certificate)
- [Sertifikayı dışarı aktarma](#export-certificate)
- [Sertifikayı Sil](#delete-certificate)

### <a name="rekey-certificate"></a>Sertifikayı yeniden anahtarlama

Sertifikanızın özel anahtarının tehlikede olduğunu düşünüyorsanız, sertifikanızı yeniden anahtarınıza dönüştürebilirsiniz. [App Service sertifikaları](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından sol gezinmede yeniden **anahtarlama ve eşitleme** ' yi seçin.

İşlemi başlatmak için yeniden **anahtarlama** ' ye tıklayın. Bu işlemin tamamlanması 1-10 dakika sürebilir.

![App Service sertifikasını yeniden anahtarlama](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Sertifikanızın yeniden oluşturulması sertifikayı sertifika yetkilisinden verilen yeni bir sertifikayla kaydeder.

Yeniden anahtarlama işlemi tamamlandıktan sonra **Eşitle**' ye tıklayın. Eşitleme işlemi, uygulamalarınıza herhangi bir kapalı kalma süresine neden olmadan App Service içindeki sertifika için konak bağlamalarını otomatik olarak güncelleştirir.

> [!NOTE]
> **Eşitle**' yi tıklamıyorsanız, App Service sertifikanızı 24 saat içinde otomatik olarak eşitler.

### <a name="renew-certificate"></a>Sertifikayı Yenile

İstediğiniz zaman sertifikanızın otomatik yenilenmesini açmak için [App Service sertifikalar](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından sol gezinti bölmesinde **Ayarları otomatik Yenile** ' ye tıklayın. App Service sertifikaların varsayılan olarak bir yıllık geçerlilik süresi vardır.

**Açık** ' i seçin ve **Kaydet**' e tıklayın. Otomatik yenileme açıksa sertifikalar, süresi dolmadan 30 gün önce otomatik olarak yenileme başlatabilir.

![App Service sertifikayı otomatik olarak Yenile](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Bunun yerine sertifikayı el ile yenilemek için **El Ile Yenile**' ye tıklayın. Sertifikanızın süresi dolmadan 60 gün önce el ile yenileme isteğinde bulunabilir.

Yenileme işlemi tamamlandıktan sonra **Eşitle**' ye tıklayın. Eşitleme işlemi, uygulamalarınıza herhangi bir kapalı kalma süresine neden olmadan App Service içindeki sertifika için konak bağlamalarını otomatik olarak güncelleştirir.

> [!NOTE]
> **Eşitle**' yi tıklamıyorsanız, App Service sertifikanızı 24 saat içinde otomatik olarak eşitler.

### <a name="export-certificate"></a>Sertifikayı dışarı aktarma

Bir App Service Sertifikası [Key Vault gizli](../key-vault/general/about-keys-secrets-certificates.md)dizi olduğundan, bunun bir PFX kopyasını dışarı aktarabilir ve diğer Azure hizmetleri veya Azure dışında kullanabilirsiniz.

App Service Sertifikası PFX dosyası olarak dışarı aktarmak için, [Cloud Shell](https://shell.azure.com)aşağıdaki komutları çalıştırın. Ayrıca, [Azure CLI 'yi yüklediyseniz](/cli/azure/install-azure-cli)yerel olarak da çalıştırabilirsiniz. Yer tutucuları [App Service sertifikayı](#start-certificate-order)oluştururken kullandığınız adlarla değiştirin.

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

İndirilen *appservicecertificate. pfx* dosyası, hem ortak hem de özel sertifikaları içeren BIR ham PKCS12 dosyasıdır. Her sorulduğunda, Import Password ve pek Pass ifadesi için boş bir dize kullanın.

### <a name="delete-certificate"></a>Sertifikayı Sil 

App Service sertifikasını silme işlemi son ve geri alınamaz. App Service Sertifikası bir kaynağın silinmesi, sertifikanın iptal edilmesini neden olur. Bu sertifikayla App Service olan herhangi bir bağlama geçersiz hale gelir. Yanlışlıkla silinmeye engel olmak için, Azure, sertifikaya bir kilit koyar. Bir App Service sertifikasını silmek için, önce sertifikadaki silme kilidini kaldırmanız gerekir.

[App Service sertifikaları](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından sol gezinti bölmesinde **kilitler** ' ı seçin.

Kilit türü **silme** ile sertifikanıza yönelik kilidi bulun. Sağ tarafta **Sil**' i seçin.

![App Service sertifikası için kilidi Sil](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Artık App Service sertifikasını silebilirsiniz. Sol gezinmede **genel bakış**  >  **Sil**' i seçin. Onay iletişim kutusunda, sertifika adını yazın ve **Tamam**' ı seçin.

## <a name="automate-with-scripts&quot;></a>Betiklerle otomatikleştirme

### <a name=&quot;azure-cli&quot;></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 &quot;Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Diğer kaynaklar

* [Azure App Service 'de TLS/SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
* [HTTPS'yi zorunlu tutma](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 zorlama](configure-ssl-bindings.md#enforce-tls-versions)
* [Azure App Service'te kodunuzda TLS/SSL sertifikası kullanma](configure-ssl-certificate-in-code.md)
* [SSS: sertifikalar App Service](./faq-configuration-and-management.md)
