---
title: Media şema örnek denetimleri
description: Medya şeması örneklerinin denetim eşlemesi. Her denetim, değerlendirmede yardımcı olan bir veya daha fazla Azure Ilke tanımına eşlenir.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: 4c04c661d7dd8b2b07a05c20524258d6bb887e62
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376707"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Medya şeması örneğinin denetim eşlemesi

Aşağıdaki makalede, Azure şemaları medya şeması 'nın medya denetimleriyle nasıl eşleştiği açıklanır. Denetimler hakkında daha fazla bilgi için bkz. [Media](https://www.motionpictures.org/best-practices).

Aşağıdaki eşlemeler **medya** denetimlerine göre yapılır. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir denetim eşlemesine atlayın. Eşlenmiş denetimlerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Ardından, önizlemeyi bulun ve seçin **\[ \] : medya denetimlerini denetle** yerleşik ilke girişimi.

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ile bir veya daha fazla ilke arasında genellikle bire bir veya tam eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)bakın.

## <a name="access-control"></a>Erişim Denetimi

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1,1-kök erişim anahtarının mevcut olmadığından emin olun

- \[Önizleme \] : güvenilen kökte belirtilen sertifikaları Içermeyen Windows sanal makinelerini denetlemek için önkoşulları dağıtın

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1,2-parolalar, PIN 'Ler ve belirteçlerin korunması gerekir

- \[Önizleme \] : en az parola uzunluğu 14 karakter olan Windows VM 'leri denetlemek için önkoşulları dağıtın

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1,8-paylaşılan hesap erişimi yasaktır

- RootManageSharedAccessKey hariç tüm yetkilendirme kuralları Service Bus ad alanından kaldırılmalıdır

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1,9-sistem, yetkili kullanıcılara erişimi kısıtlamalıdır.

- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1,14-sistem erişim haklarını zorlayamalıdır.

- \[Önizleme \] : ' Kullanıcı hakları ataması ' Içinde Windows VM 'leri yapılandırmasını denetlemek için önkoşulları dağıtın

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1,15-güvenlik ile ilgili bilgi veya işlevlere yetkisiz erişimi engelleyin.

- \[Önizleme \] : ' güvenlik seçenekleri-sistem ayarları 'Nda Windows VM yapılandırmalarından denetim sonuçlarını göster

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21-görevlerin ayrılması uygun rol ataması aracılığıyla zorlanmalıdır.

- [Önizleme \] : Role-Based Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1,40-sistemlerin, güvenilen ağı ve güvenilmeyen ağları aynı anda bağmadığından emin olun.

- \[Önizleme \] : ' güvenlik seçenekleri-ağ erişimi ' Içinde Windows VM 'leri yapılandırmasını denetlemek için önkoşulları dağıtın

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1,42 & AC-1,43-çalışanların yalnızca onaylanan bilgi sistemlerine erişimine izin vermek için uzaktan erişim kısıtlı olmalıdır

- \[Önizleme \] : parola olmadan hesaplardan uzak bağlantılara izin veren Linux VM 'lerinden denetim sonuçlarını göster

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1,50-tüm bilgi sistemi bileşenleri için güvenlikle ilgili olayları günlüğe kaydedin.

- Logic Apps tanılama günlükleri etkinleştirilmelidir

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1,54-Multi-Factor Authentication 'ın (MFA) tüm bulut konsolu kullanıcıları için etkinleştirildiğinden emin olun.

- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir
- Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication (MFA) etkinleştirilmelidir.

## <a name="auditing--logging"></a>& günlüğü denetleme

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2,1-başarılı ve başarısız olaylar günlüğe kaydedilmelidir.

- Arama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2,16-ağ cihazları/örnekleri, bu ağ aygıtı/örneği (ELBs, Web uygulaması güvenlik duvarları vb.) tarafından kritik bir güvenlik olayı olarak sınıflandırılan tüm olayları günlüğe içermemelidir.

- \[Önizleme \] : ' güvenlik seçenekleri-hesaplar ' Içindeki Windows VM yapılandırmalarından denetim sonuçlarını göster

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2,17-sunucular/örnekler, bu sunucu/örnek tarafından kritik bir güvenlik olayı olarak sınıflandırılan tüm olayları günlüğe içermemelidir

- \[Önizleme \] : ' güvenlik seçenekleri-hesaplar ' Içindeki Windows VM yapılandırmalarından denetim sonuçlarını göster

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2,19-etki alanı olayları, etki alanı yönetim yazılımıyla ilgili kritik veya yüksek güvenlik olayı olarak sınıflandırılmış tüm olayları günlüğe içermemelidir

