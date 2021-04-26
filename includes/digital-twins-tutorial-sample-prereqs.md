---
author: baanders
description: Azure dijital TWINS öğreticileri için dosya ekleme-örnek proje önkoşulları
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 00d584690d37f1dcc47b785ef533abe888befec3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510963"
---
## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle aşağıdaki önkoşulları gerçekleştirmeniz gerekir. 

Azure aboneliğiniz yoksa başlamadan önce **[ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun** .

### <a name="get-required-resources"></a>Gerekli kaynakları al

Bu öğreticiyi tamamlayabilmeniz için geliştirme makinenize **[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), sürüm 16,5 veya üstünü yüklersiniz** . Daha eski bir sürümü zaten yüklüyse, makinenizde *Visual Studio yükleyicisi* uygulamayı açabilir ve yüklemenizi güncelleştirmek için istemleri izleyebilirsiniz.

>[!NOTE]
> Visual Studio 2019 yüklemenizin **[Azure geliştirme Iş yükünü](/dotnet/azure/configure-visual-studio)** içerdiğinden emin olun. Bu iş yükü, bir uygulamanın Azure işlevleri yayımlamasını ve diğer Azure geliştirme görevlerini gerçekleştirmesini sağlar.

Öğretici, C# dilinde yazılmış örnek bir proje tarafından çalıştırılır. Örnek şurada bulunur: [Azure Digital TWINS uçtan uca örnekler](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Örnek bağlantısına gidip başlık altındaki *kodu görüntüle* düğmesini seçerek makinenizde **örnek projeyi alın** . Bu işlem sizi, örnek olarak indirebileceğiniz GitHub deposuna götürür *.* *Kod* düğmesini seçip zip ' i *indirin*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="GitHub 'daki dijital-TWINS-örnek deposunun ekran görüntüsü. Kod düğmesi seçilidir ve posta Indirme düğmesinin vurgulandığı küçük bir iletişim kutusu oluşturulur." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Bu, bir yükler *. ZIP* klasörünü makinenize **digital-twins-samples-master.zip**. Klasörü sıkıştırmayı açın ve dosyaları ayıklayın.

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure dijital TWINS örneği hazırlama

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
