---
title: Yük devretme VM 'si için ağ yapılandırmasını özelleştirme | Microsoft Docs
description: Azure Site Recovery kullanarak Azure VM 'lerinin çoğaltmasında bir yük devretme sanal makinesi için ağ yapılandırmalarının özelleştirilmesine genel bir bakış sağlar.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 191161c8185f45712052000285013a6e61c9fa6a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968900"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Hedef Azure VM’nin ağ yapılandırmalarını özelleştirme

Bu makalede [Azure Site Recovery](site-recovery-overview.md), Azure VM 'lerini bir bölgeden diğerine çoğaltırken ve kurtarırken hedef Azure sanal MAKINESINDE (VM) ağ yapılandırmalarının özelleştirilmesi hakkında rehberlik sağlanır.

## <a name="before-you-start"></a>Başlamadan önce

Site Recovery [Bu senaryo](azure-to-azure-architecture.md)için olağanüstü durum kurtarma nasıl sağladığını öğrenin.

## <a name="supported-networking-resources"></a>Desteklenen ağ kaynakları

Azure VM 'Leri çoğaltılırken yük devretme VM 'si için aşağıdaki temel kaynak yapılandırmasını sağlayabilirsiniz:

- [İç yük dengeleyici](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Genel IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- Hem alt ağ hem de NIC için [ağ güvenlik grubu](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

## <a name="prerequisites"></a>Önkoşullar

- Kurtarma tarafı yapılandırmalarının önceden planlantığınızdan emin olun.
- Ağ kaynaklarını önceden oluşturun. Azure Site Recovery hizmetin bu ayarları kabul edebilmesi ve yük devretme VM 'sinin bu ayarlara uyduğundan emin olması için bunu bir giriş olarak sağlayın.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Yük devretme ve test yük devretme ağ yapılandırmasını özelleştirme

1. **Çoğaltılan öğelere**gidin. 
2. İstediğiniz Azure VM 'yi seçin.
3. **İşlem ve ağ** ' ı seçin ve **Düzenle**' yi seçin. NIC yapılandırma ayarlarının kaynaktaki ilgili kaynakları içerdiğine dikkat edin. 

     ![Yük devretme ağ yapılandırmasını özelleştirme](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Yük devretme testi sanal ağını seçin. Boş bırakmayı seçebilir ve test yük devretmesi sırasında bir tane seçebilirsiniz.
5. Yük devretme ağı, yapılandırmak istediğiniz NIC 'in yakınında **Düzenle** ' yi seçin. Açılan sonraki dikey pencerede, yük devretme testi ve yük devretme konumunda ilgili önceden oluşturulmuş kaynakları seçin.

    ![NIC yapılandırmasını düzenleme](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. **Tamam**’ı seçin.

Site Recovery artık bu ayarları kabul eder ve yük devretmede VM 'nin ilgili NIC aracılığıyla seçili kaynağa bağlı olduğundan emin olur.

Kurtarma planı aracılığıyla yük devretme testini tetikleytiğinizden, her zaman Azure sanal ağını soracaktır. Bu sanal ağ, önceden yapılandırılmış test yük devretme ayarlarına sahip olmayan makineler için test yük devretmesi için kullanılacaktır.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="unable-to-view-or-select-a-resource"></a>Kaynak görüntülenemiyor veya kullanılamıyor

Bir ağ kaynağını seçip görüntüleyemez, aşağıdaki denetim ve koşullara geçin:

- Ağ kaynağının hedef alanı, yalnızca kaynak VM 'nin karşılık gelen bir girişi varsa etkindir. Bu bir olağanüstü durum kurtarma senaryosunda, kaynağınızın tam veya ölçeği genişletilmiş bir sürümünü isteyeceksiniz.
- Her ağ kaynağı için, yük devretme sanal makinesinin kendisini seçilen kaynağa iliştirediğinden ve yük devretme güvenilirliği korunduğundan emin olmak için, açılan listede bazı filtreler uygulanır. Bu filtreler, kaynak VM 'yi yapılandırdığınız sırada doğrulanacak olan ağ koşullarını temel alır.

İç yük dengeleyici doğrulamaları:

- Yük dengeleyici ve hedef VM 'nin aboneliği ve bölgesi aynı olmalıdır.
- İç yük dengeleyici ile ilişkili sanal ağ ve hedef VM 'nin aynı olması gerekir.
- Hedef VM 'nin genel IP SKU 'SU ve iç yük dengeleyicinin SKU 'SU aynı olmalıdır.
- Hedef VM bir kullanılabilirlik alanına yerleştirilecek şekilde yapılandırıldıysa, yük dengeleyicinin bölge için erişilebilir olup olmadığını veya herhangi bir kullanılabilirlik bölgesinin bir parçasını kontrol edin. (Temel SKU yük dengeleyiciler bölgeleri desteklemez ve bu durumda açılan listede gösterilmez.)
- İç yük dengeleyicinin önceden oluşturulmuş bir arka uç havuzuna ve ön uç yapılandırmasına sahip olduğundan emin olun.

Genel IP adresi:

- Genel IP ve hedef VM 'nin aboneliği ve bölgesi aynı olmalıdır.
- Hedef VM 'nin genel IP SKU 'SU ve iç yük dengeleyicinin SKU 'SU aynı olmalıdır.

Ağ güvenlik grubu:
- Ağ güvenlik grubunun ve hedef sanal makinenin aboneliği ve bölgesi aynı olmalıdır.


> [!WARNING]
> Hedef VM bir kullanılabilirlik kümesiyle ilişkiliyse, aynı SKU 'nun genel IP ve iç yük dengeleyiciyi, kullanılabilirlik kümesindeki diğer VM 'nin genel IP 'si ile iç yük dengeleyicisiyle ilişkilendirmeniz gerekir. Aksi takdirde, yük devretme başarısız olmayabilir.
