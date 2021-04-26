---
title: B2B tümleştirme hesapları oluşturma veya yönetme
description: Azure Logic Apps ile kurumsal tümleştirme için tümleştirme hesapları oluşturun, bağlayın ve yönetin
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: ae5ca6ac822dabd32b6463c3a742901f32b34323
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862264"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Azure Logic Apps'te B2B kurumsal tümleştirmeleri için tümleştirme hesabı oluşturma ve yönetme

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) kullanarak [kurumsal tümleştirme ve B2B çözümleri](../logic-apps/logic-apps-enterprise-integration-overview.md) oluşturabilmek için önce bir tümleştirme hesabı oluşturmanız gerekir. Bu hesap, mantıksal uygulama iş akışlarınızla tanımladığınız ve kullandığınız tümleştirme yapıları için güvenli, ölçeklenebilir ve yönetilebilir bir kapsayıcı sağlayan ayrı bir Azure kaynağıdır.

Örneğin, ticari iş ortakları, anlaşmalar, Haritalar, şemalar, sertifikalar ve toplu iş konfigürasyonları gibi B2B yapıtları oluşturabilir, saklayabilir ve yönetebilirsiniz. Ayrıca, mantıksal uygulamanızın bu yapıtlar ile çalışabilmesi ve Logic Apps B2B bağlayıcılarını kullanabilmesi için, [tümleştirme hesabınızı](#link-account) mantıksal uygulamanıza bağlamanız gerekir. Tümleştirme hesabınız ve mantıksal uygulamanız *aynı* konumda veya bölgede bulunmalıdır.

> [!IMPORTANT]
> Seçtiğiniz tümleştirme hesabı türüne bağlı olarak, bir tümleştirme hesabı oluşturmak maliyet doğurur. Daha fazla bilgi için bkz. [Logic Apps fiyatlandırma ve faturalandırma modelleri](logic-apps-pricing.md#integration-accounts) ve [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/).

Bu konu başlığı altında, bu görevlerin nasıl gerçekleştirileceği gösterilmektedir:

* Tümleştirme hesabınızı oluşturun.

  > [!TIP]
  > [Tümleştirme hizmeti ortamında](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)bir tümleştirme hesabı oluşturmak için, bkz. [bir Ise tümleştirme hesapları oluşturma](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

* Tümleştirme hesabınızı bir Logic App 'e bağlayın.

* Tümleştirme hesabınız için fiyatlandırma katmanını değiştirin.

* Tümleştirme hesabınızın mantıksal bir uygulamayla bağlantısını kaldırın.

* Tümleştirme hesabınızı başka bir Azure Kaynak grubuna veya aboneliğine taşıyın.

* Tümleştirme hesabınızı silin.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Tümleştirme hesabı oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Bu görev için, bu bölümdeki Azure portal, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)veya [Azure CLI](/cli/azure/resource#az_resource_create)içindeki adımları izleyerek kullanabilirsiniz.

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure ana menüsünde **Kaynak oluştur**'u seçin. Arama kutusuna filtreniz olarak "tümleştirme hesabı" girin ve **tümleştirme hesabı**' nı seçin.

   ![Yeni tümleştirme hesabı oluştur](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. **Tümleştirme hesabı** altında **Oluştur**' u seçin.

   ![Tümleştirme hesabı oluşturmak için "Ekle" yi seçin](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Tümleştirme hesabınız hakkında şu bilgileri sağlayın:

   ![Tümleştirme hesabı ayrıntılarını sağlayın](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Ad** | Yes | <*Tümleştirme-hesap-adı*> | Tümleştirme hesabınızın adı, yalnızca harf, rakam, kısa çizgi ( `-` ), alt çizgi ( `_` ), parantez ( `(` , `)` ) ve nokta ( `.` ) içerebilir. Bu örnek "Fabrikam-Integration" kullanır. |
   | **Abonelik** | Yes | <*Azure-abonelik-adı*> | Azure aboneliğinizin adı |
   | **Kaynak grubu** | Yes | <*Azure-Resource-Group-Name*> | İlgili kaynakları düzenlemek için kullanılacak [Azure Kaynak grubunun](../azure-resource-manager/management/overview.md) adı. Bu örnekte, "FabrikamIntegration-RG" adlı yeni bir kaynak grubu oluşturun. |
   | **Fiyatlandırma Katmanı** | Yes | <*fiyatlandırma düzeyi*> | Tümleştirme hesabının daha sonra değiştirebileceğiniz fiyatlandırma katmanı. Bu örnekte, **ücretsiz**' ı seçin. Daha fazla bilgi için şu konulara bakın: <p>- [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps sınırları ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Konum** | Yes | <*Azure-bölge*> | Tümleştirme hesabı meta verilerinizin depolanacağı bölge. Mantıksal uygulamanızla aynı konumu seçin ya da mantıksal uygulamalarınızı tümleştirme hesabınızla aynı konumda oluşturun. Bu örnekte, "Batı ABD" kullanın. <p>**Note**: bir [tümleştirme hizmeti ortamı (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)içinde bir tümleştirme hesabı oluşturmak için, konum olarak o Ise 'yi seçin. Daha fazla bilgi için bkz. [BIR ıSE tümleştirme hesapları oluşturma](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | No | Kapalı, açık | Bu örnek için **kapalı** ayarını değiştirmeyin. |
   |||||

1. İşiniz bittiğinde **Oluştur**' u seçin.

   Dağıtım tamamlandıktan sonra, Azure tümleştirme hesabınızı açar.

   ![Azure, tümleştirme hesabı 'nı açar](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Mantıksal uygulamanızın tümleştirme hesabınızı kullanabilmesi için, tümleştirme hesabınızı ve mantıksal uygulamanızı birlikte bağlamak üzere sonraki adımları izleyin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu bölümdeki Azure CLı komutlarını kullanarak bir tümleştirme hesabı oluşturabilirsiniz.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Tümleştirme hesabı oluşturma

Tümleştirme hesabı oluşturmak için bu komutları kullanın.

1. [Az Logic Integration-Account](/cli/azure/logic/integration-account) uzantısını eklemek için [az Extension Add](/cli/azure/extension#az_extension_add) komutunu kullanın:

   ```azurecli
   az extension add –-name logic
   ```

1. Bir kaynak grubu oluşturmak veya var olan bir kaynak grubunu kullanmak için [az Group Create](/cli/azure/group#az_group_create) komutunu çalıştırın:

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Bir kaynak grubunun tümleştirme hesaplarını listelemek için [az Logic Integration-Account List](/cli/azure/logic/integration-account#az_logic_integration_account_list) komutunu kullanın:

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Bir tümleştirme hesabı oluşturmak için [az Logic Integration-Account Create](/cli/azure/logic/integration-account#az_logic_integration_account_create) komutunu çalıştırın:

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Tümleştirme hesabınızın adı yalnızca harf, sayı, kısa çizgi (-), alt çizgi (_), parantez ((,)) ve nokta (.) içerebilir.

   > [!TIP]
   > Bir [tümleştirme hizmeti ortamı (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)içinde bir tümleştirme hesabı oluşturmak için, konum olarak o Ise 'yi seçin. Daha fazla bilgi için bkz. [BIR ıSE tümleştirme hesapları oluşturma](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

   Belirli bir tümleştirme hesabını görüntülemek için [az Logic Integration-Account Show](/cli/azure/logic/integration-account#az_logic_integration_account_show) komutunu kullanın:

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   [Az Logic Integration-Account Update](/cli/azure/logic/integration-account#az_logic_integration_account_update) komutunu kullanarak SKU veya fiyatlandırma katmanınızı değiştirebilirsiniz:

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Fiyatlandırma hakkında daha fazla bilgi için şu kaynaklara bakın:

   * [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps sınırları ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/)

Bir JSON dosyası kullanarak bir tümleştirme hesabını içeri aktarmak için [az Logic Integration-Account Import](/cli/azure/logic/integration-account#az_logic_integration_account_import) komutunu kullanın:

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

[Az Logic Integration-Account Delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete) komutunu kullanarak bir tümleştirme hesabını silebilirsiniz:

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Mantıksal uygulamanızın tümleştirme hesabınızı kullanabilmesi için tümleştirme hesabınızı ve mantıksal uygulamanızı birlikte bağlayın. Sonraki bölümde bağlama açıklanmaktadır.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Mantıksal uygulama ile bağlantı oluşturma

Mantıksal uygulamalarınıza B2B yapıtlarınızı içeren bir tümleştirme hesabına erişim sağlamak için öncelikle tümleştirme hesabınızı mantıksal uygulamanıza bağlamanız gerekir. Aynı bölgede hem mantıksal uygulama hem de tümleştirme hesabı bulunmalıdır. Bu görevi gerçekleştirmek için Azure portal kullanabilirsiniz. Visual Studio kullanıyorsanız ve mantıksal uygulamanız bir [Azure Kaynak grubu projesinde](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)ise, [mantıksal uygulamanızı Visual Studio kullanarak bir tümleştirme hesabına bağlayabilirsiniz](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Azure portal, mantıksal uygulamanızı bulun ve açın.

1. [Azure Portal](https://portal.azure.com), var olan bir mantıksal uygulamayı açın veya yeni bir mantıksal uygulama oluşturun.

1. Mantıksal uygulamanızın menüsünde, **Ayarlar**' ın altında, **iş akışı ayarları**' nı seçin. **Tümleştirme hesabı** altında, **bir tümleştirme hesabı seç** listesini açın. Mantıksal uygulamanıza bağlanacak tümleştirme hesabını seçin.

   ![Tümleştirme hesabınızı seçin](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Bağlamayı tamamlaması için **Kaydet**' i seçin.

   ![Tümleştirme hesabınızı seçmek için nereye kaydedileceğini gösteren ekran görüntüsü.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Tümleştirme hesabınız başarıyla bağlandıktan sonra Azure bir onay iletisi gösterir.

   ![Azure başarılı bağlantıyı onaylar](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Artık mantıksal uygulamanız tümleştirme hesabınızdaki yapıtları ve XML doğrulaması ile düz dosya kodlama veya kod çözme gibi B2B bağlayıcılarını kullanabilir.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Fiyatlandırma katmanını değiştirme

Bir tümleştirme hesabının [sınırlarını](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) artırmak için, varsa [daha yüksek bir fiyatlandırma katmanına yükseltebilirsiniz](#upgrade-pricing-tier). Örneğin, ücretsiz katmandan temel katmana veya Standart katmana yükseltebilirsiniz. Ayrıca, varsa [daha düşük bir katmana](#downgrade-pricing-tier)indirebilirsiniz. Daha fazla bilgi için fiyatlandırma bilgileri için aşağıdaki konulara bakın:

* [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Fiyatlandırma katmanını yükselt

Bu değişikliği yapmak için Azure portal ya da Azure CLı kullanabilirsiniz.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

1. Ana Azure Arama kutusuna filtreniz olarak "tümleştirme hesapları" girin ve **tümleştirme hesapları**' nı seçin.

   ![Tümleştirme hesabını bul](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure, Azure aboneliklerinizdeki tüm tümleştirme hesaplarını gösterir.

1. **Tümleştirme hesapları** altında, taşımak istediğiniz tümleştirme hesabını seçin. Tümleştirme hesabınız menüsünde **genel bakış**' ı seçin.

   ![Tümleştirme hesabı menüsünde "genel bakış" ı seçin](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Genel Bakış bölmesinde, kullanılabilir daha fazla katmanı listeleyen **fiyatlandırma katmanını Yükselt**' i seçin. Bir katman seçtiğinizde, değişiklik hemen geçerli olur.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Şimdiye kadar yapmadıysanız, [Azure CLI önkoşullarını yükleyebilirsiniz](/cli/azure/get-started-with-azure-cli).

1. Azure portal, [Azure Cloud Shell](../cloud-shell/overview.md) ortamını açın.

   ![Azure Cloud Shell’i açma](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Komut isteminde, [ **az Resource** komutunu](/cli/azure/resource#az_resource_update)girin ve istediğiniz `skuName` daha yüksek katmana ayarlayın.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Örneğin, temel katmanı varsa şunları yapabilirsiniz `skuName` `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Fiyatlandırma katmanını düşürme

Bu değişikliği yapmak için [Azure CLI](/cli/azure/get-started-with-azure-cli)'yi kullanın.

1. Şimdiye kadar yapmadıysanız, [Azure CLI önkoşullarını yükleyebilirsiniz](/cli/azure/get-started-with-azure-cli).

1. Azure portal, [Azure Cloud Shell](../cloud-shell/overview.md) ortamını açın.

   ![Azure Cloud Shell’i açma](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Komut isteminde, [ **az Resource** komutunu](/cli/azure/resource#az_resource_update) girin ve istediğiniz `skuName` alt katmana ayarlayın.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Örneğin, standart katmanı varsa şunları yapabilirsiniz `skuName` `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Mantıksal uygulama bağlantısını kaldırma

Mantıksal uygulamanızı başka bir tümleştirme hesabına bağlamak veya artık mantıksal uygulamanızla tümleştirme hesabı kullanmak istiyorsanız, Azure Kaynak Gezgini kullanarak bağlantıyı silin.

1. Tarayıcı pencerenizi açın ve [Azure Kaynak Gezgini gidin ( https://resources.azure.com) ](https://resources.azure.com). Aynı Azure hesabı kimlik bilgileriyle oturum açın.

   ![Azure Resource Manager](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Mantıksal uygulamanızı bulup seçebilmeniz için arama kutusuna mantıksal uygulamanızın adını girin.

   ![Mantıksal uygulama bul ve Seç](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Gezgin başlık çubuğunda **oku/yaz**' ı seçin.

   !["Okuma/yazma" modunu aç](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. **Veri** sekmesinde **Düzenle**' yi seçin.

   !["Veri" sekmesinde "Düzenle" yi seçin](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Düzenleyicide, `integrationAccount` nesnesini bulun ve bu biçime sahip olan özelliği silin:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Örnek:

   !["Integrationaccount" nesnesini bul](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Değişikliklerinizi kaydetmek için **veri** sekmesinde **Yerleştir** ' i seçin.

   ![Değişiklikleri kaydetmek için "yerleştir" i seçin](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Azure portal mantıksal uygulamanızı bulun ve seçin. Uygulamanızın **Iş akışı ayarları** altında, **tümleştirme hesabı** özelliğinin artık boş göründüğünden emin olun.

   ![Tümleştirme hesabının bağlı olmadığından emin olun](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Tümleştirme hesabını taşıma

Tümleştirme hesabınızı başka bir Azure Kaynak grubuna veya Azure aboneliğine taşıyabilirsiniz. Kaynakları taşırken Azure yeni kaynak kimlikleri oluşturur, bu nedenle yeni kimlikleri kullandığınızdan emin olun ve taşınan kaynaklarla ilişkili betikleri veya araçları güncelleştirin. Aboneliği değiştirmek istiyorsanız, var olan veya yeni bir kaynak grubu da belirtmeniz gerekir.

Bu görev için, bu bölümdeki veya [Azure CLI](/cli/azure/resource#az_resource_move)'deki adımları izleyerek Azure Portal kullanabilirsiniz.

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

1. Ana Azure Arama kutusuna filtreniz olarak "tümleştirme hesapları" girin ve **tümleştirme hesapları**' nı seçin.

   ![Tümleştirme hesabını bul](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure, Azure aboneliklerinizdeki tüm tümleştirme hesaplarını gösterir.

1. **Tümleştirme hesapları** altında, taşımak istediğiniz tümleştirme hesabını seçin. Tümleştirme hesabınız menüsünde **genel bakış**' ı seçin.

   ![Tümleştirme hesabı menüsünde "genel bakış" ı seçin](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. **Kaynak grubu** veya **abonelik adı**' nın yanındaki **Değiştir**' i seçin.

   ![Kaynak grubunu veya aboneliği Değiştir](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Taşımak istediğiniz ilgili kaynakları seçin.

1. Seçiminize bağlı olarak, kaynak grubunu veya aboneliği değiştirmek için şu adımları izleyin:

   * Kaynak grubu: **kaynak grubu** listesinden hedef kaynak grubunu seçin. Ya da farklı bir kaynak grubu oluşturmak için **Yeni kaynak grubu oluştur**' u seçin.

   * Abonelik: **abonelik** listesinden hedef aboneliği seçin. **Kaynak grubu** listesinden hedef kaynak grubunu seçin. Ya da farklı bir kaynak grubu oluşturmak için **Yeni kaynak grubu oluştur**' u seçin.

1. Yeni kaynak kimlikleri ile güncelleştirene kadar taşınan kaynaklarla ilişkili betiklerin veya araçların çalışmadığına ilişkin emin olmak için onay kutusunu seçin ve ardından **Tamam**' ı seçin.

1. Bitirdikten sonra, taşınan kaynaklarınızın yeni kaynak kimlikleriyle birlikte tüm komut dosyalarını ve tüm betikleri güncelleştirdiğinizden emin olun.  

## <a name="delete-integration-account"></a>Tümleştirme hesabını silme

Bu görev için, bu bölümdeki adımları izleyerek Azure portal kullanabilirsiniz, [Azure CLI](/cli/azure/resource#az_resource_delete)veya [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

1. Ana Azure Arama kutusuna filtreniz olarak "tümleştirme hesapları" girin ve **tümleştirme hesapları**' nı seçin.

   ![Tümleştirme hesabını bul](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure, Azure aboneliklerinizdeki tüm tümleştirme hesaplarını gösterir.

1. **Tümleştirme hesapları** altında, silmek istediğiniz tümleştirme hesabını seçin. Tümleştirme hesabınız menüsünde **genel bakış**' ı seçin.

   ![Tümleştirme hesabı menüsünde "genel bakış" ı seçin](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Genel Bakış bölmesinde **Sil**' i seçin.

   !["Genel bakış" bölmesinde "Sil" i seçin](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Tümleştirme hesabınızı silmek istediğinizi onaylamak için **Evet**' i seçin.

   ![Silmeyi onaylamak için "Evet" i seçin](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Tümleştirme hesabınızda ticari iş ortakları oluşturma](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Tümleştirme hesabınızdaki iş ortakları arasında anlaşma oluşturma](../logic-apps/logic-apps-enterprise-integration-agreements.md)
