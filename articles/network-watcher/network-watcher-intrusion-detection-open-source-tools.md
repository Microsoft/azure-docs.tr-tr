---
title: Açık kaynak araçları ile ağ üzerinden izinsiz giriş algılama gerçekleştirme
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure ağ Izleyicisi 'ni ve açık kaynak araçları 'nı kullanarak ağ izinsiz giriş algılama işlemini gerçekleştirme açıklanmaktadır
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b4b38112e32a55739ea14d5ff06e327819171557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014561"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Ağ Izleyicisi ve açık kaynak araçları ile ağ üzerinden izinsiz bir algılama gerçekleştirme

Paket yakalamaları, ağ izinsiz giriş algılama sistemleri (KIMLIKLER) uygulamak ve ağ güvenliği Izleme (NSM) gerçekleştirmek için önemli bir bileşendir. Paket yakalamalarını işleyen ve olası ağ yetkisiz ve kötü amaçlı etkinliklerin imzalarını gösteren çeşitli açık kaynak KIMLIĞI araçları vardır. Ağ Izleyicisi tarafından sunulan paket yakalamalarını kullanarak ağınızı herhangi bir zararlı yetkisiz kullanım veya güvenlik açığına karşı çözümleyebilirsiniz.

Bu tür açık kaynaklı bir araç, ağ trafiğini izlemek için RuleSets kullanan ve şüpheli olaylar gerçekleştiğinde uyarıları tetikleyen bir KIMLIK altyapısı olan Suricata ' dır. Suricata, çok iş parçacıklı bir altyapı sunar, yani ağ trafiği analizini daha yüksek hız ve verimlilik ile gerçekleştirebiliyor. Suricata ve özellikleri hakkında daha fazla ayrıntı için, adresindeki Web sitesini ziyaret edin https://suricata-ids.org/ .

## <a name="scenario"></a>Senaryo

Bu makalede ağ Izleyicisi, Suricata ve elastik yığın kullanarak ağ izinsiz giriş algılaması gerçekleştirmek için ortamınızın nasıl ayarlanacağı açıklanır. Ağ Izleyicisi, ağ izinsiz giriş algılaması gerçekleştirmek için kullanılan paket yakalamalarını sağlar. Suricata, belirtilen ruletehditler kümesiyle eşleşen paketlere dayanarak, paket yakalar ve uyarıları tetikler. Bu uyarılar yerel makinenizde bir günlük dosyasında depolanır. Esnek yığını kullanarak, Suricata tarafından oluşturulan günlüklerin dizini oluşturulabilir ve bir kibana panosu oluşturmak için kullanılabilir. böylece, size günlüklerin görsel bir temsilini ve olası ağ güvenlik açıklarına ilişkin öngörüleri hızlıca elde edebilirsiniz.  

![basit Web uygulaması senaryosu][1]

Açık kaynaklı araçların her ikisi de bir Azure sanal makinesinde ayarlanabilir ve bu analizi kendi Azure ağ ortamınızda gerçekleştirmenize olanak tanır.

## <a name="steps"></a>Adımlar

### <a name="install-suricata"></a>Suricata yüklemesi

Tüm diğer yükleme yöntemleri için bkz. https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. SANAL makinenizin komut satırı terminalinde aşağıdaki komutları çalıştırın:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Yüklemenizi doğrulamak için komutunu çalıştırarak `suricata -h` komutların tam listesini görüntüleyin.

### <a name="download-the-emerging-threats-ruleset"></a>Gelişen tehditler kural kümesini indirin

Bu aşamada, Suricata 'nın çalıştırması için herhangi bir kural yoktur. Ağınızda algılamak istediğiniz belirli tehditler varsa kendi kurallarınızı oluşturabilir veya Ayrıca, gelişmekte olan tehditler veya snort 'den VRT kuralları gibi bir dizi sağlayıcıdan geliştirilmiş kural kümeleri de kullanabilirsiniz. Serbestçe erişilebilen, daha önce erişilebilir tehditler kuralı kümesi kullanırız:

Kural kümesini indirin ve dizine kopyalayın:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Suricata ile işlem paketi yakalamaları

Suricata kullanarak paket yakalamalarını işlemek için aşağıdaki komutu çalıştırın:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Ortaya çıkan uyarıları denetlemek için hızlı. log dosyasını okuyun:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Elastik yığını ayarlama

Surıata 'nın ürettiği Günlükler, ağımızda neler olduğu hakkında değerli bilgiler içerirken bu günlük dosyaları, okunması ve anlaşılması en kolay yoldur. Suricata 'yı elastik Stack ile bağlayarak, günlüklerimize ilişkin neleri araymamızı, grafiklemenizi, analiz etmenizi ve türetmemizi sağlayan bir kibana panosu oluşturalım.

#### <a name="install-elasticsearch"></a>Elaa aramasını yükleme

1. 5,0 ve üzeri sürümler için esnek yığın Java 8 gerektirir. `java -version`Sürümünüzü denetlemek için komutunu çalıştırın. Java yüklü değilse, [Azure-suppored JDKs](/azure/developer/java/fundamentals/java-jdk-long-term-support)ile ilgili belgelere başvurun.

1. Sisteminiz için doğru ikili paketi indirin:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Diğer yükleme yöntemleri, [Elaun Search yüklemesinde](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html) bulunabilir

