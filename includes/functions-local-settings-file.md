---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 190524251d139e1421c1aac93d5a4dd523068a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958085"
---
## <a name="local-settings-file"></a>Yerel ayarlar dosyası

Dosya local.settings.js, uygulama ayarlarını, bağlantı dizelerini ve yerel geliştirme araçları tarafından kullanılan ayarları depolar. local.settings.jsdosyadaki ayarlar yalnızca projeleri yerel olarak çalıştırırken kullanılır. Yerel ayarlar dosyası bu yapıya sahiptir:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Projeleri yerel olarak çalıştırdığınızda bu ayarlar desteklenir:

| Ayar      | Açıklama                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Bu ayar olarak ayarlandığında `true` , tüm değerler yerel makine anahtarıyla şifrelenir. Komutlarıyla birlikte kullanılır `func settings` . Varsayılan değer `false` olarak belirlenmiştir. Yerel bilgisayarınızdaki local.settings.js, hizmet bağlantı dizeleri gibi gizli dizileri içerdiğinde şifrelemek isteyebilirsiniz. Ana bilgisayar, çalışırken ayarların şifresini otomatik olarak çözer. `func settings decrypt`Yerel olarak şifrelenen ayarları okumayı denemeden önce komutunu kullanın. |
| **`Values`** | Bir proje yerel olarak çalışırken kullanılan uygulama ayarları ve bağlantı dizeleri dizisi. Bu anahtar-değer (dize-dize) çiftleri, gibi Azure 'daki işlev uygulamanızda uygulama ayarlarına karşılık gelir [`AzureWebJobsStorage`] . Birçok tetikleyici ve bağlamanın, `Connection` [BLOB depolama tetikleyicisi](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)gibi bir bağlantı dizesi uygulama ayarına başvuran bir özelliği vardır. Bu özellikler için dizide tanımlanmış bir uygulama ayarı gereklidir `Values` . Yaygın olarak kullanılan ayarların listesi için sonraki tabloya bakın. <br/>Değerler dize olmalıdır ve JSON nesneleri veya dizileri olmamalıdır. Ayar adları iki nokta ( `:` ) veya çift alt çizgi () içeremez `__` . Çift alt çizgi karakterleri çalışma zamanı tarafından ayrılmıştır ve iki nokta üst üste [bağımlılık ekleme](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings)desteği için ayrılmıştır. |
| **`Host`** | Bu bölümdeki ayarlar, projeleri yerel olarak çalıştırdığınızda Işlevler ana bilgisayar işlemini özelleştirir. Bu ayarlar, Azure 'da projeleri çalıştırdığınızda da uygulanan ayarları host.jsfarklıdır. |
| **`LocalHttpPort`** | Yerel Işlevler Konağı (ve) çalıştırılırken kullanılan varsayılan bağlantı noktasını ayarlar `func host start` `func run` . `--port`Komut satırı seçeneği bu ayarın üzerine gelir. Örneğin, Visual Studio IDE 'de çalışırken, "Project Properties-> Debug" penceresine giderek ve `host start --port <your-port-number>` "uygulama bağımsız değişkenleri" alanında sağlanabilecek bir komutta bağlantı noktası numarasını açıkça belirterek bağlantı noktası numarasını değiştirebilirsiniz. |
| **`CORS`** | , [Çıkış noktaları arası kaynak paylaşımı (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)için izin verilen kaynakları tanımlar. Kaynaklar, boşluk olmadan virgülle ayrılmış bir liste olarak sağlanır. () Joker değeri \* desteklenir ve bu, herhangi bir kaynaktan gelen isteklere izin verir. |
| **`CORSCredentials`** |  Olarak ayarlandığında `true` , isteklere izin verir `withCredentials` . |
| **`ConnectionStrings`** | Bir koleksiyon. İşlev bağlamalarınız tarafından kullanılan bağlantı dizeleri için bu koleksiyonu kullanmayın. Bu koleksiyon, genellikle `ConnectionStrings` [Entity Framework](/ef/ef6/)gibi bir yapılandırma dosyasının bölümünden bağlantı dizelerini alan çerçeveler tarafından kullanılır. Bu nesnedeki bağlantı dizeleri [System. Data. SqlClient](/dotnet/api/system.data.sqlclient)sağlayıcı türüyle ortama eklenir. Bu koleksiyondaki öğeler, diğer uygulama ayarlarıyla Azure 'da yayımlanmaz. Bu değerleri, `Connection strings` işlev uygulaması ayarlarınızın koleksiyonuna açıkça eklemeniz gerekir. [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection)İşlev kodunuzda bir oluşturuyorsanız, bağlantı dizesi değerini portaldaki **uygulama ayarları** ' nda diğer bağlantılarınız ile depolamanız gerekir. |

Aşağıdaki uygulama ayarları, **`Values`** yerel olarak çalıştırılırken dizisine dahil edilebilir:

| Ayar | Değerler | Açıklama |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Depolama hesabı bağlantı dizesi veya<br/>`UseDevelopmentStorage=true`| Bir Azure depolama hesabı için bağlantı dizesini içerir. HTTP dışındaki Tetikleyiciler kullanılırken gereklidir. Daha fazla bilgi için bkz [`AzureWebJobsStorage`] . başvuru.<br/>[Azure Storage öykünücüsü](../articles/storage/common/storage-use-emulator.md) yerel olarak yüklüyse ve [`AzureWebJobsStorage`] olarak ayarlarsanız `UseDevelopmentStorage=true` , çekirdek araçlar öykünücüyü kullanır. Öykünücü geliştirme sırasında faydalıdır, ancak dağıtımdan önce gerçek bir depolama bağlantısı ile test etmeniz gerekir.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Yerel olarak çalışırken bir işlevi devre dışı bırakmak için, koleksiyona ekleyin; `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` burada `<FUNCTION_NAME>` işlevin adıdır. Daha fazla bilgi için bkz. [Azure işlevleri 'nde işlevleri devre dışı bırakma](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Işlevlerin çalışma zamanının hedeflenen dilini gösterir. Sürüm 2. x ve üzeri Işlevleri çalışma zamanının daha yüksek olması gerekir. Bu ayar projeniz için temel araçlar tarafından üretilir. Daha fazla bilgi için bkz [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) . başvuru.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Yerel olarak çalıştırılırken PowerShell 7 ' nin kullanılacağını gösterir. Ayarlanmamışsa, PowerShell Core 6 kullanılır. Bu ayar yalnızca yerel olarak çalıştırılırken kullanılır. Azure 'da çalışırken, PowerShell çalışma zamanı sürümü `powerShellVersion` [Portal 'da ayarlanbilen](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version)site yapılandırma ayarı tarafından belirlenir. | 

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
