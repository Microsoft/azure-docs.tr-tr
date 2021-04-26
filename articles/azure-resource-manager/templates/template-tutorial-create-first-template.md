---
title: Öğretici-& dağıtım şablonu oluşturma
description: İlk Azure Resource Manager şablonunuzu oluşturun (ARM şablonu). Öğreticide, şablon dosyası söz dizimi ve depolama hesabı dağıtma hakkında bilgi edineceksiniz.
author: mumian
ms.date: 12/17/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 001a20c38a82d053ae22a2614a32f0be9d8aabce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656997"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma

Bu öğreticide, Azure Resource Manager şablonlar (ARM şablonları) sunulmaktadır. Bu, bir başlangıç şablonu oluşturma ve Azure 'a dağıtma işlemlerinin nasıl yapılacağını gösterir. Şablon ve şablonlar ile çalışmak için gereken araçların yapısı hakkında bilgi edineceksiniz. Bu öğreticiyi tamamlamaya yönelik **12 dakika** sürer, ancak gerçek süre, yüklemeniz gereken araç sayısına göre değişir.

Bu öğretici bir serinin birincisidir. Seriler aracılığıyla ilerleyerek, bir ARM şablonunun tüm temel parçalarını araştırana kadar başlangıç şablonu adımını adım adım değiştirirsiniz. Bu öğeler çok daha karmaşık şablonlar için yapı taşlarıdır. Kendi şablonlarınızı oluşturduğunuz ve dağıtımlarınızı şablonlarla otomatik hale getirmeye hazırlamış olduğunuz serinin sonunu umuyoruz.

