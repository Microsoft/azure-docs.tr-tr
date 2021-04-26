---
title: Azure Lab Services ve GNS3 ile bir ağ Laboratuvarı ayarlama | Microsoft Docs
description: GNS3 ile ağ öğretmek için Azure Lab Services kullanarak laboratuvar ayarlamayı öğrenin.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500519"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Ağ sınıfı öğretmek için laboratuvar ayarlama 
Bu makalede, öğrencilerin [GNS3](https://www.gns3.com/) yazılımını kullanarak sanal ve gerçek ağları öykünmesine, yapılandırmasına, test etme ve sorunlarını gidermenize izin veren bir sınıfın nasıl ayarlanacağı gösterilmektedir. 

Bu makalede iki ana bölüm bulunur. Birinci bölüm, sınıf laboratuvarının nasıl oluşturulacağını ele alır. İkinci bölüm, iç içe sanallaştırma etkinleştirilmiş ve GNS3 yüklü ve yapılandırılmış olan şablon makinenin nasıl oluşturulacağını ele alır.

## <a name="lab-configuration"></a>Laboratuvar yapılandırması
Bu Laboratuvarı kurmak için başlamak üzere bir Azure aboneliğine sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Bir Azure aboneliği aldıktan sonra, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilir veya mevcut bir hesabı kullanabilirsiniz. Yeni bir laboratuvar hesabı oluşturmak için aşağıdaki öğreticiye bakın: [Laboratuvar hesabı ayarlama öğreticisi](tutorial-setup-lab-account.md).

Yeni bir laboratuvar oluşturmak ve ardından aşağıdaki ayarları uygulamak için [Bu öğreticiyi](tutorial-setup-classroom-lab.md) izleyin:

| Sanal makine boyutu | Görüntü |
| -------------------- | ----- | 
| Büyük (Iç Içe sanallaştırma) | Windows 10 Pro, sürüm 1909 |

## <a name="template-machine"></a>Şablon makinesi 

Şablon makinesi oluşturulduktan sonra, aşağıdaki üç ana görevi gerçekleştirmek için makineyi başlatın ve bu sunucuya bağlanın. 
 
1. Şablon makinesini iç içe sanallaştırma için hazırlayın.
2. GNS3 'i yükler.
3. Hyper-V ' d a iç içe GNS3 VM oluşturun.
4. Windows Hyper-V VM kullanmak için GNS3 'ı yapılandırın.
5. Uygun gereçler ekleyin.
6. Şablonu Yayımla.


### <a name="prepare-template-machine-for-nested-virtualization"></a>İç içe sanallaştırma için şablon makinesini hazırlama
- Şablon sanal makinenizi iç içe sanallaştırma için hazırlamak üzere [Bu makaledeki](how-to-enable-nested-virtualization-template-vm.md) yönergeleri izleyin. 

### <a name="install-gns3"></a>GNS3 'i yükler
- [Windows ÜZERINDE GNS3 yükleme](https://docs.gns3.com/docs/getting-started/installation/windows)yönergelerini izleyin.  **GNS3 VM** 'yi yükleme bileşeni iletişim kutusuna dahil ettiğinizden emin olun, aşağıya bakın.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Sonuç olarak GNS3 VM seçimine ulaşabilirsiniz. **Hyper-V** seçeneğini seçtiğinizden emin olun.

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Bu seçenek, Hyper-V Yöneticisi 'nde GNS3 VM oluşturmak için PowerShell betiği ve VHD dosyalarını indirir. Yüklemeye varsayılan değerleri kullanarak devam edin. **Kurulum tamamlandıktan sonra, GNS3 'i başlatmayın**.

### <a name="create-gns3-vm"></a>GNS3 VM oluşturma
Kurulum tamamlandıktan sonra, Hyper-V VM 'yi oluşturmak için sürücüleri ve PowerShell betiğini içeren bir **"GNS3.VM.Hyper-V.2.2.17.zip"** ZIP dosyası yükleme dosyası ile aynı klasöre indirilir.
- GNS3.VM.Hyper-V.2.2.17.zip **Tümünü ayıklayın** .  Bu işlem, VM 'yi oluşturmak için sürücüleri ve PowerShell betiğini ayıklar.
- "create-vm.ps1" PowerShell betiği üzerinde, dosyaya sağ tıklayarak **PowerShell Ile çalıştırın** .
- Bir yürütme Ilkesi değişiklik isteği gösterilebilir. Betiği yürütmek için "Y" yazın.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Betik tamamlandıktan sonra, Hyper-V Yöneticisi 'nde "GNS3 VM" sanal makinesi oluşturulduğunu doğrulayabilirsiniz.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>GNS3 'i Hyper-V VM kullanacak şekilde yapılandırma
Artık GNS3 yüklendi ve GNS3 VM eklendikçe, ikisini birlikte bağlamak için GNS3 'yi başlatın.  [GNS3 Kurulum Sihirbazı otomatik olarak başlayacak.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)..  
- **Sanal makineden çalıştırma gereçlerini kullanın.** seçeneğini etkinleştirebilir veya devre dışı bırakabilirsiniz.  **VMware vmrun aracı bulunamadığı** sürece sihirbazın geri kalanı için varsayılanları kullanın. hatası döndürür.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- **Tamam**' ı seçin ve sihirbazın oturumunu **iptal edin** .
- Hyper-v VM bağlantısını gerçekleştirmek için, **düzenleme**  ->  **tercihleri**  ->  **GNS3 VM** 'sini açın ve **GNS3 VM 'yi etkinleştir** ' i seçin ve **Hyper-V** seçeneğini belirleyin.
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Uygun gereçler ekleyin

Bu noktada, [sınıfının uygun gereçlerini](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace) eklemek isteyeceksiniz.

### <a name="publish-template"></a>Şablonu yayımlama

Artık şablon VM 'si doğru şekilde ayarlandığına ve yayımlanmaya hazır olduğuna göre, denetlenecek birkaç anahtar noktası vardır.
- GNS3 VM 'sinin kapanmakta veya kapalı olduğundan emin olun.  VM çalışmaya devam ederken yayımlama işlemi VM 'yi bozuyor.
- GNS3 kapatın, yayımlama sırasında ve çalışırken istenmeyen yan etkilere neden olabilir.
- Tüm yükleme dosyalarını veya gereksiz dosyaları temizleyin.

## <a name="cost"></a>Maliyet  

Bu laboratuvarın maliyetini tahmin etmek isterseniz, aşağıdaki örneği kullanabilirsiniz: 
 
20 saatlik zamanlanan sınıf süresi ve ev ödevi veya atamalar için 10 saatlik kota içeren 25 öğrenciye sahip bir sınıf için, laboratuvar fiyatı şöyle olacaktır: 

25 öğrenci * (20 + 10) saat * 84 laboratuvar birimi * 0,01 saat başına USD = 630 ABD Doları. 

**Önemli:** Maliyet tahmini yalnızca örnek amaçlıdır.  Fiyatlandırma hakkındaki güncel Ayrıntılar için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç
Bu makale, GNS3 kullanarak ağ yapılandırması için Laboratuvar oluşturma adımlarında size rehberlik sağlar.

## <a name="next-steps"></a>Sonraki adımlar
Sonraki adımlar herhangi bir laboratuvarı ayarlamak için ortaktır:

- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Öğrenciler Için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users).