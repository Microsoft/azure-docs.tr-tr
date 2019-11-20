---
title: Linux üzerinde özel görüntü kullanarak Azure Işlevleri oluşturma
description: Özel bir Linux görüntüsü üzerinde çalışan Azure İşlevleri oluşturmayı öğrenin.
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: b8d82868788d831d4db68a35c032d3f81b545417
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082841"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Linux üzerinde özel görüntü kullanarak bir işlev oluşturma

Azure İşlevleri, işlevlerinizi Linux’ta kendi özel kapsayıcınızda barındırmanıza olanak sağlar. Ayrıca, [varsayılan bir Azure App Service kapsayıcısı üzerinde barındırabilirsiniz](functions-create-first-azure-function-azure-cli-linux.md). Bu işlevsellik [2. x çalışma zamanı işlevlerini](functions-versions.md)gerektirir.

Bu öğreticide işlevlerinizi Azure'a özel bir Docker görüntüsü olarak dağıtmayı öğreneceksiniz. Bu model, yerleşik kapsayıcı görüntüsünü özelleştirmeniz gerektiğinde faydalıdır. İşlevleriniz belirli bir dil sürümüne gereksinim duyduğunda veya yerleşik görüntüde sağlanmayan belirli bir bağımlılık ya da yapılandırma gerektirdiğinde özel görüntü kullanmak isteyebilirsiniz. Azure Işlevleri için desteklenen temel görüntüler, [Azure işlevleri temel görüntüler](https://hub.docker.com/_/microsoft-azure-functions-base)deposunda bulunur. 

Bu öğretici, özel bir Linux görüntüsünde bir işlev oluşturmak için Azure İşlevleri Çekirdek Araçları'nı kullanma adımlarını göstermektedir. Bu görüntüyü Azure CLI kullanılarak oluşturulan Azure'daki bir işlev uygulamasında yayımlayabilirsiniz. Daha sonra, Azure kuyruk depolamaya bağlanmak için işlevinizi güncelleşolursunuz. Öğesini de etkinleştirmeniz gerekir.  

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Çekirdek araçları ile bir işlev uygulaması ve Dockerfile oluşturun.
> * Docker kullanarak özel bir görüntü oluşturun.
> * Özel görüntüyü bir kapsayıcı kayıt defterinde yayımlayın.
> * Bir Azure Depolama hesabı oluşturun.
> * Premium barındırma planı oluşturun.
> * Docker Hub’dan bir işlev uygulaması dağıtın.
> * Uygulama ayarlarını işlevi uygulamasına ekleyin.
> * Sürekli dağıtımı etkinleştirin.
> * Kapsayıcıya SSH bağlantılarını etkinleştirin.
> * Kuyruk depolama çıkış bağlaması ekleyin. 
> * Application Insights izleme ekleyin.

Aşağıdaki adımlar Mac, Windows veya Linux bilgisayarlarda desteklenir. 

## <a name="prerequisites"></a>Önkoşullar

Bu örneği çalıştırmadan önce aşağıdakilere sahip olmanız gerekir:

