---
title: PostgreSQL için Azure veritabanı ile Azure Active Directory kimlik doğrulaması-tek sunucu
description: PostgreSQL için Azure veritabanı-tek sunucu ile kimlik doğrulaması için Azure Active Directory kavramları hakkında bilgi edinin
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 47637396581beeafb0748066cd6a66f011e8eaa1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518742"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>PostgreSQL ile kimlik doğrulaması için Azure Active Directory kullanma

Microsoft Azure Active Directory (Azure AD) kimlik doğrulaması, Azure AD 'de tanımlanan kimlikleri kullanarak PostgreSQL için Azure veritabanı 'na bağlanma mekanizmasıdır.
Azure AD kimlik doğrulamasıyla, veritabanı kullanıcı kimliklerini ve diğer Microsoft hizmetlerini merkezi bir konumda yönetebilirsiniz ve bu da izin yönetimini basitleştirir.

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı Azure AD kimlik doğrulaması şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure AD kullanmanın avantajları şunlardır:

- Azure hizmetleri genelindeki kullanıcıların tek biçimli bir şekilde kimlik doğrulaması
- Parola ilkelerinin ve parola rotasyonun tek bir yerde yönetimi
- Azure Active Directory tarafından desteklenen ve parolaların depolanması gereksinimini ortadan kaldıran birden çok kimlik doğrulama biçimi
- Müşteriler, dış (Azure AD) gruplarını kullanarak veritabanı izinlerini yönetebilir.
- Azure AD kimlik doğrulaması, kimlik doğrulaması için PostgreSQL veritabanı rollerini veritabanı düzeyinde kullanır
- PostgreSQL için Azure veritabanı 'na bağlanan uygulamalar için belirteç tabanlı kimlik doğrulama desteği

Azure Active Directory kimlik doğrulamasını yapılandırmak ve kullanmak için aşağıdaki işlemi kullanın:

1. Gerektiğinde Kullanıcı kimlikleriyle Azure Active Directory oluşturun ve doldurun.
2. İsteğe bağlı olarak Azure aboneliğinizle ilişkili olan Active Directory ilişkilendirebilir veya değiştirin.
3. PostgreSQL için Azure veritabanı sunucusu için bir Azure AD yöneticisi oluşturun.
4. Veritabanınızda Azure AD kimlikleriyle eşlenen veritabanı kullanıcıları oluşturun.
5. Azure AD kimliği için bir belirteç alarak ve oturum açarak veritabanınıza bağlanın.

> [!NOTE]
> Azure AD oluşturma ve doldurma hakkında bilgi edinmek ve sonra Azure AD 'yi PostgreSQL için Azure veritabanı ile yapılandırmak için bkz. [PostgreSQL için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Mimari

Aşağıdaki üst düzey diyagramda kimlik doğrulamanın PostgreSQL için Azure veritabanı ile Azure AD kimlik doğrulaması kullanılarak nasıl çalıştığı özetlenmektedir. Oklar, iletişim yollarını gösterir.

![Kimlik doğrulama akışı][1]

## <a name="administrator-structure"></a>Yönetici yapısı

Azure AD kimlik doğrulaması kullanılırken, PostgreSQL sunucusu için iki yönetici hesabı bulunur; özgün PostgreSQL Yöneticisi ve Azure AD yöneticisi. Yalnızca bir Azure AD hesabını temel alan yönetici, bir kullanıcı veritabanında ilk Azure AD kapsanan veritabanı kullanıcısını oluşturabilir. Azure AD yönetici oturumu, bir Azure AD kullanıcısı veya bir Azure AD grubu olabilir. Yönetici bir grup hesabı olduğunda, PostgreSQL sunucusu için birden çok Azure AD yöneticisini etkinleştirerek herhangi bir grup üyesi tarafından kullanılabilir. Yönetici olarak bir grup hesabı kullanmak, PostgreSQL sunucusundaki kullanıcıları veya izinleri değiştirmeden Azure AD 'de grup üyelerini merkezi olarak eklemenize ve kaldırmanıza olanak tanıyarak yönetilebilirlik geliştirir. Herhangi bir anda yalnızca bir Azure AD Yöneticisi (bir kullanıcı veya grup) yapılandırılabilir.

![Yönetici yapısı][2]

## <a name="permissions"></a>İzinler

Azure AD ile kimlik doğrulayabilecek yeni kullanıcılar oluşturmak için, veritabanında `azure_ad_admin` rolüne sahip olmanız gerekir. Bu rol, belirli bir PostgreSQL için Azure veritabanı sunucusu için Azure AD yönetici hesabı yapılandırılarak atanır.

