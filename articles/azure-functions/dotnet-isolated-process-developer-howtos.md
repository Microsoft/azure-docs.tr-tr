---
title: Azure Işlevleri 'ni kullanarak .NET 5 işlevleri geliştirme ve yayımlama
description: .NET 5,0 kullanarak C# işlevleri oluşturup hata ayıklamanın yanı sıra yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için dağıtma hakkında bilgi edinin.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: c76fde9a61ca60171ac094ef541e8c5841846aab
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866281"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Azure Işlevleri 'ni kullanarak .NET 5 işlevleri geliştirme ve yayımlama 

Bu makalede, Azure Işlevleri çalışma zamanındaki işlem dışı çalışan .NET 5,0 kullanarak C# işlevleriyle nasıl çalışacağınızı gösterilmektedir. Oluşturma, yerel olarak hata ayıklama ve bu .NET yalıtılmış işlem işlevlerini Azure 'da yayımlama hakkında bilgi edineceksiniz. Azure 'da bu işlevler, .NET 5,0 'yi destekleyen yalıtılmış bir işlemde çalışır. Daha fazla bilgi edinmek için bkz. [Azure 'da .net 5,0 üzerinde işlev çalıştırmaya yönelik kılavuz](dotnet-isolated-process-guide.md).

.NET 5,0 ' i desteklemeniz veya işlevlerinizi işlem dışı olarak çalıştırmanız gerekmiyorsa, bunun yerine [bir C# sınıf kitaplığı işlevi oluşturmak](functions-create-your-first-function-visual-studio.md)isteyebilirsiniz.

>[!NOTE]
>Azure portal .NET yalıtılmış işlem işlevleri geliştirme şu anda desteklenmemektedir. Azure 'da .NET 5,0 uygulamalarını işlem dışı çalıştırmayı destekleyen bir işlev uygulaması oluşturmak için Azure CLı veya Visual Studio Code yayımlamayı kullanmanız gerekir.   

