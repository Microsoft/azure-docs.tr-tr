---
title: Azure Service Fabric ile birim testi durum bilgisi olan hizmetler | Microsoft Docs
description: Durum bilgisi olan hizmetlerin Service Fabric birim testi kavramları ve yöntemleri hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: 012d75ff6ad4acdc6612a197f274e2dfdb98370a
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249262"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Service Fabric 'de birim testi durum bilgisi olan hizmetler

Bu makalede, durum bilgisi olan hizmetlerin Service Fabric birim testi kavramlarını ve yöntemleri ele alınmaktadır. Service Fabric içinde birim testi, uygulama kodunun etkin bir şekilde birden çok farklı bağlamda çalışması nedeniyle kendi önemli noktaları sunar. Bu makalede, uygulama kodunun çalıştıraşabolduğu her farklı bağlamda kapsamında olmasını sağlamak için kullanılan uygulamalar açıklanmaktadır.

## <a name="unit-testing-and-mocking"></a>Birim testi ve sahte işlem
Bu makalenin bağlamındaki birim testi, MSTest veya NUnit gibi bir Test Çalıştırıcısı bağlamı içinde yürütülebilecek otomatikleştirilmiş sınamadır. Bu makalede yer alan birim testleri, bir veritabanı veya yeniden bir API gibi uzak bir kaynağa karşı işlem gerçekleştirmez. Bu uzak kaynaklar moclanmış olmalıdır. Bu makalenin bağlamında, Uzak kaynaklar için dönüş değerlerini taklit eder, kaydeder ve denetler.

### <a name="service-fabric-considerations"></a>Service Fabric konuları
Service Fabric durum bilgisi olan bir hizmetin birim testi çeşitli hususlar içerir. İlk olarak, hizmet kodu birden çok düğümde ve farklı roller altında yürütülür. Birim testleri, tüm kapsama ulaşmak için her rolün altındaki kodu değerlendirmelidir. Farklı roller birincil, etkin Ikincil, boşta Ikincil ve bilinmiyor olur. None rolü genellikle bu rolün void veya null hizmet olarak nitelemediği için hiçbir özel kapsama ihtiyaç duyar Service Fabric. İkinci olarak, her düğüm verilen herhangi bir noktada rolünü değiştirecek. Tüm kapsama ulaşmak için, kod yürütme yolunun, gerçekleşen rol değişiklikleriyle test edilmelidir.

## <a name="why-unit-test-stateful-services"></a>Birim test durum bilgisi neden Hizmetleri? 
Birim testi durum bilgisi olan hizmetler, geleneksel uygulama veya etki alanına özgü birim testi tarafından yakalanmak zorunda olmaması gereken bazı yaygın hataları açmaya yardımcı olabilir. Örneğin, durum bilgisi olan hizmette herhangi bir bellek içi durum varsa, bu tür bir test, bu bellek içi durumun her yinelemede eşitlenmiş olduğunu doğrulayabilirler. Bu tür bir test, durum bilgisi olan bir hizmetin Service Fabric düzenleme tarafından uygun bir şekilde geçirilen iptal belirteçlerini yanıt verdiğini de doğrulayabilirsiniz. İptaller tetiklendiğinde hizmet, uzun süre çalışan ve/veya zaman uyumsuz işlemleri durdurur.  

## <a name="common-practices"></a>Ortak uygulamalar

Aşağıdaki bölümde, durum bilgisi olan bir hizmet olan birim testi için en sık kullanılan uygulamalar üzerinde yer verilmiştir. Ayrıca, bir sahte işlem katmanının Service Fabric düzenleme ve durum yönetimine yakından uyum sağlamak için sahip olması gerektiğini de önerir. [Servicefabric.](https://www.nuget.org/packages/ServiceFabric.Mocks/) moizler, 3.3.0 veya üzeri olarak, önerilen ve aşağıdaki ana işlevleri içeren bir kitaplıktır.

### <a name="arrangement"></a>Sidir

#### <a name="use-multiple-service-instances"></a>Birden çok hizmet örneği kullanma
Birim testleri, durum bilgisi olan bir hizmetin birden çok örneğini yürütmelidir. Bu, Service Fabric kümede gerçekten ne olduğunu taklit eder; burada, farklı düğümlerde hizmetinizi çalıştıran birden çok çoğaltma sağlar. Bu örneklerin her biri, ancak farklı bir bağlam altında yürütülecektir. Testi çalıştırırken, her örnek kümede beklenen rol yapılandırması ile aynı olmalıdır. Örneğin, hizmetin hedef çoğaltma boyutu 3 ' ün olması bekleniyorsa, Service Fabric farklı düğümlerde üç çoğaltma sağlar. Bunlardan biri birincil ve diğer iki, etkin Ikincil.

Çoğu durumda, hizmet yürütme yolu bu rollerin her biri için biraz farklılık gösterecektir. Örneğin, hizmet etkin bir Ikincil bilgisayardan gelen istekleri kabul etmemelidir, ikincil üzerinde bir isteğin denendiğini belirten bilgilendirici bir özel durum oluşturması için hizmette bu durum için bir denetim olabilir. Birden çok örneğe sahip olmak, bu durumun sınanması için izin verir.

Ayrıca, birden çok örneğe sahip olmak, testlerin bu örneklerin her birinin rollerini değiştirmesine izin verir ve bu da, rolün değişmesinin ne olmasına rağmen yanıtların tutarlı olduğunu doğrular.

#### <a name="mock-the-state-manager"></a>Durum yöneticisini sahte
Durum Yöneticisi uzak bir kaynak olarak kabul edilmelidir ve bu nedenle moclanmış. Durum Yöneticisi 'ni izlerken, okunabilmesi ve doğrulanabilmesi için durum yöneticisine nelerin kaydedildiğini izlemek üzere temeldeki bellek içi depolama alanı olması gerekir. Bunu gerçekleştirmenin basit bir yolu, güvenilir koleksiyonların her türünün sahte örneklerini oluşturmaktır. Bu moizler içinde, bu koleksiyonda gerçekleştirilen işlemlerle yakından hizalanan bir veri türü kullanın. Her güvenilir koleksiyon için önerilen bazı veri türleri aşağıda verilmiştir

- Ireliabledictionary < TKey, TValue >-> System. Collections. eşzamanlı. ConcurrentDictionary < TKey, TValue >
- Ireliablequeue\<t >-> System. Collections. Generic. Queue\<T >
- Ireliableconcurrentqueue\<t >-> System. Collections. eşzamanlı. ConcurrentQueue\<t >

#### <a name="many-state-manager-instances-single-storage"></a>Birçok durum Yöneticisi örneği, tek depolama
Daha önce belirtildiği gibi, durum Yöneticisi ve güvenilir koleksiyonlar uzak bir kaynak olarak değerlendirilmelidir. Bu nedenle, bu kaynaklar, ve birim testlerinde mocize edilmelidir. Bununla birlikte, durum bilgisi olan bir hizmetin birden çok örneğini çalıştırırken, her bir çalışma durumu yöneticisinin farklı durum bilgisi olan hizmet örneklerinde eşitlenmiş olması zor olur. Durum bilgisi olan hizmet kümede çalıştığında, Service Fabric her bir ikincil çoğaltmanın durum yöneticisini birincil çoğaltmayla tutarlı tutma işlemini gerçekleştirir. Bu nedenle, testler rol değişikliklerinin benzetimini yapabilmesi için aynı şekilde davranmalıdır.

Bu eşitlemenin kolay bir şekilde elde edilebileceği, her güvenilir koleksiyona yazılan verileri depolayan temel nesne için tek bir model kullanmaktır. Örneğin, durum bilgisi olan bir hizmet kullanıyorsa `IReliableDictionary<string, string>`. Sahte durum Yöneticisi 'nin bir türünü `IReliableDictionary<string, string>`döndürmesi gerekir. Bu sahte, yazılan anahtar `ConcurrentDictionary<string, string>` /değer çiftlerini izlemek için bir kullanabilir. , `ConcurrentDictionary<string, string>` Hizmete geçirilen durum yöneticilerinin tüm örnekleri tarafından bir tek kullanılmalıdır.

#### <a name="keep-track-of-cancellation-tokens"></a>İptal belirteçlerini takip edin
İptal belirteçleri, durum bilgisi olan hizmetlerin önemli, yaygın olarak daha fazla bir yönüdür. Service Fabric, durum bilgisi olan bir hizmet için birincil çoğaltma başladığında bir iptal belirteci sağlanır. Bu iptal belirtecinin, kaldırıldığında hizmete işaret edilmesi veya farklı bir role indirgenmeye yönelik olması amaçlanmıştır. Durum bilgisi olan hizmet, Service Fabric uzun süre çalışan veya zaman uyumsuz işlemleri durdurup, bu sayede rol değişikliği iş akışını tamamlayabilmelidir.

Birim testlerini çalıştırırken, RunAsync, ChangeRoleAsync, OpenAsync ve CloseAsync için belirtilen tüm iptal belirteçleri, test yürütmesi sırasında tutulmalıdır. Bu belirteçlerin tutulması, teste bir hizmetin kapatılmasını veya indirgenmesini sağlar ve hizmetin uygun şekilde yanıt verdiğini doğrular.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Korumalı uzak kaynaklarla uçtan uca test
Birim testleri, durum bilgisi olan hizmetin durumunu mümkün olduğunca değiştirebilen uygulama kodunun çoğunu yürütmelidir. Testlerin doğası gereği daha fazla uçtan uca olması önerilir. Mevcut olan tek hareketler, uzak kaynak etkileşimlerini kaydetmek, benzetimini yapmak ve/veya doğrulamalardır. Bu, durum Yöneticisi ve güvenilir koleksiyonlara sahip etkileşimleri içerir. Aşağıdaki kod parçacığı, uçtan uca testi gösteren bir test için gherkaya bir örnektir:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Bu test, birincil olarak yükseltildiğinde bir çoğaltmada yakalanan verilerin ikincil bir çoğaltma için kullanılabilir olduğunu onaylar. Güvenilir bir koleksiyonun çalışan verileri için yedekleme deposu olduğu varsayıldığında, bu test ile yakalanabilecek bir hata, uygulama kodunun yeni çalışanı kaydetmek için işlem üzerinde yürütülmediği `CommitAsync` kabul edilir. Bu durumda, çalışanları almaya yönelik ikinci istek ilk istek tarafından eklenen çalışanı döndürmez.

### <a name="acting"></a>Makta
#### <a name="mimic-service-fabric-replica-orchestration"></a>Çoğaltma düzenleme Service Fabric benzeme
Birden çok hizmet örneği yönetirken, testlerin bu hizmetleri Service Fabric düzenleme ile aynı şekilde başlatması ve kapatması gerekir. Örneğin, yeni bir birincil çoğaltmada bir hizmet oluşturulduğunda Service Fabric, CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync ve RunAsync komutunu çağıracaktır. Yaşam döngüsü olayları aşağıdaki makalelerde belgelenmiştir:

- [Durum bilgisi olan hizmet başlatma](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Durum bilgisi olan hizmet kapatması](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Durum bilgisi olan hizmet birincil takas](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Çoğaltma rolü değişikliklerini Çalıştır
Birim testleri, hizmet örneklerinin rollerini Service Fabric düzenleme ile aynı şekilde değiştirmeli. Rol durumu makinesi aşağıdaki makalede belgelenmiştir:

[Çoğaltma rolü durum makinesi](service-fabric-concepts-replica-lifecycle.md#replica-role)

Rol değişikliklerinin benzetimini yapmak, testin daha kritik yönlerinden biridir ve çoğaltmanın durumunun birbirleriyle tutarlı olmadığı sorunları ortaya çıkarabilir. Statik veya sınıf düzeyi örnek değişkenlerinde bellek içi durumu depolanmasından dolayı tutarsız çoğaltma durumu oluşabilir. Bunun örnekleri, iptal belirteçleri, numaralandırmalar ve yapılandırma nesneleri/değerleri olabilir. Bu Ayrıca, hizmetin, rol değişikliğinin oluşmasına izin vermek için RunAsync sırasında verilen iptal belirteçlerini de daha da fazla bir şekilde ele aldığından emin olur. Rol değişikliklerinin benzetimini yapmak, bir RunAsync birden çok kez çağrıya izin vermek için kod yazıldıysa oluşabilecek sorunları da açığa çıkarabilir.

#### <a name="cancel-cancellation-tokens"></a>İptal belirteçlerini iptal et
RunAsync için girilen iptal belirtecinin iptal edildiği mevcut birim testleri olmalıdır. Bu, testin hizmetin düzgün bir şekilde kapanıp kapanmadığını doğrulamasına olanak sağlar. Bu kapatma sırasında, uzun süre çalışan veya zaman uyumsuz işlemlerin durdurulması gerekir. Bir hizmette mevcut olabilecek uzun süre çalışan bir işlem örneği, güvenilir bir kuyruktaki iletileri dinleyen bir işlemdir. Bu, doğrudan RunAsync veya bir arka plan iş parçacığı içinde bulunabilir. Bu iptal belirteci iptal edilirse, uygulamanın işlemden çıkmak için mantık içermesi gerekir.

Durum bilgisi olan hizmetler herhangi bir önbelleği veya yalnızca birincil üzerinde bulunması gereken bellek içi durumu kullanıyorsa, şu anda atılmalıdır. Bu, düğüm daha sonra tekrar bir birincil haline gelirse bu durumun tutarlı olmasını sağlamaktır. İptal testi testi, bu durumun düzgün şekilde atılanmasını doğrulamaya izin verir.

#### <a name="execute-requests-against-multiple-replicas"></a>Birden çok çoğaltmalara karşı istekleri yürütün
Onaylama testleri, farklı çoğaltmalara karşı aynı isteği yürütmelidir. Rol değişiklikleri ile eşleştirildiği zaman, tutarlılık sorunları ele alınabilir. Örnek bir test aşağıdaki adımları gerçekleştirebilir:
1. Geçerli birincil için bir yazma isteği yürütün
2. Geçerli birincil için adım 1 ' de yazılan verileri döndüren bir okuma isteği yürütün
3. İkincili birincil olarak yükseltin. Bu ayrıca geçerli birincili ikinciye indirgemelidir
4. Adım 2 ' den yeni ikinciye karşı aynı okuma isteğini yürütün.

Son adımda, test döndürülen verilerin tutarlılığını sağlayabilir. Bunun açığa çıkmasına neden olabilecek bir sorun, hizmet tarafından döndürülen verilerin bellekte, ancak sonunda güvenilir bir koleksiyon tarafından yedeklenme sürecinde olması olabilir. Bu bellek içi veriler, güvenilir koleksiyonda bulunan özellikler ile düzgün şekilde korunmayabilir.

Bellek içi veriler genellikle, güvenilir bir koleksiyonda bulunan verilerin ikincil dizinlerini veya toplanmasını oluşturmak için kullanılır.

### <a name="asserting"></a>Gerçekleştirmesini istemekle
#### <a name="ensure-responses-match-across-replicas"></a>Çoğaltmalarda yanıtların eşleştiğinden emin olun
Birim testleri, birincil öğesine geçişten sonra, belirli bir istek için bir yanıtın birden çok çoğaltmalarda tutarlı olduğunu onaylar. Bu, yanıtta belirtilen verilerin güvenilir bir koleksiyon tarafından desteklenmediğini veya bu verileri çoğaltmalar arasında eşitlemesine yönelik bir mekanizmadan bellek içinde tutulmasını mümkün hale getirebilirler. Bu, Service Fabric yeniden dengeledikten veya yeni bir birincil çoğaltmaya yük devreden sonra hizmetin tutarlı yanıtları geri göndereceğini sağlar.

#### <a name="verify-service-respects-cancellation"></a>Hizmet ve iptali doğrulama
İptal belirteci iptal edildiğinde sonlandırılması gereken uzun süre çalışan veya zaman uyumsuz süreçler, iptalden sonra gerçekten sonlandırıldığı doğrulandıktan sonra doğrulanmalıdır. Bu, çoğaltma değiştirme rollerinin, birincil olmayan çoğaltmadaki çalışmayı amaçlamayan işlemlerin geçiş tamamlanmadan önce durdurulmasını sağlar. Bu Ayrıca, bu tür bir işlemin Service Fabric bir rol değişikliği veya kapatılma isteğinin tamamlanmasını engellediği sorunları da açığa çıkarabilir.

#### <a name="verify-which-replicas-should-serve-requests"></a>Hangi çoğaltmaların istekleri sunması gerektiğini doğrulayın
Bir istek birincil olmayan bir çoğaltmaya yönlendiriliyorsa, testlerin beklenen davranışı onayı gerekir. Service Fabric, ikincil çoğaltmalara istek sunma olanağı sağlar. Bununla birlikte, güvenilir koleksiyonlara yazma işlemleri yalnızca birincil çoğaltmadan gerçekleştirilebilir. Uygulamanız yalnızca birincil kopyaların istek görmesi için veya yalnızca bir istek alt kümesi bir ikincil tarafından işlenebilmesini istiyorsa, testlerin hem pozitif hem de negatif durumlar için beklenen davranışı onayı gerekir. İstek olan negatif durum, isteği işleyememesi gereken bir kopyaya yönlendirilir ve bu da pozitif bir değer olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar
[Birim testi durum bilgisi olan hizmetleri](service-fabric-how-to-unit-test-stateful-services.md)nasıl kullanacağınızı öğrenin.
