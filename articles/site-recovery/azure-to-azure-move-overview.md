---
title: Azure Site Recovery ile Azure VM 'lerini başka bir bölgeye taşıma
description: Azure VM 'lerini bir Azure bölgesinden diğerine taşımak için Azure Site Recovery kullanma.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 61d596c4b3a65c54e1a70682adad5b7328c384f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90007375"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Azure VM 'lerini başka bir Azure bölgesine taşıma

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak Azure VM 'lerini başka bir Azure bölgesine taşımaya yönelik nedenler ve adımlarla ilgili bir genel bakış sunulmaktadır. 


## <a name="reasons-to-move-azure-vms"></a>Azure VM 'lerini taşıma nedenleri

VM 'Leri aşağıdaki nedenlerle taşıyabilirsiniz:

- Zaten bir bölgede dağıttıysanız, uygulamanızın veya hizmetinizin son kullanıcılarına daha yakın olan yeni bir bölge desteği eklenmiştir. Bu senaryoda, gecikme süresini azaltmak için sanal makinelerinizi yeni bölgeye taşımak istersiniz. Abonelikleri birleştirmek istiyorsanız veya taşımanızı gerektiren idare veya kuruluş kuralları varsa aynı yaklaşımı kullanın.
- VM 'niz tek örnekli bir VM veya bir kullanılabilirlik kümesinin parçası olarak dağıtıldı. Kullanılabilirlik SLA 'larını artırmak istiyorsanız, sanal makinelerinizi bir kullanılabilirlik bölgesine taşıyabilirsiniz.

## <a name="move-vms-with-resource-mover"></a>VM 'Leri kaynak taşıyıcısı ile taşıma

Artık, [Azure Kaynak taşıyıcısı](../resource-mover/tutorial-move-region-virtual-machines.md)Ile VM 'leri başka bir bölgeye taşıyabilirsiniz. Kaynak taşıyıcısı genel önizlemede bulunur ve şunları sağlar:
- Kaynakları bölgeler arasında taşımak için tek bir hub.
- Daha az taşıma süresi ve karmaşıklığı. İhtiyacınız olan her şey tek bir konumda.
- Farklı türlerde Azure kaynaklarını taşımak için basit ve tutarlı bir deneyim.
- Taşımak istediğiniz kaynaklar arasında bağımlılıkları belirlemenin kolay bir yolu. Bu, ilgili kaynakları bir arada taşımanızı sağlar, böylece taşıma işleminden sonra her şeyin hedef bölgede beklendiği gibi çalışması gerekir.
- Taşıma işleminden sonra silmek istiyorsanız kaynak bölgedeki kaynakların otomatik olarak temizlenmesi.
- Edici. Bir taşımayı deneyebilir ve tam bir taşıma yapmak istemiyorsanız bu uygulamayı atabilirsiniz.



## <a name="move-vms-with-site-recovery"></a>VM 'Leri Site Recovery taşıyın

VM 'Leri Site Recovery ile taşımak aşağıdaki adımları içerir:

1. Önkoşulları doğrulama.
2. Kaynak VM 'Leri hazırlayın.
3. Hedef bölgeyi hazırlayın.
4. Verileri hedef bölgeye kopyalayın. Kaynak VM 'den hedef bölgeye veri kopyalamak için Azure Site Recovery çoğaltma teknolojisini kullanın.
5. Yapılandırmayı test edin. Çoğaltma tamamlandıktan sonra, üretim dışı bir ağa yük devretme testi gerçekleştirerek yapılandırmayı test edin.
6. Taşımayı gerçekleştirin.
7. Kaynak bölgedeki kaynakları atın.

> [!NOTE]
> Aşağıdaki bölümlerde bu adımlarla ilgili ayrıntılar verilmiştir.
> [!IMPORTANT]
> Şu anda Azure Site Recovery, VM 'Lerin bir bölgeden diğerine taşınmasını destekler ancak bölge içinde taşımayı desteklemez.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Çok katmanlı bir dağıtım için tipik mimariler

Bu bölümde, Azure 'daki çok katmanlı bir uygulama için en yaygın dağıtım mimarileri açıklanmaktadır. Örnek, genel IP içeren üç katmanlı bir uygulamadır. Her katmanın (Web, uygulama ve veritabanı) her biri iki sanal makineye sahiptir ve bir Azure yük dengeleyiciye diğer katmanlara bağlanır. Veritabanı katmanı, yüksek kullanılabilirlik için VM 'Ler arasında her zaman çoğaltma SQL Server sahiptir.

* **Farklı katmanlara dağıtılan tek örnekli VM 'ler**: bir KATMANDAKI her VM tek ÖRNEKLI bir VM olarak yapılandırılır ve yük dengeleyiciler tarafından diğer katmanlara bağlanır. Bu yapılandırma, benimsemek için en basit olanıdır.

     ![Tek örnekli VM dağıtımını katmanlar arasında taşımak için seçim](media/move-vm-overview/regular-deployment.png)

* **Her katmandaki VM 'ler kullanılabilirlik kümeleri arasında dağıtılır**: bir KATMANDAKI her VM bir kullanılabilirlik kümesinde yapılandırılır. [Kullanılabilirlik kümeleri](../virtual-machines/windows/tutorial-availability-sets.md) , Azure 'Da dağıttığınız VM 'lerin bir kümedeki birden fazla yalıtılmış donanım düğümüne dağıtılmış olmasını güvence altına alır. Böylece, Azure 'da bir donanım veya yazılım hatası oluşursa, sanal makinelerinizin yalnızca bir alt kümesinin etkilenmesi ve genel çözümünüzün kullanılabilir ve çalışır durumda kalması sağlanır.

     ![Kullanılabilirlik kümeleri arasında VM dağıtımı](media/move-vm-overview/avset.png)

* **Kullanılabilirlik alanları arasında dağıtılan her katmandaki VM 'ler**: bir KATMANDAKI her VM, [kullanılabilirlik alanları](../availability-zones/az-overview.md)arasında yapılandırılır. Bir Azure bölgesindeki kullanılabilirlik bölgesi bir hata etki alanının ve bir güncelleştirme etki alanının birleşimidir. Örneğin, bir Azure bölgesindeki üç bölgede üç veya daha fazla VM oluşturursanız, VM 'niz üç hata etki alanına ve üç güncelleştirme etki alanına etkili bir şekilde dağıtılır. Azure platformu, farklı bölgelerdeki VM 'Lerin aynı anda güncelleştirildiğinden emin olmak için bu dağıtımı güncelleştirme etki alanları genelinde tanır.

     ![Kullanılabilirlik alanı dağıtımı](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>VM 'Leri bir hedef bölgeye taşıyın

Daha önce bahsedilen [mimarilere](#typical-architectures-for-a-multi-tier-deployment) bağlı olarak, taşıma işlemini gerçekleştirdikten sonra dağıtım hedef bölgeye benzer şekilde şöyle görünür.

* **Çeşitli katmanlarda dağıtılan tek örnekli VM 'Ler**
* **Kullanılabilirlik kümeleri arasında dağıtılan her katmandaki VM 'Ler**
* **Kullanılabilirlik Alanları arasında dağıtılan her katmandaki VM 'Ler**

## <a name="move-vms-to-increase-availability"></a>Kullanılabilirliği artırmak için VM 'Leri taşıma

* **Çeşitli katmanlarda dağıtılan tek örnekli VM 'Ler**

     ![Katmanlar arasında tek örnekli VM dağıtımı](media/move-vm-overview/single-zone.png)

* **Her katmandaki VM 'ler kullanılabilirlik kümeleri arasında dağıtılır**: bir kullanılabilirlik kümesindeki VM 'lerinizi, Azure SITE Recovery kullanarak VM 'niz için çoğaltmayı etkinleştirdiğinizde ayrı kullanılabilirlik alanları olarak yapılandırabilirsiniz. Taşıma işlemi tamamlandıktan sonra kullanılabilirlik SLA 'Sı% 99,99 olur.

     ![Kullanılabilirlik kümeleri ve Kullanılabilirlik Alanları arasında VM dağıtımı](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> 
> * [Azure VM’lerini başka bir bölgeye taşıma](azure-to-azure-tutorial-migrate.md)
> 
> * [Azure VM 'lerini Kullanılabilirlik Alanları içine taşıyın](move-azure-vms-avset-azone.md)

