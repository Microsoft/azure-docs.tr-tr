---
title: Azure Işlevleri için komut satırından bir Python işlevi oluşturma
description: Komut satırından bir Python işlevi oluşturmayı ve yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e144304ae1b36ca02d4b8796e7994e87b09505d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831105"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: Azure 'da komut satırından bir Python işlevi oluşturma

> [!div class="op_single_selector" title1="İşlev dilinizi seçin: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Bu makalede, HTTP isteklerine yanıt veren bir Python işlevi oluşturmak için komut satırı araçlarını kullanırsınız. Kodu yerel olarak test ettikten sonra, <abbr title="Sunucunun tüm ayrıntılarının uygulama geliştiricileri için saydam olduğu ve kodu dağıtma ve yönetme sürecini kolaylaştıran bir çalışma zamanı bilgi işlem ortamı.">sunucusuz</abbr> ortam <abbr title="Uygulamalar için düşük maliyetli bir sunucusuz bilgi işlem ortamı sağlayan bir Azure hizmeti.">Azure İşlevleri</abbr>.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [Visual Studio Code tabanlı bir sürümü](create-first-function-vs-code-python.md) de vardır.

## <a name="1-configure-your-environment"></a>1. ortamınızı yapılandırın

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Azure <abbr title="Azure kullanımı için fatura bilgilerini tutan profil.">account</abbr> etkin bir <abbr title="Azure 'da, genellikle bir kuruluş içindeki bir birey veya departmanla ilişkili kaynakları yönettiğiniz temel kuruluş yapısıdır.">aboneliği</abbr>. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x. 
  
+ Her iki <abbr title="Azure portal kullanımına alternatif olarak, yerel geliştirme bilgisayarınızdan Azure kaynaklarıyla çalışmaya yönelik platformlar arası komut satırı araçları kümesi.">Azure CLI</abbr> veya <abbr title="Azure portal kullanımına alternatif olarak, yerel geliştirme bilgisayarınızdan Azure kaynaklarıyla çalışmaya yönelik komutlar sağlayan bir PowerShell modülü.">Azure PowerShell</abbr> Azure kaynakları oluşturmak için:

    + [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya üzeri.

    + Sürüm 5,0 veya üzeri [Azure PowerShell](/powershell/azure/install-az-ps) .

+ [Python 3,8 (64-bit](https://www.python.org/downloads/release/python-382/)), [python 3,7 (64-](https://www.python.org/downloads/release/python-375/)bit), Python [3,6 (64](https://www.python.org/downloads/release/python-368/)-bit), tek başına Azure işlevleri sürüm 3. x tarafından desteklenir.

### <a name="11-prerequisite-check"></a>1,1 önkoşul denetimi

Azure CLı 'yi mi yoksa Azure kaynakları oluşturmak için Azure PowerShell mı kullandığınızı bağlı olarak önkoşulları doğrulayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Bir Terminal veya komut `func --version` penceresinde, <abbr title="Yerel bilgisayarınızda Azure Işlevleri ile çalışmaya yönelik komut satırı araçları kümesi.">Azure Functions Core Tools</abbr> sürüm 3. x ' dir.

+ `az --version`Azure CLI sürümünün 2,4 veya üzeri olduğunu denetlemek için ' i çalıştırın.

+ `az login`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

+ `python --version` `py --version` Python sürüm raporlarınızı 3.8. x, 3.7. x veya 3.6. x olarak denetlemek için (Linux/MacOS) veya (Windows) öğesini çalıştırın.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Bir Terminal veya komut `func --version` penceresinde, <abbr title="Yerel bilgisayarınızda Azure Işlevleri ile çalışmaya yönelik komut satırı araçları kümesi.">Azure Functions Core Tools</abbr> sürüm 3. x ' dir.

+ `(Get-Module -ListAvailable Az).Version`Sürüm 5,0 veya üstünü çalıştırın ve doğrulayın. 

+ `Connect-AzAccount`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

+ `python --version` `py --version` Python sürüm raporlarınızı 3.8. x, 3.7. x veya 3.6. x olarak denetlemek için (Linux/MacOS) veya (Windows) öğesini çalıştırın.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> sanal ortam oluşturma ve etkinleştirme

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. yerel bir işlev projesi oluşturma

Bu bölümde, yerel bir <abbr title="Birlikte dağıtılabilecek ve yönetilebilen bir veya daha fazla bağımsız işlev için mantıksal bir kapsayıcı.">Azure Işlevleri projesi</abbr> Python 'da. Projedeki her bir işlev belirli bir <abbr title="Bir HTTP isteği, kuyruk iletisi veya belirli bir zaman gibi işlevin kodunu çağıran olay türü.">Tetikleyicinin</abbr>.

1. `func init`Belirtilen çalışma zamanına sahip *Localfunctionproj* adlı klasörde bir işlevler projesi oluşturmak için komutunu çalıştırın:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Proje klasörüne gidin:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>LocalFunctionProj klasöründe oluşturulan özellikler nelerdir?</strong></summary>
    
    Bu klasör, [local.settings.js](functions-run-local.md#local-settings-file) ve [host.js](functions-host-json.md)adlı yapılandırma dosyaları da dahil olmak üzere, proje için çeşitli dosyalar içerir. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.
    </details>

1. Aşağıdaki komutu kullanarak projenize bir işlev ekleyin:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    `--name`Bağımsız değişkeni, işlevinizin benzersiz adıdır (HttpExample).

    `--template`Bağımsız değişken işlevin tetikleyicisini (http) belirtir.
    
    `func new`işlevin koduna ve *function.js* adlı bir yapılandırma dosyasına sahip bir *\_ \_ init \_ \_ . Kopyala* dosyası içeren işlev adıyla eşleşen bir alt klasör oluşturur.

    <br/>    
    <details>
    <summary><strong>__İnit__. Kopyala için kod</strong></summary>
    
    *\_ \_ init \_ \_ . Kopyala* `main()` , *function.jsüzerindeki* yapılandırmaya göre tetiklenen bir Python işlevi içerir.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    Bir HTTP tetikleyicisi için işlev, `req` *function.json* öğesinde tanımlanan şekilde değişkende istek verilerini alır. `req` , [Azure. Functions. HttpRequest sınıfının](/python/api/azure-functions/azure.functions.httprequest)bir örneğidir. function.jsolarak tanımlanan dönüş nesnesi, `$return` [Azure. Functions. HttpResponse sınıfının](/python/api/azure-functions/azure.functions.httpresponse)bir örneğidir.  Daha fazla bilgi için bkz. [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>function.jsiçin kod</strong></summary>

    *function.js* , şunu tanımlayan bir yapılandırma dosyasıdır <abbr title="Bir işlev ve diğer kaynaklar arasında bildirime dayalı bağlantılar. Giriş bağlama işlevine veri sağlar; çıkış bağlama, işlevden diğer kaynaklara veri sağlar.">giriş ve çıkış bağlamaları</abbr> tetikleyici türü de dahil olmak üzere işlev için.
    
    İsterseniz `scriptFile` farklı bir Python dosyası çağırmak için ' i değiştirebilirsiniz.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Her bağlama bir yön, tür ve benzersiz bir ad gerektirir. HTTP tetikleyicisinin tür ve çıkış bağlaması türünde bir giriş bağlaması vardır [`httpTrigger`](functions-bindings-http-webhook-trigger.md) [`http`](functions-bindings-http-webhook-output.md) .    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. işlevi yerel olarak çalıştırın

1. Yerel Azure Işlevleri çalışma zamanı konağını *Localfunctionproj* klasöründen başlatarak işlevinizi çalıştırın:

    ```
    func start
    ```

    Çıktının sonuna doğru, aşağıdaki satırlar görünmelidir: 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>Çıktıda HttpExample görmüyorum</strong></summary>

    HttpExample görünmezse, büyük olasılıkla ana bilgisayarı projenin kök klasörü dışından başlatmış olursunuz. Bu durumda, Konağı durdurmak için <kbd>CTRL + C</kbd> tuşlarını kullanın, projenin kök klasörüne gidin ve önceki komutu yeniden çalıştırın.
    </details>

1. **Httpexample** IŞLEVINIZIN URL 'sini bu çıktıdan tarayıcıya kopyalayın ve sorgu dizesini ekleyin **? name =<YOUR_NAME>**, tam URL 'yi gibi yapar **http://localhost:7071/api/HttpExample?name=Functions** . Tarayıcı, **Hello işlevleri** gibi bir ileti görüntülemelidir:

    ![İşlevin sonuçları tarayıcıda yerel olarak çalıştırıldı](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Projenizi başlattığınız Terminal Ayrıca, istek yaptığınız sürece günlük çıktısını gösterir.

1. İşiniz bittiğinde, <kbd>CTRL + C</kbd> tuşlarını kullanın ve işlevler ana bilgisayarını durdurmak için <kbd>y</kbd> 'yi seçin.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. işleviniz için destekleyici Azure kaynakları oluşturun

İşlev kodunuzu Azure 'a dağıtabilmeniz için önce bir <abbr title="Birim olarak yönetebileceğiniz ilgili Azure kaynakları için mantıksal bir kapsayıcı.">kaynak grubu</abbr>, bir <abbr title="Tüm Azure depolama veri nesnelerinizi içeren bir hesap. Depolama hesabı, depolama verileriniz için benzersiz bir ad alanı sağlar.">depolama hesabı</abbr>ve bir <abbr title="Azure 'da sunucusuz işlevleri barındıran, işlevlerin çalıştırıldığı temel işlem ortamını sağlayan bulut kaynağı.">işlev uygulaması</abbr> Aşağıdaki komutları kullanarak:

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
 
    [Az Group Create](/cli/azure/group#az_group_create) komutu bir kaynak grubu oluşturur. Genellikle kaynak grubunuzu ve kaynaklarınızı bir <abbr title="Kaynakların ayrıldığı belirli bir Azure veri merkezine coğrafi başvuru.">region</abbr> yakın bir şekilde, komuttan döndürülen kullanılabilir bir bölgeyi kullanarak `az account list-locations` .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutu bir kaynak grubu oluşturur. Genellikle kaynak grubunuzu ve kaynaklarınızı size yakın bir bölgede, [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 'inden döndürülen kullanılabilir bir bölgeyi kullanarak oluşturursunuz.

    ---

    Linux ve Windows uygulamalarını aynı kaynak grubunda barındıralamazsınız. `AzureFunctionsQuickstart-rg`Bir Windows işlev uygulaması veya Web uygulaması ile adlandırılmış bir kaynak grubunuz varsa, farklı bir kaynak grubu kullanmanız gerekir.

1. Kaynak grubunuzda ve bölgenizde genel amaçlı bir Azure depolama hesabı oluşturun:

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

    `<STORAGE_NAME>`Sizin için uygun bir adla değiştirin ve <abbr title="Ad, tüm Azure müşterilerinin küresel olarak kullandığı tüm depolama hesaplarında benzersiz olmalıdır. Örneğin, contosobizappstorage20 içinde olduğu gibi kişisel veya şirket adı, uygulama adı ve sayısal bir tanımlayıcı birleşimini kullanabilirsiniz.">Azure depolama 'da benzersiz</abbr>. Adlar yalnızca üç ile 24 karakter arasında ve küçük harflerden oluşmalıdır. `Standard_LRS`[işlevleri tarafından desteklenen](storage-considerations.md#storage-account-requirements), genel amaçlı bir hesabı belirtir.
    
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
    
    `<STORAGE_NAME>`Önceki adımda kullandığınız hesabın adıyla değiştirin.

    `<APP_NAME>`İle Değiştir <abbr title="Tüm Azure müşterileri genelinde küresel olarak benzersiz olması gereken bir ad. Örneğin, contoso-bizapp-Func-20 ' de olduğu gibi kişisel veya kuruluş adı, uygulama adı ve sayısal bir tanımlayıcı birleşimini kullanabilirsiniz.">sizin için uygun olan genel benzersiz ad</abbr>. `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. 
    
    <br/>
    <details>
    <summary><strong>Azure 'da sağlanan kaynakların maliyeti nedir?</strong></summary>

    Bu komut, burada yaptığınız kullanım miktarı için ücretsiz olan [Azure Işlevleri tüketim planı](functions-scale.md#overview-of-plans)altında, belirtilen dil çalışma zamanında çalışan bir işlev uygulaması oluşturur. Bu komut Ayrıca, aynı kaynak grubunda, işlev uygulamanızı izleyebilmeniz ve günlükleri görüntüleyebileceğiniz ilişkili bir Azure Application Insights örneğini sağlar. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. işlev projesini Azure 'a dağıtma

İşlev uygulamanızı Azure 'da başarıyla oluşturduktan sonra, [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) komutunu kullanarak **yerel işlevler projenizi dağıtmaya** hazırsınız demektir.  

Aşağıdaki örnekte, değerini `<APP_NAME>` uygulamanızın adıyla değiştirin.

```console
func azure functionapp publish <APP_NAME>
```

`publish`Komut aşağıdaki çıktıya benzer sonuçları gösterir (basitlik için kesildi):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Azure 'da işlevi çağırın

İşleviniz bir HTTP tetikleyicisi kullandığından, tarayıcıda URL 'sine veya şunun gibi bir araçla HTTP isteği yaparak bu uygulamayı çağırabilirsiniz. <abbr title="URL 'ye HTTP istekleri oluşturmak için bir komut satırı aracı; bakýn https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Tarayıcı](#tab/browser)

Komutun çıktısında gösterilen tüm **ÇAĞıRMA URL** 'sini `publish` bir tarayıcı adres çubuğuna kopyalayın, bu sorgu parametresi **&Name = Functions**' ı ekleyin. , İşlevi yerel olarak çalıştırdığınızda tarayıcı benzer bir çıktı görüntülemelidir.

![Azure üzerinde çalışan işlevin çıktısı bir tarayıcıda](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

[`curl`](https://curl.haxx.se/) **&Name = Functions** parametresini ekleyerek **Invoke URL 'si** ile çalıştırın. Komutun çıktısı, "Hello Functions" metni olmalıdır.

![İşlevin çıktısı, Azure 'da kıvrımlı kullanarak çalıştırıldı](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7,1 gerçek zamanlı akış günlüklerini görüntüleme

Azure portal Application Insights ' de neredeyse gerçek zamanlı [akış günlüklerini](functions-run-local.md#enable-streaming-logs) görüntülemek için aşağıdaki komutu çalıştırın:

```console
func azure functionapp logstream <APP_NAME> --browser
```

`<APP_NAME>`İşlev uygulamanızın adıyla değiştirin.

Ayrı bir Terminal penceresinde veya tarayıcıda, uzak işlevi yeniden çağırın. Azure 'da işlev yürütmenin ayrıntılı günlüğü terminalde gösterilir. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Kaynakları Temizleme

[Sonraki adıma](#next-steps) devam ederseniz ve <abbr title="Bir işlevi bir depolama kuyruğu ile ilişkilendirmekte, böylece kuyrukta ileti oluşturabilmesi anlamına gelir. ">Azure depolama kuyruğu çıktı bağlama</abbr>, daha önce yapmış olduğunuz yerde oluşturacağınız sürece tüm kaynaklarınızı saklayın.

Aksi takdirde, daha fazla maliyet ödemeden kaçınmak için kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/python-functions-qs-survey)
