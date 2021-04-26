---
title: Avustralya kamu ile korunan şema örnek denetimleri
description: Avustralya kamu ile korunan şema örneğinin denetim eşlemesi. Her denetim, değerlendirmede yardımcı olan bir veya daha fazla Azure Ilke tanımına eşlenir.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: ecb6a912d0b1f319022f6b7deff68e1495dccbca
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386458"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Avustralya kamu ile korunan şema örneğine yönelik denetim eşlemesi

Aşağıdaki makalede, Azure şemaları Avustralya kamu ile korunan şema örnek haritalarının, ISM korumalı denetimlerle nasıl yapılacağı açıklanır. Denetimler hakkında daha fazla bilgi için bkz. [ıSM korumalı](https://www.cyber.gov.au/ism).

Aşağıdaki eşlemeler, **ıSM korumalı** denetimlerine göre yapılır. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir denetim eşlemesine atlayın. Eşlenmiş denetimlerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Ardından, önizlemeyi bulup seçin **\[ \] : Avustralya kamu ile korunan denetimleri denetleme ve belirli VM uzantılarını, denetim gereksinimleri** yerleşik ilke girişimi ' ni destekleyecek şekilde dağıtma.

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ile bir veya daha fazla ilke arasında genellikle bire bir veya tam eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md)bakın.


## <a name="location-constraints"></a>Konum kısıtlamaları

Bu şema, aşağıdaki Azure Ilke tanımlarını atayarak tüm kaynakların ve kaynak grupların "Avustralya Orta", "Avustralya Central2", "Avustralya Doğu" ve "Avustralya Güneydoğu" ile dağıtım konumunu kısıtlamanıza yardımcı olur:

- İzin verilen konumlar ("Avustralya Orta", "Avustralya Central2", "Avustralya Doğu" ve "Avustralya Güneydoğu" için sabit olarak kodlanmıştır)
- Kaynak grupları için izin verilen konumlar ("Avustralya Orta", "Avustralya Central2", "Avustralya Doğu" ve "Avustralya Güneydoğu" için sabit olarak kodlanmıştır)

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Personel güvenlik-sistemlere ve kaynaklarına erişim için yönergeler

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 personeli bir sisteme erişim izni verdi ve kaynakları benzersiz şekilde tanımlanabilir

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir
- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 sistemlere standart erişim, uygulamalar ve veri depoları, personelin görevlerini üstlerinin yapması için gerekli olan ile sınırlıdır

- Aboneliğiniz için en fazla 3 sahip belirtilmelidir
- Aboneliğinize birden fazla sahip atanmalıdır
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows VM 'lerinden denetim sonuçlarını göster
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows sanal makinelerini denetlemek için önkoşulları dağıtın

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 ayrıcalıklı erişim, uygulamalar ve veri depoları, ilk kez isteniyorsa ve bir yıllık ya da daha sık aralıklarla yeniden doğrulandığında onaylanır

- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows VM 'lerinden denetim sonuçlarını göster
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows sanal makinelerini denetlemek için önkoşulları dağıtın

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508, sistemlere ayrıcalıklı erişim, uygulamalar ve veri depoları, personelin görevlerini üstlerinin yapması için gerekli olan ile sınırlıdır

- Aboneliğiniz için en fazla 3 sahip belirtilmelidir
- Aboneliğinize birden fazla sahip atanmalıdır
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows VM 'lerinden denetim sonuçlarını göster
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows sanal makinelerini denetlemek için önkoşulları dağıtın
- Sanal makinelere anlık ağ erişim denetimi uygulanmalıdır

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 paylaşılan kullanıcı hesaplarının kullanımı kesinlikle denetlenir ve bu hesaplar kullanılarak personel benzersiz bir şekilde tanımlanabilir

- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows VM 'lerinden denetim sonuçlarını göster
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows sanal makinelerini denetlemek için önkoşulları dağıtın

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 ayrıcalıklı kullanıcılara yalnızca ayrıcalıklı erişim gerektiren görevler için kullanılmak üzere adanmış bir ayrıcalıklı hesap atanır

- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows VM 'lerinden denetim sonuçlarını göster
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows sanal makinelerini denetlemek için önkoşulları dağıtın

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 sistemler, uygulamalar ve veri depoları erişimi, aynı günde artık erişim için meşru bir gereksinime sahip olmayan şekilde kaldırılır veya askıya alınır

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 personele bir sisteme geçici erişim verildiğinde, bu kişilerin yalnızca görevlerini yerine getirmek için gereken bilgilere erişimini kısıtlamak üzere etkin güvenlik denetimleri konur

- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Sistem sağlamlaştırma için yönergeler-Işletim sistemi sağlamlaştırma

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 bir işletim sisteminin en son sürümü (N) veya N-1 sürümü standart Işletim ortamları (SOEs) için kullanılır

- Makinelerinize sistem güncelleştirmeleri yüklenmelidir
- Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 gereksiz işletim sistemi hesabı, yazılım, bileşen, hizmet ve işlevsellik kaldırılmış veya devre dışı bırakılmış

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 uygulamaların, yazılım kitaplıklarının, betiklerin ve yükleyicilerin onaylanan bir küme üzerinde yürütülmesini kısıtlamak için tüm sunucularda uygulama beyaz listesi çözümü uygulandı

- Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 virüsten koruma yazılımı iş istasyonlarında ve sunucularda uygulanır ve ile yapılandırılır: imza tabanlı algılama etkin ve yüksek düzey, buluşsal-tabanlı bir algılama etkin ve yüksek bir düzeye ayarlanmış, bir para birimi için denetlenen ve en az bir günlük, otomatik ve normal tarama, tüm sabit diskler ve çıkarılabilir ortamlar için yapılandırılmış

- Microsoft ıaasantimalware uzantısı Windows Server 'lar üzerinde dağıtılmalıdır
- Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir
- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Sistem sağlamlaştırma için yönergeler-kimlik doğrulama sağlamlaştırma

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 Kullanıcı, bir sisteme ve kaynaklarına erişim verilmeden önce kimlik doğrulamasından ulaştı

- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme
- Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır
- Parolasız uzak bağlantılara izin veren Linux VM 'lerinden denetim sonuçlarını göster
- Parola olmadan hesaplardan uzak bağlantılara izin veren Linux VM 'lerini denetlemek için önkoşulları dağıtın
- Parolası olmayan hesaplara sahip Linux VM 'lerinden denetim sonuçlarını göster
- Parolası olmayan hesaplara sahip Linux sanal makinelerini denetlemek için önkoşulları dağıtın

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 çok faktörlü kimlik doğrulaması, standart kullanıcıların kimliğini doğrulamak için kullanılır

- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 Multi-Factor Authentication tüm ayrıcalıklı kullanıcılara ve diğer güven konumlarına yönelik kimlik doğrulaması için kullanılır

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 tek faktörlü kimlik doğrulaması için kullanılan parola, en az 4 Rastgele kelime olacak şekilde karmaşıklıkla birlikte 14 karakterdir

- ' Güvenlik ayarları-hesap Ilkeleri ' içindeki Windows VM yapılandırmalarından denetim sonuçlarını göster
- ' Güvenlik ayarları-hesap Ilkeleri ' içindeki Windows VM 'Leri yapılandırmasını denetlemek için önkoşulları dağıtın

## <a name="guidelines-for-system-management---system-administration"></a>Sistem yönetimi için yönergeler-sistem yönetimi

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 çok faktörlü kimlik doğrulaması ayrıcalıklı eylemler gerçekleştirirken kullanıcıların kimliğini doğrulamak için kullanılır

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir
- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 yönetim trafiğinin yalnızca sistemleri ve uygulamaları yönetmek için kullanılan ağ bölgelerinden kaynaklı olmasına izin verilir

- Sanal makinelere anlık ağ erişim denetimi uygulanmalıdır
- API Apps için uzaktan hata ayıklama kapatılmalıdır
- Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır
- Web uygulamaları için uzaktan hata ayıklama kapatılmalıdır

