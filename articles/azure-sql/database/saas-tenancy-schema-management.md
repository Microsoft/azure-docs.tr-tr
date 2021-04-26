---
title: Tek kiracılı bir uygulamada şemayı yönetme
description: Azure SQL veritabanı 'nı kullanan tek kiracılı bir uygulamada birden fazla kiracı için şemayı yönetme
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: e4328be0aade0658dedb034dbbb6980b810f771a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793203"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Azure SQL veritabanı ile kiracı başına veritabanı düzenini kullanarak bir SaaS uygulamasında şemayı yönetme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Veritabanı uygulaması geliştikçe, veritabanı şemasında veya başvuru verilerinde kaçınılmaz değişiklikler yapılması gerekir.  Veritabanı bakım görevleri de düzenli aralıklarla gereklidir. Her kiracı için veritabanını kullanan bir uygulamayı yönetmek, bu değişiklikleri veya bakım görevlerini bir filin kiracı veritabanlarına uygulamanızı gerektirir.

Bu öğretici iki senaryoyu araştırır-tüm kiracılar için başvuru veri güncelleştirmelerini dağıtma ve başvuru verilerini içeren tablodaki bir dizini yeniden oluşturma. [Elastik işler](./elastic-jobs-overview.md) özelliği, bu eylemleri tüm kiracı veritabanlarında ve yeni kiracı veritabanları oluşturmak için kullanılan şablon veritabanında yürütmek için kullanılır.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> 
> * İş Aracısı oluşturma
> * T-SQL işlerinin tüm kiracı veritabanlarında çalışmasına neden oluyor
> * Tüm kiracı veritabanlarındaki başvuru verilerini Güncelleştir
> * Tüm kiracı veritabanlarında bir tabloda dizin oluşturma


Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Kiracı uygulaması başına Wingtip bilet SaaS veritabanı dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtma ve araştırma](./saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](/powershell/azure/get-started-azureps)
* SQL Server Management Studio’nun (SSMS) en son sürümünün yüklendiğinden. [SSMS indirme ve yükleme](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS şeması yönetim düzenlerine giriş

Kiracı başına veritabanı, kiracı verilerini etkili bir şekilde ayırır, ancak yönetilecek ve bakımını yapılacak veritabanlarının sayısını artırır. [Elastik işler](./elastic-jobs-overview.md) , birden çok veritabanının yönetimini ve yönetimini kolaylaştırır. İşler, bir veritabanı grubuna karşı güvenle ve güvenilir bir şekilde görevleri (T-SQL betikleri) çalıştırmanızı sağlar. İşler, bir uygulamadaki tüm kiracı veritabanlarında şema ve ortak başvuru veri değişiklikleri dağıtabilir. Elastik Işler Ayrıca yeni kiracılar oluşturmak için kullanılan bir *şablon* veritabanını korumak için kullanılabilir. Bu, her zaman en son şema ve başvuru verilerine sahip olmasını sağlar.

![ekran](./media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-public-preview"></a>Elastik Işler genel önizleme

Artık Azure SQL veritabanı 'nın tümleşik bir özelliği olan elastik Işlerin yeni bir sürümü var. Elastik Işlerin bu yeni sürümü şu anda genel önizlemededir. Bu genel önizleme Şu anda iş Aracısı oluşturmak için PowerShell kullanmayı ve işleri oluşturmak ve yönetmek için T-SQL ' i desteklemektedir.
Daha fazla bilgi için bkz. [elastik veritabanı işleri](./elastic-jobs-overview.md) makalesi.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Kiracı uygulama betikleri başına Wingtip bilet SaaS veritabanını alın

Uygulama kaynak kodu ve yönetim betikleri [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>İş Aracısı veritabanı ve yeni iş Aracısı oluşturma

Bu öğretici, bir iş Aracısı ve onun yedekleme iş Aracısı veritabanı oluşturmak için PowerShell kullanmanız gerekir. İş Aracısı veritabanı iş tanımlarını, iş durumunu ve geçmişi barındırır. İş Aracısı ve veritabanı oluşturulduktan sonra işleri hemen oluşturabilir ve izleyebilirsiniz.

1. **PowerShell ISE 'de** açın... \\ Öğrenme modülleri \\ şema yönetimi \\ *Demo-SchemaManagement.ps1*.
1. Betiği çalıştırmak için **F5**'e basın.

*Demo-SchemaManagement.ps1* betiği, katalog sunucusunda *osagent* adlı bir veritabanı oluşturmak için *Deploy-SchemaManagement.ps1* betiğini çağırır. Daha sonra bir parametre olarak veritabanını kullanarak iş aracısını oluşturur.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tüm kiracılara yeni başvuru verilerini dağıtmak için bir iş oluşturma

Wingtip bilet uygulamasında, her kiracı veritabanı desteklenen bir mekan türleri kümesi içerir. Her bir mekan, barındırılabilecek olayların türünü tanımlayan ve uygulamada kullanılan arka plan resmini belirleyen belirli bir mekan türüdür. Uygulamanın yeni olay türlerini desteklemesi için, bu başvuru verilerinin güncellenmesi ve yeni mekan türlerinin eklenmesi gerekir.  Bu alıştırmada, şu iki ek mekan türünü eklemek için tüm kiracı veritabanlarına yönelik bir güncelleştirmeyi dağıtacaksınız: *Motosiklet Yarışı* ve *Yüzme Kulübü*.

İlk olarak, her kiracı veritabanına dahil olan mekan türlerini gözden geçirin. SQL Server Management Studio (SSMS) içindeki kiracı veritabanlarından birine bağlanın ve Venuetype tablosunu inceleyin.  Bu tabloyu, veritabanı sayfasından erişilen Azure portal sorgu düzenleyicisinde da sorgulayabilirsiniz. 

1. SSMS 'yi açın ve kiracı sunucusuna bağlanın: *tenants1-DPT- &lt; user &gt; . Database.Windows.net*
1. *Otocycle yarış* ve *yüzme kulübünün* **Şu anda** dahil edilmediğini onaylamak için *tenants1-DPT- &lt; user &gt;* sunucusundaki _contosoconcerthall_ veritabanına gidin ve *venuetype* tablosunu sorgulayın.

Şimdi yeni mekan türlerini eklemek için tüm kiracı veritabanlarındaki *Venuetype* tablosunu güncelleştirmek üzere bir iş oluşturalım.

Yeni bir iş oluşturmak için, iş aracısının oluşturulduğu sırada _JobAgent_ veritabanında oluşturulan iş kümesi sistem saklı yordamlarını kullanırsınız.

1. SSMS 'de katalog sunucusuna bağlanın: *Catalog-DPT- &lt; user &gt; . Database.Windows.net* Server 
1. SSMS 'de dosyayı açın... \\ Öğrenme modülleri \\ Şema Yönetimi \\ deployreferencedata. SQL
1. İfadeyi değiştirin: SET @wtpUser = &lt; user &gt; ve kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıttığınızda kullanılan Kullanıcı değerini yerine koy
1. _JobAgent_ veritabanına bağlandığınızdan emin olun ve betiği çalıştırmak için **F5** 'e basın

*Deployreferencedata. SQL* komut dosyasında aşağıdaki öğeleri gözlemleyin:
* **SP \_ Add \_ target \_ Group** , hedef grup adı demoservergroup öğesini oluşturur.
* hedef veritabanlarının kümesini tanımlamak için **SP \_ Add \_ target \_ Group \_ member** kullanılır.  İlk olarak _tenants1-DPT- &lt; user &gt;_ sunucusu eklenir.  Sunucuyu hedef olarak eklemek, bu sunucudaki veritabanlarının iş yürütme sırasında iş yürütmeye dahil edilmesini sağlar. Ardından _basetenantdb_ veritabanı ve *adhocretaşıma* veritabanı (sonraki bir öğreticide kullanılan) hedef olarak eklenir.
* **SP \_ Add \_ Job** , _başvuru veri dağıtımı_ adında bir iş oluşturur.
* **SP \_ Add \_ JobStep** , başvuru tablosu, venuetype 'u GÜNCELLEŞTIRMEK için T-SQL komut metnini içeren iş adımını oluşturur.
* Betikteki kalan görünümler, nesnelerin varlığını gösterir ve işin yürütülüşünü izler. İşin tüm hedef veritabanlarında ne zaman tamamlandığını öğrenmek için **yaşam döngüsü** sütunundaki durum değerini gözden geçirmek üzere bu sorguları kullanın.

Betik tamamlandıktan sonra, başvuru verilerinin güncelleştirildiğini doğrulayabilirsiniz.  SSMS 'de, *tenants1-DPT- &lt; user &gt;* sunucusundaki *Contosoconcerthall* veritabanına gidin ve *venuetype* tablosunu sorgulayın.  Artık, *Otocycle yarış* ve *yüzme kulübünün*  mevcut olduğunu denetleyin.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Başvuru tablosu dizinini yönetmek için bir iş oluşturma

Bu alıştırma, başvuru tablosunun birincil anahtarındaki dizini yeniden derlemek için bir iş kullanır.  Bu, büyük miktarlarda veri yüklendikten sonra yapılabilecek tipik bir veritabanı bakım işlemidir.

Aynı iş “sistem” saklı yordamlarını kullanarak bir iş oluşturun.

1. SSMS 'yi açın ve _Katalog-DPT- &lt; user &gt; . Database.Windows.net_ Server 'a bağlanın
1. Dosyayı Aç _... \\ Öğrenme modülleri \\ Şema Yönetimi \\ onlinereındex. SQL_
1. Sağ tıklayın, bağlantı ' yı seçin ve henüz bağlanmadıysanız _Katalog-DPT- &lt; user &gt; . Database.Windows.net_ sunucusuna bağlanın
1. _JobAgent_ veritabanına bağlandığınızdan emin olun ve betiği çalıştırmak için **F5** 'e basın

_Onlinereındex. SQL_ betiğinin aşağıdaki öğelerini gözlemleyin:
* **SP \_ Add \_ Job** , "çevrimiçi yeniden dizinle PK \_ \_ venuetyp \_ \_ 265e44fd7fd4c885" adlı yeni bir iş oluşturur
* **SP \_ Add \_ JobStep** , dizini GÜNCELLEŞTIRMEK için T-SQL komut metnini içeren iş adımını oluşturur
* Betik izleyici işi yürütmesindeki kalan görünümler. İşin tüm hedef grup üyelerinde başarıyla tamamlandığını öğrenmek için **yaşam döngüsü** sütunundaki durum değerini gözden geçirmek üzere bu sorguları kullanın.



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> 
> * T-SQL işleri birden çok veritabanı arasında çalışacak bir iş Aracısı oluşturma
> * Tüm kiracı veritabanlarındaki başvuru verilerini Güncelleştir
> * Tüm kiracı veritabanlarında bir tabloda dizin oluşturma

Ardından, kiracı veritabanlarında dağıtılmış sorguları çalıştırmayı araştırmak için geçici [Raporlama öğreticisini](./saas-tenancy-cross-tenant-reporting.md) deneyin.


## <a name="additional-resources"></a>Ek kaynaklar

* [Kiracı uygulaması dağıtımı başına Wingtip bilet SaaS veritabanı üzerine inşa eden ek öğreticiler](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Ölçeği artırılmış bulut veritabanlarını yönetme](./elastic-jobs-overview.md)