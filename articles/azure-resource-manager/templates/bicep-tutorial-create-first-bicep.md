---
title: Öğretici-& dağıtımı oluşturma Azure Resource Manager bicep dosyaları
description: Azure kaynaklarını dağıtmak için ilk Bıcep dosyanızı oluşturun. Öğreticide, Bıcep dosyası söz dizimi ve depolama hesabı dağıtma hakkında bilgi edineceksiniz.
author: mumian
ms.date: 04/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 0ffd8a97d797144d458e7ec5836042cc6d8d8193
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306693"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Öğretici: ilk Azure Resource Manager Bıcep dosyası oluşturma ve dağıtma

Bu öğretici sizi [Bıcep](./bicep-overview.md)'ye tanıtır. Bu, bir başlatıcı bicep dosyası oluşturmayı ve Azure 'a dağıtmayı gösterir. Bıcep dosyası ve Bıcep dosyaları ile çalışmak için gereken araçların yapısı hakkında bilgi edineceksiniz. Bu öğreticiyi tamamlamaya yönelik **12 dakika** sürer, ancak gerçek süre, yüklemeniz gereken araç sayısına göre değişir.

Bu öğretici bir serinin birincisidir. Seriler aracılığıyla ilerleyerek, bir bicep dosyasının tüm temel parçalarını araştırana kadar, başlangıç bicep dosyası adımını adım adım değiştirirsiniz. Bu öğeler çok daha karmaşık bicep dosyaları için yapı taşlarıdır. Kendi Bıcep dosyalarınızı oluşturma ve Bıcep dosyaları ile dağıtımlarınızı otomatikleştirmeye hazırlama serisinin sonunu umuyoruz.

Bıcep 'yi kullanmanın avantajları hakkında daha fazla bilgi edinmek istiyorsanız, bıcep dosyalarıyla dağıtımı otomatik hale getirmeniz [gerekir.](./bicep-overview.md)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Araçları al

Bicep dosyaları oluşturup dağıtmak için ihtiyacınız olan araçlara sahip olduğunuzdan başlayalım. Bu araçları yerel makinenize yükler.

### <a name="editor"></a>Düzenleyici

