---
title: Azure Veri Gezgini veri alımı
description: Azure Veri Gezgini veri alma (yükleme) gibi farklı yollar hakkında bilgi edinin
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 90387a033a43c627be4ce69a93ee37c5b959732d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091794"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure Veri Gezgini veri alımı

Veri alımı, bir veya daha fazla kaynaktan veri kayıtlarını yüklemek için kullanılan işlemdir ve Azure Veri Gezgini bir tablo oluşturmak veya güncelleştirmek için kullanılır. Giriş yapıldıktan sonra veriler sorgu için kullanılabilir hale gelir. Aşağıdaki diyagramda, veri alımı dahil olmak üzere Azure Veri Gezgini ile çalışmaya yönelik uçtan uca akış gösterilmektedir.

![Veri akışı](media/ingest-data-overview/data-flow.png)

Veri alma işleminden sorumlu Azure Veri Gezgini veri yönetimi hizmeti aşağıdaki işlevleri sağlar:

1. **Veri çekme**: bir Azure kuyruğundan dış kaynaklardan (Event Hubs) veya alma isteği isteklerinden veri çekme.

1. Toplu **işleme**: giriş aktarım hızını iyileştirmek için aynı veritabanına ve tabloya akan veri akışı.

1. **Doğrulama**: gerekirse, ön doğrulama ve biçim dönüştürme.

1. **Veri işleme**: şemayı eşleme, düzenleme, dizin oluşturma, kodlama ve verileri sıkıştırma.

1. Alma **akışındaki kalıcılık noktası**: altyapıdaki alma yükünü yönetin ve geçici hatalardan sonra yeniden denemeleri işleyin.

1. **Veri**alma: verileri sorgu için kullanılabilir hale getirir.

## <a name="ingestion-methods"></a>Alma yöntemleri

Azure Veri Gezgini, her biri kendi hedef senaryoları, avantajları ve dezavantajları olan birkaç alma yöntemini destekler. Azure Veri Gezgini, ortak hizmetlere yönelik işlem hatları ve bağlayıcılar, SDK 'Ları kullanarak program aracılığıyla alma ve araştırma amacıyla altyapıya doğrudan erişim sağlar.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>İşlem hatları, bağlayıcılar ve eklentileri kullanma

Azure Veri Gezgini Şu anda şunları destekler:

