---
title: Ağ yönlendirme tercihini yapılandırma
titleSuffix: Azure Storage
description: Ağ trafiğinin Internet üzerinden kendi hesabınıza nasıl yönlendirildiğini belirtmek için, Azure depolama hesabınız için ağ yönlendirmesi tercihini yapılandırın.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790464"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Azure depolama için ağ yönlendirme tercihini yapılandırma

Bu makalede, depolama hesabınız için ağ yönlendirme tercihini ve rotaya özgü uç noktaların nasıl yapılandırılacağı açıklanır. 

Ağ yönlendirme tercihi, ağ trafiğinin Internet üzerinden istemcilerden hesabınıza nasıl yönlendirildiğini belirtir. Rotaya özgü uç noktalar, Azure Storage 'ın depolama hesabınız için oluşturduğu yeni uç noktalardır. Bu uç noktalar, varsayılan yönlendirme tercihinizi değiştirmeden trafiği istenen bir yol üzerinden yönlendirir. Daha fazla bilgi için bkz. [Azure depolama Için ağ yönlendirme tercihi](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Varsayılan genel uç nokta için yönlendirme tercihini yapılandırma

Varsayılan olarak, depolama hesabının genel uç noktası için yönlendirme tercihi Microsoft Global Network olarak ayarlanır. Depolama hesabınızın genel uç noktası için varsayılan yönlendirme tercihi olarak Microsoft küresel ağ ve Internet yönlendirmesi arasında seçim yapabilirsiniz. Bu iki yönlendirme türü arasındaki fark hakkında daha fazla bilgi edinmek için bkz. [Azure depolama Için ağ yönlendirme tercihi](network-routing-preference.md). 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Yönlendirme tercihinizi Internet yönlendirme olarak değiştirmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Portalda depolama hesabınıza gidin.

3. **Ayarlar** altında **ağ**' ı seçin.

    > [!div class="mx-imgBorder"]
    > ![Ağ menü seçeneği](./media/configure-network-routing-preference/networking-option.png)

4.  **Güvenlik duvarları ve sanal ağlar** sekmesinde, **ağ yönlendirme** altında, **yönlendirme tercihi** ayarını **Internet yönlendirme** olarak değiştirin.

5.  **Kaydet**’e tıklayın.

    > [!div class="mx-imgBorder"]
    > ![İnternet yönlendirme seçeneği](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Komutuyla Azure aboneliğinizde oturum açın `Connect-AzAccount` ve kimlik doğrulaması yapmak için ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi statik Web sitenizi barındıracak depolama hesabının aboneliğine ayarlayın.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

3. Yönlendirme tercihinizi Internet yönlendirme olarak değiştirmek için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) komutunu kullanın ve `--routing-choice` parametresini olarak ayarlayın `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   `<resource-group-name>`Yer tutucu değerini, depolama hesabını içeren kaynak grubunun adıyla değiştirin.

   `<storage-account-name>`Yer tutucu değerini depolama hesabının adıyla değiştirin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure aboneliğinizde oturum açın.

   - Azure Cloud Shell başlatmak için [Azure Portal](https://portal.azure.com)oturum açın.

   - CLı 'nın yerel yüklemesinde oturum açmak için [az Login](/cli/azure/reference-index#az_login) komutunu çalıştırın:

     ```azurecli
     az login
     ```
2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi statik Web sitenizi barındıracak depolama hesabının aboneliğine ayarlayın.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

3. Yönlendirme tercihinizi Internet yönlendirme olarak değiştirmek için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) komutunu kullanın ve `--routing-choice` parametresini olarak ayarlayın `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

---

## <a name="configure-a-route-specific-endpoint"></a>Rotaya özgü uç nokta yapılandırma

Ayrıca, yönlendirmeye özgü bir uç nokta da yapılandırabilirsiniz. Örneğin, varsayılan uç nokta için yönlendirme tercihini *İnternet yönlendirme* olarak ayarlayabilir ve ardından İnternet üzerindeki istemciler ve depolama hesabınızın Microsoft Global ağı aracılığıyla yönlendirilmesi için bir rotaya özgü uç nokta yayımlayabilirsiniz.

