---
title: Windows sanal masaüstü güvenliği en iyi yöntemleri-Azure
description: Windows sanal masaüstü ortamınızı güvenli tutmak için en iyi uygulamalar.
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: fb0935ca2ffcad93ba47ccd207603dd870dc26b0
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445712"
---
# <a name="security-best-practices"></a>En iyi güvenlik uygulamaları

Windows sanal masaüstü, kuruluşunuzun güvende tutulması için birçok güvenlik özelliği içeren bir yönetilen sanal masaüstü hizmetidir. Windows sanal masaüstü dağıtımında, Microsoft müşterinin adına hizmetlerin parçalarını yönetir. Hizmetin, ters bağlanma gibi birçok yerleşik gelişmiş güvenlik özelliği vardır. Bu, uzak masaüstü bilgisayarların her yerden erişilebilir olması riskini azaltır.

Bu makalede, müşterilerinizin Windows sanal masaüstü dağıtımlarını güvenli tutmak için yönetici olarak uygulayabileceğiniz ek adımlar açıklanmaktadır.

## <a name="security-responsibilities"></a>Güvenlik sorumlulukları

Bulut Hizmetleri, geleneksel şirket içi sanal masaüstü altyapılarından (vDIS) farklı hale gelir, güvenlik sorumluluklarını nasıl işler. Örneğin, geleneksel bir şirket içi VDı 'da, müşteri her güvenlik yönünden sorumludur. Ancak, çoğu bulut hizmetlerinde bu sorumluluklar müşteri ve şirket arasında paylaşılır.

Windows sanal masaüstü 'Nü kullandığınızda, bazı bileşenler ortamınız için zaten güvenli bir şekilde sağlandığı zaman, kuruluşunuzun güvenlik gereksinimlerine uyacak şekilde diğer bölgeleri yapılandırmanız gerektiğini anlamak önemlidir.

Windows sanal masaüstü dağıtımınızda sorumlu olduğunuz güvenlik ihtiyaçları aşağıda verilmiştir:

| Güvenlik gereksinimi | Müşteri bundan sorumlu mi? |
|---------------|:-------------------------:|
|Kimlik|Yes|
|Kullanıcı cihazları (mobil ve bılgısayar)|Yes|
|Uygulama güvenliği|Yes|
|Oturum Ana Bilgisayarı işletim sistemi|Yes|
|Dağıtım yapılandırması|Yes|
|Ağ denetimleri|Yes|
|Sanallaştırma denetim düzlemi|Hayır|
|Fiziksel konaklar|Hayır|
|Fiziksel ağ|Hayır|
|Fiziksel veri merkezi|Hayır|

Müşterinin sorumlu olmadığı güvenlik ihtiyaçları Microsoft tarafından işlenir.

## <a name="azure-security-best-practices"></a>Azure güvenliğinde en iyi uygulamalar

Windows sanal masaüstü, Azure 'un altındaki bir hizmettir. Windows sanal masaüstü dağıtımınızın güvenliğini en üst düzeye çıkarmak için, çevreleyen Azure altyapısının ve yönetim düzleminin da güvenli hale geldiğinden emin olmanız gerekir. Altyapınızın güvenliğini sağlamak için Windows sanal masaüstü 'Nün daha büyük Azure ekosisteminize nasıl uyduğunu göz önünde bulundurun. Azure ekosistemi hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](../security/fundamentals/best-practices-and-patterns.md).

Bu bölümde, Azure ekosisteminizin güvenliğini sağlamaya yönelik en iyi yöntemler açıklanmaktadır.

### <a name="enable-azure-security-center"></a>Azure Güvenlik Merkezi 'Ni etkinleştir

Abonelikler, sanal makineler, Anahtar kasaları ve depolama hesapları için Azure Güvenlik Merkezi standardını etkinleştirmenizi öneririz.

Azure Güvenlik Merkezi standardı ile şunları yapabilirsiniz:

