---
title: Öğretici-Azure AD Yetkilendirme Yönetimi 'nde ilk erişim paketinizi oluşturma-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi 'nde ilk erişim paketinizi oluşturma konusunda adım adım öğretici.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 10/22/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: de4d4a1825149a512d7abdb192d8fb9d49e85a20
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174865"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Öğretici: Azure AD Yetkilendirme Yönetimi 'nde ilk erişim paketinizi oluşturma

Gruplar, uygulamalar ve siteler gibi çalışanların ihtiyaç duyduğu tüm kaynaklara erişimi yönetmek, kuruluşlar için önemli bir işlevdir. Çalışanlara, üretken olmaları gereken doğru erişim düzeyini vermek ve artık gerekli olmadığında erişimleri kaldırmak istiyorsunuz.

Bu öğreticide, Woodgrove Bank for It Administrator olarak çalışırsınız. Şirket içi kullanıcıların Self Servis isteğine sahip olduğu bir pazarlama kampanyası için kaynak paketi oluşturmanız istendi. İstekler onay gerektirmez ve kullanıcının erişimi 30 gün sonra dolar. Bu öğreticide, pazarlama kampanyası kaynakları yalnızca tek bir gruba üyedir, ancak gruplar, uygulamalar veya SharePoint Online siteleri koleksiyonu olabilir.

