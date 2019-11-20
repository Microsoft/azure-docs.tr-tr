---
title: Azure Active Directory içindeki uygulama ve hizmet sorumlusu nesneleri
titleSuffix: Microsoft identity platform
description: Azure Active Directory içindeki uygulama ve hizmet sorumlusu nesneleri arasındaki ilişki hakkında bilgi edinin.
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1a4d9301894c6a98abd8244fdd6c10a058a26ad
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803430"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory içindeki uygulama ve hizmet sorumlusu nesneleri

Bazen, "uygulama" teriminin anlamı, Azure Active Directory (Azure AD) bağlamında kullanıldığında yanlış anlaşılabilirler. Bu makale, Azure AD uygulama tümleştirmesinin kavramsal ve somut yönlerini, [çok kiracılı bir uygulama](developer-glossary.md#multi-tenant-application)için kayıt ve onay gösterimi ile açıklar.

## <a name="overview"></a>Genel Bakış

Azure AD ile tümleştirilmiş bir uygulama, yazılımın en boy oranını aşan etkilere sahiptir. "Uygulama" genellikle kavramsal bir terim olarak kullanılır, yalnızca uygulama yazılımının değil, ayrıca çalışma zamanında kimlik doğrulama/yetkilendirme "konuşmaları" içinde Azure AD kaydı ve rolü.

Tanım olarak bir uygulama şu rollerde çalışabilir:

- [İstemci](developer-glossary.md#client-application) rolü (bir kaynak kullanan)
- [Kaynak sunucu](developer-glossary.md#resource-server) rolü (API 'leri istemcilere gösterme)
- Hem istemci rolü hem de kaynak sunucusu rolü

Bir [OAuth 2,0 yetkilendirme verme akışı](developer-glossary.md#authorization-grant) , istemci/kaynağın sırasıyla bir kaynağın verilerine erişmesine/korumasına izin veren konuşma protokolünü tanımlar.

Aşağıdaki bölümlerde, Azure AD uygulama modelinin tasarım zamanı ve çalışma zamanında bir uygulamayı nasıl temsil ettiğini göreceksiniz.

## <a name="application-registration"></a>Uygulama kaydı

[Azure Portal][AZURE-Portal]BIR Azure AD uygulaması kaydettiğinizde, Azure AD kiracınızda iki nesne oluşturulur:

- Uygulama nesnesi ve
- Hizmet sorumlusu nesnesi

### <a name="application-object"></a>Uygulama nesnesi

Bir Azure AD uygulaması, uygulamanın kaydedildiği, uygulamanın "giriş" kiracısı olarak bilinen Azure AD kiracısında bulunan bir ve yalnızca uygulama nesnesi tarafından tanımlanır. Microsoft Graph [uygulama varlığı][MS-Graph-App-Entity] , uygulama nesnesinin özellikleri için şemayı tanımlar.

### <a name="service-principal-object"></a>Hizmet sorumlusu nesnesi

Bir Azure AD kiracısı tarafından güvenliği sağlanmış olan kaynaklara erişmek için, erişim gerektiren varlık bir güvenlik sorumlusu tarafından temsil etmelidir. Bu, hem kullanıcılar (Kullanıcı sorumlusu) hem de uygulamalar (hizmet sorumlusu) için geçerlidir.

Güvenlik sorumlusu, Azure AD kiracısında Kullanıcı/uygulama için erişim ilkesini ve izinleri tanımlar. Bu, oturum açma sırasında kullanıcı/uygulamanın kimlik doğrulaması ve kaynak erişimi sırasında yetkilendirme gibi temel özellikleri sunar.

Bir uygulamaya bir Kiracıdaki kaynaklara erişim izni verildiğinde (kayıt veya [onay](developer-glossary.md#consent)sağlandığında), bir hizmet sorumlusu nesnesi oluşturulur. Microsoft Graph [ServicePrincipal varlığı][MS-Graph-Sp-Entity] , bir hizmet sorumlusu nesnesinin özelliklerine ilişkin şemayı tanımlar.

### <a name="application-and-service-principal-relationship"></a>Uygulama ve hizmet sorumlusu ilişkisi

Uygulama nesnesini, tüm kiracılarda kullanılmak üzere uygulamanızın *genel* temsili olarak ve hizmet sorumlusu, belirli bir kiracıda kullanılmak üzere *Yerel* gösterim olarak göz önünde bulundurun.

Uygulama nesnesi, karşılık gelen hizmet sorumlusu nesneleri oluştururken kullanılmak üzere ortak ve varsayılan özelliklerin *türetildiği* şablon görevi görür. Bu nedenle, bir uygulama nesnesi, yazılım uygulamasıyla 1:1 ilişkiye sahiptir ve buna karşılık gelen hizmet sorumlusu nesnesiyle 1: çok ilişki bulunur.

Uygulamanın kullanıldığı her kiracıda bir hizmet sorumlusu oluşturulmalıdır, bu sayede oturum açma ve/veya kiracı tarafından güvenliği sağlanmış kaynaklara erişim için bir kimlik oluşturulmasını sağlar. Tek kiracılı bir uygulamanın yalnızca bir hizmet sorumlusu vardır (kendi ana kiracısında) ve uygulama kaydı sırasında kullanım için oluşturulur ve kullanıma alınmıştır. Çok kiracılı bir Web uygulaması/API 'SI, bu kiracıdan gelen bir kullanıcının kendi kullanımına neden olan her bir kiracıda oluşturulmuş bir hizmet sorumlusu de vardır.

> [!NOTE]
> Uygulama nesneniz üzerinde yaptığınız tüm değişiklikler Ayrıca uygulamanın yalnızca giriş kiracısındaki hizmet sorumlusu nesnesine yansıtılır (kayıtlı olan kiracı). Çok kiracılı uygulamalarda, erişim [uygulama erişim paneli](https://myapps.microsoft.com) aracılığıyla kaldırılana ve yeniden verilene kadar, uygulama nesnesi üzerindeki değişiklikler herhangi bir tüketici kiracının hizmet sorumlusu nesnelerinde yansıtılmaz.
>
> Ayrıca yerel uygulamaların varsayılan olarak çok kiracılı olarak kaydedildiğini unutmayın.

## <a name="example"></a>Örnek

Aşağıdaki diyagramda, bir uygulamanın uygulama nesnesi ile karşılık gelen hizmet sorumlusu nesneleri arasındaki ilişki, **HR uygulaması**adlı örnek bir çok kiracılı uygulama bağlamında gösterilmektedir. Bu örnek senaryoda üç Azure AD kiracının olması vardır:

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
- Bir uygulamanın uygulama nesnesine, Microsoft Graph API 'sini, [Azure Portal][AZURE-Portal] uygulama bildirimi düzenleyicisini veya [Azure AD PowerShell cmdlet 'Lerini](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)kullanarak, OData [uygulaması varlığıyla][MS-Graph-App-Entity]temsil ederek erişebilirsiniz.
- Bir uygulamanın hizmet sorumlusu nesnesine, Microsoft Graph API veya [Azure AD PowerShell cmdlet 'leri](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)aracılığıyla OData [ServicePrincipal varlığıyla][MS-Graph-Sp-Entity]temsil ederek erişebilirsiniz.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
