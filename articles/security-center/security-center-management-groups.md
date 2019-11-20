---
title: Azure Güvenlik Merkezi için kiracı genelinde görünürlük elde edin | Microsoft Docs
description: Azure Güvenlik Merkezi 'nde kiracı genelinde görünürlük kazanmanız hakkında bilgi edinin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 73b99b9ebcd18e1eb5b670d0809d1f0f6cbf8f9a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582922"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Azure Güvenlik Merkezi için kiracı genelinde görünürlük elde edin
Bu makale, Azure Güvenlik Merkezi 'nin sunduğu avantajları en üst düzeye çıkaran birkaç eylem gerçekleştirerek başlamanıza yardımcı olur. Bu eylemlerin gerçekleştirilmesi, Azure Active Directory kiracınızla bağlantılı olan tüm Azure aboneliklerinde görünürlük elde etmenizi ve güvenlik ilkelerini birden çok arasında uygulayarak kuruluşunuzun güvenlik duruşunu etkin bir şekilde yönetmenize olanak sağlar. bir aggreggöre abonelikler.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Yönetim grupları
Azure Yönetim grupları, abonelik gruplarında erişimi, ilkeleri ve raporlamayı verimli bir şekilde yönetebilir ve kök yönetim grubu üzerinde eylemler gerçekleştirerek Azure 'un tüm özelliklerini etkin bir şekilde yönetebilme olanağı sağlar. Her Azure AD kiracısına kök yönetim grubu adlı tek bir en üst düzey yönetim grubu verilir. Diğer tüm yönetim grupları ve abonelikler hiyerarşide en üstte yer alan bu kök yönetim grubunun altındadır. Bu grup, genel ilkelerin ve RBAC atamalarının dizin düzeyinde uygulanmasını sağlar. 

Kök yönetim grubu, aşağıdaki eylemlerden herhangi birini gerçekleştirdiğinizde otomatik olarak oluşturulur: 
1. [Azure portal](https://portal.azure.com) **Yönetim grupları** giderek Azure Yönetim grupları 'nı kullanmayı tercih edin.
2. API çağrısı aracılığıyla bir yönetim grubu oluşturun.
3. PowerShell ile bir yönetim grubu oluşturun.

Yönetim gruplarına ayrıntılı bir genel bakış için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](../azure-resource-manager/management-groups-overview.md) makalesi.

## <a name="create-a-management-group-in-the-azure-portal"></a>Azure portal bir yönetim grubu oluşturun
Abonelikleri yönetim gruplarında düzenleyebilir ve idare ilkelerinizi yönetim gruplarına uygulayabilirsiniz. Bir yönetim grubu içindeki aboneliklerin tümü otomatik olarak yönetim grubuna uygulanmış olan ilkeleri devralır. Güvenlik Merkezi 'ni eklemek için yönetim grupları gerekli olmasa da, kök yönetim grubunun oluşturulması için en az bir yönetim grubu oluşturmanız önemle tavsiye edilir. Grup oluşturulduktan sonra, Azure AD kiracınız kapsamındaki tüm abonelikler buna bağlanır. PowerShell ve daha fazla bilgi için yönergeler için bkz. [kaynak ve kuruluş yönetimi için yönetim grupları oluşturma](../azure-resource-manager/management-groups-create.md).

 
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler** > **Yönetim grupları**' nı seçin.
3. Ana sayfada **yeni yönetim grubu** ' nu seçin. 

    ![Ana grup](./media/security-center-management-groups/main.png) 
4.  Yönetim grubu KIMLIĞI alanını girin. 
    - **Yönetim grubu kimliği** , bu yönetim grubundaki komutları göndermek için kullanılan dizin benzersiz tanımlayıcısıdır. Bu tanımlayıcı, bu grubu tanımlamak için Azure sistem genelinde kullanıldığından oluşturulduktan sonra düzenlenebilir değildir. 
    - Görünen ad alanı Azure portal içinde görüntülenen addır. Ayrı bir görünen ad, yönetim grubu oluşturulurken isteğe bağlı bir alandır ve herhangi bir zamanda değiştirilebilir.  

      ![Oluşturma](./media/security-center-management-groups/create_context_menu.png)  
5.  **Kaydet**’i seçin

