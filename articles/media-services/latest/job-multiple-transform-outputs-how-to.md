---
title: Birden çok dönüşüm çıkışına sahip bir iş oluşturma
description: Bu konu, birden çok dönüştürme çıkışına sahip bir Azure Media Services işinin nasıl oluşturulacağını göstermektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ada86e0ce44977789eaa5d6327c0aad2883b47d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123533"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Birden çok dönüşüm çıkışına sahip bir iş oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu konu, iki dönüşüm çıkışına sahip bir dönüşümün nasıl oluşturulacağını gösterir. İlk bir giriş, yerleşik bir uyarlamalı [akış](encode-concept.md#builtinstandardencoderpreset) ön ayarı ile Uyarlamalı bit hızı akışı için kodlanacak şekilde çağrı yapılır. İkinci bir, giriş videosunda ses sinyalini, [audioanalizin önayarıyla](analyze-video-audio-files-concept.md#built-in-presets)işlenecek şekilde çağırır. Dönüşüm oluşturulduktan sonra, videonuzu uygun şekilde işleyecek bir iş gönderebilirsiniz. Bu örnekte, iki dönüşüm çıkışı belirttiğimiz için iki Iş çıkışı belirtmemiz gerekir. Her iki Iş çıkışını aynı kıymete (aşağıda gösterildiği gibi) doğrudan yönlendirmeye veya sonuçların ayrı varlıklara yazılmasına izin verebilirsiniz.

> [!TIP]
> Geliştirmeye başlamadan önce, [Media Services v3 API 'leri Ile geliştirmeyi](media-services-apis-overview.md) Inceleyin (API 'lere erişme hakkında bilgi, adlandırma kuralları vb.)

## <a name="create-a-transform"></a>Dönüşüm oluşturma

Aşağıdaki kod, iki çıkış üreten bir dönüşümün nasıl oluşturulacağını gösterir.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```

## <a name="submit-a-job"></a>İş gönder

HTTPS URL girişi ile ve iki iş çıktılarına sahip bir iş oluşturun.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>İş hata kodları

Bkz. [hata kodları](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Sonraki adımlar

[.NET kullanarak Azure Media Services v3 örnekleri](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
