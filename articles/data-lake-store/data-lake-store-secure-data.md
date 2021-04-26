---
title: Azure Data Lake Storage 1. 'de depolanan verilerin güvenliğini sağlama | Microsoft Docs
description: Grupları ve erişim denetimi listelerini kullanarak Azure Data Lake Storage 1. verileri güvenli hale getirme hakkında bilgi edinin
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: ac7666f4c4e68d24499f9c097dc9bd021d270355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370704"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage 1. Nesil'de depolanan verilerin güvenliğini sağlama
Azure Data Lake Storage 1. içindeki verilerin güvenliğini sağlamak, üç adımlı bir yaklaşımdır.  Hem Azure rol tabanlı erişim denetimi (Azure RBAC) hem de erişim denetim listeleri (ACL 'Ler), kullanıcılar ve güvenlik grupları için verilere erişimi tam olarak etkinleştirecek şekilde ayarlanmalıdır.

1. Azure Active Directory (Azure AD) içinde güvenlik grupları oluşturarak başlayın. Bu güvenlik grupları, Azure portal Azure rol tabanlı erişim denetimi (Azure RBAC) uygulamak için kullanılır. Daha fazla bilgi için bkz. [Azure RBAC](../role-based-access-control/role-assignments-portal.md).
2. Data Lake Storage 1. hesabına Azure AD güvenlik grupları atayın. Bu, portaldan veya API 'lerden yönetim işlemlerinden Data Lake Storage 1. hesabına erişimi denetler.
3. Azure AD güvenlik gruplarını Data Lake Storage 1. dosya sisteminde erişim denetim listeleri (ACL 'Ler) olarak atayın.
4. Ayrıca, Data Lake Storage 1. verilerine erişebilen istemciler için de bir IP adresi aralığı ayarlayabilirsiniz.

Bu makale, yukarıdaki görevleri gerçekleştirmek için Azure portal nasıl kullanılacağına ilişkin yönergeler sağlar. Data Lake Storage 1. hesap ve veri düzeyinde güvenliği nasıl uygulayan hakkında ayrıntılı bilgi için, bkz. [Azure Data Lake Storage 1. güvenlik](data-lake-store-security-overview.md). ACL 'Lerin Data Lake Storage 1. nasıl uygulandığı hakkında ayrıntılı bilgi için, bkz. [Data Lake Storage 1. Access Control genel bakış](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiye başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Azure Active Directory 'de güvenlik grupları oluşturma
Azure AD güvenlik grupları oluşturma ve gruba kullanıcı ekleme hakkında yönergeler için, bkz. [Azure Active Directory güvenlik gruplarını yönetme](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Azure portal kullanarak, hem kullanıcıları hem de diğer grupları Azure AD 'deki bir gruba ekleyebilirsiniz. Bununla birlikte, bir gruba hizmet sorumlusu eklemek için [Azure AD 'Nin PowerShell modülünü](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md)kullanın.
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Data Lake Storage 1. hesaplara Kullanıcı veya güvenlik grupları atama
Data Lake Storage 1. hesaplara Kullanıcı veya güvenlik grupları atadığınızda, Azure portal ve Azure Resource Manager API 'Lerini kullanarak hesaptaki yönetim işlemlerine erişimi kontrol edersiniz. 

1. Bir Data Lake Storage 1. hesabı açın. Sol bölmede **tüm kaynaklar**' a tıklayın ve ardından tüm kaynaklar dikey penceresinde, bir kullanıcı veya güvenlik grubu atamak istediğiniz hesap adına tıklayın.

2. Data Lake Storage 1. hesabı dikey penceresinde **Access Control (IAM)** seçeneğine tıklayın. Dikey pencere varsayılan olarak abonelik sahiplerini sahip olarak listeler.
   
    ![Azure Data Lake Storage 1. hesabına güvenlik grubu atama](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Azure Data Lake Storage 1. hesabına güvenlik grubu atama")

3. **Access Control (IAM)** dikey penceresinde **Ekle** ' ye tıklayarak **izin Ekle** dikey penceresini açın. **Izin Ekle** dikey penceresinde Kullanıcı/Grup Için bir **rol** seçin. Daha önce Azure Active Directory oluşturduğunuz güvenlik grubunu bulun ve seçin. Arama yapılacak çok sayıda kullanıcınız ve grubunuz varsa, Grup adını filtrelemek için **Seç** metin kutusunu kullanın. 
   
    ![Kullanıcı için bir rol ekleme](./media/data-lake-store-secure-data/adl.add.user.1.png "Kullanıcı için bir rol ekleme")
   
    **Sahip** ve **katkıda** bulunan rolü, Data Lake hesabındaki çeşitli yönetim işlevlerine erişim sağlar. Data Lake 'taki verilerle etkileşimde bulunan ancak hala hesap yönetimi bilgilerini görüntülemesi gereken kullanıcılar için, bunları **okuyucu** rolüne ekleyebilirsiniz. Bu rollerin kapsamı Data Lake Storage 1. hesabıyla ilgili yönetim işlemleriyle sınırlıdır.
   
    Veri işlemleri için, bireysel dosya sistemi izinleri kullanıcıların neler yapabileceğini tanımlar. Bu nedenle, okuyucu rolüne sahip bir Kullanıcı yalnızca hesapla ilişkili yönetim ayarlarını görüntüleyebilir, ancak bunlara atanan dosya sistemi izinlerine göre verileri okuyabilir ve yazabilir. Data Lake Storage 1. dosya sistemi izinleri [, güvenlik grubunu, Azure Data Lake Storage 1. dosya sistemine ACL 'ler olarak ata](#filepermissions)bölümünde açıklanmaktadır.

    > [!IMPORTANT]
    > Dosya sistemi erişimini yalnızca **sahip** rolü otomatik olarak etkinleştirilir. **Katkıda** bulunan, **okuyucu** ve diğer tüm roller, klasörlere ve dosyalara erişim düzeyini etkinleştirmek için ACL 'ler gerektirir.  **Sahip** rolü, ACL 'ler aracılığıyla geçersiz kılınamayan Süper Kullanıcı dosya ve klasör izinleri sağlar. Azure RBAC ilkelerinin veri erişimiyle nasıl eşlenme hakkında daha fazla bilgi için bkz. [Hesap yönetimi Için Azure RBAC](data-lake-store-security-overview.md#azure-rbac-for-account-management).

4. **Izin Ekle** dikey penceresinde listelenmeyen bir grup/kullanıcı eklemek istiyorsanız, bunları **Seç** metin kutusuna e-posta adresini yazarak ve ardından listeden seçerek davet edebilirsiniz.
   
    ![Güvenlik grubu Ekle](./media/data-lake-store-secure-data/adl.add.user.2.png "Güvenlik grubu Ekle")
   
5. **Kaydet**’e tıklayın. Güvenlik grubunun aşağıda gösterildiği gibi eklendiğini görmeniz gerekir.
   
    ![Güvenlik grubu eklendi](./media/data-lake-store-secure-data/adl.add.user.3.png "Güvenlik grubu eklendi")

6. Kullanıcı/güvenlik grubunuzun Data Lake Storage 1. hesabına erişimi artık vardır. Belirli kullanıcılara erişim sağlamak istiyorsanız, bunları güvenlik grubuna ekleyebilirsiniz. Benzer şekilde, bir kullanıcı için erişimi iptal etmek istiyorsanız onları güvenlik grubundan kaldırabilirsiniz. Ayrıca, bir hesaba birden fazla güvenlik grubu atayabilirsiniz. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Kullanıcıları veya güvenlik gruplarını Data Lake Storage 1. dosya sistemine ACL olarak atama
Data Lake Storage 1. dosya sistemine Kullanıcı/güvenlik grupları atayarak, Data Lake Storage 1. depolanan verilerde erişim denetimi ayarlarsınız.

1. Data Lake Storage 1. hesabı dikey penceresinde **Veri Gezgini**' e tıklayın.
   
    ![Veri Gezgini aracılığıyla verileri görüntüleme](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Veri Gezgini aracılığıyla verileri görüntüleme")
2. **Veri Gezgini** dikey PENCERESINDE, ACL 'yi yapılandırmak istediğiniz klasöre tıklayın ve ardından **erişim**' e tıklayın. ACL 'Leri bir dosyaya atamak için, önce dosyayı önizlemeniz ve ardından **dosya önizleme** dikey penceresinden **erişim** ' e tıklamanız gerekir.
   
    ![Data Lake Storage 1. dosya sisteminde ACL 'Leri ayarla](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake Storage 1. dosya sisteminde ACL 'Leri ayarla")
3. **Erişim** dikey penceresi, zaten köke atanmış olan sahipleri ve atanmış izinleri listeler. Ek erişim ACL 'Leri eklemek için **Ekle** simgesine tıklayın.
    > [!IMPORTANT]
    > Tek bir dosya için erişim izinlerinin ayarlanması, bu dosyaya bir Kullanıcı/Grup erişimi vermelidir. Dosyanın yolu atanan kullanıcı/grup için erişilebilir olmalıdır. Daha fazla bilgi ve örnek için bkz. [izinlerle Ilgili yaygın senaryolar](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Standart ve özel erişimi listeleyin](./media/data-lake-store-secure-data/adl.acl.2.png "Standart ve özel erişimi listeleyin")
   
   * **Sahipler** ve **Herkes** , okuma, yazma, yürütme (RWX) olarak üç farklı Kullanıcı sınıfına sahip olan UNIX stili erişim sağlar: sahip, Grup ve diğerleri.
   * **Atanan izinler** , dosyanın sahibi veya grubunun ötesinde belirli bir adlandırılmış Kullanıcı veya grup için izinleri ayarlamanıza olanak tanıyan POSIX ACL 'lerine karşılık gelir. 
     
     Daha fazla bilgi için bkz [..](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) ACL 'Lerin Data Lake Storage 1. nasıl uygulandığı hakkında daha fazla bilgi için, bkz. [Data Lake Storage 1. Access Control](data-lake-store-access-control.md).
4. **Izin ata** dikey penceresini açmak için **Ekle** simgesine tıklayın. Bu dikey pencerede **Kullanıcı veya Grup Seç**' e tıklayın ve ardından **Kullanıcı veya grup** dikey penceresinde, daha önce Azure Active Directory oluşturduğunuz güvenlik grubunu arayın. Arama yapmak için çok sayıda grubunuz varsa, Grup adını filtrelemek için üstteki metin kutusunu kullanın. Eklemek istediğiniz gruba tıklayın ve ardından **Seç**' e tıklayın.
   
    ![Grup Ekle](./media/data-lake-store-secure-data/adl.acl.3.png "Grup Ekle")
5. İzinleri **Seç**' e tıklayın, izinleri yinelemeli olarak uygulanıp uygulanmayacağı ve izinleri bir erişim ACL 'si, varsayılan ACL veya her ikisi olarak atamak isteyip istemediğiniz. **Tamam**'a tıklayın.
   
    ![İzinleri Seç seçeneği ile izin ata dikey penceresinin ekran görüntüsü ve Tamam seçeneği olarak adlandırılan izinleri seç dikey penceresi.](./media/data-lake-store-secure-data/adl.acl.4.png "Gruba izin atama")
   
    Data Lake Storage 1. izinler ve varsayılan/erişim ACL 'Leri hakkında daha fazla bilgi için bkz. [Data Lake Storage 1. Access Control](data-lake-store-access-control.md).
6. **Izinleri Seç** dikey penceresinde **Tamam** ' a tıkladıktan sonra, yeni eklenen grup ve ilişkili izinler artık **erişim** dikey penceresinde listelenecektir.
   
    ![Veri Mühendisliği seçeneği olarak adlandırılan erişim dikey penceresinin ekran görüntüsü.](./media/data-lake-store-secure-data/adl.acl.5.png "Gruba izin atama")
   
   > [!IMPORTANT]
   > Geçerli sürümde, **atanan izinler** altında en fazla 28 giriş olabilir. 28 ' den fazla kullanıcı eklemek istiyorsanız güvenlik grupları oluşturmanız, güvenlik gruplarına kullanıcı eklemeniz ve Data Lake Storage 1. hesap için bu güvenlik gruplarına erişim sağlamanız gerekir.
   > 
   > 
7. Gerekirse, Grup eklendikten sonra erişim izinlerini de değiştirebilirsiniz. Bu izni kaldırmak mı yoksa güvenlik grubuna atamak mı istediğinize bağlı olarak, her bir izin türünün onay kutusunu (okuma, yazma, yürütme) temizleyin veya seçin. Değişiklikleri kaydetmek için **Kaydet** ' e veya değişiklikleri geri almak için **at** ' a tıklayın.

## <a name="set-ip-address-range-for-data-access"></a>Veri erişimi için IP adresi aralığını ayarla
Data Lake Storage 1., ağ düzeyinde veri deponuza erişimi daha fazla kilitlemenizi sağlar. Güvenlik duvarını etkinleştirebilir, bir IP adresi belirtebilir veya güvenilen istemcileriniz için bir IP adresi aralığı tanımlayabilirsiniz. Etkinleştirildikten sonra yalnızca tanımlı aralıktaki IP adreslerine sahip istemciler mağazaya bağlanabilir.

![Güvenlik Duvarı ayarları ve IP erişimi](./media/data-lake-store-secure-data/firewall-ip-access.png "Güvenlik Duvarı ayarları ve IP adresi")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabının güvenlik gruplarını kaldırma
Data Lake Storage 1. hesaplarından güvenlik gruplarını kaldırdığınızda, yalnızca Azure portal ve Azure Resource Manager API 'Lerini kullanarak hesaptaki yönetim işlemlerine erişimi değiştirmiş olursunuz.  

Verilere erişim değiştirilmez ve erişim ACL 'Leri tarafından yönetilmeye devam edilir.  Bunun özel durumu, sahipler rolündeki kullanıcılar/gruplarıdır.  Sahipler rolünden kaldırılan kullanıcılar/gruplar artık süper kullanıcılar değildir ve erişimleri ACL ayarlarına geri döner. 

1. Data Lake Storage 1. hesabı dikey penceresinde **Access Control (IAM)** seçeneğine tıklayın. 
   
    ![Data Lake Storage 1. hesabına güvenlik grubu atama](./media/data-lake-store-secure-data/adl.select.user.icon.png "Data Lake Storage 1. hesabına güvenlik grubu atama")
2. **Access Control (IAM)** dikey penceresinde, kaldırmak istediğiniz güvenlik gruplarını tıklatın. **Kaldır**’a tıklayın.
   
    ![Güvenlik grubu kaldırıldı](./media/data-lake-store-secure-data/adl.remove.group.png "Güvenlik grubu kaldırıldı")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Güvenlik grubu ACL 'Lerini bir Data Lake Storage 1. dosya sisteminden kaldırma
Güvenlik grubu ACL 'Lerini bir Data Lake Storage 1. dosya sisteminden kaldırdığınızda, Data Lake Storage 1. hesabındaki verilere erişimi değiştirirsiniz.

1. Data Lake Storage 1. hesabı dikey penceresinde **Veri Gezgini**' e tıklayın.
   
    ![Data Lake Storage 1. hesapta dizin oluşturma](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Data Lake Storage 1. hesapta dizin oluşturma")
2. **Veri Gezgini** dikey PENCERESINDE, ACL 'yi kaldırmak istediğiniz klasöre tıklayın ve ardından **erişim**' e tıklayın. Bir dosyanın ACL 'Lerini kaldırmak için, önce dosyayı önizlemeniz ve ardından **dosya önizleme** dikey penceresinden **erişim** ' e tıklamanız gerekir. 
   
    ![Data Lake Storage 1. dosya sisteminde ACL 'Leri ayarla](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake Storage 1. dosya sisteminde ACL 'Leri ayarla")
3. **Erişim** dikey penceresinde, kaldırmak istediğiniz güvenlik grubuna tıklayın. **Erişim ayrıntıları** dikey penceresinde **Kaldır**' ı tıklatın.
   
    ![Veri Mühendisliği seçeneği olarak adlandırılan ve erişim ayrıntıları dikey penceresinde Kaldır seçeneği verilen erişim dikey penceresinin ekran görüntüsü.](./media/data-lake-store-secure-data/adl.remove.acl.png "Gruba izin atama")

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Data Lake Storage 1. genel bakış](data-lake-store-overview.md)
* [Azure depolama Bloblarından veri kopyalama Data Lake Storage 1.](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
* [PowerShell ile Data Lake Storage Gen1'i kullanmaya başlama](data-lake-store-get-started-powershell.md)
* [.NET SDK kullanarak Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage 1. için tanılama günlüklerine erişin](data-lake-store-diagnostic-logs.md)
