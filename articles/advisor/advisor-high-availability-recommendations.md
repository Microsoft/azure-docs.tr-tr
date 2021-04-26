---
title: Danışman ile uygulamanızın güvenilirliğini geliştirme
description: İş açısından kritik Azure dağıtımlarınızda güvenilirliği sağlamak ve geliştirmek için Azure Advisor 'ı kullanın.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: c96b47f1ee145129f4c14c6646f93abeb8a5aac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579969"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Azure Advisor 'ı kullanarak uygulamanızın güvenilirliğini geliştirme

Azure Advisor, iş açısından kritik uygulamalarınızın devamlılığını sağlamanıza ve iyileştirmenize yardımcı olur. Danışman panosunun **güvenilirlik** sekmesinde, Advisor 'ın güvenilirlik önerilerini edinebilirsiniz.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Denetim noktası ağ sanal gereç görüntünüzün sürümünü denetleyin

Danışman, sanal makinenizin, platform bakım işlemleri sırasında ağ bağlantısını kaybetmesinin bilinen bir denetim noktası görüntüsünün sürümünü çalıştırıp çalıştırmadığını tanımlayabilir. Danışman önerisi, görüntünün bu sorunu gideren daha yeni bir sürümüne yükseltmenize yardımcı olur. Bu denetim, daha iyi ağ bağlantısı sayesinde iş sürekliliği sağlayacaktır.

## <a name="ensure-application-gateway-fault-tolerance"></a>Application Gateway hata toleransı sağlayın

Bu öneri, uygulama ağ geçitleri tarafından desteklenen görev açısından kritik uygulamaların iş sürekliliği olmasını sağlar. Advisor, hata toleransı için yapılandırılmamış uygulama ağ geçidi örneklerini tanımlar. Ardından, gerçekleştirebileceğiniz düzeltme eylemlerini önerir. Danışman orta veya büyük tek örnekli uygulama ağ geçitlerini tanımlar ve en az bir örnek eklemeyi önerir. Ayrıca tek örnekli ve birden çok örnekli küçük uygulama ağ geçitlerini tanımlar ve bunları orta veya büyük SKU 'Lara geçirmeyi önerir. Danışman, uygulama ağ geçidi örneklerinizin bu kaynaklara yönelik geçerli SLA gereksinimlerini karşılayacak şekilde yapılandırılmasını sağlamak için bu eylemleri önerir.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Sanal makine verilerinizi yanlışlıkla silinmeye karşı koruyun

Sanal makine yedeklemesini ayarlama, iş açısından kritik verilerinizin kullanılabilirliğini sağlar ve yanlışlıkla silinmeye veya bozulmalara karşı koruma sağlar. Advisor yedeklemenin etkin olmadığı ve yedeklemenin etkinleştirilmesini önerdiği sanal makineleri tanımlar. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>İhtiyacınız olduğunda Azure uzmanlarıyla erişime sahip olduğunuzdan emin olun

İş açısından kritik bir iş yükü çalıştırırken, ihtiyacınız olduğunda teknik desteğe erişebilmek önemlidir. Danışman, destek planlarına dahil olan teknik desteğe sahip olmayan iş açısından kritik abonelikleri tanımlar. Teknik destek içeren bir seçeneğe yükseltmeyi önerir.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure sorunları sizi etkiliyorsa bildirim almak için Azure hizmet durumu uyarıları oluşturun