Yeni bir Azure AD veritabanı kullanıcısı oluşturmak için Azure AD yöneticisi olarak bağlanmanız gerekir. Bu, [PostgreSQL Için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-aad-authentication.md)bölümünde gösterilmiştir.

Azure AD kimlik doğrulaması, yalnızca PostgreSQL için Azure veritabanı için Azure AD yöneticisi oluşturulduysa mümkündür. Azure Active Directory Yöneticisi sunucudan kaldırılmışsa, daha önce oluşturulan mevcut Azure Active Directory Kullanıcılar artık Azure Active Directory kimlik bilgilerini kullanarak veritabanına bağlanamaz.

## <a name="connecting-using-azure-ad-identities"></a>Azure AD kimliklerini kullanarak bağlanma

Azure Active Directory kimlik doğrulaması, Azure AD kimliklerini kullanarak bir veritabanına bağlanmak için aşağıdaki yöntemleri destekler:

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- MFA ile evrensel Azure Active Directory
- Active Directory Uygulama sertifikaları veya istemci gizli dizileri kullanma

Active Directory göre kimlik doğrulamasından sonra bir belirteç alırsınız. Bu belirteç oturum açmak için parolanız.

> [!NOTE]
> Active Directory belirteciyle bağlanma hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Diğer konular

- Yönetilebilirlik geliştirmek için, bir yönetici olarak adanmış bir Azure AD grubu sağlamanızı öneririz.
- Bir PostgreSQL için Azure veritabanı sunucusu için herhangi bir anda yalnızca bir Azure AD Yöneticisi (Kullanıcı veya grup) yapılandırılabilir.
- Yalnızca PostgreSQL için Azure AD yöneticisi, Azure Active Directory bir hesabı kullanarak ilk olarak PostgreSQL için Azure veritabanı 'na bağlanabilir. Active Directory Yöneticisi, sonraki Azure AD veritabanı kullanıcılarını yapılandırabilir.
- Bir Kullanıcı Azure AD 'den silinirse, bu kullanıcı artık Azure AD 'de kimlik doğrulaması yapamaz ve bu nedenle bu kullanıcı için bir erişim belirteci elde etmek mümkün olmayacaktır. Bu durumda, eşleşen rol veritabanında olmaya devam eder, ancak bu rolle sunucuya bağlanmak mümkün olmayacaktır.
> [!NOTE]
> Silinen Azure AD kullanıcısı ile oturum açma işlemi, belirtecin süresi dolana kadar (belirteç veren 60 dakikaya kadar) yine de yapılabilir.  Kullanıcıyı PostgreSQL için Azure veritabanı 'ndan da kaldırırsanız, bu erişim hemen iptal edilir.
- Azure AD yöneticisi sunucudan kaldırılırsa, sunucu artık bir Azure AD kiracısıyla ilişkilendirilmeyecektir ve bu nedenle tüm Azure AD oturum açmaları sunucu için devre dışı bırakılır. Aynı kiracıdan yeni bir Azure AD yöneticisi eklemek, Azure AD oturum açma işlemlerini yeniden etkinleştirin.
- PostgreSQL için Azure veritabanı, Kullanıcı adını kullanmanın aksine kullanıcının benzersiz Azure AD kullanıcı KIMLIĞINI kullanarak PostgreSQL için Azure veritabanı rolüne yönelik erişim belirteçlerini eşleştirir. Bu, bir Azure AD kullanıcısının Azure AD 'de silindiği ve aynı ada sahip yeni bir kullanıcının oluşturulduğu anlamına gelir; PostgreSQL için Azure veritabanı farklı bir kullanıcı olarak kabul eder. Bu nedenle, bir Kullanıcı Azure AD 'den silinirse ve aynı ada sahip yeni bir Kullanıcı eklendiyse, Yeni Kullanıcı var olan rolle bağlantı kurabilmez. Buna izin vermek için PostgreSQL için Azure veritabanı Azure AD yöneticisi 'nin, Azure AD kullanıcı KIMLIĞINI yenilemek için "azure_ad_user" rolüne sahip olması ve ardından bu rolü kullanıcıya vermesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD oluşturma ve doldurma hakkında bilgi edinmek ve sonra Azure AD 'yi PostgreSQL için Azure veritabanı ile yapılandırmak için bkz. [PostgreSQL için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-aad-authentication.md).
- Oturum açma bilgileri, kullanıcılar ve veritabanı rolleri PostgreSQL için Azure veritabanı 'na genel bakış için bkz. [PostgreSQL Için Azure veritabanı 'nda Kullanıcı oluşturma-tek sunucu](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
