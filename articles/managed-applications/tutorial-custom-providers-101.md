---
title: Azure 'da özel eylemler ve kaynaklar oluşturma
description: Bu öğreticide, Azure Resource Manager ' de özel eylemler ve kaynaklar oluşturma konusu ele geçilir. Ayrıca, özel iş akışlarının Azure Resource Manager şablonları, Azure CLı, Azure Ilkesi ve Azure etkinlik günlüğü ile nasıl birlikte çalıştığını gösterir.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172930"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Azure 'da özel eylemler ve kaynaklar oluşturma

Özel sağlayıcı, Azure ile uç nokta arasında bir sözleşmedir. Özel sağlayıcılar sayesinde, Azure Resource Manager içine yeni API 'Ler ekleyerek Azure 'daki iş akışlarını değiştirebilirsiniz. Bu özel API 'lerle, Kaynak Yöneticisi yeni dağıtım ve yönetim yeteneklerini kullanabilir.

Bu öğretici, Azure 'a yeni eylemleri ve kaynakları ekleme ve bunları tümleştirme hakkında basit bir örnektir.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure özel sağlayıcıları için Azure Işlevleri ayarlama

Bu öğreticiden birinde, bir Azure işlevi uygulamasının özel sağlayıcılarla çalışacak şekilde nasıl ayarlanacağı açıklanmaktadır:

- [Azure özel sağlayıcıları için Azure Işlevleri ayarlama](./tutorial-custom-providers-function-setup.md)

Özel sağlayıcılar, herhangi bir genel URL ile çalışabilir.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Özel sağlayıcılar için yeniden bir uç nokta yazar

Bu öğreticinin ikinci bölümünde, özel sağlayıcılar için bir yeniden deneme uç noktası yazma açıklanmaktadır:

- [Özel sağlayıcılar için yeniden bir uç nokta yazma](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Özel bir sağlayıcı oluşturma ve kullanma

Bu öğreticinin üçüncü kısmında özel bir sağlayıcının nasıl oluşturulacağı ve özel eylemleri ve kaynakları nasıl kullanılacağı açıklanmaktadır:

- [Özel bir sağlayıcı oluşturma ve kullanma](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özel sağlayıcılar ve bir tane oluşturma hakkında bilgi edindiniz. Sonraki öğreticiye devam etmek için bkz [. Öğretici: Azure özel sağlayıcıları](./tutorial-custom-providers-function-setup.md)için Azure işlevleri ayarlayın.

Başvurular veya hızlı başlangıç arıyorsanız, bazı yararlı bağlantılar aşağıda verilmiştir:

- [Hızlı Başlangıç: Azure özel kaynak sağlayıcısı oluşturma ve özel kaynaklar dağıtma](./create-custom-provider.md)
- [Nasıl yapılır: Azure REST API özel eylemler ekleme](./custom-providers-action-endpoint-how-to.md)
- [Nasıl yapılır: Azure REST API özel kaynakları ekleme](./custom-providers-resources-endpoint-how-to.md)
