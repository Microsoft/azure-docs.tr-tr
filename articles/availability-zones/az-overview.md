---
title: Azure 'da bölgeler ve Kullanılabilirlik Alanları
description: Azure 'da teknik ve yasal gereksinimlerinizi karşılayacak bölgeler ve Kullanılabilirlik Alanları hakkında bilgi edinin.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: a15a94694f3c0623830650a8b5bbb00dc4c4cb6b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285522"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure 'da bölgeler ve Kullanılabilirlik Alanları

Microsoft Azure Hizmetleri, bulut işlemlerinizi en uygun düzeyde olacak şekilde küresel olarak kullanılabilir. Gereksinimlerinize göre en iyi bölgeyi teknik ve yasal noktalara göre seçebilirsiniz: hizmet özellikleri, veri yerleşimi, uyumluluk gereksinimleri ve gecikme.

## <a name="terminology"></a>Terminoloji

Azure 'da bölgeleri ve Kullanılabilirlik Alanları daha iyi anlamak için, temel hüküm veya kavramların anlaşılmasına yardımcı olur.

| Terim veya kavram | Description |
| --- | --- |
| region | Gecikme süresi tanımlı bir çevre içinde dağıtılan ve ayrılmış bölgesel düşük gecikmeli bir ağ ile bağlanmış bir veri merkezleri kümesi. |
| Coğrafya | En az bir Azure bölgesi içeren bir dünya alanı. Geographiler, veri uygunluğunu ve uyumluluk sınırlarını koruyan ayrı bir pazar tanımlar. Coğrafyalar, özel veri yerleşikliği ve uyumluluk gereksinimleri olan müşterilerin verileri ile uygulamalarını yakın tutmasına olanak tanır. Geographiler, adanmış yüksek kapasiteli ağ altyapımıza yönelik bağlantı yoluyla, tam bölge arızalarına karşı dayanıklı bir şekilde hataya dayanıklıdır. |
| Kullanılabilirlik Alanı | Bölge içindeki benzersiz fiziksel konumlar. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. |
| Önerilen bölge | En geniş hizmet özelliklerini sağlayan ve şimdi Kullanılabilirlik Alanları destekleyecek şekilde tasarlanan bölge. Bunlar, Azure portal **Önerilen** şekilde belirtilmiştir. |
| Alternatif (diğer) bölge | Azure 'un bir veri yerleşimi içindeki parmak izini, önerilen bir bölgenin de bulunduğu bölge için genişleten bölge. Alternatif bölgeler, gecikme süresini en iyi duruma getirmeye ve olağanüstü durum kurtarma gereksinimlerine ikinci bir bölge sağlamaya yardımcı olur. Bunlar, Kullanılabilirlik Alanları desteklemek için tasarlanmamıştır (Azure, bu bölgelerin önerilen bölgeler olup olmadığını belirlemesi için düzenli olarak değerlendirmelerine rağmen). Bunlar, Azure portal **başka** bir şekilde belirtilmiştir. |
| temel hizmet | Bölge Genel kullanıma sunulduğunda tüm bölgelerde kullanılabilen bir çekirdek Azure hizmeti. |
| temel hizmet | Genel kullanılabilirlik veya diğer bölgelerde isteğe bağlı kullanılabilirlik alanı için 90 gün içinde önerilen tüm bölgelerde kullanılabilen bir Azure hizmeti. |
| özelleştirilmiş hizmet | Özelleştirilmiş/özelleştirilmiş donanımla desteklenen bölgeler arasında isteğe bağlı kullanılabilirlik sağlayan bir Azure hizmetidir. |
| bölgesel hizmet | Bölgeye göre dağıtılan ve müşterinin hizmetin dağıtılacağı bölgeyi belirtmesini sağlayan bir Azure hizmeti. Tüm liste için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| bölgesel olmayan hizmet | Belirli bir Azure bölgesine bağımlılığı olmayan bir Azure hizmeti. Bölgesel olmayan hizmetler iki veya daha fazla bölgeye dağıtılır ve bölgesel bir hata varsa, başka bir bölgedeki hizmetin örneği müşterilere hizmet vermeye devam eder. Tüm liste için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Bölgeler

