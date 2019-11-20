---
title: Azure Active Directory B2C Kullanıcı hesaplarına genel bakış
description: Azure Active Directory B2C ' de kullanılabilecek Kullanıcı hesabı türleri hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a627b0eebc3aa4a19b0670f899d3032d8df58da4
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620465"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Kullanıcı hesaplarına genel bakış

Azure Active Directory B2C (Azure AD B2C) içinde, oluşturulabilecek birkaç hesap türü vardır. Kullanılabilecek Kullanıcı hesabı türlerinde Azure Active Directory, Active Directory B2B ve Active Directory B2C paylaşma.

Aşağıdaki hesap türleri kullanılabilir:

- **İş hesabı** -bir iş hesabı Kiracıdaki kaynaklara erişebilir ve bir yönetici rolüyle, kiracılar yönetebilir.
- **Konuk hesabı** -bir Konuk hesabı yalnızca uygulamalara erişmek veya kiracılar yönetmek için kullanılabilecek bir Microsoft hesabı veya Azure Active Directory Kullanıcı olabilir.
- **Tüketici hesabı** -bir tüketici hesabı, Azure AD B2C kaydettiğiniz uygulamaların bir kullanıcısı tarafından kullanılır. Tüketici hesapları şu şekilde oluşturulabilir:
  - Kullanıcı bir Azure AD B2C uygulamasındaki kaydolma Kullanıcı akışından geçiyor
  - Azure AD Graph API kullanma
  - Azure portalını kullanma

## <a name="work-account"></a>İş hesabı

İş hesabı, Azure AD 'ye bağlı tüm kiracılar için aynı şekilde oluşturulur. Bir iş hesabı oluşturmak için [hızlı başlangıç: Azure Active Directory Yeni Kullanıcı Ekle](../active-directory/fundamentals/add-users-azure-active-directory.md)' deki bilgileri kullanabilirsiniz. Azure portal **Yeni Kullanıcı** seçeneği kullanılarak bir iş hesabı oluşturulur.

Yeni bir iş hesabı eklediğinizde aşağıdaki yapılandırma ayarlarını göz önünde bulundurmanız gerekir:

- **Ad** ve **Kullanıcı adı** - **Name** özelliği, kullanıcının verilen ve soyadını içerir. **Kullanıcı adı** , kullanıcının oturum açmak için girdiği tanıtıcıdır. Kullanıcı adı, tam etki alanını içerir. Kullanıcı adının etki alanı adı bölümü, ilk varsayılan etki alanı adı *Your-Domain.onmicrosoft.com*veya *contoso.com*gibi doğrulanmış, Federasyon dışı bir [özel etki alanı](../active-directory/fundamentals/add-custom-domain.md) adı olmalıdır.
- **Profil** -hesap, Kullanıcı verileri profiliyle ayarlanır. Ad, soyadı, iş unvanı ve bölüm adı girme şansınız vardır. Hesap oluşturulduktan sonra profili düzenleyebilirsiniz.
- **Gruplar** -tek seferde birkaç Kullanıcı veya cihaza lisans veya izin atama gibi yönetim görevlerini gerçekleştirmek için bir grup kullanın. Yeni hesabı kiracınızdaki mevcut bir [gruba](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) koyabilirsiniz.
- **Dizin rolü** -Kullanıcı hesabının kiracınızdaki kaynaklara sahip olduğu erişim düzeyini belirtmeniz gerekir. Aşağıdaki izin düzeyleri kullanılabilir:

    - **Kullanıcı** -kullanıcılar atanan kaynaklara erişebilir, ancak çoğu kiracı kaynağını yönetemez.
    - **Genel yönetici** -Genel Yöneticiler tüm kiracı kaynakları üzerinde tam denetime sahiptir.
    - **Sınırlı yönetici** -Kullanıcı için yönetim rolü veya rolleri seçin. Seçilebilecekleri roller hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rolleri atama](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>İş hesabı oluşturma

Yeni bir iş hesabı oluşturmak için aşağıdaki bilgileri kullanabilirsiniz:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Kullanıcı profilini güncelleştirme

Bir kullanıcının profilini güncelleştirmek için aşağıdaki bilgileri kullanabilirsiniz:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Kullanıcı parolasını sıfırlama

Bir kullanıcının parolasını sıfırlamak için aşağıdaki bilgileri kullanabilirsiniz:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Konuk Kullanıcı

Dış kullanıcıları kiracınıza Konuk Kullanıcı olarak davet edebilirsiniz. Konuk kullanıcıyı Azure AD B2C kiracınıza davet etmek için tipik bir senaryo, yönetim sorumluluklarını paylaşmalıdır. Konuk hesabı kullanmanın bir örneği için bkz. [Azure ACTIVE DIRECTORY B2B işbirliği kullanıcısının özellikleri](../active-directory/b2b/user-properties.md).

Kiracınıza bir Konuk Kullanıcı davet ettiğinizde, alıcının e-posta adresini ve daveti açıklayan bir iletiyi sağlarsınız. Davet **bağlantısı, kullanıcıyı Başlarken düğmesinin seçildiği** ve izin gözden geçirinin kabul edildiği onay sayfasına götürür. Bir gelen kutusu e-posta adresine iliştirilmemişse, Kullanıcı davet edilen kimlik bilgilerini kullanarak bir Microsoft sayfasına giderek onay sayfasına gidebilir. Daha sonra Kullanıcı, e-postadaki bağlantıya tıklanmakla aynı şekilde daveti kullanmaya zorlanır. Örneğin: `https://myapps.microsoft.com/B2CTENANTNAME`.

Konuk Kullanıcı davet etmek için [MICROSOFT Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) 'sini de kullanabilirsiniz.

## <a name="consumer-user"></a>Tüketici kullanıcısı

Tüketici Kullanıcı Azure AD B2C tarafından güvenliği sağlanmış uygulamalarda oturum açabilir, ancak Azure portal gibi Azure kaynaklarına erişemez. Tüketici kullanıcısı, Facebook veya Twitter gibi yerel bir hesabı veya Federasyon hesaplarını kullanabilir. Bir tüketici hesabı bir [kaydolma veya oturum açma Kullanıcı akışı](../active-directory-b2c/active-directory-b2c-reference-policies.md)kullanılarak, Azure AD Graph API kullanılarak veya Azure Portal kullanılarak oluşturulur.

Özel Kullanıcı özniteliklerini kullanarak bir tüketici Kullanıcı hesabı oluşturulduğunda toplanan verileri belirtebilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory B2C özel öznitelikleri tanımlama](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Azure AD B2C bir tüketici hesabı oluşturmak için [Azure AD Graph API kullanma](active-directory-b2c-devquickstarts-graph-dotnet.md) konusunun **Tüketici Kullanıcı hesapları oluştur** bölümündeki bilgileri kullanabilirsiniz. Ayrıca, hesap özelliklerini yönetmek için aynı makalede bulunan **Müşteri Kullanıcı hesaplarını Güncelleştir** bölümündeki bilgileri de kullanabilirsiniz.

### <a name="migrate-consumer-user-accounts"></a>Tüketici Kullanıcı hesaplarını geçirme

Mevcut tüketici Kullanıcı hesaplarını herhangi bir kimlik sağlayıcısından Azure AD B2C geçirmeniz gerekebilir. Daha fazla bilgi için bkz. [Kullanıcı geçişi](active-directory-b2c-user-migration.md) veya [sosyal kimlikleri olan kullanıcıları geçirme](active-directory-b2c-social-migration.md).
