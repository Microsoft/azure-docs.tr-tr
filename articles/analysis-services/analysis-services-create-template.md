---
title: Hızlı başlangıç-Azure Resource Manager şablonu kullanarak Azure Analysis Services sunucu kaynağı oluşturma
description: Bir Azure Resource Manager şablonu kullanarak Azure Analysis Services sunucu kaynağının nasıl yapılacağını gösteren hızlı başlangıç.
author: minewiskan
ms.author: owend
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
tags:
- azure-resource-manager
ms.custom: devx-track-azurepowershell - subject-armqs - references_regions - mode-arm
ms.openlocfilehash: e7203f4b5890ab81cbf337c5f3201d85a3aef0c0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769380"
---
# <a name="quickstart-create-a-server---arm-template"></a>Hızlı başlangıç: sunucu ARM şablonu oluşturma

Bu hızlı başlangıçta, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure aboneliğinizde Analysis Services sunucu kaynağı oluşturma açıklanmaktadır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

* **Azure aboneliği**: hesap oluşturmak Için [Azure Ücretsiz deneme sürümünü](https://azure.microsoft.com/offers/ms-azr-0044p/) ziyaret edin.
* **Azure Active Directory**: Aboneliğinizin bir Azure Active Directory Kiracısı ile ilişkilendirilmiş olması gerekir. Ayrıca bu Azure Active Directory içindeki bir hesapla Azure'da oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [Kimlik doğrulaması ve izinler](analysis-services-manage-users.md).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-analysis-services-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

Şablonda bir güvenlik duvarı kuralına sahip tek bir [Microsoft. AnalysisServices/Servers](/azure/templates/microsoft.analysisservices/servers) kaynağı tanımlanmıştır.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve bir şablonu açmak için aşağıdaki Azure 'a dağıt bağlantısını seçin. Şablon, Analysis Services sunucu kaynağı oluşturmak ve gerekli ve isteğe bağlı özellikleri belirtmek için kullanılır.

   [![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.

    Aksi belirtilmediği takdirde, varsayılan değerleri kullanın.

    * **Abonelik**: bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Yeni oluştur**' a tıklayın ve ardından yeni kaynak grubu için benzersiz bir ad girin.
    * **Konum**: kaynak grubunda oluşturulan kaynaklar için varsayılan bir konum seçin.
    * **Sunucu adı**: sunucu kaynağı için bir ad girin. 
    * **Konum**: Analysis Services için Yoksay. Konum sunucu konumunda belirtilir.
    * **Sunucu konumu**: Analysis Services sunucusunun konumunu girin. Bu, genellikle kaynak grubu için belirtilen varsayılan konum ile aynı bölgedir, ancak gerekli değildir. Örneğin **Orta Kuzey ABD**. Desteklenen bölgeler için bkz. [bölgeye göre Analysis Services kullanılabilirliği](analysis-services-overview.md#availability-by-region).
    * **SKU adı**: oluşturulacak Analysis Services sunucusu için SKU adını girin. Şunları seçin: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. SKU kullanılabilirliği bölgeye bağlıdır. Değerlendirme ve test için S0 veya D1 önerilir.
    * **Kapasite**: toplam sorgu çoğaltma ölçeği genişletme örneği sayısını girin. Birden fazla örneğin ölçeğini genişletme yalnızca seçim bölgelerinde desteklenir.
    * **Güvenlik duvarı ayarları**: sunucu için tanımlamak üzere gelen güvenlik duvarı kurallarını girin. Belirtilmezse güvenlik duvarı devre dışı bırakılır.
    * **Yedekleme blobu kapsayıcı URI 'si**: okuma, yazma ve listeleme izinlerine sahip özel bir Azure Blob depolama KAPSAYıCıSıNA SAS URI 'sini girin. Yalnızca [yedekleme/geri yükleme](analysis-services-backup.md)kullanmak istiyorsanız gereklidir.
    * **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**: Bunu seçin.

3. **Satın al**'ı seçin. Sunucu başarıyla dağıtıldıktan sonra bir bildirim alırsınız:

   ![ARM şablonu, Portal dağıtma bildirimi](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Kaynak grubunun ve sunucu kaynağının oluşturulduğunu doğrulamak için Azure portal veya Azure PowerShell kullanın.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, kaynak grubunu ve sunucu kaynağını silmek için Azure portal, Azure CLı veya Azure PowerShell kullanın.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, yeni bir kaynak grubu ve bir Azure Analysis Services sunucu kaynağı oluşturmak için ARM şablonu kullandınız. Şablonu kullanarak bir sunucu kaynağı oluşturduktan sonra, aşağıdakileri göz önünde bulundurun:

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Sunucu güvenlik duvarını yapılandırma - Portal](analysis-services-qs-firewall.md)   
