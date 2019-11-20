---
title: Windows sanal masaüstü-Azure 'da kiracı oluşturma
description: Azure Active Directory 'da Windows sanal masaüstü kiracılarının nasıl ayarlanacağını açıklar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: helohr
ms.openlocfilehash: a7511b8026cb3f53a23eed0f0c057632314320c4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466651"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Öğretici: Windows sanal masaüstünde kiracı oluşturma

Windows sanal masaüstü 'nde kiracı oluşturmak, masaüstü sanallaştırma çözümünüzü oluşturmaya yönelik ilk adımdır. Kiracı, bir veya daha fazla konak havuzu grubudur. Her konak havuzu, Azure 'da sanal makine olarak çalışan ve Windows sanal masaüstü hizmetine kaydedilen birden çok oturum ana bilgisayardan oluşur. Her konak havuzu ayrıca, uzak masaüstü ve uzak uygulama kaynaklarını kullanıcılara yayımlamak için kullanılan bir veya daha fazla uygulama grubundan oluşur. Kiracı ile, konak havuzları oluşturabilir, uygulama grupları oluşturabilir, kullanıcılar atayabilir ve hizmet üzerinden bağlantı yapabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Windows sanal masaüstü hizmetine Azure Active Directory izinleri verin.
> * Azure Active Directory kiracınızdaki bir kullanıcıya TenantCreator uygulama rolünü atayın.
> * Windows sanal masaüstü kiracısı oluşturun.

Windows sanal masaüstü kiracınızı kurmak için gerekenler şunlardır:

* Windows sanal masaüstü kullanıcıları için [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Kiracı kimliği.
* Azure Active Directory kiracının içindeki genel yönetici hesabı.
   * Bu Ayrıca, müşterileri için bir Windows sanal masaüstü kiracısı oluşturan bulut çözümü sağlayıcısı (CSP) kuruluşları için de geçerlidir. Bir CSP kuruluşunda çalışıyorsanız, müşterinin Azure Active Directory örneğinin genel yöneticisi olarak oturum açabiliyor olmanız gerekir.
   * Yönetici hesabı, Windows sanal masaüstü kiracısı oluşturmaya çalıştığınız Azure Active Directory kiracısından kaynaklıdır. Bu işlem Azure Active Directory B2B (konuk) hesaplarını desteklemez.
   * Yönetici hesabının bir iş veya okul hesabı olması gerekir.
* Azure aboneliği.

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

2. Windows sanal masaüstü onayı sayfasında Genel yönetici hesabıyla oturum açın. Örneğin, contoso kuruluşunuzla karşılaşırsanız, hesabınız admin@contoso.com veya admin@contoso.onmicrosoft.comolabilir.
3. **Kabul Et**’i seçin.
4. Azure AD 'nin izin kaydı yapabilmesi için bir dakika bekleyin.
5. Bir tarayıcı açın ve yönetici onay akışını [Windows sanal masaüstü istemci uygulamasına](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)başlatın.
   >[!NOTE]
   > Bir müşteriyi yönetiyorsanız ve müşterinin dizinine yönetici onayı vermeniz gerekiyorsa, tarayıcıya aşağıdaki URL 'YI girin ve {Tenant} değerini müşterinin Azure AD etki alanı adıyla değiştirin. Örneğin, müşterinin organizasyonu contoso.onmicrosoft.com Azure AD etki alanı adını kaydettirirse, {Tenant} değerini contoso.onmicrosoft.com ile değiştirin.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. 2\. adımda yaptığınız gibi, genel yönetici olarak Windows sanal masaüstü onayı sayfasında oturum açın.
7. **Kabul Et**’i seçin.

## <a name="assign-the-tenantcreator-application-role"></a>TenantCreator uygulama rolünü atama

Azure Active Directory kullanıcı atama Kullanıcı rolü, kullanıcının Azure Active Directory örneğiyle ilişkili bir Windows sanal masaüstü kiracısı oluşturmasına izin verir. TenantCreator rolünü atamak için genel yönetici hesabınızı kullanmanız gerekir.

TenantCreator uygulama rolünü atamak için:

1. TenantCreator uygulama rolünü yönetmek için [Azure portal](https://portal.azure.com) gidin. **Kurumsal uygulamaları**arayın ve seçin. Birden çok Azure Active Directory kiracılarıyla çalışıyorsanız, özel bir tarayıcı oturumu açmak ve URL 'Leri kopyalayıp Adres çubuğuna yapıştırmak iyi bir uygulamadır.

   ![Azure portal kurumsal uygulamaları aramanın ekran görüntüsü](media/azure-portal-enterprise-applications.png)
2. **Kurumsal uygulamalar**Içinde **Windows sanal masaüstü**araması yapın. Önceki bölümde için izin verdiğiniz iki uygulamayı görürsünüz. Bu iki uygulama için **Windows sanal masaüstü**' nü seçin.
   
   !["Enterprise Applications" içinde "Windows sanal masaüstü" araması sırasında arama sonuçlarının ekran görüntüsü. "Windows sanal masaüstü" adlı uygulama vurgulanır.](media/tenant-enterprise-app.png)
3. **Kullanıcı ve gruplar**'ı seçin. Uygulamaya izin veren yöneticinin, **varsayılan erişim** rolü atanmış olarak zaten listelendiğini görebilirsiniz. Bu, bir Windows sanal masaüstü kiracısı oluşturmak için yeterli değildir. Bir kullanıcıya **Tenantcreator** rolünü eklemek için bu yönergeleri takip edin.
   
   !["Windows sanal masaüstü" Kurumsal uygulamasını yönetmek için atanan kullanıcı ve grupların ekran görüntüsü. Ekran görüntüsünde, "varsayılan erişim" için olan yalnızca bir atama gösterilmektedir.](media/tenant-default-access.png)
4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** dikey penceresinde **Kullanıcılar ve gruplar** ' ı seçin.
5. Windows sanal masaüstü kiracınızı oluşturacak bir kullanıcı hesabı arayın. Kolaylık olması için bu genel yönetici hesabı olabilir.
   - contosoadmin@live.com veya contosoadmin@outlook.comgibi bir Microsoft Identity sağlayıcısı kullanıyorsanız, Windows sanal masaüstü 'nde oturum açabilmeyebilirsiniz. Bunun yerine admin@contoso.com veya admin@contoso.onmicrosoft.com gibi etki alanına özgü bir hesap kullanmanızı öneririz.

   !["TenantCreator" olarak eklenecek bir kullanıcı seçme ekran görüntüsü.](media/tenant-assign-user.png)
   > [!NOTE]
   > Bu Azure Active Directory örneğinden kaynaksız bir Kullanıcı (veya Kullanıcı içeren bir grup) seçmeniz gerekir. Konuk (B2B) Kullanıcı veya hizmet sorumlusu seçemezsiniz.

6. Kullanıcı hesabı ' nı seçin, **Seç** düğmesini seçin ve ardından **ata**' yı seçin.
7. **Windows sanal masaüstü-kullanıcılar ve gruplar** sayfasında, Windows sanal masaüstü kiracı 'yı oluşturacak olan kullanıcıya atanan **tenantcreator** rolü ile yeni bir giriş gördiğinizi doğrulayın.

   !["Windows sanal masaüstü" Kurumsal uygulamasını yönetmek için atanan kullanıcı ve grupların ekran görüntüsü. Ekran görüntüsünde artık "TenantCreator" rolüne atanan bir kullanıcının ikinci girişi vardır.](media/tenant-tenant-creator-added.png)

Windows sanal masaüstü kiracınızı oluşturmak için devam etmeden önce iki bilgi parçasına ihtiyacınız vardır:

   - Azure Active Directory kiracı KIMLIĞINIZ (veya **DIZIN kimliğiniz**)
   - Azure abonelik KIMLIĞINIZ

Azure Active Directory kiracı KIMLIĞINIZI (veya **DIZIN kimliğini**) bulmak için:
1. Aynı [Azure Portal](https://portal.azure.com) oturumunda **Azure Active Directory**arayıp seçin.

   ![Azure portal "Azure Active Directory" için arama sonuçlarının ekran görüntüsü. "Hizmetler" altındaki arama sonucu vurgulanır.](media/tenant-search-azure-active-directory.png)
2. **Özellikleri**bulana kadar aşağı kaydırın ve bunu seçin.
3. **DIZIN kimliğini**bulun ve ardından Pano simgesini seçin. Daha sonra **Aadtenantıd** değeri olarak kullanabilmek için bunu kullanışlı bir konuma yapıştırın.

   ![Azure Active Directory özelliklerinin ekran görüntüsü. Fare, kopyalamak ve yapıştırmak için "dizin KIMLIĞI" Pano simgesinin üzerine getirildiğinde.](media/tenant-directory-id.png)

Azure abonelik KIMLIĞINIZI bulmak için:
1. Aynı [Azure Portal](https://portal.azure.com) oturumunda, **abonelikleri**arayıp seçin.
   
   ![Azure portal "Azure Active Directory" için arama sonuçlarının ekran görüntüsü. "Hizmetler" altındaki arama sonucu vurgulanır.](media/tenant-search-subscription.png)
2. Windows sanal masaüstü hizmeti bildirimleri almak için kullanmak istediğiniz Azure aboneliğini seçin.
3. **ABONELIK kimliğini**bulun ve ardından bir pano simgesi görünene kadar değerin üzerine gelin. Pano simgesini seçin ve daha sonra Azuyeniden bir konuma yapıştırın, böylece daha sonra **azuyeniden, Scriptionıd** değeri olarak kullanabilirsiniz.
   
   ![Azure abonelik özelliklerinin ekran görüntüsü. Fare, kopyalamak ve yapıştırmak için "abonelik KIMLIĞI" Pano simgesinin üzerine getirildiğinde.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Windows sanal masaüstü kiracısı oluşturma

Azure Active Directory sorgulamak için Windows sanal masaüstü hizmeti izinleri vermiş olduğunuza ve bir kullanıcı hesabına TenantCreator rolünü atadığınıza göre, bir Windows sanal masaüstü kiracısı oluşturabilirsiniz.

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü modülünü indirip içeri aktarın](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) .

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

## <a name="next-steps"></a>Sonraki adımlar

Kiracınızı oluşturduktan sonra, Azure Active Directory bir hizmet sorumlusu oluşturmanız ve Windows sanal masaüstü 'nde bir rol atamanız gerekir. Hizmet sorumlusu, bir konak havuzu oluşturmak için Windows sanal masaüstü Azure Marketi teklifini başarıyla dağıtmanıza olanak tanır. Konak havuzları hakkında daha fazla bilgi edinmek için Windows sanal masaüstü 'nde bir konak havuzu oluşturma öğreticisine geçin.

> [!div class="nextstepaction"]
> [PowerShell ile hizmet sorumluları ve rol atamaları oluşturma](./create-service-principal-role-powershell.md)
