---
title: Azure Event Hubs kaynaklarına erişmek için bir uygulamanın kimliğini doğrulama
description: Bu makale, Azure Event Hubs kaynaklarına erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama hakkında bilgi sağlar
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913322"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Event Hubs kaynaklara erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama
Microsoft Azure, Azure Active Directory (Azure AD) tabanlı kaynaklar ve uygulamalar için tümleşik erişim denetimi yönetimi sağlar. Azure AD 'yi Azure Event Hubs kullanmanın önemli bir avantajı, kimlik bilgilerinizi artık kodda depolamanızı gerektirmez. Bunun yerine, Microsoft Identity platform 'dan bir OAuth 2,0 erişim belirteci isteyebilirsiniz. Belirteç istemek için kaynak adı olur `https://eventhubs.azure.net/` ve tüm bulutlar/kiracılar (Kafka Istemcileri için bir belirteç istemek için kaynak) için de aynıdır `https://<namespace>.servicebus.windows.net` . Azure AD, uygulamayı çalıştıran güvenlik sorumlusu (bir Kullanıcı, Grup veya hizmet sorumlusu) kimliğini doğrular. Kimlik doğrulaması başarılı olursa, Azure AD uygulamaya bir erişim belirteci döndürür ve uygulama Azure Event Hubs kaynaklarına istek yetkilendirmek için erişim belirtecini kullanabilir.

Azure AD güvenlik sorumlusuna bir rol atandığında Azure bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, abonelik düzeyi, kaynak grubu, Event Hubs ad alanı veya bunun altındaki herhangi bir kaynak kapsamına eklenebilir. Azure AD güvenliği, bir kullanıcıya, gruba, uygulama hizmeti sorumlusuna veya [Azure kaynakları için yönetilen kimliğe](../active-directory/managed-identities-azure-resources/overview.md)roller atayabilir. 