1. Şu komutla, Elaun aramasının çalıştığını doğrulayın:

    ```
    curl http://127.0.0.1:9200
    ```

    Aşağıdakine benzer bir yanıt görmeniz gerekir:

    ```
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Elastik arama yükleme hakkında daha fazla yönerge için, sayfa [yüklemeye](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html) bakın

### <a name="install-logstash"></a>Logstash 'i yükler

1. Logstash 'i yüklemek için aşağıdaki komutları çalıştırın:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Daha sonra, dosyadaki eve.jsçıktısından okumak için Logstash yapılandırması gerekir. Şunu kullanarak bir logstash. conf dosyası oluşturun:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Dosyasına aşağıdaki içeriği ekleyin (eve.jsdosyadaki yolun doğru olduğundan emin olun):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Logstash 'in dosyayı alabilmesi için eve.jsdosyasına doğru izinleri verdiğinizden emin olun.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Logstash 'i başlatmak için şu komutu çalıştırın:

    ```
    sudo /etc/init.d/logstash start
    ```

Logstash 'i yükleme hakkında daha fazla bilgi için [resmi belgelere](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) bakın

### <a name="install-kibana"></a>Kibana'yı yükleme

1. Kibana yüklemek için aşağıdaki komutları çalıştırın:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Kibana çalıştırmak için şu komutları kullanın:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Kibana Web arabiriminizi görüntülemek için şuraya gidin `http://localhost:5601`
1. Bu senaryo için, Suricata günlükleri için kullanılan dizin deseninin "logstash-*" olması gerekir

1. Kibana panosunu uzaktan görüntülemek istiyorsanız, **5601 numaralı bağlantı noktasına** erişime izin veren bir gelen NSG kuralı oluşturun.

### <a name="create-a-kibana-dashboard"></a>Kibana panosu oluşturma

Bu makalede, uyarılarınızın eğilimlerini ve ayrıntılarını görüntülemeniz için bir örnek Pano sunuyoruz.

1. Pano dosyasını buraya [, görselleştirme dosyasına ve](https://aka.ms/networkwatchersuricatavisualization)kayıtlı arama [dosyasına buradan indirebilirsiniz](https://aka.ms/networkwatchersuricatadashboard) [.](https://aka.ms/networkwatchersuricatasavedsearch)

1. Kibana 'ın **Yönetim** sekmesi altında, **kayıtlı nesneler** ' e gidin ve üç dosyayı içeri aktarın. Sonra **Pano** sekmesinden örnek panoyu açabilir ve yükleyebilirsiniz.

Kendi görselleştirmelerinizle ilgili ölçümleriniz doğrultusunda kendi görselleştirmelerinizi ve panolarınızı da oluşturabilirsiniz. Kibana 'ın [resmi belgelerinden](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)kibana görselleştirmeler oluşturma hakkında daha fazla bilgi edinin.

![kibana panosu][2]

### <a name="visualize-ids-alert-logs"></a>KIMLIKLERI görselleştirme uyarı günlükleri

Örnek Pano, Suricata uyarı günlüklerinin çeşitli görselleştirmelerini sağlar:

1. Coğrafi konuma göre (IP tarafından belirlenir) uyarı ülkelerinin/kaynak bölgelerine göre uyarı dağılımını gösteren bir harita.

    ![Coğrafi IP][3]

1. İlk 10 uyarı: en sık tetiklenen 10 uyarının ve bunların açıklamasının Özeti. Tek bir uyarıya tıkladığınızda panonun, söz konusu uyarıya ilişkin bilgilere göre filtresi yapılır.

    ![resim 4][4]

1. Uyarı sayısı: RuleSet tarafından tetiklenen toplam uyarı sayısı

    ![resim 5][5]

1. İlk 20 kaynak/hedef IP/bağlantı noktası-uyarıların üzerinde tetiklenen en iyi 20 IP ve bağlantı noktasını gösteren pasta grafikler. Belirli IP 'Lerde/bağlantı noktalarında filtreleyerek, kaç tür uyarı tetikleneceğini görebilirsiniz.

    ![resim 6][6]

1. Uyarı Özeti: her bir uyarının belirli ayrıntılarını özetleyen bir tablo. Bu tabloyu, her uyarı için diğer ilgilendiğiniz diğer parametreleri gösterecek şekilde özelleştirebilirsiniz.

    ![Görüntü 7][7]

Özel görselleştirmeler ve panolar oluşturma hakkında daha fazla bilgi için bkz. [kibana 'in resmi belgeleri](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Sonuç

Ağ Izleyicisi tarafından sunulan paket yakalamaları ve Suricata gibi açık kaynak KIMLIKLERI araçları birleştirilerek, çok çeşitli tehditler için ağ üzerinden izinsiz giriş algılama gerçekleştirebilirsiniz. Bu panolar ağınız içindeki eğilimleri ve anormallikleri hızlıca belirlemenize ve kötü amaçlı kullanıcı aracıları veya güvenlik açığı bulunan bağlantı noktaları gibi uyarıların kök nedenlerini bulmaya yönelik verileri de belirlemenize olanak sağlar. Bu ayıklanan veriler sayesinde ağınıza herhangi bir zararlı yetkisiz giriş denemesinden yanıt verme ve koruma konusunda bilinçli kararlar verebilir ve ağınıza daha sonra yetkisiz erişimi engellemek için kurallar oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure işlevleri ile öngörülü ağ izleme yapmak için paket yakalamayı kullanma](network-watcher-alert-triggered-packet-capture.md) ' yı ziyaret ederek uyarı temelinde paket yakalamaları tetiklemeyi öğrenin

Power BI ile NSG akış günlüklerinizi görselleştirmeyi öğrenin [Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png