![Senaryoya genel bakış](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kaynak olarak grupla bir erişim paketi oluşturma
> * Dizininizdeki bir kullanıcının erişim istemesine izin verin
> * Dahili bir kullanıcının erişim paketini nasıl isteyebileceğini gösterir

İlk erişim paketinizi oluşturma da dahil olmak üzere Azure Active Directory yetkilendirme yönetimi dağıtma işleminin adım adım bir gösterimi için aşağıdaki videoyu görüntüleyin:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Önkoşullar

Azure AD Yetkilendirme Yönetimi 'ni kullanmak için aşağıdaki lisanslardan birine sahip olmanız gerekir:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 lisansı

Daha fazla bilgi için bkz. [Lisans gereksinimleri](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>1\. Adım: kullanıcıları ve grubu ayarlama

Kaynak dizininde paylaşılacak bir veya daha fazla kaynak bulunur. Bu adımda, yetkilendirme yönetimi için hedef kaynak olan Woodgrove Bank dizininde **Pazarlama Kaynakları** adlı bir grup oluşturacaksınız. Ayrıca, dahili bir istek sahibi ayarlarsınız.

**Önkoşul rolü:** Genel yönetici veya Kullanıcı Yöneticisi

![Kullanıcı ve grup oluşturma](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. [Azure Portal](https://portal.azure.com) genel yönetici veya Kullanıcı Yöneticisi olarak oturum açın.  

1. Sol gezinti bölmesinde **Azure Active Directory**' ye tıklayın.

1. Aşağıdaki iki kullanıcıyı oluşturun veya yapılandırın. Bu adları veya farklı adları kullanabilirsiniz. **Admin1** Şu anda oturum açmış olduğunuz Kullanıcı olabilir.

    | Adı | Dizin rolü |
    | --- | --- |
    | **Admin1** | Genel yönetici<br/>-veya-<br/>Kullanıcı Yöneticisi |
    | **Requestor1** | Kullanıcı |

1. **Atanan**üyelik türü ile **Pazarlama Kaynakları** adlı bir Azure AD güvenlik grubu oluşturun.

    Bu grup, yetkilendirme yönetimi için hedef kaynak olacaktır. Grup, başlamak için üyelerin boş olması gerekir.

## <a name="step-2-create-an-access-package"></a>2\. Adım: erişim paketi oluşturma

*Erişim paketi* , bir ekip ya da projenin ihtiyaç duyacağı ve ilkelerle ilişkilendirilen kaynak paketidir. Erişim paketleri, *kataloglar*olarak adlandırılan kapsayıcılar içinde tanımlanır. Bu adımda, **genel** katalogda bir **pazarlama kampanyası** erişim paketi oluşturacaksınız.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

![Erişim paketi oluşturma](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Azure portal, sol gezinti bölmesinde **Azure Active Directory**' e tıklayın.

1. Sol taraftaki menüden **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri**' ne tıklayın.  **Erişim reddedildi**görürseniz, dizininizde bir Azure AD Premium P2 lisansının bulunduğundan emin olun.

1. **Yeni erişim paketi**' ne tıklayın.

    ![Azure portal Yetkilendirme Yönetimi](./media/entitlement-management-shared/access-packages-list.png)

1. **Temel bilgiler** sekmesinde **pazarlama kampanyası** erişim paketi adı ve **Kampanya kaynaklarına yönelik açıklama erişimi**yazın.

1. **Katalog** açılan listesinden **genel**' e ayarlı bırakın.

    ![Yeni erişim paketi-temel bilgiler sekmesi](./media/entitlement-management-access-package-first/basics.png)

1. **İleri** ' ye tıklayarak **kaynak rolleri** sekmesini açın.

    Bu sekmede, erişim paketine dahil edilecek kaynakları ve kaynak rolünü seçersiniz.

1. **Gruplar ve takımlar**öğesine tıklayın.

1. Grupları seçin bölmesinde, daha önce oluşturduğunuz **Pazarlama Kaynakları** grubunu bulun ve seçin.

    Varsayılan olarak, **genel** kataloğun içinde ve dışında gruplar görürsünüz. **Genel** kataloğun dışında bir grup seçtiğinizde, **genel** kataloğa eklenir.

    ![Yeni erişim paketi-kaynak rolleri sekmesi](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Grubu listeye eklemek için **Seç** ' e tıklayın.

1. **Rol** açılan listesinde **üye**' i seçin.

    ![Yeni erişim paketi-kaynak rolleri sekmesi](./media/entitlement-management-access-package-first/resource-roles.png)

1. **İleri** ' ye tıklayarak **istekler** sekmesini açın.

    Bu sekmede bir istek ilkesi oluşturursunuz. Bir *ilke* , erişim paketine erişmek için kuralları veya guardrayları tanımlar. Kaynak dizinindeki belirli bir kullanıcının bu erişim paketini istemesine izin veren bir ilke oluşturursunuz.

1. **Erişim Isteyebilen kullanıcılar** bölümünde, **dizininizdeki kullanıcılar** ' a tıklayın ve ardından **belirli kullanıcılar ve gruplar**' a tıklayın.

    ![Yeni erişim paketi-Istekler sekmesi](./media/entitlement-management-access-package-first/requests.png)

1. **Kullanıcı ve Grup Ekle**' ye tıklayın.

1. Kullanıcıları ve grupları seç bölmesinde, daha önce oluşturduğunuz **Requestor1** kullanıcısını seçin.

    ![Yeni erişim paketi-Istekler sekmesi-kullanıcıları ve grupları seçin](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. **Seç**'e tıklayın.

1. **Onay** ve **istekleri etkinleştir** bölümlerine aşağı doğru kaydırın.

1. **Onay gerektir onay gerektir** ayarı **Hayır**olarak kalsın.

1. **Istekleri etkinleştir**için, bu erişim paketinin oluşturulduktan hemen istenmelerini etkinleştirmek için **Evet** ' i tıklatın.

    ![Yeni erişim paketi-Istekler sekmesi-onay ve etkinleştirme istekleri](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. **İleri** ' ye tıklayarak **yaşam döngüsü** sekmesini açın.

1. **Süre sonu** bölümünde, **erişim paketi atamalarının süresinin dolacağı** **gün sayısı**' nı ayarlayın.

1. Atamaları **30** gün **sonra dolacak** şekilde ayarlayın.

    ![Yeni erişim paketi-yaşam döngüsü sekmesi](./media/entitlement-management-access-package-first/lifecycle.png)

1. **İleri** ' ye tıklayarak **gözden geçir + oluştur** sekmesini açın.

    ![Yeni erişim paketi-gözden geçir + Oluştur sekmesi](./media/entitlement-management-access-package-first/review-create.png)

    Birkaç dakika sonra, erişim paketinin başarıyla oluşturulduğunu belirten bir bildirim görmeniz gerekir.

1. Pazarlama kampanyası erişim paketinin sol menüsünde **Genel Bakış ' a**tıklayın.

1. **Erişim portalı bağlantısını**Kopyala.

    Bu bağlantıyı bir sonraki adım için kullanacaksınız.

    ![Erişim paketine genel bakış-erişim portalı bağlantısı](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>3\. Adım: erişim ISTEME

Bu adımda, adımları **iç istek sahibi** olarak gerçekleştirirsiniz ve erişim paketine erişim isteği alırsınız. İstek sahipleri erişim portalı adlı bir site kullanarak isteklerini gönderir. Erişim portalı, Isteklerimin erişim paketleri isteklerini göndermesini sağlar, zaten erişimi olan erişim paketlerine bakın ve istek geçmişini görüntüler.

**Önkoşul rolü:** İç istek sahibi

1. Azure portal oturumunuzu kapatın.

1. Yeni bir tarayıcı penceresinde, önceki adımda kopyaladığınız erişimim portalı bağlantısına gidin.

1. Erişim portalından **Requestor1**olarak oturum açın.

    **Pazarlama kampanyası** erişim paketini görmeniz gerekir.

1. Gerekirse, **Açıklama** sütununda, erişim paketiyle ilgili ayrıntıları görüntülemek için oka tıklayın.

    ![Erişim Portalı-erişim paketlerim](./media/entitlement-management-shared/my-access-access-packages.png)

1. Paketi seçmek için onay işaretine tıklayın.

1. Istek erişimi bölmesini açmak için **erişim iste** ' ye tıklayın.

    ![Erişim Portalı-erişim ıste düğmesi](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. **İş gerekçe** kutusunda **yeni pazarlama kampanyası üzerinde çalıştığım**gerekçe yazın.

    ![Erişim Portalı-erişim ISTEME](./media/entitlement-management-shared/my-access-request-access.png)

1. **Gönder**' e tıklayın.

1. İsteğinizin gönderildiğini doğrulamak için sol taraftaki menüden **istek geçmişi** ' ne tıklayın.

## <a name="step-4-validate-that-access-has-been-assigned"></a>4\. Adım: erişimin atandığını doğrulama

Bu adımda, **iç istek sahibine** erişim paketi atandığını ve şimdi **Pazarlama Kaynakları** grubunun bir üyesi olduğunu onaylamanız gerekir.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Erişim portalımın oturumunu kapatın.

1. [Azure Portal](https://portal.azure.com) **admin1**olarak oturum açın.

1. **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri**' ne tıklayın.

1. **Pazarlama kampanyası** erişim paketini bulun ve tıklatın.

1. Sol menüde **istekler**' e tıklayın.

    Requestor1 ve Ilk ilke durumunu **teslim edildi**olarak görmeniz gerekir.

1. İstek ayrıntılarını görmek için isteğe tıklayın.

    ![Erişim paketi-Istek ayrıntıları](./media/entitlement-management-access-package-first/request-details.png)

1. Sol gezinti bölmesinde **Azure Active Directory**' ye tıklayın.

1. **Gruplar** ' a tıklayın ve **Pazarlama Kaynakları** grubunu açın.

1. **Üyeler**' e tıklayın.

    Üye olarak listelenmiş **Requestor1** görmeniz gerekir.

    ![Pazarlama Kaynakları üyeleri](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>5\. Adım: Kaynakları Temizleme

Bu adımda, yaptığınız değişiklikleri kaldırır ve **pazarlama kampanyası** erişim paketini silebilirsiniz.

**Önkoşul rolü:**  Genel yönetici veya Kullanıcı Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. **Pazarlama kampanyası** erişim paketini açın.

1. **Atamalar**' a tıklayın.

1. **Requestor1**için üç nokta ( **...** ) simgesini ve ardından **erişimi kaldır**' ı tıklatın. Görüntülenen iletide **Evet**' e tıklayın.

    Birkaç dakika sonra, durum teslim edildi durumundan süre dolmayacak şekilde değişir.

1. **Kaynak rolleri**' ne tıklayın.

1. **Pazarlama Kaynakları**için üç nokta ( **...** ) simgesini ve ardından **kaynak rolünü kaldır**' ı tıklatın. Görüntülenen iletide **Evet**' e tıklayın.

1. Erişim paketleri listesini açın.

1. **Pazarlama kampanyası**için üç noktaya ( **...** ) ve ardından **Sil**' e tıklayın. Görüntülenen iletide **Evet**' e tıklayın.

1. Azure Active Directory, **Requestor1** ve **admin1**gibi oluşturduğunuz tüm kullanıcıları silin.

1. **Pazarlama Kaynakları** grubunu silin.

## <a name="next-steps"></a>Sonraki adımlar

Yetkilendirme Yönetimi 'ndeki yaygın senaryo adımları hakkında bilgi edinmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Genel senaryolar](entitlement-management-scenarios.md)
