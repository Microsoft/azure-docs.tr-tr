---
title: Tümleştirme hizmeti ortamına yapıt ekleme-Azure Logic Apps
description: Azure sanal ağlarına (VNet) erişmek için tümleştirme hizmeti ortamınıza (ıSE) Logic Apps, tümleştirme hesapları ve özel bağlayıcılar ekleme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 266c942adb62b187d39d2eac4a47455482bfadaa
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680522"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps tümleştirme hizmeti ortamınıza (ıSE) yapıt ekleme

Bir [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)oluşturduktan sonra, Azure sanal ağınızdaki kaynaklara erişebilmeleri için Logic Apps, tümleştirme hesapları ve bağlayıcılar gibi yapıtları ekleyin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Mantıksal uygulamalarınızı çalıştırmak için oluşturduğunuz ıSE. Bir ıSE yoksa, [önce BIR Ise oluşturun](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Mantıksal uygulamalar oluşturma

Tümleştirme hizmeti ortamınızda (ıSE) çalışan Logic Apps oluşturmak için aşağıdaki adımları izleyin:

1. Henüz açık değilse, ıSE 'nizi bulun ve açın. ISE menüsünde **Ayarlar**' ın altında,**Ekle** >  **Logic Apps** ' i seçin.

   ![ISE 'ye yeni mantıksal uygulama ekleme](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -veya-

   Ana Azure menüsünden **kaynak oluştur** > **tümleştirme** > **mantıksal uygulama**' yı seçin.

1. Mantıksal uygulamanız için kullanılacak ad, Azure aboneliği ve Azure kaynak grubunu (yeni veya var olan) sağlayın.

1. **Konum** listesinden, **tümleştirme hizmeti ortamları** bölümünde, Ise 'yi seçin, örneğin:

   ![Tümleştirme hizmeti ortamını seçin](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Mantıksal uygulamalarınızı bir tümleştirme hesabıyla kullanmak istiyorsanız, bu mantıksal uygulamalar ve tümleştirme hesabı aynı ıSE 'yi kullanmalıdır.

1. [Mantıksal uygulamanızı her zamanki şekilde oluşturmaya](../logic-apps/quickstart-create-first-logic-app-workflow.md)devam edin.

   Tetikleyiciler ve eylemlerin nasıl çalıştığı ve küresel Logic Apps hizmetiyle karşılaştırıldığında bir ıSE kullandığınızda nasıl etiketlendiği hakkında farklılıklar için bkz. [Ise 'ye genel bakış](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Ortamınızdaki Logic Apps ve API bağlantılarını yönetmek için bkz. [tümleştirme hizmeti ortamınızı yönetme](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Tümleştirme hesapları oluşturma

Oluşturma sırasında seçilen [ıSE SKU 'su](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) temel alınarak, Ise 'niz hiçbir ek ücret ödemeden belirli tümleştirme hesabı kullanımını içerir. Bir tümleştirme hizmeti ortamında (ıSE) bulunan Logic Apps, yalnızca aynı ıSE 'de bulunan tümleştirme hesaplarına başvurabilir. Bu nedenle, bir tümleştirme hesabının bir ıSE 'de Logic Apps ile çalışması için, hem tümleştirme hesabı hem de Logic Apps, konumuyla *aynı ortamı* kullanmalıdır. Tümleştirme hesapları ve sesleri hakkında daha fazla bilgi için bkz. [Ise Ile tümleştirme hesapları](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

ISE kullanan bir tümleştirme hesabı oluşturmak için aşağıdaki adımları izleyin:

1. Henüz açık değilse, ıSE 'nizi bulun ve açın. ISE menüsünde, **Ayarlar**' ın altında,**Ekle** >  **tümleştirme hesapları** ' nı seçin.

   ![ISE 'ye yeni tümleştirme hesabı ekleme](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -veya-

   Ana Azure menüsünden **kaynak oluştur**  > **tümleştirme**  > **tümleştirme hesabı**' nı seçin.

1. Tümleştirme hesabınız için kullanılacak ad, Azure aboneliği, Azure Kaynak grubu (yeni veya var olan) ve fiyatlandırma katmanını sağlayın.

1. **Konum** listesinden, **tümleştirme hizmeti ortamları** bölümünde, mantıksal uygulamalarınızın kullandığı Ise 'yi seçin, örneğin:

   ![Tümleştirme hizmeti ortamını seçin](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Mantıksal uygulamanızı, her zamanki şekilde tümleştirme hesabınıza bağlayın](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Tümleştirme hesabınıza, [ticari iş ortakları](../logic-apps/logic-apps-enterprise-integration-partners.md) ve [anlaşmalar](../logic-apps/logic-apps-enterprise-integration-agreements.md)gibi yapıtlar ekleyerek devam edin.

1. Şirket içindeki tümleştirme hesaplarını yönetmek için bkz. [tümleştirme hizmeti ortamınızı yönetme](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE bağlayıcıları ekleme

Kullanım için kullanılabilen, ancak Ise 'de dağıtılmayan Microsoft tarafından yönetilen bağlayıcılar ekleyebilirsiniz.

1. ISE menüsünde, **Ayarlar**' ın altında, **yönetilen bağlayıcılar**' ı seçin. Araç çubuğunda **Ekle**' yi seçin.

   ![Yönetilen bağlayıcıları görüntüleme](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **Yeni bir yönetilen bağlayıcı Ekle** bölmesinde, **bağlayıcı bul** listesini açın. İstediğiniz bağlayıcı kullanılabiliyorsa, bu bağlayıcıyı seçin ve ardından **Oluştur**' u seçin.

   Listede yalnızca uygun olan ancak ıSE içinde dağıtılmayan bağlayıcılar gösterilir. ISE 'de zaten dağıtılan bağlayıcılar seçim için kullanılamaz olarak görünür.

   ![Uygun bağlayıcı seçin](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Özel Bağlayıcılar oluşturma

ISE 'de özel bağlayıcılar kullanmak için, bu özel bağlayıcıları doğrudan ıSE içinden oluşturun.

1. Henüz açık değilse, ıSE 'nizi bulun ve açın. ISE menüsünde, **Ayarlar**' ın altında,**Ekle** >  **özel bağlayıcılar** ' ı seçin.

   ![Özel bağlayıcı oluşturma](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Özel Bağlayıcınız için kullanılacak ad, Azure aboneliği ve Azure kaynak grubunu (yeni veya var olan) sağlayın.

1. **Konum** listesinden, **tümleştirme hizmeti ortamları** bölümünde, mantıksal uygulamalarınızın kullandığı Ise 'yi seçin ve **Oluştur**' u seçin, örneğin:

   ![Tümleştirme hizmeti ortamını seçin](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Yeni özel bağlayıcınızı seçin ve ardından **Düzenle**' yi seçin, örneğin:

   ![Özel bağlayıcıyı seçme ve düzenleme](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Bir [Openapı tanımından](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) veya [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)'tan her zamanki şekilde bağlayıcı oluşturarak devam edin.

1. Çalışma ortamınızdaki özel bağlayıcıları yönetmek için bkz. [tümleştirme hizmeti ortamınızı yönetme](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Tümleştirme hizmeti ortamlarını yönetme](../logic-apps/ise-manage-integration-service-environment.md)
