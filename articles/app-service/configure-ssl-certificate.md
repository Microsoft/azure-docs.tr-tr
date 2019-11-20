---
title: SSL sertifikaları ekleme ve yönetme-Azure App Service | Microsoft Docs
description: App Service sertifikasını satın almayı ve App Service uygulamanıza bağlamayı öğrenin
services: app-service
author: cephalin
manager: gwallace
tags: buy-ssl-certificates
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 48c8390eff52466d11f781447c448d04ba567f31
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907131"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>Azure App Service bir SSL sertifikası Ekle

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu makalede, App Service ' ye özel bir sertifika oluşturma, yükleme veya alma işleminin nasıl yapılacağı gösterilir. 

Sertifika, App Service uygulamanıza veya [işlev uygulamanıza](https://docs.microsoft.com/azure/azure-functions/)eklendikten sonra [özel bir DNS adının güvenliğini](configure-ssl-bindings.md) sağlayabilir veya [uygulama kodunuzda kullanabilirsiniz](configure-ssl-certificate-in-code.md).

Aşağıdaki tabloda App Service sertifika eklemek için sahip olduğunuz seçenekler listelenmektedir:

|Seçenek|Açıklama|
|-|-|
| App Service yönetilen ücretsiz sertifika oluşturma (Önizleme) | Yalnızca `www` [özel etki](app-service-web-tutorial-custom-domain.md) alanınızı veya App Service herhangi bir çıplak etki alanını güvenli hale getirmeniz gerekiyorsa, kullanımı kolay olan özel bir sertifika. |
| App Service sertifikası satın alma | Azure tarafından yönetilen özel bir sertifika. Otomatik sertifika yönetiminin basitliğini ve yenileme ve dışa aktarma seçeneklerinin esnekliğini birleştirir. |
| Key Vault bir sertifikayı içeri aktar | [PKCS12 sertifikalarınızı](https://wikipedia.org/wiki/PKCS_12)yönetmek için [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) kullanıyorsanız faydalıdır. Bkz. [özel sertifika gereksinimleri](#private-certificate-requirements). |
| Özel bir sertifikayı karşıya yükle | Bir üçüncü taraf sağlayıcıdan zaten özel bir sertifikanız varsa, bu sertifikaya yükleyebilirsiniz. Bkz. [özel sertifika gereksinimleri](#private-certificate-requirements). |
| Ortak sertifikayı karşıya yükle | Ortak sertifikalar özel etki alanlarını güvenli hale getirmek için kullanılmaz, ancak uzak kaynaklara erişmesi gerekiyorsa bunları kodunuza yükleyebilirsiniz. |

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu izlemek için:

- [App Service uygulaması oluşturun](/azure/app-service/).
- Yalnızca ücretsiz sertifika: bir alt etki alanını (örneğin, `www.contoso.com`) bir [CNAME kaydıyla](app-service-web-tutorial-custom-domain.md#map-a-cname-record)App Service eşleyin.

## <a name="private-certificate-requirements"></a>Özel sertifika gereksinimleri

[Ücretsiz App Service yönetilen sertifika](#create-a-free-certificate-preview) veya [App Service sertifikası](#import-an-app-service-certificate) App Service gereksinimlerini zaten karşılamış. App Service bir özel sertifikayı karşıya yüklemeyi veya aktarmayı seçerseniz, sertifikanızın aşağıdaki gereksinimleri karşılaması gerekir:

* [Parola KORUMALı pfx dosyası](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions) olarak verildi
* En az 2048 bit uzunluğunda özel anahtar içermesi
* Sertifika zincirindeki tüm ara sertifikaları içermesi

Bir SSL bağlamasındaki özel bir etki alanının güvenliğini sağlamak için, sertifikanın ek gereksinimleri vardır:

* Sunucu kimlik doğrulaması için bir [genişletilmiş anahtar kullanımı](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) IÇERIR (OID = 1.3.6.1.5.5.7.3.1)
* Güvenilir bir sertifika yetkilisi tarafından imzalanması

> [!NOTE]
> **Eliptik Eğri Şifrelemesi (ECC) sertifikaları**, App Service ile birlikte çalışabilir ancak bu makalenin konusu değildir. ECC sertifikaları oluşturmaya ilişkin tam adımlar için sertifika yetkilinizle birlikte çalışın.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Ücretsiz sertifika oluşturma (Önizleme)

App Service yönetilen ücretsiz sertifika, App Service özel DNS adınızı güvenli hale getirmek için bir açılan anahtar çözümüdür. Bu, App Service tarafından yönetilen ve otomatik olarak yenilenen tam işlevli bir SSL sertifikasıdır. Ücretsiz sertifika aşağıdaki sınırlamalara sahiptir:

- Joker sertifikaları desteklemez.
- Çıplak etki alanlarını desteklemez.
- Dışarı aktarılabilir değil.

> [!NOTE]
> Ücretsiz sertifika, DigiCert tarafından verilir. Bazı üst düzey etki alanları için, şu değere sahip bir [CAA etki alanı kaydı](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) oluşturarak bir sertifika veren olarak DigiCert 'e açıkça izin vermelisiniz: `0 issue digicert.com`.
> 

App Service yönetilen ücretsiz bir sertifika oluşturmak için:

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden app **Services** >  **\<app-name >** ' ı seçin.

Uygulamanızın sol gezinti bölmesinde, **App Service yönetilen sertifika oluşturmak** > **özel anahtar sertifikaları (. pfx)**  > **TLS/SSL ayarları** ' nı seçin.

![App Service 'de ücretsiz sertifika oluşturma](./media/configure-ssl-certificate/create-free-cert.png)

Bir CNAME kaydıyla uygulamanızla doğru şekilde eşlenmiş, çıplak olmayan herhangi bir etki alanı iletişim kutusunda listelenir. İçin ücretsiz bir sertifika oluşturmak üzere özel etki alanını seçin ve **Oluştur**' u seçin. Desteklenen her bir özel etki alanı için yalnızca bir sertifika oluşturabilirsiniz.

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz.

![Ücretsiz sertifika oluşturma tamamlandı](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de bir sertifika bağlaması oluşturmanız gerekir. [Bağlama oluşturma](configure-ssl-bindings.md#create-binding)bölümündeki adımları izleyin.
>

## <a name="import-an-app-service-certificate"></a>App Service Sertifikası içeri aktarma

Azure 'dan bir App Service Sertifikası satın alırsanız Azure aşağıdaki görevleri yönetir:

- GoDaddy adresinden satın alma sürecini ele alır.
- Sertifika için etki alanı doğrulaması gerçekleştirir.
- [Azure Key Vault](../key-vault/key-vault-overview.md)' de sertifikayı korur.
- Sertifika yenilemeyi yönetir (bkz. [Sertifikayı Yenile](#renew-certificate)).
- Sertifikayı App Service uygulamalardaki içeri aktarılan kopyalarla otomatik olarak eşitler.

Bir App Service sertifikası satın almak için, [Başlangıç sertifikası siparişi](#start-certificate-order)' ne gidin.

Zaten çalışan bir App Service sertifikanız varsa şunları yapabilirsiniz:

- [Sertifikayı App Service Içine aktarın](#import-certificate-into-app-service).
- [Sertifikayı](#manage-app-service-certificates), yenileme, yeniden anahtarlama ve dışarı aktarma gibi yönetin.

### <a name="start-certificate-order"></a>Sertifika sırasını Başlat

<a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service sertifikası oluştur sayfasında</a>bir App Service sertifikası siparişi başlatın.

![App Service sertifikası satın almayı Başlat](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Sertifikayı yapılandırmanıza yardımcı olması için aşağıdaki tabloyu kullanın. Tamamladığınızda **Oluştur**’a tıklayın.

| Ayar | Açıklama |
|-|-|
| Ad | App Service sertifikanız için kolay bir ad. |
| Naked etki alanı konak adı | Kök etki alanını burada belirtin. Verilen sertifika hem kök etki alanının hem *de* `www` alt etki alanının güvenliğini sağlar. Verilen sertifikada, ortak ad alanı kök etki alanını içerir ve konu alternatif adı alanı `www` etki alanını içerir. Yalnızca herhangi bir alt etki alanının güvenliğini sağlamak için burada alt etki alanının tam etki alanı adını belirtin (örneğin, `mysubdomain.contoso.com`).|
| Abonelik | Web uygulamasının barındırıldığı veri merkezi. |
| Kaynak grubu | Sertifikayı içeren kaynak grubu. Örneğin, yeni bir kaynak grubu kullanabilir veya App Service uygulamanız ile aynı kaynak grubunu seçebilirsiniz. |
| Sertifika SKU 'SU | Oluşturulacak sertifikanın türünü, standart bir sertifika veya [Joker bir sertifika](https://wikipedia.org/wiki/Wildcard_certificate)olduğunu belirler. |
| Yasal koşullar | Yasal koşulları kabul ettiğinizi onaylamak için tıklayın. Sertifikalar GoDaddy adresinden elde edilir. |

### <a name="store-in-azure-key-vault"></a>Azure Key Vault içinde depola

Sertifika satın alma işlemi tamamlandıktan sonra, bu sertifikayı kullanmaya başlayabilmeniz için yapmanız gereken birkaç adım daha vardır. 

[App Service sertifikaları](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından **sertifika > yapılandırması** ' na tıklayın. **Adım 1: depolama**' ya tıklayın.

![App Service sertifikası Key Vault depolamayı yapılandırma](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) , bulut uygulamaları ve Hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olan bir Azure hizmetidir. Bu, App Service sertifikaları için tercih edilen depolardır.

**Key Vault durum** sayfasında, yeni bir kasa oluşturmak veya mevcut bir kasayı seçmek Için **Key Vault deposu** ' na tıklayın. Yeni bir kasa oluşturmayı seçerseniz, kasayı yapılandırmanıza ve Oluştur ' a tıklaetmenize yardımcı olması için aşağıdaki tabloyu kullanın. aynı abonelik ve kaynak grubu içinde yeni Key Vault oluşturmak için bkz.

| Ayar | Açıklama |
|-|-|
| Ad | Alfasayısal karakterler ve tireler için oluşan benzersiz bir ad. |
| Kaynak grubu | Öneri olarak, App Service sertifikanız ile aynı kaynak grubunu seçin. |
| Konum | App Service uygulamanızla aynı konumu seçin. |
| Fiyatlandırma katmanı | Bilgi için bkz. [Azure Key Vault fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/key-vault/). |
| Erişim ilkeleri| , Kasa kaynaklarına yönelik olan uygulamaları ve izin verilen erişimi tanımlar. Daha sonra, [birkaç uygulamaya bir anahtar kasasına erişim verme](../key-vault/key-vault-group-permissions-for-apps.md)adımlarını izleyerek daha sonra yapılandırabilirsiniz. |
| Sanal ağ erişimi | Belirli Azure sanal ağlarına kasa erişimini kısıtlayın. Daha sonra yapılandırma [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](../key-vault/key-vault-network-security.md) içindeki adımları izleyerek yapılandırabilirsiniz |

Kasayı seçtikten sonra **Key Vault deposu** sayfasını kapatın. **Mağaza** seçeneğinde, başarılı için yeşil bir onay işareti gösterilmelidir. Sonraki adımda sayfayı açık tutun.

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
> - **El ile** -bir HTML sayfası (yalnızca**Standart** SERTIFIKA) veya bir DNS TXT kaydı kullanarak etki alanını doğrulayın. Seçeneğini belirlediğinizde yönergeler sağlanır.

### <a name="import-certificate-into-app-service"></a>Sertifikayı App Service içeri aktar

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden app **Services** >  **\<app-name >** ' ı seçin.

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları** > **özel anahtar sertifikaları (. pfx)**  > **App Service sertifikası içeri aktarma**' yı seçin.

![App Service App Service sertifikayı içeri aktar](./media/configure-ssl-certificate/import-app-service-cert.png)

Yeni satın aldığınız sertifikayı seçin ve **Tamam**' ı seçin.

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz.

![App Service sertifikası alma tamamlandı](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de bir sertifika bağlaması oluşturmanız gerekir. [Bağlama oluşturma](configure-ssl-bindings.md#create-binding)bölümündeki adımları izleyin.
>

## <a name="import-a-certificate-from-key-vault"></a>Key Vault bir sertifikayı içeri aktar

Sertifikalarınızı yönetmek için Azure Key Vault kullanıyorsanız, [gereksinimleri karşılayan](#private-certificate-requirements)sürece Key Vault bir PKCS12 sertifikasını App Service 'e aktarabilirsiniz.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden app **Services** >  **\<app-name >** ' ı seçin.

Uygulamanızın sol gezinti bölmesinde, **Key Vault sertifikasını Içeri aktarma** > **özel anahtar sertifikaları (. pfx)**  > **TLS/SSL ayarları** ' nı seçin.

![App Service Key Vault sertifikayı içeri aktar](./media/configure-ssl-certificate/import-key-vault-cert.png))

Sertifikayı seçmenize yardımcı olması için aşağıdaki tabloyu kullanın.

| Ayar | Açıklama |
|-|-|
| Abonelik | Key Vault ait olduğu abonelik. |
| Key Vault | İçeri aktarmak istediğiniz sertifikayı içeren kasa. |
| Sertifika | Kasadaki PKCS12 sertifikaları listesinden seçim yapın. Kasadaki tüm PKCS12 sertifikaları, parmak izleriyle birlikte listelenir, ancak App Service tümünde desteklenmez. |

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz. İçeri aktarma işlemi bir hatayla başarısız olursa, sertifika [App Service gereksinimlerini](#private-certificate-requirements)karşılamıyor.

![Key Vault sertifikası alma tamamlandı](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

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

Birleştirilmiş SSL sertifikanızı sertifika isteğinizi oluşturmak için kullanılan özel anahtarla dışarı aktarın.

Sertifika isteğinizi OpenSSL kullanarak oluşturduysanız bir özel anahtar dosyası oluşturduğunuz anlamına gelir. Sertifikanızı PFX dosyasına aktarmak için aşağıdaki komutu çalıştırın. _&lt;private-key-file>_ ve _&lt;merged-certificate-file>_ yer tutucularını özel anahtarınızın ve birleştirdiğiniz sertifika dosyasının yollarıyla değiştirin.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Sorulduğunda bir dışarı aktarma parolası tanımlayın. Bu parolayı daha sonra SSL sertifikanızı App Service’e yüklerken kullanacaksınız.

Sertifika isteğinizi oluşturmak için IIS veya _Certreq.exe_ kullandıysanız, sertifikayı yerel makinenize yükleyin ve sonra [sertifikayı PFX’e aktarın](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Sertifikayı App Service karşıya yükle

Artık App Service sertifikayı karşıya yükleyin.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden app **Services** >  **\<app-name >** ' ı seçin.

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları** > **özel anahtar sertifikaları (. pfx)**  > **sertifikayı karşıya yükle**' yi seçin.

![App Service özel sertifikayı karşıya yükle](./media/configure-ssl-certificate/upload-private-cert.png)

**PFX Sertifika Dosyası**’nda PFX dosyanızı seçin. **Sertifika parolası** alanına PFX dosyasını dışa aktardığınızda oluşturduğunuz parolayı yazın. İşiniz bittiğinde **karşıya yükle**' ye tıklayın. 

İşlem tamamlandığında, sertifikayı **özel anahtar sertifikaları** listesinde görürsünüz.

![Karşıya yükleme sertifikası tamamlandı](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Özel bir etki alanını bu sertifikayla güvenli hale getirmek için yine de bir sertifika bağlaması oluşturmanız gerekir. [Bağlama oluşturma](configure-ssl-bindings.md#create-binding)bölümündeki adımları izleyin.
>

## <a name="upload-a-public-certificate"></a>Ortak sertifikayı karşıya yükle

Ortak Sertifikalar *. cer* biçiminde desteklenir. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden app **Services** >  **\<app-name >** ' ı seçin.

Uygulamanızın sol gezinti bölmesinde, genel **Sertifikalar (. cer)** ** >  > ** **ortak anahtar sertifikasını karşıya yükle**' ye tıklayın.

**Ad**alanına sertifika için bir ad yazın. **Cer sertifika dosyası**' nda, cer dosyanızı seçin.

**Karşıya Yükle**'ye tıklayın.

![App Service ortak sertifikayı karşıya yükle](./media/configure-ssl-certificate/upload-public-cert.png)

Sertifika karşıya yüklendikten sonra, sertifika parmak izini kopyalayın ve [sertifikayı erişilebilir yapın](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>App Service sertifikalarını yönetme

Bu bölümde, [App Service sertifikasını Içeri aktarma](#import-an-app-service-certificate)bölümünde satın aldığınız App Service sertifikasını nasıl yöneteceğiniz gösterilmektedir.

- [Sertifikayı yeniden anahtarlama](#rekey-certificate)
- [Sertifikayı Yenile](#renew-certificate)
- [Sertifikayı dışarı aktar](#export-certificate)
- [Sertifikayı Sil](#delete-certificate)

### <a name="rekey-certificate"></a>Sertifikayı yeniden anahtarlama

Sertifikanızın özel anahtarının tehlikede olduğunu düşünüyorsanız, sertifikanızı yeniden anahtarınıza dönüştürebilirsiniz. [App Service sertifikaları](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından sol gezinmede yeniden **anahtarlama ve eşitleme** ' yi seçin.

İşlemi başlatmak için yeniden **anahtarlama** ' ye tıklayın. Bu işlemin tamamlanması 1-10 dakika sürebilir.

![App Service sertifikasını yeniden anahtarlama](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Sertifikanızın yeniden oluşturulması sertifikayı sertifika yetkilisinden verilen yeni bir sertifikayla kaydeder.

Yeniden anahtarlama işlemi tamamlandıktan sonra **Eşitle**' ye tıklayın. Eşitleme işlemi, uygulamalarınıza herhangi bir kapalı kalma süresine neden olmadan App Service içindeki sertifika için konak bağlamalarını otomatik olarak güncelleştirir.

> [!NOTE]
> **Eşitle**' yi tıklamıyorsanız, App Service sertifikanızı otomatik olarak 48 saat içinde eşitler.

### <a name="renew-certificate"></a>Sertifikayı Yenile

İstediğiniz zaman sertifikanızın otomatik yenilenmesini açmak için [App Service sertifikalar](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından sol gezinti bölmesinde **Ayarları otomatik Yenile** ' ye tıklayın. App Service sertifikaların varsayılan olarak bir yıllık geçerlilik süresi vardır.

**Açık** ' i seçin ve **Kaydet**' e tıklayın. Otomatik yenileme açıksa sertifikalar, süresi dolmadan 60 gün önce otomatik olarak yenilenbaşlayabilir.

![App Service sertifikayı otomatik olarak Yenile](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Bunun yerine sertifikayı el ile yenilemek için **El Ile Yenile**' ye tıklayın. Sertifikanızın süresi dolmadan 60 gün önce el ile yenileme isteğinde bulunabilir.

Yenileme işlemi tamamlandıktan sonra **Eşitle**' ye tıklayın. Eşitleme işlemi, uygulamalarınıza herhangi bir kapalı kalma süresine neden olmadan App Service içindeki sertifika için konak bağlamalarını otomatik olarak güncelleştirir.

> [!NOTE]
> **Eşitle**' yi tıklamıyorsanız, App Service sertifikanızı otomatik olarak 48 saat içinde eşitler.

### <a name="export-certificate"></a>Sertifikayı dışarı aktarma

Bir App Service Sertifikası [Key Vault gizli](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)dizi olduğundan, bunun bir PFX kopyasını dışarı aktarabilir ve diğer Azure hizmetleri veya Azure dışında kullanabilirsiniz.

App Service Sertifikası PFX dosyası olarak dışarı aktarmak için, [Cloud Shell](https://shell.azure.com)aşağıdaki komutları çalıştırın. Ayrıca, [Azure CLI 'yi yüklediyseniz](https://docs.microsoft.com/cli/azure/install-azure-cli)yerel olarak da çalıştırabilirsiniz. Yer tutucuları [App Service sertifikayı](#start-certificate-order)oluştururken kullandığınız adlarla değiştirin.

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

App Service sertifikasını silme işlemi son ve geri alınamaz. Bu sertifikayla App Service olan herhangi bir bağlama geçersiz hale gelir. Yanlışlıkla silinmeye engel olmak için, Azure, sertifikaya bir kilit koyar. Bir App Service sertifikasını silmek için, önce sertifikadaki silme kilidini kaldırmanız gerekir.

[App Service sertifikaları](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) sayfasında sertifikayı seçin ve ardından sol gezinti bölmesinde **kilitler** ' ı seçin.

Kilit türü **silme**ile sertifikanıza yönelik kilidi bulun. Sağ tarafta **Sil**' i seçin.

![App Service sertifikası için kilidi Sil](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Artık App Service sertifikasını silebilirsiniz. Sol gezinmede **genel bakış** > **Sil**' i seçin. Onay iletişim kutusunda, sertifika adını yazın ve **Tamam**' ı seçin.

## <a name="automate-with-scripts"></a>Betiklerle otomatikleştirme

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Diğer kaynaklar

* [SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
* [HTTPS zorlama](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 'yi zorla](configure-ssl-bindings.md#enforce-tls-versions)
* [Uygulama kodunuzda bir SSL sertifikası kullanma](configure-ssl-certificate-in-code.md)
* [SSS: sertifikalar App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
