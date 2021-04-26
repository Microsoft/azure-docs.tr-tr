---
title: Özel etki alanınızı ekleme-Azure Active Directory | Microsoft Docs
description: Azure Active Directory kullanarak özel bir etki alanı eklemeye ilişkin yönergeler.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973907"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Azure Active Directory portalını kullanarak özel etki alanı adınızı ekleme

Her yeni Azure AD kiracısı, bir ilk etki alanı adı olan *\<domainname> . onmicrosoft.com* ile gelir. İlk etki alanı adını değiştiremez veya silemezsiniz, ancak kuruluşunuzun adlarını da ekleyebilirsiniz. Özel etki alanı adları eklemek, kullanıcılarınıza alışkın olduğunuz *\@ contoso.com* gibi kullanıcı adları oluşturmanıza yardımcı olur.

## <a name="before-you-begin"></a>Başlamadan önce

Özel bir etki alanı adı ekleyebilmeniz için, etki alanı adını bir etki alanı kaydedicisi ile oluşturun. [Acann-Acalacaklandırılabilir kayıt şirketlerinde](https://www.icann.org/registrar-reports/accredited-list.html)öğesine bakın.

## <a name="create-your-directory-in-azure-ad"></a>Azure AD 'de dizininizi oluşturma

Etki alanı adınızı aldıktan sonra, ilk Azure AD dizininizi oluşturabilirsiniz. Aboneliğiniz için **sahip** rolüne sahip bir hesap kullanarak dizininiz için Azure Portal oturum açın.

[Kuruluşunuz için yeni bir kiracı oluşturma](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)' daki adımları izleyerek yeni dizininizi oluşturun.

>[!IMPORTANT]
>Kiracıyı oluşturan kişi, bu kiracının otomatik olarak genel yöneticisidir. Genel yönetici kiracıya daha fazla yönetici ekleyebilir.

Abonelik rolleri hakkında daha fazla bilgi için bkz. [Azure rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

>[!TIP]
> Şirket içi Windows Server AD 'nizi Azure AD ile federasyona eklemek istiyorsanız, dizinlerinizi eşitlemeniz için Azure AD Connect aracını çalıştırdığınızda **Bu etki alanını yerel Active Directory çoklu oturum açma için yapılandırmayı planlıyorum** ' ı seçmeniz gerekir.
>
> Ayrıca aynı etki alanı adını sihirbazın şirket içi dizininizi **Azure AD Etki Alanı** ile birleştirme adımında da kaydetmeniz gerekir. Kurulumun nasıl göründüğünü görmek için bkz. [Federasyon için seçili Azure AD etki alanını doğrulayın](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Azure AD Connect aracınız yoksa [buradan indirebilirsiniz](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Özel etki alanı adınızı Azure AD 'ye ekleme

Dizininizi oluşturduktan sonra, özel etki alanı adınızı ekleyebilirsiniz.

1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

1. Herhangi bir sayfadan *Azure Active Directory* arayın ve seçin. Ardından özel **etki alanı adları**  >  **özel etki alanı Ekle**' yi seçin.

    ![Özel etki alanı adları sayfası, gösterilen özel etki alanı Ekle](media/add-custom-domain/add-custom-domain.png)

1. **Özel etki alanı adı** alanına kuruluşunuzun yeni adını girin, bu örnekte, *contoso.com*. **Etki alanı ekle** seçeneğini belirleyin.

    ![Özel etki alanı adları sayfası, özel etki alanı Ekle sayfası](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Bunun düzgün çalışması için *. com*, *.net* veya herhangi bir üst düzey uzantı dahil etmeniz gerekir.

    Doğrulanmamış etki alanı eklendi. DNS bilgilerinizi gösteren **contoso.com** sayfası görüntülenir. Bu bilgileri kaydedin. Daha sonra DNS 'yi yapılandırmak için bir TXT kaydı oluşturmanız gerekir.

    ![DNS girişi bilgileri içeren contoso sayfası](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>DNS bilgilerinizi etki alanı kaydedicisine ekleme

Özel etki alanı adınızı Azure AD 'ye ekledikten sonra, etki alanı kayıt şirketinize dönüp Azure AD DNS bilgilerini kopyalanmış TXT dosyanıza eklemeniz gerekir. Etki alanınız için bu TXT kaydının oluşturulması, etki alanı adınızın sahipliğini doğrular.

Etki alanı kaydedicinizi geri dönüp, kopyalanmış DNS bilgilerinizi temel alan etki alanınız için yeni bir TXT kaydı oluşturun. Yaşam süresi (TTL) değerini 3600 saniye (60 dakika) olarak ayarlayın ve ardından kaydı kaydedin.

>[!IMPORTANT]
>İstediğiniz sayıda etki alanı adı kaydedebilirsiniz. Ancak, her etki alanı Azure AD 'den kendi TXT kaydını alır. Etki alanı kaydedicisinde TXT dosya bilgilerini girerken dikkatli olun. Yanlışlıkla yanlış veya yinelenen bilgileri girerseniz, yeniden deneyebilmeniz için TTL 'nin zaman aşımına uğrayana kadar beklemeniz gerekir (60 dakika).

## <a name="verify-your-custom-domain-name"></a>Özel etki alanı adınızı doğrulayın

Özel etki alanı adınızı kaydettikten sonra, Azure AD 'de geçerli olduğundan emin olun. Etki alanı kaydedicinizden Azure AD 'ye yayma, etki alanı kayıt şirketinize bağlı olarak birkaç gün sürebilir.

Özel etki alanı adınızı doğrulamak için şu adımları izleyin:

1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

1. Herhangi bir sayfadan *Azure Active Directory* arayıp seçin ve ardından **özel etki alanı adları**' nı seçin.

1. **Özel etki alanı adları**' nda özel etki alanı adını seçin. Bu örnekte, **contoso.com**' yi seçin.

    ![Fabrikam-özel etki alanı adları sayfası, contoso vurgulanmış](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. **Contoso.com** sayfasında, özel etki alanının düzgün şekilde kaydedildiğinden ve Azure AD için geçerli olduğundan emin olmak için **Doğrula** ' yı seçin.

    ![DNS giriş bilgileri ve Doğrula düğmesini içeren contoso sayfası](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Özel etki alanı adınızı doğruladıktan sonra doğrulama TXT veya MX Dosyanızı silebilirsiniz.

## <a name="common-verification-issues"></a>Yaygın doğrulama sorunları

Azure AD özel bir etki alanı adını doğrulayamıyorum, aşağıdaki önerileri deneyin:

- **En az bir saat bekleyip yeniden deneyin**. Azure AD’nin etki alanını doğrulayabilmesi için DNS kayıtlarının yayılması gerekir. Bu işlem bir saat veya daha fazla sürebilir.

- **DNS kaydının doğru olduğundan emin olun.** Etki alanı adı kaydedici sitesine geri dönün. Girişin orada olduğundan ve Azure AD tarafından belirtilen DNS girişi bilgileriyle eşleştiğinden emin olun.

  Kayıt, kaydedici sitesinde güncelleyemiyorum, girişi ekleme ve doğru doğrulama izinleri olan biriyle girişi paylaşabilirsiniz.

- **Etki alanı adının zaten başka bir dizinde kullanımda olmadığından emin olun.** Bir etki alanı adı yalnızca tek bir dizinde doğrulanabilir. Etki alanı adınız Şu anda başka bir dizinde doğrulanırsa, yeni dizinde da doğrulanamaz. Bu çoğaltma sorununu çözmek için, etki alanı adını eski dizinden silmeniz gerekir. Etki alanı adlarını silme hakkında daha fazla bilgi için bkz. [özel etki alanı adlarını yönetme](../enterprise-users/domains-manage.md).

- **Yönetilmeyen Power BI kiracılarınız olmadığından emin olun.** Kullanıcılarınız self servis kayıt aracılığıyla Power BI etkinleştiriyorsa ve kuruluşunuz için yönetilmeyen bir kiracı oluşturup, PowerShell kullanarak yönetimi bir iç veya dış yönetici olarak almanız gerekir. Daha fazla bilgi için bkz. [Azure Active Directory’de yönetilmeyen bir dizini yönetici olarak devralma](../enterprise-users/domains-admin-takeover.md).

## <a name="next-steps"></a>Sonraki adımlar

- Dizininize başka bir genel yönetici ekleyin. Daha fazla bilgi için bkz. [Roller ve yöneticiler atama](active-directory-users-assign-role-azure-portal.md).

- Etki alanınızı Kullanıcı ekleyin. Daha fazla bilgi için bkz. [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md).

- Azure AD 'de etki alanı adı bilgilerinizi yönetin. Daha fazla bilgi için bkz. [özel etki alanı adlarını yönetme](../enterprise-users/domains-manage.md).

- Azure Active Directory birlikte kullanmak istediğiniz Windows Server 'ın şirket içi sürümleri varsa, bkz. Şirket [içi dizinlerinizi Azure Active Directory Ile tümleştirme](../hybrid/whatis-hybrid-identity.md).