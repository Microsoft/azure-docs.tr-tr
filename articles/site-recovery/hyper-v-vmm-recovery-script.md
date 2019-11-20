---
title: Azure Site Recovery bir kurtarma planına betik ekleme
description: VMM bulutlarındaki Hyper-V VM 'lerinin olağanüstü durum kurtarması için bir kurtarma planına VMM betiği eklemeyi öğrenin.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084865"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Kurtarma planına VMM betiği ekleme

Bu makalede, bir System Center Virtual Machine Manager (VMM) komut dosyasının nasıl oluşturulacağı ve [Azure Site Recovery](site-recovery-overview.md)bir kurtarma planına nasıl ekleneceği açıklanmaktadır.

Bu makalenin alt kısmında veya [Azure kurtarma hizmetleri Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)' nda herhangi bir yorum veya soru gönderin.

## <a name="prerequisites"></a>Ön koşullar

Kurtarma planlarınızda PowerShell betikleri kullanabilirsiniz. Kurtarma planından erişilebilmesi için betiği yazıp VMM kitaplığına yerleştirmeniz gerekir. Betiği yazarken aşağıdaki noktaları göz önünde bulundurun:

* Özel durumların düzgün şekilde işlenebilmesi için betiklerin try-catch bloklarını kullandığından emin olun.
    - Betikte bir özel durum oluşursa, betik çalışmayı durdurduktan sonra görev başarısız olarak gösterilir.
    - Bir hata oluşursa betiğin geri kalanı çalıştırılmaz.
    - Planlanmamış yük devretme çalıştırdığınızda bir hata oluşursa, kurtarma planı devam eder.
    - Planlı Yük devretme çalıştırdığınızda bir hata oluşursa, kurtarma planı duraklar. Betiği düzelttikten sonra beklendiği gibi çalıştığını denetleyip kurtarma planını yeniden çalıştırın.
        - `Write-Host` komutu bir kurtarma planı komut dosyasında çalışmıyor. Bir betikte `Write-Host` komutunu kullanırsanız, komut dosyası başarısız olur. Çıkış oluşturmak için, ana komut dosyanızı çalıştıran bir proxy betiği oluşturun. Tüm çıktının kullanıma hazır olduğundan emin olmak için **\>\>** komutunu kullanın.
        - Kod, 600 saniye içinde döndürülmezse zaman aşımına uğrar.
        - STDERR 'e herhangi bir şey yazılmışsa, komut dosyası başarısız olarak sınıflandırılır. Bu bilgiler betik yürütme ayrıntılarında görüntülenir.

