---
title: Azure SQL Veritabanı ve Azure Synapse Analytics'ten veri paylaşma ve alma
description: Azure SQL veritabanı ve Azure SYNAPSE Analytics 'ten verileri paylaşmayı ve almayı öğrenin
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: ef8c1a50cd3568c6cec9bdb053b02e6e14741eb0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644673"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Veritabanı ve Azure Synapse Analytics'ten veri paylaşma ve alma

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure veri paylaşımı, anlık görüntü tabanlı paylaşım Azure SQL veritabanı ve Azure SYNAPSE Analytics 'i destekler. Bu makalede, bu kaynaklardan verilerin nasıl paylaşılacağını ve alınacağı açıklanmaktadır.

Azure veri paylaşımı, Azure SQL veritabanı ve Azure SYNAPSE Analytics (eski adıyla Azure SQL DW) ve Azure SYNAPSE Analytics (çalışma alanı) adanmış SQL havuzundan tablo ve görünümlerin paylaşılmasını destekler. Azure SYNAPSE Analytics (çalışma alanı) sunucusuz SQL havuzunun paylaşılması Şu anda desteklenmiyor. Veri tüketicileri, verileri Azure Data Lake Storage 2. veya Azure Blob depolama alanına CSV veya Parquet dosyası olarak kabul edip Azure SQL veritabanı ve Azure SYNAPSE Analytics 'i de tablolar olarak kabul edebilir.

Azure Data Lake Store Gen2 veya Azure Blob depolama alanına veri kabul edildiğinde, tam anlık görüntüler zaten varsa hedef dosyanın içeriğinin üzerine yazar.
Veriler SQL tablosuna alındığında ve hedef tablo henüz yoksa, Azure veri paylaşımında kaynak şeması ile SQL tablosu oluşturulur. Aynı ada sahip bir hedef tablo zaten varsa, en son tam anlık görüntüyle bırakılır ve üzerine yazılır. Artımlı anlık görüntüler şu anda desteklenmiyor.

## <a name="share-data"></a>Veri paylaşımı

### <a name="prerequisites-to-share-data"></a>Veri paylaşma önkoşulları

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Alıcının Azure oturum açma e-posta adresi (e-posta diğer adlarını kullanarak çalışmaz).
* Kaynak Azure veri deposu, veri paylaşma kaynağı oluşturmak için kullanacağınız sunucudan farklı bir Azure aboneliğinde yer alıyorsa, [Microsoft. DataShare kaynak sağlayıcısını](concepts-roles-permissions.md#resource-provider-registration) Azure veri deposunun bulunduğu abonelikte kaydedin. 

### <a name="prerequisites-for-sql-source"></a>SQL kaynağı önkoşulları
SQL kaynağından veri paylaşmaya yönelik önkoşulların listesi aşağıda verilmiştir. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL veritabanı veya Azure SYNAPSE Analytics 'ten (eski adıyla Azure SQL DW) paylaşım önkoşulları


Azure Active Directory kimlik doğrulaması kullanarak veri paylaşmak için bir önkoşul listesi aşağıda verilmiştir:

* Paylaşmak istediğiniz tablolar ve görünümler içeren bir Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW).
* *Microsoft. SQL/Servers/veritabanları/Write*'TA bulunan SQL Server 'da veritabanlarına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* SQL Server **Azure Active Directory Yöneticisi**
* Güvenlik duvarı erişimi SQL Server. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal, SQL Server 'a gidin. Sol gezinmede *güvenlik duvarları ve sanal ağlar ' ı* seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin vermek* için **Evet** ' i tıklatın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 

