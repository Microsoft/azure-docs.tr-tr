---
title: Windows Sanal Makineleri genel bakış-Azure
description: Azure’da Windows sanal makineler oluşturma ve yönetme hakkında bilgi edinin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe62f67071b77c464d5b3b8649d16db597d9ab21
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033046"
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Azure’da Windows sanal makinelere genel bakış

Azure Virtual Machines (VM) Azure’un sunduğu [isteğe bağlı ve ölçeklenebilir işlem kaynağı türlerinden](/azure/architecture/guide/technology-choices/compute-decision-tree) biridir. Genellikle, sunulan diğer seçimlere göre bilgi işlem ortamınız üzerinde daha fazla denetime ihtiyacınız varsa, bir VM seçersiniz. Bu makalede VM oluşturmadan önce dikkat etmeniz gereken bilgilere, oluşturma yöntemine ve yönetim seçeneklerine yer verilmiştir.

Bir Azure VM, onu çalıştıran fiziksel donanımı satın almanıza ve muhafaza etmenize gerek kalmadan size sanallaştırma esnekliği sunar. Ancak yine de yapılandırma, düzeltme eki uygulama ve yazılım yükleme gibi görevleri gerçekleştirerek VM’nin bakımını yapmanız gerekir.

Azure sanal makineleri farklı amaçlarla kullanılabilir. Bazı örnekler şunlardır:

* **Geliştirme ve test etme** – Azure VM’leri bir uygulama için kod yazmak ve test yapmak için özel yapılandırmalara sahip bilgisayarları hızlıca oluşturmanızı sağlar.
* **Uygulamaları buluta taşıma** – Talep düzeyinde dalgalanmalar olan uygulamalarınızı Azure üzerindeki bir VM’de çalıştırmak mantıklıdır. İhtiyaç duyduğunuzda oluşturulan ek VM’ler için ödeme yapar, ihtiyaç kalmadığında bunları kapatırsınız.
* **Veri merkezini genişletme** – Bir Azure sanal ağı üzerindeki sanal makineleri kolayca kuruluşunuzun ağına bağlayabilirsiniz.

