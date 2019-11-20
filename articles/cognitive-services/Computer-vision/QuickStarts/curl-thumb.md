---
title: 'Hızlı başlangıç: küçük resim oluşturma-REST, kıvrımlı'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Görüntü İşleme API'si kıvrımlı kullanarak görüntüden bir küçük resim oluşturursunuz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 350bc95e08aa994e4cb70db6bf1f08d53bbec5a3
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177298"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Hızlı başlangıç: Görüntü İşleme REST API ve kıvrımlı kullanarak küçük resim oluşturma

Bu hızlı başlangıçta, Görüntü İşleme REST API kullanarak görüntüden bir küçük resim oluşturursunuz. İstenen yüksekliği ve genişliği belirtirsiniz, bu da giriş görüntüsünden en fazla farklılık gösterebilir. Görüntü İşleme, ilgilendiğiniz alanı saptamak ve bu bölge etrafında kırpma koordinatları oluşturmak için akıllı kırpma kullanır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Kıvrımlı](https://curl.haxx.se/windows)olmalıdır.
- Görüntü İşleme için bir abonelik anahtarınız olması gerekir. Deneme bilişsel [Hizmetler](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)'den ücretsiz bir deneme anahtarı edinebilirsiniz. Ya da Görüntü İşleme abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ' daki yönergeleri izleyin.

## <a name="get-thumbnail-request"></a>Küçük resim isteği al

[Küçük resim al yöntemiyle](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)bir görüntünün küçük resmini oluşturabilirsiniz.

Örneği çalıştırmak için aşağıdaki adımları uygulayın:

1. Aşağıdaki kodu bir düzenleyiciye kopyalayın.
1. @No__t-0 değerini geçerli abonelik anahtarınızla değiştirin.
1. Küçük resmin kaydedileceği `<File>` ' i yol ve dosya adıyla değiştirin.
1. Gerekirse, abonelik anahtarlarınızı aldığınız konumu kullanmak için Istek URL 'sini (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.1`) değiştirin.
1. İsteğe bağlı olarak, görüntüyü (`{\"url\":\"...`) analiz edilecek şekilde değiştirin.
1. Bir bilgisayarda bir pencere üzerine bir komut penceresi açın.
1. Penceredeki kodu yapıştırın ve komutunu çalıştırın.

>[!NOTE]
>Abonelik anahtarlarınızı elde etmek için kullandığınız gibi REST çağrınızdaki aynı konumu kullanmanız gerekir. Örneğin, westus adresinden abonelik anahtarlarınızı edindiyseniz, aşağıdaki URL 'deki "westcentralus" değerini "westus" ile değiştirin.

## <a name="create-and-run-the-sample-command"></a>Örnek komutunu oluşturma ve çalıştırma

Örneği oluşturmak ve çalıştırmak için aşağıdaki adımları uygulayın:

1. Aşağıdaki komutu bir metin düzenleyicisine kopyalayın.
1. Komutta gereken yerde aşağıdaki değişiklikleri yapın:
    1. @No__t-0 değerini abonelik anahtarınızla değiştirin.
    1. @No__t-0 değerini, küçük resmin kaydedileceği dosyanın yolu ve adıyla değiştirin.
    1. İstek URL 'sinin (`westcentralus`) ilk kısmını kendi uç nokta URL 'inizdeki metinle değiştirin.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. İsteğe bağlı olarak, istek gövdesinde (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) görüntü URL 'sini, küçük resim oluşturmak için farklı bir görüntünün URL 'si ile değiştirin.
1. Bir komut istemi penceresi açın.
1. Komutu metin düzenleyicisinden komut istemi penceresine yapıştırın ve sonra komutu çalıştırın.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Yanıtı inceleyin

Başarılı bir yanıt, küçük resim görüntüsünü `<thumbnailFile>` ' da belirtilen dosyaya yazar. İstek başarısız olursa, yanıt neyin yanlış olduğunu belirlemenize yardımcı olmak için bir hata kodu ve bir ileti içerir. İstek başarılı olursa ancak oluşturulan küçük resim geçerli bir görüntü dosyası değilse, abonelik anahtarınız geçersiz olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bir görüntüyü analiz etme, ünlüler ve yer işaretlerini belirleme, küçük resim oluşturma ve yazdırılmış ve el yazısı metinleri ayıklama için Görüntü İşleme API'si keşfeder. Görüntü İşleme API'si hızlı bir şekilde denemek için, [Açık API test konsolunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)deneyin.

> [!div class="nextstepaction"]
> [Görüntü İşleme API'si keşfet](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
