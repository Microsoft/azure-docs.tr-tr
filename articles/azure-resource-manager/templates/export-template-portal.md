---
title: Azure portal şablonu dışarı aktarma
description: Aboneliğinizdeki kaynaklardan bir Azure Resource Manager şablonu dışarı aktarmak için Azure portal kullanın.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 3cc790b67b6076236a550c1fa202e0d173fb360e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731947"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Azure portal bir şablona tek ve çoklu kaynak verme

Azure Resource Manager şablonları oluşturmaya yardımcı olmak için, var olan kaynaklardan bir şablonu dışarı aktarabilirsiniz. Verdiğiniz şablon, JSON sözdizimini ve kaynaklarınızı dağıtan özellikleri anlamanıza yardımcı olur. Gelecekteki dağıtımları otomatik hale getirmek için, verdiğiniz şablonla başlayın ve senaryonuz için değiştirin.

Kaynak Yöneticisi bir şablona dışarı aktarmak için bir veya daha fazla kaynak seçmenizi sağlar. Şablonda ihtiyacınız olan kaynaklara tam olarak odaklanırsınız.

Bu makalede, Portal üzerinden şablonların nasıl dışarı aktarılacağı gösterilmektedir. [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)veya [REST API](/rest/api/resources/resources/resourcegroups/exporttemplate)de kullanabilirsiniz.

## <a name="choose-the-right-export-option"></a>Doğru dışa aktarma seçeneğini belirleyin

Şablonu dışarı aktarmanın iki yolu vardır:

* **Kaynak grubundan veya kaynaktan dışarı aktarın**. Bu seçenek, mevcut kaynaklardan yeni bir şablon oluşturur. Bu şablon, kaynak grubunun geçerli durumunun "Snapshot" ' dır. Kaynak grubunun tamamını veya kaynak grubu içindeki belirli kaynakları dışarı aktarabilirsiniz.

* **Dağıtımdan veya geçmişten önce dışarı aktarın**. Bu seçenek, dağıtım için kullanılan bir şablonun tam bir kopyasını alır.

Seçtiğiniz seçeneğe bağlı olarak, içe aktarılmış şablonların farklı nitelikleri vardır.

| Kaynak grubu veya kaynaktan | Dağıtımdan veya geçmişten önce |
| --------------------- | ----------------- |
| Şablon, kaynakların geçerli durumunun anlık görüntüsüdür. Dağıtımdan sonra yaptığınız el ile yapılan tüm değişiklikleri içerir. | Şablon, dağıtım sırasında yalnızca kaynakların durumunu gösterir. Dağıtımdan sonra yaptığınız tüm el ile değişiklikler dahil edilmez. |
| Bir kaynak grubundan dışarı aktarılacak kaynakları seçebilirsiniz. | Belirli bir dağıtımın tüm kaynakları dahil edilmiştir. Bu kaynakların bir alt kümesini seçemezsiniz veya farklı bir zamanda eklenen kaynaklar ekleyebilirsiniz. |
| Şablon, genellikle dağıtım sırasında ayarladığınız bazı özellikler dahil olmak üzere, kaynakların tüm özelliklerini içerir. Şablonu yeniden kullanmadan önce bu özellikleri kaldırmak veya temizlemek isteyebilirsiniz. | Şablon yalnızca dağıtım için gereken özellikleri içerir. Şablon kullanıma hazırdır. |
| Şablon muhtemelen yeniden kullanım için gereken tüm parametreleri içermez. Çoğu özellik değeri, şablonda sabit olarak kodlanır. Diğer ortamlarda şablonu yeniden dağıtmak için, kaynakları yapılandırma özelliğini artıran parametreler eklemeniz gerekir.  **Parametreleri içer** ' i seçimden, kendi parametrelerinizi yazabileceğiniz şekilde seçebilirsiniz. | Şablon, farklı ortamlarda yeniden dağıtmayı kolaylaştıran parametreler içerir. |

Şablonu bir kaynak grubu veya kaynağından dışarı aktarın, ne zaman:

* Başlangıçtaki dağıtımdan sonra yapılan kaynaklardaki değişiklikleri yakalamanız gerekir.
* Hangi kaynakların verildiğini seçmek istiyorsunuz.

Şablonu dağıtımdan önce veya geçmişten önce dışarı aktarın, ne zaman:

* Kullanımı kolay bir şablon istiyorsunuz.
* Özgün dağıtımdan sonra yaptığınız değişiklikleri eklemeniz gerekmez.

## <a name="limitations"></a>Sınırlamalar

Bir kaynak grubu veya kaynaktan dışarı aktarırken, dışarı aktarılan şablon her kaynak türü için [yayımlanan şemalardan](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) oluşturulur. Bazen, şema bir kaynak türü için en son sürüme sahip değildir. İhtiyacınız olan özellikleri içerdiğinden emin olmak için, verdiğiniz şablonu kontrol edin. Gerekirse, sizin için gerekli olan API sürümünü kullanmak üzere, verdiğiniz şablonu düzenleyin.

