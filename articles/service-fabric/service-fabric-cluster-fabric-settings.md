---
title: Azure Service Fabric küme ayarlarını değiştirme | Microsoft Docs
description: Bu makalede, özelleştirebileceğiniz doku ayarları ve doku yükseltme ilkeleri açıklanmaktadır.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2019
ms.author: atsenthi
ms.openlocfilehash: cf070e91d6f15e80f51242722a59918d1bc70696
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615547"
---
# <a name="customize-service-fabric-cluster-settings"></a>Service Fabric kümesi ayarlarını özelleştirme
Bu makalede, Service Fabric kümeniz için özelleştirebileceğiniz çeşitli yapı ayarları açıklanmaktadır. Azure 'da barındırılan kümeler için [Azure Portal](https://portal.azure.com) veya Azure Resource Manager şablonu kullanarak ayarları özelleştirebilirsiniz. Daha fazla bilgi için bkz. [Azure kümesinin yapılandırmasını yükseltme](service-fabric-cluster-config-upgrade-azure.md). Tek başına kümeler için, *Kümeconfig. JSON* dosyasını güncelleştirerek ve kümenizde bir yapılandırma yükseltmesi gerçekleştirerek ayarları özelleştirebilirsiniz. Daha fazla bilgi için bkz. [tek başına kümenin yapılandırmasını yükseltme](service-fabric-cluster-config-upgrade-windows-server.md).

Üç farklı yükseltme ilkesi vardır:

- **Dinamik** – dinamik yapılandırmadaki değişiklikler, Service Fabric işlemlerin veya hizmet ana bilgisayar işlemleriniz üzerinde herhangi bir işlem yeniden başlatılmasına neden olmaz. 
- **Statik** – statik bir yapılandırmaya yapılan değişiklikler Service Fabric düğümün değişikliği kullanabilmesi için yeniden başlatılmasına neden olur. Düğümlerdeki hizmetler yeniden başlatılacak.
- **NotAllowed** – bu ayarlar değiştirilemez. Bu ayarları değiştirmek için kümenin yok edilmesi ve yeni bir küme oluşturulması gerekir. 

Aşağıda, bölümüne göre organize ettiğiniz doku ayarlarının bir listesi verilmiştir.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/http

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|dize, varsayılan değer "none"|Statik| Bu, sunucu sertifikasını doğrulamaz; isteği başarılı oldu. Ters proxy 'nin güvenebileceği uzak sertifika parmak izleri için, virgülle ayrılmış bir liste için yapılandırma ServiceCertificateThumbprints bakın. Ters proxy 'nin güvenebileceği uzak sertifikaların konu adı ve veren parmak izi için yapılandırma ServiceCommonNameAndIssuer ' a bakın. Daha fazla bilgi için bkz. [ters proxy güvenli bağlantısı](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, varsayılan değer 16384 |Dinamik| Gövdesi okumak için kullanılan bayt cinsinden öbek için boyut boyutunu verir. |
|CrlCheckingFlag|uint, varsayılan değer 0x40000000 |Dinamik| Uygulama/hizmet sertifika zinciri doğrulama bayrakları; Örneğin CRL denetleniyor 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY ayarı 0 olarak ayarlanıyor CRL denetimini devre dışı bırakır desteklenen değerlerin tam listesi, Certgetcertificatezincirinin dwFlags tarafından belgelenmiştir: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Saniye cinsinden süre. Varsayılan değer 120 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin.  Http uygulama ağ geçidinde işlenmekte olan http istekleri için varsayılan istek zaman aşımını verir. |
|ForwardClientCertificate|bool, varsayılan değer FALSE|Dinamik|False olarak ayarlandığında, istemci sertifikası için ters proxy istemeyecektir. Doğru olarak ayarlandığında, ters proxy, SSL el sıkışması sırasında istemci sertifikası ister ve Base64 kodlamalı PEı biçim dizesini X-Client-Certificate adlı bir üstbilgideki hizmete iletir. hizmet, isteği uygun durum kodu ile başarısız olabilir Sertifika verilerini inceledikten sonra. Bu doğru ise ve istemci bir sertifika sunmıyorsa, ters proxy boş bir üst bilgiyi iletir ve hizmetin durumu işlemesini sağlar. Ters proxy, saydam bir katman işlevi görür. Daha fazla bilgi için bkz. [istemci sertifikası kimlik doğrulamasını ayarlama](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |dize, varsayılan değer "none" |Statik| Http uygulama ağ geçidi uç noktasında kullanılacak güvenlik kimlik bilgilerinin türünü belirtir geçerli değerler None/x509 'Tur. |
|GatewayX509CertificateFindType |dize, varsayılan değer "Findbyparmak Izi" |Dinamik| GatewayX509CertificateStoreName desteklenen değer: Findbyparmak Izi; tarafından belirtilen depodaki sertifikanın nasıl aranacağını gösterir FindBySubjectName. |
|GatewayX509CertificateFindValue | dize, varsayılan değer "" |Dinamik| Http uygulama Ağ Geçidi sertifikasını bulmak için kullanılan arama filtresi değeri. Bu sertifika, HTTPS uç noktasında yapılandırılır ve hizmetler tarafından gerekirse uygulamanın kimliğini doğrulamak için de kullanılabilir. FindValue önce aranır; yoksa, yoksa. FindValueSecondary aranır. |
|GatewayX509CertificateFindValueSecondary | dize, varsayılan değer "" |Dinamik|Http uygulama Ağ Geçidi sertifikasını bulmak için kullanılan arama filtresi değeri. Bu sertifika, HTTPS uç noktasında yapılandırılır ve hizmetler tarafından gerekirse uygulamanın kimliğini doğrulamak için de kullanılabilir. FindValue önce aranır; yoksa, yoksa. FindValueSecondary aranır.|
|GatewayX509CertificateStoreName |dize, varsayılan değer "My" |Dinamik| Http uygulama ağ geçidi için sertifika içeren X. 509.440 sertifika deposunun adı. |
|HttpRequestConnectTimeout|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (5)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin.  Http uygulaması ağ geçidinden gönderilen http istekleri için bağlantı zaman aşımını verir.  |
|IgnoreCrlOfflineError|bool, varsayılan değer doğru|Dinamik|Uygulama/hizmet sertifikası doğrulaması için CRL çevrimdışı hatası yoksayılıp yoksayılmayacağı. |
|IsEnabled |Bool, varsayılan değer false |Statik| HttpApplicationGateway 'i etkinleştirip devre dışı bırakır. HttpApplicationGateway varsayılan olarak devre dışıdır ve bu yapılandırmaya olanak tanımak için ayarlanması gerekir. |
|NumberOfParallelOperations | Uint, varsayılan değer 5000 |Statik|Http sunucusu kuyruğuna gönderilecek okuma sayısı. Bu, HttpGateway tarafından memnun olabilecek eşzamanlı isteklerin sayısını denetler. |
|RemoveServiceResponseHeaders|dize, varsayılan değer "date; Server|Statik|Hizmet yanıtından kaldırılacak yanıt üst bilgilerinin noktalı virgülle ayrılmış listesi; istemciye iletmeden önce. Boş dizeye ayarlanırsa; hizmet tarafından döndürülen tüm üst bilgileri olduğu gibi geçirin. Yani Tarih ve sunucu üzerine yazma |
|Resolveservicebackoffınterval |Saniye cinsinden süre, varsayılan değer 5 ' tir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin.  Başarısız bir hizmet çözümleme işlemini yeniden denemeden önce varsayılan geri dönme aralığını verir. |
|SecureOnlyMode|bool, varsayılan değer FALSE|Dinamik| SecureOnlyMode: true: ters proxy yalnızca, güvenli uç noktaları yayınlayan hizmetlere iletir. yanlış: ters proxy, istekleri güvenli/güvenli olmayan uç noktalara iletebilir. Daha fazla bilgi için bkz. [ters proxy uç noktası seçim mantığı](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|dize, varsayılan değer ""|Dinamik|Ters proxy 'nin güvenebileceği uzak sertifikalar için parmak izlerinin virgülle ayrılmış listesi. Daha fazla bilgi için bkz. [ters proxy güvenli bağlantısı](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/http/ServiceCommonNameAndIssuer

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, varsayılan değer None|Dinamik| Ters proxy 'nin güvenebileceği uzak sertifikaların konu adı ve veren parmak izi. Daha fazla bilgi için bkz. [ters proxy güvenli bağlantısı](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, varsayılan değer 0 ' dır|Statik|BackupRestoreService için MinReplicaSetSize |
|Placementkýsýtlamalarý|dize, varsayılan değer ""|Statik|  BackupRestore hizmeti için Placementkýsýtlamalarý |
|Secretencryptioncertparmak Izi|dize, varsayılan değer ""|Dinamik|Gizli şifreleme x509 sertifikasının parmak izi |
|SecretEncryptionCertX509StoreName|dize, varsayılan değer "My"|   Dinamik|    Bu, yedekleme geri yükleme hizmeti tarafından kullanılan depolama kimlik bilgilerinin şifresini çözmek için kullanılan X. 509.440 sertifika deposunun kimlik bilgileri şifreleme ve şifre çözme için kullanılacak sertifikayı belirtir |
|TargetReplicaSetSize|int, varsayılan değer 0 ' dır|Statik| BackupRestoreService için TargetReplicaSetSize |

## <a name="clustermanager"></a>ClusterManager

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, varsayılan değer false |Dinamik|Özel yükseltme sıralaması ilkelerine izin verilip verilmeyeceğini belirtir. Bu özelliği etkinleştirmek için 2 aşamalı yükseltmeyi gerçekleştirmek üzere kullanılır. Service Fabric 6,5, küme veya uygulama yükseltmeleri sırasında yükseltme etki alanları için sıralama ilkesi belirtmeye yönelik destek ekler. Desteklenen ilkeler sayısal, Lexıgraf, ReverseNumeric ve Reverselexicografik ' dir. Varsayılan değer sayısaldır. Bu özelliği kullanabilmek için, SF 6,5 kodunun yükseltmeyi tamamladıktan sonra, ClusterManager/AllowCustomUpgradeSortPolicies küme bildirim ayarı ikinci bir yapılandırma yükseltme adımı olarak true olarak ayarlanmalıdır. Bunun iki aşamada yapılması önemlidir, aksi takdirde kod yükseltme, ilk yükseltme sırasında yükseltme sırası hakkında kafa karışarabilir.|
|EnableDefaultServicesUpgrade | Bool, varsayılan değer false |Dinamik|Uygulama yükseltmesi sırasında varsayılan Hizmetleri yükseltmeyi etkinleştirin. Yükseltmeden sonra varsayılan hizmet açıklamalarının üzerine yazılacak. |
|Fabricupgradehealthcheckınterval |Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|İzlenen bir doku yükseltmesi sırasında sistem durumu denetimi sıklığı |
|Fabricupgradestatuspollınterval |Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|Doku yükseltme durumu için yoklama sıklığı. Bu değer, herhangi bir GetFabricUpgradeProgress çağrısının güncelleştirme oranını belirler |
|Imagebuildertimeoutbuffer |Saniye cinsinden süre, varsayılan 3 ' ün |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Görüntü Oluşturucu 'ya özgü zaman aşımı hatalarının istemciye geri dönmesi için izin verilen süre. Bu arabellek çok küçükse; daha sonra istemci zaman aşımına uğrar ve genel bir zaman aşımı hatası alırlar. |
|InfrastructureTaskHealthCheckRetryTimeout | Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Bir altyapı görevinin işlenmesi sırasında başarısız olan durum denetimlerini yeniden denemek için harcanan zaman miktarı. Geçilen bir sistem durumu denetimini gözlemlemek bu zamanlayıcıyı sıfırlar. |
|InfrastructureTaskHealthCheckStableDuration | Saniye cinsinden süre, varsayılan değer 0 ' dır|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Bir altyapı görevinin son işlemlerini işlemeden önce, birbirini izleyen ardışık durum denetimlerini gözlemlemek için geçen süre. Başarısız bir durum denetimini gözlemlemek, bu süreölçeri sıfırlar. |
|InfrastructureTaskHealthCheckWaitDuration |Saniye cinsinden süre, varsayılan değer 0 ' dır|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Bir altyapı görevinin işlenmesinden sonra sistem durumu denetimleri başlatılmadan önce beklenecek süre. |
|InfrastructureTaskProcessingInterval | Saniye cinsinden süre, varsayılan değer 10 ' dur |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Altyapı görevi işleme durumu makinesi tarafından kullanılan işleme aralığı. |
|MaxCommunicationTimeout |Saniye cinsinden süre, varsayılan değer 600 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. ClusterManager ve diğer sistem hizmetleri arasındaki iç iletişim için maksimum zaman aşımı (örn.) Adlandırma Hizmeti; Yük Devretme Yöneticisi ve vb.). Bu zaman aşımı genel MaxOperationTimeout değerinden küçük olmalıdır (her istemci işlemi için sistem bileşenleri arasında birden fazla iletişim olabilir). |
|MaxDataMigrationTimeout |Saniye cinsinden süre, varsayılan değer 600 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Yapı yükseltmesi gerçekleştirildikten sonra veri geçişi kurtarma işlemleri için maksimum zaman aşımı. |
|MaxOperationRetryDelay |Saniye cinsinden süre, varsayılan değer 5 ' tir|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Hatalarla karşılaşıldığında iç yeniden denemeler için en fazla gecikme. |
|MaxOperationTimeout |Saniye cinsinden süre, varsayılan değer MaxValue |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. ClusterManager 'daki iç işlem işlemleri için genel zaman aşımı üst sınırı. |
|MaxTimeoutRetryBuffer | Saniye cinsinden süre, varsayılan değer 600 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Zaman aşımları nedeniyle dahili olarak yeniden denendiğinde en fazla işlem zaman aşımı `<Original Time out> + <MaxTimeoutRetryBuffer>`. MinOperationTimeout artışlarına ek zaman aşımı eklenir. |
|MinOperationTimeout | Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. ClusterManager 'da iç işlem işlemleri için en düşük genel zaman aşımı. |
|MinReplicaSetSize |Int, varsayılan 3 ' dir |Izin verilmiyor|ClusterManager için MinReplicaSetSize. |
|Placementkýsýtlamalarý | dize, varsayılan değer "" |Izin verilmiyor|ClusterManager için Placementkýsýtlamalarý. |
|QuorumLossWaitDuration |Saniye cinsinden süre, varsayılan değer MaxValue |Izin verilmiyor| Zaman aralığı değerini saniye cinsinden belirtin. ClusterManager için QuorumLossWaitDuration. |
|ReplicaRestartWaitDuration |Saniye cinsinden süre, varsayılan değer (60,0 \* 30)|Izin verilmiyor|Zaman aralığı değerini saniye cinsinden belirtin. ClusterManager için ReplicaRestartWaitDuration. |
|ReplicaSetCheckTimeoutRollbackOverride |Saniye cinsinden süre, varsayılan değer 1200 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. ReplicaSetCheckTimeout, en fazla DWORD değerine ayarlanırsa geri alma amacıyla bu yapılandırma değeri ile geçersiz kılınır. İlet geri alma için kullanılan değer hiçbir şekilde geçersiz kılınmaz. |
|SkipRollbackUpdateDefaultService | Bool, varsayılan değer false |Dinamik|CM, uygulama yükseltme geri alma sırasında güncelleştirilmiş varsayılan Hizmetleri geri döndürmeyi atlar. |
|StandByReplicaKeepDuration | Saniye cinsinden süre, varsayılan değer (3600,0 \* 2)|Izin verilmiyor|Zaman aralığı değerini saniye cinsinden belirtin. ClusterManager için StandByReplicaKeepDuration. |
|TargetReplicaSetSize |Int, varsayılan değer 7 ' dir |Izin verilmiyor|ClusterManager için TargetReplicaSetSize. |
|Upgradehealthcheckınterval |Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|İzlenen uygulama yükseltmeleri sırasında sistem durumu denetimlerinin sıklığı |
|Upgradestatuspollınterval |Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|Uygulama yükseltme durumu için yoklama sıklığı. Bu değer, herhangi bir GetApplicationUpgradeProgress çağrısının güncelleştirme oranını belirler |

## <a name="common"></a>Common

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Perfmonitorınterval |Saniye cinsinden süre, varsayılan değer 1 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Performans izleme aralığı. 0 veya negatif değer ayarı izlemeyi devre dışı bırakır. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, varsayılan değer None|Dinamik|Düğümleri boşaltdığında ilke birleştirmesini belirtir. Belirli bir ölçüm 0 için, SF 'Lerin düğümleri UDs ve FDs 'lerde eşit olarak birleştirmeyi denemesinin gerektiği anlamına gelir. 1 yalnızca düğümlerin birleştirilmesi gerektiğini gösterir |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, varsayılan değer None|Dinamik|Yük Dengeleme için değil, birleştirme için kullanılması gereken ölçüm kümesini belirler. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, varsayılan değer None|Dinamik|[0,0-1,0] aralığında yüzde veya sayı olarak boş düğüm sayısı belirterek kümeyi ele almak için gereken boş düğüm sayısını belirler > = 1,0 |

## <a name="diagnostics"></a>Tanılama

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, varsayılan değer doğru | Dinamik | Kümenin durumunu denetim dışında etkilemeden HTTP isteklerini dışlayın. Seçili yalnızca "GET" türü istekleri hariç tutulur; Ancak bu değiştirilebilir. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, varsayılan değer doğru | Dinamik |Uygulama adına tanılama depolarına erişirken kimliğe bürünme gerekip gerekmediği. |
|Appetwtracedeletionageındays |Int, varsayılan 3 ' dir | Dinamik |Uygulama ETW izlemeleri içeren eski ETL dosyalarını silediğimiz gün sayısı. |
|ApplicationLogsFormatVersion |int, varsayılan değer 0 ' dır | Dinamik |Uygulama günlüklerinin biçimi için sürüm. Desteklenen değerler 0 ve 1 ' dir. Sürüm 1, ETW olay kaydından sürüm 0 ' dan daha fazla alan içerir. |
|AuditHttpRequests |Bool, varsayılan değer false | Dinamik | HTTP denetimini açın veya kapatın. Denetimin amacı, kümede gerçekleştirilen etkinlikleri görsağlamaktır; isteği kimin başlattığını de kapsayan. Bunun en iyi deneme günlüğü olduğunu unutmayın; ve izleme kaybı oluşabilir. "Kullanıcı" kimlik doğrulaması ile HTTP istekleri kaydedilmez. |
|Capturehttptelemetri|Bool, varsayılan değer doğru | Dinamik | HTTP telemetrisini açın veya kapatın. Telemetri amacı, gelecekteki işleri planlamak ve sorun bölgelerini belirlemek için telemetri verilerini yakalamaya Service Fabric içindir. Telemetri, kişisel verileri veya istek gövdesini kaydetmez. Telemetri, aksi yapılandırılmadığı takdirde tüm HTTP isteklerini yakalar. |
|Lclusterıd |Dize | Dinamik |Kümenin benzersiz kimliği. Bu, küme oluşturulduğunda üretilir. |
|Consumerınstances |Dize | Dinamik |DCA tüketici örneklerinin listesi. |
|Diskfullsafetyspaceınmb |Int, varsayılan değer 1024 ' dir | Dinamik |DCA tarafından kullanımı korumak için MB cinsinden kalan disk alanı. |
|Enablebir Ratracesession |Bool, varsayılan değer false | Statik |Bayrak, dairesel izleme oturumlarının kullanılması gerekip gerekmediğini belirtir. |
|EnablePlatformEventsFileSink |Bool, varsayılan değer false | Statik |Diske yazılan platform olaylarını etkinleştir/devre dışı bırak |
|EnableTelemetry |Bool, varsayılan değer doğru | Dinamik |Bu, Telemetriyi etkinleştirir veya devre dışı bırakır. |
|Failuıresonlyhttptelemetri | Bool, varsayılan değer false | Dinamik | HTTP telemetri yakalaması etkinse; yalnızca başarısız istekleri yakala. Bu, telemetri için oluşturulan olayların sayısını kesmeye yardımcı olur. |
|HttpTelemetryCapturePercentage | int, varsayılan değer 50 ' dir | Dinamik | HTTP telemetri yakalaması etkinse; isteklerin yalnızca rastgele yüzdesini yakala. Bu, telemetri için oluşturulan olayların sayısını kesmeye yardımcı olur. |
|Maxdiskquocontainer MB |Int, varsayılan değer 65536 ' dir | Dinamik |Windows Fabric günlük dosyaları için disk kotası (MB). |
|ProducerInstances |Dize | Dinamik |DCA üreticisi örneklerinin listesi. |

## <a name="dnsservice"></a>DnsService
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, varsayılan değer FALSE|Statik|Bölümlenmiş hizmetler için DNS sorguları desteğini etkinleştirmeye yönelik bayrak. Özellik varsayılan olarak kapalıdır. Daha fazla bilgi için bkz [. DNS hizmeti Service Fabric.](service-fabric-dnsservice.md)|
|Yükteki InstanceCount|int, varsayılan değer-1|Statik|Varsayılan değer-1 ' dir ve bu, DnsService 'in her düğümde çalıştığı anlamına gelir. DnsService 'in iyi bilinen bağlantı noktası 53 ' i kullandığından, bu, aynı makinede birden fazla örneğe sahip olmadığından, bunun 1 olarak ayarlanması gerekir.|
|IsEnabled|bool, varsayılan değer FALSE|Statik|DnsService 'i etkinleştirilir/devre dışı bırakır. DnsService varsayılan olarak devre dışıdır ve bu yapılandırmaya olanak tanımak için ayarlanması gerekir. |
|PartitionPrefix|dize, varsayılan değer "--"|Statik|Bölümlenmiş hizmetler için DNS sorgularındaki bölüm öneki dize değerini denetler. Değer: <ul><li>Bir DNS sorgusunun parçası olacağı için RFC uyumlu olmalıdır.</li><li>'. ' Noktası, DNS son ek davranışı ile nokta kesintiye uğratan olarak bir nokta içermemelidir.</li><li>5 karakterden daha uzun olmamalıdır.</li><li>Boş bir dize olamaz.</li><li>PartitionPrefix ayarı geçersiz kılınırsa, PartitionSuffix geçersiz kılınmalıdır ve tam tersi de geçerlidir.</li></ul>Daha fazla bilgi için bkz [. DNS hizmeti Service Fabric.](service-fabric-dnsservice.md).|
|PartitionSuffix|dize, varsayılan değer ""|Statik|Bölümlenmiş hizmetler için DNS sorgularında bölüm soneki dize değerini denetler. Değer: <ul><li>Bir DNS sorgusunun parçası olacağı için RFC uyumlu olmalıdır.</li><li>'. ' Noktası, DNS son ek davranışı ile nokta kesintiye uğratan olarak bir nokta içermemelidir.</li><li>5 karakterden daha uzun olmamalıdır.</li><li>PartitionPrefix ayarı geçersiz kılınırsa, PartitionSuffix geçersiz kılınmalıdır ve tam tersi de geçerlidir.</li></ul>Daha fazla bilgi için bkz [. DNS hizmeti Service Fabric.](service-fabric-dnsservice.md). |

## <a name="eventstoreservice"></a>EventStoreService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, varsayılan değer 0 ' dır|Statik|EventStore hizmeti için MinReplicaSetSize |
|Placementkýsýtlamalarý|dize, varsayılan değer ""|Statik|  EventStore hizmeti için Placementkýsýtlamalarý |
|TargetReplicaSetSize|int, varsayılan değer 0 ' dır|Statik| EventStore hizmeti için TargetReplicaSetSize |

## <a name="fabricclient"></a>FabricClient

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Connectionınitializationtimeout |Saniye cinsinden süre, varsayılan değer 2 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. İstemci, ağ geçidine bir bağlantı açmayı denediğinde her seferinde bağlantı zaman aşımı aralığı.|
|HealthOperationTimeout |Saniye cinsinden süre, varsayılan değer 120 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Health Manager 'a gönderilen rapor iletisi için zaman aşımı. |
|Healthreportretrysendınterval |Saniye cinsinden süre, varsayılan değer 30, minimum değer 1 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Raporlama bileşeninin birikmiş sistem durumu raporlarını sistem sağlığı Yöneticisi 'ne yeniden sonlandıran Aralık. |
|Healthreportsendınterval |Saniye cinsinden süre, varsayılan değer 30 ' dur |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Raporlama bileşeninin Health Manager 'a birikmiş durum raporları gönderdiği Aralık. |
|Keepaliveıntervalınseconds |Int, varsayılan değer 20 ' dir |Statik|FabricClient aktarımının ağ geçidine canlı tutma iletileri gönderdiği Aralık. 0 için; keepAlive devre dışı. Pozitif bir değer olmalıdır. |
|MaxFileSenderThreads |Uint, varsayılan değer 10 ' dur |Statik|Paralel olarak aktarılan en fazla dosya sayısı. |
|NodeAddresses |dize, varsayılan değer "" |Statik|Adlandırma Hizmeti ile iletişim kurmak için kullanılabilecek farklı düğümlerdeki adresler (bağlantı dizeleri) koleksiyonu. Başlangıçta Istemci, adreslerden birini rastgele seçerek bağlar. Birden fazla bağlantı dizesi sağlanırsa ve bir iletişim veya zaman aşımı hatası nedeniyle bağlantı başarısız olursa, Istemci, sonraki adresi sırayla kullanacak şekilde geçiş yapar. Yeniden deneme semantiğinin ayrıntıları için Adlandırma Hizmeti adresi yeniden deneme bölümüne bakın. |
|PartitionLocationCacheLimit |Int, varsayılan değer 100000 ' dir |Statik|Hizmet çözümlemesi için önbelleğe alınan bölüm sayısı (sınır olmaması için 0 olarak ayarlanır). |
|Retrybackoffınterval |Saniye cinsinden süre, varsayılan 3 ' ün |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. İşlemi yeniden denemeden önce geri dönüş aralığı. |
|Servicechangepollınterval |Saniye cinsinden süre, varsayılan değer 120 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Kaydedilen hizmet değişikliği bildirimleri geri çağırmaları için istemciden ağ geçidine yapılan arka arkaya yapılan yoklamalar arasındaki Aralık. |

## <a name="fabrichost"></a>FabricHost

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, varsayılan değer 10 ' dur |Dinamik|Bu, sistemin başarısız olarak etkinleştirme işlemini gerçekleştirmeden önce yeniden denemesi gereken en büyük sayısıdır. |
|Activationmaxretryınterval |Saniye cinsinden süre, varsayılan değer 300 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Etkinleştirme için en fazla yeniden deneme aralığı. Her sürekli hatada, yeniden deneme aralığı en az (Activationmaxretryınterval;) olarak hesaplanır. Sürekli hata sayısı * Activationretrybackoffınterval). |
|Activationretrybackoffınterval |Saniye cinsinden süre, varsayılan değer 5 ' tir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Her etkinleştirme hatasında geri alma aralığı; her sürekli etkinleştirme hatasında sistem etkinleştirmeyi MaxActivationFailureCount 'a kadar yeniden dener. Her denemeye yönelik yeniden deneme aralığı, sürekli etkinleştirme hatasının ve etkinleştirme geri dönme aralığının bir ürünüdür. |
|EnableRestartManagement |Bool, varsayılan değer false |Dinamik|Bu, sunucu yeniden başlatmasını etkinleştirmektir. |
|EnableServiceFabricAutomaticUpdates |Bool, varsayılan değer false |Dinamik|Bu, yapı otomatik güncelleştirmesini Windows Update aracılığıyla etkinleştirmektir. |
|EnableServiceFabricBaseUpgrade |Bool, varsayılan değer false |Dinamik|Bu, sunucu için temel güncelleştirmeyi etkinleştirmektir. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, varsayılan değer doğru | Statik | FabricHost hata raporlama modundayken DCA 'da daha hızlı karşıya yükleme ücretleri sağlar. |
|FailureReportingTimeout | TimeSpan, varsayılan:: TimeSpan:: FromSeconds (60) | Statik |Zaman aralığı değerini saniye cinsinden belirtin. FabricHost bir erken aşama başlatma hatasıyla karşılaştığında, DCA hata raporlama için zaman aşımı. | 
|RunDCAOnStartupFailure | Bool, varsayılan değer doğru | Statik |FabricHost 'ta başlatma sorunları olduğunda, günlükleri karşıya yüklemek için DCA 'nın başlatılıp başlatılmayacağını belirler. | 
|StartTimeout |Saniye cinsinden süre, varsayılan değer 300 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Fabricactivationmanager başlatması için zaman aşımı. |
|Durma zaman aşımı |Saniye cinsinden süre, varsayılan değer 300 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Barındırılan hizmet etkinleştirme için zaman aşımı; devre dışı bırakma ve yükseltme. |

