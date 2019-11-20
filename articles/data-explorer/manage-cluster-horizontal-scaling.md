---
title: Değişiklik talebini karşılamak için Azure Veri Gezgini küme yatay ölçeklendirmeyi (genişleme) yönetme
description: Bu makalede, değişen talebe göre bir Azure Veri Gezgini kümesinde ölçek genişletme ve ölçeklendirme adımları açıklanır.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 70e6bdfcf9718244632ad02e09d3ddadee71a617
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311567"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Değişiklik talebini karşılamak için Azure Veri Gezgini küme yatay ölçeklendirmeyi (genişleme) yönetme

Bir kümeyi uygun şekilde boyutlandırmak, Azure Veri Gezgini performansı açısından önemlidir. Statik küme boyutu, ne kadar ideal bir deyişle, kullanımı veya kullanımı aşırı olabilir.

Bir kümedeki talep mutlak doğrulukla tahmin edilebileceği için, değişen talebe sahip kapasite ve CPU kaynakları ekleme ve kaldırma, bir kümeyi *ölçeklendirmek* daha iyidir. 

Azure Veri Gezgini kümesinin ölçeklendirilmesi için iki iş akışı vardır: 

* Yatay ölçekleme, Ayrıca, ölçeklendirilmesi ve çıkışı da denir.
* [Dikey ölçekleme](manage-cluster-vertical-scaling.md), ölçeği artırma ve azaltma olarak da bilinir.

Bu makalede yatay ölçeklendirme iş akışı açıklanmaktadır.

## <a name="configure-horizontal-scaling"></a>Yatay ölçeklendirmeyi Yapılandır

Yatay ölçeklendirmeyi kullanarak, önceden tanımlanmış kurallara ve zamanlamaya göre örnek sayısını otomatik olarak ölçeklendirebilirsiniz. Kümenizin otomatik ölçeklendirme ayarlarını belirtmek için:

1. Azure portal Azure Veri Gezgini küme kaynağına gidin. **Ayarlar**altında **ölçeği Genişlet**' i seçin. 

2. **Genişleme** penceresinde, istediğiniz otomatik ölçeklendirme yöntemini seçin: **El ile ölçeklendirme**, **iyileştirilmiş otomatik**ölçeklendirme veya **özel otomatik ölçeklendirme**.

### <a name="manual-scale"></a>El ile ölçeklendirme

