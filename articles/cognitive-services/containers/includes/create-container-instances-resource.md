---
title: Kapsayıcı desteği
titleSuffix: Azure Cognitive Services
description: Azure Container Instance kaynağı oluşturmayı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 24f6052c436b73d0075371fa74160d21826e2209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97866022"
---
## <a name="create-an-azure-container-instance-resource-using-the-azure-portal"></a>Azure portal kullanarak bir Azure Container Instance kaynağı oluşturma

1. Container Instances için [Oluştur](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) sayfasına gidin.

2. **Temel bilgiler** sekmesinde, aşağıdaki ayrıntıları girin:

    |Ayar|Değer|
    |--|--|
    |Abonelik|Aboneliğinizi seçin.|
    |Kaynak grubu|Kullanılabilir kaynak grubunu seçin veya gibi yeni bir tane oluşturun `cognitive-services` .|
    |Kapsayıcı adı|Gibi bir ad girin `cognitive-container-instance` . Ad, daha küçük bir harf olmalıdır.|
    |Konum|Dağıtım için bir bölge seçin.|
    |Görüntü türü|Kapsayıcı resminiz kimlik bilgileri gerektirmeyen bir kapsayıcı kayıt defterinde depolanıyorsa, öğesini seçin `Public` . Kapsayıcı yansımanıza erişim için kimlik bilgileri gerekiyorsa, öğesini seçin `Private` . Kapsayıcı görüntüsünün [](../container-image-tags.md) mi yoksa `Public` `Private` ("genel önizleme") olup olmadığı hakkında ayrıntılar için kapsayıcı depoları ve resimlere bakın. |
    |Görüntü adı|Bilişsel hizmetler kapsayıcı konumunu girin. Konum, komuta bir bağımsız değişken olarak kullanılan şeydir `docker pull` . Kullanılabilir görüntü adları ve bunlara karşılık gelen depo için [kapsayıcı depoları ve görüntüleri](../container-image-tags.md) inceleyin.<br><br>Görüntü adı, üç bölümden oluşan tam olarak nitelenmiş olmalıdır. İlk olarak, kapsayıcı kayıt defteri, ardından depo, son olarak görüntü adı: `<container-registry>/<repository>/<image-name>` .<br><br>Azure bilişsel `mcr.microsoft.com/azure-cognitive-services/keyphrase` Hizmetler deposu altında Microsoft Container Registry anahtar ifade ayıklama görüntüsünü temsil eden bir örnek aşağıda verilmiştir. Diğer bir örnek olarak, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` kapsayıcı önizleme kapsayıcısı kayıt defterinin Microsoft deposunda konuşmayı metne dönüştürme görüntüsünü temsil eden bir örnektir. |
    |İşletim sistemi türü|`Linux`|
    |Boyut|Belirli bilişsel hizmet Kapsayıcınızın boyutunu önerilen önerilerle değiştirin:<br>2 CPU çekirdeği<br>4 GB

3. **Ağ** sekmesinde, aşağıdaki ayrıntıları girin:

    |Ayar|Değer|
    |--|--|
    |Bağlantı noktaları|TCP bağlantı noktasını olarak ayarlayın `5000` . 5000 numaralı bağlantı noktasında kapsayıcıyı kullanıma sunar.|

4. **Gelişmiş** sekmesinde, Azure Container Instance kaynağının kapsayıcı faturalama ayarları Için gereken **ortam değişkenlerini** girin:

    | Anahtar | Değer |
    |--|--|
    |`ApiKey`|Kaynağın **anahtarlar ve uç nokta** sayfasından kopyalanmış. Boşluk veya tire içermeyen 32 alfasayısal karakter dizesidir `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`Billing`| Endpoint URL 'niz kaynağın **anahtarlar ve uç nokta** sayfasından kopyalanmış.|
    |`Eula`|`accept`|

5. **Gözden geçir ve oluştur** 'a tıklayın
6. Doğrulama işleminden sonra oluşturma işlemini tamamladıktan sonra **Oluştur** ' a tıklayın.
7. Kaynak başarıyla dağıtıldığında,
