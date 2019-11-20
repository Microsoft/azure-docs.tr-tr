---
title: En son Azure Konuk işletim sistemi sürümleri hakkında bilgi edinin | Microsoft Docs
description: Azure Cloud Services Konuk işletim sistemi için en son sürüm haberleri ve SDK uyumluluğu.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/1/2019
ms.author: raiye
ms.openlocfilehash: fa6f3e77a49afc5b16d11cb0c7d434f967c7cd53
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500251"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi
Cloud Services için en son Azure Konuk işletim sistemi sürümleri hakkında güncel bilgiler sağlar. Bu bilgiler, Konuk işletim sistemi devre dışı bırakmadan önce yükseltme yolunuzu planlamanızı sağlar. Rollerinizi [Azure Konuk işletim sistemi güncelleştirme ayarlarında][Azure Guest OS Update Settings]açıklandığı şekilde *Otomatik* Konuk işletim sistemi güncelleştirmelerini kullanacak şekilde yapılandırırsanız, bu sayfayı okumanız çok önemli değildir.

> [!IMPORTANT]
> Bu sayfa, Konuk işletim sisteminin üstünde çalışan Cloud Services Web ve çalışan rolleri için geçerlidir. IaaS sanal makineleri için **uygulanmaz** .
>
>


> [!TIP]
>  Tüm konuk işletim sistemi değişikliklerinde en güncel bildirimi almak için [Konuk işletim sistemi güncelleştirme RSS akışı] abone olun.
>
>

> [!IMPORTANT]
> Konuk işletim sisteminin yalnızca en son 2 sürümü Azure portal desteklenecek ve kullanılabilir.
>
>

Konuk işletim sistemini nasıl güncelleştireceğinizden emin misiniz? [Bunu][cloud updates] kontrol edin.

## <a name="news-updates"></a>Haber güncelleştirmeleri

###### <a name="november-1-2019"></a>**1 Kasım 2019**
Ekim Konuk işletim sistemi serbest bırakıldı.

###### <a name="october-7-2019"></a>**7 Ekim 2019**
Eylül Konuk işletim sistemi serbest bırakıldı.

###### <a name="september-4-2019"></a>**4 Eylül 2019**
Ağustos Konuk işletim sistemi serbest bırakıldı.

###### <a name="july-26-2019"></a>**26 Temmuz 2019**
Temmuz Konuk işletim sistemi serbest bırakıldı.

###### <a name="july-8-2019"></a>**8 Temmuz 2019**
Haziran Konuk işletim sistemi serbest bırakıldı.

###### <a name="june-6-2019"></a>**6 Haziran 2019**
Konuk işletim sistemi, serbest bırakılmış olabilir.

###### <a name="may-7-2019"></a>**7 Mayıs 2019**
Nisan Konuk işletim sistemi serbest bırakıldı.

###### <a name="march-26-2019"></a>**26 Mart 2019**
Mart Konuk işletim sistemi serbest bırakıldı.

###### <a name="march-12-2019"></a>**12 Mart 2019**
Şubat Konuk işletim sistemi yayımlandı.

###### <a name="february-5-2019"></a>**5 Şubat 2019**
Ocak Konuk işletim sistemi serbest bırakıldı.

###### <a name="january-24-2019"></a>**24 Ocak 2019**
Aile 6 Konuk işletim sistemi (Windows Server 2019) yayımlandı.

###### <a name="january-7-2019"></a>**7 Ocak 2019**
Aralık Konuk işletim sistemi serbest bırakıldı.

###### <a name="december-14-2018"></a>**14 Aralık 2018**
Kasım Konuk işletim sistemi serbest bırakıldı.

###### <a name="november-8-2018"></a>**8 Kasım 2018**
Ekim Konuk işletim sistemi serbest bırakıldı.

###### <a name="october-12-2018"></a>**12 Ekim 2018**
Eylül Konuk işletim sistemi serbest bırakıldı.

