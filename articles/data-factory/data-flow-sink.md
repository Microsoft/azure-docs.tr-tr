---
title: Azure Data Factory veri akışı eşleme özelliğinde bir havuz dönüştürmesi ayarlayın
description: Eşleme veri akışında bir havuz dönüştürmeyi ayarlamayı öğrenin.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fa6a2fd853673493c93dbe65f889468c8e0c8617
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082944"
---
# <a name="sink-transformation-for-a-data-flow"></a>Veri akışı için havuz dönüştürme

Veri akışınızı dönüştürdükten sonra, verileri bir hedef veri kümesine havuza alabilirsiniz. Havuz dönüşümünde, hedef çıktı verileri için bir veri kümesi tanımı seçin. Veri akışınız için gereken sayıda havuz dönüşümlerine sahip olabilirsiniz.

Gelen verilerdeki şema kayması ve değişiklikler için, çıkış verilerini çıktı veri kümesinde tanımlı bir şema olmadan bir klasöre havuza alma. Ayrıca, kaynakta **şema Için Izin ver** ' i seçerek kaynaklarınızda sütun değişikliklerini hesaba ekleyebilirsiniz. Ardından, havuzdaki tüm alanları yeniden belirleyin.

![Otomatik eşleme seçeneği dahil olmak üzere havuz sekmesindeki seçenekler](media/data-flow/sink1.png "Havuz 1")

Tüm gelen alanları havuza almak için **Otomatik eşlemeyi**açın. Hedefe havuza eklenecek alanları seçmek veya hedefteki alanların adlarını değiştirmek için **Otomatik eşlemeyi**devre dışı bırakın. Ardından, çıkış alanlarını eşlemek için **eşleme** sekmesini açın.

![Eşleme sekmesindeki seçenekler](media/data-flow/sink2.png "Havuz 2")

## <a name="output"></a>Çıktı 
Azure Blob depolama veya Data Lake Storage havuz türleri için, dönüştürülmüş verileri bir klasöre çıktı. Spark, havuz dönüşümünün kullandığı bölümleme şemasına göre bölümlenmiş çıkış veri dosyaları oluşturur. 

**En iyileştirme** sekmesinden bölümleme şemasını ayarlayabilirsiniz. Çıktlarınızı tek bir dosyada birleştirmek Data Factory istiyorsanız **tek bölüm**' ü seçin. Bölümlenmiş klasörleri sürdürmek veya oluşturmak isterseniz, **anahtar bölümleme** kullanın ve bölümlenmiş klasör yapıları için kullanmak istediğiniz anahtarları ayarlayın.

![En Iyileştirme sekmesindeki seçenekler](media/data-flow/opt001.png "havuz seçenekleri")

## <a name="field-mapping"></a>Alan eşleme
Havuz dönüşümünüzün **eşleme** sekmesinde, sol taraftaki gelen sütunları sağ taraftaki hedeflere eşleyebilirsiniz. Veri akışlarını dosyalara havuza aldığınızda Data Factory her zaman bir klasöre yeni dosyalar yazar. Bir veritabanı veri kümesine eşlediğinizde, eklemek, güncelleştirmek, kaldırmak veya silmek için veritabanı tablosu işlem seçeneklerini tercih edersiniz.

![Eşleme sekmesi](media/data-flow/sink2.png "Yapma")

Eşleme tablosunda birden çok sütunu bağlamak, birden çok sütunu bağlamak veya birden çok satırı aynı sütun adına eşlemek için çoklu seçim yapabilirsiniz.

Gelen alan kümesini her zaman bir hedefle eşlemek ve esnek şema tanımlarını tam kabul etmek için, **şema Drçıkmasına Izin ver**' i seçin.

![Veri kümesindeki sütunlarla eşlenen alanları gösteren eşleme sekmesi](media/data-flow/multi1.png "birden çok seçenek")

Sütun eşlemelerinizi sıfırlamak için **yeniden eşle**' yi seçin.