* [Azure Core Tools sürüm 2.x](functions-run-local.md#v2) yükleme.

* [Azure CLI]( /cli/azure/install-azure-cli)’yi yükleyin. Bu makale, Azure CLI 2.0 veya sonraki bir sürümü gerektirir. Kullandığınız sürümü bulmak için `az --version` komutunu çalıştırın.  
[Azure Cloud Shell](https://shell.azure.com/bash)’i de kullanabilirsiniz.

* Etkin bir Azure aboneliği.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Yerel projeyi oluşturma

Geçerli yerel dizinin `MyFunctionProj` klasöründe bir işlev uygulaması projesi oluşturmak için komut satırından aşağıdaki komutu çalıştırın. Bir Python projesi için [sanal ortamda çalıştırıyor olmanız gerekir](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

`--docker` seçeneğini dahil ettiğinizde proje için bir Docker dosyası oluşturulur. Bu dosya, içinde projenin çalıştırılacağı özel bir kapsayıcı oluşturmak için kullanılır. Kullanılan temel görüntü, seçilen alt çalışma zamanı diline bağlıdır.  

İstendiğinde, şu dillerden bir alt çalışma zamanı seçin:

* `dotnet`: bir .NET Core sınıf kitaplığı projesi (. csproj) oluşturur.
* `node`: bir JavaScript projesi oluşturur.
* `python`: bir Python projesi oluşturur.  

Yeni `MyFunctionProj` proje klasörüne gitmek için aşağıdaki komutu kullanın.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Docker dosyasından derleme

Projenin kök klasöründeki _Dockerfile_'a bir göz atın. Bu dosya, işlev uygulamasını Linux üzerinde çalıştırmak için gereken ortamı açıklar. Aşağıda JavaScript (Node.js) alt çalışma zamanında bir işlev uygulaması çalıştıran bir kapsayıcı oluşturan bir Dockerfile örneği verilmiştir: 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Azure Işlevleri için desteklenen temel görüntülerin tüm listesi, [Azure işlevleri temel görüntü sayfasında](https://hub.docker.com/_/microsoft-azure-functions-base)bulunabilir.

### <a name="run-the-build-command"></a>`build` komutunu çalıştırın

Kök klasörde [docker derleme](https://docs.docker.com/engine/reference/commandline/build/) komutunu çalıştırın ve bir ad `mydockerimage` ve bir etiket `v1.0.0` sağlayın. `<docker-id>` değerini Docker Hub hesabınızın kimliğiyle değiştirin. Bu komut, kapsayıcı için Docker görüntüsünü derler.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Komut tamamlandığında, yeni kapsayıcıyı yerel olarak çalıştırabilirsiniz.

### <a name="run-the-image-locally"></a>Görüntüyü yerel olarak çalıştırma
Docker görüntüsünü yerel bir kapsayıcıda çalıştırarak derlediğiniz görüntün çalıştığını doğrulayın. [docker run](https://docs.docker.com/engine/reference/commandline/run/) komutunu gönderin ve bu komuta görüntünün adını ve etiketini geçirin. `-p` bağımsız değişkenini kullanarak bağlantı noktasını belirttiğinizden emin olun.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Özel görüntü ile yerel bir Docker kapsayıcısı içinde çalışırken <http://localhost:8080> sayfasına göz atarak uygulama ve kapsayıcının doğru şekilde çalıştığını onaylayın.

![İşlev uygulamasını yerel olarak çalıştırın.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> Bu noktada, belirli HTTP işlevinizi çağırmaya çalıştığınızda bir HTTP 401 hata yanıtı alırsınız. Bunun nedeni, işlevinizin Azure 'da olduğu gibi yerel kapsayıcıda çalıştığı, işlev anahtarının gerekli olduğu anlamına gelir. Kapsayıcı henüz bir işlev uygulamasında yayımlanmadığı için kullanılabilir bir işlev anahtarı yok. Daha sonra, kapsayıcınızı yayımlamak için temel araçları kullandığınızda, işlev tuşlarının size gösterildiğini göreceksiniz. Yerel kapsayıcıda çalışan işlevinizi test etmek istiyorsanız, [Yetkilendirme anahtarını](functions-bindings-http-webhook.md#authorization-keys) `anonymous`olarak değiştirebilirsiniz. 

Kapsayıcıda işlev uygulamasını doğruladıktan sonra, yürütmeyi durdurun. Şimdi, özel görüntüyü Docker Hub hesabınıza gönderebilirsiniz.

## <a name="push-to-docker-hub"></a>Docker Hub 'a gönder

Kayıt defteri, görüntüleri barındıran ve hizmet görüntüsüyle kapsayıcı hizmetleri sağlayan bir uygulamadır. Görüntünüzü paylaşmak için bir kayıt defterine göndermelisiniz. Docker Hub, Docker görüntüleri için kayıt defteridir ve kendi depolarınızı (ortak veya özel) barındırmanıza olanak tanır.

Görüntüyü gönderebilmek için önce [docker login](https://docs.docker.com/engine/reference/commandline/login/) komutunu kullanarak Docker Hub'da oturum açmalısınız. `<docker-id>` yerine hesabınızı kullanın ve konsoldaki bilgi istemine parolanızı yazın. Diğer Docker Hub parola seçenekleri için [docker oturum açma komutu belgelerine](https://docs.docker.com/engine/reference/commandline/login/) bakın.

```bash
docker login --username <docker-id>
```

"Oturum açma başarılı oldu" iletisi, oturum açtığınızı onaylar. Oturum açtıktan sonra [docker push](https://docs.docker.com/engine/reference/commandline/push/) komutunu kullanarak görüntüyü Docker Hub’a gönderin.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Gönderim başarılı olduktan sonra, görüntüyü Azure 'da yeni bir işlev uygulaması için dağıtım kaynağı olarak kullanabilirsiniz.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Premium planı oluşturma

[Adanmış (App Service) planlarında](functions-scale.md#app-service-plan) ve [Premium planlarda](functions-premium-plan.md#features)desteklenen özel işlevler kapsayıcıları için Linux barındırma. Bu öğretici, gerektiğinde ölçeklenebilen bir Premium plan kullanır. Barındırma hakkında daha fazla bilgi edinmek için, bkz. [Azure İşlevleri barındırma planları karşılaştırması](functions-scale.md).

Aşağıdaki örnek, **elastik Premium 1** fiyatlandırma katmanında (`--sku EP1`), Batı ABD bölgesinde (`-location WestUS`) ve bir Linux kapsayıcısında (`--is-linux`) `myPremiumPlan` adlı bir Premium plan oluşturur.

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Görüntüden uygulama oluşturma

İşlev uygulaması, barındırma planınızdaki işlevlerinizin yürütülmesini yönetir. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) komutunu kullanarak bir Docker Hub görüntüsünden işlev uygulaması oluşturun.

Aşağıdaki komutta benzersiz bir işlev uygulamasının adını `<app_name>` yer tutucusunun ve `<storage_name>` depolama hesabı adının yerine ekleyin. `<app_name>`, işlev uygulamasının varsayılan DNS etki alanı olarak kullanılacağı için adın Azure’daki tüm uygulamalarda benzersiz olması gerekir. Daha önce olduğu gibi, `<docker-id>` Docker hesap adınızdır.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

_deployment-container-image-name_ parametresi, işlev uygulamasını oluşturmak üzere kullanmak için Docker Hub üzerinde barındırılan görüntüyü belirtir. Dağıtım için kullanılan görüntü hakkındaki bilgileri görüntülemek için [az functionapp config Container Show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) komutunu kullanın. Farklı bir görüntüden dağıtmak için [az functionapp config Container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) komutunu kullanın.

## <a name="configure-the-function-app"></a>İşlev uygulamasını yapılandırma

İşlevin varsayılan depolama hesabına bağlanması için bağlantı dizesi gerekir. Özel görüntünüzü özel bir kapsayıcı hesabına yayımladığınızda, bu uygulama ayarlarını [env yönergesini](https://docs.docker.com/engine/reference/builder/#env)veya benzer bir şeyi kullanarak Dockerfile içinde ortam değişkenleri olarak ayarlamanız gerekir.

Bu durumda `<storage_name>`, oluşturduğunuz depolama hesabının adıdır. [az storage account show-connection-string](/cli/azure/storage/account) komutu ile bağlantı dizesini alın. [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) komutu ile bu uygulama ayarlarını işlev uygulamasına ekleyin.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Kapsayıcınız özel ise, aşağıdaki uygulama ayarlarını da ayarlamanız gerekir  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Bu değerlerin çekilmesi için işlev uygulamanızı durdurup başlatmanız gerekir

## <a name="verify-your-functions"></a>İşlevlerinizi doğrulayın

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

Oluşturduğunuz HTTP ile tetiklenen işlev, uç nokta çağrılırken bir [işlev anahtarı](functions-bindings-http-webhook.md#authorization-keys) gerektirir. Şu anda, anahtar dahil olmak üzere işlev URL 'nizi almanın en kolay yolu [Azure Portal]. 

> [!TIP]
> Ayrıca, [kimlik doğrulaması için bir taşıyıcı belirteci](/cli/azure/account#az-account-get-access-token)sunmanıza gerek duyan [anahtar yönetim API 'lerini](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)kullanarak işlev anahtarlarınızı elde edebilirsiniz.

Yeni işlev uygulamanızı, sayfanın üst kısmındaki **arama** kutusuna işlev uygulamanızın adını yazarak [Azure Portal] ve **App Service** kaynağını seçerek bulun.

**Myhttptrigger** işlevini seçin **</> Işlev URL 'sini al** > **varsayılan (işlev anahtarı)**  > **Kopyala**' yı seçin.

![Azure portalından işlev URL’sini kopyalama](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

Bu URL 'de, işlev anahtarı `code` sorgu parametresidir. 

> [!NOTE]  
> İşlev uygulamanız bir kapsayıcı olarak dağıtıldığından, portalda işlev kodunuzda değişiklik yapamazsınız. Bunun yerine, projeyi yerel kapsayıcıda güncelleştirmeniz ve Azure 'da yeniden yayımlamanız gerekir.

İşlev URL'sini tarayıcınızın adres çubuğuna yapıştırın. `&name=<yourname>` sorgu dizesi değerini bu URL’nin sonuna ekleyin ve isteği yürütmek için klavyenizdeki `Enter` tuşuna basın. İşlev tarafından döndürülen yanıtın tarayıcıda gösterildiğini görürsünüz.

Aşağıdaki örnekte tarayıcıdaki yanıt gösterilmektedir:

![Tarayıcıdaki işlev yanıtı.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

İstek URL’si, işlevinize HTTP üzerinden erişmek için varsayılan olarak gerekli olan bir anahtar içerir. 

## <a name="enable-continuous-deployment"></a>Sürekli dağıtımı etkinleştir

Kapsayıcıları kullanmanın avantajlarından biri sürekli dağıtım için destek sağlar. İşlevler, Kapsayıcınız kayıt defterinde güncelleştirildiğinde güncelleştirmeleri otomatik olarak dağıtmanızı sağlar. [Az functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) komutuyla sürekli dağıtımı etkinleştirin.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Bu komut, sürekli dağıtım etkinleştirildikten sonra dağıtım Web kancası URL 'sini döndürür. Bu URL 'YI döndürmek için [az functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) komutunu da kullanabilirsiniz. 

Dağıtım URL 'sini kopyalayın ve DockerHub deponuza gidin, **Web kancaları** sekmesini seçin, Web kancası Için bir **Web kancası adı** yazın, URL 'nizi **Web kancası URL 'sine**yapıştırın ve ardından artı işaretini ( **+** ) seçin.

![Web kancasını DockerHub depoya ekleme](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Web kancası kümesi ile DockerHub 'daki bağlantılı görüntüde yapılan tüm güncelleştirmeler, işlev uygulamasının en son görüntüyü indirmesine ve yüklemesine neden olacak.

## <a name="enable-ssh-connections"></a>SSH bağlantılarını etkinleştir

SSH, kapsayıcı ile istemci arasında güvenli iletişime olanak tanır. SSH etkinken, App Service gelişmiş araçlar (kudu) kullanarak kapsayıcınıza bağlanabilirsiniz. SSH kullanarak kapsayıcınıza bağlanmayı kolaylaştırmak için, Işlevleri SSH 'yi zaten etkinleştirilmiş bir temel görüntü sağlar. 

### <a name="change-the-base-image"></a>Temel görüntüyü değiştirme

Dockerfile 'da, bir JavaScript projesi için aşağıdaki gibi görünen `FROM` yönergesindeki `-appservice` dize öğesini temel görüntüye ekleyin.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

İki temel görüntüde bulunan farklılıklar, kapsayıcınıza SSH bağlantılarını etkinleştirir. Bu farklılıklar, [Bu uygulama hizmetleri öğreticisinde](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)ayrıntılı olarak açıklanmıştır.

### <a name="rebuild-and-redeploy-the-image"></a>Görüntüyü yeniden oluşturma ve yeniden dağıtma

Kök klasörde, [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) komutunu yeniden çalıştırın, daha önce olduğu gibi `<docker-id>` Docker Hub hesabı Kimliğinizle değiştirin. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Güncelleştirilmiş görüntüyü Docker Hub 'a geri gönderin.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Güncelleştirilmiş görüntü, işlev uygulamanıza yeniden dağıtılır.

### <a name="connect-to-your-container-in-azure"></a>Azure 'da kapsayıcınıza bağlanma

Tarayıcıda, işlev uygulaması Kapsayıcınız için aşağıdaki gelişmiş araçlar (kudu) `scm.` uç noktasına gidin ve `<app_name>` işlev uygulamanızın adıyla değiştirin.

```
https://<app_name>.scm.azurewebsites.net/
```

Azure hesabınızda oturum açın ve ardından, kapsayıcınıza bir SSH bağlantısı oluşturmak için **SSH** sekmesini seçin.

Bağlantı kurulduktan sonra, çalışmakta olan işlemlerin görüntülemek için `top` komutunu çalıştırın. 

![SSH oturumunda çalışan Linux üst komutu.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Kuyruk depolamaya yaz

İşlevleri, kendi tümleştirme kodunuzu yazmak zorunda kalmadan Azure hizmetlerini ve diğer kaynakları işlevlere bağlamanıza olanak tanır. Hem giriş hem de çıktıyı temsil eden bu *bağlamalar*, işlev tanımı içinde bildirilmiştir. Bağlamalardan alınan veriler işleve parametre olarak sağlanır. *Tetikleyici* özel bir giriş bağlama türüdür. Bir işlevde yalnızca bir tetikleyici olsa da, birden çok giriş ve çıkış bağlaması olabilir. Daha fazla bilgi için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

Bu bölümde, işlevinizi bir Azure depolama kuyruğu ile tümleştirme işlemi gösterilmektedir. Bu işleve eklediğiniz çıkış bağlaması, verileri bir HTTP isteğinden kuyruktaki bir iletiye yazar.

### <a name="download-the-function-app-settings"></a>İşlev uygulaması ayarlarını indirin

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Uzantı paketlerini etkinleştir

Kuyruk depolama çıkış bağlaması kullandığınızdan, projeyi çalıştırmadan önce depolama bağlamaları uzantısının yüklü olması gerekir. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

HTTP ve Zamanlayıcı Tetikleyicileri dışında, bağlamalar uzantı paketleri olarak uygulanır. Depolama uzantısı paketini projenize eklemek için Terminal penceresinde aşağıdaki [DotNet paket Ekle](/dotnet/core/tools/dotnet-add-package) komutunu çalıştırın.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Visual Studio 'yu kullanırken, bu paketi eklemek için NuGet Paket Yöneticisi 'ni de kullanabilirsiniz.

---

Şimdi projenize bir depolama çıkış bağlaması ekleyebilirsiniz.

### <a name="add-an-output-binding"></a>Çıktı bağlaması ekleme

Işlevlerde, her bağlama türü, function. json dosyasında tanımlanması için `direction`, `type`ve benzersiz bir `name` gerektirir. Bu öznitelikleri tanımlama yöntemi, işlev uygulamanızın diline bağlıdır.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Çıkış bağlaması kullanan kod ekleme

Bağlama tanımlandıktan sonra, işlev imzasında bir öznitelik olarak erişmek için bağlamanın `name` kullanabilirsiniz. Bir çıkış bağlaması kullanarak kimlik doğrulaması, kuyruk başvurusu alma veya veri yazma için Azure depolama SDK kodunu kullanmanız gerekmez. Işlevler çalışma zamanı ve kuyruk çıkış bağlaması bu görevleri sizin için işler.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Barındırılan kapsayıcıyı güncelleştirme

Kök klasörde [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) komutunu tekrar çalıştırın ve bu kez etiketteki sürümü `v1.0.2`olarak güncelleştirin. Daha önce olduğu gibi `<docker-id>` Docker Hub hesabı KIMLIĞINIZLE değiştirin. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Güncelleştirilmiş görüntüyü depoya geri gönderin.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Azure 'da güncelleştirmeleri doğrulama

İşlevinizi tetiklemek için tarayıcıdan önceki URL 'YI kullanın. Aynı yanıtı görmeniz gerekir. Ancak, bu kez `name` parametresi olarak geçirdiğiniz dize `outqueue` depolama kuyruğuna yazılır.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Özel kapsayıcınızı Azure 'daki bir işlev uygulamasına başarıyla dağıttığınıza göre, aşağıdaki konularda daha fazla bilgi alabilirsiniz:

+ [İzleme işlevleri](functions-monitoring.md)
+ [Ölçek ve barındırma seçenekleri](functions-scale.md)
+ [Kubernetes tabanlı sunucusuz barındırma](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com