## <a name="releases"></a>Yayınlar

## <a name="family-6-releases"></a>Aile 6 yayınları
**Windows Server 2019**

.NET Framework yüklendi: 3,5, 4.7.2

> [!NOTE]
> .NET için Microsoft Azure SDK-3,0 [buradan][Windows Azure SDK]indirilebilir.
>
>Yükleme adımları:
>1. Lütfen MicrosoftAzureAuthoringTools*. msi ' nin eski sürümlerini kaldırın
>2. [.Net Için Azure SDK-3,0][Windows Azure SDK] ' i yükler
>3. Makinenizi yeniden başlatın
>4. Yeni bir bulut hizmeti projesi oluşturun ve tek bir çalışan rolü ekleyin
>5. İşletim sistemi ailesini 6 olarak değiştirin ve bir paket oluşturun
>6. Azure portal veya Visual Studio kullanarak paketi Azure 'a dağıtma
>


| Yapılandırma dizesi | Sürüm tarihi | Tarihi devre dışı bırak |
| --- | --- | --- |
| WA-GUEST-OS-6,12 _201910-01 | 1 Kasım 2019 | 6,14 sonrası |  
| WA-GUEST-OS-6.11 _201909-01 | 7 Ekim 2019 | 6,13 sonrası |  
|~~WA-GUEST-OS-6.10 _201908-01~~| 4 Ağustos 2019 | 1 Kasım 2019  |  
|~~WA-KONUK-IŞLETIM SISTEMI-6.9 _201907-0~~|26 Temmuz 2019 | 7 Ekim 2019 |
|~~WA-GUEST-OS-6.8 _201906-01~~|8 Temmuz 2019 |4 Ağustos 2019 |
|~~WA-GUEST-OS-6.7 _201905-01~~ |6 Haziran 2019 |26 Temmuz 2019 |
|~~WA-KONUK-IŞLETIM SISTEMI-6,6 _201904-01~~ |7 Mayıs 2019 |8 Temmuz 2019 |
|~~WA-KONUK-IŞLETIM SISTEMI-6.5 _201903-01~~ |26 Mart 2019 |6 Haziran 2019 |
|~~WA-GUEST-OS-6.4 _201902-01~~ |12 Mart 2019 |7 Mayıs 2019 |
|~~WA-KONUK-IŞLETIM SISTEMI-6.3 _201901-01~~ |5 Şubat 2019 |26 Mart 2019 |
|~~WA-GUEST-OS-6.2 _201812-01~~ |24 Ocak 2019 |12 Mart 2019 |
|~~WA-KONUK-IŞLETIM SISTEMI-6.1 _201811-01~~ |24 Ocak 2019 |5 Şubat 2019 |

## <a name="family-5-releases"></a>Aile 5 yayınları
**Windows Server 2016**

.NET Framework yüklendi: 3,5, 4,6

> [!NOTE]
> OS ailesi 5 için RDP parolasının en az 10 karakter olması gerekir.
>


