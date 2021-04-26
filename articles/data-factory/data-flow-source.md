---
title: Eşleme veri akışında kaynak dönüştürme
description: Eşleme veri akışında bir kaynak dönüştürmeyi ayarlamayı öğrenin.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 310a062a8600539750935c93c7d10a1cf17a885d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016398"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Eşleme veri akışında kaynak dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kaynak dönüştürmesi veri akışı için veri kaynağınızı yapılandırır. Veri akışları tasarladığınızda, ilk adımınız her zaman bir kaynak dönüşümü yapılandırır. Kaynak eklemek için veri akışı tuvalindeki **Kaynak Ekle** kutusunu seçin.

Her veri akışı için en az bir kaynak dönüştürmesi gerekir, ancak veri dönüştürmelerinizi tamamlayabilmeniz için gereken sayıda kaynak ekleyebilirsiniz. Bu kaynakları bir birleştirme, arama veya birleşim dönüşümle birlikte birleştirebilirsiniz.

Her kaynak dönüştürmesi, tam olarak bir veri kümesiyle veya bağlı hizmetle ilişkilendirilir. Veri kümesi, yazmak veya okumak istediğiniz verilerin şeklini ve konumunu tanımlar. Dosya tabanlı veri kümesi kullanıyorsanız, aynı anda birden fazla dosyayla çalışmak için kaynağınızdan joker karakterler ve dosya listelerini kullanabilirsiniz.

## <a name="inline-datasets"></a>Satır içi veri kümeleri

Kaynak dönüştürmesi oluştururken yaptığınız ilk karar, kaynak bilgilerinizin bir veri kümesi nesnesi içinde veya kaynak dönüşümünde tanımlanıp tanımlanmayacağı. Çoğu biçim yalnızca bir veya diğeri içinde kullanılabilir. Belirli bir bağlayıcıyı nasıl kullanacağınızı öğrenmek için ilgili bağlayıcı belgesine bakın.

Bir biçim hem satır içi hem de bir veri kümesi nesnesinde destekleniyorsa, her ikisine de faydalanır. Veri kümesi nesneleri, diğer veri akışlarında ve kopyalama gibi etkinliklerde kullanılabilen yeniden kullanılabilir varlıklardır. Bu yeniden kullanılabilir varlıklar özellikle sıkı bir şema kullandığınızda kullanışlıdır. Veri kümeleri Spark temelli değildir. Bazen, kaynak dönüşümünde belirli ayarları veya şema projeksiyonunu geçersiz kılmanız gerekebilir.

Esnek şemalar, tek seferlik kaynak örnekleri veya parametreli kaynaklar kullandığınızda satır içi veri kümeleri önerilir. Kaynağınız çok fazla parametreli ise, satır içi veri kümeleri "kukla" bir nesne oluşturmanıza izin verir. Satır içi veri kümeleri Spark 'a dayalıdır ve özellikleri veri akışına yereldir.

Satır içi veri kümesi kullanmak için **kaynak türü** seçicisinde istediğiniz biçimi seçin. Kaynak veri kümesi seçmek yerine, bağlanmak istediğiniz bağlı hizmeti seçersiniz.

![Satır Içi seçili öğesini gösteren ekran görüntüsü.](media/data-flow/inline-selector.png "Satır Içi seçili öğesini gösteren ekran görüntüsü.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Desteklenen kaynak türleri

Veri akışı eşleme, ayıklama, yükleme ve dönüştürme (ELT) yaklaşımını izler ve Azure 'da tümü olan *hazırlama* veri kümeleri ile birlikte kullanılır. Şu anda, aşağıdaki veri kümeleri bir kaynak dönüşümünde kullanılabilir.

| Bağlayıcı | Biçimlendir | Veri kümesi/satır içi |
| --------- | ------ | -------------- |
| [Azure Blob Depolama](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Sınırlandırılmış metin](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Sınırlandırılmış metin](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage 2. Nesil](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Ortak Veri Modeli](format-common-data-model.md#source-properties)<br>[Sınırlandırılmış metin](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br/>-/✓<br>✓/✓<br>✓/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [MySQL için Azure Veritabanı](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [PostgreSQL için Azure Veritabanı](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Veritabanı](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Yönetilen Örnek](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Bu bağlayıcılara özgü ayarlar, **kaynak seçenekleri** sekmesinde bulunur. Bu ayarlara ilişkin bilgi ve veri akışı betiği örnekleri bağlayıcı belgelerinde bulunur.

Azure Data Factory, 90 ' den fazla [Yerel](connector-overview.md)bağlayıcıya erişebilir. Veri akışınız içindeki diğer kaynaklardan verileri dahil etmek için kopyalama etkinliğini kullanarak bu verileri desteklenen hazırlama alanlarından birine yükleyin.

## <a name="source-settings"></a>Kaynak ayarları

Kaynak ekledikten sonra, **kaynak ayarları** sekmesini kullanarak yapılandırın. Burada kaynak noktalarınızın veri kümesini seçebilirsiniz veya oluşturabilirsiniz. Verileriniz için şema ve örnekleme seçeneklerini de belirleyebilirsiniz.

Veri kümesi parametrelerinin geliştirme değerleri [hata ayıklama ayarlarında](concepts-data-flow-debug-mode.md)yapılandırılabilir. (Hata ayıklama modunun açık olması gerekir.)

![Kaynak ayarları sekmesini gösteren ekran görüntüsü.](media/data-flow/source1.png "Kaynak ayarları sekmesini gösteren ekran görüntüsü.")

**Çıkış akışı adı**: kaynak dönüşümünün adı.

**Kaynak türü**: satır içi veri kümesi mi yoksa mevcut bir veri kümesi nesnesi mi kullanacağınızı seçin.

**Test bağlantısı**: veri akışının Spark hizmetinin kaynak veri kümenizde kullanılan bağlantılı hizmete başarıyla bağlanıp bağlanamamadığını test edin. Bu özelliğin etkinleştirilmesi için hata ayıklama modunun açık olması gerekir.

**Şema** kayması: [şema DRFT](concepts-data-flow-schema-drift.md) , sütun değişikliklerini açıkça tanımlamaya gerek kalmadan veri akışlarınızda esnek şemaları yerel olarak işleme Data Factory yeteneğidir.

* Kaynak sütunlar sık olarak **değişeyorsa şema kurutma Izin ver** onay kutusunu seçin. Bu ayar tüm gelen kaynak alanlarının, iç dönüşümlerdeki dönüşümlere akmasını sağlar.

* **Düzeltebilecekler sütun türlerini** çıkar seçeneğinin belirlenmesi, bulunan her yeni sütunun veri türlerini algılamasını ve tanımlamasını Data Factory söyler. Bu özellik kapatılmış durumdayken tüm düzeltebilecekler sütunları dize türünde olacaktır.

**Şemayı doğrula:** **Şemayı doğrula** seçilirse, gelen kaynak verileri veri kümesinin tanımlı şemasıyla eşleşmiyorsa veri akışı çalıştırılamaz.

**Satır sayısını atla**: **satır sayısını atla** alanı, veri kümesinin başlangıcında yok sayılacak satır sayısını belirtir.

**Örnekleme**: kaynağınızdan satır sayısını sınırlamak için **örneklemesi** etkinleştirin. Hata ayıklama amacıyla kaynağınızdan verileri test ettiğinizde veya örnekleyebilirsiniz bu ayarı kullanın. Bu, bir işlem hattından hata ayıklama modundaki veri akışlarını yürütürken çok yararlı olur.

Kaynağınızın doğru yapılandırıldığını doğrulamak için hata ayıklama modunu açın ve bir veri önizlemesi getirin. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Hata ayıklama modu açıldığında, hata ayıklama ayarlarındaki satır sınırı yapılandırması, veri önizlemesi sırasında kaynaktaki örnekleme ayarının üzerine yazar.

## <a name="source-options"></a>Kaynak seçenekleri

**Kaynak seçenekleri** sekmesi, seçilen bağlayıcıya ve biçime özgü ayarları içerir. Daha fazla bilgi ve örnek için ilgili [bağlayıcı belgelerine](#supported-sources)bakın.

## <a name="projection"></a>Projeksiyon

Veri kümelerinde bulunan şemalar gibi, bir kaynaktaki projeksiyon, kaynak verilerden veri sütunlarını, türlerini ve biçimlerini tanımlar. SQL ve Parquet gibi çoğu veri kümesi türü için bir kaynaktaki projeksiyon, veri kümesinde tanımlanan şemayı yansıtacak şekilde düzeltilir. Kaynak dosyalarınız kesin olarak türsüz (örneğin, Parquet dosyaları yerine Flat. csv dosyaları), kaynak dönüşümünde her bir alan için veri türlerini tanımlayabilirsiniz.

![Projeksiyon sekmesindeki ayarları gösteren ekran görüntüsü.](media/data-flow/source3.png "Projeksiyon sekmesindeki ayarları gösteren ekran görüntüsü.")

Metin dosyanızda tanımlı bir şema yoksa, Data Factory veri türlerini ve çıkarması için veri **türünü Algıla** ' yı seçin. Varsayılan veri biçimlerini otomatik algıla için **varsayılan biçimi tanımla** ' yı seçin.

**Şemayı Sıfırla** , yansıtmayı başvurulan veri kümesinde tanımlananla sıfırlar.

Sütun veri türlerini aşağı akış türetilmiş sütun dönüşümünde değiştirebilirsiniz. Sütun adlarını değiştirmek için bir seçme dönüşümü kullanın.

### <a name="import-schema"></a>Şemayı içeri aktar

Bir şema projeksiyonu oluşturmak üzere etkin bir hata ayıklama kümesi kullanmak için **İzdüşüm** sekmesindeki **şemayı içeri aktar** düğmesini seçin. Her kaynak türünde kullanılabilir. Şemayı buraya aktarmak, veri kümesinde tanımlanan yansıtmayı geçersiz kılacaktır. DataSet nesnesi değiştirilmez.

Şemayı içeri aktarma, avro ve Azure Cosmos DB gibi veri kümelerinde yararlıdır ve şema tanımlarının veri kümesinde var olmasını gerektirmeyen karmaşık veri yapılarını destekler. Satır içi veri kümeleri için şemayı içeri aktarma, şema kayması olmadan sütun meta verilerine başvurmak için tek yoldur.

## <a name="optimize-the-source-transformation"></a>Kaynak dönüşümünü iyileştirme

**En iyileştirme** sekmesi, her bir dönüştürme adımında bölüm bilgilerinin düzenlenmesine izin verir. Çoğu durumda, **geçerli bölümleme kullan** , bir kaynağın ideal bölümlendirme yapısına yönelik en iyi hale getirir.

Bir Azure SQL veritabanı kaynağından okuyorsanız, özel **kaynak** bölümlendirme büyük olasılıkla verileri en hızlı okuyacaktır. Data Factory, veritabanınıza paralel olarak bağlantı oluşturarak büyük sorguları okur. Bu kaynak bölümleme, bir sütunda veya bir sorgu kullanılarak yapılabilir.

![Kaynak bölüm ayarlarını gösteren ekran görüntüsü.](media/data-flow/sourcepart3.png "Kaynak bölüm ayarlarını gösteren ekran görüntüsü.")

Eşleme veri akışı içinde iyileştirme hakkında daha fazla bilgi için, [optimizasyon sekmesine](concepts-data-flow-overview.md#optimize)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Türetilmiş sütun dönüşümünde](data-flow-derived-column.md) ve [seçim dönüşümünde](data-flow-select.md)veri akışınızı oluşturmaya başlayın.
