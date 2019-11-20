---
title: Azure veritabanı geçiş hizmeti ile ilişkili bilinen sorun giderme hakkında genel sorunlar/hatalar hakkında makale | Microsoft Docs
description: Azure veritabanı geçiş hizmeti 'ni kullanmayla ilgili yaygın olarak bilinen sorunların/hataların nasıl giderileceği hakkında bilgi edinin.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: a72a0e26c2af34942faaa6a7e62feef86082e196
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034822"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Yaygın Azure veritabanı geçiş hizmeti sorunlarını ve hatalarını giderme

Bu makalede, Azure veritabanı geçiş hizmeti kullanıcılarının üzerinde gelebilmesi için bazı yaygın sorunlar ve hatalar açıklanmaktadır. Makalede ayrıca bu sorunların ve hataların nasıl çözümleneceği hakkında bilgiler yer alır.

## <a name="migration-activity-in-queued-state"></a>Sıraya alınmış durumda geçiş etkinliği

Azure veritabanı geçiş hizmeti projesinde yeni etkinlikler oluşturduğunuzda, etkinlikler sıraya alınmış durumda kalır.

| Nedeni         | Çözüm |
| ------------- | ------------- |
| Bu sorun, Azure veritabanı geçiş hizmeti örneği eşzamanlı olarak çalışan devam eden görevler için maksimum kapasiteye eriştiğinde oluşur. Her yeni etkinlik, kapasite kullanılabilir olana kadar sıraya alınır. | Veri geçiş hizmeti örneğinin projeler arasında çalışan etkinlikleri olduğunu doğrulayın. Yürütülmek üzere otomatik olarak kuyruğa eklenen yeni etkinlikler oluşturmaya devam edebilirsiniz. Mevcut çalışan etkinliklerden herhangi biri tamamlandıktan hemen sonra, sıradaki sıradaki etkinlik çalışmaya başlar ve durum çalışma durumuna otomatik olarak değişir. Sıraya alınan etkinliğin geçişine başlamak için başka bir işlem yapmanız gerekmez.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Geçiş için seçilen en fazla veritabanı sayısı

Azure SQL veritabanı 'na veya Azure SQL veritabanı yönetilen örneğine geçmek üzere bir veritabanı geçiş projesi için etkinlik oluşturulurken aşağıdaki hata oluşur:

* **Hata**: Geçiş ayarları doğrulama hatası "," errorDetail ":" ' veritabanları ' taneden fazla sayıda ' 4 ' nesnesi geçiş için seçildi. "

| Nedeni         | Çözüm |
| ------------- | ------------- |
| Bu hata, tek bir geçiş etkinliği için dörtten fazla veritabanı seçtiğinizde görüntülenir. Varsa, her geçiş etkinliği dört veritabanı ile sınırlıdır. | Geçiş etkinliği başına dört veya daha az veritabanı seçin. Paralel olarak dörtten fazla veritabanı geçirmeniz gerekiyorsa, Azure veritabanı geçiş hizmeti 'nin başka bir örneğini sağlayın. Şu anda her abonelik en fazla iki Azure veritabanı geçiş hizmeti örneğini destekler.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Azure MySQL 'e kurtarma hatalarıyla yönelik MySQL geçişi hataları

Azure veritabanı geçiş hizmeti 'ni kullanarak MySQL 'ten MySQL için Azure veritabanı 'na geçiş yaptığınızda, geçiş etkinliği aşağıdaki hatayla başarısız olur:

* **Hata**: Veritabanı geçiş hatası-[n] Art arda kurtarma hataları nedeniyle ' TaskID ' görevi askıya alındı.

| Nedeni         | Çözüm |
| ------------- | ------------- |
| Bu hata, geçişi yapan kullanıcının ReplicationAdmin rolünü ve/veya çoğaltma ISTEMCISI, çoğaltma ÇOĞALTMASı ve süper (MySQL 5.6.6 'dan önceki sürümler) ayrıcalıklarına sahip olmadığı durumlarda meydana gelebilir.<br><br><br><br><br><br><br><br><br><br><br><br><br> | Kullanıcı hesabı için [Önkoşul ayrıcalıkların](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) , MySQL Için Azure veritabanı örneği üzerinde doğru şekilde yapılandırıldığından emin olun. Örneğin, aşağıdaki adımlar, gerekli ayrıcalıklara sahip ' migrateuser ' adlı bir kullanıcı oluşturmak için izlenebilir:<br>1. ' Gizli ' tarafından tanımlanan Kullanıcı migrateuser @ '% ' oluşturma; <br>2. DB_NAME. * ' de ' gizli ' tarafından tanımlanan ' migrateuser ' @ '% ' öğesine tüm ayrıcalıkların izin vermesini sağlar; daha fazla veritabanına erişim vermek için bu adımı tekrarlayın <br>3. Çoğaltmaya bağımlı izin verin *.* ' gizli ' ile tanımlanan ' migrateuser ' @ '% '<br>4. Çoğaltma istemcisine izin verin *.* ' gizli ' ile tanımlanan ' migrateuser ' @ '% '<br>5. Temizleme ayrıcalıkları; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti durdurulmaya çalışılırken hata oluştu

Azure veritabanı geçiş hizmeti örneğini durdururken şu hatayı alırsınız:

* **Hata**: Hizmet durdurulamadı. Hata: {'error':{'code':'InvalidRequest','message':'Şu anda bir veya birden fazla etkinlik çalışıyor. Hizmeti durdurmak için etkinlikler tamamlanana kadar bekleyin veya bu etkinlikleri el ile durdurun ve yeniden deneyin. '}}

| Nedeni         | Çözüm |
| ------------- | ------------- |
| Bu hata, durdurmaya çalıştığınız hizmet örneği, hala çalışmakta olan veya geçiş projelerinde bulunan etkinlikleri içerdiğinde görüntülenir. <br><br><br><br><br><br> | Durdurmaya çalıştığınız Azure veritabanı geçiş hizmeti örneğinde çalışan bir etkinlik bulunmadığından emin olun. Ayrıca, hizmeti durdurmayı denemeden önce etkinlikleri veya projeleri de silebilirsiniz. Aşağıdaki adımlarda, çalışan tüm görevleri silerek geçiş hizmeti örneğini temizlemek için projelerin nasıl kaldırılacağı gösterilmektedir:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-azurermsubscription-subscriptionName "\<subName >" <br> 4. Remove-azurermdatamigrationproject-adı \<ProjectName >-resourcegroupname \<RgName >-ServiceName \<HizmetAdı >-DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti başlatılmaya çalışılırken hata oluştu

Azure veritabanı geçiş hizmeti örneğini başlatırken aşağıdaki hatayı alıyorsunuz:

* **Hata**: Hizmet başlatılamıyor. Hata: {' errorDetail ': ' hizmet başlatılamadı, lütfen Microsoft desteği 'ne başvurun '}

| Nedeni         | Çözüm |
| ------------- | ------------- |
| Bu hata, önceki örnek dahili olarak başarısız olduğunda görüntülenir. Bu hata nadiren oluşur ve mühendislik ekibi bunun farkında değildir. <br> | Başlayacağınız hizmetin örneğini silin ve ardından yenisini koymak için yeni bir tane sağlayın. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>SQL Azure SQL VERITABANı yönetilen örneği 'ne geçirilirken veritabanı geri yüklenirken hata oluştu

Azure SQL veritabanı yönetilen örneğine SQL Server çevrimiçi geçiş gerçekleştirdiğinizde, tam geçişi aşağıdaki hatayla başarısız olur:

* **Hata**: ' OperationId ' işlem kimliği için geri yükleme Işlemi başarısız oldu. ' AuthorizationFailed ' kodu, ' ObjectID ' nesne kimliğine sahip ' ClientID ' adlı istemci, '/Subscriptions/' kapsamı üzerinde ' Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/Read ' eylemini gerçekleştirmek için yetkilendirmeye sahip değil SubscriptionID '. '.

| Nedeni         | Çözüm    |
| ------------- | ------------- |
| Bu hata, SQL Server 'den bir Azure SQL veritabanı yönetilen örneğine çevrimiçi geçiş için kullanılan uygulama sorumlusunun, abonelik üzerinde katkıda bulunan iznine sahip olmadığını gösterir. Mevcut olan yönetilen örnekle bazı API çağrıları geri yükleme işlemi için abonelikte bu izni gerektirir. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Kullanılan uygulama kimliğinin görünen adını `-ObjectId` listelemek için hata iletisinden kullanılabilir PowerShellcmdlet'inikullanın.`Get-AzureADServicePrincipal`<br><br> Bu uygulama için izinleri doğrulayın ve abonelik düzeyinde [katkıda bulunan rolüne](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) sahip olduğundan emin olun. <br><br> Azure veritabanı geçiş hizmeti mühendislik ekibi, abonelik üzerindeki geçerli katkıda bulunma rolünden gerekli erişimi kısıtlamak için çalışmaktadır. Katkıda bulunma rolünün kullanılmasına izin veren bir iş gereksinimiyle karşılaşırsanız, ek yardım için Azure desteğine başvurun. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti ile ilişkili NIC silinirken hata oluştu

Azure veritabanı geçiş hizmeti ile ilişkili bir ağ arabirimi kartını silmeye çalıştığınızda, silme girişimi şu hatayla başarısız olur:

* **Hata**: NIC 'yi kullanan DMS hizmeti nedeniyle Azure veritabanı geçiş hizmeti ile ilişkili NIC silinemiyor

| Nedeni         | Çözüm    |
| ------------- | ------------- |
| Bu sorun, Azure veritabanı geçiş hizmeti örneği hala mevcut olduğunda ve NIC 'yi tükediğinde oluşur. <br><br><br><br><br><br><br><br> | Bu NIC 'yi silmek için, hizmet tarafından kullanılan NIC 'yi otomatik olarak silen DMS hizmeti örneğini silin.<br><br> **Önemli**: Silinmekte olan Azure veritabanı geçiş hizmeti örneğinin çalışan etkinlikleri olmadığından emin olun.<br><br> Azure veritabanı geçiş hizmeti örneğiyle ilişkili tüm projeler ve Etkinlikler silindikten sonra, hizmet örneğini silebilirsiniz. Hizmet örneği tarafından kullanılan NIC, hizmet silme işleminin bir parçası olarak otomatik olarak temizlenir. |

## <a name="connection-error-when-using-expressroute"></a>ExpressRoute kullanılırken bağlantı hatası

Azure Veritabanı Geçiş Hizmeti proje sihirbazında kaynağa bağlanmayı denediğinizde, kaynak bağlantı için ExpressRoute kullanıyorsa uzun bir zaman aşımı süresinin ardından bağlantı başarısız olur.

| Nedeni         | Çözüm    |
| ------------- | ------------- |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/)kullanılırken, Azure veritabanı geçiş hizmeti, hizmetle Ilişkili sanal ağ alt ağında üç hizmet uç noktası sağlanması [gerekir](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) :<br> --Service Bus uç noktası<br> --Depolama uç noktası<br> --Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası)<br><br><br><br><br> | Kaynak ve Azure veritabanı geçiş hizmeti arasında ExpressRoute bağlantısı için gerekli hizmet uç noktalarını [etkinleştirin](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) . <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>MySQL için Azure DB 'ye bir MySQL veritabanını geçirirken kilit bekleme zaman aşımı hatası

Azure veritabanı geçiş hizmeti aracılığıyla bir MySQL veritabanını MySQL için Azure veritabanı örneğine geçirdiğinizde, geçiş aşağıdaki kilit bekleme zaman aşımı hatasıyla başarısız olur:

* **Hata**: Veritabanı geçiş hatası-dosya yüklenemedi-' n ' dosyası için yükleme işlemi başlatılamadı recode: SQL_ERROR SqlState: HY000 NativeError: 1205 iletisi: [MySQL] [ODBC sürücüsü] [mysqld] kilit bekleme zaman aşımı aşıldı; işlemi yeniden başlatmayı deneyin

| Nedeni         | Çözüm    |
| ------------- | ------------- |
| Geçiş sırasında kilit bekleme zaman aşımı nedeniyle geçiş başarısız olduğunda bu hata oluşur. | **' İnnodb_lock_wait_timeout '** sunucu parametresinin değerini artırmayı düşünün. İzin verilen en yüksek değer 1073741824 ' dir. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Dinamik bağlantı noktası veya adlandırılmış örnek kullanılırken kaynak SQL Server bağlanılırken hata oluştu

Azure veritabanı geçiş hizmeti 'ni adlandırılmış örnek veya dinamik bir bağlantı noktası üzerinde çalışan SQL Server kaynağına bağlamayı denediğinizde, bağlantı şu hatayla başarısız olur:

* **Hata**:-1-SQL bağlantısı başarısız oldu. SQL Server ile bağlantı kurulmaya çalışılırken ağ ile ilişkili veya örneğe özgü bir hata oluştu. Sunucu bulunamadı veya erişilebilir değildi. Örnek adının doğru olduğundan ve SQL Server uzak bağlantılara izin verecek şekilde yapılandırıldığından emin olun. sağlayıcısını SQL ağ arabirimleri, hata: 26-belirtilen sunucu/örnek bulunurken hata oluştu)

| Nedeni         | Çözüm    |
| ------------- | ------------- |
| Bu sorun, Azure veritabanı geçiş hizmeti 'nin bağlanmaya çalışacağı kaynak SQL Server örneğinin dinamik bir bağlantı noktasına sahip olması veya adlandırılmış bir örnek kullanılması durumunda meydana gelir. SQL Server Browser hizmeti, adlandırılmış bir örneğe gelen bağlantılar için veya dinamik bir bağlantı noktası kullanırken UDP bağlantı noktası 1434 ' i dinler. SQL Server hizmeti yeniden başlatıldığında dinamik bağlantı noktası değişebilir. SQL Server Yapılandırma Yöneticisi ağ yapılandırması aracılığıyla bir örneğe atanmış dinamik bağlantı noktasını kontrol edebilirsiniz.<br><br><br> |Azure veritabanı geçiş hizmeti 'nin UDP bağlantı noktası 1434 ' deki kaynak SQL Server Browser hizmetine ve SQL Server örneğine, uygun şekilde dinamik olarak atanmış TCP bağlantı noktası üzerinden bağlanıp bağlanamadiğini doğrulayın. |

## <a name="additional-known-issues"></a>Bilinen diğer sorunlar

* [Azure SQL veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [MySQL için Azure veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [PostgreSQL için Azure veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure veritabanı geçiş hizmeti PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)makalesini görüntüleyin.
* [Azure Portal kullanarak MySQL Için Azure veritabanı 'nda sunucu parametrelerini yapılandırma](https://docs.microsoft.com/azure/mysql/howto-server-parameters)makalesini görüntüleyin.
* [Azure veritabanı geçiş hizmeti 'ni kullanmaya yönelik önkoşullara genel bakış](https://docs.microsoft.com/azure/dms/pre-reqs)makalesini görüntüleyin.
* [Azure veritabanı geçiş hizmeti 'ni kullanma hakkında SSS](https://docs.microsoft.com/azure/dms/faq)bölümüne bakın.
