---
title: Azure Site Recovery 'de VMware VM 'Leri/fiziksel sunucu olağanüstü durum kurtarma için bir işlem sunucusunu yönetme
description: Bu makalede, Azure Site Recovery kullanarak VMware VM 'lerinin/fiziksel sunucularının olağanüstü durum kurtarması için bir işlem sunucusu yönetimi açıklanmaktadır.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954341"
---
# <a name="manage-process-servers"></a>İşlem sunucularını yönetme

Bu makalede Site Recovery işlem sunucusunu yönetmeye yönelik ortak görevler açıklanmaktadır.

İşlem sunucusu, çoğaltma verilerini almak, iyileştirmek ve Azure 'a göndermek için kullanılır. Ayrıca, VMware VM 'lerinde ve çoğaltmak istediğiniz fiziksel sunucularda Mobility hizmetinin göndererek yüklemesini gerçekleştirir ve şirket içi makinelerin otomatik olarak bulunmasını gerçekleştirir. Şirket içi VMware VM 'lerini veya fiziksel sunucuları Azure 'a çoğaltmak için, işlem sunucusu varsayılan olarak yapılandırma sunucusu makinesine yüklenir. 

- Büyük dağıtımlar için kapasiteyi ölçeklendirmek üzere ek şirket içi işlem sunucuları gerekebilir.
- Azure 'dan şirket içine yeniden çalışma için Azure 'da geçici bir işlem sunucusu ayarlamanız gerekir. Yeniden çalışma tamamlandığında bu VM 'yi silebilirsiniz. 

İşlem sunucusu hakkında daha fazla bilgi edinin.


## <a name="upgrade-a-process-server"></a>İşlem sunucusunu yükseltme

Bir işlem sunucusunu şirket içinde dağıtırken veya yeniden çalışma için bir Azure VM olarak dağıttığınızda, işlem sunucusunun en son sürümü yüklenir. Site Recovery ekipler düzenli olarak düzeltmeler ve geliştirmeler yayımlar ve işlem sunucularını güncel tutmanız önerilir. Bir işlem sunucusunu aşağıdaki şekilde yükseltebilirsiniz:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>İşlem sunucusu yükünü dengelemek için VM 'Leri taşıyın

VM 'Leri aşağıdaki gibi iki işlem sunucusu arasında taşıyarak yükü dengeleyin:

1. Kasada, **yönet** **Site Recovery altyapı**' ı tıklatın. **VMware & fiziksel makineleri için**altında **yapılandırma sunucuları**' na tıklayın.
2. İşlem sunucularının kaydedildiği yapılandırma sunucusuna tıklayın.
3. Yük Dengeleme trafiğini açmak istediğiniz işlem sunucusuna tıklayın.

    ![Yük Dengeleme](media/vmware-azure-manage-process-server/LoadBalance.png)

4. **Yük Dengeleme**' ye tıklayın, makineleri taşımak istediğiniz hedef işlem sunucusunu seçin. Ardından **Tamam 'a** tıklayın

    ![LoadPS 'Ler](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **Makine seç**' e tıklayın ve geçerli sunucudan hedef işlem sunucusuna taşımak istediğiniz makineleri seçin. Ortalama veri değişikliği ayrıntıları, her bir sanal makine için görüntülenir. Daha sonra, **Tamam**'a tıklayın. 
3. Kasada, > **Site Recovery Işleri** **izleme** altında işin ilerlemesini izleyin.

Değişikliklerin portalda yansıtılması 15 dakika sürer. Daha hızlı bir etki için [yapılandırma sunucusunu yenileyin](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Tüm iş yükünü başka bir işlem sunucusuna değiştirme

İşlem sunucusu tarafından işlenen iş yükünün tamamını, aşağıdaki gibi farklı bir işlem sunucusuna taşıyın:

1. Kasada, **yönet** **Site Recovery altyapı**' ı tıklatın. **VMware & fiziksel makineleri için**altında **yapılandırma sunucuları**' na tıklayın.
2. İşlem sunucularının kaydedildiği yapılandırma sunucusuna tıklayın.
3. İş yükünü değiştirmek istediğiniz işlem sunucusuna tıklayın.
4. **Anahtar**' a tıklayın, iş yükünü taşımak istediğiniz hedef işlem sunucusunu seçin. Ardından **Tamam 'a** tıklayın

    ![Anahtar](media/vmware-azure-manage-process-server/Switch.PNG)

5. Kasada, > **Site Recovery Işleri** **izleme** altında işin ilerlemesini izleyin.

Değişikliklerin portalda yansıtılması 15 dakika sürer. Daha hızlı bir etki için [yapılandırma sunucusunu yenileyin](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Ana hedef sunucuyu kaydetme

Ana hedef sunucu, yapılandırma sunucusunda ve genişleme işlem sunucularında bulunur. Yapılandırma sunucusu ile kayıtlı olmalıdır. Bu kayıtta bir hata olması durumunda, korunan öğelerin sistem durumunu etkileyebilir. Ana hedef sunucuyu yapılandırma sunucusuna kaydetmek için, kaydın gerekli olduğu belirli yapılandırma sunucusu/genişleme işlem sunucusunda oturum açın. **%ProgramData%\asr\agent**klasörüne gidin ve yönetici komut isteminde aşağıdaki komutu çalıştırın.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>İşlem sunucusunu yeniden kaydetme

Şirket içinde veya bir Azure VM 'de çalışan bir işlem sunucusunu yapılandırma sunucusu ile aşağıdaki şekilde yeniden kaydedin:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Ayarları kaydettikten sonra şunları yapın:

1. İşlem sunucusunda bir yönetici komut istemi açın.
2. **%ProgramData%\asr\agent**klasörüne göz atın ve şu komutu çalıştırın:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Şirket içi bir işlem sunucusu için proxy ayarlarını değiştirme

Şirket içi bir işlem sunucusu Azure 'a bağlanmak için bir proxy kullanıyorsa, proxy ayarlarını aşağıdaki şekilde değiştirebilirsiniz:

1. İşlem sunucusu makinesinde oturum açın. 
2. Bir yönetici PowerShell komut penceresi açın ve aşağıdaki komutu çalıştırın:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. **%ProgramData%\asr\agent**klasörüne göz atın ve şu komutu çalıştırın:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>İşlem sunucusunu kaldırma

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Virüsten koruma yazılımlarından klasörleri hariç tut

Virüsten koruma yazılımı, genişleme işlem sunucusu (veya ana hedef sunucusu) üzerinde çalışıyorsa, virüsten koruma işlemlerinde aşağıdaki klasörleri hariç tutun:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- İşlem sunucusu yükleme dizini. Örneğin: C:\Program Files (x86) \Microsoft Azure Site Recovery