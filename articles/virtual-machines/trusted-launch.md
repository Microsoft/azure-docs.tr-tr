---
title: "Önizleme: Azure VM 'Leri için güvenilir başlatma"
description: Azure sanal makineler için güvenilir başlatma hakkında bilgi edinin.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 5e500eca601d21f106dbe31236e9b5c2aa76b0d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801936"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Azure sanal makineleri için güvenilir başlatma (Önizleme)

Azure, [2. nesil](generation-2.md) VM 'lerin güvenliğini artırmanın sorunsuz bir yolu olarak güvenilir başlatma olanağı sunar. Güvenilir başlatma gelişmiş ve kalıcı saldırı tekniklerine karşı koruma sağlar. Güvenilir başlatma, bağımsız olarak etkinleştirilebilen birkaç, Eşgüdümlü altyapı teknolojisinden oluşur. Her teknoloji, gelişmiş tehditlere karşı başka bir savunma katmanı sunar.

> [!IMPORTANT]
> Güvenilir başlatma, yeni sanal makinelerin oluşturulmasını gerektirir. Başlangıçta oluşturulan mevcut sanal makinelerde, güvenilir başlatmayı etkinleştiremezsiniz.
>
> Güvenilen başlatma Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. 
>
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Avantajlar 

- Doğrulanmış önyükleme yükleyicileri, işletim sistemi çekirdekleri ve sürücülerle sanal makineleri güvenle dağıtın.
- Sanal makinelerde anahtarları, sertifikaları ve gizli dizileri güvenli bir şekilde koruyun.
- Tüm önyükleme zincirinin bütünlüğünden Öngörüler ve güven elde edin.
- İş yüklerinin güvenilir ve doğrulanabilir olduğundan emin olun.

## <a name="public-preview-limitations"></a>Genel önizleme sınırlamaları

**Boyut desteği**: tüm [2. nesil](generation-2.md) VM boyutları, şunlar hariç:

- Lsv2 serisi 
- M serisi 
- Mv2 serisi 
- NDv4 serisi 
- NVv4 serisi

**Işletim sistemi desteği**:
- RedHat Enterprise Linux 8,3
- SUSE 15 SP2
- Ubuntu 20,04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise
- Windows 10 Enterprise çoklu oturum

**Bölgeler**: 
- Orta Güney ABD
- Kuzey Avrupa

**Fiyatlandırma**: mevcut VM fiyatlandırmasına ek maliyet yok.

**Bu önizlemede aşağıdaki özellikler desteklenmez**:
- Backup
- Azure Site Recovery
- Paylaşılan Görüntü Galerisi
- Kısa ömürlü işletim sistemi diski
- Paylaşılan disk
- Yönetilen görüntü
- Azure Ayrılmış Konak 

## <a name="secure-boot"></a>Güvenli önyükleme

