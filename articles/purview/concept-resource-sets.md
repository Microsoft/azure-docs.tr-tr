---
title: Kaynak kümelerini anlama
description: Bu makalede, kaynak kümelerinin ne olduğu ve Azure purview 'ın bunları nasıl oluşturduğu açıklanmaktadır.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587910"
---
# <a name="understanding-resource-sets"></a>Kaynak kümelerini anlama

Bu makale, Azure purview 'ın veri varlıklarını mantıksal kaynaklarla eşlemek için kaynak kümelerini nasıl kullandığını anlamanıza yardımcı olur.
## <a name="background-info"></a>Arka plan bilgileri

Ölçekli veri işleme sistemleri genellikle birden çok dosya olarak bir diskte tek bir tabloyu depolar. Bu kavram, kaynak kümeleri kullanılarak Azure purview 'da temsil edilir. Kaynak kümesi, katalogdaki çok sayıda varlığı temsil eden katalogdaki tek bir nesnedir.

Örneğin, Spark kümenizin bir veri çerçevesini Azure Data Lake Storage (ADLS) Gen2 veri kaynağına kalıcı olduğunu varsayalım. Spark 'da tablo tek bir mantıksal kaynak gibi görünse de, her biri toplam veri çerçevesinin içeriğinin bir bölümünü temsil eden binlerce Parquet dosyası vardır. IoT verileri ve Web günlüğü verilerinde aynı zorluk vardır. Günlük dosyalarını birkaç kez veren bir sensör olduğunu düşünün. Bu tek sensörden yüzlerce binlerce günlük dosyası olana kadar uzun sürmez.

Çok sayıda veri varlığını tek bir mantıksal kaynağa eşlemenin zorluğuyla ele almak için Azure purview kaynak kümelerini kullanır.

## <a name="how-azure-purview-detects-resource-sets"></a>Azure purview 'ın kaynak kümelerini nasıl algıladığı

Azure purview, Azure Blob depolama, ADLS 1. ve ADLS 2. kaynak kümelerinin algılanmasının kullanılmasını destekler.

Azure purview tarama sırasında kaynak kümelerini otomatik olarak algılar. Bu özellik, tarama yoluyla alınan tüm verilere bakar ve bunları bir dizi tanımlı desenlerle karşılaştırır.

