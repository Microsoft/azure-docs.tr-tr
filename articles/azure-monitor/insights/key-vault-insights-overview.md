---
title: Key Vault için Azure Izleyici ile Key Vault izleme | Microsoft Docs
description: Bu makalede, Anahtar kasaları için Azure Izleyicisi açıklanmaktadır.
services: azure-monitor
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: 91aed191e3bb165d6690759426a596df39f8c10f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582292"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault"></a>Key Vault için Azure Izleyici ile Anahtar Kasası hizmetinizi izleme
Key Vault için Azure Izleyici, Key Vault isteklerinizin, performanlarınızın, hatalarından ve gecikmelerinden oluşan Birleşik bir görünüm sunarak anahtar kasalarınızın kapsamlı bir şekilde izlenmesini sağlar.
Bu makale, Key Vault için Azure Izleyici deneyiminin nasıl ekleneceğini ve özelleştirildiğini anlamanıza yardımcı olur.

## <a name="introduction-to-azure-monitor-for-key-vault"></a>Key Vault için Azure Izleyici 'ye giriş

Deneyimle karşılaşmadan önce, bilgileri nasıl sunduklarını ve görselleştirir.
-    Gecikme, hataların dökümünü ve işlemlere ve gecikme süresine göre performansının anlık görüntü görünümünü gösteren **Ölçek perspektifinde** .
-   Ayrıntılı analizler gerçekleştirmek için belirli bir anahtar kasasının **detaya gitme analizini** yapın.
-    **Hangi ölçümleri** görmek istediğinizi değiştirmek, sınırlarınız ile hizalı eşikleri değiştirmek veya ayarlamak ve kendi çalışma kitabınızı kaydetmek için özelleştirebilirsiniz. Çalışma kitabındaki grafikler Azure panolarına sabitlenebilir.

Key Vault için Azure Izleyici, genel bir izleme çözümü sağlamak için hem günlükleri hem de ölçümleri birleştirir. Tüm kullanıcılar ölçüm tabanlı izleme verilerine erişebilir, ancak günlük tabanlı görselleştirmelerin eklenmesi kullanıcıların [Azure Key Vault günlüğünü etkinleştirmesini](../../key-vault/general/logging.md)gerektirebilir.

## <a name="view-from-azure-monitor"></a>Azure Izleyici 'den görüntüle

Azure Izleyici 'den, aboneliğinizdeki birden çok anahtar kasasından gelen istek, gecikme ve başarısızlık ayrıntılarını görüntüleyebilir ve performans sorunlarını ve kısıtlama senaryolarını belirlemenize yardımcı olabilirsiniz.

Tüm aboneliklerinizde anahtar kasalarınızın kullanımını ve işlemlerini görüntülemek için aşağıdaki adımları gerçekleştirin:

