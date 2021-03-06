---
title: 'Hızlı başlangıç: SDK-kişiselleştirici ile öğrenme döngüsü oluşturma ve kullanma'
description: Bu hızlı başlangıçta, kişiselleştirici istemci kitaplığını kullanarak bilgi bankasını oluşturma ve yönetme işlemlerinin nasıl yapılacağı gösterilir.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: kişiselleştirici, Azure kişiselleştirici, makine öğrenimi
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 3acfd8134a2a65e6ea9f019baff24b8b8149f7f3
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777334"
---
# <a name="quickstart-personalizer-client-library"></a>Hızlı başlangıç: kişiselleştirici istemci kitaplığı

Bu hızlı başlangıçta kişiselleştirilmiş içeriği kişiselleştirici hizmeti ile görüntüleyin.

Kişiselleştirici istemci kitaplığını kullanmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.

 * Derecelendirme API 'SI-içerik ve bağlam hakkında sağladığınız gerçek zamanlı bilgileri temel alarak eylemlerden en iyi öğeyi seçer.
 * Ödül API-iş gereksinimlerinize göre ödül puanı belirlenir, ardından bu API ile kişiselleştiriciye gönderebilirsiniz. Bu puan, 1 gibi tek bir değer olabilir ve hatalı için 0 veya iş gereksinimlerinize göre oluşturduğunuz bir algoritmadır.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kişiselleştirici nasıl kullanılır?](how-personalizer-works.md) 
>  [Kişiselleştirici nerede kullanılır?](where-can-you-use-personalizer.md)
