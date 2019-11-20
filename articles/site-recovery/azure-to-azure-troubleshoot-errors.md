---
title: Azure Site Recovery Azure VM çoğaltma sorunlarını giderme
description: Olağanüstü durum kurtarma için Azure sanal makinelerini çoğaltma sırasında oluşan sorunları giderin.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: b3c459c0eaac98a1cb704b4346153f77ec974188
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084918"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure-Azure VM çoğaltma hatalarında sorun giderme

Bu makalede, Azure sanal makinelerinin (VM 'Ler) bir bölgeden diğerine çoğaltılması ve kurtarılması sırasında Azure Site Recovery yaygın olarak karşılaşılan hataların nasıl giderileceği açıklanmaktadır. Desteklenen yapılandırmalar hakkında daha fazla bilgi için bkz. [Azure Vm'lerini çoğaltma için destek matrisi](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure Kaynak kotası sorunları (hata kodu 150097)

Aboneliğinizin, olağanüstü durum kurtarma bölgeniz olarak kullanmayı planladığınız hedef bölgede Azure VM 'Leri oluşturmak için etkinleştirildiğinden emin olun. Ayrıca, aboneliğinizin gerekli boyutlarda VM 'Ler oluşturmak için yeterli kotası olduğundan emin olun. Varsayılan olarak Site Recovery, kaynak VM boyutuyla aynı olan bir hedef VM boyutu seçer. Eşleşen boyut kullanılamıyorsa, Site Recovery otomatik olarak kullanılabilir en yakın boyutu seçer.

Kaynak VM yapılandırmasını destekleyen bir boyut yoksa, aşağıdaki ileti görünür:

> " *VMName*sanal makinesi için çoğaltma etkinleştirilemedi."

### <a name="possible-causes"></a>Olası nedenler

- Abonelik KIMLIĞINIZ, hedef bölge konumunda herhangi bir sanal makine oluşturmak için etkinleştirilmemiş.
- Hedef bölge konumunda belirli VM boyutlarını oluşturmak için abonelik KIMLIĞINIZ etkin değil veya yeterli kotayı yok.
- Hedef bölge konumundaki abonelik KIMLIĞI için kaynak VM 'nin ağ arabirimi kartı (NIC) sayısıyla (2) eşleşecek uygun bir hedef VM boyutu bulunamadı.

### <a name="fix-the-problem"></a>Sorunu

Aboneliğinizi hedef konumda gereken boyutlarda VM 'Ler oluşturacak şekilde etkinleştirmek için [Azure Faturalandırma desteği](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 'ne başvurun. Sonra başarısız olan işlemi yeniden deneyin.

Hedef konumun bir kapasite kısıtlaması varsa, çoğaltmayı devre dışı bırakın. Daha sonra, aboneliğin gereken boyutlarda VM 'Ler oluşturmak için yeterli kotası olan farklı bir konuma çoğaltmayı etkinleştirin.

## <a name="trusted-root-certificates-error-code-151066"></a>Güvenilen kök sertifikalar (hata kodu 151066)

VM üzerinde en son güvenilen kök sertifikaların tümü yoksa, "çoğaltmayı etkinleştir" Site Recovery iş başarısız olabilir. VM 'deki Site Recovery hizmet çağrılarının kimlik doğrulaması ve yetkilendirmesi Bu sertifikalar olmadan başarısız oluyor. 

"Çoğaltmayı etkinleştir" işi başarısız olursa, aşağıdaki ileti görünür:

> "Site Recovery yapılandırması başarısız oldu."

### <a name="possible-cause"></a>Olası neden

Yetkilendirme ve kimlik doğrulaması için gerekli olan güvenilen kök sertifikalar, sanal makinede mevcut değil.

### <a name="fix-the-problem"></a>Sorunu

#### <a name="windows"></a>Windows

Windows işletim sistemini çalıştıran bir VM için, tüm güvenilen kök sertifikaların makinede bulunması için en son Windows güncelleştirmelerini VM 'ye yükler. En son kök sertifikaları ve VM 'lerde güncelleştirilmiş sertifika iptal listesini almak için kuruluşunuzdaki tipik Windows Update-Management veya sertifika güncelleştirme yönetimi sürecini izleyin.

Bağlantısı kesilmiş bir ortamda kullanıyorsanız, kuruluşunuz sertifikaları almak için standart Windows güncelleştirme işlemi izleyin. Gerekli sertifikaları sanal makinede mevcut değilse, Site Recovery hizmetine çağrı güvenlikle ilgili nedenlerle başarısız.

Sorunun giderilmiş olduğunu doğrulamak için vm'nizde bir tarayıcıdan login.microsoftonline.com için gidin.

Daha fazla bilgi için bkz. [Güvenilen kökleri ve izin verilmeyen sertifikaları yapılandırma](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

En son güvenilen kök sertifikalarını ve VM 'deki en son sertifika iptal listesini almak için Linux işletim sistemi sürümünüzün dağıtıcısında sunulan yönergeleri izleyin.

SuSE Linux bir sertifika listesini sürdürmek için sembolik bağlantılar (veya *symbağlantılar*) kullandığından, şu adımları izleyin:

1. Kök kullanıcı olarak oturum açın.

1. Dizini değiştirmek için şu komutu çalıştırın:

    **# CD/etc/SSL/CERT**

1. Symantec kök CA sertifikasının mevcut olup olmadığını denetleyin:

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5. ped**

1. Symantec kök CA sertifikası bulunamazsa, dosyayı indirmek için aşağıdaki komutu çalıştırın. Herhangi bir hata olup olmadığını denetleyin ve ağ hataları için önerilen eylemleri izleyin.

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem-O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Baltidaha fazla kök CA sertifikasının mevcut olup olmadığını denetleyin:

    **# ls Baltimore_CyberTrust_Root. ped**

1. Baltidaha fazla kök CA sertifikası bulunamazsa, sertifikayı indirmek için şu komutu çalıştırın:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem-O Baltimore_CyberTrust_Root. pem**

1. DigiCert_Global_Root_CA sertifikasının mevcut olup olmadığını denetleyin:

    **# ls DigiCert_Global_Root_CA. ped**

1. DigiCert_Global_Root_CA bulunamazsa, sertifikayı indirmek için aşağıdaki komutları çalıştırın:

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# OpenSSL x509-ın Digiccertglobalrootca. CRT-bilgilendirme der-outform Pen-Out DigiCert_Global_Root_CA. Pez**

1. Yeni indirilen sertifikaların sertifika konu karmalarını güncelleştirmek için yeniden karma betiği çalıştırın:

    **# c_rehash**

1. Sertifikalar için konu karmalarının çözümlemeyin olarak oluşturulup oluşturulmayacağını denetlemek için şu komutları çalıştırın:

    - Komutundaki

        **# ls-l | GREP Baltimore**

    - Çıktı:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Komutundaki

        **# ls-l | GREP VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Çıktı:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Komutundaki

        **# ls-l | GREP DigiCert_Global_Root**

    - Çıktı:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Dosya adı b204d74a. 0 olan VeriSign_Class_3_Public_Primary_Certification_Authority_G5. ped dosyasının bir kopyasını oluşturun:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pek b204d74a. 0**

1. Dosya adı 653b494a. 0 olan Baltimore_CyberTrust_Root. ped dosyasının bir kopyasını oluşturun:

    **# CP Baltimore_CyberTrust_Root. pek 653b494a. 0**

1. Dosya adı 3513523f. 0 olan DigiCert_Global_Root_CA. ped dosyasının bir kopyasını oluşturun:

    **# CP DigiCert_Global_Root_CA. pek 3513523f. 0**

1. Dosyaların mevcut olup olmadığını denetleyin:

    - Komutundaki

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Çıktı

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery hizmeti URL'lerine veya IP aralıkları (hata kodu 151037 veya 151072) için giden bağlantı

Site Recovery çoğaltmanın çalışması için, VM 'den belirli URL 'Lere veya IP aralıklarına giden bağlantı gerekir. Sanal makinenize bir güvenlik duvarının arkasındaysa ya da giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsa bu sorunlardan biri karşılaşıyor.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Sorun 1: Azure sanal makinesi Site Recovery kaydedilemedi (hata kodu 151195)

#### <a name="possible-cause"></a>Olası neden 

Site Recovery uç noktaları bağlantısı, DNS çözümleme hatası nedeniyle oluşturulamıyor.

Bu sorun, sanal makinenin yük devretmesidir ancak DNS sunucusuna olağanüstü durum kurtarma (DR) bölgesinden ulaşılamadığında, yeniden koruma sırasında en sık meydana gelir.

#### <a name="fix-the-problem"></a>Sorunu

Özel bir DNS kullanıyorsanız, DNS sunucusuna olağanüstü durum kurtarma bölgesinden erişilebildiğinden emin olun. Özel bir DNS olup olmadığını öğrenmek için VM 'de, *olağanüstü durum kurtarma ağı* > **DNS sunucuları**' na gidin.

![Özel DNS sunucusu listesi](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

DNS sunucusuna sanal makineden erişmeyi deneyin. Sunucu erişilebilir değilse, DNS sunucusu üzerinden yük devrederden veya DR ağı ile DNS arasında site satırı oluşturarak erişilebilir yapın.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>Sorun 2: Site Recovery yapılandırma başarısız oldu (hata kodu 151196)

#### <a name="possible-cause"></a>Olası neden

Office 365 kimlik doğrulaması ve kimlik ıP4 uç noktaları bağlantısı oluşturulamıyor.

#### <a name="fix-the-problem"></a>Sorunu

Site Recovery, kimlik doğrulaması için Office 365 IP aralıklarına erişim gerektirir.
VM 'deki giden ağ bağlantısını denetlemek için Azure NSG kuralları veya güvenlik duvarı proxy 'si kullanıyorsanız, Office 365 IP aralıklarına yönelik iletişime izin verdiğinizden emin olun. Azure AD 'ye karşılık gelen tüm IP adreslerine erişime izin veren [Azure Active Directory (Azure AD) hizmet etiketini](../virtual-network/security-overview.md#service-tags)temel alan bir NSG kuralı oluşturun. Daha sonra Azure AD 'ye yeni adresler eklenirse, yeni NSG kuralları oluşturmanız gerekir.

> [!NOTE]
> VM 'Ler *Standart* bir iç yük dengeleyicinin arkasındaysa, yük dengeleyici varsayılan olarak OFFICE 365 IP aralıklarına (yani, Login.microsoftonline.com) erişemez. İç yük dengeleyici türünü *temel* olarak değiştirin ya da [yük dengelemeyi ve giden kuralları yapılandırma](https://aka.ms/lboutboundrulescli)makalesinde açıklandığı şekilde giden erişim oluşturun.

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>Sorun 3: Site Recovery yapılandırma başarısız oldu (hata kodu 151197)

#### <a name="possible-cause"></a>Olası neden

Bağlantı, Site Recovery hizmet uç noktaları ile oluşturulamaz.

#### <a name="fix-the-problem"></a>Sorunu

Site Recovery, bölgeye bağlı olarak [SITE Recovery IP aralıklarına](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)erişim gerektirir. Gerekli IP aralıklarının sanal makineden erişilebilir olduğundan emin olun.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>Sorun 4: ağ trafiği şirket içi proxy sunucusundan geçtiğinde Azure 'dan Azure 'a çoğaltma başarısız oldu (hata kodu 151072)

#### <a name="possible-cause"></a>Olası neden

Özel ara sunucu ayarları geçersiz ve Site Recovery Mobility hizmeti Aracısı, Internet Explorer 'dan ara sunucu ayarlarını otomatik olarak algılamadı.

#### <a name="fix-the-problem"></a>Sorunu

Mobility hizmeti Aracısı Windows üzerinde Internet Explorer ve Linux üzerinde/etc/Environment aracılığıyla proxy ayarlarını algılar.

Yalnızca Mobility hizmeti için proxy 'yi ayarlamayı tercih ediyorsanız, ara sunucu ayrıntılarını şu konumlarda bulunan ProxyInfo. conf dosyasında sağlayabilirsiniz:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft Azure Site Recovery\Config

ProxyInfo. conf dosyasında, proxy ayarlarını aşağıdaki başlatma dosyası biçiminde sağlayın:

> [*proxy*]

> Adres = *http://1.2.3.4*

> Bağlantı noktası =*567*


> [!NOTE]
> Site Recovery Mobility hizmeti Aracısı yalnızca *kimliği doğrulanmamış proxy 'leri*destekler.

### <a name="more-information"></a>Daha fazla bilgi

[Gerekli URL 'leri](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) veya [gerekli IP aralıklarını](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)belirtmek için [Azure 'Da Azure 'da ağ iletişimi hakkında](site-recovery-azure-to-azure-networking-guidance.md)bölümündeki yönergeleri izleyin.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disk (hata kodu 150039) makinede bulunamadı

VM'ye yeni bir disk başlatılmalıdır. Disk bulunamazsa, aşağıdaki ileti görünür:

> "Mantıksal birim numarasına sahip Azure veri diski *diskname* *diskuri* , *aynı LUN değerine* sahip VM içinden bildirilen karşılık gelen bir diskle *eşleştirilmedi.*

### <a name="possible-causes"></a>Olası nedenler

- VM 'ye yeni bir veri diski iliştirilmiş ancak başlatılmamış.
- VM içindeki veri diski, diskin sanal makineye eklendiği mantıksal birim numarası (LUN) değerini doğru şekilde bildirmiyor.

### <a name="fix-the-problem"></a>Sorunu

Veri disklerinin başlatıldığından emin olun ve işlemi yeniden deneyin.

- **Windows**: [Yeni bir disk ekleyin ve başlatın](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Linux 'ta yeni bir veri diski başlatın](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Sorun devam ederse desteğe başvurun.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Bir veya daha fazla disk koruma için kullanılabilir (hata kodu 153039)

### <a name="possible-causes"></a>Olası nedenler

- Yakın zamanda sanal makineye korumadan sonra bir veya daha fazla disk eklendi.
- Sanal makine korunduktan sonra bir veya daha fazla disk başlatıldı.

### <a name="fix-the-problem"></a>Sorunu

VM 'nin çoğaltma durumunu yeniden sağlıklı hale getirmek için, diskleri korumayı ya da uyarıyı kapatmak için seçeneğini belirleyebilirsiniz.

#### <a name="to-protect-the-disks"></a>Diskleri korumak için

1. **Çoğaltılan öğeler** > *VM adı* > **diskleri**' ne gidin.
1. Korumasız disk ' i seçin ve ardından **çoğaltmayı etkinleştir**' i seçin:

    ![VM disklerinde çoğaltmayı etkinleştirme](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Uyarıyı kapatmak için

1. **Çoğaltılan öğeler** > *VM adı*' na gidin.
1. **Genel bakış** bölümünde uyarıyı seçin ve ardından **Tamam**' ı seçin.

    ![Yeni disk uyarısını kapat](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Sanal makineyi kasadan kaldır ile tamamlandı (hata kodu 150225)

Sanal makineyi koruduğunda Site Recovery kaynak sanal makinede bazı bağlantılar oluşturur. Korumayı kaldırdığınızda veya çoğaltmayı devre dışı bıraktığınızda, Site Recovery bu bağlantıları temizleme işinin bir parçası olarak kaldırır. Sanal makinede bir kaynak kilidi varsa, temizleme işi bilgilerle tamamlanır. Bu bilgiler, sanal makinenin kurtarma hizmetleri kasasından kaldırıldığını, ancak eski bağlantıların bazılarının kaynak makinede temizlendiğinden emin olduğunu söyler.

Bu sanal makineyi yeniden korumayı hiç düşünmüyorsanız, bu uyarıyı yoksayabilirsiniz. Ancak bu sanal makineyi daha sonra korumanız gerekiyorsa, bağlantıları temizlemek için "sorunu çözme" bölümündeki adımları izleyin.

> [!WARNING]
> Temizleme işlemini yapmazsanız:
>
> - Kurtarma Hizmetleri Kasası aracılığıyla çoğaltmayı etkinleştirdiğinizde, sanal makine listelenmez.
> - VM 'yi **sanal makine** > **ayarları** > **olağanüstü durum kurtarma**kullanarak korumaya çalışırsanız, "VM 'Deki mevcut eski kaynak bağlantıları nedeniyle çoğaltma etkinleştirilemiyor" iletisiyle başarısız olur.

### <a name="fix-the-problem"></a>Sorunu

> [!NOTE]
> Site Recovery, kaynak sanal makineyi silmez veya bu adımları gerçekleştirirken herhangi bir şekilde etkilemez.

1. Kilidi VM veya VM kaynak grubundan kaldırın. Örneğin, aşağıdaki görüntüde, "MoveDemo" adlı VM 'deki kaynak kilidi silinmelidir:

    ![VM 'den kilidi kaldır](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. [Eski bir Site Recovery yapılandırmasını kaldırmak](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)için betiği indirin.
1. Cleanup-stale-asr-config-Azure-VM. ps1 adlı komut dosyasını çalıştırın. Abonelik KIMLIĞI, VM kaynak grubu ve VM adını parametreler olarak sağlayın.
1. Azure kimlik bilgileri istenirse, bunları sağlayın. Sonra betiğin hatasız çalıştığını doğrulayın.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>VM 'deki eski kaynak bağlantıları nedeniyle çoğaltma etkinleştirilemiyor (hata kodu 150226)

### <a name="possible-cause"></a>Olası neden

Sanal makinenin önceki Site Recovery korumasından eski bir yapılandırması vardır.

Azure VM için Site Recovery kullanarak çoğaltmayı etkinleştirdiyseniz, eski bir yapılandırma bir Azure VM üzerinde gerçekleşebilir ve sonra şunları yapabilirsiniz:

- Çoğaltmayı devre dışı bırakmış, ancak kaynak VM 'nin bir kaynak kilidi vardı.
- SANAL makinede çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını silmiş olursunuz.
- VM 'de çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını içeren kaynak grubunu silmiş olursunuz.

### <a name="fix-the-problem"></a>Sorunu

> [!NOTE]
> Site Recovery, kaynak sanal makineyi silmez veya bu adımları gerçekleştirirken herhangi bir şekilde etkilemez.

1. Kilidi VM veya VM kaynak grubundan kaldırın. Örneğin, aşağıdaki görüntüde, "MoveDemo" adlı VM 'deki kaynak kilidi silinmelidir:

    ![VM 'den kilidi kaldır](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. [Eski bir Site Recovery yapılandırmasını kaldırmak](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)için betiği indirin.
1. Cleanup-stale-asr-config-Azure-VM. ps1 adlı komut dosyasını çalıştırın. Abonelik KIMLIĞI, VM kaynak grubu ve VM adını parametreler olarak sağlayın.
1. Azure kimlik bilgileri istenirse, bunları sağlayın. Sonra betiğin hatasız çalıştığını doğrulayın.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>"Çoğaltmayı etkinleştir" işinde seçim için Azure VM veya kaynak grubu görüntülenemiyor

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Neden 1: kaynak grubu ve kaynak sanal makine farklı konumlarda

Site Recovery Şu anda kaynak bölgesi kaynak grubunun ve sanal makinelerin aynı konumda olmasını gerektirir. Aksi takdirde, koruma uygulamaya çalıştığınızda sanal makineyi veya kaynak grubunu bulamayacaksınız.

Geçici bir çözüm olarak, kurtarma hizmetleri Kasası yerine VM 'den çoğaltmayı etkinleştirebilirsiniz. **Kaynak VM** > **Özellikler** > **olağanüstü durum kurtarma** ' ya gidin ve çoğaltmayı etkinleştirin.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Neden 2: kaynak grubu seçili aboneliğin bir parçası değil

Kaynak grubu seçili aboneliğin bir parçası değilse, koruma sırasında kaynak grubunu bulamamanız mümkün olmayabilir. Kaynak grubunun, kullanmakta olduğunuz aboneliğe ait olduğundan emin olun.

### <a name="cause-3-stale-configuration"></a>Neden 3: eski yapılandırma

Azure VM 'de eski bir Site Recovery yapılandırması ayrıldıysa çoğaltma için etkinleştirmek istediğiniz VM 'yi göremeyebilirsiniz. Bu durum, Site Recovery kullanarak Azure VM için çoğaltmayı etkinleştirdiyseniz oluşabilir ve ardından:

- SANAL makinede çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını silmiş olursunuz.
- VM 'de çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını içeren kaynak grubunu silmiş olursunuz.
- Çoğaltmayı devre dışı bırakmış, ancak kaynak VM 'nin bir kaynak kilidi vardı.

### <a name="fix-the-problem"></a>Sorunu

> [!NOTE]
> Bu bölümde bahsedilen betiği kullanmadan önce "Azurere. resources" modülünü güncelleştirdiğinizden emin olun.  Site Recovery, kaynak sanal makineyi silmez veya bu adımları gerçekleştirirken herhangi bir şekilde etkilemez.

1. Varsa kilidi VM veya VM kaynak grubundan kaldırın. Örneğin, aşağıdaki görüntüde, "MoveDemo" adlı VM 'deki kaynak kilidi silinmelidir:

    ![VM 'den kilidi kaldır](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. [Eski bir Site Recovery yapılandırmasını kaldırmak](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)için betiği indirin.
1. Cleanup-stale-asr-config-Azure-VM. ps1 adlı komut dosyasını çalıştırın. Abonelik KIMLIĞI, VM kaynak grubu ve VM adını parametreler olarak sağlayın.
1. Azure kimlik bilgileri istenirse, bunları sağlayın. Sonra betiğin hatasız çalıştığını doğrulayın.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Koruma için bir sanal makine seçme yapılamıyor

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Neden 1: sanal makine, başarısız veya yanıt vermeyen bir duruma yüklenmiş bir uzantıya sahiptir

**Sanal makineler** > **ayarları** > **uzantılarına** gidin ve başarısız durumundaki uzantıları denetleyin. Başarısız olan tüm uzantıları kaldırın ve ardından sanal makineyi korumayı yeniden deneyin.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Neden 2: VM 'nin sağlama durumu geçerli değil

Bu makalenin ilerleyen kısımlarında, [VM 'nin sağlama durumundaki](#the-vms-provisioning-state-is-not-valid-error-code-150019)sorun giderme adımlarının geçerli olmadığını görün.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM 'nin sağlama durumu geçerli değil (hata kodu 150019)

VM 'de çoğaltmayı etkinleştirmek için, sağlama durumunun **başarılı**olması gerekir. Sağlama durumunu denetlemek için şu adımları izleyin:

1. Azure portal, **tüm hizmetlerden** **Kaynak Gezgini** seçin.
1. Genişletin **abonelikleri** listelemek ve aboneliğinizi seçin.
1. Genişletin **ResourceGroups** listeleyebilir ve sanal makinenin kaynak grubunu seçin.
1. **Kaynaklar** listesini GENIŞLETIN ve VM 'nizi seçin.
1. Sağ taraftaki örnek görünümünde **Provisioningstate** alanını denetleyin.

### <a name="fix-the-problem"></a>Sorunu

- Varsa **provisioningState** olduğu **başarısız**, sorun giderme ayrıntıları ile Destek ekibiyle iletişime geçin.
- **Provisioningstate** **güncelleştiriyoruz**, başka bir uzantı dağıtılmış olabilir. VM 'de devam eden bir işlem olup olmadığını denetleyin, bunların tamamlanmasını bekleyin ve ardından Site Recovery başarısız olan "çoğaltmayı etkinleştir" işini yeniden deneyin.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Hedef sanal makine seçilemedi (ağ seçimi sekmesi kullanılamıyor)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Neden 1: VM 'Niz bir hedef ağa zaten eşlenmiş bir ağa bağlı

Kaynak VM bir sanal ağın parçasıysa ve aynı sanal ağdaki başka bir VM zaten hedef kaynak grubundaki bir ağla eşlenmişse, varsayılan olarak ağ seçme açılan liste kutusu kullanılamaz (soluk görünür).

![Ağ seçim listesi kullanılamıyor](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Neden 2: Site Recovery kullanarak VM 'yi koruduysanız ve çoğaltmayı devre dışı bırakmış olursunuz

VM çoğaltmasını devre dışı bırakmak Ağ eşlemesini silmez. Eşleme, VM 'nin korunduğu kurtarma hizmetleri kasasından silinmelidir. **Ağ eşlemesi** > **Site Recovery altyapı** > *Kurtarma Hizmetleri Kasası* 'na gidin.

![Ağ eşlemesini Sil](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Olağanüstü durum kurtarma kurulumu sırasında yapılandırılan hedef ağ, VM korunduktan sonra ilk kurulumdan sonra değiştirilebilir:

![Ağ eşlemesini Değiştir](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Ağ eşlemesinin değiştirilmesinin, aynı ağ eşlemesini kullanan tüm korumalı VM 'Leri etkilediğini unutmayın.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ veya birim gölge kopyası hizmeti hatası (hata kodu 151025)

Bu hata oluştuğunda aşağıdaki ileti görünür:

> "Site Recovery uzantısı yüklenemedi"

### <a name="possible-causes"></a>Olası nedenler

- COM+ sistem uygulaması hizmeti devre dışı bırakıldı.
- Birim gölge kopyası hizmeti devre dışı bırakıldı.

### <a name="fix-the-problem"></a>Sorunu

COM+ sistem uygulaması ve birim gölge kopyası hizmetlerini otomatik veya el ile başlatma moduna ayarlayın.

1. Windows 'da Hizmetler konsolunu açın.
1. COM+ sistem uygulamasının ve birim gölge kopyası hizmetlerinin **Başlangıç türü**olarak **devre dışı** olarak ayarlanmamış olduğundan emin olun.

    ![COM+ sistem uygulamasının ve birim gölge kopyası hizmetlerinin başlangıç türünü denetle](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Desteklenmeyen yönetilen disk boyutu (hata kodu 150172)

Bu hata oluştuğunda aşağıdaki ileti görünür:

> Desteklenen en düşük boyut olan 1024 MB 'tan daha küçük olan bir *disk adı* , *DiskSize*büyüklüğünde diskname içerdiğinden, sanal makine için koruma etkinleştirilemedi. "

### <a name="possible-cause"></a>Olası neden

Disk, desteklenen 1024 MB boyutundan daha küçük.

### <a name="fix-the-problem"></a>Sorunu

Disk boyutunun desteklenen boyut aralığında olduğundan emin olun ve işlemi yeniden deneyin.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>GRUB yapılandırması UUID yerine cihaz adını içerdiğinden koruma etkinleştirilemedi (hata kodu 151126)

### <a name="possible-cause"></a>Olası neden

Linux GRUB yapılandırma dosyaları (/boot/grub/menu.lst ",/Boot/Grub/grub.cfg,/Boot/GRUB2/grub.cfg veya/etc/default/grub), *kök* ve *SÜRDÜRÜLMESI* parametreleri için UUID değerleri yerine gerçek cihaz adlarını belirtebilir. Site Recovery, cihaz adları değiştirebildiğinden UUID 'ler gerektirir. Yeniden başlatma sonrasında bir VM, yük devretme sırasında aynı ada sahip olmayabilir ve sorunlar ortaya çıkabilir.

Aşağıdaki örnekler, gerekli UUID 'ler yerine cihaz adlarının (kalın olarak gösterilen) göründüğü GRUB dosyalarından alınan satırlardan oluşur:

- Dosya/boot/GRUB2/grub.cfg

  > Linux/Boot/vmlinuz-3.12.49-11-Default **root =/dev/sda2** $ {extra_cmdline} **sürdürülmesi =/dev/sda1** Splash = sessiz sessiz showopts

- Dosya:/boot/grub/menu.lst

  > çekirdek/Boot/vmlinuz-3.0.101-63-Default **root =/dev/sda2** **özgeçmişi =/dev/sda1** Splash = sessiz crashkernel = 256D-: 128dk showopts VGA = 0x314


### <a name="fix-the-problem"></a>Sorunu

Her bir cihaz adını karşılık gelen UUID ile değiştirin:

1. **Blkıd** ***Cihaz adı***komutunu yürüterek cihazın UUID 'sini bulun. Örneğin:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. **Kök = uuıd**=*UUID* ve **özgeçmişi = UUID**=*UUID*' de cihaz adını UUID ile değiştirin. Örneğin, değiştirildikten sonra,/boot/grub/menu.lst (daha önce ele alınmıştır) satırı şöyle görünür:

    > çekirdek/Boot/vmlinuz-3.0.101-63-Default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **özgeçmişi = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** tanıtım = sessiz crashkernel = 256D-: 128mshowopts VGA = 0x314

1. Korumayı yeniden deneyin.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>GRUB yapılandırmasında belirtilen cihaz mevcut olmadığından koruma etkinleştirme başarısız oldu (hata kodu 151124)

### <a name="possible-cause"></a>Olası neden

GRUB yapılandırma dosyaları (/boot/grub/menu.lst,/Boot/Grub/grub.cfg,/Boot/GRUB2/grub.cfg veya/etc/default/grub), *RD.LVM.lv* veya *rd_LVM_LV*parametrelerini içerebilir. Bu parametreler, önyükleme zamanında keşfedilecek mantıksal birim Yöneticisi (LVM) cihazlarını belirler. Bu LVM cihazları yoksa, korunan sistem önyüklenmez ve önyükleme sürecinde takılacaktır. Ayrıca, yük devretme VM 'si ile aynı sorun da görünür. İşte birkaç örnek:

- Dosya:/Boot/GRUB2/grub.cfg on RHEL7:

    > linux16/vmlinuz-3.10.0-957.EL7. x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128D\@64D **RD. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/takas** rhgb quiet LANG = en_US. UTF-8

- Dosya: RHEL7 üzerinde/etc/default/grub:

    > GRUB_CMDLINE_LINUX = "crashkernel = Auto **RD. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/Swap** rhgb quiet"

- Dosya:/boot/grub/menu.lst on RHEL6:

    > çekirdek/vmlinuz-2.6.32-754.el6. x86_64 ro root = UUID = 36dd8b45-E90D-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = Auto **rd_LVM_LV = rootvg/lv_root** KeyboardType = PC KeyTable = US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

Her örnekte, kalın olan bölüm, GRUB 'nin "rootvg" birim grubundaki "root" ve "takas" adlarına sahip iki LVM cihazını algılaması gerektiğini gösterir.

### <a name="fix-the-problem"></a>Sorunu

LVM cihazı yoksa, bunu oluşturun ya da GRUB yapılandırma dosyalarından ilgili parametreleri kaldırın. Ardından, korumayı etkinleştirmeyi yeniden deneyin.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Bir Site Recovery Mobility hizmeti güncelleştirmesi uyarılarla tamamlandı (hata kodu 151083)

Site Recovery Mobility hizmetinde, biri filtre sürücüsü olarak adlandırılan birçok bileşen vardır. Filtre sürücüsü yalnızca sistem yeniden başlatma sırasında sistem belleğine yüklenir. Mobility hizmeti güncelleştirmesi filtre sürücüsü değişikliklerini içerdiğinde makine güncelleştirilir ancak bazı düzeltmelerin yeniden başlatma gerektirdiğini bildiren bir uyarı görürsünüz. Bu uyarı, filtre sürücüsü düzeltmelerinin yalnızca yeni filtre sürücüsü yüklendiğinde ve yalnızca yeniden başlatma sırasında gerçekleşeceğinden etkili olabilmesi için görünür.

> [!NOTE]
> Bu yalnızca bir uyarıdır. Mevcut çoğaltma, yeni aracı güncelleştirmesinden sonra bile çalışmaya devam eder. Yeni filtre sürücüsünün avantajlarını istediğiniz zaman yeniden başlatmayı seçebilirsiniz, ancak yeniden başlatmazsanız eski filtre sürücüsü çalışmaya devam eder.
>
> Filtre sürücüsünden ayrı olarak, Mobility hizmeti güncelleştirmesinde yapılan diğer geliştirmelerin ve düzeltmelerin avantajları, yeniden başlatma gerektirmeden etkili olur.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Çoğaltma yönetilen diski, hedef kaynak grubunda beklenen Etiketler olmadan zaten var olduğundan koruma etkinleştirilemedi (hata kodu 150161)

### <a name="possible-cause"></a>Olası neden

Bu sorun, sanal makine daha önce korunuyorsa ve çoğaltma devre dışı bırakıldığında, çoğaltma diski temizlenmediği zaman meydana gelebilir.

### <a name="fix-the-problem"></a>Sorunu

Hata iletisinde tanımlanan çoğaltma diskini silin ve başarısız koruma işini yeniden deneyin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure sanal makinelerini çoğaltma](site-recovery-replicate-azure-to-azure.md)