## <a name="fabricnode"></a>FabricNode

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |dize, varsayılan değer "Findbyparmak Izi" |Dinamik|ClientAuthX509StoreName desteklenen değer: Findbyparmak Izi; tarafından belirtilen depodaki sertifikanın nasıl aranacağını gösterir FindBySubjectName. |
|ClientAuthX509FindValue |dize, varsayılan değer "" | Dinamik|FabricClient varsayılan yönetici rolü sertifikasını bulmak için kullanılan arama filtresi değeri. |
|ClientAuthX509FindValueSecondary |dize, varsayılan değer "" |Dinamik|FabricClient varsayılan yönetici rolü sertifikasını bulmak için kullanılan arama filtresi değeri. |
|ClientAuthX509StoreName |dize, varsayılan değer "My" |Dinamik|Varsayılan yönetici rolü FabricClient sertifikası içeren X. 509.440 sertifika deposunun adı. |
|ClusterX509FindType |dize, varsayılan değer "Findbyparmak Izi" |Dinamik|ClusterX509StoreName desteklenen değerler tarafından belirtilen depodaki küme sertifikasının nasıl aranacağını gösterir: "Findbyparmak Izi"; "FindBySubjectName" Ile "FindBySubjectName"; birden çok eşleşme olduğunda; en son süre sonu ile biri kullanılır. |
|ClusterX509FindValue |dize, varsayılan değer "" |Dinamik|Küme sertifikasını bulmak için kullanılan arama filtresi değeri. |
|ClusterX509FindValueSecondary |dize, varsayılan değer "" |Dinamik|Küme sertifikasını bulmak için kullanılan arama filtresi değeri. |
|ClusterX509StoreName |dize, varsayılan değer "My" |Dinamik|Küme içi iletişimin güvenliğini sağlamak için küme sertifikası içeren X. 509.440 sertifika deposunun adı. |
|EndApplicationPortRange |int, varsayılan değer 0 ' dır |Statik|Barındırma alt sistemi tarafından yönetilen uygulama bağlantı noktalarının sonu (dahil değil). Barındırma sırasında EndpointFilteringEnabled değeri true ise gereklidir. |
|ServerAuthX509FindType |dize, varsayılan değer "Findbyparmak Izi" |Dinamik|ServerAuthX509StoreName desteklenen değer: Findbyparmak Izi; tarafından belirtilen depodaki sunucu sertifikasının nasıl aranacağını gösterir FindBySubjectName. |
|ServerAuthX509FindValue |dize, varsayılan değer "" |Dinamik|Sunucu sertifikasını bulmak için kullanılan arama filtresi değeri. |
|ServerAuthX509FindValueSecondary |dize, varsayılan değer "" |Dinamik|Sunucu sertifikasını bulmak için kullanılan arama filtresi değeri. |
|ServerAuthX509StoreName |dize, varsayılan değer "My" |Dinamik|Bir merkezi hizmet için sunucu sertifikası içeren X. 509.440 sertifika deposunun adı. |
|StartApplicationPortRange |int, varsayılan değer 0 ' dır |Statik|Barındırma alt sistemi tarafından yönetilen uygulama bağlantı noktalarının başlangıcı. Barındırma sırasında EndpointFilteringEnabled değeri true ise gereklidir. |
|Statetraceınterval |Saniye cinsinden süre, varsayılan değer 300 ' dir |Statik|Zaman aralığı değerini saniye cinsinden belirtin. Her düğümdeki düğüm durumunun ve FM/FMM üzerindeki düğümlerin izleme aralığı. |
|UserRoleClientX509FindType |dize, varsayılan değer "Findbyparmak Izi" |Dinamik|UserRoleClientX509StoreName desteklenen değer: Findbyparmak Izi; tarafından belirtilen depodaki sertifikanın nasıl aranacağını gösterir FindBySubjectName. |
|UserRoleClientX509FindValue |dize, varsayılan değer "" |Dinamik|FabricClient Kullanıcı rolü için sertifikayı bulmak için kullanılan arama filtresi değeri. |
|UserRoleClientX509FindValueSecondary |dize, varsayılan değer "" |Dinamik|FabricClient Kullanıcı rolü için sertifikayı bulmak için kullanılan arama filtresi değeri. |
|UserRoleClientX509StoreName |dize, varsayılan değer "My" |Dinamik|FabricClient varsayılan kullanıcı rolü için sertifika içeren X. 509.440 sertifika deposunun adı. |

## <a name="failovermanager"></a>FailoverManager

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|bool, varsayılan değer FALSE |Dinamik|Çekirdek düğüm için düğüm durumunun kaldırılmasına izin verilip verilmeyeceğini belirten bayrak |
|BuildReplicaTimeLimit|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (3600)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Durum bilgisi olan bir çoğaltma oluşturmak için zaman sınırı; bir uyarı sistem durumu raporu başlatılacak |
|ClusterPauseThreshold|int, varsayılan değer 1 ' dir|Dinamik|Sistemdeki düğümlerin sayısı bu değerin altına giderseniz, yerleştirme; Yük Dengeleme; ve yük devretme durdurulur. |
|Createınstancetimelimit|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (300)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Durum bilgisi olmayan örnek oluşturmak için zaman sınırı; bir uyarı sistem durumu raporu başlatılacak |
|ExpectedClusterSize|int, varsayılan değer 1 ' dir|Dinamik|Küme başlangıçta başlatıldığında; FM, diğer hizmetleri yerleştirmeye başlamadan önce bu sayıda düğümün kendilerini rapor vermesini bekler; adlandırma gibi sistem hizmetleri de dahil. Bu değerin artırılması, kümenin başlaması için gereken süreyi artırır; Ancak, erken düğümlerin aşırı yüklenmesine ve ayrıca daha fazla düğüm çevrimiçi hale geldiği için gerekli olan ek taşınmasına engel olur. Bu değer genellikle ilk küme boyutunun küçük bir kesri olarak ayarlanmalıdır. |
|ExpectedNodeDeactivationDuration|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (60.0 \* 30)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Bu, bir düğümün ' de devre dışı bırakma işlemini tamamlaması için beklenen süredir. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (60.0 \* 30)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Bu, Windows Fabric yükseltmesi sırasında bir düğümün yükseltilmesi için beklenen süredir. |
|ExpectedReplicaUpgradeDuration|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (60.0 \* 30)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Bu, uygulama yükseltmesi sırasında bir düğümde yükseltilecek tüm çoğaltmaların beklenen süredir. |
|Isingletonreplicamoveallowedduringupgrade|bool, varsayılan değer doğru|Dinamik|True olarak ayarlanırsa; hedef çoğaltma kümesi boyutu 1 olan çoğaltmaların, yükseltme sırasında taşınmasına izin verilir. |
|MinReplicaSetSize|Int, varsayılan 3 ' dir|Izin verilmiyor|Bu, FM için en düşük çoğaltma kümesi boyutudur. Etkin FM çoğaltmaları sayısı bu değerin altına düşerse; en az çoğaltma sayısı kurtarılana kadar FM kümedeki değişiklikleri reddeder |
|Placementkýsýtlamalarý|dize, varsayılan değer ""|Izin verilmiyor|Yük Devretme Yöneticisi çoğaltmaları için herhangi bir yerleştirme kısıtlaması |
|PlacementTimeLimit|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (600)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Hedef çoğaltma sayısına ulaşmayla ilgili zaman sınırı; bir uyarı sistem durumu raporu başlatılacak |
|QuorumLossWaitDuration |Saniye cinsinden süre, varsayılan değer MaxValue |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Bu, bir bölümün çekirdek kaybı durumunda olmasını izin verdiğimiz en uzun süredir. Bu süre sonunda bölüm hala çekirdek kayıpla devam ediyorsa; Bölüm, kayıp olarak aşağı çoğaltmaları göz önünde bulundurarak çekirdek kaybından kurtarılır. Bunun potansiyel olarak veri kaybına neden olabileceğini unutmayın. |
|ReconfigurationTimeLimit|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (300)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Yeniden yapılandırma için zaman sınırı; bir uyarı sistem durumu raporu başlatılacak |
|ReplicaRestartWaitDuration|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (60.0 \* 30)|Izin verilmiyor|Zaman aralığı değerini saniye cinsinden belirtin. Bu, FMService için ReplicaRestartWaitDuration |
| SeedNodeQuorumAdditionalBufferNodes | int, varsayılan değer 0 ' dır | Dinamik | Olması gereken çekirdek düğümlerin arabelleği (çekirdek düğümlerin çekirdeği ile birlikte) FM, en fazla (totalNumSeedNodes-(seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) çekirdek düğümlerinin aşağı çıkmasına izin verilmelidir. |
|StandByReplicaKeepDuration|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (3600.0 \* 24 \* 7)|Izin verilmiyor|Zaman aralığı değerini saniye cinsinden belirtin. Bu, FMService için StandByReplicaKeepDuration ' dır |
|TargetReplicaSetSize|Int, varsayılan değer 7 ' dir|Izin verilmiyor|Bu, Windows Fabric korumayacak olan FM çoğaltmalarının hedef sayısıdır. Daha yüksek bir sayı, FM verilerinin daha yüksek güvenilirliğine neden olur; küçük bir performans zorunluluğunu getirir. |
|UserMaxStandByReplicaCount |int, varsayılan değer 1 ' dir |Dinamik|Sistemin Kullanıcı Hizmetleri için sakladığı varsayılan en fazla bekleme çoğaltması sayısı. |
|UserReplicaRestartWaitDuration |Saniye cinsinden süre, varsayılan değer 60,0 \* 30 ' dur |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Kalıcı bir çoğaltma geçtiğinde; Windows Fabric, çoğaltmanın yeni değiştirme çoğaltmaları oluşturmadan önce geri gelmesi için bu süreyi bekler (Bu durum, durumun bir kopyasını gerektirir). |
|UserStandByReplicaKeepDuration |Saniye cinsinden süre, varsayılan değer 3600,0 \* 24 \* 7 |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Kalıcı bir çoğaltma eski durumdan geri geldiğinde zaten değiştirilmiş olabilir. Bu süreölçer, bir FM 'in bekleme çoğaltmasını iptal etmeden önce ne kadar süreyle tutacağına ilişkin süreyi belirler. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, varsayılan değer 604800 ' dir |Statik| Bu, Terminal durumundaki eylemlerin ne kadar süreyle saklanacağını yaklaşık olarak ne kadar süreyle tutacağınızdır. Bu, Storedadctioncleanupınseconds öğesine de bağlıdır; temizleyen çalışma yalnızca bu aralıkta yapıldığından. 604800 7 gündür. |
|Datalosscheckpollınseconds|int, varsayılan değer 5 ' tir|Statik|Bu, sistemin gerçekleştirdiği denetimler arasındaki süredir, veri kaybını beklerken meydana gelir. Veri kaybı numarasının iç yineleme başına denetlenme sayısı DataLossCheckWaitDurationInSeconds/this olur. |
|DataLossCheckWaitDurationInSeconds|int, varsayılan değer 25 ' tir|Statik|Toplam süre miktarı; Saniyeler içinde; sistemin veri kaybını bekleyeceği bir durum olacaktır. Bu, StartPartitionDataLossAsync () API 'si çağrıldığında dahili olarak kullanılır. |
|MinReplicaSetSize |int, varsayılan değer 0 ' dır |Statik|FaultAnalysisService için MinReplicaSetSize. |
|Placementkýsýtlamalarý | dize, varsayılan değer ""|Statik| FaultAnalysisService için Placementkýsýtlamalarý. |
|QuorumLossWaitDuration | Saniye cinsinden süre, varsayılan değer MaxValue |Statik|Zaman aralığı değerini saniye cinsinden belirtin. FaultAnalysisService için QuorumLossWaitDuration. |
|ReplicaDropWaitDurationInSeconds|int, varsayılan değer 600 ' dir|Statik|Bu parametre, veri kaybı API 'si çağrıldığında kullanılır. Çoğaltmayı kaldırma işlemi dahili olarak yapıldıktan sonra sistemin bir çoğaltmanın ne kadar süreyle beklediğini denetler. |
|ReplicaRestartWaitDuration |Saniye cinsinden süre, varsayılan değer 60 dakikadır|Statik|Zaman aralığı değerini saniye cinsinden belirtin. FaultAnalysisService için ReplicaRestartWaitDuration. |
|StandByReplicaKeepDuration| Saniye cinsinden süre, varsayılan değer (60*24*7) dakikadır |Statik|Zaman aralığı değerini saniye cinsinden belirtin. FaultAnalysisService için StandByReplicaKeepDuration. |
|Storekactioncleanupıntervalınseconds | Int, varsayılan değer 3600 ' dir |Statik|Bu, deponun ne sıklıkta temizlenecektir. Yalnızca bir Terminal durumundaki eylemler; en az CompletedActionKeepDurationInSeconds önce tamamlanan ve bu işlem kaldırılacak. |
|StoredChaosEventCleanupIntervalInSeconds | Int, varsayılan değer 3600 ' dir |Statik|Bu, deponun Temizleme işlemi için ne sıklıkta denetleneceğini; olay sayısı 30000 ' den büyükse; Temizleme açılır. |
|TargetReplicaSetSize |int, varsayılan değer 0 ' dır |Statik|FaultAnalysisService için TargetReplicaSetSize NOT_PLATFORM_UNIX_START. |

