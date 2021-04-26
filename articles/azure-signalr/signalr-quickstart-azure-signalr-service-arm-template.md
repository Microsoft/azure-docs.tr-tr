---
title: 'Hızlı başlangıç: Azure SignalR hizmeti-ARM şablonu oluşturma'
description: Bu hızlı başlangıçta, bir Azure Resource Manager şablonu kullanarak Azure SignalR hizmeti oluşturmayı öğrenin (ARM şablonu).
author: sffamily
ms.author: zhshang
ms.date: 10/02/2020
ms.topic: quickstart
ms.service: signalr
ms.custom:
- subject-armqs
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 075915f2a06e29eb5b80425913c6e7a2c476ff99
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536523"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Hızlı başlangıç: Azure SignalR hizmeti dağıtmak için ARM şablonu kullanma

Bu hızlı başlangıçta, bir Azure SignalR hizmeti oluşturmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı açıklanmaktadır. Azure SignalR hizmetini Azure portal, PowerShell veya CLı aracılığıyla dağıtabilirsiniz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Bu şablon, oturum açıldıktan sonra Azure portal açılır.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure SignalR hizmetini Azure portal bir ARM şablonu kullanarak Azure 'a dağıtma düğmesi.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Önkoşullar

# <a name="portal"></a>[Portal](#tab/azure-portal)

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).
* Kodu yerel olarak çalıştırmak istiyorsanız, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/).
* Kodu yerel olarak çalıştırmak istiyorsanız:
    * Bash kabuğu ( [Windows Için git](https://gitforwindows.org)'e dahil olan git Bash gibi).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-signalr/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Şablon bir Azure kaynağı tanımlar:

* [**Microsoft. SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Şablonu dağıtma

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal ARM şablonunu kullanarak Azure SignalR hizmetini dağıtmak için aşağıdaki bağlantıyı seçin:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure SignalR hizmetini Azure portal ARM şablonunu kullanarak Azure 'a dağıtmaya yönelik düğme.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

**Azure SignalR hizmeti dağıtma** sayfasında:

1. İsterseniz, **aboneliği** varsayılan ' dan değiştirin.

2. **Kaynak grubu** Için **Yeni oluştur**' u seçin, yeni kaynak grubu için bir ad girin ve **Tamam**' ı seçin.

3. Yeni bir kaynak grubu oluşturduysanız, kaynak grubu için bir **bölge** seçin.

4. İsterseniz, Azure SignalR hizmetinin yeni bir **adını** ve **konumunu** (örneğin, **eastus2**) girin. Bir ad belirtmezseniz, otomatik olarak oluşturulur. Azure SignalR hizmetinin konumu, kaynak grubunun bölgesiyle aynı veya farklı olabilir. Bir konum belirtmezseniz, kaynak grubuyla aynı bölgeye ayarlanır.

5. **Fiyatlandırma katmanını** (**Free_F1** veya **Standard_S1**) seçin, **kapasiteyi** (SignalR birimi sayısını) girin ve **varsayılan** bir **hizmet modu** seçin (hub sunucusu gerektirir), **sunucusuz** (herhangi bir sunucu bağlantısına izin vermez) veya **Klasik** (hub sunucusu bağlantısı varsa hub sunucusuna yönlendirilir). Sonra **bağlantı günlüklerinin** etkinleştirilip etkinleştirilmeyeceğini seçin veya **mesajlaşma günlüklerini etkinleştirin**.

    > [!NOTE]
    > **Free_F1** fiyatlandırma katmanı için kapasite 1 birimle sınırlıdır.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Azure portal Azure SignalR hizmeti oluşturmak için ARM şablonunun ekran görüntüsü.":::

6. **Gözden geçir ve oluştur**’u seçin.

7. Hüküm ve koşulları okuyun ve ardından **Oluştur**' u seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> PowerShell betiklerini yerel olarak çalıştırmak istiyorsanız, ilk olarak `Connect-AzAccount` Azure kimlik bilgilerinizi kurmak için girin.

ARM şablonunu kullanarak Azure SignalR hizmetini dağıtmak için aşağıdaki kodu kullanın. Kod sizden aşağıdaki öğeleri ister:

* Yeni Azure SignalR hizmeti 'nin adı ve bölgesi
* Yeni bir kaynak grubunun adı ve bölgesi
* Azure Fiyatlandırma Katmanı (**Free_F1** veya **Standard_S1**)
* SignalR birim kapasitesi (1, 2, 5, 10, 20, 50 veya 100)
  > [!NOTE]
  > **Free_F1** fiyatlandırma katmanı için kapasite 1 birimle sınırlıdır.
* Hizmet modu: bir hub sunucusu istemek için **varsayılan** , herhangi bir sunucu bağlantısına izin vermemek Için **sunucusuz** ya da hub 'ın bir sunucu bağlantısı varsa hub sunucusuna yönlendirmek için **Klasik**
* Bağlantı veya mesajlaşma için günlüklerin etkinleştirilip etkinleştirilmeyeceğini belirtir (**true** veya **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

ARM şablonunu kullanarak Azure SignalR hizmetini dağıtmak için aşağıdaki kodu kullanın. Kod sizden aşağıdaki öğeleri ister:

* Yeni Azure SignalR hizmeti 'nin adı ve bölgesi
* Yeni bir kaynak grubunun adı ve bölgesi
* Azure Fiyatlandırma Katmanı (**Free_F1** veya **Standard_S1**)
* SignalR birim kapasitesi (1, 2, 5, 10, 20, 50 veya 100)
    > [!NOTE]
    > **Free_F1** fiyatlandırma katmanı için kapasite 1 birimle sınırlıdır.
* Hizmet modu: bir hub sunucusu istemek için **varsayılan** , herhangi bir sunucu bağlantısına izin vermemek Için **sunucusuz** ya da hub 'ın bir sunucu bağlantısı varsa hub sunucusuna yönlendirmek için **Klasik**
* Bağlantı veya mesajlaşma için günlüklerin etkinleştirilip etkinleştirilmeyeceğini belirtir (**true** veya **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtılmış kaynakları daha sonra gözden geçirmek için kullandığınız Azure SignalR hizmeti ve kaynak grubunun adlarını aklınızda bulabilirsiniz.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yeni Azure SignalR hizmetinize genel bir bakış için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com), **SignalR** öğesini arayıp seçin.

2. SignalR listesinde yeni hizmetinizi seçin. Yeni Azure SignalR hizmeti için **genel bakış** sayfası görüntülenir.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Azure SignalR hizmetiniz hakkındaki ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni hizmet ve kaynak grubunun adını girmeniz gerekir.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Azure SignalR hizmetiniz hakkındaki ayrıntıları görüntülemek için aşağıdaki etkileşimli kodu çalıştırın. Yeni hizmet ve kaynak grubunun adını girmeniz gerekir.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubundaki kaynakları silen kaynak grubunu silin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com), **kaynak gruplarını** arayıp seçin.

2. Kaynak grubu listesinde, kaynak grubunuzun adını seçin.

3. Kaynak grubunuzun **genel bakış** sayfasında **kaynak grubunu sil**' i seçin.

4. Onay iletişim kutusunda, kaynak grubunuzun adını yazın ve ardından **Sil**' i seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Sonraki adımlar

ARM şablonu oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [ Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