- \[Önizleme \] : ' güvenlik seçenekleri-hesaplar ' Içindeki Windows VM yapılandırmalarından denetim sonuçlarını göster
- \[Önizleme \] : ' güvenlik seçenekleri-Microsoft ağ istemcisi ' Içindeki Windows VM 'leri yapılandırmasını denetlemek için önkoşulları dağıtın

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2,20-etki alanı olayları, etki alanı güvenlik denetimlerine göre kritik güvenlik olayı olarak sınıflandırılmış tüm olayları günlüğe içermemelidir

- \[Önizleme \] : ' güvenlik seçenekleri-hesaplar ' Içindeki Windows VM yapılandırmalarından denetim sonuçlarını göster

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2,21-etki alanı olayları, etki alanı günlüğüne erişimi veya değişiklikleri günlüğe içermemelidir

- \[Önizleme \] : ' güvenlik seçenekleri-Kurtarma Konsolu ' nda Windows VM yapılandırmalarından denetim sonuçlarını göster

## <a name="cryptographic-controls"></a>Şifreleme denetimleri

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4,2-uygulamalar ve sistemler, verileri korumak için geçerli şifreleme çözümlerini kullanmalıdır.

- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir
- Rest verilerini korumak ve uyumluluk gereksinimlerini karşılamak için saydam veri şifrelemesi etkinleştirilmelidir

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4,5-dijital sertifikaların onaylanmış bir sertifika yetkilisi tarafından imzalanması gerekir.

- \[Önizleme \] : belirtilen gün sayısı içinde süresi dolan sertifikaları Içeren Windows VM 'lerinden denetim sonuçlarını göster

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4,6-dijital sertifikaların bir kullanıcı veya cihaza benzersiz olarak atanması gerekir.

- \[Önizleme \] : belirtilen gün sayısı içinde süresi dolan sertifikaları Içeren Windows sanal makinelerini denetlemek için önkoşulları dağıtın

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4,7-kayıt kayıtlarının saklanacağı sürenin uzunluğu için şifre çözme özelliğini etkinleştirmek üzere şifreleme malzemelerinin depolanması gerekir.

- Disk şifrelemesi sanal makinelere uygulanmalıdır
- Etkin disk şifrelemesi olmayan VM 'Ler, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4,8-gizli ve özel anahtarların güvenli bir şekilde depolanması gerekir.

- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir
- Rest verilerini korumak ve uyumluluk gereksinimlerini karşılamak için saydam veri şifrelemesi etkinleştirilmelidir

## <a name="change--config-management"></a>& yapılandırma yönetimini değiştirme

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5,2-yalnızca yetkili kullanıcılar sistemde onaylanan değişiklikleri uygulayabilir.

- Makinelerinize sistem güncelleştirmeleri yüklenmelidir
- Sunucularınızdaki eksik güvenlik sistemi güncelleştirmeleri, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5,12-bilgi sisteminin güncel, eksiksiz, doğru ve kullanıma hazır bir temel yapılandırmasını koruyun.

- Makinelerinize sistem güncelleştirmeleri yüklenmelidir
- Sunucularınızdaki eksik güvenlik sistemi güncelleştirmeleri, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5,13-bilgi sisteminin temel yapılandırmasını sürdürmek için otomatikleştirilmiş araçları kullanmayı sağlar.

- Makinelerinize sistem güncelleştirmeleri yüklenmelidir
- Sunucularınızdaki eksik güvenlik sistemi güncelleştirmeleri, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5,14-gereksiz ve/veya güvenli olmayan işlevleri, bağlantı noktalarını ve Hizmetleri belirleyip devre dışı bırakın.

- Ağ arabirimleri IP iletmeyi devre dışı bırakmalıdır
- \[Önizleme \] : sanal makinenizde IP iletimi devre dışı bırakılmalıdır

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5,19-güvenlik yapılandırma ayarlarındaki değişiklikleri Izleyin.

- Ağ güvenlik grupları için tanılama ayarlarını dağıtma

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5,22-şirket sistemlerine yalnızca yetkili yazılım ve güncelleştirmelerin yüklendiğinden emin olun.

- Makinelerinize sistem güncelleştirmeleri yüklenmelidir
- Sunucularınızdaki eksik güvenlik sistemi güncelleştirmeleri, Azure Güvenlik Merkezi tarafından öneriler olarak izlenir

## <a name="identity--authentication"></a>Kimlik & kimlik doğrulaması

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7,1-Kullanıcı hesaplarının, genel olarak sınıflandırılmayan bilgilere erişmek için bireylere benzersiz olarak atanması gerekir. Hesap kimliklerinin standartlaştırılmış bir mantıksal biçim kullanılarak oluşturulması gerekir.

- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- İzlenmeyen erişimi engellemek için sahip izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir.

## <a name="network-security"></a>Ağ Güvenliği

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9,2-ağ cihazı yönetim işlevselliğine erişim yetkili kullanıcılarla kısıtlıdır.

- \[Önizleme \] : ' güvenlik seçenekleri-ağ erişimi ' Içinde Windows VM 'leri yapılandırmasını denetlemek için önkoşulları dağıtın

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9,3-tüm ağ cihazlarının en güvenli yapılandırmalarına göre yapılandırılması gerekir.

- \[Önizleme \] : ' güvenlik seçenekleri-ağ erişimi ' Içinde Windows VM 'leri yapılandırmasını denetlemek için önkoşulları dağıtın

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9,5-bir güvenlik duvarı üzerinden bir sisteme yapılan tüm ağ bağlantıları, düzenli olarak onaylanmalıdır ve denetlenmelidir.

- \[Önizleme \] : ' Windows Güvenlik Duvarı Özellikleri ' Içindeki Windows VM yapılandırmalarından denetim sonuçlarını göster

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9,7-güvenilen bir ağ ile güvenilmeyen veya genel ağ arasındaki herhangi bir sınırında uygun denetimler bulunmalıdır.

- \[Önizleme \] : ' Windows Güvenlik Duvarı Özellikleri ' Içindeki Windows VM 'leri yapılandırmasını denetlemek için önkoşulları dağıtın

## <a name="security-planning"></a>Güvenlik Planlama

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11,3-tehditler, şirket bilgilerinin ve içeriğin gizliliğini, bütünlüğünü veya kullanılabilirliğini, oluşumlarının oluşma olasılığını olumsuz yönde etkileyebilecek tehditler tanımlanmalıdır.

- Gelişmiş tehdit koruması türleri SQL yönetilen örneği gelişmiş veri güvenliği ayarları içinde ' All ' olarak ayarlanmalıdır

### <a name="security-continuity"></a>Güvenlik sürekliliği

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12,5-uzun süreli depolamada bulunan verilere, bekletme süresi boyunca erişilebilir ve medya düşüşü ve teknoloji değişikliklerine karşı koruma verilmelidir.

- SQL Server 'lar 90 günden daha uzun denetim bekletme günlerindeki şekilde yapılandırılmalıdır.
- 90 günden daha az bir denetim saklama süresi ile yapılandırılmış SQL sunucularını denetleyin.

## <a name="system-integrity"></a>Sistem bütünlüğü

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14,3-yalnızca yetkili personel ağ ve Kullanıcı etkinliklerini izleyebilir.

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- İzleme güvenlik açığı değerlendirmesi tarama sonuçları ve veritabanı güvenlik açıklarının nasıl düzeltileceğine ilişkin öneriler.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>Sı-14,4-Internet 'e yönelik sistemlerde yetkisiz giriş algılama yapılmalıdır.

- SQL Server 'lar üzerinde tehdit algılamayı dağıtma

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14,13-standartlaştırılmış merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı şirket genelinde uygulanmalıdır.

- Windows Server için varsayılan Microsoft ıaasantimalware uzantısını dağıt

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>Sı-14,14-kötü amaçlı yazılımdan koruma yazılımı bilgisayarları ve medyayı haftalık olarak en az taramalıdır.

- Windows Server için varsayılan Microsoft ıaasantimalware uzantısını dağıt

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15,4-uygulamaların aylık olarak güvenlik açıkları için tarandığından emin olun.

- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15,5-güvenlik açıklarının tanımlandığına, tehditlere karşı eşleştirilmesine ve risk için değerlendirildiğinden emin olun.

- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15,6-belirlenen güvenlik açıklarının, zaman çizelgesinde karşılıklı olarak kabul edilen bir içinde düzeltilmiş olduğundan emin olun.

- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15,7-güvenlik açığı yönetim sistemlerinin erişimine ve kullanımına erişimi yetkili personelle sınırlandırılmalıdır.

- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Saldırılara karşı korumak için sanal makine ölçek kümelerinizin üzerindeki işletim sistemi güvenlik açıklarını denetleyin.

> [!NOTE]
> Belirli Azure Ilke tanımlarının kullanılabilirliği, Azure Kamu ve diğer ulusal bulutlarda farklılık gösterebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Media şema örneğinin denetim eşlemesini gözden geçirdiniz. Daha sonra, genel bakış ve bu örneği dağıtma hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="next step action"]
> [Medya şeması-genel bakış](./control-mapping.md) 
>  [Media şema-Deploy adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
