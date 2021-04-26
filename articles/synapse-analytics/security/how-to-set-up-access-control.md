---
title: SYNAPSE çalışma alanınız için erişim denetimi nasıl ayarlanır
description: Bu makalede, Azure rollerini, SYNAPSE rollerini, SQL izinlerini ve git izinlerini kullanarak bir Synapse çalışma alanına erişimi nasıl denetlediğiniz öğretir.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 97f9d0e0037090a8c058eb6e2393451d975e79c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103472266"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>SYNAPSE çalışma alanınız için erişim denetimi nasıl ayarlanır 

Bu makalede, Azure rollerini, SYNAPSE rollerini, SQL izinlerini ve git izinlerini kullanarak bir Synapse çalışma alanına erişimi nasıl denetlediğiniz öğretir.   

Bu kılavuzda, bir çalışma alanı ayarlayacaksınız ve birçok SYNAPSE projesi için uygun olan temel bir erişim denetimi sistemini yapılandıracaksınız.  Daha sonra daha ayrıntılı denetim için ihtiyacınız olan daha gelişmiş seçenekleri açıklar.  

SYNAPSE Access Control, kuruluşunuzda roller ve kişiler ile hizalanmış güvenlik grupları kullanılarak basitleştirilebilir.  Erişimi yönetmek için yalnızca güvenlik gruplarından Kullanıcı ekleyip kaldırmanız gerekir.

Bu yönergeyi başlamadan önce, SYNAPSE tarafından kullanılan erişim denetimi mekanizmalarını öğrenmek için [SYNAPSE Access Control Overview](./synapse-workspace-access-control-overview.md) ' ı okuyun.   

## <a name="access-control-mechanisms"></a>Erişim denetimi mekanizmaları

> [!NOTE]
> Bu kılavuzda gerçekleştirilen yaklaşım, birkaç güvenlik grubu oluşturmak ve ardından bu gruplara roller atayacaktır. Gruplar ayarlandıktan sonra, çalışma alanına erişimi denetlemek için yalnızca güvenlik grupları içindeki üyeliği yönetmeniz gerekir.

Bir Synapse çalışma alanının güvenliğini sağlamak için, aşağıdaki öğeleri yapılandırma düzeniyle karşılaşırsınız:

- Kullanıcıları benzer erişim gereksinimleriyle gruplandırmak için **güvenlik grupları**.
- **Azure rolleri**, SQL havuzlarını kimlerin oluşturup yönetebileceğini, Apache Spark havuzların ve tümleştirme çalışma zamanlarını ve ADLS 2. depolamaya erişmesini denetleyebilir.
- **SYNAPSE roller**, yayımlanmış kod yapıtlarına erişimi denetlemek için Apache Spark işlem kaynakları ve tümleştirme çalışma zamanları kullanın 
- SQL havuzlarına yönetim ve veri düzlemi erişimini denetlemek için **SQL izinleri**. 
- Çalışma alanı için git desteğini yapılandırırsanız kaynak denetimindeki kod yapılarına kimlerin erişebileceğini denetlemek için **Git izinleri** 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>SYNAPSE çalışma alanını güvenli hale getirme adımları

Bu belge yönergeleri basitleştirmek için standart adları kullanır. Bunları istediğiniz adlarla değiştirin.

|Ayar | Standart ad | Description |
| :------ | :-------------- | :---------- |
| **SYNAPSE çalışma alanı** | `workspace1` |  SYNAPSE çalışma alanının sahip olacağı ad. |
| **ADLSGEN2 hesabı** | `storage1` | Çalışma alanınız ile kullanılacak ADLS hesabı. |
| **Kapsayıcı** | `container1` | STG1 içinde, çalışma alanının varsayılan olarak kullanacağı kapsayıcı. |
| **Active Directory kiracısı** | `contoso` | Active Directory kiracı adı.|
||||

## <a name="step-1-set-up-security-groups"></a>1. Adım: güvenlik gruplarını ayarlama

>[!Note] 
>Önizleme sırasında, SYNAPSE **SYNAPSE SQL Yöneticisi** ve **SYNAPSE Apache Spark yönetici** rollerine eşlenmiş güvenlik grupları oluşturmanız önerilir.  Daha ayrıntılı SYNAPSE RBAC rollerinin ve kapsamlarının tanıtılmasıyla birlikte, çalışma alanınıza erişimi denetlemek için bu yeni özellikleri kullanmanız önerilir.  Bu yeni roller ve kapsamlar, daha fazla yapılandırma esnekliği sağlar ve geliştiricilerin analiz uygulamaları oluştururken genellikle bir SQL ve Spark karışımı kullanmasını ve tüm çalışma alanı yerine belirli kaynaklara erişim verilmesi gerekebilir. SYNAPSE RBAC hakkında [daha fazla bilgi edinin](./synapse-workspace-synapse-rbac.md) .

Çalışma alanınız için aşağıdaki güvenlik gruplarını oluşturun:

- **`workspace1_SynapseAdministrators`**, çalışma alanı üzerinde tamamen denetim gerektiren kullanıcılar için.  Kendinizi en azından bu güvenlik grubuna ekleyin.
- **`workspace1_SynapseContributors`**, hizmette kod geliştirme, hata ayıklama ve yayımlama ihtiyacı olan geliştiriciler için.   
- **`workspace1_SynapseComputeOperators`**, Apache Spark havuzlarını ve tümleştirme çalışma zamanlarını yönetmesi ve izlemesi gereken kullanıcılar için.
- **`workspace1_SynapseCredentialUsers`**, çalışma alanı MSI (yönetilen hizmet kimliği) kimlik bilgisi ve iptal işlem hattı çalıştırmalarını kullanarak düzenleme işlem hatlarını hata ayıklamasına ve çalıştırmaya ihtiyaç duyan kullanıcılar için.   

Bu gruplara kısa süre içinde SYNAPSE rolleri atayacaksınız.  

Ayrıca şu güvenlik grubunu oluştur: 
- **`workspace1_SQLAdmins`**, çalışma alanındaki SQL havuzları içinde SQL Active Directory yönetici yetkilisi olması gereken kullanıcılar için Grup. 

`workspace1_SQLAdmins`Bu grup, SQL HAVUZLARıNDA SQL izinlerini oluşturduğunuz sırada yapılandırdığınızda kullanılacaktır. 

Temel bir kurulum için, bu beş grup yeterlidir. Daha sonra, daha fazla özel erişim gerektiren kullanıcıları işlemek veya kullanıcılara yalnızca belirli kaynaklara erişim izni vermek için güvenlik grupları ekleyebilirsiniz.

> [!NOTE]
>- [Bu makalede](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)bir güvenlik grubu oluşturmayı öğrenin.
>- [Bu makaledeki](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)başka bir güvenlik grubundan bir güvenlik grubu eklemeyi öğrenin.

>[!Tip]
>Bireysel SYNAPSE kullanıcıları, hangi rollerin verildiğini belirleyen grup üyeliklerini görüntülemek için Azure portal Azure Active Directory kullanabilir.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>2. Adım: ADLS 2. depolama hesabınızı hazırlama

Bir Synapse çalışma alanı, için varsayılan bir depolama kapsayıcısı kullanır:
  - Spark tabloları için yedekleme verileri dosyalarını depolama
  - Spark işleri için yürütme günlükleri
  - Yüklemeyi seçtiğiniz kitaplıkları yönetme

Depolama bilgileriniz hakkında aşağıdaki bilgileri tanımla:

- Çalışma alanınız için kullanılacak ADLS 2. hesabı. Bu belge onu çağırır `storage1` . `storage1` , çalışma alanınız için "birincil" depolama hesabı olarak değerlendirilir.
- `workspace1`SYNAPSE çalışma alanınızın içindeki kapsayıcı varsayılan olarak kullanılacak. Bu belge onu çağırır `container1` . 

