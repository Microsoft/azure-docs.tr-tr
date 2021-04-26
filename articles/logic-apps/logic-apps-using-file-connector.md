---
title: Şirket içi dosya sistemlerine bağlanma
description: Azure Logic Apps ' deki şirket içi veri ağ geçidi aracılığıyla dosya sistemi Bağlayıcısı ile şirket içi dosya sistemlerine bağlanan görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 4715d7173dd959d12350229e457717c908a83756
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91873243"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Azure Logic Apps ile şirket içi dosya sistemlerine bağlanma

Azure Logic Apps ve dosya sistemi Bağlayıcısı ile, bir şirket içi dosya paylaşımında dosya oluşturup yöneten otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

- Dosyaları oluşturun, alın, ekleyin, güncelleştirin ve silin.
- Klasörlerdeki dosyaları veya kök klasörleri listeleyin.
- Dosya içeriğini ve meta verileri alın.

  > [!IMPORTANT]
  > Dosya sistemi Bağlayıcısı Şu anda yalnızca Windows işletim sistemlerindeki Windows dosya sistemlerini desteklemektedir.  

Bu makalede, bir şirket içi dosya sistemine bu örnek senaryo tarafından açıklandığı şekilde nasıl bağlanabilmeniz gösterilmektedir: Dropbox 'a yüklenen bir dosyayı bir dosya paylaşımında kopyalama ve sonra bir e-posta gönderme. Mantıksal uygulamalar, şirket içi sistemlere güvenli bir şekilde bağlanmak ve erişmek için şirket [içi veri ağ geçidini](../logic-apps/logic-apps-gateway-connection.md)kullanır. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps? ne olduğunu](../logic-apps/logic-apps-overview.md)gözden geçirin. Bağlayıcıya özgü teknik bilgiler için, bkz. [dosya sistemi Bağlayıcısı başvurusu](/connectors/filesystem/).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Mantıksal uygulamaları, dosya sistemi sunucunuz gibi şirket içi sistemlere bağlayabilmeniz için önce şirket [içi veri ağ geçidini yüklemeniz ve ayarlamanız](../logic-apps/logic-apps-gateway-install.md)gerekir. Bu şekilde, mantıksal uygulamanızdan dosya sistemi bağlantısı oluştururken ağ geçidi yüklemenizi kullanmayı belirtebilirsiniz.

