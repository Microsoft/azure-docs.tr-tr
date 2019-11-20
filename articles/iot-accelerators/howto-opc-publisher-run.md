---
title: OPC yayımcısı çalıştırma-Azure | Microsoft Docs
description: Bu makalede OPC yayımcısının nasıl çalıştırılacağı ve ayıklanacağı açıklanır. Ayrıca performans ve bellek konularını ele alınmaktadır.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66e2cb30dcd58b7ad0c6cedbb547f75c8039bc58
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824128"
---
# <a name="run-opc-publisher"></a>OPC Yayımcısını Çalıştırma

Bu makalede, ad hata ayıklama OPC yayımcısının nasıl çalıştırılacağı açıklanır. Ayrıca performans ve bellek konularını ele alınmaktadır.

## <a name="command-line-options"></a>Komut satırı seçenekleri

Uygulama kullanımı, `--help` komut satırı seçeneği kullanılarak aşağıdaki şekilde gösterilir:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Genellikle IoT Hub sahip bağlantı dizesini yalnızca uygulamanın ilk çalıştırmasında belirtirsiniz. Bağlantı dizesi şifrelenir ve platform sertifika deposunda depolanır. Daha sonra uygulama, sertifika deposundan bağlantı dizesini okur. Her çalıştırmada bağlantı dizesini belirtirseniz, IoT Hub cihaz kayıt defterinde uygulama için oluşturulan cihaz kaldırılır ve yeniden oluşturulur.

## <a name="run-natively-on-windows"></a>Windows üzerinde yerel olarak çalıştır

Visual Studio ile **opcpublisher. sln** projesini açın, çözümü derleyin ve yayımlayın. Uygulamayı, yayımladığınız **hedef dizinde** aşağıdaki şekilde başlatabilirsiniz:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Kendi kendine oluşturulmuş bir kapsayıcı kullanma

Kendi kapsayıcınızı oluşturun ve aşağıdaki gibi başlatın:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Microsoft Container Registry bir kapsayıcı kullanın

Microsoft Container Registry 'de önceden oluşturulmuş bir kapsayıcı mevcuttur. Şu şekilde başlatın:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Desteklenen işletim sistemlerini ve işlemci mimarilerini görmek için [Docker Hub 'ını](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) denetleyin. İşletim sistemi ve CPU mimariniz destekleniyorsa Docker otomatik olarak doğru kapsayıcıyı seçer.

## <a name="run-as-an-azure-iot-edge-module"></a>Azure IoT Edge modülü olarak çalıştır

OPC yayımcısı [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) modülü olarak kullanılmak üzere kullanılabilir. OPC yayımcısını IoT Edge modülü olarak kullandığınızda, desteklenen tek taşıma protokolleri **Amqp_Tcp_Only** ve **Mqtt_Tcp_Only**.

OPC yayımcısını IoT Edge dağıtımınıza modül olarak eklemek için, Azure portal IoT Hub ayarlarınıza gidin ve aşağıdaki adımları tamamlayın:

