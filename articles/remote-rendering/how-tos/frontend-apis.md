---
title: Kimlik doğrulaması için Azure ön uç API 'Leri
description: Kimlik doğrulaması için C# ön uç API 'sinin nasıl kullanılacağını açıklar
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 7149d7ac2625eb60a1d0d22253b93b68a99475de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592101"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Kimlik doğrulaması için Azure Ön Uç API’lerini kullanma

Bu bölümde, kimlik doğrulama ve oturum yönetimi için API 'yi nasıl kullanacağınızı anlayacağız.

> [!CAUTION]
> Bu bölümde açıklanan işlevler, sunucu üzerinde REST çağrılarını dahili olarak sorun. Tüm REST çağrılarında olduğu gibi, bu komutların çok sık gönderilmesi sunucunun bu hatayı kısıtlayacak ve geri döndürmesine neden olur. `SessionGeneralContext.HttpResponseCode`Bu örnekte üyenin değeri 429 ' dir ("çok fazla istek"). Thumb kuralı olarak, **sonraki çağrılar arasında 5-10 saniyelik** bir gecikme olmalıdır.


## <a name="sessionconfiguration"></a>SessionConfiguration

SessionConfiguration SDK 'daki bir örnek için kimlik doğrulama bilgilerini ayarlamak üzere kullanılır ```RemoteRenderingClient``` .

Önemli alanlar şunlardır:

```cs
public class SessionConfiguration
{
    // Domain that will be used for account authentication for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be set to the domain of the Azure Remote Rendering account.
    public string AccountDomain;
    // Domain that will be used to generate sessions for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be selected based on the region closest to the user. For example, westus2.mixedreality.azure.com or westeurope.mixedreality.azure.com.
    public string RemoteRenderingDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

C++ karşılığı şöyle görünür:

```cpp
struct SessionConfiguration
{
    std::string AccountDomain{};
    std::string RemoteRenderingDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};
```

Etki alanındaki _bölge_ bölümü için, [size yakın bir bölge](../reference/regions.md)kullanın.

Hesap bilgileri, [Hesap bilgilerini alma](create-an-account.md#retrieve-the-account-information) paragrafında açıklandığı gibi portaldan elde edilebilir.

## <a name="azure-frontend"></a>Azure ön ucu

İlgili sınıflar ```RemoteRenderingClient``` ve ' dir ```RenderingSession``` . ```RemoteRenderingClient``` , aşağıdakiler dahil olmak üzere hesap yönetimi ve hesap düzeyi işlevsellik için kullanılır: varlık dönüştürme ve işleme oturumu oluşturma. ```RenderingSession``` , oturum düzeyi işlevselliği için kullanılır ve şunları içerir: oturum güncelleştirme, sorgular, yenileme ve kullanımdan kaldırma.

Her açılan/oluşturulan ```RenderingSession``` , oluşturulduğu ön uç için bir başvuru tutacaktır. Temiz bir şekilde kapatmak için, ön uç serbest bırakılmadan önce tüm oturumların serbest bırakılmalıdır.

Bir oturumun ayırmayı kaldırma işlemi, Azure üzerindeki sunucuyu durdurmayacak, `RenderingSession.StopAsync` açıkça çağrılmalıdır.

Bir oturum oluşturulduktan ve durumu hazırlanıyor olarak işaretlendiğinde, ile uzaktan işleme çalışma zamanına bağlanabilir `RenderingSession.ConnectAsync` .

### <a name="threading"></a>İş Parçacığı Oluşturma

Tüm RenderingSession ve RemoteRenderingClient zaman uyumsuz çağrıları, ana uygulama iş parçacığında değil, bir arka plan iş parçacığında tamamlanır.

### <a name="conversion-apis"></a>Dönüştürme API 'Leri

Dönüştürme hizmeti hakkında daha fazla bilgi için bkz. [model dönüştürme REST API](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Varlık dönüştürmeyi Başlat

```cs
async void StartAssetConversion(RemoteRenderingClient client, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    var result = await client.StartAssetConversionAsync(
        new AssetConversionInputOptions(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputOptions(storageContainer, bloboutpath, "", outputName)
        );
}
```

```cpp
void StartAssetConversion(ApiHandle<RemoteRenderingClient> client, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputOptions input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputOptions output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    client->StartAssetConversionAsync(input, output, [](Status status, ApiHandle<AssetConversionResult> result) {
        if (status == Status::OK)
        {
            //use result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    });
}
```

#### <a name="get-conversion-status"></a>Dönüştürme durumunu al

```cs
async void GetConversionStatus(RemoteRenderingClient client, string assetId)
{
    AssetConversionStatusResult status = await client.GetAssetConversionStatusAsync(assetId);
    // do something with status (e.g. check current status etc.)
}
```

```cpp
void GetConversionStatus(ApiHandle<RemoteRenderingClient> client, std::string assetId)
{
    client->GetAssetConversionStatusAsync(assetId, [](Status status, ApiHandle<AssetConversionStatusResult> result) {
        if (status == Status::OK)
        {
            // do something with result (e.g. check current status etc.)
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }
    });
}

```


### <a name="rendering-apis"></a>Oluşturma API 'Leri

Oturum yönetimi hakkındaki ayrıntılar için [oturum yönetimi REST API](session-rest-api.md) bakın.

Bir işleme oturumu, hizmet üzerinde dinamik olarak oluşturulabilir veya zaten mevcut bir oturum KIMLIĞI, bir RenderingSession nesnesinde ' açılabilir '.

#### <a name="create-rendering-session"></a>İşleme oturumu oluştur

```cs
async void CreateRenderingSession(RemoteRenderingClient client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationOptions(vmSize, maxLeaseInMinutes / 60, maxLeaseInMinutes % 60));

    // if the call was successful, result.Session holds a valid session reference, otherwise check result.Context for error information
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    RenderingSessionCreationOptions params;
    params.MaxLeaseInMinutes = maxLeaseInMinutes;
    params.Size = vmSize;
    client->CreateNewRenderingSessionAsync(params, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            result->GetSession();
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>Mevcut bir işleme oturumunu açın

Mevcut bir oturumu açmak zaman uyumlu bir çağrıdır.

```cs
async void CreateRenderingSession(RemoteRenderingClient client, string sessionId)
{
    CreateRenderingSessionResult result = await client.OpenRenderingSessionAsync(sessionId);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // Query session status, etc.
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, std::string sessionId)
{
    client->OpenRenderingSessionAsync(sessionId, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode()==Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // Query session status, etc.
        }
    });
}
```


#### <a name="get-current-rendering-sessions"></a>Geçerli işleme oturumlarını al

```cs
async void GetCurrentRenderingSessions(RemoteRenderingClient client)
{
    RenderingSessionPropertiesArrayResult result = await client.GetCurrentRenderingSessionsAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties[] properties = result.SessionProperties;
        // Query session status, etc.
    }
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<RemoteRenderingClient> client)
{
    client->GetCurrentRenderingSessionsAsync([](Status status, ApiHandle<RenderingSessionPropertiesArrayResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            std::vector<RenderingSessionProperties> properties;
            result->GetSessionProperties(properties);
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>Oturum API 'Leri

#### <a name="get-rendering-session-properties"></a>İşleme oturumu özelliklerini al

```cs
async void GetRenderingSessionProperties(RenderingSession session)
{
    RenderingSessionPropertiesResult result = await session.GetPropertiesAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties properties = result.SessionProperties;
    }
    else
    {
        Console.WriteLine("Failed to get properties of session!");
    }
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<RenderingSession> session)
{
    session->GetPropertiesAsync([](Status status, ApiHandle<RenderingSessionPropertiesResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            RenderingSessionProperties properties = result->GetSessionProperties();
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>İşleme oturumunu Güncelleştir

```cs
async void UpdateRenderingSession(RenderingSession session, int updatedLeaseInMinutes)
{
    SessionContextResult result = await session.RenewAsync(
        new RenderingSessionUpdateOptions(updatedLeaseInMinutes / 60, updatedLeaseInMinutes % 60));
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session renewed succeeded!");
    }
    else
    {
        Console.WriteLine("Failed to renew rendering session!");
    }
}
```

```cpp
void UpdateRenderingSession(ApiHandle<RenderingSession> session, int updatedLeaseInMinutes)
{
    RenderingSessionUpdateOptions params;
    params.MaxLeaseInMinutes = updatedLeaseInMinutes;
    session->RenewAsync(params, [](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>İşleme oturumunu durdur

```cs
async void StopRenderingSession(RenderingSession session)
{
    SessionContextResult result = await session.StopAsync();
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session stopped successfully!");
    }
    else
    {
        Console.WriteLine("Failed to stop rendering session!");
    }
}
```

```cpp
void StopRenderingSession(ApiHandle<RenderingSession> session)
{
    session->StopAsync([](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>ARR denetçisine bağlanma

```cs
async void ConnectToArrInspector(RenderingSession session)
{
    string htmlPath = await session.ConnectToArrInspectorAsync();
#if WINDOWS_UWP
    UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
    {
        var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
        await Windows.System.Launcher.LaunchFileAsync(file);
    }, true);
#else
    InvokeOnAppThreadAsync(() =>
        {
            System.Diagnostics.Process.Start("file:///" + htmlPath);
        });
#endif
}
```

```cpp
void ConnectToArrInspector(ApiHandle<RenderingSession> session)
{
    session->ConnectToArrInspectorAsync([](Status status, std::string result) {
        if (status == Status::OK)
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + result;
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Hesap oluşturma](create-an-account.md)
* [Örnek PowerShell betikleri](../samples/powershell-example-scripts.md)
