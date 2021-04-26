---
title: Azure Percept DK ve IoT Edge genel sorunlarını giderme
description: Azure Percept DK ile ilgili daha yaygın sorunlardan bazıları için sorun giderme ipuçları alın
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c9c62ec07873272b956877ec51d8765ae0bbd100
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605646"
---
# <a name="azure-percept-dk-troubleshooting"></a>Azure Percept DK sorunlarını giderme

Azure Percept DK için genel sorun giderme ipuçları için aşağıdaki kılavuza bakın.

## <a name="general-troubleshooting-commands"></a>Genel sorun giderme komutları

Bu komutları çalıştırmak için, [Geliştirme Seti ' ne SSH ekleyin](./how-to-ssh-into-percept-dk.md) ve SSH istemci istemine komutları girin.

Daha fazla analiz için herhangi bir çıktıyı bir. txt dosyasına yönlendirmek için aşağıdaki sözdizimini kullanın:

```console
sudo [command] > [file name].txt
```

. Txt dosyasının kopyalanabilmesi için izinleri değiştirin:

```console
sudo chmod 666 [file name].txt
```

Çıktıyı bir. txt dosyasına yönlendirdikten sonra SCP aracılığıyla dosyayı ana bilgisayara kopyalayın:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` ,. txt dosyasını kopyalamak istediğiniz ana bilgisayar bilgisayarınızdaki konuma başvurur. ```[remote username]``` , [kurulum deneyimi](./quickstart-percept-dk-set-up.md)sırasında SSH Kullanıcı adı seçilir.

Azure IoT Edge komutları hakkında daha fazla bilgi için bkz. [Azure IoT Edge cihaz sorun giderme belgeleri](../iot-edge/troubleshoot.md).

|Kategori:         |Komutundaki                    |Çalışmayacaktır                  |
|------------------|----------------------------|---------------------------|
|İşletim Sistemi                |```cat /etc/os-release```         |Mariner görüntü sürümünü denetle |
|İşletim Sistemi                |```cat /etc/os-subrelease```      |Türev görüntü sürümünü denetle |
|İşletim Sistemi                |```cat /etc/adu-version```        |ADU sürümünü denetle |
|Sıcaklık       |```cat /sys/class/thermal/thermal_zone0/temp``` |devkit 'in sıcaklığını denetleyin |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |SoftAP günlüklerine bakın|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |Wi-Fi Services günlüklerine bakın |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |Wi-Fi sıfır dokunma sağlama hizmeti günlüklerine bakın |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |Mariner ağ yığını günlüklerine bakın |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |WiFi erişim noktası yapılandırma ayrıntılarını denetle |
|OOBE              |```sudo journalctl -u oobe -b```       |OOBE günlüklerini denetleyin |
|Telemetri         |```sudo azure-device-health-id```      |benzersiz telemetri HW_ID bulun |
|Azure IoT Edge          |```sudo iotedge check```          |sık karşılaşılan sorunlar için yapılandırma ve bağlantı denetimlerini çalıştır |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |konuşma ve vizyon modülleri gibi kapsayıcı günlüklerini denetleyin |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |Son saatten modül günlüklerini, Azure IoT Edge Güvenlik Yöneticisi günlüklerini, kapsayıcı altyapısı günlüklerini, ```iotedge check``` JSON çıkışını ve diğer yararlı hata ayıklama bilgilerini toplayın |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |Azure IoT Edge Güvenlik Yöneticisi 'nin günlüklerini görüntüleme |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Azure IoT Edge güvenlik cini 'nı yeniden başlatın |
|Azure IoT Edge          |```sudo iotedge list```           |dağıtılan Azure IoT Edge modüllerini listeleyin |
|Diğer             |```df [option] [file]```          |Belirtilen dosya sistemlerinde kullanılabilir/toplam alan bilgilerini görüntüle |
|Diğer             |`ip route get 1.1.1.1`        |Cihaz IP ve arabirim bilgilerini görüntüle |
|Diğer             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |yalnızca cihaz IP adresini görüntüle |


```journalctl```Wi-Fi komutları aşağıdaki tek komutla birleştirilebilir:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker sorun giderme komutları

|Komutundaki                        |Çalışmayacaktır                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[Hangi kapsayıcıların çalıştığını gösterir](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[cihazda hangi görüntülerin olduğunu gösterir](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[cihazdan bir görüntüyü siler](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[Belirtilen modülün kapsayıcı günlüklerini alır](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[Tüm sallaştırılmış görüntüleri kaldırır](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |Docker kapsayıcısı indirme durumunu denetle |

## <a name="usb-updates"></a>USB güncelleştirmeleri

|Hata:                                    |Çözüm:                                               |
|------------------------------------------|--------------------------------------------------------|
|UıUU aracılığıyla USB Flash sırasında LIBUSB_ERROR_XXX |Bu hata, UıUU güncelleştirmesi sırasında USB bağlantı hatasının sonucudur. USB kablosu PC veya Percept DK taşıyıcı panosundaki USB bağlantı noktalarına düzgün şekilde bağlı değilse, bu formdan bir hata oluşur. USB kablosunun her iki ucunu da yeniden bağlamayı ve güvenli bir bağlantı sağlamak için kabloyu jiggze yapmayı deneyin. Bu neredeyse her zaman sorunu çözer. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK taşıyıcı panosu LED durumları

Taşıyıcı Pano Muhafazası üzerinde üç küçük LED vardır. 1. LED 'in yanına bir bulut simgesi yazdırılır, LED 2 ' nin yanında bir Wi-Fi simgesi yazdırılır ve LED 3 ' ün yanında ünlem işareti simgesi yazdırılır. Her LED durumu hakkında bilgi için aşağıdaki tabloya bakın.

|GELIŞTIRMESINE             |Durum      |Açıklama                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |Açık (Solid) |Cihaz bir IoT Hub bağlı. |
|LED 2 (Wi-Fi)   |Yavaş yanıp sönme |Cihaz, Wi-Fi kolay bağlantı tarafından yapılandırılmaya hazırlanıyor ve bir yapılandırıcıda varlığını duyuruyor. |
|LED 2 (Wi-Fi)   |Hızlı yanıp sönen |Kimlik doğrulama başarılı oldu, cihaz ilişkilendirmesi devam ediyor. |
|LED 2 (Wi-Fi)   |Açık (Solid) |Kimlik doğrulama ve ilişkilendirme başarılı oldu; cihaz bir Wi-Fi ağa bağlı. |
|LED 3           |NA         |LED kullanımda değil. |