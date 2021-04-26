---
title: NSG 'ler için akış günlüğüne giriş
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure ağ Izleyicisi 'nin NSG akış günlükleri özelliğinin nasıl kullanılacağı açıklanmaktadır.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f737be68a28f95ab5402ba5ea08e85fcf1b04d37
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565908"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Ağ güvenlik grupları için akış günlük kaydına giriş

## <a name="introduction"></a>Giriş

[Ağ güvenlik grubu](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) akış günlükleri, BIR NSG aracılığıyla akan IP trafiği hakkındaki bilgileri günlüğe kaydetmenize olanak sağlayan bir Azure Ağ İzleyicisi özelliğidir. Akış verileri Azure depolama hesaplarına gönderilir ve buradan erişebilirsiniz ve dilediğiniz görselleştirme aracına, SıEM 'e veya KIMLIKLERINE dışarı aktarabilirsiniz.

![akış günlüklerine genel bakış](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Akış günlükleri neden kullanılmalıdır?

Güvenliği ihlal edilmemiş güvenlik, uyumluluk ve performans için kendi ağınızı izlemek, yönetmek ve bilmek önemlidir. Kendi ortamınızı bilmeniz, korumak ve iyileştirmek için önemli öneme sahip olabilir. Genellikle ağın, bağlandığı, hangi bağlantı noktalarının internet 'e açık olduğu, ağ davranışı beklenen, düzensiz ağ davranışı ve trafikte olan ani RID 'lerin geçerli durumunu bilmeniz gerekir.

Akış günlükleri, bulut ortamınızdaki tüm ağ etkinlikleri için Truth kaynağıdır. Yaklaşan bir başlangıç, kaynakları veya büyük işletmeden yetkisiz giriş algılamaya çalışırken en iyi günlüklerdir. Ağ akışlarını iyileştirmek, aktarım hızını izlemek, uyumluluğu doğrulamak, yetkisiz erişimlere ve daha fazlasını saptamak için kullanabilirsiniz.

## <a name="common-use-cases"></a>Genel kullanım örnekleri

**Ağ izleme**: bilinmeyen veya istenmeyen trafiği belirler. Trafik düzeylerini ve bant genişliği tüketimini izleyin. Uygulama davranışını anlamak için akış günlüklerini IP ve bağlantı noktasına göre filtreleyin. İzleme panoları ayarlamak için akış günlüklerini seçtiğiniz analiz ve görselleştirme araçlarına dışarı aktarın.

**Kullanım izleme ve iyileştirme:** Ağınızdaki üst konuşmaları belirler. Bölgeler arası trafiği belirlemek için GeoIP verileriyle birleştirin. Kapasite tahmini için trafik büyümesini anlayın. Fazla kısıtlayıcı trafik kurallarını kaldırmak için verileri kullanın.

**Uyumluluk**: Ağ yalıtımını ve kurumsal erişim kurallarıyla uyumluluğu doğrulamak için akış verilerini kullanın

**Ağ güvenliği analizi & güvenlik çözümlemesi**: güvenliği aşılmış IP ve ağ arabirimlerinden ağ akışlarını çözümleyin. Akış günlüklerini dilediğiniz SıEM veya KIMLIKLER aracında dışarı aktarın.

## <a name="how-logging-works"></a>Günlüğe kaydetme nasıl kullanılır

**Anahtar Özellikler**

- Akış günlükleri, [Katman 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) ' te çalışır ve bır NSG 'ye giden ve gıden tüm IP akışlarını kaydeder
- Günlükler, Azure platformu aracılığıyla **1 dakikalık aralıklarla** toplanır ve müşteri kaynaklarını veya ağ performansını herhangi bir şekilde etkilemez.
- Günlükler JSON biçiminde yazılır ve her NSG kural temelinde giden ve gelen akışları gösterir.
- Her günlük kaydı, akış için geçerli olan ağ arabirimini (NIC), 5 demet bilgisini, trafik kararı & (yalnızca sürüm 2) aktarım hızı bilgilerini içerir. Tam Ayrıntılar için aşağıdaki _günlük biçimine_ bakın.
- Akış günlükleri, oluşturma işleminden sonra otomatik olarak bir yıla kadar olan günlükleri silmeye izin veren bir bekletme özelliğine sahiptir. 

> [!NOTE]
> Saklama, yalnızca [genel amaçlı v2 depolama hesapları (GPv2)](../storage/common/storage-account-overview.md#types-of-storage-accounts)kullandığınızda kullanılabilir. 

**Temel kavramlar**

- Yazılım tanımlı ağlar, sanal ağların (VNet) ve alt ağların etrafında düzenlenir. Bu sanal ağlar ve alt ağların güvenliği NSG kullanılarak yönetilebilir.
- Bir ağ güvenlik grubu (NSG), bağlı olduğu kaynaklarda ağ trafiğine izin veren veya reddeden _güvenlik kurallarının_ bir listesini içerir. NSG 'Ler, VM 'lere bağlı olan alt ağlar, tek VM 'Ler veya tek ağ arabirimleri (NIC) ile ilişkilendirilebilir (Kaynak Yöneticisi). Daha fazla bilgi için bkz. [ağ güvenlik grubuna genel bakış](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Ağınızdaki tüm trafik akışları, geçerli NSG kuralları kullanılarak değerlendirilir.
- Bu değerlendirmelerinin sonucu NSG akış günlüklerinizi de kaydeder. Akış günlükleri Azure platformu aracılığıyla toplanır ve müşteri kaynaklarında herhangi bir değişiklik yapılmasını gerektirmez.
- Note: kurallar, her biri farklı günlüğe kaydetme davranışlarına sahip olan & Sonlandırılmamış olan iki türden oluşur.
- - NSG reddetme kuralları sonlandırılıyor. Trafiği reddetme NSG 'si, bu durumda akış günlüklerinde ve işlenmek üzere trafiği reddettikten sonra durur. 
- - NSG Izin verme kuralları Sonlandırılmamış, yani bir NSG izin veriyorsa, işleme sonraki NSG 'ye devam eder. Son NSG trafiğe izin veren trafiği akış günlüklerine kaydeder.
- NSG akış günlükleri, erişilebilen depolama hesaplarına yazılır.
- TA, splunk, Grafana, Stealthwatch gibi araçları kullanarak akış günlüklerini dışa aktarabilir, işleyebilir, çözümleyebilir ve görselleştirebilirsiniz.

## <a name="log-format"></a>Günlük biçimi

Akış günlükleri aşağıdaki özellikleri içerir:

* etkinliğin günlüğe kaydedildiği **saat**
* **SystemId** -ağ güvenlik grubu sistem kimliği.
* **Kategori** -etkinliğin kategorisi. Kategori her zaman **Networksecuritygroupflowevent**
* **RESOURCEID** -NSG kaynak kimliği
* **OperationName** -Always NetworkSecurityGroupFlowEvents
* **Özellikler** -akışın özelliklerinin bir koleksiyonu
    * Akış günlüğü olay şemasının **Sürüm** numarası
    * **akışlar** -akış koleksiyonu. Bu özelliğin farklı kurallar için birden çok girişi vardır
        * akışların listelendiği **kural** kuralı
            * **akışlar** -akış koleksiyonu
                * **Mac** -AKıŞıN toplandığı VM için NIC 'in MAC adresi
                * **flowtuple** -akış kayıt düzeni için virgülle ayrılmış biçimde birden çok özellik içeren bir dize
                    * **Zaman damgası** -bu değer, akışın UNIX dönem biçiminde gerçekleştiği zaman damgasıdır
                    * **Kaynak IP** -kaynak IP 'si
                    * **Hedef IP** -hedef IP
                    * **Kaynak bağlantı noktası** -kaynak bağlantı noktası
                    * **Hedef bağlantı noktası** -hedef bağlantı noktası
                    * **Protokol** -akışın protokolü. Geçerli değerler TCP için **T** ve UDP için **U**
                    * **Trafik akışı** -trafik akışının yönü. Giden **ve çıkış için geçerli** değerler **ı** .
                    * **Trafik kararı** -trafiğe izin verilip verilmediğini belirtir. Geçerli **değerler, izin** verilen ve **D** için için geçerlidir.
                    * **Flow durumu-yalnızca sürüm 2** -akışın durumunu yakalar. Olası durumlar **B**: bir akış oluşturulduğunda başlar. İstatistikler sağlanmamış. **C**: devam eden bir akış için devam ediliyor. İstatistikler 5 dakikalık aralıklarla sağlanır. **E**: bir akış sonlandırıldığında sonlandırın. İstatistikler sağlanır.
                    * **Paketler-kaynak-yalnızca hedef sürüm 2** Son güncelleştirmeden bu yana kaynaktan hedefe gönderilen TCP paketlerinin toplam sayısı.
                    * **Gönderilen bayt-kaynak-yalnızca hedef sürüm 2 ' ye** Son güncelleştirmeden bu yana kaynaktan hedefe gönderilen TCP paket baytlarının toplam sayısıdır. Paket baytları paket üst bilgisini ve yükünü içerir.
                    * **Paketler-hedefe yalnızca kaynak-sürüm 2** Son güncelleştirmeden bu yana Hedefin kaynaktan gönderilen toplam TCP paketi sayısı.
                    * **Gönderilen bayt-hedef-yalnızca kaynak-sürüm 2** Son güncelleştirmeden bu yana, hedefin kaynağa gönderdiği TCP paket baytlarının toplam sayısı. Paket baytları paket üst bilgisini ve yükünü içerir.


**NSG akış günlükleri sürüm 2 (vs sürüm 1)** 

Günlüklerin 2. sürümü, akış durumu kavramını tanıtır. Aldığınız akış günlüklerinin sürümünü yapılandırabilirsiniz.

Akış durumu _B_ , bir akış başlatıldığında kaydedilir. Flow durumu _C_ ve Flow durumu _E_ , sırasıyla akış ve akış sonlandırmasının devamlılığını işaretleyen durumlardır. Hem _C_ hem de _E_ durumları trafik bant genişliği bilgilerini içerir.

### <a name="sample-log-records"></a>Örnek günlük kayıtları

Aşağıdaki metin akış günlüğüne bir örnektir. Gördüğünüz gibi, önceki bölümde açıklanan özellik listesini izleyen birden çok kayıt vardır.

> [!NOTE]
> *Flowdiziler* özelliğindeki değerler, virgülle ayrılmış bir liste.
 
**Sürüm 1 NSG akış günlüğü biçim örneği**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**Sürüm 2 NSG akış günlüğü biçim örneği**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Açıklanan günlük kayıt düzeni**

![akış günlükleri kayıt düzeni](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Örnek bant genişliği hesaplaması**

185.170.185.105:35370 ve 10.2.0.4:23 arasında bir TCP görüşmesinde akış başlıkları:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, ı, A, E, 52, 29952, 47, 27072"

Devamlılık _C_ ve bitiş _E_ akışı durumları için bayt ve paket sayıları, önceki akış kayıt kümesi kaydı zamanından itibaren toplam sayılardır. Önceki örnek konuşmaya başvurarak, aktarılan toplam paket sayısı 1021 + 52 + 8005 + 47 = 9125 olur. Aktarılan toplam bayt sayısı 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>NSG Akış Günlüklerini etkinleştirme

Akış günlüklerini etkinleştirme yönergeleri için aşağıda yer alan ilgili bağlantıyı kullanın.

- [Azure portalı](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Parametreleri güncelleştirme

**Azure portalı**

Azure portal, ağ Izleyicisi 'nde NSG akış günlükleri bölümüne gidin. Sonra NSG adına tıklayın. Bu işlem, akış günlüğü için ayarlar bölmesini getirir. İstediğiniz parametreleri değiştirin ve değişiklikleri dağıtmak için **Kaydet** 'e basın.

**PS/CLı/REST/ARM**

Komut satırı araçları aracılığıyla parametreleri güncelleştirmek için, akış günlüklerini etkinleştirmek için kullanılan komutu kullanın, ancak değiştirmek istediğiniz güncelleştirilmiş parametreleri kullanın.

## <a name="working-with-flow-logs"></a>Akış günlükleriyle çalışma

*Akış günlüklerini okuma ve dışarı aktarma*

- [&amp;Portalda görünüm akışı günlüklerini indirme](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [PowerShell işlevlerini kullanarak akış günlüklerini okuma](./network-watcher-read-nsg-flow-logs.md)
- [NSG akış günlüklerini splunk 'a dışarı aktarma](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Akış günlükleri NSG 'leri hedeflerse, diğer Günlükler ile aynı görüntülenmez. Akış günlükleri yalnızca bir depolama hesabı içinde depolanır ve aşağıdaki örnekte gösterilen günlük yolunu izler:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Akış günlüklerini görselleştirme*

- [Azure Trafik Analizi](./traffic-analytics.md) , akış günlüklerini Işlemek için Azure yerel bir hizmettir, öngörüleri ayıklar ve akış günlüklerini görselleştirin. 
- [Öğreticide NSG akış günlüklerini Power BI ile görselleştirin](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Öğreticide Esnek Stack ile NSG akış günlüklerini görselleştirme](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Öğreticide Grafana kullanarak NSG akış günlüklerini yönetme ve çözümleme](./network-watcher-nsg-grafana.md)
- [Öğreticide GG akış günlüklerini gri günlük kullanarak yönetme ve çözümleme](./network-watcher-analyze-nsg-flow-logs-graylog.md)

*Akış günlüklerini devre dışı bırak*

Akış günlüğü devre dışı bırakıldığında, ilişkili NSG için akış günlüğü durdurulur. Ancak, bir kaynak olarak akış günlüğü, tüm ayarları ve ilişkilendirmeleriyle birlikte var olmaya devam eder. Yapılandırılan NSG 'de akış günlüğü oluşturmaya başlamak için dilediğiniz zaman etkinleştirilebilir. Akış günlüklerini devre dışı bırakma/etkinleştirme adımları, [Bu nasıl yapılır kılavuzunda](./network-watcher-nsg-flow-logging-powershell.md)bulunabilir.  

*Akış günlüklerini silme*

Akış günlüğü silindiğinde, yalnızca ilişkili NSG için akış günlüğü durdurulur, ancak akış günlüğü kaynağı da ayarları ve ilişkilendirmeleriyle silinir. Akış günlüğüne kaydetmeyi yeniden başlatmak için, bu NSG için yeni bir akış günlüğü kaynağı oluşturulması gerekir. Akış günlüğü, [PowerShell](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkwatcherflowlog), [CLI](https://docs.microsoft.com/cli/azure/network/watcher/flow-log#az_network_watcher_flow_log_delete) veya [REST API](https://docs.microsoft.com/rest/api/network-watcher/flowlogs/delete)kullanılarak silinebilir. Azure portal akış günlüklerini silme desteği ardışık düzen.    

Ayrıca, bir NSG silindiğinde, varsayılan olarak ilişkili akış günlüğü kaynağı silinir.

> [!NOTE]
> Bir NSG 'yi farklı bir kaynak grubuna veya aboneliğe taşımak için, ilişkili akış günlüklerinin silinmesi gerekir, yalnızca akış günlüklerinin devre dışı bırakılması çalışmaz. NSG geçişinden sonra, akış günlüğü kaydının etkinleştirilmesi için akış günlüklerinin yeniden oluşturulması gerekir.  

## <a name="nsg-flow-logging-considerations"></a>NSG akış günlüğü konuları

**Depolama hesabı konuları**: 

- Konum: kullanılan depolama hesabı NSG ile aynı bölgede olmalıdır.
- Performans katmanı: Şu anda yalnızca Standart katman depolama hesapları desteklenmektedir.
- Kendi kendine Yönetim anahtar döndürme: erişim anahtarlarını depolama hesabınıza değiştirirseniz/döndürdüğünüzde NSG akış günlükleri çalışmayı durdurur. Bu sorunu onarmak için NSG akış günlüklerini devre dışı bırakıp yeniden etkinleştirmeniz gerekir.

**Akış günlüğü maliyetleri**: NSG akış günlüğü, üretilen günlüklerin hacminde faturalandırılır. Yüksek trafik hacmi, büyük akış günlüğü hacmine ve ilişkili maliyetlere yol açabilir. NSG akış günlüğü fiyatlandırması, depolamanın temel maliyetlerini içermez. NSG akış günlüğü ile bekletme ilkesi özelliğinin kullanılması, uzun süreli depolama maliyetlerinin gerçek zamanlı olarak ayrılması anlamına gelir. Verileri süresiz olarak saklamak ve herhangi bir bekletme ilkesi uygulamak istemiyorsanız, bekletme (gün) seçeneğini 0 olarak ayarlayın. Daha fazla bilgi için bkz. [ağ Izleyicisi fiyatlandırması](https://azure.microsoft.com/pricing/details/network-watcher/) ve [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/) ek ayrıntılar için.

**Kullanıcı tanımlı gelen TCP kuralları Ile Ilgili sorunlar**: [ağ güvenlik grupları (NSG 'Ler)](../virtual-network/network-security-groups-overview.md) [durum bilgisi içeren bir güvenlik duvarı](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)olarak uygulanır. Ancak, geçerli platform sınırlamaları nedeniyle, gelen TCP akışlarını etkileyen Kullanıcı tanımlı kurallar durum bilgisiz bir biçimde uygulanır. Bu nedenle, Kullanıcı tanımlı gelen kuralların etkilediği akışlar Sonlandırılmamış hale gelir. Ayrıca, bu akışlar için bayt ve paket sayıları kaydedilmez. Sonuç olarak, NSG akış günlüklerinde (ve Trafik Analizi) raporlanan bayt ve paketlerin sayısı gerçek numaralardan farklı olabilir. Bu sorunları düzelten bir kabul etme bayrağı en son 2021 Mart 'ta kullanılabilir. Bu durumda, bu davranış nedeniyle önemli sorunlara yönelik müşterilere yönelik olan müşteriler destek aracılığıyla isteğe bağlı olarak talep edebilir, lütfen ağ Izleyicisi > NSG akış günlükleri altında bir destek isteği yükseltin.  

**Internet IP 'lerinden ortak IP Içermeyen VM 'lere kaydedilen gelen akışlar**: bir genel IP adresi, örnek DÜZEYI genel IP olarak NIC ile ilişkili bir genel IP adresi aracılığıyla atanmamış veya temel bir yük dengeleyici arka uç havuzunun parçası olan VM 'ler, [varsayılan SNAT](../load-balancer/load-balancer-outbound-connections.md) 'yi kullanın ve giden bağlantıyı kolaylaştırmak için Azure tarafından atanmış bir IP adresine sahip olmalıdır. Sonuç olarak, akış, SNAT için atanan bağlantı noktası aralığındaki bir bağlantı noktasına gidiyor ise internet IP adreslerinden akışlar için akış günlüğü girişleri görebilirsiniz. Azure bu akışlara sanal makineye izin vermediğinden, deneme günlüğe kaydedilir ve tasarıma göre ağ Izleyicisi 'nin NSG akış günlüğünde görüntülenir. İstenmeyen gelen internet trafiğinin NSG ile açıkça engellenmesini öneririz.

**Application Gateway v2 alt ağ NSG Ile sorun**: Application Gateway v2 alt ağ NSG 'de akış günlüğü oluşturma şu anda [desteklenmiyor](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) . Bu sorun Application Gateway v1 'yi etkilemez.

**Uyumsuz hizmetler**: geçerli platform sınırlamaları nedeniyle, NSG akış günlükleri tarafından desteklenmeyen küçük bir Azure Hizmetleri kümesi desteklenmez. Uyumsuz hizmetlerin geçerli listesi
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>En iyi uygulamalar

**Kritik VNET 'lerde/alt ağlarda etkinleştir**: akış günlüklerinin, aboneliğinizdeki tüm kritik VNET 'lerde/alt ağlarda bir denetlenebilirlik ve Security en iyi uygulaması olarak etkinleştirilmesi gerekir. 

**Bir kaynağa bağlı olan tüm NSG 'ler için NSG akış günlüğünü etkinleştirme**: NSG kaynağında Azure 'da akış günlüğü yapılandırılır. Akış yalnızca bir NSG kuralıyla ilişkilendirilecektir. Birden çok NSG'nin kullanıldığı senaryolarda, tüm trafiğin kaydedildiğinden emin olmak için kaynağın alt ağında veya ağ arabiriminde uygulanmış tüm NSG'lerde NSG akış günlüklerini etkinleştirmenizi öneririz. Daha fazla bilgi için bkz. trafiğin ağ güvenlik gruplarında [nasıl değerlendirildiği](../virtual-network/network-security-group-how-it-works.md) . 

Birkaç yaygın senaryo:
1. **BIR VM 'de birden çok NIC**: bir sanal makineye birden çok NIC bağlı olması durumunda, akış günlüğü 'nün tümünde etkinleştirilmesi gerekir
1. **Hem NIC hem de alt ağ düzeyinde NSG 'ye sahip olma**: NSG 'nin NIC 'de ve alt ağ düzeyinde yapılandırılması durumunda, akış günlüğü 'nün her ikisinde de her iki durumda da etkin olması gerekir. 

**Depolama sağlama**: depolama alanı beklenen akış günlüğü birimi ile ayarlama sırasında sağlanmalıdır.

**Adlandırma**: NSG adı 80 karakter ve NSG kural adları ile 65 karakter arasında olmalıdır. Adlar karakter sınırlarını aşarsa günlüğe kaydetme sırasında kesilebilir.

## <a name="troubleshooting-common-issues"></a>Genel sorunları giderme

**NSG Akış Günlüklerini etkinleştiremedim**

- **Microsoft. Insights** kaynak sağlayıcısı kayıtlı değil

_AuthorizationFailed_ veya _GatewayAuthenticationFailed_ hatası aldıysanız aboneliğinizde Microsoft Insights kaynak sağlayıcısını etkinleştirmemiş olabilirsiniz. Microsoft Insights sağlayıcısını etkinleştirmek için [yönergeleri izleyin](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) .

**NSG Akış Günlüklerini etkinleştirdim ama depolama hesabımda verileri görmüyorum**

- **Hazırlık süresi**

NSG Akış Günlüklerinin depolama hesabınızda görüntülenmesi 5 dakika kadar sürebilir (doğru yapılandırıldıysa). [Burada açıklandığı gibi](./network-watcher-nsg-flow-logging-portal.md#download-flow-log) erişilebilen bir PT1H.json dosyası görüntülenir.

- **NSG'lerinizde trafik yok**

VM'leriniz etkin olmadığından veya NSG'lerinize giden trafiği engelleyen App Gateway yukarı akış filtreleri veya başka cihazlar olduğundan günlükleri göremezsiniz.

**NSG Akış Günlüklerini otomatikleştirmek istiyorum**

NSG Akış Günlüklerinde şu an için ARM şablonları aracılığıyla otomasyon desteği sunulmamaktadır. Daha fazla bilgi için [özellik duyurusunu](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) okuyun.

## <a name="faq"></a>SSS

**NSG akış günlükleri ne yapar?**

Azure ağ kaynakları, [ağ güvenlik grupları (NSG 'ler)](../virtual-network/network-security-groups-overview.md)ile birleştirilebilir ve yönetilebilir. NSG akış günlükleri, NSG 'larınız aracılığıyla tüm trafikle ilgili 5 demet akış bilgilerini günlüğe Kaydetetkinleştirmenizi sağlar. Ham akış günlükleri, gerektikçe daha fazla işlenebileceği, çözümlenebildiği, sorgulanan veya verilebilecekleri bir Azure depolama hesabına yazılır.

**Akış günlüklerinin kullanılması ağ gecikmesinin veya performansumu etkiler mi?**

Akış günlükleri verileri, ağ trafiğinizin yolu dışında toplanır ve bu nedenle ağ aktarım hızını veya gecikme süresini etkilemez. Ağ performansına etkiyle ilgili herhangi bir risk olmadan akış günlükleri oluşturabilir veya silebilirsiniz.

**Nasıl yaparım? bir güvenlik duvarının arkasındaki depolama hesabıyla NSG akış günlüklerini kullanmak mı istiyorsunuz?**

Bir güvenlik duvarının arkasında bir depolama hesabı kullanmak için, güvenilen Microsoft hizmetlerinin depolama hesabınıza erişmesi için bir özel durum sağlamanız gerekir:

- Portala veya [depolama hesapları sayfasından](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) depolama hesabının adını yazarak depolama hesabına gidin.
- **Ayarlar** bölümünde **güvenlik duvarları ve sanal ağlar** ' ı seçin.
- **Erişime Izin ver**' de **Seçili ağlar**' ı seçin. Ardından,  **özel durumlar**' ın altında, * * * *, güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver seçeneğinin yanındaki kutuyu işaret edin * * * *
- Zaten seçiliyse, hiçbir değişiklik yapmanız gerekmez.
- [NSG akış günlüklerine Genel Bakış sayfasında](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) hedef NSG 'nizi bulun ve NSG akış günlüklerini yukarıdaki depolama hesabı seçiliyken etkinleştirin.

Birkaç dakika sonra depolama günlüklerini denetleyebilirsiniz; güncelleştirilmiş bir TimeStamp veya yeni oluşturulmuş bir JSON dosyası görmelisiniz.

**Nasıl yaparım? bir hizmet uç noktası arkasında depolama hesabı bulunan NSG akış günlükleri mi kullanıyorsunuz?**

NSG akış günlükleri, ek yapılandırma gerektirmeden hizmet uç noktaları ile uyumludur. Sanal ağınızdaki [hizmet uç noktalarını etkinleştirme öğreticisine](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) bakın.

**Akış günlükleri sürümleri 1 & 2 arasındaki fark nedir?**

Akış günlükleri sürüm 2, _akış durumu_ kavramını tanıtır & aktarılan bayt ve paketler hakkında bilgi depolar. [Daha fazla bilgi edinin](#log-format)

## <a name="pricing"></a>Fiyatlandırma

NSG akış günlükleri, toplanan her bir günlük için ücretlendirilir ve abonelik başına 5 GB/ay boyunca ücretsiz bir katman ile gelir. Bölgenizdeki geçerli fiyatlandırma için, bkz. [Ağ İzleyicisi fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/network-watcher/).

Günlüklerin depolanması ayrı olarak ücretlendirilir, ilgili fiyatlar için bkz. [Azure Storage Blok Blobu fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/storage/blobs/) .