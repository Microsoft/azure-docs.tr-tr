---
title: Multiregional ortamlarında Azure Cosmos SDK 'larının kullanılabilirliğini tanılama ve sorunlarını giderme
description: Çoklu bölgesel ortamlarda çalışırken Azure Cosmos SDK kullanılabilirlik davranışı hakkında bilgi edinin.
author: ealsur
ms.service: cosmos-db
ms.date: 02/18/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0720eb01920e39a9bee27e4d00d97acba55b0ad5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101661435"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Multiregional ortamlarında Azure Cosmos SDK 'larının kullanılabilirliğini tanılama ve sorunlarını giderme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, belirli bir bölgede veya bir bölge yük devretmesinin gerçekleştiği zaman bir bağlantı sorunu gördüğünüzde Azure Cosmos SDK 'larının en son sürümünün davranışı açıklanmaktadır.

Tüm Azure Cosmos SDK 'Ları, bölgesel tercihi özelleştirmek için size bir seçenek sunar. Aşağıdaki özellikler farklı SDK 'larda kullanılır:

* .NET v2 SDK 'daki [Connectionpolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) özelliği.
* .NET v3 SDK 'daki [Cosmosclientoptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) veya [Cosmosclientoptions. applicationpreferredregion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) özellikleri.
* Java v4 SDK 'sında [Cosmosclientbuilder. Preferredregion](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) yöntemi.
* Python SDK 'daki [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) parametresi.
* JS SDK 'daki [Cosmosclientoptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) parametresi.

Bölgesel tercihi ayarladığınızda, istemci aşağıdaki tabloda belirtildiği gibi bir bölgeye bağlanır:

|Hesap türü |Okumalar |Yazmalar |
|------------------------|--|--|
| Tek bir yazma bölgesi | Tercih edilen bölge | Birincil bölge  |
| Birden çok yazma bölgesi | Tercih edilen bölge | Tercih edilen bölge  |

**Tercih edilen bölge AYARLAMAZSANıZ** SDK istemcisi varsayılan olarak birincil bölgeye ayarlanır:

|Hesap türü |Okumalar |Yazmalar |
|------------------------|--|--|
| Tek bir yazma bölgesi | Birincil bölge | Birincil bölge |
| Birden çok yazma bölgesi | Birincil bölge  | Birincil bölge  |

> [!NOTE]
> Birincil bölge, [Azure Cosmos hesap bölgesi listesinin](distribute-data-globally.md)ilk bölgesine başvurur.
> Bölgesel tercih olarak belirtilen değerler mevcut Azure bölgeleriyle eşleşmezse, bunlar yok sayılır. Mevcut bir bölgeyle eşleşiyorsa ancak hesap bu sunucuya çoğaltılmamışsa, istemci, veya birincil bölge ile eşleşen bir sonraki tercih edilen bölgeye bağlanır.

> [!WARNING]
> Bu belgede açıklanan yük devretme ve kullanılabilirlik mantığı, istemci yapılandırmasında devre dışı bırakılabilir ve bu, Kullanıcı uygulaması kullanılabilirlik hatalarının kendisini işleyemediği takdirde önerilmez. Bu, şu şekilde sağlanabilir:
>
> * .NET v2 SDK 'da [Connectionpolicy. EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) özelliği false olarak ayarlanıyor.
> * .NET v3 SDK 'daki [Cosmosclientoptions. LimitToEndpoint](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.limittoendpoint) özelliği true olarak ayarlanıyor.
> * Java v4 SDK 'sında [Cosmosclientbuilder. endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) yöntemi false olarak ayarlanıyor.
> * Python SDK 'da [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) parametresi false olarak ayarlanıyor.
> * JS SDK 'da [Cosmosclientoptions. ConnectionPolicy. enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) parametresi false olarak ayarlanıyor.

Normal koşullarda, SDK istemcisi tercih edilen bölgeye (bölgesel bir tercih ayarlandıysa) veya birincil bölgeye (hiçbir tercih ayarlanmamışsa) bağlanır ve aşağıdaki senaryolardan herhangi biri gerçekleşmediği takdirde işlemler bu bölgeyle sınırlandıralınacaktır.

Bu durumlarda, Azure Cosmos SDK 'sını kullanan istemci günlükleri kullanıma sunar ve **işlem tanılama bilgilerinin** bir parçası olarak yeniden deneme bilgilerini içerir:

* .NET v2 SDK 'daki yanıtlarda *Requestdiagnosticsstring* özelliği.
* .NET v3 SDK 'daki yanıtlar ve özel durumlar için *Tanılama* özelliği.
* Java V4 SDK'sında yanıtlar ve özel durumlardaki *getDiagnostics()* yöntemi.

Tercih sırasına göre sonraki bölgeyi belirlerken, SDK istemcisi, tercih edilen bölgelerin önceliklerini belirlemek için hesap bölgesi listesini kullanır (varsa).

