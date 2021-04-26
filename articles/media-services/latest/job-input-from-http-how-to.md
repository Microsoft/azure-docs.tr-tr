---
title: HTTPS URL 'sinden iş girişi oluşturma
description: Bu konuda, bir HTTPS URL 'sinden Azure Media Services Iş girişi oluşturma Işlemi gösterilmektedir.
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
ms.openlocfilehash: 1073a13f477894e1b35d732fd1cd6191747a62a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955996"
---
# <a name="create-a-job-input-from-an-https-url"></a>HTTPS URL 'sinden iş girişi oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services v3 sürümünde işlenecek İşleri videolarınıza gönderirken Media Services'a giriş videosunun yerini de bildirmeniz gerekir. Seçeneklerden biri, bir HTTPS URL 'sini iş girişi olarak belirtmektir (Bu örnekte gösterildiği gibi). AMS v3’ün şu anda HTTPS URL'leri üzerinden yığın halinde aktarım kodlamasını desteklemediğini unutmayın. Tam bir örnek için bkz. [GitHub örneği](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Geliştirmeye başlamadan önce, [Media Services v3 API 'leri Ile geliştirmeyi](media-services-apis-overview.md) Inceleyin (API 'lere erişme hakkında bilgi, adlandırma kuralları vb.)

## <a name="net-sample"></a>.NET örneği

Aşağıdaki kod, bir HTTPS URL girişi ile nasıl iş oluşturulacağını göstermektedir.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>İş hata kodları

Bkz. [hata kodları](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Sonraki adımlar

[Yerel bir dosyadan iş girişi oluşturun](job-input-from-local-file-how-to.md).
