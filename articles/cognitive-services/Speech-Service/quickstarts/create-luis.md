---
title: 'Hızlı başlangıç: bir LUSıS anahtarı oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, bir LUO uygulamasının nasıl oluşturulduğunu ve bir anahtarın nasıl alınacağını öğreneceksiniz.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: wolfma
ms.openlocfilehash: 2ee2b25958feac2387779d9e60734a6600b60a2e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682066"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Hızlı başlangıç: bir LUSıS uç noktası anahtarı alma

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce aşağıdaki öğelere sahip olduğunuzdan emin olun:

* LUIS hesabı. [LUIS portalından](https://www.luis.ai/home) ücretsiz bir hesap alabilirsiniz.

## <a name="luis-and-speech"></a>LUIS ve konuşma

LU, konuşmadan amaçları tanımak için konuşma hizmetleriyle tümleştirilir. Konuşma Hizmetleri aboneliğine ihtiyacınız yoktur, yalnızca LUO.

LUSıS üç tür anahtar kullanır:

|Anahtar türü|Amaç|
|--------|-------|
|Yazma|LUSıS uygulamalarını programlı bir şekilde oluşturmanızı ve değiştirmenizi sağlar|
|Başlangıç|LUSıS uygulamanızı yalnızca metin kullanarak test etmenizi sağlar|
|Uç Nokta |Belirli bir Lua uygulamasına erişim yetkisi verir|

Bu öğretici için uç nokta anahtar türüne ihtiyacınız vardır. Öğretici, [önceden oluşturulmuş giriş Otomasyonu uygulama](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) hızlı başlangıcı ' nı Izleyerek oluşturabileceğiniz GIRIŞ Otomasyonu Luo uygulaması örneğini kullanır. Kendi bir LUSıS uygulaması oluşturduysanız bunun yerine kullanabilirsiniz.

Bir LUSıS uygulaması oluşturduğunuzda, bu uygulamayı metin sorgularını kullanarak test edebilmeniz için, LUSıS otomatik olarak bir başlangıç anahtarı oluşturur. Bu anahtar, konuşma Hizmetleri tümleştirmesini etkinleştirmez ve bu öğreticiyle çalışmaz. Azure panosunda bir LUSıS kaynağı oluşturun ve bunu LUO uygulamasına atayın. Bu öğretici için ücretsiz abonelik katmanını kullanabilirsiniz.

Azure panosu 'nda LUO kaynağını oluşturduktan sonra, [Halu portalında](https://www.luis.ai/home)oturum açın, **uygulamalarım** sayfasında uygulamanızı seçin, sonra uygulamanın **Yönet** sayfasına geçin. Son olarak, kenar çubuğunda **anahtarlar ve uç noktalar** ' ı seçin.

![LUIS portalı anahtarları ve uç nokta ayarları](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

**Anahtarlar ve uç nokta ayarları** sayfasında:

1. Aşağı kaydırarak **kaynaklar ve anahtarlar** bölümüne gidin ve **kaynak ata**' yı seçin.
1. **Uygulamanıza anahtar ata** iletişim kutusunda aşağıdaki değişiklikleri yapın:

   * **Kiracı**altında **Microsoft**' u seçin.
   * **Abonelik adı**bölümünde, kullanmak istediğiniz Luo kaynağını içeren Azure aboneliğini seçin.
   * **Anahtar**altında uygulamayla birlikte kullanmak istediğiniz Luo kaynağını seçin.

   Kısa süre içinde yeni abonelik sayfanın altındaki tabloda görüntülenir.

1. Panoya kopyalamak için bir anahtarın yanındaki simgeyi seçin. (İstediğiniz anahtarı kullanabilirsiniz.)

![LUIS app abonelik anahtarları](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Amaçları tanıma](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
