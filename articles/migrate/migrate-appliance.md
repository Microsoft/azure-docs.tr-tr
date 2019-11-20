---
title: Azure geçiş gereç mimarisi | Microsoft Docs
description: Azure geçişi gerecine genel bakış sağlar
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 249cbea173afe1671118446e0714b721b8c7f72b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685102"
---
# <a name="azure-migrate-appliance"></a>Azure Geçişi gereci

Bu makalede, Azure geçişi gereci açıklanmaktadır. Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmek, değerlendirmek ve geçirmek üzere Azure geçişi değerlendirmesi ve geçiş araçlarını kullandığınızda gereci dağıtırsınız. 

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı, iş yüklerinizi ve özel/genel bulut VM 'lerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.



## <a name="appliance-overview"></a>Gereç genel bakışı

Azure geçiş gereç türleri ve kullanımı aşağıdaki gibidir.

**Farklı dağıtıldı** | **Kullanıldığı yer** | **Ayrıntılar**
--- | --- |  ---
VMware VM | Azure geçişi değerlendirme aracı ile VMware VM değerlendirmesi.<br/><br/> Azure geçişi sunucu geçiş aracı ile VMware VM aracısız geçişi | OVA şablonunu indirin ve gereç sanal makinesini oluşturmak için vCenter Server alın.
Hyper-V VM | Azure geçişi değerlendirme aracı ile Hyper-V VM değerlendirmesi. | Sıkıştırılmış VHD 'yi indirin ve gereç sanal makinesini oluşturmak için Hyper-V ' d e aktarın.

## <a name="appliance-access"></a>Gereç erişimi

Gereci yapılandırdıktan sonra, Gereç VM 'sine TCP bağlantı noktası 3389 üzerinden uzaktan erişebilirsiniz. Ayrıca, 44368 numaralı bağlantı noktası üzerinden gereç için Web yönetimi uygulamasına uzaktan erişim sağlayabilirsiniz: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Gereç lisansı
Gereç, 180 gün için geçerli olan bir Windows Server 2016 değerlendirme lisansıyla gelir. Değerlendirme süresi sona ermeden yakın ise, yeni bir gereç indirmeniz ve dağıtmanız ya da gereç sanal makinesinin işletim sistemi lisansını etkinleştirmenizi öneririz.

## <a name="appliance-agents"></a>Gereç aracıları
Gereç bu aracıların yüklü olduğunu.

**Aracı** | **Ayrıntılar**
--- | ---
Keşif Aracısı | Şirket içi sanal makinelerin yapılandırma verilerini toplar
Değerlendirme aracısı | VM performans verilerini toplamak için şirket içi ortamı profiller.
Geçiş bağdaştırıcısı | VM çoğaltmasını düzenleyin ve VM 'Ler ile Azure arasındaki iletişimi koordine edin.
Geçiş ağ geçidi | Çoğaltılan VM verilerini Azure 'a gönderir.


## <a name="appliance-deployment-requirements"></a>Gereç dağıtım gereksinimleri

- Bir VMware gereci ve gerecin erişmesi gereken URL 'Lerin dağıtım gereksinimlerini [gözden geçirin](migrate-support-matrix-vmware.md#assessment-appliance-requirements) .
- Hyper-V gereci ve gerecin erişmesi gereken URL 'Lerin dağıtım gereksinimlerini [gözden geçirin](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) .


## <a name="collected-performance-data-vmware"></a>Toplanan performans verileri-VMware

Bu, gerecin topladığı ve Azure 'a gönderdiği VMware VM performans verileri aşağıda verilmiştir.

**Veriler** | **Sayaç** | **Değerlendirme etkisi**
--- | --- | ---
CPU utilization | CPU. Usage. Average | Önerilen VM boyutu/maliyet
Bellek kullanımı | mem. kullanım. Ortalama | Önerilen VM boyutu/maliyet
Disk okuma üretilen işi (MB/saniye) | virtualDisk. Read. Average | Disk boyutu, depolama maliyeti, VM boyutu için hesaplama
Disk yazma işleme (MB/saniye) | virtualDisk. Write. Average | Disk boyutu, depolama maliyeti, VM boyutu için hesaplama
Saniye başına disk okuma işlemi | virtualDisk. Numberreadaveryaşlandırılmış. Average | Disk boyutu, depolama maliyeti, VM boyutu için hesaplama
Saniye başına disk yazma işlemi | virtualDisk. Numberwriteortalama. Ortalama  | Disk boyutu, depolama maliyeti, VM boyutu için hesaplama
NIC okuma üretilen işi (MB/saniye) | net. alınan. Ortalama | VM boyutu için hesaplama
NIC yazma üretimi (MB/saniye) | net. iletilmiş. Average  |VM boyutu için hesaplama


