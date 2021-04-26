---
title: Azure SQL veritabanı tablolarını Azure Data Factory ile Azure CosmosDB 'ye geçirme
description: Azure SQL veritabanı 'nda var olan bir normalleştirilmiş veritabanı şemasını alın ve Azure Data Factory bir Azure CosmosDB denormallanmış kapsayıcıya geçirin.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383951"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Azure SQL Veritabanı'ndaki normalleştirilmiş veritabanı şemasını Azure Cosmos DB'deki normalleştirilmişlikten çıkarılmış kapsayıcıya geçirme

Bu kılavuzda, Azure SQL veritabanı 'nda var olan bir normalleştirilmiş veritabanı şemasının nasıl ele alınacağını ve Azure CosmosDB 'ye yükleme için Azure CosmosDB Normalleştirilmemiş bir şemaya nasıl dönüştürüleceği açıklanmaktadır.

SQL şemaları genellikle üçüncü normal form kullanılarak modellenmiştir ve yüksek düzeyde veri bütünlüğü ve daha az yinelenen veri değeri sağlayan normalleştirilmiş şemalar sağlar. Sorgular, varlıkları okuma için tablolar arasında birbirine birleştirebilirler. CosmosDB, süper hızlı işlemler için en iyi duruma getirilmiştir ve bir belge içindeki verilerle birlikte kapsanan şemalar aracılığıyla bir koleksiyon ya da kapsayıcı içinde sorgulama yapılır.

Azure Data Factory kullanarak, varlık ilişkisi olarak birincil ve yabancı anahtarlar içeren iki Azure SQL veritabanı Normalleştirilmiş tablolarından okumak için tek bir eşleme veri akışı kullanan bir işlem hattı oluşturacağız. ADF, veri akışı Spark altyapısını kullanarak bu tabloları tek bir akışta birleştirir, birleştirilmiş satırları diziler halinde toplar ve yeni bir Azure CosmosDB kapsayıcısına eklemek için tek bir temizleme belgesi oluşturur.

Bu kılavuz, ```SalesOrderHeader``` ```SalesOrderDetail``` standart SQL Server AdventureWorks örnek veritabanından ve tablolarını kullanacak olan "Orders" adlı yeni bir kapsayıcı oluşturacak. Bu tablolar tarafından birleştirilen satış işlemlerini temsil eder ```SalesOrderID``` . Her benzersiz ayrıntı kaydının kendi birincil anahtarı vardır ```SalesOrderDetailID``` . Üst bilgi ve ayrıntı arasındaki ilişki ```1:M``` . ```SalesOrderID```ADF 'de ve ardından her ilgili ayrıntı kaydını "ayrıntı" adlı bir diziye aktaracağız.

Bu kılavuz için temsili SQL sorgusu şunlardır:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

Elde edilen CosmosDB kapsayıcısı, iç sorguyu tek bir belgeye katıştırır ve şöyle görünür:

![Koleksiyon](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

1. Yeni bir işlem hattı oluşturmak için **+ Yeni Işlem hattı** ' nı seçin.

2. Veri akışı etkinliği ekleyin

3. Veri akışı etkinliğinde **Yeni eşleme veri akışı**' nı seçin.

4. Bu veri akışı grafiğini aşağıdan oluşturacağız

![Veri akışı grafiği](media/data-flow/cosmosb1.png)

5. "SourceOrderDetails" kaynağını tanımlayın. Veri kümesi için, tabloya işaret eden yeni bir Azure SQL veritabanı veri kümesi oluşturun ```SalesOrderDetail``` .

6. "SourceOrderHeader" kaynağını tanımlayın. Veri kümesi için, tabloya işaret eden yeni bir Azure SQL veritabanı veri kümesi oluşturun ```SalesOrderHeader``` .

7. Üst kaynakta, "SourceOrderDetails" öğesinden sonra bir türetilmiş sütun dönüştürmesi ekleyin. Yeni "TypeCast" dönüşümünü çağırın. Sütunu yuvarlayıp ```UnitPrice``` CosmosDB için bir Double veri türüne atamalısınız. Formülü şu şekilde ayarlayın: ```toDouble(round(UnitPrice,2))``` .

8. Başka bir türetilmiş sütun ekleyin ve bunu "MakeStruct" olarak çağırın. Burada, Ayrıntılar tablosundan değerleri tutmak için hiyerarşik bir yapı oluşturacağız. Ayrıntılar, üst bilgiyle bir ```M:1``` ilişki olduğunu unutmayın. Yeni yapıyı adlandırın ```orderdetailsstruct``` ve hiyerarşiyi bu şekilde oluşturun, her bir alt sütunu gelen sütun adına ayarlar:

![Yapı Oluştur](media/data-flow/cosmosb9.png)

9. Şimdi Sales Header kaynağına gidelim. Bir JOIN dönüştürmesi ekleyin. Sağ tarafta "MakeStruct" öğesini seçin. İç birleşime ayarlı bırakın ve ```SalesOrderID``` birleşim koşulunun her iki tarafında da seçim yapın.

10. Sonuçlarınızı bu noktaya kadar görebileceğiniz şekilde, eklediğiniz yeni birleşimde veri önizleme sekmesine tıklayın. Ayrıntı satırlarıyla birleştirilmiş tüm başlık satırlarını görmeniz gerekir. Bu, öğesinden oluşturulan birleştirmenin sonucudur ```SalesOrderID``` . Daha sonra, ortak satırlardaki ayrıntıları Ayrıntılar yapısına birleştirecek ve ortak satırları toplayacağız.

![Katılın](media/data-flow/cosmosb4.png)

11. Bu satırları normalleştirmeye yönelik dizileri oluşturabilmeniz için önce istenmeyen sütunları kaldırmalı ve veri değerlerinin CosmosDB veri türleriyle eşleştiğinden emin olmanız gerekir.

12. Next bir SELECT dönüşümü ekleyin ve alan eşlemesini şuna benzer olacak şekilde ayarlayın:

![Sütun temizleyici](media/data-flow/cosmosb5.png)

13. Şimdi bir para birimi sütununu bu kez bir daha dönüştürmeye izin verlim ```TotalDue``` . Adım 7 ' de yaptığımız gibi, formülü şu şekilde ayarlayın: ```toDouble(round(TotalDue,2))``` .

14. Bu noktada, ortak anahtara göre gruplandırma yaparak satırları normalleştirirsiniz ```SalesOrderID``` . Bir toplama dönüştürmesi ekleyin ve grubunu olarak ayarlayın ```SalesOrderID``` .

15. Toplama formülünde, "details" adlı yeni bir sütun ekleyin ve daha önce oluşturduğumuz yapıda değerleri toplamak için bu formülü kullanın ```orderdetailsstruct``` : ```collect(orderdetailsstruct)``` .

16. Toplama dönüştürmesi yalnızca, formüllerin yalnızca toplama veya gruplama kapsamında yer alan sütunları çıktı. Bu nedenle, satış başlığından sütunları da dahil etmemiz gerekir. Bunu yapmak için, aynı toplama dönüştürmesinde bir sütun stili ekleyin. Bu model çıktıda diğer tüm sütunları içerir:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Aynı sütun adlarını korumak ve ```first()``` işlevi bir toplama olarak kullanmak için diğer özelliklerde "This" söz dizimini kullanın:

![Toplama](media/data-flow/cosmosb6.png)

18. Bir havuz dönüştürmesi ekleyerek geçiş akışını bitirebilmemiz için hazırız. DataSet ' in yanındaki "yeni" ye tıklayın ve CosmosDB veritabanınıza işaret eden CosmosDB veri kümesini ekleyin. Koleksiyon için, bunu "Orders" olarak çağıracağız ve bir şemaya sahip olmayacaktır ve bir belge anında oluşturulmayacak.

19. Havuz ayarları ' nda, Bölüm anahtarını ```\SalesOrderID``` ve toplama eylemini "yeniden oluştur" olarak oluşturun. Eşleme sekmesinin şu şekilde göründüğünden emin olun:

![Ekran görüntüsü eşleme sekmesini gösterir.](media/data-flow/cosmosb7.png)

20. Yeni kapsayıcıya yeni belge olarak eklemek üzere şu 32 satırları ayarladığınızdan emin olmak için veri önizleme ' ye tıklayın:

![Ekran görüntüsü, veri önizleme sekmesini gösterir.](media/data-flow/cosmosb8.png)

Her şey iyi görünüyorsa, artık yeni bir işlem hattı oluşturmaya hazır olursunuz, bu veri akışı etkinliğini bu işlem hattına ekleyin ve yürütün. Hata ayıklama veya tetiklenen bir çalıştırma aracılığıyla çalıştırabilirsiniz. Birkaç dakika sonra, CosmosDB veritabanınızda "Orders" adlı yeni bir eşit sipariş kapsayıcısına sahip olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* Veri akışı [dönüştürmelerini](concepts-data-flow-overview.md)eşleme kullanarak veri akışı mantığınızın geri kalanını oluşturun.
* Bu öğretici için [Tamamlanan işlem hattı şablonunu indirin](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) ve şablonu fabrikanıza aktarın.
