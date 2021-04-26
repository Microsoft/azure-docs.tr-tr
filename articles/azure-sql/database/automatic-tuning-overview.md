---
title: Otomatik ayarlamaya genel bakış
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği, SQL sorgusunu analiz eder ve Kullanıcı iş yüküne otomatik olarak uyum sağlar.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/23/2021
ms.openlocfilehash: 6bd8d6001fcd3bfa487259aa219ff771f26a8a94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951292"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde otomatik ayarlama
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örnek otomatik ayarlama, AI ve makine öğrenimine dayalı sürekli performans ayarları aracılığıyla en yüksek performans ve kararlı iş yükleri sağlar.

Otomatik ayarlama tam olarak yönetilen bir akıllı performans hizmetidir. Veritabanında yürütülen sorguları sürekli izlemek için yerleşik zekayı kullanır ve performansını otomatik olarak geliştirir. Bu, veritabanı değişen iş yüklerine dinamik olarak uyarlanarak ve ayarlama önerilerini uygulayarak elde edilir. Otomatik ayarlama, Azure 'daki tüm veritabanlarından AI aracılığıyla yatay olarak öğrenir ve ayarlama eylemlerini dinamik olarak geliştirir. Daha uzun bir veritabanı otomatik ayarlama ile birlikte çalıştırıldığında, daha iyidir.

Azure SQL veritabanı ve Azure SQL yönetilen örnek otomatik ayarlama, kararlı ve en yüksek performanslı veritabanı iş yükleri sağlamak üzere etkinleştirebileceğiniz en önemli özelliklerden biri olabilir.

## <a name="what-can-automatic-tuning-do-for-you"></a>Otomatik ayarlama sizin için ne yapabilir?

- Veritabanlarının otomatik performans ayarlaması
- Performans kazançlarını otomatik doğrulama
- Otomatik geri alma ve kendi kendini düzeltme
- Ayarlama geçmişi
- El ile dağıtımlar için ayarlama eylemi Transact-SQL (T-SQL) betikleri
- Proaktif iş yükü performansı izleme
- Yüzlerce binlerce veritabanında ölçeği genişletme özelliği
- DevOps kaynaklarına yönelik olumlu etki ve toplam sahip olma maliyeti

## <a name="safe-reliable-and-proven"></a>Güvenli, güvenilir ve kanıtlanmış

Azure SQL veritabanı 'nda veritabanlarına uygulanan ayarlama işlemleri, en yoğun iş yüklerinizin performansı için tamamen güvenlidir. Sistem, Kullanıcı iş yükleriyle karışmamak üzere tasarlanmıştır. Otomatik ayarlama önerileri yalnızca düşük kullanım saatlerinde uygulanır. Ayrıca sistem, iş yükü performansını korumak için otomatik ayarlama işlemlerini geçici olarak devre dışı bırakabilir. Böyle bir durumda, "sistem tarafından devre dışı bırakıldı" iletisi Azure portal görüntülenecektir. Otomatik ayarlama, en yüksek kaynak önceliğine sahip iş yüklerini sayar.

Otomatik ayarlama mekanizmaları aynı ve Azure üzerinde çalışan birkaç milyon veritabanında ölçünüzü. Otomatik ayarlama işlemleri, iş yükü performansının olumlu bir geliştirmesi olduğundan emin olmak için otomatik olarak doğrulanır. Gerileyen performans önerileri dinamik olarak algılanır ve geri döndürülür. Kaydedilen ayarlama geçmişi sayesinde, Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde her bir veritabanı için yapılan ayarlama geliştirmelerinden oluşan açık bir izleme bulunur.

