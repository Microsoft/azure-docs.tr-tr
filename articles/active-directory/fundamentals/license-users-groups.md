---
title: Lisans atama veya kaldırma-Azure Active Directory | Microsoft Docs
description: Kullanıcılarınıza veya gruplarınızdan Azure Active Directory lisansları atamaya veya kaldırmaya ilişkin yönergeler.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504415"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Azure Active Directory portalında Lisans atama veya kaldırma

Birçok Azure Active Directory (Azure AD) Hizmetleri, bu hizmet için kullanıcılarınızın veya grupların (ve ilişkili üyelerin) her birini lisanslayabilmeniz gerekir. Yalnızca etkin lisansa sahip kullanıcılar, doğru olan lisanslı Azure AD hizmetlerine erişebilir ve bunları kullanabilir. Lisanslar, kiracı başına uygulanır ve diğer kiracılara aktarılmaz. 

## <a name="available-license-plans"></a>Kullanılabilir lisans planları

Azure AD hizmeti için aşağıdakiler de dahil olmak üzere birkaç lisans planı mevcuttur:

- Azure AD Ücretsiz

- Azure AD Premium P1

- Azure AD Premium P2

Her lisans planı ve ilgili lisanslama ayrıntıları hakkında belirli bilgiler için bkz. [Hangi lisansa ihtiyacım var?](https://azure.microsoft.com/pricing/details/active-directory/). Azure AD Premium lisans planlarına kaydolmak için [buraya](./active-directory-get-started-premium.md)bakın.

Tüm Microsoft Hizmetleri tüm konumlarda kullanılabilir değildir. Bir lisansın bir gruba atanabilmesi için önce tüm Üyeler için **kullanım konumunu** belirtmeniz gerekir. Bu değeri, Azure AD 'de **Azure Active Directory &gt; kullanıcıları &gt; profil &gt; ayarları** alanında ayarlayabilirsiniz. Kullanım konumu belirtilmemiş olan tüm kullanıcılar, Azure AD kuruluşunun konumunu devralır.

## <a name="view-license-plans-and-plan-details"></a>Lisans planlarını ve plan ayrıntılarını görüntüleme

Bireysel lisanslar dahil olmak üzere kullanılabilir hizmet planlarınızı görüntüleyebilir, bekleyen süre sonu tarihlerini denetleyebilir ve kullanılabilir atamaların sayısını görüntüleyebilirsiniz.

### <a name="to-find-your-service-plan-and-plan-details"></a>Hizmet planınızı ve plan ayrıntılarını bulmak için

1. Azure AD kuruluşunuzda bir lisans yöneticisi hesabı kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.

1. **Azure Active Directory**' yi seçin ve ardından **lisanslar**' ı seçin.

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="Lisanslar sayfası, satın alınan hizmet sayısı ve atanmış lisanslar":::

1. Tüm ürünler sayfasını görüntülemek için **Tüm** Ürünler ' i seçin ve lisans planlarınız için **Toplam**, **atanan**, **kullanılabilir** ve **süresi dolan en erken** numaraları görüntüleyin.

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="Hizmetler sayfası-hizmet lisans planlarıyla ilişkili lisans bilgileri":::

    > [!NOTE]
    > Sayılar şu şekilde tanımlanır: 
    > - Toplam: satın alınan toplam lisans sayısı
    > - Atandı: kullanıcılara atanan lisansların sayısı
    > - Kullanılabilir: atama için kullanılabilen lisansların sayısı yakında dolacak
    > - Yakında süresi doluyor: süresi yakında dolacak olan lisans sayısı

1. Lisanslı kullanıcılarını ve gruplarını görmek için bir plan adı seçin.

## <a name="assign-licenses-to-users-or-groups"></a>Kullanıcılara veya gruplara lisans atama

Lisanslı bir Azure AD hizmeti kullanması gereken herkesin uygun lisansa sahip olduğundan emin olun. Kullanıcılara veya tüm bir gruba lisans hakları ekleyebilirsiniz.

### <a name="to-assign-a-license-to-a-user"></a>Bir kullanıcıya lisans atamak için

1. **Ürünler** sayfasında, kullanıcıya atamak istediğiniz lisans planının adını seçin.

    ![Vurgulanan hizmet lisans planına sahip hizmetler sayfası](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Lisans planına Genel Bakış sayfasında **ata**' yı seçin.

    ![Vurgulanan atama seçeneğiyle Hizmetler sayfası](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. **Ata** sayfasında, **Kullanıcılar ve gruplar**' ı seçin ve ardından lisansı atadığınız kullanıcıyı arayıp seçin.

    ![Vurgulanan arama ve seçim seçeneklerini içeren lisans sayfası ata](media/license-users-groups/assign-license-blade-with-highlight.png)

1. **Atama seçeneklerini** belirleyin, uygun lisans seçeneklerinin açık olduğundan emin olun ve ardından **Tamam**' ı seçin.

    ![Lisans seçeneği sayfasında, lisans planındaki tüm seçeneklerle kullanılabilir](media/license-users-groups/license-option-blade-assignments.png)

    Bir kullanıcının seçili olduğunu ve atamaların yapılandırıldığını göstermek için **Lisans ata** sayfası güncellenir.

    > [!NOTE]
    > Tüm Microsoft Hizmetleri tüm konumlarda kullanılabilir değildir. Bir lisansın bir kullanıcıya atanabilmesi için önce **kullanım konumunu** belirtmeniz gerekir. Bu değeri, Azure AD 'de **Azure Active Directory &gt; kullanıcıları &gt; profil &gt; ayarları** alanında ayarlayabilirsiniz. Kullanım konumu belirtilmemiş olan tüm kullanıcılar, Azure AD kuruluşunun konumunu devralır.

1. **Ata**’yı seçin.

    Kullanıcı lisanslı kullanıcılar listesine eklenir ve dahil edilen Azure AD hizmetlerine erişimi vardır.
    > [!NOTE]
    > Lisanslar ayrıca kullanıcının **lisanslar** sayfasından bir kullanıcıya doğrudan atanabilir. Bir kullanıcının bir grup üyeliğiyle atanmış bir lisansı varsa ve aynı lisansı kullanıcıya doğrudan atamak istiyorsanız, yalnızca 1. adımda bahsedilen **Ürünler** sayfasından yapılabilir.

### <a name="to-assign-a-license-to-a-group"></a>Bir gruba lisans atamak için

1. **Ürünler** sayfasında, kullanıcıya atamak istediğiniz lisans planının adını seçin.

    ![Ürün dikey penceresi, vurgulanan ürün lisans planı](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. **Azure Active Directory Premium plan 2** sayfasında **ata**' yı seçin.

    ![Vurgulanan atama seçeneğiyle ürünler sayfası](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. **Ata** sayfasında, **Kullanıcılar ve gruplar**' ı seçin ve ardından lisansı atadığınız grubu bulun ve seçin.

    ![Vurgulanan aramayla lisans sayfası atayın ve seçenekleri belirleyin 2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. **Atama seçeneklerini** belirleyin, uygun lisans seçeneklerinin açık olduğundan emin olun ve ardından **Tamam**' ı seçin.

    ![Lisans seçeneği sayfasında, lisans planı 2 ' de kullanılabilir tüm seçenekler mevcuttur](media/license-users-groups/license-option-blade-group-assignments.png)

    Bir kullanıcının seçili olduğunu ve atamaların yapılandırıldığını göstermek için **Lisans ata** sayfası güncellenir.

1. **Ata**’yı seçin.

    Grup, lisanslı gruplar listesine eklenir ve tüm üyelerin dahil edilen Azure AD hizmetlerine erişimi vardır.

## <a name="remove-a-license"></a>Lisansı kaldırma

Bir kullanıcının Azure AD Kullanıcı sayfasından bir lisansı, Grup atama için gruba genel bakış sayfasından veya bir lisansın Kullanıcı ve gruplarını görmek için Azure AD **lisanslar** sayfasından başlayarak kaldırabilirsiniz.

### <a name="to-remove-a-license-from-a-user"></a>Bir kullanıcıdan lisans kaldırmak için

1. Hizmet planının **lisanslı kullanıcılar** sayfasında, Lisansı artık olmayan kullanıcıyı seçin. Örneğin, _Charon_.

1. **Lisansı kaldır**' ı seçin.

    ![Lisansı kaldır seçeneği vurgulanmış olan lisanslı kullanıcılar sayfası](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Bir kullanıcının bir gruptan devraldığı lisanslar doğrudan kaldırılamaz. Bunun yerine, kullanıcıyı lisansı devraldıkları gruptan kaldırmanız gerekir.

### <a name="to-remove-a-license-from-a-group"></a>Bir gruptan lisans kaldırmak için

1. Lisans planının **lisanslı gruplar** sayfasında, Lisansı artık olmayan grubu seçin.

1. **Lisansı kaldır**' ı seçin.

    ![Lisansı kaldır seçeneği vurgulanmış olan lisanslı gruplar sayfası 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Azure AD ile eşitlenen şirket içi bir kullanıcı hesabı eşitleme için kapsam dışına düştüğünde veya eşitleme kaldırıldığında, Kullanıcı Azure AD 'de geçici olarak silinir. Bu durumda, kullanıcıya doğrudan veya grup tabanlı lisanslama aracılığıyla atanan lisanslar, **silinmek** yerine **askıya alındı** olarak işaretlenir.

## <a name="next-steps"></a>Sonraki adımlar

Lisanslarınızı atadıktan sonra, aşağıdaki işlemleri gerçekleştirebilirsiniz:

- [Lisans atama sorunlarını tanımla ve çözümle](../enterprise-users/licensing-groups-resolve-problems.md)

- [Lisanslama için bir gruba lisanslı kullanıcılar ekleme](../enterprise-users/licensing-groups-migrate-users.md)

- [Azure Active Directory 'de lisanslamayı yönetmek için grupları kullanarak senaryolar, sınırlamalar ve bilinen sorunlar](../enterprise-users/licensing-group-advanced.md)

- [Profil bilgilerini ekleme veya değiştirme](active-directory-users-profile-azure-portal.md)
