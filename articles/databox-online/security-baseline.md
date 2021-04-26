---
title: Azure Stack Edge için Azure Güvenlik temeli
description: Azure Stack Edge güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 335056260ddea0c15db761df109e69333ef474f3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641376"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Azure Stack Edge için Azure Güvenlik temeli

Bu güvenlik temeli [Azure Güvenlik kıyaslama sürümü 2,0](../security/benchmarks/overview.md) ' dan Microsoft Azure Stack Edge 'e kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Stack Edge için geçerli olan ilgili kılavuza göre gruplandırılır. Azure Stack Edge için geçerli olmayan **denetimler** dışlandı.

Azure Stack kenarının Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure Stack Edge güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: iç trafik için güvenlik uygulama

**Rehberlik**: müşteriler, şirket içi erişim için Microsoft tarafından sağlanan, fiziksel ve Azure Stack Edge cihazını özel ağına dağıtır ve daha fazla güvenli hale getirmek için seçenekler sağlar. Örneğin, Azure Stack Edge cihazının müşterinin iç ağından erişilebilir olması ve müşterinin yapılandırdığı bir IP 'si olması gerekir. Ayrıca, müşterinin cihazın Kullanıcı arabirimine erişmesi için bir erişim parolası seçilir. 

İç trafik daha da güvenli hale getirilir:

- Azure Stack Edge cihazının Azure portal ve SDK yönetimi için Aktarım Katmanı Güvenliği (TLS) sürüm 1,2 gereklidir.

- Cihaza istemci erişimi, varsayılan güvenli protokol olarak Standart TLS 1,2 kullanan yerel Web Kullanıcı arabirimi üzerinden yapılır.

- Yalnızca yetkili bir Azure Stack Edge Pro cihazının, müşterinin Azure aboneliğinde oluşturana Azure Stack Edge hizmetine katılmasına izin verilir.

Ek bilgiler başvurulan bağlantılarda bulunabilir.
 