- Azure portal kullanarak, aşağıdaki Azure rollerini `container1` güvenlik gruplarına atayın 

  - **Depolama Blobu veri katılımcısı** rolünü öğesine ata`workspace1_SynapseAdmins` 
  - **Depolama Blobu veri katılımcısı** rolünü öğesine ata`workspace1_SynapseContributors`
  - **Depolama Blobu veri katılımcısı** rolünü öğesine ata`workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3. Adım: SYNAPSE çalışma alanınızı oluşturma ve yapılandırma

Azure portal, bir Synapse çalışma alanı oluşturun:

- Aboneliğinizi seçin
- Azure **Owner** rolüne sahip olduğunuz bir kaynak grubu seçin veya oluşturun.
- Çalışma alanını adlandırın `workspace1`
- `storage1`Depolama hesabı için seçin
- `container1`"FileSystem" olarak kullanılmakta olan kapsayıcı için seçin.
- WS1 'i SYNAPSE Studio 'da aç
-   >  **Access Control** Yönet ' e gidin ve *çalışma alanı kapsamındaki* SYNAPSE rollerini aşağıdaki gibi güvenlik gruplarına atayın:
  - **SYNAPSE yönetici** rolünü ata`workspace1_SynapseAdministrators` 
  - **SYNAPSE katkıda bulunan** rolünü ata`workspace1_SynapseContributors` 
  - **SYNAPSE Işlem işletmeni** rolünü ata`workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>4. Adım: çalışma alanına MSI erişimini varsayılan depolama kapsayıcısına verme

İşlem hatlarını çalıştırmak ve sistem görevlerini gerçekleştirmek için, SYNAPSE, çalışma alanı yönetilen hizmet kimliğinin (MSI) `container1` varsayılan ADLS 2. hesapta erişimi olması gerekir.

- Azure portalını açın
- Depolama hesabını bulun, `storage1` ve ardından `container1`
- **Access Control (IAM)** kullanarak, **Depolama Blobu VERI katılımcısı** rolünün MSI çalışma alanına atandığından emin olun
  - Atanmamıştır, atayın.
  - MSI, çalışma alanıyla aynı ada sahiptir. Bu makalede, olacaktır `workspace1` .

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>5. Adım: SYNAPSE yöneticilerine Azure katkıda bulunan rolünü çalışma alanında verme 

SQL havuzları, Apache Spark havuzları ve tümleştirme çalışma zamanları oluşturmak için, kullanıcıların çalışma alanında en az Azure katılımcısı rolü olması gerekir. Katılımcı rolü de bu kullanıcıların, duraklatma ve ölçekleme dahil olmak üzere kaynakları yönetmesine olanak tanır. SQL havuzları, Apache Spark havuzlar ve tümleştirme çalışma zamanları oluşturmak için Azure portal veya SYNAPSE Studio kullanıyorsanız, kaynak grubu düzeyinde Azure katılımcısı rolüne ihtiyacınız vardır. 

