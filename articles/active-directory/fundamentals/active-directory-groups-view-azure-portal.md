---
title: Grupları ve üyeleri görüntülemek için hızlı başlangıç-Azure Active Directory | Microsoft Docs
description: Arama ve kuruluşunuzun gruplar ve atanan üyelerini görüntüleme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b4f2f6e86ce076e7c0464609252b027499a8307
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942877"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>Hızlı Başlangıç: Azure Active Directory içinde kuruluşunuzun gruplarını ve üyelerini görüntüleme
Azure portalı kullanarak kuruluşunuzun mevcut gruplarını ve grup üyelerini görüntüleyebilirsiniz. Gruplar, büyük olasılıkla kısıtlı uygulama ve hizmetler için aynı erişim ve izinlere ihtiyacı olan kullanıcıları (üyeleri) yönetmek için kullanılır.

Bu hızlı başlangıçta, kuruluşunuzun tüm mevcut gruplarını ve atanmış üyelerini görüntüleyeceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce şunları gerçekleştirmeniz gerekir:

- Bir Azure Active Directory kiracısı oluşturun. Daha fazla bilgi için, bkz. [Azure Active Directory portalına erişme ve yeni bir kiracı oluşturma](active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın
Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açmanız gerekir.

## <a name="create-a-new-group"></a>Yeni grup oluşturma 
_MDM ilkesi - Batı_ adlı yeni bir grup oluşturun. Grup oluşturma hakkında daha fazla bilgi için, bkz. [Temel bir grup oluşturma ve üye ekleme](active-directory-groups-create-azure-portal.md).

1. **Azure Active Directory**’yi, **Gruplar**’ı ve ardından **Yeni grup**’u seçin.

2. **Grup** sayfasını tamamlayın:
    
    - **Grup türü:** **Güvenlik** seçin
    
    - **Grup adı:** _MDM ilkesi türü-Batı_
    
    - **Üyelik türü:** **Atandı**' ı seçin.

3. **Oluştur**’u seçin.

## <a name="create-a-new-user"></a>Yeni kullanıcı oluşturma
_Alain Charon_ adı yeni bir kullanıcı oluşturun. Bir kullanıcı grup üyesi olarak eklenmeden önce mevcut olmalıdır. Kullanıcıların oluşturulacağı doğrulanmış etki alanı adını almak için önce "özel etki alanı adları" sekmesini kontrol edin. Kullanıcı oluşturma hakkında daha fazla bilgi için, bkz. [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md).

1. **Azure Active Directory**’yi, **Kullanıcılar**’ı ve ardından **Yeni kullanıcı**’yı seçin.

2. **Kullanıcı** sayfasını tamamlayın:

    - **Ada** _Charon_ yazın.

    - **Kullanıcı adı:** Contoso.com yazın. *\@*

3. **Parola** kutusunda sağlanan otomatik olarak oluşturulmuş parolayı kopyalayın ve ardından **Oluştur** seçeneğini belirleyin.

## <a name="add-a-group-member"></a>Grup üyesi ekleme
Şimdi bir grubunuz ve kullanıcınız olduğuna göre, _Alain Charon_ ’u _MDM ilkesi - Batı_ grubuna üye olarak ekleyebilirsiniz. Grup üyelerini ekleme hakkında daha fazla bilgi için, bkz. [Grup üyelerini ekleme veya kaldırma](active-directory-groups-members-azure-portal.md).

1. **Azure Active Directory** > **Gruplar**'ı seçin.

2. **Gruplar - Tüm gruplar** sayfasından, **MDM ilkesi - Batı** grubunu arayın ve seçin.

3. **MDM ilkesi - Batı Genel Bakışı** sayfasında, **Yönet** alanından **Üyeler** seçeneğini belirleyin.

4. **Üye ekle**’yi seçin ve ardından **Alain Charon** öğesini arayıp seçin.

5. **Seç**’i seçin.

## <a name="view-all-groups"></a>Tüm grupları görüntüleme
Kuruluşunuz için tüm grupları Azure portalın **Gruplar - Tüm gruplar** sayfasında görebilirsiniz.

- Azure **Active Directory** > **Gruplar**’ı seçin.

    **Gruplar - Tüm gruplar** sayfası görüntülenir ve tüm etkin gruplarınız gösterilir.

    ![Tüm mevcut grupları gösteren Gruplar - Tüm gruplar sayfası](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>Grubu arama
**MDM ilkesi - Batı** grubunu bulmak için **Gruplar - Tüm gruplar** sayfasında arama yapın.

1. **Gruplar - Tüm gruplar** sayfasından, **Arama** kutusuna _MDM_ yazın.

    _MDM ilkesi - Batı_ grubu dahil arama sonuçları **Arama** kutusu altında gösterilir.

    ![Arama kutusu doldurulmuş Gruplar - Tüm gruplar sayfası](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. **MDM ilkesi - Batı** grubunu seçin.

4. **MDM ilkesi - Batı Genel Bakışı** sayfasında, grubun üye sayısı dahil grup bilgilerini görüntüleyin.

    ![Üye bilgileriyle MDM ilkesi - Batı Genel Bakışı sayfası](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>Grup üyelerini görüntüleme
Grubu bulduğunuza göre, atanan tüm üyelerini görüntüleyebilirsiniz.

- **Yönet** alanından **Üyeler**’i seçin ve ardından _Alain Charon_ da dahil olmak üzere bu gruba atanan üye adlarının tam listesini gözden geçirin.

    ![MDM ilkesi - Batı grubuna atanan üyelerin listesi](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu grup, bu belgenin **Nasıl yapılır kılavuzları** bölümündeki çeşitli nasıl yapılır işlemlerinde kullanılır. Ancak bu grubu kullanmak istemiyorsanız, aşağıdaki adımları kullanarak grubu ve atanmış üyelerini silebilirsiniz:

1. **Gruplar - Tüm gruplar** sayfasında **MDM ilkesi - Batı** grubu için arama yapın.

2.  **MDM ilkesi - Batı** grubunu seçin.

    **MDM ilkesi - Batı Genel Bakışı** sayfası görüntülenir.

3. **Sil**’i seçin.

    Grup ve ilişkili üyeleri silinir.

    ![Sil bağlantısı vurgulanan MDM ilkesi – Batı Genel Bakışı sayfası](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >Bu işlem Alain Charon’u silmez, yalnızca silinen gruptaki üyeliğini siler.

## <a name="next-steps"></a>Sonraki adımlar
Azure AD aboneliğinizle bir aboneliği ilişkilendirmeyi öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure aboneliği ilişkilendirme](active-directory-how-subscriptions-associated-directory.md)
