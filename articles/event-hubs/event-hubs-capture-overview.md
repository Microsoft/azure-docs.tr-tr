---
title: Akış olaylar - Azure Event Hubs yakalama | Microsoft Docs
description: Bu makalede, Azure Event Hubs ile akış olaylarını yakalamak izin veren yakalama özelliğine genel bakış sağlar.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 17906a7d0953d8b320301d74cda81d14c9ad340f
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123434"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Azure Blob Depolama veya Azure Data Lake Storage Azure Event Hubs ile olayları yakalama
Azure olay hub'ları otomatik olarak Event Hubs akış verilerini yakalamanıza olanak sağlayan bir [Azure Blob Depolama](https://azure.microsoft.com/services/storage/blobs/) veya [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) eklenen esnekliğini tercih ettiğiniz Hesapla bir zaman veya boyut aralığı belirtme. Yakalama ayarı hızlı, çalıştırmak için hiçbir yönetim maliyeti yoktur ve Event Hubs ile otomatik olarak ölçeklenen [üretilen iş birimleri](event-hubs-scalability.md#throughput-units). Event Hubs yakalama, Azure'a akış verileri yüklemenin en kolay yoludur ve veri yakalama yerine veri işleme odaklanmasını sağlar.

Event Hubs yakalama, aynı akışta gerçek zamanlı ve toplu işlem tabanlı işlem hatlarını işlemenizi sağlar. Başka bir deyişle, gereksinimlerinizi zamanla ile büyütün çözümleri oluşturabilirsiniz. Event Hubs yakalama batch tabanlı sistemlerde göz önünde doğrultusunda gelecekteki gerçek zamanlı işleme ile bugün oluşturuyorsunuz ve verimli bir Durgun yol var olan bir gerçek zamanlı çözüme eklemek istediğiniz olsun, akış verileri daha kolay ile çalışma hale getirir.

## <a name="how-event-hubs-capture-works"></a>Event Hubs yakalama nasıl çalışır

Event Hubs, telemetri giriş, dağıtılmış bir günlük için benzer bir saklama süresi dayanıklı arabellek. Olay hub'ları, ölçeklendirme için anahtar [bölümlenmiş tüketici modelinin](event-hubs-scalability.md#partitions). Her bölüm veri bağımsız bir parçası olan ve bağımsız olarak kullanılır. Zaman içinde yapılandırılabilir bekletme aralığını temel kapalı, bu verileri eskir. Sonuç olarak, belirli bir olay hub'ı hiçbir zaman "dolu." alır

Event Hubs yakalama, kendi Azure Blob Depolama hesabı ve kapsayıcı veya yakalanan verileri depolamak için kullanılan Azure Data Lake Store hesabı belirtmenizi sağlar. Bu hesaplar, olay hub'ı ile aynı bölgede veya başka bir bölgede Event Hubs yakalama özelliğini esnekliğini ekleme olabilir.

Yakalanan veriler [Apache avro][Apache Avro] biçiminde yazılır: satır içi şema ile zengin veri yapıları sağlayan kompakt, hızlı, ikili bir biçimdir. Bu biçim, Hadoop ekosistemi, Stream Analytics ve Azure Data Factory yaygın olarak kullanılır. Avro ile çalışma hakkında daha fazla bilgi makalenin sonraki bölümlerinde kullanılabilir.

### <a name="capture-windowing"></a>Pencereleme yakalama

Event Hubs yakalama, yakalama denetlemek için bir penceresi ayarlama sağlar. Bu, en küçük boyut ve zaman yapılandırma İlkesi'yle "karşılaşılan ilk tetikleyici bir yakalama işlemi neden olduğunu, yani ilk WINS," penceredir. On beş dakikalık varsa, 100 MB Yakala ve 1 MB saniye başına zaman penceresi önce boyutu pencere Tetikleyicileri gönderin. Her bölüm bağımsız olarak yakalar ve tamamlanan blok blobu, yakalama aralığı karşılaşıldı süredir adlı yakalama, zamanında yazar. Depolama adlandırma kuralı aşağıdaki gibidir:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Tarih değerlerini sıfırlarla doldurulur dikkat edin. bir örnek dosya adı olabilir:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Azure Storage blob 'larınızın geçici olarak kullanılamadığı bir durumda, Event Hubs yakalama, Olay Hub 'ınız üzerinde yapılandırılan veri saklama süresi için verilerinizi korur ve depolama hesabınız yeniden kullanılabilir olduğunda verileri dolduracaktır.

