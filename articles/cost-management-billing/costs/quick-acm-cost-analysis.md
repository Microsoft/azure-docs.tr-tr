---
title: Hızlı Başlangıç - Maliyet analiziyle Azure maliyetlerini keşfetme
description: Bu hızlı başlangıç, Azure kurumsal maliyetlerinizi keşfetmek ve analiz etmek için maliyet analizini kullanmanıza yardımcı olur.
author: bandersmsft
ms.author: banders
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2, devx-track-azurecli
ms.openlocfilehash: 4d0062258919de29750b644b2f8e12990e25098b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873226"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Hızlı Başlangıç: Maliyet analiziyle maliyetleri araştırma ve analiz etme

Azure maliyetlerinizi düzgün bir şekilde denetlemeden ve iyileştirmeden önce maliyetlerin kuruluşunuzun neresinden kaynaklandığını anlamanız gerekir. Hizmetlerinizin tutarının ne kadar olacağını bilmek, ortamlarınızı ve sistemlerinizi desteklemek için de yararlıdır. Maliyetlerin tüm kapsamıyla görünür olması kuruluşun harcama desenlerini doğru anlamak için önemlidir. Harcama desenlerini, bütçeler gibi maliyet denetim mekanizmalarını güçlendirmek için kullanabilirsiniz.

Bu hızlı başlangıçta, kurumsal maliyetlerinizi keşfetmek ve analiz etmek için maliyet analizini kullanırsınız. Maliyetlerin zaman içinde nerede oluştuğunu anlamak ve harcama eğilimlerini tanımlamak için kuruluşa göre toplanmış maliyetleri görüntüleyebilirsiniz. Bütçeye göre aylık, üç aylık, hatta yıllık maliyet eğilimlerini tahmin etmek için zaman içinde tahakkuk eden maliyetleri görüntüleyebilirsiniz. Bütçe, mali kısıtlamalara uymaya yardımcı olur. Bütçe, harcama düzensizliklerini yalıtmak amacıyla günlük veya aylık maliyetleri görüntülemek için de kullanılır. Dahası, geçerli raporun verilerini daha fazla analiz etmek için veya dış sistemde kullanmak üzere indirebilirsiniz.

Bu hızlı başlangıçta şunları yapmayı öğrenirsiniz:

- Maliyet analizinde maliyetleri gözden geçirme
- Maliyet görünümlerini özelleştirme
- Maliyet analizi verilerini indirme

## <a name="prerequisites"></a>Ön koşullar

Maliyet analizi, farklı türdeki Azure hesaplarını destekler. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](understand-cost-mgt-data.md). Maliyet verilerini görüntülemek için Azure hesabınızda en azından okuma yetkisine sahip olmanız gerekir.

Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](./assign-access-acm-data.md).

Yeni bir aboneliğiniz varsa Maliyet Yönetimi özelliklerini hemen kullanamazsınız. Maliyet Yönetimi özelliklerini kullanabilmek için 48 saat kadar beklemeniz gerekebilir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

- https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="review-costs-in-cost-analysis"></a>Maliyet analizinde maliyetleri gözden geçirme

Maliyetlerinizi maliyet analizinde incelemek için Azure portalında kapsamı açın ve menüden **Maliyet analizi**'ni seçin. Örneğin **Abonelikler**'e gidin, listeden bir abonelik belirleyin ve menüden **Maliyet analizi**'ni seçin. Maliyet analizinde farklı bir kapsama geçiş yapmak için **Kapsam** hapını kullanın.

Veri birleştirmesi sağlamak ve maliyet bilgilerine erişimi denetlemek için seçtiğiniz kapsam Maliyet Yönetimi’nin tamamında kullanılır. Kapsamları kullandığınızda, birden çok kapsam seçemezsiniz. Bunun yerine, diğerlerinin toplandığı büyük bir kapsam seçer ve iç içe yerleştirilmiş kapsamları ihtiyacınıza göre daraltırsınız. Bazı kullanıcılar birden fazla iç içe yerleştirilmiş kapsamı içine alan tek bir üst kapsama erişim iznine sahip olmayabileceğinden bu yaklaşımı anlamak önemlidir.

