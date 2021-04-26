---
title: Dynamics 365 müşteri katılımı için lider Yönetimi-Microsoft ticari Market
description: Microsoft AppSource ve Azure Marketi 'nden müşteri adaylarını yönetmek için Dynamics 365 müşteri katılımı ayarlamayı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 067a30d8f16b06b1b29ad494363d180886d1ab8b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560089"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Dynamics 365 müşteri katılımı için lider yönetimini yapılandırma

Bu makalede, Dynamics 365 müşteri katılımı ayarlama (daha önce Dynamics CRM Online olarak adlandırılır) açıklanmaktadır. Ticari Market teklifinizden gelen satış fırsatlarını işlemek için [müşteri katılımı ve SharePoint Online ile sunucu tabanlı kimlik doğrulaması yapılandırma](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) konusundaki değişiklik hakkında daha fazla bilgi edinin.

>[!NOTE]
>Bu yönergeler, Dynamics 365 müşteri katılımı için Microsoft tarafından barındırılan bulut ortamına özgüdür. Dynamics şirket içi bir ortama doğrudan bağlanmak Şu anda desteklenmemektedir. Bir [https uç noktası](./commercial-marketplace-lead-management-instructions-https.md) veya [Azure tablosu](./commercial-marketplace-lead-management-instructions-azure-table.md)yapılandırma gibi müşteri adaylarını almanızı sağlamak için başka seçenekler de vardır.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki Kullanıcı izinleri gereklidir:

* Dynamics 365 müşteri katılım Örneğinizde bir çözüm yükleyebilmeleri için yönetici hakları.
* Müşteri adayı hizmeti için ticari Market tekliflerden müşteri adaylarını göndermek üzere kullanılan yeni bir hizmet hesabı oluşturmak için kiracı yöneticisi hakları.
* Yönetici portalına erişim.
* Azure portal erişim.

## <a name="install-the-solution"></a>Çözümü yükler

1. [Microsoft Market lider yazıcı çözümünü](https://referralsflowpackages.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)indirin ve bilgisayarınıza yerel olarak kaydedin.

1. Dynamics örneğiniz için URL 'ye giderek Dynamics 365 müşteri katılımı 'nı açın `https://tenant.crm.dynamics.com` .

1. Üstteki çubukta dişli simgesini seçin ve ardından **Gelişmiş ayarlar**' ı seçin.

    ![Dynamics 365 Gelişmiş ayarları menü öğesi](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. **Ayarlar** sayfasında, üstteki çubukta **Ayarlar** menüsünü açın ve **çözümler**' i seçin.

    >[!NOTE]
    >Aşağıdaki ekranda seçenekleri görmüyorsanız, devam etmeniz gereken izinlere sahip değilsiniz. Dynamics 365 müşteri katılımı Örneğinizde bir yöneticiye başvurun.

    ![Dynamics 365 çözüm seçeneği](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. **Çözümler** sayfasında **içeri aktar** ' ı seçin ve adım 1 ' de indirdiğiniz **Microsoft Market öncü yazıcı** çözümünü kaydettiğiniz yere gidin.

    ![İçeri Aktar düğmesi](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Çözümü Içeri aktarma Sihirbazı 'nı izleyerek çözümü içeri aktarmayı doldurun.

## <a name="configure-user-permissions"></a>Kullanıcı izinlerini yapılandırma

Müşteri adaylarını Dynamics 365 müşteri katılımı örneğine yazmak için Microsoft ile bir hizmet hesabı paylaşmanız ve hesap için izinleri yapılandırmanız gerekir.

Hizmet hesabı oluşturmak ve izinleri atamak için aşağıdaki adımları kullanın. Azure Active Directory veya Office 365 ' i kullanabilirsiniz.

>[!NOTE]
>Seçtiğiniz kimlik doğrulama seçeneğine göre ilgili yönergelere atlayın. Bkz. [Azure Active Directory](#azure-active-directory) veya [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Müşteri adaylarını almak için Kullanıcı adınızı veya parolanızı güncelleştirmeniz gerekmeyeceğinden bu seçeneği öneririz. Azure Active Directory seçeneğini kullanmak için, uygulama KIMLIĞI, uygulama anahtarı ve Dizin KIMLIĞINI Active Directory uygulamanızdan sağlarsınız.

Dynamics 365 müşteri katılımı için Azure Active Directory yapılandırmak için:

1. [Azure portalında](https://portal.azure.com/) oturum açın. Sol bölmede **Azure Active Directory**’yi seçin.

1. **Özellikler**' i seçin ve Dizin **ÖZELLIKLERI** sayfasına **dizin kimliği** değerini kopyalayın. Market teklifiniz için müşteri adaylarını almak üzere Yayımlama portalında sağlamanız gerekeceğinden bu değeri kaydedin.

    ![Azure Active Directory Özellikler menü öğesi](media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Azure Active Directory sol bölmeden **uygulama kayıtları** seçin ve ardından bu sayfadaki **Yeni kayıt** ' ı seçin.
1. Uygulama adı için anlamlı bir ad girin.
1. **Desteklenen hesap türleri** altında, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si (isteğe bağlı)** altında **Web** ' i SEÇIN ve gibi bir URI girin `https://contosoapp1/auth` .
1. **Kaydet**’i seçin.

    ![Uygulama sayfası kaydetme](media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Uygulamanız kaydoldığına göre uygulamanın genel bakış sayfasına erişin. Bu sayfadaki **uygulama (istemci) kimliği** değerini kopyalayın. Market teklifinizin müşteri adaylarını almak için Yayımlama portalında ve Dynamics 365 ' de sağlamanız gerekeceğinden bu değeri kaydedin.

    ![Uygulama (istemci) KIMLIK kutusu](media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Uygulamanın sol bölmesinde bulunan **gizli dizileri & sertifikalar** ' ı seçin ve **yeni istemci parolası** düğmesini seçin. İstemci parolası için anlamlı bir açıklama girin ve **süresi dolan** **hiçbir zaman** seçeneğini belirleyin. İstemci parolasını oluşturmak için **Ekle** ' yi seçin.

    ![Sertifikalar & gizlilikler menü öğesi](media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. İstemci parolası başarıyla oluşturulduktan hemen sonra, **istemci gizli** değerini kopyalayın. Sayfadan ayrıldıktan sonra değeri alamazsınız. Market teklifiniz için müşteri adaylarını almak üzere Yayımlama portalında sağlamanız gerekeceğinden bu değeri kaydedin. 
1. Uygulamanın sol bölmesinde **API izinleri** ' ni seçin ve **+ izin Ekle**' yi seçin.
1. **Microsoft API 'leri** seçin ve ardından, API olarak **Dynamics CRM** ' yi seçin.
1. **Uygulamanız ne tür izinler gerektiriyor?** altında, **temsilci izinleri** seçildiğinden emin olun.
1. **İzin** altında, **Şirket kullanıcıları olarak erişim Common Data Service** için **user_impersonation** onay kutusunu seçin. Ardından **Izin Ekle**' yi seçin.

    ![İzin Ekle düğmesi](media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Azure portal 1 ' den 14 ' e kadar olan adımları tamamladıktan sonra, URL 'ye giderek (gibi) Dynamics 365 müşteri katılım örneğinizle gidin `https://tenant.crm.dynamics.com` .
1. Üstteki çubukta dişli simgesini seçin ve ardından **Gelişmiş ayarlar**' ı seçin.
1. **Ayarlar** sayfasında, üstteki çubukta **Ayarlar** menüsünü açın ve **güvenlik**' i seçin.
1. **Güvenlik** sayfasında, **Kullanıcılar**' ı seçin. **Kullanıcılar** sayfasında, **etkin kullanıcılar** açılan ' i seçin ve ardından **uygulama kullanıcıları**' nı seçin.
1. Yeni bir kullanıcı oluşturmak için **Yeni** ' yi seçin.

    ![Yeni kullanıcı oluşturma](media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. **Yeni Kullanıcı** bölmesinde, **Kullanıcı: uygulama kullanıcısı** ' nın seçili olduğundan emin olun. Bu bağlantıyla birlikte kullanmak istediğiniz kullanıcı için bir Kullanıcı adı, tam ad ve e-posta adresi sağlayın. Ayrıca, adım 8 ' den Azure portal oluşturduğunuz uygulamanın **uygulama kimliğini** de yapıştırın. Kullanıcı ekleme işleminin bitmesini **kapatmak Için kaydet & kapat** ' ı seçin.

    ![Yeni Kullanıcı bölmesi](media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Bu Kullanıcı için bağlantıyı yapılandırmayı tamamlaymak üzere bu makaledeki "güvenlik ayarları" bölümüne gidin.

### <a name="office-365"></a>Office 365

Azure Active Directory kullanmak istemiyorsanız, Microsoft 365 Yönetim merkezinde yeni bir kullanıcı kaydedebilirsiniz. Müşteri adaylarını almaya devam etmek için Kullanıcı adınızı ve parolanızı her 90 günde bir güncelleştirmeniz gerekir.

Dynamics 365 müşteri katılımı için Office 365 'yi yapılandırmak için:

1. [Microsoft 365 yönetim merkezinde](https://admin.microsoft.com) oturum açın.

1. **Kullanıcı Ekle**' yi seçin.

    ![Microsoft 365 Yönetim Merkezi Kullanıcı ekleme seçeneği](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Öncü yazıcı hizmeti için yeni bir kullanıcı oluşturun. Aşağıdaki ayarları yapılandırın:

    * Bir Kullanıcı adı girin.
    * Bir parola girin ve **ilk oturum açtıklarında bu kullanıcının parolasını değiştirmesini yap** seçeneğini temizleyin.
    * Kullanıcı rolü olarak **Kullanıcı (yönetici erişimi yok)** seçeneğini belirleyin.
    * Aşağıdaki ekranda gösterildiği gibi, ürün lisansı olarak **Dynamics 365 müşteri katılımı planı** ' nı seçin. Seçtiğiniz lisans için ücretlendirilirsiniz. 

Market teklifinizin müşteri adaylarını almak için yayımlama portalındaki **Kullanıcı adı** ve **parola** değerlerini sağlamanız gerekeceğinden bu değerleri kaydedin.

![Microsoft 365 Yönetim Merkezi Yeni Kullanıcı bölmesi](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Güvenlik ayarları

Son adım, oluşturduğunuz kullanıcıyı müşteri adaylarını yazmak üzere etkinleştirmektir.

1. Dynamics örneğiniz için URL 'ye giderek Dynamics 365 müşteri katılımı 'nı açın `https://tenant.crm.dynamics.com` .
1. Üstteki çubukta dişli simgesini seçin ve ardından **Gelişmiş ayarlar**' ı seçin.
1. **Ayarlar** sayfasında, üstteki çubukta **Ayarlar** menüsünü açın ve **güvenlik**' i seçin.
1. **Güvenlik** sayfasında, **Kullanıcılar** ' ı seçin ve bu belgenin "Kullanıcı izinlerini yapılandırma" bölümünde oluşturduğunuz kullanıcıyı seçin. Ardından **rolleri Yönet**' i seçin.

    ![Rolleri Yönet sekmesi](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. **Microsoft Market lider yazıcısının** rol adını arayın ve kullanıcıyı rolü atamak için seçin.

    ![Kullanıcı rollerini yönetme bölmesi](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Bu rol, içeri aktardığınız çözüm tarafından oluşturulur ve yalnızca müşteri adaylarını yazmak ve uyumluluk sağlamak üzere çözüm sürümünü izlemek için izinlere sahiptir.

1. **Güvenlik** sayfasına dönün ve **güvenlik rolleri**' ni seçin. **Microsoft Market lider yazıcısını** bulun ve seçin.

    ![Güvenlik rolleri bölmesi](media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Güvenlik rolünde, **temel kayıtlar** sekmesini seçin. **Kullanıcı varlığı Kullanıcı arabirimi ayarları** öğesini arayın. İlgili radyo düğmelerini seçerek bu varlık için Kullanıcı için oluşturma, okuma ve yazma izinlerini (1/4 sarı daire) etkinleştirin.

    ![Microsoft Market öncü yazıcı çekirdek kayıtları sekmesi](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. **Özelleştirme** sekmesinde, **sistem işi** öğesini arayın. İlgili radyo düğmelerini seçerek bu varlık için kuruluşa (düz yeşil radyo düğmeleri) okuma, yazma ve yeniden Appendı 'yi etkinleştirin.

    ![Microsoft Market öncü yazıcı özelleştirme sekmesi](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. **Kaydet ve kapat**’ı seçin.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Teklifinizi Dynamics 365 müşteri katılımında müşteri adaylarına göndermek için yapılandırın

Yayımlama portalında teklifinizin müşteri adayı yönetim bilgilerini yapılandırmak için:

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
1. **Müşteri müşteri adayları** bölümünde **Bağlan**' ı seçin.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Müşteri liderleri":::

1. Bağlantı ayrıntıları açılır penceresinde, müşteri adayı hedefi için **Dynamics 365 müşteri katılımı** ' nı seçin.

    ![Lider hedefi kutusu](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Dynamics 365 örneğinin **URL 'sini** girin (örneğin,) `https://contoso.crm4.dynamics.com` .

1. Azure Active Directory ya da Office 365 **kimlik doğrulaması** yöntemini seçin.
1. **Azure Active Directory** seçtiyseniz, **uygulama (istemci) kimliğini** (örneğin, `23456052-aaaa-bbbb-8662-1234df56788f` ), **dizin kimliğini** (örneğin,) `12345678-8af1-4asf-1234-12234d01db47` ve **istemci gizli** anahtarını (örneğin, `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=` ) girin.

    ![Azure Active Directory seçilen kimlik doğrulaması](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. **Office 365**' i seçtiyseniz, **Kullanıcı adını** (örneğin, `contoso@contoso.onmicrosoft.com` ) ve **parolayı** (örneğin, `P@ssw0rd` ) girin.

    ![Office 365 Kullanıcı adı kutusu](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. **İletişim e-postası** için, şirketinizde yeni bir müşteri adayı alındığında e-posta bildirimleri alması gereken kişilerin e-posta adreslerini girin. Birden çok e-posta adresini noktalı virgülle ayırarak girebilirsiniz.
1. **Tamam**’ı seçin.

Bir müşteri adayı hedefine başarıyla bağlandığınızdan emin olmak için **Doğrula** düğmesini seçin. Başarılı olursa, müşteri adayı hedefinde bir test liderine sahip olacaksınız.

![İletişim e-posta kutusu](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.
