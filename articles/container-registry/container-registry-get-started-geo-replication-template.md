---
title: Hızlı başlangıç-coğrafi olarak çoğaltılan kayıt defteri Azure Resource Manager şablonu oluşturma
description: Azure Resource Manager şablonu kullanarak coğrafi olarak çoğaltılan bir Azure Container Registry oluşturmayı öğrenin.
services: azure-resource-manager
author: dlepow
ms.author: danlep
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: c59c7897054b2ad65a76353e6d886af46cac91e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537442"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak coğrafi olarak çoğaltılan kapsayıcı kayıt defteri oluşturma

Bu hızlı başlangıçta, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure Container Registry örneği oluşturma gösterilmektedir. Şablon, birden fazla Azure bölgesinde kayıt defteri içeriğini otomatik olarak eşitleyen [coğrafi olarak çoğaltılan](container-registry-geo-replication.md) bir kayıt defteri ayarlar. Coğrafi çoğaltma, tek bir yönetim deneyimi sağlarken bölgesel dağıtımlardan gelen görüntülere ağ kapatma erişimi sağlar. Bu, [Premium](container-registry-skus.md) kayıt defteri hizmet katmanının bir özelliğidir.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/) alınmıştır. Şablon bir kayıt defteri ve ek bölgesel bir çoğaltma ayarlar.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

Aşağıdaki kaynaklar şablonda tanımlanmıştır:

* **[Microsoft. containerregistry/kayıt defterleri](/azure/templates/microsoft.containerregistry/registries)**: Azure Container Registry oluşturma
* **[Microsoft. ContainerRegistry/kayıt defterleri/çoğaltmalar](/azure/templates/microsoft.containerregistry/registries/replications)**: kapsayıcı kayıt defteri çoğaltması oluşturma

Daha fazla Azure Container Registry şablon örneği [hızlı başlangıç şablonu galerisinde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

 1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın.

    [![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Aşağıdaki değerleri seçin veya girin.

    * **Abonelik**: Bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Yeni oluştur**' u seçin, kaynak grubu için benzersiz bir ad girin ve **Tamam**' ı seçin.
    * **Bölge**: kaynak grubu için bir konum seçin. Örnek: **Orta ABD**.
    * **ACR adı**: kayıt defteri için oluşturulan adı kabul edin veya bir ad girin. Bu, genel olarak benzersiz olmalıdır.
    * **ACR Yönetici Kullanıcı etkin**: varsayılan değeri kabul edin.
    * **Konum**: kayıt defterinin ana çoğaltması için üretilen konumu kabul edin veya **Orta ABD** gibi bir konum girin. 
    * **ACR SKU 'su**: varsayılan değeri kabul edin.
    * **ACR çoğaltma konumu**: bölgenin kısa adını kullanarak kayıt defteri çoğaltması için bir konum girin. Giriş kayıt defteri konumundan farklı olmalıdır. Örnek: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Şablon Özellikleri":::

1. **Gözden geçir + oluştur**' u seçin ve hüküm ve koşulları gözden geçirin. Kabul ediyorsanız **Oluştur**' u seçin.

1. Kayıt defteri başarıyla oluşturulduktan sonra bir bildirim alırsınız:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Portal bildirimi":::

 Şablonu dağıtmak için Azure portalı kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Kapsayıcı kayıt defterinin özelliklerini gözden geçirmek için Azure portal veya Azure CLı gibi bir araç kullanın.

1. Portalda kapsayıcı kayıt defterleri ' ni arayın ve oluşturduğunuz kapsayıcı kayıt defterini seçin.

1. **Genel bakış** sayfasında, kayıt defterinin **oturum açma sunucusuna** göz atın. Resimleri etiketlemek ve Kayıt defterinize göndermek için Docker kullandığınızda bu URI 'yi kullanın. Bilgi için bkz. [Docker CLI kullanarak ilk görüntünüzü gönderme](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Kayıt defterine genel bakış":::

1. **Çoğaltmalar** sayfasında, giriş çoğaltmasının ve şablon aracılığıyla eklenen çoğaltmanın konumlarını onaylayın. İsterseniz bu sayfada daha fazla çoğaltma ekleyin.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Kayıt defteri çoğaltmaları":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında, kaynak grubunu, kayıt defterini ve kayıt defteri çoğaltmasını silin. Bunu yapmak için Azure portal gidin, kayıt defterini içeren kaynak grubunu seçin ve **kaynak grubunu sil**' i seçin.

:::image type="content" source="media/container-registry-get-started-geo-replication-template/delete-resource-group.png" alt-text="Kaynak grubunu silme":::

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta ARM şablonuyla bir Azure Container Registry oluşturdunuz ve başka bir konumda kayıt defteri çoğaltması yapılandırdınız. ACR 'ye daha ayrıntılı bir bakış için Azure Container Registry öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Container Registry öğreticileri](container-registry-tutorial-prepare-registry.md)

Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
