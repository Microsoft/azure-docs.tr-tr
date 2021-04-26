---
title: Portalı kullanarak yakın yerleştirilen bir grup oluşturma
description: Azure portal kullanarak yakınlık yerleşimi grubu oluşturmayı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: daf844870670c14db5208f45fbd9c9adf46be985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504635"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Azure portal kullanarak bir yakınlık yerleşimi grubu oluşturma

Olası en düşük gecikme süresini elde etmek üzere VM 'Leri olabildiğince yakın bir şekilde almak için, bunları bir [yakınlık yerleşimi grubu](../co-location.md#proximity-placement-groups)içinde dağıtmanız gerekir.

Yakınlık yerleşimi grubu, Azure işlem kaynaklarının fiziksel olarak birbirlerine yakın bir yerde bulunduğundan emin olmak için kullanılan mantıksal bir gruplandırmadır. Yakınlık yerleşimi grupları, düşük gecikme süresinin gereksinim olduğu iş yükleri için faydalıdır.

> [!NOTE]
> Yakınlık yerleşimi grupları adanmış konaklarla kullanılamaz.
>
> Kullanılabilirlik bölgelerini yerleştirme gruplarıyla birlikte kullanmak istiyorsanız, yerleştirme grubundaki VM 'Lerin aynı Kullanılabilirlik bölgesinde da olduğundan emin olmanız gerekir.
>

## <a name="create-the-proximity-placement-group"></a>Yakınlık yerleşimi grubunu oluşturma

1. Aramada **yakınlık yerleşimi grubunu** yazın.
1. Arama sonuçlarındaki **Hizmetler** ' ın altında **yakınlık yerleşimi grupları**' nı seçin.
1. **Yakınlık yerleşimi grupları** sayfasında **Ekle**' yi seçin.
1. **Temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, doğru aboneliğin seçildiğinden emin olun.
1. **Kaynak grubu** ' nda, yeni bir grup oluşturmak Için **Yeni oluştur** ' u seçin veya zaten var olan boş bir kaynak grubunu seçerek açılır listeden seçin. 
1. **Bölge** bölümünde, yakınlık yerleşimi grubunun oluşturulmasını istediğiniz konumu seçin.
1. **Yakınlık yerleşimi grup adı** ' na bir ad yazın ve ardından **gözden geçir + oluştur**' u seçin.
1. Doğrulama geçtikten sonra, yakınlık yerleşimi grubunu oluşturmak için **Oluştur** ' u seçin.

    ![Yakınlık yerleşimi grubu oluşturma ekran görüntüsü](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>VM oluşturma

1. Portalda bir VM oluştururken **Gelişmiş** sekmesine gidin. 
1. **Yakınlık yerleşimi grubu** seçiminde doğru yerleştirme grubunu seçin. 

    ![Portalda yeni bir VM oluşturulurken yakınlık yerleşimi grup bölümünün ekran görüntüsü](./media/ppg/vm-ppg.png)

1. Diğer tüm gerekli seçimleri yapmayı tamamladığınızda, **gözden geçir + oluştur**' u seçin.
1. Doğrulamayı geçtikten sonra, sanal makineyi yerleştirme grubuna dağıtmak için **Oluştur** ' u seçin.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Kullanılabilirlik kümesindeki VM 'Leri yakınlık yerleşimi grubuna ekleme

VM, kullanılabilirlik kümesinin parçasıysa, VM 'Leri eklemeden önce kullanılabilirlik kümesini yerleştirme grubuna eklemeniz gerekir.

1. [Portal](https://portal.azure.com) *kullanılabilirlik kümeleri* için arama yapın ve sonuçlardan kullanılabilirlik kümesi ' ni seçin.
1. VM 'yi seçip VM 'nin sayfasında **Durdur** ' u seçerek ve ardından sanal makineyi durdurmak için **Tamam** ' ı seçerek KULLANıLABILIRLIK kümesindeki her bir VM 'yi serbest bırakın.
1. Kullanılabilirlik kümesinin sayfasında, tüm VM 'Lerin **durumunun** **durduruldu (serbest bırakıldı)** olarak listelendiğinden emin olun.
1. Soldaki menüden **Yapılandırma**'yı seçin.
1. **Yakınlık yerleşimi grubu** altında, açılır listeden bir yerleştirme grubu seçin ve ardından **Kaydet**' i seçin.
1. VM 'lerin listesini yeniden görmek için sol menüden **Genel Bakış ' ı** seçin. 
1. Kullanılabilirlik kümesindeki her bir VM 'yi seçin ve sonra her VM için sayfada **Başlat** ' ı seçin. 


## <a name="add-existing-vm-to-placement-group"></a>Mevcut VM 'yi yerleştirme grubuna ekle 


1. VM 'nin sayfasında **Durdur**' u seçin.
1. VM 'nin durumu **durduruldu (serbest bırakıldı)** olarak listeleniyorsa, sol menüde **yapılandırma** ' yı seçin.
1. **Yakınlık yerleşimi grubu** altında, açılır listeden bir yerleştirme grubu seçin ve ardından **Kaydet**' i seçin.
1. Sol menüden **genel bakış** ' ı seçin ve ardından sanal makineyi yeniden başlatmak için **Başlat** ' ı seçin.

 

## <a name="next-steps"></a>Sonraki adımlar

Yakınlık yerleştirme grupları oluşturmak için [Azure PowerShell](proximity-placement-groups.md) de kullanabilirsiniz.