![Havuz sekmesi](media/data-flow/sink1.png "Bir havuz")

Şema değişirse havuzun başarısız olması için **Şemayı doğrula** ' yı seçin.

Hedef klasördeki hedef dosyaları yazmadan önce havuz klasörünün içeriğini kesmek için **klasörü temizle** ' yi seçin.

## <a name="fixed-mapping-vs-rule-based-mapping"></a>Sabit eşleme ve kural tabanlı eşleme karşılaştırması
Otomatik eşlemeyi devre dışı bırakırsanız, sütun tabanlı eşleme (Sabit eşleme) veya kural tabanlı eşleme ekleme seçeneğine sahip olursunuz. Kural tabanlı eşleme, sabit eşleme mantıksal ve fiziksel sütun adlarını eşleyebileceğiniz sırada eşleşen ifadeler yazmanıza izin verir.

![Kural tabanlı eşleme](media/data-flow/rules4.png "Kural tabanlı eşleme")

Kural tabanlı eşleme ' yi seçtiğinizde, gelen model kurallarını eşleştirmek ve giden alan adlarını tanımlamak için eşleşen ifadenizi değerlendirmek üzere ADF 'yi öğreneceksiniz. Hem alan hem de kural tabanlı eşlemelerin birleşimini ekleyebilirsiniz. Daha sonra alan adları, kaynaktan gelen meta veriler temelinde ADF tarafından çalışma zamanında oluşturulur. Oluşturulan alanların adlarını hata ayıklama sırasında ve veri önizleme bölmesini kullanarak görüntüleyebilirsiniz.

Model eşleştirme ayrıntıları, [sütun deseninin belgelerinde](concepts-data-flow-column-pattern.md)yer alır.

Ayrıca, satırı genişleterek ve "ad eşleşmeleri:" nın yanına bir normal ifade girerek kural tabanlı eşleştirmeyi kullanırken normal ifade desenleri de girebilirsiniz.

![Regex eşleme](media/data-flow/scdt1g4.png "Regex eşleme")

Kural tabanlı eşleme ve sabit eşleme ile ilgili temel yaygın bir örnek, tüm gelen alanları Hedefinizdeki aynı ada eşlemek istediğiniz durumdur. Sabit eşlemeler söz konusu olduğunda, tablodaki her sütunu ayrı ayrı listeleyin. Kural tabanlı eşleme için, ```true()``` kullanan tüm alanları ```$$```temsil eden aynı gelen alan adına eşleyen tek bir kuralınız olur.

### <a name="sink-association-with-dataset"></a>Veri kümesiyle havuz ilişkilendirmesi

Havuzunuzu seçtiğiniz veri kümesi, veri kümesi tanımında tanımlanmış bir şemaya sahip olabilir veya olmayabilir. Tanımlı bir şemaya sahip değilse, şema DRA izin vermeniz gerekir. Sabit bir eşleme tanımladığınızda, mantıksal-fiziksel ad eşlemesi havuz dönüşümünde kalır. Veri kümesinin şema tanımını değiştirirseniz, havuz eşlemenizi büyük olasılıkla bozacaksınız. Bunu önlemek için kural tabanlı eşleme kullanın. Kural tabanlı eşlemeler genelleştirilir, yani veri kümenizdeki şema değişikliklerinin eşlemeyi bozmayacak anlamına gelir.

## <a name="file-name-options"></a>Dosya adı seçenekleri

Dosya adlandırmayı ayarlama: 

   * **Varsayılan**: Spark 'ın bölüm varsayılanlarına göre dosya adına erişmesine izin verin.
   * **Model**: çıkış dosyalarınız için bir model girin. Örneğin, **[n] kredileri** , loans1. csv, loans2. csv, vb. oluşturacaktır.
   * **Bölüm başına**: bölüm başına bir dosya adı girin.
   * **Sütunda veri olarak**: çıkış dosyasını bir sütunun değeri olarak ayarlayın.
   * **Tek bir dosyaya çıkış**: Bu seçenekle ADF bölümlenmiş çıkış dosyalarını tek bir adlandırılmış dosyada birleştirir. Bu seçeneği kullanmak için, veri kümeniz bir klasör adına çözümlenmelidir. Ayrıca, bu birleştirme işleminin düğüm boyutuna bağlı olarak başarısız olabileceğini lütfen unutmayın.

