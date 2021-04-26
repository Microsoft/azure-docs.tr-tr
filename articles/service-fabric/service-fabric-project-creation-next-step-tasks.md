---
title: Proje oluşturma sonraki adımları Service Fabric
description: Visual Studio 'da yeni oluşturduğunuz uygulama projesi hakkında bilgi edinin.  Öğreticileri kullanarak hizmetler oluşturmayı ve Service Fabric için hizmet geliştirme hakkında daha fazla bilgi edinin.
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4d162918644727d4c79ad606f1ed34816f543d81
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044388"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Service Fabric uygulamanız ve sonraki adımlar
Azure Service Fabric uygulamanız oluşturuldu. Bu makalede, bir dizi kaynak, ilgilendiğiniz daha fazla bilgi ve olası [sonraki adımlar](#next-steps)yer alır.

Yeni kullanıcılar [öğreticiler, izlenecek yollar ve örneklere](#get-started-with-tutorials-walk-throughs-and-samples) yardımcı olabilir. [Oluşturulan uygulama projesinin yapısını](#the-application-project)incelemek de faydalı olabilir. Ayrıca, Service Fabric [programlama modelleri](#learn-more-about-the-programming-models), [hizmet iletişimi](#learn-about-service-communication), [uygulama güvenliği](#learn-about-configuring-application-security)ve [uygulama yaşam döngüsünün](#learn-about-the-application-lifecycle)açıklamalarıdır.

Daha deneyimli kullanıcılar Service Fabric en [iyi uygulamalar](#learn-about-best-practices) bölümünü, en yüksek göre etkinliğine sahip platform ve yapı uygulamalarından nasıl yararlanalabileceğinizi öğrenmek için yararlı olabilir.

Sorularınız veya geri bildirimde bulunmak ya da bir sorunu raporlamak isteyen kişiler için [ilgili bölüme](#have-questions-or-feedback--need-to-report-an-issue)bakın.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Öğreticiler, yürüme-kılavuzlarına ve örnekler ile çalışmaya başlama
Başlamaya hazır mısınız?  

.NET uygulama öğreticisi aracılığıyla çalışın. Bir ASP.NET Core ön uç ve durum bilgisi içeren arka uç ile uygulama [oluşturmayı](service-fabric-tutorial-create-dotnet-app.md) , [uygulamayı](service-fabric-tutorial-deploy-app-to-party-cluster.md) BIR kümeye DAĞıTMAYı, [CI/CD 'yi yapılandırmayı](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)ve [izleme ve tanılamayı ayarlamayı](service-fabric-tutorial-monitoring-aspnet.md)öğrenin.

Ya da aşağıdaki ilerm-kılavuzlarına birini deneyin ve ilk kez oluşturun...
- [Windows üzerinde C# Reliable Services hizmeti](service-fabric-reliable-services-quick-start.md) 
- [Windows üzerinde C# Reliable Actors hizmeti](service-fabric-reliable-actors-get-started.md) 
- [Windows üzerinde Konuk yürütülebilir hizmeti](quickstart-guest-app.md) 
- [Windows kapsayıcı uygulaması](service-fabric-get-started-containers.md) 

Ayrıca, [örnek uygulamalarımızı](/samples/browse/?products=azure)de denemeyebilirsiniz.

## <a name="the-application-project"></a>Uygulama Projesi
Her yeni uygulama bir uygulama projesi içerir. Seçilen hizmet türüne bağlı olarak bir veya iki ek proje olabilir.

Uygulama Projesi şunlardan oluşur:

* Uygulamanızı oluşturan hizmetlere yönelik bir dizi başvuru.
* Bir küme uç noktasıyla ilgili Tercihler ve varsayılan olarak yükseltme dağıtımları yapıp gerçekleştirmeyeceğinizi gibi farklı ortamlarla çalışma tercihlerini sürdürmek için kullanabileceğiniz üç yayımlama profili (1 düğümlü yerel, 5 düğümlü yerel ve bulut).
* Bir hizmet için oluşturulacak bölüm sayısı gibi, ortama özgü uygulama yapılandırmalarının bakımını yapmak için kullanabileceğiniz üç uygulama parametresi dosyası (yukarıdaki ile aynı). [Uygulamanızı birden çok ortam için nasıl yapılandıracağınızı](service-fabric-manage-multiple-environment-app-configuration.md)öğrenin.
* Uygulamanızı komut satırından veya otomatik bir sürekli tümleştirme ve dağıtım işlem hattının parçası olarak dağıtmak için kullanabileceğiniz bir dağıtım betiği. [PowerShell kullanarak uygulama dağıtma](service-fabric-deploy-remove-applications.md)hakkında daha fazla bilgi edinin.
* Uygulamayı açıklayan uygulama bildirimi. Bildirimi ApplicationPackageRoot klasörü altında bulabilirsiniz. [Uygulama ve hizmet bildirimleri](service-fabric-application-model.md)hakkında daha fazla bilgi edinin.

## <a name="learn-more-about-the-programming-models"></a>Programlama modelleri hakkında daha fazla bilgi edinin
Service Fabric hizmetlerinizi yazmak ve yönetmek için birçok yol sunar.  Burada [durum bilgisiz ve durum bilgisi olmayan Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [kapsayıcılar](service-fabric-containers-overview.md), [Konuk yürütülebilir dosyalar](service-fabric-guest-executables-introduction.md)ve durum [bilgisiz ve durum bilgisi olan ASP.NET Core hizmetleriyle](service-fabric-reliable-services-communication-aspnetcore.md)ilgili genel bilgiler ve

## <a name="learn-about-service-communication"></a>Hizmet iletişimi hakkında bilgi edinin
Service Fabric bir uygulama, her hizmetin özelleşmiş bir görev yaptığı farklı hizmetlerden oluşur. Bu hizmetler birbirleriyle iletişim kurabilir ve hizmetlerle bağlantı kuran ve hizmetlere iletişim kuran küme dışında istemci uygulamaları olabilir. Service Fabric [Hizmetleri ile ve arasında iletişim](service-fabric-connect-and-communicate-with-services.md) kurma hakkında bilgi edinin. 

## <a name="learn-about-configuring-application-security"></a>Uygulama güvenliğini yapılandırma hakkında bilgi edinin
Kümede çalışan uygulamaların farklı Kullanıcı hesapları altında güvenli hale getirebilirsiniz. Service Fabric Ayrıca, uygulamalar tarafından Kullanıcı hesapları (örneğin, dosyalar, dizinler ve sertifikalar) altında dağıtım sırasında kullanılan kaynakların korunmasına yardımcı olur. Bu, paylaşılan barındırılan bir ortamda bile çalışan uygulamaları diğerinden daha güvenli hale getirir.  [Uygulamanız için güvenlik ilkelerini yapılandırma](service-fabric-application-runas-security.md)hakkında bilgi edinin.

Uygulamanız, depolama bağlantı dizeleri, parolalar veya düz metin olarak işlenmemelidir diğer değerler gibi hassas bilgiler içerebilir. [Uygulamanızdaki gizli dizileri yönetmeyi](service-fabric-application-secret-management.md)öğrenin.

## <a name="learn-about-the-application-lifecycle"></a>Uygulama yaşam döngüsü hakkında bilgi edinin
Diğer platformlarda olduğu gibi, bir Service Fabric uygulama genellikle şu aşamalardan geçer: tasarım, geliştirme, test, dağıtım, yükseltme, bakım ve kaldırma. [Bu makalede](service-fabric-application-lifecycle.md) , API 'lerin yanı sıra Service Fabric uygulama yaşam döngüsünün aşamaları boyunca farklı roller tarafından nasıl kullanıldıkları hakkında genel bakış sunulmaktadır.

## <a name="learn-about-best-practices"></a>En iyi yöntemler hakkında bilgi edinin
Service Fabric [en iyi yöntemleri](./service-fabric-best-practices-security.md)açıklayan birçok makaleye sahiptir. Kümenizin ve uygulamanızın çalışmasını ve mümkün olduğunca emin olmak için bu bilgilerden yararlanın.
Kapsanan konular şunları içerir:
* [Güvenlik](./service-fabric-best-practices-security.md)
* [Ağ](./service-fabric-best-practices-networking.md)
* [İşlem planlama ve ölçeklendirme](./service-fabric-best-practices-capacity-scaling.md)
* [Kod olarak altyapı](./service-fabric-best-practices-infrastructure-as-code.md)
* [İzleme ve tanılama](./service-fabric-best-practices-monitoring.md)
* [Uygulama tasarımı](./service-fabric-best-practices-applications.md)

Ayrıca, en iyi yöntem bilgilerini kullanımı kolay bir biçimde tümleştiren bir [Üretim hazırlığı denetim listesi](./service-fabric-production-readiness-checklist.md) de mevcuttur.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Sorularınız veya geri bildiriminiz mi var?  Bir sorun bildirmek mı gerekiyor?
[Yaygın soruları](service-fabric-common-questions.md) okuyun ve Service Fabric neler yapabileceğini ve nasıl kullanılacağını öğrenin.

[Sorun giderme kılavuzlarında](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) , Service Fabric kümelerinde yaygın sorunları tanılamaya ve çözmeye yardımcı olmak için yararlı olabilir.

[Destek seçenekleri](service-fabric-support.md) , sorularınızı bildirmek, destek almak ve ürün geri bildirimleri göndermek için STACKOVERFLOW ve MSDN 'deki forumları listeler.


## <a name="next-steps"></a>Sonraki adımlar
- [Azure 'da bir Windows kümesi oluşturun](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)ile dağıtılmış uygulamalar ve fiziksel düzen dahil olmak üzere kümenizi görselleştirin.
- [Hizmetlerinizi sürüm ve yükseltme](service-fabric-application-upgrade-tutorial.md)