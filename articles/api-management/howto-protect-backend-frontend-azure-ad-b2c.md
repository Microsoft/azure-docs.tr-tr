---
title: Active Directory B2C ile Azure API Management üzerinde SPA arka ucunu koruyun
description: Azure Active Directory B2C, Azure API Management ve kolay kimlik doğrulamasını kullanarak bir 2,0 API 'yi, PCE etkinleştirilmiş SPA kimlik doğrulama akışı kullanarak bir JavaScript SPA 'dan çağrılacak şekilde koruyun.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954930"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>OAuth 2,0, Azure Active Directory B2C ve Azure API Management ile SPA arka ucunu koruyun

Bu senaryoda, bir API 'yi korumak için Azure API Management örneğinizi nasıl yapılandırabileceğiniz gösterilmektedir.
Bir belirteci almak için Azure AD B2C SPA (auth kodu + PKCE) akışını kullanacağız, bu da bir Azure Işlevleri arka ucunu EasyAuth kullanarak güvenli hale getirmek için API Management.

## <a name="aims"></a>Amaçlar

API Management, Azure Işlevleri ve Azure AD B2C ile basitleştirilmiş bir senaryoda nasıl kullanılabileceğini görebiliriz. Bir API 'yi çağıran ve Azure AD B2C kullanıcıları oturum açan bir JavaScript (JS) uygulaması oluşturacaksınız. Ardından, arka uç API 'sini korumak için API Management Validate-JWT, CORS ve anahtar ilkesi özelliklerine göre oran sınırı ' nı kullanacaksınız.

Derinlemesine savunma için, yeniden bitiş API 'SI içinde belirteci yeniden doğrulamak ve API yönetiminin Azure Işlevleri arka ucunu çağırabilecek tek hizmet olduğundan emin olmak için EasyAuth ' yi kullanırız.

## <a name="what-will-you-learn"></a>Ne öğrenirsiniz?

> [!div class="checklist"]
> * Azure Active Directory B2C 'de tek sayfalı bir uygulama ve arka uç API 'SI kurulumu
> * Azure Işlevleri arka uç API 'SI oluşturma
> * Azure Işlevleri API 'sini Azure API Management içeri aktarma
> * Azure API Management API güvenliğini sağlama
> * Microsoft Identity platform kitaplıkları aracılığıyla Azure Active Directory B2C yetkilendirme uç noktalarını çağırma (MSAL.js)
> * Bir HTML/Vanilla JS tek sayfalı uygulamayı depolama ve bir Azure Blob depolama uç noktasından sunma

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları izlemek için, şunları yapmanız gerekir:

* Ön uç JS tek sayfalı uygulamayı barındırmak için bir Azure (StorageV2) v2 depolama hesabı Genel Amaçlı.
* Bir Azure API Management örneği (' tüketim ' de dahil olmak üzere), ancak tam senaryoya uygun Özellikler bu katmanda (fiyat-sınır ve ayrılmış sanal IP) kullanılamaz. bu kısıtlamalar, aşağıda verilen makalede aşağıda verilmiştir.
* Çağrılan API 'yi barındırmak için boş bir Azure Işlev uygulaması (bir tüketim planında V 3.1 .NET Core çalışma zamanı 'nı çalıştıran)
* Bir aboneliğe bağlı Azure AD B2C kiracısı.

Uygulamada, üretim iş yüklerinde aynı bölgedeki kaynakları kullanabilirsiniz, ancak bu nasıl yapılır makalesi için dağıtım bölgesi önemli değildir.

## <a name="overview"></a>Genel Bakış

