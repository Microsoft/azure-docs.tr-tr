---
title: 'Hızlı başlangıç: PowerShell ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme'
description: Redsıs olayları için Azure önbelleğine abone olmak, olayları bir Web kancasına göndermek ve bir Web uygulamasındaki olayları işlemek için Azure Event Grid kullanın.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fa1ae9a7a2200944bd5da0211be88ba4955e3d03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833895"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Hızlı başlangıç: PowerShell ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme

Azure Event Grid, bulut için bir olay oluşturma hizmetidir. Bu hızlı başlangıçta, Redsıs olayları için Azure önbelleğine abone olmak, bir olayı tetiklemek ve sonuçları görüntülemek için Azure PowerShell kullanacaksınız. 

Normalde olayları, olay verilerini işleyen ve eylemler gerçekleştiren bir uç noktaya gönderirsiniz. Ancak, bu hızlı başlangıcı basitleştirmek için, iletileri toplayacak ve görüntüleyecek bir Web uygulamasına olayları göndereceğiz. Bu hızlı başlangıçta açıklanan adımları tamamladığınızda, olay verilerinin Web uygulamasına gönderildiğini görürsünüz.

## <a name="setup"></a>Kurulum

Bu hızlı başlangıçta Azure PowerShell en son sürümünü çalıştırıyor olmanız gerekir. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell yükleyip yapılandırma](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Komutuyla Azure aboneliğinizde oturum açın `Connect-AzAccount` ve kimlik doğrulaması yapmak için ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

Bu örnek, **westus2** kullanır ve seçimi tamamında kullanılmak üzere bir değişkende depolar.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Event Grid konular ayrı Azure kaynakları olarak dağıtılır ve bir Azure Kaynak grubu altında sağlanması gerekir. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal koleksiyondur.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun.

Aşağıdaki örnek **westus2** konumunda **gridResourceGroup** adlı bir kaynak grubu oluşturur.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Redsıs örneği için Azure önbelleği oluşturma 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
PowerShell 'de bir önbellek örneği oluşturma hakkında daha fazla bilgi için [Azure PowerShell başvurusuna](/powershell/module/az.rediscache/new-azrediscache)bakın. 

## <a name="create-a-message-endpoint"></a>İleti uç noktası oluşturma

Konuya abone olmadan önce olay iletisi için uç noktayı oluşturalım. Normalde, olay verileri temelinde uç nokta eylemleri gerçekleştirir. Bu hızlı başlangıcı basitleştirmek için, olay iletilerini görüntüleyin bir [önceden oluşturulmuş web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtırsınız. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

`<your-site-name>` değerini web uygulamanız için benzersiz bir adla değiştirin. Web uygulaması adı bir DNS girdisinin parçası olduğundan benzersiz olmalıdır.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarıyla gerçekleştirildikten sonra, web uygulamanızı görüntüleyip çalıştığından emin olun. Web tarayıcısında şu adrese gidin: `https://<your-site-name>.azurewebsites.net`

Şu anda iletilerin görüntülenmediği siteyi görüyor olmalısınız.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Event Grid Görüntüleyici sitesi boş.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Redsıs olayı için Azure önbelleğinize abone olma

Bu adımda, izlemek istediğiniz olayları Event Grid söylemek için bir konuya abone olacaksınız. Aşağıdaki örnek, oluşturduğunuz önbellek örneğine abone olur ve Web uygulamanızdan URL 'YI olay bildirimi için uç nokta olarak geçirir. Web uygulamanızın uç noktası `/api/updates/` sonekini içermelidir.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Web uygulamanızı yeniden görüntüleyin ve buna bir abonelik doğrulama olayının gönderildiğine dikkat edin. Göz simgesini seçerek olay verilerini genişletin. Uç noktanın olay verilerini almak istediğini doğrulayabilmesi için Event Grid doğrulama olayını gönderir. Web uygulaması aboneliği doğrulamak için kod içerir.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Görüntüleyici.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Redsıs için Azure önbelleğinden bir olay tetikleyin

Şimdi, Event Grid’in iletiyi uç noktanıza nasıl dağıttığını görmek için bir olay tetikleyelim.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
PowerShell 'de içeri aktarma hakkında daha fazla bilgi için [Azure PowerShell başvurusuna](/powershell/module/az.rediscache/import-azrediscache)bakın. 

Olayı tetiklediniz ve Event Grid, iletiyi abone olurken yapılandırdığınız uç noktaya gönderdi. Az önce gönderdiğiniz olayı görmek için web uygulamanızı görüntüleyin.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Kaynakları temizleme
Redsıs örneği ve olay aboneliği için bu Azure önbelleğiyle çalışmaya devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıçta oluşturduğunuz kaynakları silmek için aşağıdaki komutu kullanın.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Artık konular ve olay abonelikleri oluşturmayı bildiğinize göre, reddo olayları için Azure önbelleği hakkında daha fazla bilgi edinin ve Event Grid şunları yapmanıza yardımcı olabilir:

- [Redsıs olayları için Azure önbelleğine yeniden davranıma](cache-event-grid.md)
- [Event Grid Hakkında](../event-grid/overview.md)
