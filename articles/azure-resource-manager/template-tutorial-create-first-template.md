---
title: Öğretici-& dağıtım şablonu oluşturma
description: İlk Azure Resource Manager şablonunuzu oluşturun. Öğreticide, şablon dosyası söz dizimi ve depolama hesabı dağıtma hakkında bilgi edineceksiniz.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 961c68ca6d5e0bf6dda95a26a684c3fff60b1d1b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148003"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Öğretici: ilk Azure Resource Manager şablonunuzu oluşturma ve dağıtma

Bu öğreticide, Azure Resource Manager şablonlar sunulmaktadır. Bu, bir başlangıç şablonu oluşturma ve Azure 'a dağıtma işlemlerinin nasıl yapılacağını gösterir. Şablon ve şablonlar ile çalışmak için gereken araçların yapısı hakkında bilgi edineceksiniz. Bu öğreticiyi tamamlamaya yönelik **12 dakika** sürer, ancak gerçek süre, yüklemeniz gereken araç sayısına göre değişir.

Bu öğretici bir serinin birincisidir. Seriler aracılığıyla ilerleyerek, bir Kaynak Yöneticisi şablonunun tüm temel parçalarını araştırana kadar başlangıç şablonunu adım adım değiştirirsiniz. Bu öğeler çok daha karmaşık şablonlar için yapı taşlarıdır. Kendi şablonlarınızı oluşturduğunuz ve dağıtımlarınızı şablonlarla otomatik hale getirmeye hazırlamış olduğunuz serinin sonunu umuyoruz.

Şablon kullanmanın avantajları hakkında bilgi edinmek istiyorsanız ve şablonları ile dağıtımı otomatikleştirmeniz gerekiyorsa, bkz. [Azure Resource Manager şablonları](template-deployment-overview.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="get-tools"></a>Araçları al

Şablonlar oluşturmanız ve dağıtmanız için ihtiyacınız olan araçlara sahip olduğunuzdan emin olalım.

### <a name="editor"></a>Düzenleyici

Şablonlar JSON dosyalarıdır. Şablon oluşturmak için, iyi bir JSON düzenleyicisine ihtiyacınız vardır. Kaynak Yöneticisi Araçları uzantısı ile Visual Studio Code önerilir. Bu araçları yüklemeniz gerekiyorsa, bkz. [Azure Resource Manager şablonları oluşturmak için Visual Studio Code kullanma](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Komut satırı dağıtımı

Ayrıca, şablonu dağıtmak için Azure PowerShell ya da Azure CLı gerekir. Yükleme yönergeleri için bkz.:

- [Azure PowerShell’i yükleme](/powershell/azure/install-az-ps)
- [Windows 'da Azure CLı 'yı yükler](/cli/azure/install-azure-cli-windows)
- [Linux 'ta Azure CLı 'yı yükler](/cli/azure/install-azure-cli-linux)

Azure PowerShell veya Azure CLı yükledikten sonra, ilk kez oturum açarak emin olun. Yardım için bkz. [oturum açma-PowerShell](/powershell/azure/install-az-ps#sign-in) veya [Oturum Açma-Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Tamam, şablonlar hakkında öğrenmeye başlamaya hazırsınız.

## <a name="create-your-first-template"></a>İlk şablonunuzu oluşturma

1. Kaynak Yöneticisi Araçları uzantısı yüklü Visual Studio Code açın.
1. **Dosya** menüsünde **yeni dosya** ' yı seçerek yeni bir dosya oluşturun.
1. **Dosya** menüsünde **farklı kaydet**' i seçin.
1. Dosyayı **azuredeploy** olarak adlandırın ve **JSON** dosya uzantısını seçin. **Azuredeploy. JSON**dosyasının tüm adı.
1. Dosyayı iş istasyonunuza kaydedin. Daha sonra şablonu dağıttığınızda bu yolu sağlayacağından, anımsanması kolay bir yol seçin.
1. Aşağıdaki JSON dosyasını kopyalayıp dosyaya yapıştırın:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    VS Code ortamınız şöyle görünür:

    ![Kaynak Yöneticisi Template Visual Studio Code ilk şablonu](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Bu şablon herhangi bir kaynak dağıtmaz. Bir şeyin yanlış olma olasılığını en aza indirirken, bir şablon dağıtma adımlarını tanımanız için boş bir şablonla başlıyoruz.

    JSON dosyası şu öğelere sahiptir:

    - **$Schema**: JSON Şema dosyasının konumunu belirtir. Şema dosyası, bir şablon içinde kullanılabilen özellikleri açıklar. Örneğin, şema **kaynakları** bir şablon için geçerli özelliklerden biri olarak tanımlar. Şemanın tarihinin 2015-01-01 olduğunu merak etmeyin. Bu şema sürümü güncel ve en son özelliklerin tümünü içerir. Şema tarihi değiştirilmedi çünkü giriş sonrasında hiç bir değişiklik yok.
    - **contentversion**: şablonun (1.0.0.0 gibi) sürümünü belirtir. Bu öğe için herhangi bir değer sağlayabilirsiniz. Şablonunuzda önemli değişiklikleri belgelemek için bu değeri kullanın. Şablonu kullanarak kaynakları dağıttığınızda, bu değer doğru şablonun kullanıldığından emin olmak için kullanılabilir.
    - **kaynaklar**: dağıtmak veya güncelleştirmek istediğiniz kaynakları içerir. Şu anda boştur, ancak kaynakları daha sonra ekleyeceğiz.

1. Dosyayı kaydedin.

Tebrikler, ilk şablonunuzu oluşturdunuz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure PowerShell/Azure CLı ile çalışmaya başlamak için Azure kimlik bilgilerinizle oturum açın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Kaynak grubu oluşturma

Bir şablonu dağıtırken, kaynakları içerecek bir kaynak grubu belirtirsiniz. Dağıtım komutunu çalıştırmadan önce, kaynak grubunu Azure CLı veya Azure PowerShell ile oluşturun. Azure PowerShell ve Azure CLı arasında seçim yapmak için aşağıdaki kod bölümündeki sekmeleri seçin.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Şablon dağıtma

Şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın. Oluşturduğunuz kaynak grubunu kullanın. Dağıtım geçmişinde kolayca tanımlayabilmeniz için dağıtıma bir ad verin. Kolaylık sağlaması için, şablon dosyasının yolunu depolayan bir değişken de oluşturun. Bu değişken, dağıtım komutlarını çalıştırmanızı kolaylaştırır, çünkü her dağıttığınız zaman yolu yeniden yazmanız gerekmez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Dağıtım komutu sonuçları döndürür. Dağıtımın başarılı olup olmadığını görmek için `ProvisioningState` bakın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell dağıtımı sağlama durumu](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLı dağıtımı sağlama durumu](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol menüden **kaynak grupları**' nı seçin.

1. Son yordamda kaynak grubu dağıtımını seçin. Varsayılan ad **Myresourcegroup**' dir. Kaynak grubu içinde dağıtılan bir kaynak görmezsiniz.

1. Genel bakışın sağ üst köşesinde, dağıtımın durumunun görüntülendiğini görebilirsiniz. **1 başarılı**' i seçin.

   ![Dağıtım durumunu görüntüle](./media/template-tutorial-create-first-template/deployment-status.png)

1. Kaynak grubu için bir dağıtım geçmişi görürsünüz. **Blanktemplate**' i seçin.

   ![Dağıtım seçin](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Dağıtımın bir özetini görürsünüz. Bu durumda, hiçbir kaynak dağıtılmadığından görmeniz çok fazla olmaz. Bu serinin ilerleyen kısımlarında, dağıtım geçmişindeki Özeti gözden geçirmeyi yararlı bulabilirsiniz. Sol tarafta, girdileri, çıkışları ve dağıtım sırasında kullanılan şablonu görüntüleyebilirsiniz.

   ![Dağıtım özetini görüntüle](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silmek isteyebilirsiniz.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'a dağıtmak için basit bir şablon oluşturdunuz. Sonraki öğreticide, şablona bir depolama hesabı ekleyecek ve bunu kaynak grubunuza dağıtacaksınız.

> [!div class="nextstepaction"]
> [Kaynak Ekle](template-tutorial-add-resource.md)
