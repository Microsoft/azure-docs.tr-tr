---
title: Depolama erişim tuşlarından sonra Media Services v3 güncelleştirme | Microsoft Docs
description: Bu makale, depolama erişim tuşlarından sonra Media Services v3 güncelleştirme hakkında rehberlik sağlar.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 385e6109a80c8e1e455f98b5d02ca5762f8051c7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282219"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Depolama erişim tuşlarından sonra Media Services v3 güncelleştirme

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Yeni bir Azure Media Services (AMS) hesabı oluştururken bir Azure depolama hesabı seçmeniz istenir.  Media Services hesabınıza birden fazla depolama hesabı ekleyebilirsiniz. Bu makalede, depolama anahtarlarının nasıl döndürüleceğini gösterilmektedir. Ayrıca, bir medya hesabına depolama hesaplarının nasıl ekleneceğini gösterir.

Bu makalede açıklanan eylemleri tamamlayabilmeniz için [Azure Resource Manager API 'leri](/rest/api/media/operations/azure-media-services-rest-api-reference) ve [PowerShell](/powershell/module/az.media)'i kullanmanız gerekir.  Daha fazla bilgi için bkz. [Azure kaynaklarını PowerShell ile yönetme ve Kaynak Yöneticisi](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Depolama erişim anahtarı oluşturma

Yeni bir depolama hesabı oluşturulduğunda, Azure Depolama Hesabınıza erişimin kimliğini doğrulamak için kullanılan 2 512 bitlik depolama erişim anahtarları oluşturur. Depolama bağlantılarınızı daha güvenli tutmak için depolama erişim anahtarınızı düzenli olarak yeniden oluşturun ve döndürün. İki erişim anahtarı (birincil ve ikincil), diğer erişim anahtarını yeniden oluştururken bir erişim anahtarı kullanarak depolama hesabı bağlantılarını korumanıza olanak tanımak için sağlanır. Bu yordama "sıralı erişim tuşları" da denir.

Media Services, kendisine sunulan bir depolama anahtarına bağlıdır. Özellikle, varlıklarınızı akışa almak veya indirmek için kullanılan konum belirleyicilerinin belirtilen depolama erişim anahtarına bağlı olması gerekir. Bir AMS hesabı oluşturulduğunda, birincil depolama erişim anahtarı için varsayılan olarak bir bağımlılık alır. Ancak, bir kullanıcı olarak, AMS 'nin sahip olduğu depolama anahtarını güncelleştirebilirsiniz. Aşağıdaki adımları izleyerek Media Services hangi anahtarın kullanılacağını bilmeniz gerekir:

>[!NOTE]
> Birden çok depolama hesabınız varsa, bu yordamı her bir depolama hesabıyla gerçekleştirirsiniz. Depolama anahtarlarını döndürme sırası düzeltilmez. Önce ikincil anahtarı, ardından birincil anahtarı döndürebilir veya bunun tersini yapabilirsiniz.
>
> Bir üretim hesabındaki adımları yürütmeden önce, bunları üretim öncesi bir hesapta test ettiğinizden emin olun.
>

## <a name="steps-to-rotate-storage-keys"></a>Depolama anahtarlarını döndürme adımları
 
 1. Depolama hesabı birincil anahtarını PowerShell cmdlet 'i veya [Azure](https://portal.azure.com/) portalı üzerinden değiştirin.
 2. `Sync-AzMediaServiceStorageKeys`Medya hesabının depolama hesabı anahtarlarını seçmesini zorlamak için cmdlet 'i uygun parametrelerle çağırın
 
    Aşağıdaki örnek, anahtarların depolama hesaplarına nasıl eşitleneceğini gösterir.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Bir saat bekleyin. Akış senaryolarının çalıştığını doğrulayın.
 4. Depolama hesabı ikincil anahtarını PowerShell cmdlet 'i veya Azure portal ile değiştirin.
 5. `Sync-AzMediaServiceStorageKeys`Medya hesabının yeni depolama hesabı anahtarlarını seçmesini zorlamak için uygun parametrelerle PowerShell 'i çağırın.
 6. Bir saat bekleyin. Akış senaryolarının çalıştığını doğrulayın.
 
### <a name="a-powershell-cmdlet-example"></a>PowerShell cmdlet örneği

Aşağıdaki örnek, depolama hesabının nasıl alınacağını ve AMS hesabıyla nasıl eşitleneceğini gösterir.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>AMS hesabınıza depolama hesapları ekleme adımları

Aşağıdaki makalede, AMS hesabınıza depolama hesaplarının nasıl ekleneceği gösterilmektedir: [bir Media Services hesabına birden çok depolama hesabı](storage-managing-multiple-storage-accounts-how-to.md)ekleme.
