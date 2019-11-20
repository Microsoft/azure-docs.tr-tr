---
title: Tek kiracılı bir uygulamada şemayı yönetme
description: Azure SQL veritabanı 'nı kullanan tek kiracılı bir uygulamada birden fazla kiracı için şemayı yönetme
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: b6802d97b964b8863f6c2fce0cebfe16782b46fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822007"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Azure SQL veritabanı ile kiracı başına veritabanı düzenini kullanarak bir SaaS uygulamasında şemayı yönetme
 
Veritabanı uygulaması geliştikçe, veritabanı şemasında veya başvuru verilerinde kaçınılmaz değişiklikler yapılması gerekir.  Veritabanı bakım görevleri de düzenli aralıklarla gereklidir. Her kiracı için veritabanını kullanan bir uygulamayı yönetmek, bu değişiklikleri veya bakım görevlerini bir filin kiracı veritabanlarına uygulamanızı gerektirir.

Bu öğretici iki senaryoyu araştırır-tüm kiracılar için başvuru veri güncelleştirmelerini dağıtma ve başvuru verilerini içeren tablodaki bir dizini yeniden oluşturma. [Elastik işler](elastic-jobs-overview.md) özelliği, bu eylemleri tüm kiracı veritabanlarında ve yeni kiracı veritabanları oluşturmak için kullanılan şablon veritabanında yürütmek için kullanılır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> 
> * İş Aracısı oluşturma
> * T-SQL işlerinin tüm kiracı veritabanlarında çalışmasına neden oluyor
> * Tüm kiracı veritabanlarındaki başvuru verilerini Güncelleştir
> * Tüm kiracı veritabanlarında bir tabloda dizin oluşturma


Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Kiracı uygulaması başına Wingtip bilet SaaS veritabanı dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio’nun (SSMS) en son sürümünün yüklendiğinden. [SSMS’yi İndirin ve Yükleyin](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Bu öğretici, SQL veritabanı hizmetinin sınırlı önizlemede (Elastik Veritabanı işleri) olan özelliklerini kullanır. Bu öğreticiyi yapmak istiyorsanız, konu = elastik Işlerin önizlemesi SaaSFeedback@microsoft.com abonelik KIMLIĞINIZI belirtin. Aboneliğinizin etkinleştirildiğini belirten onayı aldıktan sonra, [en son ön sürüm işleri cmdlet’lerini indirip yükleyin](https://github.com/jaredmoo/azure-powershell/releases). Bu önizleme sınırlı olduğundan ilgili sorular veya destek için SaaSFeedback@microsoft.com başvurun.

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS şeması yönetim düzenlerine giriş

Kiracı başına veritabanı, kiracı verilerini etkili bir şekilde ayırır, ancak yönetilecek ve bakımını yapılacak veritabanlarının sayısını artırır. [Elastik Işler](elastic-jobs-overview.md) SQL veritabanlarının yönetimini ve yönetimini kolaylaştırır. İşler, bir veritabanı grubuna karşı güvenle ve güvenilir bir şekilde görevleri (T-SQL betikleri) çalıştırmanızı sağlar. İşler, bir uygulamadaki tüm kiracı veritabanlarında şema ve ortak başvuru veri değişiklikleri dağıtabilir. Elastik Işler Ayrıca yeni kiracılar oluşturmak için kullanılan bir *şablon* veritabanını korumak için kullanılabilir. Bu, her zaman en son şema ve başvuru verilerine sahip olmasını sağlar.

![ekran](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Esnek İşler sınırlı önizlemesi

Artık Azure SQL veritabanı 'nın tümleşik bir özelliği olan elastik Işlerin yeni bir sürümü var. Esnek İşler’in bu yeni sürümü, şu anda sınırlı önizlemeyle sunulmaktadır. Bu sınırlı önizleme Şu anda bir iş Aracısı oluşturmak için PowerShell kullanmayı destekler ve işleri oluşturmak ve yönetmek için T-SQL ' i destekler.

> [!NOTE]
> Bu öğretici, SQL veritabanı hizmetinin sınırlı önizlemede (Elastik Veritabanı işleri) olan özelliklerini kullanır. Bu öğreticiyi yapmak istiyorsanız, konu = elastik Işlerin önizlemesi SaaSFeedback@microsoft.com abonelik KIMLIĞINIZI belirtin. Aboneliğinizin etkinleştirildiğini belirten onayı aldıktan sonra, [en son ön sürüm işleri cmdlet’lerini indirip yükleyin](https://github.com/jaredmoo/azure-powershell/releases). Bu önizleme sınırlı olduğundan ilgili sorular veya destek için SaaSFeedback@microsoft.com başvurun.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Kiracı uygulama betikleri başına Wingtip bilet SaaS veritabanını alın

Uygulama kaynak kodu ve yönetim betikleri [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>İş Aracısı veritabanı ve yeni iş Aracısı oluşturma

Bu öğretici, bir iş Aracısı ve onun yedekleme iş Aracısı veritabanı oluşturmak için PowerShell kullanmanız gerekir. İş Aracısı veritabanı iş tanımlarını, iş durumunu ve geçmişi barındırır. İş Aracısı ve veritabanı oluşturulduktan sonra işleri hemen oluşturabilir ve izleyebilirsiniz.

1. **PowerShell ISE 'de**,...\\öğrenme modüllerini\\şema yönetimi\\*demo-SchemaManagement. ps1*.
1. Betiği çalıştırmak için **F5**'e basın.

*Demo-SchemaManagement. ps1* betiği, katalog sunucusunda *osagent* adlı bir SQL veritabanı oluşturmak için *Deploy-SchemaManagement. ps1* betiğini çağırır. Daha sonra bir parametre olarak veritabanını kullanarak iş aracısını oluşturur.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tüm kiracılara yeni başvuru verilerini dağıtmak için bir iş oluşturma

Wingtip bilet uygulamasında, her kiracı veritabanı desteklenen bir mekan türleri kümesi içerir. Her bir mekan, barındırılabilecek olayların türünü tanımlayan ve uygulamada kullanılan arka plan resmini belirleyen belirli bir mekan türüdür. Uygulamanın yeni olay türlerini desteklemesi için, bu başvuru verilerinin güncellenmesi ve yeni mekan türlerinin eklenmesi gerekir.  Bu alıştırmada, şu iki ek mekan türünü eklemek için tüm kiracı veritabanlarına yönelik bir güncelleştirmeyi dağıtacaksınız: *Motosiklet Yarışı* ve *Yüzme Kulübü*.

İlk olarak, her kiracı veritabanına dahil olan mekan türlerini gözden geçirin. SQL Server Management Studio (SSMS) içindeki kiracı veritabanlarından birine bağlanın ve Venuetype tablosunu inceleyin.  Bu tabloyu, veritabanı sayfasından erişilen Azure portal sorgu düzenleyicisinde da sorgulayabilirsiniz. 

1. SSMS 'yi açın ve kiracı sunucusuna bağlanın: *tenants1-DPT-&lt;user&gt;. Database.Windows.net*
1. *Otocycle yarış* ve *yüzme kulübünün* **Şu anda dahil** edilmediğini onaylamak için *tenants1-DPT-&lt;User&gt;* Server 'daki _contosoconcerthall_ veritabanına gidin ve *venuetype 'ı sorgulayın* tablo.

Şimdi yeni mekan türlerini eklemek için tüm kiracı veritabanlarındaki *Venuetype* tablosunu güncelleştirmek üzere bir iş oluşturalım.

Yeni bir iş oluşturmak için, iş aracısının oluşturulduğu sırada _JobAgent_ veritabanında oluşturulan iş kümesi sistem saklı yordamlarını kullanırsınız.

1. SSMS 'de katalog sunucusuna bağlanın: *Katalog-DPT-&lt;user&gt;. Database.Windows.net* Server 
1. SSMS 'de,...\\öğrenme modüllerini\\şema yönetimi\\DeployReferenceData. SQL dosyasını açın
1. İfadeyi değiştirin: SET @wtpUser = &lt;User&gt; ve kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtırken kullanılan Kullanıcı değerini yerine koy
1. _JobAgent_ veritabanına bağlandığınızdan emin olun ve betiği çalıştırmak için **F5** 'e basın

*Deployreferencedata. SQL* komut dosyasında aşağıdaki öğeleri gözlemleyin:
* **sp\_\_hedefi ekleme\_grubu** , hedef grup adı demoservergroup değerini oluşturur.
* **sp\_\_target\_grup ekleyin\_üye** , hedef veritabanlarının kümesini tanımlamak için kullanılır.  İlk olarak _tenants1-DPT-&lt;kullanıcı&gt;_ sunucusu eklenir.  Sunucuyu hedef olarak eklemek, bu sunucudaki veritabanlarının iş yürütme sırasında iş yürütmeye dahil edilmesini sağlar. Ardından _basetenantdb_ veritabanı ve *adhocretaşıma* veritabanı (sonraki bir öğreticide kullanılan) hedef olarak eklenir.
* **sp\_add\_Job** , _başvuru veri dağıtımı_adlı bir iş oluşturur.
* **sp\_add\_JobStep** , başvuru tablosu, Venuetype 'ı güncelleştirmek için t-SQL komut metnini içeren iş adımını oluşturur.
* Betikteki kalan görünümler, nesnelerin varlığını gösterir ve işin yürütülüşünü izler. İşin tüm hedef veritabanlarında ne zaman tamamlandığını öğrenmek için **yaşam döngüsü** sütunundaki durum değerini gözden geçirmek üzere bu sorguları kullanın.

Betik tamamlandıktan sonra, başvuru verilerinin güncelleştirildiğini doğrulayabilirsiniz.  SSMS 'de, *tenants1-DPT-&lt;user&gt;* Server 'daki *contosoconcerthall* veritabanına gidin ve *venuetype* tablosunu sorgulayın.  Artık, *Otocycle yarış* ve *yüzme kulübünün* mevcut olduğunu denetleyin.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Başvuru tablosu dizinini yönetmek için bir iş oluşturma

Bu alıştırma, başvuru tablosunun birincil anahtarındaki dizini yeniden derlemek için bir iş kullanır.  Bu, büyük miktarlarda veri yüklendikten sonra yapılabilecek tipik bir veritabanı bakım işlemidir.

Aynı iş “sistem” saklı yordamlarını kullanarak bir iş oluşturun.

1. SSMS 'yi açın ve _Katalog-DPT-&lt;user&gt;. Database.Windows.net_ Server 'a bağlanın
1. Dosya... _\\öğrenme modüllerini\\şema yönetimi\\Onlinereındex. SQL_ dosyasını açın
1. Sağ tıklayın, bağlantı ' yı seçin ve zaten bağlı değilse, _Katalog-DPT-&lt;user&gt;. Database.Windows.net_ Server 'a bağlanın
1. _JobAgent_ veritabanına bağlandığınızdan emin olun ve betiği çalıştırmak için **F5** 'e basın

_Onlinereındex. SQL_ betiğinin aşağıdaki öğelerini gözlemleyin:
* **sp\_add\_Job** , "çevrimiçi yeniden DIZIN oluşturma PK\_\_VenueTyp\_\_265E44FD7FD4C885" adlı yeni bir iş oluşturur
* **sp\_add\_JobStep** , dizini güncelleştirmek için t-SQL komut metnini içeren iş adımını oluşturur
* Betik izleyici işi yürütmesindeki kalan görünümler. İşin tüm hedef grup üyelerinde başarıyla tamamlandığını öğrenmek için **yaşam döngüsü** sütunundaki durum değerini gözden geçirmek üzere bu sorguları kullanın.



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunları öğrendiniz:

> [!div class="checklist"]
> 
> * T-SQL işleri birden çok veritabanı arasında çalışacak bir iş Aracısı oluşturma
> * Tüm kiracı veritabanlarındaki başvuru verilerini Güncelleştir
> * Tüm kiracı veritabanlarında bir tabloda dizin oluşturma

Ardından, kiracı veritabanlarında dağıtılmış sorguları çalıştırmayı araştırmak için geçici [Raporlama öğreticisini](saas-tenancy-cross-tenant-reporting.md) deneyin.


## <a name="additional-resources"></a>Ek kaynaklar

* [Kiracı uygulaması dağıtımı başına Wingtip bilet SaaS veritabanı üzerine inşa eden ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Ölçeği artırılmış bulut veritabanlarını yönetme](elastic-jobs-overview.md)