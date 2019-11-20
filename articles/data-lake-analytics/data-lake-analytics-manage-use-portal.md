---
title: Azure Data Lake Analytics Azure portal kullanarak yönetin
description: Bu makalede, Data Lake Analytics hesaplarını, veri kaynaklarını, kullanıcıları & işlerini yönetmek için Azure portal nasıl kullanılacağı açıklanır.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839472"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Azure portal kullanarak Azure Data Lake Analytics yönetme
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Bu makalede, Azure portal kullanılarak Azure Data Lake Analytics hesaplarının, veri kaynaklarının, kullanıcıların ve işlerin nasıl yönetileceği açıklanmaktadır.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics hesaplarını yönetme

### <a name="create-an-account"></a>Hesap oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Kaynak oluştur** > **ıntelligence + Analytics** > **Data Lake Analytics**' e tıklayın.
3. Aşağıdaki öğeler için değerleri seçin: 
   1. **Ad**: Data Lake Analytics hesabının adı.
   2. **Abonelik**: hesap Için kullanılan Azure aboneliği.
   3. **Kaynak grubu**: hesabın oluşturulacağı Azure Kaynak grubu. 
   4. **Konum**: Data Lake Analytics hesabı için Azure veri merkezi. 
   5. **Data Lake Store**: Data Lake Analytics hesabı için kullanılacak varsayılan depo. Azure Data Lake Store hesabı ve Data Lake Analytics hesabı aynı konumda olmalıdır.
4. **Oluştur**'a tıklayın. 

### <a name="delete-a-data-lake-analytics-account"></a>Data Lake Analytics hesabı silme

Bir Data Lake Analytics hesabını silmeden önce varsayılan Data Lake Store hesabını silin.

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Sil**'e tıklayın.
3. Hesap adını yazın.
4. **Sil**'e tıklayın.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Veri kaynaklarını yönetme

Data Lake Analytics aşağıdaki veri kaynaklarını destekler:

* Data Lake Store
* Azure Storage

Veri kaynaklarına gitmek ve temel dosya yönetimi işlemlerini gerçekleştirmek için Veri Gezgini kullanabilirsiniz. 

### <a name="add-a-data-source"></a>Veri kaynağı ekleme

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Veri kaynakları**' na tıklayın.
3. **Veri kaynağı Ekle**' ye tıklayın.
    
   * Bir Data Lake Store hesabı eklemek için hesap adının ve hesabın sorgulanamayacak hesaba erişmeniz gerekir.
   * Azure Blob depolama alanı eklemek için depolama hesabına ve hesap anahtarına ihtiyacınız vardır. Bunları bulmak için portalda depolama hesabına gidin.

## <a name="set-up-firewall-rules"></a>Güvenlik duvarı kurallarını ayarlama

Ağ düzeyinde Data Lake Analytics hesabınıza erişimi daha fazla kilitlemek için Data Lake Analytics kullanabilirsiniz. Bir güvenlik duvarını etkinleştirebilir, bir IP adresi belirtebilir veya güvenilir istemcileriniz için bir IP adresi aralığı tanımlayabilirsiniz. Bu ölçüleri etkinleştirdikten sonra, yalnızca tanımlı aralıktaki IP adreslerine sahip istemciler mağazaya bağlanabilir.

Azure Data Factory veya VM 'Ler gibi diğer Azure Hizmetleri Data Lake Analytics hesabına bağlanırsa, **Azure hizmetlerinin açık olmasına Izin ver** ' **in açık olduğundan emin olun.** 

### <a name="set-up-a-firewall-rule"></a>Güvenlik duvarı kuralı ayarlama

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. Soldaki menüden **güvenlik duvarı**' na tıklayın.

## <a name="add-a-new-user"></a>Yeni Kullanıcı Ekle

