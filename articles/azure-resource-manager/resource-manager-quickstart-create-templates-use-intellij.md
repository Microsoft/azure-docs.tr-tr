---
title: Şablon dağıtma-IntelliJ fıkır
description: IntelliJ FIKRINI kullanarak ilk Azure Resource Manager şablonunuzu oluşturmayı ve bunu dağıtmayı öğrenin.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.openlocfilehash: 62ea27236eec1a63b2a07dcb532f91cea7ead70b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149472"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Hızlı başlangıç: IntelliJ FIKRINI kullanarak Azure Resource Manager şablonları oluşturun ve dağıtın

IntelliJ FIKRINI kullanarak Azure 'a bir Kaynak Yöneticisi şablonu dağıtmayı ve doğrudan IDE 'den şablonu düzenlemeyle güncelleştirme işlemini öğrenin. Resource Manager şablonları, çözümünüz için dağıtmanız gereken kaynakları tanımlayan JSON dosyalarıdır. Azure çözümlerinizi dağıtma ve yönetmeyle ilgili kavramları anlamak için bkz. [Azure Resource Manager’a genel bakış](resource-group-overview.md).

![Kaynak Yöneticisi şablonu hızlı başlangıç portalı diyagramı](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Öğreticiyi tamamladıktan sonra bir Azure depolama hesabı dağıtırsınız. Aynı işlem, diğer Azure kaynaklarını dağıtmak için de kullanılabilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [IntelliJ fikir](https://www.jetbrains.com/idea/download/) Ultimate sürümü veya Community Edition yüklendi
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) yüklendi, daha fazla bilgi Için [IntelliJ 'ın eklenti yönetim kılavuzunu](https://www.jetbrains.com/help/idea/managing-plugins.html) denetleyin
* Azure Toolkit for IntelliJ için Azure hesabınızda [oturum](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) açmanız

## <a name="deploy-a-quickstart-template"></a>Hızlı başlangıç şablonu dağıtma

Sıfırdan şablon oluşturmak yerine, [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/resources/templates/)’ndan bir şablon açarsınız. Azure hızlı başlangıç şablonları Kaynak Yöneticisi şablonlar için bir depodur. Bu hızlı başlangıçta kullanılan şablon [Standart depolama hesabı oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) olarak adlandırılır. Bir Azure depolama hesabı kaynağını tanımlar.

1. Sağ tıklayın ve [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) ve [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) yerel bilgisayarınıza kaydedin.

1. Azure Toolkit 'niz düzgün yüklenip oturum açmışsa, IntelliJ fıkrın kenar çubuğunda Azure Explorer ' ı görmeniz gerekir. **Kaynak yönetimine** sağ tıklayın ve **dağıtım oluştur**' u seçin.

    ![Kaynak Yöneticisi şablonu dağıtım oluşturmak için sağ tıklayın](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. **Dağıtım adınızı**, **aboneliğinizi**, **kaynak grubunuzu**ve **bölgenizi**yapılandırma. Burada, şablonu yeni bir kaynak grubu `testRG`dağıttık. Ardından, indirdiğiniz `azuredeploy.parameters.json` olarak **kaynak şablonu** `azuredeploy.json` ve **kaynak parametreleri** olarak yolunu seçin.

    ![Kaynak Yöneticisi şablonu dağıtım oluşturmak için dosyaları seçin](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Tamam ' a tıkladıktan sonra dağıtım başlatılır. Dağıtım tamamlanana kadar, en altta IntelliJ fıkrın **durum çubuğundan** ilerlemeyi bulabilirsiniz.

    ![Kaynak Yöneticisi şablonu dağıtım durumu](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Mevcut bir dağıtıma gözatın

1. Dağıtım yapıldıktan sonra, yeni kaynak grubu `testRG` ve yeni bir dağıtım oluşturulur. Dağıtıma sağ tıklayın ve olası eylemlerin bir listesini görebilirsiniz. Şimdi **özellikleri göster**' i seçin.

    ![Kaynak Yöneticisi şablonu dağıtıma gözatmayı görüntüle](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Bir sekme görünümü, dağıtım durumu ve şablon yapısı gibi bazı yararlı özellikleri göstermek için açılacak.

    ![Kaynak Yöneticisi şablonu dağıtım özelliklerini göster](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Mevcut bir dağıtımı düzenleyin ve güncelleştirin

1. Sağ tıklama menüsünde **dağıtımı Düzenle** ' yi veya daha önce özellikleri göster görünümünü seçin. Azure üzerinde dağıtım için şablon ve parametre dosyalarını gösteren başka bir sekme görünümü açılır. Bu dosyaları yerel olarak kaydetmek için, **şablon dosyasını dışarı aktar** veya **parametre dosyalarını dışarı aktar**' a tıklayabilirsiniz.

    ![Kaynak Yöneticisi şablonu dağıtım düzenleme](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Bu sayfadaki iki dosyayı düzenleyebilir ve değişiklikleri Azure 'a dağıtabilirsiniz. Burada, `Standard_LRS` parametre dosyalarındaki **Storageaccounttype** değerini `Standard_GRS`olarak değiştirirsiniz. Ardından, alt kısımdaki **güncelleştirme dağıtımı** ' na tıklayın ve güncelleştirmeyi onaylayın.

    ![Kaynak Yöneticisi şablonu dağıtım düzenleme](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Güncelleştirme dağıtımı tamamlandıktan sonra, portalda oluşturulan depolama hesabının `Standard_GRS`olarak değiştirildiğini doğrulayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

1. Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin. Bunu Azure portal veya Azure CLı 'den yapabilirsiniz. Azure Explorer 'ın IntelliJ FIKRINDEN, oluşturduğunuz **kaynak grubunuza** sağ tıklayıp Sil ' i seçin.

    ![Azure Explorer 'da IntelliJ FIKRINDEN kaynak grubunu silme](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Bir dağıtımı silme, dağıtım tarafından oluşturulan kaynakları silmeyecektir. Artık gerekmiyorsa, ilgili kaynak grubunu veya belirli kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıç 'nin ana odağı, Azure hızlı başlangıç şablonlarından mevcut bir şablonu dağıtmak için IntelliJ FIKRINI kullanmaktır. Ayrıca, Azure 'da var olan bir dağıtımı görüntülemeyi ve güncelleştirmeyi de öğrendiniz. Azure Hızlı Başlangıç şablonlarındaki şablonlar size ihtiyacınız olan her şeyi sağlamayabilir. Şablon geliştirme hakkında daha fazla bilgi edinmek için bkz. Yeni Başlangıç Öğreticisi serisi:

> [!div class="nextstepaction"]
> [Başlangıç öğreticileri](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Azure Geliştirme Merkezi 'nde Java 'Yı ziyaret edin](https://docs.microsoft.com/azure/java)
