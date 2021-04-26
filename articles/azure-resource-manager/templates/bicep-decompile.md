---
title: JSON ile Bıcep arasında şablon dönüştürme
description: Bıcep 'den JSON 'a ve JSON 'dan Bıcep 'ye Azure Resource Manager şablonlarını dönüştürmeye yönelik komutları açıklar.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103422226"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>JSON ile Bıcep arasında ARM şablonlarını dönüştürme

Bu makalede Azure Resource Manager şablonlarını (ARM şablonları) JSON 'dan Bıcep 'ye ve Bıcep 'den JSON 'a nasıl dönüştürebileceğiniz açıklanır.

Dönüştürme komutlarını çalıştırmak için [Bıcep CLI 'nın yüklü](bicep-install.md) olması gerekir.

Dönüştürme komutları işlevsel olarak eşdeğer şablonlar üretir. Ancak, uygulamada tam olarak aynı olmayabilir. Bir şablonu JSON 'dan Bıcep 'ye dönüştürme ve sonra da JSON 'a geri dönme büyük olasılıkla özgün şablondan farklı sözdizimine sahip bir şablonla sonuçlanır. Dağıtıldığında, dönüştürülen şablonlar aynı sonuçları üretir.

## <a name="convert-from-json-to-bicep"></a>JSON 'dan Bıcep 'ye dönüştürme

Bıcep CLı, var olan herhangi bir JSON şablonunu bir bicep dosyasına derlemeyi sağlayan bir komut sağlar. Bir JSON dosyasını derlemek için şunu kullanın:

```azurecli
bicep decompile mainTemplate.json
```

Bu komut, Bıcep yazma için bir başlangıç noktası sağlar. Komut tüm şablonlar için çalışmıyor. Şu anda, iç içe geçmiş şablonlar yalnızca ' Inner ' ifade değerlendirme kapsamını kullandıklarında derlenerek parçalanamaz. Kopyalama döngüleri kullanan şablonlar derlenemiyor.

## <a name="convert-from-bicep-to-json"></a>Bıcep 'den JSON 'a dönüştürme

Bıcep CLı, Bıcep 'yi JSON 'a dönüştürmek için bir komut de sağlar. JSON dosyası oluşturmak için şunu kullanın:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Şablonu dışarı aktar ve Dönüştür

Bir kaynak grubu için şablonu dışa aktarabilir ve ardından doğrudan derlemeyi kaldırma komutuna geçirebilirsiniz. Aşağıdaki örnek, bir aktarılmış şablonun nasıl derlenmeyeceğini gösterir.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Şablonu Portal üzerinden dışarı aktarın](export-template-portal.md) .

`bicep decompile <filename>`İndirilen dosyada kullanın.

---

## <a name="side-by-side-view"></a>Yan yana görünüm

[Bicep playzemin](https://aka.ms/bicepdemo) , eşdeğer JSON ve bicep dosyalarını yan yana görüntülemenize olanak sağlar. Her iki sürümü de görmek için örnek bir şablon seçebilirsiniz. Ya da `Decompile` kendı JSON şablonunuzu karşıya yüklemeyi seçin ve eşdeğer Bıcep dosyasını görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bıcep hakkında daha fazla bilgi için bkz. [bıcep öğreticisi](./bicep-tutorial-create-first-bicep.md).
