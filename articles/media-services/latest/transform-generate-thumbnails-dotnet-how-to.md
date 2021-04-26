---
title: Media Encoder Standard .NET kullanarak küçük resimler oluşturma
description: Bu makalede, Media Encoder Standard kullanarak aynı anda bir varlık kodlamak ve küçük resimler oluşturmak için .NET kullanımı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: d20d57432ff72c5fbad14474f1da50d79c094cef
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106489936"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>.NET ile kodlayıcı standardını kullanarak küçük resimleri oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Giriş videoınızdan [JPEG](https://en.wikipedia.org/wiki/JPEG) veya [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) resim dosyası biçimlerinde bir veya daha fazla küçük resim oluşturmak için Media Encoder Standard kullanabilirsiniz.

## <a name="recommended-reading-and-practice"></a>Önerilen okuma ve uygulama

Özel dönüşümlerle [bir özel dönüşümle nasıl kodlanacağını](transform-custom-presets-how-to.md)okuyarak özel dönüştürmeleri tanıdık yapmanız önerilir.

## <a name="transform-code-example"></a>Dönüştürme kodu örneği

Aşağıdaki kod örneği yalnızca bir küçük resim oluşturur.  Aşağıdaki parametreleri ayarlamanız gerekir:

- **Başlat** -küçük resim oluşturmaya başlamak için giriş videosunun konumu. Değer ISO 8601 biçiminde (örneğin, 5 saniye içinde başlayacak şekilde PT05S) veya bir çerçeve sayısı (örneğin, 10 ' dan başlamak için 10) ya da akış süresine yönelik göreli bir değer (örneğin, akış süresinin %10 ' u ile başlaması için %10) olabilir. Ayrıca, kodlayıcıya videonun ilk birkaç saniyesinin en iyi küçük resmini seçmesini ve adım ve aralığa yönelik diğer ayarların ne olduğuna bakılmaksızın yalnızca bir küçük resim üretecağını belirten {Best} makrosunu destekler. Varsayılan değer makro {En Iyi} ' dır.
- **adım** -küçük resimlerin oluşturulduğu aralıklar. Değer ISO 8601 biçiminde olabilir (örneğin, her 5 saniyede bir görüntü için PT05S) veya çerçeve sayısı (örneğin, her 30 karede bir görüntü için 30) veya akış süresine yönelik göreli bir değer (örneğin, her %10 akış süresi için bir görüntü için %10) olabilir. Adım değeri, ilk oluşturulan küçük resmi etkiler ve bu, yalnızca dönüştürme önceden ayarlanmış başlangıç saatinde belirtilen bir değer olmayabilir. Bunun nedeni, ilk çıkış olarak başlangıç saatinden başlangıç saati ve adım konumu arasındaki en iyi küçük resmi seçmesini sağlayan kodlayıcıdır. Varsayılan değer %10 olduğundan, akışın uzun süresi varsa, ilk oluşturulan küçük resim başlangıç saatinde belirtilen bir değerden uzakta olabilir. İlk küçük resmin başlangıç saatine yakın olması bekleniyorsa, adım için makul bir değer seçmeyi deneyin veya Başlangıç zamanında yalnızca bir küçük resim gerekliyse Aralık değerini 1 olarak ayarlayın.
- **Aralık** -küçük resim oluşturmanın durdurulması için giriş videosunda önceden ayarlanmış bir başlangıç zamanına göre konum. Değer ISO 8601 biçiminde olabilir (örneğin, başlangıç zamanından önce 5 dakika ve 30 saniye içinde PT5M30S için) veya bir çerçeve sayısı (örneğin, Başlangıç zamanında çerçevenin 300th çerçevesini durdurmak için 300). Bu değer 1 ise, Başlangıç zamanında yalnızca bir küçük resim üretildiğini belirtir) veya akış süresine göreli bir değer (örneğin, başlangıç zamanından itibaren akış süresinin yarısı kadar durmak üzere %50). Varsayılan değer %100 ' dir, bu da akışın sonunda durmak anlamına gelir.
- **Katmanlar** -kodlayıcı tarafından üretilecek çıkış görüntüsü katmanlarından oluşan bir koleksiyon.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```
