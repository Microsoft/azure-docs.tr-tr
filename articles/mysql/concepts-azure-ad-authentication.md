---
title: Active Directory kimlik doğrulaması-MySQL için Azure veritabanı
description: MySQL için Azure veritabanı ile kimlik doğrulama Azure Active Directory kavramları hakkında bilgi edinin
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e3afa7e7129c34aae8487affe979bd7704580a1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612458"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Azure Active Directory kullanarak MySQL kimlik doğrulaması yapma

Microsoft Azure Active Directory (Azure AD) kimlik doğrulaması, Azure AD 'de tanımlanan kimlikleri kullanarak MySQL için Azure veritabanı 'na bağlanma mekanizmasıdır.
Azure AD kimlik doğrulamasıyla, veritabanı kullanıcı kimliklerini ve diğer Microsoft hizmetlerini merkezi bir konumda yönetebilirsiniz ve bu da izin yönetimini basitleştirir.

Azure AD kullanmanın avantajları şunlardır:

- Azure hizmetleri genelindeki kullanıcıların tek biçimli bir şekilde kimlik doğrulaması
- Parola ilkelerinin ve parola rotasyonun tek bir yerde yönetimi
- Azure Active Directory tarafından desteklenen ve parolaların depolanması gereksinimini ortadan kaldıran birden çok kimlik doğrulama biçimi
- Müşteriler, dış (Azure AD) gruplarını kullanarak veritabanı izinlerini yönetebilir.
- Azure AD kimlik doğrulaması, veritabanı düzeyinde kimlik doğrulaması yapmak için MySQL veritabanı kullanıcılarını kullanır
- MySQL için Azure veritabanı 'na bağlanan uygulamalar için belirteç tabanlı kimlik doğrulama desteği

Azure Active Directory kimlik doğrulamasını yapılandırmak ve kullanmak için aşağıdaki işlemi kullanın:

1. Gerektiğinde Kullanıcı kimlikleriyle Azure Active Directory oluşturun ve doldurun.
2. İsteğe bağlı olarak Azure aboneliğinizle ilişkili olan Active Directory ilişkilendirebilir veya değiştirin.
3. MySQL için Azure veritabanı sunucusu için bir Azure AD yöneticisi oluşturun.
4. Veritabanınızda Azure AD kimlikleriyle eşlenen veritabanı kullanıcıları oluşturun.
5. Azure AD kimliği için bir belirteç alarak ve oturum açarak veritabanınıza bağlanın.

> [!NOTE]
> Azure AD oluşturma ve doldurma hakkında bilgi edinmek ve sonra Azure AD 'yi MySQL için Azure veritabanı ile yapılandırmak için, bkz. [MySQL için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Mimari

Aşağıdaki üst düzey diyagram, kimlik doğrulamasının MySQL için Azure veritabanı ile Azure AD kimlik doğrulaması kullanılarak nasıl çalıştığını özetler. Oklar, iletişim yollarını gösterir.

![kimlik doğrulama akışı][1]

## <a name="administrator-structure"></a>Yönetici yapısı

Azure AD kimlik doğrulaması kullanılırken, MySQL sunucusu için iki yönetici hesabı bulunur; özgün MySQL yöneticisi ve Azure AD yöneticisi. Yalnızca bir Azure AD hesabını temel alan yönetici, bir kullanıcı veritabanında ilk Azure AD kapsanan veritabanı kullanıcısını oluşturabilir. Azure AD yönetici oturumu, bir Azure AD kullanıcısı veya bir Azure AD grubu olabilir. Yönetici bir grup hesabı olduğunda, MySQL sunucusu için birden çok Azure AD yöneticisini etkinleştirerek herhangi bir grup üyesi tarafından kullanılabilir. Yönetici olarak bir grup hesabı kullanmak, MySQL sunucusundaki kullanıcıları veya izinleri değiştirmeden Azure AD 'de grup üyelerini merkezi olarak eklemenize ve kaldırmanıza olanak tanıyarak yönetilebilirlik geliştirir. Herhangi bir anda yalnızca bir Azure AD Yöneticisi (bir kullanıcı veya grup) yapılandırılabilir.

![Yönetici yapısı][2]

## <a name="permissions"></a>İzinler

Azure AD ile kimlik doğrulayabilecek yeni kullanıcılar oluşturmak için, tasarlanan Azure AD yöneticisi olmanız gerekir. Bu Kullanıcı, belirli bir MySQL için Azure veritabanı sunucusu için Azure AD yönetici hesabı yapılandırılarak atanır.

