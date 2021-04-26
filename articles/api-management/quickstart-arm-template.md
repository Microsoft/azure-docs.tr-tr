---
title: Hızlı başlangıç-ARM şablonunu kullanarak Azure API Management örneği oluşturma
description: Bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Geliştirici katmanında Azure API Management örneği oluşturmayı öğrenin.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792251"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak yeni bir Azure API Management hizmet örneği oluşturma

Bu hızlı başlangıçta, bir Azure API Management (APıM) hizmet örneği oluşturmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı açıklanmaktadır. APıM, kuruluşların veri ve hizmetlerinin potansiyelini açmak için API 'Leri dış, iş ortağı ve iç geliştiricilere yayımlamalarına yardımcı olur. API Management; geliştirici katılımı, iş öngörüleri, analizler, güvenlik ve koruma aracılığıyla başarılı bir API programı yürütmeye ilişkin temel uzmanlıklar sağlar. APıM, her yerde barındırılan mevcut arka uç hizmetleri için modern API ağ geçitleri oluşturmanıza ve yönetmenize olanak sağlar. Daha fazla bilgi için bkz. [Genel Bakış](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

Aşağıdaki kaynak şablonda tanımlanmıştır:

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

[Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular)daha fazla Azure API Management şablonu örneği bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon, otomatik olarak oluşturulan bir ada sahip bir API Management hizmet örneği oluşturur.

    [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    Bu örnekte, örnek geliştirici katmanında yapılandırılır ve Azure API Management değerlendirmek için ekonomik bir seçenektir. Bu katman üretim kullanımı için değildir. API Management katmanlarını ölçeklendirme hakkında daha fazla bilgi için bkz. [yükseltme ve ölçeklendirme](upgrade-and-scale.md).

1. Aşağıdaki değerleri seçin veya girin.
    - **Abonelik**: Bir Azure aboneliği seçin.
    - **Kaynak grubu**: **Yeni oluştur**' u seçin, kaynak grubu için benzersiz bir ad girin ve **Tamam**' ı seçin.
    - **Bölge**: kaynak grubu için bir konum seçin. Örnek: **Orta ABD**.
    - **Yayımcı e-postası**: bildirimleri almak için bir e-posta adresi girin.
    - **Yayımcı adı**: API yayımcısı için seçtiğiniz bir ad girin.
    - **SKU**: **geliştiricinin** varsayılan değerini kabul edin.
    - **SKU sayısı**: varsayılan değeri kabul edin.
    - **Konum**: API Management hizmeti için üretilen konumu kabul edin.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="API Management şablonu özellikleri":::

1. **Gözden geçir + oluştur**' u seçin ve hüküm ve koşulları gözden geçirin. Kabul ediyorsanız **Oluştur**' u seçin.

    > [!TIP]
    >  Geliştirici katmanında bir API Management hizmeti oluşturmak ve etkinleştirmek için 30 ila 40 dakika sürebilir.

1. Örnek başarıyla oluşturulduktan sonra bir bildirim alırsınız:

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Dağıtım bildirimi":::

 Şablonu dağıtmak için Azure portalı kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtılan kaynakları denetlemek için Azure portal kullanın veya dağıtılan kaynakları listelemek için Azure CLı veya Azure PowerShell gibi araçları kullanın.

1. [Azure Portal](https://portal.azure.com), **API Management Hizmetleri** arayıp seçin ve oluşturduğunuz hizmet örneğini seçin.
1. **Genel bakış** sayfasında hizmetinizin özelliklerini gözden geçirin.

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Hizmete genel bakış sayfası":::

API Management hizmet örneğiniz çevrimiçi olduğunda, bunu kullanmaya hazırsınız demektir. İlk API 'nizi [içeri aktarma ve yayımlama](import-and-publish.md) öğreticisiyle başlayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticilerle çalışmaya devam etmeyi planlıyorsanız API Management örneğini yerinde bırakmak isteyebilirsiniz. Artık gerekli değilse kaynak grubundaki kaynakları silen kaynak grubunu silin.

1. [Azure Portal](https://portal.azure.com), **kaynak gruplarını** arayıp seçin. **Giriş** sayfasında **kaynak grupları** ' nı da seçebilirsiniz.
1. **Kaynak grupları** sayfasında, kaynak grubunuzu seçin.
1. Kaynak grubu sayfasında, **kaynak grubunu sil**' i seçin.

    :::image type="content" source="media/quickstart-arm-template/delete-resource-group.png" alt-text="Kaynak grubunu silme":::
1. Kaynak grubunuzun adını yazın ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: ilk API 'nizi Içeri aktarma ve yayımlama](import-and-publish.md)
