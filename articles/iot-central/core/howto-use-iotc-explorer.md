---
title: Azure IoT Central gezginini kullanarak cihaz bağlantısını izleme
description: Cihaz iletilerini izleyin ve IoT Central Explorer CLı aracılığıyla cihaz ikizi değişikliklerini gözlemleyin.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949618"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Azure IoT Central gezginini kullanarak cihaz bağlantısını izleme

*Bu konu, oluşturucular ve yöneticiler için geçerlidir.*

Cihazlarınızın IoT Central gönderdiği iletileri görmek ve IoT Hub ikizi değişiklikleri gözlemlemek için IoT Central Explorer CLı kullanın. Bu açık kaynaklı Aracı, cihaz bağlantısı durumu hakkında ayrıntılı bilgi edinmek ve ikizi değişikliklere yanıt vermeyen buluta veya cihazlara ulaşmayan cihaz iletilerinin sorunlarını tanılamak için kullanabilirsiniz.

[GitHub 'da ıotc-Explorer deposunu ziyaret edin.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Önkoşullar

+ Node. js sürüm 8. x veya üzeri- https://nodejs.org
+ Uygulamanızın Yöneticisi, ıotc-Explorer 'da kullanabilmeniz için bir erişim belirteci üretmelidir

## <a name="install-iotc-explorer"></a>İotc-Explorer 'ı yükler

Yüklemek için komut satırınızdan aşağıdaki komutu çalıştırın:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Genellikle Install komutunu UNIX benzeri ortamlarda `sudo` çalıştırmanız gerekir.

## <a name="run-iotc-explorer"></a>İotc-Explorer Çalıştır

Aşağıdaki bölümlerde, `iotc-explorer`çalıştırdığınızda kullanabileceğiniz ortak komutlar ve seçenekler açıklanır. Komutların ve seçeneklerin tam kümesini görüntülemek için `--help` `iotc-explorer` veya alt komutlarının birine geçirin.

### <a name="login"></a>Oturum aç

Çalışmaya başlamadan önce, kullanmanız için bir erişim belirteci almak üzere IoT Central uygulamanızın yöneticisine sahip olmanız gerekir. Yönetici aşağıdaki adımları gerçekleştirir:

1. **Yönetim** ' e gidin ve **belirteçlere erişin**.
1. **Belirteç oluştur**' u seçin.
    ![erişim belirteci sayfası ekran görüntüsü](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Bir belirteç adı girin, **İleri**' yi ve ardından **Kopyala**' yı seçin.
    > [!NOTE]
    > Belirteç değeri yalnızca bir kez gösteriliyor, bu nedenle iletişim kutusu kapatılmadan önce kopyalanmalıdır. İletişim kutusunu kapattıktan sonra hiç bir daha gösterilmemiştir.

    ![Erişim belirteci Kopyala iletişim kutusu ekran görüntüsü](media/howto-use-iotc-explorer/copyaccesstoken.png)

Aşağıdaki gibi, CLı 'da oturum açmak için belirtecini kullanabilirsiniz:

```cmd/sh
iotc-explorer login "<Token value>"
```

Kabuk geçmişinizde belirtecin kalıcı olmasını tercih ediyorsanız, belirteci dışarı bırakabilir ve istendiğinde şunları yapabilirsiniz:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Cihaz iletilerini izleme

`monitor-messages` komutunu kullanarak, belirli bir cihazdan ya da uygulamanızdaki tüm cihazlardan gelen iletileri izleyebilirsiniz. Bu komut, geldikçe yeni iletileri sürekli çıkaran bir izleyici başlatır:

Uygulamanızdaki tüm cihazları izlemek için aşağıdaki komutu çalıştırın:

```cmd/sh
iotc-explorer monitor-messages
```

Çıktı:

![Monitor-messages komut çıktısı](media/howto-use-iotc-explorer/monitormessages.png)

Belirli bir cihazı izlemek için, yalnızca komutun sonuna cihaz KIMLIĞI ekleyin:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Ayrıca, komutuna `--raw` seçeneğini ekleyerek daha fazla makineye yönelik bir biçim de aktarabilirsiniz:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Cihaz ikizi al

IoT Central bir cihaz için ikizi içeriğini almak üzere `get-twin` komutunu kullanabilirsiniz. Bunu yapmak için aşağıdaki komutu çalıştırın:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Çıktı:

![Get-ikizi komutu çıkışı](media/howto-use-iotc-explorer/getdevicetwin.png)

`monitor-messages`gibi, `--raw` seçeneğini geçirerek makineye yönelik daha kolay bir çıkış edinebilirsiniz:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Sonraki adımlar

IoT Central gezgin 'i nasıl kullanacağınızı öğrendiğinize göre, önerilen sonraki adım [cihazları yönetmeyi IoT Central](howto-manage-devices.md)keşfetmeye yönelik olarak sunulmuştur.
