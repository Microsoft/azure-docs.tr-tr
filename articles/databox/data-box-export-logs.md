---
title: Dışarı aktarma sırası için olayları Azure Data Box Heavy Azure Data Box izleme ve günlüğe kaydetme | Microsoft Docs
description: Azure Data Box çeşitli aşamalarda olayların nasıl izleneceğini ve günlüğe alınacağını açıklar ve Azure Data Box Heavy dışa aktarma sırası.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337517"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Azure Data Box için izleme ve olay günlüğü oluşturma ve Azure Data Box Heavy siparişleri dışarı aktarma

Bir Data Box veya Data Box Heavy dışarı aktarma sırası şu adımlardan geçer: sipariş, ayarlama, veri kopyalama, dönüş ve veri ernure. Siparişteki her adıma karşılık gelen, sıraya erişimi denetlemek, olayları denetlemek, siparişi izlemek ve oluşturulan çeşitli günlükleri yorumlamak için birden çok eylem gerçekleştirebilirsiniz.

Bu makalede, Data Box veya Data Box Heavy için dışarı aktarma emirlerini izlemek ve denetlemek için kullanılabilen çeşitli mekanizmalar veya araçların ayrıntıları açıklanmaktadır. Bu makaledeki bilgiler, Data Box ve Data Box Heavy için geçerlidir. Sonraki bölümlerde, Data Box yapılan tüm başvurular Data Box Heavy için de geçerlidir.

Aşağıdaki tabloda, Data Box dışa aktarma sırası adımlarının bir özeti ve her adım sırasında sırayı izlemek ve denetlemek için kullanılabilen araçlar gösterilmektedir.

