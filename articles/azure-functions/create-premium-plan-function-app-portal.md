---
title: Portalda bir Azure Işlevleri Premium planı oluşturma
description: Premium planında çalışan bir işlev uygulaması oluşturmak için Azure portal nasıl kullanacağınızı öğrenin.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 9cab67f096665c9333fa40bcb790896fcbebd8d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676595"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Azure portal Premium plan işlev uygulaması oluşturma

Azure Işlevleri, sanal ağ bağlantısı, soğuk başlatma ve Premium donanım sağlayan ölçeklenebilir bir Premium plan sunar. Daha fazla bilgi için bkz. [Azure Işlevleri Premium planı](functions-premium-plan.md). 

Bu makalede, Premium planda bir işlev uygulaması oluşturmak için Azure portal kullanmayı öğreneceksiniz. 

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

İşlevlerinizin yürütülmesini barındıran bir işlev uygulamasına sahip olmanız gerekir. İşlev uygulaması, kaynakların daha kolay yönetilmesi, dağıtılması, ölçeklendirilmesi ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Bu noktada, yeni işlev uygulamasında işlevleri oluşturabilirsiniz. Bu işlevler, [Premium planın](functions-premium-plan.md)avantajlarından faydalanabilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [HTTP ile tetiklenen işlev ekleme] (./functions-get-started.md
