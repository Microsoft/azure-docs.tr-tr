---
title: Yönlendirme olayları için yönetilen kimliği etkinleştirme (Önizleme)-Portal
titleSuffix: Azure Digital Twins
description: Bkz. Azure dijital TWINS için sistem tarafından atanan bir kimliği etkinleştirme ve Azure portal kullanarak olayları iletmek için kullanma.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1211cd306f10fb349bd42568697443ff103a171c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101703102"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Azure dijital TWINS olaylarını yönlendirme için yönetilen bir kimliği etkinleştirme (Önizleme): Azure portal

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Bu makalede, [bir Azure dijital TWINS örneği](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (Şu anda önizleme aşamasında) için sistem tarafından atanan bir kimliğin nasıl etkinleştirileceği ve olayların [Olay Hub](../event-hubs/event-hubs-about.md)'ı, [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   hedefleri ve [Azure depolama kapsayıcısı](../storage/blobs/storage-blobs-introduction.md)gibi desteklenen hedeflere iletilmesi sırasında kimliğin kullanılması açıklanmaktadır.

Bu makale [**Azure Portal**](https://portal.azure.com)kullanarak işlemi adım adım açıklar.

Bu makalede ele alınan adımlar aşağıda verilmiştir: 

1. Sistem tarafından atanan kimlik ile bir Azure dijital TWINS örneği oluşturun veya var olan bir Azure dijital TWINS örneğinde sistem tarafından atanan kimliği etkinleştirin. 
1. Kimliğe uygun bir rol veya roller ekleyin. Örneğin, uç nokta olay hub 'ı ise, **Azure Olay Hub 'ı veri gönderici** rolünü kimliğe atayın veya uç nokta Service Bus **veri gönderici rolü Azure Service Bus** .
1. Azure dijital TWINS 'te kimlik doğrulaması için sistem tarafından atanan kimlikleri kullanabilecek bir uç nokta oluşturun.

## <a name="enable-system-managed-identities-for-an-instance"></a>Örnek için sistem tarafından yönetilen kimlikleri etkinleştirme 

Azure dijital TWINS örneğiniz üzerinde sistem tarafından atanan bir kimliği etkinleştirdiğinizde, Azure, [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md)içinde otomatik olarak bir kimlik oluşturur. Bu kimlik daha sonra olay iletme için Azure dijital TWINS uç noktalarında kimlik doğrulaması yapmak için kullanılabilir.

Azure Digital TWINS örneği için sistem tarafından yönetilen kimlikleri, **Örneğin ilk kurulumunun bir parçası olarak** etkinleştirebilir veya **daha sonra zaten var olan bir örnekte etkinleştirebilirsiniz**.

Bu oluşturma yöntemlerinden herhangi biri, örneğiniz için aynı yapılandırma seçeneklerini ve aynı nihai sonucu verecektir. Bu bölümde, her ikisinin de nasıl yapılacağı açıklanmaktadır.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Örnek oluşturma sırasında sistem tarafından yönetilen bir kimlik ekleme

Bu bölümde, şu anda oluşturulmakta olan bir Azure dijital TWINS örneğinde sistem tarafından yönetilen bir kimliğin nasıl etkinleştirileceğini öğreneceksiniz. Bu bölüm, oluşturma işleminin yönetilen kimlik adımına odaklanır; Yeni bir Azure dijital TWINS örneği oluşturma hakkında tam bir anlatım için bkz. [*nasıl yapılır: örnek ve kimlik doğrulama ayarlama*](how-to-set-up-instance-portal.md).

Sistem tarafından yönetilen kimlik seçeneği, örnek kurulumunun **Gelişmiş** sekmesinde bulunur.

Bu sekmede, bu özelliği açmak için **sistem tarafından yönetilen kimliğin** **Açık** seçeneğini belirleyin.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Azure dijital TWINS için kaynak oluştur iletişim kutusunun Gelişmiş sekmesini gösteren Azure portal ekran görüntüsü. Sekme adı, sistem tarafından yönetilen kimlik için açık seçeneği ve gezinti düğmeleri (gözden geçirme + oluşturma, önceki, Ileri: Gelişmiş) etrafında bir vurgu vardır.":::

Daha sonra, örnek kurulumunun geri kalanı ile devam etmek için alt gezinti düğmelerini kullanabilirsiniz.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Mevcut örneğe sistem tarafından yönetilen bir kimlik ekleme

Bu bölümde, zaten mevcut olan bir Azure dijital TWINS örneğine sistem tarafından yönetilen bir kimlik ekleyeceksiniz.

1. İlk olarak, bir tarayıcıda [Azure Portal](https://portal.azure.com) gidin.

1. Portal arama çubuğunda örneğinizin adını arayın ve ayrıntılarını görüntülemek için seçin.

1. Sol taraftaki menüden **kimlik (Önizleme)** öğesini seçin.

1. Bu sayfada, bu özelliği açmak için **Açık** seçeneğini belirleyin.

1. Onaylamak için **Kaydet** düğmesine ve **Evet** ' e basın.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Bir Azure dijital TWINS örneği için kimlik (Önizleme) sayfasını gösteren Azure portal ekran görüntüsü. Azure Digital TWINS örnek menüsündeki Sayfa adının etrafında bir vurgulama, durum için açık seçeneği, Kaydet düğmesi ve Evet onay düğmesi bulunur.":::

Değişiklik kaydedildikten sonra, yeni kimliğin **nesne kimliği** ve **izinleri** için bu sayfada daha fazla alan görünür.

Gerekirse, **nesne kimliğini** buradan kopyalayabilir ve **izin** düğmesini kullanarak kimliğe atanan Azure rollerini görüntüleyebilirsiniz. Bazı rolleri ayarlamak için sonraki bölüme geçin.

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

Kimliğe bir rol atamak için [Azure Portal](https://portal.azure.com)açarak başlayın.

1. Portal arama çubuğunda adını arayarak uç nokta kaynağınız (Olay Hub 'ınızı, Service Bus konu veya depolama Kapsayıcınız) bulun. 
1. Sol taraftaki menüde **erişim denetimi (IAM)** seçeneğini belirleyin.
1. Yeni bir rol ataması eklemek için **+ Ekle** düğmesini seçin.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Bir olay hub 'ı için erişim denetimi (ıAM) sayfasını gösteren Azure portal ekran görüntüsü. + Ekle düğmesi vurgulanır." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. Aşağıdaki **rol ataması Ekle** sayfasında, değerleri girin:
    * **Rol**: açılan menüden istediğiniz rolü seçin.
    * **Erişim ata**: **sistem tarafından atanan yönetilen kimlik** altında **dijital TWINS**' i seçin.
    * **Abonelik**: Aboneliğinizi seçin. Bu, seçilen abonelik içindeki tüm Azure dijital/WINS yönetilen kimliklerini görüntüler.
    * Şunları **seçin**: burada, rolün atandığı Azure dijital TWINS örneğinizin yönetilen kimliğini seçersiniz. Yönetilen kimliğin adı örneğin adıyla eşleşiyorsa, Azure dijital TWINS örneğinizin adını seçin. Bunu seçtiğinizde, örneğin kimliği bölmenin alt kısmındaki **Seçili Üyeler** bölümünde görünür.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Listelenen alanlar ' rol ataması Ekle ' iletişim kutusunda dolduruluyor":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Ayrıntıları girmeyi tamamladığınızda **Kaydet**' i seçin.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Kimlik tabanlı kimlik doğrulaması ile bir uç nokta oluşturma

Azure dijital TWINS örneğiniz için sistem tarafından yönetilen bir kimlik ayarlayıp uygun rol (ler) atadıktan sonra, kimlik doğrulaması için kimliği kullanabilen Azure dijital TWINS [uç noktaları](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) oluşturabilirsiniz. Bu seçenek yalnızca Olay Hub 'ı ve Service Bus türü uç noktaları için kullanılabilir (Event Grid için desteklenmez).

>[!NOTE]
> Kimlik tabanlı kimlik doğrulamasına geçmek için anahtar tabanlı kimlikle oluşturulmuş olan bir uç noktayı düzenleyemezsiniz. Uç nokta ilk oluşturulduğunda kimlik doğrulama türünü seçmeniz gerekir.

[Azure dijital TWINS uç noktası oluşturmak için yönergeleri](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)izleyin.

Uç nokta türü için gereken ayrıntıları tamamlama adımına geldiğinizde, kimlik doğrulama türü için **kimlik tabanlı** ' yi seçtiğinizden emin olun.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Olay Hub 'ı türünde bir uç nokta oluşturma ekran görüntüsü." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Uç noktanızı ayarlamayı ve **Kaydet**' i seçin.

## <a name="considerations-for-disabling-system-managed-identities"></a>Sistem tarafından yönetilen kimlikleri devre dışı bırakma konuları

Bir kimlik, onu kullanan uç noktalardan ayrı olarak yönetildiğinden, kimlikte veya rollerinin üzerinde yapılan değişikliklerin Azure dijital TWINS örneğinizin uç noktalarında sahip olduğu etkileri göz önünde bulundurmanız önemlidir. Kimlik devre dışıysa veya bir uç nokta için gerekli bir rol bundan kaldırılırsa, uç nokta erişilemez hale gelebilir ve olayların akışı kesintiye uğramaz.

Artık devre dışı bırakılmış bir yönetilen kimlikle ayarlanmış bir uç noktayı kullanmaya devam etmek için, uç noktayı silmeniz ve farklı bir kimlik doğrulama türüyle [yeniden oluşturmanız](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) gerekir. Bu değişiklikten sonra, olayların uç noktaya teslimini sürdürmesini bir saate kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD 'de Yönetilen kimlikler hakkında daha fazla bilgi edinin: 
* [*Azure kaynakları için yönetilen kimlikler*](../active-directory/managed-identities-azure-resources/overview.md)