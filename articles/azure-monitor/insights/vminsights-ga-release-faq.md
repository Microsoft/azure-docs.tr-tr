---
title: VM'ler için Azure İzleyici (GA) sık sorulan sorular | Microsoft Docs
description: VM'ler için Azure İzleyici Azure 'da, Azure VM işletim sisteminin sistem durumunu ve performans izlemesini birleştiren bir çözümdür ve uygulama bileşenlerini ve bağımlılıklarını diğer kaynaklarla otomatik olarak bulabilir ve arasındaki iletişimi eşler yapıştırabilirsiniz. Bu makalede, GA sürümü hakkında sık sorulan sorular yanıtlanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 523fb2d3a3b148afc9219e666c2fbe7fa40d58ad
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553804"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>VM'ler için Azure İzleyici genel kullanıma açık (GA) sık sorulan sorular

Kısa süre önce [Azure Update](https://azure.microsoft.com/blog/) blogundan, Ekim 'de ve Kasım 2019 ' de ortaya çıkan bazı planlı değişiklikleri duyuruyoruz. Bu değişiklikler hakkında daha fazla ayrıntı bu genel kullanılabilirlik SSS bölümünde ele alınmıştır.

## <a name="updates-for-azure-monitor-for-vms"></a>VM'ler için Azure İzleyici için güncelleştirmeler

Kasım ayında VM'ler için Azure İzleyici yeni bir sürümünü serbest bırakıyoruz. Bu sürümden sonra sanal makineler için Azure Izleyicilerini etkinleştiren müşteriler, otomatik olarak yeni sürümü alır, ancak VM'ler için Azure İzleyici kullanan mevcut müşterilerin yükseltilmesi istenir.  Bu SSS ve Belgelerimiz, birden çok çalışma alanı genelinde büyük dağıtımlarınız varsa toplu bir yükseltme gerçekleştirmeye yönelik rehberlik sunar.

Bu yükseltme ile VM'ler için Azure İzleyici performans veri kümeleri artık, [kapsayıcılar Için Azure izleyici](container-insights-overview.md)ile aynı `InsightsMetrics` tablosunda depolanır ve iki veri kümesini sorgulamanızı kolaylaştırır. Ayrıca, daha önce kullandığımız tabloda depolayabildiğimiz daha fazla farklı veri kümesi depolayabileceksiniz.  Performans görünümlerimiz, bu yeni tabloyu kullanacak şekilde de güncelleştirilecektir.

Bağlantı veri kümelerimiz için yeni veri türlerine geçeceğiz. @No__t_0 ve `ServiceMapProcess_CL` özel günlük tabloları kullanan depolanan veriler, `VMComputer` ve `VMProcess` adlı adanmış veri türlerine taşınır.  Adanmış veri türlerine geçerek veri alımı için bu önceliğe izin verebilir ve tablo şeması tüm müşteriler genelinde standartlaştırılmış olacaktır.

Var olan müşterilerin yükseltmesini isteyen iş akışının kesintiye uğramasına neden olduğunu fark ettik. Bu, daha sonra GA 'ye ulaştıktan sonra genel önizleme aşamasında bunu yapmayı tercih ettiğimiz anlamına gelir.

## <a name="what-will-change"></a>Ne değişecektir?

Şu anda VM'ler için Azure İzleyici için ekleme işlemini tamamladığınızda, izleme verilerinizi depolamak için seçtiğiniz çalışma alanında Hizmet Eşlemesi çözümü etkinleştirin ve ardından sanal makinelerinizden Topladığımız veriler için performans sayaçlarını yapılandırın. Önümüzdeki hafta içinde, bu verileri Log Analytics depolamak için yeni bir konum ile birlikte veri toplamaya yönelik ek yetenekler dahil olmak üzere **Vminsıghts**adlı yeni bir çözüm yayımlayacağız.

Çalışma alanınızdaki performans sayaçlarını kullanma sürecimiz, verileri Log Analytics ' deki perf tablosuna gönderir.  Bu yeni çözüm, verileri `InsightsMetrics` adlı bir tabloya göndererek, kapsayıcılar için de Azure Izleyici tarafından da kullanılır. Bu tablo şeması, Performans Tablosu biçimiyle uyumlu olmayan ek ölçümleri ve hizmet veri kümelerini depolamamızı sağlar.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>Vminsıghts çözümünü yüklediğimde çalışma alanım 'da performans sayaçları ne yapmam gerekir?

VM'ler için Azure İzleyici etkinleştirme geçerli yöntemi, çalışma alanınızdaki performans sayaçlarını kullanır. Yeni yöntem bu verileri `InsightsMetrics` adlı yeni bir tabloda depolar.

Kullanıcı arabirimimizi ınsightsölçümler içindeki verileri kullanacak şekilde güncelleştirdikten sonra belgelerimizi güncelleştireceğiz ve bu duyuruyu, Azure portal bir başlık görüntüleme dahil olmak üzere birden çok kanal aracılığıyla ileteceğiz. Bu noktada, artık kullanmak istemiyorsanız, çalışma alanınızda bu [performans sayaçlarını](vminsights-enable-overview.md#performance-counters-enabled) devre dışı bırakmayı seçebilirsiniz. 

[!NOTE]
>Performans tablosunda bu sayaçlara başvuran uyarı kurallarınız varsa, `InsightsMetrics` tablosundaki yeni verilere başvuracak şekilde onları güncelleştirmeniz gerekir.  Bu tabloya başvuran, kullanabileceğiniz günlük sorguları için belgelerimize bakın.

Performans sayaçlarını etkin tutmaya karar verirseniz, toplanan veriler için faturalandırılır ve bu, [Log Analytics fiyatlandırması [(https://azure.microsoft.com/pricing/details/monitor/) temelinde performans tablosuna saklanır.

## <a name="how-will-this-change-affect-my-alert-rules"></a>Bu değişiklik, uyarı kurallarımı nasıl etkiler?

Çalışma alanında etkinleştirilmiş olan `Perf` tablo hedefleme performans sayaçlarını sorgulayan [günlük uyarıları](../platform/alerts-unified-log.md) oluşturduysanız bu kuralları, bunun yerine `InsightsMetrics` tablosuna başvuracak şekilde güncelleştirmeniz gerekir. Bu kılavuz Ayrıca, `ServiceMapComputer_CL` ve `ServiceMapProcess_CL` kullanan tüm günlük arama kuralları için de geçerlidir çünkü bu veri kümeleri `VMComputer` ve `VMProcess` tablolarına taşınır.

Bu SSS 'yi ve belgelerimizi topladığımız veri kümelerine yönelik örnek günlük arama uyarısı kuralları içerecek şekilde güncelleştireceğiz.

## <a name="will-there-be-any-changes-to-billing"></a>Faturamda herhangi bir değişiklik olacak mı?

Faturalandırma, Log Analytics çalışma alanınızda alınan ve saklanan verileri temel alır.

Topladığımız makine düzeyi performans verileri aynı, performans tablosunda depolandığımız verilere benzer bir boyutludır ve yaklaşık olarak aynı miktarda maliyetlidir.

## <a name="what-if-i-only-want-to-use-service-map"></a>Yalnızca Hizmet Eşlemesi kullanmak istersem ne yapmalıyım?

Bu çok uygundur.  Yaklaşan güncelleştirme hakkında VM'ler için Azure İzleyici görüntülerken Azure portal istemler görürsünüz. Yayımlandıktan sonra, yeni sürüme güncelleştirmenizi isteyen bir istem alırsınız. Yalnızca [haritalar](vminsights-maps.md) özelliğini kullanmayı tercih ediyorsanız, yükseltmemeyi seçebilir ve haritalar özelliğini VM'ler için Azure izleyici ve çalışma alanınızdan ya da Pano kutucuğundan erişilen hizmet eşlemesi çözümüne kullanmaya devam edebilirsiniz.

Çalışma alanınızdaki performans sayaçlarını el ile etkinleştirmeyi seçerseniz, bazı performans grafiklerimizden Azure Izleyici 'den görüntülenen verileri görebilirsiniz. Yeni çözüm yayımlandıktan sonra, `InsightsMetrics` tablosunda depolanan verileri sorgulamak için performans grafiklerimizi güncelleştireceğiz. Bu grafiklerde bu tablodaki verileri görmek isterseniz, VM'ler için Azure İzleyici yeni sürümüne yükseltmeniz gerekir.

@No__t_0 ve `ServiceMapProcess_CL` verileri taşıma değişiklikleri hem Hizmet Eşlemesi hem de VM'ler için Azure İzleyici etkiler, bu nedenle yine de bu güncelleştirme için plan yapmanız gerekir.

**Vminsıghts** çözümüne yükseltmemeyi seçerseniz, `Perf` tablosundaki verilere başvuran performans çalışma kitaplarımızın eski sürümlerini sağlamaya devam edeceğiz.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Hizmet Eşlemesi veri kümeleri de ınsightsölçümler ' de depolanacak mi?

Her iki çözüm de kullanırsanız veri kümeleri yinelenmez. Her iki teklif de `VMComputer` (eski adıyla ServiceMapComputer_CL), `VMProcess` (eski adıyla ServiceMapProcess_CL), `VMConnection` ve `VMBoundPort` tablolarında depolanacak veri kümelerini paylaşır ve bu da topladığımız harita veri kümelerini depolar.  

@No__t_0 tablosu, topladığımız VM, işlem ve hizmet veri kümelerini depolamak için ve yalnızca VM'ler için Azure İzleyici kullanıyorsanız doldurulacak şekilde kullanılacaktır.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Çalışma alanım 'da Hizmet Eşlemesi ve Vminsıghts çözümlerini varsa, Double olarak ücretlendirilecektir mıyım?

Hayır, iki çözüm, `VMComputer` (eski adı ServiceMapComputer_CL), `VMProcess` (eski adıyla ServiceMapProcess_CL), `VMConnection` ve `VMBoundPort` depodığımız harita veri kümelerini paylaşır.  Çalışma alanınızda her iki çözüm de varsa bu veriler için iki ücret ödemeniz gerekmez.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Hizmet Eşlemesi veya Vminsıghts çözümünü kaldırdığımda, verilerimi Log Analytics kaldıracağım mi?

Hayır, iki çözüm, `VMComputer` (eski adı ServiceMapComputer_CL), `VMProcess` (eski adıyla ServiceMapProcess_CL), `VMConnection` ve `VMBoundPort` depodığımız harita veri kümelerini paylaşır.  Çözümlerden birini kaldırırsanız, bu veri kümeleri, verileri kullanan bir çözüm olduğunu ve Log Analytics devam ettiğini fark eder.  Log Analytics çalışma alanınızdan kaldırılacak verilerin çalışma alanınızdan her iki çözümü de kaldırmanız gerekir.

## <a name="when-will-this-update-be-released"></a>Bu güncelleştirme ne zaman yayımlanacak?

VM'ler için Azure İzleyici için güncelleştirmeyi PARÇAAL-Kasım sürümünde yayınlıyoruz. Yayın tarihine yaklaşdığımızda, güncelleştirmeleri buraya göndereceğiz ve Azure Izleyici 'ye gittiğinizde Azure portal bildirimler sunacağız.

## <a name="health-feature-to-enter-limited-public-preview"></a>Sınırlı genel önizlemeye girmek için sistem durumu özelliği

VM sağlık özelliği kümesiyle ilgili olarak müşterilerden çok sayıda geri bildirim aldık.  Bu özelliğin bir çok ilgisi vardır ve izleme iş akışlarını destekleme potansiyelini aşmakatın. İşlevselliği eklemek ve aldığımız geri bildirimi gidermek için bir dizi değişiklik yapmayı planlıyoruz. Bu değişikliklerin yeni müşterilere etkilerini en aza indirmek için bu özelliği sınırlı bir genel önizlemeye taşıdık.

Bu geçiş, ilk Ekim ayında başlayacak ve ayın sonuna kadar tamamlanmalıdır.

Odaklandığımız alanlardan bazıları:

- Sistem durumu modeli ve eşiklerinin üzerinde daha fazla denetim
- VM 'lerin kapsamına uygulanan ölçekli sistem durumu modellerinde yazma
- Durum tabanlı uyarı kurallarını yönetmek için uyarılar platformunun yerel kullanımı
- Bir veya daha fazla abonelik gibi daha geniş bir kapsamda sistem durumunu görme yeteneği
- Durum geçişleri ve uyarı bildirimlerinde azaltılan gecikme süresi

## <a name="how-do-existing-customers-access-the-health-feature"></a>Mevcut müşteriler sistem durumu özelliğine nasıl erişir?

Sistem durumu özelliğini kullanan mevcut müşteriler buna erişime sahip olmaya devam eder, ancak yeni müşterilere sunulmayacaktır.  

Bu özelliğe erişmek için, [https://portal.azure.com](https://portal.azure.com)Portal URL 'sine `feature.vmhealth=true` özellik bayrağını ekleyebilirsiniz. Örnek `https://portal.azure.com/?feature.vmhealth=true`.

Ayrıca, özellik bayrağını otomatik olarak ayarlayan bu kısa URL 'yi de kullanabilirsiniz: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Mevcut bir müşteri olarak, sistem sağlığı özelliğini mevcut bir çalışma alanı kurulumuna bağlı olan VM 'lerde sistem durumu işleviyle kullanmaya devam edebilirsiniz.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>VM sistem durumunu şimdi bir ortamla kullanıyorum ve yeni bir ortam için dağıtmak istiyorum

Sistem durumu özelliğini kullanan mevcut bir müşterisiyseniz ve yeni bir dağıtım için kullanmak istiyorsanız, yönergeler istemek için lütfen vminsights@microsoft.com ' dan bizimle iletişime geçin.

## <a name="next-steps"></a>Sonraki adımlar

Sanal makinelerinizi izlemenize yardımcı olan gereksinimleri ve yöntemleri anlamak için [VM'ler için Azure izleyici dağıt](vminsights-enable-overview.md)' ı inceleyin.