SQL kimlik doğrulaması kullanarak veri paylaşmak için önkoşul listesi aşağıda verilmiştir. Önkoşulları yapılandırmak için [adım adım demo](https://youtu.be/hIE-TjJD8Dc) ' i takip edebilirsiniz.

* Paylaşmak istediğiniz tablolar ve görünümler içeren bir Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW).
* *Microsoft. SQL/Servers/veritabanları/Write*'TA bulunan SQL Server 'da veritabanlarına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* Veritabanına erişmek için veri paylaşımının yönetilen kimliği izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal, SQL Server 'a gidin ve kendiniz **Azure Active Directory Yöneticisi** olarak ayarlayın.
    1. [Sorgu Düzenleyicisi](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) 'Ni kullanarak Azure SQL veritabanı/veri ambarı 'na bağlanın veya Azure Active Directory kimlik doğrulamasıyla SQL Server Management Studio. 
    1. Veri paylaşımının kaynak yönetilen kimliğini bir db_datareader olarak eklemek için aşağıdaki betiği yürütün. SQL Server kimlik doğrulaması değil Active Directory kullanarak bağlanmanız gerekir. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* veri paylaşma kaynağınızın adı olduğunu unutmayın. Henüz bir veri paylaşma kaynağı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.  

* **' Db_datareader '** erişimine sahip BIR Azure SQL veritabanı kullanıcısı, paylaşmak istediğiniz tablolar ve/veya görünümler üzerinde gezinmek ve bunları seçmek için. 

* Güvenlik duvarı erişimi SQL Server. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal, SQL Server 'a gidin. Sol gezinmede *güvenlik duvarları ve sanal ağlar ' ı* seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin vermek* için **Evet** ' i tıklatın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Azure SYNAPSE Analytics (çalışma alanı) SQL havuzundan paylaşım önkoşulları

* Bir Azure SYNAPSE Analytics (çalışma alanı) paylaşmak istediğiniz tablolarla ayrılmış bir SQL Havuzu. Görünüm paylaşımı Şu anda desteklenmiyor. Sunucusuz SQL havuzundan paylaşım Şu anda desteklenmiyor.
* *Microsoft. SYNAPSE/Workspaces/Sqlhavuzlar/Write* Içinde bulunan SYNAPSE çalışma alanındaki SQL havuzuna yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* SYNAPSE çalışma alanı SQL havuzuna erişmek için veri paylaşımının yönetilen kimliği izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede SQL Active Directory Yöneticisi ' ni seçin ve kendiniz **Azure Active Directory Yöneticisi** olarak ayarlayın.
    1. SYNAPSE Studio 'yu açın, sol gezinmede *Yönet* ' i seçin. Güvenlik altında *erişim denetimi* ' ni seçin. Kendinize **SQL Yöneticisi** veya **çalışma alanı yönetici** rolü atayın.
    1. SYNAPSE Studio 'da sol gezinmeden *Geliştir* ' i seçin. Veri paylaşımının kaynak yönetimli kimliğini bir db_datareader olarak eklemek için SQL havuzunda aşağıdaki betiği yürütün. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* veri paylaşma kaynağınızın adı olduğunu unutmayın. Henüz bir veri paylaşma kaynağı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.  

* SYNAPSE çalışma alanı güvenlik duvarı erişimi. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede *güvenlik duvarları* ' nı seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu çalışma alanına erişmesine Izin vermek* için **Açık** ' a tıklayın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 

### <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

### <a name="create-a-data-share-account"></a>Veri paylaşma hesabı oluşturma

Azure Kaynak grubunda bir Azure veri paylaşma kaynağı oluşturun.

1. Portalın sol üst köşesindeki menü düğmesini seçin ve ardından **kaynak oluştur** (+) seçeneğini belirleyin.

1. *Veri paylaşımında* arama yapın.

1. Veri paylaşma ' yı seçin ve **Oluştur**' u seçin.

1. Aşağıdaki bilgilerle Azure veri paylaşma kaynağınızın temel ayrıntılarını doldurun. 

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Abonelik | Aboneliğiniz | Veri paylaşma hesabınız için kullanmak istediğiniz Azure aboneliğini seçin.|
    | Kaynak grubu | *test-resource-group* | Mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. |
    | Konum | *Doğu ABD 2* | Veri paylaşma hesabınız için bir bölge seçin.
    | Name | *datashareaccount* | Veri paylaşma hesabınız için bir ad belirtin. |
    | | |

1. Veri paylaşma hesabınızı sağlamak için **gözden geçir + oluştur**' u seçin ve **Oluştur** ' a tıklayın. Yeni bir veri paylaşma hesabının sağlanması genellikle yaklaşık 2 dakika veya daha kısa sürer. 

1. Dağıtım tamamlandığında **Kaynağa Git**' i seçin.

### <a name="create-a-share"></a>Bir paylaşma oluşturun

1. Veri paylaşımında genel bakış sayfasına gidin.

    ![Verilerinizi paylaşma](./media/share-receive-data.png "Verilerinizi paylaşma") 

1. **Verilerinizi paylaşmayı Başlat**' ı seçin.

1. **Oluştur**’u seçin.   

1. Paylaşımınızın ayrıntılarını doldurun. Bir ad, paylaşma türü, içerik paylaşma açıklaması ve kullanım koşulları (isteğe bağlı) belirtin. 

    ![EnterShareDetails](./media/enter-share-details.png "Paylaşma ayrıntılarını girin") 

1. **Devam**’ı seçin.

1. Paylaşımınıza veri kümeleri eklemek için **veri kümesi Ekle**' yi seçin. 

    ![Paylaşımınıza veri kümeleri ekleme](./media/datasets.png "Veri kümeleri")

1. Eklemek istediğiniz veri kümesi türünü seçin. Önceki adımda seçtiğiniz paylaşma türüne (anlık görüntü veya yerinde) bağlı olarak farklı bir veri kümesi türleri listesi görürsünüz. 

    ![Adddataset 'ler](./media/add-datasets.png "Veri kümesi Ekle")    

1. SQL Server veya SYNAPSE çalışma alanınızı seçin. AAD kimlik doğrulaması kullanıyorsanız ve **veri paylaşımının yukarıdaki ' Kullanıcı Oluştur ' SQL betiğini benim yerime çalıştırmasına Izin ver** onay kutusu görüntülenirse, onay kutusunu işaretleyin. SQL kimlik doğrulaması kullanıyorsanız, kimlik bilgilerini sağlayın ve ekranda görünen betiği çalıştırmak için önkoşulların içindeki adımları izleyin. Bu, veri paylaşma kaynağına SQL DB 'nizden okuma izni verir. 

   Paylaşmak istediğiniz nesneye gitmek ve ' veri kümesi Ekle ' seçeneğini belirlemek için **İleri ' yi** seçin. Azure SQL veritabanı ve Azure SYNAPSE Analytics (eski adıyla Azure SQL DW) veya Azure SYNAPSE Analytics (çalışma alanı) adanmış SQL havuzundaki tabloları ve görünümleri seçebilirsiniz. 

    ![Selectdataset 'ler](./media/select-datasets-sql.png "Veri kümelerini seçin")    

1. Alıcılar sekmesinde, ' + Alıcı Ekle ' seçeneğini belirleyerek veri tüketicinizin e-posta adreslerini girin. E-posta adresinin alıcının Azure oturum açma e-postası olması gerekir.

    ![AddRecipients](./media/add-recipient.png "Alıcı ekleme") 

1. **Devam**’ı seçin.

1. Anlık görüntü paylaşma türü ' nu seçtiyseniz, veri tüketicisine verilerinizin güncelleştirmelerini sağlamak için anlık görüntü zamanlamasını yapılandırabilirsiniz. 

    ![EnableSnapshots](./media/enable-snapshots.png "Anlık görüntüleri etkinleştir") 

1. Bir başlangıç saati ve yinelenme aralığı seçin. 

1. **Devam**’ı seçin.

1. Gözden geçir + Oluştur sekmesinde paket Içeriklerinizi, ayarlarınızı, alıcılarını ve eşitleme ayarlarınızı gözden geçirin. **Oluştur**’u seçin.

Azure veri paylaşımınız artık oluşturulmuştur ve veri paylaşımınızın alıcısı artık davetinizi kabul etmeye hazırdır. 

## <a name="receive-data"></a>Veri Al

### <a name="prerequisites-to-receive-data"></a>Veri alma önkoşulları
Bir veri paylaşma davetini kabul etmeden önce, aşağıda listelenen birkaç Azure kaynağı sağlamalısınız. 

Bir veri paylaşma davetini kabul etmeden önce tüm önkoşulların tümünün tamamlandığından emin olun. 

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Veri paylaşma daveti: "Azure Data Share davetiyesi" başlıklı konuyla bir Microsoft Azure daveti **<yourdataprovider@domain.com>** .
* [Microsoft. DataShare kaynak sağlayıcısını](concepts-roles-permissions.md#resource-provider-registration) , bir veri paylaşma kaynağı ve hedef Azure veri mağazalarınızın bulunduğu Azure aboneliğini oluşturacağınız Azure aboneliğine kaydedin.

### <a name="prerequisites-for-target-storage-account"></a>Hedef depolama hesabı önkoşulları
Azure depolama 'ya veri almayı seçerseniz, önkoşul listesi aşağıda verilmiştir.

* Azure depolama hesabı: henüz yoksa bir [Azure depolama hesabı](../storage/common/storage-account-create.md)oluşturabilirsiniz. 
* *Microsoft. Storage/storageAccounts/Write* içinde bulunan depolama hesabına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır. 
* Veri paylaşımının yönetilen kimliğinin, *Microsoft. Authorization/role atamaları/Write*' de bulunan depolama hesabına rol atamasını ekleme izni. Bu izin **Sahip** rolünde vardır.  

### <a name="prerequisites-for-sql-target"></a>SQL hedefi önkoşulları
Azure SQL veritabanı 'na veri almayı seçerseniz Azure SYNAPSE Analytics, önkoşul listesidir. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e veri alma önkoşulları (eski adıyla Azure SQL DW)

SQL Server 'ın **Azure Active Directory Yöneticisi** olduğunuz bir SQL Server 'a veri almak için, önkoşul listesi aşağıda verilmiştir:

* Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW).
* *Microsoft. SQL/Servers/veritabanları/Write*'TA bulunan SQL Server 'da veritabanlarına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* Güvenlik duvarı erişimi SQL Server. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal, SQL Server 'a gidin. Sol gezinmede *güvenlik duvarları ve sanal ağlar ' ı* seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin vermek* için **Evet** ' i tıklatın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 
    
**Azure Active Directory Yöneticisi** olmayan bir SQL Server 'a veri almak için aşağıdaki önkoşulların bir listesi verilmiştir. Önkoşulları yapılandırmak için [adım adım demo](https://youtu.be/aeGISgK1xro) ' i takip edebilirsiniz.

* Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW).
* *Microsoft. SQL/Servers/veritabanları/Write*'TA bulunan SQL Server 'da veritabanlarına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır. 
* Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e erişmek için veri paylaşımının yönetilen kimliği izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal, SQL Server 'a gidin ve kendiniz **Azure Active Directory Yöneticisi** olarak ayarlayın.
    1. [Sorgu Düzenleyicisi](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) 'Ni kullanarak Azure SQL veritabanı/veri ambarı 'na bağlanın veya Azure Active Directory kimlik doğrulamasıyla SQL Server Management Studio. 
    1. Veri paylaşımının yönetilen kimliğini bir ' db_datareader, db_datawriter, db_ddladmin ' olarak eklemek için aşağıdaki betiği yürütün. SQL Server kimlik doğrulaması değil Active Directory kullanarak bağlanmanız gerekir. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        *<share_acc_name>* veri paylaşma kaynağınızın adı olduğunu unutmayın. Henüz bir veri paylaşma kaynağı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.         

* Güvenlik duvarı erişimi SQL Server. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal 'deki SQL Server 'da *güvenlik duvarları ve sanal ağlar* ' a gidin.
    1. *Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin vermek* için **Evet** ' i tıklatın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Azure SYNAPSE Analytics (çalışma alanı) SQL havuzu 'na veri alma önkoşulları

* Bir Azure SYNAPSE Analytics (çalışma alanı) adanmış SQL Havuzu. Sunucusuz SQL havuzunda verilerin alınması Şu anda desteklenmiyor.
* *Microsoft. SYNAPSE/Workspaces/Sqlhavuzlar/Write* Içinde bulunan SYNAPSE çalışma alanındaki SQL havuzuna yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* SYNAPSE çalışma alanı SQL havuzuna erişmek için veri paylaşımının yönetilen kimliği izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede SQL Active Directory Yöneticisi ' ni seçin ve kendiniz **Azure Active Directory Yöneticisi** olarak ayarlayın.
    1. SYNAPSE Studio 'yu açın, sol gezinmede *Yönet* ' i seçin. Güvenlik altında *erişim denetimi* ' ni seçin. Kendinize **SQL Yöneticisi** veya **çalışma alanı yönetici** rolü atayın.
    1. SYNAPSE Studio 'da sol gezinmeden *Geliştir* ' i seçin. Veri paylaşımının kaynak yönetimli kimliğini bir ' db_datareader, db_datawriter, db_ddladmin ' olarak eklemek için SQL havuzunda aşağıdaki betiği yürütün. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       *<share_acc_name>* veri paylaşma kaynağınızın adı olduğunu unutmayın. Henüz bir veri paylaşma kaynağı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.  

* SYNAPSE çalışma alanı güvenlik duvarı erişimi. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede *güvenlik duvarları* ' nı seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu çalışma alanına erişmesine Izin vermek* için **Açık** ' a tıklayın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 

### <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

### <a name="open-invitation"></a>Daveti aç

1. Daveti e-postayla veya doğrudan Azure portal açabilirsiniz. 

   E-postadaki daveti açmak için, veri sağlayıcınızdan bir davet için gelen kutunuza bakın. Davet, ' **den <yourdataprovider@domain.com> Azure veri paylaşma daveti** başlıklı Microsoft Azure. Azure 'da davetinizi görmek için **daveti görüntüle** ' ye tıklayın. 

   Daveti doğrudan Azure portal açmak için Azure portal **veri paylaşma davetlerini** arayın. Bu, sizi veri paylaşma davetlerinin listesine götürür.

   ![Davetlerin listesi](./media/invitations.png "Davetlerin listesi") 

1. Görüntülemek istediğiniz paylaşma seçeneğini belirleyin. 

### <a name="accept-invitation"></a>Daveti kabul et
1. **Kullanım koşulları** dahil olmak üzere tüm alanların incelendiğini doğrulayın. Kullanım koşullarını kabul ediyorsanız, kabul ettiğinizi belirtmek için kutuyu denetlemeniz gerekir. 

   ![Kullanım koşulları](./media/terms-of-use.png "Kullanım koşulları") 

1. *Hedef veri paylaşma hesabı* altında, veri paylaşımınızı dağıtmak istediğiniz aboneliği ve kaynak grubunu seçin. 

   **Veri** paylaşma hesabı alanı için, mevcut bir veri paylaşma hesabınız yoksa **Yeni oluştur** ' u seçin. Aksi takdirde, veri paylaşımınızı kabul etmek istediğiniz mevcut bir veri paylaşma hesabı seçin. 

   **Alınan paylaşma adı** alanı için, veriler tarafından belirtilen varsayılanı bırakabilir veya alınan paylaşıma yönelik yeni bir ad belirtebilirsiniz. 

   Kullanım koşullarını kabul ettikten ve alınan paylaşımınızı yönetmek için bir veri paylaşma hesabı belirttikten sonra **kabul et ve Yapılandır**' ı seçin. Bir paylaşma aboneliği oluşturulacak. 

   ![Kabul etme seçenekleri](./media/accept-options.png "Kabul etme seçenekleri") 

   Bu, sizi veri paylaşma hesabınızdaki alınan paylaşıma götürür. 

   Daveti kabul etmek istemiyorsanız *Reddet*' i seçin. 

### <a name="configure-received-share"></a>Alınan paylaşma yapılandırma
Verileri almak istediğiniz yeri yapılandırmak için aşağıdaki adımları izleyin.

1. **Veri kümeleri** sekmesini seçin. Hedef atamak istediğiniz veri kümesinin yanındaki kutuyu işaretleyin. Hedef veri deposu seçmek için **+ hedefe eşle** öğesini seçin. 

   ![Hedefle eşle](./media/dataset-map-target.png "Hedefle eşle") 

1. Verilerin içinde olmasını istediğiniz bir hedef veri deposu seçin. Hedef veri deposundaki tüm veri dosyaları veya tablolar aynı yol ve ada sahip olacak şekilde üzerine yazılır. SQL hedefine veri alıyorsanız ve **benim yerime veri paylaşımının "Kullanıcı oluşturma" SQL betiğini benim yerime çalıştırmasına Izin ver** onay kutusu görüntülenirse, onay kutusunu işaretleyin. Aksi takdirde, komut dosyasını çalıştırmak için Önkoşullar bölümündeki yönergeleri izleyin. Bu, hedef SQL DB 'nize veri paylaşma kaynağına yazma izni verir.

   ![Hedef depolama hesabı](./media/dataset-map-target-sql.png "Hedef veri deposu") 

1. Anlık görüntü tabanlı paylaşım için, veri sağlayıcısı verilere düzenli güncelleştirme sağlamak üzere bir anlık görüntü zamanlaması oluşturmışsa, anlık görüntü **zamanlama** sekmesini seçerek de anlık görüntü zamanlamasını etkinleştirebilirsiniz. Anlık görüntü zamanlamasının yanındaki kutuyu işaretleyin ve **+ Etkinleştir**' i seçin. Zamanlanan ilk anlık görüntünün, zamanlama zamanının bir dakikası içinde ve sonraki anlık görüntülerin zamanlanan sürenin saniye içinde başlatılacağını unutmayın.

   ![Anlık görüntü zamanlamasını etkinleştir](./media/enable-snapshot-schedule.png "Anlık görüntü zamanlamasını etkinleştir")

### <a name="trigger-a-snapshot"></a>Anlık görüntü tetikleyin
Bu adımlar yalnızca anlık görüntü tabanlı paylaşım için geçerlidir.

1. **Ayrıntılar** sekmesini ve ardından **tetikleyici anlık görüntüsünü** seçerek bir anlık görüntü tetikleyebilirsiniz. Burada, verilerinizin tam veya artımlı anlık görüntüsünü tetikleyebilirsiniz. Veri sağlayıcınızdan ilk kez veri alıyorsanız tam kopya ' ı seçin. SQL kaynakları için yalnızca tam anlık görüntü desteklenir. Bir anlık görüntü çalışırken, sonraki anlık görüntüler bir önceki işlem tamamlanana kadar başlatılmaz.

   ![Görüntüyü Tetikle](./media/trigger-snapshot.png "Görüntüyü Tetikle") 

1. Son çalıştırma durumu *başarılı* olduğunda, alınan verileri görüntülemek için hedef veri deposuna gidin. **Veri kümeleri**' ni seçin ve Hedef yoldaki bağlantıya tıklayın. 

   ![Tüketici veri kümeleri](./media/consumer-datasets.png "Tüketici veri kümesi eşleme") 

### <a name="view-history"></a>Geçmişi görüntüleme
Bu adım yalnızca anlık görüntü tabanlı paylaşım için geçerlidir. Anlık görüntülerinizin geçmişini görüntülemek için **Geçmiş** sekmesini seçin. Burada, son 30 gün için oluşturulan tüm anlık görüntülerin geçmişini bulacaksınız. 

## <a name="supported-data-types"></a>Desteklenen veri türleri
SQL kaynağından veri paylaştığınızda aşağıdaki eşleme, anlık görüntü işlemi sırasında verileri SQL Server veri türlerinden Azure veri paylaşımında ara veri türlerine kullanır. 

| SQL Server veri türü | Azure veri paylaşımının geçici veri türü |
|:--- |:--- |
| bigint |Int64 |
| ikili |Byte [] |
| bit |Boole |
| char |Dize, Char [] |
| date |DateTime |
| Tarih saat |DateTime |
| datetime2 |DateTime |
| Türünde |DateTimeOffset |
| Ondalık |Ondalık |
| FıLESTREAM özniteliği (varbinary (max)) |Byte [] |
| Float |Çift |
| image |Byte [] |
| int |Int32 |
| etmenize |Ondalık |
| nchar |Dize, Char [] |
| n |Dize, Char [] |
| sayısal |Ondalık |
| nvarchar |Dize, Char [] |
| real |Tek |
| rowversion |Byte [] |
| girişin |DateTime |
| smallint |Int16 |
| küçük para |Ondalık |
| sql_variant |Nesne |
| metin |Dize, Char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| ikili |Byte [] |
| varchar |Dize, Char [] |
| xml |Dize |

>[!NOTE]
> 1. Ondalık geçici türle eşlenen veri türleri için şu anda anlık görüntü, 28 ' ye kadar duyarlık destekler. 28 ' den büyük bir duyarlık gerektiren verileriniz varsa, bir dizeye dönüştürmeyi düşünün. 
> 1.  Azure SQL veritabanındaki verileri Azure SYNAPSE Analytics 'e paylaşıyorsanız, tüm veri türleri desteklenmez. Ayrıntılar için [ADANMıŞ SQL havuzundaki tablo veri türleri](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) bölümüne bakın. 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted veya dinamik veri maskeleme
Şu anda Azure veri paylaşımında Always Encrypted yapılandırılmış Azure SQL veritabanlarını desteklemez. 

Dinamik veri maskeleme içeren kaynak SQL tablolarında, veriler alıcı tarafında maskeli olarak görünür.

## <a name="sql-snapshot-performance"></a>SQL anlık görüntü performansı
SQL Snapshot performansı bir dizi faktörden etkilenir. Her zaman kendi performans testinizi uygulamanız önerilir. Aşağıda, performansı etkileyen bazı örnek faktörler verilmiştir.

* Kaynak ve hedef SQL veri deposunun donanım yapılandırması (ör. Vçekirdekler, bellek, DWU). 
* Kaynak ve hedef veri depolarına eşzamanlı erişim. Aynı SQL veri deposundan birden çok tablo ve görünüm paylaşıyorsanız veya aynı SQL veri deposuna birden fazla tablo ve görünüm alıyorsanız, performans etkilenecektir.   
* Kaynak ve hedef veri depolarının konumu. 

## <a name="troubleshoot-sql-snapshot-failure"></a>SQL Snapshot hatası sorunlarını giderme
Anlık görüntü hatasının en yaygın nedeni, veri paylaşımının kaynak veya hedef veri deposu için izni olmaması olabilir. Kaynak veya hedef Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW) için veri paylaşma izni vermek üzere, SQL veritabanına Azure Active Directory kimlik doğrulaması kullanarak bağlanırken, belirtilen SQL betiğini çalıştırmanız gerekir. Ek SQL anlık görüntü hatası sorunlarını gidermek için, bkz. [sorun giderme anlık görüntü hatası](data-share-troubleshoot.md#snapshots).

## <a name="next-steps"></a>Sonraki adımlar
Azure veri paylaşma hizmeti 'ni kullanarak SQL kaynaklarından verileri paylaşmayı ve almayı öğrendiniz. Diğer veri kaynaklarından paylaşım hakkında daha fazla bilgi edinmek için [desteklenen veri depolarına](supported-data-stores.md)devam edin.