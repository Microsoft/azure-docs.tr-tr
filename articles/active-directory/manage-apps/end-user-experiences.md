---
title: Uygulamalar için son kullanıcı deneyimleri-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD), kuruluşunuzdaki son kullanıcılara uygulama dağıtmak için çeşitli özelleştirilebilir yollar sunar.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4a091abff02e544f97f5621b56836cf6ba6e237
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175963"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory uygulamalar için son kullanıcı deneyimleri

Azure Active Directory (Azure AD), kuruluşunuzdaki son kullanıcılara uygulama dağıtmak için çeşitli özelleştirilebilir yollar sağlar:

* Azure AD erişim paneli
* Office 365 uygulama başlatıcısı
* Birleştirilmiş uygulamalarda doğrudan oturum açma
* Birleştirilmiş, parola tabanlı veya var olan uygulamalara yönelik ayrıntılı bağlantılar

Kuruluşunuzda dağıtmayı seçtiğiniz Yöntem (ler) sizin için önemlidir.

## <a name="azure-ad-access-panel"></a>Azure AD erişim paneli

https://myapps.microsoft.com erişim paneli, Azure Active Directory bir kuruluş hesabına sahip bir son kullanıcının Azure AD yöneticisi tarafından erişim izni verilen bulut tabanlı uygulamaları görüntülemesine ve açmasına izin veren Web tabanlı bir portaldır. [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)olan son bir Kullanıcı Ise, erişim paneli aracılığıyla Self Servis Grup yönetimi yeteneklerini de kullanabilirsiniz.

![Ekran görüntüsü Azure AD erişim paneli portalını gösterir](media/what-is-single-sign-on/azure-ad-access-panel.png)

Varsayılan olarak, tüm uygulamalar tek bir sayfada birlikte listelenir. Ancak, ilgili uygulamaları gruplamak ve bunları ayrı bir sekmede sunmak, daha kolay bulunmalarını sağlamak için çalışma alanlarını kullanabilirsiniz. Örneğin, belirli iş rolleri, görevler, projeler vb. için mantıksal uygulama gruplandırmaları oluşturmak üzere çalışma alanlarını kullanabilirsiniz. Bilgi için bkz. [Kullanıcı erişimi panellerini (Önizleme) özelleştirmek Için uygulamalarımı kullanma çalışma alanları](access-panel-workspaces.md). 

Erişim paneli Azure portal ayrıdır ve kullanıcıların bir Azure aboneliğine veya Office 365 aboneliğine sahip olmasını gerektirmez.

Azure AD erişim paneli hakkında daha fazla bilgi için [erişim paneline giriş](../user-help/active-directory-saas-access-panel-introduction.md)bölümüne bakın.

## <a name="office-365-application-launcher"></a>Office 365 uygulama başlatıcısı

Office 365 ' i dağıtmış olan kuruluşlar için, Azure AD aracılığıyla kullanıcılara atanan uygulamalar da [https://portal.office.com/myapps](https://portal.office.com/myapps)adresindeki Office 365 portalında görünür. Bu, bir kuruluştaki kullanıcıların ikinci bir portal kullanmak zorunda kalmadan uygulamalarını başlatmasını kolaylaştırır ve Office 365 kullanan kuruluşlar için önerilen uygulama başlatma çözümüdür.

![Ekran görüntüsünde Office 365 Portalı gösterilmektedir](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Office 365 uygulama Başlatıcısı hakkında daha fazla bilgi için bkz. [uygulamanızın Office 365 uygulama başlatıcısı 'nda görünmesini sağlamak](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Birleştirilmiş uygulamalarda doğrudan oturum açma

SAML 2,0, WS-Federation veya OpenID Connect 'i destekleyen çoğu Federasyon uygulaması, kullanıcıların uygulamada başlatma yeteneğini de destekler ve sonra otomatik yeniden yönlendirme veya oturum açmak için bir bağlantıya tıklayarak Azure AD aracılığıyla oturum açabilirler. Bu, hizmet sağlayıcı tarafından başlatılan oturum açma olarak bilinir ve Azure AD uygulama galerisindeki çoğu Federasyon uygulaması bunu destekler (Ayrıntılar için Azure portal uygulamanın çoklu oturum açma yapılandırma sihirbazından bağlantılı belgelere bakın).

![Mobil uygulama oturum açma sayfası örneği](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Doğrudan oturum açma bağlantıları

Azure AD Ayrıca, parola tabanlı çoklu oturum açma, bağlantılı çoklu oturum açma ve herhangi bir Federasyon çoklu oturum açma biçimini destekleyen tek tek uygulamalara doğrudan çoklu oturum açma bağlantılarını destekler.

Bu bağlantılar, bir kullanıcıyı Azure AD erişim panelinden veya Office 365 ' den başlatmaya gerek kalmadan, belirli bir uygulama için Azure AD oturum açma işlemi aracılığıyla gönderen özel olarak hazırlanmış URL 'lardır. Bu **Kullanıcı erişimi URL 'leri** , kullanılabilir kurumsal uygulamaların özellikleri altında bulunabilir. Azure portal **Kurumsal uygulamaları** > **Azure Active Directory** seçin. Uygulamayı seçin ve ardından **Özellikler**' i seçin.

![Twitter özelliklerindeki Kullanıcı erişimi URL 'SI örneği](media/end-user-experiences/direct-sign-on-link.png)

Bu bağlantılar, seçilen uygulamaya bir oturum açma bağlantısı sağlamak istediğiniz yere kopyalanabilir ve yapıştırılabilir. Bu bir e-postada veya Kullanıcı uygulama erişimi için ayarladığınız özel Web tabanlı portalda olabilir. Twitter için bir Azure AD doğrudan çoklu oturum açma URL 'SI örneği aşağıda verilmiştir:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Erişim paneli için kuruluşa özgü URL 'Lerle benzer şekilde, myapps.microsoft.com etki alanından sonra dizininiz için etkin veya doğrulanmış etki alanlarından birini ekleyerek bu URL 'YI daha da özelleştirebilirsiniz. Bu, tüm kurumsal markalamayı, kullanıcının kullanıcı KIMLIĞINI öncelikle girmesi gerekmeden oturum açma sayfasına hemen yüklenmesini sağlar:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Yetkili bir Kullanıcı bu uygulamaya özgü bağlantılardan birine tıkladığında, önce kurumsal oturum açma sayfasını (henüz oturum açmamış olduğunu varsayarak) görür ve oturum açma sonrasında, erişim panelinde durdurulmadan önce, uygulamaya yeniden yönlendirildikten sonra. Kullanıcı, uygulamaya erişmek için parola tabanlı tek imza tarayıcısı uzantısı gibi önkoşulların önkoşulları eksikse, bağlantı kullanıcıdan eksik uzantıyı yüklemesini ister. Uygulamanın çoklu oturum açma yapılandırması değişirse bağlantı URL 'SI de sabit kalır.

Bu bağlantılar, erişim paneli ve Office 365 ile aynı erişim denetimi mekanizmalarını kullanır ve yalnızca Azure portal uygulamaya atanmış olan kullanıcılar veya gruplar başarıyla kimlik doğrulayabilecektir. Ancak yetkilendirilmemiş olan her Kullanıcı, erişim verilmediğini belirten bir ileti görür ve erişim paneli 'nin erişimleri olan kullanılabilir uygulamaları görüntülemek için bir bağlantı verildiğini kabul eder.

## <a name="next-steps"></a>Sonraki adımlar

Dağıtım planları için bkz. [Azure Active Directory dağıtım planları](../fundamentals/active-directory-deployment-plans.md)
