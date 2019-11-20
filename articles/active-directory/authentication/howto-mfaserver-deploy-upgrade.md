---
title: Azure MFA sunucusunu yükseltme-Azure Active Directory
description: Azure Multi-Factor Authentication sunucusu 'nun daha yeni bir sürüme yükseltilmesi için adım ve kılavuz.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19ca6d82b80a9ed77a842b638ff8e9ff346342e8
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988553"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>En son Azure Multi-Factor Authentication sunucusu 'na yükseltme

Bu makale, Azure Multi-Factor Authentication (MFA) Server v 6.0 veya üstünü yükseltme sürecinde size yol gösterir. PhoneFactor aracısının eski bir sürümünü yükseltmeniz gerekiyorsa, [phonefactor Aracısı 'Nı Azure Multi-Factor Authentication sunucusu 'Na yükseltme](howto-mfaserver-deploy-upgrade-pf.md)bölümüne bakın.

V6. x veya üzeri sürümden v7. x veya daha yeni bir sürüme yükseltiyorsanız, tüm bileşenler .NET 2,0 'den .NET 4,5 ' e değişir. Tüm bileşenler ayrıca Microsoft Visual C++ 2015 yeniden dağıtılabilir güncelleştirme 1 veya üstünü gerektirir. MFA sunucu yükleyicisi, zaten yüklenmemişse bu bileşenlerin x86 ve x64 sürümlerini de yüklerse. Kullanıcı Portalı ve mobil uygulama Web hizmeti ayrı sunucularda çalışıyorsa, bu bileşenleri yükseltmeden önce bu paketleri yüklemeniz gerekir. [Microsoft Indirme merkezi](https://www.microsoft.com/download/)' nde en son microsoft C++ Visual 2015 yeniden dağıtılabilir güncelleştirme için arama yapabilirsiniz. 

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sunmaz. Kullanıcılarından çok faktörlü kimlik doğrulaması gerektirmek isteyen yeni müşterilerin bulut tabanlı Azure Multi-Factor Authentication kullanması gerekir. MFA sunucusunu 1 Temmuz 'dan önce etkinleştiren mevcut müşteriler, en son sürümü ve gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

Adımları bir bakışta yükseltin:

* Azure MFA sunucularını yükseltme (astlar ana)
* Kullanıcı Portalı örneklerini yükseltme
* AD FS bağdaştırıcı örneklerini yükseltme

## <a name="upgrade-azure-mfa-server"></a>Azure MFA sunucusunu yükseltme

1. Azure MFA sunucusu yükleyicisinin en son sürümünü almak için [Azure Multi-Factor Authentication sunucusunu indirme](howto-mfaserver-deploy.md#download-the-mfa-server) bölümündeki yönergeleri kullanın.
2. Master MFA sunucunuzdaki C:\Program Files\Multi-Factor Authentication Server\data\phonefactorlarpfdata (varsayılan install Location) yolunda bulunan MFA sunucusu veri dosyasının yedeklemesini yapın.
3. Yüksek kullanılabilirlik için birden çok sunucu çalıştırırsanız, yükseltilen sunuculara trafik göndermeyi durdurması için MFA sunucusuna kimlik doğrulayan istemci sistemlerini değiştirin. Yük dengeleyici kullanıyorsanız, yük dengeleyiciden bir alt MFA sunucusunu kaldırın, yükseltmeyi yapın ve ardından sunucuyu gruba yeniden ekleyin.
4. Yeni yükleyiciyi her MFA sunucusunda çalıştırın. Ana sunucuları, ana sunucu tarafından çoğaltılan eski veri dosyasını okuyabildiğinden önce yükseltin.

   > [!NOTE]
   > Bir sunucuyu yükseltirken, diğer MFA sunucularıyla herhangi bir loaddengeleme veya trafik paylaşımının kaldırılması gerekir.
   >
   > Yükleyiciyi çalıştırmadan önce geçerli MFA sunucunuzu kaldırmanız gerekmez. Yükleyici yerinde yükseltme gerçekleştirir. Yükleme yolu önceki yüklemeden kayıt defterinden alınır, bu nedenle aynı konuma yüklenir (örneğin, C:\Program Files\Multi-Factor Authentication sunucusu).
  
5. Bir Microsoft Visual C++ 2015 yeniden dağıtılabilir güncelleştirme paketi yüklemek isteyip istemediğiniz sorulursa, istemi kabul edin. Paketin x86 ve x64 sürümleri yüklenir.
6. Web hizmeti SDK 'sını kullanıyorsanız, yeni Web hizmeti SDK 'sını yüklemek isteyip istemediğiniz sorulur. Yeni Web hizmeti SDK 'sını yüklediğinizde, sanal dizin adının daha önce yüklü olan sanal dizinle eşleştiğinden emin olun (örneğin, MultiFactorAuthWebServiceSdk).
7. Tüm bağımlı sunuculardaki adımları yineleyin. Astların birini yeni ana öğe olacak şekilde yükselterek eski ana sunucuyu yükseltin.

## <a name="upgrade-the-user-portal"></a>Kullanıcı portalını yükseltme

Bu bölüme geçmeden önce MFA sunucularınızın yükseltmesini doldurun.

1. Kullanıcı Portalı yükleme konumunun sanal dizininde bulunan Web. config dosyasının yedeklemesini oluşturun (örneğin, C:\inetpub\wwwroot\MultiFactorAuth). Varsayılan Temada herhangi bir değişiklik yapılmışsa, App_Themes\Default klasörünü de yedekleyin. Varsayılan bir klasörün bir kopyasını oluşturmak ve varsayılan temayı değiştirmek için yeni bir tema oluşturmak daha iyidir.
2. Kullanıcı Portalı diğer MFA sunucu bileşenleriyle aynı sunucuda çalışıyorsa, MFA sunucusu yüklemesi Kullanıcı portalını güncelleştirmenizi ister. İstemi kabul edin ve Kullanıcı Portalı güncelleştirmesini yükler. Sanal Dizin adının önceden yüklü olan sanal dizinle eşleşip eşleşmediğini denetleyin (örneğin, MultiFactorAuth).
3. Kullanıcı Portalı kendi sunucusahibiyseniz, MultiFactorAuthenticationUserPortalSetup64. msi dosyasını MFA sunucularından birinin install konumundan kopyalayın ve Kullanıcı Portalı Web sunucusuna yerleştirin. Yükleyiciyi çalıştırın.

   Bir hata oluşursa, "Microsoft Visual C++ 2015 Redistributable Update 1 veya daha yüksek bir sürümü gerekir," en son güncelleştirme paketini [Microsoft İndirme Merkezi](https://www.microsoft.com/download/)' nden indirin ve yükleyin. Hem x86 hem de x64 sürümlerini yükler.

4. Güncelleştirilmiş Kullanıcı Portalı yazılımı yüklendikten sonra, adım 1 ' de yaptığınız Web. config yedeklemesini yeni Web. config dosyasıyla karşılaştırın. Yeni Web. config dosyasında yeni bir öznitelik yoksa, yeni birinin üzerine yazmak için Backup Web. config dosyanızı sanal dizine kopyalayın. Diğer bir seçenek de, appSettings değerlerini ve Web hizmeti SDK URL 'sini yedekleme dosyasından yeni Web. config dosyasına kopyalayıp yapıştırmaktır.

Birden çok sunucu üzerinde Kullanıcı Portalı varsa, bu yüklemeyi tümünde tekrarlayın.

## <a name="upgrade-the-mobile-app-web-service"></a>Mobil uygulama Web hizmeti 'ni yükseltme

> [!NOTE]
> 8,0 'den daha eski bir Azure MFA sunucusu sürümünden 8.0 + sürümüne yükseltirken, mobil uygulama Web hizmeti yükseltmeden sonra kaldırılabilir

## <a name="upgrade-the-ad-fs-adapters"></a>AD FS bağdaştırıcılarını yükseltme

Bu bölüme geçmeden önce MFA sunucularınızın ve Kullanıcı portalının yükseltmesini tamamladıktan sonra.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>MFA AD FS farklı sunucularda çalışıyorsa

Bu yönergeler yalnızca, çok faktörlü kimlik doğrulama sunucusunu AD FS sunucularınızdan ayrı olarak çalıştırırsanız geçerlidir. Her iki hizmet aynı sunucuda çalışıyorsa, bu bölümü atlayın ve yükleme adımlarına gidin. 

1. AD FS kayıtlı olan MultiFactorAuthenticationAdfsAdapter. config dosyasının bir kopyasını kaydedin veya şu PowerShell komutunu kullanarak yapılandırmayı dışarı aktarın: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Daha önce yüklenen sürüme bağlı olarak bağdaştırıcı adı "bağdaştırıcı windowsazuremultifactorauthentication" veya "AzureMfaServerAuthentication" olur.
2. Aşağıdaki dosyaları MFA sunucusu yükleme konumundan AD FS sunucularına kopyalayın:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Komutun`Register-AdfsAuthenticationProvider` sonuna ekleyerek `-ConfigurationFilePath [path]` Register-MultiFactorAuthenticationAdfsAdapter. ps1 betiğini düzenleyin. *[Path]* öğesini MultiFactorAuthenticationAdfsAdapter. config dosyasının tam yoluyla veya önceki adımda aktarılmış yapılandırma dosyası ile değiştirin.

   Eski yapılandırma dosyasıyla eşleşip eşleşseler görmek için yeni MultiFactorAuthenticationAdfsAdapter. config içindeki öznitelikleri denetleyin. Yeni sürümde herhangi bir öznitelik eklendiyse veya kaldırılırsa, eski yapılandırma dosyasından öznitelik değerlerini yeni bir dosyaya kopyalayın veya eski yapılandırma dosyasını eşleşecek şekilde değiştirin.

### <a name="install-new-ad-fs-adapters"></a>Yeni AD FS bağdaştırıcıları 'nı yükler

> [!IMPORTANT]
> Bu bölümün 3-8 adımında kullanıcılarınız iki aşamalı doğrulama gerçekleştirmek zorunda olmayacaktır. Birden çok kümede yapılandırılmış AD FS varsa, kapalı kalma süresini önlemek için gruptaki her kümeyi diğer kümelerden bağımsız olarak kaldırabilir, yükseltebilir ve geri yükleyebilirsiniz.

1. Bazı AD FS sunucularını gruptan kaldırın. Diğerleri çalışmaya devam ederken bu sunucuları güncelleştirin.
2. Yeni AD FS bağdaştırıcısını AD FS grubundan kaldırılan her sunucuya yükler. MFA sunucusu her bir AD FS sunucusunda yüklüyse, MFA sunucusu yönetim UX aracılığıyla güncelleştirme yapabilirsiniz. Aksi takdirde, MultiFactorAuthenticationAdfsAdapterSetup64. msi dosyasını çalıştırarak güncelleştirin.

   Bir hata oluşursa, "Microsoft Visual C++ 2015 Redistributable Update 1 veya daha yüksek bir sürümü gerekir," en son güncelleştirme paketini [Microsoft İndirme Merkezi](https://www.microsoft.com/download/)' nden indirin ve yükleyin. Hem x86 hem de x64 sürümlerini yükler.

3. **AD FS** > **kimlik doğrulama ilkeleri** > **genel çok faktörlü kimlik doğrulama ilkesini Düzenle**' ye gidin. **Bağdaştırıcı windowsazuremultifactorauthentication** veya **AzureMFAServerAuthentication** işaretini kaldırın (yüklü olan geçerli sürüme bağlı olarak).

   Bu adım tamamlandıktan sonra, bu AD FS kümesinde MFA sunucusu üzerinden iki adımlı doğrulama, 8. adımı tamamlayana kadar kullanılamaz.

4. Unregister-MultiFactorAuthenticationAdfsAdapter. ps1 PowerShell betiğini çalıştırarak AD FS bağdaştırıcısının eski sürümünün kaydını kaldırın. *-Name* parametresinin ("bağdaştırıcı windowsazuremultifactorauthentication" veya "AzureMFAServerAuthentication") adım 3 ' te görüntülenen adla eşleştiğinden emin olun. Bu, merkezi bir yapılandırma olduğundan aynı AD FS kümesindeki tüm sunucular için geçerlidir.
5. Register-MultiFactorAuthenticationAdfsAdapter. ps1 PowerShell betiğini çalıştırarak yeni AD FS bağdaştırıcısını kaydettirin. Bu, merkezi bir yapılandırma olduğundan aynı AD FS kümesindeki tüm sunucular için geçerlidir.
6. AD FS grubundan kaldırılan her sunucuda AD FS hizmetini yeniden başlatın.
7. Güncelleştirilmiş sunucuları AD FS grubuna geri ekleyin ve diğer sunucuları gruptan kaldırın.
8. **AD FS** > **kimlik doğrulama ilkeleri** > **genel çok faktörlü kimlik doğrulama ilkesini Düzenle**' ye gidin. **AzureMfaServerAuthentication**denetleyin.
9. AD FS grubundan kaldırılan sunucuları güncelleştirmek için 2. adımı tekrarlayın ve bu sunuculardaki AD FS hizmetini yeniden başlatın.
10. Bu sunucuları AD FS grubuna geri ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Multi-Factor Authentication ve üçüncü taraf VPN 'ler Ile gelişmiş senaryolara](howto-mfaserver-nps-vpn.md) örnek alın

* [MFA sunucusunu Windows Server Active Directory ile eşitler](howto-mfaserver-dir-ad.md)

* Uygulamalarınız için [Windows kimlik doğrulamasını yapılandırma](howto-mfaserver-windows.md)
