---
title: "Windows Sanal Makinelere Genel Bakış | Microsoft Belgeleri"
description: "Azure’da Windows sanal makineler oluşturma ve yönetme hakkında bilgi edinin."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d128d1f5bdf054accac87631a5d6a65f3bb15ea6
ms.lasthandoff: 04/03/2017


---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Azure’da Windows sanal makinelere genel bakış
Azure Virtual Machines (VM) Azure’un sunduğu [isteğe bağlı ve ölçeklenebilir işlem kaynağı türlerinden](../../app-service-web/choose-web-site-cloud-service-vm.md) biridir. Genellikle, sunulan diğer seçimlere göre bilgi işlem ortamınız üzerinde daha fazla denetime ihtiyacınız varsa, bir VM seçersiniz. Bu makalede VM oluşturmadan önce dikkat etmeniz gereken bilgilere, oluşturma yöntemine ve yönetim seçeneklerine yer verilmiştir.

Bir Azure VM, onu çalıştıran fiziksel donanımı satın almanıza ve muhafaza etmenize gerek kalmadan size sanallaştırma esnekliği sunar. Ancak yine de yapılandırma, düzeltme eki uygulama ve yazılım yükleme gibi görevleri gerçekleştirerek VM’nin bakımını yapmanız gerekir.

Azure sanal makineleri farklı amaçlarla kullanılabilir. Bazı örnekler şunlardır:

* **Geliştirme ve test etme** – Azure VM’leri bir uygulama için kod yazmak ve test yapmak için özel yapılandırmalara sahip bilgisayarları hızlıca oluşturmanızı sağlar.
* **Uygulamaları buluta taşıma** – Talep düzeyinde dalgalanmalar olan uygulamalarınızı Azure üzerindeki bir VM’de çalıştırmak mantıklıdır. İhtiyaç duyduğunuzda oluşturulan ek VM’ler için ödeme yapar, ihtiyaç kalmadığında bunları kapatırsınız.
* **Veri merkezini genişletme** – Bir Azure sanal ağı üzerindeki sanal makineleri kolayca kuruluşunuzun ağına bağlayabilirsiniz.

