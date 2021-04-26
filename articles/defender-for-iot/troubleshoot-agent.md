---
title: Güvenlik Aracısı Başlangıç (Linux) sorunlarını giderme
description: Linux için IoT güvenlik aracıları için Azure Defender ile çalışma sorunlarını giderin.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 9c9c36b822ab6acb9f9a48d4ba809ad32f6f4695
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782597"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Güvenlik aracısı sorun giderme kılavuzu (Linux)

Bu makalede, güvenlik Aracısı Başlangıç sürecinde olası sorunların nasıl giderileceği açıklanmaktadır.

IoT aracısının kendisi için Azure Defender, yüklemeden hemen sonra başlar. Aracı başlatma işlemi, yerel yapılandırmayı okumayı, Azure IoT Hub bağlanmayı ve uzak ikizi yapılandırmasını almayı içerir. Bu adımlardan herhangi birinde hata, güvenlik aracısının başarısız olmasına neden olabilir.

Bu sorun giderme kılavuzunda şunları nasıl yapacağınızı öğreneceksiniz:

- Güvenlik aracısının çalışıp çalışmadığını doğrulama
- Güvenlik Aracısı hatalarını al
- Güvenlik Aracısı hatalarını anlayın ve düzeltin

## <a name="validate-if-the-security-agent-is-running"></a>Güvenlik aracısının çalışıp çalışmadığını doğrulama

1. Güvenlik aracısının çalıştığını doğrulamak için, aracıyı yükledikten sonra birkaç dakika bekleyin ve aşağıdaki komutu çalıştırın.
     <br>

    **C Aracısı**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C# Aracısı**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Komut boş bir satır döndürürse, güvenlik Aracısı başarıyla başlatılamadı.

## <a name="force-stop-the-security-agent"></a>Güvenlik aracısını durdurmayı zorla

Güvenlik aracısının başlayaamadığı durumlarda, aşağıdaki komutla aracıyı durdurun ve aşağıdaki hata tablosuna ilerleyin:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Güvenlik Aracısı hatalarını al

1. Aşağıdaki komutu çalıştırarak güvenlik Aracısı hata (ler) i alın:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Güvenlik aracısını al hatası komutu, bir IoT Aracısı için Defender tarafından oluşturulan tüm günlükleri alır. Hataları anlamak ve düzeltme için doğru adımları uygulamak üzere aşağıdaki tabloyu kullanın.

> [!Note]
> Hata günlükleri kronolojik sırada gösterilir. Düzeltbaşlamanıza yardımcı olması için her bir hatanın zaman damgasını unutmayın.

## <a name="restart-the-agent"></a>Aracıyı yeniden başlatın

1. Bir güvenlik Aracısı hatasını bulduktan ve düzelttikten sonra, aşağıdaki komutu çalıştırarak aracıyı yeniden başlatmayı deneyin.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Durdur işlemini almak ve aracı başlatma işlemini başarısız olmaya devam ederse hataları almak için önceki işlemi tekrarlayın.

## <a name="understand-security-agent-errors"></a>Güvenlik Aracısı hatalarını anlama

Güvenlik Aracısı hatalarının çoğu aşağıdaki biçimde görüntülenir:

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Hata Kodu | Hata alt kodu | Hata ayrıntıları | C 'yi düzelt | C 'yi düzelt # |
|--|--|--|--|--|
| Yerel yapılandırma | Eksik yapılandırma | Yerel yapılandırma dosyasında bir yapılandırma eksik. Hata iletisi hangi anahtarın eksik olduğunu durumdur. | Eksik anahtarı/var/LocalConfiguration.jsdosyasına ekleyin, Ayrıntılar için [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) bölümüne bakın. | Eksik anahtarı General.config dosyasına ekleyin, Ayrıntılar için [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) bölümüne bakın. |
| Yerel yapılandırma | Yapılandırma ayrıştırması yapılamıyor | Yapılandırma değeri ayrıştırılamıyor. Hata iletisi hangi anahtarın ayrıştırılamadığına yönelik olmalıdır. Değer beklenen türde olmadığından veya değer Aralık dışında olduğundan, bir yapılandırma değeri ayrıştırılamaz. | Dosyadaki/var/LocalConfiguration.jsdeğerini LocalConfiguration şemasıyla eşleşecek şekilde düzelttikten sonra Ayrıntılar için [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) bölümüne bakın. | General.config dosyadaki anahtarın değerini, şemayla eşleşecek şekilde düzelttikten sonra Ayrıntılar için [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) dosyasına bakın. |
| Yerel yapılandırma | Dosya Biçimi | Yapılandırma dosyası ayrıştırılamadı. | Yapılandırma dosyası bozuk, aracıyı indirip yeniden yükleyin. | - |
| Uzaktan yapılandırma | Zaman aşımı | Aracı, zaman aşımı süresi içinde ikizi azureiotsecurity modülünü getiremedi. | Kimlik doğrulama yapılandırmasının doğru olduğundan emin olun ve yeniden deneyin. | Aracı, zaman aşımı süresi içinde ikizi azureiotsecurity modülünü getiremedi. Kimlik doğrulama yapılandırmasının doğru olduğundan emin olun ve yeniden deneyin. |
| Kimlik Doğrulaması | Dosya yok | Verilen yoldaki dosya yok. | Dosyanın belirtilen yolda mevcut olduğundan emin olun veya dosyadaki **LocalConfiguration.js** gidin ve **FilePath** yapılandırmasını değiştirin. | Dosyanın belirtilen yolda mevcut olduğundan emin olun veya **Authentication.config** dosyasına gidin ve **FilePath** yapılandırmasını değiştirin. |
| Kimlik Doğrulaması | Dosya Izni | Aracı, dosyayı açmak için yeterli izinlere sahip değil. | **Asciotagent** kullanıcısına verilen yoldaki dosya üzerinde okuma izinleri verin. | Dosyanın erişilebilir olduğundan emin olun. |
| Kimlik Doğrulaması | Dosya Biçimi | Verilen dosya doğru biçimde değil. | Dosyanın doğru biçimde olduğundan emin olun. Desteklenen dosya türleri. pfx ve. ped. | Dosyanın geçerli bir sertifika dosyası olduğundan emin olun. |
| Kimlik Doğrulaması | Yetkisiz | Aracı verilen kimlik bilgileriyle IoT Hub karşı kimlik doğrulaması yapamadı. | LocalConfiguration dosyasında kimlik doğrulama yapılandırmasını doğrulayın, kimlik doğrulama yapılandırması ' na gidin ve tüm ayrıntıların doğru olduğundan emin olun, dosyadaki gizli dizinin kimliği doğrulanmış kimlikle eşleştiğini doğrulayın. | Kimlik doğrulama yapılandırmasını doğrulama Authentication.config, kimlik doğrulama yapılandırması ' na gidin ve tüm ayrıntıların doğru olduğundan emin olun, ardından dosyadaki gizli dizinin kimliği doğrulanmış kimlikle eşleştiğini doğrulayın. |
| Kimlik Doğrulaması | Bulunamadı | Cihaz/modül bulundu. | Kimlik doğrulama yapılandırmasını doğrulama-ana bilgisayar adının doğru olduğundan, cihazın IoT Hub olduğundan ve bir azureiotsecurity ikizi modülüne sahip olduğundan emin olun. | Kimlik doğrulama yapılandırmasını doğrulama-ana bilgisayar adının doğru olduğundan, cihazın IoT Hub olduğundan ve bir azureiotsecurity ikizi modülüne sahip olduğundan emin olun. |
| Kimlik Doğrulaması | Eksik yapılandırma | *Authentication.config* dosyasında bir yapılandırma eksik. Hata iletisi hangi anahtarın eksik olduğunu durumdur. | Dosyadaki *LocalConfiguration.js* eksik anahtarı ekleyin. | Eksik anahtarı *Authentication.config* dosyasına ekleyin, Ayrıntılar için [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) bölümüne bakın. |
| Kimlik Doğrulaması | Yapılandırma ayrıştırması yapılamıyor | Yapılandırma değeri ayrıştırılamıyor. Hata iletisi hangi anahtarın ayrıştırılamadığına yönelik olmalıdır. Değer beklenen türde olmadığından veya değer Aralık dışında olduğundan, bir yapılandırma değeri ayrıştırılamıyor. | Dosyadaki **LocalConfiguration.js** anahtarın değerini onarın. | **Authentication.config** dosyasındaki anahtarın değerini şemayla eşleşecek şekilde düzeltir, Ayrıntılar için [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) bölümüne bakın.|

## <a name="next-steps"></a>Sonraki adımlar

- IoT hizmetine [genel bakış](overview.md) Için Defender 'ı okuyun
- IoT [mimarisi](architecture.md) için Defender hakkında daha fazla bilgi edinin
- IoT [hizmeti](quickstart-onboard-iot-hub.md) Için Defender 'ı etkinleştirin
- IoT hizmeti için Defender 'ı okuyun [SSS](resources-frequently-asked-questions.md)
- [Ham güvenlik verilerine](how-to-security-data-access.md) erişme hakkında bilgi edinin
- [Önerileri](concept-recommendations.md) anlama
- Güvenlik [uyarılarını](concept-security-alerts.md) anlama
