---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020382"
---
Uzantı paketlerinizi kullanmıyorsanız, projeniz için gereken belirli uzantı paketlerini yüklemek için Azure Functions Core Tools yerel olarak kullanabilirsiniz.

> [!IMPORTANT]
> Uzantı paketleri kullanan bir işlev uygulamasına açıkça uzantı yükleyemezsiniz. `extensionBundle`Uzantıları açıkça yüklemeden önce *üzerindehost.js* bölümünü kaldırın.

Aşağıdaki öğelerde, uzantıları el ile yüklemeniz gerekebilecek bazı nedenler açıklanmaktadır:

* Bir uzantının bir pakette bulunmayan belirli bir sürümüne erişmeniz gerekir.
* Pakette kullanılamayan özel bir uzantıya erişmeniz gerekir.
* Tek bir pakette kullanılamayan belirli bir uzantı birleşimine erişmeniz gerekir.

> [!NOTE]
> Temel araçları kullanarak uzantıları el ile yüklemek için [.NET Core 2. x SDK 'sının](https://dotnet.microsoft.com/download) yüklü olması gerekir. .NET Core SDK, Azure Functions Core Tools tarafından NuGet 'ten uzantı yüklemek için kullanılır. Azure Işlevleri uzantılarını kullanmak için .NET bilmeniz gerekmez.

Uzantıları açıkça yüklediğinizde, projenizin köküne Extensions. csproj adlı bir .NET proje dosyası eklenir. Bu dosya, işlevleriniz için gereken NuGet paketleri kümesini tanımlar. Bu dosyadaki [NuGet paket başvurularıyla](/nuget/consume-packages/package-references-in-project-files) çalışabilmenize karşın, çekirdek araçları dosyayı el ile düzenlemek zorunda kalmadan uzantıları yüklemenize olanak sağlar.

Gerekli uzantıları Yerel projenize yüklemek için temel araçları kullanmanın birkaç yolu vardır. 

#### <a name="install-all-extensions"></a>Tüm uzantıları yükler 

Yerel projenizde bağlamalar tarafından kullanılan tüm uzantı paketlerini otomatik olarak eklemek için aşağıdaki komutu kullanın:

```dotnetcli
func extensions install
```
Komut, hangi paketlere ihtiyacınız olduğunu görmek için dosyadaki *function.js* okur, bunları yüklerse ve uzantılar projesini (Extensions. csproj) yeniden oluşturur. Geçerli sürümde yeni bağlamalar ekler ancak mevcut bağlamaları güncelleştirmez. `--force`Yeni yenilerini yüklerken mevcut bağlamaları en son sürüme güncelleştirmek için seçeneğini kullanın.

İşlev uygulamanız çekirdek araçların tanımadığı bağlamaları kullanıyorsa, belirli uzantıyı el ile yüklemelisiniz.

#### <a name="install-a-specific-extension"></a>Belirli bir uzantıyı yükler

Belirli bir sürüme belirli bir uzantı paketini yüklemek için aşağıdaki komutu kullanın, bu durumda depolama uzantısı:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