## <a name="federation"></a>Federasyon

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Kirasüresi |Saniye cinsinden süre, varsayılan değer 30 ' dur |Dinamik|Bir kiralamanın bir düğüm ile komşuları arasında kalacağı süre. |
|LeaseDurationAcrossFaultDomain |Saniye cinsinden süre, varsayılan değer 30 ' dur |Dinamik|Bir kiralamanın hata etki alanları genelinde bir düğüm ve komşuları arasında kalacağı süre. |

## <a name="filestoreservice"></a>FileStoreService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|AcceptChunkUpload|bool, varsayılan değer doğru|Dinamik|Dosya deposu hizmetinin öbek tabanlı dosya yükleme işleminin kabul edilip edilmeyeceğini ve kopyalama uygulama paketi sırasında olmadığını belirleme yapılandırması. |
|AnonymousAccessEnabled | Bool, varsayılan değer doğru |Statik|FileStoreService paylaşımlarına Anonim erişimi etkinleştirin/devre dışı bırakın. |
|CommonName1Ntlmx509CommonName|dize, varsayılan değer ""|Statik| NTLM kimlik doğrulaması kullanılırken CommonName1NtlmPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının ortak adı |
|CommonName1Ntlmx509StoreLocation|dize, varsayılan değer "LocalMachine"|Statik|NTLM kimlik doğrulaması kullanılırken CommonName1NtlmPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının depolama konumu |
|CommonName1Ntlmx509StoreName|dize, varsayılan değer "MY"| Statik|NTLM kimlik doğrulaması kullanılırken CommonName1NtlmPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının depolama adı |
|CommonName2Ntlmx509CommonName|dize, varsayılan değer ""|Statik|NTLM kimlik doğrulaması kullanılırken CommonName2NtlmPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının ortak adı |
|CommonName2Ntlmx509StoreLocation|dize, varsayılan değer "LocalMachine"| Statik|NTLM kimlik doğrulaması kullanılırken CommonName2NtlmPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının depolama konumu |
|CommonName2Ntlmx509StoreName|dize, varsayılan değer "MY"|Statik| NTLM kimlik doğrulaması kullanılırken CommonName2NtlmPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının depolama adı |
|CommonNameNtlmPasswordSecret|SecureString, varsayılan:: SecureString ("")| Statik|NTLM kimlik doğrulaması kullanılırken aynı parolanın üretilmesi için çekirdek olarak kullanılan parola parolası |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, varsayılan:: TimeSpan:: FromMinutes (5)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Disk boş alana yakın olduğunda raporlama sistem durumu olayı için disk alanı denetimi arasındaki zaman aralığı. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, varsayılan:: TimeSpan:: FromMinutes (15)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Diskte yeterli alan olmadığında raporlama sistem durumu olayı için disk alanı denetimi arasındaki zaman aralığı. |
|Enableımatorehealthreporting |bool, varsayılan değer doğru |Statik|Dosya deposu hizmetinin durumunu rapor etmesi gerekip gerekmediğini öğrenmek için yapılandırma. |
|Freediskspacenocertificate Ationsizeınkb|int64, varsayılan 25\*1024 |Dinamik|Sistem durumu uyarısının altında yer alan boş disk alanı boyutu. Bu yapılandırma ve Freediskspacenocertificate Ationthresholdpercentage yapılandırmasının minimum değeri, sistem durumu uyarısının gönderilmesini belirlemede kullanılır. |
|Freediskspacenocertificate Ationthresholdpercentage|Double, varsayılan değer 0,02 ' dir |Dinamik|Sistem durumu uyarısının gerçekleşebileceği boş disk alanı yüzdesi. Bu yapılandırma ve Freediskspacenotifiationınmb yapılandırmasının en düşük değeri, sistem durumu uyarısının gönderilmesini belirlemede kullanılır. |
|GenerateV1CommonNameAccount| bool, varsayılan değer doğru|Statik|Kullanıcı adı v1 oluşturma algoritması ile bir hesap oluşturulup oluşturulmayacağını belirtir. Service Fabric sürüm 6,1 ' den başlayarak; v2 oluşturma ile bir hesap her zaman oluşturulur. V1 hesabı, v2 üretimini desteklemeyen sürümlerden/sürümüne yükseltmeler için gereklidir (6,1 ' dan önce).|
|MaxCopyOperationThreads | Uint, varsayılan 0 ' dır |Dinamik| İkincil dosyanın birincil kopyadan Kopyalaya, en fazla paralel dosya sayısı. ' 0 ' = = çekirdek sayısı. |
|MaxFileOperationThreads | Uint, varsayılan değer 100 |Statik| Birincil içinde FileOperations (kopyalama/taşıma) gerçekleştirmesine izin verilen paralel iş parçacığı sayısı üst sınırı. ' 0 ' = = çekirdek sayısı. |
|MaxRequestProcessingThreads | Uint, varsayılan değer 200 |Statik|Birincil içindeki istekleri işlemeye izin verilen en fazla paralel iş parçacığı sayısı. ' 0 ' = = çekirdek sayısı. |
|MaxSecondaryFileCopyFailureThreshold | Uint, varsayılan değer 25 ' tir|Dinamik|Vermeden önce ikincil üzerinde en fazla dosya kopyalama yeniden deneme sayısı. |
|MaxStoreOperations | Uint, varsayılan değer 4096 |Statik|Birincil üzerinde izin verilen en fazla paralel depolama işlemi işlemi sayısı. ' 0 ' = = çekirdek sayısı. |
|NamingOperationTimeout |Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Adlandırma işlemini gerçekleştirmeye yönelik zaman aşımı. |
|Rol Yaccountntlmpasswordsecret | SecureString, varsayılan boş |Statik| NTLM kimlik doğrulaması kullanılırken aynı parolanın üretilmesi için çekirdek olarak kullanılan parola parolası. |
|PrimaryAccountNTLMX509StoreLocation | dize, varsayılan değer "LocalMachine"|Statik| NTLM kimlik doğrulaması kullanılırken, sımlar Yaccountntlmpasswordsecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının depolama konumu. |
|PrimaryAccountNTLMX509StoreName | dize, varsayılan değer "MY"|Statik| NTLM kimlik doğrulaması kullanılırken, eldeki-üst olan Yaccountntlmpasswordsecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının mağaza adı. |
|PrimaryAccountNTLMX509Thumbprint | dize, varsayılan değer ""|Statik|NTLM kimlik doğrulaması kullanılırken, Sımslik Yaccountntlmpasswordsecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının parmak izi. |
|PrimaryAccountType | dize, varsayılan değer "" |Statik|Asıl hizmetin, FileStoreService paylaşımlarına ACL 'ye yönelik birincil AccountType. |
|Her ne kadar Yaccountusername | dize, varsayılan değer "" |Statik|Asıl hesabın birincil hesap Kullanıcı adı, FileStoreService paylaşımlarına ACL 'ye ulaştı. |
|Her ne kadar Yaccountuserpassword | SecureString, varsayılan boş |Statik|Asıl hesabın birincil hesap parolası, FileStoreService paylaşımlarına ACL 'ye kadar. |
|QueryOperationTimeout | Saniye cinsinden süre, varsayılan değer 60 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Sorgu işlemini gerçekleştirmek için zaman aşımı. |
|SecondaryAccountNTLMPasswordSecret | SecureString, varsayılan boş |Statik| NTLM kimlik doğrulaması kullanılırken aynı parolanın üretilmesi için çekirdek olarak kullanılan parola parolası. |
|SecondaryAccountNTLMX509StoreLocation | dize, varsayılan değer "LocalMachine" |Statik|NTLM kimlik doğrulaması kullanılırken SecondaryAccountNTLMPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının depolama konumu. |
|SecondaryAccountNTLMX509StoreName | dize, varsayılan değer "MY" |Statik|NTLM kimlik doğrulaması kullanılırken SecondaryAccountNTLMPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının mağaza adı. |
|SecondaryAccountNTLMX509Thumbprint | dize, varsayılan değer ""| Statik|NTLM kimlik doğrulaması kullanılırken SecondaryAccountNTLMPasswordSecret üzerinde HMAC oluşturmak için kullanılan x509 sertifikasının parmak izi. |
|SecondaryAccountType | dize, varsayılan değer ""|Statik| Asıl hizmetin, FileStoreService paylaşımlarında ACL 'ye yönelik ikincil AccountType. |
|SecondaryAccountUserName | dize, varsayılan değer ""| Statik|Asıl hizmetin, FileStoreService paylaşımlarına ACL 'ye kadar olan ikincil hesap Kullanıcı adı. |
|SecondaryAccountUserPassword | SecureString, varsayılan boş |Statik|Birincil hesabın ikincil hesap parolası, FileStoreService paylaşımlarına ACL 'ye kadar. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, varsayılan değer 500|Dinamik|Dosya kopyalama yeniden deneme gecikmesi (milisaniye cinsinden).|
|Usechunkcontenıntransportmessage|bool, varsayılan değer doğru|Dinamik|V 6.4 ' de tanıtılan karşıya yükleme protokolünün yeni sürümünü kullanmaya yönelik bayrak. Bu protokol sürümü, önceki sürümlerde kullanılan SMB protokolüne kıyasla daha iyi performans sağlayan görüntü deposuna dosya yüklemek için Service Fabric aktarımını kullanır. |

## <a name="healthmanager"></a>HealthManager

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, varsayılan değer false |Statik|Küme durumu değerlendirme ilkesi: uygulama başına tür sistem durumu değerlendirmesi ' ni etkinleştirin. |
|Maxmülatednumberofentityhealthreports|Int, varsayılan değer 100 ' dir |Dinamik|Bir varlığın, izleyicinin sistem durumu raporlama mantığı hakkında kaygıları yapmadan önce sahip olduğu en yüksek sistem durumu raporu sayısı. Her sistem durumu varlığının görece az sayıda sistem durumu raporu olması gerekir. Rapor sayısı bu sayının üzerinde olursa; izleme uygulamasıyla ilgili sorunlar olabilir. Varlık değerlendirildiğinde, çok fazla rapora sahip bir varlık bir uyarı sistem durumu raporundan işaretlenir. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, varsayılan değer false |Statik|Küme durumu değerlendirme ilkesi: uyarılar hata olarak değerlendirilir. |
|Maxyüztunhealthyapplications | int, varsayılan değer 0 ' dır |Statik|Küme durumu değerlendirme ilkesi: kümenin sağlıklı olması için, sağlıksız uygulamaların en yüksek yüzdesi. |
|Maxyüztunhealthyınodes | int, varsayılan değer 0 ' dır |Statik|Küme durumu değerlendirme ilkesi: kümenin sağlıklı olması için uygun olmayan en fazla düğüm yüzdesi. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Maxpercentdeltaunhealthyınodes|Int, varsayılan değer 10 ' dur|Statik|Küme yükseltme sistem durumu değerlendirme ilkesi: kümenin sağlıklı olması için izin verilen Delta düğümlerinin en yüksek yüzdesi |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, varsayılan değer 15 ' tir|Statik|Küme yükseltme sistem durumu değerlendirme ilkesi: bir yükseltme etki alanında, kümenin sağlıklı olması için izin verilen en fazla sağlıksız düğüm Delta yüzdesi |

## <a name="hosting"></a>Barındırma

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Tüm sayı, varsayılan değer 10 ' dur |Dinamik|Yeniden denemeden önce sistemin kaç kez etkinleşmesinin başarısız olması |
|Activationmaxretryınterval |Saniye cinsinden süre, varsayılan değer 300 ' dir |Dinamik|Her sürekli etkinleştirme hatasında, sistem etkinleştirmeyi ActivationMaxFailureCount 'a kadar yeniden dener. Activationmaxretryınterval, her etkinleştirme hatasından sonra yeniden denemeden önce bekleme süresi aralığını belirtir |
|Activationretrybackoffınterval |Saniye cinsinden süre, varsayılan değer 5 ' tir |Dinamik|Her etkinleştirme hatasında geri alma aralığı; Her sürekli etkinleştirme hatasında, sistem etkinleştirmeyi MaxActivationFailureCount 'a kadar yeniden dener. Her denemeye yönelik yeniden deneme aralığı, sürekli etkinleştirme hatasının ve etkinleştirme geri dönme aralığının bir ürünüdür. |
|ActivationTimeout| TimeSpan, varsayılan:: TimeSpan:: FromSeconds (180)|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Uygulama etkinleştirme zaman aşımı; devre dışı bırakma ve yükseltme. |
|ApplicationHostCloseTimeout| TimeSpan, varsayılan:: TimeSpan:: FromSeconds (120)|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Otomatik olarak etkinleştirilmiş işlemlerde yapı çıkışı algılandığında; FabricRuntime kullanıcının ana bilgisayar (ApplicationHost) işlemindeki tüm çoğaltmaları kapatır. Bu, kapatma işlemi için zaman aşımı. |
| CnsNetworkPluginCnmUrlPort | wstring, varsayılan L "48080" | Statik | Azure CNP API URL 'si bağlantı noktası |
| CnsNetworkPluginCnsUrlPort | wstring, varsayılan L "10090" | Statik | Azure CNS URL bağlantı noktası |
|ContainerServiceArguments|dize, varsayılan "-H localhost: 2375-H npipe://"|Statik|Service Fabric (SF), Docker Daemon 'ı yönetir (win10 gibi Windows istemci makineler hariç). Bu yapılandırma, kullanıcının, başlatıldığında Docker Daemon 'a geçirilmesi gereken özel bağımsız değişkenler belirtmesini sağlar. Özel bağımsız değişkenler belirtildiğinde, '--PidFile ' bağımsız değişkeni dışında Docker altyapısına başka bir bağımsız değişken geçirmez Service Fabric. Bu nedenle, kullanıcılar kendi müşteri bağımsız değişkenlerinin bir parçası olarak '--PidFile ' bağımsız değişkenini belirtmemelidir. Ayrıca, özel bağımsız değişkenler, Service Fabric ile iletişim kurabilmesi için, Docker Daemon 'ın Windows 'da varsayılan ad kanalını (veya Linux üzerinde UNIX etki alanı yuvası) dinlediğinden emin olmalıdır.|
|Containerservicelogfilemaxsizeınkb|int, varsayılan değer 32768 ' dir|Statik|Docker kapsayıcıları tarafından oluşturulan günlük dosyasının maksimum dosya boyutu.  Yalnızca Windows.|
|Containerımagedownloadtimeout|int, saniye sayısı, varsayılan değer 1200 ' dir (20 dakika)|Dinamik|Görüntü indirmenin zaman aşımına uğramadan önce geçmesi gereken saniye sayısı.|
|Containerımatoskip|dize, dikey çizgi karakteriyle ayrılmış görüntü adları, varsayılan değer ""|Statik|Silinmemelidir bir veya daha fazla kapsayıcı görüntüsü adı.  Prunecontainerımages parametresi ile birlikte kullanılır.|
|Containerservicelogdosyaadıöneki|dize, varsayılan değer "sfcontainerlogs"|Statik|Docker kapsayıcıları tarafından oluşturulan günlük dosyaları için dosya adı öneki.  Yalnızca Windows.|
|ContainerServiceLogFileRetentionCount|Int, varsayılan değer 10 ' dur|Statik|Günlük dosyalarının üzerine yazılmadan önce Docker kapsayıcıları tarafından oluşturulan günlük dosyalarının sayısı.  Yalnızca Windows.|
|CreateFabricRuntimeTimeout|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (120)|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Sync FabricCreateRuntime çağrısı için zaman aşımı değeri |
|DefaultContainerRepositoryAccountName|dize, varsayılan değer ""|Statik|ApplicationManifest. xml dosyasında belirtilen kimlik bilgileri yerine varsayılan kimlik bilgileri kullanıldı |
|Defaultcontainerdepotorpassword|dize, varsayılan değer ""|Statik|ApplicationManifest. xml dosyasında belirtilen kimlik bilgileri yerine varsayılan parola kimlik bilgileri kullanıldı|
|Defaultcontainerdepotorpasswordtype|dize, varsayılan değer ""|Statik|Boş dize olmadığında, değer "şifrelenen" veya "SecretsStoreRef" olabilir.|
|Defaultdnssearchsonlt boş|bool, varsayılan değer FALSE|Statik|Varsayılan olarak, hizmet adı kapsayıcı Hizmetleri için SF DNS adına eklenir. Bu özellik, çözüm patika içinde varsayılan olarak SF DNS adına hiçbir şey eklenmediğinden bu davranışı sonlandırır.|
|DeploymentMaxFailureCount|Int, varsayılan değer 20 ' dir| Dinamik|Uygulama dağıtımı, bu uygulamanın düğümde dağıtılması başarısız olmadan önce DeploymentMaxFailureCount süreleri için yeniden denenecek.| 
|Deploymentmaxretryınterval| TimeSpan, varsayılan:: TimeSpan:: FromSeconds (3600)|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Dağıtım için en fazla yeniden deneme aralığı. Her sürekli hatada, yeniden deneme aralığı en az (Deploymentmaxretryınterval;) olarak hesaplanır. Sürekli hata sayısı * Deploymentretrybackoffınterval) |
|Deploymentretrybackoffınterval| TimeSpan, varsayılan:: TimeSpan:: FromSeconds (10)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Dağıtım hatası için geri dönüş aralığı. Her sürekli dağıtım hatasında sistem, dağıtımı MaxDeploymentFailureCount 'a kadar yeniden dener. Yeniden deneme aralığı, sürekli dağıtım hatasının ve dağıtım geri alma aralığının bir ürünüdür. |
|DisableContainers|bool, varsayılan değer FALSE|Statik|Kullanım dışı yapılandırma olan DisableContainerServiceStartOnContainerActivatorOpen yerine kapsayıcıları devre dışı bırakma yapılandırması |
|DisableDockerRequestRetry|bool, varsayılan değer FALSE |Dinamik| Varsayılan olarak, e-postayla gönderilen her http isteği için ' DockerRequestTimeout ' zaman aşımı ile gg (Docker davmeon) ile iletişim kurar. DD bu süre içinde yanıt vermezse; En üst düzey işlem hala devam ediyorsa, SF, isteği sonlandırır.  Hyperv kapsayıcısı ile; DD, kapsayıcıyı getirmek veya devre dışı bırakmak için bazen çok daha fazla zaman alabilir. Bu gibi durumlarda, istek zaman içinde SF perspektifinden zaman aşımına uğrar ve SF işlemi yeniden dener. Bazen bu, DD 'ye daha fazla basınç ekliyor gibi görünüyor. Bu yapılandırma, bu yeniden denemeyi devre dışı bırakıp DD 'nin yanıt vermesini beklemek için izin verir. |
|Dnsserverlisttwoıp 'Leri | bool, varsayılan değer FALSE | Statik | Bu bayraklar, sorunları aralıklı olarak çözmeye yardımcı olmak için yerel DNS sunucusunu iki kez ekler. |
| DoNotInjectLocalDnsServer | bool, varsayılan değer FALSE | Statik | Çalışma zamanının, kapsayıcılar için DNS sunucusu olarak yerel IP 'yi ekleme engeller. |
|EnableActivateNoWindow| bool, varsayılan değer FALSE|Dinamik| Etkinleştirilen işlem, herhangi bir konsol olmadan arka planda oluşturulur. |
|EnableContainerServiceDebugMode|bool, varsayılan değer doğru|Statik|Docker kapsayıcıları için günlüğü etkinleştirin/devre dışı bırakın.  Yalnızca Windows.|
|Enabledockerhealthcheckıntegration|bool, varsayılan değer doğru|Statik|Service Fabric sistem durumu raporu ile Docker HEALTHCHECK olaylarının tümleştirilmesine izin vermez |
|EnableProcessDebugging|bool, varsayılan değer FALSE|Dinamik| Hata ayıklayıcı altında uygulama ana bilgisayarlarının başlatılmasını sunar |
|EndpointProviderEnabled| bool, varsayılan değer FALSE|Statik| Uç nokta kaynaklarının dokuya yönetimine izin vermez. FabricNode 'da başlangıç ve bitiş uygulaması bağlantı noktası aralığının belirtimini gerektirir. |
|FabricContainerAppsEnabled| bool, varsayılan değer FALSE|Statik| |
|FirewallPolicyEnabled|bool, varsayılan değer FALSE|Statik| ServiceManifest 'de belirtilen açık bağlantı noktalarıyla uç nokta kaynakları için güvenlik duvarı bağlantı noktalarını açmaya izin vermez |
|GetCodePackageActivationContextTimeout|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (120)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. CodePackageActivationContext çağrılarının zaman aşımı değeri. Bu, geçici hizmetler için geçerli değildir. |
|GovernOnlyMainMemoryForProcesses|bool, varsayılan değer FALSE|Statik|Kaynak İdaresi 'nin varsayılan davranışı, işlemin kullandığı toplam bellek miktarı (RAM + takas) için Memorınmb MB cinsinden belirtilen sınırı koymaya yönelik olur. Sınır aşılırsa; işlem OutOfMemory özel durumu alır. Bu parametre true olarak ayarlanırsa; sınır yalnızca bir işlemin kullanacağı RAM belleği miktarına uygulanır. Bu sınır aşılırsa; Bu ayar true ise, ardından işletim sistemi, ana belleği disk olarak değiştirir. |
|Ipproviderenabled|bool, varsayılan değer FALSE|Statik|IP adreslerinin yönetimine izin vermez. |
|Idefaultcontainerdepotorpasswordencrypted|bool, varsayılan değer FALSE|Statik|Defaultcontainerdepotorpassword şifreli olup olmadığı.|
|LinuxExternalExecutablePath|dize, varsayılan değer "/usr/bin/" |Statik|Düğümdeki dış çalıştırılabilir komutların birincil dizini.|
|NTLMAuthenticationEnabled|bool, varsayılan değer FALSE|Statik| Makineler arası işlemlerin güvenli bir şekilde iletişim kurabilmesi için diğer kullanıcılar olarak çalışan kod paketleri tarafından NTLM kullanma desteğini sunar. |
|NTLMAuthenticationPasswordSecret|SecureString, varsayılan:: SecureString ("")|Statik|, NTLM kullanıcıları için parola oluşturmak için kullanılan şifreli bir paroladır. NTLMAuthenticationEnabled true ise ayarlanmalıdır. Dağıtıcı tarafından doğrulanan. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, varsayılan:: TimeSpan:: FromMinutes (3)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Ortama özgü ayarlar, barındırmanın, FileStoreService NTLM yapılandırması için kullanılacak yeni sertifikaları taradığı düzenli aralıklarla yapılır. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, varsayılan:: TimeSpan:: FromMinutes (4)|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Sertifika ortak adlarını kullanarak NTLM kullanıcılarını yapılandırmaya yönelik zaman aşımı. Fılestoreservice paylaşımları için NTLM kullanıcıları gereklidir. |
|Prunecontainerımages|bool, varsayılan değer FALSE|Dinamik| Kullanılmayan uygulama kapsayıcısı görüntülerini düğümlerden kaldırın. Service Fabric kümesinden bir ApplicationType kaldırıldığında, bu uygulama tarafından kullanılan kapsayıcı görüntüleri, Service Fabric tarafından indirildiği düğümlerde kaldırılır. Ayıklama her saat çalışır, bu nedenle Kümeden kaldırılacak görüntüler için bir saate (ve görüntünün ayıklanması zaman) kadar sürebilir.<br>Service Fabric bir uygulamayla ilgili olan görüntüleri hiçbir şekilde indirmez veya kaldırmaz.  El ile indirilen veya başka türlü ilgisiz görüntülerin açıkça kaldırılması gerekir.<br>Silinmemelidir olmayan görüntüler Containerımatoskip parametresinde belirtilebilir.| 
|RegisterCodePackageHostTimeout|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (120)|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. FabricRegisterCodePackageHost eşitleme çağrısının zaman aşımı değeri. Bu, yalnızca FWP gibi çok sayıda kod paketi uygulama konakları için geçerlidir |
|RequestTimeout|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (30)|Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Bu, fabrikada kayıt gibi çeşitli barındırma ile ilgili işlemler için kullanıcının uygulama konağı ve doku işlemi arasındaki iletişimin zaman aşımını temsil eder; çalışma zamanı kaydı. |
|RunAsPolicyEnabled| bool, varsayılan değer FALSE|Statik| Kod paketlerinin, yapı işleminin çalıştırıldığı Kullanıcı dışında yerel kullanıcı olarak çalıştırılmasını mümkün. Bu ilke dokusunun etkinleştirilmesi için SISTEM olarak veya Seatamaprimarytokenprivilege ' i içeren Kullanıcı olarak çalışıyor olmalıdır. |
|ServiceFactoryRegistrationTimeout| TimeSpan, varsayılan:: TimeSpan:: FromSeconds (120)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Eşitleme yazmacı (durum bilgisiz/durum bilgisi olmayan) ServiceFactory çağrısı için zaman aşımı değeri |
|ServiceTypeDisableFailureThreshold |Tam sayı, varsayılan 1 ' dir |Dinamik|Bu, FailoverManager 'a (FM) Bu düğümdeki hizmet türünü devre dışı bırakmak ve yerleştirme için farklı bir düğüm denemek üzere bildirim alındıktan sonra oluşan hata sayısı eşiğine göre belirlenir. |
|ServiceTypeDisableGraceInterval|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (30)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Hizmet türünün devre dışı bırakılabileceği zaman aralığı |
|Servicetyperyumuristrationtimeout |Saniye cinsinden süre, varsayılan değer 300 ' dir |Dinamik|ServiceType dokuya kaydettirilme için izin verilen maksimum süre |
|UseContainerServiceArguments|bool, varsayılan değer doğru|Statik|Bu yapılandırma, barındırma ile bağımsız değişkenleri (config ContainerServiceArguments içinde belirtilen) Docker Daemon 'a geçirmeyi atlayıp barındırmasını söyler.|

## <a name="httpgateway"></a>HttpGateway

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, varsayılan değer 50 |Statik| Http sunucusu kuyruğuna gönderilecek okuma sayısı. Bu, HttpGateway tarafından memnun olabilecek eşzamanlı isteklerin sayısını denetler. |
|Httpgatewayhealthreportsendınterval |Saniye cinsinden süre, varsayılan değer 30 ' dur |Statik|Zaman aralığı değerini saniye cinsinden belirtin. Http ağ geçidinin, sistem durumu yöneticisine birikmiş sistem durumu raporları gönderdiği Aralık. |
|HttpStrictTransportSecurityHeader|dize, varsayılan değer ""|Dinamik| HttpGateway tarafından gönderilen her yanıta dahil edilecek HTTP katı taşıma güvenliği üst bilgi değerini belirtin. Boş dizeye ayarlandığında; Bu üst bilgi, ağ geçidi yanıtında yer olmayacaktır.|
|IsEnabled|Bool, varsayılan değer false |Statik| HttpGateway 'i etkinleştirilir/devre dışı bırakır. HttpGateway varsayılan olarak devre dışıdır. |
|MaxEntityBodySize |Uint, varsayılan değer 4194304 |Dinamik|Bir http isteğinden beklenen en büyük gövde boyutunu verir. Varsayılan değer 4 MB 'dir. Bu değer > bir gövdeye sahip olması durumunda httpgateway bir istek başarısız olur. En küçük okuma öbek boyutu 4096 bayttır. Bu nedenle > = 4096 olmalıdır. |

## <a name="imagestoreservice"></a>Imatoreservice

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Etkin |Bool, varsayılan değer false |Statik|Imatoreservice için etkinleştirilen bayrak. Varsayılan: false |
|MinReplicaSetSize | Int, varsayılan 3 ' dir |Statik|Imareplicatoreservice için MinReplicaSetSize. |
|Placementkýsýtlamalarý | dize, varsayılan değer "" |Statik| Imatoreservice için Placementkýsýtlamalarý. |
|QuorumLossWaitDuration | Saniye cinsinden süre, varsayılan değer MaxValue |Statik| Zaman aralığı değerini saniye cinsinden belirtin. Imatoreservice için QuorumLossWaitDuration. |
|ReplicaRestartWaitDuration | Saniye cinsinden süre, varsayılan değer 60,0 \* 30 ' dur |Statik|Zaman aralığı değerini saniye cinsinden belirtin. Imatoreservice için ReplicaRestartWaitDuration. |
|StandByReplicaKeepDuration | Saniye cinsinden süre, varsayılan değer 3600,0 \* 2 ' dir |Statik| Zaman aralığı değerini saniye cinsinden belirtin. Imatoreservice için StandByReplicaKeepDuration. |
|TargetReplicaSetSize | Int, varsayılan değer 7 ' dir |Statik|Imatoreservice için TargetReplicaSetSize. |

## <a name="ktllogger"></a>Ktlgünlükçü

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |int, varsayılan değer 1 ' dir |Dinamik|Bellek ayarlarının otomatik ve dinamik olarak yapılandırılıp yapılandırılmadığını gösteren bayrak. Sıfır ise, bellek yapılandırma ayarları doğrudan kullanılır ve sistem koşullarına göre değişmez. Daha sonra, bellek ayarları otomatik olarak yapılandırılır ve sistem koşullarına bağlı olarak değişebilir. |
|Maximumdestagingwriteoutstandingınkb | int, varsayılan değer 0 ' dır |Dinamik|Paylaşılan günlüğün adanmış günlüğün önüne çıkmasına izin veren KB sayısı. Sınır olmadığını göstermek için 0 kullanın.
|Sharedlogıd |dize, varsayılan değer "" |Statik|Paylaşılan günlük kapsayıcısı için benzersiz GUID. Fabric veri kökü altında varsayılan yol kullanılıyorsa "" kullanın. |
|SharedLogPath |dize, varsayılan değer "" |Statik|Paylaşılan günlük kapsayıcısının yerleştirileceği konuma yol ve dosya adı. Fabric veri kökü altında varsayılan yolu kullanmak için "" kullanın. |
|Sharedlogsizeınmb |Int, varsayılan değer 8192 ' dir |Statik|Paylaşılan günlük kapsayıcısında ayrılacak MB sayısı. |
|Sharedlogthrottlelimitınyüztused|int, varsayılan değer 0 ' dır | Statik | Paylaşılan günlük kullanımının azaltmasını sağlayacak kullanım yüzdesi. Değer 0 ile 100 arasında olmalıdır. 0 değeri, varsayılan yüzde değerini kullanmayı gerektirir. 100 değeri hiç azaltma gerektirmez. 1 ile 99 arasında bir değer, üzerinde azaltma gerçekleştirilecek günlük kullanımının yüzdesini belirtir; Örneğin, paylaşılan günlük 10 GB ise ve değer 90 ise, daraltma işlemi 9GB kullanımda olduğunda meydana gelir. Varsayılan değer kullanılması önerilir.|
|WriteBufferMemoryPoolMaximumInKB | int, varsayılan değer 0 ' dır |Dinamik|Yazma arabelleği bellek havuzunun büyümesine izin veren KB sayısı. Sınır olmadığını göstermek için 0 kullanın. |
|Writebuffermemorypoolminimumınkb |Int, varsayılan değer 8388608 ' dir |Dinamik|Yazma arabelleği bellek havuzu için başlangıçta ayrılacak KB sayısı. Sınır olmadığını belirtmek için 0 kullanın varsayılan olarak Sharedlogsizeınmb ile tutarlı olmalıdır. |

## <a name="managedidentitytokenservice"></a>Managedıdentitytokenservice
| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|IsEnabled|bool, varsayılan değer FALSE|Statik|Kümedeki yönetilen kimlik belirteci hizmetinin varlığını ve durumunu denetleyen bayrak; bu, Service Fabric uygulamaların yönetilen kimlik işlevlerinin kullanılması için bir önkoşuldur.|

## <a name="management"></a>Yönetim

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Automaticunprovisionınterval|TimeSpan, varsayılan:: TimeSpan:: FromMinutes (5)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Otomatik uygulama türü Temizleme sırasında uygulama türünün kaydını silmek için izin verilen temizleme aralığı.|
|Azurestoraymagemaxconnections | Int, varsayılan değer 5000 ' dir |Dinamik|Azure depolama 'ya yönelik eşzamanlı bağlantı sayısı üst sınırı. |
|Azurestomingemaxworkerthreads | int, varsayılan değer 25 ' tir |Dinamik|Paralel olarak en fazla çalışan iş parçacığı sayısı. |
|Azurestooygeoperationtimeout | Saniye cinsinden süre, varsayılan değer 6000 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. XSTORE işleminin tamamlanabilmesi için zaman aşımı. |
|CleanupApplicationPackageOnProvisionSuccess|bool, varsayılan değer FALSE |Dinamik|Başarılı bir sağlama sırasında uygulama paketi otomatik temizlemeyi etkinleştirilir veya devre dışı bırakır. |
|CleanupUnusedApplicationTypes|bool, varsayılan değer FALSE |Dinamik|Bu yapılandırma etkinleştirilirse, kullanılmamış olan en son üç sürümü atlayarak kullanılmayan uygulama türü sürümlerinin kaydını otomatik olarak kaldırmak için izin verir. Otomatik Temizleme, bu belirli uygulama türü için başarılı sağlama sonunda tetiklenir ve ayrıca tüm uygulama türleri için günde bir kez düzenli olarak çalışır. Atlanacak kullanılmayan sürüm sayısı "MaxUnusedAppTypeVersionsToKeep" parametresi kullanılarak yapılandırılabilir. |
|DisableChecksumValidation | Bool, varsayılan değer false |Statik| Bu yapılandırma, uygulama sağlama sırasında sağlama toplamı doğrulamasını etkinleştirmemizi veya devre dışı bırakmanızı sağlar. |
|DisableServerSideCopy | Bool, varsayılan değer false |Statik|Bu yapılandırma, uygulama sağlama sırasında Imate uygulama paketinin sunucu tarafı kopyasını etkinleştirilir veya devre dışı bırakır. |
|Imagecachingenabled | Bool, varsayılan değer doğru |Statik|Bu yapılandırma, önbelleğe almayı etkinleştirmemizi veya devre dışı bırakmanızı sağlar. |
|Imagestoreconnectionstring |SecureString |Statik|Imabir için köke yönelik bağlantı dizesi. |
|Imatoanımsat ımumtransferbps | Int, varsayılan değer 1024 ' dir |Dinamik|Küme ve ımafer arasındaki en düşük aktarım hızı. Bu değer, dış ımatikten erişirken zaman aşımını belirlemede kullanılır. Bu değeri yalnızca küme ve ımaver arasındaki gecikme süresi yüksekse, kümenin dış ımak'ten indirilmesi için daha fazla zaman tanımak istiyorsanız değiştirin. |
|MaxUnusedAppTypeVersionsToKeep | Int, varsayılan 3 ' dir |Dinamik|Bu yapılandırma, temizleme için atlanacak kullanılmayan uygulama türü sürümlerinin sayısını tanımlar. Bu parametre yalnızca CleanupUnusedApplicationTypes parametresi etkinse geçerlidir. |


