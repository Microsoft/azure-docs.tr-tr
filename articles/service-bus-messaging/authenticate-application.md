---
title: Azure Service Bus varlıklara erişmek için bir uygulamanın kimliğini doğrulama
description: Bu makalede, Azure Service Bus varlıklara (kuyruklar, konular vb.) erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama hakkında bilgi sağlanır.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518237"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Azure Service Bus varlıklara erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama ve yetkilendirme
Azure Service Bus, isteklere Service Bus varlıklara (kuyruklar, konular, abonelikler veya filtreler) yetki vermek için Azure Active Directory (Azure AD) kullanılmasını destekler. Azure AD ile, bir Kullanıcı, Grup veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusu için izinler vermek üzere Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz. Roller ve rol atamaları hakkında daha fazla bilgi edinmek için bkz. [farklı rolleri anlama](../role-based-access-control/overview.md).

## <a name="overview"></a>Genel Bakış
Bir güvenlik sorumlusu (Kullanıcı, Grup veya uygulama) bir Service Bus varlığına erişmeyi denediğinde, isteğin yetkilendirilmiş olması gerekir. Azure AD ile bir kaynağa erişim iki adımlı bir işlemdir. 

 1. İlk olarak, güvenlik sorumlusunun kimliği doğrulanır ve bir OAuth 2,0 belirteci döndürülür. Belirteç istemek için kaynak adı `https://servicebus.azure.net` .
 1. Ardından, belirteç, belirtilen kaynağa erişim yetkisi vermek için Service Bus hizmetine bir isteğin bir parçası olarak geçirilir.

Kimlik doğrulama adımı, bir uygulama isteğinin çalışma zamanında bir OAuth 2,0 erişim belirteci içermesi gerekir. Bir uygulama bir Azure VM 'si, bir sanal makine ölçek kümesi veya bir Azure Işlev uygulaması gibi bir Azure varlığı içinde çalışıyorsa, kaynaklara erişmek için yönetilen bir kimlik kullanabilir. Yönetilen bir kimlik tarafından Service Bus hizmetine yapılan isteklerin nasıl doğrulanabilmesi hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile Azure Service Bus kaynaklarına erişim kimlik doğrulaması](service-bus-managed-service-identity.md). 

Yetkilendirme adımı, güvenlik sorumlusuna bir veya daha fazla Azure rolünün atanmasını gerektirir. Azure Service Bus, Service Bus kaynakları için izin kümelerini çevreleyen Azure rolleri sağlar. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. Azure Service Bus için Azure rolleri atama hakkında daha fazla bilgi için bkz. [Azure yerleşik rolleri Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Service Bus istek yapan yerel uygulamalar ve Web uygulamaları Azure AD ile de yetki verebilir. Bu makalede, bir erişim belirtecinin nasıl isteneceğini ve Service Bus kaynakları için istekleri yetkilendirmek üzere nasıl kullanılacağı gösterilmektedir. 


## <a name="assigning-azure-roles-for-access-rights"></a>Erişim hakları için Azure rolleri atama
Azure Active Directory (Azure AD), [Azure RBAC](../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim hakları verir. Azure Service Bus, Service Bus varlıklara erişmek için kullanılan ortak izin kümelerini çevreleyen Azure yerleşik rollerinin bir kümesini tanımlar ve verilere erişmek için özel roller de tanımlayabilirsiniz.

Azure AD güvenlik sorumlusuna bir Azure rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, abonelik düzeyi, kaynak grubu veya Service Bus ad alanı kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md)olabilir.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure Service Bus için Azure yerleşik rolleri
Azure Service Bus için ad alanları ve tüm ilgili kaynaklar Azure portal ve Azure Kaynak yönetimi API 'SI aracılığıyla Azure RBAC modeli kullanılarak zaten korunuyor. Azure, bir Service Bus ad alanına erişim yetkilendirmek için aşağıdaki Azure yerleşik rollerini sağlar:

- [Veri sahibi Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Service Bus ad alanına ve varlıklarına veri erişimi sağlar (kuyruklar, konular, abonelikler ve filtreler)
- [Veri göndereni Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Service Bus ad alanına ve varlıklarına gönderme erişimi sağlamak için bu rolü kullanın.
- [Azure Service Bus veri alıcısı](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Service Bus ad alanına ve varlıklarına erişim sağlamak için bu rolü kullanın. 

## <a name="resource-scope"></a>Kaynak kapsamı 
Güvenlik sorumlusuna bir Azure rolü atamadan önce, güvenlik sorumlusunun sahip olması gereken erişimin kapsamını saptayın. En iyi uygulamalar, yalnızca en dar olası kapsamı sağlamak için her zaman en iyi seçenektir.

Aşağıdaki listede, en dar kapsamdan başlayarak Service Bus kaynaklarına erişimi kapsamındaki düzeyler açıklanmaktadır:

- **Kuyruk**, **Konu** veya **abonelik**: rol ataması, belirli Service Bus varlığı için geçerlidir. Şu anda Azure portal, Kullanıcı/Grup/yönetilen kimliklerin abonelik düzeyinde Service Bus Azure rollerine atanmasını desteklemez. 
- **Service Bus ad alanı**: rol ataması, ad alanı altındaki tüm Service Bus topolojisini ve onunla ilişkili tüketici grubunu kapsar.
- **Kaynak grubu**: rol atama, kaynak grubu altındaki tüm Service Bus kaynaklarına uygulanır.
- **Abonelik**: rol ataması, abonelikteki tüm kaynak gruplarındaki tüm Service Bus kaynaklara uygulanır.

> [!NOTE]
> Azure rol atamalarının yaymanın beş dakika sürebileceğini aklınızda bulundurun. 

Yerleşik rollerin nasıl tanımlandığı hakkında daha fazla bilgi için bkz. [rol tanımlarını anlama](../role-based-access-control/role-definitions.md#management-and-data-operations). Azure özel rolleri oluşturma hakkında daha fazla bilgi için bkz. [Azure özel roller](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal kullanarak Azure rolleri atama  
Azure RBAC ve Azure portal kullanarak Azure kaynaklarına erişimi yönetme hakkında daha fazla bilgi edinmek için [Bu makaleye](..//role-based-access-control/role-assignments-portal.md)bakın. 

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

OAuth 2,0 kod verme akışına genel bakış için bkz. [oauth 2,0 kod verme akışını kullanarak Azure Active Directory Web uygulamalarına erişimi yetkilendirme](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Uygulamanızı bir Azure AD kiracısıyla kaydetme
Service Bus varlıkları yetkilendirmek için Azure AD kullanmanın ilk adımı, istemci uygulamanızı [Azure Portal](https://portal.azure.com/)BIR Azure AD kiracısıyla kaydetmekte. İstemci uygulamanızı kaydettiğinizde AD 'ye uygulama hakkında bilgi sağlarsınız. Daha sonra Azure AD, uygulamanızı Azure AD çalışma zamanı ile ilişkilendirmek için kullanabileceğiniz bir istemci KIMLIĞI (uygulama KIMLIĞI olarak da bilinir) sağlar. İstemci KIMLIĞI hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın. 

Aşağıdaki görüntüler, bir Web uygulamasını kaydetme adımlarını göstermektedir:

![Bir uygulamayı kaydetme](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Uygulamanızı yerel bir uygulama olarak kaydettiğinizde, yeniden yönlendirme URI 'SI için geçerli bir URI belirtebilirsiniz. Yerel uygulamalar için, bu değerin gerçek bir URL olması gerekmez. Web uygulamaları için, yeniden yönlendirme URI 'si, belirteçlerin sağlandığı URL 'YI belirttiğinden geçerli bir URI olmalıdır.

Uygulamanızı kaydettikten sonra **Ayarlar** altında **uygulama (istemci) kimliğini** görürsünüz:

![Kayıtlı uygulamanın uygulama KIMLIĞI](./media/authenticate-application/application-id.png)

Bir uygulamayı Azure AD 'ye kaydetme hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> **Tenantıd** ve **ApplicationId**' i unutmayın. Uygulamayı çalıştırmak için bu değerlere ihtiyacınız olacak.

### <a name="create-a-client-secret"></a>İstemci parolası oluşturma   
Uygulamanın bir belirteç istenirken kimliğini kanıtlamak için bir istemci parolası gerekir. İstemci parolasını eklemek için aşağıdaki adımları izleyin.

1. Zaten sayfada değilseniz Azure portal uygulama kaydınız ' ne gidin.
1. Sol menüdeki **sertifikalar & gizli** dizileri ' ni seçin.
1. **İstemci** gizli dizileri altında yeni bir gizli dizi oluşturmak için **yeni istemci parolası** ' nı seçin.

    ![Yeni istemci gizli anahtarı-düğme](./media/authenticate-application/new-client-secret-button.png)
1. Gizli dizi için bir açıklama girin ve istenen süre sonu aralığını seçin ve ardından **Ekle**' yi seçin.

    ![İstemci gizli dizisi Ekle sayfası](./media/authenticate-application/add-client-secret-page.png)
1. Yeni Gizliliğin değerini hemen güvenli bir konuma kopyalayın. Fill değeri size yalnızca bir kez görüntülenir.

    ![Gizli anahtar](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Service Bus API izinleri
Uygulamanız bir konsol uygulaması ise, yerel bir uygulamayı kaydetmeniz ve **gerekli izinler** kümesine **Microsoft. SERVICEBUS** için API izinleri eklemeniz gerekir. Yerel uygulamalar, Azure AD 'de bir tanımlayıcı görevi gören bir **yeniden yönlendirme URI 'si** de gerektirir; URI 'nin bir ağ hedefi olması gerekmez. `https://servicebus.microsoft.com`Örnek kod bu URI 'yi zaten kullandığından, bu örnek için kullanın.

### <a name="client-libraries-for-token-acquisition"></a>Belirteç alımı için istemci kitaplıkları  
Uygulamanızı kaydettikten ve Azure Service Bus veri gönderme/alma izinlerine sahip olduktan sonra, bir güvenlik sorumlusunun kimliğini doğrulamak ve OAuth 2,0 belirtecini almak için uygulamanıza kod ekleyebilirsiniz. Belirtecin kimliğini doğrulamak ve almak için, [Microsoft Identity platform kimlik doğrulama kitaplıklarından](../active-directory/develop/reference-v2-libraries.md) birini veya OpenID veya Connect 1,0 ' i destekleyen başka bir açık kaynak kitaplığı kullanabilirsiniz. Uygulamanız daha sonra, Azure Service Bus karşı bir istek yetkilendirmek için erişim belirtecini kullanabilir.

Belirteçleri alma için desteklenen senaryoların listesi için, .NET GitHub deposu [Için Microsoft kimlik doğrulama kitaplığı 'nın (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [senaryolar](https://aka.ms/msal-net-scenarios) bölümüne bakın.

## <a name="sample-on-github"></a>GitHub üzerinde örnek
GitHub 'da aşağıdaki örneğe bakın: [Service Bus Için Azure rol tabanlı erişim denetimi](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

**Etkileşimli kullanıcı oturum açma** seçeneğini değil, **istemci gizli anahtarı** seçeneğini kullanın. İstemci parolası seçeneğini kullandığınızda, bir açılır pencere görmezsiniz. Uygulama kimlik doğrulaması için kiracı KIMLIĞINI ve uygulama KIMLIĞINI kullanır. 

### <a name="run-the-sample"></a>Örneği çalıştırma

Örneği çalıştırmadan önce, **app.config** dosyasını düzenleyin ve senaryonuza bağlı olarak aşağıdaki değerleri ayarlayın:

- `tenantId`: **Tenantıd** değerine ayarlanır.
- `clientId`: **ApplicationId** değerine ayarlanır.
- `clientSecret`: İstemci parolasını kullanarak oturum açmak istiyorsanız, Azure AD 'de oluşturun. Ayrıca, yerel uygulama yerine bir Web uygulaması veya API kullanın. Ayrıca, daha önce oluşturduğunuz ad alanındaki **Access Control (IAM)** altına uygulamayı ekleyin.
- `serviceBusNamespaceFQDN`: Yeni oluşturulan Service Bus ad alanının tam DNS adına ayarlanır; Örneğin, `example.servicebus.windows.net` .
- `queueName`: Oluşturduğunuz kuyruğun adına ayarlanır.
- Önceki adımlarda uygulamanızda belirttiğiniz yeniden yönlendirme URI 'SI.

Konsol uygulamasını çalıştırdığınızda, bir senaryo seçmeniz istenir. Kendi numarasını yazıp ENTER tuşuna basarak **etkileşimli kullanıcı oturumu açma** ' yı seçin. Uygulama bir oturum açma penceresi görüntüler, onayınızı Service Bus erişmeyi ister ve sonra oturum açma kimliğini kullanarak gönderme/alma senaryosunda çalıştırmak için hizmeti kullanır.


## <a name="next-steps"></a>Sonraki adımlar
- Azure RBAC hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)nedir?
- Azure rol atamalarını Azure PowerShell, Azure CLı veya REST API ile atamayı ve yönetmeyi öğrenmek için şu makalelere bakın:
    - [Azure PowerShell kullanarak Azure rol atamalarını ekleme veya kaldırma](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI kullanarak Azure rol atamalarını ekleme veya kaldırma](../role-based-access-control/role-assignments-cli.md)
    - [REST API kullanarak Azure rol atamalarını ekleme veya kaldırma](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager şablonları kullanarak Azure rol atamaları ekleme veya kaldırma](../role-based-access-control/role-assignments-template.md)

Service Bus mesajlaşma hizmeti hakkında daha fazla bilgi edinmek için aşağıdaki konu başlıklarına bakın.

- [Azure RBAC örneklerini Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
- [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)