Bu olaylar sırasında SLA garantisi hakkında kapsamlı bir ayrıntı için bkz. [SLA 'lar](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Hesaptan bölge kaldırma

Bir Azure Cosmos hesabından bölge kaldırdığınızda, hesabı etkin olarak kullanan herhangi bir SDK istemcisi, bir arka uç yanıt kodu aracılığıyla bölge kaldırma işlemini algılar. İstemci daha sonra bölgesel uç noktasını kullanılamaz olarak işaretler. İstemci geçerli işlemi yeniden dener ve gelecekteki tüm işlemler, tercih sırasına göre bir sonraki bölgeye kalıcı olarak yönlendirilir. Tercih listesinin yalnızca bir girdisi varsa (veya boşsa), ancak hesabın kullanılabilir başka bölgeleri varsa, hesap listesindeki bir sonraki bölgeye yönlendirir.

## <a name="adding-a-region-to-an-account"></a>Hesaba bölge ekleme

Azure Cosmos SDK istemcisi, 5 dakikada bir, hesap yapılandırmasını okur ve farkında olan bölgeleri yeniler.

Bir bölgeyi kaldırır ve daha sonra yeniden hesaba eklerseniz, eklenen bölgenin SDK yapılandırmasında geçerli bağlı bölgeden daha yüksek bir bölgesel tercih sırası varsa, SDK bu bölgeyi kalıcı olarak kullanacak şekilde geri dönecektir. Eklenen bölge algılandıktan sonra, gelecekteki tüm istekler buna yönlendirilir.

İstemcisini tercihen Azure Cosmos hesabının sahip olmadığı bir bölgeye bağlamak üzere yapılandırırsanız, tercih edilen bölge yok sayılır. Bu bölgeyi daha sonra eklerseniz, istemci bunu algılar ve kalıcı olarak bu bölgeye geçer.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Tek bir yazma bölgesi hesabındaki yazma bölgesinin yükünü devreder

Geçerli yazma bölgesinin yük devretmesini başlatırsanız, sonraki yazma isteği bilinen bir arka uç yanıtıyla başarısız olur. Bu yanıt algılandığında, istemci yeni yazma bölgesini öğrenmekte olan hesabı sorgular ve geçerli işlemi yeniden denemeye devam eder ve gelecekteki tüm yazma işlemlerini yeni bölgeye kalıcı olarak yönlendirir.

## <a name="regional-outage"></a>Bölgesel kesinti

Hesap tek bir yazma bölgedeyse ve bir yazma işlemi sırasında bölgesel kesinti oluşursa, davranış [el ile yük devretmeyle](#manual-failover-single-region)benzerdir. Okuma istekleri veya birden çok yazma bölgesi hesabı için, davranış [bir bölgeyi kaldırmaya](#remove-region)benzerdir.

## <a name="session-consistency-guarantees"></a>Oturum tutarlılığı garantisi

[Oturum tutarlılığı](consistency-levels.md#guarantees-associated-with-consistency-levels)kullanılırken, istemcinin kendi yazma işlemlerini okuyabileceğinizin garantisi vardır. Okuma bölgesi tercihinin yazma bölgesinden farklı olduğu tek bir yazma bölgesi hesabında, kullanıcının bir yazma yaptığını ve yerel bir bölgeden okuma yaparken, yerel bölge henüz veri çoğaltmasını (hafif kısıtlama hızı kısıtlaması) almamış olabilir. Bu gibi durumlarda SDK, okuma işleminde belirli bir hatayı algılar ve oturum tutarlılığını sağlamak için birincil bölgedeki okumayı yeniden dener.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP protokolünde geçici bağlantı sorunları

Azure Cosmos SDK istemcisinin, belirli bir istek için TCP protokolünü kullanacak şekilde yapılandırıldığı senaryolarda, ağ koşullarının belirli bir uç nokta ile iletişimi geçici olarak etkilediği durumlar olabilir. Bu geçici ağ koşulları, TCP zaman aşımları ve hizmet kullanılamıyor (HTTP 503) hataları olarak yüzeydedir. İstemci, hataya sızmadan önce, isteği bir süre sonra aynı uç noktada yerel olarak yeniden deneyecek.

Kullanıcı, birden fazla bölgeyle bir tercih edilen bölge listesi yapılandırmışsa ve Azure Cosmos hesabı birden fazla yazma bölgesi veya tek bir yazma bölgesidir ve işlem bir okuma isteği ise, istemci yerel hatayı algılar ve sonraki bölgede bulunan tek işlemi tercih listesinden yeniden dener.

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanılabilirlik SLA](high-availability.md#slas-for-availability)'larını gözden geçirin.
* En son [.NET SDK 'sını](sql-api-sdk-dotnet-standard.md) kullanma
* En son [Java SDK 'sını](sql-api-sdk-java-v4.md) kullanma
* En son [Python SDK 'sını](sql-api-sdk-python.md) kullanma
* En son [node SDK 'sını](sql-api-sdk-node.md) kullanma
