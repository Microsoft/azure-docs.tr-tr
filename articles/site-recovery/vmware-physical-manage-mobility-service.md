---
title: Azure Site Recovery ile VMware/fiziksel sunucuları için Mobility Aracısı 'nı yönetme
description: Azure Site Recovery hizmetini kullanarak VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için Mobility hizmeti aracısını yönetin.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: d921bddf90c415cb244e2cc9ad98354392a537ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530174"
---
# <a name="manage-the-mobility-agent"></a>Mobility aracısını yönetme 

VMware VM 'lerini ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarması için Azure Site Recovery kullandığınızda Mobility Agent 'ı sunucunuzda ayarlarsınız. Mobility Aracısı, korumalı makineniz, yapılandırma sunucusu/genişleme işlem sunucusu arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir. Bu makalede, Mobility aracısının dağıtıldıktan sonra yönetilmesi için ortak görevler özetlenmektedir.

>[!TIP]
>Belirli bir işletim sistemi/Linux için yükleyiciyi indirmek için [buradaki](vmware-physical-mobility-service-overview.md#locate-installer-files)kılavuza bakın. Portaldan otomatik olarak güncelleştirmek için, yükleyiciyi indirmeniz gerekmez. [ASR, yükleyiciyi yapılandırma sunucusundan otomatik olarak getirir ve aracıyı günceller](#update-mobility-service-from-azure-portal).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mobility hizmetini Azure portal güncelleştirme

1. Başlamadan önce, korunan makinelerde Mobility hizmetini güncelleştirmeden önce, yapılandırma sunucusunun, genişleme işlem sunucularının ve dağıtımınızın bir parçası olan ana hedef sunucuların güncelleştirildiğinden emin olun.
    1. 9,36 sürümünden başlayarak, SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, detem 7 için en son yükleyicinin [yapılandırma sunucusu ve genişleme işlem sunucusu 'nda kullanılabildiğinden](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)emin olun.
1. Portalda, **çoğaltılan öğeler**> kasayı açın.
1. Yapılandırma sunucusu en son sürümsa, "Yeni Site Recovery çoğaltma Aracısı güncelleştirmesi kullanılabilir ' ı okuduğunu belirten bir bildirim görürsünüz. Yüklemek için tıklayın. "

     ![Çoğaltılan Öğeler penceresi](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Bildirime tıklayın ve **Aracı güncelleştirme**' de Mobility hizmetini yükseltmek istediğiniz makineleri seçin. Daha sonra, **Tamam**'a tıklayın.

     ![Çoğaltılan öğeler VM listesi](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Mobility hizmetini Güncelleştir işi seçilen makinelerin her biri için başlar. Mobility Aracısı yapılandırma sunucusu sürümüne güncelleştirilir. Örneğin, yapılandırma sunucusu 9,33 sürümitesi, korunan bir VM 'deki Mobility Aracısı da 9,33 sürümüne güncelleştirilir.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows Server 'da PowerShell betiği aracılığıyla Mobility hizmetini güncelleştirme

Başlamadan önce, korunan makinelerde Mobility hizmetini güncelleştirmeden önce, yapılandırma sunucusunun, genişleme işlem sunucularının ve dağıtımınızın bir parçası olan ana hedef sunucuların güncelleştirildiğinden emin olun.

Power Shell cmdlet 'i aracılığıyla bir sunucuda Mobility hizmetini yükseltmek için aşağıdaki betiği kullanın

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Mobility hizmetini her korumalı sunucuda el ile güncelleştirme

1. Başlamadan önce, korunan makinelerde Mobility hizmetini güncelleştirmeden önce, yapılandırma sunucusunun, genişleme işlem sunucularının ve dağıtımınızın bir parçası olan ana hedef sunucuların güncelleştirildiğinden emin olun.

2. Sunucu işletim sistemine bağlı olarak [Aracı yükleyicisini bulun](vmware-physical-mobility-service-overview.md#locate-installer-files) .

>[!IMPORTANT]
> Azure IaaS VM 'yi bir Azure bölgesinden diğerine çoğaltırken, bu yöntemi kullanmayın. Kullanılabilir tüm seçeneklerle ilgili bilgi için [kılavuzumuza](azure-to-azure-autoupdate.md) bakın.

3. Yükleme dosyasını korunan makineye kopyalayın ve Mobility Aracısı 'nı güncelleştirmek için çalıştırın.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Mobility hizmetinin anında yüklenmesi için kullanılan hesabı Güncelleştir

Site Recovery dağıttığınızda, Mobility hizmetini göndererek yüklemeyi etkinleştirmek için, Site Recovery işlem sunucusunun makinelere erişmek için kullandığı bir hesap belirttiniz ve makine için çoğaltma etkinleştirildiğinde hizmeti yükler. Bu hesabın kimlik bilgilerini güncelleştirmek istiyorsanız, [Bu yönergeleri](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)izleyin.

## <a name="uninstall-mobility-service"></a>Mobility hizmetini kaldır

### <a name="on-a-windows-machine"></a>Bir Windows makinesinde

Kullanıcı arabiriminden veya bir komut isteminden kaldırın.

- **Kullanıcı arabiriminden**: makinenin Denetim Masası 'Nda, **Programlar**' ı seçin. **Mobility hizmeti/ana hedef sunucusu kaldırma Microsoft Azure Site Recovery** seçin  >  .
- **Bir komut isteminden**: makinede yönetici olarak bir komut istemi penceresi açın. Şu komutu çalıştırın: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Bir Linux makinesinde
1. Linux makinesinde, **kök** Kullanıcı olarak oturum açın.
2. Bir terminalde/usr/local/asrdizinine gidin.
3. Şu komutu çalıştırın:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Kaynak makineye Site Recovery VSS sağlayıcısı 'nı yükler

Uygulama tutarlılığı noktaları oluşturmak için kaynak makinede Azure Site Recovery VSS sağlayıcısı gerekir. Sağlayıcı yüklemesi göndererek yükleme işlemi başarılı olmadıysa, el ile yüklemek için aşağıdaki yönergeleri izleyin.

1. Yönetici cmd penceresini açın.
2. Mobility hizmeti yükleme konumuna gidin. (Örn. C:\Program Files (x86) \Microsoft Azure Site Recovery\agent)
3. InMageVSSProvider_Uninstall. cmd betiğini çalıştırın. Bu işlem zaten varsa hizmeti kaldırır.
4. VSS sağlayıcısını el ile yüklemek için InMageVSSProvider_Install. cmd betiğini çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

- [VMware VM 'Leri için olağanüstü durum kurtarmayı ayarlama](vmware-azure-tutorial.md)
- [Fiziksel sunucular için olağanüstü durum kurtarmayı ayarlama](physical-azure-disaster-recovery.md)
