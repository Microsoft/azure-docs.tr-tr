---
title: 'Uygulama yükseltme: yükseltme parametreleri | Microsoft Docs'
description: Gerçekleştirilecek durum denetimleri ve otomatik olarak yükseltmeyi geri alma ilkeleri de dahil olmak üzere Service Fabric uygulamasını yükseltmeyle ilgili parametreleri açıklar.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: atsenthi
ms.openlocfilehash: 8503b3c8a630575b917e3710ce4d7c5d6fbe70cb
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934029"
---
# <a name="application-upgrade-parameters"></a>Uygulama yükseltme parametreleri
Bu makalede, bir Azure Service Fabric uygulamasının yükseltilmesi sırasında uygulanan çeşitli parametreler açıklanmaktadır. Uygulama yükseltme parametreleri, yükseltme sırasında uygulanan zaman aşımlarını ve sistem durumu denetimlerini denetler ve bir yükseltme başarısız olduğunda uygulanması gereken ilkeleri belirler. Uygulama parametreleri şu kullanılarak yükseltmeler için geçerlidir:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Uygulama yükseltmeleri, Kullanıcı tarafından seçilebilen üç yükseltme modundan biri ile başlatılır. Her mod kendi uygulama parametreleri kümesine sahiptir:
- İstek
- İzlenmeyen otomatik
- İzlenmeyen El Ile

Uygun gerekli ve isteğe bağlı parametreler, her bölümde aşağıdaki gibi açıklanmıştır.

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio ve PowerShell parametreleri

PowerShell kullanarak uygulama yükseltmeleri Service Fabric [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) komutunu kullanın. Yükseltme modu, [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)öğesine Izlenen, izlenmeyen **Toredauto**ya da **uniztoredmanual** parametresi geçirilerek seçilir.

