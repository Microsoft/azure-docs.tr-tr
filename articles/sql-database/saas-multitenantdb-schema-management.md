---
title: Çok kiracılı bir uygulamada şemayı yönetme
description: Azure SQL Veritabanı’nı kullanan çok kiracılı bir uygulamada birden fazla kiracı için Şemayı yönetme
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6f660426c41b37dd27438c28cbf603bdbf1e58b3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822113"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Parçalı çok kiracılı SQL veritabanlarını kullanan bir SaaS uygulamasında şemayı yönetme

Bu öğretici, bir hizmet olarak yazılım (SaaS) uygulaması içindeki bir veritabanını bir yandan korumanın sorunlarını inceler. Çözümler, veritabanları genelinde şema değişikliklerini ayrıntılı hale getirmek için gösterilmiştir.

Tüm uygulamalar gibi, Wingtip biletleri SaaS uygulaması zaman içinde gelişirken veritabanında değişiklik yapılmasını gerektirecektir. Değişiklikler şema veya başvuru verilerini etkileyebilir ya da veritabanı bakım görevlerini uygulayabilir. Kiracı başına bir veritabanı kullanan bir SaaS uygulamasıyla, değişikliklerin potansiyel olarak büyük ölçekli bir kiracı veritabanları genelinde koordine olması gerekir. Ayrıca, bu değişiklikleri yeni veritabanlarına dahil olduklarından emin olmak için veritabanı sağlama sürecine eklemeniz gerekir.

#### <a name="two-scenarios"></a>İki senaryo

Bu öğretici aşağıdaki iki senaryoyu araştırır:
- Tüm kiracılar için başvuru verileri güncelleştirmelerini dağıtın.
- Başvuru verilerini içeren tablodaki bir dizini yeniden derleyin.

Azure SQL veritabanı 'nın [elastik işler](elastic-jobs-overview.md) özelliği, bu işlemleri kiracı veritabanları arasında yürütmek için kullanılır. İşler ' şablon ' Kiracı veritabanı üzerinde de çalışır. Bu şablon veritabanı, Wingtip biletleri örnek uygulamasında yeni bir kiracı veritabanı sağlamak için kopyalanır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir iş Aracısı oluşturun.
> * Birden çok kiracı veritabanında bir T-SQL sorgusu yürütün.
> * Tüm kiracı veritabanlarındaki başvuru verilerini güncelleştirin.
> * Tüm kiracı veritabanlarındaki bir tabloda bir dizin oluşturun.

## <a name="prerequisites"></a>Ön koşullar

