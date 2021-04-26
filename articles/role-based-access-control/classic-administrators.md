---
title: Azure klasik abonelik yöneticileri
description: Azure Co-Administrator ve hizmet yöneticisi rollerinin nasıl ekleneceğini ve değiştirileceğini ve hesap yöneticisinin nasıl görüntüleneceğini açıklar.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b61636d6048f63ae962d4f755a29c02e6785d5e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557533"
---
# <a name="azure-classic-subscription-administrators"></a>Azure klasik abonelik yöneticileri

Microsoft, Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak Azure kaynaklarına erişimi yönetmenizi önerir. Ancak yine de klasik dağıtım modelini kullanıyorsanız, klasik bir abonelik yönetici rolü kullanmanız gerekir: Hizmet Yöneticisi ve ortak yönetici. Daha fazla bilgi için bkz. [Azure Resource Manager ile klasik dağıtım](../azure-resource-manager/management/deployment-models.md).

Bu makalede Co-Administrator ve hizmet yöneticisi rollerinin nasıl ekleneceği ve değiştirileceği ve hesap yöneticisinin nasıl görüntüleneceği açıklanır.

## <a name="add-a-co-administrator"></a>Ortak Yönetici ekleme

> [!TIP]
> Yalnızca kullanıcının [Azure hizmet yönetimi PowerShell modülünü](/powershell/module/servicemanagement/azure.service)kullanarak klasik Azure dağıtımlarını yönetmesi gerekiyorsa Co-Administrator eklemeniz gerekir. Kullanıcı yalnızca klasik kaynakları yönetmek için Azure portal kullanıyorsa, Kullanıcı için klasik yönetici eklemeniz gerekmez.

