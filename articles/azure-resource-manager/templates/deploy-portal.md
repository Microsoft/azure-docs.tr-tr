---
title: Azure portal ile kaynak dağıtma
description: Kaynaklarınızı aboneliğinizdeki bir kaynak grubuna dağıtmak için Azure portal ve Azure Kaynak yönetme 'yi kullanın.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d8467bb4e51fc4e6ba89a84f1260a8d2743758d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028684"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>ARM şablonları ve Azure portal kaynak dağıtma

Azure kaynaklarınızı dağıtmak için [Azure Resource Manager şablonları (ARM şablonları)](overview.md) ile [Azure Portal](https://portal.azure.com) nasıl kullanacağınızı öğrenin. Kaynaklarınızı yönetme hakkında bilgi edinmek için, bkz. [Azure Portal kullanarak Azure kaynaklarını yönetme](../management/manage-resources-portal.md).

Azure portal kullanarak Azure kaynaklarını dağıtmak, genellikle iki adımdan oluşur:

- Bir kaynak grubu oluşturun.
- Kaynakları kaynak grubuna dağıtın.

Ayrıca, Azure kaynaklarını dağıtmak için özelleştirilmiş bir ARM şablonu oluşturabilirsiniz.

Bu makalede her iki yöntem de gösterilmektedir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

1. Yeni bir kaynak grubu oluşturmak için [Azure Portal](https://portal.azure.com) **kaynak grupları** ' nı seçin.

   ![Kaynak gruplarını seçin](./media/deploy-portal/select-resource-groups.png)

1. Kaynak grupları altında **Ekle**' yi seçin.

   ![Kaynak Grubu Ekle](./media/deploy-portal/add-resource-group.png)

1. Aşağıdaki özellik değerlerini seçin veya girin:

    - **Abonelik**: bir Azure aboneliği seçin.
    - **Kaynak grubu**: kaynak grubuna bir ad verin.
    - **Bölge**: bir Azure konumu belirtin. Bu konum, kaynak grubunun kaynaklarla ilgili meta verileri depoladığı yerdir. Uyumluluk nedenleriyle, meta verilerin depolanacağı yeri belirtmek isteyebilirsiniz. Genel olarak, kaynaklarınızın çoğunun nerede olacağı bir konum belirtmenizi öneririz. Aynı konumun kullanılması şablonunuzun basitleşmesini sağlayabilir.

   ![Grup değerlerini ayarla](./media/deploy-portal/set-group-properties.png)

1. **Gözden geçir ve oluştur**’u seçin.
1. Değerleri gözden geçirin ve ardından **Oluştur**' u seçin.
1. Listeden yeni kaynak grubunu görebilmek için **Yenile** ' yi seçin.

## <a name="deploy-resources-to-a-resource-group"></a>Kaynakları bir kaynak grubuna dağıtma

Bir kaynak grubu oluşturduktan sonra Market 'ten gruba kaynak dağıtabilirsiniz. Market, yaygın senaryolar için önceden tanımlanmış çözümler sağlar.

1. Bir dağıtımı başlatmak için [Azure Portal](https://portal.azure.com) **kaynak oluştur** ' u seçin.

   ![Yeni kaynak](./media/deploy-portal/new-resources.png)

1. Dağıtmak istediğiniz kaynak türünü bulun. Kaynaklar kategoriler halinde düzenlenir. Dağıtmak istediğiniz belirli bir çözümü görmüyorsanız Market 'Te bunu arayabilirsiniz. Aşağıdaki ekran görüntüsünde Ubuntu sunucusu seçilmiş gösterilmektedir.

   ![Kaynak türünü seçin](./media/deploy-portal/select-resource-type.png)

1. Seçili kaynağın türüne bağlı olarak, dağıtımdan önce ayarlanacak ilgili özellikler koleksiyonuna sahip olursunuz. Tüm türler için bir hedef kaynak grubu seçmeniz gerekir. Aşağıdaki görüntüde, bir Linux sanal makinesinin nasıl oluşturulacağı ve oluşturduğunuz kaynak grubuna nasıl dağıtılacağı gösterilmektedir.

   ![Kaynak grubu oluşturma](./media/deploy-portal/select-existing-group.png)

   Kaynaklarınızı dağıttığınızda bir kaynak grubu oluşturmaya karar verebilirsiniz. **Yeni oluştur** ' u seçin ve kaynak grubuna bir ad verin.

1. Dağıtımınız başlar. Dağıtım birkaç dakika sürebilir. Bazı kaynaklar diğer kaynaklardan daha uzun zaman alır. Dağıtım bittiğinde bir bildirim görürsünüz. Açmak için **Kaynağa Git** ' i seçin

   ![Bildirimi görüntüle](./media/deploy-portal/view-notification.png)

1. Kaynaklarınızı dağıttıktan sonra **Ekle**' yi seçerek kaynak grubuna daha fazla kaynak ekleyebilirsiniz.

   ![Kaynak Ekle](./media/deploy-portal/add-resource.png)

Bunu görmediğiniz halde, Portal seçtiğiniz kaynakları dağıtmak için bir ARM şablonu kullandı. Şablonu dağıtım geçmişinden bulabilirsiniz. Daha fazla bilgi için bkz. [dağıtımdan sonra şablonu dışarı aktarma](export-template-portal.md#export-template-after-deployment).

## <a name="deploy-resources-from-custom-template"></a>Kaynakları özel şablondan dağıtma

Bir dağıtımı yürütmek, ancak Market 'teki şablonlardan hiçbirini kullanmak istemiyorsanız, çözümünüz için altyapıyı tanımlayan özelleştirilmiş bir şablon oluşturabilirsiniz. Şablon oluşturma hakkında bilgi edinmek için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).

> [!NOTE]
> Portal arabirimi [Key Vault bir gizli](key-vault-parameter.md)diziyi başvurmayı desteklemez. Bunun yerine, yerel olarak veya harici bir URI 'den şablonunuzu dağıtmak için [PowerShell](deploy-powershell.md) veya [Azure CLI](deploy-cli.md) kullanın.

1. Portal üzerinden özelleştirilmiş bir şablon dağıtmak için **kaynak oluştur**' u seçin, **şablon** araması yapın. sonra **şablon dağıtımı**' yi seçin.

   ![Arama şablonu dağıtımı](./media/deploy-portal/search-template.png)

1. **Oluştur**’u seçin.
1. Şablon oluşturmak için çeşitli seçenekler görürsünüz:

    - **Düzenleyicide kendi şablonunuzu** oluşturun: Portal şablonu düzenleyicisinde kendi şablonunuzu oluşturun.
    - **Ortak şablonlar**: ortak çözümler arasından seçim yapın.
    - **GitHub hızlı başlangıç şablonu yükleme**: [hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/)arasından seçim yapın.

   ![Görüntüleme seçenekleri](./media/deploy-portal/see-options.png)

    Bu öğretici, bir hızlı başlangıç şablonu yükleme yönergesini sağlar.

1. **GitHub hızlı başlangıç şablonu yükle** altında, **101-Storage-Account-Create** yazın veya seçin.

    İki seçeneğiniz vardır:

    - **Şablon seçin**: şablonu dağıtın.
    - **Şablonu Düzenle**: dağıtmadan önce hızlı başlangıç şablonunu düzenleyin.

1. Portal şablonu düzenleyicisini araştırmak için **Şablonu Düzenle** ' yi seçin. Şablon düzenleyiciye yüklenir. İki parametre olduğuna dikkat edin: `storageAccountType` ve `location` .

   ![Şablon oluşturma](./media/deploy-portal/show-json.png)

1. Şablonda küçük bir değişiklik yapın. Örneğin, `storageAccountName` değişkenini şu şekilde güncelleştirin:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. **Kaydet**’i seçin. Artık portal şablonu dağıtım arabirimini görürsünüz. Şablonda tanımladığınız iki parametreye dikkat edin.
1. Özellik değerlerini girin veya seçin:

    - **Abonelik**: bir Azure aboneliği seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve bir ad verin.
    - **Konum**: bir Azure konumu seçin.
    - **Depolama hesabı türü**: varsayılan değeri kullanın.
    - **Konum**: varsayılan değeri kullanın.
    - **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**: (seçin)

1. **Satın al**'ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Denetim günlüklerini görüntülemek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../management/view-activity-logs.md).
- Dağıtım hatalarını gidermek için bkz. [dağıtım Işlemlerini görüntüleme](deployment-history.md).
- Bir dağıtımı veya kaynak grubundan bir şablonu dışarı aktarmak için bkz. [ARM şablonlarını dışarı aktarma](export-template-portal.md).
- Hizmetinizi birden çok bölgede güvenle kullanıma almak için bkz. [Azure Deployment Manager](deployment-manager-overview.md).
