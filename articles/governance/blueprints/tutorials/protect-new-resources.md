---
title: Yeni kaynakları şema kilitleri ile koruma
description: Bu öğreticide, Azure şemaları kaynak kilitleri seçeneklerini salt okunurdur ve yeni dağıtılan kaynakları korumak için silme ' yi kullanmayı öğreneceksiniz.
ms.date: 03/28/2019
ms.topic: tutorial
ms.openlocfilehash: e2adbb1e47222055753d4b3690646daa83b32bf1
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960262"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Öğretici: Azure şemaları kaynak kilitleri ile yeni kaynakları koruma

Azure şemaları [kaynak kilitleri](../concepts/resource-locking.md)ile, yeni dağıtılan _kaynakların, sahip rolü olan_ bir hesap tarafından bile üzerinde oynanarak korunmasını sağlayabilirsiniz. Bu korumayı, bir kaynak yöneticisi şablonu yapıtı tarafından oluşturulan kaynakların şema tanımlarına ekleyebilirsiniz.

Bu öğreticide, aşağıdaki adımları tamamlayacaksınız:

> [!div class="checklist"]
> - Şema tanımı oluşturma
> - Şema tanımınızı **yayımlandı** olarak işaretleyin
> - Şema tanımınızı mevcut bir aboneliğe atama
> - Yeni kaynak grubunu İncele
> - Kilitleri kaldırmak için şema atamasını kaldırma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için bir Azure aboneliğinizin olması gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-a-blueprint-definition"></a>Şema tanımı oluşturma

İlk olarak, şema tanımını oluşturun.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Soldaki **Başlarken** sayfasında, şema **Oluştur**altında **Oluştur** ' u seçin.

1. Sayfanın üst kısmındaki **boş BLUEPRINT** şema örneğini bulun. **Boş şema ile Başlat**' ı seçin.

1. **Temel** bilgiler sekmesinde bu bilgileri girin:

   - **Şema adı**: şema örneğinin kopyasına bir ad verin. Bu öğreticide, **kilitli-storageaccount**adını kullanacağız.
   - **Şema açıklaması**: şema tanımı için bir açıklama ekleyin. **Dağıtılan kaynaklarda şema kaynak kilitlemeyi test etmek için**kullanın.
   - **Tanım konumu**: üç nokta düğmesini (...) seçin ve ardından şema tanımınızı kaydetmek için yönetim grubunu veya aboneliği seçin.

1. Sayfanın üst kısmındaki **yapıtlar** sekmesini seçin veya sayfanın altındaki **yapıtlar** ' ı seçin.

1. Abonelik düzeyinde bir kaynak grubu ekleyin:
   1. **Abonelik**altında **yapıt Ekle** satırını seçin.
   1. **Yapıt türü**altında **kaynak grubu** ' nu seçin.
   1. **Yapıt görünen adını** **rgtolock**olarak ayarlayın.
   1. **Kaynak grubu adını** ve **konum** kutularını boş bırakın, ancak her bir özellikte onay kutusunun seçili olduğundan emin olun ve **dinamik parametreleri**yapın.
   1. Yapıtı şemasını Blueprint öğesine eklemek için **Ekle** ' yi seçin.

1. Kaynak grubunun altına bir şablon ekleyin:
   1. **Rgtolock** girişinin altındaki **yapıt Ekle** satırını seçin. 
   1. **Yapıt türü**altında **Azure Resource Manager şablonu** ' nu seçin, **yapıt görünen adını** **storageaccount**olarak ayarlayın ve **açıklamayı** boş bırakın. 
   1. **Şablon** sekmesinde, aşağıdaki kaynak yöneticisi şablonunu düzenleyici kutusuna yapıştırın. Şablonu yapıştırdıktan sonra, yapıtı şemasını Blueprint 'e eklemek için **Ekle** ' yi seçin.

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
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
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

1. Sayfanın alt kısmındaki **Taslağı kaydet** ' i seçin.

Bu adım, seçilen yönetim grubunda veya abonelikte şema tanımını oluşturur.

Şema **tanımını kaydetme başarılı oldu** Portal bildirimi göründüğünde bir sonraki adıma gidin.

## <a name="publish-the-blueprint-definition"></a>Şema tanımını yayımlama

Şema tanımınız artık ortamınızda oluşturulmuştur. **Taslak** modunda oluşturulur ve atanmadan ve dağıtılmadan önce yayımlanmaları gerekir.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. **Kilitli-storageaccount** şeması tanımını bulmak için filtreleri kullanın ve ardından seçin.

