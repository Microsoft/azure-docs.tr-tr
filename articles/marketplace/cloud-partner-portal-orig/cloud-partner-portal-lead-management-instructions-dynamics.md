---
title: Dynamics CRM | Azure Marketi
description: Dynamics CRM için lider yönetimini yapılandırın.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 4ccc8b85e72a4da3b0e640abcc70d24b7cdc54af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825254"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Dynamics CRM Online için lider yönetimini yapılandırma

Bu makalede, Dynamics CRM Online 'ın satış fırsatlarını işlemek üzere nasıl ayarlanacağı açıklanır.

## <a name="prerequisites"></a>Ön koşullar

Aşağıdaki Kullanıcı izinleri, bu makaledeki adımları tamamlamak için gereklidir:
- Bir çözüm yüklemek için Dynamics CRM Online örneğiniz üzerinde yönetici olmanız gerekir.
- Müşteri adayı hizmeti için yeni bir hizmet hesabı oluşturmak için bir kiracı yöneticisi olmanız gerekir.

<a name="install-the-solution"></a>Çözümü yükler
--------------------

1.  [Microsoft Market lider yazıcısı çözümünü](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) indirin ve yerel olarak kaydedin.

2.  Dynamics CRM Online 'ı açın ve ayarlar 'a gidin.
    >[!NOTE]
    >Sonraki ekran yakalamadaki seçenekleri görmüyorsanız, ihtiyacınız olan izinleriniz yoktur.
 
       ![Dynamics kurulum görünümü](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  **Içeri aktar**' ı seçin ve ardından adım 1 ' de indirdiğiniz çözümü seçin.
 
    ![Dynamics içeri aktarma seçeneği](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Çözümü yüklemeyi tamamlama.

## <a name="configure-user-permissions"></a>Kullanıcı izinlerini yapılandırma

Müşteri adaylarını Dynamics CRM örneğinizle yazmak için, bir hizmet hesabını bizimle paylaşmanız ve hesap için izinleri yapılandırmanız gerekir.

Hizmet hesabı oluşturmak ve izinleri atamak için aşağıdaki adımları kullanın. **Azure Active Directory** veya **Office 365**' i kullanabilirsiniz.

### <a name="azure-active-directory"></a>Azure Active Directory

Müşteri adaylarını almanızı sağlamak için, Kullanıcı adınızı/parolanızı güncelleştirme gereksinimi olmadan bu seçeneği öneririz. Azure Active Directory seçeneğini kullanmak için Active Directory uygulamanızdan uygulama kimliği, uygulama anahtarı ve dizin kimliğini sağlarsınız.

Dynamics CRM için Azure Active Directory yapılandırmak için aşağıdaki adımları kullanın.

1.  [Azure Portal](https://portal.azure.com/) için oturum açın ve Azure Active Directory hizmeti seçin.

2.  **Özellikler** ' i seçin ve ardından **dizin kimliğini**kopyalayın. Bu, Bulut İş Ortağı Portalı kullanmanız gereken kiracı hesabı tanımlamasıdır.

    ![Dizin KIMLIĞINI al](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  **Uygulama kayıtları**' yi seçin ve ardından **Yeni uygulama kaydı**' nı seçin.
4.  Uygulama adını girin.
5.  Tür için **Web uygulaması/API**' yi seçin.
6.  Bir URL girin. Bu alan, müşteri adayları için gerekli değildir, ancak bir uygulama oluşturmak için gereklidir.
7. **Oluştur**'u seçin.
8.  Uygulamanız kaydoldığına göre **Özellikler** ' i seçin ve ardından **uygulama kimliğini Kopyala**' yı seçin. Bu bağlantı bilgilerini Bulut İş Ortağı Portalı kullanacaksınız.
9.  Özellikler ' de, uygulamayı çoklu kiracı olarak ayarlayın ve ardından **Kaydet**' i seçin.

10. **Anahtarlar** ' ı seçin ve süresi *hiçbir zaman dolmayacak*şekilde ayarlanmış yeni bir anahtar oluşturun. Anahtarı oluşturmak için **Kaydet** ' i seçin. 
11. Anahtarlar menüsünde, **anahtar değerini Kopyala** ' yı seçin. Bulut İş Ortağı Portalı için ihtiyaç duyacağınız için bu değerin bir kopyasını kaydedin.
    
    ![Dynamics kayıtlı anahtarı al](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. **Gerekli izinleri** seçin ve ardından **Ekle**' yi seçin. 
13. Yeni API olarak **DYNAMICS CRM Online** ' ı seçin ve *ŞIRKET kullanıcıları olarak CRM Online 'a erişim*iznini denetleyin.

14. Dynamics CRM 'de kullanıcılara gidin ve **uygulama kullanıcılarına**geçiş yapmak Için "etkin kullanıcılar" açılan listesini seçin.
    
    ![Uygulama kullanıcıları](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Yeni bir kullanıcı oluşturmak için **Yeni** ' yi seçin. **Kullanıcı: uygulama Kullanıcı** açılan listesini seçin.
    
    ![Yeni uygulama kullanıcısı ekle](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. **Yeni Kullanıcı**' da, bu bağlantıyla birlikte kullanmak istediğiniz adı ve e-postayı girin. Azure portal oluşturduğunuz uygulamanın **uygulama kimliğini** yapıştırın.

     ![Yeni Kullanıcı yapılandırma](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Bu Kullanıcı için bağlantıyı yapılandırmayı tamamlaymak üzere bu makaledeki "güvenlik ayarları" na gidin.

### <a name="office-365"></a>Office 365

Azure Active Directory kullanmak istemiyorsanız, *Microsoft 365 Yönetim merkezinde*yeni bir kullanıcı kaydedebilirsiniz. Müşteri adayı almaya devam etmek için Kullanıcı adınızı/parolanızı her 90 günde bir güncelleştirmeniz gerekir.

Dynamics CRM için Office 365 ' i yapılandırmak için aşağıdaki adımları kullanın.

1. [Microsoft 365 Yönetim merkezinde](https://admin.microsoft.com)oturum açın.

2. **Yönetici** kutucuğunu seçin.

    ![Office Online Yöneticisi](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. **Kullanıcı Ekle**' yi seçin.

    ![Kullanıcı ekleme](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Öncü yazıcı hizmeti için yeni bir kullanıcı oluşturun. Aşağıdaki ayarları yapılandırın:

    -   Bir parola girin ve "ilk oturum açtıklarında bu kullanıcının parolasını değiştirmesini yap" seçeneğini kaldırın.
    -   Kullanıcı rolü olarak "Kullanıcı (yönetici erişimi yok)" seçeneğini belirleyin.
    -   Sonraki ekran yakalama bölümünde gösterilen ürün lisansını seçin. Seçtiğiniz lisans için ücretlendirilirsiniz. Bu çözüm, Dynamics CRM Online temel lisansı ile de çalışır.
    
    ![Kullanıcı izinlerini ve lisansını yapılandırma](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Güvenlik ayarları

Son adım, oluşturduğunuz kullanıcıyı müşteri adaylarını yazmak üzere etkinleştirmektir.

1.  Dynamics CRM Online 'da oturum açın.
2.  **Ayarlar**' da **güvenlik**' i seçin.
    
    ![Güvenlik ayarları](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  **Kullanıcı izinleri**' nde oluşturduğunuz kullanıcıyı seçin ve ardından **Kullanıcı Rollerini Yönet**' i seçin. Rolü atamak için **Microsoft Market lider yazıcısını** denetleyin.

    ![Kullanıcı rolü atama](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Bu rol, içeri aktardığınız çözüm tarafından oluşturulur ve yalnızca müşteri adaylarını yazmak ve uyumluluk sağlamak üzere çözüm sürümünü izlemek için izinlere sahiptir.

4.  Güvenlik bölümünde **güvenlik rolleri** ' ni seçin ve Microsoft Market öncü yazıcı için rolü bulun.
    
    ![Güvenlik lideri yazıcısını yapılandırma](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. **Çekirdek Kayıtlar** sekmesini seçin. Kullanıcı VARLıĞı Kullanıcı arabirimi için Oluştur/oku/yaz seçeneğini etkinleştirin.

    ![Kullanıcı için oluşturma/okuma/yazma özelliğini etkinleştir](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Yukarı kaydırın

Oluşturulan hesap bilgilerini Bulut İş Ortağı Portalı ekleyerek, müşteri adayı yönetimi için Dynamics CRM 'yi yapılandırmayı son yapın. Örneğin:

-   **Azure Active Directory** - **uygulama kimliği** (örnek: *23456052-aaaa-bbbb-8662-1234df56788f*), **dizin kimliği** (örnek: *12345678-8af1-4asf-1234-12234D01db47*) ve **uygulama anahtarı** ( Örnek: *1234Abcdedfrz/G/FdY0aUABCEDcqhbLn/ST122345nBc =* ).
-   **Office 365** - **URL** (örnek: *https://contoso.crm4.dynamics.com* ), **kullanıcı adı** (örnek: *contoso\@contoso.onmicrosoft.com*) ve **parola** (örnek: *P\@ssw0rd*).