* Güvenlik açıklarını yönetin.
* PCI gibi yaygın çerçeveler ile uyumluluğu değerlendirin.
* Ortamınızın genel güvenliğini güçlendirin.

Daha fazla bilgi edinmek için bkz. [Azure aboneliğinizi Güvenlik Merkezi standardına](../security-center/security-center-get-started.md)ekleme.

### <a name="improve-your-secure-score"></a>Güvenlik Puanınızı İyileştirme

Güvenli puan, genel güvenliği iyileştirmeye yönelik öneriler ve en iyi uygulama önerileri sağlar. Bu öneriler, en önemli olanları seçmenize yardımcı olmak için önceliklendirilir ve hızlı onarım seçenekleri olası güvenlik açıklarını hızla gidermeye yardımcı olur. Bu öneriler zaman içinde günceldir ve ortamınızın güvenliğini sağlamanın en iyi yolları hakkında sizi güncel tutar. Daha fazla bilgi edinmek için bkz. [Azure Güvenlik Merkezi 'Nde güvenli puanınızı geliştirme](../security-center/secure-score-security-controls.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Windows sanal masaüstü güvenlik en iyi uygulamaları

Windows sanal masaüstü 'nde birçok yerleşik güvenlik denetimi vardır. Bu bölümde, kullanıcılarınızı ve verilerinizi güvenli tutmak için kullanabileceğiniz güvenlik denetimleri hakkında bilgi edineceksiniz.

### <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication gerektir

Windows sanal masaüstündeki tüm kullanıcılar ve yöneticiler için Multi-Factor Authentication gerektirme, tüm dağıtımınızın güvenliğini artırır. Daha fazla bilgi için bkz. [Windows sanal masaüstü Için Azure AD Multi-Factor Authentication etkinleştirme](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Koşullu erişimi etkinleştir

[Koşullu erişimin](../active-directory/conditional-access/overview.md) etkinleştirilmesi, kullanıcılara Windows sanal masaüstü ortamınıza erişim vermeden önce riskleri yönetmenizi sağlar. Hangi kullanıcılara erişim izni vereceğinize karar verirken, kullanıcının ne olduğunu, oturum açmasını ve hangi cihazdan kullandığını da düşünmeniz önerilir.

### <a name="collect-audit-logs"></a>Denetim günlüklerini topla

Denetim günlüğü toplamayı etkinleştirmek, Windows sanal masaüstü ile ilgili Kullanıcı ve yönetici etkinliğini görüntülemenize olanak sağlar. Anahtar denetim günlüklerine bazı örnekler şunlardır:

-   [Azure Etkinlik Günlüğü](../azure-monitor/essentials/activity-log.md)
-   [Etkinlik günlüğünü Azure Active Directory](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Oturum Konakları](../azure-monitor/agents/agent-windows.md)
-   [Windows sanal masaüstü tanılama günlüğü](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault günlükleri](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>RemoteApps kullanma

Bir dağıtım modeli seçerken, uzak kullanıcılara tüm sanal masaüstlerine erişim sağlayabilir ya da yalnızca uygulamaları seçebilirsiniz. Uzak uygulamalar veya RemoteApps, Kullanıcı sanal masaüstündeki uygulamalarla birlikte çalışarak sorunsuz bir deneyim sağlar. RemoteApps, yalnızca kullanıcının uygulama tarafından kullanıma sunulan uzak makinenin bir alt kümesiyle çalışmasına izin vererek riski azaltır.

### <a name="monitor-usage-with-azure-monitor"></a>Azure Izleyici ile kullanımı izleme

[Azure izleyici](https://azure.microsoft.com/services/monitor/)Ile Windows sanal masaüstü hizmetinizin kullanımını ve kullanılabilirliğini izleyin. Hizmet etkileyen bir olay olduğunda bildirim almak için Windows sanal masaüstü hizmetinin [hizmet durumu uyarılarını](../service-health/alerts-activity-log-service-notifications-portal.md) oluşturmayı düşünün.

## <a name="session-host-security-best-practices"></a>Oturum Ana bilgisayarı güvenlik en iyi uygulamaları

Oturum Konakları, bir Azure aboneliği ve sanal ağ içinde çalışan sanal makinelerdir. Windows sanal masaüstü dağıtımınızın genel güvenliği, oturum konaklarınıza yerleştirdiğiniz güvenlik denetimlerine bağlıdır. Bu bölümde, oturum konaklarınızın güvende tutulması için en iyi uygulamalar açıklanmaktadır.

### <a name="enable-screen-capture-protection-preview"></a>Ekran yakalama korumasını etkinleştir (Önizleme)

Ekran yakalama koruması özelliği, önemli bilgilerin istemci uç noktalarında yakalanmasını önler. Bu özelliği etkinleştirdiğinizde, uzak içerik ekran görüntüleri ve ekran paylaşımlarında otomatik olarak engellenir veya gizlenir. Ayrıca, ekranınızın içeriğini sürekli olarak yakaladığı kötü amaçlı yazılımdan de gizlenir. Bu özelliği kullanırken uzak içeriğin uç noktalara kopyalanmasını engellemek için pano yeniden yönlendirmeyi devre dışı bırakmanızı öneririz.

Bu ilke, bir kayıt defteri anahtarı yapılandırılarak ana bilgisayar düzeyinde zorlanır. Bu ilkeyi etkinleştirmek için PowerShell 'i açın ve şu cmdlet 'i çalıştırarak **Fenablescreencaptureprotection** kayıt defteri anahtarını ayarlayın:

```powershell
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableScreenCaptureProtection /t REG_DWORD /d 1
```

Bu yeni özelliği test etmek için:

- Konak havuzlarınızın doğrulama ortamında sağlandığından emin olun.
- Windows Masaüstü istemcisi, sürüm 1.2.1526 veya üstünü indirdiğinizden ve yüklediğinizden emin olun.

>[!NOTE]
>Önizleme sırasında, yalnızca Windows 10 uç noktalarından gelen tam masaüstü bağlantıları bu özelliği destekler.

### <a name="enable-endpoint-protection"></a>Endpoint Protection 'ı etkinleştir

Dağıtımınızı bilinen kötü amaçlı yazılımlardan korumak için tüm oturum konaklarda Endpoint Protection 'ın etkinleştirilmesini öneririz. Windows Defender virüsten koruma veya bir üçüncü taraf programını kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. [VDI ortamında Windows Defender virüsten koruma Için dağıtım kılavuzu](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

FSLogix gibi profil çözümleri veya VHD dosyalarını bağlayan diğer çözümler için, VHD dosya uzantılarının dışında tutulması önerilir.

### <a name="install-an-endpoint-detection-and-response-product"></a>Uç nokta algılama ve yanıt ürünü yüklemesi

Gelişmiş algılama ve yanıt özellikleri sağlamak için bir uç nokta algılama ve yanıt (EDR) ürünü yüklemenizi öneririz. [Azure Güvenlik Merkezi](../security-center/security-center-services.md) 'nin etkinleştirildiği sunucu işletim sistemleri için, BIR EDR ürünü yüklemek Defender ATP 'yi dağıtır. İstemci işletim sistemleri için, bu uç noktalara [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) veya üçüncü taraf bir ürün dağıtabilirsiniz.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Tehdit ve güvenlik açığı yönetimi değerlendirmelerini etkinleştir

İşletim sistemlerinde ve uygulamalarda bulunan yazılım güvenlik açıklarını tanımlama, ortamınızı güvende tutmak için kritik öneme sahiptir. Azure Güvenlik Merkezi, sunucu işletim sistemleri için güvenlik açığı değerlendirmelerinde sorun noktaları belirlemenize yardımcı olabilir. Ayrıca, masaüstü işletim sistemleri için tehdit ve güvenlik açığı yönetimi sağlayan Defender ATP 'yi de kullanabilirsiniz. Ayrıca, Azure Güvenlik Merkezi ve Defender ATP kullanmanızı önerdiğimiz halde, üçüncü taraf ürünleri de kullanabilirsiniz.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Ortamınızdaki yazılım güvenlik açıklarını düzeltme eki uygulama

Bir güvenlik açığını tanımladıktan sonra, bu düzeltme ekini yapmanız gerekir. Bu, çalışan işletim sistemlerini, bunların içine dağıtılan uygulamaları ve yeni makineler oluşturduğunuz görüntüleri de kapsayan sanal ortamlar için geçerlidir. Satıcı düzeltme eki bildirim iletişimlerinizi izleyin ve düzeltme eklerini zamanında uygulayın. Yeni dağıtılan makinelerin mümkün olduğunca güvenli olmasını sağlamak için temel görüntülerinize her ay yama yapmanızı öneririz.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Etkin olmayan en fazla zaman ve bağlantı kesme ilkesi oluşturma

Etkin olmayan kullanıcılar, kaynakları korur ve yetkisiz kullanıcılar tarafından erişimi engeller. Zaman aşımları, Kullanıcı üretkenliğini ve kaynak kullanımını dengelemenize önerilir. Durum bilgisi olmayan uygulamalarla etkileşim kuran kullanıcılar için, makineleri kapatan ve kaynakları koruyan daha fazla agresif ilkeler göz önünde bulundurun. Bir kullanıcı boşta kalırsa (simülasyonu ya da CAD işleme gibi) çalışmaya devam eden uzun süre çalışan uygulamaların bağlantısının kesilmesi, kullanıcının çalışmasını kesintiye uğratır ve hatta bilgisayarın yeniden başlatılmasını gerektirebilir.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Boştaki oturumlar için ekran kilitleri ayarlama

Windows sanal masaüstü 'Nü, boşta kalma süresi boyunca bir makinenin ekranını kilitleyecek ve kilidini açmak için kimlik doğrulamanın gerekli hale getirerek istenmeyen sistem erişimini engelleyebilirsiniz.

### <a name="establish-tiered-admin-access"></a>Katmanlı yönetici erişimi oluşturma

Kullanıcılarınıza sanal masaüstleri için yönetici erişimi vermenizi önermiyoruz. Yazılım paketlerine ihtiyacınız varsa, bunları Microsoft Endpoint Manager gibi yapılandırma yönetimi yardımcı programları aracılığıyla kullanılabilir hale getirmeniz önerilir. Çoklu oturum ortamında, kullanıcıların yazılımı doğrudan yüklemesine izin vermemenizi öneririz.

### <a name="consider-which-users-should-access-which-resources"></a>Hangi kullanıcıların hangi kaynaklara erişmesi gerektiğini düşünün

Oturum konaklarınızı, mevcut masaüstü dağıtımınızın bir uzantısı olarak düşünün. Ağ kaynaklarına erişimi, ortamınızdaki diğer masaüstleri için yaptığınız gibi, ağ kesimlemesini ve filtrelemeyi kullanma gibi denetlemenizi öneririz. Varsayılan olarak, oturum Konakları internet 'teki herhangi bir kaynağa bağlanabilir. Azure Güvenlik Duvarı, ağ sanal gereçleri veya proxy 'leri de dahil olmak üzere trafiği sınırlamak için kullanabileceğiniz çeşitli yollar vardır. Trafiği sınırlandırmaya ihtiyacınız varsa, Windows sanal masaüstü 'Nün düzgün çalışabilmesi için uygun kuralları eklediğinizden emin olun.

### <a name="manage-office-pro-plus-security"></a>Office Pro ve güvenliğini yönetme

Oturum konaklarınızın güvenliğini sağlamaya ek olarak, bunların içinde çalışan uygulamaların da güvenliğinin sağlanması önemlidir. Office Pro Plus, oturum ana bilgisayarlarında dağıtılan en yaygın uygulamalardan biridir. Office dağıtım güvenliğini geliştirmek için, Enterprise için Microsoft 365 uygulamaları için [Güvenlik Ilkesi Danışmanı](/DeployOffice/overview-of-security-policy-advisor) 'nı kullanmanızı öneririz. Bu araç, daha fazla güvenlik için dağıtımınıza uygulayabileceğiniz ilkeleri tanımlar. Güvenlik Ilkesi Danışmanı, güvenlik ve verimliliğine etkileri temel alan ilkeler de önerir.

### <a name="other-security-tips-for-session-hosts"></a>Oturum konakları için diğer güvenlik ipuçları

İşletim sistemi yeteneklerini sınırlayarak, oturum konaklarınızın güvenliğini güçlendirebilirsiniz. Yapabileceğiniz birkaç şey aşağıda verilmiştir:

- Sürücüleri, yazıcıları ve USB cihazlarını bir kullanıcının Uzak Masaüstü oturumunda yerel cihazına yönlendirerek cihaz yeniden yönlendirmeyi denetleyin. Güvenlik gereksinimlerinizi değerlendirmeniz ve bu özelliklerin devre dışı bırakılıp bırakılmadığını kontrol etmenizi öneririz.

- Yerel ve uzak sürücü eşlemelerini gizleyerek Windows Gezgini erişimini kısıtlayın. Bu, kullanıcıların sistem yapılandırması ve kullanıcılar hakkında istenmeyen bilgileri bulmasını engeller.

- Ortamınızdaki oturum konaklarına doğrudan RDP erişimini önleyin. Yönetim veya sorun giderme için doğrudan RDP erişimine ihtiyacınız varsa, bir oturum ana bilgisayarında olası saldırı yüzeyini sınırlamak için [tam zamanında](../security-center/security-center-just-in-time.md) erişimi etkinleştirin.

- Yerel ve uzak dosya sistemlerine erişirken kullanıcılara sınırlı izinleri verin. Yerel ve uzak dosya sistemlerinizin, en az ayrıcalığa sahip erişim denetim listelerine sahip olduğundan emin olmak için izinleri kısıtlayabilirsiniz. Bu şekilde, kullanıcılar yalnızca ihtiyaç duydukları kaynaklara erişebilir ve kritik kaynakları değiştiremez veya silemez.

- İstenmeyen yazılımların oturum konakları üzerinde çalışmasını engelleyin. Oturum konakları üzerinde ek güvenlik için uygulama Kilitleyici 'yi etkinleştirerek yalnızca izin verilen uygulamaların konakta çalıştırılabilmesini sağlayabilirsiniz.

## <a name="windows-virtual-desktop-support-for-trusted-launch"></a>Güvenilen başlatma için Windows sanal masaüstü desteği

Güvenilir başlatma, rootkits, önyükleme setleri ve çekirdek düzeyi kötü amaçlı yazılım gibi saldırı vektörleri aracılığıyla "yığının en altında" tehditleri korumak için geliştirilmiş güvenlik özellikleriyle Gen2 Azure VM 'leridir. Aşağıda, Windows sanal masaüstü 'nde desteklenen, güvenilir başlatma 'nın gelişmiş güvenlik özellikleri verilmiştir. Güvenilen başlatma hakkında daha fazla bilgi edinmek için [Azure sanal makineler Için güvenilir başlatma (Önizleme)](../virtual-machines/trusted-launch.md)sayfasını ziyaret edin.

### <a name="secure-boot"></a>Güvenli Önyükleme

Güvenli önyükleme, platform üretici yazılımının desteklediği, bellenimlerinizi kötü amaçlı yazılım tabanlı kök takımları ve önyükleme setlerinden koruyan bir moddur. Bu mod yalnızca, imzalanmış Işletim sistemleri ve sürücülerin makinenin başlatılmasını sağlar. 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>Uzaktan kanıtlama kullanarak önyükleme bütünlüğünü izleme

Uzaktan kanıtlama, sanal makinelerinizin durumunu denetlemek için harika bir yoldur. Uzak kanıtlama, ölçülen önyükleme kayıtlarının var olduğunu, orijinal olduğunu ve sanal Güvenilir Platform Modülü (vTPM) geldiğini doğrular. Bir sistem durumu denetimi olarak, bir platformun doğru şekilde başlatıldığını belirten şifreleme belirsizlik sağlar. 

### <a name="vtpm"></a>vTPM

VTPM, sanal makıne başına TPM 'nin sanal örneğiyle bir donanım Güvenilir Platform Modülü (TPM) 'nin sanallaştırılmış bir sürümüdür. vTPM, sanal makinenin (UEFı, IŞLETIM sistemi, sistem ve sürücüler) tüm önyükleme zincirinin bütünlük ölçüsünü gerçekleştirerek uzaktan kanıtlamayı mümkün bir şekilde sunar. 

Sanal makinelerinize uzaktan kanıtlama kullanmak için vTPM 'nin etkinleştirilmesini öneririz. VTPM etkinken, bekleyen verileri korumak için tam birim şifrelemesi sağlayan BitLocker işlevselliğini de etkinleştirebilirsiniz. VTPM kullanan tüm özellikler, belirli bir VM 'ye göre gizli dizileri oluşmasına neden olur. Kullanıcılar havuza alınmış bir senaryoda Windows sanal masaüstü hizmetine bağlandıklarında, kullanıcılar konak havuzundaki herhangi bir sanal makineye yeniden yönlendirilebilir. Özelliğin nasıl tasarlandığına bağlı olarak, bu bir etkiye sahip olabilir.

>[!NOTE]
>FSLogix profil verilerinizi depoladığınız belirli diski şifrelemek için BitLocker kullanılmamalıdır.

### <a name="virtualization-based-security"></a>Sanallaştırma tabanlı güvenlik

Sanallaştırma tabanlı güvenlik (VBS), işletim sistemi tarafından erişilemeyen güvenli bir bellek bölgesi oluşturup yalıtmak için hiper yöneticiyi kullanır. Hypervisor-Protected Code Integrity (HVCı) ve Windows Defender Credential Guard her ikisi de güvenlik açıklarından daha fazla koruma sağlamak için VBS 'yi kullanır. 

#### <a name="hypervisor-protected-code-integrity"></a>Hypervisor-Protected kodu bütünlüğü

HVCı, Windows çekirdek modu süreçlerini kötü amaçlı veya doğrulanmamış kodların eklenmesine ve yürütülmesine karşı korumak için, VBS kullanan güçlü bir sistem risk azaltma sürecidir.

#### <a name="windows-defender-credential-guard"></a>Windows Defender Credential Guard

Windows Defender Credential Guard, yalnızca ayrıcalıklı sistem yazılımlarının erişebilmesi için gizli dizileri yalıtmak ve korumak üzere VBS 'yi kullanır. Bu, karma değer geçişi saldırıları gibi bu gizli dizi ve kimlik bilgisi hırsızlığı saldırılarına karşı yetkisiz erişimi engeller.

### <a name="deploy-trusted-launch-in-your-windows-virtual-desktop-environment"></a>Windows sanal masaüstü ortamınızda güvenilen başlatma dağıtma

Windows sanal masaüstü Şu anda konak Havuzu Kurulum işlemi sırasında güvenilen başlatmayı otomatik olarak yapılandırmayı desteklememektedir. Windows sanal masaüstü ortamınızda güvenilir başlatma 'yı kullanmak için, güvenilir başlatmayı normal olarak dağıtmanız ve ardından sanal makineyi istediğiniz konak havuzunuza el ile eklemeniz gerekir.

## <a name="nested-virtualization"></a>İç içe sanallaştırma

Aşağıdaki işletim sistemleri, Windows sanal masaüstü 'nde iç içe sanallaştırmayı çalıştırmayı destekler:

- Windows Server 2016
- Windows Server 2019
- Windows 10 Enterprise
- Windows 10 Enterprise çoklu oturum.

## <a name="next-steps"></a>Sonraki adımlar

Multi-Factor Authentication 'ı nasıl etkinleştireceğinizi öğrenmek için bkz. [Multi-Factor Authentication 'ı ayarlama](set-up-mfa.md).