1. Sayfanın üst kısmındaki şemayı **Yayımla** ' yı seçin. Sağdaki yeni bölmede **Sürüm**olarak **1,0** girin. Daha sonra bir değişiklik yaparsanız bu özellik faydalıdır. **Şeması dağıtılan kaynakları kilitlemek için yayımlanan ilk sürüm**gibi **değişiklik notlarını**girin. Ardından sayfanın alt kısmında **Yayımla** ' yı seçin.

Bu adım, şema 'in bir aboneliğe atanmasını olanaklı kılar. Şema tanımı yayımlandıktan sonra yine de değişiklik yapabilirsiniz. Değişiklik yaparsanız, aynı şema tanımının sürümleri arasındaki farkları izlemek için tanımı yeni bir sürüm değeriyle yayımlamanız gerekir.

Şema **tanımını yayımlama başarılı oldu** Portal bildirimi göründüğünde bir sonraki adıma gidin.

## <a name="assign-the-blueprint-definition"></a>Şema tanımını atama

Şema tanımı yayımlandıktan sonra, bunu kaydettiğiniz yönetim grubu içindeki bir aboneliğe atayabilirsiniz. Bu adımda, şema tanımının her bir dağıtımını benzersiz hale getirmek için parametreler sağlarsınız.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. **Kilitli-storageaccount** şeması tanımını bulmak için filtreleri kullanın ve ardından seçin.

1. Şema tanım sayfasının en üstünde şema **ata** ' yı seçin.