Azure hizmeti sorun sizi etkiliyorsa size bildirimde bulunulmasını sağlamak için Azure hizmet durumu uyarılarını ayarlamayı öneririz. [Azure hizmet durumu](https://azure.microsoft.com/features/service-health/) , bir Azure hizmet sorunundan etkilendiğiniz zaman kişiselleştirilmiş rehberlik ve destek sağlayan ücretsiz bir hizmettir. Danışman, uyarıları yapılandırılmış olmayan ve bunları yapılandırmanızı öneren abonelikleri tanımlar.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Dayanıklılık için Traffic Manager uç noktalarını yapılandırma

Belirli bir uç nokta başarısız olursa, birden fazla uç nokta ile Azure Traffic Manager profillerinin kullanılabilirliği daha yüksektir. Uç noktaların farklı bölgelerde yerleştirilmesi hizmet güvenilirliğini artırır. Danışman yalnızca bir uç noktanın bulunduğu ve başka bir bölgede en az bir uç nokta eklenmesini öneren Trafik Yöneticisi profillerini tanımlar.

Yakınlık yönlendirme için yapılandırılmış bir Traffic Manager profilindeki tüm uç noktalar aynı bölgedeyse, diğer bölgelerdeki kullanıcılar bağlantı gecikmeleri yaşayabilir. Bir uç noktayı başka bir bölgeye eklemek veya taşımak, genel performansı iyileştirir ve bir bölgedeki tüm uç noktalar başarısız olursa daha iyi kullanılabilirlik sağlar. Advisor, tüm uç noktaların aynı bölgede olduğu yakınlık yönlendirme için yapılandırılmış Traffic Manager profillerini tanımlar. Bir uç noktayı başka bir Azure bölgesine eklemeyi veya taşımayı önerir.

Coğrafi yönlendirme için bir Traffic Manager profili yapılandırılmışsa trafik, tanımlanan bölgelere göre uç noktalara yönlendirilir. Bir bölge başarısız olursa, önceden tanımlanmış yük devretme yoktur. Bölgesel gruplandırmanın **Tümü (Dünya)** olarak yapılandırıldığı bir uç noktanız varsa, bırakılan trafikten kaçınabilir ve hizmet kullanılabilirliğini geliştirebilirsiniz. Advisor, coğrafi yönlendirme için yapılandırılmış Traffic Manager profillerini tanımlar. Bu, **tüm (Dünya)** bölgesel gruplandırmasına sahip olacak şekilde yapılandırılmış bir uç nokta yok. Bir uç nokta **(Dünya)** yapmak için yapılandırmanın değiştirilmesini önerir.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Yanlışlıkla üzerine yazma veya silme işleminden sonra verileri kaydetmek ve kurtarmak için Azure depolama hesabınızda geçici silme kullanın

Silinen Blobların kalıcı olarak silinmesi yerine geçici olarak silinen bir duruma geçmesi için depolama hesabınızda [geçici silme](../storage/blobs/soft-delete-blob-overview.md) özelliğini etkinleştirin. Verilerin üzerine yazıldığında, üzerine yazılan verilerin durumunu kaydetmek için geçici silinen bir anlık görüntü oluşturulur. Geçici silme kullanmak, yanlışlıkla silme veya üzerine yazma işlemleri için kurtarma yapmanıza olanak sağlar. Danışman, geçici silme özelliği etkinleştirilmemiş Azure depolama hesaplarını tanımlar ve bunu etkinleştirmenizi önerir.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>VPN ağ geçidinizi bağlantı dayanıklılığı için etkin-etkin olarak yapılandırma

Etkin-etkin yapılandırmada, her iki VPN ağ geçidi örneği de şirket içi VPN cihazınıza S2S VPN tünellerini kurar. Planlı bir bakım olayı veya plansız bir olay bir ağ geçidi örneğinde olduğunda, trafik otomatik olarak diğer etkin IPSec tüneline geçiş yapılır. Azure Advisor, etkin-etkin olarak yapılandırılmayan VPN ağ geçitlerini tanımlar ve bunları yüksek kullanılabilirlik için yapılandırmanızı önerir.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Üretim iş yüklerinizi çalıştırmak için üretim VPN ağ geçitlerini kullanın

Azure Danışmanı, temel SKU kullanan tüm VPN ağ geçitlerini denetler ve bunun yerine bir üretim SKU 'SU kullanmanızı önerir. Temel SKU, geliştirme ve test için tasarlanmıştır. Üretim SKU 'Ları teklifi:
- Daha fazla tünel. 
- BGP desteği. 
- Etkin-etkin yapılandırma seçenekleri. 
- Özel IPSec/ıKE ilkesi. 
- Daha yüksek kararlılık ve kullanılabilirlik.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Sanal makine hata toleransı sağlayın (geçici olarak devre dışı)

Uygulamanız için artıklık sağlamak üzere, bir kullanılabilirlik kümesinde iki veya daha fazla sanal makineyi gruplandırmalarını öneririz. Danışman, bir kullanılabilirlik kümesinin parçası olmayan sanal makineleri tanımlar ve bunları bir tane içine taşımayı önerir. Bu yapılandırma, planlı veya planlanmamış bakım sırasında en az bir sanal makinenin kullanılabilir olmasını sağlar ve Azure sanal makine SLA 'sını karşılar. Sanal makine için bir kullanılabilirlik kümesi oluşturmayı veya var olan bir kullanılabilirlik kümesine sanal makineyi eklemeyi seçebilirsiniz.

> [!NOTE]
> Bir kullanılabilirlik kümesi oluşturmayı seçerseniz, buna en az bir sanal makine eklemeniz gerekir. Bir kesinti sırasında en az bir makinenin kullanılabilir olduğundan emin olmak için bir kullanılabilirlik kümesinde iki veya daha fazla sanal makineyi gruplandırmalarını öneririz.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Kullanılabilirlik kümesi hata toleransı sağlayın (geçici olarak devre dışı)

Uygulamanız için artıklık sağlamak üzere, bir kullanılabilirlik kümesinde iki veya daha fazla sanal makineyi gruplandırmalarını öneririz. Danışman tek bir sanal makine içeren kullanılabilirlik kümelerini tanımlar ve kendisine bir veya daha fazla sanal makine eklenmesini önerir. Bu yapılandırma, planlı veya planlanmamış bakım sırasında en az bir sanal makinenin kullanılabilir olmasını sağlar ve Azure sanal makine SLA 'sını karşılar. Bir sanal makine oluşturmayı veya var olan bir sanal makineyi kullanılabilirlik kümesine eklemeyi seçebilirsiniz.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Veri güvenilirliğini artırmak için yönetilen diskleri kullanma (geçici olarak devre dışı)

Depolama hesaplarını veya depolama ölçek birimlerini paylaşan diskler içeren bir kullanılabilirlik kümesindeki sanal makineler, kesintiler sırasında tek depolama ölçek birimlerine yönelik hatalara dayanıklı değildir. Advisor bu kullanılabilirlik kümelerini tanımlar ve Azure yönetilen disklere geçiş yapmanızı önerir. Bu geçiş, kullanılabilirlik kümesindeki sanal makinelerin disklerinin, tek bir hata noktası oluşmasını önlemek için yeterince yalıtılmış olmasını sağlar. 

## <a name="repair-invalid-log-alert-rules"></a>Geçersiz günlük uyarısı kurallarını Onar

Azure Danışmanı, durumu bölümünde geçersiz sorguları belirtilmiş olan günlük uyarısı kurallarını algılar. Azure Izleyici günlük uyarı kuralları belirtilen sıklıkta sorgu çalıştırır ve sonuçlara göre uyarı tetiklersiniz. Başvurulan kaynaklardaki, tablolardaki veya komutlarda değişiklikler nedeniyle sorgular zaman içinde geçersiz hale gelebilir. Danışman, kuralların otomatik olarak devre dışı bırakılmasını ve izleme kapsamının olmasını önlemeye yönelik uyarı sorguları için düzeltmeler önerir. Daha fazla bilgi için bkz. [Uyarı kuralları sorunlarını giderme](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Azure Cosmos DB koleksiyonunuzda tutarlı dizin oluşturma modu yapılandırma

Yavaş dizin oluşturma moduyla Azure Cosmos DB kapsayıcıları yapılandırmak sorgu sonuçlarının yeniliği etkileyebilir. Advisor bu şekilde yapılandırılmış kapsayıcıları algılar ve tutarlı moda geçmeyi önerir. [Azure Cosmos DB 'de dizin oluşturma ilkeleri hakkında daha fazla bilgi edinin.](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Azure Cosmos DB kapsayıcılarınızı bölüm anahtarı ile yapılandırın

Azure Advisor, sağlanan depolama kotasına yaklaşmayan Azure Cosmos DB bölümlenmemiş koleksiyonları tanımlar. Bu koleksiyonları, hizmet tarafından otomatik olarak ölçeklendirilebilmeleri için bölüm anahtarı tanımıyla yeni koleksiyonlara geçirmeniz önerilir. [Bölüm anahtarı seçme hakkında daha fazla bilgi edinin.](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Azure Cosmos DB .NET SDK 'nizi NuGet 'den en son sürüme yükseltme

Azure Advisor, .NET SDK 'sının eski sürümlerini kullanan Azure Cosmos DB hesapları tanımlar. En son düzeltmeler, performans iyileştirmeleri ve özellik özellikleri için NuGet 'den en son sürüme yükseltmenizi önerir. [.NET SDK Azure Cosmos DB hakkında daha fazla bilgi edinin.](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Azure Cosmos DB Java SDK'nızı Maven'dan en son sürüme yükseltin

Azure Advisor, Java SDK 'sının eski sürümlerini kullanan Azure Cosmos DB hesapları tanımlar. En son düzeltmeler, performans iyileştirmeleri ve özellik özellikleri için Maven 'ten en son sürüme yükseltmenizi önerir. [Java SDK Azure Cosmos DB hakkında daha fazla bilgi edinin.](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Azure Cosmos DB Spark bağlayıcısını Maven 'nin en son sürümüne yükseltin

Azure Advisor, Azure Cosmos DB Spark bağlayıcısının eski sürümlerini kullanan Azure Cosmos DB hesapları tanımlar. En son düzeltmeler, performans iyileştirmeleri ve özellik özellikleri için Maven 'ten en son sürüme yükseltmenizi önerir. [Azure Cosmos DB Spark Bağlayıcısı hakkında daha fazla bilgi edinin.](../cosmos-db/spark-connector.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>HDInsight 4,0 üzerinde Kafka 2,1 ' ye geçmeyi düşünün

1 Temmuz 2020 ' den itibaren, Azure HDInsight 4,0 üzerinde Kafka 1,1 ' yi kullanarak yeni Kafka kümeleri oluşturamayacak. Mevcut kümeler Microsoft desteği olmadan, olduğu gibi çalıştırılacak. Olası sistem/destek kesintilerini önlemek için HDInsight 4,0 ' de 30 Haziran 2020 ' ye Kafka 2,1 ' ye geçmeyi düşünün.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>HDInsight Spark kümelerinde eski Spark sürümlerini yükseltmeyi düşünün

1 Temmuz 2020 ' den itibaren, HDInsight 3,6 ' te Spark 2,1 veya 2,2 ' i kullanarak yeni Spark kümeleri oluşturamayacak. HDInsight 4,0 üzerinde Spark 2,3 kullanarak yeni Spark kümeleri oluşturamayacak. Mevcut kümeler Microsoft desteği olmadan, olduğu gibi çalıştırılacak. 

## <a name="enable-virtual-machine-replication"></a>Sanal makine çoğaltmasını etkinleştirme
Başka bir bölgeye çoğaltma özelliği etkinleştirilmemiş sanal makineler, bölgesel kesintiler için esnek değildir. Sanal makinelerin çoğaltılması, Azure bölgesi kesintileri sırasında olumsuz iş etkisini azaltır. Advisor, çoğaltmanın etkin olmadığı ve etkinleştirilmesini önerdiği VM 'Leri algılar. Çoğaltmayı etkinleştirdiğinizde, bir kesinti oluşursa, sanal makinelerinizi uzak bir Azure bölgesinde hızlıca alabilirsiniz. [Sanal makine çoğaltma hakkında daha fazla bilgi edinin.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Azure Connected Machine aracısının en son sürümüne yükseltme
[Azure bağlı makine Aracısı](../azure-arc/servers/manage-agent.md) , hata düzeltmeleri, kararlılık iyileştirmeleri ve yeni işlevlerle düzenli olarak güncelleştirilir. Makine aracısının en son sürümünde çalışmayan kaynakları belirledik ve bu danışman önerisi, en iyi Azure Arc deneyimi için aracınızı en son sürüme yükseltmenizi öneriyor.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>Web sitesi bütünlüğünü sağlamak için konak adını geçersiz kılmayın
Advisor, Application Gateway yapılandırılırken ana bilgisayar adının geçersiz kılınmasından kaçınmanızı öneririz. Application Gateway'in ön ucundaki etki alanının arka uca erişim için kullanılan etki alanından farklı olması, tanımlama bilgilerinin veya yeniden yönlendirme URL'lerinin bozulmasına yol açabilir. Bunun her durumda söz konusu olmayabileceğini ve genelde bazı arka uç kategorilerinin (REST API'ler gibi) buna daha az duyarlı olduğunu aklınızda bulundurun. Arka ucun bu durumla başa çıkabileceğinden emin olun veya Application Gateway yapılandırmasını güncelleştirerek konak adının arka ucun üzerine yazılması gereğini ortadan kaldırın. App Service ile kullanıldığında, Web uygulamasına özel bir etki alanı adı ekleyin ve *arka uca doğru. azurewebsites.NET ana bilgisayar adını* kullanmaktan kaçının. [Özel etki alanı hakkında daha fazla bilgi edinin](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md).

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Danışman 'de yüksek kullanılabilirlik önerilerine erişme

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

2.  Danışman panosunda, **yüksek kullanılabilirlik** sekmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi için bkz.
* [Advisor 'a giriş](advisor-overview.md)
* [Danışman'ı kullanmaya başlama](advisor-get-started.md)
* [Danışman puanı](azure-advisor-score.md)
* [Danışman maliyet önerileri](advisor-cost-recommendations.md)
* [Advisor performans önerileri](advisor-performance-recommendations.md)
* [Danışman güvenlik önerileri](advisor-security-recommendations.md)
* [Danışman işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)