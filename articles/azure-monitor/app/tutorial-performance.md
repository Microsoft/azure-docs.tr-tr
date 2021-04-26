---
title: Azure Application Insights kullanarak performans sorunlarını tanılama | Microsoft Docs
description: Azure Application Insights kullanarak uygulamanızdaki performans sorunlarını bulma ve tanılama hakkındaki öğretici.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: 36a581cb958639f4e9d88a8828b341ef1a1919d7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383227"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Azure Application Insights ile performans sorunlarını bulma ve tanılama

Azure Application Insights, uygulamanızdan çalışma ve performans analizine yardımcı olan telemetri verileri toplar.  Bu bilgileri kullanarak gerçekleşmekte olan sorunları belirleyebilir veya uygulamanın geliştirilmesi durumunda kullanıcıları en çok etkileyecek özelliklerini belirleyebilirsiniz.  Bu öğreticide hem uygulamanızın sunucu bileşenlerinin performansını hem de istemcinin bakış açısını analiz etme süreci adım adım gösterilir.  Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Sunucu tarafı işlemlerin performansını belirleme
> * Düşük performansın kök nedenini belirlemek için sunucu işlemlerini analiz etme
> * En yavaş istemci tarafı işlemleri belirleme
> * Sorgu dilini kullanarak sayfa görüntülemelerinin ayrıntılarını analiz etme


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- Aşağıdaki iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) ' i yükledikten sonra:
    - ASP.NET ve web geliştirme
    - Azure geliştirme
