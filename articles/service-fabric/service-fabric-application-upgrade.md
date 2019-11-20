---
title: Uygulama yükseltmesini Service Fabric | Microsoft Docs
description: Bu makalede, yükseltme modlarını seçme ve sistem durumu denetimleri gerçekleştirme dahil olmak üzere Service Fabric uygulamasını yükseltmeye yönelik bir giriş sunulmaktadır.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 3c50ee149f5bcdda6cbb697830945cdc7f7a15f4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167280"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric uygulaması yükseltme
Azure Service Fabric uygulaması, bir hizmet koleksiyonudur. Yükseltme sırasında, Service Fabric yeni [uygulama bildirimini](service-fabric-application-and-service-manifests.md) önceki sürümle karşılaştırır ve uygulamadaki hangi hizmetlerin güncelleştirme gerektirdiğini belirler. Service Fabric, hizmet bildirimlerinde sürüm numaralarını önceki sürümdeki sürüm numaralarıyla karşılaştırır. Bir hizmet değiştirilmemiştir, bu hizmet yükseltilmemiştir.

## <a name="rolling-upgrades-overview"></a>Sıralı yükseltmelere genel bakış
Sıralı bir uygulama yükseltmesinde yükseltme, aşamalar halinde gerçekleştirilir. Her aşamada, güncelleştirme etki alanı olarak adlandırılan kümedeki düğümlerin bir alt kümesine yükseltme uygulanır. Sonuç olarak, uygulama yükseltme boyunca kullanılabilir kalır. Yükseltme sırasında küme, eski ve yeni sürümlerin bir karışımını içerebilir.

Bu nedenle, iki sürümün ileri ve geri uyumlu olması gerekir. Bunlar uyumlu değilse, kullanılabilirliği sürdürmek üzere çok aşamalı bir yükseltmenin hazırlanması için uygulama Yöneticisi sorumludur. Birden çok aşamalı yükseltmede ilk adım, uygulamanın önceki sürümle uyumlu bir ara sürümüne yükseltilmekte. İkinci adım güncelleştirme öncesi sürüm ile uyumluluğu kesen son sürümü yükseltmeye, ancak ara sürümle uyumludur.

Kümeyi yapılandırdığınızda, güncelleştirme etki alanları küme bildiriminde belirtilir. Güncelleştirme etki alanları belirli bir sırada güncelleştirmeleri almaz. Güncelleştirme etki alanı, bir uygulama için mantıksal dağıtım birimidir. Güncelleştirme etki alanları, hizmetlerin bir yükseltme sırasında yüksek kullanılabilirliğe açık kalmasına izin verir.

Yükseltme, kümedeki tüm düğümlere uygulanırsa, uygulamanın yalnızca bir güncelleştirme etki alanına sahip olması durumunda bu durum oluşabilir. Hizmet çalışmadığından ve yükseltme sırasında kullanılamadığından bu yaklaşım önerilmez. Ayrıca, bir küme yalnızca bir güncelleştirme etki alanıyla ayarlandığında Azure hiçbir garanti vermez.

Yükseltme tamamlandıktan sonra, tüm hizmetler ve çoğaltmalar (örnekler) aynı sürüm-i. e içinde kalır. yükseltme başarılı olursa, yeni sürüme güncelleştirilecektir; yükseltme başarısız olursa ve geri alınırsa, eski sürüme geri alınacaktır.

## <a name="health-checks-during-upgrades"></a>Yükseltmeler sırasında durum denetimleri
Bir yükseltme için, sistem durumu ilkeleri ayarlanmalıdır (veya varsayılan değerler kullanılabilir). Tüm güncelleştirme etki alanları belirtilen zaman aşımları içinde yükseltildiğinde ve tüm güncelleştirme etki alanları sağlıklı olarak kabul edildiğinde yükseltme başarılı olur.  Sağlıklı güncelleştirme etki alanı, güncelleştirme etki alanının sistem durumu ilkesinde belirtilen tüm durum denetimlerini geçirdiğini gösterir. Örneğin, sistem durumu Service Fabric tarafından tanımlandığından, bir sistem durumu ilkesi, bir uygulama örneğindeki tüm hizmetlerin *sağlıklı*olması gereken bir durum olabilir.

