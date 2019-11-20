---
title: Azure sanal makine kullanımını anlama
description: Sanal makine kullanım ayrıntılarını anlayın
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: fe3c8a3b5d63c67813a5098742392d5658e5c204
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034229"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Azure sanal makine kullanımını anlama
Azure kullanım verilerinizi analiz ederek, güçlü tüketim öngörüleri, kuruluşunuzun tamamında daha iyi maliyet yönetimi ve ayırmayı olanaklı hale getirebilirler. Bu belge, Azure Işlem tüketimi ayrıntılarınız hakkında ayrıntılı bilgi sağlar. Genel Azure kullanımı hakkında daha fazla bilgi için [Faturanızı Anlama](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)bölümüne gidin.

## <a name="download-your-usage-details"></a>Kullanım ayrıntılarınızı indirin
Başlamak için [kullanım ayrıntılarınızı indirin](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Aşağıdaki tabloda, Azure Resource Manager aracılığıyla dağıtılan sanal makineler için kullanım tanımı ve örnek değerleri verilmiştir. Bu belge, klasik modelimiz aracılığıyla dağıtılan VM 'Ler için ayrıntılı bilgiler içermez.


| Alanlar             | Anlamı                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Örnek değerler                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kullanım Tarihi         | Kaynağın kullanıldığı tarih.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| Ölçüm Kimliği           | Bu kullanımın ait olduğu en üst düzey hizmeti belirler.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Sanal makineler"                                                                                                                                                                                                                                                                                                                                               |
| Ölçüm Alt Kategorisi | Faturalanan ölçüm tanımlayıcısı. <ul><li>Işlem saati kullanımı için, her VM boyutu + OS (Windows, Windows dışı) + bölgesi için bir ölçüm vardır.</li><li>Premium yazılım kullanımı için her yazılım türü için bir ölçüm vardır. Çoğu Premium yazılım görüntülerinin her çekirdek boyutu için farklı ölçümleri vardır. Daha fazla bilgi için, [Işlem fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/virtual-machines/) ziyaret edin.</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Ölçüm Adı         | Bu, Azure 'daki her hizmet için özeldir. İşlem için her zaman "İşlem Saatleri" olur.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "İşlem Saatleri"                                                                                                                                                                                                                                                                                                                                                  |
| Ölçüm Bölgesi       | Veri merkezi konumuna bağlı olarak ücretlendirilen belirli hizmetler için veri merkezinin konumunu belirtir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA Doğu"                                                                                                                                                                                                                                                                                                                                                       |
| Birim               | Hizmetin ücretlendirildiği birimi tanımlar. İşlem kaynakları saat başına faturalandırılır.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Saatlerinin                                                                                                                                                                                                                                                                                                                                                          |
| Kullanılan           | O gün için tüketilen kaynağın miktarı. Işlem için, VM 'nin belirli bir saat boyunca çalıştığı her dakika için faturalandırırız (en fazla 6 ondalık doğruluk).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Kaynak Konumu  | Kaynağın çalıştığı veri merkezini tanımlar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA Doğu"                                                                                                                                                                                                                                                                                                                                                        |
| Kullanılan Hizmet   | Kullandığınız Azure platform hizmeti.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft. COMPUTE"                                                                                                                                                                                                                                                                                                                                              |
| Kaynak Grubu     | Dağıtılan kaynağın içinde çalıştığı kaynak grubu. Daha fazla bilgi için bkz [. Azure Resource Manager genel bakış.](https://docs.microsoft.com/azure/virtual-machines/linux/vm-usage)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    MyRG                                                                                                                                                                                                                                                                                                                                                        |
| Örnek Kimliği        | Kaynak için tanımlayıcı. Tanımlayıcı, kaynağı oluştururken belirttiğiniz adı içerir. VM 'Ler için, örnek KIMLIĞI SubscriptionID, ResourceGroupName ve VMName (veya ölçek kümesi kullanımı için ölçek kümesi adı) içerecektir.                                                                                                                                                                                                                                                                                                                                                                                                                    | "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyRG/Providers/Microsoft. COMPUTE/virtualMachines/MyVM1"<br><br>veya<br><br>"/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyRG/Providers/Microsoft. COMPUTE/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Etiketler               | Kaynağa atadığınız etikettir. Fatura kayıtlarını gruplamak için etiketleri kullanın. [Sanal makinelerinizi nasıl etiketleyeceğinizi öğrenin.](tag.md) Bu yalnızca Kaynak Yöneticisi VM 'Ler için kullanılabilir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{" myDepartment ":" RD "," myUser ":" myName "}"                                                                                                                                                                                                                                                                                                                        |
| Ek Bilgi    | Hizmete özgü meta veriler. VM 'Ler için ek bilgi alanında aşağıdaki verileri doldurduk: <ul><li>Çalıştırdığınız görüntü türüne özgü görüntü. Aşağıda, görüntü türleri altında desteklenen dizelerin tam listesini bulun.</li><li>Hizmet türü: dağıttığınız boyut.</li><li>VMName: sanal makinenizin adı. Bu alan yalnızca ölçek kümesi VM 'Leri için doldurulur. Ölçek kümesi VM 'Leri için VM adınızın olması gerekiyorsa, bunu yukarıdaki örnek KIMLIĞI dizesinde bulabilirsiniz.</li><li>UsageType: Bu, temsil ettiği kullanım türünü belirtir.<ul><li>Bu, temel alınan VM için Standard_D1_v2 gibi Işlem saati kullanımdır.</li><li>ComputeHR_SW, sanal makine Microsoft R Server gibi Premium yazılım kullanıyorsa Premium yazılım ücretlendirilir.</li></ul></li></ul>    | Sanal makineler {"ImageType": "kurallı", "ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "Bilgrir"}<br><br>Sanal Makine Ölçek Kümeleri {"ImageType": "kurallı", "ServiceType": "Standard_DS1_v2", "VMName": "myVM1", "UsageType": "Bilgır"}<br><br>Premium yazılım {"ImageType": "", "ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR_SW"} |

## <a name="image-type"></a>Görüntü türü
Azure galerisindeki bazı görüntülerde, görüntü türü ek bilgi alanında doldurulur. Bu, kullanıcıların sanal makinenize ne dağıttığlarını anlamalarına ve izlemesine olanak sağlar. Dağıttığınız görüntüye göre bu alana doldurulan aşağıdaki değerler:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Açık mantık 
  - Oracle 
  - SLES for SAP 
  - SQL Server 14 Preview for Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - SAP Business Applications için Red Hat     
  - SAP HANA için Red Hat 
  - Windows Istemcisi KLG 
  - Windows Server KLG 
  - Windows Server Önizleme 

## <a name="service-type"></a>Hizmet Türü
Ek bilgi alanındaki hizmet türü alanı, dağıttığınız tam VM boyutuna karşılık gelir. Premium depolama VM 'Leri (SSD tabanlı) ve Premium olmayan depolama VM 'Leri (HDD tabanlı) aynı şekilde fiyatlandırılır. Standart\_DS2\_v2 gibi SSD tabanlı bir boyut dağıtırsanız, dizi alt kategori sütununda SSD olmayan boyut (' standart\_D2\_v2 VM ') ve ek bilgi alanındaki SSD boyutunu (' standart\_DS2\_v2 ') görürsünüz.

## <a name="region-names"></a>Bölge adları
Kullanım ayrıntılarında kaynak konumu alanında doldurulmuş bölge adı, Azure Resource Manager kullanılan bölge adından farklıdır. Bölge değerleri arasında bir eşleme aşağıda verilmiştir:

|    **Kaynak Yöneticisi bölge adı**       |    **Kullanım ayrıntılarında kaynak konumu**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    AU Doğu                               |
|    australiasoutheast    |    AU Güneydoğu                          |
|    brazilsouth           |    BR Güney                              |
|    Canadaorta         |    CA Orta                            |
|    Canadadoğu            |    CA Doğu                               |
|    Merkezileştirme Hindistan          |    IN Orta                            |
|    centralus             |    Orta ABD                            |
|    chinaeast             |    Çin Doğu                            |
|    chinanorth            |    Çin Kuzey                           |
|    eastasia              |    Doğu Asya                             |
|    eastus                |    Doğu ABD                               |
|    eastus2               |    Doğu ABD 2                             |
|    Germanyorta        |    DE Orta                            |
|    Germanykuzeydoğu      |    DE Kuzeydoğu                          |
|    japaneast             |    JA Doğu                               |
|    japanwest             |    JA Batı                               |
|    KoreaCentral          |    KR Orta                            |
|    Koreagüney            |    KR Güney                              |
|    Kuzeydoğu ABD        |    Orta Kuzey ABD                      |
|    northeurope           |    Kuzey Avrupa                          |
|    southcentralus        |    Orta Güney ABD                      |
|    Güneydoğu         |    Güneydoğu Asya                        |
|    Güneydoğu            |    IN Güney                              |
|    Ukkuzey               |    ABD Kuzey                              |
|    uksouth               |    Birleşik Krallık Güney                              |
|    UKSouth2              |    Birleşik Krallık Güney 2                            |
|    ukbatı                |    Birleşik Krallık Batı                               |
|    Usdodorta          |    US DoD Orta                        |
|    USDoDEast             |    US DoD Doğu                           |
|    USGovArizona          |    USGov Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    USGov Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    ABD Orta Batı                       |
|    westeurope            |    Batı Avrupa                           |
|    WestIndia             |    IN Batı                               |
|    westus                |    Batı ABD                               |
|    westus2               |    ABD Batı 2                             |


## <a name="virtual-machine-usage-faq"></a>Sanal makine kullanımı SSS
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>VM dağıtımı sırasında hangi kaynaklar ücretlendirilir?    
VM 'Ler için maliyet, VM 'de çalışan tüm Premium yazılımlar, VM ile ilişkili depolama muhasebeci tarafından yönetilen disk ve VM 'den ağ bant genişliği aktarımları elde eder.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>VM 'nin kullanım CSV 'sinde Azure Hibrit Avantajı kullanıp kullancağımı nasıl anlayabilirim?
[Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kullanarak dağıtıyorsanız, buluta kendi lisansınızı getirmeden bu yana WINDOWS dışı VM ücreti üzerinden ücretlendirilirsiniz. Faturanızda, ImageType sütununda "Windows\_Server KLG" veya "Windows\_Client KLG" olduklarından, hangi Kaynak Yöneticisi sanal makinelerin Azure Hibrit Avantajı çalıştığını ayırabilirsiniz.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Temel ve standart VM türleri kullanım CSV 'si ile nasıl farklılaştırılabilir?
Hem temel hem de standart A serisi VM 'Ler sunulur. Temel bir VM dağıtırsanız, ölçüm alt kategorisinde "temel" dizesi vardır. Standart bir A serisi VM dağıtırsanız, standart varsayılan değer olduğundan VM boyutu "a1 VM" olarak görünür. Temel ve standart arasındaki farklılıklar hakkında daha fazla bilgi edinmek için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/virtual-machines/)bakın.
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Extraküçük, küçük, orta, büyük ve ExtraLarge boyutlar nelerdir?
ExtraSmall-ExtraLarge, standart\_a0 – Standart\_a4 için eski adlardır. Klasik VM kullanım kayıtlarında, bu boyutları dağıttıysanız bu kuralı kullanabilirsiniz.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Ölçüm bölgesi ve kaynak konumu arasındaki fark nedir?
Ölçüm bölgesi, ölçüm ile ilişkilendirilir. Tüm bölgeler için bir fiyat kullanan bazı Azure hizmetleri için ölçüm bölgesi alanı boş olabilir. Ancak, VM 'Lerin sanal makineler için bölge başına adanmış fiyatları olduğundan, bu alan doldurulur. Benzer şekilde, sanal makineler için kaynak konumu, VM 'nin dağıtıldığı konumdur. Her iki alandaki Azure bölgeleri de aynıdır, ancak bölge adı için farklı bir dize kuralına sahip olabilirler.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Ek bilgi alanında ImageType değeri neden boştur?
ImageType alanı yalnızca görüntülerin bir alt kümesi için doldurulur. Yukarıdaki görüntülerden birini dağıtmadıysanız ImageType boş olur.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Ek bilgisinde VMName neden boş?
VMName yalnızca bir ölçek kümesindeki VM 'Ler için ek bilgi alanına doldurulur. InstanceId alanı, ölçek olmayan küme VM 'lerinin VM adını içerir.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Ek bilgi içindeki UsageType alanında ne anlama geliyor?
Hesaplama saati, temel alınan altyapı maliyeti için kullanım olayını temsil eden Işlem saatini gösterir. UsageType,\_SW olarak kullanılıyorsa, kullanım olayı VM için Premium yazılım ücreti temsil eder.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Nasıl yaparım? Premium yazılım için ücretlendirildiğimde emin misiniz?
Gereksinimlerinize en uygun VM görüntüsünü araştırırken [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute)' ni kullanıma aldığınızdan emin olun. Görüntü, yazılım planı oranına sahiptir. Ücret için "ücretsiz" görürseniz, yazılım için ek maliyet yoktur. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Tüketilen hizmette Microsoft. ClassicCompute ve Microsoft. COMPUTE arasındaki fark nedir?
Microsoft. ClassicCompute, Azure Service Manager aracılığıyla dağıtılan klasik kaynakları temsil eder. Kaynak Yöneticisi aracılığıyla dağıtıyorsanız, Microsoft. COMPUTE tüketilen hizmette doldurulur. [Azure dağıtım modelleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)hakkında daha fazla bilgi edinin.
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Sanal makine kullanımım için InstanceId alanı neden boş?
Klasik dağıtım modeli aracılığıyla dağıtıyorsanız InstanceId dizesi kullanılamaz.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Sanal makinelerimin etiketleri neden kullanım ayrıntılarına akmıyor?
Etiketler yalnızca Kaynak Yöneticisi VM 'Ler için kullanım CSV 'sini size akar. Klasik kaynak etiketleri kullanım ayrıntılarında kullanılamaz.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Tüketilen miktar bir günden daha fazla 24 saat daha olabilir mi?
Klasik modelde, kaynaklar için faturalandırma, bulut hizmeti düzeyinde toplanır. Aynı faturalandırma ölçümünü kullanan bir bulut hizmetinde birden fazla VM 'niz varsa, kullanımınız birlikte toplanır. Kaynak Yöneticisi aracılığıyla dağıtılan VM 'Ler VM düzeyinde faturalandırılır, bu nedenle bu toplama uygulanmaz.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Fiyatlandırma sayfasındaki DS/FS/GS/LS boyutları için fiyatlandırma neden kullanılamıyor?
Premium depolama özellikli VM 'Ler, Premium olmayan depolama özellikli VM 'Lerle aynı hızda faturalandırılır. Yalnızca depolama maliyetleriniz farklılık gösterir. Daha fazla bilgi için [Depolama fiyatlandırması sayfasını](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar
Kullanım ayrıntılarınız hakkında daha fazla bilgi edinmek için bkz [. Microsoft Azure Faturanızı Anlama.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill
)

