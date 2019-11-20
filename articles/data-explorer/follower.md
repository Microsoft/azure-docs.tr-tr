---
title: Azure Veri Gezgini veritabanlarını eklemek için izleyici veritabanı özelliğini kullanın
description: Azure Veri Gezgini veritabanı özelliğini kullanarak veritabanlarının nasıl ekleneceği hakkında bilgi edinin.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 2306b6cbdd347e3be9921b196ae06385ef5ca90a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083186"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Azure Veri Gezgini veritabanlarını eklemek için izleyici veritabanını kullanma

**İzleyici veritabanı** özelliği, farklı bir kümede bulunan bir veritabanını Azure Veri Gezgini kümenize eklemenize olanak tanır. **İzleyici veritabanı** *salt okuma* modunda iliştirilir, bu da verileri görüntülemeyi ve **öncü veritabanına**alınan verilerde sorgu çalıştırmayı mümkün hale getirir. İzleyici veritabanı, öncü veritabanlarındaki değişiklikleri eşitler. Eşitleme nedeniyle, veri kullanılabilirliğinin birkaç saniye boyunca birkaç dakika veri gecikmesi vardır. Gecikme süresinin uzunluğu, öncü veritabanı meta verilerinin genel boyutuna bağlıdır. Öncü ve izleyici veritabanları verileri getirmek için aynı depolama hesabını kullanır. Depolama, öncü veritabanına aittir. İzleyici veritabanı, verileri almak gerekmeden verileri görüntüler. Ekli veritabanı bir salt okuma veritabanı olduğundan, veritabanındaki veriler, tablolar ve ilkeler [önbelleğe alma ilkesi](#configure-caching-policy), [sorumlular](#manage-principals)ve [izinler](#manage-permissions)dışında değiştirilemez. Ekli veritabanları silinemez. Bunlar, öncü veya izleyici tarafından ayrılmaları gerekir ve bu öğeler silinebilir. 

İzleme özelliğini kullanarak bir veritabanını farklı bir kümeye eklemek, kuruluşlar ve takımlar arasında veri paylaşmak için altyapı olarak kullanılır. Bu özellik, üretim ortamının üretim dışı kullanım çalışmalarından korunmasını sağlamak için işlem kaynaklarını ayırt etmek için yararlıdır. İzleme, Azure Veri Gezgini kümesinin maliyetini verilerde sorgular çalıştıran tarafla ilişkilendirmek için de kullanılabilir.

## <a name="which-databases-are-followed"></a>Hangi veritabanları izlensin?

* Bir küme bir veritabanını, birkaç veritabanını veya öncü bir kümenin tüm veritabanlarını izleyebilir. 
* Tek bir küme, birden çok öncü kümeden veritabanlarını izleyebilir. 
* Bir küme, hem izleyici veritabanlarını hem de öncü veritabanlarını içerebilir

## <a name="prerequisites"></a>Önkoşullar

1. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
1. Öncü ve izleme için [küme ve DB oluşturun](/azure/data-explorer/create-cluster-database-portal) .
1. Alım [bölümünde ele](/azure/data-explorer/ingest-data-overview)alınan çeşitli yöntemlerden birini kullanarak [verileri](/azure/data-explorer/ingest-sample-data) öncü veritabanına alma.

## <a name="attach-a-database"></a>Veritabanı ekleme

Bir veritabanı eklemek için kullanabileceğiniz çeşitli yöntemler vardır. Bu makalede, veya Azure Resource Manager şablonu kullanarak C# bir veritabanı eklemeyi tartıştık. Bir veritabanı eklemek için, öncü kümede ve izleyici kümesinde izinlerinizin olması gerekir. İzinler hakkında daha fazla bilgi için bkz. [izinleri yönetme](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Kullanarak veritabanı iliştirmeC#

**Gerekli nal**

* [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)uygulamasını yükler.
* [Kimlik doğrulaması Için Microsoft. Rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)'ı yükler.


```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db" // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak veritabanı iliştirme

Bu bölümde, bir [Azure Resource Manager şablonu](../azure-resource-manager/resource-group-overview.md)kullanarak bir veritabanının nasıl iliştirileyeceğinizi öğrenirsiniz. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Şablonu dağıtma 

[Azure Portal](https://portal.azure.com) veya PowerShell 'i kullanarak Azure Resource Manager şablonu dağıtabilirsiniz.

   ![Şablon dağıtımı](media/follower/template-deployment.png)


|**Ayar**  |**Açıklama**  |
|---------|---------|
|İzleyici kümesi adı     |  İzleyici kümesinin adı       |
|Ekli veritabanı yapılandırması adı    |    Eklenmiş veritabanı yapılandırma nesnesinin adı. Ad, küme düzeyinde benzersiz olmalıdır.     |
|Veritabanı Adı     |      Takip edilecek veritabanının adı. Tüm öncü veritabanlarını izlemek isterseniz, ' * ' kullanın.   |
|Öncü küme kaynak KIMLIĞI    |   Öncü kümenin kaynak KIMLIĞI.      |
|Varsayılan asıl adlar değişiklik türü    |   Varsayılan asıl değişiklik türü. `Union`, `Replace` veya `None`olabilir. Varsayılan asıl değişiklik türü hakkında daha fazla bilgi için bkz. [asıl değişiklik türü denetim komutu](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Konum   |   Tüm kaynakların konumu. Öncü ve izleyici aynı konumda olmalıdır.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Veritabanının başarıyla eklendiğinden emin olun

Veritabanının başarıyla eklendiğinden emin olmak için, [Azure Portal](https://portal.azure.com)eklenen veritabanlarınızı bulun. 

1. İzleyici kümesine gidin ve **veritabanları** ' nı seçin.
1. Veritabanı listesinde yeni salt okuma veritabanlarını arayın.

    ![Salt okuma izleyici veritabanı](media/follower/read-only-follower-database.png)

Alternatif olarak:

1. Öncü kümesine gidin ve **veritabanlarını** seçin
2. İlgili veritabanlarının **başkalarıyla paylaşılan** olarak işaretlendiğinden emin olun > **Evet**

    ![Ekli veritabanlarını okuma ve yazma](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>İzleme veritabanını kullanarak ayırmaC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Ekli izleyici veritabanını izleyici kümesinden ayır

İzleyici kümesi, ekli herhangi bir veritabanını şu şekilde ayırabilir:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Ekli izleyici veritabanını öncü kümeden ayır

Öncü küme, eklenen herhangi bir veritabanını şu şekilde ayırabilir:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Sorumluları, izinleri ve önbelleğe alma ilkesini yönetme

### <a name="manage-principals"></a>Sorumluları yönetme

Bir veritabanı eklenirken, **varsayılan asıl adlar değişiklik türünü**belirtin. Varsayılan değer, [yetkili sorumlular](/azure/kusto/management/access-control/index#authorization) için öncü veritabanı koleksiyonunu tutuyor

|**Denetlenmesi** |**Açıklama**  |
|---------|---------|
|**Birleşim**     |   Ekli veritabanı sorumluları her zaman özgün veritabanı sorumlularını ek olarak, yeni asıl sorumluları da izleyici veritabanına ekler.      |
|**Değiştir**   |    Asıl veritabanından asıl öğeler devralınmaz. Eklenen veritabanı için yeni sorumlular oluşturulmalıdır. Birincil devralmayı engellemek için en az bir sorumlunun eklenmesi gerekir.     |
|**Yok.**   |   Ekli veritabanı sorumluları yalnızca, ek asıl olmayan özgün veritabanının sorumlularını içerir.      |

Yetkili sorumlularını yapılandırmak üzere denetim komutlarını kullanma hakkında daha fazla bilgi için bkz. [bir izleyici kümesini yönetmek Için denetim komutları](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>İzinleri Yönet

Salt okunurdur veritabanını yönetme izni tüm veritabanı türleriyle aynıdır. Bkz. [Azure Portal izinleri yönetme](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Önbelleğe alma ilkesini yapılandırma

İzleyici veritabanı yöneticisi, bağlı veritabanının veya barındırma kümesindeki tablolarının herhangi birinin [önbellek ilkesini](/azure/kusto/management/cache-policy) değiştirebilir. Varsayılan değer veritabanı ve tablo düzeyinde önbelleğe alma ilkelerinin öncü veritabanı koleksiyonunu tutuyor. Örneğin, aylık raporlama çalıştırmak için öncü veritabanında 30 günlük önbelleğe alma ilkesine ve sorun giderme için yalnızca son verileri sorgulamak üzere izleyici veritabanında üç günlük önbelleğe alma ilkesine sahip olabilirsiniz. İzleyici veritabanı veya tablosunda önbelleğe alma ilkesini yapılandırmak üzere denetim komutlarını kullanma hakkında daha fazla bilgi için, bkz. [bir izleyici kümesini yönetmek Için denetim komutları](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Sınırlamalar

* İzleme ve öncü kümelerin aynı bölgede olması gerekir.
* [Akış](/azure/data-explorer/ingest-data-streaming) alımı, izlenebilecek bir veritabanında kullanılamaz.
* Ayırmayı yapmadan önce farklı bir kümeye bağlı bir veritabanını silemezsiniz.
* Bir veritabanını kullanımdan çıkarmadan önce farklı bir kümeye eklenmiş bir kümeyi silemezsiniz.
* Bağlı izleyici veya öncü veritabanı olan bir kümeyi durduramazsınız. 

## <a name="next-steps"></a>Sonraki adımlar

* İzleme kümesi yapılandırma hakkında daha fazla bilgi için bkz. [bir izleyici kümesini yönetmek Için denetim komutları](/azure/kusto/management/cluster-follower).