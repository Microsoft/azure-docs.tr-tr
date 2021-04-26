---
title: Uyarıları yapılandırma-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Bu makalede PostgreSQL için Azure veritabanı-hiper ölçek (Citus) için ölçüm uyarılarını yapılandırma ve erişme açıklanmaktadır
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577090"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı-hiper ölçek (Citus) ölçümlerinde uyarıları ayarlamak için Azure portal kullanın

Bu makalede, Azure portal kullanarak PostgreSQL için Azure veritabanı uyarılarını ayarlama konusu gösterilmektedir. Azure hizmetleriniz için [izleme ölçümlerini](concepts-hyperscale-monitoring.md) temel alan bir uyarı alabilirsiniz.

Belirtilen bir ölçümün değeri bir eşiği aştığında tetiklenecek bir uyarı ayarlayacağız. Uyarı, koşul ilk karşılandığında tetiklenir ve daha sonra tetiklemeye devam eder.

Bir uyarıyı, tetiklendiğinde aşağıdaki eylemleri yapmak üzere yapılandırabilirsiniz:
* Hizmet yöneticisine ve ortak yöneticilere e-posta bildirimleri gönderin.
* Belirttiğiniz ek e-postalara e-posta gönderin.
* Web kancası çağırın.

Kullanarak uyarı kuralları hakkında bilgi alabilir ve bunları alabilirsiniz:
* [Azure portalı](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure İzleyici REST API'si](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure portaldan bir ölçüm hakkında uyarı kuralı oluşturma
1. [Azure Portal](https://portal.azure.com/), Izlemek Istediğiniz PostgreSQL Için Azure veritabanı sunucusu ' nu seçin.

2. Kenar çubuğunun **izleme** bölümü altında gösterildiği gibi **Uyarılar** ' ı seçin:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Uyarı kurallarını seçin":::

3. **Yeni uyarı kuralı** (+ simge) seçeneğini belirleyin.

4. **Kural oluştur** sayfası aşağıda gösterildiği gibi açılır. Gereken bilgileri doldurun:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Ölçüm uyarısı formu Ekle":::

5. **Koşul** bölümünde **Ekle**' yi seçin.

6. Uyarı almak için sinyaller listesinden bir ölçüm seçin. Bu örnekte, "depolama alanı yüzdesi" ni seçin.
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Ekran görüntüsü, çeşitli sinyalleri görüntüleyebileceğiniz sinyal mantığını Yapılandır sayfasını gösterir.":::

7. Uyarı mantığını yapılandırın:

    * **İşleç** (örn. "Büyüktür")
    * **Eşik değeri** (örn. yüzde 85)
    * **Toplama ayrıntı düzeyi** , uyarı tetikleyiciden önce ölçüm kuralının karşılanması gereken süre (örn. "Son 30 dakika içinde")
    * ve **değerlendirme sıklığı** (örn. "1 dakika")
   
   Tamamlandığında **bitti** ' yi seçin.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Ekran görüntüsü, uyarı mantığını yapılandırabileceğiniz bölmeyi gösterir.":::

8. Uyarı üzerinde bildirim almak üzere yeni bir grup oluşturmak için **eylem grupları** bölümünde **Yeni oluştur** ' u seçin.

9. "Eylem grubu Ekle" formunu bir ad, kısa ad, abonelik ve kaynak grubuyla doldurun.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Ekran görüntüsü, tanımlanan değerleri girebileceğiniz eylem Ekle Grup formunu gösterir.":::

10. **E-posta/SMS/Push/Voice** eylem türünü yapılandırın.
    
    Abonelik sahiplerine, katkıda bulunanlar ve okuyucular için bildirim göndermek üzere "e-posta Azure Resource Manager rolü" ni seçin.
   
    Tamamlandığında **Tamam ' ı** seçin.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Ekran görüntüsü e-posta/S g/t/ses bölmesini gösterir.":::

11. Bir uyarı kuralı adı, açıklaması ve önem derecesi belirtin.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Ekran görüntüsü Uyarı ayrıntıları bölmesini gösterir."::: 

12. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' u seçin.

    Birkaç dakika içinde, uyarı etkin ve daha önce açıklandığı gibi tetikler.

### <a name="managing-alerts"></a>Uyarıları yönetme

Bir uyarı oluşturduktan sonra, bunu seçebilir ve aşağıdaki eylemleri gerçekleştirebilirsiniz:

* Ölçüm eşiğini ve Bu uyarıyla ilgili olan önceki günün gerçek değerlerini gösteren bir grafik görüntüleyin.
* Uyarı kuralını **düzenleyin** veya **silin** .
* Bildirimleri almayı geçici olarak durdurmak veya yeniden başlatmak istiyorsanız, uyarıyı **devre dışı bırakın** veya **etkinleştirin** .

## <a name="suggested-alerts"></a>Önerilen uyarılar

### <a name="disk-space"></a>Disk alanı

İzleme ve uyarı her üretim hiper ölçek (Citus) sunucu grubu için önemlidir. Temel alınan PostgreSQL veritabanı, boş disk alanının doğru şekilde çalışmasını gerektirir. Disk doluysa, veritabanı sunucusu düğümü çevrimdışı olur ve kullanılabilir alan oluncaya kadar başlatmayı reddeder. Bu noktada, durumu onarmak için bir Microsoft destek isteği gerektirir.

Üretim dışı kullanım için bile, her sunucu grubundaki her düğümde disk alanı uyarılarını ayarlamayı öneririz. Disk alanı kullanım uyarıları, müdahale etmek ve düğümlerin sağlıklı kalmasını sağlamak için gerekli olan ön uyarıyı sağlar. En iyi sonuçlar için %75, %85 ve %95 kullanım ' de bir dizi uyarı deneyin. Hızlı veri alma, diski daha hızlı doldurduğundan, veri alma hızına göre seçim yapmak için kullanılacak yüzdeler.

Disk, alan sınırına yaklaşırsa, daha fazla boş alan almak için şu teknikleri deneyin:

* Veri saklama ilkesini gözden geçirin. Mümkünse, eski verileri soğuk depolamaya taşıyın.
* Sunucu grubuna [düğüm eklemeyi](howto-hyperscale-scale-grow.md#add-worker-nodes) ve parçaları yeniden dengelemeyi düşünün. Yeniden dengeleme, verileri daha fazla bilgisayar arasında dağıtır.
* Çalışan düğümlerinin [kapasitesini büyümeye](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) devam edin. Her çalışan en fazla 2 TiB depolama alanına sahip olabilir. Ancak düğüm ekleme işlemi daha hızlı tamamlandığından düğümlerin yeniden boyutlandırılması için düğüm ekleme denenmelidir.

### <a name="cpu-usage"></a>CPU kullanımı

CPU kullanımını izlemek, performans için bir taban çizgisi oluşturmak için kullanışlıdır. Örneğin, CPU kullanımının genellikle% 40-60 etrafında olduğunu fark edebilirsiniz. CPU kullanımı aniden %95 etrafında durmaya başlarsa bir anomali tanıyabilirsiniz. CPU kullanımı organik büyümeyi yansıtabilir, ancak aynı zamanda bir boş sorgu açığa çıkabilir. Bir CPU uyarısı oluştururken, uzun süreli bir ayrıntı düzeyini yakalamak ve kopan artışlarını yoksaymak için uzun bir toplama ayrıntı düzeyi ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar
* [Uyarılarda Web kancalarını yapılandırma](../azure-monitor/alerts/alerts-webhooks.md)hakkında daha fazla bilgi edinin.
* Hizmetinizin kullanılabilir olduğundan ve yanıt verebilmesini sağlamak için [ölçüm koleksiyonuna genel bakış](../azure-monitor/data-platform.md) alın.
