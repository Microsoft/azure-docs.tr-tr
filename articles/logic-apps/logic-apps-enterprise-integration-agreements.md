---
title: Ticari iş ortağı sözleşmeleri-Azure Logic Apps
description: Azure Logic Apps ve Enterprise Integration Pack kullanarak ticari iş ortakları arasında anlaşmalar oluşturun ve yönetin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 35ebaab47edd110258f537dbbb044387515ed6c4
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680412"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Azure Logic Apps 'de ticari iş ortağı sözleşmeleri oluşturun ve yönetin

Bir [ticaret ortağı](../logic-apps/logic-apps-enterprise-integration-partners.md)  
*sözleşmesi* , IŞLETMELERDEN işletmeye (B2B) iletileri değiş tokuşu yaparken kullanılacak sektör standardı protokolünü tanımlayarak birbirleriyle sorunsuz bir şekilde iletişim kurmasına yardımcı olur. Sözleşmeler, yaygın avantajları sağlar, örneğin:

* Kuruluşların, iyi bilinen bir biçimi kullanarak bilgi alışverişi için izin verir.
* B2B işlemleri yaparken verimliliği artırabilirsiniz.
* Kurumsal tümleştirme çözümleri oluşturmak, yönetmek ve kullanmak kolaydır.

Bu makalede, [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) ve [Azure Logic Apps](../logic-apps/logic-apps-overview.md)kullanarak B2B senaryolarında kurumsal tümleştirme ÇÖZÜMLERINI oluştururken kullanabileceğiniz bir AS2, ediolgu veya x12 sözleşmesinin nasıl oluşturulacağı gösterilmektedir. Bir anlaşma oluşturduktan sonra, B2B iletileri alışverişi için AS2, EDIOLGU veya x12 bağlayıcılarını kullanabilirsiniz.

RosettaNet iletilerini değiş tokuş eden anlaşmalar oluşturmak için bkz. [Exchange RosettaNet iletileri](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Sözleşmenizi ve diğer B2B yapıtlarını depolamak için bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Bu tümleştirme hesabının Azure aboneliğinizle ilişkilendirilmesi gerekir.

* Tümleştirme hesabınızda zaten oluşturduğunuz en az iki [ticari iş ortağı](../logic-apps/logic-apps-enterprise-integration-partners.md) . Bir anlaşma hem ana makine ortağı hem de Konuk iş ortağı gerektirir. Her iki iş ortağı da, AS2, x12 veya EDIOLGU gibi oluşturmak istediğiniz anlaşmayla aynı "iş kimliği" niteleyicisi kullanmalıdır.

* İsteğe bağlı: sözleşmenizi ve mantıksal uygulamanızın iş akışını başlatan bir tetikleyiciyi kullanmak istediğiniz mantıksal uygulama. Yalnızca tümleştirme hesabınızı ve B2B yapıtlarınızı oluşturmak için bir mantıksal uygulama gerekmez. Ancak, mantıksal uygulamanızın tümleştirme hesabınızdaki B2B yapılarını kullanabilmesi için, tümleştirme hesabınızı mantıksal uygulamanıza bağlamanız gerekir. Logic Apps 'e yeni başladıysanız, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin.

## <a name="create-agreements"></a>Anlaşma oluşturma

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
Ana Azure menüsünde **tüm hizmetler**' i seçin. Arama kutusuna filtreniz olarak "tümleştirme" yazın. Sonuçlardan şu kaynağı seçin: **tümleştirme hesapları**

   ![Tümleştirme hesabınızı bulun](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. **Tümleştirme hesapları**altında, sözleşmeyi oluşturmak istediğiniz tümleştirme hesabını seçin.

   ![Anlaşmanın oluşturulacağı tümleştirme hesabını seçin](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Sağ bölmedeki **Bileşenler**altında **anlaşmalar** kutucuğunu seçin.

   !["Anlaşmaları" seçin](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. **Sözleşmeler**altında **Ekle**' yi seçin. **Ekle** bölmesinde sözleşmeniz hakkında bilgi sağlayın, örneğin:

   !["Ekle" yi seçin](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Adı** | Yes | <*Sözleşmesi-adı* > | Sözleşmenizin adı |
   | **Anlaşma türü** | Yes | **AS2**, **x12**veya **ediolgu** | Anlaşmanızın protokol türü. Anlaşma dosyanızı oluşturduğunuzda, bu dosyadaki içeriğin anlaşma türüyle eşleşmesi gerekir. | |  
   | **Ana bilgisayar ortağı** | Yes | <*konak-iş ortağı-adı* > | Ana bilgisayar ortağı, sözleşmeyi belirten kuruluşu temsil eder |
   | **Ana bilgisayar kimliği** | Yes | <*konak-iş ortağı tanımlayıcı* > | Ana bilgisayar ortağının tanımlayıcısı |
   | **Konuk Iş ortağı** | Yes | <*Konuk iş ortağı adı* > | Konuk iş ortağı, ana bilgisayar ortağıyla iş yapan kuruluşu temsil eder |
   | **Konuk kimliği** | Yes | <*Konuk iş ortağı tanımlayıcısı* > | Konuk iş ortağının tanımlayıcısı |
   | **Ayarları al** | Değişir | Değişir | Bu özellikler, ana bilgisayar ortağının anlaşmada Konuk iş ortağından gelen tüm iletileri nasıl alacağını belirtir. Daha fazla bilgi için bkz. ilgili anlaşma türü: <p>- [AS2 ileti ayarları](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ediolgu ileti ayarları](logic-apps-enterprise-integration-edifact.md) <br>- [x12 ileti ayarları](logic-apps-enterprise-integration-x12.md) |
   | **Ayarları gönder** | Değişir | Değişir | Bu özellikler, ana bilgisayar ortağının tüm giden iletileri anlaşmada Konuk iş ortağına nasıl göndereceğini belirtir. Daha fazla bilgi için bkz. ilgili anlaşma türü: <p>- [AS2 ileti ayarları](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ediolgu ileti ayarları](logic-apps-enterprise-integration-edifact.md) <br>- [x12 ileti ayarları](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Sözleşmenizi oluşturmayı tamamladığınızda, **Ekle** sayfasında **Tamam**' ı seçin ve tümleştirme hesabınıza geri dönün.

   **Sözleşmeler** listesinde artık yeni anlaşmanız gösterilmektedir.

## <a name="edit-agreements"></a>Anlaşmaları düzenleme

1. [Azure Portal](https://portal.azure.com)ana Azure menüsünde **tüm hizmetler**' i seçin.

1. Arama kutusuna filtreniz olarak "tümleştirme" yazın. Sonuçlardan şu kaynağı seçin: **tümleştirme hesapları**

1. **Tümleştirme hesapları**altında, düzenlemek istediğiniz sözleşmenin bulunduğu tümleştirme hesabını seçin.

1. Sağ bölmedeki **Bileşenler**altında **anlaşmalar** kutucuğunu seçin.

1. **Anlaşmalar**' ın altında, sözleşmenizi seçin ve **Düzenle**' yi seçin.

1. Değişikliklerinizi yapın ve kaydedin.

## <a name="delete-agreements"></a>Anlaşmaları Sil

1. [Azure Portal](https://portal.azure.com)ana Azure menüsünde **tüm hizmetler**' i seçin.

1. Arama kutusuna filtreniz olarak "tümleştirme" yazın. Sonuçlardan şu kaynağı seçin: **tümleştirme hesapları**

1. **Tümleştirme hesapları**altında, silmek istediğiniz sözleşmenin bulunduğu tümleştirme hesabını seçin.

1. Sağ bölmedeki **Bileşenler**altında **anlaşmalar** kutucuğunu seçin.

1. **Anlaşmalar**' ın altında, sözleşmenizi seçin ve **Sil**' i seçin.

1. Seçili sözleşmeyi silmek istediğinizi onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Exchange AS2 iletileri](logic-apps-enterprise-integration-as2.md)
* [Exchange EDIOLGU iletileri](logic-apps-enterprise-integration-edifact.md)
* [Exchange x12 iletileri](logic-apps-enterprise-integration-x12.md)
