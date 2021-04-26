---
title: Azure Stream Analytics giriş
description: Nesnelerin İnterneti (IoT) ile gerçek zamanlı akış verilerini çözümlemenize yardımcı olan Azure Stream Analytics yönetilen bir hizmet hakkında bilgi edinin.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc, contperf-fy21q2
ms.date: 11/12/2020
ms.openlocfilehash: 5aea6460f3a876d63544ce8422f9f205c22f2a0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015258"
---
# <a name="welcome-to-azure-stream-analytics"></a>Azure Stream Analytics hoş geldiniz

Azure Stream Analytics, birden fazla kaynaktan aynı anda yüksek hacimli hızlı akış verilerini çözümlemek ve işlemek için tasarlanan gerçek zamanlı analiz ve karmaşık olay işleme altyapısıdır. Desenler ve ilişkiler cihazlar, sensörler, tıklama akışları, sosyal medya akışları ve uygulamalar dahil olmak üzere çeşitli giriş kaynaklarından ayıklanan bilgiler içinde tanımlanabilir. Bu desenler eylemleri tetiklemek ve uyarı oluşturma, bir raporlama aracına bilgi akışı veya dönüştürülen verileri daha sonra kullanmak üzere depolama gibi iş akışlarını başlatmak için kullanılabilir. Ayrıca, Stream Analytics Azure IoT Edge çalışma zamanında kullanılabilir ve IoT cihazlarındaki verilerin işlemesini etkinleştirir.

Aşağıdaki senaryolar Azure Stream Analytics kullanabileceğiniz örnekler örneğidir:

* IoT cihazlarından gerçek zamanlı telemetri akışlarını çözümleme
* Web günlükleri/clickstream analizi
* Filo yönetimi ve sürücüsüz araçlar için jeo-uzamsal analiz
* Yüksek değerli varlıkların uzaktan izlenmesi ve tahmine dayalı Bakımı
* Envanter denetimi ve anomali algılama için Satış Noktasında gerçek zamanlı analizler