Service Fabric tarafından yükseltme sırasında sistem durumu ilkeleri ve denetimleri hizmet ve uygulama belirsiz bir uygulamadır. Diğer bir deyişle, hizmete özgü testler yapılmaz.  Örneğin, hizmetiniz bir verimlilik gereksinimlerine sahip olabilir, ancak Service Fabric aktarım hızını denetlemek için bilgilere sahip değildir. Gerçekleştirilen denetimler için [sistem durumu makalelerine](service-fabric-health-introduction.md) bakın. Yükseltme sırasında gerçekleşen denetimler, uygulama paketinin doğru kopyalanıp kopyalanmayacağını, örneğin başlatılmış olup olmadığı için testleri içerir.

Uygulama sistem durumu, uygulamanın alt varlıklarının bir toplamadır. Kısacası, Service Fabric uygulamada bildirilen sistem durumu aracılığıyla uygulamanın sistem durumunu değerlendirir. Ayrıca, uygulamanın tüm hizmetlerinin sistem durumunu bu şekilde değerlendirir. Service Fabric, alt öğelerinin sistem durumunu toplayarak hizmet çoğaltması gibi uygulama hizmetlerinin sistem durumunu daha fazla değerlendirir. Uygulama sistem durumu ilkesi karşılandıktan sonra yükseltme işlemi devam edebilir. Sistem durumu ilkesi ihlal edilirse, uygulama yükseltme başarısız olur.

## <a name="upgrade-modes"></a>Yükseltme modları
Uygulama yükseltme için önerdiğimiz mod, yaygın olarak kullanılan mod olan izlenen moddur. İzlenen mod, yükseltmeyi tek bir güncelleştirme etki alanında gerçekleştirir ve tüm durum denetimleri başarılı olursa (belirtilen ilke başına), sonraki güncelleştirme etki alanına otomatik olarak geçer.  Durum denetimleri başarısız olur ve/veya zaman aşımlarına ulaşıldığında yükseltme, güncelleştirme etki alanı için geri alınır veya mod izlenmeyen el ile değiştirilir. Yükseltmeyi, başarısız yükseltmeler için bu iki moddan birini seçmek üzere yapılandırabilirsiniz. 

İzlenmeyen el ile modu, bir güncelleştirme etki alanındaki her yükseltmeden sonra, sonraki güncelleştirme etki alanındaki yükseltmeyi açmak için el ile müdahale gerektirir. Service Fabric sistem durumu denetimi yapılmaz. Yönetici, sonraki güncelleştirme etki alanında yükseltmeyi başlatmadan önce sistem durumunu veya durum denetimlerini gerçekleştirir.

## <a name="upgrade-default-services"></a>Varsayılan Hizmetleri yükselt
[Uygulama bildiriminde](service-fabric-application-and-service-manifests.md) tanımlanan bazı varsayılan hizmet parametreleri, uygulama yükseltmesinin bir parçası olarak da yükseltilebilir. Yükseltmenin bir parçası olarak yalnızca [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) aracılığıyla değiştirilmesini destekleyen hizmet parametreleri değiştirilebilir. Uygulama yükseltme sırasında varsayılan Hizmetleri değiştirmenin davranışı aşağıdaki gibidir:

1. Yeni uygulama bildiriminde, kümede zaten bulunmayan varsayılan hizmetler oluşturulur.
2. Önceki ve yeni uygulama bildirimlerinde bulunan varsayılan hizmetler güncellenir. Yeni uygulama bildirimindeki varsayılan hizmetin parametreleri var olan hizmetin parametrelerinin üzerine yazar. Varsayılan bir hizmetin güncelleştirilmesi başarısız olursa uygulama yükseltme otomatik olarak geri alınacaktır.
3. Yeni uygulama bildiriminde mevcut olmayan varsayılan hizmetler, kümede mevcutsa silinir. **Varsayılan bir hizmetin silinmesinin tüm hizmetin durumunun silinmesine neden olacağını ve geri alınmadığını unutmayın.**

Uygulama yükseltmesi geri alındığında, yükseltme başlatılmadan önce varsayılan hizmet parametreleri eski değerlerine geri döndürülür, ancak silinen hizmetler eski durumuyla yeniden oluşturulamaz.

> [!TIP]
> Yukarıda bulunan kuralları 2) ve 3) üzerinde etkinleştirmek için [Enabledefaultservicesupgrade](service-fabric-cluster-fabric-settings.md) küme yapılandırma ayarı *doğru* olmalıdır (varsayılan hizmet güncelleştirmesi ve silme). Bu özellik Service Fabric sürüm 5,5 ' den itibaren desteklenir.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>HTTPS uç noktaları ile birden çok uygulamayı yükseltme
HTTP**S**kullanırken aynı uygulamanın farklı örnekleri için **aynı bağlantı noktasını** kullanmamaya dikkat etmeniz gerekir. Bunun nedeni Service Fabric uygulama örneklerinden biri için sertifikayı yükseltemeyecektir. Örneğin, uygulama 1 veya uygulama 2 ' nin her ikisi de CERT 1 ' i CERT 2 ' ye yükseltmek ister. Yükseltme gerçekleştiğinde, Service Fabric diğer uygulama hala kullandığından, http. sys ile CERT 1 kaydını temizlemiş olabilir. Bunu engellemek için Service Fabric, sertifika ile bağlantı noktasında kayıtlı olan başka bir uygulama örneğinin zaten bulunduğunu algılar (http. sys nedeniyle) ve işlem başarısız olur.

Bu nedenle Service Fabric, farklı uygulama örneklerinde **aynı bağlantı noktasını** kullanarak iki farklı hizmetin yükseltilmesini desteklemez. Diğer bir deyişle, aynı sertifikayı aynı bağlantı noktasında farklı hizmetlerde kullanamazsınız. Aynı bağlantı noktasında paylaşılan bir sertifikanız olması gerekiyorsa, hizmetlerin yerleştirme kısıtlamalarına sahip farklı makinelere yerleştirildiğinden emin olmanız gerekir. Ya da her bir uygulama örneğindeki her bir hizmet için mümkünse Service Fabric dinamik bağlantı noktaları kullanmayı düşünün. 

Https ile bir yükseltme başarısız görürseniz, "Windows HTTP sunucu API 'SI bir bağlantı noktasını paylaşan uygulamalar için birden çok sertifikayı desteklemez" hatası bildiren bir hata uyarısı.

## <a name="application-upgrade-flowchart"></a>Uygulama yükseltme akış çizelgesi
Bu paragrafı izleyen akış çizelgesi, bir Service Fabric uygulamasının yükseltme işlemini anlamanıza yardımcı olabilir. Özellikle, akışta *Healthcheckstableduration*, *healthcheckretrytimeout*ve *upgradehealthcheckınterval*dahil olmak üzere, bir güncelleştirme etki alanında yükseltmenin başarılı olduğu kabul edildiğinde yardım denetiminin nasıl yapılacağı açıklanır. bir hata.

![Service Fabric bir uygulama için yükseltme işlemi][image]

## <a name="next-steps"></a>Sonraki adımlar
[Visual Studio 'Yu kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial.md) , Visual Studio kullanarak bir uygulama yükseltme işleminde size yol gösterir.

[PowerShell kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial-powershell.md) , PowerShell kullanarak bir uygulama yükseltme işleminde size yol gösterir.

Uygulamanızın [yükseltme parametrelerini](service-fabric-application-upgrade-parameters.md)kullanarak nasıl yükseltileceğini denetleyin.

[Veri serileştirmesini](service-fabric-application-upgrade-data-serialization.md)nasıl kullanacağınızı öğrenerek uygulamanızın yükseltmelerini uyumlu hale getirin.

[Gelişmiş konulara](service-fabric-application-upgrade-advanced.md)başvurarak uygulamanızı yükseltirken gelişmiş işlevselliği nasıl kullanacağınızı öğrenin.

Uygulama [yükseltmelerinde sorun giderme](service-fabric-application-upgrade-troubleshooting.md)adımlarını izleyerek uygulama yükseltmelerinde karşılaşılan yaygın sorunları giderin.

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