- Azure’a .NET uygulaması dağıtma ve [Application Insights SDK’sını etkinleştirme](../app/asp-net.md).
- Uygulamanız için [Application Insights profil oluşturucuyu etkinleştirme](../app/profiler.md#installation).

## <a name="log-in-to-azure"></a>Azure'da oturum açma
Üzerinde Azure portal oturum açın [https://portal.azure.com](https://portal.azure.com) .

## <a name="identify-slow-server-operations"></a>Yavaş sunucu işlemlerini belirleme
Application Insights, uygulamanızdaki farklı işlemlerin performans ayrıntılarını toplar. Süresi en uzun olan işlemleri belirleyerek olası sorunları veya sürmekte olan geliştirme sürecinizin en iyi hedefini tanılayabilir ve uygulamanın genel performansını geliştirebilirsiniz.

1. **Application Insights**’ı ve sonra aboneliğinizi seçin.  
1. **Performans** panelini açmak için **Araştır** menüsü altından **Performans**’ı seçin veya **Sunucu Yanıt Süresi** grafiğine tıklayın.

    ![Performans](media/tutorial-performance/1-overview.png)

2. **Performans** panelinde uygulamaya ait her işlemin sayısı ve ortalama süresi gösterilir.  Bu bilgileri kullanarak kullanıcıları en çok etkileyen işlemleri bulabilirsiniz. Bu örnekte, olası araştırma adayları süresi ve çağrı sayısı nispeten yüksek olan **GET Customers/Details** ve **GET Home/Index** işlemleridir.  Süresi yüksek olsa da nadiren çağrıldığı için geliştirilmesinin çok etkili olmayacağı başka işlemler olabilir.  

    ![Performans sunucusu paneli](media/tutorial-performance/2-server-operations.png)

3. Grafikte şu anda, seçilen çağrıların zaman içindeki ortalama süresi gösterilmektedir. Performans sorunlarını bulmak için 95. yüzdebirliğe geçiş yapabilirsiniz. İlgilendiğiniz işlemleri grafiğe sabitleyerek ekleyin.  Burada, araştırmaya değer bazı tepe noktaları olduğu görülmektedir.  Grafiğin zaman aralığını kısaltarak sorunu daha iyi yalıtın.

    ![İşlemleri sabitleme](media/tutorial-performance/3-server-operations-95th.png)

4.  Sağ taraftaki performans panelinde, seçilen işleme ilişkin farklı istek sürelerinin dağıtımı gösterilmektedir.  95. yüzdebirlikten başlamak için zaman aralığını kısaltın. "İlk 3 bağımlılık" içgörü kartı, dış bağımlılıkların yavaş işlemler üzerinde etkili olma olasılığını bir bakışta analiz edebilir.  Örnek listesini görmek için, örnek sayısının yer aldığı düğmeye tıklayın. Ardından, işlem ayrıntılarını görmek için herhangi bir örneği seçin.

5.  Toplam işlem süresini en çok Fabrikamaccount başlıklı Azure Tablosuna yapılan çağrının etkilediğini bir bakışta görebilirsiniz. Ayrıca, işlemin başarısız olmasına neden olan bir özel durum oluştuğunu da görebilirsiniz. Sağ tarafta ayrıntılarını görmek istediğiniz herhangi bir öğeye tıklayabilirsiniz. [İşlem tanılama deneyimi hakkında daha fazla bilgi edinin](../app/transaction-diagnostics.md)

    ![İşlem uçtan uca ayrıntıları](media/tutorial-performance/4-end-to-end.png)
    

6.  [**Profil Oluşturucu**](../app/profiler-overview.md) , işlem için çalıştırılan gerçek kodu ve her adım için gereken süreyi göstererek kod düzeyi tanılamalarla daha fazla yararlanmanıza yardımcı olur. Profil oluşturucu belirli aralıklarla çalıştığından, bazı işlemlerin izlemesi olmayabilir.  Zamanla daha fazla işlemin izlemesi olmalıdır.  İşlem için profil oluşturucuyu başlatmak için **Profiler izlemeleri**’ne tıklayın.
5.  İzlemede her işleme yönelik olaylar tek tek gösterildiğinden, genel işlem süresinin kök nedenini tanılayabilirsiniz.  Üstteki en uzun süreye sahip örneklerden birine tıklayın.
6.  İşlemin toplam süresine en çok katkıda bulunan olayların belirli yolunu vurgulamak için **etkin yol** ' e tıklayın.  Bu örnekte, en yavaş çağrının *FabrikamFiberAzureStorage.GetStorageTableData* metodundan geldiğini görebilirsiniz. En çok zaman alan bölüm *CloudTable.CreateIfNotExist* metodudur. İşlev her çağrıldığında bu kod satırı yürütülürse gereksiz ağ çağrısı ve CPU kaynağı tüketilir. Kodunuzu düzeltmenin en iyi yolu, bu satırı yalnızca bir kere yürütülen bir başlangıç yöntemine eklemektir.

    ![Profil oluşturucu ayrıntıları](media/tutorial-performance/5-hot-path.png)

7.  Ekranın üstündeki **Performans İpucu**, sürenin uzun olmasının beklemekten kaynaklandığı değerlendirmesini destekliyor.  Farklı olay türlerinin yorumlanmasına yönelik belgeler için **bekliyor** bağlantısına tıklayın.

    ![Performans ipucu](media/tutorial-performance/6-perf-tip.png)

8.   Daha fazla analiz için, izlemeyi indirmek üzere **Izlemeyi indir** ' e tıklayabilirsiniz. [PerfView](https://github.com/Microsoft/perfview#perfview-overview)kullanarak bu verileri görüntüleyebilirsiniz.

## <a name="use-logs-data-for-server"></a>Sunucu için günlük verilerini kullan
 Günlükler, Application Insights tarafından toplanan tüm verileri analiz etmenizi sağlayan zengin bir sorgu dili sağlar. Bunu kullanarak istek ve performans verileri üzerinde ayrıntılı analiz gerçekleştirebilirsiniz.

1. İşlem ayrıntısı paneline dönün ve günlüklerde ![ Günlükler simgesi görünümü ' ne tıklayın](media/tutorial-performance/app-viewinlogs-icon.png)**(Analiz)**

2. Günlükler, paneldeki her bir görünüm için bir sorgu ile açılır.  Bu sorguları olduğu gibi çalıştırabilir veya gereksinimlerinize göre özelleştirebilirsiniz.  İlk sorgu, zaman içinde bu işlemin süresini gösterir.

    ![günlük sorgusu](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Yavaş istemci işlemlerini belirleme
Application Insights, iyileştirilecek sunucu işlemlerini belirlemeye ek olarak istemci tarayıcılarının bakış açısını da analiz edebilir.  Bu, istemci bileşenlerine yönelik olası geliştirme fırsatlarını, hatta farklı tarayıcı veya konumlarla ilgili sorunları belirlemenize yardımcı olabilir.

1. **Araştır** **' ın altında** **tarayıcı** ' yı **seçin ve** **tarayıcı performansı özetini** açmak için sağ üst köşedeki sunucu/tarayıcı geçişi düğmesine tıklayarak **tarayıcı sekmesine geçin** . Burada, tarayıcının bakış açısından uygulamanızın çeşitli telemetri verilerinin görsel bir özetini sağlar.

    ![Tarayıcı özeti](media/tutorial-performance/8-browser.png)

2. İşlem adlarından birini seçin ve ardından sağ alt köşedeki mavi örnekler düğmesine tıklayın ve bir işlem seçin. Bu, uçtan uca işlem ayrıntılarını getirecek ve sağ taraftaki **sayfa görünümü özelliklerini** görüntüleyemeyecektir. Bu, tarayıcı türü ve konumu dahil olmak üzere sayfayı isteyen istemcinin ayrıntılarını görüntülemenizi sağlar. Bu bilgiler, belirli istemci türleriyle ilgili performans sorunları olup olmadığını belirlemenize yardımcı olabilir.

    ![Sayfa görüntüleme](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>İstemci için günlük verilerini kullan
Sunucu performansı için toplanan veriler gibi Application Insights, tüm istemci verilerinin Günlükler kullanılarak ayrıntılı analiz için kullanılabilir olmasını sağlar.

1. Tarayıcı özetine geri dönün ve Günlükler ![ simgesi ](media/tutorial-performance/app-viewinlogs-icon.png) **Görünüm günlüklerde (Analiz)** tıklayın

2. Günlükler, paneldeki her bir görünüm için bir sorgu ile açılır. İlk sorgu, zaman içinde farklı sayfa görüntüleme işlemlerinin süresini gösterir.

    ![Günlük sorgusu](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Sonraki adımlar
Artık çalışma zamanı özel durumlarının nasıl belirleneceğini öğrendiğinize göre, hatalara yanıt olarak uyarı oluşturmayı öğrenmek için bir sonraki öğreticiye ilerleyebilirsiniz.

> [!div class="nextstepaction"]
> [Uygulama durumuyla ilgili uyarılar gönderme](./tutorial-alert.md)

