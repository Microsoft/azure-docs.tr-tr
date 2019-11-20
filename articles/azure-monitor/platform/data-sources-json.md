---
title: Azure Izleyici 'de özel JSON verileri toplama | Microsoft Docs
description: Özel JSON veri kaynakları, Linux için Log Analytics Aracısı kullanılarak Azure Izleyici 'ye toplanabilir.  Bu özel veri kaynakları, bir dizi gibi JSON döndüren basit betikler veya Floentıd 'nin 300 + eklentilerinden biridir. Bu makalede, bu veri koleksiyonu için gereken yapılandırma açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/28/2018
ms.openlocfilehash: c7628badb993c26b989c1fe610d2360ff466de39
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932468"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Azure Izleyici 'de Linux için Log Analytics Aracısı ile özel JSON veri kaynakları toplama
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Özel JSON veri kaynakları, Linux için Log Analytics Aracısı kullanılarak [Azure izleyici](data-platform.md) 'ye toplanabilir.  Bu özel veri kaynakları, bir dizi gibi JSON döndüren basit betikler [veya](https://curl.haxx.se/) [floentıd 'nin 300 + eklentilerinden](https://www.fluentd.org/plugins/all)biridir. Bu makalede, bu veri koleksiyonu için gereken yapılandırma açıklanmaktadır.


> [!NOTE]
> Özel JSON verileri için Log Analytics Agent for Linux v 1.1.0-TII + gereklidir

## <a name="configuration"></a>Yapılandırma

### <a name="configure-input-plugin"></a>Giriş eklentisini yapılandırma

Azure Izleyici 'de JSON verileri toplamak için giriş eklentisindeki bir Floentd etiketinin başlangıcına `oms.api.` ekleyin.

Örneğin, aşağıdaki `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`ayrı bir yapılandırma dosyası `exec-json.conf`.  Bu, her 30 saniyede bir kıvrımlı komutu çalıştırmak için Floentd eklentisini `exec` kullanır.  Bu komutun çıktısı, JSON çıktı eklentisi tarafından toplanır.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` altına eklenen yapılandırma dosyası, aşağıdaki komutla onun sahipliğinin değiştirilmesini gerektirir.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Çıkış eklentisini yapılandırma 
Aşağıdaki çıkış eklentisi yapılandırmasını `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ' deki ana yapılandırmaya veya `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` yerleştirilmiş ayrı bir yapılandırma dosyası olarak ekleyin

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Linux için Log Analytics aracısını yeniden başlatma
Linux hizmeti için Log Analytics aracısını aşağıdaki komutla yeniden başlatın.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Çıktı
Veriler Azure Izleyici 'de `<FLUENTD_TAG>_CL`kayıt türüyle toplanacaktır.

Örneğin, özel etiket Azure Izleyici 'de `tomcat_CL`kayıt türüyle `tag oms.api.tomcat`.  Bu türün tüm kayıtlarını aşağıdaki günlük sorgusuyla alabilirsiniz.

    Type=tomcat_CL

İç içe geçmiş JSON veri kaynakları desteklenir, ancak üst alanı temel alan dizine alınır. Örneğin, aşağıdaki JSON verileri `tag_s : "[{ "a":"1", "b":"2" }]`olarak bir günlük sorgusundan döndürülür.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Sonraki adımlar
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin. 