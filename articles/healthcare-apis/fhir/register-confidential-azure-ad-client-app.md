---
title: Azure AD 'de gizli bir istemci uygulamasını kaydetme-FHIR için Azure API
description: Bir kullanıcı adına kimlik doğrulaması yapan ve kaynak uygulamalarına erişim isteyen Azure Active Directory bir gizli istemci uygulamasını kaydedin.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284448"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Gizli bir istemci uygulamasını Azure Active Directory kaydetme

Bu öğreticide, Azure Active Directory (Azure AD) bir gizli istemci uygulamasını nasıl kaydedeceğinizi öğreneceksiniz.  

İstemci uygulama kaydı, bir uygulamanın kullanıcı adına kimlik doğrulaması yapmak ve [kaynak uygulamalarına](register-resource-azure-ad-client-app.md)erişim istemek için kullanılabilecek BIR Azure AD gösterimidir. Gizli bir istemci uygulaması, gizli dizi tutmak ve erişim belirteçleri istenirken gizli anahtar sağlamak için güvenilir bir uygulamadır. Gizli uygulama örnekleri, sunucu tarafı uygulamalardır. 

Yeni bir gizli istemci uygulamasını kaydetmek için aşağıdaki adımlara bakın. 

## <a name="register-a-new-application"></a>Yeni uygulama kaydetme

1. [Azure portalda](https://portal.azure.com)**Azure Active Directory**'yi seçin.

1. **Uygulama kayıtları**’nı seçin. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure portal. Yeni uygulama kaydı.":::

1. **Yeni kayıt** seçeneğini belirleyin.

1. Uygulamaya kullanıcıya dönük bir ekran adı verin.

1. **Desteklenen hesap türleri** için, uygulamayı kullanabilecek kişileri SEÇIN veya API 'ye erişim sağlayabilirsiniz.

1. Seçim **Yeniden yönlendirme URI 'si** sağlayın. Bu ayrıntılar daha sonra değiştirilebilir, ancak uygulamanızın yanıt URL 'sini biliyorsanız, şimdi girin.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Yeni gizli Istemci uygulaması kaydı.":::

1. **Kaydet**’i seçin.

## <a name="api-permissions"></a>API izinleri

Uygulamanızı kaydettirdiğiniz artık, bu uygulamanın kullanıcı adına hangi API izinlerini isteyeceğini seçmeniz gerekir.

1. **API izinleri**' ni seçin.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Gizli istemci. API Izinleri.":::

1. **Izin Ekle**' yi seçin.

    FHıR için Azure API kullanıyorsanız, **Kuruluşumun kullandığı API 'ler** altında **Azure sağlık API 'Sini** arayarak Azure sağlık API 'lerine bir izin ekleyeceksiniz. Azure sağlık API 'sine yönelik arama sonucu yalnızca, [FHıR Için Azure API](fhir-paas-powershell-quickstart.md)'sini zaten dağıttıysanız döndürülür.

    Farklı bir kaynak uygulamasına başvuruyorsanız, daha önce **API 'Lerim** altında oluşturduğunuz [FHıR API kaynak uygulaması kaydınızı](register-resource-azure-ad-client-app.md) seçin.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Gizli istemci. Org API 'lerim" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Gizli istemci uygulamasının Kullanıcı adına isteyeceğiz kapsamları (izinler) seçin. **User_impersonation**' yi seçin ve ardından **izin Ekle**' yi seçin.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Gizli istemci. Temsilci Izinleri":::


## <a name="application-secret"></a>Uygulama gizli dizisi

1. **Sertifikalar & sertifikalar**' ı seçin ve ardından **yeni istemci parolası**' nı seçin. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Gizli istemci. Uygulama gizli anahtarı.":::

1. İstemci parolası için bir **Açıklama** girin. Süre sonu **açılan menüsünü seçerek sona erme** saati çerçevesini seçin ve ardından **Ekle**' ye tıklayın.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="İstemci parolasını ekleyin.":::

1. İstemci gizli dizesi oluşturulduktan sonra, **değerini** ve **kimliğini** kopyalayın ve tercih ettiğiniz güvenli bir yerde depolayın.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="İstemci gizli dizesi."::: 

> [!NOTE]
>İstemci gizli dizesi Azure portal yalnızca bir kez görünür. Sertifikalar & gizli dizi Web sayfasından uzaklaştığınızda ve sonra geri döndüğünüzde, değer dizesi maskelenmiş hale gelir. İstemci gizli dizesini oluşturulduktan hemen sonra kopyalamak önemlidir. İstemci gizlilikinizin yedek kopyası yoksa, yeniden oluşturmak için yukarıdaki adımları tekrarlamanız gerekir.
 
## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir gizli istemci uygulamasını Azure AD 'ye kaydetme adımlarında size yol gösterilecektir. Ayrıca, Azure sağlık API 'sine API izinleri ekleme adımlarında de gezinirsiniz. Son olarak, uygulama gizli dizisi oluşturma hakkında gösterildi. Ayrıca, Postman kullanarak FHıR sunucunuza nasıl erişebileceğinizi de öğrenebilirsiniz.
 
>[!div class="nextstepaction"]
>[Postman ile FHıR için Azure API 'sine erişme](access-fhir-postman-tutorial.md)
