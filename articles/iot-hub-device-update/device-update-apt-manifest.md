---
title: Azure IoT Hub APT bildirimi için cihaz güncelleştirmesini anlama | Microsoft Docs
description: IoT Hub cihaz güncelleştirmesinin paket tabanlı bir güncelleştirme için apt bildirimini nasıl kullandığını anlayın.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 878748fcfc9b096e340b53c06969962af99f603f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561177"
---
# <a name="device-update-apt-manifest"></a>Cihaz güncelleştirme APT bildirimi

APT bildirimi, APT güncelleştirme Işleyicisi için gereken güncelleştirme ayrıntılarını açıklayan bir JSON dosyasıdır. Bu dosya, diğer tüm güncelleştirmeler gibi IoT Hub için cihaz güncelleştirmesine aktarılabilir.

Güncelleştirmeleri cihaz güncelleştirmesine aktarma hakkında [daha fazla bilgi edinin](import-update.md) .

## <a name="overview"></a>Genel Bakış

Bir APT bildirimi bir güncelleştirme olarak cihaz güncelleştirme aracısına teslim edildiğinde, aracı bildirimi işler ve gerekli işlemleri gerçekleştirebilir. Bu işlemler APT bildirim dosyasında ve bağımlılıklarında belirtilen paketlerin indirilmesini ve yüklenmesini içerir.

Cihaz güncelleştirme APT UpdateType ve APT güncelleştirme Işleyicisini destekler. Bu destek, cihaz güncelleştirme aracısının yüklü DESI paketlerini değerlendirmesini ve gerekli paketleri güncelleştirmesini sağlar. 

## <a name="schema"></a>Şema

Bir APT bildirim dosyası, sürümlü bir şemaya sahip bir JSON dosyasıdır.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Örnek:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Bu APT bildiriminin adı. Bu, senaryolarınız için anlamlı bir ad veya KIMLIK olabilir. Örneğin, `contoso-iot-edge`.

### <a name="version"></a>sürüm

Bu APT bildirimi için bir sürüm numarası. Örneğin, `1.0.0.0`.


### <a name="packages"></a>paketlerle

Pakete özgü özellikleri içeren nesnelerin listesi.

#### <a name="name"></a>name

Paketin adı veya KIMLIĞI. Örneğin, `iotedge`.

#### <a name="version"></a>sürüm

Paket için istenen sürüm ölçütü. Örneğin, `1.0.8-2`.

Şu anda yalnızca tam sürüm numarası desteklenir. Sürüm numarası, [dönem:] upstream_version [-debian_revision] biçiminde istenen detem paket sürümüdür.

**Dönem** işaretsiz bir int.

**upstream_version** alfasayısal karakterleri ve ".", "+", "-" ve "~" karakterlerini içerebilir. Bir rakamla başlamalıdır.
> [!NOTE]
> ' 1.0.8 ', ' 1.0.8-0 ' değerine eşittir

