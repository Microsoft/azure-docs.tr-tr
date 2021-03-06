---
title: Mantıksal uygulamaları abonelikler, kaynak grupları veya bölgeler arasında taşıyın
description: Mantıksal uygulamaları veya tümleştirme hesaplarını diğer Azure aboneliklerine, kaynak gruplarına veya konumlarına (bölgelere) geçirin
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87826220"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Mantıksal uygulama kaynaklarını diğer Azure Kaynak gruplarına, bölgelerine veya aboneliklerine taşıyın

Mantıksal uygulamanızı veya ilgili kaynaklarınızı başka bir Azure Kaynak grubuna, bölgesine veya aboneliğine geçirmek için Azure portal, Azure PowerShell, Azure CLı ve REST API gibi bu görevleri tamamlamaya yönelik çeşitli yöntemlere sahipsiniz. Kaynakları taşımadan önce şu hususları gözden geçirin: 

* Yalnızca [belirli mantıksal uygulama kaynak türlerini](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) Azure Kaynak grupları veya abonelikleri arasında taşıyabilirsiniz.

* Azure aboneliğinizde ve her bir Azure bölgesinde sahip olduğunuz mantıksal uygulama kaynakları sayısı [sınırlarını](../logic-apps/logic-apps-limits-and-config.md) denetleyin. Bu sınırlar, bölge abonelikler veya kaynak grupları arasında aynı kaldığında belirli kaynak türlerini taşıyıp taşıyamayacağını etkiler. Örneğin, her bir Azure aboneliğinde her bir Azure bölgesi için yalnızca bir ücretsiz katman tümleştirme hesabına sahip olabilirsiniz.

* Azure, kaynakları taşıdığınızda yeni kaynak kimlikleri oluşturur. Bu nedenle, bunun yerine yeni kimlikleri kullandığınızdan emin olun ve taşınan kaynaklarla ilişkili tüm betikleri veya araçları güncelleştirin.

* Abonelikler, kaynak grupları veya bölgeler arasında mantıksal uygulamaları geçirdikten sonra, açık kimlik doğrulaması (OAuth) gerektiren tüm bağlantıları yeniden oluşturmanız veya yeniden yetkilendirmeli.

* Bir [tümleştirme hizmeti ortamını (ıSE)](connect-virtual-network-vnet-isolated-environment-overview.md) yalnızca aynı Azure bölgesinde veya Azure aboneliğinde bulunan başka bir kaynak grubuna taşıyabilirsiniz. ISE 'yi farklı bir Azure bölgesinde veya Azure aboneliğinde bulunan bir kaynak grubuna taşıyamazsınız. Ayrıca, bu tür bir taşıma işleminden sonra mantıksal uygulama iş akışlarınızda, tümleştirme hesaplarınızda, bağlantılarında ve bu durumda yer alan tüm başvuruları güncelleştirmeniz gerekir.

## <a name="prerequisites"></a>Önkoşullar

* Taşımak istediğiniz mantıksal uygulamayı veya tümleştirme hesabını oluşturmak için kullanılan aynı Azure aboneliği

* İstediğiniz kaynakları taşımak ve ayarlamak için kaynak sahibi izinleri. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/built-in-roles.md#owner)hakkında daha fazla bilgi edinin.

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Kaynakları abonelikler arasında taşıma

Mantıksal uygulama veya tümleştirme hesabı gibi bir kaynağı başka bir Azure aboneliğine taşımak için Azure portal, Azure PowerShell, Azure CLı veya REST API kullanabilirsiniz. Bu adımlar, kaynak bölgesi aynı kaldığında kullanabileceğiniz Azure portal kapsar. Diğer adımlar ve genel hazırlık için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. [Azure Portal](https://portal.azure.com), taşımak istediğiniz mantıksal uygulama kaynağını bulup seçin.

1. Kaynağın **genel bakış** sayfasında, **abonelik**' ın yanındaki bağlantıyı **Değiştir** ' i seçin.

1. **Kaynakları taşı** sayfasında, mantıksal uygulama kaynağını ve taşımak istediğiniz ilgili kaynakları seçin.

1. **Abonelik** listesinden hedef aboneliği seçin.

1. **Kaynak grubu** listesinden hedef kaynak grubunu seçin. Ya da farklı bir kaynak grubu oluşturmak için **Yeni grup oluştur**' u seçin.

1. Yeni kaynak kimlikleri ile güncelleştirene kadar taşınan kaynaklarla ilişkili tüm betiklerin veya araçların çalışmadığına emin olmak için onay kutusunu işaretleyin ve ardından **Tamam**' ı seçin.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Kaynakları kaynak grupları arasında taşıma

Mantıksal uygulama, tümleştirme hesabı veya [tümleştirme hizmeti ortamı (ıSE)](connect-virtual-network-vnet-isolated-environment-overview.md)gibi bir kaynağı başka bir Azure Kaynak grubuna taşımak için Azure portal, Azure PowerShell, Azure clı veya REST API kullanabilirsiniz. Bu adımlar, kaynak bölgesi aynı kaldığında kullanabileceğiniz Azure portal kapsar. Diğer adımlar ve genel hazırlık için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Kaynakları gruplar arasında gerçekten taşımadan önce, kaynağı başka bir gruba başarıyla taşıyıp taşıyamayacağını test edebilirsiniz. Daha fazla bilgi için bkz. [taşımanızı doğrulama](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. [Azure Portal](https://portal.azure.com), taşımak istediğiniz mantıksal uygulama kaynağını bulup seçin.

1. Kaynağın **genel bakış** sayfasında, **kaynak grubu**' nun yanındaki bağlantıyı **Değiştir** ' i seçin.

1. **Kaynakları taşı** sayfasında, mantıksal uygulama kaynağını ve taşımak istediğiniz ilgili kaynakları seçin.

1. **Kaynak grubu** listesinden hedef kaynak grubunu seçin. Ya da farklı bir kaynak grubu oluşturmak için **Yeni grup oluştur**' u seçin.

1. Yeni kaynak kimlikleri ile güncelleştirene kadar taşınan kaynaklarla ilişkili tüm betiklerin veya araçların çalışmadığına emin olmak için onay kutusunu işaretleyin ve ardından **Tamam**' ı seçin.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Kaynakları bölgeler arasında taşıma

Mantıksal uygulamayı farklı bir bölgeye taşımak istediğinizde, seçenekleriniz mantıksal uygulamanızı oluşturma yöntemine bağlıdır. Seçtiğiniz seçeneğe bağlı olarak, mantıksal uygulamanızdaki bağlantıları yeniden oluşturmanız veya yeniden yetkilendirmeli.

* Azure portal, mantıksal uygulamayı yeni bölgede yeniden oluşturun ve iş akışı ayarlarını yeniden yapılandırın. Zaman kazanmak için, temel alınan iş akışı tanımını ve bağlantıları kaynak uygulamadan hedef uygulamaya kopyalayabilirsiniz. Mantıksal uygulama arkasındaki "kodu" görüntülemek için, mantıksal uygulama Tasarımcısı araç çubuğunda **kod görünümü**' nü seçin.

* Visual Studio ve Visual Studio için Azure Logic Apps araçları 'nı kullanarak mantıksal uygulamanızı Azure portal bir [Azure Resource Manager şablonu](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)olarak [açabilir ve indirebilirsiniz](../logic-apps/manage-logic-apps-with-visual-studio.md) . Bu şablon, genellikle dağıtım için hazırlanmıştır ve mantıksal uygulamanız için iş akışının kendisi ve bağlantıları dahil kaynak tanımlarını içerir. Şablon, dağıtımda kullanılacak değerler için parametreler de bildirir. Böylece, gereksinimlerinize göre mantıksal uygulamayı nasıl ve nasıl dağıtabileceğinizi daha kolay bir şekilde değiştirebilirsiniz. Dağıtım için konumu ve diğer gerekli bilgileri belirtmek için ayrı bir parametre dosyası kullanabilirsiniz.

* Azure DevOps 'daki Azure Pipelines gibi sürekli tümleştirme (CI) ve sürekli teslim (CD) araçlarını kullanarak mantıksal uygulamanızı oluşturup dağıttıysanız, bu araçları kullanarak uygulamanızı başka bir bölgeye dağıtabilirsiniz.

Mantıksal uygulamalar için dağıtım şablonları hakkında daha fazla bilgi için şu konulara bakın:

* [Genel Bakış: Azure Resource Manager şablonları kullanarak Azure Logic Apps dağıtımı otomatikleştirin](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Mantıksal uygulamanızı Azure portal Visual Studio 'da bulun, açın ve indirin](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure Logic Apps için Azure Resource Manager şablonları oluşturma](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Logic Apps için Azure Resource Manager şablonu oluşturma](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>İlgili kaynaklar

Azure 'daki şirket içi veri ağ geçidi kaynakları gibi bazı Azure kaynakları, bu kaynakları kullanan mantıksal uygulamalardan farklı bir bölgede bulunabilir. Ancak, bağlantılı tümleştirme hesapları gibi diğer Azure kaynakları, Logic Apps ile aynı bölgede bulunmalıdır. Senaryonuza bağlı olarak, mantıksal uygulamalarınızın uygulamalarınızın aynı bölgede bulunması beklenen kaynaklara erişebildiğinden emin olun.

Örneğin, bir mantıksal uygulamayı bir tümleştirme hesabına bağlamak için her iki kaynağın de aynı bölgede bulunması gerekir. Olağanüstü durum kurtarma gibi senaryolarda, genellikle aynı yapılandırmaya ve yapıtlara sahip tümleştirme hesapları isteyeceksiniz. Diğer senaryolarda, farklı yapılandırmalara ve yapıtlara sahip tümleştirme hesaplarına ihtiyacınız bulunabilir.

Azure Logic Apps içindeki özel bağlayıcılar, aynı Azure aboneliğine ve aynı Azure Active Directory kiracısına sahip olan bağlayıcıların yazarları ve kullanıcıları tarafından görülebilir. Bu bağlayıcılar, Logic Apps 'in dağıtıldığı bölgede kullanılabilir. Daha fazla bilgi için bkz. [Kuruluşunuzda özel bağlayıcıları paylaşma](/connectors/custom-connectors/share).

Visual Studio 'dan alacağınız şablon yalnızca mantıksal uygulamanız ve bağlantıları için kaynak tanımları içerir. Bu nedenle, mantıksal uygulamanız diğer kaynakları (örneğin, iş ortakları, sözleşmeler ve şemalar gibi bir tümleştirme hesabı ve B2B yapıları) kullanıyorsa, Azure portal kullanarak bu tümleştirme hesabının şablonunu dışarı aktarmanız gerekir. Bu şablon hem tümleştirme hesabı hem de yapıtları için kaynak tanımları içerir. Ancak, şablon tamamen parametreli değildir. Bu nedenle, dağıtım için kullanmak istediğiniz değerleri el ile parametreleştirebilirsiniz.

### <a name="export-templates-for-integration-accounts"></a>Tümleştirme hesapları için şablonları dışarı aktarma

1. [Azure Portal](https://portal.azure.com)tümleştirme hesabınızı bulun ve açın.

1. Tümleştirme hesabınızın menüsünde, **Ayarlar**' ın altında, **şablonu dışarı aktar**' ı seçin.

1. Araç çubuğunda **İndir**' i seçin ve şablonu kaydedin.

1. Dağıtım için gerekli değerleri parametreleştirmek için şablonu açın ve düzenleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure kaynaklarını yeni kaynak gruplarına veya aboneliklerine taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
