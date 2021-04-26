---
title: Microsoft Dynamics 365 sahtekarlık koruması ile Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Riskli ve sahte hesabı belirlemek için Microsoft Dynamics 365 sahtekarlık korumasıyla Azure Active Directory B2C yapılandırma öğreticisi
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8c9d760ed888eb194ad8f282f180a634e3c09538
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587825"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C ile Microsoft Dynamics 365 sahtekarlık korumasını yapılandırma

Bu örnek öğreticide, [Microsoft Dynamics 365 sahtekarlık korumasını](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (dfp) Azure ACTIVE DIRECTORY (ad) B2C ile tümleştirme hakkında rehberlik sağlıyoruz.

Microsoft DFP istemcilere yeni hesap oluşturma ve istemcinin ekosisteminde oturum açma girişimleri için deneme riski olup olmadığını değerlendirme yeteneği sağlar. Yeni sahte hesaplar oluşturmaya veya var olan hesapların güvenliğinin aşılmasına yönelik şüpheli girişimleri engellemek veya zorluk yapmak için Microsoft DFP değerlendirmesi, müşteri tarafından kullanılabilir. Hesap koruması, yapay zeka uyumlu cihaz parmak izlerini, gerçek zamanlı risk değerlendirmesi, kural ve liste deneyimi için API 'Leri, istemci iş gereksinimlerine göre risk stratejisini iyileştirmek için kural ve liste deneyimini ve istemci ekosistemindeki sahtekarlık koruma verimliliğini ve eğilimlerini izlemek için bir karneyi içerir.

Bu örnekte, Microsoft DFP 'nin hesap koruma özelliklerini bir Azure AD B2C Kullanıcı akışıyla tümleştireceğiz. Hizmet, tüm oturum açma veya kaydolma girişiminin dışarıdan parmak izini alacak ve geçmişteki veya mevcut şüpheli davranışlar için izleme. Azure AD B2C, Microsoft DFP 'den bir karar uç noktası çağırır, bu, tanımlı kullanıcıdan gelen ve mevcut davranışa ve ayrıca Microsoft DFP hizmeti içinde belirtilen özel kurallara göre bir sonuç döndürür. Azure AD B2C, bu sonuca göre onay kararı verir ve aynı geriye Microsoft DFP 'e geçirir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- [Azure AD B2C kiracısı](./tutorial-create-tenant.md). Kiracı, Azure aboneliğinize bağlı.

- Microsoft DFP [aboneliği](https://dynamics.microsoft.com/pricing/#Sales)alın. [Deneme sürümü istemci sürümünü](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) de ayarlayabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Microsoft DFP tümleştirmesi aşağıdaki bileşenleri içerir:

- **Azure AD B2C kiracı**: kullanıcının kimliğini doğrular ve bir Microsoft DFP istemcisi görevi görür. Bir hedef ilke yürüten her kullanıcının kimliğini ve tanılama verilerini toplayan bir parmak izi betiği barındırır. Daha sonra, Microsoft DFP onları şüpheli bulursa, oturum açma veya kaydolma girişimlerini daha sonra engeller.

- **Özel App Service**: iki amaca hizmet eden bir Web uygulaması.

  - Kimlik deneyimi çerçevesinin Kullanıcı arabirimi olarak kullanılacak HTML sayfalarına hizmet verir. Microsoft Dynamics 365 parmak izi yazdırma betiği katıştırmaktan sorumludur.

  - Microsoft DFP 'yi Azure AD B2C 'e bağlayan, yeniden takip eden uç noktaları içeren bir API denetleyicisi. Tanıtıcı veri işleme, yapı ve her ikisinin güvenlik gereksinimlerine uyar.

- **Microsoft DFP parmak izi hizmeti**: cihaz telemetrisini ve kendi kendini onaylanan Kullanıcı ayrıntılarını günlüğe kaydeden, kullanıcının karar verme sürecinde daha sonra kullanılabilmesi için benzersiz olarak tanımlanabilen bir parmak izi oluşturmak üzere, dinamik olarak gömülü betik.

- **Microsoft DFP API uç noktaları**: karar sonucunu sağlar ve istemci uygulaması tarafından gerçekleştirilen işlemi yansıtan son durumu kabul eder. Azure AD B2C, farklı güvenlik ve API yükü gereksinimleri nedeniyle doğrudan uç noktalarla iletişim kurmaz, bunun yerine App Service 'i ara olarak kullanır.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![Görüntüde Microsoft dynamics365 sahtekarlık koruma mimarisi diyagramı gösterilmektedir](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı bir oturum açma sayfasına ulaşır. Kullanıcılar yeni bir hesap oluşturmak ve sayfaya bilgi girmek için kaydol ' u seçer. Azure AD B2C Kullanıcı özniteliklerini toplar.
| 2. | Azure AD B2C orta katman API 'sini çağırır ve Kullanıcı özniteliklerine geçirir.
| 3. | Orta katman API 'SI Kullanıcı özniteliklerini toplar ve bunu Microsoft DFP API 'nin tükettiği bir biçime dönüştürür. Ardından, Microsoft DFP API 'sine gönderdikten sonra.
| 4. | Microsoft DFP API 'SI bilgileri kullandıktan ve işleyerek, sonuç orta katman API 'sine döndürülür.
| 5. | Orta katman API 'SI bilgileri işler ve ilgili bilgileri Azure AD B2C geri gönderir.
| 6. | Azure AD B2C, bilgileri ortadaki katman API 'sinden geri alır. Hata yanıtı gösteriyorsa, kullanıcıya bir hata iletisi görüntülenir. Başarılı bir yanıt gösteriyorsa, kullanıcının kimliği doğrulanır ve dizine yazılır.

## <a name="set-up-the-solution"></a>Çözümü ayarlama

1. Federasyonun Azure AD B2C izin verecek şekilde yapılandırılmış [bir Facebook uygulaması oluşturun](./identity-provider-facebook.md#create-a-facebook-application) .
2. Oluşturduğunuz [Facebook gizli](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) anahtarını bir kimlik deneyimi çerçevesi ilke anahtarı olarak ekleyin.

## <a name="configure-your-application-under-microsoft-dfp"></a>Uygulamanızı Microsoft DFP altında yapılandırma

[Azure AD kiracınızı](/dynamics365/fraud-protection/integrate-real-time-api) Microsoft DFP 'yi kullanacak şekilde ayarlayın.

## <a name="deploy-to-the-web-application"></a>Web uygulamasına dağıtma

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Microsoft DFP hizmeti parmak izi uygulama

[Microsoft DFP cihaz parmak izi](/dynamics365/fraud-protection/device-fingerprinting) , Microsoft DFP hesap koruması için bir gereksinimdir.

>[!NOTE]
>Kullanıcı Azure AD B2C UI sayfalarına ek olarak, daha kapsamlı cihaz profili oluşturma için uygulama kodu içinde parmak izi hizmetini de uygulayabilir. Uygulama kodundaki parmak izi hizmeti bu örneğe dahil değildir.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Azure AD B2C API kodunu dağıtma

[BELIRTILEN API kodunu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) bir Azure hizmetine dağıtın. Kod, [Visual Studio 'dan yayımlanabilir](/visualstudio/deployment/quickstart-deploy-to-azure).

Kurulum CORS, **Izin verilen kaynağı** Ekle `https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Daha sonra, Azure AD 'yi gerekli ayarlarla yapılandırmak için dağıtılan hizmetin URL 'sine ihtiyacınız olacaktır.

Daha fazla bilgi için bkz. [App Service belgeleri](../app-service/app-service-web-tutorial-rest-api.md) .

### <a name="add-context-dependent-configuration-settings"></a>Bağlama bağımlı yapılandırma ayarları ekleme

[Azure 'Daki App Service](../app-service/configure-common.md#configure-app-settings)'te uygulama ayarlarını yapılandırın. Bu, ayarların bir depoya denetlenmeden güvenli bir şekilde yapılandırılmasını sağlar. REST API 'SI için aşağıdaki ayarlar gereklidir:

| Uygulama ayarları | Kaynak | Notlar |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Microsoft DFP yapılandırması |     |
|FraudProtectionSettings: Deviceparmak Izi \ müşteri kimliği | Microsoft cihaz parmak izi müşteri KIMLIĞINIZ |     |
| FraudProtectionSettings: ApiBaseUrl |  Microsoft DFP portalından temel URL 'niz   | Bunun yerine üretim API 'sini çağırmak için '-int ' öğesini kaldırın|
|  TokenProviderConfig: kaynak  | Taban URL 'niz- https://api.dfp.dynamics-int.com     | Bunun yerine üretim API 'sini çağırmak için '-int ' öğesini kaldırın|
|   TokenProviderConfig: ClientID       |Sahtekarlık koruması ticari Azure AD istemci uygulaması KIMLIĞI      |       |
| TokenProviderConfig: Authority | https://login.microsoftonline.com/<directory_ID> | Sahtekarlık koruması ticari Azure AD kiracı yetkiliniz |
| TokenProviderConfig: CertificateThumbprint * | Ticari Azure AD İstemci uygulamanızda kimlik doğrulaması yapmak için kullanılan sertifikanın parmak izi |
| TokenProviderConfig: ClientSecret * | Ticari Azure AD İstemci uygulamanız için gizli dizi | Gizli dizi Yöneticisi kullanılması önerilir |

* Bir sertifikayla veya parola gibi bir gizli dizi ile kimlik doğrulamasından geçirildiğinize bağlı olarak, 2 işaretli parametrelerin yalnızca 1 ' i ayarlayın.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C yapılandırması

### <a name="replace-the-configuration-values"></a>Yapılandırma değerlerini Değiştir

Belirtilen [özel ilkelerde](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies), aşağıdaki yer tutucuları bulun ve örnekten karşılık gelen değerlerle değiştirin.

| Yer tutucu | Şununla değiştir | Notlar |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Kiracınızın kısa adı |  yourtenant.onmicrosoft.com 'dan "kiracınız"   |
|{your_tenantId} | Azure AD B2C kiracınızın kiracı KIMLIĞI |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Azure AD B2C kiracınızda yapılandırılan IdentityExperienceFramework uygulamasının uygulama KIMLIĞI    |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Azure AD B2C kiracınızda yapılandırılan ProxyIdentityExperienceFramework uygulamasının uygulama KIMLIĞI      |   01234567-89ab-cdef-0123-456789ABCDEF     |
|  {your_tenant_extensions_appid}   |  Kiracının depolama uygulamasının uygulama KIMLIĞI   |  01234567-89ab-cdef-0123-456789ABCDEF  |
|   {your_tenant_extensions_app_objectid}  | Kiracınızın depolama uygulamasının nesne KIMLIĞI    | 01234567-89ab-cdef-0123-456789ABCDEF   |
|   {your_app_insights_instrumentation_key}  |   App Insights örneğiniz için izleme anahtarı *  |   01234567-89ab-cdef-0123-456789ABCDEF |
|  {your_ui_base_url}   | Uygulama hizmetinizde, Kullanıcı arabirimi dosyalarınıza hizmet verilen uç nokta    | `https://yourapp.azurewebsites.net/B2CUI/GetUIPage`   |
|   {your_app_service_url}  | Uygulama hizmetinizin URL 'SI    |  `https://yourapp.azurewebsites.net`  |
|   {Facebook-uygulama kimliği}  |  Azure AD B2C ile Federasyon için yapılandırdığınız Facebook uygulamasının uygulama KIMLIĞI   | 000000000000000   |
|  {Facebook-uygulama-gizli}   |  Facebook 'un uygulama gizli anahtarını kaydettiğiniz ilke anahtarının adı   | B2C_1A_FacebookAppSecret   |

* App Insights farklı bir kiracıda olabilir. Bu adım isteğe bağlıdır. Gerekli değilse ilgili teknisyen ve OrechestrationSteps 'yi kaldırın.

### <a name="call-microsoft-dfp-label-api"></a>Microsoft DFP Label API 'YI çağır

Müşterilerin [etiket API 'si uygulaması](/dynamics365/fraud-protection/integrate-ap-api)gerekir. Daha fazla bilgi için bkz. [Microsoft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) .

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Kullanıcı Kimliği değerinin, karşılık gelen Azure AD B2C yapılandırma değeri (ObjectID) ile aynı olması gerekir.

>[!NOTE]
>Öznitelik koleksiyonu sayfasına onay bildirimi ekleyin. Kullanıcıların telemetri ve Kullanıcı kimlik bilgilerinin hesap koruma amacıyla kaydedileceğini bildirin.

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C ilkesini yapılandırma

1. Ilkeler klasöründeki [Azure AD B2C ilkesine](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) gidin.

2. [LocalAccounts başlangıç paketini](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) indirmek için bu [belgeyi](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack) izleyin

3. Azure AD B2C kiracı için ilkeyi yapılandırın.

>[!NOTE]
>Belirli kiracınızla ilgili olarak belirtilen ilkeleri güncelleştirin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın ve Ilkeler altında **kimlik deneyimi çerçevesi**' ni seçin.

2. Daha önce oluşturduğunuz **Signupsignın**' ı seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ayarları seçin:

   a. **Uygulama**: kayıtlı uygulamayı seçin (örnek JWT)

   b. **Yanıt URL 'si**: **yeniden yönlendirme URL** 'sini seçin

   c. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışına gidin ve hesap oluşturun

5. Kullanıcı özniteliği oluşturulduktan sonra, akış sırasında Microsoft DFP hizmeti çağrılır. Akış tamamlanmamışsa, kullanıcının dizine kaydedilip kaydedilmediğini kontrol edin.

>[!NOTE]
>[MICROSOFT DFP kural altyapısını](/dynamics365/fraud-protection/rules)kullanıyorsanız, kuralları doğrudan Microsoft DFP portalındaki güncelleştirme.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Microsoft DFP örnekleri](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