| Data Box dışarı aktarma siparişi aşaması       | İzlenecek ve denetlenecek araç                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Sipariş oluşturma               | [Azure RBAC aracılığıyla sırada erişim denetimini ayarlama](#set-up-access-control-on-the-order) <br> [Ayrıntılı günlüğü sırayla etkinleştirin](#enable-verbose-log-in-the-order)                                                    |
| Sıra işlendi            | [Sıralamayı izleme](#track-the-order) <ul><li> Azure portalı </li><li> Kargo taşıyıcısı Web sitesi </li><li>E-posta bildirimleri</ul> |
| Cihazı ayarlama              | Cihaz kimlik bilgileri erişim oturum açmış [etkinlik günlükleri](#query-activity-logs-during-setup)              |
| Cihazdan veri kopyalama        | [Kopyalama günlüklerini gözden geçirme](#copy-log) <br> Verileri kopyalayabilmeniz için önce [ayrıntılı günlükleri gözden geçirin](#verbose-log)            |
| Cihazdan veri ernure   | Denetim günlükleri ve sıra geçmişi dahil [, gözetim günlüklerinin zincirini görüntüleme](#get-chain-of-custody-logs-after-data-erasure)                |


## <a name="set-up-access-control-on-the-order"></a>Sıraya göre erişim denetimi ayarlama

Sipariş ilk oluşturulduğunda, siparişinizi kimlerin erişebileceğini kontrol edebilirsiniz. Data Box sırasına erişimi denetlemek için çeşitli kapsamlardaki Azure rollerini ayarlayın. Bir Azure rolü, erişim türünü, okuma-yazma, salt okunurdur, okuma-yazma işlemlerini bir işlem alt kümesine belirler.

Azure Data Box hizmeti için tanımlanabilir iki rol şunlardır:

- **Data Box okuyucu** -kapsam tarafından tanımlanan bir sıraya (ler) salt okuma erişimi vardır. Yalnızca bir siparişin ayrıntılarını görüntüleyebilirler. Depolama hesaplarıyla ilgili diğer ayrıntılara erişemez veya adres gibi sipariş ayrıntılarını düzenleyebilir.
- **Katkıda bulunan Data Box** -yalnızca *bir depolama hesabına yazma erişimi varsa,* verileri belirli bir depolama hesabına aktarmak için bir sipariş oluşturulabilir. Bir depolama hesabına erişimi yoksa, verileri hesaba kopyalamak için bile Data Box bir sıra oluşturamazlar. Bu rol, depolama hesabı ile ilgili herhangi bir izin tanımlamaz ve depolama hesaplarına erişim verir.  

Erişimi bir siparişle kısıtlamak için şunları yapabilirsiniz:

- Bir rolü bir sıra düzeyinde atayın. Kullanıcı yalnızca, roller tarafından tanımlanan yalnızca bu Data Box sipariş ve başka hiçbir şey ile etkileşim kurmak üzere bu izinlere sahiptir.
- Kaynak grubu düzeyinde bir rol atama, kullanıcının bir kaynak grubundaki tüm Data Box emirlerine erişimi vardır.

Önerilen Azure RBAC kullanımı hakkında daha fazla bilgi için bkz. [Azure RBAC Için en iyi uygulamalar](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Ayrıntılı günlüğü sırayla etkinleştirin

Data Box için bir dışarı aktarma siparişi yerleştirirken, ayrıntılı günlük koleksiyonunu etkinleştirme seçeneğiniz vardır. Ayrıntılı günlüğü etkinleştirebileceğiniz sıra ekranı aşağıda verilmiştir:

![Dışarı aktarma seçeneğini belirleyin](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

**Ayrıntılı günlük Ekle** seçeneğini belirlediğinizde, verileri Azure depolama hesabınızdan kopyalarken ayrıntılı bir günlük dosyası oluşturulur. Bu günlük başarıyla aktarılmış tüm dosyaların bir listesini içerir.

Dışarı aktarma sırası hakkında daha fazla bilgi için bkz. [Data Box için dışarı aktarma siparişi oluşturma](data-box-deploy-export-ordered.md)

## <a name="track-the-order"></a>Siparişi izleme

Siparişinizi Azure portal ve sevkiyat taşıyıcısı Web sitesi aracılığıyla izleyebilirsiniz. Data Box sırayı istediğiniz zaman izlemek için aşağıdaki mekanizmalar bulunur:

- Cihazın Azure veri merkezinde veya şirket içinde olduğu sırayı izlemek için, Azure portal ' **e genel bakış > Data Box siparişiniz** sayfasına gidin.

    ![Sipariş durumunu görüntüleme ve izleme No](media/data-box-logs/overview-view-status-1.png)

- Cihaz aktarım sırasında sırayı izlemek için, bölgesel taşıyıcı Web sitesine gidin, örneğin, ABD 'deki UPS Web sitesi. Siparişinizin ilişkili izleme numarasını girin.
- Data Box, siparişin oluşturulduğu sırada belirtilen e-postalara göre her zaman e-posta bildirimleri de gönderir. Tüm Data Box sipariş durumlarının listesi için bkz. [Order Status görüntüleme durumu](data-box-portal-admin.md#view-order-status). Siparişle ilişkili bildirim ayarlarını değiştirmek için bkz. [bildirim ayrıntılarını düzenleme](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Kurulum sırasında sorgu etkinliği günlükleri

- Data Box, şirket içinde kilitli bir durumda ulaşır. Azure portal için kullanılabilir cihaz kimlik bilgilerini siparişiniz için kullanabilirsiniz.  

    Bir Data Box ayarlandığında, cihaz kimlik bilgilerine kimlerin eriştiğini bilmeniz gerekebilir. **Cihaz kimlik bilgileri** dikey penceresine kimin eriştiğini anlamak için etkinlik günlüklerini sorgulayabilirsiniz.  **Cihaz ayrıntıları > kimlik bilgileri** dikey penceresine erişimi içeren herhangi bir eylem etkinlik günlüklerine eylem olarak kaydedilir `ListCredentials` .

    ![Etkinlik günlüklerini sorgulama](media/data-box-logs/query-activity-log-1.png)

- Data Box her oturum, gerçek zamanlı olarak günlüğe kaydedilir. Ancak bu bilgiler, sipariş başarıyla tamamlandıktan sonra yalnızca [gözetim denetim günlüklerinin zincirinde](#chain-of-custody-audit-logs) kullanılabilir.

## <a name="view-logs-during-data-copy"></a>Veri kopyalama sırasında günlükleri görüntüleme

Data Box verileri kopyalayabilmeniz için, Data Box kopyalanmış veriler için *kopyalama günlüğünü* ve *ayrıntılı günlüğü* indirebilir ve gözden geçirebilirsiniz. Bu Günlükler, veriler Azure 'daki depolama hesabınızdan Data Box kopyalanırken oluşturulur. 

### <a name="copy-log"></a>Günlüğü Kopyala

Verileri Data Box kopyalamanız için, **Bağlan ve Kopyala** sayfasından kopyalama günlüğünü indirin.

Bir hata olmadığında ve Azure 'dan Data Box cihaza veri kopyalama sırasında tüm dosyalar kopyalanırsa, *Kopyalama günlüğü* ' nin örnek bir çıktısı aşağıda verilmiştir.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Burada, *kopyalama günlüğünde* hatalar olduğunda ve bazı dosyaların Azure 'dan kopyalanması başarısız olduğunda örnek bir çıktı verilmiştir.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Bu dosyaları dışarı aktarmak için aşağıdaki seçenekleri kullanabilirsiniz: 

- Kopyalanamayan dosyaları ağ üzerinden aktarabilirsiniz. 
- Veri boyutunuz kullanılabilir cihaz kapasitesinden büyükse kısmi kopyalama gerçekleşir ve kopyalanamayan tüm dosyalar bu günlükte listelenir. Bu günlüğü, yeni bir Data Box siparişi oluşturmak ve daha sonra bu dosyaları kopyalamak için giriş XML dosyası olarak kullanabilirsiniz.

### <a name="verbose-log"></a>Ayrıntılı günlük

*Ayrıntılı günlük*, Azure Depolama hesabından başarıyla dışarı aktarılmış tüm dosyaların bir listesini içerir. Günlük ayrıca dosya boyutu ve sağlama toplamı hesaplamasını da içerir.

Ayrıntılı günlük bilgileri aşağıdaki biçimde bulunur:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Ayrıntılı günlüğün örnek bir çıktısı aşağıda verilmiştir. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

Ayrıntılı Günlükler Azure Storage hesabına de kopyalanır. Varsayılan olarak, Günlükler adlı bir kapsayıcıya yazılır `copylog` . Günlükler aşağıdaki adlandırma kuralına göre saklanır:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Kopyalama günlüğü yolu Ayrıca portalın **genel bakış** dikey penceresinde de görüntülenir.

<!-- add a screenshot-->

Bu günlükleri, Azure 'dan kopyalanan dosyaların şirket içi sunucunuza kopyalanan verilerle eşleştiğini doğrulamak için kullanabilirsiniz. 

Ayrıntılı günlük dosyanızı kullanın:

- Gerçek adlara ve Data Box kopyalanan dosya sayısına karşı doğrulamak için.
- Dosyaların gerçek boyutlarına karşı doğrulamak için.
- *Crc64* sıfır olmayan bir dizeye karşılık geldiğini doğrulamak için. Azure 'dan dışarı aktarma sırasında Döngüsel artıklık denetimi (CRC) hesaplaması yapılır. Dışarı aktarma işleminden ve sonrasında veriler Data Box şirket içi sunucuya kopyalandıktan sonra karşılaştırılan CRCs 'Ler karşılaştırılabilir. CRC uyumsuzluğu, karşılık gelen dosyaların düzgün şekilde kopyalanıp ayarlanmadığını gösterir.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Veri eriyinden sonra gözetim günlüklerinin zincirini al

NIST SP 800-88 düzeltme 1 yönergelerine göre Data Box disklerden veriler silindikten sonra, gözetim günlüklerinin zinciri kullanılabilir. Bu Günlükler, gözetim denetim günlükleri zincirini ve sipariş geçmişini içerir. BOM veya manifest dosyaları da denetim günlükleriyle birlikte kopyalanır.

### <a name="chain-of-custody-audit-logs"></a>Gözetim denetim günlüklerinin Zinciri

Gözetim denetim günlüklerinin zinciri, Data Box paylaşımları açma ve bunlara erişme hakkında bilgiler içerir veya Azure veri merkezi 'nin dışında olduğunda Data Box Heavy. Bu Günlükler şurada bulunur: `storage-account/azuredatabox-chainofcustodylogs`

Data Box bir denetim günlüğü örneği aşağıda verilmiştir:

```output
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```

## <a name="download-order-history"></a>Sipariş geçmişi indirme

Sipariş geçmişi Azure portal kullanılabilir. Sıra tamamlanmadıysa ve cihaz temizleme (disklerden veri ernemi) tamamlandıktan sonra, cihaz sıraınızdan gidip **sipariş ayrıntıları**' na gidin. **Sipariş geçmişi indirme** seçeneği bulunur. Daha fazla bilgi için bkz. [yükleme sırası geçmişi](data-box-portal-admin.md#download-order-history).

Sipariş geçmişi boyunca kaydırırsanız şunu görürsünüz:

- Cihazınız için taşıyıcı izleme bilgileri.
- *SecureErase* etkinliğine sahip olaylar. Bu olaylar, diskteki verilerin eriyine karşılık gelir.
- Günlük bağlantılarını Data Box. *Denetim günlükleri*, *kopyalama günlükleri* ve *bom* dosyaları için yollar gösterilir.

Azure portal sipariş geçmişi günlüğü örneği aşağıda verilmiştir:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Sonraki adımlar

- [Data Box ve Data Box Heavy ilgili sorunları nasıl giderebileceğinizi](data-box-troubleshoot.md)öğrenin.
