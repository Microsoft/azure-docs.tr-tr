---
title: Windows sanal masaüstü 'nde kiracı oluşturma (klasik)-Azure
description: Azure Active Directory 'da Windows sanal masaüstü (klasik) kiracılarının nasıl ayarlanacağını açıklar.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b2421c8b8c2d74649084f1cc8b75160857f40fc1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444335"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Öğretici: Windows sanal masaüstü 'nde kiracı oluşturma (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir.

Windows sanal masaüstü 'nde kiracı oluşturmak, masaüstü sanallaştırma çözümünüzü oluşturmaya yönelik ilk adımdır. Kiracı, bir veya daha fazla konak havuzu grubudur. Her konak havuzu, Azure 'da sanal makine olarak çalışan ve Windows sanal masaüstü hizmetine kaydedilen birden çok oturum ana bilgisayardan oluşur. Her konak havuzu ayrıca, uzak masaüstü ve uzak uygulama kaynaklarını kullanıcılara yayımlamak için kullanılan bir veya daha fazla uygulama grubundan oluşur. Kiracı ile, konak havuzları oluşturabilir, uygulama grupları oluşturabilir, kullanıcılar atayabilir ve hizmet üzerinden bağlantı yapabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Windows sanal masaüstü hizmetine Azure Active Directory izinleri verin.
> * Azure Active Directory kiracınızdaki bir kullanıcıya TenantCreator uygulama rolünü atayın.
> * Windows sanal masaüstü kiracısı oluşturun.

## <a name="what-you-need-to-set-up-a-tenant"></a>Kiracı kurmak için gerekenler

Windows sanal masaüstü kiracınızı ayarlamaya başlamadan önce şunları yaptığınızdan emin olun:

* Windows sanal masaüstü kullanıcıları için [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Kiracı kimliği.
* Azure Active Directory kiracının içindeki genel yönetici hesabı.
   * Bu Ayrıca, müşterileri için bir Windows sanal masaüstü kiracısı oluşturan bulut çözümü sağlayıcısı (CSP) kuruluşları için de geçerlidir. Bir CSP kuruluşunda çalışıyorsanız, müşterinin Azure Active Directory örneğinin genel yöneticisi olarak oturum açabiliyor olmanız gerekir.
   * Yönetici hesabı, Windows sanal masaüstü kiracısı oluşturmaya çalıştığınız Azure Active Directory kiracısından kaynaklıdır. Bu işlem Azure Active Directory B2B (konuk) hesaplarını desteklemez.
   * Yönetici hesabının bir iş veya okul hesabı olması gerekir.
* Azure aboneliği.

Bu öğreticide açıklanan işlemin düzgün şekilde çalışabilmesi için kiracı KIMLIĞI, genel yönetici hesabı ve Azure aboneliğine sahip olmanız gerekir.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Windows sanal masaüstüne izin verme

Bu Azure Active Directory örneği için Windows sanal masaüstü için izinleri zaten verdiyseniz, bu bölümü atlayın.

Windows sanal masaüstü hizmetine izin verilmesi, yönetim ve Son Kullanıcı görevleri için Azure Active Directory sorgulamasına olanak sağlar.

Hizmet izinleri vermek için:

1. Bir tarayıcı açın ve [Windows sanal masaüstü sunucusu uygulamasına](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)yönetici onay akışını başlatın.
   > [!NOTE]
   > Bir müşteriyi yönetiyorsanız ve müşterinin dizinine yönetici onayı vermeniz gerekiyorsa, tarayıcıya aşağıdaki URL 'YI girin ve {Tenant} değerini müşterinin Azure AD etki alanı adıyla değiştirin. Örneğin, müşterinin organizasyonu contoso.onmicrosoft.com Azure AD etki alanı adını kaydettirirse, {Tenant} değerini contoso.onmicrosoft.com ile değiştirin.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Windows sanal masaüstü onayı sayfasında Genel yönetici hesabıyla oturum açın. Örneğin, contoso organizasyonu ile karşılaşırsanız hesabınız admin@contoso.com veya olabilir admin@contoso.onmicrosoft.com .
3. **Kabul Et**’i seçin.
4. Azure AD 'nin izin kaydı yapabilmesi için bir dakika bekleyin.
5. Bir tarayıcı açın ve yönetici onay akışını [Windows sanal masaüstü istemci uygulamasına](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)başlatın.
   >[!NOTE]
   > Bir müşteriyi yönetiyorsanız ve müşterinin dizinine yönetici onayı vermeniz gerekiyorsa, tarayıcıya aşağıdaki URL 'YI girin ve {Tenant} değerini müşterinin Azure AD etki alanı adıyla değiştirin. Örneğin, müşterinin organizasyonu contoso.onmicrosoft.com Azure AD etki alanı adını kaydettirirse, {Tenant} değerini contoso.onmicrosoft.com ile değiştirin.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. 2. adımda yaptığınız gibi, genel yönetici olarak Windows sanal masaüstü onayı sayfasında oturum açın.
7. **Kabul Et**’i seçin.

## <a name="assign-the-tenantcreator-application-role"></a>TenantCreator uygulama rolünü atama

Azure Active Directory kullanıcı atama Kullanıcı rolü, kullanıcının Azure Active Directory örneğiyle ilişkili bir Windows sanal masaüstü kiracısı oluşturmasına izin verir. TenantCreator rolünü atamak için genel yönetici hesabınızı kullanmanız gerekir.

TenantCreator uygulama rolünü atamak için:

1. TenantCreator uygulama rolünü yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Kurumsal uygulamaları** arayın ve seçin. Birden çok Azure Active Directory kiracılarıyla çalışıyorsanız, özel bir tarayıcı oturumu açmak ve URL 'Leri kopyalayıp Adres çubuğuna yapıştırmak iyi bir uygulamadır.

   > [!div class="mx-imgBorder"]
   > ![Azure portal kurumsal uygulamaları aramanın ekran görüntüsü](../media/azure-portal-enterprise-applications.png)

2. **Kurumsal uygulamalar** Içinde **Windows sanal masaüstü** araması yapın. Önceki bölümde için izin verdiğiniz iki uygulamayı görürsünüz. Bu iki uygulama için **Windows sanal masaüstü**' nü seçin.

   > [!div class="mx-imgBorder"]
   > !["Enterprise Applications" içinde "Windows sanal masaüstü" araması sırasında arama sonuçlarının ekran görüntüsü. "Windows sanal masaüstü" adlı uygulama vurgulanır.](../media/tenant-enterprise-app.png)

3. **Kullanıcılar ve gruplar ' ı** seçin. Uygulamaya izin veren yöneticinin, **varsayılan erişim** rolü atanmış olarak zaten listelendiğini görebilirsiniz. Bu, bir Windows sanal masaüstü kiracısı oluşturmak için yeterli değildir. Bir kullanıcıya **Tenantcreator** rolünü eklemek için bu yönergeleri takip edin.

   > [!div class="mx-imgBorder"]
   > !["Windows sanal masaüstü" Kurumsal uygulamasını yönetmek için atanan kullanıcı ve grupların ekran görüntüsü. Ekran görüntüsünde, "varsayılan erişim" için olan yalnızca bir atama gösterilmektedir.](../media/tenant-default-access.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** sekmesinden **Kullanıcılar ve gruplar** ' ı seçin.
5. Windows sanal masaüstü kiracınızı oluşturacak bir kullanıcı hesabı arayın. Kolaylık olması için bu genel yönetici hesabı olabilir.
   - Veya gibi bir Microsoft Identity sağlayıcısı kullanıyorsanız contosoadmin@live.com contosoadmin@outlook.com , Windows sanal masaüstü 'nde oturum açabilmeyebilirsiniz. Bunun gibi etki alanına özgü bir hesap kullanmanızı öneririz admin@contoso.com admin@contoso.onmicrosoft.com .

   > [!div class="mx-imgBorder"]
   > !["TenantCreator" olarak eklenecek bir kullanıcı seçme ekran görüntüsü.](../media/tenant-assign-user.png)

   > [!NOTE]
   > Bu Azure Active Directory örneğinden kaynaksız bir Kullanıcı (veya Kullanıcı içeren bir grup) seçmeniz gerekir. Konuk (B2B) Kullanıcı veya hizmet sorumlusu seçemezsiniz.

6. Kullanıcı hesabı ' nı seçin, **Seç** düğmesini seçin ve ardından **ata**' yı seçin.
7. **Windows sanal masaüstü-kullanıcılar ve gruplar** sayfasında, Windows sanal masaüstü kiracı 'yı oluşturacak olan kullanıcıya atanan **tenantcreator** rolü ile yeni bir giriş gördiğinizi doğrulayın.

   > [!div class="mx-imgBorder"]
   > !["Windows sanal masaüstü" Kurumsal uygulamasını yönetmek için atanan kullanıcı ve grupların ekran görüntüsü. Ekran görüntüsünde artık "TenantCreator" rolüne atanan bir kullanıcının ikinci girişi vardır.](../media/tenant-tenant-creator-added.png)

Windows sanal masaüstü kiracınızı oluşturmak için devam etmeden önce iki bilgi parçasına ihtiyacınız vardır:

   - Azure Active Directory kiracı KIMLIĞINIZ (veya **DIZIN kimliğiniz**)
   - Azure abonelik KIMLIĞINIZ

Azure Active Directory kiracı KIMLIĞINIZI (veya **DIZIN kimliğini**) bulmak için:
1. Aynı [Azure Portal](https://portal.azure.com) oturumunda **Azure Active Directory** arayıp seçin.

   > [!div class="mx-imgBorder"]
   > ![Azure portal "Azure Active Directory" için arama sonuçlarının ekran görüntüsü. "Hizmetler" altındaki arama sonucu vurgulanır.](../media/tenant-search-azure-active-directory.png)

2. **Özellikleri** bulana kadar aşağı kaydırın ve bunu seçin.
3. **DIZIN kimliğini** bulun ve ardından Pano simgesini seçin. Daha sonra **Aadtenantıd** değeri olarak kullanabilmek için bunu kullanışlı bir konuma yapıştırın.

   > [!div class="mx-imgBorder"]
   > ![Azure Active Directory özelliklerinin ekran görüntüsü. Fare, kopyalamak ve yapıştırmak için "dizin KIMLIĞI" Pano simgesinin üzerine getirildiğinde.](../media/tenant-directory-id.png)

Azure abonelik KIMLIĞINIZI bulmak için:
1. Aynı [Azure Portal](https://portal.azure.com) oturumunda, **abonelikleri** arayıp seçin.

   > [!div class="mx-imgBorder"]
   > ![Azure portal "Azure Active Directory" için arama sonuçlarının ekran görüntüsü. "Hizmetler" için arama sonucu vurgulanır.](../media/tenant-search-subscription.png)

2. Windows sanal masaüstü hizmeti bildirimleri almak için kullanmak istediğiniz Azure aboneliğini seçin.
3. **ABONELIK kimliğini** bulun ve ardından bir pano simgesi görünene kadar değerin üzerine gelin. Pano simgesini seçin ve daha sonra Azuyeniden bir konuma yapıştırın, böylece daha sonra **azuyeniden, Scriptionıd** değeri olarak kullanabilirsiniz.

   > [!div class="mx-imgBorder"]
   > ![Azure abonelik özelliklerinin ekran görüntüsü. Fare, kopyalamak ve yapıştırmak için "abonelik KIMLIĞI" Pano simgesinin üzerine getirildiğinde.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Windows sanal masaüstü kiracısı oluşturma

Azure Active Directory sorgulamak için Windows sanal masaüstü hizmeti izinleri vermiş olduğunuza ve bir kullanıcı hesabına TenantCreator rolünü atadığınıza göre, bir Windows sanal masaüstü kiracısı oluşturabilirsiniz.

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/) .

Bu cmdlet ile TenantCreator Kullanıcı hesabını kullanarak Windows sanal masaüstü 'nde oturum açın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Bundan sonra, Azure Active Directory kiracısı ile ilişkili yeni bir Windows sanal masaüstü kiracısı oluşturun:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Parantez içine alınmış değerleri, kuruluşunuzla ve kiracınızla ilgili değerlerle değiştirin. Yeni Windows sanal masaüstü kiracınız için seçtiğiniz ad genel olarak benzersiz olmalıdır. Örneğin, contoso kuruluşunun Windows sanal masaüstü 'Nü sunuyoruz. Çalıştırdığınız cmdlet şöyle görünür:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Hesabınızı bilgisayarınızdan kilitlediyseniz veya bir kişinin sizin devamsızınızda kiracı yöneticisi olarak hareket etmesini gerektiren ikinci bir kullanıcıya yönetici erişimi atamanız iyi bir fikirdir. İkinci bir kullanıcıya yönetici erişimi atamak için aşağıdaki cmdlet 'i ile çalıştırın `<TenantName>` ve `<Upn>` kiracı adınızla ıkıncı kullanıcının UPN 'sini kullanın.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Sonraki adımlar
Kiracınızı oluşturduktan sonra, Azure Active Directory bir hizmet sorumlusu oluşturmanız ve Windows sanal masaüstü 'nde bir rol atamanız gerekir. Hizmet sorumlusu, bir konak havuzu oluşturmak için Windows sanal masaüstü Azure Marketi teklifini başarıyla dağıtmanıza olanak tanır. Konak havuzları hakkında daha fazla bilgi edinmek için Windows sanal masaüstü 'nde bir konak havuzu oluşturma öğreticisine geçin.

> [!div class="nextstepaction"]
> [PowerShell ile hizmet sorumluları ve rol atamaları oluşturma](create-service-principal-role-powershell.md)
