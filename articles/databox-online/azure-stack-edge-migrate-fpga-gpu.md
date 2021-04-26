---
title: Azure Stack Edge Pro FPGA 'nin GPU fiziksel cihazına geçiş kılavuzu
description: Bu kılavuz, Azure Stack Edge Pro FPGA cihazındaki iş yüklerini bir Azure Stack Edge Pro GPU cihazına geçirmeye yönelik yönergeler içerir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 430e34a1ca631be00ef46170affd4b56c79894a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566413"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro FPGA 'dan iş yüklerini Azure Stack Edge Pro GPU 'suna geçirme

Bu makalede, Azure Stack Edge Pro FPGA cihazındaki iş yüklerinin ve verilerin bir Azure Stack Edge Pro GPU cihazına nasıl geçirileceği açıklanır. Geçiş işlemi, iki cihazın karşılaştırması, bir geçiş planı ve geçiş konularının incelenmesi ile başlar. Geçiş yordamı, doğrulama ve cihaz Temizleme ile biten ayrıntılı adımları sağlar.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>Geçiş hakkında

Geçiş, iş yüklerini ve uygulama verilerini bir depolama konumundan diğerine taşıma işlemidir. Bu, bir kuruluşun geçerli verilerinin bir depolama cihazından başka bir depolama cihazına (tercihen etkin uygulamaları kesintiye uğratmadan veya devre dışı bırakmadan) ve ardından tüm giriş/çıkış (g/ç) etkinliğini yeni cihaza yeniden yönlendirmeksizin tam bir kopyasını oluşturmak gerektirir. 

Bu geçiş kılavuzu, Azure Stack Edge Pro FPGA cihazından Azure Stack Edge Pro GPU cihazına veri geçirmek için gereken adımlara adım adım yönergeler sağlar. Bu belge, veri merkezindeki Azure Stack Edge cihazlarını çalıştırma, dağıtma ve yönetme konusunda sorumlu olan bilgi teknolojisi (BT) uzmanlarına ve bilgi çalışanlarına yöneliktir.

Bu makalede, Azure Stack Edge Pro FPGA cihazı *kaynak* cihaz olarak adlandırılır ve *hedef* cihaz, Azure Stack Edge Pro GPU aygıtıdır. 

## <a name="comparison-summary"></a>Karşılaştırma Özeti

