---
title: Azure VM aracısını çevrimdışı modda yükler | Microsoft Docs
description: Azure VM aracısını çevrimdışı moda yüklemeyi öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 438143d3253f1cab1afb958a90f427dcba59a98e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059247"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Azure sanal makine aracısını çevrimdışı modda yükler 

Azure sanal makine Aracısı (VM Aracısı), yerel yönetici parola sıfırlama ve betik gönderme gibi yararlı özellikler sağlar. Bu makalede, çevrimdışı bir Windows sanal makinesi (VM) için VM aracısının nasıl yükleneceği gösterilmektedir. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Sanal makine aracısının çevrimdışı modda ne zaman kullanılacağı

Aşağıdaki senaryolarda VM aracısını çevrimdışı modda yüklersiniz:

- Dağıtılan Azure VM 'sinde VM Aracısı yüklü değil veya aracı çalışmıyor.
- VM 'nin yönetici parolasını unuttum veya VM 'ye erişemezsiniz.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>VM aracısını çevrimdışı modda yüklemek

VM aracısını çevrimdışı modda yüklemek için aşağıdaki adımları kullanın.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>1\. adım: VM 'nin işletim sistemi diskini başka bir sanal makineye veri diski olarak ekleyin

1. Etkilenen VM 'nin işletim sistemi diski için bir anlık görüntü alın, anlık görüntüden bir disk oluşturun ve ardından diski bir sorun giderme VM 'sine bağlayın. Daha fazla bilgi için, [Azure Portal kullanarak işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek WINDOWS VM sorunlarını giderme](troubleshoot-recovery-disks-portal-windows.md)bölümüne bakın. Klasik VM için, VM 'yi silin ve işletim sistemi diskini koruyun ve ardından işletim sistemi diskini sorun giderme VM 'sine bağlayın.

2.  Sorun giderici sanal makinesine bağlanın. **Bilgisayar Yönetimi** > **disk yönetimi**'ni açın. İşletim sistemi diskinin çevrimiçi olduğunu ve sürücü harflerinin disk bölümlerine atandığını onaylayın.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>2\. adım: Azure VM Aracısı 'nı yüklemek için işletim sistemi diskini değiştirme

1.  Sorun giderici VM 'sine bir Uzak Masaüstü bağlantısı oluşturun.

2.  Sorun giderici VM 'de, eklediğiniz işletim sistemi diskine gidin, \Windows\System32\Config klasörünü açın. Geri almanın gerekli olması durumunda bu klasördeki tüm dosyaları yedek olarak kopyalayın.

3.  **Kayıt defteri Düzenleyicisi 'ni** (Regedit. exe) başlatın.

4.  **HKEY_LOCAL_MACHINE** anahtarını seçin. Menüde **Dosya** > **yükleme Hive**: ' ı seçin.

    ![Hive yükleme](./media/install-vm-agent-offline/load-hive.png)

5.  Eklediğiniz işletim sistemi diskinde \windows\system32\config\SYSTEM klasörüne gidin. Hive adı için **brokensystem**girin. Yeni kayıt defteri kovanı, **HKEY_LOCAL_MACHINE** anahtarının altında görüntülenir.

6.  Eklediğiniz işletim sistemi diskinde \windows\system32\config\SOFTWARE klasörüne gidin. Hive yazılımının adı için **Brokensoftware**' i girin.

7. Bağlı işletim sistemi diskinde VM Aracısı yüklüyse, geçerli yapılandırmanın bir yedeklemesini gerçekleştirin. VM Aracısı yüklü değilse, sonraki adıma geçin.
      
    1. \Windowsazure klasörünü \windowsazure.exe olarak yeniden adlandırın.

    2. Aşağıdaki kayıt defterlerini dışarı aktarın:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  VM Aracısı yüklemesi için bir depo olarak sorun giderici VM 'sinde var olan dosyaları kullanın. Aşağıdaki adımları tamamlayın:

    1. Sorun giderici VM 'sinden aşağıdaki alt anahtarları kayıt defteri biçiminde (. reg) dışarı aktarın: 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \System\controlset001\services\rdadgent

          ![Kayıt defteri alt anahtarlarını dışarı aktarma](./media/install-vm-agent-offline/backup-reg.png)

    2. Kayıt defteri dosyalarını düzenleyin. Her dosyada, giriş değeri **sistemini** **brokensystem** (aşağıdaki görüntülerde gösterildiği gibi) olarak değiştirin ve dosyayı kaydedin. Geçerli VM aracısının **ImagePath** öğesini hatırla. İlgili klasörü ekli işletim sistemi diskine kopyalamamız gerekir. 

        ![Kayıt defteri alt anahtarı değerlerini değiştirme](./media/install-vm-agent-offline/change-reg.png)

    3. Kayıt defteri dosyalarını, her bir kayıt defteri dosyasına çift tıklayarak depoya aktarın.

    4. Aşağıdaki üç alt anahtarın, **brokensystem** kovanına başarıyla aktarıldığını doğrulayın:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - Rdadgent

    5. Geçerli VM aracısının yükleme klasörünü bağlı işletim sistemi diskine kopyalayın: 

        1.  Eklediğiniz işletim sistemi diskinde, kök yolunda WindowsAzure adlı bir klasör oluşturun.

        2.  Sorun giderici VM 'sinde C:\WindowsAzure ' a gidin, C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. adında bir klasör arayın. C:\WindowsAzure öğesinden en son sürüm numarasına sahip GuestAgent klasörünü, ekli işletim sistemi diskinde WindowsAzure klasörüne kopyalayın. Hangi klasörün kopyalanacağından emin değilseniz, tüm GuestAgent klasörlerini kopyalayın. Aşağıdaki görüntüde, ekli işletim sistemi diskine kopyalanmış olan GuestAgent klasörünün bir örneği gösterilmektedir.

             ![GuestAgent klasörünü Kopyala](./media/install-vm-agent-offline/copy-files.png)

9.  **Brokensistem**' i seçin. Menüden **Dosya** > **Kaldır Hive**' yi seçin.

10.  **Brokensoftware**' i seçin. Menüden **Dosya** > **Kaldır Hive**' yi seçin.

11.  İşletim sistemi diskini ayırın ve ardından [ETKILENEN VM 'nin işletim sistemi diskini değiştirin](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Klasik VM için, onarılan işletim sistemi diskini kullanarak yeni bir VM oluşturun.

12.  VM 'ye erişin. Rdadgent 'ın çalıştığından ve günlüklerin oluşturulduğuna dikkat edin.

VM 'yi Kaynak Yöneticisi dağıtım modelini kullanarak oluşturduysanız işiniz bitti demektir.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Klasik VM 'Ler için ProvisionGuestAgent özelliğini kullanın

Sanal makineyi klasik model kullanarak oluşturduysanız, **ProvisionGuestAgent** özelliğini güncelleştirmek için Azure PowerShell modülünü kullanın. Özelliği, VM 'nin VM aracısının yüklü olduğunu Azure 'a bildirir.

**ProvisionGuestAgent** özelliğini ayarlamak için Azure PowerShell ' de aşağıdaki komutları çalıştırın:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Sonra `Get-AzureVM` komutunu çalıştırın. **Guestagentstatus** özelliğinin artık verilerle doldurulduğuna dikkat edin:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure sanal makine aracısına genel bakış](../extensions/agent-windows.md)
- [Windows için sanal makine uzantıları ve özellikleri](../extensions/features-windows.md)
