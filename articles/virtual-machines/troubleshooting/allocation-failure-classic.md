---
title: Klasik dağıtım modelinde Azure VM ayırma hatalarında sorun giderme | Microsoft Docs
description: Azure 'da klasik VM oluşturma, yeniden başlatma veya yeniden boyutlandırma sırasında ayırma hatalarıyla ilgili sorunları giderme
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d43176e04337c2faf7be0bea682428056bc4ab46
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059190"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Klasik dağıtım modelindeki ayırma hatası senaryolarına özel sorun giderme adımları

Aşağıda, bir ayırma isteğinin sabitlenememesine neden olan yaygın ayırma senaryoları verilmiştir. Bu makalenin ilerleyen kısımlarında her senaryoya değineceğiz.

- Bir VM 'yi yeniden boyutlandırma veya var olan bir bulut hizmetine VM veya rol örnekleri ekleme
- Kısmen durdurulmuş (serbest bırakılmış) VM'leri yeniden başlatma
- Tamamen durdurulmuş (serbest bırakılmış) VM'leri yeniden başlatma
- Hazırlama ve üretim dağıtımları (yalnızca hizmet olarak platform)
- Benzeşim grubu (VM veya hizmet yakınlığı)
- Benzeşim – grup tabanlı sanal ağ

Bir ayırma hatası aldığınızda, listelenen senaryolardan herhangi birinin hata için uygulanıp uygulanmadığını denetleyin. Karşılık gelen senaryoyu belirlemek için Azure platformu tarafından döndürülen ayırma hatasını kullanın. İsteğiniz sabitlenmişse, isteğinizi daha fazla kümeye açmak için sabitleme kısıtlamalarından bazılarını kaldırın ve bu sayede ayırma başarısını artırabilirsiniz.
Genel olarak, hata "istenen VM boyutu desteklenmiyor" olarak ayarlanırsa, daha sonra her zaman yeniden deneyebilirsiniz. Bunun nedeni, isteğinize uyum sağlamak için kümede yeterli kaynak bırakılmış olabilir. Sorun istenen VM boyutunun desteklenmiyorsa, farklı bir VM boyutu deneyin. Aksi halde, tek seçenek sabitleme kısıtlamasını kaldırdır.

İki yaygın hata senaryosu benzeşim gruplarıyla ilgilidir. Geçmişte, VM 'Ler ve hizmet örneklerine yakın bir yakınlık sağlamak için bir benzeşim grubu kullanıldı veya bir sanal ağın oluşturulmasını etkinleştirmek için kullanılmış. Bölgesel sanal ağların tanıtılmasıyla birlikte, benzeşim grupları artık sanal ağ oluşturmak için gerekli değildir. Azure altyapısında ağ gecikme süresi azaltılmasıyla, VM 'Ler veya hizmet yakınlığı için benzeşim grupları kullanma önerisi değişmiştir.

Aşağıdaki diyagramda (sabitlenmiş) ayırma senaryolarına ait taksonomi gösterilmektedir. 

