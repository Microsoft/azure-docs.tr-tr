---
title: Azure Hibrit Avantajı
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL veritabanı ve SQL yönetilen örnek indirimleri için mevcut SQL Server lisanslarını kullanın.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: b5f85e0dcb8ca70d5773b8f1c3b53e0b449ef013
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779210"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hibrit Avantajı-Azure SQL veritabanı & SQL yönetilen örneği
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Sanal çekirdek tabanlı satın alma modelinin sağlanan işlem katmanında, [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kullanarak Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde indirimli ücretler için mevcut lisanslarınızı Exchange 'e dönüştürebilirsiniz. Bu Azure avantajı, Yazılım Güvencesi kapsamındaki SQL Server lisanslarınızı kullanarak SQL veritabanı & SQL yönetilen örneği 'ne en az %30 tasarruf etmenize olanak tanır. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) sayfasında, tasarrufları belirlemede yardımcı olması için bir Hesaplayıcı vardır.  Azure Hibrit Avantajı Azure SQL veritabanı sunucusuz için uygulanmadığını unutmayın.

> [!NOTE]
> Azure Hibrit Avantajı değiştirmek için kapalı kalma süresi gerekmez.

![sanal çekirdek Fiyatlandırma yapısı](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Bir lisans modeli seçin

Azure Hibrit Avantajı ile, yalnızca SQL Server veritabanı altyapısının kendisi için mevcut SQL Server lisansınızı kullanarak (temel Işlem fiyatlandırması) yalnızca temel alınan Azure altyapısı için ödeme yapmayı seçebilirsiniz veya hem temel alınan altyapı hem de SQL Server Lisansı (lisans dahil fiyatlandırması) için ödeme yapabilirsiniz.

Azure portal lisans modelinizi seçebilir veya değiştirebilirsiniz: 
- Yeni veritabanları için, oluşturma sırasında **temel bilgiler** sekmesinde **Veritabanını yapılandır** ' ı seçin ve para tasarrufu seçeneğini belirleyin.
- Mevcut veritabanları için, **Ayarlar** menüsünde **Yapılandır** ' ı seçin ve para tasarrufu seçeneğini belirleyin.

Ayrıca, aşağıdaki API 'lerden birini kullanarak yeni veya mevcut bir veritabanını da yapılandırabilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak lisans türünü ayarlamak veya güncelleştirmek için:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-Azsqlınstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanarak lisans türünü ayarlamak veya güncelleştirmek için:

- [az sql db create](/cli/azure/sql/db#az_sql_db_create)
- [az SQL mı oluştur](/cli/azure/sql/mi#az_sql_mi_create)
- [az SQL mı güncelleştirmesi](/cli/azure/sql/mi#az_sql_mi_update)

# <a name="rest-api"></a>[REST API](#tab/rest)

REST API kullanarak lisans türünü ayarlamak veya güncelleştirmek için:

- [Veritabanları-oluştur veya güncelleştir](/rest/api/sql/databases/createorupdate)
- [Veritabanları-Güncelleştir](/rest/api/sql/databases/update)
- [Yönetilen örnekler-oluştur veya güncelleştir](/rest/api/sql/managedinstances/createorupdate)
- [Yönetilen örnekler-güncelleştirme](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure Hibrit Avantajı sorular

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server için Azure Hibrit Avantajı birlikte çift kullanım hakları var mı?

Geçişlerin sorunsuz çalıştığından emin olmak için lisansın 180 gün süreyle çift kullanım haklarına sahip olursunuz. Bu 180 gün sonra, SQL veritabanı 'nda yalnızca bulutta SQL Server lisansını kullanabilirsiniz. Artık şirket içinde ve bulutta ikili kullanım haklarına sahip olmanız gerekmez.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server için Azure Hibrit Avantajı, lisans taşınabilirliği 'nden farklı midir?

Yazılım Güvencesi kapsamındaki müşterilere SQL Server için lisans taşınabilirliği avantajları sunuyoruz. Bu, lisanslarının iş ortağının paylaşılan sunucularına yeniden atanmasını sağlar. Bu avantajı Azure IaaS ve AWS EC2 üzerinde kullanabilirsiniz.

SQL Server için Azure Hibrit Avantajı, iki anahtar alanındaki lisans taşınabilirliğine göre farklılık gösterir:

- Yüksek oranda sanallaştırılmış iş yüklerini Azure 'a taşımak için ekonomik avantajlar sağlar. SQL Server Enterprise Edition müşterileri, Azure 'da, yüksek oranda sanallaştırılmış uygulamalar için şirket içinde sahip oldukları her çekirdeğe yönelik Genel Amaçlı SKU 'sunda dört çekirdek alabilir. Lisans Taşınabilirliği, sanallaştırılmış iş yüklerini buluta taşımak için özel maliyet avantajlarına izin vermez.
- Azure 'da (SQL yönetilen örneği) SQL Server ile yüksek oranda uyumlu bir PaaS hedefi sağlar.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server için Azure Hibrit Avantajı belirli hakları nelerdir?

SQL veritabanı ve SQL yönetilen örnek müşterileri, SQL Server için Azure Hibrit Avantajı ilişkili aşağıdaki haklara sahiptir:

|Lisans ayak izi|SQL Server için ne Azure Hibrit Avantajı?|
|---|---|
|SA ile SQL Server Enterprise sürümü çekirdek müşterileri|<li>Hiperscale, Genel Amaçlı veya İş Açısından Kritik SKU 'sunda taban ücret ödeyebilir</li><br><li>1 çekirdek şirket içi = 4 çekirdek, hiper ölçek SKU 'sunda</li><br><li>1 çekirdek şirket içi = Genel Amaçlı SKU 'da 4 çekirdek</li><br><li>1 çekirdek şirket içi = 1 çekirdek İş Açısından Kritik SKU 'da</li>|
|SA ile SQL Server Standard sürümü çekirdek müşterileri|<li>Hiperscale, Genel Amaçlı veya İş Açısından Kritik SKU 'sunda taban ücret ödeyebilir</li><br><li>1 çekirdek şirket içi = 1 çekirdek, Hiperscale SKU 'sunda</li><br><li>1 çekirdek şirket içi = 1 çekirdek Genel Amaçlı SKU 'da</li><br><li>4 çekirdek on-şirket içi = 1 çekirdek İş Açısından Kritik SKU 'da</li>|
|||


## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL dağıtım seçeneği seçme konusunda yardım için bkz. [Azure SQL 'de doğru dağıtım seçeneğini belirleyin](azure-sql-iaas-vs-paas-what-is-overview.md).
- SQL veritabanı ve SQL yönetilen örnek özelliklerinin karşılaştırması için bkz. SQL [veritabanı & SQL yönetilen örnek özellikleri](database/features-comparison.md).
