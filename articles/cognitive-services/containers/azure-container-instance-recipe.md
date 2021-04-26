---
title: Azure Container Instance tarifi
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler kapsayıcılarını Azure Container Instance üzerinde dağıtmayı öğrenin
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 4641723ed1ad334ab9f960728f4986b03fd0ac99
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065897"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Azure Container Örneğinde kapsayıcı dağıtma ve çalıştırma

Aşağıdaki adımlarla, Azure [Container Instances](../../container-instances/index.yml)Ile Bulutta Azure bilişsel hizmetler uygulamalarını kolayca ölçeklendirin. Kapsayıcılama, altyapıyı yönetmek yerine uygulamalarınızı oluşturmaya odaklanmanıza yardımcı olur. Kapsayıcıları kullanma hakkında daha fazla bilgi için bkz. [Özellikler ve avantajlar](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Önkoşullar

Tarif, tüm bilişsel hizmetler kapsayıcısıyla birlikte çalışarak. Tarif kullanılmadan önce bilişsel hizmet kaynağının oluşturulması gerekir. Kapsayıcıları destekleyen her bilişsel hizmetin, bir kapsayıcı için hizmeti yükleme ve yapılandırma için bir "yükleme" makalesi vardır. Bazı hizmetler, kapsayıcı için girdi olarak bir dosya veya dosya kümesi gerektirir, bu çözümü kullanmadan önce kapsayıcıyı anlamanız ve başarıyla kullanmış olmanız önemlidir.

* Kullanmakta olduğunuz Azure bilişsel hizmeti için bir Azure kaynağı.
* Bilişsel hizmet **uç noktası URL 'si** -uç nokta url 'sinin Azure Portal içinden nerede olduğunu ve URL 'nin doğru bir örneğini nasıl göründüğünü bulmak için, belirli hizmetinizin "nasıl yüklenir" konusunu gözden geçirin. Tam biçim hizmetten hizmete değiştirilebilir.
* Bilişsel hizmet **anahtarı** -anahtarlar, Azure kaynağı için **anahtarlar** sayfasıdır. Yalnızca iki anahtardan birine ihtiyacınız vardır. Anahtar, 32 alfasayısal karakterlerden oluşan bir dizedir.

* Yerel ana bilgisayarınızda (Bilgisayarınız) tek bir bilişsel hizmetler kapsayıcısı. Şunları yapadığınızdan emin olun:
  * Bir komutla görüntüyü çekin `docker pull` .
  * Tüm gerekli yapılandırma ayarları ile bir komutla yerel kapsayıcıyı başarıyla çalıştırın `docker run` .
  * HTTP 2xx ve JSON yanıtının geri yanıtını alarak kapsayıcının uç noktasını çağırın.

Açılı ayraçlar içindeki tüm değişkenlerin `<>` kendi değerlerinizle değiştirilmesini gerektirir. Bu değiştirme açılı ayraçları içerir.

> [!IMPORTANT]
> LUSıS kapsayıcısı, `.gz` çalışma zamanında çekilecek bir model dosyası gerektiriyor. Kapsayıcı, kapsayıcı örneğinden bir birim bağlaması aracılığıyla bu model dosyasına erişebilmelidir. Bir model dosyasını karşıya yüklemek için şu adımları izleyin:
> 1. [Bir Azure dosya paylaşma oluşturun](../../storage/files/storage-how-to-create-file-share.md). Daha sonra ihtiyacınız olacak şekilde Azure depolama hesabı adı, anahtar ve dosya paylaşımının adını göz önünde ayırın.
> 2. lusıs [modelinizi (paketlenmiş uygulama) Lua portalından dışarı aktarın](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. Azure portal, depolama hesabı kaynağınızın **genel bakış** sayfasına gidin ve **dosya paylaşımları**' nı seçin. 
> 4. Son oluşturduğunuz dosya paylaşımının adını seçip **karşıya yükle**' yi seçin. Ardından paketlenmiş uygulamanızı karşıya yükleyin. 

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Kapsayıcı örneğini kullanma

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. **Genel bakış** ' ı SEÇIN ve IP adresini kopyalayın. Bu, gibi sayısal bir IP adresi olacaktır `55.55.55.55` .
1. Yeni bir tarayıcı sekmesi açın ve IP adresini kullanın, örneğin, `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Kapsayıcının ana sayfasını görürsünüz ve kapsayıcının çalıştığını bilmenizi sağlar.

    ![Kapsayıcının ana sayfası](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Kapsayıcının Swagger sayfasını görüntülemek için **hizmet API 'Si açıklaması** ' nı seçin.

1. **Post** API 'lerinden birini seçin ve **deneyin**' i seçin.  Parametreler, giriş dahil görüntülenir. Parametreleri girin.

1. İsteği kapsayıcı örneğinize göndermek için **Yürüt** ' ü seçin.

    Azure Container Instance 'da bilişsel hizmetler kapsayıcılarını başarıyla oluşturdunuz ve kullandınız.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Sistem durumu kapsayıcısı için Metin Analizi çalıştırıyorsanız, sorguları göndermek için aşağıdaki URL 'YI kullanın: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
