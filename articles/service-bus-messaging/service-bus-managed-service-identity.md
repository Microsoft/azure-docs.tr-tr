---
title: Service Bus ile Azure kaynakları için Yönetilen kimlikler
description: Azure Service Bus ile Azure kaynakları için Yönetilen kimlikler kullanma
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 57c52640262854037420c1679804f611394230ef
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793146"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Azure Service Bus kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama
[Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , uygulama kodunuzun çalıştırıldığı dağıtımla ilişkili güvenli bir kimlik oluşturmanıza olanak sağlayan bir çapraz Azure özelliğidir. Daha sonra bu kimliği, uygulamanızın ihtiyaç duyacağı belirli Azure kaynaklarına erişim için özel izinler veren erişim denetimi rolleriyle ilişkilendirebilirsiniz.

Yönetilen kimlikler ile Azure platformu bu çalışma zamanı kimliğini yönetir. Uygulama kodunuzda veya yapılandırmanızda, kimliğin kendisi için ya da erişmeniz gereken kaynaklar için erişim anahtarlarını depolamanız ve korumanız gerekmez. Bir Azure App Service uygulamasının içinde veya Azure kaynakları için etkinleştirilmiş yönetilen varlıklara sahip bir sanal makinede çalışan Service Bus istemci uygulamasının SAS kurallarını ve anahtarlarını veya diğer erişim belirteçlerini işlemesi gerekmez. İstemci uygulaması yalnızca Service Bus mesajlaşma ad alanının uç nokta adresine ihtiyaç duyuyor. Uygulama bağlandığında, Service Bus yönetilen varlığın bağlamını Bu makalenin ilerleyen kısımlarında bir örnekte gösterilen bir işlemde istemciye bağlar. Yönetilen bir kimlikle ilişkilduktan sonra, Service Bus istemciniz tüm yetkili işlemleri yapabilir. Yönetilen bir varlık Service Bus rolleriyle ilişkilendirerek yetki verilir. 

## <a name="overview"></a>Genel Bakış
Bir güvenlik sorumlusu (Kullanıcı, Grup veya uygulama) bir Service Bus varlığına erişmeyi denediğinde, isteğin yetkilendirilmiş olması gerekir. Azure AD ile bir kaynağa erişim iki adımlı bir işlemdir. 

 1. İlk olarak, güvenlik sorumlusunun kimliği doğrulanır ve bir OAuth 2,0 belirteci döndürülür. Belirteç istemek için kaynak adı `https://servicebus.azure.net`.
 1. Ardından, belirteç, belirtilen kaynağa erişim yetkisi vermek için Service Bus hizmetine bir isteğin bir parçası olarak geçirilir.

Kimlik doğrulama adımı, bir uygulama isteğinin çalışma zamanında bir OAuth 2,0 erişim belirteci içermesi gerekir. Bir uygulama bir Azure VM 'si, bir sanal makine ölçek kümesi veya bir Azure Işlev uygulaması gibi bir Azure varlığı içinde çalışıyorsa, kaynaklara erişmek için yönetilen bir kimlik kullanabilir. 

Yetkilendirme adımı, güvenlik sorumlusuna bir veya daha fazla RBAC rolünün atanmasını gerektirir. Azure Service Bus, Service Bus kaynakları için izin kümelerini çevreleyen RBAC rolleri sağlar. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. Azure Service Bus için RBAC rolleri atama hakkında daha fazla bilgi için, bkz. [Azure Service Bus Için YERLEŞIK RBAC rolleri](#built-in-rbac-roles-for-azure-service-bus). 

Service Bus istek yapan yerel uygulamalar ve Web uygulamaları Azure AD ile de yetki verebilir. Bu makalede, bir erişim belirtecinin nasıl isteneceğini ve Service Bus kaynakları için istekleri yetkilendirmek üzere nasıl kullanılacağı gösterilmektedir. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Erişim hakları için RBAC rolleri atama
Azure Active Directory (Azure AD), [rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim haklarını yetkilendirir. Azure Service Bus, Service Bus varlıklara erişmek için kullanılan ortak izin kümelerini çevreleyen yerleşik RBAC rollerinin bir kümesini tanımlar ve verilere erişmek için özel roller de tanımlayabilir.

Azure AD güvenlik sorumlusuna bir RBAC rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, abonelik düzeyi, kaynak grubu veya Service Bus ad alanı kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya Azure kaynakları için yönetilen bir kimlik olabilir.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure Service Bus için yerleşik RBAC rolleri
Azure Service Bus için ad alanları ve tüm ilgili kaynakların Azure portal ve Azure Kaynak yönetimi API 'SI aracılığıyla yönetimi, *rol tabanlı erişim denetimi* (RBAC) modeli kullanılarak zaten korunuyor. Azure, bir Service Bus ad alanına erişim yetkilendirmek için aşağıdaki yerleşik RBAC rollerini sağlar:

- [Veri sahibi Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Service Bus ad alanına ve varlıklarına veri erişimi sağlar (kuyruklar, konular, abonelikler ve filtreler)
- [Veri göndereni Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Service Bus ad alanına ve varlıklarına gönderme erişimi sağlamak için bu rolü kullanın.
- [Azure Service Bus veri alıcısı](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Service Bus ad alanına ve varlıklarına erişim sağlamak için bu rolü kullanın. 

## <a name="resource-scope"></a>Kaynak kapsamı 
Bir güvenlik sorumlusuna RBAC rolü atamadan önce, güvenlik sorumlusunun sahip olması gereken erişimin kapsamını saptayın. En iyi uygulamalar, yalnızca en dar olası kapsamı sağlamak için her zaman en iyi seçenektir.

Aşağıdaki listede, en dar kapsamdan başlayarak Service Bus kaynaklarına erişimi kapsamındaki düzeyler açıklanmaktadır:

- **Kuyruk**, **Konu**veya **abonelik**: rol ataması, belirli Service Bus varlığı için geçerlidir. Şu anda Azure portal, Kullanıcı/Grup/yönetilen kimliklerin abonelik düzeyindeki RBAC rollerinin Service Bus atanmasını desteklemez. İşte bir Service Bus RBAC rolüne kimlik atamak için, [az-role-atama-Create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) komutunu kullanın: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus ad alanı**: rol ataması, ad alanı altındaki tüm Service Bus topolojisini ve onunla ilişkili tüketici grubunu kapsar.
- **Kaynak grubu**: rol atama, kaynak grubu altındaki tüm Service Bus kaynaklarına uygulanır.
- **Abonelik**: rol ataması, abonelikteki tüm kaynak gruplarındaki tüm Service Bus kaynaklara uygulanır.

> [!NOTE]
> RBAC rol atamalarının yaymanın beş dakika sürebileceğini aklınızda bulundurun. 

Yerleşik rollerin nasıl tanımlandığı hakkında daha fazla bilgi için bkz. [rol tanımlarını anlama](../role-based-access-control/role-definitions.md#management-and-data-operations). Özel RBAC rolleri oluşturma hakkında daha fazla bilgi için bkz. [Azure rol tabanlı Access Control için özel roller oluşturma](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>VM 'de yönetilen kimlikleri etkinleştirme
VM 'nizden Service Bus kaynaklarını yetkilendirmek üzere Azure kaynakları için Yönetilen kimlikler kullanabilmeniz için önce VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirmeniz gerekir. Azure kaynakları için yönetilen kimliklerin nasıl etkinleştireceğinizi öğrenmek için şu makalelerden birine bakın:

- [Azure portalda](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonu](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager istemci kitaplıkları](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD 'de yönetilen bir kimliğe izin verme
Uygulamanızdaki yönetilen bir kimlikle Service Bus hizmetine bir istek yetkilendirmek için, önce bu yönetilen kimlik için rol tabanlı erişim denetimi (RBAC) ayarlarını yapılandırın. Azure Service Bus Service Bus gönderme ve okuma izinlerini çevreleyen RBAC rollerini tanımlar. RBAC rolü yönetilen bir kimliğe atandığında, yönetilen kimliğe uygun kapsamda Service Bus varlıklara erişim verilir.

RBAC rolleri atama hakkında daha fazla bilgi için bkz. [Service Bus kaynaklara erişim için Azure Active Directory kimlik doğrulaması ve yetkilendirme](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimliklerle Service Bus kullanma
Yönetilen kimliklerle Service Bus kullanmak için, rol ve uygun kapsam kimliğini atamanız gerekir. Bu bölümdeki yordam, yönetilen bir kimlik altında çalışan ve Service Bus kaynaklara erişen basit bir uygulama kullanır.

Burada [Azure App Service](https://azure.microsoft.com/services/app-service/)' de barındırılan örnek bir Web uygulaması kullanıyoruz. Web uygulaması oluşturmaya yönelik adım adım yönergeler için bkz. [Azure 'da ASP.NET Core Web uygulaması oluşturma](../app-service/app-service-web-get-started-dotnet.md)

Uygulama oluşturulduktan sonra aşağıdaki adımları izleyin: 

1. **Ayarlar** ' a gidin ve **kimlik**' i seçin. 
1. Görüntülenecek **durumu** **seçin.** 
1. Ayarları kaydetmek için **Kaydet**’i seçin. 

    ![Bir Web uygulaması için yönetilen kimlik](./media/service-bus-managed-service-identity/identity-web-app.png)

Bu ayarı etkinleştirdikten sonra, Azure Active Directory (Azure AD) ve App Service ana bilgisayarında yapılandırılmış yeni bir hizmet kimliği oluşturulur.

Şimdi, bu hizmet kimliğini Service Bus kaynaklarınızın gerekli kapsamındaki bir role atayın.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Azure portal kullanarak RBAC rolleri atamak için
Bir Service Bus ad alanına rol atamak için Azure portal ad alanına gidin. Kaynak için Access Control (ıAM) ayarlarını görüntüleyin ve rol atamalarını yönetmek için aşağıdaki yönergeleri izleyin:

> [!NOTE]
> Aşağıdaki adımlar Service Bus ad alanlarınıza bir hizmet kimliği rolü atar. Aynı adımları izleyerek, desteklenen diğer kapsamlardaki (kaynak grubu ve abonelik) bir rol atayabilirsiniz. 
> 
> Bir [Service Bus mesajlaşma ad alanı oluşturun](service-bus-create-namespace-portal.md) . 

1. Azure portal, Service Bus ad alanına gidin ve ad alanı için **genel bakış** ' ı görüntüleyin. 
1. Service Bus ad alanı için erişim denetimi ayarlarını göstermek için sol taraftaki menüden **Access Control (IAM)** seçeneğini belirleyin.
1.  Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin.
3.  Yeni bir rol eklemek için **Ekle** ' yi seçin.
4.  **Rol ataması Ekle** sayfasında, atamak istediğiniz Azure Service Bus rolleri seçin. Ardından, rolü atamak için kaydettiğiniz hizmet kimliğini bulmak için arama yapın.
    
    ![Rol atama sayfası ekle](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  **Kaydet**’i seçin. Rolü atadığınız kimlik söz konusu rol altında listelenir. Örneğin, aşağıdaki görüntüde, hizmet kimliğinin Azure Service Bus veri sahibi olduğunu gösterilmektedir.
    
    ![Bir role atanan kimlik](./media/service-bus-managed-service-identity/role-assigned.png)

Rolü atadıktan sonra, Web uygulamasının tanımlı kapsamda Service Bus varlıklara erişimi olur. 

### <a name="run-the-app"></a>Uygulamayı çalıştırma

Şimdi, oluşturduğunuz ASP.NET uygulamasının varsayılan sayfasını değiştirin. [Bu GitHub deposundan](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)Web uygulaması kodunu kullanabilirsiniz.  

Varsayılan. aspx sayfası, giriş sayfanız. Kodu Default.aspx.cs dosyasında bulabilirsiniz. Sonuç olarak, birkaç giriş alanı olan en az bir Web uygulaması ve ileti göndermek ya da almak için Service Bus bağlanan **Gönder** ve **Al** düğmeleri vardır.

[Messagingfactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) nesnesinin nasıl başlatıldığını aklınızda edin. Kod, paylaşılan erişim belirteci (SAS) belirteci sağlayıcısını kullanmak yerine, yönetilen kimlik için `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` çağrısıyla bir belirteç sağlayıcısı oluşturur. Bu nedenle, tutulacak ve kullanılacak gizli dizi yok. Yönetilen kimlik bağlamının Service Bus ve yetkilendirme el sıkışması akışı, otomatik olarak belirteç sağlayıcısı tarafından işlenir. SAS kullanmaktan daha basit bir modeldir.

Bu değişiklikleri yaptıktan sonra, uygulamayı yayımlayın ve çalıştırın. Visual Studio 'da bir yayımlama profilini indirerek ve içeri aktararak doğru yayımlama verilerini kolayca edinebilirsiniz:

![Yayımlama profili al](./media/service-bus-managed-service-identity/msi3.png)
 
İleti göndermek veya almak için, ad alanının adını ve oluşturduğunuz varlığın adını girin. Ardından, **Gönder** veya **Al**' a tıklayın.


> [!NOTE]
> - Yönetilen kimlik yalnızca Azure ortamında, App Services, Azure VM 'Lerde ve ölçek kümelerinde kullanılabilir. Service Bus NuGet paketi tarafından kullanılan Microsoft. Azure. Services. AppAuthentication kitaplığı, .NET uygulamaları için, bu protokol üzerinden bir soyutlama sağlar ve yerel bir geliştirme deneyimini destekler. Bu kitaplık Ayrıca, Visual Studio, Azure CLı 2,0 veya tümleşik kimlik doğrulaması Active Directory kullanıcı hesabınızı kullanarak kodunuzu geliştirme makinenizde yerel olarak test etmenizi sağlar. Bu kitaplıkla ilgili yerel geliştirme seçenekleri hakkında daha fazla bilgi için bkz. [.NET kullanarak Azure Key Vault Için hizmetten hizmete kimlik doğrulaması](../key-vault/service-to-service-authentication.md).  
> 
> - Şu anda yönetilen kimlikler App Service dağıtım yuvaları ile çalışmaz.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)
