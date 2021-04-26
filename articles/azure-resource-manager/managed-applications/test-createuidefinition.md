---
title: UI tanım dosyasını test etme
description: Portal aracılığıyla Azure yönetilen uygulamanızı oluşturmak için kullanıcı deneyiminin nasıl test edileceğini açıklar.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: eeef454e4c5706b39d07261ade1c2f0ffbc942ad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478905"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure yönetilen uygulamalar için Portal arabiriminizi test etme

Yönetilen uygulamanız için [dosyada createUiDefinition.js](create-uidefinition-overview.md) oluşturduktan sonra, Kullanıcı deneyimini test etmeniz gerekir. Sınamayı basitleştirmek için, dosyanızı portala yükleyen bir korumalı alan ortamı kullanın. Yönetilen uygulamanızı gerçekten dağıtmanız gerekmez. Korumalı alan, Kullanıcı arabiriminizi geçerli, tam ekran portalı deneyiminde gösterir. Ya da, arabirimi test etmek için bir komut dosyası kullanabilirsiniz. Her iki yaklaşım da bu makalede gösterilmektedir. Korumalı alan, arabirimi önizlemek için önerilen yoldur.

## <a name="prerequisites"></a>Önkoşullar

* Dosyadaki bir **createUiDefinition.js** . Bu dosyaya sahip değilseniz [örnek dosyayı](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json)kopyalayın.

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="use-sandbox"></a>Korumalı alanı kullan

1. [UI tanımı oluştur korumalı alanını](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)açın.

   ![Korumalı alanı göster](./media/test-createuidefinition/show-sandbox.png)

1. Boş tanımı, dosyadaki createUiDefinition.jsiçeriğiyle değiştirin. **Önizleme**' yi seçin.

   ![Önizlemeyi seçin](./media/test-createuidefinition/select-preview.png)

1. Oluşturduğunuz form görüntülenir. Kullanıcı deneyiminden ileredebilir ve değerleri doldurabilirsiniz.

   ![Formu göster](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Sorun giderme

**Önizleme** seçildikten sonra formunuz görünmezse, bir sözdizimi hatası olabilir. Sağ kaydırma çubuğunda kırmızı göstergeyi bulun ve üzerine gelin.

![Sözdizimi göster hatası](./media/test-createuidefinition/show-syntax-error.png)

Formunuz görünmezse ve bunun yerine, bir bulutun simgesini tear ile görürseniz, formunuzda eksik bir özellik gibi bir hata vardır. Web Geliştirici Araçları tarayıcınızda açın. **Konsol** arabiriminiz hakkında önemli iletileri görüntüler.

![Hatayı göster](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Test betiği kullan

Arabiriminizi portalda test etmek için aşağıdaki betiklerin birini yerel makinenize kopyalayın:

* [PowerShell dışarıdan yükleme betiği-az Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell dışarıdan yükleme betiği-Azure modülü](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLı dışarıdan yükleme betiği](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Arabirim dosyanızı portalda görmek için, indirilen komut dosyanızı çalıştırın. Betik, Azure aboneliğinizde bir depolama hesabı oluşturur ve dosyayı createUiDefinition.jsdepolama hesabına yükler. Depolama hesabı, betiği ilk kez çalıştırdığınızda veya depolama hesabı silinmişse oluşturulur. Depolama hesabı Azure aboneliğinizde zaten varsa, komut dosyası onu yeniden kullanır. Betik, portalı açar ve dosyanızı depolama hesabından yükler.

Depolama hesabı için bir konum sağlayın ve dosyada createUiDefinition.jsolan klasörü belirtin.

PowerShell için şunu kullanın:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI için şunu kullanın:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

createUiDefinition.jsdosyadaki dosyanız komut dosyasıyla aynı klasörsdakiler ve depolama hesabını zaten oluşturduysanız, bu parametreleri sağlamanız gerekmez.

PowerShell için şunu kullanın:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Azure CLI için şunu kullanın:

```bash
./sideload-createuidef.sh
```

Betik, tarayıcınızda yeni bir sekme açar. Bu, portalı yönetilen uygulamayı oluşturmak için arabiriminize göre görüntüler.

Alanlar için değerler sağlayın. İşiniz bittiğinde, tarayıcınızın geliştirici araçları konsolunda bulunan şablona geçirilen değerleri görürsünüz.

![Değerleri göster](./media/test-createuidefinition/show-json.png)

Bu değerleri, dağıtım şablonunuzu test etmek için parametre dosyası olarak kullanabilirsiniz.

Portal Özet ekranında askıda kalırsa, çıkış bölümünde bir hata olabilir. Örneğin, mevcut olmayan bir denetime başvurmuş olabilirsiniz. Çıktıdaki bir parametre boşsa, parametre mevcut olmayan bir özelliğe başvuruda bulunabilir. Örneğin, denetime başvuru geçerlidir, ancak özellik başvurusu geçerli değildir.

## <a name="test-your-solution-files"></a>Çözüm dosyalarınızı test etme

Portal Arabiriminizin beklendiği gibi çalıştığını doğruladığınıza göre, Createuıdefinition dosyanızın dosyadaki mainTemplate.jsuygun şekilde tümleştirildiğini doğrulamanız zaman alır. Createuıdefinition dosyası dahil olmak üzere çözüm dosyalarınızın içeriğini test etmek için bir doğrulama betik testi çalıştırabilirsiniz. Betik JSON sözdizimini doğrular, metin alanlarındaki Regex ifadelerini denetler ve Portal arabiriminin çıkış değerlerinin şablonunuzun parametreleriyle eşleştiğinden emin olur. Bu betiği çalıştırma hakkında bilgi için bkz. [Şablonlar için statik doğrulama denetimlerini çalıştırma](https://aka.ms/arm-ttk).

## <a name="next-steps"></a>Sonraki adımlar

Portal arabiriminizi doğruladıktan sonra [Azure yönetilen uygulamanızı Market 'te kullanılabilir](../../marketplace/create-new-azure-apps-offer.md)hale getirme hakkında bilgi edinin.