Uygulamanız tarafından kullanılan VM sayısı, ihtiyaçlarınıza göre ölçeklendirilebilir.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM oluşturmadan önce dikkat etmem gereken noktalar nelerdir?
Azure’da uygulama altyapısı oluştururken [dikkate almanız gereken tasarım ölçütleri](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vardır. Başlamadan önce dikkat etmeniz gereken VM özellikleri şunlardır:

* Uygulama kaynaklarınızın adları
* Kaynakların depolanacağı konum
* VM’nin boyutu
* Oluşturulabilecek en fazla VM sayısı
* VM üzerinde çalışan işletim sistemi
* VM’nin başlatıldıktan sonraki yapılandırması
* VM’nin ihtiyaç duyduğu kaynaklar

### <a name="naming"></a>Adlandırma
Bir sanal makine, kendisine verilen [ada](/azure/architecture/best-practices/resource-naming) ek olarak işletim sisteminin bir parçası olarak atanan bilgisayar adına sahiptir. VM adı en fazla 15 karakter uzunluğunda olabilir.

Azure’u işletim sistemi diski oluşturmak için kullanıyorsanız, bilgisayar adı ve sanal makine adı aynı olur. Önceden yapılandırılmış bir işletim sistemini içeren [görüntüyü yükleyip kullanarak](upload-generalized-managed.md) bir sanal makine oluşturmanız halinde adlar farklı olabilir. Kendi görüntü dosyanızı yüklediğinizde, işletim sistemindeki bilgisayar adıyla sanal makine adını aynı yapmanız önerilir.

### <a name="locations"></a>Konumlar
Azure’da oluşturulan tüm kaynaklar, dünyanın farklı yerindeki çeşitli [coğrafi bölgelere](https://azure.microsoft.com/regions/) dağıtılır. VM oluştururken bu bölgeler genelde **konum** olarak adlandırılır. Bir VM için konum, sanal sabit disklerin depolandığı yeri belirtir.

Bu tabloda, kullanılabilen konumların listesini edinme yöntemlerinden bazıları gösterilmektedir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portalında |VM oluştururken listeden konum seçin. |
| Azure PowerShell |[Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) komutunu kullanın. |
| REST API |[List locations](https://docs.microsoft.com/rest/api/resources/subscriptions) işlemini kullanın. |
| Azure CLI |[az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest) işlemini kullanın. |

### <a name="vm-size"></a>VM boyutu
Kullandığınız VM’nin [boyutu](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), çalıştırmak istediğiniz iş yüküne göre belirlenir. Seçtiğiniz boyut işlemci gücü, bellek ve depolama kapasitesi gibi ölçütleri belirler. Azure çok sayıda kullanım türünü desteklemek için büyük çeşitlilikteki boyutları sunar.

Azure’un ücretlendirdiği, VM’nin boyutu ve işletim sistemi temelinde [saatlik fiyat](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Kısmi saatler için, Azure yalnızca kullanılan dakikaları ücretlendirir. Depolama ayrı olarak fiyatlandırılır ve ücretlendirilir.

### <a name="vm-limits"></a>VM Sınırları
Aboneliğinizde, projeniz için birden fazla VM dağıtımını etkileyebilecek varsayılan [kota sınırları](../../azure-subscription-service-limits.md) vardır. Geçerli sınırlar abonelik başına her bölge için 20 VM olarak belirlenmiştir. Sınırların [yükseltilmesini talep etmek için destek bileti oluşturabilirsiniz](../../azure-supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>İşletim sistemi diskleri ve görüntüleri
Sanal makineler, kendi işletim sistemlerini (OS) ve verilerini depolamak için [sanal sabit diskleri (VHD)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kullanır. VHD bir işletim sistemi yüklemek için seçebileceğiniz görüntüler için de kullanılır. 

Azure’da Windows Server işletim sistemlerinin farklı sürümleri ve türleri ile birlikte kullanılabilecek birçok [market görüntüsü](https://azure.microsoft.com/marketplace/virtual-machines/) bulunmaktadır. Market görüntüleri; görüntü yayımcısı, teklif, sku ve sürüm (genelde sürüm en son belirtilir) bilgileriyle tanımlanır. Yalnızca 64 bit işletim sistemleri desteklenir. Desteklenen konuk işletim sistemleri, roller ve özellikler hakkında daha fazla bilgi için bkz. [Microsoft Azure sanal makineleri için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

Bu tabloda bir görüntünün bilgilerine nasıl erişebileceğiniz gösterilmiştir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portalında |Bir görüntüyü kullanmak istediğinizde değerler otomatik olarak belirtilir. |
| Azure PowerShell |[Get-Azvmımagepublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) -Location *konumu*<BR>[Get-Azvmımageteklifinin](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) -Location *konumu* -yayımcı *PublisherName*<BR>[Get-Azvmımagesku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) -Location *konumu* -yayımcı *PublisherName* -bir *offername* sunma |
| REST API'leri |[Görüntü yayımcılarını listeleme](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Görüntü tekliflerini listeleme](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Görüntü sku’larını listeleme](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *konum*<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *konum* --publisher *yayımcıAdı*<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest) --location *konum* --publisher *yayımcıAdı* --offer *teklifAdı*|

[Kendi görüntünüzü yükleyip kullanmanız halinde](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account) yayımcı adı, teklif ve sku kullanılmaz.

### <a name="extensions"></a>Uzantılar
VM [uzantıları](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), dağıtım sonrası yapılandırma ve otomatik görevlerle VM’nize yeni özellikler ekler.

Uzantıları kullanarak şu genel görevleri gerçekleştirebilirsiniz:

* **Özel betik çalıştırma** – [Özel Betik Uzantısı](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), VM hazırlandığında kendi betiğinizi çalıştırarak iş yükü yapılandırması gerçekleştirmenize yardımcı olur.
* **Yapılandırma dağıtma ve yönetme** – [PowerShell İstenen Durum Yapılandırması (DSC) Uzantısı](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), yapılandırma ve ortam yönetimi amacıyla bir VM üzerinde DSC kurulumu yapmanıza yardımcı olur.
* **Tanılama verilerini toplama** – [Azure Tanılama Uzantısı](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), VM’i uygulama durumunu izlemek için kullanılabilecek tanılama verilerini toplayacak şekilde yapılandırmanıza yardımcı olur.

### <a name="related-resources"></a>İlgili kaynaklar
Bu tablodaki kaynaklar VM tarafından kullanılır ve VM oluşturulduğunda mevcut olmaları ya da oluşturulmaları gerekir.

| Kaynak | Gerekli | Açıklama |
| --- | --- | --- |
| [Kaynak grubu](../../azure-resource-manager/resource-group-overview.md) |Yes |VM bir kaynak grubunda yer almalıdır. |
| [Depolama hesabı](../../storage/common/storage-create-storage-account.md) |Yes |VM, sanal sabit disklerini depolamak için bir depolama hesabına ihtiyaç duyar. |
| [Sanal ağ](../../virtual-network/virtual-networks-overview.md) |Yes |VM’in bir sanal ağa üye olması gerekir. |
| [Genel IP adresi](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Hayır |VM, uzaktan erişim için atanmış bir genel IP adresine sahip olabilir. |
| [Ağ arabirimi](../../virtual-network/virtual-network-network-interface.md) |Yes |VM’in ağda iletişim kurabilmek için ağ arabirimine ihtiyacı vardır. |
| [Veri diskleri](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Hayır |VM, depolama olanaklarını genişletmek için veri disklerine sahip olabilir. |

## <a name="how-do-i-create-my-first-vm"></a>İlk VM’mi nasıl oluşturabilirim?
İlk VM’nizi oluşturmak için kullanabileceğiniz birden fazla yöntem vardır. Seçeceğiniz yöntem, bulunduğunuz ortama bağlıdır. 

Bu tabloda VM’nizi oluşturmak için ihtiyaç duyacağınız giriş bilgileri yer almaktadır.

| Yöntem | Makale |
| --- | --- |
| Azure portalında |[Portalı kullanarak Windows çalıştıran sanal makine oluşturma](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Şablonlar |[Resource Manager şablonu kullanarak Windows sanal makine oluşturma](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[PowerShell kullanarak Windows VM oluşturma](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| İstemci SDK'ları |[C# kullanarak Azure Kaynaklarını dağıtma](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API'leri |[VM oluşturma veya güncelleştirme](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Azure CLI |[Azure CLI ile VM oluşturma](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

Hiç istemeyiz ama bazen ters giden bir şeyler olabilir. Bu gibi durumlarda [Azure’da Windows sanal makine oluşturmayla ilgili Resource Manager dağıtım sorunlarını giderme](../troubleshooting/troubleshoot-deployment-new-vm-windows.md) sayfasına bakın.

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Oluşturduğum VM’yi nasıl yönetebilirim?
VM’ler tarayıcı tabanlı bir portal, betik oluşturma desteğine sahip komut satırı araçları kullanılarak veya doğrudan API’ler aracılığıyla yönetilebilir. Gerçekleştirmek isteyebileceğiniz genel yönetim görevlerinden bazıları VM hakkında bilgi alma, VM’de oturum açma, kullanılabilirlik durumunu yönetme ve yedekleme yapmadır.

### <a name="get-information-about-a-vm"></a>VM hakkında bilgi alma
Bu tabloda VM hakkında bilgi almak için kullanabileceğiniz yöntemlerden bazıları gösterilmektedir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portalında |Hub menüsünde **Sanal Makineler**’e tıklayıp açılan listeden VM’yi seçin. VM’nin dikey penceresinden özet bilgilere, ayar değerlerine ve izleme ölçümlerine erişebilirsiniz. |
| Azure PowerShell |VM'leri yönetmek üzere PowerShell'i kullanma hakkında daha fazla bilgi için bkz. [Azure PowerShell modülü ile Windows VM'leri oluşturma ve yönetme](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| REST API |Bir VM hakkında bilgi almak için [VM bilgilerini alma](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) işlemini kullanın. |
| İstemci SDK'ları |VM yönetimi için C# kullanımı hakkında bilgi almak için bkz. [Azure Sanal Makinelerini Azure Resource Manager ve C# ile yönetme](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Azure CLI |VM’leri yönetmek için Azure CLI kullanmayla ilgili daha fazla bilgi için bkz. [Azure CLI Başvurusu](https://docs.microsoft.com/cli/azure/vm). |

### <a name="log-on-to-the-vm"></a>VM’de oturum açma
Azure portalındaki Bağlan düğmesini kullanarak [Uzak Masaüstü (RDP) oturumu başlatabilirsiniz](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Uzak bağlantı özelliğini kullanmaya çalışırken hatalarla karşılaşabilirsiniz. Bu durumda [Windows çalıştıran bir Azure sanal makinesine yapılan Uzak Masaüstü bağlantılarında sorun giderme](../troubleshooting/troubleshoot-rdp-connection.md) sayfasındaki yardım bilgilerini inceleyin.

### <a name="manage-availability"></a>Kullanılabilirliği yönetme
Uygulamanız için [yüksek kullanılabilirlik düzeyini](manage-availability.md) nasıl sağlayacağınızı kavramanız önemlidir. Bu yapılandırma, en az birinin çalıştığından emin olmak için birden fazla VM oluşturmayı kapsamaktadır.

Dağıtımınızın 99,95 VM Hizmet Düzeyi Sözleşmesi kapsamına girebilmesi için iş yükünüzü çalıştıran iki veya daha fazla VM’yi bir [kullanılabilirlik kümesi](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) içinde dağıtmanız gerekir. Bu yapılandırma, VM’lerinizin birden fazla hata etki alanına dağıtılmasını ve dağıtımlarının farklı bakım aralıklarına sahip ana bilgisayarlara yapılmasını sağlar. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

### <a name="back-up-the-vm"></a>VM’yi yedekleme
Verileri ve varlıkları hem Azure Backup hem de Azure Site Recovery hizmetlerinde korumak için [Kurtarma Hizmetleri kasası](../../backup/backup-introduction-to-azure-backup.md) kullanılır. Kurtarma Hizmetleri kasası sayesinde [PowerShell kullanılarak Resource Manager ile dağıtılmış VM’ler için yedekleme dağıtımı ve yönetimi gerçekleştirebilirsiniz](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Sonraki adımlar
* Linux VM’leri ile çalışmayı düşünüyorsanız bkz. [Azure ve Linux](../linux/overview.md).
* Altyapınızı kurma hakkında daha fazla bilgi almak için [Örnek Azure altyapısı gözden geçirme](infrastructure-example.md) sayfasını inceleyin.
