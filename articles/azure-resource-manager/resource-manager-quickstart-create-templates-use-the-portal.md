---
title: Şablon dağıtma-Azure portal
description: Azure portalı kullanarak ilk Azure Resource Manager şablonunuzu oluşturmayı ve dağıtmayı öğrenin.
author: mumian
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 9a747dbdda4b822c70edaa5a08c30db80328c321
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150546"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Hızlı başlangıç: Azure portalı kullanarak Azure Resource Manager şablonu oluşturma ve dağıtma

Azure portal kullanarak bir Kaynak Yöneticisi şablonu oluşturmayı ve portaldan şablonu düzenlemenin ve dağıtmanın işlemini öğrenin. Resource Manager şablonları, çözümünüz için dağıtmanız gereken kaynakları tanımlayan JSON dosyalarıdır. Azure çözümlerinizi dağıtma ve yönetmeyle ilgili kavramları anlamak için bkz. [Azure Resource Manager’a genel bakış](resource-group-overview.md).

![Kaynak Yöneticisi şablonu hızlı başlangıç portalı diyagramı](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Öğreticiyi tamamladıktan sonra bir Azure depolama hesabı dağıtırsınız. Aynı işlem, diğer Azure kaynaklarını dağıtmak için de kullanılabilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="generate-a-template-using-the-portal"></a>Portalı kullanarak şablon oluşturma

Sıfırdan bir Kaynak Yöneticisi şablonu oluşturmak, özellikle de Azure dağıtımı için yeni bir işlemdir ve JSON biçimi hakkında bilgi sahibi değilseniz kolay bir görev değildir. Azure portal kullanarak bir kaynağı (örneğin, bir Azure Storage hesabı) yapılandırabilirsiniz. Kaynağı dağıtmadan önce, yapılandırmanızı bir Kaynak Yöneticisi şablonuna dışarı aktarabilirsiniz. Şablonu kaydedebilir ve daha sonra yeniden kullanabilirsiniz.

Birçok deneyimli şablon geliştiricisi, bu yöntemi, alışık oldukları Azure kaynaklarını dağıtmaya çalıştıklarında şablonlar oluşturmak için kullanır. Portalı kullanarak şablonları dışarı aktarma hakkında daha fazla bilgi için bkz. [kaynak gruplarını şablonlara dışarı aktarma](./manage-resource-groups-portal.md#export-resource-groups-to-templates). Çalışma şablonu bulmanın diğer yolu [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/)de bulunur.

1. Bir Web tarayıcısında [Azure Portal](https://portal.azure.com) gidin ve oturum açın.
2. Azure portal menüsünde **kaynak oluştur**' u seçin.

    ![Azure portal menüsünden kaynak oluştur ' u seçin](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

3. **Depolama** > **depolama hesabı**' nı seçin.

    ![Azure Storage hesabı oluşturma](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Aşağıdaki bilgileri girin:

    |Ad|Değer|
    |----|----|
    |**Kaynak grubu**|**Yeni oluştur**' u seçin ve tercih ettiğiniz bir kaynak grubu adı belirtin. Ekran görüntüsünde kaynak grubu adı *mystorage1016rg* olarak belirtilmiştir. Kaynak grubu, Azure kaynakları için bir kapsayıcıdır. Kaynak grubu, Azure kaynaklarını yönetmeyi kolaylaştırır. |
    |**Adı**|Depolama hesabınıza benzersiz bir ad verin. Depolama hesabı adı tüm Azure genelinde benzersiz olmalıdır ve yalnızca küçük harf ve rakam içermelidir. Ad 3 ile 24 karakter arasında olmalıdır. "Depolama hesabı adı ' mystorage1016 ' zaten alınmış" olarak bir hata iletisi alırsanız, **adınızı&lt;> depolama alanını&lt;>** , örneğin **johndolestorage1016**) kullanmayı deneyin. Daha fazla bilgi için bkz. [adlandırma kuralları ve kısıtlamaları](/azure/architecture/best-practices/resource-naming).|

    Diğer özellikler için varsayılan değerleri kullanabilirsiniz.

    ![Azure portalı kullanarak Azure depolama hesabı yapılandırması oluşturma](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Dışarı aktarılan şablonların bazılarını kullanabilmeniz için yapmanız gereken düzenlemeler vardır.

5. Ekranın alt tarafından **Gözden geçir + oluştur**'u seçin. Sonraki adımda **Oluştur** ' u seçmeyin.
6. Ekranın alt tarafından **Otomasyon için bir şablon indir**'i seçin. Portalda oluşturulan şablon gösterilir:

    ![Portaldan şablon oluşturma](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Şablon ana bölmede gösterilir. Altı üst düzey öğe içeren bir JSON dosyasıdır; `schema`, `contentVersion`, `parameters`, `variables`, `resources`ve `output`. Daha fazla bilgi için bkz. [Azure Resource Manager şablonlarının yapısını ve söz dizimini anlama](./resource-group-authoring-templates.md)

    Tanımlı altı parametre bulunur. Bunlardan biri **storageAccountName** olarak adlandırılmıştır. Önceki ekran görüntüsünde vurgulanan ikinci bölüm, şablondaki bu parametreye nasıl başvurulacağını gösterir. Sonraki bölümde şablonu düzenleyerek depolama hesabı için oluşturulan bir adı kullanacaksınız.

    Şablonda bir Azure kaynağı tanımlanmıştır. Tür `Microsoft.Storage/storageAccounts`. Kaynağın nasıl tanımlandığını ve tanım yapısını göz atın.
7. Ekranın üstünden **İndir** ' i seçin.
8. İndirilen ZIP dosyasını açın ve ardından **Template. JSON** ' u bilgisayarınıza kaydedin. Sonraki bölümde bir şablon dağıtım aracı kullanarak şablonu düzenleyeceksiniz.
9. Parametre için sağladığınız değerleri görmek üzere **Parametre** sekmesini seçin. Bu değerleri not alın. Bir sonraki bölümde şablonu dağıtırken ihtiyacınız olacak.

    ![Portaldan şablon oluşturma](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Hem şablon dosyası hem de parametreler dosyasını kullanarak bir Azure depolama hesabı olan bu öğreticide bir kaynak oluşturabilirsiniz.

## <a name="edit-and-deploy-the-template"></a>Şablonu düzenleme ve dağıtma

Basit şablon düzenleme işlemleri için Azure portalı kullanabilirsiniz. Bu hızlı başlangıçta *Şablon Dağıtımı* adlı bir portal aracını kullanacaksınız. Bu öğreticide *şablon dağıtımı* , tek bir arabirim kullanarak tüm öğreticiyi tamamlayabilmeniz için kullanılır-Azure Portal. Daha karmaşık bir şablonu düzenlemek için, daha zengin düzenleme işlevleri sağlayan [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)kullanmayı düşünün.

> [!IMPORTANT]
> Şablon dağıtımı, basit şablonları test etmek için bir arabirim sağlar. Bu özelliğin üretimde kullanılması önerilmez. Bunun yerine, şablonlarınızı bir Azure depolama hesabında veya GitHub gibi bir kaynak kod deposunda saklayın.

Azure'daki her Azure hizmetinin adının benzersiz olması gerekir. Zaten var olan bir depolama hesabı adı girdiyseniz dağıtım başarısız olabilir. Bu sorundan kaçınmak için şablonu, bir şablon işlevi çağrısı `uniquestring()` kullanılacak şekilde değiştirerek benzersiz bir depolama hesabı adı oluşturur.

1. Azure portal menüsünden veya **giriş** sayfasından **kaynak oluştur**' u seçin.
2. **Market içinde ara** alanına **şablon dağıtımı** yazın ve **ENTER** tuşuna basın.
3. **Şablon dağıtımı**'nı seçin.

    ![Azure Resource Manager şablon kitaplığı](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. **Oluştur**'u seçin.
5. **Düzenleyicide kendi şablonunuzu oluşturun**'u seçin.
6. **Dosya yükle**'yi seçin ve ardından yönergeleri izleyerek önceki bölümde indirdiğiniz template.json dosyasını yükleyin.
7. Şablonda aşağıdaki üç değişikliği yapın:

    ![Azure Resource Manager şablonları](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - **StorageAccountName** parametresini önceki ekran görüntüsünde gösterildiği gibi kaldırın.
   - Önceki ekran görüntüsünde gösterildiği gibi **storageAccountName** adlı bir değişken ekleyin:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       İki şablon işlevi burada kullanılır: `concat()` ve `uniqueString()`.
   - **Microsoft.Storage/storageAccounts** kaynağının name öğesini parametre yerine yeni tanımlanan değişkeni kullanacak şekilde güncelleştirin:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     Şablonun son halinin şu şekilde olması gerekir:

     ```json
     {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
           "supportsHttpsTrafficOnly": {
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
               "apiVersion": "2018-07-01",
               "location": "[parameters('location')]",
               "properties": {
                   "accessTier": "[parameters('accessTier')]",
                   "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
               },
               "dependsOn": [],
               "sku": {
                   "name": "[parameters('accountType')]"
               },
               "kind": "[parameters('kind')]"
           }
       ],
       "outputs": {}
     }
     ```
8. **Kaydet**’i seçin.
9. Aşağıdaki değerleri girin:

    |Ad|Değer|
    |----|----|
    |**Kaynak grubu**|Son bölümde oluşturduğunuz kaynak grubu adını seçin. |
    |**Konum**|Depolama hesabı için bir konum seçin. Örneğin, **Orta ABD**. |
    |**Hesap türü**|Bu hızlı başlangıç için **Standard_LRS** girin. |
    |**Denetlenmesi**|Bu hızlı başlangıç için **StorageV2** girin. |
    |**Erişim katmanı**|Bu hızlı başlangıç için **sık** erişimli girin. |
    |**Yalnızca HTTPS trafiği etkin**| Bu hızlı başlangıç için **true** değerini seçin. |
    |**Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**|seçin|

    Örnek bir dağıtımın ekran görüntüsü:

    ![Azure Resource Manager şablonlarını dağıtma](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. **Satın al**'ı seçin.
11. Dağıtım durumunu görmek için ekranın en üstündeki zil simgesini (bildirimler) seçin. **Dağıtım devam ediyor**görürsünüz. Dağıtım tamamlanana kadar bekleyin.

    ![Azure Resource Manager şablonlarını dağıtma bildirimi](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. Bildirim bölmesinden **Kaynak grubuna git**'i seçin. Şuna benzer bir ekran görmeniz gerekir:

    ![Azure Resource Manager şablonlarını dağıtma kaynak grubu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Dağıtım durumunun başarılı olduğunu ve kaynak grubunda yalnızca bir depolama hesabı olduğunu görebilirsiniz. Depolama hesabı adı, şablon tarafından oluşturulan benzersiz bir dizedir. Azure depolama hesaplarını kullanma hakkında daha fazla bilgi edinmek için bkz. [Hızlı başlangıç: Azure portalı kullanarak blobları yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Depolama hesabı kaynak grubunda bulunmalıdır.
4. Üstteki menüden **Kaynak grubunu sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portaldan şablon oluşturmayı ve portalı kullanarak şablonu dağıtmayı öğrendiniz. Bu Hızlı Başlangıçta kullanılan şablon, tek bir Azure kaynağına sahip basit bir şablondur. Şablon karmaşık olduğunda geliştirme için Visual Studio Code veya Visual Studio uygulamasını kullanmak daha kolaydır. Şablon geliştirme hakkında daha fazla bilgi edinmek için bkz. Yeni Başlangıç Öğreticisi serisi:

> [!div class="nextstepaction"]
> [Başlangıç öğreticileri](./template-tutorial-create-first-template.md)