### <a name="scaling-to-throughput-units"></a>İşleme birimleri ile ölçeklendirme

Olay hub'ları trafiği tarafından denetlenir [üretilen iş birimleri](event-hubs-scalability.md#throughput-units). Tek bir işleme birimi, ikinci veya 1000 olay giriş ve çıkış miktarı iki kez saniye başına 1 MB sağlar. Standart Event Hubs 1-20 üretilen iş birimiyle yapılandırılabilir ve bir kota artışı [destek isteğiyle][support request]daha fazla satın alabilirsiniz. Kullanım, satın alınan işleme birimlerinin ötesinde kısıtlanır. Event Hubs yakalama doğrudan İç olay hub'ları depolama alanındaki verileri, aktarım hızı birimi çıkış kotaları atlayarak ve Stream Analytics veya Spark gibi diğer işleme okuyucular için çıkış kaydetme kopyalar.

Event Hubs yakalama yapılandırıldıktan sonra ilk olay gönderdiğinizde, otomatik olarak çalışır ve çalışmaya devam eder. İşlemin çalıştığını bilmek, aşağı akış işleme kolaylaştırmak için hiçbir veri olduğunda olay hub'ları boş dosyalarını yazar. Bu işlem, tahmin edilebilir temposu ve, batch işlemci besleyebilecek işaret sunar.

## <a name="setting-up-event-hubs-capture"></a>Event Hubs yakalama özelliğini ayarlama

Olay hub'ı kullanarak, oluşturma sırasında yakalama özelliğini yapılandırabilirsiniz [Azure portalında](https://portal.azure.com), veya Azure Resource Manager şablonlarını kullanarak. Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure portalını kullanarak Event Hubs yakalama özelliğini etkinleştirme](event-hubs-capture-enable-through-portal.md)
- [Bir olay hub'ı ile bir Event Hubs ad alanı oluşturma ve bir Azure Resource Manager şablonu kullanarak yakalamayı etkinleştirme](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Yakalanan dosyaları keşfetme ve Avro ile çalışma

Event Hubs yakalama, yapılandırılmış zaman penceresi belirtildiği gibi Avro biçiminde dosyaları oluşturur. Bu dosyaları, [Azure Depolama Gezgini][Azure Storage Explorer]gibi herhangi bir araçta görüntüleyebilirsiniz. Yerel olarak çalışacak şekilde dosyalarını indirebilirsiniz.

Event Hubs yakalama tarafından üretilen dosyalar aşağıdaki Avro şemanın vardır:

![Avro şeması][3]

Avro dosyalarını keşfetmeye yönelik kolay bir yol, Apache 'den [avro araçları][Avro Tools] jar 'i kullanmaktır. Ayrıca, basit bir SQL odaklı deneyim için [Apache detaya gitme][Apache Drill] veya [Apache Spark][Apache Spark] , alınan verilerde karmaşık dağıtılmış işleme gerçekleştirmek için de kullanabilirsiniz. 

### <a name="use-apache-drill"></a>Apache detaya gitme kullan

[Apache detaya gitme][Apache Drill] , yapılandırılmış ve yarı yapılandırılmış verileri nerede olursa olsun Sorgulayabileceğiniz "büyük veri araştırması için açık KAYNAKLı bir SQL sorgu altyapısıdır". Motor tek başına bir düğüm olarak veya harika bir performans için çok büyük bir küme olarak çalışabilir.

Azure Blob depolama 'ya yerel bir destek sağlanır. Bu, belgelerde açıklandığı şekilde, verileri bir avro dosyasında sorgulamayı kolaylaştırır.

[Apache detaylandırma: Azure Blob depolama eklentisi][Apache Drill: Azure Blob Storage Plugin]

Yakalanan dosyaları kolayca sorgulamak için Azure Blob depolamaya erişmek üzere bir kapsayıcı aracılığıyla Apache detaya sahip bir VM oluşturup yürütebilirsiniz:

https://github.com/yorek/apache-drill-azure-blob

Eksiksiz bir uçtan uca örnek, akış üzerinde ölçek deposunda mevcuttur:

[Ölçekte akış: Event Hubs yakalama]

### <a name="use-apache-spark"></a>Apache Spark kullan

[Apache Spark][Apache Spark] , büyük ölçekli veri işleme için birleştirilmiş bir analiz altyapısıdır. " SQL dahil farklı dilleri destekler ve Azure Blob depolamaya kolayca erişebilir. Azure 'da Apache Spark çalıştırmak için iki seçenek vardır ve her ikisi de Azure Blob depolamaya kolay erişim sağlar:

- [HDInsight: Azure Storage 'daki adres dosyaları][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob depolama][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Avro araçlarını kullanma

[Avro araçları][Avro Tools] jar paketi olarak kullanılabilir. Jar dosyasını indirdikten sonra, aşağıdaki komutu çalıştırarak belirli bir avro dosyasının şemasını görebilirsiniz:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Bu komut döndürür

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

Avro araçları, dosya JSON biçimine dönüştürün ve başka bir işlem gerçekleştirmek için de kullanabilirsiniz.

Daha gelişmiş işleme gerçekleştirmek için indirin ve kendi seçtiğiniz platform için Avro yükleyin. Bu makalenin yazıldığı sırada, kullanılabilir uygulamalar için C, C++, C\#, Java, NodeJS, Perl, PHP, Python ve Ruby.

Apache avro, [Java][Java] ve [Python][Python]için Başlarken kılavuzlarını içerir. Ayrıca okuyabilirsiniz [Event Hubs yakalama ile çalışmaya başlama](event-hubs-capture-python.md) makalesi.

## <a name="how-event-hubs-capture-is-charged"></a>Event Hubs yakalama nasıl ücretlendirilir

Event Hubs yakalama ölçülen benzer şekilde üretilen iş birimleri: saatlik bir ücret olarak. Ücretlendirme, ad alanı için satın alınan işleme birimlerinin sayısı doğrudan orantılıdır. İşleme birimleri artan ve azalan olarak Event Hubs yakalama ölçümleri artırın ve eşleşen performans sağlayacak şekilde azaltın. Ölçümleri dağıtımınızla oluşur. Fiyatlandırma ayrıntıları için bkz: [Event Hubs fiyatlandırması](https://azure.microsoft.com/pricing/details/event-hubs/). 

Yakalama, ayrı olarak faturalandırılırken çıkış kotasının tüketilmediğini unutmayın. 

## <a name="integration-with-event-grid"></a>Event Grid ile tümleştirme 

Bir Event Hubs ad alanı ile kaynağı olarak Azure Event Grid aboneliği oluşturabilirsiniz. Aşağıdaki öğreticide kaynağı olarak bir olay hub'ı ve havuz olarak bir Azure işlev uygulaması ile bir Event Grid aboneliği oluşturmak gösterilmektedir: [işlem ve Event Grid ve Azure işlevlerikullanarakbirSQLveriambarıyakalananEventHubsverilerinigeçirme](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs yakalama, Azure'a veri almak için en kolay yoludur. Azure Data Lake, Azure Data Factory ve Azure HDInsight'ı kullanarak, toplu işlem gerçekleştirebilir ve dilediğiniz ölçekte alışık olduğunuz araçları ve seçtiğiniz platformları kullanarak, diğer analiz gerekir.

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Olayları gönderip kullanmaya başlayın](event-hubs-dotnet-framework-getstarted-send.md)
* [Event Hubs’a genel bakış][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.1.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Ölçekte akış: Event Hubs yakalama]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
