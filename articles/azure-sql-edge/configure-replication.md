---
title: Azure SQL Edge 'e çoğaltmayı yapılandırma
description: Azure SQL Edge 'e çoğaltmayı yapılandırma hakkında bilgi edinin.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395232"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Azure SQL Edge 'e çoğaltmayı yapılandırma 

Azure SQL Edge 'in bir örneğini, tek yönlü işlem çoğaltması veya anlık görüntü çoğaltması için anında iletme abonesi olarak yapılandırabilirsiniz. Bu örnek, bir işlem çoğaltma yapılandırması için yayımcı veya dağıtıcı olarak çalışamaz. Azure SQL Edge 'in birleştirme çoğaltmasını, eşler arası çoğaltmayı veya Oracle yayımlamasını desteklemediğini unutmayın.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar
  
- Azure SQL Edge örneği, yayımcı için bir itme abonesi olmalıdır.
- Yayımcı ve dağıtıcı şunlardan biri olabilir:
   - Şirket içinde çalışan bir SQL Server örneği veya bir Azure sanal makinesinde çalışan bir SQL Server örneği. Daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](../azure-sql/virtual-machines/index.yml). SQL Server örneklerin SQL Server 2016 ' den sonraki bir sürümü kullanıyor olması gerekir.
   - Azure SQL yönetilen örneği örneği. SQL yönetilen örneği, Yayımcı, dağıtıcı ve abone veritabanlarını barındırabilirler. Daha fazla bilgi için bkz. [SQL veritabanı yönetilen örneği Ile çoğaltma](/azure/sql-database/replication-with-sql-database-managed-instance/).

- Dağıtım veritabanı ve çoğaltma aracıları bir Azure SQL Edge örneğine yerleştirilemez.  

> [!NOTE]
> Çoğaltmayı desteklenmeyen bir sürümü kullanarak yapılandırmayı denerseniz, şu iki hatayı alabilirsiniz: MSSQL_REPL20084 ("işlem aboneye bağlanamadı.") ve MSSQL_REPL40532 (" \<name> oturum açma tarafından istenen sunucuyu açamıyor. Oturum açılamadı. ").  

## <a name="remarks"></a>Açıklamalar

Aşağıdaki gereksinimler ve en iyi yöntemler, çoğaltmayı yapılandırırken anlaşılması açısından önemlidir:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak çoğaltmayı yapılandırabilirsiniz. Ayrıca, SQL Server Management Studio veya [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)kullanarak yayımcıda Transact-SQL deyimlerini çalıştırarak da bunu yapabilirsiniz.
- Azure SQL Edge örneğine çoğaltmak için, oturum açmak için SQL Server kimlik doğrulaması kullanmanız gerekir.
- Çoğaltılan tablolarda birincil anahtar olmalıdır.
- SQL Server tek bir yayını hem Azure SQL Edge hem de SQL Server (Şirket içi ve SQL Server bir Azure sanal makinesinde) abonelerine destekleyebilir.  
- Çoğaltma yönetimi, izleme ve sorun giderme SQL Server örneğinden gerçekleştirilmelidir.  
- Yalnızca Azure SQL Edge 'e yönelik gönderme abonelikleri desteklenir.  
- Yalnızca `@subscriber_type = 0` `sp_addsubscription` Azure SQL Edge için saklı yordamda desteklenir.  
- Azure SQL Edge, iki yönlü, anında, güncelleştirilebilir veya eşler arası çoğaltmayı desteklemez.
- Azure SQL Edge yalnızca SQL Server veya SQL yönetilen örneği 'nde kullanılabilen özelliklerin bir alt kümesini destekler. Desteklenmeyen bir veya daha fazla özellik içeren bir veritabanını (veya veritabanı içindeki nesneleri) çoğaltmaya çalışırsanız, deneme başarısız olur. Örneğin, uzamsal veri türlerine sahip nesneler içeren bir veritabanını çoğaltmaya çalışırsanız bir hata alırsınız. Daha fazla bilgi için bkz. [Azure SQL Edge 'In desteklenen özellikleri](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Azure SQL Edge örneğinde başvuru verilerini başlatma

Örneğinizi, zaman içinde değişen başvuru verileriyle birlikte başlatmak isteyebilirsiniz. Örneğin, Azure SQL Edge örneğinizin bir SQL Server örneği üzerinde eğitilen bir örnek üzerinde makine öğrenimi modellerini güncelleştirmek isteyebilirsiniz. Aşağıda bu tür bir senaryoda örneğinizi nasıl başlataöğreneceksiniz:

1. SQL Server veritabanında bir işlem çoğaltma yayını oluşturun.  
2. SQL Server örneğinde, Azure SQL Edge 'e yönelik bir aboneliğe gönderim oluşturmak için **yeni abonelik Sihirbazı 'nı** veya Transact-SQL deyimlerini kullanın.  
3. Azure SQL Edge 'de çoğaltılan veritabanını, anlık görüntü Aracısı tarafından oluşturulan ve Dağıtım Aracısı tarafından dağıtılan ve sunulan bir anlık görüntü kullanarak başlatabilirsiniz. Alternatif olarak, yayımcıdan bir veritabanının yedeğini kullanarak da başlatabilirsiniz. Veritabanı yedeklemesi Azure SQL Edge tarafından desteklenmeyen nesneler veya özellikler içeriyorsa geri yükleme işleminin başarısız olduğunu unutmayın.

## <a name="limitations"></a>Sınırlamalar

Azure SQL Edge abonelikleri için aşağıdaki seçenekler desteklenmez:

- Dosya grupları ilişkilendirmesini Kopyala  
- Tablo bölümleme düzenlerini Kopyala  
- Dizin bölümleme düzenlerini Kopyala  
- Kullanıcı tanımlı istatistikleri kopyalama  
- Varsayılan bağlamaları Kopyala  
- Kural bağlamalarını Kopyala  
- Tam metin dizinlerini Kopyala  
- XML XSD 'yi Kopyala  
- XML dizinlerini Kopyala  
- İzinleri Kopyala  
- Uzamsal dizinleri Kopyala  
- Filtrelenmiş dizinleri Kopyala  
- Veri sıkıştırma özniteliğini Kopyala  
- Seyrek sütun özniteliğini Kopyala  
- FILESTREAM, `hierarchyid` veya uzamsal veri türlerini Kopyala
- `hierarchyid`Maks. veri türlerine Dönüştür  
- Uzamsal değeri en fazla veri türlerine Dönüştür  
- Genişletilmiş özellikleri Kopyala  
- İzinleri Kopyala  

## <a name="examples"></a>Örnekler

Yayın ve gönderme temelli bir abonelik oluşturun. Daha fazla bilgi için bkz.
  
- [Yayın oluşturma](/sql/relational-databases/replication/publish/create-a-publication)
- Azure SQL Edge sunucu adı ve IP 'si (örneğin, **Myedgeınstance, 1433**) ve Azure SQL Edge örneğindeki hedef veritabanı (örneğin, **AdventureWorks**) olarak bir veritabanı adı kullanarak [bir anında iletme aboneliği oluşturun](/sql/relational-databases/replication/create-a-push-subscription/) .  

## <a name="next-steps"></a>Sonraki adımlar  

- [Yayın oluşturma](/sql/relational-databases/replication/publish/create-a-publication)
- [İtme aboneliği oluşturma](/sql/relational-databases/replication/create-a-push-subscription/)
- [Çoğaltma türleri](/sql/relational-databases/replication/types-of-replication)
- [İzleme (çoğaltma)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Abonelik başlatma](/sql/relational-databases/replication/initialize-a-subscription)