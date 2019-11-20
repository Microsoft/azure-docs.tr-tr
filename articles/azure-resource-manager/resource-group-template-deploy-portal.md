---
title: Azure portal ile kaynak dağıtma
description: Kaynaklarınızı aboneliğinizdeki bir kaynak grubuna dağıtmak için Azure portal ve Azure Kaynak yönetme 'yi kullanın.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 6a8842a9fbda11bf48d45152c167d7b072f38119
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150762"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Kaynakları Resource Manager şablonları ve Azure portalı ile dağıtma

Azure kaynaklarınızı dağıtmak için Azure Resource Manager [Azure Portal](https://portal.azure.com) nasıl [](resource-group-overview.md) kullanacağınızı öğrenin. Kaynaklarınızı yönetme hakkında bilgi edinmek için, bkz. [Azure Portal kullanarak Azure kaynaklarını yönetme](manage-resources-portal.md).

Azure portal kullanarak Azure kaynaklarını dağıtmak, genellikle iki adımdan oluşur:

- Bir kaynak grubu oluşturun.
- Kaynakları kaynak grubuna dağıtın.

Ayrıca, Azure kaynakları oluşturmak için bir Azure Resource Manager şablonu da dağıtabilirsiniz.

Bu makalede her iki yöntem de gösterilmektedir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

1. Yeni bir kaynak grubu oluşturmak için [Azure Portal](https://portal.azure.com) **kaynak grupları** ' nı seçin.

   ![Kaynak gruplarını seçin](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Kaynak grupları altında **Ekle**' yi seçin.

   ![Kaynak Grubu Ekle](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Aşağıdaki özellik değerlerini seçin veya girin:

    - **Abonelik**: Bir Azure aboneliği seçin.
    - **Kaynak grubu**: kaynak grubuna bir ad verin.
    - **Bölge**: bir Azure konumu belirtin. Burada kaynak grubu kaynaklarla ilgili meta verileri depolar. Uyumluluk nedenleriyle, meta verilerin depolanacağı yeri belirtmek isteyebilirsiniz. Genel olarak, kaynaklarınızın çoğunun bulunacağı bir konum belirtmenizi öneririz. Aynı konumun kullanılması şablonunuzun basitleşmesini sağlayabilir.

   ![Grup değerlerini ayarla](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. **İncele ve oluştur**’u seçin.
1. değerleri gözden geçirin ve ardından **Oluştur**' u seçin.
1. Listeden yeni kaynak grubunu görebilmek için **Yenile** ' yi seçin.

## <a name="deploy-resources-to-a-resource-group"></a>Kaynakları bir kaynak grubuna dağıtma

Bir kaynak grubu oluşturduktan sonra Market 'ten gruba kaynak dağıtabilirsiniz. Market, yaygın senaryolar için önceden tanımlanmış çözümler sağlar.

1. Bir dağıtımı başlatmak için [Azure Portal](https://portal.azure.com) **kaynak oluştur** ' u seçin.

   ![Yeni kaynak](./media/resource-group-template-deploy-portal/new-resources.png)

1. Dağıtmak istediğiniz kaynak türünü bulun. Kaynaklar kategoriler halinde düzenlenir. Dağıtmak istediğiniz belirli bir çözümü görmüyorsanız Market 'Te bunu arayabilirsiniz. Aşağıdaki ekran görüntüsünde Ubuntu sunucusu seçilmiş gösterilmektedir.

   ![Kaynak türünü seçin](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Seçili kaynağın türüne bağlı olarak, dağıtımdan önce ayarlanacak ilgili özellikler koleksiyonuna sahip olursunuz. Tüm türler için bir hedef kaynak grubu seçmeniz gerekir. Aşağıdaki görüntüde, bir Linux sanal makinesinin nasıl oluşturulacağı ve oluşturduğunuz kaynak grubuna nasıl dağıtılacağı gösterilmektedir.

   ![Kaynak grubu oluşturma](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternatif olarak, kaynaklarınızı dağıttığınızda bir kaynak grubu oluşturmaya karar verebilirsiniz. **Yeni oluştur** ' u seçin ve kaynak grubuna bir ad verin.

1. Dağıtımınız başlar. Dağıtım birkaç dakika sürebilir. Bazı kaynaklar diğer kaynaklardan daha uzun zaman alır. Dağıtım bittiğinde bir bildirim görürsünüz. Açmak için **Kaynağa Git** ' i seçin

   ![Bildirimi görüntüle](./media/resource-group-template-deploy-portal/view-notification.png)

1. Kaynaklarınızı dağıttıktan sonra **Ekle**' yi seçerek kaynak grubuna daha fazla kaynak ekleyebilirsiniz.

   ![Kaynak ekle](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Kaynakları özel şablondan dağıtma

Bir dağıtımı yürütmek, ancak Market 'teki şablonlardan hiçbirini kullanmak istemiyorsanız, çözümünüz için altyapıyı tanımlayan özelleştirilmiş bir şablon oluşturabilirsiniz. Şablon oluşturma hakkında bilgi edinmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).

> [!NOTE]
> Portal arabirimi [Key Vault bir gizli](resource-manager-keyvault-parameter.md)diziyi başvurmayı desteklemez. Bunun yerine, yerel olarak veya harici bir URI 'den şablonunuzu dağıtmak için [PowerShell](resource-group-template-deploy.md) veya [Azure CLI](resource-group-template-deploy-cli.md) kullanın.

1. Portal üzerinden özelleştirilmiş bir şablon dağıtmak için **kaynak oluştur**' u seçin, **şablon**araması yapın. sonra **şablon dağıtımı**' yi seçin.

   ![Arama şablonu dağıtımı](./media/resource-group-template-deploy-portal/search-template.png)

1. **Oluştur**'u seçin.
1. Şablon oluşturmak için çeşitli seçenekler görürsünüz:

    - **Düzenleyicide kendi şablonunuzu**oluşturun: Portal şablonu düzenleyicisini kullanarak şablon oluşturma.  Düzenleyici, bir kaynak şablonu şeması ekleyebilir.
    - **Ortak şablonlar**: bir Linux sanal makinesi, Windows sanal makinesi, bir Web uygulaması ve bır Azure SQL veritabanı oluşturmak için dört ortak şablon vardır.
    - **GitHub hızlı başlangıç şablonu yükleme**: mevcut bir [Hızlı Başlangıç şablonlarını](https://azure.microsoft.com/resources/templates/)kullanın.

   ![Görüntüleme seçenekleri](./media/resource-group-template-deploy-portal/see-options.png)

    Bu öğretici, bir hızlı başlangıç şablonu yükleme yönergesini sağlar.

1. **GitHub hızlı başlangıç şablonu yükle**altında, **101-Storage-Account-Create**yazın veya seçin.

    İki seçeneğiniz vardır:

    - **Şablon seçin**: şablonu dağıtın.
    - **Şablonu Düzenle**: dağıtmadan önce hızlı başlangıç şablonunu düzenleyin.

1. Portal şablonu düzenleyicisini araştırmak için **Şablonu Düzenle** ' yi seçin. Şablon düzenleyiciye yüklenir. İki parametre olduğuna dikkat edin: **Storageaccounttype** ve **Location**.

   ![Şablon oluşturma](./media/resource-group-template-deploy-portal/show-json.png)

1. Şablonda küçük bir değişiklik yapın. Örneğin, **storageAccountName** değişkenini şu şekilde güncelleştirin:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. **Kaydet**’i seçin. Artık portal şablonu dağıtım arabirimini görürsünüz. Şablonda tanımladığınız iki parametreye dikkat edin.
1. Özellik değerlerini girin veya seçin:

    - **Abonelik**: Bir Azure aboneliği seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve bir ad verin.
    - **Konum**: bir Azure konumu seçin.
    - **Depolama hesabı türü**: varsayılan değeri kullanın.
    - **Konum**: varsayılan değeri kullanın.
    - **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**: (seçin)

1. **Satın al**'ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Denetim günlüklerini görüntülemek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](./resource-group-audit.md).
- Dağıtım hatalarını gidermek için bkz. [dağıtım Işlemlerini görüntüleme](./resource-manager-deployment-operations.md).
- Bir dağıtımı veya kaynak grubundan bir şablonu dışarı aktarmak için bkz. [Azure Resource Manager şablonlarını dışarı aktarma](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Hizmetinizi birden çok bölgede güvenle kullanıma almak için bkz. [Azure dağıtım Yöneticisi](./deployment-manager-overview.md).