![Sabitlenmiş ayırma sınıflandırması](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Bir VM 'yi yeniden boyutlandırma veya var olan bir bulut hizmetine VM veya rol örnekleri ekleme
**Hata:**

Upgrade_VMSizeNotSupported veya GeneralError

**Küme sabitleme nedeni**

Bir VM 'yi yeniden boyutlandırma veya var olan bir bulut hizmetine bir VM veya rol örneği ekleme isteği, var olan bulut hizmetini barındıran orijinal kümede denenmelidir. Yeni bir bulut hizmeti oluşturmak, Azure platformunun ücretsiz kaynakları olan veya istediğiniz VM boyutunu destekleyen başka bir küme bulmasına olanak tanır.

**Geçici çözüm**

Hata Upgrade_VMSizeNotSupported * ise farklı bir VM boyutu deneyin. Farklı bir VM boyutu kullanmak bir seçenek değilse, ancak farklı bir sanal IP adresi (VIP) kullanmayı kabul ediyorsanız, yeni VM 'yi barındırmak için yeni bir bulut hizmeti oluşturun ve yeni bulut hizmetini mevcut VM 'Lerin çalıştığı bölgesel sanal ağa ekleyin. Mevcut bulut hizmetiniz bölgesel bir sanal ağ kullanmıyorsa, yeni bulut hizmeti için yeni bir sanal ağ oluşturmaya devam edebilir ve ardından [var olan sanal ağınızı yeni sanal ağa](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)bağlayabilirsiniz. [Bölgesel sanal ağlar](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)hakkında daha fazla bilgi için bkz.

Hata GeneralError * ise, kaynağın türü (örneğin, belirli bir VM boyutu) küme tarafından desteklenir, ancak kümede boş kaynaklar yok. Yukarıdaki senaryoya benzer şekilde, yeni bir bulut hizmeti oluşturarak istenen işlem kaynağını ekleyin (yeni bulut hizmetinin farklı bir VIP kullanması gerektiğini unutmayın) ve bulut hizmetlerinizi bağlamak için bölgesel bir sanal ağ kullanın.

## <a name="restart-partially-stopped-deallocated-vms"></a>Kısmen durdurulmuş (serbest bırakılmış) VM'leri yeniden başlatma
**Hata:**

GeneralError *

**Küme sabitleme nedeni**

Kısmi olarak ayırmayı kaldırma, bir bulut hizmetindeki bir veya daha fazla VM 'yi değil, bir veya daha fazla sanal makineyi durdurduysanız (serbest bırakılır) Bir VM 'yi durdurduğunuzda (serbest bırakma), ilişkili kaynaklar serbest bırakılır. Bu durdurulan (serbest bırakıldı) VM 'nin yeniden başlatılması bu nedenle yeni bir ayırma isteğidir. VM 'lerin kısmen serbest bırakılmış bir bulut hizmetinde yeniden başlatılması, mevcut bir bulut hizmetine VM ekleme ile eşdeğerdir. Ayırma isteğinin, var olan bulut hizmetini barındıran orijinal kümede denenmelidir. Farklı bir bulut hizmeti oluşturmak, Azure platformunun boş kaynak içeren başka bir kümeyi bulmasını veya istediğiniz VM boyutunu desteklediğini sağlar.

**Geçici çözüm**

Farklı bir VIP kullanmayı kabul ediyorsanız, durdurulmuş (serbest bırakılmış) VM 'Leri silin (ancak ilişkili diskleri tutun) ve VM 'Leri farklı bir bulut hizmeti aracılığıyla geri ekleyin. Bulut hizmetlerinizi bağlamak için bölgesel bir sanal ağ kullanın:

* Mevcut bulut hizmetiniz bölgesel bir sanal ağ kullanıyorsa, yeni bulut hizmetini aynı sanal ağa eklemeniz yeterlidir.
* Mevcut bulut hizmetiniz bölgesel bir sanal ağ kullanmıyorsa, yeni bulut hizmeti için yeni bir sanal ağ oluşturun ve ardından [var olan sanal ağınızı yeni sanal ağa bağlayın](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). [Bölgesel sanal ağlar](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)hakkında daha fazla bilgi için bkz.

## <a name="restart-fully-stopped-deallocated-vms"></a>Tamamen durdurulmuş (serbest bırakılmış) VM'leri yeniden başlatma
**Hata:**

GeneralError *

**Küme sabitleme nedeni**

Tam kaldırma, bir bulut hizmetindeki tüm sanal makineleri durdurduğunuzun (serbest bırakıldığı) anlamına gelir. Bu VM 'Leri yeniden başlatmaya yönelik ayırma isteklerinin, bulut hizmetini barındıran orijinal kümede denenmelidir. Yeni bir bulut hizmeti oluşturmak, Azure platformunun ücretsiz kaynakları olan veya istediğiniz VM boyutunu destekleyen başka bir küme bulmasına olanak tanır.

**Geçici çözüm**

Farklı bir VIP kullanmayı kabul ediyorsanız, özgün durdurulmuş (serbest bırakılmış) VM 'Leri silin (ancak ilişkili diskleri tutun) ve ilgili bulut hizmetini silin (VM 'Leri durdurduysanız (serbest bırakıldığında), ilişkili işlem kaynakları zaten serbest bırakıldı. VM 'Leri yeniden eklemek için yeni bir bulut hizmeti oluşturun.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Hazırlama/üretim dağıtımları (yalnızca hizmet olarak platform)
**Hata:**

New_General * veya New_VMSizeNotSupported *

**Küme sabitleme nedeni**

Bir bulut hizmetinin hazırlama dağıtımı ve üretim dağıtımı aynı kümede barındırılır. İkinci dağıtımı eklediğinizde, karşılık gelen ayırma isteği, ilk dağıtımı barındıran kümede denenir.

**Geçici çözüm**

İlk dağıtımı ve özgün bulut hizmetini silin ve bulut hizmetini yeniden dağıtın. Bu eylem, her iki dağıtımda veya istediğiniz VM boyutlarını destekleyen bir kümede yeterli boş kaynağa sahip olan bir kümedeki ilk dağıtımı gösterebilir.

## <a name="affinity-group-vmservice-proximity"></a>Benzeşim grubu (VM/hizmet yakınlığı)
**Hata:**

New_General * veya New_VMSizeNotSupported *

**Küme sabitleme nedeni**

Benzeşim grubuna atanan tüm işlem kaynakları tek bir kümeye bağlıdır. Bu benzeşim grubundaki yeni işlem kaynağı istekleri, mevcut kaynakların barındırıldığı kümede denenir. Bu, yeni kaynakların yeni bir bulut hizmeti veya var olan bir bulut hizmeti aracılığıyla oluşturulup oluşturulmayacağı için geçerlidir.

**Geçici çözüm**

Benzeşim grubu gerekli değilse, benzeşim grubu kullanmayın veya işlem kaynaklarınızı birden çok benzeşim grubuna gruplandırın.

## <a name="affinity-group-based-virtual-network"></a>Benzeşim grubu tabanlı sanal ağ
**Hata:**

New_General * veya New_VMSizeNotSupported *

**Küme sabitleme nedeni**

Bölgesel sanal ağların tanıtılmasından önce, bir sanal ağı bir benzeşim grubuyla ilişkilendirmeniz gerekiyordu. Sonuç olarak, bir benzeşim grubuna yerleştirilmiş işlem kaynakları, "ayırma senaryosunda açıklanan kısıtlamalar ile ilişkilidir: Benzeşim grubu (VM/hizmet yakınlığı) "bölümü. İşlem kaynakları bir kümeye bağlıdır.

**Geçici çözüm**

Benzeşim grubuna ihtiyacınız yoksa, eklemekte olduğunuz yeni kaynaklar için yeni bir bölgesel sanal ağ oluşturun ve ardından [mevcut sanal ağınızı yeni sanal ağa bağlayın](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). [Bölgesel sanal ağlar](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)hakkında daha fazla bilgi için bkz.

Alternatif olarak, [benzeşim grubu tabanlı sanal ağınızı bölgesel bir sanal ağa geçirebilir](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)ve sonra istediğiniz kaynakları yeniden ekleyebilirsiniz.


