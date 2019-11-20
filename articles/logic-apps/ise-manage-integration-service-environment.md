---
title: Azure Logic Apps tümleştirme hizmeti ortamlarını yönetme
description: Azure Logic Apps için tümleştirme hizmeti ortamınızda (ıSE) ağ durumunu kontrol edin ve Logic Apps, bağlantılar, özel bağlayıcılar ve tümleştirme hesaplarını yönetin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6a6b096911ac8596fe29aeb4596f1da6d5266794
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967801"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps tümleştirme hizmeti ortamınızı (ıSE) yönetme

[Tümleştirme hizmeti ortamınızın (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ağ durumunu denetlemek ve Ise 'de mevcut olan Logic Apps, bağlantılar, tümleştirme hesapları ve bağlayıcıları yönetmek için bu konudaki adımları izleyin. Bu yapıtları ıSE 'nize eklemek için bkz. [Integration Service ortamınıza yapıt ekleme](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>ISE 'nizi görüntüleme

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. Portalın arama kutusuna "tümleştirme hizmeti ortamları" yazın ve ardından **tümleştirme hizmeti ortamları**' nı seçin.

   ![Tümleştirme hizmeti ortamlarını bulun](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Sonuçlar listesinden tümleştirme hizmeti ortamınızı seçin.

   ![Tümleştirme hizmeti ortamını seçin](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Şirket içinde Logic Apps, bağlantılar, bağlayıcılar veya tümleştirme hesapları bulmak için sonraki bölümlere devam edin.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Ağ durumunu denetle

ISE menüsünde **Ayarlar**' ın altında **ağ durumu**' nu seçin. Bu bölmede, alt ağlarınızın sistem durumu ve diğer hizmetlere giden bağımlılıklarınız gösterilir.

![Ağ durumunu denetle](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Mantıksal uygulamalarınızı yönetin

ISE 'de olan Logic Apps 'i görüntüleyebilir ve yönetebilirsiniz.

1. ISE menüsünde, **Ayarlar**' ın altında **Logic Apps**' i seçin.

   ![Mantıksal uygulamaları görüntüleme](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Artık ihtiyacınız olmayan Logic Apps 'i kaldırmak için bu Logic Apps ' i seçin ve **Sil**' i seçin. Silmek istediğinizi onaylamak için **Evet**' i seçin.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API bağlantılarını yönetme

ISE 'de çalışan Logic Apps tarafından oluşturulan bağlantıları görüntüleyebilir ve yönetebilirsiniz.

1. ISE menüsünde **Ayarlar**' ın altında **API bağlantıları**' nı seçin.

   ![API bağlantılarını görüntüle](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Artık ihtiyacınız olmayan bağlantıları kaldırmak için bu bağlantıları seçin ve **Sil**' i seçin. Silmek istediğinizi onaylamak için **Evet**' i seçin.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE bağlayıcılarını yönetme

ISE 'nize dağıtılan API bağlayıcılarını görüntüleyebilir ve yönetebilirsiniz.

1. ISE menüsünde, **Ayarlar**' ın altında, **yönetilen bağlayıcılar**' ı seçin.

   ![Yönetilen bağlayıcıları görüntüleme](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. ISE 'de kullanılabilir olmasını istemediğiniz bağlayıcıları kaldırmak için bu bağlayıcıları seçin ve **Sil**' i seçin. Silmek istediğinizi onaylamak için **Evet**' i seçin.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Özel bağlayıcıları yönetme

ISE 'nize dağıttığınız özel bağlayıcıları görüntüleyebilir ve yönetebilirsiniz.

1. ISE menüsünde **Ayarlar**' ın altında **özel bağlayıcılar**' ı seçin.

   ![Özel bağlayıcıları bulma](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Artık ihtiyacınız olmayan özel bağlayıcıları kaldırmak için bu Bağlayıcılar ' ı seçin ve **Sil**' i seçin. Silmek istediğinizi onaylamak için **Evet**' i seçin.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Tümleştirme hesaplarını yönetme

1. ISE menüsünde **Ayarlar**' ın altında **tümleştirme hesapları**' nı seçin.

   ![Tümleştirme hesaplarını bul](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Artık gerekmeyen tümleştirme hesaplarını ıSE bilgisayarınızdan kaldırmak için bu tümleştirme hesaplarını seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Yalıtılmış Logic Apps 'Ten Azure sanal ağlarına](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) nasıl bağlanacağınızı öğrenin
