---
title: Azure adanmış Konakları Azure portal kullanarak dağıtma
description: Azure portal kullanarak VM 'Leri ve ölçek kümelerini adanmış ana bilgisayarlara dağıtın.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 3bc7909f38e63256d7d708ec189c628662cf8837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667339"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Portalı kullanarak VM 'Leri ve ölçek kümelerini adanmış konaklara dağıtma 

Bu makalede, sanal makinelerinizi (VM 'Ler) barındırmak için Azure [adanmış ana bilgisayar](dedicated-hosts.md) oluşturma konusunda size kılavuzluk eder. 


## <a name="limitations"></a>Sınırlamalar

- Adanmış konaklar için kullanılabilen Boyutlar ve donanım türleri bölgelere göre farklılık gösterir. Daha fazla bilgi edinmek için konak [fiyatlandırma sayfasına](https://aka.ms/ADHPricing) bakın.

## <a name="create-a-host-group"></a>Konak grubu oluştur

**Konak grubu** , adanmış Konakları koleksiyonunu temsil eden bir kaynaktır. Bir bölge ve kullanılabilirlik bölgesinde bir konak grubu oluşturur ve bu gruba ana bilgisayar ekleyebilirsiniz. Yüksek kullanılabilirlik için planlama yaparken ek seçenekler vardır. Aşağıdaki seçeneklerden birini veya her ikisini de adanmış konaklarınız ile kullanabilirsiniz: 
- Birden çok kullanılabilirlik alanı arasında yayılır. Bu durumda, kullanmak istediğiniz her bölgede bir konak grubunuz olması gerekir.
- Fiziksel raflarla eşlenen birden çok hata etki alanı arasında yayılma. 
 
Her iki durumda da, konak grubunuz için hata etki alanı sayısını sağlamanız gerekir. Grubunuzdaki hata etki alanlarına yayılması istemiyorsanız, hata etki alanı sayısı 1 ' i kullanın. 

Hem kullanılabilirlik bölgelerini hem de hata etki alanlarını kullanmaya karar verebilirsiniz. 

Bu örnekte, 1 kullanılabilirlik alanı ve 2 hata etki alanı kullanarak bir konak grubu oluşturacağız. 


1. Azure [portalını](https://portal.azure.com)açın. 
1. Sol üst köşedeki **kaynak oluştur** ' u seçin.
1. **Konak grubunu** arayın ve sonra sonuçlardan **konak grupları** ' nı seçin.
1. **Konak grupları** sayfasında **Oluştur**' u seçin.
1. Kullanmak istediğiniz aboneliği seçin ve yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' u seçin.
1. **Ad** olarak *Myayrılmış hostsrg* yazın ve ardından **Tamam**' ı seçin.
1. **Konak grubu adı** Için *myhostgroup* yazın.
1. **Konum** için **Doğu ABD**' yi seçin.
1. **Kullanılabilirlik bölgesi** için **1**' i seçin.
1. **Hata etki alanı sayısı** için **2**' yi seçin.
1. VM 'Leri ve ölçek kümesi örneklerini bu gruptaki kullanılabilir bir konağa otomatik olarak atamak için **otomatik yerleşimi** seçin.
1. **Gözden geçir + oluştur** ' u seçin ve doğrulama için bekleyin.
1. **Doğrulama başarılı** iletisini gördüğünüzde, konak grubunu oluşturmak için **Oluştur** ' u seçin.

Konak grubunun oluşturulması yalnızca birkaç dakika sürer.


## <a name="create-a-dedicated-host"></a>Adanmış konak oluşturma

Şimdi konak grubunda ayrılmış bir konak oluşturun. Konak için bir ada ek olarak, ana bilgisayar için SKU sağlamanız gerekir. Ana bilgisayar SKU 'SU, desteklenen VM serisini ve adanmış ana bilgisayarınız için donanım oluşturmayı yakalar.

Konak SKU 'Ları ve fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure ayrılmış ana bilgisayar fiyatlandırması](https://aka.ms/ADHPricing).

Konak grubunuz için bir hata etki alanı sayısı ayarlarsanız, ana bilgisayarınız için hata etki alanını belirtmeniz istenir.  

1. Sol üst köşedeki **kaynak oluştur** ' u seçin.
1. **Adanmış ana bilgisayar** için arama yapın ve sonra sonuçlardan **adanmış konaklar** ' ı seçin.
1. **Adanmış konaklar** sayfasında **Oluştur**' u seçin.
1. Kullanmak istediğiniz aboneliği seçin.
1. **Kaynak grubu** olarak, *Myayrılmış hostsrg* öğesini seçin.
1. **Örnek ayrıntıları**' nda **ad** için *myhost* yazın ve konum için *Doğu ABD* seçin.
1. **Donanım profili**' nde, **Boyut ailesi** için *Standart Es3 Family-Type 1* ' i seçin, **konak grubu** için *myhostgroup* ' u seçin ve ardından **hata etki alanı** için *1* ' i seçin. Diğer alanları için varsayılan değerleri bırakın.
1. İşiniz bittiğinde, **gözden geçir + oluştur** ' u seçin ve doğrulama için bekleyin.
1. **Doğrulama başarılı** iletisini gördüğünüzde, konağı oluşturmak için **Oluştur** ' u seçin.

## <a name="create-a-vm"></a>VM oluşturma

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesini seçin.
1. Azure Marketi kaynakları listesinin üzerindeki arama kutusunda, kullanmak istediğiniz görüntüyü arayıp seçin ve ardından **Oluştur**' u seçin.
1. **Temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, doğru aboneliğin seçildiğinden emin olun ve ardından **kaynak grubu** olarak, *myayrılmış hostsrg* ' yi seçin. 
1. **Örnek ayrıntıları** altında, **Sanal makine adı** için *myVM* yazın ve **Konum** için *Doğu ABD*'yi seçin.
1. **Kullanılabilirlik seçenekleri** ' nde **kullanılabilirlik alanı**' nı seçin, açılan listeden *1* ' i seçin.
1. Boyut için **boyutu Değiştir**' i seçin. Kullanılabilir boyutlar listesinde, **Standart E2s v3** gibi Esv3 serilerinden birini seçin. Tüm kullanılabilir boyutları görmek için filtreyi temizlemeniz gerekebilir.
1. Gerektiğinde **temel bilgiler** sekmesindeki alanların geri kalanını tamamlayın.
1. VM 'niz için kullanılacak Konağı belirtmek istiyorsanız sayfanın üst kısmında **Gelişmiş** sekmesini seçin ve **konak bölümünde konak** **grubu** için *Myhostgroup* ve **konak** için *myhost* ' u seçin. Aksi halde, sanal makinenizin kapasitesi olan bir konağa otomatik olarak yerleştirilmesi gerekir.
    ![Konak grubu ve konak seçin](./media/dedicated-hosts-portal/advanced.png)
1. Kalan varsayılan ayarları bırakın, ardından sayfanın alt kısmındaki **Gözden geçir + oluştur** düğmesini seçin.
1. Doğrulamanın geçtiğini belirten iletiyi gördüğünüzde **Oluştur**' u seçin.

VM'nizin dağıtılması birkaç dakika sürer.

## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma 

Bir ölçek kümesi dağıttığınızda konak grubunu belirtirsiniz.

1. *Ölçek kümesi* araması yapın ve **sanal makine ölçek kümelerini** listeden seçin.
1. Yeni bir ölçek kümesi oluşturmak için **Ekle** ' yi seçin.
1. Genellikle yaptığınız gibi **temel bilgiler** sekmesindeki alanları doldurun, ancak **Standart E2s v3** gibi adanmış ana bilgisayarınız IÇIN seçtiğiniz serideki bir VM boyutu seçtiğinizden emin olun.
1. **Gelişmiş** sekmesinde, **yayma algoritması** için **Maksimum yayma** seçeneğini belirleyin.
1. **Konak grubu**' nda, açılan listeden konak grubunu seçin. Son zamanlarda grubu oluşturduysanız, listeye eklenmesi bir dakika sürebilir.

## <a name="add-an-existing-vm"></a>Var olan bir VM 'yi ekleme 

Bir çıkış VM 'sini ayrılmış bir konağa ekleyebilirsiniz, ancak önce VM 'nin Stop\satıcılarla locatedolması gerekir. Bir VM 'yi adanmış bir konağa taşımadan önce, VM yapılandırmasının desteklendiğinden emin olun:

- VM boyutu, ayrılmış konakla aynı büyüklükte bir aile içinde olmalıdır. Örneğin, adanmış ana bilgisayarınız DSv3 ise sanal makine boyutu Standard_D4s_v3 olabilir, ancak bir Standard_A4_v2 olamaz. 
- VM 'nin adanmış konakla aynı bölgede bulunması gerekir.
- VM, bir yakınlık yerleşimi grubunun parçası olamaz. Ayrılmış bir konağa taşımadan önce VM 'yi yakınlık yerleşimi grubundan kaldırın. Daha fazla bilgi için bkz. [bir VM 'yi bir yakınlık yerleşimi grubundan taşıma](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM bir kullanılabilirlik kümesinde olamaz.
- VM bir kullanılabilirlik bölgeindeyse, konak grubuyla aynı Kullanılabilirlik bölgesi olması gerekir. VM ve konak grubu için kullanılabilirlik bölgesi ayarlarının eşleşmesi gerekir.

[Portalı](https://portal.azure.com)kullanarak VM 'yi adanmış bir konağa taşıyın.

1. VM için sayfayı açın.
1. VM 'yi serbest bırakmak için **Durdur** ' u seçin.
1. Sol menüden **yapılandırma** ' yı seçin.
1. Açılır menülerden bir konak grubu ve konak seçin.
1. İşiniz bittiğinde sayfanın en üstündeki **Kaydet** ' i seçin.
1. VM konağa eklendikten sonra, sol menüden **genel bakış** ' ı seçin.
1. Sanal makineyi yeniden başlatmak için sayfanın üst kısmındaki **Başlat** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için bkz. [adanmış ana bilgisayarlara](dedicated-hosts.md) genel bakış.

- [Burada](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), bir bölgedeki maksimum dayanıklılık için hem bölge hem de hata etki alanı kullanan örnek şablon vardır.

- Ayrıca, [Azure CLI](./linux/dedicated-hosts-cli.md) veya [PowerShell](./windows/dedicated-hosts-powershell.md)kullanarak adanmış bir konak da dağıtabilirsiniz.
