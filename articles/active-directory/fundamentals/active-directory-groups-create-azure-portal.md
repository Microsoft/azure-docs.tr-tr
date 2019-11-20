---
title: Temel Grup oluşturma ve üye ekleme-Azure Active Directory | Microsoft Docs
description: Azure Active Directory kullanarak temel Grup oluşturma yönergeleri.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c551a81788df8d68664abaa03f467a4521222d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473479"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Azure Active Directory kullanarak temel Grup oluşturma ve üye ekleme
Azure Active Directory (Azure AD) portalını kullanarak temel bir grup oluşturabilirsiniz. Bu makalenin amaçları doğrultusunda, kaynak sahibi (yönetici) tarafından tek bir kaynağa temel bir grup eklenir ve bu grup, o kaynağa erişmesi gereken belirli üyeleri (çalışanlar) içerir. Dinamik üyelikler ve kural oluşturma da dahil olmak üzere daha karmaşık senaryolar için bkz. [Azure Active Directory kullanıcı yönetimi belgeleri](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Temel bir grup oluşturma ve üye ekleme
Temel bir grup oluşturabilir ve aynı anda üyelerinizi ekleyebilirsiniz.

### <a name="to-create-a-basic-group-and-add-members"></a>Temel bir grup oluşturmak ve üye eklemek için
1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com) oturum açın.

1. Arama yapın ve **Azure Active Directory**seçin.

    ![Grupları gösteren Azure AD sayfası](media/active-directory-groups-create-azure-portal/search-active-directory.png)