> [!NOTE]
> Dosya işlemleri yalnızca veri akışını Yürüt etkinliğini çalıştırdığınızda başlar. Veri akışı hata ayıklama modunda başlamamazlar.

## <a name="database-options"></a>Veritabanı seçenekleri

Veritabanı ayarlarını seçin:

![SQL havuzu seçeneklerini gösteren ayarlar sekmesi](media/data-flow/alter-row2.png "SQL seçenekleri")

* **Güncelleştirme yöntemi**: varsayılan değer ekleme için izin verilir. Kaynağınızdan yeni satırlar eklemeyi durdurmak istiyorsanız **eklemeye Izin ver** ' i temizleyin. Satırları güncelleştirmek, kaldırmak veya silmek için önce bu eylemler için satırları etiketlemek üzere bir alter-Row dönüşümü ekleyin. 
* **Tablo yeniden**oluşturma: veri akışı tamamlanmadan önce hedef tablonuzu bırakın veya oluşturun.
* **Tablo kes**: veri akışı tamamlanmadan önce hedef tablonuzdaki tüm satırları kaldırın.
* **Yığın boyutu**: demetini parçalara yazmalar için bir sayı girin. Büyük veri yükleri için bu seçeneği kullanın. 
* **Hazırlamayı etkinleştir**: Azure veri ambarı 'nı havuz veri kümeniz olarak yüklediğinizde PolyBase kullanın.
* **SQL betiklerini ön ve sonrası**: (ön işleme) ve sonra (işlem sonrası) verileri havuz veritabanınıza yazıldıktan sonra yürütülecek çok satırlı SQL betikleri girin

![SQL işleme betikleri ön ve sonrası](media/data-flow/prepost1.png "SQL işleme betikleri")

> [!NOTE]
> Veri akışı ' nda, hedef veritabanınızda yeni bir tablo tanımı oluşturmak için Data Factory yönlendirebilirsiniz. Tablo tanımını oluşturmak için, havuz dönüşümünde yeni bir tablo adına sahip bir veri kümesi ayarlayın. SQL veri kümesinde tablo adının altında **Düzenle** ' yi seçin ve yeni bir tablo adı girin. Ardından, havuz dönüşümünde, **şema Drçıkmasına Izin ver**' i açın. **Içeri aktarma şemasını** **none**olarak ayarlayın.

![Tablo adının nerede düzenleneceğini gösteren SQL veri kümesi ayarları](media/data-flow/dataset2.png "SQL şeması")

> [!NOTE]
> Veritabanı havuzinizdeki satırları güncelleştirdiğinizde veya sildiğinizde, anahtar sütununu ayarlamanız gerekir. Bu ayar alter-Row dönüşümünün veri taşıma kitaplığındaki (DML) benzersiz satırı belirlemesine izin verir.

### <a name="cosmosdb-specific-settings"></a>CosmosDB 'ye özgü ayarlar

CosmosDB 'de giriş verileri olduğunda, bu ek seçenekleri göz önünde bulundurmanız gerekir:

* Bölüm anahtarı: Bu gerekli bir alandır. Koleksiyonunuz için bölüm anahtarını temsil eden bir dize girin. Örnek: ```/movies/title```
* Aktarım hızı: Bu veri akışının her yürütmesi için CosmosDB koleksiyonunuza uygulamak istediğiniz ru sayısı için isteğe bağlı bir değer ayarlayın. Minimum değer 400 ' dir.

## <a name="next-steps"></a>Sonraki adımlar
Veri akışınızı oluşturduğunuza göre, işlem [hattınızı bir veri akışı etkinliği](concepts-data-flow-overview.md)ekleyin.