Maliyet Analizi’nin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için [Azure portalında Maliyet Yönetimi’ni kullanma](https://www.youtube.com/watch?v=mfxysF-kTFA) videosunu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/mfxysF-kTFA]

İlk maliyet analizi görünümünde aşağıdaki alanlar bulunur.

**Birikmiş maliyet görünümü**: Önceden tanımlanmış maliyet analizi görünümü yapılandırmasını temsil eder. Her bir görünüm tarih aralığı, ayrıntı düzeyi, gruplama ölçütü ve filtreleme ayarlarını içerir. Varsayılan görünümde geçerli faturalama dönemi içinde birikmiş maliyetler gösterilir ama başka yerleşik görünümlere geçebilirsiniz.

**Gerçek maliyet**: Geçerli aya ait olan toplam kullanım ve satın alma maliyetlerini, tahakkuk ettikleri ve faturanızda görüntülenecekleri şekilde gösterir.

**Tahmin**: Seçtiğiniz zaman dönemine ait tahmini maliyet toplamını gösterir.

**Bütçe**: Varsa, seçilen kapsam için planlanan harcama limitini gösterir.

**Birikmiş ayrıntı düzeyi**: Fatura döneminin başından itibaren toplam günlük maliyetleri gösterir. Fatura hesabınız veya aboneliğiniz için bütçe oluşturduktan sonra, bütçeye göre harcama eğiliminizi hemen görebilirsiniz. Tarihin üzerine gelerek o gün için birikmiş maliyeti görüntüleyebilirsiniz.

**Özet (halka) grafikler**: Toplam maliyeti ortak bir standart özellikler kümesine ayırarak dinamik özetler sağlar. Geçerli ay için en büyükten en küçüğe kadar tüm maliyetleri gösterir. İstediğiniz zaman farklı bir özet seçerek özet grafikleri değiştirebilirsiniz. Maliyetler varsayılan olarak şu kategorilere ayrılır: hizmet (ölçüm kategorisi), konum (bölge) ve alt kapsam. Örneğin, fatura hesapları altında kayıt hesapları, abonelikler altında kaynak grupları ve kaynak grupları altında kaynaklar bulunur.

![Azure portalındaki maliyet analizinin ilk görünümü](./media/quick-acm-cost-analysis/cost-analysis-01.png)

### <a name="understand-forecast"></a>Tahmini anlama

Son kullanımınız doğrultusunda, maliyet tahminleri seçili dönem için tahmini maliyetlerinizin bir projeksiyonunu gösterir. Maliyet analizinde bir bütçe ayarlandıysa, tahmini harcamanın ne zaman bütçe eşiğini aşmayı görüntüleyebilirsiniz. Tahmin modeli, gelecekteki maliyetleri bir yıla kadar tahmin edebilir. Seçtiğiniz boyutun ayrıntılı tahmini maliyetini görüntülemek için filtreler ' i seçin.

Tahmin modeli, zaman serisi regresyon modelini temel alır. Maliyetleri doğru bir şekilde tahmin etmek için en az 10 günlük güncel maliyetler ve kullanım verileri gerekir. Belirli bir süre için tahmin modeli, tahmin dönemine ait eşit eğitim verilerine ihtiyaç duyar. Örneğin üç aylık tahmin için en az üç aylık güncel maliyet ve kullanım verileri gerekir.

## <a name="customize-cost-views"></a>Maliyet görünümlerini özelleştirme

Maliyet analizi, en yaygın hedeflere göre iyileştirilmiş dört yerleşik görünüme sahiptir:

Görünüm | Yanıt verdiği sorular
--- | ---
Birikmiş maliyet | Bu ay şimdiye kadar ne kadar harcama yaptım? Bütçemin dışına çıkar mıyım?
Günlük maliyet | Son 30 gün içinde günlük maliyetlerde artış yaşandı mı?
Hizmete göre maliyet | Son üç faturada aylık kullanım nasıl değişti?
Kaynağa göre maliyet | Bu ay en yüksek maliyete sahip olan kaynaklar hangileri?
Fatura ayrıntıları | Son faturamdaki ücretler nelerdi?

![Bu ay için örnek seçimi gösteren görünüm seçici](./media/quick-acm-cost-analysis/view-selector.png)

Öte yandan, birçok durumda daha derin analizler gerekir. Özelleştirme, seçilen tarihle sayfanın en üstünde başlatılır.

Maliyet analizi, varsayılan olarak geçerli ayın verilerini gösterir. Tarih seçiciyi kullanarak yaygın tarih aralıkları arasında hızlıca geçiş yapabilirsiniz. Örneğin son yedi gün, geçen ay, geçerli yıl veya özel tarih aralığı seçebilirsiniz. Kullandıkça öde abonelikleri ayrıca faturalama döneminize göre belirlenen ve takvim ayına bağlı olmayan tarih aralıklarına da sahiptir (geçerli faturalama dönemi veya son fatura gibi). Menünün üst tarafındaki **<ÖNCEKİ** ve **SONRAKİ>** bağlantılarını kullanarak önceki veya sonraki döneme geçiş yapabilirsiniz. Örneğin **<ÖNCEKİ** bağlantısı, **Son 7 gün** görünümünü **8-14 gün önce** veya **15-21 gün önce** olarak değiştirir. Özel bir tarih aralığı belirlerken en fazla bir yıl (örneğin, 1 Ocak-31 Aralık) seçebileceğinizi unutmayın.

![Bu ay için örnek seçimi gösteren tarih seçici](./media/quick-acm-cost-analysis/date-selector.png)

Maliyet analizi varsayılan olarak **birikmiş** maliyetleri gösterir. Birikmiş maliyetler, günlük maliyetleri önceki günlerin maliyetleriyle toplayarak ilerler ve günlük birikmiş maliyetlerinizin sürekli artan bir görünümünü sunar. Bu görünüm, seçilen zaman aralığı için bütçeye göre nasıl bir eğilim gösterdiğinizi ortaya koymak için iyileştirilmiştir.

Olası bütçe ihlallerini belirlemek için tahmin grafiği görünümünü kullanabilirsiniz. Olası bir bütçe ihlali söz konusu olduğunda tahmini fazla harcama kırmızı renkle gösterilir. Ayrıca grafikte bir gösterge simgesi de gösterilir. Simgenin üzerine geldiğinizde bütçe ihlali için tahmini tarih gösterilir.

![Olası bütçe ihlalini gösteren örnek](./media/quick-acm-cost-analysis/budget-breach.png)

Ayrıca, her günün maliyetlerini gösteren bir **günlük** görünüm vardır. Günlük görünüm büyüme eğilimini göstermez. Görünüm, günden güne maliyet sıçrama yaptığında veya iyice düştüğünde ortaya çıkan düzensizlikleri gösterecek şekilde tasarlanmıştır. Bütçe seçtiyseniz, günlük görünüm günlük bütçenize ilişkin bir tahmin de gösterir.

Günlük maliyetleriniz tutarlı olarak tahmini günlük bütçenin üzerinde olduğunda aylık bütçenizin aşılacağını öngörebilirsiniz. Tahmini günlük bütçe, bütçenizi alt düzeyde görselleştirmeye yardımcı olan bir araçtır. Günlük maliyetlerinizde dalgalanmalar olduğunda tahmini günlük bütçenin aylık bütçeyle karşılaştırılması daha az kesinlik sağlar.

Harcama tahmini etkinleştirildiğinde son harcamaların günlük görünümü aşağıdaki gibi olur.
![Geçerli aya ait örnek günlük maliyetleri gösteren günlük görünüm](./media/quick-acm-cost-analysis/daily-view.png)

Harcama tahmini kapattığınızda ileri tarihler için öngörülen harcamayı göremezsiniz. Ayrıca geçmişe ait maliyetlere baktığınızda maliyet tahmininde herhangi bir maliyet gösterilmez.

Genellikle 8-12 saat içinde tüketilen kaynaklara ait veriler veya bildirimler gösterilir.

Maliyetlerin dökümünü almak ve en çok katkıda bulunanları belirlemek için ortak özelliklere göre **gruplayın**. Örneğin kaynak etiketlerine göre gruplamak için istediğiniz etiket anahtarını seçin. Her bir etiket değerinin maliyetlerine ait döküm gösterilir ve etiketin uygulanmadığı kaynaklar ek bir segmente eklenir.

Çoğu Azure kaynağı etiketlemeyi destekler. Ancak bazı etiketler Maliyet Yönetimi ve faturalandırma için kullanılamaz. Ayrıca kaynak gruplarının etiketlenmesi de desteklenmez. Etiket desteği, etiket kaynağa uygulandıktan *sonra* bildirilen kullanımlar için geçerlidir. Etiketler, maliyet toplamları için geriye dönük olarak uygulanmaz.

Maliyet verilerinin görünürlüğünü artırmak üzere Azure etiket ilkesini kullanma hakkında bilgi edinmek için [Azure Maliyet Yönetimi ile etiket ilkelerini gözden geçirme](https://www.youtube.com/watch?v=nHQYcYGKuyw) videosunu izleyin.

Burada, geçerli aya ait Azure hizmet maliyetlerinin bir görünümü yer almaktadır.

![Geçen aya ait örnek Azure hizmet maliyetlerini gösteren gruplandırılmış günlük birikmiş görünüm](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Maliyet analizi varsayılan olarak tüm kullanım ve satın alma maliyetlerini tahakkuk ettikleri ve faturanızda görünecekleri şekilde gösterir. Buna **Gerçek maliyet** de denir. Gerçek maliyeti görüntülemek, faturanızı mutabık kılmak için idealdir. Ancak harcama anomalileri ve diğer maliyet değişikliklerini takip ederken maliyette yaşanan satın alma kaynaklı ani artışlar uyarı verilmesine neden olabilir. Rezervasyon satın alma maliyetlerinin neden olduğu ani artışları ortadan kaldırmak için **Amorti edilen maliyet** görünümüne geçin.

![Rezervasyon satın alma işlemlerinin döneme yayıldığını ve rezervasyonu kullanan kaynaklara ayrıldığını görmek için gerçek ve amorti edilen maliyetler arasında geçiş yapın](./media/quick-acm-cost-analysis/metric-picker.png)

Amorti edilen maliyet, rezervasyon satın alma işlemlerini günlük parçalara böler ve rezervasyon süresine yayar. Örneğin 1 Ocak tarihinde 365 ABD doları tutarında bir satın alma işlemi görmek yerine 1 Ocak ile 31 Aralık arasındaki her gün 1,00 ABD doları değerinde satın alma işlemi görürsünüz. Bu maliyetler, temel amorti işlemlerinin yanı sıra rezervasyonu kullanan kaynaklara yeniden atanır ve onlarla ilişkilendirilir. Örneğin 1,00 ABD doları değerindeki günlük ücretin iki sanal makineye ayrılması durumunda gün içinde iki farklı 0,50 ABD doları ücret görürsünüz. Rezervasyonun bir bölümünün aynı gün kullanılmaması halinde ilgili sanal makine ile ilişkilendirilmiş 0,50 ABD doları değerinde ücret ve `UnusedReservation` ücret türüne sahip ayrı bir 0,50 ABD doları harcama görürsünüz. Kullanılmayan rezervasyon maliyetleri yalnızca itfa edilen maliyet gösterilirken görülebilir.

Maliyetlerin gösterilmesindeki değişiklik nedeniyle gerçek maliyet ile amorti edilen maliyet görünümlerinde farklı toplam değerlerin gösterildiğini unutmayın. Genel olarak amorti edilen maliyetler görüntülendiğinde rezervasyon satın alma işlemi gerçekleştirilen ayların toplam maliyeti azalacak, rezervasyon satın alma işlemini takip eden ayların maliyeti ise artacaktır. Amorti etme yalnızca rezervasyon satın alma işlemleri için geçerlidir ve şu an için Azure Market satın alma işlemlerine uygulanmaz.

Aşağıdaki görüntüde kaynak grubu adları gösterilmiştir. Etiketlere göre gruplandırarak etiket başına toplam maliyeti görüntüleyebilir veya **Kaynağa göre maliyet** görünümünü kullanarak belirli bir kaynağa ait olan tüm etiketleri görebilirsiniz.

![Kaynak grubu adlarını gösteren tam kapsamlı geçerli görünüm](./media/quick-acm-cost-analysis/full-data-set.png)

Maliyetler belirli bir özniteliğe göre gruplanırken maliyet açısından ilk 10 katkıda bulunan en yüksekten en düşüğe doğru gösterilir. Toplamda 10'dan fazla grup varsa maliyet açısından ilk dokuz katkıda bulunana ek olarak kalan tüm grupları temsil eden bir **Diğer** grubu gösterilir. Etiketlere göre gruplandırma yaptığınızda etiket anahtarının uygulanmadığı maliyetler için bir **Etiketlenmemiş** grubu görünür. Etiketlenmemiş maliyetler diğer etiketlenmiş maliyetlerden yüksek olsa dahi **Etiketlenmemiş** grubu her zaman son sırada yer alır. 10 veya daha fazla etiket değeri varsa etiketlenmemiş maliyetler, **Diğer** grubuna dahil edilir. Tüm değerleri en yüksek maliyetten en düşük maliyete doğru sıralanmış şekilde görmek için tablo görünümüne geçin ve ayrıntı düzeyini **Yok** olarak ayarlayın.

Klasik sanal makineler, ağ ve depolama kaynakları ayrıntılı fatura bilgisi paylaşmaz. Bunlar maliyet gruplarında **Klasik hizmetler** olarak gösterilir.

Ana grafiğin altındaki özet grafikler farklı gruplandırmalar gösterir ve bunlar seçilen zaman aralığı ve filtreler için genel maliyetlerinizin daha geniş bir görünümünü sunar. Toplu maliyetleri herhangi bir boyuta göre görüntülemek için bir özellik veya etiket seçin.

![Özet grafikleri gösteren örnek](./media/quick-acm-cost-analysis/pivot-charts.png)

Herhangi bir görünümde tam veri kümesini görüntüleyebilirsiniz. Sunulan veriler, uyguladığınız seçimlere veya filtrelere göre belirlenir. Tam veri kümesini görmek için **grafik türü** listesini ve ardından **Tablo** görünümünü seçin.

![Geçerli görünüm için tablo görünümünde sunulan veriler](./media/quick-acm-cost-analysis/chart-type-table-view.png)

## <a name="saving-and-sharing-customized-views"></a>Özelleştirilmiş görünümleri kaydetme ve paylaşma

Özelleştirilmiş görünümleri kaydetmek ve başkalarıyla paylaşmak için maliyet analizini Azure portalı panosuna sabitleyebilir veya maliyet analizi bağlantısını kopyalayabilirsiniz.

Portalı kullanarak kuruluşunuzda maliyet bilgilerini paylaşma hakkında daha fazla bilgi edinmek için [Azure Maliyet Yönetimi’nde görünümleri paylaşma ve kaydetme](https://www.youtube.com/watch?v=kQkXXj-SmvQ) videosunu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/kQkXXj-SmvQ]

Maliyet analizini sabitlemek için sağ üst köşede veya "<Subscription Name> | Maliyet analizi" öğesinden sonra yer alan raptiye simgesini seçin. Maliyet analizini sabitlediğinizde yalnızca ana grafik veya tablo görünümü kaydedilir. Panoyu kaydederek kutucuğa başkalarının da erişmesini sağlayabilirsiniz. Paylaşım yalnızca pano yapılandırmasını paylaşır ve başkalarına temel alınan veriler için erişim izni vermez. Maliyetlere erişiminiz yoksa ancak paylaşılan bir panoya erişebiliyorsanız "erişim engellendi" iletisiyle karşılaşırsınız.

Maliyet analizi bağlantısını paylaşmak için pencerenin üst tarafında **Paylaş**'ı seçin. Bu belirli kapsam için belirli görünümü açan özel bir URL gösterilir. Maliyetlere erişiminiz yoksa bu URL'yi kullandığınızda "erişim engellendi" iletisiyle karşılaşırsınız.

## <a name="download-usage-data"></a>Kullanım verilerini indirme

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ek analiz gerçekleştirmek için verileri indirmek, kendi verilerinizle birleştirmek veya kendi sistemlerinizle tümleştirmek isteyebilirsiniz. Maliyet Yönetimi birkaç farklı seçenek sunar. Başlangıç noktası olarak maliyet analizinde olduğu gibi anlık üst düzey özete ihtiyacınız varsa ihtiyacınız olan görünümü oluşturmanız gerekir. Ardından **Dışarı aktar**'ı ve ardından **Verileri CSV olarak indir** veya **Verileri Excel'e indir**'i seçerek oluşturduğunuz görünümü indirebilirsiniz. Excel dosyası kapsam, sorgu yapılandırması, toplam ve oluşturma tarihi gibi indirilen dosyayı oluşturmak için kullandığınız görünümle ilgili ek bağlam bilgisi sunar.

Tam, toplanmamış veri kümesine ihtiyacınız varsa ödeme hesabından indirebilirsiniz. Ardından, portalın sol gezinti bölmesindeki hizmetler listesinden **Maliyet Yönetimi + Faturalandırma**'yı seçin. Gerekirse ödeme hesabınızı seçin. **Kullanım + ücretler**'e gidip bir faturalama dönemine ait **İndir** simgesini seçin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Başlangıç olarak ortamınızı Azure CLI için hazırlayın:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Oturum açtıktan sonra [az costmanagement query](/cli/azure/costmanagement#az_costmanagement_query) komutunu kullanarak aboneliğiniz için ay başından bugüne kadarki kullanım bilgilerini sorgulayın:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

Ayrıca **--dataset-filter** parametresini veya başka parametreleri kullanarak sorguyu daraltabilirsiniz:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" \
   --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

**--dataset-filter** parametresi bir JSON dizesi veya `@json-file` alır.

Kullanım verilerini bir Azure depolama hesabına aktarmak için [az costmanagement export](/cli/azure/costmanagement/export) komutlarını da kullanabilirsiniz. Verileri oradan indirebilirsiniz.

1. Bir kaynak grubu oluşturun veya mevcut kaynak grubunu kullanın. Kaynak grubu oluşturmak için [az group create](/cli/azure/group#az_group_create) komutunu çalıştırın:

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Dışarı aktarmaları almak için bir depolama hesabı oluşturun veya mevcut depolama hesabını kullanın. Hesap oluşturmak için [az storage account create](/cli/azure/storage/account#az_storage_account_create) komutunu kullanın:

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Dışarı aktarmayı oluşturmak için [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) komutunu kullanın:

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

- Maliyet analizi için özelleştirilmiş bir görünüm sabitlediyseniz ve artık buna ihtiyacınız yoksa bu görünümü sabitlediğiniz panoya gidin ve sabitlenmiş görünümü silin.
- Kullanım veri dosyalarını indirdiyseniz ve artık ihtiyaç duymuyorsanız bunları sildiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bütçelerin nasıl oluşturulduğunu ve yönetildiğini öğrenmek için ilk öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Bütçe oluşturma ve yönetme](tutorial-acm-create-budgets.md)
