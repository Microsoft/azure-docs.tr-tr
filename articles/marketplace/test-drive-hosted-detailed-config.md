---
title: Azure Marketi 'nde barındırılan bir test sürücüsü için ayrıntılı yapılandırma
description: Ticari Market 'teki barındırılan bir test sürücüsü için açıklama yapılandırma adımları
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 04/20/2021
ms.openlocfilehash: f11f1d5601a61bbd9b8729b478c278db8d3e73dc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812434"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Barındırılan test sürücüleri için ayrıntılı yapılandırma

Bu makalede, Dynamics 365 for Customer Engagement veya Operations için Dynamics 365 için bir barındırılan test sürücüsünün nasıl yapılandırılacağı açıklanır.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Dynamics 365 müşteri katılımı için yapılandırma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/)' nde oturum açın.
2. Yukarıdaki bağlantıya erişemiyorsanız, uygulamanızı yayımlamak için [buraya](https://appsource.microsoft.com/partners/list-an-app) bir istek göndermeniz gerekir. İsteği gözden geçirdikten sonra, yayımlama işlemini başlatmak için size erişim hakkı vermiş olursunuz.
3. **Müşteri için mevcut bir dynamics 365** teklif teklifi bulun veya yeni bir **Dynamics 365 müşteri katılımı** teklifi oluşturun.
4. **Sınama sürücüsünü etkinleştir** onay kutusunu seçin ve bir **test sürücüsü türü** seçin (aşağıdaki madde işaretine bakın) ve ardından **Kaydet**' i seçin.

    [![' Test sürücüsünü etkinleştir ' onay kutusu seçiliyor.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Test sürücüsü türü** – **Microsoft hosted (müşteri için Dynamics 365, PowerApps &)** seçeneğini belirleyin. Bu, Microsoft 'un Kullanıcı hazırlama ve sağlamayı kaldırma sınama sürücüsünü gerçekleştiren hizmeti barındırdığını ve bakımını gerçekleştireceğini belirtir.

5. [Bu yönergeleri](./test-drive-azure-subscription-setup.md)kullanarak kiracınızdaki test sürücüsü kullanıcılarını sağlama ve sağlamayı kaldırma iznini Microsoft AppSource verin. Bu adımda, aşağıda bahsedilen **Azure AD UYGULAMASı kimliği** ve **Azure AD uygulaması anahtar** değerlerini oluşturacaksınız.
6. **Sınama sürücüsü teknik yapılandırma** sayfasında bu alanları doldurun.

    [![Sınama sürücüsü teknik yapılandırma sayfası.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maksimum eşzamanlı test sürücüsü** : aynı anda çalışan etkin bir test sürücüsüne sahip olabilecek eşzamanlı kullanıcı sayısı. Test sürücüleri etkin durumdayken her Kullanıcı bir Dynamics lisansını kullanır, bu nedenle, sınama sürücüsü kullanıcıları için en az bu sayıda Dynamics lisansın kullanılabilir olmasını sağlayın. 3 ile 5 arasında önerilir.
    - **Sınama sürücüsü süresi** – kullanıcının test sürücüsünün etkin olacağı saat sayısı. Sürenin süresi dolduktan sonra, Kullanıcı kiracınızdan kaldırılacak. Uygulamanızın karmaşıklığına bağlı olarak 2-24 saat önerilir. Kullanıcı, zaman aşımına uğrar ve test sürücüsüne yeniden erişmek istediğinizde, her zaman başka bir test sürücüsü isteyebilir.
    - **Örnek URL 'si** – test sürücüsünü başlattıklarında Kullanıcı tarafından gönderilecek test sürücüsü URL 'si. Bu, genellikle uygulamanızın ve örnek verilerinizin yüklendiği Dynamics 365 örneğinizin URL 'sidir. Örnek değer: `https://testdrive.crm.dynamics.com` .
    - **Örnek Web API URL 'si** : Dynamics 365 örneğiniz IÇIN Web API URL 'si. Microsoft Dynamics 365 Örneğinizde oturum açarak bu değeri alın ve   >  **Özelleştirme**  >  **Geliştirici kaynakları**  >  **örnek Web API 'sini** ayarlamak ve adresi (URL) kopyalamak için gezinin. Örnek değer: 

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Örnek Web API 'SI örneği.":::

    - **Rol adı** – test sürücüsü için oluşturduğunuz özel Dynamics 365 güvenlik rolünün adı veya var olan bir rolü kullanabilirsiniz. Yeni bir rol, bir müşteri katılımı örneğinde oturum açmak için role eklenmiş minimum gerekli ayrıcalıklara sahip olmalıdır. [Microsoft Dynamics 365 ' te oturum açmak için gereken en düşük ayrıcalıklara](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)bakın. Bu, kullanıcılara test sürücüleri sırasında atanacak olan roldür. Örnek değer: `testdriverole` .
    
        > [!IMPORTANT]
        > Güvenlik grubu denetiminin eklendiğinden emin olun. Bu, kullanıcının müşteri katılım örneğiyle eşitlenmesini sağlar.

    - **Azure Active Directory KIRACı kimliği** : Dynamics 365 örneğiniz için Azure kiracısı kimliği. Bu değeri almak için Azure Portal oturum açın ve **Azure Active Directory**  >  **Özellikler** ' e gidin ve dizin kimliğini kopyalayın. Örnek değer: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory kiracı adı** : Dynamics 365 örneğiniz Için Azure kiracısı adı. `<tenantname>.onmicrosoft.com` biçimini kullanın. Örnek değer: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory Uygulama kimliği** : 5. adımda oluşturduğunuz Azure ACTIVE DIRECTORY (ad) uygulamanın kimliği. Örnek değer: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Uygulama istemci gizli anahtarı** – 5. adımda oluşturulan Azure AD uygulaması için gizli anahtar. Azure Active Directory Örnek değer: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Market listeleme ayrıntılarını sağlayın. Daha fazla gerekli alanları görmek için **dil** ' i seçin.

    [![Market listeleme ayrıntıları sayfası.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Açıklama** : test sürücünüze genel bakış. Sınama sürücüsü sağlanırken bu metin kullanıcıya gösterilir. Bu alan, biçimlendirilen içerik sağlamak istiyorsanız HTML 'yi destekler.
    - **Kullanıcı el ile** – sürüş, kullanıcıların uygulamanızı nasıl kullanacağınızı anlamalarına yardımcı olan bir PDF Kullanıcı el kitabı.
    - **Test sürüşü videosu** – uygulamanızı gösteren bir video (isteğe bağlı).

## <a name="configure-for-dynamics-365-operations"></a>Dynamics 365 Işlemleri için yapılandırma

2. Yukarıdaki bağlantıya erişemiyorsanız, uygulamanızı yayımlamak için [buraya](https://appsource.microsoft.com/partners/list-an-app) bir istek göndermeniz gerekir. İsteği gözden geçirdikten sonra, yayımlama işlemini başlatmak için size erişim hakkı vermiş olursunuz.
3. Mevcut bir **dynamics 365 Operations** teklifi bulun veya yeni bir **Dynamics 365 for Operations** teklifi oluşturun.
4. **Sınama sürücüsünü etkinleştir** onay kutusunu seçin ve bir **test sürücüsü türü** seçin (aşağıdaki madde işaretine bakın) ve ardından **Kaydet**' i seçin.

    [![' Test sürücüsünü etkinleştir ' onay kutusunu seçin.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Test sürücüsü türü** – **Operations için Dynamics 365** seçeneğini belirleyin. Bu, Microsoft 'un Kullanıcı hazırlama ve sağlamayı kaldırma sınama sürücüsünü gerçekleştiren hizmeti barındırması ve bakımını gerçekleştireceği anlamına gelir.

5. [Bu yönergeleri](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)kullanarak kiracınızdaki test sürücüsü kullanıcılarını sağlama ve sağlamayı kaldırma iznini Microsoft AppSource verin. Bu adımda, aşağıda bahsedilen **Azure AD UYGULAMASı kimliği** ve **Azure AD uygulaması anahtar** değerlerini oluşturacaksınız.
6. **Sınama sürücüsü teknik yapılandırma** sayfasında bu alanları doldurun.

    [![Market teknik yapılandırma sayfası.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maksimum eşzamanlı test sürücüsü** : aynı anda çalışan etkin bir test sürücüsüne sahip olabilecek eşzamanlı kullanıcı sayısı. Test sürücüleri etkin durumdayken her Kullanıcı bir Dynamics lisansını kullanır, bu nedenle, sınama sürücüsü kullanıcıları için en az bu sayıda Dynamics lisansın kullanılabilir olmasını sağlayın. 3 ile 5 arasında önerilir.
    - **Sınama sürücüsü süresi** – kullanıcının test sürücüsünün etkin olacağı saat sayısı. Sürenin süresi dolduktan sonra, Kullanıcı kiracınızdan kaldırılacak. Uygulamanızın karmaşıklığına bağlı olarak 2-24 saat önerilir. Kullanıcı, zaman aşımına uğrar ve test sürücüsüne yeniden erişmek istediğinizde, her zaman başka bir test sürücüsü isteyebilir.
    - **Örnek URL 'si** – test sürücüsünü başlattıklarında Kullanıcı tarafından gönderilecek test sürücüsü URL 'si. Bu, genellikle uygulamanızın ve örnek verilerinizin yüklendiği Dynamics 365 örneğinizin URL 'sidir. Örnek değer: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory KIRACı kimliği** : Dynamics 365 örneğiniz için Azure kiracısı kimliği. Bu değeri almak için Azure Portal oturum açın ve **Azure Active Directory**  >  **Özellikler** ' e gidin ve dizin kimliğini kopyalayın. Örnek değer: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory kiracı adı** : Dynamics 365 örneğiniz Için Azure kiracısı adı. `<tenantname>.onmicrosoft.com` biçimini kullanın. Örnek değer: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory Uygulama kimliği** : 5. adımda oluşturduğunuz Azure ACTIVE DIRECTORY (ad) uygulamanın kimliği. Örnek değer: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Uygulama istemci gizli anahtarı** – 5. adımda oluşturulan Azure AD uygulaması için gizli anahtar. Azure Active Directory Örnek değer: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Deneme tüzel kişiliği** : bir deneme kullanıcısı atamak için yasal bir varlık sağlar. [Yasal bir varlık oluşturma veya değiştirme](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)konusunda yeni bir tane oluşturabilirsiniz.
    - **Rol adı** – test sürücüsü için oluşturduğunuz özel Dynamics 365 GÜVENLIK rolünün AOT adı (uygulama nesnesi ağacı). Bu, kullanıcılara test sürücüleri sırasında atanacak olan roldür.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Güvenlik Yapılandırması sayfası.":::

7. Market listeleme ayrıntılarını sağlayın. Daha fazla gerekli alanları görmek için **dil** ' i seçin.

    [![Market listeleme ayrıntıları sayfası.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Açıklama** : test sürücünüze genel bakış. Sınama sürücüsü sağlanırken bu metin kullanıcıya gösterilir. Bu alan, biçimlendirilen içerik sağlamak istiyorsanız HTML 'yi destekler.
    - **Kullanıcı el ile** – sürüş, kullanıcıların uygulamanızı nasıl kullanacağınızı anlamalarına yardımcı olan bir PDF Kullanıcı el kitabı.
    - **Test sürüşü videosu** – uygulamanızı gösteren bir video (isteğe bağlı).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->