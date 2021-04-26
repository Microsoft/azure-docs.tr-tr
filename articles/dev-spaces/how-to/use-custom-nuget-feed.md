---
title: Özel NuGet akışı kullanma
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Azure dev alanında NuGet paketlerine erişmek ve bunları kullanmak için özel bir NuGet akışı kullanın.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kapsayıcılar
manager: gwallace
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960227"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Azure Dev Spaces ile özel bir NuGet akışı kullanma

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Bir NuGet akışı, paket kaynaklarını bir projeye dahil etmek için uygun bir yol sağlar. Bağımlılıkların Docker kapsayıcısına düzgün şekilde yüklenmesi için Azure Dev Spaces bu akışa erişmesi gerekir.

## <a name="set-up-a-nuget-feed"></a>NuGet akışı ayarlama

Düğümün altındaki dosyaya bağımlılığı için bir [paket başvurusu](/nuget/consume-packages/package-references-in-project-files) ekleyin `*.csproj` `PackageReference` . Örnek:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Proje klasöründe bir [NuGet.Config](/nuget/reference/nuget-config-file) dosyası oluşturun ve `packageSources` `packageSourceCredentials` NuGet akışınız için ve bölümlerini ayarlayın. `packageSources`Bölüm, AKS kümenizdeki erişilebilir olması gereken akış URL 'nizi içerir. , `packageSourceCredentials` Akışa erişim için kimlik bilgileridir. Örnek:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Dockerfiles 'ı, dosyayı görüntüye kopyalamak için güncelleştirin `NuGet.Config` . Örnek:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows,, `NuGet.Config` `Nuget.Config` , ve `nuget.config` hepsi geçerli dosya adları olarak çalışmaktadır. Linux 'ta, `NuGet.Config` Bu dosya için yalnızca geçerli bir dosya adıdır. Azure Dev Spaces Docker ve Linux kullandığından, bu dosyanın adlandırılmış olması gerekir `NuGet.Config` . Adlandırmayı el ile veya çalıştırarak giderebilirsiniz `dotnet restore --configfile nuget.config` .


Git kullanıyorsanız, sürüm denetiminde NuGet akışınız için kimlik bilgilerine sahip olmanız gerekmez. `NuGet.Config` `.gitignore` `NuGet.Config` Dosyanın sürüm denetimine eklenmemesi için projenize öğesine ekleyin. Azure Dev Spaces kapsayıcı görüntüsü oluşturma işlemi sırasında bu dosyaya ihtiyaç duyar, ancak varsayılan olarak, ' de `.gitignore` ve eşitleme sırasında tanımlanan kurallara uyar `.dockerignore` . Varsayılanı değiştirmek ve Azure Dev Spaces dosyayı eşitlemesine izin vermek için `NuGet.Config` , `azds.yaml` dosyayı güncelleştirin:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Git kullanmıyorsanız, bu adımı atlayabilirsiniz.

Visual Studio Code veya Visual Studio 'da bir sonraki sefer çalıştırdığınızda veya bir kez daha `azds up` `F5` karşılaşışınızda, Azure dev Spaces `NuGet.Config` dosyayı eşitleyecek ve paket bağımlılıklarını yükleyecek şekilde kullanacaktır.

## <a name="next-steps"></a>Sonraki adımlar

[NuGet ve nasıl çalıştığı](/nuget/what-is-nuget)hakkında daha fazla bilgi edinin.