Ücretsiz bir Azure aboneliğiyle Azure Stream Analytics deneyebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Stream Analytics deneyin](https://azure.microsoft.com/services/stream-analytics/)

## <a name="how-does-stream-analytics-work"></a>Stream Analytics nasıl çalışır?

Azure Stream Analytics iş bir giriş, sorgu ve bir çıktıdan oluşur. Azure Event Hubs (Apache Kafka 'den Azure Event Hubs dahil), Azure IoT Hub veya Azure Blob depolama 'dan verileri Stream Analytics. SQL sorgu diline dayalı sorgu, akış verilerini kolayca filtrelemek, sıralamak, toplamak ve bir süre boyunca birleştirmek için kullanılabilir. JavaScript ve C# Kullanıcı tanımlı işlevlerle (UDF 'ler) Bu SQL dilini de genişletebilirsiniz. Basit dil yapıları ve/veya yapılandırmalarında toplama işlemleri gerçekleştirirken olay sıralama seçeneklerini ve zaman pencerelerinin süresini kolayca ayarlayabilirsiniz.

Her işin dönüştürülmüş veriler için bir veya birkaç çıkışı vardır ve analiz ettiğiniz bilgilere yanıt olarak neler olduğunu denetleyebilirsiniz. Örneğin, şunları yapabilirsiniz:

* İletişimleri veya özel iş akışlarını aşağı yönde tetiklemek için Azure Işlevleri, Service Bus konuları veya kuyrukları gibi hizmetlere veri gönderin.
* Gerçek zamanlı bir taslak oluşturma için Power BI panoya veri gönderme.
* Diğer Azure depolama hizmetlerinde (örneğin, Azure Data Lake, Azure SYNAPSE Analytics vb.) verileri, geçmiş verilere göre bir makine öğrenimi modeli eğitmek veya Batch Analytics 'i gerçekleştirmek için depolayın.

Aşağıdaki görüntüde, verilerin depolama veya sunum gibi diğer eylemler için Stream Analytics, analiz edildi ve gönderildi olarak nasıl gönderildiği gösterilmektedir:

![Stream Analytics giriş işlem hattı](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Temel işlevler ve avantajlar

Azure Stream Analytics kullanımı kolay, esnek, güvenilir ve her boyuttaki iş için ölçeklenebilir olacak şekilde tasarlanmıştır. Birden fazla Azure bölgesinde mevcuttur ve IoT Edge veya Azure Stack üzerinde çalışır.

## <a name="ease-of-getting-started"></a>Başlama kolaylığı

Azure Stream Analytics kolayca başlatılabilir. Birden çok kaynağa ve havuza bağlanmak için, uçtan uca bir işlem hattı oluşturarak yalnızca birkaç tıklama sürer. Stream Analytics, veri alımı için Azure Event Hubs ve Azure IoT Hub ve geçmiş verileri almak için Azure Blob depolama alanı 'na bağlanabilir. İş girişi, Azure Blob depolama alanından veya arama işlemlerini gerçekleştirmek için akış verilerine katılabilmeniz gereken SQL veritabanından statik veya yavaş değişen başvuru verilerini de içerebilir.

Stream Analytics, iş çıkışını Azure Blob depolama, Azure SQL veritabanı, Azure Data Lake Store ve Azure CosmosDB gibi birçok depolama sistemine yönlendirebilir. Batch Analytics 'i Azure SYNAPSE Analytics veya HDInsight ile Stream çıktıları üzerinde de çalıştırabilir ya da çıktıyı, tüketim Power BI veya gerçek zamanlı görselleştirme için Event Hubs gibi başka bir hizmete gönderebilirsiniz.

Tüm Stream Analytics çıktıları listesi için bkz. [Azure Stream Analytics çıktılarını anlama](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programcı verimliliği

Azure Stream Analytics, hareket halindeki verileri çözümlemek için güçlü zamana bağlı kısıtlamalarla genişletilmiş bir SQL sorgu dili kullanır. Azure PowerShell, Azure CLı, Stream Analytics Visual Studio Araçları, [Stream Analytics Visual Studio Code uzantısı](quick-create-visual-studio-code.md)ya da Azure Resource Manager şablonları gibi geliştirici araçlarını kullanarak da işler oluşturabilirsiniz. Geliştirici araçlarının kullanımı, çevrimdışı dönüşüm sorguları geliştirmenize ve Azure 'a iş göndermek için CI/CD işlem hattını kullanmanıza olanak sağlar.

Stream Analytics sorgu dili, akış verilerini çözümlemek için geniş bir işlev dizisi sunarak CEP (karmaşık olay Işleme) gerçekleştirmesine olanak sağlar. Bu sorgu dili basit veri işleme, toplama ve analiz işlevlerini, Jeo uzamsal işlevleri, model eşleştirmeyi ve anomali algılamayı destekler. Portalda sorguları düzenleyebilir veya geliştirme araçlarımızı kullanarak bunları canlı bir akıştan ayıklanan örnek verileri kullanarak test edebilirsiniz.

Ek işlevler tanımlayıp çağırarak sorgu dilinin yapabileceklerini artırabilirsiniz. Azure Machine Learning çözümlerinin avantajlarından yararlanmak için Azure Machine Learning işlev çağrıları tanımlayabilir ve bir Stream Analytics sorgusunun parçası olarak karmaşık hesaplamalar gerçekleştirmek için JavaScript veya C# Kullanıcı tanımlı işlevleri (UDF 'ler) veya Kullanıcı tanımlı toplamaları tümleştirebilirsiniz.

## <a name="fully-managed"></a>Tam olarak yönetilir

Azure Stream Analytics, Azure üzerinde tam olarak yönetilen (PaaS) bir tekliftir. Herhangi bir donanım veya altyapı sağlamanız, işletim sistemi veya yazılım güncelleştirmeniz gerekmez. Azure Stream Analytics, işinizi tamamen yönetir, bu sayede altyapıya değil iş mantığınıza odaklanırsınız.


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Bulutta veya akıllı kenarda çalıştırın

Azure Stream Analytics bulutta çalışabilir, büyük ölçekli analizler için veya IoT Edge ya da ultra düşük gecikmeli analizler için Azure Stack çalıştırabilirsiniz. Azure Stream Analytics hem bulutta hem de kenarda aynı araçları ve sorgu dilini kullanır, geliştiricilerin Stream işleme için gerçekten karma mimariler oluşturmalarına olanak tanır. 

## <a name="low-total-cost-of-ownership"></a>Düşük toplam sahip olma maliyeti

Bir bulut hizmeti olan Stream Analytics, maliyet için iyileştirilmiştir. Dahil ön maliyet yoktur; yalnızca kullandığınız [akış birimleri](stream-analytics-streaming-unit-consumption.md)için ödeme yaparsınız. Taahhüt veya küme sağlama gerekmez ve iş gereksinimlerinize göre işi yukarı veya aşağı ölçeklendirebilirsiniz.

## <a name="mission-critical-ready"></a>Görev açısından kritik hazırlık

Azure Stream Analytics dünya çapındaki birden çok bölgede kullanılabilir ve güvenilirlik, güvenlik ve uyumluluk gereksinimlerini destekleyerek görev açısından kritik iş yüklerini çalıştırmak için tasarlanmıştır.

### <a name="reliability"></a>Güvenilirlik

Azure Stream Analytics olay işleme ve en az bir kez olayları teslim etmek için olaylar hiçbir zaman kaybedilmez. Olay teslim garantisi ' nda açıklandığı gibi, seçili çıkışlarla tam olarak bir kez işleme garanti edilir.

Azure Stream Analytics, bir olay tesliminin başarısız olması durumunda yerleşik kurtarma özellikleri vardır. Stream Analytics Ayrıca, işinizin durumunu korumak için yerleşik kontrol noktaları sağlar ve yinelenebilir sonuçlar sağlar.

Yönetilen bir hizmet olarak Stream Analytics, bir dakikalık ayrıntı düzeyi ile% 99,9 kullanılabilirlik ile olay işlemeyi güvence altına alır. 

### <a name="security"></a>Güvenlik

Güvenlik açısından, Azure Stream Analytics tüm gelen ve giden iletişimleri şifreler ve TLS 1,2 ' yi destekler. Yerleşik kontrol noktaları da şifrelenir. Tüm işlemler bellek içinde yapıldığından, Stream Analytics gelen verileri depolamaz. Stream Analytics Ayrıca, bir [Stream Analytics kümesinde](./cluster-overview.md)Iş çalıştırılırken Azure sanal AĞLARıNı (VNet) destekler.

### <a name="compliance"></a>Uyumluluk

Azure Stream Analytics, [Azure uyumluluğuna genel bakış](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)bölümünde açıklandığı gibi birden çok uyumluluk sertifikası izler. 

## <a name="performance"></a>Performans

Stream Analytics her saniye milyonlarca olayı işleyebilir ve çok fazla gecikme süreleriyle sonuç sunabilir. Büyük gerçek zamanlı ve karmaşık olay işleme uygulamalarını işlemek için ölçeği ölçeklendirmenize ve ölçeklendirmenize olanak tanır. Stream Analytics bölümleyerek daha yüksek performansı destekler, böylece karmaşık sorguların paralelleştirilmesine ve birden çok akış düğümünde yürütülmesine izin verir. Azure Stream Analytics, Microsoft Research ile işbirliği içinde geliştirilen yüksek performanslı, bellek içi akış analizi altyapısı, [popüler bir yapıda](https://github.com/Microsoft/Trill)oluşturulmuştur.

## <a name="next-steps"></a>Sonraki adımlar

Azure Stream Analytics’e genel bakışı gördünüz. Bundan sonra derinlere inerek ilk Stream Analytics işinizi oluşturabilirsiniz:

* [Azure portal kullanarak Stream Analytics işi oluşturun](stream-analytics-quick-create-portal.md)
* [Azure PowerShell kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-powershell.md)
* [Visual Studio 'Yu kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-vs.md)
* [Visual Studio Code kullanarak Stream Analytics işi oluşturma](quick-create-visual-studio-code.md)