Uygulamanız tarafından kullanılan VM sayısı, ihtiyaçlarınıza göre ölçeklendirilebilir.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM oluşturmadan önce dikkat etmem gereken noktalar nelerdir?
Azure’da uygulama altyapısı oluştururken [dikkate almanız gereken tasarım ölçütleri](infrastructure-virtual-machine-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vardır. Başlamadan önce dikkat etmeniz gereken VM özellikleri şunlardır:

* Uygulama kaynaklarınızın adları
* Kaynakların depolanacağı konum
* VM’nin boyutu
* Oluşturulabilecek en fazla VM sayısı
* VM üzerinde çalışan işletim sistemi
* VM’nin başlatıldıktan sonraki yapılandırması
* VM’nin ihtiyaç duyduğu kaynaklar

### <a name="naming"></a>Adlandırma
Bir sanal makine, kendisine verilen [ada](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ek olarak işletim sisteminin bir parçası olarak atanan bilgisayar adına sahiptir. VM adı en fazla 15 karakter uzunluğunda olabilir.

Azure’u işletim sistemi diski oluşturmak için kullanıyorsanız, bilgisayar adı ve sanal makine adı aynı olur. Önceden yapılandırılmış bir işletim sistemini içeren [görüntüyü yükleyip kullanarak](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bir sanal makine oluşturmanız halinde adlar farklı olabilir. Kendi görüntü dosyanızı yüklediğinizde, işletim sistemindeki bilgisayar adıyla sanal makine adını aynı yapmanız önerilir.

### <a name="locations"></a>Konumlar
Azure’da oluşturulan tüm kaynaklar, dünyanın farklı yerindeki çeşitli [coğrafi bölgelere](https://azure.microsoft.com/regions/) dağıtılır. VM oluştururken bu bölgeler genelde **konum** olarak adlandırılır. Bir VM için konum, sanal sabit disklerin depolandığı yeri belirtir.

Bu tabloda, kullanılabilen konumların listesini edinme yöntemlerinden bazıları gösterilmektedir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portal |VM oluştururken listeden konum seçin. |
| Azure PowerShell |[Get-AzureRmLocation](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/get-azurermlocation) komutunu kullanın. |
| REST API |[List locations](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations) işlemini kullanın. |

### <a name="vm-size"></a>VM boyutu
Kullandığınız VM’nin [boyutu](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), çalıştırmak istediğiniz iş yüküne göre belirlenir. Seçtiğiniz boyut işlemci gücü, bellek ve depolama kapasitesi gibi ölçütleri belirler. Azure çok sayıda kullanım türünü desteklemek için büyük çeşitlilikteki boyutları sunar.

Azure’un ücretlendirdiği, VM’nin boyutu ve işletim sistemi temelinde [saatlik fiyat](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Kısmi saatler için, Azure yalnızca kullanılan dakikaları ücretlendirir. Depolama ayrı olarak fiyatlandırılır ve ücretlendirilir.

### <a name="vm-limits"></a>VM Sınırları
Aboneliğinizde, projeniz için birden fazla VM dağıtımını etkileyebilecek varsayılan [kota sınırları](../../azure-subscription-service-limits.md) vardır. Geçerli sınırlar abonelik başına her bölge için 20 VM olarak belirlenmiştir. Sınırların yükseltilmesini talep etmek için destek bileti oluşturabilirsiniz.

### <a name="operating-system-disks-and-images"></a>İşletim sistemi diskleri ve görüntüleri
Sanal makineler, kendi işletim sistemlerini (OS) ve verilerini depolamak için [sanal sabit diskleri (VHD)](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kullanır. VHD bir işletim sistemi yüklemek için seçebileceğiniz görüntüler için de kullanılır. 

Azure’da Windows Server işletim sistemlerinin farklı sürümleri ve türleri ile birlikte kullanılabilecek birçok [market görüntüsü](https://azure.microsoft.com/marketplace/virtual-machines/) bulunmaktadır. Market görüntüleri; görüntü yayımcısı, teklif, sku ve sürüm (genelde sürüm en son belirtilir) bilgileriyle tanımlanır. 

Bu tabloda bir görüntünün bilgilerine nasıl erişebileceğiniz gösterilmiştir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portal |Bir görüntüyü kullanmak istediğinizde değerler otomatik olarak belirtilir. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher) -Location "konum"<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer) -Location "konum" -Publisher "yayımcıAdı"<BR>[Get-AzureRMVMImageSku](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagesku) -Location "konum" -Publisher "yayımcıAdı" -Offer "teklifAdı" |
| REST API'leri |[Görüntü yayımcılarını listeleme](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Görüntü tekliflerini listeleme](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Görüntü sku’larını listeleme](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |

[Kendi görüntünüzü yükleyip kullanmanız halinde](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) yayımcı adı, teklif ve sku kullanılmaz.

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
| [Kaynak grubu](../../azure-resource-manager/resource-group-overview.md) |Evet |VM bir kaynak grubunda yer almalıdır. |
| [Depolama hesabı](../../storage/storage-create-storage-account.md) |Yes |VM, sanal sabit disklerini depolamak için bir depolama hesabına ihtiyaç duyar. |
| [Sanal ağ](../../virtual-network/virtual-networks-overview.md) |Yes |VM’in bir sanal ağa üye olması gerekir. |
| [Genel IP adresi](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Hayır |VM, uzaktan erişim için atanmış bir genel IP adresine sahip olabilir. |
| [Ağ arabirimi](../../virtual-network/virtual-network-network-interface.md) |Evet |VM’in ağda iletişim kurabilmek için ağ arabirimine ihtiyacı vardır. |
| [Veri diskleri](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Hayır |VM, depolama olanaklarını genişletmek için veri disklerine sahip olabilir. |

## <a name="how-do-i-create-my-first-vm"></a>İlk VM’mi nasıl oluşturabilirim?
İlk VM’nizi oluşturmak için kullanabileceğiniz birden fazla yöntem vardır. Seçeceğiniz yöntem, bulunduğunuz ortama bağlıdır. 

Bu tabloda VM’nizi oluşturmak için ihtiyaç duyacağınız giriş bilgileri yer almaktadır.

| Yöntem | Makale |
| --- | --- |
| Azure portal |[Portalı kullanarak Windows çalıştıran sanal makine oluşturma](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Şablonlar |[Resource Manager şablonu kullanarak Windows sanal makine oluşturma](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[PowerShell kullanarak Windows VM oluşturma](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| İstemci SDK'ları |[C# kullanarak Azure Kaynaklarını dağıtma](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API'leri |[VM oluşturma veya güncelleştirme](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |

Hiç istemeyiz ama bazen ters giden bir şeyler olabilir. Bu gibi durumlarda [Azure’da Windows sanal makine oluşturmayla ilgili Resource Manager dağıtım sorunlarını giderme](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sayfasına bakın.

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Oluşturduğum VM’yi nasıl yönetebilirim?
VM’ler tarayıcı tabanlı bir portal, betik oluşturma desteğine sahip komut satırı araçları kullanılarak veya doğrudan API’ler aracılığıyla yönetilebilir. Gerçekleştirmek isteyebileceğiniz genel yönetim görevlerinden bazıları VM hakkında bilgi alma, VM’de oturum açma, kullanılabilirlik durumunu yönetme ve yedekleme yapmadır.

### <a name="get-information-about-a-vm"></a>VM hakkında bilgi alma
Bu tabloda VM hakkında bilgi almak için kullanabileceğiniz yöntemlerden bazıları gösterilmektedir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portal |Hub menüsünde **Sanal Makineler**’e tıklayıp açılan listeden VM’yi seçin. VM’nin dikey penceresinden özet bilgilere, ayar değerlerine ve izleme ölçümlerine erişebilirsiniz. |
| Azure PowerShell |VM yönetimi için PowerShell kullanma hakkında daha fazla bilgi için bkz. [Azure Sanal Makinelerini Resource Manager ve PowerShell ile yönetme](ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| REST API |Bir VM hakkında bilgi almak için [VM bilgilerini alma](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) işlemini kullanın. |
| İstemci SDK'ları |VM yönetimi için C# kullanımı hakkında bilgi almak için bkz. [Azure Sanal Makinelerini Azure Resource Manager ve C# ile yönetme](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |

### <a name="log-on-to-the-vm"></a>VM’de oturum açma
Azure portalındaki Bağlan düğmesini kullanarak [Uzak Masaüstü (RDP) oturumu başlatabilirsiniz](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Uzak bağlantı özelliğini kullanmaya çalışırken hatalarla karşılaşabilirsiniz. Bu durumda [Windows çalıştıran bir Azure sanal makinesine yapılan Uzak Masaüstü bağlantılarında sorun giderme](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sayfasındaki yardım bilgilerini inceleyin.

### <a name="manage-availability"></a>Kullanılabilirliği yönetme
Uygulamanız için [yüksek kullanılabilirlik düzeyini](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nasıl sağlayacağınızı kavramanız önemlidir. Bu yapılandırma, en az birinin çalıştığından emin olmak için birden fazla VM oluşturmayı kapsamaktadır.

Dağıtımınızın 99,95 VM Hizmet Düzeyi Sözleşmesi kapsamına girebilmesi için iş yükünüzü çalıştıran iki veya daha fazla VM’yi bir [kullanılabilirlik kümesi](infrastructure-availability-sets-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) içinde dağıtmanız gerekir. Bu yapılandırma, VM’lerinizin birden fazla hata etki alanına dağıtılmasını ve dağıtımlarının farklı bakım aralıklarına sahip ana bilgisayarlara yapılmasını sağlar. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

### <a name="back-up-the-vm"></a>VM’yi yedekleme
Verileri ve varlıkları hem Azure Backup hem de Azure Site Recovery hizmetlerinde korumak için [Kurtarma Hizmetleri kasası](../../backup/backup-introduction-to-azure-backup.md) kullanılır. Kurtarma Hizmetleri kasası sayesinde [PowerShell kullanılarak Resource Manager ile dağıtılmış VM’ler için yedekleme dağıtımı ve yönetimi gerçekleştirebilirsiniz](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Sonraki adımlar
* Linux VM’leri ile çalışmayı düşünüyorsanız bkz. [Azure ve Linux](../linux/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Altyapınızı kurma hakkında daha fazla bilgi almak için [Örnek Azure altyapısı gözden geçirme](infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sayfasını inceleyin.
* [Azure’da Windows VM çalıştırmaya yönelik En İyi Uygulamalar](guidance-compute-single-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sayfasını incelemeyi unutmayın.


