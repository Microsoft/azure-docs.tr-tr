---
title: Çoklu DRM içerik koruma sistemi
description: Bu makaleler, Azure Media Services ile birden çok DRM içerik koruma sisteminin nasıl tasarlanacağını ayrıntılı olarak açıklamaktadır.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 48ae7b2356f7313acbfb298b7a492a0482e3106b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068141"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Erişim denetimi ile çoklu DRM'ye sahip içerik koruma sistemi tasarlama

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Üst düzey (OTT) veya çevrimiçi akış çözümü için dijital Rights Management (DRM) alt sistemi tasarlama ve oluşturma karmaşık bir görevdir. Operatörler/çevrimiçi video sağlayıcıları bu görevi genellikle özelleştirilmiş DRM hizmeti sağlayıcılarına dış olarak sağlar. Bu belgenin amacı, bir OTT veya çevrimiçi akış çözümünde uçtan uca bir DRM alt sisteminin başvuru tasarımını ve başvuru uygulamasını sunmaktır.

Bu belgeye yönelik hedeflenen okuyucular, OTT 'nin DRM alt sistemlerinde veya DRM alt sistemleri ile ilgilenen çevrimiçi akış/çok ekranlı çözümlerin veya okuyucuların çalıştığı mühendislerdir. Bu varsayım, okuyucuların PlayReady, Widevine, FairPlay veya Adobe Access gibi pazardaki DRM teknolojilerinden en az birini öğrenme hızıdır.

Bu tartışmada, çoklu DRM tarafından Azure Media Services desteklenen 3 DRMs, PlayReady ve Widevine için Common Encryption (CENC), FairPlay ve AES-128 şifresiz anahtar şifrelemesi için de vardır. Çevrimiçi akış ve OTT sektöründe önemli bir eğilim, çeşitli istemci platformlarında yerel DRMs kullanmaktır. Bu eğilim, bir önceki, çeşitli istemci platformları için tek bir DRM ve istemci SDK 'sını kullanan bir vardiyadır. Multi-Native DRM ile CENC kullandığınızda hem PlayReady hem de Widevine [Common Encryption (ISO/ıec 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) belirtimi başına şifrelenir.

İçerik koruma için yerel çoklu DRM kullanmanın avantajları şunlardır:

* Tek bir şifreleme işlemi, kendi yerel Drtığından farklı platformları hedeflemek için kullanıldığından, şifreleme maliyetini azaltır.
* Depolama alanında yalnızca tek bir varlık kopyası gerektiğinden, varlıkları yönetme maliyetini azaltır.
* Yerel DRM istemcisi genellikle yerel platformunda ücretsiz olduğundan, DRM istemci lisanslama maliyetini ortadan kaldırır.

### <a name="goals-of-the-article"></a>Makalenin amaçları

Bu makalenin amaçları şunlardır:

* Tüm 3 DRMs 'Leri (DASH için CENC, for HLS için FairPlay ve kesintisiz akış için PlayReady) kullanan bir DRM alt sisteminin başvuru tasarımını sağlayın.
* Azure ve Azure Media Services platformunda bir başvuru uygulama sağlayın.
* Bazı tasarım ve uygulama konularını tartışın.

Aşağıdaki tabloda farklı platformlarda yerel DRM desteği ve farklı tarayıcılarda EME desteği özetlenmektedir.

