---
title: Azure Işlevleri çalışma zamanı sürümlerini hedefleme
description: Azure Işlevleri, çalışma zamanının birden çok sürümünü destekler. Azure 'da barındırılan bir işlev uygulamasının çalışma zamanı sürümünü belirtmeyi öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: c30dbad9e2d433920ade6890eabd85f083f9d968
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596823"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Işlevleri çalışma zamanı sürümlerini hedefleme

Bir işlev uygulaması, Azure Işlevleri çalışma zamanının belirli bir sürümünde çalışır. Önizleme aşamasında sürüm 3. x olan iki ana sürüm vardır: [1. x ve 2. x](functions-versions.md). Varsayılan olarak, çalışma zamanının 2. x sürümünü oluşturulan işlev uygulamaları. Bu makalede, Azure 'da bir işlev uygulamasının seçtiğiniz sürümde çalışacak şekilde nasıl yapılandırılacağı açıklanmaktadır. Belirli bir sürüm için yerel bir geliştirme ortamının nasıl yapılandırılacağı hakkında bilgi için bkz. [Code ve Azure işlevlerini yerel olarak test](functions-run-local.md)etme.

## <a name="automatic-and-manual-version-updates"></a>Otomatik ve el ile sürüm güncelleştirmeleri

Azure Işlevleri, bir işlev uygulamasındaki `FUNCTIONS_EXTENSION_VERSION` uygulama ayarını kullanarak çalışma zamanının belirli bir sürümünü hedeflemenizi sağlar. İşlev uygulaması, açıkça yeni bir sürüme taşımayı seçinceye kadar belirtilen ana sürümde tutulur.

Yalnızca ana sürümü belirtirseniz, işlev uygulaması kullanılabilir hale geldiğinde çalışma zamanının yeni ikincil sürümlerine otomatik olarak güncelleştirilir. Yeni ikincil sürümler, son değişiklikleri sunmaz. Küçük bir sürüm belirtirseniz (örneğin, "2.0.12345"), işlev uygulaması, açıkça değiştirene kadar bu belirli sürüme sabitlenmiştir.

> [!NOTE]
> Azure Işlevleri 'nin belirli bir sürümüne sabitleyebilir ve sonra Visual Studio 'Yu kullanarak Azure 'a yayımlamayı denerseniz, en son sürüme güncelleştirmenizi isteyip istemediğinizi soran bir iletişim kutusu penceresi açılır veya yayımlamayı iptal edersiniz. Bunu önlemek için, `.csproj` dosyanıza `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` özelliğini ekleyin.

Yeni bir sürüm herkese açık olduğunda, portalda bir istem bu sürüme kadar ilerme şansı sağlar. Yeni bir sürüme taşıdıktan sonra, `FUNCTIONS_EXTENSION_VERSION` uygulaması ayarını her zaman önceki bir sürüme geri dönmek için kullanabilirsiniz.

Aşağıdaki tabloda, otomatik güncelleştirmeleri etkinleştirmek üzere her ana sürüm için `FUNCTIONS_EXTENSION_VERSION` değerleri gösterilmektedir:

| Ana sürüm | `FUNCTIONS_EXTENSION_VERSION` değeri |
| ------------- | ----------------------------------- |
| 3. x (Önizleme) | `~3` |
| 2.x  | `~2` |
| 'in | `~1` |

Çalışma zamanı sürümünde yapılan bir değişiklik, bir işlev uygulamasının yeniden başlatılmasına neden olur.

## <a name="view-and-update-the-current-runtime-version"></a>Geçerli çalışma zamanı sürümünü görüntüle ve Güncelleştir

İşlev uygulamanız tarafından kullanılan çalışma zamanı sürümünü değiştirebilirsiniz. Son değişiklikler nedeniyle, işlev uygulamanızda herhangi bir işlev oluşturmadan önce çalışma zamanı sürümünü değiştirebilirsiniz. 

> [!IMPORTANT]
> Çalışma zamanı sürümü `FUNCTIONS_EXTENSION_VERSION` ayarı tarafından belirlendiği halde, ayarı doğrudan değiştirerek bu değişikliği Azure portal yapmalısınız. Bunun nedeni, portalın yaptığınız değişiklikleri doğrulaması ve ilgili diğer değişiklikleri gerekli hale getirir.

### <a name="from-the-azure-portal"></a>Azure portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Azure portal kullanarak, zaten işlevleri bulunan bir işlev uygulamasının çalışma zamanı sürümünü değiştiremezsiniz.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Azure CLı 'dan

Ayrıca Azure CLı 'dan `FUNCTIONS_EXTENSION_VERSION` görüntüleyebilir ve ayarlayabilirsiniz.

>[!NOTE]
>Diğer ayarlar çalışma zamanı sürümünden etkilenbileceğinden, portalda sürümü değiştirmelisiniz. Portal, çalışma zamanı sürümlerini değiştirdiğinizde Node. js sürümü ve çalışma zamanı yığını gibi diğer gerekli güncelleştirmeleri otomatik olarak yapar.  

Azure CLı 'yı kullanarak, [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) komutuyla geçerli çalışma zamanı sürümünü görüntüleyin.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Bu kodda `<function_app>`, işlev uygulamanızın adıyla değiştirin. Ayrıca `<my_resource_group>`, işlev uygulamanızın kaynak grubunun adıyla değiştirin. 

Aşağıdaki çıktıda `FUNCTIONS_EXTENSION_VERSION` görürsünüz ve bu, açıklık açısından kısaltıldı:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

İşlev uygulamasındaki `FUNCTIONS_EXTENSION_VERSION` ayarını [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) komutuyla güncelleştirebilirsiniz.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

@No__t_0, işlev uygulamanızın adıyla değiştirin. Ayrıca `<my_resource_group>`, işlev uygulamanızın kaynak grubunun adıyla değiştirin. Ayrıca, `<version>`, 1. x çalışma zamanının geçerli bir sürümüyle veya sürüm 2. x için `~2` ile değiştirin.

Yukarıdaki kod örneğinde **deneyin** ' i seçerek bu komutu [Azure Cloud Shell](../cloud-shell/overview.md) çalıştırabilirsiniz. Ayrıca, oturum açmak için [az Login](/cli/azure/reference-index#az-login) komutunu çalıştırdıktan sonra bu komutu yürütmek IÇIN [Azure CLI 'yı yerel olarak](/cli/azure/install-azure-cli) da kullanabilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yerel geliştirme ortamınızda 2,0 çalışma zamanını hedefleyin](functions-run-local.md)

> [!div class="nextstepaction"]
> [Bkz. çalışma zamanı sürümleri için sürüm notları](https://github.com/Azure/azure-webjobs-sdk-script/releases)