- Azure portalını açın
- Çalışma alanını bulun, `workspace1`
- Üzerinde **Azure katkıda** bulunan rolünü `workspace1` uygulamasına atayın `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>6. Adım: SQL Active Directory yönetici rolü atama

Çalışma alanı Oluşturucusu, çalışma alanı için otomatik olarak SQL Active Directory yöneticisi olarak ayarlanır.  Bu rol yalnızca tek bir kullanıcı veya gruba izin verebilir. Bu adımda, çalışma alanındaki SQL Active Directory Yöneticisini `workspace1_SQLAdmins` güvenlik grubuna atarsınız.  Bu rolü atamak, bu gruba, çalışma alanındaki tüm SQL havuzlarına ve veritabanlarına yüksek düzeyde ayrıcalıklı yönetici erişimi sağlar.   

- Azure portalını açın
- `workspace1` sayfasına gidin
- **Ayarlar** altında, **SQL Active Directory Yöneticisi** ' ni seçin.
- **Yönetici ayarla** ve Seç ' i seçin **`workspace1_SQLAdmins`**

>[!Note]
>6. adım isteğe bağlıdır.  `workspace1_SQLAdmins`Gruba daha az ayrıcalıklı bir rol vermeyi tercih edebilirsiniz. `db_owner`Veya başka SQL rolleri atamak için, her SQL veritabanında betikleri çalıştırmanız gerekir. 

## <a name="step-7-grant-access-to-sql-pools"></a>7. Adım: SQL havuzlarına erişim Izni verme

Varsayılan olarak, SYNAPSE yönetici rolüne atanan tüm kullanıcılara SQL `db_owner` rolü sunucusuz SQL havuzunda, ' yerleşik ' ve tüm veritabanlarında de atanır.

Diğer kullanıcılar ve MSI çalışma alanı için SQL havuzlarının erişimi, SQL izinleri kullanılarak denetlenir.  SQL izinlerinin atanması, her SQL veritabanının oluşturulduktan sonra SQL betikleri 'nin çalıştırılmasını gerektirir.  Bu komut dosyalarını çalıştırmanızı gerektiren üç durum vardır:
1. Diğer kullanıcılara sunucusuz SQL havuzuna, ' yerleşik ' ve bu veritabanlarına erişim verme
2. Adanmış havuz veritabanlarına Kullanıcı erişimi verme
3. SQL havuzu erişiminin başarıyla çalışmasını gerektiren işlem hatlarını etkinleştirmek için bir SQL havuzu veritabanına çalışma alanı MSI erişimi verme.

Örnek SQL betikleri aşağıda verilmiştir.

Adanmış bir SQL havuzu veritabanına erişim izni vermek için betikler, çalışma alanı oluşturan veya grubun herhangi bir üyesi tarafından çalıştırılabilir `workspace1_SQLAdmins` .  

' Yerleşik ' sunucusuz SQL havuzuna erişim vermek için betikler, grubun veya grubun herhangi bir üyesi tarafından çalıştırılabilir `workspace1_SQLAdmins`  `workspace1_SynapseAdministrators` . 

> [!TIP]
> Aşağıdaki adımlar, bir kullanıcıyı çalışma alanı düzeyinde bir sysadmin rolü atayabileceğiniz bölüm [kapsamlı izin](#workspace-scoped-permission) dışında **tüm SQL veritabanlarına** Kullanıcı erişimi vermek için çalıştırılmalıdır.

### <a name="step-71-serverless-sql-pool-built-in"></a>Adım 7,1: sunucusuz SQL havuzu, yerleşik

Bu bölümde, bir kullanıcıya belirli bir veritabanına veya ' yerleşik ' sunucusuz SQL havuzundaki tüm veritabanlarına erişim izni verme işlemlerinin nasıl yapılacağını gösteren komut dosyası örnekleri vardır.

> [!NOTE]
> Betik örneklerinde, *diğer adı* , erişim izni verilen kullanıcı veya grubun diğer adıyla ve kullandığınız şirket etki alanı ile *etki alanı* ile değiştirin.

#### <a name="database-scoped-permission"></a>Veritabanı kapsamlı izin

Bir kullanıcıya **tek** bir SUNUCUSUZ SQL veritabanına erişim vermek için bu örnekteki adımları izleyin:

1. OTURUM açma oluştur

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Kullanıcı Oluştur

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Belirtilen rolün üyelerine Kullanıcı Ekle

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Çalışma alanı kapsamlı izin

Çalışma alanındaki **Tüm** SUNUCUSUZ SQL havuzlarına tam erişim vermek için bu örnekteki betiği kullanın:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>Adım 7,2: adanmış SQL havuzları

**Tek** BIR adanmış SQL havuzu veritabanına erişim vermek Için, SYNAPSE SQL betik Düzenleyicisi ' nde bu adımları izleyin:

1. Hedef veritabanında aşağıdaki komutu çalıştırarak kullanıcıyı veritabanında oluşturun, açılan listeye *Bağlan* ' ı kullanarak seçilidir:

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Kullanıcıya veritabanına erişmek için bir rol verin:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* ve *db_datawriter* , *db_owner* izin verilmesi istenmiyorsa okuma/yazma izinleri için çalışabilir.
> Spark kullanıcısının doğrudan Spark 'tan veya bir SQL havuzundan okuması ve yazması için *db_owner* izin gerekir.

Kullanıcıları oluşturduktan sonra, sunucusuz SQL havuzunun depolama hesabını sorgulayabilecek olduğunu doğrulamak için sorguları çalıştırın.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>Adım 7,3: SYNAPSE işlem hattı çalıştırmaları için SQL Access Control

### <a name="workspace-managed-identity"></a>Çalışma alanı yönetilen kimliği

> [!IMPORTANT]
> Bir SQL havuzuna başvuran veri kümelerini veya etkinlikleri içeren işlem hatlarını başarıyla çalıştırmak için, çalışma alanı kimliğine SQL havuzuna erişim verilmesi gerekir.

Çalışma alanı yönetilen sistem kimliğinin SQL havuzu veritabanında işlem hatlarını çalıştırmasına izin vermek için her bir SQL havuzunda aşağıdaki komutları çalıştırın:  

>[!note]
>Aşağıdaki betiklerin, adanmış bir SQL havuzu veritabanı için DatabaseName, havuz adıyla aynıdır.  Sunucusuz SQL havuzundaki ' yerleşik ' veritabanı için, DatabaseName veritabanının adıdır.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

Bu izin, aynı SQL havuzunda aşağıdaki betiği çalıştırılarak kaldırılabilir:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>8. Adım: güvenlik gruplarına kullanıcı ekleme

Erişim denetimi sisteminiz için başlangıç yapılandırması tamamlanmıştır.

Erişimi yönetmek için, ayarlamış olduğunuz güvenlik gruplarına kullanıcı ekleyip çıkarabilirsiniz.  Kullanıcıları SYNAPSE rollerine el ile atayabilseniz de, bunu yaparsanız, izinleri tutarlı bir şekilde yapılandırmaz. Bunun yerine, yalnızca güvenlik gruplarına kullanıcı ekleyin veya bunları kaldırın.

## <a name="step-9-network-security"></a>9. Adım: ağ güvenliği

Çalışma alanınızı güvenli hale getirmeye yönelik son bir adım olarak, aşağıdakileri kullanarak ağ erişimini güvenli hale getirin:
- [Çalışma alanı güvenlik duvarı](./synapse-workspace-ip-firewall.md)
- [Yönetilen sanal ağ](./synapse-workspace-managed-vnet.md) 
- [Özel uç noktalar](./synapse-workspace-managed-private-endpoints.md)
- [Özel Bağlantı](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>10. Adım: tamamlama

Çalışma alanınız artık tam olarak yapılandırılmış ve güvenli hale getirildi.

## <a name="supporting-more-advanced-scenarios"></a>Daha gelişmiş senaryolar destekleme

Bu kılavuz, temel bir erişim denetimi sistemi ayarlamaya odaklanır. Ek güvenlik grupları oluşturarak ve bu gruplara daha fazla özel kapsamlar üzerinde daha ayrıntılı roller atayarak daha gelişmiş senaryolar destekleyebilirsiniz. Aşağıdaki durumları göz önünde bulundurun:

CI/CD dahil daha gelişmiş geliştirme senaryoları için çalışma alanı için **Git desteğini etkinleştirin** .  Git modundayken git izinleri bir kullanıcının çalışma dallarına değişiklikleri yapıp kullanamayacağını tespit eder.  Hizmete yayımlama yalnızca işbirliği dalından gerçekleşir.  Çalışan bir dalda güncelleştirme geliştirme ve hata ayıklama ihtiyacı olan ancak canlı hizmette değişiklik yayınlaması gerekmeyen geliştiriciler için bir güvenlik grubu oluşturmayı düşünün.

Belirli kaynaklara **Geliştirici erişimini kısıtlayın** .  Yalnızca belirli kaynaklara erişmesi gereken geliştiriciler için ek daha ayrıntılı güvenlik grupları oluşturun.  Bu grupları belirli Spark havuzları, tümleştirme çalışma zamanları veya kimlik bilgileri kapsamındaki uygun SYNAPSE rollerine atayın.

**Operatörlerin kod yapılarına erişmesini engelleyin**.  SYNAPSE işlem kaynaklarının işletimsel durumunu izlemesi ve günlüğe bakmak, ancak koda erişmesi veya hizmette güncelleştirmeleri yayımlamak zorunda olmayan operatörler için güvenlik grupları oluşturun. Bu grupları, belirli Spark havuzları ve tümleştirme çalışma zamanları kapsamındaki Işlem Işletmeni rolünü atayın.  

## <a name="next-steps"></a>Sonraki adımlar

[SYNAPSE RBAC rol atamalarının nasıl yönetileceğini](./how-to-manage-synapse-rbac-role-assignments.md) öğrenin [SYNAPSE çalışma alanı](../quickstart-create-workspace.md) oluşturma
