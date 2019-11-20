---
title: System Center Configuration Manager kullanarak VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için Azure Site Recovery Mobility hizmetinin yüklemesini otomatikleştirin | Microsoft Docs
description: Bu makale, Site Recovery kullanarak VMware VM 'lerini ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarması için System Center Configuration Manager ile Mobility hizmeti yüklemesini otomatik hale getirmenize yardımcı olur.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ee92ad6e0687018f69044bf3edde76b9f98cee52
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255593"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>System Center Configuration Manager ile Mobility hizmeti yüklemesini otomatikleştirin

Mobility hizmeti, [Azure Site Recovery](site-recovery-overview.md) kullanarak Azure 'a çoğaltmak Istediğiniz VMware VM 'lerine ve fiziksel sunuculara yüklenir

Bu makalede, Azure Site Recovery Mobility hizmetini bir VMware sanal makinesine dağıtmak için System Center Configuration Manager nasıl kullanabileceğinizi gösteren bir örnek sunulmaktadır. Configuration Manager gibi bir yazılım dağıtım aracı kullanılması aşağıdaki avantajları içerir:

* Yazılım güncelleştirmeleri için planlı bakım pencerenizde yeni yüklemeleri ve yükseltmeleri zamanlayın
* Dağıtımı aynı anda yüzlerce sunucuya ölçeklendirin

Bu makalede dağıtım etkinliğini göstermek için System Center Configuration Manager 2012 R2 kullanılmaktadır. Mobility hizmetinin sürüm **9.9.4510.1** veya üstünü kullandığınızı varsaydık.

Alternatif olarak, [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md)Ile Mobility hizmeti yüklemesini otomatik hale getirebilirsiniz.

## <a name="prerequisites"></a>Prerequisites