Bölge, gecikme tarafından tanımlanan bir çevre içinde dağıtılan ve adanmış bölgesel düşük gecikmeli bir ağ ile bağlanan bir veri merkezleri kümesidir. Azure, siteler arası dayanıklılık sağlamak için birden çok bölgeye dahil olmak üzere, ihtiyacınız olan uygulamaları dağıtma esnekliği sunar. Daha fazla bilgi için bkz. [dayanıklılık bilgilerine genel bakış](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Kullanılabilirlik Alanları

Kullanılabilirlik alanı, uygulamalarınızı ve verilerinizi veri merkezi hatalarından koruyan yüksek kullanılabilirliğe sahip bir tekliftir. Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılığı güvence altına almak için etkinleştirilmiş tüm bölgelerde en az üç ayrı alan vardır. Bir bölgedeki Kullanılabilirlik Alanları fiziksel ayrımı, uygulamaları ve verileri veri merkezi hatalarından korur. Bölgesel olarak yedekli hizmetler, uygulamalarınızı ve verilerinizi Kullanılabilirlik Alanları arasında çoğaltarak hata noktalarından koruyun. Azure, Kullanılabilirlik Alanları sayesinde sektörün en iyi% 99,99 VM çalışma süresi SLA 'sını sunmaktadır. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

Bir Azure bölgesindeki kullanılabilirlik bölgesi bir hata etki alanının ve bir güncelleştirme etki alanının birleşimidir. Örneğin, bir Azure bölgesindeki üç bölgede üç veya daha fazla VM oluşturursanız, VM 'niz üç hata etki alanına ve üç güncelleştirme etki alanına etkili bir şekilde dağıtılır. Azure platformu, farklı bölgelerdeki VM 'Lerin aynı anda güncellenmek üzere zamanlanmadığından emin olmak için bu dağıtımı güncelleştirme etki alanları genelinde tanır.

Bir bölgedeki işlem, depolama, ağ ve veri kaynaklarınızı birlikte bularak ve diğer bölgelerde çoğaltarak uygulama mimarinizde yüksek kullanılabilirlik oluşturun. Kullanılabilirlik Alanlarını destekleyen Azure hizmetleri iki kategoriye ayrılır:

-  Bir kaynağın belirli bir bölgeye sabitlendiği (örneğin, sanal makineler, yönetilen diskler, standart IP adresleri) veya
- Bölgesel olarak **yedekli hizmetler** – Azure platformu bölgeler arasında otomatik olarak çoğaltılır (örneğin, bölgesel olarak yedekli depolama, SQL veritabanı).

Azure 'da kapsamlı iş sürekliliği elde etmek için Azure bölge çiftleriyle Kullanılabilirlik Alanları birleşimini kullanarak uygulama mimarinizi geliştirin. Uygulamalarınızı ve verilerinizi Azure bölgesindeki Kullanılabilirlik Alanları kullanarak yüksek kullanılabilirlik ve olağanüstü durum kurtarma koruması için Azure bölgelerinde zaman uyumsuz olarak çoğaltma yapabilirsiniz.
 
![bölgede aşağı doğru bir bölgenin kavramsal görünümü](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Kullanılabilirlik alanı tanımlayıcıları (yukarıdaki resimde 1, 2 ve 3 sayıları), her aboneliğin bağımsız olarak gerçek fiziksel bölgelere mantıksal olarak eşlenir. Bu, belirli bir abonelikteki kullanılabilirlik Bölge 1 farklı bir abonelikte kullanılabilirlik Bölge 1 farklı bir fiziksel bölgeye başvurabileceği anlamına gelir. Sonuç olarak, sanal makine yerleştirmesi için farklı aboneliklerde kullanılabilirlik bölgesi kimliklerinin olmaması önerilir.

## <a name="region-and-service-categories"></a>Bölge ve hizmet kategorileri

Azure 'un bölgeler genelinde Azure hizmetlerinin kullanılabilirliğine yönelik yaklaşımı, önerilen bölgelerde ve alternatif bölgelerde sunulan hizmetleri ifade ederek en iyi şekilde açıklanmıştır.

- **Önerilen bölge** -en geniş hizmet özelliklerini sağlayan ve şimdi kullanılabilirlik alanları veya gelecekte destekleyecek şekilde tasarlanan bölge. Bunlar, Azure portal **Önerilen** şekilde belirtilmiştir.
- **Alternatif (diğer) bölge** -Azure 'un bir veri yerleşimi içindeki parmak izini, önerilen bir bölgenin de bulunduğu bölge için genişleten bir bölgedir. Alternatif bölgeler, gecikme süresini en iyi duruma getirmeye ve olağanüstü durum kurtarma gereksinimlerine ikinci bir bölge sağlamaya yardımcı olur. Bunlar, Kullanılabilirlik Alanları desteklemek için tasarlanmamıştır (Azure, bu bölgelerin önerilen bölgeler olup olmadığını belirlemesi için düzenli olarak değerlendirmelerine rağmen). Bunlar, Azure portal **başka** bir şekilde belirtilmiştir.

### <a name="comparing-region-types"></a>Bölge türlerini karşılaştırma

Azure hizmetleri üç kategoride gruplandırılır: temel, genel ve özel hizmetler. Azure 'un belirli bir bölgeye hizmet dağıtmaya yönelik genel ilkesi, birincil olarak bölge türü, hizmet kategorileri ve müşteri talebi tarafından çalıştırılır:

- Temel **: bölge** genel olarak kullanılabilir olduğunda veya yeni bir temel hizmetin genel kullanıma sunulduğunda 90 gün içinde, önerilen ve alternatif bölgelerde kullanılabilir.
- **Temel** : bölge genel kullanılabilirliği 90 gün içinde önerilen tüm bölgelerde kullanılabilir; diğer bölgelerde talep odaklı (birçoğu, diğer bölgelere ait büyük bir alt kümeye zaten dağıtılır).
- **Özelleştirilmiş-hedeflenen** hizmet sunumları, genellikle sektör odaklı veya özelleştirilmiş/özelleştirilmiş donanımlar tarafından desteklenir. Bölgeler arasında isteğe bağlı kullanılabilirlik (birçoğu, önerilen bölgelerin büyük bir alt kümesine zaten dağıtılır).

Belirli bir bölgede hangi hizmetlerin dağıtıldığını görmek ve bir bölgedeki hizmetlerin önizleme veya genel kullanılabilirliği için gelecekteki yol haritasını görmek üzere bkz. [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Belirli bir bölgede bir hizmet teklifi yoksa, Microsoft satış temsilcinizle iletişim kurarak ilgi alanınızı paylaşabilirsiniz.

| Bölge türü | Bölgesel olmayan | Temel | Temel | Özelleştirilmiş | Kullanılabilirlik Alanları | Veri yerleşimi |
| --- | --- | --- | --- | --- | --- | --- |
| Önerilen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Talep odaklı | :heavy_check_mark: | :heavy_check_mark: |
| Diğer | :heavy_check_mark: | :heavy_check_mark: | Talep odaklı | Talep odaklı | Yok | :heavy_check_mark: |

### <a name="services-by-category"></a>Kategoriye göre hizmetler

Daha önce belirtildiği gibi, Azure hizmetleri üç kategoride sınıflandırır: temel, genel ve özel. Hizmet kategorileri genel kullanıma sunulduğuna göre atanır. Genellikle, hizmetler yaşam döngüsünü özelleştirilmiş bir hizmet olarak başlatır ve talep ve kullanım artışları, temel veya temel olarak yükseltilebilir. Aşağıdaki tabloda temel olarak hizmetler kategorisi listelenmektedir. Tabloyla ilgili olarak aşağıdakileri göz önünde belirtmelisiniz:

- Bazı hizmetler bölgesel değildir. Bölgesel olmayan hizmetlerin bilgileri ve bir listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/).
- Daha eski nesil hizmetler veya sanal makineler listelenmez. Daha fazla bilgi için bkz. [önceki nesil sanal makine boyutlarında](../virtual-machines/sizes-previous-gen.md)belgeler.
- Hizmetlere genel kullanıma (GA) kadar bir kategori atanmaz. Bilgi ve önizleme hizmetlerinin bir listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Temel                           | Temel                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Depolama Hesapları                       | API Management                                    | 
> | Application Gateway                    | Uygulama Yapılandırması                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Otomasyon                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure Genel IP                        | Redis için Azure Cache                             | 
> | Azure SQL Veritabanı                     | Azure Bilişsel Hizmetler                          | 
> | Azure SQL Yönetilen Örnek             | Azure Bilişsel Hizmetler: Görüntü İşleme         | 
> | Disk Depolama                           | Azure bilişsel hizmetler: Content Moderator       | 
> | Event Hubs                             | Azure bilişsel hizmetler: yüz                    | 
> | Key Vault                              | Azure Bilişsel Hizmetler: Metin Analizi          | 
> | Yük dengeleyici                          | Azure Veri Gezgini                               | 
> | Service Bus                            | MySQL için Azure Veritabanı                          | 
> | Service Fabric                         | PostgreSQL için Azure Veritabanı                     | 
> | Depolama: sık/seyrek erişimli BLOB depolama katmanları   | Azure DDoS Koruması                             | 
> | Depolama: yönetilen diskler                 | Azure Güvenlik Duvarı                                    | 
> | Sanal Makine Ölçek Kümeleri             | Azure Güvenlik Duvarı Yöneticisi                            | 
> | Sanal Makineler                       | Azure İşlevleri                                   | 
> | Sanal makineler: Azure adanmış ana bilgisayar | Azure IoT Hub                                     | 
> | Sanal makineler: Av2-Series           | Azure Kubernetes Service (AKS)                    | 
> | Sanal makineler: Bs-Series            | Azure Izleyici: Application Insights               | 
> | Sanal makineler: DSv2-Series          | Azure Izleyici: Log Analytics                      | 
> | Sanal makineler: DSv3-Series          | Azure Özel Bağlantı                                | 
> | Sanal makineler: Dv2-Series           | Azure Site Recovery                               | 
> | Sanal makineler: Dv3-Series           | Azure Synapse Analytics                           |     
> | Sanal makineler: ESv3-Series          | Batch                                             | 
> | Sanal makineler: Ev3-Series           | Cloud Services: a serisi                          | 
> | Sanal Ağ                        | Container Instances                               | 
> | VPN Gateway                            | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Ağ İzleyicisi                                   | 
> |                                        | Premium blob depolaması                              | 
> |                                        | Premium dosya depolaması                             | 
> |                                        | Sanal makineler: Ddsv4-Series                    | 
> |                                        | Sanal makineler: Ddv4-Series                     | 
> |                                        | Sanal makineler: Dsv4-Series                     | 
> |                                        | Sanal makineler: Dv4-Series                      | 
> |                                        | Sanal makineler: Edsv4-Series                    | 
> |                                        | Sanal makineler: Edv4-Series                     | 
> |                                        | Sanal makineler: Esv4-Series                     | 
> |                                        | Sanal makineler: Ev4-Series                      | 
> |                                        | Sanal makineler: Fsv2-Series                     | 
> |                                        | Sanal makineler: a serisi                        | 
> |                                        | Sanal WAN                                       | 



### <a name="specialized-services"></a>Özel hizmetler
Daha önce belirtildiği gibi, Azure hizmetleri üç kategoride sınıflandırır: temel, genel ve özel. Hizmet kategorileri genel kullanıma sunulduğuna göre atanır. Genellikle, hizmetler yaşam döngüsünü özelleştirilmiş bir hizmet olarak başlatır ve talep ve kullanım artışları, temel veya temel olarak yükseltilebilir. Aşağıdaki tabloda, özel hizmetler listelenmektedir. 

> [!div class="mx-tableFixed"]
> | Özelleştirilmiş                                          |
> |------------------------------------------------------|
> | FHIR için Azure API'si                                   |
> | Azure Analysis Services                              |
> | Azure Blok Zinciri Hizmeti                             |
> | Azure bilişsel hizmetler: anomali algılayıcısı           |
> | Azure bilişsel hizmetler: Özel Görüntü İşleme              |
> | Azure bilişsel hizmetler: form tanıyıcı            |
> | Azure bilişsel hizmetler: modern okuyucu           |
> | Azure bilişsel hizmetler: Language Understanding     |
> | Azure bilişsel hizmetler: kişiselleştirici               |
> | Azure bilişsel hizmetler: Soru-Cevap Oluşturma                  |
> | Azure bilişsel hizmetler: konuşma Hizmetleri            |
> | Azure Veri Paylaşımı                                     |
> | Azure Databricks                                     |
> | MariaDB için Azure Veritabanı                           |
> | Azure Veritabanı Geçiş Hizmeti                     |
> | Azure Ayrılmış HSM                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Önbelleği                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure Red Hat OpenShift                              |
> | Azure SignalR Hizmeti                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware Çözümü                                |
> | CloudSimple tarafından sunulan Azure VMware Çözümü                 |
> | Spatial Anchors                                      |
> | Depolama alanı: Arşiv Depolama                             |
> | Ultra Disk Depolama                                   |
> | Video Indexer                                        |
> | Sanal makineler: DASv4-Series                       |
> | Sanal makineler: DAv4-Series                        |
> | Sanal makineler: DCsv2-Series                       |
> | Sanal makineler: EASv4-Series                       |
> | Sanal makineler: EAv4-Series                        |
> | Sanal makineler: HBv1-Series                        |
> | Sanal makineler: HBv2-Series                        |
> | Sanal makineler: HCv1-Series                        |
> | Sanal makineler: H serisi                           |
> | Sanal makineler: LSv2-Series                        |
> | Sanal makineler: Mv2-Series                         |
> | Sanal makineler: NCv3-Series                        |
> | Sanal makineler: NDv2-Series                        |
> | Sanal makineler: NVv3-Series                        |
> | Sanal makineler: NVv4-Series                        | 
> | Sanal makineler: Azure üzerinde SAP HANA Büyük Örnekleri  |




## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da Kullanılabilirlik Alanları destekleyen bölgeler](az-region.md)
- [Hızlı başlangıç şablonları](https://aka.ms/azqs)
