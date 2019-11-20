---
title: Öğretici-Azure Terrayform Visual Studio Code uzantısını yapılandırma
description: Visual Studio Code'a Azure Terraform uzantısını yüklemeyi ve kullanmayı öğrenin.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 7f2f15c3a45480b8922f34e191fb965d5e83d9fa
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159226"
---
# <a name="tutorial-configure-the-azure-terraform-visual-studio-code-extension"></a>Öğretici: Azure Terrayform Visual Studio Code uzantısını yapılandırma

Azure Terrayform Visual Studio Code uzantısı, düzenleyiciden Terrayform ile çalışmanıza olanak sağlar. Bu uzantıyla birlikte, Terrayform yapılandırmasını yazabilir, test edebilir ve çalıştırabilirsiniz. Uzantı ayrıca kaynak grafik görselleştirmesini destekler.

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
> * Terrayform kullanarak Azure hizmetlerinin sağlanması otomatikleştirin
> * Azure hizmetleri için Terrayform Visual Studio Code uzantısını yükleyip kullanın.
> * Terrayform planlarını yazmak, planlamak ve yürütmek için Visual Studio Code kullanın.

## <a name="prerequisites"></a>Önkoşullar
- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

- **Terraform**: [Terraform’u yükleyin ve yapılandırın](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: Ortamınız için uygun [Visual Studio Code](https://code.visualstudio.com/download) sürümünü yükleyin.

## <a name="prepare-your-dev-environment"></a>Geliştirme ortamınızı hazırlama

### <a name="install-git"></a>Git’i yükleme

Bu makaledeki alıştırmaları tamamlamak için [Git'i yüklemeniz](https://git-scm.com/) gerekir.

### <a name="install-hashicorp-terraform"></a>HashiCorp Terraform'u yükleme

HashiCorp [Install Terraform](https://www.terraform.io/intro/getting-started/install.html) (Terraform'u Yükleme) web sayfasındaki yönergeleri izleyerek şu adımları gerçekleştirin:

- Terraform'u indirme
- Terraform'u yükleme
- Terraform'un gereken şekilde yüklendiğini doğrulama

>[!Tip]
>PATH sistem değişkeninizi ayarlamayla ilgili yönergeleri izlediğinizden emin olun.

### <a name="install-nodejs"></a>Node.js yükleme

Terraform'u Cloud Shell'de kullanabilmek için [Node.js](https://nodejs.org/) 6.0+ sürümünü yüklemeniz gerekir.

>[!NOTE]
>Node.js uygulamasının yüklü olup olmadığını belirlemek için bir terminal penceresi açıp `node -v` yazın.

### <a name="install-graphviz"></a>GraphViz'i yükleme

Terraform'un görselleştirme işlevini kullanmak için [GraphViz'i yüklemeniz gerekir](https://graphviz.org/).

>[!NOTE]
>GraphViz uygulamasının yüklü olup olmadığını belirlemek için bir terminal penceresi açıp `dot -V` yazın.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Azure Terraform Visual Studio Code uzantısını yükleme

1. Visual Studio Code'u başlatın.

1. **Uzantılar**'ı seçin.

    ![Uzantılar düğmesi](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. **Market'te Uzantı Ara** metin kutusunu kullanarak Azure Terraform uzantısını arayın:

    ![Market'te Visual Studio Code uzantılarını arama](media/terraform-vscode-extension/tf-search-extensions.png)

1. **Yükle**’yi seçin.

    >[!NOTE]
    >Azure Terraform uzantısı için **Yükle**'yi seçtiğinizde Visual Studio Code otomatik olarak Azure Account uzantısını yükler. Azure Account, Azure Terraform uzantısı için yüklenmesi gereken bir dosyadır ve Azure aboneliği kimlik doğrulaması işlemleri ile Azure ile ilgili kod uzantıları bu dosya kullanılarak gerçekleştirilir.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Terraform uzantısının Visual Studio Code'a yüklendiğini doğrulama

1. **Uzantılar**'ı seçin.

1. Arama metin kutusuna `@installed` yazın.

    ![Yüklü uzantılar](media/terraform-vscode-extension/tf-installed-extensions.png)

Azure Terraform uzantısı yüklü uzantıların arasında görünür.

![Yüklü Terraform uzantıları](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Artık desteklenen tüm Terraform komutlarını Visual Studio Code içinde Cloud Shell ortamınızda çalıştırabilirsiniz.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Alıştırma 1: Temel Terraform komutlarını kullanma adımları

Bu alıştırmada yeni bir Azure kaynak grubu sağlayan basit bir Terraform yapılandırma dosyası oluşturup yürüteceksiniz.

### <a name="prepare-a-test-plan-file"></a>Test planı dosyası hazırlama

1. Visual Studio Code menü çubuğundan **Dosya > Yeni Dosya**'yı seçin.

1. Tarayıcınızda [Terraform azurerm_resource_group page](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) dosyasına gidin ve **Örnek Kullanım** kod bloğu içindeki kodu kopyalayın:

    ![Örnek Kullanım](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Kopyalanan kodu Visual Studio Code'da oluşturduğunuz yeni dosyaya yapıştırın.

    ![Örnek Kullanım kodunu yapıştırma](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Kaynak grubunun **name** değerini değiştirebilirsiniz ancak bu değerin Azure aboneliğinizde benzersiz olması gerekir.

1. Menü çubuğundan **Dosya > Farklı Kaydet**'i seçin.

1. **Farklı Kaydet** iletişim kutusunda istediğiniz konuma gidin ve **Yeni klasör**'ü seçin. (Yeni klasör için *Yeni klasör* yerine daha açıklayıcı bir ad seçmeniz önerilir.)

    >[!NOTE]
    >Bu örnekte klasöre TERRAFORM-TEST-PLAN adı verilmiştir.

1. Yeni klasörünüzün vurgulandığından (seçildiğinden) emin olun ve ardından **Aç**'ı seçin.

1. **Farklı Kaydet** iletişim kutusunda dosyanın varsayılan adını *main.tf* olarak değiştirin.

    ![main.tf olarak kaydedin](media/terraform-vscode-extension/tf-save-as-main.png)

1. **Kaydet**’i seçin.
1. Menü çubuğundan **Dosya > Klasör Aç**'ı seçin. Yeni oluşturduğunuz klasöre gidin ve seçin.

### <a name="run-terraform-init-command"></a>Terraform *init* komutunu çalıştırma

1. Visual Studio Code'u başlatın.

1. Visual Studio Code menü çubuğundan **Dosya > Klasör Aç...** öğesini ve ardından *main.tf* dosyanızı seçin.

    ![main.tf dosyası](media/terraform-vscode-extension/tf-main-tf.png)

1. Menü çubuğundan **Görünüm > Komut Paleti... > Azure Terraform: Init** öğesini seçin.

1. Onay göründüğünde **Tamam**'ı seçin.

    ![Cloud Shell'i açmak istiyor musunuz?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Yeni bir klasörden Cloud Shell ilk kez başlattığınızda, bir Web uygulaması oluşturmanız istenir. **Aç**'ı seçin.

    ![Cloud Shell'i ilk kez başlatma](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Azure Cloud Shell'e hoş geldiniz sayfası açılır. Bash veya PowerShell'i seçin.

    ![Azure Cloud Shell'e hoş geldiniz](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Bu örnekte Bash (Linux) seçilmiştir.

1. Azure depolama hesabı oluşturmadıysanız aşağıdaki ekran açılır. **Depolama oluştur**'u seçin.

    ![Hiçbir depolama takılı değil](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell, seçtiğiniz kabukta başlatılır ve sizin adınıza oluşturulan bulut sunucusuna ait bilgiler gösterilir.

    ![Bulut sürücünüz oluşturuldu](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Artık Cloud Shell'i kapatabilirsiniz

1. Menü çubuğundan **Görünüm** > **Komut Paleti** > **Azure Terraform: init** öğesini seçin.

    ![Terraform başarıyla başlatıldı](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Planı görselleştirme

Bu öğreticinin önceki bölümlerinde GraphViz'i yüklemiştiniz. Terraform, GraphViz'i kullanarak bir yapılandırmanın veya yürütme planının görsel temsilini oluşturabilir. Azure Terraform Visual Studio Code uzantısı bu özelliği *visualize* komutuyla kullanır.

- Menü çubuğundan **Görünüm > Komut Paleti > Azure Terraform: Visualize** öğesini seçin.

    ![Planı görselleştirme](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Terraform *plan* komutunu çalıştırma

Terraform *plan* komutu, belirli bir değişiklik kümesine ait yürütme planının istediğiniz işlemleri gerçekleştirip gerçekleştirmeyeceğini denetlemek için kullanılır.

>[!NOTE]
>Terraform *plan* komutu, Azure kaynaklarınızda değişiklik yapmaz. Planınızdaki değişiklikleri gerçekleştirmek için Terraform *apply* komutunu kullanmanız gerekir.

- Menü çubuğundan **Görünüm** > **Komut Paleti** > **Azure Terraform: plan** öğesini seçin.

    ![Terraform plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Terraform *apply* komutunu uygulama

Terraform *plan* komutunun sonucundan memnunsanız *apply* komutunu çalıştırabilirsiniz.

1. Menü çubuğundan **Görünüm** > **Komut Paleti** > **Azure Terraform: apply** öğesini seçin.

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. `yes` yazın.

    ![Terraform apply yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Terraform planınızın yürütüldüğünü doğrulama

Yeni Azure kaynak grubunuzun başarıyla oluşturulup oluşturulmadığını görmek için:

1. Azure portalı açın.

1. Soldaki gezinti bölmesinden **Kaynak grupları**'nı seçin.

    ![Yeni kaynağınızı doğrulama](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Yeni kaynak grubunuzun **NAME** sütununda listelenmesi gerekir.

>[!NOTE]
>Azure portal pencerenizi açık bırakabilirsiniz. Bir sonraki adımda kullanıyor olacağız.

### <a name="run-terraform-destroy-command"></a>Terraform *destroy* komutunu çalıştırma

1. Menü çubuğundan **Görünüm** > **Komut Paleti** > **Azure Terraform: destroy** öğesini seçin.

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. *yes* yazın.

    ![Terraform destroy yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Kaynak grubunuzun yok edildiğini doğrulama

Terraform'un yeni kaynak grubunuzu başarıyla yok ettiğini doğrulamak için:

1. Azure portalın **Kaynak grupları** sayfasından **Yenile**'yi seçin.

1. Kaynak grubunuzun listelenmediğini göreceksiniz.

    ![Kaynak grubunuzun yok edildiğini doğrulama](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Alıştırma 2: Terraform *compute* modülü

Bu alıştırmada Terraform *compute* modülünü Visual Studio Code ortamına yüklemeyi öğreneceksiniz.

### <a name="clone-the-terraform-azurerm-compute-module"></a>terraform-azurerm-compute modülünü kopyalama

1. [Bu bağlantıyı](https://github.com/Azure/terraform-azurerm-compute) kullanarak GitHub'daki Terraform Azure Rm Compute modülüne erişin.

1. **Clone or download**'u (Kopyala veya indir) seçin.

    ![Kopyala veya indir](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Bu örnekte klasörümüze *terraform-azurerm-compute* adını vermiştik.

### <a name="open-the-folder-in-visual-studio-code"></a>Visual Studio Code’da klasörü açın

1. Visual Studio Code'u başlatın.

1. Menü çubuğundan **Dosya > Klasör Aç**'ı seçip bir önceki adımda oluşturduğunuz klasöre gidin ve seçin.

    ![terraform-azurerm-compute klasörü](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Terraform'u başlatma

Terraform komutlarını Visual Studio Code'da kullanmaya başlamak için iki Azure sağlayıcısı eklentisini indirmeniz gerekir: random ve azurerm.

1. Visual Studio Code IDE ortamının Terminal bölmesinde `terraform init` yazın.

    ![terraform init komutu](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. `az login` yazın ve, **Enter** tuşuna basın ve ekrandaki yönergeleri izleyin.

### <a name="module-test-lint"></a>Modül testi: *lint*

1. Menü çubuğundan **Görünüm > Komut Paleti > Azure Terraform: Execute Test** öğesini seçin.

1. Test türü seçeneklerinin arasından **lint** öğesini seçin.

    ![Test türünü seçme](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Onay ekranı açıldığında **Tamam**'ı seçin ve ekrandaki yönergeleri izleyin.

    ![Cloud Shell'i açmak istiyor musunuz?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>**lint** veya **end to end** testini yürüttüğünüzde Azure bir kapsayıcı hizmetini kullanarak gerçek testi gerçekleştirecek bir test makinesi sağlar. Bu nedenle test sonuçlarınızın döndürülmesi genellikle birkaç dakika sürebilir.

Biraz bekledikten sonra Terminal bölmesinde şu örneğe benzer bir giriş görürsünüz:

![Lint test sonuçları](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="test-the-module"></a>Modülü test etme

1. Menü çubuğundan **Görünüm > Komut Paleti > Azure Terraform: Execute Test** öğesini seçin.

1. Test türü seçeneklerinin arasından **end to end** öğesini seçin.

    ![Test türünü seçme](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Onay ekranı açıldığında **Tamam**'ı seçin ve ekrandaki yönergeleri izleyin.

    ![Cloud Shell'i açmak istiyor musunuz?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>**lint** veya **end to end** testini yürüttüğünüzde Azure bir kapsayıcı hizmetini kullanarak gerçek testi gerçekleştirecek bir test makinesi sağlar. Bu nedenle test sonuçlarınızın döndürülmesi genellikle birkaç dakika sürebilir.

Biraz bekledikten sonra Terminal bölmesinde şu örneğe benzer bir giriş görürsünüz:

![Test sonuçları](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure'da (ve desteklenen diğer sağlayıcılarda) kullanılabilecek Terraform düğümlerinin listesi](https://registry.terraform.io/)