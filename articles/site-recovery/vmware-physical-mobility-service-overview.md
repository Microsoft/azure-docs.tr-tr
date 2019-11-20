---
title: Azure Site Recovery ile VMware VM 'Leri ve fiziksel sunucuları olağanüstü durum kurtarmaya yönelik Mobility hizmeti hakkında | Microsoft Docs
description: Azure Site Recovery hizmetini kullanarak VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarmaya yönelik Mobility hizmeti Aracısı hakkında bilgi edinin.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: aeb00b84ac254232e0d68fd9631fb539a928e67d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931902"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM 'Leri ve fiziksel sunucular için Mobility hizmeti hakkında

[Azure Site Recovery](site-recovery-overview.md)kullanarak VMware VM 'leri ve fiziksel sunucular için olağanüstü durum kurtarmayı ayarlarken, her şirket içi VMware VM 'sine ve fiziksel sunucuya Site Recovery Mobility hizmetini yüklersiniz.  Mobility hizmeti makinede veri yazmaları yakalar ve bunları Site Recovery işlem sunucusuna iletir. Mobility hizmetini aşağıdaki yöntemleri kullanarak dağıtabilirsiniz:

- [Gönderim yükleme](#push-installation): Site Recovery, koruma Azure portal aracılığıyla etkinleştirildiğinde sunucuya Mobility Aracısı 'nı yüklüyor.
- El ile yüklensin: Mobility hizmetini her makineye [Kullanıcı arabirimi](#install-mobility-agent-through-ui) veya [komut istemi](#install-mobility-agent-through-command-prompt)aracılığıyla el ile yükleyebilirsiniz.
- [Otomatik dağıtım](vmware-azure-mobility-install-configuration-mgr.md): System Center Configuration Manager gibi yazılım dağıtım araçlarıyla yüklemeyi otomatik hale getirebilirsiniz.

## <a name="anti-virus-on-replicated-machines"></a>Çoğaltılan makinelerde virüsten koruma

Çoğaltmak istediğiniz makinelerin etkin bir virüsten koruma yazılımı çalışıyorsa, Mobility hizmeti yükleme klasörünü virüsten koruma işlemlerinden (*C:\programdata\asr\agent*) dıştığınızdan emin olun. Bu, çoğaltmanın beklendiği gibi çalıştığından emin olmanızı sağlar.

## <a name="push-installation"></a>Gönderim yükleme

Anında yükleme, portalda tetiklenen "[çoğaltmayı etkinleştir](vmware-azure-enable-replication.md#enable-replication)" işinin ayrılmaz bir parçasıdır. Korumak istediğiniz sanal makine kümesini seçtikten sonra "çoğaltmayı etkinleştir" i tetikledikten sonra, Configuration Server Mobility Aracısı 'nı sunuculara gönderir, aracıyı ve aracıyı Configuration Server ile tamamen kaydını yapar. Bu işlemin başarılı bir şekilde tamamlanması için,

- Tüm gönderme yükleme [önkoşullarının](vmware-azure-install-mobility-service.md) karşılandığından emin olun.
- Tüm sunucu yapılandırmalarının [, VMware 'Den Azure 'A Dr senaryosunda desteklenen bir matris](vmware-physical-azure-support-matrix.md)olduğunu doğrulayın.

Anında yükleme iş akışının ayrıntıları aşağıdaki bölümlerde açıklanmıştır.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>[9,23 sürümünden sonraki sürümler](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

Mobility aracısının anında yüklenmesi sırasında aşağıdaki adımlar gerçekleştirilir

1. Aracıyı kaynak makineye iter. Aracının kaynak makineye kopyalanması, birden çok ortam hatası nedeniyle başarısız olabilir. Anında yükleme hatalarıyla ilgili sorunları gidermek için [kılavuzumuzu](vmware-azure-troubleshoot-push-install.md) ziyaret edin.
2. Aracı sunucuya başarıyla kopyalandıktan sonra sunucuda önkoşul denetimleri gerçekleştirilir. Bir veya daha fazla [Önkoşul](vmware-physical-azure-support-matrix.md) karşılanmazsa yükleme başarısız olur. Tüm önkoşullar karşılanıyorsa, yükleme tetiklenir.
3. Azure Site Recovery VSS sağlayıcısı, Mobility Aracısı yüklemesinin parçası olarak sunucuya yüklenir. Bu sağlayıcı, uygulamayla tutarlı noktaları oluşturmak için kullanılır. VSS sağlayıcısı yüklemesi başarısız olursa, bu adım atlanır ve aracı yüklemesi devam eder.
4. Aracı yüklemesi başarılı olur ancak VSS sağlayıcısı yüklemesi başarısız olursa, iş durumu "uyarı" olarak işaretlenir. Bu, kilitlenme tutarlılığı noktalarının oluşturulmasını etkilemez.

    a. Uygulamayla tutarlı noktaları oluşturmak için Site Recovery VSS sağlayıcısı 'nı el ile yüklemeyi tamamlamaya yönelik [kılavuzumuza](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) bakın. </br>
    b.  Uygulamayla tutarlı noktaların oluşturulmasını istemiyorsanız, uygulamayla tutarlı noktaları devre dışı bırakmak için [çoğaltma ilkesini değiştirin](vmware-azure-set-up-replication.md#create-a-policy) .

### <a name="before-922-versions"></a>9,22 sürümden önce

1. Aracıyı kaynak makineye iter. Aracının kaynak makineye kopyalanması, birden çok ortam hatası nedeniyle başarısız olabilir. Anında yükleme hatalarıyla ilgili sorunları gidermek için [kılavuzumuzu](vmware-azure-troubleshoot-push-install.md) ziyaret edin.
2. Aracı sunucuya başarıyla kopyalandıktan sonra sunucuda önkoşul denetimleri gerçekleştirilir. Bir veya daha fazla [Önkoşul](vmware-physical-azure-support-matrix.md) karşılanmazsa yükleme başarısız olur. Tüm önkoşullar karşılanıyorsa, yükleme tetiklenir.
3. Azure Site Recovery VSS sağlayıcısı, Mobility Aracısı yüklemesinin parçası olarak sunucuya yüklenir. Bu sağlayıcı, uygulamayla tutarlı noktaları oluşturmak için kullanılır. VSS sağlayıcısı yüklemesi başarısız olursa, aracı yüklemesi başarısız olur. Mobility Aracısı yüklemesinde hata oluşmasını önlemek için, kilitlenme tutarlı noktaları oluşturmak ve VSS sağlayıcısını el ile yüklemek için [9,23 sürümünü](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) veya üstünü kullanın.

## <a name="install-mobility-agent-through-ui"></a>Kullanıcı arabirimi aracılığıyla Mobility Aracısı 'nı yükler

### <a name="prerequisite"></a>Önkoşul

- Tüm sunucu yapılandırmalarının [, VMware 'Den Azure 'A Dr senaryosunda desteklenen bir matris](vmware-physical-azure-support-matrix.md)olduğunu doğrulayın.
- Sunucu işletim sistemini temel alan [yükleyiciyi bulun](#locate-installer-files) .

>[!IMPORTANT]
> Azure IaaS VM 'yi bir Azure bölgesinden diğerine çoğaltırken, bu yöntemi kullanmayın. Bunun yerine komut satırı tabanlı yükleme yöntemini kullanın.

1. Yükleme dosyasını makineye kopyalayın ve çalıştırın.
2. **Yükleme seçeneği**içinde **Mobility hizmetini yükleme**' yi seçin.
3. Yükleme **> yükleme**konumunu seçin.

    ![Mobility hizmeti yükleme seçeneği sayfası](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Yükleme **işlemindeki**yüklemeyi izleyin. Yükleme tamamlandıktan sonra, hizmeti yapılandırma sunucusuna kaydetmek için **yapılandırmaya devam et** ' i seçin.

    ![Mobility hizmeti kayıt sayfası](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. **Yapılandırma sunucusu ayrıntıları**' nda, yapılandırdığınız IP adresini ve parolayı belirtin.  

    ![Mobility hizmeti kayıt sayfası](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Kayıt işleminin sona ermesini sağlamak için **Kaydet** ' i seçin.

    ![Mobility hizmeti kaydı son sayfası](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Komut istemi aracılığıyla Mobility Aracısı 'nı yükler

### <a name="prerequisite"></a>Önkoşul

- Tüm sunucu yapılandırmalarının [, VMware 'Den Azure 'A Dr senaryosunda desteklenen bir matris](vmware-physical-azure-support-matrix.md)olduğunu doğrulayın.
- Sunucu işletim sistemini temel alan [yükleyiciyi bulun](#locate-installer-files) .

### <a name="on-a-windows-machine"></a>Bir Windows makinesinde

- Yükleyiciyi korumak istediğiniz sunucuda yerel bir klasöre (örneğin, C:\Temp) kopyalayın.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Aşağıdaki gibi yüklenir:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Aracıyı yapılandırma sunucusuna kaydedin.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Yükleme ayarları
**Ayar** | **Ayrıntılar**
--- | ---
Kullanım | Unifiedagent. exe/role \<MS/MT >/ınstalllocation \<yüklemesi konumu >/Platform "VMware"/Silent
Kurulum günlükleri | %ProgramData%\asrsetuplogs\asrunifiedagentınstaller.log altında
/Role | Zorunlu yükleme parametresi. Mobility hizmeti (MS) veya ana hedefin (MT) yüklenip yüklenmeyeceğini belirtir.
/InstallLocation| İsteğe bağlı parametre. Mobility hizmeti yükleme konumunu (herhangi bir klasör) belirtir.
/Platform | Zorunlu. Mobility hizmetinin yüklü olduğu platformu belirtir. VMware VM 'Leri/fiziksel sunucuları için **VMware** ; Azure VM 'Leri için **Azure** .<br/><br/> Azure VM 'lerini fiziksel makine olarak değerlendiriyorsanız, **VMware**' yi belirtin.
/Silent| İsteğe bağlı. Yükleyicinin sessiz modda çalıştırılıp çalıştırılmayacağını belirtir.

#### <a name="registration-settings"></a>Kayıt ayarları
**Ayar** | **Ayrıntılar**
--- | ---
Kullanım | Unifiedagentconfigurator. exe/csendpoint \<CsIP >/parolalar sefilepath \<parola sefilepath >
Aracı yapılandırma günlükleri | %ProgramData%\asrsetuplogs\asrunifiedagentconfigurator.log altında
/CSEndPoint | Zorunlu parametre. Yapılandırma sunucusunun IP adresini belirtir. Herhangi bir geçerli IP adresi kullanın.
/PassphraseFilePath |  Zorunlu. Parolanın konumu. Geçerli bir UNC veya yerel dosya yolu kullanın.

### <a name="on-a-linux-machine"></a>Bir Linux makinesinde

1. Yükleyiciyi korumak istediğiniz sunucuda yerel bir klasöre (örneğin,/tmp) kopyalayın. Bir terminalde aşağıdaki komutları çalıştırın:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Aşağıdaki gibi yüklenir:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Yükleme tamamlandıktan sonra, Mobility hizmetinin yapılandırma sunucusuna kayıtlı olması gerekir. Mobility hizmetini yapılandırma sunucusuna kaydetmek için aşağıdaki komutu çalıştırın:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Yükleme ayarları
**Ayar** | **Ayrıntılar**
--- | ---
Kullanım | ./install-d \<install location >-r \<MS/MT >-v VMware-q
-r | Zorunlu yükleme parametresi. Mobility hizmeti (MS) veya ana hedefin (MT) yüklenip yüklenmeyeceğini belirtir.
-d | İsteğe bağlı parametre. Mobility hizmeti yükleme konumunu belirtir:/usr/local/ASR.
-v | Zorunlu. Mobility hizmetinin yüklü olduğu platformu belirtir. VMware VM 'Leri/fiziksel sunucuları için **VMware** ; Azure VM 'Leri için **Azure** .
-q | İsteğe bağlı. Yükleyicinin sessiz modda çalıştırılıp çalıştırılmayacağını belirtir.

#### <a name="registration-settings"></a>Kayıt ayarları
**Ayar** | **Ayrıntılar**
--- | ---
Kullanım | CD/usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh-i \<CsIP >-P \<parola sefilepath >
-ı | Zorunlu parametre. Yapılandırma sunucusunun IP adresini belirtir. Herhangi bir geçerli IP adresi kullanın.
-P |  Zorunlu. Parolasının kaydedildiği dosyanın tam dosya yolu. Geçerli bir klasör kullanın.

## <a name="azure-virtual-machine-agent"></a>Azure sanal makine Aracısı

- **Windows VM 'leri**: Mobility hizmetinin 9.7.0.0 sürümünde [Azure VM Aracısı](../virtual-machines/extensions/features-windows.md#azure-vm-agent) , Mobility hizmeti yükleyicisi tarafından yüklenir. Bu, makinenin Azure 'a devredilmesini sağlar, Azure VM, herhangi bir VM uzantısını kullanmak için aracı yükleme önkoşullarını karşılar.
- **Linux VM 'leri**: Yük devretme işleminden sonra [Walınuxagent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) 'ıN Azure VM 'ye el ile yüklenmesi gerekir.

## <a name="locate-installer-files"></a>Yükleyici dosyalarını bul

Yapılandırma sunucusundaki%ProgramData%\asr\home\svsystems\pushınstallsvc\repository klasörüne gidin. İşletim sistemine bağlı olarak hangi yükleyicide ihtiyacınız olduğunu denetleyin. Aşağıdaki tabloda, her VMware VM ve fiziksel sunucu işletim sistemi için yükleyici dosyaları özetlenmektedir. Başlamadan önce [desteklenen işletim sistemlerini](vmware-physical-azure-support-matrix.md#replicated-machines) gözden geçirebilirsiniz.

**Yükleyici dosyası** | **İşletim sistemi (yalnızca 64 bit)**
--- | ---
Microsoft-ASR\_UA\*Windows\*Release. exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*Release. tar. gz | Red Hat Enterprise Linux (RHEL) 6. * </br> CentOS 6. *
Microsoft-ASR\_UA\*RHEL7-64\*Release. tar. gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7. *
Microsoft-ASR\_UA\*SLES12-64\*Release. tar. gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*Release. tar. gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*Release. tar. gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*Release. tar. gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*Ubuntu-14.04-64\*Release. tar. gz | Ubuntu Linux 14,04
Microsoft-ASR\_UA\*Ubuntu-16.04-64\*Release. tar. gz | Ubuntu Linux 16,04 LTS sunucusu
Microsoft ASR_UA\*DEBIAN7 64\*release.tar.gz | Deyi 7
Microsoft ASR_UA\*DEBIAN8 64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Sonraki adımlar

[Mobility hizmeti için anında yükleme ayarlayın](vmware-azure-install-mobility-service.md).
