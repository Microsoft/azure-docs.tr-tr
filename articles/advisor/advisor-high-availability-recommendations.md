---
title: Azure Advisor ile uygulamanızın kullanılabilirliğini geliştirme | Microsoft Docs
description: Azure dağıtımlarınızın yüksek oranda kullanılabilirliğini geliştirmek için Azure Advisor 'ı kullanın.
services: advisor
documentationcenter: NA
author: saket-ms
ms.author: sagupt
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: dd1b898adf4c4cdff45e05427757d90d5f80bf25
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145337"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Azure Danışmanı ile uygulamanızın kullanılabilirliğini geliştirme

Azure Advisor, iş açısından kritik uygulamalarınızın devamlılığını sağlamanıza ve iyileştirmenize yardımcı olur. Danışman panosunun **yüksek kullanılabilirlik** sekmesinden danışman ile yüksek kullanılabilirlik önerileri edinebilirsiniz.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Sanal makine hata toleransı sağlayın

Uygulamanıza yedeklilik sağlamak için bir kullanılabilirlik kümesinde iki veya daha fazla sanal makinenin gruplandırılması önerilir. Danışman, bir kullanılabilirlik kümesinin parçası olmayan sanal makineleri tanımlar ve bunları bir kullanılabilirlik kümesine taşımayı önerir. Bu yapılandırma, planlı veya plansız bir bakım olayı sırasında en az bir sanal makinenin kullanılabilir olmasını ve Azure sanal makine SLA 'sını karşılamasını sağlar. Sanal makine için bir kullanılabilirlik kümesi oluşturmayı veya var olan bir kullanılabilirlik kümesine sanal makineyi eklemeyi seçebilirsiniz.

> [!NOTE]
> Bir kullanılabilirlik kümesi oluşturmayı seçerseniz, buna en az bir sanal makine eklemeniz gerekir. Bir kesinti sırasında en az bir makinenin kullanılabilir olduğundan emin olmak için bir kullanılabilirlik kümesinde iki veya daha fazla sanal makineyi gruplandırmalarını öneririz.

## <a name="ensure-availability-set-fault-tolerance"></a>Kullanılabilirlik kümesi hata toleransı sağlayın

Uygulamanıza yedeklilik sağlamak için bir kullanılabilirlik kümesinde iki veya daha fazla sanal makinenin gruplandırılması önerilir. Danışman tek bir sanal makine içeren kullanılabilirlik kümelerini tanımlar ve kendisine bir veya daha fazla sanal makine eklenmesini önerir. Bu yapılandırma, planlı veya plansız bir bakım olayı sırasında en az bir sanal makinenin kullanılabilir olmasını ve Azure sanal makine SLA 'sını karşılamasını sağlar. Bir sanal makine oluşturmayı veya var olan bir sanal makineyi kullanılabilirlik kümesine eklemeyi seçebilirsiniz.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Veri güvenilirliğini geliştirmek için Yönetilen Diskler kullanın

Depolama hesaplarını veya depolama ölçek birimlerini paylaşan diskler içeren bir kullanılabilirlik kümesindeki sanal makineler, kesintiler sırasında tek depolama ölçek birimi hatalarıyla dayanıklı değildir. Danışman, bu kullanılabilirlik kümelerini tanımlar ve Azure yönetilen disklere geçiş yapmanızı önerir. Bu, kullanılabilirlik kümesindeki farklı sanal makinelerin disklerinin tek bir hata noktası oluşmasını önlemek için yeterince yalıtılmış olmasını sağlar. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Application Gateway hata toleransı sağlayın

Bu öneri, uygulama ağ geçitleri tarafından desteklenen görev açısından kritik uygulamaların iş sürekliliği olmasını sağlar. Danışman, hata toleransı için yapılandırılmamış uygulama ağ geçidi örneklerini tanımlar ve gerçekleştirebileceğiniz düzeltme eylemlerini önerir. Danışman, orta veya büyük tek örnekli uygulama ağ geçitlerini tanımlar ve en az bir örnek eklemeyi önerir. Ayrıca, tek veya çok örnekli küçük uygulama ağ geçitlerini tanımlar ve orta veya büyük SKU 'Lara geçiş yapmanızı önerir. Danışman, uygulama ağ geçidi örneklerinizin bu kaynaklara yönelik geçerli SLA gereksinimlerini karşılayacak şekilde yapılandırıldığından emin olmak için bu eylemleri önerir.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Sanal makine verilerinizi yanlışlıkla silinmeye karşı koruyun

Sanal makine yedeklemesini ayarlama, iş açısından kritik verilerinizin kullanılabilirliğini sağlar ve yanlışlıkla silinmeye veya bozulmalara karşı koruma sağlar. Advisor, yedeklemenin etkinleştirilmediği sanal makineleri tanımlar ve yedeklemeyi etkinleştirmeyi önerir. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>İhtiyacınız olduğunda Azure bulut uzmanlarıyla erişime sahip olduğunuzdan emin olun