## <a name="metricactivitythresholds"></a>Metricactivityeşikleri
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, varsayılan değer None|Dinamik|Kümedeki ölçümler için Metricactivityeşikleri kümesini belirler. MaxNodeLoad, Metricactivityeşiklerinden fazlaysa, Dengeleme işlemi çalışır. Birleştirme ölçümleri için Service Fabric düğümü boş olarak kabul edecek olan veya bu değere eşit olan yük miktarını tanımlar |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, varsayılan değer None|Dinamik|Kümedeki ölçümler için MetricBalancingThresholds kümesini belirler. MaxNodeLoad/minNodeLoad değeri MetricBalancingThresholds ' den büyükse, Dengeleme işlemi çalışır. En az bir FD veya UD 'de maxNodeLoad/minNodeLoad, MetricBalancingThresholds değerinden küçükse birleştirme işlemi çalışacaktır. |

## <a name="metricloadstickinessforswap"></a>Metricloadkıkinessforswap
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, varsayılan değer None|Dinamik|Bir yükün, takas edildiğinde çoğaltma ile birlikte bulunan bir kısmının 0 arasında (yükleme, çoğaltma ile kontrol etmez) ve 1 (çoğaltma ile varsayılan olarak yükle) arasında değer aldığını belirler |

## <a name="namingservice"></a>NamingService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |int, varsayılan değer 0 ' dır |Statik|Adlandırma ağ geçidinde LRU hizmeti Açıklama önbelleğinde tutulan en fazla giriş sayısı (sınır olmaması için 0 olarak ayarlanır). |
|MaxClientConnections |Int, varsayılan değer 1000 ' dir |Dinamik|Ağ Geçidi başına izin verilen en yüksek istemci bağlantısı sayısı. |
|MaxFileOperationTimeout |Saniye cinsinden süre, varsayılan değer 30 ' dur |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Dosya depolama hizmeti işlemi için izin verilen en büyük zaman aşımı süresi. Daha büyük bir zaman aşımı belirten istekler reddedilir. |
|Maxındexedemptypartitions |Int, varsayılan değer 1000 ' dir |Dinamik|Yeniden bağlanan istemcileri eşitlemek için bildirim önbelleğinde dizinli kalacak en fazla boş bölüm sayısı. Bu sayının üzerindeki boş bölümler, artan arama sürümü sırasıyla dizinden kaldırılır. İstemcilerin yeniden bağlanması, hala unutulan boş bölüm güncelleştirmelerini eşitleyebilir ve alabilir; Ancak eşitleme protokolü daha pahalı hale gelir. |
|MaxMessageSize |Int, varsayılan 4\*1024\*1024 |Statik|Adlandırma kullanılırken istemci düğümü iletişimi için en büyük ileti boyutu. DOS saldırısı alleviation; Varsayılan değer 4 MB 'dir. |
|MaxNamingServiceHealthReports | Int, varsayılan değer 10 ' dur |Dinamik|Depolama hizmetini adlandıran en fazla yavaş işlem sayısı, tek seferde sağlıksız bir şekilde rapor verebilir. 0 ise; Tüm yavaş işlemler gönderilir. |
|MaxOperationTimeout |Saniye cinsinden süre, varsayılan değer 600 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. İstemci işlemlerinde izin verilen en büyük zaman aşımı süresi. Daha büyük bir zaman aşımı belirten istekler reddedilir. |
|MaxOutstandingNotificationsPerClient |Int, varsayılan değer 1000 ' dir |Dinamik|İstemci kaydı, ağ geçidi tarafından zorla kapatılmadan önce bekleyen en fazla bildirim sayısı. |
|MinReplicaSetSize | Int, varsayılan 3 ' dir |Izin verilmiyor| Bir güncelleştirmeyi tamamlamaya yönelik yazmak için gereken Adlandırma Hizmeti çoğaltma sayısı alt sınırı. Sistemde bu değerden daha az çoğaltma varsa, güvenilirlik sistemi çoğaltmalar geri yüklenene kadar Adlandırma Hizmeti deposundaki güncelleştirmeleri reddeder. Bu değer asla TargetReplicaSetSize 'den daha fazla olmamalıdır. |
|PartitionCount |Int, varsayılan 3 ' dir |Izin verilmiyor|Oluşturulacak Adlandırma Hizmeti deposunun bölüm sayısı. Her bölüm, dizinine karşılık gelen tek bir bölüm anahtarına sahiptir; Bu nedenle bölüm anahtarları [0; PartitionCount] var. Adlandırma Hizmeti bölümlerinin artırılması, herhangi bir yedekleme çoğaltması kümesi tarafından tutulan ortalama veri miktarını azaltarak Adlandırma Hizmeti gerçekleştirebildiği ölçeği artırır; kaynakların kullanımının artmasıyla (PartitionCount * ReplicaSetSize hizmet çoğaltmalarının tutulması gerektiğinden).|
|Placementkýsýtlamalarý | dize, varsayılan değer "" |Izin verilmiyor| Adlandırma Hizmeti için yerleştirme kısıtlaması. |
|QuorumLossWaitDuration | Saniye cinsinden süre, varsayılan değer MaxValue |Izin verilmiyor| Zaman aralığı değerini saniye cinsinden belirtin. Adlandırma Hizmeti, çekirdek kaybına geldiğinde Bu süreölçer başlar. Bu süre sona erdiğinde, FM çoğaltmaları kayıp olarak kabul eder; ve çekirdeği kurtarmaya çalışır. Bu, veri kaybına neden olabilir. |
|Repairınterval | Saniye cinsinden süre, varsayılan değer 5 ' tir |Statik| Zaman aralığı değerini saniye cinsinden belirtin. Yetkili sahibi ve ad sahibi arasındaki adlandırma tutarsızlığı onarımın başlayacağı Aralık. |
|ReplicaRestartWaitDuration | Saniye cinsinden süre, varsayılan değer (60,0 * 30)|Izin verilmiyor| Zaman aralığı değerini saniye cinsinden belirtin. Bir Adlandırma Hizmeti çoğaltması kaldığında; Bu süreölçer başlar. Süresi dolmuşsa, FM, kapatılmış çoğaltmaları değiştirmeye başlar (henüz kaybedilmez). |
|ServiceDescriptionCacheLimit | int, varsayılan değer 0 ' dır |Statik| Adlandırma deposu hizmetindeki LRU hizmeti Açıklama önbelleğinde tutulan en fazla giriş sayısı (sınır olmaması için 0 olarak ayarlanır). |
|Servicenocertificate zaman aşımı |Saniye cinsinden süre, varsayılan değer 30 ' dur |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. İstemciye hizmet bildirimleri sunarken kullanılan zaman aşımı. |
|StandByReplicaKeepDuration | Saniye cinsinden süre, varsayılan değer 3600,0 * 2 ' dir |Izin verilmiyor| Zaman aralığı değerini saniye cinsinden belirtin. Bir Adlandırma Hizmeti çoğaltma bir aşağı durumdan geri geldiğinde zaten değiştirilmiş olabilir. Bu süreölçer, bir FM 'in bekleme çoğaltmasını iptal etmeden önce ne kadar süreyle tutacağına ilişkin süreyi belirler. |
|TargetReplicaSetSize |Int, varsayılan değer 7 ' dir |Izin verilmiyor|Adlandırma Hizmeti deposunun her bölümü için çoğaltma kümesi sayısı. Çoğaltma kümesi sayısının artırılması Adlandırma Hizmeti deposundaki bilgilerin güvenilirlik düzeyini artırır; düğüm hatalarının sonucu olarak bilgilerin kaybolacağı değişikliği azaltma; Windows Fabric daha fazla yükün ve adlandırma verilerinde güncelleştirmeler gerçekleştirmek için gereken süre miktarının bir maliyetinde.|

## <a name="nodebufferpercentage"></a>Nohata ayıklama Fferpercentage
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, varsayılan değer None|Dinamik|Ölçüm adı başına düğüm kapasitesi yüzdesi; Yük devretme durumu için bir düğümde bir boş yer tutmak üzere bir arabellek olarak kullanılır. |

## <a name="nodecapacities"></a>Nodekapasiteler

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statik|Farklı ölçümler için düğüm kapasiteleri koleksiyonu. |

## <a name="nodedomainids"></a>Nodedomainıds

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup |Nodefaultdomainıdcollection |Statik|Bir düğümün ait olduğu hata etki alanlarını açıklar. Hata etki alanı, veri merkezindeki düğümün konumunu açıklayan bir URI aracılığıyla tanımlanır.  Hata etki alanı URI 'Leri FD:/FD/arkasından bir URI yol kesimi biçimindedir.|
|Upgradedomainıd |dize, varsayılan değer "" |Statik|Bir düğümün ait olduğu yükseltme etki alanını açıklar. |

## <a name="nodeproperties"></a>NodeProperties

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statik|Düğüm özellikleri için dize anahtar-değer çiftleri koleksiyonu. |

## <a name="paas"></a>PaaS

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Lclusterıd |dize, varsayılan değer "" |Izin verilmiyor|Yapılandırma koruması için doku tarafından kullanılan x509 sertifika deposu. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Sayacını |Dize | Dinamik |Toplanacak performans sayaçlarının virgülle ayrılmış listesi. |
|IsEnabled |Bool, varsayılan değer doğru | Dinamik |Bayrak, yerel düğümdeki performans sayacı koleksiyonunun etkinleştirilip etkinleştirilmediğini belirtir. |
|Maxcounterbinaryfilesizeınmb |int, varsayılan değer 1 ' dir | Dinamik |Her performans sayacı ikili dosyası için en büyük boyut (MB cinsinden). |
|Newcounterbinaryfilecreationıntervalınminutes |Int, varsayılan değer 10 ' dur | Dinamik |Yeni bir performans sayacı ikili dosyasının oluşturulması için geçmesi gereken en uzun Aralık (saniye cinsinden). |
|Samplingıntervalınseconds |Int, varsayılan değer 60 ' dir | Dinamik |Toplanmakta olan performans sayaçları için örnekleme aralığı. |

