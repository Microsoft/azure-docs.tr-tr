---
title: PıM-Azure AD 'de konuklara Azure Kaynak rolleri atama | Microsoft Docs
description: Dış Konuk kullanıcıları davet etmeyi ve Azure AD Privileged Identity Management (PıM) içinde Azure Kaynak rolleri atamayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021933"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management ' de Konuk kullanıcıları davet edin ve Azure Kaynak rolleri atayın

Azure Active Directory (Azure AD) Konuk kullanıcılar, Azure AD 'deki işletmeden işletmeye (B2B) işbirliği olanaklarından bir parçasıdır. böylece, dış Konuk kullanıcılarını ve satıcıları Azure AD 'de Konuk olarak yönetebilirsiniz. B2B işbirliğini Azure AD Privileged Identity Management (PıM) ile birleştirdiğinizde, uyumluluk ve idare gereksinimlerinizi konuklara genişletebilirsiniz. Örneğin, bu Privileged Identity Management özelliklerini konuklarla Azure kimlik görevleri için kullanabilirsiniz:

- Belirli Azure kaynaklarına erişim atama
- Tam zamanında erişimi etkinleştir
- Atama süresini ve bitiş tarihini belirtin
- Etkin atama veya etkinleştirme sırasında çok faktörlü kimlik doğrulaması gerektir
- Erişim gözden geçirmeleri gerçekleştirin
- Uyarıları ve denetim günlüklerini kullanma

Bu makalede, kuruluşunuza bir konuğa davet etme ve Privileged Identity Management kullanarak Azure kaynaklarına erişimleri yönetme işlemlerinin nasıl yapılacağı açıklanır.

## <a name="when-would-you-invite-guests"></a>Konukları ne zaman davet edersiniz?

Aşağıda, kuruluşunuza konukları davet etmeye yönelik birkaç örnek verilmiştir:

- Yalnızca bir proje için Azure kaynaklarınıza erişmek üzere bir e-posta hesabına sahip olan bir dış şirket içi satıcıya izin verin.
- Şirket içi Active Directory Federasyon Hizmetleri (AD FS) kullanan büyük bir kuruluşta, harcama uygulamanıza erişmek için bir dış ortağa izin verin.
- Sorunları gidermek için kuruluşunuzda Azure kaynağına geçici olarak erişmek için (Microsoft desteği gibi) destek mühendislerine izin verin.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>B2B konukları kullanarak işbirliği nasıl çalışır?

B2B işbirliği kullandığınızda, bir dış kullanıcıyı kuruluşunuza Konuk olarak davet edebilirsiniz. Konuk, kuruluşunuzda bir kullanıcı olarak yönetilebilir, ancak bir konuğun Azure AD kuruluşunuzda değil, kendi ana kuruluşunda kimlik doğrulaması yapmak gerekir. Bu, konuğun artık ev kuruluşuna erişimi yoksa kuruluşunuza erişimi kaybetmemesi anlamına gelir. Örneğin, Konuk organizasyonlarını bırakırsa, herhangi bir şey yapmanıza gerek kalmadan Azure AD 'de paylaştığınız kaynaklara erişimi otomatik olarak kaybeder. B2B işbirliği hakkında daha fazla bilgi için bkz. [Azure ACTIVE DIRECTORY B2B 'de Konuk Kullanıcı erişimi nedir?](../b2b/what-is-b2b.md).

