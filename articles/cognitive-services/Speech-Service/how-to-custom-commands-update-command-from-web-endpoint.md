---
title: Web uç noktasından bir komutu güncelleştirme
titleSuffix: Azure Cognitive Services
description: Bir Web uç noktasına çağrı kullanarak bir komutun durumunu güncelleştirme hakkında bilgi edinin.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560279"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Web uç noktasından bir komutu güncelleştirme

İstemci uygulamanız, ses girişi olmadan devam eden bir komutun durumunda bir güncelleştirme gerektiriyorsa, komutu güncelleştirmek için bir Web uç noktası çağrısı kullanabilirsiniz.

Bu makalede, bir Web uç noktasından devam eden bir komutun nasıl güncelleştireceğinizi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar
> [!div class = "checklist"]
> * Önceden oluşturulmuş [Özel Komutlar uygulaması](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure işlevi oluşturma 

Bu örnekte, aşağıdaki girişi (veya bu girişin bir alt kümesini) destekleyen HTTP ile tetiklenen bir [Azure işlevi](../../azure-functions/index.yml) gerekir:

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Bu girdinin anahtar özniteliklerini gözden geçirelim:

| Öznitelik | Açıklama |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **ConversationId** | Görüşmenin benzersiz tanımlayıcısı. Bu KIMLIğIN istemci uygulamasından oluşturulup oluşturulmadığını unutmayın. |
| **currentCommand** | Şu anda konuşmada etkin olan komut. |
| **ada** | Komutun adı. `parameters`Öznitelik, parametrelerin geçerli değerlerini içeren bir eşlemedir. |
| **currentGlobalParameters** | Gibi bir eşlem `parameters` , ancak genel parametreler için kullanılır. |

Azure işlevinin çıktısının aşağıdaki biçimi desteklemesi gerekir:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

[İstemciden bir komut güncelleştirirken](./how-to-custom-commands-update-command-from-client.md)kullandığınız aynı olduğundan, bu biçimi görebilirsiniz. 

Şimdi, Node.js dayalı bir Azure işlevi oluşturun. Bu kodu kopyala/yapıştır:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Bu Azure işlevini özel komutlardan çağırdığınızda görüşmenin geçerli değerlerini gönderirsiniz. Güncelleştirmek istediğiniz parametreleri veya geçerli komutu iptal etmek istiyorsanız bu parametreleri döndürebilirsiniz.

## <a name="update-the-existing-custom-commands-app"></a>Var olan özel komutlar uygulamasını güncelleştirme

Azure işlevini var olan özel komutlar uygulamasıyla yedeklim:

1. Adlı yeni bir komut ekleyin `IncrementCounter` .
1. Değeri ile yalnızca bir örnek tümce ekleyin `increment` .
1. `Counter`Varsayılan değeri ile türünün adlı yeni bir parametre (Azure işlevinde belirtilen aynı ad) ekleyin `Number` `0` .
1. `IncrementEndpoint` **Uzak güncelleştirmeler** **etkin** olarak ayarlanmış şekılde, Azure işlevinizin URL 'siyle birlikte adlı yeni bir Web uç noktası ekleyin.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Uzak güncelleştirmelerle bir Web uç noktası ayarlamayı gösteren ekran görüntüsü.":::
1. **IncrementRule** adlı yeni bir etkileşim kuralı oluşturun ve **çağrı Web uç noktası** eylemi ekleyin.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Bir etkileşim kuralının oluşturulmasını gösteren ekran görüntüsü.":::
1. Eylem yapılandırması ' nda, öğesini seçin `IncrementEndpoint` . Değeri ile **konuşma yanıtı gönderme** **başarısını** yapılandırın `Counter` ve hata iletisiyle **başarısız** olarak yapılandırın.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Web uç noktası çağırmak için bir artış sayacı ayarlamanın gösterildiği ekran görüntüsü.":::
1. **Kullanıcının girişini beklemek** için kuralın yürütme sonrası durumunu ayarlayın.

## <a name="test-it"></a>Test etme

1. Uygulamanızı kaydedin ve eğitme.
1. **Test**'i seçin.
1. `increment`Birkaç kez gönderin (komutun örnek cümlesi olur `IncrementCounter` ).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Artış sayacı örneği gösteren ekran görüntüsü.":::

Azure işlevinin her bir sırayla parametresinin değerini nasıl artırdığına dikkat edin `Counter` .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel Komutlar uygulamanız için CI/CD sürecini etkinleştirme](./how-to-custom-commands-deploy-cicd.md)