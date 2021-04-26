---
title: Azure Geçişi aleti mimarisi
description: Sunucu bulma, değerlendirme ve geçiş konusunda kullanılan Azure geçiş gerecine genel bir bakış sağlar.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715019"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Geçişi aleti mimarisi

Bu makalede Azure geçiş gereci mimarisi ve süreçler açıklanmaktadır. Azure geçişi gereci, şirket içinde dağıtılan ve Azure 'a geçiş için sunucuları bulmaya yönelik hafif bir gereç.

## <a name="deployment-scenarios"></a>Dağıtım senaryoları

Azure geçişi gereci aşağıdaki senaryolarda kullanılır.

**Senaryo** | **Araç** | **Kullanıldığı yer**
--- | --- | ---
**VMware ortamında çalışan sunucuların keşfi ve değerlendirmesi** | Azure geçişi: bulma ve değerlendirme | VMware ortamınızda çalışan sunucuları bulma<br/><br/> Yüklü yazılım envanteri, aracısız bağımlılık Analizi ve SQL Server örnekleri ve veritabanlarını bulma işlemini gerçekleştirin.<br/><br/> Değerlendirmeler için sunucu yapılandırma ve performans meta verilerini toplayın.
**VMware ortamında çalışan sunucuların aracısız geçişi** | Azure geçişi: sunucu geçişi | VMware ortamınızda çalışan sunucuları bulun.<br/><br/> Herhangi bir aracı yüklemeden sunucuları çoğaltın.
**Hyper-V ortamında çalışan sunucuların keşfi ve değerlendirmesi** | Azure geçişi: bulma ve değerlendirme | Hyper-V ortamınızda çalışan sunucuları bulun.<br/><br/> Değerlendirmeler için sunucu yapılandırma ve performans meta verilerini toplayın.
**Şirket içi fiziksel veya sanallaştırılmış sunucuların bulunması ve değerlendirmesi** |  Azure geçişi: bulma ve değerlendirme |  Şirket içi fiziksel veya sanallaştırılmış sunucuları bulun.<br/><br/> Değerlendirmeler için sunucu yapılandırma ve performans meta verilerini toplayın.

## <a name="deployment-methods"></a>Dağıtım yöntemleri

Gereç birkaç yöntem kullanılarak dağıtılabilir:

- Gereç, VMware veya Hyper-V ortamında ([VMware Için ova şablonu](how-to-set-up-appliance-vmware.md) veya [Hyper-v için VHD](how-to-set-up-appliance-hyper-v.md)) çalışan sunucular için bir şablon kullanılarak dağıtılabilir.
- Bir şablon kullanmak istemiyorsanız, bir [PowerShell yükleyici betiği](deploy-appliance-script.md)kullanarak VMware veya Hyper-V ortamı için gereci dağıtabilirsiniz.
- Azure Kamu 'da, bir PowerShell yükleyici betiği kullanarak gereci dağıtmanız gerekir. [Burada](deploy-appliance-script-government.md)Dağıtım adımlarına bakın.
- Şirket içinde veya başka bir bulutta bulunan fiziksel veya sanallaştırılmış sunucular için, her zaman gereci bir PowerShell yükleyici betiği kullanarak dağıtırsınız. [Burada](how-to-set-up-appliance-physical.md)Dağıtım adımlarına bakın.
- İndirme bağlantıları aşağıdaki tablolarda bulunur.

## <a name="appliance-services"></a>Gereç Hizmetleri

Gereç aşağıdaki hizmetlere sahiptir:

- **Gereç Yapılandırma Yöneticisi**: Bu, sunucuların bulunmasını ve değerlendirmesini başlatmak üzere kaynak ayrıntıları ile yapılandırılabilecek bir Web uygulamasıdır. 
- **Keşif Aracısı**: Aracı, şirket içi değerlendirmeler olarak oluşturmak için kullanılabilecek sunucu yapılandırma meta verilerini toplar.
- **Değerlendirme Aracısı**: Aracı, performans tabanlı değerlendirmeler oluşturmak için kullanılabilecek sunucu performansı meta verilerini toplar.
- **Otomatik güncelleştirme hizmeti**: hizmet, Gereç üzerinde çalışan tüm aracıların güncel kalmasını sağlar. Her 24 saatte bir otomatik olarak çalışır.
- **DRA Aracısı**: sunucu çoğaltmasını düzenleyin ve çoğaltılan sunucular ile Azure arasındaki iletişimi koordine edin. Yalnızca Azure 'a aracısız geçiş kullanılarak sunucular çoğaltılırken kullanılır.
- **Ağ geçidi**: çoğaltılan verileri Azure 'a gönderir. Yalnızca Azure 'a aracısız geçiş kullanılarak sunucular çoğaltılırken kullanılır.
- **SQL bulma ve değerlendirme Aracısı**: SQL Server örneklerinin ve veritabanlarının yapılandırma ve performans meta verilerini Azure 'a gönderir.

> [!Note]
> Son 3 hizmet yalnızca VMware ortamınızda çalışan sunucuların bulunması ve değerlendirmesi için kullanılan gereç içinde kullanılabilir.<br/> VMware ortamınızda çalışan SQL Server örnekleri ve veritabanlarının keşfi ve değerlendirmesi artık önizlemededir. Bu özelliği denemek için [**bu bağlantıyı**](https://aka.ms/AzureMigrate/SQL) kullanarak **Doğu Avustralya** bölgesinde bir proje oluşturun. Zaten Doğu Avustralya bölgesinde bir projeniz varsa ve bu özelliği denemek istiyorsanız, lütfen portalda bu [**önkoşulları**](how-to-discover-sql-existing-project.md) tamamladığınızdan emin olun.

## <a name="discovery-and-collection-process"></a>Bulma ve toplama işlemi

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Gereç mimarisi":::

Gereç, aşağıdaki işlemi kullanarak bulma kaynaklarıyla iletişim kurar.

**İşleme** | **VMware gereci** | **Hyper-V gereci** | **Fiziksel Gereç**
---|---|---|---
**Bulmayı Başlat** | Gereç, varsayılan olarak TCP bağlantı noktası 443 üzerindeki vCenter Server ile iletişim kurar. VCenter sunucusu farklı bir bağlantı noktasını dinliyorsa, bunu gereç Yapılandırma Yöneticisi 'nde yapılandırabilirsiniz. | Gereç, WinRM bağlantı noktası 5985 (HTTP) üzerindeki Hyper-V konaklarıyla iletişim kurar. | Gereç, 22 (TCP) bağlantı noktası üzerinden Linux sunucularıyla WinRM bağlantı noktası 5985 (HTTP) üzerinden Windows sunucularıyla iletişim kurar.
**Yapılandırma ve performans meta verilerini toplayın** | Gereç, bağlantı noktası 443 (varsayılan bağlantı noktası) veya başka herhangi bir bağlantı noktası üzerinden vCenter Server, vSphere API 'Leri kullanarak, vCenter Server üzerinde çalışan sunucuların meta verilerini toplar. | Gereç, 5985 numaralı bağlantı noktasında konaklarla Genel Bilgi Modeli (CıM) oturumu kullanarak Hyper-V konaklarında çalışan sunucuların meta verilerini toplar.| Gereç, 5985 numaralı bağlantı noktasında ve numaralı bağlantı noktasında SSH bağlantısı kullanan Linux sunucularından Genel Bilgi Modeli (CıM) oturumu kullanarak Windows sunucularından meta verileri toplar.
**Bulgu verileri gönder** | Gereç, toplanan verileri Azure geçişi 'ne gönderir: bulma ve değerlendirme ve Azure geçişi: SSL bağlantı noktası 443 üzerinden sunucu geçişi.<br/><br/>  Gereç, internet üzerinden veya ExpressRoute özel eşlemesi ya da Microsoft eşleme devreleri aracılığıyla Azure 'a bağlanabilir. | Gereç, toplanan verileri Azure geçişi: SSL bağlantı noktası 443 üzerinden bulma ve değerlendirme 'a gönderir.<br/><br/> Gereç, internet üzerinden veya ExpressRoute özel eşlemesi ya da Microsoft eşleme devreleri aracılığıyla Azure 'a bağlanabilir. | Gereç, toplanan verileri Azure geçişi: SSL bağlantı noktası 443 üzerinden bulma ve değerlendirme 'a gönderir.<br/><br/> Gereç, internet üzerinden veya ExpressRoute özel eşlemesi ya da Microsoft eşleme devreleri aracılığıyla Azure 'a bağlanabilir. 
**Veri toplama sıklığı** | Yapılandırma meta verileri, her 30 dakikada bir toplanır ve gönderilir. <br/><br/> Performans meta verileri 20 saniyede bir toplanır ve her 10 dakikada bir veri noktasını Azure 'a göndermek için toplanır. <br/><br/> Yazılım envanteri verileri her 12 saatte bir Azure 'a gönderilir. <br/><br/> Aracısız bağımlılık verileri 5 dakikada bir toplanır ve her 6 saatte bir Azure 'a gönderilir. <br/><br/> SQL Server yapılandırma verileri her 24 saatte bir güncelleştirilir ve performans verileri her 30 saniyede yakalanır.| Yapılandırma meta verileri, her 30 dakikada bir toplanır ve gönderilir. <br/><br/> Performans meta verileri 30 saniyede bir toplanır ve Azure 'a her 10 dakikada bir veri noktası göndermek için toplanır.|  Yapılandırma meta verileri, her 30 dakikada bir toplanır ve gönderilir. <br/><br/> Performans meta verileri 5 dakikada bir toplanır ve her 10 dakikada bir veri noktasını Azure 'a göndermek için toplanır.
**Değerlendirme ve geçirme** | Azure geçişi: bulma ve değerlendirme aracı kullanılarak gereç tarafından toplanan meta verilerden değerlendirmeler oluşturabilirsiniz.<br/><br/>Ayrıca, Azure geçişi: sunucu geçiş aracını kullanarak, aracısız sunucu çoğaltmasını organize etmek için VMware ortamınızda çalışan sunucuları geçirmeyi de başlatabilirsiniz.| Azure geçişi: bulma ve değerlendirme aracı kullanılarak gereç tarafından toplanan meta verilerden değerlendirmeler oluşturabilirsiniz. | Azure geçişi: bulma ve değerlendirme aracı kullanılarak gereç tarafından toplanan meta verilerden değerlendirmeler oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Gereç destek matrisini [gözden geçirin](migrate-appliance.md) .