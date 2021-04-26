---
title: Akış olaylarını yakala-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Azure Event Hubs aracılığıyla olayları akışını yakalamanızı sağlayan yakalama özelliğine bir genel bakış sunulmaktadır.
ms.topic: article
ms.date: 02/16/2021
ms.openlocfilehash: 9f0ec1223c06b908a9aa9f3ac5c5b19ead2fe962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595954"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Azure Blob depolama veya Azure Data Lake Storage Azure Event Hubs aracılığıyla olayları yakalama
Azure Event Hubs, bir [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/) alanında Event Hubs akış verilerini otomatik olarak yakalamanızı veya tercih ettiğiniz bir zaman veya boyut aralığı belirtme esnekliğine sahip olan [Azure Data Lake Storage Gen 1 veya Gen 2](https://azure.microsoft.com/services/data-lake-store/) hesabı. Yakalama ayarı hızlıdır, çalıştırmak için yönetim maliyeti yoktur ve Event Hubs [üretilen iş birimleriyle](event-hubs-scalability.md#throughput-units)otomatik olarak ölçeklendirilir. Event Hubs yakalama, akış verilerini Azure 'a yüklemenin en kolay yoludur ve veri yakalama yerine veri işlemeye odaklanmanızı sağlar.

> [!NOTE]
> Event Hubs yakalamanın Azure Data Lake Storage **Gen 2** kullanacak şekilde yapılandırılması, Azure Blob depolamayı kullanmak için yapılandırma ile aynıdır. Ayrıntılar için bkz. [Event Hubs yakalamayı yapılandırma](event-hubs-capture-enable-through-portal.md). 

Event Hubs yakalama, aynı akışta gerçek zamanlı ve toplu işlem tabanlı işlem hatlarını işlemenizi sağlar. Bu, zaman içinde gereksinimlerinize göre büyüyerek çözüm oluşturabileceğiniz anlamına gelir. Günümüzde, gelecekteki gerçek zamanlı işleme doğru bir gözle toplu iş tabanlı sistemler oluşturuyor veya var olan gerçek zamanlı bir çözüme verimli bir soğuk yol eklemek istiyorsanız, Event Hubs yakalama, akış verileriyle çalışmayı kolaylaştırır.

> [!IMPORTANT]
> Hedef depolama (Azure depolama veya Azure Data Lake Storage) hesabı, Olay Hub 'ı ile aynı abonelikte olmalıdır. 

## <a name="how-event-hubs-capture-works"></a>Event Hubs yakalama nasıl çalıştığını

Event Hubs, bir dağıtılmış günlüğe benzer bir telemetri girişi için zaman bekletme dayanıklı bir arabellek. Event Hubs ölçeklendirmeye yönelik anahtar [bölümlenmiş tüketici modelidir](event-hubs-scalability.md#partitions). Her bölüm bağımsız bir veri segmentine ve bağımsız olarak tüketilebilir. Bu verilerin zaman içinde, yapılandırılabilir saklama dönemine göre kapalı olması. Sonuç olarak, belirli bir olay hub 'ı hiçbir şekilde "çok dolu" olmaz.

Event Hubs yakalama, kendi Azure Blob depolama hesabınızı ve kapsayıcınızı veya yakalanan verileri depolamak için kullanılan Azure Data Lake Storage hesabını belirtmenize olanak sağlar. Bu hesaplar, Event Hubs yakalama özelliğinin esnekliğini ekleyerek, Olay Hub 'ınız veya başka bir bölgedeki aynı bölgede olabilir.

Yakalanan veriler [Apache avro][Apache Avro] biçiminde yazılır: satır içi şema ile zengin veri yapıları sağlayan kompakt, hızlı, ikili bir biçimdir. Bu biçim, Hadoop ekosisteminde, Stream Analytics ve Azure Data Factory yaygın olarak kullanılır. Bu makalenin ilerleyen kısımlarında avro ile çalışma hakkında daha fazla bilgi bulabilirsiniz.

### <a name="capture-windowing"></a>Pencereleme yakala

Event Hubs yakalama, yakalamayı denetlemek için bir pencere ayarlamanıza olanak sağlar. Bu pencere, "ilk WINS ilkesi" olan en düşük boyut ve zaman yapılandırması, karşılaşılan ilk tetikleyicinin bir yakalama işlemine neden olduğu anlamına gelir. On beş dakikalık, 100 MB yakalama pencereniz varsa ve saniye başına 1 MB gönderirseniz, boyut penceresi zaman penceresinden önce tetiklenir. Her bölüm bağımsız olarak yakalar ve yakalama sırasında bir tamamlanmış Blok Blobu yazar, yakalama aralığının karşılaştığı zaman olarak adlandırılır. Depolama adlandırma kuralı aşağıdaki gibidir:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Tarih değerleri sıfır ile doldurulur; örnek dosya adı şu olabilir:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Azure Storage blob 'larınızın geçici olarak kullanılamadığı bir durumda, Event Hubs yakalama, Olay Hub 'ınız üzerinde yapılandırılan veri saklama süresi için verilerinizi korur ve depolama hesabınız yeniden kullanılabilir olduğunda verileri dolduracaktır.

### <a name="scaling-to-throughput-units"></a>Üretilen iş birimlerine ölçekleme

Event Hubs trafik, [işleme birimleri](event-hubs-scalability.md#throughput-units)tarafından denetlenir. Tek bir üretilen iş birimi saniyede 1 MB veya saniye başına 1000 olay ve bu çıkış miktarı kadar iki kez izin verir. Standart Event Hubs 1-20 üretilen iş birimiyle yapılandırılabilir ve bir kota artışı [destek isteğiyle][support request]daha fazla satın alabilirsiniz. Satın alınan üretilen iş birimlerinizin ötesinde kullanım kısıtlanıyor. Event Hubs, verileri doğrudan dahili Event Hubs depolamadan, üretilen iş birimi çıkış kotalarını atlayarak ve Stream Analytics ya da Spark gibi diğer işleme okuyucular için çıkış bilgilerinizi kaydederek yakalar.

Yapılandırıldıktan sonra, ilk olaylarınızı gönderdiğinizde Event Hubs yakalama otomatik olarak çalışır ve çalışmaya devam eder. Aşağı akış işlemenin işlemin çalıştığını bilmesini kolaylaştırmak için, Event Hubs veri olmadığında boş dosyalar yazar. Bu işlem, toplu işlemcinizi besleyebilecek öngörülebilir bir temposunda ve işaret sağlar.

## <a name="setting-up-event-hubs-capture"></a>Event Hubs yakalama ayarlanıyor

[Azure Portal](https://portal.azure.com)kullanarak veya Azure Resource Manager şablonlarını kullanarak Olay Hub 'ı oluşturma sırasında yakalamayı yapılandırabilirsiniz. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure portalını kullanarak Event Hubs Yakalama özelliğini etkinleştirme](event-hubs-capture-enable-through-portal.md)
- [Bir olay hub'ı ile bir Event Hubs ad alanı oluşturma ve Azure Resource Manager şablonu kullanarak Yakalamayı etkinleştirme](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

> [!NOTE]
> Mevcut bir olay hub 'ı için yakalama özelliğini etkinleştirirseniz özellik, özellik **açıldıktan sonra** Olay Hub 'ına gelen olayları yakalar. Özellik açılmadan önce olay hub 'ında var olan olayları yakalamaz. 

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Yakalanan dosyaları keşfetme ve avro ile çalışma

Event Hubs yakalama, yapılandırılan zaman penceresinde belirtilen şekilde dosyaları avro biçiminde oluşturur. Bu dosyaları, [Azure Depolama Gezgini][Azure Storage Explorer]gibi herhangi bir araçta görüntüleyebilirsiniz. Dosyaları üzerinde çalışmak için yerel olarak indirebilirsiniz.

Event Hubs yakalama tarafından üretilen dosyalar aşağıdaki avro şemasına sahiptir:

![Avro şeması][3]

Avro dosyalarını keşfetmeye yönelik kolay bir yol, Apache 'den [avro araçları][Avro Tools] jar 'i kullanmaktır. Ayrıca, basit bir SQL odaklı deneyim için [Apache detaya gitme][Apache Drill] veya [Apache Spark][Apache Spark] , alınan verilerde karmaşık dağıtılmış işleme gerçekleştirmek için de kullanabilirsiniz. 

### <a name="use-apache-drill"></a>Apache detaya gitme kullan

[Apache detaya gitme][Apache Drill] , yapılandırılmış ve yarı yapılandırılmış verileri nerede olursa olsun Sorgulayabileceğiniz "büyük veri araştırması için açık KAYNAKLı bir SQL sorgu altyapısıdır". Motor tek başına bir düğüm olarak veya harika bir performans için çok büyük bir küme olarak çalışabilir.

Azure Blob depolama 'ya yerel bir destek sağlanır. Bu, belgelerde açıklandığı şekilde, verileri bir avro dosyasında sorgulamayı kolaylaştırır.

[Apache detaylandırma: Azure Blob depolama eklentisi][Apache Drill: Azure Blob Storage Plugin]

Yakalanan dosyaları kolayca sorgulamak için Azure Blob depolamaya erişmek üzere bir kapsayıcı aracılığıyla Apache detaya sahip bir VM oluşturup çalıştırabilirsiniz. Aşağıdaki örneğe bakın: [Event Hubs yakalama Ile ölçeklendirmeye yönelik akış](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-capture).

### <a name="use-apache-spark"></a>Apache Spark kullan

[Apache Spark][Apache Spark] , büyük ölçekli veri işleme için birleştirilmiş bir analiz altyapısıdır. " SQL dahil farklı dilleri destekler ve Azure Blob depolamaya kolayca erişebilir. Azure 'da Apache Spark çalıştırmak için birkaç seçenek vardır ve her biri Azure Blob depolamaya kolay erişim sağlar:

- [HDInsight: Azure Storage 'daki adres dosyaları][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob depolama][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Avro araçlarını kullanma

[Avro araçları][Avro Tools] jar paketi olarak kullanılabilir. Jar dosyasını indirdikten sonra, aşağıdaki komutu çalıştırarak belirli bir avro dosyasının şemasını görebilirsiniz:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Bu komut şunu döndürür

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Ayrıca, avro araçlarını kullanarak dosyayı JSON biçimine dönüştürebilir ve başka işlemler yapabilirsiniz.

Daha gelişmiş işleme gerçekleştirmek için, platform seçiminiz için avro indirin ve yükleyin. Bu yazma sırasında, C, C++, C \# , Java, NodeJS, Perl, php, Python ve Ruby için kullanılabilir uygulamalar vardır.

Apache avro, [Java][Java] ve [Python][Python]için Başlarken kılavuzlarını içerir. [Event Hubs yakalama ile çalışmaya](event-hubs-capture-python.md) başlama makalesini de okuyabilirsiniz.

## <a name="how-event-hubs-capture-is-charged"></a>Event Hubs yakalama nasıl ücretlendirilir

Event Hubs yakalama, üretilen iş birimlerine benzer şekilde ölçülür: saatlik ücret. Bu ücret, ad alanı için satın alınan üretilen iş birimi sayısıyla doğrudan orantılıdır. Üretilen iş birimleri arttırılarak ve azaldıkça, eşleşen performans sağlamak için ölçüm artışı artar ve azalır Event Hubs. Ölçümler art arda oluşur. Fiyatlandırma ayrıntıları için bkz. [Event Hubs fiyatlandırması](https://azure.microsoft.com/pricing/details/event-hubs/). 

Yakalama, ayrı olarak faturalandırılırken çıkış kotasını tüketmez. 

## <a name="integration-with-event-grid"></a>Event Grid ile tümleştirme 

Kaynağı olarak bir Event Hubs ad alanı ile Azure Event Grid aboneliği oluşturabilirsiniz. Aşağıdaki öğreticide, havuz olarak bir olay hub 'ı kaynak ve Azure Işlevleri uygulaması ile bir Event Grid aboneliği oluşturma: [Event Grid ve Azure işlevleri 'ni kullanarak bir Azure SYNAPSE analizinin işlenmesi ve Event Hubs geçirilmesi](store-captured-data-data-warehouse.md)gösterilmektedir.

## <a name="next-steps"></a>Sonraki adımlar
Event Hubs yakalama, verileri Azure 'a almanın en kolay yoludur. Azure Data Lake, Azure Data Factory ve Azure HDInsight 'ı kullanarak, ihtiyacınız olan herhangi bir ölçekte dilediğiniz ölçekte, seçtiğiniz tanıdık araç ve platformları kullanarak toplu işlem ve diğer çözümlemeler yapabilirsiniz.

Azure portal ve Azure Resource Manager şablonunu kullanarak bu özelliği etkinleştirmeyi öğrenin:

- [Event Hubs Capture için Azure portalı kullanma](event-hubs-capture-enable-through-portal.md)
- [Event Hubs yakalamayı etkinleştirmek için Azure Resource Manager şablonu kullanma](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming at Scale: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
