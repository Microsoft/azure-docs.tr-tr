---
title: Bir sanal makine ağ trafiği filtresini tanılama sorunu | Microsoft Docs
description: Bir sanal makine için geçerli güvenlik kurallarını görüntüleyerek bir sanal makine ağ trafiği Filtreleme sorununu tanılamayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: d6835d06015923a70301c95370c76efbd0c2163e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776744"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Bir sanal makine ağ trafiği Filtreleme sorununu Tanıla

Bu makalede, bir sanal makine (VM) için geçerli olan ağ güvenlik grubu (NSG) güvenlik kurallarını görüntüleyerek bir ağ trafiği Filtreleme sorununu tanılamayı öğreneceksiniz.

NSG 'ler, bir VM 'ye ve dışına akan trafik türlerini denetlemenizi sağlar. Bir NSG 'yi Azure sanal ağındaki bir alt ağla, bir VM 'ye bağlı bir ağ arabirimine veya her ikisine birden ilişkilendirebilirsiniz. Bir ağ arabirimine uygulanan etkin güvenlik kuralları, bir ağ arabirimiyle ilişkili NSG 'de bulunan kuralların toplamı ve ağ arabiriminin bulunduğu alt ağ. Farklı NSG 'lerdeki kurallar bazen birbirleriyle çakışabilir ve bir VM 'nin ağ bağlantısını etkileyebilir. Sanal makine ağ arabirimlerinizde uygulanan NSG 'lerdeki tüm etkin güvenlik kurallarını görüntüleyebilirsiniz. Sanal ağ, ağ arabirimi veya NSG kavramlarına alışkın değilseniz bkz. [sanal ağa genel bakış](virtual-networks-overview.md), [ağ arabirimi](virtual-network-network-interface.md)ve [ağ güvenlik gruplarına genel bakış](./network-security-groups-overview.md).

## <a name="scenario"></a>Senaryo

İnternet 'ten 80 numaralı bağlantı noktası üzerinden bir VM 'ye bağlanmaya çalışırsanız, ancak bağlantı başarısız olur. 80 numaralı bağlantı noktasına Internet 'ten neden erişemediğiniz hakkında daha fazla anlamak için, Azure [Portal](#diagnose-using-azure-portal), [POWERSHELL](#diagnose-using-powershell)veya [Azure CLI](#diagnose-using-azure-cli)kullanarak bir ağ arabirimi için geçerli güvenlik kurallarını görüntüleyebilirsiniz.

Aşağıdaki adımlarda, için geçerli güvenlik kurallarını görüntülemek için mevcut bir sanal makinenizin olduğunu varsayalım. Mevcut bir sanal makinenizin yoksa, bu makaledeki görevleri gerçekleştirmek için önce bir [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM dağıtın. Bu makaledeki örneklerde, *Myvmvmnıc* adlı bir ağ arabirimine sahip *myvm* adlı bir sanal makine kullanılmaktadır. VM ve ağ arabirimi, *Myresourcegroup* adlı bir kaynak grubunda yer alan ve *Doğu ABD* bölgedeyse. Adımdaki değerleri, sorunu tanılarken kullandığınız VM için uygun şekilde değiştirin.

## <a name="diagnose-using-azure-portal"></a>Azure portal kullanarak tanılama

1. [Gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir Azure hesabıyla Azure [portalında](https://portal.azure.com) oturum açın.
2. Azure portal en üstünde, arama kutusuna VM 'nin adını girin. VM 'nin adı arama sonuçlarında göründüğünde, bunu seçin.
3. **Ayarlar** altında, aşağıdaki resimde gösterildiği gibi **ağ**' ı seçin:

   ![Ekran görüntüsünde, V M V M NIC 'lerimin ağ ayarlarına sahip Azure portal gösterilmektedir.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Önceki resimde listelenen kurallar, **Myvmvmnıc** adlı bir ağ arabirimi içindir. Ağ arabirimi için iki farklı ağ güvenlik grubundan **gelen bağlantı noktası kuralları** olduğunu görürsünüz:
   
   - **Mysubnetnsg**: ağ arabiriminin içinde bulunduğu alt ağ ile ilişkili.
   - **Myvmnsg**: **Myvmvmnıc** adlı VM 'deki ağ arabirimiyle ilişkili.

   **Inyallinbound** adlı kural, [senaryoda](#scenario)açıklandığı gibi, 80 NUMARALı bağlantı noktası üzerinden VM 'ye gelen iletişimi önletedir. Kural, **kaynak** için *0.0.0.0/0* listeler ve bu da internet 'i içerir. Daha yüksek önceliğe (düşük sayı) sahip başka hiçbir kural, bağlantı noktası 80 ' ye izin verir. VM 'nin internet 'ten gelen bağlantı noktası 80 ' e izin vermek için bkz. [sorunu çözün](#resolve-a-problem). Güvenlik kuralları ve Azure 'un nasıl uygulandığı hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grupları](./network-security-groups-overview.md).

   Resmin en altında **giden bağlantı noktası kurallarını** da görürsünüz. , Bu, ağ arabirimi için giden bağlantı noktası kuralları ' nda bulunur. Resimde her NSG için yalnızca dört gelen kural gösterilse de NSG 'lerinizin dörtten fazla kuralı olabilir. Resimde **kaynak ve** **hedef** altında **VirtualNetwork** ve **kaynak** altında **AzureLoadBalancer** görürsünüz. **VirtualNetwork** ve **AzureLoadBalancer** , [hizmet etiketlerdir](./network-security-groups-overview.md#service-tags). Hizmet etiketleri, güvenlik kuralı oluşturma karmaşıklığını en aza indirmeye yardımcı olmak için bir IP adresi önekleri grubunu temsil eder.

4. VM 'nin çalışır durumda olduğundan emin olun ve ardından önceki resimde gösterildiği gibi, aşağıdaki resimde gösterilen etkin güvenlik kurallarını görmek için **geçerli güvenlik kuralları**' nı seçin:

   ![Ekran görüntüsünde, download seçili ve AllowAzureLoadBalancerInbound gelen kuralı seçiliyken etkin güvenlik kuralları bölmesi görüntülenir.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Listelenen kurallar, 3. adımda gördüğünüz şekilde aynıdır, ancak ağ arabirimi ve alt ağ ile ilişkili NSG 'nin farklı sekmeleri vardır. Resimde görebileceğiniz gibi, yalnızca ilk 50 kural gösterilir. Tüm kuralları içeren bir. csv dosyasını indirmek için **İndir**' i seçin.

   Her bir hizmet etiketinin hangi önekleri temsil ettiğini görmek için, **AllowAzureLoadBalancerInbound** adlı kural gibi bir kural seçin. Aşağıdaki resimde **AzureLoadBalancer** Service etiketinin ön ekleri gösterilmektedir:

   ![Ekran görüntüsü, girilen AllowAzureLoadBalancerInbound için adres ön eklerini gösterir.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   **AzureLoadBalancer** hizmet etiketi yalnızca bir ön eki temsil ediyorsa, diğer hizmet etiketleri bazı önekleri temsil eder.

5. Önceki adımlarda, **Myvmvmnıc** adlı bir ağ arabirimine yönelik güvenlik kuralları gösterildi, ancak önceki bazı resimlerde **myVMVMNic2** adlı bir ağ arabirimi de gördünüz. Bu örnekteki VM 'ye bağlı iki ağ arabirimi vardır. Etkin güvenlik kuralları her bir ağ arabirimi için farklı olabilir.

   **MyVMVMNic2** ağ arabiriminin kurallarını görmek için seçin. Aşağıdaki resimde gösterildiği gibi, ağ arabirimi, her iki ağ arabirimi de aynı alt ağda olduğundan, **Myvmvmnıc** ağ arabirimiyle alt ağıyla ilişkili kurallara sahiptir. Bir NSG 'yi bir alt ağ ile ilişkilendirdiğinizde, kuralları alt ağdaki tüm ağ arabirimlerine uygulanır.

   ![Ekran görüntüsünde, V M V M NIC 2 için ağ ayarlarını içeren Azure portal gösterilmektedir.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   **Myvmvmnıc** ağ arabiriminden farklı olarak, **myVMVMNic2** ağ arabirimine ilişkili bir ağ güvenlik grubu yoktur. Her ağ arabirimi ve alt ağ ile ilişkili sıfır veya bir NSG olabilir. Her ağ arabirimiyle veya alt ağıyla ilişkili NSG aynı veya farklı olabilir. Aynı ağ güvenlik grubunu, seçtiğiniz kadar çok ağ arabirimi ve alt ağ ile ilişkilendirebilirsiniz.

Etkin güvenlik kuralları VM aracılığıyla görüntülenmekle birlikte, geçerli güvenlik kurallarını bir bireysel aracılığıyla da görüntüleyebilirsiniz:
- **Ağ arabirimi**: [bir ağ arabirimini görüntülemeyi](virtual-network-network-interface.md#view-network-interface-settings)öğrenin.
- **NSG**: [bir NSG 'yi görüntülemeyi](manage-network-security-group.md#view-details-of-a-network-security-group)öğrenin.

## <a name="diagnose-using-powershell"></a>PowerShell kullanarak tanılama

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell)izleyen komutları veya bilgisayarınızdan PowerShell 'i çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. PowerShell 'i bilgisayarınızdan çalıştırırsanız, Azure PowerShell Module, sürüm 1.0.0 veya sonraki bir sürümü gerekir. `Get-Module -ListAvailable Az`Yüklü sürümü bulmak için bilgisayarınızda çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız, Ayrıca, `Connect-AzAccount` [gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir hesapla Azure 'da oturum açmak için çalıştırmanız gerekir.

[Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)ile bir ağ arabirimi için geçerli güvenlik kurallarını alın. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubunda bulunan *Myvmvmnıc* adlı bir ağ arabirimi için geçerli güvenlik kurallarını alır:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Çıktı JSON biçiminde döndürülür. Çıktıyı anlamak için bkz. [komut çıkışını yorumlama](#interpret-command-output).
Çıktı yalnızca ağ arabirimiyle ilişkili bir NSG, ağ arabiriminin bulunduğu alt ağ veya her ikisi de varsa döndürülür. VM 'nin çalışır durumda olması gerekir. VM, farklı NSG 'ler uygulanmış birden çok ağ arabirimine sahip olabilir. Sorun giderirken, her bir ağ arabirimi için komutunu çalıştırın.

Hala bir bağlantı sorununuz varsa bkz. [ek tanılama](#additional-diagnosis) ve [önemli noktalar](#considerations).

Bir ağ arabiriminin adını bilmiyorsanız, ancak ağ arabiriminin eklendiği VM 'nin adını biliyorsanız, aşağıdaki komutlar bir VM 'ye bağlı tüm ağ arabirimlerinin kimliklerini döndürür:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Aşağıdaki örneğe benzer bir çıktı alırsınız:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Önceki çıktıda, ağ arabirimi adı *Myvmvmnic*' dir.

## <a name="diagnose-using-azure-cli"></a>Azure CLı kullanarak tanılama

Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu makale, Azure CLı sürüm 2.0.32 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, Ayrıca, `az login` [gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir hesapla Azure 'da çalıştırmanız ve oturum açmanız gerekir.

[Az Network Nic List-etkilidir-NSG](/cli/azure/network/nic#az_network_nic_list_effective_nsg)ile bir ağ arabirimi için geçerli güvenlik kurallarını alın. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubunda bulunan *Myvmvmnıc* adlı bir ağ arabirimi için geçerli güvenlik kurallarını alır:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Çıktı JSON biçiminde döndürülür. Çıktıyı anlamak için bkz. [komut çıkışını yorumlama](#interpret-command-output).
Çıktı yalnızca ağ arabirimiyle ilişkili bir NSG, ağ arabiriminin bulunduğu alt ağ veya her ikisi de varsa döndürülür. VM 'nin çalışır durumda olması gerekir. VM, farklı NSG 'ler uygulanmış birden çok ağ arabirimine sahip olabilir. Sorun giderirken, her bir ağ arabirimi için komutunu çalıştırın.

Hala bir bağlantı sorununuz varsa bkz. [ek tanılama](#additional-diagnosis) ve [önemli noktalar](#considerations).

Bir ağ arabiriminin adını bilmiyorsanız, ancak ağ arabiriminin eklendiği VM 'nin adını biliyorsanız, aşağıdaki komutlar bir VM 'ye bağlı tüm ağ arabirimlerinin kimliklerini döndürür:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

Döndürülen çıktı içinde, aşağıdaki örneğe benzer bilgiler görürsünüz:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

Önceki çıktıda, ağ arabirimi adı *Myvmvmnıc arabirimidir*.

## <a name="interpret-command-output"></a>Komut çıkışını yorumlama

[PowerShell](#diagnose-using-powershell)'i veya sorunu tanılamak IÇIN [Azure CLI](#diagnose-using-azure-cli) 'yi kullanlemenizden bağımsız olarak, aşağıdaki bilgileri içeren çıktıyı alırsınız:

- **Networksecuritygroup**: ağ GÜVENLIK grubunun kimliği.
- **İlişkilendirme**: ağ güvenlik grubunun bir *NetworkInterface* veya *subnet* ile ilişkili olup olmadığı. Bir NSG ile ilişkili ise, her NSG için **Networksecuritygroup**, **Association** ve **securittivesecurityrules** ile çıkış döndürülür. Etkin güvenlik kurallarını görüntülemek için komut çalıştırılmadan önce NSG ilişkili veya ilişkisi varsa, değişikliğin komut çıktısında yansıtılması için birkaç saniye beklemeniz gerekebilir.
- **Securittivesecurityrules**: [güvenlik kuralı oluşturma](manage-network-security-group.md#create-a-security-rule)bölümünde her bir özelliğin açıklaması ayrıntılı olarak açıklanmıştır. *Defaultsecurityrules/* ile önceden ortaya çıkacak kural adları, her NSG 'de mevcut olan varsayılan güvenlik kurallarıdır. Kural adları *SecurityRules/* ile önceden oluşturulmuş kurallardır. **Destinationaddresspredüzeltmesini** veya **Sourceaddresspredüzeltilme** özellikleri için **Internet**, **VirtualNetwork** ve **AzureLoadBalancer** gibi bir [hizmet etiketi](./network-security-groups-overview.md#service-tags)belirten kurallar, **expandeddestinationaddresspredüzeltmesini** özelliği için de değerler içermelidir. **Expandeddestinationaddresspredüzeltmesini** özelliği, hizmet etiketiyle temsil edilen tüm adres öneklerini listeler.

Çıktıda yinelenen kuralların listelendiğini görürseniz, bunun nedeni, bir NSG 'nin hem ağ arabirimi hem de alt ağ ile ilişkilendirilmesi olabilir. Her iki NSG de aynı varsayılan kurallara sahiptir ve her iki NSG 'de aynı olan kendi kurallarınızı oluşturduysanız ek yinelenen kurallara sahip olabilir.

**Defaultsecurityrules/ınyallinbound** adlı kural, [senaryoda](#scenario)açıklanan 80 NUMARALı bağlantı noktası üzerinden VM 'ye gelen iletişimi engellemedir. Daha yüksek önceliğe (düşük sayı) sahip başka hiçbir kural, internet 'ten gelen bağlantı noktası 80 ' i sağlar.

## <a name="resolve-a-problem"></a>Bir sorunu çözün

Bu makaledeki [senaryoda](#scenario) sunulan sorunu tanılamak için Azure [portalı](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell)veya [Azure CLI](#diagnose-using-azure-cli) kullanıp kullanmayacağınızı, çözüm aşağıdaki özelliklerle bir ağ güvenlik kuralı oluşturmaktır:

| Özellik                | Değer                                                                              |
|---------                |---------                                                                           |
| Kaynak                  | Herhangi bir                                                                                |
| Kaynak bağlantı noktası aralıkları      | Herhangi biri                                                                                |
| Hedef             | VM 'nin IP adresi, bir IP adresi aralığı veya alt ağdaki tüm adresler. |
| Hedef bağlantı noktası aralıkları | 80                                                                                 |
| Protokol                | TCP                                                                                |
| Eylem                  | İzin Ver                                                                              |
| Öncelik                | 100                                                                                |
| Ad                    | Allow-HTTP-All                                                                     |

Kuralı oluşturduktan sonra, bağlantı noktası 80 internet 'ten gelen trafiğe izin verilir, çünkü kuralın önceliği, trafiği reddeden *Denyallinbound* adlı varsayılan güvenlik kuralından daha yüksektir. [Bir güvenlik kuralı oluşturmayı](manage-network-security-group.md#create-a-security-rule)öğrenin. Farklı NSG 'ler hem ağ arabirimiyle hem de alt ağ ile ilişkiliyse, her iki NSG 'de de aynı kuralı oluşturmanız gerekir.

Azure gelen trafiği işlediğinde, alt ağla ilişkili NSG 'deki kuralları işler (ilişkili bir NSG varsa) ve ardından ağ arabirimiyle ilişkili NSG 'deki kuralları işler. Ağ arabirimi ve alt ağ ile ilişkili bir NSG varsa, trafiğin VM 'ye ulaşması için bağlantı noktasının her iki NSG 'de açık olması gerekir. Yönetim ve iletişim sorunlarını kolaylaştırmak için, bir NSG 'yi tek bir ağ arabirimi yerine bir alt ağ ile ilişkilendirmenizi öneririz. Bir alt ağ içindeki VM 'Lerin farklı güvenlik kuralları olması gerekiyorsa, ağ arabirimlerini bir uygulama güvenlik grubunun (ASG) üyesi yapabilir ve bir güvenlik kuralının kaynağı ve hedefi olarak bir ASG belirtebilirsiniz. [Uygulama güvenlik grupları](./network-security-groups-overview.md#application-security-groups)hakkında daha fazla bilgi edinin.

Hala iletişim sorunlarınız varsa bkz. [hususlar](#considerations) ve ek tanılama.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Bağlantı sorunlarını giderirken aşağıdaki noktaları göz önünde bulundurun:

* Varsayılan güvenlik kuralları Internet 'ten gelen erişimi engeller ve yalnızca sanal ağdan gelen trafiğe izin verir. Internet 'ten gelen trafiğe izin vermek için, varsayılan kurallara göre daha yüksek önceliğe sahip güvenlik kuralları ekleyin. [Varsayılan güvenlik kuralları](./network-security-groups-overview.md#default-security-rules)veya [bir güvenlik kuralı ekleme](manage-network-security-group.md#create-a-security-rule)hakkında daha fazla bilgi edinin.
* Eşlenmiş sanal ağlarınız varsa, varsayılan olarak **VIRTUAL_NETWORK** hizmet etiketi, eşlenmiş sanal ağların öneklerini içerecek şekilde otomatik olarak genişletilir. Sanal ağ eşlemesiyle ilgili sorunları gidermek için, **genişletmeleri Expandedadddresspredüzeltmesini** listesinde görebilirsiniz. [Sanal ağ eşlemesi](virtual-network-peering-overview.md) ve [hizmet etiketleri](./network-security-groups-overview.md#service-tags)hakkında daha fazla bilgi edinin.
* Geçerli güvenlik kuralları yalnızca, VM 'nin ağ arabirimi ve veya alt ağı ile ilişkili bir NSG varsa ve VM çalışır durumdaysa bir ağ arabirimi için gösterilir.
* Ağ arabirimi veya alt ağ ile ilişkili hiçbir NSG yoksa ve bir VM 'ye atanan bir [genel IP adresiniz](virtual-network-public-ip-address.md) varsa, tüm bağlantı noktaları, gelen erişim için ve herhangi bir yere giden erişim için açıktır. VM 'nin genel bir IP adresi varsa, ağ arabirimine alt ağa bir NSG uygulamanız önerilir.

## <a name="additional-diagnosis"></a>Ek tanılama

* Bir VM 'ye veya sanal makineye giden trafiğe izin verilip verilmediğini belirlemede hızlı bir test çalıştırmak için, Azure ağ Izleyicisi 'nin [IP akışı doğrulama](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) özelliğini kullanın. IP akışı doğrulama, trafiğe izin verildiğini veya reddedildiğini bildirir. Reddedilirse, IP akışı doğrulama, trafiği hangi güvenlik kuralının reddetdiğini söyler.
* Bir sanal makinenin ağ bağlantısının başarısız olmasına neden olan bir güvenlik kuralı yoksa, sorunun nedeni aşağıdakiler olabilir:
  * VM 'nin işletim sistemi içinde çalışan güvenlik duvarı yazılımı
  * Sanal gereçler veya şirket içi trafik için yapılandırılmış yollar. Internet trafiği, [Zorlamalı tünel](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)aracılığıyla şirket içi ağınıza yönlendirilebilir. İnternet trafiğini bir Sanal Gereç veya şirket içi olarak tünele zorlarsanız, VM 'ye internet 'ten bağlanamadıysanız. VM dışında trafik akışını engelleyebilecek yol sorunlarının nasıl tanılandığını öğrenmek için bkz. [sanal makine ağ trafiği yönlendirme sorununu tanılama](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Sonraki adımlar

- Bir [ağ güvenlik grubu](manage-network-security-group.md#work-with-network-security-groups) ve [güvenlik kuralları](manage-network-security-group.md#work-with-security-rules)için tüm görevler, Özellikler ve ayarlar hakkında bilgi edinin.
- [Varsayılan güvenlik kuralları](./network-security-groups-overview.md#default-security-rules), [hizmet etiketleri](./network-security-groups-overview.md#service-tags)ve Azure 'un bir VM 'nin [gelen ve giden trafiği için güvenlik kurallarını nasıl işlediği](./network-security-groups-overview.md#network-security-groups) hakkında bilgi edinin.