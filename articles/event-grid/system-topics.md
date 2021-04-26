---
title: Azure Event Grid sistem konuları
description: Azure Event Grid içindeki Sistem konularını açıklar.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: ca59dd6bb99b9b7d06f0622e8c0cb4e4234e21ff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733052"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure Event Grid sistem konuları
Event Grid bir sistem konusu, Azure depolama ve Azure Event Hubs gibi Azure hizmetleri tarafından yayınlanan bir veya daha fazla olayı temsil eder. Örneğin, bir sistem konusu **tüm blob olaylarını** veya yalnızca **BLOB oluşturulmuş** ve **belirli bir depolama hesabı** için yayımlanan **BLOB silinen** olayları temsil edebilir. Bu örnekte, bir BLOB depolama hesabına yüklendiğinde Azure Storage hizmeti, Event Grid ' deki Sistem konusuna **BLOB tarafından oluşturulan** bir olay yayımlar, daha sonra olayı alan ve işleyen [abonelere](event-handlers.md) iletir. 

> [!NOTE] 
> Yalnızca Azure Hizmetleri, olayları sistem konularına yayımlayabilir. Bu nedenle, özel konular veya etki alanları için yaptığınız gibi olayları yayımlamak için kullanabileceğiniz bir uç nokta veya erişim anahtarları edinmezsiniz.

## <a name="azure-services-that-support-system-topics"></a>Sistem konularını destekleyen Azure hizmetleri
Bunlar üzerinde sistem konuları oluşturmayı destekleyen Azure hizmetlerinin güncel listesi aşağıda verilmiştir.

- [Azure Uygulama Yapılandırması](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
- [Azure Blob Depolama](event-schema-blob-storage.md)
- [Azure İletişim Hizmetleri](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Haritalar](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure İlkesi](./event-schema-policy.md)
- [Azure kaynak grupları](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure abonelikleri](event-schema-subscriptions.md)
- [Redis için Azure Önbelleği](event-schema-azure-cache.md)

## <a name="system-topics-as-azure-resources"></a>Azure kaynakları olarak sistem konuları
Geçmişte, bir sistem konusu örtük ve kolaylık sağlaması için açık değildi. Sistem konuları artık Azure kaynakları olarak görünür ve aşağıdaki özellikleri sağlar:

- [Azure portal sistem konularını görüntüleme](create-view-manage-system-topics.md#view-all-system-topics)
- Azure portal sistem konuları ve olay abonelikleri için Kaynak Yöneticisi şablonları dışarı aktarma
- [Sistem konuları için tanılama günlüklerini ayarlama](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Yayımlama ve teslim hatalarında uyarıları ayarlama 

## <a name="lifecycle-of-system-topics"></a>Sistem konuları
Bir sistem konusunu iki şekilde oluşturabilirsiniz: 

- [Bir Azure kaynağında uzantı kaynağı olarak bir olay aboneliği](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)oluşturun, bu, adı şu biçimde olan bir sistem konusunu otomatik olarak oluşturur: `<Azure resource name>-<GUID>` . Bu şekilde oluşturulan sistem konusu, konunun son olay aboneliği silindiğinde otomatik olarak silinir. 
- Bir Azure kaynağı için bir sistem konusu oluşturun ve ardından bu sistem konusu için bir olay aboneliği oluşturun. Bu yöntemi kullandığınızda, sistem konusu için bir ad belirtebilirsiniz. Son olay aboneliği silindiğinde sistem konusu otomatik olarak silinmez. El ile silmeniz gerekir. 

    Azure portal kullandığınızda, bu yöntemi her zaman kullanıyorsunuz demektir. [Bir Azure kaynağının **Olaylar** sayfasını](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)kullanarak bir olay aboneliği oluşturduğunuzda, önce sistem konusu, ardından konu için abonelik oluşturulur. Öncelikle [ **Event Grid sistem konuları** sayfasını](create-view-manage-system-topics.md#create-a-system-topic) kullanarak ve ardından bu konu için bir abonelik oluşturduğunuzda, bir sistem konusunu açık bir şekilde oluşturabilirsiniz. 

[CLI](create-view-manage-system-topics-cli.md), [rest](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)veya [Azure Resource Manager şablonu](create-view-manage-system-topics-arm.md)kullandığınızda yukarıdaki yöntemlerden birini seçebilirsiniz. Bu, sistem konuları oluşturmanın en son yolu olduğu için öncelikle bir sistem konusu oluşturmanız ve ardından bir abonelik oluşturmanız önerilir.

Azure ilkelerini Event Grid hizmetinin oluşturamamasında bu şekilde ayarladıysanız, sistem konusu oluşturma işlemi başarısız olur. Örneğin, abonelikte yalnızca belirli türdeki kaynakların oluşturulmasına izin veren bir ilkeniz olabilir (örneğin: Azure Storage, Azure Event Hubs, vb.). 

## <a name="location-and-resource-group-for-a-system-topic"></a>Bir sistem konusunun konumu ve kaynak grubu
Belirli bir bölgedeki/konumdaki Azure olay kaynakları için sistem konusu, Azure Olay kaynağıyla aynı konumda oluşturulur. Örneğin, Doğu ABD bir Azure Blob depolama alanı için bir olay aboneliği oluşturursanız, sistem konusu Doğu ABD oluşturulur. Azure abonelikleri, kaynak grupları veya Azure haritaları gibi genel Azure olay kaynakları için Event Grid, **genel** konumdaki Sistem konusunu oluşturur. 

Genel olarak, sistem konusu Azure olay kaynağının bulunduğu kaynak grubunda oluşturulur. Azure abonelik kapsamında oluşturulan olay abonelikleri için, sistem konusu **Batı ABD 2** bölgesindeki **Default-eventgrid** kaynak grubunda oluşturulur. Kaynak grubu yoksa, Azure Event Grid Sistem konusunu oluşturmadan önce onu oluşturur. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın: 

- [Azure Portal kullanarak sistem konuları oluşturun, görüntüleyin ve yönetin](create-view-manage-system-topics.md).
- [Azure CLı kullanarak Event Grid sistem konuları oluşturma, görüntüleme ve yönetme](create-view-manage-system-topics-cli.md)
- [Azure Resource Manager şablonları kullanarak Event Grid sistem konuları oluşturma](create-view-manage-system-topics-arm.md)