Visual Studio Service Fabric uygulama yükseltme parametreleri, Visual Studio yükseltme ayarları iletişim kutusu aracılığıyla ayarlanır. Visual Studio yükseltme modu, **izlenen, Izlenebilecek** **Toredauto**veya **iztoredel ile izleme**için **yükseltme modu** açılan kutusu kullanılarak seçilir. Daha fazla bilgi için bkz. [Visual Studio 'da Service Fabric uygulamasının yükseltmesini yapılandırma](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Gerekli parametreler
(PS = PowerShell, VS = Visual Studio)

| Parametre | Uygulama hedefi | Açıklama |
| --- | --- | --- |
ApplicationName |PS| Yükseltilmekte olan uygulamanın adı. Örnekler: Fabric:/VisualObjects, Fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Yükseltmenin hedeflediği uygulama türünün sürümü. |
FailureAction |PS, VS|İzin verilen değerler **geri alma**, **el ile**ve **geçersiz**. *İzlenen* bir yükseltme, izleme ilkesi veya sistem durumu ilke ihlalleri ile karşılaştığında gerçekleştirilecek telafi eylemi. <br>**Geri alma** , yükseltmenin yükseltme öncesi sürümüne otomatik olarak geri dönecektir. <br>**El ile** yükseltme, yükseltmenin, *tasenel ile* yükseltme moduna geçmeyeceğini gösterir. <br>**Geçersiz** , hata eyleminin geçersiz olduğunu gösterir.|
İstek |PS|Yükseltme modunun izlendiğini gösterir. Cmdlet 'i bir yükseltme etki alanı için yükseltmeyi tamamladıktan sonra, yükseltme etki alanı ve kümenin sistem durumu, tanımladığınız sistem durumu ilkelerini karşılıyorsa, bir sonraki yükseltme etki alanını yükseltir Service Fabric. Yükseltme etki alanı veya kümesi sistem durumu ilkelerini karşılamıyorsa, yükseltme başarısız olur ve Service Fabric yükseltme etki alanına yönelik yükseltmeyi geri alır veya belirtilen ilke başına el ile moda geri döner. Bu, bir üretim ortamında uygulama yükseltmeleri için önerilen moddur. |
UpgradeMode | ANLARA | İzin verilen değerler Izleniyor (varsayılan), Izlenemez **Toredauto**veya **Iziztoredmanual**. Ayrıntılar için bu makaledeki her mod için PowerShell parametreleri bölümüne bakın. |
Depountoredauto | PS | Yükseltme modunun otomatik olarak izlenmeyen olduğunu gösterir. Service Fabric bir yükseltme etki alanını yükseltdikten sonra, Service Fabric uygulamanın sistem durumu ne olursa olsun bir sonraki yükseltme etki alanını yükseltir. Bu mod üretim için önerilmez ve yalnızca uygulamanın geliştirilmesi sırasında faydalıdır. |
Monitortoredmanual | PS | Yükseltme modunun izlenmeyen el ile olduğunu gösterir. Service Fabric bir yükseltme etki alanını yükseltdikten sonra, *sürdürme-ServiceFabricApplicationUpgrade* cmdlet 'ini kullanarak bir sonraki yükseltme etki alanını yükseltmenizi bekler. |

### <a name="optional-parameters"></a>İsteğe bağlı parametreler

Sistem durumu değerlendirme parametreleri isteğe bağlıdır. Bir yükseltme başladığında durum değerlendirme ölçütleri belirtilmemişse, Service Fabric uygulama örneğinin ApplicationManifest. xml dosyasında belirtilen uygulama sistem durumu ilkelerini kullanır.

> [!div class="mx-tdBreakAll"]
> | Parametre | Uygulama hedefi | Açıklama |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Uygulama parametrelerinin geçersiz kılmalarını belirtir.<br>PowerShell uygulama parametreleri, Hashtable ad/değer çiftleri olarak belirtilir. Örneğin, @ {"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>Visual Studio uygulama parametreleri, **uygulama parametreleri dosya** alanındaki Service Fabric uygulaması Yayımla iletişim kutusunda belirtilebilir.
> | Onayla |PS| İzin verilen değerler **true** ve **false**şeklindedir. Cmdlet 'ini çalıştırmadan önce onay ister. |
> | ConsiderWarningAsError |PS, VS |İzin verilen değerler **true** ve **false**şeklindedir. Varsayılan ayar, **False** değeridir. Yükseltme sırasında uygulamanın sistem durumunu değerlendirirken, uygulamanın uyarı durumu olaylarını hata olarak değerlendirin. Varsayılan olarak Service Fabric, uyarı durumu olaylarını hatalara (hata) göre değerlendirmez, bu nedenle, uyarı olayları olsa bile yükseltme devam edebilir. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Maxyüztunhealthypartitionsperservice, Maxyüztunhealthyıreplicasperpartition, Maxyüztunhealthyıservices biçimindeki izlenen yükseltme için kullanılacak varsayılan hizmet türünün sistem durumu ilkesini belirtir. Örneğin, 5, 10, 15 şu değerleri gösterir: Maxyüztunhealthypartitionsperservice = 5, Maxyüztunhealthyreplicasperpartition = 10, Maxyüztunhealthyservices = 15. |
> | Zorla | PS, VS | İzin verilen değerler **true** ve **false**şeklindedir. Yükseltme işleminin uyarı iletisini atlayıp atmadığını ve sürüm numarası değiştirilmediyse bile yükseltmeyi zorlandığını belirtir. Bu, yerel test için yararlıdır, ancak bir üretim ortamında kullanım için önerilmez; bu durumda, zaman ve potansiyel veri kaybına neden olan mevcut dağıtımın kaldırılması gerekir. |
> | ForceRestart |PS, VS |Bir yapılandırma veya veri paketini hizmet kodunu güncelleştirmeden güncelleştirirseniz, hizmet yalnızca ForceRestart özelliği **true**olarak ayarlandığında yeniden başlatılır. Güncelleştirme tamamlandığında, hizmete yeni bir yapılandırma paketinin veya veri paketinin kullanılabildiğini bildiren Service Fabric. Bu hizmet, değişiklikleri uygulamaktan sorumludur. Gerekirse, hizmet kendi kendine yeniden başlatılabilir. |
> | HealthCheckRetryTimeoutSec |PS, VS |Service Fabric, yükseltmeyi başarısız olarak bildirmeden önce sistem durumu değerlendirmesi gerçekleştirmeye devam eden süre (saniye cinsinden). Varsayılan değer 600 saniyedir. Bu süre, *HealthCheckWaitDurationSec* ulaşıldıktan sonra başlar. Bu *Healthcheckretrytimeout*içinde Service Fabric, uygulama sistem durumu için birden çok sistem durumu denetimi gerçekleştirebilir. Varsayılan değer 10 dakikadır ve uygulamanız için uygun şekilde özelleştirilmelidir. |
> | HealthCheckStableDurationSec |PS, VS |Bir sonraki yükseltme etki alanına geçmeden önce uygulamanın kararlı olduğunu doğrulamak veya yükseltmeyi tamamlamak için süre (saniye cinsinden). Bu bekleme süresi, sistem durumu denetimi gerçekleştirildikten sonra sistem durumunun algılanmamış değişikliklerini engellemek için kullanılır. Varsayılan değer 120 saniyedir ve uygulamanız için uygun şekilde özelleştirilmelidir. |
> | HealthCheckWaitDurationSec |PS, VS | Uygulamanın sistem durumunu değerlendirinceye Service Fabric önce yükseltme etki alanında yükseltme tamamlandıktan sonra beklenecek süre (saniye cinsinden). Bu süre, bir uygulamanın sağlıklı olarak kabul edilmeden önce çalıştırılması gereken zaman olarak da düşünülebilir. Sistem durumu denetimi geçerse, yükseltme işlemi bir sonraki yükseltme etki alanına geçer.  Sistem durumu denetimi başarısız olursa, *Healthcheckretrytimeout sec* 'e ulaşılana kadar sistem durumu denetimini yeniden denemeden önce, Service Fabric [Upgradehealthcheckınterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) değerini bekler. Varsayılan ve önerilen değer 0 saniyedir. |
> | Maxyüztunhealthyıdeployedapplications|PS, VS |Varsayılan ve önerilen değer 0 ' dır. Uygulamanın sağlıksız olduğu kabul edilmeden ve yükseltme başarısız olmadan önce sağlıksız olabilecek en fazla dağıtılan uygulama sayısını belirtin ( [sistem durumu bölümüne](service-fabric-health-introduction.md)bakın). Bu parametre, düğümdeki uygulama durumunu tanımlar ve yükseltme sırasında sorunları algılamaya yardımcı olur. Genellikle, uygulamanın çoğaltmaları diğer düğüme yük dengelemesi sağlar ve böylece uygulamanın sağlıklı görünmesine olanak tanınır ve yükseltmenin devam etmesine izin verilir. Kesin bir *Maxyüztunhealthıdeployedapplications* sistem durumu belirterek, Service Fabric uygulama paketiyle hızlı bir şekilde sorun algılayabilir ve hızlı yükseltme işlemi üretmeye yardımcı olabilir. |
> | Maxyüztunhealthi Hizmetleri |PS, VS |*Defaultservicetypehealthpolicy* ve *Servicetypehealthpolicymap*için bir parametre. Varsayılan ve önerilen değer 0 ' dır. Uygulama örneğinde, uygulamanın sağlıksız olduğu kabul edilmeden ve yükseltme başarısız olmadan önce sağlıksız olabilecek en fazla hizmet sayısını belirtin. |
> | Maxyüztunhealthypartitionsperservice|PS, VS |*Defaultservicetypehealthpolicy* ve *Servicetypehealthpolicymap*için bir parametre. Varsayılan ve önerilen değer 0 ' dır. Hizmetin sağlıksız olduğu kabul edilmeden önce sağlıksız olabilecek en fazla bölüm sayısını belirtin. |
> | Maxyüztunhealthyıreplicasperpartition|PS, VS |*Defaultservicetypehealthpolicy* ve *Servicetypehealthpolicymap*için bir parametre. Varsayılan ve önerilen değer 0 ' dır. Bölüm sağlıksız olarak kabul edilmeden önce sorunlu olabilecek en fazla çoğaltma sayısını belirtin. |
> | ServiceTypeHealthPolicyMap | PS, VS | Hizmet türüne ait hizmetlerin sistem durumunu değerlendirmek için kullanılan sistem durumu ilkesini temsil eder. Aşağıdaki biçimde bir karma tablo girişi alır: @ {"ServiceTypeName": "Maxyüztunhealthypartitionsperservice, Maxyüztunhealthyreplicasperpartition, Maxyüztunhealthyservices"} Örneğin: @ {"ServiceTypeName01" = "5, 10, 5"; "ServiceTypeName02" = "5, 5, 5"} |
> | TimeoutSec | PS, VS | İşlem için saniye cinsinden zaman aşımı süresini belirtir. |
> | UpgradeDomainTimeoutSec |PS, VS |Tek bir yükseltme etki alanını yükseltmek için en uzun süre (saniye cinsinden). Bu zaman aşımına ulaşıldığında, yükseltme, *FailureAction*ayarına göre duraklar ve devam eder. Varsayılan değer asla (sonsuz) ve uygulamanız için uygun şekilde özelleştirilmelidir. |
> | Yükseltme Dereplicasetchecktimeoutsec |PS, VS |Saniyeler içinde ölçülür.<br>**Durum bilgisi olmayan hizmet**--tek bir yükseltme etki alanı içinde Service Fabric, hizmetin ek örneklerinin kullanılabilir olmasını sağlamaya çalışır. Hedef örnek sayısı birden fazla ise, en fazla bir zaman aşımı değerine kadar birden fazla örneğin kullanılabilir olmasını bekler Service Fabric. Bu zaman aşımı, *yükseltilebilir Dereplicasetchecktimeoutsec* özelliği kullanılarak belirtilir. Zaman aşımı süresi dolarsa, hizmet örneklerinin sayısından bağımsız olarak Service Fabric yükseltme işlemine devam eder. Hedef örnek sayısı bir ise, Service Fabric beklemez ve hemen yükseltme işlemine devam eder.<br><br>**Durum bilgisi olan hizmet**--tek bir yükseltme etki alanı içinde, Service Fabric çoğaltma kümesinin bir çekirdeğe sahip olduğundan emin olun. Service Fabric, bir çekirdeğin kullanılabilir olmasını, en yüksek zaman aşımı değerine (yani, *yükseltilebilir Dereperepsetchecktimeoutsec* özelliği tarafından belirtilir) kadar bekler. Zaman aşımı süresi dolarsa Service Fabric, çekirdeğe bakılmaksızın yükseltmeye devam eder. Bu ayar, iletme sırasında hiçbir zaman (sonsuz), geri alınırken 1200 saniye olarak ayarlanır. |
> | UpgradeTimeoutSec |PS, VS |Tüm yükseltme için geçerli olan zaman aşımı (saniye cinsinden). Bu zaman aşımına ulaşıldığında, yükseltme durduruluyor ve *FailureAction* tetiklenir. Varsayılan değer asla (sonsuz) ve uygulamanız için uygun şekilde özelleştirilmelidir. |
> | WhatIf | PS | İzin verilen değerler **true** ve **false**şeklindedir. Cmdlet çalıştırılıyorsa ne olacağını gösterir. Cmdlet çalıştırılmadı. |

Bir uygulama örneği için *Maxyüztunhealthyıservices*, *maxyüztunhealthypartitionsperservice*ve *Maxyüztunhealthyıreplicasperpartition* ölçütü her hizmet türü için belirtilebilir. Bu parametrelerin hizmet başına ayarlanması, bir uygulamanın farklı değerlendirme ilkelerine sahip farklı hizmet türleri içermesini sağlar. Örneğin, durum bilgisiz ağ geçidi hizmet türü, belirli bir uygulama örneği için durum bilgisi olan bir altyapı hizmeti türünden farklı bir *Maxyüztunhealthypartitionsperservice* içerebilir.

## <a name="sfctl-parameters"></a>SFCTL parametreleri

Service Fabric CLı kullanarak uygulama yükseltmeleri Service Fabric, [sfctl uygulama yükseltme](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) komutunu aşağıdaki gerekli ve isteğe bağlı parametrelerle birlikte kullanın.

### <a name="required-parameters"></a>Gerekli parametreler

| Parametre | Açıklama |
| --- | --- |
| Uygulama kimliği  |Yükseltilmekte olan uygulamanın KIMLIĞI. <br> Bu genellikle uygulamanın ' Fabric: ' URI düzeni olmadan tam adıdır. Sürüm 6,0 ' den başlayarak, hiyerarşik adlar ' \~ ' karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı ' Fabric:/MyApp/APP1 ' ise, uygulama kimliği önceki sürümlerde 6.0 + ve ' MyApp/APP1 ' içinde ' MyApp\~APP1 ' olur.|
Uygulama sürümü |Yükseltmenin hedeflediği uygulama türünün sürümü.|
parametreler  |Uygulamanın yükseltilmesi sırasında uygulanacak bir JSON kodlamalı uygulama parametresi listesi.|

### <a name="optional-parameters"></a>İsteğe bağlı parametreler

| Parametre | Açıklama |
| --- | --- |
Varsayılan-hizmet-sistem durumu-ilke | Bir hizmet türünün sistem durumunu değerlendirmek için varsayılan olarak kullanılan sistem durumu ilkesinin [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) kodlu belirtimi. Harita varsayılan olarak boştur. |
hata-eylem | İzin verilen değerler **geri alma**, **el ile**ve **geçersiz**. *İzlenen* bir yükseltme, izleme ilkesi veya sistem durumu ilke ihlalleri ile karşılaştığında gerçekleştirilecek telafi eylemi. <br>**Geri alma** , yükseltmenin yükseltme öncesi sürümüne otomatik olarak geri dönecektir. <br>**El ile** yükseltme, yükseltmenin, *tasenel ile* yükseltme moduna geçmeyeceğini gösterir. <br>**Geçersiz** , hata eyleminin geçersiz olduğunu gösterir.|
zorla yeniden başlatma | Bir yapılandırma veya veri paketini hizmet kodunu güncelleştirmeden güncelleştirirseniz, hizmet yalnızca ForceRestart özelliği **true**olarak ayarlandığında yeniden başlatılır. Güncelleştirme tamamlandığında, hizmete yeni bir yapılandırma paketinin veya veri paketinin kullanılabildiğini bildiren Service Fabric. Bu hizmet, değişiklikleri uygulamaktan sorumludur. Gerekirse, hizmet kendi kendine yeniden başlatılabilir. |
durum denetimi-yeniden deneme-zaman aşımı | *FailureAction* yürütülmeden önce uygulama veya küme sağlıksız olduğunda sistem durumu değerlendirmesinin yeniden denenme süresi. İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. Varsayılan: PT0H10M0S. |
sistem durumu-kararlı-süre | Yükseltme bir sonraki yükseltme etki alanına geçmeden önce uygulamanın veya kümenin sağlıklı kalması gereken süre. İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. Varsayılan: PT0H2M0S. |
sistem durumu-denetim bekleme süresi | Bir yükseltme etki alanını tamamladıktan sonra, sistem durumu ilkelerini uygulamadan önce beklenecek süre. İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. Varsayılan: 0.|
en fazla sağlıksız-uygulamalar | Varsayılan ve önerilen değer 0 ' dır. Uygulamanın sağlıksız olduğu kabul edilmeden ve yükseltme başarısız olmadan önce sağlıksız olabilecek en fazla dağıtılan uygulama sayısını belirtin ( [sistem durumu bölümüne](service-fabric-health-introduction.md)bakın). Bu parametre, düğümdeki uygulama durumunu tanımlar ve yükseltme sırasında sorunları algılamaya yardımcı olur. Genellikle, uygulamanın çoğaltmaları diğer düğüme yük dengelemesi sağlar ve böylece uygulamanın sağlıklı görünmesine olanak tanınır ve yükseltmenin devam etmesine izin verilir. Sıkı bir *en iyi durumda olmayan uygulamalar* sistem durumu belirterek, Service Fabric uygulama paketiyle hızlı bir şekilde sorun algılayabilir ve başarısız hızlı bir yükseltme üretmenize yardımcı olabilir. 0 ile 100 arasında bir sayı olarak temsil edilir. |
Modundaysa | İzin verilen değerler Izleniyor, **upgrademode**, **iziztoredauto**, **monitortoredmanual**. Varsayılan olarak, **Monitortoredauto**. Bu değerlerin açıklamaları için Visual Studio ve PowerShell *gerekli parametreler* bölümüne bakın.|
Çoğaltma-ayarlama-denetim zaman aşımı |Saniyeler içinde ölçülür. <br>**Durum bilgisi olmayan hizmet**--tek bir yükseltme etki alanı içinde Service Fabric, hizmetin ek örneklerinin kullanılabilir olmasını sağlamaya çalışır. Hedef örnek sayısı birden fazla ise, en fazla bir zaman aşımı değerine kadar birden fazla örneğin kullanılabilir olmasını bekler Service Fabric. Bu zaman aşımı, *çoğaltma-Set-Check-Timeout* özelliği kullanılarak belirtilir. Zaman aşımı süresi dolarsa, hizmet örneklerinin sayısından bağımsız olarak Service Fabric yükseltme işlemine devam eder. Hedef örnek sayısı bir ise, Service Fabric beklemez ve hemen yükseltme işlemine devam eder.<br><br>**Durum bilgisi olan hizmet**--tek bir yükseltme etki alanı içinde, Service Fabric çoğaltma kümesinin bir çekirdeğe sahip olduğundan emin olun. Service Fabric, bir çekirdeğin kullanılabilir olmasını, en yüksek zaman aşımı değerine ( *çoğaltma-Set-Check-Timeout* özelliği tarafından belirtilen) kadar bekler. Zaman aşımı süresi dolarsa Service Fabric, çekirdeğe bakılmaksızın yükseltmeye devam eder. Bu ayar, iletme sırasında hiçbir zaman (sonsuz), geri alınırken 1200 saniye olarak ayarlanır. |
hizmet-sistem durumu-ilke | Hizmet türü adı başına hizmet türü sistem durumu ilkesiyle JSON kodlamalı eşleme. Harita varsayılan olarak boştur. [JSON biçimi parametresi.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). "Value" bölümü için JSON, **Maxyüztunhealthyıservices**, **maxyüztunhealthypartitionsperservice**ve **Maxyüztunhealthyıreplicasperpartition**değerlerini içerir. Bu parametrelerin açıklamaları için Visual Studio ve PowerShell Isteğe bağlı parametreleri bölümüne bakın.
timeout | İşlem için saniye cinsinden zaman aşımı süresini belirtir. Varsayılan: 60. |
yükseltme-etki alanı zaman aşımı | Her yükseltme etki alanının, *FailureAction* yürütülmeden önce tamamlaması gereken süre miktarı. İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. Varsayılan değer asla (sonsuz) ve uygulamanız için uygun şekilde özelleştirilmelidir. Varsayılan: P10675199DT02H48M 05.4775807 S. |
yükseltme-zaman aşımı | Her yükseltme etki alanının, *FailureAction* yürütülmeden önce tamamlaması gereken süre miktarı. İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. Varsayılan değer asla (sonsuz) ve uygulamanız için uygun şekilde özelleştirilmelidir. Varsayılan: P10675199DT02H48M 05.4775807 S.|
hata olarak uyarı | İzin verilen değerler **true** ve **false**şeklindedir. Varsayılan ayar, **False** değeridir. , Bir bayrak olarak geçirilebilir. Yükseltme sırasında uygulamanın sistem durumunu değerlendirirken, uygulamanın uyarı durumu olaylarını hata olarak değerlendirin. Varsayılan olarak Service Fabric, uyarı durumu olaylarını hatalara (hata) göre değerlendirmez, bu nedenle, uyarı olayları olsa bile yükseltme devam edebilir. |

## <a name="next-steps"></a>Sonraki adımlar
[Visual Studio 'Yu kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial.md) , Visual Studio kullanarak bir uygulama yükseltme işleminde size yol gösterir.

[PowerShell kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial-powershell.md) , PowerShell kullanarak bir uygulama yükseltme işleminde size yol gösterir.

[Linux 'ta SERVICE fabrıc CLI kullanarak uygulamanızı yükseltmek](service-fabric-application-lifecycle-sfctl.md#upgrade-application) , Service Fabric CLI kullanarak uygulama yükseltmesinde size yol gösterir.

[Service Fabric Tutulma eklentisi kullanarak uygulamanızı yükseltme](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

[Veri serileştirmesini](service-fabric-application-upgrade-data-serialization.md)nasıl kullanacağınızı öğrenerek uygulamanızın yükseltmelerini uyumlu hale getirin.

[Gelişmiş konulara](service-fabric-application-upgrade-advanced.md)başvurarak uygulamanızı yükseltirken gelişmiş işlevselliği nasıl kullanacağınızı öğrenin.

Uygulama [yükseltmelerinde sorun giderme](service-fabric-application-upgrade-troubleshooting.md)adımlarını izleyerek uygulama yükseltmelerinde karşılaşılan yaygın sorunları giderin.