1. [Azure Portal](https://portal.azure.com/) oturum açın

2. Azure portal sol bölmeden **izleyici** ' yi seçin ve Öngörüler bölümünde, **Anahtar kasaları**' nı seçin.

![Birden çok grafik içeren genel bakış deneyiminin ekran görüntüsü](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Genel Bakış çalışma kitabı

Seçili abonelik için genel bakış çalışma kitabında, tablo, abonelik dahilinde gruplanmış Anahtar kasaları için etkileşimli Anahtar Kasası ölçümlerini görüntüler. Aşağıdaki açılan listelerden seçtiğiniz seçeneklere göre sonuçlara filtre uygulayabilirsiniz:

* Abonelikler – yalnızca anahtar kasaları içeren abonelikler listelenir.

* Anahtar kasaları: varsayılan olarak en fazla 5 Anahtar Kasası önceden seçilmiştir. Kapsam seçicide tüm veya birden çok anahtar kasalarını seçerseniz, en fazla 200 Anahtar Kasası döndürülür. Örneğin, seçtiğiniz üç abonelik arasında toplam 573 Anahtar Kasası varsa, yalnızca 200 kasa görüntülenecektir.

* Zaman aralığı: varsayılan olarak, yapılan ilgili seçimlere göre son 24 saati bilgileri görüntüler.

Sayaç kutucuğu, açılan liste altında, seçilen aboneliklerdeki anahtar kasalarının toplam sayısını kaydeder ve kaç tane seçili olduğunu yansıtır. Çalışma kitabının istek, başarısızlık ve gecikme süresi ölçümlerini rapor eden sütunları için koşullu renk kodlu ısı haritalarını vardır. En büyük renk en yüksek değere sahiptir ve en düşük değere göre daha açık bir renge sahiptir.

## <a name="failures-workbook"></a>Çalışma kitabı başarısız

Sayfanın üst kısmında bulunan **hataların** yanı sıra arızalar sekmesi açılır. API isabetlerinin, zaman içinde sıklık, belirli yanıt kodlarının miktarı ile birlikte gösterilir.

![Başarısızlık çalışma kitabının ekran görüntüsü](./media/key-vaults-insights-overview/failures.png)

Çalışma kitabındaki sütunlar için, API isabetlerinin bir mavi değerle rapor veren koşullu renk kodlaması veya ısı haritalarını vardır. En büyük renk en yüksek değere sahiptir ve en düşük değere göre daha açık bir renge sahiptir.

Çalışma kitabı başarıları (2xx durum kodları), kimlik doğrulama hatalarını (401/403 durum kodları), daraltma (429 durum kodları) ve diğer hataları (4xx durum kodları) görüntüler.

Durum kodlarının her birinin neyi temsil ettiğini daha iyi anlamak için, [Azure Key Vault durum ve yanıt kodlarıyla](../../key-vault/general/authentication-requests-and-responses.md)ilgili belgelerde okumanız önerilir.

## <a name="view-from-a-key-vault-resource"></a>Key Vault kaynağından görüntüleme

Key Vault için Azure Izleyici 'ye doğrudan bir anahtar kasasından erişmek için:

1. Azure portal, Anahtar kasaları ' nı seçin.

2. Listeden bir Anahtar Kasası seçin. İzleme bölümünde Öngörüler ' i seçin.

Bu görünümlere, Azure Izleyici düzeyi çalışma kitabından bir anahtar kasasının kaynak adı seçilerek de erişilebilir.

![Bir Anahtar Kasası kaynağından görünümün ekran görüntüsü](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Anahtar Kasası için **genel bakış** çalışma kitabında, hızlı bir şekilde değerlendirmenize yardımcı olan çeşitli performans ölçümleri gösterilmektedir:

- Anahtar Kasası işlemleri, gecikme süresi ve kullanılabilirliğiyle ilgili en önemli ayrıntıları gösteren etkileşimli performans grafikleri.

- Ölçümler ve durum kutucukları, hizmet kullanılabilirliğini, Anahtar Kasası kaynağına toplam işlem sayısını ve genel gecikme süresini vurgular.

**Hatalara** veya **işlemlere** yönelik diğer sekmelerin birini seçmek ilgili çalışma kitaplarını açar.

![Başarısızlık görünümü ekran görüntüsü](./media/key-vaults-insights-overview/resource-failures.png)

Hatalar çalışma kitabı, seçili zaman çerçevesinde tüm Anahtar Kasası isteklerinin sonuçlarını ortadan kaldıracak ve başarı (2xx), kimlik doğrulama hataları (401/403), azaltma (429) ve diğer hatalar üzerinde kategori sağlar.

![İşlemler görünümünün ekran görüntüsü](./media/key-vaults-insights-overview/operations.png)

Işlemler çalışma kitabı, kullanıcıların, en üst düzey kutucuklar kullanılarak sonuç durumuna göre filtrelenebilir tüm işlemlerin tam ayrıntılarına derinlemesine bakış sağlamasına olanak tanır.

![Tüm işlemlerin tüm ayrıntılarını içeren Işlemler çalışma kitabını gösteren ekran görüntüsü.](./media/key-vaults-insights-overview/info.png)

Kullanıcılar, üst tablodaki belirli işlem türlerine göre görünümleri Ayrıca, kullanıcıların bir açılan bağlam bölmesinde tam işlem ayrıntılarını görüntüleyebileceği, daha düşük tabloyu dinamik olarak güncelleştiren şekilde de kapsamını belirleyebilir.

>[!NOTE]
> Kullanıcıların bu çalışma kitabını görüntülemek için tanılama ayarlarının etkin olması gerektiğini unutmayın. Tanılama ayarını etkinleştirme hakkında daha fazla bilgi edinmek için [Azure Key Vault günlüğe kaydetme](../../key-vault/general/logging.md)hakkında daha fazla bilgi edinin.

## <a name="pin-and-export"></a>PIN ve dışarı aktarma

Bölümün sağ üst köşesindeki raptiye simgesini seçerek ölçüm bölümlerinin herhangi birini bir Azure panosuna sabitleyebilirsiniz.

Çoklu abonelik ve Anahtar kasaları genel bakış veya başarısızlık çalışma kitapları, raptiye simgesinin solunda bulunan İndir simgesini seçerek Excel biçimindeki sonuçları dışarı aktarmayı destekler.

![PIN simgesinin seçili ekran görüntüsü](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Key Vault için Azure Izleyicisini özelleştirme

Bu bölümde, veri analizi ihtiyaçlarınızı desteklemeye yönelik olarak özelleştirmek üzere çalışma kitabını düzenlemeyle ilgili yaygın senaryolar vurgulanmıştır:
*  Çalışma kitabını her zaman belirli bir abonelik veya Anahtar Kasası seçilecek şekilde kapsama
* Kılavuzdaki ölçümleri değiştirme
* İstek eşiğini değiştirme
* Renk işlemeyi değiştirme

En üstteki araç çubuğundan **Özelleştir** düğmesini seçerek düzenleme modunu etkinleştirerek özelleştirmeleri başlatabilirsiniz.

![Özelleştirme düğmesinin ekran görüntüsü](./media/key-vaults-insights-overview/customize.png)

Özelleştirmeler, yayımlanan çalışma kitabımızda varsayılan yapılandırmanın üzerine yazılmasını engellemek için özel bir çalışma kitabına kaydedilir. Çalışma kitapları, sizin için özel Raporlarım bölümünde veya kaynak grubuna erişimi olan herkesin erişebileceği paylaşılan Raporlar bölümünde bir kaynak grubuna kaydedilir. Özel çalışma kitabını kaydettikten sonra başlatmak için çalışma kitabı galerisine gitmeniz gerekir.

![Çalışma kitabı galerinin ekran görüntüsü](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Abonelik veya Anahtar Kasası belirtme

Aşağıdaki adımları gerçekleştirerek, çoklu abonelik ve Anahtar Kasası genel bakış ya da çalışma kitaplarını her çalıştırmada belirli bir aboneliğe veya anahtar kasalarına göre yapılandırabilirsiniz:

1. Portaldan **izleme** ' yi seçin ve ardından sol bölmedeki **Anahtar kasaları** ' nı seçin.
2. **Genel bakış** çalışma kitabında, komut çubuğundan **Düzenle**' yi seçin.
3. **Abonelikler** açılan listesinden, varsayılan olarak yo kullanmak istediğiniz bir veya daha fazla aboneliği seçin. Çalışma kitabının toplam 10 aboneliği seçmeyi desteklediğini unutmayın.
4. **Anahtar kasaları** açılan listesinden, varsayılan olarak kullanmak istediğiniz bir veya daha fazla hesabı seçin. Çalışma kitabının toplam 200 depolama hesabı seçmeyi desteklediğini unutmayın.
5. Özelleştirmelerinizle çalışma kitabının bir kopyasını kaydetmek için komut çubuğundan **farklı kaydet** ' i seçin ve ardından okuma moduna dönmek Için **Düzenle bitti** ' ye tıklayın.

## <a name="troubleshooting"></a>Sorun giderme

Genel sorun giderme kılavuzu için, adanmış çalışma kitabı tabanlı Öngörüler [sorun giderme makalesine](troubleshoot-workbooks.md)bakın.

Bu bölüm, Key Vault için Azure Izleyicisi 'ni kullanırken karşılaşabileceğiniz bazı yaygın sorunları tanılamada ve sorun gidermeye yardımcı olur. Belirli sorununuzla ilgili bilgileri bulmak için aşağıdaki listeyi kullanın.

### <a name="resolving-performance-issues-or-failures"></a>Performans sorunlarını veya başarısızlıklarını çözme

Key Vault için Azure Izleyici ile belirttiğiniz anahtar kasasıyla ilgili sorunları gidermeye yardımcı olmak için [Azure Key Vault belgelerine](../../key-vault/index.yml)bakın.

### <a name="why-can-i-only-see-200-key-vaults"></a>Neden yalnızca 200 anahtar kasalarını görebilirim?

Seçilebileceğini ve görüntülenebilecek 200 anahtar kasalarının bir sınırı vardır. Seçili aboneliklerin sayısından bağımsız olarak, seçili anahtar kasalarının sayısında 200 limiti vardır.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Abonelik seçicide neden tüm aboneliklerimi görmüyorum?

Yalnızca Azure portal üstbilgisindeki "Dizin + abonelik" bölümünde seçilen abonelik filtresinden seçtiğiniz anahtar kasalarını içeren abonelikler gösteriliyor.

![Abonelik filtresi ekran görüntüsü](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Key Vault içgörüler için değişiklik yapmak veya ek görselleştirme eklemek istiyorum, bunu nasıl yapabilirim?

Değişiklik yapmak için, çalışma kitabını değiştirmek üzere "düzenleme modunu" seçin, sonra çalışmanızı belirlenen bir aboneliğe ve kaynak grubuna bağlı yeni bir çalışma kitabı olarak kaydedebilirsiniz.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Çalışma kitaplarının herhangi bir bölümünü sabitledikten sonra zaman çizgisi nedir?

"Auto" zaman dilimi ' ni kullanıyoruz, bu nedenle hangi zaman aralığının seçili olduğuna bağlıdır.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Çalışma kitabının herhangi bir bölümünün sabitlendiği zaman aralığı nedir?

Zaman aralığı, pano ayarlarına bağlı olarak değişir.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Diğer verileri görmek veya kendi görselleştirmelerimi yapmak istersem ne yapmalıyım? Key Vault öngörülerini nasıl değiştirebilir?

Mevcut çalışma kitabını düzenleme modunun kullanımı ile düzenleyebilir ve sonra çalışmanızı tüm yeni değişiklerinizi alacak yeni bir çalışma kitabı olarak kaydedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Çalışma kitaplarının desteklemek için tasarlandıkları senaryoları, mevcut raporların yeni nasıl yazılacağını ve özelleştirildiğini ve [Azure izleyici çalışma kitaplarını kullanarak etkileşimli raporlar oluşturma](../visualize/workbooks-overview.md)konusunu gözden geçirin.
