---
title: Microsoft Azure Stack Edge Pro 'ya genel bakış | Microsoft Docs
description: Azure 'a ağ tabanlı Aktarım için fiziksel bir cihaz kullanan bir depolama çözümü olan Azure Stack Edge Pro 'Yu açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: e6cd1f8a1f7d1777e786ab91637b4065a2c5e850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585953"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>FPGA ile Azure Stack Edge Pro nedir?

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

FPGA ile Azure Stack Edge Pro, ağ veri aktarımı özelliklerine sahip bir AI özellikli Edge bilgi işlem aygıtıdır. Bu makalede, Azure Stack Edge Pro 'Yu FPGA çözümü, avantajları, önemli özellikler ve dağıtım senaryolarıyla ilgili bir genel bakış sunulmaktadır.

Azure Stack Edge Pro, FPGA ile bir hizmet olarak donanım çözümüdür. Microsoft, hızlandırılmış AI-ınlili sağlayan ve bir ağ depolama ağ geçidi 'nin tüm özelliklerine sahip olan yerleşik bir alan programlanabilir geçit dizisi (FPGA) ile birlikte bulut tarafından yönetilen bir cihaz sunar.

Azure Data Box Edge Azure Stack Edge olarak yeniden markalı.

## <a name="use-cases"></a>Uygulama alanları

Azure Stack Edge Pro 'nun, en uçta hızlı Machine Learning (ML) için kullanılabileceği ve verileri Azure 'a göndermeden önce ön işlemesi için kullanabileceğiniz çeşitli senaryolar aşağıda verilmiştir.