| **İstemci platformu** | **Yerel DRM** | **EME** |
| --- | --- | --- |
| **Akıllı TV 'ler, STBs** | PlayReady, Widevine ve/veya diğer | PlayReady ve/veya Widevine için gömülü tarayıcı/EME|
| **Windows 10** | PlayReady | PlayReady için Microsoft Edge/ıE11|
| **Android cihazlar (telefon, tablet, TV)** |Widevine |Widevine için Chrome |
| **iOS** | FairPlay | FairPlay için Safari (iOS 11,2 ' den itibaren) |
| **macOS** | FairPlay | FairPlay için Safari (macOS X 10.11 + El Capitan 'da Safari 9 + bu yana)|
| **tvOS** | FairPlay | |

Her DRM için dağıtımın geçerli durumunu göz önünde bulundurarak, bir hizmet genellikle iki veya üç DRMs uygulamak istiyor ve en iyi şekilde tüm uç nokta türlerini adreslediğinizden emin olmanızı sağlar.

Çeşitli istemcilerde belirli bir kullanıcı deneyimi düzeyine ulaşmak için hizmet mantığının karmaşıklığı ve istemci tarafındaki karmaşıklık arasında bir zorunluluğunu getirir vardır.

Seçiminizi yapmak için aşağıdakileri göz önünde bulundurun:

* PlayReady, her Windows cihazında, bazı Android cihazlarda yerel olarak uygulanır ve neredeyse tüm platformlarda yazılım SDK 'Ları aracılığıyla kullanılabilir.
* Widevine, her Android cihazında, Chrome 'da ve diğer bazı cihazlarda yerel olarak uygulanır. Wıdevine aynı zamanda Firefox ve Opera tarayıcılarında DASH üzerinde de desteklenir.
* FairPlay, iOS, macOS ve tvOS üzerinde kullanılabilir.

## <a name="a-reference-design"></a>Başvuru tasarımı

Bu bölümde, bunu uygulamak için kullanılan teknolojilerde belirsiz bir başvuru tasarımı sunulmaktadır.

DRM alt sistemi aşağıdaki bileşenleri içerebilir:

* Anahtar yönetimi
* DRM şifreleme paketleme
* DRM lisansı verme
* Yetkilendirme denetimi/erişim denetimi
* Kullanıcı kimlik doğrulaması/yetkilendirme
* Oynatıcı uygulaması
* Kaynak/içerik teslim ağı (CDN)

Aşağıdaki diyagramda, bir DRM alt sistemindeki bileşenler arasındaki üst düzey etkileşim gösterilmektedir:

![CENC ile DRM alt sistemi](./media/architecture-design-multi-drm-system/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Güvenli belirteç hizmeti (STS)** |Azure AD |
| **DRM koruması iş akışı** |Dinamik koruma Azure Media Services |
| **DRM lisansı verme** |* Media Services lisans teslimi (PlayReady, Widevine, FairPlay) <br/>* Axinom lisans sunucusu <br/>* Özel PlayReady lisans sunucusu |
| **Tıdır** |Azure Media Services akış uç noktası |
| **Anahtar yönetimi** |Başvuru uygulamasında gerekli değildir |
| **İçerik yönetimi** |Bir C# konsol uygulaması |

Diğer bir deyişle, Azure AD tarafından her iki ıDP ve STS sağlanır. Oynatıcı için [Azure Media Player API 'si](https://amp.azure.net/libs/amp/latest/docs/) kullanılır. Hem Azure Media Services hem de Azure Media Player, FairPlay Over HLS, kesintisiz akış üzerinden PlayReady ve DASH, HLS ve Düzgünleştir için AES-128 şifrelemesini destekler.

Aşağıdaki diyagramda, önceki teknoloji eşlemesiyle birlikte genel yapı ve akış gösterilmektedir:

![Media Services CENC](./media/architecture-design-multi-drm-system/media-services-cenc-subsystem-on-AMS-platform.png)

İçerik yönetimi aracı, DRM içerik korumasını ayarlamak için aşağıdaki girişleri kullanır:

* İçeriği aç
* Anahtar yönetiminden içerik anahtarı
* Lisans alma URL 'Leri
* Azure AD 'deki, hedef kitle, veren ve belirteç talepleri gibi bilgilerin listesi

İçerik yönetim aracının çıktısı aşağıda verilmiştir:

* ContentKeyPolicy, kullanılan her DRM türü için DRM lisans şablonunu açıklar;
* ContentKeyPolicyRestriction, bir DRM lisansı verilmeden önce erişim denetimini açıklar
* Streamingpolicy, akış için DRM şifreleme modu-akış protokolü-kapsayıcı biçiminin çeşitli birleşimlerini açıklar
* Streamminglocator, şifreleme için kullanılan içerik anahtarını/IV ve akış URL 'Lerini açıklar 

İşte çalışma zamanı sırasında akış:

* Kullanıcı kimlik doğrulamasından sonra bir JWT oluşturulur.
* JWT içinde yer alan taleplerden biri, EntitledUserGroup Grup nesnesi KIMLIĞINI içeren bir grup talebi. Bu talep, Yetkilendirme denetimini geçirmek için kullanılır.
* Oynatıcı, CENC korumalı içeriğin istemci bildirimini indirir ve aşağıdakileri tanımlar:
   * Anahtar KIMLIĞI.
   * İçerik DRM korumalı.
   * Lisans alma URL 'Leri.
* Oynatıcı, desteklenen tarayıcıyı/DRM 'yi temel alan bir lisans alma isteği yapar. Lisans alma isteğinde, anahtar KIMLIĞI ve JWT de gönderilir. Lisans teslimi hizmeti, JWT ve gerekli lisans vermeden önce içerilen talepleri doğrular.

## <a name="implementation"></a>Uygulama
### <a name="implementation-procedures"></a>Uygulama yordamları
Uygulama aşağıdaki adımları içerir:

1. Test varlıklarını hazırlayın. Media Services içinde çoklu bit hızlı parçalanmış MP4 için bir test videosunu kodlayın/paketleyin. Bu varlık DRM korumalı *değil* . DRM koruması, dinamik koruma tarafından daha sonra yapılır.

2. Anahtar KIMLIĞI ve içerik anahtarı (isteğe bağlı olarak bir anahtar tohum 'dan) oluşturun. Bu örnekte, birkaç test varlığı için yalnızca tek bir anahtar KIMLIĞI ve içerik anahtarı gerektiğinden, anahtar yönetim sistemi gerekli değildir.

3. Test varlığı için çoklu DRM lisans teslimat hizmetlerini yapılandırmak üzere Media Services API 'sini kullanın. Şirketiniz veya şirketinizin satıcısı tarafından Media Services Lisans Hizmetleri yerine özel lisans sunucuları kullanıyorsanız, bu adımı atlayabilirsiniz. Lisans alımı yapılandırırken, adımda lisans edinme URL 'Lerini belirtebilirsiniz. Farklı DRM lisans Hizmetleri için yetkilendirme ilkesi kısıtlaması ve lisans yanıt şablonları gibi bazı ayrıntılı yapılandırmaların belirtilmesi için Media Services API 'SI gereklidir. Bu süre içinde Azure portal, bu yapılandırma için gerekli Kullanıcı arabirimini sağlamaz. API düzeyi bilgi ve örnek kod için bkz. [PlayReady ve/veya Widevine dinamik ortak şifreleme kullanma](drm-protect-with-drm-tutorial.md).

4. Test varlığı için varlık teslim ilkesini yapılandırmak üzere Media Services API 'sini kullanın. API düzeyi bilgi ve örnek kod için bkz. [PlayReady ve/veya Widevine dinamik ortak şifreleme kullanma](drm-protect-with-drm-tutorial.md).

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

* Geliştirici bilgilerini [Azure Active Directory geliştirici kılavuzunda](../../active-directory/develop/v2-overview.md)bulabilirsiniz.
* Yönetici bilgilerini, [Azure AD kiracı dizininizi yönetme](../../active-directory/fundamentals/active-directory-whatis.md)bölümünde bulabilirsiniz.

### <a name="some-issues-in-implementation"></a>Uygulamadaki bazı sorunlar

Uygulama sorunlarıyla ilgili yardım için aşağıdaki sorun giderme bilgilerini kullanın.

* Veren URL 'SI "/" ile bitmelidir. Hedef kitle, oynatıcı uygulaması istemci KIMLIĞI olmalıdır. Ayrıca, veren URL 'sinin sonuna "/" ekleyin.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    [JWT kod çözücüde](http://jwt.calebb.net/), JWT içinde gösterildiği gibi **AUD** ve **ISS**' yi görürsünüz:

    ![JWT](./media/architecture-design-multi-drm-system/media-services-1st-gotcha.png)

* Uygulamanın **Yapılandır** SEKMESINDE Azure AD 'de uygulamaya izinler ekleyin. Her uygulama için hem yerel hem de dağıtılan sürümler için izinler gereklidir.

    ![İzinler](./media/architecture-design-multi-drm-system/media-services-perms-to-other-apps.png)

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

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    SWT (ACS) ' ye ek olarak JWT (Azure AD) desteği eklediğiniz için varsayılan TokenType, TokenType. JWT ' dir. SWT/ACS kullanırsanız, belirteci TokenType. SWT olarak ayarlamanız gerekir.

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

![Özel Azure AD kiracı etki alanı hesabı tek](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain1.png)

**Akıllı kart Ile Microsoft etki alanı hesabı**: MICROSOFT kurumsal BT tarafından iki öğeli kimlik doğrulama ile özelleştirilmiş oturum açma sayfası.

![Özel Azure AD kiracı etki alanı hesabı iki](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain2.png)

**Microsoft hesabı**: tüketiciler için Microsoft hesabı oturum açma sayfası.

![Özel Azure AD kiracı etki alanı hesabı üç](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>PlayReady için şifreli medya uzantıları kullanma

Windows 8.1 veya üzeri sürümlerde Internet Explorer 11 ve Windows 10 ' da Microsoft Edge tarayıcısı gibi PlayReady desteği için şifreli Medya Uzantıları (EME) ile modern bir tarayıcıda, PlayReady, EME için temel alınan DRM 'dir.

![PlayReady için EME kullanma](./media/architecture-design-multi-drm-system/media-services-eme-for-playready1.png)

Koyu oynatıcı alanı, PlayReady korumasının, korunan videonun ekran yakalanmasını istemelerini engeller.

Aşağıdaki ekran görüntüsünde, oynatıcı eklentileri ve Microsoft Security Essentials (MSE)/EME desteği gösterilmektedir:

![PlayReady için oynatıcı eklentileri](./media/architecture-design-multi-drm-system/media-services-eme-for-playready2.png)

Windows 10 ' da Microsoft Edge ve Internet Explorer 11 ' deki EME, [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) 'in onu destekleyen Windows 10 cihazlarında çağrılmasını sağlar. PlayReady SL3000, gelişmiş Premium içeriğin (4K, HDR) ve yeni içerik teslim modellerinin (Gelişmiş içerik için) akışını kaldırır.

Windows cihazlarına odaklanmak için PlayReady, Windows cihazlarında (PlayReady SL3000) kullanılabilen tek DRM 'dir. Bir akış hizmeti, PlayReady aracılığıyla veya Evrensel Windows Platformu bir uygulamayla PlayReady kullanabilir ve başka bir DRM 'den PlayReady SL3000 kullanarak daha yüksek bir video kalitesi sunabilir. Genellikle, Microsoft Edge/Internet Explorer 11 veya aynı cihazdaki Evrensel Windows Platformu bir uygulama aracılığıyla en çok 10 ' a kadar akış akışı sağlar. Miktar, hizmet ayarlarına ve uygulamaya göre değişir.

#### <a name="use-eme-for-widevine"></a>Widevine için EME kullanma

Android 4.4.4 üzerinde Windows 10, Windows 8.1, Mac OSX Yosemite ve Chrome 'daki Chrome 41 + gibi EME/Widevine desteğiyle modern bir tarayıcıda, Google Widevine, EME 'nin arkasındaki DRM 'dir.

![Widevine için EME kullanma](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine1.png)

Widevine, korunan videonun ekran yakalanmasını engellemenizi engellemez.

![Widevine için oynatıcı eklentileri](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>FairPlay için EME kullanma

Benzer şekilde, FairPlay Protected içeriğini macOS veya iOS 11,2 ve üzeri sürümlerde Safari 'de bu test yürütücüsünde test edebilirsiniz.

"FairPlay" öğesini Protectionınfo olarak yerleştirdiğinizden emin olun. (FairPlay akış uygulaması sertifika yolu), uygulama sertifikanız için doğru URL 'YI girin.

### <a name="unentitled-users"></a>Kullanıcıları hak edilmemiş

Kullanıcı "kullanıcılar" grubunun üyesi değilse, Kullanıcı Yetkilendirme denetimini geçirmez. Çoklu DRM lisans hizmeti ardından istenen lisansı gösterildiği gibi verilmesinin reddeder. Ayrıntılı açıklama, tasarlanan "lisans alma başarısız oldu" dır.

![Kullanıcıları hak edilmemiş](./media/architecture-design-multi-drm-system/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Özel bir güvenlik belirteci hizmeti çalıştırma

Özel bir STS çalıştırırsanız, JWT bir simetrik ya da asimetrik anahtar kullanılarak özel STS tarafından verilir.

Aşağıdaki ekran görüntüsünde simetrik anahtar (Chrome kullanarak) kullanan bir senaryo gösterilmektedir:

![Simetrik anahtar ile özel STS](./media/architecture-design-multi-drm-system/media-services-running-sts1.png)

Aşağıdaki ekran görüntüsünde, bir x509 sertifikası aracılığıyla asimetrik anahtar kullanan bir senaryo gösterilmektedir (Microsoft modern tarayıcı kullanılarak):

![Asimetrik anahtarla özel STS](./media/architecture-design-multi-drm-system/media-services-running-sts2.png)

Her iki durumda da, Kullanıcı kimlik doğrulaması aynı kalır. Azure AD aracılığıyla gerçekleşir. Tek fark, JWTs 'nin Azure AD yerine özel STS tarafından verilme sayısıdır. Dinamik CENC korumasını yapılandırdığınızda, lisans teslimi hizmeti kısıtlaması, bir simetrik veya asimetrik anahtar olan JWT türünü belirtir.