![Otomatik ayarlama nasıl çalışır?](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

Azure SQL veritabanı otomatik ayarlama, temel mantığını veritabanı altyapısındaki SQL Server otomatik ayarlama özelliği ile paylaşıyor. Yerleşik zeka mekanizması hakkında daha fazla teknik bilgi için [SQL Server otomatik ayarlama](/sql/relational-databases/automatic-tuning/automatic-tuning)bölümüne bakın.

Otomatik ayarlamanın nasıl çalıştığına ve tipik kullanım senaryolarıyla ilgili genel bir bakış için bkz. gömülü video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Otomatik ayarlamayı etkinleştirme

- Azure portal veya [alter database](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) T-SQL deyimini kullanarak [Azure SQL veritabanı için otomatik ayarlamayı etkinleştirirsiniz](automatic-tuning-enable.md) .
- [Alter database](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current&preserve-view=true) T-SQL deyimini kullanarak Azure SQL yönetilen örneği için otomatik ayarlamayı etkinleştirirsiniz.

## <a name="automatic-tuning-options"></a>Otomatik ayarlama seçenekleri

Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde kullanılabilen otomatik ayarlama seçenekleri şunlardır:

| Otomatik ayarlama seçeneği | Tek veritabanı ve havuza alınmış veritabanı desteği | Örnek veritabanı desteği |
| :----------------------------- | ----- | ----- |
| **Dizin oluşturma** -iş yükünüzün performansını iyileştirebilecek dizinleri tanımlar, dizinler oluşturur ve sorguların performansının iyileştirildiğini otomatik olarak doğrular. | Yes | Hayır |
| **Drop Index** -kullanılmayan (son 90 gün boyunca) ve yinelenen dizinlerin sayısını bırakır. Birincil anahtar ve benzersiz kısıtlamaları destekleyen dizinler dahil benzersiz dizinler hiçbir şekilde bırakılmazlar. Bu seçenek, iş yükünde Dizin ipuçları içeren sorgular varsa veya iş yükü bölüm değiştirme yaptığında otomatik olarak devre dışı bırakılabilir. Premium ve İş Açısından Kritik hizmet katmanlarında, bu seçenek kullanılmayan dizinleri hiçbir şekilde bırakamaz, ancak varsa yinelenen dizinleri de bırakacak. | Yes | Hayır |
| **Son ıyı planı zorla** (otomatik plan düzeltmesi)-Azure SQL sorgularını, önceki iyi plandan daha yavaş bir yürütme planı kullanarak tanımlar ve gerileme planı yerine bilinen son iyi planı kullanarak sorgular. | Yes | Yes |

### <a name="automatic-tuning-for-sql-database"></a>SQL veritabanı için otomatik ayarlama

Azure SQL veritabanı için otomatik ayarlama, veritabanı performansınızı iyileştirmek için **Dizin oluşturma**, **Drop Index** ve **en son iyi plan planı** veritabanı Danışmanı önerilerini kullanır. Daha fazla bilgi için Azure portal, [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)'de ve [REST API](/rest/api/sql/serverautomatictuning) [veritabanı Danışmanı önerilerine](database-advisor-find-recommendations-portal.md)bakın.

Azure portal kullanarak ayarlama önerilerini el ile uygulayabilir veya otomatik ayarlama olarak çalışabilen sizin için ayarlama önerilerini uygulamanıza izin verebilirsiniz. System olarak çalışabilen 'ın ayarlama önerilerini uygulamasına izin vermenin avantajları, iş yükü performansına yönelik olumlu bir kazanç olduğunu otomatik olarak doğrular ve algılanan önemli performans geliştirmesi yoksa ayarlama önerisi otomatik olarak döndürülür. Bu, sık yürütülemeyen bir ayar önerisi tarafından etkilenen sorgular söz konusu olduğunda, doğrulama aşamasının tasarım ile 72 saat arasında sürebileceğini unutmayın.

T-SQL aracılığıyla ayarlama önerileri uyguluyorsanız, otomatik performans doğrulaması ve ters çevirme mekanizmaları kullanılamaz. Bu şekilde uygulanan öneriler etkin kalır ve 24-48 saat için ayarlama önerileri listesinde gösterilir. sistemi otomatik olarak çizmadan önce. Bir öneriyi daha erken kaldırmak istiyorsanız, Azure portal ' den atabilirsiniz.

Otomatik ayarlama seçenekleri veritabanı başına bağımsız olarak etkinleştirilebilir veya devre dışı bırakılabilir veya sunucu düzeyinde yapılandırılabilir ve ayarları sunucudan devralan her veritabanında uygulanabilir. Sunucular otomatik ayarlama ayarları için Azure varsayılanlarını alabilir. Azure Varsayılanları Şu anda FORCE_LAST_GOOD_PLAN etkinleştirilir, CREATE_INDEX etkinleştirilir ve DROP_INDEX devre dışı bırakılır.

> [!IMPORTANT]
> Mart itibariyle, otomatik ayarlama için Azure varsayılanlarına yapılan 2020 değişiklikler şu şekilde etkili olacaktır:
>
> - Yeni Azure Varsayılanları FORCE_LAST_GOOD_PLAN = etkin, CREATE_INDEX = devre dışı ve DROP_INDEX = devre dışı olacaktır.
> - Yapılandırılmış otomatik ayarlama tercihleri olmayan mevcut sunucular, yeni Azure varsayılanlarını DEVRALACAK şekilde otomatik olarak yapılandırılır. Bu, şu anda tanımsız bir durumda otomatik ayarlama için sunucu ayarlarına sahip olan tüm müşteriler için geçerlidir.
> - Oluşturulan yeni sunucular yeni Azure varsayılanlarını DEVRALACAK şekilde otomatik olarak yapılandırılacak (otomatik ayarlama yapılandırması yeni sunucu oluşturma sırasında tanımsız bir durumda olduğunda, daha önce farklı olur).

Bir sunucuda otomatik ayarlama seçeneklerinin yapılandırılması ve üst sunucuya ait olan veritabanlarının ayarlarını devralma, çok sayıda veritabanı için otomatik ayarlama seçeneklerinin yönetimini basitleştikten sonra otomatik ayarlamayı yapılandırmak için önerilen bir yöntemdir.

Otomatik ayarlama önerileri için e-posta bildirimleri oluşturma hakkında bilgi edinmek için bkz. [otomatik ayarlama Için e-posta bildirimleri](automatic-tuning-email-notifications-configure.md)

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için otomatik ayarlama

SQL yönetilen örneği için otomatik ayarlama yalnızca **ZORLAMALı son ıyı planı** destekliyor. T-SQL aracılığıyla otomatik ayarlama seçeneklerini yapılandırma hakkında daha fazla bilgi için bkz. otomatik [ayarlama otomatik plan düzeltmesini](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) ve [Otomatik plan düzeltmesini](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)tanıtır.

## <a name="next-steps"></a>Sonraki adımlar

- Otomatik ayarlama konusunda kullanılan yerleşik zeka hakkında bilgi edinmek için bkz. [yapay zeka uyarlayan Azure SQL veritabanı](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Otomatik ayarlama işleminin aynı şekilde nasıl çalıştığını öğrenmek için bkz. [Microsoft Azure SQL veritabanı milyonlarca veritabanını otomatik olarak dizine ekleme](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).