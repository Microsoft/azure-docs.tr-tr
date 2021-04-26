---
title: 'Hızlı başlangıç: şablonlarla paylaşılan bir sorgu oluşturma'
description: Bu hızlı başlangıçta, sanal makineleri işletim sistemine göre sayan bir kaynak grafiği paylaşılan sorgusu oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: ac736d4371531bb38a8cd2cf095acbdfbc7c08a1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535795"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak paylaşılan sorgu oluşturma

Kaynak Grafiği sorguları, _özel bir sorgu_ veya _paylaşılan sorgu_ olarak kaydedilebilir. Özel bir sorgu bireyler portalı profiline kaydedilir ve başkaları tarafından görülemez. Paylaşılan sorgu, izinler ve rol tabanlı erişim aracılığıyla başkalarıyla paylaşılabilecek Kaynak Yöneticisi nesnesidir. Paylaşılan sorgu, kaynak bulmanın ortak ve tutarlı bir şekilde yürütülmesini sağlar. Bu hızlı başlangıç, paylaşılan bir sorgu oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanır.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure 'da paylaşılan bir sorgu oluşturmak için ARM şablonunu dağıtma" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta, _işletim sistemi tarafından Count VM 'ler_ adlı bir paylaşılan sorgu oluşturacaksınız. Bu sorguyu SDK 'da veya kaynak Graph Explorer ile portalda denemek için bkz. [OS türlerine göre örnek sayısı sanal makineleri](./samples/starter.md#count-os).

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/) alınmıştır.

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

Şablonda tanımlanan kaynak:

- [Microsoft. ResourceGraph/sorgular](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>Şablonu dağıtma

> [!NOTE]
> Azure Kaynak Grafiği hizmeti ücretsizdir. Daha fazla bilgi için bkz. [Azure Kaynak grafiğine genel bakış](./overview.md).

1. Azure portalında oturum açıp şablonu açmak için aşağıdaki görüntüyü seçin:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure 'da paylaşılan bir sorgu oluşturmak için ARM şablonunu dağıtma" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Aşağıdaki değerleri seçin veya girin:

   | Name | Değer |
   |------|-------|
   | Abonelik | Azure aboneliğinizi seçin. |
   | Kaynak grubu | **Yeni oluştur**' u seçin, bir ad belirtin ve ardından **Tamam**' ı seçin. |
   | Konum | Bir bölge seçin. Örneğin **Orta ABD**. |
   | Sorgu adı | Varsayılan değeri bırakın: **VM 'leri işletim sistemine göre say**. |
   | Sorgu kodu | Varsayılan değeri bırakın: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Sorgu açıklaması | Varsayılan değeri bırakın: **Bu paylaşılan sorgu tüm sanal makine kaynaklarını sayar ve işletim sistemi türüne göre özetler.** |
   | Yukarıda belirtilen hüküm ve koşulları kabul ediyorum | Seçin |

1. **Satın al**'ı seçin.

Bazı ek kaynaklar:

- Daha fazla örnek şablonu bulmak için bkz. [Azure hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Şablon başvurusunu görmek için [Azure şablon başvurusu](/azure/templates/microsoft.resourcegraph/allversions)' na gidin.
- ARM şablonları geliştirmeyi öğrenmek için [Azure Resource Manager belgelerine](../../azure-resource-manager/management/overview.md)bakın.
- Abonelik düzeyinde dağıtım hakkında bilgi edinmek için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Yeni paylaşılan sorguyu çalıştırmak için aşağıdaki adımları izleyin:

1. Portal arama çubuğundan **Kaynak Grafiği sorgularını** arayın ve seçin.

1. **İşletim sistemine göre sayı VM 'leri** adlı paylaşılan sorguyu seçin, sonra **genel bakış** sayfasında **sonuçlar** sekmesini seçin.

Paylaşılan sorgu kaynak grafiği Gezgini 'nden de açılabilir:

1. Portal arama çubuğundan **Kaynak Grafiği Gezgini** ' ni arayın ve seçin.

1. **Sorgu aç** düğmesini seçin.

1. **Türü** _paylaşılan sorgular_ olarak değiştirin. Listedeki **VM 'Leri bayt sayısı** ' nı görmüyorsanız, sonuçları sınırlandırmak için filtre kutusunu kullanın. **İşletim sistemi paylaşılan sorgusuna göre VM sayısı** görünür olduğunda, adını seçin.

1. Sorgu yüklendikten sonra **Sorguyu Çalıştır** düğmesini seçin. Sonuçlar, **sonuçlar** sekmesinde görüntülenir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturulan paylaşılan sorguyu kaldırmak için şu adımları izleyin:

1. Portal arama çubuğundan **Kaynak Grafiği sorgularını** arayın ve seçin.

1. **İşletim sisteminin say** adlı paylaşılan sorgunun yanındaki onay kutusunu belirleyin.

1. Sayfanın üst kısmında **Sil** düğmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak grafiği paylaşılan sorgusu oluşturdunuz.

Paylaşılan sorgular hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [Azure portal sorguları yönetme](./tutorials/create-share-query.md)
