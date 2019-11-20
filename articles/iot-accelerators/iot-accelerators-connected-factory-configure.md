---
title: Bağlı fabrika Topolojisini Yapılandırma-Azure | Microsoft Docs
description: Bu makalede, topolojisi dahil bağlı fabrika çözümü hızlandırıcının nasıl yapılandırılacağı açıklanır.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820127"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Bağlı fabrika çözüm Hızlandırıcısını yapılandırma

Bağlı fabrika çözümü hızlandırıcısı, kurgusal bir şirket Contoso için sanal bir panoyu gösterir. Bu şirketin küresel olarak çok sayıda küresel konumda fabrikası vardır.

Bu makalede, bağlı bir fabrika çözümünün topolojisinin nasıl yapılandırılacağını anlatan bir örnek olarak Contoso kullanılır.

## <a name="simulated-factories-configuration"></a>Sanal fabrika yapılandırması

Her contoso fabrikası, her biri üç istasyondan oluşan üretim satırlarına sahiptir. Her istasyon, belirli bir role sahip gerçek bir OPC UA sunucusudur:

* Derleme İstasyonu
* Test İstasyonu
* Paketleme İstasyonu

Bu OPC UA sunucuları OPC UA düğümlerine sahiptir ve [OPC yayımcısı](overview-opc-publisher.md) bu düğümlerin değerlerini bağlı fabrikaya gönderir. Buna aşağıdakiler dahildir:

* Geçerli güç tüketimi gibi geçerli işlemsel durum.
* Üretilen ürünlerin sayısı gibi üretim bilgileri.

Panoları, genel bir görünümden bir istasyon düzeyi görünümüne kadar contoso fabrika topolojisine ulaşmak için kullanabilirsiniz. Bağlı fabrika panosu şunları sunar:

* Topolojideki her katman için OEE ve KPI rakamları görselleştirmesi.
* İstasyonlardaki OPC UA düğümlerinin geçerli değerlerinin görselleştirilmesi.
* OEE ve KPI rakamları, istasyon düzeyinden genel düzeye kadar toplanmaktadır.
* Değerler belirli eşiklere ulaştıklarında gerçekleştirilecek uyarıların ve eylemlerin görselleştirilmesi.

## <a name="connected-factory-topology"></a>Bağlı fabrika topolojisi

Fabrikalar, üretim hatları ve istasyonların topolojisi hiyerarşidir:

* Genel düzeyin alt öğe olarak fabrika düğümleri vardır.
* Fabrikalar, üretim satırı düğümlerine alt öğe olarak sahiptir.
* Üretim satırlarında alt öğe olarak istasyon düğümleri vardır.
* İstasyonlar (OPC UA sunucuları) alt öğe olarak OPC UA düğümlerine sahiptir.

Topolojideki her düğüm, aşağıdakileri tanımlayan ortak bir özellikler kümesine sahiptir:

* Topoloji düğümü için benzersiz bir tanımlayıcı.
* Bir ad.
* Bir açıklama.
* Bir görüntü.
* Topoloji düğümünün alt öğeleri.
* OEE ve KPI rakamları için en düşük, hedef ve maksimum değerler ve yürütülecek uyarı eylemleri.

## <a name="topology-configuration-file"></a>Topoloji yapılandırma dosyası

Önceki bölümde listelenen özellikleri yapılandırmak için, bağlı fabrika çözümü [Contosotopologyıdescription. JSON](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json)adlı bir yapılandırma dosyası kullanır.

Bu dosyayı `WebApp/Contoso/Topology` klasöründeki çözüm kaynak kodunda bulabilirsiniz.

Aşağıdaki kod parçacığında `ContosoTopologyDescription.json` yapılandırma dosyasının bir ana hattı gösterilmektedir:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

 `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`ve `<station_configuration>` ortak özellikleri şunlardır:

* **Ad** (tür dizesi)

  Bir tanımlayıcı ad tanımlar, bu, bir topoloji düğümünün panoda gösterilmesi için yalnızca bir kelime olmalıdır.

* **Açıklama** (tür dizesi)

  Topoloji düğümünü daha ayrıntılı olarak açıklar.

* **Görüntü** (tür dizesi)

  Panoda topoloji düğümü hakkında bilgi gösterildiğinde gösterilecek bir görüntünün yolu.

