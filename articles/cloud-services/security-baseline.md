---
title: Azure Cloud Services için Azure Güvenlik temeli
description: Azure Cloud Services güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c389cfd2af6fe83ec232e5f205041676ba46bd2a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558202"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azure Cloud Services için Azure Güvenlik temeli

Bu güvenlik temeli, Microsoft Azure Cloud Services için [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Cloud Services için geçerli olan ilgili kılavuza göre gruplandırılır. Cloud Services için geçerli olmayan **denetimler** dışlandı.

 
Cloud Services Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Cloud Services güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: güvenilen bağlantı NOKTALARıNA ve IP aralıklarına dayanarak yalıtımına zorlamak için ayrı genel ve özel alt ağlarla klasik bir Azure sanal ağı oluşturun. Bu sanal ağ ve alt ağlar, geçerli Azure Resource Manager kaynakları değil, klasik sanal ağ (klasik dağıtım) tabanlı kaynaklar olmalıdır.  

Trafik yönüne, protokole, kaynak adrese ve bağlantı noktasına ve hedef adrese ve bağlantı noktasına dayalı erişim denetimi kuralları içeren bir ağ güvenlik grubu kullanarak trafiğe izin ver veya Reddet. Bir ağ güvenlik grubunun kuralları herhangi bir zamanda değiştirilebilir ve değişiklikler tüm ilişkili örneklere uygulanır.

Microsoft Azure Cloud Services (klasik) Azure Resource Manager sanal ağlara yerleştirilemez. Ancak, Kaynak Yöneticisi tabanlı sanal ağlar ve klasik dağıtım tabanlı sanal ağlar eşleme aracılığıyla bağlanabilir. 

- [Ağ güvenlik grubuna genel bakış](../virtual-network/network-security-groups-overview.md)

- [Sanal Ağ eşlemesi](./cloud-services-connectivity-and-networking-faq.md#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Kılavuz**: Azure Cloud Services yapılandırmanızı belgeleyin ve değişiklikler için izleyin. Hizmetin yapılandırma dosyasını, hizmette her bir rol için dağıtılacak rol örneği sayısını, herhangi bir yapılandırma ayarlarının değerlerini ve bir rolle ilişkili tüm sertifikalar için parmak izlerini belirtmek üzere kullanın. 

Hizmet bir sanal ağın parçasıysa, hizmet yapılandırma dosyasında ve sanal ağ yapılandırma dosyasında ağ için yapılandırma bilgilerinin sağlanması gerekir. Hizmet yapılandırma dosyası için varsayılan uzantı. cscfg ' dir. Azure Ilkesinin, yapılandırma zorlaması için klasik dağıtımlar için desteklenmediğini unutmayın.

Hizmet yapılandırma dosyası (. cscfg) ve bir hizmet tanımı (. csdef) dosyasındaki tanımı bir bulut hizmetinin yapılandırma değerlerini ayarlayın. Bir uygulamanın hizmet modelini tanımlamak için hizmet tanımı dosyasını kullanın. Bulut hizmeti için kullanılabilen rolleri tanımlayın ve ayrıca hizmet uç noktalarını belirtin. Azure Cloud Services için yapılandırmayı hizmet yapılandırma dosyası ile günlüğe kaydedin. Yeniden yapılandırma, ServiceConfig. cscfg dosyası aracılığıyla yapılabilir. 

Belirtilen iç uç noktalarla hangi rollerin iletişim kurabildiğini kısıtlayan isteğe bağlı NetworkTrafficRules öğe hizmeti tanımını izleyin. Rollerin birbirleriyle nasıl iletişim kurması gerektiğini belirtmek için, hizmet tanımı dosyasındaki isteğe bağlı bir öğe olan NetworkTrafficRules düğümünü yapılandırın. Belirli rolün iç uç noktalarına hangi rollerin erişebileceği sınırlar koyun.  Hizmet tanımının değiştirilemeyeceği unutulmamalıdır. 

Denetim için ağ güvenlik grubu akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Akış günlüklerini bir Log Analytics çalışma alanına gönderin ve Azure kiracınızda trafik desenlerine yönelik Öngörüler sağlamak için Trafik Analizi kullanın. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme, etkin noktaları ve güvenlik tehditlerini anlama, trafik akışı desenlerini anlama ve ağ yapılandırmalarını belirlemeyi bilme yeteneğidir.

- [Azure Resource Manager ile klasik dağıtım-dağıtım modellerini ve kaynaklarınızın durumunu anlayın](../azure-resource-manager/management/deployment-models.md)

- [Cloud Services config dosyası](schema-cscfg-file.md)

- [Azure Ilkesi tarafından desteklenen hizmetlerin listesi](/cli/azure/azure-services-the-azure-cli-can-manage)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: Microsoft, Azure Cloud Services ve müşterileri arasında seyahat edildiğinde verileri korumak Için aktarım katmanı GÜVENLIĞI (TLS) Protokolü v 1.2 'yi kullanır. Microsoft veri merkezleri, Azure hizmetlerine bağlanan istemci sistemleriyle bir TLS bağlantısı anlaşmasına sahiptir. TLS, güçlü kimlik doğrulaması, ileti gizliliği ve bütünlük (ileti izinsiz değişikliği, yakalama ve forgery), birlikte çalışabilirlik, algoritma esnekliği ve dağıtım ve kullanım kolaylığı sağlar.

- [Şifreleme temelleri](../security/fundamentals/encryption-overview.md)

- [TLS/SSL sertifikalarını yapılandırma](cloud-services-configure-ssl-certificate-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Kılavuz**: Azure bulutu, platform hizmetlerini dağıtılmış hizmet reddi (DDoS) saldırılarına karşı korumak için çok katmanlı bir ağ güvenliği uygular. Azure DDoS koruması, Azure bulutunun sürekli izleme sürecinin bir parçasıdır ve bu da sızma testi aracılığıyla sürekli olarak geliştirilmiştir. Bu DDoS koruması, yalnızca dış ve diğer Azure kiracılarından gelen saldırıları değil, yalnızca dışarıdan gerçekleştirilen saldırıları değil. 

Azure Cloud Services içindeki platform düzeyi korumasının yanı sıra iletişimi engellemeye veya reddetmeye yönelik birkaç farklı yol vardır. Bunlar: 

-  Belirli IP adreslerini seçmeli olarak engellemek için bir başlangıç görevi oluşturma
-  IIS web.config dosyanızı değiştirerek belirli bir IP adresi kümesine yönelik bir Azure Web rolü erişimini kısıtlayın

Varsayılan URL veya Cloud Services adına gelen trafiği engelleyin, örneğin *. cloudapp.net. Cloud Services tanım (. csdef) dosyasındaki site bağlama yapılandırması altında, ana bilgisayar üst bilgisini özel bir DNS adına ayarlayın*.

Klasik abonelik Yöneticisi atamalarına Izin verme kuralını yapılandırın. Varsayılan olarak, bir iç uç nokta tanımlandıktan sonra, iletişim herhangi bir rolden herhangi bir kısıtlama olmadan bir rolün iç uç noktasına akabilir. İletişimi kısıtlamak için, hizmet tanımı dosyasındaki ServiceDefinition öğesine bir NetworkTrafficRules öğesi eklemeniz gerekir.

- [Bulut hizmetimin varsayılan URL 'sine gelen trafiği engelleme/devre dışı bırakabilir](./cloud-services-connectivity-and-networking-faq.md#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDOS koruması](./cloud-services-connectivity-and-networking-faq.md#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Belirli bir IP adresini engelle](./cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: Azure ağlarını izlemeye Izin veren Azure Ağ İzleyicisi, ağ performansı izleme, tanılama ve analiz hizmetini kullanın. Ağ Izleyicisi Aracısı sanal makine uzantısı, istek üzerine ağ trafiği yakalama ve Azure sanal makinelerinde diğer gelişmiş işlevler için bir gereksinimdir. Ağ Izleyicisi Aracısı sanal makine uzantısını yükleyip ağ güvenlik grubu akış günlüklerini açın.

Bir ağ güvenlik grubunda akış günlüğünü yapılandırın. Ağ Izleyicisi sanal makine uzantısının klasik dağıtım modeliyle dağıtılan var olan bir sanal makineye nasıl dağıtılacağı hakkındaki ayrıntıları gözden geçirin.

- [Ağ güvenlik grubunda akış günlüğünü yapılandırma](../virtual-machines/extensions/network-watcher-linux.md)

- [Akış günlüklerini yapılandırma hakkında daha fazla bilgi için ziyaret edin](/cli/azure/azure-services-the-azure-cli-can-manage)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: Azure Cloud SERVICES yerleşik kimlikleri veya IP 'leri yeteneğine sahip değil. Müşteriler kurumsal gereksinimlerine bağlı olarak Azure Marketi 'nden bir veya daha fazla ağ tabanlı KIMLIK veya IP çözümü seçebilir ve dağıtabilir. Üçüncü taraf çözümleri kullanırken, doğru işlem ve işlevselliği sağlamak için Azure Cloud Services ile seçtiğiniz KIMLIKLERI veya IP 'leri çözümünüzü iyice test ettiğinizden emin olun.

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Kılavuz**: Azure Cloud Services 'a eklenen hizmet sertifikaları, hizmetten ve hizmet üzerinden güvenli bir şekilde iletişime olanak sağlar. Bu sertifikalar, hizmetlerin tanımında tanımlanmıştır ve bir Web rolünün örneğini çalıştıran sanal makineye otomatik olarak dağıtılır. Örnek olarak, bir Web rolü için, sunulan bir HTTPS uç noktasının kimliğini doğrulayabilecek bir hizmet sertifikası kullanabilirsiniz. 

Sertifikayı güncelleştirmek için, yalnızca yeni bir sertifika yüklemek ve hizmet yapılandırma dosyasındaki parmak izi değerini değiştirmek gereklidir.

Gizlilik ve bütünlük koruması sağlamak için en yaygın olarak kullanılan bir yöntem olan TLS 1,2 protokolünü kullanın. 

Genellikle, Web uygulamalarını korumak ve bunları OWASP En Iyi 10 gibi saldırılara karşı korumak için, Web uygulamalarını korumak üzere Azure Web uygulaması güvenlik duvarı özellikli bir Azure Application Gateway dağıtabilirsiniz. 

- [Hizmet sertifikaları](cloud-services-certs-create.md)

- [Azure 'da bir uygulama için TLS Yapılandırma](cloud-services-configure-ssl-certificate-portal.md)

- [Application Gateway dağıtma](../application-gateway/quick-create-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure Cloud Services yapılandırmanızı sağlamlaştırın ve değişiklikler için izleyin. Hizmet yapılandırma dosyası, hizmette her bir rol için dağıtılacak rol örneği sayısını, herhangi bir yapılandırma ayarlarının değerlerini ve bir rolle ilişkili tüm sertifikalar için parmak izlerini belirtir. 

Hizmetiniz bir sanal ağın parçasıysa, ağ için yapılandırma bilgilerinin yanı sıra, sanal ağ yapılandırma dosyasında da hizmet yapılandırma dosyasında sağlanması gerekir. Hizmet yapılandırma dosyası için varsayılan uzantı. cscfg ' dir.

Azure Ilkesinin yapılandırma zorlaması için Azure Cloud Services ile desteklenmediğini unutmayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Kılavuz**: Azure ağ güvenlik grupları, bir Azure sanal ağındaki Azure kaynaklarından gelen ve giden ağ trafiğini filtrelemek için kullanılabilir. Bir ağ güvenlik grubu, gelen ağ trafiğine veya giden ağ trafiğine izin veren veya reddeden güvenlik kurallarını, çeşitli Azure kaynakları türlerini içerir. Her kural için kaynak, hedef, bağlantı noktası ve protokol belirtebilirsiniz.

Azure Cloud Services içindeki her bir ağ güvenlik grubu kuralı için "Açıklama" alanını kullanarak, gelen trafiğe veya bir ağdan trafiğe izin veren kuralları belgeleyin.

- [Ağ güvenlik grubu kuralları ile ağ trafiğini filtreleme](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure Traffic Manager yerleşik uç nokta izleme ve otomatik uç nokta yük devretme özelliklerini kullanın. Uç nokta ve Azure bölge hatalarıyla dayanıklı olan yüksek kullanılabilirliğe sahip uygulamalar sunmanıza yardımcı olurlar. Uç nokta izlemeyi yapılandırmak için Traffic Manager profilinizde belirli ayarları belirtmeniz gerekir.

Azure 'da bir platform günlüğü olan etkinlik günlüğünden, abonelik düzeyindeki olaylara ilişkin Öngörüler toplayın. Bu, bir kaynağın değiştirildiği veya bir sanal makinenin başlatıldığı zaman gibi bilgileri içerir. Azure portal etkinlik günlüğünü görüntüleyin veya PowerShell ve CLı ile girdileri alın. 

Etkinlik günlüğünü Azure Izleyici 'ye, Azure Event Hubs Azure 'un dışına veya arşivleme için Azure depolama 'ya göndermek üzere bir tanılama ayarı oluşturun. Azure Cloud Services 'de kritik kaynaklar değiştirildiğinde bildirim uyarıları için Azure Izleyicisini yapılandırın. 

- [Azure etkinlik günlüğü](../azure-monitor/essentials/activity-log.md)

- [Azure Izleyici 'yi kullanarak etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-activity-log.md)

- [Traffic Manager Izleme](../traffic-manager/traffic-manager-monitoring.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: onaylanan zaman eşitleme kaynaklarını kullanın

**Rehberlik**: Microsoft, Azure Cloud Services Azure kaynakları için zaman kaynaklarını korur. Müşterilerin, ortamınızda kullanılan bir zaman sunucusuna erişim izni vermek için, UDP protokolüyle 123 numaralı bağlantı noktası üzerinden bir ağ kuralı oluşturması gerekebilir.

- [NTP sunucusu erişimi](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: Azure Event Hubs aracılığıyla bulut hizmeti akış verilerinizi programlama yoluyla kullanın. Günlüklerinizi izlemek ve gözden geçirmek için bu verileri tümleştirin ve Azure Sentinel 'e gönderin veya bir üçüncü taraf SıEM kullanın. Merkezi güvenlik günlük yönetimi için, seçtiğiniz Azure Güvenlik Merkezi verilerinizi Azure Event Hubs 'da sürekli dışarı aktarmayı yapılandırın ve SıEM 'niz için uygun bağlayıcıyı ayarlayın. Üçüncü taraf araçları dahil olmak üzere Azure Sentinel için bazı seçenekler şunlardır:

- Azure Sentinel-yerel güvenlik merkezi uyarıları veri bağlayıcısını kullanın
- Splunk-splunk için Azure Izleyici eklentisini kullanma
- IBM QRadar-el ile yapılandırılmış bir günlük kaynağı kullanın
- Arcgörüş – SmartConnector kullanın

Azure Sentinel ile kullanılabilir bağlayıcılar hakkında daha fazla bilgi için Azure Sentinel belgelerini gözden geçirin. 

- [Veri kaynaklarını bağlama](../sentinel/connect-data-sources.md)

- [SıEM ile tümleştirme](../security-center/continuous-export.md)

- [Tanılama verilerini depola](diagnostics-extension-to-storage.md)

- [SıEM tümleştirmesini Azure Event Hubs aracılığıyla yapılandırma](../security-center/continuous-export.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: Azure Cloud Services çalıştıran sanal makine örnekleri de dahil olmak üzere, sanal makinelerde sistem ve günlük verilerini yakalayan Azure Cloud Services sorunlarını gidermeye yönelik Azure tanılama ayarlamak Için Visual Studio 'yu yapılandırın. Tanılama verileri, seçtiğiniz bir depolama hesabına aktarılır. Dağıtımdan önce Azure Cloud Services projelerinde tanılamayı açın.

 
Azure Izleyici içindeki etkinlik günlüğündeki bazı olayların değişiklik geçmişini görüntüleyin. Bir olay zaman diliminde gerçekleşen değişiklikleri denetleyin. Değişiklik geçmişi (Önizleme) sekmesiyle daha derin bir inceleme için etkinlik günlüğünden bir olay seçin. Visual Studio 'dan bir Azure Cloud Services yayımladığınızda Application Insights tanılama verilerini gönderin. Bu anda Application Insights Azure kaynağını oluşturun veya mevcut bir Azure kaynağına verileri gönderin. 

Azure Cloud Services, kullanılabilirlik, performans, başarısızlık ve kullanım için Application Insights tarafından izlenebilir. En önemli verileri görebilmeniz için Application Insights özel grafikler eklenebilir. Rol örneği verileri, Azure Cloud Services projenizde Application Insights SDK kullanılarak toplanabilir. 

- [Dağıtımdan önce Visual Studio 'da tanılamayı aç](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Değişiklik geçmişini görüntüle](../azure-monitor/essentials/activity-log.md#view-change-history)

- [Azure bulut hizmeti için Application Insights (klasik)](../azure-monitor/app/cloudservices.md)

- [Azure bulut hizmeti (klasik) ve sanal makineler için tanılamayı ayarlama](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure Cloud Services gelişmiş izlemeyi kullanarak, ek ölçümlerin, 5 dakika, 1 saat ve 12 saat aralıklarında örneklenmesi ve toplanmasına olanak tanır. Toplanan veriler depolama hesabında, tablolarda depolanır ve 10 gün sonra temizlenir. Ancak, kullanılan depolama hesabı role göre yapılandırılır ve farklı roller için farklı depolama hesapları kullanabilirsiniz. Bu,. csdef ve. cscfg dosyalarında bir bağlantı dizesiyle yapılandırılır.

Gelişmiş izlemenin, izlemek istediğiniz rolde Azure Tanılama uzantısının (Application Insights SDK isteğe bağlı) kullanılmasını gerektirdiğini unutmayın. Tanılama uzantısı, izlenen tanılama ölçümlerini yapılandırmak için Diagnostics. wadcfgx adlı bir yapılandırma dosyası (rol başına) kullanır. Azure tanılama uzantısı, verileri bir Azure depolama hesabında toplar ve depolar. Bu ayarlar. wadcfgx,. csdef ve. cscfg dosyalarında yapılandırılır.

- [Bulut hizmeti Izlemeye giriş](cloud-services-how-to-monitor.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: temel veya gelişmiş Izleme modları Azure Cloud Services için kullanılabilir. Azure Cloud Services, bir konak sanal makinesinden temel izleme verilerini (CPU yüzdesi, ağ/çıkış ve disk okuma/yazma) otomatik olarak toplar. Azure portal bir bulut hizmetinin genel bakış ve ölçümler sayfalarında toplanan izleme verilerini görüntüleyin. 

Azure Tanılama uzantısını kullanırken uygulama günlükleri, performans sayaçları ve daha fazlası gibi tanılama verilerini toplamak için Azure Cloud Services tanılamaları etkinleştirin. Set-AzureServiceDiagnosticsExtension cmdlet 'i ile zaten çalışan bir bulut hizmetinde tanılama yapılandırmasını etkinleştirin veya güncelleştirin ya da otomatik olarak tanılama uzantısı olan bir bulut hizmeti dağıtın. İsteğe bağlı olarak, Application Insights SDK 'sını yüklersiniz. Performans sayaçlarını Azure Izleyici 'ye gönderin.

Azure tanılama uzantısı, verileri bir Azure depolama hesabında toplar ve depolar. Tanılama verilerini kalıcı olarak depolanmadığından Microsoft Azure Depolama Öykünücüsü veya Azure depolama 'ya aktarın. Depolama alanındaki bir kez, Visual Studio, Microsoft Azure Depolama Gezgini, Azure Management Studio Sunucu Gezgini gibi kullanılabilir araçlardan biri ile görüntülenebilir. Tanılama, tanılama uzantısında Diagnostics. wadcfgx adlı bir yapılandırma dosyası (rol başına) ile izlenecek tanılama ölçümlerini yapılandırın. 

- [Bulut hizmeti Izlemeye giriş](cloud-services-how-to-monitor.md)

- [Çalışan rolünde tanılamayı etkinleştirme-SıEM ile tümleştirme](../security-center/continuous-export.md)

- [PowerShell kullanarak Azure Cloud Services tanılamayı etkinleştirme](cloud-services-diagnostics-powershell.md)

- [Azure Depolama’daki tanılama verilerini depolama ve görüntüleme](./diagnostics-extension-to-storage.md?preserve-view=)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure Cloud Services günlük verilerini Azure Sentinel ile tümleştirerek veya üçüncü taraf SIEM ile, anormal etkinlikler için uyarı vermeyi etkinleştirerek izleyebilirsiniz.

- [SıEM ile tümleştirme](../security-center/continuous-export.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Kılavuz**: Azure Için Microsoft kötü amaçlı yazılımdan koruma, Azure Cloud Services ve sanal makinelerini korur. Web uygulaması ateş duvarları, ağ güvenlik duvarları, kötü amaçlı yazılım, yetkisiz giriş algılama ve önleme sistemleri (KIMLIKLER veya IP 'ler) ve daha fazlası gibi üçüncü taraf güvenlik çözümlerini de dağıtma seçeneğiniz vardır.

- [Azure temel IP/KIMLIKLERININ ve DDOS 'nin sağladığı özellikler ve yetenekler nelerdir?](./cloud-services-configuration-and-management-faq.md#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Microsoft, Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak Azure kaynaklarına erişimi yönetmenizi önerir. Ancak Azure Cloud Services, Azure RBAC modelini desteklemez, çünkü bu bir Azure Resource Manager tabanlı hizmet değildir ve klasik bir abonelik kullanmanız gerekir

Varsayılan olarak, hesap yöneticisi, hizmet yöneticisi ve Co-Administrator Azure 'daki üç klasik abonelik Yöneticisi rolünün bir hesaptır. 

Klasik abonelik yöneticileri, Azure aboneliğinde tam erişime sahiptir. Bu yöneticiler Azure portal, Azure Resource Manager API'leri ve klasik dağıtım modeli API'leri aracılığıyla kaynakları yönetebilir. Azure'a kaydolmak için kullanılan hesap otomatik olarak hem Hesap Yöneticisi hem de Hizmet Yöneticisi olarak ayarlanır. Ek Co-Administrators daha sonra eklenebilir. 

Hizmet Yöneticisi ve Co-Administrators, abonelik kapsamında sahip rolü (bir Azure rolü) atanan kullanıcılara eşit erişime sahiptir. Co-Administrators yönetin veya Azure portal içindeki klasik Yöneticiler sekmesini kullanarak hizmet yöneticisini görüntüleyin. 

Klasik hizmet yöneticisi ve PowerShell ile birlikte yönetici için rol atamalarını şu komutla listeleyin:

Get-AzRoleAssignment-ıncludeclassicadministrators

Klasik abonelik yönetim rolleri arasındaki farkları gözden geçirin. 

- [Klasik üç abonelik yönetim rolü arasındaki farklar](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: kullanılabilir rollere ve Azure Cloud Services kaynaklarını çalıştırmak ve yönetmek için gereken izinlere bağlı olarak, adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturmanız önerilir.

- [Klasik abonelik yönetim rolleri arasındaki farklar](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Kılavuz**: Azure Cloud Services üzerinde çalışan uygulamalar için ayrı kimlikler yönetmekten kaçının. Kullanıcıların birden çok kimliği ve kimlik bilgilerini yönetmesini gerektirmemek için çoklu oturum açma uygulayın.

- [Çoklu oturum açma (SSO) nedir?](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: yükseltilmiş ayrıcalıklar gerektiren, yönetim görevleri için güvenli, Azure tarafından yönetilen bir iş Istasyonu (ayrıcalıklı erişim iş istasyonu olarak da bilinir) kullanmanız önerilir.

- [Güvenli, Azure tarafından yönetilen iş istasyonlarını anlayın](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Kılavuz**: Azure Cloud Service REST API 'lerini kullanarak, hassas bilgiler Için Azure bulut hizmeti kaynaklarınızı envanterini çıkarın. Yapılandırma ve. pkg kaynaklarını almak için dağıtılan bulut hizmeti kaynaklarını yoklayın.

 Örnek olarak, birkaç API aşağıda listelenmiştir:

- Dağıtımı al-dağıtımı al işlemi, bir dağıtım için yapılandırma bilgilerini, durumu ve sistem özelliklerini döndürür.
- Paketi al-paket al işlemi bir dağıtım için bulut hizmeti paketi alır ve paket dosyalarını Microsoft Azure Blob depolama alanına depolar
- Bulut hizmeti özelliklerini al-bulut hizmeti al özellikleri işlemi belirtilen bulut hizmeti için özellikleri alır

Azure Cloud Service REST API 'Leri belgelerini gözden geçirin ve kuruluşunuzun gereksinimlerine bağlı olarak hassas bilgilerin veri korumasına yönelik bir işlem oluşturun.

- [Dağıtım Alma](/rest/api/compute/cloudservices/rest-get-deployment)

- [Bulut hizmeti özelliklerini al](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Paketi al](/rest/api/compute/cloudservices/rest-get-package)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Kılavuz**: Azure Cloud Services için ortam türü ve veri duyarlılığı düzeyi gibi bireysel güvenlik etki alanları için ayrı abonelikler ve yönetim grupları kullanarak yalıtım uygulayın.

Rol işlemlerine verilen erişim izinlerini belirtmek için Azure Cloud Service 'in sertifika öğesinde "permissionLevel" öğesini de düzenleyebilirsiniz. Yalnızca yükseltilmiş işlemlerin özel anahtara erişip erişebilmesini istiyorsanız, yükseltilmiş izin ' ı belirtin. Limitedorepervated izni, tüm rol işlemlerinin özel anahtara erişmesine izin verir. Olası değerler, Limitedoretated veya yükseltilmiş. Varsayılan değer Limitedorelet değeridir.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [WebRole Şeması](./schema-csdef-webrole.md#Certificate)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: bir üçüncü taraf çözümünü kullanarak, hassas bilgilerin yetkisiz aktarımını izlemek ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engellemek için ağ duvarlar ' da bir üçüncü taraf çözümü kullanmanız önerilir.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Kılavuz**: Azure Cloud SERVICES için TLS v2 'yi yapılandırın. Sertifikayı hazırlanan Azure Cloud Services dağıtımınıza eklemek ve hizmetlerin CSDEF ve CSCFG dosyalarına sertifika bilgilerini eklemek için Azure portal kullanın. Uygulamanızı yeniden paketleyin ve hazırlanan dağıtımınızı yeni paketi kullanacak şekilde güncelleştirin. 

Azure 'da ve hizmetten güvenli iletişim sağlamak için Azure Cloud Services bağlı olan hizmet sertifikalarını kullanın. Sunulan bir HTTPS uç noktasının kimliğini doğrulayabilecek bir sertifika sağlayın. Bulut hizmetinin hizmet tanımında hizmet sertifikalarını tanımlayın ve bu hizmetleri, rolünüzün bir örneğini çalıştırarak sanal makineye otomatik olarak dağıtın.

Yönetim sertifikalarla yönetim API 'siyle kimlik doğrulama) yönetim sertifikaları, klasik dağıtım modeliyle kimlik doğrulaması yapmanıza olanak sağlar. Birçok program ve araç (Visual Studio veya Azure SDK gibi), çeşitli Azure hizmetlerinin yapılandırılmasını ve dağıtımını otomatik hale getirmek için bu sertifikaları kullanır. 

Ek başvuru için klasik dağıtım modeli API 'SI, Azure portal aracılığıyla kullanılabilen klasik dağıtım modeli işlevine programlı erişim sağlar. Python için Azure SDK, Azure Cloud Services ve Azure depolama hesaplarını yönetmek için kullanılabilir. Python için Azure SDK, bir REST API klasik dağıtım modeli API 'sini sarmalanmış. Tüm API işlemleri TLS üzerinden gerçekleştirilir ve X. 509.440 v3 sertifikaları kullanılarak karşılıklı kimlik doğrulaması yapılır. Yönetim hizmetine Azure 'da çalışan bir hizmetin içinden erişilebilir. Ayrıca, HTTPS isteği gönderebilen ve HTTPS yanıtı alabilen herhangi bir uygulamadan Internet üzerinden doğrudan erişilebilir.

- [Azure 'da bir uygulama için TLS Yapılandırma](cloud-services-configure-ssl-certificate-portal.md)

- [Python 'da klasik dağıtım modelini kullanma](cloud-services-python-how-to-use-service-management.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: bir üçüncü taraf etkin bulma aracı kullanarak, şirket içi veya uzak bir hizmet sağlayıcıdakiler dahil olmak üzere kuruluşun teknoloji sistemleri tarafından depolanan, işlenen veya aktarılan tüm hassas bilgileri tanımlamak ve ardından kuruluşun hassas bilgi envanterini güncelleştirmek için önerilir.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: bulut hizmeti (klasik) için geçerli değildir. Veri kaybı engellemesini zorlamaz.

Veriler bir sistemden kopyalandıklarında bile verilere erişim denetimlerine zorlamak üzere otomatik ana bilgisayar tabanlı veri kaybı önleme çözümü gibi bir üçüncü taraf araç uygulamanız önerilir.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Kılavuz**: Azure Cloud Services Rest 'ten şifrelemeyi desteklemez. Bunun nedeni Azure Cloud Services durum bilgisiz olacak şekilde tasarlanmalıdır. Azure Cloud Services, örneğin varsayılan olarak, bekleyen şekilde şifrelenen Azure Storage gibi harici depolamayı destekler.  

Geçici disklerde depolanan uygulama verileri şifrelenmemiştir. Müşteri, gerektiğinde bu verilerin yönetilmesi ve şifrelenmesi sorumludur.  

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure izleyici 'de klasik ölçüm uyarılarını kullanarak ölçülerinizin biri bir eşiğin üzerinde kritik kaynaklara uygulandığında bildirim alabilirsiniz. Klasik ölçüm uyarıları yalnızca boyutlu olmayan ölçümlerde uyarı vermeyi sağlayan eski bir işlevdir. Klasik ölçüm uyarıları üzerinde geliştirilmiş işlevlere sahip olan ölçüm uyarıları adlı yeni bir işlev mevcuttur. 

Ayrıca, Application Insights kullanılabilirlik, performans, başarısızlık ve kullanım için Azure Cloud Services uygulamalarını izleyebilir. Bu, Azure Cloud Services Azure Tanılama verileri ile Application Insights SDK 'lardan Birleşik verileri kullanır.

- [Azure Izleyici kullanarak klasik ölçüm uyarılarını oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-classic-portal.md)

- [Ölçüm uyarılarına genel bakış](../azure-monitor/alerts/alerts-metric-overview.md) 

- [Azure bulut hizmeti için Application Insights (klasik)](../azure-monitor/app/cloudservices.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: Bu bilgilerin Azure Cloud Services worker ve hizmet olarak platform (PaaS) ile Web rollerinin Azure Konuk işletim sistemiyle ilişkili olduğunu unutmayın. Ancak hizmet olarak altyapı (IaaS) ile sanal makinelere uygulanmaz.

Azure, varsayılan olarak müşterinin Konuk işletim sistemini, Windows Server 2016 gibi hizmet yapılandırmasında (. cscfg) belirtilen en son desteklenen görüntüye düzenli olarak güncelleştirir.

Bir müşteri Azure Cloud Services dağıtımı için belirli bir işletim sistemi sürümünü seçtiğinde, otomatik işletim sistemi güncelleştirmelerini devre dışı bırakır ve sorumluluğunda düzeltme eki oluşturur. Müşterinin, rol örneklerinin güncelleştirmeleri aldığından emin olması veya uygulamaları güvenlik açıklarına karşı savunmasız bırakabilmeleri gerekir.

- [Azure Konuk işletim sistemi](cloud-services-guestos-msrc-releases.md)

- [Azure Konuk işletim sistemi desteklenebilirlik ve emeklilik ilkesi](cloud-services-guestos-retirement-policy.md)

- [Bulut hizmetini yapılandırma (klasik)](cloud-services-how-to-configure-portal.md)

- [Konuk işletim sistemi sürümünü yönetme](./cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümü dağıtma

**Rehberlik**: üçüncü taraf bir düzeltme eki yönetimi çözümü kullanın. Ortamlarında zaten Configuration Manager kullanan müşteriler, Windows Server Update Service 'te özel güncelleştirmeler yayımlamasına izin veren System Center Updates Publisher da kullanabilir. 

Bu, Güncelleştirme Yönetimi, üçüncü taraf yazılımlarla güncelleştirme deposu olarak Configuration Manager kullanan makinelere yaması sağlar.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: bir müşterinin bir DDoS saldırısından gelen risk kapsamını sürekli olarak anlaması önerilir. 

Bu senaryolar arasında düşünce sunuyoruz:

- Hangi yeni genel kullanıma açık Azure kaynakları koruması gerekir?
- Hizmette tek hata noktası var mı?
- Hizmetler geçerli müşteriler için kullanılabilir olmaya devam ederken, bir saldırının etkilerini sınırlamak için hizmetler nasıl yalıtılarak?
- DDoS koruma standardının etkinleştirilmesi gereken ancak olmaması gereken sanal ağlar var mı?
- Hizmetlerim, birden çok bölgede yük devretmeyle etkin/etkin mi?

Destekleyici belgeler:

- [Azure kaynaklarınızın risk değerlendirmesi](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Kılavuz**: Azure Cloud Services için geçerli değildir. Bu öneri IaaS işlem kaynakları için geçerlidir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: envanterin düzenli olarak mutabık kılınmanız ve yetkisiz kaynakların aboneliğden zamanında silindiğinden emin olmanız önerilir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: müşteri, işlem kaynakları Için onaylanan Azure kaynaklarını ve onaylanan yazılımları tanımlamalıdır.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Kılavuz**: Azure Güvenlik Merkezi 'Nde kullanılabilen Uyarlamalı uygulama denetimi özelliğini kullanın. Güvenlik Merkezi 'nden, Windows ve Linux, Azure ve Azure dışı makinelerde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Ayrıca makinelerinizin kötü amaçlı yazılımlara karşı korunmasına da yardımcı olur. 

Bu özellik hem Azure hem de Azure olmayan Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux makineler için kullanılabilir.

Güvenlik Merkezi, makinelerinizde çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zeka izin verilenler listesi oluşturur. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir ve şunları yapmanıza olanak sağlar:
- Kötü amaçlı yazılımdan koruma çözümlerinin kaçırılanlanlar da dahil olmak üzere kötü amaçlı uygulamalar çalıştırma girişimlerini engelleyin veya uyarır.

- Kuruluşunuzun yalnızca lisanslı yazılım kullanımını gerektiren kuruluş güvenlik ilkelerine uygun hareket etme.
- Ortamınızda istenmeyen yazılımların kullanılmasını önleme.
- Eski ve desteklenmeyen uygulamaların çalışmasını önleme.
- Kuruluşunuzda kullanılmasına izin verilmeyen belirli yazılım araçlarını engelleme.
- BT ekibinin uygulama üzerinden gizli verilere erişimi denetlemesini mümkün kılma.

Başvurulan bağlantılarda daha fazla ayrıntı bulunabilir.

- [Uyarlamalı uygulama denetimleri](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Kılavuz**: Azure Güvenlik Merkezi 'Nde kullanılabilen Uyarlamalı uygulama denetimi özelliğini kullanın. Güvenlik Merkezi 'nden, Windows ve Linux, Azure ve Azure dışı makinelerde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Ayrıca makinelerinizin kötü amaçlı yazılımlara karşı korunmasına da yardımcı olur. 

Bu özellik hem Azure hem de Azure olmayan Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux makineler için kullanılabilir.

Güvenlik Merkezi, makinelerinizde çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zeka izin verilenler listesi oluşturur. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir ve şunları yapmanıza olanak sağlar:

- Kötü amaçlı yazılımdan koruma çözümlerinin kaçırılanlanlar da dahil olmak üzere kötü amaçlı uygulamalar çalıştırma girişimlerini engelleyin veya uyarır.

- Kuruluşunuzun yalnızca lisanslı yazılım kullanımını gerektiren kuruluş güvenlik ilkelerine uygun hareket etme.

- Ortamınızda istenmeyen yazılımların kullanılmasını önleme.

- Eski ve desteklenmeyen uygulamaların çalışmasını önleme.

- Kuruluşunuzda kullanılmasına izin verilmeyen belirli yazılım araçlarını engelleme.

- BT ekibinin uygulama üzerinden gizli verilere erişimi denetlemesini mümkün kılma.

Başvurulan bağlantılarda daha fazla ayrıntı bulunabilir.

- [Uyarlamalı uygulama denetimleri](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Kılavuz**: Azure Güvenlik Merkezi 'Nde kullanılabilen Uyarlamalı uygulama denetimi özelliğini kullanın. Güvenlik Merkezi 'nden, Windows ve Linux, Azure ve Azure dışı makinelerde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Ayrıca makinelerinizin kötü amaçlı yazılımlara karşı korunmasına da yardımcı olur. 

Bu özellik hem Azure hem de Azure olmayan Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux makineler için kullanılabilir.

Güvenlik Merkezi, makinelerinizde çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zeka izin verilenler listesi oluşturur. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir ve şunları yapmanıza olanak sağlar:

- Kötü amaçlı yazılımdan koruma çözümlerinin kaçırılanlanlar da dahil olmak üzere kötü amaçlı uygulamalar çalıştırma girişimlerini engelleyin veya uyarır.

- Kuruluşunuzun yalnızca lisanslı yazılım kullanımını gerektiren kuruluş güvenlik ilkelerine uygun hareket etme.

- Ortamınızda istenmeyen yazılımların kullanılmasını önleme.

- Eski ve desteklenmeyen uygulamaların çalışmasını önleme.

- Kuruluşunuzda kullanılmasına izin verilmeyen belirli yazılım araçlarını engelleme.

- BT ekibinin uygulama üzerinden gizli verilere erişimi denetlemesini mümkün kılma.

Başvurulan bağlantılarda daha fazla ayrıntı bulunabilir.

- [Uyarlamalı uygulama denetimleri](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Kılavuz**: Azure Güvenlik Merkezi 'Nde kullanılabilen Uyarlamalı uygulama denetimi özelliğini kullanın. Güvenlik Merkezi 'nden, Windows ve Linux, Azure ve Azure dışı makinelerde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Ayrıca makinelerinizin kötü amaçlı yazılımlara karşı korunmasına da yardımcı olur. 

Bu özellik hem Azure hem de Azure olmayan Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux makineler için kullanılabilir.

Güvenlik Merkezi, makinelerinizde çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zeka izin verilenler listesi oluşturur. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir ve şunları yapmanıza olanak sağlar:

- Kötü amaçlı yazılımdan koruma çözümlerinin kaçırılanlanlar da dahil olmak üzere kötü amaçlı uygulamalar çalıştırma girişimlerini engelleyin veya uyarır.

- Kuruluşunuzun yalnızca lisanslı yazılım kullanımını gerektiren kuruluş güvenlik ilkelerine uygun hareket etme.

- Ortamınızda istenmeyen yazılımların kullanılmasını önleme.

- Eski ve desteklenmeyen uygulamaların çalışmasını önleme.

- Kuruluşunuzda kullanılmasına izin verilmeyen belirli yazılım araçlarını engelleme.

- BT ekibinin uygulama üzerinden gizli verilere erişimi denetlemesini mümkün kılma.

Başvurulan bağlantılarda daha fazla ayrıntı bulunabilir.

- [Uyarlamalı uygulama denetimleri](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Kılavuz**: Azure Güvenlik Merkezi 'Nde kullanılabilen Uyarlamalı uygulama denetimi özelliğini kullanın. Güvenlik Merkezi 'nden, Windows ve Linux, Azure ve Azure dışı makinelerde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Ayrıca makinelerinizin kötü amaçlı yazılımlara karşı korunmasına da yardımcı olur. 

Bu özellik hem Azure hem de Azure olmayan Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux makineler için kullanılabilir.

Güvenlik Merkezi, makinelerinizde çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zeka izin verilenler listesi oluşturur. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir ve şunları yapmanıza olanak sağlar:
- Kötü amaçlı yazılımdan koruma çözümlerinin kaçırılanlanlar da dahil olmak üzere kötü amaçlı uygulamalar çalıştırma girişimlerini engelleyin veya uyarır.

- Kuruluşunuzun yalnızca lisanslı yazılım kullanımını gerektiren kuruluş güvenlik ilkelerine uygun hareket etme.
- Ortamınızda istenmeyen yazılımların kullanılmasını önleme.
- Eski ve desteklenmeyen uygulamaların çalışmasını önleme.
- Kuruluşunuzda kullanılmasına izin verilmeyen belirli yazılım araçlarını engelleme.
- BT ekibinin uygulama üzerinden gizli verilere erişimi denetlemesini mümkün kılma.

Başvurulan bağlantılarda daha fazla ayrıntı bulunabilir.

- [Uyarlamalı uygulama denetimleri](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: kullanıcıların işlem kaynaklarında betikleri yürütme yeteneğini sınırlayın

**Kılavuz**: Azure Güvenlik Merkezi 'Nde kullanılabilen Uyarlamalı uygulama denetimi özelliğini kullanın. Güvenlik Merkezi 'nden, Windows ve Linux, Azure ve Azure dışı makinelerde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Ayrıca makinelerinizin kötü amaçlı yazılımlara karşı korunmasına da yardımcı olur. 

Bu özellik hem Azure hem de Azure olmayan Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux makineler için kullanılabilir.

Güvenlik Merkezi, makinelerinizde çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zeka izin verilenler listesi oluşturur. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir ve şunları yapmanıza olanak sağlar:

- Kötü amaçlı yazılımdan koruma çözümlerinin kaçırılanlanlar da dahil olmak üzere kötü amaçlı uygulamalar çalıştırma girişimlerini engelleyin veya uyarır.

- Kuruluşunuzun yalnızca lisanslı yazılım kullanımını gerektiren kuruluş güvenlik ilkelerine uygun hareket etme.

- Ortamınızda istenmeyen yazılımların kullanılmasını önleme.

- Eski ve desteklenmeyen uygulamaların çalışmasını önleme.

- Kuruluşunuzda kullanılmasına izin verilmeyen belirli yazılım araçlarını engelleme.

- BT ekibinin uygulama üzerinden gizli verilere erişimi denetlemesini mümkün kılma.

Başvurulan bağlantılarda daha fazla ayrıntı bulunabilir.

- [Uyarlamalı uygulama denetimleri](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Kılavuz**: Azure Cloud Services ile hassas veya yüksek riskli uygulamalar için yalıtım sağlamak üzere ayrı abonelikler veya yönetim grupları uygulayın.

Bir ağ güvenlik grubu kullanın, bir gelen güvenlik kuralı oluşturun, http gibi bir hizmet seçin, özel bir bağlantı noktası seçin, buna bir öncelik ve ad verin. Öncelik kuralların uygulanma sırasını etkiler, sayısal değeri azaltır, önceki kural uygulanır. Ağ trafiğini iş gereksinimlerinize göre yalıtmak veya segmentlere ayırmak için, ağ güvenlik grubunuzu bir alt ağ veya belirli bir ağ arabirimiyle ilişkilendirmeniz gerekir.

Başvurulan bağlantılarda daha fazla ayrıntı bulunabilir.

- [Öğretici-Azure portal kullanarak bir ağ güvenlik grubu ile ağ trafiğini filtreleme](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure Güvenlik Merkezi 'Ndeki önerileri Azure Cloud Services kaynaklarınız için güvenli bir yapılandırma temeli olarak kullanın. 

Azure portal, &amp; hizmet kaynaklarınız için geçerli olan önerileri görmek Için Güvenlik Merkezi ' ni ve ardından işlem uygulamaları ' nı ve Azure Cloud Services ' nı seçin.

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure Cloud Services için geçerli değildir. Klasik dağıtım modelini temel alır. Güvenli Azure Kaynak yapılandırmalarının bakımını yapmak için bir üçüncü taraf çözümü kullanmanız önerilir

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: Azure bulut hizmeti 'nin yapılandırma dosyası bir kaynağın işletim özniteliklerini depolar. Yapılandırma dosyalarının bir kopyasını güvenli bir depolama hesabına kaydedebilirsiniz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Cloud Services için geçerli değildir. Bu, klasik dağıtım modelini temel alır ve Azure Resource Manager dağıtım tabanlı yapılandırma araçları tarafından yönetilemez.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Cloud Services için geçerli değildir. Bu öneri, hizmet olarak altyapı (IaaS) tabanlı işlem kaynakları için geçerlidir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: Azure kaynaklarınız için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'ni kullanın.  

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Kılavuz**: Azure Güvenlik Merkezi 'nde işlem &amp; uygulamaları özelliği ' ni seçin ve sanal makineler, sunucular ve kapsayıcılar önerilerini izleyin.

- [Azure Güvenlik Merkezi'ndeki kapsayıcı önerilerini anlama](../security-center/container-security.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Kılavuz**: Azure Cloud Services, klasik bir dağıtım modelini temel alır ve Azure Key Vault tümleştirmez.

Azure Cloud Services 'de kullanılan kimlik bilgileri gibi gizli dizileri güvenli hale getirebilirsiniz, böylece her seferinde bir parola yazmanız gerekmez. Başlamak için, bir düz metin parolası belirtin, ConvertTo-SecureString, PowerShell komutunu kullanarak güvenli bir dizeye dönüştürün. Sonra, bu güvenli dizeyi ConvertFrom-SecureString kullanarak şifrelenmiş bir standart dizeye dönüştürün.  Artık bu şifrelenen standart dizeyi, set-Content kullanarak bir dosyaya kaydedebilirsiniz.

Ayrıca, Azure Cloud Services 'de kullanılan sertifikaların özel anahtarlarını güvenli bir depolama alanına depolamanız önerilir.

- [Uzak Masaüstü 'Nü PowerShell 'den yapılandırma](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: her seferinde bir parola yazmanız gerekmez diye Azure Cloud Services 'de kullanılan kimlik bilgileri gibi güvenli gizli dizileri. 
 

Başlamak için, bir düz metin parolası belirtin, ConvertTo-SecureString, PowerShell komutunu kullanarak güvenli bir dize ile değiştirin. Sonra, bu güvenli dizeyi ConvertFrom-SecureString kullanarak şifrelenmiş bir standart dizeye dönüştürün. Şimdi bu şifreli standart dizeyi Set-Content komutu kullanarak bir dosyaya kaydedin.

Azure Cloud Services 'de kullanılan sertifikaların özel anahtarlarını güvenli bir depolama konumuna depolayın.

- [Uzak Masaüstü 'Nü PowerShell 'den yapılandırma](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanma

**Rehberlik**: Azure Cloud Services ve sanal makineler Için Microsoft kötü amaçlı yazılımdan koruma sunulmaktadır. Virüslerin, casus yazılımların ve diğer kötü amaçlı yazılımların tanımlanmasına ve kaldırılmasına yardımcı olan ücretsiz bir gerçek zamanlı koruma. Bilinen kötü amaçlı veya istenmeyen yazılımlar kendisini yüklemeyi veya Azure sistemlerinizde çalıştırmayı denediğinde uyarı oluşturur. 

Kötü amaçlı yazılımdan koruma yapılandırmasını "Get-AzureServiceAntimalwareConfig" ile almak için PowerShell tabanlı kötü amaçlı yazılımdan koruma cmdlet 'ini kullanın.

Azure Cloud Services 'de başlangıç görevinde bir PowerShell betiği ile kötü amaçlı yazılımdan koruma uzantısını etkinleştirin.

Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi özelliğini, akıllı, otomatik, uçtan uca bir çözüm olarak seçin. Makinelerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur ve kötü amaçlı yazılımdan koruma çözümleri tarafından kaçırılmayabilir.

- [Azure Cloud Services için otomatikleştirilmiş bir şekilde kötü amaçlı yazılımdan koruma uzantısı nasıl ekleyebilirim?](./cloud-services-configuration-and-management-faq.md#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Kötü amaçlı yazılımdan koruma senaryoları](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Uyarlamalı uygulama denetimleri](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Müşteri, kendi olay yanıt planının oluşturulmasına yardımcı olması için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzunu da kullanabilir](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Kılavuz**: Azure Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır. 

Abonelikleri açık bir şekilde işaretleyin (örneğin, üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin. 

- [NıST 'nin yayını: BT planları ve özellikleri için test, eğitim ve alıştırma programlarını inceleyin](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin. 

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Uyarıları Azure Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanabilirsiniz. 

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md) 

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Rehberlik**: güvenlik uyarılarında ve önerilerinde "Logic Apps" aracılığıyla yanıtları otomatik olarak tetiklemek Için Azure Güvenlik Merkezi 'Nde Iş akışı Otomasyonu özelliğini kullanın.

- [Iş akışı otomasyonu ve Logic Apps yapılandırma](../security-center/workflow-automation.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft bulut Penme test kurallarını izleyin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın. 

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin
