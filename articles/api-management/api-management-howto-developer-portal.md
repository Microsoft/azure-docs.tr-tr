---
title: Azure API Management geliştirici portalına genel bakış-Azure API Management | Microsoft Docs
description: API Management 'de geliştirici portalı hakkında bilgi edinin.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 6bf8c8690977ef1036c853d8c1c01a3a366b50df
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011490"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API Management geliştirici portalına genel bakış

Geliştirici portalı, API 'lerinizin belgelerinde otomatik olarak oluşturulan ve tamamen özelleştirilebilir bir Web sitesidir. API tüketicilerinin API 'lerinizi bulabileceği, bunları nasıl kullanacağınızı, erişim isteyeceğini ve bunları nasıl deneyebileceği de vardır.

Bu makalede, API Management 'de geliştirici portalının şirket içinde barındırılan ve yönetilen sürümleri arasındaki farklar açıklanmaktadır. Ayrıca, mimarisini açıklar ve sık sorulan soruların yanıtlarını sağlar.

> [!WARNING]
> Yeni geliştirici portalı şu anda API Management Hizmetleri 'ne alındı.
> Hizmetiniz yeni oluşturulmuşsa veya bir geliştirici katmanı hizmeti ise, en son sürüme sahip olmanız gerekir. Aksi takdirde sorunlarla karşılaşabilirsiniz (örneğin, yayımlama işlevselliğiyle). Özellik dağıtımı, 22nd, 2019 Cuma günü tamamlanmalıdır.
>
> [Önizleme sürümünden geliştirici portalının genel kullanıma sunulan sürümüne geçiş yapmayı öğrenin](#preview-to-ga) .

![API Management geliştirici portalı](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Yönetilen ve şirket içinde barındırılan sürümler

Geliştirici portalınızı iki şekilde oluşturabilirsiniz:

- **Yönetilen sürüm** -API Management örneğiniz içinde yerleşik olan ve URL `<your-api-management-instance-name>.developer.azure-api.net`üzerinden erişilebilen portalı düzenleyerek ve özelleştirerek. Yönetilen portala erişme ve bunları özelleştirme hakkında bilgi edinmek için [Bu belge makalesine](api-management-howto-developer-portal-customize.md) başvurun.
- **Şirket içinde barındırılan sürüm** -portalınızın bir API Management örneği dışında dağıtıp barındırılmasına göre. Bu yaklaşım, portalın kod temelini düzenlemenizi ve sunulan çekirdek işlevselliği genişletmenizi sağlar. Ayrıca, portalı en son sürüme yükseltmeniz gerekir. Ayrıntılar ve yönergeler için, [portalın kaynak kodu Ile GitHub deposuna][1]bakın. [Yönetilen sürüm için öğretici](api-management-howto-developer-portal-customize.md) , portalın Yönetim panelinden, ayrıca şirket içinde barındırılan sürümde de tanıtılan bir adım adım yol gösterir.

## <a name="portal-architectural-concepts"></a>Portal mimari kavramları

Portal bileşenleri, mantıksal olarak iki kategoriye ayrılabilir: *kod* ve *içerik*.

*Kod* [GitHub deposunda][1] tutulur ve şunları içerir:

- Pencere öğeleri-görsel öğeleri temsil eder ve HTML, JavaScript, stil oluşturma özelliği, ayarlar ve içerik eşlemeyi birleştirir. Örnekler, bir resim, metin paragrafı, form, API 'lerin listesi vb.
- Pencere öğelerinin nasıl Stillenebilir olduğunu belirten stil tanımları
- Altyapı-Portal içeriğinden statik Web sayfaları oluşturur ve JavaScript 'te yazılmıştır
- Tarayıcı içi özelleştirme ve yazma deneyimine izin veren görsel düzenleyici

*İçerik* iki alt kategorilere ayrılmıştır: *portal içeriği* ve *API Management içeriği*.

*Portal içeriği* portala özeldir ve şunları içerir:

- Sayfalar-örneğin, giriş sayfası, API öğreticileri, blog gönderileri
- Medya-görüntüler, animasyonlar ve diğer dosya tabanlı içerikler
- Düzenler-bir URL ile eşleşen ve sayfaların nasıl görüntülendiğini tanımlayan şablonlar
- Stiller-stil tanımlarının değerleri, örn. yazı tipleri, renkler, kenarlıklar
- Ayarlar-yapılandırma, örneğin, ayrıcalıklı simge, Web sitesi meta verileri

Medya dışında *Portal içeriği*, JSON belgeleri olarak ifade edilir.

*API Management içerik* , API 'Ler, Işlemler, ürünler, abonelikler gibi varlıkları içerir.

Portal, [Paperbits çerçevesinin](https://paperbits.io/)uyarbir çatalını temel alır. Özgün Paperbits işlevselliği, API Management özgü pencere öğeleri (örneğin, API 'lerin listesi, ürünlerin listesi) ve içeriği kaydetmek ve almak için bir bağlayıcıya API Management bir bağlayıcı sağlamak üzere genişletilmiştir.

## <a name="faq"></a>Sık sorulan sorular

Bu bölümde, genel doğası olan yeni geliştirici portalı hakkında sık sorulan soruları yanıtlarız. Şirket içinde barındırılan sürüme özgü sorular için [GitHub deposunun wiki bölümüne](https://github.com/Azure/api-management-developer-portal/wiki)bakın.

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a>Portalın önizleme sürümünden nasıl geçiş yapabilirim <a id="preview-to-ga"/>?

Geliştirici portalının önizleme sürümünü kullanarak, API Management hizmetinizde önizleme içeriğini sağlamış olursunuz. Varsayılan içerik, daha iyi kullanıcı deneyimi için genel kullanıma sunulan sürümde önemli ölçüde değiştirilmiştir. Ayrıca yeni pencere öğeleri içerir.

Yönetilen sürümü kullanıyorsanız, **işlemler** menüsü bölümünde **içeriği Sıfırla** ' ya tıklayarak portalın içeriğini sıfırlayın. Bu işlemin onaylanmasının, portalın tüm içeriğini kaldıracaksa ve yeni varsayılan içeriği sağlaması istenir. Portalın altyapısı, API Management hizmetinizde otomatik olarak güncelleştirilmiştir.

![Portal içeriğini sıfırlama](media/api-management-howto-developer-portal/reset-content.png)

Şirket içinde barındırılan sürümü kullanıyorsanız, mevcut içeriği kaldırmak ve yeni içerik sağlamak için GitHub deposundan `scripts/cleanup.bat` ve `scripts/generate.bat` kullanın. Portalınızın kodunu önceden GitHub deposundan en son sürüme yükseltdiğinizden emin olun.

Portalın içeriğini sıfırlamak istemiyorsanız, sayfalarınızın tamamında yeni kullanılabilir pencere öğelerini kullanmayı düşünebilirsiniz. Mevcut pencere öğeleri, en son sürümlere otomatik olarak güncelleştirildi.

Portalınız genel kullanılabilirlik duyurusu sonrasında sağlandıysa, yeni varsayılan içeriği zaten kullanıma hazır olmalıdır. Sizin tarafınızdan bir işlem yapılması gerekmez.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Eski geliştirici portalından yeni geliştirici portalına nasıl geçiş yapabilirim?

Portallar uyumsuzdur ve içeriği el ile geçirmeniz gerekir.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Yeni Portal eski portalın tüm özelliklerine sahip mi?

Yeni geliştirici portalı *uygulamaları* ve *sorunları*desteklemez. Eski portalda *sorunlar* kullandıysanız ve bunların yeni bir tane olması gerekiyorsa, [özel bir GitHub sorununa](https://github.com/Azure/api-management-developer-portal/issues/122)yorum gönderin.

### <a name="has-the-old-portal-been-deprecated"></a>Eski Portal kullanımdan kaldırılmıştır mı?

Eski geliştirici ve yayımcı portalları artık *eski* özelliklerdir; yalnızca güvenlik güncelleştirmelerini alacak. Yeni özellikler yalnızca yeni geliştirici portalında uygulanır.

Eski portalların kullanımdan kaldırılması, ayrı olarak duyurulacak. Sorularınız, endişeleriniz veya açıklamalarınız varsa, bunları [özel bir GitHub sorunuyla](https://github.com/Azure/api-management-developer-portal/issues/121)yükseltin.

### <a name="how-can-i-automate-portal-deployments"></a>Portal dağıtımlarını nasıl otomatikleştirebilirim?

Yönetilen veya şirket içinde barındırılan bir sürümü kullanıyor olmanız fark etmeksizin, geliştirici portalının içeriğine REST API aracılığıyla programlı bir şekilde erişebilir ve yönetebilirsiniz.

API, [GitHub deposunun wiki bölümünde][2]belgelenmiştir. Ayrıca, ortamlar arasında portal içeriğinin geçişini otomatik hale getirmek için kullanılabilir (örneğin, bir test ortamından üretim ortamına). Bu işlem hakkında daha fazla bilgi için GitHub 'daki [Bu belge makalesine](https://aka.ms/apimdocs/migrateportal) daha fazla bilgi edinebilirsiniz.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Portal Azure Resource Manager şablonları destekliyor mu ve/veya API Management DevOps kaynak seti ile uyumlu mı?

Hayır.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Yönetilen Portal bağımlılıkları için ek VNET bağlantısını etkinleştirmem gerekir mi?

Hayır.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>Etkileşimli konsolu kullanırken CORS hatası alıyorum. Ne yapmalıyım?

Etkileşimli konsol tarayıcıdan istemci tarafı API isteği yapar. API 'lerinize [BIR CORS ilkesi](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) ekleyerek CORS sorununu çözebilirsiniz. Tüm parametreleri el ile belirtebilir veya joker karakter `*` değerlerini kullanabilirsiniz. Örneğin:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

## <a name="next-steps"></a>Sonraki adımlar

Yeni geliştirici portalı hakkında daha fazla bilgi edinin:

- [Yönetilen geliştirici portalına erişme ve bunları özelleştirme](api-management-howto-developer-portal-customize.md)
- [Portalın şirket içinde barındırılan sürümünü ayarlama][2]

Diğer kaynaklara gözatamazsınız:

- [Kaynak kodlu GitHub deposu][1]
- [Projenin ortak yol haritası][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects