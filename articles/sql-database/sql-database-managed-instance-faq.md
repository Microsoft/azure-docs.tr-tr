---
title: Yönetilen örnekler hakkında SSS
description: SQL veritabanı yönetilen örnek hakkında sık sorulan sorular (SSS)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 34ffcd63879920c3c744deb4cf0b28684081dc25
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825742"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL veritabanı yönetilen örnek hakkında sık sorulan sorular (SSS)

Bu makale, [SQL veritabanı yönetilen örneği](sql-database-managed-instance.md)hakkında en yaygın soruların çoğunu içerir.

## <a name="where-can-i-find-a-list-of-features-supported-on-managed-instance"></a>Yönetilen örnekte desteklenen özelliklerin listesini nerede bulabilirim?

Yönetilen örnekteki desteklenen özelliklerin listesi için bkz. [Azure SQL veritabanı SQL Server karşılaştırması](sql-database-features.md).

Azure SQL veritabanı yönetilen örneği ve şirket içi SQL Server arasındaki sözdizimi ve davranıştaki farklar için, bkz. [T-SQL farkları SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Yönetilen örnek için teknik özellikleri ve kaynak sınırlarını nereden bulabilirim?

Kullanılabilir donanım oluşturma özellikleri için bkz. [donanım oluşumlarında teknik farklılıklar](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Kullanılabilir hizmet katmanları ve özellikleri için bkz. [hizmet katmanları arasındaki teknik farklılıklar](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Bilinen sorunları ve hataları nerede bulabilirim?

Hatalar ve bilinen sorunlar için bkz. [bilinen sorunlar](sql-database-managed-instance-transact-sql-information.md#Issues).

## <a name="where-can-i-find-latest-features-and-the-features-in-public-preview"></a>En son özellikleri ve özellikleri genel önizlemede nerede bulabilirim?

Yeni ve Önizleme özellikleri için bkz. [sürüm notları](/azure/sql-database/sql-database-release-notes?tabs=managed-instance).

## <a name="how-much-time-takes-to-create-or-update-instance-or-to-restore-a-database"></a>Örnek oluşturmak veya güncelleştirmek ya da bir veritabanını geri yüklemek için ne kadar süre sürer?

Yeni yönetilen örnek oluşturmak için beklenen süre veya hizmet katmanını değiştirme (Vçekirdekler, depolama) çeşitli etkenlere bağlıdır. [Yönetim işlemlerine](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) göz atın 

## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Yönetilen bir örnek şirket içi SQL Server aynı ada sahip olabilir mi?

Yönetilen örnek, *Database.Windows.net*ile biten bir ada sahip olmalıdır. Varsayılan yerine başka bir DNS bölgesi kullanmak için, örneğin, **mı-diğer-adı**. contoso.com: 
- Bir diğer ad tanımlamak için CliConfig 'i kullanın. Araç yalnızca bir kayıt defteri ayarları sarmalayıcısıdır ve bu nedenle Grup İlkesi veya betiği kullanılarak da yapılabilir.
- *TrustServerCertificate = true* seçeneğiyle *CNAME* kullanın.

## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Veritabanını yönetilen örnekten geri SQL Server veya Azure SQL veritabanı 'na nasıl taşıyabilirim?

[Veritabanını BACPAC 'e aktarabilir](sql-database-export.md) ve ardından [bacpac dosyasını içeri aktarabilirsiniz]( sql-database-import.md). Veritabanınız 100 GB 'den küçükse bu önerilen bir yaklaşımdır.

Veritabanındaki tüm tablolarda birincil anahtarlar varsa işlemsel çoğaltma kullanılabilir.

Yönetilen örnekten alınan yerel `COPY_ONLY` yedeklemeleri, SQL Server kıyasla daha yüksek bir veritabanı sürümüne sahip olduğundan SQL Server geri yüklenemez.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Örnek veritabanımı tek bir Azure SQL veritabanına nasıl geçirebilirim?

Bir seçenek, [veritabanını BIR BACPAC 'e aktarmak](sql-database-export.md) ve ardından [bacpac dosyasını içeri aktaryıdır](sql-database-import.md). 

Veritabanınız 100 GB 'den küçükse bu önerilen yaklaşımdır. Veritabanındaki tüm tablolarda birincil anahtarlar varsa işlemsel çoğaltma kullanılabilir.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Yönetilen örnek için gen 4 ve Gen 5 donanım oluşturma arasında seçim mi Nasıl yaparım??

Bazı donanım oluşturma, diğer iş yükü türleri için daha iyi olduğundan iş yükünüze bağımlıdır. Performans konusunun basitleşmesi tercih edilirken, iş yükü performansını etkileyen donanım nesilleri arasında aşağıdaki farklılıklar vardır:
- Gen 4, fiziksel işlemcilere göre daha iyi bir işlem desteği sağlar ve bu, vCore işlemcilere bağlı olan gen 5 ' tir. İşlem yoğunluğu yoğun iş yükleri için daha avantajlı olabilir.
- Gen 5, uzak depolama için daha iyi bir GÇ bant genişliğine neden olan hızlandırılmış ağı destekler. Genel Amaçlı hizmeti katmanlarında GÇ yoğun iş yükleri için avantajlı olabilir. Gen 5, gen 4 ' e kıyasla daha hızlı SSD yerel disklerini kullanır. İş açısından kritik hizmet katmanlarında GÇ yoğun iş yükleri için avantajlı olabilir.

Belirli bir durumda hangi donanım oluşturmanın daha iyi çalışacağını öğrenmek için üretime yönelik olarak tasarlanan gerçek iş yüklerinin performansını test etmek önemle önerilir.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Yönetilen örnek donanım oluşturma işlemi ile gen 4 ve Gen 5 arasında geçiş yapabilir miyim? 

Yönetilen örneğinizin sağlandığı bölgede her iki donanım nesli de kullanılabiliyorsa, donanım oluşturmaları arasında otomatik çevrimiçi geçiş mümkündür. Bu durumda, donanım oluşturma arasında nasıl geçiş yapılacağını açıklayan [blog gönderisine ait betiği](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Change-hardware-generation-on-Managed-Instance/ba-p/699824) kullanabilirsiniz.

Arka planda yeni bir yönetilen örnek sağlanacağı ve veritabanlarının sonunda hızlı bir yük devretmeyle, eski ve yeni örnek arasında otomatik olarak aktarılan, bu uzun süredir çalışan bir işlemdir. 

Aynı bölgede her iki donanım neslini de desteklenmiyorsa, donanım oluşturma işlemi değiştirilebilir, ancak el ile yapılması gerekir. Bu, istenen donanım oluşturma 'nın kullanılabildiği bölgede yeni bir örnek sağlamanızı ve eski ve yeni örnek arasında verileri el ile yedeklemeniz ve geri yüklemeniz gerekir.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Yönetilen örneðimin performansı Nasıl yaparım? mi? 

Genel Amaçlı yönetilen örnek, performans için önemli veri ve günlük dosyası boyutu nedeniyle uzak depolama kullanır. Daha fazla bilgi için bkz. [genel amaçlı yönetilen örnek performansı üzerinde günlük dosyası boyutunun etkisi](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Yoğun GÇ yoğunluklu iş yükleri için Gen 5 donanımı kullanmayı ve işlem yoğunluğu iş yükleri için gen 4 ' ü kullanmayı düşünün. Daha fazla bilgi için bkz. [Gen 4 ve Gen 5 arasında nasıl yaparım? seçme](#how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance).

İş yükünüz çok sayıda küçük işlem içeriyorsa, bağlantı türünü proxy 'den yeniden yönlendirme moduna geçirmeyi düşünün.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Yönetilen örnek için en büyük depolama boyutu nedir? 

Yönetilen örnek için depolama boyutu, seçilen hizmet katmanına (Genel Amaçlı veya İş Açısından Kritik) göre değişir. Bu hizmet katmanlarının depolama sınırlamaları için bkz. [hizmet katmanı özelliği](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Yedekleme deposu yönetilen örnek depolamama göre mi kesilsin? 

Hayır, yedekleme depolama alanı yönetilen örnek depolama alanından kesilmiyor. Yedekleme depolaması, örnek depolama alanından bağımsızdır ve bu boyut sınırlı değildir. Yedekleme depolaması, örnek veritabanlarınızın yedeğinin tutulacağı zaman dilimi ile sınırlıdır ve 7 ile 35 gün arasında yapılandırılabilir. Ayrıntılar için bkz. [otomatik yedeklemeler](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Yönetim bağlantı noktalarında gelen NSG kurallarını nasıl ayarlayabilirim?

Yerleşik güvenlik duvarı özelliği, yalnızca Microsoft Yönetim/dağıtım makineleri ile ilişkili IP aralıklarından gelen bağlantılara izin vermek için kümedeki tüm sanal makinelerde Windows Güvenlik Duvarı 'nı yapılandırır ve güvenli yönetim iş istasyonları etkin bir şekilde engelleniyor ağ katmanı üzerinden yetkisiz.

Hangi bağlantı noktalarının kullanıldığı aşağıda verilmiştir:

9000 ve 9003 bağlantı noktaları Service Fabric altyapısı tarafından kullanılır. Service Fabric birincil rol, sanal kümenin sağlıklı tutulması ve hedef durumunun Bileşen çoğaltmaları sayısı bakımından tutulmasını sağlamaktır.

1438, 1440 ve 1452 bağlantı noktaları düğüm Aracısı tarafından kullanılır. Düğüm Aracısı, küme içinde çalışan ve yönetim komutlarını yürütmek için denetim düzlemi tarafından kullanılan bir uygulamadır.

Ağ katmanındaki yerleşik güvenlik duvarına ek olarak, iletişim de sertifikalarla korunur.
  
Daha fazla bilgi ve yerleşik güvenlik duvarını doğrulama hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı yönetilen örneği yerleşik güvenlik duvarı](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Ağ risklerini nasıl azaltabilirim? 

Herhangi bir ağ riskini azaltmak için müşterilerin bir dizi güvenlik ayarı ve denetimi uygulaması önerilir:

- Tüm veritabanlarında [Saydam veri şifrelemesi (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) öğesini açın.
- Ortak dil çalışma zamanını (CLR) devre dışı bırakın. Bu, şirket içinde de önerilir.
- Yalnızca Azure Active Directory (AAD) kimlik doğrulaması kullanın.
- Düşük ayrıcalıklı DBA hesabıyla Access örneğine erişin.
- Sysadmin hesabı için JIT atlama kutusu erişimini yapılandırın.
- [SQL denetimini](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)açın ve uyarı mekanizmalarıyla tümleştirin.
- [Tehdit algılamayı](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) [Gelişmiş VERI güvenliği (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) paketinden açın.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Yönetilen örnekle birlikte kullanım durumlarını ve sonuçta elde edilen maliyet tasarrufunu nereden bulabilirim?

Yönetilen örnek örnek olay incelemeleri:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Azure SQL veritabanı yönetilen örneği dağıtmayla ilgili avantajları, maliyetleri ve riskleri daha iyi anlamak için, Forrester 'in bir incelemesi de vardır: [mı? un toplam ekonomik etkisi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>DNS yenileme yapabilir miyim? 
  
Şu anda yönetilen örnek için DNS sunucusu yapılandırmasını yenileme özelliği sağlamayız.

DNS yapılandırması sonunda yenilenir:

- DHCP kira süresi dolar.
- Platform yükseltmesi üzerinde.

Geçici bir çözüm olarak, yönetilen örneği 4 sanal çekirdekli bir sürüme indirip daha sonra tekrar yükseltin. Bunun, DNS yapılandırmasını yenilemeyi yan etkisi vardır.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Yönetilen bir örnek statik IP adresine sahip olabilir mi?

Nadir ancak gerekli durumlarda, yönetilen bir örnek için yeni bir sanal kümeye çevrimiçi geçiş yapmanız gerekebilir. Gerekirse, bu geçiş, teknoloji yığınımızda hizmetin güvenliğini ve güvenilirliğini artırmaya yönelik değişiklikler nedeniyle oluşur. Yeni bir sanal kümeye geçiş, yönetilen örnek ana bilgisayar adıyla eşlenen IP adresinin değiştirilmesine neden olur. Yönetilen örnek hizmeti statik IP adresi desteğini talep etmez ve normal bakım döngülerinin bir parçası olarak fark etmeden değiştirme hakkını saklı tutar.

Bu nedenle, IP adresinin gereksiz kapalı kalma süresine neden olabileceği için IP adresi 'nin dengeszlik düzeyini önemli bir şekilde ele geçirmesini kesinlikle önermiyoruz.

## <a name="can-i-move-a-managed-instance-or-its-vnet-to-another-resource-group"></a>Yönetilen bir örneği veya VNet 'i başka bir kaynak grubuna taşıyabilir miyim?

Hayır, bu geçerli platform kısıtlamasıdır. Yönetilen bir örnek oluşturulduktan sonra, yönetilen örneği veya VNet 'i başka bir kaynak grubuna veya aboneliğe taşımak desteklenmez.

## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Mevcut bir yönetilen örnek için saat dilimini değiştirebilir miyim?

Yönetilen bir örnek ilk kez sağlandığında saat dilimi yapılandırması ayarlanabilir. Mevcut yönetilen Örneğin saat dilimini değiştirme desteklenmiyor. Ayrıntılar için bkz. [saat dilimi sınırlamaları](sql-database-managed-instance-timezone.md#limitations).

Geçici çözümler, doğru saat dilimine sahip yeni bir yönetilen örnek oluşturmayı ve sonra el ile yedekleme ve geri yükleme işlemi gerçekleştirmeyi ya da önerdiğimiz bir süre sonra bir [çapraz örnek için geri yükleme](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)gerçekleştirmesini içerir.


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Yönetilen örneğimde performans sorunlarını çözmek Nasıl yaparım?

Yönetilen örnek ve SQL Server arasındaki bir performans karşılaştırması için iyi bir başlangıç noktası, [Azure SQL yönetilen örneği ve SQL Server makalesi arasında performans karşılaştırması Için en iyi](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) uygulamadır.

Veri yükleme, zorunlu tam kurtarma modeli ve işlem günlüğü yazma aktarım hızı [sınırları](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) nedeniyle SQL Server yönetilen örnekte genellikle daha yavaştır. Bazen bu, geçici verileri Kullanıcı veritabanı yerine tempdb 'ye yükleyerek veya kümelenmiş columnstore ya da bellek için iyileştirilmiş tablolar kullanılarak gerçekleştirilebilir.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Şifrelenmiş veritabanımı yönetilen örneğe geri yükleyebilir miyim?

Evet, yönetilen örneğe geri yükleyebilmek için veritabanınızın şifresini çözmeniz gerekmez. Şifrelenmiş yedekleme dosyasından veri okuyabilmeniz için, yönetilen örneğe kaynak sistemde şifreleme anahtarı koruyucusu olarak kullanılan bir sertifika/anahtar sağlamanız gerekir. Bunu yapmak için iki olası yol vardır:

- *Yönetilen örneğe sertifika koruyucuyu yükleyin*. Yalnızca PowerShell kullanılarak yapılabilir. [Örnek betik](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) , tüm süreci açıklar.
- *Asimetrik anahtar koruyucuyu Azure Key Vault (AKV) ve işaret ile yönetilen örneğe yükleyin*. Bu yaklaşım, şifreleme anahtarını depolamak için aynı zamanda AKV tümleştirmesini kullanan, kendi anahtarını getir (BYOK) TDE kullanım örneğine benzer. Anahtarı şifreleme anahtar koruyucusu olarak kullanmak istemiyorsanız ve yalnızca anahtarı yönetilen örneğin şifrelenmiş veritabanlarını geri yüklemek için kullanılabilir hale getirmek istiyorsanız, [BYOK TDE ayarlama](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)yönergelerini izleyin ve onay kutusunu *Seçili anahtarı oluştur onay kutusunu işaretlemeyin Varsayılan TDE koruyucusu*.

Şifreleme koruyucusunu yönetilen örnek için kullanılabilir hale getirildikten sonra, standart veritabanı geri yükleme yordamıyla devam edebilirsiniz.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Azure SQL veritabanı tekil veya elastik havuzdan yönetilen örneğe nasıl geçiş yapabilirim? 

Yönetilen örnek, Azure SQL veritabanı 'nın diğer dağıtım seçenekleri olarak işlem ve depolama boyutuna göre aynı performans düzeylerini sunmaktadır. Tek bir örnekteki verileri birleştirmek istiyorsanız veya yalnızca yönetilen örnekte yalnızca desteklenen bir özelliğe ihtiyacınız varsa, dışarı aktarma/içeri aktarma (BACPAC) işlevini kullanarak verilerinizi geçirebilirsiniz.
