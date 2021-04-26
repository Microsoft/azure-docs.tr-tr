---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663038"
---
## <a name="add-secret-manager"></a>Gizli dizi Yöneticisi ekleme

Gizli dizi Yöneticisi olarak adlandırılan bir araç, geliştirme çalışması için hassas verileri proje ağacınızdaki dışında depolar. Bu yaklaşım, uygulama gizli dizilerini kaynak kodunun içinde yanlışlıkla paylaşmayı önlemeye yardımcı olur. ASP.NET Core projesinde gizli yöneticinin kullanımını etkinleştirmek için aşağıdaki adımları izleyin:

#### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

Projenin kök dizinine gidin ve projede gizli dizileri etkinleştirmek için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets init
```

`UserSecretsId`GUID içeren bir öğe *. csproj* dosyasına eklenir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

Projenin kök dizinine gidin ve projede gizli dizileri etkinleştirmek için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets init
```

`UserSecretsId`GUID içeren bir öğe *. csproj* dosyasına eklenir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

1. *. Csproj* dosyasını açın.

1. `UserSecretsId`Burada gösterildiği gibi *. csproj* dosyasına bir öğesi ekleyin. Aynı GUID 'i kullanabilir veya bu değeri kendi kendinizinkini kullanarak değiştirebilirsiniz.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. *. Csproj* dosyasını kaydedin.

---

> [!TIP]
> Gizli yönetici hakkında daha fazla bilgi edinmek için bkz. [ASP.NET Core geliştirme sırasında uygulama gizli dizileri Için güvenli depolama](/aspnet/core/security/app-secrets).
