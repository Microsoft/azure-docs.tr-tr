---
title: Yerel PowerShell betikleri kullanarak şirket içinde barındırılan tümleştirme çalışma zamanı yüklemesini otomatikleştirme
description: Yerel makinelerde şirket içinde barındırılan Integration Runtime yüklemesini otomatik hale getirmek için.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 8cbe54a23cb1c8b55afd86a18b51c0e392c3f78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376216"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Yerel PowerShell betikleri kullanarak şirket içinde barındırılan tümleştirme çalışma zamanı yüklemesini otomatikleştirme
Yerel makinelerde şirket içinde barındırılan Integration Runtime yüklemesini otomatik hale getirmek için (Bunun yerine Kaynak Yöneticisi şablonundan yararlandığımız Azure sanal makineleri dışında), yerel PowerShell betiklerini kullanabilirsiniz. Bu makalede, kullanabileceğiniz iki komut dosyası tanıtılmıştır.

## <a name="prerequisites"></a>Önkoşullar

* Yerel makinenizde PowerShell 'i başlatın. Betikleri çalıştırmak için **yönetici olarak çalıştır**' ı seçmeniz gerekir.
* Şirket içinde barındırılan tümleştirme çalışma zamanı yazılımını [indirin](https://www.microsoft.com/download/details.aspx?id=39717) . İndirilen dosyanın bulunduğu yolu kopyalayın. 
* Şirket içinde barındırılan tümleştirme çalışma zamanını kaydetmek için bir **kimlik doğrulama anahtarına** de ihtiyacınız vardır.
* El ile güncelleştirmelerin otomatikleştirilmesi için, önceden yapılandırılmış bir şirket içinde barındırılan tümleştirme çalışma zamanına sahip olmanız gerekir.

## <a name="scripts-introduction"></a>Betiklerin tanıtımı 

> [!NOTE]
> Bu betikler, şirket içinde barındırılan tümleştirme çalışma zamanındaki [belgelenen komut satırı yardımcı programı](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) kullanılarak oluşturulur. Gerekirse, bu betikleri Otomasyon ihtiyaçlarına uygun şekilde özelleştirebilir.
> Betiklerin düğüm başına uygulanması gerekir, bu nedenle yüksek kullanılabilirlik kurulumu (2 veya daha fazla düğüm) durumunda tüm düğümlerde çalıştırıldığınızdan emin olun.

* Kurulumu otomatik hale getirmek için: **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** kullanarak yeni bir şirket içinde barındırılan tümleştirme çalışma zamanı düğümü yükleme ve kaydetme-betik, şirket içinde barındırılan tümleştirme çalışma zamanı düğümünü yüklemek ve bir kimlik doğrulama anahtarına kaydetmek için kullanılabilir. Komut dosyası, **önce** şirket içinde [barındırılan tümleştirme çalışma zamanının](https://www.microsoft.com/download/details.aspx?id=39717) konumunu yerel bir diskte belirterek, **ikinci** olarak **kimlik doğrulama anahtarını** belirten (Şirket içinde barındırılan IR düğümünü kaydetmek için) iki bağımsız değişkeni kabul eder.

* El ile güncelleştirmelerin otomatikleştirilmesi için: şirket içinde barındırılan IR düğümünü belirli bir sürümle veya en son sürüm **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** güncelleştirme-bu, otomatik güncelleştirmeyi kapatmış olmanız veya güncelleştirmeler üzerinde daha fazla denetime sahip olmanız durumunda da desteklenir. Komut dosyası, şirket içinde barındırılan tümleştirme çalışma zamanı düğümünü en son sürüme veya belirtilen daha yüksek bir sürüme (düşürme çalışmıyor) güncelleştirmek için kullanılabilir. Sürüm numarası belirtmek için bir bağımsız değişken kabul eder (örnek:-Version 3.13.6942.1). Sürüm belirtilmediğinde, her zaman kendiliğinden konak IR 'yi [indirmelerde](https://www.microsoft.com/download/details.aspx?id=39717)bulunan en son sürüme güncelleştirir.
    > [!NOTE]
    > Yalnızca son 3 sürüm belirtilebilir. İdeal olarak, mevcut bir düğümü en son sürüme güncelleştirmek için kullanılır. **kayıtlı BIR kendı kendıne BARıNDıRıLAN IR olduğunu varsayar**. 

## <a name="usage-examples"></a>Kullanım örnekleri

### <a name="for-automating-setup"></a>Kurulumu otomatikleştirme için
1. Şirket içinde barındırılan IR 'yi [buradan](https://www.microsoft.com/download/details.aspx?id=39717)indirin. 
1. Yukarıdaki indirilen SHIR MSI 'nin (yükleme dosyası) bulunduğu yolu belirtin. Örneğin, yol *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi* ise, bu görev için PowerShell komut satırı örneğini aşağıdaki şekilde kullanabilirsiniz:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > IR 'nizi kaydetmek için [key] öğesini kimlik doğrulama anahtarıyla değiştirin.
    > "Username" değerini kullanıcı adınızla değiştirin.
    > Betiği çalıştırırken "InstallGatewayOnLocalMachine.ps1" dosyasının konumunu belirtin. Bu örnekte, bunu masaüstünde depoladık.

1. Makinenizde önceden yüklenmiş bir otomatik olarak barındırılan IR varsa, komut dosyası otomatik olarak kaldırır ve yeni bir tane yapılandırır. Şu pencerenin ölçeğini görürsünüz: ![ Integration Runtime 'ı yapılandırma](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Yükleme ve anahtar kaydı tamamlandığında, *ağ geçidini yükleme* ve yerel PowerShell 'de *ağ geçidi sonuçlarını kaydetme* işleminin başarılı olduğunu görürsünüz.
        [![betik 1 çalıştırma sonucu](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>El ile güncelleştirmelerin otomatikleştirilmesi için
Bu betik, en son şirket içinde barındırılan tümleştirme çalışma zamanını güncelleştirmek/yüklemek ve kaydetmek için kullanılır. Betik çalıştırması aşağıdaki adımları gerçekleştirir:
1. Geçerli şirket içinde barındırılan IR sürümünü denetle
2. Bağımsız değişkenden en son sürümü veya belirtilen sürümü Al
3. Geçerli sürümden daha yeni bir sürüm varsa:
    * Şirket içinde barındırılan IR MSI 'yi indirin
    * Bunu yükseltin

Bu betiği kullanmak için aşağıdaki komut satırı örneğini takip edebilirsiniz:
* En son ağ geçidini indir ve Yükle:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Belirtilen sürüme ait ağ geçidini indir ve Yükle:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Geçerli sürümünüz zaten en son sürüm ise, bir güncelleştirme gerekmediği için aşağıdaki sonucu görürsünüz.   
    [![betik 2 çalıştırma sonucu](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)