---
title: Azure Service Bus varlıklara erişmek için bir uygulamanın kimliğini doğrulama
description: Bu makalede, Azure Service Bus varlıklara (kuyruklar, konular vb.) erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama hakkında bilgi sağlanır.
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997000"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Azure Service Bus varlıklara erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama ve yetkilendirme
Azure Service Bus, isteklere Service Bus varlıklara (kuyruklar, konular, abonelikler veya filtreler) yetki vermek için Azure Active Directory (Azure AD) kullanılmasını destekler. Azure AD ile rol tabanlı erişim denetimi 'ni (RBAC), bir Kullanıcı, Grup veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusuna izinler vermek için kullanabilirsiniz. Roller ve rol atamaları hakkında daha fazla bilgi edinmek için bkz. [farklı rolleri anlama](../role-based-access-control/overview.md).

## <a name="overview"></a>Genel Bakış
Bir güvenlik sorumlusu (Kullanıcı, Grup veya uygulama) bir Service Bus varlığına erişmeyi denediğinde, isteğin yetkilendirilmiş olması gerekir. Azure AD ile bir kaynağa erişim iki adımlı bir işlemdir. 

 1. İlk olarak, güvenlik sorumlusunun kimliği doğrulanır ve bir OAuth 2,0 belirteci döndürülür. Belirteç istemek için kaynak adı `https://servicebus.azure.net`.
 1. Ardından, belirteç, belirtilen kaynağa erişim yetkisi vermek için Service Bus hizmetine bir isteğin bir parçası olarak geçirilir.

Kimlik doğrulama adımı, bir uygulama isteğinin çalışma zamanında bir OAuth 2,0 erişim belirteci içermesi gerekir. Bir uygulama bir Azure VM 'si, bir sanal makine ölçek kümesi veya bir Azure Işlev uygulaması gibi bir Azure varlığı içinde çalışıyorsa, kaynaklara erişmek için yönetilen bir kimlik kullanabilir. Yönetilen bir kimlik tarafından Service Bus hizmetine yapılan isteklerin nasıl doğrulanabilmesi hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile Azure Service Bus kaynaklarına erişim kimlik doğrulaması](service-bus-managed-service-identity.md). 

