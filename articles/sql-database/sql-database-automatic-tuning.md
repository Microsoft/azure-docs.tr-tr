---
title: Otomatik ayarlamaya genel bakış
description: Azure SQL veritabanı, SQL sorgusunu analiz eder ve Kullanıcı iş yüküne otomatik olarak uyum sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/06/2019
ms.openlocfilehash: 179bb5c9d718a556b829af8f860cb284597835aa
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821886"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL veritabanı 'nda otomatik ayarlama

Azure SQL veritabanı otomatik ayarlama, AI ve makine öğrenimine dayalı sürekli performans ayarları aracılığıyla en yüksek performans ve kararlı iş yükleri sağlar.

Otomatik ayarlama, bir veritabanında yürütülen sorguları sürekli olarak izlemek için yerleşik zeka kullanan tam olarak yönetilen bir akıllı performans hizmetidir ve performanslarını otomatik olarak geliştirir. Bu, veritabanı değişen iş yüklerine dinamik olarak uyarlanarak ve ayarlama önerilerini uygulayarak elde edilir. Otomatik ayarlama, Azure 'daki tüm veritabanlarından AI aracılığıyla yatay olarak öğrenir ve ayarlama eylemlerini dinamik olarak geliştirir. Daha uzun bir Azure SQL veritabanı, üzerinde otomatik ayarlama ile çalışır, daha iyidir.

Azure SQL veritabanı otomatik ayarlama, kararlı ve en yüksek performanslı veritabanı iş yükleri sağlamak üzere etkinleştirebileceğiniz en önemli özelliklerden biri olabilir.

## <a name="what-can-automatic-tuning-do-for-you"></a>Otomatik ayarlama sizin için ne yapabilir?

- Azure SQL veritabanlarının otomatik performans ayarlaması
- Performans kazançlarını otomatik doğrulama
- Otomatik geri alma ve kendi kendini düzeltme
- Ayarlama geçmişi
- El ile dağıtımlar için işlem T-SQL betikleri ayarlama
- Proaktif iş yükü performansı izleme
- Yüzlerce binlerce veritabanında ölçeği genişletme özelliği
- DevOps kaynaklarına yönelik olumlu etki ve toplam sahip olma maliyeti

## <a name="safe-reliable-and-proven"></a>Güvenli, güvenilir ve kanıtlanmış

Azure SQL veritabanlarına uygulanan ayarlama işlemleri, en yoğun iş yüklerinizin performansı için tamamen güvenlidir. Sistem, Kullanıcı iş yükleriyle karışmamak üzere tasarlanmıştır. Otomatik ayarlama önerileri yalnızca düşük kullanım saatlerinde uygulanır. Ayrıca sistem, iş yükü performansını korumak için otomatik ayarlama işlemlerini geçici olarak devre dışı bırakabilir. Böyle bir durumda, "sistem tarafından devre dışı bırakıldı" iletisi Azure portal görüntülenecektir. Otomatik ayarlama, en yüksek kaynak önceliğine sahip iş yüklerini sayar.

Otomatik ayarlama mekanizmaları aynı ve Azure üzerinde çalışan birkaç milyon veritabanında ölçünüzü. Otomatik ayarlama işlemleri, iş yükü performansının olumlu bir geliştirmesi olduğundan emin olmak için otomatik olarak doğrulanır. Gerileyen performans önerileri dinamik olarak algılanır ve geri döndürülür. Kaydedilen ayarlama geçmişi ile, her bir Azure SQL veritabanı için yapılan ayarlama geliştirmelerinden oluşan açık bir izleme bulunur. 

