---
title: Azure depolama geliştirme ve test için Azurıite açık kaynak öykünücüsünü kullanma (Önizleme)
description: Azurite açık kaynaklı öykünücü (Önizleme), Azure depolama uygulamalarınızı test etmek için ücretsiz bir yerel ortam sağlar.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: e611afd6f10154636eb2e0dd08437b4f7468d6b3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309554"
---
# <a name="use-the-azurite-open-source-emulator-for-azure-storage-development-and-testing-preview"></a>Azure depolama geliştirme ve test için Azurıite açık kaynak öykünücüsünü kullanma (Önizleme)

Azurite sürümü 3,2 açık kaynaklı öykünücü (Önizleme), Azure Blob ve kuyruk depolama uygulamalarınızı test etmek için ücretsiz bir yerel ortam sağlar. Uygulamanızın yerel olarak nasıl çalıştığı konusunda memnun olduğunuzda, bulutta bir Azure depolama hesabı kullanmaya geçiş yapın. Öykünücü, Windows, Linux ve MacOS 'ta platformlar arası destek sağlar. Azurite v3, Azure Blob hizmeti tarafından uygulanan API 'Leri destekler.

Azurite, gelecekteki depolama öykünücü platformudur. Azurite, [Azure depolama öykünücüsünün](storage-use-emulator.md)yerini almıştır. Azurite, Azure depolama API 'lerinin en son sürümlerini destekleyecek şekilde güncellenmeye devam edecektir.

