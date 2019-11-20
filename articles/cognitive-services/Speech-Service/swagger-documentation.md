---
title: Swagger belgeleri-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Swagger belgeleri, birkaç programlama dili için SDK 'Ları otomatik olarak oluşturmak üzere kullanılabilir. Hizmetimizdeki tüm işlemler Swagger tarafından destekleniyor
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 232435a424d2461bce4598356a986473cb1d3644
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552586"
---
# <a name="swagger-documentation"></a>Swagger belgeleri

Konuşma Hizmetleri, verileri içeri aktarmak, modeller oluşturmak, model doğruluğunu test etmek, Özel uç noktalar oluşturmak, toplu iş dökümlerini kuyruğa almak ve abonelikleri yönetmek için kullanılan el ile yapılan bir REST API 'si ile etkileşimde bulunmak için bir Swagger belirtimi sunar. Özel Konuşma Tanıma portalı aracılığıyla kullanılabilen çoğu işlem, bu API 'Ler kullanılarak programlı bir şekilde tamamlanabilir.

> [!NOTE]
> Hem konuşmadan metne hem de metinden konuşmaya işlemleri, ' de Swagger belirtiminde belgelenen REST API 'Leri olarak desteklenir.

## <a name="generating-code-from-the-swagger-specification"></a>Swagger belirtiminden kod oluşturuluyor

[Swagger belirtiminde](https://cris.ai/swagger/ui/index) , çeşitli yollar için hızlı test yapmanıza olanak sağlayan seçenekler vardır. Ancak, bazı durumlarda, gelecekteki çözümleri temel alan tek bir çağrı kitaplığı oluşturarak tüm yollar için kod oluşturulması istenebilir. Bir Python kitaplığı oluşturma işlemine göz atalım.

Swagger 'yi konuşma hizmeti aboneliğinizle aynı bölgeye ayarlamanız gerekir. Azure portal, bölgenizi konuşma Hizmetleri kaynağınızın altında doğrulayabilirsiniz. Desteklenen bölgelerin tüm listesi için bkz. [bölgeler](regions.md).

1. Şuraya gidin: https://editor.swagger.io
2. **Dosya**' ya ve ardından **içeri aktar** ' a tıklayın
3. Konuşma Hizmetleri aboneliğinizin bölgesi dahil Swagger URL 'sini girin`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. **Istemci oluştur** ' a tıklayın ve Python ' ı seçin
5. İstemci kitaplığını Kaydet

[GitHub 'Daki konuşma Hizmetleri örnekleri](https://aka.ms/csspeech/samples)Ile oluşturduğunuz Python kitaplığını kullanabilirsiniz.

## <a name="reference-docs"></a>Başvuru belgeleri

* [REST (Swagger): Toplu iş dökümü ve özelleştirme](https://westus.cris.ai/swagger/ui/index)
* [REST API: Konuşmayı metne dönüştürme](rest-speech-to-text.md)
* [REST API: Metin okuma](rest-text-to-speech.md)

## <a name="next-steps"></a>Sonraki adımlar

* [GitHub 'Da konuşma Hizmetleri örnekleri](https://aka.ms/csspeech/samples).
* [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
