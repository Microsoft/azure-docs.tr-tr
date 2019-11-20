---
title: Portalda bir şema oluşturma
description: Azure portal aracılığıyla yapıtlar oluşturmak, tanımlamak ve dağıtmak için Azure şemaları kullanın.
ms.date: 03/11/2019
ms.topic: quickstart
ms.openlocfilehash: 1c2d619a0383cc9542bc6dc3a5f5822f0477237c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960466"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Hızlı başlangıç: portalda bir şema tanımlama ve atama

Planlar oluşturmayı ve atamayı öğrendikçe, Azure Resource Manager şablonları, ilkesi, güvenliği ve daha fazlasını temel alan yeniden kullanılabilir ve hızlı dağıtılabilir yapılandırma geliştirmek için ortak desenler tanımlayabilirsiniz. Bu öğreticide, kuruluşunuzda şema oluşturma, yayımlama ve atama ile ilgili genel görevlerden bazılarını yapmak için Azure şemaları kullanmayı öğreneceksiniz. Bu görevler şunları içerir:

> [!div class="checklist"]
> - Yeni bir şema oluşturma ve çeşitli desteklenen yapıtlar ekleme
> - **Taslak** durumundaki bir şemada değişiklik yapma
> - Bir şemayı **Yayımlandı** durumuna getirerek atamaya hazır hale getirme
> - Bir şemayı var olan bir aboneliğe atama
> - Atanmış bir şemanın durumunu ve ilerlemesini denetleme
> - Bir aboneliğe atanmış olan şemayı kaldırma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-a-blueprint"></a>Şema oluşturma

Uyumluluk için standart desen tanımlamanın ilk adımı kullanılabilir durumdaki kaynaklardan bir şema oluşturmaktır. Bu örnekte, abonelik için rol ve ilke atamalarını yapılandırmak üzere **myblueprint** adlı yeni bir şema oluşturun. Ardından yeni bir kaynak grubu ekleyin ve yeni kaynak grubunda bir Kaynak Yöneticisi şablonu ve rol ataması oluşturun.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki sayfadan şema **tanımlarını** seçin ve sayfanın üst kısmındaki **+ şema oluştur** düğmesini seçin.

   Ya da bir Blueprint oluşturmak için **Başlarken** sayfasından **Oluştur** ' u seçin.

   ![Şema tanımları sayfasından bir şema oluşturma](./media/create-blueprint-portal/create-blueprint-button.png)

1. **Myblueprint**gibi bir **Blueprint adı** sağlayın. (En fazla 48 harf ve sayı kullanın, ancak boşluk veya özel karakter kullanmayın). **Blueprint açıklamasını** şimdilik boş bırakın.

1. **Tanım konumu** kutusunda, sağdaki üç noktayı seçin, şemayı kaydetmek istediğiniz [yönetim grubunu](../management-groups/overview.md) veya aboneliği seçin ve **Seç**' i seçin.

1. Bilgilerin doğru olduğundan emin olun. **Blueprint adı** ve **tanım konumu** alanları daha sonra değiştirilemez. Ardından Ileri: sayfanın en altındaki **yapıtlar** ' ı veya sayfanın en üstündeki **yapılar** sekmesini seçin.

