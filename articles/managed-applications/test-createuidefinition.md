---
title: Azure yönetilen uygulamalar için Kullanıcı arabirimi tanımını sınama | Microsoft Docs
description: Portal aracılığıyla Azure yönetilen uygulamanızı oluşturmak için kullanıcı deneyiminin nasıl test edileceğini açıklar.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 5a28acfeeda12ab999b16f71cbe789fefcea736f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028352"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure yönetilen uygulamalar için Portal arabiriminizi test etme

Yönetilen uygulamanız için [Createuıdefinition. json dosyasını](create-uidefinition-overview.md) oluşturduktan sonra, Kullanıcı deneyimini test etmeniz gerekir. Sınamayı basitleştirmek için, dosyanızı portala yükleyen bir korumalı alan ortamı kullanın. Yönetilen uygulamanızı gerçekten dağıtmanız gerekmez. Korumalı alan, Kullanıcı arabiriminizi geçerli, tam ekran portalı deneyiminde gösterir. Ya da, arabirimi test etmek için bir komut dosyası kullanabilirsiniz. Her iki yaklaşım da bu makalede gösterilmektedir. Korumalı alan, arabirimi önizlemek için önerilen yoldur.

## <a name="prerequisites"></a>Önkoşullar

* **Createuıdefinition. JSON** dosyası. Bu dosyaya sahip değilseniz [örnek dosyayı](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)kopyalayın.

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="use-sandbox"></a>Korumalı alanı kullan

1. [UI tanımı oluştur korumalı alanını](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)açın.

   ![Korumalı alanı göster](./media/test-createuidefinition/show-sandbox.png)

1. Boş tanımı Createuıdefinition. JSON dosyanızın içeriğiyle değiştirin. **Önizleme**' yi seçin.

   ![Önizlemeyi seçin](./media/test-createuidefinition/select-preview.png)

1. Oluşturduğunuz form görüntülenir. Kullanıcı deneyiminden ileredebilir ve değerleri doldurabilirsiniz.

   ![Formu göster](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Sorun giderme

**Önizleme**seçildikten sonra formunuz görünmezse, bir sözdizimi hatası olabilir. Sağ kaydırma çubuğunda kırmızı göstergeyi bulun ve üzerine gelin.

![Sözdizimi göster hatası](./media/test-createuidefinition/show-syntax-error.png)

Formunuz görünmezse ve bunun yerine, bir bulutun simgesini tear ile görürseniz, formunuzda eksik bir özellik gibi bir hata vardır. Web Geliştirici Araçları tarayıcınızda açın. **Konsol** arabiriminiz hakkında önemli iletileri görüntüler.

![Hatayı göster](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Test betiği kullan

Arabiriminizi portalda test etmek için aşağıdaki betiklerin birini yerel makinenize kopyalayın:

* [PowerShell dışarıdan yükleme betiği-az Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell dışarıdan yükleme betiği-Azure modülü](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLı dışarıdan yükleme betiği](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Arabirim dosyanızı portalda görmek için, indirilen komut dosyanızı çalıştırın. Betik, Azure aboneliğinizde bir depolama hesabı oluşturur ve Createuıdefinition. JSON dosyanızı depolama hesabına yükler. Depolama hesabı, betiği ilk kez çalıştırdığınızda veya depolama hesabı silinmişse oluşturulur. Depolama hesabı Azure aboneliğinizde zaten varsa, komut dosyası onu yeniden kullanır. Betik, portalı açar ve dosyanızı depolama hesabından yükler.

Depolama hesabı için bir konum sağlayın ve Createuıdefinition. JSON dosyanıza sahip klasörü belirtin.

PowerShell için şunu kullanın:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI için şunu kullanın:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Createuıdefinition. JSON dosyanız komut dosyasıyla aynı klasörsdakiler ve depolama hesabını zaten oluşturduysanız, bu parametreleri sağlamanız gerekmez.

PowerShell için şunu kullanın:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Azure CLI için şunu kullanın:

```azurecli
./sideload-createuidef.sh
```

Betik, tarayıcınızda yeni bir sekme açar. Bu, portalı yönetilen uygulamayı oluşturmak için arabiriminize göre görüntüler.

Alanlar için değerler sağlayın. İşiniz bittiğinde, tarayıcınızın geliştirici araçları konsolunda bulunan şablona geçirilen değerleri görürsünüz.

![Değerleri göster](./media/test-createuidefinition/show-json.png)

Bu değerleri, dağıtım şablonunuzu test etmek için parametre dosyası olarak kullanabilirsiniz.

Portal Özet ekranında askıda kalırsa, çıkış bölümünde bir hata olabilir. Örneğin, mevcut olmayan bir denetime başvurmuş olabilirsiniz. Çıktıdaki bir parametre boşsa, parametre mevcut olmayan bir özelliğe başvuruda bulunabilir. Örneğin, denetime başvuru geçerlidir, ancak özellik başvurusu geçerli değildir.

## <a name="test-your-solution-files"></a>Çözüm dosyalarınızı test etme

Portal Arabiriminizin beklendiği gibi çalıştığını doğruladığınıza göre, Createuıdefinition dosyanızın mainTemplate. JSON dosyanız ile düzgün bir şekilde tümleştirildiğini doğrulamanız zaman alabilir. Createuıdefinition dosyası dahil olmak üzere çözüm dosyalarınızın içeriğini test etmek için bir doğrulama betik testi çalıştırabilirsiniz. Betik JSON sözdizimini doğrular, metin alanlarındaki Regex ifadelerini denetler ve Portal arabiriminin çıkış değerlerinin şablonunuzun parametreleriyle eşleştiğinden emin olur. Bu betiği çalıştırma hakkında bilgi için bkz. [Şablonlar için statik doğrulama denetimlerini çalıştırma](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Sonraki adımlar

Portal arabiriminizi doğruladıktan sonra [Azure yönetilen uygulamanızı Market 'te kullanılabilir](publish-marketplace-app.md)hale getirme hakkında bilgi edinin.
