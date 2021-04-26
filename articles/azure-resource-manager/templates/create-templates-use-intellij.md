---
title: Şablon dağıtma-IntelliJ fıkır
description: IntelliJ FIKRINI kullanarak ilk Azure Resource Manager şablonunuzu (ARM şablonu) oluşturmayı ve nasıl dağıtılacağını öğrenin.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: f0e007fe8930b30cee1c95159d7e964e3792449f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905970"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>IntelliJ FIKRINI kullanarak ARM şablonları oluşturma ve dağıtma

IntelliJ FIKRINI kullanarak Azure 'a bir Azure Resource Manager şablonu (ARM şablonu) dağıtmayı ve şablonu doğrudan IDE 'den güncelleştirme ve güncelleştirme işlemini öğrenin. ARM şablonları, çözümünüz için dağıtmanız gereken kaynakları tanımlayan JSON dosyalarıdır. Azure çözümlerinizi dağıtma ve yönetme ile ilgili kavramları anlamak için bkz. [şablon dağıtımına genel bakış](overview.md).

![Kaynak Yöneticisi Şablon portalı diyagramı](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Öğreticiyi tamamladıktan sonra bir Azure depolama hesabı dağıtırsınız. Aynı işlem, diğer Azure kaynaklarını dağıtmak için de kullanılabilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* Yüklü [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition veya Community Edition
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) yüklü; daha fazla bilgi için [IntelliJ eklentiler yönetimi kılavuzuna](https://www.jetbrains.com/help/idea/managing-plugins.html) bakın
* Azure Toolkit for IntelliJ için Azure hesabınızda [oturum açmış](/azure/developer/java/toolkit-for-intellij/sign-in-instructions) olmalısınız

## <a name="deploy-a-quickstart-template"></a>Hızlı başlangıç şablonu dağıtma

Sıfırdan şablon oluşturmak yerine, [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/resources/templates/)’ndan bir şablon açarsınız. Azure hızlı başlangıç şablonları, ARM şablonları için bir depodur. Bu makalede kullanılan şablona [Standart depolama hesabı oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/)adı verilir. Bir Azure depolama hesabı kaynağını tanımlar.

1. Sağ tıklayın ve [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) yerel bilgisayarınıza ve kaydedin [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) .

1. Azure Toolkit 'niz düzgün yüklenip oturum açmışsa, IntelliJ fıkrın kenar çubuğunda Azure Explorer ' ı görmeniz gerekir. **Kaynak yönetimine** sağ tıklayın ve **dağıtım oluştur**' u seçin.

    ![Kaynak Yöneticisi şablonu dağıtım oluşturmak için sağ tıklayın](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. **Dağıtım adınızı**, **aboneliğinizi**, **kaynak grubunuzu** ve **bölgenizi** yapılandırma. Burada, şablonu yeni bir kaynak grubuna dağıttık `testRG` . Ardından, indirdiğiniz **kaynak şablonu** `azuredeploy.json` ve **kaynak parametreleri** için yol ' ı seçin `azuredeploy.parameters.json` .

    ![Kaynak Yöneticisi şablonu dağıtım oluşturmak için dosyaları seçin](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Tamam ' a tıkladıktan sonra dağıtım başlatılır. Dağıtım tamamlanana kadar, en altta IntelliJ fıkrın **durum çubuğundan** ilerlemeyi bulabilirsiniz.

    ![Kaynak Yöneticisi şablonu dağıtım durumu](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Mevcut bir dağıtıma gözatın

1. Dağıtım tamamlandıktan sonra, yeni kaynak grubunu `testRG` ve yeni bir dağıtım oluşturulduğunu görebilirsiniz. Dağıtıma sağ tıklayın ve olası eylemlerin bir listesini görebilirsiniz. Şimdi **özellikleri göster**' i seçin.

    ![Kaynak Yöneticisi şablonu dağıtıma gözatmayı görüntüle](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Bir sekme görünümü, dağıtım durumu ve şablon yapısı gibi bazı yararlı özellikleri göstermek için açılacak.

    ![Kaynak Yöneticisi şablonu dağıtım özelliklerini göster](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Mevcut bir dağıtımı düzenleyin ve güncelleştirin

1. Sağ tıklama menüsünde **dağıtımı Düzenle** ' yi veya daha önce özellikleri göster görünümünü seçin. Azure üzerinde dağıtım için şablon ve parametre dosyalarını gösteren başka bir sekme görünümü açılır. Bu dosyaları yerel olarak kaydetmek için, **şablon dosyasını dışarı aktar**  veya **parametre dosyalarını dışarı aktar**' a tıklayabilirsiniz.

    ![Kaynak Yöneticisi şablonu dağıtım düzenleme](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Bu sayfadaki iki dosyayı düzenleyebilir ve değişiklikleri Azure 'a dağıtabilirsiniz. Burada, parametresi dosyalarındaki **Storageaccounttype** değerini ' dan ' a değiştirirsiniz `Standard_LRS` `Standard_GRS` . Ardından, alt kısımdaki **güncelleştirme dağıtımı** ' na tıklayın ve güncelleştirmeyi onaylayın.

    ![Ekran görüntüsü, güncelleştirme dağıtımı istemiyle gösterilen Kaynak Yöneticisi şablonunu gösterir.](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Güncelleştirme dağıtımı tamamlandıktan sonra, portalda oluşturulan depolama hesabının değiştiğini doğrulayabilirsiniz `Standard_GRS` .

## <a name="clean-up-resources"></a>Kaynakları temizleme

1. Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin. Bunu Azure portal veya Azure CLı 'den yapabilirsiniz. Azure Explorer 'ın IntelliJ FIKRINDEN, oluşturduğunuz **kaynak grubunuza** sağ tıklayıp Sil ' i seçin.

    ![Azure Explorer 'da IntelliJ FIKRINDEN kaynak grubunu silme](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Bir dağıtımı silme, dağıtım tarafından oluşturulan kaynakları silmeyecektir. Artık gerekmiyorsa, ilgili kaynak grubunu veya belirli kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalenin ana odağı, Azure hızlı başlangıç şablonlarından mevcut bir şablonu dağıtmak için IntelliJ FIKRINI kullanmaktır. Ayrıca, Azure 'da var olan bir dağıtımı görüntülemeyi ve güncelleştirmeyi de öğrendiniz. Azure Hızlı Başlangıç şablonlarındaki şablonlar size ihtiyacınız olan her şeyi sağlamayabilir. Şablon geliştirme hakkında daha fazla bilgi edinmek için bkz. Yeni Başlangıç Öğreticisi serisi:

> [!div class="nextstepaction"]
> [Başlangıç öğretileri](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Azure Geliştirme Merkezi 'nde Java 'Yı ziyaret edin](/azure/java)
