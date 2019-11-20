---
title: Genişletilmiş etkinlikler
description: Azure SQL veritabanı 'nda genişletilmiş olayları (XEvents) ve olay oturumlarının Microsoft SQL Server olay oturumlarından biraz farklı bir şekilde nasıl farklı olduğunu açıklar.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cab5b5baf318eb9eadc398ce525e0de716d0df2d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822292"
---
# <a name="extended-events-in-sql-database"></a>SQL veritabanında genişletilmiş olaylar
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Bu konuda, Azure SQL veritabanı 'nda genişletilmiş olayların uygulanma Microsoft SQL Server, genişletilmiş olaylara kıyasla biraz farklı olduğu açıklanmaktadır.

- SQL Veritabanı V12, 2015 takviminin ikinci yarısında genişletilmiş olaylar özelliğini kazandı.
- SQL Server, 2008 ' den beri genişletilmiş olaylara sahipti.
- SQL veritabanı 'nda genişletilmiş olayların özellik kümesi, SQL Server özelliklerinin sağlam bir alt kümesidir.

*XEvents* , bazen bloglardaki ' genişletilmiş olaylar ' ve diğer resmi olmayan konumlarda kullanılan, resmi olmayan bir takma konumdadır.

Azure SQL veritabanı ve Microsoft SQL Server için genişletilmiş olaylar hakkında ek bilgiler şurada bulunabilir:

- [Hızlı başlangıç: SQL Server genişletilmiş olaylar](https://msdn.microsoft.com/library/mt733217.aspx)
- [Genişletilmiş Olaylar](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Ön koşullar

Bu konu başlığı altında zaten bir bilginiz olduğunu varsaymaktadır:

- [Azure SQL veritabanı hizmeti](https://azure.microsoft.com/services/sql-database/).
- Microsoft SQL Server [genişletilmiş olaylar](https://msdn.microsoft.com/library/bb630282.aspx) .

- Genişletilmiş olaylar hakkındaki belgelerimizin toplu bilgileri hem SQL Server hem de SQL veritabanı için geçerlidir.

Aşağıdaki öğelerin önceki pozlaması, olay dosyasını [hedef](#AzureXEventsTargets)olarak seçerken yararlı olur:

- [Azure depolama hizmeti](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Azure depolama ile Azure PowerShell kullanmak](../storage/common/storage-powershell-guide-full.md) -PowerShell ve Azure depolama hizmeti hakkında kapsamlı bilgiler sağlar.

## <a name="code-samples"></a>Kod örnekleri

İlgili konular iki kod örneği sağlar:


- [SQL veritabanı 'nda genişletilmiş olaylar için halka arabelleği hedef kodu](sql-database-xevent-code-ring-buffer.md)
    - Kısa basit Transact-SQL betiği.
    - Bir halka arabelleği hedefi ile işiniz bittiğinde, bir alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` deyimini yürüterek kaynaklarını serbest bırakmanız gerektiğini belirten kod örneği konusunu vurgularız. Daha sonra `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`tarafından başka bir halka arabelleği örneği ekleyebilirsiniz.


- [SQL veritabanı 'nda genişletilmiş olaylar için olay dosyası hedef kodu](sql-database-xevent-code-event-file.md)
    - Aşama 1, Azure depolama kapsayıcısı oluşturmak için PowerShell 'dir.
    - 2\. aşama, Azure Storage kapsayıcısını kullanan Transact-SQL ' dir.

## <a name="transact-sql-differences"></a>Transact-SQL farklılıkları


- SQL Server [olay oturumu oluştur](https://msdn.microsoft.com/library/bb677289.aspx) komutunu YÜRÜTTÜĞÜNÜZDE **on Server** yan tümcesini kullanırsınız. Ancak SQL veritabanı 'nda bunun yerine **on Database** yan tümcesini kullanın.


- **On Database** yan TÜMCESI [alter Event SESSION](https://msdn.microsoft.com/library/bb630368.aspx) ve [Drop Event Session](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL komutları için de geçerlidir.


- En iyi uygulama, **oluşturma olay** oturumunuz veya **alter Event session** DEYIMLERINIZIN **startup_state = üzerinde** olay oturumu seçeneğini içerkullanmaktır.
    - **= On** değeri, yük devretme nedeniyle mantıksal veritabanının yeniden yapılandırması sonrasında otomatik yeniden başlatmayı destekler.

## <a name="new-catalog-views"></a>Yeni Katalog görünümleri

Genişletilmiş olaylar özelliği, çeşitli [Katalog görünümleri](https://msdn.microsoft.com/library/ms174365.aspx)tarafından desteklenir. Katalog görünümleri, geçerli veritabanında kullanıcı tarafından oluşturulan olay oturumlarının *meta verilerini veya tanımlarını* size bildirir. Görünümler, etkin olay oturumlarının örnekleri hakkında bilgi döndürmez.

| Adı<br/>Katalog görünümü | Açıklama |
|:--- |:--- |
| **sys. database_event_session_actions** |Olay oturumunun her bir olayında her eylem için bir satır döndürür. |
| **sys. database_event_session_events** |Olay oturumundaki her olay için bir satır döndürür. |
| **sys. database_event_session_fields** |Olaylar ve hedeflerde açıkça ayarlanmış olan her özelleştirme özellikli sütun için bir satır döndürür. |
| **sys. database_event_session_targets** |Olay oturumu için her olay hedefi için bir satır döndürür. |
| **sys. database_event_sessions** |SQL veritabanı veritabanındaki her olay oturumu için bir satır döndürür. |

Microsoft SQL Server, benzer katalog görünümlerinde. *database\_* yerine *. Server\_* dahil olmak üzere adlar bulunur. Ad deseninin **sys. server_event_%** olduğu görülüyor.

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Yeni dinamik yönetim görünümleri [(DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL veritabanı, genişletilmiş olayları destekleyen [dinamik yönetim görünümlerine (DMVs)](https://msdn.microsoft.com/library/bb677293.aspx) sahiptir. DMVs, *etkin* olay oturumları hakkında bilgi ister.

| DMV adı | Açıklama |
|:--- |:--- |
| **sys. dm_xe_database_session_event_actions** |Olay oturumu eylemleri hakkında bilgi döndürür. |
| **sys. dm_xe_database_session_events** |Oturum olayları hakkında bilgi döndürür. |
| **sys. dm_xe_database_session_object_columns** |Bir oturuma bağlanan nesneler için yapılandırma değerlerini gösterir. |
| **sys. dm_xe_database_session_targets** |Oturum hedefleri hakkında bilgi döndürür. |
| **sys. dm_xe_database_sessions** |Geçerli veritabanı kapsamındaki her olay oturumu için bir satır döndürür. |

Microsoft SQL Server, benzer Katalog görünümleri adın *\_veritabanı* bölümü olmadan adlandırılır, örneğin:

- **sys. dm_xe_sessions**, ad yerine<br/>**sys. dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Her ikisine de genel olarak DMVs
Genişletilmiş olaylar için hem Azure SQL veritabanı hem de Microsoft SQL Server için ortak olan ek DMVs 'ler vardır:

- **sys. dm_xe_map_values**
- **sys. dm_xe_object_columns**
- **sys. dm_xe_objects**
- **sys. dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Kullanılabilir genişletilmiş olayları, eylemleri ve hedefleri bulun

Kullanılabilir olaylar, Eylemler ve hedefin bir listesini almak için basit bir SQL **seçimi** çalıştırabilirsiniz.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>SQL veritabanı olay oturumlarınız için hedefler

SQL veritabanında olay oturumlarınızdan sonuçları yakalayan hedefler şunlardır:

- [Halka arabelleği hedefi](https://msdn.microsoft.com/library/ff878182.aspx) -olay verilerini kısaca bellekte tutar.
- [Olay sayacı hedefi](https://msdn.microsoft.com/library/ff878025.aspx) -bir genişletilmiş olaylar oturumu sırasında oluşan tüm olayları sayar.
- [Olay dosyası hedefi](https://msdn.microsoft.com/library/ff878115.aspx) -bir Azure depolama kapsayıcısına tam arabellekleri yazar.

[Windows Için olay izleme (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API 'SI, SQL veritabanı 'ndaki genişletilmiş olaylar için kullanılamaz.

## <a name="restrictions"></a>Kısıtlamalar

SQL veritabanı 'nın bulut ortamına yer çıkaran güvenlikle ilgili birkaç fark vardır:

- Genişletilmiş olaylar, tek kiracılı yalıtım modelinde kurulmuştur. Bir veritabanındaki olay oturumu başka bir veritabanından veriye veya olaylara erişemez.
- **Ana** veritabanı BAĞLAMıNDA bir **olay oluştur oturum** bildirisi veremez.

## <a name="permission-model"></a>İzin modeli

**Olay oluştur oturumu oluşturmak** Için veritabanında **Denetim** izninizin olması gerekir. Veritabanı sahibi (dbo) **Denetim** iznine sahiptir.

### <a name="storage-container-authorizations"></a>Depolama kapsayıcısı yetkilendirmeleri

Azure depolama Kapsayıcınız için oluşturduğunuz SAS belirtecinin izinleri için **RwL** belirtmesi gerekir. **RwL** değeri aşağıdaki izinleri sağlar:

- Okuma
- Yazma
- Liste

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Genişletilmiş olayların yoğun kullanımı, genel sistem için sağlıklı olandan daha fazla etkin bellek biriktirilebileceği senaryolar vardır. Bu nedenle, Azure SQL veritabanı sistemi bir olay oturumu tarafından birikmiş etkin bellek miktarına göre sınırları dinamik olarak ayarlar ve ayarlar. Birçok etken dinamik hesaplamaya gider.

Bellek üst sınırının zorlandığını belirten bir hata iletisi alırsanız, gerçekleştirebileceğiniz bazı düzeltme işlemleri şunlardır:

- Daha az eşzamanlı olay oturumu çalıştırın.
- Olay oturumlarına yönelik **oluşturma** ve **değiştirme** deyimlerinizi kullanarak, **Maksimum\_bellek** yan tümcesinde belirttiğiniz bellek miktarını azaltın.

### <a name="network-latency"></a>Ağ gecikmesi

**Olay dosyası** hedefi, verileri Azure depolama Blobları ile kalıcı hale getirmeyi sağlarken ağ gecikme süresi veya hata yaşayabilir. SQL veritabanı 'ndaki diğer olaylar, ağ iletişiminin tamamlanmasını beklerken gecikebilir. Bu gecikme, iş yükünüzü yavaşlatabilir.

- Bu performans riskini azaltmak için, **EVENT_RETENTION_MODE** seçeneğini olay oturumu tanımlarınızda **NO_EVENT_LOSS** olarak ayarlamaktan kaçının.

## <a name="related-links"></a>İlgili bağlantılar

- [Azure depolama ile Azure PowerShell kullanma](../storage/common/storage-powershell-guide-full.md).
- [Azure depolama cmdlet 'Leri](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Azure depolama ile Azure PowerShell kullanmak](../storage/common/storage-powershell-guide-full.md) -PowerShell ve Azure depolama hizmeti hakkında kapsamlı bilgiler sağlar.
- [.NET 'ten blob depolamayı kullanma](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [Olay oturumu oluşturma (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Microsoft SQL Server 'da genişletilmiş olaylar hakkında Kehayias ' blog gönderileri](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure *hizmet güncelleştirmeleri* Web sayfası, parametre tarafından Azure SQL veritabanı 'na göre daha dar:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Genişletilmiş olaylara yönelik diğer kod örnek konuları aşağıdaki bağlantılarda bulunabilir. Ancak, örnek hedeflerin Azure SQL veritabanı 'na karşı Microsoft SQL Server için her bir örneğe düzenli olarak göz atın. Daha sonra örneği çalıştırmak için küçük değişiklikler gerekip gerekmediğine karar verebilirsiniz.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
