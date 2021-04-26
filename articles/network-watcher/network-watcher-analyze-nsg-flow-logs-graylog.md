---
title: Azure ağ güvenlik grubu akış günlüklerini çözümleme-gri günlük | Microsoft Docs
description: Ağ Izleyicisi ve gri günlük kullanarak Azure 'da ağ güvenlik grubu akış günlüklerini yönetme ve çözümleme hakkında bilgi edinin.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965553"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Ağ Izleyicisi ve gri günlük kullanarak Azure 'da ağ güvenlik grubu akış günlüklerini yönetme ve çözümleme

[Ağ güvenlik grubu akış günlükleri](network-watcher-nsg-flow-logging-overview.md) , Azure ağ arabirimleri için giriş ve çıkış IP trafiğini anlamak için kullanabileceğiniz bilgiler sağlar. Akış günlükleri her bir ağ güvenlik grubu kuralı temelinde giden ve gelen akışları gösterir, akışın ağ arabirimi (Flow), 5 demet bilgileri (kaynak/hedef IP, kaynak/hedef bağlantı noktası, protokol) Flow hakkında ve trafiğe izin verildiyse veya reddedildiyse.

Ağınızda akış günlüğü etkin olan birçok ağ güvenlik grubu olabilir. Akış günlüğü etkin olan birkaç ağ güvenlik grubu, verilerinizden ayrıştırmaya ve günlüklerinizden öngörülere sahip olmasını sağlayabilir. Bu makalede, bir açık kaynak sunucu tarafı veri işleme işlem hattı olan bir açık kaynak günlük yönetimi ve Analiz Aracı ve Logstash kullanarak bu ağ güvenlik grubu akış günlüklerini merkezi olarak yönetmek için bir çözüm sunulmaktadır.

> [!Warning]
> Aşağıdaki adımlar akış günlükleri sürüm 1 ile birlikte çalışır. Ayrıntılar için bkz. [ağ güvenlik grupları için akış günlüğüne giriş](network-watcher-nsg-flow-logging-overview.md). Aşağıdaki yönergeler, değişiklik yapılmadan günlük dosyalarının 2. sürümüyle birlikte çalışmayacaktır.

## <a name="scenario"></a>Senaryo

Ağ güvenlik grubu akış günlükleri, ağ Izleyicisi kullanılarak etkinleştirilir. Akış günlükleri Azure Blob depolama alanına akar. Bir Logstash eklentisi, blob depolamadan akış günlüklerini bağlamak ve işlemek ve bunları griye göndermek için kullanılır. Akış günlükleri, grileştirilmiş günlükte depolandıktan sonra analiz edilebilir ve özelleştirilmiş panolar halinde görselleştirilebilir.

