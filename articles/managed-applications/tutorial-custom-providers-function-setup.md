---
title: Azure özel sağlayıcıları için Azure Işlevleri ayarlama
description: Bu öğreticide bir Azure işlev uygulaması oluşturma ve Azure özel sağlayıcılarıyla çalışacak şekilde ayarlama işlemi nasıl yapılır?
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6b5ab6948d382a9925c9ced91e04f360ecf51a0e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173020"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure özel sağlayıcıları için Azure Işlevleri ayarlama

Özel sağlayıcı, Azure ile uç nokta arasında bir sözleşmedir. Özel sağlayıcılar ile Azure 'da iş akışlarını değiştirebilirsiniz. Bu öğreticide, bir Azure işlevi uygulamasının özel bir sağlayıcı uç noktası olarak çalışacak şekilde nasıl ayarlanacağı gösterilmektedir.

## <a name="create-the-azure-function-app"></a>Azure işlevi uygulaması oluşturma

> [!NOTE]
> Bu öğreticide, bir Azure işlev uygulaması kullanan bir basit hizmet uç noktası oluşturacaksınız. Ancak, özel bir sağlayıcı herkese açık olarak erişilebilen herhangi bir uç noktası kullanabilir. Alternatifler Azure Logic Apps, Azure API Management ve Azure App Service Web Apps özelliğini içerir.

Bu öğreticiyi başlatmak için öncelikle [Azure Portal Ilk Azure işlev uygulamanızı oluşturma](../azure-functions/functions-create-first-azure-function.md)öğreticisini izlemelisiniz. Bu öğreticide, Azure portal değiştirilebilen bir .NET Core Web kancası işlevi oluşturulur. Bu, aynı zamanda geçerli öğreticinin temelidir.

## <a name="install-azure-table-storage-bindings"></a>Azure Tablo depolama bağlamalarını yükler

Azure Tablo depolama bağlamalarını yüklemek için:

1. HttpTrigger için **tümleştir** sekmesine gidin.
1. **+ Yeni giriş**' i seçin.
1. **Azure Tablo depolama**' yı seçin.
1. Zaten yüklenmemişse Microsoft. Azure. WebJobs. Extensions. Storage uzantısını yükleme.
1. **Tablo parametresi adı** kutusuna **TableStorage**girin.
1. **Tablo adı** kutusuna **mycustomresources**yazın.
1. Güncelleştirilmiş giriş parametresini kaydetmek için **Kaydet** ' i seçin.

![Özel sağlayıcıya genel bakış tablo bağlamalarını gösterme](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Yeniden takip eden HTTP yöntemlerini güncelleştirme

Azure işlevini, özel sağlayıcı yeniden deneme isteği yöntemlerini içerecek şekilde ayarlamak için:

1. HttpTrigger için **tümleştir** sekmesine gidin.
1. **SEÇILI http metotları**altında **Al**, **Postala**, **Sil**ve **koy**' u seçin.

![HTTP yöntemlerini gösteren özel sağlayıcıya genel bakış](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet paketleri Ekle

> [!NOTE]
> C# Proje dosyanız proje dizininde yoksa, el ile ekleyebilirsiniz. Ya da Microsoft. Azure. WebJobs. Extensions. Storage uzantısı işlev uygulamasında yüklendikten sonra görüntülenir.

Sonra, C# proje dosyasını faydalı NuGet kitaplıklarını içerecek şekilde güncelleştirin. Bu kitaplıklar, özel sağlayıcılardan gelen istekleri ayrıştırmayı kolaylaştırır. [Portaldan uzantı eklemek](../azure-functions/install-update-binding-extensions-manual.md) ve C# proje dosyasını aşağıdaki paket başvurularını içerecek şekilde güncelleştirmek için adımları izleyin:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Aşağıdaki XML öğesi örnek C# bir proje dosyasıdır:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure işlevi uygulamasını Azure özel sağlayıcı uç noktası olarak çalışacak şekilde ayarlarsınız.

Yeniden bir özel sağlayıcı uç noktası yazmak hakkında bilgi edinmek için bkz [. Öğretici: Yeniden bir özel sağlayıcı uç noktası](./tutorial-custom-providers-function-authoring.md)yazma.

