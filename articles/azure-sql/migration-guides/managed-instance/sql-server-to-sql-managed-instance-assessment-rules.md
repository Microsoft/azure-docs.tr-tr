---
title: Azure SQL yönetilen örnek geçişine SQL Server için değerlendirme kuralları
description: Azure SQL yönetilen örneğine geçmeden önce değinilmesi gereken kaynak SQL Server örneğiyle ilgili sorunları belirlemek için değerlendirme kuralları.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: fc8959d44fbacd90916a045d23db4bee872c4670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026045"
---
# <a name="assessment-rules-for-sql-server-to--azure-sql-managed-instance-migration"></a>Azure SQL yönetilen örnek geçişine SQL Server için değerlendirme kuralları
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Geçiş araçları, SQL Server veritabanınızı Azure SQL yönetilen örneği 'ne geçirmeden önce ele geçirilmesi gereken sorunları belirlemek için, kaynak SQL Server örneğinizi bir dizi değerlendirme kuralı çalıştırarak doğrular. 

Bu makale, SQL Server veritabanınızı Azure SQL yönetilen örneği 'ne geçirme durumunu değerlendirmek için kullanılan kuralların bir listesini sağlar. 

## <a name="analysiscommand-job"></a>AnalysisCommand işi<a id="AnalysisCommandJob"></a>

**Başlık: AnalysisCommand işi adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.**   
**Kategori**: uyarı   


**Açıklaması**   
Bir Analysis Services komutu çalıştıran bir iş adımıdır. AnalysisCommand işi adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.

**Önerilen**     
Analysis Service komut iş adımını kullanarak tüm işleri görmek ve iş adımının veya etkilenen nesnenin kaldırılıp kaldırılamadığını değerlendirmek için Azure geçişi 'ndeki etkilenen nesneleri inceleyin bölümü. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneğindeki SQL Server Agent farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery işi<a id="AnalysisQueryJob"></a>

**Başlık: AnalysisQuery iş adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.**   
**Kategori**: uyarı   

**Açıklaması**   
Analysis Services bir sorgu çalıştıran bir iş adımıdır. AnalysisQuery iş adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.


**Önerilen**   
Analysis Service sorgu işi adımını kullanarak tüm işleri görmek ve iş adımının ya da etkilenen nesnenin kaldırılıp kaldırılamadığını değerlendirmek için Azure geçişi 'ndeki etkilenen nesneleri inceleyin bölümü. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneğindeki SQL Server Agent farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Dosyadan derleme<a id="AssemblyFromFile"></a>

**Başlık: ' derleme oluşturma ' ve ' ALTER ASSEMBLY ' bir dosya parametresiyle birlikte Azure SQL yönetilen örneği 'nde desteklenmiyor.**   
**Kategori**: uyarı   

**Açıklaması**   
Azure SQL yönetilen örneği dosya paylaşımlarına veya Windows klasörlerine erişemez. Azure Blob 'a başvurmayan BULK INSERT deyimlerinin belirli kullanımları için "etkilenen nesneler" bölümüne bakın. Kaynağın Azure Blob depolama olmadığı ' BULK INSERT ' olan nesneler, Azure SQL yönetilen örneği 'ne geçtikten sonra çalışmayacaktır.


**Önerilen**   
Azure SQL yönetilen örneği 'ne geçiş yaparken bunun yerine, yerel dosyaları veya dosya paylaşımlarını kullanan BULK INSERT deyimlerini Azure Blob depolamadaki dosyaları kullanacak şekilde dönüştürmeniz gerekir. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın. 

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde clr farkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Toplu ekleme<a id="BulkInsert"></a>

**Başlık: Azure SQL yönetilen örneği 'nde Azure olmayan BLOB veri kaynağıyla BULK INSERT desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
Azure SQL yönetilen örneği dosya paylaşımlarına veya Windows klasörlerine erişemez. Azure Blob 'a başvurmayan BULK INSERT deyimlerinin belirli kullanımları için "etkilenen nesneler" bölümüne bakın. Kaynağın Azure Blob depolama olmadığı ' BULK INSERT ' olan nesneler, Azure SQL yönetilen örneği 'ne geçtikten sonra çalışmayacaktır.


