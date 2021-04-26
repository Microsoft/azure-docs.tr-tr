---
title: 'Öğretici: portalda coğrafi çoğaltma & yük devretme'
description: Azure portal kullanarak bir veritabanı için Coğrafi çoğaltmayı yapılandırın ve yük devretmeyi başlatın.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673268"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Öğretici: Azure portal (Azure SQL veritabanı) etkin Coğrafi çoğaltmayı ve yük devretmeyi yapılandırma

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, [Azure Portal](https://portal.azure.com) kullanarak [Azure SQL veritabanı için etkin Coğrafi çoğaltmayı](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) yapılandırma ve yük devretmeyi başlatma işlemi gösterilmektedir.

Otomatik yük devretme gruplarını kullanan en iyi uygulamalar için bkz. Azure SQL [veritabanı Için en iyi uygulamalar](auto-failover-group-overview.md#best-practices-for-sql-database) ve [Azure SQL yönetilen örneği için en iyi uygulamalar](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Önkoşullar

Azure portal kullanarak etkin Coğrafi çoğaltmayı yapılandırmak için aşağıdaki kaynağa ihtiyacınız vardır:

* Azure SQL veritabanı 'ndaki bir veritabanı: farklı bir coğrafi bölgeye çoğaltmak istediğiniz birincil veritabanı.

> [!Note]
> Azure portal kullanırken, birincil ile aynı abonelik içinde yalnızca ikincil bir veritabanı oluşturabilirsiniz. İkincil bir veritabanının farklı bir abonelikte olması gerekiyorsa [create database REST API](/rest/api/sql/databases/createorupdate) veya [ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql)kullanın.

## <a name="add-a-secondary-database"></a>İkincil veritabanı Ekle

Aşağıdaki adımlar, coğrafi çoğaltma ortaklığında yeni bir ikincil veritabanı oluşturur.  

İkincil bir veritabanı eklemek için, aboneliğin sahibi veya ortak sahibi olmanız gerekir.

İkincil veritabanı, birincil veritabanıyla aynı ada sahiptir ve varsayılan olarak aynı hizmet katmanını ve işlem boyutunu içerir. İkincil veritabanı tek bir veritabanı veya havuza alınmış bir veritabanı olabilir. Daha fazla bilgi için bkz. [DTU tabanlı satın alma modeli](service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md).
İkincil oluşturulup oluşturulduktan sonra veriler birincil veritabanından yeni ikincil veritabanına çoğaltılmaya başlar.

> [!NOTE]
> İş ortağı veritabanı zaten mevcutsa (örneğin, önceki coğrafi çoğaltma ilişkisini sonlandırmayla ilgili bir sonuç olarak), komut başarısız olur.

1. [Azure Portal](https://portal.azure.com), coğrafi çoğaltma için ayarlamak istediğiniz veritabanına gidin.
2. SQL veritabanı sayfasında, **coğrafi çoğaltma**' yı seçin ve ardından ikincil veritabanını oluşturmak için bölgeyi seçin. Birincil veritabanını barındıran bölge dışında herhangi bir bölgeyi seçebilirsiniz, ancak [eşleştirilmiş bölgeyi](../../best-practices-availability-paired-regions.md)öneririz.

    ![Coğrafi çoğaltmayı yapılandırma](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. İkincil veritabanı için sunucu ve fiyatlandırma katmanını seçin ya da yapılandırın.

    ![ikincil form oluştur](./media/active-geo-replication-configure-portal/create-secondary.png)
4. İsteğe bağlı olarak, elastik bir havuza ikincil bir veritabanı ekleyebilirsiniz. Bir havuzda ikincil veritabanı oluşturmak için **elastik havuz** ' a tıklayın ve hedef sunucuda bir havuz seçin. Hedef sunucuda bir havuz zaten var olmalıdır. Bu iş akışı havuz oluşturmaz.
5. İkincili eklemek için **Oluştur** ' a tıklayın.
6. İkincil veritabanı oluşturulur ve dengeli dağıtım işlemi başlar.

    ![ikincil eşleme](./media/active-geo-replication-configure-portal/seeding0.png)
7. Dengeli dağıtım işlemi tamamlandığında, ikincil veritabanı durumunu görüntüler.

    ![Dengeli dağıtım Tamam](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Yük devretme başlatma

İkincil veritabanı birincil olacak şekilde değiştirilebilir.  

1. [Azure Portal](https://portal.azure.com), coğrafi çoğaltma ortaklığının birincil veritabanına gidin.
2. SQL veritabanı dikey penceresinde **Tüm ayarlar**  >  **coğrafi çoğaltma**' yı seçin.
3. **İkincil** öğeler listesinde, yeni birincil olmasını istediğiniz veritabanını seçin ve **zorlamalı yük devretme**' ye tıklayın.

    ![yük devretme](./media/active-geo-replication-configure-portal/secondaries.png)
4. Yük devretmeyi başlatmak için **Evet** ' e tıklayın.

Komut, ikincil veritabanını hemen birincil role geçirir. Bu işlem normalde 30 saniye veya daha az bir sürede tamamlanır.

Roller geçildiğinde her iki veritabanının da kullanılamadığı (0 ile 25 saniye arasında) kısa bir süre vardır. Birincil veritabanının birden çok ikincil veritabanı varsa, komut otomatik olarak diğer ikincilleri yeni birincil sunucuya bağlanacak şekilde yeniden yapılandırır. İşlemin tamamının normal koşullarda tamamlanması bir dakikadan kısa sürer.

> [!NOTE]
> Bu komut, bir kesinti olması durumunda veritabanını hızlı bir şekilde kurtarmaya yönelik olarak tasarlanmıştır. Veri eşitleme (zorlamalı yük devretme) olmadan yük devretmeyi tetikler.  Birincil çevrimiçi ise ve komut verildiğinde işlemler varsa, bazı veri kaybı oluşabilir.

## <a name="remove-secondary-database"></a>İkincil veritabanını kaldır

Bu işlem, çoğaltmayı ikincil veritabanına kalıcı olarak sonlandırır ve ikincil öğesinin rolünü düzenli bir okuma-yazma veritabanıyla değiştirir. İkincil veritabanı bağlantısı bozulur, komut başarılı olur ancak bağlantı geri yüklenene kadar ikincil okuma-yazma olmaz.  

1. [Azure Portal](https://portal.azure.com), coğrafi çoğaltma ortaklığının birincil veritabanına gidin.
2. SQL veritabanı sayfasında, **coğrafi çoğaltma**' yı seçin.
3. **İkincil** öğeler listesinde, coğrafi çoğaltma ortaklığından kaldırmak istediğiniz veritabanını seçin.
4. **Çoğaltmayı durdur**' a tıklayın.

    ![İkincili kaldır](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Bir onay penceresi açılır. Veritabanını coğrafi çoğaltma ortaklığından kaldırmak için **Evet** ' e tıklayın. (Herhangi bir çoğaltmanın parçası olmayan bir okuma-yazma veritabanına ayarlayın.)

## <a name="next-steps"></a>Sonraki adımlar

* Etkin coğrafi çoğaltma hakkında daha fazla bilgi için bkz. [etkin coğrafi çoğaltma](active-geo-replication-overview.md).
* Otomatik yük devretme grupları hakkında bilgi edinmek için bkz. [otomatik yük devretme grupları](auto-failover-group-overview.md)
* İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md).