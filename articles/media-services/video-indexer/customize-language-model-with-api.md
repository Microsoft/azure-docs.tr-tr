---
title: Video Indexer API ile dil modelini özelleştirme
titlesuffix: Azure Media Services
description: Video Indexer API ile bir dil modelini özelleştirmeyi öğrenin.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: 890e02812fb06cf0b0ebe990b0175311d5c85ab5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312935"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Video Indexer API ile dil modelini özelleştirme

Video Indexer, altyapı özelliğini benzetirecek olan etki alanındaki metin olan uyarlama metnini karşıya yükleyerek konuşma tanımayı özelleştirmek için özel dil modelleri oluşturmanızı sağlar. Modelinize eğtikten sonra, uyarlama metninde görüntülenen yeni kelimeler tanınacaktır.

Özel dil modelleriyle ilgili ayrıntılı genel bakış ve en iyi uygulamalar için bkz. [video Indexer bir dil modelini özelleştirme](customize-language-model-overview.md).

Bu konuda açıklandığı gibi, hesabınızda özel dil modelleri oluşturmak ve düzenlemek için Video Indexer API 'Lerini kullanabilirsiniz. Ayrıca, [video Indexer Web sitesini kullanarak dil modelini özelleştirme](customize-language-model-with-api.md)bölümünde açıklandığı gibi Web sitesini de kullanabilirsiniz.

## <a name="create-a-language-model"></a>Dil modeli oluşturma

[Dil modeli oluşturma](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) API 'si, belirtilen hesapta yeni bir özel dil modeli oluşturur. Bu çağrıda dil modeli için dosyaları karşıya yükleyebilirsiniz. Alternatif olarak, dili modelini buradan oluşturup dil modelini güncelleştirerek model için dosyaları karşıya yükleyebilirsiniz.

> [!NOTE]
> Dosyaların içeriğini öğrenmek için modeli etkin dosyaları ile eğitmeye devam etmeniz gerekir. Bir dilin eğitiminde yönergeler sonraki bölümde yer alır.

Dil modeline eklenecek dosyaları karşıya yüklemek için, yukarıdaki gerekli parametrelere değer sağlamaya ek olarak FormData kullanarak gövdeye dosya yüklemeniz gerekir. Bu görevi iki şekilde yapabilirsiniz:

* Anahtar dosya adı olacaktır ve değer txt dosyası olur.
* Anahtar dosya adı olacaktır ve değer txt dosyasının URL 'SI olacaktır.

### <a name="response"></a>Yanıt

Yanıt, yeni oluşturulan dil modelinde meta veriler sağlar ve bu örnek JSON çıkışı biçiminden sonra model dosyalarının her birinde meta veriler sağlar:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Dil modeli eğitme

[Dil modeli eğitme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Language-Model) API 'si, belirtilen hesapta, dil modelinde yüklenmiş ve etkin olan dosyalardaki içeriğe sahip olan özel bir dil modeli sağlar.

> [!NOTE]
> Önce dil modelini oluşturmanız ve dosyalarını karşıya yüklemeniz gerekir. Dil modelini oluştururken veya dil modelini güncelleştirerek dosyaları karşıya yükleyebilirsiniz.

### <a name="response"></a>Yanıt

Yanıt, yeni eğitilen dil modelinde meta veriler sağlar ve bu örnek JSON çıkışı biçiminden sonra model dosyalarının her birinde meta verilerle birlikte.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Döndürülen, `id` dil modellerini ayırt etmek için kullanılan benzersiz BIR kimliktir, ancak `languageModelId` video API 'leri dizine eklemek ve [yeniden dizinlemek](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) için [bir video yüklemek](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) için kullanılır (Ayrıca, `linguisticModelId` video Indexer karşıya yükleme/yeniden oluşturma API 'lerinde olarak da bilinir).

## <a name="delete-a-language-model"></a>Dil modelini silme

[Dil modeli silme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model) API 'si, belirtilen hesaptan özel bir dil modelini siler. Silinen dil modelini kullanan tüm videolar, videoyu yeniden dizinle aynı dizini tutar. Videoyu yeniden dizinle, videoya yeni bir dil modeli atayabilirsiniz. Aksi takdirde, Video Indexer videoyu yeniden eklemek için varsayılan modelini kullanır.

### <a name="response"></a>Yanıt

Dil modeli başarıyla silindiğinde döndürülen içerik yok.

