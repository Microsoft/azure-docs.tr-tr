---
title: Modeller
description: Bir modelin Azure uzaktan Işlemede ne olduğunu açıklar
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593883"
---
# <a name="models"></a>Modeller

Azure uzaktan Işleme 'deki bir *model* , [varlıkların](entities.md) ve [bileşenlerden](components.md)oluşan tam bir nesne temsiline başvurur. Modeller, uzaktan işleme hizmetine özel veri almanın ana yoludur.

## <a name="model-structure"></a>Model yapısı

Bir modelin kök düğümü olarak tam olarak bir [varlığı](entities.md) vardır. Aşağıda, alt varlıkların rastgele bir hiyerarşisi olabilir. Bir model yüklerken, bu kök varlığa bir başvuru döndürülür.

Her varlıkta [bileşen](components.md) eklenmiş olabilir. En yaygın durumda, varlıkların, [kafes kaynaklarına](meshes.md)başvuran *meshcomponents* vardır.

## <a name="creating-models"></a>Modeller oluşturma

Çalışma zamanı için model oluşturma, FBX ve GLTF gibi dosya biçimlerinden [giriş modellerini dönüştürerek](../how-tos/conversion/model-conversion.md) elde edilir. Dönüştürme işlemi, dokular, malzemeler ve kafesler gibi tüm kaynakları ayıklar ve bunları iyileştirilmiş çalışma zamanı biçimlerine dönüştürür. Ayrıca yapısal bilgileri ayıklar ve bunu ARR 'nin varlık/bileşen grafik yapısına dönüştürür.

> [!IMPORTANT]
> [Model dönüştürme](../how-tos/conversion/model-conversion.md) , [kafeslerin](meshes.md)oluşturmanın tek yoludur. Kafesler, çalışma zamanında varlıklar arasında paylaşılabilecek olsa da, bir modeli yükleme dışında çalışma zamanına bir ağ almanın başka bir yolu yoktur.

## <a name="loading-models"></a>Modeller yükleniyor

Model dönüştürüldükten sonra, Azure Blob depolamadan çalışma zamanına yüklenebilir.

Varlığın BLOB depolama alanında ilgilenme yöntemiyle farklı olan iki farklı yükleme işlevi vardır:

* BLOB depolama, bu [Hesapla](../how-tos/create-an-account.md#link-storage-accounts)doğrudan BLOB depolama parametreleri ile çözülebilir. Bu durumda ilgili yükleme işlevi `LoadModelAsync` parametresiyle birlikte `LoadModelOptions` .
* Model, SAS URI 'SI tarafından çözülebilir. İlgili yükleme işlevi `LoadModelFromSasAsync` parametresi ile `LoadModelFromSasOptions` . [Yerleşik modeller](../samples/sample-model.md)yüklenirken de bu değişkeni kullanın.

Aşağıdaki kod parçacıkları, her iki işlevle de modellerin nasıl yükleneceğini gösterir. Bir modeli BLOB depolama parametrelerini kullanarak yüklemek için aşağıdaki gibi bir kod kullanın:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Bir SAS belirteci kullanarak bir model yüklemek isterseniz, aşağıdaki kod parçacığına benzer bir kod kullanın:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Daha sonra varlık hiyerarşisinde çapraz geçiş yapabilir ve varlıkları ve bileşenleri değiştirebilirsiniz. Aynı modeli birden çok kez yüklemek, her biri varlığın/bileşen yapısının kopyasıyla birlikte birden çok örnek oluşturur. Kafesler, malzemeler ve dokular [paylaşılan kaynaklar](../concepts/lifetime.md)olduğundan, verileri de yeniden yüklenmez. Bu nedenle, bir modelin birden çok kez örnekleniyor görece daha az bellek yükü doğurur.

## <a name="api-documentation"></a>API belgeleri

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Sonraki adımlar

* [Varlıklar](entities.md)
* [Ağ yapıları](meshes.md)