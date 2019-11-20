---
title: Azure VM başlatması Windows Update konumunda takıldı | Microsoft Docs
description: Azure VM başlatması Windows Update 'e takıldığında sorunu nasıl giderebileceğinizi öğrenin.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 226151d81319dc4e6f132e76ce2d310f88a484e8
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087030"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure VM başlatması Windows Update 'te takıldı

Bu makale, sanal makineniz (VM) başlangıç sırasında Windows Update aşamada takıldığında sorunu çözmeye yardımcı olur. 

> [!NOTE] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede Kaynak Yöneticisi dağıtım modelinin kullanımı ele alınmaktadır. Klasik dağıtım modelini kullanmak yerine bu modeli Yeni dağıtımlar için kullanmanızı öneririz.

## <a name="symptom"></a>Belirti

 Bir Windows VM 'si başlamıyor. [Önyükleme tanılaması](../troubleshooting/boot-diagnostics.md) penceresinde ekran görüntülerini denetlediğinizde, başlatmanın güncelleştirme işleminde takılı olduğunu görürsünüz. Alabileceğiniz ileti örnekleri aşağıda verilmiştir:

- Windows yükleme #%, bilgisayarınızı kapatmayın. Bu işlem, BILGISAYARıNıZ birkaç kez yeniden başlatılacak.
- Bu işlem tamamlanana kadar BILGISAYARıNıZı açık tutun. #/# Güncelleştirme yükleniyor... 
- Değişiklikleri geri almak için güncelleştirmeleri tamamlayamadık, bilgisayarınızı kapatmayın
- Değişiklikleri geri almak için Windows güncelleştirmeleri yapılandırılırken hata, bilgisayarınızı kapatmayın
- Hata < hata kodu > güncelleştirme işlemleri uygulanıyor # # # # #/# # # # # (\Kayıt defteri)
- Önemli hata < güncelleştirme işlemleri uygulanıyor > hata kodu # # # # # # # # # # ($ $...)


## <a name="solution"></a>Çözüm

Yüklenen veya toplanan güncelleştirmelerin sayısına bağlı olarak güncelleştirme işlemi biraz zaman alabilir. Bu durumda VM 'yi 8 saat boyunca bırakın. VM bu süre sonunda hala bu durumundaysa, VM 'yi Azure portal yeniden başlatın ve normal bir şekilde başlayıp başlamadığından emin olmanız gerekir. Bu adım işe yaramazsa, aşağıdaki çözümü deneyin.

### <a name="remove-the-update-that-causes-the-problem"></a>Soruna neden olan güncelleştirmeyi kaldırın

1. Etkilenen VM 'nin işletim sistemi diskinin anlık görüntüsünü bir yedekleme olarak alın. Daha fazla bilgi için [bir diskin anlık görüntüsünü alma](../windows/snapshot-copy-managed-disk.md). 
2. [İşletim sistemi diskini bir kurtarma VM'si ekleme](troubleshoot-recovery-disks-portal-windows.md).
3. İşletim sistemi diski kurtarma sanal makinesine eklendikten sonra, disk yönetimi 'ni açmak için **diskmgmt. msc** ' yi çalıştırın ve ekli diskin **çevrimiçi**olduğundan emin olun. \Windows klasörünü tutan bağlı işletim sistemi diskine atanan sürücü harfini unutmayın. Disk şifrelenirse, bu belgedeki sonraki adımlara geçmeden önce diskin şifresini çözün.

4. Yükseltilmiş bir komut istemi örneği açın (yönetici olarak çalıştır). Bağlı işletim sistemi diskinde bulunan güncelleştirme paketlerinin listesini almak için aşağıdaki komutu çalıştırın:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Örneğin, bağlı işletim sistemi diski F sürücüsündeyse aşağıdaki komutu çalıştırın:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. C:\temp\Patch_level.txt dosyasını açın ve ardından aşağıdan yukarıya okuyun. **Yükleme beklemede** veya **kaldırma bekleme** durumunda olan güncelleştirmeyi bulun.  Aşağıda, güncelleştirme durumunun bir örneği verilmiştir:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Soruna neden olan güncelleştirmeyi kaldırın:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Örnek: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Paketin boyutuna bağlı olarak, DıSM aracının yükleme kaldırma işlemi bir süre sürer. Normalde işlem, 16 dakika içinde tamamlanır.

7. [İşletim sistemi diskini ve VM yeniden](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Sonra sorunun çözümlenip çözümlenmediğini denetleyin.
