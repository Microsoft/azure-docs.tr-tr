---
title: Azure Kaynak Durumu aracılığıyla desteklenen kaynak türleri | Microsoft Docs
description: Azure Kaynak durumu aracılığıyla desteklenen kaynak türleri
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: 1692e8acda88a51173eb0eabcd90c377b431a3c3
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955028"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Azure Kaynak durumu 'nda kaynak türleri ve durum denetimleri
Kaynak türlerine göre kaynak sistem durumu aracılığıyla yürütülen tüm denetimlerin listesi aşağıda verilmiştir.

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/sunucuları
|Yürütülen denetimler|
|---|
|<ul><li>Sunucu çalışır duruma mi çalışıyor?</li><li>Sunucunun belleği tükensin mi?</li><li>Sunucu başlatılıyor mu?</li><li>Sunucu mı kurtarıyor?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Yürütülen denetimler|
|---|
|<ul><li>API Yönetimi hizmeti çalışır duruma mi çalışıyor?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Yürütülen denetimler|
|---|
|<ul><li>Batch hesabı çalışır duruma mi çalışıyor?</li><li>Bu Batch hesabı için havuz kotası aşıldı mi?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Yürütülen denetimler|
|---|
|<ul><li>Tüm önbellek düğümleri çalışır duruma mi çalışıyor?</li><li>Veri merkezinde önbellekten ulaşılmış olabilir mi?</li><li>Önbellek en fazla bağlantı sayısına ulaştı mi?</li><li> Önbelleğin kullanılabilir belleği tükendi mi? </li><li>Önbellek çok sayıda sayfa hatası yaşıyor mu?</li><li>Önbellek ağır yük altında mı?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft. CDN/profil
|Yürütülen denetimler|
|---|
|<ul> <li>Ek portal CDN yapılandırma işlemleri için erişilebilir mi?</li><li>CDN uç noktalarında devam eden teslim sorunları var mı?</li><li>Kullanıcılar CDN kaynaklarının yapılandırmalarını değiştirebilir mi?</li><li>Yapılandırma değişiklikleri beklenen hızda yayılıyor mu?</li><li>Kullanıcılar Azure portal, PowerShell veya API kullanarak CDN yapılandırmasını yönetebilir mi?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. classiccompute/virtualmachines
|Yürütülen denetimler|
|---|
|<ul><li>Ana bilgisayar sunucusu çalışır duruma mi çalışıyor?</li><li>Konak işletim sistemi önyüklemesi tamamlandı mı?</li><li>Sanal makine kapsayıcısı sağlanıyor ve destekleniyor mu?</li><li>Ana bilgisayar ve depolama hesabı arasında ağ bağlantısı var mı?</li><li>Konuk işletim sisteminin önyüklemesine tamamlandı mı?</li><li>Devam eden planlı bakım var mı?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. biliveservices/hesapları
|Yürütülen denetimler|
|---|
|<ul><li>Hesap, veri merkezi içinden mi erişilebilir?</li><li>Bilişsel hizmetler kaynak sağlayıcısı kullanılabilir mi?</li><li>Bilişsel hizmet uygun bölgede kullanılabilir mi?</li><li>Kaynak meta verilerini tutan depolama hesabında okuma işlemleri yapılabilir mi?</li><li>API çağrısı kotasına ulaşıldı mu?</li><li>API çağrısı okuma sınırına ulaşıldı mu?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft. COMPUTE/virtualmachines
|Yürütülen denetimler|
|---|
|<ul><li>Bu sanal makineyi barındıran sunucu çalışır duruma mi çalışıyor?</li><li>Konak işletim sistemi önyüklemesi tamamlandı mı?</li><li>Sanal makine kapsayıcısı sağlanıyor ve destekleniyor mu?</li><li>Ana bilgisayar ve depolama hesabı arasında ağ bağlantısı var mı?</li><li>Konuk işletim sisteminin önyüklemesine tamamlandı mı?</li><li>Devam eden planlı bakım var mı?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/Factory
|Yürütülen denetimler|
|---|
|<ul><li>İşlem hattı çalıştırma hatalarıyla karşılaşıldı mi?</li><li>Küme, Data Factory sağlıklı olarak barınıyor mu?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. datalakeanalytics/hesapları
|Yürütülen denetimler|
|---|
|<ul><li>Kullanıcıların Data Lake Analytics işlerini gönderme veya listeleme konusunda sorun yaşadı mi?</li><li>Data Lake Analytics işleri sistem hataları nedeniyle tamamlanamadı mu?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. datalakestore/accounts
|Yürütülen denetimler|
|---|
|<ul><li>Kullanıcıların Data Lake Store verileri karşıya yüklerken sorun yaşadı mi?</li><li>Kullanıcıların Data Lake Store verileri indirirken sorun yaşmış olduğundan emin misiniz?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft. datamigration/hizmetler
|Yürütülen denetimler|
|---|
|<ul><li>Veritabanı geçiş hizmeti sağlanamadı mi?</li><li>Veritabanı geçiş hizmeti, eylemsizlik veya Kullanıcı isteği nedeniyle durduruldu mu?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/hesapları
|Yürütülen denetimler|
|---|
|<ul><li>Veri paylaşımının hesabı çalışıyor mu?</li><li>Veri paylaşımının barındırıldığı küme kullanılabilir mi?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft. Dbformarıdb/sunucular
|Yürütülen denetimler|
|---|
|<ul><li>Sunucu bakım nedeniyle kullanılamıyor mu?</li><li>Sunucu yeniden yapılandırma nedeniyle kullanılamıyor mu?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft. Dbformyısql/sunucuları
|Yürütülen denetimler|
|---|
|<ul><li>Sunucu bakım nedeniyle kullanılamıyor mu?</li><li>Sunucu yeniden yapılandırma nedeniyle kullanılamıyor mu?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/sunucuları
|Yürütülen denetimler|
|---|
|<ul><li>Sunucu bakım nedeniyle kullanılamıyor mu?</li><li>Sunucu yeniden yapılandırma nedeniyle kullanılamıyor mu?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Yürütülen denetimler|
|---|
|<ul><li>IoT Hub 'ı çalışır duruma mi çalışıyor?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts
|Yürütülen denetimler|
|---|
|<ul><li>Bir Azure Cosmos DB hizmeti KULLANILAMAMASINDAN dolayı hiçbir veritabanı veya koleksiyon isteği yoktu mi?</li><li>Azure Cosmos DB hizmeti KULLANILAMAMASINDAN dolayı hiçbir belge isteği sunulmadı mu?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/ad alanları
|Yürütülen denetimler|
|---|
|<ul><li>Event Hubs ad alanı kullanıcı tarafından oluşturulan hatalar yaşıyor mu?</li><li>Event Hubs ad alanı şu anda yükseltiliyor mi?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kümeler
|Yürütülen denetimler|
|---|
|<ul><li>HDInsight kümesinde çekirdek hizmetleri var mı?</li><li>HDInsight kümesi Rest 'de BYOK şifrelemesi için anahtara erişebilsin mi?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Yürütülen denetimler|
|---|
|<ul><li>Azure Keykasaplatformu sorunları nedeniyle Anahtar Kasası başarısız olma istekleri mi?</li><li>Müşteri tarafından çok fazla istek yapıldığından anahtar kasasının kısıtlanıyor olması ister misiniz?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft. Machinöğrenim/webServices
|Yürütülen denetimler|
|---|
|<ul><li>Web hizmeti çalışır duruma mi çalışıyor?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices
|Yürütülen denetimler|
|---|
|<ul><li>Medya hizmeti çalışır duruma mi çalışıyor?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationgateway 'ler
|Yürütülen denetimler|
|---|
|<ul><li>Application Gateway performansı düşürülmüş mi?</li><li>Application Gateway kullanılabilir mi?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections
|Yürütülen denetimler|
|---|
|<ul><li>VPN tüneli bağlı mı?</li><li>Bağlantıda yapılandırma çakışması var mı?</li><li>Önceden paylaşılan anahtarlar düzgün şekilde yapılandırılmış mi?</li><li>VPN şirket içi cihaz erişilebilir mi?</li><li>IPSec/ıKE güvenlik ilkesinde uyuşmazlıklar var mı?</li><li>S2S VPN bağlantısı düzgün şekilde sağlandı veya hatalı durumda mı?</li><li>VNET-VNET bağlantısının düzgün bir şekilde sağlanması veya başarısız olması durumunda mi?</li></ul>|

## <a name="microsoftnetworkexpressreoutecircuits"></a>Microsoft. Network/expressreoutedevreleri
|Yürütülen denetimler|
|---|
|<ul><li>ExpressRoute devresi sağlıklı mı?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontkapaklı
|Yürütülen denetimler|
|---|
|<ul><li>Ön kapı arka uçları, sistem durumu araştırmalarının hatalarını vererek yanıt veriyor mu?</li><li>Yapılandırma değişiklikleri gecikiyor mu?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Yürütülen denetimler|
|---|
|<ul><li>Yük Dengeleme uç noktaları kullanılabilir mi?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/Virtualnetworkgateway 'ler
|Yürütülen denetimler|
|---|
|<ul><li>VPN ağ geçidi internet 'ten erişilebilir mi?</li><li>VPN Gateway bekleme modunda mı?</li><li>VPN hizmeti ağ geçidinde çalışıyor mu?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft. Notificationhub 'Lar/ad alanı
|Yürütülen denetimler|
|---|
|<ul><li>Ad alanı üzerinde kayıt, yükleme veya gönderme gibi çalışma zamanı işlemleri yapılabilir mi?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. operationalınsights/çalışma alanları
|Yürütülen denetimler|
|---|
|<ul><li>Çalışma alanı için dizin oluşturma gecikmeleri var mı?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. Powerbiadanmış/kapasiteler
|Yürütülen denetimler|
|---|
|<ul><li>Kapasite kaynağı çalışır duruma mi çalışıyor?</li><li>Tüm iş yükleri çalışır ve çalışıyor mu?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft. PowerBI/workspaceCollections
|Yürütülen denetimler|
|---|
|<ul><li>Ana bilgisayar işletim sistemi çalışıyor mu?</li><li>WorkspaceCollection, veri merkezi dışından erişilebilir mi?</li><li>Power BI kaynak sağlayıcısı kullanılabilir mi?</li><li>Power BI hizmeti uygun bölgede kullanılabilir mi?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices
|Yürütülen denetimler|
|---|
|<ul><li>Küme üzerinde tanılama işlemleri gerçekleştirilebilir mi?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Yürütülen denetimler|
|---|
|<ul><li>Müşteriler Kullanıcı tarafından oluşturulan Service Bus hatalarıyla karşılaşıyor mu?</li><li>Kullanıcılar Service Bus ad alanı yükseltmesi nedeniyle geçici hatalarda artış yaşlıyor mu?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/ManagedInstances/veritabanları
|Yürütülen denetimler|
|---|
|<ul><li>Veritabanı çalışır duruma mi çalışıyor?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft. SQL/Server/veritabanları
|Yürütülen denetimler|
|---|
|<ul><li>Veritabanında oturum açma işlemleri var mı?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Yürütülen denetimler|
|---|
|<ul><li>Azure depolama platformu sorunları nedeniyle depolama hesabından veri okuma istekleri mi var?</li><li>Azure depolama platformu sorunları nedeniyle depolama hesabına veri yazma istekleri mi var?</li><li>Depolama hesabının kullanılabilir olmadığı depolama kümesi mi var?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs
|Yürütülen denetimler|
|---|
|<ul><li>İşin yürütüldüğü ve çalıştırıldığı tüm konaklar mı?</li><li>İş başlatılamadı mi?</li><li>Devam eden çalışma zamanı yükseltmeleri var mı?</li><li>İş beklenen bir durumda mı (örneğin, müşteri tarafından çalışıyor veya durdurulmuş)?</li><li>İş, yetersiz bellek özel durumu ile karşılaştı mu?</li><li>Devam eden zamanlanmış işlem güncelleştirmeleri var mı?</li><li>Yürütme Yöneticisi (Denetim planı) kullanılabilir mi?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/Sunucugrupları
|Yürütülen denetimler|
|---|
|<ul><li>Ana bilgisayar sunucusu çalışır duruma mi çalışıyor?</li><li>Internet Information Services çalışıyor mu?</li><li>Yük dengeleyici çalışıyor mu?</li><li>App Service planına veri merkezi içinden ulaşımı ulaşılmalıdır?</li><li>ServerFarm için siteleri içeriğini barındıran depolama hesabı mı var?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft. Web/siteler
|Yürütülen denetimler|
|---|
|<ul><li>Ana bilgisayar sunucusu çalışır duruma mi çalışıyor?</li><li>Internet Information Server çalışıyor mu?</li><li>Yük dengeleyici çalışıyor mu?</li><li>Web uygulamasına veri merkezi içinden ulaşılmış olabilir mi?</li><li>Site içeriğini barındıran depolama hesabı kullanılabilir mi?</li></ul>|

## <a name="next-steps"></a>Sonraki Adımlar
-  Bkz. [Azure hizmet durumu panosuna giriş](service-health-overview.md) ve bunlarla ilgili daha fazla bilgi Için [Azure Kaynak durumu giriş](resource-health-overview.md) . 
-  [Azure Kaynak Durumu hakkında sık sorulan sorular](resource-health-faq.md)
- Sistem durumu sorunları hakkında bildirim almak için uyarıları ayarlayın. Daha fazla bilgi için bkz. [hizmet durumu olayları Için uyarıları yapılandırma](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
