---
title: Güvenli, Azure tarafından yönetilen iş istasyonlarını anlama-Azure Active Directory
description: Güvenli, Azure tarafından yönetilen iş istasyonları hakkında bilgi edinin ve neden önemli olduğunu anlayın.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2abc5434f11bf00c6872775b1336694c04972e95
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200224"
---
# <a name="understand-secure-azure-managed-workstations"></a>Güvenli, Azure tarafından yönetilen iş istasyonlarını anlayın

Güvenli, yalıtılmış iş istasyonları Yöneticiler, geliştiriciler ve kritik hizmet işleçleri gibi hassas rollerin güvenliği açısından kritik öneme sahiptir. İstemci iş istasyonu güvenliği tehlikeye girerse, pek çok güvenlik denetimi ve güvenme başarısız olabilir veya etkisiz olabilir.

Bu belgede, genellikle ayrıcalıklı erişim iş istasyonu (PAW) olarak bilinen güvenli bir iş istasyonu oluşturmak için gerekenler açıklanmaktadır. Makalede, ilk güvenlik denetimlerini ayarlamaya yönelik ayrıntılı yönergeler de bulunur. Bu kılavuzda, bulut tabanlı teknolojinin hizmeti nasıl yönetebileceğini açıklanmaktadır. Windows 10 RS5, Microsoft Defender Gelişmiş tehdit koruması (ATP), Azure Active Directory ve Intune 'da sunulan güvenlik yeteneklerini kullanır.

> [!NOTE]
> Bu makalede, güvenli bir iş istasyonu kavramı ve önemi açıklanmaktadır. Kavramı zaten biliyor ve dağıtıma atlamak istiyorsanız, [güvenli bir Iş Istasyonu dağıtma](howto-azure-managed-workstation.md)makalesini ziyaret edin.

## <a name="why-secure-workstation-access-is-important"></a>Güvenli iş istasyonu erişiminin neden önemli olduğu

Bulut hizmetlerini hızlı benimseme ve her yerden çalışma yeteneği yeni bir yararlanma yöntemi oluşturdu. Yöneticilerin çalıştığı cihazlarda zayıf güvenlik denetimlerinden yararlanarak saldırganlar ayrıcalıklı kaynaklara erişim elde edebilir.

