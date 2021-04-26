---
title: Azure şablonu kullanarak Azure Service Bus ad alanı ve kuyruk oluşturma
description: 'Hızlı başlangıç: Azure Resource Manager şablonu kullanarak Service Bus ad alanı ve kuyruk oluşturma'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6577abf1900a166087ff63c331d7c0390fab1342
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535023"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Service Bus ad alanı ve kuyruk oluşturma

Bu makalede, bir Service Bus ad alanı ve bu ad alanı içinde bir sıra oluşturan Azure Resource Manager şablonun (ARM şablonu) nasıl kullanılacağı gösterilmektedir. Makalesinde hangi kaynakların dağıtıldığını ve dağıtım yürütüldüğünde belirtilen parametrelerin nasıl tanımlanacağı açıklanmaktadır. Bu şablonu kendi dağıtımlarınız için kullanabilir veya kendi gereksinimlerinize göre özelleştirebilirsiniz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue) alınmıştır.

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

Şablonda tanımlanan kaynaklar şunları içerir:

- [**Microsoft. ServiceBus/ad alanları**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft. ServiceBus/namespaces/kuyruklar**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Aşağıdaki ARM şablonları indirme ve dağıtım için kullanılabilir.
>
> * [Kuyruk ve yetkilendirme kuralıyla Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-auth-rule.md)
> * [Konu ve abonelikle bir Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace.md)
> * [Konu, abonelik ve kuralla Service Bus ad alanı oluşturma](service-bus-resource-manager-namespace-topic-with-rule.md)

[Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular) daha fazla şablon bulabilirsiniz

## <a name="deploy-the-template"></a>Şablonu dağıtma

Bu şablonla bir Service Bus ad alanını bir sıraya dağıtırsınız.

[Service Bus kuyrukları](service-bus-queues-topics-subscriptions.md#queues) , bir veya daha fazla rakip tüketiciye ilk olarak ilk çıkar (FIFO) ileti teslimi sunar.

Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın: daha sonra kolayca temizlenebilmeniz için dağıtım için yeni bir kaynak grubu oluşturun.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

1. Dağıtımın durumunu görmek için en üstteki **Bildirimler** ' i seçin. Dağıtım başarılı olana kadar bekleyin. Ardından, Service Bus ad alanını içeren kaynak grubunun sayfasına gitmek için bildirim iletisindeki **kaynak grubuna git** ' i seçin. 

    ![Dağıtımdan bildirim](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Kaynak listesinde Service Bus ad alanınızı görtığınızdan emin olun. 

    ![Kaynak grubu-ad alanı](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. **Service Bus ad alanı** sayfasını görmek için listeden ad alanını seçin. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

1. Azure portal, kaynak grubunuzun **kaynak grubu** sayfasına gidin.
2. Araç çubuğundan **Kaynak grubunu sil** seçeneğini belirleyin. 
3. Kaynak grubunun adını yazın ve **Sil**' i seçin. 

    ![Kaynak Grubu-Sil](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Sonraki adımlar

Ad alanı/sıra için nasıl yetkilendirme kuralı oluşturulacağını gösteren aşağıdaki konuya bakın:

[ARM şablonunu kullanarak ad alanı ve sıra için Service Bus yetkilendirme kuralı oluşturma](service-bus-resource-manager-namespace-auth-rule.md)

Şu makaleleri görüntüleyerek bu kaynakları yönetmeyi öğrenin:

* [Service Bus’ı PowerShell ile yönetme](service-bus-manage-with-ps.md)
* [Service Bus Gezgini ile Service Bus kaynaklarını yönetme](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
