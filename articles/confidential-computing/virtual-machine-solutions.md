---
title: Sanal makinelerde Azure gizli bilgi işlem çözümleri
description: Sanal makinelerde Azure gizli bilgi işlem çözümleri hakkında bilgi edinin.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 580c53f311bc8ee70e974df2bc4111e6361d06f6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818971"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure sanal makinelerinde çözümler

Bu makalede, [Intel Software Guard uzantısı](https://software.intel.com/sgx) (Intel SGX) tarafından desteklenen Intel Işlemcileri çalıştıran Azure gizli bilgi işlem sanal makinelerini (VM 'ler) dağıtma hakkında bilgiler yer almaktadır. 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure gizli bilgi işlem VM boyutları

Azure gizli bilgi işlem sanal makineleri, bulutta işlendiği sırada verilerinizin ve kodunuzun gizliliğini ve bütünlüğünü korumak için tasarlanmıştır 

[DCsv2 serisi](../virtual-machines/dcv2-series.md) VM 'Ler en son ve en son gizli bilgi işlem boyutu ailesidir. Bu VM 'Ler daha büyük bir dağıtım özelliği yelpazesi destekler, 2x ve DC-Series sanal makinelerimize kıyasla daha büyük bir boyut seçimi vardır. [DC Serisi](../virtual-machines/sizes-previous-gen.md#preview-dc-series) VM 'ler Şu anda önizleme aşamasındadır ve genel kullanıma sunulmayacak ve kullanım dışı bırakılacak.

[Hızlı başlangıç öğreticisini](quick-create-marketplace.md)izleyerek Microsoft ticari Marketi aracılığıyla BIR DCsv2-Series VM dağıtmaya başlayın.

### <a name="current-available-sizes-and-regions"></a>Kullanılabilir geçerli Boyutlar ve bölgeler

Kullanılabilir bölgeler ve kullanılabilirlik bölgelerinde genel olarak kullanılabilen tüm gizli işlem VM boyutlarının bir listesini almak için [Azure CLI](/cli/azure/install-azure-cli-windows)'de aşağıdaki komutu çalıştırın:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Yukarıdaki boyutların daha ayrıntılı bir görünümü için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Adanmış ana bilgisayar gereksinimleri
DCSv2-Series VM ailesinden **Standard_DC8_v2** sanal makine boyutunu dağıtmak, tam ana bilgisayarı kaplayacaktır ve diğer kiracılar veya aboneliklerle paylaşılmaz. Bu VM SKU 'SU ailesi, genellikle adanmış bir ana bilgisayar hizmetine sahip olunarak karşılanacak uyumluluk ve güvenlik mevzuatı gereksinimlerini karşılamak için ihtiyacınız olabilecek yalıtımı sağlar. **Standard_DC8_v2** SKU seçtiğinizde, fiziksel ana bilgisayar sunucusu yalnızca sanal makinenize EPC belleği dahil tüm kullanılabilir donanım kaynaklarını ayırır. Lütfen bu işlevin altyapı tasarımı tarafından mevcut olduğunu ve **Standard_DC8_v2** tüm özelliklerinin desteklendiğini unutmayın. Bu dağıtım, diğer Azure VM aileleri tarafından sunulan [Azure ayrılmış ana bilgisayar](../virtual-machines/dedicated-hosts.md) hizmeti ile aynı değildir.


## <a name="deployment-considerations"></a>Dağıtma konuları

DCsv2-Series bir sanal makineyi 10 dakikadan kısa bir süre içinde dağıtmak için hızlı başlangıç öğreticisini izleyin. 

- **Azure aboneliği** – bir gizli BILGI işlem VM örneği dağıtmak için bir Kullandıkça Öde aboneliğine veya diğer satın alma seçeneğine göz önünde bulundurun. [Ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/)kullanıyorsanız, uygun Azure işlem çekirdeği miktarı için kotayı kullanamazsınız.

- **Fiyatlandırma ve bölgesel kullanılabilirlik** - [sanal makine fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)DCsv2-Series VM 'leri için fiyatlandırmayı bulun. Azure bölgelerinde kullanılabilirlik için [bölgeye göre kullanılabilir ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) denetleyin.


- **Çekirdek kotası** – Azure aboneliğinizdeki çekirdek kotasını varsayılan değerden artırmanız gerekebilir. Aboneliğiniz Ayrıca, DCsv2-Series dahil olmak üzere belirli VM boyutu ailelerinde dağıtabileceğiniz çekirdek sayısını da sınırlayabilir. Kota artışı istemek için, ücretsiz [bir çevrimiçi müşteri destek isteği açın](../azure-portal/supportability/per-vm-quota-requests.md) . Varsayılan sınırlar, abonelik kategorime bağlı olarak değişebilir.

  > [!NOTE]
  > Büyük ölçekli kapasite gereksinimleriniz varsa Azure desteği 'ne başvurun. Azure kotaları, kapasite garantisi değil kredi limitlerdir. Kotasından bağımsız olarak yalnızca kullandığınız çekirdekler için ücretlendirilirsiniz.
  
- **Yeniden boyutlandırma** – özel donanımlar nedeniyle, yalnızca aynı büyüklükte aile içindeki gizli bilgi işlem örneklerini yeniden boyutlandırabilirsiniz. Örneğin, bir DCsv2 serisi VM 'yi yalnızca bir DCsv2 serisi boyutundan diğerine yeniden boyutlandırabilirsiniz. Gizli olmayan bir bilgi işlem boyutunun gizli bilgi işlem boyutuna göre yeniden boyutlandırılması desteklenmez.  

- **Görüntü** : gizli bilgi işlem örnekleri üzerinde Intel Software Guard uzantısı (Intel SGX) desteği sağlamak için, tüm dağıtımların 2. nesil görüntülerde çalıştırılması gerekir. Azure gizli bilgi işlem, Ubuntu 18,04 Gen 2, Ubuntu 20,04 Gen 2, Windows Server 2019 Gen2 ve Windows Server 2016 Gen 2 ' de çalışan iş yüklerini destekler. Desteklenen ve desteklenmeyen senaryolar hakkında daha fazla bilgi edinmek için [Azure 'da 2. nesil VM 'ler için destek](../virtual-machines/generation-2.md) konusunu okuyun. 

- **Depolama** – Azure gizli bilgi işlem sanal makine veri diskleri ve kısa ömürlü işletim sistemi disklerimiz NVMe disklerinde. Örnekler, Ultra SSD veya Standart HDD değil yalnızca Premium SSD ve Standart SSD disklerini destekler. Sanal makine boyutu **DC8_v2** Premium depolamayı desteklemez. 

- **Disk şifrelemesi** – gizli işlem örnekleri şu anda disk şifrelemeyi desteklemez. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Yüksek kullanılabilirlik ve olağanüstü durum kurtarma konuları

Azure 'da sanal makineler kullanırken, kapalı kalma süresini önlemek için yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümü uygulamaktan siz sorumlusunuz. 

Azure gizli bilgi işlem, şu anda Kullanılabilirlik Alanları aracılığıyla bölge yedekliliği desteklemez. Gizli bilgi işlem için en yüksek kullanılabilirlik ve yedeklilik için [kullanılabilirlik kümelerini](../virtual-machines/availability-set-overview.md)kullanın. Donanım kısıtlamaları nedeniyle, gizli bilgi işlem örneklerinin kullanılabilirlik kümelerinin yalnızca en fazla 10 güncelleştirme etki alanı olabilir. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) şablonuyla dağıtım

Azure Resource Manager, Azure için dağıtım ve yönetim hizmetidir. Azure aboneliğinizde kaynak oluşturmanıza, güncelleştirmenize ve silmenizi sağlayan bir yönetim katmanı sağlar. Dağıtımdan sonra kaynaklarınızı güvenli hale getirmek ve düzenlemek için erişim denetimi, kilitler ve Etiketler gibi yönetim özelliklerini kullanabilirsiniz.

ARM şablonları hakkında bilgi edinmek için bkz. [şablon dağıtımı genel bakış](../azure-resource-manager/templates/overview.md).

ARM şablonunda bir DCsv2-Series VM dağıtmak için [sanal makine kaynağını](../virtual-machines/windows/template-description.md)kullanacaksınız. **VMSize** ve **ImageReference** için doğru özellikleri belirttiğinizden emin olun.

### <a name="vm-size"></a>VM boyutu

Sanal makine kaynağında ARM şablonunuzda aşağıdaki boyutlardan birini belirtin. Bu dize, **özelliklerde** **VMSize** olarak konur.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS görüntüsü

**Özellikler** altında, **storageprofile** altındaki bir görüntüye de başvurmanız gerekir. **ImageReference** için aşağıdaki görüntülerden *yalnızca birini* kullanın.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Sonraki adımlar 

Bu makalede, gizli bilgi işlem sanal makinesi oluştururken gereken niteliklerin ve yapılandırmaların öğrenildiği hakkında bilgi edindiniz. Artık DCsv2-Series bir sanal makine dağıtmak için Microsoft Azure Market gidebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Marketi 'nde DCsv2-Series sanal makine dağıtma](quick-create-marketplace.md)