Örneğin, URL 'SI olan bir veri kaynağını taradığınızı varsayalım `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure takip görünümü yol kesimlerine bakar ve yerleşik desenlerle eşleşip eşleşmediğine karar verir. GUID, sayı, tarih biçimleri, yerelleştirme kodları (örneğin, en-US) için yerleşik desenleri vardır. Bu durumda, sayı deseninin *23*' ü eşleştirir. Azure purview, bu dosyanın adlı bir kaynak kümesinin parçası olduğunu varsayar `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Ya da gibi bir URL için `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` Azure purview, adlı bir kaynak kümesi üreten yerelleştirme düzeniyle ve sayı düzeniyle eşleşir `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Azure purview, bu stratejiyi kullanarak aşağıdaki kaynakları aynı kaynak kümesiyle eşleyebilir `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure purview 'ın kaynak kümesi olarak algılayamayacağı dosya türleri

Kasıtlı olarak, Word, Excel veya PDF gibi çoğu belge dosya türünü kaynak kümesi olarak sınıflandırmamaya çalışır. Ortak bölümlenmiş bir dosya biçimi olduğundan özel durum CSV biçimidir.

## <a name="how-azure-purview-scans-resource-sets"></a>Azure purview kaynak kümelerini tarar

Azure purview, bir kaynak kümesinin parçası olduğu kaynakları algıladığında, tam taramadan örnek taramaya geçiş yapar. Örnek bir taramada, yalnızca kaynak kümesinde olduğu bir dosyanın alt kümesini açar. Açık olduğu her dosya için şemasını kullanır ve sınıflandırıcılarından çalıştırılır. Daha sonra Azure purview, açılan kaynaklar arasından en yeni kaynağı bulur ve bu kaynağın şema ve sınıflandırmalarını katalogda tüm kaynak kümesi için girişte kullanır.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Kaynak kümeleri hakkında Azure purview depoları

Tek şema ve sınıflandırmaların yanı sıra Azure purview, kaynak kümeleriyle ilgili olarak aşağıdaki bilgileri depolar:

- Derin taranan en son bölüm kaynağından alınan veriler.
- Kaynak kümesini oluşturan bölüm kaynaklarıyla ilgili bilgileri toplayın.
- Kaç bölüm kaynağı bulunduğunu gösteren bölüm sayısı.
- Derin taradığı örnek kümesinde kaç tane benzersiz şema bulunduğunu gösteren bir şema sayısı. Bu değer 1 – 5 veya 5 ' ten büyük değerler için bir sayıdır.
- Kaynak kümesine birden çok bölüm türü dahil edildiğinde bölüm türlerinin listesi. Örneğin, her ikisi de aynı kaynak kümesinin mantıksal bir parçası olsa da, bir IoT algılayıcısı hem XML hem de JSON dosyalarının çıktısını alabilir.

## <a name="built-in-resource-set-patterns"></a>Yerleşik kaynak kümesi desenleri

Azure takip görünümü aşağıdaki kaynak kümesi düzenlerini destekler. Bu desenler, bir dizinde veya dosya adının bir parçası olarak bir ad olarak görünebilir.
### <a name="regex-based-patterns"></a>Regex tabanlı desenler

| Model adı | Görünen Ad | Açıklama |
|--------------|--------------|-------------|
| Guid         | 'INI       | [RFC 4122](https://tools.ietf.org/html/rfc4122) ' de tanımlanan genel benzersiz tanımlayıcı |
| Sayı       | No          | Bir veya daha fazla rakam |
| Tarih/saat biçimleri | Yıl Başından Günündeki No     | Çeşitli tarih/saat biçimlerini destekliyoruz, ancak tümü {Year} [sınırlayıcı] {month} [sınırlayıcı] {Day} veya {N} s serisi ile gösteriliyor. |
| 4ByteHex     | EŞLENECEK        | 4 basamaklı bir ONALTıLıK sayı. |
| Yerelleştirme | ÇERÇEVE        | [BCP 47](https://tools.ietf.org/html/bcp47)' de tanımlanan bir dil etiketi, her ikisi-ve _ ad desteklenir (örneğin, en_ca ve en-CA) |

### <a name="complex-patterns"></a>Karmaşık desenler

| Model adı | Görünen Ad | Açıklama |
|--------------|--------------|-------------|
| Mini yol    | {Mini bölümler} | Spark bölüm dosyası tanımlayıcısı |
| Tarih (yyyy/aa/gg) InPath  | {Year}/{Month}/{Day} | Birden çok klasörü kapsayan yıl/ay/gün deseninin |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Azure purview kataloğunda kaynak kümeleri nasıl görüntülenir

Azure purview bir varlık grubuyla bir kaynak kümesi ile eşleştiğinde, katalogda görünen ad olarak kullanmak için en faydalı bilgileri çıkarmaya çalışır. Varsayılan adlandırma kuralına uygulanan bazı örnekler: 

### <a name="example-1"></a>Örnek 1

Tam ad: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Görünen ad: "Spark çıktısının adı"

### <a name="example-2"></a>Örnek 2

Tam ad: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Görünen ad: "bölümlenmiş verilerim"

### <a name="example-3"></a>Örnek 3

Tam ad: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Görünen ad: "veri"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Model kurallarını kullanarak kaynak kümesi gruplamayı özelleştirme

bir depolama hesabını tararken, Azure purview bir varlık grubunun bir kaynak kümesi olup olmadığını anlamak için tanımlı bir desenler kümesi kullanır. Azure purview 'ın kaynak kümesi gruplandırması, bazı durumlarda verilerinizi doğru şekilde yansıtmayabilir. Bu sorunlar şunları içerebilir:

- Bir varlığı kaynak kümesi olarak yanlış işaretleme
- Bir varlığı yanlış kaynak kümesine koyma
- Bir varlık, kaynak kümesi olmayan şekilde işaretleniyor

Azure purview 'ın hangi varlıkların kaynak kümesi olarak gruplandığını ve Katalog içinde nasıl görüntüleneceğini belirlemek veya geçersiz kılmak için, Yönetim Merkezi 'nde model kuralları tanımlayabilirsiniz. Adım adım yönergeler ve sözdizimi için lütfen bkz. [kaynak kümesi model kuralları](how-to-resource-set-pattern-rules.md).
## <a name="next-steps"></a>Sonraki adımlar

Azure purview kullanmaya başlamak için bkz. [hızlı başlangıç: Azure purview hesabı oluşturma](create-catalog-portal.md).
