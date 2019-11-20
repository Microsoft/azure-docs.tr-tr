---
title: Web Analytics
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d47a67be157f71a30a6ae155790ed78a78fd6743
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814652"
---
<a name="web-analytics"></a>Web Analytics
=============

Bu makalede, işletmenizin en iyi şekilde büyümesini sağlamak için Web analizinin nasıl öğreneceği ve kullanılacağı hakkında yönergeler sunulmaktadır. Şu anda bu Öngörüler sekmesi tüm AppSource teklifleri için kullanılabilir.

Teklifinizi oluşturup yayımladığınıza göre, yolculuğun bir sonraki bölümü,\' başarısını izleyip ölçmeye yönelik. **Web Analytics**ile, tekliflerinizin her birinin Market 'te ne kadar iyi bir şekilde çalıştığını görme imkanını ekledik. Yolculuğa başlamak için, yeni analiz sekmesini görmek üzere Bulut İş Ortağı Portalı sol tarafındaki Öngörüler sayfasına gidin.

![WebAnalytics sayfası](./media/si-getting-started/WebAnalytics1.png)

Microsoft Power BI ile oluşturulmuş yayımcı KIMLIĞINIZ için zengin bir pano görürsünüz ve her gün, her gün yenilenen\' verilerinizi görmenize olanak sağlar.

<a name="microsoft-campaigns"></a>**Microsoft kampanyaları**
-----------------------

Tekliflerinizi büyütmek ve tekliflerinizin büyümesini izlemek için Bulut İş Ortağı Portalı **Microsoft kampanyalarını** kullanma özelliğini etkinleştirdik. Kampanyalar, Market için, uygulama ayrıntıları sayfanıza müşteri gönderen farklı kanalları izlemenize imkan tanıyan yeni, desteklenen bir özelliktir.

### <a name="how-to-make-a-campaign"></a>**Kampanya oluşturma**

Kampanyaları tanımlamanın en kolay yolu, URL 'nize Market 'teki uygulama ayrıntısı sayfasında yer alan özel bir sözcük/terim ekliyoruz. Google, Bing, LinkedIn ve diğer birçok site, bir reklam oluşturmanızı, sitelerini istediğiniz siteye bağlamayı teşvik eder.

Genel olarak, bu çabalar yeni müşterileri ürününüzle kullanmanıza yardımcı olmak ve kanallarınızın her birinin nasıl yaptığı hakkında başarıyı ölçmek açısından önemlidir. Bu, kampanyaların geldiği yerdir.

Kendi kampanyanızı oluşturmak için iki yol vardır.

1. URL 'nize, kampanyanın ne olduğunu ve bu müşterilerin hangi sayfa/olay geldiğini açıklayan **mktcmpıd** sorgu parametresini ekleyin.

Örneğin, şunları kullanabilirsiniz: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Gelişmiş): URL 'de desteklenen, genel kampanya kimliklerimizden birini kullanın. Kullanmanız gereken ek başvuru etiketlerine göz duymasını istiyoruz, bu nedenle bu ek etiketleri otomatik olarak tanıma kuralını destekliyoruz:
    
    1. **UTM\_kampanyası**
    2. **Uta\_kaynağı**
    3. **ref**
    4. **YN**

Örneğin, şunları kullanabilirsiniz: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Kampanyanın, müşterinin geldiği konum (e-posta, blog, sosyal medya kaynağı vb.) gibi birden fazla kaynağı daha ayrıntılı olarak belirlemek için bu kampanya kimliklerinin birden çok birleşimini kullanabilirsiniz.

Örneğin:

1. Bülten başvuran: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn başvuran: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Kampanyaların tüm sayfalarınızda geçiş yapmalarını sağlama**

Kampanyalarınızın, trafiği yönlendiren bir ara sayfasına sahip olduğu bir senaryo olabilir ve bu sayede müşterileri Market 'e gönderebilirsiniz. İlk kampanya kimliklerinizi Market 'e göndereceğiniz son URL 'ye geçirmek önemlidir.

Örnek aşağıda verilmiştir:

1. Pazarlama çalışanı, Google 'dan şirket\'s giriş sayfasına <https://contoso.com>trafiği yönlendirmek için reklamları satın alarak. Bu giriş sayfasında, <https://appsource.com>'e giden ürün\" bağlantısını deneyin \".
2. Kullanıcı ad 'ye tıklar ve şirket\'s giriş sayfasında yer alır.
    1.  Başvuru URL 'SI = google.com
    2.  Giriş sayfası URL 'SI = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. Kullanıcı \"tıkladıktan sonra ürünümüzü\" bağlantısını deneyin ve AppSource 'a gider.
    1. Başvuru URL 'SI = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. Giriş sayfası URL 'SI (**Bu URL 'nin utm\_kampanyası içerdiğinden ve utm\_kaynağının bu URL 'ye eklendiğinden emin olun**) = [https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**UTM\_kampanya = MyCampaignAdName & UTM\_kaynak = mysourceadname** ](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Kampanyanın başarısını değerlendirme
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Kampanyaya göre sayfa ziyaretleri**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Bu, günlük sayfa ziyaretlerinizin her birinin, geldiği kampanya tarafından dökülübir dökümden oluşur.

### <a name="conversion-rate-by-campaign"></a>**Kampanyaya göre dönüştürme oranı**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Tüm teklifinizin dönüştürme oranını gösterdiğimiz gibi, bu grafikte farklı kampanyalarınızın nasıl yaptığına ilişkin dökümü görebilirsiniz. Bu grafik, daha yüksek dönüştürme oranı kampanyalarınızın nereden geldiğini belirlemenize yardımcı olmalıdır.

### <a name="distribution-by-campaign"></a>**Kampanyaya göre dağıtım**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Müşterilerinizin etki alanlarına baktığımız gibi, bu grafik, kullanıcıların kapsamındaki Market 'e geldiği kampanya başına verilerinizin dağılımını görmenizi sağlar. \_NoCampaign, müşterinin Market 'e gezindiklerinde URL 'de bir kampanya KIMLIĞI olmadığı anlamına gelir.

<a name="next-steps"></a>**Sonraki Adımlar**
--------------

Tekliflerinizi başarılı bir şekilde izleyebilmenizi sağladığınıza göre, size kendi kampanyalarınızı oluşturmanız önerilir.

Sorularınız/Özellik istekleriniz varsa, bunları sağ üst köşede bulunan geri bildirimde bulunarak paylaşabilirsiniz.

![Bulut İş Ortağı Portalı geri bildirim](./media/si-getting-started/WebAnalytics5.png)