Yerel sisteminizde Azurite yüklemek ve çalıştırmak için birkaç farklı yol vardır:

  1. [Azurite Visual Studio Code uzantısını yükleyip çalıştırma](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [NPM kullanarak Azurite 'i yükleyip çalıştırma](#install-and-run-azurite-by-using-npm)
  1. [Azurite Docker görüntüsünü yükleyip çalıştırma](#install-and-run-the-azurite-docker-image)
  1. [GitHub deposundan Azurite kopyalama, derleme ve çalıştırma](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Azurite Visual Studio Code uzantısını yükleyip çalıştırma

Visual Studio Code içinde, **Uzantılar** bölmesini seçin ve **Uzantılar: market**' te *Azurite* için arama yapın.

![Visual Studio Code uzantıları marketi](media/storage-use-azurite/azurite-vs-code-extension.png)

Alternatif olarak, tarayıcınızda [vs Code uzantısı pazarına](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) gidebilirsiniz. Visual Studio Code açmak için **Aç düğmesini seçin** ve doğrudan Azurite uzantısı sayfasına gidin.

VS Code durum çubuğunda **[Azurite blob hizmeti]** veya **[Azurıite kuyruk hizmeti]** öğesine tıklayarak veya vs Code komut paletinde aşağıdaki komutları yayımlayarak, azurıon 'u hızlı bir şekilde başlatabilir veya kapatabilirsiniz. Komut paletini açmak için VS Code **F1** tuşuna basın.

Uzantı aşağıdaki Visual Studio Code komutlarını destekler:

   * **Azurite: Başlat** -tüm Azurite hizmetlerini Başlat
   * **Azurite: Kapat** -tüm Azurite hizmetlerini kapat
   * **Azurite: Tüm** Azurite Hizmetleri için temiz verileri temizle
   * **Azurite: Blob hizmetini** Başlat-blob hizmetini Başlat
   * **Azurite: Blob hizmetini** kapat-blob hizmetini kapat
   * **Azurite: Blob hizmetini** temizle-blob hizmetini temizle
   * **Azurite: Kuyruk hizmetini** Başlat-kuyruk hizmetini Başlat
   * **Azurite: Kuyruk hizmetini** kapat-kuyruk hizmetini kapat
   * **Azurite: Temizleme kuyruğu hizmeti** -Temizleme kuyruğu hizmeti

Visual Studio Code içinde Azurite yapılandırmak için Uzantılar bölmesini seçin. **Azurite**için **Yönet** (dişli) simgesini seçin. **Uzantı ayarlarını yapılandır**' ı seçin.

![Azurite uzantı ayarlarını yapılandırma](media/storage-use-azurite/azurite-configure-extension-settings.png)

Aşağıdaki ayarlar desteklenir:

   * **Azurite: Blob Konağı** -blob hizmeti dinleme uç noktası. Varsayılan ayar 127.0.0.1 ' dir.
   * **Azurite: Blob bağlantı** noktası-blob hizmeti dinleme bağlantı noktası. Varsayılan bağlantı noktası 10000 ' dir.
   * **Azurite: Hata** Ayıkla-hata ayıklama günlüğünü Azurite kanalına çıkar. Varsayılan değer **false**.
   * **Azurite: Konum** -çalışma alanı konum yolu. Varsayılan değer Visual Studio Code çalışma klasörüdür.
   * **Azurite: Kuyruk Konağı** -dinleme uç noktası kuyruk hizmeti. Varsayılan ayar 127.0.0.1 ' dir.
   * **Azurite: Kuyruk bağlantı** noktası-dinleme bağlantı noktası kuyruk hizmeti. Varsayılan bağlantı noktası 10001 ' dir.
   * **Azurite: Sessiz** -sessiz mod, erişim günlüğünü devre dışı bırakır. Varsayılan değer **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>NPM kullanarak Azurite 'i yükleyip çalıştırma

Bu yükleme yöntemi için [Node. js sürüm 8,0 veya daha yeni bir sürümün](https://nodejs.org) yüklü olması gerekir. **NPM** , her Node. js yüklemesinde bulunan paket yönetim aracıdır. Node. js ' yi yükledikten sonra, Azurite yüklemek için aşağıdaki **NPM** komutunu yürütün.

```console
npm install -g azurite
```

Azurite yükledikten sonra, bkz. [komut satırından Azurıite çalıştırma](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Azurite Docker görüntüsünü yükleyip çalıştırma

Şu komutu kullanarak [en son Azurite görüntüsünü](https://hub.docker.com/_/microsoft-azure-storage-azurite) çekmek Için [dockerhub](https://hub.docker.com/) 'ı kullanın:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite Docker görüntüsünü çalıştırın**:

Aşağıdaki komut Azurite Docker görüntüsünü çalıştırır. Parametresi `-p 10000:10000` , istekleri ana makinenin 10000 numaralı bağlantı noktasından Docker örneğine yeniden yönlendirir.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Çalışma alanı konumunu belirtin**:

Aşağıdaki örnekte `-v c:/azurite:/data` parametresi, Azurite kalıcı veri konumu olarak *c:/Azurite* belirtir. Docker komutu çalıştırılmadan önce, *c:/Azurite*dizininin oluşturulması gerekir.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Yalnızca blob hizmetini Çalıştır**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Tüm Azurite parametrelerini ayarla**:

Bu örnekte, komut satırı parametrelerinin tümünün nasıl ayarlanacağı gösterilmektedir. Aşağıdaki parametrelerin tümü tek bir komut satırına yerleştirilmelidir.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Başlangıç aşamasında Azurite yapılandırma hakkında daha fazla bilgi için bkz. [komut satırı seçenekleri](#command-line-options) .

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
> Yalnızca Visual Studio Code uzantısını yüklediyseniz, Azurite komut satırından çalıştırılamaz. Bunun yerine VS Code komut paletini kullanın. Daha fazla bilgi için bkz. [Azurite Visual Studio Code uzantısını yükleyip çalıştırma](#install-and-run-the-azurite-visual-studio-code-extension).

Komut satırı ile hemen başlamak için, **c:\azurite**adlı bir dizin oluşturun ve aşağıdaki komutu vererek Azurite başlatın:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Bu komut, Azurite 'ın tüm verileri belirli bir dizinde ( **c:\azurite**) depolamasını söyler. **--Location** seçeneği atlanırsa, geçerli çalışma dizinini kullanacaktır.

## <a name="command-line-options"></a>Komut satırı seçenekleri

Bu bölüm, Azurıite başlatılırken kullanılabilen komut satırı anahtarlarının ayrıntılarını yapar. Tüm komut satırı anahtarları isteğe bağlıdır.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

\- **D** , **--Debug**için bir kısayoldur,- **l** anahtarı **--konumu**kısayoludur,- **s** -- **Silent**kısayoludur ve **-h** , **--help**için bir kısayoldur.

### <a name="blob-listening-host"></a>Blob dinleme ana bilgisayarı

**Isteğe bağlı** Varsayılan olarak, Azurite yerel sunucu olarak 127.0.0.1 'yi dinler. Adresi gereksinimlerinize göre ayarlamak için **--blobhost** anahtarını kullanın.

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

**Isteğe bağlı** Varsayılan olarak, Azurite bağlantı noktası 10000 üzerinde blob hizmetini dinler. İhtiyaç duyduğunuz dinleme bağlantı noktasını belirtmek için **--blobport** anahtarını kullanın.

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

**Isteğe bağlı** Varsayılan olarak, Azurite yerel sunucu olarak 127.0.0.1 'yi dinler. Adresi gereksinimlerinize göre ayarlamak için **--queuehost** anahtarını kullanın.

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

**Isteğe bağlı** Varsayılan olarak, Azurite 10001 numaralı bağlantı noktasında Kuyruk hizmeti dinleyecektir. İhtiyaç duyduğunuz dinleme bağlantı noktasını belirtmek için **--QueuePort** anahtarını kullanın.

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

**Isteğe bağlı** Azurite, yürütme sırasında verileri yerel diske depolar. Çalışma alanı konumu olarak bir yol belirtmek için **--Location** anahtarını kullanın. Varsayılan olarak, geçerli işlem çalışma dizini kullanılacaktır.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Erişim günlüğü

**Isteğe bağlı** Varsayılan olarak, erişim günlüğü konsol penceresinde görüntülenir. **--Sessiz** anahtarını kullanarak erişim günlüğü görüntüsünü devre dışı bırakın.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Hata ayıklama günlüğü

**Isteğe bağlı** Hata ayıklama günlüğü, her istek ve özel durum yığın izlemesi hakkında ayrıntılı bilgi içerir. **--Debug** anahtarına geçerli bir yerel dosya yolu sağlayarak hata ayıklama günlüğünü etkinleştirin.

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Araçlar ve SDK 'lar için yetkilendirme

Herhangi bir kimlik doğrulama stratejisi kullanarak Azure Storage SDK 'Ları veya [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)gibi araçlardan Azurıite 'e bağlanın. Kimlik doğrulaması gereklidir. Azurite, paylaşılan anahtar ve paylaşılan erişim imzaları (SAS) ile yetkilendirmeyi destekler. Azurite ayrıca ortak kapsayıcılara anonim erişimi destekler.

### <a name="well-known-storage-account-and-key"></a>İyi bilinen depolama hesabı ve anahtarı

Aşağıdaki hesap adını ve anahtarını Azurite ile birlikte kullanabilirsiniz. Bu, eski Azure depolama öykünücüsü tarafından kullanılan iyi bilinen hesap ve anahtardır.

* Hesap adı:`devstoreaccount1`
* Hesap anahtarı:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> SharedKey kimlik doğrulamasına ek olarak, Azurite hesabı ve hizmet SAS kimlik doğrulamasını destekler. Anonim erişim, bir kapsayıcı genel erişime izin verecek şekilde ayarlandığında da kullanılabilir.

### <a name="connection-string"></a>Bağlantı dizesi

Uygulamanızdan Azurite 'ya bağlanmanın en kolay yolu, uygulamanızın yapılandırma dosyasında *Usedevelopmentstorage = true*kısayoluna başvuran bir bağlantı dizesi yapılandırmaktır. Bir *app. config* dosyasındaki bağlantı dizesine bir örnek aşağıda verilmiştir:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Daha fazla bilgi için bkz. [Azure Storage bağlantı dizelerini yapılandırma](storage-configure-connection-string.md).

### <a name="storage-explorer"></a>Depolama Gezgini

Azure Depolama Gezgini, **Hesap Ekle** simgesine tıklayarak Azurite öğesine bağlanın ve ardından **Yerel öykünücüyü Ekle** ' yi seçin ve **Bağlan**' a tıklayın.

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

Azurite, ölçeklenebilir bir depolama hizmeti değildir ve çok sayıda eşzamanlı istemciyi desteklemez. Performans garantisi yoktur. Azurite, geliştirme ve test amaçları için tasarlanmıştır.

### <a name="error-handling"></a>Hata işleme

Azurite, Azure depolama hatası işleme mantığı ile hizalanır, ancak farklar vardır. Örneğin hata iletileri farklı olabilir, hata durum kodları hizalanır.

### <a name="ra-grs"></a>RA-GRS

Azurite, Okuma Erişimli Coğrafi olarak yedekli çoğaltmayı (RA-GRS) destekler. Depolama kaynakları için, hesap adına **İkincil** ekleyerek ikincil konuma erişin. Örneğin, aşağıdaki adres, Azurıite içindeki salt okunurdur ikincil kullanarak bir bloba erişmek için kullanılabilir:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite açık kaynaklı

Azurite için katkı ve öneriler hoş geldiniz. Yaklaşan Özellikler ve hata düzeltmeleri için izlemekte olduğumuz kilometre taşları ve iş öğeleri için Azurite [GitHub proje](https://github.com/Azure/Azurite/projects) sayfasına veya [GitHub sorunlarına](https://github.com/Azure/Azurite/issues) gidin. Ayrıntılı iş öğeleri GitHub 'da da izlenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Geliştirme ve test Için Azure depolama öykünücüsünü kullanarak](storage-use-emulator.md) , Azurite tarafından yenisiyle değiştirilen eski Azure depolama öykünücüsünü belgelerini kullanın.
* [Azure depolama bağlantı dizelerini yapılandırma](storage-configure-connection-string.md) geçerli bir Azure depolama bağlantı dizesinin nasıl derleneceği açıklanmaktadır.
