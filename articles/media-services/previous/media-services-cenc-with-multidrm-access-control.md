---
title: Azure Media Services kullanarak erişim denetimiyle bir içerik koruma sisteminin tasarımı | Microsoft Docs
description: Microsoft Kesintisiz Akış Istemci taşıma paketi 'nin nasıl lisansladığı hakkında bilgi edinin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a4f151b597b57b77fa6517c6ea0d586c1106986
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017146"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Azure Media Services kullanarak erişim denetimiyle içerik koruma sistemi tasarlama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış

Üst düzey (OTT) veya çevrimiçi akış çözümü için dijital hak yönetimi (DRM) alt sistemi tasarlama ve oluşturma karmaşık bir görevdir. Operatörler/çevrimiçi video sağlayıcıları bu görevi genellikle özelleştirilmiş DRM hizmeti sağlayıcılarına dış olarak sağlar. Bu belgenin amacı, bir OTT veya çevrimiçi akış çözümünde uçtan uca bir DRM alt sisteminin başvuru tasarımını ve uygulamasını sunmaktır.

Bu belgeye yönelik hedeflenen okuyucular, OTT 'nin DRM alt sistemlerinde veya DRM alt sistemleri ile ilgilenen çevrimiçi akış/çok ekranlı çözümlerin veya okuyucuların çalıştığı mühendislerdir. Bu varsayım, okuyucuların PlayReady, Widevine, FairPlay veya Adobe Access gibi pazardaki DRM teknolojilerinden en az birini öğrenme hızıdır.

DRM 'nin bu tartışmasında, çoklu DRM ile ortak şifreleme (CENC) de yer alır. Çevrimiçi akış ve OTT sektöründe önemli bir eğilim, çeşitli istemci platformlarında birden çok yerel DRM ile CENC 'yi kullanmaktır. Bu eğilim, bir önceki, çeşitli istemci platformları için tek bir DRM ve istemci SDK 'sını kullanan bir vardiyadır. Multi-Native DRM ile CENC kullandığınızda hem PlayReady hem de Widevine [Common Encryption (ISO/ıec 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) belirtimi başına şifrelenir.

Birden çok DRM ile CENC 'nin avantajları şunlardır:

* Tek bir şifreleme işlemi, kendi yerel Drtığından farklı platformları hedeflemek için kullanıldığından, şifreleme maliyetini azaltır.
* Şifrelenmiş varlıkların yalnızca tek bir kopyası gerektiğinden, şifrelenmiş varlıkların yönetilmesi maliyetini azaltır.
* Yerel DRM istemcisi genellikle yerel platformunda ücretsiz olduğundan, DRM istemci lisanslama maliyetini ortadan kaldırır.

Microsoft, bazı önemli sektör oynatıcılarıyla birlikte DASH ve CENC 'nin etkin bir Promoter. Azure Media Services DASH ve CENC için destek sağlar. Son Duyurular için aşağıdaki bloglara bakın:

*  [Azure Media Services’de Google Widevine lisans teslim hizmetleri ile tanışın](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services, çok DRM akışı sunmak için Google Widevine paketlemeyi ekler](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Makalenin amaçları

Bu makalenin amaçları şunlardır:

* Multi-DRM ile CENC 'yi kullanan bir DRM alt sisteminin başvuru tasarımını sağlayın.
* Azure/Media Services platformunda bir başvuru uygulama sağlayın.
* Bazı tasarım ve uygulama konularını tartışın.

Makalesinde, "çoklu DRM" terimi aşağıdaki ürünleri kapsar:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

Aşağıdaki tabloda, her DRM tarafından desteklenen yerel platform/yerel uygulama ve tarayıcılar özetlenmektedir.

| **İstemci platformu** | **Yerel DRM desteği** | **Tarayıcı/uygulama** | **Akış biçimleri** |
| --- | --- | --- | --- |
| **Akıllı TV 'ler, işletmen STBs, OTT STBs** |PlayReady birincil, ve/veya Widevine, ve/veya diğer |Linux, Opera, WebKit, diğer |Çeşitli biçimler |
| **Windows 10 cihazlar (Windows BILGISAYARı, Windows tabletler, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/ıE11/EME<br/><br/><br/>Evrensel Windows Platformu |TIRE (HLS için PlayReady desteklenmez)<br/><br/>DASH, Kesintisiz Akış (HLS için PlayReady desteklenmez) |
| **Android cihazlar (telefon, tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X istemcileri ve Apple TV** |FairPlay |Safari 8 +/EME |HLS |

Her DRM için dağıtımın geçerli durumunu göz önünde bulundurarak, bir hizmet genellikle iki veya üç DRMs uygulamak istiyor ve en iyi şekilde tüm uç nokta türlerini adreslediğinizden emin olmanızı sağlar.

Çeşitli istemcilerde belirli bir kullanıcı deneyimi düzeyine ulaşmak için hizmet mantığının karmaşıklığı ve istemci tarafındaki karmaşıklık arasında bir zorunluluğunu getirir vardır.

Seçiminizi yapmak için aşağıdakileri göz önünde bulundurun:

* PlayReady, her Windows cihazında, bazı Android cihazlarda yerel olarak uygulanır ve neredeyse tüm platformlarda yazılım SDK 'Ları aracılığıyla kullanılabilir.
* Widevine, her Android cihazında, Chrome 'da ve diğer bazı cihazlarda yerel olarak uygulanır.
* FairPlay yalnızca iOS ve Mac OS istemcilerde veya iTunes üzerinden kullanılabilir.

Tipik bir çoklu DRM için iki seçenek vardır:

* PlayReady ve Widevine
* PlayReady, Widevine ve FairPlay

## <a name="a-reference-design"></a>Başvuru tasarımı
Bu bölümde, bunu uygulamak için kullanılan teknolojilerde belirsiz bir başvuru tasarımı sunulmaktadır.

DRM alt sistemi aşağıdaki bileşenleri içerebilir:

* Anahtar yönetimi
* DRM paketleme
* DRM lisansı verme
* Yetkilendirme denetimi
* Kimlik doğrulama/yetkilendirme
* Oynatıcı
* Kaynak/içerik teslim ağı (CDN)

Aşağıdaki diyagramda, bir DRM alt sistemindeki bileşenler arasındaki üst düzey etkileşim gösterilmektedir:

![CENC ile DRM alt sistemi](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Tasarımda üç temel katman vardır:

* Arka ofis katmanı (siyah) dışarıdan gösterilmez.
* Bir DMZ katmanı (koyu mavi), herkese açık olan tüm uç noktaları içerir.
* Genel internet katmanı (açık mavi), genel İnternet genelinde trafiği olan CDN ve oynatıcılar içerir.

Ayrıca, bu dosyanın statik veya dinamik şifreleme olmasına bakılmaksızın DRM korumasını denetlemek için bir içerik yönetim aracı olmalıdır. DRM şifrelemesi için girişler şunları içerir:

* MBR video içeriği
* İçerik anahtarı
* Lisans alma URL 'Leri

Kayıttan yürütme süresi boyunca üst düzey akış aşağıda verilmiştir:

* Kullanıcının kimliği doğrulanır.
* Kullanıcı için bir yetkilendirme belirteci oluşturulur.
* DRM korumalı içerik (bildirim), yürütücüye indirilir.
* Oynatıcı, bir anahtar KIMLIĞI ve yetkilendirme belirteciyle birlikte lisans sunucularına bir lisans alma isteği gönderir.

Aşağıdaki bölümde, anahtar yönetiminin tasarımı açıklanmaktadır.

| **ContentKey-varlık** | **Senaryo** |
| --- | --- |
| 1--1 |En basit durum. En yoğun denetimi sağlar. Ancak bu düzenleme genellikle en yüksek lisans teslimat maliyetine neden olur. En azından, korunan her varlık için bir lisans isteği gerekir. |
| 1-çok |Birden çok varlık için aynı içerik anahtarını kullanabilirsiniz. Örneğin, bir mantıksal gruptaki tüm varlıklar için (örn. bir tarz veya bir tarz alt kümesi (veya film gene), tek bir içerik anahtarı kullanabilirsiniz. |
| Çoka çok-1 |Her varlık için birden çok içerik anahtarı gereklidir. <br/><br/>Örneğin, MPEG-DASH için çoklu DRM ve HLS için dinamik AES-128 şifrelemesi ile dinamik CENC koruması uygulamanız gerekiyorsa, iki ayrı içerik anahtarınız olması gerekir. Her içerik anahtarı kendi ContentKeyType öğesine ihtiyaç duyuyor. (Dinamik CENC koruması için kullanılan içerik anahtarı için ContentKeyType. CommonEncryption kullanın. Dinamik AES-128 şifrelemesi için kullanılan içerik anahtarı için ContentKeyType. EnvelopeEncryption kullanın.)<br/><br/>Diğer bir örnek olarak, DASH içeriğinin CENC korumasında, teorik olarak, video akışını korumak için bir içerik anahtarı ve ses akışını korumak için başka bir içerik anahtarı kullanabilirsiniz. |
| Çok-çok |Önceki iki senaryonun birleşimi. Aynı varlık grubundaki birden çok varlık için bir dizi içerik anahtarı kullanılır. |

Dikkate alınması gereken diğer önemli etken, kalıcı ve kalıcı olmayan lisansların kullanılması.

Bu hususlar neden önemlidir?

Lisans teslimi için genel bir bulut kullanıyorsanız, kalıcı ve kalıcı olmayan lisanslarda lisans teslimat maliyeti üzerinde doğrudan bir etkisi olur. Aşağıdaki iki farklı tasarım çalışması şunları göstermeye yönelik olarak tasarlanmıştır:

* Aylık abonelik: kalıcı bir lisans ve 1 ' den fazla içerik arasında anahtar-varlık eşleştirmesi kullanın. Örneğin, tüm çocuk filmlerine şifreleme için tek bir içerik anahtarı kullanıyoruz. Bu durumda:

    Tüm çocukların filmler/cihaz = 1 için istenen toplam lisans sayısı

* Aylık abonelik: içerik anahtarı ve varlık arasında kalıcı olmayan bir lisans ve 1 ila 1 eşleme kullanın. Bu durumda:

    Tüm çocukların filmler/cihaz = [izlenen Film sayısı] x [oturum sayısı] için istenen toplam lisans sayısı

İki farklı tasarım, çok farklı lisans isteği desenleriyle sonuçlanır. Lisans teslimi hizmeti Media Services gibi bir genel bulut tarafından sağlanıyorsa farklı desenler farklı lisans teslimi maliyetine neden olur.

## <a name="map-design-to-technology-for-implementation"></a>Uygulamayı uygulama için teknolojiye eşleyin
Daha sonra, genel tasarım, her yapı bloğu için hangi teknolojinin kullanılacağını belirterek Azure/Media Services platformunda teknolojilerle eşlenir.

Aşağıdaki tabloda eşleme gösterilmektedir.

| **Yapı taşı** | **Teknoloji** |
| --- | --- |
| **Player** (Oyuncu) |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Kimlik sağlayıcısı (ıDP)** |Azure Active Directory (Azure AD) |
| **Güvenlik belirteci hizmeti (STS)** |Azure AD |
| **DRM koruması iş akışı** |Dinamik koruma Media Services |
| **DRM lisansı verme** |* Media Services lisans teslimi (PlayReady, Widevine, FairPlay) <br/>* Axinom lisans sunucusu <br/>* Özel PlayReady lisans sunucusu |
| **Tıdır** |Media Services akış uç noktası |
| **Anahtar yönetimi** |Başvuru uygulamasında gerekli değildir |
| **İçerik yönetimi** |Bir C# konsol uygulaması |

Diğer bir deyişle, Azure AD ile ıDP ve STS birlikte kullanılır. Oynatıcı için [Azure Media Player API 'si](https://amp.azure.net/libs/amp/latest/docs/) kullanılır. Hem Media Services hem de Media Player, çoklu DRM ile DASH ve CENC 'yi destekler.

Aşağıdaki diyagramda, önceki teknoloji eşlemesiyle birlikte genel yapı ve akış gösterilmektedir:

![Media Services CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Dinamik CENC şifrelemesini ayarlamak için, içerik yönetim aracı aşağıdaki girişleri kullanır:

* İçeriği aç
* Anahtar oluşturma/yönetimden içerik anahtarı
* Lisans alma URL 'Leri
* Azure AD 'den bilgi listesi

İçerik yönetim aracının çıktısı aşağıda verilmiştir:

* ContentKeyAuthorizationPolicy, lisans tesliminin bir JSON Web Token (JWT) ve DRM lisans belirtimlerini nasıl doğruladığına ilişkin belirtimi içerir.
* AssetDeliveryPolicy, akış biçimi, DRM koruması ve lisans alımı URL 'Leri hakkında özellikler içerir.

İşte çalışma zamanı sırasında akış:

* Kullanıcı kimlik doğrulamasından sonra bir JWT oluşturulur.
* JWT içinde yer alan taleplerden biri, EntitledUserGroup Grup nesnesi KIMLIĞINI içeren bir grup talebi. Bu talep, Yetkilendirme denetimini geçirmek için kullanılır.
* Oynatıcı, CENC korumalı içeriğin istemci bildirimini indirir ve aşağıdakileri tanımlar:
   * Anahtar KIMLIĞI.
   * İçerik CENC korumalıdır.
   * Lisans alma URL 'Leri.
* Oynatıcı, desteklenen tarayıcıyı/DRM 'yi temel alan bir lisans alma isteği yapar. Lisans alma isteğinde, anahtar KIMLIĞI ve JWT de gönderilir. Lisans teslimi hizmeti, JWT ve gerekli lisans vermeden önce içerilen talepleri doğrular.

## <a name="implementation"></a>Uygulama
### <a name="implementation-procedures"></a>Uygulama yordamları
Uygulama aşağıdaki adımları içerir:

1. Test varlıklarını hazırlayın. Media Services içinde çoklu bit hızlı parçalanmış MP4 için bir test videosunu kodlayın/paketleyin. Bu varlık DRM korumalı *değil* . DRM koruması, dinamik koruma tarafından daha sonra yapılır.

2. Anahtar KIMLIĞI ve içerik anahtarı (isteğe bağlı olarak bir anahtar tohum 'dan) oluşturun. Bu örnekte, birkaç test varlığı için yalnızca tek bir anahtar KIMLIĞI ve içerik anahtarı gerektiğinden, anahtar yönetim sistemi gerekli değildir.

3. Test varlığı için çoklu DRM lisans teslimat hizmetlerini yapılandırmak üzere Media Services API 'sini kullanın. Şirketiniz veya şirketinizin satıcısı tarafından Media Services Lisans Hizmetleri yerine özel lisans sunucuları kullanıyorsanız, bu adımı atlayabilirsiniz. Lisans alımı yapılandırırken, adımda lisans edinme URL 'Lerini belirtebilirsiniz. Farklı DRM lisans Hizmetleri için yetkilendirme ilkesi kısıtlaması ve lisans yanıt şablonları gibi bazı ayrıntılı yapılandırmaların belirtilmesi için Media Services API 'SI gereklidir. Bu süre içinde Azure portal, bu yapılandırma için gerekli Kullanıcı arabirimini sağlamaz. API düzeyi bilgi ve örnek kod için bkz. [PlayReady ve/veya Widevine dinamik ortak şifreleme kullanma](media-services-protect-with-playready-widevine.md).

4. Test varlığı için varlık teslim ilkesini yapılandırmak üzere Media Services API 'sini kullanın. API düzeyi bilgi ve örnek kod için bkz. [PlayReady ve/veya Widevine dinamik ortak şifreleme kullanma](media-services-protect-with-playready-widevine.md).

5. Azure 'da bir Azure AD kiracısı oluşturun ve yapılandırın.

6. Azure AD kiracınızda birkaç kullanıcı hesabı ve grubu oluşturun. En az bir "hak kazanır" grubu oluşturun ve bu gruba bir kullanıcı ekleyin. Bu gruptaki kullanıcılar, lisans alımı ' nda Yetkilendirme denetimini geçer. Bu grupta bulunmayan kullanıcılar kimlik doğrulama denetimini geçemez ve lisans alamaz. Bu "hak sahibi" grubundaki üyelik, Azure AD tarafından verilen JWT 'daki gerekli gruplar talebinde bulunur. Bu talep gereksinimini, çoklu DRM lisans teslim hizmetlerini yapılandırırken bu adımda belirtirsiniz.

7. Video yürütücünüzü barındırmak için bir ASP.NET MVC uygulaması oluşturun. Bu ASP.NET uygulaması, Azure AD kiracısında Kullanıcı kimlik doğrulamasıyla korunuyor. Kullanıcı kimlik doğrulamasından sonra alınan erişim belirteçlerine uygun talepler dahildir. Bu adım için OpenID Connect API 'SI önerilir. Aşağıdaki NuGet paketlerini yükleyin:

   * Microsoft. Azure. ActiveDirectory. GraphClient Install-Package
   * Microsoft. Owin. Security. Openıdconnect Install-Package
   * Microsoft. Owin. Security. Cookies Install-Package
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Microsoft. IdentityModel. clients. ActiveDirectory Install-Package

8. [Azure MEDIA Player API](https://amp.azure.net/libs/amp/latest/docs/)'sini kullanarak bir oyuncu oluşturun. Farklı DRM platformlarında hangi DRM teknolojisinin kullanılacağını belirtmek için [Azure Media Player Protectionınfo API](https://amp.azure.net/libs/amp/latest/docs/) 'sini kullanın.

9. Aşağıdaki tabloda test matrisi gösterilmektedir.

    | **DRM** | **Tarayıcı** | **Sorumlu kullanıcı için sonuç** | **Sorumlu olmayan kullanıcı için sonuç** |
    | --- | --- | --- | --- |
    | **PlayReady** |Windows 10 ' da Microsoft Edge veya Internet Explorer 11 |Başarıya |Başarısız |
    | **Widevine** |Chrome, Firefox, Opera |Başarıya |Başarısız |
    | **FairPlay** |MacOS üzerinde Safari      |Başarıya |Başarısız |
    | **AES-128** |En modern tarayıcılar  |Başarıya |Başarısız |

ASP.NET MVC oynatıcı uygulaması için Azure AD ayarlama hakkında daha fazla bilgi için bkz. [Azure Active Directory ile Azure Media Services OWIN MVC tabanlı uygulamayı tümleştirme ve JWT taleplerine göre içerik anahtarı teslimini kısıtlama](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Daha fazla bilgi için, [Azure Media Services ve dinamik şifrelemede JWT belirteci kimlik doğrulaması](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)konusuna bakın.  

Azure AD hakkında bilgi için:

* Geliştirici bilgilerini [Azure Active Directory geliştirici kılavuzunda](../../active-directory/azuread-dev/v1-overview.md)bulabilirsiniz.
* Yönetici bilgilerini, [Azure AD kiracı dizininizi yönetme](../../active-directory/fundamentals/active-directory-whatis.md)bölümünde bulabilirsiniz.

### <a name="some-issues-in-implementation"></a>Uygulamadaki bazı sorunlar
Uygulama sorunlarıyla ilgili yardım için aşağıdaki sorun giderme bilgilerini kullanın.

* Veren URL 'SI "/" ile bitmelidir. Hedef kitle, oynatıcı uygulaması istemci KIMLIĞI olmalıdır. Ayrıca, veren URL 'sinin sonuna "/" ekleyin.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    [JWT kod çözücüde](http://jwt.calebb.net/), JWT içinde gösterildiği gibi **AUD** ve **ISS**' yi görürsünüz:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Uygulamanın **Yapılandır** SEKMESINDE Azure AD 'de uygulamaya izinler ekleyin. Her uygulama için hem yerel hem de dağıtılan sürümler için izinler gereklidir.

    ![İzinler](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Dinamik CENC korumasını ayarlarken doğru sertifikayı kullanın.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    Aşağıdakiler çalışmaz:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    GUID, Azure AD kiracı KIMLIĞIDIR. GUID, Azure portal **uç noktalar** açılır menüsünde bulunabilir.

* Grup üyeliği talep ayrıcalıkları verin. Aşağıdakilerin Azure AD uygulama bildirimi dosyasında bulunduğundan emin olun: 

    "Groupmembershipclaim": "All" (varsayılan değer null)

* Kısıtlama gereksinimleri oluştururken uygun TokenType öğesini ayarlayın.

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    SWT (ACS) ' ye ek olarak JWT (Azure AD) desteği eklediğiniz için varsayılan TokenType, TokenType. JWT ' dir. SWT/ACS kullanırsanız, belirteci TokenType. SWT olarak ayarlamanız gerekir.

## <a name="additional-topics-for-implementation"></a>Uygulama için ek konular
Bu bölümde, tasarım ve uygulama ile ilgili bazı ek konular açıklanmaktadır.

### <a name="http-or-https"></a>HTTP veya HTTPS?
ASP.NET MVC oynatıcı uygulamasının şunları desteklemesi gerekir:

* HTTPS altında olan Azure AD aracılığıyla Kullanıcı kimlik doğrulaması.
* HTTPS altında olan istemci ile Azure AD arasında JWT Exchange.
* Lisans teslimi Media Services tarafından sağlanıyorsa, istemci tarafından, HTTPS 'nin altında olması gereken DRM lisans alımı. PlayReady ürün paketi, lisans teslimi için HTTPS 'yi desteklemez. PlayReady lisans sunucunuz Media Services dışındaysa, HTTP ya da HTTPS kullanabilirsiniz.

ASP.NET Player uygulaması en iyi uygulama olarak HTTPS kullanır, bu nedenle Media Player HTTPS altındaki bir sayfada bulunur. Ancak, HTTP akış için tercih edilir, bu nedenle karma içerik sorununu göz önünde bulundurmanız gerekir.

* Tarayıcı karışık içeriğe izin vermez. Ancak, sorunsuz ve kesik çizgi için Silverlight ve OSMF eklentisi gibi eklentiler buna izin verir. Karışık içerik, müşteri verilerinin riskli olmasına neden olabilecek kötü amaçlı JavaScript ekleme yeteneğinin tehdidi nedeniyle güvenlik konusudur. Tarayıcılar bu özelliği varsayılan olarak engeller. Bu soruna geçici bir çözüm olarak, tüm etki alanlarına izin vererek (HTTPS veya HTTP 'den bağımsız olarak) sunucu (Origin) tarafında çalışır. Bu muhtemelen iyi bir fikir değildir.
* Karışık içerikten kaçının. Oynatıcı uygulaması ve Media Player her ikisi de HTTP veya HTTPS kullanmalıdır. Karışık içerik oynatılırken, silverlightSS Tech bir karma içerik uyarısının temizlenmesini gerektirir. FlashSS Tech karışık içeriği bir karma içerik uyarısı olmadan işler.
* Akış uç noktanız 2014 Ağustos 'Tan önce oluşturulduysa, HTTPS 'yi desteklemez. Bu durumda, HTTPS için yeni bir akış uç noktası oluşturun ve kullanın.

DRM korumalı içerik için başvuru uygulamasında, hem uygulama hem de akış HTTPS altındadır. Açık içerik için Player kimlik doğrulama veya bir lisansa ihtiyaç duymazsa, HTTP veya HTTPS kullanabilirsiniz.

### <a name="azure-active-directory-signing-key-rollover"></a>İmzalama anahtarı geçişi Azure Active Directory
Anahtar geçişi imzalama, uygulamanızda dikkate alınması gereken önemli bir noktadır. Bunu yoksayabilirsiniz, son olarak en fazla altı hafta içinde tamamlanmış sistem tamamen çalışmayı sonlandırır.

Azure AD, Azure AD kullanan uygulamalar arasında güven sağlamak için endüstri standartları kullanır. Özellikle, Azure AD ortak ve özel anahtar çiftinden oluşan bir imzalama anahtarı kullanır. Azure AD Kullanıcı hakkında bilgi içeren bir güvenlik belirteci oluşturduğunda, uygulamaya geri gönderilmeden önce Azure AD tarafından özel bir anahtarla imzalanır. Belirtecin geçerli olduğunu ve Azure AD 'den geldiğini doğrulamak için, uygulamanın belirtecin imzasını doğrulaması gerekir. Uygulama, kiracının Federasyon meta veri belgesinde bulunan Azure AD tarafından kullanıma sunulan ortak anahtarı kullanır. Bu ortak anahtar ve ondan türetilen imzalama anahtarı, Azure AD 'deki tüm kiracılar için kullanılan aynı.

Azure AD anahtar geçişi hakkında daha fazla bilgi için bkz. [Azure AD 'de anahtar geçişi imzalama hakkında önemli bilgiler](../../active-directory/develop/active-directory-signing-key-rollover.md).

[Ortak-özel anahtar çifti](https://login.microsoftonline.com/common/discovery/keys/)arasında:

* Özel anahtar, Azure AD tarafından bir JWT oluşturmak için kullanılır.
* Ortak anahtar, JWT 'yi doğrulamak için Media Services DRM lisans teslimat hizmetleri gibi bir uygulama tarafından kullanılır.

Azure AD, güvenlik nedeniyle sertifikayı düzenli aralıklarla döndürür (altı haftada bir). Güvenlik ihlallerine karşı, anahtar geçişi herhangi bir zamanda gerçekleşebilir. Bu nedenle, Media Services içindeki lisans teslimi Hizmetleri, Azure AD 'nin anahtar çiftini döndüğü şekilde kullanılan ortak anahtarı güncelleştirmesi gerekir. Aksi takdirde Media Services 'de belirteç kimlik doğrulaması başarısız olur ve lisans verilmez.

Bu hizmeti ayarlamak için, DRM lisans teslimat hizmetleri 'ni yapılandırırken TokenRestrictionTemplate. Openıdconnectdiscoverydocument ayarlarsınız.

JWT akışı şöyledir:

* Azure AD, kimliği doğrulanmış bir kullanıcı için geçerli özel anahtarla JWT 'yi yayınlar.
* Bir oyuncu çoklu DRM korumalı içeriği olan bir CENC 'yi gördüğünde, önce Azure AD tarafından verilen JWT 'yi bulur.
* Oyuncu, Media Services sürümünde lisans Teslim Hizmetleri için JWT ile bir lisans alma isteği gönderir.
* Media Services lisans Teslim Hizmetleri, lisansları vermeden önce JWT 'yi doğrulamak için Azure AD 'den geçerli/geçerli ortak anahtarı kullanır.

DRM lisans teslimat hizmetleri her zaman Azure AD 'den geçerli/geçerli ortak anahtarı kontrol edin. Azure AD tarafından sunulan ortak anahtar, Azure AD tarafından verilen bir JWT 'yi doğrulamak için kullanılan anahtardır.

Azure AD 'den sonra anahtar geçişi gerçekleşiyorsa, ancak kimlik doğrulama için Media Services ' deki DRM lisans teslimat hizmetleri 'ne oyuncu tarafından gönderilmeden önce ne olur?

Bir anahtar herhangi bir anda alınırsa, Federasyon meta verileri belgesinde birden fazla geçerli ortak anahtar her zaman kullanılabilir. Media Services lisans teslimi, belgede belirtilen anahtarlardan herhangi birini kullanabilir. Bir anahtar yakında alınabilir, diğeri de bunun yerini alır ve bu şekilde devam edebilir.

### <a name="where-is-the-access-token"></a>Erişim belirteci nerede?
Bir Web uygulamasının, [OAuth 2,0 istemci kimlik bilgileri verme Ile uygulama kimliği](../../active-directory/azuread-dev/web-api.md)altında bir API uygulamasını nasıl çağırdığı hakkında bakarsanız, kimlik doğrulama akışı aşağıdaki gibidir:

* Kullanıcı, Web uygulamasında Azure AD 'de oturum açar. Daha fazla bilgi için Web [tarayıcısı Web uygulamasına](../../active-directory/azuread-dev/web-app.md)bakın.
* Azure AD yetkilendirme uç noktası, Kullanıcı aracısını bir yetkilendirme kodu ile istemci uygulamasına yeniden yönlendirir. Kullanıcı Aracısı, kimlik doğrulama kodunu istemci uygulamanın yeniden yönlendirme URI 'sine döndürür.
* Web uygulamasının, Web API 'sinin kimliğini doğrulayabilmesi ve istenen kaynağı alabilmesi için bir erişim belirteci alması gerekir. Azure AD belirteç uç noktasına bir istek yapar ve kimlik bilgisini, istemci KIMLIĞINI ve Web API 'sinin uygulama KIMLIĞI URI 'sini sağlar. Kullanıcı tarafından onaylanan yetkilendirme kodunu temsil eder.
* Azure AD uygulamanın kimliğini doğrular ve Web API 'sini çağırmak için kullanılan bir JWT erişim belirteci döndürür.
* HTTPS üzerinden Web uygulaması, Web API 'sine isteğin "Authorization" başlığına sahip JWT dizesini eklemek için döndürülen JWT erişim belirtecini kullanır. Web API 'SI daha sonra JWT 'yi doğrular. Doğrulama başarılı olursa, istenen kaynağı döndürür.

Bu uygulama kimliği akışında, Web API 'SI Web uygulamasının kimliğinin doğrulandığını güvendiğinde. Bu nedenle, bu modele güvenilen alt sistem denir. [Yetkilendirme akış diyagramı](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) , yetkilendirme kodu-verme akışının nasıl çalıştığını açıklar.

Belirteç kısıtlaması ile lisans alımı aynı güvenilir alt sistem düzenine uyar. Media Services içindeki lisans teslimi hizmeti Web API kaynağıdır veya bir Web uygulamasının erişmesi gereken "arka uç kaynağıdır". Erişim belirteci nerede?

Erişim belirteci Azure AD 'den alınır. Başarılı Kullanıcı kimlik doğrulamasından sonra, bir yetkilendirme kodu döndürülür. Daha sonra yetkilendirme kodu, erişim belirteci için Exchange 'e istemci KIMLIĞI ve uygulama anahtarı ile birlikte kullanılır. Erişim belirteci, Media Services lisans teslim hizmetini işaret eden veya temsil eden bir "Pointer" uygulamasına erişmek için kullanılır.

İşaretçi uygulamasını Azure AD 'de kaydetmek ve yapılandırmak için aşağıdaki adımları uygulayın:

1. Azure AD kiracısında:

   * Oturum açma URL 'SI https://[resource_name]. azurewebsites. NET/ile bir uygulama (kaynak) ekleyin. 
   * Https://[aad_tenant_name]. onmicrosoft. com/[resource_name] URL 'sine sahip bir uygulama KIMLIĞI ekleyin.

2. Kaynak uygulama için yeni bir anahtar ekleyin.

3. Groupmembershipclaim özelliğinin "Groupmembershipclaim" değerine sahip olması için uygulama bildirim dosyasını güncelleştirin: "All".

4. Oynatıcı Web uygulamasına işaret eden Azure AD uygulamasında, **diğer uygulamalara yönelik izinler** bölümünde, 1. adımda eklenen kaynak uygulamasını ekleyin. **Temsilci izni** altında, **erişim [resource_name]** öğesini seçin. Bu seçenek, Web uygulamasına kaynak uygulamasına erişen erişim belirteçleri oluşturma izni verir. Visual Studio ve Azure Web uygulaması ile geliştirirseniz, Web uygulamasının hem yerel hem de dağıtılan sürümünde bunu yapın.

Azure AD tarafından verilen JWT, işaretçi kaynağına erişmek için kullanılan erişim belirtecidir.

### <a name="what-about-live-streaming"></a>Canlı akış hakkında ne olacak?
Önceki tartışma, isteğe bağlı varlıklara odaklanılmıştır. Canlı akış hakkında ne olacak?

Bir programla ilişkili varlığı bir VOD varlığı olarak düşünerek Media Services canlı akışı korumak için tam olarak aynı tasarımı ve uygulamayı kullanabilirsiniz.

Özellikle de Media Services canlı akış yapmak için kanal oluşturmanız ve ardından kanal altında bir program oluşturmanız gerekir. Programı oluşturmak için, programın canlı arşivini içeren bir varlık oluşturmanız gerekir. Canlı içeriğin çok DRM koruması olan CENC 'yi sağlamak için, programa başlamadan önce aynı kurulum/işlemeyi bir VOD varlığı gibi kıymete uygulayın.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services dışında lisans sunucuları ne olacak?
Genellikle, müşteriler bir lisans sunucu grubuna kendi veri merkezinde veya DRM hizmet sağlayıcıları tarafından barındırılan bir şekilde yatırım yapmış olur. Media Services Content Protection ile karma modda çalıştırabilirsiniz. DRM lisansları Media Services dışındaki sunucular tarafından teslim edilirken, içerikler Media Services içinde barındırılabilir ve dinamik olarak korunabilir. Bu durumda, aşağıdaki değişiklikleri göz önünde bulundurun:

* STS 'nin kabul edilebilir ve lisans sunucusu grubu tarafından doğrulanabildiği belirteçleri vermesi gerekir. Örneğin, Axinom tarafından sunulan Widevine lisans sunucuları, bir yetkilendirme iletisi içeren belirli bir JWT gerektirir. Bu nedenle, bu tür bir JWT vermek için bir STS 'ye sahip olmanız gerekir. Bu uygulama türü hakkında daha fazla bilgi için [Azure belge merkezi](https://azure.microsoft.com/documentation/) 'ne gidin ve [Azure Media Services Widevine lisansları teslim etmek Için Axinom kullanma](media-services-axinom-integration.md)makalesine bakın.
* Artık Media Services lisans teslim hizmetini (ContentKeyAuthorizationPolicy) yapılandırmanız gerekmez. Multi-DRM ile CENC 'yi ayarlamak için AssetDeliveryPolicy 'ı yapılandırırken lisans alma URL 'Lerini (PlayReady, Widevine ve FairPlay için) sağlamanız gerekir.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Özel STS kullanmak istersem ne yapmalıyım?
Bir müşteri, JWTs sağlamak için özel bir STS kullanmayı tercih edebilir. Nedenler şunlardır:

* Müşteri tarafından kullanılan ıDP STS 'yi desteklemez. Bu durumda, özel bir STS bir seçenek olabilir.
* Müşteri, STS 'nin müşterinin abone faturalandırma sistemiyle tümleştirileceği daha esnek veya daha sıkı bir denetim gerektirebilir. Örneğin, bir MVPD işleci, Premium, temel ve spor gibi birden çok OTT abone paketi sunabilir. İşleci, yalnızca belirli bir paketteki içeriklerin kullanılabilir hale getirilmesinden dolayı bir söz konusu belirteçteki talepleri bir abonenin paketiyle eşleştirmek isteyebilir. Bu durumda, özel bir STS gereken esnekliği ve denetimi sağlar.

Özel bir STS kullandığınızda, iki değişikliğin yapılması gerekir:

* Bir varlık için lisans teslim hizmetini yapılandırırken, Azure AD 'den geçerli anahtar yerine özel STS tarafından doğrulama için kullanılan güvenlik anahtarını belirtmeniz gerekir. (Daha fazla ayrıntı izleyin.) 
* JTW belirteci oluşturulduğunda, Azure AD 'de geçerli x509 sertifikasının özel anahtarı yerine bir güvenlik anahtarı belirtilir.

İki tür güvenlik anahtarı vardır:

* Simetrik anahtar: bir JWT oluşturmak ve doğrulamak için aynı anahtar kullanılır.
* Asimetrik anahtar: bir x509 sertifikasında ortak özel anahtar çifti, bir JWT şifrelemek/oluşturmak için özel anahtarla ve belirteci doğrulamak için ortak anahtarla birlikte kullanılır.

> [!NOTE]
> Geliştirme platformunuz olarak .NET Framework/C# kullanıyorsanız, asimetrik güvenlik anahtarı için kullanılan x509 sertifikası en az 2048 anahtar uzunluğuna sahip olmalıdır. Bu, .NET Framework içinde System. IdentityModel. Tokens. X509AsymmetricSecurityKey sınıfının bir gereksinimidir. Aksi takdirde, aşağıdaki özel durum oluşturulur:
> 
> IDX10630: imzalama için ' System. IdentityModel. Tokens. X509AsymmetricSecurityKey ', ' 2048 ' bitten küçük olamaz.

## <a name="the-completed-system-and-test"></a>Tamamlanan sistem ve test
Bu bölümde, oturum açma hesabı almadan önce davranışın temel bir resmine sahip olmak için tamamlanmış uçtan uca sistemde aşağıdaki senaryolarda izlenecek yol gösterilmektedir:

* Tümleşik olmayan bir senaryoya ihtiyacınız varsa:

    * Korumasız veya DRM korumalı, ancak belirteç kimlik doğrulaması olmadan (bir lisans veren) Media Services barındırılan video varlıkları için oturum açmadan test edebilirsiniz. Video akışınızda HTTP üzerinden olması durumunda HTTP 'ye geçin.

* Uçtan uca tümleşik bir senaryoya ihtiyacınız varsa:

    * Media Services sürümünde dinamik DRM koruması kapsamındaki video varlıkları için, belirteç kimlik doğrulaması ve Azure AD tarafından oluşturulan JWT ile oturum açmanız gerekir.

Oynatıcı Web uygulaması ve oturum açma için [Bu Web sitesine](https://openidconnectweb.azurewebsites.net/)bakın.

### <a name="user-sign-in"></a>Kullanıcı oturumu açma
Uçtan uca tümleşik DRM sistemini test etmek için oluşturulmuş veya eklenmiş bir hesabınız olması gerekir.

Hesap nedir?

Azure tarafından yalnızca Microsoft hesabı kullanıcıları tarafından erişime izin verilse de, kullanıcılar artık her iki sistem tarafından erişime izin verilir. Azure AD, Azure AD kimlik doğrulaması için artık güvenilir ve Azure AD kuruluş kullanıcılarının kimliğini doğrular. Azure AD 'nin tüketici kullanıcılarının kimliğini doğrulamak için Microsoft hesabı tüketici kimliği sistemine güvendiği bir Federasyon ilişkisi oluşturulmuştur. Sonuç olarak, Azure AD, Konuk Microsoft hesaplarının yanı sıra yerel Azure AD hesapları için kimlik doğrulaması yapabilir.

Azure AD Microsoft hesabı etki alanına güventiğinden, aşağıdaki etki alanlarından herhangi bir hesabı özel Azure AD kiracısına ekleyebilir ve hesabı kullanarak oturum açabilirsiniz:

| **Etki alanı adı** | **Etki alanı** |
| --- | --- |
| **Özel Azure AD kiracı etki alanı** |somename.onmicrosoft.com |
| **Kurumsal etki alanı** |microsoft.com |
| **Microsoft hesabı etki alanı** |outlook.com, live.com, hotmail.com |

Sizin için bir hesap oluşturulmasını veya eklenmesini sağlamak üzere yazarlarla herhangi biriyle iletişim sağlayabilirsiniz.

Aşağıdaki ekran görüntülerinde farklı etki alanı hesapları tarafından kullanılan farklı oturum açma sayfaları gösterilmektedir:

**Özel Azure AD kiracı etki alanı hesabı**: özel Azure AD kiracı etki alanının özelleştirilmiş oturum açma sayfası.

![Özel Azure A D kiracı etki alanının özelleştirilmiş oturum açma sayfasını gösteren ekran görüntüsü.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Akıllı kart Ile Microsoft etki alanı hesabı**: MICROSOFT kurumsal BT tarafından iki öğeli kimlik doğrulama ile özelleştirilmiş oturum açma sayfası.

![İki öğeli kimlik doğrulama ile Microsoft Kurumsal ı T tarafından özelleştirilmiş oturum açma sayfasını gösteren ekran görüntüsü.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft hesabı**: tüketiciler için Microsoft hesabı oturum açma sayfası.

![Özel Azure AD kiracı etki alanı hesabı](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>PlayReady için şifreli medya uzantıları kullanma
Windows 8.1 veya üzeri sürümlerde Internet Explorer 11 ve Windows 10 ' da Microsoft Edge tarayıcısı gibi PlayReady desteği için şifreli Medya Uzantıları (EME) ile modern bir tarayıcıda, PlayReady, EME için temel alınan DRM 'dir.

![PlayReady için EME kullanma](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Koyu oynatıcı alanı, PlayReady korumasının, korunan videonun ekran yakalanmasını istemelerini engeller.

Aşağıdaki ekran görüntüsünde, oynatıcı eklentileri ve Microsoft Security Essentials (MSE)/EME desteği gösterilmektedir:

![PlayReady için oynatıcı eklentileri](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10 ' da Microsoft Edge ve Internet Explorer 11 ' deki EME, [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) 'in onu destekleyen Windows 10 cihazlarında çağrılmasını sağlar. PlayReady SL3000, gelişmiş Premium içeriğin (4K, HDR) ve yeni içerik teslim modellerinin (Gelişmiş içerik için) akışını kaldırır.

Windows cihazlarına odaklanmak için PlayReady, Windows cihazlarında (PlayReady SL3000) kullanılabilen tek DRM 'dir. Bir akış hizmeti, PlayReady aracılığıyla veya Evrensel Windows Platformu bir uygulamayla PlayReady kullanabilir ve başka bir DRM 'den PlayReady SL3000 kullanarak daha yüksek bir video kalitesi sunabilir. Genellikle, Microsoft Edge/Internet Explorer 11 veya aynı cihazdaki Evrensel Windows Platformu bir uygulama aracılığıyla en çok 10 ' a kadar akış akışı sağlar. Miktar, hizmet ayarlarına ve uygulamaya göre değişir.

#### <a name="use-eme-for-widevine"></a>Widevine için EME kullanma
Android 4.4.4 üzerinde Windows 10, Windows 8.1, Mac OSX Yosemite ve Chrome 'daki Chrome 41 + gibi EME/Widevine desteğiyle modern bir tarayıcıda, Google Widevine, EME 'nin arkasındaki DRM 'dir.

![Widevine için EME kullanma](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine, korunan videonun ekran yakalanmasını engellemenizi engellemez.

![Widevine için oynatıcı eklentileri](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Kullanıcıları hak edilmemiş
Kullanıcı "kullanıcılar" grubunun üyesi değilse, Kullanıcı Yetkilendirme denetimini geçirmez. Çoklu DRM lisans hizmeti ardından istenen lisansı gösterildiği gibi verilmesinin reddeder. Ayrıntılı açıklama, tasarlanan "lisans alma başarısız oldu" dır.

![Kullanıcıları hak edilmemiş](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Özel bir güvenlik belirteci hizmeti çalıştırma
Özel bir STS çalıştırırsanız, JWT bir simetrik ya da asimetrik anahtar kullanılarak özel STS tarafından verilir.

Aşağıdaki ekran görüntüsünde simetrik anahtar (Chrome kullanarak) kullanan bir senaryo gösterilmektedir:

![Simetrik anahtar ile özel STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Aşağıdaki ekran görüntüsünde, bir x509 sertifikası aracılığıyla asimetrik anahtar kullanan bir senaryo gösterilmektedir (Microsoft modern tarayıcı kullanılarak):

![Asimetrik anahtarla özel STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Her iki durumda da, Kullanıcı kimlik doğrulaması aynı kalır. Azure AD aracılığıyla gerçekleşir. Tek fark, JWTs 'nin Azure AD yerine özel STS tarafından verilme sayısıdır. Dinamik CENC korumasını yapılandırdığınızda, lisans teslimi hizmeti kısıtlaması, bir simetrik veya asimetrik anahtar olan JWT türünü belirtir.

## <a name="summary"></a>Özet

Bu belgede, belirteç kimlik doğrulaması, tasarımı ve Azure, Media Services ve Media Player kullanarak uygulama aracılığıyla çok yerel DRM ve erişim denetimi ile ilgili CENC ele alınmıştır.

* Bir DRM/CENC alt sistemindeki tüm gerekli bileşenleri içeren bir başvuru tasarımı sunulmuştur.
* Azure, Media Services ve Media Player bir başvuru uygulamasına sunulmuştur.
* Tasarım ve uygulamayla doğrudan ilgili bazı konular da ele alınmıştır.

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
