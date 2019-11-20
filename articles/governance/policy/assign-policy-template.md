---
title: Kaynak Yöneticisi şablonuyla atama oluşturma
description: Bu makale, uyumlu olmayan kaynakları belirlemek üzere bir ilke ataması oluşturmak için Kaynak Yöneticisi şablonu kullanma adımlarında size yol gösterir.
ms.date: 03/13/2019
ms.topic: quickstart
ms.openlocfilehash: 0695f0b5993f996ff35296be609d9978f57907f1
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960141"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Hızlı başlangıç: Kaynak Yöneticisi şablonu kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir.
Bu hızlı başlangıç, yönetilen disk kullanmayan sanal makineleri belirlemek üzere ilke ataması oluşturma işleminde size yol gösterir.

Bu işlemin sonunda, yönetilen disk kullanmayan sanal makineleri başarılı bir şekilde belirlemiş olacaksınız. Bu sanal makineler, ilke ataması ile *uyumsuzdur*.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, bir ilke ataması oluşturup *yönetilen diskleri kullanmayan VM 'Leri denetle*adlı yerleşik bir ilke tanımı atayabilirsiniz. Kullanılabilir yerleşik ilkelerin kısmi listesi için bkz. [Azure ilke örnekleri](./samples/index.md).

İlke atamaları oluşturmak için çeşitli yöntemler vardır. Bu hızlı başlangıçta bir [hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)kullanırsınız.
Şablonun bir kopyası aşağıda verilmiştir:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Ilke hizmeti ücretsizdir.  Daha fazla bilgi için bkz. [Azure Ilkesine genel bakış](./overview.md).

1. Azure portal oturum açmak ve şablonu açmak için aşağıdaki görüntüyü seçin:

   [![Ilke şablonunu Azure 'a dağıtma](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Aşağıdaki değerleri seçin veya girin:

   | Ad | Değer |
   |------|-------|
   | Abonelik | Azure aboneliğinizi seçin. |
   | Kaynak grubu | **Yeni oluştur**' u seçin, bir ad belirtin ve ardından **Tamam**' ı seçin. Ekran görüntüsünde, kaynak grubu adı, *MMDD > RG 'Deki tarih\<* . |
   | Konum | Bir bölge seçin. Örneğin, **Orta ABD**. |
   | İlke atama adı | Bir ilke atama adı belirtin. İsterseniz ilke tanımı ekranını kullanabilirsiniz. Örneğin, **yönetilen disk kullanmayan VM 'Leri denetleyin**. |
   | RG adı | İlkeyi atamak istediğiniz kaynak grubu adını belirtin. Bu hızlı başlangıçta **[resourceGroup (). Name]** varsayılan değerini kullanın. **[resourceGroup ()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** , kaynak grubunu alan bir şablon işlevidir. |
   | İlke tanımı KIMLIĞI | **/Providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-B460-a2d36003525a**belirtin. |
   | Yukarıda belirtilen hüküm ve koşulları kabul ediyorum | Seçin |

1. **Satın al**'ı seçin.

Bazı ek kaynaklar:

- Daha fazla örnek şablonu bulmak için bkz. [Azure hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Şablon başvurusunu görmek için [Azure şablon başvurusu](/azure/templates/microsoft.authorization/allversions)' na gidin.
- Kaynak Yöneticisi şablonlarının nasıl geliştirileceği hakkında bilgi edinmek için bkz. [Azure Resource Manager belgeleri](/azure/azure-resource-manager/).
- Abonelik düzeyinde dağıtım hakkında bilgi edinmek için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

Seçin **Uyumluluk** sayfanın sol tarafındaki. Ardından bulun **denetim yönetilen diskleri kullanmayan Vm'leri** oluşturduğunuz ilke ataması.

![İlke uyumluluğuna genel bakış sayfası](./media/assign-policy-template/policy-compliance.png)

Bu yeni atamayla uyumlu olmayan mevcut kaynaklar varsa, altında görünür **uyumlu olmayan kaynaklar**.

Daha fazla bilgi için bkz. [Uyumluluk nasıl çalıştığını öğrenin](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz atamayı kaldırmak için aşağıdaki adımları izleyin:

1. Azure İlkesi sayfasının sol tarafından **Uyumluluk**’u (veya **Atamalar**’ı) seçin ve oluşturduğunuz **Yönetilen disk kullanmayan VM'leri denetle** ilke atamasını bulun.

1. Yönetilen diskler ilkesi atamasını **kullanmayan VM 'Leri denetle** ' ye sağ tıklayın ve **atamayı Sil**' i seçin.

   ![Uyumluluk genel bakış sayfasından bir atamayı silme](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir kapsama yerleşik bir ilke tanımı atamış ve uyumluluk raporunu değerlendirdiniz. İlke tanımı, kapsamdaki tüm kaynakların uyumlu olan ve olmayanları tanımlayan doğrular.

Yeni kaynakların uyumlu olduğunu doğrulamak için ilkeleri atama hakkında daha fazla bilgi için öğreticisiyle devam edin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)