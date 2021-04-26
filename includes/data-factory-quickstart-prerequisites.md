---
title: include dosyası
description: include dosyası
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: include
ms.custom: include file
ms.date: 06/27/2019
ms.openlocfilehash: 0c21c576cbb99ccc96bf7edf733769a9b28b9344
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612232"
---
## <a name="prerequisites"></a>Önkoşullar

### <a name="azure-subscription"></a>Azure aboneliği

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

### <a name="azure-roles"></a>Azure rolleri

Data Factory örnekleri oluşturmak için, Azure’da oturum açarken kullandığınız kullanıcı hesabı, *katkıda bulunan*, *sahip* veya *yönetici* rollerinin üyesi ya da bir Azure aboneliğinin yöneticisi olmalıdır. Abonelikte sahip olduğunuz izinleri görüntülemek için [Azure Portal](https://portal.azure.com)gidin, sağ üst köşedeki Kullanıcı adınızı seçin, daha fazla seçenek için "**...**" simgesini seçin ve sonra **izinlerim**' i seçin. Birden çok aboneliğe erişiminiz varsa uygun aboneliği seçin.

Data Factory için veri kümeleri, bağlı hizmetler, işlem hatları, tetikleyiciler ve tümleştirme çalışma zamanları gibi alt kaynaklar oluşturup yönetmek için aşağıdaki gereksinimlerin karşılanması gerekir:

- Azure portal alt kaynakları oluşturmak ve yönetmek için, kaynak grubu düzeyinde veya yukarıdaki **Data Factory katkıda bulunan** rolüne ait olmanız gerekir.
- PowerShell veya SDK ile alt öğe oluşturup yönetebilmek için kaynak düzeyinde **katkıda bulunan** rolü veya daha üst düzey bir rol yeterli olacaktır.

Kullanıcı rolü eklemeye ilişkin örnek yönergeler için [Rol ekleme](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) makalesine bakın.

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Data Factory Katılımcısı rolü](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Azure Data Factory için roller ve izinler](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage hesabı

Bu hızlı başlangıçta, genel amaçlı bir Azure Depolama hesabını (özel olarak BLOB depolama) hem *kaynak* hem de *hedef* veri deposu olarak kullanırsınız. Genel amaçlı bir Azure depolama hesabınız yoksa, bir [depolama hesabı](../articles/storage/common/storage-account-create.md) oluşturmak için bkz. hesap oluşturma. 

#### <a name="get-the-storage-account-name"></a>Depolama hesabı adını al

Bu hızlı başlangıç için Azure depolama hesabınızın adına ihtiyacınız vardır. Aşağıdaki yordam, depolama hesabınızın adını almak için gereken adımları sağlar: 

1. Bir Web tarayıcısında [Azure Portal](https://portal.azure.com) gidin ve Azure Kullanıcı adınızı ve parolanızı kullanarak oturum açın.
2. Azure Portal menüsünde **tüm hizmetler**' i ve ardından **depolama**  >  **depolama hesapları**' nı seçin. Ayrıca, herhangi bir sayfadan *depolama hesaplarını* arayabilir ve seçebilirsiniz.
3. **Depolama hesapları** sayfasında, depolama hesabınız için filtre uygulayın (gerekirse) ve ardından depolama hesabınızı seçin. 

Ayrıca, herhangi bir sayfadan *depolama hesaplarını* arayabilir ve seçebilirsiniz.

#### <a name="create-a-blob-container"></a>Blob kapsayıcısı oluşturma

Bu bölümde, Azure Blob depolama alanında **adftutorial** adlı bir blob kapsayıcısı oluşturursunuz.

1. Depolama hesabı sayfasında **genel bakış**  >  **kapsayıcıları**' nı seçin.
2. *\<Account name>*  -  **Kapsayıcılar** sayfasının araç çubuğunda **kapsayıcı**' yı seçin.
3. **Yeni kapsayıcı** iletişim kutusunda ad olarak **adftutorial** girin ve ardından **Tamam**’ı seçin. *\<Account name>*  -  **Kapsayıcılar** sayfası, kapsayıcılar listesinde **adföğreticisi** içerecek şekilde güncelleştirilir.

   :::image type="content" source="media/data-factory-quickstart-prerequisites/list-of-containers.png" alt-text="Kapsayıcılar listesi":::


#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Blob kapsayıcısı için bir giriş klasörü ve dosyası ekleyin

Bu bölümde, oluşturduğunuz kapsayıcıda **giriş** adlı bir klasör oluşturur ve ardından giriş klasörüne örnek bir dosya yüklersiniz. Başlamadan önce, **Not defteri** gibi bir metin düzenleyicisini açın ve aşağıdaki içeriğe sahip **emp.txt** adlı bir dosya oluşturun:

```emp.txt
John, Doe
Jane, Doe
```

Dosyayı **C:\ADFv2QuickStartPSH** klasörüne kaydedin. (Klasör zaten mevcut değilse, oluşturun.) Sonra Azure portal geri dönüp şu adımları izleyin:

1. Kaldığınız *\<Account name>*  -  **kapsayıcılar** sayfasında, güncelleştirilmiş kapsayıcı listesinden **adföğreticisi** ' ni seçin.

   1. Pencereyi kapattıysanız veya başka bir sayfaya bir sorun yaşıyorsanız, [Azure Portal](https://portal.azure.com) yeniden oturum açın.
   1. Azure Portal menüsünde **tüm hizmetler**' i ve ardından **depolama**  >  **depolama hesapları**' nı seçin. Ayrıca, herhangi bir sayfadan *depolama hesaplarını* arayabilir ve seçebilirsiniz.
   1. Depolama hesabınızı seçin ve ardından **kapsayıcılar**  >  **adföğreticisi**' ni seçin.

2. **Adföğreticisi** kapsayıcı sayfasının araç çubuğunda **karşıya yükle**' yi seçin.
3. **Blobu karşıya yükle** sayfasında, **dosyalar** kutusunu seçin ve ardından **emp.txt** dosyasına gidin ve dosyayı seçin.
4. **Gelişmiş** başlık ' ı genişletin. Sayfa artık gösterildiği gibi görüntülenir:

   :::image type="content" source="media/data-factory-quickstart-prerequisites/upload-blob-advanced.png" alt-text="Gelişmiş bağlantı seçeneğini belirleme":::

5. **Klasöre yükle** kutusuna **giriş**' i girin.
6. **Karşıya Yükle** düğmesini seçin. Listede **emp.txt** dosyasını ve karşıya yükleme durumunu görmeniz gerekir.
7. **BLOB yükle** sayfasını kapatmak için **Kapat** simgesini (bir **X**) seçin.

**Adföğreticisi** kapsayıcı sayfasını açık tutun. Bu hızlı başlangıcın sonundaki çıktıyı doğrulamak için bu sayfayı kullanırsınız.