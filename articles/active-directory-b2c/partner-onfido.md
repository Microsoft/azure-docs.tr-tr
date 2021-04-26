---
title: Onfido ile Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Belge KIMLIĞI ve yüz Biyometri doğrulaması için Onfido ile Azure AD B2C kimlik doğrulamasını tümleştirme hakkında bilgi edinin
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 1441210303984bf777c6b580f6f7ab0c67ce6f21
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257866"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile Onfido yapılandırma öğreticisi

Bu örnek öğreticide, Azure AD B2C [Onfido](https://onfido.com/)ile tümleştirme hakkında rehberlik sağlıyoruz. Onfido, bir belge KIMLIĞI ve yüz Biyometri doğrulama uygulamasıdır. Şirketlerin *Müşteri* ve kimlik gereksinimlerinizi gerçek zamanlı olarak karşılamasına olanak sağlar. Onfido, önce bir fotoğraf KIMLIĞINI doğrulayan ve sonra yüz biyometri ile eşleşen, ayrıntılı AI tabanlı kimlik doğrulaması kullanır. Bu çözüm, gerçek dünyalarına dijital bir kimlik sağlar ve dolandırıcılığın azaltılması sırasında güvenli bir ekleme deneyimi sağlar.

Bu örnekte, kimlik doğrulaması yapmak için kaydolma veya oturum açma akışındaki Onfido 'ın hizmetini bağlayacağız. Kullanıcının hangi ürün ve hizmete erişebileceği hakkında bilinçli kararlar, Onfido 'ın sonuçlarına göre yapılır.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](./tutorial-create-tenant.md) .