- **Azure Machine Learning Ile çıkarım** -Azure Stack Edge Pro ile, veri buluta gönderilmeden önce üzerinde işlem yapılabilir hızlı sonuçlar almak için ml modellerini çalıştırabilirsiniz. Tam veri kümesi, ML modellerinizi yeniden eğitmeye ve artırmaya devam etmek için isteğe bağlı olarak aktarılabilir. Azure Stack Edge Pro cihazında Azure ML donanım hızlandırılmış modellerini kullanma hakkında daha fazla bilgi için, bkz. [Azure Stack Edge Pro 'Da Azure ML donanım hızlandırılmış modellerini dağıtma](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Önceden işlem** yapılabilir bir veri kümesi oluşturmak Için verileri Azure 'a göndermeden önce veri dönüştürme. Önceden işleme şu amaçlarla kullanılabilir: 

    - Verileri toplama.
    - Verileri değiştirin, örneğin kişisel verileri kaldırmak için.
    - Depolama ve bant genişliğini iyileştirmek veya daha fazla analiz için verileri alt kümele.
    - IoT Olaylarını analiz etme ve bunlara yanıt verme. 

- **Ağ üzerinden Azure 'a veri aktarma** -daha fazla bilgi işlem ve analiz sağlamak veya arşivleme amacıyla verileri kolayca ve hızlı bir şekilde Azure 'a aktarmak Için Azure Stack Edge Pro 'yu kullanın. 

## <a name="key-capabilities"></a>Temel işlevler

Azure Stack Edge Pro aşağıdaki yeteneklere sahiptir:

|Özellik |Açıklama  |
|---------|---------|
|Hızlandırılmış AI ınkrii| Yerleşik FPGA tarafından etkinleştirildi.|
|Bilgi İşlem       |Verilerin analizine, işlenmesine, filtrelenmesine olanak tanır.|
|Yüksek performans | Yüksek performanslı bilgi işlem ve veri aktarımları.|
|Veri erişimi     | Bulutta ek veri işleme için bulut API'lerini kullanarak Azure Depolama Blobları ve Azure Dosyaları'ndan doğrudan veri erişimi. Cihazdaki yerel önbellek, en son kullanılan dosyalara hızlı erişim için kullanılır.|
|Bulutta yönetilen     |Cihaz ve hizmet, Azure portal aracılığıyla yönetilir.  |
|Çevrimdışı karşıya yükleme     | Bağlantısız mod, çevrimdışı karşıya yükleme senaryolarını destekler.|
|Desteklenen protokoller     | Veri alımında standart SMB ve NFS protokolleri için destek. <br> Desteklenen sürümler hakkında daha fazla bilgi için bkz. [Edge Pro sistem gereksinimleri Azure Stack](azure-stack-edge-system-requirements.md).|
|Veri yenileme     | Yerel dosyaları buluttaki en son sürümle yenileme olanağı.|
|Şifreleme    | Verileri yerel olarak şifrelemek ve *https* üzerinden buluta veri aktarımını güvenli hale getirmek için BitLocker desteği.|
|Bant genişliği azaltma| Yoğun saatlerde bant genişliği kullanımını sınırlandırmaya kısıtlama.|
|ExpressRoute | ExpressRoute aracılığıyla güvenlik eklendi. Yerel cihazlardan gelen trafiğin, ExpressRoute üzerinden dolaşmasına neden olan eşleme yapılandırmasını kullanın. Daha fazla bilgi için bkz. [ExpressRoute'a genel bakış](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Bileşenler

Azure Stack Edge Pro çözümü, Azure Stack Edge kaynağı, Azure Stack Edge Pro fiziksel cihazından ve yerel bir Web kullanıcı arabiriminden oluşur.

* **Azure Stack Edge Pro fiziksel cihazı**: Microsoft tarafından sağlanan ve Azure 'a veri gönderecek şekilde yapılandırılabilen bir 1U takılabilir sunucu.
    
* **Azure Stack Edge kaynağı**: bir Azure Stack Edge Pro cihazını, farklı coğrafi konumlardan erişebileceğiniz bir web arabiriminden yönetmenizi sağlayan Azure Portal bir kaynaktır. Kaynakları oluşturmak ve yönetmek, paylaşımları yönetmek ve cihazları ve uyarıları görüntülemek ve yönetmek için Azure Stack Edge kaynağını kullanın.
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   Azure Stack Edge Pro yaşam sonuna yaklaşırsa, yeni Azure Stack Edge Pro cihazlarının hiçbir siparişi doldurulamaz. Yeni bir müşteriyseniz, iş yükleriniz için Azure Stack Edge Pro GPU cihazlarını kullanarak araştırmanızı öneririz. Daha fazla bilgi için [GPU ile Azure Stack Edge Pro](azure-stack-edge-gpu-overview.md)'ya gidin. Azure Stack Edge Pro 'yu GPU cihazı ile sıralama hakkında daha fazla bilgi için, [Azure Stack Edge Pro-GPU için yeni kaynak oluşturma](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)bölümüne gidin.

   Mevcut bir müşteriyseniz, mevcut Azure Stack Edge Pro cihazınızı değiştirmeniz veya sıfırlamanız gerekiyorsa yeni bir Azure Stack Edge Pro kaynağı oluşturabilirsiniz. Yönergeler için [Azure Stack Edge Pro cihazınız için sipariş oluşturma](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device)bölümüne gidin.

* **Azure Stack Edge Pro yerel Web Kullanıcı arabirimi** -tanılamayı çalıştırmak için yerel Web Kullanıcı arabirimini kullanın, Azure Stack Edge Pro cihazını kapatıp yeniden başlatın, kopyalama günlüklerini görüntüleyin ve hizmet isteği dosyasını dosya dosyasına Microsoft desteği başvurun.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Web tabanlı kullanıcı arabirimini kullanma hakkında daha fazla bilgi için, [Azure Stack Edge Pro 'yu yönetmek üzere Web tabanlı kullanıcı arabirimini kullanma](azure-stack-edge-manage-access-power-connectivity-mode.md)bölümüne gidin.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Azure Stack Edge Pro fiziksel cihazı, Azure kaynağı ve veri aktarımı yaptığınız hedef depolama hesabı, tümünün aynı bölgede olması gerekmez.

- **Kaynak kullanılabilirliği** -Azure Stack Edge kaynağının kullanılabildiği tüm bölgelerin listesi için bkz. [bölgeye göre kullanılabilir Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge Pro, Azure Kamu Bulutu 'nda da dağıtılabilir. Daha fazla bilgi için bkz. [Azure Kamu nedir?](../azure-government/documentation-government-welcome.md).
    
- **Hedef Depolama hesapları**: Verilerin depolandığı depolama hesapları, tüm Azure bölgelerinde sağlanır. Depolama hesaplarının Azure Stack Edge Pro verilerini depolayan bölgeler, cihazın en iyi performans için bulunduğu yere yakın yerleştirilmelidir. Cihazdan uzağa konumlandırılan depolama hesabı uzun gecikme sürelerine ve daha yavaş bir performansa yol açar.

Azure Stack Edge hizmeti bölgesel olmayan bir hizmettir. Daha fazla bilgi için bkz. [Azure 'Da bölgeler ve kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview). Azure Stack Edge hizmetinin belirli bir Azure bölgesine bağımlılığı yoktur, bu da bölge genelinde kesintiler ve bölge genelinde kesintiler için dayanıklı hale gelir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro sistem gereksinimlerini](azure-stack-edge-system-requirements.md)gözden geçirin.
- [Azure Stack Edge Pro sınırlarını](azure-stack-edge-limits.md)anlayın.
- Azure portal [Azure Stack Edge Pro 'yu](azure-stack-edge-deploy-prep.md) dağıtın.
