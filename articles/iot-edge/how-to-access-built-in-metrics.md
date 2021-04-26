---
title: Yerleşik ölçümlere erişin-Azure IoT Edge
description: IoT Edge çalışma zamanı bileşenlerinden yerleşik ölçümlere uzaktan erişim
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a78db821c0fab01ad5d6752216a8f7682fb2c46
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200489"
---
# <a name="access-built-in-metrics"></a>Yerleşik ölçümlere erişme

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge çalışma zamanı bileşenleri, IoT Edge hub ve IoT Edge Aracısı, [Prometheus Exposition biçiminde](https://prometheus.io/docs/instrumenting/exposition_formats/)yerleşik ölçümler üretir. Bir IoT Edge cihazının durumunu izlemek ve anlamak için bu ölçümlere uzaktan erişin.

Sürüm 1.0.10 itibariyle, varsayılan olarak,, **Edgehub** ve **edgeagent** modüllerinin (ve) **bağlantı noktası 9600** ' de otomatik olarak gösterilir `http://edgeHub:9600/metrics` `http://edgeAgent:9600/metrics` . Varsayılan olarak ana bilgisayara eşlenmiş bağlantı noktaları değildir.

Ölçüm bağlantı noktası modülünü görüntüleyerek ve eşleştirerek, ana bilgisayardan ölçümlere erişin `createOptions` . Aşağıdaki örnekte, varsayılan ölçüm bağlantı noktası, konaktaki 9601 numaralı bağlantı noktasıyla eşlenir:

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Hem edgeHub hem de edgeAgent 'ın ölçüm uç noktalarını eşlerken farklı ve benzersiz ana bilgisayar bağlantı noktası numaraları seçin.

> [!NOTE]
> `httpSettings__enabled`Yerleşik ölçümlerin koleksiyon için kullanılabilir olması için ortam değişkeni olarak ayarlanmalıdır `false` .
>
> Ölçümleri devre dışı bırakmak için kullanılabilen ortam değişkenleri, [Azure/ıotedge deposu belgesi](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)içinde listelenmiştir.

## <a name="available-metrics"></a>Mevcut ölçümler

Ölçümler, toplanmakta olan ölçümün yapısını belirlemede yardımcı olacak Etiketler içerir. Tüm ölçümler aşağıdaki etiketleri içerir:

| Etiket | Description |
|-|-|
| ıothub | Cihazın konuştuğu hub |
| edge_device | Geçerli cihazın KIMLIĞI |
| instance_number | Geçerli çalışma zamanını temsil eden bir GUID. Yeniden başlatma sırasında tüm ölçümler sıfırlanır. Bu GUID, yeniden başlatmaları bağdaştırmayı kolaylaştırır. |

Prometheus Exposition biçiminde dört temel ölçüm türü vardır: sayaç, ölçer, histogram ve Özet. Farklı ölçüm türleri hakkında daha fazla bilgi için bkz. [Prometheus ölçüm Types belgeleri](https://prometheus.io/docs/concepts/metric_types/).

Yerleşik histogram ve Özet ölçümler için sunulan quantiles, 0,1, 0,5, 0,9 ve 0,99.

**Edgehub** modülü aşağıdaki ölçümleri üretir:

| Name | Boyutlar | Description |
|-|-|-|
| `edgehub_gettwin_total` | `source` (işlem kaynağı)<br> `id` (modül KIMLIĞI) | Tür: sayaç<br> Toplam GetTwin çağrısı sayısı |
| `edgehub_messages_received_total` | `route_output` (gönderilen iletiyi çıkar)<br> `id` | Tür: sayaç<br> İstemcilerden alınan toplam ileti sayısı |
| `edgehub_messages_sent_total` | `from` (ileti kaynağı)<br> `to` (ileti hedefi)<br>`from_route_output`<br> `to_route_input` (ileti hedefi girişi)<br> `priority` (hedefe yönelik ileti önceliği) | Tür: sayaç<br> İstemcilere veya yukarı akışa gönderilen toplam ileti sayısı<br> `to_route_input`$upstream olduğunda boştur `to` |
| `edgehub_reported_properties_total` | `target`(hedefi Güncelleştir)<br> `id` | Tür: sayaç<br> Bildirilen toplam özellik güncelleştirme çağrısı sayısı |
| `edgehub_message_size_bytes` | `id`<br> | Tür: Özet<br> İstemcilerden gelen ileti boyutu<br> Değerler `NaN` , belirli bir süre (Şu anda 10 dakika) için yeni bir ölçüm raporlanmadı olarak bildirilebilir; `summary` tür için, karşılık gelen `_count` ve `_sum` Sayaçlar yayınlanır. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Tür: Özet<br> Get ikizi işlemleri için harcanan süre |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Tür: Özet<br> İleti göndermek için geçen süre |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Tür: Özet<br> Kuyruktaki bir iletiyi işlemek için geçen süre |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Tür: Özet<br> Bildirilen özellikleri güncelleştirmek için geçen süre |
| `edgehub_direct_method_duration_seconds` | `from` yapana<br> `to` bildiği | Tür: Özet<br> Bir doğrudan iletiyi çözümlemek için geçen süre |
| `edgehub_direct_methods_total` | `from`<br> `to` | Tür: sayaç<br> Gönderilen toplam doğrudan ileti sayısı |
| `edgehub_queue_length` | `endpoint` (ileti kaynağı)<br> `priority` (sıra önceliği) | Tür: ölçer<br> Verilen öncelik için geçerli edgeHub sırası uzunluğu |
| `edgehub_messages_dropped_total` | `reason` (no_route ttl_expiry)<br> `from` <br> `from_route_output` | Tür: sayaç<br> Neden yüzünden kaldırılan toplam ileti sayısı |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Tür: sayaç<br> Depolama hatası nedeniyle, kabul edilmemiş toplam ileti sayısı |
| `edgehub_offline_count_total` | `id` | Tür: sayaç<br> Toplam edgeHub 'ın çevrimdışı olma sayısı |
| `edgehub_offline_duration_seconds`| `id` | Tür: Özet<br> Sınır hub 'ı çevrimdışı |
| `edgehub_operation_retry_total` | `id`<br> `operation` (işlem adı) | Tür: sayaç<br> Toplam edgeHub işlemi yeniden denenme sayısı |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (kimliği doğrulanmamış)<br> | Tür: sayaç<br> Toplam istemci sayısı ile edgeHub 'a bağlantı kurulamadı |

**Edgeagent** modülü aşağıdaki ölçümleri üretir:

| Name | Boyutlar | Description |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Tür: ölçer<br> Modülün dağıtımda belirtildiği ve çalışır durumda olduğu süre miktarı |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Tür: ölçer<br> Modülün dağıtımda belirtildiği zaman miktarı |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Tür: sayaç<br> EdgeAgent 'ın modülün başlamasını istediğiniz Docker sayısı |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Tür: sayaç<br> EdgeAgent 'in modülü durdurmak için kaç kez sorulduğu |
| `edgeAgent_command_latency_seconds` | `command` | Tür: ölçer<br> Verilen komutu yürütmek için Docker geçen süre. Olası komutlar şunlardır: oluşturma, güncelleştirme, kaldırma, başlatma, durdurma, yeniden başlatma |
| `edgeAgent_iothub_syncs_total` | | Tür: sayaç<br> EdgeAgent 'ın ikizi öğesini ıothub ile eşitlemeye kaç kez denendiği, hem başarılı hem de başarısız. Bu sayı, bir ikizi güncelleştirmesi için ikizi ve hub bildirimi isteyen her iki aracıyı de içerir |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Tür: sayaç<br> EdgeAgent 'ın ıothub ile ikizi eşitlemesini başarısız olma sayısı. |
| `edgeAgent_deployment_time_seconds` | | Tür: sayaç<br> Değişiklik alındıktan sonra yeni bir dağıtımın tamamlanması için geçen süre. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Tür: sayaç<br> Bir yerleşik edgeAgent doğrudan yönteminin, ping veya yeniden başlatma gibi bir kez çağrıldığı sayı. |
| `edgeAgent_host_uptime_seconds` | | Tür: ölçer<br> Konağın açık olduğu süre |
| `edgeAgent_iotedged_uptime_seconds` | | Tür: ölçer<br> Iotedşlı ne kadar çalışıyor |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Tür: ölçer<br> Diskte kalan alan miktarı |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Tür: ölçer<br> Diskin boyutu |
| `edgeAgent_used_memory_bytes` | `module_name` | Tür: ölçer<br> Tüm işlemlerin kullandığı RAM miktarı |
| `edgeAgent_total_memory_bytes` | `module_name` | Tür: ölçer<br> RAM kullanılabilir |
| `edgeAgent_used_cpu_percent` | `module_name` | Tür: histogram<br> Tüm işlemlerin kullandığı CPU yüzdesi |
| `edgeAgent_created_pids_total` | `module_name` | Tür: ölçer<br> Kapsayıcının oluşturduğu işlem veya iş parçacığı sayısı |
| `edgeAgent_total_network_in_bytes` | `module_name` | Tür: ölçer<br> Ağdan alınan bayt sayısı |
| `edgeAgent_total_network_out_bytes` | `module_name` | Tür: ölçer<br> Ağa gönderilen bayt sayısı |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Tür: ölçer<br> Diskten okunan bayt sayısı |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Tür: ölçer<br> Diske yazılan bayt sayısı |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Tür: ölçer<br> Cihazla ilgili genel meta veriler. Değer her zaman 0 ' dır, Etiketler etiketlerinde kodlanır. `experimental_features`Ve `host_information` JSON nesnelerdir. `host_information` gibi görünüyor ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Bu, `ServerVersion` Docker sürümüdür ve `Version` IoT Edge güvenlik cini sürümüdür. |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md)
* [IoT Edge Aracısı ve IoT Edge hub modülünün özellikleri TWINS](module-edgeagent-edgehub.md)
