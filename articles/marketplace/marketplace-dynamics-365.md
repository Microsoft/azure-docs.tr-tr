---
title: Microsoft AppSource için Dynamics 365 tekliflerini planlayın
description: Microsoft AppSource için Dynamics 365 tekliflerini planlayın
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/16/2021
ms.openlocfilehash: c5746b70f84ff68ea578ba141cc5251cc5ce07ea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819459"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Microsoft Dynamics 365 teklifini planlayın

Bu makalede, ticari Market 'teki Microsoft AppSource Dynamics 365 teklifinin farklı seçenekleri ve özellikleri açıklanmaktadır. AppSource; Microsoft 365, Dynamics 365, PowerApps ve Power BI derleme veya genişletme tekliflerini içerir. AppSource, ödemeli (*Şimdi al*), Listele (*benimle iletişime geçin*) ve deneme (*Şimdi deneyin*) tekliflerini sağlar.

Başlamadan önce, [Iş Ortağı Merkezi](./partner-center-portal/create-account.md) 'nde ticari bir market hesabı oluşturun ve ticari Market programına kaydedildiğinden emin olun. Ayrıca, [yayımlama işlemini ve yönergeleri](/office/dev/store/submit-to-appsource-via-partner-center)gözden geçirin.

## <a name="licensing-options"></a>Lisanslama seçenekleri

Yeni bir teklif yayımlamaya hazırlanırken, hangi lisans seçeneğinin seçileceğine karar vermeniz gerekir. Bu, daha sonra teklifi Iş Ortağı Merkezi 'nde oluştururken sağlamanız gereken ek bilgileri saptayacaktır.

Dynamics 365 teklifleri için kullanılabilir lisanslama seçenekleri şunlardır:

| Lisans seçeneği | İşlem işlemi |
| --- | --- |
| Şimdi alın (ücretsiz) | Müşterilerinizin teklifinizi ücretsiz olarak listeleyin. |
| Ücretsiz deneme (listeleme) | Müşterilerinize bir adet, üç veya altı aylık ücretsiz deneme sürümü sunun. Ücretsiz denemelerdeki teklif listesi, hizmetiniz tarafından oluşturulur, yönetilir ve yapılandırılır ve Microsoft tarafından yönetilen abonelikler içermez. |
| Benimle iletişim kurun | Müşteri Ilişkileri yönetimi (CRM) sisteminizi bağlayarak müşteri iletişim bilgilerini toplayın. Müşterinin, bilgilerini paylaşması için izin istenir. Bu müşteri ayrıntıları, teklifinizin bulunduğu teklif adı, KIMLIĞI ve Market kaynağıyla birlikte, yapılandırdığınız CRM sistemine gönderilir. CRM 'nizi yapılandırma hakkında daha fazla bilgi için, teklif türünün **teklif kurulum** sayfasının **müşteri adayları** bölümüne bakın. |
|

## <a name="test-drive"></a>Sınama sürücüsü

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Müşteri liderleri

Iş Ortağı Merkezi ile ticari Market 'e bir teklif yayımladığınızda, bunu Müşteri Ilişkileri yönetimi (CRM) sisteminize bağlamak isteyeceksiniz. Bu, bir kişi, ürününüzü ilgilendiren veya ürünü kullandığı anda müşteri iletişim bilgilerini almanızı sağlar. Bir test sürücüsünü etkinleştirmek istiyorsanız, bir CRM 'ye bağlanmak gerekir; Aksi takdirde, bir CRM 'ye bağlanmak isteğe bağlıdır. İş Ortağı Merkezi, Azure tablosu, Dynamics 365 müşteri katılımı, HTTPS uç noktası, Marketo ve Salesforce 'ı destekler.

## <a name="legal"></a>Yasal Bildirim

**Hüküm ve koşullarınızı** oluşturun. Müşterilerinizin teklifinizi deneyebilmeleri için bunları kabul etmesi gerekir. Microsoft standart bir sözleşmeye sahiptir, ancak Dynamics 365 tekliflere uygulanmaz.

## <a name="offer-listing-details"></a>Teklif listeleme ayrıntıları

Teklif bilgilerinin Microsoft AppSource nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir (listelenen tüm fiyatlar yalnızca örnek amaçlıdır ve gerçek maliyetlerin yansıtılmaz):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Bu teklifin Microsoft AppSource nasıl göründüğünü gösterir.":::

> [!NOTE]
> Teklif açıklamasının "Bu uygulama yalnızca [Ingilizce olmayan dil]" ifadesi ile başlıyorsa, teklif listesi içeriğinin Ingilizce olması gerekmez.

Teklifinizi daha kolay bir şekilde oluşturmaya yardımcı olmak için bu öğeleri önceden hazırlayın. Belirtilenler dışında tümü gereklidir.

