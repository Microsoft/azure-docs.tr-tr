---
title: Azure HPC önbelleği oluşturma
description: Azure HPC önbellek örneği oluşturma
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 07aba1b1536635e414fc5fab4ece148683909188
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168598"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC önbelleği oluşturma

Önbelleğinizi oluşturmak için Azure portal kullanın.

![En altta oluştur düğmesi ile Azure portal önbellekte genel bakış ekran görüntüsü](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Temel ayrıntıları tanımla

![Azure portal içindeki proje ayrıntıları sayfasının ekran görüntüsü](media/hpc-cache-create-basics.png)

**Proje ayrıntıları**' nda, önbelleği barındıracak aboneliği ve kaynak grubunu seçin. Aboneliğin [erişim](hpc-cache-prereqs.md#azure-subscription) listesinde olduğundan emin olun.

**Hizmet Ayrıntıları**' nda, önbellek adını ve bu diğer öznitelikleri ayarlayın:

* Konum- [desteklenen bölgelerden](hpc-cache-overview.md#region-availability)birini seçin.
* Sanal ağ-mevcut bir tane seçebilirsiniz veya yeni bir sanal ağ oluşturabilirsiniz.
* Alt ağ-yalnızca bu Azure HPC önbellek örneği için kullanılacak en az 64 IP adresi (/24) olan bir alt ağ seçin veya oluşturun.

## <a name="set-cache-capacity"></a>Önbellek kapasitesini ayarla
<!-- referenced from GUI - update aka.ms link if you change this header text -->

**Önbellek** sayfasında önbelleğinizin kapasitesini ayarlamanız gerekir. Burada ayarlanan değerler, önbelleğinizin ne kadar veri tutabildiğini ve ne kadar hızlı istemci isteklerine hizmet kullanabileceğini belirlemektir.

Genel Önizleme süresinden sonra kapasite, önbelleğin maliyetini de etkiler.

Şu iki değeri belirleyerek kapasiteyi seçin:

* Önbellek (aktarım hızı) için GB/saniye cinsinden maksimum veri aktarımı oranı
* Önbelleğe alınmış veriler için ayrılan depolama miktarı (TB)

Kullanılabilir aktarım hızı değerlerinden birini ve önbellek depolama boyutunu seçin.

Gerçek veri aktarımı hızının iş yüküne, ağ hızına ve depolama hedeflerinin türüne bağlı olduğunu unutmayın. Seçtiğiniz değerler tüm önbellek sistemi için en fazla aktarım hızını ayarlar, ancak bazıları ek yük görevleri için kullanılır. Örneğin, bir istemci önbellekte zaten depolanmayan bir dosya isterse veya dosya eski olarak işaretlenmişse önbelleğiniz, arka uç depolamadan getirmek için aktarım hızını kullanır.

Azure HPC Cache, önbellek isabet oranlarını en üst düzeye çıkarmak için hangi dosyaların önbelleğe alınacağını ve ön yüklenmiş olduğunu yönetir Önbellek içeriği sürekli değerlendirilir ve dosyalar daha az sıklıkla erişildiği zaman uzun süreli depolamaya taşınır. Etkin çalışma dosyaları kümesini, meta veriler ve diğer ek yük için ek alanla rahatça tutabilecek bir önbellek depolama boyutu seçin.

![önbellek boyutlandırma sayfasının ekran görüntüsü](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Kaynak etiketleri ekleme (isteğe bağlı)

**Etiketler** sayfası, Azure HPC Cache örneğinizi [kaynak etiketleri](https://go.microsoft.com/fwlink/?linkid=873112) eklemenize olanak tanır.

## <a name="finish-creating-the-cache"></a>Önbellek oluşturmayı tamamlama

Yeni önbelleği yapılandırdıktan sonra, **gözden geçir + oluştur** sekmesine tıklayın. Portal seçimlerinizi doğrular ve seçimlerinizi incelemenizi sağlar. Her şey doğruysa, **Oluştur**' a tıklayın.

Önbellek oluşturma yaklaşık 10 dakika sürer. İlerlemeyi Azure portal Bildirimler panelinde izleyebilirsiniz.

!["dağıtım çalışma" ve "Bildirimler" sayfalarındaki önbellek oluşturma ekranının ekran görüntüsü](media/hpc-cache-deploy-status.png)

Oluşturma tamamlandığında, yeni Azure HPC önbellek örneğinin bağlantısını içeren bir bildirim görünür ve önbellek, aboneliğinizin **kaynaklar** listesinde görünür.
<!-- double check on notification -->

![Azure portal 'de Azure HPC önbellek örneğinin ekran görüntüsü](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Sonraki adımlar

Önbelleğiniz **kaynaklar** listesinde görüntülendikten sonra, veri kaynaklarınıza önbelleğe erişim sağlamak için depolama hedeflerini tanımlayın.

* [Depolama hedefleri ekleme](hpc-cache-add-storage.md)
