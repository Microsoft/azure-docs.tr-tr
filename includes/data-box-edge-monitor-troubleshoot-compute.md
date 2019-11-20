---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/26/2019
ms.author: alkohli
ms.openlocfilehash: f3bb391dceb1948820d00c0d09229f2c106ffc0b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601392"
---
İşlem rolü yapılandırılmış bir Data Box Edge cihazında, modülleri izlemek veya sorunlarını gidermek için Docker komutlarının bir alt kümesi kullanılabilir. Kullanılabilir komutların listesini görmek için [PowerShell arabirimine bağlanın](#connect-to-the-powershell-interface) ve `dkrdbe` işlevini kullanın.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
Aşağıdaki tabloda, için `dkrdbe`kullanılabilen komutların kısa bir açıklaması verilmiştir:

|Komutu  |Açıklama |
|---------|---------|
|`image`     | Görüntüleri yönetin. Kullanılmayan görüntüleri kaldırmak için şunu kullanın:`dkrdbe image prune -a -f`       |
|`images`     | Resimleri Listele         |
|`inspect`     | Docker nesnelerinde alt düzey bilgileri döndür         |
|`login`     | Docker kayıt defterinde oturum açma         |
|`logout`     | Docker kayıt defterinden oturumu kapatma         |
|`logs`     | Bir kapsayıcının günlüklerini getirme        |
|`port`     | Bağlantı noktası eşlemelerini veya kapsayıcı için belirli bir eşlemeyi listeleyin        |
|`ps`     | Kapsayıcıları listeleme        |
|`pull`     | Kayıt defterinden görüntü veya depo çekme         |
|`start`     | Durdurulmuş bir veya daha fazla kapsayıcıyı Başlat         |
|`stats`     | Kapsayıcı kaynak kullanımı istatistiklerinin canlı akışını görüntüle         |
|`stop`     | Çalışan bir veya daha fazla kapsayıcıyı durdur        |
|`system`     | Docker 'ı yönetme         |
|`top`     | Bir kapsayıcının çalışan süreçlerini görüntüleme         |

Kullanılabilir herhangi bir komutla ilgili yardım almak için kullanın `dkrdbe <command-name> --help`.

Örneğin, `port` komutun kullanımını anlamak için şunu yazın:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

`dkrdbe` İşlevi için kullanılabilen komutlar, normal Docker komutları için kullanıldıkları parametrelerle aynı parametreleri kullanır. Docker komutuyla kullanılan seçenekler ve parametreler için [Docker CommandLine kullanma](https://docs.docker.com/engine/reference/commandline/docker/)bölümüne gidin.

### <a name="to-check-if-the-module-deployed-successfully"></a>Modülün başarıyla dağıtılıp dağıtılmadığını denetlemek için

İşlem modülleri, iş mantığı uygulanmış kapsayıcılardır. Bir işlem modülünün başarıyla dağıtılıp dağıtılmadığını denetlemek için, `ps` komutunu çalıştırın ve kapsayıcının (işlem modülüne karşılık gelen) çalışıp çalışmadığını denetleyin.

Tüm kapsayıcıların (duraklatılmış olanlar dahil) listesini almak için `ps -a` komutunu çalıştırın.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Kapsayıcı görüntüsünü oluştururken veya görüntüyü çekirken bir hata oluşursa, öğesini çalıştırın `logs edgeAgent`.  `EdgeAgent`, diğer kapsayıcıları sağlamaktan sorumlu IoT Edge çalışma zamanı kapsayıcısıdır.

Tüm `logs edgeAgent` günlüklerin dökümünü yaptığından, son hataları görmenin iyi bir yolu, seçeneğini `--tail 20`kullanmaktır.


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Kapsayıcı günlüklerini almak için

Belirli bir kapsayıcıya ait günlükleri almak için, önce kapsayıcıyı listeleyin ve ardından ilgilendiğiniz kapsayıcının günlüklerini alın.

1. [PowerShell arabirimine bağlanın](#connect-to-the-powershell-interface).
2. Çalışan kapsayıcıların listesini almak için `ps` komutunu çalıştırın.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. İçin günlüklere ihtiyacınız olan kapsayıcının kapsayıcı KIMLIĞINI bir yere getirin.

4. Belirli bir kapsayıcının günlüklerini almak için, kapsayıcı kimliğini sağlayan `logs` komutunu çalıştırın.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Cihazın kullanım istatistiklerini izlemek için

Cihazdaki belleği, CPU kullanımını ve GÇ 'yi izlemek için `stats` komutunu kullanın.

1. [PowerShell arabirimine bağlanın](#connect-to-the-powershell-interface).
2. Canlı akışı devre dışı bırakmak ve yalnızca ilk sonucu çekmek için komutunuçalıştırın.`stats`

   ```powershell
   dkrdbe stats --no-stream
   ```

   Aşağıdaki örnek, bu cmdlet 'in kullanımını gösterir:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

