---
title: Şablon dağıtma-Azure portal
description: Azure portal kullanarak ilk Azure Resource Manager şablonunuzu (ARM şablonu) oluşturmayı ve nasıl dağıtılacağını öğrenin.
author: mumian
ms.date: 03/26/2021
ms.topic: quickstart
ms.author: jgao
ms.custom: contperfq1
ms.openlocfilehash: 279dde9c4cd7b19086d29d3ba31a32c60a8cdf0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612934"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak ARM şablonları oluşturma ve dağıtma

Azure portal kullanarak Azure Resource Manager şablonu (ARM şablonu) oluşturmayı ve şablonu nasıl düzenleyip dağıtacağınızı öğrenin. ARM şablonları, çözümünüz için dağıtmanız gereken kaynakları tanımlayan JSON dosyalarıdır. Azure çözümlerinizi dağıtma ve yönetme ile ilgili kavramları anlamak için bkz. [şablon dağıtımına genel bakış](overview.md).

Öğreticiyi tamamladıktan sonra bir Azure depolama hesabı dağıtırsınız. Aynı işlem, diğer Azure kaynaklarını dağıtmak için de kullanılabilir.

![Kaynak Yöneticisi şablonu hızlı başlangıç portalı diyagramı](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="generate-a-template-using-the-portal"></a>Portalı kullanarak şablon oluşturma

Sıfırdan bir ARM şablonu oluşturmak, özellikle de Azure dağıtımı için yeni bir işlemdir ve JSON biçimi hakkında bilgi sahibi değilseniz kolay bir görev değildir. Azure portal kullanarak bir kaynağı (örneğin, bir Azure Storage hesabı) yapılandırabilirsiniz. Kaynağı dağıtmadan önce, yapılandırmanızı bir şablona dışarı aktarabilirsiniz. Şablonu kaydedebilir ve daha sonra yeniden kullanabilirsiniz.

Birçok deneyimli şablon geliştiricisi, bu yöntemi, alışık oldukları Azure kaynaklarını dağıtmaya çalıştıklarında şablonlar oluşturmak için kullanır. Portalı kullanarak şablonları dışarı aktarma hakkında daha fazla bilgi için bkz. [kaynak gruplarını şablonlara dışarı aktarma](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Çalışma şablonu bulmanın diğer yolu [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/)de bulunur.

1. Bir Web tarayıcısında [Azure Portal](https://portal.azure.com) gidin ve oturum açın.
1. Azure portalı menüsünden **Kaynak oluştur**'u seçin.

    ![Azure portal menüsünden kaynak oluştur ' u seçin](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Arama kutusuna **depolama hesabı** yazın ve **[ENTER]** tuşuna basın.
1. **Oluştur**' un yanındaki aşağı oku seçin ve ardından **depolama hesabı**' nı seçin.

    ![Azure depolama hesabı oluşturma](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)

1. Aşağıdaki bilgileri girin:

    |Name|Değer|
    |----|----|
    |**Kaynak grubu**|**Yeni oluştur**' u seçin ve tercih ettiğiniz bir kaynak grubu adı belirtin. Ekran görüntüsünde kaynak grubu adı *mystorage1016rg* olarak belirtilmiştir. Kaynak grubu, Azure kaynakları için bir kapsayıcıdır. Kaynak grubu, Azure kaynaklarını yönetmeyi kolaylaştırır. |
    |**Ad**|Depolama hesabınıza benzersiz bir ad verin. Depolama hesabı adı tüm Azure genelinde benzersiz olmalıdır ve yalnızca küçük harf ve rakam içermelidir. Ad 3 ile 24 karakter arasında olmalıdır. "Depolama hesabı adı ' mystorage1016 ' zaten alınmış" olduğunu söyleyen bir hata iletisi alırsanız, **&lt; adınızı &lt;>>**, örneğin **johndolestorage1016**. Daha fazla bilgi için bkz. [adlandırma kuralları ve kısıtlamaları](/azure/architecture/best-practices/resource-naming).|

    Diğer özellikler için varsayılan değerleri kullanabilirsiniz.

    ![Azure portalı kullanarak Azure depolama hesabı yapılandırması oluşturma](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Dışarı aktarılan şablonların bazılarını kullanabilmeniz için yapmanız gereken düzenlemeler vardır.

1. Ekranın alt tarafından **Gözden geçir + oluştur**'u seçin. Sonraki adımda **Oluştur** ' u seçmeyin.
1. Ekranın alt tarafından **Otomasyon için bir şablon indir**'i seçin. Portalda oluşturulan şablon gösterilir:

    ![Portaldan şablon oluşturma](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Şablon ana bölmede gösterilir. Bu, en üst düzey altı öğe olan (,,, `schema` `contentVersion` `parameters` `variables` , `resources` , ve `output` ) bir JSON dosyasıdır. Daha fazla bilgi için bkz [. ARM şablonlarının yapısını ve sözdizimini anlama](./template-syntax.md)

    Tanımlanmış dokuz parametre vardır. Bunlardan biri **storageAccountName** olarak adlandırılmıştır. Önceki ekran görüntüsünde vurgulanan ikinci bölüm, şablondaki bu parametreye nasıl başvurulacağını gösterir. Sonraki bölümde şablonu düzenleyerek depolama hesabı için oluşturulan bir adı kullanacaksınız.

    Şablonda bir Azure kaynağı tanımlanmıştır. Türü `Microsoft.Storage/storageAccounts` . Kaynağın nasıl tanımlandığını ve tanım yapısını göz atın.
1. Ekranın üstünden **İndir** ' i seçin.
1. İndirilen ZIP dosyasını açın ve bilgisayarınıza **template.js** kaydedin. Sonraki bölümde bir şablon dağıtım aracı kullanarak şablonu düzenleyeceksiniz.
1. Parametre için sağladığınız değerleri görmek üzere **Parametre** sekmesini seçin. Bu değerleri not alın. Bir sonraki bölümde şablonu dağıtırken ihtiyacınız olacak.

    ![Girdiğiniz değerleri gösteren parametre sekmesini vurgulayan ekran görüntüsü.](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Hem şablon dosyası hem de parametreler dosyasını kullanarak bir Azure depolama hesabı olan bu öğreticide bir kaynak oluşturabilirsiniz.

## <a name="edit-and-deploy-the-template"></a>Şablonu düzenleme ve dağıtma

Basit şablon düzenleme işlemleri için Azure portalı kullanabilirsiniz. Bu hızlı başlangıçta *Şablon Dağıtımı* adlı bir portal aracını kullanacaksınız. Bu öğreticide *şablon dağıtımı* , tek bir arabirim kullanarak tüm öğreticiyi tamamlayabilmeniz için kullanılır-Azure Portal. Daha karmaşık bir şablonu düzenlemek için, daha zengin düzenleme işlevleri sağlayan [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)kullanmayı düşünün.

> [!IMPORTANT]
> Şablon dağıtımı, basit şablonları test etmek için bir arabirim sağlar. Bu özelliğin üretimde kullanılması önerilmez. Bunun yerine, şablonlarınızı bir Azure depolama hesabında veya GitHub gibi bir kaynak kod deposunda saklayın.

Azure'daki her Azure hizmetinin adının benzersiz olması gerekir. Zaten var olan bir depolama hesabı adı girdiyseniz dağıtım başarısız olabilir. Bu sorundan kaçınmak için şablonu, `uniquestring()` benzersiz bir depolama hesabı adı oluşturmak üzere bir şablon işlevi çağrısı kullanacak şekilde değiştirirsiniz.

1. Azure portal menüsünde, ara kutusuna **Dağıt** yazın ve ardından **özel şablon dağıt**' ı seçin.

    ![Azure Resource Manager şablon kitaplığı](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)

1. **Düzenleyicide kendi şablonunuzu oluşturun öğesini** seçin.
1. **Dosya yükle**'yi seçin ve ardından yönergeleri izleyerek önceki bölümde indirdiğiniz template.json dosyasını yükleyin.
1. Şablonda aşağıdaki üç değişikliği yapın:

    ![Azure Resource Manager şablonları](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - **StorageAccountName** parametresini önceki ekran görüntüsünde gösterildiği gibi kaldırın.
   - Önceki ekran görüntüsünde gösterildiği gibi **storageAccountName** adlı bir değişken ekleyin:

      ```json
      "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
      ```

      İki şablon işlevi burada kullanılır: `concat()` ve `uniqueString()` .
   - **Microsoft.Storage/storageAccounts** kaynağının name öğesini parametre yerine yeni tanımlanan değişkeni kullanacak şekilde güncelleştirin:

      ```json
      "name": "[variables('storageAccountName')]",
      ```

      Şablonun son halinin şu şekilde olması gerekir:

      ```json
      {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "location": {
            "type": "string"
          },
          "accountType": {
            "type": "string"
          },
          "kind": {
            "type": "string"
          },
          "accessTier": {
            "type": "string"
          },
          "minimumTlsVersion": {
            "type": "string"
          },
          "supportsHttpsTrafficOnly": {
            "type": "bool"
          },
          "allowBlobPublicAccess": {
            "type": "bool"
          },
          "allowSharedKeyAccess": {
            "type": "bool"
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
        },
        "resources": [
          {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[parameters('location')]",
            "properties": {
              "accessTier": "[parameters('accessTier')]",
              "minimumTlsVersion": "[parameters('minimumTlsVersion')]",
              "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
              "allowBlobPublicAccess": "[parameters('allowBlobPublicAccess')]",
              "allowSharedKeyAccess": "[parameters('allowSharedKeyAccess')]"
            },
            "dependsOn": [],
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "[parameters('kind')]",
            "tags": {}
          }
        ],
        "outputs": {}
      }
      ```

1. **Kaydet**’i seçin.
1. Aşağıdaki değerleri girin:

    |Name|Değer|
    |----|----|
    |**Kaynak grubu**|Son bölümde oluşturduğunuz kaynak grubu adını seçin. |
    |**Bölge**|Kaynak grubu için bir konum seçin. Örneğin **Orta ABD**. |
    |**Konum**|Depolama hesabı için bir konum seçin. Örneğin **Orta ABD**. |
    |**Hesap Türü**|Bu hızlı başlangıç için **Standard_LRS** girin. |
    |**Tip**|Bu hızlı başlangıç için **StorageV2** girin. |
    |**Erişim katmanı**|Bu hızlı başlangıç için **sık** erişimli girin. |
    |**En düşük TLS sürümü**|**TLS1_0** girin. |
    |**Yalnızca HTTPS trafiğini destekler**| Bu hızlı başlangıç için **true** değerini seçin. |
    |**Blob genel erişimine izin ver**| Bu hızlı başlangıç için **false** değerini seçin. |
    |**Paylaşılan anahtar erişimine izin ver**| Bu hızlı başlangıç için **true** değerini seçin. |

1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.
1. Dağıtım durumunu görmek için ekranın en üstündeki zil simgesini (bildirimler) seçin. **Dağıtım devam ediyor** görürsünüz. Dağıtım tamamlanana kadar bekleyin.

    ![Azure Resource Manager şablonlarını dağıtma bildirimi](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. Bildirim bölmesinden **Kaynak grubuna git**'i seçin. Şuna benzer bir ekran görmeniz gerekir:

    ![Azure Resource Manager şablonlarını dağıtma kaynak grubu](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Dağıtım durumunun başarılı olduğunu ve kaynak grubunda yalnızca bir depolama hesabı olduğunu görebilirsiniz. Depolama hesabı adı, şablon tarafından oluşturulan benzersiz bir dizedir. Azure depolama hesaplarını kullanma hakkında daha fazla bilgi edinmek için bkz. [Hızlı başlangıç: Azure portalı kullanarak blobları yükleme, indirme ve listeleme](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
1. **Ada göre filtrele** alanına kaynak grubu adını girin.
1. Kaynak grubu adını seçin.  Depolama hesabı kaynak grubunda bulunmalıdır.
1. Üstteki menüden **Kaynak grubunu sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portaldan şablon oluşturmayı ve portalı kullanarak şablonu dağıtmayı öğrendiniz. Bu Hızlı Başlangıçta kullanılan şablon, tek bir Azure kaynağına sahip basit bir şablondur. Şablon karmaşık olduğunda geliştirme için Visual Studio Code veya Visual Studio uygulamasını kullanmak daha kolaydır. Şablon geliştirme hakkında daha fazla bilgi edinmek için bkz. Yeni Başlangıç Öğreticisi serisi:

> [!div class="nextstepaction"]
> [Başlangıç öğretileri](./template-tutorial-create-first-template.md)
