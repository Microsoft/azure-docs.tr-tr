---
title: Ölçümler ile Azure Veri Gezgini performansını, sistem durumunu ve kullanımını izleyin
description: Kümenin performansını, sistem durumunu ve kullanımını izlemek için Azure Veri Gezgini ölçümlerini nasıl kullanacağınızı öğrenin.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173782"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Ölçümler ile Azure Veri Gezgini performansını, sistem durumunu ve kullanımını izleyin

Azure Veri Gezgini uygulamalar, web siteleri, IoT cihazları ve daha fazlasından akışı yapılan büyük miktarda veri üzerinde gerçek zamanlı analiz yapmaya yönelik hızlı ve tam olarak yönetilen bir veri analizi hizmetidir. Azure Veri Gezgini kullanmak için, önce bir küme oluşturun ve bu kümede bir veya daha fazla veritabanı oluşturursunuz. Daha sonra sorguları bu verilere karşı çalıştırmak için bir veritabanına (yükleme) sahip olursunuz. Azure Veri Gezgini ölçümleri, küme kaynaklarının sistem durumu ve performansına göre önemli göstergeler sağlar. Azure Veri Gezgini küme durumunu ve belirli bir senaryonuzdaki performansı tek başına ölçümler olarak izlemek için bu makalede ayrıntılı ölçümleri kullanın. Ölçümleri, işletimsel [Azure panoları](/azure/azure-portal/azure-portal-dashboards) ve [Azure uyarıları](/azure/azure-monitor/platform/alerts-metric-overview)temeli olarak da kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/)oluşturun.

* Bir [küme ve veritabanı](create-cluster-database-portal.md)oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="using-metrics"></a>Ölçümleri kullanma

Azure Veri Gezgini kümenizde **ölçümler** ' i seçerek ölçümler bölmesini açın ve kümenizdeki Analize başlayın.

![Ölçümleri seçin](media/using-metrics/select-metrics.png)

Ölçümler bölmesinde:

![Ölçümler bölmesi](media/using-metrics/metrics-pane.png)

