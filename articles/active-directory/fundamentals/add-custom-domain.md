---
title: Özel etki alanınızı - Azure Active Directory'ye ekleme | Microsoft Docs
description: Azure Active Directory'yi kullanarak özel bir etki alanı ekleme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073510"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Azure Active Directory portalı kullanarak özel etki alanı adınızı ekleme

Her yeni Azure AD kiracısı, *\<domainname >. onmicrosoft. com*adlı bir ilk etki alanı adıyla gelir. İlk etki alanı adını değiştiremez veya silemezsiniz, ancak kuruluşunuzun adlarını da ekleyebilirsiniz. Özel etki alanı adları eklemek, kullanıcılarınıza tanıdık *\@contoso.com*gibi kullanıcı adları oluşturmanıza yardımcı olur.

## <a name="before-you-begin"></a>Başlamadan önce

Özel bir etki alanı adı ekleyebilmeniz için, etki alanı adını bir etki alanı kaydedicisi ile oluşturun. Akredite bir etki alanı kayıt şirketi için bkz. [ICANN-Accredited kaydedicilerin](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Azure AD dizininizi oluşturma

Etki alanı adınızı aldıktan sonra ilk Azure AD dizininizi oluşturabilirsiniz. Aboneliğiniz için **sahip** rolüne sahip bir hesap kullanarak dizininiz için Azure Portal oturum açın.

İçindeki adımları izleyerek yeni dizininizi oluşturma [kuruluşunuz için yeni bir kiracı](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>Kiracı oluşturan kişi, otomatik olarak bu Kiracı için genel yönetici olur. Genel yönetici, Kiracı için başka Yöneticiler ekleyebilirsiniz.

Abonelik rolleri hakkında daha fazla bilgi için bkz. [Azure RBAC rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Şirket içi Windows Server AD 'nizi Azure AD ile federasyona eklemek istiyorsanız, dizinlerinizi eşitlemeniz için Azure AD Connect aracını çalıştırdığınızda **Bu etki alanını yerel Active Directory çoklu oturum açma için yapılandırmayı planlıyorum** ' ı seçmeniz gerekir.
>
> Ayrıca aynı etki alanı adını sihirbazın şirket içi dizininizi **Azure AD Etki Alanı** ile birleştirme adımında da kaydetmeniz gerekir. Kurulumun nasıl göründüğünü görmek için bkz. [Federasyon için seçili Azure AD etki alanını doğrulayın](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Azure AD Connect aracınız yoksa [buradan indirebilirsiniz](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Özel etki alanı adınızı Azure AD'ye ekleme

Dizininizi oluşturduktan sonra özel etki alanı adı ekleyebilirsiniz.

1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

1. Herhangi bir sayfadan *Azure Active Directory* arayın ve seçin. Özel etki alanı **eklemek** > **özel etki alanı adları** ' nı seçin.

    ![Özel etki alanı adları sayfası, gösterilen özel etki alanı Ekle](media/add-custom-domain/add-custom-domain.png)

1. **Özel etki alanı adı**alanına kuruluşunuzun yeni adını girin, bu örnekte, *contoso.com*. **Etki alanı ekle**'yi seçin.

    ![Özel etki alanı adları sayfası, özel etki alanı Ekle sayfası](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Bunun düzgün çalışması için *. com*, *.net*veya herhangi bir üst düzey uzantı dahil etmeniz gerekir.

    Doğrulanmamış etki alanı eklendi. DNS bilgilerinizi gösteren **contoso.com** sayfası görüntülenir. Bu bilgileri kaydedin. Daha sonra DNS 'yi yapılandırmak için bir TXT kaydı oluşturmanız gerekir.

    ![Contoso DNS girdisi bilgi sayfası](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Etki alanı kayıt şirketi için DNS bilgilerinizi ekleyin

Azure AD'ye özel etki alanı adınızı ekledikten sonra için etki alanı kayıt şirketinizde dönün ve Azure AD DNS bilgilerini, kopyalanan bir TXT dosyasından eklemeniz gerekir. Etki alanınız için bu TXT kaydının oluşturulması, etki alanı adınızın sahipliğini doğrular.

Etki alanı kaydedicinizi geri dönüp, kopyalanmış DNS bilgilerinizi temel alan etki alanınız için yeni bir TXT kaydı oluşturun. Yaşam süresi (TTL) değerini 3600 saniye (60 dakika) olarak ayarlayın ve ardından kaydı kaydedin.

>[!IMPORTANT]
>İstediğiniz sayıda etki alanı adları kaydedebilirsiniz. Ancak, her etki alanı kendi TXT kaydı, Azure AD'den alır. Etki alanı kaydedicisinde TXT dosya bilgilerini girerken dikkatli olun. Yanlışlıkla yanlış veya yinelenen bilgileri girerseniz, yeniden deneyebilmeniz için TTL 'nin zaman aşımına uğrayana kadar beklemeniz gerekir (60 dakika).

## <a name="verify-your-custom-domain-name"></a>Özel etki alanı adınızı doğrulayın

Özel etki alanı adınızı kaydettikten sonra, Azure AD 'de geçerli olduğundan emin olun. Etki alanı kaydedicinizden Azure AD 'ye yayma, etki alanı kayıt şirketinize bağlı olarak birkaç gün sürebilir.

Özel etki alanı adınızı doğrulamak için şu adımları izleyin:

1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

1. Herhangi bir sayfadan *Azure Active Directory* arayıp seçin ve ardından **özel etki alanı adları**' nı seçin.

1. **Özel etki alanı adları**' nda özel etki alanı adını seçin. Bu örnekte, **contoso.com**' yi seçin.

    ![Fabrikam - vurgulanmış contoso ile özel etki alanı adları sayfası](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. **Contoso.com** sayfasında, özel etki alanının düzgün şekilde kaydedildiğinden ve Azure AD için geçerli olduğundan emin olmak için **Doğrula** ' yı seçin.

    ![DNS girişi bilgilerini ve Doğrula düğmesine contoso sayfası](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Özel etki alanı adınızı doğruladıktan sonra doğrulama TXT veya MX Dosyanızı silebilirsiniz.

## <a name="common-verification-issues"></a>Sık karşılaşılan doğrulama sorunları

Azure AD'ye özel etki alanı adını doğrulayamıyorsanız, aşağıdaki önerileri deneyin:

- **En az bir saat bekleyin ve yeniden deneyin**. Azure AD’nin etki alanını doğrulayabilmesi için DNS kayıtlarının yayılması gerekir. Bu işlem bir saat veya daha fazla sürebilir.

- **DNS kaydı doğru olduğundan emin olun.** Etki alanı adı kaydedici sitesine geri dönün. Girişin orada olduğundan ve Azure AD tarafından belirtilen DNS girişi bilgileriyle eşleştiğinden emin olun.

  Kayıt, kaydedici sitesinde güncelleyemiyorum, girişi ekleme ve doğru doğrulama izinleri olan biriyle girişi paylaşabilirsiniz.

- **Etki alanı adı zaten başka bir dizindeki kullanımda olmadığından emin olun.** Bir etki alanı adı yalnızca tek bir dizinde doğrulanabilir. Etki alanı adınız Şu anda başka bir dizinde doğrulanırsa, yeni dizinde da doğrulanamaz. Bu çoğaltma sorunu gidermek için etki alanı adı eski dizinden silmeniz gerekir. Etki alanı adlarını silme hakkında daha fazla bilgi için bkz. [özel etki alanı adlarını yönetme](../users-groups-roles/domains-manage.md).

- **Yönetilmeyen hiçbir Power BI Kiracı yoksa emin olun.** Kullanıcılarınızın Power BI Self etkinleştirmiş ve kuruluşunuz için yönetilmeyen bir kiracı oluşturuldu, iç veya dış Yöneticisi olarak Management PowerShell kullanarak atmanız durumunda. Daha fazla bilgi için bkz. [Azure Active Directory’de yönetilmeyen bir dizini yönetici olarak devralma](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Sonraki adımlar

- Başka bir genel yönetici, dizininize ekleyin. Daha fazla bilgi için bkz. [Roller ve yöneticiler atama](active-directory-users-assign-role-azure-portal.md).

- Etki alanınızı Kullanıcı ekleyin. Daha fazla bilgi için bkz. [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md).

- Azure AD etki alanı adı bilgilerinizi yönetin. Daha fazla bilgi için bkz. [özel etki alanı adlarını yönetme](../users-groups-roles/domains-manage.md).

- Şirket içi Azure Active Directory ile birlikte kullanmak üzere istediğiniz Windows Server sürümleri varsa [şirket içi dizinlerinizi Azure Active Directory ile tümleştirme](../connect/active-directory-aadconnect.md).