> [!NOTE]
> Rol tanımı, izinlerden oluşan bir koleksiyondur. Azure rol tabanlı erişim denetimi (Azure RBAC), bu izinlerin rol ataması aracılığıyla nasıl uygulanacağını denetler. Rol ataması üç öğeden oluşur: güvenlik sorumlusu, rol tanımı ve kapsam. Daha fazla bilgi için bkz. [farklı rolleri anlama](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure Event Hubs için yerleşik roller
Azure, Azure AD ve OAuth kullanarak Event Hubs verilerine erişim yetkilendirmek için aşağıdaki Azure yerleşik rollerini sağlar:

- [Azure Event Hubs veri sahibi](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Event Hubs kaynaklara yönelik tüm erişim sağlamak için bu rolü kullanın.
- [Azure Event Hubs veri gönderici](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Event Hubs kaynaklara gönderme erişimi sağlamak için bu rolü kullanın.
- [Azure Event Hubs veri alıcısı](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Event Hubs kaynaklara erişim sağlamak için bu rolü kullanın.   

Şema kayıt defteri yerleşik rolleri için bkz. [şema kayıt defteri rolleri](schema-registry-overview.md#azure-role-based-access-control).

> [!IMPORTANT]
> Önizleme sürümümüzü, sahip veya katkıda bulunan rolüne Event Hubs veri erişimi ayrıcalıkları eklemeyi destekliyoruz. Bununla birlikte, sahip ve katkıda bulunan rolü için veri erişimi ayrıcalıkları artık onaylanmaz. Sahip veya katkıda bulunan rolü kullanıyorsanız, Azure Event Hubs veri sahibi rolünü kullanarak geçiş yapın.


## <a name="authenticate-from-an-application"></a>Uygulamadan kimlik doğrulaması yapma
Azure AD 'yi Event Hubs kullanmanın önemli bir avantajı, kimlik bilgilerinizin artık kodunuzda depolanmasına gerek kalmaz. Bunun yerine, Microsoft Identity platform 'dan bir OAuth 2,0 erişim belirteci isteyebilirsiniz. Azure AD, uygulamayı çalıştıran güvenlik sorumlusunun (bir Kullanıcı, Grup veya hizmet sorumlusu) kimliğini doğrular. Kimlik doğrulaması başarılı olursa, Azure AD uygulamaya erişim belirtecini döndürür ve uygulama Azure Event Hubs isteklerini yetkilendirmek için erişim belirtecini kullanabilir.

Aşağıdaki bölümlerde, Microsoft Identity Platform 2,0 ile yerel uygulamanızı veya Web uygulamanızı kimlik doğrulaması için nasıl yapılandıracağınız gösterilmektedir. Microsoft Identity Platform 2,0 hakkında daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) genel bakış](../active-directory/develop/v2-overview.md).

OAuth 2,0 kod verme akışına genel bakış için bkz. [oauth 2,0 kod verme akışını kullanarak Azure Active Directory Web uygulamalarına erişimi yetkilendirme](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Uygulamanızı bir Azure AD kiracısıyla kaydetme
Event Hubs kaynaklarını yetkilendirmek için Azure AD kullanmanın ilk adımı, istemci uygulamanızı [Azure Portal](https://portal.azure.com/)BIR Azure AD kiracısıyla kaydetmekte. İstemci uygulamanızı kaydettiğinizde AD 'ye uygulama hakkında bilgi sağlarsınız. Daha sonra Azure AD, uygulamanızı Azure AD çalışma zamanı ile ilişkilendirmek için kullanabileceğiniz bir istemci KIMLIĞI (uygulama KIMLIĞI olarak da bilinir) sağlar. İstemci KIMLIĞI hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın. 

Aşağıdaki görüntüler, bir Web uygulamasını kaydetme adımlarını göstermektedir:

![Bir uygulamayı kaydetme](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Uygulamanızı yerel bir uygulama olarak kaydettiğinizde, yeniden yönlendirme URI 'SI için geçerli bir URI belirtebilirsiniz. Yerel uygulamalar için, bu değerin gerçek bir URL olması gerekmez. Web uygulamaları için, yeniden yönlendirme URI 'si, belirteçlerin sağlandığı URL 'YI belirttiğinden geçerli bir URI olmalıdır.

Uygulamanızı kaydettikten sonra **Ayarlar** altında **uygulama (istemci) kimliğini** görürsünüz:

![Kayıtlı uygulamanın uygulama KIMLIĞI](./media/authenticate-application/application-id.png)

Bir uygulamayı Azure AD 'ye kaydetme hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>İstemci parolası oluşturma   
Uygulamanın bir belirteç istenirken kimliğini kanıtlamak için bir istemci parolası gerekir. İstemci parolasını eklemek için aşağıdaki adımları izleyin.

1. Azure portal uygulama kaydınız ' ne gidin.
1. **Sertifikalar & gizli** dizi ayarını seçin.
1. **İstemci** gizli dizileri altında yeni bir gizli dizi oluşturmak için **yeni istemci parolası** ' nı seçin.
1. Gizli dizi için bir açıklama sağlayın ve istenen süre sonu aralığını seçin.
1. Yeni Gizliliğin değerini hemen güvenli bir konuma kopyalayın. Fill değeri size yalnızca bir kez görüntülenir.

    ![Gizli anahtar](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal kullanarak Azure rolleri atama  
Uygulamayı kaydettikten sonra, uygulamanın hizmet sorumlusunu [Azure Için derleme rolleri Event Hubs](#built-in-roles-for-azure-event-hubs) bölümünde açıklanan Event Hubs BIR Azure AD rolüne atarsınız. 

1. [Azure Portal](https://portal.azure.com/), Event Hubs ad alanına gidin.
2. **Genel bakış** sayfasında, rol atamak istediğiniz olay hub 'ını seçin.

    ![Olay Hub 'ınızı seçin](./media/authenticate-application/select-event-hub.png)
1. Olay Hub 'ı için erişim denetimi ayarlarını göstermek üzere **Access Control (IAM)** öğesini seçin. 
1. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin. Araç çubuğunda **Ekle** düğmesini seçin ve ardından **rol ataması Ekle**' yi seçin. 

    ![Araç çubuğuna düğme Ekle](./media/authenticate-application/role-assignments-add-button.png)
1. **Rol ataması Ekle** sayfasında, aşağıdaki adımları uygulayın:
    1. Atamak istediğiniz **Event Hubs rolünü** seçin. 
    1. Rolü atamak istediğiniz **güvenlik sorumlusunu** (Kullanıcı, Grup, hizmet sorumlusu) bulmak için arama yapın. Listeden **kayıtlı uygulamayı** seçin. 
    1. Rol atamasını kaydetmek için **Kaydet** ' i seçin. 

        ![Bir kullanıcıya rol atama](./media/authenticate-application/assign-role-to-user.png)
    4. **Rol atamaları** sekmesine geçin ve rol atamasını onaylayın. Örneğin, aşağıdaki görüntüde **MyWebApp** 'nin **Azure Event Hubs veri gönderici** rolünde olduğu gösterilmektedir. 
        
        ![Listedeki Kullanıcı](./media/authenticate-application/user-in-list.png)

Event Hubs ad alanına, kaynak grubuna veya aboneliğe kapsamlı bir rol atamak için de benzer adımları izleyebilirsiniz. Rolü ve kapsamını tanımladıktan sonra bu davranışı [Bu GitHub konumundaki](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)örneklerle test edebilirsiniz. Azure RBAC ve Azure portal kullanarak Azure kaynaklarına erişimi yönetme hakkında daha fazla bilgi edinmek için [Bu makaleye](..//role-based-access-control/role-assignments-portal.md)bakın. 


### <a name="client-libraries-for-token-acquisition"></a>Belirteç alımı için istemci kitaplıkları  
Uygulamanızı kaydettikten ve Azure Event Hubs veri gönderme/alma izinlerine sahip olduktan sonra, bir güvenlik sorumlusunun kimliğini doğrulamak ve OAuth 2,0 belirtecini almak için uygulamanıza kod ekleyebilirsiniz. Belirtecin kimliğini doğrulamak ve almak için, [Microsoft Identity platform kimlik doğrulama kitaplıklarından](../active-directory/develop/reference-v2-libraries.md) birini veya OpenID veya Connect 1,0 ' i destekleyen başka bir açık kaynak kitaplığı kullanabilirsiniz. Uygulamanız daha sonra erişim belirtecini kullanarak Azure Event Hubs karşı bir isteği yetkilendirebilirler.

Belirteçleri alma için desteklenen senaryoların listesi için, .NET GitHub deposu [Için Microsoft kimlik doğrulama kitaplığı 'nın (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [senaryolar](https://aka.ms/msal-net-scenarios) bölümüne bakın.

## <a name="samples"></a>Örnekler
- [Microsoft. Azure. EventHubs örnekleri](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Bu örnekler eski **Microsoft. Azure. EventHubs** kitaplığını kullanır, ancak en son **Azure. Messaging. eventhubs** kitaplığını kullanarak kolayca güncelleştirebilirsiniz. Eski kitaplığı kullanarak örneği yeni bir tane ile taşımak için [Microsoft. Azure. eventhubs 'Den Azure. Messaging. eventhubs 'ye geçiş kılavuzu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)'na bakın.
- [Azure. Messaging. EventHubs örnekleri](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Bu örnek, en son **Azure. Messaging. EventHubs** kitaplığını kullanacak şekilde güncelleştirilmiştir.

## <a name="next-steps"></a>Sonraki adımlar
- Azure RBAC hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)nedir?
- Azure rol atamalarını Azure PowerShell, Azure CLı veya REST API ile atamayı ve yönetmeyi öğrenmek için şu makalelere bakın:
    - [Azure PowerShell kullanarak Azure rol atamalarını ekleme veya kaldırma](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI kullanarak Azure rol atamalarını ekleme veya kaldırma](../role-based-access-control/role-assignments-cli.md)
    - [REST API kullanarak Azure rol atamalarını ekleme veya kaldırma](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager şablonları kullanarak Azure rol atamaları ekleme](../role-based-access-control/role-assignments-template.md)

Aşağıdaki ilgili makalelere bakın:
- [Event Hubs kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](authenticate-managed-identity.md)
- [Paylaşılan erişim Imzalarını kullanarak Azure Event Hubs istek kimliklerini doğrulama](authenticate-shared-access-signature.md)
- [Azure Active Directory kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-azure-active-directory.md)
- [Paylaşılan erişim Imzalarını kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-shared-access-signature.md)