1. [Azure Portal](https://portal.azure.com) bir hizmet yöneticisi veya ortak yönetici olarak oturum açın.

1. [Abonelikler](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)'i açın ve bir abonelik seçin.

    Co-Administrators, yalnızca abonelik kapsamında atanabilir.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. **Klasik Yöneticiler** sekmesine tıklayın.

    ![Klasik yöneticileri açan ekran görüntüsü](./media/classic-administrators/classic-administrators.png)

1. Ortak   >  yönetici Ekle bölmesini açmak için **ortak yönetici Ekle** ' ye tıklayın.

    Ortak yönetici Ekle seçeneği devre dışıysa, izinleriniz yoktur.

1. Eklemek istediğiniz kullanıcıyı seçin ve **Ekle**' ye tıklayın.

    ![Ortak yönetici ekleyen ekran görüntüsü](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Co-Administrator olarak Konuk Kullanıcı ekleme

Konuk kullanıcıyı ortak yönetici olarak eklemek için, önceki [ortak yönetici ekleme](#add-a-co-administrator) bölümünde ile aynı adımları izleyin. Konuk kullanıcının aşağıdaki ölçütlere uyması gerekir:

- Konuk kullanıcının dizininizde bir varlık olması gerekir. Bu, kullanıcının dizininize davet edildiği ve daveti kabul ettiği anlamına gelir.

Dizininize Konuk Kullanıcı ekleme hakkında daha fazla bilgi için, bkz. [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../active-directory/external-identities/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Konuk kullanıcılar için farklılıklar

Co-Administrator rolün atandığı Konuk kullanıcılar, Co-Administrator rolüne sahip üye kullanıcılarla karşılaştırıldığında bazı farklılıklar görebilirler. Şu senaryoyu göz önünde bulundurun:

- Bir Azure AD hesabı (iş veya okul hesabı) olan A kullanıcısı, bir Azure aboneliği için hizmet yöneticisidir.
- B kullanıcısının bir Microsoft hesabı vardır.
- A kullanıcısı, B kullanıcısına Co-Administrator rolünü atar.
- B kullanıcısı neredeyse her şeyi yapabilir, ancak Azure AD dizininde uygulama kaydedemez veya kullanıcıları arayabilir.

B kullanıcısının her şeyi yönetebileceğini beklemeniz gerekir. Bu farkın nedeni, Microsoft hesabı aboneliğe üye kullanıcı yerine Konuk Kullanıcı olarak eklendiğine yöneliktir. Konuk kullanıcıların Azure AD 'de üye kullanıcılarla karşılaştırıldığında farklı varsayılan izinleri vardır. Örneğin, üye kullanıcılar Azure AD 'de diğer kullanıcıları okuyabilir ve Konuk kullanıcılar tarafından kullanılamaz. Üye kullanıcılar, Azure AD 'de yeni hizmet sorumlularını kaydedebilir ve Konuk kullanıcılar bu hizmetleri kullanamaz.

Konuk kullanıcının bu görevleri gerçekleştirebilmesi gerekiyorsa, Konuk kullanıcının ihtiyacı olan belirli Azure AD rollerinin atanması olası bir çözümdür. Örneğin, önceki senaryoda, diğer kullanıcıları okumak için [Dizin okuyucuları](../active-directory/roles/permissions-reference.md#directory-readers) rolünü atayabilir ve hizmet sorumlularını oluşturabilmeniz Için [uygulama geliştirici](../active-directory/roles/permissions-reference.md#application-developer) rolünü atayabilirsiniz. Üye ve Konuk kullanıcılar ve bunların izinleri hakkında daha fazla bilgi için bkz. [Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?](../active-directory/fundamentals/users-default-permissions.md). Konuk kullanıcılar için erişim verme hakkında daha fazla bilgi için, bkz. [Azure Portal kullanarak dış Konuk kullanıcılara Azure rolleri atama](role-assignments-external-users.md).

[Azure yerleşik rollerinin](../role-based-access-control/built-in-roles.md) [Azure AD rollerinden](../active-directory/roles/permissions-reference.md)farklı olduğunu unutmayın. Yerleşik roller Azure AD 'ye hiçbir erişim vermez. Daha fazla bilgi için bkz. [farklı rolleri anlama](../role-based-access-control/rbac-and-directory-admin-roles.md).

Üye kullanıcıları ve konuk kullanıcıları karşılaştıran bilgiler için, bkz. [Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Co-Administrator kaldırma

1. [Azure Portal](https://portal.azure.com) bir hizmet yöneticisi veya ortak yönetici olarak oturum açın.

1. [Abonelikler](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)'i açın ve bir abonelik seçin.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. **Klasik Yöneticiler** sekmesine tıklayın.

1. Kaldırmak istediğiniz Co-Administrator yanına onay işareti ekleyin.

1. **Kaldır**’a tıklayın.

1. Görüntülenen ileti kutusunda **Evet**' e tıklayın.

    ![Ortak yöneticiyi kaldıran ekran görüntüsü](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Hizmet Yöneticisini değiştirme

Yalnızca hesap yöneticisi bir abonelik için hizmet yöneticisini değiştirebilir. Varsayılan olarak, bir Azure aboneliğine kaydolduğunuzda, hizmet yöneticisi hesap yöneticisiyle aynı olur. Hesap Yöneticisi rolüne sahip kullanıcının Azure portal erişimi yok. Hizmet Yöneticisi rolüne sahip kullanıcının Azure portal tam erişimi vardır. Hesap Yöneticisi ve hizmet yöneticisi aynı kullanıcı ise ve hizmet yöneticisini farklı bir kullanıcıyla değiştirirseniz, hesap yöneticisi Azure portal erişimi kaybeder. Ancak hesap yöneticisi her zaman hesap merkezini kullanarak hizmet yöneticisini kendilerine geri değiştirebilir.

Azure portal hizmet yöneticisini değiştirmek için aşağıdaki adımları izleyin.

1. [Hizmet yöneticisini değiştirme sınırlamalarını](#limitations-for-changing-the-service-administrator)denetleyerek senaryonuzun desteklendiğinden emin olun.

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.

1. **Maliyet yönetimi + faturalandırma** ' i açın ve bir abonelik seçin.

1. Sol gezinti bölmesinde **Özellikler**' e tıklayın.

1. **Hizmet Yöneticisi**' ne tıklayın.

    ![Azure portal abonelik özelliklerini gösteren ekran görüntüsü](./media/classic-administrators/service-admin.png)

1. **Hizmet yöneticisini Düzenle** sayfasında, yeni hizmet yöneticisinin e-posta adresini girin.

    ![Hizmet yöneticisini Düzenle sayfasını gösteren ekran görüntüsü](./media/classic-administrators/service-admin-edit.png)

1. Değişikliği kaydetmek için **Tamam** 'a tıklayın.

### <a name="limitations-for-changing-the-service-administrator"></a>Hizmet yöneticisini değiştirme sınırlamaları

Azure aboneliği başına yalnızca bir hizmet yöneticisi olabilir. Hizmet yöneticisinin değiştirilmesi, hesap yöneticisinin bir Microsoft hesabı mı yoksa bir Azure AD hesabı mı (iş veya okul hesabı) olup olmadığına bağlı olarak farklı davranaacaktır.

| Hesap yönetici hesabı | Hizmet Yöneticisi farklı bir Microsoft hesabı değiştirilebilir mi? | Hizmet yöneticisini aynı dizindeki bir Azure AD hesabına değiştirebilir mi? | Hizmet Yöneticisi 'ni farklı bir dizinde Azure AD hesabı olarak değiştirebilir miyim? |
| --- | --- | --- | --- |
| Microsoft hesabı | Yes | Hayır | Hayır |
| Azure AD hesabı | Yes | Yes | Hayır |

Hesap Yöneticisi bir Azure AD hesabı ise, hizmet yöneticisini aynı dizinde bir Azure AD hesabı olarak değiştirebilirsiniz, ancak farklı bir dizinde kullanamazsınız. Örneğin, abby@contoso.com hizmet yöneticisini olarak değiştirebilir bob@contoso.com , ancak john@notcontoso.com john@notcontoso.com contoso.com dizininde bir varlık olmadığı müddetçe hizmet yöneticisini olarak değiştiremezsiniz.

Microsoft hesapları ve Azure AD hesapları hakkında daha fazla bilgi için bkz. [Azure Active Directory nedir?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Hesap Yöneticisini görüntüleme

Hesap Yöneticisi, başlangıçta Azure aboneliğine kaydolan ve aboneliğin fatura sahibi olarak sorumlu olan Kullanıcı olur. Bir aboneliğin hesap yöneticisini değiştirmek için bkz. bir [Azure aboneliğinin sahipliğini başka bir hesaba aktarma](../cost-management-billing/manage/billing-subscription-transfer.md).

Hesap yöneticisini görüntülemek için aşağıdaki adımları izleyin.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Maliyet yönetimi + faturalandırma** ' i açın ve bir abonelik seçin.

1. Sol gezinti bölmesinde **Özellikler**' e tıklayın.

    Aboneliğin Hesap Yöneticisi, **Hesap** Yöneticisi kutusunda görüntülenir.

    ![Hesap yöneticisini gösteren ekran görüntüsü](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Farklı rolleri anlama](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)
* [Azure aboneliği yöneticileri ekleme veya değiştirme](../cost-management-billing/manage/add-change-subscription-administrator.md)