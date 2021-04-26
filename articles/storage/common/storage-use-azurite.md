---
title: Yerel Azure depolama geliştirmesi için Azurite öykünücüsünü kullanma
description: Azurite açık kaynaklı öykünücü, Azure depolama uygulamalarınızı test etmek için ücretsiz bir yerel ortam sağlar.
author: twooley
ms.author: twooley
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: a921de0d976b9c92ca7978feb7caf69484ba9379
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277134"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>Yerel Azure depolama geliştirmesi için Azurite öykünücüsünü kullanma

Azurite açık kaynaklı öykünücü, Azure Blob ve kuyruk depolama uygulamalarınızı test etmek için ücretsiz bir yerel ortam sağlar. Uygulamanızın yerel olarak nasıl çalıştığı konusunda memnun olduğunuzda, bulutta bir Azure depolama hesabı kullanmaya geçiş yapın. Öykünücü, Windows, Linux ve macOS 'ta platformlar arası destek sağlar.

Azurite, gelecekteki depolama öykünücü platformudur. Azurite, [Azure depolama öykünücüsünün](storage-use-emulator.md)yerini almıştır. Azurite, Azure depolama API 'lerinin en son sürümlerini destekleyecek şekilde güncellenmeye devam edecektir.

Yerel sisteminizde Azurite yüklemek ve çalıştırmak için birkaç farklı yol vardır:

  1. [Azurite Visual Studio Code uzantısını yükleyip çalıştırma](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [NPM kullanarak Azurite 'i yükleyip çalıştırma](#install-and-run-azurite-by-using-npm)
  1. [Azurite Docker görüntüsünü yükleyip çalıştırma](#install-and-run-the-azurite-docker-image)
  1. [GitHub deposundan Azurite kopyalama, derleme ve çalıştırma](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Azurite Visual Studio Code uzantısını yükleyip çalıştırma

Visual Studio Code içinde, **Uzantılar** bölmesini seçin ve **Uzantılar: market**' te *Azurite* için arama yapın.

![Visual Studio Code uzantıları marketi](media/storage-use-azurite/azurite-vs-code-extension.png)

Ayrıca, tarayıcınızda [Visual Studio Code uzantısı pazarına](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) gidebilirsiniz. Visual Studio Code açmak için **Aç düğmesini seçin** ve doğrudan Azurite uzantısı sayfasına gidin.

Uzantı aşağıdaki Visual Studio Code komutlarını destekler. Komut paletini açmak için Visual Studio Code F1 tuşuna basın. 

   - **Azurite:** tüm Azurite Hizmetleri kalıcılığı verilerini temizle
   - **Azurite: Temizleme blobu hizmeti** -Temizleme blobu hizmeti
   - **Azurite: Temizleme kuyruğu hizmeti** -Temizleme kuyruğu hizmeti
   - **Azurite:** tüm Azurite hizmetlerini kapat
   - **Azurite: blob hizmetini kapat** -blob hizmetini kapat
   - **Azurite: kuyruk hizmetini kapat** -kuyruk hizmetini kapat
   - **Azurite: Başlat** -tüm Azurite hizmetlerini Başlat
   - **Azurite: blob hizmetini Başlat** -blob hizmetini Başlat
   - **Azurite: kuyruk hizmetini Başlat** -kuyruk hizmetini Başlat

Visual Studio Code içinde Azurite yapılandırmak için Uzantılar bölmesini seçin. **Azurite** için **Yönet** (dişli) simgesini seçin. **Uzantı ayarları**' nı seçin.

![Azuri 'ler uzantı ayarlarını yapılandırır](media/storage-use-azurite/azurite-configure-extension-settings.png)

Aşağıdaki ayarlar desteklenir:

   - **Azurite: blob Konağı** -blob hizmeti dinleme uç noktası. Varsayılan ayar 127.0.0.1 ' dir.
   - **Azurite: blob bağlantı** noktası-blob hizmeti dinleme bağlantı noktası. Varsayılan bağlantı noktası 10000 ' dir.
   - **Azurite:** https modunu etkinleştirmek için yerel olarak güvenilen bir ped veya PFX Sertifika dosya yolunun CERT yolu.
   - **Azurite: hata** ayıklama-hata ayıklama günlüğünü Azurite kanalına çıkar. Varsayılan değer **false** şeklindedir.
   - **Azurite:** **Azurite: CERT** bir ped dosyasına işaret ediyorsa, yerel olarak güvenilen bir PEI anahtar dosyasının anahtar yolu.
   - **Azurite: konum** -çalışma alanı konum yolu. Varsayılan değer Visual Studio Code çalışma klasörüdür.
   - **Azurite:** , desteklenmeyen üst bilgileri ve parametreleri yok sayan gevşek modunu etkinleştirin.
   - **Azurite: OAuth** Isteğe bağlı OAuth düzeyi.
   - **Azurite: PWD** -pfx dosyası parolası. **Azurite: CERT** bir PFX dosyasına işaret ediyorsa gereklidir.
   - **Azurite: kuyruk Konağı** -dinleme uç noktası kuyruk hizmeti. Varsayılan ayar 127.0.0.1 ' dir.
   - **Azurite: kuyruk bağlantı** noktası-dinleme bağlantı noktası kuyruk hizmeti. Varsayılan bağlantı noktası 10001 ' dir.
   - **Azurite: sessiz** -sessiz mod, erişim günlüğünü devre dışı bırakır. Varsayılan değer **false** şeklindedir.
   - **Azurite: API sürüm denetimini atla** -Istek API 'si sürüm denetimini atla. Varsayılan değer **false** şeklindedir.

## <a name="install-and-run-azurite-by-using-npm"></a>NPM kullanarak Azurite 'i yükleyip çalıştırma

Bu yükleme yöntemi, [Node.js sürüm 8,0 veya sonraki bir sürümün](https://nodejs.org) yüklü olmasını gerektirir. Düğüm Paket Yöneticisi (NPM), her Node.js yüklemesinde bulunan paket yönetim aracıdır. Node.js yükledikten sonra, `npm` Azurite yüklemek için aşağıdaki komutu yürütün.

```console
npm install -g azurite
```

Azurite yükledikten sonra, bkz. [komut satırından Azurite çalıştırma](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Azurite Docker görüntüsünü yükleyip çalıştırma

Şu komutu kullanarak [en son Azurite görüntüsünü](https://hub.docker.com/_/microsoft-azure-storage-azurite) çekmek Için [dockerhub](https://hub.docker.com/) 'ı kullanın:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite Docker görüntüsünü çalıştırın**:

Aşağıdaki komut Azurite Docker görüntüsünü çalıştırır. `-p 10000:10000`Parametresi, istekleri ana makinenin 10000 numaralı bağlantı noktasından Docker örneğine yeniden yönlendirir.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Çalışma alanı konumunu belirtin**:

Aşağıdaki örnekte `-v c:/azurite:/data` parametresi, Azurite kalıcı veri konumu olarak *c:/Azurite* belirtir. Docker komutu çalıştırılmadan önce, *c:/Azurite* dizininin oluşturulması gerekir.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Yalnızca blob hizmetini Çalıştır**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Başlangıç aşamasında Azurite yapılandırma hakkında daha fazla bilgi için bkz. [komut satırı seçenekleri](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>GitHub deposundan Azurite kopyalama, derleme ve çalıştırma

Bu yükleme yöntemi, [Git](https://git-scm.com/) 'in yüklü olmasını gerektirir. Aşağıdaki konsol komutunu kullanarak Azurite projesi için [GitHub deposunu](https://github.com/azure/azurite) kopyalayın.

```console
git clone https://github.com/Azure/Azurite.git
```

Kaynak kodu kopyaladıktan sonra, Azurite oluşturmak ve yüklemek için klonlanan deponun kökünden aşağıdaki komutları yürütün.

```console
npm install
npm run build
npm install -g
```

Azurite yükledikten ve oluşturduktan sonra, bkz. [komut satırından Azurıite çalıştırma](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Bir komut satırından Azurıite çalıştırma

> [!NOTE]
> Yalnızca Visual Studio Code uzantısını yüklediyseniz, Azurite komut satırından çalıştırılamaz. Bunun yerine Visual Studio Code komut paletini kullanın. Daha fazla bilgi için bkz. [Azurite Visual Studio Code uzantısını yükleyip çalıştırma](#install-and-run-the-azurite-visual-studio-code-extension).

Komut satırı ile hemen başlamak için, *c:\azurite* adlı bir dizin oluşturun, ardından aşağıdaki komutu vererek Azurite başlatın:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Bu komut, Azurite 'ın tüm verileri belirli bir dizinde ( *c:\azurite*) depolamasını söyler. `--location`Seçenek atlanırsa, geçerli çalışma dizinini kullanacaktır.

## <a name="command-line-options"></a>Komut satırı seçenekleri

Bu bölüm, Azurıite başlatılırken kullanılabilen komut satırı anahtarlarının ayrıntılarını yapar.

### <a name="help"></a>Help

**Isteğe bağlı** -veya anahtarını kullanarak komut satırı yardımını alın `-h` `--help` .

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Blob dinleme ana bilgisayarı

**Isteğe bağlı** -varsayılan olarak, Azurite yerel sunucu olarak 127.0.0.1 'yi dinler. `--blobHost`Adresi gereksinimlerinize göre ayarlamak için anahtarını kullanın.

İstekleri yalnızca yerel makinede kabul et:

```console
azurite --blobHost 127.0.0.1
```

Uzak isteklere izin ver:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Uzak isteklere izin vermek sisteminizi dış saldırılara karşı savunmasız hale getirebilir.

### <a name="blob-listening-port-configuration"></a>Blob dinleme bağlantı noktası yapılandırması

**Isteğe bağlı** -varsayılan olarak, Azurite bağlantı noktası 10000 üzerinde blob hizmeti dinleyecektir. `--blobPort`Gerekli dinleme bağlantı noktasını belirtmek için anahtarını kullanın.

> [!NOTE]
> Özelleştirilmiş bir bağlantı noktasını kullandıktan sonra, Azure depolama araçlarınızın veya SDK 'larındaki bağlantı dizesini veya ilgili yapılandırmayı güncelleştirmeniz gerekir.

Blob hizmeti dinleme bağlantı noktasını özelleştirin:

```console
azurite --blobPort 8888
```

Sistemin kullanılabilir bir bağlantı noktasını otomatik olarak seçmesini sağlayın:

```console
azurite --blobPort 0
```

Kullanımdaki bağlantı noktası Azurite başlatması sırasında görüntülenir.

### <a name="queue-listening-host"></a>Kuyruk dinleme Konağı

**Isteğe bağlı** -varsayılan olarak, Azurite yerel sunucu olarak 127.0.0.1 'yi dinler. `--queueHost`Adresi gereksinimlerinize göre ayarlamak için anahtarını kullanın.

İstekleri yalnızca yerel makinede kabul et:

```console
azurite --queueHost 127.0.0.1
```

Uzak isteklere izin ver:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Uzak isteklere izin vermek sisteminizi dış saldırılara karşı savunmasız hale getirebilir.

### <a name="queue-listening-port-configuration"></a>Kuyruk dinleme bağlantı noktası yapılandırması

**Isteğe bağlı** -varsayılan olarak Azurite, 10001 numaralı bağlantı noktasında kuyruk hizmeti dinleyecektir. `--queuePort`Gerekli dinleme bağlantı noktasını belirtmek için anahtarını kullanın.

> [!NOTE]
> Özelleştirilmiş bir bağlantı noktasını kullandıktan sonra, Azure depolama araçlarınızın veya SDK 'larındaki bağlantı dizesini veya ilgili yapılandırmayı güncelleştirmeniz gerekir.

Kuyruk hizmeti dinleme bağlantı noktasını özelleştirin:

```console
azurite --queuePort 8888
```

Sistemin kullanılabilir bir bağlantı noktasını otomatik olarak seçmesini sağlayın:

```console
azurite --queuePort 0
```

Kullanımdaki bağlantı noktası Azurite başlatması sırasında görüntülenir.

### <a name="workspace-path"></a>Çalışma alanı yolu

**Isteğe bağlı** -Azurite, yürütme sırasında verileri yerel diske depolar. `-l` `--location` Çalışma alanı konumu olarak bir yol belirtmek için veya anahtarını kullanın. Varsayılan olarak, geçerli işlem çalışma dizini kullanılacaktır. Küçük harfli ' l ' değerini aklınızda edin.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Erişim günlüğü

**Isteğe bağlı** -varsayılan olarak, erişim günlüğü konsol penceresinde görüntülenir. Veya anahtarını kullanarak erişim günlüğü görüntüsünü devre dışı bırakın `-s` `--silent` .

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Hata ayıklama günlüğü

**Isteğe bağlı** -hata ayıklama günlüğü, her istek ve özel durum yığın izlemesi hakkında ayrıntılı bilgi içerir. Veya anahtarına geçerli bir yerel dosya yolu sağlayarak hata ayıklama günlüğünü etkinleştirin `-d` `--debug` .

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Gevşek mod

**Isteğe bağlı** -varsayılan olarak Azurite, desteklenmeyen istek üst bilgilerini ve parametrelerini engellemek için katı mod uygular. Veya anahtarını kullanarak katı modu devre dışı bırakın `-L` `--loose` . Sermaye ' L ' değerini aklınızda edin.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Sürüm

**Isteğe bağlı** -veya anahtarını kullanarak yüklü Azurite sürüm numarasını görüntüleyin `-v` `--version` .

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Sertifika yapılandırması (HTTPS)

**Isteğe bağlı** -varsayılan olarak, Azurite http protokolünü kullanır. Anahtara gizlilik Gelişmiş posta (. pek) veya [kişisel bilgi değişimi (. pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) sertifika dosyası için bir yol sağlayarak https modunu etkinleştirin `--cert` .

`--cert`BIR ped dosyası için sağlandığında, buna karşılık gelen bir anahtar sağlamanız gerekir `--key` .

```console
azurite --cert path/server.pem --key path/key.pem
```

`--cert`, BIR pfx dosyası için sağlandığında, karşılık gelen bir anahtar sağlamanız gerekir `--pwd` .

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

PEK ve PFX dosyaları oluşturma hakkında ayrıntılı bilgi için bkz. [https kurulumu](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>OAuth yapılandırması

**Isteğe bağlı** -anahtarı kullanarak Azurite için OAuth kimlik doğrulamasını etkinleştirin `--oauth` .

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth bir HTTPS uç noktası gerektirir. Anahtarla birlikte anahtar sağlayarak HTTPS 'nin etkinleştirildiğinden emin olun `--cert` `--oauth` .

Azurite, anahtarın parametresini belirterek temel kimlik doğrulamasını destekler `basic` `--oauth` . Azurite, gelen taşıyıcı belirtecini doğrulama, verenin, izleyici ve süre sonu denetleme gibi temel kimlik doğrulaması yapar. Azurite, belirteç imzasını veya izinleri denetlemez.

### <a name="skip-api-version-check"></a>API sürüm denetimini atla

**Isteğe bağlı** -Başlarken, Azurite, istenen API sürümünün geçerli olduğunu denetler. Aşağıdaki komut API sürüm denetimini atlar:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Araçlar ve SDK 'lar için yetkilendirme

Herhangi bir kimlik doğrulama stratejisi kullanarak Azure Storage SDK 'Ları veya [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)gibi araçlardan Azurıite 'e bağlanın. Kimlik doğrulaması gereklidir. Azurite, OAuth, paylaşılan anahtar ve paylaşılan erişim imzaları (SAS) ile yetkilendirmeyi destekler. Azurite ayrıca ortak kapsayıcılara anonim erişimi destekler.

Azure SDK 'larını kullanıyorsanız, seçeneklerle Azurite ' ı başlatın `--oauth basic and --cert --key/--pwd` .

### <a name="well-known-storage-account-and-key"></a>İyi bilinen depolama hesabı ve anahtarı

Azurite, eski Azure depolama öykünücüsü tarafından kullanılan aynı tanınmış hesabı ve anahtarı kabul eder.

- Hesap adı: `devstoreaccount1`
- Hesap anahtarı: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Özel depolama hesapları ve anahtarları

Azurite, ortam değişkenini aşağıdaki biçimde ayarlayarak özel depolama hesabı adlarını ve anahtarlarını destekler `AZURITE_ACCOUNTS` : `account1:key1[:key2];account2:key1[:key2];...` .

Örneğin, bir anahtara sahip özel bir depolama hesabı kullanın:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Ya da iki anahtar içeren birden çok depolama hesabı kullanın:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite, varsayılan olarak her dakikada ortam değişkeninden özel hesap adlarını ve anahtarlarını yeniler. Bu özellikle, hesap anahtarını dinamik olarak döndürebilir veya Azurite 'ı yeniden başlatmanıza gerek kalmadan yeni depolama hesapları ekleyebilirsiniz.

> [!NOTE]
> `devstoreaccount1`Özel depolama hesapları ayarladığınızda varsayılan depolama hesabı devre dışıdır.

### <a name="connection-strings"></a>Bağlantı dizeleri

Uygulamanızdan Azurite 'ya bağlanmanın en kolay yolu, uygulamanızın yapılandırma dosyasında *Usedevelopmentstorage = true* kısayoluna başvuran bir bağlantı dizesi yapılandırmaktır. Bir *app.config* dosyasındaki bağlantı dizesine bir örnek aşağıda verilmiştir:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP bağlantı dizeleri

Aşağıdaki bağlantı dizelerini Azure CLı 2,0 veya Depolama Gezgini gibi [Azure SDK](https://aka.ms/azsdk) 'larına veya araçlarına geçirebilirsiniz.

Tam bağlantı dizesi:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Yalnızca blob hizmetine bağlanmak için bağlantı dizesi:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Yalnızca kuyruk hizmetine bağlanmak için bağlantı dizesi:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS bağlantı dizeleri

Tam HTTPS bağlantı dizesi:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Yalnızca blob hizmetini kullanmak için HTTPS bağlantı dizesi:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Yalnızca kuyruk hizmetini kullanmak için HTTPS bağlantı dizesi:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

`dotnet dev-certs`Otomatik olarak imzalanan sertifikanızı oluşturmak için kullandıysanız, aşağıdaki bağlantı dizesini kullanın.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

[Özel depolama hesapları ve anahtarlar](#custom-storage-accounts-and-keys)kullanırken bağlantı dizesini güncelleştirin.

Daha fazla bilgi için bkz. [Azure Storage bağlantı dizelerini yapılandırma](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>Azure SDK’ları

[Azure SDK](https://aka.ms/azsdk)'Leriyle Azurite kullanmak için OAUTH ve https seçeneklerini kullanın:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Depolama

Daha sonra bir BlobContainerClient, BlobServiceClient veya BlobClient örneği oluşturabilirsiniz.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Kuyruk Depolama

Ayrıca bir QueueClient veya QueueServiceClient örneği oluşturabilirsiniz.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini

Azurite içinde depolanan verileri görüntülemek için Depolama Gezgini kullanabilirsiniz.

#### <a name="connect-to-azurite-using-http"></a>HTTP kullanarak Azurite 'a bağlanma

Depolama Gezgini, aşağıdaki adımları izleyerek Azurite öğesine bağlanın:

 1. **Hesapları Yönet** simgesini seçin
 1. **Hesap Ekle** ' yi seçin
 1. **Yerel öykünücüye Ekle** seçeneğini belirleyin
 1. **İleri**’yi seçin
 1. **Görünen ad** alanını seçtiğiniz bir adla düzenleyin
 1. **İleri ' yi** yeniden seçin
 1. **Bağlan** 'ı seçin

#### <a name="connect-to-azurite-using-https"></a>HTTPS kullanarak Azurite 'a bağlanma

Varsayılan olarak Depolama Gezgini otomatik olarak imzalanan bir sertifika kullanan bir HTTPS uç noktası açmaz. HTTPS ile Azurite çalıştırıyorsanız, muhtemelen kendinden imzalı bir sertifika kullanıyorsunuz demektir. Depolama Gezgini, SSL sertifikalarını **düzenleme**  ->    ->  **sertifikaları içeri aktar** iletişim kutusunu kullanarak SSL sertifikalarını içeri aktarın.

##### <a name="import-certificate-to-storage-explorer"></a>Sertifikayı Depolama Gezgini içeri aktar

1. Yerel makinenizde sertifikayı bulun.
1. Depolama Gezgini ' de,   ->  **SSL sertifikalarını** düzenleme  ->  **sertifikaları içeri** aktarma ve sertifikanızı içeri aktarma bölümüne gidin.

Bir sertifikayı içeri aktarmazsanız bir hata alırsınız:

`unable to verify the first certificate` veya `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>HTTPS bağlantı dizesi aracılığıyla Azurite ekleme

Depolama Gezgini için Azurite HTTPS eklemek üzere aşağıdaki adımları izleyin:

1. **Gezgin 'ı değiştirmeyi** Seç
1. **Ekli yerel &** seçin
1. **Depolama hesapları** ' na sağ tıklayın ve **Azure depolama 'ya Bağlan**' ı seçin.
1. **Bağlantı dizesi kullan** ' ı seçin
1. **İleri**’yi seçin.
1. **Görünen ad** alanına bir değer girin.
1. Bu belgenin önceki bölümündeki [HTTPS bağlantı dizesini](#https-connection-strings) girin
1. **İleri**’yi seçin
1. **Bağlan** 'ı seçin

## <a name="workspace-structure"></a>Çalışma alanı yapısı

Azurite başlatılırken çalışma alanı konumunda aşağıdaki dosyalar ve klasörler oluşturulabilir.

- `__blobstorage__` -Azurite blob hizmeti kalıcı ikili verileri içeren dizin
- `__queuestorage__` -Azurite kuyruğu hizmeti kalıcı ikili verileri içeren dizin
- `__azurite_db_blob__.json` -Azurite blob hizmeti meta veri dosyası
- `__azurite_db_blob_extent__.json` -Azurite blob hizmeti uzantısı meta veri dosyası
- `__azurite_db_queue__.json` -Azurite kuyruğu hizmeti meta veri dosyası
- `__azurite_db_queue_extent__.json` -Azurite kuyruk hizmeti uzantısı meta veri dosyası

Azurıite 'yi temizlemek için dosya ve klasörleri silin ve öykünücüyü yeniden başlatın.

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite ve Azure depolama arasındaki farklılıklar

Yerel bir Azurite örneği ve buluttaki bir Azure depolama hesabı arasında işlevsel farklılıklar vardır.

### <a name="endpoint-and-connection-url"></a>Uç nokta ve bağlantı URL 'SI

Azurite için hizmet uç noktaları, bir Azure depolama hesabının uç noktalarından farklıdır. Yerel bilgisayar, etki alanı adı çözümlemesi yapmaz ve Azurite uç noktalarının yerel adresler olmasını gerektirir.

Bir Azure depolama hesabındaki bir kaynağı adresettiğinizde, hesap adı URI ana bilgisayar adının bir parçasıdır. Değinmekte olan kaynak URI yolunun bir parçası:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Aşağıdaki URI, Azure depolama hesabındaki bir blob için geçerli bir adrestir:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Yerel bilgisayar etki alanı adı çözümlemesi gerçekleştirmediğinden, hesap adı konak adı yerine URI yolunun bir parçasıdır. Azurite içindeki bir kaynak için aşağıdaki URI biçimini kullanın:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Aşağıdaki adres, Azurite içindeki bir bloba erişmek için kullanılabilir:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Ölçeklendirme ve performans

Azurite, çok sayıda bağlı istemciyi desteklemez. Performans garantisi yoktur. Azurite, geliştirme ve test amaçları için tasarlanmıştır.

### <a name="error-handling"></a>Hata işleme

Azurite, Azure depolama hatası işleme mantığı ile hizalanır, ancak farklar vardır. Örneğin hata iletileri farklı olabilir, hata durum kodları hizalanır.

### <a name="ra-grs"></a>RA-GRS

Azurite, Okuma Erişimli Coğrafi olarak yedekli çoğaltmayı (RA-GRS) destekler. Depolama kaynakları için, hesap adına ekleyerek ikincil konuma erişin `-secondary` . Örneğin, aşağıdaki adres, Azurıite içindeki salt okunurdur ikincil kullanarak bir bloba erişmek için kullanılabilir:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Tablo desteği

Azurite içindeki tablolar için destek şu anda geliştirme aşamasındadır ve katkı olarak açıktır! En son ilerleme için [Azurite v3 tablo](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) projesini denetleyin.

Dayanıklı işlevlere yönelik destek tabloları gerektirir.

## <a name="azurite-is-open-source"></a>Azurite açık kaynaklı

Azurite için katkı ve öneriler hoş geldiniz. Yaklaşan Özellikler ve hata düzeltmeleri için izlemekte olduğumuz kilometre taşları ve iş öğeleri için Azurite [GitHub proje](https://github.com/Azure/Azurite/projects) sayfasına veya [GitHub sorunlarına](https://github.com/Azure/Azurite/issues) gidin. Ayrıntılı iş öğeleri GitHub 'da da izlenir.

## <a name="next-steps"></a>Sonraki adımlar

- [Geliştirme ve test Için Azure depolama öykünücüsünü kullanarak](storage-use-emulator.md) , Azurite tarafından yenisiyle değiştirilen eski Azure depolama öykünücüsünü belgelerini kullanın.
- [Azure depolama bağlantı dizelerini yapılandırma](storage-configure-connection-string.md) geçerli bir Azure depolama bağlantı dizesinin nasıl derleneceği açıklanmaktadır.
