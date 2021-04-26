---
title: Linux üzerinde özel görüntü kullanarak Azure Işlevleri oluşturma
description: Özel bir Linux görüntüsü üzerinde çalışan Azure İşlevleri oluşturmayı öğrenin.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 7950bfb4a57db812da87f4e5f76f3075d50a8293
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782286"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Linux üzerinde özel kapsayıcı kullanarak bir işlev oluşturma

Bu öğreticide, bir Linux temel görüntüsü kullanarak kodunuzu oluşturup Azure Işlevlerine özel bir Docker kapsayıcısı olarak dağıtırsınız. İşlevleriniz belirli bir dil sürümü gerektirdiğinde veya yerleşik görüntü tarafından sağlanmayan belirli bir bağımlılığı ya da yapılandırmaya sahip olduğunda genellikle özel bir görüntü kullanırsınız.

::: zone pivot="programming-language-other"
Azure Işlevleri, [özel işleyiciler](functions-custom-handlers.md)kullanarak tüm dilleri veya çalışma zamanını destekler. Bu öğreticide kullanılan R programlama dili gibi bazı dillerde, çalışma zamanını veya ek kitaplıkları özel bir kapsayıcının kullanılmasını gerektiren bağımlılıklar olarak yüklemeniz gerekir.
::: zone-end