1. Ölçüm grafiği oluşturmak için aşağıda açıklandığı şekilde **ölçüm adı ve** ölçüm başına uygun **toplama** ' yı seçin. **Kaynak** ve **ölçüm ad alanı** seçiciler, Azure Veri Gezgini kümeniz için önceden seçilmiştir.

    **Ölçüm** | **Birim** | **Toplama** | **Ölçüm açıklaması**
    |---|---|---|---|
    | Önbellek kullanımı | Percent | Ortalama, en fazla, en az | Küme tarafından şu anda kullanılan ayrılmış önbellek kaynaklarının yüzdesi. Önbellek, tanımlı önbellek ilkesine göre Kullanıcı etkinliği için ayrılan SSD boyutunu ifade eder. Ortalama önbellek kullanımı %80 veya daha az bir küme için sürdürülebilir bir durumdur. Ortalama önbellek kullanımı %80 ' den fazla ise, küme, depolama için iyileştirilmiş bir fiyatlandırma katmanına [ölçeklendirilebilir](manage-cluster-vertical-scaling.md) veya daha fazla örneğe [ölçeklenmelidir](manage-cluster-horizontal-scaling.md) . Alternatif olarak, önbellek ilkesini uyarlayın (önbellekte daha az gün). Önbellek kullanımı %100 ' den fazlaysa, önbelleğe alma ilkesine göre önbelleğe alınacak verilerin boyutu, kümedeki toplam önbellek boyutudur. |
    | CPU | Percent | Ortalama, en fazla, en az | Kümedeki makineler tarafından şu anda kullanılmakta olan ayrılmış işlem kaynaklarının yüzdesi. Bir küme için bir ortalama %80 veya daha az CPU, sürdürülebilirlik. En yüksek CPU değeri %100, bu da verileri işlemek için ek bilgi işlem kaynakları olmadığı anlamına gelir. Bir küme iyi gerçekleştirmediğinde, engellenen belirli CPU 'ların olup olmadığını öğrenmek için CPU 'nun en büyük değerini denetleyin. |
    | İşlenen Olaylar (Event Hubs için) | Sayı | Max, min, Sum | Olay Hub 'larından okunan ve küme tarafından işlenen toplam olay sayısı. Olaylar, reddedilen olaylara bölünür ve küme altyapısı tarafından kabul edilen olaylar. |
    | Alma gecikmesi | Saniye | Ortalama, en fazla, en az | Sorgu için hazırlanana kadar, verilerin kümede alındığı zamandan itibaren alınan veri gecikmesi. Alım gecikmesi dönemi alma senaryosuna bağlıdır. |
    | Alım sonucu | Sayı | Sayı | Başarısız ve başarılı olan alma işlemlerinin toplam sayısı. Başarı ve başarısızlık sonuçlarının demetlerini oluşturmak ve boyutları çözümlemek için **bölmeyi Uygula** ' yı kullanın (**değer** > **durumu**).|
    | Alım kullanımı | Percent | Ortalama, en fazla, en az | Kapasite ilkesinde, alma işlemini gerçekleştirmek için ayrılan toplam kaynaklardan verileri almak için kullanılan gerçek kaynakların yüzdesi. Varsayılan kapasite ilkesi 512 ' den fazla eşzamanlı alım işlemi veya kaynak için yatırılan küme kaynaklarının %75 ' inden fazla değil. %80 veya daha az% ' un ortalama alım kullanımı, bir küme için sürdürülebilir bir durumdur. Alım kullanımının en büyük değeri %100 ' dir, bu da tüm küme alma yeteneğinin kullanıldığı ve bir alım sırasının neden olabileceği anlamına gelir. |
    | Alım birimi (MB) | Sayı | Max, min, Sum | Sıkıştırmadan önce kümeye alınan verilerin toplam boyutu (MB cinsinden). |
    | Canlı tut | Sayı | Cin | Kümenin yanıt hızını izler. Tam yanıt veren bir küme 1 değerini döndürür ve Engellenen veya bağlantısı kesik bir küme 0 döndürür. |
    | Sorgu süresi | Saniye | Count, AVG, min, Max, Sum | Sorgu sonuçları alınana kadar geçen toplam süre (ağ gecikmesi dahil değil). |
    | | | |

    [Desteklenen Azure Veri Gezgini kümesi ölçümleriyle](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters) ilgili ek bilgiler

2. Aynı grafikte birden çok ölçümü çizmek için **ölçüm Ekle** düğmesini seçin.
3. Birden çok grafiği tek bir görünümde görmek için **+ yeni grafik** düğmesini seçin.
4. Zaman aralığını değiştirmek için saat seçiciyi kullanın (varsayılan: son 24 saat).
5. Boyutları olan ölçümler için [ **Filtre Ekle** ve **bölmeyi Uygula** '](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) yı kullanın.
6. Grafik yapılandırmanızı Pano 'ya eklemek için **panoya sabitle** ' yi seçerek yeniden görüntüleyebilirsiniz.
7. Küme ölçütlerini kullanarak ölçümlerinizi görselleştirmek için **Yeni bir uyarı kuralı** ayarlayın. Yeni uyarı kuralı, grafiğinizdeki hedef kaynağı, ölçümü, bölmeyi ve filtre boyutlarını içerir. [Uyarı kuralı oluşturma bölmesinde](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)bu ayarları değiştirin.

[Ölçüm Gezgini](/azure/azure-monitor/platform/metrics-getting-started)kullanımı hakkında ek bilgi.


## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: Azure Veri Gezgini veri alma ve sorgu izleme](/azure/data-explorer/ingest-data-no-code)
* [Tanılama günlüklerini kullanarak Azure Veri Gezgini alma işlemlerini izleme](/azure/data-explorer/using-diagnostic-logs)
* [Hızlı başlangıç: Azure Veri Gezgini'ndeki verileri sorgulama](web-query-data.md)
