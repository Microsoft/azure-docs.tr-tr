---
title: Yönetilen örneği sildikten sonra bir alt ağı silme
description: Azure SQL veritabanı yönetilen örneğini sildikten sonra Azure sanal ağını silme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820458"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneğini sildikten sonra bir alt ağı silme

Bu makalede, içinde bulunan son Azure SQL veritabanı yönetilen örneğini sildikten sonra bir alt ağın el ile nasıl silineceği hakkında yönergeler verilmektedir.

Yönetilen örnekler [sanal kümelere](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)dağıtılır. Her sanal küme bir alt ağla ilişkilendirilir. Aynı alt ağda yönetilen örnekleri daha hızlı bir şekilde oluşturmanıza olanak tanımak için sanal küme, son örnek silinmeden sonra 12 saat sonra tasarıma devam ediyor. Boş bir sanal kümenin tutulması ücretsizdir. Bu süre boyunca sanal kümeye ilişkilendirilmiş alt ağa silinemez.

12 saat beklemek istemiyorsanız ve sanal kümeyi ve alt ağını daha erken silmeyi tercih ediyorsanız, bunu el ile yapabilirsiniz. Azure portal veya Sanal kümeler API 'sini kullanarak sanal kümeyi el ile silin.

> [!IMPORTANT]
> - Silme işleminin başarılı olabilmesi için sanal kümenin yönetilen örnek içermemesi gerekir. 
> - Sanal bir kümeyi silme işlemi, yaklaşık 1,5 saat boyunca uzun süredir çalışan bir işlemdir (Bu işlem, sanal kümenin portalda hala görülebilmesi için bkz. sanal küme silme zamanı için [yönetilen örnek yönetimi işlemleri](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) ) işlem tamamlandı.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Azure portal sanal kümeyi silme

Azure portal kullanarak bir sanal kümeyi silmek için sanal küme kaynaklarını arayın.

![Arama kutusuyla vurgulanan Azure portal ekran görüntüsü](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Silmek istediğiniz sanal kümeyi bulduktan sonra bu kaynağı seçin ve **Sil**' i seçin. Sanal küme silmeyi onaylamanız istenir.

![Azure portal Sanal kümeler panosu, silme seçeneği vurgulanmış şekilde ekran görüntüsü](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure portal bildirimler, sanal kümeyi silme isteğinin başarıyla gönderildiğini belirten bir onay gösterecektir. Silme işleminin kendisi, sanal kümenin portalda hala görünebileceği yaklaşık 1,5 saat boyunca sona acaktır. İşlem tamamlandıktan sonra, sanal küme artık görünür olmayacaktır ve bununla ilişkili alt ağ yeniden kullanım için serbest bırakılır.

> [!TIP]
> Sanal kümede gösterilen yönetilen örnek yoksa ve sanal kümeyi silemiyorsa devam eden bir devam eden örnek dağıtımına sahip olduğunuzdan emin olun. Bu, başlatılan ve iptal edilen dağıtımları devam etmektedir. Bunun nedeni, bu işlemlerin hala silme işleminden sonra sanal küme kilitlemeyi kullanacaktır. Örnek dağıtılan kaynak grubunun dağıtımlar sekmesini gözden geçirme işlemi, sürmekte olan dağıtımları gösterir. Bu durumda, dağıtımın tamamlanmasını bekleyin, yönetilen örneği ve sonra sanal kümeyi silin.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Sanal kümeyi API kullanarak silme

Bir sanal kümeyi API aracılığıyla silmek için, [Sanal kümeler silme yönteminde](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)belirtilen URI parametrelerini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [yönetilen örnek nedir?](sql-database-managed-instance.md).
- [Yönetilen örnekteki bağlantı mimarisi](sql-database-managed-instance-connectivity-architecture.md)hakkında bilgi edinin.
- [Yönetilen örnek için var olan bir sanal ağı değiştirme](sql-database-managed-instance-configure-vnet-subnet.md)hakkında bilgi edinin.
- Bir sanal ağ oluşturmayı, yönetilen bir örnek oluşturmayı ve bir veritabanını bir veritabanı yedeklemesinden geri yüklemeyi gösteren bir öğretici için bkz. [Azure SQL veritabanı yönetilen örneği oluşturma](sql-database-managed-instance-get-started.md).
- DNS sorunları için bkz. [Özel BIR DNS yapılandırma](sql-database-managed-instance-custom-dns.md).
