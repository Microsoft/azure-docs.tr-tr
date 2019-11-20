---
title: include dosyası
description: include dosyası
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 26793c91c85ba4cc33a07358ab8ff318becb325f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490701"
---
## <a name="prerequisites"></a>Önkoşullar

### <a name="azure-subscription"></a>Azure aboneliği
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

### <a name="azure-roles"></a>Azure rolleri
Data Factory örnekleri oluşturmak için, Azure’da oturum açarken kullandığınız kullanıcı hesabı, *katkıda bulunan*, *sahip* veya *yönetici* rollerinin üyesi ya da bir Azure aboneliğinin yöneticisi olmalıdır. Abonelikte sahip olduğunuz izinleri görüntülemek için [Azure Portal](https://portal.azure.com)gidin, sağ üst köşedeki Kullanıcı adınızı seçin, **diğer seçenekler** (...) öğesini seçin ve ardından **izinlerim**' i seçin. Birden çok aboneliğe erişiminiz varsa uygun aboneliği seçin.

Data Factory için veri kümeleri, bağlı hizmetler, işlem hatları, tetikleyiciler ve tümleştirme çalışma zamanları gibi alt kaynaklar oluşturup yönetmek için aşağıdaki gereksinimlerin karşılanması gerekir:

- Azure portalda alt öğe oluşturup yönetebilmek için kaynak grubu düzeyinde **Data Factory Katılımcısı** rolüne veya daha üst düzey bir role sahip olmanız gerekir.
- PowerShell veya SDK ile alt öğe oluşturup yönetebilmek için kaynak düzeyinde **katkıda bulunan** rolü veya daha üst düzey bir rol yeterli olacaktır.

Kullanıcı rolü eklemeye ilişkin örnek yönergeler için [Rol ekleme](../articles/billing/billing-add-change-azure-subscription-administrator.md) makalesine bakın.

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Data Factory Katılımcısı rolü](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Azure Data Factory için roller ve izinler](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure depolama hesabı
Bu hızlı başlangıçta, genel amaçlı Azure depolama hesabını (özel olarak Blob depolama) hem *kaynak* hem de *hedef* veri deposu olarak kullanırsınız. Genel amaçlı bir Azure depolama hesabınız yoksa oluşturma bilgileri için bkz. [Depolama hesabı oluşturma](../articles/storage/common/storage-quickstart-create-account.md). 

#### <a name="get-the-storage-account-name"></a>Depolama hesabı adını al
Bu hızlı başlangıç için Azure depolama hesabınızın adına ihtiyacınız olacak. Aşağıdaki yordam, depolama hesabınızın adını almak için gereken adımları sağlar: 

1. Bir Web tarayıcısında [Azure Portal](https://portal.azure.com) gidin ve Azure Kullanıcı adınızı ve parolanızı kullanarak oturum açın.
2. Azure portal menüsünde **tüm hizmetler**' i seçin ve ardından **depolama** > **depolama hesapları**' nı seçin. Ayrıca, herhangi bir sayfadan *depolama hesaplarını* arayabilir ve seçebilirsiniz.
3. **Depolama hesapları** sayfasında, depolama hesabınız için filtre uygulayın (gerekirse) ve ardından depolama hesabınızı seçin. 

Ayrıca, herhangi bir sayfadan *depolama hesaplarını* arayabilir ve seçebilirsiniz.

#### <a name="create-a-blob-container"></a>Blob kapsayıcısı oluşturma
Bu bölümde, Azure Blob depolama alanında **adftutorial** adlı bir blob kapsayıcısı oluşturursunuz.

1. Depolama hesabı sayfasından **genel bakış** > **Blobları**' nı seçin.
2. *\<hesap adı >*  - **Blobları** sayfasının araç çubuğunda **kapsayıcı**' yı seçin.
3. **Yeni kapsayıcı** iletişim kutusunda ad olarak **adftutorial** girin ve ardından **Tamam**’ı seçin. *\<hesap adı >*  - **Blobları** sayfası, kapsayıcılar listesinde **adföğreticisi** içerecek şekilde güncelleştirilir.

   ![Kapsayıcılar listesi](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Blob kapsayıcısı için bir giriş klasörü ve dosyası ekleyin
Bu bölümde, yeni oluşturduğunuz kapsayıcıda **giriş** adlı bir klasör oluşturur ve ardından giriş klasörüne örnek bir dosya yüklersiniz. Başlamadan önce, **Not defteri**gibi bir metin düzenleyicisini açın ve aşağıdaki **içerikle birlikte bir** dosya Düzenleyicisi oluşturun:

```emp.txt
John, Doe
Jane, Doe
```

Dosyayı **C:\ADFv2QuickStartPSH** klasörüne kaydedin. (Klasör zaten mevcut değilse, oluşturun.) Sonra Azure portal geri dönüp şu adımları izleyin:

1. *\<hesap adı >*  - **Blobları** sayfasında, güncelleştirilmiş kapsayıcı listesinden **adföğreticisi** ' ni seçin.

   1. Pencereyi kapattıysanız veya başka bir sayfaya bir sorun yaşıyorsanız, [Azure Portal](https://portal.azure.com) yeniden oturum açın.
   1. Azure portal menüsünde **tüm hizmetler**' i seçin ve ardından **depolama** > **depolama hesapları**' nı seçin. Ayrıca, herhangi bir sayfadan *depolama hesaplarını* arayabilir ve seçebilirsiniz.
   1. Depolama hesabınızı seçin ve ardından **blob** > **adföğreticisi**' ni seçin.

2. **Adföğreticisi** kapsayıcı sayfasının araç çubuğunda **karşıya yükle**' yi seçin.
3. **Blobu karşıya yükle** sayfasında, **dosyalar** kutusunu seçin ve ardından, ve sonra da **. txt** dosyasını seçin.
4. **Gelişmiş** başlık ' ı genişletin. Sayfa artık gösterildiği gibi görüntülenir:

   ![Gelişmiş bağlantı seçeneğini belirleme](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. **Klasöre yükle** kutusuna **giriş**' i girin.
6. Ardından **Yükle** düğmesini seçin. Listede **emp.txt** dosyasını ve karşıya yükleme durumunu görmeniz gerekir.
7. **BLOB yükle** sayfasını kapatmak için **Kapat** simgesini (bir **X**) seçin.

**Adföğreticisi** kapsayıcı sayfasını açık tutun. Bu hızlı başlangıcın sonundaki çıktıyı doğrulamak için bu sayfayı kullanırsınız.