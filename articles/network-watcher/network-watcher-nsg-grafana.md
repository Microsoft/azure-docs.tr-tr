---
title: Grafana kullanarak NSG akış günlüklerini yönetme
titleSuffix: Azure Network Watcher
description: Ağ Izleyicisi ve Grafana kullanarak Azure 'da ağ güvenlik grubu akış günlüklerini yönetin ve çözümleyin.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: d522d305c70214009b8aa2886d07d2d5403dd2b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656317"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Ağ Izleyicisi ve Grafana kullanarak ağ güvenlik grubu akış günlüklerini yönetme ve çözümleme

[Ağ güvenlik grubu (NSG) akış günlükleri](network-watcher-nsg-flow-logging-overview.md) , ağ arabirimlerinde giriş ve çıkış IP trafiğini anlamak için kullanılabilecek bilgiler sağlar. Bu akış günlükleri, NSG kural temelinde giden ve gelen akışları gösterir, akışın akış (kaynak/hedef IP 'si, kaynak/hedef bağlantı noktası, protokol) hakkında, 5 demet bilgileri için geçerli olduğu ve trafiğe izin veriliyorsa veya reddedildiyse.

Ağınızda akış günlüğü etkin olan çok sayıda NSG 'niz olabilir. Bu günlük veri miktarı, günlüklerinizden daha fazla bilgi elde etmek ve bunların öngörülerini elde etmelerini sağlar. Bu makalede, açık kaynaklı sunucu tarafı veri işleme işlem hattı olan Grafana, açık kaynaklı bir grafik aracı, Elam araması, dağıtılmış arama ve analiz altyapısı ve Logstash kullanarak bu NSG akış günlüklerini merkezi olarak yönetmeye yönelik bir çözüm sunulmaktadır.  

## <a name="scenario"></a>Senaryo

NSG akış günlükleri, ağ Izleyicisi kullanılarak etkinleştirilir ve Azure Blob depolama alanında depolanır. Bir Logstash eklentisi, blob depolamadan akış günlüklerini bağlamak ve işlemek ve onları Elabi aramasına göndermek için kullanılır.  Akış günlükleri Elabi aramasında depolandıktan sonra, Grafana içinde özelleştirilmiş panolar halinde çözümlenebilirler ve görselleştirilir.

![NSG ağ Izleyicisi Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Yükleme adımları

### <a name="enable-network-security-group-flow-logging"></a>Ağ güvenlik grubu akış günlüğünü etkinleştir

Bu senaryoda, hesabınızdaki en az bir ağ güvenlik grubunda ağ güvenlik grubu akış günlüğü 'nün etkin olması gerekir. Ağ güvenlik akışı günlüklerinin etkinleştirilmesi hakkındaki yönergeler için, [ağ güvenlik grupları için akış günlüğüne giriş konusuna giriş](network-watcher-nsg-flow-logging-overview.md)olarak aşağıdaki makaleye bakın.

### <a name="setup-considerations"></a>Kurulumda dikkat edilecek noktalar

Bu örnekte Grafana, Elaun Search ve Logstash, Azure 'da dağıtılan bir Ubuntu 16,04 LTS sunucusunda yapılandırılmıştır. Bu en düşük kurulum, üç bileşeni de çalıştırmak için kullanılır; hepsi aynı VM üzerinde çalışır. Bu kurulum yalnızca test ve kritik olmayan iş yükleri için kullanılmalıdır. Logstash, Elaun Search ve Grafana birçok örnek arasında bağımsız olarak ölçeklendirilmesi için kullanılabilir. Daha fazla bilgi için, bu bileşenlerin her biri için belgelere bakın.

### <a name="install-logstash"></a>Logstash 'i yükler

JSON biçimli akış günlüklerini bir akış grubu düzeyine düzleştirmek için Logstash 'i kullanırsınız.

1. Logstash 'i yüklemek için aşağıdaki komutları çalıştırın:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Logstash 'i akış günlüklerini ayrıştırmak ve Elabi aramasına göndermek için yapılandırın. Şunu kullanarak bir Logstash. conf dosyası oluşturun:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Aşağıdaki içeriği dosyaya ekleyin. Depolama hesabı adını ve erişim anahtarını, depolama hesabı ayrıntılarınızı yansıtacak şekilde değiştirin:

   ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
    "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
    "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
    "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
    "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
    "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
   ```

Girilen Logstash yapılandırma dosyası üç bölümden oluşur: giriş, filtre ve çıkış.
Giriş bölümü, Logstash 'in işlem yapacağı günlüklerin giriş kaynağını belirler. Bu durumda, blob depolamada depolanan NSG akış günlüğü JSON dosyalarına erişebilmemizi sağlayacak bir "azureblob" giriş eklentisi (sonraki adımlarda yüklenir) kullanacağız. 

Filtre bölümü, her akış kayıt düzeninin ve ilişkili özelliklerinin ayrı bir Logstash olayı haline gelmesi için her akış günlük dosyasını düzleştirir.

Son olarak, çıkış bölümü her bir Logstash olayını Elaksearch sunucusuna iletir. Logstash yapılandırma dosyasını özel gereksinimlerinize uyacak şekilde değiştirebilirsiniz.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Azure Blob depolama için Logstash giriş eklentisini yükler

Bu Logstash eklentisi, akış günlüklerine belirlenen BLOB depolama hesabından doğrudan erişmenizi sağlar. Bu eklentiyi yüklemek için, varsayılan Logstash yükleme dizininden (Bu durumda/usr/share/logstash/bin) komutunu çalıştırın:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Bu eklenti hakkında daha fazla bilgi için bkz. [Azure depolama Blobları Için Logstash girişi eklentisi](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Elaa aramasını yükleme

Elaun Search yüklemek için aşağıdaki betiği kullanabilirsiniz. Elau aramasını yükleme hakkında daha fazla bilgi için bkz. [elastik yığın](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Grafana 'i yükler

Grafana yüklemek ve çalıştırmak için aşağıdaki komutları çalıştırın:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Ek yükleme bilgileri için bkz. [debir/Ubuntu 'A yükleme](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Elaun Search sunucusunu bir veri kaynağı olarak ekleme

Ardından, akış günlüklerini içeren Elaun Search dizinini veri kaynağı olarak eklemeniz gerekir. **Veri kaynağı Ekle** ' ye tıklayarak ve ilgili bilgilerle formu tamamlayarak bir veri kaynağı ekleyebilirsiniz. Bu yapılandırmanın bir örneği aşağıdaki ekran görüntüsünde bulunabilir:

![Veri kaynağı ekleme](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Pano oluşturma

NSG akış günlükleri içeren Elam arama dizininden okumak üzere Grafana başarıyla yapılandırdığınıza göre, panoları oluşturabilir ve kişiselleştirebilirsiniz. Yeni bir pano oluşturmak için **ilk panonuzu oluştur**' u seçin. Aşağıdaki örnek grafik yapılandırmasında NSG kuralına göre bölünmüş akışlar gösterilmektedir:

![Pano grafiği](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Aşağıdaki ekran görüntüsünde, üstteki akışlar ve bunların sıklığını gösteren bir grafik ve grafik gösterilmektedir. Akışlar Ayrıca NSG kuralı ve kararlara göre akışlar tarafından da gösterilir. Grafana, özel izleme gereksinimlerinize uyacak şekilde panolar oluşturmanız önerildiğinden, büyük ölçüde özelleştirilebilir. Aşağıdaki örnek tipik bir panoyu göstermektedir:

![NSG kuralına göre bölünmüş akışlar ile örnek grafik yapılandırmasını gösteren ekran görüntüsü.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Sonuç

Ağ İzleyicisini Elagr Search ve Grafana ile tümleştirerek, artık NSG akış günlüklerini ve diğer verileri yönetmek ve görselleştirmek için kullanışlı ve merkezi bir yola sahip olursunuz. Grafana, akış günlüklerini daha fazla yönetmek ve ağ trafiğinizi daha iyi anlamak için de kullanılabilen çeşitli güçlü grafikleme özelliklerine sahiptir. Artık bir Grafana örneği ayarlamış ve Azure 'a bağlı olduğunuza göre, sunduğu diğer işlevleri keşfetmeye devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Ağ İzleyicisi](network-watcher-monitoring-overview.md)'ni kullanma hakkında daha fazla bilgi edinin.

