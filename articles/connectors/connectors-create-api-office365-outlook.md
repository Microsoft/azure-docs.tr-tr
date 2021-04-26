---
title: Office 365 Outlook ile tümleştirme
description: Azure Logic Apps kullanarak Office 365 Outlook 'ta e-posta, kişi ve takvimleri yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 87a9c8a38ede7008d658068329c8c72e7e3c4117
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095457"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Azure Logic Apps’i kullanarak Office 365 Outlook’ta e-postayı, kişileri ve takvimleri yönetme

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Office 365 Outlook Bağlayıcısı](/connectors/office365connector/)sayesinde, Logic Apps oluşturarak iş veya okul hesabınızı yöneten otomatik görevler ve iş akışları oluşturabilirsiniz. Örneğin şu görevleri otomatikleştirebilirsiniz:

* E-posta alın, gönderin ve yanıtlayın.
* Takviminizde toplantılar zamanlayın.
* Kişiler ekleyin ve düzenleyin.

İş akışınızı başlatmak için herhangi bir tetikleyiciyi kullanabilirsiniz. Örneğin, yeni bir e-posta geldiğinde, bir takvim öğesi güncelleştirilirken veya fark hizmetinde bir olay gerçekleştiğinde Salesforce gibi. Tetikleyici olayına yanıt veren eylemleri (örneğin, e-posta gönder veya yeni bir takvim olayı oluşturma) kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Bir [iş veya okul hesabıyla](https://www.office.com/)oturum açtığınızda bir Outlook hesabı. Bir @outlook.com veya @hotmail.com hesabınız varsa, bunun yerine [Outlook.com bağlayıcısını](../connectors/connectors-create-api-outlook.md) kullanın. Outlook 'a hizmet hesabı gibi farklı bir kullanıcı hesabı ile bağlanmak için bkz. [diğer hesapları kullanarak bağlanma](#connect-using-other-accounts).

* Bir Azure hesabı ve aboneliği Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Outlook hesabınıza erişmek istediğiniz mantıksal uygulama. İş akışınızı Office 365 Outlook tetikleyicisi ile başlatmak için [boş bir mantıksal uygulamanız](../logic-apps/quickstart-create-first-logic-app-workflow.md)olması gerekir. İş akışınıza Office 365 Outlook eylemi eklemek için mantıksal uygulamanızın zaten bir tetikleyicisi olması gerekir.

## <a name="add-a-trigger"></a>Tetikleyici ekleme

[Tetikleyici](../logic-apps/logic-apps-overview.md#logic-app-concepts) , mantıksal uygulamanızda iş akışını başlatan bir olaydır. Bu örnek mantıksal uygulama, belirtilen Aralık ve sıklık temelinde e-posta hesabınızda güncelleştirilmiş herhangi bir takvim olayını denetleyen bir "yoklama" tetikleyicisi kullanır.

1. [Azure Portal](https://portal.azure.com), mantıksal uygulama tasarımcısında boş mantıksal uygulamanızı açın.

1. Arama kutusuna `office 365 outlook` filtreniz olarak yazın. Bu örnek **yakında yaklaşan bir olayın ne zaman başlaması** gerektiğini belirler.
   
   ![Mantıksal uygulamanızı başlatmak için tetikleyiciyi seçin](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Outlook hesabınızla etkin bir bağlantınız yoksa, oturum açmanız ve bu bağlantıyı oluşturmanız istenir. Outlook 'a hizmet hesabı gibi farklı bir kullanıcı hesabı ile bağlanmak için bkz. [diğer hesapları kullanarak bağlanma](#connect-using-other-accounts). Aksi takdirde, tetikleyicisinin özellikleriyle ilgili bilgileri sağlayın.

   > [!NOTE]
   > Oturum açma kimlik bilgilerinizi değiştirseniz bile, bağlantınızın kullanım süreleri dolmaz. Daha fazla bilgi için [Azure Active Directory 'de yapılandırılabilir belirteç yaşam süreleri](../active-directory/develop/active-directory-configurable-token-lifetimes.md)bölümüne bakın.

   Bu örnek, tetikleyicinin denetlediği takvimi seçer, örneğin:

   ![Tetikleyicinin özelliklerini yapılandırın](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Tetikleyicide **Sıklık** ve **Aralık** değerlerini ayarlayın. **Saat dilimi** gibi diğer kullanılabilir tetikleyici özelliklerini eklemek için **yeni parametre Ekle** listesinden bu özellikleri seçin.

   Örneğin, tetikleyicinin takvimi 15 dakikada bir denetlemesini istiyorsanız **Sıklık** değerini **dakika** olarak ayarlayın ve **aralığı** olarak ayarlayın `15` . 

   ![Tetikleyici için sıklık ve Aralık ayarlama](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Şimdi tetikleyici tetiklendiğinde çalışan bir eylem ekleyin. Örneğin, bir takvim olayı 15 dakika içinde başladığında bir metin gönderen Twilio **Ileti gönder** eylemini ekleyebilirsiniz.

## <a name="add-an-action"></a>Eylem ekleme

[Eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) , mantıksal uygulamanızdaki iş akışı tarafından çalıştırılan bir işlemdir. Bu örnek mantıksal uygulama, Office 365 Outlook 'ta yeni bir kişi oluşturur. Kişiyi oluşturmak için başka bir tetikleyiciden veya eylemden çıktıyı kullanabilirsiniz. Örneğin, mantıksal uygulamanızın **bir kayıt oluşturulduğunda** Dynamics 365 tetikleyicisini kullandığını varsayalım. Office 365 Outlook **kişi oluştur** eylemini ekleyebilir ve yeni kişiyi oluşturmak için Salesforce tetikleyicisinden çıktıları kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. İş akışınızda son adım olarak bir eylem eklemek için **yeni adım**' ı seçin. 

   Adımlar arasında bir eylem eklemek için, işaretçinizi Bu adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna `office 365 outlook` filtreniz olarak yazın. Bu örnek, **kişi oluştur** öğesini seçer.

   ![Mantıksal uygulamanızda çalıştırılacak eylemi seçin](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Outlook hesabınızla etkin bir bağlantınız yoksa, oturum açmanız ve bu bağlantıyı oluşturmanız istenir. Outlook 'a hizmet hesabı gibi farklı bir kullanıcı hesabı ile bağlanmak için bkz. [diğer hesapları kullanarak bağlanma](#connect-using-other-accounts). Aksi takdirde, eylemin özellikleriyle ilgili bilgileri sağlayın.

   > [!NOTE]
   > Oturum açma kimlik bilgilerinizi değiştirseniz bile, bağlantınızın kullanım süreleri dolmaz. Daha fazla bilgi için [Azure Active Directory 'de yapılandırılabilir belirteç yaşam süreleri](../active-directory/develop/active-directory-configurable-token-lifetimes.md)bölümüne bakın.

   Bu örnek, eylemin yeni kişiyi oluşturduğu kişiler klasörünü seçer, örneğin:

   ![Eylemin özelliklerini yapılandırın](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Diğer kullanılabilir eylem özelliklerini eklemek için bu özellikleri **yeni parametre Ekle** listesinden seçin.

1. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Diğer hesapları kullanarak bağlan

Azure 'da oturum açmış olandan farklı bir hesap kullanarak Outlook 'a bağlanmayı denerseniz, [Çoklu oturum açma (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md) hataları alabilirsiniz. Bu sorun, Azure portal bir hesapla oturum açtığınızda oluşur, ancak bağlantıyı oluşturmak için farklı bir hesap kullanın. Tasarımcı, Azure portal oturum açmış hesabı kullanmanızı bekler. Bu sorunu çözmek için şu seçeneklere sahipsiniz:

* Mantıksal uygulamanızın kaynak grubundaki **katkıda** bulunan rolüyle diğer hesabı ayarlayın.

  1. Mantıksal uygulamanızın kaynak grubu menüsünde, **erişim denetimi (IAM)** seçeneğini belirleyin. Diğer hesabı **katkıda** bulunan rolüyle ayarlayın. 
  
     Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md).

  1. Bu rolü ayarladıktan sonra, Azure portal ' de artık katkıda bulunan izinleri olan hesapla oturum açın. Outlook bağlantısını oluşturmak için artık bu hesabı kullanabilirsiniz.

* İş veya okul hesabınızda "Farklı Gönder" izinleri olması için diğer hesabı ayarlayın.

   Yönetici izinleriniz varsa, hizmet hesabının posta kutusunda iş veya okul hesabınızı, **Farklı Gönder** veya Izin **adına Gönder** seçeneğiyle ayarlayın. Daha fazla bilgi için bkz. [Diğer Kullanıcı Yöneticisi yardımına posta kutusu Izinleri verme](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Daha sonra iş veya okul hesabınızı kullanarak bağlantı oluşturabilirsiniz. Şimdi, göndereni belirtebileceğiniz Tetikleyiciler veya Eylemler ' de, hizmet hesabının e-posta adresini kullanabilirsiniz.

   Örneğin, **e-posta gönder** eyleminde, **(farklı Gönder)**, eyleme ekleyebileceğiniz ve hizmet hesabınızın e-posta adresini Gönderici olarak kullanabileceğiniz isteğe bağlı bir parametre vardır. Bu parametreyi eklemek için aşağıdaki adımları izleyin:

   1. **E-posta gönder** eyleminde **parametre Ekle** listesini açın ve **Kimden (farklı Gönder)** parametresini seçin.

   1. Eylemde parametre göründükten sonra, hizmet hesabının e-posta adresini girin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bu bağlayıcı hakkında, bağlayıcının Swagger dosyasında açıklandığı şekilde Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/office365/)bakın. 

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
