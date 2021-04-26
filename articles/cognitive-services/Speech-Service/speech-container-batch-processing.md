---
title: Konuşma kapsayıcıları için Batch işleme seti
titleSuffix: Azure Cognitive Services
description: Konuşma kapsayıcısı isteklerini ölçeklendirmek için Batch işleme setini kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: cc6bcef77ca1601b76468586aa6af202836f1438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97632001"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Konuşma kapsayıcıları için Batch işleme seti

Konuşma kapsayıcılarındaki iş yüklerini tamamlamak ve genişletmek için Batch işleme setini kullanın. Bir kapsayıcı olarak kullanılabilir, bu açık kaynaklı yardımcı program, çok sayıda ses dosyası için, her türlü şirket içi ve bulut tabanlı konuşma kapsayıcısı uç noktası arasında toplu olarak bir dökümü kolaylaştırmaya yardımcı olur. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Örnek Batch-Kit kapsayıcı iş akışını gösteren diyagram.":::

Toplu iş seti kapsayıcısı [GitHub](https://github.com/microsoft/batch-processing-kit) ve   [Docker Hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)'da ücretsiz olarak kullanılabilir. Yalnızca kullandığınız konuşma kapsayıcıları için [faturalandırılırsınız](speech-container-howto.md#billing) .

| Özellik  | Açıklama  |
|---------|---------|
| Toplu ses dosyası dağıtımı     | Şirket içi veya bulut tabanlı konuşma kapsayıcısı uç noktalarına çok sayıda dosyayı otomatik olarak gönderir. Dosyalar, ağ dosya sistemleri dahil olmak üzere POSIX ile uyumlu herhangi bir birimde olabilir.       |
| Konuşma SDK 'Sı tümleştirmesi | Yaygın bayrakları konuşma SDK 'sına geçirin; örneğin: n-en iyi hipotezleri, atlama, dil, küfür maskeleme.  |
|Çalıştırma modları     | Batch istemcisini, arka planda sürekli olarak çalıştırın veya ses dosyaları için HTTP uç noktaları oluşturun.         |
| Hataya dayanıklılık | İlerlemeye devam etmeden otomatik olarak yeniden deneyin ve devam edin, ve hangi hataların kullanılabilir olduğunu ve üzerinde yeniden denenemez. |
| Uç nokta kullanılabilirliği algılama | Bir uç nokta kullanılamaz hale gelirse, toplu iş istemcisi diğer kapsayıcı uç noktalarını kullanarak bir şekilde devam eder. Yeniden kullanılabilir duruma geçtikten sonra istemci, uç noktayı kullanmaya otomatik olarak başlayacaktır.   |
| Uç nokta etkin değiştirme | Toplu işlem ilerlemesini kesintiye uğramadan çalışma zamanında konuşma kapsayıcısı uç noktaları ekleyin, kaldırın veya değiştirin. Güncelleştirmeler hemen. |
| Gerçek zamanlı günlüğe kaydetme | Her ses dosyası için konuşma SDK günlük dosyaları ile yapılmaya çalışılan isteklerin, zaman damgalarının ve hata nedenlerinin gerçek zamanlı günlüğü. |

## <a name="get-the-container-image-with-docker-pull"></a>Kapsayıcı görüntüsünü al `docker pull`

En son Batch seti kapsayıcısını indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Uç nokta yapılandırması

Batch istemcisi, şirket içi kapsayıcı uç noktalarını belirten bir YAML yapılandırma dosyası alır. Aşağıdaki örnekte `/mnt/my_nfs/config.yaml` , aşağıdaki örneklerde kullanılan öğesine yazılabilir. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Bu YAML örneği üç ana bilgisayarda üç konuşma kapsayıcısı belirtir. İlk ana bilgisayar bir IPv4 adresi ile belirtilir, ikincisi ise Batch istemcisiyle aynı VM üzerinde çalışır ve üçüncü kapsayıcı başka bir sanal makinenin DNS ana bilgisayar adı ile belirtilir. `concurrency`Değer, aynı kapsayıcıda çalışabilecek maksimum eşzamanlı dosya dökümünü belirtir. `rtf`(Gerçek zamanlı faktör) değeri isteğe bağlıdır ve performansı ayarlamak için kullanılabilir.
 
Batch istemcisi, bir uç noktanın kullanılamaz hale geldiğini (örneğin, bir kapsayıcı yeniden başlatma veya ağ sorunu nedeniyle) dinamik olarak algılayabilir ve yeniden kullanılabilir hale gelir. Döküm istekleri kullanılamayan kapsayıcılara gönderilmez ve istemci, diğer kullanılabilir kapsayıcıları kullanmaya devam eder. Toplu iş sürecini ilerlemeden istediğiniz zaman uç noktalar ekleyebilir, kaldırabilir veya düzenleyebilirsiniz.


## <a name="run-the-batch-processing-container"></a>Toplu işlem kapsayıcısını çalıştırma
  
> [!NOTE] 
> * Bu örnek `/my_nfs` yapılandırma dosyası ve girişler, çıktılar ve Günlükler dizinleri için aynı dizini () kullanır. Bu klasörler için barındırılan veya NFS bağlı dizinleri kullanabilirsiniz.
> * İstemcisini ile çalıştırmak, `–h` kullanılabilir komut satırı parametrelerini ve bunların varsayılan değerlerini listeler. 
> * Toplu işlem kapsayıcısı yalnızca Linux üzerinde desteklenir.

`run`Kapsayıcıyı başlatmak Için Docker komutunu kullanın. Bu, kapsayıcı içinde etkileşimli bir kabuk başlatacak.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Batch istemcisini çalıştırmak için:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Batch istemcisini ve kapsayıcısını tek bir komutta çalıştırmak için:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


İstemci çalışmaya başlayacak. Bir ses dosyası önceki bir çalıştırmaya zaten eklenmiş ise, istemci dosyayı otomatik olarak atlar. Dosyalar, geçici hatalar oluşursa otomatik yeniden denemeye gönderilir ve istemcisini yeniden denemek istediğiniz hataları ayırt edebilirsiniz. Bir döküm hatası durumunda, istemci, devam etmeden devam eder ve devam etmeden yeniden deneyebilir.  

## <a name="run-modes"></a>Çalıştırma modları 

Batch işleme seti, parametresini kullanarak üç mod sunar `--run-mode` .

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` mod, tek bir ses dosyası toplu işi (bir giriş dizininden ve isteğe bağlı dosya listesinden) çıkış klasörüne dökümünü sağlar.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Oneshot modundaki batch paketi kapsayıcısı işleme dosyalarını gösteren bir diyagram.":::

1. Batch istemcisinin dosyada kullanacağı konuşma kapsayıcısı uç noktalarını tanımlayın `config.yaml` . 
2. Giriş dizinine döküm için ses dosyalarını yerleştirin.  
3. Dizindeki kapsayıcıyı çağırın, bu da dosyaları işlemeye başlayacaktır. Ses dosyası, aynı çıkış dizini (aynı dosya adı ve sağlama toplamı) ile önceki bir çalıştırmaya zaten eklenmiş ise, istemci dosyayı atlar. 
4. Dosyalar, 1. adımdaki kapsayıcı uç noktalarına dağıtılır.
5. Günlükler ve konuşma kapsayıcısı çıkışı belirtilen çıkış dizinine döndürülür. 

#### <a name="daemon"></a>[İnin](#tab/daemon)

> [!TIP]
> Giriş dizinine aynı anda birden çok dosya eklenirse, bunları düzenli bir aralığa ekleyerek performansı artırabilirsiniz.

`DAEMON` mod, belirli bir klasöre var olan dosyaları yeniden bes ve yeni ses dosyalarını eklendikçe sürekli olarak yeniden ekledi.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Daemon modundaki Batch-Kit kapsayıcı işleme dosyalarını gösteren diyagram.":::

1. Batch istemcisinin dosyada kullanacağı konuşma kapsayıcısı uç noktalarını tanımlayın `config.yaml` . 
2. Kapsayıcıyı bir giriş dizininde çağırın. Batch istemcisi, gelen dosyalar için dizini izlemeye başlayacaktır. 
3. Giriş dizinine sürekli ses dosyası teslimi ayarlayın. Ses dosyası, aynı çıkış dizini (aynı dosya adı ve sağlama toplamı) ile önceki bir çalıştırmaya zaten eklenmiş ise, istemci dosyayı atlar. 
4. Dosya yazma veya POSIX sinyali algılandıktan sonra, kapsayıcı yanıt vermek üzere tetiklenir.
5. Dosyalar, 1. adımdaki kapsayıcı uç noktalarına dağıtılır.
6. Günlükler ve konuşma kapsayıcısı çıkışı belirtilen çıkış dizinine döndürülür. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` mod, ses dosyası toplu işlem gönderimi, durum denetimi ve uzun yoklama için temel bir HTTP uç noktası kümesi sağlayan bir API sunucu modudur. Ayrıca, bir Python modül uzantısı kullanılarak veya bir alt modül olarak içeri aktarırken programlı tüketim sunar.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="REST modundaki batch paketi kapsayıcısı işleme dosyalarını gösteren diyagram.":::

1. Batch istemcisinin dosyada kullanacağı konuşma kapsayıcısı uç noktalarını tanımlayın `config.yaml` . 
2. API sunucusunun uç noktalarından birine bir HTTP istek isteği gönderin. 
        
    |Uç Nokta  |Açıklama  |
    |---------|---------|
    |`/submit`     | Yeni toplu iş istekleri oluşturmak için uç nokta.        |
    |`/status`     | Toplu iş isteğinin durumunu denetlemek için uç nokta. Toplu işlem tamamlanana kadar bağlantı açık kalır.       |
    |`/watch`     | Batch tamamlanana kadar HTTP uzun yoklamayı kullanmaya yönelik uç nokta.        |

3. Ses dosyaları giriş dizininden karşıya yüklenir. Ses dosyası, aynı çıkış dizini (aynı dosya adı ve sağlama toplamı) ile önceki bir çalıştırmaya zaten eklenmiş ise, istemci dosyayı atlar. 
4. Uç noktaya bir istek gönderilirse `/submit` , dosyalar adım 1 ' deki kapsayıcı uç noktalarına gönderilir.
5. Günlükler ve konuşma kapsayıcısı çıkışı belirtilen çıkış dizinine döndürülür. 

---

## <a name="logging"></a>Günlüğe Kaydetme

> [!NOTE]
> Batch istemcisi çok büyük alırsa *Run. log* dosyasının üzerine düzenli olarak yazılabilir.

İstemci, Docker komutunda bağımsız değişken tarafından belirtilen dizinde bir *Run. log* dosyası oluşturur `-log_folder` `run` . Günlükler, varsayılan olarak hata ayıklama düzeyinde yakalanır. Aynı Günlükler öğesine gönderilir `stdout/stderr` ve `-file_log_level` veya `console_log_level` bağımsız değişkenlerine göre filtrelenir. Bu günlük yalnızca hata ayıklama için gereklidir veya destek için bir izleme göndermeniz gerekir. Günlük klasörü, her ses dosyası için konuşma SDK günlüklerini de içerir.

Tarafından belirtilen çıkış dizini `-output_folder`    , her 30 saniyede bir veya yeni döküm her tamamlandığında düzenli olarak yeniden yazılabilir birrun_summary.jsdosyası içerecektir. Toplu işlem devam ederken ilerlemeyi denetlemek için bu dosyayı kullanabilirsiniz. Toplu işlem tamamlandığında her dosyanın son çalıştırma istatistiklerini ve nihai durumunu da içerir. İşlemin temiz bir çıkışı olduğunda Batch tamamlanır. 

## <a name="next-steps"></a>Sonraki adımlar

* [Kapsayıcıları yüklemek ve çalıştırmak](speech-container-howto.md)
