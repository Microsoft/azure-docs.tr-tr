---
title: Microsoft Azure Stack kenara genel bakış | Microsoft Docs
description: Azure 'a ağ tabanlı Aktarım için fiziksel bir cihaz kullanan bir depolama çözümü olan Azure Stack Edge açıklanmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 38aa45a9fe9e182fa98756725a8b20d737929d23
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585386"
---
# <a name="what-is-azure-stack-edge"></a>Azure Stack Edge nedir? 

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge, ağ veri aktarımı özelliklerine sahip bir AI özellikli Edge bilgi işlem aygıtıdır. Bu makalede, Azure Stack Edge çözümüne, avantajlara, önemli yeteneklere ve bu cihazı dağıtabileceğiniz senaryolara ilişkin bir genel bakış sunulmaktadır. 

Azure Stack Edge, bir hizmet olarak donanım çözümüdür. Microsoft, hızlandırılmış AI-ınlili sağlayan ve bir ağ depolama ağ geçidi 'nin tüm özelliklerine sahip olan yerleşik bir alan programlanabilir geçit dizisi (FPGA) ile birlikte bulut tarafından yönetilen bir cihaz sunar. 

## <a name="use-cases"></a>Uygulama alanları

Azure Stack Edge 'in Edge 'de hızlı Machine Learning (ML) için kullanılabileceği ve verileri Azure 'a göndermeden önce ön işlemesi için kullanabileceğiniz çeşitli senaryolar aşağıda verilmiştir.

- **Azure Machine Learning Ile çıkarım** -Azure Stack Edge ile, veri buluta gönderilmeden önce üzerinde işlem yapılabilir hızlı sonuçlar almak için ml modellerini çalıştırabilirsiniz. Tam veri kümesi, ML modellerinizi yeniden eğitmeye ve artırmaya devam etmek için isteğe bağlı olarak aktarılabilir. Azure Stack Edge cihazında Azure ML donanım hızlandırılmış modellerini kullanma hakkında daha fazla bilgi için bkz. [Azure Stack Edge üzerinde Azure ML Hardware hızlandırılmış modellerini dağıtma](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Önceden işlem** yapılabilir bir veri kümesi oluşturmak Için verileri Azure 'a göndermeden önce veri dönüştürme. Önceden işleme şu amaçlarla kullanılabilir: 

    - Verileri toplama.
    - Verileri değiştirin, örneğin kişisel verileri kaldırmak için.
    - Depolama ve bant genişliğini iyileştirmek veya daha fazla analiz için verileri alt kümele.
    - IoT Olaylarını analiz etme ve bunlara yanıt verme. 

- **Ağ üzerinden Azure 'a veri aktarma** -daha fazla bilgi işlem ve analiz sağlamak veya arşivleme amacıyla verileri kolayca ve hızlı bir şekilde Azure 'a aktarmak Için Azure Stack Edge kullanın. 


## <a name="key-capabilities"></a>Temel işlevler

Azure Stack Edge aşağıdaki yeteneklere sahiptir:

|Özellik |Açıklama  |
|---------|---------|
|Hızlandırılmış AI ınkrii| Yerleşik FPGA tarafından etkinleştirildi.|
|Bilgi işlem       |Verilerin analizine, işlenmesine, filtrelenmesine olanak tanır.|
|Yüksek performans | Yüksek performanslı işlem ve veri aktarımları.|
|Veri erişimi     | Bulutta ek veri işleme için bulut API'lerini kullanarak Azure Depolama Blobları ve Azure Dosyaları'ndan doğrudan veri erişimi. Cihazdaki yerel önbellek, en son kullanılan dosyalara hızlı erişim için kullanılır.|
|Bulutta yönetilen     |Cihaz ve hizmet, Azure portal aracılığıyla yönetilir.  |
|Çevrimdışı karşıya yükleme     | Bağlantısız mod, çevrimdışı karşıya yükleme senaryolarını destekler.|
|Desteklenen protokoller     | Veri alımında standart SMB ve NFS protokolleri için destek. <br> Desteklenen sürümler hakkında daha fazla bilgi için [Azure Stack Edge sistem gereksinimleri](data-box-edge-system-requirements.md)' ne gidin.|
|Veri yenileme     | Yerel dosyaları buluttaki en son sürümle yenileme olanağı.|
|Şifreleme    | Verileri yerel olarak şifrelemek ve *https* üzerinden buluta veri aktarımının güvenliğini sağlamak için BitLocker desteği.|
|Bant genişliği azaltma| Yoğun saatlerde bant genişliği kullanımını sınırlandırmaya kısıtlama.|


## <a name="components"></a>Bileşenler

Azure Stack Edge çözümü, Azure Stack Edge kaynağı, Azure Stack Edge fiziksel cihazından ve yerel bir Web kullanıcı arabiriminden oluşur.

* **Azure Stack Edge fiziksel cihaz** -Azure 'a veri gönderecek şekilde yapılandırılabilen, Microsoft tarafından sağlanan bir 1U takılabilir sunucu. 
    
* **Azure Stack Edge kaynağı** : bir Azure Stack Edge cihazını, farklı coğrafi konumlardan erişebileceğiniz bir web arabiriminden yönetmenizi sağlayan Azure Portal bir kaynaktır. Kaynak oluşturmak ve yönetmek, cihazları ve uyarıları görüntülemek ve yönetmek ve paylaşımları yönetmek için Azure Stack Edge kaynağını kullanın.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Daha fazla bilgi için [Azure Stack Edge cihazınız için sipariş oluşturma](data-box-edge-deploy-prep.md#create-a-new-resource)bölümüne gidin.

* **Azure Stack Edge Yerel Web Kullanıcı arabirimi** -tanılamayı çalıştırmak için yerel Web Kullanıcı arabirimini kullanın, Azure Stack Edge cihazını kapatıp yeniden başlatın, kopyalama günlüklerini görüntüleyin ve hizmet isteği dosyası için Microsoft desteği başvurun.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Web tabanlı kullanıcı arabirimini kullanma hakkında daha fazla bilgi için, [Azure Stack kenarını yönetmek üzere Web tabanlı kullanıcı arabirimini kullanma](data-box-edge-manage-access-power-connectivity-mode.md)bölümüne gidin.


## <a name="region-availability"></a>Bölge kullanılabilirliği

Azure Stack Edge fiziksel cihazı, Azure kaynağı ve verileri aktardığınız hedef depolama hesabının hepsi aynı bölgede olmalıdır.

- **Kaynak kullanılabilirliği** -Azure Stack Edge kaynağının kullanılabildiği tüm bölgelerin listesi için [bölgeye göre kullanılabilir Azure ürünlerine](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)gidin. Azure Stack Edge Azure Kamu Bulutu 'nda da dağıtılabilir. Daha fazla bilgi için bkz. [Azure Kamu nedir?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Hedef Depolama hesapları**: Verilerin depolandığı depolama hesapları, tüm Azure bölgelerinde sağlanır. Depolama hesaplarının Azure Stack uç verilerini depolayan bölgeler, cihazın en iyi performans için bulunduğu yere yakın yerleştirilmelidir. Cihazdan uzağa konumlandırılan depolama hesabı uzun gecikme sürelerine ve daha yavaş bir performansa yol açar. 


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge sistem gereksinimlerini](data-box-edge-system-requirements.md)gözden geçirin.
- [Azure Stack Edge sınırlarını](data-box-edge-limits.md)anlayın.
- [Azure Azure Stack Edge](data-box-edge-deploy-prep.md) 'i Azure portal olarak dağıtın.




