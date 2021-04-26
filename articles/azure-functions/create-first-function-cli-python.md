---
title: Komut satırından bir Python işlevi oluşturma-Azure Işlevleri
description: Komut satırından bir Python işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: b006f006c9fb45c9a7d80e815f95bec812e5ec3f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831843"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: Azure 'da komut satırından bir Python işlevi oluşturma

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Bu makalede, HTTP isteklerine yanıt veren bir Python işlevi oluşturmak için komut satırı araçlarını kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [Visual Studio Code tabanlı bir sürümü](create-first-function-vs-code-python.md) de vardır.

## <a name="configure-your-local-environment"></a>Yerel ortamınızı yapılandırma

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x.
  
+ Azure kaynakları oluşturmak için aşağıdaki araçlardan biri:

    + [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya üzeri.

    + Sürüm 5,0 veya üzeri [Azure PowerShell](/powershell/azure/install-az-ps) .

+ [Azure Işlevleri tarafından desteklenen Python sürümleri](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>Önkoşul denetimi

Azure CLı veya Azure kaynakları oluşturmak için Azure PowerShell kullanıp kullanmayacağınızı temel alan önkoşulları doğrulayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `az --version`Azure CLI sürümünün 2,4 veya üzeri olduğunu denetlemek için ' i çalıştırın.

+ `az login`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

+ `python --version` `py --version` Python sürüm raporlarınızı 3.8. x, 3.7. x veya 3.6. x olarak denetlemek için (Linux/MacOS) veya (Windows) öğesini çalıştırın.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `(Get-Module -ListAvailable Az).Version`Sürüm 5,0 veya üstünü çalıştırın ve doğrulayın. 

+ `Connect-AzAccount`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

+ `python --version` `py --version` Python sürüm raporlarınızı 3.8. x, 3.7. x veya 3.6. x olarak denetlemek için (Linux/MacOS) veya (Windows) öğesini çalıştırın.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Sanal ortam oluşturma ve etkinleştirme

Uygun bir klasörde, adlı bir sanal ortam oluşturmak ve etkinleştirmek için aşağıdaki komutları çalıştırın `.venv` . Azure Işlevleri tarafından desteklenen Python 3,8, 3,7 veya 3,6 kullandığınızdan emin olun.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Python, venv paketini Linux dağıtımına yüklememediyse aşağıdaki komutu çalıştırın:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Bu etkinleştirilmiş sanal ortamda sonraki tüm komutları çalıştırırsınız. 

## <a name="create-a-local-function-project"></a>Yerel işlev projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir tetikleyiciye yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, tek bir işlev içeren bir işlev projesi oluşturursunuz.

1. `func init`Belirtilen çalışma zamanına sahip *Localfunctionproj* adlı klasörde bir işlevler projesi oluşturmak için komutu aşağıdaki gibi çalıştırın:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Proje klasörüne gidin:

    ```console
    cd LocalFunctionProj
    ```
    
    Bu klasör, [local.settings.js](functions-run-local.md#local-settings-file) ve [host.js](functions-host-json.md)adlı yapılandırma dosyaları da dahil olmak üzere, proje için çeşitli dosyalar içerir. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.

1. Aşağıdaki komutu kullanarak, `--name` bağımsız değişkenin işlevinizin (HttpExample) benzersiz adı olduğu ve `--template` bağımsız değişkeninin işlevin TETIKLEYICISINI (http) belirttiği, projenize bir işlev ekleyin.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` Projenin seçtiği dile uygun bir kod dosyası ve *üzerindefunction.js* adlı bir yapılandırma dosyası içeren işlev adıyla eşleşen bir alt klasör oluşturur.

### <a name="optional-examine-the-file-contents"></a>Seçim Dosya içeriğini inceleyin

İsterseniz, [işlevi yerel olarak çalıştırmayı](#run-the-function-locally) atlayıp dosya içeriğini daha sonra incelemenizi sağlayabilirsiniz.

#### <a name="__init__py"></a>\_\_init \_ \_ . Kopyala

*\_ \_ init \_ \_ . Kopyala* `main()` , *function.jsüzerindeki* yapılandırmaya göre tetiklenen bir Python işlevi içerir.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Bir HTTP tetikleyicisi için işlev, `req` *function.json* öğesinde tanımlanan şekilde değişkende istek verilerini alır. `req` , [Azure. Functions. HttpRequest sınıfının](/python/api/azure-functions/azure.functions.httprequest)bir örneğidir. function.jsolarak tanımlanan dönüş nesnesi, `$return` [Azure. Functions. HttpResponse sınıfının](/python/api/azure-functions/azure.functions.httpresponse)bir örneğidir.  Daha fazla bilgi için bkz. [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

*function.js* , `bindings` tetikleyici türü de dahil olmak üzere işlevin giriş ve çıkışını tanımlayan bir yapılandırma dosyasıdır.

İsterseniz `scriptFile` farklı bir Python dosyası çağırmak için ' i değiştirebilirsiniz.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Her bağlama bir yön, tür ve benzersiz bir ad gerektirir. HTTP tetikleyicisinin tür ve çıkış bağlaması türünde bir giriş bağlaması vardır [`httpTrigger`](functions-bindings-http-webhook-trigger.md) [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>İşleviniz için destekleyici Azure kaynakları oluşturma

İşlev kodunuzu Azure 'a dağıtabilmeniz için önce üç kaynak oluşturmanız gerekir:

- İlgili kaynaklar için mantıksal kapsayıcı olan bir kaynak grubu.
- Projelerinizle ilgili durumu ve diğer bilgileri tutan bir depolama hesabı.
- İşlev kodunuzu yürütmek için ortam sağlayan bir işlev uygulaması. Bir işlev uygulaması yerel işlev projenize eşlenir ve kaynakların daha kolay yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanızı sağlar.

Bu öğeleri oluşturmak için aşağıdaki komutları kullanın. Hem Azure CLı hem de PowerShell desteklenir.

1. Şimdiye kadar yapmadıysanız Azure 'da oturum açın:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [Az Login](/cli/azure/reference-index#az_login) komutu sizi Azure hesabınızda oturum açar.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'i Azure hesabınızda oturum açar.

    ---

1. Bölgesinde adlı bir kaynak grubu oluşturun `AzureFunctionsQuickstart-rg` `westeurope` . 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [Az Group Create](/cli/azure/group#az_group_create) komutu bir kaynak grubu oluşturur. Genellikle kaynak grubunuzu ve kaynaklarınızı, komuttan döndürülen kullanılabilir bir bölgeyi kullanarak size yakın bir bölgede oluşturursunuz `az account list-locations` .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutu bir kaynak grubu oluşturur. Genellikle kaynak grubunuzu ve kaynaklarınızı size yakın bir bölgede, [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 'inden döndürülen kullanılabilir bir bölgeyi kullanarak oluşturursunuz.

    ---

    > [!NOTE]
    > Linux ve Windows uygulamalarını aynı kaynak grubunda barındıralamazsınız. `AzureFunctionsQuickstart-rg`Bir Windows işlev uygulaması veya Web uygulaması ile adlandırılmış bir kaynak grubunuz varsa, farklı bir kaynak grubu kullanmanız gerekir.

1. Kaynak grubunuzda ve bölgenizde genel amaçlı bir depolama hesabı oluşturun:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [Az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutu depolama hesabı oluşturur. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 'i depolama hesabı oluşturur.

    ---

    Önceki örnekte, `<STORAGE_NAME>` Azure depolama 'da size uygun ve benzersiz olan bir adla değiştirin. Adlar yalnızca üç ile 24 karakter arasında ve küçük harflerden oluşmalıdır. `Standard_LRS`[işlevleri tarafından desteklenen](storage-considerations.md#storage-account-requirements), genel amaçlı bir hesabı belirtir.
    
    Depolama hesabı bu hızlı başlangıç için yalnızca birkaç ilay (USD) doğurur.

1. Azure 'da işlev uygulaması oluşturma:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    [Az functionapp Create](/cli/azure/functionapp#az_functionapp_create) komutu, Azure 'da işlev uygulaması oluşturur. Python 3,7 veya 3,6 kullanıyorsanız, sırasıyla veya olarak değiştirin `--runtime-version` `3.7` `3.6` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 'i Azure 'da işlev uygulaması oluşturur. Python 3,7 veya 3,6 kullanıyorsanız, sırasıyla veya olarak değiştirin `-RuntimeVersion` `3.7` `3.6` .

    ---
    
    Önceki örnekte, değerini, `<STORAGE_NAME>` önceki adımda kullandığınız hesabın adıyla değiştirin ve öğesini `<APP_NAME>` sizin için uygun olan bir genel benzersiz adla değiştirin.  `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. 
    
    Bu komut, burada yaptığınız kullanım miktarı için ücretsiz olan [Azure Işlevleri tüketim planı](consumption-plan.md)altında, belirtilen dil çalışma zamanında çalışan bir işlev uygulaması oluşturur. Bu komut Ayrıca, aynı kaynak grubunda, işlev uygulamanızı izleyebilmeniz ve günlükleri görüntüleyebileceğiniz ilişkili bir Azure Application Insights örneğini sağlar. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Azure portal Application Insights ' de neredeyse gerçek zamanlı [akış günlüklerini](functions-run-local.md#enable-streaming-logs) görüntülemek için aşağıdaki komutu çalıştırın:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Ayrı bir Terminal penceresinde veya tarayıcıda, uzak işlevi yeniden çağırın. Azure 'da işlev yürütmenin ayrıntılı günlüğü terminalde gösterilir. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/python-functions-qs-survey)