- [Windows istemcilerinde TLS 1,2 'yi Yapılandırma Azure Stack Edge Pro GPU cihazına erişme](./azure-stack-edge-gpu-configure-tls-settings.md)

- [Hızlı başlangıç-GPU ile Azure Stack Edge Pro ile çalışmaya başlama](azure-stack-edge-gpu-quickstart.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ns-2-connect-private-networks-together"></a>NS-2: özel ağları birbirine bağlama

**Rehberlik**: müşteriler, Azure Stack Edge cihazını şirket içi özel ağınızdan Azure ağına bağlamak için Noktadan siteye sanal özel ağ (VPN) seçebilirler. VPN, müşterinin cihazından Azure 'a kadar aktarım katmanı güvenliği üzerinden hareket halindeki veriler için ikinci bir şifreleme katmanı sunar. 

Müşteriler, Azure Stack Edge cihazında Azure portal veya Azure PowerShell aracılığıyla bir sanal özel ağ yapılandırabilir.

- [Azure Stack Edge Pro R ve Azure Stack Edge Mini R için Azure PowerShell betiği aracılığıyla Azure VPN 'yi yapılandırma](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Windows istemcilerinde TLS 1,2 'yi Yapılandırma Azure Stack Edge Pro GPU cihazına erişme](./azure-stack-edge-gpu-configure-tls-settings.md)

- [Öğretici: Azure Stack Edge Pro R 'niz için sertifikaları yapılandırma](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure hizmetlerine özel ağ erişimi sağlayın

**Rehberlik**: müşteriler, Azure Stack Edge cihazını şirket içi özel ağınızdan Azure ağına bağlamak için Noktadan siteye sanal özel ağ (VPN) seçebilirler. VPN, müşterinin cihazından Azure 'a kadar aktarım katmanı güvenliği üzerinden hareket halindeki veriler için ikinci bir şifreleme katmanı sunar. 

- [Azure Stack Edge Pro R ve Azure Stack Edge Mini R için Azure PowerShell betiği aracılığıyla Azure VPN 'yi yapılandırma](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Windows istemcilerinde TLS 1,2 'yi Yapılandırma Azure Stack Edge Pro GPU cihazına erişme](./azure-stack-edge-gpu-configure-tls-settings.md)

- [Öğretici: Azure Stack Edge Pro R 'niz için sertifikaları yapılandırma](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: uygulamaları ve Hizmetleri dış ağ saldırılarına karşı koruma

**Rehberlik**: Azure Stack Edge cihazı, müşteriler tarafından yapılandırılamayan standart Windows Server ağ koruma özelliklerini içerir.

Müşteriler, Gelişmiş dağıtılmış hizmet reddi (DDoS) korumalarına sahip bir güvenlik duvarı gibi bir ağ sanal gereci kullanarak kendi özel ağını Azure Stack Edge cihazından güvenli hale getirmek için tercih edebilir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: izinsiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: Azure Stack Edge cihazı tarafından kullanılan uç noktalar Microsoft tarafından yönetilir. Müşteriler, şirket içi sistemlerine dağıtmak istedikleri ek denetimlerden sorumludur.

Azure Stack Edge cihazı hizmeti korumak için kendi yetkisiz giriş algılama özelliklerini kullanır. 

- [Azure Stack Edge güvenliğini anlama](azure-stack-edge-security.md)

- [Azure Stack Edge için bağlantı noktası bilgileri](azure-stack-edge-gpu-system-requirements.md)

- [Donanım ve yazılım yetkisiz giriş algılama günlüklerini al](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Uygulama kimliklerini güvenli ve otomatik bir şekilde yönetin

**Rehberlik**: tüm Azure Stack Edge cihazlarının Azure Active Directory (Azure AD) içinde sistem tarafından atanan yönetilen kimliği otomatik olarak vardır. Şu anda yönetilen kimlik Azure Stack Edge üzerinde barındırılan sanal makinelerin bulut yönetiminde kullanılır.

Azure Stack Edge cihazları, yerel erişim için kilitli bir duruma kadar ön yüklenir. Yerel cihaz yöneticisi hesabı için, yerel Web Kullanıcı arabirimi veya PowerShell arabirimi aracılığıyla cihazınıza bağlanmanız ve güçlü bir parola ayarlamanız gerekir. Cihaz yönetici kimlik bilgilerinizi Azure Key Vault gibi güvenli bir konumda depolayın ve yönetin ve yönetici parolasını kuruluş standartlarınıza göre döndürün.

- [Azure Stack Edge cihazını parola aracılığıyla koruyun](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Uygulama erişimi için Azure AD çoklu oturum açma (SSO) özelliğini kullanın

**Rehberlik**: Azure Stack Edge Endpoint cihazlarında çoklu oturum açma desteklenmez. Ancak, Azure bulut kaynaklarınıza erişimi güvenli hale getirmek için standart Azure Active Directory (Azure AD) tabanlı çoklu oturum açmayı etkinleştirmeyi seçebilirsiniz.

- [Azure AD ile uygulama SSO 'SU anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın

**Rehberlik**: çok faktörlü Authentication, güçlü bir kimlik doğrulama denetimidir, ancak Azure Stack Edge hizmeti kullanıcıları için bir katılım özelliğidir. Azure portal Azure Stack Edge cihazlarının uç yönetimi gibi desteklenen senaryolar için yararlanılabilir olabilir. Azure Stack Edge cihazlarına yerel erişimin çok faktörlü kimlik doğrulamasını desteklemediğini unutmayın.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Hesap anomalilerini izleyin ve uyarı oluşturun

**Rehberlik**: Azure Stack Edge hizmetinize yönelik cihaz veya kapsayıcı günlüklerini toplamak Için Azure izleyicisini etkinleştirin. Cihazınızdaki Kubernetes kümesinde birden çok işlem uygulaması çalıştıran uygulamalar gibi kapsayıcıları izleyin.

Ayrıca, Denetim günlüklerini içeren bir destek paketi, Azure Stack Edge cihazının yerel Web Kullanıcı arabiriminde toplaneklenebilir. Destek paketinin Azure portal sunulmadığını unutmayın.
 
- [Azure Stack Edge Pro cihazınızda Azure Izleyicisini etkinleştirme](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Destek paketi toplayın](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure kaynaklarına erişimi koşullara göre kısıtlayın

**Rehberlik**: Azure Active Directory (Azure AD) koşullu erişim, Azure Stack Edge hizmetinde kimlik doğrulamaya yönelik bir katılım özelliğidir. Azure Stack Edge hizmeti, farklı coğrafi konumlarda Azure Stack Edge Pro cihazını yönetmenizi sağlayan Azure portal bir kaynaktır. 

- [Koşullu erişim nedir?](../active-directory/conditional-access/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Kimlik bilgilerinin istenmeden açığa çıkma olasılığını ortadan kaldırın

**Rehberlik**: kimlik bilgilerini korumak için en iyi uygulamaları izleyin, örneğin:

- Etkinleştirme anahtarı, cihazı Azure 'daki Azure Stack Edge kaynağıyla etkinleştirmek için kullanılır.
- Azure Stack Edge cihazına erişmek için oturum açma kimlik bilgileri.
- Azure Stack Edge cihazının kurtarılmasını olanaklı kılmak için anahtar dosyaları.
- Kanal şifreleme anahtarı

Depolama hesabınızı yetkisiz kullanıcılara karşı korumaya yardımcı olmak için depolama hesabı anahtarlarınızı düzenli olarak döndürün ve eşitleyin.

- [Azure Stack Edge Pro güvenlik ve veri koruması](azure-stack-edge-security.md)

- [Depolama anahtarlarını eşitleme](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Rehberlik**: Azure Stack Edge çözümünde, iş açısından kritik cihazlara erişimi kısıtlamak için güçlü erişim denetimleri olan birkaç bileşen vardır. Kuruluşunuzun, cihazı yapılandırmak ve yönetmek için bir Kurumsal Anlaşma (EA) veya bulut çözümü sağlayıcısı (CSP) veya bir Microsoft Azure Sponsorluğu aboneliğine sahip olması gerekir: 

Azure Stack Edge hizmeti, Azure güvenlik özellikleriyle Azure 'da barındırılan bir yönetim hizmeti olarak korunur. Tüm yazılım geliştirme seti yönetim işlemlerine yönelik cihaz özelliklerindeki kaynağınız için şifreleme anahtarını alabilir, ancak yalnızca kaynak Graph API için uygun izinlere sahipseniz şifreleme anahtarını görüntüleyebilirsiniz.

Azure Stack Edge Pro cihazı, yerel olarak işleyerek ve sonra Azure 'a göndererek verilerinizi dönüştürmenizi sağlayan şirket içi bir cihazdır. Cihazınız:

- Azure Stack Edge hizmetine erişmek için bir etkinleştirme anahtarına ihtiyaç duyuyor.
- her zaman bir cihaz parolasıyla korunur.
- güvenli önyükleme etkin.

- kilitli bir cihazdır. Cihaz temel kart yönetim denetleyicisi (BMC) ve BIOS, parola korumalı. BIOS, sınırlı Kullanıcı erişimi tarafından korunur.
- Windows Defender Device Guard 'ı çalıştırır. Device Guard, yalnızca kod bütünlüğü ilkeleriniz içinde tanımladığınız güvenilen uygulamaları çalıştırmanızı sağlar.

Azure Stack Edge 'e erişmek için kullanılan tüm kimlik bilgilerini ve gizli dizileri korumak için en iyi uygulamaları izleyin. 

- [Parolalarla ilgili en iyi yöntemler](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Kullanıcı erişimini düzenli olarak gözden geçirin ve mutabık kılın

**Rehberlik**: Azure Stack Edge, cihazı yapılandırabilirler ' EdgeUser ' adlı bir kullanıcıya sahiptir. Ayrıca, cihazdaki yerel Azure Resource Manager özellikleri için ' EdgeArmUser ' Azure Resource Manager kullanıcısı da vardır. 

En iyi uygulamalar aşağıdakileri korumak için izlenir:

- Şirket içi cihaza erişmek için kullanılan kimlik bilgileri

- SMB paylaşımının kimlik bilgileri.

- NFS paylaşımlarını kullanacak şekilde yapılandırılmış istemci sistemlerine erişim.

- Blob REST API kullanılırken yerel depolama hesaplarına erişmek için kullanılan yerel depolama hesabı anahtarları.

Başvurulan bağlantıda ek bilgiler bulunabilir.

- [Azure Stack Edge cihazlarınızla ilgili güvenlik bilgileri](azure-stack-edge-security.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Ayrıcalıklı erişim iş istasyonlarını kullanın

**Rehberlik**: Yöneticiler, geliştiriciler ve kritik hizmet işleçleri gibi hassas rollerin güvenliği açısından güvenli ve yalıtılmış iş istasyonları kritik öneme sahiptir. Yönetim görevleri için Azure savunma ile veya olmayan yüksek düzeyde güvenli Kullanıcı iş istasyonları kullanın. Yönetim görevleri için güvenli ve yönetilen bir kullanıcı iş istasyonu dağıtmak üzere Azure Active Directory (Azure AD), Microsoft Defender Gelişmiş tehdit koruması (ATP) ve Microsoft Intune kullanın. 

Güvenli iş istasyonları güçlü kimlik doğrulaması, yazılım ve donanım temelleri, kısıtlı mantıksal erişim ve ağ erişimi gibi güvenli yapılandırma özelliklerinin uygulanması için merkezi olarak yönetilebilir.

- [Ayrıcalıklı erişim iş istasyonlarını anlama](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Ayrıcalıklı erişim iş istasyonu dağıtma](/security/compass/privileged-access-deployment)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Hassas verileri koruyun

**Rehberlik**: Azure Stack Edge, bu verilere yalnızca yetkili kullanıcılarla erişim sahibi olan tüm verileri gizli olarak değerlendirir. Azure Stack Edge hizmetine erişmek için kullanılan kimlik bilgilerini korumak için en iyi uygulamaları izlemeniz gerekir.

- [Azure Stack Edge Pro güvenlik ve veri koruması](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Hassas bilgileri aktarım sırasında şifreleyin

**Rehberlik**: Azure Stack Edge, uçuştaki veriler için güvenli kanallar kullanır. Bunlar:

- Standart TLS 1,2, cihaz ile Azure bulutu arasında taşınan veriler için kullanılır. TLS 1,1 ve önceki bir sürümü için geri dönüş yoktur. TLS 1,2 desteklenmiyorsa, aracı iletişimi engellenir. TLS 1,2 Ayrıca Azure portal ve yazılım geliştirme seti (SDK) yönetimi için de gereklidir.

- İstemciler, bir tarayıcının yerel Web Kullanıcı arabirimi aracılığıyla cihazınıza erişebilince, Standart TLS 1,2 varsayılan güvenli protokol olarak kullanılır

En iyi uygulama, tarayıcınızı TLS 1,2 kullanacak şekilde yapılandırmaktır. Veri sunucularınızdan verileri korumak için şifreleme ile SMB 3,0 kullanın.

- [Uçuş sırasında verileri korumak için en iyi uygulamalar](azure-stack-edge-security.md#protect-data-in-flight)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="asset-management"></a>Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Varlık Yönetimi](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>VY-1: Güvenlik ekibinin varlıklara yönelik risklerde görünürlük elde etmesini sağlama

**Rehber**: Güvenlik ekiplerinin Azure Güvenlik Merkezi’ni kullanarak güvenlik risklerini izlemesini sağlamak için bu ekiplere Azure kiracınız ve abonelikleriniz üzerinde Güvenlik Okuyucusu izinleri verildiğinden emin olun. 

Güvenlik ekibi sorumluluklarının yapılandırmasına bağlı olarak, güvenlik risklerinin izlenmesi merkezi güvenlik ekibinin veya yerel bir ekibin sorumluluğunda olabilir. Gerçi güvenlik içgörüleri ve risklerinin kuruluş içinde her zaman merkezi bir konumda toplanması gerekir. 

Güvenlik Okuyucusu izinleri kiracının tamamına (Kök Yönetim Grubu) geniş kapsamlı olarak uygulanabileceği gibi, izinlerin kapsamı yönetim gruplarıyla veya belirli aboneliklerle de sınırlanabilir. 

İş yüklerinin ve hizmetlerin görünürlüğünü almak için ek izinlerin gerekli olabileceğini unutmayın. 

- [Güvenlik Okuyucusu Rolüne Genel Bakış](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure Yönetim Gruplarına Genel Bakış](../governance/management-groups/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>ÖÖ-5: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: yalnızca yetkili kullanıcılar (örneğin, ' EdgeArmUser ', Azure Stack Edge cihaz API 'lerine yerel Azure Resource Manager aracılığıyla erişebilir. Kullanıcı hesabı parolaları yalnızca Azure portal yönetilebilir. 

- [Azure Resource Manager parolasını ayarlama](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>HAR-6: yalnızca işlem kaynaklarında onaylanan uygulamaları kullanın

**Rehberlik**: kendi uygulamalarınızı yerel olarak oluşturulan tüm sanal makinelerde çalışacak şekilde getirebilirsiniz. Yığın uç cihazınızda yerel işlem sanal makineleri oluşturmak için PowerShell betikleri kullanın. Yalnızca güvenilen uygulamaların yerel sanal makinelerde çalıştırılmasını kesinlikle öneririz. 

- [Windows ortamında PowerShell betiği yürütmeyi denetleme](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure kaynakları için tehdit algılamayı etkinleştirme

**Rehberlik**: Azure Stack Edge tehdit algılama özellikleri sunmaz. Ancak, müşteriler çevrimdışı tehdit algılama ve çözümleme için bir destek paketinde denetim günlüklerini toplayabilir. 

- [Azure Stack Edge cihazı için destek paketini topla](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure ağ etkinlikleri için günlüğe kaydetmeyi etkinleştirin

**Rehberlik**: Azure Stack Edge, indirilebilir destek paketinin bir parçası olarak etkinleştirilmiş ağ denetim günlükleri içeriyor. Bu Günlükler, Azure Stack Edge cihazlarınız için yarı gerçek zamanlı izleme uygulamak üzere ayrıştırılabilir.

- [Azure Stack Edge bir destek paketi toplayın](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Rehberlik**: Günlükler ile gerçek zamanlı izleme şu anda Azure Stack Edge için desteklenmiyor. Azure Stack Edge Pro cihazınıza yönelik güvenlik duvarı, yazılım, donanım izinsiz giriş ve sistem olay günlükleri gibi çeşitli günlükleri analiz etmenize olanak tanıyan bir destek paketi toplama özelliği vardır. Yazılım izinsiz giriş veya varsayılan güvenlik duvarı günlüklerinin gelen ve giden trafik için toplandığını unutmayın.

- [Azure Stack Edge için bir destek paketi toplayın](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Rehberlik**: Günlükler ile gerçek zamanlı izleme şu anda Azure Stack Edge için desteklenmiyor. Bununla birlikte, içinde yer alan çeşitli günlükleri analiz etmenizi sağlayan bir destek paketi toplayabilirsiniz.  Destek paketi sıkıştırılır ve seçtiğiniz yola indirilir. Paketin sıkıştırmasını açın ve içinde bulunan sistem günlük dosyalarını görüntüleyin. Bu günlükleri, analiz için bir güvenlik bilgileri olay yönetimi aracına veya başka bir merkezi depolama konumuna da gönderebilirsiniz.

- [Azure Stack Edge için bir destek paketi toplayın](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Günlük depolama alanının saklama süresini yapılandırın

**Rehberlik**: günlük depolama saklama süresi Azure Stack Edge cihazında değiştirilemez. Daha eski Günlükler gerektiğinde temizlenir. Her türlü gereksinimi daha uzun bir süre tutmaya yönelik gereksinimler için düzenli aralıklarla cihazdan destek paketleri toplayabilirsiniz. 

- [Azure Stack Edge için bir destek paketi toplayın](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: onaylanan zaman eşitleme kaynaklarını kullanın

**Rehberlik**: Azure Stack Edge, Microsoft tarafından bir ağ zaman sağlayıcısı sunucusu olan Time.Windows.com kullanır.  Azure Stack Edge, müşterinin kendi tercih eden ağ zaman Protokolü sunucusunu yapılandırmasına de olanak tanır.

- [Azure Stack Edge cihaz saati ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Hazırlık: Azure için olay yanıtı sürecini güncelleştirin

**Rehberlik**: kurumsal olay yanıt planınızın süreçler içerdiğinden emin olun: 

- güvenlik olaylarına yanıt vermek için

- Azure bulutu için güncelleştirilmiş
 
- hazırlık sağlamak için düzenli olarak uygulanır

Kurumsal ortam genelinde standartlaştırılmış olay yanıt süreçlerini kullanarak güvenlik uygulamanız önerilir.

- [Güvenliği kuruluş genelinde uygulama](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Olay yanıtı başvuru kılavuzu](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Hazırlık: Olay bildirimini ayarlayın

**Rehberlik**: Azure Güvenlik Merkezi'nde güvenlik olayı iletişim bilgilerini ayarlayın. Microsoft, Microsoft Güvenlik Yanıt Merkezi'nin (MSRC) verilerinize kanuna aykırı veya yetkisiz erişim sağlanmasını keşfetmesi durumunda sizinle iletişime geçmek için bu iletişim bilgilerini kullanır. İsterseniz farklı Azure hizmetlerindeki olay uyarılarını ve bildirimlerini olay yanıt gereksinimlerinize göre özelleştirebilirsiniz. 

- [Azure Güvenlik Merkezi güvenlik iletişimini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: algılama ve analiz – yüksek kaliteli uyarılara göre olaylar oluşturun 

**Rehberlik**: yüksek kaliteli uyarılar oluşturma ve kalitesini ölçmek için bir işleminiz olduğundan emin olun. Bu, geçmiş olaylardan dersler öğrenmenize ve hatalı pozitif uyarıların işlenmesi için harcanan süreyi önlemeye olanak tanımak üzere analist için uyarıları önceliklendirmenize olanak tanır. Geçmiş olaylardan deneyimlerinizi temel alan yüksek kaliteli uyarılar oluşturun, farklı uyarı kaynakları için günlüğe kaydetme ve bağıntı ile uyarıları oluşturmak ve temizlemek üzere tasarlanan, doğrulanan topluluk kaynakları ve araçları. 

Azure Güvenlik Merkezi, birçok Azure varlığı arasında yüksek kaliteli uyarılar sağlar. Uyarıları Azure Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanabilirsiniz. Bir araştırmaya yönelik otomatik olaylar oluşturmak için Azure Sentinel ile gelişmiş uyarı kuralları oluşturun. 

Azure kaynaklarına riskleri belirlemenize yardımcı olmak için, dışa aktarma özelliğiyle Güvenlik Merkezi uyarılarınızı ve önerilerinizi Azure Sentinel 'e aktarın. Sürekli güvenlik için uyarıları ve önerileri otomatik bir biçimde dışarı aktarmak için bir işlem oluşturmanız önerilir.

- [Dışarı aktarma işlemini yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Algılama ve analiz: Olay araştırması

**Rehberlik**: analistlerin, olası olayları araştırırken oluşan etkinliğin tam görünümünü oluşturmak için farklı veri kaynaklarını sorgulayabileceği ve kullanabileceği şekilde emin olun. Görünmeyen noktaları önlemek için, bir olası saldırganın sonlandırma zincirindeki etkinliklerini izlemek üzere farklı günlük türleri toplanmalıdır.  Ayrıca, Öngörüler ve dersleri 'un geçmiş başvurusu için yakalandığından emin olun.  

Araştırmaya yönelik veri kaynakları kapsam içindeki hizmetlerden ve çalışan sistemlerden zaten toplanmakta olan merkezi günlük kaynaklarının yanı sıra şunları da içerebilir:

- Ağ verileri – ağ akış günlüklerini ve diğer analiz bilgilerini yakalamak için ağ güvenlik gruplarının akış günlüklerini, Azure Ağ İzleyicisi’ni ve Azure İzleyici’ni kullanın. 

- Çalışan sistemlerin anlık görüntüleri: 

    - Çalışan sistemin diskinin anlık görüntüsünü oluşturmak için Azure sanal makinesinin anlık görüntü özelliğini kullanın. 

    - Çalışan sistem belleğinin bir anlık görüntüsünü oluşturmak için işletim sisteminin yerel bellek dökümü özelliğini seçin.

    - Çalışan sistemlerin anlık görüntülerini oluşturmak için Azure hizmetlerinin veya üçüncü taraf yazılım yeteneğinin anlık görüntü özelliğini kullanın.

Azure Sentinel hemen her veri kaynağı üzerinde kapsamlı veri analizi gerçekleştirdiği gibi, olayların tüm yaşam döngüsünü yöneten bir olay yönetim portalı da sağlar. Araştırma sırasında elde edilen inceleme bilgileri izleme ve raporlama amacıyla bir olayla ilişkilendirilebilir. 

- [Windows makinesi diskinin anlık görüntüsü](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux makinesi diskinin anlık görüntüsü](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Desteği tanılama bilgileri ve bellek dökümü toplama](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel ile olayları araştırma](../sentinel/tutorial-investigate-cases.md)

**Azure Güvenlik Merkezi izlemesi**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Algılama ve analiz: Olayları önceliklendirin

**Rehberlik**: Uyarı önem derecesine ve varlık duyarlığına göre analistlere öncelikli olarak odaklanmaları gereken olaylar hakkında bağlam bilgisi sağlayın. İlk olarak araştırılması gereken uyarıları önceliklendirmenize yardımcı olmak için Azure Güvenlik Merkezi 'ndeki her uyarı için bir önem derecesi atandı ' yı kullanın. Önem derecesinde, Güvenlik Merkezi’nin bulguya ne kadar güvendiği veya uyarıyı verirken kullanılan analiz kadar, uyarıya yol açan etkinliğin ardında kötü bir amaç olduğuna ilişkin güvenilirlik düzeyi de temel alınır.

Buna ek olarak Azure kaynaklarını, özellikle hassas verileri işleyen kaynakları belirlemek ve kategorilere ayırmak için etiketleri kullanarak kaynakları işaretleyin ve bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Kapsama, ortadan kaldırma ve kurtarma: Olay işleme sürecini otomatikleştirin

**Rehberlik**: Yanıt süresini kısaltmak ve analistlerin yükünü hafifletmek için el ile yapılan yinelenen görevleri otomatikleştirin. El ile gerçekleştirilen görevlerin yürütülmesi daha uzun sürer ve olay yanıt sürecini yavaşlatarak analistlerin işleyebileceği olay sayısını azaltır. Ayrıca el ile gerçekleştirilen görevler analistler açısından daha yorucudur. Dolayısıyla gecikmelere neden olan insan hatası riskini artırır ve analistlerin karmaşık görevlere etkili bir şekilde odaklanma becerisini azaltır. Eylemleri otomatik olarak tetiklemek veya gelen güvenlik uyarılarını yanıtlamak üzere bir playbook çalıştırmak için Azure Güvenlik Merkezi’nin ve Azure Sentinel’in iş akışı otomasyonu özelliklerini kullanın. Playbook bildirimleri gönderme, hesapları devre dışı bırakma ve sorunlu ağları yalıtma gibi eylemleri gerçekleştirir. 

- [Güvenlik Merkezi’nde iş akışı otomasyonunu yapılandırma](../security-center/workflow-automation.md)

- [Azure Güvenlik Merkezi’nde otomatik tehdit yanıtlarını ayarlama](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel’de otomatik tehdit yanıtlarını ayarlama](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="posture-and-vulnerability-management"></a>Duruş ve Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Duruş ve Güvenlik Açığı Yönetimi](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>BD-3: işlem kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: Azure Stack Edge, müşteriler tarafından oluşturulan yerel sanal makineler için güvenli yapılandırma oluşturma desteği sunar. Yerel sanal makineler oluştururken güvenlik temellerini oluşturmak için Microsoft tarafından sunulan yönergelerin kullanılması önemle önerilir,

- [Güvenlik uyumluluğu araç seti 'ne dahil edilen güvenlik temellerini indirin](/windows/security/threat-protection/windows-security-baselines)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>BD-4: işlem kaynakları için güvenli yapılandırmalara dayanmalar

**Rehberlik**: Azure Stack Edge, müşteriler tarafından oluşturulan yerel sanal makineler için güvenli yapılandırmaların sağlanmasına yönelik herhangi bir destek sunmaz. Müşterilerin, işlem kaynakları için güvenli yapılandırmaların sağlanmasına yardımcı olması için güvenlik uyumluluğu araç takımları 'nı (SCT) kullanmaları önemle önerilir.

Azure Stack Edge tarafından yönetilen konak işletim sistemi ve sanal makineler, güvenlik yapılandırmalarının bakımını korur. 

- [Windows güvenlik temelleri](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>BD-5: özel işletim sistemi ve kapsayıcı görüntülerini güvenli bir şekilde depolayın

**Rehberlik**: Azure Stack Edge tarafından yönetilen ana bilgisayar işletim sistemleri ve sanal makineler güvenli bir şekilde depolanır. 

Müşteriler, kendi sanal makine ve kapsayıcı görüntülerini getirebilir ve bunların güvenli yönetiminden sorumludur.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>DG-7: Yazılım güvenlik açıklarını hızla ve otomatik olarak düzeltme

**Rehberlik**: Azure Stack Edge, güvenlik açıklarını düzeltmek için bazı güncelleştirmeler kullanılabilir olduğunda düzenli Düzeltme Eki Güncellemeleri ve uyarılar müşterileri sağlar. En son düzeltme ekleriyle, cihazlarını ve sanal makinelerini (bunlar tarafından oluşturulan) güncel tutmanız müşterinin sorumluluğundadır.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Gerektiğinde Azure kaynaklarınızda sızma testi veya kırmızı takım etkinlikleri gerçekleştirerek tüm kritik güvenlik bulgularının düzeltilmesini sağlayın.
Sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak için Microsoft Bulut Sızma Testi Etkileşim Kuralları'na uygun hareket edin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Azure'da sızma testi yapma](../security/fundamentals/pen-testing.md)

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="endpoint-security"></a>Uç nokta güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: uç nokta güvenliği](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: uç nokta algılamayı ve yanıtını kullanın (EDR)

**Rehberlik**: Azure Stack Edge, doğrudan Endpoint Detection ve yanıt (EDR) desteklemez. Ancak, bir destek paketi toplayabilir ve denetim günlüklerini alabilirsiniz. Bu günlükler daha sonra anormal etkinlikleri denetlemek için analiz edilebilir. 

- [Azure Stack Edge cihazı için destek paketini topla](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: merkezi olarak yönetilen modern kötü amaçlı yazılımdan koruma yazılımı kullanın

**Rehberlik**: Azure Stack Edge, Windows Defender uygulama denetimi 'NI (WDAC) yalnızca önceden belirlenmiş uygulamaların ve betiklerin çalıştırılmasına izin veren katı kod bütünlüğü (CI) ilkesiyle kullanır. Windows Defender gerçek zamanlı koruması (RTP) kötü amaçlı yazılımdan koruma da etkinleştirilmiştir. Müşteri, Azure Stack Edge cihazında yerel olarak çalıştırmak için oluşturdukları işlem VM 'lerinde kötü amaçlı yazılımdan koruma çalıştırmayı seçebilir.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="backup-and-recovery"></a>Yedekleme ve Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Yedekleme ve Kurtarma](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: düzenli otomatik yedeklemeler sağlayın

**Rehberlik**: Azure Stack Edge cihazınızın düzenli yedeklemeleri önerilir ve cihazda dağıtılan sanal makinelerde yer alan verileri korumak için Azure Backup ve diğer üçüncü taraf veri koruma çözümleriyle gerçekleştirilebilir. 

Cohesity, Commkasave VERITAS gibi üçüncü taraf veri koruma çözümleri, yerel SMB veya NFS paylaşımlarında bulunan veriler için bir yedekleme çözümü de sağlayabilir. 

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Bir cihaz hatası için hazırlanma](azure-stack-edge-gpu-prepare-device-failure.md)

- [VM 'lerde dosya ve klasörleri koruma](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="br-2-encrypt-backup-data"></a>BR-2: yedekleme verilerini şifreleme

**Rehberlik**: yedeklemelerinizin saldırılara karşı korunduğundan emin olun. Bu, gizlilik kaybına karşı korunmak için yedeklemelerin şifrelenmesini içermelidir.  Daha fazla bilgi için, Ayrıntılar için tercih ettiğiniz yedekleme çözümünüze bakın.

- [Edge Yerel paylaşımlarında verileri koruma](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Sanal makinelerdeki dosya ve klasörleri koruma](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Müşteri tarafından yönetilen anahtarlar dahil olmak üzere tüm yedeklemeleri doğrulayın

**Rehberlik**: yedeklemelerinizin veri geri yüklemesini düzenli olarak gerçekleştirir. 

- [Azure Stack Edge için Kurtarma yordamları](azure-stack-edge-gpu-recover-device-failure.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Kayıp anahtar riskini azaltma

**Rehberlik**: müşteri tarafından yönetilen tüm anahtarlar dahil tüm Azure Stack uç yedeklemelerinin kurumsal en iyi yöntemlere uygun olarak korunduğundan emin olun. Azure Stack Edge cihazınız, Azure 'daki verilerinize yönelik hedef depo olarak kullanılan bir Azure depolama hesabıyla ilişkilendirilir. 

Azure depolama hesabına erişim, Azure abonelikleri ve bu depolama hesabıyla ilişkili 2 512 bitlik depolama erişim anahtarları tarafından denetlenir. Azure Stack Edge cihazı depolama hesabına eriştiğinde, kimlik doğrulama amacıyla erişim anahtarlarından biri kullanılır. Diğer anahtar, düzenli anahtar döndürme için ayrılmış olarak tutulur. Anahtar döndürme için en iyi güvenlik uygulamalarının kullanıldığından emin olun.

- [Azure depolama hesaplarında Azure Stack Edge cihaz verilerini koruma](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="governance-and-strategy"></a>İdare ve Strateji

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: İdare ve Strateji](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Varlık yönetimi ve veri koruma stratejisi tanımlayın 

**Rehberlik**: Sistemlerin ve verilerin sürekli izlenmesine ve korunmasına yönelik net bir strateji belgelendirdiğinizden ve paylaştığınızdan emin olun. İş açısından kritik verilerin ve sistemlerin bulma, değerlendirme, koruma ve izleme süreçleri için öncelik belirleyin. 

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   İş risklerine göre veri sınıflandırma standardı

-   Riskler ve varlık envanteriyle ilgili güvenlik kuruluşu görünürlüğü 

-   Güvenlik kuruluşunun kullanılmasını onayladığı Azure hizmetleri 

-   Varlıkların yaşam döngüsü boyunca güvenliği

-   Kuruluşun veri sınıflandırmasına uygun gerekli erişim denetimi stratejisi

-   Yerel Azure ve üçüncü taraf veri koruma özelliklerinin kullanılması

-   Taşıma durumundaki ve bekleyen veriler için şifreleme gereksinimleri

-   Uygun şifreleme standartları

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenlik Mimarisi Önerileri: Depolama, veri ve şifreleme](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure Güvenliği Temelleri: Azure'da veri güvenliği, şifreleme ve depolama](../security/fundamentals/encryption-overview.md)

- [Bulut Benimseme Çerçevesi: Azure'da veri güvenliği ve şifreleme için en iyi deneyimler](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Güvenlik Karşılaştırması: Varlık yönetimi](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Güvenlik Karşılaştırması: Veri koruma](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Kurumsal segmentasyon stratejisini tanımlayın 

**Rehberlik**: Kimlik, ağ, uygulama, abonelik, yönetim grubu ve diğer denetimleri kullanarak varlık erişimi segmentasyonuna yönelik kuruluş genelinde kullanılacak bir strateji belirleyin.

Güvenlik ayrımı gereksinimiyle birbirleriyle iletişim kurma ve verilere erişme gereksinimine sahip olan sistemlerin günlük çalışmasını etkinleştirme gereksinimi arasında bir denge kurun.

Segmentasyon stratejisinin ağ güvenliği, kimlik ve erişim modelleri, uygulama izinleri/erişim modelleri ve insanlar tarafından gerçekleştirilen süreç denetimleri gibi farklı denetim türlerinde tutarlı bir şekilde uygulandığından emin olun.

- [Azure için segmentasyon stratejisi rehberi (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure için segmentasyon stratejisi rehberi (belge)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ağ segmentasyonunu kurumsal segmentasyon stratejisiyle uyumlu hale getirme](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Güvenlik duruşu yönetim stratejisini tanımlayın

**Rehberlik**: Varlıklarınız ve içinde bulundukları ortamla ilgili riskleri sürekli olarak ölçün ve ortadan kaldırın. Yayımlanmış uygulamalar, ağ giriş ve çıkış noktaları, kullanıcı ve yönetici uç noktaları gibi değeri yüksek varlıkları ve kullanıma açık olan saldırı yüzeylerini önceliklendirin.

- [Azure Güvenlik Karşılaştırması: Duruş ve güvenlik açığı yönetimi](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Kuruluş genelindeki rolleri, sorumlulukları ve hesap verilebilirlik durumlarını uyumlu hale getirin

**Rehberlik**: Güvenlik kuruluşunuzdaki roller ve sorumluluklar için net bir strateji oluşturup bunu belgelendirdiğinizden ve paylaştığınızdan emin olun. Güvenlik kararları için net bir hesap verilebilirlik süreci oluşturmayı önceliklendirin, herkesi paylaşılan sorumluluk modeli hakkında eğitin ve teknik ekiplere bulut ortamının güvenliğini sağlamaya yönelik eğitimler verin.

- [Azure Güvenliği En İyi Deneyimi 1 - İnsanlar: Ekipleri Bulut Güvenliği Yolculuğu Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Güvenliği En İyi Deneyimi 2 - İnsanlar: Ekipleri Bulut Güvenliği Teknolojileri Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Güvenliği En İyi Deneyimi 3 - Süreç: Bulut Güvenliği Kararları için Hesap Verilebilirlik Ataması Yapın](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Ağ güvenlik stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure ağ güvenliği için bir yaklaşım belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi ağ yönetimi ve güvenlik sorumluluğu

-   Kurumsal segmentasyon stratejisiyle uyumlu sanal ağ segmentasyon modeli

-   Farklı tehdit ve saldırı senaryolarına yönelik düzeltme stratejisi

-   İnternet uç düğümü ile giriş ve çıkış stratejisi

-   Hibrit bulut ve şirket içi bağlantı stratejisi

-   Güncel ağ güvenliği yapıtları (ağ diyagramları, başvuru amaçlı ağ mimarisi vb.)

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/index.yml)

- [Azure ile ağ güvenliğine genel bakış](../security/fundamentals/network-overview.md)

- [Kurumsal ağ mimarisi stratejisi](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Kimlik ve ayrıcalıklı erişim stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure kimlik ve ayrıcalıklı erişim yaklaşımları belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi bir kimlik ve kimlik doğrulaması sistemi ile diğer iç ve dış kimlik sistemleriyle olan bağlantısı

-   Farklı kullanım örneklerine ve koşullara yönelik güçlü kimlik doğrulama yöntemleri

-   Yüksek ayrıcalıklı kullanıcıların korunması

-   Kullanıcı etkinlikleri için anomali izleme ve işleme  

-   Kullanıcı kimliği ve erişim gözden geçirmesi ile uzlaştırma süreci

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Kimlik yönetimi](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Güvenlik Karşılaştırması: Ayrıcalıklı erişim](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure'da kimlik yönetim güvenliğine genel bakış](../security/fundamentals/identity-management-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Günlüğe kaydetme ve tehdit yanıtı stratejisi tanımlayın

**Rehberlik**: Uyumluluk gereksinimleri kapsamında tehditleri hızlı bir şekilde algılamak ve düzeltmek için bir günlüğe kaydetme ve tehdit yanıtı stratejisi oluşturun. Tümleştirme ve el ile gerçekleştirilen adımlar yerine tehditlere odaklanabilmeleri için analistlere yüksek kaliteli uyarılar ve sorunsuz deneyimler sunmayı önceliklendirin. 

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Güvenlik operasyonları (SecOps) organizasyonunun rolü ve sorumlulukları 

-   NIST veya diğer sektör çerçeveleriyle uyumlu iyi tanımlanmış bir olay yanıt süreci 

-   Tehdit algılama, olay yanıtı ve uyumluluk gereksinimlerini karşılamak için günlük yakalama ve saklama

-   SIEM, yerel Azure özellikleri ve diğer kaynaklar aracılığıyla merkezi görünürlük ve bilgi bağıntısı 

-   Planı müşterilerinize, tedarikçilerinize ve diğer proje katılımcılarına iletme ve onları bilgilendirme

-   Günlüğe kaydetme ve tehdit algılama, adli araştırma, saldırı önleme ve ortadan kaldırma gibi olay işleme adımları için yerel Azure ve üçüncü taraf platformları kullanma

-   Olayları ve çıkarılan dersler ile kanıt koruma gibi olay sonrası etkinlikleri işlemeye yönelik süreçler

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Günlüğe kaydetme ve tehdit algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Güvenlik Karşılaştırması: Olay yanıtı](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Güvenliği En İyi Deneyimi 4 - Süreç. Bulut için Olay Yanıt Sürecini Güncelleştirme](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Benimseme Çerçevesi, günlüğe kaydetme ve raporlama kararı rehberi](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin