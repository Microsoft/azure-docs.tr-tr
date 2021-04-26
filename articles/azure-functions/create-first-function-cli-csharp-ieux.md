---
title: Komut satırından C# işlevi oluşturma-Azure Işlevleri
description: Komut satırından C# işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 45173a74c0e3189c1f356aea2f8024ff15409f32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866206"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: Azure 'da komut satırından C# işlevi oluşturma

> [!div class="op_single_selector" title1="İşlev dilinizi seçin: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Bu makalede, HTTP isteklerine yanıt veren C# sınıf kitaplığı tabanlı bir işlev oluşturmak için komut satırı araçlarını kullanırsınız. Kodu yerel olarak test ettikten sonra, <abbr title="Sunucunun tüm ayrıntılarının uygulama geliştiricileri için saydam olduğu ve kodu dağıtma ve yönetme sürecini kolaylaştıran bir çalışma zamanı bilgi işlem ortamı.">sunucusuz</abbr> ortam <abbr title="Uygulamalar için düşük maliyetli bir sunucusuz bilgi işlem ortamı sağlayan Azure hizmeti.">Azure İşlevleri</abbr>.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [Visual Studio Code tabanlı bir sürümü](create-first-function-vs-code-csharp.md) de vardır.

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

+ Azure alın <abbr title="Azure kullanımı için fatura bilgilerini tutan profil.">account</abbr> etkin bir <abbr title="Azure 'da, genellikle bir kuruluş içindeki bir birey veya departmanla ilişkili kaynakları yönettiğiniz temel kuruluş yapısıdır.">aboneliği</abbr>. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download) yükler

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x sürümünü yükler.

+ Her iki <abbr title="Azure portal kullanımına alternatif olarak, yerel geliştirme bilgisayarınızdan Azure kaynaklarıyla çalışmaya yönelik platformlar arası komut satırı araçları kümesi.">Azure CLI</abbr> veya <abbr title="Azure portal kullanımına alternatif olarak, yerel geliştirme bilgisayarınızdan Azure kaynaklarıyla çalışmaya yönelik komutlar sağlayan bir PowerShell modülü.">Azure PowerShell</abbr> Azure kaynakları oluşturmak için:

    + [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya üzeri.

    + Sürüm 5,0 veya üzeri [Azure PowerShell](/powershell/azure/install-az-ps) .

---

### <a name="2-verify-prerequisites"></a>2. önkoşulları doğrulama

Azure CLı 'yi mi yoksa Azure kaynakları oluşturmak için Azure PowerShell mı kullandığınızı bağlı olarak önkoşulları doğrulayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Bir Terminal veya komut `func --version` penceresinde, <abbr title="Yerel bilgisayarınızda Azure Işlevleri ile çalışmaya yönelik komut satırı araçları kümesi.">Azure Functions Core Tools</abbr> sürüm 3. x ' dir.

+ **Çalıştır** `az --version` Azure CLı sürümünün 2,4 veya üzeri olduğunu denetlemek için.

+ **Çalıştır** `az login` Azure 'da oturum açın ve etkin bir aboneliği doğrulayın.

+ **Çalıştır** `dotnet --list-sdks` .NET Core SDK 3.1. x sürümünün yüklü olduğunu denetlemek için

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Çalıştır** `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için.

+ **Çalıştır** `(Get-Module -ListAvailable Az).Version` ve sürüm 5,0 veya üstünü doğrulayın. 

+ **Çalıştır** `Connect-AzAccount` Azure 'da oturum açın ve etkin bir aboneliği doğrulayın.

+ **Çalıştır** `dotnet --list-sdks` .NET Core SDK 3.1. x sürümünün yüklü olduğunu denetlemek için

---

## <a name="3-create-a-local-function-project"></a>3. yerel bir işlev projesi oluşturma

Bu bölümde, yerel bir <abbr title="Birlikte dağıtılabilecek ve yönetilebilen bir veya daha fazla bağımsız işlev için mantıksal bir kapsayıcı.">Azure Işlevleri projesi</abbr> C# dilinde. Projedeki her bir işlev belirli bir <abbr title="HTTP isteği, kuyruk iletisi veya belirli bir zaman gibi işlevin kodunu çağıran bir olay.">Tetikleyicinin</abbr>.

1. `func init`Belirtilen çalışma zamanına sahip *Localfunctionproj* adlı klasörde bir işlevler projesi oluşturmak için komutunu çalıştırın:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Çalıştır** ' CD LocalFunctionProj ' öğesine gidin <abbr title="Bu klasör, local.settings.jsve host.jsadlı yapılandırma dosyaları da dahil olmak üzere, proje için çeşitli dosyalar içerir. local.settings.json, Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya,. gitignore dosyasında varsayılan olarak kaynak denetiminden çıkarılır.">proje klasörü</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Aşağıdaki komutu kullanarak projenize bir işlev ekleyin:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    `--name`Bağımsız değişkeni, işlevinizin benzersiz adıdır (HttpExample).

    `--template`Bağımsız değişken işlevin tetikleyicisini (http) belirtir.

    
    <br/>   
    <details>  
    <summary><strong>İsteğe bağlı: HttpExample. cs kodu</strong></summary>  
    
    *Httpexample. cs* , `Run` değişkende istek verilerini alan bir yöntem içerir `req` , bu da tetikleme davranışını tanımlayan **Httptriggerattribute** ile donatılmış bir [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) 'dir.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Dönüş nesnesi, bir [Okobjectresult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) veya [Badrequestobjectresult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) olarak bir yanıt Iletisi döndüren bir [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) . Daha fazla bilgi için bkz. [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](./functions-bindings-http-webhook.md?tabs=csharp).  
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


1. <kbd>CTRL + C</kbd> ' yi seçin ve işlevler ana bilgisayarını durdurmak için <kbd>y</kbd> ' yi seçin.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. işleviniz için destekleyici Azure kaynakları oluşturun

İşlev kodunuzu Azure 'a dağıtabilmeniz için önce bir <abbr title="Birim olarak yönetebileceğiniz ilgili Azure kaynakları için mantıksal bir kapsayıcı.">kaynak grubu</abbr>, bir <abbr title="Tüm Azure depolama veri nesnelerinizi içeren bir hesap. Depolama hesabı, depolama verileriniz için benzersiz bir ad alanı sağlar.">depolama hesabı</abbr>ve bir <abbr title="Azure 'da sunucusuz işlevleri barındıran, işlevlerin çalıştırıldığı temel işlem ortamını sağlayan bulut kaynağı.">işlev uygulaması</abbr> Aşağıdaki komutları kullanarak:

1. Şimdiye kadar yapmadıysanız Azure 'da oturum açın:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Linux ve Windows uygulamalarını aynı kaynak grubunda barındıralamazsınız. `AzureFunctionsQuickstart-rg`Bir Windows işlev uygulaması veya Web uygulaması ile adlandırılmış bir kaynak grubunuz varsa, farklı bir kaynak grubu kullanmanız gerekir.
    
1. Kaynak grubunuzda ve bölgenizde genel amaçlı bir Azure depolama hesabı oluşturun:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    `<STORAGE_NAME>`Sizin için uygun bir adla değiştirin ve <abbr title="Ad, tüm Azure müşterilerinin küresel olarak kullandığı tüm depolama hesaplarında benzersiz olmalıdır. Örneğin, contosobizappstorage20 içinde olduğu gibi kişisel veya şirket adı, uygulama adı ve sayısal bir tanımlayıcı birleşimini kullanabilirsiniz.">Azure depolama 'da benzersiz</abbr>. Adlar yalnızca üç ile 24 karakter arasında ve küçük harflerden oluşmalıdır. `Standard_LRS`[işlevleri tarafından desteklenen](storage-considerations.md#storage-account-requirements), genel amaçlı bir hesabı belirtir.


1. Azure 'da işlev uygulaması oluşturun.
**Değiştir** Önceki adımda adı ' <STORAGE_NAME> * *.
**Değiştir** ' <APP_NAME> ' genel olarak benzersiz bir ada sahip.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    `<STORAGE_NAME>`Önceki adımda kullandığınız hesabın adıyla değiştirin.

    `<APP_NAME>`İle Değiştir <abbr title="Tüm Azure müşterileri genelinde küresel olarak benzersiz olması gereken bir ad. Örneğin, contoso-bizapp-Func-20 ' de olduğu gibi kişisel veya kuruluş adı, uygulama adı ve sayısal bir tanımlayıcı birleşimini kullanabilirsiniz.">benzersiz ad</abbr>. `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. 

    <br/>
    <details>
    <summary><strong>Azure 'da sağlanan kaynakların maliyeti nedir?</strong></summary>

    Bu komut, burada yaptığınız kullanım miktarı için ücretsiz olan [Azure Işlevleri tüketim planı](consumption-plan.md)altında, belirtilen dil çalışma zamanında çalışan bir işlev uygulaması oluşturur. Bu komut Ayrıca, aynı kaynak grubunda, işlev uygulamanızı izleyebilmeniz ve günlükleri görüntüleyebileceğiniz ilişkili bir Azure Application Insights örneğini sağlar. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. işlev projesini Azure 'a dağıtma


**Kopyala** ' Func Azure funtionapp Publish <APP_NAME>  `<APP_NAME>` , uygulamanızın adıyla değiştirin.
**Çalıştır**

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

Komutun çıktısında gösterilen tüm **çağırma URL 'sini** `publish` bir tarayıcı adres çubuğuna kopyalayın. **&Name = Functions** sorgu parametresini **ekleyin** . 

![Azure üzerinde çalışan işlevin çıktısı bir tarayıcıda](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Kaynakları Temizleme

Bir [sonraki adımla](#next-steps) devam ederseniz ve bir Azure depolama kuyruğu çıkışı eklerseniz <abbr title="Bir işlev ve diğer kaynaklar arasında bildirime dayalı bağlantı. Giriş bağlama işlevine veri sağlar; çıkış bağlama, işlevden diğer kaynaklara veri sağlar.">bağlama</abbr>, daha önce yapmış olduğunuz yerde oluşturacağınız sürece tüm kaynaklarınızı saklayın.

Aksi takdirde, daha fazla maliyet ödemeden kaçınmak için kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