İş açısından kritik bir iş yükü çalıştırırken, gerektiğinde teknik desteğe erişebilmek önemlidir. Danışman destek planına dahil olmayan ve teknik destek içeren bir seçeneğe yükseltmeyi öneren potansiyel iş açısından kritik abonelikleri tanımlar.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure sorunları sizi etkiliyorsa bildirim almak için Azure hizmet durumu uyarıları oluşturun

Azure hizmet sorunları sizi etkiliyorsa bildirim almak için Azure hizmet durumu uyarılarını ayarlamayı öneririz. [Azure hizmet durumu](https://azure.microsoft.com/features/service-health/) , bir Azure hizmet sorunundan etkilendiğiniz zaman kişiselleştirilmiş rehberlik ve destek sağlayan ücretsiz bir hizmettir. Advisor, uyarıları yapılandırılmamış ve bir tane oluşturulmasını öneren abonelikleri tanımlar.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Dayanıklılık için Traffic Manager uç noktalarını yapılandırma

Belirli bir uç nokta başarısız olursa, birden fazla uç nokta ile daha yüksek kullanılabilirliği olan Traffic Manager profiller. Uç noktaların farklı bölgelerde yerleştirilmesi hizmet güvenilirliğini artırır. Danışman yalnızca bir uç nokta olduğu ve başka bir bölgede en az bir uç nokta eklenmesini öneren Trafik Yöneticisi profillerini tanımlar.

Yakınlık yönlendirme için yapılandırılmış bir Traffic Manager profilindeki tüm uç noktalar aynı bölgedeyse, diğer bölgelerdeki kullanıcılar bağlantı gecikmeleri yaşayabilir. Bir uç noktayı başka bir bölgeye eklemek veya taşımak, genel performansı iyileştirir ve bir bölgedeki tüm uç noktalar başarısız olursa daha iyi kullanılabilirlik sağlar. Advisor, tüm uç noktaların aynı bölgede olduğu yakınlık yönlendirme için yapılandırılmış Traffic Manager profillerini tanımlar. Bir uç noktayı başka bir Azure bölgesine eklemeyi veya taşımayı önerir.

Coğrafi yönlendirme için bir Traffic Manager profili yapılandırılmışsa trafik, tanımlanan bölgelere göre uç noktalara yönlendirilir. Bir bölge başarısız olursa, önceden tanımlanmış yük devretme yoktur. Bölgesel gruplandırmanın "tümü (Dünya)" olarak yapılandırıldığı bir uç noktaya sahip olma, trafiğin bırakılmasına ve hizmet kullanılabilirliğini iyileştirmesine engel olur. Advisor, bölgesel gruplandırmada "tümü (Dünya)" olarak yapılandırılmış bir uç noktanın olmadığı coğrafi yönlendirme için yapılandırılmış Traffic Manager profillerini tanımlar. "Tümü (Dünya)" uç noktası oluşturmak için yapılandırmanın değiştirilmesini önerir.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Yanlışlıkla üzerine yazma veya silme işleminden sonra verileri kaydetmek ve kurtarmak için Azure depolama hesabınızda geçici silme kullanın

Silinen Blobların kalıcı olarak silinmesi yerine geçici olarak silinen bir duruma geçmesi için depolama hesabınızda [geçici silme](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) özelliğini etkinleştirin. Verilerin üzerine yazıldığında, üzerine yazılan verilerin durumunu kaydetmek için geçici silinen bir anlık görüntü oluşturulur. Geçici silme kullanmak, yanlışlıkla silme veya üzerine yazma işlemleri yaparsanız kurtarmanızı sağlar. Danışman, geçici silme özelliği etkinleştirilmemiş Azure depolama hesaplarını tanımlar ve bunu etkinleştirmenizi önerir.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>VPN ağ geçidinizi bağlantı dayanıklılığı için etkin-etkin olarak yapılandırma

Etkin-etkin yapılandırmada, her iki VPN ağ geçidi örneği de şirket içi VPN cihazınıza S2S VPN tünellerini kurar. Planlı bir bakım olayı veya plansız bir olay bir ağ geçidi örneğinde olduğunda, trafik otomatik olarak diğer etkin IPSec tüneline geçiş yapılır. Azure Danışmanı, etkin-etkin olarak yapılandırılmayan VPN ağ geçitlerini belirler ve bunları yüksek kullanılabilirlik için yapılandırmanızı önerir.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Üretim iş yüklerinizi çalıştırmak için üretim VPN ağ geçitlerini kullanın

Azure Danışmanı, temel SKU 'SU olan tüm VPN ağ geçitlerini denetler ve bunun yerine bir üretim SKU 'SU kullanmanızı önerir. Temel SKU, geliştirme ve test amacıyla tasarlanmıştır. Üretim SKU 'Ları daha fazla sayıda tünel, BGP desteği, etkin-etkin yapılandırma seçenekleri, Özel IPSec/ıKE ilkesi ve daha yüksek kararlılık ve kullanılabilirlik sağlar.

## <a name="repair-invalid-log-alert-rules"></a>Geçersiz günlük uyarısı kurallarını Onar

Azure Danışmanı, durumu bölümünde geçersiz sorguları belirtilmiş olan uyarı kurallarını algılar. Günlük uyarı kuralları Azure İzleyici'de oluşturulur ve belirtilen zaman aralıklarında analiz sorguları çalıştırmak için kullanılır. Sorgu sonuçları, uyarıyı tetiklemenin gerekip gerekmediğini belirler. Başvurulan kaynaklarda, tablolarla veya komutlarda zamanla değişiklik oldukça analiz sorguları geçersiz hale gelir. Danışman, otomatik devre dışı bırakılmasını ve Azure 'da kaynaklarınızın kapsamını izlemeyi güvence altına almak için uyarı kuralındaki sorguyu düzeltmeniz önerilir. [Uyarı kuralları sorunlarını giderme hakkında daha fazla bilgi edinin](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Cosmos DB koleksiyonunuzda tutarlı dizin oluşturma modu yapılandırma

Geç dizin oluşturma moduyla yapılandırılan Azure Cosmos DB kapsayıcıları sorgu sonuçlarının yeniliği etkileyebilir. Danışman bu şekilde yapılandırılmış kapsayıcıları algılar ve tutarlı moda geçiş yapmanızı önerir. [Cosmos DB 'de dizin oluşturma ilkeleri hakkında daha fazla bilgi edinin](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Azure Cosmos DB kapsayıcılarınızı bölüm anahtarı ile yapılandırın

Azure Advisor, sağlanan depolama kotasına yaklaştığı Azure Cosmos DB bölümlenmemiş koleksiyonları tanımlayacaktır. Bu koleksiyonları, hizmet tarafından otomatik olarak ölçeklenebilmeleri için bölüm anahtarı tanımıyla yeni koleksiyonlara geçirmeyi öneririz. [Bölüm anahtarı seçme hakkında daha fazla bilgi edinin](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Azure Cosmos DB .NET SDK'nızı Nuget'ten en son sürüme yükseltin

Azure Advisor, .NET SDK 'sının eski sürümlerini kullanan Azure Cosmos DB hesaplarını belirler ve en son düzeltmeler, performans iyileştirmeleri ve yeni özellik özellikleri için NuGet 'den en son sürüme yükseltmeyi önerir. [.NET SDK Cosmos DB hakkında daha fazla bilgi edinin](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Azure Cosmos DB Java SDK'nızı Maven'dan en son sürüme yükseltin

Azure Advisor, Java SDK 'sının eski sürümlerini kullanan Azure Cosmos DB hesaplarını belirler ve en son düzeltmeler, performans iyileştirmeleri ve yeni özellik özellikleri için Maven 'den en son sürüme yükseltmeyi önerir. [Java SDK Cosmos DB hakkında daha fazla bilgi edinin](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Azure Cosmos DB Spark Bağlayıcınızı Maven'dan en son sürüme yükseltin

Azure Advisor, Cosmos DB Spark bağlayıcısının eski sürümlerini kullanan Azure Cosmos DB hesaplarını belirler ve en son düzeltmeler, performans iyileştirmeleri ve yeni özellik özellikleri için Maven 'nin en son sürümüne yükseltme yapmanızı önerir. [Cosmos DB Spark Bağlayıcısı hakkında daha fazla bilgi edinin](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Sanal makine çoğaltmasını etkinleştirme
Başka bir bölgeye çoğaltma özelliği etkinleştirilmemiş sanal makineler, bölgesel kesintilere dayanıklı değildir. Sanal makinelerin çoğaltılması, Azure bölge kesintisi sırasında olumsuz iş etkisini azaltır. Danışman, çoğaltma etkinleştirilmemiş olan VM 'Leri algılar ve bir kesinti olması durumunda sanal makinelerinizi uzak bir Azure bölgesinde hızlı bir şekilde getirebilmeniz için çoğaltmayı etkinleştirmeyi önerir. [Sanal makine çoğaltma hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Danışman 'de yüksek kullanılabilirlik önerilerine erişme

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

2.  Advisor panosunda, **yüksek kullanılabilirlik** sekmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi için bkz.
* [Azure Advisor 'a giriş](advisor-overview.md)
* [Danışman’ı kullanmaya başlama](advisor-get-started.md)
* [Danışman maliyet önerileri](advisor-cost-recommendations.md)
* [Advisor performans önerileri](advisor-performance-recommendations.md)
* [Danışman güvenlik önerileri](advisor-security-recommendations.md)
* [Danışman Işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)