## <a name="placementandloadbalancing"></a>Placementandloaddengeleme

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | int, varsayılan değer 0 ' dır | Dinamik|Benzeşim kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; negatif: yoksay. |
|ApplicationCapacityConstraintPriority | int, varsayılan değer 0 ' dır | Dinamik|Kapasite kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; negatif: yoksay. |
|Oto algılayıcısı Tavailablereso,|bool, varsayılan değer doğru|Statik|Bu yapılandırma, bu yapılandırma true olarak ayarlandığında, düğüm üzerindeki kullanılabilir kaynakların otomatik olarak algılanmasını tetikler (CPU ve bellek) ve kullanıcı hatalı düğüm kapasiteleri belirtse veya bu yapılandırma yanlış olarak ayarlanırsa,  Kullanıcı hatalı düğüm kapasiteleri belirtmeyen bir uyarı izleyin; Ancak bunlar düzeltilmeyecektir; kullanıcının, gerçekten sahip olduğu veya kapasitesi tanımsız olan > olarak belirtilen kapasiteye sahip olmasını istediği anlamına gelir; Sınırsız kapasite kabul eder |
|BalancingDelayAfterNewNode | Saniye cinsinden süre, varsayılan değer 120 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Yeni bir düğüm ekledikten sonra bu süre içinde etkinlikleri dengelemeyi başlatma. |
|BalancingDelayAfterNodeDown | Saniye cinsinden süre, varsayılan değer 120 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Düğüm azaltma olayından sonra bu süre içinde etkinlikleri dengelemeyi başlatmayın. |
|CapacityConstraintPriority | int, varsayılan değer 0 ' dır | Dinamik|Kapasite kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; negatif: yoksay. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, varsayılan değer 20 ' dir | Dinamik|Tanılama gerçekleştirilmeden ve sistem durumu uyarıları yayınlanmadan önce, Resourcedengeleyiciden çıkarılan hareketlerin kaç kez bırakılacağını tanımlar. Negatif: Bu koşul altına hiçbir uyarı yayınlanmadı. |
|ConstraintFixPartialDelayAfterNewNode | Saniye cinsinden süre, varsayılan değer 120 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Yeni bir düğüm eklendikten sonra bu süre içinde FaultDomain ve UpgradeDomain kısıtlama ihlallerini Düzelmeyin. |
|ConstraintFixPartialDelayAfterNodeDown | Saniye cinsinden süre, varsayılan değer 120 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Bir düğüm azaltma olayından sonra bu süre içinde FaultDomain ve UpgradeDomain kısıtlama ihlallerini Düzelmeyin. |
|ConstraintViolationHealthReportLimit | int, varsayılan değer 50 ' dir |Dinamik| Tanılama yürütülmesinden ve sistem durumu raporlarının yayılmasından önce, çoğaltmanın kaç kez kalıcı olarak düzeltilmelidir. |
|DetailedConstraintViolationHealthReportLimit | Int, varsayılan değer 200 ' dir |Dinamik| Tanılama gerçekleştirilmeden ve ayrıntılı sistem durumu raporlarının yayınlanmasından önce, çoğaltmanın kaç kez kalıcı olarak düzeltilme sayısını tanımlar. |
|DetailedDiagnosticsInfoListLimit | int, varsayılan değer 15 ' tir |Dinamik| Tanılamalarda kesilmadan önce dahil edilecek kısıtlama başına tanı girişi sayısını (ayrıntılı bilgilerle birlikte) tanımlar.|
|DetailedNodeListLimit | int, varsayılan değer 15 ' tir |Dinamik| Yerleştirilmemiş çoğaltma raporlarında kesilmesinden önce dahil edilecek kısıtlama başına düğüm sayısını tanımlar. |
|DetailedPartitionListLimit | int, varsayılan değer 15 ' tir |Dinamik| Tanılamada kesilmenin önüne eklenecek bir kısıtlamanın tanılama girişi başına bölüm sayısını tanımlar. |
|DetailedVerboseHealthReportLimit | Int, varsayılan değer 200 ' dir | Dinamik|Bir yerleştirilmemiş çoğaltmanın, ayrıntılı sistem durumu raporlarının yayınlanmasından önce kalıcı olarak yerleştirilme sayısını tanımlar. |
|Enforceuserservicemetrickapasiteleri|bool, varsayılan değer FALSE | Statik |Doku Hizmetleri korumasını izin vermez. Tüm Kullanıcı Hizmetleri bir iş nesnesi/cgroup altında bulunur ve belirtilen kaynak miktarıyla sınırlıdır. Bu, Kullanıcı işi nesnesinin oluşturulması/kaldırılması ve doku ana bilgisayarının açılması sırasında yapılan limitlerin ayarlanması olarak statik olması gerekir (FabricHost 'ın yeniden başlatılmasını gerektirir). |
|FaultDomainConstraintPriority | int, varsayılan değer 0 ' dır |Dinamik| Hata etki alanı kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; negatif: yoksay. |
|Globalmovementthrottlecountingınterval | Saniye cinsinden süre, varsayılan değer 600 ' dir |Statik| Zaman aralığı değerini saniye cinsinden belirtin. Etki alanı çoğaltma hareketleri başına izlenecek son aralığın uzunluğunu belirtin (GlobalMovementThrottleThreshold ile birlikte kullanılır). , Genel azaltmayı tamamen yok saymak için 0 olarak ayarlanabilir. |
|GlobalMovementThrottleThreshold | Uint, varsayılan değer 1000 |Dinamik| Son aralıktaki, Globalmovementthrottlecountingınterval tarafından belirtilen Dengeleme aşamasında izin verilen maksimum hareket sayısı. |
|Globalmovementthrottlethresholdfordengeleme | Uint, varsayılan 0 ' dır | Dinamik|Son aralıkta Globalmovementthrottlecountingınterval tarafından belirtilen, Dengeleme aşamasında izin verilen maksimum hareket sayısı. 0 sınır olmadığını gösterir. |
|Globalmovementthrottlethresholdforyerleştirmesini | Uint, varsayılan 0 ' dır |Dinamik| Globalmovementthrottlecountingınterval tarafından belirtilen geçmiş aralıkta yerleştirme aşamasında izin verilen maksimum hareket sayısı. 0 sınır olmadığını gösterir.|
|GlobalMovementThrottleThresholdPercentage|Double, varsayılan 0 ' dır|Dinamik|Globalmovementthrottlecountingınterval tarafından belirtilen son aralıktaki Dengeleme ve yerleştirme aşamalarında (kümedeki toplam çoğaltma sayısının yüzdesi olarak ifade edilir) izin verilen en fazla toplam taşıma sayısı. 0 sınır olmadığını gösterir. Hem bu hem de GlobalMovementThrottleThreshold belirtilmişse; daha sonra, daha fazla koruyucu sınırı kullanılır.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, varsayılan 0 ' dır|Dinamik|Globalmovementthrottlecountingınterval tarafından belirtilen son aralıktaki Dengeleme aşamasında izin verilen en fazla hareket sayısı (PLB içindeki toplam çoğaltma sayısının yüzdesi olarak ifade edilir). 0 sınır olmadığını gösterir. Hem bu hem de Globalmovementthrottlethresholdfordengelemeyi belirtilirse; daha sonra, daha fazla koruyucu sınırı kullanılır.|
|Inbuildkısıtlar Lingilişkili ölçüm | dize, varsayılan değer "" |Statik| Bu daraltma için ilişkili ölçüm adı. |
|Inbuildkısıtlar Lingenabled | Bool, varsayılan değer false |Dinamik| Yapı üzerinde azaltma özelliğinin etkinleştirilip etkinleştirilmeyeceğini belirleme. |
|Inbuildkısıtlar Lingglobalmaxvalue | int, varsayılan değer 0 ' dır |Dinamik|Derleme içi çoğaltmaların maxhayvan sayısı küresel olarak izin verilir. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, varsayılan değer false | Dinamik|Herhangi bir yük devretme birimi güncelleştirmesinin hızlı veya yavaş Dengeleme çalıştırmasını kesmesine mi olmayacağını belirler. FailoverUnit: oluşturulduysa/silinirse, belirtilen "false" Dengeleme çalıştırması kesintiye uğratılacaktır; çoğaltmaları eksik; Birincil çoğaltma konumu değiştirildi veya yineleme sayısı değişti. Dengeleme çalıştırması diğer durumlarda kesintiye uğramaz; FailoverUnit: ek çoğaltmalar içeriyorsa; herhangi bir çoğaltma bayrağı değiştirildi; yalnızca bölüm sürümü veya başka bir durum değiştirildi. |
|Minconstraintcheckınterval | Saniye cinsinden süre, varsayılan değer 1 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Art arda iki kısıtlama denetiminin önüne geçmesi gereken en az süreyi tanımlar. |
|MinLoadBalancingInterval | Saniye cinsinden süre, varsayılan değer 5 ' tir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Art arda iki dengelemenin önüne geçmesi gereken en az süreyi tanımlar. |
|Minplacementınterval | Saniye cinsinden süre, varsayılan değer 1 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Art arda iki yerleşimi yuvarlarken geçmesi gereken en az süreyi tanımlar. |
|Moveexistingreplicaforyerleştirme | Bool, varsayılan değer doğru |Dinamik|Yerleştirme sırasında var olan çoğaltmanın taşınacağını belirleyen ayar. |
|Movementperpartitionthrottlecountingınterval | Saniye cinsinden süre, varsayılan değer 600 ' dir |Statik| Zaman aralığı değerini saniye cinsinden belirtin. Her bölüm için çoğaltma taşımalarını izlemek üzere geçen aralığın uzunluğunu belirtin (MovementPerPartitionThrottleThreshold ile birlikte kullanılır). |
|MovementPerPartitionThrottleThreshold | Uint, varsayılan değer 50 |Dinamik| Bu bölümün çoğaltmaları için Dengeleme ile ilgili hareketlerin sayısı, geçmişte belirtilen aralıkta MovementPerFailoverUnitThrottleThreshold değerine ulaşıldığında veya aşılırsa, bir bölüm için Dengeleme ile ilgili bir hareket gerçekleşmeyecektir. Movementperpartitionthrottlecountingınterval. |
|Moveparenttofixaffinityihlal | Bool, varsayılan değer false |Dinamik| Bu ayar, üst çoğaltmaların, benzeşim kısıtlamalarını onarmak üzere taşınıp taşınamayacağını belirler.|
|PartiallyPlaceServices | Bool, varsayılan değer doğru |Dinamik| Kümedeki tüm hizmet çoğaltmalarının, kendileri için sınırlı uygun düğümlere "All veya Nothing" olarak yerleştirilip yerleştirilmeyeceğini belirler.|
|PlaceChildWithoutParent | Bool, varsayılan değer doğru | Dinamik|Üst çoğaltma yoksa alt hizmet çoğaltmasının yerleştirilebileceğini belirleyen ayar. |
|PlacementConstraintPriority | int, varsayılan değer 0 ' dır | Dinamik|Yerleşim kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; negatif: yoksay. |
|PlacementConstraintValidationCacheSize | Int, varsayılan değer 10000 ' dir |Dinamik| Hızlı doğrulama ve yerleştirme kısıtlama Ifadelerinin önbelleğe alma için kullanılan tablo boyutunu sınırlandırır. |
|PlacementSearchTimeout | Saniye cinsinden süre, varsayılan değer 0,5 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Hizmetler yerleştirilirken; bir sonuç döndürmeden önce en fazla bu kadar arama yapın. |
|PLBRefreshGap | Saniye cinsinden süre, varsayılan değer 1 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. PLB 'nin durumu yeniden yenilenene kadar geçmesi gereken minimum süreyi tanımlar. |
|PreferredLocationConstraintPriority | Int, varsayılan değer 2 ' dir| Dinamik|Tercih edilen konum kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; 2: iyileştirme; negatif: yoksay |
|Tercih edilen Yükselds|bool, varsayılan değer doğru|Dinamik|Zaten yükseltilen UDs 'ye geçmeyi tercih eden mantığı açar ve kapatır.|
|Preventgeçişli Entovercommıt | Bool, varsayılan değer false | Dinamik|Başlatılan taşımaların serbest bırakılacağına ilişkin kaynaklarda hemen PLMASıNı belirler. Varsayılan olarak; PLB, taşınmayı başlatabilir ve aynı düğüm üzerinde, geçici yerleştirme oluşturabileceğinden, üzerinde hareket edebilir. Bu parametrenin true olarak ayarlanması, bu tür fazla işleme ve isteğe bağlı birleştirme (aka placementWithMove) için devre dışı bırakılacak. |
|ScaleoutCountConstraintPriority | int, varsayılan değer 0 ' dır |Dinamik| Ölçek Genişletme sayısı kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; negatif: yoksay. |
|SwapPrimaryThrottlingAssociatedMetric | dize, varsayılan değer ""|Statik| Bu daraltma için ilişkili ölçüm adı. |
|SwapPrimaryThrottlingEnabled | Bool, varsayılan değer false|Dinamik| Takas birincili azaltma 'nın etkin olup olmadığını belirleme. |
|SwapPrimaryThrottlingGlobalMaxValue | int, varsayılan değer 0 ' dır |Dinamik| Küresel olarak izin verilen takas birincil çoğaltmalarının sayısı. |
|Tracecrmnedenler |Bool, varsayılan değer doğru |Dinamik|CRM tarafından verilen hareketleri işletimsel olaylar kanalına göre izlemenin yapılıp yapılmayacağını belirtir. |
|UpgradeDomainConstraintPriority | int, varsayılan değer 1 ' dir| Dinamik|Yükseltme etki alanı kısıtlamasının önceliğini belirler: 0: Hard; 1: geçici; negatif: yoksay. |
|UseMoveCostReports | Bool, varsayılan değer false | Dinamik|LB öğesine Puanlama işlevinin Cost öğesini yok saymasını söyler; daha iyi dengeli yerleştirme için büyük olasılıkla çok fazla sayıda hareketi ortaya çıkarır. |
|UseSeparateSecondaryLoad | Bool, varsayılan değer doğru | Dinamik|Ayar, farklı ikincil yük kullanıp kullanmayacağını belirler. |
|ValidatePlacementConstraint | Bool, varsayılan değer doğru |Dinamik| Bir hizmetin ServiceDescription 'ı güncelleştirilirken bir hizmetin PlacementConstraint ifadesinin doğrulanıp onaylanmayacağını belirtir. |
|ValidatePrimaryPlacementConstraintOnPromote| bool, varsayılan değer doğru |Dinamik|Bir hizmetin PlacementConstraint ifadesinin yük devretmede birincil tercih için değerlendirilip değerlendirilmeyeceğini belirtir. |
|VerboseHealthReportLimit | Int, varsayılan değer 20 ' dir | Dinamik|Bir çoğaltmanın bir sistem durumu uyarısı bildirilmesinden önce yerleştirilme sayısı (ayrıntılı sistem durumu raporlaması etkinse) tanımlar. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Saniye cinsinden süre, varsayılan değer 900 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Uygulama yükseltmesi sırasında kapanmakta olan çoğaltmaları olan hizmet konaklarını sonlandırmadan önce sistemin bekleyeceği süre.|
|FabricUpgradeMaxReplicaCloseDuration | Saniye cinsinden süre, varsayılan değer 900 ' dir |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Yapı yükseltmesi sırasında, sistemin yakın bir şekilde takılmış çoğaltmaları olan hizmet konaklarını sonlandırmadan önce bekleyeceği süre. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (120)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Sistemin kapalı durumda kalmış çoğaltmaları olan hizmet konaklarını sonlandırmadan önce bekleyeceği süre. Bu değer 0 olarak ayarlanırsa çoğaltmalar kapanmaz.|
|NodeDeactivationMaxReplicaCloseDuration | Saniye cinsinden süre, varsayılan değer 900 ' dir |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Düğümün devre dışı bırakılması sırasında kapanmakta olan çoğaltmaları olan hizmet konaklarını sonlandırmadan önce sistemin bekleyeceği süre. |
|Dönemsiz Apislowtraceınterval | Saniye cinsinden süre, varsayılan değer 5 dakikadır |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. Dönemapislowtraceınterval, API izleyicisinin yavaş API çağrılarının geri yükleneceği aralığı tanımlar. |
|ReplicaChangeRoleFailureRestartThreshold|Int, varsayılan değer 10 ' dur|Dinamik| Gir. Birincil yükseltme sırasında, otomatik risk azaltma eyleminden (çoğaltma yeniden başlatma) uygulanacak API hatalarının sayısını belirtin. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, varsayılan değer 2147483647 ' dir|Dinamik| Gir. Birincil yükseltme sırasında, uyarı durumu raporunun oluşturulmadan önce API hatası sayısını belirtin.|
|ServiceApiHealthDuration | Saniye cinsinden süre, varsayılan değer 30 dakikadır |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. ServiceApiHealthDuration, hizmet API 'sinin sağlıksız bir şekilde raporlamamız için ne kadar süre bekleyeceğini tanımlar. |
|ServiceReconfigurationApiHealthDuration | Saniye cinsinden süre, varsayılan değer 30 ' dur |Dinamik| Zaman aralığı değerini saniye cinsinden belirtin. ServiceReconfigurationApiHealthDuration, sağlıksız bir şekilde raporlamamız için bir hizmet API 'sinin ne kadar süre bekletireceğinizi tanımlar. Bu, kullanılabilirliği etkileyen API çağrıları için geçerlidir.|

## <a name="replication"></a>Çoğaltma
| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Batchval Gementınterval|TimeSpan, varsayılan:: TimeSpan:: FromMilliseconds (15)|Statik|Zaman aralığı değerini saniye cinsinden belirtin. Bir onayı geri göndermeden önce çoğaltıcı tarafından bir işlem alındıktan sonra beklediği süreyi belirler. Bu süre boyunca alınan diğer işlemler, bildirimlerin tek bir ileti içinde geri gönderilmesini >, ağ trafiğini azaltmakta ve çoğaltıcı verimini potansiyel olarak azaltmaktadır.|
|MaxCopyQueueSize|uint, varsayılan değer 1024|Statik|Bu en büyük değer, çoğaltma işlemlerini tutan sıranın ilk boyutunu tanımlar. 2 ' nin üssü olması gerektiğini unutmayın. Çalışma zamanı sırasında sıranın bu boyut ile büyümesi, birincil ve ikincil çoğaltıcılar arasında kısıtlanacak.|
|MaxPrimaryReplicationQueueMemorySize|Uint, varsayılan 0 ' dır|Statik|Bu, birincil çoğaltma sırasının bayt cinsinden en yüksek değeridir.|
|MaxPrimaryReplicationQueueSize|uint, varsayılan değer 1024|Statik|Bu, birincil çoğaltma kuyruğunda mevcut olabilecek en fazla işlem sayısıdır. 2 ' nin üssü olması gerektiğini unutmayın.|
|MaxReplicationMessageSize|uint, varsayılan değer 52428800|Statik|Çoğaltma işlemlerinin en büyük ileti boyutu. Varsayılan değer 50 MB 'tır.|
|MaxSecondaryReplicationQueueMemorySize|Uint, varsayılan 0 ' dır|Statik|Bu, ikincil çoğaltma sırasının bayt cinsinden en yüksek değeridir.|
|MaxSecondaryReplicationQueueSize|uint, varsayılan değer 2048|Statik|Bu, ikincil çoğaltma kuyruğunda olabilecek en fazla işlem sayısıdır. 2 ' nin üssü olması gerektiğini unutmayın.|
|Queuehealthmonitoringınterval|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (30)|Statik|Zaman aralığı değerini saniye cinsinden belirtin. Bu değer, çoğaltma işlemi kuyruklarındaki herhangi bir uyarı/hata sistem durumu olayını izlemek için çoğaltıcı tarafından kullanılan zaman dilimini belirler. ' 0 ' değeri sistem durumu izlemeyi devre dışı bırakır |
|Queuehealthwarningatusage yüzdesi|uint, varsayılan değer 80|Statik|Bu değer, daha sonra yüksek kuyruk kullanımı hakkında uyarı bildirdiğimiz Çoğaltma sırası kullanımını (yüzde cinsinden) belirler. Bu, bir Queuehealthmonitoringınterval izin aralığından sonra yaptık. Kuyruk kullanımı, yetkisiz kullanım aralığındaki bu yüzdenin altına düşerse|
|Replicatoraydresi|dize, varsayılan değer "localhost: 0"|Statik|Windows Fabric çoğaltıcı tarafından, gönderme/alma işlemleri için diğer yinelemelerle bağlantı kurmak üzere kullanılan ' IP: Port ' dizesi biçimindeki uç nokta.|
|ReplicatorListenAddress|dize, varsayılan değer "localhost: 0"|Statik|Diğer çoğaltmalardan işlemleri almak için Windows Fabric çoğaltıcı tarafından kullanılan, ' IP: Port ' dizesi biçimindeki uç nokta.|
|ReplicatorPublishAddress|dize, varsayılan değer "localhost: 0"|Statik|Diğer yinelemelere işlem göndermek için Windows Fabric çoğaltıcı tarafından kullanılan, ' IP: Port ' dizesi biçimindeki uç nokta.|
|RetryInterval|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (5)|Statik|Zaman aralığı değerini saniye cinsinden belirtin. Bir işlem kaybolduğunda veya reddedildiğinde, bu süreölçer, çoğaltıcının işlemi göndermeyi ne sıklıkta yeniden denemesini belirler.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|IsEnabled|bool, varsayılan değer FALSE |Statik|Hizmetin kümede etkin olup olmadığını denetler. |

## <a name="runas"></a>Farklı

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|RunAsAccountName |dize, varsayılan değer "" |Dinamik|RunAs hesabı adını gösterir. Bu yalnızca "DomainUser" veya "ManagedServiceAccount" hesap türü için gereklidir. Geçerli değerler "etkialanı \ Kullanıcı" veya "user@domain". |
|RunAsAccountType|dize, varsayılan değer "" |Dinamik|RunAs hesabı türünü gösterir. Bu, herhangi bir RunAs bölümü için gereklidir. geçerli değerler şunlardır. "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|dize, varsayılan değer "" |Dinamik|RunAs hesabı parolasını gösterir. Bu yalnızca "DomainUser" hesap türü için gereklidir. |

## <a name="runas_dca"></a>RunAs_DCA

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|RunAsAccountName |dize, varsayılan değer "" |Dinamik|RunAs hesabı adını gösterir. Bu yalnızca "DomainUser" veya "ManagedServiceAccount" hesap türü için gereklidir. Geçerli değerler "etkialanı \ Kullanıcı" veya "user@domain". |
|RunAsAccountType|dize, varsayılan değer "" |Dinamik|RunAs hesabı türünü gösterir. Bu, herhangi bir RunAs bölümü için gereklidir. geçerli değerler şunlardır "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|dize, varsayılan değer "" |Dinamik|RunAs hesabı parolasını gösterir. Bu yalnızca "DomainUser" hesap türü için gereklidir. |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|RunAsAccountName |dize, varsayılan değer "" |Dinamik|RunAs hesabı adını gösterir. Bu yalnızca "DomainUser" veya "ManagedServiceAccount" hesap türü için gereklidir. Geçerli değerler "etkialanı \ Kullanıcı" veya "user@domain". |
|RunAsAccountType|dize, varsayılan değer "" |Dinamik|RunAs hesabı türünü gösterir. Bu, herhangi bir RunAs bölümü için gereklidir. geçerli değerler şunlardır "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|dize, varsayılan değer "" |Dinamik|RunAs hesabı parolasını gösterir. Bu yalnızca "DomainUser" hesap türü için gereklidir. |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|RunAsAccountName |dize, varsayılan değer "" |Dinamik|RunAs hesabı adını gösterir. Bu yalnızca "DomainUser" veya "ManagedServiceAccount" hesap türü için gereklidir. Geçerli değerler "etkialanı \ Kullanıcı" veya "user@domain". |
|RunAsAccountType|dize, varsayılan değer "" |Dinamik|RunAs hesabı türünü gösterir. Bu, herhangi bir RunAs bölümü için gereklidir. geçerli değerler şunlardır "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|dize, varsayılan değer "" |Dinamik|RunAs hesabı parolasını gösterir. Bu yalnızca "DomainUser" hesap türü için gereklidir. |

## <a name="security"></a>Güvenlik
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme Ilkesi**| **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|dize, varsayılan değer ""|Statik|Azure Kamu "https:\//login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml gibi varsayılan olmayan ortamlar için belirtilen varsayılan Azure Commercial AAD CERT uç nokta biçimi |
|AADClientApplication|dize, varsayılan değer ""|Statik|Yapı Istemcilerini temsil eden yerel Istemci uygulama adı veya KIMLIĞI |
|AADClusterApplication|dize, varsayılan değer ""|Statik|Kümeyi temsil eden Web API 'SI uygulama adı veya KIMLIĞI |
|AADLoginEndpoint|dize, varsayılan değer ""|Statik|Azure Kamu "https:\//login.microsoftonline.us" gibi varsayılan olmayan ortamlar için belirtilen AAD oturum açma uç noktası, varsayılan Azure Commercial |
|Aadtenantıd|dize, varsayılan değer ""|Statik|Kiracı KIMLIĞI (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, varsayılan değer FALSE|Dinamik|Parmak izi tarafından tanımlanan, zaman aşımına uğradı küme sertifikalarının kabul edilip edilmeyeceğini belirten bayrak yalnızca küme sertifikaları için geçerlidir; Bu nedenle, kümeyi canlı tutmak için. |
|Adminclientcertparmak Izleri|dize, varsayılan değer ""|Dinamik|Yönetici rolünde istemciler tarafından kullanılan sertifikaların parmak izleri. Bu, virgülle ayrılmış bir ad listesidir. |
|AADTokenEndpointFormat|dize, varsayılan değer ""|Statik|Azure Kamu "https:\//login.microsoftonline.us/{0}" gibi varsayılan olmayan ortamlar için belirtilen AAD belirteç uç noktası, varsayılan Azure Commercial |
|Adminclientclaim|dize, varsayılan değer ""|Dinamik|Yönetici istemcilerinden beklenen tüm olası talepler; Clientclaim ile aynı biçim; Bu liste, Clientclaim 'e dahili olarak eklenir; Bu nedenle, aynı girdileri Clientclaim 'e de eklemeniz gerekmez. |
|Adminclienentidentities|dize, varsayılan değer ""|Dinamik|Yönetici rolünde doku istemcilerinin Windows kimlikleri; ayrıcalıklı yapı işlemlerine yetki vermek için kullanılır. Bu, virgülle ayrılmış bir liste; her giriş bir etki alanı hesap adı veya grup adıdır. Kolaylık sağlaması için; Fabric. exe ' yi çalıştıran hesaba otomatik olarak yönetici rolü atanır; Bu nedenle, Grup hizmeti Fabricadministrators. |
|AppRunAsAccountGroupX509Folder|dize, varsayılan değer/Home/sfuser/sfusercerts |Statik|AppRunAsAccountGroup x509 sertifikalarının ve özel anahtarların bulunduğu klasör |
|Certificateexpıryıetetymargin|TimeSpan, varsayılan:: TimeSpan:: FromMinutes (43200)|Statik|Zaman aralığı değerini saniye cinsinden belirtin. Sertifika süre sonu için güvenlik kenar boşluğu; süre sonu bundan daha yakınsa, sertifika durumu raporu durumu Tamam iken uyarı olarak değişir. Varsayılan değer 30 gündür. |
|Certificatehealthreportingınterval|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (3600 * 8)|Statik|Zaman aralığı değerini saniye cinsinden belirtin. Sertifika sistem durumu raporlaması için aralığı belirtin; Varsayılan olarak 8 saat; 0 olarak ayarlandığında sertifika sistem durumu raporlaması devre dışı bırakılır |
|Clientcertparmak Izleri|dize, varsayılan değer ""|Dinamik|İstemciler tarafından kümeyle konuşmak için kullanılan sertifikaların parmak izleri; küme, bu gelen bağlantıyı Yetkilendir bağlantısını kullanır. Bu, virgülle ayrılmış bir ad listesidir. |
|ClientClaimAuthEnabled|bool, varsayılan değer FALSE|Statik|İstemcilerde talep tabanlı kimlik doğrulamasının etkinleştirilip etkinleştirilmediğini belirtir; Bu true ayarı açıkça Clienentrotaenabled olarak ayarlanır. |
|Clientclaim|dize, varsayılan değer ""|Dinamik|Ağ geçidine bağlanmak için istemcilerden beklenen tüm olası talepler. Bu bir ' veya ' listesidir: ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry... Her ClaimsEntry bir "ve" listesidir: ClaimType = ClaimValue & & ClaimType = ClaimValue & & ClaimType = ClaimValue... |
|Clienentidentities|dize, varsayılan değer ""|Dinamik|FabricClient Windows kimlikleri; ad ağ geçidi, gelen bağlantıları yetkilendirmek için bunu kullanır. Bu, virgülle ayrılmış bir liste; her giriş bir etki alanı hesap adı veya grup adıdır. Kolaylık sağlaması için; Fabric. exe ' yi çalıştıran hesaba otomatik olarak izin verilir; Bu nedenle, Grup ServiceFabricAllowedUsers ve ServiceFabricAdministrators ' dir. |
|Clienentrolet etkin|bool, varsayılan değer FALSE|Statik|İstemci rolünün etkinleştirilip etkinleştirilmediğini belirtir; true olarak ayarlandığında; istemcilere kimliklerine göre roller atanır. V2 için; Bunun etkinleştirilmesi AdminClientCommonNames/Adminclienentidentities içinde olmayan istemcinin yalnızca salt okuma işlemlerini yürütebileceği anlamına gelir. |
|Clustercertparmak Izleri|dize, varsayılan değer ""|Dinamik|Kümeye katılmasına izin verilen sertifikaların parmak izleri; virgülle ayrılmış bir ad listesi. |
|ClusterCredentialType|dize, varsayılan değer "none"|Izin verilmiyor|Kümenin güvenliğini sağlamak için kullanılacak güvenlik kimlik bilgilerinin türünü belirtir. Geçerli değerler "none/x509/Windows" |
|Kümekimlikleri|dize, varsayılan değer ""|Dinamik|Küme düğümlerinin Windows kimlikleri; küme üyeliği yetkilendirmesi için kullanılır. Bu, virgülle ayrılmış bir liste; her giriş bir etki alanı hesap adı veya grup adıdır |
|Kümespn|dize, varsayılan değer ""|Izin verilmiyor|Kümenin hizmet sorumlusu adı; doku tek bir etki alanı kullanıcısı (gMSA/etki alanı kullanıcı hesabı) olarak çalıştırıldığında. Fabric. exe ' de kiralama dinleyicileri ve dinleyicilerinin SPN 'si vardır: Federasyon dinleyicileri; iç çoğaltma dinleyicileri; çalışma zamanı hizmeti dinleyicisi ve adlandırma ağ geçidi dinleyicisi. Doku makine hesapları olarak çalıştırıldığında bu, boş bırakılmalıdır; Bu durumda, yan işlem dinleyicisi SPN 'si dinleyici aktarım adresinden bağlanıyor. |
|CrlCheckingFlag|uint, varsayılan değer 0x40000000|Dinamik|Varsayılan sertifika zinciri doğrulama bayrağı; bileşene özgü bayrak tarafından geçersiz kılınabilir. Örneğin, Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ YALNıZCA 0 olarak ayarlandığında, desteklenen değerler listesinin CRL denetimini devre dışı bırakır, Certgetcertificatezincirinin dwFlags tarafından belgelenmiştir: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, varsayılan:: TimeSpan:: FromMinutes (15)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Çevrimdışı hatayla karşılaşıldığında, belirli bir sertifika için CRL denetlemesi ne kadar devre dışı bırakılır? CRL çevrimdışı hatası yoksayılabilir. |
|CrlOfflineHealthReportTtl|TimeSpan, varsayılan:: TimeSpan:: FromMinutes (1440)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. |
|DisableFirewallRuleForDomainProfile| bool, varsayılan değer doğru |Statik| Güvenlik duvarı kuralının etki alanı profili için etkinleştirilip etkinleştirilmeyeceğini belirtir |
|DisableFirewallRuleForPrivateProfile| bool, varsayılan değer doğru |Statik| Güvenlik duvarı kuralının özel profil için etkinleştirilip etkinleştirilmeyeceğini belirtir | 
|DisableFirewallRuleForPublicProfile| bool, varsayılan değer doğru | Statik|Güvenlik duvarı kuralının genel profil için etkinleştirilip etkinleştirilmeyeceğini belirtir |
| Enforcelınuxmintlsversion | bool, varsayılan değer FALSE | Dinamik | True olarak ayarlanırsa; Yalnızca TLS sürüm 1.2 + desteklenir.  Yanlışsa; önceki TLS sürümlerini destekler. Yalnızca Linux için geçerlidir |
|FabricHostSpn| dize, varsayılan değer "" |Statik| FabricHost hizmet sorumlusu adı; doku tek bir etki alanı kullanıcısı (gMSA/etki alanı kullanıcı hesabı) olarak çalıştırıldığında ve FabricHost makine hesabı altında çalıştığında. FabricHost için IPC dinleyicisinin SPN 'si. FabricHost makine hesabı altında çalıştığından bu varsayılan olarak boş bırakılmalıdır |
|IgnoreCrlOfflineError|bool, varsayılan değer FALSE|Dinamik|Sunucu tarafı gelen istemci sertifikalarını doğrularken CRL çevrimdışı hatası yoksayılıp yoksayılmayacağı |
|IgnoreSvrCrlOfflineError|bool, varsayılan değer doğru|Dinamik|İstemci tarafı gelen sunucu sertifikalarını doğrularken CRL çevrimdışı hatası yoksayılıp yoksayılmayacağı; Varsayılan değeri true 'dur. İptal edilen sunucu sertifikalarına sahip saldırılar DNS güvenliğinin tehlikeye girmesini gerektirir; iptal edilen istemci sertifikalarıyla daha zordur. |
|ServerAuthCredentialType|dize, varsayılan değer "none"|Statik|FabricClient ve Cluster arasındaki iletişimin güvenliğini sağlamak için kullanılacak güvenlik kimlik bilgilerinin türünü belirtir. Geçerli değerler "none/x509/Windows" |
|Sunucucertparmak Izleri|dize, varsayılan değer ""|Dinamik|Küme tarafından istemcilerle konuşmak üzere kullanılan sunucu sertifikalarının parmak izleri; istemciler, kümeyi doğrulamak için bunu kullanır. Bu, virgülle ayrılmış bir ad listesidir. |
|SettingsX509StoreName| dize, varsayılan değer "MY"| Dinamik|Yapılandırma koruması için doku tarafından kullanılan x509 sertifika deposu |
|UseClusterCertForIpcServerTlsSecurity|bool, varsayılan değer FALSE|Statik|IPC sunucusu TLS aktarım birimini güvenli hale getirmek için küme sertifikası kullanılıp kullanılmayacağını belirtir |
|X509Folder|dize, varsayılan değer/var/lib/waagent|Statik|X509 sertifikalarının ve özel anahtarların bulunduğu klasör |

## <a name="securityadminclientx509names"></a>Güvenlik/AdminClientX509Names

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, varsayılan değer None|Dinamik|Bu, "ad" ve "değer" çiftinin bir listesidir. Her bir "ad", yönetici istemci işlemleri için yetkilendirilmiş bir konu ortak adı ya da/yönetici x509 sertifikadır. Verilen bir "ad" için, "değer" veren sabitleme için sertifika parmak izlerinin virgülle ayrılmış listesidir, boş değilse, yönetici istemci sertifikalarının doğrudan vereni listede olmalıdır. |

## <a name="securityclientaccess"></a>Güvenlik/ClientAccess

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ActivateNode |dize, varsayılan değer "admin" |Dinamik| Düğümü etkinleştirmek için güvenlik yapılandırması. |
|CancelTestCommand |dize, varsayılan değer "admin" |Dinamik| Belirli bir TestCommand-uçuş durumunda olduğunda iptal eder. |
|CodePackageControl |dize, varsayılan değer "admin" |Dinamik| Kod paketlerinin yeniden başlatılmasına yönelik güvenlik yapılandırması. |
|Nodestateremoved |dize, varsayılan değer "admin" | Dinamik|Uygulama oluşturma için güvenlik yapılandırması. |
|CreateComposeDeployment|dize, varsayılan değer "admin"| Dinamik|Oluşturma dosyaları tarafından tanımlanan bir oluşturma dağıtımı oluşturur |
|CreateGatewayResource|dize, varsayılan değer "admin"| Dinamik|Ağ Geçidi kaynağı oluşturma |
|CreateName |dize, varsayılan değer "admin" |Dinamik|Adlandırma URI 'SI oluşturma için güvenlik yapılandırması. |
|CreateNetwork|dize, varsayılan değer "admin" |Dinamik|Kapsayıcı ağı oluşturur |
|CreateService |dize, varsayılan değer "admin" |Dinamik| Hizmet oluşturma için güvenlik yapılandırması. |
|CreateServiceFromTemplate |dize, varsayılan değer "admin" |Dinamik|Şablondan hizmet oluşturmaya yönelik güvenlik yapılandırması. |
|CreateVolume|dize, varsayılan değer "admin"|Dinamik|Bir birim oluşturur |
|DeactivateNode |dize, varsayılan değer "admin" |Dinamik| Düğümü devre dışı bırakma için güvenlik yapılandırması. |
|DeactivateNodesBatch |dize, varsayılan değer "admin" |Dinamik| Birden çok düğümü devre dışı bırakmak için güvenlik yapılandırması. |
|Sil |dize, varsayılan değer "admin" |Dinamik| Görüntü deposu istemcisi silme işlemi için güvenlik yapılandırması. |
|DeleteApplication |dize, varsayılan değer "admin" |Dinamik| Uygulama silmeye yönelik güvenlik yapılandırması. |
|DeleteComposeDeployment|dize, varsayılan değer "admin"| Dinamik|Oluşturma dağıtımını siler |
|DeleteGatewayResource|dize, varsayılan değer "admin"| Dinamik|Bir ağ geçidi kaynağını siler |
|DeleteName |dize, varsayılan değer "admin" |Dinamik|Adlandırma URI 'SI silme için güvenlik yapılandırması. |
|DeleteNetwork|dize, varsayılan değer "admin" |Dinamik|Bir kapsayıcı ağını siler |
|DeleteService |dize, varsayılan değer "admin" |Dinamik|Hizmet silme için güvenlik yapılandırması. |
|DeleteVolume|dize, varsayılan değer "admin"|Dinamik|Bir birimi siler.| 
|EnumerateProperties |dize, varsayılan "admin\|\|user" | Dinamik|Adlandırma özelliği numaralandırması için güvenlik yapılandırması. |
|EnumerateSubnames |dize, varsayılan "admin\|\|user" |Dinamik| Adlandırma URI numaralandırması için güvenlik yapılandırması. |
|Dosya Içeriği |dize, varsayılan değer "admin" |Dinamik| Görüntü deposu istemci dosyası aktarımı (dış küme) için güvenlik yapılandırması. |
|Dosya Indirme |dize, varsayılan değer "admin" |Dinamik| Görüntü deposu istemci dosyası indirme başlatma (harici küme) için güvenlik yapılandırması. |
|FinishInfrastructureTask |dize, varsayılan değer "admin" |Dinamik| Altyapı görevlerinin tamamlanması için güvenlik yapılandırması. |
|Birleşimi | dize, varsayılan "admin\|\|user" |Dinamik| Belirli bir zaman aralığı içinde Chaos durumunu getirir. |
|GetClusterConfiguration | dize, varsayılan "admin\|\|user" | Dinamik|Bir bölümde GetClusterConfiguration. |
|GetClusterConfigurationUpgradeStatus | dize, varsayılan "admin\|\|user" |Dinamik| Bir bölümde GetClusterConfigurationUpgradeStatus. |
|GetFabricUpgradeStatus |dize, varsayılan "admin\|\|user" |Dinamik| Küme yükseltme durumunu yoklamaya yönelik güvenlik yapılandırması. |
|GetFolderSize |dize, varsayılan değer "admin" |Dinamik|FileStoreService 'in klasör boyutunu alma güvenlik yapılandırması |
|UpdateApplication |dize, varsayılan değer "admin" |Dinamik| Devre dışı bırakma durumunu denetlemek için güvenlik yapılandırması. |
|Getnodeınlationprogress | dize, varsayılan "admin\|\|user" |Dinamik| Düğüm geçiş komutunda ilerleme durumunu almak için güvenlik yapılandırması. |
|GetPartitionDataLossProgress | dize, varsayılan "admin\|\|user" | Dinamik|Çağırma veri kaybı API çağrısı ilerleme durumunu getirir. |
|GetPartitionQuorumLossProgress | dize, varsayılan "admin\|\|user" |Dinamik| Bir Invoke Quorum kayıp API çağrısı ilerleme durumunu getirir. |
|GetPartitionRestartProgress | dize, varsayılan "admin\|\|user" |Dinamik| Yeniden başlatma bölümü API çağrısı ilerleme durumunu getirir. |
|Getgizlilikler|dize, varsayılan değer "admin"|Dinamik|Gizli değerleri Al |
|GetServiceDescription |dize, varsayılan "admin\|\|user" |Dinamik| Uzun yoklama hizmeti bildirimleri ve hizmet açıklamalarını okumak için güvenlik yapılandırması. |
|GetStagingLocation |dize, varsayılan değer "admin" |Dinamik| Görüntü deposu istemci hazırlama konumu alma için güvenlik yapılandırması. |
|GetStoreLocation |dize, varsayılan değer "admin" |Dinamik| Görüntü deposu istemci deposu konum alımı için güvenlik yapılandırması. |
|GetUpgradeOrchestrationServiceState|dize, varsayılan değer "admin"| Dinamik|Bir bölümde GetUpgradeOrchestrationServiceState |
|GetUpgradesPendingApproval |dize, varsayılan değer "admin" |Dinamik| Bir bölümde GetUpgradesPendingApproval. |
|GetUpgradeStatus |dize, varsayılan "admin\|\|user" |Dinamik| Uygulama yükseltme durumunu yoklamaya yönelik güvenlik yapılandırması. |
|InternalList |dize, varsayılan değer "admin" | Dinamik|Görüntü deposu istemci dosyası listesi işlemi için güvenlik yapılandırması (iç). |
|Invokecontainerapı|dize, varsayılan değer "admin"|Dinamik|Kapsayıcı API 'sini çağır |
|InvokeInfrastructureCommand |dize, varsayılan değer "admin" |Dinamik| Altyapı görevi yönetim komutları için güvenlik yapılandırması. |
|InvokeInfrastructureQuery |dize, varsayılan "admin\|\|user" | Dinamik|Altyapı görevlerini sorgulamak için güvenlik yapılandırması. |
|Liste |dize, varsayılan "admin\|\|user" | Dinamik|Görüntü deposu istemci dosyası listesi işlemi için güvenlik yapılandırması. |
|MoveNextFabricUpgradeDomain |dize, varsayılan değer "admin" |Dinamik| Açık yükseltme etki alanı ile küme yükseltmelerini sürdürmek için güvenlik yapılandırması. |
|MoveNextUpgradeDomain |dize, varsayılan değer "admin" |Dinamik| Açık yükseltme etki alanı ile uygulama yükseltmelerini sürdürmek için güvenlik yapılandırması. |
|Movereperepcontrol |dize, varsayılan değer "admin" | Dinamik|Çoğaltmayı taşıyın. |
|Ad var |dize, varsayılan "admin\|\|user" | Dinamik|URI varlık denetimlerini adlandırmak için güvenlik yapılandırması. |
|NodeControl |dize, varsayılan değer "admin" |Dinamik| Başlangıç için güvenlik yapılandırması; durdurulamadı ve düğümleri yeniden başlatın. |
|NodeStateRemoved |dize, varsayılan değer "admin" |Dinamik| Raporlama düğümü durumunun güvenlik yapılandırması kaldırıldı. |
|Ping |dize, varsayılan "admin\|\|user" |Dinamik| İstemci ping işlemleri için güvenlik yapılandırması. |
|PredeployPackageToNode |dize, varsayılan değer "admin" |Dinamik| Ön dağıtım API 'si. |
|PrefixResolveService |dize, varsayılan "admin\|\|user" |Dinamik| Şikayet tabanlı hizmet ön eki çözümlemesi için güvenlik yapılandırması. |
|PropertyReadBatch |dize, varsayılan "admin\|\|user" |Dinamik| Adlandırma özelliği okuma işlemleri için güvenlik yapılandırması. |
|PropertyWriteBatch |dize, varsayılan değer "admin" |Dinamik|Adlandırma özelliği yazma işlemleri için güvenlik yapılandırması. |
|ProvisionApplicationType |dize, varsayılan değer "admin" |Dinamik| Uygulama türü sağlama için güvenlik yapılandırması. |
|ProvisionFabric |dize, varsayılan değer "admin" |Dinamik| MSI ve/veya küme bildirimi sağlama için güvenlik yapılandırması. |
|Sorgu |dize, varsayılan "admin\|\|user" |Dinamik| Sorgular için güvenlik yapılandırması. |
|RecoverPartition |dize, varsayılan değer "admin" | Dinamik|Bir bölümü kurtarmak için güvenlik yapılandırması. |
|RecoverPartitions |dize, varsayılan değer "admin" | Dinamik|Bölümleri kurtarmak için güvenlik yapılandırması. |
|RecoverServicePartitions |dize, varsayılan değer "admin" |Dinamik| Hizmet bölümlerini kurtarmak için güvenlik yapılandırması. |
|RecoverSystemPartitions |dize, varsayılan değer "admin" |Dinamik| Sistem hizmeti bölümlerini kurtarmak için güvenlik yapılandırması. |
|UpdateApplication |dize, varsayılan değer "admin" |Dinamik| Birden çok düğümdeki devre dışı bırakma işlemini geri almak için güvenlik yapılandırması. |
|ReportFabricUpgradeHealth |dize, varsayılan değer "admin" |Dinamik| Geçerli yükseltme ilerlemesiyle küme yükseltmelerini sürdürmek için güvenlik yapılandırması. |
|ReportFault |dize, varsayılan değer "admin" |Dinamik| Raporlama hatası için güvenlik yapılandırması. |
|Raporla |dize, varsayılan değer "admin" |Dinamik| Raporlama durumu için güvenlik yapılandırması. |
|ReportUpgradeHealth |dize, varsayılan değer "admin" |Dinamik| Geçerli yükseltme ilerlemesiyle uygulama yükseltmelerini sürdürmek için güvenlik yapılandırması. |
|ResetPartitionLoad |dize, varsayılan "admin\|\|user" |Dinamik| FailoverUnit için sıfırlama yükünün güvenlik yapılandırması. |
|ResolveNameOwner |dize, varsayılan "admin\|\|user" | Dinamik|Adlandırma URI 'SI sahibini çözümlemek için güvenlik yapılandırması. |
|ResolvePartition |dize, varsayılan "admin\|\|user" | Dinamik|Sistem hizmetlerini çözümlemek için güvenlik yapılandırması. |
|ResolveService |dize, varsayılan "admin\|\|user" |Dinamik| Şikayet tabanlı hizmet çözümlemesi için güvenlik yapılandırması. |
|Resolvessystemservice|dize, varsayılan "admin\|\|user"|Dinamik| Sistem hizmetlerini çözümlemek için güvenlik yapılandırması |
|RollbackApplicationUpgrade |dize, varsayılan değer "admin" |Dinamik| Uygulama yükseltmelerini geri alma için güvenlik yapılandırması. |
|RollbackFabricUpgrade |dize, varsayılan değer "admin" |Dinamik| Küme yükseltmelerini geri alma için güvenlik yapılandırması. |
|ServiceNotifications |dize, varsayılan "admin\|\|user" |Dinamik| Olay tabanlı hizmet bildirimleri için güvenlik yapılandırması. |
|SetUpgradeOrchestrationServiceState|dize, varsayılan değer "admin"| Dinamik|Bir bölümde SetUpgradeOrchestrationServiceState |
|Startapprove yükseltmeleri |dize, varsayılan değer "admin" |Dinamik| Bir bölümde Startapprove yükseltmeleri. |
|StartChaos |dize, varsayılan değer "admin" |Dinamik| Zaten başlatılmamışsa, Chaos başlatır. |
|StartClusterConfigurationUpgrade |dize, varsayılan değer "admin" |Dinamik| Bir bölümde StartClusterConfigurationUpgrade. |
|Starınfrastructuretask |dize, varsayılan değer "admin" | Dinamik|Altyapı görevlerinin başlatılmasına yönelik güvenlik yapılandırması. |
|StartNodeTransition |dize, varsayılan değer "admin" |Dinamik| Düğüm geçişi başlatmak için güvenlik yapılandırması. |
|StartPartitionDataLoss |dize, varsayılan değer "admin" |Dinamik| Bir bölümde veri kaybı. |
|Startpartitionquorumkaybetme |dize, varsayılan değer "admin" |Dinamik| Bir bölümde çekirdek kaybına yol açar. |
|StartPartitionRestart |dize, varsayılan değer "admin" |Dinamik| Bir bölümün bazı veya tüm çoğaltmalarını eşzamanlı olarak yeniden başlatır. |
|StopChaos |dize, varsayılan değer "admin" |Dinamik| Başlatılmışsa, Chaos 'yi durduruyor. |
|ToggleVerboseServicePlacementHealthReporting | dize, varsayılan "admin\|\|user" |Dinamik| Ayrıntılı Serviceyerleştirmesini HealthReporting 'i değiştirmek için güvenlik yapılandırması. |
|UnprovisionApplicationType |dize, varsayılan değer "admin" |Dinamik| Uygulama türü sağlamayı kaldırma için güvenlik yapılandırması. |
|UnprovisionFabric |dize, varsayılan değer "admin" |Dinamik| MSI ve/veya küme bildirimi sağlamayı kaldırma için güvenlik yapılandırması. |
|UnreliableTransportControl |dize, varsayılan değer "admin" |Dinamik| Davranışları eklemek ve kaldırmak için güvenilir olmayan aktarım. |
|UpdateService |dize, varsayılan değer "admin" |Dinamik|Hizmet güncelleştirmeleri için güvenlik yapılandırması. |
|UpgradeApplication |dize, varsayılan değer "admin" |Dinamik| Uygulama yükseltmelerini başlatmak veya kesintiye uğratma için güvenlik yapılandırması. |
|UpgradeComposeDeployment|dize, varsayılan değer "admin"| Dinamik|Oluşturma dağıtımını yükseltir |
|Yüksellen Bric |dize, varsayılan değer "admin" |Dinamik| Küme yükseltmelerini başlatmak için güvenlik yapılandırması. |
|Karşıya Yükle |dize, varsayılan değer "admin" | Dinamik|Görüntü deposu istemcisini karşıya yükleme işlemi için güvenlik yapılandırması. |

## <a name="securityclientcertificateissuerstores"></a>Güvenlik/Clientcertificateıssuermağazalarında

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|Issuerstorekeyvaluemap, varsayılan değer None |Dinamik|İstemci sertifikaları için x509 veren sertifika depoları; Ad = Clienentissuercn; Değer = depoların virgülle ayrılmış listesi |

## <a name="securityclientx509names"></a>Güvenlik/ClientX509Names

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, varsayılan değer None|Dinamik|Bu, "ad" ve "değer" çiftinin bir listesidir. Her bir "ad", istemci işlemleri için yetkilendirilmiş bir konu ortak adı ya da bir veya DnsName sertifikası. Verilen bir "ad" için, "değer" veren sabitleme için sertifika parmak izlerinin virgülle ayrı bir listesidir, boş değilse, istemci sertifikalarının doğrudan vereni listede olmalıdır.|

## <a name="securityclustercertificateissuerstores"></a>Güvenlik/Clustercertificateıssuermağazalarında

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|Issuerstorekeyvaluemap, varsayılan değer None |Dinamik|Küme sertifikaları için x509 veren sertifika depoları; Ad = Clusterıssuercn; Değer = depoların virgülle ayrılmış listesi |

## <a name="securityclusterx509names"></a>Güvenlik/ClusterX509Names

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, varsayılan değer None|Dinamik|Bu, "ad" ve "değer" çiftinin bir listesidir. Her "ad", küme işlemlerine yetki verilen ilgili ortak addır veya DnsName sertifikalardır. Verilen bir "ad" için, "değer" veren sabitleme için sertifika parmak izlerinin virgülle ayrılmış listesidir, boş değilse, küme sertifikalarının doğrudan vereni listede olmalıdır.|

## <a name="securityservercertificateissuerstores"></a>Güvenlik/Servercertificateıssuermağazalarında

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|Issuerstorekeyvaluemap, varsayılan değer None |Dinamik|Sunucu sertifikaları için x509 veren sertifika depoları; Ad = Sunucuıssuercn; Değer = depoların virgülle ayrılmış listesi |

## <a name="securityserverx509names"></a>Güvenlik/ServerX509Names

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, varsayılan değer None|Dinamik|Bu, "ad" ve "değer" çiftinin bir listesidir. Her bir "ad", sunucu işlemleri için yetkilendirilmiş bir konu ortak adı ya da bir veya DnsName sertifikası. Verilen bir "ad" için, "değer" veren sabitleme için sertifika parmak izlerinin virgülle ayrılmış listesidir, boş değilse, sunucu sertifikalarının doğrudan vereni listede olmalıdır.|

## <a name="setup"></a>Kurulum

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ContainerNetworkName|dize, varsayılan değer ""| Statik |Bir kapsayıcı ağı ayarlanırken kullanılacak ağ adı.|
|ContainerNetworkSetup|bool, varsayılan değer FALSE| Statik |Bir kapsayıcı ağının oluşturulup ayarlanmayacağı.|
|FabricDataRoot |Dize | Izin verilmiyor |Veri kök dizinini Service Fabric. Azure için varsayılan değer d:\svcfab |
|FabricLogRoot |Dize | Izin verilmiyor |Service Fabric günlük kök dizini. Bu, SF günlüklerinin ve izlemelerin yerleştirildiği yerdir. |
|Nodestoberetaşındı|dize, varsayılan değer ""| Dinamik |Yapılandırma yükseltmesinin bir parçası olarak kaldırılması gereken düğümler. (Yalnızca tek başına dağıtımlar için)|
|ServiceRunAsAccountName |Dize | Izin verilmiyor |Yapı ana bilgisayar hizmetinin çalıştırılacağı hesap adı. |
|SkipContainerNetworkResetOnReboot|bool, varsayılan değer FALSE|NotAllowed|Yeniden başlatma sırasında kapsayıcı ağının sıfırlanıp atlanmayacağı.|
|SkipFirewallConfiguration |Bool, varsayılan değer false | Izin verilmiyor |Güvenlik Duvarı ayarlarının sistem tarafından ayarlanması gerektiğini belirtir. Bu, yalnızca Windows güvenlik duvarı kullanıyorsanız geçerlidir. Üçüncü taraf güvenlik duvarları kullanıyorsanız, kullanılacak sistem ve uygulamalar için bağlantı noktalarını açmanız gerekir |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Sağlayıcılar |dize, varsayılan değer "DSTS" |Statik|Etkinleştirilecek belirteç doğrulama sağlayıcılarının virgülle ayrılmış listesi (geçerli sağlayıcılar: DSTS; AAD). Şu anda herhangi bir zamanda yalnızca tek bir sağlayıcı etkinleştirilebilir. |

## <a name="traceetw"></a>İzleme/ETW

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Düzey |Int, varsayılan değer 4 ' dir | Dinamik |Trace ETW düzeyi 1, 2, 3, 4 değerlerini alabilir. Desteklenecek, izleme düzeyini 4 ' te tutmanız gerekir |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Batchval Gementınterval | Saniye cinsinden süre, varsayılan değer 0,015 ' dir | Statik | Zaman aralığı değerini saniye cinsinden belirtin. Bir onayı geri göndermeden önce çoğaltıcı tarafından bir işlem alındıktan sonra beklediği süreyi belirler. Bu süre boyunca alınan diğer işlemler, bildirimlerin tek bir ileti içinde geri gönderilmesini >, ağ trafiğini azaltmakta ve çoğaltıcı verimini potansiyel olarak azaltmaktadır. |
|MaxCopyQueueSize |Uint, varsayılan değer 16384 | Statik |Bu en büyük değer, çoğaltma işlemlerini tutan sıranın ilk boyutunu tanımlar. 2 ' nin üssü olması gerektiğini unutmayın. Çalışma zamanı sırasında sıranın bu boyut ile büyümesi, birincil ve ikincil çoğaltıcılar arasında kısıtlanacak. |
|MaxPrimaryReplicationQueueMemorySize |Uint, varsayılan 0 ' dır | Statik |Bu, birincil çoğaltma sırasının bayt cinsinden en yüksek değeridir. |
|MaxPrimaryReplicationQueueSize |Uint, varsayılan değer 8192 | Statik |Bu, birincil çoğaltma kuyruğunda mevcut olabilecek en fazla işlem sayısıdır. 2 ' nin üssü olması gerektiğini unutmayın. |
|MaxReplicationMessageSize |uint, varsayılan değer 52428800 | Statik | Çoğaltma işlemlerinin en büyük ileti boyutu. Varsayılan değer 50 MB 'tır. |
|MaxSecondaryReplicationQueueMemorySize |Uint, varsayılan 0 ' dır | Statik |Bu, ikincil çoğaltma sırasının bayt cinsinden en yüksek değeridir. |
|MaxSecondaryReplicationQueueSize |Uint, varsayılan değer 16384 | Statik |Bu, ikincil çoğaltma kuyruğunda olabilecek en fazla işlem sayısıdır. 2 ' nin üssü olması gerektiğini unutmayın. |
|Replicatoraydresi |dize, varsayılan değer "localhost: 0" | Statik | Windows Fabric çoğaltıcı tarafından, gönderme/alma işlemleri için diğer yinelemelerle bağlantı kurmak üzere kullanılan ' IP: Port ' dizesi biçimindeki uç nokta. |

## <a name="transport"></a>Aktarım
| **Parametresinin** | **İzin verilen değerler** |**Yükseltme ilkesi** |**Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (60)|Statik|Zaman aralığı değerini saniye cinsinden belirtin. Hem gelen hem de kabul eden tarafta bağlantı kurulumu için zaman aşımı (güvenli modda güvenlik anlaşması dahil) |
|FrameHeaderErrorCheckingEnabled|bool, varsayılan değer doğru|Statik|Çerçeve üstbilgisinde güvenli olmayan modda hata denetimi için varsayılan ayar; bileşen ayarı bunu geçersiz kılar. |
|MessageErrorCheckingEnabled|bool, varsayılan değer FALSE|Statik|İleti üst bilgisinde ve gövdesinde güvenli olmayan modda hata denetimi için varsayılan ayar; bileşen ayarı bunu geçersiz kılar. |
|ResolveOption|dize, varsayılan değer "belirtilmemiş"|Statik|FQDN 'nin nasıl çözümlendiğini belirler.  Geçerli değerler şunlardır "belirtilmemiş/IPv4/IPv6". |
|Binding üstündeki SendTimeout|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (300)|Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. Takılmış bağlantıyı algılamak için zaman aşımı gönder. TCP hata raporları, bazı ortamlarda güvenilir değildir. Bunun, kullanılabilir ağ bant genişliğine ve giden verilerin boyutuna (\*MaxMessageSize\/\*SendQueueSizeLimit) göre ayarlanması gerekebilir. |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|Oto Upgradeenabled | Bool, varsayılan değer doğru |Statik| Hedef durum dosyasına göre otomatik yoklama ve yükseltme eylemi. |
|AutoupgradeInstallEnabled|bool, varsayılan değer FALSE|Statik|Hedef durum dosyasını temel alarak kod yükseltme eyleminin otomatik yoklaması, sağlanması ve yüklenmesi.|
|GoalStateExpirationReminderInDays|int, varsayılan değer 30 ' dur|Statik|Hedef durumu anımsatıcısının gösterilmesinin ardından kalan gün sayısını ayarlar.|
|MinReplicaSetSize |int, varsayılan değer 0 ' dır |Statik |UpgradeOrchestrationService için MinReplicaSetSize.|
|Placementkýsýtlamalarý | dize, varsayılan değer "" |Statik| UpgradeOrchestrationService için Placementkýsýtlamalarý. |
|QuorumLossWaitDuration | Saniye cinsinden süre, varsayılan değer MaxValue |Statik| Zaman aralığı değerini saniye cinsinden belirtin. UpgradeOrchestrationService için QuorumLossWaitDuration. |
|ReplicaRestartWaitDuration | Saniye cinsinden süre, varsayılan değer 60 dakikadır|Statik| Zaman aralığı değerini saniye cinsinden belirtin. UpgradeOrchestrationService için ReplicaRestartWaitDuration. |
|StandByReplicaKeepDuration | Saniye cinsinden süre, varsayılan değer 60*24*7 dakikadır |Statik| Zaman aralığı değerini saniye cinsinden belirtin. UpgradeOrchestrationService için StandByReplicaKeepDuration. |
|TargetReplicaSetSize |int, varsayılan değer 0 ' dır |Statik |UpgradeOrchestrationService için TargetReplicaSetSize. |
|UpgradeApprovalRequired | Bool, varsayılan değer false | Statik|Kod yükseltmesini yapmak için ayarlama devam etmeden önce yönetici onayı gerektirir. |

## <a name="upgradeservice"></a>UpgradeService

| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|BaseUrl | dize, varsayılan değer "" |Statik|UpgradeService için BaseUrl. |
|Lclusterıd | dize, varsayılan değer "" |Statik|UpgradeService için Clusterıd. |
|Koordinatör Tortype | dize, varsayılan değer "WUTest"|Izin verilmiyor|UpgradeService için Koordinatör Tortype. |
|MinReplicaSetSize | Int, varsayılan değer 2 ' dir |Izin verilmiyor| UpgradeService için MinReplicaSetSize. |
|OnlyBaseUpgrade | Bool, varsayılan değer false |Dinamik|UpgradeService için OnlyBaseUpgrade. |
|Placementkýsýtlamalarý |dize, varsayılan değer "" |Izin verilmiyor|Yükseltme hizmeti için Placementkýsýtlamalarý. |
|Pollıntervalınseconds|TimeSpan, varsayılan:: TimeSpan:: FromSeconds (60) |Dinamik|Zaman aralığı değerini saniye cinsinden belirtin. ARM yönetim işlemleri için UpgradeService yoklaması arasındaki Aralık. |
|TargetReplicaSetSize | Int, varsayılan 3 ' dir |Izin verilmiyor| UpgradeService için TargetReplicaSetSize. |
|Testcabklasörü | dize, varsayılan değer "" |Statik| UpgradeService için Testcabklasörü. |
|X509FindType | dize, varsayılan değer ""|Dinamik| UpgradeService için X509FindType. |
|X509FindValue | dize, varsayılan değer "" |Dinamik| UpgradeService için X509FindValue. |
|X509SecondaryFindValue | dize, varsayılan değer "" |Dinamik| UpgradeService için X509SecondaryFindValue. |
|X509StoreLocation | dize, varsayılan değer "" |Dinamik| UpgradeService için X509StoreLocation. |
|x509StoreName | dize, varsayılan değer "My"|Dinamik|UpgradeService için X509StoreName. |

## <a name="userservicemetriccapacities"></a>Userservicemetrickapasiteler
| **Parametresinin** | **İzin verilen değerler** | **Yükseltme Ilkesi** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, varsayılan değer None | Statik | Kullanıcı Hizmetleri kaynak idare limitlerinin bir koleksiyonu, oto algılama mantığını etkilediği için statik olmalıdır |

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [Azure kümesinin yapılandırmasını yükseltme](service-fabric-cluster-config-upgrade-azure.md) ve [tek başına kümenin yapılandırmasını yükseltme](service-fabric-cluster-config-upgrade-windows-server.md).
