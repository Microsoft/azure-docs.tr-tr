---
title: Windows 7 sanal makinesini dağıtma Windows sanal masaüstü-Azure
description: Windows sanal masaüstü 'nde Windows 7 sanal makinesini yapılandırma ve dağıtma.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 65ef7af1247fdb38a7c85c1a8134257942f5ef5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447038"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Windows Sanal Masaüstü’nde Windows 7 sanal makinesi dağıtma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)bakın.

Windows sanal masaüstü üzerinde bir Windows 7 sanal makinesi (VM) dağıtma işlemi, Windows 'un sonraki sürümlerini çalıştıran VM 'lerden biraz farklıdır. Bu kılavuzda, Windows 7 ' nin nasıl dağıtılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, bir konak havuzu oluşturmak için [PowerShell ile konak havuzu oluşturma](create-host-pools-powershell.md) ' daki yönergeleri izleyin. Portalı kullanıyorsanız, [Azure Portal kullanarak bir konak havuzu oluşturmak için](create-host-pools-azure-marketplace.md)adım 1 ' den 9 ' a kadar olan yönergeleri izleyin. Bundan sonra boş bir konak havuzu oluşturmak için **gözden geçir + oluştur** ' u seçin.

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 sanal makinesini yapılandırma

Önkoşulları tamamladıktan sonra, Windows 7 VM 'nizi Windows sanal masaüstü 'nde dağıtıma yapılandırmaya hazırsınız demektir.

Windows sanal masaüstü 'nde Windows 7 VM ayarlamak için:

1. Azure portal oturum açın ve Windows 7 Enterprise görüntüsünü arayın ya da kendi özelleştirilmiş Windows 7 Enterprise (x64) görüntünüzü karşıya yükleyin.
2. Windows 7 Enterprise ile bir veya birden çok sanal makineyi konak işletim sistemi olarak dağıtın. Sanal makinelerin Uzak Masaüstü Protokolü (RDP) izin ver (TCP/3389 bağlantı noktası) olduğundan emin olun.
3. RDP kullanarak Windows 7 Enterprise konağına bağlanın ve dağıtımınızı yapılandırırken tanımladığınız kimlik bilgileriyle kimlik doğrulaması yapın.
4. Ana bilgisayara RDP ile bağlanırken kullandığınız hesabı "uzak masaüstü kullanıcısı" grubuna ekleyin. Hesabı eklememeniz durumunda, Active Directory etki alanınıza ekledikten sonra VM 'ye bağlanamadınız.
5. SANAL makinenizde Windows Update gidin.
6. Tüm Windows güncelleştirmelerini önemli kategoride yükler.
7. Tüm Windows güncelleştirmelerini Isteğe bağlı kategoride (dil paketleri hariç) yükler. Bu işlem, bu yönergeleri tamamlayabilmeniz için ihtiyaç duyduğunuz Uzak Masaüstü Protokolü 8,0 güncelleştirmesini ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) yüklüyor.
8. Yerel Grup İlkesi Düzenleyicisi açın ve   >    >    >    >  **Uzak Masaüstü oturumu ana bilgisayarı**  >  **uzak oturum ortamı** Uzak Masaüstü Hizmetleri Windows bileşenleri Yönetim Şablonları bilgisayar yapılandırması ' na gidin.
9. Uzak Masaüstü Protokolü 8,0 ilkesini etkinleştirin.
10. Bu sanal makineyi Active Directory etki alanınıza ekleyin.
11. Aşağıdaki komutu çalıştırarak sanal makineyi yeniden başlatın:

     ```cmd
     shutdown /r /t 0
     ```

12. Kayıt belirteci almak için [buradaki](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo) yönergeleri izleyin.

      - Azure portal kullanmayı tercih ediyorsanız, VM 'yi eklemek istediğiniz konak havuzunun genel bakış sayfasına gidebilir ve burada bir belirteç oluşturabilirsiniz.

13. Windows [7 Için Windows sanal masaüstü aracısını indirin](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. Windows [7 Için Windows sanal masaüstü Aracısı Yöneticisi 'Ni indirin](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Windows sanal masaüstü Aracısı yükleyicisini açın ve yönergeleri izleyin. İstendiğinde, 12. adımda oluşturduğunuz kayıt anahtarını verin.
16. Windows sanal masaüstü Aracısı yöneticisini açın ve yönergeleri izleyin.
17. İsteğe bağlı olarak, VM 'ye doğrudan Uzak Masaüstü Protokolü erişimi kaldırmak için TCP/3389 bağlantı noktasını engelleyin.
18. İsteğe bağlı olarak, .NET Framework 'ün en az sürüm 4.7.2 olduğunu doğrulayın. Özel bir görüntü oluşturuyorsanız, iskekinizi güncelleştirme özellikle önemlidir.

## <a name="next-steps"></a>Sonraki adımlar

Windows sanal masaüstü dağıtımınız artık kullanıma hazırdır. Başlamak için [Windows sanal masaüstü istemcisinin en son sürümünü indirin](https://aka.ms/wvd/clients/windows) .

Windows sanal masaüstündeki Windows 7 ' de bilinen sorunların ve sorun giderme yönergelerinin bir listesi için, Windows [sanal masaüstündeki Windows 7 sanal makinelerinde sorun](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md)giderme makalesindeki sorun giderme makalemizi inceleyin.