1. Şema atamasının parametre değerlerini sağlayın:

   - **Temel Bilgiler**

     - **Abonelikler**: şema tanımınızı kaydettiğiniz yönetim grubunda olan bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girdiğiniz parametreleri kullanarak her bir abonelik için bir atama oluşturulacaktır.
     - **Atama adı**: ad, şema tanımının adına göre önceden doldurulur. Bu atamanın yeni kaynak grubunu kilitlemeyi göstermesini istiyoruz, bu nedenle atama adını **atama-kilitli-storageaccount-Testingbpkilitler**olarak değiştirin.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).
       Bu öğretici için **Doğu ABD 2**' yi seçin.
     - Şema **tanımı sürümü**: şema tanımının yayınlanan **1,0** sürümünü seçin.

   - **Kilit ataması**

     **Salt okuma** şeması kilit modunu seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

   - **Yönetilen kimlik**

     Varsayılan seçeneği kullanın: **sistem atandı**. Daha fazla bilgi için bkz. [Yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Yapıt parametreleri**

     Bu bölümde tanımlanan parametreler, tanımlandıkları yapıt için geçerlidir. Şema atama sırasında tanımlandıklarından, bu parametreler [dinamik parametrelerdir](../concepts/parameters.md#dynamic-parameters) . Her yapıt için, parametre değerini değer sütununda gördüğünüz **değere** ayarlayın.

     |Yapıt adı|Yapıt türü|Parametre adı|Değer|Açıklama|
     |-|-|-|-|-|
     |RGtoLock kaynak grubu|Kaynak grubu|Ad|Testingbpkilitleri|Şema kilitlerinin uygulanacağı yeni kaynak grubunun adını tanımlar.|
     |RGtoLock kaynak grubu|Kaynak grubu|Konum|Batı ABD 2|Şema kilitlerini uygulamak için yeni kaynak grubunun konumunu tanımlar.|
     |StorageAccount|Resource Manager şablonu|storageAccountType (StorageAccount)|Standard_GRS|Depolama SKU 'SU. Varsayılan değer _Standard_LRS_.|

1. Tüm parametreleri girdikten sonra sayfanın alt kısmındaki **ata** ' yı seçin.

Bu adım, tanımlı kaynakları dağıtır ve seçili **kilit atamasını**yapılandırır. Şema kilitlerinin uygulanması 30 dakika kadar sürebilir.

Şema **tanımı başarılı oldu** Portal bildirimi seçildikten sonra, bir sonraki adıma gidin.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Atama tarafından dağıtılan kaynakları İncele

Atama, _Testingbpkilitler_ kaynak grubunu ve Kaynak Yöneticisi şablonu yapıtı tarafından dağıtılan depolama hesabını oluşturur. Yeni kaynak grubu ve seçilen kilit durumu atama ayrıntıları sayfasında gösterilir.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **atanan** şemalar sayfasını seçin. **Atama-kilitli-storageaccount-Testingbpkilitleri** şeması atamasını bulmak için filtreleri kullanın ve ardından seçin.

   Bu sayfadan, atamanın başarılı olduğunu ve kaynakların yeni şema kilit durumuyla dağıtıldığını görebiliriz. Atama güncelleştirilirse, **atama işlemi** açılır listesi her tanım sürümünün dağıtımıyla ilgili ayrıntıları gösterir. Özellik sayfasını açmak için kaynak grubunu seçebilirsiniz.

1. **Testingbpkilitler** kaynak grubunu seçin.

1. Sol taraftaki **erişim denetimi (IAM)** sayfasını seçin. Ardından **rol atamaları** sekmesini seçin.

   Burada, _atama-kilitleme-storageaccount-testingbpkilitleri_ şema atamasının _Owner_ rolüne sahip olduğunu görüyoruz. Bu rol, kaynak grubunu dağıtmak ve kilitlemek için kullanıldığından bu rolü içeriyor.

1. **Atamaları Reddet** sekmesini seçin.

   Şema ataması, **salt okuma** şeması kilit modunu zorlamak için dağıtılan kaynak grubunda bir [reddetme ataması](../../../role-based-access-control/deny-assignments.md) oluşturdu. Reddetme ataması, **rol atamaları** sekmesinde uygun haklara sahip birinin belirli eylemleri almasını engeller. Reddetme ataması _tüm sorumluları_etkiler.

   Bir sorumluyu reddetme atamasından dışlamak hakkında bilgi için bkz. [kaynak kilitlemeyi planlar](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Reddetme atamasını seçin ve ardından sol taraftaki **Reddedilenler izinleri** sayfasını seçin.

   Reddetme ataması **\*** ve **eylem** yapılandırmasıyla tüm Işlemleri engellemektedir, ancak **NotActions**aracılığıyla **\*/Read** 'i dışlayarak okuma erişimine izin verir.

1. Azure portal içerik haritasında **Testingbpkilitler-Access Control (IAM)** öğesini seçin. Ardından sol taraftaki **genel bakış** sayfasını ve ardından **kaynak grubunu sil** düğmesini seçin. Silmeyi onaylamak için **Testingbpkilitleri** adını girin ve ardından bölmenin altındaki **Sil** ' i seçin.

   Portal bildirim **silme kaynak grubu Testingbpkilitleri başarısız oldu** . Hata, hesabınız kaynak grubunu silme iznine sahip olsa da, erişim şeması atama tarafından reddedilir. Şema atama sırasında **yalnızca okuma** şeması kilit modunu seçtiğinizi unutmayın. Şema Lock, izin olan bir hesabın, hatta _sahibi_, kaynağın silinmesini engeller. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

Bu adımlarda, dağıtılmış kaynaklarımızın, kaynakları silme izni olan bir hesaptan bile, istenmeyen silme işlemini önleyen şema kilitleri ile korunduğu gösterilmektedir.

## <a name="unassign-the-blueprint"></a>Şema atamasını kaldırma

Son adım, şema tanımının atamasını kaldırdır. Atamanın kaldırılması ilişkili yapıtları kaldırmaz.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **atanan** şemalar sayfasını seçin. **Atama-kilitli-storageaccount-Testingbpkilitleri** şeması atamasını bulmak için filtreleri kullanın ve ardından seçin.

1. Sayfanın üst kısmında **şema atamasını Kaldır** ' ı seçin. Onay iletişim kutusunda uyarıyı okuyun ve ardından **Tamam**' ı seçin.

   Şema ataması kaldırıldığında, BLUEPRINT kilitleri da kaldırılır. Kaynaklar bir kez daha uygun izinlere sahip bir hesap tarafından silinebilir.

1. Azure menüsünden **kaynak grupları** ' nı seçin ve ardından **Testingbpkilitleri**' nı seçin.

1. Sol taraftaki **erişim denetimi (IAM)** sayfasını seçin ve ardından **rol atamaları** sekmesini seçin.

Kaynak grubunun güvenliği, şema atamasının artık _sahip_ erişimine sahip olmadığını gösterir.

Şema **atamasını kaldırma başarılı oldu** Portal bildirimi görüntülendikten sonra bir sonraki adıma gidin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiyi tamamladığınızda, şu kaynakları silin:

- Kaynak grubu _Testingbpkilitleri_
- Blueprint Definition _kilitlendi-storageaccount_

## <a name="next-steps"></a>Sonraki adımlar

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema kaynak kilitlemeyi](../concepts/resource-locking.md)nasıl kullanacağınızı öğrenin.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- Blueprint atama sırasında [sorunları giderin](../troubleshoot/general.md) .
