---
title: İzleme ve günlüğe kaydetme Azure Data Box, Azure Data Box Heavy olayları | Microsoft Docs
description: Azure Data Box ve Azure Data Box Heavy siparişinizin çeşitli aşamalarda olayların nasıl izleneceğini ve günlüğe alınacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 72e1d3b0ad72b1e68b88eb0550cbe839ade9d929
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535177"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box ve Azure Data Box Heavy için izleme ve olay günlüğü

Bir Data Box veya Data Box Heavy sırası şu adımlardan geçer: sipariş, ayarlama, veri kopyalama, döndürme, Azure 'a yükleme ve doğrulama ve veri Erimi. Siparişteki her adıma karşılık gelen, sıraya erişimi denetlemek, olayları denetlemek, siparişi izlemek ve oluşturulan çeşitli günlükleri yorumlamak için birden çok eylem gerçekleştirebilirsiniz.

Aşağıdaki tabloda Data Box veya Data Box Heavy sipariş adımlarının bir özeti ve her adım sırasında sırayı izlemek ve denetlemek için kullanılabilen araçlar gösterilmektedir.

| Data Box sipariş aşaması       | İzlenecek ve denetlenecek araç                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Sipariş oluştur               | [RBAC aracılığıyla sırada erişim denetimini ayarlama](#set-up-access-control-on-the-order)                                                    |
| Sıra işlendi            | [Sıralamayı izleme](#track-the-order) <ul><li> Azure portal </li><li> Kargo taşıyıcısı Web sitesi </li><li>E-posta bildirimleri</ul> |
| Cihazı ayarlama              | Cihaz kimlik bilgileri erişim oturum açmış [etkinlik günlükleri](#query-activity-logs-during-setup)                                              |
| Cihaza veri kopyalama        | Veri kopyası için [ *Error. xml* dosyalarını görüntüle](#view-error-log-during-data-copy)                                                             |
| Göndermeye Hazırlama            | Cihazdaki [bom dosyalarını](#inspect-bom-during-prepare-to-ship) veya bildirim dosyalarını inceleyin                                      |
| Azure 'a veri yükleme       | Azure veri merkezinde karşıya veri yükleme sırasında oluşan hataları [kopyalama günlüklerini gözden geçirme](#review-copy-log-during-upload-to-azure)                         |
| Cihazdan veri ernure   | Denetim günlükleri ve sıra geçmişi dahil [, gözetim günlüklerinin zincirini görüntüleme](#get-chain-of-custody-logs-after-data-erasure)                |

Bu makalede, Data Box veya Data Box Heavy sırasını izlemek ve denetlemek için kullanılabilen çeşitli mekanizmalar veya araçların ayrıntıları açıklanmaktadır. Bu makaledeki bilgiler, Data Box ve Data Box Heavy için geçerlidir. Sonraki bölümlerde, Data Box yapılan tüm başvurular Data Box Heavy için de geçerlidir.

## <a name="set-up-access-control-on-the-order"></a>Sıraya göre erişim denetimi ayarlama

Sipariş ilk oluşturulduğunda, siparişinizi kimlerin erişebileceğini kontrol edebilirsiniz. Data Box sırasına erişimi denetlemek için çeşitli kapsamlardaki rol tabanlı Access Control (RBAC) rolleri ayarlayın. RBAC rolü, erişim türünü, okuma-yazma, salt okunurdur, okuma-yazma işlemlerini bir işlem alt kümesine belirler.

Azure Data Box hizmeti için tanımlanabilir iki rol şunlardır:

- **Data Box okuyucu** -kapsam tarafından tanımlanan bir sıraya (ler) salt okuma erişimi vardır. Yalnızca bir siparişin ayrıntılarını görüntüleyebilirler. Depolama hesaplarıyla ilgili diğer ayrıntılara erişemez veya adres gibi sipariş ayrıntılarını düzenleyebilir.
- **Katkıda bulunan Data Box** -yalnızca *bir depolama hesabına yazma erişimi varsa,* verileri belirli bir depolama hesabına aktarmak için bir sipariş oluşturulabilir. Bir depolama hesabına erişimi yoksa, verileri hesaba kopyalamak için bile Data Box bir sıra oluşturamazlar. Bu rol, depolama hesabı ile ilgili herhangi bir izin tanımlamaz ve depolama hesaplarına erişim verir.  

Erişimi bir siparişle kısıtlamak için şunları yapabilirsiniz:

- Bir rolü bir sıra düzeyinde atayın. Kullanıcı yalnızca, roller tarafından tanımlanan yalnızca bu Data Box sipariş ve başka hiçbir şey ile etkileşim kurmak üzere bu izinlere sahiptir.
- Kaynak grubu düzeyinde bir rol atama, kullanıcının bir kaynak grubundaki tüm Data Box emirlerine erişimi vardır.

Önerilen RBAC kullanımı hakkında daha fazla bilgi için bkz. [RBAC Için en iyi uygulamalar](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Siparişi izleme

Siparişinizi Azure portal ve sevkiyat taşıyıcısı Web sitesi aracılığıyla izleyebilirsiniz. Data Box sırayı istediğiniz zaman izlemek için aşağıdaki mekanizmalar bulunur:

- Cihazın Azure veri merkezinde veya şirket içinde olduğu sırayı izlemek için, Azure portal ' **e genel bakış > Data Box siparişiniz** sayfasına gidin.

    ![Sipariş durumunu görüntüleme ve izleme No](media/data-box-logs/overview-view-status-1.png)

- Cihaz aktarım sırasında sırayı izlemek için, bölgesel taşıyıcı Web sitesine gidin, örneğin, ABD 'deki UPS Web sitesi. Siparişinizin ilişkili izleme numarasını girin.
- Data Box, siparişin oluşturulduğu sırada belirtilen e-postalara göre her zaman e-posta bildirimleri de gönderir. Tüm Data Box sipariş durumlarının listesi için bkz. [Order Status görüntüleme durumu](data-box-portal-admin.md#view-order-status). Siparişle ilişkili bildirim ayarlarını değiştirmek için bkz. [bildirim ayrıntılarını düzenleme](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Kurulum sırasında sorgu etkinliği günlükleri

- Data Box, şirket içinde kilitli bir durumda ulaşır. Azure portal için kullanılabilir cihaz kimlik bilgilerini siparişiniz için kullanabilirsiniz.  

    Bir Data Box ayarlandığında, cihaz kimlik bilgilerine kimlerin eriştiğini bilmeniz gerekebilir. **Cihaz kimlik bilgileri** dikey penceresine kimin eriştiğini anlamak için etkinlik günlüklerini sorgulayabilirsiniz.  **Cihaz ayrıntıları > kimlik bilgileri** dikey penceresine erişimi içeren herhangi bir eylem etkinlik günlüklerine eylem olarak `ListCredentials` kaydedilir.

    ![Etkinlik günlüklerini sorgulama](media/data-box-logs/query-activity-log-1.png)

- Data Box her oturum, gerçek zamanlı olarak günlüğe kaydedilir. Ancak, bu bilgiler yalnızca, sipariş başarıyla tamamlandıktan sonra [Denetim günlüklerinde](#audit-logs) kullanılabilir.

## <a name="view-error-log-during-data-copy"></a>Veri kopyalama sırasında hata günlüğünü görüntüle

Data Box veya Data Box Heavy veri kopyalama sırasında, kopyalandığı verilerle ilgili herhangi bir sorun varsa bir hata dosyası oluşturulur.

### <a name="errorxml-file"></a>Error. xml dosyası

Kopyalama işlerinin hatasız bitdiğinizden emin olun. Kopyalama işlemi sırasında hatalar varsa, **Bağlan ve Kopyala** sayfasından günlükleri indirin.

- 512 bayt olmayan bir dosyayı Data Box bir yönetilen disk klasörüne kopyaladıysanız, dosya hazırlama depolama hesabınıza Sayfa Blobu olarak yüklenmeyecektir. Günlüklerde bir hata görürsünüz. Dosyayı kaldırın ve 512 bayt hizalı bir dosyayı kopyalayın.
- Bir VHDX veya bir Dinamik VHD veya bir fark kayıt VHD 'SI (Bu dosyalar desteklenmiyorsa) kopyaladıysanız günlüklerde bir hata görürsünüz.

Yönetilen disklere kopyalarken farklı hatalar için *Error. xml* örneği aşağıda verilmiştir.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Sayfa bloblarına kopyalanırken farklı hatalar için *Error. xml* örneği aşağıda verilmiştir.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Blok bloblarına kopyalanırken farklı hatalar için *Error. xml* örneği aşağıda verilmiştir.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Azure dosyalarına kopyalanırken farklı hatalar için *Error. xml* dosyasının bir örneği aşağıda verilmiştir.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

Yukarıdaki her bir durumda, sonraki adıma geçmeden önce hataları çözün. SMB veya NFS protokolleri aracılığıyla Data Box veri kopyalama sırasında alınan hatalar hakkında daha fazla bilgi için, [sorun giderme Data Box ve Data Box Heavy sorunları](data-box-troubleshoot.md)bölümüne gidin. REST aracılığıyla Data Box veri kopyalama sırasında alınan hatalar hakkında bilgi için, [BLOB depolama sorunlarını Data Box sorun giderme](data-box-troubleshoot-rest.md)bölümüne gidin.

## <a name="inspect-bom-during-prepare-to-ship"></a>Sevk hazırlığı sırasında ürün reçetesini İncele

Gönderim hazırlığı sırasında, ürün reçeteleri (BOM) veya bildirim dosyası olarak bilinen dosyaların listesi oluşturulur.

- Gerçek adlara ve Data Box kopyalanan dosya sayısına karşı doğrulamak için bu dosyayı kullanın.
- Dosyaların gerçek boyutlarına karşı doğrulamak için bu dosyayı kullanın.
- *Crc64* sıfır olmayan bir dizeye karşılık geldiğini doğrulayın. <!--A null value for crc64 indicates that there was a reparse point error)-->

Gönderim hazırlığı sırasında alınan hatalar hakkında daha fazla bilgi için [Data Box sorun giderme ve Data Box Heavy sorunları](data-box-troubleshoot.md)bölümüne gidin.

### <a name="bom-or-manifest-file"></a>Ürün reçetesi veya bildirim dosyası

BOM veya manifest dosyası, Data Box cihazına kopyalanan tüm dosyaların listesini içerir. BOM dosyası dosya adlarına ve buna karşılık gelen boyutlara ve sağlama toplamına sahiptir. Blok Blobları, sayfa Blobları, Azure dosyaları, REST API 'Leri aracılığıyla kopyalama için ve Data Box yönetilen disklere kopyalama için ayrı bir BOM dosyası oluşturulur. Sevk hazırlığı sırasında, cihazın yerel Web kullanıcı arabiriminden BOM dosyalarını indirebilirsiniz.

Bu dosyalar Ayrıca Data Box cihazda bulunur ve Azure veri merkezinde ilişkili depolama hesabına yüklenir.

### <a name="bom-file-format"></a>BOM dosyası biçimi

Ürün reçetesi veya bildirim dosyası aşağıdaki genel biçime sahiptir:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Bu, veriler Data Box Blok Blobu paylaşımında kopyalandığında oluşturulan bir bildirimin örneğidir.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Ürün reçetesi veya bildirim dosyaları da Azure depolama hesabına kopyalanır. Azure 'a yüklenen dosyaların Data Box kopyalanan verilerle eşleştiğini doğrulamak için BOM veya manifest dosyalarını kullanabilirsiniz.

## <a name="review-copy-log-during-upload-to-azure"></a>Azure 'a yükleme sırasında kopyalama günlüğü 'nü gözden geçirme

Azure 'a veri yükleme sırasında bir kopyalama günlüğü oluşturulur.

### <a name="copy-log"></a>Günlüğü Kopyala

İşlenen her sıra için, Data Box hizmeti ilişkili depolama hesabında kopyalama günlüğü oluşturur. Kopyalama günlüğü, karşıya yüklenen toplam dosya sayısını ve veri kopyalama sırasında hatalı giden dosya sayısını Azure depolama hesabınıza Data Box.

Azure 'a yükleme sırasında Döngüsel artıklık denetimi (CRC) hesaplaması yapılır. Veri kopyalama işleminden sonra ve veri karşıya yüklemeden sonra CRCs 'Ler karşılaştırılır. CRC uyumsuzluğu, karşılık gelen dosyaların karşıya yüklenemediğini belirtir.

Varsayılan olarak, Günlükler adlı `copylog`bir kapsayıcıya yazılır. Günlükler aşağıdaki adlandırma kuralına göre saklanır:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Kopyalama günlüğü yolu Ayrıca portalın **genel bakış** dikey penceresinde de görüntülenir.

![Tamamlandığında genel bakış dikey penceresine günlük kopyalama yolu](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Karşıya yükleme başarıyla tamamlandı 

Aşağıdaki örnek, başarıyla tamamlanan Data Box karşıya yükleme için bir kopyalama günlüğünün Genel biçimini açıklar:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Karşıya yükleme hatalarla tamamlandı 

Azure 'a yükleme, hatalarla da tamamlanabilir.

![Hatalar ile tamamlandığında genel bakış dikey penceresine günlük kopyalama yolu](media/data-box-logs/copy-log-path-2.png)

Karşıya yüklemenin hatalarla tamamlandığı bir kopyalama günlüğü örneği aşağıda verilmiştir:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Karşıya yükleme uyarılarla tamamlandı

Verileriniz Azure adlandırma kurallarına uymayan kapsayıcı/BLOB/dosya adları içeriyorsa ve Azure 'a karşıya yükleme, verileri Azure 'a yüklemek üzere değiştirilseydi, Azure 'a yükleme işlemi uyarılarla tamamlanır.

![Uyarılar ile tamamlandığında genel bakış dikey penceresine günlük kopyalama yolu](media/data-box-logs/copy-log-path-3.png)

Azure 'a veri yükleme sırasında Azure adlandırma kurallarıyla uyumlu olmayan kapsayıcıların yeniden adlandırılmadığı bir kopya günlüğü örneği aşağıda verilmiştir.

Kapsayıcıların yeni benzersiz adları biçimindedir `DataBox-GUID` ve kapsayıcının verileri yeni yeniden adlandırılmış kapsayıcıya konur. Kopyalama günlüğü, kapsayıcının eski ve yeni kapsayıcı adını belirtir.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Azure 'a veri yükleme sırasında, Blobların veya Azure adlandırma kurallarıyla uyumlu olmayan dosyaların yeniden adlandırıldığını gösteren bir kopyalama günlüğü örneği aşağıda verilmiştir. Yeni blob veya dosya adları, kapsayıcıya göreli yolun SHA256 özetine dönüştürülür ve hedef türüne göre yola yüklenir. Hedef blok Blobları, sayfa Blobları veya Azure dosyaları olabilir.

, `copylog` Eski ve yeni blob ya da dosya adını ve Azure 'daki yolu belirtir.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Veri eriyinden sonra gözetim günlüklerinin zincirini al

NIST SP 800-88 düzeltme 1 yönergelerine göre Data Box disklerden veriler silindikten sonra, gözetim günlüklerinin zinciri kullanılabilir. Bu Günlükler denetim günlüklerini ve sipariş geçmişini içerir. BOM veya manifest dosyaları da denetim günlükleriyle birlikte kopyalanır.

### <a name="audit-logs"></a>Denetim günlükleri

Denetim günlükleri Data Box veya Azure veri merkezi 'nin dışında olduğunda Data Box Heavy paylaşımlara erişme ve bunların nasıl çalıştığı hakkında bilgiler içerir. Bu Günlükler şurada bulunur:`storage-account/azuredatabox-chainofcustodylogs`

Data Box bir denetim günlüğü örneği aşağıda verilmiştir:

```
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


## <a name="download-order-history"></a>İndirme sırası geçmişi

Sipariş geçmişi Azure portal kullanılabilir. Sıra tamamlanmadıysa ve cihaz temizleme (disklerden veri ernemi) tamamlandıktan sonra, cihaz sıraınızdan gidip **sipariş ayrıntıları**' na gidin. **Sipariş geçmişi indirme** seçeneği bulunur. Daha fazla bilgi için bkz. [yükleme sırası geçmişi](data-box-portal-admin.md#download-order-history).

Sipariş geçmişi boyunca kaydırırsanız şunu görürsünüz:

- Cihazınız için taşıyıcı izleme bilgileri.
- *SecureErase* etkinliğine sahip olaylar. Bu olaylar, diskteki verilerin eriyine karşılık gelir.
- Günlük bağlantılarını Data Box. *Denetim günlükleri*, *kopyalama günlükleri*ve *bom* dosyaları için yollar gösterilir.

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

- [Data Box ve Data Box Heavy ilgili sorunları](data-box-troubleshoot.md)nasıl giderebileceğinizi öğrenin.
