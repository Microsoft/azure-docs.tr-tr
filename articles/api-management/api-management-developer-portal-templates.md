---
title: Şablonları kullanarak API Management Geliştirici Portalını Özelleştirme-Azure | Microsoft Docs
description: Şablonları kullanarak Azure API Management geliştirici portalını özelleştirmeyi öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 9657414c56296ee710f17663bf0bbdd3a4d67ac8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176804"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Şablonları kullanarak Azure API Management Geliştirici Portalını Özelleştirme

Azure API Management'ta geliştirici portalını özelleştirmek için kullanılabilecek üç temel yöntem vardır:

* [Statik sayfaların ve sayfa düzeni öğelerinin içeriğini düzenleme][modify-content-layout]
* [Geliştirici portalı genelinde sayfa öğeleri için kullanılan stilleri güncelleştirme][customize-styles]
* [Portal tarafından oluşturulan sayfalar için kullanılan şablonları değiştirme][portal-templates] (Bu kılavuzda açıklanmıştır)

Şablonlar, sistem tarafından oluşturulan geliştirici portalı sayfalarının (örneğin, API belgeleri, ürünler, Kullanıcı kimlik doğrulaması vb.) içeriğini özelleştirmek için kullanılır. [Dotlikit](http://dotliquidmarkup.org/) sözdizimi ve sağlanan yerelleştirilmiş dize kaynakları, simgeler ve sayfa denetimleri kullanılarak sayfaların içeriğini uygun gördüğünüz şekilde yapılandırmak için harika bir esneklik vardır.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Geliştirici portalı şablonlarına genel bakış

Şablonları düzenleyici, yönetici olarak oturum açarken **Geliştirici portalından** yapılır. Önce Azure portal açın ve API Management örneğinizin hizmet araç çubuğundan **Geliştirici Portalı** ' na tıklayın.

Geliştirici portalı şablonlarına erişmek için sol taraftaki Özelleştir simgesine tıklayarak özelleştirme menüsünü görüntüleyin ve **Şablonlar**' a tıklayın.

![Geliştirici portalı şablonları][api-management-customize-menu]

Şablonlar listesi, geliştirici portalındaki farklı sayfaları kapsayan birkaç şablon kategorisini görüntüler. Her şablon farklıdır, ancak bunları düzenleme ve değişiklikleri yayımlama adımları aynıdır. Bir şablonu düzenlemek için şablonun adına tıklayın.

![Geliştirici portalı şablonları][api-management-templates-menu]

Bir şablona tıkladığınızda bu şablon tarafından özelleştirilebilen geliştirici portalı sayfasına gidersiniz. Bu örnekte, **ürün listesi** şablonu görüntülenir. **Ürün listesi** şablonu, ekranın kırmızı dikdörtgenle belirtilen alanını denetler.

![Ürün listesi şablonu][api-management-developer-portal-templates-overview]

**Kullanıcı profili** şablonları gibi bazı şablonlar, aynı sayfanın farklı parçalarını özelleştirir.

![Kullanıcı profili şablonları][api-management-user-profile-templates]

Her geliştirici portalı şablonunun Düzenleyicisi, sayfanın alt kısmında iki bölümden oluşur. Sol tarafta, şablonun düzenlenme bölmesi görüntülenir ve sağ taraftaki şablon için veri modeli görüntülenir.

Şablon düzenlemesi bölmesi, geliştirici portalındaki karşılık gelen sayfanın görünümünü ve davranışını denetleyen biçimlendirmeyi içerir. Şablondaki biçimlendirme, [Dotlikit](http://dotliquidmarkup.org/) sözdizimini kullanır. Dotlikit için popüler bir düzenleyici, [tasarımcılar Için Dotlikit](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)' dir. Düzen sırasında şablonda yapılan tüm değişiklikler tarayıcıda gerçek zamanlı olarak görüntülenir, ancak şablonu [kaydedip](#to-save-a-template) [yayımlaana](#to-publish-a-template) kadar müşterilerinize görülemez.

![Şablon biçimlendirmesi][api-management-template]

**Şablon verileri** bölmesi, belirli bir şablonda kullanıma sunulan varlıkların veri modeline kılavuzluk sağlar. Geliştirici Portalında Şu anda görüntülenen canlı verileri görüntüleyerek bu kılavuzu sağlar. Şablon bölmeleri, **şablon verileri** bölmesinin sağ üst köşesindeki dikdörtgene tıklayarak genişletebilirsiniz.

![Şablon veri modeli][api-management-template-data]

Önceki örnekte, aşağıdaki örnekte gösterildiği gibi, **şablon verileri** bölmesinde görüntülenen verilerden alınan Geliştirici Portalında iki ürün görüntülenir:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

**Ürün listesi** şablonundaki biçimlendirme, bilgileri ve her bir ürüne yönelik bir bağlantıyı göstermek üzere ürünlerin toplanması yoluyla istenen çıktıyı sağlamak üzere verileri işler. İşaretlemede `<search-control>` ve `<page-control>` öğelerine göz önünde edin. Bu denetim sayfada arama ve sayfalama denetimleri görüntülenir. `ProductsStrings|PageTitleProducts`, sayfanın `h2` üst bilgi metnini içeren yerelleştirilmiş bir dize başvurusudur. Geliştirici portalı şablonlarında kullanılabilecek dize kaynakları, sayfa denetimleri ve simgelerin bir listesi için, bkz. [API Management geliştirici portalı şablonları başvurusu](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Bir şablonu kaydetmek için
Bir şablonu kaydetmek için şablon düzenleyicisinde Kaydet ' e tıklayın.

![Şablonu kaydet][api-management-save-template]

Kayıtlı değişiklikler yayımlanana kadar Geliştirici Portalında canlı değildir.

## <a name="to-publish-a-template"></a>Şablon yayımlamak için
Kaydedilmiş şablonlar tek tek veya hepsi birlikte yayımlanabilir. Tek bir şablonu yayımlamak için şablon düzenleyicisinde Yayımla ' ya tıklayın.

![Şablonu yayımlama][api-management-publish-template]

Onaylamak için **Evet** ' e tıklayın ve şablonu Geliştirici Portalında canlı hale getirin.

![Yayımlamayı Onayla][api-management-publish-template-confirm]

Kaldırılmış olan tüm şablon sürümlerini yayımlamak için şablonlar listesinde **Yayımla** ' ya tıklayın. Yayımdan kaldırılan şablonlar, şablon adından sonra bir yıldız işaretiyle atanır. Bu örnekte, **ürün listesi** ve **ürün** şablonları yayımlanıyor.

![Şablonları Yayımlama][api-management-publish-templates]

Onaylamak için **Özelleştirmeleri Yayımla** ' ya tıklayın.

![Yayımlamayı Onayla][api-management-publish-customizations]

Yeni yayınlanan şablonlar, Geliştirici Portalında hemen etkili olur.

## <a name="to-revert-a-template-to-the-previous-version"></a>Bir şablonu önceki sürüme geri dönüştürmek için
Bir şablonu önceki yayımlanmış sürüme geri dönüştürmek için şablon düzenleyicisinde geri çevir ' e tıklayın.

![Şablonu tekrar çevir][api-management-revert-template]

Onaylamak için **Evet**’e tıklayın.

![Onayla][api-management-revert-template-confirm]

Bir şablonun daha önce yayımlanmış olan sürümü, bir önceki olarak, iade işlemi tamamlandıktan sonra Geliştirici Portalında canlı olur.

## <a name="to-restore-a-template-to-the-default-version"></a>Bir şablonu varsayılan sürüme geri yüklemek için
Şablonları varsayılan sürümüne geri yüklemek iki adımlı bir işlemdir. Önce şablonların geri yüklenmesi gerekir ve ardından geri yüklenen sürümlerin yayımlanması gerekir.

Tek bir şablonu varsayılan sürüme geri yüklemek için şablon düzenleyicisinde geri yükle ' ye tıklayın.

![Şablonu tekrar çevir][api-management-reset-template]

Onaylamak için **Evet**’e tıklayın.

![Onayla][api-management-reset-template-confirm]

Tüm şablonları varsayılan sürümlerine geri yüklemek için şablon listesinde **varsayılan şablonları geri yükle** ' ye tıklayın.

![Şablonları geri yükleme][api-management-restore-templates]

Geri yüklenen şablonlar, [bir şablon yayımlamak için](#to-publish-a-template)içindeki adımları izleyerek tek seferde veya her bir kez yayımlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar
Geliştirici portalı şablonları, dize kaynakları, simgeler ve sayfa denetimleri için başvuru bilgileri için bkz. [API Management geliştirici portalı şablonları başvurusu](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
