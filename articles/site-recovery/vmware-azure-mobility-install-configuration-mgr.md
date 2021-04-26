---
title: Azure Site Recovery 'da yüklemenin olağanüstü durum kurtarması için Mobility hizmetini otomatikleştirin
description: Azure Site Recovery ile VMware/fiziksel sunucu olağanüstü durum kurtarma için Mobility hizmetini otomatik olarak yüklemek.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92544826"
---
# <a name="automate-mobility-service-installation"></a>Mobility hizmeti yüklemesini otomatikleştirin

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)' de Mobility hizmeti Aracısı için yükleme ve güncelleştirmelerin nasıl otomatikleştirilmesi açıklanmaktadır.

Şirket içi VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için Site Recovery dağıtırken, çoğaltmak istediğiniz her makineye Mobility hizmet aracısını yüklersiniz. Mobility hizmeti makinede veri yazmaları yakalar ve bunları çoğaltma için Site Recovery işlem sunucusuna iletir. Mobility hizmetini birkaç yolla dağıtabilirsiniz:

- **Anında yükleme**: Azure Portal bir makine için çoğaltmayı etkinleştirdiğinizde Mobility hizmet aracısını Site Recovery yüklemesine izin verin.
- **El ile yükleme**: Mobility hizmetini her makineye el ile yükleme. Gönderim ve el ile yükleme hakkında [daha fazla bilgi edinin](vmware-physical-mobility-service-overview.md) .
- **Otomatik dağıtım**: Microsoft uç noktası Configuration Manager gibi yazılım dağıtım araçlarıyla veya jetpatch gibi üçüncü taraf araçlarla yüklemeyi otomatikleştirin.

Otomatik yükleme ve güncelleştirme, şu durumlarda bir çözüm sağlar:

- Kuruluşunuz, korumalı sunucularda anında yüklemeye izin vermiyor.
- Şirket ilkeniz, parolaların düzenli aralıklarla değiştirilmesini gerektirir. Anında yükleme için bir parola belirtmeniz gerekir.
- Güvenlik ilkeniz belirli makineler için güvenlik duvarı özel durumlarının eklenmesine izin vermez.
- Bir barındırma hizmeti sağlayıcısı görevi gören ve Site Recovery ile anında yükleme için gereken müşteri makine kimlik bilgilerini sağlamak istemiyoruz.
- Aracı yüklemelerini aynı anda çok sayıda sunucuya ölçeklendirmeniz gerekir.
- Planlı bakım pencereleri sırasında yüklemeleri ve yükseltmeleri zamanlamak istiyorsunuz.

## <a name="prerequisites"></a>Önkoşullar

Yüklemeyi otomatikleştirmek için aşağıdaki öğeler gereklidir:

- [Configuration Manager](/configmgr/) veya [jetpatch](https://jetpatch.com/microsoft-azure-site-recovery/)gibi dağıtılan bir yazılım yükleme çözümü.
- VMware olağanüstü durum kurtarması veya [fiziksel sunucu](physical-azure-disaster-recovery.md) olağanüstü durum kurtarma için [Azure](tutorial-prepare-azure.md) 'da ve [Şirket](vmware-azure-tutorial-prepare-on-premises.md) içinde dağıtım önkoşulları. Olağanüstü durum kurtarma için [destek gereksinimlerini](vmware-physical-azure-support-matrix.md) gözden geçirin.

## <a name="prepare-for-automated-deployment"></a>Otomatik dağıtıma hazırlanma

Aşağıdaki tabloda, Mobility hizmeti dağıtımını otomatikleştirmek için araçlar ve süreçler özetlenmektedir.

**Araç** | **Ayrıntılar** | **Yönergeler**
--- | --- | ---
**Configuration Manager** | 1. yukarıda belirtilen [önkoşulların](#prerequisites) bulunduğundan emin olun. <br/><br/> 2. Site Recovery yapılandırma sunucusunu OVF şablonu kullanarak bir VMware VM 'si olarak dağıtmak üzere bir OVA dosyası indirmek üzere, kaynak ortamı ayarlayarak olağanüstü durum kurtarma dağıtın.<br/><br/> 3. yapılandırma sunucusunu Site Recovery hizmetine kaydeder, hedef Azure ortamını ayarlar ve bir çoğaltma ilkesi yapılandırırsınız.<br/><br/> 4. otomatik Mobility hizmeti dağıtımı için yapılandırma sunucusu parolasını ve Mobility hizmeti yükleme dosyalarını içeren bir ağ paylaşma oluşturursunuz.<br/><br/> 5. yükleme veya güncelleştirme içeren bir Configuration Manager paketi oluşturun ve Mobility hizmeti dağıtımına hazırlanın.<br/><br/> 6. daha sonra Mobility hizmetinin yüklü olduğu makineler için Azure 'a çoğaltmayı etkinleştirebilirsiniz. | [Configuration Manager ile Otomatikleştir](#automate-with-configuration-manager)
**JetPatch** | 1. yukarıda belirtilen [önkoşulların](#prerequisites) bulunduğundan emin olun. <br/><br/> 2. bir OVF şablonu kullanarak Site Recovery ortamınızda Azure Site Recovery için JetPatch Aracısı yöneticisini indirmek ve dağıtmak da dahil olmak üzere, kaynak ortamı ayarlayarak olağanüstü durum kurtarma dağıtın.<br/><br/> 3. yapılandırma sunucusunu Site Recovery kaydeder, hedef Azure ortamını ayarlar ve bir çoğaltma ilkesi yapılandırırsınız.<br/><br/> 4. otomatik dağıtım için JetPatch Aracısı Yöneticisi yapılandırmasını başlatın ve doldurun.<br/><br/> 5. JetPatch 'de Mobility hizmeti aracısının dağıtımını ve yükseltmesini otomatikleştirmek için bir Site Recovery ilkesi oluşturabilirsiniz. <br/><br/> 6. daha sonra Mobility hizmetinin yüklü olduğu makineler için Azure 'a çoğaltmayı etkinleştirebilirsiniz. | [JetPatch Aracısı Yöneticisi ile otomatikleştirin](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [JetPatch 'de aracı yüklemesinde sorun giderme](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Configuration Manager ile Otomatikleştir

### <a name="prepare-the-installation-files"></a>Yükleme dosyalarını hazırlama

1. Önkoşulların yerinde olduğundan emin olun.
1. Yapılandırma sunucusunu çalıştıran makine tarafından erişilebilen bir güvenli ağ dosya paylaşma (SMB paylaşma) oluşturun.
1. Configuration Manager ' de, Mobility hizmetini yüklemek veya güncelleştirmek istediğiniz [sunucuları kategorilere ayırın](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) . Tek bir koleksiyon tüm Windows Server 'lar, diğer tüm Linux sunucularını içermelidir.
1. Ağ paylaşımında, bir klasör oluşturun:

   - Windows makinelerine yükleme için _Mobsvcwindows_ adlı bir klasör oluşturun.
   - Linux makinelerinde yükleme için _Mobsvclinux_ adlı bir klasör oluşturun.

1. Yapılandırma sunucusu makinesinde oturum açın.
1. Yapılandırma sunucusu makinesinde bir yönetim komut istemi açın.
1. Parola dosyasını oluşturmak için şu komutu çalıştırın:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. _Mobsvc. parola_ dosyasını Windows klasörüne ve Linux klasörüne kopyalayın.
1. Yükleme dosyalarını içeren klasöre gitmek için şu komutu çalıştırın:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Bu yükleme dosyalarını ağ paylaşımında Kopyala:

   - Windows için _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ _Mobsvcwindows_'a kopyalayın.
   - Linux için aşağıdaki dosyaları _Mobsvclinux_'a kopyalayın:
     - _Microsoft-ASR_UARHEL6 -64release. tar. gz_
     - _Microsoft-ASR_UARHEL7 -64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft-ASR_UAOL6 -64release. tar. gz_
     - _Microsoft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Aşağıdaki yordamlarda açıklandığı gibi, kodu Windows veya Linux klasörlerine kopyalayın. Şu olduğunu varsayıyoruz:

   - Yapılandırma sunucusunun IP adresi `192.168.3.121` .
   - Güvenli ağ dosya paylaşma `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Kodu Windows klasörüne kopyala

Aşağıdaki kodu kopyalayın:

- Kodu _Mobsvcwindows_ klasörüne _install.bat_ olarak kaydedin.
- `[CSIP]`Bu betikteki yer tutucuları yapılandırma SUNUCUNUZUN IP adresinin gerçek değerleriyle değiştirin.
- Betik, Mobility hizmeti aracısının yeni yüklemelerini ve zaten yüklü olan aracıların güncelleştirmelerini destekler.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Kodu Linux klasörüne kopyala

Aşağıdaki kodu kopyalayın:

- Kodu _Mobsvclinux_ klasörüne _install_linux. sh_ olarak kaydedin.
- `[CSIP]`Bu betikteki yer tutucuları yapılandırma SUNUCUNUZUN IP adresinin gerçek değerleriyle değiştirin.
- Betik, Mobility hizmeti aracısının yeni yüklemelerini ve zaten yüklü olan aracıların güncelleştirmelerini destekler.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Paket oluşturma

1. Configuration Manager konsolunda oturum açın ve **yazılım kitaplığı**  >  **uygulama yönetim**  >  **paketleri**' ne gidin.
1. **Paketler** için  >  **paket oluştur**' a sağ tıklayın.
1. Bir ad, açıklama, üretici, dil ve sürüm gibi paket ayrıntılarını sağlayın.
1. **Bu paket kaynak dosyaları içerir**' i seçin.
1. **Araştır**' a tıklayın ve ilgili yükleyiciyi içeren ağ payını ve klasörünü (_Mobsvcwindows_ veya _mobsvclinux_) seçin. Ardından **İleri**' yi seçin.

   ![Paket ve program oluşturma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. **Oluşturmak istediğiniz program türünü seçin** sayfasında, sonra **standart program**' ı seçin  >  .

   ![Standart Program seçeneğini gösteren paket ve program oluşturma Sihirbazı 'nın ekran görüntüsü.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. **Bu standart programla ilgili bilgileri belirtin** sayfasında, aşağıdaki değerleri belirtin:

    **Parametre** | **Windows değeri** | **Linux değeri**
    --- | --- | ---
    **Ad** | Microsoft Azure Mobility hizmetini (Windows) yükler | Microsoft Azure Mobility hizmeti 'ni (Linux) yükler.
    **Komut satırı** | install.bat | ./install_linux. sh
    **Program çalışabilir** | Kullanıcının oturum açmış olup olmadığı | Kullanıcının oturum açmış olup olmadığı
    **Diğer parametreler** | Varsayılan ayarı kullan | Varsayılan ayarı kullan

   ![Standart Program için belirtebileceğiniz bilgileri gösteren ekran görüntüsü.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. **Bu standart program için gereksinimleri belirtin** bölümünde aşağıdaki görevleri yapın:

   - Windows makineleri için, **Bu program yalnızca belirtilen platformlarda çalıştırılabilir**' i seçin. Ardından, [desteklenen Windows işletim sistemlerini](vmware-physical-azure-support-matrix.md#replicated-machines) seçin ve **İleri**' yi seçin.
   - Linux makineleri için, **Bu program herhangi bir platformda çalışabilir**' i seçin. Sonra **İleri**’yi seçin.

1. Sihirbazı tamamlayın.

### <a name="deploy-the-package"></a>Paketi dağıtma

1. Configuration Manager konsolunda, pakete sağ tıklayın ve **Içeriği dağıt**' ı seçin.

   ![Configuration Manager konsolunun ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Paketlerin kopyalanacağı dağıtım noktalarını seçin. [Daha fazla bilgi edinin](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Sihirbazı tamamlayın. Paket daha sonra belirtilen dağıtım noktalarına çoğaltmaya başlar.
1. Paket dağıtımı tamamlandıktan sonra, > **Dağıt** paketine sağ tıklayın.

   ![Configuration Manager konsolunun dağıt menü seçeneğini gösteren ekran görüntüsü.](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Daha önce oluşturduğunuz Windows veya Linux cihaz koleksiyonunu seçin.
1. **İçerik hedefini belirtin** sayfasında **dağıtım noktaları**' nı seçin.
1. **Bu yazılımın nasıl dağıtılacağını denetlemek için ayarları belirtin** sayfasında, **amacı** **gereken** olarak ayarlayın.

   ![Yazılım Dağıtma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. **Bu dağıtımın zamanlamasını belirtin** bölümünde bir zamanlama ayarlayın. [Daha fazla bilgi edinin](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Mobility hizmeti, belirttiğiniz zamanlamaya uygun olarak yüklenir.
   - Gereksiz yeniden başlatmaların olmaması için, aylık bakım pencerenizde veya yazılım güncelleştirmeleri penceresinde paket yüklemesini zamanlayın.

1. **Dağıtım noktaları** sayfasında, ayarları yapılandırın ve Sihirbazı sona erdirin.
1. Configuration Manager konsolundaki dağıtım ilerlemesini izleyin. **İzleme** dağıtımları ' na gidin  >    >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>Mobility hizmetini kaldırma

Mobility hizmetini kaldırmak için Configuration Manager paketler oluşturabilirsiniz. Örneğin, aşağıdaki komut dosyası Mobility hizmetini kaldırır:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Sonraki adımlar

VM 'Ler için [çoğaltmayı etkinleştirin](vmware-azure-enable-replication.md) .
