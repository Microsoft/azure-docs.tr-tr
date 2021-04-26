---
title: Azure Işlevleri çalışma zamanı sürümlerini hedefleme
description: Azure Işlevleri, çalışma zamanının birden çok sürümünü destekler. Azure 'da barındırılan bir işlev uygulamasının çalışma zamanı sürümünü belirtmeyi öğrenin.
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ca107ec2f0ce04bf7b1eae3a98087217c267d33d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830925"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Işlevleri çalışma zamanı sürümlerini hedefleme

Bir işlev uygulaması, Azure Işlevleri çalışma zamanının belirli bir sürümünde çalışır. Üç ana sürüm vardır: [3. x, 2. x ve 1. x](functions-versions.md). Varsayılan olarak, işlev uygulamaları çalışma zamanının sürüm 3. x ' de oluşturulur. Bu makalede, Azure 'da bir işlev uygulamasının seçtiğiniz sürümde çalışacak şekilde nasıl yapılandırılacağı açıklanmaktadır. Belirli bir sürüm için yerel bir geliştirme ortamının nasıl yapılandırılacağı hakkında bilgi için bkz. [Code ve Azure işlevlerini yerel olarak test](functions-run-local.md)etme.

Belirli bir sürümü el ile hedeflediğiniz şekilde, Windows veya Linux 'u çalıştırıp çalıştırdığınıza bağlı olarak değişir.

## <a name="automatic-and-manual-version-updates"></a>Otomatik ve el ile sürüm güncelleştirmeleri