Ayrıcalıklı kötüye kullanımı ve tedarik zinciri saldırıları, saldırganların kuruluşları ihlal etmek için kullandığı ilk beş yöntem arasındadır. Bunlar ayrıca, [Verizon tehdit raporuna](https://enterprise.verizon.com/resources/reports/dbir/)ve [güvenlik zekası raporuna](https://aka.ms/sir)göre 2018 ' de raporlanan olaylarda, en yaygın olarak algılanan tackler ' ı da sunuyoruz.

Çoğu saldırganlar şu adımları izler:

1. Keşif, genellikle sektöre özgü bir yöntem bulmak için.
1. Bilgi toplamak ve düşük değer olarak algılanan bir iş istasyonuna giriş yapmanın en iyi yolunu belirlemek için analiz.
1. [Geçici](https://en.wikipedia.org/wiki/Network_Lateral_Movement)olarak hareket eden bir yolu aramak için kalıcılık.
1. Gizli ve hassas verilerin çıkarılması.

Keşif sırasında saldırganlar, düşük riskli veya az değerli olan cihazları sıklıkla taşır. Bu güvenlik açığı bulunan cihazları, yan yana hareket için bir fırsat bulmak ve yönetici kullanıcıları ve cihazları bulmak için kullanırlar. Ayrıcalıklı kullanıcı rollerine erişim kazandıktan sonra saldırganlar yüksek değerli verileri belirler ve bu verileri başarıyla elde edebilir.

![Tipik bir uzlaşması deseninin](./media/concept-azure-managed-workstation/typical-timeline.png)

Bu belgede, bilgi işlem cihazlarınızı bu tür yan yana saldırılara karşı korumaya yardımcı olabilecek bir çözüm açıklanmaktadır. Çözüm, daha az değerli üretkenlik cihazlarından yönetim ve Hizmetleri yalıtır ve bu, hassas bulut kaynaklarına erişimi olan cihazdan önce zinciri boztılabilir. Çözüm, Microsoft 365 Kurumsal yığınının parçası olan yerel Azure hizmetlerini kullanır:

* Cihaz yönetimi için Intune ve uygulamalar ile URL 'Lerin güvenli listesi
* Cihaz kurulumu, dağıtım ve yenileme için Autopilot
* Kullanıcı yönetimi, koşullu erişim ve çok faktörlü kimlik doğrulaması için Azure AD
* Cihaz sistem durumu kanıtlama ve Kullanıcı deneyimi için Windows 10 (geçerli sürüm)
* Bulut tarafından yönetilen Endpoint Protection, algılama ve yanıt için Defender ATP
* Yetkilendirmeyi yönetmek için Azure AD PıM ve kaynaklara yönelik tam zamanında (JıT) ayrıcalıklı erişim

## <a name="who-benefits-from-a-secure-workstation"></a>Güvenli bir iş istasyonundan kimler yarar?

Tüm kullanıcılar ve işleçler, güvenli bir iş istasyonu kullanırken faydalanır. Bir BILGISAYARı veya cihazı kapatan bir saldırgan, önbelleğe alınmış tüm hesapların kimliğine bürünebilir. Cihazda oturum açıldığında, kimlik bilgilerini ve belirteçleri de kullanabilirler. Bu risk, yönetim hakları dahil olmak üzere ayrıcalıklı roller için kullanılan cihazların güvenliğini sağlamayı önemli hale getirir. Ayrıcalıklı hesaplara sahip cihazlar, yan yana taşıma ve ayrıcalık yükseltme saldırıları için hedeflerdir. Bu hesaplar, aşağıdaki gibi çeşitli varlıklar için kullanılabilir:

* Şirket içi veya bulut tabanlı sistemlerin Yöneticisi
* Kritik sistemler için geliştirici iş istasyonu
* Yüksek pozlama ile sosyal medya hesabı Yöneticisi
* SWIFT ödeme terminali gibi son derece hassas iş istasyonu
* İş istasyonu ticari gizli dizileri işliyor

Riski azaltmak için, bu hesapları kullanan ayrıcalıklı iş istasyonları için yükseltilmiş güvenlik denetimleri uygulamalısınız. Daha fazla bilgi için bkz. [Azure Active Directory özelliği dağıtım kılavuzu](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [Office 365 yol haritası](https://aka.ms/o365secroadmap)ve [ayrıcalıklı erişim yol haritasını güvenlik](https://aka.ms/sparoadmap).

## <a name="why-use-dedicated-workstations"></a>Adanmış iş istasyonlarını neden kullanmalısınız?

Mevcut bir cihaza güvenlik eklemek mümkün olsa da, güvenli bir temel ile başlamak daha iyidir. Kuruluşunuzun yüksek bir güvenlik düzeyini korumak için en iyi konuma koymak üzere güvenli olduğunu bildiğiniz bir cihazla başlayın ve bilinen bir güvenlik denetimi kümesi uygulayın.

E-posta ve Web 'e göz atma aracılığıyla büyüyen çok sayıda saldırı vektörü, bir cihazın güvenilir olmasını zorlaştırır. Bu kılavuzda, adanmış bir iş istasyonunun standart üretkenlik, göz atma ve e-posta ile yalıtılmış olduğu varsayılır. Bir cihazdan üretkenlik, Web 'e göz atma ve e-postaların kaldırılması üretkenlik üzerinde olumsuz bir etkiye sahip olabilir. Bununla birlikte, bu koruma genellikle iş görevlerinin açıkça gerektirmeyen senaryolar için kabul edilebilir ve güvenlik olayının riski yüksek olur.

> [!NOTE]
> Burada Web 'e göz atma, yüksek riskli bir etkinlik olabilen rastgele Web sitelerine genel erişime başvurur. Bu tür göz atma, Azure, Office 365, diğer bulut sağlayıcıları ve SaaS uygulamaları gibi hizmetler için çok sayıda iyi bilinen Yönetim Web sitesine erişmek üzere bir Web tarayıcısı kullanmaktan oldukça farklıdır.

Kapsama stratejileri, bir saldırganın hassas varlıklara erişim sağlamasını yapan denetimlerin sayısını ve türünü artırarak güvenliği güçlendir. Bu makalede açıklanan model katmanlı ayrıcalık tasarımını kullanır ve yönetim ayrıcalıklarını belirli cihazlarla kısıtlar.

## <a name="supply-chain-management"></a>Tedarik zinciri yönetimi

Güvenli bir iş istasyonu için temel, ' güven kökü ' adlı güvenilir bir iş istasyonu kullandığınız bir tedarik zinciri çözümüdür. Bu çözüm için, güven kökü [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) teknolojisini kullanır. Bir iş istasyonunun güvenliğini sağlamak için, Autopilot Microsoft OEM ile iyileştirilmiş Windows 10 cihazlarından yararlanmanızı sağlar. Bu cihazlar, üreticiden bilinen iyi bir durumda gelir. Autopilot, güvenli olmayabilecek bir cihazı yeniden Imaging yerine bir Windows cihazını "iş için kullanılabilir" durumuna dönüştürebilir. Ayarlar ve ilkeler uygular, uygulamalar yüklenir ve hatta Windows 10 sürümünü değiştirir. Örneğin, Autopilot, bir cihazın Windows yüklemesini Windows 10 Pro 'dan Windows 10 Enterprise 'a değiştirerek gelişmiş özellikleri kullanabilir.

![Güvenli iş istasyonu düzeyleri](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Cihaz rolleri ve profilleri

Bu kılavuzda, kullanıcılar, geliştiriciler ve BT personeli için daha güvenli çözümler oluşturmanıza yardımcı olabilecek birkaç güvenlik profili ve rolü başvuruyordur. Bu profiller, gelişmiş veya güvenli bir iş istasyonundan faydalanabilecek ortak kullanıcılara yönelik kullanılabilirliği ve riskleri dengeleyebilir. Burada sunulan ayarların yapılandırması, sektörde kabul edilen standartları temel alır. Bu kılavuzda, Windows 10 ' un nasıl zor yapılacağı ve cihaz ya da Kullanıcı güvenliğinin aşılmasına ilişkin riskleri azaltma gösterilmektedir. Bu, güvenlik özelliklerinin ve risklerinin yönetilmesine yardımcı olmak üzere ilke ve teknolojiyi kullanarak bunu yapar.
Güvenli iş istasyonu seviyelerini ![](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Düşük güvenlik** – yönetilen, standart bir iş istasyonu çoğu ev ve küçük işletme kullanımı için iyi bir başlangıç noktası sağlar. Bu cihazlar Azure AD 'ye kaydedilir ve Intune ile yönetilir. Bu profil kullanıcıların herhangi bir uygulamayı çalıştırmasına ve herhangi bir Web sitesine gözatmasına izin verir. [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) gibi kötü amaçlı yazılımdan koruma çözümü etkinleştirilmelidir.

* **Gelişmiş güvenlik** – bu giriş düzeyi, korunan çözüm ev kullanıcıları, küçük işletme kullanıcıları ve genel geliştiriciler için uygundur.

   Gelişmiş iş istasyonu, düşük Güvenlik profilinin güvenliğini artırmanın ilke tabanlı bir yoludur. Ayrıca, e-posta ve Web 'e göz atma gibi üretkenlik araçlarını kullanırken müşteri verileriyle çalışmak için güvenli bir yol sunar. Kullanıcı davranışı ve profil kullanımı için gelişmiş bir iş istasyonunu izlemek üzere denetim ilkelerini ve Intune 'U kullanabilirsiniz. Geliştirilmiş iş istasyonu profilini Windows10 (1809) betiğine dağıtırsınız ve gelişmiş [tehdit koruması (ATP)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)kullanan gelişmiş kötü amaçlı yazılımdan koruma özelliğinden yararlanır.

* **Yüksek güvenlik** : bir iş istasyonunun saldırı yüzeyini azaltmanın en etkili yolu, iş istasyonunun kendini yönetme özelliğini kaldırmaktır. Yerel yönetim haklarının kaldırılması güvenliği artıran bir adımdır, ancak yanlış uygulanırsa üretkenliği etkileyebilir. Yüksek güvenlik profili, gelişmiş güvenlik profilinde önemli bir değişiklik ile oluşturulur: yerel yöneticinin kaldırılması. Bu profil, yüksek profilli kullanıcılar için tasarlanmıştır: Yöneticiler, bordro ve hassas veri kullanıcıları, hizmet ve süreçlerle ilgili onaylayanlar.

   Yüksek güvenlik kullanıcısı, kullanımı kolay bir deneyimde e-posta ve Web 'e göz atma gibi etkinlikleri yapabilirken daha denetimli bir ortam talep eder. Kullanıcılar tanımlama bilgileri, Sık Kullanılanlar ve diğer kısayolların çalışması gibi özellikler bekler. Ancak, bu kullanıcılar cihazlarını değiştirme veya hatalarını ayıklama özelliği gerektirmez. Ayrıca, sürücü yüklemesi gerekmez. Yüksek güvenlik profili, High Security-Windows10 (1809) betiği kullanılarak dağıtılır.

* **Özel** – saldırganlar, SALDıRGANLAR ve BT yöneticileri, saldırganların ilgisini çeken sistemleri değiştirebilecek şekilde hedefleyebilir. Özelleştirilmiş iş istasyonu, yerel uygulamaları yöneterek ve Web sitelerini sınırlayarak yüksek güvenlik iş istasyonunun ilkelerine genişletilir. Ayrıca, ActiveX, Java, tarayıcı eklentileri ve diğer Windows denetimleri gibi yüksek riskli üretkenlik yeteneklerini de kısıtlar. Bu profili DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline betiği ile dağıtabilirsiniz.

* **Güvenli** – bir yönetim hesabını kapatan bir saldırgan, veri hırsızlığı, veri değişikliği veya hizmet kesintisi nedeniyle önemli ölçüde iş hasarı oluşmasına neden olabilir. Bu sağlamlaştırılmış durumda, iş istasyonu yerel uygulama yönetiminin doğrudan denetimini kısıtlayan tüm güvenlik denetimlerini ve ilkelerini sunar. Güvenli bir iş istasyonunun üretkenlik araçları yoktur, bu nedenle Cihazın güvenliğinin aşılmasına daha zordur. Sızdırma saldırıları için en yaygın vektörü engeller: e-posta ve sosyal medya.  Güvenli iş istasyonu, Secure Workstation-Windows10 (1809) SecurityBaseline betiği ile dağıtılabilir.

   ![Güvenli iş istasyonu](./media/concept-azure-managed-workstation/secure-workstation.png)

   Güvenli bir iş istasyonu, açık uygulama denetimi ve Application Guard 'ı olan sağlamlaştırılmış bir iş istasyonu sunan bir yönetici sağlar. İş istasyonu, konak kötü davranışından korumak için Credential Guard, Device Guard ve Exploit Guard kullanır. Tüm yerel diskler de BitLocker ile şifrelenir.

* **Yalıtılmış** – bu özel, çevrimdışı senaryo, tayfın aşırı sonunu temsil eder. Bu durum için yükleme betikleri sağlanmaz. Desteklenmeyen veya yüklenmemiş eski bir işletim sistemi gerektiren, iş açısından kritik bir işlevi yönetmeniz gerekebilir. Örneğin, yüksek değerli bir üretim satırı veya yaşam destek sistemi. Güvenlik kritik ve bulut hizmetleri kullanılamadığından, bu bilgisayarları el ile veya Gelişmiş Güvenlik yönetici ortamı (ESAE) gibi yalıtılmış bir Active Directory orman mimarisiyle yönetebilir ve güncelleştirebilirsiniz. Bu koşullarda, temel Intune ve ATP sistem durumu denetimleri hariç tüm erişimi kaldırmayı göz önünde bulundurun.

  * [Intune ağ iletişimleri gereksinimi](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [ATP ağ iletişimleri gereksinimi](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Sonraki adımlar

[Güvenli bir Azure tarafından yönetilen iş Istasyonu dağıtın](howto-azure-managed-workstation.md).