* Ücretsiz olarak kaydolabilir bir [Dropbox hesabı](https://www.dropbox.com/). Hesap kimlik bilgileriniz, mantıksal uygulamanız ve Dropbox hesabınız arasında bağlantı oluşturmak için gereklidir.

* Kullanmak istediğiniz dosya sistemine sahip olan bilgisayara erişin. Örneğin, veri ağ geçidini dosya sisteminizle aynı bilgisayara yüklerseniz, bu bilgisayarın hesap kimlik bilgilerine sahip olmanız gerekir.

* Office 365 Outlook, Outlook.com veya Gmail gibi Logic Apps tarafından desteklenen sağlayıcıdan gelen bir e-posta hesabı. Diğer sağlayıcılar için [buradaki bağlayıcı listesini inceleyin](/connectors/). Bu mantıksal uygulama bir iş veya okul hesabı kullanır. Başka bir e-posta hesabı kullanıyorsanız genel adımlar aynıdır, ancak kullanıcı arabirimi biraz farklı olabilir.

  > [!IMPORTANT]
  > Gmail bağlayıcısını kullanmak istiyorsanız, mantıksal uygulamalarda kısıtlama olmadan yalnızca G-Suite iş hesapları bu bağlayıcıyı kullanabilir. Gmail tüketicisi hesabınız varsa, bu bağlayıcıyı yalnızca belirli Google onaylı hizmetlerle kullanabilirsiniz veya [Gmail Bağlayıcınız ile kimlik doğrulaması için kullanmak üzere bir Google istemci uygulaması oluşturabilirsiniz](/connectors/gmail/#authentication-and-bring-your-own-application). Daha fazla bilgi için, bkz. [Azure Logic Apps Google bağlayıcıları Için veri güvenliği ve gizlilik ilkeleri](../connectors/connectors-google-data-security-privacy-policy.md).

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi. Bu örnekte, boş bir mantıksal uygulama gerekir.

## <a name="add-trigger"></a>Tetikleyici ekleme

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Arama kutusuna filtreniz olarak "Dropbox" yazın. Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **bir dosya oluşturulduğunda**

   ![Dropbox tetikleyicisi Seç](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Dropbox hesabınızın kimlik bilgilerinizle oturum açın ve Azure Logic Apps için Dropbox verilerinize erişim yetkisi verin.

1. Tetikleyiciniz için gerekli bilgileri sağlayın.

   ![Dropbox tetikleyicisi](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Eylem ekleme

1. Tetikleyici altında, **İleri adım**' ı seçin. Arama kutusuna filtreniz olarak "dosya sistemi" yazın. Eylemler listesinden şu eylemi seçin: **dosya oluştur**

   ![Dosya sistemi bağlayıcısını bul](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Dosya sisteminize zaten bir bağlantınız yoksa bir bağlantı oluşturmanız istenir.

   ![Bağlantı oluşturma](media/logic-apps-using-file-connector/file-system-connection.png)

   | Özellik | Gerekli | Değer | Açıklama |
   | -------- | -------- | ----- | ----------- |
   | **Bağlantı adı** | Yes | <*bağlantı adı*> | Bağlantınız için istediğiniz ad |
   | **Kök klasör** | Yes | <*kök klasörü-adı*> | Dosya sisteminiz için kök klasör; Örneğin, şirket içi veri ağ geçidinin yüklü olduğu bilgisayarda yerel bir klasör gibi şirket içi veri ağ geçidinizi veya bilgisayarın erişebileceği bir ağ paylaşımının klasörünü yüklediyseniz. <p>Örnek: `\\PublicShare\\DropboxFiles` <p>Kök klasör, tüm dosya ile ilgili eylemler için göreli yollar için kullanılan ana üst klasördür. |
   | **Kimlik doğrulama türü** | No | <*kimlik doğrulama türü*> | Dosya sisteminizin kullandığı kimlik doğrulaması türü: **Windows** |
   | **Kullanıcı adı** | Yes | <*etki* > \\ alanı < *Kullanıcı adı*> <p>-veya- <p><*Yerel* > \\ bilgisayar < *Kullanıcı adı*> | Dosya sistemi klasörünüzün bulunduğu bilgisayarın Kullanıcı adı. <p>Dosya sistemi klasörünüz şirket içi veri ağ geçidiyle aynı bilgisayarda varsa, <*Yerel bilgisayar* > \\ < *Kullanıcı adı*> kullanabilirsiniz. |
   | **Parola** | Yes | <*Parolanız*> | Dosya sisteminizin bulunduğu bilgisayarın parolası |
   | **geçidinde** | Yes | <*yüklü-ağ geçidi-adı*> | Daha önce yüklenen ağ geçidinizin adı |
   |||||

1. İşiniz bittiğinde **Oluştur**’u seçin.

   Logic Apps bağlantınızın düzgün çalıştığından emin olmak için bağlantınızı yapılandırır ve test eder. Bağlantı doğru ayarlandıysa, daha önce seçtiğiniz eylem için seçenekler görünür.

1. **Dosya oluştur** eyleminde, Dropbox 'tan şirket içi dosya paylaşımınızda bulunan kök klasöre dosya kopyalama ayrıntılarını belirtin. Önceki adımlardan çıkış eklemek için, kutuların içine tıklayın ve dinamik içerik listesi göründüğünde kullanılabilir alanlar ' ı seçin.

   ![Dosya oluşturma eylemi](media/logic-apps-using-file-connector/create-file-filled.png)

1. Şimdi, uygun kullanıcıların yeni dosya hakkındaki bilgileri bilmesi için e-posta gönderen bir Outlook eylemi ekleyin. E-postanın alıcılarını, başlığını ve gövdesini girin. Sınama için kendi e-posta adresinizi kullanabilirsiniz.

   ![E-posta eylemi gönder](media/logic-apps-using-file-connector/send-email.png)

1. Mantıksal uygulamanızı kaydedin. Dropbox 'a bir dosya yükleyerek uygulamanızı test edin.

   Mantıksal uygulamanızın dosyayı şirket içi dosya paylaşımınıza kopyalaması ve alıcılara kopyalanmış dosya hakkında bir e-posta gönderilmesi gerekir.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bu bağlayıcı hakkında, bağlayıcının Swagger dosyasında açıklanan Tetikleyiciler, Eylemler ve sınırlar gibi daha teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/fileconnector/)bakın.

> [!NOTE]
> Bir [tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü bunun yerine [Ise ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* Şirket [içi verilere bağlanmayı](../logic-apps/logic-apps-gateway-connection.md) öğrenin 
* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
