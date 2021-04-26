---
title: Temel genel 'ten standart Genel Azure Load Balancer yükseltin
description: Bu makalede, Azure genel Load Balancer temel SKU 'dan standart SKU 'ya nasıl yükselteceğiniz gösterilmektedir
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: ef018e58f8336220b96eba568c94efc40a0fb0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612839"
---
# <a name="upgrade-azure-public-load-balancer"></a>Azure genel Load Balancer yükseltme
[Azure Standart Load Balancer](load-balancer-overview.md) , bölge artıklığı aracılığıyla zengin bir işlev kümesi ve yüksek kullanılabilirlik sağlar. Load Balancer SKU 'SU hakkında daha fazla bilgi için bkz. [karşılaştırma tablosu](./skus.md#skus).

Bir yükseltmede iki aşama vardır:

1. IP ayırma yöntemini dinamik iken statik olarak değiştirin.
2. Yükseltme ve trafik geçişini gerçekleştirmek için PowerShell betiğini çalıştırın.

## <a name="upgrade-overview"></a>Yükseltmeye genel bakış

Aşağıdakileri gerçekleştiren bir Azure PowerShell betiği vardır:

* Temel Standart Load Balancer aynı kaynak grubunda belirttiğiniz konum ile standart bir SKU Load Balancer oluşturur.
* Temel SKU 'dan ortak IP adreslerini yerinde standart SKU 'ya yükseltir.
* Temel SKU 'nun yapılandırmalarının Load Balancer yeni oluştur Standart Load Balancer sorunsuzca kopyasını oluşturur.
* Giden bağlantıyı sağlayan varsayılan bir giden kuralı oluşturur.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Betik yalnızca ortak Load Balancer yükseltmesini destekler. Iç temel Load Balancer yükseltme için, yönergeler için [Bu sayfaya](./upgrade-basicinternal-standard.md) bakın.
* Betiği çalıştırmadan önce genel IP adresinin ayırma yöntemi "static" olarak değiştirilmelidir. 
* Load Balancer herhangi bir ön uç IP yapılandırması veya arka uç havuzu yoksa, betiği çalıştırırken bir hatayla karşılaşamayacaksınız. Lütfen boş olmadıklarından emin olun.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Genel IP adresinin ayırma yöntemini statik olarak değiştir