![Gri günlük iş akışı](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Yükleme adımları

### <a name="enable-network-security-group-flow-logging"></a>Ağ güvenlik grubu akış günlüğünü etkinleştir

Bu senaryoda, hesabınızdaki en az bir ağ güvenlik grubunda ağ güvenlik grubu akış günlüğü 'nün etkin olması gerekir. Ağ güvenlik grubu akış günlüklerini etkinleştirme hakkında yönergeler için, [ağ güvenlik grupları için akış günlüğüne giriş konusuna giriş](network-watcher-nsg-flow-logging-overview.md)olarak aşağıdaki makaleye bakın.

### <a name="setting-up-graylog"></a>Gri günlük ayarlama

Bu örnekte, Azure 'da dağıtılan bir Ubuntu 14,04 sunucusunda her iki gri günlük ve Logstash yapılandırılır.

- Ubuntu 'da nasıl yükleneceğine ilişkin adım adım yönergeler için, griden günlük [belgelere](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) bakın.
- [Belgeler](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)' i izleyerek Ayrıca, bir gri günlük Web arabirimini de yapılandırmadığınızdan emin olun.

Bu örnek, minimum gri günlük kurulumunu kullanır (ör. bir gri tonlamalı günlüğün tek bir örneği), ancak gri, sistem ve üretim gereksinimlerinize bağlı olarak kaynaklar genelinde ölçeklenebilen şekilde etkinleştirilebilir. Mimari konuları veya derinlemesine mimari kılavuz hakkında daha fazla bilgi için bkz. gri günlük [belgeleri](https://docs.graylog.org/en/2.2/pages/architecture.html) ve [mimari Kılavuzu](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Gri, platformunuza ve tercihlerinize bağlı olarak birçok şekilde gri bir şekilde yüklenebilir. Olası yükleme yöntemlerinin tam listesi için, gri tonlamalı günlüğün resmi [belgelerine](https://docs.graylog.org/en/2.2/pages/installation.html)bakın. Gri günlük sunucu uygulaması, Linux dağıtımları üzerinde çalışır ve aşağıdaki önkoşullara sahiptir:

-  Java s 8 veya üzeri – [Azul Azure JDK belgeleri](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Elastik arama 2. x (2.1.0 veya üzeri)- [Elaksearch yükleme belgeleri](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 veya üzeri – [MongoDB Yükleme belgeleri](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Logstash 'i yükler

Logstash, JSON biçimli akış günlüklerini bir akış grubu düzeyine düzleştirmek için kullanılır. Akış günlüklerinin düzleştirilmesi, günlüklerin daha kolay bir şekilde okunmasını ve bu günlükte aranmasını kolaylaştırır.

1. Logstash 'i yüklemek için aşağıdaki komutları çalıştırın:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Günlük günlüklerini ayrıştırmak ve bunları griye göndermek için Logstash 'i yapılandırın. Logstash. conf dosyası oluşturma:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Aşağıdaki içeriği dosyaya ekleyin. Vurgulanan değerleri, depolama hesabı ayrıntılarınızı yansıtacak şekilde değiştirin:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => ","}
        add_field => {
                    "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                    "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                    "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                    "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                    "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                    "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                    "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                    "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Girilen Logstash yapılandırma dosyası üç bölümden oluşur: giriş, filtre ve çıkış. Giriş bölümü, Logstash 'in işlem yapacağı günlüklerin giriş kaynağını belirler. Bu durumda, blob depolamada depolanan ağ güvenlik grubu akış günlüğü JSON dosyalarına erişebilmemizi sağlayan bir Azure blog giriş eklentisi (sonraki adımlarda yüklenir) kullanacaksınız.

Filtre bölümü, her akış kayıt düzeninin ve ilişkili özelliklerinin ayrı bir Logstash olayı haline gelmesi için her akış günlük dosyasını düzleştirir.

Son olarak, çıkış bölümü her Logstash olayını gri günlük sunucusuna iletir. Özel gereksinimlerinize uyacak şekilde, Logstash yapılandırma dosyasını gerektiği şekilde değiştirin.

   > [!NOTE]
   > Önceki yapılandırma dosyası, yerel ana bilgisayar geri döngü IP adresi 127.0.0.1 üzerinde bir gri günlük sunucusunun yapılandırıldığını varsayar. Aksi takdirde, çıkış bölümünde ana bilgisayar parametresini doğru IP adresi ile değiştirdiğinizden emin olun.

Logstash 'i yükleme hakkında daha fazla yönerge için bkz. Logstash [belgeleri](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Azure Blob depolama için Logstash giriş eklentisini yükler

Logstash eklentisi, akış günlüklerine belirlenen BLOB depolama hesabından doğrudan erişmenizi sağlar. Eklentiyi yüklemek için, varsayılan Logstash yükleme dizininden (Bu durumda/usr/share/logstash/bin), aşağıdaki komutu çalıştırın:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Bu eklenti hakkında daha fazla bilgi için [belgelerine](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)bakın.

### <a name="set-up-connection-from-logstash-to-graylog"></a>Logstash 'ten gri günlüğe bağlantı kurma

Logstash kullanarak akış günlüklerine bir bağlantı kurdığınıza ve bir gri günlük sunucusunu ayarlamaya göre, gelen günlük dosyalarını kabul etmek için, bir gri günlük yapılandırmanız gerekir.

1. Sizin için yapılandırdığınız URL 'YI kullanarak, gri günlük sunucunuzun Web arabirimine gidin. Tarayıcınızı şu şekilde yönlendirerek arabirimine erişebilirsiniz `http://<graylog-server-ip>:9000/`

2. Yapılandırma sayfasına gitmek için sağ üst gezinti çubuğundan **sistem** açılan menüsünü seçin ve ardından **girişler**' e tıklayın.
   Alternatif olarak, şuraya gidin `http://<graylog-server-ip>:9000/system/inputs`

   ![Kullanmaya başlama](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Yeni girişi başlatmak için **Giriş Seç** açılan kutusunda *gelf UDP* ' yi seçin ve ardından formu doldurun. GELF, gri günlük Genişletilmiş günlük biçimini temsil eder. GELF biçimi, Grig günlüğü tarafından geliştirilmiştir. Avantajları hakkında daha fazla bilgi edinmek için bkz. gri günlük [belgeleri](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Girişi, gri tonlamalı günlük sunucunuzu yapılandırdığınız IP 'ye bağladığınızdan emin olun. IP adresi, Logstash yapılandırma dosyasının UDP çıktısının **ana bilgisayar** alanıyla eşleşmelidir. Varsayılan bağlantı noktası *12201* olmalıdır. Bağlantı noktasının Logstash yapılandırma dosyasında belirlenen UDP çıkışındaki **bağlantı noktası** alanıyla eşleştiğinden emin olun.

   ![Ekran görüntüsünde, giriş başlatma ve bulma seçenekleri ile gri günlük girişleri gösterilmektedir.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Girişi başlattıktan sonra, aşağıdaki resimde gösterildiği gibi **Yerel girişler** bölümünün altında göründüğünü görmeniz gerekir:

   ![Başlattığınız girişi içeren yerel girişler bölümünü gösteren ekran görüntüsü.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Gri günlük ileti girişleri hakkında daha fazla bilgi edinmek için [belgelerine](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs)bakın.

4. Bu yapılandırma yapıldıktan sonra, aşağıdaki komutla akış günlüklerinde okumaya başlamak için Logstash 'i başlatabilirsiniz: `sudo systemctl start logstash.service` .

### <a name="search-through-graylog-messages"></a>Gri günlük iletilerinde arama

Bir süredir, bir gri günlük sunucunuzun iletileri toplaması için bir süre geçtikten sonra iletilerde arama yapabilirsiniz. Gri günlük sunucunuza gönderilen iletileri denetlemek için, **giriş** yapılandırma sayfasından oluşturduğunuz GELF UDP girişinin "**alınan iletileri göster**" düğmesine tıklayın. Aşağıdaki resme benzer bir ekrana yönlendirilirsiniz: 

![Ekran görüntüsü, arama sonucunu, histogramı ve Iletileri görüntüleyen bir gri günlük sunucusunu gösterir.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Mavi "% {Message}" bağlantısına tıklanması her bir iletiyi aşağıdaki resimde gösterildiği gibi her bir akış tanımlama alanının parametrelerini gösterecek şekilde genişletir:

![Ekran görüntüsünde, gri günlük sunucusundan gelen ileti ayrıntısı gösterilmektedir.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Arama yapmak için belirli bir ileti alanı seçmezseniz, varsayılan olarak tüm ileti alanları aramaya dahil edilir. Belirli iletileri aramak istiyorsanız (ör. – belirli bir kaynak IP 'den akış başlıkları) [belgelendiği](https://docs.graylog.org/en/2.2/pages/queries.html) gibi, gri günlük arama sorgu dilini kullanabilirsiniz

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Gri günlük kullanarak ağ güvenlik grubu akış günlüklerini çözümleme

Bu durumda, daha sonra gri günlük verilerinizi daha iyi anlamak için bazı işlevlerinden yararlanabilirsiniz. Bu tür bir yol, verilerinizin belirli görünümlerini oluşturmak için panoları kullanmaktır.

### <a name="create-a-dashboard"></a>Pano oluşturma

1. Üst gezinti çubuğunda **panolar** ' ı veya şuraya git ' i seçin. `http://<graylog-server-ip>:9000/dashboards/`

2. Buradan yeşil **Pano Oluştur** düğmesine tıklayın ve kısa formu, panonuzun başlığı ve açıklamasıyla doldurun. Yeni panoyu oluşturmak için **Kaydet** düğmesine basın. Aşağıdaki resme benzer bir pano görürsünüz:

    ![Ekran görüntüsü, panoları oluşturma ve düzenleme seçenekleriyle gri günlük sunucu panoları gösterir.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Pencere öğeleri ekle

Herhangi bir pencere öğesi eklemediğimiz için panonun başlığına tıklayabilirsiniz, ancak artık boştur. Panoya eklemek için kolay ve kullanışlı bir tür pencere öğesi, seçilen alanın bir değer listesini ve bunların dağılımını görüntüleyen **hızlı değerler** grafiklerdir.

1. Üst gezinti çubuğundan **Ara** ' yı seçerek akış GÜNLÜKLERINI alan UDP girişinin arama sonuçlarına geri gidin.

2. Ekranın sol tarafındaki **Arama sonucu** panelinde, her bir gelen akış grubu iletisinin çeşitli alanlarını listeleyen **alanlar** sekmesini bulun.

3. Görselleştirilecek istediğiniz parametreyi seçin (Bu örnekte, IP kaynağı seçilidir). Olası pencere öğelerinin listesini göstermek için alanın solundaki mavi aşağı açılan oka tıklayın ve sonra pencere öğesini oluşturmak için **hızlı değerler** ' i seçin. Aşağıdaki resme benzer bir şey görmeniz gerekir:

   ![Kaynak IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Buradan, pencere öğesinin sağ üst köşesindeki **panoya Ekle** düğmesini seçebilir ve eklenecek panoyu seçebilirsiniz.

5. Yeni eklediğiniz pencere öğesini görmek için panoya geri gidin.

   Aşağıdaki resimde gösterilen örnek pano gibi önemli ölçümleri izlemek için panonuza histogram ve sayımlar gibi çeşitli diğer pencere öğeleri ekleyebilirsiniz:

   ![Flowlogs panosu](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Panolar ve diğer pencere öğesi türleri hakkında daha fazla bilgi için, gri günlük [belgelerine](https://docs.graylog.org/en/2.2/pages/dashboards.html)bakın.

Ağ İzleyicisini, gri tonlamalı günlük ile tümleştirerek ağ güvenlik grubu akış günlüklerini yönetmek ve görselleştirmek için kullanışlı ve merkezi bir yoldur. Gri, akış günlüklerini daha fazla yönetmek ve ağ trafiğinizi daha iyi anlamak için kullanılabilen akışlar ve uyarılar gibi çeşitli güçlü özelliklere sahiptir. Artık gri ve Azure 'a bağlı olduğunuza göre, sunduğu diğer işlevleri keşfetmeye devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Power BI ile ağ güvenlik grubu akış günlüklerini görselleştirme](network-watcher-visualize-nsg-flow-logs-power-bi.md)' i ziyaret ederek ağ güvenlik grubu akış günlüklerinizi Power BI ile görselleştirmeyi öğrenin.