Yeni bir Azure AD veritabanı kullanıcısı oluşturmak için Azure AD yöneticisi olarak bağlanmanız gerekir. Bu, [MySQL Için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-azure-ad-authentication.md)bölümünde gösterilmiştir.

Tüm Azure AD kimlik doğrulaması yalnızca Azure AD yöneticisi MySQL için Azure veritabanı 'nda oluşturulduysa mümkündür. Azure Active Directory Yöneticisi sunucudan kaldırılmışsa, daha önce oluşturulan mevcut Azure Active Directory Kullanıcılar artık Azure Active Directory kimlik bilgilerini kullanarak veritabanına bağlanamaz.

## <a name="connecting-using-azure-ad-identities"></a>Azure AD kimliklerini kullanarak bağlanma

Azure Active Directory kimlik doğrulaması, Azure AD kimliklerini kullanarak bir veritabanına bağlanmak için aşağıdaki yöntemleri destekler:

- Azure Active Directory parolası
- Azure Active Directory tümleşik
- MFA ile evrensel Azure Active Directory
- Active Directory Uygulama sertifikaları veya istemci gizli dizileri kullanma
- [Yönetilen Kimlik](howto-connect-with-managed-identity.md)

Active Directory göre kimlik doğrulamasından sonra bir belirteç alırsınız. Bu belirteç oturum açmak için parolanız.

Lütfen yeni kullanıcı ekleme gibi yönetim işlemlerinin bu noktada yalnızca Azure AD Kullanıcı rolleri için desteklendiğini unutmayın.

> [!NOTE]
> Active Directory belirteciyle bağlanma hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Diğer konular

- Azure Active Directory kimlik doğrulaması yalnızca MySQL 5,7 ve üzeri sürümlerde kullanılabilir.
- Bir MySQL için Azure veritabanı sunucusu için herhangi bir anda yalnızca bir Azure AD yöneticisi yapılandırılabilir.
- Yalnızca MySQL için bir Azure AD yöneticisi, Azure Active Directory bir hesabı kullanarak MySQL için Azure veritabanı 'na ilk kez bağlanabilir. Active Directory Yöneticisi, sonraki Azure AD veritabanı kullanıcılarını yapılandırabilir.
- Bir Kullanıcı Azure AD 'den silinirse, bu kullanıcı artık Azure AD 'de kimlik doğrulaması yapamaz ve bu nedenle bu kullanıcı için bir erişim belirteci elde etmek mümkün olmayacaktır. Bu durumda, eşleşen kullanıcı hala veritabanında olmaya devam etse de, bu kullanıcıyla sunucuya bağlanmak mümkün olmayacaktır.
> [!NOTE]
> Silinen Azure AD kullanıcısı ile oturum açma işlemi, belirtecin süresi dolana kadar (belirteç veren 60 dakikaya kadar) yine de yapılabilir.  Kullanıcıyı MySQL için Azure veritabanı 'ndan da kaldırırsanız, bu erişim hemen iptal edilir.
- Azure AD yöneticisi sunucudan kaldırılırsa, sunucu artık bir Azure AD kiracısıyla ilişkilendirilmeyecektir ve bu nedenle tüm Azure AD oturum açmaları sunucu için devre dışı bırakılır. Aynı kiracıdan yeni bir Azure AD yöneticisi eklemek, Azure AD oturumlarını yeniden etkinleştirir.
- MySQL için Azure veritabanı, Kullanıcı adını kullanmanın aksine kullanıcının benzersiz Azure AD kullanıcı KIMLIĞINI kullanarak MySQL için Azure veritabanı kullanıcısına erişim belirteçlerini eşleştirir. Bu, bir Azure AD kullanıcısının Azure AD 'de silindiği ve aynı adla yeni bir kullanıcının oluşturulduğu anlamına gelir, MySQL için Azure veritabanı farklı bir kullanıcı tarafından kabul edilir. Bu nedenle, bir Kullanıcı Azure AD 'den silinirse ve aynı ada sahip yeni bir Kullanıcı eklendiyse, Yeni Kullanıcı var olan kullanıcıyla bağlantı kurabilmez.

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD oluşturma ve doldurma hakkında bilgi edinmek ve sonra Azure AD 'yi MySQL için Azure veritabanı ile yapılandırmak için, bkz. [MySQL için Azure veritabanı Için Azure AD Ile yapılandırma ve oturum açma](howto-configure-sign-in-azure-ad-authentication.md).
- Oturum açma bilgileri ve MySQL için Azure veritabanı kullanıcılarına yönelik bir genel bakış için bkz. [MySQL Için Azure veritabanı 'nda Kullanıcı oluşturma](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