- **Ad** : ad, ticari Market 'teki teklif listelerinizin başlığı olarak görünür. Ad trademarked olabilir. Bu, emojıs (ticari marka ve telif hakkı sembolleri olmadıkları müddetçe) içeremez ve 50 karakterle sınırlıdır.
- **Arama sonuçları Özeti** : teklifinizin amacı veya işlevi, 100 karakter veya daha az satır sonu olmayan tek bir cümle olarak sunulur. Bu, ticari Market listeleme arama sonuçlarında kullanılır.
- **Açıklama** – bu açıklama ticari Market dökümlerinin genel bakış bölümünde görüntülenir. Bir değer teklifi, önemli avantajlar, hedeflenen kullanıcı tabanı, herhangi bir kategori ya da sektör ilişkilendirmesi, uygulama içi satın alma fırsatları, gerekli ön kapanışlar ve daha fazla bilgi edinmek için bir bağlantı dahil etmeyi göz önünde bulundurun. Bu metin kutusunda, tanımlarınızın daha ilgi çekici olması için zengin metin düzenleyici denetimleri vardır. İsterseniz, biçimlendirme için HTML etiketlerini kullanın.
- **Arama anahtar sözcükleri** (isteğe bağlı) – müşterilerin teklifinizi bulmak Için kullanabileceği en fazla üç arama anahtar sözcüğü. Teklif **adı** ve **açıklamasını** eklemeyin; Bu metin, aramaya otomatik olarak eklenir.
- **Uygulamanızın birlikte çalışması** (isteğe bağlı): teklifinizin en fazla üç ürünün ile birlikte çalışması.
- **Yardım ve Gizlilik ilkesi bağlantıları** : teklifinizin yardım ve şirketin gizlilik Ilkesinin URL 'leri. Uygulamanızın gizlilik yasaları ve yönetmeliklerle uyumlu olmasını sağlamaktan siz sorumlusunuz.
- **İletişim bilgileri**
  - **Destek kişisi** : müşterileriniz açtıklarında Microsoft iş ortaklarının kullanacağı ad, telefon ve e-posta. Destek Web sitenizin URL 'sini ekleyin.
  - **Mühendislik ilgili kişisi** : Microsoft 'un Teklifinizle ilgili sorunlar olduğunda doğrudan kullanacağı ad, telefon ve e-posta. Bu iletişim bilgileri ticari Market 'te listelenmez.
- **Belgeler** , PDF biçimindeki teknik incelemeler, broşürler, Yapılacaklar listeleri veya PowerPoint sunuları gibi müşterilere yönelik üç belgeyi destekleme.
- **Medya**
    - **Amblemler** : **büyük** amblem için bir PNG dosyası. İş Ortağı Merkezi, diğer gerekli logo boyutlarını oluşturmak için bunu kullanacaktır. İsterseniz bunları daha sonra farklı görüntülerle değiştirebilirsiniz.
    - **Ekran görüntüleri** : teklifinizin nasıl çalıştığını gösteren en az bir ve en fazla beş ekran görüntüsü. Görüntüler, PNG biçiminde 1280 x 720 piksel olmalıdır ve bir resim yazısı içermelidir.
    - **Videolar** (isteğe bağlı) – teklifinizi gösteren dört adede kadar video. Bir ad, YouTube veya Vimeo 'un URL 'sini ve 1280 x 720 piksel PNG küçük resmini ekleyin.

> [!Note]
> Teklifinizin ticari Market 'te yayımlanacak genel [ticari Market sertifika ilkelerini](/legal/marketplace/certification-policies#100-general) karşılaması gerekir.

## <a name="additional-sales-opportunities"></a>Ek satış fırsatları

Microsoft tarafından desteklenen pazarlama ve satış kanallarını kabul edebilirsiniz. Iş Ortağı Merkezi 'nde teklifinizi oluştururken, **Microsoft Ile ortak satış** için işlemin sonuna doğru iki sekme görürsünüz. Bu seçenek, Microsoft satış ekiplerinin, müşterilerinin ihtiyaçlarını değerlendirirken, IP ortak satışı uygun çözümünüzü kabul etmenize olanak tanır. Teklifinizi değerlendirme için hazırlama hakkında ayrıntılı bilgi edinmek için [Iş Ortağı Merkezi 'Nde ortak satış seçeneğine](commercial-marketplace-co-sell.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Yukarıda açıklanan planlama öğelerini kabul ettikten sonra, teklifinizi oluşturmaya başlamak için aşağıdakilerden birini seçin (sol taraftaki içindekiler tablosunda da mevcuttur).

| Yayımlama Kılavuzu    | Notlar  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) | Enterprise Edition için derleme yaparken, önce bu ek [Yayımlama süreçlerini ve yönergeleri](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source)gözden geçirin. |
| [Dynamics 365 for Business Central](partner-center-portal/create-new-business-central-offer.md) |   |
| [Power Apps & müşteri katılımı için Dynamics 365](dynamics-365-customer-engage-offer-setup.md) | Önce bu ek [Yayımlama süreçlerini ve yönergeleri](/dynamics365/customer-engagement/developer/publish-app-appsource)gözden geçirin. |
| [Power BI](/partner-center-portal/create-power-bi-app-offer.md) | Önce bu ek [Yayımlama süreçlerini ve yönergeleri](/power-bi/developer/office-store)gözden geçirin. |
|
