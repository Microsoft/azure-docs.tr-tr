---
title: 'Hızlı başlangıç: iş yükü sınıflandırıcı Oluşturma-Portal'
description: Yüksek öneme sahip bir iş yükü Sınıflandırıcısı oluşturmak için Azure portal kullanın.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: 6f1997a80e66d1ca7928afc02acf92f376fc3376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534275"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak adanmış bir SQL havuzu iş yükü Sınıflandırıcısı oluşturma

Bu hızlı başlangıçta, bir iş yükü grubuna sorgu atamak için bir [iş yükü Sınıflandırıcısı](sql-data-warehouse-workload-classification.md) oluşturacaksınız.  Sınıflandırıcı, `ELTLogin` SQL kullanıcısının `DataLoads` iş yükü grubuna istek atayacaktır.   [Hızlı başlangıç: iş yükü yalıtımını yapılandırma](quickstart-configure-workload-isolation-portal.md) öğreticisini izleyin `DataLoads` .  Bu öğreticide, istekleri doğru şekilde sınıflandırmasına yardımcı olmak için WLM_LABEL seçeneği ile bir iş yükü Sınıflandırıcısı oluşturulur.  Sınıflandırıcı, `HIGH` Bu isteklere de [iş yükü önem derecesini](sql-data-warehouse-workload-importance.md) atayacaktır.


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.


## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

> [!NOTE]
> Azure SYNAPSE Analytics 'te adanmış bir SQL havuzu örneği oluşturmak, yeni bir faturalanabilir hizmetle sonuçlanabilir.  Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta, DENETIM VERITABANı izinlerine sahip olduğunuz adanmış bir SQL havuzu örneği zaten var. Bir tane oluşturmanız gerekiyorsa, **Mysampledatawarehouse** adlı özel bir SQL havuzu oluşturmak için [Oluştur ve Bağlan-Portal](create-data-warehouse-portal.md) ' ı kullanın.
<br><br>
Bir iş yükü grubu `DataLoads` var.  İş yükü grubunu oluşturmak için [hızlı başlangıç: iş yükü yalıtımı yapılandırma](quickstart-configure-workload-isolation-portal.md) öğreticisini inceleyin.
<br><br>
>[!IMPORTANT] 
>İş yükü yönetimini yapılandırmak için adanmış SQL havuzunuzun çevrimiçi olması gerekir. 


## <a name="create-a-login-for-eltlogin"></a>ELTLogin için oturum açma oluşturma

`master`IÇIN [oturum açma oluştur](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kullanarak veritabanında SQL Server kimlik doğrulaması oturumu oluşturun `ELTLogin` .

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Kullanıcı oluşturma ve izin verme

Oturum açma oluşturulduktan sonra, veritabanında bir kullanıcının oluşturulması gerekir.  Mysampledatawarehouse içinde SQL kullanıcısını oluşturmak için [Kullanıcı oluşturma](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ' yı kullanın `ELTRole` .   Bu öğretici sırasında sınıflandırmayı test edeceğiz, `ELTLogin` **Mysampledatawarehouse** için izin verin. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>İş yükü sınıflandırmasını yapılandırma
Sınıflandırma, istekleri bir dizi kurala göre iş yükü grubuna yönlendirmenize olanak tanır.  [Hızlı başlangıç: iş yükü yalıtımı yapılandırma](quickstart-configure-workload-isolation-portal.md) öğreticisini `DataLoads` iş yükü grubunu oluşturduk.  Artık sorguları iş yükü grubuna yönlendirmek için bir iş yükü Sınıflandırıcısı oluşturacaksınız `DataLoads` .


1.  **Mysampledatawarehouse** adanmış SQL havuzu sayfanıza gidin.
3.  **Iş yükü yönetimi**' ni seçin.

    ![Tıklama menüsü](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  İş yükü grubunun sağ tarafında bulunan **sınıflandırıcılar & ayarlar** ' ı seçin `DataLoads` .

    ![Oluştur’a tıklayın](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Sınıflandırıcılar sütunu altında  **Yapılandırılmadı** ' ı seçin.
6. **+ Sınıflandırıcı Ekle**' yi seçin.

    ![Ekle'ye tıklayın.](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  `ELTLoginDataLoads` **Ad** için girin.
8.  `ELTLogin` **Üye** için girin.
9.  `High` **İstek önemi** için seçin.  *Isteğe bağlı*, normal önem varsayılandır.
10. `fact_loads` **Etiket** için girin.
11. **Add (Ekle)** seçeneğini belirleyin.
12. **Kaydet**’i seçin.

    ![Yapılandırma ' ya tıklayın](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Doğrulama ve test sınıflandırması
Sınıflandırıcının varlığını doğrulamak için [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalog görünümünü denetleyin `ELTLoginDataLoads` .

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Sınıflandırıcı ayrıntılarını doğrulamak için [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalog görünümünü denetleyin.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Sınıflandırmayı test etmek için aşağıdaki deyimlerini çalıştırın.  Olarak bağlı olduğunuzdan ``ELTLogin`` ve ``Label`` sorgusunda kullanıldığından emin olun.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

İş yükü `CREATE TABLE` `DataLoads` sınıflandırıcısını kullanarak iş yükü grubuna sınıflandırılan ifadeyi doğrulayın `ELTLoginDataLoads` .
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

`ELTLoginDataLoads`Bu öğreticide oluşturulan iş yükü sınıflandırıcısını silmek için:

1. İş yükü grubunun sağ tarafında bulunan **1 sınıflandırıcıya** tıklayın `DataLoads` .

    ![Sil 'e tıklayın](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. **Sınıflandırıcılar**' ne tıklayın.
3. **`...`** `ELTLoginDataLoads` İş yükü sınıflandırıcıya sağ tarafındaki öğesine tıklayın.
4. **Sil**' e tıklayın.
5. **Kaydet**'e tıklayın.

    ![Kaydet’e tıklayın.](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Veri ambarı birimleri ve adanmış SQL havuzunuzdaki depolanan veriler için ücretlendiriliyorsunuz. Bu işlem ve depolama alanı kaynakları ayrı ayrı faturalandırılır.

- Verileri depolamada tutmak istiyorsanız, adanmış SQL havuzunuzu kullanmadığınız zaman, işlem duraklatabilirsiniz. İşlem duraklatıldığında yalnızca veri depolama alanı için ücret ödersiniz. Verilerle çalışmaya hazırsanız, işlem işlemini sürdürmeniz gerekir.
- Gelecekteki ücretleri kaldırmak istiyorsanız, adanmış SQL havuzunuzu silebilirsiniz.

Kaynakları temizlemek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın, adanmış SQL havuzunuzu seçin.

    ![Kaynakları temizleme](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. İşlem duraklatmak için **Duraklat** düğmesini seçin. Adanmış SQL havuzu duraklatıldığında, bir **Başlat** düğmesi görürsünüz.  İşlem işlemini sürdürmesini sağlamak için **Başlat**' ı seçin.

3. İşlem veya depolama için ücretlendirilmemek üzere adanmış SQL havuzunu kaldırmak için **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure portal izleme ölçümlerini kullanarak iş yükünüzü izleyin.  Ayrıntılar için bkz. [Iş yükü yönetimini yönetme ve izleme](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .
