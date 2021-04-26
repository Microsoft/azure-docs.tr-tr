---
title: SAP Solutions sağlayıcıları için Azure Izleyici | Microsoft Docs
description: Bu makalede, SAP çözüm sağlayıcıları için Azure izleyici hakkında sık sorulan sorulara yanıtlar verilmektedir.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: fe8ac81a8b04aa88ce91a978c2bc9b979a065370
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576168"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>SAP Solutions sağlayıcıları için Azure izleyici (Önizleme)

## <a name="overview"></a>Genel Bakış  

SAP Çözümleri için Azure Izleyici bağlamında, *sağlayıcı türü* belirli bir *sağlayıcıyı* ifade eder. Örneğin, SAP yatay içindeki belirli bir bileşen için SAP HANA veritabanı gibi yapılandırılmış *SAP HANA*. Sağlayıcı, ilgili bileşene ait bağlantı bilgilerini içerir ve bu bileşenden telemetri verileri toplamaya yardımcı olur. SAP Çözümleri kaynağı (SAP izleme kaynağı olarak da bilinir) için bir Azure Izleyici, aynı sağlayıcı türünün veya birden çok sağlayıcı türündeki birden çok sağlayıcı ile yapılandırılabilir.
   
Müşteriler, SAP yatay içindeki ilgili bileşenden veri toplamayı etkinleştirmek için farklı sağlayıcı türlerini yapılandırmayı seçebilirler. Örneğin, müşteriler SAP HANA sağlayıcı türü için bir sağlayıcıyı, yüksek kullanılabilirlik kümesi sağlayıcısı türü için başka bir sağlayıcıyı yapılandırabilir ve bu şekilde devam edebilir.  

Müşteriler aynı SAP izleyici kaynağını ve ilişkili yönetilen grubu yeniden kullanmak için belirli bir sağlayıcı türünün birden çok sağlayıcısını yapılandırmayı da tercih edebilir. Yönetilen kaynak grubu hakkında daha fazla bilgi edinin. Genel önizleme için aşağıdaki sağlayıcı türleri desteklenir:   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Yüksek kullanılabilirlik kümesi
- Operating System

![SAP Solutions sağlayıcıları için Azure Izleyici](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

Müşteriler, SAP Monitor kaynağını dağıtma sırasında kullanılabilir sağlayıcı türlerinden en az bir sağlayıcıyı yapılandırmak için önerilir. Bir sağlayıcı yapılandırarak, müşteriler sağlayıcının yapılandırıldığı ilgili bileşenden veri toplamayı başlatır.   

Müşteriler SAP izleyici kaynağını dağıtırken herhangi bir sağlayıcı yapılandırmadığında, SAP izleyici kaynağı başarıyla dağıtılırsa, hiçbir telemetri verisi toplanmaz. Müşterilerin, Azure portal içindeki SAP Monitor kaynağı aracılığıyla dağıtımdan sonra sağlayıcı ekleme seçeneği vardır. Müşteriler, her zaman SAP Monitor kaynağından sağlayıcı ekleyebilir veya silebilir.

## <a name="provider-type-sap-netweaver"></a>Sağlayıcı türü: SAP NetWeaver

Müşteriler SAP NetWeaver katmanından veri toplamayı etkinleştirmek için bir veya daha fazla dizi sağlayıcı türü SAP NetWeaver yapılandırabilir. AMS NetWeaver sağlayıcısı, uygun telemetri bilgilerini almak için mevcut [Sapcontrol WebService](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) arabirimini kullanır.

Geçerli yayın için aşağıda, AMS tarafından çağrılan Standart kullanıma hazır SOAP Web yöntemleri verilmiştir.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

Genel önizlemede, müşteriler SAP NetWeaver sağlayıcısı ile aşağıdaki verileri görmeyi bekleyebilir: 
- Sistem ve örnek kullanılabilirliği
- İş süreci kullanımı
- Kuyruk kullanımı
- Kilit istatistiklerini sıraya alma.

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Sağlayıcı türü: SAP HANA

Müşteriler, SAP HANA veritabanından veri toplamayı etkinleştirmek için *SAP HANA* bir veya daha fazla sağlayıcı türü sağlayıcısı yapılandırabilir. SAP HANA sağlayıcı, SQL bağlantı noktası üzerinden SAP HANA veritabanına bağlanır, veri kaynağından telemetri verilerini çeker ve müşteri aboneliğinde Log Analytics çalışma alanına gönderir. SAP HANA sağlayıcısı SAP HANA veritabanından her 1 dakikada bir veri toplar.  

Genel önizlemede, müşteriler SAP HANA sağlayıcı ile aşağıdaki verileri görmeyi bekleyebilir: temel altyapı kullanımı, SAP HANA ana bilgisayar durumu, SAP HANA sistem çoğaltması ve SAP HANA yedekleme telemetri verileri. SAP HANA sağlayıcıyı yapılandırmak için ana bilgisayar IP adresi, HANA SQL bağlantı noktası numarası ve SYSTEMDB Kullanıcı adı ve parola gereklidir. Müşterilerin SAP HANA sağlayıcısı 'nı SYSTEMDB 'ye karşı yapılandırmak önerilir, ancak diğer veritabanı kiracılarına göre daha fazla sağlayıcı yapılandırılabilir.

![SAP Solutions sağlayıcıları için Azure Izleyici-SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Sağlayıcı türü: Microsoft SQL Server

Müşteriler, [sanal makinelerde SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)veri toplamayı etkinleştirmek için *Microsoft SQL Server* bir veya daha fazla sağlayıcı türü sağlayıcısı yapılandırabilir. SQL Server sağlayıcı, SQL bağlantı noktası üzerinden Microsoft SQL Server bağlanır, veritabanından telemetri verileri çeker ve müşteri aboneliğinde Log Analytics çalışma alanına gönderir. SQL Server, SQL kimlik doğrulaması için yapılandırılmalı ve bir SQL Server oturum açması gerekir ve bu, sağlayıcının varsayılan veritabanı olarak SAP DB ile oluşturulmalıdır. SQL Server sağlayıcı, SQL Server 'dan her saate kadar her 60 saniyede veri toplar.  

Genel önizlemede, müşteriler SQL Server sağlayıcı ile aşağıdaki verileri görmeyi bekleyebilir: temeldeki altyapı kullanımı, popüler SQL deyimleri, en büyük tablo, SQL Server hata günlüklerinde kaydedilen sorunlar ve diğer sorunları engelleme.  

Microsoft SQL Server sağlayıcıyı yapılandırmak için, SAP sistem KIMLIĞI, ana bilgisayar IP adresi, SQL Server bağlantı noktası numarası ve SQL Server oturum açma adı ve parolası gerekir.

![SAP Solutions sağlayıcıları için Azure Izleyici-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Sağlayıcı türü: yüksek kullanılabilirlik kümesi
Müşteriler, bir veya daha fazla sağlayıcı türü *yüksek kullanılabilirlik kümesi* SAĞLAYıCıSıNı, SAP yatay Içindeki pacemaker kümesinden veri toplamayı etkinleştirecek şekilde yapılandırabilir. Yüksek kullanılabilirlik kümesi sağlayıcısı, [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) uç noktası kullanarak pacemaker 'a bağlanır, verileri veritabanından çeker ve müşteri aboneliğinde Log Analytics çalışma alanına gönderir. Yüksek kullanılabilirlik kümesi sağlayıcısı, pacemaker 'dan her 60 saniyede bir veri toplar.  

Genel önizlemede, müşteriler yüksek kullanılabilirlik kümesi sağlayıcısıyla aşağıdaki verileri görmeyi bekleyebilir:   
 - Düğüm ve kaynak durumu toplama olarak temsil edilen küme durumu 
 - [bilgisayarlardan](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![SAP çözüm sağlayıcıları için Azure Izleyici-yüksek kullanılabilirlik kümesi](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Yüksek kullanılabilirlik kümesi sağlayıcısını yapılandırmak için, iki birincil adım vardır:

1. Paceoluşturucu kümesi içindeki *her* bir düğüme [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) yükler.

   Ha_cluster_exporter yüklemek için iki seçeneğiniz vardır:
   
   - Yüksek kullanılabilirlik kümesi dağıtmak için Azure Otomasyonu betikleri kullanın. Betikler her bir küme düğümüne [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) yükler.  
   - [El ile yükleme](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build)yapın. 

2. Paceoluşturucu kümesi içindeki *her* düğüm için yüksek kullanılabilirliğe sahip bir küme sağlayıcısı yapılandırın.

   Yüksek kullanılabilirlik kümesi sağlayıcısını yapılandırmak için aşağıdaki bilgiler gereklidir:
   
   - **Ad**. Bu sağlayıcının adı. Bu Azure Monitor for SAP Solutions örneği için benzersiz olmalıdır.
   - **Prometheus uç noktası**. http \: // \<servername or ip address\> : 9664/ölçümler.
   - **SID**. SAP sistemleri için SAP SID 'sini kullanın. Diğer sistemler (örneğin, NFS kümeleri) için, küme için üç karakterli bir ad kullanın. SID, izlenen diğer kümelerden farklı olmalıdır.   
   - **Küme adı**. Küme oluşturulurken kullanılan küme adı. Küme adı küme özelliğinde bulunabilir `cluster-name` .
   - **Ana bilgisayar adı**. VM 'nin Linux ana bilgisayar adı.  

## <a name="provider-type-os-linux"></a>Sağlayıcı türü: OS (Linux)
Müşteriler, BareMetal veya VM düğümünden veri toplamayı etkinleştirmek için bir veya daha fazla sağlayıcı türü işletim sistemi (Linux) sağlayıcısı yapılandırabilir. İşletim sistemi (Linux) sağlayıcısı, [Node_Exporter](https://github.com/prometheus/node_exporter)uç noktası kullanarak BareMetal veya VM düğümlerine bağlanır   , düğümlerdeki telemetri verilerini çeker ve müşteri aboneliğinde Log Analytics çalışma alanına gönderir. İşletim sistemi (Linux) sağlayıcısı, düğümlerden çoğu ölçüm için verileri her 60 saniyede bir toplar. 

Genel önizlemede, müşteriler OS (Linux) sağlayıcısı ile aşağıdaki verileri görmeyi bekleyebilir: 
   - CPU kullanımı, Işleme göre CPU kullanımı 
   - Disk kullanımı, g/ç okuma & yazma 
   - Bellek dağıtımı, bellek kullanımı, takas belleği kullanımı 
   - Ağ kullanımı, ağ gelen & giden trafik ayrıntıları. 

Bir işletim sistemi (Linux) sağlayıcısı yapılandırmak için iki birincil adım vardır:
1.  [](https://github.com/prometheus/node_exporter)   Her bir baremetal veya VM düğümlerine Node_Exporter yükler.
   [Node_exporter](https://github.com/prometheus/node_exporter)yüklemek için iki seçeneğiniz vardır: 
      - İşletim sistemi (Linux) sağlayıcısı yüklemek için her bir BareMetal veya VM düğümlerinde, anormal kullanım [Node_Exporter](https://github.com/prometheus/node_exporter) Otomasyon yüklemesi için.  
      -  [El ile yükleme](https://prometheus.io/docs/guides/node-exporter/)yapın.

2. Ortamınızdaki her bir BareMetal veya VM düğüm örneği için bir işletim sistemi (Linux) sağlayıcısı yapılandırın. 
   İşletim sistemi (Linux) sağlayıcısını yapılandırmak için aşağıdaki bilgiler gereklidir: 
      - Ada. Bu sağlayıcının adı. Bu Azure Monitor for SAP Solutions örneği için benzersiz olmalıdır. 
      - Düğüm verme uç noktası. Genellikle http:// <servername or ip address> : 9100/ölçümler 

> [!NOTE]
> 9100 Node_Exporter uç noktası için sunulan bir bağlantı noktasıdır.

> [!Warning]
> Düğüm yeniden başlatmasından sonra düğüm dışarı aktarıcı 'nın çalışmaya devam edin. 

## <a name="next-steps"></a>Sonraki adımlar

- [Ekleme adımları](./azure-monitor-sap-quickstart.md) ' nı ınceleyın ve SAP Çözümleri Için Ilk Azure izleyiciyi oluşturun kaynağını oluşturun.
- SAP Çözümleri için Azure Izleyici hakkında sorularınız mı var? [SSS](./azure-monitor-faq.md) bölümüne bakın