* **Oeegenel**, **oeeperformance**, **oeeavailability**, **oeequality**, **Kpi1**, **Kpi2** (tür `<performance_definition>`)

  Bu özellikler, uyarı oluşturmak için kullanılan işletimsel şeklin en küçük, hedef ve maxhayvan değerlerini tanımlar. Bu özellikler ayrıca bir uyarı algılandığında yürütülecek eylemleri tanımlar.

`<factory_configuration>` ve `<production_line_configuration>` öğelerinin bir özelliği vardır:

* **GUID** (tür dizesi)

  Topoloji düğümünü benzersiz şekilde tanımlar.

`<factory_configuration>` bir özelliğe sahiptir:

* **Konum** (tür `<location_definition>`)

  Fabrikasının bulunduğu yeri belirtir.

`<station_configuration>` özellikler vardır:

* **Opcuri** (dize türü)

  Bu özellik OPC UA sunucusunun OPC UA uygulama URI 'sine ayarlanmalıdır.
  OPC UA belirtimi tarafından genel olarak benzersiz olması gerektiğinden, bu özellik istasyon topolojisi düğümünü belirlemek için kullanılır.

* OPC UA düğümlerinin dizisi olan **Opcnodes**(tür `<opc_node_description>`)

`<location_definition>` özellikler vardır:

* **Şehir** (tür dizesi)

  Konuma en yakın şehrin adı

* **Ülke** (tür dizesi)

  Konumun ülkesi

* **Enlem** (çift türü)

  Konumun enlem

* **Boylam** (çift türü)

  Konumun Boylam

`<performance_definition>` özellikler vardır:

* **Minimum** (tür Double)

  Değer, daha düşük olabilir. Geçerli değer bu eşiğin altındaysa, bir uyarı oluşturulur.

* **Target** (çift türü)

  Ideal hedef değeri.

* **Maksimum** (çift türü)

  Değerin ulaşabileceği üst eşik değeri. Geçerli değer bu eşiğin üstünde ise, bir uyarı oluşturulur.

* **Minimumalertactions** (tür `<alert_action>`)

  En az bir uyarıya yanıt olarak alınabilecek eylem kümesini tanımlar.

* **Maximumalertactions** (tür `<alert_action>`)

  Maksimum uyarıya yanıt olarak alınabilecek eylem kümesini tanımlar.

`<alert_action`> özellikleri vardır:

* **Tür** (dize türü)

  Uyarı eyleminin türü. Aşağıdaki türler bilinmektedir:

  * Bildirim alınan **gealert**: uyarının durumu Onaylandı olarak değiştirilmelidir.
  * **Closealert**: aynı türdeki tüm eski uyarılar artık panoda gösterilmemelidir.
  * **CallOpcMethod**: OPC UA yöntemi çağrılmalıdır.
  * **OpenWeb sayfası**: ek bağlamsal bilgileri gösteren bir tarayıcı penceresi açılmalıdır.

* **Açıklama** (tür dizesi)

  Panoda gösterilen eylemin açıklaması.

* **Parameter** (tür dizesi)

  Eylemi yürütmek için gereken parametreler. Değer, eylem türüne bağlıdır.

  * Bildiren **Gealert**: parametre gerekli değil.
  * **Closealert**: parametre gerekli değil.
  * **CallOpcMethod**: BIR OPC UA yönteminin "NodeId of Parent Node, çağrılacak yöntemin NodeId, OPC UA sunucusu URI 'si) biçiminde çağırmak için düğüm bilgileri ve parametreleri."
  * **OpenWeb sayfası**: tarayıcı PENCERESINDE gösterilecek URL.

`<opc_node_description>` bir istasyondaki OPC UA düğümleri hakkında bilgiler içerir (OPC UA sunucusu). Mevcut OPC UA düğümlerini temsil eden, ancak bağlı fabrika hesaplama mantığındaki depolama alanı olarak kullanılan düğümler de geçerlidir. Aşağıdaki özelliklere sahiptir:

* **NodeId** (tür dizesi)

  İstasyonun (OPC UA sunucusu) adres alanındaki OPC UA düğümünün adresi. Sözdizimi bir NodeId için OPC UA belirtiminde belirtilen şekilde olmalıdır.

* **Sembolicname** (tür dizesi)

  Bu OPC UA düğümünün değeri gösterildiğinde panoda gösterilecek ad.