## <a name="guidelines-for-system-management---system-patching"></a>Sistem yönetimi için yönergeler-sistem düzeltme eki uygulama

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 çok fazla risk olarak değerlendirilen uygulama ve sürücülerdeki güvenlik açıkları, satıcılar, bağımsız üçüncü taraflar, sistem yöneticileri veya kullanıcılar tarafından tanımlanan güvenlik açıklarının 48 saat içinde düzeltme eki uygulanmış, güncelleştirilir veya azaltılmıştır

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Güvenlik açığı değerlendirmesi makinelerde etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir
- SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 uygulamalarda ve sürücülerde yüksek riskli olarak değerlendirilen güvenlik açıkları, satıcılar, bağımsız üçüncü taraflar, sistem yöneticileri veya kullanıcılar tarafından tanımlanan güvenlik açığının iki haftası içinde düzeltme eki uygulanmış, güncelleştirilir veya azaltılmıştır

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir
- SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472, uygulamalar ve sürücülerdeki orta veya düşük riskli olarak değerlendirilen güvenlik açıkları, satıcılar, bağımsız üçüncü taraflar, sistem yöneticileri veya kullanıcılar tarafından tanımlanan güvenlik açığının bir ayda düzeltme eki uygulanmış, güncelleştirilir veya azaltılmıştır

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir
- SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 işletim sistemlerinde ve bellenim 'da çok fazla risk olarak değerlendirilen güvenlik açıkları, tedarikçiler, bağımsız üçüncü taraflar, sistem yöneticileri veya kullanıcılar tarafından tanımlanmakta olan güvenlik açıklarının 48 saat içinde düzeltme eki uygulanmış, güncelleştirilir veya azaltılmıştır

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir
- SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 işletim sistemlerinde ve bellenim 'da yüksek riskli olarak değerlendirilen güvenlik açıkları, tedarikçiler, bağımsız üçüncü taraflar, sistem yöneticileri veya kullanıcılar tarafından tanımlanan güvenlik açığının iki haftası içinde düzeltme eki uygulanmış, güncelleştirilir veya azaltılmıştır

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir
- SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 işletim sistemlerinde ve bellenim 'da orta veya düşük riskli olarak değerlendirilen güvenlik açıkları, satıcılar, bağımsız üçüncü taraflar, sistem yöneticileri veya kullanıcılar tarafından tanımlanan güvenlik açığının bir ayda düzeltme eki uygulanmış, güncelleştirilmiş veya hafifletildır

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir
- SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Sistem yönetimi için yönergeler-veri yedekleme ve geri yükleme

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>önemli bilgilerin, yazılım ve yapılandırma ayarlarının 1511 yedekleri en az günlük olarak gerçekleştirilir

- Olağanüstü durum kurtarma yapılandırması olmadan sanal makineleri denetleme

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Sistem Izleme için yönergeler-olay günlüğü ve denetimi

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 bir denetleyebileceği Logging özelliği uygulandı ve sistemler olay günlüklerini her bir olay oluştuktan sonra mümkün olan en kısa sürede denetleyebileceği günlüğü tesisine kaydetmek üzere yapılandırılmıştır

- Azure aboneliklerinin etkinlik günlüğü için bir günlük profili olmalıdır

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 işletim sistemleri için aşağıdaki olaylar günlüğe kaydedilir: önemli veri ve işlemlere, uygulama kilitlenmelerine ve hata iletilerine yönelik erişim, özel ayrıcalıklar kullanma girişimleri, hesap değişiklikleri, güvenlik ilkesinde yapılan değişiklikler, sistem yapılandırmalarında yapılan değişiklikler, etki alanı adı sistemi (DNS) ve Köprü Metni Aktarım Protokolü (HTTP) istekleri, veri ve sistem kaynaklarına erişmeye yönelik başarısız denemeler, sistem başlatma ve kapatmalar , dış medyaya, Kullanıcı veya grup yönetimine veri aktarımı, özel ayrıcalıkların kullanımı

- \[Önizleme \] : denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- VMSS-VM görüntüsündeki (OS) denetim Log Analytics Aracısı dağıtımı listelenmemiş
- VM için Log Analytics çalışma alanını denetleme-rapor uyumsuzluğu
- Tanılama ayarını denetle

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 şu olaylar günlüğe kaydedilir: özellikle önemli bilgilere, yeni kullanıcıların eklenmesine, özellikle ayrıcalıklı kullanıcılara, açıklama içeren herhangi bir sorguya, birden çok katıştırılmış sorgu içeren herhangi bir sorguya, herhangi bir sorgu ya da veritabanına uyarı veya başarısızlık, ayrıcalıkların yükseltilme girişimleri, veritabanı yapısında yapılan değişiklikler, Kullanıcı rollerinde veya veritabanı izinlerinde yapılan değişiklikler, veritabanı yönetici eylemleri , veritabanı oturum açmaları ve günlüğe kaydetme, verilerde yapılan değişiklikler, yürütülebilir komutların kullanımı

- Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir
- Tanılama ayarını denetle
- Gelişmiş veri güvenliği, SQL yönetilen örneklerinizin üzerinde etkinleştirilmelidir

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Sistem Izleme için yönergeler-güvenlik açığı yönetimi

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 güvenlik açığı değerlendirmeleri ve sızma testleri, sistem dağıtılmadan önce uygun şekilde nitelikli personel tarafından, bir sistemde önemli bir değişiklikten sonra ve en az yıllık olarak veya sistem sahibi tarafından belirtilen şekilde yönetilir

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Güvenlik açığı değerlendirmesi sanal makinelerde etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Kapsayıcı güvenlik yapılandırmalarında güvenlik açıkları düzeltilmelidir

## <a name="guidelines-for-database-systems-management---database-servers"></a>Veritabanı sistemleri yönetimi için yönergeler-veritabanı sunucuları

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 sabit disk veritabanı sunucularının tam disk şifrelemesi kullanılarak şifrelenmesi

