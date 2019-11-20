---
title: Azure Işlevleri bağlama uzantılarını kaydetme
description: Ortamınıza bağlı olarak bir Azure Işlevleri bağlama uzantısı kaydetmeyi öğrenin.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 40dca0797d75597f4728423eb9d6d071a15d81b9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129267"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Işlevleri bağlama uzantılarını kaydetme

Azure Işlevleri sürüm 2. x, [bağlama](./functions-triggers-bindings.md) işlevleri çalışma zamanından ayrı paketler olarak kullanılabilir. .NET işlevleri NuGet paketleri aracılığıyla bağlamalara erişirken, uzantı paketleri diğer işlevlere bir yapılandırma ayarı aracılığıyla tüm bağlamalara erişmesine izin verir.

Bağlama uzantıları ile ilgili aşağıdaki öğeleri göz önünde bulundurun:

- Bağlama uzantıları, [Visual Studio kullanarak bir C# sınıf kitaplığı oluşturma](#local-csharp)dışında, 1. x işlevlerine açık olarak kayıtlı değildir.

- HTTP ve Zamanlayıcı Tetikleyicileri varsayılan olarak desteklenir ve bir uzantı gerektirmez.

Aşağıdaki tablo, bağlamaları ne zaman ve nasıl kaydedeceğinizi gösterir.

| Geliştirme ortamı |Kayıt<br/> 1\. x Işlevleri içinde  |Kayıt<br/> 2\. x Işlevleri  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|Otomatik|Otomatik|
|Non-.NET dilleri veya yerel Azure Core araçları geliştirme|Otomatik|[Azure Functions Core Tools ve uzantı paketleri kullanma](#extension-bundles)|
|C#Visual Studio kullanarak sınıf kitaplığı|[NuGet araçlarını kullanma](#vs)|[NuGet araçlarını kullanma](#vs)|
|C#Visual Studio Code kullanarak sınıf kitaplığı|Yok|[.NET Core CLI kullan](#vs-code)|

## <a name="extension-bundles"></a>Yerel geliştirme için uzantı paketleri

Uzantı demeti, işlev uygulamanıza uygun bir Işlev kümesi bağlama uzantısı eklemenize olanak sağlayan bir dağıtım teknolojisidir. Uygulamanızı oluşturduğunuzda önceden tanımlanmış bir uzantılar kümesi eklenir. Bir pakette tanımlanan uzantı paketleri birbirleriyle uyumludur, bu da paketler arasındaki çakışmalardan kaçınmanıza yardımcı olur. Uygulamanın Host. json dosyasında uzantı paketleri 'ni etkinleştirirsiniz.  

Uzantı paketlerini, sürüm 2. x ve sonraki sürümler çalışma zamanının sürümleriyle birlikte kullanabilirsiniz. Yerel olarak geliştirme yaparken [Azure Functions Core Tools](functions-run-local.md#v2)en son sürümünü kullandığınızdan emin olun.

Azure Functions Core Tools, Visual Studio Code kullanarak ve uzaktan oluştururken yerel geliştirme için uzantı paketleri kullanın.

Uzantı paketleri kullanmıyorsanız, herhangi bir bağlama uzantısı yüklemeden önce .NET Core 2. x SDK 'sını yerel bilgisayarınıza yüklemelisiniz. Uzantı demeti yerel geliştirme için bu gereksinimi ortadan kaldırır. 

Uzantı paketlerini kullanmak için *Host. JSON* dosyasını `extensionBundle`için aşağıdaki girişi içerecek şekilde güncelleştirin:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>Visual Studio ile C\# sınıf kitaplığı

**Visual Studio**'da, aşağıdaki örnekte gösterildiği gibi [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) komutunu kullanarak paket yöneticisi konsolundan paketleri yükleyebilirsiniz:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Belirli bir bağlama için kullanılan paketin adı, söz konusu bağlamaya ilişkin başvuru makalesinde sunulmaktadır. Bir örnek için [Service Bus bağlama başvurusu makalesinin paketler bölümüne](functions-bindings-service-bus.md#packages---functions-1x)bakın.

Örnekteki `<TARGET_VERSION>`, paketin `3.0.0-beta5`gibi belirli bir sürümü ile değiştirin. Geçerli sürümler, [NuGet.org](https://nuget.org)adresindeki ayrı paket sayfalarında listelenir. Çalışma zamanı 1. x veya 2. x Işlevlerine karşılık gelen ana sürümler bağlama için başvuru makalesinde belirtilmiştir.

Bağlama başvurmak için `Install-Package` kullanıyorsanız, [uzantı paketleri](#extension-bundles)kullanmanız gerekmez. Bu yaklaşım, Visual Studio 'da oluşturulan sınıf kitaplıkları için özeldir.

## <a name="vs-code"></a>C# Visual Studio Code ile sınıf kitaplığı

> [!NOTE]
> Işlevlerin, uyumlu bir bağlama uzantısı paketleri kümesini otomatik olarak yüklemesini sağlamak için [uzantı paketleri](#extension-bundles) kullanmanızı öneririz. 

**Visual Studio Code**, .NET Core CLI bir sınıf kitaplığı projesi C# için paketleri, bir komut isteminden, [DotNet Add Package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) komutunu kullanarak yükler. Aşağıdaki örnek nasıl bağlama ekleneceğini gösterir:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI yalnızca Azure Işlevleri 2. x geliştirme için kullanılabilir.

`<BINDING_TYPE_NAME>`, ihtiyacınız olan bağlamayı içeren paketin adıyla değiştirin. İstenen bağlama başvuru makalesini [desteklenen bağlamalar listesinde](./functions-triggers-bindings.md#supported-bindings)bulabilirsiniz.

Örnekteki `<TARGET_VERSION>`, paketin `3.0.0-beta5`gibi belirli bir sürümü ile değiştirin. Geçerli sürümler, [NuGet.org](https://nuget.org)adresindeki ayrı paket sayfalarında listelenir. Çalışma zamanı 1. x veya 2. x Işlevlerine karşılık gelen ana sürümler bağlama için başvuru makalesinde belirtilmiştir.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Işlev tetikleyicisi ve bağlama örneği](./functions-bindings-example.md)