* **İlgi** (dize türünde dizi)

  OPC UA düğüm değerinin hangi şekilde kullanılacağını belirtir. Her dizi öğesi aşağıdaki değerlerden biri olabilir:

  * **OeeAvailability_Running**: değer, OEE kullanılabilirliği hesaplanmasıyla ilgilidir.
  * **OeeAvailability_Fault**: değer, OEE kullanılabilirliği hesaplanmasıyla ilgilidir.
  * **OeePerformance_Ideal**: değer, OEE performansının hesaplanmasıyla ilgilidir ve genellikle sabit bir değerdir.
  * **OeePerformance_Actual**: değer, OEE performansının hesaplanmasıyla ilgilidir.
  * **OeeQuality_Good**: değer OEE kalitesi hesaplanmasıyla ilgilidir.
  * **OeeQuality_Bad**: değer OEE kalitesi hesaplanmasıyla ilgilidir.
  * **Kpi1**: değer, Kpi1 hesaplamasında geçerlidir.
  * **Kpi2**: değer, Kpi2 hesaplamasında geçerlidir.

* **Opcode** (tür dizesi)

  OPC UA düğümünün değerinin, zaman serisi Insight sorguları ve OEE/KPI hesaplamalarında nasıl işlendiğini gösterir. Her zaman, bir dizi Insight sorgusu, sorgunun bir parametresi olan ve bir sonuç sunan belirli bir TimeSpan 'yi hedefler. OpCode, sonucun nasıl hesaplanabileceğini denetler ve aşağıdaki değerlerden biri olabilir:

  * **Fark**: TimeSpan içindeki Last ve First değeri arasındaki fark.
  * **AVG**: TimeSpan içindeki tüm değerlerin ortalaması.
  * **Sum**: TimeSpan içindeki tüm değerlerin toplamı.
  * **Son**: Şu anda kullanılmıyor.
  * **Sayı**: TimeSpan içindeki değer sayısı.
  * **Max**: TimeSpan içindeki maxhayvan değeri.
  * **Min**: TimeSpan içindeki minimum değer.
  * **Const**: sonuç, constValue özelliği tarafından belirtilen değerdir.
  * **Submaxmin**: maxhayvan ve en düşük değer arasındaki fark.
  * **TimeSpan**: TimeSpan.

* **Birimler** (tür dizesi)

  Panoda görüntülenmek üzere bir değer birimi tanımlar.

* **Visible** (tür Boolean)

  Değerin panoda gösterilmesi gerekip gerekmediğini denetler.

* **ConstValue** (çift tür)

  **Opcode** **const**ise, bu özellik düğümün değeridir.

* **Minimum** (tür Double)

  Geçerli değer bu değerin altına düşerse, en az bir uyarı oluşturulur.

* **Maksimum** (çift türü)

  Geçerli değer bu değerin üzerine yükselirse, en fazla bir uyarı oluşturulur.

* **Minimumalertactions** (tür `<alert_action>`)

  En az bir uyarıya yanıt olarak alınabilecek eylem kümesini tanımlar.

* **Maximumalertactions** (tür `<alert_action>`)

  Maksimum uyarıya yanıt olarak alınabilecek eylem kümesini tanımlar.

İstasyon düzeyinde **simülasyon** nesnelerini de görürsünüz. Bu nesneler yalnızca bağlı fabrika simülasyonu yapılandırmak için kullanılır ve gerçek bir topolojiyi yapılandırmak için kullanılmamalıdır.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Yapılandırma verileri çalışma zamanında nasıl kullanılır

Yapılandırma dosyasında kullanılan tüm özellikler, kullanıldıkları ayarlara bağlı olarak farklı kategorilerde gruplandırılabilir. Bu kategoriler şunlardır:

### <a name="visual-appearance"></a>Görsel görünüm

Bu kategorideki Özellikler bağlı fabrika panosunun görsel görünümünü tanımlar. Örneklere şunlar dahildir:

* Ad
* Açıklama
* Görüntü
* Konum
* Birimler
* Görüne

### <a name="internal-topology-tree-addressing"></a>İç topoloji ağacı adresleme

WebApp, tüm topoloji düğümlerinin bilgilerini içeren bir iç veri sözlüğünü tutar. Properties **Guid** ve **opcuri** , bu sözlüğe erişmek ve benzersiz olması gereken anahtarlar olarak kullanılır.

### <a name="oeekpi-computation"></a>OEE/KPI hesaplaması

Bağlı fabrika simülasyonu için OEE/KPI rakamları şu şekilde parametreleştiriyordu:

* Hesaplamaya dahil edilecek OPC UA düğüm değerleri.
* Şekil telemetri değerlerinden nasıl hesaplanır.

Bağlı fabrika, [http://www.oeefoundation.org](http://www.oeefoundation.org)tarafından yayınlanan OEE formüllerini kullanır.

İstasyonlardaki OPC UA düğüm nesneleri OEE/KPI hesaplamasında kullanım için etiketlemeyi etkinleştirir. **İlgi** özelliği, OPC UA düğüm değerinin kullanılması gereken OEE/KPI şeklini belirtir. **Opcode** özelliği değerin hesaplamada nasıl ekleneceğini tanımlar.

### <a name="alert-handling"></a>Uyarı işleme

Bağlı fabrika basit en düşük/en yüksek eşik tabanlı uyarı oluşturma mekanizmasını destekler. Bu uyarılara yanıt olarak yapılandırabileceğiniz bir dizi önceden tanımlanmış eylem vardır. Aşağıdaki özellikler bu mekanizmayı denetler:

* Maksimum
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Telemetri verileriyle ilişkilendirme

Son değeri görselleştirme veya zaman serisi Insight sorguları oluşturma gibi belirli işlemler için WebApp, alınan telemetri verileri için bir adres düzeni gerektirir. Bağlı fabrikaya gönderilen Telemetriyi iç veri yapılarında da depolanması gerekir. Bu işlemleri etkinleştiren iki özellik istasyon (OPC UA sunucusu) ve OPC UA düğüm düzeyi:

* **OpcUri**

  Telemetrinin geldiği OPC UA sunucusunu tanımlar (genel olarak benzersiz). Alınan iletilerde, bu özellik **ApplicationUri**olarak gönderilir.

* **NodeId**

  OPC UA sunucusunda düğüm değerini tanımlar. Özelliğin biçimi OPC UA belirtiminde belirtilen şekilde olmalıdır. Alınan iletilerde, bu özellik **NodeId**olarak gönderilir.

Telemetri verilerinin bağlı fabrikaya nasıl yapılacağı hakkında daha fazla bilgi için bkz. [OPC yayımcısı nedir?](overview-opc-publisher.md) .

## <a name="example-how-kpi1-is-calculated"></a>Örnek: KPI1 nasıl hesaplanır?

`ContosoTopologyDescription.json` dosyadaki yapılandırma, OEE/KPI rakamlarını nasıl hesaplanacağını denetler. Aşağıdaki örnek, bu dosyadaki özelliklerin KPI1 hesaplamasını denetleme şeklini gösterir.

Bağlı fabrika KPI1, son bir saat içinde başarıyla üretilmiş ürünlerin sayısını ölçmek için kullanılır. Bağlı fabrika simülasyonu içindeki her istasyon (OPC UA sunucusu), bu KPI 'Yı hesaplamak için telemetri sağlayan bir OPC UA düğümü (`NodeId: "ns=2;i=385"`) sağlar.

Bu OPC UA düğümünün yapılandırması aşağıdaki kod parçacığına benzer şekilde görünür:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Bu yapılandırma, Time Series Insights kullanarak bu düğümün telemetri değerlerinin sorgulanmasını sunar. Time Series Insights sorgu şunu alır:

* Değer sayısı.
* En küçük değer.
* Maxhayvan değeri.
* Tüm değerlerin ortalaması.
* Tüm benzersiz **Opcuri** (**ApplicationUri**) için tüm değerlerin toplamı, belirli bir TimeSpan içindeki **NodeId** çiftleri.

**Numberofmanufactureredproducts** düğümü değerinin bir özelliği yalnızca artar. Zaman aralığı içinde üretilen ürünlerin sayısını hesaplamak için bağlı fabrika, **submaxmin** **işlem** kodunu kullanır. Hesaplama, zaman yayılımı başlangıcında en küçük değeri ve TimeSpan 'nin sonundaki maksimum değeri alır.

Yapılandırmadaki **Opcode** , maksimum ve minimum değer farkının sonucunu hesaplamak için hesaplama mantığını yapılandırır. Bu sonuçlar daha sonra kök (genel) düzeyine kadar biriktirilir ve panoda gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

Önerilen bir sonraki adım, [bağlı fabrika çözümünü nasıl özelleştireceğinizi](iot-accelerators-connected-factory-customize.md)öğrenirsiniz.
