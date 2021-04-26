---
title: Özel Görüntü İşleme projesi için bir etki alanı seçin-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Bu makalede, Özel Görüntü İşleme Hizmeti projeniz için bir etki alanı seçme gösterilmektedir.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 8aba6f13957d37f843114572f001029baf41ded6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889357"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Özel Görüntü İşleme projesi için bir etki alanı seçin

Özel Görüntü İşleme projenizin ayarlar sekmesinde, projeniz için bir etki alanı seçebilirsiniz. Senaryonuza en yakın etki alanını seçin. Özel Görüntü İşleme istemci kitaplığı veya REST API üzerinden erişiyorsanız, projeyi oluştururken bir etki alanı KIMLIĞI belirtmeniz gerekir. Etki [alanlarını al](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)' ı kullanarak etki alanı kimliklerinin bir listesini alabilir veya aşağıdaki tabloyu kullanabilirsiniz.

## <a name="image-classification"></a>Resimleri Sınıflandırma

|Etki alanı|Amaç|
|---|---|
|__Genel__| Çok çeşitli resim Sınıflandırma görevleri için iyileştirilmiştir. Diğer belirli etki alanlarından hiçbiri uygun değilse veya hangi etki alanının seçeceğinizden emin değilseniz, genel etki alanlarından birini seçin. NUMARASıNı `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Genel [a1]__| Benzer bir çıkarım süresi genel etki alanı olarak daha iyi doğruluk için iyileştirilmiştir. Daha büyük veri kümeleri veya daha zor kullanıcı senaryoları için önerilir. Bu etki alanı için daha fazla eğitim süresi gerekiyor. NUMARASıNı `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Genel [a2]__| Genel [a1] ve genel etki alanlarından daha hızlı çıkarım süresi ile daha iyi doğruluk için iyileştirilmiştir. Çoğu veri kümesi için önerilir. Bu etki alanı, genel ve genel [a1] etki alanlarından daha az eğitim süresi gerektirir. NUMARASıNı `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Yemek__|Bir restoran menüsünde gördüğünüz gibi yemeklerin fotoğrafları için iyileştirildi. Bireysel meyve veya vegetables fotoğraflarını sınıflandırmak istiyorsanız, yiyecek etki alanını kullanın. NUMARASıNı `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Bölümler__|Hem doğal hem de yapay olan tanınabilir yer işaretleri için iyileştirilmiştir. Bu etki alanı, yer işareti fotoğrafta açık bir şekilde görünür olduğunda en iyi şekilde çalışıyor. Bu etki alanı, yer işareti, önündeki kişiler tarafından biraz engelde olsa bile çalışıyor. NUMARASıNı `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Perakende__|Bir alışveriş kataloğunda veya alışveriş web sitesinde bulunan görüntüler için iyileştirilmiştir. Dresler, Pants ve Shirts arasında yüksek duyarlıklı sınıflandırmaları istiyorsanız bu etki alanını kullanın. NUMARASıNı `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Compact etki alanları__| Edge cihazlarında gerçek zamanlı sınıflandırmanın kısıtlamaları için iyileştirildi.|


> [!NOTE]
> Genel [a1] ve genel [a2] etki alanları, çok sayıda senaryo için kullanılabilir ve doğruluk açısından iyileştirilmiştir. Daha iyi çıkarım hızı ve daha kısa eğitim süresi için genel [a2] modelini kullanın. Daha büyük veri kümeleri için genel [a1] öğesini Genel [a2] değerinden daha iyi bir şekilde işleyecek şekilde kullanmak isteyebilirsiniz, ancak daha fazla eğitim ve çıkarım süresi gerektirir. Genel model, genel [a1] ve genel [a2] değerinden daha fazla çıkarım süresi gerektirir.

## <a name="object-detection"></a>Nesne Algılama

|Etki alanı|Amaç|
|---|---|
|__Genel__| Çok çeşitli nesne algılama görevleri için iyileştirilmiştir. Diğer etki alanlarından hiçbiri uygun değilse veya hangi etki alanının seçeceğinizden emin değilseniz, genel etki alanını seçin. NUMARASıNı `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Genel [a1]__| Benzer bir çıkarım süresi genel etki alanı olarak daha iyi doğruluk için iyileştirilmiştir. Daha doğru bölge konumu gereksinimlerine, daha büyük veri kümelerine veya daha zor Kullanıcı senaryolarına önerilir. Bu etki alanı daha fazla eğitim süresi gerektiriyor ve sonuçlar belirleyici değil: belirtilen eğitim verileriyle birlikte bir +-1% ortalama duyarlık (eşleme) farkı bekliyor. NUMARASıNı `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logo__|Görüntülerde marka logolarını bulmak için iyileştirildi. NUMARASıNı `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Rafların ürünleri__|Rafların ürünlerini algılamak ve sınıflandırmak için iyileştirildi. NUMARASıNı `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Compact etki alanları__| Uç cihazlarda gerçek zamanlı nesne algılama kısıtlamaları için iyileştirilmiştir.|

## <a name="compact-domains"></a>Compact etki alanları

Compact etki alanları tarafından oluşturulan modeller yerel olarak çalışacak şekilde aktarılabilir. Özel Görüntü İşleme 3,4 genel önizleme API 'sinde, GetDomains API 'sini çağırarak, Compact etki alanları için dışarı aktarılabilir platformların bir listesini alabilirsiniz.

Model performansı seçilen etki alanına göre değişir. Aşağıdaki tabloda, Intel Masaüstü CPU ve NVIDIA GPU 1 ' deki model boyutunu ve çıkarım zamanını raporlarız \[ \] . Bu numaralar ön işleme ve anında işleme süresini içermez.

|Görev|Etki alanı|ID|Model Boyutu|CPU çıkarımı süresi|GPU çıkarımı süresi|
|---|---|---|---|---|---|
|Sınıflandırma|General (compact) (Genel (kompakt))|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 MS|5 MS|
|Sınıflandırma|Genel (Compact) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 MS|5 MS|
|Nesne Algılama|General (compact) (Genel (kompakt))|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 MS|
|Nesne Algılama|Genel (Compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 MS|

>[!NOTE]
>Nesne algılaması için __Genel (Compact)__ etki alanı özel bir özelleştirmediğiniz mantığı gerektirir. Ayrıntılı bilgi için lütfen bkz. bir örnek komut dosyası. Gönderme işlemi mantığı olmadan bir modele ihtiyacınız varsa __Genel (Compact) [S1]__ kullanın.

>[!IMPORTANT]
>Verme modellerinin, buluttaki tahmin API 'siyle tam olarak aynı sonucu vereceğiyle ilgili hiçbir garanti yoktur. Çalışan platformda veya ön işleme uygulamasındaki hafif fark, model çıkışları üzerinde daha büyük farka neden olabilir. Ön işleme mantığının ayrıntıları için lütfen [Bu belgeye](quickstarts/image-classification.md)bakın.

\[1 \] Intel Xeon E5-2690 CPU ve NVIDIA Tesla M60