Şablon kullanmanın avantajları hakkında bilgi edinmek istiyorsanız ve şablonları ile dağıtımı otomatikleştirmeniz gerekiyorsa, bkz. [ARM şablonuna genel bakış](overview.md). Microsoft Learn üzerinde bir Kılavuzlu dizi modülle ARM şablonları hakkında bilgi edinmek için bkz. [ARM şablonlarını kullanarak Azure 'da kaynakları dağıtma ve yönetme](/learn/paths/deploy-manage-resource-manager-templates/).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="get-tools"></a>Araçları al

Şablonlar oluşturmanız ve dağıtmanız için ihtiyacınız olan araçlara sahip olduğunuzdan emin olalım. Bu araçları yerel makinenize yükler.

### <a name="editor"></a>Düzenleyici

Şablonlar JSON dosyalarıdır. Şablon oluşturmak için, iyi bir JSON düzenleyicisine ihtiyacınız vardır. Kaynak Yöneticisi Araçları uzantısı ile Visual Studio Code önerilir. Bu araçları yüklemeniz gerekiyorsa bkz. [hızlı başlangıç: VISUAL STUDIO Code ARM şablonları oluşturma](quickstart-create-templates-use-visual-studio-code.md).

### <a name="command-line-deployment"></a>Komut satırı dağıtımı

Ayrıca, şablonu dağıtmak için Azure PowerShell ya da Azure CLı gerekir. Azure CLı kullanıyorsanız, en son sürüme sahip olmanız gerekir. Yükleme yönergeleri için bkz.:

- [Azure PowerShell’i yükleme](/powershell/azure/install-az-ps)
- [Windows'da Azure CLI'yi yükleme](/cli/azure/install-azure-cli-windows)
- [Linux 'ta Azure CLı 'yı yükler](/cli/azure/install-azure-cli-linux)
- [macOS’ta Azure CLI'yi yükleme](/cli/azure/install-azure-cli-macos)

Azure PowerShell veya Azure CLı yükledikten sonra, ilk kez oturum açarak emin olun. Yardım için bkz. [oturum açma-PowerShell](/powershell/azure/install-az-ps#sign-in) veya [Oturum Açma-Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

> [!IMPORTANT]
> Azure CLı kullanıyorsanız, sürüm 2,6 veya sonraki bir sürüme sahip olduğunuzdan emin olun. Önceki sürümleri kullanıyorsanız bu öğreticide gösterilen komutlar çalışmayacaktır. Yüklü sürümünüzü denetlemek için şunu kullanın: `az --version` .

Tamam, şablonlar hakkında öğrenmeye başlamaya hazırsınız.

## <a name="create-your-first-template"></a>İlk şablonunuzu oluşturma

1. Kaynak Yöneticisi Araçları uzantısı yüklü Visual Studio Code açın.
1. **Dosya** menüsünde **yeni dosya** ' yı seçerek yeni bir dosya oluşturun.
1. **Dosya** menüsünde **farklı kaydet**' i seçin.
1. Dosyayı _azuredeploy_ olarak adlandırın ve _JSON_ dosya uzantısını seçin. Dosyanın tamamının adı _azuredeploy.js_.
1. Dosyayı iş istasyonunuza kaydedin. Daha sonra şablonu dağıttığınızda bu yolu sağlayacağından, anımsanması kolay bir yol seçin.
1. Aşağıdaki JSON dosyasını kopyalayıp dosyaya yapıştırın:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Visual Studio Code ortamınız şöyle görünür:

    ![ARM şablonu Visual Studio Code ilk şablon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Bu şablon herhangi bir kaynak dağıtmaz. Bir şeyin yanlış olma olasılığını en aza indirirken, bir şablon dağıtma adımlarını tanımanız için boş bir şablonla başlıyoruz.

    JSON dosyası şu öğelere sahiptir:

    - `$schema`: JSON Şema dosyasının konumunu belirtir. Şema dosyası, bir şablon içinde kullanılabilen özellikleri açıklar. Örneğin, şema, `resources` bir şablon için geçerli özelliklerden biri olarak tanımlar. Şemanın tarihinin 2019-04-01 olduğunu merak etmeyin. Bu şema sürümü güncel ve en son özelliklerin tümünü içerir. Şema tarihi değiştirilmedi çünkü giriş sonrasında hiç bir değişiklik yok.
    - `contentVersion`: Şablonun (1.0.0.0 gibi) sürümünü belirtir. Bu öğe için herhangi bir değer sağlayabilirsiniz. Şablonunuzda önemli değişiklikleri belgelemek için bu değeri kullanın. Şablonu kullanarak kaynakları dağıttığınızda, bu değer doğru şablonun kullanıldığından emin olmak için kullanılabilir.
    - `resources`: Dağıtmak veya güncelleştirmek istediğiniz kaynakları içerir. Şu anda boştur, ancak kaynakları daha sonra ekleyeceğiz.

1. Dosyayı kaydedin.

Tebrikler, ilk şablonunuzu oluşturdunuz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure PowerShell/Azure CLı ile çalışmaya başlamak için Azure kimlik bilgilerinizle oturum açın.

Azure PowerShell ve Azure CLı arasında seçim yapmak için aşağıdaki kod bölümlerindeki sekmeleri seçin. Bu makaledeki CLı örnekleri bash kabuğu için yazılmıştır.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Birden çok Azure aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin. `[SubscriptionID/SubscriptionName]`Ve köşeli ayraçları `[]` Abonelik bilgileriniz ile değiştirin:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

Bir şablonu dağıtırken, kaynakları içerecek bir kaynak grubu belirtirsiniz. Dağıtım komutunu çalıştırmadan önce Azure CLI veya Azure PowerShell ile kaynak grubunu oluşturun.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Şablon dağıtma

Şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın. Oluşturduğunuz kaynak grubunu kullanın. Dağıtım geçmişinde kolayca tanımlayabilmeniz için dağıtıma bir ad verin. Kolaylık sağlaması için, şablon dosyasının yolunu depolayan bir değişken de oluşturun. Her dağıtım yaptığınızda yolu yeniden yazmanız gerekmediğinden bu değişken, dağıtım komutlarını çalıştırmanızı kolaylaştırır. `{provide-the-path-to-the-template-file}`Ve küme ayraçları, `{}` şablon dosyanızın yoluyla değiştirin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Dağıtım komutu sonuçları döndürür. `ProvisioningState`Dağıtımın başarılı olup olmadığını görmek için bölümüne bakın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell dağıtımı sağlama durumu](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLı dağıtımı sağlama durumu](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

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

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'a dağıtmak için basit bir şablon oluşturdunuz. Sonraki öğreticide, şablona bir depolama hesabı ekleyecek ve bunu kaynak grubunuza dağıtacaksınız.

> [!div class="nextstepaction"]
> [Kaynak Ekle](template-tutorial-add-resource.md)
