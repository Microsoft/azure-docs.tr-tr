---
title: Naisnexwith Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Bir profil oluşturma ve kimlik doğrulama hizmeti olan ve Kullanıcı kimliğini doğrulamak ve kullanıcının cihazına göre kapsamlı risk değerlendirmeleri sağlamak için kullanılan Lexisnexwith Azure AD B2C kimlik doğrulamasını tümleştirme hakkında bilgi edinin.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 362704368958d2600d16cd907421917b824a59cb
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256608"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Lexisnexto yapılandırma öğreticisi Azure Active Directory B2C

Bu örnek öğreticide, Azure AD B2C [Lexisnexwith](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)ile tümleştirme hakkında rehberlik sağlıyoruz. Lexisnex, çeşitli çözümler sağlar ve [burada](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)bulunabilir. Bu örnek öğreticide, LexisnexThreatMetrix  çözümünü ele alacağız. ThreatMetrix, profil oluşturma ve kimlik doğrulama hizmetidir. Kullanıcı kimliğini doğrulamak ve kullanıcının cihazına göre kapsamlı risk değerlendirmeleri sağlamak için kullanılır.

Bu tümleştirme, oturum açma akışı sırasında Kullanıcı tarafından belirtilen birkaç Kullanıcı bilgisi parçasını temel alan profil oluşturmayı yapar. ThreatMetrix, kullanıcının oturum açmaya devam etmesine izin verilip verilmeyeceğini belirler. ThreatMetrix 'in Risk analizinde aşağıdaki öznitelikler dikkate alınır:

- E-posta
- Telefon Numarası
- Kullanıcının makinesinden toplanan profil oluşturma bilgileri

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](./tutorial-create-tenant.md) .

## <a name="scenario-description"></a>Senaryo açıklaması

ThreatMetrix tümleştirmesi aşağıdaki bileşenleri içerir:

- Azure AD B2C – kimlik sağlayıcısı olarak da bilinen kullanıcının kimlik bilgilerini doğrulamadan sorumlu yetkilendirme sunucusu

- ThreatMetrix – ThreatMetrix hizmeti kullanıcı tarafından sağlanmış girdileri alır ve kullanıcı etkileşiminin güvenliğini doğrulamak üzere kullanıcının makinesinden toplanan profil bilgileri ile birleştirir.

- Özel REST API 'SI – bu API, Azure AD B2C ile ThreatMetrix hizmeti arasındaki tümleştirmeyi uygular.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![LexisNexis-mimari-diyagram ekran görüntüsü](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Adım | Description |
|:--------------|:-------------|
|1. | Kullanıcı bir oturum açma sayfasına ulaştı. Kullanıcı, yeni bir hesap oluşturmak ve sayfaya bilgi girmek için kaydolma ' yı seçer. Azure AD B2C Kullanıcı özniteliklerini toplar.
| 2. | Azure AD B2C orta katman API 'sini çağırır ve Kullanıcı özniteliklerine geçirir.
| 3. | Orta katman API 'SI Kullanıcı özniteliklerini toplar ve bunu Lexisnexsıs API 'sinin tüketebileceği bir biçime dönüştürür. Ardından, Naisnexa 'ya gönderir.  
| 4. | Lexisnex, bilgileri kullanır ve risk analizine göre Kullanıcı kimliğini doğrulamak için onu işler. Ardından, sonucu orta katman API 'sine döndürür.
| 5. | Orta katman API 'SI bilgileri işler ve ilgili bilgileri Azure AD B2C 'a geri gönderir.
| 6. | Azure AD B2C, bilgileri ortadaki katman API 'sinden geri alır. Hata yanıtı gösteriyorsa, kullanıcıya bir hata iletisi görüntülenir. Başarılı bir yanıt gösteriyorsa, kullanıcının kimliği doğrulanır ve erişim izni verilir.

## <a name="onboard-with-lexisnexis"></a>Lexisnexwith ile ekleme

1. Bir Lexisnexu hesabı oluşturmak için, [lexisnexwith](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) ile iletişim kurun

2. Gereksinimlerinizi karşılayan bir Lexisnexnext ilkesi oluşturun. [Burada](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)bulunan belgeleri kullanın.

>[!NOTE]
> İlke adı daha sonra kullanılacaktır.

Bir hesap oluşturulduktan sonra, API yapılandırması için gereken bilgileri alacaksınız. Aşağıdaki bölümlerde işlem açıklanır.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Naisnexwith Azure AD B2C yapılandırma

### <a name="part-1---deploy-the-api"></a>1. Bölüm-API 'YI dağıtma

Belirtilen [API kodunu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) bir Azure hizmetine dağıtın. Kod, bu [yönergeleri](/visualstudio/deployment/quickstart-deploy-to-azure)Izleyerek Visual Studio 'dan yayımlanabilir.