| Yapılandırma dizesi | Sürüm tarihi | Tarihi devre dışı bırak |
| --- | --- | --- |
| WA-GUEST-OS-5.36 _201910-01 | 1 Kasım 2019 | 5,38 sonrası |  
| WA-GUEST-OS-5.35 _201909-01 | 7 Ekim 2019 | 5,37 sonrası |  
|~~WA-GUEST-OS-5.34 _201908-01~~|  4 Ağustos 2019  |  5,36 sonrası |  
|~~WA-GUEST-OS-5.33 _201907-01~~| 26 Temmuz 2019 | 7 Ekim 2019 |  
|~~WA-GUEST-OS-5.32 _201906-01~~|8 Temmuz 2019 |4 Ağustos 2019 |
|~~WA-GUEST-OS-5.31 _201905-01~~ |6 Haziran 2019 |26 Temmuz 2019 |
|~~WA-GUEST-OS-5.30 _201904-01~~ |7 Mayıs 2019 |8 Temmuz 2019 |
|~~WA-GUEST-OS-5.29 _201903-01~~ |26 Mart 2019 |6 Haziran 2019 |
|~~WA-GUEST-OS-5.28 _201902-01~~ |12 Mart 2019 |7 Mayıs 2019 |
|~~WA-GUEST-OS-5.27 _201901-01~~ |5 Şubat 2019 |26 Mart 2019 |
|~~WA-GUEST-OS-5.26 _201812-01~~ |7 Ocak 2019 |12 Mart 2019 |
|~~WA-KONUK-IŞLETIM SISTEMI-5,25 _201811-01~~ |14 Aralık 2018 |5 Şubat 2019 |
|~~WA-GUEST-OS-5.24 _201810-01~~ |8 Kasım 2018 |7 Ocak 2019 |
|~~WA-GUEST-OS-5.23 _201809-01~~ |12 Ekim 2018 |14 Aralık 2018 |

## <a name="family-4-releases"></a>Aile 4 yayınları
**Windows Server 2012 R2**

.NET Framework yüklendi: 3,5, 4.5.1, 4.5.2

| Yapılandırma dizesi | Sürüm tarihi | Tarihi devre dışı bırak |
| --- | --- | --- |
| WA-GUEST-OS-4.71 _201910-01 | 1 Kasım 2019 | 4,73 sonrası |  
| WA-GUEST-OS-4.70 _201909-01 | 7 Ekim 2019 | 4,72 sonrası |  
|~~WA-GUEST-OS-4.69 _201908-01~~| 4 Ağustos 2019 | 1 Kasım 2019 |  
|~~WA-GUEST-OS-4.68 _201907-01~~| 26 Temmuz 2019  | 7 Ekim 2019 |
|~~WA-GUEST-OS-4.67 _201906-01~~| 8 Temmuz 2019 |4 Ağustos 2019 |
|~~WA-GUEST-OS-4.66 _201905-01~~ |6 Haziran 2019 |26 Temmuz 2019 |
|~~WA-GUEST-OS-4.65 _201904-01~~ |7 Mayıs 2019 |8 Temmuz 2019 |
|~~WA-GUEST-OS-4.64 _201903-01~~ |26 Mart 2019 |6 Haziran 2019 |
|~~WA-GUEST-OS-4.63 _201902-01~~ |12 Mart 2019 |7 Mayıs 2019 |
|~~WA-GUEST-OS-4.62 _201901-01~~ |5 Şubat 2019 |26 Mart 2019 |
|~~WA-GUEST-OS-4.61 _201812-01~~ |7 Ocak 2019 |12 Mart 2019 |
|~~WA-GUEST-OS-4.60 _201811-01~~ |14 Aralık 2018 |5 Şubat 2019 |
|~~WA-GUEST-OS-4.59 _201810-01~~ |8 Kasım 2018 |7 Ocak 2019 |
|~~WA-GUEST-OS-4.58 _201809-01~~ |12 Ekim 2018 |14 Aralık 2018 |

## <a name="family-3-releases"></a>Aile 3 yayınları
**Windows Server 2012**

.NET Framework yüklendi: 3,5, 4,5