## <a name="collected-metadata-vmware"></a>Toplanan meta veriler-VMware

> [!NOTE]
> Azure geçişi gereci tarafından bulunan meta veriler, uygulamalarınızı Azure 'a geçirirken, Azure uygunluk analizi, uygulama bağımlılığı Analizi ve maliyet planlaması gerçekleştirerek uygulamalarınızı doğru boyuta getirmenize yardımcı olmak için kullanılır. Microsoft bu verileri, herhangi bir lisans uyumluluğu denetimine göre kullanmaz.

Bu, gerecin topladığı ve Azure 'a gönderdiği VMware VM meta verilerinin tam listesini aşağıda bulabilirsiniz.

**Veriler** | **Sayaç**
--- | --- 
**Makine ayrıntıları** | 
VM KIMLIĞI | 'nin. Config. ınstanceuuıd 
VM adı | 'nin. Config.Name
vCenter Server KIMLIĞI | VMwareClient. Instance. UUID
VM açıklaması | 'nin. Summary. config. Annotation
Lisans ürün adı | 'nin. Client. ServiceContent. about. LicenseProductName
İşletim sistemi türü | 'nin. SummaryConfig. GuestFullName
Önyükleme türü | 'nin. Config. bellenim
Çekirdek sayısı | 'nin. Config. Hardware. NumCPU
Bellek (MB) | 'nin. Config. Hardware. MemoryMB
Disk sayısı | 'nin. Config. Hardware. Device. ToList (). FindAll (x = > VirtualDisk). Count
Disk boyutu listesi | 'nin. Config. Hardware. Device. ToList (). FindAll (x = > VirtualDisk)
Ağ bağdaştırıcıları listesi | 'nin. Config. Hardware. Device. ToList (). FindAll (x = > Virtualalethernet). Count
CPU utilization | CPU. Usage. Average
Bellek kullanımı |mem. kullanım. Ortalama
**Disk başına Ayrıntılar** | 
Disk anahtarı değeri | dis. Anahtar
Dikunit numarası | dis. UnitNumber
Disk denetleyicisi anahtar değeri | dis. ControllerKey. Value
Gigabayt sağlandı | virtualDisk. DeviceInfo. Summary
Disk adı | Disk kullanılarak oluşturulan değer. UnitNumber, disk. Anahtar, disk. ControllerKey. VAlue
Saniye başına okuma işlemi | virtualDisk. Numberreadaveryaşlandırılmış. Average
Saniye başına yazma işlemi | virtualDisk. Numberwriteortalama. Ortalama
Aktarım hızını oku (MB/saniye) | virtualDisk. Read. Average
Yazma üretilen işi (MB/saniye) | virtualDisk. Write. Average
**NIC başına Ayrıntılar** | 
Ağ bağdaştırıcısı adı | 'i. Anahtar
MAC adresi | ((Virtualalether, NIC) NIC). MacAddress
IPv4 adresleri | 'nin. Guest.Net
IPv6 adresleri | 'nin. Guest.Net
Aktarım hızını oku (MB/saniye) | net. alınan. Ortalama
Yazma üretilen işi (MB/saniye) | net. iletilmiş. Average
**Envanter yolu ayrıntıları** | 
Ad | kapsayıcı. GetType (). Ada
Alt nesnenin türü | kapsayıcı. ChildType
Başvuru ayrıntıları | kapsayıcı. MoRef
Üst Ayrıntılar | Container. Parent
VM başına klasör ayrıntıları | ((Klasör) kapsayıcısı). ChildEntity. Type
VM başına veri merkezi ayrıntıları | (Datacenter) kapsayıcısı). VmFolder
Konak klasörü başına veri merkezi ayrıntıları | (Datacenter) kapsayıcısı). HostFolder
Konak başına küme ayrıntıları | ((ClusterComputeResource) kapsayıcısı). Konağının
VM başına ana bilgisayar ayrıntıları | (HostSystem) kapsayıcısı). 'Nın



