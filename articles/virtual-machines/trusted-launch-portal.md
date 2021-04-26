---
title: "Önizleme: güvenilen bir başlatma VM 'si dağıtma"
description: Güvenilen başlatma kullanan bir VM dağıtın.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 295579d17f3b24adcf43f6907cc4b1aca01dcae2
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565925"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Güvenilir başlatma etkin bir VM dağıtma (Önizleme)

[Güvenilir başlatma](trusted-launch.md) [2. nesil](generation-2.md) VM 'lerin güvenliğini geliştirmenin bir yoludur. Güvenilen başlatma, vTPM ve güvenli önyükleme gibi altyapı teknolojilerini birleştirerek gelişmiş ve kalıcı saldırı tekniklerine karşı koruma sağlar.

> [!IMPORTANT]
> Güvenilen başlatma Şu anda genel önizlemededir.
> 
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Portalı kullanarak dağıtma

Güvenilir başlatma etkin olan bir sanal makine oluşturun.

1. Azure [portalında](https://aka.ms/TL_preview)oturum açın.
   > [!NOTE] 
   > Portal bağlantısı, güvenilir başlatma önizlemesi için benzersizdir.
   >  
2. **Sanal makineler** için arama yapın.
3. **Hizmetler** altında **sanal makineler**' i seçin.
4. **Sanal makineler** sayfasında, **Ekle**' yi ve ardından **sanal makine**' yi seçin.
5. **Proje ayrıntıları**' nın altında, doğru aboneliğin seçildiğinden emin olun.
6. **Kaynak grubu** altında **Yeni oluştur** ' u seçin ve kaynak grubunuz için bir ad yazın veya açılan listeden var olan bir kaynak grubunu seçin.
7. **Örnek ayrıntıları**' nın altında, sanal makine adı için bir ad yazın ve [Güvenilen başlatmayı](trusted-launch.md#public-preview-limitations)destekleyen bir bölge seçin.
8. **Görüntü** altında, [güvenilir başlatmayı destekleyen](trusted-launch.md#public-preview-limitations)bir gen 2 görüntüsü seçin. Aşağıdaki iletiyi görtığınızdan emin olun: **Bu görüntü, güvenilen başlatma önizlemesini destekler. Gelişmiş sekmesinde yapılandırın**.
   > [!TIP]
   > Açılan kutuda istediğiniz görüntünün Gen 2 sürümünü görmüyorsanız, **tüm görüntüleri görüntüle** ' yi seçin ve ardından yalnızca Gen 2 görüntülerini göstermek Için **VM oluşturma** filtresini değiştirin. Listede görüntüyü bulun, sonra da Gen 2 sürümünü seçmek için **Seç** açılan listesini kullanın.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Bunun, güvenilir başlatmayı destekleyen bir Gen2 görüntüsü olduğunu onaylayan iletiyi gösteren ekran görüntüsü.":::

13. Güvenilen başlatmayı destekleyen bir VM boyutu seçin. [Desteklenen boyutlar](trusted-launch.md#public-preview-limitations)listesine bakın.
14. **Yönetici hesabı** bilgilerini ve ardından **gelen bağlantı noktası kurallarını** girin. 
1. Sayfanın üst kısmında seçerek **Gelişmiş** sekmesine geçin.
1. **VM oluşturma** bölümüne gidin. **Gen 2** ' nin seçili olduğundan emin olun.
1. Hala **Gelişmiş** sekmesinde, **güvenilir başlatma**' ya gidin ve **güvenilir başlatma** onay kutusunu seçin. Böylece, güvenli önyükleme ve vTPM olmak üzere iki seçenek daha görünecektir. Dağıtımınız için uygun seçenekleri seçin.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Güvenilir başlatma seçeneklerini gösteren ekran görüntüsü.":::

15. Sayfanın alt kısmındaki **gözden geçir + oluştur** ' u seçin.
16. **Sanal makine oluştur** sayfasında, dağıtmak üzere olduğunuz VM hakkındaki ayrıntıları görebilirsiniz. Hazır olduğunuzda **Oluştur**'u seçin.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Doğrulama sayfasının, güvenilen başlatma seçeneklerinin gösterilmesi dahil olmak üzere Sceenshot.":::


VM'nizin dağıtılması birkaç dakika sürer. 

## <a name="deploy-using-a-template"></a>Şablon kullanarak dağıtma

Bir hızlı başlangıç şablonu kullanarak güvenilen başlatma VM 'Leri dağıtabilirsiniz:

**Linux**:    
[![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Görüntüle ve Güncelleştir

Portalda VM 'nin **genel bakış** sayfasını ziyaret ederek mevcut bir VM için güvenilir başlatma yapılandırmasını görüntüleyebilirsiniz.

Güvenilen başlatma yapılandırmasını değiştirmek için, sol taraftaki menüden **Ayarlar** bölümünde **yapılandırma** ' yı seçin. Güvenli önyükleme ve vTPM 'yi **güvenilir başlatma** bölümünden etkinleştirebilir veya devre dışı bırakabilirsiniz. İşiniz bittiğinde sayfanın en üstünde **Kaydet** ' i seçin. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Güvenilen başlatma yapılandırmasının nasıl değiştirileceği ekran görüntüsü.":::

VM çalışıyorsa, değiştirilen güvenilen başlatma yapılandırmasını uygulamak için VM 'nin yeniden başlatılacağınızı belirten bir ileti alırsınız. **Evet** ' i seçin, değişikliklerin etkili olması için VM 'nin yeniden başlatılmasını bekleyin.


## <a name="verify-secure-boot-and-vtpm"></a>Güvenli önyükleme ve vTPM 'yi doğrulama

Güvenli önyükleme ve vTPM 'nin sanal makinede etkinleştirildiğini doğrulayabilirsiniz.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: güvenli önyükleme 'nin çalıştığını doğrulama

SANAL makineye SSH ve ardından aşağıdaki komutu çalıştırın: 

```bash
mokutil --sb-state
```

Güvenli önyükleme etkin ise, komut şu şekilde döndürülür:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: vTPM 'nin etkinleştirilip etkinleştirilmediğini doğrula

VM’nizde SSH gerçekleştirin. Tpm0 cihazının mevcut olup olmadığını denetleyin: 

```bash
ls /dev/tpm0
```

VTPM etkinse, komut şu şekilde döndürülür:

```output
/dev/tpm0
```

VTPM devre dışıysa, komut şunu döndürür:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: güvenli önyükleme 'nin çalıştığını doğrulama

Uzak Masaüstü kullanarak VM 'ye bağlanın ve sonra çalıştırın `msinfo32.exe` .

Sağ bölmede, güvenli önyükleme durumunun **Açık** olduğunu denetleyin.

## <a name="enable-the-azure-security-center-experience"></a>Azure Güvenlik Merkezi deneyimini etkinleştirin

Azure Güvenlik Merkezi 'ni, güvenilen başlatma sanal makineleriniz hakkında bilgi görüntüleyecek şekilde etkinleştirmek için, birkaç ilkeyi etkinleştirmeniz gerekir. İlkeleri etkinleştirmenin en kolay yolu, bu [Kaynak Yöneticisi şablonunu](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) aboneliğinize dağıtmaktan biridir. 

İlkeleri aboneliğinize dağıtmak için aşağıdaki düğmeyi seçin:

[![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Şablonun her abonelik için yalnızca bir kez dağıtılması gerekir. Desteklenen tüm sanal makinelere otomatik olarak yüklenir `GuestAttestation` ve `AzureSecurity` uzantıları. Hata alırsanız, şablonu yeniden dağıtmaya çalışın.

Güvenilen başlatma VM 'Leri için vTPM ve güvenli önyükleme önerilerini almak üzere, [aboneliğinize özel bir girişim ekleme](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)bölümüne bakın.
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Linux 'ta güvenli önyükleme için işlemleri imzala

Bazı durumlarda, UEFı güvenli önyükleme için işlemleri imzalamanız gerekebilir.  Örneğin, Ubuntu için [Güvenli önyükleme için Işlemleri nasıl imzalayabileceğinize](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) gitmeniz gerekebilir. Bu gibi durumlarda, sanal makinenizin MOK yardımcı program kaydetme anahtarlarını girmeniz gerekir. Bunu yapmak için, MOK yardımcı programına erişmek üzere Azure seri konsolu 'nu kullanmanız gerekir.

1. Linux için Azure seri konsolunu etkinleştirin. Daha fazla bilgi için bkz. [Linux Için seri konsol](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. [Azure Portal](https://portal.azure.com)oturum açın.
1. **Sanal makineleri** arayın ve listeden VM 'nizi seçin.
1. Sol taraftaki menüde, **destek + sorun giderme** altında **seri konsol**' yi seçin. Seri konsol ile sağ tarafta bir sayfa açılır.
1. Azure seri konsolu 'Nu kullanarak VM 'de oturum açın. **Oturum açmak** IÇIN, VM oluştururken kullandığınız kullanıcı adını girin. Örneğin, *azureuser*. İstendiğinde, Kullanıcı adıyla ilişkili parolayı girin.
1. Oturum açtıktan sonra `mokutil` ortak anahtar dosyasını içeri aktarmak için kullanın `.der` .

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Yazarak makineyi Azure seri konsolundan yeniden başlatın `sudo reboot` . 10 saniyelik geri sayım başlatılır.
1. Geri sayımı kesmek ve UEFı konsol modunda beklemek için yukarı veya aşağı tuşuna basın. Süreölçer kesintiye uğrarsa, önyükleme işlemi devam eder ve tüm MOK değişiklikleri kaybolur.
1. MOK yardımcı programı menüsünden uygun eylemi seçin.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Seri konsolundaki MOK yönetimi menüsündeki kullanılabilir seçenekleri gösteren ekran görüntüsü.":::


## <a name="next-steps"></a>Sonraki adımlar

[Güvenilir başlatma](trusted-launch.md) ve [2. nesil](generation-2.md) VM 'ler hakkında daha fazla bilgi edinin.
