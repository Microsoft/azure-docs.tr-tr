---
title: Eşleme veri akışında havuz dönüştürme
description: Eşleme veri akışında bir havuz dönüştürmeyi yapılandırmayı öğrenin.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/06/2021
ms.openlocfilehash: 8970fc2d5f11d106f34cf2f4c05a1c61bdc6c438
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107423"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Eşleme veri akışında havuz dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verilerinizi dönüştürmeyi tamamladıktan sonra havuz dönüşümünü kullanarak bir hedef depoya yazın. Her veri akışı için en az bir havuz dönüştürmesi gerekir, ancak dönüştürme akışınızı tamamlaması için gereken sayıda havuz yazabilirsiniz. Ek havuzlar yazmak için yeni dallar ve koşullu bölmeler aracılığıyla yeni akışlar oluşturun.

Her havuz dönüştürmesi, tam olarak bir Azure Data Factory veri kümesi nesnesiyle veya bağlı hizmetle ilişkilendirilir. Havuz dönüştürmesi, yazmak istediğiniz verilerin şeklini ve konumunu belirler.

## <a name="inline-datasets"></a>Satır içi veri kümeleri

Bir havuz dönüştürmesi oluşturduğunuzda, havuz bilgilerinizin bir veri kümesi nesnesi içinde mi yoksa havuz dönüştürmesi içinde mi tanımlandığını seçin. Çoğu biçim yalnızca bir veya diğeri içinde kullanılabilir. Belirli bir bağlayıcıyı nasıl kullanacağınızı öğrenmek için ilgili bağlayıcı belgesine bakın.

Bir biçim hem satır içi hem de bir veri kümesi nesnesinde destekleniyorsa, her ikisine de faydalanır. Veri kümesi nesneleri, diğer veri akışlarında ve kopyalama gibi etkinliklerde kullanılabilen yeniden kullanılabilir varlıklardır. Bu yeniden kullanılabilir varlıklar özellikle sıkı bir şema kullandığınızda kullanışlıdır. Veri kümeleri Spark temelli değildir. Bazen, havuz dönüşümünde belirli ayarları veya şema projeksiyonunu geçersiz kılmanız gerekebilir.

Esnek şemalar, tek kapalı havuz örnekleri veya parametreli havuzlar kullandığınızda satır içi veri kümeleri önerilir. Havuzunuzu yoğun şekilde parametreleştirmiş ise, satır içi veri kümeleri "kukla" bir nesne oluşturmanıza izin verir. Satır içi veri kümeleri Spark 'a dayalıdır ve özellikleri veri akışına yereldir.

Satır içi veri kümesi kullanmak için **Havuz türü** seçicisinde istediğiniz biçimi seçin. Bir havuz veri kümesi seçmek yerine, bağlanmak istediğiniz bağlı hizmeti seçersiniz.