1. **Active Directory** sayfasında, **gruplar** ' ı seçin ve ardından **Yeni Grup**' u seçin.

    ![Grupları gösteren Azure AD sayfası](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. **Yeni Grup** sayfasında, gerekli bilgileri doldurun.

    ![Örnek bilgileriyle doldurulmuş şekilde yeni grup sayfası](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Grup türü (gerekli).** Önceden tanımlanmış bir grup türü seçin. Buna şunlar dahildir:- **Security**. Bir kullanıcı grubu için paylaşılan kaynaklara üye ve bilgisayar erişimini yönetmek için kullanılır. Örneğin, belirli bir güvenlik ilkesi için bir güvenlik grubu oluşturabilirsiniz. Böylece, her bir üyeye ayrı ayrı izin eklemek zorunda kalmadan aynı anda tüm üyelere bir dizi izin verebilirsiniz. Kaynaklara erişimi yönetme hakkında daha fazla bilgi için bkz. [Azure Active Directory grupları ile kaynaklara erişimi yönetme](active-directory-manage-groups.md).
               - **Office 365**. Üyelerin paylaşılan posta kutusuna, takvime, takvime, dosyalara, SharePoint sitesine vb.’ye erişmesini sağlayarak işbirliği fırsatları sunar. Bu seçenek, kuruluşunuzun dışındaki kişilerin de gruba erişmesini sağlar. Office 365 Grupları hakkında daha fazla bilgi için bkz. [Office 365 Grupları hakkında bilgi edinin](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Grup adı (gerekli).** Grup için unutmayacağınız ve mantıklı olan bir ad ekler. Adın zaten başka bir grup için kullanıldığını tespit etmek üzere bir denetim gerçekleştirilecek. Ad zaten kullanımda ise, yinelenen adlandırmayı önlemek için grubunuzun adını değiştirmeniz istenir.

   - **Grup e-posta adresi (gerekli).** Grup için bir e-posta adresi ekleyin veya otomatik olarak doldurulmuş e-posta adresini saklayın.

   - **Grup açıklaması.** Grubunuza isteğe bağlı bir açıklama ekler.

   - **Üyelik türü (gerekli).** Önceden tanımlanmış bir üyelik türü seçin. Buna şunlar dahildir:- **atandı.** Bu grubun üyesi olacak ve benzersiz izinlere sahip olacak şekilde belirli kullanıcıları eklemenize olanak sağlar. Bu makalenin amaçları doğrultusunda, bu seçeneği kullanıyoruz.
          **dinamik kullanıcı - .** , Üyeleri otomatik olarak eklemek ve kaldırmak için dinamik üyelik kuralları kullanmanıza olanak sağlar. Bir üyenin öznitelikleri değişirse sistem, üyenin kural gereksinimlerini karşıladığını mı (eklendiğini) yoksa artık kural gereksinimlerini karşılamadığını mı (kaldırıldığını) görmek amacıyla dizin için dinamik grup kurallarınıza bakar.
          **dinamik cihaz - .** Otomatik olarak cihazlar eklemek ve kaldırmak için dinamik grup kuralları kullanmanıza olanak sağlar. Bir cihazın öznitelikleri değişirse sistem, cihazın kural gereksinimlerini karşıladığını mı (eklendiğini) yoksa artık kural gereksinimlerini karşılamadığını mı (kaldırıldığını) görmek amacıyla dizin için dinamik grup kurallarınıza bakar.

        >[!Important]
        >Ya cihazlar ya da kullanıcılar için bir dinamik grup oluşturabilirsiniz, her ikisi için oluşturamazsınız. Ayrıca cihaz sahiplerinin özniteliklerine göre de bir cihaz grubu oluşturamazsınız. Cihaz üyeliği kuralları yalnızca cihaz ilişkilendirmesine başvurabilir. Kullanıcılar ve cihazlar için dinamik bir grup oluşturma hakkında daha fazla bilgi için bkz. [dinamik grup oluşturma ve durumu denetleme](../users-groups-roles/groups-create-rule.md).

1. **Oluştur**'u seçin.

    Grubunuz oluşturulur ve üyeler eklemeniz için hazır olur.

1. **Grup** sayfasından **Üyeler** alanını seçin ve sonra **Üyeleri seç** sayfasından grubunuza eklenecek üyeleri aramaya başlayın.

    ![Grup oluşturma işlemi sırasında grubunuz için üyeleri seçme](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Üye ekleme işleminiz bittiğinde **Seç** öğesini seçin.

    **Gruba Genel Bakış** sayfası, şimdi gruba eklenen üye sayısını gösterecek şekilde güncelleştirilir.

    ![Üye sayısı vurgulanmış şekilde Gruba Genel Bakış sayfası](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Hoş geldiniz e-postasını aç veya kapat

Herhangi bir yeni Office 365 Grubu oluşturulduğunda, dinamik veya statik üyeliğe bakılmaksızın, gruba eklenen tüm kullanıcılara bir hoş geldiniz bildirimi gönderilir. Bir kullanıcının veya cihazın herhangi bir özniteliği değiştiğinde, kuruluştaki tüm dinamik grup kuralları olası üyelik değişiklikleri için işlenir. Ayrıca eklenen kullanıcılar da hoş geldiniz bildirimini alır. Bu davranışı [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)'de devre dışı bırakabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar
Şimdi bir grup ve en az bir kullanıcı eklediğinize göre artık aşağıdakileri yapabilirsiniz:

- [Grupları ve üyeleri görüntüleme](active-directory-groups-view-azure-portal.md)

- [Grup üyeliğini yönetme](active-directory-groups-membership-azure-portal.md)

- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](../users-groups-roles/groups-create-rule.md)

- [Grup ayarlarınızı düzenleme](active-directory-groups-settings-azure-portal.md)

- [Grupları kullanarak kaynaklara erişimi yönetme](active-directory-manage-groups.md)

- [Grupları kullanarak SaaS uygulamalarına erişimi yönetme](../users-groups-roles/groups-saasapps.md)

- [PowerShell komutlarını kullanarak grupları yönetme](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Azure Active Directory’ye bir Azure aboneliğini ekleme veya ilişkilendirme](active-directory-how-subscriptions-associated-directory.md)
