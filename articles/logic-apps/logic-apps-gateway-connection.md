---
title: Şirket içindeki veri kaynaklarına erişin
description: Azure 'da bir veri ağ geçidi kaynağı oluşturarak Azure Logic Apps şirket içi veri kaynaklarına bağlanma
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 49da5d7f045ed06ba16696ebd16ad212b9d140d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763318"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps'ten şirket içi veri kaynaklarına bağlanma

Şirket [ *içi veri ağ geçidini* yerel bir bilgisayara yükledikten](../logic-apps/logic-apps-gateway-install.md) sonra ve Şirket içindeki veri kaynaklarına mantıksal uygulamalarınızdan erişebilmeniz için, Azure 'da ağ geçidi yüklemeniz için bir ağ geçidi kaynağı oluşturmanız gerekir. Daha sonra, Azure Logic Apps ' de bulunan [Şirket içi bağlayıcılar](../connectors/managed.md#on-premises-connectors) için kullanmak istediğiniz Tetikleyiciler ve Eylemler ' de bu ağ geçidi kaynağı ' nı seçebilirsiniz. Azure Logic Apps, veri ağ geçidi aracılığıyla okuma ve yazma işlemlerini destekler. Ancak, bu işlemlerin [Yük boyutuyla ilgili limitleri](/data-integration/gateway/service-gateway-onprem#considerations)vardır.

Bu makalede, [yerel bilgisayarınızda daha önce yüklenmiş bir ağ geçidi](../logic-apps/logic-apps-gateway-install.md)için Azure Gateway kaynağınızın nasıl oluşturulacağı gösterilmektedir. Ağ Geçidi hakkında daha fazla bilgi için bkz. [ağ geçidinin nasıl çalıştığı](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Azure sanal ağlarında şirket içi kaynaklara ağ geçidini kullanmak zorunda kalmadan doğrudan erişmek için bunun yerine bir [*tümleştirme hizmeti ortamı*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) oluşturmayı düşünün. 

Ağ geçidini diğer hizmetlerle kullanma hakkında daha fazla bilgi için şu makalelere bakın:

* [Microsoft Power otomatikleştirir şirket içi veri ağ geçidi](/power-automate/gateway-reference)
* [Microsoft Power BI şirket içi veri ağ geçidi](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps şirket içi veri ağ geçidi](/powerapps/maker/canvas-apps/gateway-reference)
* [Şirket içi veri ağ geçidini Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları

Azure Logic Apps, şirket içi veri ağ geçidi, bu veri kaynakları için şirket [içi bağlayıcıları](../connectors/managed.md#on-premises-connectors) destekler:

* BizTalk Server 2016
* Dosya Sistemi
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Veritabanı
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

REST veya SOAP kullanarak, HTTP veya HTTPS üzerinden veri kaynaklarına bağlanan [özel bağlayıcılar](../logic-apps/custom-connector-overview.md) da oluşturabilirsiniz. Ağ geçidinin kendisi ek maliyet uygulamaz, ancak [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md) bu bağlayıcılar ve Azure Logic Apps diğer işlemler için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

* Şirket [içi veri ağ geçidini zaten yerel bir bilgisayara yüklediniz](../logic-apps/logic-apps-gateway-install.md). Bu yüklemeye bağlantı sağlayan bir ağ geçidi kaynağı oluşturabilmeniz için bu ağ geçidi yüklemesinin mevcut olması gerekir.

* Ağ Geçidi yüklemeniz için kullandığınız [Azure hesabı ve aboneliğiniz](../logic-apps/logic-apps-gateway-install.md#requirements) vardır. Bu Azure hesabı yalnızca tek bir [Azure Active Directory (Azure AD) kiracısına veya dizine](../active-directory/fundamentals/active-directory-whatis.md#terminology)ait olmalıdır. Yalnızca ağ geçidi Yöneticisi Azure 'da ağ geçidi kaynağını oluşturabileceğinden, Azure 'da ağ geçidi kaynağınızı oluşturmak için aynı Azure hesabı ve aboneliğini kullanmanız gerekir. Hizmet sorumluları Şu anda desteklenmiyor.

  * Azure 'da bir ağ geçidi kaynağı oluşturduğunuzda, ağ geçidi kaynağı ve yalnızca bu ağ geçidi kaynağı ile bağlantı için bir ağ geçidi yüklemesi seçersiniz. Her ağ geçidi kaynağı yalnızca bir ağ geçidi yüklemesine bağlanabilir. Zaten başka bir ağ geçidi kaynağıyla ilişkilendirilmiş bir ağ geçidi yüklemesi seçemezsiniz.

  * Mantıksal uygulamanız ve ağ geçidi kaynağınızın aynı Azure aboneliğinde mevcut olması gerekmez. Ağ Geçidi kaynağını kullanabileceğiniz Tetikleyiciler ve Eylemler ' de, ağ geçidi kaynağına sahip farklı bir Azure aboneliği seçebilirsiniz, ancak yalnızca bu abonelik, mantıksal uygulamanızla aynı Azure AD kiracısında veya dizinde bulunuyorsa olabilir. Diğer bir yöneticinin sizin için ayarlayabileceği ağ geçidinde yönetici izinlerine de sahip olmanız gerekir. Daha fazla bilgi için bkz. [Data Gateway: Automation with PowerShell-Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) ve [PowerShell: Data Gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > Şu anda birden çok abonelik arasında bir ağ geçidi kaynağı veya yüklemesi paylaşamazsınız. Ürün geri bildirimi göndermek için [Microsoft Azure geri bildirim Forumu](https://feedback.azure.com/forums/34192--general-feedback)' na bakın.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure ağ geçidi kaynağı oluşturma

Ağ geçidini yerel bir bilgisayara yükledikten sonra, ağ geçidiniz için Azure kaynağı oluşturun.

1. Ağ geçidini yüklemek için kullanılan aynı Azure hesabıyla [Azure Portal](https://portal.azure.com) oturum açın.

1. Azure portal arama kutusuna "Şirket içi veri ağ geçidi" girin ve şirket **Içi veri ağ geçitleri**' ni seçin.

   !["Şirket içi veri ağ geçidi" ni bulun](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. **Şirket Içi veri ağ geçitleri** altında **Ekle**' yi seçin.

   ![Veri ağ geçidi için yeni Azure kaynağı ekleme](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. **Bağlantı ağ geçidi oluştur** altında, ağ geçidi kaynağınız için bu bilgileri sağlayın. İşiniz bittiğinde **Oluştur**'u seçin.

   | Özellik | Açıklama |
   |----------|-------------|
   | **Kaynak Adı** | Ağ Geçidi kaynağınız için yalnızca harf, sayı, kısa çizgi ( `-` ), alt çizgi ( `_` ), parantez ( `(` , `)` ) veya nokta ( `.` ) içeren bir ad sağlayın. |
   | **Abonelik** | Ağ geçidi yüklemesi için kullanılan Azure hesabı için Azure aboneliğini seçin. Varsayılan abonelik, oturum açmak için kullandığınız Azure hesabını temel alır. |
   | **Kaynak grubu** | Kullanmak istediğiniz [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) |
   | **Konum** | Ağ [geçidi yüklemesi](../logic-apps/logic-apps-gateway-install.md)sırasında ağ geçidi bulutu hizmeti için seçilen bölge veya konum. Aksi takdirde, ağ geçidi yüklemeniz **yükleme adı** listesinde görünmez. Mantıksal uygulama konumunuz, ağ geçidi kaynak konumunuzda farklılık gösterebilir. |
   | **Yükleme adı** | Listede yalnızca bu koşullar karşılandığında görüntülenen bir ağ geçidi yüklemesi seçin: <p><p>-Ağ geçidi yüklemesi, oluşturmak istediğiniz ağ geçidi kaynağıyla aynı bölgeyi kullanır. <br>-Ağ geçidi yüklemesi başka bir Azure ağ geçidi kaynağıyla bağlantılı değildir. <br>-Ağ geçidi yüklemesi, ağ geçidi kaynağını oluşturmak için kullandığınız Azure hesabıyla bağlantılıdır. <br>-Azure hesabınız tek bir [Azure Active Directory (Azure AD) kiracısına veya dizine](../active-directory/fundamentals/active-directory-whatis.md#terminology) aittir ve ağ geçidi yüklemesi için kullandığınız hesaptır. <p><p>Daha fazla bilgi için [sık sorulan sorular](#faq) bölümüne bakın. |
   |||

   Ağ Geçidi kaynağınız ile aynı bölgede olan ve aynı Azure hesabına bağlanan bir ağ geçidi yüklemesi gösteren bir örnek aşağıda verilmiştir:

   ![Veri ağ geçidi kaynağı oluşturma ayrıntılarını sağlayın](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Şirket içi verilere bağlanma

Ağ Geçidi kaynağınızı oluşturduktan ve Azure aboneliğinizi bu kaynakla ilişkilendirdikten sonra, ağ geçidini kullanarak mantıksal uygulamanız ve şirket içi veri kaynağınız arasında bir bağlantı oluşturabilirsiniz.

1. Azure portal mantıksal uygulama tasarımcısında mantıksal uygulamanızı oluşturun veya açın.

1. Şirket içi bağlantıları destekleyen bir bağlayıcı ekleyin, örneğin **SQL Server**.

1. Şirket **içi veri ağ geçidi üzerinden Bağlan '** ı seçin.

1. **Ağ geçidi** altında, **abonelik** listesinden istediğiniz ağ geçidi kaynağına sahip Azure aboneliğinizi seçin.

   Mantıksal uygulamanız ve ağ geçidi kaynağınızın aynı Azure aboneliğinde mevcut olması gerekmez. Her birinde bir ağ geçidi kaynağına sahip olan diğer Azure aboneliklerinden seçim yapabilirsiniz, ancak bu abonelikler yalnızca mantıksal uygulamanızla aynı Azure AD kiracısında veya dizinde bulunuyorsa ve ağ geçidinde yönetici izinleriniz varsa, başka bir yöneticinin sizin için ayarlayabileceği anlamına gelir. Daha fazla bilgi için bkz. [Data Gateway: Automation with PowerShell-Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) ve [PowerShell: Data Gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. Seçili aboneliğinizdeki kullanılabilir ağ geçidi kaynaklarını gösteren **bağlantı ağ geçidi** listesinden istediğiniz ağ geçidi kaynağını seçin. Her ağ geçidi kaynağı, tek bir ağ geçidi yüklemesine bağlanır.

   > [!NOTE]
   > Mantıksal uygulamanızın konumu ağ geçidi kaynağınızın konumundan farklı olabileceğinden, ağ geçitleri listesi diğer bölgelerdeki ağ geçidi kaynaklarını içerir. 

1. Benzersiz bir bağlantı adı ve oluşturmak istediğiniz bağlantıya bağlı diğer gerekli bilgileri sağlayın.

   Benzersiz bir bağlantı adı, özellikle birden çok bağlantı oluşturuyorsanız bu bağlantıyı daha sonra kolayca bulmanıza yardımcı olur. Uygulanabiliyorsa, Kullanıcı adınız için tam etki alanını da dahil edin.

   Aşağıda bir örnek verilmiştir:

   ![Mantıksal uygulama ve veri ağ geçidi arasında bağlantı oluşturma](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. İşiniz bittiğinde **Oluştur**'u seçin.

Ağ Geçidi bağlantınız artık mantıksal uygulamanızın kullanması için hazırdır.

## <a name="edit-connection"></a>Bağlantıyı Düzenle

Bir ağ geçidi bağlantısının ayarlarını güncelleştirmek için bağlantınızı düzenleyebilirsiniz.

1. Yalnızca mantıksal uygulamanız için tüm API bağlantılarını bulmak için, mantıksal uygulamanızın menüsünde, **geliştirme araçları**' nın altında **API bağlantıları**' nı seçin.

   ![Mantıksal uygulama menünüzde "API bağlantıları" nı seçin](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. İstediğiniz ağ geçidi bağlantısını seçin ve ardından **API bağlantısını Düzenle**' yi seçin.

   > [!TIP]
   > Güncelleştirmelerinizin etkisi yoksa ağ geçidi yüklemeniz için [ağ geçidi Windows hizmeti hesabını durdurup yeniden başlatmayı](../logic-apps/logic-apps-gateway-install.md#restart-gateway) deneyin.

Azure aboneliğinizle ilişkili tüm API bağlantılarını bulmak için:

* Azure Portal menüsünde, **tüm hizmetler**  >  **Web**  >  **API bağlantıları**' nı seçin.
* Ya da Azure portal menüsünde **tüm kaynaklar**' ı seçin. **Tür** filtresini **API bağlantısı** olarak ayarlayın.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ağ Geçidi kaynağını Sil

Farklı bir ağ geçidi kaynağı oluşturmak, ağ geçidi yüklemenizi farklı bir ağ geçidi kaynağına bağlamak veya ağ geçidi kaynağını kaldırmak için ağ geçidi yüklemesini etkilemeden ağ geçidi kaynağını silebilirsiniz.

1. Azure portal menüsünden **tüm kaynaklar**' ı seçin veya herhangi bir sayfadan **tüm kaynakları** arayıp seçin. Ağ Geçidi kaynağınızı bulun ve seçin.

1. Henüz seçili değilse, ağ geçidi kaynak menüsünde Şirket **Içi veri ağ geçidi**' ni seçin. Ağ Geçidi kaynağı araç çubuğunda **Sil**' i seçin.

   Örnek:

   ![Azure 'da ağ geçidi kaynağını silme](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S**: Azure 'da ağ geçidi kaynağı oluşturdığımda ağ geçidi yüklemem neden görünmüyor? <br/>
Y **: Bu** sorun şu nedenlerden kaynaklanabilir:

* Azure hesabınız, yerel bilgisayarınızdaki ağ geçidi yüklemesi için kullandığınız hesap değildir. Ağ geçidi yüklemesi için kullandığınız kimlik ile Azure portal oturum açtığınızdan emin olun. Yalnızca ağ geçidi Yöneticisi, Azure 'da ağ geçidi kaynağı oluşturabilir. Hizmet sorumluları Şu anda desteklenmiyor.

* Azure hesabınız yalnızca tek bir [Azure AD kiracısına veya dizine](../active-directory/fundamentals/active-directory-whatis.md#terminology)ait değil. Ağ geçidi yüklemesi sırasında kullandığınız Azure AD kiracısı veya dizinini kullandığınızdan emin olun.

* Ağ Geçidi kaynağınız ve ağ geçidi yüklemeniz aynı bölgede yok. Ancak mantıksal uygulamanızın konumu, ağ geçidi kaynağı konumınızdan farklı olabilir.

* Ağ Geçidi yüklemeniz zaten başka bir ağ geçidi kaynağıyla ilişkili. Her ağ geçidi kaynağı yalnızca bir Azure hesabına ve aboneliğine bağlanabilir ve yalnızca bir ağ geçidi yüklemesine bağlanabilir. Bu nedenle, zaten başka bir ağ geçidi kaynağıyla ilişkilendirilmiş bir ağ geçidi yüklemesi seçemezsiniz. Bu yüklemeler **yükleme adı** listesinde görünmez.

  Azure portal ağ geçidi kayıtlarınızı gözden geçirmek için *Tüm* Azure aboneliklerinizde **Şirket Içi veri ağ geçitleri** kaynak türüne sahip tüm Azure kaynaklarınızı bulun. Ağ Geçidi yüklemesinin diğer ağ geçidi kaynağıyla bağlantısını kaldırmak için bkz. [ağ geçidi kaynağını silme](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal uygulamanızı güvenli hale getirme](./logic-apps-securing-a-logic-app.md)
* [Logic Apps için ortak örnekler ve senaryolar](./logic-apps-examples-and-scenarios.md)
