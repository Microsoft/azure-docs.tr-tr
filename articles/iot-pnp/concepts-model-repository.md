---
title: Cihaz modelleri deposunun kavramlarını anlayın | Microsoft Docs
description: Bir çözüm geliştiricisi veya BT uzmanı olarak, cihaz modelleri deposunun temel kavramları hakkında bilgi edinin.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582655"
---
# <a name="device-models-repository"></a>Cihaz modelleri deposu

Cihaz modelleri deposu (DMR), cihaz oluşturucuların IoT Tak ve Kullan cihaz modellerini yönetmesine ve paylaşmasına olanak sağlar. Cihaz modelleri, [dijital TWINS modelleme dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)KULLANıLARAK tanımlanan JSON ld belgelerdir.

DMR, DTDL arabirimlerini cihaz ikizi modeli tanımlayıcısı (DTMı) temelinde bir klasör yapısına depolamak için bir model tanımlar. DTMı 'yi göreli bir yola dönüştürerek DMR 'de bir arabirim bulabilirsiniz. Örneğin, `dtmi:com:example:Thermostat;1` dtmı öğesine çevrilir `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-models-repository"></a>Ortak cihaz modelleri deposu

Microsoft, şu özelliklerle genel bir DMR barındırır:

- Seçkin modeller. Microsoft, GitHub çekme isteği (PR) doğrulama iş akışını kullanarak tüm kullanılabilir arabirimleri gözden geçirir ve onaylar.
- Değiştirilemezlik.  Yayımlandıktan sonra bir arabirim güncelleştirilemiyor.
- Hiper ölçek. Microsoft, cihaz modellerini yayımlayabileceğiniz ve kullanabileceğiniz güvenli, ölçeklenebilir bir uç nokta oluşturmak için gerekli altyapıyı sağlar.

## <a name="custom-device-models-repository"></a>Özel cihaz modelleri deposu

Yerel dosya sistemi veya özel HTTP Web sunucuları gibi herhangi bir depolama ortamında özel bir DMR oluşturmak için aynı DMR modelini kullanın. Özel DMR 'den cihaz modellerini genel DMR 'den aynı şekilde, DMR 'e erişmek için kullanılan temel URL 'YI değiştirerek alabilirsiniz.

> [!NOTE]
> Microsoft, genel DMR 'de cihaz modellerini doğrulamaya yönelik araçlar sağlar. Bu araçları özel depolarda yeniden kullanabilirsiniz.

## <a name="public-models"></a>Ortak modeller

Modeller deposunda depolanan ortak cihaz modelleri, herkesin uygulamalarında kullanması ve tümleştirilmesi için kullanılabilir. Ortak cihaz modelleri, cihaz oluşturucular ve çözüm geliştiricilerinin IoT Tak ve Kullan cihaz modellerini paylaşmasını ve yeniden kullanmasına olanak sağlayan açık bir ekonomik sistem sağlar.

Model oluşturma bölümünde model yayımlama hakkında yönergeler için [model yayımlama](#publish-a-model) bölümüne bakın.

Kullanıcılar resmi [GitHub deposundan](https://github.com/Azure/iot-plugandplay-models)ortak arabirimlere gözatabilir, arama yapabilir ve görüntüleyebilir.

Klasörlerdeki tüm arabirimler `dtmi` genel uç noktada da kullanılabilir [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Modelleri çözümle

Bu arabirimlere programlı bir şekilde erişmek için `ModelsRepositoryClient` [Azure. IoT. ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository)NuGet paketindeki kullanılabilir öğesini kullanabilirsiniz. Bu istemci varsayılan olarak [DeviceModels.Azure.com](https://devicemodels.azure.com/) adresinde bulunan genel dmr 'yi sorgulamak için yapılandırılır ve herhangi bir özel depoya yapılandırılabilir.

İstemci bir `DTMI` as girişi kabul eder ve tüm gerekli arabirimlerin bulunduğu bir sözlük döndürür:

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Beklenen çıktı, `DTMI` bağımlılık zincirinde bulunan üç arabirimin sayısını görüntülemelidir:

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

, `ModelsRepositoryClient` Http (s) üzerinden kullanılabilir olan özel bir model deposunu sorgulamak için yapılandırılabilir ve kullanılabilir herhangi birini kullanarak bağımlılık çözünürlüğünü belirtir `ModelDependencyResolution` :

- Devre dışı. Hiçbir bağımlılığı olmadan yalnızca belirtilen arabirimi döndürür.
- Etkin. Bağımlılık zincirindeki tüm arabirimleri döndürür
- TryFromExpanded. `.expanded.json`Önceden hesaplanmış bağımlılıkları almak için dosyasını kullanın 

> [!Tip] 
> Özel depolar dosyayı açığa sunmayabilir `.expanded.json` , yoksa istemci her bağımlılığı yerel olarak işlemeye geri dönüş olur.

Sonraki örnek kodda `ModelsRepositoryClient` özel bir depo temel URL 'si kullanılarak nasıl başlatıldığı gösterilmektedir. Bu durumda, bu örnekte `raw` form KULLANıLMADAN GitHub API 'sindeki URL 'ler kullanılarak, bu durum `expanded` `raw` uç noktada kullanılamaz. , `AzureEventSourceListener` İstemcisi tarafından GERÇEKLEŞTIRILEN http isteğini incelemek üzere başlatılır:

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Azure SDK GitHub deposundaki kaynak kodunda daha fazla örnek mevcuttur: [Azure. IoT. ModelsRepository/Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>Modeli yayımlama

> [!Important]
> Genel DMR 'e model gönderebilmek için bir GitHub hesabınızın olması gerekir.

1. Genel GitHub deposunu çatalla: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Ele geçirilen depoyu kopyalayın. Değişikliklerinizin dalından yalıtılmış olmasını sağlamak için isteğe bağlı olarak yeni bir dal oluşturun `main` .
1. Klasörü `dtmi` /dosya adı kuralını kullanarak yeni arabirimleri klasöre ekleyin. Daha fazla bilgi için bkz. [bir modeli `dtmi/` klasöre aktarma](#import-a-model-to-the-dtmi-folder).
1. Aracı kullanarak modelleri yerel olarak doğrulayın `dmr-client` . Daha fazla bilgi için bkz. [modelleri doğrulama](#validate-models).
1. Değişiklikleri yerel olarak işleyin ve Çatalınıza gönderin.
1. Çatalınızdan dalı hedefleyen bir çekme isteği oluşturun `main` . Bkz. [bir sorun veya çekme isteği belgeleri oluşturma](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. [Çekme isteği gereksinimlerini](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)gözden geçirin.

Çekme isteği, gönderilen arabirimleri doğrulayan bir GitHub eylemi kümesi tetikler ve çekme isteğinizin tüm gereksinimleri karşıladığından emin olur.

Microsoft, bir çekme isteğine üç iş günü içinde tüm denetimleri vererek yanıt verir.

### <a name="dmr-client-tools"></a>`dmr-client` Aracı

PR denetimleri sırasında modelleri doğrulamak için kullanılan araçlar, DTDL arabirimlerini yerel olarak eklemek ve doğrulamak için de kullanılabilir.

> [!NOTE]
> Bu araç, [.NET SDK](https://dotnet.microsoft.com/download) sürüm 3,1 veya üstünü gerektirir.

### <a name="install-dmr-client"></a>Yükleyeceğiniz `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Klasöre model aktarma `dtmi/`

