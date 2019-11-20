---
title: Azure Dev Spaces içinde özel bir NuGet akışı kullanma
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Azure dev alanında NuGet paketlerine erişmek ve bunları kullanmak için özel bir NuGet akışı kullanın.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kapsayıcılar
manager: gwallace
ms.openlocfilehash: 019335cd73e8eaf0ada6897f08c88ef2b8bbf631
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162808"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Azure dev alanında özel bir NuGet akışı kullanma

Bir NuGet akışı, paket kaynaklarını bir projeye dahil etmek için uygun bir yol sağlar. Bağımlılıkların Docker kapsayıcısına düzgün şekilde yüklenmesi için Azure Dev Spaces bu akışa erişmesi gerekir.

## <a name="set-up-a-nuget-feed"></a>NuGet akışı ayarlama

`PackageReference` düğümü altındaki `*.csproj` dosyasına bağımlılığı için bir [paket başvurusu](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) ekleyin. Örnek:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Proje klasöründe bir [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) dosyası oluşturun ve NuGet akışınız için `packageSources` ve `packageSourceCredentials` bölümlerini ayarlayın. `packageSources` bölümü, AKS kümenizdeki erişilebilir olması gereken akış URL 'nizi içerir. `packageSourceCredentials`, akışa erişim için kimlik bilgileridir. Örnek:

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

`NuGet.Config` dosyasını görüntüye kopyalamak için Dockerfiles 'ı güncelleştirin. Örnek:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows 'da, `NuGet.Config`, `Nuget.Config`ve `nuget.config` tümü geçerli dosya adları olarak çalışmaktadır. Linux 'ta yalnızca `NuGet.Config`, bu dosya için geçerli bir dosya adıdır. Azure Dev Spaces Docker ve Linux kullandığından, bu dosya `NuGet.Config`olarak adlandırılmalıdır. Adlandırmayı el ile veya `dotnet restore --configfile nuget.config`çalıştırarak giderebilirsiniz.


Git kullanıyorsanız, sürüm denetiminde NuGet akışınız için kimlik bilgilerine sahip olmanız gerekmez. `NuGet.Config` dosyasının sürüm denetimine eklenmemesi için, projeniz için `.gitignore` `NuGet.Config` ekleyin. Azure Dev Spaces kapsayıcı görüntüsü oluşturma işlemi sırasında bu dosya gerekecektir, ancak varsayılan olarak, eşitleme sırasında `.gitignore` ve `.dockerignore` tanımlanan kurallara uyar. Varsayılanı değiştirmek ve Azure Dev Spaces `NuGet.Config` dosyayı eşitlemesine izin vermek için `azds.yaml` dosyasını güncelleştirin:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Git kullanmıyorsanız, bu adımı atlayabilirsiniz.

`azds up` veya Visual Studio Code ya da Visual Studio 'da `F5` bir sonraki sefer çalıştırdığınızda Azure Dev Spaces, paket bağımlılıklarını yüklemek için `NuGet.Config` dosyasını eşitler.

## <a name="next-steps"></a>Sonraki adımlar

[NuGet ve nasıl çalıştığı](https://docs.microsoft.com/nuget/what-is-nuget)hakkında daha fazla bilgi edinin.