1. Abonelik düzeyinde bir rol ataması ekleyin:

   1. **Abonelik**altında **+ yapıt Ekle** satırını seçin. Tarayıcının sağ tarafında **yapıt Ekle** penceresi açılır.

   1. **Yapıt türü**için **rol ataması** seçin.

   1. **Rol**altında **katkıda bulunan**' ı seçin. Bir dinamik parametreyi belirten onay kutusuyla **Kullanıcı, uygulama veya Grup Ekle** kutusunu bırakın.

   1. Bu yapıtı şemasını Blueprint öğesine eklemek için **Ekle** ' yi seçin.

   ![Şema yapıtı için rol ataması](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Çoğu yapıt parametreleri destekler. Şema oluşturma sırasında bir değer atanan bir parametre *statik bir parametredir*. Parametre, şema atama sırasında atanırsa *dinamik bir parametredir*. Daha fazla bilgi için bkz. [Şema parametreleri](./concepts/parameters.md).

1. Abonelik düzeyine bir ilke ataması ekleyin:

   1. Rol atama yapıtı altında **+ yapıt Ekle** satırını seçin.

   1. **Yapıt türü**için **ilke atamasını** seçin.

   1. **Türü** **yerleşik**olarak değiştirin. **Ara**alanına **Tag**yazın.

   1. Filtrelemenin yapılması için **Ara**’ya tıklayın. **Kaynak grupları için etiketi ve varsayılan değerini Ekle '** yi seçin.

   1. Bu yapıtı şemasını Blueprint öğesine eklemek için **Ekle** ' yi seçin.

1. İlke atama **ekleme etiketinin satırını ve kaynak grupları için varsayılan değerini**seçin.

1. Şema tanımının bir parçası olarak yapıt için parametreler sağlayan pencere açılır ve atama (dinamik parametreler) yerine bu şema temelinde tüm atamalar (statik parametreler) için parametrelerin ayarlanmasına izin verir. Bu örnek, şema atama sırasında dinamik parametreleri kullanır, bu nedenle varsayılan değerleri bırakın ve **iptal**' i seçin.

1. Abonelik düzeyinde bir kaynak grubu ekleyin:

   1. **Abonelik**altında **+ yapıt Ekle** satırını seçin.

   1. **Yapıt türü**için **kaynak grubu** seçin.

   1. **Yapıt görünen adı**, **kaynak grubu adı**ve **konum** kutularını boş bırakın, ancak her bir parametre özelliği için onay kutusunun işaretli olduğundan emin olun.

   1. Bu yapıtı şemasını Blueprint öğesine eklemek için **Ekle** ' yi seçin.

1. Kaynak grubunun altına bir şablon ekleyin:

   1. **ResourceGroup** girişinde **+ yapıt Ekle** satırını seçin.

   1. **Yapıt türü**için **Azure Resource Manager şablonu** seçin, **yapıt görünen adını** **storageaccount**olarak ayarlayın ve **açıklamayı** boş bırakın.

   1. Düzenleyici kutusundaki **Şablon** sekmesinde aşağıdaki Resource Manager şablonunu yapıştırın.
      Şablonu yapıştırdıktan sonra **Parametreler** sekmesini seçin ve **storageaccounttype** ve **Location** şablon parametrelerinin algılandığını unutmayın. Her bir parametre otomatik olarak algılanır ve doldurulmuştur, ancak dinamik parametre olarak yapılandırıldı.

      > [!IMPORTANT]
      > Şablonu içeri aktarıyorsanız, dosyanın yalnızca JSON olduğundan ve HTML içermediğinden emin olun. GitHub 'da bir URL 'yi işaret ederken, GitHub 'da görüntülenmek üzere bir HTML ile sarmalanmayan saf JSON dosyasını almak için **Ham** seçtiğinizden emin olun. İçeri aktarılan şablon saf JSON değilse bir hata oluşur.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. **Storageaccounttype** onay kutusunu temizleyin ve aşağı açılan listenin yalnızca, **allowedValues**altındaki Kaynak Yöneticisi şablonuna dahil edilen değerleri içerdiğini unutmayın. Bir dinamik parametreye geri ayarlamak için kutuyu seçin.

   1. Bu yapıtı şemasını Blueprint öğesine eklemek için **Ekle** ' yi seçin.

   ![Şema yapıtı için Kaynak Yöneticisi şablonu](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Tamamladığınız şema aşağıdakine benzer olmalıdır. Her yapının **Parameters** sütununda doldurulmuş  **_x_ -Out parametresi** olduğuna dikkat edin. Dinamik parametreler, Blueprint 'in her ataması sırasında ayarlanır.

   ![Şema tanımı tamamlandı](./media/create-blueprint-portal/completed-blueprint.png)

1. Tüm planlı yapıtlar eklenmiş olduğuna göre, sayfanın en altındaki **Taslağı kaydet** ' i seçin.

## <a name="edit-a-blueprint"></a>Şema düzenleme

Şema [oluşturma](#create-a-blueprint)' da, bir açıklama sağlamamadınız veya yeni kaynak grubuna rol ataması eklemelisiniz. Aşağıdaki adımları izleyerek her ikisini de giderebilirsiniz:

1. Soldaki sayfadan **Blueprint tanımlarını** seçin.

1. Planlar listesinde, daha önce oluşturduğunuz birine sağ tıklayın ve şemayı **Düzenle**' yi seçin.

1. **Şema açıklamasında**, şema ve onu oluşturan yapıtlar hakkında bazı bilgiler sağlayın. Bu durumda, şöyle bir şey girin: **Bu şema, abonelik üzerinde etiket ilkesi ve rol atamasını ayarlar, bir resourcegroup oluşturur ve bu resourcegroup 'e bir kaynak şablonu ve rol ataması dağıtır.**

1. Ileri ' yi seçin: sayfanın en altındaki **yapıtlar** veya sayfanın en üstündeki **yapılar** sekmesi.

1. Kaynak grubunun altına bir rol ataması ekleyin:

   1. Yalnızca **ResourceGroup** girişinin altında bulunan **+ yapıt Ekle** satırını seçin.

   1. **Yapıt türü**için **rol ataması** seçin.

   1. **Rol**altında, **sahip**' i seçin ve **Kullanıcı, uygulama veya Grup Ekle** kutusunda onay kutusunun işaretini kaldırın.

   1. Eklenecek bir Kullanıcı, uygulama veya grup bulun ve seçin. Bu yapıt, bu Blueprint 'in her atamasında aynı şekilde ayarlanmış statik bir parametre kullanır.

   e. Bu yapıtı şemasını Blueprint öğesine eklemek için **Ekle** ' yi seçin.

   ![Şema yapıtı için ikinci rol ataması](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Tamamladığınız şema aşağıdakine benzer olmalıdır. Yeni eklenen rol atamasında 1 ' den fazla **parametre doldurulduğuna**dikkat edin. Bu, bir statik parametre olduğu anlamına gelir.

   ![Tamamlanmış şema için ikinci tanım](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Taslağı Şimdi güncelleştirilmiş olarak **Kaydet** ' i seçin.

## <a name="publish-a-blueprint"></a>Şemayı yayımlama

Planlanan tüm yapıtları ekledikten sonra şemayı yayımlayabilirsiniz.
Yayımlama, BLUEPRINT 'in bir aboneliğe atanmasını sağlar.

1. Soldaki sayfadan **Blueprint tanımlarını** seçin.

1. Planlar listesinde, daha önce oluşturduğunuz birine sağ tıklayın ve şema **Yayımla**' yı seçin.

1. Açılan bölmede, **v1**gibi en fazla 20 karakter uzunluğunda bir **Sürüm** (harfler, rakamlar ve kısa çizgiler) sağlayın. İsteğe bağlı olarak, **ilk yayımlama**gibi **değişiklik notlarına**metin girin.

1. Sayfanın alt kısmındaki **Yayımla** ' yı seçin.

## <a name="assign-a-blueprint"></a>Şema atama

Şema yayımlandıktan sonra bir aboneliğe atanabilir. Oluşturduğunuz şema öğesini yönetim grubu hiyerarşinizin altındaki aboneliklerden birine atayın. Şema bir aboneliğe kaydedilirse, bu aboneliğe yalnızca atanabilir.

1. Soldaki sayfadan **Blueprint tanımlarını** seçin.

1. Planlar listesinde, daha önce oluşturduğunuz birine sağ tıklayın (veya üç noktayı seçin) ve şema **ata**' yı seçin.

1. **Şeması ata** sayfasında, **abonelik** açılan listesinde, bu şemayı dağıtmak istediğiniz abonelikleri seçin.

   [Azure faturalandırmaya](../../billing/index.md)sunulan desteklenen kurumsal teklifler varsa, **abonelik** kutusu altında **Yeni bir oluştur** bağlantısı etkinleştirilir. Şu adımları uygulayın:

   1. Yeni bir abonelik oluşturmak için **Yeni oluştur** bağlantısını seçin.

   1. Yeni abonelik için bir **görünen ad** belirtin.

   1. Açılan listeden kullanılabilir **teklifi** seçin.

   1. Aboneliğin bir alt öğesi olacağı [yönetim grubunu](../management-groups/overview.md) seçmek için üç noktayı kullanın.

   1. Sayfanın alt kısmında **Oluştur** ' u seçin.

   ![Şema atama aboneliği için abonelik oluşturma](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > Yeni Abonelik **Oluştur**' u seçtikten hemen sonra oluşturulur.

   > [!NOTE]
   > Seçtiğiniz her abonelik için bir atama oluşturulur. Seçili aboneliklerin geri kalanı üzerinde değişiklik yapmaya gerek kalmadan, tek bir abonelik atamasında daha sonra değişiklikler yapabilirsiniz.

1. **Atama adı**için, bu atama için benzersiz bir ad sağlayın.

1. **Konum**' da, içinde oluşturulacak yönetilen kimlik ve abonelik dağıtım nesnesi için bir bölge seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md).

1. **V1** girişinde, **yayımlanan** sürümlerin şema **tanımı sürümü** açılan seçimini bırakın. (Varsayılan değer en son yayımlanan sürümdür.)

1. **Atamayı Kilitle** seçeneği için varsayılan **Kilitleme** ayarını değiştirmeyin. Daha fazla bilgi için bkz. [kaynak kilitlemeyi planlar](./concepts/resource-locking.md).

   ![Atama için kilitleme ve yönetilen kimlikler](./media/create-blueprint-portal/assignment-locking-mi.png)

1. **Yönetilen kimlik**altında, varsayılan olarak **atanmış sistem**' i bırakın.

1. Abonelik düzeyinde rol ataması **[Kullanıcı grubu veya uygulama adı]:Katkıda bulunan** için bir kullanıcı, uygulama veya grup arayıp seçin.

1. Abonelik düzeyi ilke ataması için, **etiket adı** ' nı **costcenter** olarak ayarlayın ve bu **değeri** **contoso.**

1. **ResourceGroup**için, açılan listeden bir **storageaccount** **adı** ve **Doğu ABD 2** bir **konum** sağlayın.

   > [!NOTE]
   > Şema tanımı sırasında kaynak grubu altına eklediğiniz her bir yapıt için, bu yapıt, ile dağıtacağınız kaynak grubu veya nesne ile hizalanacak şekilde girintilenir.
   > Parametre alma veya atama sırasında tanımlanacak hiçbir parametresi olmayan yapıtlar yalnızca bağlamsal bilgiler için listelenir.

1. Azure Resource Manager şablonu **Storageaccount**' da **storageaccounttype** parametresi için **Standard_GRS** öğesini seçin.

1. Sayfanın altındaki bilgi kutusunu okuyun ve ardından **ata**' yı seçin.

## <a name="track-deployment-of-a-blueprint"></a>Şema dağıtımını izleme

Bir şema bir veya daha fazla aboneliğe atandığında iki şey gerçekleşir:

- Şema, her abonelik için **atanan** şemalar sayfasına eklenir.
- Şema tarafından tanımlanan tüm yapıtları dağıtma işlemi başlar.

Şema bir aboneliğe atandığına göre, dağıtımın ilerlemesini doğrulayın:

1. Soldaki sayfadan **atanan** şemalar ' ı seçin.

1. Planlar listesinde, daha önce atadığınız birine sağ tıklayın ve **atama ayrıntılarını görüntüle**' yi seçin.

   ![Atanan şemalar sayfasından atama ayrıntılarını görüntüleme](./media/create-blueprint-portal/view-assignment-details.png)

1. **Blueprint atama** sayfasında, tüm yapıtların başarıyla dağıtıldığını ve dağıtım sırasında hata olmadığını doğrulayın. Hata oluştuysa, nelerin yanlış olduğunu belirlemek için bkz. [sorun giderme şemaları](./troubleshoot/general.md) .

## <a name="unassign-a-blueprint"></a>Şema atamasını kaldırma

Artık bir şema ataması gerekmiyorsa, aboneliği bir abonelikten kaldırın. Şema, güncelleştirilmiş desenler, ilkeler ve tasarımlarla daha yeni bir şema ile değiştirilmiş olabilir. Bir şema kaldırıldığında o şemanın bir parçası olarak atanan yapıtlar geride kalır. Şema atamasını kaldırmak için aşağıdaki adımları izleyin:

1. Soldaki sayfadan **atanan** şemalar ' ı seçin.

1. Planlar listesinde, atamasını kaldırmak istediğiniz şema öğesini seçin. Sonra sayfanın üst kısmındaki **şema atamasını Kaldır** düğmesini seçin.

1. Onay iletisini okuyun ve **Tamam**' ı seçin.

## <a name="delete-a-blueprint"></a>Şema silme

1. Soldaki sayfadan **Blueprint tanımlarını** seçin.

1. Silmek istediğiniz şema öğesine sağ tıklayın ve şemayı **Sil**' i seçin. Onay iletişim kutusunda **Evet** ' i seçin.

> [!NOTE]
> Bu yöntemde bir şema silindiğinde, seçilen şema 'in yayımlanan tüm sürümleri de silinir.
> Tek bir sürümü silmek için, Blueprint ' i açın, **yayımlanmış sürümler** sekmesini seçin, silmek istediğiniz sürümü seçin ve ardından **Bu sürümü Sil**' i seçin. Ayrıca, söz konusu şema tanımının tüm şema atamalarını silene kadar bir şeması silemezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Şema yaşam döngüsü](./concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](./concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](./concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](./concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](./how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](./troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.