Bu tercih yalnızca rotaya özgü uç noktayı etkiler. Bu tercih, varsayılan yönlendirme tercihinizi etkilemez.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Portalda depolama hesabınıza gidin.

2.  **Ayarlar** altında **ağ**' ı seçin.

3.  **Güvenlik duvarları ve sanal ağlar** sekmesindeki **rotaya özgü uç noktalar**' ın altında, yönlendirmeye özgü uç noktaıza ait yönlendirme tercihini seçin ve ardından **Kaydet**' e tıklayın.

    Aşağıdaki görüntüde **Microsoft ağ yönlendirme** seçeneği seçili gösterilmektedir.

    > [!div class="mx-imgBorder"]
    > ![Microsoft ağ yönlendirme seçeneği](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Rotaya özgü bir uç nokta yapılandırmak için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) komutunu kullanın. 

   - Microsoft ağ yönlendirme tercihini kullanan bir rotaya özgü uç nokta oluşturmak için, `-PublishMicrosoftEndpoint` parametresini olarak ayarlayın `true` . 

   - Internet yönlendirme tercihini kullanan bir rotaya özgü uç nokta oluşturmak için, `-PublishInternetEndpointTo` parametresini olarak ayarlayın `true` .  

   Aşağıdaki örnek, Microsoft ağ yönlendirme tercihini kullanan rotaya özgü bir uç nokta oluşturur.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   `<resource-group-name>`Yer tutucu değerini, depolama hesabını içeren kaynak grubunun adıyla değiştirin.

   `<storage-account-name>`Yer tutucu değerini depolama hesabının adıyla değiştirin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Rotaya özgü bir uç nokta yapılandırmak için [az Storage Account Update](/azure/storage/account#az_storage_account_update) komutunu kullanın. 

   - Microsoft ağ yönlendirme tercihini kullanan bir rotaya özgü uç nokta oluşturmak için, `--publish-microsoft-endpoints` parametresini olarak ayarlayın `true` . 

   - Internet yönlendirme tercihini kullanan bir rotaya özgü uç nokta oluşturmak için, `--publish-internet-endpoints` parametresini olarak ayarlayın `true` .  

   Aşağıdaki örnek, Microsoft ağ yönlendirme tercihini kullanan rotaya özgü bir uç nokta oluşturur.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   `<storage-account-name>`Yer tutucu değerini depolama hesabının adıyla değiştirin.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Rotaya özgü uç nokta için uç nokta adı bulma

Bir rotaya özgü uç nokta yapılandırdıysanız, uç noktayı depolama hesabınızın özelliklerinde bulabilirsiniz.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  **Ayarlar** altında **Özellikler**' i seçin.

    > [!div class="mx-imgBorder"]
    > ![Özellikler menü seçeneği](./media/configure-network-routing-preference/properties.png)

2.  **Microsoft ağ yönlendirme** uç noktası, yönlendirme tercihlerini destekleyen her bir hizmet için gösterilir. Bu görüntüde blob ve dosya hizmetleri için uç nokta gösterilmektedir.

    > [!div class="mx-imgBorder"]
    > ![Rotaya özgü uç noktalar için Microsoft ağ yönlendirme seçeneği](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Uç noktaları konsola yazdırmak için, `PrimaryEndpoints` depolama hesabı nesnesinin özelliğini kullanın.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   `<resource-group-name>`Yer tutucu değerini, depolama hesabını içeren kaynak grubunun adıyla değiştirin.

   `<storage-account-name>`Yer tutucu değerini depolama hesabının adıyla değiştirin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Uç noktaları konsola yazdırmak için, depolama hesabı nesnesinin [az Storage Account Show](/cli/azure/storage/account#az_storage_account_show) özelliğini kullanın.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   `<resource-group-name>`Yer tutucu değerini, depolama hesabını içeren kaynak grubunun adıyla değiştirin.

   `<storage-account-name>`Yer tutucu değerini depolama hesabının adıyla değiştirin.

---

## <a name="see-also"></a>Ayrıca bkz.

- [Ağ yönlendirme tercihi](network-routing-preference.md)
- [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](storage-network-security.md)
- [BLOB depolama için güvenlik önerileri](../blobs/security-recommendations.md)
