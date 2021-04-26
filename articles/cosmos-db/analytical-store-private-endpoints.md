---
title: Azure Cosmos DB analitik depo için özel uç noktaları yapılandırın.
description: Ağ erişimini kısıtlamak için Azure Cosmos DB analitik depo için yönetilen özel uç noktaları ayarlamayı öğrenin.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: fd0b3ada5fec283562cee9727e3f805a7d34c532
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479058"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitik depo için Azure özel bağlantısını yapılandırma
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Bu makalede, Azure Cosmos DB analitik depo için yönetilen özel uç noktaları ayarlamayı öğreneceksiniz. İşlem deposunu kullanıyorsanız, bkz. [işlem deposu Için özel uç noktalar](how-to-configure-private-endpoints.md) . [Yönetilen özel uç noktaları](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)kullanarak, Azure Cosmos DB analitik deponuzda ağ erişimini Azure SYNAPSE çalışma alanınız ile Ilişkili bir yönetilen sanal ağa kısıtlayabilirsiniz. Yönetilen özel uç noktalar, analitik deponuza özel bir bağlantı kurar.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Analitik depo için özel bir uç noktayı etkinleştirme

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Azure SYNAPSE Analytics çalışma alanını yönetilen bir sanal ağla ayarlama

[Azure SYNAPSE Analytics 'te Data-exfiltration özelliği etkinken bir çalışma alanı oluşturun.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) [Veri alma koruması](../synapse-analytics/security/workspace-data-exfiltration-protection.md)sayesinde, kötü niyetli kullanıcıların Azure kaynaklarınızdan kuruluşunuzun kapsamı dışındaki konumlara veri kopyalayamıyor veya bu verileri aktarabilmesi için emin olabilirsiniz.

Aşağıdaki erişim kısıtlamaları, bir Azure SYNAPSE Analytics çalışma alanı için veri-değişim koruması açık olduğunda geçerlidir:

* Azure SYNAPSE Analytics için Azure Spark kullanıyorsanız, erişime yalnızca Azure Cosmos DB analitik mağaza için onaylanan yönetilen özel uç noktalara izin verilir.

* SYNAPSE sunucusuz SQL havuzları kullanıyorsanız, Azure SYNAPSE bağlantısını kullanarak herhangi bir Azure Cosmos DB hesabını sorgulayabilirsiniz. Ancak, [Select (CETAS) olarak dış tablolar](../synapse-analytics/sql/develop-tables-cetas.md) oluşturan yazma istekleri yalnızca çalışma alanı sanal ağındaki onaylanan özel uç noktalara izin verilir.

> [!NOTE]
> Çalışma alanı oluşturulduktan sonra yönetilen sanal ağ ve veri dönüştürme yapılandırmasını değiştiremezsiniz.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitik depo için yönetilen özel uç nokta ekleme

1. [Azure portal](https://portal.azure.com/) oturum açın.

1. Azure portal, SYNAPSE Analytics çalışma alanınıza gidin ve **genel bakış** bölmesini açın.

1. **Başlarken bölmesine giderek** SYNAPSE Studio 'yu başlatın ve **SYNAPSE Studio 'Yu aç** altında **Aç** ' ı seçin.

1. SYNAPSE Studio 'da **Yönet** sekmesini açın.

1. **Yönetilen özel uç noktalara** gidin ve **Yeni** ' yi seçin

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Analitik depo için yeni bir özel uç nokta oluşturun." border="true":::

1. **Devam**> **Azure Cosmos DB (SQL API)** hesap türünü seçin.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Özel bir uç nokta oluşturmak için Azure Cosmos DB SQL API 'sini seçin." border="true":::

1. **Yeni yönetilen özel uç nokta** formunu aşağıdaki ayrıntılarla doldurun:

   * Yönetilen özel uç noktanız için **ad** adı. Bu ad oluşturulduktan sonra güncelleştirilemez.
   * **Açıklama** -özel uç noktanızı belirlemek için kolay bir açıklama sağlayın.
   * **Azure aboneliği** -Azure aboneliklerinizdeki kullanılabilir hesaplar listesinden bir Azure Cosmos DB hesabı seçin.
   * **Azure Cosmos DB hesap adı** -SQL veya MongoDB türünde mevcut bir Azure Cosmos DB hesabı seçin.
   * **Hedef alt kaynak** -şu seçeneklerden birini seçin: **analitik**: Azure Cosmos DB analitik depo için özel uç nokta eklemek istiyorsanız.
     **SQL** (veya **MongoDB**): OLTP veya işlemsel hesap uç noktası eklemek istiyorsanız.

   > [!NOTE]
   > Azure SYNAPSE Analytics çalışma alanındaki aynı Azure Cosmos DB hesabına hem işlem deposu hem de analitik mağaza özel uç noktaları ekleyebilirsiniz. Yalnızca analitik sorgular çalıştırmak istiyorsanız, yalnızca analitik özel uç noktasını eşlemek isteyebilirsiniz.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Hedef alt kaynak için analitik öğesini seçin." border="true":::

1. Oluşturduktan sonra özel uç nokta adına gidin ve **Azure Portal onayları Yönet '** i seçin.

1. Azure Cosmos DB hesabınıza gidin, Özel uç noktayı seçin ve **Onayla**' yı seçin.

1. SYNAPSE Analytics çalışma alanına geri gidin ve **yönetilen özel uç noktalar** bölmesinde **Yenile** ' ye tıklayın. Özel uç noktanın **onaylanmış** durumda olduğunu doğrulayın.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Özel uç noktanın onaylandığını doğrulayın." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Azure SYNAPSE Analytics için Apache Spark kullanma

Data-exfiltration koruması açıkken bir Azure SYNAPSE çalışma alanı oluşturduysanız, SYNAPSE Spark 'tan Azure Cosmos DB hesaplarına giden erişim varsayılan olarak engellenir. Ayrıca, Azure Cosmos DB zaten mevcut bir özel uç noktası varsa, SYNAPSE Spark 'ın buna erişimi engellenir.

Azure Cosmos DB verilere erişime izin vermek için:

* Azure Cosmos DB verileri sorgulamak için Azure SYNAPSE bağlantısı kullanıyorsanız, Azure Cosmos DB hesabı için yönetilen bir **analitik** özel uç nokta ekleyin.

* İşlem deposuna toplu yazma/okuma ve/veya akış yazma/okuma işlemleri kullanıyorsanız, Azure Cosmos DB hesabı için yönetilen bir *SQL* veya *MongoDB* özel uç noktası ekleyin. Ayrıca, aşağıdaki kod parçacığında gösterildiği gibi *Connectionmode* 'u *Gateway* olarak da ayarlamanız gerekir:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>SYNAPSE sunucusuz SQL havuzları kullanma

SYNAPSE sunucusuz SQL havuzları, yönetilen sanal ağa dağıtılmamış çok kiracılı yetenekler kullanır. Azure Cosmos DB hesabının özel bir uç noktası varsa, SYNAPSE sunucusuz SQL havuzunun, Azure Cosmos DB hesabındaki ağ yalıtımı denetimleri nedeniyle hesaba erişmesi engellenir.

Bu hesap için Ağ yalıtımını bir Synapse çalışma alanından yapılandırmak için:

1. Hesapta ayarı belirterek SYNAPSE çalışma alanının Azure Cosmos DB hesabına erişmesine izin verin `NetworkAclBypassResourceId` .

   **PowerShell'i kullanma**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Azure CLI’yı kullanma**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB hesabı ve Azure SYNAPSE Analytics çalışma alanı aynı Azure Active Directory (AD) kiracısında olmalıdır.

2. Artık, Azure SYNAPSE bağlantısı üzerinden T-SQL sorguları kullanarak sunucusuz SQL havuzlarından hesaba erişebilirsiniz. Ancak, analitik depodaki veriler için ağ yalıtımı sağlamak üzere bu hesap için bir **analitik** yönetilen özel uç noktası eklemeniz gerekir. Aksi takdirde, analitik depodaki verilerin genel erişime karşı engellenmeyecektir.

> [!IMPORTANT]
> Azure SYNAPSE bağlantısı kullanıyorsanız ve analitik depodaki verileriniz için ağ yalıtımına ihtiyacınız varsa, **analitik** yönetilen özel uç noktasını kullanarak Azure Cosmos DB hesabını SYNAPSE çalışma alanına eşlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure SYNAPSE Spark ile analitik depo sorgulama](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) ile çalışmaya başlama
* [Azure SYNAPSE sunucusuz SQL havuzları ile analitik depo sorgulama](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) ile çalışmaya başlama
