---
title: Azure geçişi 'ndeki yenilikler
description: Azure geçişi hizmetindeki yenilikler ve son güncelleştirmeler hakkında bilgi edinin.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: 3f49c0acc99f82cc2986ca896d40b3998cf47c5c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835011"
---
# <a name="whats-new-in-azure-migrate"></a>Azure geçişi 'ndeki yenilikler

[Azure geçişi](migrate-services-overview.md) , şirket içi sunucuları, uygulamaları ve verileri Microsoft Azure bulutuna keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olur. Bu makalede, Azure geçişi 'ndeki yeni sürümler ve Özellikler özetlenmektedir.

## <a name="update-march-2021"></a>Güncelleştirme (Mart 2021)
- Yüklü uygulamaları (yazılım envanteri) ve aracısız bağımlılık analizini öğrenmek ve VMware ortamınızda SQL Server örnekleri ve veritabanlarını öğrenmek için Azure geçiş gereci üzerinde birden çok sunucu kimlik bilgisi sağlamaya yönelik destek. [Daha fazla bilgi edinin](tutorial-discover-vmware.md#provide-server-credentials)
- VMware ortamınızda çalışan SQL Server örnekleri ve veritabanlarının keşfi ve değerlendirmesi artık önizlemededir. [Daha fazla bilgi](concepts-azure-sql-assessment-calculation.md) Başlamak için [bulma](tutorial-discover-vmware.md) ve [değerlendirme](tutorial-assess-sql.md) öğreticilerine bakın.
- Aracısız VMware geçişi artık vCenter başına 500 VM 'nin eşzamanlı çoğaltmasını desteklemektedir.

## <a name="update-january-2021"></a>Güncelleştirme (Ocak 2021)
-  Azure geçişi: sunucu geçiş aracı artık, VMware sanal makinelerini, fiziksel sunucuları ve sanal makineleri diğer bulutlardan Azure sanal makinelerine, müşteri tarafından yönetilen anahtarlarla (CMK) sunucu tarafı şifrelemesiyle şifrelenen disklerle Azure sanal makinelerine geçirmenize olanak tanır.

## <a name="update-december-2020"></a>Güncelleştirme (Aralık 2020)
- Azure geçişi artık, VMware geçiş 'nin aracısız yöntemini kullanarak Azure VM aracısını VMware VM 'lerine otomatik olarak Azure 'a geçirirken VMware VM 'lerine otomatik olarak yüklenir. (Windows Server 2008 R2 ve üzeri)
- VMware VM 'lerinin, müşteri tarafından yönetilen anahtarlarla (CMK) birlikte sunucu tarafı şifreleme (SSE) kullanılarak şifrelenmiş disklerle Azure sanal makinelerine geçirilmesi, Azure geçişi sunucu geçişini (aracısız çoğaltma) kullanarak artık Azure portal aracılığıyla kullanıma sunulmuştur.

## <a name="update-september-2020"></a>Güncelleştirme (Eylül 2020)
- Sunucuların Kullanılabilirlik Alanları geçişi artık desteklenmektedir.
- UEFı tabanlı VM 'lerin ve fiziksel sunucuların Azure 2. nesil VM 'lere geçirilmesi artık desteklenmektedir. Bu sürümle birlikte, Azure geçişi: sunucu geçiş aracı, geçiş sırasında Gen 2 VM 'den Gen 1 VM 'ye dönüştürmeyi gerçekleştirmeyecektir.
- Farklı değerlendirme ayarları genelinde maliyetleri karşılaştırmanıza yardımcı olmak için yeni bir Azure geçişi Power BI değerlendirme Panosu kullanılabilir. Pano, Power BI panosuna eklenen değerlendirmeleri otomatik olarak oluşturan bir PowerShell yardımcı programıyla birlikte gelir. [Daha fazla bilgi edinin.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- Bağımlılık Analizi (aracısız), artık 1000 VM 'lerde eşzamanlı olarak çalıştırılabilir.
- Bağımlılık Analizi (aracısız) artık PowerShell betikleri kullanılarak uygun ölçekte etkinleştirilebilir veya devre dışı bırakılabilir. [Daha fazla bilgi edinin.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Bağımlılık analizi kullanılarak toplanan verileri kullanarak Power BI ağ bağlantılarını görselleştirin (aracısız) [daha fazla bilgi edinin.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- VMware VM 'lerinin 32 TB 'a kadar veri diski ile geçirilmesi artık Azure geçişi: sunucu geçişi aracısız VMware geçiş yöntemi kullanılarak desteklenmektedir.

## <a name="update-august-2020"></a>Güncelleştirme (Ağustos 2020)

- Portaldan bir Azure geçişi proje anahtarı oluşturulduğu ve gereç kaydını tamamlamaya yönelik kullanılan ekleme deneyimi geliştirildi.
- VMware ve Hyper-V gereçlerini ayarlamak için portaldan OVA/VHD dosyalarını veya yükleyici betikleri indirme seçeneği.
- Gelişmiş Kullanıcı deneyimiyle gereç Yapılandırma Yöneticisi yenilendi.
- Hyper-V VM 'Leri bulma için birden çok kimlik bilgisi desteği.

## <a name="update-july-2020"></a>Güncelleştirme (Temmuz 2020)

- Aracısız VMware geçişi artık vCenter başına 300 VM 'nin eşzamanlı çoğaltmasını destekliyor

## <a name="update-june-2020"></a>Güncelleştirme (Haziran 2020)

- Şirket içi VMware VM 'lerini [Azure VMware çözümüne (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) geçirme değerlendirmeleri artık desteklenmektedir. [Daha fazla bilgi edinin](how-to-create-azure-vmware-solution-assessment.md)
- Fiziksel sunucu keşfi için gereç üzerinde birden çok kimlik bilgisi desteği.
- Kiracı kısıtlamasının yapılandırıldığı kiracı için gerecden Azure oturum açmaya izin verme desteği.


## <a name="update-april-2020"></a>Güncelleştirme (Nisan 2020)

Azure geçişi, Azure Kamu 'da dağıtımları destekler. 

- VMware VM 'Leri, Hyper-V VM 'Leri ve fiziksel sunucuları bulabilir ve değerlendirebilirsiniz.
- VMware VM 'leri, Hyper-V VM 'Leri ve fiziksel sunucuları Azure 'a geçirebilirsiniz.
- VMware geçişi için aracısız veya aracı tabanlı geçiş kullanabilirsiniz. [Daha fazla bilgi edinin](server-migrate-overview.md).
- Azure Kamu için desteklenen coğrafi lıkları ve bölgeleri [gözden geçirin](migrate-support-matrix.md#supported-geographies-azure-government) .
- [Aracı tabanlı bağımlılık Analizi](concepts-dependency-visualization.md#agent-based-analysis) , Azure Kamu 'da desteklenmez.
- Önizlemede bulunan özellikler Azure Kamu 'da, özellikle [aracısız bağımlılık Analizi](concepts-dependency-visualization.md#agentless-analysis)ve [uygulama bulma](how-to-discover-applications.md)'da desteklenir.


## <a name="update-march-2020"></a>Güncelleştirme (Mart 2020)

[Azure geçişi](migrate-appliance.md)gerecini ayarlamak için bir komut dosyası tabanlı yükleme artık kullanılabilir:

- Komut dosyası tabanlı yükleme, için bir alternatiftir. OVA (VMware)/VHD (Hyper-V) gereci yüklemesi.
- Windows Server 2016 çalıştıran var olan bir makinede VMware/Hyper-V için gereci ayarlamak üzere kullanılabilecek bir PowerShell yükleyici betiği sağlar.

## <a name="update-november-2019"></a>Güncelleştirme (Kasım 2019)

Azure geçişi 'ne bir dizi yeni özellik eklenmiştir:

- **Fiziksel sunucu değerlendirmesi**. Daha önce desteklenen fiziksel sunucu geçişine ek olarak, şirket içi fiziksel sunucuların değerlendirmesi artık desteklenmektedir.
- **Içeri aktarma temelli değerlendirme**. CSV dosyasında belirtilen meta verileri ve performans verilerini kullanan makinelerin değerlendirmesi artık desteklenmektedir.
- **Uygulama bulma**: Azure geçişi artık Azure geçişi gereci kullanılarak uygulamaların, rollerin ve özelliklerin uygulama düzeyinde bulunmasını desteklemektedir. Bu, şu anda yalnızca VMware VM 'Leri için destekleniyor ve yalnızca bulma ile sınırlıdır (değerlendirme Şu anda desteklenmiyor). [Daha fazla bilgi edinin](how-to-discover-applications.md)
- **Aracısız bağımlılık görselleştirmesi**: artık bağımlılık görselleştirmesi için aracıları açıkça yüklemeniz gerekmez. Hem aracısız hem de aracı tabanlı destek artık desteklenmektedir.
- **Sanal masaüstü**: Azure 'da şirket içi sanal masaüstü ALTYAPıSıNı (VDI) değerlendirmek ve Windows sanal masaüstüne GEÇIRMEK için ISV araçları kullanın.
- **Web uygulaması**: değerlendirme ve geçiş Web uygulamaları için kullanılan Azure App Service Geçiş Yardımcısı artık Azure geçişi ile tümleşiktir.

Azure geçişi 'ne yeni değerlendirme ve geçiş araçları eklenmiştir:

- **Rackware**: bulut geçişi sunma.
- **Movere**: değerlendirme sunma.

Azure geçişi 'nde değerlendirme ve geçiş için araçları ve ISV tekliflerini kullanma hakkında [daha fazla bilgi edinin](migrate-services-overview.md) .

## <a name="azure-migrate-current-version"></a>Azure geçişi geçerli sürümü

Azure geçişi 'nin geçerli sürümü (Temmuz 2019 ' de yayımlanmıştır) bir dizi yeni özellik sağlar:

- **Birleşik geçiş platformu**: Azure geçişi artık, gelişmiş bir dağıtım akışı ve Portal deneyimiyle Azure 'a geçiş yolculuğunu merkezileştirmek, yönetmek ve izlemek için tek bir portal sağlıyor.
- **Değerlendirme ve geçiş araçları**: Azure geçişi, yerel araçlar sağlar ve diğer Azure hizmetleriyle ve bağımsız yazılım SATıCıSı (ISV) araçları ile tümleşir. ISV tümleştirmesi hakkında [daha fazla bilgi edinin](migrate-services-overview.md#isv-integration) .
- **Azure geçişi değerlendirmesi**: Azure geçişi sunucu değerlendirmesi aracı 'nı kullanarak, VMware VM 'Lerini ve Hyper-V VM 'lerini Azure 'a geçiş için değerlendirebilirsiniz. Diğer Azure hizmetlerini ve ISV araçlarını kullanarak da geçiş için değerlendirme yapabilirsiniz.
- **Azure geçişi geçişi**: Azure geçişi sunucu geçiş aracı 'nı kullanarak, şirket Içi VMware VM 'Lerini ve Hyper-V VM 'lerini Azure 'a, ayrıca fiziksel sunucuları, diğer sanallaştırılmış sunucuları ve özel/genel bulut VM 'lerini geçirebilirsiniz. Ayrıca, ISV araçlarını kullanarak Azure 'a geçiş yapabilirsiniz.
- **Azure geçişi** gereci: Azure geçişi, şirket Içi VMware VM 'Leri ve Hyper-V VM 'lerinin bulunması ve değerlendirmesi için hafif bir gereç dağıtır.
    - Bu gereç Azure geçişi sunucu değerlendirmesi tarafından ve aracısız geçiş için Azure geçişi sunucu geçişi tarafından kullanılır.
    - Gereç, değerlendirme ve geçiş amacıyla sunucu meta verilerini ve performans verilerini sürekli olarak bulur.  
- **VMware VM geçişi**: Azure geçişi sunucu geçişi, şirket Içi VMware VM 'lerini Azure 'a geçirmek için birkaç yöntem sağlar.  Azure geçişi gereci kullanılarak aracısız bir geçiş ve bir çoğaltma gereci kullanan bir aracı tabanlı geçiş, geçirmek istediğiniz her sanal makineye bir aracı dağıtır. [Daha fazla bilgi edinin](server-migrate-overview.md)
 - **Veritabanı değerlendirmesi ve geçişi**: Azure geçişi 'Nde azure veritabanı geçiş Yardımcısı kullanarak Azure 'a geçiş için şirket içi veritabanlarını değerlendirebilirsiniz. Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını geçirebilirsiniz.
- **Web uygulaması geçişi**: Azure App Service bir genel uç nokta URL 'si kullanarak Web uygulamalarını değerlendirebilirsiniz. İç .NET uygulamalarının geçirilmesi için App Service Geçiş Yardımcısı indirebilir ve çalıştırabilirsiniz.
- **Data Box**: Azure geçişi 'nde Azure Data Box kullanarak büyük miktarlarda çevrimdışı verileri Azure 'a aktarın.

## <a name="azure-migrate-previous-version"></a>Azure önceki sürümü geçir

Azure geçişi 'nin önceki sürümünü kullanıyorsanız (yalnızca şirket içi VMware VM 'lerinin değerlendirmesi desteklenir), artık geçerli sürümü kullanmanız gerekir. Önceki sürümde, artık yeni Azure geçişi projesi oluşturamaz veya yeni bulmalar gerçekleştiremezsiniz. Mevcut projelere erişmeye devam edebilirsiniz. Bunu **tüm hizmetlere**> Azure Portal yapmak Için **Azure geçişi**' ni arayın. Azure geçişi bildirimlerinde, eski Azure geçiş projelerine erişme bağlantısı vardır.



## <a name="next-steps"></a>Sonraki adımlar

- Azure Geçişi fiyatlandırması hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/azure-migrate/).
- Azure Geçişi hakkında [sık sorulan soruları gözden geçirin](resources-faq.md).
- [VMware VM](./tutorial-assess-vmware-azure-vm.md) 'Leri ve [Hyper-V VM](tutorial-assess-hyper-v.md)'lerini değerlendirmek için öğreticilerimizi deneyin.