El ile ölçeklendirme, küme oluşturma sırasında varsayılan ayardır. Küme otomatik olarak değişmeyen bir statik kapasiteye sahiptir. **Örnek sayısı** çubuğunu kullanarak statik kapasiteyi seçersiniz. Kümenin ölçeklendirilmesi, başka bir değişiklik yapana kadar bu ayarda kalır.

   ![El ile ölçeklendirme yöntemi](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>İyileştirilmiş otomatik ölçeklendirme

İyileştirilmiş otomatik ölçeklendirme önerilen otomatik ölçeklendirme yöntemidir. Bu yöntem, küme performansını ve maliyetlerini iyileştirir. Küme, kullanım kapsamında bir duruma yaklaşırsa, bu, içinde ölçeklendirilir. Bu eylem maliyetleri düşürür, ancak performans düzeyini korur. Küme, aşırı kullanım durumuna yaklaşırsa, en iyi performansı elde etmek için bu durum ayarlanır. En Iyileştirilmiş otomatik ölçeklendirmeyi yapılandırmak için:

1. **En iyileştirilmiş otomatik ölçeklendirme**seçeneğini belirleyin. 

1. Minimum örnek sayısı ve en fazla örnek sayısı seçin. Küme otomatik ölçeklendirme, yük temel alınarak bu iki sayı arasında aralıklar.

1. **Kaydet**’i seçin.

   ![İyileştirilmiş otomatik ölçeklendirme yöntemi](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

İyileştirilmiş otomatik ölçeklendirme çalışmaya başlar. Eylemleri artık kümenin Azure etkinlik günlüğünde görünür.

### <a name="custom-autoscale"></a>Özel otomatik ölçeklendirme

Özel otomatik ölçeklendirmeyi kullanarak, belirttiğiniz ölçümlere göre kümenizi dinamik olarak ölçeklendirebilirsiniz. Aşağıdaki grafikte, özel otomatik ölçeklendirmeyi yapılandırmak için akış ve adımlar gösterilmektedir. Daha fazla ayrıntı grafiği izler.

1. **Otomatik ölçeklendirme ayarı adı** kutusuna, *genişleme: önbellek kullanımı*gibi bir ad girin. 

   ![Ölçek kuralı](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. **Ölçek modu**için, **ölçüm temelinde ölçek**' i seçin. Bu mod dinamik ölçeklendirme sağlar. Ayrıca, **belirli bir örnek sayısına göre ölçeklendir**' i de seçebilirsiniz.

3. **+ Kural Ekle**' yi seçin.

4. Sağdaki **Ölçek kuralı** bölümünde her bir ayar için değerler girin.

    **Ölçütler**

    | Ayar | Açıklama ve değer |
    | --- | --- |
    | **Zaman toplama** | **Ortalama**gibi bir toplama ölçütü seçin. |
    | **Ölçüm adı** | Ölçek işleminin, **önbellek kullanımı**gibi temel alarak olmasını istediğiniz ölçümü seçin. |
    | **Zaman dilimi istatistiği** | **Ortalama**, **En düşük**, **en yüksek**ve **Toplam**arasında seçim yapın. |
    | **İşleci** | **Büyük veya eşittir**gibi uygun seçeneği belirleyin. |
    | **Eşiği** | Uygun bir değer seçin. Örneğin, önbellek kullanımı için yüzde 80 iyi bir başlangıç noktasıdır. |
    | **Süre (dakika cinsinden)** | Sistemin ölçümleri hesaplarken geri araması için uygun bir süre seçin. Varsayılan 10 dakikalık ile başlayın. |
    |  |  |

    **Eylem**

    | Ayar | Açıklama ve değer |
    | --- | --- |
    | **İşlem** | Ölçeklemek veya ölçeklendirmek için uygun seçeneği belirleyin. |
    | **Örnek sayısı** | Ölçüm koşulu karşılandığında eklemek veya kaldırmak istediğiniz düğüm veya örnek sayısını seçin. |
    | **Seyrek Erişimli (dakika)** | Ölçek işlemleri arasında beklenecek uygun bir zaman aralığı seçin. Varsayılan beş dakikalık bir başlangıç yapın. |
    |  |  |

5. **Add (Ekle)** seçeneğini belirleyin.

6. Sol taraftaki **örnek sınırları** bölümünde her bir ayar için değerler girin.

    | Ayar | Açıklama ve değer |
    | --- | --- |
    | **En düşük** | Kullanıma almadan, kümenizin aşağıda ölçeklendirmeyeceği örnek sayısı. |
    | **Çok** | Kullanıma göre, kümenizin üzerine ölçeklenmeyeceği örnek sayısı. |
    | **Varsayılan** | Varsayılan örnek sayısı. Bu ayar, kaynak ölçümlerini okumada sorunlar varsa kullanılır. |
    |  |  |

7. **Kaydet**’i seçin.

Azure Veri Gezgini kümeniz için artık yatay ölçeklendirmeyi yapılandırdınız. Dikey ölçeklendirme için başka bir kural ekleyin. Küme ölçeklendirme sorunlarıyla ilgili yardıma ihtiyacınız varsa Azure portal [bir destek isteği açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) .

## <a name="next-steps"></a>Sonraki adımlar

* [Ölçümler ile Azure Veri Gezgini performansını, sistem durumunu ve kullanımını izleyin](using-metrics.md)

* Kümenin uygun boyutu için [küme dikey ölçeklendirmesini yönetin](manage-cluster-vertical-scaling.md) .
