---
title: 'Azure portal: coğrafi çoğaltma'
description: Azure portal kullanarak Azure SQL veritabanı 'nda tek veya havuza alınmış bir veritabanı için Coğrafi çoğaltmayı yapılandırma ve yük devretmeyi başlatma
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 7ddaefb0f65db1cfc828e4baa844f6d8e01f9d2e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808117"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Azure portal Azure SQL veritabanı için etkin Coğrafi çoğaltmayı yapılandırma ve yük devretmeyi başlatma

Bu makalede, Azure SQL veritabanı 'nda [Azure Portal](https://portal.azure.com) kullanarak [tek ve havuza alınmış veritabanları için etkin Coğrafi çoğaltmayı](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) yapılandırma ve yük devretmeyi başlatma gösterilmektedir.

Tek ve havuza alınmış veritabanlarına sahip otomatik yük devretme grupları hakkında daha fazla bilgi için bkz. [tek ve havuza alınmış veritabanlarıyla yük devretme grupları kullanmanın en iyi yöntemleri](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Yönetilen örneklerle otomatik yük devretme grupları hakkında daha fazla bilgi için bkz. [yönetilen örneklerle yük devretme grupları kullanmanın en iyi yöntemleri](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Ön koşullar

Azure portal kullanarak etkin Coğrafi çoğaltmayı yapılandırmak için aşağıdaki kaynağa ihtiyacınız vardır:

* Azure SQL veritabanı: farklı bir coğrafi bölgeye çoğaltmak istediğiniz birincil veritabanı.

> [!Note]
> Azure portal kullanırken, birincil ile aynı abonelik içinde yalnızca ikincil bir veritabanı oluşturabilirsiniz. İkincil veritabanının farklı bir abonelikte olması gerekiyorsa [create database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) veya [ALTER DATABASE Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)kullanın.

## <a name="add-a-secondary-database"></a>İkincil veritabanı Ekle

Aşağıdaki adımlar, coğrafi çoğaltma ortaklığında yeni bir ikincil veritabanı oluşturur.  

İkincil bir veritabanı eklemek için, aboneliğin sahibi veya ortak sahibi olmanız gerekir.

İkincil veritabanı, birincil veritabanıyla aynı ada sahiptir ve varsayılan olarak aynı hizmet katmanını ve işlem boyutunu içerir. İkincil veritabanı tek bir veritabanı veya havuza alınmış bir veritabanı olabilir. Daha fazla bilgi için bkz. [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md).
İkincil oluşturulup oluşturulduktan sonra veriler birincil veritabanından yeni ikincil veritabanına çoğaltılmaya başlar.

> [!NOTE]
> İş ortağı veritabanı zaten mevcutsa (örneğin, önceki coğrafi çoğaltma ilişkisini sonlandırmayla ilgili bir sonuç olarak), komut başarısız olur.

1. [Azure Portal](https://portal.azure.com), coğrafi çoğaltma için ayarlamak istediğiniz veritabanına gidin.
2. SQL veritabanı sayfasında, **coğrafi çoğaltma**' yı seçin ve ardından ikincil veritabanını oluşturmak için bölgeyi seçin. Birincil veritabanını barındıran bölge dışında herhangi bir bölgeyi seçebilirsiniz, ancak [eşleştirilmiş bölgeyi](../best-practices-availability-paired-regions.md)öneririz.

    ![Coğrafi çoğaltmayı yapılandırma](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. İkincil veritabanı için sunucu ve fiyatlandırma katmanını seçin ya da yapılandırın.

    ![İkincil yapılandırma](./media/sql-database-geo-replication-portal/create-secondary.png)
4. İsteğe bağlı olarak, elastik bir havuza ikincil bir veritabanı ekleyebilirsiniz. Bir havuzda ikincil veritabanı oluşturmak için **elastik havuz** ' a tıklayın ve hedef sunucuda bir havuz seçin. Hedef sunucuda bir havuz zaten var olmalıdır. Bu iş akışı havuz oluşturmaz.
5. İkincili eklemek için **Oluştur** ' a tıklayın.
6. İkincil veritabanı oluşturulur ve dengeli dağıtım işlemi başlar.

    ![İkincil yapılandırma](./media/sql-database-geo-replication-portal/seeding0.png)
7. Dengeli dağıtım işlemi tamamlandığında, ikincil veritabanı durumunu görüntüler.

    ![Dengeli dağıtım Tamam](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Yük devretme başlatma

İkincil veritabanı birincil olacak şekilde değiştirilebilir.  

1. [Azure Portal](https://portal.azure.com), coğrafi çoğaltma ortaklığının birincil veritabanına gidin.
2. SQL veritabanı dikey penceresinde, **coğrafi çoğaltma** > **Tüm ayarlar** ' ı seçin.
3. **İkincil** öğeler listesinde, yeni birincil olmasını istediğiniz veritabanını seçin ve **Yük devretme**' ye tıklayın.

    ![Yükünü](./media/sql-database-geo-replication-failover-portal/secondaries.png)
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

    ![İkincili kaldır](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Bir onay penceresi açılır. Veritabanını coğrafi çoğaltma ortaklığından kaldırmak için **Evet** ' e tıklayın. (Herhangi bir çoğaltmanın parçası olmayan bir okuma-yazma veritabanına ayarlayın.)

## <a name="next-steps"></a>Sonraki adımlar

* Etkin coğrafi çoğaltma hakkında daha fazla bilgi için bkz. [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md).
* Otomatik yük devretme grupları hakkında bilgi edinmek için bkz. [otomatik yük devretme grupları](sql-database-auto-failover-group.md)
* İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](sql-database-business-continuity.md).