## <a name="collected-performance-data-hyper-v"></a>Toplanan performans verileri-Hyper-V

> [!NOTE]
> Azure geçişi gereci tarafından bulunan meta veriler, uygulamalarınızı Azure 'a geçirirken, Azure uygunluk analizi, uygulama bağımlılığı Analizi ve maliyet planlaması gerçekleştirerek uygulamalarınızı doğru boyuta getirmenize yardımcı olmak için kullanılır. Microsoft bu verileri, herhangi bir lisans uyumluluğu denetimine göre kullanmaz.

Bu, gerecin topladığı ve Azure 'a gönderdiği Hyper VM performans verileri aşağıda verilmiştir.

**Performans sayacı sınıfı** | **Sayaç** | **Değerlendirme etkisi**
--- | --- | ---
Hyper-V hiper yönetici sanal Işlemcisi | % Konuk çalışma zamanı | Önerilen VM boyutu/maliyet
Hyper-V Dinamik Bellek VM | Geçerli basınç (%)<br/> Konuk görünür fiziksel bellek (MB) | Önerilen VM boyutu/maliyet
Hyper-V sanal depolama cihazı | Okunan bayt/saniye | Disk boyutu, depolama maliyeti, VM boyutu için hesaplama
Hyper-V sanal depolama cihazı | Yazılan bayt/saniye | Disk boyutu, depolama maliyeti, VM boyutu için hesaplama
Hyper-V sanal ağ bağdaştırıcısı | Alınan bayt/saniye | VM boyutu için hesaplama
Hyper-V sanal ağ bağdaştırıcısı | Gönderilen bayt/saniye | VM boyutu için hesaplama

- CPU kullanımı, bir VM 'ye bağlı tüm sanal işlemcilerin toplam kullanım toplamıdır.
- Bellek kullanımı (geçerli basınç * Konuk görünür fiziksel bellek)/100.
- Disk ve ağ kullanım değerleri, listelenen Hyper-V performans sayaçlarından toplanır.

## <a name="collected-metadata-hyper-v"></a>Toplanan meta veriler-Hyper-V

Bu, gerecin topladığı ve Azure 'a gönderdiği Hyper-V VM meta verilerinin tam listesidir.