![Otomatik ayarlama nasıl çalışır?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL veritabanı otomatik ayarlama, temel mantığını SQL Server otomatik ayarlama altyapısıyla paylaşıyor. Yerleşik zeka mekanizması hakkında daha fazla teknik bilgi için [SQL Server otomatik ayarlama](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)bölümüne bakın.

## <a name="use-automatic-tuning"></a>Otomatik ayarlama kullan

Aboneliğinizde otomatik ayarlama özelliğinin etkinleştirilmesi gerekir. Azure portal kullanarak otomatik ayarlamayı etkinleştirmek için, bkz. [otomatik ayarlamayı etkinleştirme](sql-database-automatic-tuning-enable.md).

Otomatik ayarlama, otomatik olarak performans kazanmasının otomatik olarak doğrulanması dahil ayarlama önerilerini uygulayarak olarak çalışabilen işleyebilir. 

Daha fazla denetim için ayarlama önerilerinin otomatik uygulaması kapatılabilir ve ayarlama önerileri Azure portal aracılığıyla el ile uygulanabilir. Çözümü yalnızca otomatik ayarlama önerilerini görüntülemek ve seçtiğiniz betikler ve araçlar aracılığıyla el ile uygulamak için de kullanabilirsiniz. 

Otomatik ayarlamanın nasıl çalıştığına ve tipik kullanım senaryolarıyla ilgili genel bir bakış için bkz. gömülü video:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Otomatik ayarlama seçenekleri

Azure SQL veritabanında bulunan otomatik ayarlama seçenekleri şunlardır:

| Otomatik ayarlama seçeneği | Tek veritabanı ve havuza alınmış veritabanı desteği | Örnek veritabanı desteği |
| :----------------------------- | ----- | ----- |
| **Dizin oluşturma** -iş yükünüzün performansını iyileştirebilecek dizinleri tanımlar, dizinler oluşturur ve sorguların performansının iyileştirildiğini otomatik olarak doğrular. | Evet | Hayır | 
| **Drop Index** -benzersiz dizinler ve uzun süredir kullanılmayan dizinler dışında, yedekli ve yinelenen dizinleri her gün tanımlar (> 90 gün). Bu seçeneğin bölüm değiştirme ve Dizin ipuçlarını kullanan uygulamalarla uyumlu olmadığına lütfen unutmayın. Kullanılmayan dizinleri bırakma, Premium ve İş Açısından Kritik hizmet katmanlarında desteklenmez. | Evet | Hayır |
| **Son ıyı planı zorla** (otomatik plan düzeltmesi)-SQL sorgularını, önceki iyi plandan daha yavaş bir yürütme planı kullanarak tanımlar ve gerileme planı yerine bilinen son iyi planı kullanarak sorgular. | Evet | Evet |

Otomatik ayarlama, veritabanı performanslarını iyileştirebilen ve bunları [Azure Portal](sql-database-advisor-portal.md)gösteren ve [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) [Ile ortaya çıkaran son iyi plan önerilerini tanımlar, Drop Index ve zorlar. REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). EN son ıyı hale zorlama planı ve T-SQL aracılığıyla otomatik ayarlama seçeneklerini yapılandırma hakkında daha fazla bilgi edinmek için bkz. otomatik [ayarlama otomatik plan düzeltmesini tanıtır](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Portalı kullanarak ayarlama önerilerini el ile uygulayabilir ya da otomatik ayarlama olarak çalışabilen ' nin ayarlama önerilerini sizin yerinize gerçekleştirmesini sağlayabilirsiniz. System olarak çalışabilen 'ın ayarlama önerilerini uygulamasına izin vermenin avantajları, iş yükü performansına yönelik olumlu bir kazanç olduğunu otomatik olarak doğruladığından, önemli bir performans geliştirmesi algılanmadığında, bu işlem ayarlama önerisini otomatik olarak döndürür. Bu, sık yürütülemeyen bir ayar önerisi tarafından etkilenen sorgular söz konusu olduğunda, doğrulama aşamasının tasarım ile 72 saat arasında sürebileceğini unutmayın.

T-SQL aracılığıyla ayarlama önerileri uyguluyorsanız, otomatik performans doğrulaması ve ters çevirme mekanizmaları kullanılamaz. Bu şekilde uygulanan öneriler etkin kalır ve 24-48 saat için ayarlama önerileri listesinde gösterilir. sistemi otomatik olarak çizmadan önce. Bir öneriyi daha erken kaldırmak istiyorsanız, Azure portal ' den atabilirsiniz.

Otomatik ayarlama seçenekleri veritabanı başına bağımsız olarak etkinleştirilebilir veya devre dışı bırakılabilir ya da SQL veritabanı sunucularında yapılandırılabilirler ve ayarları sunucudan devralan her veritabanına uygulanabilir. SQL veritabanı sunucuları, otomatik ayarlama ayarları için Azure varsayılanlarını devralınabilir. Azure Varsayılanları Şu anda FORCE_LAST_GOOD_PLAN etkinleştirilir, CREATE_INDEX etkinleştirilir ve DROP_INDEX devre dışı bırakılır.

Bir sunucuda otomatik ayarlama seçeneklerinin yapılandırılması ve üst sunucuya ait olan veritabanlarının ayarlarını devralma, çok sayıda veritabanı için otomatik ayarlama seçeneklerinin yönetimini basitleştikten sonra otomatik ayarlamayı yapılandırmak için önerilen bir yöntemdir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı 'nda iş yükünüzü yönetmek üzere otomatik ayarlamayı etkinleştirmek için bkz. [otomatik ayarlamayı etkinleştirme](sql-database-automatic-tuning-enable.md).
- Otomatik ayarlama önerilerini el ile gözden geçirmek ve uygulamak için bkz. [performans önerilerini bulma ve uygulama](sql-database-advisor-portal.md).
- T-SQL ' i kullanarak otomatik ayarlama önerilerini uygulama ve görüntüleme hakkında bilgi edinmek için bkz. [t-SQL aracılığıyla otomatik ayarlamayı yönetme](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Otomatik ayarlama önerileri için e-posta bildirimleri oluşturma hakkında bilgi edinmek için bkz. [otomatik ayarlama Için e-posta bildirimleri](sql-database-automatic-tuning-email-notifications.md)
- Otomatik ayarlama sırasında kullanılan yerleşik zeka hakkında bilgi edinmek için bkz. [yapay zeka uyarlayan Azure SQL veritabanları](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Otomatik ayarlama 'nın Azure SQL veritabanı ve SQL Server 2017 ' de nasıl çalıştığı hakkında bilgi edinmek için [SQL Server otomatik ayarlama](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)bölümüne bakın.
