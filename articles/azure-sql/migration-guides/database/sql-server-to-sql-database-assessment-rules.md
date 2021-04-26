---
title: Azure SQL veritabanı geçişine SQL Server için değerlendirme kuralları
description: Azure SQL veritabanına geçmeden önce değinilmesi gereken kaynak SQL Server örneğiyle ilgili sorunları belirlemek için değerlendirme kuralları.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: c407b62b5f0308fbb6d9ff7223a3554ca09592db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027219"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>Azure SQL veritabanı geçişine SQL Server için değerlendirme kuralları
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Geçiş araçları, SQL Server veritabanınızı Azure SQL veritabanı 'na geçirmeden önce ele geçirilmesi gereken sorunları belirlemek için bir dizi değerlendirme kuralı çalıştırarak kaynak SQL Server örneğinizi doğrular. 

Bu makale, SQL Server veritabanınızı Azure SQL veritabanı 'na geçirme durumunu değerlendirmek için kullanılan kuralların bir listesini sağlar. 


## <a name="bulk-insert"></a>Toplu ekleme<a id="BulkInsert"></a>

**Başlık: Azure SQL veritabanı 'nda Azure olmayan BLOB veri kaynağıyla BULK INSERT desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
Azure SQL veritabanı, dosya paylaşımlarına veya Windows klasörlerine erişemez. Azure Blob 'a başvurmayan BULK INSERT deyimlerinin belirli kullanımları için "etkilenen nesneler" bölümüne bakın. Kaynağın Azure Blob depolama olmadığı ' BULK INSERT ' olan nesneler, Azure SQL veritabanı 'na geçtikten sonra çalışmaz. 


**Önerilen**   
Azure SQL veritabanı 'na geçiş yaparken bunun yerine, yerel dosyaları veya dosya paylaşımlarını kullanan BULK INSERT deyimlerini Azure Blob depolamadaki dosyaları kullanacak şekilde dönüştürmeniz gerekir. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

## <a name="compute-clause"></a>COMPUTE yan tümcesi<a id="ComputeClause"></a>

**Title: COMPUTE yan tümcesi durdurulmuş ve kaldırılmış.**   
**Kategori**: uyarı   

**Açıklaması**   
COMPUTE yan tümcesi, sonuç kümesinin sonunda ek Özet sütunları olarak görünen toplamlar oluşturur. Ancak, bu yan tümce artık Azure SQL veritabanı 'nda desteklenmiyor. 


**Önerilen**   
T-SQL modülünün yerine ROLLUP işleci kullanılarak yeniden yazılması gerekir. Aşağıdaki kod, Işlem TOPLAMASıNıN nasıl değiştirileceğini göstermektedir: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Değişiklik verilerini yakalama (CDC)<a id="CDC"></a>

**Başlık: değişiklik verilerini yakalama (CDC), Azure SQL veritabanı 'nda desteklenmiyor**   
**Kategori**: sorun   


**Açıklaması**   
Değişiklik verilerini yakalama (CDC), Azure SQL veritabanı 'nda desteklenmiyor. Bunun yerine Değişiklik İzleme kullanılacağını değerlendirin.  Alternatif olarak Azure sanal makinelerinde Azure SQL yönetilen örneği veya SQL Server geçirin. 


**Önerilen**   
Değişiklik verilerini yakalama (CDC), Azure SQL veritabanı 'nda desteklenmiyor. Bunun yerine Değişiklik İzleme kullanılacağını değerlendirin veya Azure SQL yönetilen örneği 'ne geçiş yapmayı deneyin.

Daha fazla bilgi: [Azure SQL değişiklik Izlemeyi etkinleştirme](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>CLR derlemeleri<a id="ClrAssemblies"></a>

**Başlık: SQL CLR derlemeleri Azure SQL veritabanı 'nda desteklenmiyor**   
**Kategori**: sorun   


**Açıklaması**   
Azure SQL veritabanı, SQL CLR derlemelerini desteklemez. 


**Önerilen**   
Şu anda Azure SQL veritabanı 'nda bunu gerçekleştirmenin bir yolu yoktur. Önerilen alternatif çözümler, uygulama kodu ve veritabanı değişikliklerinin yalnızca Azure SQL veritabanı tarafından desteklenen derlemeleri kullanmasını gerektirir. Alternatif olarak Azure sanal makinesinde Azure SQL yönetilen örneği veya SQL Server geçirme

Daha fazla bilgi: [SQL veritabanı 'Nda desteklenmeyen Transact-SQL farklılıkları](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Şifreleme sağlayıcısı<a id="CryptographicProvider"></a>

**Başlık: Azure SQL veritabanı 'nda desteklenmeyen, ŞIFRELEME sağlayıcısı oluşturma veya ALTER CRYPTOGRAPHIC PROVIDER 'ın kullanımı bulundu**   
**Kategori**: sorun   

**Açıklaması**   
Azure SQL veritabanı, dosyalara erişemediği için ŞIFRELEME sağlayıcısı deyimlerini desteklemez. ŞIFRELEME sağlayıcısı deyimlerinin belirli kullanımları için etkilenen nesneler bölümüne bakın. `CREATE CRYPTOGRAPHIC PROVIDER` `ALTER CRYPTOGRAPHIC PROVIDER` Azure SQL veritabanı 'na geçtikten sonra, veya içeren nesneler düzgün çalışmayacak.  


**Önerilen**   
Veya ile nesneleri gözden geçirin `CREATE CRYPTOGRAPHIC PROVIDER` `ALTER CRYPTOGRAPHIC PROVIDER` . Gereken bu tür nesnelerde, bu özelliklerin kullanımları kaldırılır. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın

## <a name="cross-database-references"></a>Çapraz veritabanı başvuruları<a id="CrossDataseReferences"></a>

**Başlık: Azure SQL veritabanında çapraz veritabanı sorguları desteklenmez**   
**Kategori**: sorun   

**Açıklaması**   
Bu sunucudaki veritabanları, Azure SQL veritabanı 'nda desteklenmeyen çapraz veritabanı sorguları kullanır. 


**Önerilen**   
Azure SQL veritabanı, veritabanları arası sorguları desteklemez. Aşağıdaki eylemler önerilir: 
- Bağımlı veritabanlarını Azure SQL veritabanına geçirin ve Azure SQL veritabanlarında sorgulamak için elastik veritabanı sorgusu (Şu anda önizleme aşamasında) kullanın. 
- Bağımlı veri kümelerini diğer veritabanlarından geçirilmekte olan veritabanına taşıyın. 
- Azure SQL yönetilen örneği 'ne geçiş yapın.
- Azure sanal makinesinde SQL Server ' ye geçirin. 

Daha fazla bilgi: [Azure SQL veritabanı elastik veritabanı sorgusuna bakın (Önizleme)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Veritabanı uyumluluğu<a id="DbCompatLevelLowerThan100"></a>

**Başlık: Azure SQL veritabanı 100 altındaki uyumluluk düzeylerini desteklemiyor.**   
**Kategori**: uyarı   

**Açıklaması**   
Veritabanı uyumluluk düzeyi, SQL Server veritabanı altyapısının yükseltilebilmesine izin vererek veritabanı modernleştirmesine yardımcı olmaya yönelik değerli bir araçtır. Bu arada, aynı yükseltme öncesi veritabanı uyumluluk düzeyini koruyarak uygulamaların işlevsel durumunun bağlanmasına olanak sağlar. Azure SQL veritabanı, 100 altındaki uyumluluk düzeylerini desteklemez. 


**Önerilen**   
Veritabanı uyumluluk düzeyi, Azure SQL yönetilen örneği üzerinde 100 sürümüne yükseltildiğinde, uygulama işlevselliğinin bozulmadan olduğunu değerlendirin. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın

## <a name="database-mail"></a>Veritabanı posta<a id="DatabaseMail"></a>

**Başlık: Veritabanı Postası Azure SQL veritabanı 'nda desteklenmiyor.**   
**Kategori**: uyarı   

**Açıklaması**   
Bu sunucu, Azure SQL veritabanı 'nda desteklenmeyen Veritabanı Postası özelliğini kullanıyor.


**Önerilen**   
Veritabanı Postası destekleyen Azure SQL yönetilen örneği 'ne geçiş yapmayı düşünün.  Alternatif olarak, Azure SQL veritabanı 'nda posta işlevselliği gerçekleştirmek için Azure işlevleri 'ni ve SendGrid 'i kullanmayı düşünün.

Daha fazla bilgi: Azure [işlevleri betiği kullanarak Azure SQL veritabanından e-posta gönderme](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Asıl veritabanı diğer adı<a id="DatabasePrincipalAlias"></a>

**Title: SYS. DATABASE_PRINCIPAL_ALIASES Discontinued ve kaldırılmıştır.**   
**Kategori**: sorun   

**Açıklaması**   
Dosyasında. DATABASE_PRINCIPAL_ALIASES kullanımdan kaldırılmıştır ve Azure SQL veritabanı 'nda kaldırılmıştır.  


**Önerilen**   
Diğer adlar yerine rolleri kullanın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK seçeneği<a id="DisableDefCNSTCHK"></a>

**Başlık: SET seçeneği DISABLE_DEF_CNST_CHK Discontinued ve kaldırılmıştır.**   
**Kategori**: sorun   

**Açıklaması**   
DISABLE_DEF_CNST_CHK SET seçeneği Discontinued ve Azure SQL veritabanı 'nda kaldırılmıştır.  


Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW İpucu<a id="FastFirstRowHint"></a>

**Başlık: FASTFIRSTROW sorgu ipucu durdurulmuş ve kaldırılmış.**   
**Kategori**: uyarı   

**Açıklaması**   
FASTFIRSTROW sorgu ipucu kullanımdan kaldırılmıştır ve Azure SQL veritabanı 'nda kaldırılmıştır.  


**Önerilen**   
FASTFIRSTROW sorgu ipucu kullan SEÇENEĞI (FAST n).

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Akışı<a id="FileStream"></a>

**Başlık: FILESTREAM Azure SQL veritabanı 'nda desteklenmiyor**   
**Kategori**: sorun   

**Açıklaması**   
NTFS dosya sisteminde metin belgeleri, Resimler ve videolar gibi yapılandırılmamış verileri depolamanıza olanak tanıyan FILESTREAM özelliği, Azure SQL veritabanı 'nda desteklenmez. 


**Önerilen**   
Yapılandırılmamış dosyaları Azure Blob depolama alanına yükleyin ve bu dosyalarla ilgili meta verileri (ad, tür, URL konumu, depolama anahtarı vb.) Azure SQL veritabanı 'nda depolayın. Azure SQL veritabanı 'na ve Azure SQL Database 'ten akış bloblarını etkinleştirmek için uygulamanıza yeniden mühendislik uygulamanız gerekebilir. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL bloguna ve Azure 'a blob 'Ları akışa](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/) alma


## <a name="linked-server"></a>Bağlantılı sunucu<a id="LinkedServer"></a>

**Başlık: bağlı sunucu işlevselliği Azure SQL veritabanı 'nda desteklenmiyor**   
**Kategori**: sorun   

**Açıklaması**   
Bağlı sunucular, SQL Server veritabanı altyapısının SQL Server örneğinin dışındaki OLE DB veri kaynaklarına karşı komutları yürütmesini sağlar. 


**Önerilen**   
Azure SQL veritabanı bağlı sunucu işlevselliğini desteklemez. Bağlı sunucular gereksinimini ortadan kaldırmak için aşağıdaki eylemler önerilir: 
- Uzak SQL sunucularından bağımlı veri kümelerini tanımlayabilir ve bunları geçirilmekte olan veritabanına taşımayı düşünün. 
- Bağımlı veritabanlarını Azure 'a geçirin ve Azure SQL veritabanı 'nda veritabanları arasında sorgulama yapmak için elastik veritabanı sorgusu (Önizleme) işlevini kullanın. 

Daha fazla bilgi: [Azure SQL veritabanı elastik sorgusuna bakın (Önizleme)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Başlık: Azure SQL veritabanı 'nda DAĞıTıM IŞLEMINI Başlat desteklenmez.**   
**Kategori**: sorun   

**Açıklaması**   
Microsoft Dağıtılmış İşlem Düzenleyicisi (MS DTC) tarafından desteklenen ve Azure SQL veritabanı 'nda Transact SQL tarafından başlatılan dağıtılmış işlem.  


**Önerilen**   
Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümü, Başlat ' a kadar tüm nesneleri görmek için kullanın. Katılımcı veritabanlarını birden çok örnekteki dağıtılmış işlemlerin desteklendiği Azure SQL yönetilen örneği 'ne geçirmeyi düşünün (Şu anda önizleme aşamasındadır). Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [Azure SQL yönetilen örneği için birden çok sunucu arasında işlem ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (toplu)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Başlık: Azure SQL veritabanı 'nda Azure olmayan BLOB depolama veri kaynağıyla toplu işlemde kullanılan OpenRowSet desteklenmez.**   
**Kategori**: sorun   

**Açıklama** OPENROWSET, bir dosyadaki verilerin bir satır kümesi olarak okunmasını ve döndürülmesini sağlayan yerleşik bir toplu sağlayıcı aracılığıyla toplu işlemleri destekler. Azure olmayan BLOB depolama veri kaynağı olan OPENROWSET, Azure SQL veritabanı 'nda desteklenmez.


**Önerilen**   
Azure SQL veritabanı dosya paylaşımlarına ve Windows klasörlerine erişemez, bu nedenle dosyalar Azure Blob depolama alanından içeri aktarılmalıdır. Bu nedenle, OPENROWSET işlevinde yalnızca blob türü VERI kaynağı desteklenir. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın

Daha fazla bilgi: [SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözme](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (sağlayıcı)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Başlık: SQL veya SQL sağlayıcısı olmayan OpenRowSet, Azure SQL veritabanı 'nda desteklenmiyor.**   
**Kategori**: sorun   

**Açıklaması**   
SQL veya SQL sağlayıcı olmayan OpenRowSet, bağlantılı bir sunucudaki tablolara erişmenin bir alternatifidir ve OLE DB kullanarak uzak verilere bağlanmak ve bunlara erişmek için tek seferlik, geçici bir yöntemdir. Azure SQL veritabanı 'nda SQL veya SQL sağlayıcı olmayan OpenRowSet desteklenmez.


**Önerilen**   
Azure SQL veritabanı, yalnızca Azure Blob depolamadan içeri aktarmak için OPENROWSET 'yi destekler. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın

Daha fazla bilgi: [SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözme](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>ANSI olmayan sol dış birleşim<a id="NonANSILeftOuterJoinSyntax"></a>

**Başlık: ANSI olmayan stil sol dış birleşim durdurulmuş ve kaldırılmıştır.**   
**Kategori**: uyarı   

**Açıklaması**   
ANSI olmayan stil sol dış birleşim kullanımdan kaldırılmıştır ve Azure SQL veritabanı 'nda kaldırılmıştır. 


**Önerilen**   
ANSI JOIN söz dizimini kullanın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>ANSI olmayan sağ dış birleşim<a id="NonANSIRightOuterJoinSyntax"></a>

**Başlık: ANSI olmayan stil sağ dış birleşim durdurulmuş ve kaldırılmıştır.**   
**Kategori**: uyarı   

**Açıklaması**   
ANSI olmayan stil sağ dış birleşim kullanımdan kaldırılmıştır ve Azure SQL veritabanı 'nda kaldırılmıştır. 


**Önerilen**   
ANSI JOIN söz dizimini kullanın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Sonraki sütun<a id="NextColumn"></a>

**Başlık: ILERI adlı tablolar ve sütunlar, Azure SQL veritabanında bir hataya yol açabilir.**   
**Kategori**: sorun   

**Açıklaması**   
NEXT adlı tablolar veya sütunlar algılandı. Microsoft SQL Server ' de tanıtılan diziler, ANSI standart NEXT VALUE FOR işlevini kullanın. Bir tablo veya sütun daha sonra adlandırılmış ise ve sütun değer olarak diğer ad ise ve ANSI standardı atlanırsa, sonuçta elde edilen ifade hataya yol açabilir.  


**Önerilen**   
Bir tablo veya sütunu diğer ad yaparken ANSI standardı AS anahtar sözcüğünü dahil etmek için deyimlerini yeniden yazın. Örneğin, bir sütunun bir sonrakı adı olduğunda ve bu sütun değer olarak diğer ad olduğunda, sorgu `SELECT NEXT VALUE FROM TABLE` bir hataya yol açacaktır ve TABLODAKI değer olarak Next ' i seçerek yeniden yazılması gerekir. Benzer şekilde, bir tablo NEXT olarak adlandırılmışsa ve bu tablo değer olarak diğer ad olduğunda, sorgu `SELECT Col1 FROM NEXT VALUE` bir hataya neden olur ve olarak yeniden yazılması gerekir `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Başlık: eski stil RAERROR çağrıları modern eşdeğerleriyle değiştirilmelidir.**   
**Kategori**: uyarı   

**Açıklaması**   
Aşağıdaki örnek gibi RAERROR çağrıları, virgül ve parantez içermediği için eski stil olarak adlandırılır. `RAISERROR 50001 'this is a test'`. Bu RAERROR çağırma yöntemi, Azure SQL veritabanı 'nda kullanımdan kaldırılmıştır ve kaldırılır. 


**Önerilen**   
Geçerli RAERROR söz dizimini kullanarak ifadeyi yeniden yazın ya da modern yaklaşımın uygulanabilir olup olmadığını değerlendirin `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` .

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Sunucu denetimleri<a id="ServerAudits"></a>

**Başlık: sunucu denetimlerini değiştirmek için Azure SQL veritabanı denetim özelliklerini kullanma**   
**Kategori**: uyarı   

**Açıklaması**   
Sunucu denetimleri Azure SQL veritabanı 'nda desteklenmiyor.


**Önerilen**   
Sunucu denetimlerini değiştirmek için Azure SQL veritabanı denetim özelliklerini göz önünde bulundurun.  Azure SQL, denetimi destekler ve özellikler SQL Server daha hızlıdır. Azure SQL veritabanı; verilere erişim, şema değişiklikleri (DDL), veri değişiklikleri (DML), hesaplar, roller ve izinler (DCL, güvenlik özel durumları dahil olmak üzere çeşitli veritabanı eylemlerini ve olaylarını denetleyebilir. Azure SQL veritabanı denetimi bir kuruluşun, veritabanında yapılan güncelleştirmeler ve sorgular da dahil olmak üzere veritabanları içinde gerçekleşen olaylar ve değişiklikler hakkında derin Öngörüler elde etme yeteneğini artırır. Alternatif olarak Azure sanal makinesinde Azure SQL yönetilen örneği 'ne veya SQL Server geçiş yapın.

Daha fazla bilgi: [Azure SQL veritabanı Için denetim ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Sunucu kimlik bilgileri<a id="ServerCredentials"></a>

**Başlık: sunucu kapsamlı kimlik bilgileri Azure SQL veritabanı 'nda desteklenmiyor**   
**Kategori**: uyarı   

**Açıklaması**   
Kimlik bilgileri, SQL Server dışında bir kaynağa bağlanmak için gereken kimlik doğrulama bilgilerini (kimlik bilgileri) içeren bir kayıttır. Azure SQL veritabanı, SQL Server kapsamında oluşturulmuş olanları değil, veritabanı kimlik bilgilerini destekler.   


**Önerilen**   
Azure SQL veritabanı, veritabanı kapsamlı kimlik bilgilerini destekler. Sunucu kapsamlı kimlik bilgilerini veritabanı kapsamlı kimlik bilgilerine dönüştürün. Alternatif olarak Azure sanal makinesinde Azure SQL yönetilen örneği veya SQL Server geçirme

Daha fazla bilgi: [veritabanı kapsamlı kimlik bilgileri oluşturma](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Hizmet Aracısı<a id="ServiceBroker"></a>

**Başlık: Hizmet Aracısı Özellik Azure SQL veritabanı 'nda desteklenmiyor**   
**Kategori**: sorun   

**Açıklaması**   
SQL Server Hizmet Aracısı, SQL Server veritabanı altyapısındaki mesajlaşma ve kuyruğa alma uygulamaları için yerel destek sağlar. Hizmet Aracısı özelliği Azure SQL veritabanı 'nda desteklenmiyor.


**Önerilen**   
Hizmet Aracısı özelliği Azure SQL veritabanı 'nda desteklenmiyor. Aynı örnek içinde hizmet Aracısı 'nı destekleyen Azure SQL yönetilen örneği 'ne geçiş yapmayı düşünün. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın. 

## <a name="server-scoped-triggers"></a>Sunucu kapsamlı tetikleyiciler<a id="ServerScopedTriggers"></a>

**Başlık: sunucu kapsamlı tetikleyici Azure SQL veritabanı 'nda desteklenmiyor**   
**Kategori**: uyarı   

**Açıklaması**   
Tetikleyici, verileri ekleme, silme veya güncelleştirme gibi bir tablodaki belirli bir eyleme yanıt olarak yürütülen özel bir tür saklı yordamdır. Sunucu kapsamlı tetikleyiciler Azure SQL veritabanı 'nda desteklenmez. Azure SQL veritabanı Tetikleyiciler için aşağıdaki seçenekleri desteklemez: oturum açma, ŞIFRELEME, çoğaltma IÇIN DEĞIL, dış ad seçeneği (dış yöntem desteği yoktur), tüm sunucu seçeneği (DDL tetikleyicisi), oturum açma olayında tetikleyici (oturum açma tetikleyicisi), Azure SQL veritabanı CLR-tetikleyicilerini desteklemez.


**Önerilen**   
Bunun yerine veritabanı düzeyi tetikleyicisini kullanın. Alternatif olarak Azure sanal makinesinde Azure SQL yönetilen örneği veya SQL Server geçirme

Daha fazla bilgi: [SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözme](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL Aracısı işleri<a id="AgentJobs"></a>

**Başlık: SQL Server Agent işleri Azure SQL veritabanı 'nda kullanılamaz**   
**Kategori**: uyarı   

**Açıklaması**   
SQL Server Agent, SQL Server iş olarak adlandırılan Zamanlanmış yönetim görevlerini yürüten bir Microsoft Windows hizmetidir. SQL Server Agent işleri Azure SQL veritabanı 'nda kullanılamıyor. 


**Öneri** Azure SQL veritabanı 'nda SQL Server Agent işlerin yerini alacak elastik işler (Önizleme) kullanın. Azure SQL veritabanı için Elastik Veritabanı işleri, otomatik olarak yeniden denenirken ve nihai tamamlanma garantisi sağlarken birden çok veritabanına yayılan T-SQL betikleri güvenilir bir şekilde yürütmelerine olanak tanır. Alternatif olarak Azure sanal makineler 'de Azure SQL yönetilen örneği 'ne veya SQL Server geçirmeyi düşünün.

Daha fazla bilgi: [elastik veritabanı işleri kullanmaya başlama (Önizleme) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL veritabanı boyutu<a id="SQLDBDatabaseSize"></a>

**Başlık: Azure SQL veritabanı, 100 TB 'den büyük veritabanı boyutunu desteklemez.**   
**Kategori**: sorun   

**Açıklaması**   
Veritabanının boyutu, desteklenen en büyük boyut olan 100 TB 'den büyük. 


**Önerilen**   
Verilerin arşivlenip arşivlenmeyeceğini veya sıkıştırılmış ya da birden çok veritabanına parçalar halinde olduğunu değerlendirin. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın. 

Daha fazla bilgi: [sanal çekirdek kaynak limitleri](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL posta<a id="SqlMail"></a>

**Başlık: SQL Mail kullanımdan kaldırılmıştır.**   
**Kategori**: uyarı   

**Açıklaması**   
SQL Mail kullanımdan kaldırılmıştır ve Azure SQL veritabanı 'nda kaldırılmıştır.


**Önerilen**   
Azure sanal makineler 'de Azure SQL yönetilen örneği 'ne veya SQL Server geçirmeyi düşünün ve Veritabanı Postası kullanın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: Azure SQL veritabanı 'nda kullanılamayan, kaldırılmış sistem saklı yordamlarına başvuran deyimler algılandı.**   
**Kategori**: uyarı   

**Açıklaması**   
Azure SQL veritabanı 'nda,,,,,, desteklenmeyen sistem ve genişletilmiş saklı yordamlar şu şekilde kullanılamaz `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` .


**Önerilen**    
Azure SQL veritabanı 'nda kaldırılmış olan desteklenmeyen sistem yordamlarına yönelik başvuruları kaldırın.

Daha fazla bilgi: [SQL Server 'de kullanımdan kaldırılmıştır veritabanı altyapısı işlevleri](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>İzleme bayrakları<a id="TraceFlags"></a>

**Başlık: Azure SQL veritabanı izleme bayraklarını desteklemiyor**   
**Kategori**: uyarı   

**Açıklaması**   
İzleme bayrakları, belirli sunucu özelliklerini geçici olarak ayarlamak veya belirli bir davranışı değiştirmek için kullanılır. İzleme bayrakları, performans sorunlarını tanılamak veya saklı yordamlarda ya da karmaşık bilgisayar sistemlerinde hata ayıklamak için sık sık kullanılır. Azure SQL veritabanı izleme bayraklarını desteklemiyor. 


**Önerilen**   
Azure SQL veritabanı 'nda desteklenmeyen tüm izleme bayraklarını görmek ve kaldırılıp kaldırılamadığını değerlendirmek için Azure geçişi 'ndeki etkilenen nesneleri inceleyin bölümü. Alternatif olarak, Azure sanal makinesinde sınırlı sayıda küresel izleme bayrağını veya SQL Server destekleyen Azure SQL yönetilen örneği 'ne geçiş yapın.

Daha fazla bilgi: [SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözme](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows kimlik doğrulaması<a id="WindowsAuthentication"></a>

**Başlık: Windows kimlik doğrulaması (tümleşik güvenlik) ile eşlenen veritabanı kullanıcıları Azure SQL veritabanı 'nda desteklenmez.**   
**Kategori**: uyarı   

**Açıklaması**   
Azure SQL veritabanı iki tür kimlik doğrulamasını destekler 
- SQL kimlik doğrulaması: Kullanıcı adı ve parola kullanır 
- Azure Active Directory kimlik doğrulaması: Azure Active Directory tarafından yönetilen kimlikleri kullanır ve yönetilen ve tümleşik etki alanları için desteklenir. 

Windows kimlik doğrulaması (tümleşik güvenlik) ile eşlenen veritabanı kullanıcıları Azure SQL veritabanı 'nda desteklenmez. 



**Önerilen**   
Yerel Active Directory Azure Active Directory ile federasyona edin. Daha sonra Windows kimliği eşdeğer Azure Active Directory kimliklerle değiştirilebilir. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın.

Daha fazla bilgi: [SQL veritabanı güvenlik özellikleri](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Başlık: xp_cmdshell Azure SQL veritabanı 'nda desteklenmiyor.**   
**Kategori**: sorun   

**Açıklaması**   
bir Windows komut kabuğu 'nun ve yürütme için bir dizede geçen xp_cmdshell, Azure SQL veritabanı 'nda desteklenmez. 


**Önerilen**   
Xp_cmdshell kullanarak tüm nesneleri görmek ve xp_cmdshell veya etkilenen nesnenin kaldırılabileceği takdirde değerlendirmek için Azure geçişi 'ndeki etkilenen nesneleri gözden geçirin bölümü. Ayrıca, bulut tabanlı Otomasyon ve yapılandırma hizmeti sunan Azure Otomasyonu 'Nu araştırmayı göz önünde bulundurun. Alternatif olarak, Azure sanal makinesinde SQL Server 'e geçiş yapın. 

## <a name="next-steps"></a>Sonraki adımlar

SQL Server Azure SQL veritabanı 'na geçirmeye başlamak için [SQL Server SQL veritabanı geçiş kılavuzuna](sql-server-to-sql-database-guide.md)bakın.

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerin yanı sıra size yardımcı olmak için sunulan Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).

- SQL veritabanı hakkında daha fazla bilgi için bkz.
   - [Azure SQL veritabanı 'na genel bakış](../../database/sql-database-paas-overview.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 

- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [İş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler Azure 'a geçiş](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Veri erişim katmanını bir/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).
