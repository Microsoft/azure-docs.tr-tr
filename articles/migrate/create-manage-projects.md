---
title: Projeleri oluşturma ve yönetme
description: Azure geçişi 'nde projeleri bulun, oluşturun, yönetin ve silin.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714856"
---
# <a name="create-and-manage-projects"></a>Projeleri oluşturma ve yönetme

Bu makalede, [projeleri](migrate-services-overview.md)oluşturma, yönetme ve silme işlemlerinin nasıl yapılacağı açıklanır. 

Klasik Azure geçişi, Şubat 2024 ' de devre dışı bırakılıyor. Şubat 2024 ' den sonra Azure geçişi 'nin klasik sürümü artık desteklenmeyecektir ve klasik projedeki envanter meta verileri silinir. Klasik projeler kullanıyorsanız, bu projeleri silin ve yeni bir proje oluşturmak için adımları izleyin. Klasik projeleri veya bileşenleri Azure geçişi 'ne yükseltemezsiniz. Oluşturma işlemini başlatmak için önce [SSS](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) 'yi görüntüleyin.

Proje, değerlendirmekte veya geçiş yaptığınız ortamdan toplanan bulma, değerlendirme ve geçiş meta verilerini depolamak için kullanılır. Bir projede keşfedilen varlıkları izleyebilir, değerlendirmeler oluşturabilir ve geçişleri Azure 'da düzenleyebilirsiniz.  

## <a name="verify-permissions"></a>İzinleri doğrulama

Proje oluşturmak için doğru izinlere sahip olup olmadığınızı kontrol edin:

1. Azure portal, ilgili aboneliği açın ve **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntüle ' yi seçin. *Katkıda bulunan* veya *sahip* izinlerinizin olması gerekir. 


## <a name="create-a-project-for-the-first-time"></a>İlk kez bir proje oluşturun

Azure aboneliğinde yeni bir proje ayarlayın.

1. Azure portal *Azure geçişi*' ni arayın.
2. **Hizmetler**' de **Azure geçişi**' ni seçin.
3. **Genel Bakış** bölümünde **Sunucuları değerlendir ve geçir**’i seçin.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Sunucuları değerlendirmek ve geçirmek için genel bakış seçeneği":::

4. **Sunucular** bölümünde **proje oluştur**' u seçin.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Proje oluşturmaya başlamak için düğme":::

5. **Proje oluştur**' da Azure aboneliğini ve kaynak grubunu seçin. Yoksa, bir kaynak grubu oluşturun.
6. **Proje ayrıntıları**' nda projeyi oluşturmak istediğiniz proje adını ve coğrafi konumu belirtin.
    - Coğrafya yalnızca şirket içi sunuculardan toplanan meta verileri depolamak için kullanılır. Geçiş için herhangi bir hedef bölge seçebilirsiniz. 
    - [Kamu](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government)için desteklenen coğrafi lıkları gözden geçirin. 


    > [!Note]
    > Özel uç nokta bağlantısına sahip bir Azure geçişi projesi oluşturmak için **Gelişmiş** yapılandırma bölümünü kullanın. [Daha fazla bilgi edinin](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. **Oluştur**’u seçin.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Proje ayarlarının giriş sayfası":::


Projenin dağıtılması için birkaç dakika bekleyin.

## <a name="create-a-project-in-a-specific-region"></a>Belirli bir bölgede proje oluşturma

Portalda, içinde projeyi oluşturmak istediğiniz coğrafi seçimi seçebilirsiniz. Projeyi oluşturmak için aşağıdaki API komutunu kullanarak belirli bir Azure bölgesi içinde proje oluşturmak istiyorsanız.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Ek projeler oluşturma

Zaten bir projeniz varsa ve ek bir proje oluşturmak istiyorsanız, aşağıdakileri yapın:  

1. Azure [genel portalı](https://portal.azure.com) veya [Azure Kamu](https://portal.azure.us)'da **Azure geçişi**' ni arayın.
2. Azure 'a Pano geçirme > **sunucularında** sağ üst köşedeki **Değiştir** ' i seçin.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Projeyi Değiştir":::

3. Yeni bir proje oluşturmak için **buraya tıklayın ' ı** seçin.


## <a name="find-a-project"></a>Proje bulma

Projeyi aşağıdaki gibi bulun:

1. [Azure Portal](https://portal.azure.com) *Azure geçişi*' ni arayın.
2. Azure 'da Pano geçirme > **sunucularında** sağ üst köşedeki **Değiştir** ' i seçin.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Mevcut bir projeye geç":::

3. Uygun aboneliği ve projeyi seçin.


### <a name="find-a-classic-project"></a>Klasik proje bulma

Projeyi Azure geçişi 'nin [önceki sürümünde](migrate-services-overview.md#azure-migrate-versions) oluşturduysanız, aşağıdaki gibi bulun:

1. [Azure Portal](https://portal.azure.com) *Azure geçişi*' ni arayın.
2. Azure geçişi panosunda, önceki sürümde bir proje oluşturduysanız, eski projelere başvuran bir başlık görüntülenir. Başlığı seçin.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Mevcut projelere erişin":::

3. Eski projelerin listesini gözden geçirin.


## <a name="delete-a-project"></a>Projeyi silme

Aşağıdaki gibi silin:

1. Projenin oluşturulduğu Azure kaynak grubunu açın.
2. Kaynak grubu sayfasında **gizli türleri göster**' i seçin.
3. Silmek istediğiniz projeyi ve ilişkili kaynaklarını seçin.
    - Kaynak türü **Microsoft. Migrate/migrateprojects**.
    - Kaynak grubu proje tarafından özel olarak kullanılıyorsa, tüm kaynak grubunu silebilirsiniz.

Şunlara dikkat edin:

- Sildiğinizde, hem proje hem de bulunan sunucularla ilgili meta veriler silinir.
- Azure geçişi 'nin eski sürümünü kullanıyorsanız, projenin oluşturulduğu Azure kaynak grubunu açın. Silmek istediğiniz projeyi (kaynak türü **geçiş projesi**) seçin.
- Bir Azure Log Analytics çalışma alanıyla bağımlılık Analizi kullanıyorsanız:
    - Sunucu değerlendirmesi aracına bir Log Analytics çalışma alanı eklediyseniz, çalışma alanı otomatik olarak silinmez. Aynı Log Analytics çalışma alanı birden çok senaryo için kullanılabilir.
    - Log Analytics çalışma alanını silmek istiyorsanız, bunu el ile yapın.
- Proje silme işlemi geri alınamaz. Silinen nesneler kurtarılamaz.

### <a name="delete-a-workspace-manually"></a>Çalışma alanını el ile silme

1. Projeye bağlı Log Analytics çalışma alanına gidin.

    - Projeyi silmediyse, **Essentials**  >  **Sunucu değerlendirmesi**' nde çalışma alanının bağlantısını bulabilirsiniz.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA çalışma alanı":::
       
    - Projeyi zaten sildiyseniz, Azure portal sol bölmesinde **kaynak grupları** ' nı seçin ve çalışma alanını bulun.
       
2. Çalışma alanını silmek için [yönergeleri izleyin](../azure-monitor/logs/delete-workspace.md) .

## <a name="next-steps"></a>Sonraki adımlar

Projelere [değerlendirme](how-to-assess.md) veya [geçiş](how-to-migrate.md) araçları ekleyin.