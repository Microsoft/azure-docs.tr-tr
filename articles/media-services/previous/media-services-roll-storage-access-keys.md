---
title: Depolama erişim anahtarlarını tamamladıktan sonra Media Services güncelleştirin | Microsoft Docs
description: Bu makaleler, depolama erişim tuşlarından sonra Media Services güncelleştirme hakkında rehberlik sağlar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 1cebe0fda7da97933fc94082a62c671535fe689b
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015795"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Depolama erişim anahtarlarını dağıttıktan sonra Media Services'i güncelleştirme 

Yeni bir Azure Media Services (AMS) hesabı oluşturduğunuzda, Ayrıca medya içeriğinizi depolamak için kullanılan bir Azure depolama hesabı seçmeniz istenir. Media Services hesabınıza birden fazla depolama hesabı ekleyebilirsiniz. Bu makalede, depolama anahtarlarının nasıl döndürüleceğini gösterilmektedir. Ayrıca, bir medya hesabına depolama hesaplarının nasıl ekleneceğini gösterir. 

Bu makalede açıklanan eylemleri gerçekleştirmek için [Azure Resource Manager API 'leri](/rest/api/media/operations/azure-media-services-rest-api-reference) ve [PowerShell](https://docs.microsoft.com/powershell/module/az.media)'i kullanmanız gerekir.  Daha fazla bilgi için bkz. [Azure kaynaklarını PowerShell ile yönetme ve Kaynak Yöneticisi](../../azure-resource-manager/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış

Yeni bir depolama hesabı oluşturulduğunda, Azure Depolama Hesabınıza erişimin kimliğini doğrulamak için kullanılan 2 512 bitlik depolama erişim anahtarları oluşturur. Depolama bağlantılarınızı daha güvenli tutmak için depolama erişim anahtarınızı düzenli olarak yeniden oluşturmanız ve döndürmenizi öneririz. İki erişim anahtarı (birincil ve ikincil), diğer erişim anahtarını yeniden oluştururken tek bir erişim anahtarı kullanarak depolama hesabı bağlantılarını korumanıza olanak tanımak için sağlanır. Bu yordama "sıralı erişim tuşları" da denir.

Media Services, kendisine sunulan bir depolama anahtarına bağlıdır. Özellikle, varlıklarınızı akışa almak veya indirmek için kullanılan konum belirleyicilerinin belirtilen depolama erişim anahtarına bağlı olması gerekir. Bir AMS hesabı oluşturulduğunda, birincil depolama erişim anahtarı için varsayılan olarak bir bağımlılık alır, ancak bir kullanıcı olarak AMS 'nin sahip olduğu depolama anahtarını güncelleştirebilirsiniz. Bu makalede açıklanan adımları izleyerek Media Services hangi anahtarın kullanılacağını öğrendiğinizden emin olmanız gerekir.  

>[!NOTE]
> Birden çok depolama hesabınız varsa, bu yordamı her bir depolama hesabıyla gerçekleştirirsiniz. Depolama anahtarlarını döndürme sırası düzeltilmez. Önce ikincil anahtarı, ardından birincil anahtarı döndürebilir veya bunun tersini yapabilirsiniz.
>
> Bir üretim hesabındaki Bu makalede açıklanan adımları yürütmeden önce, bunları üretim öncesi bir hesapta test ettiğinizden emin olun.
>

## <a name="steps-to-rotate-storage-keys"></a>Depolama anahtarlarını döndürme adımları 
 
 1. Depolama hesabı birincil anahtarını PowerShell cmdlet 'i veya [Azure](https://portal.azure.com/) portalı üzerinden değiştirin.
 2. Medya hesabının depolama hesabı anahtarlarını seçmesini zorlamak için uygun params ile Sync-AzMediaServiceStorageKeys cmdlet 'ini çağırın
 
    Aşağıdaki örnek, anahtarların depolama hesaplarına nasıl eşitleneceğini gösterir.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Bir saat bekleyin. Akış senaryolarının çalıştığını doğrulayın.
 4. Depolama hesabı ikincil anahtarını PowerShell cmdlet 'i veya Azure portal ile değiştirin.
 5. Medya hesabının yeni depolama hesabı anahtarlarını seçmesini zorlamak için uygun params ile Sync-AzMediaServiceStorageKeys PowerShell 'i çağırın. 
 6. Bir saat bekleyin. Akış senaryolarının çalıştığını doğrulayın.
 
### <a name="a-powershell-cmdlet-example"></a>PowerShell cmdlet örneği 

Aşağıdaki örnek, depolama hesabının nasıl alınacağını ve AMS hesabıyla nasıl eşitleneceğini gösterir.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>AMS hesabınıza depolama hesapları ekleme adımları

Aşağıdaki makalede, AMS hesabınıza depolama hesaplarının nasıl ekleneceği gösterilmektedir: [Media Services hesabına birden çok depolama hesabı ekleyin](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Bilgilendirme
Bu belgeyi oluşturmak için katkıda bulunan şu kişileri bildirmek istiyoruz: Cenk dingiloglu, Milan gada, Seva Titov.
