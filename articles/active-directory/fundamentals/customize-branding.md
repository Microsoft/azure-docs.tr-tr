---
title: Kuruluşunuzun oturum açma sayfasına - Azure Active Directory markalama Ekle | Microsoft Docs
description: Azure Active Directory oturum açma sayfasına kuruluşunuzun markası ekleme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7caf6eff9f5bd497ab6c3a500f3940549b198576
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959048"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Oturum açma, kuruluşunuzun Azure Active Directory sayfasına markalama Ekle
Kuruluşunuzun logosu ve özel renk düzenleriyle bir tutarlı görünüm ve hisse oturum açma, Azure Active Directory (Azure AD) sayfalarında sağlamak için kullanın. Oturum açma sayfaları kuruluşunuzun web tabanlı uygulamalara, Azure AD kimlik sağlayıcınız olarak kullanan Office 365 gibi kullanıcılar oturum açtığında görünür.

>[!Note]
>Özel marka öğelerini eklemek için Azure Active Directory Premium 1, 2 Premium veya Basic sürümleri kullanın ya da bir Office 365 lisansına sahip olması gerekir. Lisanslama ve sürümleri hakkında daha fazla bilgi için bkz: [Azure AD Premium'a kaydolun](active-directory-get-started-premium.md).<br><br>Azure AD Premium ve Temel sürümleri, Azure Active Directory'nin dünya çapındaki örneğini kullanan Çin'deki müşterilerin kullanımına sunulmuştur. Azure AD Premium ve Temel sürümleri, şu anda Çin’de 21Vianet tarafından işletilen Azure hizmeti kapsamında desteklenmemektedir. Daha fazla bilgi için [Azure Active Directory Forumu](https://feedback.azure.com/forums/169401-azure-active-directory/)’nu kullanarak bizimle görüşün.

## <a name="customize-your-azure-ad-sign-in-page"></a>Azure AD oturum açma sayfanızı özelleştirme
Kullanıcılar, kuruluşunuzun kiracıya özel uygulamalar için aşağıdaki gibi oturum açtığında görüntülenir, Azure AD oturum açma sayfaları özelleştirebilirsiniz [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), veya gibibiretkialanıdeğişkeninigeçirme[ *https://passwordreset.microsoftonline.com/?whr=contoso.com* ](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Kullanıcılarınızın www\.Office.com gibi sitelere gitmesi durumunda özel markası hemen görünmez. Bunun yerine, kullanıcının özelleştirilmiş markalama görünmeden önce oturum açmanız gerekir. Kullanıcı oturum açtıktan sonra, marka 15 dakika veya daha uzun sürebilir. 

> [!NOTE]
> Tüm marka öğeleri isteğe bağlıdır. Örneğin, hiçbir arka plan görüntüsü ile bir başlık logosu belirtirseniz oturum açma sayfası varsayılan arka plan görüntüsü (örneğin, Office 365) hedef siteden Logonuzla gösterir.<br><br>Ayrıca, oturum açma sayfasında bulunan marka kişisel Microsoft hesapları için aktarılmaz. Kullanıcılarınıza veya şirket konuklarınız kişisel bir Microsoft hesabı kullanarak oturum açın, oturum açma sayfasında, kuruluşunuzun markasını yansıtmaz.

### <a name="to-customize-your-branding"></a>Markanızın özelleştirmek için
1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

2. Seçin **Azure Active Directory**ve ardından **şirket markası ekleme**ve ardından **yapılandırma**.

    ![Contoso - şirket markalama sayfasında, yapılandırma seçeneği vurgulanmış](media/customize-branding/company-branding-configure-button.png)

3. Üzerinde **yapılandırma şirket markası** sayfasında, aşağıdaki bilgilerin bir bölümünü veya tamamını sağlayın.

    >[!Important]
    >Bu sayfada eklediğiniz tüm özel görüntüleri görüntü boyutu (piksel) sahip ve büyük olasılıkla dosya boyutu (KB) cinsinden kısıtlamaları. Bu kısıtlamalar nedeniyle, olasılıkla Fotoğraf Düzenleyici doğru boyutta görüntüleri oluşturmak için kullanmanız gerekecektir.

    - **Genel ayarlar**

        ![Şirket ile genel ayarlar Tamamlandı sayfasında markasını yapılandırın](media/customize-branding/configure-company-branding-general-settings.png)

        - **Dili.** Dil, varsayılan olarak otomatik olarak ayarlanır ve değiştirilemez.
        
        - **Oturum açma sayfası arka plan resmi.** Oturum açma sayfalarını arka planı olarak görüntülenecek bir .png veya .jpg görüntüsü dosyası seçin. 
        
            Görüntü boyutu 1920 x 1080 piksel değerinden büyük olamaz ve az 300 KB dosya boyutuna sahip olmalıdır.

        - **Başlık logosu.** Logonuz oturum açma sayfasında kullanıcının kullanıcı adı girdiğinde ve üzerinde görünmesi için bir .png veya .jpg sürümünü seçin **uygulamalarım** portal sayfası.
            
            Görüntü 60 pikselden veya 280 pikselden daha büyük olamaz. Arka plan logo arka plan eşleşmeyebilir beri saydam bir görüntü kullanmanızı öneririz. Ayrıca görüntü çevresindeki dolgunun eklenmiyor öneririz veya küçük Ara logonuz hale getirebilirsiniz.

        - **Kullanıcı adı ipucu.** Kullanıcı adlarını unuturlarsa kullanıcılara görüntülenen İpucu metni yazın. Bu metin, Unicode bağlantılar veya kod olmalıdır ve 64 karakterden uzun olamaz. Konuklar uygulamanızda oturum açarsa, bu ipucu eklenmiyor öneririz.

        - **Oturum açma sayfası metni.** Oturum açma sayfasının en altında görüntülenen metin yazın. Bu metin, Yardım masanıza ya da yasal bildirim telefon numarası gibi ek bilgiler iletmek için kullanabilirsiniz. Bu metin, Unicode ve 256 karakterden uzun değil. Bağlantılar veya HTML etiketleri içermeden öneririz.

    - **Gelişmiş ayarlar**
            
        ![Şirket ile Gelişmiş ayarlar Tamamlandı sayfasında markasını yapılandırın](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Oturum açma sayfası arka plan rengi.** Onaltılık renk belirtin (örneğin #FFFFFF beyaz) arka plan görüntünüzü düşük bant genişlikli bağlantı durumlarda yerine görüntülenir. Başlık logosu, ya da kuruluş renginiz birincil rengini kullanmanızı öneririz.

        - **Kare logo görüntüsü.** (Tercih edilir) bir .png veya .jpg görüntüsü yeni Windows 10 Enterprise cihazları için Kurulum işlemi sırasında kullanıcıların göreceği kuruluşunuzun logo seçin. Bu görüntü, yalnızca Windows kimlik doğrulaması için kullanılır ve yalnızca kullandığınız kiracının görünür [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) parola girişi veya dağıtım için diğer Windows 10 sayfalarında deneyimleri. Bazı durumlarda onay iletişim kutusunda da görünebilir.
        
            Görüntü boyutu 240 x 240 piksel değerinden büyük olamaz ve dosya boyutunun 10 KB'den az olmalıdır. Arka plan logo arka plan eşleşmeyebilir beri saydam bir görüntü kullanmanızı öneririz. Ayrıca görüntü çevresindeki dolgunun eklenmiyor öneririz veya küçük Ara logonuz hale getirebilirsiniz.
    
        - **Kare logo görüntüsü, koyu tema.** Yukarıdaki kare logo görüntüsü ile aynıdır. Bu logo görüntüsü, koyu renkli arka plan ile gibi Windows 10 Azure AD'ye katılmış ekranlar kullanıma hazır deneyimi (OOBE) sırasında kullanıldığında kare logo görüntüsü yerini alır.  Logonuz beyaz, koyu mavi ve siyah arka plan üzerinde iyi görünüyorsa, bu görüntüyü eklemenize gerek yoktur. 
        
        - **Oturumu açık bırak seçeneğini göster.** Kullanıcılarınızın açıkça oturumunuzu kadar Azure AD'ye oturumunun açık kalmasına izin vermeyi seçebilirsiniz. Seçerseniz **Hayır**, bu seçenek gizlenir ve kullanıcılar tarayıcı kapatıldı ve yeniden her zaman içinde oturum gerekir.
        
            >[!Note]
            >SharePoint Online ve Office 2010’un bazı özellikleri kullanıcıların oturumun açık kalmasını seçebilmesine bağlıdır. Bu ayarı **Hayır** olarak ayarlarsanız kullanıcılarınız oturum açmaya yönelik ek ve beklenmeyen istemler görebilir.
   

3. Marka bilgilerinizi ekleyerek bitirdikten sonra seçin **Kaydet**.

    Bu işlem ilk özel marka yapılandırmanızı oluşturursa, kiracınız için varsayılan olur. Ek yapılandırmalar varsa, varsayılan yapılandırmayı seçmek mümkün olacaktır.
    
    >[!Important]
    >Daha fazla bilgi eklemek için Kurumsal yapılandırmaları, kiracınız marka seçmeniz gerekir **yeni dil** üzerinde **Contoso - şirket markası** sayfası. Bu açılır **yapılandırma şirket markası** sayfasında, burada izleyebilirsiniz yukarıdaki gibi aynı adımları.

## <a name="update-your-custom-branding"></a>Özel bir marka bilgilerinizi güncelleştirin
Özel marka oluşturduktan sonra geri dönün ve istediğiniz değişikliği.

### <a name="to-edit-your-custom-branding"></a>Özel bir marka bilgilerinizi düzenlemek için
1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

2. Seçin **Azure Active Directory**ve ardından **şirket markası ekleme**ve ardından **yapılandırma**.

    ![Contoso - gösterilen varsayılan yapılandırma ile şirket markalama sayfası](media/customize-branding/company-branding-default-config.png)

3. Üzerinde **yapılandırma şirket markası** sayfasında, eklemek, kaldırmak veya açıklamasında temel alan bilgiler, değişiklik [Azure AD oturum açma sayfanızı özelleştirmek](#customize-your-azure-ad-sign-in-page) bu makalenin.

4. **Kaydet**’i seçin.

   Oturum açma sayfası markasında yaptığınız değişikliklerin görünmesi bir saate kadar sürebilir.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Dizininize dile özgü şirket markası ekleme
Varsayılan dilinizi özgün yapılandırmasının dili değiştiremezsiniz. Ancak, farklı bir dil yapılandırmasında ihtiyacınız varsa, yeni bir yapılandırma oluşturabilirsiniz.

### <a name="to-add-a-language-specific-branding-configuration"></a>Dile özgü marka yapılandırması eklemek için

1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

2. Seçin **Azure Active Directory**ve ardından **şirket markası ekleme**ve ardından **yeni dil**.

    ![Contoso - yeni dil seçeneği vurgulanmış ile şirket markalama sayfası](media/customize-branding/company-branding-new-language.png)

3. Üzerinde **yapılandırma şirket markası** sayfasında dilinizi (örneğin, Fransızca) seçin ve ardından bağlantısındaki açıklamalara göre çevrilmiş bilgilerinizi ekleyin [Azure AD oturum açma sayfanızı özelleştirmek](#customize-your-azure-ad-sign-in-page) Bu makalede bölümü.

4. **Kaydet**’i seçin.

    **Contoso – şirket markası** sayfasında yeni Fransızca yapılandırmanızı göstermek için güncelleştirmeler.

    ![Contoso - gösterilen varsayılan yapılandırma ile şirket markalama sayfası](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Özel sayfalara markanızı ekleme
Metni, URL'nin sonuna değiştirerek marka, özel bilgilerinizi sayfalarına ekleme `?whr=yourdomainname`. Bu değişiklik, multi-Factor Authentication (MFA) Kurulum sayfasına, Self Servis parola sıfırlama (SSPR) Kurulum sayfasına ve oturum açma sayfasında da dahil olmak üzere çeşitli sayfalar üzerinde çalışır.

**Örnekler:**

**Özgün URL:** https://aka.ms/MFASetup<br>
**Özel URL:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**Özgün URL:** https://aka.ms/SSPR<br>
**Özel URL:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 