**Önerilen**   
Azure SQL yönetilen örneği 'ne geçiş yaparken bunun yerine, yerel dosyaları veya dosya paylaşımlarını kullanan BULK INSERT deyimlerini Azure Blob depolamadaki dosyaları kullanacak şekilde dönüştürmeniz gerekir.

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde toplu ekleme ve openrowset farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR güvenliği<a id="ClrStrictSecurity"></a>

**Başlık: GÜVENLI veya EXTERNAL_ACCESS olarak işaretlenen CLR derlemeleri GÜVENLI DEĞIL olarak kabul edilir**   
**Kategori**: sorun   

**Açıklaması**   
CLR sıkı güvenlik modu, Azure SQL yönetilen örneği 'nde zorlanır. Bu mod varsayılan olarak etkindir ve GÜVENLI veya EXTERNAL_ACCESS olarak işaretlenmiş Kullanıcı tanımlı CLR derlemelerini içeren veritabanları için en son değişiklikleri sunar.


**Önerilen**   
CLR, artık güvenlik sınırı olarak desteklenmeyen .NET Framework, kod erişim güvenliği (CAS) kullanır. SQL Server 2017 (14. x) veritabanı altyapısından itibaren `sp_configure` clr derlemelerinin güvenliğini geliştirmek için clr Strict Security adlı bir seçenek sunulmuştur. Clr sıkı güvenliği varsayılan olarak etkindir ve GÜVENLI ve EXTERNAL_ACCESS CLR derlemelerini güvenli olmayan olarak işaretlenmiş gibi davranır. Clr sıkı güvenliği devre dışı bırakıldığında, PERMISSION_SET = SAFE ile oluşturulan bir CLR derlemesi, dış sistem kaynaklarına erişebilir, yönetilmeyen kod çağırabilir ve sysadmin ayrıcalıkları alabilir. Katı güvenlik etkinleştirildikten sonra, imzasız tüm derlemeler yüklenemez. Ayrıca, bir veritabanında GÜVENLI veya EXTERNAL_ACCESS derlemeler varsa, RESTORE veya ATTACH DATABASE deyimleri tamamlanabilir, ancak derlemeler yüklenmeyebilir. Derlemeleri yüklemek için, her bir derlemeyi değiştirmeniz veya bırakıp yeniden oluşturmanız gerekir, böylece sunucu üzerinde güvenli olmayan derleme iznine sahip bir sertifika veya asimetrik anahtarla imzalanmalıdır.