Bu işlem tamamlandıktan sonra, kullanımdaki bileşenlerin ve aralarındaki akışın bir gösterimi aşağıda verilmiştir.
![Kullanımdaki ve Flow bileşenleri](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Kullanımdaki ve Flow bileşenleri")

Adımlara hızlı bir genel bakış aşağıda verilmiştir:

1. Kapsamlarla (ön uç, API Management) ve API uygulamaları ile API erişimi sağlamak Azure AD B2C oluşturun
1. Kullanıcıların Azure AD B2C oturum açmasını sağlamak için kaydolma ve oturum açma ilkelerini oluşturma 
1. Geliştirici konsolunda OAuth2 Kullanıcı yetkilendirmesini etkinleştirmek için yeni Azure AD B2C Istemci kimlikleri ve anahtarlarıyla API Management yapılandırın
1. Işlev API 'sini oluşturma
1. Yeni Azure AD B2C Istemci KIMLIĞI ve anahtarlarıyla EasyAuth 'yi etkinleştirmek ve APıM VIP 'ye kilitlemek için Işlev API 'sini yapılandırın
1. API Management içinde API tanımını oluşturma
1. API Management API yapılandırması için Oauth2 ayarlama
1. **CORS** ilkesini ayarlama ve her gelen Istek için OAuth belirtecini doğrulamak üzere **Validate-JWT** ilkesini ekleme
1. API 'YI kullanmak için çağıran uygulamayı oluşturun
1. JS SPA örneğini karşıya yükle
1. Örnek JS Istemci uygulamasını yeni Azure AD B2C Istemci KIMLIĞI ve anahtarlarıyla yapılandırın 
1. Istemci uygulamasını test etme

   > [!TIP]
   > Birkaç bilgi ve anahtar parçasını yakalacağız ve bu belgeyi yürütireceğiz gibi, aşağıdaki yapılandırma öğelerini geçici olarak depolamak için bir metin düzenleyicisinin açık olduğunu fark edebilirsiniz.  
   >
   > B2C ARKA UÇ ISTEMCI KIMLIĞI:  
   > B2C ARKA UÇ ISTEMCI GIZLI ANAHTARı:  
   > B2C ARKA UÇ APı KAPSAM URI 'SI:  
   > B2C ÖN UÇ ISTEMCI KIMLIĞI:  
   > B2C KULLANıCı AKıŞ UÇ NOKTASı URı 'SI:  
   > B2C IYI BILINEN OPENıD UÇ NOKTASı:   
   > B2C Ilke adı: Frontendapp_signupandsignin Işlev URL 'SI:  
   > APıM APı TABAN URL 'SI: DEPOLAMA BIRINCIL UÇ NOKTA URL 'SI:  

## <a name="configure-the-backend-application"></a>Arka uç uygulamasını yapılandırma

Portalda Azure AD B2C dikey penceresini açın ve aşağıdaki adımları uygulayın.

1. **Uygulama kayıtları** sekmesini seçin
1. ' Yeni kayıt ' düğmesine tıklayın.
1. Yeniden yönlendirme URI 'SI seçim kutusundan ' Web ' seçeneğini belirleyin.
1. Şimdi görünen adı ayarlayın, benzersiz bir öğe seçin ve oluşturulmakta olan hizmetle ilgili bir seçim yapın. Bu örnekte, "arka uç uygulaması" adını kullanacağız.
1. Yanıt URL 'leri için ' https://jwt.ms ' (Microsoft 'un belirteç kod çözme sitesi) gibi yer tutucuları kullanın, bu URL 'leri daha sonra güncelleştireceğiz.
1. "Tüm kimlik sağlayıcılardaki veya kuruluş dizinindeki (Kullanıcı akışlara sahip kullanıcıların kimliğini doğrulamak için) hesaplar" seçeneğini seçtiğinizden emin olun.
1. Bu örnek için, bugün offline_access izin vermediğimiz için "yönetici onayı verme" kutusunun işaretini kaldırın.
1. 'Kaydet' düğmesine tıklayın.
1. Arka uç uygulama Istemci KIMLIĞINI daha sonra kullanılmak üzere kaydedin (' uygulama (istemci) KIMLIĞI ' altında gösterilir).
1. *Sertifikalar ve gizlilikler* sekmesini seçin (Yönet bölümünde) ve bir kimlik doğrulama anahtarı oluşturmak Için ' yeni istemci parolası ' seçeneğine tıklayın (varsayılan ayarları kabul edin ve ' Ekle ' seçeneğine tıklayın).
1. ' Ekle 'ye tıklandıktan sonra, anahtarı (' değer ' altında) daha sonra ' arka uç istemci parolası ' olarak kullanılmak üzere bir yere kopyalayın-Bu iletişim kutusunun bu anahtarı kopyalamanız için sahip olmanız gereken tek şans olduğunu unutmayın.
1. Şimdi *BIR API 'Yi kullanıma* sunma sekmesini seçin (Yönet altında).
1. AppID URI 'sini ayarlamanız istenir, varsayılan değeri seçin ve kaydedin.
1. Işlev API 'niz için "Hello" kapsamını oluşturun ve adlandırın, ' Merhaba ' ifadesini kullanarak, tüm kurumsal seçenekler için ' Hello ' ifadesini kullanabilirsiniz, doldurulmuş tam kapsam değeri URI 'sini kaydedebilir ve ardından ' kapsam Ekle ' seçeneğine tıklayabilirsiniz.
1. Portalın sol üst kısmındaki ' Azure AD B2C ' içerik haritasını seçerek Azure AD B2C dikey pencerenin köküne dönün.

   > [!NOTE]
   > Azure AD B2C kapsamları, API 'lerinizin API erişimi dikey penceresi aracılığıyla, diğer uygulamaların uygulama izinlerini, sizin de yalnızca adlandırılmış API 'niz için sizin oluşturduğunuz etkin bir şekilde bir şekilde oluşturabilebileceği etkin izinlere sahiptir.

## <a name="configure-the-frontend-application"></a>Ön uç uygulamasını yapılandırma

1. **Uygulama kayıtları** sekmesini seçin
1. ' Yeni kayıt ' düğmesine tıklayın.
1. Yeniden yönlendirme URI 'SI seçim kutusundan ' tek sayfalı uygulama (SPA) ' seçeneğini belirleyin.
1. Şimdi görünen adı ve AppID URI 'sini ayarlayın, benzersiz bir öğe seçin ve bu AAD B2C uygulama kaydını kullanacak ön uç uygulamasıyla ilgili bir seçim yapın. Bu örnekte, "ön uç uygulaması" kullanabilirsiniz
1. İlk uygulama kaydına göre, desteklenen hesap türleri seçimini varsayılan olarak bırakın (Kullanıcı akışları ile kullanıcıların kimliğini doğrulama)
1. Yanıt URL 'leri için ' https://jwt.ms ' (Microsoft 'un belirteç kod çözme sitesi) gibi yer tutucuları kullanın, bu URL 'leri daha sonra güncelleştireceğiz.
1. Yönetici izni verme kutusunu işaretlendiğinden bırakın
1. 'Kaydet' düğmesine tıklayın.
1. Ön uç uygulama Istemci KIMLIĞINI daha sonra kullanılmak üzere kaydedin (' uygulama (istemci) KIMLIĞI ' altında gösterilir).
1. *API izinleri* sekmesine geçin.
1. ' İzin Ekle ' ve ardından ' API 'Lerim ' öğesine tıklayarak arka uç uygulamasına erişim izni verin, "arka uç uygulaması" ' nı seçin, ' Izinler ' i seçin, önceki bölümde oluşturduğunuz kapsamı seçin ve ' izin Ekle ' seçeneğine tıklayın.
1. ' {Tenant} için yönetici izni ver ' e tıklayın ve açılan iletişim kutusunda ' Evet 'e tıklayın. Bu açılan pencere, daha önce oluşturulmuş "arka uç uygulaması" içinde tanımlanan "Hello" iznini kullanmak için "ön uç uygulaması" nı onaylar.
1. Artık tüm Izinler, uygulama için Durum sütununun altında yeşil bir onay işareti olarak gösterilmelidir

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>"Kaydolma ve oturum açma" Kullanıcı akışı oluşturma

1. Azure AD B2C içerik haritasını seçerek B2C dikey penceresinin köküne dönün.
1. ' Kullanıcı Akışları ' (Ilkeler altında) sekmesine geçin.
1. "Yeni Kullanıcı akışı" na tıklayın
1. ' Kaydol ve oturum aç ' Kullanıcı akış türünü seçin ve ' önerilen ' öğesini ve ardından ' oluştur ' seçeneğini belirleyin
1. İlkeye bir ad verin ve daha sonra için kaydedin. Bu örnekte, "Frontendapp_signupandsignin" kullanabilirsiniz, bunun "B2C_1_Frontendapp_signupandsignin" yapmak için "B2C_1_" öneki olacağını aklınızda bulabilirsiniz
1. ' Kimlik sağlayıcıları ' ve "yerel hesaplar" altında, ' e-posta kaydı ' veya B2C kiracınızın yapılandırmasına bağlı olarak ' Kullanıcı KIMLIĞI kaydolma ' seçeneğini işaretleyin ve Tamam ' a tıklayın. Bu yapılandırma, bir kullanıcının var olan sosyal medya hesabını kullanmak için başka bir kimlik sağlayıcısına (sosyal kimlik sağlayıcısı gibi) ertelemeyi değil, yerel B2C hesaplarını kaydettireceğiz.
1. MFA ve koşullu erişim ayarlarını varsayılan olarak bırakın.
1. ' Kullanıcı öznitelikleri ve talepler ' altında ' daha fazla göster... ' seçeneğine tıklayın. daha sonra kullanıcılarınızın girmesini ve belirtece döndürülmesini istediğiniz talep seçeneklerini belirleyin. ' Görünen ad ' ve ' e-posta adresi ' öğesini toplamak için ' görünen ad ' ve ' e-posta adresi ' seçeneğini işaretleyin (emaadresinde, tekil ' i topladığınız ve e-posta adresleri, birden çok) ve ardından ' oluştur ' seçeneğine tıklayın.
1. Listede oluşturduğunuz Kullanıcı akışına tıklayın ve sonra ' Kullanıcı akışını Çalıştır ' düğmesine tıklayın.
1. Bu eylem kullanıcı akışını Çalıştır dikey penceresini açacak, ön uç uygulamasını seçtiğinizde, Kullanıcı akış uç noktasını kopyalayıp daha sonra kaydederek kaydetmeyecektir.
1. Bağlantıyı en üstte kopyalayın ve daha sonra kullanmak üzere ' iyi bilinen OpenID yapılandırma uç noktası ' olarak kaydederek saklayın.

   > [!NOTE]
   > B2C Ilkeleri, farklı veri bileşenleri yakalamak ve kullanıcıların farklı yollarla oturum açabilmek için Azure AD B2C oturum açma uç noktalarını kullanıma sunlamanızı sağlar.
   > 
   > Bu durumda kaydolma veya oturum açma akışı (ilke) yapılandırdık. Bu, her iki durumda da "p =" sorgu dizesi parametresi tarafından oluşturulan ilkenizin URL 'sinde tanımlanmıştı.  
   >
   > Bu işlem tamamlandıktan sonra, artık kullanıcıları birden çok uygulamada imzalayabileceğiniz bir sektör kimliği platformu olan işlevsel bir Işletmeniz vardır.

## <a name="build-the-function-api"></a>İşlev API 'sini oluşturma

1. Aboneliğinizdeki öğeleri yeniden yapılandırabilmemiz için Azure portal standart Azure AD kiracınıza geri dönün.
1. Azure portal Işlev uygulamaları dikey penceresine gidin, boş işlev uygulamanızı açın, sonra ' Işlevler ' ' e tıklayın, ' Ekle ' seçeneğine tıklayın.
1. Görüntülenen açılır menü ' de ' Portal 'da geliştir ' seçeneğini belirleyin, ' şablon seç ' in altında ' HTTP tetikleyicisi ' öğesini seçin, şablon Ayrıntıları ' nın altında, Yetkilendirme düzeyi ' function ' olan ' Hello ' olarak adlandırın ve Ekle ' yi seçin.
1. Kod + test dikey penceresine geçin ve aşağıdaki örnek kodu kopyalayın ve görüntülenen *Varolan kodun üzerine* yapıştırın.
1. Kaydet’i seçin.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!TIP]
   > Yeni yapıştırdığınız c# betiği işlev kodu yalnızca bir satırı işlevler günlüklerine kaydeder ve bazı dinamik veriler (Tarih ve saat) ile "Merhaba Dünya" metnini döndürür.

1. Sol taraftaki dikey pencerede "tümleştirme" seçeneğini belirleyin ve ardından ' tetikleyici ' kutusunun içindeki http (req) bağlantısına tıklayın.
1. ' Seçili HTTP metotları ' açılır listesinden HTTP POST yönteminin işaretini kaldırın ve yalnızca seçili bırakın ve Kaydet ' e tıklayın.
1. Kod + test sekmesine geri dönün, ' Işlev URL 'SI Al ' seçeneğine tıklayın, ardından görüntülenen URL 'yi kopyalayın ve daha sonra kaydedin.

   > [!NOTE]
   > Az önce oluşturduğunuz bağlamalar, yalnızca yeni kopyaladığınız URL 'ye anonim HTTP GET istekleri için yanıt vermek için IŞLEVLERE söyleyecektir `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` . Artık çok basit bir yük döndürmekte olan ölçeklenebilir bir sunucusuz https API 'SI sunuyoruz.
   >
   > Artık, yeni kopyaladığınız ve kaydettiğiniz URL sürümünüzü kullanarak bu API 'yi bir Web tarayıcısından çağırmayı test edebilirsiniz. Ayrıca, URL 'nin "? Code = SecretKey" sorgu dizesi parametrelerini kaldırabilir ve Azure Işlevlerinin 401 hatası döndürmekte olduğunu kanıtlamak için yeniden test edebilirsiniz.

## <a name="configure-and-secure-the-function-api"></a>İşlev API 'sini yapılandırma ve koruma

1. İşlev uygulamasındaki iki ek alanın yapılandırılması gerekir (yetkilendirme ve ağ kısıtlamaları).
1. Kimlik doğrulama/yetkilendirme yapılandırmam, bu nedenle, içerik haritası aracılığıyla işlev uygulamasının kök dikey penceresine geri gidin.
1. Sonra ' kimlik doğrulaması/yetkilendirme ' seçeneğini belirleyin (' Ayarlar ' altında).
1. App Service kimlik doğrulaması özelliğini açın.
1. İsteğin kimliği doğrulanmamış aşağı açılan "Azure Active Directory oturum aç" olarak ayarlandığında yapılacak eylemi ayarlayın.
1. ' Kimlik doğrulama sağlayıcıları ' altında ' Azure Active Directory ' öğesini seçin.
1. Yönetim modu anahtarından ' Gelişmiş ' seçeneğini belirleyin.
1. Arka uç uygulamasının [Uygulama] Istemci KIMLIĞINI (Azure AD B2C) ' Istemci KIMLIĞI ' kutusuna yapıştırın
1. En Iyi bilinen açık kimlik yapılandırma uç noktasını veren URL 'SI kutusuna (Bu yapılandırmayı daha önce kaydettik), kayıt ve oturum açma ilkesinden yapıştırın.
1. ' Gizli dizi ' düğmesine tıklayın ve arka uç uygulamasının istemci gizli anahtarını uygun kutuya yapıştırın.
1. Kimlik sağlayıcısı seçim dikey penceresine/ekranına geri döndüğünüzde Tamam ' ı seçin.
1. Gelişmiş ayarlar altında [belirteç deposu](../app-service/overview-authentication-authorization.md#token-store) etkin kalsın (varsayılan).
1. ' Kaydet ' seçeneğine tıklayın (dikey pencerenin sol üst kısmında).

   > [!IMPORTANT]
   > Artık Işlev API 'niz dağıtıldı ve doğru JWT yetkilendirme olarak sağlanmamışsa 401 yanıt vermelidir ve geçerli bir istek sunulursa verileri döndürmelidir.  
   > Kimliği doğrulanmamış istekleri işlemek için ' Azure AD Ile oturum aç ' seçeneğini yapılandırarak EasyAuth 'e ek derinlemesine savunma güvenliği ekledik. Bu işlem arka uç İşlev Uygulaması ve ön uç SPA arasındaki yetkisiz istek davranışını değiştirecek ve EasyAuth, 401 yetkili olmayan bir yanıt yerine AAD 'ye bir 302 yeniden yönlendirme yayınlacağına dikkat edin. bunu API Management daha sonra kullanarak düzeltebiliriz.  
   >
   > Hala bir IP güvenliği uygulanamadığından, geçerli bir anahtarınız ve OAuth2 belirteciniz varsa, herkes bunu her yerde çağırabilir ve tüm isteklerin API Management aracılığıyla gelmesini zorlamak istiyoruz.  
   > 
   > APıM tüketim katmanını kullanıyorsanız, işlevler erişim kısıtlamalarına sahip izin verilenler için [bir adanmış Azure API Management sanal IP 'si yoktur](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) . Azure API Management standart SKU 'sunda ve VIP 'nin üstünde [tek kiracı ve kaynağın kullanım ömrü için kullanılır](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). Azure API Management tüketim katmanı için, yukarıda kopyaladığınız URI 'nin bölümündeki paylaşılan gizli dizi işlev anahtarı aracılığıyla API çağrılarınızı azaltabilirsiniz. Ayrıca, tüketim katmanı için aşağıdaki 12-17 adımları uygulanmaz.

1. ' Kimlik doğrulama/yetkilendirme ' dikey penceresini kapat 
1. *Portalın API Management dikey penceresini* açın, sonra *örneğinizi* açın.
1. Genel Bakış sekmesinde gösterilen özel VIP 'yi kaydedin.
1. *Portalın Azure işlevleri dikey penceresine* dönüp *örneğinizi* yeniden açın.
1. ' Ağ ' ' ı seçin ve ardından ' erişim kısıtlamalarını Yapılandır ' öğesini seçin
1. ' Kural Ekle ' seçeneğine tıklayın ve yukarıdaki 3. adımda bulunan VIP 'yi xx. xx. xx. xx/32 biçiminde girin.
1. İşlevler portalı ile etkileşime devam etmek ve aşağıdaki isteğe bağlı adımları uygulamak istiyorsanız, kendi genel IP adresinizi veya CıDR aralığınızı da buraya eklemeniz gerekir.
1. Listede bir izin verme girişi olduğunda Azure, diğer tüm adresleri engellemek için örtük bir reddetme kuralı ekler.

IP kısıtlamaları paneline CıDR biçimli adres blokları eklemeniz gerekir. API Management VIP gibi tek bir adres eklemeniz gerektiğinde, bunu xx. xx. xx. xx/32 biçiminde eklemeniz gerekir.

   > [!NOTE]
   > Artık Işlev API 'niz, API Yönetimi veya adresiniz aracılığıyla değil herhangi bir yerden çağrılabilir olmamalıdır.

1. *API Management dikey penceresini* açın ve sonra *örneğinizi* açın.
1. API dikey penceresini seçin (API 'Ler altında).
1. ' Yeni bir API Ekle ' bölmesinden ' İşlev Uygulaması ' öğesini seçin ve ardından açılan pencerenin üst kısmından ' tam ' seçeneğini belirleyin.
1. Araştır ' a tıklayın, içinde API barındırmakta olduğunuz işlev uygulamasını seçin ve Seç ' e tıklayın. Sonra, yeniden Seç ' e tıklayın.
1. API 'ye API Management iç kullanımı için bir ad ve açıklama verin ve bunu ' sınırsız ' ürüne ekleyin.
1. API 'nin ' temel URL 'sini kopyalayıp kaydedin ve ' oluştur ' düğmesine tıklayın.
1. ' Ayarlar ' sekmesine tıklayın ve ardından abonelik ' ın altında ' abonelik gerekli ' onay kutusunu işaretleyin. Bu durumda, bu örnekte OAuth JWT belirtecini hız limiti olarak kullanırız. Tüketim katmanını kullanıyorsanız, bunun hala bir üretim ortamında gerekli olacağını unutmayın. 

   > [!TIP]
   > APıM tüketim katmanını kullanıyorsanız, Sınırsız ürün kullanıma hazır olmayacaktır. Bunun yerine, "API 'Ler" altında "Ürünler" e gidin ve "Ekle" düğmesine basın.  
   > Ürün adı ve açıklama olarak "sınırsız" yazın ve ekranın sol alt kısmındaki "+" API çağrısı ' ndan yeni eklediğiniz API 'yi seçin. "Yayımlandı" onay kutusunu seçin. Rest 'i varsayılan olarak bırakın. Son olarak, "Oluştur" düğmesine basın. Bu, "sınırsız" ürünü oluşturdu ve API 'nize atamıştır. Yeni ürününüzü daha sonra özelleştirebilirsiniz.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Doğru depolama uç noktası ayarlarını yapılandırın ve yakalayın

1. Azure portal depolama hesapları dikey penceresini açın 
1. Oluşturduğunuz hesabı seçin ve ayarlar bölümünden ' statik Web sitesi ' dikey penceresini seçin (' statik Web sitesi ' seçeneğini görmüyorsanız, v2 hesabı oluşturduğunuza bakın).
1. Statik web barındırma özelliğini ' Enabled ' olarak ayarlayın ve Dizin belgesi adını ' index.html ' olarak ayarlayın ve ardından ' Kaydet 'e tıklayın.
1. Bu konum ön uç sitesinin barındırıldığı yer olduğu için, daha sonra ' birincil uç nokta ' içeriğini göz önünde edin.

   > [!TIP]
   > SPA 'yı barındırmak için Azure Blob Storage + CDN yeniden yazma veya Azure App Service kullanabilirsiniz; ancak blob depolamanın statik Web sitesi barındırma özelliği, Azure depolama 'dan statik Web içeriği/HTML/JS/CSS hizmeti sunmak için varsayılan bir kapsayıcı verir ve, sıfır iş için ABD için varsayılan bir sayfa çıkarır.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>**CORS** ve **Validate-JWT** ilkelerini ayarlama

> Aşağıdaki bölümler, kullanılan APıM katmanının ne olursa olsun gelmelidir. Depolama hesabı URL 'SI, bu makalenin en üstündeki ön koşullardan kullanılabilir hale getirdiğiniz depolama hesabından alınır.
1. Portalın API Yönetimi dikey penceresine geçin ve örneğinizi açın.
1. API 'Leri seçin ve ardından "tüm API 'Ler" i seçin.
1. "Gelen işleme" altında, ilke düzenleyicisini göstermek için "</>" kod görünümü düğmesine tıklayın.
1. Gelen bölümünü düzenleyin ve aşağıdaki XML 'yi şöyle olacak şekilde yapıştırın.
1. Ilkede aşağıdaki parametreleri değiştirin
1. {PrimaryStorageEndpoint} (Önceki bölümde kopyaladığınız ' birincil depolama uç noktası '), {b2cpolicy-Well-bilinen-OpenID} (daha önce kopyaladığınız ' iyi bilinen OpenID yapılandırma uç noktası ') ve {arka uç API-API-Application-Client-id} ( **arka uç API 'si** Için B2C uygulaması/istemci kimliği), daha önce kaydedilmiş doğru değerlerle birlikte kaydedilir.
1. API Management tüketim katmanını kullanıyorsanız, Azure API Management 'in tüketim katmanı kullanılırken bu ilke kullanılamadığından her iki hız-limit ilkesini de kaldırmanız gerekir.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Artık Azure API Yönetimi, JavaScript SPA uygulamalarınızdan gelen çıkış istekleri için yanıt verebilir ve istek, isteğin API 'sine iletilmeden önce geçirilen JWT kimlik doğrulama belirtecinin, azaltma, hız sınırlaması ve ön doğrulamayı gerçekleştirecek
   > 
   > Tebrikler, artık bir API 'yi yayımlamak, güvenli hale getirmek ve kullanmak için birlikte çalışan Azure AD B2C, API Management ve Azure Işlevleriniz var!

   > [!TIP]
   > Daha sonra API Management tüketim katmanını kullanıyorsanız, JWT konusu veya gelen IP adresi tarafından sınırlama oranı yerine (anahtar ilkesi ile çağrı hızını sınırlandır "tüketim" katmanında desteklenmez), çağrı hızı kotasına göre [sınırlandırabilirsiniz.](./api-management-access-restriction-policies.md#LimitCallRate)  
   > Bu örnek, JavaScript tek sayfalı bir uygulama olduğundan, yalnızca fiyat sınırlaması ve faturalandırma çağrıları için API Management anahtarını kullanırız. Gerçek yetkilendirme ve kimlik doğrulama Azure AD B2C tarafından işlenir ve JWT içinde kapsüllenir. Bu, API Management ve sonra arka uç Azure Işlevi tarafından iki kez doğrulanan bir kez yapılır.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>JavaScript SPA örneğini statik depolamaya yükleme

1. Hala depolama hesabı dikey penceresinde, blob hizmeti bölümünde ' kapsayıcılar ' dikey penceresini seçin ve sağ bölmede görüntülenen $web kapsayıcısına tıklayın.
1. Aşağıdaki kodu makinenizde index.html olarak yerel olarak bir dosyaya kaydedin ve sonra index.html dosyasını $web kapsayıcısına yükleyin.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Son bölümde daha önce depoladığınız statik Web sitesi birincil uç noktasına gidin.

   > [!NOTE]
   > Tebrikler, Azure depolama statik içerik barındırma için yalnızca bir JavaScript tek sayfalı uygulama dağıttınız.  
   > JS uygulamasını Azure AD B2C ayrıntılarınız henüz yapılandırmadığımızdan, bu dosyayı açtığınızda sayfa henüz çalışmayacak.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Azure AD B2C için JavaScript SPA 'yı yapılandırın

1. Artık her şeyin nerede olduğunu biliyoruz: SPA 'yı uygun API Management API adresiyle ve doğru Azure AD B2C uygulama/istemci kimlikleri ile yapılandırabiliriz.
1. Azure portal depolama dikey penceresine dön 
1. ' Kapsayıcılar ' (' Ayarlar ' altında) seçeneğini belirleyin 
1. Listeden ' $web ' kapsayıcısını seçin
1. Listeden index.html blobu seçin 
1. ' Düzenle ' öğesine tıklayın 
1. Msal config bölümündeki auth değerlerini, B2C 'de daha önce kaydettiğiniz *ön uç* uygulamanızla eşleşecek şekilde güncelleştirin. Yapılandırma değerlerinin nasıl görünebilmelidir hakkında ipuçları için kod açıklamalarını kullanın.
*Yetkili* değerin şu biçimde olması gerekir:-https://{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}. onmicrosoft. com}/{signupandsignınpolicyname}, örnek adlarımızı kullandıysanız ve B2C kiracınız ' contoso ' olarak çağrılırsa, yetkilinin ' ' olmasını beklemeniz gerekir https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin .
1. API değerlerini arka uç adresinizle eşleşecek şekilde ayarlayın (daha önce kaydettiğiniz API taban URL 'Si ve ' b2cScopes ' değerleri, *arka uç uygulaması* için daha önce kaydedilir).
1. Kaydet’e tıklayın.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Azure AD B2C ön uç uygulaması için yeniden yönlendirme URI 'Lerini ayarlayın

1. Azure AD B2C dikey penceresini açın ve JavaScript ön uç uygulaması için uygulama kaydına gidin.
1. ' URI 'yi yeniden yönlendir ' öğesine tıklayın ve https://jwt.ms daha önce girdiğimiz ' ' yer tutucusunu silin.
1. Birincil (depolama) uç noktası için yeni bir URI ekleyin (sondaki eğik çizgi eksi işareti).

   > [!NOTE]
   > Bu yapılandırma, ön uç uygulamasının istemcisinin Azure AD B2C uygun taleplerle bir erişim belirteci almasına neden olur.  
   > SPA, arka uç API 'sine yapılan çağrıda https üstbilgisine bir taşıyıcı belirteci olarak ekleyebilecektir.  
   > 
   > API Management, Azure KIMLIĞI (Kullanıcı) tarafından verilen JWT konusunun ve çağıranın IP adresine göre (API Management hizmet katmanına bağlı olarak, Yukarıdaki nota bakın), Azure Işlev API 'sine yönelik isteği geçirmeden önce, Yukarıdaki nota bakın), simgenin güvenlik anahtarını ekleyerek belirtecin ön noktasını önceden doğrulayacaktır.  
   > SPA, yanıtı tarayıcıda işleyecek.
   >
   > *Tebrikler, Azure AD B2C, Azure API Management, Azure Işlevleri Azure App Service yetkilendirmeyi, mükemmel uyum içinde çalışacak şekilde yapılandırdınız!*

Artık basit bir güvenlik API 'SI olan basit bir uygulama sunuyoruz, bunu test edelim.

## <a name="test-the-client-application"></a>İstemci uygulamasını test etme

1. Daha önce oluşturduğunuz depolama hesabından not ettiğiniz örnek uygulama URL 'sini açın.
1. Sağ üst köşede "oturum aç" düğmesine tıklayın, bu tıklama Azure AD B2C oturum açma veya oturum açma profilini açılır.
1. Uygulama, B2C profil adınızla size hoş geldiniz.
1. Şimdi "API çağrısı" seçeneğine tıklayın ve sayfanın, güvenli API 'nizden geri gönderilen değerlerle güncelleştirilmesi gerekir.
1. API çağır düğmesine *tekrar tekrar* tıklarsanız ve geliştirici katmanında veya API Management üzerinde çalıştırıyorsanız, çözümünüzün API 'yi Derecelendir ve bu özellik uygulamada uygun bir iletiyle bildirilmesi gerektiğini unutmayın.

## <a name="and-were-done"></a>Biz de yaptık

Yukarıdaki adımlar, API Management Azure AD B2C birçok farklı kullanılmasına izin vermek için uyarlanmıştır ve düzenlenebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Active Directory ve OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md)hakkında daha fazla bilgi edinin.
* API Management hakkında daha fazla [videoya](https://azure.microsoft.com/documentation/videos/index/?services=api-management) göz atın.
* Arka uç hizmetinizi güvenli hale getirmeye yönelik diğer yollar için bkz. [Karşılıklı sertifika kimlik doğrulaması](api-management-howto-mutual-certificates.md).
* [API Management hizmet örneği oluşturun](get-started-create-service-instance.md).
* [Ilk API 'Nizi yönetin](import-and-publish.md).