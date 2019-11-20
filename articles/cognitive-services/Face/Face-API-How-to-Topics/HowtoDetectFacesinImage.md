---
title: Görüntüdeki yüzeyleri algılama-Yüz Tanıma API'si
titleSuffix: Azure Cognitive Services
description: Bu kılavuzda, belirli bir görüntüden cinsiyet, Age veya poz gibi öznitelikleri ayıklamak için yüz algılamanın nasıl kullanılacağı gösterilmektedir.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 9b66231d995ffb6980ce36852115c571cd102681
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744287"
---
# <a name="get-face-detection-data"></a>Yüz algılama verilerini al

Bu kılavuzda, belirli bir görüntüden cinsiyet, Age veya poz gibi öznitelikleri ayıklamak için yüz algılamanın nasıl kullanılacağı gösterilmektedir. Bu kılavuzdaki kod parçacıkları, Azure bilişsel C# Hizmetler Yüz Tanıma API'si istemci kitaplığı kullanılarak içine yazılır. Aynı işlevsellik [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)ile kullanılabilir.

Bu kılavuzda nasıl yapılacağı gösterilmektedir:

- Görüntüdeki yüzlerin konumlarını ve boyutlarını alın.
- Bir görüntüde pupıls, burun ve ağız gibi çeşitli yüzlerin konumlarını alın.
- Bir algılanan yüzün cinsiyeti, yaşını, duygu ve diğer özniteliklerini tahmin edin.

## <a name="setup"></a>Kurulum

Bu kılavuzda, bir yüz abonelik anahtarı ve uç nokta URL 'SI ile `faceClient`adlı bir [Faceclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) nesnesi oluşturmuş olduğunuz varsayılmaktadır. Buradan, bu kılavuzda kullanılan [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), ya da [Detectwithstreamasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)' i çağırarak yüz algılama özelliğini kullanabilirsiniz. Bu özelliği ayarlama hakkında yönergeler için hızlı başlangıçlardan birini izleyin.

Bu kılavuz, hangi bağımsız değişkenleri geçebileceğinize ve döndürülen verilerle yapabileceklerinize ilişkin olarak, algılama çağrısının özelliklerine odaklanır. Yalnızca ihtiyacınız olan özellikleri sorgulamanızı öneririz. Her işlemin tamamlanabilmesi için ek süre sürer.

## <a name="get-basic-face-data"></a>Temel yüz verilerini al

Yüzeyleri bulmak ve konumlarını bir görüntüde almak için, _Returnceıd_ parametresi **true**olarak ayarlanan yöntemi çağırın. Bu varsayılan ayardır.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Döndürülen [Detectedface](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) nesnelerini benzersiz kimlikleri ve yüzün piksel koordinatlarını sağlayan bir dikdörtgen için sorgulayabilirsiniz.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Yüzün konumunu ve boyutlarını ayrıştırma hakkında daha fazla bilgi için bkz. [Facerectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Bu dikdörtgen genellikle gözler, eyebro, burun ve ağız içerir. Baş, korun ve Chin en üstü dahil değildir. Yüz dikdörtgenini, tam bir kafa kırpmak veya orta ölçekli bir dikey almak için, belki de fotoğraf KIMLIĞI türünde bir görüntü için dikdörtgeni her yönde genişletebilirsiniz.

## <a name="get-face-landmarks"></a>Yüz çizgilerini al

[Yüz yüzleri](../concepts/face-detection.md#face-landmarks) , bir yüz üzerinde yer alan, pupıls veya burun ipucu gibi kolay bir arama noktası kümesidir. Yüz yer işareti verilerini almak için, _Returncelandiþaretleri_ parametresini **true**olarak ayarlayın.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Aşağıdaki kod, burun ve pupler 'in konumlarını nasıl alacağınızı gösterir:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Yüzün yönünü doğru şekilde hesaplamak için yüz surtik verilerini de kullanabilirsiniz. Örneğin, yüzün merkezinden bir vektör olarak dönüşü, gözlerinizin merkezine bir vektör olarak tanımlayabilirsiniz. Aşağıdaki kod bu vektörü hesaplar:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Yüzün yönünü bildiğiniz zaman, dikdörtgen yüzey çerçevesini daha doğru hizalamak için döndürebilirsiniz. Görüntüdeki yüzeyleri kırpmak için, yüzeyleri her zaman sağ görünecek şekilde görüntüyü program aracılığıyla döndürebilirsiniz.

## <a name="get-face-attributes"></a>Yüz özniteliklerini Al

Yüz algılama API 'SI, yüz dikdörtgeni ve yer işaretlerinin yanı sıra bir yüzün çeşitli kavramsal özniteliklerini analiz edebilir. Tam liste için, [yüz nitelikleri](../concepts/face-detection.md#attributes) kavramsal bölümüne bakın.

Yüz özniteliklerini çözümlemek için, _Returnfaceattributes_ parametresini [Faceattributetype numaralandırma](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) değerlerinin bir listesine ayarlayın.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Ardından, döndürülen verilere yönelik başvuruları alın ve gereksinimlerinize göre daha fazla işlem yapın.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Özniteliklerin her biri hakkında daha fazla bilgi edinmek için, [yüz algılama ve öznitelikler](../concepts/face-detection.md) kavramsal Kılavuzu ' na bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, çeşitli yüz algılama işlevlerini nasıl kullanacağınızı öğrendiniz. Daha sonra, derinlemesine bir öğreticiyi izleyerek bu özellikleri uygulamanız ile tümleştirin.

- [Öğretici: görüntüde yüz verileri göstermek için WPF uygulaması oluşturma](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Öğretici: görüntüdeki yüzeyleri algılamak ve çerçeveye eklemek için bir Android uygulaması oluşturma](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>İlgili konular

- [Başvuru belgeleri (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Başvuru belgeleri (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)