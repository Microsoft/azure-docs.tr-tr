---
title: Barındırılan test sürücüleri için bir Azure Marketi aboneliği ayarlama
description: Müşteri katılımı ve Operations test sürücüleri için Dynamics 365 için Dynamics 365 için bir Azure Marketi aboneliği ayarlamayı açıklar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: a7f12891bf394e54ee46c60598536faed1731202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600892"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Barındırılan test sürücüleri için bir Azure Marketi aboneliği ayarlama

Bu makalede, test sürücüleri için bir Azure Market aboneliği ve **dynamics 365 for Customer Engagement** veya Operations Environment için **Dynamics 365** nasıl ayarlanacağı açıklanır.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Dynamics 365 for Customer Engagement için ayarlama

1. Yönetici hesabıyla [Azure Portal](https://portal.azure.com/) oturum açın.
2. Sağ üst köşedeki hesap simgenizin üzerine gelerek Dynamics 365 test sürücü örneğinizle ilişkili kiracıda olduğunuzu doğrulayın. Doğru kiracıda değilseniz, doğru kiracıya geçiş yapmak için hesap simgesini seçin.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Doğru kiracı seçiliyor.":::

3. **Dynamics 365 müşteri katılımı planı** lisansının kullanılabilir olduğunu doğrulayın.

    [![Plan lisansı denetleniyor.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Azure 'da bir Azure Active Directory (AD) uygulaması oluşturun. AppSource, kiracınızda test sürücüsü kullanıcısını sağlamak ve sağlamak için bu uygulamayı kullanacaktır.
    1. Filtre bölmesinden **Azure Active Directory**' yi seçin.
    2. **Uygulama kayıtları**’nı seçin.

        [![Uygulama kayıtları seçiliyor.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. **Yeni kayıt** seçeneğini belirleyin.
    4. Uygun bir uygulama adı girin.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Bir uygulamayı kaydetme.":::

    5. Desteklenen hesap türleri altında, **herhangi bir kuruluş dizininde ve kişisel Microsoft hesaplarında hesap**' ı seçin.
    6. **Oluştur** ' u seçin ve uygulamanızın oluşturulmasını bekleyin.
    7. Uygulama oluşturulduktan sonra genel bakış ekranında görünen **uygulama kimliğine** göz atın. Test sürücünüzü yapılandırırken bu değere daha sonra ihtiyacınız olacak.
    8. **Uygulamayı Yönet** altında **API izinleri**' ni seçin.
    9. **Izin Ekle** ' yi ve ardından **API Microsoft Graph**' yi seçin.
    10. **Uygulama** izin kategorisini ve sonra **User. ReadWrite. All**, **Directory. Read. All** ve **Directory. ReadWrite. All** izinlerini seçin.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Uygulama izinleri ayarlanıyor.":::

    11. İzin eklendikten sonra, **Microsoft için yönetici onayı ver**' i seçin.
    12. İleti uyarısında **Evet**' i seçin.

        [![Başarıyla izin verilen uygulama izinlerini gösterir.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. Azure AD Uygulaması için bir gizli dizi oluşturmak için:
        1. **Uygulamayı Yönet**' den **sertifika ve gizlilikler**' ı seçin.
        2. Istemci gizli dizileri altında **yeni istemci parolası**' nı seçin.
        3. *Test sürücüsü* gibi bir açıklama girin ve uygun bir süre seçin. Bu anahtarın süresi dolduktan sonra test sürücüsü kesilir ve AppSource yeni bir anahtar sağlamanız gerekir.
        4. Azure uygulama gizli anahtarını oluşturmak için **Ekle** ' yi seçin. Bu dikey pencereyi, bu dikey pencereye göre gizlenmesi için, bu değeri kopyalayın. Test sürücünüzü yapılandırırken bu değere daha sonra ihtiyacınız olacak.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="İstemci parolası ekleme.":::

5. Azure AD uygulamasının Azure kiracınızdan kullanıcıları kaldırmasına izin vermek için hizmet sorumlusu rolünü uygulamaya ekleyin.
    1. Bir yönetim düzeyi PowerShell komut istemi açın.
    2. Install-Module MSOnline (MSOnline yüklü değilse bu komutu çalıştırın).
    3. Connect-MsolService (Bu, açılan pencereyi görüntüler; yeni oluşturulan kuruluş kiracısı ile oturum açın).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-Appprincipalıd $applicationId.
    6. Add-MsolRoleMember-Roleobjectıd fe930be7-5e62-47dB-91af-98c3a49a38b1-Rolememberobjectıd $sp. ObjectID-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Hesabınızda oturum açma.":::

6. Yukarıdaki oluşturulan Azure uygulamasını bir uygulama kullanıcısı olarak Test Drive CRM Örneğinizde ekleyin.
    1. **Azure Active Directory** yeni bir kullanıcı ekleyin. Bu kullanıcıyı oluşturmak için yalnızca **ad** ve **Kullanıcı** adı değerleri (aynı kiracıya ait) gereklidir, diğer alanları varsayılan olarak bırakın. Kullanıcı adı değerini kopyalayın.
    2. **CRM örneğinde** oturum açın ve   >  **güvenlik**  >  **kullanıcılarını** ayarla ' yı seçin.
    3. Görünümü **uygulama kullanıcıları** olarak değiştirin.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Bir kullanıcı için hesap bilgilerini ayarlama.":::

    4. Yeni bir kullanıcı ekleyin (formun uygulama KULLANıCıSı için olduğundan emin olun).
    5. **Birincil e-posta** ve **Kullanıcı adı** alanlarında aynı kullanıcı adını girin. **Uygulama kimliğinde** **Azure ApplicationId** 'yi ekleyin.
    6. Tüm **tam adı** verin.
    7. **Kaydet**’i seçin.
    8. **Rolleri Yönet**' i seçin.
    9. Okuma, yazma ve rol ayrıcalıklarını ( *Sistem Yöneticisi* gibi) içeren özel veya OOB güvenlik rolü atayın.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Rol ayrıcalıkları seçiliyor.":::

    10. Ayrıca, **başka bir kullanıcı ayrıcalığı adına işlem** yapın.
    11. Uygulama kullanıcısına, test sürücünüz için oluşturduğunuz özel güvenlik rolünü atayın.

## <a name="set-up-for-dynamics-365-for-operations"></a>Operations için Dynamics 365 için ayarlama

1. Yönetici hesabıyla [Azure Portal](https://portal.azure.com/) oturum açın.
2. Sağ üst köşedeki hesap simgenizin üzerine gelerek Dynamics 365 test sürücü örneğinizle ilişkili kiracıda olduğunuzu doğrulayın. Doğru kiracıda değilseniz, doğru kiracıya geçiş yapmak için hesap simgesini seçin.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Doğru kiracı seçiliyor.":::

3. Azure 'da bir Azure AD Uygulaması oluşturun. AppSource, kiracınızda test sürücüsü kullanıcısını sağlamak ve sağlamak için bu uygulamayı kullanacaktır.
    1. Filtre bölmesinden **Azure Active Directory**' yi seçin.
    2. **Uygulama kayıtları**’nı seçin.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Uygulama kaydı seçme.":::

    3. **Yeni kayıt** seçeneğini belirleyin.
    4. Uygun bir uygulama adı girin.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Bir uygulamayı kaydetme.":::

    5. Desteklenen hesap türleri altında, **herhangi bir kuruluş dizininde ve kişisel Microsoft hesaplarında hesap**' ı seçin.
    6. **Oluştur** ' u seçin ve uygulamanızın oluşturulmasını bekleyin.
    7. Uygulama oluşturulduktan sonra genel bakış ekranında görünen **uygulama kimliğine** göz atın. Test sürücünüzü yapılandırırken bu değere daha sonra ihtiyacınız olacak.
    8. **Uygulamayı Yönet** altında **API izinleri**' ni seçin.
    9. **Izin Ekle** ' yi ve ardından **API Microsoft Graph**' yi seçin.
    10. **Uygulama** izin kategorisini ve ardından **Directory. Read. All** ve **Directory. ReadWrite. All** izinlerini seçin.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Uygulama izinleri ayarlanıyor.":::

    11. **Izin Ekle**' yi seçin.
    12. İzin eklendikten sonra, **Microsoft için yönetici onayı ver**' i seçin.
    13. İleti uyarısında **Evet**' i seçin.

        [![Uygulama izinlerinin başarıyla verildiğini gösterir.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Azure AD Uygulaması için bir gizli dizi oluşturmak için:
        1. **Uygulamayı Yönet**' den **sertifika ve gizlilikler**' ı seçin.
        2. Istemci gizli dizileri altında **yeni istemci parolası**' nı seçin.
        3. *Test sürücüsü* gibi bir açıklama girin ve uygun bir süre seçin. Bu anahtarın süresi dolduktan sonra test sürücüsü kesilir ve AppSource yeni bir anahtar sağlamanız gerekir.
        4. Azure uygulama gizli anahtarını oluşturmak için **Ekle** ' yi seçin. Bu dikey pencereyi, bu dikey pencereye göre gizlenmesi için, bu değeri kopyalayın. Test sürücünüzü yapılandırırken bu değere daha sonra ihtiyacınız olacak.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="İstemci parolası ekleme.":::

4. Azure AD uygulamasının Azure kiracınızdan kullanıcıları kaldırmasına izin vermek için hizmet sorumlusu rolünü uygulamaya ekleyin.
    1. Bir yönetim düzeyi PowerShell komut istemi açın.
    2. Install-Module MSOnline (MSOnline yüklü değilse bu komutu çalıştırın).
    3. Connect-MsolService (Bu, açılan pencereyi görüntüler; yeni oluşturulan kuruluş kiracısı ile oturum açın).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-Appprincipalıd $applicationId.
    6. Add-MsolRoleMember-Roleobjectıd fe930be7-5e62-47dB-91af-98c3a49a38b1-Rolememberobjectıd $sp. ObjectID-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Hesabınızda oturum açma.":::

5. Bundan sonra uygulamanın kullanıcıları yönetmesine olanak tanımak için yukarıdaki uygulamayı **Dynamics 365 for Operations** 'a ekleyin.
    1. **Dynamics 365 for Operations** örneğinizi bulun.
    2. Sol üst köşeden Üç satırlık menüye (hamburger) tıklayın.
    3. **Sistem yönetimi**' ni seçin.
    4. **Azure Active Directory uygulamalar**' ı seçin.
    5. **+ Yeni** seçeneğini belirleyin.
    6. Adına eylemleri gerçekleştirecek olan **Azure AD uygulamasının ISTEMCI kimliğini** girin.

    > [!NOTE]
    > Eylemleri adına sahip kullanıcı KIMLIĞI (genellikle örneğin sistem yöneticisi veya diğer kullanıcıları ekleme ayrıcalıklarına sahip bir Kullanıcı).

    [![Eylemleri adına sahip kullanıcı KIMLIĞI (genellikle örneğin sistem yöneticisi veya diğer kullanıcıları ekleme ayrıcalıklarına sahip bir Kullanıcı).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