Daha fazla bilgi: [clr sıkı güvenliği](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>COMPUTE yan tümcesi<a id="ComputeClause"></a>

**Title: COMPUTE yan tümcesi durdurulmuş ve kaldırılmış.**   
**Kategori**: uyarı   

**Açıklaması**   
COMPUTE yan tümcesi, sonuç kümesinin sonunda ek Özet sütunları olarak görünen toplamlar oluşturur. Ancak, bu yan tümce artık Azure SQL yönetilen örneği 'nde desteklenmiyor.



**Önerilen**   
T-SQL modülünün yerine ROLLUP işleci kullanılarak yeniden yazılması gerekir. Aşağıdaki kod, Işlem TOPLAMASıNıN nasıl değiştirileceğini göstermektedir: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Şifreleme sağlayıcısı<a id="CryptographicProvider"></a>

**Başlık: Azure SQL yönetilen örneği 'nde desteklenmeyen, ŞIFRELEME sağlayıcısı oluşturma veya ALTER CRYPTOGRAPHIC PROVIDER 'ın kullanımı bulundu.**   
**Kategori**: sorun   

**Açıklaması**   
Azure SQL yönetilen örneği, dosyalara erişemediği için ŞIFRELEME sağlayıcısı deyimlerini desteklemez. ŞIFRELEME sağlayıcısı deyimlerinin belirli kullanımları için etkilenen nesneler bölümüne bakın. ' ŞIFRELEME sağlayıcısı oluşturma ' veya ' ALTER CRYPTOGRAPHIC PROVIDER ' içeren nesneler, Azure SQL yönetilen örneği 'ne geçtikten sonra düzgün çalışmayacaktır.


**Önerilen**   
' ŞIFRELEME sağlayıcısı oluşturma ' veya ' ALTER CRYPTOGRAPHIC PROVIDER ' ile nesneleri gözden geçirin. Gereken bu tür nesnelerde, bu özelliklerin kullanımları kaldırılır. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın. 

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde şifreleme sağlayıcısı farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Veritabanı uyumluluğu<a id="DbCompatLevelLowerThan100"></a>

**Başlık: 100 altındaki veritabanı uyumluluk düzeyi desteklenmiyor**   
**Kategori**: uyarı   

**Açıklaması**   
Veritabanı uyumluluk düzeyi, SQL Server veritabanı altyapısının yükseltilebilmesine izin vererek veritabanı modernleştirmesine yardımcı olmaya yönelik değerli bir araçtır. Bu arada, aynı yükseltme öncesi veritabanı uyumluluk düzeyini koruyarak uygulamaların işlevsel durumunun bağlanmasına olanak sağlar. Azure SQL yönetilen örneği 100 altındaki uyumluluk düzeylerini desteklemez. 100 altındaki uyumluluk düzeyine sahip veritabanı Azure SQL yönetilen örneği 'nde geri yüklendiğinde, uyumluluk düzeyi 100 ' e yükseltilir. 


**Öneri**... Veritabanı uyumluluk düzeyi, Azure SQL yönetilen örneği üzerinde 100 sürümüne yükseltildiğinde, uygulama işlevselliğinin bozulmadan olduğunu değerlendirin. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde desteklenen uyumluluk düzeyleri ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Asıl veritabanı diğer adı<a id="DatabasePrincipalAlias"></a>

**Title: SYS. DATABASE_PRINCIPAL_ALIASES Discontinued ve kaldırılmıştır.**   
**Kategori**: sorun   

**Açıklaması**   
Dosyasında. DATABASE_PRINCIPAL_ALIASES kullanımdan kaldırılmıştır ve Azure SQL yönetilen örneği ' nde kaldırılmıştır.


**Önerilen**   
Diğer adlar yerine rolleri kullanın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK seçeneği<a id="DisableDefCNSTCHK"></a>

**Başlık: SET seçeneği DISABLE_DEF_CNST_CHK Discontinued ve kaldırılmıştır.**   
**Kategori**: sorun   

**Açıklaması**   
DISABLE_DEF_CNST_CHK SET seçeneği kullanımdan kaldırılmıştır ve Azure SQL yönetilen örneği 'nde kaldırılmıştır.


Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW İpucu<a id="FastFirstRowHint"></a>

**Başlık: FASTFIRSTROW sorgu ipucu durdurulmuş ve kaldırılmış.**   
**Kategori**: uyarı   

**Açıklaması**   
FASTFIRSTROW sorgu ipucu kullanımdan kaldırılmıştır ve Azure SQL yönetilen örneği ' nde kaldırılmıştır.


**Önerilen**   
FASTFIRSTROW sorgu ipucu kullan SEÇENEĞI (FAST n).

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Akışı<a id="FileStream"></a>

**Başlık: FILESTREAM ve FileTable, Azure SQL yönetilen örneği 'nde desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
NTFS dosya sisteminde metin belgeleri, Resimler ve videolar gibi yapılandırılmamış verileri depolamanıza olanak tanıyan FILESTREAM özelliği, Azure SQL yönetilen örneği 'nde desteklenmez. **FILESTREAM dosya gruplarını içeren yedekleme, Azure SQL yönetilen örneği 'ne geri yüklenemediğinden bu veritabanı geçirilemez.**


**Önerilen**   
Yapılandırılmamış dosyaları Azure Blob depolama alanına yükleyin ve bu dosyalarla ilgili meta verileri (ad, tür, URL konumu, depolama anahtarı vb.) Azure SQL yönetilen örneği 'nde depolayın. Azure SQL yönetilen örneğinden gelen ve olmayan akış bloblarını etkinleştirmek için uygulamanıza yeniden mühendislik uygulamanız gerekebilir. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [SQL Azure blogundan ve bu kaynaklardan akış](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/) blobu

## <a name="heterogeneous-ms-dtc"></a>Heterojen MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Başlık: Azure SQL yönetilen örneği 'nde SQL Server uzak sunucu olmayan dağıtılmış Işlem desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
Microsoft Dağıtılmış İşlem Düzenleyicisi (MS DTC) tarafından başlatılan dağıtılmış işlem, uzak sunucu SQL Server değilse Azure SQL yönetilen örneği 'nde desteklenmez. 


**Önerilen**   
Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümü, Başlat ' a kadar tüm nesneleri görmek için kullanın. Katılımcı veritabanlarını birden çok örnekteki dağıtılmış işlemlerin desteklendiği Azure SQL yönetilen örneği 'ne geçirmeyi düşünün (Şu anda önizleme aşamasındadır). Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneği için birden çok sunucu arasında işlem ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>Hogenou MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Başlık: Azure SQL yönetilen örneği için birden çok sunucu arasında dağıtılmış Işlem Başlat.**   
**Kategori**: sorun   

**Açıklaması**   
Azure SQL yönetilen örneği için birden çok sunucuda Microsoft Dağıtılmış İşlem Düzenleyicisi (MS DTC) tarafından desteklenen ve Transact SQL tarafından başlatılan dağıtılmış işlem.


**Önerilen**   
Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümü, Başlat ' a kadar tüm nesneleri görmek için kullanın. Katılımcı veritabanlarını birden çok örnekteki dağıtılmış işlemlerin desteklendiği Azure SQL yönetilen örneği 'ne geçirmeyi düşünün (Şu anda önizleme aşamasındadır). Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın. 

Daha fazla bilgi: [Azure SQL yönetilen örneği için birden çok sunucu arasında işlem](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Bağlı sunucu (SQL sağlayıcı olmayan)<a id="LinkedServerWithNonSQLProvider"></a>

**Başlık: SQL Server olmayan sağlayıcıyla bağlantılı sunucu, Azure SQL yönetilen örneği 'nde desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
Bağlı sunucular, SQL Server veritabanı altyapısının SQL Server örneğinin dışındaki OLE DB veri kaynaklarına karşı komutları yürütmesini sağlar. SQL Server olmayan sağlayıcıyla bağlantılı sunucu, Azure SQL yönetilen örneği 'nde desteklenmez. 


**Önerilen**   
Uzak sunucu sağlayıcısı Oracle, Sybase vb. gibi SQL Server değilse, Azure SQL yönetilen örneği bağlı sunucu işlevselliğini desteklemez. 

Bağlı sunucular gereksinimini ortadan kaldırmak için aşağıdaki eylemler önerilir: 
- Uzak SQL Server olmayan sunuculardan bağımlı veritabanlarını belirleyip bunları geçirilmekte olan veritabanına taşımayı düşünün. 
- Bağımlı veritabanlarını SQL yönetilen örneği, SQL veritabanı, Azure SYNAPSE SQL ve SQL Server örnekleri gibi desteklenen hedeflere geçirin. 
- Azure sanal makinesinde (SQL VM) Azure SQL yönetilen örneği ve SQL Server arasında bağlı sunucu oluşturmayı düşünün.  Ardından SQL VM 'den Oracle, Sybase vb. için bağlı sunucu oluşturun. Bu yaklaşım iki atlama içerir, ancak geçici geçici çözüm olarak kullanılabilir.  
- Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde bağlı sunucu farklılıkları](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Birleştirme işi<a id="MergeJob"></a>

**Başlık: birleştirme işi adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.**   
**Kategori**: uyarı   

**Açıklaması**   
Çoğaltma birleştirme aracısını etkinleştiren bir iş adımıdır. Çoğaltma Birleştirme Aracısı, abonelere veritabanı tablolarında tutulan ilk anlık görüntüyü uygulayan bir yardımcı programdır. Ayrıca, ilk anlık görüntü oluşturulduktan sonra yayımcıda gerçekleşen artımlı veri değişikliklerini birleştirir ve yapılandırdığınız kurallara göre veya oluşturduğunuz özel bir çözümleyici kullanarak çakışmaları uzlaştırır. Birleştirme işi adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.


**Önerilen**   
Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin Bölümü birleştirme işini kullanarak tüm işleri görüntüleyin ve iş adımının veya etkilenen nesnenin kaldırılıp kaldırılamadığını değerlendirin. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın

Daha fazla bilgi: [Azure SQL yönetilen örneğindeki SQL Server Agent farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>Mı veritabanı boyutu<a id="MIDatabaseSize<"></a>

**Başlık: Azure SQL yönetilen örneği 8 TB 'den büyük veritabanı boyutunu desteklemez.**   
**Kategori**: sorun   

**Açıklaması**   
Veritabanının boyutu, en büyük örnek ayrılmış depolama değerinden daha büyük. **Boyut izin verilen sınırı aştığından geçiş için bu veritabanı seçilemiyor.**


**Önerilen**   
Verilerin, sıkıştırılmış veya birden çok veritabanına parçalar halinde arşivlenip arşivlenmeyeceğini değerlendirin. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneğinin donanım oluşturma özellikleri ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Mı örnek boyutu<a id="MIInstanceSize<"></a>

**Başlık: Azure SQL yönetilen örneğindeki en büyük örnek depolama boyutu 8 TB 'den büyük olamaz.**   
**Kategori**: uyarı   

**Açıklaması**   
Tüm veritabanlarının boyutu, en büyük örnek ayrılmış depolama değerinden daha büyük.  


**Önerilen**   
Tüm veritabanlarının aynı örnekte bulunması gerekiyorsa, veritabanlarını farklı Azure SQL yönetilen örneklerine veya Azure sanal makinesine SQL Server geçirmeyi düşünün. 

Daha fazla bilgi: [Azure SQL yönetilen örneğinin donanım oluşturma özellikleri ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Birden çok günlük dosyası<a id="MultipleLogFiles<"></a>

**Başlık: Azure SQL yönetilen örneği birden çok günlük dosyasını desteklemez.**   
**Kategori**: sorun   

**Açıklaması**   
SQL Server, bir veritabanının birden çok dosyada oturum açmasını sağlar. Bu veritabanında Azure SQL yönetilen örneği 'nde desteklenmeyen birden çok günlük dosyası vardır. * * Yedekleme, Azure SQL yönetilen örneği üzerinde geri yüklenemediğinden bu veritabanı geçirilemez. 
**

**Önerilen**   
Azure SQL yönetilen örneği veritabanı başına yalnızca tek bir günlüğü destekler. Bu veritabanını Azure 'a geçirmeden önce günlük dosyalarından biri hariç tümünü silmeniz gerekir: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde desteklenmeyen veritabanı seçenekleri  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Sonraki sütun<a id="NextColumn"></a>

**Başlık: ILERI adlı tablolar ve sütunlar, Azure SQL yönetilen örneği 'Nde bir hataya yol açabilir.**   
**Kategori**: sorun   

**Açıklaması**   
NEXT adlı tablolar veya sütunlar algılandı. Microsoft SQL Server ' de tanıtılan diziler, ANSI standart NEXT VALUE FOR işlevini kullanın. Bir tablo veya sütun daha sonra adlandırılmış ise ve sütun değer olarak diğer ad ise ve ANSI standardı atlanırsa, sonuçta elde edilen ifade hataya yol açabilir.


**Önerilen**   
Bir tablo veya sütunu diğer ad yaparken ANSI standardı AS anahtar sözcüğünü dahil etmek için deyimlerini yeniden yazın. Örneğin, bir sütun bir sonrakı adı ve bu sütun değer olarak diğer ad olduğunda, TABLODAKI sonrakı DEĞERI Seç değeri bir hataya neden olur ve TABLODAKI değer olarak ILERI ' yi SEÇIN. Benzer şekilde, bir tablo NEXT olarak adlandırılmışsa ve bu tablonun DEĞERI olarak diğer adı varsa, sonrakı değerden Sütun1 'YI SEÇIN bir hataya neden olur ve sonrakı değerden Sütun1 'YI SEÇIN olarak yeniden yazılması gerekir.



## <a name="non-ansi-style-left-outer-join"></a>ANSI olmayan stil sol dış birleşim<a id="NonANSILeftOuterJoinSyntax"></a>

**Başlık: ANSI olmayan stil sol dış birleşim durdurulmuş ve kaldırılmıştır.**   
**Kategori**: uyarı   

**Açıklaması**   
ANSI olmayan stil sol dış birleşim kullanımdan kaldırılmıştır ve Azure SQL yönetilen örneği ' nde kaldırılmıştır. 


**Önerilen**   
ANSI JOIN söz dizimini kullanın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>ANSI olmayan stil sağ dış birleşim<a id="NonANSIRightOuterJoinSyntax"></a>

**Başlık: ANSI olmayan stil sağ dış birleşim durdurulmuş ve kaldırılmıştır.**   
**Kategori**: uyarı   

**Açıklaması**   
ANSI olmayan stil sağ dış birleşim kullanımdan kaldırılmıştır ve Azure SQL yönetilen örneği 'nde kaldırılmıştır. 



Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Önerilen**   
ANSI JOIN söz dizimini kullanın.

## <a name="databases-exceed-100"></a>Veritabanları 100 değerini aşıyor <a id="NumDbExceeds100"></a>

**Başlık: Azure SQL yönetilen örneği, örnek başına en fazla 100 veritabanını destekler.**   
**Kategori**: uyarı   

**Açıklaması**   
Örnek depolama boyutu sınırına ulaşılmadığı müddetçe Azure SQL yönetilen örneği 'nde desteklenen en fazla veritabanı sayısı 100 ' dir.



**Önerilen**   
Tüm veritabanlarının aynı örnekte bulunması gerekiyorsa, veritabanlarını farklı Azure SQL yönetilen örneklerine veya Azure sanal makinesine SQL Server geçirmeyi düşünün. 

Daha fazla bilgi: [Azure SQL yönetilen örnek kaynak limitleri ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (blob olmayan veri kaynağı)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Başlık: Azure SQL yönetilen örneği 'nde Azure olmayan BLOB depolama veri kaynağıyla toplu işlemde kullanılan OpenRowSet desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
OPENROWSET, bir dosyadaki verilerin bir satır kümesi olarak okunmasını ve döndürülmesini sağlayan yerleşik bir toplu sağlayıcı aracılığıyla toplu işlemleri destekler. Azure olmayan BLOB depolama veri kaynağı olan OPENROWSET, Azure SQL yönetilen örneği 'nde desteklenmez. 



**Önerilen**   
OPENROWSET işlevi, yalnızca SQL Server örneklerine (yönetilen, şirket içi veya sanal makinelerde) sorgu yürütmek için kullanılabilir. Sağlayıcı olarak yalnızca SQLNCLI, SQLNCLI11 ve SQLOLEDB değerleri desteklenir. Bu nedenle, öneri eylemi, uzak SQL Server dışı sunuculardan bağımlı veritabanlarını tanımlamak ve bunları geçirilmekte olan veritabanına taşımayı göz önünde bulundurmaktır. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde toplu ekleme ve openrowset farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (SQL sağlayıcı olmayan)<a id="OpenRowsetWithNonSQLProvider"></a>

**Başlık: SQL sağlayıcısı olmayan OpenRowSet, Azure SQL yönetilen örneği 'nde desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
Bu yöntem, bağlantılı bir sunucudaki tablolara erişmenin bir alternatifidir ve OLE DB kullanarak uzak verilere bağlanmak ve bunlara erişmek için tek seferlik, geçici bir yöntemdir. SQL sağlayıcısı olmayan OpenRowSet, Azure SQL yönetilen örneği 'nde desteklenmez. 



**Önerilen**   
OPENROWSET işlevi, yalnızca SQL Server örneklerine (yönetilen, şirket içi veya sanal makinelerde) sorgu yürütmek için kullanılabilir. Sağlayıcı olarak yalnızca SQLNCLI, SQLNCLI11 ve SQLOLEDB değerleri desteklenir. Bu nedenle, öneri eylemi, uzak SQL Server dışı sunuculardan bağımlı veritabanlarını tanımlamak ve bunları geçirilmekte olan veritabanına taşımayı göz önünde bulundurmaktır.

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde toplu ekleme ve openrowset farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell işi<a id="PowerShellJob"></a>

**Başlık: PowerShell işi adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.**   
**Kategori**: uyarı   

**Açıklaması**   
Bir PowerShell betiği çalıştıran bir iş adımıdır. PowerShell işi adımı Azure SQL yönetilen örneği 'nde desteklenmiyor. 



**Önerilen**   
Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümünde, PowerShell işi adımını kullanarak tüm işleri görüntüleyin ve iş adımının veya etkilenen nesnenin kaldırılıp kaldırılabileceği değerlendirilir.  Azure Automation 'ın kullanılabilir olup olmadığını değerlendirin. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın

Daha fazla bilgi: [Azure SQL yönetilen örneğindeki SQL Server Agent farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Sıra okuyucu işi<a id="QueueReaderJob"></a>

**Başlık: Azure SQL yönetilen örneği 'nde sıra okuyucu işi adımı desteklenmez.**   
**Kategori**: uyarı   

**Açıklaması**   
Çoğaltma Sıra Okuyucu Aracısı etkinleştiren bir iş adımıdır. Çoğaltma Sıra Okuyucu Aracısı, bir Microsoft SQL Server kuyruğunda veya Microsoft Ileti kuyruğunda depolanan iletileri okuyan bir yürütülebilir dosyadır ve ardından bu iletileri yayımcıya uygular. Sıra Okuyucu Aracısı, sıraya alınmış güncelleştirmeye izin veren Snapshot ve işlemsel yayınlar ile kullanılır. Kuyruk okuyucu işi adımı Azure SQL yönetilen örneği 'nde desteklenmiyor.


**Önerilen**   
Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümü, tüm işleri sıraya alma okuyucu iş adımını kullanarak görüntüleyin ve iş adımının veya etkilenen nesnenin kaldırılıp kaldırılamadığını değerlendirin. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneğindeki SQL Server Agent farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Başlık: eski stil RAERROR çağrıları modern eşdeğerleriyle değiştirilmelidir.**   
**Kategori**: uyarı   

**Açıklaması**   
Aşağıdaki örnek gibi RAERROR çağrıları, virgül ve parantez içermediği için eski stil olarak adlandırılır. RAERROR 50001 ' Bu bir sınamadır. Bu RAERROR çağırma yöntemi, Azure SQL yönetilen örneği 'nde kullanımdan kaldırılmıştır ve kaldırılır.



**Önerilen**   
Geçerli RAERROR söz dizimini kullanarak ifadeyi yeniden yazın ya da modern yaklaşımın uygulanabilir olup olmadığını değerlendirin `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` .

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Hizmet Aracısı<a id="ServiceBrokerWithNonLocalAddress"></a>

**Başlık: Hizmet Aracısı Özellik Azure SQL yönetilen örneği 'nde kısmen desteklenir.**   
**Kategori**: sorun   

**Açıklaması**   
SQL Server Hizmet Aracısı, SQL Server veritabanı altyapısındaki mesajlaşma ve kuyruğa alma uygulamaları için yerel destek sağlar. Bu veritabanında Azure SQL yönetilen örneği 'nde desteklenmeyen çoklu örnek Hizmet Aracısı etkinleştirilmiş. 


**Önerilen**   
Azure SQL yönetilen örneği, örneğin, adres yerel olmayan Hizmet Aracısı 'nı desteklemez. Bu veritabanını Azure 'a geçirmeden önce aşağıdaki komutu kullanarak Hizmet Aracısı 'yi devre dışı bırakmanız gerekir: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Ayrıca, iletilerin SQL örneğine konmasını engellemek için Hizmet Aracısı uç noktasını kaldırmanız veya durdurmanız gerekebilir. Veritabanı Azure 'a geçirildikten sonra, Hizmet Aracısı yerine genel, bulut tabanlı bir mesajlaşma sistemi uygulamak için Azure Service Bus işlevselliğe bakabilirsiniz. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın. 

Daha fazla bilgi: [Azure SQL yönetilen örneğindeki hizmet Aracısı farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL posta<a id="SqlMail"></a>

**Başlık: SQL Mail kullanımdan kaldırılmıştır.**   
**Kategori**: uyarı   


**Açıklaması**   
SQL Mail kullanımdan kaldırılmıştır ve Azure SQL yönetilen örneği 'nde kaldırılmıştır.



**Önerilen**   
Veritabanı Postası kullanın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: Azure SQL yönetilen örneği 'nde kullanılamayan, kaldırılmış sistem saklı yordamlarına başvuran deyimler algılandı.**   
**Kategori**: uyarı   

**Açıklaması**   
Desteklenmeyen sistem ve genişletilmiş saklı yordamlar, Azure SQL yönetilen örneği-,,,,, ve içinde kullanılamaz `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` . 




**Önerilen**   
Azure SQL yönetilen örneği 'nde kaldırılan desteklenmeyen sistem yordamlarına yönelik başvuruları kaldırın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL işi<a id="TransactSqlJob"></a>

**Başlık: TSQL Job adımı, Azure SQL yönetilen örneği 'nde desteklenmeyen komutları içerir**   
**Kategori**: uyarı   


**Açıklaması**   
Zamanlanan zamanda TSQL betikleri çalıştıran bir iş adımıdır. TSQL iş adımı, Azure SQL yönetilen örneği 'nde desteklenmeyen desteklenmeyen komutlar içeriyor.



**Önerilen**   
Azure SQL yönetilen örneği 'nde desteklenmeyen komutları içeren tüm işleri görmek ve iş adımının veya etkilenen nesnenin kaldırılıp kaldırılamediğini değerlendirmek için Azure geçişi 'ndeki etkilenen nesneleri inceleyin bölümü. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneğindeki SQL Server Agent farklılıkları ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>İzleme bayrakları<a id="TraceFlags"></a>

**Başlık: Azure SQL yönetilen örneği 'nde Izleme bayrakları desteklenmiyor**   
**Kategori**: uyarı   


**Açıklaması**   
Azure SQL yönetilen örneği yalnızca sınırlı sayıda genel izleme bayrağını destekler. Oturum izleme bayrakları desteklenmez.



**Önerilen**   
Azure SQL yönetilen örneği 'nde desteklenmeyen tüm izleme bayraklarını görmek ve kaldırılabilecekleri değerlendirmesi yapmak için Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümü. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [izleme bayrakları](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows kimlik doğrulaması<a id="WindowsAuthentication"></a>

**Başlık: Windows kimlik doğrulamasıyla eşlenen veritabanı kullanıcıları (tümleşik güvenlik), Azure SQL yönetilen örneği 'nde desteklenmez**   
**Kategori**: uyarı   


**Açıklaması**   
Azure SQL yönetilen örneği iki tür kimlik doğrulamasını destekler: 
- SQL Kimlik Doğrulaması: Kullanıcı adı ve parola kullanır
- Azure Active Directory tarafından yönetilen ve yönetilen ve tümleşik etki alanları için desteklenen kimlikleri kullanan Azure Active Directory kimlik doğrulaması. 

Windows kimlik doğrulaması (tümleşik güvenlik) ile eşlenen veritabanı kullanıcıları Azure SQL yönetilen örneği 'nde desteklenmez. 


**Önerilen**   
Yerel Active Directory Azure Active Directory ile federasyona edin. Daha sonra Windows kimliği eşdeğer Azure Active Directory kimliklerle değiştirilebilir. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [SQL yönetilen örnek güvenlik özellikleri](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Başlık: xp_cmdshell Azure SQL yönetilen örneği 'nde desteklenmez.**   
**Kategori**: sorun   


**Açıklaması**   
Bir Windows komut kabuğu 'nun ve yürütme için bir dizede geçen Xp_cmdshell, Azure SQL yönetilen örneği 'nde desteklenmez. 



**Önerilen**   
Xp_cmdshell kullanarak tüm nesneleri görmek ve xp_cmdshell veya etkilenen nesnenin kaldırılabileceği takdirde değerlendirmek için Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümü. Bulut tabanlı Otomasyon ve yapılandırma hizmeti sunan Azure Otomasyonu 'Nu araştırmayı düşünün. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneği 'Nde saklı yordam farklılıkları](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Sonraki adımlar

SQL Server Azure SQL yönetilen örneği 'ne geçirmeye başlamak için, [SQL Server SQL yönetilen örnek geçiş kılavuzuna](sql-server-to-managed-instance-guide.md)bakın.

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerin yanı sıra size yardımcı olmak için sunulan Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).

- Azure SQL yönetilen örneği hakkında daha fazla bilgi için bkz.:
   - [Azure SQL yönetilen örneği 'nde hizmet katmanları](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server ile Azure SQL yönetilen örneği arasındaki farklar](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 


- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [İş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler Azure 'a geçiş](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Veri erişim katmanını bir/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).