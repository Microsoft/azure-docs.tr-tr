---
title: Azure Site Recovery iki bölge arasında sanal ağları eşleme
description: Azure Site Recovery ile Azure VM olağanüstü durum kurtarma için iki Azure bölgesi arasında sanal ağları eşleme hakkında bilgi edinin.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mayg
ms.openlocfilehash: b8f0512f978f25ca196ad6e9a7a03243c47f0662
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954148"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>VNET 'ler için Ağ eşlemesini ve IP adresini ayarlama

Bu makalede, farklı Azure bölgelerinde bulunan iki Azure sanal ağı örneğinin (VNet 'ler) nasıl eşleneceğini ve ağlar arasında IP adreslemenin nasıl ayarlanacağı açıklanır. Ağ eşleme, çoğaltmayı etkinleştirme sırasında kaynak ağa göre hedef ağ seçimi için varsayılan bir davranış sağlar.

## <a name="prerequisites"></a>Önkoşullar

Ağları eşleştirmadan önce kaynak ve hedef Azure bölgelerinde [Azure sanal](../virtual-network/virtual-networks-overview.md) ağlarına sahip olmanız gerekir. 

## <a name="set-up-network-mapping-manually-optional"></a>Ağ eşlemesini el ile ayarlama (Isteğe bağlı)

Ağları aşağıdaki gibi eşleyin:

1. **Site Recovery altyapısında** **+ ağ eşlemesi**' ne tıklayın.

    ![ Ağ eşlemesi oluşturma](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. **Ağ eşlemesi Ekle**' de, kaynak ve hedef konumları seçin. Örneğimizde, kaynak VM Doğu Asya bölgesinde çalışıyor ve Güneydoğu Asya bölgesine çoğaltılır.

    ![Kaynak ve hedef seçin](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Artık ters yönde bir ağ eşlemesi oluşturun. Bizim örneğimizde, kaynak artık Güneydoğu Asya olacak ve hedef Doğu Asya olacaktır.

    ![Ağ eşleme bölmesi Ekle-hedef ağ için kaynak ve hedef konumları seçin](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Çoğaltmayı etkinleştirdiğinizde ağları eşleme

Azure VM 'Leri için olağanüstü durum kurtarmayı yapılandırmadan önce ağ eşlemesi hazırladıysanız, [çoğaltmayı ayarlarken ve etkinleştirdiğinizde](azure-to-azure-how-to-enable-replication.md)bir hedef ağ belirtebilirsiniz. Bunu yaptığınızda şunlar olur:

- Seçtiğiniz hedefe göre, Site Recovery otomatik olarak kaynaktan hedef bölgeye ve hedeften kaynak bölgeye ağ eşlemeleri oluşturur.
- Varsayılan olarak, Site Recovery kaynak ağla aynı olan hedef bölgede bir ağ oluşturur. Site Recovery, kaynak ağın adına son ek olarak **-ASR** ekler. Hedef ağı özelleştirebilirsiniz.
- Bir kaynak ağ için ağ eşleme zaten gerçekleştiyse, eşlenen hedef ağ, daha fazla VM için çoğaltmaları etkinleştirme sırasında her zaman varsayılan değer olacaktır. Açılan listeden diğer kullanılabilir seçenekleri belirleyerek hedef sanal ağı değiştirmeyi seçebilirsiniz. 
- Yeni çoğaltmalar için varsayılan hedef sanal ağı değiştirmek için, mevcut ağ eşlemesini değiştirmeniz gerekir.
- A bölgesinden B bölgesine bir ağ eşlemesini değiştirmek istiyorsanız, ilk olarak B bölgesinden A bölgesine Ağ eşlemesini sildikten emin olun. Ters eşlemeyi silme işleminden sonra A bölgesinden B bölgesine Ağ eşlemesini değiştirin ve ardından ilgili ters eşlemeyi oluşturun.

>[!NOTE]
>* Ağ eşlemesini değiştirme yalnızca yeni VM çoğaltmaları için varsayılanları değiştirir. Var olan çoğaltmalar için hedef sanal ağ seçimlerini etkilemez. 
>* Var olan bir çoğaltma için hedef ağı değiştirmek istiyorsanız, çoğaltılan öğenin Işlem ve ağ ayarlarına gidin.

## <a name="specify-a-subnet"></a>Bir alt ağ belirtin

Hedef sanal makinenin alt ağı, kaynak VM 'nin alt ağının adına göre seçilir.

- Hedef ağda kaynak VM alt ağıyla aynı ada sahip bir alt ağ varsa, bu alt ağ hedef VM için ayarlanır.
- Hedef ağda aynı ada sahip bir alt ağ yoksa, alfabetik sırada ilk alt ağ hedef alt ağ olarak ayarlanır.
- Hedef alt ağı, VM için **işlem ve ağ** ayarları ' nda değiştirebilirsiniz.

    ![İşlem ve ağ işlem özellikleri penceresi](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Hedef VM 'Ler için IP adresleme ayarlama

Hedef sanal makinedeki her NIC için IP adresi şu şekilde yapılandırılır:

- **DHCP**: kaynak VM 'nin NIC 'si DHCP kullanıyorsa, hedef sanal makinenin NIC 'ı de DHCP kullan olarak ayarlanır.
- **STATIK IP adresi**: kaynak VM 'nin NIC 'SI statik IP adresleme kullanıyorsa, hedef VM NIC 'i STATIK bir IP adresi de kullanacaktır.


## <a name="ip-address-assignment-during-failover"></a>Yük devretme sırasında IP adresi atama

**Kaynak ve hedef alt ağlar** | **Ayrıntılar**
--- | ---
Aynı adres alanı | Kaynak VM NIC 'sinin IP adresi, hedef VM NIC IP adresi olarak ayarlanır.<br/><br/> Adres yoksa, bir sonraki kullanılabilir IP adresi hedef olarak ayarlanır.
Farklı adres alanı | Hedef alt ağdaki bir sonraki kullanılabilir IP adresi hedef VM NIC adresi olarak ayarlanır.



## <a name="ip-address-assignment-during-test-failover"></a>Yük devretme testi sırasında IP adresi ataması

**Hedef ağ** | **Ayrıntılar**
--- | ---
Hedef ağ yük devretme VNet | -Hedef IP adresi aynı IP adresiyle statik olacak. <br/><br/>  -Aynı IP adresi zaten atanmışsa, IP adresi alt ağ aralığının sonunda kullanılabilir bir sonraki adrestir. Örneğin: kaynak IP adresi 10.0.0.19 ise ve yük devretme ağı 10.0.0.0/24 aralığını kullanıyorsa, hedef VM 'ye atanan sonraki IP adresi 10.0.0.254 olur.
Hedef ağ yük devretme VNet değil | -Hedef IP adresi aynı IP adresiyle statik olacak.<br/><br/>  -Aynı IP adresi zaten atanmışsa, IP adresi alt ağ aralığının sonunda kullanılabilir bir sonraki adrestir.<br/><br/> Örneğin: kaynak statik IP adresi 10.0.0.19 ise ve yük devretme, 10.0.0.0/24 aralığına sahip yük devretme ağı olmayan bir ağ üzerinde ise, hedef statik IP adresi varsa 10.0.0.0.19 olur ve aksi takdirde 10.0.0.254 olur.

- Yük devretme VNet, olağanüstü durum kurtarmayı ayarlarken seçtiğiniz hedef ağ olur.
- Yük devretme testi için her zaman üretim dışı bir ağ kullanmanızı öneririz.
- Hedef IP adresini, VM 'nin **işlem ve ağ** ayarları ' nda değiştirebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

- Azure VM olağanüstü durum kurtarma için [ağ kılavuzunu](site-recovery-azure-to-azure-networking-guidance.md) gözden geçirin.
- Yük devretmeden sonra IP adreslerini koruma hakkında [daha fazla bilgi edinin](site-recovery-retain-ip-azure-vm-failover.md) .
