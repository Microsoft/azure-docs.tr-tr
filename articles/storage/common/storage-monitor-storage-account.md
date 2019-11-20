---
title: Azure portal bir Azure Depolama hesabını izleme | Microsoft Docs
description: Azure portal kullanarak Azure 'da bir depolama hesabını izlemeyi öğrenin.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 143574ff02960fcd0fd33ccaed5a80a9bb4f3147
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211862"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Azure portal bir depolama hesabını izleme

[Azure depolama Analizi](storage-analytics.md) , tüm depolama hizmetleri için ölçümler, blob 'lar, kuyruklar ve tablolar için Günlükler sağlar. Hesabınız için hangi ölçüm ve günlüklerin kaydedileceğini yapılandırmak ve ölçüm verilerinizin görsel sunumlarını sağlayan grafikleri yapılandırmak için [Azure Portal](https://portal.azure.com) kullanabilirsiniz. 

[Depolama Için Azure Izleyicisi 'ni](../../azure-monitor/insights/storage-insights-overview.md) (Önizleme) incelemenizi öneririz. Azure Storage hizmetlerinizin performansı, kapasitesi ve kullanılabilirliğinden oluşan Birleşik bir görünüm sunarak Azure depolama hesaplarınızın kapsamlı bir şekilde izlenmesini sağlayan bir Azure Izleyici özelliğidir. Her şeyi etkinleştirmenizi veya yapılandırmanızı gerektirmez ve bu ölçümleri önceden tanımlı etkileşimli grafiklerden ve diğer Görselleştirmelerde hemen görüntüleyebilirsiniz.

> [!NOTE]
> Azure portal izleme verilerini inceleme ile ilişkili maliyetler vardır. Daha fazla bilgi için bkz. [depolama Analizi](storage-analytics.md).
>
> Azure dosyaları şu anda Depolama Analizi ölçümleri destekler, ancak günlüğe kaydetmeyi henüz desteklememektedir.
>
> Azure depolama ile ilgili sorunları tanımlamak, tanılamak ve sorunlarını gidermek için Depolama Analizi ve diğer araçları kullanma hakkında ayrıntılı bir kılavuz için bkz. [izleme, tanılama ve sorun giderme Microsoft Azure depolama](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Depolama hesabı için izlemeyi yapılandırma

1. [Azure Portal](https://portal.azure.com), **depolama hesapları**' nı ve ardından depolama hesabı adını seçerek hesap panosunu açın.
1. Menü dikey penceresinin **izleme** bölümünde **Tanılama** ' yı seçin.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. İzlemek istediğiniz her bir **hizmet** için ölçüm verilerinin **türünü** ve verilerin **bekletme ilkesini** seçin. Ayrıca, **durumu** **kapalı**olarak ayarlayarak izlemeyi devre dışı bırakabilirsiniz.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Veri saklama ilkesini ayarlamak için, **bekletme (gün)** kaydırıcısını taşıyın veya tutulacak veri günü sayısını 1 ile 365 arasında girin. Yeni depolama hesapları için varsayılan değer yedi gündür. Bir bekletme ilkesi ayarlamak istemiyorsanız, sıfır girin. Bekletme ilkesi yoksa, izleme verilerini silmek size ait olur.

   > [!WARNING]
   > Ölçüm verilerini el ile sildiğinizde ücretlendirilirsiniz. Eski analiz verileri (bekletme ilkenizden daha eski veriler) sistem tarafından ücretsiz olarak silinir. Hesabınız için depolama analizi verilerini ne kadar süreyle saklamak istediğinize bağlı olarak bir bekletme ilkesi ayarlamayı öneririz. Daha fazla bilgi için bkz. [depolama ölçümlerinde faturalandırma](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

1. İzleme yapılandırmasını tamamladığınızda **Kaydet**' i seçin.

Varsayılan ölçüm kümesi, depolama hesabı dikey penceresindeki grafiklerde ve tek tek hizmet Blade (blob, kuyruk, tablo ve dosya) ile birlikte görüntülenir. Bir hizmet için ölçümleri etkinleştirdikten sonra, verilerin grafiklerde görünmesi bir saate kadar sürebilir. Grafikte hangi ölçümlerin görüntülendiğini yapılandırmak için herhangi bir ölçüm grafiğinde **Düzenle** seçeneğini belirleyebilirsiniz.

**Durumu** **kapalı**olarak ayarlayarak ölçüm toplamayı ve günlüğe kaydetmeyi devre dışı bırakabilirsiniz.

> [!NOTE]
> Azure depolama, depolama hesabınızın ölçümlerini depolamak için [Tablo Depolaması](storage-introduction.md#table-storage) kullanır ve ölçümleri hesabınızdaki tablolarda depolar. Daha fazla bilgi için bkz. [Ölçümler nasıl depolanır](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Ölçüm grafiklerini özelleştirme

Ölçüm grafiğinde görüntülenecek depolama ölçümlerini seçmek için aşağıdaki yordamı kullanın.

1. Azure portal bir depolama ölçümü grafiğini görüntüleyerek başlayın. **Depolama hesabı dikey** penceresinde ve tek bir hizmet için **ölçümler** dikey penceresinde (blob, kuyruk, tablo, dosya) grafikler bulabilirsiniz.

   Bu örnekte, **depolama hesabı dikey penceresinde**görüntülenen aşağıdaki grafiği kullanır:

   ![Azure portal grafik seçimi](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Grafiği düzenlemek için grafiğin içinde herhangi bir yere tıklayın.

1. Ardından, grafikte görüntülenecek ölçümlerin **zaman aralığını** ve ölçümlerini görüntülemek istediğiniz **hizmeti** (blob, kuyruk, tablo, dosya) seçin. Burada, önceki hafta ölçümleri blob hizmeti için görüntülenmek üzere seçilir:

   ![Grafik düzenleme dikey penceresinde zaman aralığı ve hizmet seçimi](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Grafikte görüntülenmesini istediğiniz bireysel **ölçümleri** seçin ve ardından **Tamam**' a tıklayın.

   ![Grafik düzenleme dikey penceresinde bireysel ölçüm seçimi](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Grafik ayarlarınız, depolama hesabındaki izleme verilerinin toplanmasını, toplanmasını veya depolanmasını etkilemez.

### <a name="metrics-availability-in-charts"></a>Grafiklerde ölçüm kullanılabilirliği

Kullanılabilir ölçümlerin listesi, açılan listede seçtiğiniz hizmete ve düzenlemekte olduğunuz grafiğin birim türüne göre değişir. Örneğin, yalnızca yüzde cinsinden birimleri görüntüleyen bir grafik düzenliyorsanız, *Percentnetworkerror* ve *Percentkısıtlar lingerror* gibi yüzde ölçümleri seçebilirsiniz:

![Azure portal hata yüzdesi grafiği iste](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Ölçüm çözümlemesi

**Tanılamada** seçtiğiniz ölçümler, hesabınız için kullanılabilir olan ölçümlerin çözümlenme durumunu belirler:

* **Toplama** izlemesi, giriş/çıkış, kullanılabilirlik, gecikme süresi ve başarı yüzdeleri gibi ölçümler sağlar. Bu ölçümler blob, tablo, dosya ve kuyruk hizmetlerinden toplanır.
* **API başına** , hizmet düzeyi toplamalarının yanı sıra ayrı depolama işlemleri için kullanılabilen ölçümler sayesinde daha fazla çözüm sağlar.

## <a name="configure-metrics-alerts"></a>Ölçüm uyarılarını yapılandırma

Depolama kaynağı ölçümleri için eşiklere ulaşıldığında sizi bilgilendirmek üzere uyarılar oluşturabilirsiniz.

1. **Uyarı kuralları dikey penceresini**açmak Için, **menü dikey** penceresinin **izleme** bölümüne gidin ve **Uyarılar (klasik)** öğesini seçin.
2. **Bir uyarı kuralı ekle** dikey penceresini açmak için **ölçüm uyarısı Ekle (klasik)** öğesini seçin
3. Yeni uyarı kuralınız için bir **ad** ve **Açıklama** girin.
4. Uyarı eklemek istediğiniz **ölçümü** , bir uyarı **koşulunu**ve **eşiği**seçin. Eşik birim türü, seçtiğiniz ölçüme bağlı olarak değişir. Örneğin, "Count" *Containercount*için birim türüdür, *percentnetworkerror* ölçüsünün birimi bir yüzde olur.
5. **Dönemi**seçin. Dönem içindeki eşiğe ulaşan veya aşan ölçümler bir uyarı tetikler.
6. Seçim **E-posta** ve **Web kancası** bildirimlerini yapılandırın. Web kancaları hakkında daha fazla bilgi için bkz. [Azure ölçüm uyarısında Web kancası yapılandırma](../../azure-monitor/platform/alerts-webhooks.md). E-posta veya Web kancası bildirimlerini yapılandırmazsanız, uyarılar yalnızca Azure portal görünür.

![Azure portal ' uyarı kuralı Ekle ' dikey penceresi](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Portal panosuna ölçüm grafikleri ekleme

Portal panonuza herhangi bir depolama hesabınız için Azure depolama ölçümleri grafikleri ekleyebilirsiniz.

1. Panonuzu [Azure Portal](https://portal.azure.com)görüntülerken **panoyu Düzenle** ' yi seçin.
1. **Kutucuk galerisinde**,**türe** >  **göre kutucukları bul**' u seçin.
1. **Tür** > **depolama hesapları**' nı seçin.
1. **Kaynaklar**' da, ölçümlerini panoya eklemek istediğiniz depolama hesabını seçin.
1. **Kategori** > **izlemeyi**seçin.
1. Görüntülenmesini istediğiniz ölçüm için grafik kutucuğunu panonuza sürükleyip bırakın. Panoda görüntülenmesini istediğiniz tüm ölçümler için tekrarlayın. Aşağıdaki görüntüde, "blob 'Lar-toplam istek" grafiği bir örnek olarak vurgulanır, ancak tüm grafikler panonuza yerleştirme için kullanılabilir.

   ![Azure portal kutucuk Galerisi](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Grafik ekleme işiniz bittiğinde panonun en üst kısmındaki **özelleştirmeyi bitti** ' yi seçin.

Panonuza grafik ekledikten sonra ölçüm grafiklerini özelleştirme bölümünde açıklandığı gibi daha fazla özelleştirme yapabilirsiniz.

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Blob, tablo ve kuyruk Hizmetleri için okuma, yazma ve silme istekleri için Azure Storage 'ın tanılama günlüklerini kaydetmesini söyleyebilirsiniz. Ayarladığınız veri saklama ilkesi bu Günlükler için de geçerlidir.

> [!NOTE]
> Azure dosyaları şu anda Depolama Analizi ölçümleri destekler, ancak günlüğe kaydetmeyi henüz desteklememektedir.
>

1. [Azure Portal](https://portal.azure.com)depolama **hesapları**' nı seçin ve depolama hesabı dikey penceresini açmak için depolama hesabı adı ' nı seçin.
1. Menü dikey penceresinin **izleme** bölümünde **Tanılama** ' yı seçin.

    ![Azure portal Izleme altındaki tanılama menü öğesi.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. **Durumun** **Açık**olarak ayarlandığından emin olun ve günlük kaydını etkinleştirmek istediğiniz **Hizmetleri** seçin.

    ![Azure portal günlüğe kaydetmeyi yapılandırın.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. **Kaydet**’e tıklayın.

Tanılama günlükleri, depolama hesabınızda *$logs* adlı bir blob kapsayıcısına kaydedilir. Günlük verilerini [Microsoft Depolama Gezgini](https://storageexplorer.com)gibi bir Depolama Gezgini ile veya program aracılığıyla depolama istemci kitaplığı veya PowerShell kullanarak görüntüleyebilirsiniz.

$Logs kapsayıcısına erişme hakkında daha fazla bilgi için bkz. [Storage Analytics günlüğü](storage-analytics-logging.md).

## <a name="next-steps"></a>Sonraki adımlar

* Depolama Analizi için [ölçümler, günlüğe kaydetme ve faturalandırma](storage-analytics.md) hakkında daha fazla ayrıntı bulun.