- Bir Onfido [deneme hesabı](https://onfido.com/signup/).

## <a name="scenario-description"></a>Senaryo açıklaması

Onfido tümleştirmesi aşağıdaki bileşenleri içerir:

- Azure AD B2C kiracı – yetkilendirme sunucusu, Kullanıcı kimlik bilgilerinin kiracıda tanımlı özel ilkelere göre doğrulanmasından sorumludur. Kimlik sağlayıcısı olarak da bilinir. Kullanıcı belgelerini toplayan ve Onfido API hizmetine ileten Onfido istemci uygulamasını barındırır.

- Onfido istemcisi: diğer Web sayfaları içinde dağıtılan yapılandırılabilir bir JavaScript istemci belge koleksiyonu yardımcı programı. Belgeleri toplar ve belge boyutu ve kalitesi gibi ön denetimleri yapar.

- Ara REST API 'SI – Azure AD B2C kiracının Onfido API hizmetiyle iletişim kurması, veri işlemeyi işlemesi ve her ikisinin de güvenlik gereksinimlerine uymak için uç noktalar sağlar.

- Onfido API hizmeti – kullanıcı tarafından sunulan belgeleri kaydeden ve doğrulayan Onfido tarafından sunulan arka uç hizmeti.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![onfido mimarisi için ekran görüntüsü-diyagram](media/partner-onfido/onfido-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı bir oturum açma sayfasına ulaştı. Kullanıcı, yeni bir hesap oluşturmak için kaydolun ve sayfaya bilgi girer. Azure AD B2C Kullanıcı özniteliklerini toplar. Azure AD B2C barındırılan onfido istemci uygulaması, Kullanıcı bilgilerini kontrol eder.
| 2. | Azure AD B2C orta katman API 'sini çağırır ve Kullanıcı özniteliklerine geçirir.
| 3. | Orta katman API 'SI Kullanıcı özniteliklerini toplar ve Onfido API 'sinin tüketebileceği bir biçime dönüştürür. Ardından, onu Onfido 'ya gönderir.
| 4. | Onfido, bilgileri kullanır ve Kullanıcı kimliğini doğrulamak için onu işler. Ardından, sonucu orta katman API 'sine döndürür.
| 5. | Orta katman API 'SI bilgileri işler ve ilgili bilgileri doğru JSON biçiminde geri gönderir Azure AD B2C.
| 6. | Azure AD B2C, bilgileri ortadaki katman API 'sinden geri alır. Hata yanıtı gösteriyorsa, kullanıcıya bir hata iletisi görüntülenir. Başarılı bir yanıt gösteriyorsa, kullanıcının kimliği doğrulanır ve dizine yazılır.

## <a name="onboard-with-onfido"></a>Onfido ile ekleme

1. Bir Onfido hesabı oluşturmak için [Onfido](https://onfido.com/signup/)ile iletişim kurun.

2. Bir hesap oluşturulduktan sonra bir [API anahtarı](https://documentation.onfido.com/)oluşturun. Ancak gerçek zamanlı anahtarlar faturalandırılabilir, ancak çözümü [test etmek için Sandbox anahtarlarını](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) kullanabilirsiniz. Korumalı alan anahtarları, canlı anahtarlarla aynı sonuç yapısını üretir, ancak sonuçlar her zaman önceden belirlenir. Belgeler işlenmez veya kaydedilmez.

>[!NOTE]
> Daha sonra anahtara ihtiyacınız olacak.

Onfido hakkında daha fazla bilgi için bkz. [ONFIDO API belgeleri](https://documentation.onfido.com) ve [Onfido Geliştirici Merkezi](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Onfido ile Azure AD B2C yapılandırma

### <a name="part-1---deploy-the-api"></a>1. Bölüm-API 'YI dağıtma

- Belirtilen [API kodunu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) bir Azure hizmetine dağıtın. Kod, bu [yönergeleri](/visualstudio/deployment/quickstart-deploy-to-azure)Izleyerek Visual Studio 'dan yayımlanabilir.
- Kurulum CORS, **Izin verilen kaynağı** https://{your_tenant_name}. b2clogin. com olarak ekleyin

>[!NOTE]
>Azure AD 'yi gerekli ayarlarla yapılandırmak için dağıtılan hizmetin URL 'sine ihtiyacınız vardır.

#### <a name="adding-sensitive-configuration-settings"></a>Hassas yapılandırma ayarları ekleme

Uygulama ayarları [Azure 'Daki App Service](../app-service/configure-common.md#configure-app-settings)'te yapılandırılabilir. App Service, ayarların bir depoya denetlenmeden güvenli bir şekilde yapılandırılmasını sağlar. REST API 'SI aşağıdaki ayarlara ihtiyaç duyuyor:

| Uygulama ayarı adı | Kaynak | Notlar |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Onfido hesabı |

### <a name="part-2---deploy-the-ui"></a>Bölüm 2-Kullanıcı arabirimini dağıtma

#### <a name="configure-your-storage-location"></a>Depolama konumunuzu yapılandırın

1. [Depolama hesabınızda bir BLOB depolama kapsayıcısı](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) ayarlayın

2. Kullanıcı arabirimi dosyalarını [Kullanıcı arabirimi klasöründen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) blob kapsayıcınıza depolayın.

3. Aşağıdaki yönergeleri izleyerek, oluşturduğunuz depolama kapsayıcısına CORS erişimi sağlar:

   a. **Ayarlar**  > **izin verildi kaynağına** gidin, girin `https://{your_tenant_name}.b2clogin.com` . Kiracı adınızı Azure AD B2C kiracınızın adıyla değiştirin. Örneğin, https://fabrikam.b2clogin.com . Kiracı adınızı girerken tüm küçük harfleri kullanın.

   b. **Izin verilen Yöntemler** için `GET` ve seçin `PUT` .

   c. **Kaydet**’i seçin.

#### <a name="update-ui-files"></a>UI dosyalarını Güncelleştir

1. Kullanıcı arabirimi dosyalarında ocean_blue klasöre gidin. [ ](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Her bir HTML dosyasını açın.

3. {Size-UI-blob-Container-URL} ' i, UI **ocean_blue**, **dağ** ve **varlıklar** klasörlerinizin bulunduğu URL ile bulur ve değiştirin

4. {The-ara-api-URL}} öğesini ara API App Service 'in URL 'siyle bulun ve değiştirin.

#### <a name="upload-your-files"></a>Dosyalarınızı karşıya yükleyin

1. Kullanıcı arabirimi dosyalarını Kullanıcı arabirimi klasöründen blob kapsayıcınıza depolayın.

2. Dosyalarınızı ve erişim izinlerinizi yönetmek için [Azure Depolama Gezgini](../virtual-machines/disks-use-storage-explorer-managed-disks.md) kullanın.

### <a name="part-3---configure-azure-ad-b2c"></a>Bölüm 3-Azure AD B2C yapılandırma

#### <a name="replace-the-configuration-values"></a>Yapılandırma değerlerini Değiştir

Belirtilen [özel ilkelerde](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies), aşağıdaki yer tutucuları bulun ve örneğinizi karşılık gelen değerlerle değiştirin.

| Yer tutucu | Değer ile Değiştir | Örnek  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Kiracınızın kısa adı |  yourtenant.onmicrosoft.com 'dan "kiracınız" |
| {your_tenantID} | Azure AD B2C kiracınızın Tenantıd | 01234567-89ab-cdef-0123-456789ABCDEF           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C kiracınızda yapılandırılan IdentityExperienceFramework uygulamasının uygulama KIMLIĞI      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C kiracınızda yapılandırılan ProxyIdentityExperienceFramework uygulamasının uygulama KIMLIĞI | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | Kiracının depolama uygulamasının uygulama KIMLIĞI                                      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | Kiracınızın depolama uygulamasının nesne KIMLIĞI                                   | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_app_insights_instrumentation_key} | App Insights örneğiniz için izleme anahtarı *| 01234567-89ab-cdef-0123-456789ABCDEF|
|{your_ui_file_base_url}| UI **ocean_blue**, **dağ** ve **varlıklar** klasörlerinizin bulunduğu konumun URL 'si | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | Ayarladığınız App Service 'in URL 'SI                                             | `https://yourapp.azurewebsites.net`          |

* App Insights farklı bir kiracıda olabilir. Bu adım isteğe bağlıdır. Gerekli değilse, ilgili teknisyen ve düzenleme adımlarını kaldırın.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Bölüm 4-Azure AD B2C ilkesini yapılandırma

Azure AD B2C kiracınızı ayarlama ve ilkeleri yapılandırma hakkında yönergeler için bu [belgeye](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) başvurun.

>[!NOTE]
> En iyi uygulama olarak, müşterilerin öznitelik koleksiyonu sayfasına onay bildirimi eklemesini öneririz. Kimlik doğrulaması için bilgilerin üçüncü taraf hizmetlere gönderileceğini kullanıcılara bildirin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın ve Ilkeler altında **kimlik deneyimi çerçevesi**' ni seçin.

2. Daha önce oluşturduğunuz **Signupsignın**' ı seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ayarları seçin:

   a. **Uygulama**: kayıtlı uygulamayı seçin (örnek JWT)

   b. **Yanıt URL 'si**: **yeniden yönlendirme URL** 'sini seçin

   c. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışına gidin ve hesap oluşturun

5. Kullanıcı özniteliği oluşturulduktan sonra, akış sırasında onfido hizmeti çağrılacaktır. Akış tamamlanmamışsa, kullanıcının dizine kaydedilip kaydedilmediğini kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
