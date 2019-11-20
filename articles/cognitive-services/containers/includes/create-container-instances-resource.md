---
title: Kapsayıcı desteği
titleSuffix: Azure Cognitive Services
description: Azure Container Instance kaynağı oluşturmayı öğrenin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051184"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure Container Instance kaynağı oluşturma

1. Container Instances için [Oluştur](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) sayfasına gidin.

2. **Temel bilgiler** sekmesinde, aşağıdaki ayrıntıları girin:

    |Ayar|Value|
    |--|--|
    |Subscription|Aboneliğinizi seçin.|
    |Resource group|Kullanılabilir kaynak grubunu seçin veya gibi yeni bir tane `cognitive-services`oluşturun.|
    |Kapsayıcı adı|Gibi bir ad `cognitive-container-instance`girin. Ad, daha küçük bir harf olmalıdır.|
    |Location|Dağıtım için bir bölge seçin.|
    |Görüntü türü|Kapsayıcı resminiz kimlik bilgileri gerektirmeyen bir kapsayıcı kayıt defterinde depolanıyorsa, öğesini seçin `Public`. Kapsayıcı yansımanıza erişim için kimlik bilgileri gerekiyorsa, öğesini `Private`seçin. Kapsayıcı görüntüsünün `Public` miyoksa("genelönizleme")olupolmadığıhakkındaayrıntılariçinkapsayıcıdepolarıveresimlerebakın.`Private` [](../../cognitive-services-container-support.md#container-repositories-and-images) |
    |Görüntü adı|Bilişsel hizmetler kapsayıcı konumunu girin. Konum, `docker pull` komuta bir bağımsız değişken olarak kullanılan şeydir. Kullanılabilir görüntü adları ve bunlara karşılık gelen depo için [kapsayıcı depoları ve görüntüleri](../../cognitive-services-container-support.md#container-repositories-and-images) inceleyin.<br><br>Görüntü adı, üç bölümden oluşan tam olarak nitelenmiş olmalıdır. İlk olarak, kapsayıcı kayıt defteri, ardından depo, son olarak görüntü adı: `<container-registry>/<repository>/<image-name>`.<br><br>Azure bilişsel Hizmetler `mcr.microsoft.com/azure-cognitive-services/keyphrase` deposu altında Microsoft Container Registry anahtar ifade ayıklama görüntüsünü temsil eden bir örnek aşağıda verilmiştir. Diğer bir örnek olarak `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , kapsayıcı önizleme kapsayıcısı kayıt defterinin Microsoft deposunda konuşmayı metne dönüştürme görüntüsünü temsil eden bir örnektir. |
    |İşletim sistemi türü|`Linux`|
    |Size|Belirli bilişsel hizmet Kapsayıcınızın boyutunu önerilen önerilerle değiştirin:<br>2 CPU çekirdeği<br>4 GB

3. **Ağ** sekmesinde, aşağıdaki ayrıntıları girin:

    |Ayar|Value|
    |--|--|
    |Bağlantı Noktaları|TCP bağlantı noktasını olarak `5000`ayarlayın. 5000 numaralı bağlantı noktasında kapsayıcıyı kullanıma sunar.|

4. **Gelişmiş** sekmesinde, Azure Container Instance kaynağının kapsayıcı faturalama ayarları Için gereken **ortam değişkenlerini** girin:

    | Anahtar | Value |
    |--|--|
    |`apikey`|Kaynağın **anahtarlar** sayfasından kopyalanmış. Boşluk veya tire `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`içermeyen 32 alfasayısal karakter dizesidir.|
    |`billing`|Kaynağın **genel bakış** sayfasından kopyalanmış.|
    |`eula`|`accept`|

1. **Gözden geçir ve oluştur** 'a tıklayın
1. Doğrulama işleminden sonra oluşturma işlemini tamamladıktan sonra **Oluştur** ' a tıklayın.
1. Kaynak başarıyla dağıtıldığında,