---
title: Site Recovery kurtarma planlarına Azure Otomasyonu runbook 'ları ekleme
description: Azure Site Recovery kullanarak olağanüstü durum kurtarma için Azure Otomasyonu ile kurtarma planlarını genişletmeyi öğrenin.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173489"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Kurtarma planlarına Azure Otomasyonu runbook 'ları ekleme

Bu makalede, Azure Otomasyonu runbook 'larının [Azure Site Recovery](site-recovery-overview.md) kurtarma planlarını uzatmak için nasıl tümleştirileceği açıklanır. Bunun için el ile müdahale etmesi gereken temel görevleri otomatikleştirmeyi ve çok adımlı bir kurtarmanın tek tıklamayla bir eyleme nasıl dönüştürüleceğini göstereceğiz.

## <a name="recovery-plans"></a>Kurtarma planları 

Şirket içi makinelerin veya Azure VM 'lerinin yükünü devretmek için kurtarma planlarını kullanabilirsiniz. Kurtarma planları, makinelerin yük devretme işlemini ve yük devretmeden sonra nasıl başlatılacağını ve kurtarılacağını tanımlayan sistematik bir kurtarma işlemi tanımlamanıza yardımcı olur. 

Kurtarma büyük uygulamaları karmaşık olabilir. Kurtarma planları, kurtarmanın sürekli doğru, yinelenebilir ve otomatik olmasını sağlayacak şekilde sıra sağlamaya yardımcı olur. Komut dosyalarını ve Azure Otomasyonu runbook 'larını kullanarak bir kurtarma planı içindeki görevleri otomatikleştirebilirsiniz. Tipik örnekler, yük devretmeden sonra Azure VM üzerinde ayarları veya VM üzerinde çalışan bir uygulamayı yeniden yapılandırmayı yapılandırıyor olabilir.

- Kurtarma planları hakkında [daha fazla bilgi edinin](recovery-plan-overview.md) .
- Azure Otomasyonu runbook 'ları hakkında [daha fazla bilgi edinin](../automation/automation-runbook-types.md) .



## <a name="runbooks-in-recovery-plans"></a>Kurtarma planlarındaki runbook 'lar

Bir kurtarma planına Azure Otomasyonu hesabı ve Runbook 'lar eklersiniz. Kurtarma planı çalıştırıldığında runbook çağrılır.

- Otomasyon hesabı herhangi bir Azure bölgesinde olabilir ve Site Recovery kasasıyla aynı abonelikte olmalıdır. 
- Bir runbook, birincil konumdan ikinciye yük devretme sırasında veya ikincil konumdan birincil konuma yeniden çalışma sırasında bir kurtarma planında çalıştırılabilir.
- Bir kurtarma planındaki runbook 'lar, bir diğeri sonra, küme sırasına göre çalıştırılır.
- Kurtarma planındaki runbook 'lar VM 'Leri farklı gruplarda başlatılacak şekilde yapılandırdıysanız, kurtarma planı yalnızca Azure tüm VM 'Leri çalışıyor olarak raporladığında devam eder.
- Kurtarma planları, bir komut dosyası başarısız olsa bile çalışmaya devam eder.

### <a name="recovery-plan-context"></a>Kurtarma planı bağlamı

Bir betik çalıştırıldığında, runbook 'a bir kurtarma planı bağlamını çıkarır. Bağlam, tabloda özetlenen değişkenleri içerir.

| **Değişken adı** | **Açıklama** |
| --- | --- |
| RecoveryPlanName |Kurtarma planı adı. Ada göre eylemlerde kullanılır. |
| Yük üzerine yazma |Bir test veya üretim yük devretmesi olup olmadığını belirtir. 
| FailoverDirection | Kurtarmanın birincil veya ikincil bir konuma yapılıp yapılmayacağını belirtir. |
| ID |Plan çalışırken kurtarma planındaki grup numarasını tanımlar. |
| VmMap |Gruptaki tüm VM 'lerin bir dizisi. |
| VMMap anahtarı |Her VM için benzersiz bir anahtar (GUID). |
| SubscriptionID |VM 'nin oluşturulduğu Azure abonelik KIMLIĞI. |
| resourceGroupName | VM 'nin bulunduğu kaynak grubunun adı.
| CloudServiceName |VM 'nin altında oluşturulduğu Azure bulut hizmeti adı. |
| RoleName |Azure VM 'nin adı. |
| Recoverypointıd|VM kurtarma için zaman damgası. |

Aşağıdaki örnek bir bağlam değişkenini gösterir:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Bir döngüde VMMap içindeki tüm VM 'Lere erişmek istiyorsanız aşağıdaki kodu kullanabilirsiniz:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman parçalara ayırma [bulutlarının](http://harvestingclouds.com) üzerinde bir [kurtarma planı bağlam betiğinin](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)yararlı bir örneği vardır.



## <a name="before-you-start"></a>Başlamadan önce

- Azure Otomasyonu ' na yeni başladıysanız [örnek komut dosyalarını](https://azure.microsoft.com/documentation/scripts/) [kaydedebilir ve](https://azure.microsoft.com/services/automation/) indirebilirsiniz.
- Otomasyon hesabının aşağıdaki modüllere sahip olduğundan emin olun:
    - Azurerd. profil
    - Azurerd. resources
    - Azurerd. Otomasyon
    - Azurerd. ağ
    - Azurerd. Işlem

    Tüm modüller uyumlu sürümler olmalıdır. En basit yol, her zaman tüm modüllerin en son sürümlerini kullanmaktır.



## <a name="customize-the-recovery-plan"></a>Kurtarma planını özelleştirme

1. Kasada, **kurtarma planları ' nı (Site Recovery)** seçin
2. Bir kurtarma planı oluşturmak için **+ kurtarma planı**' na tıklayın. [Daha fazla bilgi edinin](site-recovery-create-recovery-plans.md). Zaten bir kurtarma planınız varsa, açmak için seçin.
3. Kurtarma planı sayfasında, **Özelleştir**' e tıklayın.

    ![Özelleştir düğmesine tıklayın](media/site-recovery-runbook-automation-new/custom-rp.png)

2. **Grup 1**' in yanındaki üç noktaya (...) ve @no__t Başlat-1 ' i**Gönder eylem Ekle**' yi tıklatın.
3. **Ekle eyleminde** **betiğin** seçili olduğunu doğrulayın ve betik için bir ad belirtin (**Merhaba Dünya**).
4. Bir Otomasyon hesabı belirtin ve bir runbook seçin. Betiği kaydetmek için **Tamam**' a tıklayın. Komut dosyası **1. Grup: son adımlar**' a eklenir.


## <a name="reuse-a-runbook-script"></a>Runbook betiğini yeniden kullanma

Çoklu kurtarma planlarında, dış değişkenleri kullanarak tek bir runbook betiği kullanabilirsiniz. 

- Bir kurtarma planını çalıştırmaya yönelik parametreleri depolamak için [Azure Otomasyonu değişkenlerini](../automation/automation-variables.md) kullanırsınız.
- Kurtarma planı adını değişkenine önek olarak ekleyerek her bir kurtarma planı için bağımsız değişkenler oluşturabilirsiniz. Sonra değişkenleri parametre olarak kullanın.
- Betiği değiştirmeden bir parametreyi değiştirebilirsiniz, ancak yine de betiğin çalışma şeklini değiştirebilirsiniz.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Runbook betikte basit bir dize değişkeni kullanma

Bu örnekte, bir komut dosyası bir ağ güvenlik grubu (NSG) girişi alır ve bunu bir kurtarma planındaki VM 'lere uygular. 

1. Komut dosyasının hangi kurtarma planının çalıştığını algılayabilmesi için, bu kurtarma planı bağlamını kullanın:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. NSG adı ve kaynak grubu ' na göz önünde edin. Bu değişkenleri, kurtarma planı betikleri için giriş olarak kullanırsınız. 
1. Otomasyon hesabı varlıkları. NSG adını depolamak için bir değişken oluşturun. Kurtarma planının adı ile değişken adına bir ön ek ekleyin.

    ![NSG ad değişkeni oluşturma](media/site-recovery-runbook-automation-new/var1.png)

2. NSG kaynağı için kaynak grubu adını depolamak üzere bir değişken oluşturun. Kurtarma planının adı ile değişken adına bir ön ek ekleyin.

    ![NSG kaynak grubu adı oluşturma](media/site-recovery-runbook-automation-new/var2.png)


3.  Betikte, değişken değerlerini almak için bu başvuru kodunu kullanın:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  NSG 'yi, yük devredilen VM 'nin ağ arabirimine uygulamak için Runbook 'taki değişkenleri kullanın:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Her kurtarma planı için, betiği yeniden kullanabilmeniz için bağımsız değişkenler oluşturun. Kurtarma planı adını kullanarak bir ön ek ekleyin. 

Bu senaryoya yönelik tam, uçtan uca bir betik için [bu betiği](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)gözden geçirin.


### <a name="use-a-complex-variable-to-store-more-information"></a>Daha fazla bilgi depolamak için karmaşık bir değişken kullanın

Bazı senaryolarda, her kurtarma planı için ayrı değişkenler oluşturabilemeyebilirsiniz. Tek bir betiğin belirli sanal makinelere genel IP adresi atamasını istediğiniz bir senaryo düşünün. Başka bir senaryoda farklı sanal makinelere (tüm VM 'lerde değil) farklı NSG 'ler uygulamak isteyebilirsiniz. Unutmayın:

- Herhangi bir kurtarma planı için yeniden kullanılabilir olan bir betiği yapabilirsiniz.
- Her kurtarma planının değişken sayıda VM olabilir.
- Örneğin, bir SharePoint kurtarması iki ön ucu vardır. Temel bir iş kolu (LOB) uygulamasının yalnızca bir ön ucu vardır.
- Bu senaryoda, her kurtarma planı için ayrı değişkenler oluşturamazsınız.

Aşağıdaki örnekte, Azure Otomasyonu hesabında karmaşık bir [değişken](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) oluşturacağız.

Bunu, Azure PowerShell kullanarak birden çok değer belirterek yapacağız.

1. PowerShell 'de Azure aboneliğinizde oturum açın:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Parametreleri depolamak için, kurtarma planının adını kullanarak karmaşık değişkeni oluşturun:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Bu karmaşık değişkende, **Vmdetails** korunan VM 'nın VM kimliğidir. VM KIMLIĞINI almak için Azure portal VM özelliklerini görüntüleyin. Aşağıdaki ekran görüntüsünde, iki VM 'nin ayrıntılarını depolayan bir değişken gösterilmektedir:

    ![VM KIMLIĞINI GUID olarak kullan](media/site-recovery-runbook-automation-new/vmguid.png)

4. Runbook 'inizdeki bu değişkeni kullanın. Belirtilen VM GUID 'ı kurtarma planı bağlamında bulunursa, sanal makineye NSG 'yi uygulayın:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Runbook 'ınızdan kurtarma planı bağlamının VM 'Leri üzerinden ilerleyin. VM 'nin **$VMDetailsObj**olup olmadığını denetleyin. Varsa, NSG 'yi uygulamak için değişkenin özelliklerine erişin:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Farklı kurtarma planları için aynı betiği kullanabilirsiniz. Farklı değişkenlerde bir kurtarma planına karşılık gelen değeri depolayarak farklı parametreler girin.

## <a name="sample-scripts"></a>Örnek betikler

Otomasyon hesabınıza örnek betikler dağıtmak için **Azure 'A dağıt** düğmesine tıklayın.

[![Azure 'a dağıtın](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Bu video başka bir örnek sağlar. İki katmanlı bir WordPress uygulamasının Azure 'a nasıl kurtarılacağı gösterilmektedir:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Otomasyonu farklı çalıştır hesabı](../automation/automation-create-runas-account.md) hakkında bilgi edinin
- [Azure Otomasyonu örnek komut dosyalarını](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)gözden geçirin.
- Yük devretme çalıştırma hakkında [daha fazla bilgi edinin](site-recovery-failover.md) .



