---
title: 'Hızlı başlangıç: Otomasyon hesabı oluşturma-Azure şablonu'
titleSuffix: Azure Automation
description: Bu hızlı başlangıçta, Azure Resource Manager şablonu kullanılarak bir Otomasyon hesabının nasıl oluşturulacağı gösterilmektedir.
services: automation
ms.author: magoedte
ms.date: 01/07/2021
ms.topic: quickstart
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: 50a8c057a6fdf6350a18225cd2dc538942d6686d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538400"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Otomasyon hesabı oluşturma

Azure Otomasyonu, Azure ve Azure dışı ortamlarınızda tutarlı Yönetimi destekleyen bulut tabanlı bir Otomasyon ve yapılandırma hizmeti sunar. Bu hızlı başlangıçta, bir Otomasyon hesabı oluşturan Azure Resource Manager şablonun (ARM şablonu) nasıl dağıtılacağı gösterilmektedir. ARM şablonunun kullanılması, diğer dağıtım yöntemlerine kıyasla daha az adım sürer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu örnek şablon şunları gerçekleştirir:

* Bir Azure Izleyici Log Analytics çalışma alanı oluşturulmasını otomatikleştirir.
* Bir Azure Otomasyonu hesabının oluşturulmasını otomatikleştirir.
* Otomasyon hesabını Log Analytics çalışma alanına bağlar.
* Hesaba örnek Automation runbook 'ları ekler.

>[!NOTE]
>Bir ARM şablonu kullanırken Otomasyon farklı çalıştır hesabının oluşturulması desteklenmez. Portaldan veya PowerShell ile el ile bir farklı çalıştır hesabı oluşturmak için bkz. [Farklı Çalıştır hesabı oluşturma](create-run-as-account.md).

Bu adımları tamamladıktan sonra, bağlı Log Analytics çalışma alanına runbook iş durumu ve iş akışları göndermek üzere otomasyon hesabınız için [tanılama ayarlarını yapılandırmanız](automation-manage-send-joblogs-log-analytics.md) gerekir.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-automation/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API sürümleri

Aşağıdaki tabloda, bu örnekte kullanılan kaynakların API sürümü listelenmektedir.

| Kaynak | Kaynak türü | API sürümü |
|:---|:---|:---|
| [Çalışma alanı](/azure/templates/microsoft.operationalinsights/workspaces) | çalışma alanı | 2020-03-01-Önizleme |
| [Otomasyon hesabı](/azure/templates/microsoft.automation/automationaccounts) | otomasyon | 2020-01-13-Önizleme |
| [Çalışma alanı bağlı hizmetleri](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | çalışma alanı | 2020-03-01-Önizleme |

### <a name="before-you-use-the-template"></a>Şablonu kullanmadan önce

JSON parametreleri şablonu şunları belirtmeniz için yapılandırılır:

* Çalışma alanının adı.
* Çalışma alanının oluşturulacağı bölge.
* Otomasyon hesabının adı.
* Otomasyon hesabının oluşturulacağı bölge.

Şablondaki aşağıdaki parametreler, Log Analytics çalışma alanı için varsayılan bir değerle ayarlanır:

* *SKU* , Nisan 2018 fiyatlandırma MODELINDE yayınlanan GB başına fiyatlandırma katmanını varsayılan olarak alır.
* *Dataretention* varsayılan değer 30 gündür.

>[!WARNING]
>Nisan 2018 fiyatlandırma modelini kabul eden bir abonelikte Log Analytics çalışma alanı oluşturmak veya yapılandırmak istiyorsanız, geçerli Log Analytics fiyatlandırma katmanı yalnızca *PerGB2018* olur.
>

JSON şablonu, ortamınızda standart bir yapılandırma olarak kullanılacak diğer parametreler için varsayılan bir değer belirtir. Şablonu kuruluşunuzda paylaşılan erişim için bir Azure depolama hesabında saklayabilirsiniz. Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [ARM şablonları ve Azure CLI ile kaynak dağıtma](../azure-resource-manager/templates/deploy-cli.md).

Azure Otomasyonu ve Azure Izleyici 'de yeni başladıysanız aşağıdaki yapılandırma ayrıntılarını anlamanız önemlidir. Yeni otomasyon hesabınıza bağlı bir Log Analytics çalışma alanı oluşturmaya, yapılandırmaya ve kullanmaya çalıştığınızda hatalardan kaçınmanıza yardımcı olabilirler.

* Erişim denetimi modu, fiyatlandırma katmanı, bekletme ve kapasite ayırma düzeyi gibi çalışma alanı yapılandırma seçeneklerini tam olarak anlamak için [ek ayrıntıları](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) gözden geçirin.

* Desteklenen bölgeleri satır içi veya bir parametre dosyasında belirtmek için [çalışma alanı eşlemelerini](how-to/region-mappings.md) gözden geçirin. Log Analytics çalışma alanını ve aboneliğinizdeki Otomasyon hesabını bağlamak için yalnızca belirli bölgeler desteklenir.

* Azure Izleyici günlüklerine yeni başladıysanız ve bir çalışma alanını henüz dağıtmadıysanız, [çalışma alanı tasarım kılavuzunu](../azure-monitor/logs/design-logs-deployment.md)gözden geçirmeniz gerekir. Erişim denetimi hakkında bilgi edinmenize ve kuruluşunuz için önerdiğimiz tasarım uygulama stratejilerini anlamanıza yardımcı olur.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir Azure Otomasyonu hesabı, bir Log Analytics çalışma alanı oluşturur ve otomasyon hesabını çalışma alanına bağlar.

    [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Değerleri girin.

3. Dağıtımın tamamlanması birkaç dakika sürebilir. Tamamlandığında, çıktı aşağıdakine benzer:

    ![Dağıtım tamamlandığında örnek sonuç](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portal, az önce oluşturduğunuz Otomasyon hesabını açın. 

3. Sol bölmeden **runbook 'lar**' ı seçin. **Runbook 'lar** sayfasında, Otomasyon hesabıyla oluşturulan üç öğretici runbook 'u listelenir.

    ![Otomasyon hesabıyla oluşturulan öğretici runbook 'lar](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Sol bölmeden **bağlantılı çalışma alanı**' nı seçin. **Bağlantılı çalışma alanı** sayfasında, daha önce Otomasyon hesabınıza bağlı olarak belirttiğiniz Log Analytics çalışma alanını gösterir.

    ![Log Analytics çalışma alanına bağlı Otomasyon hesabı](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında Otomasyon hesabının Log Analytics çalışma alanından bağlantısını kaldırın ve ardından Otomasyon hesabını ve çalışma alanını silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Otomasyon hesabı, bir Log Analytics çalışma alanı oluşturdunuz ve bunları birbirine bağladınız.

Daha fazla bilgi edinmek için Azure Otomasyonu öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Otomasyonu öğreticileri](learn/automation-tutorial-runbook-graphical.md)
