---
title: Visual Studio Code kullanarak Azure Işlevleri geliştirin | Microsoft Docs
description: Visual Studio Code için Azure Işlevleri uzantısını kullanarak Azure Işlevleri geliştirmeyi ve test yapmayı öğrenin.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: glenga
ms.openlocfilehash: 5aac99dbeb6ebd7c0b131df5027daa352c30f3bd
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082794"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Visual Studio Code kullanarak Azure Işlevleri geliştirme

[Visual Studio Code Için Azure işlevleri uzantısı] , işlevleri yerel olarak geliştirmenize ve bunları Azure 'a dağıtmanıza imkan tanır. Azure Işlevleri ile ilk kez bu deneyim varsa, [Azure işlevleri 'ne giriş](functions-overview.md)konusunda daha fazla bilgi edinebilirsiniz.

Azure Işlevleri uzantısı şu avantajları sağlar:

* Yerel geliştirme bilgisayarınızda işlevleri düzenleyin, derleyin ve çalıştırın.
* Azure Işlevleri projenizi doğrudan Azure 'da yayımlayın.
* Visual Studio Code avantajlarından yararlanarak işlevlerinizi çeşitli dillerde yazın.

Uzantı, Azure Işlevleri sürüm 2. x çalışma zamanı tarafından desteklenen aşağıdaki dillerle kullanılabilir:

