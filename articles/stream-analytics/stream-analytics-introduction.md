---
title: Azure Stream Analytics’e Genel Bakış
description: Nesnelerin İnterneti'nden (IoT) sağlanan akış verilerini gerçek zamanlı olarak analiz etmenize yardım eden bir yönetilen hizmet olan Stream Analytics hakkında bilgi edinin.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 0982cc90d26c9f04e8d547c7d634e09280d7fca2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467904"
---
# <a name="what-is-azure-stream-analytics"></a>Azure Stream Analytics nedir?

Azure Stream Analytics, birden fazla kaynaktan aynı anda yüksek hacimli hızlı akış verilerini çözümlemek ve işlemek için tasarlanan gerçek zamanlı analiz ve karmaşık olay işleme altyapısıdır. Desenler ve ilişkiler cihazlar, sensörler, tıklama akışları, sosyal medya akışları ve uygulamalar dahil olmak üzere çeşitli giriş kaynaklarından ayıklanan bilgiler içinde tanımlanabilir. Bu desenler eylemleri tetiklemek ve uyarılar oluşturmak, bir raporlama aracına bilgi akışı yapmak veya dönüştürülen verileri daha sonra kullanmak üzere depolamak için kullanılabilir. Ayrıca, Stream Analytics Azure IoT Edge çalışma zamanında kullanılabilir ve bulut ile aynı dilin veya sözdiziminin aynısını destekler. 

Aşağıdaki senaryolar Azure Stream Analytics kullanabileceğiniz örnekler örneğidir:

* IoT cihazlarından gerçek zamanlı telemetri akışlarını çözümleme
* Web günlükleri/clickstream analizi
* Filo yönetimi ve sürücüsüz araçlar için jeo-uzamsal analiz
* Yüksek değerli varlıkların uzaktan izlenmesi ve tahmine dayalı Bakımı
* Envanter denetimi ve anomali algılama için Satış Noktasında gerçek zamanlı analizler

## <a name="how-does-stream-analytics-work"></a>Stream Analytics nasıl çalışır?

Azure Stream Analytics iş bir giriş, sorgu ve bir çıktıdan oluşur. Azure Event Hubs, Azure IoT Hub veya Azure Blob depolama 'dan verileri Stream Analytics. SQL sorgu diline dayalı sorgu, akış verilerini kolayca filtrelemek, sıralamak, toplamak ve bir süre boyunca birleştirmek için kullanılabilir. Bu SQL dilini JavaScript ve C# Kullanıcı tanımlı Işlevler (UDF 'ler) ile de genişletebilirsiniz. Basit dil yapıları ve/veya yapılandırmalarında toplama işlemleri yaparken, olay sıralama seçeneklerini ve zaman pencerelerinin süresini kolayca ayarlayabilirsiniz.

Her iş, dönüştürülen veriler için bir çıktıya sahiptir ve analiz ettiğiniz bilgilere yanıt olarak ne olduğunu denetleyebilirsiniz. Örneğin, şunları yapabilirsiniz:

* İletişimleri veya özel iş akışlarını aşağı yönde tetiklemek için Azure Işlevleri, Service Bus konuları veya kuyrukları gibi hizmetlere veri gönderin.
* Gerçek zamanlı bir taslak oluşturma için Power BI panoya veri gönderme.
* Bir makine öğrenimi modelini geçmiş verilere göre eğitme veya Batch Analytics 'i gerçekleştirmeye yönelik verileri diğer Azure Storage hizmetlerinde depolayın.

Aşağıdaki görüntüde, verilerin depolama veya sunum gibi diğer eylemler için Stream Analytics, analiz edildi ve gönderildi olarak nasıl gönderildiği gösterilmektedir:

![Stream Analytics giriş işlem hattı](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Temel işlevler ve avantajlar

Azure Stream Analytics kullanımı kolay, esnek, güvenilir ve her boyuttaki iş için ölçeklenebilir olacak şekilde tasarlanmıştır. Birden çok Azure bölgesinde mevcuttur. Aşağıdaki görüntüde Azure Stream Analytics temel özellikleri gösterilmektedir:

![Stream Analytics temel özellikleri](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Başlama kolaylığı

Azure Stream Analytics kolayca başlatılabilir. Birden çok kaynağa ve havuza bağlanmak için, uçtan uca bir işlem hattı oluşturarak yalnızca birkaç tıklama sürer. Stream Analytics, veri alımı için Azure [Event Hubs](/azure/event-hubs/) ve [Azure IoT Hub](/azure/iot-hub/) ve geçmiş verileri almak için [Azure Blob depolama alanı](/azure/storage/storage-introduction) 'na bağlanabilir. İş girişi, Azure Blob depolama alanından veya arama işlemlerini gerçekleştirmek için akış verilerine katılabilmeniz gereken [SQL veritabanından](stream-analytics-use-reference-data.md#azure-sql-database) statik veya yavaş değişen başvuru verilerini de içerebilir.

Stream Analytics, iş çıkışını [Azure Blob depolama](/azure/storage/storage-introduction), [Azure SQL veritabanı](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/)ve [Azure cosmosdb](/azure/cosmos-db/introduction)gibi birçok depolama sistemine yönlendirebilir. Azure HDInsight ile saklı çıktıda Batch Analytics 'i çalıştırabilir ya da çıktıyı, tüketim [Power BI](https://docs.microsoft.com/power-bi/) veya gerçek zamanlı görselleştirme için Event Hubs gibi başka bir hizmete gönderebilirsiniz.

Tüm Stream Analytics çıktıları listesi için bkz. [Azure Stream Analytics çıktılarını anlama](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programcı verimliliği

Azure Stream Analytics, hareket halindeki verileri çözümlemek için güçlü bir zamana bağlı kısıtlamalarla genişletilmiş basit bir SQL tabanlı sorgu dili kullanır. İş dönüşümlerini tanımlamak için, basit SQL yapıları kullanarak karmaşık geçici sorgular ve analizler yazmanıza olanak tanıyan basit, bildirim temelli bir [Stream Analytics sorgu dili](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) kullanırsınız. Stream Analytics sorgu dili SQL diline uygun olduğundan, SQL 'e alışkın olan işleri oluşturmaya başlamak yeterlidir. Azure PowerShell, [Visual Studio araçları Stream Analytics](stream-analytics-tools-for-visual-studio-install.md), [Stream Analytics Visual Studio Code uzantısı](quick-create-vs-code.md)veya Azure Resource Manager şablonları gibi geliştirici araçlarını kullanarak da işler oluşturabilirsiniz. Geliştirici araçlarının kullanılması, çevrimdışı ortamda dönüşüm sorguları geliştirmenize ve [CI/CD işlem hattı](stream-analytics-tools-for-visual-studio-cicd.md) kullanarak Azure’a iş göndermenize olanak tanır.

Stream Analytics sorgu dili, akış verilerini analiz etmek ve işlemek için geniş bir işlevler dizisi sunar. Bu sorgu dili basit veri işleme, toplama işlevleri ve karmaşık Jeo uzamsal işlevleri destekler. Portalda sorguları düzenleyebilir ve canlı bir akıştan ayıklanan örnek verileri kullanarak test edebilirsiniz.

Ek işlevler tanımlayıp çağırarak sorgu dilinin yapabileceklerini artırabilirsiniz. Azure Machine Learning çözümlerden faydalanmak için Azure Machine Learning işlev çağrıları tanımlayabilir ve bir akışın parçası olarak karmaşık hesaplamalar gerçekleştirmek için JavaScript veya C# Kullanıcı tanımlı Işlevleri (UDF 'ler) veya Kullanıcı tanımlı toplamaları tümleştirebilirsiniz Analiz sorgusu.

## <a name="fully-managed"></a>Tam olarak yönetilir

Azure Stream Analytics, Azure üzerinde tam olarak yönetilen bir sunucusuz (PaaS) tekliftir. İşlerinizi çalıştırmak için herhangi bir donanım sağlamanız veya kümeleri yönetmeniz gerekmez. Azure Stream Analytics, bulutta karmaşık işlem kümeleri ayarlayarak ve işi çalıştırmak için gereken performans ayarlamasından yararlanarak işinizi tam olarak yönetir. Azure Event Hubs ve Azure IoT Hub tümleştirmesi, işinizin bir dizi kaynaktan gelen saniye başına milyonlarca olayı almasına, bağlı cihazları, tıklama akışlarını ve günlük dosyalarını dahil etmesine olanak tanır. Event Hubs bölümlendirme özelliğini kullanarak, hesaplamaları mantıksal adımlara göre bölümleyebilir ve bunların her biri, ölçeklenebilirliği artırmak için daha fazla bölümlendirilme olanağı vardır.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Bulutta veya akıllı kenarda çalıştırın

Azure Stream Analytics bulutta çalışabilir, büyük ölçekli analizler için veya ultra düşük gecikmeli analizler için IoT Edge çalıştırabilirsiniz. Azure Stream Analytics hem bulutta hem de kenarda aynı sorgu dilini kullanarak geliştiricilerin akış işleme için gerçekten karma mimariler oluşturmalarına olanak tanır. 

## <a name="low-total-cost-of-ownership"></a>Düşük toplam sahip olma maliyeti

Bir bulut hizmeti olan Stream Analytics, maliyet için iyileştirilmiştir. Önde bir ön maliyet yoktur; yalnızca kullandığınız [akış birimleri](stream-analytics-streaming-unit-consumption.md)ve işlenen veri miktarı için ödeme yaparsınız. Taahhüt veya küme sağlama gerekmez ve iş gereksinimlerinize göre işi yukarı veya aşağı ölçeklendirebilirsiniz.

## <a name="mission-critical-ready"></a>Görev açısından kritik hazırlık

Azure Stream Analytics dünya çapındaki birden çok bölgede kullanılabilir ve güvenilirlik, güvenlik ve uyumluluk gereksinimlerini destekleyerek görev açısından kritik iş yüklerini çalıştırmak için tasarlanmıştır.

### <a name="reliability"></a>Güvenilirlik

Azure Stream Analytics, tek bir olay işleme ve olayların en az bir kez teslim edilmesini garanti eder, bu nedenle olaylar hiçbir zaman kaybedilmez. Tam bir kez işleme, [olay teslim garantisi](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)bölümünde açıklandığı gibi seçili çıkışlarla garanti edilir.

Azure Stream Analytics, bir olay tesliminin başarısız olması durumunda yerleşik kurtarma özellikleri vardır. Stream Analytics Ayrıca, işinizin durumunu korumak için yerleşik kontrol noktaları sağlar ve yinelenebilir sonuçlar sağlar.

Yönetilen bir hizmet olarak Stream Analytics, bir dakikalık ayrıntı düzeyi ile% 99,9 kullanılabilirlik ile olay işlemeyi güvence altına alır. Daha fazla bilgi için [Stream ANALYTICS SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) sayfasına bakın. 

### <a name="security"></a>Güvenlik

Güvenlik açısından, Azure Stream Analytics tüm gelen ve giden iletişimleri şifreler ve TLS 1,2 ' yi destekler. Yerleşik kontrol noktaları da şifrelenir. Tüm işlemler bellek içinde yapıldığından, Stream Analytics gelen verileri depolamaz.

### <a name="compliance"></a>Uyumluluk

Azure Stream Analytics, [Azure uyumluluğuna genel bakış](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)bölümünde açıklandığı gibi birden çok uyumluluk sertifikası izler. 

## <a name="performance"></a>Performans

Stream Analytics her saniye milyonlarca olayı işleyebilir ve çok fazla gecikme süreleriyle sonuç sunabilir. Büyük gerçek zamanlı ve karmaşık olay işleme uygulamalarını işlemek için ölçeği ölçeklendirmenize ve ölçeklendirmenize olanak tanır. Stream Analytics bölümleyerek daha yüksek performansı destekler, böylece karmaşık sorguların paralelleştirilmesine ve birden çok akış düğümünde yürütülmesine izin verir. Azure Stream Analytics, Microsoft Research ile işbirliği içinde geliştirilen yüksek performanslı, bellek içi akış analizi altyapısı, [popüler bir yapıda](https://github.com/Microsoft/Trill)oluşturulmuştur.

## <a name="next-steps"></a>Sonraki adımlar

Azure Stream Analytics’e genel bakışı gördünüz. Bundan sonra derinlere inerek ilk Stream Analytics işinizi oluşturabilirsiniz:

* [Azure portalını kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md).
* [Azure PowerShell kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-powershell.md).
* [Visual Studio 'yu kullanarak bir Stream Analytics Işi oluşturun](stream-analytics-quick-create-vs.md).
* [Visual Studio Code kullanarak Stream Analytics bir Iş oluşturun](quick-create-vs-code.md).
