---
title: 'Hızlı başlangıç: küçük resim oluşturma-REST, Python'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Python ile Görüntü İşleme API'si kullanarak bir görüntüden küçük resim oluşturursunuz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 042fc22e66924fb2ea1a6985f442c46061b7fc31
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176400"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Hızlı başlangıç: Görüntü İşleme REST API ve Python kullanarak küçük resim oluşturma

Bu hızlı başlangıçta, Görüntü İşleme REST API kullanarak bir görüntüden küçük resim oluşturacaksınız. [Küçük resim al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) yöntemiyle, istenen yüksekliği ve genişliği belirtebilir ve görüntü işleme akıllı kırpma kullanarak ilgilendiğiniz alanı ve bu bölgeyi temel alan kırpma koordinatları oluşturabilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/try/cognitive-services/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Görüntü İşleme için bir abonelik anahtarınız olması gerekir. Deneme bilişsel [Hizmetler](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)'den ücretsiz bir deneme anahtarı edinebilirsiniz. Ya da Görüntü İşleme abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ' daki yönergeleri izleyin. Ardından, sırasıyla `COMPUTER_VISION_SUBSCRIPTION_KEY` ve `COMPUTER_VISION_ENDPOINT` adlı anahtar ve hizmet uç noktası dizesi için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .
- [Visual Studio Code](https://code.visualstudio.com/download)gibi bir kod Düzenleyicisi.

## <a name="create-and-run-the-sample"></a>Örnek oluşturma ve çalıştırma

Örneği oluşturmak ve çalıştırmak için, kod düzenleyicisine aşağıdaki kodu kopyalayın. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Sonra, şunları yapın:
1. İsteğe bağlı olarak, `image_url` değerini, küçük resim oluşturmak istediğiniz farklı bir görüntünün URL 'siyle değiştirin.
1. Kodu `.py` uzantılı bir dosya olarak kaydedin. Örneğin, `get-thumbnail.py`.
1. Bir komut istemi penceresi açın.
1. İstemde, örneği çalıştırmak için `python` komutunu kullanın. Örneğin, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Yanıtı inceleyin

Başarılı bir yanıt, küçük resim için görüntü verilerini temsil eden ikili veriler olarak döndürülür. Örnek bu görüntüyü görüntülemelidir. İstek başarısız olursa, yanıt komut istemi penceresinde görüntülenir ve bir hata kodu içermelidir.

## <a name="run-in-jupyter-optional"></a>Jupyıter 'da Çalıştır (isteğe bağlı)

Bu hızlı başlangıcı, [Myciltçi](https://mybinder.org)üzerinde bir Jupyter Not defteri kullanarak bir adım adım biçimde çalıştırabilirsiniz. Cildi başlatmak için aşağıdaki düğmeyi seçin:

[![Ciltçi](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, küçük resim oluşturma özelliği hakkında daha ayrıntılı bilgi edinin.

> [!div class="nextstepaction"]
> [Küçük resim oluşturma](../concept-generating-thumbnails.md)