>[!NOTE]
>Azure AD 'yi gerekli ayarlarla yapılandırmak için dağıtılan hizmetin URL 'sine ihtiyacınız vardır.

### <a name="part-2---configure-the-api"></a>Bölüm 2-API 'YI yapılandırma

Uygulama ayarları [Azure 'Daki App Service 'te yapılandırılabilir](../app-service/configure-common.md#configure-app-settings).  Bu yöntemde, ayarlar bir depoya denetlenmeden güvenle yapılandırılabilir. REST API için aşağıdaki ayarları sağlamanız gerekir:

| Uygulama ayarları | Kaynak | Notlar |
| :-------- | :------------| :-----------|
|ThreatMetrix: URL | ThreatMetrix hesabı yapılandırması |     |
|ThreatMetrix: OrgID | ThreatMetrix hesabı yapılandırması |     |
|ThreatMetrix: ApiKey |ThreatMetrix hesabı yapılandırması|  |
|ThreatMetrix: Ilke | ThreatMetrix içinde oluşturulan ilkenin adı | |
| BasicAuth: ApiUsername |API için Kullanıcı adı tanımlama| Kullanıcı adı Azure AD B2C yapılandırmasında kullanılacaktır
| BasicAuth: ApiPassword | API için bir parola tanımlayın | Parola Azure AD B2C yapılandırmasında kullanılacaktır

### <a name="part-3---deploy-the-ui"></a>3. kısım-Kullanıcı arabirimini dağıtma

Bu çözüm, Azure AD B2C tarafından yüklenen özel kullanıcı arabirimi şablonlarını kullanır. Bu Kullanıcı arabirimi şablonları doğrudan ThreatMetrix hizmetine gönderilen profil oluşturmayı yapılır.

Dahil edilen [Kullanıcı arabirimi dosyalarını](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) bir BLOB depolama hesabına dağıtmak için bu [yönergelere](./customize-ui-with-html.md#custom-page-content-walkthrough) bakın. Yönergeler bir BLOB depolama hesabı ayarlamayı, CORS 'yi yapılandırmayı ve genel erişimi etkinleştirmeyi içerir.

Kullanıcı arabirimi, [okyanus mavi şablonunu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)temel alır. Kullanıcı arabirimindeki tüm bağlantıların dağıtılan konuma başvurması için güncelleştirilmeleri gerekir. Kullanıcı arabirimi klasöründe, dağıtılan konumuyla bulun ve değiştirin https://yourblobstorage/blobcontainer .

### <a name="part-4---create-api-policy-keys"></a>4. Bölüm-API ilkesi anahtarları oluşturma

Bu [belgeye](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) başvurun ve bir API Kullanıcı adı ve yukarıda tanımladığınız API parolası için olmak üzere iki ilke anahtarı oluşturun.

Örnek ilke şu anahtar adlarını kullanır:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>5. kısım-API URL 'sini güncelleştirme

Belirtilen [TrustFrameworkExtensions ilkesinde](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)adlı teknik profili bulun `Rest-LexisNexus-SessionQuery` ve `ServiceUrl` meta VERI öğesini yukarıda dağıtılan API 'nin konumuyla güncelleştirin.

### <a name="part-6---update-ui-url"></a>Bölüm 6-Kullanıcı arabirimi URL 'sini güncelleştirme

Belirtilen [TrustFrameworkExtensions ilkesinde](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml), bir bul ve Değiştir seçeneğini https://yourblobstorage/blobcontainer/ kullanarak, Kullanıcı arabirimi dosyalarının dağıtıldığı konum ile arama yapın.

>[!NOTE]
> En iyi uygulama olarak, müşterilerin öznitelik koleksiyonu sayfasına onay bildirimi eklemesini öneririz. Kimlik doğrulaması için bilgilerin üçüncü taraf hizmetlere gönderileceğini kullanıcılara bildirin.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Bölüm 7-Azure AD B2C ilkesini yapılandırma

[Yerel hesapların başlangıç paketini](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) indirmek ve Azure AD B2C kiracısı için [ilkeyi](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) yapılandırmak üzere bu [belgeye](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) başvurun.

>[!NOTE]
>Belirli kiracınızla ilgili olarak belirtilen ilkeleri güncelleştirin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın ve Ilkeler altında **Kullanıcı akışları ' nı** seçin.

2. Daha önce oluşturduğunuz **Kullanıcı akışınızı** seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ayarları seçin:

   a. **Uygulama**: kayıtlı uygulamayı seçin (örnek JWT)

   b. **Yanıt URL 'si**: **yeniden yönlendirme URL** 'sini seçin

   c. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışına gidin ve hesap oluşturun

5. Oturumu Kapat

6. Oturum açma akışına git  

7. ThreatMetrix bulmaca, **devam** girdikten sonra açılır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
