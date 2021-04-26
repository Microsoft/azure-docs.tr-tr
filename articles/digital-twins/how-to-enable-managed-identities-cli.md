---
title: Yönlendirme olayları (Önizleme) için yönetilen bir kimlik etkinleştirme-CLı
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS için sistem tarafından atanan bir kimliği etkinleştirme ve Azure CLı kullanarak olayları iletmek için kullanma konusuna bakın.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e9eb5950b5cf7d4e7d0270deed72866ee28e3962
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106913"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Azure dijital TWINS olaylarını yönlendirme için yönetilen bir kimliği etkinleştirme (Önizleme): Azure CLı

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Bu makalede, [bir Azure dijital TWINS örneği](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (Şu anda önizleme aşamasında) için sistem tarafından atanan bir kimliğin nasıl etkinleştirileceği ve olayların [Olay Hub](../event-hubs/event-hubs-about.md)'ı, [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   hedefleri ve [Azure depolama kapsayıcısı](../storage/blobs/storage-blobs-introduction.md)gibi desteklenen hedeflere iletilmesi sırasında kimliğin kullanılması açıklanmaktadır.

Bu makalede, [**Azure CLI**](/cli/azure/what-is-azure-cli)'yı kullanarak işlem adım adım açıklanmaktadır.

Bu makalede ele alınan adımlar aşağıda verilmiştir: 

1. Sistem tarafından atanan kimlik ile bir Azure dijital TWINS örneği oluşturun veya var olan bir Azure dijital TWINS örneğinde sistem tarafından atanan kimliği etkinleştirin. 
1. Kimliğe uygun bir rol veya roller ekleyin. Örneğin, uç nokta olay hub 'ı ise, **Azure Olay Hub 'ı veri gönderici** rolünü kimliğe atayın veya uç nokta Service Bus **veri gönderici rolü Azure Service Bus** .
1. Azure dijital TWINS 'te kimlik doğrulaması için sistem tarafından atanan kimlikleri kullanabilecek bir uç nokta oluşturun.

## <a name="enable-system-managed-identities-for-an-instance"></a>Örnek için sistem tarafından yönetilen kimlikleri etkinleştirme 

Azure dijital TWINS örneğiniz üzerinde sistem tarafından atanan bir kimliği etkinleştirdiğinizde, Azure, [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md)içinde otomatik olarak bir kimlik oluşturur. Bu kimlik daha sonra olay iletme için Azure dijital TWINS uç noktalarında kimlik doğrulaması yapmak için kullanılabilir.

Azure Digital TWINS örneği için sistem tarafından yönetilen kimlikleri, **Örneğin ilk kurulumunun bir parçası olarak** etkinleştirebilir veya **daha sonra zaten var olan bir örnekte etkinleştirebilirsiniz**.

Bu oluşturma yöntemlerinden herhangi biri, örneğiniz için aynı yapılandırma seçeneklerini ve aynı nihai sonucu verecektir. Bu bölümde, her ikisinin de nasıl yapılacağı açıklanmaktadır.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Örnek oluşturma sırasında sistem tarafından yönetilen bir kimlik ekleme

Bu bölümde, şu anda oluşturulmakta olan bir Azure dijital TWINS örneğinde sistem tarafından yönetilen bir kimliğin nasıl etkinleştirileceğini öğreneceksiniz. 

Bu, `--assign-identity` `az dt create` örneği oluşturmak için kullanılan komuta bir parametre eklenerek yapılır. (Bu komut hakkında daha fazla bilgi için, [başvuru belgelerine](/cli/azure/dt#az_dt_create) veya [bir Azure dijital TWINS örneği ayarlamaya yönelik genel yönergelere](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)bakın).

Sistem tarafından yönetilen kimliğe sahip bir örnek oluşturmak için aşağıdaki  `--assign-identity` gibi parametreyi ekleyin:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Mevcut örneğe sistem tarafından yönetilen bir kimlik ekleme

Bu bölümde, zaten mevcut olan bir Azure dijital TWINS örneğine sistem tarafından yönetilen bir kimlik ekleyeceksiniz.

Bu, `az dt create` komut ve parametresiyle de yapılır `--assign-identity` . Oluşturulacak örneğin yeni bir adını sağlamak yerine, bu örneğin değerini güncelleştirmek için zaten var olan bir örneğin adını belirtebilirsiniz `--assign-identity` .

Yönetilen kimliği **etkinleştirme** komutu, sistem tarafından yönetilen kimliğe sahip bir örnek oluşturma komutuyla aynıdır. Tüm bu değişiklikler örnek adı parametresinin değeridir:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Şu anda etkin olduğu bir örnekte yönetilen kimliği **devre dışı bırakmak** için, olarak ayarlamak için aşağıdaki benzer komutu kullanın `--assign-identity` `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Kimliğe Azure rolleri atama 

Azure dijital TWINS örneğiniz için sistem tarafından atanan bir kimlik oluşturulduktan sonra, olayları desteklenen hedeflere iletmek için farklı [uç nokta](concepts-route-events.md) türleriyle kimlik doğrulaması yapmak üzere uygun rolleri atamanız gerekir. Bu bölüm, rol seçeneklerini ve bunları sistem tarafından atanan kimliğe atamayı açıklar.

>[!NOTE]
> Bu önemli bir adımdır. Bu, kimlik uç noktalarınıza erişemez ve etkinliklerinizin teslim edilamadığına gerek kalmaz.

### <a name="supported-destinations-and-azure-roles"></a>Desteklenen hedefler ve Azure rolleri 

Bir kimliğin bir uç noktaya erişmesi gereken en düşük roller, hedef türüne göre aşağıda verilmiştir. Daha yüksek izinlerle (veri sahibi rolleri gibi) roller de çalışacaktır.

| Hedef | Azure rolü |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs veri gönderici |
| Azure Service Bus | Veri Göndericisini Azure Service Bus |
| Azure depolama kapsayıcısı | Depolama Blob Verileri Katkıda Bulunanı |

Azure dijital TWINS 'de yönlendirme için desteklenen uç noktalar, rotalar ve hedef türleri hakkında daha fazla bilgi için bkz. [*Kavramlar: olay yolları*](concepts-route-events.md).

### <a name="assign-the-role"></a>Rolü ata

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

`--scopes` `az dt create` Kimliği, belirtilen bir role sahip bir veya daha fazla kapsam için atamak üzere komutuna ekleyebilirsiniz. Bu, örneği ilk oluştururken veya daha sonra zaten var olan bir örneğin adına geçirerek kullanılabilir.

Aşağıda, sistem tarafından yönetilen kimliğe sahip bir örnek oluşturan ve bu kimliğe bir olay hub 'ında çağrılan özel bir rol atayan bir örnek verilmiştir `MyCustomRole` .

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Bu komutla rol atamalarından daha fazla örnek için, bkz. daha [ **az DT Create** Reference documentation](/cli/azure/dt#az_dt_create).

Alternatif olarak, rolleri oluşturmak ve yönetmek için [**az role atama**](/cli/azure/role/assignment) komut grubunu da kullanabilirsiniz. Bu, Create komutuyla rol atamasını gruplandırmak istemediğiniz ek senaryoları desteklemek için kullanılabilir.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Kimlik tabanlı kimlik doğrulaması ile bir uç nokta oluşturma

Azure dijital TWINS örneğiniz için sistem tarafından yönetilen bir kimlik ayarlayıp uygun rol (ler) atadıktan sonra, kimlik doğrulaması için kimliği kullanabilen Azure dijital TWINS [uç noktaları](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) oluşturabilirsiniz. Bu seçenek yalnızca Olay Hub 'ı ve Service Bus türü uç noktaları için kullanılabilir (Event Grid için desteklenmez).

>[!NOTE]
> Kimlik tabanlı kimlik doğrulamasına geçmek için anahtar tabanlı kimlikle oluşturulmuş olan bir uç noktayı düzenleyemezsiniz. Uç nokta ilk oluşturulduğunda kimlik doğrulama türünü seçmeniz gerekir.

Bu, `--auth-type` `az dt endpoint create` uç noktayı oluşturmak için kullanılan komuta bir parametre eklenerek yapılır. (Bu komut hakkında daha fazla bilgi için, [başvuru belgelerine](/cli/azure/dt/endpoint/create) veya [bir Azure dijital TWINS uç noktası ayarlamaya yönelik genel yönergelere](how-to-manage-routes-apis-cli.md#create-the-endpoint)bakın).

Kimlik tabanlı kimlik doğrulaması kullanan bir uç nokta oluşturmak için, `IdentityBased` parametresiyle birlikte kimlik doğrulama türünü belirtin  `--auth-type` . Aşağıdaki örnekte Event Hubs uç noktası için gösterilmektedir.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Sistem tarafından yönetilen kimlikleri devre dışı bırakma konuları

Bir kimlik, onu kullanan uç noktalardan ayrı olarak yönetildiğinden, kimlikte veya rollerinin üzerinde yapılan değişikliklerin Azure dijital TWINS örneğinizin uç noktalarında sahip olduğu etkileri göz önünde bulundurmanız önemlidir. Kimlik devre dışıysa veya bir uç nokta için gerekli bir rol bundan kaldırılırsa, uç nokta erişilemez hale gelebilir ve olayların akışı kesintiye uğramaz.

Artık devre dışı bırakılmış bir yönetilen kimlikle ayarlanmış bir uç noktayı kullanmaya devam etmek için, uç noktayı silmeniz ve farklı bir kimlik doğrulama türüyle [yeniden oluşturmanız](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) gerekir. Bu değişiklikten sonra, olayların uç noktaya teslimini sürdürmesini bir saate kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD 'de Yönetilen kimlikler hakkında daha fazla bilgi edinin: 
* [*Azure kaynakları için yönetilen kimlikler*](../active-directory/managed-identities-azure-resources/overview.md)