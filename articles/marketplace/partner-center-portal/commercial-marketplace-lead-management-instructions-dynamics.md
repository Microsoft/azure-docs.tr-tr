---
title: Müşteri katılımı için Dynamics 365 için lider yönetimi | Azure Marketi
description: Müşteri katılımı için Dynamics 365 için lider yönetimini yapılandırın.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 37cf613b6e0bd2ec9910dd3e7431c0feaa02431c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812300"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Müşteri katılımı için Dynamics 365 için lider yönetimini yapılandırma

Bu makalede, müşteri katılımı için Dynamics 365 'nin nasıl ayarlanacağı açıklanır (daha önce Dynamics CRM Online), satış fırsatlarını Market teklifinizi işleyecek şekilde işlemek için [buradaki](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) değişiklik hakkında daha fazla bilgi edinin. 

>[!Note]
>Bu yönergeler, Microsoft tarafından barındırılan, müşteri katılım ortamına yönelik bulut Dynamics 365 için özeldir. Dynamics şirket içi bir ortama doğrudan bağlanmak Şu anda desteklenmemektedir. müşteri adaylarını almak için bir [https uç noktası](./commercial-marketplace-lead-management-instructions-https.md) veya [Azure tablosu](./commercial-marketplace-lead-management-instructions-azure-table.md) yapılandırma gibi müşteri adaylarını almanızı sağlamak için başka seçenekler de vardır.

## <a name="prerequisites"></a>Ön koşullar

Bu makaledeki adımları tamamlamak için aşağıdaki Kullanıcı izinleri gereklidir:

* Bir çözüm yükleyebilmek ve bu yönergeleri takip edebilmek için Dynamics 365 for Customer Engagement örneğinde yönetici olmanız gerekir.
* Müşteri adayı hizmeti için Market tekliflerden müşteri adaylarını göndermek üzere kullanılan yeni bir hizmet hesabı oluşturmak için bir kiracı yöneticisi olmanız gerekir.
* Office 365 Yönetici portalına erişiminizin olması gerekir.
* Azure portal erişiminizin olması gerekir.

## <a name="install-the-solution"></a>Çözümü yükler

1.  [Microsoft Market lider yazıcı çözümünü](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) indirin ve bilgisayarınıza yerel olarak kaydedin.

2.  Dynamics örneğinizin URL 'sine giderek (örneğin, `https://tenant.crm.dynamics.com`), müşteri katılımı için Dynamics 365 ' i açın.

3.  Üst gezinti çubuğunda dişli simgesini ve **Gelişmiş ayarları** seçerek erişim ayarları.
 
    ![Dynamics-Gelişmiş ayarlar](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Ayarlar sayfasında, üst gezinti çubuğundan ayar menüsüne erişin ve **çözümler**' i seçin.

    >[!Note]
    >Sonraki ekran yakalamadaki seçenekleri görmüyorsanız, devam etmeniz gereken izinleriniz yoktur. Dynamics 365 for Customer Engagement örneği üzerindeki bir yöneticiye ulaşın.

    ![Dynamics 365-çözümler](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Çözümler sayfasından bir kez **Içeri aktar** ' ı seçin ve adım 1 ' de Indirdiğiniz *Microsoft Market lider yazıcısı* çözümünü kaydettiğiniz yere gidin.

    ![Müşteri katılımı için Dynamics 365-Içeri aktarma](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Çözümü Içeri aktarma Sihirbazı 'nı izleyerek çözümü içeri aktarmayı doldurun.

## <a name="configure-user-permissions"></a>Kullanıcı izinlerini yapılandırma

Müşteri için Dynamics 365 katılım örneğine müşteri adayları yazmak için, bir hizmet hesabını bizimle paylaşmanız ve hesap için izinleri yapılandırmanız gerekir.

Hizmet hesabı oluşturmak ve izinleri atamak için aşağıdaki adımları kullanın. **Azure Active Directory** veya **Office 365**' i kullanabilirsiniz.

>[!Note]
>Seçtiğiniz kimlik doğrulama seçeneğine bağlı olarak, tercihinize göre ilgili yönergelere atlayabilirsiniz. Bkz. [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) veya [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Müşteri adaylarını almak için Kullanıcı adınızı/parolanızı güncelleştirme gereksiniminden hiç ihtiyaç duymamanız gerektiği için bu seçeneği öneririz. Azure Active Directory seçeneğini kullanmak için, uygulama KIMLIĞI, uygulama anahtarı ve Dizin KIMLIĞINI Active Directory uygulamanızdan sağlarsınız.

Müşteri katılımı için Dynamics 365 Azure Active Directory yapılandırmak için aşağıdaki adımları kullanın.

1. [Azure Portal](https://portal.azure.com/)oturum açın ve ardından sol gezinmede Azure Active Directory hizmeti seçin.

2. Sol gezinti Azure Active Directory **Özellikler** ' i seçin ve bu SAYFADAKI **dizin kimliği** değerini kopyalayın. Market teklifiniz için müşteri adaylarını almak üzere Yayımlama portalında sağlamanız gereken *DIZIN kimliği* değeri olduğundan, bu değeri kaydedin.

    ![Azure Active Directory-Özellikler](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Azure Active Directory sol gezinti **uygulama kayıtları** seçin ve ardından bu sayfadaki **Yeni kayıt** ' ı seçin.
4. Uygulama adı için bir ad girin. Anlamlı bir uygulama adı girin.
5. Desteklenen hesap türleri altında, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
6. Yeniden yönlendirme URI 'SI altında **Web** ' i seçin ve bir URI sağlayın (örneğin `https://contosoapp1/auth`). 
7. **Kaydol**’u seçin.

    ![Bir uygulamayı kaydetme](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Uygulamanız kayıtlı olduğuna göre, uygulamanın genel bakış sayfasına erişin ve **uygulama (istemci) kimliği** değerini bu sayfaya kopyalayın. Market teklifinizin müşteri adaylarını almak için Yayımlama portalında ve Dynamics 'te sağlamanız gereken *uygulama (istemci) kimliği* değeri olduğundan bu değeri kaydedin.

    ![Uygulama (istemci) KIMLIĞI](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Uygulamanın sol gezinmesinin **sertifika ve gizli** dizileri ' ni seçin ve bu sayfada **yeni istemci parolası** ' nı seçin. İstemci parolası için anlamlı bir açıklama girin ve süresi dolan **hiçbir zaman** seçeneğini belirleyin. İstemci parolasını oluşturmak için **Ekle** ' yi seçin.

    ![Uygulama-sertifika ve gizlilikler](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. İstemci parolası başarıyla oluşturulduktan hemen sonra, **istemci gizli değerini kopyalayın**. Sayfadan ayrıldıktan sonra değeri alamazsınız. Market teklifinizin müşteri adaylarını almak için Yayımlama portalında sağlamanız gereken *istemci gizli* değeri olduğundan, bu değeri kaydedin. 
11. Uygulamaların sol gezinti listesinden **API izinleri** ' ni seçin ve **izin Ekle**' yi seçin.
12. Microsoft API 'Leri seçin ve ardından, API olarak **DYNAMICS CRM** ' yi seçin.
13. *Uygulamanız için gereken izin türü*altında, **temsilci izinleri** seçildiğinden emin olun. *Kuruluş kullanıcıları olarak user_impersonation erişim Common Data Service*iznini denetleyin. **Izin Ekle**' yi seçin.

    ![İzin ekleme](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Azure portal 1-13 adımlarını tamamladıktan sonra, URL 'ye giderek (`https://tenant.crm.dynamics.com`gibi) Dynamics 365 for Customer Engagement örneğine gidin.
15. Üst gezinti çubuğunda dişli simgesini ve **Gelişmiş ayarları** seçerek erişim ayarları.
16. Ayarlar sayfasında, üst gezinti çubuğundan ayar menüsüne erişin ve **güvenlik**' i seçin.
17. Güvenlik sayfasından bir kez **Kullanıcı**' yı seçin.  Kullanıcılar sayfasında, **uygulama kullanıcılarına**geçiş yapmak Için "etkin kullanıcılar" açılan listesini seçin.
18. Yeni bir kullanıcı oluşturmak için **Yeni** ' yi seçin. 

    ![Yeni kullanıcı oluşturma](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. **Yeni Kullanıcı**' da, Kullanıcı: uygulama kullanıcısının seçili olduğundan emin olun. Bu bağlantıyla birlikte kullanmak istediğiniz kullanıcı için bir Kullanıcı adı, tam ad ve e-posta adresi sağlayın. Ayrıca, adım 8 ' den Azure portal oluşturduğunuz uygulamanın **uygulama kimliğini** de yapıştırın. Kullanıcıyı eklemeyi tamamladıktan **sonra Kaydet ve Kapat ' ı** seçin.

    ![Yeni Kullanıcı](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Bu Kullanıcı için bağlantıyı yapılandırmayı tamamlaymak üzere bu makaledeki "güvenlik ayarları" na gidin.

### <a name="office-365"></a>Office 365

Azure Active Directory kullanmak istemiyorsanız, *Microsoft 365 Yönetim merkezinde*yeni bir kullanıcı kaydedebilirsiniz. Müşteri adayı almaya devam etmek için Kullanıcı adınızı/parolanızı her 90 günde bir güncelleştirmeniz gerekir.

Müşteri katılımı için Dynamics 365 için Office 365 ' i yapılandırmak için aşağıdaki adımları kullanın.

1. [Microsoft 365 Yönetim merkezinde](https://admin.microsoft.com)oturum açın.

2. **Kullanıcı Ekle**' yi seçin.

    ![Microsoft 365 Yönetim Merkezi-Kullanıcı ekleme](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Öncü yazıcı hizmeti için yeni bir kullanıcı oluşturun. Aşağıdaki ayarları yapılandırın:

    * Kullanıcı adı girin
    * Bir parola girin ve "ilk oturum açtıklarında bu kullanıcının parolasını değiştirmesini yap" seçeneğini kaldırın.
    * Kullanıcı rolü olarak "Kullanıcı (yönetici erişimi yok)" seçeneğini belirleyin.
    * Sonraki ekran yakalamada Gösterilen ürün lisansı olarak "Dynamics 365 müşteri katılımı planı" nı seçin. Seçtiğiniz lisans için ücretlendirilirsiniz. 

Market teklifinizin müşteri adaylarını almak için Yayımlama portalında sağlamanız gereken *Kullanıcı adı ve parola* değerleri olduklarından bu değerleri kaydedin.

![Microsoft 365 Yönetim Merkezi-Yeni Kullanıcı](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Güvenlik ayarları

Son adım, oluşturduğunuz kullanıcıyı müşteri adaylarını yazmak üzere etkinleştirmektir.

1. Dynamics örneğinizin URL 'sine giderek (örneğin, `https://tenant.crm.dynamics.com`), müşteri katılımı için Dynamics 365 ' i açın.
2. Üst gezinti çubuğunda dişli simgesini ve **Gelişmiş ayarları** seçerek erişim ayarları.
3. Ayarlar sayfasında, üst gezinti çubuğundan ayar menüsüne erişin ve **güvenlik**' i seçin.
4. Güvenlik sayfasında, **Kullanıcılar** ' ı seçin ve bu belgenin Kullanıcı izinlerini Yapılandır bölümünde oluşturduğunuz kullanıcıyı seçin ve ardından **rolleri Yönet**' i seçin. 

    ![Rolleri yönetme](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. "Microsoft Market lider yazıcısı" rol adını arayın ve kullanıcıyı rolü atamak için seçin.

    ![Kullanıcı rollerini yönetme](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Bu rol, içeri aktardığınız çözüm tarafından oluşturulur ve yalnızca müşteri adaylarını yazmak ve uyumluluk sağlamak üzere çözüm sürümünü izlemek için izinlere sahiptir.

6. Güvenlik sayfasına geri gidin ve **güvenlik rolleri**' ni seçin. "Microsoft Market lider yazıcısı" rolünü arayın ve seçin.

    ![Güvenlik rolleri](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Güvenlik rolünde bir kez, **temel kayıtlar** sekmesini seçin. "Kullanıcı VARLıĞı Kullanıcı arabirimi ayarları" varlığını arayın ve ilgili dairelerin her birine bir kez tıklayarak bu varlık için Kullanıcı için oluşturma, okuma ve yazma izinlerini (1/4 sarı daire) etkinleştirin.

    ![Microsoft Market öncü yazıcı-çekirdek kayıtlar](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Şimdi **Özelleştirme** sekmesine gidin. "sistem işi" varlığını arayın ve ilgili dairelerin her birine dört kez tıklayarak bu varlık için kuruluşa (düz yeşil) izinleri okuma, yazma ve bu varlığa eklemesine izin veren.

    ![Microsoft Market öncü yazıcı-özelleştirme](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Kaydet ve Kapat**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Müşteri katılımı Için teklifinizi Dynamics 365 ' e göndermek için teklifinizi yapılandırın

Yayımlama portalında teklifiniz için müşteri adayı yönetim bilgilerini yapılandırmaya hazırsanız, aşağıdaki adımları izleyin:

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
2. Müşteri adayı Yönetimi bölümünde **Bağlan** ' ı seçin.

    ![Müşteri adayı yönetimine Bağlan](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Bağlantı ayrıntıları açılır penceresinde, müşteri adayı hedefi için **Dynamics 365** ' i seçin.

    ![Bağlantı ayrıntıları-müşteri adayı hedefi](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. `https://contoso.crm4.dynamics.com`gibi **Dynamics 365 örnek URL 'sini** sağlayın.
5. **Kimlik doğrulaması**, Azure Active Directory veya Office 365 yöntemini seçin. 
6. Azure Active Directory ' yi seçtiyseniz, **uygulama (istemci) kimliğini** (örnek: `23456052-aaaa-bbbb-8662-1234df56788f`), **dizin kimliğini** (örnek: `12345678-8af1-4asf-1234-12234d01db47`) ve **istemci gizliliğini** sağlayın (örnek: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Bağlantı ayrıntıları-Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Office 365 ' i seçtiyseniz, **Kullanıcı adını** (örnek: `contoso@contoso.onmicrosoft.com`) ve parolayı (örnek: `P@ssw0rd`) belirtin.

    ![Bağlantı ayrıntıları-Kullanıcı adı](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.
