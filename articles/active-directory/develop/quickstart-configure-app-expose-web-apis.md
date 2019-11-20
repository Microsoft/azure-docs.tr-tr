---
title: Web API 'Leri kullanıma sunmak için bir uygulama yapılandırma-Microsoft Identity platform
description: Yeni bir izni/kapsamı ve rolü kullanıma sunmak ve uygulamayı istemci uygulamaları için kullanılabilir hale getirmek üzere bir uygulamayı yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9c8e4a2e5dadf64312481f33993911177c90bc7
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "68989283"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis"></a>Hızlı başlangıç: Web API 'Lerini kullanıma sunmak için uygulama yapılandırma

Bir web API'si geliştirip [izinleri/kapsamları](developer-glossary.md#scopes) ve [rolleri](developer-glossary.md#roles) kullanıma sunarak istemci uygulamaları tarafından kullanılmasını sağlayabilirsiniz. Doğru şekilde yapılandırılmış olan bir web API'sini kullanıma sunmak için yapılması gereken işlemler Graph API ve Office 365 API'leri gibi diğer Microsoft web API'leri için yapılması gerekenlerle aynıdır.

Bu hızlı başlangıçta, yeni bir kapsamı kullanıma sunmak ve istemci uygulamaları için kullanılabilir hale getirmek üzere bir uygulamayı yapılandırmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki önkoşulları karşıladığınızdan emin olun:

* Diğer kullanıcılar veya uygulamalar tarafından kullanılması gereken uygulamaları derleme konusunda önemli olan desteklenen [izinler ve onaylar](v2-permissions-and-consent.md) hakkında bilgi edinin.
* Uygulamaların kaydedilmiş olduğu bir kiracı kullanın.
  * Kayıtlı uygulama yoksa, [Microsoft kimlik platformu ile uygulamaları kaydetmeyi öğrenin](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure portalında oturum açın ve uygulamayı seçin

Uygulamayı yapılandırmadan önce, aşağıdaki adımları izleyin:

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalında](https://portal.azure.com) oturum açın.
1. Hesabınız size birden fazla Azure AD kiracısına erişim sunuyorsa sağ üst köşeden hesabınızı seçin ve portal oturumunuzu istediğiniz Azure AD kiracısına ayarlayın.
1. Sol taraftaki Gezinti bölmesinde **Azure Active Directory** hizmetini seçin ve **uygulama kayıtları**' i seçin.
1. Yapılandırmak istediğiniz uygulamayı bulun ve seçin. Uygulamayı seçtiğinizde, uygulamanın **Genel Bakış** veya ana kayıt sayfasını görürsünüz.
1. Yeni bir kapsamı kullanıma sunmak için kullanıcı birimi veya uygulama bildirimi arasından kullanmak istediğiniz yöntemi seçin:
    * [Kullanıcı arabirimi aracılığıyla yeni bir kapsamı kullanıma sunma](#expose-a-new-scope-through-the-ui)
    * [Uygulama bildirimi aracılığıyla yeni bir kapsamı veya rolü kullanıma sunma](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Kullanıcı arabirimi aracılığıyla yeni bir kapsamı kullanıma sunma

[Kullanıcı arabirimini kullanarak bir API 'yi kullanıma sunma ![Shows](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Kullanıcı arabirimi aracılığıyla yeni bir kapsamı kullanıma sunmak için:

1. Uygulamanın **Genel Bakış** sayfasında, **API’yi kullanıma sunma** bölümünü seçin.

1. **Kapsam ekle**’yi seçin.

1. Bir **Uygulama Kimliği URI’si** ayarlamadıysanız, bir tane girmeniz istenir. Uygulama kimliği URI’nizi girin veya sağlanan kimliği kullanın ve ardından **Kaydet ve devam et** seçeneğini belirleyin.

1. **Kapsam ekleyin** sayfası görüntülendiğinde, kapsamınızın bilgilerini girin:

    | Alan | Açıklama |
    |-------|-------------|
    | **Kapsam adı** | Kapsamınız için anlamlı bir ad girin.<br><br>Örneğin, `Employees.Read.All`. |
    | **Kimler onaylayabilir** | Bu kapsama kullanıcılar tarafından onay verilip verilemeyeceğini veya yönetici onayının gerekli olup olmadığını seçin. Daha yüksek ayrıcalıklı izinler için **Yalnızca yöneticiler**’i seçin. |
    | **Yönetici onayı görünen adı** | Kapsamınız için yöneticilerin göreceği anlamlı bir açıklama girin.<br><br>Örneğin, `Read-only access to Employee records` |
    | **Yönetici onayı açıklaması** | Kapsamınız için yöneticilerin göreceği anlamlı bir açıklama girin.<br><br>Örneğin, `Allow the application to have read-only access to all Employee data.` |

    Kullanıcılar kapsamınıza onay verebiliyorsa, aşağıdaki alanlar için değerler ekleyin:

    | Alan | Açıklama |
    |-------|-------------|
    | **Kullanıcı onayı görünen adı** | Kapsamınız için kullanıcıların göreceği anlamlı bir ad girin.<br><br>Örneğin, `Read-only access to your Employee records` |
    | **Kullanıcı onayı açıklaması** | Kapsamınız için kullanıcıların göreceği anlamlı bir açıklama girin.<br><br>Örneğin, `Allow the application to have read-only access to your Employee data.` |

1. **Durum**’u ayarlayın ve işiniz bittiğinde **Kapsam ekle**’yi seçin.

1. [Web API'sinin diğer uygulamaların kullanımına sunulduğunu doğrulama](#verify-the-web-api-is-exposed-to-other-applications) adımlarını izleyin.

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Uygulama bildirimi aracılığıyla yeni bir kapsamı veya rolü kullanıma sunma

[![Bildirimde oauth2Permissions koleksiyonunu kullanarak yeni bir kapsamı kullanıma sunma](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Uygulama bildirimi aracılığıyla yeni bir kapsamı kullanıma sunmak için:

1. Uygulamanın **Genel Bakış** sayfasında, **Bildirim** bölümünü seçin. Bildirimi portalda **Düzenleme** seçeneği sunana web tabanlı bir bildirim düzenleyici açılır. İsteğe bağlı olarak **İndir** seçeneğini belirleyip bildirimi yerel ortamda düzenledikten sonra **Yükle** seçeneğiyle uygulamanıza yeniden uygulayabilirsiniz.
    
    Bu örnekte `oauth2Permissions` koleksiyonuna aşağıdaki JSON öğesini ekleyerek kaynak/API öğesinde `Employees.Read.All` adlı yeni bir kapsamı kullanıma sunma gösterilmektedir.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

   > [!NOTE]
   > `id` değerinin program aracılığıyla veya [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) gibi bir GUID oluşturma aracı kullanılarak oluşturulması gerekir. `id`, web API'si tarafından kullanıma sunulan kapsam için benzersiz tanıtıcıyı temsil eder. Bir istemci web API'nize erişmek için gerekli izinlerle uygun şekilde yapılandırıldıktan sonra Azure AD tarafından bir OAuth 2.0 erişim belirteci düzenlenir. İstemci web API'sini çağırdığında uygulama kaydında istenen izinlere göre ayarlanan kapsam (scp) talebine sahip olan erişim belirtecini sunar.
   >
   > Gerekirse daha sonra ek kapsamları kullanıma sunabilirsiniz. Web API'niz farklı işlevlerle ilişkilendirilmiş birden fazla kapsamı kullanıma sunabilir. Kaynağınız alınan OAuth 2.0 erişim belirtecindeki kapsam (`scp`) taleplerini değerlendirerek web API'si erişimini çalışma zamanında denetleyebilir.

1. İşlemi tamamladıktan sonra **Kaydet**’e tıklayın. Web API'nizi dizininizdeki diğer uygulamalar tarafından kullanılacak şekilde yapılandırmış oldunuz.
1. [Web API'sinin diğer uygulamaların kullanımına sunulduğunu doğrulama](#verify-the-web-api-is-exposed-to-other-applications) adımlarını izleyin.

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Web API'sinin diğer uygulamaların kullanımına sunulduğunu doğrulama

1. Azure AD kiracınıza geri dönün, **Uygulama kayıtları**'nı seçin ve yapılandırmak istediğiniz istemci uygulamasını seçin.
1. [Bir istemci uygulamasını web API'lerine erişecek şekilde yapılandırma](quickstart-configure-app-access-web-apis.md) bölümünde belirtilen adımları tekrarlayın.
1. [BIR API seçme](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis
)adımına geldiğinizde, kaynağı seçin. İstemci izin istekleri için kullanılabilir durumdaki yeni kapsamı görmeniz gerekir.

## <a name="more-on-the-application-manifest"></a>Uygulama bildirimi hakkında daha fazla bilgi

Uygulama bildirimi, bir Azure AD uygulamasının kimlik yapılandırmasının tüm özniteliklerini tanımlayan uygulama varlığının güncelleştirilmesi için bir uygulama bildirimi görevi görür. Uygulama varlığı ve şeması hakkında daha fazla bilgi için bkz. [Graph API Uygulama varlığı belgeleri](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). İlgili makalede aşağıdakiler dahil olmak üzere API'niz için izin belirtmek için kullanılan Uygulama varlığı üyeleri hakkında ayrıntılı başvuru bilgileri bulunur:  

* [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) varlığı koleksiyonu olan ve bir web API'sinin [uygulama izinlerini](developer-glossary.md#permissions) tanımlamak için kullanılan appRoles üyesi.
* [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) varlığı koleksiyonu olan ve bir web API'sinin [temsilcili izinlerini](developer-glossary.md#permissions) tanımlamak için kullanılan oauth2Permissions üyesi.

Uygulama bildirimi kavramları hakkında daha fazla genel bilgi için bkz. [Azure Active Directory uygulama bildirimini anlama](reference-app-manifest.md).

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalar için diğer ilgili uygulama yönetimi hızlı başlangıçları hakkında bilgi edinin:

* [Microsoft kimlik platformu ile uygulama kaydetme](quickstart-register-app.md)
* [Bir istemci uygulamasını web API'lerine erişecek şekilde yapılandırma](quickstart-configure-app-access-web-apis.md)
* [Bir uygulama tarafından desteklenen hesapları değiştirme](quickstart-modify-supported-accounts.md)
* [Microsoft kimlik platformu ile kaydedilmiş bir uygulamayı kaldırma](quickstart-remove-app.md)

Kayıtlı uygulamayı temsil eden iki Azure AD nesnesi ve aralarındaki ilişki hakkında daha fazla bilgi edinmek için bkz. [Uygulama nesneleri ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).

Azure Active Directory ile uygulama geliştirirken kullanmanız gereken markalama yönergeleri hakkında daha fazla bilgi edinmek için bkz. [Uygulamalar için markalama yönergeleri](howto-add-branding-in-azure-ad-apps.md).