**Veriler** | **WMI sınıfı** | **WMI sınıfı özelliği**
--- | --- | ---
**Makine ayrıntıları** | 
BIOS _ Msvm_BIOSElement seri numarası | Bıino SerialNumber
VM türü (Gen 1 veya 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM görünen adı | Msvm_VirtualSystemSettingData | ElementName
VM sürümü | Msvm_ProcessorSettingData | VirtualQuantity
Bellek (bayt) | Msvm_MemorySettingData | VirtualQuantity
VM tarafından tüketilen maksimum bellek | Msvm_MemorySettingData | Sınır
Dinamik bellek etkin | Msvm_MemorySettingData | DynamicMemoryEnabled
İşletim sistemi adı/sürümü/FQDN | Msvm_KvpExchangeComponent | Guestıntrinsicexchangeıtems adı verileri
VM güç durumu | Msvm_ComputerSystem | EnabledState
**Disk başına Ayrıntılar** | 
Disk tanımlayıcısı | Msvm_VirtualHardDiskSettingData | Virtualdiskıd
Sanal sabit disk türü | Msvm_VirtualHardDiskSettingData | Tür
Sanal sabit disk boyutu | Msvm_VirtualHardDiskSettingData | Maxınternalsize
Sanal sabit disk üst öğesi | Msvm_VirtualHardDiskSettingData | ParentPath
**NIC başına Ayrıntılar** | 
IP adresleri (yapay NIC 'ler) | Msvm_GuestNetworkAdapterConfiguration | IpAdresleri
DHCP etkin (yapay NIC 'ler) | Msvm_GuestNetworkAdapterConfiguration | DHCPEtkin
NIC KIMLIĞI (yapay NIC 'ler) | Msvm_SyntheticEthernetPortSettingData | InstanceId
NIC MAC adresi (yapay NIC 'ler) | Msvm_SyntheticEthernetPortSettingData | Adres
NIC KIMLIĞI (eski NIC 'ler) | MsvmEmulatedEthernetPortSetting verileri | InstanceId
NIC MAC KIMLIĞI (eski NIC 'ler) | MsvmEmulatedEthernetPortSetting verileri | Adres




## <a name="discovery-and-collection-process"></a>Bulma ve toplama işlemi

Gereç, aşağıdaki işlemi kullanarak vCenter sunucularıyla ve Hyper-V konaklarıyla/kümesiyle iletişim kurar.


1. **Bulmayı Başlat**:
    - Hyper-V gereci üzerinde bulmayı başlattığınızda, WinRM bağlantı noktaları 5985 (HTTP) ve 5986 (HTTPS) üzerindeki Hyper-V konaklarıyla iletişim kurar.
    - VMware gereci üzerinde bulmayı başlattığınızda, varsayılan olarak TCP bağlantı noktası 443 üzerindeki vCenter Server ile iletişim kurar. VCenter sunucusu farklı bir bağlantı noktasını dinliyorsa, bunu gereç Web uygulamasında yapılandırabilirsiniz.
2. **Meta verileri ve performans verilerini toplayın**:
    - Gereç, 5985 ve 5986 bağlantı noktalarında Hyper-V konağı üzerinden Hyper-V VM verilerini toplamak için bir Genel Bilgi Modeli (CıM) oturumu kullanır.
    - Gereç, vCenter Server VMware VM verilerini toplamak için varsayılan olarak bağlantı noktası 443 ile iletişim kurar.
3. **Veri Gönder**: gereç, toplanan verileri Azure geçişi sunucu değerlendirmesini ve Azure geçişi sunucu geçişini SSL bağlantı noktası 443 üzerinden gönderir.
    - Performans verileri için, Gereç gerçek zamanlı kullanım verilerini toplar.
        - Performans verileri her bir performans ölçümü için VMware için 20 saniyede bir ve Hyper-V için her 30 saniyede bir toplanır.
        - Toplanan veriler on dakika boyunca tek bir veri noktası oluşturmak için toplanır.
        - En yüksek kullanım değeri, 20/30 saniyelik tüm veri noktalarından seçilir ve değerlendirme hesaplaması için Azure 'a gönderilir.
        - Değerlendirme özelliklerinde belirtilen yüzdebirlik değerine (50./90./yüzde/sn) göre, on dakikalık noktaları artan düzende sıralanır ve değerlendirmeyi hesaplamak için uygun yüzdebirlik değeri kullanılır
    - Sunucu geçişi için, Gereç VM verilerini toplamaya başlar ve bunu Azure 'a çoğaltır.
4. **Değerlendirin ve geçirin**: artık Azure geçişi sunucu değerlendirmesini kullanarak gereç tarafından toplanan meta verilerden değerlendirmeler oluşturabilirsiniz. Ayrıca, Azure geçişi sunucu geçişini kullanarak VMware VM 'Leri geçirmeyi daha az VM çoğaltmasını düzenlemek için de başlatabilirsiniz.


![Mimari](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Gereç yükseltmeleri

Gereç üzerinde çalışan Azure geçiş aracıları güncelleştirildiğinden, Gereç yükseltilir.

- Otomatik güncelleştirme gereç üzerinde varsayılan olarak etkin olduğundan bu otomatik olarak gerçekleşir.
- Aracıları el ile güncelleştirmek için bu varsayılan ayarı değiştirebilirsiniz.
- Otomatik güncelleştirmeyi devre dışı bırakmak için, HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance > kayıt defteri düzenleyicisine gidin ve kayıt defteri anahtarını-"otomatik güncelleştirme" (DWORD) olarak ayarlayın.
 
### <a name="set-agent-updates-to-manual"></a>Aracı güncelleştirmelerini el ile olarak ayarla

El ile güncelleştirmeler için, Gereç üzerindeki tüm olmayan aracıların **Güncelleştir** düğmesini kullanarak, aracıdaki tüm aracıları aynı anda güncelleştirdiğinizden emin olun. Güncelleştirme ayarını dilediğiniz zaman yeniden otomatik güncelleştirmelere geçirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

VMware için gereci ayarlamayı [öğrenin](tutorial-assess-vmware.md#set-up-the-appliance-vm) .
Hyper-V için gereci ayarlamayı [öğrenin](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) .