![Konuk kullanıcının kendi giriş dizininde kimlik doğrulamasının nasıl yapıldığını gösteren diyagram](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Konuk işbirliği ayarlarını denetleme

Konukları kuruluşa davet edebilmeniz için, Konuk işbirliği ayarlarınızı denetlemeniz gerekir.

1. [Azure portalda](https://portal.azure.com/) oturum açın.

1. **Azure Active Directory** > **Kullanıcı ayarları**' nı seçin.

1. **Dış işbirliği ayarlarını yönet**' i seçin.

    ![İzin, davet ve işbirliği kısıtlama ayarlarını gösteren dış işbirliği ayarları sayfası](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **Konuk davet eden rolündeki yöneticilerin ve kullanıcıların, davet** etmesini sağlamak Için anahtarı **Evet**olarak ayarlayın.

## <a name="invite-a-guest-and-assign-a-role"></a>Konuk davet etme ve rol atama

Privileged Identity Management kullanarak, bir konuğa davet edebilir ve bunları bir Azure Kaynak rolü için uygun hale getirebilirsiniz.

1. [Ayrıcalıklı rol yöneticisinin](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) veya [Kullanıcı Yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) rolünün üyesi olan bir kullanıcıyla [Azure Portal](https://portal.azure.com/) oturum açın.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure kaynakları**' nı seçin.

1. Yönetilen kaynakların listesini filtrelemek için **kaynak filtresini** kullanın.

1. Yönetmek istediğiniz kaynağı seçin (örneğin, kaynak, kaynak grubu, abonelik veya yönetim grubu).

    Kapsamı yalnızca konuğun ihtiyaçlarına göre ayarlamanız gerekir.

1. Yönet altında, Azure kaynakları rollerinin listesini görmek için **Roller** ' i seçin.

    ![Etkin ve uygun kullanıcı sayısını gösteren Azure kaynakları rol listesi](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kullanıcının ihtiyacı olacak en düşük rolü seçin.

    ![Seçilen rol sayfası bu rolün geçerli üyelerini listelemesi](./media/pim-resource-roles-external-users/selected-role.png)

1. Rol sayfasında, yeni atama bölmesini açmak için **üye Ekle** ' yi seçin.

1. **Üye veya Grup Seç**' e tıklayın.

    ![Yeni atama-kullanıcılar ve grupları bir davet seçeneğiyle birlikte listeleyerek üye veya grup bölmesi seçin](./media/pim-resource-roles-external-users/select-member-group.png)

1. Bir konuğa davet etmek için **davet**' e tıklayın.

    ![Bir e-posta adresi girmek ve kişisel bir ileti belirtmek için kutulara Konuk sayfası davet edin](./media/pim-resource-roles-external-users/invite-guest.png)

1. Bir konuk seçtikten sonra **davet**' e tıklayın.

    Konuk seçili üye olarak eklenmelidir.

1. **Üye veya grup seçin** bölmesinde **Seç**' e tıklayın.

1. **Üyelik ayarları** bölmesinde, atama türünü ve süreyi seçin.

    ![Atama türü, başlangıç tarihi ve bitiş tarihi belirtme seçenekleriyle yeni atama-Üyelik ayarları sayfası](./media/pim-resource-roles-external-users/membership-settings.png)

1. Atamayı tamamladıktan sonra **bitti** ' yi ve ardından **Ekle**' yi seçin.

    Konuk rolü ataması, rol listenizde görünür.

    ![Konuk, uygun şekilde listelendiği rol sayfası](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Rolü Konuk olarak etkinleştir

Dış bir Kullanıcı kullanıyorsanız, daveti Azure AD kuruluşunda Konuk olarak kabul etmeniz ve muhtemelen rol atamasını etkinleştirmeniz gerekir.

1. Davetiniz ile e-postayı açın. E-posta aşağıdakine benzer şekilde görünür.

    ![Dizin adı, kişisel ileti ve kullanmaya başlama bağlantısı ile e-posta daveti](./media/pim-resource-roles-external-users/email-invite.png)

1. E-postadaki **Başlarken** bağlantısını seçin.

1. İzinleri inceledikten sonra **kabul et**' e tıklayın.

    ![Bir tarayıcıdaki izin sayfasını, kuruluşunuzun incelemenizi istediğiniz izin listesiyle birlikte gözden geçirin](./media/pim-resource-roles-external-users/invite-accept.png)

1. Kullanım koşullarını kabul etmeniz ve oturum açmış kalmak isteyip istemediğinizi belirtmeniz istenebilir. Azure portal, bir rol için *uygunsanız* , henüz kaynaklara erişemezsiniz.

1. Rol atamasını etkinleştirmek için, rol etkinleştir bağlantısına sahip e-postayı açın. E-posta aşağıdakine benzer şekilde görünür.

    ![Rol etkinleştirme bağlantısı olan bir rol için uygun olduğunuzu belirten e-posta](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Privileged Identity Management uygun rollerinizi açmak için **rolü etkinleştir** ' i seçin.

    ![Uygun rollerinizi listelemek Privileged Identity Management roller sayfası](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Eylem altında **Etkinleştir** bağlantısını seçin.

    Rol ayarlarına bağlı olarak, rolü etkinleştirmek için bazı bilgiler belirtmeniz gerekir.

1. Rolün ayarlarını belirledikten sonra, rolü etkinleştirmek için **Etkinleştir** ' e tıklayın.

    ![Başlangıç zamanı, süre ve nedenini belirtmek için sayfa listesi kapsamını ve seçeneklerini etkinleştirin](./media/pim-resource-roles-external-users/activate-role.png)

    İsteğiniz onaylanmak için yönetici gerekli olmadığı takdirde, belirtilen kaynaklara erişiminizin olması gerekir.

## <a name="view-activity-for-a-guest"></a>Konuk için etkinliği görüntüleme

Konukların neler yaptığını izlemek için Denetim günlüklerini görüntüleyebilirsiniz.

1. Yönetici olarak, Privileged Identity Management açın ve paylaşılan kaynağı seçin.

1. Bu kaynağa yönelik etkinliği görüntülemek için **kaynak denetimi** ' ni seçin. Aşağıda, bir kaynak grubu için etkinliğin bir örneği gösterilmektedir.

    ![Azure kaynakları-saat, istek sahibi ve eylemi listeleme kaynak denetimi sayfası](./media/pim-resource-roles-external-users/audit-resource.png)

1. Konuğun etkinliğini görüntülemek için **Azure Active Directory** > **Kullanıcı** > *Konuk adı*' nı seçin.

1. Kuruluş için Denetim günlüklerini görmek üzere **Denetim günlükleri** ' ni seçin. Gerekirse, filtreler belirtebilirsiniz.

    ![Tarih, hedef, başlatılan ve etkinliği listeleme dizin denetim günlükleri](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD yönetici rolleri atama](pim-how-to-add-role-to-user.md)
- [Azure AD B2B işbirliğinde Konuk Kullanıcı erişimi nedir?](../b2b/what-is-b2b.md)