## <a name="prerequisites"></a>Önkoşullar

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET 5,0 SDK](https://dotnet.microsoft.com/download)

+ Sürüm 3.0.3381 veya sonraki bir sürümü [Azure Functions Core Tools](functions-run-local.md#v2) .

+ [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,20 veya sonraki bir sürümü.  
::: zone pivot="development-environment-vscode"
+ [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms) birinde çalışan [Visual Studio Code](https://code.visualstudio.com/).  

+ Visual Studio Code için [C# uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).  

+ Visual Studio Code, sürüm 1.3.0 veya daha yeni bir sürümü için [Azure işlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .
::: zone-end
::: zone pivot="development-environment-vs"
+ **Azure geliştirme** iş yükü dahil olmak üzere [Visual Studio 2019](https://azure.microsoft.com/downloads/).  
.NET yalıtılmış işlev proje şablonları ve yayımlama Şu anda Visual Studio 'da kullanılamıyor.
::: zone-end

## <a name="create-a-local-function-project"></a>Yerel işlev projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir tetikleyiciye yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, tek bir işlev içeren bir işlev projesi oluşturursunuz.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Şu anda, .NET yalıtılmış işlev projeleri oluşturmayı destekleyen Visual Studio proje şablonları yoktur. Bu makalede, Visual Studio 'da yerel olarak çalıştırabileceğiniz ve hata ayıklamanıza olanak sağlayan C# projenizi oluşturmak için çekirdek araçların nasıl kullanılacağı gösterilmektedir.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Etkinlik çubuğundan Azure simgesini seçtikten sonra **Azure: İşlevler** alanında **Yeni proje oluştur...** simgesini seçin.

    ![Yeni proje oluştur'u seçin](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Projenizin çalışma alanı için bir dizin konumu seçtikten sonra **Seç**'i belirleyin.

    > [!NOTE]
    > Bu adımlar, bir çalışma alanının dışında tamamlanacak şekilde tasarlandı. Bu örnekte, bir çalışma alanının parçası olan bir proje klasörünü seçmeyin.

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **İşlev projeniz için bir dil seçin**: `C#` öğesini seçin.

    + **.NET çalışma zamanı seçin**: seçin `.NET 5 isolated` .

    + **Projenizin ilk işlevi için bir şablon seçin**: `HTTP trigger` öğesini seçin.

    + **Bir işlev adı sağlayın**: `HttpExample` yazın.

    + **Bir ad alanı sağlayın**: `My.Functions` yazın.

    + **Yetkilendirme düzeyi**: `Anonymous` herhangi bir kişinin işlev uç noktanızı çağırmasını sağlayan öğesini seçin. Yetkilendirme düzeyi hakkında bilgi edinmek için bkz. [Yetkilendirme anahtarları](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Projenizi nasıl açmak istediğinizi seçin**: `Add to workspace` öğesini seçin.

1. Bu bilgileri kullanarak, Visual Studio Code HTTP tetikleyicisiyle bir Azure Işlevleri projesi oluşturur. Yerel proje dosyalarını Gezgin içinde görüntüleyebilirsiniz. Oluşturulan dosyalar hakkında daha fazla bilgi edinmek için bkz. [oluşturulan proje dosyaları](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. `func init` *Localfunctionproj* adlı bir klasörde bir işlevler projesi oluşturmak için komutunu aşağıdaki gibi çalıştırın:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Belirtme `dotnetisolated` , .net 5,0 üzerinde çalışan bir proje oluşturur.


1. Proje klasörüne gidin:

    ```console
    cd LocalFunctionProj
    ```

    Bu klasör, yapılandırma dosyalarında [local.settings.js](functions-run-local.md#local-settings-file) ve [host.js](functions-host-json.md) dahil olmak üzere, proje için çeşitli dosyalar içerir. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.

1. Aşağıdaki komutu kullanarak, `--name` bağımsız değişkenin işlevinizin (HttpExample) benzersiz adı olduğu ve `--template` bağımsız değişkeninin işlevin TETIKLEYICISINI (http) belirttiği, projenize bir işlev ekleyin.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` bir HttpExample. cs kod dosyası oluşturur.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Bu noktada, `func start` C# yalıtılmış işlevler projesini derlemek ve çalıştırmak için proje klasörünüzün kökünden komutunu çalıştırabilirsiniz. Şu anda, Visual Studio 'da işlem dışı işlev kodunuzda hata ayıklamak istiyorsanız, çalışan Işlevler çalışma zamanı işlemine aşağıdaki adımları kullanarak el ile bir hata ayıklayıcı eklemeniz gerekir:  

1. Visual Studio 'da proje dosyasını (. csproj) açın. Proje kodunuzu gözden geçirebilir ve değiştirebilir ve istenen kesme noktalarını kodda ayarlayabilirsiniz. 

1. Kök proje klasöründen, çalışma zamanı konağını başlatmak için terminalden veya bir komut isteminden aşağıdaki komutu kullanın:

    ```console
    func start --dotnet-isolated-debug
    ```

    `--dotnet-isolated-debug`Seçeneği, işleme devam etmeden önce bir hata ayıklayıcının iliştirmesine izin vermesini söyler. Çıktının sonuna doğru, aşağıdaki satırlara benzer bir şey görmeniz gerekir: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    , `PID: XXXXXX` Çalışan işlevlerin ana bilgisayarı olan dotnet.exe işleminin Işlem kimliğini (PID) gösterir.
 
1. Azure Işlevleri çalışma zamanı çıktısında, bir hata ayıklayıcı ekleyebileceğiniz ana bilgisayar işleminin işlem KIMLIĞINI bir yere unutmayın. Ayrıca yerel işlevinizin URL 'sini de göz önünde bulabilirsiniz.

1. Visual Studio 'da **Hata Ayıkla** menüsünde, **işleme iliştir...**' i seçin, işlem kimliğiyle eşleşen işlemi bulun ve **Ekle**' yi seçin. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Işlev ana bilgisayar işlemine hata ayıklayıcıyı iliştirme":::    

    Hata ayıklayıcı ekli olarak, işlev kodunuzda hata ayıklama yapabilirsiniz.

1. Tarayıcınızın adres çubuğuna, aşağıdaki gibi görünen yerel işlev URL 'nizi yazın ve isteği çalıştırın. 

    `http://localhost:7071/api/HttpExample`

    Çalışan terminale yazılan istekten izleme çıktısını görmeniz gerekir. Kod yürütme, işlev kodunuzda ayarladığınız tüm kesme noktalarında duraklar.

1. İşiniz bittiğinde, terminale gidin ve ana bilgisayar işlemini durdurmak için CTRL + C tuşlarına basın.
 
İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

> [!NOTE]  
> Visual Studio yayımlama Şu anda .NET yalıtılmış işlem uygulamaları için kullanılamaz. Visual Studio 'da projenizi geliştirmeyi bitirdikten sonra, uzak Azure kaynaklarını oluşturmak için Azure CLı 'yi kullanmanız gerekir. Daha sonra, projenizi Azure 'da yayımlamak için komut satırından Azure Functions Core Tools kullanabilirsiniz.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>İşleviniz için destekleyici Azure kaynakları oluşturma

İşlev kodunuzu Azure 'a dağıtabilmeniz için önce üç kaynak oluşturmanız gerekir:

- İlgili kaynaklar için mantıksal kapsayıcı olan bir [kaynak grubu](../azure-resource-manager/management/overview.md).
- İşlevleriniz hakkında durum ve diğer bilgileri korumak için kullanılan bir [depolama hesabı](../storage/common/storage-account-create.md).
- İşlev kodunuzu yürütmek için ortam sağlayan bir işlev uygulaması. Bir işlev uygulaması yerel işlev projenize eşlenir ve kaynakların daha kolay yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanızı sağlar.

Bu öğeleri oluşturmak için aşağıdaki komutları kullanın.

1. Şimdiye kadar yapmadıysanız Azure 'da oturum açın:

    ```azurecli
    az login
    ```

    [Az Login](/cli/azure/reference-index#az_login) komutu sizi Azure hesabınızda oturum açar.

1. Bölgede adlı bir kaynak grubu oluşturun `AzureFunctionsQuickstart-rg` `westeurope` :

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [Az Group Create](/cli/azure/group#az_group_create) komutu bir kaynak grubu oluşturur. Genellikle kaynak grubunuzu ve kaynaklarınızı, komuttan döndürülen kullanılabilir bir bölgeyi kullanarak size yakın bir bölgede oluşturursunuz `az account list-locations` .

1. Kaynak grubunuzda ve bölgenizde genel amaçlı bir depolama hesabı oluşturun:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [Az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutu depolama hesabı oluşturur. 

    Önceki örnekte, `<STORAGE_NAME>` Azure depolama 'da size uygun ve benzersiz olan bir adla değiştirin. Adlar yalnızca üç ile 24 karakter arasında ve küçük harflerden oluşmalıdır. `Standard_LRS`[işlevleri tarafından desteklenen](storage-considerations.md#storage-account-requirements), genel amaçlı bir hesabı belirtir.

1. Azure 'da işlev uygulaması oluşturma:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    [Az functionapp Create](/cli/azure/functionapp#az_functionapp_create) komutu, Azure 'da işlev uygulaması oluşturur. 
    
    Önceki örnekte, değerini, `<STORAGE_NAME>` önceki adımda kullandığınız hesabın adıyla değiştirin ve öğesini `<APP_NAME>` sizin için uygun olan bir genel benzersiz adla değiştirin. `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. 
    
    Bu komut, [Azure Işlevleri tüketim planı](consumption-plan.md)altında .NET 5,0 çalıştıran bir işlev uygulaması oluşturur. Bu plan, bu makalede yaptığınız kullanım miktarı için ücretsiz olmalıdır. Bu komut ayrıca aynı kaynak grubundaki ilişkili bir Azure Application Insights örneğini sağlar. İşlev uygulamanızı izlemek ve günlükleri görüntülemek için bu örneği kullanın. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Projeyi Azure'da yayımlama

Bu bölümde, Azure aboneliğinizde bir işlev uygulaması ve ilgili kaynaklar oluşturup kodunuzu dağıtırsınız.

> [!IMPORTANT]
> Varolan bir işlev uygulamasına yayımladığınızda Azure’daki uygulamanın içeriğinin üzerine yazılır.


1. Etkinlik çubuğunda Azure simgesini seçin, sonra **Azure: işlevler** alanında, **işlev uygulamasına dağıt uygulama...** düğmesini seçin.

    ![Projenizi Azure'da yayımlama](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. İstemlerde aşağıdaki bilgileri sağlayın:

    - **Klasör seçin**: çalışma alanınızdan bir klasör seçin veya işlev uygulamanızı içeren bir klasöre gidin. Zaten açılmış geçerli bir işlev uygulamanız varsa bu istemi görmezsiniz.

    - **Abonelik seçin**: kullanılacak aboneliği seçin. Yalnızca bir aboneliğiniz olduğunda bu istemi görmezsiniz.

    - **Azure'daki işlev uygulamasını seçin**: `- Create new Function App` öğesini seçin. ( `Advanced` Bu makalede kapsanmayan seçeneğini seçmeyin.)
      
    - **İşlev uygulaması için genel olarak benzersiz bir ad girin**: URL yolunda geçerli olan bir ad yazın. Yazdığınız ad, Azure Işlevlerinde benzersiz olduğundan emin olmak için onaylanır.
    
    - **Çalışma zamanı yığını seçin**: seçin `.NET 5 (non-LTS)` . 
    
    - **Yeni kaynaklar için bir konum seçin**: daha iyi performans için, size yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. 
    
    Bildirim alanında, tek tek kaynakların Azure 'da oluşturulan durumunu görürsünüz.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure kaynak oluşturma bildirimi":::
    
1.  Tamamlandığında, aşağıdaki Azure kaynakları, işlev uygulamanızın adına göre adlar kullanılarak aboneliğinizde oluşturulur:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    İşlev uygulamanız oluşturulduktan sonra bir bildirim görüntülenir ve dağıtım paketi uygulanır. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Oluşturduğunuz Azure kaynakları dahil olmak üzere oluşturma ve dağıtım sonuçlarını görüntülemek için bu bildirimde **çıktıyı görüntüle** ' yi seçin. Bildirimi kaçırırsanız, sağ alt köşedeki zil simgesini seçerek tekrar görüntüleyin.

    ![Tüm bildirimi oluştur](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makaleyi tamamlayacak kaynakları oluşturdunuz. [Hesap durumunuza](https://azure.microsoft.com/account/) ve [hizmet fiyatlandırmanıza](https://azure.microsoft.com/pricing/) bağlı olarak size bu kaynakların ücretleri yansıtılabilir. 

::: zone pivot="development-environment-cli"  
Daha fazla maliyet ödemeden kaçınmak için kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Daha fazla maliyet ödemeden kaçınmak için işlev uygulamasını ve ilgili kaynaklarını silmek için aşağıdaki adımları kullanın.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Daha fazla maliyet ödemeden kaçınmak için işlev uygulamasını ve ilgili kaynaklarını silmek için aşağıdaki adımları kullanın.

1. Cloud Explorer 'da, abonelik > **uygulama hizmetleri**' ni genişletin, işlev uygulamanıza sağ tıklayın ve **portalda aç**' ı seçin. 

1. İşlev uygulaması sayfasında **genel bakış** sekmesini seçin ve **kaynak grubu**' nun altındaki bağlantıyı seçin.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="İşlev uygulaması sayfasından silinecek kaynak grubunu seçin":::

2. **Kaynak grubu** sayfasında, dahil edilen kaynakların listesini gözden geçirin ve silmek istedikleriniz olduklarından emin olun.
 
3. **Kaynak grubunu sil**’i seçin ve yönergeleri izleyin.

   Silme işlemi birkaç dakika sürebilir. İşlem tamamlandığında, birkaç saniye boyunca bir bildirim görüntülenir. Bildirimi görüntülemek için sayfanın üst kısmındaki zil simgesini de seçebilirsiniz.
::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [.NET yalıtılmış işlevleri hakkında daha fazla bilgi edinin](dotnet-isolated-process-guide.md)

