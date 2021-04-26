---
title: Azure VM 'Leri arasında bağlantı sorunlarını giderme | Microsoft Docs
description: Azure VM 'Ler arasında karşılaşabileceğiniz bağlantı sorunlarını giderme ve çözme hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5e602f8bbdf6ad92acacac6688326a555a0420b6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105042875"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Azure VM’leri arasında bağlantı sorunlarını giderme

Azure sanal makineler (VM 'Ler) arasında bağlantı sorunlarıyla karşılaşabilirsiniz. Bu makalede, bu sorunu çözmenize yardımcı olacak sorun giderme adımları sunulmaktadır. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Belirti

Bir Azure VM, başka bir Azure VM 'ye bağlanamaz.

## <a name="troubleshooting-guidance"></a>Sorun giderme rehberi 

1. [NIC 'nin yanlış yapılandırılmış olup olmadığını denetleyin](#step-1-check-whether-nic-is-misconfigured)
2. [Ağ trafiğinin NSG veya UDR tarafından engellenip engellenmeyeceğini denetleyin](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Ağ trafiğinin VM Güvenlik Duvarı tarafından engellenip engellenmeyeceğini denetleyin](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [VM uygulamasının veya hizmetinin bağlantı noktasında dinleme yapıp yapmadığını denetleyin](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Sorunun SNAT 'den kaynaklanıp kaynaklanmadığını denetleyin](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Klasik VM için trafiğin ACL 'Ler tarafından engellenip engellenmeyeceğini denetleyin](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Uç noktanın klasik VM için oluşturulup oluşturulmayacağını denetleyin](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Bir VM ağ paylaşımıyla bağlantı kurmak için deneyin](#step-8-try-to-connect-to-a-vm-network-share)
9. [Inter-Vnet bağlantısını denetle](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Sorun giderme adımları

Sorunu gidermek için aşağıdaki adımları izleyin. Her adımı tamamladıktan sonra, sorunun çözümlenip çözümlenmediğini denetleyin. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>1. Adım: NIC 'in yanlış yapılandırılmış olup olmadığını denetleme

[Azure WINDOWS VM için ağ arabirimini sıfırlama](/troubleshoot/azure/virtual-machines/reset-network-interface)bölümündeki adımları izleyin. 

Ağ arabirimini (NIC) değiştirdikten sonra sorun oluşursa, aşağıdaki adımları izleyin:

**Çoklu NIC VM 'Leri**

1. Bir NIC ekleyin.
2. Hatalı NIC 'deki sorunları giderin veya Hatalı NIC 'yi kaldırın.  Sonra NIC 'ı yeniden ekleyin.

Daha fazla bilgi için bkz. [sanal makinelere ağ arabirimleri ekleme veya sanal makinelere kaldırma](virtual-network-network-interface-vm.md).

**Tek NIC sanal makinesi** 

- [Windows VM 'yi yeniden dağıtma](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-windows)
- [Linux VM 'yi yeniden dağıtma](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-linux)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>2. Adım: ağ trafiğinin NSG veya UDR tarafından engellenip engellenmeyeceğini denetleyin

Trafik akışını engelleyen bir ağ güvenlik grubu (NSG) veya User-Defined yolu (UDR) olup olmadığını öğrenmek için [ağ IZLEYICISI IP akışı doğrulama](../network-watcher/network-watcher-ip-flow-verify-overview.md) ve [NSG akış günlüğünü](../network-watcher/network-watcher-nsg-flow-logging-overview.md) kullanın.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>3. Adım: ağ trafiğinin VM Güvenlik Duvarı tarafından engellenip engellenmeyeceğini denetleyin

Güvenlik duvarını devre dışı bırakın ve ardından sonucu test edin. Sorun çözümlenirse, güvenlik duvarı ayarlarını doğrulayın ve sonra güvenlik duvarını yeniden etkinleştirin.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>4. Adım: VM uygulamasının veya hizmetinin bağlantı noktasında dinleme yapıp yapmadığını denetleyin

VM uygulamasının veya hizmetinin bağlantı noktasında dinleme yapıp yapmadığını denetlemek için aşağıdaki yöntemlerden birini kullanabilirsiniz.

- Sunucunun bu bağlantı noktasında dinleme yapıp yapmadığını denetlemek için aşağıdaki komutları çalıştırın.

**Windows VM**

```console
netstat –ano
```

**Linux VM**

```console
netstat -l
```

- Bağlantı noktasını test etmek için sanal makinede **Telnet** komutunu çalıştırın. Test başarısız olursa, uygulama veya hizmet Bu bağlantı noktasını dinlemek üzere yapılandırılmamıştır.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>5. Adım: sorunun SNAT 'den kaynaklanıp kaynaklanmadığını denetleyin

Bazı senaryolarda VM, Azure dışındaki kaynaklara bağımlılığı olan bir yük dengeleme çözümünün arkasına yerleştirilir. Bu senaryolarda, aralıklı bağlantı sorunlarıyla karşılaşırsanız, bu sorun [SNAT bağlantı noktası tükenmesi](../load-balancer/load-balancer-outbound-connections.md)yüzünden olabilir. Bu sorunu çözmek için yük dengeleyicinin arkasındaki ve NSG veya ACL ile güvenli olan her bir sanal makine için bir VIP (veya klasik için ıLPıP) oluşturun. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>6. Adım: klasik VM için trafiğin ACL 'Ler tarafından engellenip engellenmeyeceğini denetleyin

Bir erişim denetim listesi (ACL), sanal makine uç noktası için trafiği seçmeli olarak izin verme veya reddetme yeteneği sağlar. Daha fazla bilgi için bkz. [bir uç noktada ACL 'Yi yönetme](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>7. Adım: uç noktanın klasik VM için oluşturulup oluşturulmayacağını denetleme

Klasik dağıtım modelini kullanarak Azure 'da oluşturduğunuz tüm VM 'Ler, aynı bulut hizmetindeki veya sanal ağdaki diğer sanal makinelerle özel bir ağ kanalı üzerinden otomatik olarak iletişim kurabilir. Ancak, diğer sanal ağlardaki bilgisayarların gelen ağ trafiğini bir sanal makineye yönlendirmek için uç noktalar gerekir. Daha fazla bilgi için bkz. [uç noktaları ayarlama](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>8. Adım: bir VM ağ paylaşımından bağlanmayı deneyin

Bir VM ağ paylaşımıyla bağlantı bağlanamazsanız, bu sorun VM 'de kullanılamayan NIC 'lerden kaynaklanıyor olabilir. Kullanılamayan NIC 'Leri silmek için bkz [. kullanılamayan NIC 'leri silme](/troubleshoot/azure/virtual-machines/reset-network-interface#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>9. Adım: Inter-Vnet bağlantıyı denetleme

Trafik akışını engelleyen bir NSG veya UDR olup olmadığını öğrenmek için [ağ IZLEYICISI IP akışı doğrulama](../network-watcher/network-watcher-ip-flow-verify-overview.md) ve [NSG akış günlüğünü](../network-watcher/network-watcher-nsg-flow-logging-overview.md) kullanın. Inter-Vnet yapılandırmanızı [burada](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)da doğrulayabilirsiniz.

### <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun.
Yine de yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).