| Yapılandırma dizesi | Sürüm tarihi | Tarihi devre dışı bırak |
| --- | --- | --- |
| WA-GUEST-OS-3.78 _201910-01 | 1 Kasım 2019 | 3,80 sonrası |  
| WA-GUEST-OS-3.77 _201909-01 | 7 Ekim 2019 | 3,79 sonrası |  
|~~WA-GUEST-OS-3.76 _201908-01~~|  4 Ağustos 2019  |  1 Kasım 2019  |  
|~~WA-GUEST-OS-3,75 _201907-01~~| 26 Temmuz 2019 | 7 Ekim 2019 |
|~~WA-GUEST-OS-3.74 _201906-01~~| 8 Temmuz 2019 |4 Ağustos 2019 |
|~~WA-GUEST-OS-3.73 _201905-01~~ |6 Haziran 2019 |26 Temmuz 2019 |
|~~WA-GUEST-OS-3.72 _201904-01~~ |7 Mayıs 2019 |8 Temmuz 2019 |
|~~WA-GUEST-OS-3.71 _201903-01~~ |26 Mart 2019 |6 Haziran 2019 |
|~~WA-GUEST-OS-3.70 _201902-01~~ |12 Mart 2019 |7 Mayıs 2019 |
|~~WA-GUEST-OS-3.69 _201901-01~~ |5 Şubat 2019 |26 Mart 2019 |
|~~WA-GUEST-OS-3.68 _201812-01~~ |7 Ocak 2019 |12 Mart 2019 |
|~~WA-GUEST-OS-3.67 _201811-01~~ |14 Aralık 2018 |5 Şubat 2019 |
|~~WA-GUEST-OS-3.66 _201810-01~~ |8 Kasım 2018 |7 Ocak 2019 |
|~~WA-GUEST-OS-3.65 _201809-01~~ |12 Ekim 2018 |14 Aralık 2018 |

## <a name="family-2-releases"></a>2\. aile yayınları
**Windows Server 2008 R2 SP1**

.NET Framework yüklendi: 3,5 (2,0 ve 3,0 içerir), 4,5

| Yapılandırma dizesi | Sürüm tarihi | Tarihi devre dışı bırak |
| --- | --- | --- |
| WA-GUEST-OS-2.91 _201910-01 | 1 Kasım 2019 | 2,93 sonrası |  
| WA-GUEST-OS-2.90 _201909-01 | 7 Ekim 2019 | 2,92 sonrası |  
|~~WA-GUEST-OS-2.89 _201908-01~~| 4 Ağustos 2019 | 1 Kasım 2019 |  
|~~WA-GUEST-OS-2.88 _201907-01~~| 26 Temmuz 2019 | 7 Ekim 2019 |
|~~WA-GUEST-OS-2.87 _201906-01~~|8 Temmuz 2019 | 4 Ağustos 2019 |
|~~WA-GUEST-OS-2.86 _201905-01~~ |6 Haziran 2019 |26 Temmuz 2019 |
|~~WA-GUEST-OS-2.85 _201904-01~~ |7 Mayıs 2019 |8 Temmuz 2019 |
|~~WA-GUEST-OS-2.84 _201903-01~~ |26 Mart 2019 |6 Haziran 2019 |
|~~WA-GUEST-OS-2.83 _201902-01~~ |12 Mart 2019 |7 Mayıs 2019 |
|~~WA-GUEST-OS-2.82 _201901-01~~ |5 Şubat 2019 |26 Mart 2019 |
|~~WA-GUEST-OS-2.81 _201812-01~~ |7 Ocak 2019 |12 Mart 2019 |
|~~WA-GUEST-OS-2.80 _201811-01~~ |14 Aralık 2018 |5 Şubat 2019 |
|~~WA-GUEST-OS-2.79 _201810-01~~ |8 Kasım 2018 |7 Ocak 2019 |
|~~WA-GUEST-OS-2.78 _201809-01~~ |12 Ekim 2018 |14 Aralık 2018 |

## <a name="msrc-patch-updates"></a>MSRC Patch güncelleştirmeleri
Her aylık Konuk işletim sistemi sürümüne dahil olan düzeltme eklerinin listesi [burada][patches]bulunabilir.