![Satır Içi seçili öğesini gösteren ekran görüntüsü.](media/data-flow/inline-selector.png "Satır Içi seçili öğesini gösteren ekran görüntüsü.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Desteklenen havuz türleri

Veri akışı eşleme, ayıklama, yükleme ve dönüştürme (ELT) yaklaşımını izler ve Azure 'da tümü olan *hazırlama* veri kümeleri ile birlikte kullanılır. Şu anda, aşağıdaki veri kümeleri bir kaynak dönüşümünde kullanılabilir.

| Bağlayıcı | Biçimlendir | Veri kümesi/satır içi |
| --------- | ------ | -------------- |
| [Azure Blob Depolama](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Sınırlandırılmış metin](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Sınırlandırılmış metin](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage 2. Nesil](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Ortak Veri Modeli](format-common-data-model.md#sink-properties)<br>[Sınırlandırılmış metin](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [MySQL için Azure Veritabanı](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [PostgreSQL için Azure Veritabanı](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Veritabanı](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL yönetilen örneği (Önizleme)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Bu bağlayıcılara özgü ayarlar **Ayarlar** sekmesinde bulunur. Bu ayarlara ilişkin bilgi ve veri akışı betiği örnekleri bağlayıcı belgelerinde bulunur.

Azure Data Factory, 90 ' den fazla [Yerel](connector-overview.md)bağlayıcıya erişebilir. Veri akışınızdan diğer kaynaklara veri yazmak için kopyalama etkinliğini kullanarak bu verileri desteklenen bir alıcıdan yükleyin.

## <a name="sink-settings"></a>Havuz ayarları

Bir havuz ekledikten sonra, **Havuz** sekmesi aracılığıyla yapılandırın. Burada, havuzlarınızın yazdığı veri kümesini seçebilir veya oluşturabilirsiniz. Veri kümesi parametrelerinin geliştirme değerleri [hata ayıklama ayarlarında](concepts-data-flow-debug-mode.md)yapılandırılabilir. (Hata ayıklama modunun açık olması gerekir.)

Aşağıdaki videoda, metin ile ayrılmış dosya türleri için birçok farklı havuz seçeneği açıklanmaktadır.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Havuz ayarlarını gösteren ekran görüntüsü.](media/data-flow/sink-settings.png "Havuz ayarlarını gösteren ekran görüntüsü.")

**Şema** kayması: [şema DRFT](concepts-data-flow-schema-drift.md) , sütun değişikliklerini açıkça tanımlamaya gerek kalmadan veri akışlarınızda esnek şemaları yerel olarak işleme Data Factory yeteneğidir. Havuz veri şemasında tanımlandıklarınızın üzerine ek sütunlar yazmak için **şema bitsede Izin ver** ' i etkinleştirin.

**Şemayı doğrula**: şemayı doğrula seçilirse, gelen kaynak şemasının herhangi bir sütunu kaynak projeksiyonda bulunamazsa veya veri türleri eşleşmiyorsa veri akışı başarısız olur. Kaynak verilerin tanımlı projeksiyonun sözleşmesini karşıladığından zorlamak için bu ayarı kullanın. Bu, sütun adlarının veya türlerin değiştiğini bildirmek için veritabanı kaynak senaryolarında yararlı olur.

## <a name="cache-sink"></a>Önbellek havuzu

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

*Önbellek havuzu* , veri akışı veri deposu yerine Spark önbelleğine yazar. Veri akışlarını eşleme bölümünde, bir *önbellek araması* kullanarak bu veriye birçok kez aynı Flow dahilinde başvuru yapabilirsiniz. Bu, bir ifadenin parçası olarak verilere başvurmak istediğinizde ancak sütunlara açıkça dahil etmek istemediğinizde yararlıdır. Bir önbellek havuzunun, bir veri deposunda en yüksek değer ve bir hata iletisi veritabanına eşleşen hata kodları arayan yaygın örnekleri. 

Önbellek havuzuna yazmak için bir havuz dönüştürmesi ekleyin ve havuz türü olarak **önbellek** ' yi seçin. Diğer havuz türlerinden farklı olarak, bir dış depoya yazmamak istemediğiniz için bir veri kümesi veya bağlı hizmet seçmeniz gerekmez. 

![Önbellek havuzunu seçin](media/data-flow/select-cache-sink.png "Önbellek havuzunu seçin")

Havuz ayarları ' nda, isteğe bağlı olarak önbellek havuzunun anahtar sütunlarını belirtebilirsiniz. Bu, `lookup()` işlevi bir önbellek aramasında kullanırken eşleşen koşullar olarak kullanılır. Anahtar sütunları belirtirseniz, `outputs()` işlevi bir önbellek aramasında kullanamazsınız. Önbellek arama sözdizimi hakkında daha fazla bilgi edinmek için bkz. [önbelleğe alınmış aramalar](concepts-data-flow-expression-builder.md#cached-lookup).

![Önbellek havuzu anahtar sütunları](media/data-flow/cache-sink-key-columns.png "Önbellek havuzu anahtar sütunları")

Örneğin, adlı bir önbellek havuzunda tek bir anahtar sütunu belirtdiğimde `column1` `cacheExample` , çağırma `cacheExample#lookup()` tek bir parametreye sahip olur ve önbellek havuzunda hangi satırın eşleştiğini belirtir. İşlevi, eşlenen her sütun için alt sütunlarla tek bir karmaşık sütun verir.

> [!NOTE]
> Önbellek havuzu, bir önbellek araması aracılığıyla buna başvuran dönüşümden tamamen bağımsız bir veri akışında olmalıdır. Önbellek havuzu ayrıca yazılan ilk havuz olmalıdır. 

## <a name="field-mapping"></a>Alan eşlemesi

Bir seçim dönüşümüne benzer şekilde, havuzun **eşleme** sekmesinde hangi gelen sütunların yazılacağını seçebilirsiniz. Varsayılan olarak, düzeltebilecekler sütunları dahil olmak üzere tüm giriş sütunları eşlenir. Bu davranış, *automaas* olarak bilinir.

Automakapatmadan sonra, sabit sütun tabanlı eşlemeler veya kural tabanlı eşlemeler ekleyebilirsiniz. Kural tabanlı eşlemelerle, desenler eşleştirme ile ifadeler yazabilirsiniz. Sabit eşleme, mantıksal ve fiziksel sütun adlarını eşler. Kural tabanlı eşleme hakkında daha fazla bilgi için bkz. [eşleme veri akışındaki sütun desenleri](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Özel havuz sıralaması

Varsayılan olarak, veriler belirleyici olmayan bir sırada birden çok havuza yazılır. Yürütme altyapısı, dönüştürme mantığı tamamlanana kadar verileri paralel olarak yazar ve havuz sıralaması her bir çalıştırmaya göre değişiklik gösterebilir. Tam bir havuz sıralaması belirtmek için, veri akışının **genel** sekmesinde **özel havuz sıralamasını** etkinleştirin. Etkinleştirildiğinde, havuzlar artan sırada sırayla yazılır.

![Özel havuz sıralamasını gösteren ekran görüntüsü.](media/data-flow/custom-sink-ordering.png "Özel havuz sıralamasını gösteren ekran görüntüsü.")

> [!NOTE]
> [Önbelleğe alınmış aramaları](./concepts-data-flow-expression-builder.md#cached-lookup)kullanırken, havuz sıraağınızın önbelleğe alınmış havuzlarınızın, sıralamada en düşük (veya ilk) olarak ayarlandığından emin olun.

![Özel havuz sıralaması](media/data-flow/cache-2.png "Özel havuz sıralaması")

### <a name="sink-groups"></a>Havuz grupları

Bir havuz serisi için aynı sıra numarasını uygulayarak, havuzları birlikte gruplandırabilirsiniz. ADF, bu havuzları paralel olarak yürütülebilecek gruplar olarak değerlendirir. Paralel yürütme seçenekleri, işlem hattı veri akışı etkinliğinde belirir.

## <a name="error-row-handling"></a>Hata satırı işleme

Veritabanlarına yazarken, hedef tarafından ayarlanan kısıtlamalar nedeniyle belirli veri satırları başarısız olabilir. Varsayılan olarak, bir veri akışı çalıştırması aldığı ilk hatada başarısız olur. Belirli bağlayıcılarda, tek tek satırlarda bile veri akışının tamamlanmasını sağlayan **hata durumunda devam** etmeyi tercih edebilirsiniz. Şu anda bu özellik yalnızca Azure SQL veritabanı 'nda kullanılabilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda hata satırı işleme](connector-azure-sql-database.md#error-row-handling).

Aşağıda, havuz dönüşümünüze veritabanı hata satırı işlemenin otomatik olarak nasıl kullanılacağı hakkında bir video öğreticisi verilmiştir.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Havuzda veri önizleme

Hata ayıklama modunda bir veri önizlemesi getirilirken, havuzunuzu hiçbir veri yazılmaz. Verilerin nasıl görüneceğine ilişkin bir anlık görüntü döndürülür, ancak hedefe hiçbir şey yazılmayacak. Havuzunuzu veri yazmayı test etmek için işlem hattı tuvalinden bir işlem hattı hata ayıklaması çalıştırın.

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="example"></a>Örnek

Aşağıda bir havuz dönüştürme ve veri akışı betiği örneği verilmiştir:

```
sink(input(
        movie as integer,
        title as string,
        genres as string,
        year as integer,
        Rating as integer
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:false,
    updateable:true,
    upsertable:false,
    keys:['movie'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true,
    saveOrder: 1,
    errorHandlingOption: 'stopOnFirstError') ~> sink1
```

## <a name="next-steps"></a>Sonraki adımlar

Veri akışınızı oluşturduğunuza göre, işlem [hattınızı bir veri akışı etkinliği](concepts-data-flow-overview.md)ekleyin.
