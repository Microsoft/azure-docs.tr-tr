---
title: Görüntüde yüzleri algılayın
titleSuffix: Azure Cognitive Services
description: Bu kılavuzda, belirli bir görüntüden cinsiyet, Age veya poz gibi öznitelikleri ayıklamak için yüz algılamanın nasıl kullanılacağı gösterilmektedir.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 71e98b735b4aa4631d73f8730a48c56a8c7585ab
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497651"
---
# <a name="get-face-detection-data"></a>Yüz algılama verilerini al

Bu kılavuzda, belirli bir görüntüden cinsiyet, Age veya poz gibi öznitelikleri ayıklamak için yüz algılamanın nasıl kullanılacağı gösterilmektedir. Bu kılavuzdaki kod parçacıkları, Azure bilişsel hizmetler yüz istemci kitaplığı kullanılarak C# dilinde yazılmıştır. Aynı işlevsellik [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)ile kullanılabilir.

Bu kılavuzda nasıl yapılacağı gösterilmektedir:

- Görüntüdeki yüzlerin konumlarını ve boyutlarını alın.
- Bir görüntüde pupıls, burun ve ağız gibi çeşitli yüzlerin konumlarını alın.
- Bir algılanan yüzün cinsiyeti, yaşını, duygu ve diğer özniteliklerini tahmin edin.

## <a name="setup"></a>Kurulum

Bu kılavuzda [](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) `faceClient` , bir yüz abonelik anahtarı ve uç nokta URL 'si olan adlı bir faceclient nesnesi oluşturmuş olduğunuz varsayılmaktadır. Buradan, bu kılavuzda kullanılan [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), ya da [Detectwithstreamasync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync)' i çağırarak yüz algılama özelliğini kullanabilirsiniz. Bu özelliği ayarlama hakkında yönergeler için hızlı başlangıçlardan birini izleyin.

Bu kılavuz, hangi bağımsız değişkenleri geçebileceğinize ve döndürülen verilerle yapabileceklerinize ilişkin olarak, algılama çağrısının özelliklerine odaklanır. Yalnızca ihtiyacınız olan özellikleri sorgulamanızı öneririz. Her işlemin tamamlanabilmesi için ek süre sürer.

## <a name="get-basic-face-data"></a>Temel yüz verilerini al

Yüzeyleri bulmak ve konumlarını bir görüntüde almak için, [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) veya [Detectwithstreamasync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) yöntemini, _returnceıd_ parametresi **true** olarak ayarlanmış şekilde çağırın. Bu varsayılan ayardır.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Döndürülen [Detectedface](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) nesnelerini benzersiz kimlikleri ve yüzün piksel koordinatlarını sağlayan bir dikdörtgen için sorgulayabilirsiniz.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Yüzün konumunu ve boyutlarını ayrıştırma hakkında daha fazla bilgi için bkz. [Facerectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Bu dikdörtgen genellikle gözler, eyebro, burun ve ağız içerir. Baş, korun ve Chin en üstü dahil değildir. Yüz dikdörtgenini, tam bir kafa kırpmak veya orta ölçekli bir dikey almak için, belki de fotoğraf KIMLIĞI türünde bir görüntü için dikdörtgeni her yönde genişletebilirsiniz.

## <a name="get-face-landmarks"></a>Yüz çizgilerini al

[Yüz yüzleri](../concepts/face-detection.md#face-landmarks) , bir yüz üzerinde yer alan, pupıls veya burun ipucu gibi kolay bir arama noktası kümesidir. Yüz yer işareti verilerini almak için _detectionModel_ parametresini **detectionModel. Detection01** ve _returncelandiþaretleri_ parametresini **true** olarak ayarlayın.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Aşağıdaki kod, burun ve pupler 'in konumlarını nasıl alacağınızı gösterir:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Yüzün yönünü doğru şekilde hesaplamak için yüz surtik verilerini de kullanabilirsiniz. Örneğin, yüzün merkezinden bir vektör olarak dönüşü, gözlerinizin merkezine bir vektör olarak tanımlayabilirsiniz. Aşağıdaki kod bu vektörü hesaplar:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Yüzün yönünü bildiğiniz zaman, dikdörtgen yüzey çerçevesini daha doğru hizalamak için döndürebilirsiniz. Görüntüdeki yüzeyleri kırpmak için, yüzeyleri her zaman sağ görünecek şekilde görüntüyü program aracılığıyla döndürebilirsiniz.

## <a name="get-face-attributes"></a>Yüz özniteliklerini Al

Yüz algılama API 'SI, yüz dikdörtgeni ve yer işaretlerinin yanı sıra bir yüzün çeşitli kavramsal özniteliklerini analiz edebilir. Tam liste için, [yüz nitelikleri](../concepts/face-detection.md#attributes) kavramsal bölümüne bakın.

Yüz özniteliklerini çözümlemek için _detectionModel_ parametresini **detectionModel. Detection01** ve _returnfaceattributes_ parametresini bir [faceattributetype numaralandırma](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) değerleri listesine ayarlayın.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Ardından, döndürülen verilere yönelik başvuruları alın ve gereksinimlerinize göre daha fazla işlem yapın.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Özniteliklerin her biri hakkında daha fazla bilgi edinmek için, [yüz algılama ve öznitelikler](../concepts/face-detection.md) kavramsal Kılavuzu ' na bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, çeşitli yüz algılama işlevlerini nasıl kullanacağınızı öğrendiniz. Sonra, kullanıcılardan yüz verileri eklemek için bu özellikleri bir uygulamayla tümleştirin.

- [Öğretici: bir yüz hizmetine Kullanıcı ekleme](../enrollment-overview.md)

## <a name="related-topics"></a>İlgili konular

- [Başvuru belgeleri (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Başvuru belgeleri (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