Modelinize zaten JSON dosyalarında depolandıysanız, bu `dmr-client import` komutu, `dtmi/` doğru dosya adlarıyla klasöre eklemek için kullanabilirsiniz:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> `--local-repo`Yerel depo kök klasörünü belirtmek için bağımsız değişkenini kullanabilirsiniz.

### <a name="validate-models"></a>Modelleri doğrula

Şu komutla modellerinizi doğrulayabilirsiniz `dmr-client validate` :

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> Doğrulama, tüm arabirimlerin DTDL dil belirtimiyle uyumlu olduğundan emin olmak için en son DTDL ayrıştırıcısı sürümünü kullanır.

Dış bağımlılıkları doğrulamak için yerel depoda mevcut olmaları gerekir. Modelleri doğrulamak için, `--repo` `local` bağımlılıkları çözümlemek üzere bir veya klasörü belirtmek için seçeneğini kullanın `remote` :

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Katı doğrulama

DMR ek [gereksinimler](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)içerir, `stict` modelinizi bunlara karşı doğrulamak için bayrağını kullanın:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Hata iletileri için konsol çıktısını denetleyin.

### <a name="export-models"></a>Modelleri dışarı aktarma

Modeller, belirli bir depodan (yerel veya uzak) bir JSON dizisi kullanılarak tek bir dosyaya aktarılabilir:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Sonraki adımlar

Önerilen sonraki adım [ıot Tak ve kullan mimarisini](concepts-architecture.md)gözden geçirmeye yönelik olur.
