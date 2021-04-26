---
title: Azure AD 'de hizmet sorumlularını & uygulamalar | Mavisi
titleSuffix: Microsoft identity platform
description: Azure Active Directory içindeki uygulama ve hizmet sorumlusu nesneleri arasındaki ilişki hakkında bilgi edinin.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: fc1b5356ab607ecb60a457a7295831958e6815e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727069"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory'deki uygulama ve hizmet sorumlusu nesneleri

Bu makalede uygulama kaydı, uygulama nesneleri ve hizmet sorumluları Azure Active Directory: ne oldukları, nasıl kullanıldıkları ve birbirleriyle nasıl ilişkili oldukları açıklanmaktadır. Bir uygulamanın uygulama nesnesi ile buna karşılık gelen hizmet sorumlusu nesneleri arasındaki ilişkiyi göstermek için de çok kiracılı bir örnek senaryo sunulmaktadır.

## <a name="application-registration"></a>Uygulama kaydı
Kimlik ve erişim yönetimi işlevlerinin Azure AD 'ye temsilciliğini sağlamak için bir uygulamanın bir Azure AD [kiracısıyla](developer-glossary.md#tenant)kayıtlı olması gerekir. Uygulamanızı Azure AD 'ye kaydettiğinizde, uygulamanız için Azure AD ile tümleşmesini sağlayan bir kimlik yapılandırması oluşturuyorsunuz. [Azure Portal][AZURE-Portal]bir uygulamayı kaydettiğinizde, tek bir kiracı (yalnızca kiracınızda erişilebilir) veya çok kiracılı (diğer kiracılarda erişilebilir) olduğunu ve isteğe bağlı olarak bir yeniden yönlendirme URI 'si (erişim belirtecinin gönderildiği konum) ayarlayabileceğinizi seçersiniz.

Bir uygulamayı kaydetmeye ilişkin adım adım yönergeler için bkz. [uygulama kaydı hızlı başlangıç](quickstart-register-app.md).

