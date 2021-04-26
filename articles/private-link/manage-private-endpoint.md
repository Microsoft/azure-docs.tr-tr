---
title: Azure 'da özel bir uç nokta bağlantısını yönetme
description: Azure 'da özel uç nokta bağlantılarını yönetmeyi öğrenin
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531381"
---
# <a name="manage-a-private-endpoint-connection"></a>Özel Uç Nokta bağlantısını yönetme
Azure özel bağlantısı, özel bağlantı hizmeti tüketicisinin hizmeti tüketmesi için hizmet sağlayıcısına bir bağlantı isteyebildiğini bir onay çağrı akışı modelinde çalışmaktadır. Hizmet sağlayıcı, tüketicinin bağlanmasına izin verip vermeyeceğine karar verebilir. Azure özel bağlantısı, hizmet sağlayıcılarının kaynakları üzerinde özel uç nokta bağlantısını yönetmesine olanak sağlar. Bu makale, Özel uç nokta bağlantılarının nasıl yönetileceği hakkında yönergeler sağlar.

![Özel uç noktaları yönetme](media/manage-private-endpoint/manage-private-endpoint.png)

Özel bağlantı hizmeti tüketicisinin aralarından seçim yapabileceğiniz iki bağlantı onay yöntemi vardır:
- **Otomatik**: hizmet TÜKETİCİSİNDE hizmet sağlayıcı KAYNAĞıNDA Azure RBAC izinleri varsa, tüketici otomatik onay yöntemini seçebilir. Bu durumda, istek hizmet sağlayıcı kaynağına ulaştığında, hizmet sağlayıcısından herhangi bir işlem yapmanız gerekmez ve bağlantı otomatik olarak onaylanır. 
- **El ile**: hizmet tüketicisinin hizmet sağlayıcı KAYNAĞıNDA Azure RBAC izinleri yoksa, tüketici el ile onay yöntemini seçebilir. Bu durumda, bağlantı isteği **bekliyor** olarak hizmet kaynaklarında görüntülenir. Bağlantı kurulamamadan önce hizmet sağlayıcının isteği el ile onaylaması de istenir. El ile yapılan durumlarda, hizmet tüketicisi hizmet sağlayıcısına daha fazla bağlam sağlama isteğine sahip bir ileti de belirtebilir. Hizmet sağlayıcısı tüm özel uç nokta bağlantılarında arasından seçim yapmak için aşağıdaki seçeneklere sahiptir: **Onaylandı**, **Reddet**, **Kaldır**.

Aşağıdaki tabloda, çeşitli hizmet sağlayıcısı eylemleri ve özel uç noktalar için ortaya çıkan bağlantı durumları gösterilmektedir.  Hizmet sağlayıcı ayrıca, Özel uç nokta bağlantısının bağlantı durumunu daha sonra tüketici müdahalesi olmadan değiştirebilir. Bu eylem, tüketici tarafındaki uç noktanın durumunu güncelleştirir. 


|Hizmet sağlayıcı eylemi   |Hizmet tüketicisi özel uç nokta durumu   |Açıklama   |
|---------|---------|---------|
|Hiçbiri    |    Beklemede     |    Bağlantı el ile oluşturulur ve özel bağlantı kaynağı sahibi tarafından onay bekliyor.       |
|Onaylama    |  Onaylandı       |  Bağlantı otomatik olarak veya el ile onaylandı ve kullanılabilir hale gelmiştir.     |
|Reddet     | Reddedildi        | Bağlantı, özel bağlantı kaynağı sahibi tarafından reddedildi.        |
|Kaldır    |  Bağlantı kesildi       | Bağlantı, özel bağlantı kaynağı sahibi tarafından kaldırıldı, Özel uç nokta bilgilendirici hale gelir ve temizleme için silinmelidir.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Azure PaaS kaynaklarında Özel Uç Nokta Bağlantılarını yönetme
Portal, Azure PaaS kaynaklarında özel uç nokta bağlantılarını yönetmek için tercih edilen yöntemdir. Şu anda Azure PaaS kaynakları üzerinde bağlantıları yönetmek için PowerShell/CLı desteğiniz yok.
1. https://portal.azure.com adresinden Azure portalında oturum açın.
2. Özel bağlantı merkezi ' ne gidin.
3. **Kaynaklar** altında, Özel uç nokta bağlantılarını yönetmek istediğiniz kaynak türünü seçin.
4. Kaynak tipinin her biri için, onunla ilişkili özel uç nokta bağlantılarının sayısını görüntüleyebilirsiniz. Kaynakları gerektiği gibi filtreleyebilirsiniz.
5. Özel uç nokta bağlantılarını seçin.  Listelenen bağlantılar altında, yönetmek istediğiniz bağlantıyı seçin. 
6. En üstteki seçeneklerden birini seçerek bağlantının durumunu değiştirebilirsiniz.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Müşterinin/ortağın sahip olduğu özel bağlantı hizmetinde özel uç nokta bağlantılarını yönetme

Azure PowerShell ve Azure CLı, Microsoft Iş ortağı hizmetlerinde veya müşterinin sahip olduğu hizmetlerde özel uç nokta bağlantılarını yönetmek için tercih edilen yöntemlerdir. Şu anda, bir özel bağlantı hizmetinde bağlantıları yönetmek için Portal desteğiniz yok.  
 
### <a name="powershell"></a>PowerShell 
  
Özel uç nokta bağlantılarını yönetmek için aşağıdaki PowerShell komutlarını kullanın.  
#### <a name="get-private-link-connection-states"></a>Özel bağlantı bağlantı durumlarını al 
`Get-AzPrivateLinkService`Özel uç nokta bağlantılarını ve bunların durumlarını almak için cmdlet 'ini kullanın.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Özel bir uç nokta bağlantısını onaylama 
 
`Approve-AzPrivateEndpointConnection`Özel bir uç nokta bağlantısını onaylamak için cmdlet 'ini kullanın. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Özel uç nokta bağlantısını reddet 
 
`Deny-AzPrivateEndpointConnection`Özel bir uç nokta bağlantısını reddetmek için cmdlet 'ini kullanın. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Özel uç nokta bağlantısını kaldır 
 
`Remove-AzPrivateEndpointConnection`Özel bir uç nokta bağlantısını kaldırmak için cmdlet 'ini kullanın. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI’si 
 
`az network private-link-service update`Özel uç nokta bağlantılarınızı yönetmek için kullanın. Bağlantı durumu ```azurecli connection-status``` parametresinde belirtilir. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Sonraki adımlar
- [Özel uç noktalar hakkında bilgi edinin](private-endpoint-overview.md)
 
