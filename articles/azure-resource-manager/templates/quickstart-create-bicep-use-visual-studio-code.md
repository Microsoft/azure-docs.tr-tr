---
title: Bicep dosyaları oluşturma-Visual Studio Code
description: Azure kaynaklarını dağıtmak için Bıcep dosyalarına Visual Studio Code ve bicep uzantısı kullanın
author: mumian
ms.date: 04/12/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: e82f047f553290c9121d91060c347c5b242c7ec2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306475"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code bicep dosyaları oluşturma

Visual Studio Code için Bıcep uzantısı, dil desteği ve kaynak otomatik tamamlama sağlar. Bu araçlar, [bicep](./bicep-overview.md) dosyalarını oluşturma ve doğrulamaya yardımcı olur. Bu hızlı başlangıçta, uzantıyı sıfırdan bir bicep dosyası oluşturmak için kullanacaksınız. Bunu yaparken, doğrulama ve tamamlamalar gibi uzantı özelliklerine de karşılaşırsınız.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

Bu hızlı başlangıcı tamamlayabilmeniz için, [Bıcep uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) yüklüyken [Visual Studio Code](https://code.visualstudio.com/)gerekir. Ayrıca en son [Azure CLI](/cli/azure/) veya en son [Azure PowerShell modülünün](/powershell/azure/new-azureps-module-az) yüklü ve kimliği doğrulanmış olması gerekir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="create-a-bicep-file"></a>Bicep dosyası oluşturma

*Azuredeploy. bıcep* adlı yeni bir dosya Visual Studio Code oluşturun ve açın.

## <a name="add-an-azure-resource"></a>Azure kaynağı ekleme

Bıcep dosyasına temel bir depolama hesabı kaynağı ekleyin.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

Kaynak bildiriminde dört bileşen vardır:

- **kaynak**: anahtar sözcüğü.
- **simgesel ad** (STG): sembolik ad, bıcep dosyanız boyunca kaynağa başvurmak için bir tanıtıcıdır. Bu, kaynağın adı dağıtıldığında ne olur? Kaynağın adı, **Name** özelliği tarafından tanımlanır.  Bu listedeki dördüncü bileşene bakın.
- **kaynak türü** ( Microsoft.Storage/storageAccounts@2019-06-01 ): kaynak sağlayıcıdan (Microsoft. Storage), kaynak türünden (Storageaccounts) ve apiversion 'dan (2019-06-01) oluşur. Her kaynak sağlayıcı kendi API sürümlerini yayımlar, bu nedenle bu değer türüne özeldir. [ARM şablon başvurusundan](/azure/templates/)çeşitli Azure kaynakları için daha fazla tür ve apiVersions bulabilirsiniz.
- **Özellikler** (= {...} içindeki her şey): kaynak türü Için özellikleri belirtin. Her kaynağın bir `name` özelliği vardır. Kaynakların çoğu ayrıca `location` , kaynağın dağıtıldığı bölgeyi ayarlayan bir özelliğine sahiptir. Diğer özellikler kaynak türüne ve API sürümüne göre farklılık gösterir.

Daha fazla bilgi için bkz. [Bıcep yapısı](./bicep-file.md).

Name özelliği için bir açıklama vardır.  `//`Tek satır açıklamaları veya `/* ... */` çok satırlı açıklamalar için kullanın

## <a name="completion-and-validation"></a>Tamamlama ve doğrulama

Uzantının en güçlü özelliklerinden biri, Azure şemalarıyla tümleştirmedir. Azure şemaları, bir uzantıyı doğrulama ve kaynak kullanan tamamlama özellikleri sağlar. Doğrulama ve tamamlama eylemini görmek için depolama hesabını değiştirelim.

İlk olarak, depolama hesabı türünü gibi geçersiz bir değere güncelleştirin `megaStorage` . Bu eylemin geçerli bir değer olmadığını belirten bir uyarı ürettiğine dikkat edin `megaStorage` .

![Geçersiz depolama yapılandırmasını gösteren resim](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Tamamlanma yeteneklerini kullanmak için, `megaStorage` imleci tek tırnak işareti içine yerleştirin ve tuşuna basın `ctrl`  +  `space` . Bu eylem geçerli değerlerin tamamlanma listesini gösterir.

![Uzantı otomatik tamamlamayı gösteren resim](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Parametreler ekleme

Şimdi depolama hesabı adını belirtmek için bir parametre oluşturun ve kullanın.

Aşağıdaki kodu dosyanın başına ekleyin:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Azure depolama hesabı adlarında en az 3 karakter uzunluğunda ve en fazla 24 tane vardır. `minLength` `maxLength` Uygun değerleri sağlamak için ve kullanın.

Şimdi, depolama kaynağında, parametresini kullanmak için Name özelliğini güncelleştirin. Bunu yapmak için, tek tırnak dahil olmak üzere geçerli depolama kaynağı adını kaldırın. tuşuna basın `ctrl`  +  `space` . Listeden **storageAccountName** parametresini seçin. Parametrelere Bıcep içinde adlarını kullanarak doğrudan başvurulabileceğini unutmayın. JSON şablonları bir Parameter () işlevi gerektirir.

![Bıcep kaynaklarında parametreler kullanılırken otomatik tamamlamayı gösteren resim](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Bıcep dosyasını dağıtma

Anahtar bileşimini kullanarak tümleşik Visual Studio Code terminali açın `ctrl`  +  ```` ` ```` , geçerli dizini bıcep dosyasının bulunduğu konum olarak değiştirin ve ardından bıcep dosyasını dağıtmak için Azure CLI veya Azure PowerShell modülünü kullanın.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure kaynaklarına artık ihtiyaç duyulmadığında hızlı başlangıç kaynak grubunu silmek için Azure CLı veya Azure PowerShell modülünü kullanın.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Acemi olarak bicep öğreticileri](./bicep-tutorial-create-first-bicep.md)