Uygulama kaydını tamamladığınızda, ana kiracınızda veya dizininizde bulunan, uygulamanın ( [uygulama nesnesi](#application-object)) genel olarak benzersiz bir örneğine sahip olursunuz.  Ayrıca, uygulamanız için genel olarak benzersiz bir KIMLIĞINIZ (uygulama veya istemci KIMLIĞI) vardır.  Portalda, uygulamanızı çalışır hale getirmek, oturum açma iletişim kutusunda uygulamanızın markasını özelleştirmek ve daha fazlasını yapmak için gizli dizileri veya sertifikaları ve kapsamları ekleyebilirsiniz.

Bir uygulamayı portala kaydettiğinizde, ana kiracınızda bir uygulama nesnesi ve hizmet sorumlusu nesnesi otomatik olarak oluşturulur.  Microsoft Graph API 'Lerini kullanarak bir uygulamayı kaydeder/oluşturursanız, hizmet sorumlusu nesnesini oluşturmak ayrı bir adımdır.

## <a name="application-object"></a>Uygulama nesnesi
Bir Azure AD uygulaması, uygulamanın kaydedildiği (uygulamanın "ana" kiracısı olarak bilinir) Azure AD kiracısında bulunan bir ve yalnızca uygulama nesnesi tarafından tanımlanır.  Bir uygulama nesnesi, bir veya daha fazla hizmet sorumlusu nesnesi oluşturmak için şablon veya şema olarak kullanılır.  Uygulamanın kullanıldığı her kiracıda bir hizmet sorumlusu oluşturulur. Nesne odaklı programlamada bir sınıfa benzer şekilde, uygulama nesnesi oluşturulan tüm hizmet sorumlularına (veya uygulama örneklerine) uygulanan bazı statik özelliklere sahiptir.

Uygulama nesnesi bir uygulamanın üç yönlerini açıklar: hizmetin uygulamaya erişmek için belirteçleri nasıl yayımlayabileceğini, uygulamanın erişmesi gerekebilecek kaynakları ve uygulamanın gerçekleştirebileceği eylemleri açıklar.

Azure portal **uygulama kayıtları** dikey penceresi, [][AZURE-Portal] giriş kiracınızdaki uygulama nesnelerini listelemek ve yönetmek için kullanılır.

![Uygulama kayıtları dikey pencere](./media/app-objects-and-service-principals/app-registrations-blade.png)

Microsoft Graph [uygulama varlığı][MS-Graph-App-Entity] , uygulama nesnesinin özellikleri için şemayı tanımlar.

## <a name="service-principal-object"></a>Hizmet sorumlusu nesnesi
Bir Azure AD kiracısı tarafından güvenliği sağlanmış olan kaynaklara erişmek için, erişim gerektiren varlık bir güvenlik sorumlusu tarafından temsil etmelidir. Bu gereksinim, hem kullanıcılar (Kullanıcı sorumlusu) hem de uygulamalar (hizmet sorumlusu) için geçerlidir. Güvenlik sorumlusu, Azure AD kiracısında Kullanıcı/uygulama için erişim ilkesini ve izinleri tanımlar. Bu, oturum açma sırasında kullanıcı/uygulamanın kimlik doğrulaması ve kaynak erişimi sırasında yetkilendirme gibi temel özellikleri sunar.

Üç tür hizmet sorumlusu vardır: uygulama, yönetilen kimlik ve eski.

Hizmet sorumlusu ilk türü, tek bir Kiracıdaki veya dizindeki genel uygulama nesnesinin yerel temsili veya uygulama örneğidir. Bu durumda, hizmet sorumlusu uygulama nesnesinden oluşturulan somut bir örneğidir ve bu uygulama nesnesinden belirli özellikleri devralır. Uygulamanın kullanıldığı her kiracıda bir hizmet sorumlusu oluşturulur ve genel olarak benzersiz uygulama nesnesine başvurur.  Hizmet sorumlusu nesnesi, uygulamanın belirli bir kiracıda ne yapabileceğini, uygulamaya kimlerin erişebileceğini ve uygulamanın erişebileceği kaynakları tanımlar.

Bir uygulamaya bir Kiracıdaki kaynaklara erişim izni verildiğinde (kayıt veya [onay](developer-glossary.md#consent)sağlandığında), bir hizmet sorumlusu nesnesi oluşturulur. Ayrıca [Azure PowerShell](howto-authenticate-service-principal-powershell.md), [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli), [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http), [Azure Portal][AZURE-Portal]ve diğer araçları kullanarak bir kiracıda hizmet sorumlusu nesneleri oluşturabilirsiniz. Portalı kullanırken, bir uygulamayı kaydettiğinizde bir hizmet sorumlusu otomatik olarak oluşturulur.

Hizmet sorumlusu ikinci türü, [yönetilen bir kimliği](/azure/active-directory/managed-identities-azure-resources/overview)temsil etmek için kullanılır. Yönetilen kimlikler, geliştiricilerin kimlik bilgilerini yönetme gereksinimini ortadan kaldırır. Yönetilen kimlikler, uygulamalar için Azure AD kimlik doğrulamasını destekleyen kaynaklara bağlanırken kullanılacak bir kimlik sağlar. Yönetilen bir kimlik etkinleştirildiğinde, kiracınızda yönetilen kimliği temsil eden bir hizmet sorumlusu oluşturulur. Yönetilen kimlikleri temsil eden hizmet sorumlularına erişim ve izinler verilebilir, ancak doğrudan güncelleştirilemez veya değiştirilemez.

Üçüncü hizmet asıl türü eski bir uygulamayı temsil eder (uygulama kayıtları, eski deneyimler aracılığıyla tanıtılmadan önce oluşturulan bir uygulama). Eski bir hizmet sorumlusu kimlik bilgileri, hizmet sorumlusu adları, yanıt URL 'Leri ve yetkili bir kullanıcı tarafından düzenlenebilir ancak ilişkili bir uygulama kaydı olmayan diğer özelliklere sahip olabilir. Hizmet sorumlusu yalnızca oluşturulduğu kiracıda kullanılabilir.

Microsoft Graph [ServicePrincipal varlığı][MS-Graph-Sp-Entity] , bir hizmet sorumlusu nesnesinin özelliklerine ilişkin şemayı tanımlar.

Portaldaki **Kurumsal uygulamalar** dikey penceresi, bir Kiracıdaki hizmet sorumlularını listelemek ve yönetmek için kullanılır. Hizmet sorumlusunun izinlerini, Kullanıcı onaylı izinleri, hangi kullanıcıların onay, oturum açma bilgileri ve daha fazlasını yapmış olduğunu görebilirsiniz.

![Kurumsal uygulamalar dikey penceresi](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Uygulama nesneleri ve hizmet sorumluları arasındaki ilişki

Uygulama nesnesi, uygulamanızın tüm kiracılarda kullanılmak üzere *genel* gösterimidir ve hizmet sorumlusu, belirli bir kiracıda kullanılmak üzere *Yerel* gösterimidir.

Uygulama nesnesi, buna karşılık gelen hizmet sorumlusu nesnelerini oluştururken kullanılmak üzere ortak ve varsayılan özelliklerin *türetildiği* şablon görevi görür. Bu nedenle bir uygulama nesnesi, yazılım uygulamasıyla 1:1 ilişkisine sahiptir ve buna karşılık gelen hizmet sorumlusu nesnesiyle 1: çok ilişki vardır.

Uygulamanın kullanıldığı her kiracıda bir hizmet sorumlusu oluşturulmalıdır, bu sayede oturum açma ve/veya kiracı tarafından güvenliği sağlanmış kaynaklara erişim için bir kimlik oluşturulmasını sağlar. Tek kiracı uygulamasının (ana kiracısında) uygulama kaydı sırasında kullanılmak için oluşturulan ve onaylanan tek bir hizmet sorumlusu vardır. Çok kiracılı bir uygulama, bu kiracıdan gelen bir kullanıcının kendi kullanımına yönelik olarak çalıştığı her bir kiracıda oluşturulmuş bir hizmet sorumlusuna sahiptir.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Uygulamaları değiştirme ve silme sonuçları
Uygulama nesneniz üzerinde yaptığınız tüm değişiklikler Ayrıca uygulamanın yalnızca giriş kiracısındaki hizmet sorumlusu nesnesine yansıtılır (kayıtlı olan kiracı). Bu, bir uygulama nesnesinin silinmesinin ana kiracı hizmet sorumlusu nesnesini de silebileceği anlamına gelir.  Ancak, bu uygulama nesnesinin geri yüklenmesi karşılık gelen hizmet sorumlusunu geri yüklememeyecektir. Çok kiracılı uygulamalarda, erişim [uygulama erişim paneli](https://myapps.microsoft.com) aracılığıyla kaldırılana ve yeniden verilene kadar, uygulama nesnesi üzerindeki değişiklikler herhangi bir tüketici kiracının hizmet sorumlusu nesnelerinde yansıtılmaz.

## <a name="example"></a>Örnek

Aşağıdaki diyagramda, bir uygulamanın uygulama nesnesi ile karşılık gelen hizmet sorumlusu nesneleri arasındaki ilişki, **HR uygulaması** adlı örnek bir çok kiracılı uygulama bağlamında gösterilmektedir. Bu örnek senaryoda üç Azure AD kiracının olması vardır:

- **Adatum** -şirket tarafından **İK uygulamasını** geliştiren kiracı
- **Contoso** - **İK uygulamasının** bir tüketicisi olan contoso organizasyonu tarafından kullanılan kiracı
- **Fabrikam** -fabrikam organizasyonu tarafından kullanılan kiracı, ayrıca **HR uygulamasını** kullanır

![Uygulama nesnesi ve hizmet sorumlusu nesnesi arasındaki ilişki](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Bu örnek senaryoda:

| Adım | Açıklama |
|------|-------------|
| 1    | Uygulamanın giriş kiracısında uygulama ve hizmet sorumlusu nesneleri oluşturma işlemidir. |
| 2    | Contoso ve Fabrikam yöneticileri onayı tamamladıktan sonra, şirketinin Azure AD kiracısında bir hizmet sorumlusu nesnesi oluşturulur ve yöneticinin verdiği izinler atanır. Ayrıca, IK uygulamasının Kullanıcı tarafından bireysel kullanım için izin verecek şekilde yapılandırılıp tasarlanmadığını unutmayın. |
| 3    | HR uygulamasının (contoso ve Fabrikam) tüketici kiracılarının her biri kendi hizmet sorumlusu nesnesine sahiptir. Her biri, ilgili yönetici tarafından onaylanan izinlerle yönetilen, çalışma zamanında uygulamanın bir örneğinin kullanımını temsil eder. |

## <a name="next-steps"></a>Sonraki adımlar

- Hem uygulama hem de hizmet sorumlusu nesnelerini sorgulamak için [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) kullanabilirsiniz.
- Bir uygulamanın uygulama nesnesine, Microsoft Graph API 'sini, [Azure Portal][AZURE-Portal] uygulama bildirimi düzenleyicisini veya [Azure AD PowerShell cmdlet 'Lerini](/powershell/azure/)kullanarak, OData [uygulaması varlığıyla][MS-Graph-App-Entity]temsil ederek erişebilirsiniz.
- Bir uygulamanın hizmet sorumlusu nesnesine, Microsoft Graph API veya [Azure AD PowerShell cmdlet 'leri](/powershell/azure/)aracılığıyla OData [ServicePrincipal varlığıyla][MS-Graph-Sp-Entity]temsil ederek erişebilirsiniz.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