* [C#Medi](functions-dotnet-class-library.md)
* <sup></sup> [ C# betik](functions-reference-csharp.md)*
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup> [Komut dosyasını varsayılan proje C# diliniz olarak ayarlamanızı](#c-script-projects)gerektirir.

Bu makalede, örnekler şu anda yalnızca JavaScript (node. js) ve C# sınıf kitaplığı işlevleri için kullanılabilir.  

Bu makalede, işlevleri geliştirmek ve Azure 'da yayımlamak için Azure Işlevleri uzantısının nasıl kullanılacağına ilişkin ayrıntılar sağlanmaktadır. Bu makaleyi okuduktan sonra, [Visual Studio Code kullanarak ilk işlevinizi oluşturmanız](functions-create-first-function-vs-code.md)gerekir.

> [!IMPORTANT]
> Tek bir işlev uygulaması için yerel geliştirme ve Portal geliştirmeyi karışmayın. Yerel bir projeden bir işlev uygulamasına yayımladığınızda, dağıtım işlemi portalda geliştirdiğiniz işlevlerin üzerine yazar.

## <a name="prerequisites"></a>Önkoşullar

Visual Studio Code için [Azure işlevleri uzantısı][visual studio code için azure işlevleri uzantısı]yükleyip çalıştırmadan önce, şu gereksinimleri karşılamanız gerekir:

* [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms)birine [Visual Studio Code](https://code.visualstudio.com/) .

* Etkin bir Azure aboneliği.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Azure depolama hesabı gibi ihtiyacınız olan diğer kaynaklar, [Visual Studio Code kullanarak yayımladığınızda](#publish-to-azure)aboneliğinizde oluşturulur.

> [!IMPORTANT]
> Yerel olarak başlatıp çalıştırmak zorunda kalmadan, işlevleri yerel olarak geliştirebilir ve Azure 'da yayımlayabilirsiniz. İşlevlerinizi yerel olarak çalıştırmak için Azure Functions Core Tools otomatik olarak indirilmesi dahil bazı ek gereksinimleri karşılamanız gerekir. Daha fazla bilgi edinmek için bkz. [projeyi yerel olarak çalıştırmaya yönelik ek gereksinimler](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure İşlevleri projesi oluşturma

Işlevler uzantısı, ilk işlevinizle birlikte bir işlev uygulaması projesi oluşturmanızı sağlar. Aşağıdaki adımlarda, yeni Işlevler projesinde HTTP ile tetiklenen bir işlevin nasıl oluşturulacağı gösterilmektedir. [Http tetikleyicisi](functions-bindings-http-webhook.md) , göstermek için en basit işlev tetikleyici şablonudur.

1. **Azure: işlevler**' de **işlev oluştur** simgesini seçin:

    ![İşlev oluşturma](./media/functions-develop-vs-code/create-function.png)

1. İşlev uygulaması projeniz için klasörü seçin ve ardından **işlev projeniz için bir dil seçin**.

1. **Http tetikleyici** işlev şablonunu seçin veya artık işlev olmadan bir proje oluşturmak için **Atla** seçeneğini belirleyebilirsiniz. Projenize daha sonra dilediğiniz zaman [bir işlev ekleyebilirsiniz](#add-a-function-to-your-project) .

    ![HTTP tetikleyicisi şablonunu seçin](./media/functions-develop-vs-code/create-function-choose-template.png)

1. İşlev adı için **Httptrigger** yazın ve ENTER ' ı seçin ve ardından **işlev** yetkilendirmesi ' ni seçin. Bu yetkilendirme düzeyi, işlev uç noktasını çağırdığınızda bir [işlev anahtarı](functions-bindings-http-webhook.md#authorization-keys) sağlamanızı gerektirir.

    ![Işlev yetkilendirmesi seçin](./media/functions-develop-vs-code/create-function-auth.png)

    Seçtiğiniz dilde ve HTTP ile tetiklenen bir işlev için şablonda bir işlev oluşturulur.

    ![Visual Studio Code 'de HTTP ile tetiklenen işlev şablonu](./media/functions-develop-vs-code/new-function-full.png)

Proje şablonu seçtiğiniz dilde bir proje oluşturur ve gerekli bağımlılıkları kurar. Her dil için, yeni proje bu dosyalara sahiptir:

* **Host. JSON**: işlevler konağını yapılandırmanızı sağlar. Bu ayarlar, işlevleri yerel olarak çalıştırırken ve bunları Azure 'da çalıştırırken geçerlidir. Daha fazla bilgi için bkz. [Host. JSON başvurusu](functions-host-json.md).

* **Local. Settings. JSON**: işlevleri yerel olarak çalıştırırken kullanılan ayarları korur. Bu ayarlar yalnızca işlevleri yerel olarak çalıştırdığınızda kullanılır. Daha fazla bilgi için bkz. [yerel ayarlar dosyası](#local-settings-file).

    >[!IMPORTANT]
    >Local. Settings. JSON dosyası gizli dizileri içerebildiğinden, bunu proje kaynak denetiinizden çıkarmanız gerekir.

Bu noktada, [function. json dosyasını değiştirerek](#add-a-function-to-your-project) veya [bir C# sınıf kitaplığı işlevine parametre ekleyerek](#add-a-function-to-your-project)işlevlerinize giriş ve çıkış bağlamaları ekleyebilirsiniz.

[Projenize de yeni bir işlev ekleyebilirsiniz](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Bağlama uzantılarını yükleme

HTTP ve Zamanlayıcı Tetikleyicileri dışında, bağlamalar uzantı paketlerinde uygulanır. Gereken Tetikleyiciler ve bağlamalar için uzantı paketlerini yüklemeniz gerekir. Bağlama Uzantıları yükleme işlemi projenizin diline bağlıdır.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Projenizde ihtiyaç duyduğunuz uzantı paketlerini yüklemek için, Terminal penceresinde [DotNet paket Ekle](/dotnet/core/tools/dotnet-add-package) komutunu çalıştırın. Aşağıdaki komut blob, kuyruk ve tablo depolaması için bağlamaları uygulayan Azure Storage uzantısını yüklüyor.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

---

## <a name="add-a-function-to-your-project"></a>Projenize bir işlev ekleyin

Önceden tanımlanmış Işlevler tetikleyici şablonlarından birini kullanarak var olan bir projeye yeni bir işlev ekleyebilirsiniz. Yeni bir işlev tetikleyicisi eklemek için F1 ' i seçerek komut paletini açın ve ardından **Azure işlevleri: oluşturma işlevi**komutunu arayıp çalıştırın. Tetikleyici türünü seçmek ve tetikleyicinin gerekli özniteliklerini tanımlamak için istemleri izleyin. Tetikleyiciniz bir hizmete bağlanmak için bir erişim anahtarı veya bağlantı dizesi gerektiriyorsa, işlev tetikleyicisi oluşturmadan önce bu uygulamayı hazırlayın.

Bu eylemin sonuçları projenizin diline bağlıdır:

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Projede yeni bir klasör oluşturulur. Klasör yeni bir Function. JSON dosyası ve yeni JavaScript kod dosyası içeriyor.

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Projenize yeni C# bir sınıf kitaplığı (. cs) dosyası eklenir.

---

## <a name="add-input-and-output-bindings"></a>Giriş ve çıkış bağlamaları ekleme

Giriş ve çıkış bağlamaları ekleyerek işlevinizi genişletebilirsiniz. Bağlama ekleme işlemi projenizin diline bağlıdır. Bağlamalar hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

Aşağıdaki örnekler, depolama hesabının bağlantı dizesinin yerel. Settings. JSON içindeki `MyStorageConnection` uygulama ayarında ayarlandığı `outqueue`adlı bir depolama kuyruğuna bağlanır.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Visual Studio Code, uygun bir istem kümesini izleyerek function. JSON dosyanıza bağlama eklemenizi sağlar. Bir bağlama oluşturmak için, işlev klasörünüzdeki **function. JSON** dosyasını sağ tıklatın (MacOS üzerinde CTRL + tıklama) ve **bağlama Ekle**' yi seçin:

![Varolan bir JavaScript işlevine bağlama ekleme ](media/functions-develop-vs-code/function-add-binding.png)

Aşağıda yeni bir depolama çıkış bağlaması tanımlamak için örnek istemler verilmiştir:

| İstem | Değer | Açıklama |
| -------- | ----- | ----------- |
| **Bağlama yönünü seçin** | `out` | Bağlama bir çıkış bağlamadır. |
| **Yön içeren bağlamayı seçin** | `Azure Queue Storage` | Bağlama bir Azure depolama kuyruğu bağlamadır. |
| **Kodunuzda bu bağlamayı tanımlamak için kullanılan ad** | `msg` | Kodunuzda başvurulan bağlama parametresini tanımlayan ad. |
| **İletinin gönderileceği kuyruk** | `outqueue` | Bağlamanın yazdığı kuyruğun adı. *SıraAdı* mevcut olmadığında, bağlama ilk kullanımda oluşturulur. |
| **"Yerel. Setting. JSON" ayarını seçin** | `MyStorageConnection` | Depolama hesabı için bağlantı dizesini içeren bir uygulama ayarının adı. `AzureWebJobsStorage` ayarı, işlev uygulamasıyla oluşturduğunuz depolama hesabı için bağlantı dizesini içerir. |

Bu örnekte, function. JSON dosyanızdaki `bindings` dizisine aşağıdaki bağlama eklenir:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Aynı bağlama tanımını doğrudan işleviniz. json ' a de ekleyebilirsiniz.

İşlev kodunuzda `msg` bağlamaya, bu örnekte olduğu gibi `context`erişilir:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Daha fazla bilgi edinmek için [kuyruk depolama çıkışı bağlama](functions-bindings-storage-queue.md#output---javascript-example) başvurusuna bakın.

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Aşağıdaki parametreyi `Run` yöntemi tanımına eklemek için işlev yöntemini güncelleştirin:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Bu kod aşağıdaki `using` ifadesini eklemenizi gerektirir:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

---

`msg` parametresi, işlev tamamlandığında çıkış bağlamaya yazılan bir ileti koleksiyonunu temsil eden bir `ICollector<T>` türüdür. Koleksiyona bir veya daha fazla ileti eklersiniz. Bu iletiler, işlev tamamlandığında kuyruğa gönderilir.

Daha fazla bilgi için bkz. [kuyruk depolama çıkışı bağlama](functions-bindings-storage-queue.md#output---c-example) belgeleri.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Azure'a Yayımlama

Visual Studio Code, Işlevler projenizi doğrudan Azure 'da yayımlamanıza olanak sağlar. Süreç kapsamında, Azure abonelik bir işlev uygulaması ve ilgili kaynakları oluşturursunuz. İşlev uygulaması, işlevlerinize ilişkin bir yürütme bağlamı sağlar. Proje, Azure aboneliğinizdeki yeni işlev uygulamasında paketlenir ve dağıtılır.

Visual Studio Code 'den Azure 'da yeni bir işlev uygulamasına yayımladığınızda, hem hızlı işlev uygulaması oluşturma yolu hem de gelişmiş yol sunulur. 

Visual Studio Code yayımladığınızda, [ZIP dağıtım](functions-deployment-technologies.md#zip-deploy) teknolojisinin avantajlarından yararlanabilirsiniz. 

### <a name="quick-function-app-create"></a>Hızlı işlev uygulaması oluşturma

**Azure 'da yeni işlev uygulaması oluştur**' u seçtiğinizde, uzantı, işlev uygulamanız tarafından gerek duyulan Azure kaynakları için otomatik olarak değerler üretir. Bu değerler, seçtiğiniz işlev uygulaması adına göre yapılır. Projenizi Azure 'da yeni bir işlev uygulamasına yayımlamak için varsayılanlar kullanmanın bir örneği için [Visual Studio Code hızlı başlangıç makalesi](functions-create-first-function-vs-code.md#publish-the-project-to-azure)' ne bakın.

Oluşturulan kaynaklar için açık adlar sağlamak istiyorsanız, gelişmiş oluşturma yolunu seçmeniz gerekir.

### <a name="enable-publishing-with-advanced-create-options"></a>Gelişmiş seçenekleri kullanarak bir projeyi Azure 'da yeni bir işlev uygulamasına yayımlama

Aşağıdaki adımlar, projenizi gelişmiş oluşturma seçenekleriyle oluşturulan yeni bir işlev uygulamasına yayımlar:

1. **Azure: Functions** alanında **işlev uygulaması dağıt** simgesini seçin.

    ![İşlev uygulaması ayarları](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Oturum açmadıysanız **Azure 'Da oturum açmanız**istenir. Ayrıca, **ücretsiz bir Azure hesabı da oluşturabilirsiniz**. Tarayıcıdan oturum açtıktan sonra Visual Studio Code bölümüne geri dönün.

1. Birden çok aboneliğiniz varsa, işlev uygulaması için **bir abonelik seçin** ve sonra da **Azure 'Da yeni işlev uygulaması oluştur ' u seçin... _Gelişmiş_** . Bu _Gelişmiş_ seçenek, Azure 'da oluşturduğunuz kaynaklar üzerinde daha fazla denetim sağlar. 

1. İstemlerin ardından, şu bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | Azure 'da işlev uygulaması seçin | Azure 'da yeni İşlev Uygulaması oluşturma | Sonraki istem sırasında, yeni işlev uygulamanızı tanımlayan bir genel benzersiz ad yazın ve ardından ENTER ' u seçin. İşlev uygulaması adına ilişkin geçerli karakterler `a-z`, `0-9` ve `-` işaretidir. |
    | Bir işletim sistemi seçin | Windows | İşlev uygulaması Windows üzerinde çalışır. |
    | Barındırma planı seçin | Tüketim planı | Sunucusuz [Tüketim planı barındırma](functions-scale.md#consumption-plan) kullanılır. |
    | Yeni uygulamanız için bir çalışma zamanı seçin | Proje diliniz | Çalışma zamanının yayımlamakta olduğunuz projeyle eşleşmesi gerekir. |
    | Yeni kaynaklar için bir kaynak grubu seçin | Yeni kaynak grubu oluştur | Sonraki istem sırasında, `myResourceGroup`gibi bir kaynak grubu adı yazın ve ardından ENTER ' u seçin. Ayrıca, var olan bir kaynak grubunu seçebilirsiniz. |
    | Bir depolama hesabı seçin | Yeni depolama hesabı oluşturma | Sonraki istem sırasında, işlev uygulamanız tarafından kullanılan yeni depolama hesabı için genel olarak benzersiz bir ad yazın ve ENTER ' u seçin. Depolama hesabı adları 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir. Ayrıca var olan bir hesabı da seçebilirsiniz. |
    | Yeni kaynaklar için bir konum seçin | bölgesinde kullanılamıyor | Size yakın bir [bölgede](https://azure.microsoft.com/regions/) veya işlevlerinizin erişebileceği diğer hizmetlere yakın bir konum seçin. |

    İşlev uygulamanız oluşturulduktan ve dağıtım paketi uygulandıktan sonra bir bildirim görüntülenir. Oluşturduğunuz Azure kaynakları dahil olmak üzere oluşturma ve dağıtım sonuçlarını görüntülemek için bu bildirimde **çıktıyı görüntüle** ' yi seçin.

## <a name="republish-project-files"></a>Proje dosyalarını yeniden Yayımla

[Sürekli dağıtım](functions-continuous-deployment.md)ayarladığınızda, Azure 'daki işlev uygulamanız, kaynak dosyalar bağlı kaynak konumunda güncelleştirildiğinde güncelleştirilir. Sürekli dağıtım yapmanızı öneririz, ancak Visual Studio Code ' den proje dosya güncelleştirmelerinizi yeniden yayımlayabilirsiniz.

> [!IMPORTANT]
> Varolan bir işlev uygulamasına yayımladığınızda Azure’daki uygulamanın içeriğinin üzerine yazılır.

1. Visual Studio Code ' de, F1 ' i seçerek komut paleti ' ni açın. Komut paletinde, **Azure işlevleri: işlev uygulamasına dağıt**' ı arayın ve seçin.

1. Oturum açmadıysanız **Azure 'Da oturum açmanız**istenir. Tarayıcıdan oturum açtıktan sonra Visual Studio Code ' e geri dönün. Birden çok aboneliğiniz varsa, işlev uygulamanızı içeren **bir abonelik seçin** .

1. Azure 'da mevcut işlev uygulamanızı seçin. İşlev uygulamasındaki tüm dosyaların üzerine yazma konusunda uyarı aldığınızda, uyarıyı onaylamak ve devam etmek için **Dağıt** ' ı seçin.

Proje yeniden oluşturulur, yeniden paketlenmiştir ve Azure 'a yüklenir. Mevcut proje yeni paket ile değiştirilmiştir ve işlev uygulaması yeniden başlatılır.

## <a name="get-the-url-of-the-deployed-function"></a>Dağıtılan işlevin URL 'sini al

HTTP ile tetiklenen bir işlevi çağırmak için işlev uygulamanıza dağıtıldığında işlevin URL 'sine ihtiyacınız vardır. Bu URL, gerekli [işlev anahtarlarını](functions-bindings-http-webhook.md#authorization-keys)içerir. Bu URL 'Leri dağıtılan işlevleriniz için almak üzere uzantısını kullanabilirsiniz.

1. F1 ' i seçerek komut paletini açın ve ardından **Azure işlevleri: kopyalama işlev URL 'si**komutunu arayıp çalıştırın.

1. Azure 'da işlev uygulamanızı ve ardından çağırmak istediğiniz belirli HTTP tetikleyicisini seçmek için istemleri izleyin.

İşlev URL 'SI panoya kopyalanır ve `code` sorgu parametresi tarafından geçirilen gerekli anahtarlarla birlikte. POST istekleri göndermek için bir HTTP aracı veya uzak işleve GET istekleri için bir tarayıcı kullanın.  

## <a name="run-functions-locally"></a>İşlevleri yerel olarak çalıştır

Azure Işlevleri uzantısı, yerel geliştirme bilgisayarınızda bir Işlevler projesi çalıştırmanızı sağlar. Yerel çalışma zamanı, Azure 'da işlev uygulamanızı barındıran çalışma zamanının aynısıdır. Yerel ayarlar [yerel. Settings. json dosyasından](#local-settings-file)okunmalıdır.

### <a name="additional-requirements-for-running-a-project-locally"></a>Projeyi yerel olarak çalıştırmaya yönelik ek gereksinimler

Işlevler projenizi yerel olarak çalıştırmak için aşağıdaki ek gereksinimleri karşılamanız gerekir:

* Sürüm 2. x ' i yükler [Azure Functions Core Tools](functions-run-local.md#v2). Temel Araçlar paketi, projeyi yerel olarak başlattığınızda otomatik olarak indirilir ve yüklenir. Temel araçlar tüm Azure Işlevleri çalışma zamanını içerir, bu nedenle indirme ve yükleme biraz zaman alabilir.

* Seçtiğiniz dile özgü gereksinimleri yükleyin:

    | Dil | Gereksinim |
    | -------- | --------- |
    | **C#** | [C#uzantının](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET Core CLI araçları](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Java uzantısı için hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 veya üzeri](https://maven.apache.org/) |
    | **JavaScript** | [Node. js](https://nodejs.org/) <sup>*</sup> |  
    | **Python** | [Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) önerilir|

    <sup>*</sup> Etkin LTS ve bakım LTS sürümleri (8.11.1 ve 10.14.1 önerilir).

### <a name="configure-the-project-to-run-locally"></a>Projeyi yerel olarak çalışacak şekilde yapılandırma

Işlevler çalışma zamanı, HTTP ve Web kancaları dışındaki tüm tetikleyici türleri için dahili olarak bir Azure depolama hesabı kullanır. Bu nedenle, **values. AzureWebJobsStorage** anahtarını geçerli bir Azure depolama hesabı bağlantı dizesine ayarlamanız gerekir.

Bu bölümde, depolama bağlantı dizesine bağlanmak ve bu dizeyi almak için [Azure Depolama Gezgini](https://storageexplorer.com/) olan [Visual Studio Code için Azure Storage uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) kullanılmaktadır.

Depolama hesabı bağlantı dizesini ayarlamak için:

1. Visual Studio 'da **Cloud Explorer**'ı açın, depolama hesabınızı > **depolama hesabı** **' nı**genişletin ve ardından **Özellikler** ' i seçin ve **birincil bağlantı dizesi** değerini kopyalayın.

2. Projenizde, Local. Settings. json dosyasını açın ve **AzureWebJobsStorage** anahtarının değerini kopyaladığınız bağlantı dizesine ayarlayın.

3. İşlevlerinizin gerektirdiği diğer tüm bağlantılar için **Values** dizisine benzersiz anahtarlar eklemek için önceki adımı tekrarlayın.

Daha fazla bilgi için bkz. [yerel ayarlar dosyası](#local-settings-file).

### <a name="debugging-functions-locally"></a>İşlevleri yerel olarak hata ayıklama  

İşlevlerinizi hata ayıklamak için F5 ' i seçin. [Azure Functions Core Tools] [temel araçları]henüz indirmediyseniz bunu yapmanız istenir. Temel araçlar yüklenip çalıştırıldığında, çıkış terminalde görüntülenir. Bu, terminalden `func host start` çekirdek araçları komutunu çalıştırmayla aynıdır, ancak ek derleme görevleri ve ekli bir hata ayıklayıcı ile aynıdır.  

Proje çalışırken işlevlerinizi proje Azure 'a dağıtıldığında yaptığınız gibi tetikleyebilirsiniz. Proje hata ayıklama modunda çalışırken, beklenildiği gibi Visual Studio Code kesme noktaları isabet edilir.

HTTP Tetikleyicileri için istek URL 'SI terminaldeki çıktıda görüntülenir. HTTP Tetikleyicileri için işlev anahtarları, bir proje yerel olarak çalışırken kullanılmaz. Daha fazla bilgi için bkz. [Azure işlevlerinde kodunuzu test etme stratejileri](functions-test-a-function.md).  

Daha fazla bilgi için bkz. Azure Functions Core Tools[Azure Functions Core Tools] [ile çalışma].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Varsayılan olarak, bu ayarlar proje Azure 'da yayımlandığında otomatik olarak geçirilmez. Yayımlama bittikten sonra, Azure 'daki işlev uygulamanıza yerel. Settings. JSON konumundan ayarları yayımlama seçeneği sunulur. Daha fazla bilgi için bkz. [uygulama ayarlarını yayımlama](#publish-application-settings).

**ConnectionString** içindeki değerler hiçbir şekilde yayımlanmaz.

İşlev uygulama ayarları değerleri, kodunuzda ortam değişkenleri olarak da okunabilir. Daha fazla bilgi için, bu dile özgü başvuru makalelerinin ortam değişkenleri bölümlerine bakın:

* [C#derlemesi](functions-dotnet-class-library.md#environment-variables)
* [C# betiği (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure 'da uygulama ayarları

Projenizdeki Local. Settings. JSON dosyasındaki ayarlar, Azure 'daki işlev uygulamasındaki uygulama ayarlarıyla aynı olmalıdır. Yerel. Settings. JSON öğesine eklediğiniz tüm ayarlar ayrıca Azure 'daki işlev uygulamasına eklenmelidir. Projeyi yayımladığınızda bu ayarlar otomatik olarak karşıya yüklenemez. Benzer şekilde, [portalda](functions-how-to-use-azure-function-app-settings.md#settings) işlev uygulamanızda oluşturduğunuz tüm ayarlar yerel projenize indirilmelidir.

### <a name="publish-application-settings"></a>Uygulama ayarlarını Yayımla

Azure 'daki işlev uygulamanıza gereken ayarları yayımlamanın en kolay yolu, projenizi yayımladıktan sonra görüntülenen **karşıya yükleme ayarları** bağlantısını kullanmaktır:

![Uygulama ayarlarını karşıya yükle](./media/functions-develop-vs-code/upload-app-settings.png)

Ayrıca, komut paletindeki **Azure işlevleri: yerel ayarı yükle** komutunu kullanarak ayarları yayımlayabilirsiniz. Azure **işlevleri: yeni ayar Ekle** komutunu kullanarak Azure 'daki uygulama ayarlarına ayrı ayarlar ekleyebilirsiniz.

> [!TIP]
> Yayımlamadan önce Local. Settings. JSON dosyanızı kaydettiğinizden emin olun.

Yerel dosya şifrelendiyse, şifresi çözülür, yayımlanır ve yeniden şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir.

Aboneliğinizi, işlev uygulamanızı ve **uygulama ayarlarınızı**genişleterek **Azure: Functions** alanındaki mevcut uygulama ayarlarını görüntüleyin.

![İşlev uygulaması ayarlarını Visual Studio Code görüntüle](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Azure 'dan ayarları indirin

Azure 'da uygulama ayarları oluşturduysanız, **Azure işlevleri: uzak ayarları indir** komutunu kullanarak bunları yerel. Settings. JSON dosyanıza indirebilirsiniz.

Karşıya yüklenirken olduğu gibi, yerel dosya şifrelenirse, yeniden çözülür, güncelleştirilir ve şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir.

## <a name="monitoring-functions"></a>İzleme işlevleri

[İşlevleri yerel olarak çalıştırdığınızda](#run-functions-locally), günlük verileri Terminal konsoluna akışla kaydedilir. Ayrıca, Işlevler projeniz Azure 'daki bir işlev uygulamasında çalışırken günlük verilerini de alabilirsiniz. Neredeyse gerçek zamanlı günlük verilerini görmek için Azure 'daki akış günlüklerine bağlanabilir ya da işlev uygulamanızın nasıl davrandığının daha ayrıntılı bir şekilde anlayabilmesi için Application Insights etkinleştirebilirsiniz.

### <a name="streaming-logs"></a>Akış günlükleri

Bir uygulama geliştirirken genellikle günlüğe kaydetme bilgilerini neredeyse gerçek zamanlı olarak görmeniz yararlı olur. İşlevleriniz tarafından oluşturulan günlük dosyalarının akışını görüntüleyebilirsiniz. Bu çıktı, HTTP ile tetiklenen bir işleve yönelik bir istek için akış günlüğü örneği örneğidir:

![HTTP tetikleyicisi için akış günlüğü çıkışı](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Daha fazla bilgi için bkz. [akış günlükleri](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Akış günlükleri, Işlevlerin yalnızca tek bir örneğini destekler. İşleviniz birden çok örneğe ölçeklenirse, diğer örneklerden alınan veriler günlük akışında gösterilmez. Application Insights [canlı ölçüm akışı](../azure-monitor/app/live-stream.md) birden fazla örneği destekler. Ayrıca, neredeyse gerçek zamanlı olarak, Akış Analizi [örneklenmiş verileri](functions-monitoring.md#configure-sampling)temel alır.

### <a name="application-insights"></a>Application Insights

İşlev uygulamanızı Application Insights ile tümleştirerek işlevlerinizin yürütülmesini izlemenizi öneririz. Azure portal bir işlev uygulaması oluşturduğunuzda, bu tümleştirme varsayılan olarak oluşur. Visual Studio yayımlama sırasında işlev uygulamanızı oluşturduğunuzda Application Insights kendiniz tümleştirmeniz gerekir.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# betik projeleri

Varsayılan olarak, tüm C# projeler [ C# derlenmiş sınıf kitaplığı projeleri](functions-dotnet-class-library.md)olarak oluşturulur. Bunun yerine C# betik projeleriyle çalışmayı tercih ediyorsanız, komut dosyasını Azure işlevleri uzantısı C# ayarlarında varsayılan dil olarak seçmeniz gerekir:

1. **Dosya** > **tercihleri** > **ayarları**' nı seçin.

1. **Azure işlevleri** > **Kullanıcı ayarları** > **uzantılarına** gidin.

1. Azure Işlevinden **C # betiği** seçin **: proje dili**.

Bu adımları tamamladıktan sonra, temel alınan temel araçlara yapılan çağrılar, betik (. CSX) proje dosyalarını üreten ve C# yayımlayan `--csx` seçeneğini içerir. Bu varsayılan dili belirttiğinizde, varsayılan olarak oluşturduğunuz tüm projeler C# komut dosyası projelerine yapılır. Varsayılan değer ayarlandığında bir proje dili seçmeniz istenmez. Diğer dillerde proje oluşturmak için bu ayarı değiştirmeniz veya Kullanıcı ayarları. json dosyasından kaldırmanız gerekir. Bu ayarı kaldırdıktan sonra bir proje oluşturduğunuzda dilinizi seçmeniz istenir.

## <a name="command-palette-reference"></a>Komut paleti başvurusu

Azure Işlevleri uzantısı, Azure 'daki işlev uygulamalarınızla etkileşim kurmak için alanında yararlı bir grafik arabirimi sağlar. Aynı işlevsellik, komut paletinde (F1) komutlar olarak da kullanılabilir. Bu Azure Işlevleri komutları kullanılabilir:

|Azure Işlevleri komutu  | Açıklama  |
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
| **Ayarı Sil** | Azure 'da bir işlev uygulaması ayarını siler. Bu silme işlemi, Local. Settings. JSON dosyanızdaki ayarları etkilemez. |
| **Deponun bağlantısını kes**  | Azure 'daki bir işlev uygulaması ve bir kaynak denetimi deposu arasındaki [sürekli dağıtım](functions-continuous-deployment.md) bağlantısını kaldırır. |
| **Uzak ayarları indir** | Azure 'daki seçili işlev uygulamasındaki ayarları yerel. Settings. JSON dosyanıza indirir. Yerel dosya şifrelendiyse, şifresi çözülür, güncelleştirilir ve yeniden şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir. Bu komutu çalıştırmadan önce yerel. Settings. JSON dosyanızdaki değişiklikleri kaydettiğinizden emin olun. |
| **Ayarları Düzenle** | Azure 'da var olan bir işlev uygulaması ayarının değerini değiştirir. Bu komut yerel. Settings. JSON dosyanızdaki ayarları etkilemez.  |
| **Şifreleme ayarları** | [Yerel ayarlarda](#local-settings-file)`Values` dizisindeki ayrı öğeleri şifreler. Bu dosyada, `IsEncrypted` Ayrıca, yerel çalışma zamanının onları kullanmadan önce ayarların şifresini çözmesini belirten `true`olarak ayarlanır. Değerli bilgilerin sızması riskini azaltmak için yerel ayarları şifreleyin. Azure 'da uygulama ayarları her zaman şifreli olarak depolanır. |
| **Işlevi Şimdi Çalıştır** | Azure 'da [Zamanlayıcı tarafından tetiklenen bir işlevi](functions-bindings-timer.md) el ile başlatır. Bu komut test için kullanılır. HTTP olmayan işlevleri Azure 'da tetikleme hakkında daha fazla bilgi edinmek için bkz. [http ile tetiklenen bir Işlevi el ile çalıştırma](functions-manually-run-non-http.md). |
| **Projeyi VS Code ile kullanım için Başlat** | Gerekli Visual Studio Code proje dosyalarını var olan Işlevler projesine ekler. Temel araçları kullanarak oluşturduğunuz bir projeyle çalışmak için bu komutu kullanın. |
| **Azure Functions Core Tools yüklemeyi veya güncelleştirmeyi** | İşlevleri yerel olarak çalıştırmak için kullanılan [Azure Functions Core Tools]yüklenir veya güncelleştirir. |
| **Neden**  | Bağlı bir git deposundan proje dosyalarını Azure 'daki belirli bir dağıtıma yeniden dağıtmanıza olanak tanır. Visual Studio Code yerel güncelleştirmeleri yeniden yayımlamak için [projenizi yeniden yayımlayın](#republish-project-files). |
| **Ayarları yeniden adlandır** | Azure 'da var olan bir işlev uygulaması ayarının anahtar adını değiştirir. Bu komut yerel. Settings. JSON dosyanızdaki ayarları etkilemez. Azure 'daki ayarları yeniden adlandırdıktan sonra, [Bu değişiklikleri yerel projeye indirmeniz](#download-settings-from-azure)gerekir. |
| **Uygulamasını** | İşlev uygulamasını Azure 'da yeniden başlatır. Güncelleştirmelerin dağıtımı, işlev uygulamasını da yeniden başlatır. |
| **AzureWebJobsStorage ayarla**| `AzureWebJobsStorage` uygulama ayarının değerini ayarlar. Bu ayar, Azure Işlevleri için gereklidir. Azure 'da bir işlev uygulaması oluşturulduğunda ayarlanır. |
| **Start** | Azure 'da durdurulmuş bir işlev uygulaması başlatır. |
| **Akış günlüklerini başlatma** | Azure 'da işlev uygulaması için akış günlüklerini başlatır. Günlük bilgilerini neredeyse gerçek zamanlı olarak görmeniz gerekiyorsa Azure 'da uzaktan sorun giderme sırasında akış günlüklerini kullanın. Daha fazla bilgi için bkz. [akış günlükleri](#streaming-logs). |
| **Durdurulması** | Azure 'da çalışan bir işlev uygulamasını sonlandırır. |
| **Akış günlüklerini durdur** | Azure 'da işlev uygulaması için akış günlüklerini sonlandırır. |
| **Yuva ayarı olarak değiştirme** | Etkinleştirildiğinde, belirli bir dağıtım yuvası için bir uygulama ayarının devam ettiğinden emin olur. |
| **Azure Functions Core Tools kaldır** | Uzantısının gerektirdiği Azure Functions Core Tools kaldırır. |
| **Yerel ayarları karşıya yükle** | Yerel. Settings. JSON dosyanızdaki ayarları Azure 'da seçilen işlev uygulamasına yükler. Yerel dosya şifrelendiyse, dosyanın şifresi çözülür, karşıya yüklenir ve yeniden şifrelenir. İki konumda çakışan değerlere sahip ayarlar varsa, nasıl devam etmek istediğinizi seçmeniz istenir. Bu komutu çalıştırmadan önce yerel. Settings. JSON dosyanızdaki değişiklikleri kaydettiğinizden emin olun. |
| **GitHub 'da yürütmeyi görüntüle** | İşlev uygulamanız depoya bağlıyken belirli bir dağıtımda en son yürütmeyi gösterir. |
| **Dağıtım günlüklerini görüntüle** | Azure 'daki işlev uygulamasına belirli bir dağıtımın günlüklerini gösterir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure Functions Core Tools hakkında daha fazla bilgi için bkz. [Azure Functions Core Tools Ile çalışma](functions-run-local.md).

.NET sınıf kitaplıkları olarak işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Azure C# işlevleri geliştirici başvurusu](functions-dotnet-class-library.md). Bu makalede ayrıca, Azure Işlevleri tarafından desteklenen çeşitli tür bağlamaları bildirmek üzere özniteliklerin nasıl kullanılacağına dair örneklere bağlantılar sağlanır.

[Visual Studio Code için Azure İşlevleri uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md