Örneğin, **`"name":"iotedge" and "version":"1.0.8-2"`** komutunu kullanarak bir paket yükleme ile eşdeğerdir `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> Sürüm değeri eşittir işareti içermiyor

Sürüm atlanırsa, belirtilen paketin en son kullanılabilir sürümü yüklenir.

De, paketlerinin sürümü oluşturma hakkında [daha fazla bilgi edinin](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) .

> [!NOTE]
> APT paket yöneticisi, yüklenecek bağımlı paketler otomatik olarak çözümlendiğinde bir paket tarafından verilen sürüm oluşturma gereksinimlerini yoksayar. Bağımlı paketlerin açık sürümleri verilmese de, paketin kendisi belirli bir sürümde kesin bir gereksinim (=) belirtse bile en son, bu, en sonuncuyu kullanacaktır. Bu otomatik çözüm, karşılanmadı bağımlılığı ile ilgili hatalara yol açabilir. [Daha Fazla Bilgi](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Azure IoT Edge güvenlik arka plan programının belirli bir sürümünü güncelleştiriyorsanız, paketin istenen sürümünü `iotedge` ve ona bağlı `libiothsm-std` paketini apt bildiriminiz içinde dahil etmelisiniz.
[Daha Fazla Bilgi](../iot-edge/how-to-update-iot-edge.md#update-the-security-daemon)

> [!NOTE]
> Bir apt bildirimi, cihaz güncelleştirme aracısını ve onun bağımlılıklarını güncelleştirmek için kullanılabilir. Diğer tüm paketler için yaptığınız gibi, apt bildiriminde Cihaz Güncelleştirme Aracısı adını ve istenen sürümü listeleyin. Bu apt bildirimi daha sonra IoT Hub işlem hattı için cihaz güncelleştirmesi aracılığıyla içeri ve dağıtılabilir. 

## <a name="removing-packages"></a>Paketler kaldırılıyor

Ayrıca, yüklü paketleri cihazınızdan kaldırmak için bir apt bildirimi de kullanabilirsiniz. Tek bir apt bildirimi, birden çok paketi kaldırmak, eklemek ve güncelleştirmek için kullanılabilir. Bir paketi kaldırmak için paket adından sonra bir eksi işareti "-" ekleyin. Kaldırdığınız paketler için bir sürüm numarası dahil etmemeniz gerekir. Bir apt bildirimi aracılığıyla paketlerin kaldırılması, bağımlılıklarını ve yapılandırmasını kaldırmaz.

Örnek:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Bu apt bildirimi, dağıtıldığı cihazdan "foo" paketini kaldıracak. 

## <a name="recommended-value-for-installed-criteria"></a>Yüklü ölçütler için önerilen değer

Bir APT bildirimi için yüklenmiş ölçütler `<name>-<version>` nerede `<name>` apt bildiriminin adıdır ve `<version>` apt bildiriminin sürümüdür. Örneğin, `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>APT bildirimi oluşturma yönergeleri

APT bildirimini oluştururken aklınızda bulundurmanız gereken bazı yönergeler vardır:

- Her zaman APT bildiriminin iyi biçimlendirilmiş bir JSON dosyası olduğundan emin olun
- Her APT bildiriminin benzersiz bir sürümü olmalıdır. APT bildiriminin sürümünü artırmak için standartlaştırılmış bir metodolojiye sahip olun, böylece senaryolarınız için anlamlı hale gelir ve kolayca izlenebilir
- Her tek paketin istenen durumuna geldiğinde, cihazınıza yüklemek istediğiniz paketin tam adını ve sürümünü belirtin. Değerleri her zaman paketin kaynağı olarak kullanmayı düşündüğünüz paket deposuna göre doğrulayın
- APT bildirimindeki paketlerin yüklenmesi/kaldırılması gereken sırada listelendiğinden emin olun
- Sonucun istendiğinden emin olmak için bir test cihazında paketlerin yüklenmesini her zaman doğrulayın
- Bir paketin belirli bir sürümünü yüklerken (örneğin, `iotedge 1.0.9-1` ), APT bildiriminde, yüklenecek bağımlı paketlerin açık sürümlerini de (örneğin,) de sahip olmanız en iyi uygulamadır. `libiothsm 1.0.9-1`
- Uygulanan olmadığı sürece, cihazınızın bilinmeyen bir duruma alınmasını önlemek için APT bildiriminizde her zaman birikimli olduğundan emin olun. Toplu güncelleştirme, cihazlarda hata nedeniyle bir APT güncelleştirme dağıtımı atlanmışsa veya çevrimdışı duruma getirildiğinde, cihazlarınızın istediğiniz her paketin istenen sürümüne sahip olmasını sağlayacaktır.

Örnek:

**Temel APT bildirimi**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**HATALı GÜNCELLEŞTIRME**

Bu güncelleştirme, foo paketini değil, çubuk paketini içerir.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**IYI GÜNCELLEŞTIRME**

Bu güncelleştirme foo Package içerir ve çubuk paketini de içerir.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yeni güncelleştirme al](import-update.md)