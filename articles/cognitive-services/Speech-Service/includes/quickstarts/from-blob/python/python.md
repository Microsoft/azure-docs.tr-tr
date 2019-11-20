---
title: 'Hızlı başlangıç: blob depolamada depolanan konuşmayı tanıma, C# konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 40b226796b4dfb9aced3c6b00eba1a12bad66894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504028"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Azure Blob 'a kaynak dosya yükleme](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md)

## <a name="download-and-install-the-api-client-library"></a>API istemci kitaplığını indirme ve yükleme

Örneği yürütmek için, [Swagger](https://swagger.io)aracılığıyla oluşturulan REST API için Python kitaplığı oluşturmanız gerekir.

Yükleme için aşağıdaki adımları izleyin:

1. https://editor.swagger.io kısmına gidin.
1. **Dosya**' ya ve ardından **URL içeri aktar**' a tıklayın.
1. Konuşma Hizmetleri aboneliğinizin bölgesi de dahil olmak üzere Swagger URL 'sini girin: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. **Istemci oluştur** ' a tıklayın ve **Python**' ı seçin.
1. İstemci kitaplığını kaydedin.
1. İndirilen Python-Client-generated. zip dosyasını dosya sisteminizde bir yere ayıklayın.
1. PIP: `pip install path/to/package/python-client`kullanarak Python ortamınıza ayıklanan Python-Client modülünü yükler.
1. Yüklü paketin adı `swagger_client`. Yüklemenin `python -c "import swagger_client"`komutu kullanılarak çalıştığını kontrol edebilirsiniz.

> **Note:** [Swagger yeniden oluşturmada bilinen bir hata](https://github.com/swagger-api/swagger-codegen/issues/7541)nedeniyle `swagger_client` paketini içeri aktarırken hatalarla karşılaşabilirsiniz.
> Bu, satırı içerikle silerek düzeltilebilir
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> dosya `swagger_client/models/model.py` ve içeriğe sahip satır
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> dosyası, yüklü paketin içinde `swagger_client/models/inner_error.py`. Hata mesajı, bu dosyaların yüklemenizin bulunduğu yer olduğunu bildirir.

## <a name="install-other-dependencies"></a>Diğer bağımlılıkları yükler

Örnek, `requests` kitaplığını kullanır. Komutunu komutuyla yükleyebilirsiniz

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Bazı demirbaş kodla başlayın

Projemiz için bir çatı olarak çalışacak bir kod ekleyelim.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(`YourSubscriptionKey`, `YourServiceRegion`ve `YourFileUrl` değerlerini kendi değerlerinizle değiştirmeniz gerekir.)

## <a name="create-and-configure-an-http-client"></a>Http Istemcisi oluşturma ve yapılandırma
İhtiyaç duyduğumuz ilk şey, doğru temel URL ve kimlik doğrulama kümesine sahip bir http Istemcidir.
Bu kodu `transcribe` [!code-pythonekleyin [](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Döküm isteği oluştur
Ardından, döküm isteğini oluşturacağız. Bu kodu `transcribe` [!code-pythonekleyin [](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>İsteği gönder ve durumunu denetle
Artık isteği konuşma hizmetine gönderiyoruz ve ilk yanıt kodunu denetliyoruz. Bu yanıt kodu yalnızca hizmetin isteği aldığını gösterir. Hizmet, yanıt üst bilgilerinde, döküm durumunu depolayacağı konuma bir URL döndürür.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Döküm işleminin tamamlanmasını bekleyin
Hizmet, dökümü zaman uyumsuz olarak işlediğinden, her ne kadar her durumda yoklama yapması gerekir. Her 5 saniyede bir kontrol edeceğiz.

Bu konuşma hizmeti kaynağının işlediği tüm dökümlerini listelettireceğiz ve oluşturduğumuz bir arama yapacağız.

Başarılı bir tamamlama haricinde her şey için durum görüntüleme ile yoklama kodu aşağıda verilmiştir.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Döküm sonuçlarını görüntüleme
Hizmet başarıyla tamamlandığında, sonuçlar durum yanıtından elde ettiğimiz başka bir URL 'de depolanır.

Burada, bu sonuç JSON ' i alır ve onu görüntüyoruz.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Kodunuzu denetleyin
Bu noktada, kodunuz şuna benzemelidir: (Bu sürüme bazı açıklamalar ekledik) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Uygulamanızı derleyin ve çalıştırın

Artık uygulamanızı oluşturmaya ve konuşma tanıma özelliğini kullanarak konuşma tanıma 'yı test etmeye hazır olursunuz.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]
