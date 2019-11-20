---
title: VM'ler için Azure İzleyici (Önizleme) sık sorulan sorular | Microsoft Docs
description: VM'ler için Azure İzleyici Azure 'da, Azure VM işletim sisteminin sistem durumunu ve performans izlemesini birleştiren bir çözümdür ve uygulama bileşenlerini ve bağımlılıklarını diğer kaynaklarla otomatik olarak bulabilir ve arasındaki iletişimi eşler yapıştırabilirsiniz. Bu makalede yaygın soruların yanıtları vardır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/09/2018
ms.openlocfilehash: 675277a33a5613507297f1c77ee9cef3215b22a2
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555203"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>VM'ler için Azure İzleyici (Önizleme) sık sorulan sorular
Bu Microsoft SSS, VM'ler için Azure İzleyici hakkında sık sorulan soruların bir listesidir. Çözümle ilgili başka sorularınız varsa, [tartışma forumuna](https://feedback.azure.com/forums/34192--general-feedback) gidin ve sorularınızı gönderin. Bir soru sıkça sorulduğunda, hızlı ve kolay bir şekilde bulunabilmesi için bu makaleye ekleyeceğiz.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Mevcut bir çalışma alanına ekleyebilir miyim?
Sanal makineleriniz zaten bir Log Analytics çalışma alanına bağlıysa, [burada](vminsights-enable-overview.md#prerequisites)listelenen desteklenen bölgelerden birinde olması kaydıyla, VM'ler için Azure izleyici ekleme sırasında bu çalışma alanını kullanmaya devam edebilirsiniz.

Ekleme sırasında, çalışma alanı için performans sayaçlarını yapılandıracağız, bu bilgileri VM'ler için Azure İzleyici ' de görüntüleme ve analize yönelik tüm VM 'Ler raporlama verilerinin toplamaya başlamasına neden olacak.  Sonuç olarak, seçili çalışma alanına bağlı tüm VM 'lerden performans verilerini görürsünüz.  Sistem durumu ve eşleme özellikleri yalnızca eklemek üzere belirttiğiniz VM 'Ler için etkinleştirilir.

Hangi performans sayaçlarının etkinleştirildiği hakkında daha fazla bilgi için, [etkinleştirme genel bakış](vminsights-enable-overview.md#performance-counters-enabled) makalemize bakın.

## <a name="can-i-onboard-to-a-new-workspace"></a>Yeni bir çalışma alanına ekleyebilir miyim? 
VM 'niz Şu anda mevcut bir Log Analytics çalışma alanına bağlı değilse, verilerinizi depolamak için yeni bir çalışma alanı oluşturmanız gerekir. Azure portal üzerinden VM'ler için Azure İzleyici için tek bir Azure VM yapılandırırsanız, yeni bir varsayılan çalışma alanı oluşturma otomatik olarak yapılır.

Betik tabanlı yöntemi kullanmayı seçerseniz, bu adımlar [Azure PowerShell veya Kaynak Yöneticisi şablonu kullanarak etkinleştirme VM'ler için Azure izleyici (Önizleme)](vminsights-enable-at-scale-powershell.md) içinde ele alınmıştır. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>VM 'im mevcut bir çalışma alanına zaten bildirildiğimde ne yapmam gerekiyor?
Sanal makinelerinizden zaten veri topluyorsanız, mevcut bir Log Analytics çalışma alanına verileri raporlamak için onu zaten yapılandırmış olabilirsiniz.  Çalışma alanı desteklenen bölgelerden birinde olduğu sürece, VM'ler için Azure İzleyici önceden var olan çalışma alanına etkinleştirebilirsiniz.  Zaten kullanmakta olduğunuz çalışma alanı desteklenen bölgelerden birinde değilse, şu anda VM'ler için Azure İzleyici içine yükleyemezsiniz.  Ek bölgeleri desteklemek için etkin bir şekilde çalışıyoruz.

>[!NOTE]
>Çalışma alanına rapor veren tüm VM 'Leri etkileyen çalışma alanı için performans sayaçlarını yapılandıracağız, bunlara VM'ler için Azure İzleyici eklemek isteyip istemediğinizi seçmiş olursunuz. Çalışma alanı için performans sayaçlarının nasıl yapılandırıldığı hakkında daha fazla bilgi için lütfen [belgelerimize](../../azure-monitor/platform/data-sources-performance-counters.md)bakın. VM'ler için Azure İzleyici için yapılandırılan sayaçlar hakkında daha fazla bilgi için lütfen [etkinleştirme VM'ler için Azure izleyici](vminsights-enable-overview.md#performance-counters-enabled) makalemize bakın.  

## <a name="why-did-my-vm-fail-to-onboard"></a>Sanal makinem neden eklenemedi?
Azure portal bir Azure VM 'si eklenirken aşağıdaki adımlar oluşur:

* Bu seçenek işaretliyse, varsayılan bir Log Analytics çalışma alanı oluşturulur.
* Performans sayaçları seçili çalışma alanı için yapılandırıldı. Bu adım başarısız olursa, bazı performans grafiklerinin ve tablolarının eklendi olduğunuz VM için verileri göstermediğini fark edersiniz. [Burada](vminsights-enable-at-scale-powershell.md#enable-performance-counters)belgelenen PowerShell betiğini çalıştırarak bu hatayı çözebilirsiniz.
* Log Analytics Aracısı, gerekli olduğunu tespit ederseniz bir VM uzantısı kullanılarak Azure VM 'lerine yüklenir.  
* VM'ler için Azure İzleyici Map bağımlılık Aracısı, gerekli olduğunu tespit ederseniz bir uzantı kullanılarak Azure VM 'lerine yüklenir.  
* Sistem durumu özelliğini destekleyen Azure Izleyici bileşenleri, gerekirse yapılandırılır ve VM, sistem durumu verilerini raporlamak üzere yapılandırılır.

Ekleme işlemi sırasında, portalda size bir bildirim durumu döndürmek için yukarıdaki her bir durumu denetliyoruz. Çalışma alanının yapılandırması ve aracı yüklemesi genellikle 5 ila 10 dakika sürer. Portalda izleme ve sistem durumu verilerinin görüntülenmesi 10 dakikaya kadar 5 dakika sürer.  

Ekleme işlemini başlattınız ve VM 'nin eklendi olması gerektiğini belirten iletiler görürseniz, VM 'nin işlemi tamamlaması için 30 dakikaya kadar bekleyin. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Yalnızca VM'ler için Azure İzleyici etkinleştirdim, neden tüm sanal makinelerimin sistem durumu özelliği tarafından izlendiğini görüyorum?
Durum özelliği, tek bir VM için işlem başlatıldığında bile Log Analytics çalışma alanına bağlı tüm VM 'Ler için etkinleştirilir.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Durum ölçütlerinin bir koşulu değerlendirirken Zamanlamayı değiştirebilir miyim?
Hayır, durum ölçütlerinin zaman dilimi ve sıklığı bu sürümle değiştirilemez. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>İzleme gerektirmeyen bir koşul için sistem durumu ölçütlerini devre dışı bırakabilir miyim?
Bu sürümde durum ölçütleri devre dışı bırakılamaz.

## <a name="are-the-health-alert-severities-configurable"></a>Sistem sağlığı uyarısı önem dereceleri yapılandırılabilir mi?  
Sağlık uyarısı önem derecesi değiştirilemez, yalnızca etkinleştirilebilir veya devre dışı bırakılabilir. Ayrıca, bazı uyarı önem dereceleri durum ölçütlerine göre güncelleştirme yapılır. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Belirli bir sistem durumu ölçütlerinin ayarlarını yeniden yapılandırdığımda, belirli bir örneğe kapsam eklenebilir mi?  
Bir sistem durumu ölçüt örneğinin herhangi bir ayarını değiştirirseniz, Azure VM 'de aynı türdeki tüm durum ölçütleri örnekleri değiştirilir. Örneğin, mantıksal disk C: öğesine karşılık gelen disk boş alan sistem durumu ölçüt örneğinin eşiği değiştirilirse, bu eşik aynı VM için bulunan ve izlenen diğer tüm mantıksal diskler için geçerlidir.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Sistem durumu özelliği mantıksal işlemcileri ve çekirdekleri izler mi?
Hayır, tek tek işlemci ve mantıksal işlemci düzeyi durum ölçütleri bir Windows için dahil değildir, Azure VM 'nin kullanabildiği toplam mantıksal CPU sayısına göre CPU basıncını etkin bir şekilde değerlendirmek için varsayılan olarak yalnızca toplam CPU kullanımı izlenir. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Tüm durum ölçütleri eşikleri yapılandırılabilir mi?  
Windows VM 'yi hedefleyen sistem durumu ölçütlerine yönelik eşikler, sistem durumları *çalışıyor* veya *kullanılabilir*olarak ayarlandığı için değiştirilebilir değildir. [Iş yükü IZLEYICISI API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)'sinden sistem durumunu sorgulayıp, şu durumlarda hizmet veya varlık için **4** *eşik* değeriyle **LessThan** veya **GreaterThan** *değerini görüntüler* :
   - DNS Istemci hizmeti sistem durumu – hizmeti çalışmıyor. 
   - DHCP istemci hizmeti sistem durumu – hizmeti çalışmıyor. 
   - RPC hizmeti sistem durumu – hizmeti çalışmıyor. 
   - Windows Güvenlik Duvarı hizmeti sistem durumu – hizmeti çalışmıyor.
   - Windows olay günlüğü hizmeti sistem durumu – hizmeti çalışmıyor. 
   - Sunucu hizmeti sistem durumu – hizmeti çalışmıyor. 
   - Windows Uzaktan Yönetim hizmeti sistem durumu – hizmeti çalışmıyor. 
   - Dosya sistemi hatası veya bozulması – mantıksal disk kullanılamıyor.

Aşağıdaki Linux durum ölçütlerine yönelik eşikler, sistem durumu zaten *true*olarak ayarlandığından değiştirilebilir değildir. Sistem durumu, içeriğine bağlı olarak varlık için Iş yükü Izleme API 'sinden sorgulandığında, değeri **LessThan** ve *eşik* değeri **1** olan *ComparisonOperator* ' i görüntüler.
   - Mantıksal disk durumu – mantıksal disk çevrimiçi değil/kullanılabilir
   - Disk durumu – disk çevrimiçi/kullanılabilir değil
   - Ağ bağdaştırıcısı durumu-ağ bağdaştırıcısı devre dışı

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Sistem durumu özelliğine dahil olan uyarıları değiştirmek Nasıl yaparım??
Her bir sistem durumu ölçütü için tanımlanan uyarı kuralları Azure portal gösterilmez. Bir sistem durumu uyarı kuralını yalnızca [Iş yükü IZLEYICISI API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)'sinde etkinleştirebilir veya devre dışı bırakabilirsiniz. Ayrıca, Azure portal sistem durumu uyarıları için bir [Azure izleyici eylem grubu](../../azure-monitor/platform/action-groups.md) atayamazsınız. Bir sistem durumu uyarısı tetiklendiğinde tetiklenecek bir eylem grubunu yapılandırmak için yalnızca bildirim ayarı API 'sini kullanabilirsiniz. Şu anda, VM 'ye karşı gerçekleştirilen tüm *sistem durumu uyarılarının* aynı eylem gruplarını tetiklenmesi IÇIN bir VM 'ye karşı eylem grupları atayabilirsiniz. Geleneksel Azure uyarılarının aksine, her bir sistem durumu uyarı kuralı için ayrı bir eylem grubu kavramı yoktur. Ayrıca, sistem durumu uyarıları tetiklendiğinde yalnızca e-posta veya SMS bildirimleri sağlamak üzere yapılandırılan eylem grupları desteklenir. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>VM 'im için Performans grafiklerinde bir veya daha fazla veri görmüyorum
Disk tablosunda veya bazı performans grafiklerinde performans verilerini görmüyorsanız, performans sayaçlarınız çalışma alanında yapılandırılmamış olabilir. Çözümlemek için aşağıdaki [PowerShell betiğini](vminsights-enable-at-scale-powershell.md#enable-with-powershell)çalıştırın.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM'ler için Azure İzleyici eşleme özelliği Hizmet Eşlemesi nasıl farklıdır?
VM'ler için Azure İzleyici Map özelliği Hizmet Eşlemesi tabanlıdır, ancak aşağıdaki farklılıklara sahiptir:

* Harita görünümüne VM dikey penceresinden ve Azure Izleyici altındaki VM'ler için Azure İzleyici erişilebilir.
* Haritadaki bağlantılar artık tıklatılabilir ve seçili bağlantı için yan bölmede bağlantı ölçüm verilerinin bir görünümünü görüntüler.
* Daha karmaşık haritaları daha iyi destekleyecek şekilde eşlemeler oluşturmak için kullanılan yeni bir API vardır.
* İzlenen VM 'Ler artık istemci grubu düğümüne dahildir ve halka grafik gruptaki izlenen ve izlenmeyen sanal makinelerin oranını gösterir.  Grup genişletildiğinde makine listesini filtrelemek için de kullanılabilir.
* İzlenen sanal makineler artık sunucu bağlantı noktası grubu düğümlerine dahildir ve halka grafik gruptaki izlenen ve izlenmeyen makinelerin oranını gösterir.  Grup genişletildiğinde makine listesini filtrelemek için de kullanılabilir.
* Harita stili, Application Insights 'tan uygulama haritası ile daha tutarlı olacak şekilde güncelleştirilmiştir.
* Yan paneller güncelleştirilmiştir ve Hizmet Eşlemesi-Güncelleştirme Yönetimi, Değişiklik İzleme, güvenlik ve hizmet masası 'nda desteklenen tam tümleştirme kümesine sahip değildir. 
* Eşlenecek grupları ve makineleri seçme seçeneği güncelleştirildi ve artık abonelikleri, kaynak gruplarını, Azure sanal makine ölçek kümelerini ve bulut hizmetlerini desteklemektedir.
* VM'ler için Azure İzleyici Map özelliğinde yeni Hizmet Eşlemesi makine grupları oluşturamazsınız.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Performans grafiklerim neden noktalı çizgiler gösteriyor?
Bu, birkaç nedenden dolayı oluşabilir.  Veri koleksiyonunda bir boşluk olduğu durumlarda, satırları noktalı olarak belirledik.  Performans sayaçları etkin olan veri örnekleme sıklığını değiştirdiyseniz (varsayılan ayar, her 60 saniyede bir veri toplamaktır), grafik için dar bir zaman aralığı seçerseniz ve örnekleme sıklığınızdan daha küçükse, grafikte noktalı çizgileri görebilirsiniz. grafikte kullanılan demet boyutu (örneğin, örnekleme sıklığı 10 dakikadır ve grafikteki her demet 5 dakikadır).  Görüntülenecek daha geniş bir zaman aralığı seçilmesi, grafik çizgilerinin bu durumda nokta yerine düz çizgiler olarak görünmesine neden olmalıdır.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Gruplar VM'ler için Azure İzleyici destekleniyor mu?
Evet, bağımlılık aracısını yükledikten sonra abonelikleri, kaynak grubunu, sanal makine ölçek kümelerini ve bulut hizmetlerini temel alan grupları göstermek için VM 'lerden bilgi topladık.  Hizmet Eşlemesi kullanıyorsanız ve makine grupları oluşturduysanız, bunlar da görüntülenir.  Siz görüntülemekte olduğunuz çalışma alanı için oluşturduysanız, bilgisayar grupları gruplar filtresinde da görünür. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Nasıl yaparım? toplam Performans grafiklerinde 95. yüzdebirlik satırını neyin yönlendirdiğini görmek için ayrıntılara bakın.
Varsayılan olarak, liste, en düşük 5 ' lik değere sahip makineleri gösteren kullanılabilir bellek grafiği dışında, seçili ölçüm için en yüksek 95. yüzdebirlik değerini içeren VM 'Leri gösterecek şekilde sıralanır.  Grafiğe tıklandığında, uygun ölçüm seçili olan **Ilk N liste** görünümü açılır.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Harita özelliği farklı VNET 'lerde ve alt ağlarda yinelenen IP 'Leri nasıl işler?
IP aralıklarını VM 'Ler veya alt ağlar ve sanal makine ölçek kümeleri arasında çoğaltdıysanız, VM'ler için Azure İzleyici eşlemenin yanlış bilgileri görüntülemesine neden olabilir. Bu bilinen bir sorundur ve bu deneyimi geliştirme seçeneklerini araştırıyoruz.

## <a name="does-map-feature-support-ipv6"></a>Harita özelliği IPv6 'Yı destekliyor mu?
Map özelliği şu anda yalnızca IPv4 'Ü destekliyor ve IPv6 desteğini araştırıyoruz. IPv6 içinde tünel oluşturulan IPv4 de destekliyoruz.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Bir kaynak grubu veya başka bir büyük grup için bir harita yüklediğimde haritanın görüntülemesi zordur
Büyük ve karmaşık yapılandırmaların işlenmesi amacıyla haritada geliştirmeler yaptığımız için, bir haritanın çok fazla düğüm, bağlantı ve düğüm kümesi olarak çalışan bir düğüm olabileceğini fark ettik.  Ölçeklenebilirliği artırmak için desteği geliştirmeye devam etmek için çalışıyoruz.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Performans sekmesindeki ağ grafiği neden Azure VM 'ye Genel Bakış sayfasındaki ağ grafiğinden farklı görünüyor?

Bir Azure VM 'ye ilişkin genel bakış sayfası, konağın Konuk VM 'deki etkinlik ölçüsünün bulunduğu grafikleri görüntüler.  Azure VM 'de ağ grafiğine genel bakış için yalnızca faturalandırılacak ağ trafiği görüntülenir.  Bu, sanal ağlar arası trafiği içermez.  VM'ler için Azure İzleyici için gösterilen veriler ve grafikler, Konuk VM 'deki verileri temel alır ve ağ grafiği, sanal ağlar dahil olmak üzere bu VM 'ye gelen ve giden tüm TCP/IP trafiğini görüntüler.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Yanıt süresi, VMConnection 'da depolanan ve bağlantı panelinde ve çalışma kitaplarında görüntülenmekte olan veriler için nasıl ölçülür?

Yanıt süresi yaklaşık bir değer. Uygulamanın kodunu işaretlemediğimiz için, bir isteğin ne zaman başladığını ve yanıtın ne zaman ulaştığında gerçekten haberdar ettik. Bunun yerine, bir bağlantı üzerinden gönderilen verileri gözlemliyoruz ve sonra bu bağlantıya geri dönüş yaptık. Aracımız, bu gönderme ve alma girişimlerini takip eder ve bunları eşleştirmeye çalışır: bir dizi Gönderen, sonrasında bir alma dizisi, istek/yanıt çifti olarak yorumlanır. Bu işlemler arasındaki zamanlama yanıt süresi olur. Ağ gecikmesi ve sunucu işleme süresi de dahil olur.

Bu yaklaşık, istek/yanıt tabanlı protokollerde iyi sonuç verir: tek bir istek bağlantı üzerinde geçer ve tek bir yanıt ulaşır. Bu, HTTP (S) için (ardışık düzen olmadan), ancak diğer protokollerin karşılanmadığı durumdur.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics ücretsiz fiyatlandırma planıyla karşılaştığım kısıtlamalardır.
*Ücretsiz* fiyatlandırma katmanını kullanarak Azure izleyici 'yi bir Log Analytics çalışma alanıyla yapılandırdıysanız, VM'ler için Azure izleyici eşleme özelliği yalnızca çalışma alanına bağlı beş bağlı makineyi destekler. Ücretsiz bir çalışma alanına bağlı beş VM varsa, VM 'lerden birinin bağlantısını kesip daha sonra yeni bir VM 'yi bağladığınızda yeni VM izlenmiyor ve harita sayfasında yansıtılmaz.  

Bu koşulun altında, VM 'yi açıp daha önce sanal makineye yüklendikten sonra bile sağ bölmedeki **öngörüleri (Önizleme)** seçtiğinizde **Şimdi dene** seçeneği istenir.  Ancak, bu VM VM'ler için Azure İzleyici için eklendi değilse, genellikle bu sanal makine için bir seçenek istenmez. 

## <a name="next-steps"></a>Sonraki adımlar
Gözden geçirme, sanal makinelerinizi izlemeyi etkinleştirmek için gereksinimleri ve yöntemleri anlamak üzere [VM'ler için Azure izleyici etkinleştirin](vminsights-enable-overview.md) .