## <a name="sdk-support"></a>SDK desteği
[Azure SDK 'nın emeklilik ilkesi][retire policy sdk] yalnızca 2,2 ' ün üzerinde desteklenen sürümlerin desteklendiğini gösteriyorsa, belirli Konuk işletim sistemi aileleri önceki sürümleri kullanmanıza izin verir. Desteklenen en son SDK 'yi her zaman kullanmanız gerekir.

| Konuk işletim sistemi ailesi | Uyumlu SDK sürümleri |
| --- | --- |
| 6 |Sürüm 2.9.6 + |
| 5 |Sürüm 2.9.5.1 + |
| 4 |Sürüm 2.1 + |
| 3 |Sürüm 1.8 + |
| 2 |Sürüm 1.3 + |
| 1 |Sürüm 1.0 + |

## <a name="guest-os-release-information"></a>Konuk işletim sistemi sürüm bilgileri
Konuk işletim sistemi sürümleri için önemli olan üç tarih vardır: **yayın** tarihi, **devre dışı** tarihi ve **sona erme** tarihi. Konuk işletim sistemi portalda olduğunda kullanılabilir kabul edilir ve hedef Konuk işletim sistemi olarak seçilebilir. Konuk işletim sistemi **devre dışı** bir tarihe ulaştığında, Azure 'dan kaldırılır. Ancak Konuk işletim sisteminin hedeflediği tüm bulut Hizmetleri normal şekilde çalışmaya devam edecektir.

**Devre dışı bırakılan** tarih ve **sona erme** tarihi arasındaki pencere, bir konuk işletim sisteminden diğerine kolayca geçiş yapmak için bir arabellek sağlar. Konuk işletim sistemi olarak *Otomatik* ' i kullanıyorsanız, her zaman en son sürümü kullanmaya başlayacaksınız ve süresi dolmadan önce endişelenmeniz gerekmez.

**Sona erme** tarihi geçtiğinde, hala bu konuk işletim sistemini kullanan tüm bulut hizmetleri durdurulur, silinir veya yükseltmeye zorlanır. Bu kullanımdan [kaldırma ilkesi hakkında][retirepolicy]daha fazla bilgi edinebilirsiniz.

## <a name="guest-os-family-version-explanation"></a>Konuk işletim sistemi ailesi-sürüm açıklaması
Konuk işletim sistemi aileleri, Microsoft Windows Server 'ın yayınlanan sürümlerini temel alır. Konuk IŞLETIM sistemi, Azure Cloud Services üzerinde çalıştığı temel işletim sistemidir. Her konuk işletim sisteminde bir aile, sürüm ve sürüm numarası vardır.

* **Konuk işletim sistemi ailesi**  
  Konuk IŞLETIM sisteminin dayandığı bir Windows Server işletim sistemi sürümü. Örneğin, *Family 3* , Windows Server 2012 ' i temel alır.
* **Konuk işletim sistemi sürümü**  
  Konuk işletim sistemi ailesi görüntüsüne ve yeni Konuk işletim sistemi sürümünün oluşturulduğu tarihte kullanılabilen ilgili [Microsoft Güvenlik Yanıt Merkezi (MSRC)][msrc] düzeltme eklerine özgüdür. Tüm düzeltme ekleri dahil edilebilir.

    Sayılar 0 ' dan başlar ve yeni bir güncelleştirme kümesi eklendiğinde 1 ' i artırır. Sondaki sıfırlar yalnızca önemli ise gösterilir. Yani sürüm 2,10, sürüm 2,1 ' den farklı, daha sonraki bir sürümdür.
* **Konuk işletim sistemi sürümü**  
  Konuk işletim sistemi sürümünün yeniden yayımlanan kopyası. Microsoft, test sırasında sorunları bulursa bir yeniden yayımlama meydana gelir; değişiklik gerektirme. En son sürüm, tüm önceki sürümlerin yerini alır, herkese açıktır. Azure portal, kullanıcıların belirli bir sürüm için en son sürümü seçmesine izin verir. Önceki bir sürümde çalışan dağıtımlar genellikle hatanın önem derecesine bağlı olarak yükseltilmez.

Aşağıdaki örnekte, 2 aile, 12 sürümdür ve "REL2" sürümüdür.

**Konuk işletim sistemi sürümü** -2,12 REL2

**Bu yayın Için yapılandırma dizesi** -WA-Guest-OS-2.12 _201208-02

Konuk işletim sistemi için yapılandırma dizesinde, bu yayında gömülü olan bu bilgiler, bu sürüm için hangi MSRC düzeltme eklerinin kabul edileceğini gösteren bir tarihle birlikte bulunur. Bu örnekte, Windows Server 2008 R2 için üretilen ve 2012 ile birlikte bulunan MSRC yamaları ekleme için kabul edilir. Yalnızca bu Windows Server sürümüne uygulanan düzeltme ekleri dahildir. Örneğin, bir MSRC düzeltme eki Microsoft Office için geçerliyse, bu ürün Windows Server temel görüntüsünün parçası olmadığından dahil edilmez.

## <a name="guest-os-system-update-process"></a>Konuk IŞLETIM sistemi sistemi güncelleştirme işlemi
Bu sayfa yaklaşan konuk işletim sistemi sürümleri hakkında bilgiler içerir. Müşteriler, "otomatik" güncelleştirme olarak ayarlandıysa bulut hizmeti rollerinin yeniden başlatılması için bir sürüm ne zaman meydana geldiğinde haberdar olmak istediğini gösterdi. Konuk işletim sistemi yayınları genellikle her ayın ikinci Salı günü gerçekleşen MSRC güncelleştirme sürümünden sonra 2-3 hafta sonra gerçekleşir. Yeni yayınlar, her konuk işletim sistemi ailesine yönelik tüm ilgili MSRC düzeltme eklerini içerir.

Microsoft Azure sürekli olarak güncelleştirmeleri serbest bırakır. Konuk işletim sistemi, ardışık düzendeki yalnızca bir güncelleştirmedir. Bir sürüm, burada listelemek için çok fazla sayıda faktörden etkilenebilir. Ayrıca, Azure, yüzlerce binlerce makinede çalışır. Bu, rollerinizin yeniden başlatılacağını tam bir tarih ve saat vermek olanaksız hale gelir. Yeniden başlatmaları sınırlamak için bir plan üzerinde çalışıyoruz.

Konuk işletim sisteminin yeni bir sürümü yayımlandığında, Azure genelinde tamamen yaymak zaman alabilir. Hizmetler yeni Konuk işletim sistemine güncelleştirildiğinden, güncelleştirme etki alanlarının yeniden başlatılması gerekir. "Otomatik" güncelleştirmeleri kullanacak şekilde ayarlanan hizmetler, önce bir yayın alır. Güncelleştirmeden sonra, Azure portal hizmetiniz için yeni Konuk işletim sistemi sürümünün listelendiğini görürsünüz. Bu süre boyunca Rereleases olabilir. Bazı sürümler uzun süreler içinde dağıtılabilir ve otomatik yükseltme yeniden başlatmalar, resmi sürüm tarihinden sonraki birkaç hafta boyunca gerçekleşmeyebilir. Konuk işletim sistemi kullanılabilir olduktan sonra bu sürümü Portal 'dan veya yapılandırma dosyanızda açıkça seçebilirsiniz.

Yeniden başlatmalar hakkında harika bilgiler ve konuk ve konak işletim sistemi güncelleştirmeleri hakkında daha fazla bilgi almak için, [işletim sistemi yükseltmeleri nedeniyle rol örneği yeniden başlatmaları][restarts]başlıklı MSDN blog gönderisine bakın.

Konuk işletim sistemini el ile güncelleştirirseniz daha fazla bilgi için [Konuk işletim sistemi kullanımdan kaldırma ilkesine][retirepolicy] bakın.

## <a name="guest-os-supportability-and-retirement-policy"></a>Konuk işletim sistemi desteklenebilirlik ve emeklilik ilkesi
Konuk işletim sistemi desteklenebilirlik ve emeklilik ilkesi [burada][retirepolicy]açıklanmıştır.

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Konuk işletim sistemi güncelleştirme RSS akışı]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
