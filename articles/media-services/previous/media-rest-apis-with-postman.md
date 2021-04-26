---
title: Azure Media Services REST API'si çağrıları için Postman yapılandırma
description: Bu makalede, Media Services REST API çağrıları için Postman 'ın nasıl yapılandırılacağı açıklanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ef92e772085b1b89388c3f85fb3fdb91df0f6a75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012216"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Media Services V2 REST API çağrıları için Postman yapılandırma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Bu öğreticide, Azure Media Services (AMS) REST API 'Lerini çağırmak için kullanılacak **Postman** 'ın nasıl yapılandırılacağı gösterilmektedir. Öğreticide ortam ve koleksiyon dosyalarının **Postman**'a nasıl aktarılacağı gösterilmektedir. Koleksiyon, Azure Media Services (AMS) REST API 'Lerini çağıran HTTP isteklerinin gruplandırılmış tanımlarını içerir. Ortam dosyası, koleksiyon tarafından kullanılan değişkenleri içerir.

Bu ortam ve koleksiyon, Azure Media Services REST API 'Leri ile çeşitli görevlerin nasıl elde alınacağını gösteren makalelerde kullanılır.

## <a name="prerequisites"></a>Önkoşullar

- AMS REST öğreticilerinden bazılarında gösterilen REST API'lerini yürütmek için [Postman](https://www.getpostman.com/) REST istemcisini yükleyin. 

    Biz **Postman**'ı kullanıyoruz, ancak herhangi bir REST aracı da olabilir. Diğer seçenekler şunlardır: REST eklentili **Visual Studio Code** veya **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Ortamı yapılandırma 

1. AMS öğreticilerinde kullanılan ortam değişkenlerini içeren bir. JSON dosyası oluşturun. Dosyayı adlandırın (örneğin, **AzureMediaServices.postman_environment.js**). Dosyayı açın ve Postman ortamını tanımlayan kodu [Bu kod](postman-environment.md)listesinden yapıştırın. 
2. **Postman**'ı açın.
3. Ekranın sağ tarafında **Ortamı yönet** seçeneğini belirleyin.

    ![Ekran görüntüsü ortamı Yönet seçeneğinin seçili olduğunu gösterir.](./media/media-services-rest-upload-files/postman-create-env.png)
4. **Ortamı yönet** iletişim kutusunda **İçe aktar**'ı tıklatın.
5. Dosyasında **AzureMediaServices.postman_environment.js** gösterin ve seçin.
6. **AzureMedia** ortamı eklenir.
7. İletişim kutusunu kapatın.
8. **AzureMedia** ortamını seçin.

    ![Ekran görüntüsü AzureMedia ortamının seçili olduğunu gösterir.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Koleksiyonu yapılandırma

1. Media Services bir dosyayı karşıya yüklemek için gereken tüm işlemlerle **Postman** koleksiyonunu içeren bir. JSON dosyası oluşturun. Dosyayı adlandırın (örneğin, **AzureMediaServicesOperations.postman_collection.js**). Dosyayı açın ve **Postman** koleksiyonunu tanımlayan kodu [Bu kod](postman-collection.md)listesinden yapıştırın.
2. Koleksiyon dosyasını içe aktarmak için **İçe Aktar**'ı tıklatın.
3. Dosyadaki **AzureMediaServicesOperations.postman_collection.js** seçin.

    ![Ekran görüntüsü, dosyaları seç seçiliyken IÇERI Aktar iletişim kutusunu gösterir.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Sonraki adımlar

[Varlık yükle](media-services-rest-upload-files.md) makalesine göz atın.  