- Wingtip biletleri çok kiracılı veritabanı uygulaması zaten dağıtılmalıdır:
    - Yönergeler için, Wingtip bilet SaaS çok kiracılı veritabanı uygulamasını tanıtan ilk öğreticiye bakın:<br />[Azure SQL veritabanı 'nı kullanan, parçalı bir çok kiracılı uygulamayı dağıtın ve araştırın](saas-multitenantdb-get-started-deploy.md).
        - Dağıtım işlemi beş dakikadan kısa bir süre boyunca çalışır.
    - Wingtip 'in *parçalı çok kiracılı* sürümünü yüklemiş olmanız gerekir. *Tek başına* ve *kiracı başına veritabanı* sürümleri bu öğreticiyi desteklemez.

- SQL Server Management Studio (SSMS) öğesinin en son sürümü yüklü olmalıdır. [SSMS 'Yi Indirip yükleyin](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell yüklü olmalıdır. Ayrıntılar için bkz. [Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)kullanmaya başlama.

> [!NOTE]
> Bu öğretici, Azure SQL veritabanı hizmetinin sınırlı önizlemede ([elastik veritabanı işleri](sql-database-elastic-database-client-library.md)) olan özelliklerini kullanır. Bu öğreticiyi yapmak istiyorsanız, microsoft.com for Subject = elastik Işler Preview ile abonelik KIMLIĞINIZI *Saasfeedback\@* sağlayın. Aboneliğinizin etkinleştirildiğini belirten onayı aldıktan sonra, [en son ön sürüm işleri cmdlet’lerini indirip yükleyin](https://github.com/jaredmoo/azure-powershell/releases). Bu önizleme sınırlı olduğundan ilgili sorular veya destek için *microsoft.com\@Saasfeedback* ile iletişime geçin.

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS şeması yönetim düzenlerine giriş

Bu örnekte kullanılan parçalı çok kiracılı veritabanı modeli, kiracılar veritabanının bir veya daha fazla kiracı içermesini sağlar. Bu örnek, bir *karma* kiracı yönetim modelini etkinleştirerek çok kiracılı ve tek kiracılı veritabanlarının bir karışımını kullanma potansiyelini araştırır. Bu veritabanlarında yapılan değişikliklerin yönetilmesi karmaşık olabilir. [Elastik işler](elastic-jobs-overview.md) , çok sayıda veritabanının yönetimini ve yönetimini kolaylaştırır. İşler, kiracı veritabanları grubuna karşı, Transact-SQL komut dosyalarını görev olarak güvenli ve güvenilir bir şekilde çalıştırmanızı sağlar. Görevler, Kullanıcı etkileşimi veya girişten bağımsızdır. Bu yöntem, bir uygulamadaki tüm kiracılarda yapılan değişiklikleri şemaya veya ortak başvuru verilerine dağıtmak için kullanılabilir. Elastik Işler, veritabanının altın şablon kopyasını tutmak için de kullanılabilir. Şablon, yeni kiracılar oluşturmak için kullanılır, her zaman en son şema ve başvuru verilerinin kullanımda olmasını sağlar.

![ekran](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Esnek İşler sınırlı önizlemesi

Artık Azure SQL veritabanı 'nın tümleşik bir özelliği olan elastik Işlerin yeni bir sürümü vardır. Esnek İşler’in bu yeni sürümü, şu anda sınırlı önizlemeyle sunulmaktadır. Sınırlı Önizleme Şu anda iş Aracısı oluşturmak için PowerShell kullanmayı destekler ve işleri oluşturmak ve yönetmek için T-SQL ' i destekler.
> [!NOTE]
> Bu öğretici, SQL veritabanı hizmetinin sınırlı önizlemede (Elastik Veritabanı işleri) olan özelliklerini kullanır. Bu öğreticiyi yapmak istiyorsanız, konu = elastik Işlerin önizlemesi SaaSFeedback@microsoft.com abonelik KIMLIĞINIZI belirtin. Aboneliğinizin etkin olduğunu doğruladıktan sonra, en son yayın öncesi işleri cmdlet 'lerini indirip yükleyin. Bu önizleme sınırlı olduğundan ilgili sorular veya destek için SaaSFeedback@microsoft.com başvurun.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip bilet SaaS çok kiracılı veritabanı uygulaması kaynak kodu ve betikleri alın

Wingtip biletleri SaaS çok kiracılı veritabanı betikleri ve uygulama kaynak kodu, GitHub 'daki [Wingtipbilet ssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) deposunda bulunabilir. Wingtip bilet SaaS betikleri indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) bakın.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>İş Aracısı veritabanı ve yeni iş Aracısı oluşturma

Bu öğreticide, İş Aracısı veritabanı ve iş Aracısı oluşturmak için PowerShell kullanmanız gerekir. SQL Aracısı tarafından kullanılan MSDB veritabanı gibi, İş Aracısı iş tanımlarını, iş durumunu ve geçmişi depolamak için bir Azure SQL veritabanı kullanır. İş Aracısı oluşturulduktan sonra, işleri hemen oluşturabilir ve izleyebilirsiniz.

1. **PowerShell ISE**'de, *...\\öğrenme modüllerini\\şema yönetimi\\demo-SchemaManagement. ps1*.
2. Betiği çalıştırmak için **F5**'e basın.

*Demo-SchemaManagement. ps1* betiği, katalog sunucusunda _JobAgent_ adlı bir veritabanı oluşturmak için *Deploy-SchemaManagement. ps1* betiğini çağırır. Betik daha sonra, _JobAgent_ veritabanını bir parametre olarak geçirerek iş aracısını oluşturur.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tüm kiracılara yeni başvuru verilerini dağıtmak için bir iş oluşturma

#### <a name="prepare"></a>Hazırlama

Her kiracının veritabanı, **Venuetype** tablosunda bir mekan türleri kümesi içerir. Her bir mekan türü, bir mekanda barındırılabilecek olayların türünü tanımlar. Bu mekan türleri, kiracı olayları uygulamasında gördüğünüz arka plan görüntülerine karşılık gelir.  Bu alıştırmada, iki ek mekan türü eklemek için tüm veritabanlarına bir güncelleştirme dağıtırsınız: *Otocycle yarış* ve *Yüzme Kulübü*.

İlk olarak, her kiracı veritabanına dahil olan mekan türlerini gözden geçirin. SQL Server Management Studio (SSMS) içindeki kiracı veritabanlarından birine bağlanın ve Venuetype tablosunu inceleyin.  Bu tabloyu, veritabanı sayfasından erişilen Azure portal sorgu düzenleyicisinde da sorgulayabilirsiniz.

1. SSMS 'yi açın ve kiracı sunucusuna bağlanın: *tenants1-DPT-&lt;user&gt;. Database.Windows.net*
1. *Otocycle yarış* ve *yüzme kulübünün* **Şu anda dahil** edilmediğini onaylamak için *tenants1-DPT-&lt;User&gt;* Server 'daki *contosoconcerthall* veritabanına gidin ve *venuetype 'ı sorgulayın* tablo.



#### <a name="steps"></a>Adımlar

Şimdi, her bir kiracılar veritabanındaki **Venuetype** tablosunu güncelleştirmek için bir iş oluşturun ve iki yeni mekan türünü ekleyebilirsiniz.

Yeni bir iş oluşturmak için, _JobAgent_ veritabanında oluşturulan iş kümesi sistem saklı yordamlarını kullanırsınız. Saklı yordamlar iş Aracısı oluşturulduğunda oluşturulmuştur.

1. SSMS 'de kiracı sunucusuna bağlanın: tenants1-MT-&lt;User&gt;. database.windows.net

2. *Tenants1* veritabanına gidin.

3. *Motouetype* tablosunu, *otocycle yarış* ve *yüzme kulükünün* sonuçlar listesinde henüz olmadığından emin olmak için sorgulayın.

4. *Katalog-MT-&lt;user&gt;. Database.Windows.net*olan katalog sunucusuna bağlanın.

5. Katalog sunucusundaki _JobAgent_ veritabanına bağlanın.

6. SSMS 'de,... *\\öğrenme modüllerini\\şema yönetimi\\DeployReferenceData. SQL*dosyasını açın.

7. İfadeyi değiştirin: set @User = User&gt; &lt;ve Wingtip bilet SaaS çok kiracılı veritabanı uygulamasını dağıtırken kullanılan Kullanıcı değerini yerine koyun.

8. Betiği çalıştırmak için **F5**'e basın.

#### <a name="observe"></a>Uyarak

*Deployreferencedata. SQL* komut dosyasında aşağıdaki öğeleri gözlemleyin:

- **sp\_\_hedefi ekleme\_grubu** , hedef grup adı *demoservergroup*' u oluşturur ve gruba hedef Üyeler ekler.

- **sp\_\_target\_grubu ekle\_üye** aşağıdaki öğeleri ekler:
    - *Sunucu* hedefi üye türü.
        - Bu, kiracılar veritabanlarını içeren *tenants1-MT-&lt;user&gt;* sunucusudur.
        - Sunucu dahil, iş yürütüldüğü sırada mevcut olan kiracı veritabanlarını içerir.
    - *Katalog-MT-&lt;user&gt;* Server 'da bulunan şablon veritabanı (*basetenantdb*) için bir *veritabanı* hedefi üye türü,
    - Daha sonraki bir öğreticide kullanılan *adhocretaşıma* veritabanını dahil etmek için bir *veritabanı* hedefi üye türü.

- **sp\_add\_Job** , *başvuru veri dağıtımı*adlı bir iş oluşturur.

- **sp\_add\_JobStep** , başvuru tablosu, Venuetype 'ı güncelleştirmek için t-SQL komut metnini içeren iş adımını oluşturur.

- Betikteki kalan görünümler, nesnelerin varlığını gösterir ve işin yürütülüşünü izler. İşin ne zaman tamamlandığını öğrenmek için **yaşam döngüsü** sütunundaki durum değerini gözden geçirmek üzere bu sorguları kullanın. İş kiracılar veritabanını güncelleştirir ve başvuru tablosunu içeren iki ek veritabanını günceller.

SSMS 'de, *tenants1-MT-&lt;kullanıcı&gt;* sunucusundaki kiracı veritabanına gidin. *Otocycle yarış* ve *yüzme kulübünün* tabloya eklendiğini onaylamak için *venuetype* tablosunu sorgulayın. Mekan türlerinin toplam sayısı iki ile artmalıdır.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Başvuru tablosu dizinini yönetmek için bir iş oluşturma

Bu alıştırma, tüm kiracı veritabanlarındaki başvuru tablosu birincil anahtarındaki dizini yeniden oluşturmak için bir iş oluşturur. Dizin yeniden oluşturma, performansı artırmak için bir yöneticinin büyük miktarda veri yükü yükledikten sonra çalıştırabilecek tipik bir veritabanı yönetimi işlemidir.

1. SSMS 'de, *Katalog-MT-&lt;User&gt;. Database.Windows.net* Server 'daki _JobAgent_ veritabanına bağlanın.

2. SSMS 'de,... *\\öğrenme modüllerini\\şema yönetimi\\OnlineReindex. SQL*' i açın.

3. Betiği çalıştırmak için **F5**'e basın.

#### <a name="observe"></a>Uyarak

*Onlinereındex. SQL* betiğinin aşağıdaki öğelerini gözlemleyin:

* **sp\_add\_Job** , çevrimiçi yeniden DIZIN oluşturma *PK\_\_VenueTyp\_\_265E44FD7FD4C885*adlı yeni bir iş oluşturur.

* **sp\_add\_JobStep** , dizini güncelleştirmek için t-SQL komut metnini içeren iş adımını oluşturur.

* Betik izleyici işi yürütmesindeki kalan görünümler. İşin tüm hedef grup üyelerinde başarıyla tamamlandığını öğrenmek için **yaşam döngüsü** sütunundaki durum değerini gözden geçirmek üzere bu sorguları kullanın.

## <a name="additional-resources"></a>Ek kaynaklar

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Ölçeği artırılmış bulut veritabanlarını yönetme](elastic-jobs-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunları öğrendiniz:

> [!div class="checklist"]
> * Birden çok veritabanı arasında T-SQL işlerini çalıştırmak için bir iş Aracısı oluşturma
> * Tüm kiracı veritabanlarındaki başvuru verilerini Güncelleştir
> * Tüm kiracı veritabanlarında bir tabloda dizin oluşturma

Ardından, kiracı veritabanlarında dağıtılmış sorguları çalıştırmayı araştırmak için geçici [Raporlama öğreticisini](saas-multitenantdb-adhoc-reporting.md) deneyin.

