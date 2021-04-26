---
title: Pazarlama özellikleri
description: Portalda toplanan farklı pazarlama alanlarının açıklaması ve bunların Azure Sertifikalı cihaz kataloğunda görünme sıklığı
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 262616ca82e9c06baec0d7a1b81a0e3dff2ed8db
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304520"
---
# <a name="marketing-properties"></a>Pazarlama özellikleri

[Cihaz ayrıntılarınızı ekleme](tutorial-02-adding-device-details.md)sürecinde, [Azure Sertifikalı cihaz kataloğunda](https://devicecatalog.azure.com)görüntülenecek olan pazarlama bilgilerini sağlamanız gerekecektir. Bu bilgiler, sertifika gönderme işlemi sırasında Azure Sertifikalı cihaz portalı 'nda toplanır ve katalogda filtre parametresi olarak kullanılacaktır. Bu makalede, portalda toplanan alanlarla Katalog üzerinde görünme sıklığı arasında bir eşleme sunulmaktadır. Bu makaleyi okuduktan sonra iş ortakları, sertifika işlemi sırasında, ürün kataloğunda en iyi şekilde temsil edecek bilgileri daha iyi anlamalıdır.

![PDP genel bakış](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Azure Sertifikalı cihaz kataloğu ürün kutucuğu

Kataloğa yönelik ziyaretçiler, ilk olarak, arama sayfasında bir katalog ürün kutucuğu olarak cihazlarınızla etkileşime geçebilir. Bu, cihaza ve bu sertifikaya verilen sertifikalamaları için temel bir genel bakış sağlar.

![Ürün kutucuk şablonu](./media/concepts-marketing/product-tile.png)

| Alan | Açıklama                  | Portala eklenecek yer                |
|---------------|-------------------------|----------------------------------|
| Aygıt Adı | Sertifikalı cihazınızın ortak adı         | Cihaz ayrıntılarının temelleri sekmesi|
| Şirket adı| Şirketinizin ortak adı  | Portalda düzenlenemez. MPN hesap adından ayıklanır |
| Ürün fotoğrafı  | En düşük çözünürlükte cihazınızın görüntüsü 200p x 200p  | Pazarlama ayrıntıları |
| Sertifika sınıflandırması  | Zorunlu Azure Sertifikalı cihaz sertifika etiketi ve isteğe bağlı sertifika rozetleri  | Cihaz ayrıntılarının temelleri sekmesi. Connect & test bölümünde uygun test geçişi yapılmalıdır. |

## <a name="product-description-page-information"></a>Ürün açıklaması sayfası bilgileri

Bir müşteri, katalog arama sayfasından cihaz kutucuğuna tıkladıktan sonra, cihazınızın ürün açıklaması sayfasına gidilir. Bu, sertifika işlemi sırasında belirtilen bilgilerin toplu olarak bulunacağı yerdir.

Ürün açıklaması sayfasının en üst kısmında, bazıları ürün kutucuğunda zaten kullanılmış olan önemli özellikler vurgulanmıştır.

![PDP üst çubuk](./media/concepts-marketing/pdp-top.png)

| Alan | Açıklama                  | Portala eklenecek yer                |
|---------------|-------------------------|----------------------------------|
| Cihaz sınıfı | Cihazınızın form faktörü ve birincil amacı sınıflandırması ([daha fazla bilgi](./resources-glossary.md))       | Cihaz ayrıntılarının temelleri sekmesi|
| Cihaz Türü | Uygulama hazırlığı temelli cihaz sınıflandırması ([daha fazla bilgi](./resources-glossary.md)) | Cihaz ayrıntılarının temelleri sekmesi |
| Coğrafi kullanılabilirlik | Cihazınızın satın alma için kullanılabilir olduğu bölgeler  | Pazarlama ayrıntıları |
| İşletim sistemleri  | Cihazınızın desteklediği işletim sistemleri  | Cihaz ayrıntılarının Ürün Ayrıntıları sekmesi |
| Hedef endüstriler  | Cihazınızın en iyi duruma getirildiği ilk 3 sektör  | Pazarlama ayrıntıları |
| Ürün açıklaması  | Ürününüzün pazarlama tanımlarınızı yazmanız için ücretsiz metin alanı. Bu, portalda listelenmeyen ayrıntıları yakalayabilir veya cihazınızı kullanmanın avantajları için ek bağlam ekleyebilir. | Pazarlama ayrıntıları|

Sayfanın geri kalanı, cihazınızın teknik özelliklerinin, ürününüzü daha iyi anlamanıza yardımcı olacak şekilde tablo biçiminde görüntülenmesine odaklanılmıştır. Kolaylık sağlaması için sayfanın en üstünde görünen bilgiler de burada listelenir. Tablonun geri kalanı portalda belirtilen bileşenlerle bölümlenebilir.

![PDP alt sayfa](./media/concepts-marketing/pdp-bottom.png)

| Alan | Açıklama                  | Portala eklenecek yer                |
|---------------|-------------------------|----------------------------------|
| Bileşen türü | Cihazınızın form faktörü ve birincil amacı sınıflandırması ([daha fazla bilgi](./resources-glossary.md))       | Cihaz ayrıntılarının ürün ayrıntıları|
| Bileşen adı| Açıkladığınız bileşenin adı | Cihaz ayrıntılarının ürün ayrıntıları |
| Ek bileşen bilgileri | Dahil edilen sensörler, bağlantı, Hızlandırıcılar vb. gibi ek donanım belirtimleri.  | Cihaz ayrıntılarının ek bileşen bilgileri ([daha fazla bilgi](./how-to-using-the-components-feature.md))  |
| Cihaz bağımlılığı metni | İş ortağı tarafından sunulan ve ürünün Azure 'a bağlanması için gereken farklı bağımlılıkları açıklayan metin ([daha fazla bilgi edinin](./how-to-indirectly-connected-devices.md))   | Cihaz ayrıntılarının bağımlılıklar sekmesinin müşterilere yönelik açıklamalar bölümü |
| Cihaz bağımlılığı bağlantısı  | Geçerli ürününüzün gerektirdiği sertifikalı bir cihaza bağlantı  | Cihaz ayrıntılarının bağımlılıklar sekmesi |

## <a name="shop-links"></a>Mağaza bağlantıları
Hem ürün kutucuğunda hem de ürün açıklaması sayfasında bulunan bir atölye düğmesidir. Müşteri tarafından tıklandığında, bir dağıtıcı seçmesini sağlayan bir pencere açılır (5 dağıtıcı listeleyerek izin veriliyor). Seçildiğinde, müşteri iş ortağı tarafından belirtilen URL 'ye yeniden yönlendirilir.

![Atölye açılır deneyimi görüntüsü](./media/concepts-marketing/shop.png)

| Alan | Açıklama                  | Portala eklenecek yer                |
|---------------|-------------------------|----------------------------------|
| Dağıtıcı adı | Ürününüzü satan dağıtıcısının adı | Pazarlama ayrıntıları|
| Cihazı al| Cihazı satın almak (veya dağıtıcıdan bir teklif istemek) için müşterinin dış Web sitesine bağlanın. Bu, dağıtıcı cihaz üreticisi ile aynı ise üreticinin sayfasıyla aynı olabilir. Bir satın alma sayfası kullanılamıyorsa, bu, müşterinin doğrudan iletişim kurabilmesi için dağıtıcının sayfasına yönlendirecektir.  | Pazarlama ayrıntılarında dağıtımcı ürün sayfası URL 'SI. Kullanılabilir bir satın alma sayfası yoksa, bağlantı, pazarlama ayrıntısında varsayılan olarak dağıtıcı URL 'sine ayarlanır. |

## <a name="external-links"></a>Dış bağlantılar
Ürün açıklaması sayfasına da dahil edilen bağlantılar, müşterinin ürünü daha iyi anlamasına yardımcı olan iş ortakları tarafından sağlanan sitelere veya dosyalara gider. Bunlar sayfanın üst kısmında, ürün açıklaması metninin altına doğru görünürler. Farklı cihaz türleri ve sertifika programları için, görüntülenecek bağlantılar farklılık gösterir.

| Bağlantı | Açıklama                  | Portala eklenecek yer                |
|---------------|-------------------------|----------------------------------|
| Kullanmaya başlama kılavuzu * | Azure hizmetleriyle cihazınızı bağlamaya ve kullanmaya yönelik Kullanıcı yönergeleriyle birlikte PDF dosyası | Portalın ' Başlarken ' Kılavuzu bölümüne ekleme|
| Üretici sayfası *|Üretici sayfasına bağlantı. Bu sayfa, cihazınız için belirli bir ürün sayfası veya bir pazarlama sayfası yoksa Şirket giriş sayfası olabilir. | Pazarlama ayrıntılarında üreticinin pazarlama sayfası |
| Cihaz modeli | IoT Tak ve Kullan çözümleri için genel DTDL modelleri  | Portalda düzenlenemez. Cihaz modeli ([ortak model deposuna](https://aka.ms/modelrepo) ) yüklenmelidir  |
| Cihaz kaynak kodu | Geliştirme Seti cihaz türleri için cihaz kaynak kodu URL 'SI| Cihaz ayrıntılarının temelleri sekmesi  |

 **Yayımlanan tüm cihazlar için gereklidir*

## <a name="next-steps"></a>Sonraki adımlar
Sertifika sırasında sağladığınız bilgileri nasıl kullandığımızdan emin olduğunuza göre artık cihazınızı onaylamak için hazırsınız! Kendi pazarlama bilgilerinizi eklemek için, sertifika projenizi başlatın veya cihaz ayrıntıları aşamasına geri gidin.

- [Sertifika yolculuğunu başlatın](./tutorial-00-selecting-your-certification.md)
- [Cihaz ayrıntıları ekleniyor](./tutorial-02-adding-device-details.md)
