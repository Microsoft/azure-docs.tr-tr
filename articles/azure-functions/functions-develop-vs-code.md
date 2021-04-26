---
title: Visual Studio Code kullanarak Azure İşlevleri geliştirme
description: Visual Studio Code için Azure Işlevleri uzantısını kullanarak Azure Işlevleri geliştirmeyi ve test yapmayı öğrenin.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871282"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Visual Studio Code kullanarak Azure İşlevleri geliştirme

[Visual Studio Code Için Azure işlevleri uzantısı] , işlevleri yerel olarak geliştirmenize ve bunları Azure 'a dağıtmanıza imkan tanır. Azure Işlevleri ile ilk kez bu deneyim varsa, [Azure işlevleri 'ne giriş](functions-overview.md)konusunda daha fazla bilgi edinebilirsiniz.

Azure Işlevleri uzantısı şu avantajları sağlar:

* Yerel geliştirme bilgisayarınızda işlevleri düzenleyin, derleyin ve çalıştırın.
* Azure Işlevleri projenizi doğrudan Azure 'da yayımlayın.
* Visual Studio Code avantajlarından yararlanarak işlevlerinizi çeşitli dillerde yazın.

Uzantı, sürüm 2. x ile başlayan Azure Işlevleri çalışma zamanı tarafından desteklenen aşağıdaki dillerde kullanılabilir:

* [Derlenen C#](functions-dotnet-class-library.md)
* [C# betiği](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>[C# betiğini varsayılan proje diliniz olarak ayarlamanızı](#c-script-projects)gerektirir.

Bu makalede, örnekler şu anda yalnızca JavaScript (Node.js) ve C# sınıf kitaplığı işlevleri için kullanılabilir.  

Bu makalede, işlevleri geliştirmek ve Azure 'da yayımlamak için Azure Işlevleri uzantısının nasıl kullanılacağına ilişkin ayrıntılar sağlanmaktadır. Bu makaleyi okuduktan sonra, [Visual Studio Code kullanarak ilk işlevinizi oluşturmanız](./create-first-function-vs-code-csharp.md)gerekir.

> [!IMPORTANT]
> Tek bir işlev uygulaması için yerel geliştirme ve Portal geliştirmeyi karışmayın. Yerel bir projeden bir işlev uygulamasına yayımladığınızda, dağıtım işlemi portalda geliştirdiğiniz işlevlerin üzerine yazar.

## <a name="prerequisites"></a>Önkoşullar

Visual Studio Code için [Azure işlevleri uzantısı][Azure işlevleri uzantısını]yükleyip çalıştırmadan önce, şu gereksinimleri karşılamanız gerekir:

* [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms)birine [Visual Studio Code](https://code.visualstudio.com/) .

* Etkin bir Azure aboneliği.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Azure depolama hesabı gibi ihtiyacınız olan diğer kaynaklar, [Visual Studio Code kullanarak yayımladığınızda](#publish-to-azure)aboneliğinizde oluşturulur. 

### <a name="run-local-requirements"></a>Yerel gereksinimleri Çalıştır

Bu önkoşulların yalnızca [işlevlerinizi yerel olarak çalıştırmak ve hatalarını ayıklamak](#run-functions-locally)için gereklidir. Azure Işlevleri üzerinde proje oluşturmak veya yayımlamak gerekli değildir.

# <a name="c"></a>[,\#](#tab/csharp)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) sürüm 2. x veya üzeri. Temel Araçlar paketi, projeyi yerel olarak başlattığınızda otomatik olarak indirilir ve yüklenir. Temel araçlar tüm Azure Işlevleri çalışma zamanını içerir, bu nedenle indirme ve yükleme biraz zaman alabilir.

+ Visual Studio Code için [C# uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp). 

+ [.NET Core CLI araçları](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) sürüm 2. x veya üzeri. Temel Araçlar paketi, projeyi yerel olarak başlattığınızda otomatik olarak indirilir ve yüklenir. Temel araçlar tüm Azure Işlevleri çalışma zamanını içerir, bu nedenle indirme ve yükleme biraz zaman alabilir.

+ [Java uzantısı Için hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) önerilir. Desteklenen diğer sürümler için bkz. [Java sürümleri](functions-reference-java.md#java-versions).

+ [Maven 3 veya üzeri](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) sürüm 2. x veya üzeri. Temel Araçlar paketi, projeyi yerel olarak başlattığınızda otomatik olarak indirilir ve yüklenir. Temel araçlar tüm Azure Işlevleri çalışma zamanını içerir, bu nedenle indirme ve yükleme biraz zaman alabilir.

+ [Node.js](https://nodejs.org/), ETKIN LTS ve bakım LTS sürümleri (10.14.1 önerilir). `node --version`Sürümünüzü denetlemek için komutunu kullanın. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) sürüm 2. x veya üzeri. Temel Araçlar paketi, projeyi yerel olarak başlattığınızda otomatik olarak indirilir ve yüklenir. Temel araçlar tüm Azure Işlevleri çalışma zamanını içerir, bu nedenle indirme ve yükleme biraz zaman alabilir.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) önerilir. Sürüm bilgileri için bkz. [PowerShell sürümleri](functions-reference-powershell.md#powershell-versions).

+ Hem [.net core 3,1 çalışma zamanı](https://dotnet.microsoft.com/download) hem de [.NET Core 2,1 çalışma zamanı](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ [Visual Studio Code Için PowerShell uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) sürüm 2. x veya üzeri. Temel Araçlar paketi, projeyi yerel olarak başlattığınızda otomatik olarak indirilir ve yüklenir. Temel araçlar tüm Azure Işlevleri çalışma zamanını içerir, bu nedenle indirme ve yükleme biraz zaman alabilir.

+ [Python 3. x](https://www.python.org/downloads/). Sürüm bilgileri için bkz. Azure Işlevleri çalışma zamanı tarafından [Python sürümleri](functions-reference-python.md#python-version) .

+ Visual Studio Code için [Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure İşlevleri projesi oluşturma

Işlevler uzantısı, ilk işlevinizle birlikte bir işlev uygulaması projesi oluşturmanızı sağlar. Aşağıdaki adımlarda, yeni Işlevler projesinde HTTP ile tetiklenen bir işlevin nasıl oluşturulacağı gösterilmektedir. [Http tetikleyicisi](functions-bindings-http-webhook.md) , göstermek için en basit işlev tetikleyici şablonudur.

1. **Azure: işlevler**' de **işlev oluştur** simgesini seçin:

    ![İşlev oluşturma](./media/functions-develop-vs-code/create-function.png)

1. İşlev uygulaması projeniz için klasörü seçin ve ardından **işlev projeniz için bir dil seçin**.

1. **Http tetikleyici** işlev şablonunu seçin veya artık işlev olmadan bir proje oluşturmak için **Atla** seçeneğini belirleyebilirsiniz. Projenize daha sonra dilediğiniz zaman [bir işlev ekleyebilirsiniz](#add-a-function-to-your-project) .

    ![HTTP tetikleyicisi şablonunu seçin](./media/functions-develop-vs-code/create-function-choose-template.png)

1. İşlev adı olarak **Httpexample** yazın ve ENTER ' ı seçin ve ardından **işlev** yetkilendirmesi ' ni seçin. Bu yetkilendirme düzeyi, işlev uç noktasını çağırdığınızda bir [işlev anahtarı](functions-bindings-http-webhook-trigger.md#authorization-keys) sağlamanızı gerektirir.

    ![Işlev yetkilendirmesi seçin](./media/functions-develop-vs-code/create-function-auth.png)

    Seçtiğiniz dilde ve HTTP ile tetiklenen bir işlev için şablonda bir işlev oluşturulur.

    ![Visual Studio Code 'de HTTP ile tetiklenen işlev şablonu](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Oluşturulan proje dosyaları

Proje şablonu seçtiğiniz dilde bir proje oluşturur ve gerekli bağımlılıkları kurar. Her dil için, yeni proje bu dosyalara sahiptir:

* **host.js**:, işlevleri ana bilgisayarını yapılandırmanıza izin verir. Bu ayarlar, işlevleri yerel olarak çalıştırırken ve bunları Azure 'da çalıştırırken geçerlidir. Daha fazla bilgi için bkz. [host.jsbaşvurusu](functions-host-json.md).

* **local.settings.js**:, işlevleri yerel olarak çalıştırırken kullanılan ayarları korur. Bu ayarlar yalnızca işlevleri yerel olarak çalıştırdığınızda kullanılır. Daha fazla bilgi için bkz. [yerel ayarlar dosyası](#local-settings-file).

    >[!IMPORTANT]
    >Dosyadaki local.settings.jsgizli dizileri içerebildiğinden, bunu proje kaynak denetiinizden çıkarmanız gerekir.

Dilinize bağlı olarak, bu diğer dosyalar oluşturulur:

# <a name="c"></a>[,\#](#tab/csharp)

* İşlevi uygulayan [Httpexample. cs sınıf kitaplığı dosyası](functions-dotnet-class-library.md#functions-class-library-project) .

# <a name="java"></a>[Java](#tab/java)

+ Proje bağımlılıkları ve [Java sürümü](functions-reference-java.md#java-versions)dahil olmak üzere proje ve dağıtım parametrelerini tanımlayan kök klasörde bir pom.xml dosyası. pom.xml Ayrıca, dağıtım sırasında oluşturulan Azure kaynakları hakkında bilgiler içerir.   

+ Src yolunuzda işlevi uygulayan bir [Functions. Java dosyası](functions-reference-java.md#triggers-and-annotations) .

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Kök klasördeki dosya package.js.

* [Tanım dosyasıfunction.js](functions-reference-node.md#folder-structure) Içeren bir httpexample klasörü ve işlev kodunu içeren bir Node.js dosyası [index.js dosyası](functions-reference-node.md#exporting-a-function).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [Tanım dosyasıfunction.js](functions-reference-powershell.md#folder-structure) Içeren bir httpexample klasörü ve işlev kodunu içeren run.ps1 dosyası.
 
# <a name="python"></a>[Python](#tab/python)
    
* Işlevleri için gereken paketleri listeleyen proje düzeyi requirements.txt dosyası.
    
* [Tanım dosyasıfunction.js](functions-reference-python.md#folder-structure) Içeren bir httpexample klasörü ve \_ \_ \_ \_ işlev kodunu içeren init. Kopyala dosyası.

---

Bu noktada, işlevinizde [giriş ve çıkış bağlamaları ekleyebilirsiniz](#add-input-and-output-bindings) . [Projenize de yeni bir işlev ekleyebilirsiniz](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Bağlama uzantılarını yükleme

HTTP ve Zamanlayıcı Tetikleyicileri dışında, bağlamalar uzantı paketlerinde uygulanır. Gereken Tetikleyiciler ve bağlamalar için uzantı paketlerini yüklemeniz gerekir. Bağlama Uzantıları yükleme işlemi projenizin diline bağlıdır.

# <a name="c"></a>[,\#](#tab/csharp)

Projenizde ihtiyaç duyduğunuz uzantı paketlerini yüklemek için, Terminal penceresinde [DotNet paket Ekle](/dotnet/core/tools/dotnet-add-package) komutunu çalıştırın. Aşağıdaki komut blob, kuyruk ve tablo depolaması için bağlamaları uygulayan Azure Storage uzantısını yüklüyor.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Projenize bir işlev ekleyin

Önceden tanımlanmış Işlevler tetikleyici şablonlarından birini kullanarak var olan bir projeye yeni bir işlev ekleyebilirsiniz. Yeni bir işlev tetikleyicisi eklemek için F1 ' i seçerek komut paletini açın ve ardından **Azure işlevleri: oluşturma işlevi** komutunu arayıp çalıştırın. Tetikleyici türünü seçmek ve tetikleyicinin gerekli özniteliklerini tanımlamak için istemleri izleyin. Tetikleyiciniz bir hizmete bağlanmak için bir erişim anahtarı veya bağlantı dizesi gerektiriyorsa, işlev tetikleyicisi oluşturmadan önce bu uygulamayı hazırlayın.

Bu eylemin sonuçları projenizin diline bağlıdır:

# <a name="c"></a>[,\#](#tab/csharp)

Projenize yeni bir C# sınıf kitaplığı (. cs) dosyası eklenir.

# <a name="java"></a>[Java](#tab/java)

Projenize yeni bir Java (. Java) dosyası eklenir.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Projede yeni bir klasör oluşturulur. Klasör yeni bir function.jsdosya ve yeni JavaScript kod dosyası içerir.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Projede yeni bir klasör oluşturulur. Klasörde yeni bir function.jsdosya ve yeni PowerShell kod dosyası yer alır.

# <a name="python"></a>[Python](#tab/python)

Projede yeni bir klasör oluşturulur. Klasör yeni bir function.jsdosya ve yeni Python kod dosyası içerir.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Hizmetlere bağlanma

Giriş ve çıkış bağlamaları ekleyerek işlevinizi diğer Azure hizmetlerine bağlayabilirsiniz. Bağlamalar, bağlantı kodunu yazmak zorunda kalmadan işlevinizi diğer hizmetlere bağlar. Bağlama ekleme işlemi projenizin diline bağlıdır. Bağlamalar hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

Aşağıdaki örneklerde `outqueue` , depolama hesabının bağlantı dizesinin `MyStorageConnection` local.settings.jsüzerindeki uygulama ayarında ayarlandığı adlı bir depolama kuyruğuna bağlanır.

# <a name="c"></a>[,\#](#tab/csharp)

Yöntem tanımına aşağıdaki parametreyi eklemek için işlev yöntemini güncelleştirin `Run` :

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg`Parametresi `ICollector<T>` , işlev tamamlandığında çıkış bağlamaya yazılan bir ileti koleksiyonunu temsil eden bir türdür. Aşağıdaki kod, koleksiyona bir ileti ekler:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 İşlev tamamlandığında iletiler kuyruğa gönderilir.

Daha fazla bilgi edinmek için bkz. [kuyruk depolama çıkış bağlama başvuru makalesi](functions-bindings-storage-queue-output.md?tabs=csharp) belgeleri. Bir işleve hangi bağlamaların eklenebileceği hakkında genel bilgi edinmek için bkz. [Azure işlevlerinde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Yöntem tanımına aşağıdaki parametreyi eklemek için işlev yöntemini güncelleştirin `Run` :

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg`Parametresi bir `OutputBinding<T>` türdür, burada, `T` işlev tamamlandığında çıkış bağlamasının yazıldığı bir dizedir. Aşağıdaki kod, çıkış bağlamasındaki iletiyi ayarlar:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Bu ileti, işlev tamamlandığında kuyruğa gönderilir.

Daha fazla bilgi edinmek için bkz. [kuyruk depolama çıkış bağlama başvuru makalesi](functions-bindings-storage-queue-output.md?tabs=java) belgeleri. Bir işleve hangi bağlamaların eklenebileceği hakkında genel bilgi edinmek için bkz. [Azure işlevlerinde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

İşlev kodunuzda, `msg` `context` Bu örnekte olduğu gibi bağlama öğesinden öğesine erişilir:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Bu ileti, işlev tamamlandığında kuyruğa gönderilir.

Daha fazla bilgi edinmek için bkz. [kuyruk depolama çıkış bağlama başvuru makalesi](functions-bindings-storage-queue-output.md?tabs=javascript) belgeleri. Bir işleve hangi bağlamaların eklenebileceği hakkında genel bilgi edinmek için bkz. [Azure işlevlerinde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Bu ileti, işlev tamamlandığında kuyruğa gönderilir.

Daha fazla bilgi edinmek için bkz. [kuyruk depolama çıkış bağlama başvuru makalesi](functions-bindings-storage-queue-output.md?tabs=powershell) belgeleri. Bir işleve hangi bağlamaların eklenebileceği hakkında genel bilgi edinmek için bkz. [Azure işlevlerinde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Tanımı `Main` `msg: func.Out[func.QueueMessage]` Aşağıdaki örnekteki gibi görünmesi için bir çıkış parametresi eklemek üzere tanımı güncelleştirin:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Aşağıdaki kod, istekten çıkış kuyruğuna dize verileri ekler:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Bu ileti, işlev tamamlandığında kuyruğa gönderilir.

Daha fazla bilgi edinmek için bkz. [kuyruk depolama çıkış bağlama başvuru makalesi](functions-bindings-storage-queue-output.md?tabs=python) belgeleri. Bir işleve hangi bağlamaların eklenebileceği hakkında genel bilgi edinmek için bkz. [Azure işlevlerinde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Azure’da Yayımlama

Visual Studio Code, Işlevler projenizi doğrudan Azure 'da yayımlamanıza olanak sağlar. Süreç kapsamında, Azure abonelik bir işlev uygulaması ve ilgili kaynakları oluşturursunuz. İşlev uygulaması, işlevlerinize ilişkin bir yürütme bağlamı sağlar. Proje, Azure aboneliğinizdeki yeni işlev uygulamasında paketlenir ve dağıtılır.

Azure 'daki yeni bir işlev uygulamasına Visual Studio Code yayımladığınızda, varsayılan olarak bir hızlı işlev uygulaması oluşturma yolu veya oluşturulan uzak kaynaklar üzerinde daha fazla denetiminiz olan gelişmiş bir yol seçebilirsiniz. 

Visual Studio Code yayımladığınızda, [ZIP dağıtım](functions-deployment-technologies.md#zip-deploy) teknolojisinin avantajlarından yararlanabilirsiniz. 

### <a name="quick-function-app-create"></a>Hızlı işlev uygulaması oluşturma

**Azure 'da yeni işlev uygulaması oluştur**' u seçtiğinizde, uzantı, işlev uygulamanız tarafından gerek duyulan Azure kaynakları için otomatik olarak değerler üretir. Bu değerler, seçtiğiniz işlev uygulaması adına göre yapılır. Projenizi Azure 'da yeni bir işlev uygulamasına yayımlamak için varsayılanlar kullanmanın bir örneği için [Visual Studio Code hızlı başlangıç makalesi](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)' ne bakın.

Oluşturulan kaynaklar için açık adlar sağlamak istiyorsanız, gelişmiş oluşturma yolunu seçmeniz gerekir.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Gelişmiş seçenekleri kullanarak bir projeyi Azure 'da yeni bir işlev uygulamasına yayımlama

Aşağıdaki adımlar, projenizi gelişmiş oluşturma seçenekleriyle oluşturulan yeni bir işlev uygulamasına yayımlar:

1. Komut paleti ' nde **Azure işlevleri: işlev uygulamasına dağıt**' ı girin.

1. Oturum açmadıysanız **Azure 'Da oturum açmanız** istenir. Ayrıca, **ücretsiz bir Azure hesabı da oluşturabilirsiniz**. Tarayıcıdan oturum açtıktan sonra Visual Studio Code bölümüne geri dönün.

1. Birden çok aboneliğiniz varsa, işlev uygulaması için **bir abonelik seçin** ve sonra da **Azure 'Da yeni işlev uygulaması oluştur ' u seçin... _Gelişmiş_**. Bu _Gelişmiş_ seçenek, Azure 'da oluşturduğunuz kaynaklar üzerinde daha fazla denetim sağlar. 

1. İstemlerin ardından, şu bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | Azure 'da işlev uygulaması seçin | Azure 'da yeni İşlev Uygulaması oluşturma | Sonraki istem sırasında, yeni işlev uygulamanızı tanımlayan bir genel benzersiz ad yazın ve ardından ENTER ' u seçin. İşlev uygulaması adına ilişkin geçerli karakterler `a-z`, `0-9` ve `-` işaretidir. |
    | Bir işletim sistemi seçin | Windows | İşlev uygulaması Windows üzerinde çalışır. |
    | Barındırma planı seçin | Tüketim planı | Sunucusuz [Tüketim planı barındırma](consumption-plan.md) kullanılır. |
    | Yeni uygulamanız için bir çalışma zamanı seçin | Proje diliniz | Çalışma zamanının yayımlamakta olduğunuz projeyle eşleşmesi gerekir. |
    | Yeni kaynaklar için bir kaynak grubu seçin | Yeni kaynak grubu oluştur | Sonraki istem sırasında, gibi bir kaynak grubu adı yazın `myResourceGroup` ve ardından ENTER ' u seçin. Ayrıca, var olan bir kaynak grubunu seçebilirsiniz. |
    | Bir depolama hesabı seçin | Yeni depolama hesabı oluşturma | Sonraki istem sırasında, işlev uygulamanız tarafından kullanılan yeni depolama hesabı için genel olarak benzersiz bir ad yazın ve ENTER ' u seçin. Depolama hesabı adları 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir. Ayrıca var olan bir hesabı da seçebilirsiniz. |
    | Yeni kaynaklar için bir konum seçin | region | Size yakın bir [bölgede](https://azure.microsoft.com/regions/) veya işlevlerinizin erişebileceği diğer hizmetlere yakın bir konum seçin. |

    İşlev uygulamanız oluşturulduktan ve dağıtım paketi uygulandıktan sonra bir bildirim görüntülenir. Oluşturduğunuz Azure kaynakları dahil olmak üzere oluşturma ve dağıtım sonuçlarını görüntülemek için bu bildirimde **çıktıyı görüntüle** ' yi seçin.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Azure 'da HTTP ile tetiklenen bir işlevin URL 'sini alın

İstemciden HTTP ile tetiklenen bir işlev çağırmak için işlev uygulamanıza dağıtıldığında işlevin URL 'sine ihtiyacınız vardır. Bu URL, gerekli işlev anahtarlarını içerir. Bu URL 'Leri dağıtılan işlevleriniz için almak üzere uzantısını kullanabilirsiniz. Yalnızca uzak işlevi Azure 'da çalıştırmak istiyorsanız, uzantının [Şimdi Çalıştır işlevini kullanın](#run-functions-in-azure) .

1. F1 ' i seçerek komut paletini açın ve ardından **Azure işlevleri: kopyalama işlev URL 'si** komutunu arayıp çalıştırın.

1. Azure 'da işlev uygulamanızı ve ardından çağırmak istediğiniz belirli HTTP tetikleyicisini seçmek için istemleri izleyin.

İşlev URL 'SI panoya kopyalanır ve sorgu parametresi tarafından geçirilen gerekli anahtarlarla birlikte `code` . POST istekleri göndermek için bir HTTP aracı veya uzak işleve GET istekleri için bir tarayıcı kullanın.  

Azure 'daki işlevlerin URL 'SI alınırken, uzantı, işlevi başlatmak için gereken anahtarları otomatik olarak almak için Azure hesabınızı kullanır. [İşlev erişim anahtarları hakkında daha fazla bilgi edinin](security-concepts.md#function-access-keys). HTTP dışı tetiklenen işlevlerin başlatılması için yönetici anahtarının kullanılması gerekir.

## <a name="republish-project-files"></a>Proje dosyalarını yeniden Yayımla

[Sürekli dağıtımı](functions-continuous-deployment.md)ayarlarken, bağlantılı kaynak konumundaki kaynak dosyaları güncelleştirdiğinizde Azure 'daki işlev uygulamanız güncelleştirilir. Sürekli dağıtım yapmanızı öneririz, ancak Visual Studio Code ' den proje dosya güncelleştirmelerinizi yeniden yayımlayabilirsiniz.

> [!IMPORTANT]
> Varolan bir işlev uygulamasına yayımladığınızda Azure’daki uygulamanın içeriğinin üzerine yazılır.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Çalıştırma işlevleri

Azure Işlevleri uzantısı, projenizdeki her bir işlevi yerel geliştirme bilgisayarınızda veya Azure aboneliğinizde çalıştırmanıza olanak sağlar. 

HTTP tetikleyici işlevleri için, uzantı HTTP uç noktasını çağırır. Diğer tetikleyici türleri için, işlevi başlatmak üzere yönetici API 'Lerini çağırır. İşleve gönderilen isteğin ileti gövdesi, tetikleyicinin türüne bağlıdır. Bir tetikleyici test verileri gerektirdiğinde, verileri belirli bir JSON biçiminde girmeniz istenir.

### <a name="run-functions-in-azure"></a>Azure 'da işlevleri çalıştırma

Visual Studio Code Azure 'da bir işlevi yürütmek için. 

1. Komut paleti ' nde **Azure işlevleri: Execute işlevini şimdi** girin ve Azure aboneliğinizi seçin. 

1. Listeden Azure 'da işlev uygulamanızı seçin. İşlev uygulamanızı görmüyorsanız, doğru abonelikte oturum açtığınızdan emin olun. 

1. Listeden çalıştırmak istediğiniz işlevi seçin ve isteğin ileti gövdesini **ENTER istek gövdesine** yazın. Bu istek iletisini işlevinizde göndermek için ENTER tuşuna basın. **ENTER istek gövdesinde** varsayılan metin, gövdenin biçimini göstermelidir. İşlev uygulamanızın işlevleri yoksa, bu hatayla birlikte bir bildirim hatası gösterilir. 

1. İşlev Azure 'da yürütüldüğünde ve bir yanıt döndürdüğünde, Visual Studio Code bir bildirim oluşturulur.
 
İşlevinizi **Azure: Functions** (Mac üzerinde CTRL) ' e sağ tıklayarak ve işlev uygulamanızdan çalıştırmak Istediğiniz işlevi Azure aboneliğinizde çalıştırın ve **Şimdi Çalıştır işlevi ' ni seçebilirsiniz...**.

Azure 'da işlevleri çalıştırırken, uzantı, işlevi başlatmak için gereken anahtarları otomatik olarak almak için Azure hesabınızı kullanır. [İşlev erişim anahtarları hakkında daha fazla bilgi edinin](security-concepts.md#function-access-keys). HTTP dışı tetiklenen işlevlerin başlatılması için yönetici anahtarının kullanılması gerekir.

### <a name="run-functions-locally"></a>İşlevleri yerel olarak çalıştır

Yerel çalışma zamanı, Azure 'da işlev uygulamanızı barındıran çalışma zamanının aynısıdır. Yerel ayarlar [ dosyadakilocal.settings.js](#local-settings-file)okur. Işlevler projenizi yerel olarak çalıştırmak için [ek gereksinimleri](#run-local-requirements)karşılamanız gerekir.

#### <a name="configure-the-project-to-run-locally"></a>Projeyi yerel olarak çalışacak şekilde yapılandırma

Işlevler çalışma zamanı, HTTP ve Web kancaları dışındaki tüm tetikleyici türleri için dahili olarak bir Azure depolama hesabı kullanır. Bu nedenle, **values. AzureWebJobsStorage** anahtarını geçerli bir Azure depolama hesabı bağlantı dizesine ayarlamanız gerekir.

Bu bölümde, depolama bağlantı dizesine bağlanmak ve bu dizeyi almak için [Azure Depolama Gezgini](https://storageexplorer.com/) olan [Visual Studio Code için Azure Storage uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) kullanılmaktadır.

Depolama hesabı bağlantı dizesini ayarlamak için:

1. Visual Studio 'da **Cloud Explorer**' ı açın, **depolama hesabı ' nı genişletin**  >  ve ardından **Özellikler** ' i seçin ve **birincil bağlantı dizesi** değerini kopyalayın.

2. Projenizde local.settings.jsdosya üzerinde açın ve **AzureWebJobsStorage** anahtarının değerini kopyaladığınız bağlantı dizesi olarak ayarlayın.

3. İşlevlerinizin gerektirdiği diğer tüm bağlantılar için **Values** dizisine benzersiz anahtarlar eklemek için önceki adımı tekrarlayın.

Daha fazla bilgi için bkz. [yerel ayarlar dosyası](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>İşlevlerde yerel olarak hata ayıklama  

İşlevlerinizi hata ayıklamak için F5 ' i seçin. [Azure Functions Core Tools] [temel araçları]henüz indirmediyseniz bunu yapmanız istenir. Temel araçlar yüklenip çalıştırıldığında, çıkış terminalde görüntülenir. Bu, `func host start` terminalden çekirdek araçları komutunu çalıştırmayla aynıdır, ancak ek derleme görevleri ve ekli bir hata ayıklayıcı ile aynıdır.  

Proje çalışırken, işlevlerinizi proje Azure 'a dağıtıldığında yaptığınız gibi tetikleyebilmeniz için uzantının **Şimdi Çalıştır...** özelliğini kullanabilirsiniz. Proje hata ayıklama modunda çalışırken, beklenirken Visual Studio Code kesme noktaları isabet edilir. 

1. Komut paleti ' nde **Azure işlevleri: Execute işlevini şimdi** girin ve **Yerel proje**' yi seçin. 

1. Projenizde çalıştırmak istediğiniz işlevi seçin ve isteğin ileti gövdesini **ENTER istek gövdesine** yazın. Bu istek iletisini işlevinizde göndermek için ENTER tuşuna basın. **ENTER istek gövdesinde** varsayılan metin, gövdenin biçimini göstermelidir. İşlev uygulamanızın işlevleri yoksa, bu hatayla birlikte bir bildirim hatası gösterilir. 

1. İşlev yerel olarak çalıştırıldığında ve yanıt alındıktan sonra, Visual Studio Code bir bildirim oluşturulur. İşlev yürütme hakkında bilgi, **Terminal** panelinde gösterilir.

İşlevleri yerel olarak çalıştırma, anahtar kullanmayı gerektirmez. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Varsayılan olarak, bu ayarlar proje Azure 'da yayımlandığında otomatik olarak geçirilmez. Yayımlama bittikten sonra, Azure 'daki işlev uygulamanıza local.settings.jsayarları yayımlama seçeneği sunulur. Daha fazla bilgi için bkz.  [uygulama ayarlarını yayımlama](#publish-application-settings).

**ConnectionString** içindeki değerler hiçbir şekilde yayımlanmaz.

İşlev uygulama ayarları değerleri, kodunuzda ortam değişkenleri olarak da okunabilir. Daha fazla bilgi için, bu dile özgü başvuru makalelerinin ortam değişkenleri bölümlerine bakın:

* [C# önceden derlenmiş](functions-dotnet-class-library.md#environment-variables)
* [C# betiği (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure 'da uygulama ayarları

Projenizdeki local.settings.jsdosyadaki ayarlar, Azure 'daki işlev uygulamasındaki uygulama ayarları ile aynı olmalıdır. local.settings.jsiçin eklediğiniz tüm ayarlar, Azure 'daki işlev uygulamasına da eklemeniz gerekir. Projeyi yayımladığınızda bu ayarlar otomatik olarak karşıya yüklenemez. Benzer şekilde, [portalda](functions-how-to-use-azure-function-app-settings.md#settings) işlev uygulamanızda oluşturduğunuz tüm ayarlar yerel projenize indirilmelidir.

### <a name="publish-application-settings"></a>Uygulama ayarlarını Yayımla

Azure 'daki işlev uygulamanıza gereken ayarları yayımlamanın en kolay yolu, projenizi yayımladıktan sonra görüntülenen **karşıya yükleme ayarları** bağlantısını kullanmaktır:

![Uygulama ayarlarını karşıya yükle](./media/functions-develop-vs-code/upload-app-settings.png)

Ayrıca, komut paletindeki **Azure işlevleri: yerel ayarı yükle** komutunu kullanarak ayarları yayımlayabilirsiniz. Azure **işlevleri: yeni ayar Ekle** komutunu kullanarak Azure 'daki uygulama ayarlarına ayrı ayarlar ekleyebilirsiniz.

> [!TIP]
> Yayımlamadan önce local.settings.jsdosyayı kaydettiğinizden emin olun.

Yerel dosya şifrelendiyse, şifresi çözülür, yayımlanır ve yeniden şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir.

Aboneliğinizi, işlev uygulamanızı ve **uygulama ayarlarınızı** genişleterek **Azure: Functions** alanındaki mevcut uygulama ayarlarını görüntüleyin.

![İşlev uygulaması ayarlarını Visual Studio Code görüntüle](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Azure 'dan ayarları indirin

Azure 'da uygulama ayarları oluşturduysanız **Azure işlevleri: uzak ayarları indir** komutunu kullanarak bunları dosya local.settings.jsyükleyebilirsiniz.

Karşıya yüklenirken olduğu gibi, yerel dosya şifrelenirse, yeniden çözülür, güncelleştirilir ve şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir.

## <a name="monitoring-functions"></a>İzleme işlevleri

[İşlevleri yerel olarak çalıştırdığınızda](#run-functions-locally), günlük verileri Terminal konsoluna akışla kaydedilir. Ayrıca, Işlevler projeniz Azure 'daki bir işlev uygulamasında çalışırken günlük verilerini de alabilirsiniz. Neredeyse gerçek zamanlı günlük verilerini görmek için Azure 'daki akış günlüklerine bağlanabilir ya da işlev uygulamanızın nasıl davrandığının daha ayrıntılı bir şekilde anlayabilmesi için Application Insights etkinleştirebilirsiniz.

### <a name="streaming-logs"></a>Akış günlükleri

Bir uygulama geliştirirken genellikle günlüğe kaydetme bilgilerini neredeyse gerçek zamanlı olarak görmeniz yararlı olur. İşlevleriniz tarafından oluşturulan günlük dosyalarının akışını görüntüleyebilirsiniz. Bu çıktı, HTTP ile tetiklenen bir işleve yönelik bir istek için akış günlüğü örneği örneğidir:

![HTTP tetikleyicisi için akış günlüğü çıkışı](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Daha fazla bilgi için bkz. [akış günlükleri](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Akış günlükleri, Işlevlerin yalnızca tek bir örneğini destekler. İşleviniz birden çok örneğe ölçeklenirse, diğer örneklerden alınan veriler günlük akışında gösterilmez. Application Insights [canlı ölçüm akışı](../azure-monitor/app/live-stream.md) birden fazla örneği destekler. Ayrıca, neredeyse gerçek zamanlı olarak, Akış Analizi [örneklenmiş verileri](configure-monitoring.md#configure-sampling)temel alır.

### <a name="application-insights"></a>Application Insights

İşlev uygulamanızı Application Insights ile tümleştirerek işlevlerinizin yürütülmesini izlemenizi öneririz. Azure portal bir işlev uygulaması oluşturduğunuzda, bu tümleştirme varsayılan olarak oluşur. Visual Studio yayımlama sırasında işlev uygulamanızı oluşturduğunuzda Application Insights kendiniz tümleştirmeniz gerekir. Nasıl yapılacağını öğrenmek için bkz. [Application Insights Tümleştirmesini Etkinleştirme](configure-monitoring.md#enable-application-insights-integration).

Application Insights kullanarak izleme hakkında daha fazla bilgi edinmek için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

## <a name="c-script-projects"></a>C \# betik projeleri

Varsayılan olarak, tüm C# projeleri [C# derlenmiş sınıf kitaplığı projeleri](functions-dotnet-class-library.md)olarak oluşturulur. Bunun yerine C# betik projeleriyle çalışmayı tercih ediyorsanız, Azure Işlevleri uzantısı ayarlarında varsayılan dil olarak C# betiğini seçmeniz gerekir:

1. **Dosya**  >  **tercihleri**  >  **ayarları**' nı seçin.

1. **Kullanıcı ayarları**  >  **uzantıları**  >  **Azure işlevleri**' ne gidin.

1. Azure Işlevinden **C # betiği** seçin **: proje dili**.

Bu adımları tamamladıktan sonra, temel alınan temel araçlara yapılan çağrılar `--csx` C# betiği (. CSX) proje dosyalarını oluşturan ve yayımlayan seçeneğini içerir. Bu varsayılan dili belirttiğinizde, varsayılan olarak C# betik projelerinde oluşturduğunuz tüm projeler. Varsayılan değer ayarlandığında bir proje dili seçmeniz istenmez. Diğer dillerde proje oluşturmak için bu ayarı değiştirmeniz veya dosyadaki Kullanıcı settings.jskaldırmanız gerekir. Bu ayarı kaldırdıktan sonra bir proje oluşturduğunuzda dilinizi seçmeniz istenir.

## <a name="command-palette-reference"></a>Komut paleti başvurusu

Azure Işlevleri uzantısı, Azure 'daki işlev uygulamalarınızla etkileşim kurmak için alanında yararlı bir grafik arabirimi sağlar. Aynı işlevsellik, komut paletinde (F1) komutlar olarak da kullanılabilir. Bu Azure Işlevleri komutları kullanılabilir:

|Azure Işlevleri komutu  | Description  |
|---------|---------|
|**Yeni ayarlar Ekle**  |  Azure 'da yeni bir uygulama ayarı oluşturur. Daha fazla bilgi için bkz. [uygulama ayarlarını yayımlama](#publish-application-settings). Ayrıca, [Bu ayarı yerel ayarlarınıza indirmeniz](#download-settings-from-azure)gerekebilir. |
| **Dağıtım kaynağını Yapılandır** | İşlev uygulamanızı Azure 'da yerel bir git deposuna bağlar. Daha fazla bilgi için bkz. [Azure işlevleri Için sürekli dağıtım](functions-continuous-deployment.md). |
| **GitHub deposuna Bağlan** | İşlev uygulamanızı bir GitHub deposuna bağlar. |
| **Işlev URL 'sini Kopyala** | Azure 'da çalışan HTTP ile tetiklenen bir işlevin uzak URL 'sini alır. Daha fazla bilgi için bkz. [dağıtılan IŞLEVIN URL 'Sini alma](#get-the-url-of-the-deployed-function). |
| **Azure 'da işlev uygulaması oluşturma** | Azure 'da aboneliğinizde yeni bir işlev uygulaması oluşturur. Daha fazla bilgi edinmek için bkz. [Azure 'da yeni bir işlev uygulamasına yayımlama](#publish-to-azure)hakkında bölümüne bakın.        |
| **Ayarları çöz** | Azure Işlevleri tarafından şifrelenmiş [yerel ayarların](#local-settings-file) şifrelerini çözer **: şifreleme ayarları**.  |
| **İşlev Uygulaması Sil** | Azure 'daki aboneliğinizden bir işlev uygulamasını kaldırır. App Service planında başka uygulama yoksa, bunu silme seçeneği sunulur. Depolama hesapları ve kaynak grupları gibi diğer kaynaklar silinmez. Tüm kaynakları kaldırmak için, bunun yerine [kaynak grubunu silmelisiniz](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Yerel projeniz etkilenmez. |
|**Delete Işlevi**  | Azure 'daki bir işlev uygulamasından mevcut bir işlevi kaldırır. Bu silme işlemi yerel projenizi etkilemediğinden, bunun yerine işlevi yerel olarak kaldırıp [projenizi yeniden yayımönünde](#republish-project-files)bulundurun. |
| **Ara sunucuyu Sil** | Azure Işlevleri proxy 'sini Azure 'daki işlev uygulamanızdan kaldırır. Proxy 'ler hakkında daha fazla bilgi için bkz. [Azure işlev proxy'leri Ile çalışma](functions-proxies.md). |
| **Ayarı Sil** | Azure 'da bir işlev uygulaması ayarını siler. Bu silme işlemi dosyadaki local.settings.jsayarları etkilemez. |
| **Deponun bağlantısını kes**  | Azure 'daki bir işlev uygulaması ve bir kaynak denetimi deposu arasındaki [sürekli dağıtım](functions-continuous-deployment.md) bağlantısını kaldırır. |
| **Uzak ayarları indir** | Azure 'daki seçili işlev uygulamasından gelen ayarları local.settings.jsdosyasına indirir. Yerel dosya şifrelendiyse, şifresi çözülür, güncelleştirilir ve yeniden şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir. Bu komutu çalıştırmadan önce dosyadaki değişiklikleri local.settings.jskaydettiğinizden emin olun. |
| **Ayarları Düzenle** | Azure 'da var olan bir işlev uygulaması ayarının değerini değiştirir. Bu komut, local.settings.jsdosyadaki ayarları etkilemez.  |
| **Şifreleme ayarları** | `Values` [Yerel ayarlarda](#local-settings-file)dizideki tek tek öğeleri şifreler. Bu dosyada Ayrıca, `IsEncrypted` `true` yerel çalışma zamanının kullanılmadan önce ayarların şifresini çözmesini belirten olarak ayarlanır. Değerli bilgilerin sızması riskini azaltmak için yerel ayarları şifreleyin. Azure 'da uygulama ayarları her zaman şifreli olarak depolanır. |
| **Işlevi Şimdi Çalıştır** | Yönetici API 'Leri kullanarak bir işlevi el ile başlatır. Bu komut, hata ayıklama sırasında ve Azure 'da çalışan işlevlerde yerel olarak test etmek için kullanılır. Azure 'da bir işlevi tetiklerken, uzantı ilk olarak, Azure 'da işlevleri Başlatan uzak yönetici API 'Lerini çağırmak için kullandığı bir yönetici anahtarını otomatik olarak alır. API 'ye gönderilen iletinin gövdesi tetikleyicinin türüne bağlıdır. Zamanlayıcı Tetikleyicileri, herhangi bir veri geçirmenize gerek kalmaz. |
| **Projeyi VS Code ile kullanım için Başlat** | Gerekli Visual Studio Code proje dosyalarını var olan Işlevler projesine ekler. Temel araçları kullanarak oluşturduğunuz bir projeyle çalışmak için bu komutu kullanın. |
| **Azure Functions Core Tools yüklemeyi veya güncelleştirmeyi** | İşlevleri yerel olarak çalıştırmak için kullanılan [Azure Functions Core Tools]yüklenir veya güncelleştirir. |
| **Yeniden dağıtım**  | Bağlı bir git deposundan proje dosyalarını Azure 'daki belirli bir dağıtıma yeniden dağıtmanıza olanak tanır. Visual Studio Code yerel güncelleştirmeleri yeniden yayımlamak için [projenizi yeniden yayımlayın](#republish-project-files). |
| **Ayarları yeniden adlandır** | Azure 'da var olan bir işlev uygulaması ayarının anahtar adını değiştirir. Bu komut, local.settings.jsdosyadaki ayarları etkilemez. Azure 'daki ayarları yeniden adlandırdıktan sonra, [Bu değişiklikleri yerel projeye indirmeniz](#download-settings-from-azure)gerekir. |
| **Yeniden başlat** | İşlev uygulamasını Azure 'da yeniden başlatır. Güncelleştirmelerin dağıtımı, işlev uygulamasını da yeniden başlatır. |
| **AzureWebJobsStorage ayarla**| `AzureWebJobsStorage`Uygulama ayarının değerini ayarlar. Bu ayar, Azure Işlevleri için gereklidir. Azure 'da bir işlev uygulaması oluşturulduğunda ayarlanır. |
| **Başlangıç** | Azure 'da durdurulmuş bir işlev uygulaması başlatır. |
| **Akış günlüklerini başlatma** | Azure 'da işlev uygulaması için akış günlüklerini başlatır. Günlük bilgilerini neredeyse gerçek zamanlı olarak görmeniz gerekiyorsa Azure 'da uzaktan sorun giderme sırasında akış günlüklerini kullanın. Daha fazla bilgi için bkz. [akış günlükleri](#streaming-logs). |
| **Durdur** | Azure 'da çalışan bir işlev uygulamasını sonlandırır. |
| **Akış günlüklerini durdur** | Azure 'da işlev uygulaması için akış günlüklerini sonlandırır. |
| **Yuva ayarı olarak değiştirme** | Etkinleştirildiğinde, belirli bir dağıtım yuvası için bir uygulama ayarının devam ettiğinden emin olur. |
| **Azure Functions Core Tools kaldır** | Uzantısının gerektirdiği Azure Functions Core Tools kaldırır. |
| **Yerel ayarları karşıya yükle** | local.settings.jsdosyadaki ayarları Azure 'daki seçili işlev uygulamasına yükler. Yerel dosya şifrelendiyse, dosyanın şifresi çözülür, karşıya yüklenir ve yeniden şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir. Bu komutu çalıştırmadan önce dosyadaki değişiklikleri local.settings.jskaydettiğinizden emin olun. |
| **GitHub 'da yürütmeyi görüntüle** | İşlev uygulamanız depoya bağlıyken belirli bir dağıtımda en son yürütmeyi gösterir. |
| **Dağıtım günlüklerini görüntüle** | Azure 'daki işlev uygulamasına belirli bir dağıtımın günlüklerini gösterir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure Functions Core Tools hakkında daha fazla bilgi için bkz. [Azure Functions Core Tools Ile çalışma](functions-run-local.md).

.NET sınıf kitaplıkları olarak işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Azure Işlevleri C# Geliştirici başvurusu](functions-dotnet-class-library.md). Bu makalede ayrıca, Azure Işlevleri tarafından desteklenen çeşitli tür bağlamaları bildirmek üzere özniteliklerin nasıl kullanılacağına dair örneklere bağlantılar sağlanır.

[Visual Studio Code için Azure İşlevleri uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md