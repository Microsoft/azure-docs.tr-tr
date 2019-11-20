---
title: Öğretici - Azure PowerShell ile ölçek kümesini otomatik ölçeklendirme| Microsoft Docs
description: CPU talepleri arttıkça ve azaldıkça, sanal makine ölçek kümesini Azure PowerShell ile otomatik olarak ölçeklendirmeyi öğrenin
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2d743b53f5ca74299c865d381f0832729fc956f4
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677587"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Öğretici: Azure PowerShell ile sanal makine ölçek kümesini otomatik olarak ölçeklendirme

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Ölçek kümesi oluşturduğunuzda, çalıştırmak istediğiniz sanal makine örneği sayısını tanımlarsınız. Uygulamanızın talebi değiştikçe, sanal makine örneklerinin sayısını otomatik olarak artırabilir veya azaltabilirsiniz. Otomatik ölçeklendirme özelliği, uygulamanızın yaşam döngüsü boyunca uygulama performansındaki değişikliklere veya müşteri taleplerine ayak uydurmanıza olanak tanır. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ölçek kümesi ile otomatik ölçeklendirmeyi kullanma
> * Otomatik ölçeklendirme kuralları oluşturma ve kullanma
> * Sanal makine örneklerinde stres testi yapma ve otomatik ölçeklendirme kurallarını tetikleme
> * Talep düştüğünde geriye doğru otomatik ölçeklendirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Azure Cloud Shell’in geçerli sürümü de dahil olmak üzere Azure PowerShell modülünün 6.8.1 veya üzeri sürümlerini etkilediği bilinen bir sorun var. Bu öğretici yalnızca Azure PowerShell modülünün 6.0.0 ile 6.8.0 arasındaki sürümleri kullanılarak çalıştırılabilir. Sürümü bulmak için `Get-Module -ListAvailable AzureRM` komutunu çalıştırın. PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzureRmAccount` komutunu da çalıştırmanız gerekir.


## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma
Otomatik ölçeklendirme kurallarını daha kolay oluşturmak için, ölçek kümenize bazı değişkenler tanımlayın. Aşağıdaki örnekte, *myResourceGroup* kaynak grubunda ve *East US* bölgesinde yer alan *myScaleSet* adlı ölçek kümesi için değişkenler tanımlanır. Abonelik kimliğiniz [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) ile alınır. Hesabınızla ilişkilendirilmiş birden çok aboneliğiniz varsa, yalnızca ilk abonelik döndürülür. Adları ve abonelik kimliğini şöyle ayarlayın:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Bu adımda [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) ile bir sanal makine ölçek kümesi oluşturun. Tek tek sanal makine örneklerine trafiği dağıtmak için bir yük dengeleyici de oluşturulur. Yük dengeleyici hem 80 numaralı TCP bağlantı noktasında trafiği dağıtmak hem de 3389 numaralı TCP bağlantı noktasında uzak masaüstü trafiğine ve 5985 numaralı TCP bağlantı noktasında PowerShell uzaktan iletişimine olanak tanımak için kurallar içerir. İstendiğinde, ölçek kümesindeki sanal makine örnekleri için kendi istediğiniz yönetici kimlik bilgilerini sağlayın:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.

## <a name="create-a-rule-to-autoscale-out"></a>Otomatik ölçeklendirme ölçeğini genişletmek için kural oluşturma
Uygulamanızın talebi artarsa, ölçek kümenizdeki sanal makine örneklerinde üzerindeki yük de artar. Bu kısa süreli bir talep olmayıp tutarlı şekilde yük artıyorsa, ölçek kümesindeki sanal makine örneği sayısını artırmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Bu sanal makine örnekleri oluşturulduğunda ve uygulamalarınız dağıtıldığında ölçek kümesi, yük dengeleyici aracılığıyla bunlara trafiği dağıtmaya başlar. CPU veya disk gibi hangi ölçümlerin izleneceğini, uygulama yükünün belirli bir eşiği ne kadar süre karşılaması gerektiği ve ölçek kümesine kaç sanal makine örneği ekleneceğini denetlersiniz.

Şimdi [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) ile, ortalama CPU yükü 5 dakika boyunca %70’in üzerine çıktığında bir ölçek kümesindeki sanal makine örneği sayısını artıran bir kural oluşturalım. Kural tetiklendiğinde, sanal makine örneği sayısı üç artırılır.

Bu kural için aşağıdaki parametreler kullanılır:

| Parametre               | Açıklama                                                                                                         | Değer          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | İzlenecek ve ölçek kümesi eylemlerinin uygulanmasında temel alınacak performans ölçümü.                                                   | CPU yüzdesi |
| *-TimeGrain*            | Analiz için ölçümlerin toplanma sıklığı.                                                                   | 1 dakika       |
| *-MetricStatistic*      | Toplanan ölçümlerin analiz için nasıl bir araya getirileceğini tanımlar.                                                | Ortalama        |
| *-TimeWindow*           | Ölçüm ve eşik değerleri karşılaştırılmadan önce izleme yapılacak süre.                                   | 5 dakika      |
| *-Operator*             | Ölçüm verilerini eşikle karşılaştırmak için kullanılan işleç.                                                     | Büyüktür   |
| *-Threshold*            | Otomatik ölçeklendirme kuralının bir eylemi tetiklemesine neden olan değer.                                                      | %70            |
| *-ScaleActionDirection* | Kural geçerli olduğunda ölçek kümesinin ölçeğinin büyütüleceğini veya küçültüleceğini tanımlar.                                             | Artır       |
| *–ScaleActionScaleType* | Sanal makine örneği sayısının belirli bir değerle değiştirilmesi gerektiğini belirtir.                                    | Değiştirme Sayısı   |
| *-ScaleActionValue*     | Kural tetiklendiğinde değiştirilmesi gereken sanal makine örneklerinin yüzdesi.                                            | 3              |
| *-ScaleActionCooldown*  | Otomatik ölçeklendirme eylemlerinin geçerli olması için kural tekrar uygulanmadan önceki bekleme süresi. | 5 dakika      |

Aşağıdaki örnekte, bu ölçek genişletme kuralını barındıran *myRuleScaleOut* adlı bir nesne oluşturulur. *-MetricResourceId*, daha önce abonelik kimliği, kaynak grubu adı ve ölçek kümesi adı için tanımlanan değişkenleri kullanır:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Otomatik ölçeklendirme ölçeğini daraltmak için kural oluşturma
Bir akşam veya hafta sonu uygulama talebiniz azalabilir. Yük belirli bir süreye yayılarak tutarlı şekilde azalıyorsa, ölçek kümesindeki sanal makine örneği sayısını azaltmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Mevcut talebi karşılamak için gerekli örnek sayısını yalnızca siz çalıştırdığınızdan, bu ölçeği daraltma eylemi ölçek kümenizi çalıştırma maliyetini azaltır.

[New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) ile, ortalama CPU yükü 5 dakika boyunca %30’un altına düştüğünde bir ölçek kümesindeki sanal makine örneği sayısını azaltan başka bir kural oluşturun. Kural tetiklendiğinde, sanal makine örneklerinin sayısı bir azaltılır. Aşağıdaki örnekte bu ölçek daraltma kuralını barındıran *myRuleScaleDown* adlı bir nesne oluşturulur. *-MetricResourceId*, daha önce abonelik kimliği, kaynak grubu adı ve ölçek kümesi adı için tanımlanan değişkenleri kullanır:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Otomatik ölçeklendirme profilini tanımlama
Otomatik ölçeklendirme kurallarınızı ölçek kümesiyle ilişkilendirmek için bir profil oluşturun. Otomatik ölçeklendirme profili varsayılan, en düşük ve en yüksek ölçek kümesi kapasitesini tanımlar ve otomatik ölçeklendirme kurallarınızı ilişkilendirir. [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile) ile bir otomatik ölçeklendirme profili oluşturun. Aşağıdaki örnekte, varsayılan ve en düşük sanal makine örneği kapasitesi *2* ve en yüksek kapasite de *10* olarak ayarlanır. Önceki adımlarda oluşturulan ölçeği genişletme ve ölçeği daraltma kuralları eklenir:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>Ölçek kümesine otomatik ölçeklendirme profili uygulama
Son adım, otomatik ölçeklendirme profilini ölçek kümenize uygulamaktır. Bundan sonra ölçek kümenizin ölçeği, uygulamanın talebine bağlı olarak daraltılabilir veya genişletilebilir. Otomatik ölçek profilini aşağıdaki gösterildiği gibi [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) kullanarak uygulayın:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Ölçek kümesinde CPU yükü oluşturma
Otomatik ölçeklendirme kurallarını test etmek için, ölçek kümesindeki sanal makine örneklerinde biraz CPU yükü oluşturun. Bu benzetimi yapılan CPU yükü, otomatik ölçeklendirme kurallarının ölçeği genişletmesine ve sanal makine örneği sayısını artırmasına neden olur. Benzetimi yapılan CPU yükü daha sonra azaldığında otomatik ölçeklendirme kuralları ölçeği daraltır ve sanal makine örneği sayısını azaltır.

Ölçek kümesindeki sanal makine örneklerine bağlanırken kullanılacak NAT bağlantı noktalarını listelemek için, önce [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer) komutuyla yük dengeleyici nesnesini alın. Ardından, [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) komutuyla gelen NAT kurallarını görüntüleyin:

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Aşağıdaki örnek çıkışta, yük dengeleyicinin genel IP adresi, örnek adı ve NAT kurallarının trafiği ilettiği bağlantı noktası numarası gösterilir:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

Kuralın *Name* değeri, önceki [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) komutunda gösterildiği gibi sanal makine örneğinin adıyla uyumludur. Örneğin, sanal makine örneği *0*'a bağlanmak için *myRDPRule.0* kullanır ve *50001* numaralı bağlantı noktasına bağlanırsınız. Sanal makine örneği *1*'e bağlanmak için, *myRDPRule.1*'den gelen değeri kullanın ve *50002* numaralı bağlantı noktasına bağlanın.

[Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) ile yük dengeleyicinin genel IP adresini görüntüleyin:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Örnek çıktı:

```powershell
IpAddress
---------
52.168.121.216
```

İlk sanal makine örneğinize bir uzak bağlantı oluşturun. Önceki komutlarda gösterildiği gibi, gerekli sanal makine örneği için kendi genel IP adresinizi ve bağlantı noktası numaranızı belirtin. İstendiğinde, ölçek kümesini oluştururken kullanılan kimlik bilgilerini girin (örnek komutlarda varsayılan olarak *azureuser* ve *\@P ssw0rd!* ). Azure Cloud Shell kullanıyorsanız, bu adımı yerel PowerShell isteminden veya Uzak Masaüstü İstemcisinden gerçekleştirin. Aşağıdaki örnek sanal makine örneği *0*'a bağlanır:

```powershell
mstsc /v 52.168.121.216:50001
```

Oturum açıldıktan sonra, görev çubuğundan Internet Explorer’ı açın.

- **Önerilen güvenlik, gizlilik ve uyumluluk ayarlarını kullan** istemini kabul etmek için *Tamam*'ı seçin
- Adres çubuğuna *http://download.sysinternals.com/files/CPUSTRES.zip* yazın.
- Internet Explorer Artırılmış Güvenlik Yapılandırması etkinleştirildiğinden, *http://download.sysinternals.com* etki alanının güvenilen siteler listenize eklenmesi için **Ekle**'yi seçin.
- Dosya indirme için sorulduğunda **Aç**'ı seçin ve ardından *CPUSTRES.EXE* aracı için **Çalıştır**'ı seçin.

Biraz CPU yükü oluşturmak için, **Etkin** iş parçacıklarının iki kutusunu işaretleyin. Her iki iş parçacığının **Etkinlik** açılan menüsünde *En fazla*'yı seçin. Görev Yöneticisi'ni açarak sanal makinedeki CPU yükünün %100 olduğunu doğrulayabilirsiniz.

![CPU Stress yardımcı programı sanal makine örneğinde yük oluşturur](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Uzak masaüstü bağlantısı oturumunu açık bırakın ve başka bir uzak masaüstü bağlantısı oturumu açın. İkinci sanal makine örneğine, önceki [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) cmdlet'inde listelenen bağlantı noktası numarasıyla bağlanın:

```powershell
mstsc /v 52.168.121.216:50002
```

İkinci sanal makine örneğinde oturum açtıktan sonra, önceki adımları yineleyerek *CPUSTRES.EXE* dosyasını indirin ve çalıştırın. Bir kez daha iki **Etkin** iş parçacığı başlatın ve etkinliği *En fazla* olarak ayarlayın.

**CPU Stress** aracının çalışmaya devam etmesini sağlamak için, her iki uzak masaüstü bağlantısı oturumunu da açık bırakın.


## <a name="monitor-the-active-autoscale-rules"></a>Etkin otomatik ölçeklendirme kurallarını izleme
Ölçek kümenizdeki sanal makine örneği sayısını izlemek için **while** kullanın. Otomatik ölçeklendirme ölçeklerinin, sanal makine örneklerinin her birinde **CPUStress* tarafından oluşturulan CPU yüküne yanıt olarak ölçeği genişletme işlemini başlatması 5 dakika sürer:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

CPU eşiği karşılandıktan sonra otomatik ölçeklendirme kuralları, ölçek kümesindeki sanal makine örneği sayısını artırır. Aşağıdaki çıkışta, ölçek kümesi ölçeği genişlettiğinde oluşturulan üç sanal makine gösterilir:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Sanal makine örneklerinizin her birine yönelik uzak masaüstü bağlantısı oturumunuzda **CPU Stress** aracını kapatın. Ölçek kümesi genelinde ortalama CPU yükü normal düzeyine döner. Bir 5 dakika daha geçtikten sonra otomatik ölçeklendirme kuralları, ölçeği daraltarak sanal makine örneklerinin sayısını azaltır. Ölçeği daraltma eylemleri önce en yüksek kimliğe sahip sanal makine örneklerini kaldırır. Bir ölçek kümesi Kullanılabilirlik Kümeleri veya Kullanılabilirlik Alanları kullandığında, eylemlerdeki ölçek VM örnekleri arasında eşit olarak dağıtılır. Aşağıdaki örnek çıkışta, ölçek kümesi otomatik olarak ölçeği daralttığında tek bir sanal makine örneğinin silindiği gösterilir:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

`Ctrl-c` ile *while* yardımcı programından çıkın. Ölçek kümesi, en az örnek sayısı olan 2 örneğe ulaşılıncaya kadar her 5 dakikada bir ölçeği daraltmaya ve bir sanal makine örneğini kaldırmaya devam eder.


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümenizi ve ek kaynaklarınızı kaldırmak için [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) komutunu kullanarak kaynak grubunu ve bu kaynak grubunun tüm kaynaklarını silin. `-Force` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar. `-AsJob` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure PowerShell ile otomatik olarak bir ölçek kümesinin ölçeğini daraltma veya genişletme işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Ölçek kümesiyle otomatik ölçeklendirmeyi kullanma
> * Otomatik ölçeklendirme kuralları oluşturma ve kullanma
> * Sanal makine örneklerinde stres testi yapma ve otomatik ölçeklendirme kurallarını tetikleme
> * Talep düştüğünde geriye doğru otomatik ölçeklendirme

Sanal makine ölçek kümelerinin uygulamalı olarak daha fazla örneğini görmek için aşağıdaki Azure PowerShell örnek betiklerine bakın:

> [!div class="nextstepaction"]
> [Azure PowerShell için ölçek kümesi betik örnekleri](powershell-samples.md)