* Kurtarma planındaki betikler VMM hizmet hesabı bağlamında çalışır. Bu hesabın, betiğin bulunduğu uzak paylaşımda okuma izinlerine sahip olduğundan emin olun. Betiği VMM hizmet hesabıyla aynı düzeyde kullanıcı haklarıyla çalışacak şekilde test edin.
* VMM cmdlet 'leri bir Windows PowerShell modülünde dağıtılır. Modül, VMM konsolunu yüklediğinizde yüklenir. Modülünü betiğe yüklemek için komut dosyasında aşağıdaki komutu kullanın: 

    `Import-Module -Name virtualmachinemanager`

    Daha fazla bilgi için bkz. [Windows PowerShell ve VMM ile çalışmaya başlama](https://technet.microsoft.com/library/hh875013.aspx).
* VMM dağıtımınızda en az bir kitaplık sunucusuna sahip olduğunuzdan emin olun. Varsayılan olarak, VMM sunucusunun kitaplık paylaşma yolu VMM sunucusunda yerel olarak bulunur. Klasör adı MSCVMMLibrary ' dir.

  Kitaplık paylaşım yolunuz uzak ise (veya yerel ise ve MSCVMMLibrary ile paylaşılmışsa), bir örnek olarak \\libserver2. contoso. com\share\ kullanarak, aşağıdaki gibi bir paylaşım yapılandırın:
  
  1. Kayıt defteri düzenleyicisini açın ve **HKEY_LOCAL_MACHINE \SOFTWARE\MICROSOFT\Azure site Recovery\Registration**adresine gidin.

  1. **Scriptlibrarypath** için değeri **\\\libserver2.contoso.com\share\\** olacak şekilde değiştirin. Tam FQDN 'yi belirtin. Paylaşma konumuna izin verin. Bu, paylaşımın kök düğümüdür. Kök düğümü denetlemek için, VMM 'de, kitaplıktaki kök düğüme gidin. Açılan yol yolun köküdür. Bu, değişkeninde kullanmanız gereken yoldur.

  1. VMM hizmet hesabıyla aynı düzeyde kullanıcı haklarına sahip bir kullanıcı hesabı kullanarak betiği test edin. Bu kullanıcı haklarının kullanılması, tek başına çalıştırılan ve sınanan betiklerin kurtarma planlarında çalıştıkları şekilde çalıştığını doğrular. VMM sunucusunda, yürütme ilkesini aşağıdaki gibi geçiş olarak ayarlayın:

     a. Yönetici olarak **64-bit Windows PowerShell** konsolunu açın.
     
     b. **Set-executionpolicy bypass**girin. Daha fazla bilgi için bkz. [set-ExecutionPolicy cmdlet 'Ini kullanma](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > **Set-executionpolicy bypass** yalnızca 64-bit PowerShell konsolunda ayarlanır. Bunu 32-bit PowerShell konsolu için ayarlarsanız betikler çalıştırılmaz.

## <a name="add-the-script-to-the-vmm-library"></a>Betiği VMM kitaplığına ekleme

VMM kaynak siteniz varsa, VMM sunucusunda bir komut dosyası oluşturabilirsiniz. Ardından, betiği kurtarma planınıza dahil edin.

1. Kitaplık paylaşımında yeni bir klasör oluşturun. Örneğin, > \Msscvmmlibrary\rpscriptsvmm sunucu adını \<. Klasörü kaynak ve hedef VMM sunucularına yerleştirin.
1. Betiği oluşturun. Örneğin, komut dosyası RPScript olarak adlandırın. Betiğin beklendiği gibi çalıştığını doğrulayın.
1. Betiği, kaynak ve hedef VMM sunucularındaki \<VMM sunucu adı > \MSSCVMMLibrary klasörüne yerleştirin.

## <a name="add-the-script-to-a-recovery-plan"></a>Betiği bir kurtarma planına ekleyin

Bir kurtarma planına VM 'Ler veya çoğaltma grupları ekledikten ve planı oluşturduktan sonra, betiği gruba ekleyebilirsiniz.

1. Kurtarma planını açın.
1. **Adım** listesinde bir öğe seçin. Ardından, **komut dosyası** veya **el ile eylem**' i seçin.
1. Seçili öğeden önce veya sonra betik ya da eylemin eklenip eklenmeyeceğini belirtin. Betiğin konumunu yukarı veya aşağı taşımak için **Yukarı taşı** ve **aşağı taşı** düğmelerini seçin.
1. Bir VMM betiği eklerseniz, **VMM betiğine yük devretme**' yı seçin. **Betik yolu**' nda, paylaşımın göreli yolunu girin. Örneğin, **\Rpscripts\rpscript.exe. ps1**yazın.
1. Bir Azure Otomasyonu runbook 'u eklerseniz, runbook 'un bulunduğu Otomasyon hesabını belirtin. Ardından, kullanmak istediğiniz Azure runbook betiğini seçin.
1. Betiğin beklendiği gibi çalıştığından emin olmak için kurtarma planının yük devretmesini test edin.


## <a name="next-steps"></a>Sonraki adımlar
* [Yük devretme çalıştırma](site-recovery-failover.md)hakkında daha fazla bilgi edinin.