İşlev kodunuzu özel bir Linux kapsayıcısında dağıtmak [Premium plan](functions-premium-plan.md) veya [adanmış (App Service) bir plan](dedicated-plan.md) barındırmayı gerektirir. Bu öğreticiyi tamamlamak, Azure hesabınızda birkaç ABD Doları ücretlerinden oluşur ve bu işlem tamamlandığında [kaynakları temizleyerek](#clean-up-resources) en aza indirmenize neden olur.

[Linux üzerinde barındırılan ilk işlevinizi oluşturma](./create-first-function-cli-csharp.md?pivots=programming-language-python)konusunda açıklandığı gibi varsayılan bir Azure App Service kapsayıcısını de kullanabilirsiniz. Azure Işlevleri için desteklenen temel görüntüler, [Azure işlevleri temel görüntüler](https://hub.docker.com/_/microsoft-azure-functions-base)deposunda bulunur.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Azure Functions Core Tools kullanarak bir işlev uygulaması ve Dockerfile oluşturun.
> * Docker kullanarak özel bir görüntü oluşturun.
> * Özel görüntüyü bir kapsayıcı kayıt defterinde yayımlayın.
> * İşlev uygulaması için Azure 'da destekleyici kaynaklar oluşturma
> * Docker Hub’dan bir işlev uygulaması dağıtın.
> * Uygulama ayarlarını işlevi uygulamasına ekleyin.
> * Sürekli dağıtımı etkinleştirin.
> * Kapsayıcıya SSH bağlantılarını etkinleştirin.
> * Kuyruk depolama çıkış bağlaması ekleyin. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Azure Functions Core Tools kullanarak bir işlev uygulaması ve Dockerfile oluşturun.
> * Docker kullanarak özel bir görüntü oluşturun.
> * Özel görüntüyü bir kapsayıcı kayıt defterinde yayımlayın.
> * İşlev uygulaması için Azure 'da destekleyici kaynaklar oluşturma
> * Docker Hub’dan bir işlev uygulaması dağıtın.
> * Uygulama ayarlarını işlevi uygulamasına ekleyin.
> * Sürekli dağıtımı etkinleştirin.
> * Kapsayıcıya SSH bağlantılarını etkinleştirin.
::: zone-end

Bu öğreticiyi Windows, macOS veya Linux çalıştıran herhangi bir bilgisayarda takip edebilirsiniz. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Bir [Docker kimliği](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ `docker login`Docker 'da oturum açmak için ' i çalıştırın. Docker çalışmıyorsa bu komut başarısız olur ve bu durumda Docker başlatılır ve komutu yeniden deneyin.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Yerel işlevler projesi oluşturma ve test etme

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Bir Terminal veya komut isteminde, adlı bir klasörde işlev uygulaması projesi oluşturmak için seçtiğiniz dil için aşağıdaki komutu çalıştırın `LocalFunctionsProject` .  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
İşlevler projesini bir [Maven arketipinden](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) oluşturmak için boş bir klasörde aşağıdaki komutu çalıştırın.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion`Parametresi, işlevleri çalışma zamanına hangi Java sürümünün kullanılacağını söyler. İşlevlerinizin Java 11 üzerinde çalışmasını istiyorsanız `-DjavaVersion=11` ifadesini kullanın. Belirtmezseniz `-DjavaVersion` Maven varsayılan olarak Java 8 ' dir. Daha fazla bilgi için bkz. [Java sürümleri](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> `JAVA_HOME`Bu makaleyi tamamlayabilmeniz için ortam değişkeni, JDK 'nin doğru sürümünün yükleneceği konum olarak ayarlanmalıdır.

Maven, dağıtımda projenin oluşturulmasını tamamlaması için gereken değerleri ister.   
İstendiğinde aşağıdaki değerleri sağlayın:

| İstem | Değer | Açıklama |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Java için [paket adlandırma kurallarını](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) takip eden tüm projelerde projenizi benzersiz bir şekilde tanımlayan bir değer. |
| **ArtifactId** | `fabrikam-functions` | Bir sürüm numarası olmadan jar adı olan bir değer. |
| **Sürüm** | `1.0-SNAPSHOT` | Varsayılan değeri seçin. |
| **leyebilir** | `com.fabrikam.functions` | Oluşturulan işlev kodu için Java paketi olan bir değer. Varsayılan değeri kullanın. |

`Y`Onaylamak için yazın veya ENTER tuşuna basın.

Maven, proje dosyalarını, bu örnekte olduğu gibi, _ArtifactId_ adında yeni bir klasörde oluşturur `fabrikam-functions` . 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

`--docker`Seçeneği, `Dockerfile` Azure işlevleri ve seçilen çalışma zamanı ile kullanılmak üzere uygun bir özel kapsayıcıyı tanımlayan proje için bir oluşturur.

Proje klasörüne gidin:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Aşağıdaki komutu kullanarak projenize bir işlev ekleyin; burada `--name` bağımsız değişken işlevinizin benzersiz adıdır ve `--template` bağımsız değişken işlevin tetikleyicisini belirtir. `func new` Projenin seçtiği dile uygun bir kod dosyası ve *üzerindefunction.js* adlı bir yapılandırma dosyası içeren işlev adıyla eşleşen bir alt klasör oluşturur.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Aşağıdaki komutu kullanarak projenize bir işlev ekleyin; burada `--name` bağımsız değişken işlevinizin benzersiz adıdır ve `--template` bağımsız değişken işlevin tetikleyicisini belirtir. `func new`*üzerindefunction.js* adlı bir yapılandırma dosyası içeren işlev adıyla eşleşen bir alt klasör oluşturur.

```console
func new --name HttpExample --template "HTTP trigger"
```

Bir metin düzenleyicisinde, Handler adlı proje klasöründe bir dosya oluşturun *. R*. İçeriği olarak aşağıdakileri ekleyin.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

*host.jsüzerinde*, `customHandler` özel işleyicinin başlangıç komutunu yapılandırmak için bölümünü değiştirin.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

İşlevi yerel olarak test etmek için, proje klasörünün kökündeki yerel Azure Işlevleri çalışma zamanı konağını başlatın: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

`HttpExample`Uç noktanın çıktıda göründüğünü gördüğünüzde öğesine gidin `http://localhost:7071/api/HttpExample?name=Functions` . Tarayıcı `Functions` , sorgu parametresine sağlanan değeri gösteren bir "Merhaba" iletisi görüntülemelidir `name` .

 - Konağı durdurmak için CTRL **C** 'yi kullanın.

## <a name="build-the-container-image-and-test-locally"></a>Kapsayıcı görüntüsünü oluşturma ve yerel olarak test etme

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
Seçim Proje klasörünün kökündeki *Dockerfile dosyasını* inceleyin. Dockerfile, Linux üzerinde işlev uygulamasını çalıştırmak için gerekli ortamı açıklar.  Azure Işlevleri için desteklenen temel görüntülerin tüm listesi, [Azure işlevleri temel görüntü sayfasında](https://hub.docker.com/_/microsoft-azure-functions-base)bulunabilir.
::: zone-end

::: zone pivot="programming-language-other"
Proje klasörünün kökündeki *Dockerfile dosyasını* inceleyin. Dockerfile, Linux üzerinde işlev uygulamasını çalıştırmak için gerekli ortamı açıklar. Özel işleyici uygulamaları, `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` görüntüsünü temel olarak kullanır.

*Dockerfile* 'ı R 'yi yükleyecek şekilde değiştirin. *dockerfile* içeriğini aşağıdakiler ile değiştirin.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Kök proje klasöründe [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) komutunu çalıştırın ve bir ad, `azurefunctionsimage` , ve etiketi belirtin `v1.0.0` . `<DOCKER_ID>` değerini Docker Hub hesabınızın kimliğiyle değiştirin. Bu komut, kapsayıcı için Docker görüntüsünü derler.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Komut tamamlandığında, yeni kapsayıcıyı yerel olarak çalıştırabilirsiniz.
    
Derlemeyi test etmek için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanarak görüntüyü yerel bir kapsayıcıda çalıştırın, `<DOCKER_ID` Docker Kimliğiniz ile yeniden değiştirin ve bağlantı noktası bağımsız değişkenini ekleyin `-p 8080:80` :

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Görüntü yerel kapsayıcıda çalışmaya başladıktan sonra, bir tarayıcı açın `http://localhost:8080` ve aşağıda gösterilen yer tutucu görüntüsünü görüntülemesi gerekir. Bu noktada, işleviniz Azure 'da olduğu gibi yerel kapsayıcıda çalıştığı ve bu, özelliği ile birlikte *function.js* tanımlı bir erişim anahtarı tarafından korunduğu için görüntü bu noktada görünür `"authLevel": "function"` . Kapsayıcı henüz Azure 'da bir işlev uygulamasına yayımlanmadı, bu nedenle anahtar henüz kullanılamıyor. Yerel kapsayıcıya karşı test etmek istiyorsanız Docker 'ı durdurun, yetkilendirme özelliğini olarak değiştirin `"authLevel": "anonymous"` , görüntüyü yeniden derleyin ve Docker 'ı yeniden başlatın. Sonra `"authLevel": "function"` *function.jsüzerinde* sıfırlayın. Daha fazla bilgi için bkz. [Yetkilendirme anahtarları](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Kapsayıcının yerel olarak çalıştığını gösteren yer tutucu resim](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Görüntü yerel kapsayıcıda çalışmaya başladıktan sonra, `http://localhost:8080/api/HttpExample?name=Functions` daha önce olduğu gibi aynı "Merhaba" iletisini görüntülemesi gereken öğesine gidin. Maven arşiv ETYPE, anonim yetkilendirme kullanan bir HTTP ile tetiklenen bir işlev oluşturduğundan, kapsayıcıda çalışıyor olsa bile işlevi çağırabilirsiniz. 
::: zone-end  

Kapsayıcıda işlev uygulamasını doğruladıktan sonra, **CTRL** C ile Docker 'ı durdurun + .

## <a name="push-the-image-to-docker-hub"></a>Görüntüyü Docker Hub 'a gönderme

Docker Hub, görüntüleri barındıran ve görüntü ve kapsayıcı hizmetleri sağlayan bir kapsayıcı kayıt defteridir. Azure 'a dağıtma da dahil olmak üzere görüntünüzü paylaşmak için bir kayıt defterine göndermeniz gerekir.

1. Docker 'da henüz oturum açmadıysanız [, DOCKER](https://docs.docker.com/engine/reference/commandline/login/) ID ile değiştirin `<docker_id>` . Bu komut sizden Kullanıcı adı ve parola girmenizi ister. "Oturum açma başarılı oldu" iletisi oturum açtığınızdan emin olur.

    ```console
    docker login
    ```
    
1. Oturum açtıktan sonra Docker [Push](https://docs.docker.com/engine/reference/commandline/push/) komutunu kullanarak görüntüyü Docker Hub 'a gönderin, yeniden `<docker_id>` Docker Kimliğiniz ile değiştirin.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Ağ hızınıza bağlı olarak, görüntüyü ilk kez göndermek birkaç dakika sürebilir (sonraki değişiklikler çok daha hızlıdır). Beklerken, bir sonraki bölüme devam edebilir ve Azure kaynaklarını başka bir terminalde oluşturabilirsiniz.

## <a name="create-supporting-azure-resources-for-your-function"></a>İşleviniz için destekleyici Azure kaynakları oluşturma

İşlev kodunuzu Azure 'a dağıtmak için üç kaynak oluşturmanız gerekir:

- İlgili kaynaklar için mantıksal kapsayıcı olan bir kaynak grubu.
- Projelerinizle ilgili durumu ve diğer bilgileri tutan bir Azure depolama hesabı.
- İşlev kodunuzu yürütmek için ortam sağlayan bir işlev uygulaması. Bir işlev uygulaması yerel işlev projenize eşlenir ve kaynakların daha kolay yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanızı sağlar.

Bu öğeleri oluşturmak için Azure CLı komutlarını kullanırsınız. Her komut, tamamlandıktan sonra JSON çıktısı sağlar.

1. [Az Login](/cli/azure/reference-index#az_login) komutuyla Azure 'da oturum açın:

    ```azurecli
    az login
    ```
    
1. [az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, bölgesinde adlı bir kaynak grubu oluşturur `AzureFunctionsContainers-rg` `westeurope` . (Bir bölgede kullanılabilir bir bölge kullanarak, genellikle kaynak grubunuzu ve kaynaklarınızı size yakın bir bölgede oluşturursunuz `az account list-locations` .)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Linux ve Windows uygulamalarını aynı kaynak grubunda barındıralamazsınız. `AzureFunctionsContainers-rg`Bir Windows işlev uygulaması veya Web uygulaması ile adlandırılmış bir kaynak grubunuz varsa, farklı bir kaynak grubu kullanmanız gerekir.
    
1. [Az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutunu kullanarak kaynak grubunuzda ve bölgenizde genel amaçlı bir depolama hesabı oluşturun. Aşağıdaki örnekte, öğesini `<storage_name>` sizin için uygun olan bir genel benzersiz adla değiştirin. Adlar yalnızca üç ile 24 karakter arasında ve küçük harflerden oluşmalıdır. `Standard_LRS` tipik bir genel amaçlı hesabı belirtir.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Depolama hesabı, bu öğretici için yalnızca birkaç USD ilay.
    
1. Bu komutu kullanarak, `myPremiumPlan` **elastik Premium 1** fiyatlandırma katmanında ( `--sku EP1` ), Batı Avrupa bölgesinde ( `-location westeurope` veya size yakın uygun bir bölge kullanın) ve bir Linux kapsayıcısında () adlı Azure işlevleri için Premium bir plan oluşturun `--is-linux` .

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Burada, gerektiğinde ölçeklenebilen Premium planı kullanıyoruz. Barındırma hakkında daha fazla bilgi edinmek için, bkz. [Azure İşlevleri barındırma planları karşılaştırması](functions-scale.md). Maliyetleri hesaplamak için bkz. [işlevler fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/functions/).

    Bu komut Ayrıca, aynı kaynak grubunda, işlev uygulamanızı izleyebilmeniz ve günlükleri görüntüleyebileceğiniz ilişkili bir Azure Application Insights örneğini sağlar. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Azure 'da görüntüyle bir işlev uygulaması oluşturma ve yapılandırma

Azure 'daki bir işlev uygulaması, barındırma planınızdaki işlevlerinizin yürütülmesini yönetir. Bu bölümde, Docker Hub 'daki görüntüden bir işlev uygulaması oluşturmak ve bunu Azure depolama 'ya bir bağlantı dizesiyle yapılandırmak için önceki bölümde yer alan Azure kaynaklarını kullanırsınız.

1. [Az functionapp Create](/cli/azure/functionapp#az_functionapp_create) komutunu kullanarak işlevler uygulamasını oluşturun. Aşağıdaki örnekte, değerini `<storage_name>` depolama hesabı için önceki bölümde kullandığınız adla değiştirin. Ayrıca `<app_name>` , sizin için uygun bir genel benzersiz adla ve `<docker_id>` DOCKER Kimliğiniz ile değiştirin.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    *Dağıtım-kapsayıcı-görüntü-adı* parametresi, işlev uygulaması için kullanılacak resmi belirtir. Dağıtım için kullanılan görüntü hakkındaki bilgileri görüntülemek için [az functionapp config Container Show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) komutunu kullanabilirsiniz. Farklı bir görüntüden dağıtmak için [az functionapp config Container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) komutunu da kullanabilirsiniz.

1. [Az Storage Account Show-Connection-String](/cli/azure/storage/account) komutunu kullanarak oluşturduğunuz depolama hesabı için bağlantı dizesini görüntüleyin. `<storage-name>`Yukarıda oluşturduğunuz depolama hesabının adıyla değiştirin:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Bu ayarı, [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) komutunu kullanarak işlev uygulamasına ekleyin. Aşağıdaki komutta, öğesini `<app_name>` işlev uygulamanızın adıyla değiştirin ve `<connection_string>` önceki adımdaki bağlantı dizesiyle değiştirin ("DefaultEndpointProtocol =" ile başlayan uzun kodlanmış bir dize):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > Bash 'de, pano kullanmak yerine bağlantı dizesini yakalamak için bir Shell değişkeni kullanabilirsiniz. İlk olarak, bağlantı dizesiyle bir değişken oluşturmak için aşağıdaki komutu kullanın:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Ardından ikinci komutta değişkenine başvurun:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. Bu işlev artık depolama hesabına erişmek için bu bağlantı dizesini kullanabilir.

> [!NOTE]    
> Özel görüntünüzü özel bir kapsayıcı hesabına yayımlarsanız, bunun yerine bağlantı dizesinin Dockerfile içindeki ortam değişkenlerini kullanmanız gerekir. Daha fazla bilgi için bkz. [env yönergesi](https://docs.docker.com/engine/reference/builder/#env). Ayrıca, ve değişkenlerini de ayarlamanız `DOCKER_REGISTRY_SERVER_USERNAME` gerekir `DOCKER_REGISTRY_SERVER_PASSWORD` . Değerleri kullanmak için, görüntüyü yeniden oluşturmanız, görüntüyü kayıt defterine göndermeniz ve sonra işlev uygulamasını Azure 'da yeniden başlatmanız gerekir.

## <a name="verify-your-functions-on-azure"></a>Azure 'da işlevlerinizi doğrulama

Azure 'da işlev uygulamasına dağıtılan görüntü ile, artık işlevi HTTP istekleri aracılığıyla çalıştırabilirsiniz. Tanımda *function.js* özelliği içerdiğinden `"authLevel": "function"` , önce erişim anahtarını edinmeniz ("işlev anahtarı" olarak da bilinir) ve bunu uç noktaya Istek üzerine bir URL parametresi olarak eklemeniz gerekir.

1. Azure portal kullanarak veya komutuyla Azure CLı kullanarak işlev URL 'sini erişim (işlev) anahtarıyla alın `az rest` .)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Azure portal oturum açın, **işlev uygulaması** arayıp seçin.

    1. Doğrulamak istediğiniz işlevi seçin.

    1. Sol gezinti panelinde **işlevler**' i seçin ve ardından doğrulamak istediğiniz işlevi seçin.

        ![Azure portal işlevinizi seçin](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. **Işlev URL 'Sini al**' ı seçin.

        ![Azure portal işlev URL 'sini alın](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Açılır pencerede, **varsayılan (işlev anahtarı)** seçeneğini belirleyin ve ardından URL 'yi panoya kopyalayın. Anahtar, aşağıdaki karakter dizesidir `?code=` .

        ![Varsayılan işlev erişim anahtarını seçin](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > İşlev uygulamanız bir kapsayıcı olarak dağıtıldığından, portalda işlev kodunuzda değişiklik yapamazsınız. Bunun yerine yerel görüntüde projeyi güncelleştirmeniz, görüntüyü kayıt defterine yeniden göndermeniz ve sonra Azure 'a yeniden dağıtmanız gerekir. Sonraki bölümde sürekli dağıtım ayarlayabilirsiniz.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Aşağıdaki biçimde bir URL dizesi oluşturun,, ve yerine `<subscription_id>` `<resource_group>` `<app_name>` Azure abonelik kimliğinizi, işlev uygulamanızın kaynak grubunu ve işlev uygulamanızın adını sırasıyla değiştirin:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Örneğin, URL şu adrese benzeyebilir:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Bunun için, URL 'YI bir ortam değişkenine atayıp `az rest` komutta kullanabilirsiniz.
    
    1. Aşağıdaki komutu çalıştırın `az rest` (Azure CLI sürüm 2.0.77 ve üzeri sürümlerde bulunur), `<uri>` tırnak işaretleri dahil olmak üzere son adımdaki URI dizesiyle değiştirin:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Komutun çıktısı, işlev anahtarıdır. Tam işlev URL 'SI,,, `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` `<app_name>` `<function_name>` ve `<key>` değerlerini belirli değerlerinizle değiştirir.
    
        > [!NOTE]
        > Burada alınan anahtar, işlevler uygulamasındaki tüm işlevler için çalışır olan *ana bilgisayar* anahtarıdır; Portal için gösterilen yöntem yalnızca bir işlev için anahtarı alır.

    ---

1. Bu URL 'nin sonuna parametreyi ekleyerek, işlev URL 'sini tarayıcınızın adres çubuğuna yapıştırın `&name=Azure` . "Hello, Azure" gibi metinler tarayıcıda görünmelidir.

    ![Tarayıcıdaki işlev yanıtı.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Yetkilendirmeyi test etmek için, `code=` URL 'den parametreyi kaldırın ve işlevden yanıt bulunmadığını doğrulayın.


## <a name="enable-continuous-deployment-to-azure"></a>Azure 'a sürekli dağıtımı etkinleştir

Kayıt defterinde görüntüyü güncelleştirdiğinizde bir görüntü dağıtımınızı otomatik olarak güncelleştirmek için Azure Işlevleri 'ni etkinleştirebilirsiniz.

1. İşlev uygulamanızın adıyla değiştirerek [az functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) komutunu kullanarak sürekli dağıtımı etkinleştirin `<app_name>` :

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Bu komut sürekli dağıtımı etkinleştirilir ve dağıtım Web kancası URL 'sini döndürür. ( [Az functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url) komutunu kullanarak bu URL 'yi daha sonra da alabilirsiniz.)

1. Dağıtım Web kancası URL 'sini panoya kopyalayın.

1. [Docker Hub 'ını](https://hub.docker.com/)açın, oturum açın ve gezinti çubuğunda **depolar** ' ı seçin. Görüntü bulun ve seçin, **Web kancaları** sekmesini seçin, **Web kancası adı** belirtin, URL 'nizi **Web kancası URL 'sine** yapıştırın ve sonra **Oluştur**' u seçin:

    ![Web kancasını DockerHub depoya ekleme](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Web kancası ayarlandığında, Azure Işlevleri, Docker Hub 'ında her güncelleştirdiğinizde görüntünüzü yeniden dağıtır.

## <a name="enable-ssh-connections"></a>SSH bağlantılarını etkinleştir

SSH, kapsayıcı ile istemci arasında güvenli iletişime olanak tanır. SSH etkinken, App Service gelişmiş araçlar (kudu) kullanarak kapsayıcınıza bağlanabilirsiniz. Azure Işlevleri, SSH kullanarak kapsayıcınıza bağlanmayı kolaylaştırmak için SSH 'nin zaten etkinleştirilmiş olduğu bir temel görüntü sağlar. Yalnızca Dockerfile dosyanızı düzenlemeniz, ardından görüntüyü yeniden oluşturup yeniden dağıtmanız gerekir. Daha sonra, gelişmiş araçlar (kudu) aracılığıyla kapsayıcıya bağlanabilirsiniz

1. Dockerfile dosyanızda, `-appservice` yönergedeki taban görüntüye dizeyi ekleyin `FROM` :

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. `docker build`Komutu tekrar kullanarak, `<docker_id>` DOCKER Kimliğiniz ile değiştirerek görüntüyü yeniden oluşturun:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Güncelleştirilmiş görüntüyü Docker Hub 'a gönderin. Bu, ilk gönderiden yalnızca görüntünün güncelleştirilmiş segmentlerinin karşıya yüklenmesi gereken zamandan çok daha az zaman alır.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Işlevleri otomatik olarak görüntüyü işlevler uygulamanıza yeniden dağıtır; işlem bir dakikadan kısa bir sürede gerçekleşir.

1. Bir tarayıcıda, öğesini açın `https://<app_name>.scm.azurewebsites.net/` , `<app_name>` benzersiz adınızla değiştirin. Bu URL, işlev uygulaması Kapsayıcınız için gelişmiş araçlar (kudu) uç noktasıdır.

1. Azure hesabınızda oturum açın ve kapsayıcıya bir bağlantı kurmak için **SSH** 'yi seçin. Azure 'un kapsayıcı görüntüsünü güncelleştirmesi devam ediyorsa, bağlanma işlemi birkaç dakika sürebilir.

1. Kapsayıcınıza bir bağlantı kurulduktan sonra, `top` çalışmakta olan işlemi görüntülemek için komutunu çalıştırın. 

    ![SSH oturumunda çalışan Linux üst komutu](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Azure depolama kuyruğuna yazma

Azure Işlevleri, kendi tümleştirme kodunuzu yazmak zorunda kalmadan işlevlerinizi diğer Azure hizmetleri ve kaynaklarına bağlamanıza olanak tanır. Hem giriş hem de çıktıyı temsil eden bu *bağlamalar*, işlev tanımı içinde bildirilmiştir. Bağlamalardan alınan veriler işleve parametre olarak sağlanır. *Tetikleyici* özel bir giriş bağlama türüdür. Bir işlevde yalnızca bir tetikleyici olsa da, birden çok giriş ve çıkış bağlaması olabilir. Daha fazla bilgi için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

Bu bölümde, işlevinizi bir Azure depolama kuyruğu ile tümleştirme işlemi gösterilmektedir. Bu işleve eklediğiniz çıkış bağlaması, verileri bir HTTP isteğinden kuyruktaki bir iletiye yazar.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Çıkış bağlamayı kullanmak için kod ekleme

Sıra bağlaması tanımlı ile, artık işlevinizi, `msg` Çıkış parametresini alacak ve kuyruğa ileti yazacak şekilde güncelleştirebilirsiniz.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Kayıt defterindeki görüntüyü güncelleştirme

1. Kök klasörde, `docker build` yeniden çalıştırın ve bu kez etiketteki sürümü olarak güncelleştirin `v1.0.1` . Daha önce olduğu gibi, `<docker_id>` Docker Hub HESABı Kimliğinizle değiştirin:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Güncelleştirilmiş görüntüyü şu ile depoya geri gönderin `docker push` :

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Sürekli teslimi yapılandırdığınız için, kayıt defterindeki görüntüyü güncelleştirme işlemi otomatik olarak Azure 'daki işlev uygulamanızı güncelleştirir.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure depolama sırasındaki iletiyi görüntüleme

Bir tarayıcıda, işlevinizi çağırmak için aynı URL 'YI kullanın. İşlev kodunun bu bölümünü değiştirmediğiniz için tarayıcı, önceki ile aynı yanıtı görüntülemelidir. Ancak eklenen kod, `name` depolama kuyruğuna URL parametresi kullanılarak bir ileti yazdı `outqueue` .

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz kaynakları kullanarak Azure Işleviyle çalışmaya devam etmek istiyorsanız, tüm bu kaynakları yerinde bırakabilirsiniz. Azure Işlevleri için Premium bir plan oluşturduğunuza göre, sürekli maliyetlerde günlük bir veya iki USD olacaktır.

Devam eden maliyetlerin önüne geçmek için, `AzureFunctionsContainer-rg` Bu gruptaki tüm kaynakları temizlemek üzere kaynak grubunu silin: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Sonraki adımlar

+ [İzleme işlevleri](functions-monitoring.md)
+ [Ölçek ve barındırma seçenekleri](functions-scale.md)
+ [Kubernetes tabanlı sunucusuz barındırma](functions-kubernetes-keda.md)