1. **IoT Edge** gidin ve IoT Edge cihazınızı oluşturun veya seçin.
1. **Modülleri Ayarlama**'yı seçin.
1. **Dağıtım modülleri** altında **Ekle** ' yi ve ardından **IoT Edge modülünü**seçin.
1. **Ad** alanına **Yayımcı**yazın.
1. **Görüntü URI 'si** alanına `mcr.microsoft.com/iotedge/opc-publisher:<tag>` girin.
1. Kullanılabilir etiketleri [Docker Hub 'ında](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) bulabilirsiniz
1. Aşağıdaki JSON öğesini **kapsayıcı oluşturma seçenekleri** alanına yapıştırın:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Bu yapılandırma, OPC yayımcı görüntüsünü kullanarak **Yayımcı** adlı bir kapsayıcıyı başlatmak için IoT Edge yapılandırır. Kapsayıcının sisteminin konak adı **Publisher**olarak ayarlanmıştır. OPC yayımcısı aşağıdaki komut satırı bağımsız değişkeniyle çağrılır: `--aa`. Bu seçenekle OPC yayımcısı, bağlandığı OPC UA sunucularının sertifikalarına güvenir. Herhangi bir OPC yayımcısı komut satırı seçeneğini kullanabilirsiniz. Tek sınırlama, IoT Edge tarafından desteklenen **kapsayıcı oluşturma seçeneklerinin** boyutudur.

1. Diğer ayarları değiştirmeden bırakın ve **Kaydet**'i seçin.
1. OPC yayımcısının çıkışını başka bir IoT Edge modülüyle yerel olarak işlemek istiyorsanız, **modülleri ayarla** sayfasına dönün. Ardından **rotalar belirt** sekmesine gidin ve aşağıdaki JSON gibi görünen yeni bir yol ekleyin:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. **Modülleri ayarla** sayfasına döndüğünüzde, yapılandırmanın son sayfasına ulaşana kadar **İleri**' yi seçin.
1. Yapılandırmanızı IoT Edge göndermek için **Gönder** ' i seçin.
1. Edge cihazınızda IoT Edge başlattığınızda ve Docker kapsayıcı **yayımcısı** çalışıyorsa OPC yayımcısının günlük çıkışını `docker logs -f publisher` kullanarak veya günlük dosyasına bakarak kullanıma alabilirsiniz. Önceki örnekte, günlük dosyası `d:\iiotegde\publisher-publisher.log`üzerinde. [IoT-Edge-OPC-Publisher-Diagnostics aracını](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)da kullanabilirsiniz.

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Yapılandırma dosyalarını konakta erişilebilir hale getirme

IoT Edge modülü yapılandırma dosyalarını konak dosya sisteminde erişilebilir hale getirmek için aşağıdaki **kapsayıcı oluşturma seçeneklerini**kullanın. Aşağıdaki örnek, Windows için Linux kapsayıcıları kullanan bir dağıtımdır:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Bu seçeneklerle OPC yayımcısı, dosya `./pn.json` yayımlaması gereken düğümleri okur ve kapsayıcının çalışma dizini başlangıçta `/appdata` olarak ayarlanır. Bu ayarlarla OPC yayımcısı, yapılandırmasını almak için kapsayıcıdan dosya `/appdata/pn.json` okur. `--pf` seçeneği olmadan OPC yayımcısı varsayılan yapılandırma dosyası `./publishednodes.json`okumaya çalışır.

`publisher-publisher.log`varsayılan adı kullanılarak günlük dosyası `/appdata` yazılır ve `CertificateStores` dizini de bu dizinde oluşturulur.

Bu dosyaların tümünün konak dosya sisteminde kullanılabilmesini sağlamak için, kapsayıcı yapılandırması bir bağlama bağlama birimi gerektirir. `d://iiotedge:/appdata` BIND, kapsayıcı başlangıcında geçerli çalışma dizini olan dizin `/appdata``d://iiotedge`konak dizinine eşler. Bu seçenek olmadan, kapsayıcı bir sonraki başlatıldığında hiçbir dosya verisi kalıcı olmaz.

Windows kapsayıcıları çalıştırıyorsanız `Binds` parametresinin sözdizimi farklıdır. Kapsayıcı başlangıcında, çalışma dizini `c:\appdata`. Yapılandırma dosyasını konaktaki Dizin `d:\iiotedge`yerleştirmek için `HostConfig` bölümünde aşağıdaki eşlemeyi belirtin:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Linux üzerinde Linux kapsayıcıları çalıştırıyorsanız, `Binds` parametresinin sözdizimi de farklı olur. Kapsayıcı başlangıcında, çalışma dizini `/appdata`. Yapılandırma dosyasını konaktaki Dizin `/iiotedge` yerleştirmek için `HostConfig` bölümünde aşağıdaki eşlemeyi belirtin:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Kapsayıcı kullanırken dikkat edilecek noktalar

Aşağıdaki bölümlerde bir kapsayıcı kullandığınızda göz önünde bulundurmanız gereken bazı şeyler listelenmektedir:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>OPC yayımcısı OPC UA sunucusuna erişim

Varsayılan olarak OPC yayımcısı OPC UA sunucusu 62222 numaralı bağlantı noktasını dinler. Bu gelen bağlantı noktasını bir kapsayıcıda kullanıma sunmak için aşağıdaki komutu kullanın:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Kapsayıcı adı çözümlemesini etkinleştir

Kapsayıcının içinden diğer kapsayıcılara ad çözümlemesini etkinleştirmek için, Docker köprü ağını tanımlayan bir kullanıcı oluşturun ve `--network` seçeneğini kullanarak kapsayıcıyı bu ağa bağlayın. Ayrıca, `--name` seçeneğini kullanarak kapsayıcının adını aşağıdaki gibi atayın:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kapsayıcıya artık aynı ağdaki diğer kapsayıcılar tarafından `publisher` adı kullanılarak erişilebilir.

### <a name="access-other-systems-from-within-the-container"></a>Diğer sistemlere kapsayıcı içinden erişin

Önceki bölümde açıklanan parametreler kullanılarak diğer kapsayıcılara erişilebilir. Docker 'ın barındırıldığı işletim sistemi DNS etkin ise, DNS tarafından bilinen tüm sistemlere erişim işe yarar.

NetBIOS ad çözümlemesi kullanan ağlarda, kapsayıcınızı `--add-host` seçeneğiyle başlatarak diğer sistemlere erişimi etkinleştirin. Bu seçenek, kapsayıcının konak dosyasına bir girişi etkin bir şekilde ekler:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Konak adı ata

OPC yayımcısı, sertifika ve uç nokta üretimi için üzerinde çalıştığı makinenin ana bilgisayar adını kullanır. Docker, `-h` seçeneği tarafından ayarlanmamışsa rastgele bir konak adı seçer. Aşağıdaki örnekte, kapsayıcının iç ana bilgisayar adının `publisher`olarak nasıl ayarlanacağı gösterilmektedir:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>BIND takar (paylaşılan dosya sistemi) kullan

Kapsayıcı dosya sistemini kullanmak yerine, yapılandırma bilgilerini ve günlük dosyalarını depolamak için konak dosya sistemini seçebilirsiniz. Bu seçeneği yapılandırmak için bağlama bağlama modunda `docker run` `-v` seçeneğini kullanın.

## <a name="opc-ua-x509-certificates"></a>OPC UA X. 509.440 sertifikaları

OPC UA, bir bağlantı kurarken ve aralarındaki iletişimi şifrelemek için OPC UA istemcisinin ve sunucusunun kimliğini doğrulamak üzere X. 509.440 sertifikaları kullanır. OPC yayımcısı tüm sertifikaları yönetmek için OPC UA yığını tarafından tutulan sertifika depolarını kullanır. Başlangıçta OPC yayımcısı kendisi için bir sertifika olup olmadığını denetler. Sertifika deposunda sertifika yoksa ve biri komut satırında geçirilmemişse OPC yayımcısı otomatik olarak imzalanan bir sertifika oluşturur. Daha fazla bilgi için `OpcApplicationConfigurationSecurity.cs`içindeki **ıningpplicationsecurityasync** yöntemine bakın.

Otomatik olarak imzalanan sertifikalar, güvenilir bir CA tarafından imzalanmadığı için herhangi bir güvenlik sağlamaz.

OPC yayımcısı şunları yapmak için komut satırı seçenekleri sağlar:

- OPC yayımcısı tarafından kullanılan geçerli uygulama sertifikasının CSR bilgilerini alın.
- CA imzalı bir sertifika ile OPC yayımcısı sağlayın.
- Yeni bir anahtar çifti ve eşleşen CA imzalı sertifikayla OPC yayımcısı sağlayın.
- Güvenilen bir eşe veya güvenilir veren sertifika deposuna sertifikalar ekleyin.
- Bir CRL ekleyin.
- Güvenilen eşten veya güvenilen verenler sertifika deposundan bir sertifikayı kaldırın.

Tüm bu seçenekler, dosyaları veya Base64 kodlamalı dizeleri kullanarak parametreleri geçirmenize olanak sağlar.

Tüm sertifika depoları için varsayılan depolama türü, komut satırı seçeneklerini kullanarak değiştirebileceğiniz dosya sistemidir. Kapsayıcı, dosya sisteminde kalıcı depolama alanı sağlamadığından, farklı bir depo türü seçmeniz gerekir. Konak dosya sisteminde veya bir Docker biriminde sertifika depolarını kalıcı hale getirmek için Docker `-v` seçeneğini kullanın. Bir Docker birimi kullanıyorsanız, sertifikaları Base64 kodlamalı dizeler kullanarak geçirebilirsiniz.

Çalışma zamanı ortamı sertifikaların kalıcı olduğunu etkiler. Uygulamayı her çalıştırdığınızda yeni sertifika depoları oluşturmaktan kaçının:

- Windows üzerinde yerel olarak çalışan, özel anahtara erişim başarısız olduğundan `Directory` türünde bir uygulama sertifika deposu kullanamazsınız. Bu durumda `--at X509Store`seçeneğini kullanın.
- Linux Docker kapsayıcısı olarak çalışan, Docker Run seçeneği `-v <hostdirectory>:/appdata`sertifika depolarını konak dosya sistemiyle eşleyebilirsiniz. Bu seçenek, sertifikanın uygulama genelinde kalıcı olmasını sağlar.
- Linux Docker kapsayıcısı olarak çalışıyor ve uygulama sertifikası için bir x509 deposu kullanmak istiyorsanız, Docker Run seçeneğini `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` ve uygulama seçeneğini kullanın `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Performans ve bellek konuları

Bu bölümde bellek ve performansı yönetme seçenekleri açıklanmaktadır:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Performansı ve belleği denetlemek için komut satırı parametreleri

OPC yayımcısı 'nı çalıştırdığınızda, performans gereksinimlerinizi ve ana bilgisayarınızda bulunan bellek kaynaklarını bilmeniz gerekir.

Bellek ve performans birbirine bağımlıdır ve her ikisi de, yayımlanacak düğüm sayısına göre yapılandırmaya bağlıdır. Aşağıdaki parametrelerin gereksinimlerinizi karşıladığından emin olun:

- IoT Hub zaman aralığı gönderir: `--si`
- IoT Hub ileti boyutu (varsayılan `1`): `--ms`
- İzlenen öğeler kuyruk kapasitesi: `--mq`

`--mq` parametresi, tüm OPC düğümü değer değişikliği bildirimlerini arabelleğe alarak iç sıranın kapasitesinin üst sınırını denetler. OPC yayımcısı IoT Hub yeterince hızlı bir şekilde ileti gönderemıyorsa, bu sıra bildirimleri arabelleğe alır. Parametresi, ara belleğe kullanılabilecek bildirimlerin sayısını ayarlar. Bu kuyruktaki öğelerin sayısını test çalıştırmalarınız içinde artırdıysanız, iletileri gevzleşmemek için şunları yapmanız gerekir:

- IoT Hub gönderme aralığını azaltma
- IoT Hub ileti boyutunu artır

`--si` parametresi OPC yayımcısı 'nı belirtilen aralıkta IoT Hub iletileri gönderecek şekilde zorlar. OPC Publisher, `--ms` parametresi tarafından belirtilen ileti boyutuna ulaşıldığında veya `--si` parametresi tarafından belirtilen aralığa ulaşıldığında, hemen bir ileti gönderir. İleti boyutu seçeneğini devre dışı bırakmak için `--ms 0`kullanın. Bu durumda OPC yayımcısı, toplu iş verilerine 256 kB 'Lık en büyük olası IoT Hub ileti boyutunu kullanır.

`--ms` parametresi, IoT Hub gönderilecek toplu iletileri sağlar. Kullanmakta olduğunuz protokol, yük göndermenin gerçek zamanına kıyasla IoT Hub ileti gönderme yükünün yüksek olup olmadığını belirler. Veriler IoT Hub tarafından yapıldığında senaryonuz gecikme süresine izin veriyorsa, OPC yayımcısını 256 kB olan en büyük ileti boyutunu kullanacak şekilde yapılandırın.

Üretim senaryolarında OPC yayımcısını kullanmadan önce, üretim koşullarında performans ve bellek kullanımını test edin. OPC yayımcısının tanılama bilgilerini yazmasının saniye cinsinden aralığını belirtmek için `--di` parametresini kullanabilirsiniz.

### <a name="test-measurements"></a>Test ölçümleri

Aşağıdaki örnek tanılama, 1 saniyelik bir OPC yayımlama aralığıyla 500 düğümü yayımlamak `--si` ve `--ms` parametreleri için farklı değerlerle ölçümleri gösterir.  Test, 120 saniye için Windows 10 ' da yerel olarak bir OPC yayımcısı hata ayıklama derlemesi kullandı. IoT Hub protokolü varsayılan MQTT protokolüdür.

#### <a name="default-configuration---si-10---ms-262144"></a>Varsayılan yapılandırma (--si 10--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Varsayılan yapılandırma, verileri her 10 saniyede bir IoT Hub veya IoT Hub almak için 256 kB 'Lık verilerin kullanılabilir olduğu durumlarda gönderir. Bu yapılandırma yaklaşık 10 saniyelik bir gecikme süresi ekler, ancak büyük ileti boyutu nedeniyle verilerin gevleşmesinin en düşük olasılığa sahiptir. Tanılama çıkışında kayıp OPC düğümü güncelleştirmesi olmadığını gösterilmektedir: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Sabit gönderme aralığı (--si 1--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

İleti boyutu 0 olarak ayarlandığında OPC Publisher, 256 kB olan en büyük desteklenen IoT Hub ileti boyutunu kullanarak verileri dahili olarak toplu olarak işler. Tanılama çıktısı, ortalama ileti boyutunu 115.019 bayt olduğunu gösterir. Bu yapılandırmada OPC yayımcısı hiçbir OPC düğümü değer güncelleştirmesini kaybetmez ve varsayılana kıyasla daha düşük gecikme süresine sahiptir.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Her OPC düğüm değeri güncelleştirmesini gönder (--si 0--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Bu yapılandırma her OPC düğümü değeri için bir ileti IoT Hub değiştirin. Tanılama, ortalama ileti boyutunu gösterir, küçük olan 234 bayttır. Bu yapılandırmanın avantajı, OPC yayımcısının herhangi bir gecikme eklemesıdır. Kayıp OPC düğüm değeri güncelleştirmeleri (`monitored item notifications enqueue failure: 44624`) yüksek olduğundan, bu yapılandırmayı yüksek hacime sahip senaryolarda yayımlanacak senaryolara uygun hale getirir.

### <a name="maximum-batching---si-0---ms-262144"></a>En fazla toplu işlem (--si 0--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Bu yapılandırma, olabildiğince fazla OPC düğüm değeri güncelleştirmesi olarak toplu işler. En büyük IoT Hub ileti boyutu, burada yapılandırılan 256 kB 'tır. Gönderme aralığı istenmez, bu, alma için IoT Hub veri miktarının gecikme süresini belirlediği anlamına gelir. Bu yapılandırma, herhangi bir OPC düğümü değerini gevşmek için en az olasılığa sahiptir ve çok sayıda düğüm yayımlamak için uygundur. Bu yapılandırmayı kullandığınızda, 256 kB 'lik ileti boyutuna ulaşılmazsa, senaryonuzun gecikme süresi yüksek olan koşullar olmadığından emin olun.

## <a name="debug-the-application"></a>Uygulamada hata ayıklama

Uygulamada hata ayıklamak için, Visual Studio ile **opcpublisher. sln** çözüm dosyasını açın ve Visual Studio hata ayıklama araçlarını kullanın.

OPC Yayımcısındaki OPC UA sunucusuna erişmeniz gerekiyorsa, güvenlik duvarınızın sunucunun dinlediği bağlantı noktasına erişmesine izin verdiğinden emin olun. Varsayılan bağlantı noktası: 62222.

## <a name="control-the-application-remotely"></a>Uygulamayı uzaktan denetleme

Yayımlanacak düğümleri yapılandırmak, IoT Hub doğrudan yöntemler kullanılarak yapılabilir.

OPC yayımcısı, okumak için birkaç ek IoT Hub doğrudan yöntem çağrısı uygular:

- Genel bilgiler.
- OPC oturumlarında, aboneliklerde ve izlenen öğelerde tanılama bilgileri.
- IoT Hub iletileri ve olayları hakkında tanılama bilgileri.
- Başlangıç günlüğü.
- Günlüğün son 100 satırı.
- Uygulamayı kapatın.

Aşağıdaki GitHub depoları, [Tanılama bilgilerini](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)yayımlamak ve okumak [üzere düğümleri yapılandırmak](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) için araçlar içerir. Her iki araç da Docker Hub 'da kapsayıcılar olarak da kullanılabilir.

## <a name="use-a-sample-opc-ua-server"></a>Örnek OPC UA sunucusu kullanma

Gerçek bir OPC UA sunucunuz yoksa, kullanmaya başlamak için [OPC UA PLC örneğini](https://github.com/Azure-Samples/iot-edge-opc-plc) kullanabilirsiniz. Bu örnek PLC, Docker Hub 'da da kullanılabilir.

Bu, anormallarla rastgele veri ve Etiketler oluşturan bir dizi etiket uygular. Ek etiket değerlerinin benzetimini yapmanız gerekiyorsa, örneği genişletebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

OPC yayımcısının nasıl çalıştırılacağını öğrendiğinize göre, önerilen sonraki adımlar [OPC ikizi](overview-opc-twin.md) ve [OPC Kasası](overview-opc-vault.md)hakkında bilgi edinmek için kullanılır.
