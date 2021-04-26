---
title: Azure SQL yönetilen örneği 'nde Machine Learning Services
description: Bu makalede, Azure SQL yönetilen örneği 'ne genel bakış veya Machine Learning Services sağlanmaktadır.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599570"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği 'nde Machine Learning Services

Machine Learning Services, hem Python hem de R betiklerini destekleyen, veritabanı içi makine öğrenimi sağlayan Azure SQL yönetilen örneği 'nin bir özelliğidir. Özelliği, yüksek performanslı tahmine dayalı analiz ve makine öğrenimi için Microsoft Python ve R paketleri içerir. İlişkisel veriler saklı yordamlar aracılığıyla betiklerde, Python veya R deyimlerini içeren T-SQL betiği veya T-SQL içeren Python veya R kodu kullanılarak kullanılabilir.

## <a name="what-is-machine-learning-services"></a>Machine Learning Services nedir?

Azure SQL yönetilen örneği 'nde Machine Learning Services, veritabanında Python ve R betikleri çalıştırmanızı sağlar. Bu özelliği kullanarak verileri hazırlayın, temizleyebilir, özellik Mühendisliği yapabilir, makine öğrenimi modellerini bir veritabanı içinde eğitin, değerlendirebilir ve dağıtabilirsiniz. Özelliği, verilerin bulunduğu yerde betikleri çalıştırır ve verilerin ağ üzerinden başka bir sunucuya aktarımını ortadan kaldırır.

Azure SQL yönetilen örneği 'nde R/Python desteğiyle Machine Learning Services kullanın:

- **Veri hazırlama ve genel amaçlı veri işleme yapmak Için r ve Python betiklerini çalıştırın** . artık, r/Python betiklerinizi, verileri başka bir sunucuya taşımak yerine r ve Python betikleri çalıştırmak yerine verilerinizin bulunduğu Azure SQL yönetilen örneğine getirebilirsiniz. Gecikme, güvenlik ve uyumlulukla ilgili veri taşıma ve ilişkili sorunlar gereksinimini ortadan kaldırabilirsiniz.

- **Makine öğrenimi modellerini veritabanında eğitme** -herhangi bir açık kaynak algoritmasını kullanarak modeller eğitebilirsiniz. Veritabanı dışına çıkan örnek veri kümelerine güvenmek yerine, öğreticinizi kolayca veri kümesinin tamamına ölçeklendirebilirsiniz.

- **Modellerinizi ve betikleri saklı yordamlar halinde üretime dağıtma** -betikler ve eğitilen modeller yalnızca T-SQL saklı yordamlarına katıştırarak çalıştırılabilir. Azure SQL yönetilen örneği 'ne bağlanan uygulamalar, yalnızca bir saklı yordam çağırarak, bu modellerdeki tahminlerden ve zekadan yararlanabilir. Ayrıca, yüksek oranda eşzamanlı gerçek zamanlı Puanlama senaryolarında hızlı Puanlama için modelleri kullanıma hazır hale getirmek üzere yerel T-SQL tahmın işlevini de kullanabilirsiniz.

Python ve R 'nin temel dağıtımları Machine Learning Services eklenmiştir. Microsoft paketlerine yönelik iptallerini ve microsoftml 'yi Python için, Microsoft [paketlerine,](/sql/machine-learning/r/ref-r-revoscaler) [microsoftml](/sql/machine-learning/r/ref-r-microsoftml), [olapr](/sql/machine-learning/r/ref-r-olapr)ve R için [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) gibi açık kaynaklı paketleri ve çerçeveleri yükleyebilir ve bu [paketlerin yanı](/sql/machine-learning/python/ref-py-revoscalepy) sıra, Microsoft paketlerine yönelik iptallerini ve [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) 'yi de kullanabilirsiniz.

## <a name="how-to-enable-machine-learning-services"></a>Machine Learning Services etkinleştirme

Aşağıdaki SQL komutlarıyla genişletilebilirlik etkinleştirerek Azure SQL yönetilen örneği 'nde Machine Learning Services etkinleştirebilirsiniz (SQL yönetilen örneği yeniden başlatılır ve birkaç saniye boyunca kullanılamaz):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Bu komutun SQL yönetilen örnek kaynaklarını nasıl etkilediği hakkında daha fazla bilgi için bkz. [Resource idare](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Yük devretme grubunda Machine Learning Services etkinleştirme

Bir [Yük devretme grubunda](failover-group-add-instance-tutorial.md), sistem veritabanları ikincil örneğe çoğaltılmaz (daha fazla bilgi için [Yük devretme gruplarının kısıtlamalarına](../database/auto-failover-group-overview.md#limitations-of-failover-groups) bakın).

Kullanmakta olduğunuz yönetilen örnek bir yük devretme grubunun parçasıysa, şunları yapın:

- `sp_configure` `RECONFIGURE` Machine Learning Services sağlamak için yük devretme grubunun her bir örneğinde ve komutlarını çalıştırın.

- R/Python kitaplıklarını ana veritabanı yerine bir kullanıcı veritabanına yükler.

## <a name="next-steps"></a>Sonraki adımlar

- [SQL Server Machine Learning Services arasındaki önemli farklılıkları](machine-learning-services-differences.md)inceleyin.
- Machine Learning Services 'de Python kullanmayı öğrenmek için bkz. [Python betiklerini çalıştırma](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Machine Learning Services 'de R 'yi nasıl kullanacağınızı öğrenmek için bkz. [Run r Scripts](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Diğer SQL platformlarında makine öğrenimi hakkında daha fazla bilgi için bkz. [SQL Machine Learning belgeleri](/sql/machine-learning/index).