Bicep dosyaları oluşturmak için iyi bir düzenleyiciye ihtiyacınız vardır. Bıcep Uzantısı ile Visual Studio Code önerilir. Bu araçları yüklemeniz gerekiyorsa bkz. [Bıcep geliştirme ortamını yapılandırma](./bicep-install.md#development-environment).

### <a name="command-line-deployment"></a>Komut satırı dağıtımı

Azure CLı veya Azure PowerShell kullanarak Bıcep dosyalarını dağıtabilirsiniz. Azure CLı için sürüm 2.20.0 veya daha yeni bir sürüme ihtiyacınız vardır; Azure PowerShell için sürüm 5.6.0 veya sonraki bir sürüme ihtiyacınız vardır. Yükleme yönergeleri için bkz.:

- [Azure PowerShell’i yükleme](/powershell/azure/install-az-ps)
- [Windows'da Azure CLI'yi yükleme](/cli/azure/install-azure-cli-windows)
- [Linux 'ta Azure CLı 'yı yükler](/cli/azure/install-azure-cli-linux)
- [macOS’ta Azure CLI'yi yükleme](/cli/azure/install-azure-cli-macos)

Azure PowerShell veya Azure CLı yükledikten sonra, ilk kez oturum açarak emin olun. Yardım için bkz. [oturum açma-PowerShell](/powershell/azure/install-az-ps#sign-in) veya [Oturum Açma-Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Sorun devam ederseniz, Bıcep hakkında öğrenmeye başlamaya hazırsınız.

## <a name="create-your-first-bicep-file"></a>İlk Bıcep dosyanızı oluşturma

1. Bıcep uzantısı yüklü Visual Studio Code açın.
1. **Dosya** menüsünde **yeni dosya** ' yı seçerek yeni bir dosya oluşturun.
1. **Dosya** menüsünde **farklı kaydet**' i seçin.
1. Dosyayı _azuredeploy_ olarak adlandırın ve _bıcep_ dosya uzantısını seçin. Dosyanın tam adı _azuredeploy. bıcep_' dir.
1. Dosyayı iş istasyonunuza kaydedin. Daha sonra Bıcep dosyasını dağıttığınızda bu yolu sağlayacağından, anımsanması kolay bir yol seçin.
1. Aşağıdaki içeriği kopyalayıp dosyaya yapıştırın:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'  // must be globally unique
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Visual Studio Code ortamınız şöyle görünür:

    ![İlk bicep dosyasını Visual Studio Code](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    Kaynak bildiriminde dört bileşen vardır:

    - **kaynak**: anahtar sözcüğü.
    - **simgesel ad** (STG): sembolik ad, bıcep dosyanız boyunca kaynağa başvurmak için bir tanıtıcıdır. Bu, kaynağın adı dağıtıldığında ne olur? Kaynağın adı, **Name** özelliği tarafından tanımlanır.  Bu listedeki dördüncü bileşene bakın. Öğreticilerin izlenmesi kolay hale getirmek için, bu öğretici serisinde depolama hesabı kaynağının sembolik adı olarak **STG** kullanılır. Nesne özelliklerinin tam listesini almak üzere sembolik adı nasıl kullanacağınızı görmek için bkz. [çıkışları ekleme](./bicep-tutorial-add-outputs.md).
    - **kaynak türü** ( Microsoft.Storage/storageAccounts@2019-06-01 ): kaynak sağlayıcıdan (Microsoft. Storage), kaynak türünden (Storageaccounts) ve apiversion 'dan (2019-06-01) oluşur. Her kaynak sağlayıcı kendi API sürümlerini yayımlar, bu nedenle bu değer türüne özeldir. [ARM şablon başvurusundan](/azure/templates/)çeşitli Azure kaynakları için daha fazla tür ve apiVersions bulabilirsiniz.
    - **Özellikler** (= {...} içindeki her şey): Bunlar, belirtilen kaynak türü için belirtmek istediğiniz özel özelliklerdir. Bunlar, bir ARM şablonunda yalnızca sizin için kullanılabilen özelliklerdir. Her kaynağın bir `name` özelliği vardır. Kaynakların çoğu ayrıca `location` , kaynağın dağıtıldığı bölgeyi ayarlayan bir özelliğine sahiptir. Diğer özellikler kaynak türüne ve API sürümüne göre farklılık gösterir. API sürümü ve kullanılabilir özellikler arasındaki bağlantıyı anlamanız önemlidir. bu nedenle daha fazla ayrıntıya geçelim.

        Bu depolama hesabı için, bu API sürümünü [Storageaccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts)' de görebilirsiniz. Tüm özellikleri Bıcep dosyanıza eklemediğiniz konusunda dikkat edin. Özelliklerin birçoğu isteğe bağlıdır. `Microsoft.Storage`Kaynak sağlayıcısı yeni BIR API sürümünü serbest bırakabilir, ancak dağıttığınız sürümün değiştirmek zorunda değildir. Bu sürümü kullanmaya devam edebilir ve dağıtımınızın sonuçlarının tutarlı olacağını bilirsiniz.

        [Storageaccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)gibi eskı bir API sürümünü görüntülediğinizde, daha küçük bir özellik kümesinin kullanılabildiğini görürsünüz.

        Bir kaynağın API sürümünü değiştirmeye karar verirseniz, bu sürümün özelliklerini değerlendirdiğinizden emin olun ve Bıcep dosyanızı uygun şekilde ayarlayın.

    Daha fazla bilgi için bkz. [Bıcep yapısı](./bicep-file.md).

    Name özelliği için bir açıklama vardır.  `//`Tek satır açıklamaları veya `/* ... */` çok satırlı açıklamalar için kullanın

1. `{provide-unique-name}`Küme ayraçları dahil, `{}` benzersiz bir depolama hesabı adıyla değiştirin.

    > [!IMPORTANT]
    > Depolama hesabı adının Azure’da benzersiz olması gerekir. Ad yalnızca küçük harf veya rakam içermelidir. Bu, 24 karakterden uzun olamaz. Ön ek olarak **store1** kullanma ve sonra adınızın baş harflerini ve bugünün tarihini ekleme gibi bir adlandırma modelini deneyebilirsiniz. Örneğin, kullandığınız ad **store1abc09092019** gibi görünebilir.

    Depolama hesabı için benzersiz bir ad tahmin etmek kolay değildir ve büyük dağıtımları otomatikleştirmede iyi çalışmaz. Bu öğretici serisinde daha sonra, benzersiz bir ad oluşturmayı kolaylaştıran Bıcep özelliklerini kullanacaksınız.

1. Dosyayı kaydedin.

Tebrikler, ilk Bıcep dosyanızı oluşturdunuz.

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

Bir Bıcep dosyası dağıttığınızda, kaynakları içerecek bir kaynak grubu belirtirsiniz. Dağıtım komutunu çalıştırmadan önce Azure CLI veya Azure PowerShell ile kaynak grubunu oluşturun.

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

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bıcep, Azure Resource Manager şablonları (ARM şablonları) üzerinde şeffaf bir soyutlamadır. Her bir bicep dosyası, dağıtılmadan önce standart bir ARM şablonuna derlenir. Bıcep dosyanızı dağıtmadan önce bir ARM şablonuna derleyebilir ya da Bıcep dosyanızı doğrudan dağıtamazsınız. Bıcep dosyasını dağıtmak için Azure CLı veya Azure PowerShell kullanın. Oluşturduğunuz kaynak grubunu kullanın. Dağıtım geçmişinde kolayca tanımlayabilmeniz için dağıtıma bir ad verin. Kolaylık olması için, Bıcep dosyasının yolunu depolayan bir değişken de oluşturun. Her dağıtım yaptığınızda yolu yeniden yazmanız gerekmediğinden bu değişken, dağıtım komutlarını çalıştırmanızı kolaylaştırır. `{provide-the-path-to-the-bicep-file}`Küme ayraçları dahil olmak üzere, `{}` bıcep dosya uzantısı adına sahip kicep  dosyanızın yolu ile değiştirin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

Dağıtım komutu sonuçları döndürür. `ProvisioningState`Dağıtımın başarılı olup olmadığını görmek için bölümüne bakın.

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

## <a name="verify-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol menüden **kaynak grupları**' nı seçin.

1. Son yordamda kaynak grubu dağıtımını seçin. Varsayılan ad **Myresourcegroup**' dir. Kaynak grubu içinde dağıtılan bir kaynak görmezsiniz.

1. Genel bakışın sağ üst köşesinde, dağıtımın durumunun görüntülendiğini görebilirsiniz. **1 başarılı**' i seçin.

   ![Dağıtım durumunu görüntüle](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Kaynak grubu için bir dağıtım geçmişi görürsünüz. **Firstbıcep**'yi seçin.

   ![Dağıtım seçin](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Dağıtımın bir özetini görürsünüz. Bir depolama hesabı dağıtıldı. Sol tarafta, girdileri, çıkışları ve dağıtım sırasında kullanılan şablonu görüntüleyebilirsiniz.

   ![Dağıtım özetini görüntüle](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silmek isteyebilirsiniz.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'a dağıtmak için basit bir Bıcep dosyası oluşturdunuz.  Sonraki öğreticilerde, bir bicep dosyasına parametreler, değişkenler, çıktılar ve modüller eklemeyi öğreneceksiniz. Bu özellikler, çok daha karmaşık bicep dosyaları için yapı taşlarıdır.

> [!div class="nextstepaction"]
> [Parametreler ekleme](bicep-tutorial-add-parameters.md)
