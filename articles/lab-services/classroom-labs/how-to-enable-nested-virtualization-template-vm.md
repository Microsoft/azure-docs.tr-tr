---
title: Azure Lab Services | bir şablon sanal makinesinde iç içe sanallaştırmayı etkinleştirme | Microsoft Docs
description: Azure Lab Services bir şablon sanal makinesinde iç içe sanallaştırmayı nasıl etkinleştirebileceğinizi öğrenin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71982001"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Azure Lab Services bir şablon sanal makinesinde iç içe sanallaştırmayı etkinleştir
Bu makalede, Azure Lab Services bir şablon sanal makinesinde iç içe sanallaştırmanın nasıl ayarlanacağı ele alınmaktadır. Bir sınıftaki her öğrenci birden çok makine gerektirdiğinde Azure Lab Services iç içe sanallaştırma kullanılır.
 
## <a name="considerations"></a>Dikkat edilmesi gerekenler
İç içe sanallaştırmaya sahip bir laboratuvar ayarlamadan önce göz önünde bulundurmanız gereken birkaç nokta vardır.

- Yeni bir laboratuvar oluştururken **Orta (Iç Içe sanallaştırma)** veya sanal makine boyutu için **büyük** boyutlarda ' ı seçin. Bu sanal makine boyutları, iç içe sanallaştırmayı destekler. 
- Hem konak hem de istemci sanal makineleri için iyi performans sağlayacak bir boyut seçin.  Sanallaştırmayı kullanırken, seçtiğiniz boyutun yalnızca bir makine değil, ana bilgisayarın yanı sıra aynı anda çalıştırılması gereken tüm istemci makineler için yeterli olması gerektiğini unutmayın.
- İstemci sanal makinelerinin Azure sanal ağındaki DNS sunucuları gibi Azure kaynaklarına erişimi olmayacaktır.
- Konak sanal makinesi, istemci makinenin internet bağlantısına izin vermek için kurulum gerektirir. 
- İstemci sanal makineleri bağımsız makineler olarak lisanslanır. Microsoft Operasyon sistemleri ve ürünleri için lisanslama hakkında bilgi için bkz. [Microsoft lisanslama](https://www.microsoft.com/licensing/default) . Şablon makinesini ayarlamadan önce kullanılan diğer yazılımlar için lisans sözleşmelerini denetleyin.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Bir şablon VM 'de iç içe sanallaştırmayı etkinleştirme
Bu bölümdeki adımlar, Windows Server 2016 veya Windows Server 2019 için iç içe sanallaştırmayı ayarlamaya odaklanmaktadır. Hyper-V ile şablon makinesi ayarlamak için bir komut dosyası kullanacaksınız. Otomatikleştirilmiş bir çözüm için bkz. [Laboratuvar Hizmetleri Hyper-V betiklerdeki](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)betikler. Aşağıdaki adımlar, komut dosyasını nasıl kullanacağınızı adım adım gösterecektir.

1. Internet Explorer kullanıyorsanız, güvenilen siteler listesine `https://github.com` eklemeniz gerekebilir. 
    1. Internet Explorer 'ı açın.
    1. Dişli simgesini seçin ve **Internet seçenekleri**' ni seçin.  
    1. **Internet seçenekleri** iletişim kutusu göründüğünde, **güvenlik**' i seçin, **Güvenilen siteler**' i seçin, **siteler** ' i tıklatın.
    1. **Güvenilen siteler** iletişim kutusu göründüğünde, güvenilir web siteleri listesine `https://github.com` ekleyin ve **Kapat**' ı seçin.

        ![Güvenilen siteler](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Git deposu dosyalarını aşağıdaki adımlarda özetlenen şekilde indirin.  Alternatif olarak, git deposu [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)' den kopyalanabilir. 
    1. [@No__t-1](https://github.com/Azure/azure-devtestlab/)' e gidin.
    1. ***Kopyala veya indir** düğmesine tıklayın.
    1. **ZIP 'ı indir**' e tıklayın.
    1. ZIP dosyasını Ayıkla
1. **PowerShell** 'i **yönetici** modunda başlatın.
1. PowerShell penceresinde, indirilen betiği içeren klasöre gidin. Depo dosyalarının üst klasöründen gezindiğinizde, betik `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` konumunda bulunur.
1. Betiği başarıyla çalıştırmak için yürütme ilkesini değiştirmeniz gerekebilir. Şu komutu çalıştırın:
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. Betiği çalıştırın:
    
    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Betik için makinenin yeniden başlatılması gerekebilir. Betikteki yönergeleri izleyin ve komut dosyası **tamamlanana** kadar çıkışta betiği yeniden çalıştırın.
1. Yürütme ilkesini sıfırlamayı unutmayın. Şu komutu çalıştırın: 

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Sonuç
Şablon makineniz artık Hyper-V sanal makineleri oluşturmak için hazırdır. Bkz. Hyper-V sanal makineleri oluşturma hakkında yönergeler için bkz. [Hyper-v ' d e sanal makine oluşturma](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Ayrıca, kullanılabilir işletim sistemlerini ve yazılımlarını kullanıma almak için bkz. [Microsoft değerlendirme merkezi](https://www.microsoft.com/evalcenter/) .  

## <a name="next-steps"></a>Sonraki adımlar 
Aşağıdaki makalelere bakın:

- [Azure VM 'de iç içe sanallaştırmayı etkinleştirme](../../virtual-machines/windows/nested-virtualization.md) 
- [Windows Server 'da Hyper-V rolünü yükler](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Laboratuvar Hizmetleri Hyper-V betikleri](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
