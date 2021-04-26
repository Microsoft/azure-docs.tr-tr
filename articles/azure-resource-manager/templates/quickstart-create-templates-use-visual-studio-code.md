---
title: Şablon oluşturma-Visual Studio Code
description: Azure Resource Manager şablonları üzerinde çalışmak için Visual Studio Code ve Azure Resource Manager araçları uzantısını kullanın (ARM şablonları).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 3e41d4f6f8b8aeb2be376ae1385189fe02e9b897
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504666"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code ARM şablonları oluşturma

Visual Studio Code için Azure Resource Manager araçları dil desteği, kaynak parçacıkları ve kaynak otomatik tamamlama sağlar. Bu araçlar Azure Resource Manager şablonları (ARM şablonları) oluşturma ve doğrulamaya yardımcı olur. Bu hızlı başlangıçta, uzantıyı sıfırdan bir ARM şablonu oluşturmak için kullanırsınız. Bunu yaparken ARM şablon parçacıkları, doğrulama, tamamlama ve parametre dosyası desteği gibi uzantı özelliklerine sahip olursunuz.

Bu hızlı başlangıcı tamamlayabilmeniz için, [Azure Resource Manager araçları uzantısı](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) yüklüyken [Visual Studio Code](https://code.visualstudio.com/)gerekir. Ayrıca, [Azure CLI](/cli/azure/) veya [Azure PowerShell modülünün](/powershell/azure/new-azureps-module-az) yüklü ve kimliği doğrulanmış olması gerekir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="create-an-arm-template"></a>ARM şablonunu oluşturma

*Üzerindeazuredeploy.js* adlı yeni bir dosya Visual Studio Code oluşturun ve açın. `arm`ARM şablonuna yönelik yapı iskelesi için Azure Resource Manager parçacıkları başlatan kod düzenleyicisine girin.

`arm!`Azure Kaynak grubu dağıtımı için kapsamı belirlenmiş bir şablon oluşturmayı seçin.

![Azure Resource Manager yapı iskelesi parçacıklarını gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Bu kod parçacığı bir ARM şablonu için temel yapı taşlarını oluşturur.

![Tam yapı iskelesi bir ARM şablonunu gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Visual Studio Code dil modunun *JSON* olarak *Azure Resource Manager şablonuna* değiştiği hakkında dikkat edin. Uzantı, ARM şablonlarına özgü, ARM şablonuna özgü doğrulama, tamamlama ve diğer dil hizmetleri sağlayan bir dil sunucusu içerir.

![Visual Studio Code dil modu olarak Azure Resource Manager gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Azure kaynağı ekleme

Uzantı birçok Azure kaynağı için kod parçacıkları içerir. Bu kod parçacıkları, şablon dağıtımınıza kolayca kaynak eklemek için kullanılabilir.

İmleci şablon **kaynakları** bloğuna yerleştirin, yazın `storage` ve *ARM depolama* kod parçacığını seçin.

![ARM şablonuna eklenmekte olan bir kaynağı gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Bu eylem, şablona bir depolama kaynağı ekler.

![ARM şablonunda bir Azure depolama kaynağını gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/5.png)

**Sekme** tuşu, depolama hesabındaki yapılandırılabilir özellikler arasında sekme almak için kullanılabilir.

![Sekme anahtarının kaynak yapılandırması üzerinden gezinmek için nasıl kullanılabileceğini gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Tamamlama ve doğrulama

Uzantının en güçlü özelliklerinden biri, Azure şemalarıyla tümleştirmedir. Azure şemaları, bir uzantıyı doğrulama ve kaynak kullanan tamamlama özellikleri sağlar. Doğrulama ve tamamlama eylemini görmek için depolama hesabını değiştirelim.

İlk olarak, depolama hesabı türünü gibi geçersiz bir değere güncelleştirin `megaStorage` . Bu eylemin geçerli bir değer olmadığını belirten bir uyarı ürettiğine dikkat edin `megaStorage` .

![Geçersiz depolama yapılandırmasını gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Tamamlanma yeteneklerini kullanmak için, `megaStorage` imleci çift tırnak işareti içine yerleştirin ve tuşuna basın `ctrl`  +  `space` . Bu eylem geçerli değerlerin tamamlanma listesini gösterir.

![Uzantı otomatik tamamlamayı gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Şablon parametreleri Ekle

Şimdi depolama hesabı adını belirtmek için bir parametre oluşturun ve kullanın.

İmlecinizi parametreler bloğuna yerleştirin, bir satır başı ekleyin, yazın `"` ve sonra `new-parameter` kod parçacığını seçin. Bu eylem, şablona bir genel parametre ekler.

![ARM şablonuna eklenen bir parametreyi gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Parametresinin adını `storageAccountName` ve açıklamasını güncelleştirin `Storage Account Name` .

![ARM şablonunda tamamlanan parametreyi gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Azure depolama hesabı adlarında en az 3 karakter uzunluğunda ve en fazla 24 tane vardır. `minLength` `maxLength` Parametresini ve parametresine ekleyin ve uygun değerleri sağlayın.

![ARM Şablon parametresine eklenen minLength ve maxLength gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Şimdi, depolama kaynağında, parametresini kullanmak için Name özelliğini güncelleştirin. Bunu yapmak için geçerli adı kaldırın. Bir çift tırnak ve bir sol köşeli ayraç girerek `[` ARM şablon işlevlerinin bir listesini oluşturur. Listeden *Parametreler* ' i seçin.

![ARM şablon kaynaklarında parametreler kullanılırken otomatik tamamlamayı gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/12.png)

`'`Yuvarlak köşeli ayracın içinde tek bir tırnak işareti girilmesi şablonda tanımlanan tüm parametrelerin bir listesini oluşturur, bu durumda *storageAccountName*. Parametresini seçin.

![ARM şablon kaynağında tamamlanan parametreyi gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Parametre dosyası oluşturma

ARM şablon parametre dosyası, ortama özgü parametre değerlerini depolamanıza ve bu değerleri dağıtım zamanında bir grup olarak geçirmenize olanak sağlar. Örneğin, bir test ortamına özgü değerler içeren bir parametre dosyasına ve bir üretim ortamı için başka bir parametreye sahip olabilirsiniz.

Uzantı, mevcut şablonlarınızla bir parametre dosyası oluşturmayı kolaylaştırır. Bunu yapmak için, kod düzenleyicisinde şablona sağ tıklayın ve öğesini seçin `Select/Create Parameter File` .

![ARM şablonundan bir parametre dosyası oluşturmak için sağ tıklama işlemini gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/14.png)

`New`  >  `All Parameters` Parametre dosyası için bir ad ve konum seçin > seçin.

![ARM şablonundan parametreler dosyası oluştururken adı ve dosya Kaydet iletişim kutusunu gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Bu eylem yeni bir parametre dosyası oluşturur ve onu oluşturulduğu şablonla eşler. Şablon seçiliyken Visual Studio Code durum çubuğunda geçerli şablon/parametre dosya eşlemesini görebilir ve değiştirebilirsiniz.

![Visual Studio Code durum çubuğunda şablon/parametre dosya eşlemesini gösteren resim.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Artık parametre dosyası şablonla eşlendiğine göre, uzantı hem şablonu hem de parametre dosyasını birlikte doğrular. Bu doğrulamayı uygulamada görmek için, parametre dosyasındaki parametresine iki karakterli bir değer ekleyin `storageAccountName` ve dosyayı kaydedin.

![Parametre dosyası sorunu nedeniyle geçersiz kılınan bir şablonu gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/17.png)

ARM şablonuna geri gidin ve değerin parametre ölçütlerini karşılamadığını belirten bir hata yapıldığını unutmayın.

![Geçerli bir ARM şablonunu gösteren resim](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Değeri uygun bir şekilde güncelleştirin, dosyayı kaydedin ve şablona geri gidin. Parametresindeki hatanın çözümlendiğine dikkat edin.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Anahtar birleşimini kullanarak tümleşik Visual Studio Code terminalini açın `ctrl`  +  ```` ` ```` ve şablonu dağıtmak için Azure CLI ya da Azure PowerShell modülünü kullanın.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
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
> [Başlangıç öğretileri](./template-tutorial-create-first-template.md)
