---
title: Temel Iç istemciden standart Iç Azure Load Balancer yükseltme
description: Bu makalede, Azure Iç Load Balancer temel SKU 'dan standart SKU 'ya nasıl yükselteceğiniz gösterilmektedir
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 1b7bdbdb9e1d642f2ef4a715d4993e4f449ccd0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050706"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure Iç Load Balancer yükseltme-giden bağlantı gerekmez
[Azure Standart Load Balancer](load-balancer-overview.md) , bölge artıklığı aracılığıyla zengin bir işlev kümesi ve yüksek kullanılabilirlik sağlar. Load Balancer SKU 'SU hakkında daha fazla bilgi için bkz. [karşılaştırma tablosu](./skus.md#skus).

Bu makalede temel Standart Load Balancer Load Balancer Load Balancer aynı yapılandırmaya sahip bir Standart Load Balancer oluşturan bir PowerShell betiği tanıtılmıştır.

## <a name="upgrade-overview"></a>Yükseltmeye genel bakış

Aşağıdakileri gerçekleştiren bir Azure PowerShell betiği vardır:

* Belirttiğiniz konumda standart bir Iç SKU Load Balancer oluşturur. Standart Iç Load Balancer [giden bağlantı](./load-balancer-outbound-connections.md) sağlamayacağını unutmayın.
* Temel SKU 'nun yapılandırmalarının Load Balancer yeni oluşturulan Standart Load Balancer sorunsuzca kopyalar.
* Özel IP 'Leri temel Load Balancer yeni oluşturulan Standart Load Balancer sorunsuzca taşıyın.
* VM 'Leri temel Load Balancer arka uç havuzundan sorunsuz bir şekilde Standart Load Balancer arka uç havuzuna taşıyın

### <a name="caveatslimitations"></a>Caveats\Limitations

* Betik yalnızca giden bağlantı gerekli olmadığında dahili Load Balancer yükseltmesini destekler. Bazı sanal makinelerinize [giden bağlantı](./load-balancer-outbound-connections.md) gerekiyorsa lütfen yönergeler için bu [sayfaya](upgrade-InternalBasic-To-PublicStandard.md) bakın. 
* Temel Load Balancer arka uç VM 'Leri ve NIC 'Lerle aynı kaynak grubunda olması gerekir.
* Standart yük dengeleyici farklı bir bölgede oluşturulduysa, eski bölgede var olan VM 'Leri yeni oluşturulan Standart Load Balancer ilişkilendiremeyeceksiniz. Bu kısıtlamayı geçici olarak çözmek için yeni bölgede yeni bir VM oluşturun.
* Load Balancer herhangi bir ön uç IP yapılandırması veya arka uç havuzu yoksa, betiği çalıştırırken bir hatayla karşılaşamayacaksınız. Bunların boş olmadığından emin olun.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>Ön uç IP yapılandırması için IP ayırma yöntemini statik olarak değiştir (zaten statikse bu adımı yoksayın)

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden temel Load Balancer seçin.

2. **Ayarlar**' ın altında, **ön uç IP yapılandırması**' nı seçin ve ilk ön uç IP yapılandırmasını seçin. 

3. **Atama** için **statik** ' i seçin

4. Temel Load Balancer için tüm ön uç IP yapılandırmalarının 3. adımını tekrarlayın.


## <a name="download-the-script"></a>Betiği indir

[PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0)geçiş betiğini indirin.
## <a name="use-the-script"></a>Betiği kullan

Yerel PowerShell ortamınız kuruluma ve tercihlerinize bağlı olarak sizin için iki seçenek vardır:

* Azure az modules yüklü değilse veya Azure az modüllerini kaldırmayı bilmiyorsanız, en iyi seçenek `Install-Script` betiği çalıştırmak için seçeneğini kullanmaktır.
* Azure az modules tutmanız gerekiyorsa, en iyi sonuç, betiği indirmek ve doğrudan çalıştırmak olacaktır.

Azure az modules yüklü olup olmadığınızı öğrenmek için çalıştırın `Get-InstalledModule -Name az` . Yüklü az modül görmüyorsanız, `Install-Script` yöntemini kullanabilirsiniz.

### <a name="install-using-the-install-script-method"></a>Install-Script yöntemi kullanarak Install

Bu seçeneği kullanmak için, bilgisayarınızda Azure az modules yüklü olmamalıdır. Yüklüyse, aşağıdaki komut bir hata görüntüler. Azure az modüller ' i kaldırabilir veya betiği el ile indirmek ve çalıştırmak için diğer seçeneği kullanabilirsiniz.
  
Betiği aşağıdaki komutla çalıştırın:

`Install-Script -Name AzureILBUpgrade`

Bu komut ayrıca gerekli az modülleri de yüklüyor.  

### <a name="install-using-the-script-directly"></a>Betiği kullanarak doğrudan yüklemeyi

Bazı Azure az modülleriniz varsa ve bunları kaldıramıyorsanız (veya kaldırmak istemiyorsanız), betik indirme bağlantısındaki **El Ile indir** sekmesini kullanarak betiği el ile indirebilirsiniz. Betik, ham nupkg dosyası olarak indirilir. Betiği bu nupkg dosyasından yüklemek için bkz. [El Ile paket indirme](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Betiği çalıştırmak için:

1. `Connect-AzAccount`Azure 'a bağlanmak için kullanın.

1. `Import-Module Az`Az modülleri içeri aktarmak için kullanın.

1. Gerekli parametreleri inceleyin:

   * **RgName: [dize]: gerekli** – bu, var olan temel Load Balancer ve yeni standart Load Balancer için kaynak grubudur. Bu dize değerini bulmak için Azure portal gidin, temel Load Balancer kaynağınızı seçin ve yük dengeleyiciye **Genel Bakış ' a** tıklayın. Kaynak grubu bu sayfada bulunur.
   * **Oldlbname: [dize]: gerekli** – bu, yükseltmek Istediğiniz mevcut temel dengeleyicinizin adıdır. 
   * **newLocation: [dize]: gerekli** – standart Load Balancer oluşturulacağı konumdur. Diğer mevcut kaynaklarla daha iyi ilişki sağlamak için, seçilen temel Load Balancer aynı konumun Standart Load Balancer aynı konuma devralması önerilir.
   * **Newlbname: [dize]: gerekli** – bu, oluşturulacak standart Load Balancer adıdır.
1. Uygun parametreleri kullanarak betiği çalıştırın. Tamamlanması beş ila yedi dakika sürebilir.

    **Örnek**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg&quot; -oldLBName &quot;LBForInternal&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

## <a name="common-questions"></a>Sık sorulan sorular

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Yapılandırmayı v1 'den v2 'ye geçirmek için Azure PowerShell betiğiyle ilgili herhangi bir sınırlama var mı?

Evet. Bkz. [Uyarılar/sınırlamalar](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell betiği, temel Load Balancer trafik üzerinde yeni oluşturulan Standart Load Balancer da geçiş yapar mi?

Evet, trafiği geçirir. Trafiği kişisel olarak geçirmek istiyorsanız, VM 'Leri sizin için taşımayan [bu betiği](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Standart Load Balancer hakkında bilgi edinin](load-balancer-overview.md)
