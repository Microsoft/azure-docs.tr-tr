---
title: Azure Service Fabric barındırma modeli
description: Dağıtılan bir Service Fabric hizmetinin ve hizmet ana bilgisayar işleminin çoğaltmaları (veya örnekleri) arasındaki ilişkiyi açıklar.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 9932c11332a616928d59c213d4f4806feb81cfe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791654"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric barındırma modeli
Bu makalede, Azure Service Fabric tarafından sağlanan uygulama barındırma modellerine genel bir bakış sağlanır ve **paylaşılan işlem** Ile **özel işlem** modelleri arasındaki farklar açıklanmaktadır. Dağıtılan bir uygulamanın bir Service Fabric düğümüne nasıl göründüğünü ve hizmetin çoğaltmaları (veya örnekleri) ile hizmet ana bilgisayar işleminin arasındaki ilişkiyi açıklar.

Devam etmeden önce, [Service Fabric bir uygulama modelleme][a1]bölümünde açıklanan çeşitli kavramları ve ilişkileri anladığınızdan emin olun. 

> [!NOTE]
> Bu makalede, açıkça başka bir şeyin anlamı olarak belirtilmedikçe:
>
> - *Çoğaltma* , durum bilgisi olan bir hizmetin bir çoğaltmasını ve durum bilgisi olmayan bir hizmetin örneğini ifade eder.
> - *CodePackage* , bir *serviceType* kayıt yapan bir *ServiceHost* Işlemine eşdeğer olarak değerlendirilir ve bu *serviceType* hizmet çoğaltmalarını barındırır.
>

Barındırma modelini anlamak için bir örnek adım adım inceleyelim. ' Myservicetype ' adlı bir *serviceType* ' myapptype ' adlı bir *ApplicationType* olduğunu varsayalım. ' MyServiceType ', ' mycodepackage ' *CodePackage* 'e sahip ' Myservicepackage ' *servicepackage* tarafından sağlanıyor. ' MyCodePackage ', çalışırken ' myservicetype ' adlı *serviceType* kayıt yapar.

Üç düğümlü bir kümeniz olduğunu varsayalım ve ' MyAppType ' türünde bir *Application* **Fabric oluşturacağız:/app1** . Bu Application **Fabric içinde:/app1**, ' myservicetype ' türünde bir Service **Fabric:/app1/servicea** oluşturacağız. Bu hizmette iki bölüm (örneğin, **P1** ve **P2**) ve bölüm başına üç çoğaltma vardır. Aşağıdaki diyagramda, bu uygulamanın bir düğüm üzerinde dağıtılan görünümü gösterilmektedir.


![Bir düğümde dağıtılmış olarak bu uygulamanın görünümünü gösteren diyagram.][node-view-one]


Service Fabric, her iki bölümden de çoğaltmaları barındıran ' MyCodePackage ' öğesini Başlatan ' MyServicePackage ' öğesini etkinleştirdi. Kümedeki düğümlerin sayısına eşit olacak şekilde, bölüm başına çoğaltma sayısını seçtiğimiz için kümedeki tüm düğümler aynı görünüme sahiptir. Application **Fabric:/app1** içinde başka bir hizmet olan **Fabric:/app1/serviceb** oluşturalım. Bu hizmette bir bölüm (örneğin, **P3**) ve bölüm başına üç çoğaltma vardır. Aşağıdaki diyagramda, düğüm üzerindeki yeni görünüm gösterilmektedir:


![Düğüm üzerindeki yeni görünümü gösteren diyagram.][node-view-two]


Service Fabric, mevcut ' MyServicePackage ' etkinleştirmesinde Service **Fabric:/app1/ServiceB** adlı **P3** bölümünün yeni çoğaltmasını yerleştirdi. Sunuldu. ' MyAppType ' türünde başka bir Application Fabric oluşturalım **:/app2** . **Fabric:/app2** içinde bir Service Fabric oluşturun **:/app2/servicea**. Bu hizmette iki bölüm (**P4** ve **P5**) ve bölüm başına üç çoğaltma vardır. Aşağıdaki diyagramda yeni düğüm görünümü gösterilmektedir:


![Yeni düğüm görünümünü gösteren diyagram.][node-view-three]


Service Fabric ' MyCodePackage ' öğesinin yeni bir kopyasını Başlatan ' MyServicePackage ' öğesinin yeni bir kopyasını etkinleştirir. Service **Fabric:/app2/ServiceA** (**P4** ve **P5**) bölümlerinin her iki bölümünde bulunan çoğaltmalar, bu yeni ' mycodepackage ' kopyasına yerleştirilir.

## <a name="shared-process-model"></a>Paylaşılan Işlem modeli
Yukarıdaki bölümde, paylaşılan Işlem modeli olarak adlandırılan Service Fabric tarafından sunulan varsayılan barındırma modeli açıklanmaktadır. Bu modelde, belirli bir uygulama için, belirli bir *Servicepackage* yalnızca bir kopyası bir düğüm üzerinde etkinleştirilir (Bu, içinde bulunan tüm *codepackages* ' i başlatır). Belirli bir *serviceType* ilgili tüm hizmetlerin tüm çoğaltmaları, bu *serviceType* kayıt yapan *CodePackage* 'e yerleştirilir. Diğer bir deyişle, belirli bir *serviceType* bir düğümdeki tüm hizmetlerin tüm çoğaltmaları aynı işlemi paylaşır.

## <a name="exclusive-process-model"></a>Dışlamalı Işlem modeli
Service Fabric tarafından sunulan diğer barındırma modeli, dışlamalı Işlem modelidir. Bu modelde, belirli bir düğümde, her çoğaltma kendi adanmış sürecinde bulunur. Service Fabric, *Servicepackage* 'in yeni bir kopyasını etkinleştirir (Bu, içinde bulunan tüm *codepackages* ' i başlatır). Çoğaltmalar, çoğaltmanın ait olduğu hizmetin *serviceType* kaydı yapılan *CodePackage* 'e yerleştirilir. 

Service Fabric sürüm 5,6 veya sonraki bir sürümü kullanıyorsanız, hizmet oluşturma sırasında özel Işlem modelini seçebilirsiniz ( [PowerShell][p1], [rest][r1]veya [FabricClient][c1]kullanarak). **Servicepackageactivationmode** değerini ' exclusiveprocess ' olarak belirtin.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Uygulama bildiriminizde varsayılan bir hizmetiniz varsa, **Servicepackageactivationmode** özniteliğini belirterek dışlamalı işlem modelini seçebilirsiniz:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Şimdi Application **Fabric:/app1** içinde başka bir hizmet olan **Fabric:/app1/servicec** oluşturalım. Bu hizmette iki bölüm (örneğin, **P6** ve **P7**) ve bölüm başına üç çoğaltma vardır. **Servicepackageactivationmode** ' exclusiveprocess ' olarak ayarlanır. Aşağıdaki diyagramda düğüm üzerinde yeni görünüm gösterilmektedir:


![Dağıtılan uygulamanın düğüm görünümü diyagramı][node-view-four]


Görebileceğiniz gibi, ' MyServicePackage ' öğesinin iki yeni kopyasını ( **P6** ve **P7** bölümünden her çoğaltma için bir tane) etkinleştirdi Service Fabric. Service Fabric her çoğaltmayı *CodePackage*'in adanmış kopyasına yerleştirdi. Özel Işlem modelini kullandığınızda, belirli bir uygulama için belirli bir *hizmet paketinin* birden fazla kopyası bir düğüm üzerinde etkin olabilir. Yukarıdaki örnekte, **Fabric:/app1** için üç ' MyServicePackage ' kopyası etkindir. ' MyServicePackage ' öğesinin bu etkin kopyalarının her birinin kendisiyle ilişkili bir **Servicepackageactivationıd** 'si vardır. Bu KIMLIK, uygulama yapısı içinde bu kopyayı tanımlar **:/app1**.

Bir uygulama için yalnızca paylaşılan Işlem modelini kullandığınızda, bir düğümde *Servicepackage* 'in yalnızca bir etkin kopyası vardır. *Servicepackage* 'in bu etkinleştirmesi Için **Servicepackageactivationıd** boş bir dize. Bu durum örneğin, **doku:/app2**.

> [!NOTE]
>- Paylaşılan Işlem barındırma modeli **Servicepackageactivationmode** 'A eşittir **sharedprocess** öğesine karşılık gelir. Bu, varsayılan barındırma modelidir ve hizmet oluşturma sırasında **Servicepackageactivationmode** belirtilmelidir.
>
>- Özel Işlem barındırma modeli **Servicepackageactivationmode** 'A eşittir **exclusiveprocess** öğesine karşılık gelir. Bu ayarı kullanmak için, hizmeti oluşturma sırasında açıkça belirtmeniz gerekir. 
>
>- Bir hizmetin barındırma modelini görüntülemek için, [hizmet açıklamasını][p2]sorgulayın ve **Servicepackageactivationmode** değerine bakın.
>
>

## <a name="work-with-a-deployed-service-package"></a>Dağıtılan bir hizmet paketiyle çalışma
Bir düğümdeki *servicepackage* etkin bir kopyası [dağıtılan hizmet paketi][p3]olarak adlandırılır. Belirli bir uygulama için hizmetler oluşturmak üzere özel Işlem modelini kullandığınızda, aynı *Servicepackage* için birden çok dağıtılan hizmet paketi olabilir. Dağıtılan bir hizmet paketine özgü işlemler gerçekleştiriyorsanız, dağıtılan belirli bir hizmet paketini belirlemek için **Servicepackageactivationıd** sağlamalısınız. Örneğin, [dağıtılan bir hizmet paketinin sistem durumunu bildir,][p4] veya [dağıtılan bir hizmet paketinin kod paketini YENIDEN başlattıktan][p5]sonra kimliği belirtin.

Bir düğümdeki [dağıtılan hizmet paketlerinin][p3] listesini sorgulayarak, dağıtılan bir hizmet paketinin **Servicepackageactivationıd** 'sini bulabilirsiniz. [Dağıtılmış hizmet türlerini][p6], [dağıtılan çoğaltmaları][p7]ve bir düğümde [dağıtılan kod paketlerini][p8] sorgularken, sorgu sonucu, üst dağıtılan hizmet paketinin **servicepackageactivationıd** 'sini de içerir.

> [!NOTE]
>- Belirli bir uygulama için, belirli bir düğümdeki paylaşılan Işlem barındırma modeli altında, bir *Servicepackage* yalnızca bir kopyası etkinleştirilir. *Boş dizeye* eşit bir **Servicepackageactivationıd** değerine sahiptir ve dağıtılan hizmet paketiyle ilgili işlemler gerçekleştirilirken belirtilmemelidir. 
>
> - Belirli bir uygulama için, belirli bir düğümdeki özel Işlem barındırma modeli altında, bir *hizmet paketinin* bir veya daha fazla kopyası etkin olabilir. Her etkinleştirmenin, dağıtılan hizmet paketiyle ilgili işlemler gerçekleştirilirken *boş olmayan* bir **servicepackageactivationıd 'si** vardır. 
>
> - **Servicepackageactivationıd** atlandığında, varsayılan olarak *boş dize* olur. Paylaşılan Işlem modeli altında etkinleştirilmiş dağıtılan bir hizmet paketi varsa, işlem bunun üzerinde gerçekleştirilir. Aksi takdirde işlem başarısız olur.
>
> - Bir kez sorgulama ve **Servicepackageactivationıd**'yi önbelleğe alma. KIMLIK dinamik olarak oluşturulur ve çeşitli nedenlerle değişebilir. **Servicepackageactivationıd** gerektiren bir işlem gerçekleştirmeden önce, önce bir düğümdeki [dağıtılan hizmet paketlerinin][p3] listesini sorgulayın. Ardından, özgün işlemi gerçekleştirmek için sorgu sonucundan **Servicepackageactivationıd** kullanın.
>
>

## <a name="guest-executable-and-container-applications"></a>Konuk yürütülebilir ve kapsayıcı uygulamaları
Service Fabric, [Konuk yürütülebilir][a2] ve [kapsayıcı][a3] uygulamaları, kendi içinde olan durum bilgisi olmayan hizmetler olarak değerlendirir. *ServiceHost* (bir işlem veya kapsayıcı) içinde Service Fabric çalışma zamanı yok. Bu hizmetler kendine dahil olduğundan, *ServiceHost* başına çoğaltma sayısı bu hizmetler için geçerli değildir. Bu hizmetlerle kullanılan en yaygın yapılandırma, [InstanceCount][c2] -1 ' e eşit (kümenin her bir düğümünde çalışan hizmet kodunun bir kopyası) tek bölümlemedir. 

Bu hizmetler için varsayılan **Servicepackageactivationmode** değeri **sharedprocess**, bu durumda Service Fabric yalnızca belirli bir uygulama Için bir düğümde *servicepackage* 'in bir kopyasını etkinleştirir.  Bu, bir hizmet kodu kopyasının yalnızca bir düğümü çalıştıracağı anlamına gelir. Bir düğümde hizmet kodunuzun birden çok kopyasının çalıştırılmasını istiyorsanız hizmeti oluştururken **Exclusiveprocess** olarak **Servicepackageactivationmode** değerini belirtin. Örneğin, bu işlemi, *serviceType* ( *servicemanifest* içinde belirtilen) birden çok hizmet (*Service1* için *servicen*) oluşturduğunuzda ya da hizmetiniz çok bölümlendiğinde yapabilirsiniz. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Mevcut bir hizmetin barındırma modelini değiştirme
Şimdiki zamanda, var olan bir hizmetin barındırma modelini, paylaşılan Işlemden özel Işlem (veya tam tersi) olarak değiştiremezsiniz.

## <a name="choose-between-the-hosting-models"></a>Barındırma modelleri arasında seçim yapma
Hangi barındırma modelinin gereksinimlerinize en iyi şekilde uyduğunu değerlendirmeniz gerekir. Paylaşılan Işlem modeli, daha az işlem yapıldığından ve aynı işlemde birden fazla çoğaltma bağlantı noktasını paylaşabileceğinden, işletim sistemi kaynaklarını daha iyi kullanır. Ancak, çoğaltmalardan birinde hizmet ana bilgisayarını getirmek için gereken bir hata varsa, tüm diğer çoğaltmaları aynı işlemle etkiler.

 Dışlamalı Işlem modeli, her çoğaltma kendi sürecinde daha iyi yalıtım sağlar. Çoğaltmalardan birinde hata varsa, diğer çoğaltmaları etkilemez. Bu model, bağlantı noktası paylaşımının iletişim protokolü tarafından desteklenmediği durumlarda faydalıdır. Çoğaltma düzeyinde kaynak yönetimini uygulama olanağını kolaylaştırır. Ancak, özel Işlem, düğümdeki her bir çoğaltma için bir işlem oluşturduğu için daha fazla işletim sistemi kaynağı tüketir.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Dışlamalı Işlem modeli ve uygulama modeli konuları
Çoğu uygulama için, *Servicepackage* başına bir *ServiceType* tutarak uygulamanızı Service Fabric modelleyebilirsiniz. 

Service Fabric, bazı durumlarda, *servicepackage* başına birden fazla *serviceType* da izin verir (ve bir *CodePackage* birden fazla *serviceType* kaydedebilir). Bu yapılandırmaların yararlı olabilecek bazı senaryolar aşağıda verilmiştir:

- Daha az işlem oluşturarak ve süreç başına daha yüksek çoğaltma yoğunluğu bulundurarak kaynak kullanımını iyileştirmek istiyorsunuz.
- Farklı *serviceType* çoğaltmalarının çoğaltmaları, yüksek bir başlatma veya bellek maliyeti olan bazı yaygın verileri paylaşmalıdır.
- Ücretsiz bir hizmet sunumuna sahipsiniz ve hizmetin tüm çoğaltmalarını aynı işleme koyarak kaynak kullanımına bir sınır koymak istiyorsunuz.

Özel Işlem barındırma modeli, *Servicepackage* başına birden fazla *serviceType* içeren bir uygulama modeliyle tutarlı değildir. Bunun nedeni, *Servicepackage* başına birden fazla *serviceType* , çoğaltmalar arasında daha yüksek kaynak paylaşımına ulaşmak üzere tasarlanmasıdır ve işlem başına daha yüksek çoğaltma yoğunluğu sağlar. Dışlamalı Işlem modeli farklı sonuçlar elde etmek için tasarlanmıştır.

Her bir *serviceType* kayıt yaptırın farklı bir *CodePackage* ile, *Servicepackage* başına birden fazla *serviceType* durumunu göz önünde bulundurun. İki *CodePackage*'e sahip olan bir *servicepackage* ' multitypeservicepackage ' olduğunu varsayalım:

- ' MyCodePackageA ', *serviceType* ' myservicetypea ' öğesini kaydeder.
- ' MyCodePackageB ', *serviceType* ' myservicetypeb ' öğesini kaydeder.

Şimdi, bir uygulama oluşturduğumuz, **Fabric:/SpecialApp** olduğunu varsayalım. **Fabric:/SpecialApp** Içinde, dışlamalı işlem modeliyle aşağıdaki iki hizmeti oluşturacağız:

- Service **Fabric:/SpecialApp/ServiceA** ' MyServiceTypeA ' türünde iki bölüm (örneğin, **P1** ve **P2**) ve bölüm başına üç çoğaltma.
- Service **Fabric:/SpecialApp/ServiceB** türünde iki bölüm (**P3** ve **P4**) ve bölüm başına üç çoğaltma.

Belirli bir düğümde, her iki hizmet de iki çoğaltma vardır. Hizmetleri oluşturmak için özel Işlem modelini kullandığımızda, Service Fabric her çoğaltma için yeni bir ' MyServicePackage ' kopyası etkinleştirir. ' MultiTypeServicePackage ' öğesinin her etkinleştirilmesi ' MyCodePackageA ' ve ' MyCodePackageB ' öğesinin bir kopyasını başlatır. Ancak, yalnızca bir ' MyCodePackageA ' veya ' MyCodePackageB ', ' MultiTypeServicePackage ' öğesinin etkinleştirildiği çoğaltmayı barındırır. Aşağıdaki diyagramda düğüm görünümü gösterilmektedir:


![Düğüm görünümünü gösteren diyagram.][node-view-five]


Service **Fabric:/SpecialApp/ServiceA**' ın bölüm **P1** kopyası Için ' multitypeservicepackage ' ' in etkinleştirilmesinde, ' mycodepackagea ', çoğaltmayı barındırıyor. ' MyCodePackageB ' çalışıyor. Benzer şekilde, Service **Fabric:/SpecialApp/serviceb**, ' Mycodepackageb ' **P3** bölümünün çoğaltması Için ' multitypeservicepackage ' etkinleştirmesi, çoğaltmayı barındırıyor. ' MyCodePackageA ' çalışıyor. Bu nedenle, *Servicepackage* başına *codepackages* (farklı *serviceType* kaydetme) sayısı arttıkça, yedekli kaynak kullanımı artar. 
 
 Ancak, Service **Fabric:/SpecialApp/ServiceA** ve **Fabric:/SpecialApp/serviceb** ' yi paylaşılan işlem modeliyle oluşturuyoruz, Service Fabric uygulama dokusu Için yalnızca bir ' multitypeservicepackage ' kopyası etkinleştirir **:/SpecialApp**. ' MyCodePackageA ', Service Fabric için tüm çoğaltmaları barındırır **:/SpecialApp/ServiceA**. ' MyCodePackageB ', Service Fabric için tüm çoğaltmaları barındırır **:/SpecialApp/ServiceB**. Aşağıdaki diyagramda düğüm görünümü bu ayarda gösterilmektedir: 


![Dağıtılan uygulamanın düğüm görünümü diyagramı][node-view-six]


Yukarıdaki örnekte, ' MyCodePackageA ' ' MyServiceTypeA ' ve ' MyServiceTypeB ' öğelerinin her ikisini de kaydeder ve ' MyCodePackageB ' yoksa, çalışan gereksiz *CodePackage* yok demektir. Bu doğru olsa da, bu uygulama modeli dışlamalı Işlem barındırma modeliyle hizalanmaz. Hedef her bir yinelemeyi kendi adanmış işleminde koymak istiyorsanız, her iki *serviceType* de aynı *CodePackage* öğesinden kaydetmeniz gerekmez. Bunun yerine, her bir *serviceType* kendi *servicepackage* içine yerleştirmeniz yeterlidir.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services ve aktör alt süreçler

Service Fabric güvenilir Hizmetleri ve daha sonra güvenilir aktörlere alt süreçler desteklemez. Bunun neden desteklenmeyen bir örnek [Codepackageactivationcontext](/dotnet/api/system.fabric.codepackageactivationcontext) , desteklenmeyen bir alt işlemi kaydetmek için kullanılamaz ve iptal belirteçleri yalnızca kayıtlı işlemlere gönderilir; üst işlem bir iptal belirteci aldıktan sonra alt işlemler kapanmadığında, yükseltme hataları gibi her türlü soruna neden olur.

## <a name="next-steps"></a>Sonraki adımlar
[Bir uygulamayı paketleyin][a4] ve dağıtıma başlamaya hazırlanın.

[Uygulamaları dağıtın ve kaldırın][a5]. Bu makalede, uygulama örneklerini yönetmek için PowerShell 'in nasıl kullanılacağı açıklanır.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