* Azure portal Yönetim Sihirbazı kullanılarak yönetilebilecek Event Grid işlem hattı. Daha fazla bilgi için bkz. Azure [Bloblarını azure Veri Gezgini 'a](ingest-data-event-grid.md)alma.

* Azure portal Yönetim Sihirbazı kullanılarak yönetilebilecek Olay Hub 'ı işlem hattı. Daha fazla bilgi için bkz. [Olay Hub 'ından Azure Veri Gezgini 'a veri](ingest-data-event-hub.md)alma.

* Logstash eklentisi, bkz. [logstash 'Ten Azure 'a veri alma veri Gezgini](ingest-data-logstash.md).

* Kafka Bağlayıcısı, bkz. [Kafka 'Dan Azure Veri Gezgini 'a veri](ingest-data-kafka.md)alma.

### <a name="ingestion-using-integration-services"></a>Tümleştirme Hizmetleri kullanılarak alma

* Azure 'daki analiz iş yükleri için tam olarak yönetilen bir veri tümleştirme hizmeti olan Azure Data Factory (ADF), [desteklenen veri depoları ve biçimlerini](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)kullanarak verileri Azure Veri Gezgini kopyalamak için. Daha fazla bilgi için bkz. [Azure Data Factory verileri Azure Veri Gezgini kopyalama](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Programlı giriş

Azure Veri Gezgini, sorgu ve veri alımı için kullanılabilecek SDK 'lar sağlar. Programlı alma, Alım maliyetlerini (SMM) azaltmak için en iyi duruma getirilir ve bu işlem sırasında ve sonrasında depolama işlemlerini en aza indirir.

**Kullanılabilir SDK 'lar ve açık kaynaklı projeler**:

Kusto, ile veri almak ve sorgulamak için kullanılabilen istemci SDK 'Sı sunar:

* [Python SDK'sı](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Düğüm SDK 'Sı](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programlama teknikleri**:

* Verileri Azure Veri Gezgini veri yönetimi hizmeti üzerinden alma (yüksek aktarım hızı ve güvenilir giriş):

    [**Toplu iş**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) alımı (SDK tarafından verilmiştir): istemci, verileri Azure Blob depolama alanına yükler (Azure Veri Gezgini Data Management hizmeti tarafından belirlenir) ve bir Azure kuyruğuna bildirim gönderir. Toplu alma, yüksek hacimli, güvenilir ve ucuz veri alımı için önerilen tekniktir.

* Verileri doğrudan Azure Veri Gezgini altyapısına geri alma (en iyi araştırma ve prototipleme için uygundur):

  * **Satır içi**Alım: bant içi veriler içeren denetim komutu (. ınest satır içi), geçici test amaçlarıyla tasarlanmıştır.

  * **Sorgudan al: sorgu**sonuçlarına işaret eden denetim komutu (. set,. set-veya-Append,. set-veya-replace) rapor veya küçük geçici tablolar oluşturmak için kullanılır.

  * **Depolamadan**alma: dışarıda depolanan veriler (örneğin, Azure Blob depolama) ile denetim komutu (. ınest), verilerin verimli bir şekilde oluşturulmasına olanak sağlar.

**Farklı yöntemlerin gecikmesi**:

| Yöntem | Gecikme süresi |
| --- | --- |
| **Satır içi Alım** | Hemen |
| **Sorgudan al** | Sorgu süresi + işleme süresi |
| **Depolama alanından alma** | İndirme süresi + işleme süresi |
| **Sıraya alınan Alım** | Toplu işlem süresi + işleme süresi |
| |

İşleme süresi, birkaç saniyeden daha az veri boyutuna bağlıdır. Toplu işlem süresi varsayılan olarak 5 dakikadır.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>En uygun alma yöntemini seçme

Verileri almak için başlamadan önce aşağıdaki soruları sormanız gerekir.

* Verilerim nerede bulunur? 
* Veri biçimi nedir ve değişebilir mi? 
* Sorgulanacak gerekli alanlar nelerdir? 
* Beklenen veri hacmi ve hızı nedir? 
* Kaç olay türü bekleniyor (tablo sayısı olarak yansıtılmıştır)? 
* Olay şemasının ne sıklıkta değiştirilmesi bekleniyor? 
* Verileri kaç tane düğüm oluşturacak? 
* Kaynak işletim sistemi nedir? 
* Gecikme süresi gereksinimleri nelerdir? 
* Mevcut yönetilen alma ardışık düzenlerinden biri kullanılabilir mi? 

Olay Hub 'ı ve IoT Hub gibi bir mesajlaşma hizmetini temel alan mevcut bir altyapısına sahip kuruluşlar için bağlayıcı kullanmak muhtemelen en uygun çözümdür. Sıraya alınan Alım, büyük veri birimlerine uygundur.

## <a name="supported-data-formats"></a>Desteklenen veri biçimleri

Sorgudan alma haricinde tüm alım yöntemleri için, verileri Azure Veri Gezgini ayrıştırabilmesi için biçimlendirin. 
* Desteklenen veri biçimleri şunlardır: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (satır için ayrılmış, çok satırlı), avro ve Parquet. 
* ZIP ve GZIP sıkıştırmasını destekler.

> [!NOTE]
> Veriler kullanılırken, veri türleri hedef tablo sütunlarına göre algılanır. Bir kayıt tamamlanmamışsa veya bir alan gerekli veri türü olarak ayrıştırılamıyorsa, karşılık gelen tablo sütunları null değerlerle doldurulur.

## <a name="ingestion-recommendations-and-limitations"></a>Alma önerileri ve sınırlamaları

* Alınan verilerin etkili bekletme ilkesi, veritabanının bekletme ilkesinden türetilir. Ayrıntılar için [bekletme ilkesi](/azure/kusto/concepts/retentionpolicy) 'ne bakın. Verilerin alımı, **tablo** alımı veya **veritabanı** alma izinleri gerektirir.
* Alım, en fazla 5 GB dosya boyutunu destekler. Öneri, 100 MB ile 1 GB arasında dosya alma.

## <a name="schema-mapping"></a>Şema eşleme

Şema eşleme, kaynak veri alanlarını hedef tablo sütunlarına bağlamaya yardımcı olur.

* [CSV eşleme](/azure/kusto/management/mappings?branch=master#csv-mapping) (isteğe bağlı) tüm sıralı tabanlı biçimler ile birlikte kullanılır. Bu, alma komutu parametresi kullanılarak gerçekleştirilebilir veya [tabloda önceden oluşturulmuş](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) ve alma komut parametresinden Başvurulmuş olabilir.
* [JSON eşlemesi](/azure/kusto/management/mappings?branch=master#json-mapping) (zorunlu) ve [avro eşlemesi](/azure/kusto/management/mappings?branch=master#avro-mapping) (zorunlu), alma komutu parametresi kullanılarak gerçekleştirilebilir. Ayrıca [, tabloda önceden oluşturulmuş](/azure/kusto/management/tables#create-ingestion-mapping) ve alma komut parametresinden başvurulabilirler.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Olay Hub 'ından Azure Veri Gezgini veri alma](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Azure Veri Gezgini Event Grid aboneliğini kullanarak verileri alma](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Kafka 'dan Azure 'a veri alma Veri Gezgini](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Azure Veri Gezgini Python kitaplığını kullanarak verileri alma](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Azure Veri Gezgini node kitaplığını kullanarak verileri alma](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Azure Veri Gezgini .NET Standard SDK 'sını kullanarak verileri alma (Önizleme)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Logstash 'ten Azure 'a veri alma Veri Gezgini](ingest-data-logstash.md)
