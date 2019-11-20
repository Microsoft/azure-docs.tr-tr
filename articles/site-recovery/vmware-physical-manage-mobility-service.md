---
title: Azure Site Recovery ile VMware/fiziksel sunucuları için Mobility Aracısı 'nı yönetme
description: Azure Site Recovery hizmetini kullanarak VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için Mobility hizmeti aracısını yönetin.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: e6e7beeb4c10098f36636aad2709e03d1a1a0fea
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953640"
---
# <a name="manage-the-mobility-agent"></a>Mobility aracısını yönetme 

VMware VM 'lerini ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarması için Azure Site Recovery kullandığınızda Mobility Agent 'ı sunucunuzda ayarlarsınız. Mobility Aracısı, korumalı makineniz, yapılandırma sunucusu/genişleme işlem sunucusu arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir. Bu makalede, Mobility aracısının dağıtıldıktan sonra yönetilmesi için ortak görevler özetlenmektedir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mobility hizmetini Azure portal güncelleştirme

1. Başlamadan önce, korunan makinelerde Mobility hizmetini güncelleştirmeden önce, yapılandırma sunucusunun, genişleme işlem sunucularının ve dağıtımınızın bir parçası olan ana hedef sunucuların güncelleştirildiğinden emin olun.
2. Portalda, **çoğaltılan öğeler**> kasayı açın.
3. Yapılandırma sunucusu en son sürümsa, "Yeni Site Recovery çoğaltma Aracısı güncelleştirmesi kullanılabilir ' ı okuduğunu belirten bir bildirim görürsünüz. Yüklemek için tıklayın. "

     ![Çoğaltılan Öğeler penceresi](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Bildirime tıklayın ve **Aracı güncelleştirme**' de Mobility hizmetini yükseltmek istediğiniz makineleri seçin. Daha sonra, **Tamam**'a tıklayın.

     ![Çoğaltılan öğeler VM listesi](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Mobility hizmetini Güncelleştir işi seçilen makinelerin her biri için başlar.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows Server 'da PowerShell betiği aracılığıyla Mobility hizmetini güncelleştirme

Power Shell cmdlet 'i aracılığıyla bir sunucuda Mobility hizmetini yükseltmek için aşağıdaki betiği kullanın

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Mobility hizmetinin anında yüklenmesi için kullanılan hesabı Güncelleştir

Site Recovery dağıttığınızda, Mobility hizmetini göndererek yüklemeyi etkinleştirmek için, Site Recovery işlem sunucusunun makinelere erişmek için kullandığı bir hesap belirttiniz ve makine için çoğaltma etkinleştirildiğinde hizmeti yükler. Bu hesabın kimlik bilgilerini güncelleştirmek istiyorsanız, [Bu yönergeleri](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)izleyin.

## <a name="uninstall-mobility-service"></a>Mobility hizmetini kaldır

### <a name="on-a-windows-machine"></a>Bir Windows makinesinde

Kullanıcı arabiriminden veya bir komut isteminden kaldırın.

- **Kullanıcı arabiriminden**: makinenin Denetim Masası 'Nda, **Programlar**' ı seçin. **Microsoft Azure Site Recovery Mobility hizmeti/ana hedef sunucusu** > **Kaldır**' ı seçin.
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
