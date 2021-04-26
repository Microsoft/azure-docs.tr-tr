---
title: Ticari Market 'te kiracılar yönetme-Azure Marketi
description: Iş Ortağı Merkezi 'nde ticari Market programı için kiracılar yönetme hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108457"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Ticari Market 'te kiracılar yönetme

**Uygun roller**

- Yönetici

Bu belge boyunca *iş hesabınız* olarak da adlandırılan bir Azure ACTIVE DIRECTORY (ad) kiracısı, kuruluşunuzun Azure Portal ayarlanmış bir gösterimidir ve dahili ve harici kullanıcılarınız için Microsoft bulut hizmetleri 'nin belirli bir örneğini yönetmenize yardımcı olur. Kuruluşunuz Azure, Microsoft Intune veya Microsoft 365 gibi bir Microsoft bulut hizmetine abone olduysa, sizin için bir Azure AD kiracısı oluşturulmuştur.

Iş Ortağı Merkezi ile kullanmak için birden çok kiracı ayarlayabilirsiniz. Şirketinizde birden fazla kiracı varsa (örneğin, contoso.com, contoso.uk vb.), ek kiracıları buraya bağlayabilirsiniz. Bu ilişki, ticari Market hesabınızdaki ek kiracılardan Kullanıcı eklemenize ve yönetmenize olanak sağlar.

Iş Ortağı Merkezi hesabında yönetici rolüne sahip herhangi bir Kullanıcı, hesaptan Azure AD kiracılar ekleme ve kaldırma seçeneğine sahip olur.

## <a name="add-an-existing-tenant"></a>Mevcut bir kiracıyı ekleyin

Başka bir Azure AD kiracısını Iş Ortağı Merkezi hesabınızla ilişkilendirmek için:

1. İş Ortağı Merkezi 'nin sağ üst kısmında **Ayarlar**  >  **Hesap ayarları**' nı seçin.
1. **Kuruluş profili** altında **kiracılar**' ı seçin. Geçerli kiracı ilişkilendirmeleri gösteriliyor.
1. **Geliştirici** sekmesinde **ilişkilendir**' i seçin.
1. İlişkilendirmek istediğiniz kiracının Azure AD kimlik bilgilerinizi girin.
1. Azure AD kiracınız için kuruluş ve etki alanı adını gözden geçirin. İlişkilendirmeyi gerçekleştirmek için **Onayla**' yı seçin.

İlişkilendirme başarılı olursa, Iş Ortağı Merkezi 'ndeki kullanıcılar bölümünde Hesap kullanıcılarını eklemek ve yönetmek için bu işlemi gerçekleştirebilirsiniz. Kullanıcı ekleme hakkında bilgi edinmek için bkz. [kullanıcıları yönetme](add-manage-users.md).

## <a name="create-a-new-tenant"></a>Yeni kiracı oluşturma

Iş Ortağı Merkezi hesabınızla yeni bir Azure AD kiracısı oluşturmak için:

1. İş Ortağı Merkezi 'nin sağ üst kısmında **Ayarlar**  >  **Hesap ayarları**' nı seçin.
1. **Kuruluş profili** altında **kiracılar**' ı seçin. Geçerli kiracı ilişkilendirmeleri gösteriliyor.
1. Geliştirici sekmesinde **Oluştur**' u seçin.
1. Yeni Azure AD 'niz için dizin bilgilerini girin:
    - **Etki alanı adı**: Azure AD etki alanınız için kullanacağımız benzersiz ad ". onmicrosoft.com" ile birlikte. Örneğin, "örnek" girdiyseniz, Azure AD etki alanınız "example.onmicrosoft.com" olacaktır.
    - **İletişim e-postası**: gerekirse hesabınız hakkında sizinle iletişim kurabildiğimiz bir e-posta adresi.
    - **Genel yönetici kullanıcı hesabı bilgileri**: yeni genel yönetici hesabı için kullanmak istediğiniz ilk ad, soyadı, Kullanıcı adı ve parola.
1. Yeni etki alanı ve hesap bilgilerini onaylamak için Oluştur ' u seçin.
1. [Kullanıcı eklemeye ve yönetmeye](add-manage-users.md)başlamak Için yenı Azure AD Genel Yönetici Kullanıcı adı ve parolasıyla oturum açın.

Azure portal içinde yeni kiracılar oluşturma hakkında daha fazla bilgi için, Iş Ortağı Merkezi portalı yerine [Azure Active Directory yeni bir kiracı oluşturma](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)makalesine bakın.

## <a name="remove-a-tenant"></a>Kiracı kaldırma

Iş Ortağı Merkezi hesabınızdan bir kiracı kaldırmak için **kiracılar** sayfasında adını bulun ( **Hesap ayarları**' nda) ve ardından **Kaldır**' ı seçin. Kiracıyı kaldırmak istediğinizi onaylamanız istenir. Bunu yaptıktan sonra, söz konusu Kiracıdaki hiçbir Kullanıcı Iş Ortağı Merkezi hesabında oturum açamaz ve bu kullanıcılar için yapılandırdığınız tüm izinler kaldırılır.

> [!TIP]
> Halen aynı Kiracıdaki bir hesabı kullanarak Iş Ortağı Merkezi 'nde oturum açtıysanız, kiracıyı kaldıramazsınız. Bir kiracıyı kaldırmak için Iş Ortağı Merkezi 'Nde hesapla ilişkili başka bir kiracının **Yöneticisi** olarak oturum açmanız gerekir. Hesapla ilişkili yalnızca bir kiracı varsa, bu kiracı yalnızca hesabı açan Microsoft hesabı oturum açtıktan sonra kaldırılabilir.
