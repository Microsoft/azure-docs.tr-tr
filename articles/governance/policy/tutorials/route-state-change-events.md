---
title: 'Öğretici: Azure CLı ile ilke durumu değişikliği olaylarını Event Grid olarak yönlendirme'
description: Bu öğreticide, ilke durumu değişikliği olaylarını dinlemek ve bir Web kancası çağırmak için Event Grid yapılandırırsınız.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735562"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Öğretici: Azure CLı ile ilke durumu değişikliği olaylarını Event Grid olarak yönlendirme

Bu makalede, bir Web uç noktasına ilke durum değişikliği olayları göndermek için Azure Ilke olay aboneliklerini ayarlamayı öğreneceksiniz. Azure Ilkesi kullanıcıları, kaynaklarda ilke durumu değişikliği gerçekleştiğinde oluşturulan olaylara abone olabilir. Bu olaylar Web kancalarını, [Azure işlevlerini](../../../azure-functions/index.yml), [Azure depolama kuyruklarını](../../../storage/queues/index.yml)veya [Azure Event Grid](../../../event-grid/index.yml)tarafından desteklenen herhangi bir olay işleyicisini tetikleyebilirler. Normalde olayları, olay verilerini işleyen ve eylemler gerçekleştiren bir uç noktaya gönderirsiniz. Bununla birlikte, bu öğreticiyi basitleştirmek için olayları toplayıp görüntüleyen bir Web uygulamasına gönderirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Bu hızlı başlangıç, Azure CLı sürüm 2.0.76 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

- Daha önce Azure Ilkesi 'ni veya Event Grid kullanmış olsanız bile, ilgili kaynak sağlayıcılarını yeniden kaydedin:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Event Grid konuları Azure kaynaklarıdır ve bir Azure kaynak grubuna yerleştirilmelidir. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal koleksiyondur.

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. 

Aşağıdaki örnek `<resource_group_name>` , _westus_ konumunda adlı bir kaynak grubu oluşturur. `<resource_group_name>` değerini kaynak grubunuz için benzersiz bir adla değiştirin.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Event Grid sistem konusu oluşturma

Artık bir kaynak grubumuz olduğuna göre, bir [Sistem konusu](../../../event-grid/system-topics.md)oluşturacağız. Event Grid bir sistem konusu Azure Ilkesi ve Azure Event Hubs gibi Azure hizmetleri tarafından yayımlanan bir veya daha fazla olayı temsil eder. Bu sistem konusu, `Microsoft.PolicyInsights.PolicyStates` Azure ilke durumu değişiklikleri için konu türünü kullanır. `<SubscriptionID>` **Kapsam** PARAMETRESINDE, aboneliğinizin kimliği ve `<resource_group_name>` **kaynak grubu** parametresinde, daha önce oluşturulmuş kaynak grubuyla değiştirin.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>İleti uç noktası oluşturma

Konuya abone olmadan önce olay iletisi için uç noktayı oluşturalım. Normalde, olay verileri temelinde uç nokta eylemleri gerçekleştirir. Bu hızlı başlangıcı basitleştirmek için, olay iletilerini görüntüleyin bir [önceden oluşturulmuş web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtırsınız. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

`<your-site-name>` değerini web uygulamanız için benzersiz bir adla değiştirin. Web uygulaması adı bir DNS girdisinin parçası olduğundan benzersiz olmalıdır.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarıyla gerçekleştirildikten sonra, web uygulamanızı görüntüleyip çalıştığından emin olun. Web tarayıcısında şu adrese gidin: `https://<your-site-name>.azurewebsites.net`

Şu anda iletilerin görüntülenmediği siteyi görüyor olmalısınız.

## <a name="subscribe-to-the-system-topic"></a>Sistem konusuna abone olma

Event Grid’e hangi olayları izlemek istediğinizi ve bu olayların nereye gönderileceğini bildirmek için bir konuya abone olursunuz. Aşağıdaki örnek, oluşturduğunuz sistem konusuna abone olur ve olay bildirimlerini almak için Web uygulamanızdan URL 'YI uç nokta olarak geçirir. Olay aboneliğiniz için `<event_subscription_name>` öğesini bir ad ile değiştirin. `<resource_group_name>` ve `<your-site-name>` için daha önce oluşturduğunuz değerleri kullanın.

Web uygulamanızın uç noktası `/api/updates/` sonekini içermelidir.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Web uygulamanızı yeniden görüntüleyin ve buna bir abonelik doğrulama olayının gönderildiğine dikkat edin. Göz simgesini seçerek olay verilerini genişletin. Uç noktanın olay verilerini almak istediğini doğrulayabilmesi için Event Grid doğrulama olayını gönderir. Web uygulaması aboneliği doğrulamak için kod içerir.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Önceden oluşturulmuş Web uygulamasındaki Abonelik doğrulama olayının Event Grid ekran görüntüsü.":::

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, bir ilke ataması oluşturup **kaynak grupları tanımına bir etiket gerektir** ' i atarsınız. Bu ilke tanımı, ilke ataması sırasında yapılandırılan etiketin eksik olduğu kaynak gruplarını tanımlar.

Olay Kılavuzu konusunu tutmak için oluşturduğunuz kaynak grubuna kapsamlı bir ilke ataması oluşturmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

Yukarıdaki komutta aşağıdaki bilgiler kullanılmaktadır:

- **Ad** - Atamanın gerçek adı. Bu örnekte, _requiredtags olayları_ kullanıldı.
- **Görünen Ad** - Bu ilke atamasının görünen adı. Bu durumda, _RG için Tag gerektir_' i kullanıyorsunuz.
- **Kapsam** - Kapsam, ilke atamasının hangi kaynaklarda veya kaynak gruplarında uygulanacağını belirler. Bir abonelikten kaynak gruplarına kadar değişiklik gösterebilir. &lt;Kapsam&gt; yerine kaynak grubunuzun adını yazdığınızdan emin olun. Kaynak grubu kapsamının biçimi `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **İlke** - Bu, atamayı oluşturmak için kullandığınız ilke tanımı kimliğidir. Bu durumda, ilke tanımının KIMLIĞI, _kaynak gruplarında bir etiket gerektirir_. İlke tanımı kimliğini almak için şu komutu çalıştırın: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

İlke atamasını oluşturduktan sonra, Web uygulamasında bir **Microsoft. Policınghts. PolicyStateCreated** Olay bildiriminin görünmesini bekleyin. Oluşturduğumuz kaynak grubu `data.complianceState` başlamak Için _uyumsuz_ bir değer gösteriyor.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Önceden oluşturulmuş Web uygulamasındaki kaynak grubu için Event Grid abonelik Ilkesi durumu oluşturma olayının ekran görüntüsü.":::

> [!NOTE]
> Kaynak grubu, abonelik veya yönetim grubu hiyerarşisinden diğer ilke atamalarını devralırsa, her biri için olaylar da görüntülenir. Özelliği değerlendirerek bu öğreticide atamaya yönelik olayın olduğunu onaylayın `data.policyDefinitionId` .

## <a name="trigger-a-change-on-the-resource-group"></a>Kaynak grubunda bir değişiklik tetikleyin

Kaynak grubunu uyumlu hale getirmek için **EventTest** adlı bir etiket gereklidir. Aşağıdaki komutla, `<SubscriptionID>` ABONELIK kimliğiniz ve kaynak grubunun adı ile değiştirerek etiketini kaynak grubuna ekleyin `<ResourceGroup>` :

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Kaynak grubuna gerekli etiketi ekledikten sonra, Web uygulamasında bir **Microsoft. Policınghts. PolicyStateChanged** Olay bildiriminin görünmesini bekleyin. Olayı genişletin ve `data.complianceState` değer artık _uyumlu_ olarak gösterilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu Web uygulamasıyla ve Azure Ilkesi olay aboneliğiyle çalışmaya devam etmeyi planlıyorsanız, bu makalede oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu makalede oluşturduğunuz kaynakları silmek için aşağıdaki komutu kullanın.

`<resource_group_name>` değerini yukarıda oluşturduğunuz kaynak grubuyla değiştirin.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Ilkesi için konu ve olay abonelikleri oluşturmayı bildiğinize göre, ilke durumu değişikliği olayları hakkında daha fazla bilgi edinin ve Event Grid şunları yapmanıza yardımcı olabilir:

- [Azure Ilke durum değişikliği olaylarına yeniden davranıma](../concepts/event-overview.md)
- [Event Grid için Azure Ilke şeması ayrıntıları](../../../event-grid/event-schema-policy.md)
- [Event Grid Hakkında](../../../event-grid/overview.md)