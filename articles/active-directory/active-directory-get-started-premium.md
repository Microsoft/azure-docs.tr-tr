---
title: "Azure Active Directory Premium ile çalışmaya başlama"
description: "Azure Active Directory Premium sürümü için, Toplu Lisanslama web sitesi üzerinden kayıt işleminin nasıl yapıldığını açıklayan bir konu başlığı."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7
ms.lasthandoff: 12/07/2016


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Azure Active Directory Premium ile çalışmaya başlama
Active Directory Premium'a kaydolmanız için birkaç seçenek sunulmaktadır: 

**Azure veya Office 365** Azure veya Office 365 abonesi olarak, Active Directory Premium'u çevrimiçi satın alabilirsiniz. Ayrıntılı adımlar için bkz. [Azure Active Directory Premium'u satın alma - Mevcut Müşteriler](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) veya [Azure Active Directory Premium'u satın alma - Yeni Müşteriler](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

**Enterprise Mobility + Security** - Enterprise Mobility + Security, (eski adıyla Enterprise Mobility Suite), kuruluşların şu hizmetleri tek lisans planı kapsamında birlikte kullanmasını sağlayan uygun maliyetli bir yöntemdir: Active Directory Premium, Azure Rights Management, Microsoft Intune. Daha fazla bilgi için [Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) web sitesine göz atın. Ücretsiz 30 günlük deneme sürümünü edinmek için [buraya](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0) tıklayın.

**Microsoft Toplu Lisanslama** - Azure Active Directory Premium bir [Microsoft Enterprise Sözleşmesi](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 veya daha fazla lisans) veya [Açık Toplu Lisans](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5 - 250 lisans) programı ile sunulmaktadır.

Bu konu başlığında, Toplu Lisanslama programı aracılığıyla satın aldığınız Azure Active Directory Premium ile çalışmaya nasıl başlayacağınız gösterilmektedir. Azure Active Directory'nin farklı sürümleri hakkında henüz bilgi sahibi değilseniz bkz. [Azure Active Directory sürümleri](active-directory-editions.md).  

> [!NOTE]
> Azure Active Directory Premium ve Basic sürümleri, Azure Active Directory'nin dünya çapındaki örneğini kullanan Çin'deki müşterilerin kullanımına sunulmuştur. Azure Active Directory Premium ve Basic sürümleri, şu anda Çin'de 21Vianet tarafından işletilen Microsoft Azure hizmeti kapsamında desteklenmemektedir. Daha fazla bilgi için [Azure Active Directory Forumu](https://feedback.azure.com/forums/169401-azure-active-directory/)'nda bizimle iletişime geçin.
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>1. Adım: Active Directory Premium'a kaydolma
Kaydolmak için bkz. [Toplu Lisanslama ile satın alma](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).

## <a name="step-2-activate-your-license-plan"></a>2. Adım: Lisans planınızı etkinleştirme
Bu, Microsoft Enterprise Toplu Lisanslama programı aracılığıyla satın aldığınız ilk lisans planı mı?
Bu durumda, satın alma işleminiz tamamlandıktan sonra bir onay e-postası alırsınız.
İlk lisans planınızı etkinleştirmek için bu e-postaya ihtiyacınız vardır.

Bu dizine yönelik sonraki tüm satın alma işlemlerinde, lisanslar otomatik olarak aynı dizinde etkinleştirilir.

**Lisans planınızı etkinleştirmek için aşağıdaki adımlardan birini gerçekleştirin:**

1. Etkinleştirmeyi başlatmak için, **Oturum Aç** veya **Kaydol** seçeneğine tıklayın.
   
    ![Oturum aç][1]

    - Mevcut bir kiracınız varsa mevcut yönetici hesabınızla oturum açmak için **Oturum Aç** seçeneğine tıklayın. Lisansların etkinleştirilmesi için kullanılması gereken dizinden genel yönetici kimlik bilgileriyle oturum açmanız gerekir.

    - Lisans planınızla kullanmak üzere yeni bir Azure Active Directory kiracısı oluşturmak istiyorsanız **Hesap Profili Oluştur** iletişim kutusunu açmak için **Kaydol** seçeneğine tıklayın.

        ![Hesap profili oluşturma][2]

İşleminiz tamamlandığında, kiracınız için lisans planının etkinleştirilmesinin onayı olarak aşağıdaki iletişim kutusu görünür.

![Onay][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>3. Adım: Azure Active Directory erişiminizi etkinleştirme
Microsoft Azure'ı daha önce kullandıysanız [4. Adım](#step-4-assign-license-to-user-accounts)'a geçebilirsiniz. 

Lisanslar, dizininize sağlandığında size bir **Hoş geldiniz e-postası** gönderilir. E-posta, Azure Active Directory Premium veya Enterprise Mobility Suite lisanslarınızı ve özelliklerinizi yönetmeye başlayabileceğinizi onaylar. 

Hoş Geldiniz e-postasını almadan önce Azure Active Directory erişiminizi etkinleştirme girişiminde bulunursanız aşağıdaki hata iletisini alırsınız. 

![Erişim kullanılamıyor][9]

Lütfen e-postayı aldıktan birkaç dakika sonra tekrar deneyin.

Aboneliğiniz kapsamındaki yeni yöneticiler de bu bağlantı üzerinden klasik Azure portalına erişimlerini etkinleştirebilir.

**Azure Active Directory erişiminizi etkinleştirmek için aşağıdaki adımları uygulayın:**

1. **Hoş geldiniz e-postanızda**, **Oturum Aç**'a tıklayın. 
   
    ![Hoş geldiniz e-postası][4]
2. Başarıyla oturum açtıktan sonra, mobil doğrulama biçimindeki ikinci kimlik doğrulaması öğesini tamamlamanız gerekir:
   
    ![Mobil doğrulama][5]

Etkinleştirme birkaç dakika sürebilir. Erişiminiz etkinleştirildikten sonra, kahverengi çubuk kaybolur ve **Portal**'a tıklayabilirsiniz.

![Lütfen bekleyin, ayarlar yapılıyor][6]

Bu durumda, Azure erişiminiz Azure Active Directory ile sınırlıdır.

![Azure özellikleri][7]

Önceki kullanımınız nedeniyle Azure erişiminiz zaten mevcut olabilir; buna ek olarak, ek Azure aboneliklerini etkinleştirerek Azure Active Directory erişiminizi tam Azure erişimine yükseltebilirsiniz. Bu gibi durumlarda klasik Azure portalı daha fazla özellik içerir.

![Azure özellikleri][8]

## <a name="step-4-assign-license-to-user-accounts"></a>4. Adım: Kullanıcı hesaplarına lisans atama
Satın aldığınız planı kullanmaya başlamadan önce, Premium ile sağlanan zengin özellikleri kullanabilmeleri için kuruluşunuzdaki kullanıcı hesaplarına lisansları kendiniz atamanız gerekir. Azure Active Directory Premium özelliklerini kullanabilmeleri için kullanıcılara lisans atamak üzere aşağıdaki adımları uygulayın.

**Kullanıcılara lisans atamak için aşağıdaki adımları uygulayın:**

1. Klasik Azure portalında, özelleştirmek istediğiniz dizinin genel yöneticisi olarak oturum açın.
2. **Active Directory** seçeneğine tıklayın ve ardından, lisansları atamak istediğiniz dizini seçin.
3. **Lisanslar** sekmesini seçin, **Active Directory Premium** veya **Enterprise Mobility Suite** seçeneğini belirleyin ve ardından **Ata**'ya tıklayın.
   
    ![Lisans planları][10]
4. İletişim kutusunda, lisans atamak istediğiniz kullanıcıları seçin ve ardından değişiklikleri kaydetmek için onay işareti simgesine tıklayın.
   
    ![Lisans atama][11]

### <a name="license-restrictions"></a>Lisans kısıtlamaları
Bazı lisans planları diğer lisans planlarının alt kümeleri veya üst kümeleridir. Genellikle, bir kullanıcı zaten kendisine atanmış olan bir lisans planına atanamaz. Üst küme olan bir lisans planını atamak istiyorsanız öncelikle alt küme lisans planını kaldırmanız gerekir.

### <a name="license-requirements"></a>Lisans gereksinimleri
Bir kullanıcıya lisans atadığınızda, hesabının özelliklerinde birincil kullanım konumu belirtebilirsiniz. Kullanım konumu belirtilmezse kullanıcıya otomatik olarak kiracının konumu atanır.

![Kullanıcı konumu][12]

Bir Microsoft bulut hizmeti için hizmetlerin ve özelliklerin kullanılabilirliği ülkeye veya bölgeye göre değişiklik gösterir. Internet protokolü üzerinden ses (VoIP) gibi bir hizmet, bir ülkede veya bölgede kullanılabilirken başka bir ülkede veya bölgede kullanılamıyor olabilir. Bir hizmet kapsamındaki özellikler, belirli ülke veya bölgelerde yasal nedenlerden ötürü kısıtlanmış olabilir. Bir hizmetin veya özelliğin kısıtlamalı veya kısıtlamasız olarak kullanılabilirlik durumunu öğrenmek için hizmetin lisans kısıtlamaları sitesinde ülkenizi veya bölgenizi arayın.

## <a name="whats-next"></a>Sırada ne var?
* [Oturum Açma ve Erişim Paneli sayfalarınıza şirket markası ekleme](active-directory-add-company-branding.md)
* [Erişim ve kullanım raporlarınızı görüntüleme](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