Yeni Data Lake kullanıcılarını kolayca sağlamak için **Kullanıcı ekleme sihirbazını** kullanabilirsiniz.

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. Sol tarafta, **Başlarken**altında **Kullanıcı Ekleme Sihirbazı**' na tıklayın.
3. Bir kullanıcı seçin ve ardından **Seç**' e tıklayın.
4. Bir rol seçin ve ardından **Seç**' e tıklayın. Azure Data Lake kullanmak üzere yeni bir geliştirici ayarlamak için **Data Lake Analytics geliştirici** rolünü seçin.
5. U-SQL veritabanlarının erişim denetim listelerini (ACL 'Ler) seçin. Seçimlerinizden memnun olduğunuzda **Seç**' e tıklayın.
6. Dosyalar için ACL 'Leri seçin. Varsayılan depo için, "/" kök klasörü ve/sistem klasörü için ACL 'leri değiştirmeyin. **Seç**'e tıklayın.
7. Seçtiğiniz tüm değişiklikleri gözden geçirin ve ardından **Çalıştır**' a tıklayın.
8. Sihirbaz tamamlandığında **bitti**' ye tıklayın.

## <a name="manage-role-based-access-control"></a>Rol tabanlı Access Control yönetme

Diğer Azure hizmetleri gibi, kullanıcıların hizmetle nasıl etkileşime gireceğini denetlemek için rol tabanlı Access Control (RBAC) kullanabilirsiniz.

Standart RBAC rolleri aşağıdaki yeteneklere sahiptir:
* **Sahip**: işleri gönderebilir, işleri izleyebilir, herhangi bir kullanıcının işini iptal edebilir ve hesabı yapılandırabilir.
* **Katkıda bulunan**: işleri gönderebilir, işleri izleyebilir, herhangi bir kullanıcının işini iptal edebilir ve hesabı yapılandırabilir.
* **Okuyucu**: işleri izleyebilir.

U-SQL geliştiricilerinin Data Lake Analytics hizmetini kullanmasını sağlamak için Data Lake Analytics geliştirici rolünü kullanın. Data Lake Analytics geliştirici rolünü kullanarak şunları yapabilirsiniz:
* İşleri gönder.
* İş durumunu ve herhangi bir kullanıcı tarafından gönderilen işlerin ilerlemesini izleyin.
* Herhangi bir kullanıcı tarafından gönderilen işlerin U-SQL betiklerine bakın.
* Yalnızca kendi işlerinizi iptal edin.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Data Lake Analytics hesaba Kullanıcı veya güvenlik grupları ekleme

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Rol ataması eklemek** > **ERIŞIM denetimi (IAM)** seçeneğine tıklayın.
3. Bir rol seçin.
4. Bir kullanıcı ekleyin.
5. **Tamam** düğmesine tıklayın.

>[!NOTE]
>Bir kullanıcının veya güvenlik grubunun işleri göndermesi gerekiyorsa mağaza hesabında da izne ihtiyacı vardır. Daha fazla bilgi için bkz. [Data Lake Store 'de depolanan verileri güvenli hale getirme](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>İşleri yönetme

### <a name="submit-a-job"></a>İş gönder

1. Azure portal Data Lake Analytics hesabınıza gidin.

2. **Yeni iş**öğesine tıklayın. Her iş için aşağıdakileri yapılandırın:

    1. **Iş adı**: işin adı.
    2. **Öncelik**: düşük sayıların önceliği daha yüksektir. İki iş sıraya alınmışsa, daha düşük öncelikli bir değer önce çalışır.
    3. **Paralellik**: Bu iş için ayrılacak en fazla işlem işlemi sayısı.

3. **İşi Gönder**'e tıklayın.

### <a name="monitor-jobs"></a>İşleri izleme

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Tüm Işleri görüntüle**' ye tıklayın. Hesaptaki tüm etkin ve son tamamlanan işlerin bir listesi gösterilir.
3. İsteğe bağlı olarak, **zaman aralığı**, **Iş adı**ve **Yazar** değerlerine göre işleri bulmanıza yardımcı olması için **filtre** ' ye tıklayın. 

### <a name="monitoring-pipeline-jobs"></a>İşlem hattı işlerini izleme
Bir işlem hattının parçası olan işler, belirli bir senaryoyu gerçekleştirmek için genellikle sıralı olarak birlikte çalışır. Örneğin, müşteri öngörüleri için kullanımı Temizleme, çıkaran, dönüştüren bir işlem hattına sahip olabilirsiniz. İşlem hattı işleri, iş gönderildiğinde "ardışık düzen" özelliği kullanılarak tanımlanır. ADF v2 kullanılarak zamanlanan işler, bu özelliği otomatik olarak dolduracaktır. 

İşlem hatları parçası olan U-SQL işlerinin listesini görüntülemek için: 

1. Azure portal, Data Lake Analytics hesaplarınıza gidin.
2. **Iş öngörüleri**' ne tıklayın. "Tüm Işler" sekmesi, çalışan, sıraya alınmış ve sona ermiş işlerin bir listesini göstererek varsayılan olarak açılır.
3. İşlem **hattı işleri** sekmesine tıklayın. İşlem hattı işlerinin listesi, her bir işlem hattı için toplanan istatistiklerle birlikte gösterilir.

### <a name="monitoring-recurring-jobs"></a>Yinelenen işleri izleme
Yinelenen bir iş aynı iş mantığına sahip olmakla kalmaz, her çalıştırıldığında farklı giriş verilerini kullanır. İdeal olarak, yinelenen işler her zaman başarılı olur ve görece kararlı yürütme zamanına sahip olur; Bu davranışları izlemek, işin sağlıklı olduğundan emin olmaya yardımcı olur. Yinelenen işler, "yinelenme" özelliği kullanılarak tanımlanır. ADF v2 kullanılarak zamanlanan işler, bu özelliği otomatik olarak dolduracaktır.

Yinelenen U-SQL işlerinin bir listesini görüntülemek için: 

1. Azure portal, Data Lake Analytics hesaplarınıza gidin.
2. **Iş öngörüleri**' ne tıklayın. "Tüm Işler" sekmesi, çalışan, sıraya alınmış ve sona ermiş işlerin bir listesini göstererek varsayılan olarak açılır.
3. **Yinelenen işler** sekmesine tıklayın. Yinelenen işlerin bir listesi, her bir yinelenen iş için toplanan istatistiklerle birlikte gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Analytics genel bakış](data-lake-analytics-overview.md)
* [Azure PowerShell kullanarak Azure Data Lake Analytics yönetme](data-lake-analytics-manage-use-powershell.md)
* [İlkeleri kullanarak Azure Data Lake Analytics yönetme](data-lake-analytics-account-policies.md)