Bu bölüm, Azure Stack Edge Pro GPU 'SU ile Azure Stack Edge Pro FPGA cihazları arasındaki yeteneklerin karşılaştırılma özetini sağlar. Hem kaynak hem de hedef cihazdaki donanım büyük ölçüde aynıdır; yalnızca donanım hızlandırma kartı ve depolama kapasitesi farklı olabilir.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Özellik  | Azure Stack Edge Pro GPU (hedef cihaz)  | Azure Stack Edge Pro FPGA (kaynak cihaz)|
|----------------|-----------------------|------------------------|
| Donanım       | Donanım hızlandırma: 1 veya 2 NVIDIA T4 GPU 'ları <br> İşlem, bellek, ağ arabirimi, güç kaynağı birimi ve güç kablosu belirtimleri, cihazla FPGA ile aynıdır.  | Donanım hızlandırma: Intel varış a 10 FPGA <br> İşlem, bellek, ağ arabirimi, güç kaynağı birimi ve güç kablosu belirtimleri, cihaz ile GPU ile aynıdır.          |
| Kullanılabilir depolama alanı | 4,19 TB <br> Eşlik dayanıklılığı ve dahili kullanım için alan ayırdıktan sonra | 12,5 TB <br> Dahili kullanım için alan ayırdıktan sonra |
| Güvenlik       | Sertifikalar |                                                     |
| İş yükleri      | IoT Edge iş yükleri <br> VM iş yükleri <br> Kubernetes iş yükleri| IoT Edge iş yükleri |
| Fiyatlandırma        | [Fiyatlandırma](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Fiyatlandırma](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Geçiş planı

Geçiş planınızı oluşturmak için aşağıdaki bilgileri göz önünde bulundurun:

- Geçiş için bir zamanlama geliştirin. 
- Verileri geçirdiğinizde kesinti yaşayabilirsiniz. İşlem kesintiye uğratan bir kesinti süresi bakım penceresi sırasında geçişi zamanlamanızı öneririz. Bu belgede daha sonra açıklandığı şekilde, bu kapalı kalma süresi içinde yapılandırmaların kurulumunu ve geri yüklemesini öğreneceksiniz.
- Toplam kesinti süresini anlayın ve tüm paydaşlarla iletişim kurun.
- Kaynak cihazdan geçirilmesi gereken yerel verileri belirler. Bir önlem olarak, var olan depolama üzerindeki tüm verilerin son yedeklemesi olduğundan emin olun. 


## <a name="migration-considerations"></a>Geçiş fikirleri 

Geçişe devam etmeden önce aşağıdaki bilgileri göz önünde bulundurun: 

- Bir Azure Stack Edge Pro GPU cihazı, bir Azure Stack Edge Pro FPGA kaynağına karşı etkinleştirilemez. Azure Stack Edge Pro GPU cihazı için [Azure Stack Edge Pro GPU siparişi oluşturma](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)bölümünde açıklandığı gibi yeni bir kaynak oluşturmanız gerekir.
- FPGA kullanan kaynak cihazda dağıtılan Machine Learning modellerinin GPU ile hedef cihaz için değiştirilmesi gerekir. Modellerle ilgili yardım için Microsoft Desteği başvurabilirsiniz. Kaynak cihaza dağıtılan özel modeller, FPGA kullanmayan (yalnızca kullanılan CPU), hedef cihazda (CPU kullanılarak) olduğu gibi çalışır.
- Kaynak cihaza dağıtılan IoT Edge modüller, hedef cihazda modüller başarıyla dağıtılmadan önce değişiklik gerektirebilir. 
- Kaynak cihaz, NFS 3,0 ve 4,1 protokollerini destekler. Hedef cihaz yalnızca NFS 3,0 protokolünü destekler.
- Kaynak cihaz SMB ve NFS protokollerini destekler. Hedef cihaz, paylaşımlar için SMB ve NFS protokollerine ek olarak depolama hesaplarını kullanan REST protokolü aracılığıyla depolamayı destekler.
- Kaynak aygıttaki paylaşma erişimi IP adresidir, ancak hedef cihazdaki paylaşma erişimi cihaz adı üzerinden yapılır.

## <a name="migration-steps-at-a-glance"></a>Bir bakışta geçiş adımları

Bu tablo, geçiş için gereken adımları ve bu adımların bulunacağı konumu açıklayan geçişin genel akışını özetler.

| Bu aşamada | Bu adımı yapın| Bu cihazda |
|---------------|-------------|----------------|
| Kaynak cihazı hazırla *       | 1. yapılandırma verilerini kaydetme <br>2. paylaşma verilerini yedekleme <br>3. IoT Edge iş yüklerini hazırlama| Kaynak cihaz  |
| Hedef cihazı hazırla *       |1. yeni bir sipariş oluşturun <br>2. Yapılandır ve Etkinleştir| Hedef cihaz  |
| Geçiş verileri       | 1. paylaşımların verilerini geçirin <br>2. IoT Edge iş yüklerini yeniden dağıtın| Hedef cihaz  |
| Verileri doğrulama            |Geçirilen verileri doğrulama |Hedef cihaz  |
| Temizle, geri dön              |Verileri silme ve döndürme| Kaynak cihaz  |

**Kaynak ve hedef cihazlar paralel olarak hazır olabilir.*

## <a name="prepare-source-device"></a>Kaynak cihazı hazırlama

Hazırlık, uç bulut paylaşımlarını, Edge Yerel paylaşımlarını ve cihazda dağıtılan IoT Edge modüllerini tanımlamanızı içerir. 

### <a name="1-record-configuration-data"></a>1. yapılandırma verilerini kaydetme

Bu adımları, yerel kullanıcı arabirimi aracılığıyla kaynak cihazınızda yapın.

Yapılandırma verilerini *kaynak* cihaza kaydedin. Cihaz yapılandırmasını kaydetmenize yardımcı olması için [dağıtım denetim listesini](azure-stack-edge-gpu-deploy-checklist.md) kullanın. Geçiş sırasında, yeni hedef cihazı yapılandırmak için bu yapılandırma bilgilerini kullanacaksınız. 

### <a name="2-back-up-share-data"></a>2. paylaşma verilerini yedekleme

Cihaz verileri aşağıdaki türlerden biri olabilir:

- Edge bulut paylaşımlarında veri
- Yerel paylaşımlardaki veriler

#### <a name="data-in-edge-cloud-shares"></a>Edge bulut paylaşımlarında veri

Edge bulutu, cihazınızdan Azure 'a katman verileri paylaşır. Azure portal aracılığıyla *kaynak* cihazınızda bu adımları uygulayın. 

- Tüm uç bulut paylaşımlarının ve kaynak cihazda bulunan kullanıcıların bir listesini oluşturun.
- Sahip olduğunuz tüm bant genişliği zamanlamalarının bir listesini oluşturun. Bu bant genişliği zamanlamalarını hedef cihazınızda yeniden oluşturacaksınız.
- Kullanılabilir ağ bant genişliğine bağlı olarak, buluta veri katmanlı verileri en üst düzeye çıkarmak için cihazınızda bant genişliği zamanlamalarını yapılandırın. Bu, cihazdaki yerel verileri en aza indirir.
- Paylaşımların buluta tamamen katmanlantığından emin olun. Katmanlama, Azure portal paylaşma durumu denetlenerek onaylanır.  

#### <a name="data-in-edge-local-shares"></a>Edge Yerel paylaşımlarında veri

Edge Yerel paylaşımlarında bulunan veriler cihazda kalır. Azure portal aracılığıyla *kaynak* cihazınızda bu adımları uygulayın. 

- Cihazdaki Edge Yerel paylaşımlarının bir listesini oluşturun.
- Verilerin tek seferlik geçişini yapacağından, uç yerel paylaşma verilerinin bir kopyasını başka bir şirket içi sunucuya oluşturun. `robocopy`Verileri kopyalamak için (SMB) veya `rsync` (NFS) gibi kopyalama araçlarını kullanabilirsiniz. İsteğe bağlı olarak, yerel paylaşımlardaki verileri yedeklemek için bir üçüncü taraf veri koruma çözümünü zaten dağıtmış olabilirsiniz. Aşağıdaki üçüncü taraf çözümler Azure Stack Edge Pro FPGA cihazlarıyla kullanım için desteklenir:

    | Üçüncü taraf yazılım           | Çözüme başvuru                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Ayrıntılar için Cohesity başvurun.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Ayrıntılar için, Commkasaya başvurun.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Ayrıntılar için VERITAS ile iletişim kurun.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Ayrıntılar için, Veead ile iletişim kurun. |


### <a name="3-prepare-iot-edge-workloads"></a>3. IoT Edge iş yüklerini hazırlama

- IoT Edge modüller dağıttıysanız ve FPGA hızlandırma kullanıyorsanız, bu modüllerin GPU cihazında çalışması için önce modülleri değiştirmeniz gerekebilir. [IoT Edge modüllerini değiştirme](azure-stack-edge-gpu-modify-fpga-modules-gpu.md)bölümündeki yönergeleri izleyin. 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Hedef cihazı hazırla

### <a name="1-create-new-order"></a>1. yeni sipariş oluştur

*Hedef* cihazınız için yeni bir sipariş (ve yeni bir kaynak) oluşturmanız gerekir. Hedef cihazın, FPGA kaynağına karşı değil, GPU kaynağına karşı etkinleştirilmesi gerekir.

Bir sipariş yerleştirmek için, Azure portal [Yeni bir Azure Stack Edge kaynağı oluşturun](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) .


### <a name="2-set-up-activate"></a>2. ayarlama, etkinleştirme

*Hedef* cihazı, daha önce oluşturduğunuz yeni kaynakla aynı şekilde ayarlamanız ve etkinleştirmeniz gerekir. 

*Hedef* cihazı Azure Portal aracılığıyla yapılandırmak için aşağıdaki adımları izleyin:

1. [Dağıtım denetim listesinde](azure-stack-edge-gpu-deploy-checklist.md)gereken bilgileri toplayın. Kaynak cihaz yapılandırmasından kaydettiğiniz bilgileri kullanabilirsiniz. 
1. [Paketi](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)açın, [raf takın](azure-stack-edge-gpu-deploy-install.md#rack-the-device) ve [cihazınızı bağlayın](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Cihazın yerel kullanıcı arabirimine bağlanın](azure-stack-edge-gpu-deploy-connect.md).
1. Eski cihazınız için kullandıklardan farklı bir IP adresi kümesi (statik IP kullanılıyorsa) kullanarak ağı yapılandırın. Bkz. [ağ ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Eski aygıtınızla aynı cihaz adını atayın ve bir DNS etki alanı sağlayın. Bkz. [cihaz ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Yeni cihazda sertifikaları yapılandırın. Bkz. [sertifikaları yapılandırma](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Azure portal etkinleştirme anahtarını alın ve yeni cihazı etkinleştirin. Bkz. [cihazı etkinleştirme](azure-stack-edge-gpu-deploy-activate.md).

Artık, paylaşılan verileri geri yüklemeye ve eski cihazda çalıştırdığınız iş yüklerini dağıtmaya hazırsınız.

## <a name="migrate-data"></a>Geçiş verileri

Artık, kaynak cihazdan *hedef* cihazınızdaki uç bulut paylaşımlarına ve uç yerel paylaşımlarına veri kopyalayacaksınız.

### <a name="1-from-edge-cloud-shares"></a>1. Edge bulut paylaşımlarından

Hedef cihazınızdaki uç bulut paylaşımlarındaki verileri eşitlemek için aşağıdaki adımları izleyin:

1. Kaynak cihazda oluşturulan paylaşım adlarına karşılık gelen [Paylaşımlar ekleyin](azure-stack-edge-j-series-manage-shares.md#add-a-share) . Paylaşımları oluşturduğunuzda, **BLOB kapsayıcısı Seç** ' ın **Varolanı kullan**' ı seçin ve ardından önceki cihazla kullanılan kapsayıcıyı seçin.
1. Önceki cihaza erişimi olan [kullanıcıları ekleyin](azure-stack-edge-j-series-manage-users.md#add-a-user) .
1. Azure 'daki [Share verilerini yenileyin](azure-stack-edge-j-series-manage-shares.md#refresh-shares) . Paylaşımın yenilenmesi, var olan kapsayıcıdan tüm bulut verilerini paylaşımlara alacak.
1. Paylaşımlarınız ile ilişkilendirilecek bant genişliği zamanlamalarını yeniden oluşturun. Ayrıntılı adımlar için bkz. [bant genişliği zamanlaması ekleme](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) .


### <a name="2-from-edge-local-shares"></a>2. Edge Yerel paylaşımlarından

IoT iş yükleriniz için Yerel paylaşım verilerini korumak üzere bir üçüncü taraf yedekleme çözümü dağıtmış olabilirsiniz. Şimdi bu verileri geri yüklemeniz gerekir.

Değiştirme aygıtı tam olarak yapılandırıldıktan sonra, cihazı yerel depolama için etkinleştirin. 

Yerel paylaşımlardaki verileri kurtarmak için şu adımları izleyin:

1. [Cihazda Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Hedef cihazdaki tüm yerel paylaşımları ekleyin. [Yerel bir paylaşma ekleme](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)içindeki ayrıntılı adımlara bakın.
1. Kaynak aygıttaki SMB paylaşımlarına erişmek, hedef cihazda IP adreslerini kullanır, ancak cihaz adını kullanırsınız. Bkz. [Azure Stack Edge Pro GPU üzerinde BIR SMB paylaşımıyla bağlantı](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-smb-share). Hedef cihazdaki NFS paylaşımlarına bağlanmak için cihazla ilişkili yeni IP adreslerini kullanmanız gerekir. Bkz. [Azure Stack Edge Pro GPU ÜZERINDE NFS paylaşımıyla bağlantı](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-nfs-share). 

    Paylaşım verilerinizi SMB veya NFS üzerinden bir ara sunucuya kopyaladıysanız, verileri ara sunucudan hedef cihazdaki paylaşımlara kopyalayabilirsiniz. Hem kaynak hem de hedef cihaz *çevrimiçiyse*, verileri doğrudan kaynak cihazdan da kopyalayabilirsiniz.

    Yerel paylaşımlardaki verileri yedeklemek için üçüncü taraf yazılım kullandıysanız, tercih ettiğiniz veri koruma çözümü tarafından sunulan kurtarma yordamını çalıştırmanız gerekir. Aşağıdaki tablodaki başvurulara bakın.

    | Üçüncü taraf yazılım           | Çözüme başvuru                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Ayrıntılar için Cohesity başvurun.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Ayrıntılar için, Commkasaya başvurun. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Ayrıntılar için VERITAS ile iletişim kurun.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Ayrıntılar için, Veead ile iletişim kurun. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. IoT Edge iş yüklerini yeniden dağıtın

IoT Edge modüller hazırlandıktan sonra, hedef cihazınıza IoT Edge iş yüklerini dağıtmanız gerekecektir. IoT Edge modüllerini dağıtmada herhangi bir hata varsa, bkz.:

- [Azure IoT Edge Için sık karşılaşılan sorunlar ve çözümleri](../iot-edge/troubleshoot-common-errors.md). 
- [Çalışma zamanı hatalarını IoT Edge](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Verileri doğrulama

Geçişten sonra tüm verilerin geçirildiğini ve iş yüklerinin hedef cihaza dağıtıldığını doğrulayın.

## <a name="erase-data-return"></a>Verileri silme, döndürme

Veri geçişi tamamlandıktan sonra yerel verileri silin ve kaynak cihazı döndürün. [Azure Stack Edge Pro cihazınızı döndürme](azure-stack-edge-return-device.md)bölümündeki adımları izleyin.


## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro GPU cihazında IoT Edge iş yüklerini dağıtmayı öğrenin](azure-stack-edge-gpu-deploy-compute-module-simple.md)