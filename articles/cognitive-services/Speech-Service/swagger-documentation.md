---
title: Swagger belgeleri-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Swagger belgeleri, birkaç programlama dili için SDK 'Ları otomatik olarak oluşturmak üzere kullanılabilir. Hizmetimizdeki tüm işlemler Swagger tarafından destekleniyor
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100636277"
---
# <a name="swagger-documentation"></a>Swagger belgeleri

Konuşma hizmeti, verileri içeri aktarmak, modeller oluşturmak, model doğruluğu test etmek, Özel uç noktalar oluşturmak, toplu iş dökümlerini kuyruğa almak ve abonelikleri yönetmek için kullanılan el ile yapılan bir REST API 'si ile etkileşimde bulunmak için bir Swagger belirtimi sunar. [Konuşma Studio 'nun özel konuşma tanıma alanı](https://aka.ms/customspeech) aracılığıyla kullanılabilen çoğu işlem, bu API 'ler kullanılarak programlı bir şekilde tamamlanabilir.

> [!NOTE]
> Konuşma hizmetinde, [konuşmadan metne](rest-speech-to-text.md) ve [metinden konuşmaya](rest-text-to-speech.md)kadar birkaç REST API 'si vardır.  
>
> Ancak yalnızca [konuşmayı metne REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) ve v 2.0, Swagger belirtiminde belgelenmiştir. Diğer tüm konuşma Hizmetleri REST API 'Leri hakkında daha fazla bilgi için önceki paragrafta başvurulan belgelere bakın.

## <a name="generating-code-from-the-swagger-specification"></a>Swagger belirtiminden kod oluşturuluyor

[Swagger belirtiminde](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) , çeşitli yollar için hızlı test yapmanıza olanak sağlayan seçenekler vardır. Ancak, bazı durumlarda, gelecekteki çözümleri temel alan tek bir çağrı kitaplığı oluşturarak tüm yollar için kod oluşturulması istenebilir. Bir Python kitaplığı oluşturma işlemine göz atalım.

Swagger 'yi konuşma kaynağınızın bölgesine ayarlamanız gerekir. Azure portal ' de konuşma kaynak ayarlarınızın **genel bakış** bölümünde yer alan bölgeyi doğrulayabilirsiniz. Desteklenen bölgelerin tüm listesi [burada](regions.md#speech-to-text)bulunabilir.

1. Bir tarayıcıda, [bölgeniz](regions.md#speech-to-text)için Swagger belirtimine gidin:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Bu sayfada, **API tanımı**' na tıklayın ve **Swagger**' ye tıklayın. Görüntülenen sayfanın URL 'sini kopyalayın.
1. Yeni bir tarayıcıda şuraya gidin [https://editor.swagger.io](https://editor.swagger.io)
1. **Dosya**, URL 'Yi **içeri aktar**' a tıklayın, URL 'yi yapıştırın ve **Tamam**' a tıklayın.
1. **Istemci oluştur** ' a tıklayın ve **Python**' ı seçin. İstemci kitaplığı bir dosyadaki bilgisayarınıza indirilir `.zip` .
1. İndirmenin her şeyi ayıklayın. `tar -xf`Her şeyi ayıklamak için kullanabilirsiniz.
1. Ayıklanan modülün Python ortamınıza yüklenmesi:  
      `pip install path/to/package/python-client`
1. Yüklü paketin adı `swagger_client` . Yüklemenin çalıştığını denetleyin:  
       `python -c "import swagger_client"`

[GitHub 'Daki konuşma hizmeti örnekleri](https://aka.ms/csspeech/samples)Ile oluşturduğunuz Python kitaplığını kullanabilirsiniz.

## <a name="reference-documents"></a>Başvuru belgeleri

* [Swagger: konuşmadan metne REST API v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Konuşmayı metne dönüştürme REST API'si](rest-speech-to-text.md)
* [Metin okuma REST API'si](rest-text-to-speech.md)

## <a name="next-steps"></a>Sonraki adımlar

* [GitHub 'Da konuşma hizmeti örnekleri](https://aka.ms/csspeech/samples).
* [Ücretsiz bir konuşma hizmeti abonelik anahtarı alın](overview.md#try-the-speech-service-for-free)
