---
title: Azure Functions Core Tools çalışma | Microsoft Docs
description: Azure işlevleri 'nde çalıştırmadan önce yerel bilgisayarınızdaki komut isteminden veya terminalden Azure işlevlerini nasıl kodleyeceğinizi ve test leyeceğinizi öğrenin.
author: ggailey777
manager: gwallace
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: glenga
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 72abfef1f86fe47eb7817241a674741f56817f24
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082717"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools çalışın

Azure Functions Core Tools, komut isteminden veya terminalden yerel bilgisayarınızda işlevlerinizi geliştirmenize ve test etmenize olanak tanır. Yerel işlevleriniz canlı Azure hizmetlerine bağlanabilir ve tüm Işlevler çalışma zamanını kullanarak işlevlerinizi yerel bilgisayarınızdaki hata ayıklaması yapabilirsiniz. Hatta, Azure aboneliğinize bir işlev uygulaması dağıtabilirsiniz.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Yerel bilgisayarınızda işlevleri geliştirme ve temel araçları kullanarak bunları Azure 'da yayımlama aşağıdaki temel adımları izler:

> [!div class="checklist"]
> * [Çekirdek araçları ve bağımlılıklarını yükler.](#v2)
> * [Dile özgü şablondan bir işlev uygulama projesi oluşturun.](#create-a-local-functions-project)
> * [Tetikleyici ve bağlama uzantılarını kaydedin.](#register-extensions)
> * [Depolama ve diğer bağlantıları tanımlayın.](#local-settings-file)
> * [Bir tetikleyiciden ve dile özgü şablondan bir işlev oluşturun.](#create-func)
> * [İşlevi yerel olarak çalıştırın](#start)
> * [Projeyi Azure 'da yayımlayın](#publish)

## <a name="core-tools-versions"></a>Temel araçlar sürümleri

Azure Functions Core Tools iki sürümü vardır. Kullandığınız sürüm yerel geliştirme ortamınıza, [dil seçimine](supported-languages.md)ve gerekli destek düzeyine bağlıdır:

+ Sürüm 1. x: çalışma zamanının sürüm 1. x ' i destekler. Araçların bu sürümü yalnızca Windows bilgisayarlarda desteklenir ve bir [NPM paketinden](https://docs.npmjs.com/getting-started/what-is-npm)yüklenir. Bu sürümle birlikte, resmi olarak desteklenmeyen deneysel dillerde işlevler oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure Işlevlerinde desteklenen diller](supported-languages.md)

+ [Sürüm 2. x](#v2): [çalışma zamanının sürüm 2. x](functions-versions.md)' i destekler. Bu sürüm [Windows](#windows-npm), [MacOS](#brew)ve [Linux](#linux)'u destekler. Yükleme için platforma özgü paket yöneticilerini veya NPM 'yi kullanır.

Aksi belirtilmediği takdirde, bu makaledeki örnekler 2. x sürümü içindir.

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools’u Yükleme

[Azure Functions Core Tools] , yerel geliştirme bilgisayarınızda çalıştırabileceğiniz Azure işlevleri çalışma zamanını destekleyen aynı çalışma zamanının bir sürümünü içerir. Ayrıca işlevler oluşturmak, Azure 'a bağlanmak ve işlev projelerini dağıtmak için komutlar sağlar.

### <a name="v2"></a>Sürüm 2. x

Araçların sürüm 2. x, .NET Core üzerinde oluşturulan Azure Işlevleri çalışma zamanı 2. x 'i kullanır. Bu sürüm, [Windows](#windows-npm), [MacOS](#brew)ve [Linux](#linux)dahil tüm platformlarda .NET Core 2. x desteği için desteklenir. 

> [!IMPORTANT]
> [Uzantı demeti]kullanarak .NET Core 2. x SDK 'yı yükleme gereksinimini atlayabilirsiniz.

#### <a name="windows-npm"></a>Pencerelerin

Aşağıdaki adımlarda, Windows 'a temel araçları yüklemek için NPM kullanılır. [Chocolatey](https://chocolatey.org/)de kullanabilirsiniz. Daha fazla bilgi için bkz. [temel araçlar Benioku](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. NPM içeren [Node.js]' yi yükler. Araçların sürüm 2. x için yalnızca Node. js 8,5 ve üzeri sürümleri desteklenir.

1. Temel Araçlar paketini yükler:

    ```bash
    npm install -g azure-functions-core-tools
    ```

   NPM 'nin temel araçlar paketini indirmesi ve yüklemesi birkaç dakika sürebilir.

1. [Uzantı demeti]kullanmayı planlamıyorsanız, [Windows için .NET Core 2. x SDK 'sını](https://www.microsoft.com/net/download/windows)yükleyebilirsiniz.

#### <a name="brew"></a>Homebrew ile MacOS

Aşağıdaki adımlarda, macOS 'a çekirdek araçları yüklemek için homebrew kullanılır.

1. Zaten yüklenmemişse [homebrew](https://brew.sh/)'ı yükleme.

1. Temel Araçlar paketini yükler:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

1. [Uzantı demeti]kullanmayı planlamıyorsanız, [MacOS için .NET Core 2. x SDK](https://www.microsoft.com/net/download/macos)'yı yükleyebilirsiniz.


#### <a name="linux"></a>APT ile Linux (Ubuntu/dekim)

Aşağıdaki adımlarda, Ubuntu/de, Linux dağıtımına çekirdek araçları yüklemek için [apt](https://wiki.debian.org/Apt) kullanılır. Diğer Linux dağıtımları için bkz. [temel araçlar Benioku dosyası](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Paket bütünlüğünü doğrulamak için Microsoft paket deposu GPG anahtarını yükler:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Bir APT güncelleştirmesi yapmadan önce .NET geliştirme kaynak listesini ayarlayın.

   Ubuntu için APT kaynak listesini ayarlamak için şu komutu çalıştırın:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Dekim için APT kaynak listesini ayarlamak için şu komutu çalıştırın:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Aşağıda listelenen uygun Linux sürümü dizelerinden biri için `/etc/apt/sources.list.d/dotnetdev.list` dosyasını denetleyin:

    | Linux dağıtım | Sürüm |
    | --------------- | ----------- |
    | Debian 10 | `buster` |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. APT kaynak güncelleştirmesini başlatın:

    ```bash
    sudo apt-get update
    ```

1. Temel Araçlar paketini yükler:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. [Uzantı demeti]kullanmayı planlamıyorsanız, [Linux için .NET Core 2. x SDK](https://www.microsoft.com/net/download/linux)'yı yükleyebilirsiniz.

## <a name="create-a-local-functions-project"></a>Bir yerel işlevler projesi oluşturma

Bir işlevler proje dizini, [Host. JSON](functions-host-json.md) ve [Local. Settings. JSON](#local-settings-file)dosyalarını, tek tek işlevlerin kodunu içeren alt klasörlerle birlikte içerir. Bu dizin, Azure 'daki bir işlev uygulamasının eşdeğeridir. Işlevler klasör yapısı hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri Geliştirici Kılavuzu](functions-reference.md#folder-structure).

Sürüm 2. x, başlatıldığında projeniz için varsayılan bir dil seçmenizi ve tüm işlevlerin varsayılan dil şablonlarını kullanmasını gerektirir. 1\. x sürümünde, her bir işlev oluşturduğunuzda dili belirtirsiniz.

Terminal penceresinde veya bir komut isteminden, projeyi ve yerel Git deposunu oluşturmak için aşağıdaki komutu çalıştırın:

```bash
func init MyFunctionProj
```

Bir proje adı sağladığınızda, bu ada sahip yeni bir klasör oluşturulur ve başlatılır. Aksi takdirde, geçerli klasör başlatılır.  
Sürüm 2. x içinde, komutunu çalıştırdığınızda projeniz için bir çalışma zamanı seçmeniz gerekir. 

```output
Select a worker runtime:
dotnet
node
python 
powershell
```

Yukarı/aşağı ok tuşlarını kullanarak bir dil seçin ve ENTER tuşuna basın. JavaScript veya TypeScript işlevleri geliştirmeyi planlıyorsanız **düğüm**' yi seçin ve ardından dili seçin. TypeScript [bazı ek gereksinimlere](functions-reference-node.md#typescript)sahiptir. 

Çıktı bir JavaScript projesi için aşağıdaki örneğe benzer şekilde görünür:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init`, aksi belirtilmediği takdirde yalnızca sürüm 2. x olan aşağıdaki seçenekleri destekler:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Bir [ C# sınıf kitaplığı (. cs) projesi](functions-dotnet-class-library.md)başlatır. |
| **`--csx`** | Bir [ C# betik (. CSX) projesi](functions-reference-csharp.md)başlatır. Sonraki komutlarda `--csx` belirtmelisiniz. |
| **`--docker`** | Seçilen `--worker-runtime`temel bir görüntü kullanarak kapsayıcı için bir Dockerfile oluşturun. Özel bir Linux kapsayıcısına yayımlamayı planlarken bu seçeneği kullanın. |
| **`--docker-only`** |  Mevcut bir projeye Dockerfile ekler. Belirtilmemişse veya Local. Settings. JSON içinde ayarlanmamışsa çalışan çalışma zamanı için sorar. Mevcut bir projeyi özel bir Linux kapsayıcısına yayımlamayı planlarken bu seçeneği kullanın. |
| **`--force`** | Projede var olan dosyalar olduğunda bile projeyi başlatın. Bu ayar aynı ada sahip varolan dosyaların üzerine yazar. Proje klasöründeki diğer dosyalar etkilenmez. |
| **`--java`**  | Bir [Java projesi](functions-reference-java.md)başlatır. |
| **`--javascript`**<br/>**`--node`**  | Bir [JavaScript projesi](functions-reference-node.md)başlatır. |
| **`--no-source-control`**<br/>**`-n`** | 1\. x sürümünde bir git deposunun varsayılan oluşturulmasını engeller. Sürüm 2. x içinde git deposu varsayılan olarak oluşturulmaz. |
| **`--powershell`**  | Bir [PowerShell projesi](functions-reference-powershell.md)başlatır. |
| **`--python`**  | Bir [Python projesi](functions-reference-python.md)başlatır. |
| **`--source-control`** | Git deposunun oluşturulup oluşturulmayacağını denetler. Varsayılan olarak, bir depo oluşturulmaz. `true`, bir depo oluşturulur. |
| **`--typescript`**  | Bir [TypeScript projesi](functions-reference-node.md#typescript)başlatır. |
| **`--worker-runtime`** | Projenin dil çalışma zamanını ayarlar. Desteklenen değerler şunlardır: `csharp`, `dotnet`, `java`, `javascript`,`node` (JavaScript), `powershell`, `python`ve `typescript`. Ayarlanmaması durumunda, başlatma sırasında çalışma zamanını seçmeniz istenir. |

> [!IMPORTANT]
> Varsayılan olarak, temel araçların sürüm 2. x 'i, .NET çalışma zamanına yönelik işlev uygulaması projelerini [ C# sınıf projeleri](functions-dotnet-class-library.md) (. csproj) olarak oluşturur. Visual C# Studio veya Visual Studio Code ile kullanılabilen bu projeler, test sırasında ve Azure 'a yayımlarken derlenir. Bunun yerine, 1. x sürümünde oluşturulan aynı C# komut dosyası (. CSX) dosyalarını oluşturmak ve bunlarla çalışmak istiyorsanız, işlevleri oluştururken ve dağıtırken`--csx`parametresini eklemeniz gerekir.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Varsayılan olarak, bu ayarlar proje Azure 'da yayımlandığında otomatik olarak geçirilmez. Bu ayarların Azure 'daki işlev uygulamasına eklendiğinden emin olmak için [yayımlarken](#publish) `--publish-local-settings` anahtarını kullanın. **ConnectionString** içindeki değerlerin hiçbir şekilde yayımlanmadığını unutmayın.

İşlev uygulaması ayarları değerleri, kodunuzda ortam değişkenleri olarak da okunabilir. Daha fazla bilgi için, bu dile özgü başvuru konularının ortam değişkenleri bölümüne bakın:

* [C#derlemesi](functions-dotnet-class-library.md#environment-variables)
* [C# betiği (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

[`AzureWebJobsStorage`] için geçerli bir depolama bağlantı dizesi ayarlanmamışsa ve öykünücü kullanılmıyorsa, aşağıdaki hata iletisi görüntülenir:

> Yerel. Settings. JSON içinde AzureWebJobsStorage için eksik değer. HTTP dışındaki tüm tetikleyiciler için bu gereklidir. ' Func Azure functionapp Fetch-App-settings \<functionAppName\>' çalıştırabilir veya yerel. Settings. JSON içinde bir bağlantı dizesi belirtebilirsiniz.

### <a name="get-your-storage-connection-strings"></a>Depolama bağlantı dizelerinizi alın

Geliştirme için depolama öykünücüsü kullanılırken bile gerçek bir depolama bağlantısıyla test etmek isteyebilirsiniz. Zaten [bir depolama hesabı oluşturmuş](../storage/common/storage-create-storage-account.md)olduğunuz varsayılarak, aşağıdaki yollarla geçerli bir depolama bağlantı dizesi alabilirsiniz:

- [Azure Portal], **depolama hesaplarını**arayıp seçin. 
  Azure portal ![Storagea hesaplarını seçin](./media/functions-run-local/select-storage-accounts.png)
  
  Depolama hesabınızı seçin, **Ayarlar**' da **erişim tuşları** ' nı seçin, sonra **bağlantı dizesi** değerlerinden birini kopyalayın.
  Azure portal](./media/functions-run-local/copy-storage-connection-portal.png) bağlantı dizesini ![Kopyala

- Azure hesabınıza bağlanmak için [Azure Depolama Gezgini](https://storageexplorer.com/) kullanın. **Gezgin**'de aboneliğinizi genişletin, depolama hesabınızı seçin ve birincil veya ikincil bağlantı dizesini kopyalayın.

  ![Bağlantı dizesini Depolama Gezgini Kopyala](./media/functions-run-local/storage-explorer.png)

+ Azure 'dan bağlantı dizesini aşağıdaki komutlardan biriyle indirmek için temel araçları kullanın:

  + Var olan bir işlev uygulamasından tüm ayarları indir:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Belirli bir depolama hesabı için bağlantı dizesini al:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Azure 'da henüz oturum açmadıysanız, bunu yapmanız istenir.

## <a name="create-func"></a>İşlev oluşturma

Bir işlev oluşturmak için aşağıdaki komutu çalıştırın:

```bash
func new
```

Sürüm 2. x ' de `func new` çalıştırdığınızda, işlev uygulamanızın varsayılan dilinde bir şablon seçmeniz istenir. Ayrıca, işleviniz için bir ad seçmeniz istenir. 1\. x sürümünde de dili seçmeniz istenir.

```output
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
```

İşlev kodu, aşağıdaki kuyruk tetikleyicisi çıktısında görebileceğiniz gibi, belirtilen işlev adına sahip bir alt klasörde oluşturulur:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Ayrıca, aşağıdaki bağımsız değişkenleri kullanarak komutta bu seçenekleri belirtebilirsiniz:

| Bağımsız Değişken     | Açıklama                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Sürüm 2. x) 1. x C# sürümünde ve portalda kullanılan aynı komut dosyası (. CSX) şablonlarını üretir. |
| **`--language -l`**| ,, Veya JavaScript gibi şablon programlama C#dili F#. Bu seçenek, 1. x sürümünde gereklidir. Sürüm 2. x içinde bu seçeneği kullanmayın veya çalışan çalışma zamanıyla eşleşen bir dil seçin. |
| **`--name -n`** | İşlev adı. |
| **`--template -t`** | Desteklenen her dil için kullanılabilir şablonların tüm listesini görmek için `func templates list` komutunu kullanın.   |

Örneğin, tek bir komutta bir JavaScript HTTP tetikleyicisi oluşturmak için şunu çalıştırın:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Tek bir komutta kuyruk tetiklemeli bir işlev oluşturmak için şunu çalıştırın:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>İşlevleri yerel olarak çalıştır

Bir Işlevler projesi çalıştırmak için, Işlevler ana bilgisayarını çalıştırın. Konak, projedeki tüm işlevler için Tetikleyicileri mümkün. 

### <a name="version-2x"></a>Sürüm 2. x

Çalışma zamanının 2. x sürümünde, başlangıç komutu Proje dilinize bağlı olarak değişir.

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>Sürüm 1. x

Işlevler çalışma zamanının sürüm 1. x, aşağıdaki örnekte olduğu gibi `host` komutunu gerektirir:

```command
func host start
```

`func start` aşağıdaki seçenekleri destekler:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Çalıştırmadan önce derleme geçerli projesi yok. Yalnızca DotNet projeleri için. Varsayılan değer false olarak ayarlanmıştır. Yalnızca sürüm 2. x. |
| **`--cert`** | Özel anahtar içeren bir. pfx dosyasının yolu. Yalnızca `--useHttps`ile kullanılır. Yalnızca sürüm 2. x. |
| **`--cors-credentials`** | Yalnızca çıkış noktaları arası kimlik doğrulamalı isteklere (yani, tanımlama bilgileri ve kimlik doğrulama üstbilgisi) sürüm 2. x 'e izin verin. |
| **`--cors`** | CORS kaynakları için boşluk olmadan virgülle ayrılmış bir liste. |
| **`--language-worker`** | Dil çalışanını yapılandırmak için bağımsız değişkenler. Yalnızca sürüm 2. x. |
| **`--nodeDebugPort -n`** | Kullanılacak düğüm hata ayıklayıcı bağlantı noktası. Varsayılan: Launch. JSON veya 5858 değeri. Yalnızca sürüm 1. x. |
| **`--password`** | Bir. pfx dosyasının parolasını içeren parola veya dosya. Yalnızca `--cert`ile kullanılır. Yalnızca sürüm 2. x. |
| **`--port -p`** | Dinlenecek yerel bağlantı noktası. Varsayılan değer: 7071. |
| **`--pause-on-error`** | İşlemden çıkmadan önce ek giriş için duraklatın. Yalnızca tümleşik geliştirme ortamından (IDE) temel araçlar başlatılırken kullanılır.|
| **`--script-root --prefix`** | Çalıştırılacak veya dağıtılacak işlev uygulamasının köküne ait yolu belirtmek için kullanılır. Bu, bir alt klasöre proje dosyaları üreten derlenmiş projeler için kullanılır. Örneğin, bir C# sınıf kitaplığı projesi oluşturduğunuzda, Host. JSON, Local. Settings. JSON ve function. json dosyaları `MyProject/bin/Debug/netstandard2.0`gibi bir yol içeren bir *kök* alt klasörde oluşturulur. Bu durumda, ön eki `--script-root MyProject/bin/Debug/netstandard2.0`olarak ayarlayın. Bu, Azure 'da çalışırken işlev uygulamasının köküdür. |
| **`--timeout -t`** | Işlevlerin başlaması için gereken zaman aşımı (saniye cinsinden). Varsayılan: 20 saniye.|
| **`--useHttps`** | `http://localhost:{port}`yerine `https://localhost:{port}` bağlayın. Varsayılan olarak, bu seçenek bilgisayarınızda güvenilir bir sertifika oluşturur.|

Işlevler ana bilgisayarı başlatıldığında, HTTP ile tetiklenen işlevlerin URL 'sini verir:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Yerel olarak çalışırken, HTTP uç noktaları için kimlik doğrulama zorlanmaz. Bu, tüm yerel HTTP isteklerinin `authLevel = "anonymous"`olarak işlendiği anlamına gelir. Daha fazla bilgi için bkz. [http bağlama makalesi](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Test verilerini bir işleve geçirme

İşlevlerinizi yerel olarak test etmek için, [işlevleri ana bilgisayarı başlatır](#start) ve http isteklerini kullanarak yerel sunucuda uç noktaları çağırabilirsiniz. Çağırdığınız uç nokta, işlevin türüne bağlıdır.

>[!NOTE]
> Bu konudaki örnekler, Terminal veya komut isteminden HTTP istekleri göndermek için kıvrımlı aracı kullanır. Yerel sunucuya HTTP istekleri göndermek için tercih ettiğiniz bir aracı kullanabilirsiniz. , Linux tabanlı sistemlerde ve Windows 10 derleme 17063 ve sonraki sürümlerde kıvrımlı aracı varsayılan olarak kullanılabilir. Daha eski Windows 'ta, önce [kıvrımlı aracını](https://curl.haxx.se/)indirip yüklemeniz gerekir.

Test işlevleri hakkında daha fazla genel bilgi için bkz. [Azure işlevlerinde kodunuzu test etme stratejileri](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP ve Web kancası tarafından tetiklenen işlevler

HTTP ve Web kancası tarafından tetiklenen işlevleri yerel olarak çalıştırmak için aşağıdaki uç noktayı çağırın:

    http://localhost:{port}/api/{function_name}

Işlevlerin barındırın dinlediği sunucu adını ve bağlantı noktasını kullandığınızdan emin olun. Bunu, Işlev Konağı başlatılırken oluşturulan çıktıda görürsünüz. Tetikleyici tarafından desteklenen herhangi bir HTTP yöntemini kullanarak bu URL 'YI çağırabilirsiniz.

Aşağıdaki kıvrımlı komutu, sorgu dizesinde geçirilen _Name_ parametresine sahıp bir GET isteğinden `MyHttpTrigger` hızlı başlangıç işlevini tetikler.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Aşağıdaki örnek, istek gövdesinde bir POST isteği geçirme _adından_ çağrılan aynı fonksiyondır:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Sorgu dizesindeki verileri geçirerek tarayıcıdan GET istekleri yapabilirsiniz. Diğer tüm HTTP yöntemleri için kıvrımlı, Fiddler, Postman veya benzer bir HTTP test aracı kullanmanız gerekir.

#### <a name="non-http-triggered-functions"></a>HTTP dışı tetiklenen işlevler

HTTP Tetikleyicileri ve Web kancaları dışındaki tüm işlev türlerinde, bir yönetim uç noktası çağırarak işlevlerinizi yerel olarak test edebilirsiniz. Bu uç noktanın yerel sunucuda bir HTTP POST isteğiyle çağrılması işlevi tetikler. İsteğe bağlı olarak, POST isteğinin gövdesinde test verilerini yürütmeye geçirebilirsiniz. Bu işlev, Azure portal **Test** sekmesine benzerdir.

HTTP olmayan işlevleri tetiklemek için aşağıdaki yönetici uç noktasını çağırın:

    http://localhost:{port}/admin/functions/{function_name}

Test verilerini bir işlevin yönetici uç noktasına geçirmek için, verileri bir POST isteği iletisinin gövdesinde belirtmeniz gerekir. İleti gövdesinin aşağıdaki JSON biçimine sahip olması gerekir:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` değeri, işlevi tarafından beklenen bir biçimde veriler içeriyor. Aşağıdaki kıvrımlı örnek bir `QueueTriggerJS` işlevinin GÖNDERISINI sağlar. Bu durumda, giriş sırada bulunması beklenen iletiyle eşdeğer bir dizedir.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>1\. x sürümünde `func run` komutu kullanma

>[!IMPORTANT]
> `func run` komutu, araçların 2. x sürümünde desteklenmez. Daha fazla bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerini hedefleme](set-runtime-version.md)konusu.

Ayrıca, `func run <FunctionName>` kullanarak doğrudan bir işlevi çağırabilir ve işlev için giriş verileri sağlayabilirsiniz. Bu komut, Azure portal **Test** sekmesini kullanarak bir işlevi çalıştırmaya benzer.

`func run` aşağıdaki seçenekleri destekler:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Satır içi içerik. |
| **`--debug -d`** | İşlevi çalıştırmadan önce konak işlemine bir hata ayıklayıcı ekleyin.|
| **`--timeout -t`** | Yerel Işlevlerin barındırmaya hazırlanana kadar beklenecek süre (saniye cinsinden).|
| **`--file -f`** | İçerik olarak kullanılacak dosya adı.|
| **`--no-interactive`** | Girişi istemez. Otomasyon senaryoları için faydalıdır.|

Örneğin, HTTP ile tetiklenen bir işlev çağırmak ve içerik gövdesini geçirmek için aşağıdaki komutu çalıştırın:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Azure 'da yayımlama

Azure Functions Core Tools iki tür dağıtımı destekler: işlev projesi dosyalarını [ZIP dağıtımı](functions-deployment-technologies.md#zip-deploy) ve [dağıtım](functions-deployment-technologies.md#docker-container)aracılığıyla doğrudan işlev uygulamanıza dağıtma. Kodunuzu dağıtacağınız [Azure aboneliğinizde zaten bir işlev uygulaması oluşturmuş](functions-cli-samples.md#create)olmanız gerekir. İkililerin dağıtılması için derleme gerektiren projeler oluşturulmalıdır.

Proje klasörü, yayınlanmaması gereken dile özgü dosyalar ve dizinler içerebilir. Dışlanan öğeler kök proje klasöründeki bir. funcignore dosyasında listelenir.     

### <a name="project-file-deployment"></a>Dağıtım (proje dosyaları)

Yerel kodunuzu Azure 'da bir işlev uygulamasına yayımlamak için `publish` komutunu kullanın:

```bash
func azure functionapp publish <FunctionAppName>
```

Bu komut, Azure 'da var olan bir işlev uygulamasına yayınlar. Aboneliğinizde mevcut olmayan bir `<FunctionAppName>` yayımlamaya çalışırsanız hata alırsınız. Azure CLı kullanarak komut isteminden veya Terminal penceresinde bir işlev uygulaması oluşturmayı öğrenmek için bkz. [sunucusuz yürütme için işlev uygulaması oluşturma](./scripts/functions-cli-create-serverless.md). Varsayılan olarak, bu komut, [uzak derlemeyi](functions-deployment-technologies.md#remote-build) kullanır ve uygulamanızı [dağıtım paketinden çalıştırmak](run-functions-from-deployment-package.md)üzere dağıtır. Bu önerilen dağıtım modunu devre dışı bırakmak için `--nozip` seçeneğini kullanın.

>[!IMPORTANT]
> Azure portal bir işlev uygulaması oluşturduğunuzda, varsayılan olarak Işlev çalışma zamanının 2. x sürümünü kullanır. İşlev uygulamasının çalışma zamanının sürüm 1. x ' i kullanmasını sağlamak için [Sürüm 1. x üzerinde Çalıştır](functions-versions.md#creating-1x-apps)' daki yönergeleri izleyin.
> Mevcut işlevlere sahip bir işlev uygulamasının çalışma zamanı sürümünü değiştiremezsiniz.

Aşağıdaki yayımlama seçenekleri, 1. x ve 2. x sürümleri için geçerlidir:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Ayarları yerel. Settings. json ' da Azure 'a yayımlayın, bu ayar zaten varsa üzerine yazma isteminde bulunur. Depolama öykünücüsünü kullanıyorsanız, önce uygulama ayarını [gerçek bir depolama bağlantısı](#get-your-storage-connection-strings)olarak değiştirin. |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` kullanıldığında uygulama ayarlarının üzerine yazma istemi 'ni gizleyin.|

Aşağıdaki yayımlama seçenekleri yalnızca sürüm 2. x içinde desteklenir:

| Seçenek     | Açıklama                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Yalnızca ayarları yayımlayın ve içeriği atlayın. Varsayılan istem. |
|**`--list-ignored-files`** | Yayımlama sırasında yoksayılan,. funcignore dosyasını temel alan dosyaların listesini görüntüler. |
| **`--list-included-files`** | Yayımlanan dosyaların bir listesini görüntüler, bu,. funcignore dosyasını temel alır. |
| **`--nozip`** | Varsayılan `Run-From-Package` modunu kapatır. |
| **`--build-native-deps`** | Python işlev uygulamaları yayımlanırken. tekerlek klasörü oluşturmayı atlar. |
| **`--build`**<br/>**`-b`** | Bir Linux işlev uygulamasına dağıtım yaparken derleme eylemi gerçekleştirir. Kabul eder: `remote` ve `local`. |
| **`--additional-packages`** | Yerel bağımlılıklar oluşturulurken yüklenecek paketlerin listesi. Örneğin: `python3-dev libevent-dev`. |
| **`--force`** | Belirli senaryolarda yayımlama öncesi doğrulamayı yoksayın. |
| **`--csx`** | Bir C# betik (. CSX) projesi yayımlayın. |
| **`--no-build`** | .NET sınıf kitaplığı işlevleri oluşturmayın. |
| **`--dotnet-cli-params`** | Derlenen C# (. csproj) işlevleri yayımlandığında, temel Araçlar ' DotNet Build--output bin/Publish ' yöntemini çağırır. Buna geçirilen parametreler komut satırına eklenecektir. |

### <a name="deployment-custom-container"></a>Dağıtım (özel kapsayıcı)

Azure Işlevleri, işlev projenizi [özel bir Docker kapsayıcısına](functions-deployment-technologies.md#docker-container)dağıtmanızı sağlar. Daha fazla bilgi için bkz. [özel bir görüntü kullanarak Linux 'ta Işlev oluşturma](functions-create-function-linux-custom-image.md). Özel kapsayıcılar bir Dockerfile içermelidir. Dockerfile ile bir uygulama oluşturmak için, `func init`üzerinde--dockerfile seçeneğini kullanın.

```bash
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

### <a name="enable-application-insights-integration"></a>Application Insights tümleştirmeyi etkinleştir

Azure portal bir işlev uygulaması oluşturduğunuzda, Application Insights tümleştirmesi varsayılan olarak sizin için yapılır. Ancak, Azure CLı kullanarak işlev uygulamanızı oluşturduğunuzda, Azure 'daki işlev uygulamanızda tümleştirme yapılmaz.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Akış günlüklerini etkinleştir

İşlevleriniz tarafından oluşturulan günlük dosyalarının akışını, yerel bilgisayarınızdaki bir komut satırı oturumunda görüntüleyebilirsiniz. 

#### <a name="native-streaming-logs"></a>Yerel akış günlükleri

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Bu tür akış günlükleri, işlev uygulamanız için [Application Insights tümleştirmeyi etkinleştirmenizi](#enable-application-insights-integration) gerektirir.   


## <a name="next-steps"></a>Sonraki adımlar

Azure Functions Core Tools [açık kaynaktır ve GitHub üzerinde barındırılır](https://github.com/azure/azure-functions-cli).  
Bir hata veya özellik isteğini dosyabir [GitHub sorunu açın](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[Uzantı demeti]: functions-bindings-register.md#extension-bundles
