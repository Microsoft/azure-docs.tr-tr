---
title: 'Öğretici: Azure purview ile verileri tarama (Önizleme)'
description: Bu öğreticide, bir veri kaynağı kurmak ve sonra veri kaynaklarından verileri Azure purview kataloğunuza taramak için bir başlangıç seti çalıştırırsınız.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 16692ac75f0ab6df0c8ee1bebef393848ca066b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676553"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Öğretici: Azure purview ile verileri tarama (Önizleme)

> [!IMPORTANT]
> Azure takip görünümü şu anda ÖNIZLEME aşamasındadır. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya başka bir şekilde genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.

Bu *beş bölümden oluşan öğretici serisinde*, Azure purview (Önizleme) kullanarak bir veri üzerinde veri yönetimini yönetme temellerini öğreneceksiniz. Öğreticinin bu bölümünde oluşturduğunuz veriler, serinin geri kalanı için kullanılır.

Bu öğretici serisinin 1. bölümünde şunları yapmanız gerekir:

> [!div class="checklist"]
>
> * Çeşitli Azure veri kaynaklarıyla bir veri kaynağı oluşturun.
> * Verileri bir katalogda tarayın.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Azure purview hesabı](create-catalog-portal.md).
* Verilerinizi dağıtan [Başlatıcı seti](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) .

> [!NOTE]
> Starter Kit yalnızca Windows 'da kullanılabilir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-data-estate"></a>Bir veri Emlak oluştur

Bu bölümde, sanal bir veri Emlak oluşturmak için başlangıç seti betikleri çalıştırırsınız. Bir veri, şirketin sahip olduğu tüm verilerin portföyüne aittir. Starter Kit betiği aşağıdaki eylemleri gerçekleştirir:

* Bir Azure Blob depolama hesabı oluşturur ve hesabı verileri ile doldurur.
* Bir Azure Data Lake Storage 2. hesabı oluşturur.
* Bir Azure Data Factory örneği oluşturur ve örneği Azure purview ile ilişkilendirir.
* Azure Blob depolama ile Azure Data Lake Storage 2. hesapları arasında bir kopyalama etkinliği işlem hattı oluşturur ve tetikler.
* İlişkili kökenini Azure Data Factory 'den Azure purview 'a iter.

### <a name="prepare-to-run-the-starter-kit"></a>Başlangıç paketini çalıştırmaya hazırlanma

Windows makinenizde Starter Kit istemci yazılımını ayarlamak için aşağıdaki adımları izleyin:

1. [Başlangıç paketini indirin](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)ve içeriğini seçtiğiniz konuma ayıklayın.


1. Bilgisayarınızda, **PowerShell** ' i Windows görev çubuğundaki arama kutusuna yazın. Arama listesinde, **Windows PowerShell**' e sağ tıklayın ve ardından **yönetici olarak çalıştır**' ı seçin.

1. PowerShell penceresinde, aşağıdaki komutu girin ve `<path-to-starter-kit>` ayıklanan Başlatıcı seti dosyalarının klasör yoluyla değiştirin.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Azure cmdlet 'lerini yüklemek için aşağıdaki komutu girin.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Uyarı istemi görürseniz, *devam etmek Için NuGet sağlayıcısı gerekir*, **Y** girin ve ardından ENTER tuşuna basın.

1. Uyarı isteğini *güvenilir olmayan bir depo* olarak görürseniz, **bir** girin ve ardından ENTER tuşuna basın.

PowerShell 'in gerekli modülleri yüklemesi bir dakika kadar sürebilir.

### <a name="collect-data-needed-to-run-the-scripts"></a>Betikleri çalıştırmak için gereken verileri toplayın

Kataloğu önyüklemek için PowerShell betiklerini çalıştırmadan önce betiklerinizde kullanılacak aşağıdaki bağımsız değişkenlerin değerlerini alın:

