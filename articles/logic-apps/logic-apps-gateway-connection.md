---
title: Şirket içi Azure Logic Apps veri kaynaklarına erişin
description: Azure şirket içi veri ağ geçidi kaynağı oluşturarak Azure Logic Apps şirket içi veri kaynaklarına bağlanma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 20cdcfdd3795a7614c054ed96b82cc99fc2c5894
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068903"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps şirket içi veri kaynaklarına bağlanma

Mantıksal uygulamalarınızdan şirket içi veri kaynaklarına erişebilmek için önce [yerel bir bilgisayara Şirket *içi veri ağ geçidini* ](../logic-apps/logic-apps-gateway-install.md)yükledikten sonra bir Azure kaynağı oluşturmanız gerekir. Daha sonra mantıksal uygulamalarınız bu Azure ağ geçidi kaynağını, Azure Logic Apps için mevcut olan [Şirket içi bağlayıcılar](../connectors/apis-list.md#on-premises-connectors) tarafından sağlanan Tetikleyiciler ve eylemlerde kullanır.

Bu makalede, [yerel bilgisayarınızda daha önce yüklenmiş bir ağ geçidi](../logic-apps/logic-apps-gateway-install.md)için Azure Gateway kaynağınızın nasıl oluşturulacağı gösterilmektedir. Ağ Geçidi hakkında daha fazla bilgi için bkz. [ağ geçidinin nasıl çalıştığı](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Azure sanal ağlarına bağlanmak için bunun yerine bir [*tümleştirme hizmeti ortamı*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) oluşturmayı düşünün. 

Ağ geçidini diğer hizmetlerle kullanma hakkında daha fazla bilgi için şu makalelere bakın:

* [Microsoft Power otomatikleştirir şirket içi veri ağ geçidi](/power-automate/gateway-reference)
* [Microsoft Power BI şirket içi veri ağ geçidi](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps şirket içi veri ağ geçidi](/powerapps/maker/canvas-apps/gateway-reference)
* [Şirket içi veri ağ geçidini Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları

Azure Logic Apps, şirket içi veri ağ geçidi, bu veri kaynakları için şirket [içi bağlayıcıları](../connectors/apis-list.md#on-premises-connectors) destekler:

* BizTalk Server 2016
* Dosya Sistemi
* IBM DB2  
* IBM Informix
* ıBM MQ
* MySQL
* Oracle Veritabanı
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure Logic Apps, veri ağ geçidi aracılığıyla okuma ve yazma işlemlerini destekler. Ancak, bu işlemlerin [Yük boyutuyla ilgili limitleri](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)vardır. Ağ geçidinin kendisi ek maliyet gerçekleştirmese de, [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md) bu bağlayıcılar ve Azure Logic Apps diğer işlemler için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

* Şirket [içi veri ağ geçidini zaten yerel bir bilgisayara yüklediniz](../logic-apps/logic-apps-gateway-install.md).

* Bu veri ağ geçidini yüklerken kullanılan [Azure hesabı ve aboneliğini](../logic-apps/logic-apps-gateway-install.md#requirements) kullanıyorsunuz. Bu Azure hesabı tek bir [Azure Active Directory (Azure AD) kiracısına veya dizine](../active-directory/fundamentals/active-directory-whatis.md#terminology)ait olmalıdır.

* Ağ Geçidi yüklemeniz zaten kayıtlı değil ve başka bir Azure ağ geçidi kaynağı tarafından talep edildi.

  Azure portal bir ağ geçidi kaynağı oluşturduğunuzda, ağ geçidi kaynağına ve yalnızca o ağ geçidi kaynağına bağlanan bir ağ geçidi yüklemesi seçersiniz. Azure Logic Apps, şirket içi Tetikleyiciler ve Eylemler ' de şirket içi veri kaynaklarına bağlanmak için ağ geçidi kaynağını kullanır. Bu Tetikleyiciler ve Eylemler ' de, kullanmak istediğiniz Azure aboneliğinizi ve ilişkili ağ geçidi kaynağını seçersiniz. Her ağ geçidi kaynağı yalnızca bir Azure hesabına bağlanan yalnızca bir ağ geçidi yüklemesine bağlanır.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure ağ geçidi kaynağı oluşturma

Ağ geçidini yerel bir bilgisayara yükledikten sonra, ağ geçidiniz için Azure kaynağı oluşturun.

1. Ağ geçidini yüklemek için kullanılan aynı Azure hesabıyla [Azure Portal](https://portal.azure.com) oturum açın.

1. Azure portal arama kutusuna "Şirket içi veri ağ geçidi" girin ve şirket **Içi veri ağ geçitleri**' ni seçin.

   !["Şirket içi veri ağ geçidi" ni bulun](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. **Şirket Içi veri ağ geçitleri**altında **Ekle**' yi seçin.

   ![Veri ağ geçidi için yeni Azure kaynağı ekleme](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. **Bağlantı ağ geçidi oluştur**altında, ağ geçidi kaynağınız için bu bilgileri sağlayın. İşiniz bittiğinde **Oluştur**’u seçin.

   | Özellik | Açıklama |
   |----------|-------------|
   | **Kaynak Adı** | Ağ Geçidi kaynağınız için yalnızca harf, sayı, kısa çizgi (`-`), alt çizgi (`_`), parantezler (`(`, `)`) veya nokta (`.`) içeren bir ad sağlayın. |
   | **Abonelik** | Ağ geçidi yüklemesi için kullanılan Azure hesabı için Azure aboneliğini seçin. Varsayılan abonelik, oturum açmak için kullandığınız Azure hesabını temel alır. |
   | **Kaynak grubu** | Kullanmak istediğiniz [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) |
   | **Konum** | Ağ [geçidi yüklemesi](../logic-apps/logic-apps-gateway-install.md)sırasında ağ geçidi bulutu hizmeti için seçilen bölge veya konum. Aksi takdirde, ağ geçidi yüklemeniz **yükleme adı** listesinde görünmez. Mantıksal uygulama konumunuz, ağ geçidi kaynak konumunuzda farklılık gösterebilir. |
   | **Yükleme adı** | Listede yalnızca bu koşullar karşılandığında görüntülenen bir ağ geçidi yüklemesi seçin: <p><p>-Ağ geçidi yüklemesi, oluşturmak istediğiniz ağ geçidi kaynağıyla aynı bölgeyi kullanır. <br>-Ağ geçidi yüklemesi başka bir Azure ağ geçidi kaynağıyla bağlantılı değildir. <br>-Ağ geçidi yüklemesi, ağ geçidi kaynağını oluşturmak için kullandığınız Azure hesabıyla bağlantılıdır. <br>-Azure hesabınız tek bir [Azure Active Directory (Azure AD) kiracısına veya dizine](../active-directory/fundamentals/active-directory-whatis.md#terminology) aittir ve ağ geçidi yüklemesi için kullanılan hesaptır. <p><p>Daha fazla bilgi için [sık sorulan sorular](#faq) bölümüne bakın. |
   |||

   Ağ Geçidi kaynağınız ile aynı bölgede olan ve aynı Azure hesabına bağlanan bir ağ geçidi yüklemesi gösteren bir örnek aşağıda verilmiştir:

   ![Veri ağ geçidi kaynağı oluşturma ayrıntılarını sağlayın](./media/logic-apps-gateway-connection/add-azure-data-gateway-information.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Şirket içi verilere bağlanma

Ağ Geçidi kaynağınızı oluşturduktan ve Azure aboneliğinizi bu kaynakla ilişkilendirdikten sonra, ağ geçidini kullanarak mantıksal uygulamanız ve şirket içi veri kaynağınız arasında bir bağlantı oluşturabilirsiniz.

1. Azure portal mantıksal uygulama tasarımcısında mantıksal uygulamanızı oluşturun veya açın.

1. Şirket içi bağlantıları destekleyen bir bağlayıcı ekleyin, örneğin **SQL Server**.

1. Şirket **içi veri ağ geçidi üzerinden Bağlan '** ı seçin.

1. **Ağ geçitleri**altında, **abonelikler** listesinden istediğiniz ağ geçidi kaynağına sahip Azure aboneliğinizi seçin.

1. Seçili aboneliğinizdeki kullanılabilir ağ geçidi kaynaklarını gösteren **bağlantı ağ geçidi** listesinden istediğiniz ağ geçidi kaynağını seçin. Her ağ geçidi kaynağı, tek bir ağ geçidi yüklemesine bağlanır.

   > [!NOTE]
   > Mantıksal uygulamanızın konumu ağ geçidi kaynağınızın konumundan farklı olabileceğinden, ağ geçitleri listesi diğer bölgelerdeki ağ geçidi kaynaklarını içerir. 

1. Benzersiz bir bağlantı adı ve oluşturmak istediğiniz bağlantıya bağlı diğer gerekli bilgileri sağlayın.

   Benzersiz bir bağlantı adı, özellikle birden çok bağlantı oluşturuyorsanız bu bağlantıyı daha sonra kolayca bulmanıza yardımcı olur. Uygulanabiliyorsa, Kullanıcı adınız için tam etki alanını da dahil edin.

   Örnek aşağıda verilmiştir:

   ![Mantıksal uygulama ve veri ağ geçidi arasında bağlantı oluşturma](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. İşiniz bittiğinde **Oluştur**’u seçin.

Ağ Geçidi bağlantınız artık mantıksal uygulamanızın kullanması için hazırdır.

## <a name="edit-connection"></a>Bağlantıyı Düzenle

Bir ağ geçidi bağlantısının ayarlarını güncelleştirmek için bağlantınızı düzenleyebilirsiniz.

1. Yalnızca mantıksal uygulamanız için tüm API bağlantılarını bulmak için, mantıksal uygulamanızın menüsünde, **geliştirme araçları**' nın altında **API bağlantıları**' nı seçin.

   ![Mantıksal uygulama menünüzde "API bağlantıları" nı seçin](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. İstediğiniz ağ geçidi bağlantısını seçin ve ardından **API bağlantısını Düzenle**' yi seçin.

   > [!TIP]
   > Güncelleştirmelerinizin etkisi yoksa ağ geçidi yüklemeniz için [ağ geçidi Windows hizmeti hesabını durdurup yeniden başlatmayı](../logic-apps/logic-apps-gateway-install.md#restart-gateway) deneyin.

Azure aboneliğinizle ilişkili tüm API bağlantılarını bulmak için:

* Ana Azure menüsünden **tüm hizmetler** > **Web** > **API bağlantıları**' na gidin.
* Ya da, ana Azure menüsünden **tüm kaynaklar**' a gidin. **Tür** filtresini **API bağlantısı**olarak ayarlayın.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ağ Geçidi kaynağını Sil

Farklı bir ağ geçidi kaynağı oluşturmak, ağ geçidi yüklemenizi farklı bir ağ geçidi kaynağına bağlamak veya ağ geçidi kaynağını kaldırmak için ağ geçidi yüklemesini etkilemeden ağ geçidi kaynağını silebilirsiniz.

1. Ana Azure menüsünden **tüm kaynaklar**' ı seçin. Ağ Geçidi kaynağınızı bulun ve seçin.

1. Henüz seçili değilse, ağ geçidi kaynak menüsünde Şirket **Içi veri ağ geçidi**' ni seçin. Ağ Geçidi kaynağı araç çubuğunda **Sil**' i seçin.

   Örneğin:

   ![Azure 'da ağ geçidi kaynağını silme](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway-resource.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S**: Azure 'da ağ geçidi kaynağı oluşturdığımda ağ geçidi yüklemem neden görünmüyor? <br/>
Y **: Bu**sorun şu nedenlerden kaynaklanabilir:

* Azure hesabınız, yerel bilgisayardaki ağ geçidi yüklemesiyle bağlantılı olan hesapla aynı olmalıdır. Ağ Geçidi yüklemesiyle bağlantılı aynı kimliğe sahip Azure portal oturum açtığınızdan emin olun. Ayrıca, Azure hesabınızın tek bir [Azure AD kiracısı veya dizinine](../active-directory/fundamentals/active-directory-whatis.md#terminology) ait olduğundan ve ağ geçidi yüklemesi sırasında kullanılan aynı Azure AD kiracısı veya dizinine ayarlandığından emin olun.

* Ağ Geçidi kaynağınız ve ağ geçidi yüklemenizin aynı bölgeyi kullanması gerekir. Ancak, mantıksal uygulama konumunuz ağ geçidi kaynağı konumınızdan farklı olabilir.

* Ağ Geçidi yüklemeniz zaten kayıtlı ve başka bir ağ geçidi kaynağı tarafından talep edildi. Bu yüklemeler **yükleme adı** listesinde görünmez. Azure portal ağ geçidi kayıtlarınızı gözden geçirmek için *Tüm* Azure aboneliklerinizde **Şirket Içi veri ağ geçitleri** türüne sahip tüm Azure kaynaklarınızı bulun. Ağ Geçidi yüklemesinin diğer ağ geçidi kaynağıyla bağlantısını kaldırmak için bkz. [ağ geçidi kaynağını silme](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal uygulamanızı güvenli hale getirme](./logic-apps-securing-a-logic-app.md)
* [Logic Apps için ortak örnekler ve senaryolar](./logic-apps-examples-and-scenarios.md)