Yetkilendirme adımı, güvenlik sorumlusuna bir veya daha fazla RBAC rolünün atanmasını gerektirir. Azure Service Bus, Service Bus kaynakları için izin kümelerini çevreleyen RBAC rolleri sağlar. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. Azure Service Bus için RBAC rolleri atama hakkında daha fazla bilgi için, bkz. [Azure Service Bus Için YERLEŞIK RBAC rolleri](#built-in-rbac-roles-for-azure-service-bus). 

Service Bus istek yapan yerel uygulamalar ve Web uygulamaları Azure AD ile de yetki verebilir. Bu makalede, bir erişim belirtecinin nasıl isteneceğini ve Service Bus kaynakları için istekleri yetkilendirmek üzere nasıl kullanılacağı gösterilmektedir. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Erişim hakları için RBAC rolleri atama
Azure Active Directory (Azure AD), [rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim haklarını yetkilendirir. Azure Service Bus, Service Bus varlıklara erişmek için kullanılan ortak izin kümelerini çevreleyen yerleşik RBAC rollerinin bir kümesini tanımlar ve verilere erişmek için özel roller de tanımlayabilir.

Azure AD güvenlik sorumlusuna bir RBAC rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, abonelik düzeyi, kaynak grubu veya Service Bus ad alanı kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md)olabilir.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure Service Bus için yerleşik RBAC rolleri
Azure Service Bus için ad alanları ve tüm ilgili kaynakların Azure portal ve Azure Kaynak yönetimi API 'SI aracılığıyla yönetimi, *rol tabanlı erişim denetimi* (RBAC) modeli kullanılarak zaten korunuyor. Azure, bir Service Bus ad alanına erişim yetkilendirmek için aşağıdaki yerleşik RBAC rollerini sağlar:

- [Azure Service Bus veri sahibi](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Service Bus ad alanına ve varlıklarına veri erişimi sağlar (kuyruklar, konular, abonelikler ve filtreler)
- [Veri Göndericisini Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Service Bus ad alanına ve varlıklarına gönderme erişimi vermek için bu rolü kullanın.
- [Azure Service Bus veri alıcısı](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Service Bus ad alanına ve varlıklarına erişim sağlamak için bu rolü kullanın. 

## <a name="resource-scope"></a>Kaynak kapsamı 
Bir güvenlik sorumlusuna RBAC rolü atamadan önce, güvenlik sorumlusunun sahip olması gereken erişimin kapsamını saptayın. En iyi uygulamalar, yalnızca en dar olası kapsamı sağlamak için her zaman en iyi seçenektir.

Aşağıdaki listede, en dar kapsamdan başlayarak Service Bus kaynaklarına erişimi kapsamındaki düzeyler açıklanmaktadır:

- **Kuyruk**, **Konu**veya **abonelik**: Rol ataması, belirli Service Bus varlığı için geçerlidir. Şu anda Azure portal, Kullanıcı/Grup/yönetilen kimliklerin abonelik düzeyindeki RBAC rollerinin Service Bus atanmasını desteklemez. 
- **Service Bus ad alanı**: Rol ataması, ad alanı altındaki tüm Service Bus topolojisine ve onunla ilişkili tüketici grubuna yayılır.
- **Kaynak grubu**: Rol ataması, kaynak grubu altındaki tüm Service Bus kaynaklarına uygulanır.
- **Abonelik**: Rol ataması, abonelikteki tüm kaynak gruplarındaki tüm Service Bus kaynaklara uygulanır.

> [!NOTE]
> RBAC rol atamalarının yaymanın beş dakika sürebileceğini aklınızda bulundurun. 

Yerleşik rollerin nasıl tanımlandığı hakkında daha fazla bilgi için bkz. [rol tanımlarını anlama](../role-based-access-control/role-definitions.md#management-and-data-operations). Özel RBAC rolleri oluşturma hakkında daha fazla bilgi için bkz. [Azure rol tabanlı Access Control için özel roller oluşturma](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure portal kullanarak RBAC rolleri atama  
RBAC ve Azure portal kullanarak Azure kaynaklarına erişimi yönetme hakkında daha fazla bilgi edinmek için [Bu makaleye](..//role-based-access-control/role-assignments-portal.md)bakın. 

Bir rol ataması için uygun kapsamı belirledikten sonra, Azure portal bu kaynağa gidin. Kaynak için erişim denetimi (ıAM) ayarlarını görüntüleyin ve rol atamalarını yönetmek için aşağıdaki yönergeleri izleyin:

> [!NOTE]
> Aşağıda açıklanan adımlar Service Bus ad alanına bir rol atar. Desteklenen diğer kapsamlara (kaynak grubu, abonelik vb.) rol atamak için aynı adımları izleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com/), Service Bus ad alanına gidin. Ad alanı için erişim denetimi ayarlarını göstermek için sol taraftaki menüden **Access Control (IAM)** seçeneğini belirleyin. Bir Service Bus ad alanı oluşturmanız gerekiyorsa, bu makaledeki yönergeleri izleyin: [Service Bus mesajlaşma ad alanı oluşturun](service-bus-create-namespace-portal.md).

    ![Sol menüdeki Access Control seçin](./media/authenticate-application/select-access-control-menu.png)
1. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin. Araç çubuğunda **Ekle** düğmesini seçin ve ardından **rol ataması Ekle**' yi seçin. 

    ![Araç çubuğuna düğme Ekle](./media/authenticate-application/role-assignments-add-button.png)
1. **Rol ataması Ekle** sayfasında, aşağıdaki adımları uygulayın:
    1. Atamak istediğiniz **Service Bus rolünü** seçin. 
    1. Rolü atamak istediğiniz **güvenlik sorumlusunu** (Kullanıcı, Grup, hizmet sorumlusu) bulmak için arama yapın.
    1. Rol atamasını kaydetmek için **Kaydet** ' i seçin. 

        ![Bir kullanıcıya rol atama](./media/authenticate-application/assign-role-to-user.png)
    4. Rolü atadığınız kimlik söz konusu rol altında listelenir. Örneğin, aşağıdaki görüntüde Azure-Users 'ın Azure Service Bus veri sahibi rolünde olduğu gösterilmektedir. 
        
        ![Listedeki Kullanıcı](./media/authenticate-application/user-in-list.png)

Bir kaynak grubuna veya aboneliğe kapsamlı bir rol atamak için de benzer adımları izleyebilirsiniz. Rolü ve kapsamını tanımladıktan sonra bu davranışı [GitHub 'daki örneklerle](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)test edebilirsiniz.


## <a name="authenticate-from-an-application"></a>Uygulamadan kimlik doğrulaması yapma
Azure AD 'yi Service Bus kullanmanın önemli bir avantajı, kimlik bilgilerinizin artık kodunuzda depolanmasına gerek kalmaz. Bunun yerine, Microsoft Identity platform 'dan bir OAuth 2,0 erişim belirteci isteyebilirsiniz. Azure AD, uygulamayı çalıştıran güvenlik sorumlusunun (bir Kullanıcı, Grup veya hizmet sorumlusu) kimliğini doğrular. Kimlik doğrulaması başarılı olursa, Azure AD uygulamaya erişim belirtecini döndürür ve uygulama, istekleri Azure Service Bus yetkilendirmek için erişim belirtecini kullanabilir.

Aşağıdaki bölümlerde, Microsoft Identity Platform 2,0 ile yerel uygulamanızı veya Web uygulamanızı kimlik doğrulaması için nasıl yapılandıracağınız gösterilmektedir. Microsoft Identity Platform 2,0 hakkında daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) genel bakış](../active-directory/develop/v2-overview.md).

OAuth 2.0 kodu verme akışı genel bakış için bkz: [Authorize OAuth 2.0 kod kullanarak Azure Active Directory web uygulamalarına erişim akışı](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD kiracısı ile uygulamanızı kaydetme
Service Bus varlıkları yetkilendirmek için Azure AD kullanmanın ilk adımı, istemci uygulamanızı [Azure Portal](https://portal.azure.com/)BIR Azure AD kiracısıyla kaydetmekte. İstemci uygulamanızı kaydettiğinizde AD 'ye uygulama hakkında bilgi sağlarsınız. Daha sonra Azure AD, uygulamanızı Azure AD çalışma zamanı ile ilişkilendirmek için kullanabileceğiniz bir istemci KIMLIĞI (uygulama KIMLIĞI olarak da bilinir) sağlar. İstemci kimliği hakkında daha fazla bilgi için bkz: [uygulaması ve Azure Active Directory'de Hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md). 

Aşağıdaki görüntüler, bir Web uygulamasını kaydetme adımlarını göstermektedir:

![Bir uygulamayı kaydet](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Uygulamanızı yerel bir uygulama olarak kaydettiğinizde, yeniden yönlendirme URI 'SI için geçerli bir URI belirtebilirsiniz. Yerel uygulamalar için, bu değerin gerçek bir URL olması gerekmez. Web uygulamaları için, yeniden yönlendirme URI 'si, belirteçlerin sağlandığı URL 'YI belirttiğinden geçerli bir URI olmalıdır.

Uygulamanızı kaydettikten sonra **Ayarlar**altında **uygulama (istemci) kimliğini** görürsünüz:

![Kayıtlı uygulamanın uygulama KIMLIĞI](./media/authenticate-application/application-id.png)

Bir uygulamayı Azure AD'ye kaydetme hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory ile tümleştirme](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> **Tenantıd** ve **ApplicationId**' i unutmayın. Uygulamayı çalıştırmak için bu değerlere ihtiyacınız olacak.

### <a name="create-a-client-secret"></a>İstemci parolası oluşturma   
Uygulamanın bir belirteç istenirken kimliğini kanıtlamak için bir istemci parolası gerekir. İstemci parolasını eklemek için aşağıdaki adımları izleyin.

1. Zaten sayfada değilseniz Azure portal uygulama kaydınız ' ne gidin.
1. Sol menüdeki **sertifikalar & gizli** dizileri ' ni seçin.
1. **İstemci**gizli dizileri altında yeni bir gizli dizi oluşturmak için **yeni istemci parolası** ' nı seçin.

    ![Yeni istemci gizli anahtarı-düğme](./media/authenticate-application/new-client-secret-button.png)
1. Gizli dizi için bir açıklama girin ve istenen süre sonu aralığını seçin ve ardından **Ekle**' yi seçin.

    ![İstemci gizli dizisi Ekle sayfası](./media/authenticate-application/add-client-secret-page.png)
1. Yeni Gizliliğin değerini hemen güvenli bir konuma kopyalayın. Fill değeri size yalnızca bir kez görüntülenir.

    ![İstemci parolası](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Service Bus API izinleri
Uygulamanız bir konsol uygulaması ise, yerel bir uygulamayı kaydetmeniz ve **gerekli izinler** kümesine **Microsoft. SERVICEBUS** için API izinleri eklemeniz gerekir. Yerel uygulamalar, Azure AD 'de bir tanımlayıcı görevi gören bir **yeniden yönlendirme URI 'si** de gerektirir; URI 'nin bir ağ hedefi olması gerekmez. Kullanım `https://servicebus.microsoft.com` kod örneği olduğundan bu örnek için bu URI kullanır.

### <a name="client-libraries-for-token-acquisition"></a>Belirteç alımı için istemci kitaplıkları  
Uygulamanızı kaydettikten ve Azure Service Bus veri gönderme/alma izinlerine sahip olduktan sonra, bir güvenlik sorumlusunun kimliğini doğrulamak ve OAuth 2,0 belirtecini almak için uygulamanıza kod ekleyebilirsiniz. Belirtecin kimliğini doğrulamak ve almak için, [Microsoft Identity platform kimlik doğrulama kitaplıklarından](../active-directory/develop/reference-v2-libraries.md) birini veya OpenID veya Connect 1,0 ' i destekleyen başka bir açık kaynak kitaplığı kullanabilirsiniz. Uygulamanız daha sonra, Azure Service Bus karşı bir istek yetkilendirmek için erişim belirtecini kullanabilir.

Belirteçleri alma için desteklenen senaryoların listesi için, .NET GitHub deposu [Için Microsoft kimlik doğrulama kitaplığı 'nın (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [senaryolar](https://aka.ms/msal-net-scenarios) bölümüne bakın.

## <a name="sample-on-github"></a>GitHub üzerinde örnek
GitHub 'da aşağıdaki örneğe bakın: [Service Bus Için rol tabanlı erişim denetimi](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

**Etkileşimli kullanıcı oturum açma** seçeneğini değil, **istemci gizli anahtarı** seçeneğini kullanın. İstemci parolası seçeneğini kullandığınızda, bir açılır pencere görmezsiniz. Uygulama kimlik doğrulaması için kiracı KIMLIĞINI ve uygulama KIMLIĞINI kullanır. 

### <a name="run-the-sample"></a>Örneği çalıştırma

Örneği çalıştırmadan önce, **app. config** dosyasını düzenleyin ve senaryonuza bağlı olarak aşağıdaki değerleri ayarlayın:

- `tenantId`: **Tenantıd** değeri olarak ayarlayın.
- `clientId`: **ApplicationId** değerine ayarlanır.
- `clientSecret`: İstemci parolasını kullanarak oturum açmak istiyorsanız, Azure AD 'de oluşturun. Ayrıca, bir web uygulaması veya API yerine yerel bir uygulama kullanın. Ayrıca, altında uygulama Ekle **erişim denetimi (IAM)** daha önce oluşturduğunuz ad alanı içinde.
- `serviceBusNamespaceFQDN`: Yeni oluşturulan Service Bus ad alanının tam DNS adına ayarlanır; Örneğin, `example.servicebus.windows.net`.
- `queueName`: Oluşturduğunuz sıranın adına ayarlanır.
- Uygulamanıza, önceki adımlarda belirtilen yeniden yönlendirme URI'si.

Konsol uygulamasını çalıştırdığınızda, bir senaryo seçmeniz istenir. Kendi numarasını yazıp ENTER tuşuna basarak **etkileşimli kullanıcı oturumu açma** ' yı seçin. Uygulama bir oturum açma penceresi görüntüler, onayınızı Service Bus erişmeyi ister ve sonra oturum açma kimliğini kullanarak gönderme/alma senaryosunda çalıştırmak için hizmeti kullanır.


## <a name="next-steps"></a>Sonraki adımlar
- RBAC hakkında daha fazla bilgi edinmek için bkz. [rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md)nedir?
- Azure PowerShell, Azure CLı veya REST API ile RBAC rol atamalarını atamayı ve yönetmeyi öğrenmek için şu makalelere bakın:
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure PowerShell ile yönetme](../role-based-access-control/role-assignments-powershell.md)  
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure CLı ile yönetme](../role-based-access-control/role-assignments-cli.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) REST API ile yönetme](../role-based-access-control/role-assignments-rest.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure Resource Manager şablonlarıyla yönetme](../role-based-access-control/role-assignments-template.md)

Service Bus mesajlaşma hizmeti hakkında daha fazla bilgi edinmek için aşağıdaki konu başlıklarına bakın.

- [RBAC örneklerini Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
- [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)