- Disk şifrelemesi sanal makinelere uygulanmalıdır
- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 veritabanı sunucuları ve Web uygulamaları arasında iletilen bilgiler şifrelenir

- Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir
- Depolama hesaplarına güvenli aktarım etkinleştirilmelidir
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularından denetim sonuçlarını göster
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularını denetlemek için önkoşulları dağıtma

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Veritabanı sistemleri yönetimi için yönergeler-veritabanı yönetim sistemi yazılımı

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 Varsayılan veritabanı yönetici hesapları devre dışı, yeniden adlandırılmış veya parolaları değişmiş

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 veritabanı yöneticileri benzersiz ve tanımlanabilir hesaplara sahip

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 veritabanı yönetici hesapları farklı veritabanları arasında paylaşılmıyor

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 veritabanı yönetici hesapları, veritabanı ile genel amaçlı etkileşimler için kullanılan standart veritabanı hesaplarıyla birlikte yalnızca yönetim görevleri için kullanılır

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 veritabanı yöneticisi erişimi, varsayılan yönetim izinlerine sahip hesaplar yerine tanımlanmış rollerle kısıtlıdır veya tüm izinler

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Şifreleme kullanımıyla ilgili yönergeler-şifreleme temelleri

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459, bekleyen veriler için kullanılan şifreleme yazılımı tam disk şifrelemesi veya erişim denetimlerinin yalnızca şifrelenmiş bölüme yazmaya izin vermeyeceği kısmi şifrelemeyi uygular

- Disk şifrelemesi sanal makinelere uygulanmalıdır

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Şifreleme-Aktarım Katmanı Güvenliği kullanma yönergeleri

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 yalnızca en son TLS sürümü kullanılıyor

- API uygulamanızda en son TLS sürümü kullanılmalıdır
- Web uygulamanızda en son TLS sürümü kullanılmalıdır
- İşlev Uygulaması en son TLS sürümü kullanılmalıdır
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularını denetlemek için önkoşulları dağıtma
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularından denetim sonuçlarını göster

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Veri aktarımları ve Içerik filtreleme için yönergeler-Içerik filtreleme

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 virüsten koruma taraması, birden çok farklı tarama altyapısı kullanılarak tüm içerikte gerçekleştiriliyor

- Microsoft ıaasantimalware uzantısı Windows Server 'lar üzerinde dağıtılmalıdır
- Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir
- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Veri aktarımları ve Içerik filtreleme için yönergeler-Web uygulaması geliştirme

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 tüm Web uygulaması içeriği özel olarak HTTPS kullanılarak sunuluyor

- İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 Web uygulamaları ve kullanıcıların korunmasına yardımcı olmak için Web uygulamaları için Web tarayıcısı tabanlı güvenlik denetimleri uygulanır

- CORS, her kaynağın Web uygulamalarınıza erişmesine izin vermemelidir

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Ağ yönetimi için yönergeler-ağ tasarımı ve yapılandırma

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 ağ erişim denetimleri, yetkili olmayan ağ cihazlarının bağlantısını engellemek için ağlarda uygulanır

- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 ağ erişim denetimleri, ağ kesimleri içinde ve arasında trafiği yalnızca iş amaçları için gerekli olanlarla sınırlamak üzere uygulanır

- Internet 'e yönelik sanal makineler ağ güvenlik gruplarıyla korunmalıdır
- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme
- Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Çevrimiçi hizmetler için ağ yönetimi için yönergeler-hizmet sürekliliği

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 hizmet reddi saldırısı önleme ve risk azaltma stratejileri, hizmet sağlayıcılarıyla birlikte ele alınmıştır, özellikle de hizmet reddi saldırılarına karşı kapasitesi, hizmet reddi saldırılarından kaynaklanan müşteriler, müşterilere bildirimde bulunmak veya hizmet reddi saldırıları sırasında çevrimiçi hizmetler kapatmak için eşikler, ön uç trafiği mümkün olduğunca yukarı akış olarak engellemek için yukarı akış sağlayıcıları ile hizmet reddi saldırıları önlemeye yönelik eşikler, önceden onaylanmış olan tüm ücretler

- DDoS koruma standardı etkinleştirilmelidir


> [!NOTE]
> Belirli Azure Ilke tanımlarının kullanılabilirliği, Azure Kamu ve diğer ulusal bulutlarda farklılık gösterebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

> [!div class="nextstepaction"]
> [ISM korumalı şema-genel bakış](./index.md) 
>  [ISM korumalı şema-Deploy adımları](./deploy.md)

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
