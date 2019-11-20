---
title: Öğretici-Azure 'da Yeumman kullanarak bir Terrayform temel şablonu oluşturma
description: Azure 'da Yeumman kullanarak bir Terrayform temel şablonu oluşturmayı öğrenin.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba81d0ee797fd879fdadc3a6b25ca8f310383f61
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159158"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Öğretici: Azure 'da Yeumman kullanarak bir Terrayform temel şablonu oluşturma

Bu öğreticide [Terrayform](/azure/terraform/) ve [yeumman](https://yeoman.io/)birleşimini kullanmayı öğreneceksiniz. Terrayform, Azure üzerinde altyapı oluşturmaya yönelik bir araçtır. Yeumman, Teraform modülleri oluşturmayı kolaylaştırır.

Bu makalede, aşağıdaki görevleri nasıl gerçekleştireceğinizi öğreneceksiniz:
> [!div class="checklist"]
> * Yeumman modül oluşturucuyu kullanarak bir temel Teroyform şablonu oluşturun.
> * Terrayform şablonunu iki farklı yöntem kullanarak test edin.
> * Bir Docker dosyası kullanarak Terrayform modülünü çalıştırın.
> * Terrayform modülünü Azure Cloud Shell yerel olarak çalıştırın.

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- **Visual Studio Code**: platformunuz için [Visual Studio Code indirin](https://code.visualstudio.com/download) .
- **Terrayform**: yeumman tarafından oluşturulan modülü çalıştırmak Için [terrayform 'u yükler](/azure/virtual-machines/linux/terraform-install-configure ) .
- **Docker**: yeumman Oluşturucu tarafından oluşturulan modülü çalıştırmak Için [Docker 'ı yükler](https://www.docker.com/get-started) .
- **Go programlama dili**: [Install](https://golang.org/) , yeumman tarafından oluşturulan test çalışmaları go dilini kullanan koddur.

>[!NOTE]
>Bu öğreticideki yordamların çoğu komut satırı arabirimini içerir. Açıklanan adımlar tüm işletim sistemleri ve komut satırı araçları için geçerlidir. Örnekler için, PowerShell yerel ortam ve Cloud Shell ortamı için git bash için seçilmiştir.

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

### <a name="install-nodejs"></a>Node.js yükleme

Terraform'u Cloud Shell'de kullanabilmek için [Node.js](https://nodejs.org/en/download/) 6.0+ sürümünü yüklemeniz gerekir.

>[!NOTE]
>Node.js uygulamasının yüklü olup olmadığını belirlemek için bir terminal penceresi açıp `node --version` yazın.

### <a name="install-yeoman"></a>Yeoman’ı yükleme

Şu komutu çalıştırın:

```bash
npm install -g yo
```

![Yeoman’ı yükleme](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Terraform modülü için Yeoman şablonunu yükleyin

Şu komutu çalıştırın:

```bash
npm install -g generator-az-terra-module
```

![generator-az-terra-module yükleyin](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Yeumman 'un yüklendiğini doğrulamak için şu komutu çalıştırın:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Yeumman tarafından oluşturulan modül için dizin oluşturma

Yeumman şablonu, geçerli dizindeki dosyaları oluşturur. Bu nedenle, bir dizin oluşturmanız gerekir.

Bu boş dizinin $GOPATH/src altına yerleştirilmesi gerekir. Bu yol hakkında daha fazla bilgi için [GOPATH ayarlama](https://github.com/golang/go/wiki/SettingGOPATH)makalesine bakın.

1. Yeni dizin oluşturmak için kullanılacak üst dizine gidin.

1. Yer tutucusunu değiştirerek aşağıdaki komutu çalıştırın. Bu örnekte, `GeneratorDocSample` dizin adı kullanılır.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Yeni dizine gidin:

    ```bash
    cd <new-directory-name>
    ```

    ![Yeni dizininize gidin](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Temel modül şablonu oluşturma

1. Şu komutu çalıştırın:

    ```bash
    yo az-terra-module
    ```

1. Ekrandaki yönergeleri izleyerek aşağıdaki bilgileri sağlayın:

    - **Terrayform modülü proje adı** -örnek için `doc-sample-module` değeri kullanılır.

        ![Proje adı](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Docker görüntü dosyasını eklemek ister misiniz?** -`y`girin. `n`' yi seçerseniz, oluşturulan modül kodu yalnızca yerel modda çalışmayı destekleyecektir.

        ![Docker görüntü dosyası eklensin mi?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Oluşturulan sonuç dosyalarını görüntülemek için dizin içeriğini listeleyin:

    ```bash
    ls
    ```

    ![Oluşturulan dosyaları listeleme](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Oluşturulan modülü kodunu gözden geçirme

1. Visual Studio Code'u başlatma

1. Menü çubuğundan **Dosya > Klasör Aç**'ı ve oluşturduğunuz klasörü seçin.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Aşağıdaki dosyalar Yeumman modül üreticisi tarafından oluşturulmuştur. Bu dosyalar ve kullanımları hakkında daha fazla bilgi için bkz [. Terrayform modüllerinde Terraytest.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf`-`random-shuffle`adlı bir modülü tanımlar. Giriş bir `string_list`. Çıktı, permütasyon sayısıdır.
- `variables.tf`-modül tarafından kullanılan giriş ve çıkış değişkenlerini tanımlar.
- `outputs.tf`-modülün ne çıkış yaptığını tanımlar. Burada, bir yerleşik, Teraform modülü olan `random_shuffle`tarafından döndürülen değer.
- `Rakefile`-derleme adımlarını tanımlar. Bu adımlar şunlardır:
    - `build`-main.tf dosyasının biçimlendirmesini doğrular.
    - `unit`-oluşturulan modül iskelet 'i birim testi için kod içermez. Bir birim testi senaryosu belirtmek isterseniz, o kodu burada eklersiniz.
    - `e2e`-modülün uçtan uca testini çalıştırır.
- `test`
    - Test çalışmaları Go dilinde yazılır.
    - Testteki tüm kodlar uçtan uca testlerdir.
    - Uçtan uca testler `fixture`altında tanımlanan tüm öğeleri sağlamaya çalışır. `template_output.go` dosyasındaki sonuçlar önceden tanımlanmış beklenen değerlerle karşılaştırılır.
    - `Gopkg.lock` ve `Gopkg.toml`: bağımlılıkları tanımlar. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Yeni Teraform modülünüzü bir Docker dosyası kullanarak test etme

Bu bölümde, bir Docker dosyası kullanarak bir Teraform modülünün nasıl test yapılacağı gösterilmektedir.

>[!NOTE]
>Bu örnek, modülü yerel olarak çalıştırır; Azure 'da değil.

### <a name="confirm-docker-is-installed-and-running"></a>Docker’ın yüklü ve çalışır durumda olduğunu onaylayın

Bir komut isteminden `docker version` komutunu girin.

![Docker sürümü](media/terraform-vscode-module-generator/ymg-docker-version.png)

Elde edilen çıktı, Docker'ın yüklü olduğunu onaylar.

Docker’ın gerçekten çalışır durumda olduğunu onaylamak için `docker info` girin.

![Docker bilgisi](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Docker kapsayıcısı ayarlama

1. Bir komut isteminden şu komutu girin:

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    **Başarıyla derlendi** iletisi gösterilir.

    ![Başarılı bir derlemeyi belirten ileti](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Komut isteminden, oluşturulan `terra-mod-example` listelenen modülünüzü görmek için `docker image ls` girin.

    ![Yeni modülün bulunduğu liste](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. `docker run -it terra-mod-example /bin/sh` yazın. `docker run` komutunu çalıştırdıktan sonra Docker ortamınızdan olursunuz. Bu noktada, `ls` komutunu kullanarak dosyayı keşfedebilirsiniz.

    ![Docker 'da dosya listesi](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Modülü oluşturma

1. Şu komutu çalıştırın:

    ```bash
    bundle install
    ```

1. Şu komutu çalıştırın:

    ```bash
    rake build
    ```

    ![Rake derlemesi](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Uçtan uca testi çalıştırma

1. Şu komutu çalıştırın:

    ```bash
    rake e2e
    ```

1. Birkaç dakika sonra **BAŞARILI** iletisi görünür.

    ![BAŞARILI](media/terraform-vscode-module-generator/ymg-pass.png)

1. Testi tamamlamak ve Docker ortamından çıkmak için `exit` girin.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Cloud Shell bir modül oluşturmak ve test etmek için Yeumman oluşturucuyu kullanın

Bu bölümde, Cloud Shell bir modül oluşturmak ve test etmek için Yeumman Oluşturucu kullanılır. Docker dosyası kullanmak yerine Cloud Shell kullanmak işlemi büyük ölçüde basitleştirir. Cloud Shell kullanarak, aşağıdaki ürünlerin tümü önceden yüklenmiş olarak verilmiştir:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Cloud Shell oturumu başlatma

1. [Azure Portal](https://portal.azure.com/), [Shell.Azure.com](https://shell.azure.com)veya [Azure mobil uygulaması](https://azure.microsoft.com/features/azure-portal/mobile-app/)aracılığıyla Azure Cloud Shell bir oturum başlatın.

1. **Azure Cloud Shell hoş geldiniz** sayfası açılır. **Bash (Linux)** seçeneğini belirleyin.

    ![Azure Cloud Shell'e hoş geldiniz](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Azure depolama hesabı oluşturmadıysanız aşağıdaki ekran açılır. **Depolama oluştur**'u seçin.

    ![Hiçbir depolama takılı değil](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell, seçtiğiniz kabukta başlatılır ve sizin adınıza oluşturulan bulut sunucusuna ait bilgiler gösterilir.

    ![Bulut sürücünüz oluşturuldu](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Tertuform modülünüzü tutacak bir dizin hazırlama

1. Bu noktada, Cloud Shell sizin için ortam değişkeninizdeki GOPATH 'i zaten yapılandırmış olacak. Yolu görmek için `go env`girin.

1. Zaten mevcut değilse $GOPATH dizini oluşturun: `mkdir ~/go`girin.

1. $GOPATH dizininde bir dizin oluşturun. Bu dizin, bu örnekte oluşturulan farklı proje dizinlerini tutmak için kullanılır. 

    ```bash
    mkdir ~/go/src
    ```

1. Yer tutucuyu değiştirerek Tertuform modülünüzü tutacak bir dizin oluşturun. Bu örnekte, `my-module-name` dizin adı kullanılır.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Modül dizininize gidin: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Terrayform modülünüzü oluşturun ve test edin

1. Aşağıdaki komutu çalıştırın ve yönergeleri izleyin. Docker dosyalarını oluşturmak isteyip istemediğiniz sorulduğunda `N`girersiniz.

    ```bash
    yo az-terra-module
    ```

1. Bağımlılıkları yüklemek için aşağıdaki komutu çalıştırın:

    ```bash
    bundle install
    ```

1. Modülünü derlemek için aşağıdaki komutu çalıştırın:

    ```bash
    rake build
    ```

    ![Rake derlemesi](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Metni çalıştırmak için aşağıdaki komutu çalıştırın:

    ```bash
    rake e2e
    ```

    ![Test-geçiş sonuçları](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Terrayform Visual Studio Code uzantısını yükleyip kullanın](/azure/terraform/terraform-vscode-extension).