## <a name="update-a-language-model"></a>Dil modelini güncelleştirme

[Dil modeli güncelleştirme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API 'si, belirtilen hesapta özel bir dil kişi modelini güncelleştirir.

> [!NOTE]
> Dil modelini zaten oluşturmuş olmanız gerekir. Bu çağrıyı, modelin altındaki tüm dosyaları etkinleştirmek veya devre dışı bırakmak, dil modelinin adını güncelleştirmek ve dil modeline eklenecek dosyaları yüklemek için kullanabilirsiniz.

Dil modeline eklenecek dosyaları karşıya yüklemek için, yukarıdaki gerekli parametrelere değer sağlamaya ek olarak FormData kullanarak gövdeye dosya yüklemeniz gerekir. Bu görevi iki şekilde yapabilirsiniz:

* Anahtar dosya adı olacaktır ve değer txt dosyası olur.
* Anahtar dosya adı olacaktır ve değer txt dosyasının URL 'SI olacaktır.

### <a name="response"></a>Yanıt

Yanıt, yeni eğitilen dil modelinde meta veriler sağlar ve bu örnek JSON çıkışı biçiminden sonra model dosyalarının her birinde meta verilerle birlikte.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

`id`Dosyanın içeriğini indirmek için yanıtta döndürülen dosyaları kullanın.

## <a name="update-a-file-from-a-language-model"></a>Dil modelinden dosya güncelleştirme

[Bir dosyayı güncelleştirme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model-file) , `enable` belirtilen hesaptaki özel dil modelinde bir dosyanın adını ve durumunu güncelleştirmenize olanak tanır.

### <a name="response"></a>Yanıt

Yanıt, aşağıdaki örnek JSON çıktısı biçimini izleyerek güncelleştirdiğiniz dosyada meta veriler sağlar.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

`id`Dosyanın içeriğini indirmek için yanıtta döndürülen dosyayı kullanın.

## <a name="get-a-specific-language-model"></a>Belirli bir dil modelini al

[Get](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model) API 'si, belirtilen hesapta belirtilen dil modeli ve dil modelindeki dosyalar gibi bilgileri döndürür.

### <a name="response"></a>Yanıt

Yanıt, belirtilen dil modelinde meta veriler sağlar ve bu örnek JSON çıkışı biçiminden sonra model dosyalarının her birinde meta verilerle birlikte.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

`id`Dosyanın içeriğini indirmek için yanıtta döndürülen dosyayı kullanın.

## <a name="get-all-the-language-models"></a>Tüm dil modellerini al

Tüm API 'leri [Al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Models) , belirtilen hesaptaki tüm özel dil modellerini bir listede döndürür.

### <a name="response"></a>Yanıt

Yanıt, hesabınızdaki tüm dil modellerinin ve bu örnek JSON çıkışının biçimini izleyen meta verileri ve dosyalarının bir listesini sağlar:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Dil modelinden dosya silme

[Delete](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model-File) API 'si belirtilen hesapta belirtilen dil modelinden belirtilen dosyayı siler.

### <a name="response"></a>Yanıt

Dosya dil modelinden başarıyla silindiğinde döndürülen içerik yok.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Dil modelinden dosya üzerinde meta verileri al

[Bir dosya API 'sinin Al meta verileri](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model-File-Data) , hesabınızdaki seçili dil modelinden belirtilen dosyadaki içeriğini ve meta verileri döndürür.

### <a name="response"></a>Yanıt

Yanıt, dosyanın içeriğini ve meta verilerini JSON biçiminde, bu örneğe benzer şekilde sağlar:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Bu örnek dosyanın içeriği, iki ayrı satırda "Merhaba" ve dünya "sözcüklerdir.

## <a name="download-a-file-from-a-language-model"></a>Dil modelinden dosya indirme

[Bir dosya indir](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Download-Language-Model-File-Content) API 'si, belirtilen dosyanın içeriğini içeren bir metin dosyasını belirtilen hesapta belirtilen dil modelinden indirir. Bu metin dosyası, özgün olarak karşıya yüklenen metin dosyasının içeriğiyle eşleşmelidir.

### <a name="response"></a>Yanıt

Yanıt, dosyanın içeriği JSON biçiminde olan bir metin dosyası olarak indirilir.

## <a name="next-steps"></a>Sonraki adımlar

[Web sitesini kullanarak dil modelini özelleştirme](customize-language-model-with-website.md)
