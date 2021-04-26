---
title: NDES sunucusu üzerinde AD Uygulama Ara Sunucusu tümleştirme
titleSuffix: Azure Active Directory
description: NDES sunucunuzu korumak için bir Azure Active Directory Uygulama Ara Sunucusu Dağıtma Kılavuzu.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 69193296069765ae6e94ffe97913c136a0d033d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257905"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Bir ağ cihazı kayıt hizmeti (NDES) sunucusu üzerinde Azure AD Uygulama Ara Sunucusu ile tümleştirme

Azure Active Directory (AD) uygulama proxy 'Si, ağınızdaki uygulamaları yayımlamanıza olanak sağlar. Bu uygulamalar SharePoint siteleri, Microsoft Outlook Web App ve diğer Web uygulamaları gibi uygulamalardır. Ayrıca, Azure aracılığıyla ağınızın dışındaki kullanıcılara güvenli erişim sağlar.

Azure AD Uygulama Ara Sunucusu yeni başladıysanız ve daha fazla bilgi edinmek istiyorsanız bkz. [azure ad uygulama ara sunucusu aracılığıyla şirket içi uygulamalara uzaktan erişim](application-proxy.md).

Azure AD Uygulama Ara Sunucusu, Azure üzerinde oluşturulmuştur. Bu, dağıtılmış hizmet reddi (DDOS) saldırılarına ve mükemmel kullanılabilirliğe karşı daha iyi koruma sağlayan büyük miktarda ağ bant genişliği ve sunucu altyapısı sağlar. Ayrıca, şirket içi ağınıza dışarıdan güvenlik duvarı bağlantı noktaları açmanıza gerek yoktur ve DMZ sunucusu gerekli değildir. Tüm trafiğin kaynağı gelen. Giden bağlantı noktalarının tam listesi için bkz. [öğretici: Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> Azure AD Uygulama Ara Sunucusu, yalnızca Azure Active Directory Premium veya Basic sürümlerini kullanıyorsanız kullanılabilir bir özelliktir. Daha fazla bilgi için bkz. [Azure Active Directory fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/). 
> Enterprise Mobility Suite (EMS) lisanslarınız varsa, bu çözümü kullanmaya uygun olursunuz.
> Azure AD Uygulama Ara Sunucusu Bağlayıcısı yalnızca Windows Server 2012 R2 veya sonraki sürümlere yüklenir. Bu ayrıca NDES sunucusunun bir gereksinimidir.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Bağlayıcıyı NDES sunucusuna yükleyip kaydetme

1. Uygulama proxy 'Si kullanan dizinin uygulama Yöneticisi olarak [Azure Portal](https://portal.azure.com/) oturum açın. Örneğin, kiracı etki alanı contoso.com ise yönetici admin@contoso.com ya da bu etki alanında başka bir yönetici diğer adı olmalıdır.
1. Sağ üst köşedeki Kullanıcı adınızı seçin. Uygulama proxy 'Si kullanan bir dizine oturum açtığınızdan emin olun. Dizinleri değiştirmeniz gerekiyorsa, **dizini** Değiştir ' i seçin ve uygulama proxy 'si kullanan bir dizin seçin.
1. Sol gezinti panelinde **Azure Active Directory**' yi seçin.
1. **Yönet** altında **uygulama proxy 'si**' ni seçin.
1. **Bağlayıcı hizmetini indir**' i seçin.

    ![Hizmet koşullarını görmek için bağlayıcı hizmetini indirin](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Hizmet koşullarını okuyun. Hazırsanız **& koşulları kabul et**' i seçin.
1. Azure AD Uygulama Ara Sunucusu Bağlayıcısı kurulum dosyasını NDES sunucunuza kopyalayın. 
   > Bağlayıcıyı, şirket ağınız içindeki herhangi bir sunucuya NDES erişimi ile yükleyebilirsiniz. Bunu NDES sunucusunun kendisine yüklemenize gerek yoktur.
1. *AADApplicationProxyConnectorInstaller.exe* gibi kurulum dosyasını çalıştırın. Yazılım lisans koşulları 'nı kabul edin.
1. Yüklemesi sırasında, bağlayıcıyı Azure AD dizininize uygulama proxy 'Si ile kaydetmeniz istenir.
   * Azure AD dizininizde küresel veya uygulama yöneticisinin kimlik bilgilerini sağlayın. Azure AD küresel veya uygulama Yöneticisi kimlik bilgileri, portalda Azure kimlik bilgilerinizle farklı olabilir.

        > [!NOTE]
        > Bağlayıcıyı kaydetmek için kullanılan genel veya uygulama yöneticisi hesabı, uygulama ara sunucusu hizmetini etkinleştirdiğiniz dizine ait olmalıdır.
        >
        > Örneğin, Azure AD etki alanı *contoso.com* ise, genel/uygulama Yöneticisi `admin@contoso.com` o etki alanında veya geçerli bir diğer ad olmalıdır.

   * Bağlayıcıyı yüklediğiniz sunucu için Internet Explorer Artırılmış Güvenlik Yapılandırması açıksa, kayıt ekranı engellenebilir. Erişime izin vermek için, hata iletisindeki yönergeleri uygulayın veya yüklemenin işlemi sırasında Internet Explorer gelişmiş güvenliği ' ni kapatın.
   * Bağlayıcı kaydı başarısız olursa bkz. [uygulama proxy 'Si sorunlarını giderme](application-proxy-troubleshoot.md).
1. Kurulumun sonunda, giden ara sunucuya sahip ortamlar için bir Note gösterilir. Azure AD Uygulama Ara Sunucusu bağlayıcısını giden ara sunucu üzerinden çalışacak şekilde yapılandırmak için, belirtilen betiği çalıştırın, örneğin `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Azure portal uygulama proxy 'si sayfasında, yeni bağlayıcı aşağıdaki örnekte gösterildiği gibi *etkin* durumuyla listelenir:

    ![Azure portal etkin olarak gösterilen yeni Azure AD Uygulama Ara Sunucusu Bağlayıcısı](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Azure AD Uygulama Ara Sunucusu kimlik doğrulaması yapan uygulamalar için yüksek kullanılabilirlik sağlamak üzere, bağlayıcıları birden çok VM 'ye yükleyebilirsiniz. Bağlayıcıyı Azure AD DS yönetilen etki alanına katılmış diğer sunuculara yüklemek için önceki bölümde listelenen adımları yineleyin.

1. Yükleme başarılı olduktan sonra Azure portal geri dönün.

1. **Kurumsal uygulamalar**' ı seçin.

   ![doğru paydaşları ilgi çekici olduğunuzdan emin olun](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. **+ Yeni uygulama**' yı seçin ve ardından Şirket **içi uygulama**' yı seçin. 

1. **Kendi şirket içi uygulamanızı ekleyin**, aşağıdaki alanları yapılandırın:

   * **Ad**: uygulama için bir ad girin.
   * **Iç URL**: BAĞLAYıCıYı yüklediğiniz NDES SUNUCUNUZUN iç URL 'SINI/FQDN 'sini girin.
   * **Ön kimlik doğrulaması**: **geçiş** öğesini seçin. Herhangi bir ön kimlik doğrulama biçimi kullanılması mümkün değildir. Sertifika Istekleri (SCEP) için kullanılan protokol bu seçeneği sağlamaz.
   * Belirtilen **dış URL 'yi** panonuza kopyalayın.

1. Uygulamanızı kaydetmek için **+ Ekle** ' yi seçin.

1. Adım 15 ' te kopyaladığınız bağlantıyı bir tarayıcıya yapıştırarak Azure AD uygulama proxy 'si aracılığıyla NDES sunucunuza erişip erişemeyeceğinizi test edin. Varsayılan bir IIS karşılama sayfası görmeniz gerekir.

1. Son bir test olarak, önceki adımda yapıştırdığınız mevcut URL 'ye *mscep.dll* yolunu ekleyin:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. **Http hatası 403 – Yasak** yanıt görmeniz gerekir.

1. Belirtilen NDES URL 'sini (Microsoft Intune aracılığıyla) cihazlara değiştirin. Bu değişiklik, Microsoft uç noktası Configuration Manager veya Microsoft Uç Nokta Yöneticisi Yönetim Merkezi 'nde olabilir.

   * Configuration Manager için, sertifika kayıt noktasına gidin ve URL 'YI ayarlayın. Bu URL, cihazların ne kadar arama yaptığını ve zorluğun sunulmasını sağlar.
   * Tek başına Intune için yeni bir SCEP ilkesi düzenleyin ya da oluşturun ve yeni URL 'YI ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

NDES ile tümleştirilmiş Azure AD Uygulama Ara Sunucusu, kullanıcıların erişebileceği uygulamalar yayımlayın. Daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](./application-proxy-add-on-premises-application.md).