_Bu bölüm, [Linux üzerinde](#manual-version-updates-on-linux)işlev uygulamanızı çalıştırırken uygulanmaz._

Azure Işlevleri, bir işlev uygulamasındaki uygulama ayarını kullanarak Windows çalışma zamanının belirli bir sürümünü hedeflemenizi sağlar `FUNCTIONS_EXTENSION_VERSION` . İşlev uygulaması, açıkça yeni bir sürüme taşımayı seçinceye kadar belirtilen ana sürümde tutulur. Yalnızca ana sürümü belirtirseniz, işlev uygulaması kullanılabilir hale geldiğinde çalışma zamanının yeni ikincil sürümlerine otomatik olarak güncelleştirilir. Yeni ikincil sürümler, son değişiklikleri sunmamalıdır. 

Küçük bir sürüm belirtirseniz (örneğin, "2.0.12345"), işlev uygulaması, açıkça değiştirene kadar bu belirli sürüme sabitlenmiştir. Eski küçük sürümler düzenli olarak üretim ortamından kaldırılır. İkincil sürümünüz kaldırılırsa, işlev uygulamanız ' de ayarlanan sürüm yerine en son sürümde çalışmaya geri döner `FUNCTIONS_EXTENSION_VERSION` . Bu nedenle, belirli bir ikincil sürümü gerektiren işlev uygulamanızla ilgili sorunları hızlıca çözmeniz gerekir. Ardından, ana sürümü hedeflemek için geri dönebilirsiniz. Küçük sürüm kaldırma işlemleri [App Service bildirilerinde](https://github.com/Azure/app-service-announcements/issues)duyurulur.

> [!NOTE]
> Azure Işlevlerinin belirli bir ana sürümüne sabitleyebilir ve sonra Visual Studio 'Yu kullanarak Azure 'a yayımlamayı denerseniz, en son sürüme güncelleştirmenizi isteyip istemediğinizi soran bir iletişim kutusu penceresi açılır veya yayımlamayı iptal edersiniz. Bunu önlemek için, `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` dosyanıza özelliği ekleyin `.csproj` .

Yeni bir sürüm herkese açık olduğunda, portalda bir istem bu sürüme kadar ilerme şansı sağlar. Yeni bir sürüme taşıdıktan sonra, `FUNCTIONS_EXTENSION_VERSION` uygulama ayarını her zaman önceki bir sürüme geri gitmek için kullanabilirsiniz.

Aşağıdaki tabloda, `FUNCTIONS_EXTENSION_VERSION` otomatik güncelleştirmeleri etkinleştirmek için her ana sürüm için değerler gösterilmektedir:

| Ana sürüm | `FUNCTIONS_EXTENSION_VERSION` deeri |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 'in  | `~1` |

Çalışma zamanı sürümünde yapılan bir değişiklik, bir işlev uygulamasının yeniden başlatılmasına neden olur.

>[!NOTE]
>.Net `~2.0` Core 3,1 ' e otomatik yükseltmeyi devre dışı bırakmak için sabitlenmiş .net Function uygulamaları. Daha fazla bilgi için bkz. [işlevler v2. x konuları](functions-dotnet-class-library.md#functions-v2x-considerations).  

## <a name="view-and-update-the-current-runtime-version"></a>Geçerli çalışma zamanı sürümünü görüntüle ve Güncelleştir

_Bu bölüm, [Linux üzerinde](#manual-version-updates-on-linux)işlev uygulamanızı çalıştırırken uygulanmaz._

İşlev uygulamanız tarafından kullanılan çalışma zamanı sürümünü değiştirebilirsiniz. Son değişiklikler nedeniyle, işlev uygulamanızda herhangi bir işlev oluşturmadan önce çalışma zamanı sürümünü değiştirebilirsiniz. 

> [!IMPORTANT]
> Çalışma zamanı sürümü ayarı tarafından belirlense de `FUNCTIONS_EXTENSION_VERSION` , bu değişikliği yalnızca Azure Portal, ayarı doğrudan değiştirerek değil, yapmanız gerekir. Bunun nedeni, portalın yaptığınız değişiklikleri doğrulaması ve ilgili diğer değişiklikleri gerekli hale getirir.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Azure portal kullanarak, zaten işlevleri bulunan bir işlev uygulamasının çalışma zamanı sürümünü değiştiremezsiniz.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Ayrıca, Azure CLı 'dan ' i görüntüleyebilir ve ayarlayabilirsiniz `FUNCTIONS_EXTENSION_VERSION` .  

Azure CLı 'yı kullanarak, [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) komutuyla geçerli çalışma zamanı sürümünü görüntüleyin.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Bu kodda, öğesini `<function_app>` işlev uygulamanızın adıyla değiştirin. Ayrıca `<my_resource_group>` , işlev uygulamanız için kaynak grubunun adıyla değiştirin. 

`FUNCTIONS_EXTENSION_VERSION`Netme için kesilmiş olan aşağıdaki çıktıda görürsünüz:

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

`FUNCTIONS_EXTENSION_VERSION`İşlev uygulamasındaki ayarı [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) komutuyla güncelleştirebilirsiniz.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

`<FUNCTION_APP>`İşlev uygulamanızın adıyla değiştirin. Ayrıca `<RESOURCE_GROUP>` , işlev uygulamanız için kaynak grubunun adıyla değiştirin. Ayrıca, öğesini `<VERSION>` belirli bir sürümle veya, ya da ile değiştirin `~3` `~2` `~1` .

[Azure Cloud Shell](../cloud-shell/overview.md)komutu çalıştırmak için önceki kod örneğinde **deneyin** öğesini seçin. Bu komutu yürütmek için [Azure CLI 'yı yerel olarak](/cli/azure/install-azure-cli) da çalıştırabilirsiniz. Yerel olarak çalışırken, önce oturum açmak için [az Login](/cli/azure/reference-index#az_login) çalıştırmanız gerekir.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure Işlevleri çalışma zamanını denetlemek için aşağıdaki cmdlet 'i kullanın: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

`<FUNCTION_APP>`İşlev uygulamanızın adıyla değiştirin `<RESOURCE_GROUP>` . Ayarın geçerli değeri, `FUNCTIONS_EXTENSION_VERSION` karma tablosunda döndürülür.

Işlevler çalışma zamanını değiştirmek için aşağıdaki betiği kullanın:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Daha önce olduğu gibi, `<FUNCTION_APP>` işlev uygulamanızın adıyla ve `<RESOURCE_GROUP>` kaynak grubunun adıyla değiştirin. Ayrıca, ya da gibi `<VERSION>` belirli bir sürüm veya ana sürümle değiştirin `~2` `~3` . `FUNCTIONS_EXTENSION_VERSION`Döndürülen karma tablosunda ayarının güncelleştirilmiş değerini doğrulayabilirsiniz. 

---

İşlev uygulaması, uygulama ayarında değişiklik yapıldıktan sonra yeniden başlatılır.

## <a name="manual-version-updates-on-linux"></a>Linux üzerinde el ile sürüm güncelleştirmeleri

Bir Linux işlev uygulamasını belirli bir ana bilgisayar sürümüne sabitlemek için, görüntü URL 'sini site yapılandırması içindeki ' LinuxFxVersion ' alanında belirtirsiniz. Örneğin: bir düğüm 10 işlev uygulamasını söyleyin ana bilgisayar sürümü 3.0.13142 'e sabitlemek istiyoruz

**Linux App Service/elastik Premium uygulamaları** için- `LinuxFxVersion` olarak ayarlayın `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

**Linux tüketim uygulamaları** için- `LinuxFxVersion` olarak ayarlayın `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .

# <a name="portal"></a>[Portal](#tab/portal)

İşlev uygulamaları için site yapılandırma ayarlarını görüntüleme ve değiştirme Azure portal desteklenmez. Bunun yerine Azure CLı kullanın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLı kullanarak öğesini görüntüleyebilir ve ayarlayabilirsiniz `LinuxFxVersion` .  

Geçerli çalışma zamanı sürümünü görüntülemek için [az functionapp config Show](/cli/azure/functionapp/config) komutuyla kullanın.

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

Bu kodda, öğesini `<function_app>` işlev uygulamanızın adıyla değiştirin. Ayrıca `<my_resource_group>` , işlev uygulamanız için kaynak grubunun adıyla değiştirin. Geçerli değeri `linuxFxVersion` döndürüldü.

`linuxFxVersion`İşlev uygulamasındaki ayarı güncelleştirmek için [az functionapp config Set](/cli/azure/functionapp/config) komutunu kullanın.

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

`<FUNCTION_APP>`İşlev uygulamanızın adıyla değiştirin. Ayrıca `<RESOURCE_GROUP>` , işlev uygulamanız için kaynak grubunun adıyla değiştirin. Ayrıca, `<LINUX_FX_VERSION>` yukarıda açıklanan şekilde belirli bir görüntünün değeri ile değiştirin.

Yukarıdaki kod örneğinde **deneyin** ' i seçerek bu komutu [Azure Cloud Shell](../cloud-shell/overview.md) çalıştırabilirsiniz. Ayrıca, oturum açmak için [az Login](/cli/azure/reference-index#az_login) komutunu çalıştırdıktan sonra bu komutu yürütmek IÇIN [Azure CLI 'yı yerel olarak](/cli/azure/install-azure-cli) da kullanabilirsiniz.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure PowerShell Şu anda ayarlamak için kullanılamaz `linuxFxVersion` . Bunun yerine Azure CLı kullanın.

---

Uygulama işlevi, site yapılandırmasına değişiklik yapıldıktan sonra yeniden başlatılır.

> [!NOTE]
> Bir tüketim planında çalışan uygulamalar için `LinuxFxVersion` belirli bir görüntüye ayarlandığında soğuk başlangıç süreleri artırılabilir. Bunun nedeni, belirli bir görüntüye sabitlemenin Işlevlerin bazı soğuk başlatma iyileştirmeleri kullanmasını önler. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yerel geliştirme ortamınızda 2,0 çalışma zamanını hedefleyin](functions-run-local.md)

> [!div class="nextstepaction"]
> [Bkz. çalışma zamanı sürümleri için sürüm notları](https://github.com/Azure/azure-webjobs-sdk-script/releases)
