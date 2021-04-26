---
title: VNet ve güvenlik duvarının arkasındaki depolama hesabına yönelik denetim
description: Sanal ağ ve güvenlik duvarının arkasındaki bir depolama hesabında veritabanı olaylarını yazmak için denetimi yapılandırma
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 908c9f1d05c83eaa58f77b79a32d956898c35076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93348262"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>VNet ve güvenlik duvarının arkasındaki depolama hesabına yönelik denetim yazma
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


[Azure SQL veritabanı](sql-database-paas-overview.md) ve [Azure SYNAPSE Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) için denetim, bir sanal ağ ve güvenlik duvarının arkasındaki bir [Azure depolama hesabına](../../storage/common/storage-account-overview.md) veritabanı olaylarının yazılmasını destekler.

Bu makalede, bu seçenek için Azure SQL veritabanı ve Azure depolama hesabı 'nı yapılandırmanın iki yolu açıklanmaktadır. İlki Azure portal kullanır, ikincisi REST kullanır.

## <a name="background"></a>Arka Plan

[Azure sanal ağı (VNet)](../../virtual-network/virtual-networks-overview.md) , Azure 'daki özel ağınız için temel yapı taşdır. VNet, Azure sanal makineler (VM) gibi birçok Azure Kaynak türünün birbirleriyle güvenli bir şekilde iletişim kurmasına olanak sağlar, internet ve şirket içi ağlar. VNet, kendi veri merkezinizdeki geleneksel bir ağa benzerdir, ancak ölçek, kullanılabilirlik ve yalıtım gibi Azure altyapısının ek avantajlarından yararlanır.

VNet kavramları, En Iyi uygulamalar ve çok daha fazlası hakkında daha fazla bilgi edinmek için bkz. [Azure sanal ağı nedir?](../../virtual-network/virtual-networks-overview.md).

Sanal ağ oluşturma hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: Azure Portal kullanarak sanal ağ oluşturma](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Önkoşullar

VNet veya güvenlik duvarının arkasındaki bir depolama hesabına yazma denetimi için aşağıdaki Önkoşullar gereklidir:

> [!div class="checklist"]
>
> * Genel amaçlı v2 depolama hesabı. Genel amaçlı bir v1 veya blob depolama hesabınız varsa, [genel amaçlı v2 depolama hesabına yükseltin](../../storage/common/storage-account-upgrade.md). Daha fazla bilgi için bkz. [depolama hesabı türleri](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Depolama hesabının aynı abonelikte ve [mantıksal SQL Server](logical-servers.md)aynı konumda olması gerekir.
> * Azure depolama hesabı gerekir `Allow trusted Microsoft services to access this storage account` . Bunu depolama hesabı **güvenlik duvarları ve sanal ağlarda** ayarlayın.
> * `Microsoft.Authorization/roleAssignments/write`Seçili depolama hesabı üzerinde izninizin olması gerekir. Daha fazla bilgi için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Azure portalında yapılandırma

Aboneliğinizle [Azure Portal](https://portal.azure.com) bağlayın. Kaynak grubuna ve sunucusuna gidin.

1. Güvenlik başlığı altında **Denetim** ' e tıklayın. **Açık** seçeneğini belirleyin.

2. **Depolama**’yı seçin. Günlüklerin kaydedileceği depolama hesabını seçin. Depolama hesabı, [Önkoşullar](#prerequisites)bölümünde listelenen gereksinimlere uymalıdır.

3. Açık **depolama ayrıntıları**

  > [!NOTE]
  > Seçili depolama hesabı VNet 'in arkasındaysa aşağıdaki iletiyi görürsünüz:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Bu iletiyi görmüyorsanız, depolama hesabı bir sanal ağın arkasında değildir.

4. Bekletme dönemi için gün sayısını seçin. Daha sonra, **Tamam**'a tıklayın. Saklama süresinden daha eski Günlükler silinir.

5. Denetim ayarlarınızda **Kaydet** ' i seçin.

Bir VNet veya güvenlik duvarının arkasındaki depolama hesabına yazmak için denetimi başarıyla yapılandırdınız.

## <a name="configure-with-rest-commands"></a>REST komutlarıyla yapılandırma

Azure portal kullanımına alternatif olarak, VNet ve güvenlik duvarının arkasındaki bir depolama hesabında veritabanı olaylarını yazmak üzere denetim yapılandırmak için REST komutlarını kullanabilirsiniz.

Bu bölümdeki örnek betikler, çalıştırmadan önce betiği güncelleştirmenizi gerektirir. Betiklerdeki aşağıdaki değerleri değiştirin:

|Örnek değer|Örnek açıklama|
|:-----|:-----|
|`<subscriptionId>`| Azure abonelik KIMLIĞI|
|`<resource group>`| Kaynak grubu|
|`<logical SQL Server>`| Sunucu adı|
|`<administrator login>`| Yönetici hesabı |
|`<complex password>`| Yönetici hesabı için karmaşık parola|

SQL denetimini, bir VNet veya güvenlik duvarının arkasındaki bir depolama hesabına olay yazacak şekilde yapılandırmak için:

1. Sunucunuzu Azure Active Directory (Azure AD) ile kaydedin. PowerShell veya REST API kullanın.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST API**](/rest/api/sql/servers/createorupdate):

   Örnek istek

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   İstek gövdesi

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
     }
   }
   ```

2. [Azure portalını](https://portal.azure.com) açın. Depolama hesabınıza gidin. **Access Control (IAM)** öğesini bulun ve **rol ataması Ekle**' ye tıklayın. Önceki adımda olduğu gibi Azure Active Directory (Azure AD) ile kaydettiğiniz veritabanını barındıran sunucuya **Depolama Blobu verileri katılımcısı** Azure rolü atayın.

   > [!NOTE]
   > Yalnızca sahibi ayrıcalığına sahip Üyeler bu adımı gerçekleştirebilir. Çeşitli Azure yerleşik rolleri için [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md)' ne bakın.

3. *Storageaccountaccesskey* belirtmeden [sunucunun blob denetim ilkesini](/rest/api/sql/server%20auditing%20settings/createorupdate)yapılandırın:

   Örnek istek

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   İstek gövdesi

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

- [Veritabanı denetim Ilkesi oluştur veya güncelleştir (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Sunucu denetim Ilkesi oluştur veya güncelleştir (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

Aşağıdaki örnekte gösterildiği gibi [Azure Resource Manager](../../azure-resource-manager/management/overview.md) şablonu kullanarak sanal ağ ve güvenlik duvarının arkasındaki bir depolama hesabında veritabanı olaylarını yazmak için denetim yapılandırabilirsiniz:

> [!IMPORTANT]
> Sanal ağın ve güvenlik duvarının arkasındaki depolama hesabını kullanmak için **isStorageBehindVnet** parametresini true olarak ayarlamanız gerekir

- [Denetim günlüklerini blob depolamaya yazmak için etkinleştirilen bir Azure SQL Server dağıtma](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> Bağlantılı örnek, bir dış ortak havuzda bulunur ve ' olduğu gibi ', garanti olmadan ve herhangi bir Microsoft destek programı/hizmeti kapsamında desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

* [PowerShell kullanarak bir sanal ağ hizmeti uç noktası ve ardından Azure SQL veritabanı için bir sanal ağ kuralı oluşturun.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Sanal ağ kuralları: REST API 'Ler ile Işlemler](/rest/api/sql/virtualnetworkrules)
* [Sunucular için sanal ağ hizmet uç noktalarını ve kurallarını kullanma](vnet-service-endpoint-rule-overview.md)
