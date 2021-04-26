---
title: Media Services ile kodlarken bir videoyu alt kırpın
description: Bu konuda, .NET SDK kullanarak Azure Media Services kodlama sırasında bir videonun nasıl alt kırpılacağını açıklamaktadır
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 333d9d7e23182a98e9b8ad5bcc9c2a63362b2293
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492367"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Media Services .NET ile kodlarken bir videoyu alt kırpın

Bir [işi](/rest/api/media/jobs)kullanarak kodlarken videoyu kırpabilir veya alt kırpabilirsiniz. Bu işlev, [Builtınstandardencoderönayar](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ön ayarları veya [Standardencoderönayar](/rest/api/media/transforms/createorupdate#standardencoderpreset) önayarları kullanılarak oluşturulan [dönüşümlerle](/rest/api/media/transforms) birlikte kullanılır.

Aşağıdaki C# örneği bir varlık içinde bir kodlama işi gönderdiği bir videoyu kırpan bir iş oluşturur. 

## <a name="prerequisites"></a>Önkoşullar

Bu konuda açıklanan adımları tamamlayabilmeniz için şunları yapmanız gerekir:

- [Azure Media Services hesabı oluşturma](./account-create-how-to.md)
- Bir dönüşüm ve giriş ve çıkış varlıkları oluşturun. .NET öğreticisini [kullanarak karşıya yükleme, kodlama ve akış videoları](stream-files-tutorial-with-api.md) Için nasıl dönüşüm ve giriş ve çıkış varlıkları oluşturulacağını görebilirsiniz.
- [Kodlama kavramı](encode-concept.md) konusunu gözden geçirin.

## <a name="example"></a>Örnek

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Özel dönüşümle kodlama](transform-custom-presets-how-to.md) 
