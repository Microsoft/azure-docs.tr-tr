---
title: Hizmet kaynağınızı bölgeler arasında taşıma
titleSuffix: Azure Cognitive Search
description: Bu makalede, Azure Bilişsel Arama kaynaklarınızı Azure bulutundaki bir bölgeden diğerine nasıl taşıyacağınız gösterilmektedir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/10/2020
ms.openlocfilehash: a5050958f01743ff3c6fdcdecfee3067b1d2073e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934277"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Bilişsel Arama hizmetinizi başka bir Azure bölgesine taşıyın

Bazen, müşteriler bir arama hizmetini başka bir bölgeye taşımayı ister. Şu anda, bu görevle ilgili yardım almak için yerleşik bir mekanizma veya araç yoktur, ancak bu makale aynı sonucu elde etmek için el ile yapılan adımları anlamanıza yardımcı olabilir.

> [!NOTE]
> Azure portal, tüm hizmetlerin bir **şablonu dışarı aktar** komutu vardır. Azure Bilişsel Arama söz konusu olduğunda, bu komut bir hizmetin temel tanımını (ad, konum, katman, çoğaltma ve bölüm sayısı) üretir, ancak hizmetinizin içeriğini tanımaz, ya da anahtar, rol ya da Günlükler üzerinde devam etmez. Komut var olsa da, bir arama hizmeti taşımak için kullanılması önerilmez.

## <a name="prerequisites"></a>Önkoşullar

+ Hesabınızın kullandığı hizmetlerin ve özelliklerin hedef bölgede desteklendiğinden emin olun.

+ Önizleme özellikleri için aboneliğinizin hedef bölge için onaylandığından emin olun.

## <a name="prepare-and-move"></a>Hazırlama ve taşıma

1. Yalnızca Azure Bilişsel Arama daha fazlasını taşımanız gerekiyorsa, bir hizmetin yeniden konumlandırılmalarından tam etkileri anlamak için bağımlılıkları ve ilgili hizmetleri belirler.

   Azure depolama, günlüğe kaydetme, bilgi deposu oluşturma, yaygın olarak kullanılan bir dış veri kaynağı ve AI zenginleştirme ve dizin oluşturma için kullanılır. Bilişsel hizmetler, AI zenginleştirme 'nin bir bağımlılığı. Yalnızca bilişsel hizmetler ve arama hizmetinizin, AI zenginleştirme kullanıyorsanız aynı bölgede olması gerekir.

1. Ne taşınacağını bilmeniz için hizmette tüm nesnelerin envanterini oluşturun: dizinler, eş anlamlı haritalar, Dizin oluşturucular, veri kaynakları, becerileri. Günlüğe kaydetmeyi etkinleştirdiyseniz, bir geçmiş kaydı için ihtiyaç duyduğunuz tüm raporları oluşturun ve arşivleyin.

1. Yeni bölgedeki Azure Bilişsel Arama ve ilgili hizmetlerin kullanılabilirliğini sağlamak için fiyatlandırma ve kullanılabilirliği denetleyin. Özelliklerin büyük bölümü tüm bölgelerde kullanılabilir, ancak bazı Önizleme özelliklerinin kısıtlı kullanılabilirliği vardır.

1. Yeni bölgede bir hizmet oluşturun ve mevcut dizinler, eş anlamlı eşlemeler, Dizin oluşturucular, veri kaynakları ve becerileri kaynak kodundan yeniden yayımlayın. Mevcut adı yeniden kullanabilmeniz için hizmet adlarının benzersiz olması gerektiğini unutmayın. Bilişsel hizmetler bağlantılarının aynı bölge gereksinimi bakımından hala geçerli olup olmadığını görmek için her beceri denetleyin. Ayrıca, bilgi depoları oluşturulduysa, farklı bir hizmet kullanıyorsanız Azure Storage bağlantı dizelerini kontrol edin.

1. Varsa dizinleri ve bilgi depolarını yeniden yükleyin. JSON verilerini bir dizine göndermek için uygulama kodunu kullanacaksınız veya dış kaynaklardan gelen belgeleri çekmek için dizin oluşturucularının yeniden çalıştırılması gerekir. 

1. Günlüğe kaydetmeyi etkinleştirin ve bunları kullanıyorsanız, güvenlik rollerini yeniden oluşturun.

1. İstemci uygulamalarını ve test paketlerini yeni hizmet adı ve API anahtarlarını kullanacak şekilde güncelleştirin ve tüm uygulamaları test edin.

## <a name="discard-or-clean-up"></a>Atma veya temizleme

Yeni hizmet tam olarak sınandıktan ve çalışır duruma getirildikten sonra eski hizmeti silin. Hizmetin silinmesi, hizmetle ilişkili tüm içeriği otomatik olarak siler.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bağlantılar, yukarıda özetlenen adımları tamamlarken daha fazla bilgi bulmanıza yardımcı olabilir.

+ [Azure Bilişsel Arama fiyatlandırması ve bölgeleri](https://azure.microsoft.com/pricing/details/search/)
+ [Katman seçme](search-sku-tier.md)
+ [Arama hizmeti oluşturma](search-create-service-portal.md)
+ [Arama belgelerini yükleme](search-what-is-data-import.md)
+ [Günlü kaydını etkinleştir](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is allowlisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->