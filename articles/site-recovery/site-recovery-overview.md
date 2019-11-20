---
title: Azure Site Recovery hakkında
description: Bu sayfada, Azure Site Recovery hizmetine genel bir bakış sağlanmış ve olağanüstü durum kurtarma ile geçiş dağıtım senaryoları özetlenmiştir.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3f38560c92e91925ab8a18924acdbb74f4b90e59
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814388"
---
# <a name="about-site-recovery"></a>Site Recovery Hakkında

Azure Site Recovery hizmetine hoş geldiniz! Bu makalede bir hizmet genel hatlarıyla kısaca ele alınmaktadır.

Kuruluş olarak, planlı ve plansız kesintiler oluştuğunda verilerinizi güvende ve uygulamalarınız ile iş yüklerinizi çalışır durumda tutan bir iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisi benimsemeniz gerekir.

Azure Kurtarma Hizmetleri, BCDR stratejinize katkıda bulunur:

- **Site Recovery hizmeti**: Site Recovery, iş uygulamaları ve iş yüklerini kesintiler sırasında çalışır durumda tutarak iş sürekliliği sağlamaya yardımcı olur. Site Recovery, fiziksel ve sanal makineler (VM) üzerinde çalışan iş yüklerini birincil siteden ikincil bir konuma çoğaltır. Birincil sitenizde bir kesinti oluştuğunda ikinci konuma yük devredersiniz ve uygulamalara oradan erişirsiniz. Birincil konum tekrar çalışmaya başladıktan sonra o konuma geri dönebilirsiniz.  
- **Yedekleme hizmeti**: [Azure Backup](https://docs.microsoft.com/azure/backup/) hizmeti, verileri Azure 'a yedekleyerek verilerinizi güvende ve kurtarılabilir halde tutar.

Site Recovery, şunlar için çoğaltmayı yönetebilir:

- Azure bölgeleri arasında çoğaltılan Azure VM’leri.
- Şirket içi VM’ler, Azure Stack VM’leri ve fiziksel sunucular.


## <a name="what-does-site-recovery-provide"></a>Site Recovery ne sağlar?


**Özelliği** | **Ayrıntılar**
--- | ---
**Basit BCDR çözümü** | Site Recovery hizmetini kullanarak Azure portalındaki tek bir konumdan çoğaltma, yük devretme ve yeniden çalışmayı yönetebilirsiniz.
**Azure VM çoğaltma** | Azure VM’lerin olağanüstü durum kurtarma özelliğini birincil bir bölgeden ikincil bölgeye ayarlayabilirsiniz.
**Şirket içi VM çoğaltma** | Şirket içi VM’leri ve fiziksel sunucuları Azure’a veya ikincil bir şirket içi veri merkezine çoğaltabilirsiniz. Azure’a çoğaltma seçeneği, ikincil bir veri merkezi kullanmanın maliyetini ve karmaşıklığını ortadan kaldırır.
**İş yükü çoğaltma** | Desteklenen Azure VM’lerinde, şirket içi Hyper-V ve VMware VM'leri ile Windows/Linux fiziksel sunucularında çalışan tüm iş yüklerini çoğaltın.
**Veri esnekliği** | Site Recovery, uygulama verilerini kesintiye uğratmadan çoğaltmayı düzenler. Azure’a çoğaltılan veriler, dayanıklılık özelliği sunan Azure Depolama'da saklanır. Yük devretme işlemi gerçekleştiğinde, çoğaltılan veriler temel alınarak Azure VM'leri oluşturulur.
**RTO ve RPO hedefleri** | Kurtarma süresi hedeflerini (RTO) ve kurtarma noktası hedeflerini (RPO) kuruluş sınırları içinde tutun. Site Recovery, Azure VM’leri ve VMware VM’leri için sürekli çoğaltma sağlar ve Hyper-V için çoğaltma sıklığı 30 saniyeye kadar düşer. [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) sayesinde, RTO’yu daha da düşürebilirsiniz.
**Yük devretme boyunca uygulamalarınızın tutarlı kalmasını sağlayın** | Uygulama içinde tutarlı anlık görüntülerle kurtarma noktalarını kullanarak çoğaltabilirsiniz. Bu anlık görüntüler, disk verilerini, bellekteki tüm verileri ve devam eden tüm işlemleri yakalar.
**Kesintisiz test etme** | Sürmekte olan çoğaltmayı etkilemeden kolayca olağanüstü durum kurtarma tatbikatları gerçekleştirebilirsiniz.
**Esnek yük devretme işlemleri** | Beklenen kesintilere yönelik olarak sıfır veri kaybıyla planlı yük devretme işlemleri çalıştırabileceğiniz gibi, beklenmeyen olağanüstü durumlar için en düşük düzeyde veri kaybıyla sonuçlanan (çoğaltma sıklığına bağlı olarak) plansız yük devretme işlemleri de çalıştırabilirsiniz. Yeniden kullanılabilir olduğunda, birincil sitenize kolayca geri dönebilirsiniz.
**Özelleştirilebilir kurtarma planları** | Kurtarma planlarını kullanarak, birden çok VM’de çalışan çok katmanlı uygulamaların yük devretme ve kurtarma işlemlerini özelleştirebilir ve sıralayabilirsiniz. Bir kurtarma planında makineleri gruplayabilir ve isteğe bağlı olarak betikler ve el ile yapılan işlemler ekleyebilirsiniz. Kurtarma planları, Azure Otomasyonu runbook'ları ile tümleştirilebilir.
**BCDR tümleştirme** | Site Recovery, diğer BCDR teknolojileriyle tümleşir. Örneğin, kullanılabilir grupların yük devretme işlemlerini yönetmek amacıyla SQL Server AlwaysOn yerel desteği ile birlikte kurumsal iş yüklerinin SWL Server arka ucunu korumak için Site Recovery'yi kullanabilirsiniz.
**Azure otomasyonu tümleştirmesi** | Zengin Azure Otomasyonu kitaplığı, indirilebilen ve Site Recovery ile tümleştirilebilen üretime hazır ve uygulamaya özgü betikler sağlar.
**Ağ tümleştirmesi** | Site Recovery, Azure ile tümleşerek IP adresleri ayırma, yük dengeleyicileri yapılandırma ve etkili ağ değişimleri için Azure Traffic Manager ile tümleştirme dahil olmak üzere uygulama ağ gereksinimlerini basitleştirir.


## <a name="what-can-i-replicate"></a>Neleri çoğaltabilirim?

**Destekleniyor** | **Ayrıntılar**
--- | ---
**Çoğaltma senaryoları** | Azure VM’leri bir Azure bölgesinden diğerine çoğaltın.<br/><br/>  Şirket içi VMware VM 'lerini, Hyper-V VM 'lerini, fiziksel sunucuları (Windows ve Linux) Azure Stack VM 'Leri Azure 'a çoğaltın.<br/><br/> <br/><br/> AWS Windows örneklerini Azure 'a çoğaltın.<br/><br/> Şirket içi VMware VM’leri, System Center VMM tarafından yönetilen Hyper-V VM’leri ve fiziksel sunucuları ikincil siteye çoğaltın.
**Bölgeler** | Site Recovery için [desteklenen bölgeleri](https://azure.microsoft.com/regions/services/) inceleyin. |
**Çoğaltılan makineler** | [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) çoğaltma, [şirket içi VMware VM’ler ve fiziksel sunucular](vmware-physical-azure-support-matrix.md#replicated-machines) ve [şirket içi Hyper-V VM’lere](hyper-v-azure-support-matrix.md#replicated-vms) yönelik çoğaltma gereksinimlerini inceleyin.
**İş yükleri** | Çoğaltma için desteklenen bir makinede çalışan tüm iş yüklerini çoğaltabilirsiniz. Ayrıca, Site Recovery ekibi [çeşitli uygulamalar](site-recovery-workload.md#workload-summary) için uygulamaya özgü testler gerçekleştirdi.



## <a name="next-steps"></a>Sonraki adımlar
* [İş yükü desteği](site-recovery-workload.md) hakkında daha fazla bilgi edinin.
* [Bölgeler arasında Azure VM çoğaltma](azure-to-azure-quickstart.md) ile çalışmaya başlayın. 
