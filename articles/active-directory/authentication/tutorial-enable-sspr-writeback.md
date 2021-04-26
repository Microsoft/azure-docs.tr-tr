---
title: Azure Active Directory parola geri yazmayı etkinleştir
description: Bu öğreticide, değişiklikleri yeniden şirket içi Active Directory Domain Services ortamıyla eşitlemeye yönelik Azure AD Connect kullanarak Azure AD self servis parola sıfırlama geri yazma 'yı etkinleştirmeyi öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 444ca19732921b336cae32a9b1eb5755a08e4bd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97028062"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Öğretici: şirket içi bir ortama Azure Active Directory self servis parola sıfırlama geri yazma özelliğini etkinleştirme

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR) ile, kullanıcılar parolalarını güncelleştirebilir veya bir Web tarayıcısı kullanarak hesaplarının kilidini açabilir. Azure AD 'nin şirket içi Active Directory Domain Services (AD DS) ortamına bağlandığı bir karma ortamda, bu senaryo parolaların iki dizin arasında farklı olmasına neden olabilir.

Parola geri yazma özelliği, Azure AD 'deki parola değişikliklerini şirket içi AD DS ortamınıza geri aktarmak için kullanılabilir. Azure AD Connect, bu parola değişikliklerini Azure AD 'den mevcut bir şirket içi dizine geri göndermek için güvenli bir mekanizma sağlar.

> [!IMPORTANT]
> Bu öğreticide, self servis parola sıfırlamayı şirket içi bir ortama nasıl etkinleştireceğinizi gösteren bir yönetici gösterilmektedir. Self servis parola sıfırlama için zaten kayıtlı bir son kullanıcı varsa ve hesabınıza geri dönmek istiyorsanız adresine gidin https://aka.ms/sspr .
>
> BT ekibiniz kendi parolanızı sıfırlama özelliğini etkinleştirmediyseniz, ek yardım için yardım masasına ulaşın.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Parola geri yazma için gerekli izinleri yapılandırın
> * Azure AD Connect parola geri yazma seçeneğini etkinleştirin
> * Azure AD SSPR 'de parola geri yazmayı etkinleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* En az bir Azure AD Premium P1 veya deneme lisansı etkin çalışan bir Azure AD kiracısı.
    * Gerekirse, [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Daha fazla bilgi için bkz. [Azure AD SSPR Için lisans gereksinimleri](concept-sspr-licensing.md).
* *Genel yönetici* ayrıcalıklarına sahip bir hesap.
* Self servis parola sıfırlama için yapılandırılmış Azure AD.
    * Gerekirse, [Azure AD SSPR 'yi etkinleştirmek için önceki öğreticiyi](tutorial-enable-sspr.md)izleyin.
* Geçerli bir Azure AD Connect sürümü ile yapılandırılmış mevcut bir şirket içi AD DS ortamı.
    * Gerekirse, [Express](../hybrid/how-to-connect-install-express.md) veya [özel](../hybrid/how-to-connect-install-custom.md) ayarları kullanarak Azure AD Connect yapılandırın.
    * Parola geri yazma özelliğini kullanmak için, etki alanı denetleyicileriniz Windows Server 2012 veya sonraki bir sürümü olmalıdır.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Azure AD Connect için hesap izinlerini yapılandırma

Azure AD Connect, şirket içi AD DS ortamı ve Azure AD arasında kullanıcıları, grupları ve kimlik bilgilerini eşitlemenize olanak tanır. Genellikle Azure AD Connect, şirket içi AD DS etki alanına katılmış bir Windows Server 2012 veya üzeri bilgisayara yüklersiniz.

SSPR geri yazma ile doğru şekilde çalışmak için Azure AD Connect ' de belirtilen hesap uygun izinlere ve seçeneklere sahip olmalıdır. Şu anda kullanılmakta olan hesabın olmadığından emin değilseniz Azure AD Connect açın ve **geçerli yapılandırmayı görüntüle** seçeneğini belirleyin. İzin eklemeniz gereken hesap, **eşitlenmiş dizinler** altında listelenmiştir. Hesapta aşağıdaki izinler ve seçenekler ayarlanmalıdır:

* **Parola sıfırlama**
* Üzerine **yazma izinleri**`lockoutTime`
* Üzerine **yazma izinleri**`pwdLastSet`
* Zaten ayarlanmamışsa, o ormandaki *her etki alanının* kök nesnesinde "süre sonu olmayan parola" için **genişletilmiş haklar** .

Bu izinleri atamadıysanız, geri yazma doğru şekilde yapılandırılmış görünebilir, ancak kullanıcılar şirket içi parolalarını buluttan yönettiklerinde hatalarla karşılaşabilirler. İzinler **Bu nesneye ve** "süre sonu parolasının kaldırılması" için tüm alt nesnelere uygulanmalıdır.  

> [!TIP]
>
> Bazı Kullanıcı hesaplarının parolaları şirket içi dizine geri yazılmadıysa, şirket içi AD DS ortamındaki hesap için devralmanın devre dışı bırakılmadığından emin olun. Özelliğin düzgün çalışması için, alt nesnelere parola yazma izinlerinin uygulanması gerekir.

Parola geri yazma işleminin gerçekleşmesi için uygun izinleri ayarlamak için aşağıdaki adımları izleyin:

1. Şirket içi AD DS ortamınızda, uygun *etki alanı yöneticisi* izinlerine sahip bir hesapla **Active Directory Kullanıcıları ve bilgisayarları** açın.
1. **Görünüm** menüsünde, **Gelişmiş Özellikler** ' in açık olduğundan emin olun.
1. Sol bölmede, etki alanının kökünü temsil eden nesneyi sağ seçin ve **Özellikler**  >  **güvenlik**  >  **Gelişmiş**' i seçin.
1. **İzinler** sekmesinde **Ekle**' yi seçin.
1. **Sorumlu** için, izinlerin uygulanması gereken hesabı seçin (Azure AD Connect tarafından kullanılan hesap).
1. **Uygulanacak** öğe açılır listesinde, alt **Kullanıcı nesneleri**' ni seçin.
1. *İzinler* altında, aşağıdaki seçeneğe ait kutuyu seçin:
    * **Parola sıfırlama**
1. *Özellikler* altında, aşağıdaki seçenekler için kutuları seçin. Zaten varsayılan olarak ayarlanmış olabilecek bu seçenekleri bulmak için listeyi kaydırın:
    * **Yazma lockoutTime**
    * **PwdLastSet yazma**

    [![Azure AD Connect ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png) tarafından kullanılan hesap Için etkin kullanıcılar ve bilgisayarlarda uygun izinleri ayarlayın](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Hazırsanız, değişiklikleri uygulamak ve açık iletişim kutularından çıkmak için **Uygula/Tamam** ' ı seçin.

İzinleri güncelleştirdiğinizde, bu izinlerin dizininizdeki tüm nesnelere çoğaltılması bir saat veya daha fazla sürebilir.

Şirket içi AD DS ortamındaki parola ilkeleri, parola sıfırlamaları 'nın doğru işlenmesini engelleyebilir. Parola geri yazma özelliğinin en verimli şekilde çalışması için, *Minimum parola yaşı* için Grup ilkesi 0 olarak ayarlanmalıdır. Bu ayar, **Windows ayarları > güvenlik ayarları > hesap ilkeleri > bilgisayar yapılandırma > ilkeleri** altında bulunabilir `gpedit.msc` .

Grup ilkesini güncelleştirirseniz, güncelleştirilmiş ilkenin çoğaltılmasını bekleyin veya `gpupdate /force` komutunu kullanın.

> [!Note]
> Parolaların hemen değiştirilebilmesi için parola geri yazma özelliğinin 0 olarak ayarlanması gerekir. Ancak, kullanıcılar şirket içi ilkelere uysa ve *Minimum parola yaşı* sıfırdan büyük bir değere ayarlanmışsa, şirket içi ilkeler değerlendirildikten sonra parola geri yazma işlemi yine de geçerlidir.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Azure AD Connect parola geri yazmayı etkinleştirme

Azure AD Connect yapılandırma seçeneklerinden biri parola geri yazma içindir. Bu seçenek etkinleştirildiğinde, parola değiştirme olayları Azure AD Connect güncelleştirilmiş kimlik bilgilerini şirket içi AD DS ortamına geri eşitlemesine neden olur.

SSPR geri yazmayı etkinleştirmek için öncelikle Azure AD Connect içindeki geri yazma seçeneğini etkinleştirin. Azure AD Connect sunucusundan aşağıdaki adımları uygulayın:

1. Azure AD Connect sunucunuzda oturum açın ve **Azure AD Connect** Yapılandırma Sihirbazı 'nı başlatın.
1. **Hoş Geldiniz** sayfasında, **Yapılandır**’ı seçin.
1. **Ek görevler** sayfasında **Eşitleme seçeneklerini özelleştirme**'yi ve ardından **İleri**'yi seçin.
1. **Azure AD 'ye Bağlan** sayfasında, Azure kiracınız için genel yönetici kimlik bilgilerini girin ve ardından **İleri**' yi seçin.
1. **Dizinleri bağla** ve **Etki Alanı/OU** filtreleme sayfalarında **İleri**'yi seçin.
1. **İsteğe bağlı özellikler** sayfasında **Parola geri yazma** özelliğinin yanındaki kutuyu işaretleyin ve **İleri**'yi seçin.

    ![Parola geri yazma için Azure AD Connect yapılandırma](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. **Yapılandırma için hazır** sayfasında **Yapılandır**'ı seçin ve işlemin tamamlanmasını bekleyin.
1. Yapılandırma tamamlandığında **Çıkış**'ı seçin.

## <a name="enable-password-writeback-for-sspr"></a>SSPR için parola geri yazmayı etkinleştirme

Parola geri yazma özelliği etkinken Azure AD Connect, artık Azure AD SSPR 'yi geri yazma için yapılandırın. SSPR 'yi parola geri yazma özelliğini kullanacak şekilde etkinleştirdiğinizde, parolasını değiştiren veya sıfırlayan kullanıcılar, güncelleştirilmiş parolanın şirket içi AD DS ortamına geri eşitlendiğini de sağlar.

SSPR 'de parola geri yazma özelliğini etkinleştirmek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) bir genel yönetici hesabı kullanarak oturum açın.
1. **Azure Active Directory** arayıp seçin, **parola sıfırlama**' yı seçin ve ardından Şirket **içi tümleştirme**' i seçin.
1. **Şirket içi dizininize yeniden parola yazma** seçeneğini *Evet* olarak ayarlayın.
1. **Kullanıcıların, parolasını sıfırlamadan hesapların kilidini açmalarına Izin ver** seçeneğini *Evet* olarak ayarlayın.

    ![Parola geri yazma için Azure AD self servis parola sıfırlamayı etkinleştirme](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Hazırlanıyor, **Kaydet**' i seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticinin bir parçası olarak yapılandırdığınız SSPR geri yazma işlevini artık kullanmak istemiyorsanız, aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** arayıp seçin, **parola sıfırlama**' yı seçin ve ardından Şirket **içi tümleştirme**' i seçin.
1. Şirket **içi dizininize yeniden parola yazma** seçeneğini *belirleyin.*
1. **Kullanıcıların, parolasını sıfırlamadan hesapların kilidini açmalarına Izin ver** seçeneğini *belirleyin.*

Artık herhangi bir parola işlevini kullanmak istemiyorsanız, Azure AD Connect sunucunuzdaki aşağıdaki adımları uygulayın:

1. Azure AD Connect sunucunuzda oturum açın ve **Azure AD Connect** Yapılandırma Sihirbazı 'nı başlatın.
1. **Hoş Geldiniz** sayfasında, **Yapılandır**’ı seçin.
1. **Ek görevler** sayfasında **Eşitleme seçeneklerini özelleştirme**'yi ve ardından **İleri**'yi seçin.
1. **Azure AD 'ye Bağlan** sayfasında, Azure kiracınız için genel yönetici kimlik bilgilerini girin ve ardından **İleri**' yi seçin.
1. **Dizinleri bağla** ve **Etki Alanı/OU** filtreleme sayfalarında **İleri**'yi seçin.
1. **Isteğe bağlı özellikler** sayfasında, **parola geri yazma** ' nın yanındaki kutunun işaretini kaldırın ve **İleri**' yi seçin.
1. **Yapılandırma için hazır** sayfasında **Yapılandır**'ı seçin ve işlemin tamamlanmasını bekleyin.
1. Yapılandırma tamamlandığında **Çıkış**'ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure AD SSPR geri yazma özelliğini şirket içi AD DS ortamına etkinleştirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Parola geri yazma için gerekli izinleri yapılandırın
> * Azure AD Connect parola geri yazma seçeneğini etkinleştirin
> * Azure AD SSPR 'de parola geri yazmayı etkinleştirme

> [!div class="nextstepaction"]
> [Oturum açma sırasında risk değerlendirmesi yapma](tutorial-risk-based-sspr-mfa.md)