1. Ortamınızda zaten dağıtılan Configuration Manager gibi bir yazılım dağıtım aracı.
2. Site Recovery kullanarak korumak istediğiniz tüm **Windows Server**ve diğer **Linux sunucuları**için bir tane olmak üzere iki [cihaz koleksiyonu](https://technet.microsoft.com/library/gg682169.aspx)oluşturmanız gerekir.
3. Kurtarma Hizmetleri kasasına zaten kayıtlı olan bir yapılandırma sunucusu.
4. Configuration Manager makinesi tarafından erişilebilen güvenli bir ağ dosya paylaşımında (SMB paylaşma).

## <a name="deploy-on-windows-machines"></a>Windows makinelerde dağıtma
> [!NOTE]
> Bu makalede yapılandırma sunucusunun IP adresinin 192.168.3.121 olduğu ve güvenli ağ dosya paylaşımının \\ \ Contososecurefs\\system ityserviceınstallerolduğu varsayılır.

### <a name="prepare-for-deployment"></a>Dağıtıma hazırlanma
1. Ağ paylaşımında bir klasör oluşturun ve **Mobsvcwindows**olarak adlandırın.
2. Yapılandırma sunucunuzda oturum açın ve bir yönetim komut istemi açın.
3. Bir parola dosyası oluşturmak için aşağıdaki komutları çalıştırın:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. **Mobsvc. parola** dosyasını ağ paylaşımınızda **Mobsvcwindows** klasörüne kopyalayın.
5. Aşağıdaki komutu çalıştırarak yapılandırma sunucusundaki yükleyici deposuna gidin:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. **Microsoft-ASR @ no__t-1UA @ no__t-2*Sürüm*\_Windows @ NO__T-5ga @ no__t-6*Tarih*@no__t -8release. exe** ' yi ağ paylaşımınızda **mobsvcwindows** klasörüne kopyalayın.
7. Aşağıdaki kodu kopyalayın ve bu dosyayı **Mobsvcwindows** klasörüne **Install. bat** olarak kaydedin.

   > [!NOTE]
   > Bu betikteki [CSıP] yer tutucuları yapılandırma sunucunuzun IP adresinin gerçek değerleriyle değiştirin.

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

### <a name="create-a-package"></a>Paket oluşturma

1. Configuration Manager konsolunda oturum açın.
2. @No__t-1**uygulama yönetimi** > **paketlerine** **yazılım kitaplığı**'na gidin.
3. **Paketler**' e sağ tıklayın ve **paket oluştur**' u seçin.
4. Ad, açıklama, üretici, dil ve sürüm için değerler sağlayın.
5. **Bu paket kaynak dosyaları içerir** onay kutusunu seçin.
6. **Göz at**' a tıklayın ve yükleyicinin depolandığı ağ payını seçin (\\ \ Contososecurefs\\system Ityserviceınstaller\mobsvcwindows).

   ![Paket ve program oluşturma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. **Oluşturmak istediğiniz program türünü seçin** sayfasında, **standart program**' ı seçin ve **İleri**' ye tıklayın.

   ![Paket ve program oluşturma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. **Bu standart programla ilgili bilgileri belirtin** sayfasında, aşağıdaki girişleri sağlayın ve **İleri**' ye tıklayın. (Diğer girişler varsayılan değerlerini kullanabilir.)

   | **Parametre adı** | **Değer** |
   |--|--|
   | Name | Microsoft Azure Mobility hizmetini (Windows) yükler |
   | Komut satırı | . bat dosyasını install |
   | Program çalışabilir | Kullanıcının oturum açmış olup olmadığı |

   ![Paket ve program oluşturma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Sonraki sayfada, hedef işletim sistemlerini seçin. 
10. Sihirbazı tamamladığınızda, **İleri** ' ye iki kez tıklayın.


> [!NOTE]
> Betik, Mobility hizmeti aracılarının yeni yüklemelerini ve zaten yüklü olan aracıların güncelleştirmelerini destekler.

### <a name="deploy-the-package"></a>Paketi dağıtma
1. Configuration Manager konsolunda paketinize sağ tıklayın ve **Içeriği dağıt**' ı seçin.
   ![Configuration Manager konsolunun @ ekran görüntüsü @ no__t-1
2. Paketlerin kopyalanacağı **[dağıtım noktalarını](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** seçin.
3. Sihirbazı tamamlayın. Paket daha sonra belirtilen dağıtım noktalarına çoğaltmaya başlar.
4. Paket dağıtımı yapıldıktan sonra, pakete sağ tıklayın ve **Dağıt**' ı seçin.
   ![Configuration Manager konsolunun @ ekran görüntüsü @ no__t-1
5. Dağıtım için hedef koleksiyon olarak Önkoşullar bölümünde oluşturduğunuz Windows Server cihaz koleksiyonunu seçin.

   ![Yazılım Dağıtma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. **İçerik hedefini belirtin** sayfasında **dağıtım noktalarınızı**seçin.
7. **Bu yazılımın nasıl dağıtılacağını denetlemek için ayarları belirtin** sayfasında, amacının **gerekli**olduğundan emin olun.

   ![Yazılım Dağıtma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. **Bu dağıtım için zamanlamayı belirtin** sayfasında bir zamanlama belirtin. Daha fazla bilgi için bkz. [paketleri zamanlama](https://technet.microsoft.com/library/gg682178.aspx).
9. **Dağıtım noktaları** sayfasında, özellikleri veri merkezinizin ihtiyaçlarına göre yapılandırın. Ardından Sihirbazı doldurun.

> [!TIP]
> Gereksiz yeniden başlatmaların olmaması için, aylık bakım pencerenizde veya yazılım güncelleştirmeleri penceresinde paket yüklemesini zamanlayın.

Configuration Manager konsolunu kullanarak dağıtım ilerlemesini izleyebilirsiniz. @No__t-1**dağıtımlarını** **izleme** >  *[paketinizin adı]* sayfasına gidin.

  ![Dağıtımları izlemek için Configuration Manager seçeneğinin ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Linux makinelerinde dağıtma
> [!NOTE]
> Bu makalede yapılandırma sunucusunun IP adresinin 192.168.3.121 olduğu ve güvenli ağ dosya paylaşımının \\ \ Contososecurefs\\system ityserviceınstallerolduğu varsayılır.

### <a name="prepare-for-deployment"></a>Dağıtıma hazırlanma
1. Ağ paylaşımında bir klasör oluşturun ve **Mobsvclinux**olarak adlandırın.
2. Yapılandırma sunucunuzda oturum açın ve bir yönetim komut istemi açın.
3. Bir parola dosyası oluşturmak için aşağıdaki komutları çalıştırın:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. **Mobsvc. parola** dosyasını ağ paylaşımınızda **Mobsvclinux** klasörüne kopyalayın.
5. Şu komutu çalıştırarak yapılandırma sunucusundaki yükleyici deposuna gidin:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Aşağıdaki dosyaları ağ paylaşımınızda **Mobsvclinux** klasörüne kopyalayın:
   * Microsoft-ASR @ no__t-0UA @ no__t-1RHEL6-64 * Release. tar. gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1RHEL7-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1OL6-64\*release.tar.gz
   * Microsoft-ASR @ no__t-0UA @ no__t-1UBUNTU-14.04-64\*release.tar.gz


7. Aşağıdaki kodu kopyalayın ve **install_linux. sh** olarak dosyayı **Mobsvclinux** klasörüne kaydedin.
   > [!NOTE]
   > Bu betikteki [CSıP] yer tutucuları yapılandırma sunucunuzun IP adresinin gerçek değerleriyle değiştirin.

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

1. Configuration Manager konsolunda oturum açın.
2. @No__t-1**uygulama yönetimi** > **paketlerine** **yazılım kitaplığı**'na gidin.
3. **Paketler**' e sağ tıklayın ve **paket oluştur**' u seçin.
4. Ad, açıklama, üretici, dil ve sürüm için değerler sağlayın.
5. **Bu paket kaynak dosyaları içerir** onay kutusunu seçin.
6. **Göz at**' a tıklayın ve yükleyicinin depolandığı ağ payını seçin (\\ \ Contososecurefs\\system Ityserviceınstaller\mobsvclinux).

   ![Paket ve program oluşturma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. **Oluşturmak istediğiniz program türünü seçin** sayfasında, **standart program**' ı seçin ve **İleri**' ye tıklayın.

   ![Paket ve program oluşturma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. **Bu standart programla ilgili bilgileri belirtin** sayfasında, aşağıdaki girişleri sağlayın ve **İleri**' ye tıklayın. (Diğer girişler varsayılan değerlerini kullanabilir.)

    | **Parametre adı** | **Değer** |
   |--|--|
   | Name | Microsoft Azure Mobility hizmeti 'ni (Linux) yükler |
   | Komut satırı | ./ınstall_linux.exe |
   | Program çalışabilir | Kullanıcının oturum açmış olup olmadığı |

   ![Paket ve program oluşturma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Sonraki sayfada, **Bu program herhangi bir platformda çalışabilir**' i seçin.
   @no__t--paket ve program oluşturma Sihirbazı 'nın 0 ekran görüntüsü @ no__t-1

10. Sihirbazı tamamladığınızda, **İleri** ' ye iki kez tıklayın.

> [!NOTE]
> Betik, Mobility hizmeti aracılarının yeni yüklemelerini ve zaten yüklü olan aracıların güncelleştirmelerini destekler.

### <a name="deploy-the-package"></a>Paketi dağıtma
1. Configuration Manager konsolunda paketinize sağ tıklayın ve **Içeriği dağıt**' ı seçin.
   ![Configuration Manager konsolunun @ ekran görüntüsü @ no__t-1
2. Paketlerin kopyalanacağı **[dağıtım noktalarını](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** seçin.
3. Sihirbazı tamamlayın. Paket daha sonra belirtilen dağıtım noktalarına çoğaltmaya başlar.
4. Paket dağıtımı yapıldıktan sonra, pakete sağ tıklayın ve **Dağıt**' ı seçin.
   ![Configuration Manager konsolunun @ ekran görüntüsü @ no__t-1
5. Dağıtım için hedef koleksiyon olarak Önkoşullar bölümünde oluşturduğunuz Linux sunucu cihaz koleksiyonunu seçin.

   ![Yazılım Dağıtma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. **İçerik hedefini belirtin** sayfasında **dağıtım noktalarınızı**seçin.
7. **Bu yazılımın nasıl dağıtılacağını denetlemek için ayarları belirtin** sayfasında, amacının **gerekli**olduğundan emin olun.

   ![Yazılım Dağıtma Sihirbazı ekran görüntüsü](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. **Bu dağıtım için zamanlamayı belirtin** sayfasında bir zamanlama belirtin. Daha fazla bilgi için bkz. [paketleri zamanlama](https://technet.microsoft.com/library/gg682178.aspx).
9. **Dağıtım noktaları** sayfasında, özellikleri veri merkezinizin ihtiyaçlarına göre yapılandırın. Ardından Sihirbazı doldurun.

Mobility hizmeti, yapılandırdığınız zamanlamaya göre Linux sunucu cihaz koleksiyonuna yüklenir.


## <a name="uninstall-the-mobility-service"></a>Mobility hizmetini kaldırma
Mobility hizmetini kaldırmak için Configuration Manager paketler oluşturabilirsiniz. Bunu yapmak için aşağıdaki betiği kullanın:

```
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
Artık sanal makineleriniz için [korumayı etkinleştirmeye](vmware-azure-enable-replication.md) hazırsınız.
