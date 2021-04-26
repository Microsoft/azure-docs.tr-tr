---
title: Azure Site Recovery Azure VM çoğaltma sorunlarını giderme
description: Olağanüstü durum kurtarma için Azure sanal makinelerini çoğaltma sırasında oluşan sorunları giderin.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900425"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure’dan Azure’a VM çoğaltma hatalarını giderme

Bu makalede, Azure sanal makinelerini (VM) bir bölgeden diğerine çoğaltma ve kurtarma işlemleri sırasında Azure Site Recovery yaygın olarak karşılaşılan hataların nasıl giderileceği açıklanmaktadır. Desteklenen konfigürasyonlar hakkında daha fazla bilgi için bkz. [Azure VM 'leri çoğaltmak için destek matrisi](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure Kaynak kotası sorunları (hata kodu 150097)

Aboneliğinizin, olağanüstü durum kurtarma (DR) bölgeniz olarak kullanmayı planladığınız hedef bölgede Azure VM 'Leri oluşturmak için etkinleştirildiğinden emin olun. Aboneliğinizin gereken boyutlarda VM 'Ler oluşturmak için yeterli kotası olmalıdır. Varsayılan olarak Site Recovery, kaynak VM boyutuyla aynı olan bir hedef VM boyutu seçer. Eşleşen boyut kullanılamıyorsa, Site Recovery otomatik olarak kullanılabilir en yakın boyutu seçer.

Kaynak VM yapılandırmasını destekleyen bir boyut yoksa, aşağıdaki ileti görüntülenir:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Olası nedenler

- Abonelik KIMLIĞINIZ, hedef bölge konumunda herhangi bir sanal makine oluşturmak için etkinleştirilmemiş.
- Hedef bölge konumunda belirli VM boyutlarını oluşturmak için abonelik KIMLIĞINIZ etkin değil veya yeterli kotayı yok.
- Hedef bölge konumundaki abonelik KIMLIĞI için kaynak VM 'nin ağ arabirimi kartı (NIC) sayısıyla (2) eşleşecek uygun bir hedef VM boyutu bulunamadı.

### <a name="fix-the-problem"></a>Sorunu çözme

Aboneliğinizi hedef konumda gereken boyutlarda VM 'Ler oluşturacak şekilde etkinleştirmek için [Azure Faturalandırma desteği](../azure-portal/supportability/resource-manager-core-quotas-request.md) 'ne başvurun. Sonra başarısız olan işlemi yeniden deneyin.

Hedef konumun bir kapasite kısıtlaması varsa, bu konuma yinelemeyi devre dışı bırakın. Daha sonra, aboneliğin gereken boyutlarda VM 'Ler oluşturmak için yeterli kotası olan farklı bir konuma çoğaltmayı etkinleştirin.

## <a name="trusted-root-certificates-error-code-151066"></a>Güvenilen kök sertifikalar (hata kodu 151066)

VM 'de en son güvenilen kök sertifikaların tümü yoksa, Site Recovery için çoğaltmayı etkinleştirme işiniz başarısız olabilir. VM 'deki Site Recovery hizmet çağrılarının kimlik doğrulaması ve yetkilendirmesi Bu sertifikalar olmadan başarısız oluyor.

Çoğaltma etkinleştirme işi başarısız olursa, aşağıdaki ileti görüntülenir:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Olası nedeni

Yetkilendirme ve kimlik doğrulaması için gerekli olan güvenilen kök sertifikalar, sanal makinede mevcut değil.

### <a name="fix-the-problem"></a>Sorunu çözme

#### <a name="windows"></a>Windows

Windows işletim sistemini çalıştıran bir VM için, tüm güvenilen kök sertifikaların VM 'de mevcut olması için en son Windows güncelleştirmelerini yükler. En son kök sertifikaları ve VM 'lerde güncelleştirilmiş sertifika iptal listesini almak için kuruluşunuzdaki tipik Windows Update yönetimi veya sertifika güncelleştirme yönetimi sürecini izleyin.

- Bağlantısı kesilen bir ortamındaysanız, sertifikaları almak için kuruluşunuzdaki standart Windows güncelleştirme işlemini izleyin.
- Gerekli sertifikalar VM 'de yoksa, Site Recovery hizmetine yapılan çağrılar güvenlik nedenleriyle başarısız olur.

Sorunun çözümlendiğini doğrulamak için, `login.microsoftonline.com` sanal makinenizde bir tarayıcıdan bölümüne gidin.

Daha fazla bilgi için bkz. [Güvenilen kökleri ve izin verilmeyen sertifikaları yapılandırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

En son güvenilen kök sertifikalarını ve VM 'deki en son sertifika iptal listesini almak için Linux işletim sistemi sürümünüzün dağıtıcısında sunulan yönergeleri izleyin.

SUSE Linux sembolik bağlantılar veya symbağlantıları kullandığından, bir sertifika listesini sürdürmek için aşağıdaki adımları izleyin:

1. **Kök** Kullanıcı olarak oturum açın. Karma simgesi ( `#` ) varsayılan komut istemindeki bir değer.

1. Dizini değiştirmek için şu komutu çalıştırın:

   `cd /etc/ssl/certs`

1. Symantec kök CA sertifikasının mevcut olup olmadığını denetleyin:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Symantec kök CA sertifikası bulunamazsa, dosyayı indirmek için aşağıdaki komutu çalıştırın. Herhangi bir hata olup olmadığını denetleyin ve ağ hataları için önerilen eylemleri izleyin.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Baltidaha fazla kök CA sertifikasının mevcut olup olmadığını denetleyin:

   `ls Baltimore_CyberTrust_Root.pem`

   - Baltidaha fazla kök CA sertifikası bulunamazsa, sertifikayı indirmek için şu komutu çalıştırın:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. DigiCert_Global_Root_CA sertifikasının mevcut olup olmadığını denetleyin:

   `ls DigiCert_Global_Root_CA.pem`

    - DigiCert_Global_Root_CA bulunamazsa, sertifikayı indirmek için aşağıdaki komutları çalıştırın:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Yeni indirilen sertifikaların sertifika konu karmalarını güncelleştirmek için yeniden karma betiği çalıştırın:

   `c_rehash`

1. Sertifikalar için konu karmalarını çözümlemeyin olarak oluşturulup oluşturulmayacağını denetlemek için şu komutları çalıştırın:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Dosya adı _b204d74a. 0_ olan _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. ped_ dosyasının bir kopyasını oluşturun:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Dosya adı _653b494a. 0_ olan _Baltimore_CyberTrust_Root. ped_ dosyasının bir kopyasını oluşturun:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Dosya adı _3513523f. 0_ olan _DigiCert_Global_Root_CA. ped_ dosyasının bir kopyasını oluşturun:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Dosyaların mevcut olup olmadığını denetleyin:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Giden URL 'Ler veya IP aralıkları (hata kodu 151037 veya 151072)

Site Recovery çoğaltmanın çalışması için, VM 'den belirli URL 'lere giden bağlantı gerekir. VM 'niz bir güvenlik duvarının arkasındaysa veya giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsa, bu sorunlardan birini görebilirsiniz. URL 'Ler aracılığıyla giden erişimi desteklemeye devam ediyoruz, ancak bir IP aralıkları listesinin kullanılması artık desteklenmiyor.

#### <a name="possible-causes"></a>Olası nedenler

- Etki alanı adı sistemi (DNS) çözümleme hatası nedeniyle uç noktalara Site Recovery bağlantı oluşturulamıyor.
- Bu sorun, sanal makine yük devretmeye çalıştığınızda, ancak DNS sunucusuna olağanüstü durum kurtarma (DR) bölgesinden ulaşılamadığında yeniden koruma sırasında daha yaygındır.

#### <a name="fix-the-problem"></a>Sorunu çözme

Özel DNS kullanıyorsanız, olağanüstü durum kurtarma bölgesinden DNS sunucusuna erişilebildiğinden emin olun.

VM 'nin özel bir DNS ayarı kullanıp kullanmadığını denetlemek için:

1. **Sanal makineleri** açın ve VM 'yi seçin.
1. VM 'Ler **ayarlarına** gidin ve **ağ**' ı seçin.
1. **Sanal ağ/alt ağ**' da, sanal ağın kaynak sayfasını açmak için bağlantıyı seçin.
1. **Ayarlar** ' a gidin ve **DNS sunucuları**' nı seçin.

DNS sunucusuna sanal makineden erişmeyi deneyin. DNS sunucusu erişilebilir değilse, DNS sunucusu üzerinden yük devrederden veya DR ağı ile DNS arasında site satırı oluşturarak erişilebilir hale getirin.

> [!NOTE]
> Özel uç noktalar kullanıyorsanız, VM 'Lerin özel DNS kayıtlarını çözümleyediğinden emin olun.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-hata.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Sorun 2: Site Recovery yapılandırma başarısız oldu (151196)

#### <a name="possible-cause"></a>Olası nedeni

Microsoft 365 kimlik doğrulaması ve kimlik ıP4 uç noktalarına bir bağlantı kurulamazsa.

#### <a name="fix-the-problem"></a>Sorunu çözme

Kimlik doğrulaması için Microsoft 365 IP aralıklarına gereken Azure Site Recovery erişimi.
VM 'deki giden ağ bağlantısını denetlemek için Azure ağ güvenlik grubu (NSG) kuralları/güvenlik duvarı proxy 'si kullanıyorsanız, AAD 'ye erişim izni vermek için [Azure Active Directory (AAD) hizmet etiketi](../virtual-network/network-security-groups-overview.md#service-tags) tabanlı NSG kuralını kullandığınızdan emin olun. IP adresi tabanlı NSG kurallarını artık desteklemiyoruz.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Sorun 3: Site Recovery yapılandırma başarısız oldu (151197)

#### <a name="possible-cause"></a>Olası nedeni

Hizmet uç noktalarına Azure Site Recovery bir bağlantı kurulamazsa.

#### <a name="fix-the-problem"></a>Sorunu çözme

VM 'deki giden ağ bağlantısını denetlemek için Azure ağ güvenlik grubu (NSG) kuralları/güvenlik duvarı proxy 'si kullanıyorsanız, hizmet etiketlerini kullandığınızdan emin olun. Azure Site Recovery için NSG 'ler aracılığıyla bir IP adresi listesi kullanımını artık desteklemiyoruz.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Sorun 4: ağ trafiği şirket içi ara sunucu kullandığında çoğaltma başarısız olur (151072)

#### <a name="possible-cause"></a>Olası nedeni

Özel ara sunucu ayarları geçersiz ve Mobility hizmeti Aracısı, proxy ayarlarını Internet Explorer 'dan (IE) otomatik olarak algılamadım.

#### <a name="fix-the-problem"></a>Sorunu çözme

1. Mobility hizmeti Aracısı Windows ve Linux üzerinde IE 'deki proxy ayarlarını algılar `/etc/environment` .
1. Yalnızca Mobility hizmeti için ara sunucu ayarlamayı tercih ediyorsanız, proxy ayrıntılarını şurada bulunan _ProxyInfo. conf_ dosyasında sağlayabilirsiniz:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ _dosyası aşağıdaki ını_ biçiminde proxy ayarlarına sahip olmalıdır.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Mobility hizmeti Aracısı yalnızca **kimliği doğrulanmamış proxy 'leri** destekler.

### <a name="more-information"></a>Daha fazla bilgi

[Gerekli URL 'leri](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) veya [gerekli IP aralıklarını](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)belirtmek için [Azure 'Da Azure 'da ağ iletişimi hakkında](azure-to-azure-about-networking.md)bölümündeki yönergeleri izleyin.

## <a name="disk-not-found-in-vm-error-code-150039"></a>VM 'de disk bulunamadı (hata kodu 150039)

VM 'ye bağlı yeni bir disk başlatılmalıdır. Disk bulunamazsa, aşağıdaki ileti görüntülenir:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Olası nedenler

- VM 'ye yeni bir veri diski iliştirilmiş ancak başlatılmamış.
- VM içindeki veri diski, diskin sanal makineye eklendiği mantıksal birim numarası (LUN) değerini doğru şekilde bildirmiyor.

### <a name="fix-the-problem"></a>Sorunu çözme

Veri disklerinin başlatıldığından emin olun ve işlemi yeniden deneyin.

- **Windows**: [Yeni bir disk ekleyin ve başlatın](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [Linux 'ta yeni bir veri diski başlatın](../virtual-machines/linux/add-disk.md).

Sorun devam ederse desteğe başvurun.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Koruma için birden çok disk var (hata kodu 153039)

### <a name="possible-causes"></a>Olası nedenler

- Yakın zamanda sanal makineye korumadan sonra bir veya daha fazla disk eklendi.
- Sanal makine korunduktan sonra bir veya daha fazla disk başlatıldı.

### <a name="fix-the-problem"></a>Sorunu çözme

VM 'nin çoğaltma durumunu yeniden sağlıklı hale getirmek için, diskleri korumayı ya da uyarıyı kapatmak için seçeneğini belirleyebilirsiniz.

#### <a name="to-protect-the-disks"></a>Diskleri korumak için

1. **Çoğaltılan öğeler**  >  _VM adı_  >  **disklere** gidin.
1. Korumasız disk ' i seçin ve ardından **çoğaltmayı etkinleştir**' i seçin:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="VM disklerinde çoğaltmayı etkinleştirin.":::

#### <a name="to-dismiss-the-warning"></a>Uyarıyı kapatmak için

1. **Çoğaltılan öğeler**  >  _sanal makine adına_ gidin.
1. **Genel bakış** bölümünde uyarıyı seçin ve ardından **Tamam**' ı seçin.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Yeni disk uyarısını kapat.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>Kasadan kaldırılan VM, bilgilerle tamamlandı (hata kodu 150225)

Site Recovery sanal makineyi koruduğunda, kaynak sanal makinede bağlantılar oluşturur. Korumayı kaldırdığınızda veya çoğaltmayı devre dışı bıraktığınızda, Site Recovery bu bağlantıları temizleme işinin bir parçası olarak kaldırır. Sanal makinede bir kaynak kilidi varsa, temizleme işi bilgilerle tamamlanır. Bu bilgiler, sanal makinenin kurtarma hizmetleri kasasından kaldırıldığını, ancak eski bağlantıların bazılarının kaynak makinede temizlendiğinden emin olduğunu söyler.

Bu sanal makineyi yeniden korumayı hiç düşünmüyorsanız, bu uyarıyı yoksayabilirsiniz. Ancak bu sanal makineyi daha sonra korumanız gerekiyorsa, bağlantıları temizlemek için bu bölümdeki adımları izleyin.

> [!WARNING]
> Temizleme işlemini yapmazsanız:
>
> - Kurtarma Hizmetleri Kasası aracılığıyla çoğaltmayı etkinleştirdiğinizde, sanal makine listelenmez.
> - VM 'yi **sanal makine**  >  **ayarları**  >  **olağanüstü durum kurtarma** kullanarak korumaya çalışırsanız, **VM 'deki var olan eski kaynak bağlantıları nedeniyle işlem ileti çoğaltmasıyla** başarısız olur.

### <a name="fix-the-problem"></a>Sorunu çözme

> [!NOTE]
> Site Recovery, kaynak sanal makineyi silmez veya bu adımları gerçekleştirirken herhangi bir şekilde etkilemez.

1. Kilidi VM veya VM kaynak grubundan kaldırın. Örneğin, aşağıdaki görüntüde adlı VM 'deki kaynak kilidi `MoveDemo` silinmelidir:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Sanal makineyi kilitle 'yi kaldırın.":::

1. [Eski bir Site Recovery yapılandırmasını kaldırmak](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)için betiği indirin.
1. _Cleanup-stale-asr-config-Azure-VM.ps1_ betiği çalıştırın. **ABONELIK kimliği**, **VM kaynak grubu** ve **VM adını** parametre olarak belirtin.
1. Azure kimlik bilgileri istenirse, bunları sağlayın. Sonra betiğin hatasız çalıştığını doğrulayın.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Çoğaltma, eski kaynaklarla VM 'de etkin değil (hata kodu 150226)

### <a name="possible-causes"></a>Olası nedenler

Sanal makinenin önceki Site Recovery korumasından eski bir yapılandırması vardır.

Azure VM için Site Recovery kullanarak çoğaltmayı etkinleştirdiyseniz, eski bir yapılandırma bir Azure VM üzerinde gerçekleşebilir ve sonra şunları yapabilirsiniz:

- Çoğaltmayı devre dışı bırakmış, ancak kaynak VM 'nin bir kaynak kilidi vardı.
- SANAL makinede çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını silmiş olursunuz.
- VM 'de çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını içeren kaynak grubunu silmiş olursunuz.

### <a name="fix-the-problem"></a>Sorunu çözme

> [!NOTE]
> Site Recovery, kaynak sanal makineyi silmez veya bu adımları gerçekleştirirken herhangi bir şekilde etkilemez.

1. Kilidi VM veya VM kaynak grubundan kaldırın. Örneğin, aşağıdaki görüntüde adlı VM 'deki kaynak kilidi `MoveDemo` silinmelidir:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Sanal makineyi kilitle 'yi kaldırın.":::

1. [Eski bir Site Recovery yapılandırmasını kaldırmak](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)için betiği indirin.
1. _Cleanup-stale-asr-config-Azure-VM.ps1_ betiği çalıştırın. **ABONELIK kimliği**, **VM kaynak grubu** ve **VM adını** parametre olarak belirtin.
1. Azure kimlik bilgileri istenirse, bunları sağlayın. Sonra betiğin hatasız çalıştığını doğrulayın.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Çoğaltmayı etkinleştir işinde VM veya kaynak grubu seçemezsiniz

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Sorun 1: kaynak grubu ve kaynak VM farklı konumlarda

Site Recovery Şu anda kaynak bölgesi kaynak grubunun ve sanal makinelerin aynı konumda olmasını gerektirir. Bu değillerse, korumayı uygulamaya çalıştığınızda sanal makineyi veya kaynak grubunu bulamayacaksınız.

Geçici bir çözüm olarak, kurtarma hizmetleri Kasası yerine VM 'den çoğaltmayı etkinleştirebilirsiniz. **Kaynak VM**  >  **özellikleri**  >  **olağanüstü durum kurtarma** ' ya gidin ve çoğaltmayı etkinleştirin.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Sorun 2: kaynak grubu seçili aboneliğin bir parçası değil

Kaynak grubu seçili aboneliğin bir parçası değilse, koruma sırasında kaynak grubunu bulamamanız mümkün olmayabilir. Kaynak grubunun, kullanmakta olduğunuz aboneliğe ait olduğundan emin olun.

### <a name="issue-3-stale-configuration"></a>Sorun 3: eski yapılandırma

Azure VM 'de eski bir Site Recovery yapılandırması varsa, çoğaltma için etkinleştirmek istediğiniz VM 'yi göremeyebilirsiniz. Bu durum, Site Recovery kullanarak Azure VM için çoğaltmayı etkinleştirdiyseniz oluşabilir ve ardından:

- SANAL makinede çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını silmiş olursunuz.
- VM 'de çoğaltmayı açıkça devre dışı bırakmadan Site Recovery kasasını içeren kaynak grubunu silmiş olursunuz.
- Çoğaltmayı devre dışı bırakmış, ancak kaynak VM 'nin bir kaynak kilidi vardı.

### <a name="fix-the-problem"></a>Sorunu çözme

> [!NOTE]
> `AzureRM.Resources`Bu bölümde bahsedilen betiği kullanmadan önce modülü güncelleştirdiğinizden emin olun. Site Recovery, kaynak sanal makineyi silmez veya bu adımları gerçekleştirirken herhangi bir şekilde etkilemez.

1. Varsa kilidi VM veya VM kaynak grubundan kaldırın. Örneğin, aşağıdaki görüntüde adlı VM 'deki kaynak kilidi `MoveDemo` silinmelidir:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Sanal makineyi kilitle 'yi kaldırın.":::

1. [Eski bir Site Recovery yapılandırmasını kaldırmak](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)için betiği indirin.
1. _Cleanup-stale-asr-config-Azure-VM.ps1_ betiği çalıştırın. **ABONELIK kimliği**, **VM kaynak grubu** ve **VM adını** parametre olarak belirtin.
1. Azure kimlik bilgileri istenirse, bunları sağlayın. Sonra betiğin hatasız çalıştığını doğrulayın.

## <a name="unable-to-select-a-vm-for-protection"></a>Koruma için VM seçimi yapılamıyor

### <a name="possible-cause"></a>Olası nedeni

Sanal makine, başarısız veya yanıt vermeyen bir durumda yüklenmiş bir uzantıya sahip

### <a name="fix-the-problem"></a>Sorunu çözme

**Sanal makine**  >  **ayarları**  >  **uzantılarına** gidin ve başarısız durumundaki uzantıları denetleyin. Başarısız olan tüm uzantıları kaldırın ve ardından sanal makineyi korumayı yeniden deneyin.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>VM sağlama durumu geçerli değil (hata kodu 150019)

VM 'de çoğaltmayı etkinleştirmek için, sağlama durumunun **başarılı** olması gerekir. Sağlama durumunu denetlemek için şu adımları izleyin:

1. Azure portal, **tüm hizmetlerden** **Kaynak Gezgini** seçin.
1. **Abonelikler** listesini genişletin ve aboneliğinizi seçin.
1. **ResourceGroups** listesini GENIŞLETIN ve VM 'nin kaynak grubunu seçin.
1. **Kaynaklar** listesini GENIŞLETIN ve VM 'nizi seçin.
1. Sağ taraftaki örnek görünümünde **Provisioningstate** alanını denetleyin.

### <a name="fix-the-problem"></a>Sorunu çözme

- **Provisioningstate** **başarısız** olursa, sorun gidermeye yönelik ayrıntılarla desteğe başvurun.
- **Provisioningstate** **güncelleştiriyoruz**, başka bir uzantı dağıtılmış olabilir. VM 'de devam eden bir işlem olup olmadığını denetleyin, bunların tamamlanmasını bekleyin ve ardından çoğaltmayı etkinleştirmek için başarısız Site Recovery işini yeniden deneyin.

## <a name="unable-to-select-target-vm"></a>Hedef VM seçimi yapılamıyor

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Sorun 1: VM, zaten hedef ağa eşlenmiş bir ağa bağlı

Olağanüstü durum kurtarma yapılandırması sırasında, kaynak VM bir sanal ağın parçasıysa ve aynı sanal ağdaki başka bir VM, hedef kaynak grubundaki bir ağla zaten eşlenmişse, varsayılan olarak ağ seçimi açılır liste kutusu kullanılamaz (soluk görünür).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Ağ seçim listesi kullanılamıyor.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Sorun 2: daha önce VM 'yi koruduktan sonra çoğaltmayı devre dışı bırakmış olursunuz

VM çoğaltmasını devre dışı bırakmak Ağ eşlemesini silmez. Eşleme, VM 'nin korunduğu kurtarma hizmetleri kasasından silinmelidir. **Kurtarma Hizmetleri kasasını** seçin ve   >    >  **Azure sanal makineler**  >  **ağ eşlemesi** için Site Recovery altyapısını Yönet ' e gidin.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Ağ eşlemesini silin.":::

Olağanüstü durum kurtarma kurulumu sırasında yapılandırılan hedef ağ, İlk kurulumdan sonra ve VM korunduktan sonra değiştirilebilir. **Ağ eşlemesini değiştirmek** için ağ adını seçin:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Ağ eşlemesini değiştir.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ veya VSS (hata kodu 151025)

COM+ veya Birim Gölge Kopyası Hizmeti (VSS) hatası oluştuğunda aşağıdaki ileti görüntülenir:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Olası nedenler

- COM+ sistem uygulaması hizmeti devre dışı bırakıldı.
- Birim Gölge Kopyası Hizmeti devre dışı bırakıldı.

### <a name="fix-the-problem"></a>Sorunu çözme

COM+ sistem uygulamasını ve Birim Gölge Kopyası Hizmeti otomatik veya el ile başlatma moduna ayarlayın.

1. Windows 'da Hizmetler konsolunu açın.
1. COM+ sistem uygulamasının ve Birim Gölge Kopyası Hizmeti, **Başlangıç türü** olarak **devre dışı** olarak ayarlanmamış olduğundan emin olun.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="COM ve sistem uygulamasının başlangıç türünü ve Birim Gölge Kopyası Hizmeti denetleyin.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Desteklenmeyen yönetilen disk boyutu (hata kodu 150172)

Bu hata oluştuğunda, aşağıdaki ileti görüntülenir:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Olası nedeni

Disk, desteklenen 1024 MB boyutundan daha küçük.

### <a name="fix-the-problem"></a>Sorunu çözme

Disk boyutunun desteklenen boyut aralığında olduğundan emin olun ve işlemi yeniden deneyin.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>GRUB cihaz adı kullandığında Koruma etkinleştirilmemiş (hata kodu 151126)

### <a name="possible-causes"></a>Olası nedenler

Linux genel Birleşik önyükleme yükleyicisi (GRUB) yapılandırma dosyaları (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/GRUB2/grub.cfg_ veya _/etc/default/grub_), ve parametreleri için evrensel benzersiz tanımlayıcı (UUID) değerleri yerine gerçek cihaz adlarını belirtebilir `root` `resume` . Site Recovery, cihaz adları değiştirebildiğinden UUID 'ler gerektirir. Yeniden başlatma sonrasında bir VM, yük devretme sırasında aynı ada sahip olmayabilir ve sorunlar ortaya çıkabilir.

Aşağıdaki örnekler, gerekli UUID 'ler yerine cihaz adlarının göründüğü GRUB dosyalarından alınan satırlardan oluşur:

- Dosya _/Boot/GRUB2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Dosya: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Sorunu çözme

Her bir cihaz adını karşılık gelen UUID ile değiştirin:

1. Komutu yürüterek cihazın UUID 'sini bulun `blkid <device name>` . Örnek:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Cihaz adını, ve biçimlerinde, UUID 'SI ile değiştirin `root=UUID=<UUID>` `resume=UUID=<UUID>` . Örneğin, değiştirildikten sonra _/boot/grub/menu.lst_ öğesinden gelen satır aşağıdaki satırı şöyle görünür:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Korumayı yeniden deneyin.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>GRUB cihazı mevcut olmadığından koruma başarısız oldu (hata kodu 151124)

### <a name="possible-cause"></a>Olası nedeni

GRUB yapılandırma dosyaları (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/GRUB2/grub.cfg_ veya _/etc/default/grub_), veya parametrelerini içerebilir `rd.lvm.lv` `rd_LVM_LV` . Bu parametreler, önyükleme zamanında keşfedilecek mantıksal birim Yöneticisi (LVM) cihazlarını belirler. Bu LVM cihazları yoksa, korunan sistem önyüklenmez ve önyükleme sürecinde takılacaktır. Ayrıca, yük devretme VM 'si ile aynı sorun da görünür. İşte birkaç örnek:

- Dosya: _/Boot/GRUB2/grub.cfg_ on RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Dosya: RHEL7 üzerinde _/etc/default/grub_ :

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Dosya: _/boot/grub/menu.lst_ on RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

Her örnekte, GRUB 'nin adlarıyla `root` ve birim grubundan ıkı LVM cihazı algılaması gerekir `swap` `rootvg` .

### <a name="fix-the-problem"></a>Sorunu çözme

LVM cihazı yoksa, bunu oluşturun ya da GRUB yapılandırma dosyalarından ilgili parametreleri kaldırın. Ardından, korumayı etkinleştirmeyi yeniden deneyin.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Mobility hizmeti güncelleştirmesi uyarılarla tamamlandı (hata kodu 151083)

Site Recovery Mobility hizmetinde, biri filtre sürücüsü olarak adlandırılan birçok bileşen vardır. Filtre sürücüsü yalnızca sistem yeniden başlatma sırasında sistem belleğine yüklenir. Mobility hizmeti güncelleştirmesi filtre sürücüsü değişikliklerini içerdiğinde makine güncelleştirilir ancak bazı düzeltmelerin yeniden başlatma gerektirdiğini bildiren bir uyarı görürsünüz. Bu uyarı, filtre sürücüsü düzeltmelerinin yalnızca yeni filtre sürücüsü yüklendiğinde ve yalnızca yeniden başlatma sırasında gerçekleşeceğinden etkili olabilmesi için görünür.

> [!NOTE]
> Bu yalnızca bir uyarıdır. Mevcut çoğaltma, yeni aracı güncelleştirmesinden sonra bile çalışmaya devam eder. Yeni filtre sürücüsünün avantajlarını istediğiniz zaman yeniden başlatmayı seçebilirsiniz, ancak yeniden başlatmazsanız eski filtre sürücüsü çalışmaya devam eder.
>
> Filtre sürücüsünden ayrı olarak, Mobility hizmeti güncelleştirmesinde yapılan diğer geliştirmelerin ve düzeltmelerin avantajları, yeniden başlatma gerektirmeden etkili olur.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Çoğaltma yönetilen diski varsa koruma etkin değil

Bu hata, çoğaltma yönetilen diski, hedef kaynak grubunda beklenen Etiketler olmadan zaten mevcutsa oluşur.

### <a name="possible-cause"></a>Olası nedeni

Bu sorun, sanal makine daha önce korunuyorsa ve çoğaltma devre dışı bırakıldığında çoğaltma diski kaldırılmamışsa meydana gelebilir.

### <a name="fix-the-problem"></a>Sorunu çözme

Hata iletisinde tanımlanan çoğaltma diskini silin ve başarısız koruma işini yeniden deneyin.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Yükleyici kök diski bulamadığı için korumayı etkinleştirme başarısız oldu (hata kodu 151137)

Bu hata, işletim sistemi diskinin Azure disk şifrelemesi (ADE) kullanılarak şifrelendiği Linux makinelerinde oluşur. Bu yalnızca aracı sürümü 9,35 ' de geçerli bir sorundur.

### <a name="possible-causes"></a>Olası Nedenler

Yükleyici, kök dosya sistemini barındıran kök diski bulamıyor.

### <a name="fix-the-problem"></a>Sorunu çözme

Bu sorunu gidermeye yönelik aşağıdaki adımları izleyin-

1. Aşağıdaki komutu kullanarak RHEL ve CentOS makinelerinde _/var/lib/waagent_ dizininde bulunan aracı bitlerini bulun: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Beklenen çıktı:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Yeni bir dizin oluşturun ve dizini bu yeni dizin ile değiştirin.
3. Aşağıdaki komutu kullanarak buradaki ilk adımda bulunan aracı dosyasını ayıklayın:

    `tar -xf <Tar Ball File>`

4. Dosya _prereq_check_installer.js_ açın ve aşağıdaki satırları silin. Dosyayı kaydettikten sonra kaydedin.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Şu komutu kullanarak yükleyiciyi çağırın: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Yükleyici başarılı olursa, çoğaltmayı etkinleştir işini yeniden deneyin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure sanal makinelerini başka bir Azure bölgesine çoğaltma](azure-to-azure-how-to-enable-replication.md)