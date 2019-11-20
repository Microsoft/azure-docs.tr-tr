---
title: Azure 'da Windows Sanal Makineleri SQL Server SSS | Microsoft Docs
description: Bu makalede, Azure VM 'lerinde SQL Server çalıştırma hakkında sık sorulan sorulara yanıtlar sağlanmaktadır.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: eeda78c69c21fafcbe64071422bf7d73a4737249
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208309"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Azure 'da Windows sanal makinelerinde çalışan SQL Server hakkında sık sorulan sorular

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Bu makalede, [Azure 'da Windows sanal makineleri üzerinde SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)çalıştırmaya ilişkin en yaygın soruların yanıtları sunulmaktadır.

> [!NOTE]
> Bu makalede, Windows VM 'lerinde SQL Server özgü sorunlar ele alınmaktadır. Linux üzerinde SQL Server VM 'Leri çalıştırıyorsanız, [Linux hakkında SSS](../../linux/sql/sql-server-linux-faq.md)bölümüne bakın.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Yansımasını

1. **SQL Server sanal makine galeri görüntüleri kullanılabilir?** 

   Azure, hem Windows hem de Linux için tüm sürümlerde SQL Server tüm desteklenen ana sürümleri için sanal makine görüntülerini tutar. Daha fazla bilgi için [WINDOWS VM görüntülerinin](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) ve [Linux VM görüntülerinin](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)tüm listesini inceleyin.

1. **Mevcut SQL Server sanal makine galeri görüntüleri güncelleştirildi mi?**

   Her iki ayda bir sanal makine galerisinde SQL Server görüntüler en son Windows ve Linux güncelleştirmeleriyle güncelleştirilir. Windows görüntüleri için, önemli SQL Server güvenlik güncelleştirmeleri ve hizmet paketleri de dahil olmak üzere Windows Update önemli olarak işaretlenmiş tüm güncelleştirmeleri içerir. Linux görüntüleri için bu en son sistem güncelleştirmelerini içerir. SQL Server toplu güncelleştirmeler, Linux ve Windows için farklı şekilde işlenir. Linux için SQL Server toplu güncelleştirmeler de yenilemeye dahildir. Ancak şu anda Windows VM 'Ler SQL Server veya Windows Server toplu güncelleştirmeleriyle güncellenmez.

1. **SQL Server sanal makine görüntülerinin Galeriden kaldırılması olabilir mi?**

   Evet. Azure, ana sürüm ve sürüm başına yalnızca bir görüntü tutar. Örneğin, yeni bir SQL Server hizmet paketi yayınlandığında Azure bu hizmet paketinin galerisine yeni bir görüntü ekler. Önceki hizmet paketinin SQL Server görüntüsü hemen Azure portal kaldırılır. Ancak, sonraki üç ay için PowerShell 'den sağlama için hala kullanılabilir. Üç aydan sonra, önceki hizmet paketi görüntüsü artık kullanılamaz. SQL Server bir sürüm yaşam döngüsünün sonuna ulaştığında desteklenmiyorsa bu kaldırma ilkesi de geçerlidir.


1. **Azure portal görünmeyen SQL Server eski bir görüntüsünü dağıtmak mümkün mü?**

   Evet, PowerShell kullanarak. PowerShell kullanarak SQL Server VM 'Leri dağıtma hakkında daha fazla bilgi için, bkz. [Azure PowerShell ile SQL Server sanal makineler sağlama](virtual-machines-windows-ps-sql-create.md).

1. **SQL Server VM genelleştirilmiş bir Azure SQL Server Market görüntüsü oluşturabilir miyim ve VM dağıtmak için kullanabilir miyim?**

   Evet, ancak her bir SQL Server VM portalda SQL Server VM yönetmeniz ve otomatik düzeltme eki uygulama ve otomatik yedeklemeler gibi özelliklerden yararlanmak için [SQL Server VM kaynak sağlayıcısıyla kaydetmeniz](virtual-machines-windows-sql-register-with-resource-provider.md) gerekir. Kaynak sağlayıcısına kaydolurken her bir SQL Server VM için lisans türünü de belirtmeniz gerekecektir. 

1. **SQL Server VM dağıtmak için kendi VHD 'mi kullanabilir miyim?**

   Evet, ancak her bir SQL Server VM portalda SQL Server VM yönetmeniz ve otomatik düzeltme eki uygulama ve otomatik yedeklemeler gibi özelliklerden yararlanmak için [SQL Server VM kaynak sağlayıcısıyla kaydetmeniz](virtual-machines-windows-sql-register-with-resource-provider.md) gerekir.

1. **Sanal makine galerisinde gösterilmez (örneğin, Windows 2008 R2 + SQL Server 2012), yapılandırma ayarları yapılabilir mi?**

   Hayır. SQL Server içeren sanal makine galeri görüntüleri için, Azure portal veya [PowerShell](virtual-machines-windows-ps-sql-create.md)aracılığıyla, belirtilen görüntülerden birini seçmeniz gerekir. Bununla birlikte, bir Windows sanal makinesini dağıtabilir ve kendisine SQL Server kendi kendine yükleyebilirsiniz. Sonra, portalda SQL Server VM yönetmek için [SQL Server VM SQL Server VM kaynak sağlayıcısına kaydetmelisiniz](virtual-machines-windows-sql-register-with-resource-provider.md) ve otomatik düzeltme eki uygulama ve otomatik yedeklemeler gibi özelliklerden yararlanabilirsiniz. 


## <a name="creation"></a>Oluşturulurken

1. **SQL Server bir Azure sanal makinesi mi Nasıl yaparım??**

   En kolay yöntem SQL Server içeren bir sanal makine oluşturmaktır. Azure 'a kaydolma ve portaldan SQL Server VM oluşturma hakkında bir öğretici için, bkz. [Azure portal SQL Server sanal makinesi sağlama](virtual-machines-windows-portal-sql-server-provision.md). Saniye başına ödeme SQL Server Lisanslama kullanan bir sanal makine görüntüsü seçebilir veya kendi SQL Server lisansınızı getirmenize olanak sağlayan bir görüntü kullanabilirsiniz. Ayrıca, ücretsiz lisanslı bir sürüme (Geliştirici veya Express) sahip bir VM 'ye SQL Server el ile yükleme veya şirket içi bir lisans yeniden kullanma seçeneğiniz de vardır. Portalda SQL Server VM yönetmek için [SQL Server VM kaynak sağlayıcısına SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) kaydettiğinizden ve otomatik düzeltme eki uygulama ve otomatik yedeklemeler gibi özelliklerden yararlanın. Kendi lisansınızı getirayarladıysanız [Azure üzerinde Yazılım Güvencesiyle Lisans Taşınabilirliği](https://azure.microsoft.com/pricing/license-mobility/)olması gerekir. Daha fazla bilgi için bkz. [SQL Server Azure VM’leri için fiyatlandırma kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Şirket içi SQL Server veritabanından buluta nasıl geçirebilirim?**

   İlk olarak SQL Server örneği olan bir Azure sanal makinesi oluşturun. Daha sonra şirket içi veritabanlarınızı bu örneğe geçirin. Veri geçiş stratejileri için bkz. [Azure VM 'de SQL Server SQL Server veritabanını geçirme](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Lisanslama

1. **Lisanslı SQL Server kopyamı bir Azure sanal makinesine nasıl yükleyebilirim?**

   Bunu yapmak için üç yol vardır. Kurumsal Anlaşma (EA) müşterisiyseniz, [Lisansları destekleyen sanal makine görüntülerinden](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)birini sağlayabilirsiniz ve bu, kendi lisansını getır (KLG) olarak da bilinir. [Yazılım güvencesi](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)varsa, mevcut bir Kullandıkça Öde (PAYG) görüntüsünde [Azure hibrit avantajı](virtual-machines-windows-sql-ahb.md) etkinleştirebilirsiniz. Veya SQL Server yükleme medyasını bir Windows Server sanal makinesine kopyalayabilir ve sonra sanal makineye SQL Server yükleyebilirsiniz. Portal yönetimi, otomatik yedekleme ve otomatik düzeltme eki uygulama gibi özellikler için SQL Server VM [kaynak sağlayıcısına](virtual-machines-windows-sql-register-with-resource-provider.md) kaydettiğinizden emin olun. 

1. **Yalnızca bekleme/yük devretme için kullanılıyorsa Azure sanal makinesindeki SQL Server'ı lisanslamak için ödeme yapmam gerekir mi?**

   Bekleme bir ikincil kullanılabilirlik grubu veya yük devretme kümelenmiş örneği için ücretsiz bir pasif lisansa sahip olmak için, [Lisanslama Kılavuzu PDF](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf)tarafından belirtilen aşağıdaki ölçütlerin tümünü karşılamanız gerekir:

   1. [Yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)aracılığıyla [Lisans Taşınabilirliği](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) vardır. 
   1. Pasif SQL Server örneği, istemcilere SQL Server verileri sunabilir veya etkin SQL Server iş yüklerini çalıştırmaz. Yalnızca birincil sunucuyla eşitlenmek ve pasif veritabanını bir sıcak bekleme durumunda tutmak için kullanılır. Etkin SQL Server iş yüklerini çalıştıran istemcilere rapor veya ikincil sunucudan ek yedeklemeler gibi "iş" işlemi gerçekleştiriyorsa, ücretli bir lisanslı SQL Server örneği olması gerekir. 
   1. Etkin SQL Server Lisansı yazılım güvencesi kapsamında ele alınmıştır ve yalnızca lisanslı etkin sunucu ile aynı işlem miktarına kadar **bir** pasif ikincil SQL Server örneğine izin verir. 
   1. İkincil SQL Server VM, kendi lisansını getir (KLG) veya Azure Hibrit Avantajı (AHB) [Lisans modelini](virtual-machines-windows-sql-ahb.md)kullanır. 

1. **Kullandıkça ödeme galeri görüntülerinden biri kullanılarak oluşturulan sanal makineyi kendi SQL Server lisansımı kullanmak için değiştirebilir miyim?**

   Evet. Bir Kullandıkça Öde (PAYG) Galerisi görüntüsünü, [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)etkinleştirerek kendi lisansını getır (KLG) seçeneğine kolayca geçirebilirsiniz.  Daha fazla bilgi için bkz. [SQL Server VM için lisans modelini değiştirme](virtual-machines-windows-sql-ahb.md). Şu anda bu özellik yalnızca genel bulut müşterileri için kullanılabilir.

1. **Lisanslama modellerini değiştirmek için SQL Server'ın kapalı kalması gerekir mi?**

   Hayır. Değişiklik hemen etkin olduğundan ve VM 'nin yeniden başlatılmasını gerektirmediğinden, [Lisans modelinin değiştirilmesi](virtual-machines-windows-sql-ahb.md) SQL Server için kapalı kalma süresi gerektirmez. Ancak, SQL Server VM SQL Server VM kaynak sağlayıcısına kaydetmek için [SQL IaaS uzantısı](virtual-machines-windows-sql-server-agent-extension.md) bir önkoşuldur ve SQL IaaS uzantısını _tam_ modda yüklemek SQL Server hizmetini yeniden başlatır. Bu nedenle, SQL IaaS uzantısının yüklenmesi gerekiyorsa, sınırlı işlevsellik için _basit_ modda yükleme yapın veya bir bakım penceresi sırasında _tam_ moda yükleme yapın. _Hafif_ modda yüklü olan SQL IaaS uzantısı dilediğiniz zaman _tam_ moda yükseltilebilir, ancak SQL Server hizmeti 'nin yeniden başlatılmasını gerektirir. 

1. **Aynı VM 'de birden çok örneği yönetmek için Azure portal kullanabilir miyim?**

   Hayır. Portal yönetimi, SQL Server IaaS Aracısı uzantısına bağlı SQL Server VM kaynak sağlayıcısı tarafından sunulan bir özelliktir. Bu nedenle, uzantı ile aynı sınırlamalar kaynak sağlayıcısı için de geçerlidir. Portal, doğru şekilde yapılandırıldığı sürece yalnızca bir varsayılan örneği ya da bir adlandırılmış örneği yönetebilir. Bu sınırlamalar hakkında daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](virtual-machines-windows-sql-server-agent-extension.md). 

1. **CSP abonelikleri Azure Hibrit Avantajı aktive edebilir mi?**

   Evet, Azure Hibrit Avantajı CSP abonelikleri için kullanılabilir. CSP müşterilerinin öncelikle bir Kullandıkça Öde görüntüsünü dağıtması ve [Lisans modelini](virtual-machines-windows-sql-ahb.md) kendi lisansını getir olarak değiştirmesi gerekir.

1. **Yeni SQL Server VM kaynak sağlayıcısına ait VM mi kaydedilecek ek maliyetler mi olacak?**

   Hayır. SQL Server VM kaynak sağlayıcısı, ek ücret ödemeden Azure sanal makinesinde SQL Server için ek yönetilebilirlik sunar. 

1. **SQL Server VM kaynak sağlayıcısı tüm müşteriler için kullanılabilir mi?**
 
   Evet, SQL Server VM genel buluta, klasik model değil Kaynak Yöneticisi modeli kullanılarak dağıtıldığı sürece. Diğer tüm müşteriler yeni SQL Server VM kaynak sağlayıcısına kaydolayamaz. Ancak, yalnızca [yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) avantajı olan müşteriler bir SQL Server VM [Azure hibrit avantajı (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) etkinleştirerek kendi lisansını kullanabilir. 

1. **VM kaynağı taşındığında veya bırakıldığında kaynak sağlayıcısı (_Microsoft. SqlVirtualMachine_) kaynağına ne olur?** 

   Microsoft. COMPUTE/VirtualMachine kaynağı bırakılır veya taşındığında, ilişkili Microsoft. SqlVirtualMachine kaynağına işlem zaman uyumsuz olarak çoğaltılır.

1. **Kaynak sağlayıcısı (_Microsoft. SqlVirtualMachine_) kaynağı bırakıldığında VM 'ye ne olur?**

    Microsoft. SqlVirtualMachine kaynağı bırakıldığında Microsoft. COMPUTE/VirtualMachine kaynağı etkilenmez. Ancak, lisans değişiklikleri varsayılan olarak orijinal görüntü kaynağına geri gönderilir. 

1. **SQL Server VM kaynak sağlayıcısı ile kendi kendine dağıtılan SQL Server VM 'Leri kaydetmek mümkün mü?**

    Evet. SQL Server kendi medyanınızdan dağıttıysanız ve SQL IaaS uzantısını yüklediyseniz, SQL IaaS uzantısının sağladığı yönetilebilirlik avantajlarından yararlanmak için SQL Server VM kaynak sağlayıcısına kaydedebilirsiniz. Ancak, kendinden dağıtılan bir SQL Server VM Kullandıkça Öde 'ye dönüştüremezsiniz.

1. **Klasik model kullanılarak dağıtılan bir SQL Server VM lisans modelini değiştirmek mümkün mü?**

   Hayır. Lisanslama modelinin değiştirilmesi, klasik bir sanal makinede desteklenmez. VM 'nizi Azure Resource Manager modeline geçirebilir ve SQL Server VM kaynak sağlayıcısına kaydedebilirsiniz. VM, SQL Server VM kaynak sağlayıcısına kaydedildikten sonra, VM 'de lisans modeli değişiklikleri kullanılabilir olacaktır. 
   


## <a name="administration"></a>Yönetim

1. **Aynı VM'ye SQL Server'ın ikinci örneğini yükleyebilir miyim? Varsayılan örneğin yüklü özelliklerini değiştirebilir miyim?**

   Evet. SQL Server yükleme medyası **C** sürücüsündeki bir klasörde yer alır. Yeni SQL Server örnekleri eklemek veya makinedeki SQL Server diğer yüklü özelliklerini değiştirmek için bu konumdan **Setup. exe** ' yi çalıştırın. Otomatik yedekleme, otomatik düzeltme eki uygulama ve Azure Key Vault tümleştirme gibi bazı özelliklerin yalnızca varsayılan örneğe veya düzgün yapılandırılmış adlandırılmış bir örneğe göre çalışacağını unutmayın (bkz. soru 3). 

1. **SQL Server'ın varsayılan örneğini kaldırabilir miyim?**

   Evet, ama bazı noktaları dikkate almalısınız. İlk olarak, SQL Server ilişkili faturalandırma VM 'nin lisans modeline bağlı olarak devam edebilir. İkincisi, önceki cevap ' de belirtildiği gibi, [SQL Server IaaS Aracısı uzantısına](virtual-machines-windows-sql-server-agent-extension.md)güvenen özellikler vardır. IaaS uzantısını kaldırmadan varsayılan örneği de kaldırırsanız, uzantı varsayılan örneği aramaya devam eder ve olay günlüğü hataları oluşturabilir. Bu hatalar şu iki kaynaktan gelir: **Microsoft SQL Server kimlik bilgileri yönetimi** ve **ıaas Aracısı Microsoft SQL Server**. Hatalardan biri aşağıdakine benzer olabilir:

      SQL Server ile bağlantı kurulmaya çalışılırken ağ ile ilişkili veya örneğe özgü bir hata oluştu. Sunucu bulunamadı veya erişilebilir değildi.

   Varsayılan örneği kaldırmaya karar verirseniz Ayrıca [SQL Server IaaS Aracısı uzantısını](virtual-machines-windows-sql-server-agent-extension.md) da kaldırın. 

1. **IaaS uzantılı SQL Server adlandırılmış bir örneğini kullanabilir miyim**?
   
   Evet, adlandırılmış örnek SQL Server tek örnek ise ve özgün varsayılan örnek [düzgün şekilde kaldırılmışsa](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Varsayılan örnek yoksa ve tek bir SQL Server VM birden çok adlandırılmış örnek varsa, SQL Server IaaS Aracısı uzantısı yüklenemeyecektir. 

1. **SQL Server VM SQL Server tamamen kaldırabilir miyim?**

   Evet, ancak [SQL Server Azure VM 'leri Için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md)' nda açıklandığı gibi SQL Server VM ücretlendirilmeye devam edersiniz. Artık SQL Server'a ihtiyacınız kalmadıysa yeni bir sanal makine dağıtabilir ve verilerle uygulamaları yeni sanal makineye geçirebilirsiniz. Ardından SQL Server sanal makinesini kaldırabilirsiniz.
   
## <a name="updating-and-patching"></a>Güncelleştirme ve düzeltme eki uygulama

1. **Azure sanal makinesinde SQL Server'ın farlı bir sürümüne/yayınına nasıl geçebilirim?**

   Müşteriler SQL Server'ın istenen sürümünü veya yayınını içeren kurulum medyasını kullanarak SQL Server sürümlerini/yayınlarını değiştirebilir. Yayın değiştikten sonra Azure portalını kullanarak VM için faturalamayı doğru yansıtacak şekilde VM'nin yayın özelliğini değiştirin. Daha fazla bilgi için bkz. [SQL Server VM sürümünü değiştirme](virtual-machines-windows-sql-change-edition.md). Farklı SQL Server sürümleri için faturalandırma farkı yoktur, bu nedenle SQL Server sürümü değiştirildikten sonra başka bir eylem gerekmez.

1. **SQL Server sürümünü veya sürümünü değiştirmek için kurulum medyasını nereden alabilirim?**

  [Yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) sahibi olan müşteriler, yükleme medyasını [toplu lisanslama merkezinden](https://www.microsoft.com/Licensing/servicecenter/default.aspx)elde edebilir. Yazılım güvencesi olmayan müşteriler, istenen sürümü olan bir market SQL Server VM görüntüsünden kurulum medyasını kullanabilir.

1. **Güncelleştirmeler ve hizmet paketleri bir SQL Server VM nasıl uygulanır?**

   Sanal makineler güncelleştirmeleri ne zaman ve nasıl uygulayacağınız dahil olmak üzere ana makine üzerinde denetim sağlar. İşletim sistemi için Windows güncelleştirmelerini el ile uygulayabilir veya [Otomatik Düzeltme](virtual-machines-windows-sql-automated-patching.md)için adlandırılan bir zamanlama hizmetini etkinleştirebilirsiniz. Otomatik Düzeltme Eki Uygulama, önemli olarak işaretlenmiş tüm güncelleştirmeleri, bu kategorideki SQL Server güncelleştirmeleri de dahil olmak üzere yükler. SQL Server için diğer isteğe bağlı güncelleştirmeler el ile yüklenmelidir.

1. **SQL Server 2008/2008 R2 örneğinden SQL Server VM kaynak sağlayıcısına kaydettikten sonra yükseltebilir miyim?**

   Evet. SQL Server sürümünü yükseltmek için herhangi bir kurulum medyası kullanabilir ve sonra [SQL IaaS uzantı modınızı](virtual-machines-windows-sql-register-with-resource-provider.md#change-management-modes) herhangi bir _aracıdan_ _tam_'e yükseltebilirsiniz. Bunun yapılması, Portal yönetilebilirliği, otomatik yedeklemeler ve otomatik düzeltme eki uygulama gibi SQL IaaS uzantısının tüm avantajlarına erişmenizi sağlayacaktır. 

## <a name="general"></a>Genel

1. **SQL Server yük devretme kümesi örneği (FCı) Azure VM 'lerinde destekleniyor mu?**

   Evet. [Windows Server 2016 ' de bir Windows Yük devretme kümesi oluşturabilir](virtual-machines-windows-portal-sql-create-failover-cluster.md) ve küme depolaması için depolama ALANLARı doğrudan (S2D) kullanabilirsiniz. Alternatif olarak, [Azure sanal makinelerinde SQL Server Için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)bölümünde açıklandığı gibi üçüncü taraf kümeleme veya depolama çözümlerini de kullanabilirsiniz.

   > [!IMPORTANT]
   > Şu anda _tam_ [SQL Server IaaS Aracısı uzantısı](virtual-machines-windows-sql-server-agent-extension.md) , Azure 'da SQL Server FCI için desteklenmez. FCı 'ye katılan VM 'lerden _tam_ uzantıyı kaldırmanızı ve bunun yerine eklentiyi _hafif_ modda yüklemenizi öneririz. Bu uzantı, SQL Server için otomatik yedekleme ve düzeltme eki uygulama ve bazı Portal özellikleri gibi özellikleri destekler. Bu özellikler, _tam_ aracı kaldırıldıktan sonra SQL Server VM 'ler için çalışmayacaktır.

1. **SQL Server VM 'Ler ile SQL veritabanı hizmeti arasındaki fark nedir?**

   Kavramsal olarak, Azure sanal makinesinde SQL Server çalıştırmak, uzak bir veri merkezinde SQL Server çalıştırmanın farklı değildir. Buna karşılık, [SQL veritabanı](../../../sql-database/sql-database-technical-overview.md) hizmet olarak veritabanı sağlar. SQL veritabanı ile veritabanlarınızı barındıran makinelere erişiminiz yok. Tam karşılaştırma için bkz [. bulut SQL Server seçin seçeneği: Azure SQL (PaaS) veritabanı veya Azure VM 'lerinde SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Azure VM 'imde SQL Data araçları 'nı Nasıl yaparım? yüklensin mi?**

    Microsoft SQL Server veri araçlarından SQL Data araçları 'nı indirip yükleyin. [Visual Studio 2013 Için Iş zekası](https://www.microsoft.com/download/details.aspx?id=42313).

1. **SQL Server VM 'lerde desteklenen MSDTC ile dağıtılmış işlemler mi var?**
   
    Evet. SQL Server 2016 SP2 ve üzeri için Yerel DTC desteklenir. Ancak, bir yük devretme sırasında hareket durumunda olan işlemler başarısız olur ve yeniden denenmek zorunda olduğundan uygulamalar her zaman açık kullanılabilirlik grupları kullanılırken test edilmiş olmalıdır. Kümelenmiş DTC, Windows Server 2019 ile başlayarak kullanılabilir. 

## <a name="resources"></a>Kaynaklar

**Windows VM 'leri**:

* [WINDOWS VM 'de SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md).
* [SQL Server Windows VM sağlama](virtual-machines-windows-portal-sql-server-provision.md)
* [Bir veritabanını Azure VM 'de SQL Server geçirme](virtual-machines-windows-migrate-sql.md)
* [Azure sanal makinelerinde SQL Server için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Sanal Makineler’de SQL Server için performansa yönelik en iyi uygulamalar](virtual-machines-windows-sql-performance.md)
* [Azure Sanal Makineler'de SQL Server için Uygulama Desenleri ve Geliştirme Stratejileri](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM 'leri**:

* [Linux VM 'de SQL Server genel bakış](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM sağlama](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [SSS (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux üzerinde SQL Server belgeleri](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
