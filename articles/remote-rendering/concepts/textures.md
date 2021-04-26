---
title: Dokular
description: Doku kaynağı iş akışı
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594426"
---
# <a name="textures"></a>Dokular

Dokular, sabit bir [paylaşılan kaynaktır](../concepts/lifetime.md). Dokular, [öğretici: ortamı ve malzemeleri değiştirme](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)bölümünde gösterildiği gibi [BLOB depolamadan](../how-tos/conversion/blob-storage.md) yüklenebilir ve doğrudan modellere uygulanabilir. En yaygın olarak, dokular, kendi [malzemeleri](materials.md)tarafından başvurulduğu [dönüştürülmüş bir modelin](../how-tos/conversion/model-conversion.md)bir parçası olur.

## <a name="texture-types"></a>Doku türleri

Farklı doku türlerinde farklı kullanım durumları vardır:

* **2B dokular** genellikle [malzemelerde](materials.md)kullanılır.
* [Gök](../overview/features/sky.md) **haritaları için cubemaps** kullanılabilir.

## <a name="supported-texture-formats"></a>Desteklenen doku biçimleri

ARR 'ye verilen tüm dokuların [DDS biçiminde](https://en.wikipedia.org/wiki/DirectDraw_Surface)olması gerekir. Tercihen, msunucudan haritalar ve doku sıkıştırması vardır.

## <a name="loading-textures"></a>Dokular yükleniyor

Bir doku yüklerken, beklenen türünü belirtmeniz gerekir. Tür uyuşmazlıkları varsa doku yükleme başarısız olur.
Aynı URI 'yi iki kez içeren bir doku yüklemek, [paylaşılan bir kaynak](../concepts/lifetime.md)olduğundan aynı doku nesnesini döndürür.

Model yüklemeye benzer şekilde, kaynak blob depolamada bir doku varlığını ele almak için iki çeşit vardır:

* Bu doku, BLOB depolama alanı parametreleri ile doğrudan çözülebilir ve bu da [BLOB depolamanın Hesapla bağlantılı](../how-tos/create-an-account.md#link-storage-accounts)olması durumunda olabilir. Bu durumda ilgili yükleme işlevi `LoadTextureAsync` parametresiyle birlikte `LoadTextureOptions` .
* Doku varlığı, SAS URI 'SI tarafından çözülebilir. İlgili yükleme işlevi `LoadTextureFromSasAsync` parametresi ile `LoadTextureFromSasOptions` . [Yerleşik dokuları](../overview/features/sky.md#built-in-environment-maps)yüklerken bu değişkeni de kullanın.

Aşağıdaki örnek kod, nasıl bir doku yükleneceğini göstermektedir:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

SAS çeşidinin kullanılması durumunda yalnızca yükleme işlevi/parametresi farklı olduğunu unutmayın.

Dokuya yönelik olarak kullanılması beklenen değere bağlı olarak, doku türü ve içeriği için kısıtlamalar olabilir. Örneğin, bir [PBR malzemelerinin](../overview/features/pbr-materials.md) kablık eşlemesi gri tonlamalı olmalıdır.

## <a name="api-documentation"></a>API belgeleri

* [C# doku sınıfı](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [C++ doku sınıfı](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Sonraki adımlar

* [Malzemeler](materials.md)
* [Çat](../overview/features/sky.md)