* Değerine
   1. [Azure portalda](https://portal.azure.com)**Azure Active Directory**'yi seçin.
   1. Sol gezinti bölmesinin **Yönet** bölümünde **Özellikler**' i seçin. Ardından, değeri panonuza kaydetmek için **KIRACı kimliği** için Kopyala simgesini seçin. Değeri daha sonra kullanmak üzere bir metin düzenleyicisine yapıştırın.

* SubscriptionID
   1. Azure portal, önkoşul olarak oluşturduğunuz Azure purview örneğinin adını arayıp seçin.
   1. **Genel bakış** bölümünü seçin ve **abonelik kimliği** için GUID 'yi kaydedin.

   > [!NOTE]
   > - Azure purview hesabını oluştururken kullandığınız abonelikle aynı aboneliği kullandığınızdan emin olun. Bu, izin verilenler listesine yerleştirilmiş olan abonelikdir.
   > - Kökenini, başlangıç paketini çalıştırdıktan sonra bazen Azure purview içinde eksik olabilir. Bunun nedeni, Starter Kit tarafından oluşturulan Data Factory purview 'da izinlerin eksik olmasından kaynaklanır. Data Factory doğru şekilde yapılandırıldığından ve uygun rolün purview 'a atandığından emin olmak için [**Bu belge bağlantısını**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections)  seçin


* Katalogadı: [Azure purview hesabı oluştur](create-catalog-portal.md)bölümünde oluşturduğunuz Azure purview hesabının adı.

* CatalogResourceGroupName: Azure purview hesabınızı oluşturduğunuz kaynak grubunun adı.

### <a name="verify-permissions"></a>İzinleri doğrulama

Betiği çalıştıran kullanıcıyı bir önkoşul olarak oluşturulan Azure purview hesabına eklemek için bu adımları izleyin. Kullanıcılar hem *purview Data Curator* hem de *veri kaynağı Yöneticisi* rollerine sahip olmalıdır. 

Azure purview hesabını kendiniz oluşturduysanız, otomatik olarak erişim verilir ve bu bölümü atlayabilirsiniz.

1. Azure portal takip hesapları sayfasına gidin ve ardından değiştirmek istediğiniz Azure purview hesabını seçin.

1. Hesabın sayfasında, **erişim denetimi (IAM)** sekmesini ve **+ Ekle**' yi seçin.

1. **Rol ataması ekle**’yi seçin.

1. *Rol* Için **purview Data Curator rolünü** girin.
 
1. *Erişim atama* için varsayılan değeri kullanın. Varsayılan değer **Kullanıcı, Grup veya hizmet sorumlusu** olmalıdır.

1. **Select**' de betiği çalıştıran kullanıcının adını girin.

1. **Kaydet**’i seçin.

1. Önceki adımları, rol **veri kaynağı Yöneticisi rolü** olarak ayarlanan *rolle* tekrarlayın.

Daha fazla bilgi için bkz. [Katalog izinleri](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>İstemci tarafı kurulum betiklerini çalıştırma

Katalog yapılandırması tamamlandıktan sonra, varlıkları oluşturmak için PowerShell penceresinde aşağıdaki komut dosyalarını çalıştırın ve yer tutucuları daha önce topladığınız değerlerle değiştirin.

1. Başlangıç paketi dizinine gitmek için aşağıdaki komutu kullanın. `path-to-starter-kit`Ayıklanan dosyanın klasör yoluyla değiştirin.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. Aşağıdaki komut yerel bilgisayar için yürütme ilkesini ayarlar. Yürütme ilkesini değiştirmeniz istendiğinde, Evet için **bir** *değeri* girin.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Aşağıdaki komutu kullanarak Azure 'a bağlanın. `TenantID` ve `SubscriptionID` yer tutucularını değiştirin.

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Komutu çalıştırdığınızda, Azure Active Directory kimlik bilgilerinizi kullanarak oturum açmanız için bir açılır pencere görünür.


1. Başlangıç paketini çalıştırmak için aşağıdaki komutu kullanın. ,, `CatalogName` , `TenantID` `SubscriptionID` `NewResourceGroupName` Ve `CatalogResourceGroupName` yer tutucularını değiştirin. İçin `NewResourceGroupName` , verileri içeren kaynak grubu için benzersiz bir ad (yalnızca küçük harf alfasayısal karakterlerle) kullanın.

   > [!IMPORTANT]
   > **Newresourcegroupname** yalnızca sayı ve küçük harf kullanır ve 17 karakterden az olmalıdır. **Büyük harfli harflerden oluşan sonuçlara ve özel karakterlere izin verilmez.** Bu kısıtlama, depolama hesabı adlandırma kurallarından gelir.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Ortamın ayarlanması, en fazla 10 dakika sürebilir. Bu süre boyunca, çeşitli açılır pencereler görebilirsiniz. Bu işlem, yoksayabilirsiniz. **BlobDataCreator.exe** penceresini kapatmayın; bittiğinde otomatik olarak kapanır.

İletiyi gördüğünüzde `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` , başka bir **BlobDataCreator.exe** örneğinin çalışmaya başlamasını ve bitmesini bekleyin.

> [!IMPORTANT]
> ' Etkin görev sayısı ' azaltılmasını sonlandırması durumunda, blob Oluşturucu penceresinden çıkabilir ve PowerShell penceresinde ENTER tuşuna basabilirsiniz

İşlem tamamlandıktan sonra, sağladığınız ada sahip bir kaynak grubu oluşturulur. Azure Data Factory, Azure Blob depolama ve Azure Data Lake Storage 2. hesapları bu kaynak grubunda bulunur. Kaynak grubu, belirttiğiniz abonelikte yer alır.

## <a name="scan-data-into-the-catalog"></a>Verileri kataloğa tarama

Tarama, kataloğun Kullanıcı tarafından belirtilen zamanlamaya göre doğrudan bir veri kaynağına bağlandığı bir işlemdir. Katalog, bir şirketin tarama, kökenini, Portal ve API aracılığıyla veri Emlak olduğunu yansıtır. Hedefler, içinde nelerin olduğunu, şemaları ayıkladığını ve semantiğini anlamaya çalışırken kapsar. Bu bölümde, Starter Kit ile oluşturduğunuz veri kaynaklarını bir taramayı ayarlarsınız.

Çalıştırdığınız Başlatıcı seti betiği, Azure Blob depolama ve Azure Data Lake Storage 2. olmak üzere iki veri kaynağı oluşturdunuz. Bu veri kaynaklarını tek seferde Katalog halinde tarayabilirsiniz.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Yönetilen kimlikle depolama alanınızı kimlik doğrulama

Hesabınız oluşturulduğunda, Azure purview hesabınızla aynı ada sahip yönetilen bir kimlik otomatik olarak oluşturulur. Verilerinizi taramadan önce depolama hesaplarınıza Azure purview rolü izinleri vermeniz gerekir.

1. Starter Kit tarafından oluşturulan kaynak grubuna gidin ve BLOB depolama hesabınızı seçin.

1. Sol gezinti menüsünden **Access Control (IAM)** seçeneğini belirleyin. Ardından **+ Ekle**' yi seçin.

1. Rolü **Depolama Blobu veri okuyucusu** olarak ayarlayın ve **Seç** için Azure purview hesabınızın adını girin. Ardından, bu rol atamasını purview hesabınıza vermek için **Kaydet** ' i seçin.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Rol ataması ekle":::

1. Azure Data Lake Storage 2. için önceki adımları tekrarlayın.

### <a name="scan-your-data-sources"></a>Veri kaynaklarınızı tarayın

1. [Azure Portal](https://portal.azure.com) Azure purview kaynağına gidin ve *purview Studio 'yu aç*' ı seçin. Otomatik olarak purview Studio 'nun giriş sayfasına yönlendirilirsiniz.

1. Kataloğunuzun Web sayfasında **kaynakları** seçin ve **Kaydet**' i seçin. Ardından **Azure Blob depolama** ' yı seçin ve **devam edin**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="BLOB depolama kaynağını Kaydet":::

1. **Kaynakları kaydet** sayfasında, bir **ad** girin. Daha önce Starter Kit ile oluşturduğunuz Azure Blob depolama hesabının **depolama hesabı adını** seçin. Hesabın adı vardır `<YourResourceGroupName>adcblob` . **Son**'u seçin.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Azure Blob depolama veri kaynağının kaydedileceği ayarları gösteren ekran görüntüsü." border="true":::

1. **Veri kaynakları** Haritası görünümünde, Azure Blob depolama kutucuğunda **Yeni tarama** ' yı seçin.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Bir veri kaynağından tarama kurulumunun nasıl seçileceğini gösteren ekran görüntüsü." border="true":::

1. **Tarama** sayfasında, bir tarama adı girin, **kimlik bilgileri** açılan listesinden yönetilen kimliğinizi seçin ve **devam edin**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Azure purview 'da blob depolamayı Tara":::

1. Taramanızı ayrı bloblara atayabilirsiniz. Bu öğreticide, her şeyi taramak ve **devam** etmek için tüm varlıkları kontrol edin.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Depolama taramanızı kapsama":::

1. Varsayılan tarama kuralı kümesini seçin ve **devam edin**.

1. Yinelenen taramalar için bir tarama tetikleyicisi ayarlayabilirsiniz. Bu öğretici için **bir kez** seçin ve **devam edin**.

1. Taramayı gerçekleştirmek için **Kaydet ve Çalıştır ' ı** seçin.

1. Azure Data Lake Storage 2. hesabınızı taramak için önceki adımları tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
>
> * Bir veri Emlak ortamı ayarlamak için Starter Kit betiğini çalıştırın.
> * Verileri bir Azure purview kataloğuna tarayın.

Giriş sayfasında gezinme ve varlık arama hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Giriş sayfasında gezinme ve varlık arama](tutorial-asset-search.md)