### <a name="view-management-groups-in-the-azure-portal"></a>Azure portal Yönetim gruplarını görüntüleme
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Yönetim gruplarını görüntülemek için Azure ana menüsünde **tüm hizmetler** ' i seçin.
3. **Genel**altında **Yönetim grupları**' yi seçin.

    ![Yönetim grubu oluşturma](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Kiracı düzeyinde görünürlük ve ilke atama özelliği verme

Azure AD kiracısında kayıtlı olan tüm aboneliklerin güvenlik duruşunu görünürlüğünü almak için, kök yönetim grubuna yeterli okuma izinleri olan bir RBAC rolünün atanması gerekir.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Azure Active Directory genel yönetici için erişimi yükseltme
Azure Active Directory kiracı yöneticisinin Azure aboneliklerine doğrudan erişimi yoktur. Ancak, bir dizin Yöneticisi olarak, kendilerine erişimi olan bir role yükseltme hakkı vardır. Azure AD kiracı yöneticisinin, RBAC rolleri atayabilmeleri için kendisini kök yönetim grubu düzeyinde Kullanıcı erişimi Yöneticisi 'ne yükseltmesine ihtiyacı vardır. PowerShell yönergeleri ve ek bilgiler için, bkz. [Azure Active Directory genel yönetici için erişimi yükseltme](../role-based-access-control/elevate-access-global-admin.md). 


1. [Azure Portal](https://portal.azure.com) veya [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com)oturum açın.

2. Gezinti listesinde **Azure Active Directory** ' a ve ardından **Özellikler**' e tıklayın.

   ![Azure AD özellikleri-ekran görüntüsü](./media/security-center-management-groups/aad-properties.png)

3. **Azure kaynakları Için erişim yönetimi**altında, anahtarı **Evet**olarak ayarlayın.

   ![Genel yönetici, Azure aboneliklerini ve Yönetim Grupları yönetebilir-ekran görüntüsü](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Anahtarı Evet olarak belirlediğinizde, Azure RBAC 'de kök kapsamda (/) Kullanıcı erişimi Yöneticisi rolü atanır. Bu, tüm Azure aboneliklerine ve bu Azure AD diziniyle ilişkili yönetim gruplarına rol atama izni verir. Bu anahtar yalnızca Azure AD 'de Genel yönetici rolü atanmış kullanıcılar tarafından kullanılabilir.

   - Anahtarı Hayır olarak ayarladığınızda, Azure RBAC 'deki Kullanıcı erişimi yönetici rolü kullanıcı hesabınızdan kaldırılır. Artık bu Azure AD diziniyle ilişkili tüm Azure aboneliklerinde ve yönetim gruplarında rol atayamazsınız. Yalnızca erişim izni verilen Azure aboneliklerini ve Yönetim gruplarını görüntüleyebilir ve yönetebilirsiniz.

4. Ayarınızı kaydetmek için **Kaydet** ' e tıklayın.

    - Bu ayar genel bir özellik değildir ve yalnızca şu anda oturum açmış olan kullanıcı için geçerlidir.

5. Yükseltilmiş erişimle yapmanız gereken görevleri gerçekleştirin. İşiniz bittiğinde, anahtarı tekrar **Hayır**olarak ayarlayın.


### <a name="assign-rbac-roles-to-users"></a>Kullanıcılara RBAC rolleri atama
Tüm Aboneliklerle ilgili görünürlük elde etmek için, kiracı yöneticilerinin, kök yönetim grubu düzeyinde dahil olmak üzere kiracı genelinde görünürlük sağlamak istedikleri kullanıcılara uygun RBAC rolünü ataması gerekir. Atanacak önerilen roller **Güvenlik Yöneticisi** veya **güvenlik okuyucusu**' dir. Genellikle güvenlik yöneticisi rolü kök düzeyinde uygulamak için gereklidir, ancak güvenlik okuyucusu kiracı düzeyinde görünürlük sağlamak için yeterli olacaktır. Bu rollerin verdiği izinler hakkında daha fazla bilgi için bkz. [Güvenlik Yöneticisi yerleşik rol açıklaması](../role-based-access-control/built-in-roles.md#security-admin) veya [güvenlik okuyucusu yerleşik rol açıklaması](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Kullanıcılara Azure portal aracılığıyla RBAC rolleri atama: 

1. [Azure portalında](https://portal.azure.com) oturum açın. 
1. Yönetim gruplarını görüntülemek için Azure ana menüsünde **tüm hizmetler** ' i seçin ve **Yönetim grupları**' yi seçin.
1.  Bir yönetim grubu seçin ve **Ayrıntılar**' a tıklayın.

    ![Ayrıntılar Yönetim Grupları ekran görüntüsü](./media/security-center-management-groups/management-group-details.PNG)
 
1. **Erişim denetimi (IAM)** ve **rol atamaları**' na tıklayın.

1. **Rol ataması Ekle**' ye tıklayın.

1. Atanacak rolü ve Kullanıcı ' yı seçin ve ardından **Kaydet**' e tıklayın.  
   
   ![Güvenlik okuyucusu rolü Ekle ekran görüntüsü](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Kullanıcılara PowerShell ile RBAC rolleri atama: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. [Azure PowerShell](/powershell/azure/install-az-ps)'i yükleyin.
2. Aşağıdaki komutları çalıştırın: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. İstendiğinde, genel yönetici kimlik bilgileriyle oturum açın. 

    ![Oturum açma istemi ekran görüntüsü](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Aşağıdaki komutu çalıştırarak okuyucu rolü izinleri verin:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Rolü kaldırmak için aşağıdaki komutu kullanın: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Güvenlik merkezini aç veya Yenile
Erişimi yükselttikten sonra Azure AD kiracınız kapsamındaki tüm aboneliklerde görünürlük olduğunu doğrulamak için Azure Güvenlik Merkezi 'ni açın veya yenileyin. 

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Güvenlik Merkezi 'nde görüntülemek istediğiniz abonelik seçicisindeki tüm abonelikleri seçtiğinizden emin olun.

    ![Abonelik seçici ekran görüntüsü](./media/security-center-management-groups/subscription-selector.png)

1. Azure ana menüsünde **tüm hizmetler** ' i seçin ve ardından **Güvenlik Merkezi**' ni seçin.
2. **Genel bakışta**bir abonelik kapsamı grafiği vardır.

    ![Abonelik kapsamı grafik ekran görüntüsü](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Kapsanan Aboneliklerin listesini görmek için **kapsam** ' a tıklayın. 

    ![Abonelik kapsamı listesi ekran görüntüsü](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Yükseltilmiş erişimi kaldır 
RBAC rolleri kullanıcılara atandıktan sonra, kiracı yöneticisinin kendisini Kullanıcı erişimi yönetici rolünden kaldırması gerekir.

1. [Azure Portal](https://portal.azure.com) veya [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com)oturum açın.

2. Gezinti listesinde **Azure Active Directory** ' a ve ardından **Özellikler**' e tıklayın.

3. **Genel yönetici altında Azure aboneliklerini ve yönetim grupları yönetebilir**, anahtarı **Hayır**olarak ayarlayabilirsiniz.

4. Ayarınızı kaydetmek için **Kaydet** ' e tıklayın.



## <a name="adding-subscriptions-to-a-management-group"></a>Bir yönetim grubuna abonelikler ekleme
Oluşturduğunuz yönetim grubuna abonelikler ekleyebilirsiniz. Bu adımlar, kiracı genelinde görünürlük ve genel ilke ve erişim yönetimi kazanmak için zorunlu değildir.

1. **Yönetim grupları**altında, aboneliğinizi eklemek için bir yönetim grubu seçin.

    ![Abonelik eklenecek bir yönetim grubu seçin](./media/security-center-management-groups/management-group-subscriptions.png)

2. **Mevcut Ekle**' yi seçin.

    ![Mevcut olanı Ekle](./media/security-center-management-groups/add-existing.png)

3. **Mevcut kaynağı Ekle** altına abonelik girin ve **Kaydet**' e tıklayın.

4. Kapsamdaki tüm abonelikleri ekleyinceye kadar 1 ile 3 arasındaki adımları yineleyin.

   > [!NOTE]
   > Yönetim gruplarında hem abonelikler hem de alt yönetim grupları bulunabilir. Bir kullanıcıyı üst yönetim grubuna RBAC rolü atadığınızda, erişim alt yönetim grubunun abonelikleri tarafından devralınır. Üst yönetim grubunda ayarlanan ilkeler alt öğeler tarafından da devralınır. 

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure Güvenlik Merkezi için kiracı genelinde görünürlük elde etme hakkında öğrendiniz. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)

