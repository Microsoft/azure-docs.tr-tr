---
title: Azure endüstriyel IoT verilerini ADX 'e çekme
description: Bu öğreticide, IIoT verilerini ADX 'e çekmeyi öğreneceksiniz.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787772"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Öğretici: Azure endüstriyel IoT verilerini ADX 'e çekme

Azure endüstriyel IoT (IIoT) platformu, sektör modüllerini ve bulut mikro hizmetlerini, endüstriyel varlık bulma özelliği sağlamak ve OPC UA kullanarak bu varlıklardan veri toplamak için bir dizi Azure PaaS hizmeti ile birleştirir. [Azure Veri Gezgini (ADX)](https://docs.microsoft.com/azure/data-explorer) , OPC yayımcısı aracılığıyla IoT Hub bağlı olan OPC UA sunucularından alınan verilerde esnek sorgular çalıştırmaya olanak sağlayan veri analizi özelliklerine sahip IIoT verileri için doğal bir hedefdir. Bir ADX kümesi verileri doğrudan IoT Hub aracılığıyla alabilse de, IIoT platformu, mikro hizmetlerin tam dağıtımı kullanıldığında sağlanan olay hub 'ına geçirmeden önce verileri daha yararlı hale getirmek üzere verilerin işlenmesini sağlar (IIoT platform mimarisine bakın).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * ADX 'te tablo oluşturma
> * Olay Hub 'ını ADX kümesine bağlama
> * ADX 'teki verileri analiz etme

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Verileri etkin bir şekilde sorgulamak için ADX kümesinde kullanılabilir hale getirme 

Olay Hub 'ından ileti biçimine baktığımızda (Microsoft. Azure. IIoT. OpcUa. Subscriber. modeller. MonitoredItemMessageModel sınıfı tarafından tanımlandığı gibi), ADX tablo şeması için ihtiyacımız olan yapıya bir ipucu görebiliriz.

![Yapı](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Aşağıda, verileri ADX kümesinde kullanılabilir hale getirmek ve verileri etkili bir şekilde sorgulamak için gereken adımlar verilmiştir.  
1. Bir ADX kümesi oluşturun. IIoT platformu ile sağlanan bir ADX kümeniz yoksa veya farklı bir küme kullanmak isterseniz [buradaki](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster)adımları izleyin. 
2. [Burada](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster)açıklandığı gıbı, ADX kümesinde akış alımı etkinleştirin. 
3. [Buradaki](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database)adımları izleyerek bir ADX veritabanı oluşturun.

Aşağıdaki adım için, gerekli sorguları çalıştırmak için [ADX Web arabirimini](https://docs.microsoft.com/azure/data-explorer/web-query-data) kullanacağız. Kümenizin, bağlantıda açıklandığı gibi Web arabirimine eklediğinizden emin olun.  
 
4. Giriş verilerini içine koymak için ADX içinde bir tablo oluşturun.  MonitoredItemMessageModel sınıfı ADX tablosunun şemasını tanımlamak için kullanılabilse de, verileri ilk olarak [dinamik](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)türünde bir sütun olan hazırlama tablosuna almak önerilir. Bu, verileri ve işlemeyi birden çok kullanım durumuna yönelik gereksinimlere sunan diğer tablolara (büyük olasılıkla başka veri kaynaklarıyla birleştiren) işleme konusunda daha fazla esneklik sağlar. Aşağıdaki ADX sorgusu, ' iiot_stage ' hazırlama tablosunu tek bir ' payload ' sütunuyla oluşturur,

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Ayrıca, kümenin tüm JSON iletisini, Olay Hub 'ından hazırlama tablosuna yerleştirmelerini istemek için bir JSON alma eşlemesi eklememiz gerekir.

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. Tablomız artık Olay Hub 'ından veri almaya hazırdır. 
6. Olay Hub 'ını ADX kümesine bağlamak ve hazırlama tablomızda verileri almak için [buradaki](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) yönergeleri kullanın. Yalnızca IIoT platformu tarafından sağlanan bir olay hub 'ınız zaten olduğu için bağlantıyı oluşturmanız gerekir.  
7. Bağlantı doğrulandıktan sonra, veriler tablomuza akar ve tablomızda verileri incelemeyi başlatabilmemiz için kısa bir gecikmeden sonra. 10 satırlık bir veri örneğine bakmak için ADX Web arabirimindeki aşağıdaki sorguyu kullanın. Yükteki verilerin daha önce bahsedilen MonitoredItemMessageModel sınıfına nasıl benzediğini burada görebiliriz.

![Sorgu](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Artık ' yük ' sütunundaki dinamik verileri doğrudan ayrıştırarak bu verilerde bazı analizler çalıştırmamıza izin verin. Bu örnekte, belirli bir zaman noktasında (min_t tarafından tanımlanan) bu yana alınan tüm kayıtlarda "DisplayName": "PositiveTrendData" tarafından tanımlanan telemetri ortalamasını, belirli bir zaman noktasına (değişken tarafından tanımlanır) izin ver min_t = DateTime (2020-10-23) olarak hesaplama yapacağız. iiot_stage | WHERE ToDateTime (yük. Zaman damgası) > min_t | WHERE ToString (yük. DisplayName) = = ' PositiveTrendData ' | Özetle event_avg = AVG (ToDouble (yük). Value)), bin (ToDateTime (yük. Zaman damgası), 10 milyon)
 
' Payload ' sütunumuz dinamik bir veri türü içerdiğinden, hesaplamalarımızın doğru veri türlerinde yürütülmesi için sorgu zamanında veri dönüştürme işlemi gerçekleştirmeniz gerekir.

![Yük zaman damgası](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Daha önce bahsedildiği gibi, OPC UA verilerinin bir ' Dynamic ' sütunu olan bir hazırlama tablosuna hazırlanması bize esneklik kazandırır. Bununla birlikte, veri türü dönüştürmelerinin sorgu zamanında çalıştırılması, özellikle veri hacmi büyükse ve çok sayıda eşzamanlı sorgu varsa sorguları yürütmede gecikmelere neden olabilir. Bu aşamada, sorgu zamanı veri türü dönüştürmelerinden kaçınmak için, zaten belirlenmiş olan veri türleriyle başka bir tablo oluşturarız.
 
9. Hazırlama tablosundaki dinamik ' yük ' içeriğinden sınırlı bir seçimden oluşan ayrıştırılmış veriler için yeni bir tablo oluşturun. Telemetride beklenen her türlü veri türü için bir değer sütunu oluşturduğumuzu unutmayın.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Hazırlama tablosundan gerekli verileri proje için bir işlev (veritabanı düzeyinde) oluşturun. Burada ' yük ' sütunundan ' timestamp ', ' publisherID ', ' DisplayName ', ' DataType ' ve ' NodeId ' öğelerini seçtik ve bunları ' Tag_Timestamp ', ' Tag_PublisherId ', ' Tag ', ' Tag_Datatype ', ' Tag_NodeId ' olarak projeceğiz. ' Value ' öğesi, ' DataType ' temelinde üç farklı parça olarak yansıtıldır.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

ADX içindeki veri türlerini eşleme hakkında daha fazla bilgi için buraya [bakın ve](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)ADX işlevleri için [buradan](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions)başlayabilirsiniz.
 
11. Bir güncelleştirme ilkesi kullanarak, önceki adımdan ayrıştırılmış tabloya işlevi uygulayın. Güncelleştirme [ilkesi](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) , kaynak tabloya eklenen veriler üzerinde çalışan bir dönüştürme sorgusuna göre kaynak tabloya her yeni veri eklendiğinde, ADX 'in bir hedef tabloya otomatik olarak veri eklemesini sağlar. Ayrıştırılmış tabloyu, önceki adımda oluşturduğumuz işlev tarafından tanımlanan güncelleştirme ilkesinin kaynağı olarak hedef ve aşama tablosu olarak atamak için aşağıdaki sorguyu kullanabiliriz.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Yukarıdaki sorgu yürütüldüğü anda, veriler akışa başlar ve ' iiot_parsed ' hedef tablosunu dolduracaktır. ' İiot_parsed aşağıdaki gibi verilere göz atabiliriz.

![Ayrıştırılmış tablo](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Önceki adımda yaptığımız analizler nasıl yineliyoruz? konusunda bize bakalım. belirli bir zaman noktasında (min_t tarafından tanımlanan) bu yana alınan tüm kayıtlarda, "DisplayName&quot; (&quot;PositiveTrendData") tarafından tanımlanan telemetri ortalamasını hesaplamak için 10 dakikalık zaman pencereleri Artık bir Double veri türü sütununda depolanan ' PositveTrendData ' etiketinin değerlerine sahip olduğumuz için sorgu performansında bir geliştirme beklenir.

![Analizi Yinele](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Son olarak her iki durumda da sorgu performansını karşılaştırmamıza izin verin. ADX Kullanıcı arabiriminde (sorgu sonuçlarının üzerinde bulunabilir) ' stats ' öğesini kullanarak sorguyu yürütmek için geçen süreyi bulabiliriz.  

![Sorgu performansı 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Sorgu performansı 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Ayrıştırılmış tabloyu kullanan sorgunun, hazırlama tablosu için kabaca iki kez daha hızlı olduğunu görebiliriz. Bu örnekte, küçük bir veri kümeniz vardı ve sorgu yürütme süresi üzerinde etkili olmayacak şekilde çalışan bir eşzamanlı sorgu yoktur, ancak gerçekçi bir iş yükü için performans üzerinde büyük bir etkisi olabilir. Farklı veri türlerini farklı sütunlara ayırmayı düşünmek önemlidir.

> [!NOTE] 
> Güncelleştirme Ilkesi, ilke ayarlandıktan sonra hazırlama tablosuna alınan veriler üzerinde çalışır ve önceden varolan veriler için geçerli değildir. Örneğin, güncelleştirme ilkesini değiştirmemiz gereken durumlarda dikkate alınması gerekir. Tüm ayrıntılar ADX belgelerinde bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar
Artık yapılandırmanın varsayılan değerlerini değiştirme hakkında daha fazla öğrendiğinize göre, 

> [!div class="nextstepaction"]
> [Endüstriyel IoT bileşenlerini yapılandırma](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Time Series Insights kullanarak verileri görselleştirme ve çözümleme](tutorial-visualize-data-time-series-insights.md)