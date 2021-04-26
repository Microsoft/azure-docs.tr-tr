---
title: Azure Functions Core Tools ile çalışma
description: Azure işlevleri 'nde çalıştırmadan önce yerel bilgisayarınızdaki komut isteminden veya terminalden Azure Işlevlerini nasıl kodleyeceğinizi ve test leyeceğinizi öğrenin.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 3ddd84f2f73546b42a3925802b3357df16485488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521450"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools ile çalışma

Azure Functions Core Tools, yerel bilgisayarınızda komut isteminden veya terminalden işlevlerinizi geliştirip test etmenize olanak tanır. Yerel işlevleriniz canlı Azure hizmetlerine bağlanabilir ve tüm Işlevler çalışma zamanını kullanarak işlevlerinizi yerel bilgisayarınızdaki hata ayıklaması yapabilirsiniz. Hatta, Azure aboneliğinize bir işlev uygulaması dağıtabilirsiniz.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Yerel bilgisayarınızda işlevleri geliştirme ve temel araçları kullanarak bunları Azure 'da yayımlama aşağıdaki temel adımları izler:

> [!div class="checklist"]
> * [Çekirdek araçları ve bağımlılıklarını yükler.](#v2)
> * [Dile özgü şablondan bir işlev uygulama projesi oluşturun.](#create-a-local-functions-project)
> * [Tetikleyici ve bağlama uzantılarını kaydedin.](#register-extensions)
> * [Depolama ve diğer bağlantıları tanımlayın.](#local-settings-file)
> * [Bir tetikleyiciden ve dile özgü şablondan bir işlev oluşturun.](#create-func)
> * [İşlevi yerel olarak çalıştırın.](#start)
> * [Projeyi Azure 'da yayımlayın.](#publish)

## <a name="core-tools-versions"></a>Core Tools sürümleri

Azure Functions Core Tools üç sürümü vardır. Kullandığınız sürüm yerel geliştirme ortamınıza, [dil seçimine](supported-languages.md)ve gerekli destek düzeyine bağlıdır:

+ [**Sürüm 3. x/2. x**](#v2): [Azure işlevleri çalışma zamanının sürüm 3. x veya 2. x](functions-versions.md)'i destekler. Bu sürümler [Windows](?tabs=windows#v2), [MacOS](?tabs=macos#v2)ve [Linux](?tabs=linux#v2) 'u destekler ve yükleme için platforma özgü paket yöneticilerini veya NPM 'yi kullanır.

+ **Sürüm 1. x**: Azure işlevleri çalışma zamanının sürüm 1. x ' i destekler. Araçların bu sürümü yalnızca Windows bilgisayarlarda desteklenir ve bir [NPM paketinden](https://www.npmjs.com/package/azure-functions-core-tools)yüklenir.

Belirli bir bilgisayara yalnızca bir temel araçlar sürümü yükleyebilirsiniz. Aksi belirtilmedikçe, bu makaledeki örnekler sürüm 3. x içindir.

## <a name="prerequisites"></a>Önkoşullar

Azure Functions Core Tools Şu anda Azure hesabınızda kimlik doğrulaması için [Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/install-az-ps) bağlı olabilir. Bu, Azure Functions Core Tools 'ten [Azure 'a yayımlayabilmek](#publish) için bu araçlardan birini yüklemelisiniz. 

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools’u Yükleme

[Azure Functions Core Tools] , yerel geliştirme bilgisayarınızda çalıştırabileceğiniz Azure işlevleri çalışma zamanını destekleyen aynı çalışma zamanının bir sürümünü içerir. Ayrıca işlevler oluşturmak, Azure 'a bağlanmak ve işlev projelerini dağıtmak için komutlar sağlar.

### <a name="version-3x-and-2x"></a><a name="v2"></a>Sürüm 3. x ve 2. x

Araçların sürüm 3. x/2. x, .NET Core üzerinde oluşturulan Azure Işlevleri çalışma zamanını kullanır. Bu sürüm, [Windows](?tabs=windows#v2), [MacOS](?tabs=macos#v2)ve [Linux](?tabs=linux#v2)dahil tüm platformlarda .NET Core desteği için desteklenir. 

> [!IMPORTANT]
> [Uzantı paketleri]'ni kullanarak .NET Core SDK yükleme gereksinimini atlayabilirsiniz.

# <a name="windows"></a>[Windows](#tab/windows)

Aşağıdaki adımlarda, v3. x temel araçları yüklemek için bir Windows Installer (MSI) kullanılır. Çekirdek araçları v2. x ' i yüklemek için gereken diğer paket tabanlı yükleyiciler hakkında daha fazla bilgi için bkz. [temel araçlar Benioku](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Windows sürümünüze bağlı olarak temel araçlar yükleyicisini indirip çalıştırın:

    - [v3. x-Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2135274) (önerilir. [Visual Studio Code hata ayıklaması](functions-develop-vs-code.md#debugging-functions-locally) 64 bit gerektirir.)
    - [v3. x-Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2135275)

1. [Uzantı paketleri](functions-bindings-register.md#extension-bundles)kullanmayı planlamıyorsanız, [Windows için .NET Core 3. x SDK 'sını](https://dotnet.microsoft.com/download)yükleyebilirsiniz.

# <a name="macos"></a>[macOS](#tab/macos)

Aşağıdaki adımlarda, macOS 'a çekirdek araçları yüklemek için homebrew kullanılır.

1. Zaten yüklenmemişse [homebrew](https://brew.sh/)'ı yükleme.

1. Temel Araçlar paketini yükler:

    ##### <a name="v3x-recommended"></a>v3. x (önerilir)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. [Uzantı paketleri](functions-bindings-register.md#extension-bundles)kullanmayı planlamıyorsanız, [MacOS için .NET Core 3. x SDK 'sını](https://dotnet.microsoft.com/download)yükleyebilirsiniz.

# <a name="linux"></a>[Linux](#tab/linux)

Aşağıdaki adımlarda, Ubuntu/de, Linux dağıtımına çekirdek araçları yüklemek için [apt](https://wiki.debian.org/Apt) kullanılır. Diğer Linux dağıtımları için bkz. [temel araçlar Benioku dosyası](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Paket bütünlüğünü doğrulamak için Microsoft paket deposu GPG anahtarını yükler:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Bir APT güncelleştirme yapmadan önce APT kaynak listesini ayarlayın.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. `/etc/apt/sources.list.d/dotnetdev.list`Aşağıda listelenen uygun Linux sürümü dizelerinden birine ait dosyayı denetleyin:

    | Linux dağıtımı | Sürüm |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. APT kaynak güncelleştirmesini başlatın:

    ```bash
    sudo apt-get update
    ```

1. Temel Araçlar paketini yükler:

    ##### <a name="v3x-recommended"></a>v3. x (önerilir)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2. x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. [Uzantı paketleri](functions-bindings-register.md#extension-bundles)kullanmayı planlamıyorsanız, [Linux için .NET Core 3. x SDK](https://dotnet.microsoft.com/download)'yı yükleyebilirsiniz.

---

## <a name="create-a-local-functions-project"></a>Yerel Işlevler projesi oluşturma

Işlevler proje dizini, her bir işlev için kodu içeren alt klasörlerle birlikte, [ üzerindehost.js](functions-host-json.md) ve [local.settings.jsüzerinde](#local-settings-file)dosya içerir. Bu dizin, Azure 'daki bir işlev uygulamasının eşdeğeridir. Işlevler klasör yapısı hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri Geliştirici Kılavuzu](functions-reference.md#folder-structure).

Sürüm 3. x/2. x, başlatıldığında projeniz için varsayılan bir dil seçmenizi gerektirir. Sürüm 3. x/2. x ' te, eklenen tüm işlevler varsayılan dil şablonlarını kullan ' ı kullanır. 1. x sürümünde, her bir işlev oluşturduğunuzda dili belirtirsiniz.

Terminal penceresinde veya bir komut isteminden, projeyi ve yerel Git deposunu oluşturmak için aşağıdaki komutu çalıştırın:

```
func init MyFunctionProj
```

>[!IMPORTANT]
> Java, ilk HTTP tetiklemeli işlevinizle birlikte yerel Işlevler projesini oluşturmak için bir Maven ıbetype kullanır. Java projenizi oluşturmak için şu komutu kullanın: `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype` . Maven arşiv EType kullanan bir örnek için bkz. [komut satırı hızlı başlangıç](./create-first-function-cli-java.md).  

Bir proje adı sağladığınızda, bu ada sahip yeni bir klasör oluşturulur ve başlatılır. Aksi takdirde, geçerli klasör başlatılır.  
Sürüm 3. x/2. x ' te, komutunu çalıştırdığınızda projeniz için bir çalışma zamanı seçmeniz gerekir. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Yukarı/aşağı ok tuşlarını kullanarak bir dil seçin ve ENTER tuşuna basın. JavaScript veya TypeScript işlevleri geliştirmeyi planlıyorsanız **düğüm**' yi seçin ve ardından dili seçin. TypeScript [bazı ek gereksinimlere](functions-reference-node.md#typescript)sahiptir. 

Çıktı bir JavaScript projesi için aşağıdaki örneğe benzer şekilde görünür:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` , aksi belirtilmediği takdirde sürüm 3. x/2. x-Only olan aşağıdaki seçenekleri destekler:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--csx`** | .NET işlevlerini, sürüm 1. x davranışı olan C# betiği olarak oluşturur. Yalnızca ile geçerlidir `--worker-runtime dotnet` . |
| **`--docker`** | Seçili bir temel görüntü kullanarak kapsayıcı için bir Dockerfile oluşturur `--worker-runtime` . Özel bir Linux kapsayıcısına yayımlamayı planlarken bu seçeneği kullanın. |
| **`--docker-only`** |  Mevcut bir projeye Dockerfile ekler. Belirtilmediğinde çalışan çalışma zamanı için ister veya üzerinde local.settings.jsayarlanır. Mevcut bir projeyi özel bir Linux kapsayıcısına yayımlamayı planlarken bu seçeneği kullanın. |
| **`--force`** | Projede var olan dosyalar olduğunda bile projeyi başlatın. Bu ayar aynı ada sahip varolan dosyaların üzerine yazar. Proje klasöründeki diğer dosyalar etkilenmez. |
| **`--language`** | Dile özgü bir projeyi başlatır. Şu anda `--worker-runtime` olarak ayarlandığında desteklenir `node` . Seçenekler `typescript` ve ' dir `javascript` . Veya de kullanabilirsiniz `--worker-runtime javascript` `--worker-runtime typescript` .  |
| **`--managed-dependencies`**  | Yönetilen bağımlılıkları kurar. Şu anda yalnızca PowerShell Worker çalışma zamanı bu işlevselliği desteklemektedir. |
| **`--source-control`** | Git deposunun oluşturulup oluşturulmayacağını denetler. Varsayılan olarak, bir depo oluşturulmaz. Ne zaman `true` bir depo oluşturulur. |
| **`--worker-runtime`** | Projenin dil çalışma zamanını ayarlar. Desteklenen değerler şunlardır: `csharp` , `dotnet` , `javascript` , `node` (JavaScript),,, `powershell` `python` ve `typescript` . Java için [Maven](functions-reference-java.md#create-java-functions)'i kullanın. Ayarlanmaması durumunda, başlatma sırasında çalışma zamanını seçmeniz istenir. |
|
> [!IMPORTANT]
> Varsayılan olarak, temel araçların sürüm 2. x ve sonraki sürümleri, .NET çalışma zamanına yönelik işlev uygulama projelerini [C# sınıf projeleri](functions-dotnet-class-library.md) (. csproj) olarak oluşturur. Visual Studio veya Visual Studio Code ile kullanılabilen bu C# projeleri, test sırasında ve Azure 'a yayımlarken derlenir. Bunun yerine, 1. x sürümünde oluşturulan aynı C# betiği (. CSX) dosyalarını oluşturup, portalda çalışmak istiyorsanız, `--csx` işlevleri oluştururken ve dağıtırken parametresini eklemeniz gerekir.

## <a name="register-extensions"></a>Uzantıları Kaydet

HTTP ve Zamanlayıcı Tetikleyicileri hariç olmak üzere, çalışma zamanı sürüm 2. x ve üzeri içindeki Işlev bağlamaları uzantı paketleri olarak uygulanır. HTTP bağlamaları ve Zamanlayıcı Tetikleyicileri uzantı gerektirmez. 

Çeşitli uzantı paketleri arasındaki uyumsuzlukları azaltmak için Işlevler, proje dosyasında host.jsbir uzantı paketine başvurmanıza olanak sağlar. Uzantı paketlerinizi kullanmayı tercih ederseniz, .NET Core 2. x SDK 'sını yerel olarak yüklemeniz ve bir uzantıları. csproj ' ı işlevler projem ile korumanız gerekir.  

Azure Işlevleri çalışma zamanının 2. x ve sonraki sürümlerinde, işlevleriniz içinde kullanılan bağlama türleri için uzantıları açıkça kaydetmeniz gerekir. Bağlama uzantılarını tek tek yüklemeyi seçebilir veya proje dosyasında host.jsbir uzantı paketi başvurusu ekleyebilirsiniz. Uzantı demeti, birden çok bağlama türü kullanırken paket uyumluluk sorunları olma olasılığını ortadan kaldırır. Bağlama uzantılarını kaydetmek için önerilen yaklaşımdır. Uzantı paketleri de .NET Core 2. x SDK yükleme gereksinimini ortadan kaldırır. 

### <a name="use-extension-bundles"></a>Uzantı paketleri kullan

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

Daha fazla bilgi için bkz. [Azure işlevleri bağlama uzantılarını kaydetme](functions-bindings-register.md#extension-bundles). Dosyadaki function.jsbağlama eklemeden önce host.jsuzantı paketleri eklemeniz gerekir.

### <a name="explicitly-install-extensions"></a>Uzantıları açıkça yükler

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Varsayılan olarak, bu ayarlar proje Azure 'da yayımlandığında otomatik olarak geçirilmez. `--publish-local-settings`Bu ayarların Azure 'daki işlev uygulamasına eklendiğinden emin olmak için [yayımladığınızda](#publish) , anahtarını kullanın. **ConnectionString** içindeki değerlerin hiçbir şekilde yayımlanmadığını unutmayın.

İşlev uygulaması ayarları değerleri, kodunuzda ortam değişkenleri olarak da okunabilir. Daha fazla bilgi için, bu dile özgü başvuru konularının ortam değişkenleri bölümüne bakın:

* [C# önceden derlenmiş](functions-dotnet-class-library.md#environment-variables)
* [C# betiği (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

İçin geçerli bir depolama bağlantı dizesi ayarlanmamışsa [`AzureWebJobsStorage`] ve öykünücü kullanılmıyorsa, aşağıdaki hata iletisi gösterilir:

> local.settings.jsüzerindeki AzureWebJobsStorage için eksik değer. HTTP dışındaki tüm tetikleyiciler için bu gereklidir. ' Func Azure functionapp Fetch-App-Settings ' i çalıştırabilir \<functionAppName\> veya üzerinde local.settings.jsbir bağlantı dizesi belirtebilirsiniz.

### <a name="get-your-storage-connection-strings"></a>Depolama bağlantı dizelerinizi alın

Geliştirme için Microsoft Azure Depolama Öykünücüsü kullanılırken bile gerçek bir depolama bağlantısıyla test etmek isteyebilirsiniz. Zaten [bir depolama hesabı oluşturmuş](../storage/common/storage-account-create.md)olduğunuz varsayılarak, aşağıdaki yollarla geçerli bir depolama bağlantı dizesi alabilirsiniz:

- [Azure Portal], **depolama hesaplarını** arayıp seçin. 
  ![Azure portal depolama hesaplarını seçin](./media/functions-run-local/select-storage-accounts.png)
  
  Depolama hesabınızı seçin, **Ayarlar**' da **erişim tuşları** ' nı seçin, sonra **bağlantı dizesi** değerlerinden birini kopyalayın.
  ![Bağlantı dizesini Azure portal Kopyala](./media/functions-run-local/copy-storage-connection-portal.png)

- Azure hesabınıza bağlanmak için [Azure Depolama Gezgini](https://storageexplorer.com/) kullanın. **Gezgin**'de aboneliğinizi genişletin, **depolama hesapları**' nı genişletin, depolama hesabınızı seçin ve birincil veya ikincil bağlantı dizesini kopyalayın.

  ![Bağlantı dizesini Depolama Gezgini Kopyala](./media/functions-run-local/storage-explorer.png)

+ Azure 'dan gelen bağlantı dizesini aşağıdaki komutlardan biriyle indirmek için proje kökünden temel araçları kullanın:

  + Var olan bir işlev uygulamasından tüm ayarları indir:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Belirli bir depolama hesabı için bağlantı dizesini al:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Azure 'da henüz oturum açmadıysanız bunu yapmanız istenir. Bu komutlar dosyadaki local.settings.jsvar olan ayarların üzerine yazar. 

## <a name="create-a-function"></a><a name="create-func"></a>İşlev oluşturma

İşlev oluşturmak için aşağıdaki komutu çalıştırın:

```
func new
```

Sürüm 3. x/2. x ' te, çalıştırdığınızda `func new` işlev uygulamanızın varsayılan dilinde bir şablon seçmeniz istenir, ardından işleviniz için bir ad seçmeniz istenir. 1. x sürümünde de dili seçmeniz istenir.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

İşlev kodu, aşağıdaki kuyruk tetikleyicisi çıktısında görebileceğiniz gibi, belirtilen işlev adına sahip bir alt klasörde oluşturulur:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Ayrıca, aşağıdaki bağımsız değişkenleri kullanarak komutta bu seçenekleri belirtebilirsiniz:

| Bağımsız Değişken     | Description                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Sürüm 2. x ve sonraki sürümler.) 1. x sürümünde ve portalda kullanılan C# betiği (. CSX) şablonlarını üretir. |
| **`--language`**, **`-l`**| C#, F # veya JavaScript gibi şablon programlama dili. Bu seçenek, 1. x sürümünde gereklidir. Sürüm 2. x ve sonraki sürümlerde, bu seçeneği kullanmayın veya çalışan çalışma zamanıyla eşleşen bir dil seçin. |
| **`--name`**, **`-n`** | İşlev adı. |
| **`--template`**, **`-t`** | `func templates list`Desteklenen her dil için kullanılabilir şablonların tüm listesini görmek için komutunu kullanın.   |


Örneğin, tek bir komutta bir JavaScript HTTP tetikleyicisi oluşturmak için şunu çalıştırın:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Tek bir komutta kuyruk tetiklemeli bir işlev oluşturmak için şunu çalıştırın:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>İşlevleri yerel olarak çalıştır

Bir Işlevler projesi çalıştırmak için, Işlevler ana bilgisayarını çalıştırın. Konak, projedeki tüm işlevler için Tetikleyicileri mümkün. Başlat komutu, Proje dilinize bağlı olarak farklılık gösterir.

# <a name="c"></a>[,\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Bu komutun [sanal bir ortamda çalıştırılması](./create-first-function-cli-python.md)gerekir.

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Işlevler çalışma zamanının sürüm 1. x, `host` Aşağıdaki örnekte olduğu gibi komutunu gerektirir:
>
> ```
> func host start
> ```

`func start` Aşağıdaki seçenekleri destekler:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Çalıştırmadan önce derleme geçerli projesi yok. Yalnızca DotNet projeleri için. Varsayılan değer false olarak ayarlanmıştır. 1. x sürümü için desteklenmez. |
| **`--cors-credentials`** | Sürüm 1. x için desteklenmeyen, çıkış noktaları ve kimlik doğrulama üst bilgisi gibi kimlik doğrulamalı isteklere izin ver. |
| **`--cors`** | CORS kaynakları için boşluk olmadan virgülle ayrılmış bir liste. |
| **`--language-worker`** | Dil çalışanını yapılandırmak için bağımsız değişkenler. Örneğin, [hata ayıklama bağlantı noktası ve diğer gerekli bağımsız değişkenleri](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)sağlayarak dil çalışanı için hata ayıklamayı etkinleştirebilirsiniz. 1. x sürümü için desteklenmez. |
| **`--cert`** | Özel anahtar içeren bir. pfx dosyasının yolu. Yalnızca ile kullanılır `--useHttps` . 1. x sürümü için desteklenmez. |
| **`--password`** | Bir. pfx dosyasının parolasını içeren parola veya dosya. Yalnızca ile kullanılır `--cert` . 1. x sürümü için desteklenmez. |
| **`--port`**, **`-p`** | Dinlenecek yerel bağlantı noktası. Varsayılan değer: 7071. |
| **`--pause-on-error`** | İşlemden çıkmadan önce ek giriş için duraklatın. Yalnızca tümleşik geliştirme ortamından (IDE) temel araçlar başlatılırken kullanılır.|
| **`--script-root`**, **`--prefix`** | Çalıştırılacak veya dağıtılacak işlev uygulamasının köküne ait yolu belirtmek için kullanılır. Bu, bir alt klasöre proje dosyaları üreten derlenmiş projeler için kullanılır. Örneğin, bir C# sınıf kitaplığı projesi oluşturduğunuzda, dosyalar üzerinde host.js, local.settings.jsve function.js, gibi bir yol içeren bir *kök* alt klasörde oluşturulur `MyProject/bin/Debug/netstandard2.0` . Bu durumda, önekini olarak ayarlayın `--script-root MyProject/bin/Debug/netstandard2.0` . Bu, Azure 'da çalışırken işlev uygulamasının köküdür. |
| **`--timeout`**, **`-t`** | Işlevlerin başlaması için gereken zaman aşımı (saniye cinsinden). Varsayılan: 20 saniye.|
| **`--useHttps`** | Yerine öğesine bağlayın `https://localhost:{port}` `http://localhost:{port}` . Varsayılan olarak, bu seçenek bilgisayarınızda güvenilir bir sertifika oluşturur.|

Işlevler ana bilgisayarı başlatıldığında, HTTP ile tetiklenen işlevlerin URL 'sini verir:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Yerel olarak çalışırken, HTTP uç noktaları için yetkilendirme zorlanmaz. Bu, tüm yerel HTTP isteklerinin olarak işlendiği anlamına gelir `authLevel = "anonymous"` . Daha fazla bilgi için bkz. [http bağlama makalesi](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Test verilerini bir işleve geçirme

İşlevlerinizi yerel olarak test etmek için, [işlevleri ana bilgisayarı başlatır](#start) ve http isteklerini kullanarak yerel sunucuda uç noktaları çağırabilirsiniz. Çağırdığınız uç nokta, işlevin türüne bağlıdır.

>[!NOTE]
> Bu konudaki örnekler, Terminal veya komut isteminden HTTP istekleri göndermek için kıvrımlı aracı kullanır. Yerel sunucuya HTTP istekleri göndermek için tercih ettiğiniz bir aracı kullanabilirsiniz. , Linux tabanlı sistemlerde ve Windows 10 derleme 17063 ve sonraki sürümlerde kıvrımlı aracı varsayılan olarak kullanılabilir. Daha eski Windows 'ta, önce [kıvrımlı aracını](https://curl.haxx.se/)indirip yüklemeniz gerekir.

Test işlevleri hakkında daha fazla genel bilgi için bkz. [Azure işlevlerinde kodunuzu test etme stratejileri](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP ve Web kancası tarafından tetiklenen işlevler

HTTP ve Web kancası tarafından tetiklenen işlevleri yerel olarak çalıştırmak için aşağıdaki uç noktayı çağırın:

```
http://localhost:{port}/api/{function_name}
```

Işlevlerin barındırın dinlediği sunucu adını ve bağlantı noktasını kullandığınızdan emin olun. Bunu, Işlev Konağı başlatılırken oluşturulan çıktıda görürsünüz. Tetikleyici tarafından desteklenen herhangi bir HTTP yöntemini kullanarak bu URL 'YI çağırabilirsiniz.

Aşağıdaki kıvrımlı komutu, `MyHttpTrigger` sorgu dizesinde geçirilen _Name_ PARAMETRESINE sahip bir Get isteğinden hızlı başlangıç işlevini tetikler.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Aşağıdaki örnek, istek gövdesinde bir POST isteği geçirme _adından_ çağrılan aynı fonksiyondır:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Sorgu dizesindeki verileri geçirerek tarayıcıdan GET istekleri yapabilirsiniz. Diğer tüm HTTP yöntemleri için kıvrımlı, Fiddler, Postman veya benzer bir HTTP test aracı kullanmanız gerekir.

#### <a name="non-http-triggered-functions"></a>HTTP dışı tetiklenen işlevler

HTTP Tetikleyicileri ve Web kancaları ve Event Grid Tetikleyicileri dışındaki tüm işlev türlerinde, bir yönetim uç noktası çağırarak işlevlerinizi yerel olarak test edebilirsiniz. Bu uç noktanın yerel sunucuda bir HTTP POST isteğiyle çağrılması işlevi tetikler. 

Event Grid tetiklenmiş işlevleri yerel olarak test etmek için bkz. [Viewer Web App Ile yerel test](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

İsteğe bağlı olarak, POST isteğinin gövdesinde test verilerini yürütmeye geçirebilirsiniz. Bu işlev, Azure portal **Test** sekmesine benzerdir.

HTTP olmayan işlevleri tetiklemek için aşağıdaki yönetici uç noktasını çağırın:

```
http://localhost:{port}/admin/functions/{function_name}
```

Test verilerini bir işlevin yönetici uç noktasına geçirmek için, verileri bir POST isteği iletisinin gövdesinde belirtmeniz gerekir. İleti gövdesinin aşağıdaki JSON biçimine sahip olması gerekir:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>`Değer, işlev tarafından beklenen bir biçimde veriler içeriyor. Aşağıdaki kıvrımlı örnek, bir işlevin GÖNDERISINI sağlar `QueueTriggerJS` . Bu durumda, giriş sırada bulunması beklenen iletiyle eşdeğer bir dizedir.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Komutu kullanma `func run` (yalnızca sürüm 1. x)

>[!IMPORTANT]
> `func run`Komut yalnızca araçların 1. x sürümünde desteklenir. Daha fazla bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerini hedefleme](set-runtime-version.md)konusu.

Sürüm 1. x ' de, kullanarak bir işlevi doğrudan çağırabilirsiniz `func run <FunctionName>` ve işlev için giriş verileri sağlayabilirsiniz. Bu komut, Azure portal **Test** sekmesini kullanarak bir işlevi çalıştırmaya benzer.

`func run` Aşağıdaki seçenekleri destekler:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Satır içi içerik. |
| **`--debug`**, **`-d`** | İşlevi çalıştırmadan önce konak işlemine bir hata ayıklayıcı ekleyin.|
| **`--timeout`**, **`-t`** | Yerel Işlevlerin barındırmaya hazırlanana kadar beklenecek süre (saniye cinsinden).|
| **`--file`**, **`-f`** | İçerik olarak kullanılacak dosya adı.|
| **`--no-interactive`** | Girişi istemez. Otomasyon senaryoları için faydalıdır.|

Örneğin, HTTP ile tetiklenen bir işlev çağırmak ve içerik gövdesini geçirmek için aşağıdaki komutu çalıştırın:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Azure 'da yayımlama

Azure Functions Core Tools iki tür dağıtımı destekler: işlev projesi dosyalarını [ZIP dağıtımı](functions-deployment-technologies.md#zip-deploy) ve [dağıtım](functions-deployment-technologies.md#docker-container)aracılığıyla doğrudan işlev uygulamanıza dağıtma. Kodunuzu dağıtacağınız [Azure aboneliğinizde zaten bir işlev uygulaması oluşturmuş](functions-cli-samples.md#create)olmanız gerekir. İkililerin dağıtılması için derleme gerektiren projeler oluşturulmalıdır.

>[!IMPORTANT]
>Azure [CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/install-az-ps) yerel olarak yüklü olması gerekir.  

Proje klasörü, yayınlanmaması gereken dile özgü dosyalar ve dizinler içerebilir. Dışlanan öğeler kök proje klasöründeki bir. funcignore dosyasında listelenir.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Proje dosyalarını dağıt

Yerel kodunuzu Azure 'da bir işlev uygulamasına yayımlamak için şu `publish` komutu kullanın:

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> Java, yerel projenizi Azure 'da yayımlamak için Maven 'yi kullanır. Azure 'da yayımlamak için şu komutu kullanın: `mvn azure-functions:deploy` . Azure kaynakları ilk dağıtım sırasında oluşturulur.

Bu komut, Azure 'da var olan bir işlev uygulamasına yayınlar. Aboneliğinizde mevcut olmayan bir ' a yayımlamayı denerseniz bir hata alırsınız `<FunctionAppName>` . Azure CLı veya Azure PowerShell kullanarak komut isteminden veya Terminal penceresinde bir işlev uygulaması oluşturmayı öğrenmek için bkz. [sunucusuz yürütme için işlev uygulaması oluşturma](./scripts/functions-cli-create-serverless.md). Varsayılan olarak, bu komut, [uzak derlemeyi](functions-deployment-technologies.md#remote-build) kullanır ve uygulamanızı [dağıtım paketinden çalıştırmak](run-functions-from-deployment-package.md)üzere dağıtır. Bu önerilen dağıtım modunu devre dışı bırakmak için `--nozip` seçeneğini kullanın.

>[!IMPORTANT]
> Azure portal bir işlev uygulaması oluşturduğunuzda, varsayılan olarak Işlev çalışma zamanının 3. x sürümünü kullanır. İşlev uygulamasının çalışma zamanının sürüm 1. x ' i kullanmasını sağlamak için [Sürüm 1. x üzerinde Çalıştır](functions-versions.md#creating-1x-apps)' daki yönergeleri izleyin.
> Mevcut işlevlere sahip bir işlev uygulamasının çalışma zamanı sürümünü değiştiremezsiniz.

Aşağıdaki yayımlama seçenekleri tüm sürümler için geçerlidir:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Ayarları Azure 'da local.settings.jsyayımlayın, bu ayar zaten varsa üzerine yazmak isteyip istemediğinizi sorar. Microsoft Azure Depolama Öykünücüsü kullanıyorsanız, önce uygulama ayarını [gerçek bir depolama bağlantısı](#get-your-storage-connection-strings)olarak değiştirin. |
| **`--overwrite-settings -y`** | Kullanıldığında uygulama ayarlarının üzerine yazma istemi 'ni gizleyin `--publish-local-settings -i` .|

Aşağıdaki yayımlama seçenekleri yalnızca sürüm 2. x ve sonraki sürümler için desteklenir:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Yalnızca ayarları yayımlayın ve içeriği atlayın. Varsayılan istem. |
|**`--list-ignored-files`** | Yayımlama sırasında yoksayılan,. funcignore dosyasını temel alan dosyaların listesini görüntüler. |
| **`--list-included-files`** | Yayımlanan dosyaların bir listesini görüntüler, bu,. funcignore dosyasını temel alır. |
| **`--nozip`** | Varsayılan modu kapatır `Run-From-Package` . |
| **`--build-native-deps`** | Python işlev uygulamaları yayımlanırken. tekerlek klasörü oluşturmayı atlar. |
| **`--build`**, **`-b`** | Bir Linux işlev uygulamasına dağıtım yaparken derleme eylemi gerçekleştirir. Kabul eder: `remote` ve `local` . |
| **`--additional-packages`** | Yerel bağımlılıklar oluşturulurken yüklenecek paketlerin listesi. Örneğin: `python3-dev libevent-dev`. |
| **`--force`** | Belirli senaryolarda yayımlama öncesi doğrulamayı yoksayın. |
| **`--csx`** | Bir C# betiği (. CSX) projesi yayımlayın. |
| **`--no-build`** | Proje Yayımlama sırasında derlenmedi. Python için `pip install` gerçekleştirilmedi. |
| **`--dotnet-cli-params`** | Derlenmiş C# (. csproj) işlevlerini yayımlarken, temel Araçlar ' DotNet Build--output bin/Publish ' yöntemini çağırır. Buna geçirilen parametreler komut satırına eklenecektir. |

### <a name="deploy-custom-container"></a>Özel kapsayıcı dağıtma

Azure Işlevleri, işlev projenizi [özel bir Docker kapsayıcısına](functions-deployment-technologies.md#docker-container)dağıtmanızı sağlar. Daha fazla bilgi için bkz. [özel bir görüntü kullanarak Linux 'ta Işlev oluşturma](functions-create-function-linux-custom-image.md). Özel kapsayıcılar bir Dockerfile içermelidir. Dockerfile ile bir uygulama oluşturmak için, üzerinde--dockerfile seçeneğini kullanın `func init` .

```
func deploy
```

Aşağıdaki özel kapsayıcı dağıtım seçenekleri kullanılabilir:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Geçerli kullanıcının oturum açan bir Docker kayıt defterinin adı. |
| **`--platform`** | İşlev uygulaması için platform barındırma. Geçerli seçenekler şunlardır `kubernetes` |
| **`--name`** | İşlev uygulaması adı. |
| **`--max`**  | İsteğe bağlı olarak, dağıtılacak maksimum işlev uygulaması örneği sayısını ayarlar. |
| **`--min`**  | İsteğe bağlı olarak, dağıtılacak en düşük işlev uygulaması örneği sayısını ayarlar. |
| **`--config`** | İsteğe bağlı bir dağıtım yapılandırma dosyası ayarlar. |

## <a name="monitoring-functions"></a>İzleme işlevleri

İşlevlerinizin yürütülmesini izlemek için önerilen yol, Azure Application Insights ile tümleştirilmesine göre yapılır. Ayrıca, yürütme günlüklerini yerel bilgisayarınıza da akışla aktarabilirsiniz. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

### <a name="application-insights-integration"></a>Application Insights tümleştirme

Azure 'da işlev uygulamanızı oluştururken Application Insights tümleştirme etkinleştirilmelidir. Bazı nedenlerle işlev uygulamanız bir Application Insights örneğine bağlı değilse, bu tümleştirmeyi Azure portal yapmak kolaydır. Daha fazla bilgi için bkz. [Application Insights Tümleştirmesini Etkinleştirme](configure-monitoring.md#enable-application-insights-integration).

### <a name="enable-streaming-logs"></a>Akış günlüklerini etkinleştir

İşlevleriniz tarafından oluşturulan günlük dosyalarının akışını, yerel bilgisayarınızdaki bir komut satırı oturumunda görüntüleyebilirsiniz. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Bu tür akış günlükleri, işlev uygulamanız için Application Insights tümleştirmenin etkinleştirilmesini gerektirir.   


## <a name="next-steps"></a>Sonraki adımlar

[Microsoft](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools kullanarak Azure işlevleri geliştirmeyi, test yapmayı ve yayımlamayı öğrenin Azure Functions Core Tools [açık kaynak ve GitHub üzerinde barındırılır](https://github.com/azure/azure-functions-cli).  
Bir hata veya özellik isteğini dosyabir [GitHub sorunu açın](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portalı]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[Uzantı demeti]: functions-bindings-register.md#extension-bundles