Şablonu dışarı aktar özelliği Azure Data Factory kaynaklarının dışarı aktarılmasını desteklemez. Data Factory kaynaklarını nasıl dışarı aktarabilirsiniz hakkında bilgi edinmek için bkz. [Azure Data Factory veri fabrikasını kopyalama veya](../../data-factory/copy-clone-data-factory.md)kopyalama.

Klasik dağıtım modeliyle oluşturulan kaynakları dışarı aktarmak için, [bunları Kaynak Yöneticisi dağıtım modeline geçirmeniz](../../virtual-machines/migration-classic-resource-manager-overview.md)gerekir.

Bir kaynak türünün dışarı aktarılmadığını belirten bir şablonu dışarı aktarırken uyarı alırsanız, bu kaynağın özelliklerini yine de bulabilirsiniz. Kaynak özelliklerini görüntülemeye yönelik farklı seçenekler hakkında bilgi edinmek için bkz. [keşif kaynağı özellikleri](view-resources.md). Kaynak türü için [Azure REST API](/rest/api/azure/) de bakabilirsiniz.

Kaynak grubunda, için aktarılmış şablonu oluşturduğunuz 200 kaynağın bir sınırı vardır. 200 ' den fazla kaynağa sahip bir kaynak grubunu dışarı aktarmaya çalışırsanız, hata iletisi `Export template is not supported for resource groups more than 200 resources` gösterilir.

## <a name="export-template-from-a-resource-group"></a>Kaynak grubundan şablonu dışarı aktarma

Bir kaynak grubundan bir veya daha fazla kaynağı dışarı aktarmak için:

1. Dışarı aktarmak istediğiniz kaynakları içeren kaynak grubunu seçin.

1. Onay kutularını seçerek bir veya daha fazla kaynak seçin.  Tümünü seçmek için, **adının** solundaki onay kutusunu seçin. **Şablonu dışarı aktar** menü öğesi yalnızca en az bir kaynak seçildikten sonra etkin hale gelir.

   ![Tüm kaynakları dışarı aktar](./media/export-template-portal/select-all-resources.png)

    Ekran görüntüsünde yalnızca depolama hesabı seçilir.
1. **Şablonu dışarı aktar**' ı seçin.

1. İçe aktarılmış şablon görüntülenir ve indirilebilir ve dağıtılabilir.

   ![Şablonu göster](./media/export-template-portal/show-template.png)

   **Include parametreleri** varsayılan olarak seçilidir.  Seçildiğinde, şablon oluşturulduğunda tüm şablon parametreleri dahil edilir. Kendi parametrelerinizi yazmak isterseniz, bu onay kutusunu dahil etmez.

## <a name="export-template-from-a-resource"></a>Kaynak şablonunu dışarı aktarma

Bir kaynağı dışarı aktarmak için:

1. Dışarı aktarmak istediğiniz kaynağı içeren kaynak grubunu seçin.

1. Kaynağı açmak için dışarı aktarmak istediğiniz kaynağı seçin.

1. Bu kaynak için sol bölmedeki **şablonu dışarı aktar** ' ı seçin.

   ![Kaynağı dışarı aktar](./media/export-template-portal/export-single-resource.png)

1. İçe aktarılmış şablon görüntülenir ve indirilebilir ve dağıtılabilir. Şablon yalnızca tek bir kaynağı içerir. **Include parametreleri** varsayılan olarak seçilidir.  Seçildiğinde, şablon oluşturulduğunda tüm şablon parametreleri dahil edilir. Kendi parametrelerinizi yazmak isterseniz, bu onay kutusunu dahil etmez.

## <a name="export-template-before-deployment"></a>Dağıtımdan önce şablonu dışarı aktar

1. Dağıtmak istediğiniz Azure hizmetini seçin.

1. Yeni hizmet için değerleri girin.

1. Doğrulamayı geçirdikten sonra, ancak dağıtımı başlatmadan önce **Otomasyon için bir şablon indir**' i seçin.

   ![Şablon indirme](./media/export-template-portal/download-before-deployment.png)

1. Şablon görüntülenir ve indirileceği ve dağıtım için kullanılabilir.


## <a name="export-template-after-deployment"></a>Dağıtımdan sonra şablonu dışarı aktar

Mevcut kaynakları dağıtmak için kullanılan şablonu dışarı aktarabilirsiniz. Aldığınız şablon tam olarak dağıtım için kullanılan bir şablondur.

1. Dışarı aktarmak istediğiniz kaynak grubunu seçin.

1. **Dağıtımlar** altındaki bağlantıyı seçin.

   ![Dağıtım geçmişini seçin](./media/export-template-portal/select-deployment-history.png)

1. Dağıtım geçmişinden dağıtımlardan birini seçin.

   ![Dağıtım seçin](./media/export-template-portal/select-details.png)

1. **Şablon** seçin. Bu dağıtım için kullanılan şablon görüntülenir ve indirilmek üzere kullanılabilir.

   ![Şablon seçme](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)veya [REST API](/rest/api/resources/resources/resourcegroups/exporttemplate)ile şablonları dışarı aktarmayı öğrenin.
- Kaynak Yöneticisi Şablon sözdizimini öğrenmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
- Şablon geliştirmeyi öğrenmek için [adım adım öğreticiler](../index.yml)bölümüne bakın.
- Azure Resource Manager şablonu şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).