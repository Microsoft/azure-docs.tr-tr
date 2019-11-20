---
title: Fiyat Kılavuzu & maliyetleri yönetme
description: ', Doğru SQL Server sanal makine fiyatlandırma modelini seçmek için en iyi yöntemleri sağlar.'
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 076d6fc387aaee85a1cd407fa48e7347ff185ef4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038871"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Azure SQL Server VM 'Leri için fiyatlandırma Kılavuzu

Bu makalede, Azure 'da [SQL Server sanal makineler](virtual-machines-windows-sql-server-iaas-overview.md) için fiyatlandırma Kılavuzu sunulmaktadır. Maliyeti etkileyen çeşitli seçenekler vardır ve maliyetleri iş gereksinimleriyle dengeleyen doğru görüntüyü seçmek önemlidir.

> [!TIP]
> Yalnızca SQL Server Edition 'ın ve sanal makine boyutunun belirli bir birleşimi için maliyet tahmini yapmanız gerekiyorsa, [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) veya [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux)için fiyatlandırma sayfasına bakın. **Işletim sistemi/yazılım** listesinden platformunuzu ve SQL Server sürümünüzü seçin.
>
> ![VM fiyatlandırma sayfasında kullanıcı arabirimi](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Ya da bir sanal makine eklemek ve yapılandırmak için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/#explore-cost) kullanın. 

## <a name="free-licensed-sql-server-editions"></a>Ücretsiz lisanslı SQL Server sürümleri

Kavram kanıtı geliştirmek, test etmek veya oluşturmak istiyorsanız, ücretsiz lisanslı **SQL Server Developer sürümünü**kullanın. Bu sürüm SQL Server Enterprise sürümünün tüm özelliklerine sahiptir, böylece herhangi bir uygulama türünü oluşturup test edebilirsiniz. Ancak, üretim ortamında geliştirici sürümünü çalıştıramazsınız. SQL Server Developer sürümü VM, ilişkili bir SQL Server Lisanslama maliyeti olmadığından yalnızca VM 'nin maliyeti için ücret doğurur.

Üretimde hafif bir iş yükü (< 4 çekirdek, < 1 GB bellek, < 10 GB/veritabanı) çalıştırmak istiyorsanız, serbestçe lisanslanan **SQL Server Express sürümünü**kullanın. Bir SQL Server Express sürümü VM 'si, yalnızca VM 'nin maliyeti için ücret doğurur.

Bu geliştirme/test ve hafif üretim iş yükleri için, bu iş yükleriyle eşleşen daha küçük bir VM boyutu seçerek de tasarruf edebilirsiniz. DS1v2, bazı senaryolarda iyi bir seçenek olabilir.

Bu görüntülerden biriyle SQL Server 2017 Azure VM oluşturmak için aşağıdaki bağlantılara bakın:

| Platform | Serbestçe lisanslanan görüntüler |
|---|---|
| Windows Server 2016 | [SQL Server 2017 geliştirici Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 geliştirici Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 geliştirici Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 geliştirici Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Ücretli SQL Server sürümleri

Hafif olmayan bir üretim iş yükünüz varsa, aşağıdaki SQL Server sürümlerinden birini kullanın:

| SQL Server sürümü | İş yükü |
|-----|-----|
| Web | Küçük Web siteleri |
| Standart | Küçük ve orta ölçekli iş yükleri |
| Enterprise | Büyük veya görev açısından kritik iş yükleri|

Bu sürümler için SQL Server Lisanslama için ödeme yapmak üzere iki seçeneğiniz vardır: *kullanım başına ödeme* *yapın veya kendi LISANSıNıZı getirin (KLG)* .

## <a name="pay-per-usage"></a>Kullanım başına ödeme

**SQL Server lisansını kullanım başına** (Kullandıkça Öde olarak da bilinir) **ödeyin**, Azure VM 'yi çalıştırmanın ikinci başına maliyetinin SQL Server lisansın maliyetini içerir. [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) veya [LINUX](https://azure.microsoft.com/pricing/details/virtual-machines/linux)için Azure VM fiyatlandırma sayfasında farklı SQL Server sürümleri (Web, standart, kurumsal) için fiyatlandırmayı görebilirsiniz.

Maliyet, tüm SQL Server sürümleri (2012 SP3 2017 ' e) için aynıdır. Saniye başına lisans maliyeti, VM vCPU sayısına bağlıdır.

Kullanım başına SQL Server lisansa ödeme yapılması önerilir:

- **Geçici veya düzenli iş yükleri**. Örneğin, her yıl birkaç ayda bir olayı desteklemesi gereken bir uygulama veya Mondays iş analizi.

- **Bilinmeyen yaşam süresine veya ölçeğe sahip Iş yükleri**. Örneğin, birkaç ay içinde gerekli olmayan veya talebe bağlı olarak daha fazla veya daha az işlem gücü gerektiren bir uygulama.

Bu Kullandıkça Öde görüntülerinden biriyle bir SQL Server 2017 Azure sanal makinesi oluşturmak için aşağıdaki bağlantılara bakın:

| Platform | Lisanslı görüntüler |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standart Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standart Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standart Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standart Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Portalda bir SQL Server sanal makine oluşturduğunuzda, **boyut seç** penceresinde tahmini bir maliyet gösterilir. Bu tahminin yalnızca VM 'yi çalıştırmaya yönelik işlem maliyetleri (Windows veya üçüncü taraf Linux işletim sistemleri) ile birlikte çalıştırılması önemlidir.
>
> ![VM boyutu dikey penceresini seçin](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Web, Standard ve Enterprise sürümleri için ek SQL Server lisanslama maliyetleri içermez. En doğru fiyatlandırma tahminini almak için, [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) veya [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)'un fiyatlandırma sayfasında işletim sisteminizi ve SQL Server sürümünüzü seçin.

> [!NOTE]
> Lisans modelini, kendi lisansını getir (KLG) ve geri almak için kullanım başına ödeme durumundan değiştirmek mümkündür. Daha fazla bilgi için bkz. [SQL VM için lisans modelini değiştirme](virtual-machines-windows-sql-ahb.md). 

## <a id="byol"></a>Kendi lisansınızı getirin (KLG)

**Kendi SQL Server lisansınızı** **KLG**olarak da adlandırılan lisans taşınabilirliği üzerinden GETIRME, bir Azure VM 'de yazılım güvencesi Ile mevcut bir SQL Server toplu lisansın kullanılması anlamına gelir. KLG kullanarak yalnızca bir toplu lisanslama programı aracılığıyla lisans ve yazılım güvencesi almış olmanız durumunda, SQL Server Lisanslama için değil, yalnızca VM 'nin çalıştırılmasına yönelik ücret ücretleri SQL Server VM.

> [!IMPORTANT]
> KLG görüntüleri yazılım güvencesi içeren bir Kurumsal Anlaşma gerektirir. Azure bulut çözümü ortağı 'nın (CSP) bir parçası olarak şu anda kullanılamaz. CSP müşterileri, Kullandıkça Öde görüntüsünü dağıtarak ve sonra [Azure hibrit avantajı](virtual-machines-windows-sql-ahb.md)etkinleştirerek kendi lisansını getirebilir.

> [!NOTE]
> KLG görüntüleri şu anda yalnızca Windows sanal makineler için kullanılabilir. Ancak, yalnızca Linux VM 'ye SQL Server el ile yükleyebilirsiniz. [LINUX SQL VM SSS](../../linux/sql/sql-server-linux-faq.md)bölümündeki yönergelere bakın.

Kendi SQL lisanslarınızın Lisans Taşınabilirliği üzerinden getirilmesi için önerilir:

- **Sürekli iş yükleri**. Örneğin, iş işlemlerini 7/24 desteklemek için gereken bir uygulama.

- **Bilinen yaşam süresi ve ölçeğe sahip Iş yükleri**. Örneğin, tüm yıl için gerekli olan ve hangi talebin tahmin ettiği bir uygulama.

Bir SQL Server VM ile KLG kullanmak için, bazı toplu lisanslama programları ve diğer kişilerle isteğe bağlı satın alma işlemleri aracılığıyla gerekli bir seçenek olan SQL Server Standard veya kuruluş ve [yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1)lisansına sahip olmanız gerekir. Toplu Lisanslama programları aracılığıyla belirtilen fiyatlandırma düzeyi, sözleşmenin türüne ve miktarına ve SQL Server taahhütüne göre değişir. Ancak, bir Thumb kuralı olarak, sürekli üretim iş yükleri için kendi lisansınızı getirmek aşağıdaki avantajlara sahiptir:

| KLG avantajı | Açıklama |
|-----|-----|
| **Maliyet tasarrufları** | [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) %55 tasarruf sağlar. Daha fazla bilgi için bkz. [Lisans modelini değiştirme](virtual-machines-windows-sql-ahb.md) |
| **Ücretsiz pasif ikincil çoğaltma** | Kendi lisansınızı kullanmanın bir diğer avantajı da, yüksek kullanılabilirlik amaçlarıyla SQL Server başına [bir pasif ikincil çoğaltmanın ücretsiz lisanslaması](https://azure.microsoft.com/pricing/licensing-faq/) olur. Bu, yüksek oranda kullanılabilir SQL Server dağıtımının lisanslama maliyetinin yarısını keser (örneğin, Always on kullanılabilirlik grupları kullanılarak). Pasif ikincil çalıştırma hakları, yük devretme sunucuları yazılım güvencesi avantajı üzerinden sağlanır. |

Kendi lisans yansımalarından biriyle bir SQL Server 2017 Azure VM oluşturmak için, "{KLG}" önekli VM 'Lere bakın:

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standart Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Azure 'da kaç SQL Server Lisansı kullandığınızı öğrenmek için 10 gün içinde bize bilgi verin. Önceki görüntülerin bağlantıları bunun nasıl yapılacağı hakkında yönergeler sağlar.

> [!NOTE]
> Lisans modelini, kendi lisansını getir (KLG) ve geri almak için kullanım başına ödeme durumundan değiştirmek mümkündür. Daha fazla bilgi için bkz. [SQL VM için lisans modelini değiştirme](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Maliyetleri azaltma

Gereksiz maliyetleri önlemek için, en iyi sanal makine boyutunu seçin ve sürekli olmayan iş yükleri için aralıklı kapatmalar düşünün.

### <a id="machinesize"></a>SANAL makinenizin boyutunu doğru yapın

SQL Server Lisanslama ücreti, vCPU sayısıyla doğrudan ilgilidir. CPU, bellek, depolama ve g/ç bant genişliği için beklenen gereksinimle eşleşen bir VM boyutu seçin. Makine boyutu seçeneklerinin tüm listesi için bkz. [WINDOWS VM boyutları](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) ve [Linux VM boyutları](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Belirli türdeki SQL Server iş yükleriyle iyi çalışan yeni makine boyutları vardır. Bu makine boyutları, yüksek düzeyde bellek, depolama ve g/ç bant genişliğini korur, ancak daha düşük bir sanallaştırılmış çekirdek sayısına sahiptir. Örneğin, aşağıdaki örneği göz önünde bulundurun:

| VM Boyutu | vCPU | Bellek | En fazla disk | En fazla g/ç performansı | SQL lisanslama maliyetleri | Toplam maliyet (Işlem + lisanslama) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51.200 ıOPS veya 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51.200 ıOPS veya 768 MB/s | %75 daha düşük | %57 daha düşük |

> [!IMPORTANT]
> Bu bir zaman noktası örneğidir. En son belirtimlerde, [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ve [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)için makine boyutları makalelerine ve Azure fiyatlandırma sayfasına bakın.

Önceki örnekte, **Standard_DS14v2** ve **Standard_DS14-4V2** belirtimlerinin vCPU 'lar hariç özdeş olduğunu görebilirsiniz. **Standard_DS14-4v2** makine boyutunun sonundaki son ek **-4v2** , etkin vCPU sayısını gösterir. SQL Server lisanslama maliyetleri vCPU sayısına bağlı olduğundan, bu, ek vCPU 'ların gerekli olmadığı senaryolarda VM 'nin maliyetini önemli ölçüde azaltır. Bu bir örnektir ve bu sonek düzeniyle tanımlanan kısıtlanmış vCPU 'Lara sahip çok sayıda makine boyutu vardır. Daha fazla bilgi için [daha fazla maliyetli veritabanı çalışması için yeni Azure VM boyutlarını duyuran](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)blog gönderisine bakın.

### <a name="shut-down-your-vm-when-possible"></a>Mümkün olduğunda sanal makineyi kapatın

Sürekli çalıştırmayan iş yüklerini kullanıyorsanız, etkin olmayan dönemler sırasında sanal makineyi kapatmayı göz önünde bulundurun. Sadece kullandığınız kadar ödersiniz.

Örneğin, yalnızca bir Azure VM üzerinde SQL Server deniyorsanız, yanlışlıkla hafta boyunca çalışır durumda bırakarak ücretlendirmeye tabi olmak istemezsiniz. Tek bir çözüm [otomatik kapatılma özelliğini](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)kullanmaktır.

![SQL VM oto kapatması](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Otomatik kapatmalar, [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab)tarafından sunulan daha büyük bir benzer özellikler kümesinin bir parçasıdır.

Diğer iş akışları için Azure [Otomasyonu](https://azure.microsoft.com/services/automation/)gibi bir betik çözümü Ile Azure VM 'lerini otomatik olarak kapatmayı ve yeniden başlatmayı düşünün.

> [!IMPORTANT]
> SANAL makinenizin kapatılması ve ayırmayı kaldırmak, ücretlendirmenin tek yoludur. VM 'yi kapatmak için güç seçeneklerini durdurmak veya kullanmak, hala kullanım ücretleri doğurur.

## <a name="next-steps"></a>Sonraki adımlar

Genel Azure fiyatlandırma Kılavuzu için bkz. [Azure Faturalandırma ve maliyet yönetimiyle ilgili beklenmedik maliyetleri engelleme](../../../billing/billing-getting-started.md). SQL Server dahil olmak üzere en son sanal makineler fiyatlandırması için bkz. [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 'Leri ve [Linux VM 'LERI](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)için Azure VM Azure fiyatlandırma sayfası.

Azure sanal makinelerinde çalışan SQL Server bir genel bakış için aşağıdaki makalelere bakın:

- [Windows VM 'lerinde SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
- [Linux üzerinde SQL Server VM 'lerine genel bakış](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
