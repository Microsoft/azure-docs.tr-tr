---
title: Öğretici-geliştirici portalını erişim ve özelleştirme-Azure API Management | Microsoft Docs
description: API 'nizin belgeleriyle birlikte otomatik olarak oluşturulan ve tamamen özelleştirilebilir bir Web sitesi olan API Management geliştirici portalını nasıl özelleştireceğinizi öğrenmek için bu öğreticiyi izleyin.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 7c341dee3106530715248355da4412b97ed30980
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739629"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Öğretici: Geliştirici portalına erişme ve bunları özelleştirme

*Geliştirici Portalı* , API 'lerinizin belgelerinde otomatik olarak oluşturulan ve tamamen özelleştirilebilir bir Web sitesidir. API tüketicilerinin API 'lerinizi keşfedebileceği, nasıl kullanılacağını ve nasıl erişim isteyeceğini burada bulabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Geliştirici portalının yönetilen sürümüne erişin
> * Yönetim arabiriminde gezin
> * İçeriği özelleştirme
> * Değişiklikleri Yayımla
> * Yayınlanan portalı görüntüleme

Geliştirici Portalında daha fazla ayrıntı için [Azure API Management geliştirici portalına genel bakış ' a](api-management-howto-developer-portal.md)ulaşabilirsiniz.

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management Geliştirici Portalı-yönetici modu" border="false":::

## <a name="prerequisites"></a>Önkoşullar

- Şu hızlı başlangıcı doldurun: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)
- Azure API Management örneğini içeri aktarın ve yayımlayın. Daha fazla bilgi için bkz. [Içeri aktarma ve yayımlama](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Portala yönetici olarak erişin

Portalın yönetilen sürümüne erişmek için aşağıdaki adımları izleyin.

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. Üst gezinti çubuğunda **Geliştirici Portalı** düğmesini seçin. Portalın yönetici sürümüne sahip yeni bir tarayıcı sekmesi açılır.


## <a name="developer-portal-architectural-concepts"></a>Geliştirici portalı mimari kavramları

Portal bileşenleri, mantıksal olarak iki kategoriye ayrılabilir: *kod* ve *içerik*.

### <a name="code"></a>Kod

Kod, API Management geliştirici portalı [GitHub deposunda](https://github.com/Azure/api-management-developer-portal) tutulur ve şunları içerir:

- **Pencere** öğeleri-görsel öğeleri temsil eder ve HTML, JavaScript, stil oluşturma özelliği, ayarlar ve içerik eşlemeyi birleştirir. Örnekler, bir resim, metin paragrafı, form, API 'lerin listesi vb.
- **Stil tanımları** -Pencere öğelerinin nasıl Stillenebilir olduğunu belirtin
- **Altyapı** -Portal içeriğinden statik Web sayfaları oluşturur ve JavaScript 'te yazılmıştır
- **Görsel düzenleyici** -tarayıcı içi özelleştirme ve yazma deneyimine izin verir

### <a name="content"></a>Content

İçerik iki alt kategorilere ayrılmıştır: *Portal içeriği* ve *API Management içeriği*.

*Portal içeriği* portala özeldir ve şunları içerir:

- **Sayfalar** -Örneğin, giriş sayfası, API öğreticileri, blog gönderileri
- **Medya** -görüntüler, animasyonlar ve diğer dosya tabanlı içerikler
- **Düzenler** -bir URL ile eşleşen ve sayfaların nasıl görüntülendiğini tanımlayan şablonlar
- **Stiller** -yazı tipleri, renkler, kenarlıklar gibi stil tanımlarının değerleri
- **Ayarlar** -ayrıcalıklı simge, Web sitesi meta verileri gibi yapılandırma

    Medya dışında Portal içeriği, JSON belgeleri olarak ifade edilir.

*API Management içerik* , API 'Ler, Işlemler, ürünler, abonelikler gibi varlıkları içerir.
## <a name="understand-the-portals-administrative-interface"></a>Portalın yönetim arabirimini anlayın

### <a name="default-content"></a>Varsayılan içerik 

Portala ilk kez erişiyorsanız, varsayılan içerik arka planda otomatik olarak sağlanır. Varsayılan içerik, portalın yeteneklerini göstermek ve portalınızı kişiselleştirmek için gereken özelleştirmeleri en aza indirmek için tasarlanmıştır. [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)bölümünde Portal içeriğine nelerin dahil olduğu hakkında daha fazla bilgi edinebilirsiniz.

### <a name="visual-editor"></a>Görsel düzenleyici

Portalın içeriğini görsel düzenleyiciyle özelleştirebilirsiniz. 
* Sol taraftaki menü bölümleri; sayfaları, medyayı, düzenleri, menüleri, stilleri veya Web sitesi ayarlarını oluşturmanıza veya değiştirmenize olanak sağlar. 
* Alt kısımdaki menü öğeleri, görünüm pencereleri (örneğin, mobil veya masaüstü) arasında geçiş yapmanızı, portalın öğelerini kimliği doğrulanmış veya anonim kullanıcılarla görüntülemeyi veya eylemleri kaydetmenizi veya geri almanızı sağlar.
* Bir sayfaya, artı işareti ile mavi simgesine tıklayarak satır ekleyin. 
* Pencere öğeleri (örneğin, metin, görüntüler veya API listesi), artı işaretiyle bir gri simgeye basılarak eklenebilir.
* Sürükle ve bırak etkileşimi ile sayfadaki öğeleri yeniden düzenleyin. 

### <a name="layouts-and-pages"></a>Düzenler ve sayfalar

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Sayfalar ve düzenler" border="false":::

Düzenler sayfaların nasıl görüntüleneceğini tanımlar. Örneğin, varsayılan içerikte iki düzen vardır: biri giriş sayfasına, diğeri ise kalan tüm sayfalara uygulanır.

Bir düzen, URL şablonuyla sayfanın URL 'siyle eşleştirerek bir sayfaya uygulanır. Örneğin, URL şablonuna sahip bir düzen, `/wiki/*` `/wiki/` URL 'deki segmentle birlikte her sayfaya uygulanır: `/wiki/getting-started` , `/wiki/styles` vb.

Önceki görüntüde, sayfa kırmızı olarak işaretlenirken düzene ait içerikler mavi olarak işaretlenir. Menü bölümleri sırasıyla işaretlenir.

### <a name="styling-guide"></a>Stil Kılavuzu

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Stil Kılavuzu" border="false":::

Stil Kılavuzu, tasarımcılara göz önünde bulundurularak oluşturulan bir paneldir. Portalınızdaki tüm görsel öğeleri fazla görmeye ve stillendirilmesini sağlar. Stil hiyerarşik bir öğelerdir; birçok öğe diğer öğelerden özellikleri miras alır. Örneğin düğme öğeleri metin ve arka plan renklerini kullanır. Bir düğmenin rengini değiştirmek için özgün renk türevini değiştirmeniz gerekir.

Bir varyantı düzenlemek için, onu seçin ve üzerine açılan kurşun kalem simgesini seçin. Açılır penceredeki değişiklikleri yaptıktan sonra kapatın.

### <a name="save-button"></a>Kaydet düğmesi

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Kaydet düğmesi" border="false":::

Portalda her değişiklik yaptığınızda, alt kısımdaki menüden **Kaydet** düğmesini seçerek veya [Ctrl] + [S] tuşlarına basarak el ile kaydetmeniz gerekir. Değişikliklerinizi kaydettiğinizde, değiştirilen içerik API Management hizmetinize otomatik olarak yüklenir.

## <a name="customize-the-portals-content"></a>Portalın içeriğini özelleştirme

Portalınızı ziyaretçi tarafından kullanılabilir yapmadan önce, otomatik olarak oluşturulan içeriği kişiselleştirmelisiniz. Önerilen değişiklikler, ana sayfanın düzenlerini, stillerini ve içeriğini içerir.

> [!NOTE]
> Tümleştirme konuları nedeniyle, aşağıdaki sayfalar farklı bir URL altında kaldırılamaz veya taşınamaz: `/404` , `/500` ,, `/captcha` `/change-password` , `/config.json` , `/confirm/invitation` , `/confirm-v2/identities/basic/signup` , `/confirm-v2/password` , `/internal-status-0123456789abcdef` , `/publish` , `/signin` `/signin-sso` `/signup` ,,,,,,,,,,,.

### <a name="home-page"></a>Giriş sayfası

Varsayılan **giriş** sayfası, yer tutucu içeriğiyle doldurulur. Bu içeriği içeren tüm bölümleri kaldırabilir ya da yapıyı koruyabilir ve öğeleri tek tek ayarlayabilirsiniz. Oluşturulan metin ve görüntüleri kendi kendinize değiştirin ve bağlantıların istenen konumlara işaret ettiğinizden emin olun.

### <a name="layouts"></a>Düzenler

Gezinti çubuğundaki otomatik olarak oluşturulan logoyu kendi görüntünüz ile değiştirin.

### <a name="styling"></a>Stil oluşturma

Herhangi bir stili ayarlamanız gerekmez, ancak belirli öğeleri ayarlamayı düşünebilirsiniz. Örneğin, birincil rengi markaınızın rengine uyacak şekilde değiştirin.

### <a name="customization-example"></a>Özelleştirme örneği

Aşağıdaki videoda portalın içeriğini düzenlemeyi, Web sitesinin görünümünü özelleştirmeyi ve değişiklikleri yayımlamayı inceleyeceğiz.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Portalı yayımlama

Portalınızın ve en son değişikliklerinin ziyaretçi tarafından kullanılabilmesini sağlamak için, onu *yayımlamanız* gerekir. Portalı Portal 'ın yönetim arabiriminde veya Azure portal yayımlayabilirsiniz.

### <a name="publish-from-the-administrative-interface"></a>Yönetim arabiriminden Yayımla

1. **Kaydet** simgesini seçerek değişikliklerinizi kaydettiğinizden emin olun.
1. Menünün **işlemler** bölümünde **Web sitesini Yayımla** ' yı seçin. Bu işlem birkaç dakika sürebilir.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Portalı Yayımla" border="false":::

### <a name="publish-from-the-azure-portal"></a>Azure portal yayımlama

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. Sol taraftaki menüde, **Geliştirici Portalı** altında **portala genel bakış**' ı seçin.
1. **Portala genel bakış** penceresinde **Yayımla**' yı seçin.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Azure portal portalı Yayımla":::

> [!NOTE]
> API Management hizmeti yapılandırması değiştirildikten sonra portalın yeniden yayımlanması gerekir. Örneğin, özel bir etki alanı atadıktan sonra, kimlik sağlayıcılarını güncelleştirerek, temsili ayarlayarak ya da oturum açma ve ürün koşullarını belirterek portalı yeniden yayımlayın.


## <a name="visit-the-published-portal"></a>Yayınlanan portalı ziyaret edin

Portalı yayımladıktan sonra, yönetim paneliyle aynı URL 'de buna erişebilirsiniz (örneğin,) `https://contoso-api.developer.azure-api.net` . Dış ziyaretçi olarak ayrı bir tarayıcı oturumunda (ınbilito veya Private gözatma modu kullanarak) görüntüleyin.

## <a name="apply-the-cors-policy-on-apis"></a>API 'lerde CORS ilkesini uygulama

Portalınızın ziyaretçilerinin API 'Leri yerleşik etkileşimli konsol yoluyla test etmek için, API 'lerinizin CORS 'yi (Cross-Origin kaynak paylaşımı) etkinleştirin. Ayrıntılar için bkz. [Azure API Management geliştirici PORTALı SSS](developer-portal-faq.md#cors).

## <a name="next-steps"></a>Sonraki adımlar

Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)
- Kullanımdan kaldırılan eski portaldan [Yeni geliştirici portalına geçiş yapın](developer-portal-deprecated-migration.md) .