* * * Önerilen adımlarımız şunlardır:

    1. Bu hızlı başlangıçta görevleri yapmak için [Azure Portal](https://portal.azure.com)oturum açın.
 
    1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynak listesinden **temel Load Balancer ILIŞKILI temel genel IP adresini** seçin.
   
    1. **Ayarlar** altında, **Konfigürasyonlar**' ı seçin.
   
    1. **Atama** altında **statik**' ı seçin.
    1. **Kaydet**’i seçin.
    >[!NOTE]
    >Ortak IP 'leri olan VM 'Ler için, ilk olarak aynı IP adresinin garantili olmadığı standart IP adresleri oluşturmanız gerekecektir. VM 'Lerin temel IP 'lerden ilişkisini kaldırın ve yeni oluşturulan standart IP adresleriyle ilişkilendirin. Ardından, Standart Load Balancer arka uç havuzuna VM 'Ler eklemek için yönergeleri takip edebilirsiniz. 

* **Yeni oluşturulan Standart genel Load Balancer arka uç havuzlarına eklemek için yeni VM 'Ler oluşturma**.
    * VM oluşturma ve Standart Load Balancer ile ilişkilendirme hakkında daha fazla yönerge [burada](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)bulunabilir.


## <a name="download-the-script"></a>Betiği indir

[PowerShell Galerisi](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0)geçiş betiğini indirin.
## <a name="use-the-script"></a>Betiği kullan

Yerel PowerShell ortamınız kuruluma ve tercihlerinize bağlı olarak sizin için iki seçenek vardır:

* Azure az modules yüklü değilse veya Azure az modüllerini kaldırmayı bilmiyorsanız, en iyi seçenek `Install-Script` betiği çalıştırmak için seçeneğini kullanmaktır.
* Azure az modules tutmanız gerekiyorsa, en iyi sonuç, betiği indirmek ve doğrudan çalıştırmak olacaktır.

Azure az modules yüklü olup olmadığınızı öğrenmek için çalıştırın `Get-InstalledModule -Name az` . Yüklü az modül görmüyorsanız, `Install-Script` yöntemini kullanabilirsiniz.

### <a name="install-using-the-install-script-method"></a>Install-Script yöntemi kullanarak Install

Bu seçeneği kullanmak için, bilgisayarınızda Azure az modules yüklü olmamalıdır. Yüklüyse, aşağıdaki komut bir hata görüntüler. Azure az modüller ' i kaldırabilir veya betiği el ile indirmek ve çalıştırmak için diğer seçeneği kullanabilirsiniz.
  
Betiği aşağıdaki komutla çalıştırın:

`Install-Script -Name AzurePublicLBUpgrade`

Bu komut ayrıca gerekli az modülleri de yüklüyor.  

### <a name="install-using-the-script-directly"></a>Betiği kullanarak doğrudan yüklemeyi

Bazı Azure az modülleriniz varsa ve bunları kaldıramıyorsanız (veya kaldırmak istemiyorsanız), betik indirme bağlantısındaki **El Ile indir** sekmesini kullanarak betiği el ile indirebilirsiniz. Betik, ham nupkg dosyası olarak indirilir. Betiği bu nupkg dosyasından yüklemek için bkz. [El Ile paket indirme](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Betiği çalıştırmak için:

1. `Connect-AzAccount`Azure 'a bağlanmak için kullanın.

1. `Import-Module Az`Az modülleri içeri aktarmak için kullanın.

1. Gerekli parametreleri inceleyin:

   * **Oldrgname: [dize]: gereklidir** – bu, yükseltmek Istediğiniz mevcut temel Load Balancer kaynak grubudur. Bu dize değerini bulmak için Azure portal gidin, temel Load Balancer kaynağınızı seçin ve yük dengeleyiciye **Genel Bakış ' a** tıklayın. Kaynak grubu bu sayfada bulunur.
   * **Oldlbname: [dize]: gerekli** – bu, yükseltmek Istediğiniz mevcut temel dengeleyicinizin adıdır. 
   * **Newlbname: [dize]: gerekli** – bu, oluşturulacak standart Load Balancer adıdır.
1. Uygun parametreleri kullanarak betiği çalıştırın. Tamamlanması beş ila yedi dakika sürebilir.

    **Örnek**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg&quot; -oldLBName &quot;LBForPublic&quot; -newLbName &quot;LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Giden bağlantı için giden bir kural oluşturma

Bir giden kuralı oluşturmak için [yönergeleri](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) izleyin, böylece
* Giden NAT 'yi sıfırdan tanımlayın.
* Varolan giden NAT 'nin davranışını ölçeklendirin ve ayarlayın.

## <a name="common-questions"></a>Sık sorulan sorular

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Yapılandırmayı v1 'den v2 'ye geçirmek için Azure PowerShell betiğiyle ilgili herhangi bir sınırlama var mı?

Evet. Bkz. [Uyarılar/sınırlamalar](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Yükseltme ne kadar sürer?

Genellikle betiğin tamamlanması yaklaşık 5-10 dakika sürer ve Load Balancer yapılandırmanızın karmaşıklığına göre daha uzun sürebilir. Bu nedenle, kapalı kalma süresini göz önünde bulundurun ve gerekirse yük devretmeyi planlayın.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell betiği, temel Load Balancer trafik üzerinde yeni oluşturulan Standart Load Balancer da geçiş yapar mi?

Evet. Azure PowerShell betiği yalnızca genel IP adresini yükseltmez, yapılandırmayı temel sunucudan Standart Load Balancer kopyalar, ancak aynı zamanda VM 'yi yeni oluşturulan standart ortak Load Balancer arkasına geçirir. 

## <a name="next-steps"></a>Sonraki adımlar

[Standart Load Balancer hakkında bilgi edinin](load-balancer-overview.md)