Güvenilen başlatma kökünde VM 'niz için güvenli önyükleme gerçekleştirilir. Platform üretici yazılımında uygulanan bu mod, kötü amaçlı yazılım tabanlı kök takımları ve önyükleme setleri yüklemesine karşı koruma sağlar. Güvenli önyükleme, yalnızca imzalı işletim sistemlerinin ve sürücülerin önyüklenebilmesini sağlamak için geçerlidir. VM 'nizin yazılım yığını için bir "güven kökü" oluşturur. Güvenli önyükleme etkin olduğunda, tüm işletim sistemi önyükleme bileşenleri (önyükleme yükleyicisi, çekirdek, çekirdek sürücüler) güvenilen yayımcılar tarafından imzalanması gerekir. Hem Windows hem de Linux dağıtımlarını seçin güvenli önyükleme 'yi destekler. Güvenli önyükleme, görüntünün güvenilen bir yayımcı tarafından imzalandığını doğrulayamazsa, VM 'nin önyüklemesine izin verilmez. Daha fazla bilgi için, bkz. [Güvenli Önyükleme](/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

Güvenilen başlatma, Azure VM 'Ler için vTPM 'yi de tanıtır. Bu, TPM 2.0 belirtimi ile uyumlu bir donanım [Güvenilir Platform Modülü](/windows/security/information-protection/tpm/trusted-platform-module-overview)sanallaştırılmış sürümüdür. Anahtarlar ve ölçümler için özel bir güvenli kasa görevi görür. Güvenilir başlatma, VM 'nizi, herhangi bir VM 'nin dışında güvenli bir ortamda çalışan kendi adanmış TPM örneğiyle birlikte sağlar. VTPM, sanal makinenizin (UEFı, IŞLETIM sistemi, sistem ve sürücüler) önyükleme zincirinin tamamını ölçerek [kanıtlamayı](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) mümkün bir şekilde sunar. 

Güvenilen başlatma, bulut tarafından uzaktan kanıtlama gerçekleştirmek için vTPM 'yi kullanır. Bu, platform sistem durumu denetimleri ve güven tabanlı kararlar sağlamak için kullanılır. Bir sistem durumu denetimi olarak, güvenilir başlatma, sanal makinenizin doğru şekilde önyüklendiğini şifreli olarak onaylayamaz. İşlem başarısız olursa, büyük olasılıkla VM 'niz yetkisiz bir bileşen çalıştırıyorsa Azure Güvenlik Merkezi, bütünlük uyarıları verir. Uyarılar, hangi bileşenlerin bütünlük denetimlerini geçemediğini gösteren ayrıntıları içerir.

## <a name="virtualization-based-security"></a>Sanallaştırma tabanlı güvenlik

[Sanallaştırma tabanlı güvenlik](/windows-hardware/design/device-experiences/oem-vbs) (VBS), belleğin güvenli ve yalıtılmış bir bölgesini oluşturmak için hiper yöneticiyi kullanır. Windows, güvenlik açıklarına ve kötü amaçlı saldırılara karşı daha fazla koruma sayesinde çeşitli güvenlik çözümlerini çalıştırmak için bu bölgeleri kullanır. Güvenilir başlatma, hiper yönetici kod bütünlüğünü (HVCı) ve Windows Defender Credential Guard 'ı etkinleştirmenizi sağlar.

HVCı, Windows çekirdek modu süreçlerini kötü amaçlı veya doğrulanmamış kodların eklenmesine ve yürütülmesine karşı koruyan güçlü bir sistem risk azaltma sürecidir. Çekirdek modu sürücülerini ve ikililerini çalıştırmadan önce denetler ve imzasız dosyaların belleğe yüklenmesini önler. Bu, çalıştırılabilir kodun yüklenmesine izin verildiğinde değiştirilmesini sağlar. VBS ve HVCı hakkında daha fazla bilgi için bkz. [sanallaştırma tabanlı güvenlik (VBS) ve hiper yönetici tarafından zorlanan kod bütünlüğü (hvcı)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Güvenilen başlatma ve VBS ile, Windows Defender Credential Guard 'ı etkinleştirebilirsiniz. Bu özellik, yalnızca ayrıcalıklı sistem yazılımlarının erişebilmesi için gizli dizileri yalıtır ve korur. Karma değer geçişi (PtH) saldırıları gibi gizli dizileri ve kimlik bilgisi hırsızlığı saldırılarına karşı yetkisiz erişimi önlemeye yardımcı olur. Daha fazla bilgi için bkz. [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Güvenlik Merkezi tümleştirmesi

Güvenilen başlatma, sanal makinelerinizin düzgün yapılandırıldığından emin olmak için Azure Güvenlik Merkezi ile tümleşiktir. Azure Güvenlik Merkezi, uyumlu VM 'Leri sürekli olarak değerlendirir ve ilgili önerileri ortaya çıkarmak için.

- **Güvenli önyüklemeyi etkinleştirme önerisi** -bu öneri yalnızca güvenilen başlatmayı destekleyen VM 'ler için geçerlidir. Azure Güvenlik Merkezi, güvenli önyüklemeyi etkinleştirebilen, ancak devre dışı bırakılmış olan VM 'Leri tanımlayacaktır. Bunu etkinleştirmek için düşük öneme sahip bir öneri verecek.
- **VTPM 'yi etkinleştirme önerisi** -sanal makinenizin vtpm 'Si etkinse Azure Güvenlik Merkezi bunu Konuk kanıtlama gerçekleştirmek ve Gelişmiş tehdit düzenlerini belirlemek için kullanabilir. Azure Güvenlik Merkezi, güvenilen başlatmayı destekleyen ve vTPM 'nin devre dışı bırakıldığı VM 'Leri tanımlarsa, bunu etkinleştirmek için düşük bir önem derecesi sağlar. 
- **Kanıtlama sistem durumu değerlendirmesi** -sanal makinenizin vtpm 'Si etkinse Azure Güvenlik Merkezi 'nin bir UZANTıSı, sanal makinenizin sağlıklı bir şekilde önyüklendiğini uzaktan doğrulayabilir. Bu, uzaktan kanıtlama olarak bilinir. Azure Güvenlik Merkezi, uzaktan kanıtlama durumunu belirten bir değerlendirme yayınlar.

## <a name="azure-defender-integration"></a>Azure Defender tümleştirmesi

Sanal makinelerleriniz güvenilir başlatma ile düzgün şekilde ayarlandıysa, Azure Defender, VM sistem durumu sorunlarını algılayabilir ve sizi uyarabilir.

- **VM kanıtlama hatası Için uyarı** -Azure Defender, sanal makinelerinize düzenli aralıklarla kanıtlama gerçekleştirir. Bu, VM 'niz önyüklendikten sonra da olur. Kanıtlama başarısız olursa, orta önem derecesi uyarısını tetikler.
    VM kanıtlama aşağıdaki nedenlerden dolayı başarısız olabilir:
    - Bir önyükleme günlüğü içeren ve güvenilir bir taban çizgisinden saptan, atsınanan bilgiler. Bu, güvenilmeyen modüllerin yüklendiğini ve işletim sisteminin tehlikede olabileceğini gösterebilir.
    - Kanıtlama teklifinin, atsınanan VM 'nin vTPM 'sinden kaynağı olarak doğrulanamadı. Bu, kötü amaçlı yazılımın mevcut olduğunu ve vTPM 'ye giden trafiği kesintiye uğradığını gösterebilir.
    - VM 'deki kanıtlama uzantısı yanıt vermiyor. Bu, kötü amaçlı yazılımlara veya bir işletim sistemi yöneticisine yönelik bir hizmet reddi saldırısı olduğunu gösterebilir.

    > [!NOTE]
    >  Bu uyarı, vTPM 'nin etkinleştirildiği ve kanıtlama uzantısının yüklü olduğu VM 'Ler için kullanılabilir. Kanıtlama başarılı olması için güvenli önyükleme etkinleştirilmelidir. Güvenli önyükleme devre dışıysa kanıtlama başarısız olur. Güvenli önyüklemeyi devre dışı bırakmanız gerekirse, hatalı pozitif sonuçlar oluşmasını önlemek için bu uyarıyı bastırabilirsiniz.

- **Güvenilmeyen Linux çekirdek modülü Için uyarı** -güvenli önyükleme etkin olan güvenilir başlatma için, bir çekirdek sürücü doğrulamanın başarısız olmasına ve yüklenmesine izin verilse bıle bir VM 'nin önyüklemesi mümkündür. Bu durumda, Azure Defender düşük önem derecesi uyarısı verecek. Güvenilir olmayan sürücü yüklenmediği için, bu olaylar araştırılmalıdır. Aşağıdaki topluluklara bir göz atın:
    - Hangi çekirdek sürücüsü başarısız? Bu sürücüyü öğrendim ve yüklenmesini bekliyor mıyım?
    - Bu, beklendiğim sürücünün tam sürümüdür mi? Sürücü ikilileri bozulmadan mı? Bu bir 3. taraf sürücü ise, satıcının imzalanmış olması için işletim sistemi uyumluluk testlerini iletsin mi?


## <a name="faq"></a>SSS

Güvenilir başlatma hakkında sık sorulan sorular.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Neden güvenilir başlatma kullanmalıyım? Güvenilir başlatma koruması ne için?

Önyükleme setleri, kökler ve çekirdek düzeyinde kötü amaçlı yazılımlara karşı güvenilen başlatma koruyucuları. Bu gelişmiş kötü amaçlı yazılım türleri çekirdek modunda çalışır ve kullanıcılardan gizlenmeye devam eder. Örnek:
- Bellenim rootkits: Bu setleri, sanal makinenin BIOS belleniminin üzerine yazılır, bu nedenle rootkit işletim sisteminden önce başlayabilir. 
- Önyükleme setleri: Bu setleri, sanal makinenin işletim sistemi için önyükleme paketini yüklemesi için işletim sisteminin önyükleme yükleyicisine göre değiştirilir.
- Çekirdek kökler: Bu setleri, işletim sistemi çekirdeğinin bir bölümünü değiştirir, böylece rootkit, işletim sistemi yüklendiğinde otomatik olarak başlayabilir.
- Sürücü kök takımları: Bu setleri, işletim sisteminin sanal makinenin bileşenleriyle iletişim kurmak için kullandığı güvenilir sürücülerden biri olmaya eğilimindedir.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Güvenli önyükleme ile ölçülen önyükleme arasındaki farklar nelerdir?

Güvenli önyükleme zincirinde, önyükleme işlemindeki her adım sonraki adımların şifreli imzasını denetler. Örneğin, BIOS, yükleyicisindeki bir imzayı denetlemelidir ve yükleyici, yüklediği tüm çekirdek nesnelerinde imzaları kontrol eder ve bu şekilde devam eder. Nesnelerden herhangi biri tehlikeye girerse, imza eşleşmez ve VM 'nin önyüklemesi olmayacaktır. Daha fazla bilgi için, bkz. [Güvenli Önyükleme](/windows-hardware/design/device-experiences/oem-secure-boot). Ölçülen önyükleme önyükleme işlemini durdurmaz, zincirdeki sonraki nesnelerin karmasını ölçer veya hesaplar ve karma değerleri vTPM üzerindeki platform yapılandırma kayıtları 'nda (PLER) depolar. Ölçülen önyükleme kayıtları, önyükleme bütünlüğü izlemesi için kullanılır.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Bütünlük hatası algılandığında ne olur?

Azure sanal makineleri için güvenilir başlatma, gelişmiş tehditler için izlenir. Bu tür tehditler algılanırsa bir uyarı tetiklenir. Uyarılar yalnızca Azure Güvenlik Merkezi 'nin [Standart katmanında](../security-center/security-center-pricing.md) kullanılabilir.
Azure Güvenlik Merkezi düzenli aralıklarla kanıtlama gerçekleştirir. Kanıtlama başarısız olursa, orta öneme sahip bir uyarı tetiklenir. Güvenilen başlatma kanıtlama aşağıdaki nedenlerden dolayı başarısız olabilir: 
- Güvenilir bilgi Işlem tabanının (TCB) bir günlüğünü içeren, güvenilir bir taban çizgisinden (güvenli önyükleme etkinleştirildiğinde olduğu gibi) dahil olan, atsınanan bilgiler. Bu, güvenilmeyen modüllerin yüklendiğini ve işletim sisteminin tehlikede olabileceğini gösterebilir.
- Kanıtlama teklifinin, atsınanan VM 'nin vTPM 'sinden kaynağı olarak doğrulanamadı. Bu, kötü amaçlı yazılımın mevcut olduğunu ve TPM 'ye giden trafiği kesintiye uğradığını gösterebilir. 
- VM 'deki kanıtlama uzantısı yanıt vermiyor. Bu, kötü amaçlı yazılımlara veya bir işletim sistemi yöneticisine yönelik bir hizmet reddi saldırısı olduğunu gösterebilir.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Hyper-V korumalı VM ile nasıl güvenilir bir şekilde başlatılır?
Hyper-V korumalı VM Şu anda yalnızca Hyper-V ' d e kullanılabilir. [Hyper-V KORUMALı VM](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) genellikle korunan yapıyla birlikte dağıtılır. Korunan bir yapı, bir konak koruyucu hizmeti (HGS), bir veya daha fazla korunan konak ve bir dizi korumalı VM 'den oluşur. Hyper-V korumalı VM 'Ler, sanal makinenin verilerinin ve durumunun, Hyper-V konaklarında çalışıyor olabilecek hem yapı yöneticileri hem de güvenilmeyen yazılımlar tarafından korunması gereken dokuların kullanımına yöneliktir. Diğer taraftan güvenilir başlatma, HGS 'nin ek dağıtımı ve yönetimi olmadan tek başına bir sanal makine veya sanal makine ölçek kümeleri olarak dağıtılabilir. Tüm güvenilir başlatma özellikleri, dağıtım kodunda basit bir değişiklik veya Azure portal onay kutusu ile etkinleştirilebilir.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Mevcut VM 'Leri güvenilir başlatmaya nasıl dönüştürebilirim?
2. nesil VM için, güvenilir başlatmaya dönüştürülecek geçiş yolu genel kullanıma (GA) yöneliktir.

## <a name="next-steps"></a>Sonraki adımlar

[Portalı kullanarak güvenilir bir başlatma VM](trusted-launch-portal.md)'si dağıtın.
