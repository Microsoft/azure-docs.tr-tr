---
title: Azure Içeri/dışarı aktarma hizmeti hakkında SSS | Microsoft Docs
description: Azure Içeri aktarma hizmeti hakkında sık sorulan soruların yanıtlarını okuyun.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 109f541157716ae4f9b195d0a3ed02a1d8c91960
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314117"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure Içeri/dışarı aktarma hizmeti: sık sorulan sorular 
Azure depolama 'ya veri aktarmak için Azure Içeri/dışarı aktarma hizmetinizi kullandığınızda kullanabileceğiniz sorular ve yanıtlar aşağıda verilmiştir. Sorular ve yanıtlar aşağıdaki kategorilere ayrılmıştır:

- Içeri/dışarı aktarma hizmeti hakkında
- Diskler içeri/dışarı aktarma için hazırlanıyor
- İşleri içeri/dışarı aktarma
- Diskler teslim etme
- Çeşitli 

## <a name="about-importexport-service"></a>Içeri/dışarı aktarma hizmeti hakkında

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Azure Içeri/dışarı aktarma hizmetini kullanarak Azure dosya depolamayı kopyalayabilir miyim?

Evet. Azure Içeri/dışarı aktarma hizmeti, Azure dosya depolama alanına aktarmayı destekler. Şu anda Azure dosyalarının dışarı aktarılmasını desteklemez.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Azure Içeri/dışarı aktarma hizmeti CSP abonelikleri için kullanılabilir mi?

Evet. Azure Içeri/dışarı aktarma hizmeti, bulut çözümü sağlayıcıları (CSP) aboneliklerini destekler.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Azure Içeri/dışarı aktarma hizmetini kullanarak PST posta kutularını ve SharePoint verilerini O365 'e kopyalayabilir miyim?

Evet. Daha fazla bilgi için bkz. [PST dosyalarını veya SharePoint verilerini Office 365 'ye aktarma](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Yedeklemelerimi Azure Backup hizmetine çevrimdışı kopyalamak için Azure Içeri/dışarı aktarma hizmetini kullanabilir miyim?

Evet. Daha fazla bilgi için [Azure Backup çevrimdışı yedekleme iş akışına](../../backup/backup-azure-backup-import-export.md)gidin.

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Microsoft 'tan içeri/dışarı aktarma işleri için sürücü satın alabilir miyim?

Hayır. İçeri ve dışarı aktarma işleri için kendi sürücülerinizi teslim etmeniz gerekir.


## <a name="preparing-disks-for-importexport"></a>Diskler içeri/dışarı aktarma için hazırlanıyor

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Bir içeri aktarma işi için sürücü hazırlama adımını atlayabilir miyim? Bir sürücüyü kopyalamadan hazırlayabilir miyim?

Hayır. Verileri içeri aktarmak için kullanılan tüm sürücüler Azure Waımportexport Aracı kullanılarak hazırlanmalıdır. Ayrıca, verileri sürücüye kopyalamak için aracını kullanın.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Dışarı aktarma işi oluştururken disk hazırlığı gerçekleştirmem gerekir mi?

Hayır. Bazı ön denetimler önerilir. Gereken disk sayısını denetlemek için Waımportexport aracının önizleme dışarı aktar komutunu kullanın. Daha fazla bilgi için bkz. [bir dışarı aktarma işi Için sürücü kullanımının önizlemesi](https://msdn.microsoft.com/library/azure/dn722414.aspx). Bu komut, kullanacağınız sürücülerin boyutuna bağlı olarak seçili Blobların sürücü kullanımının önizlemesini yapmanıza yardımcı olur. Ayrıca, dışa aktarma işi için gönderilen sabit sürücüden okuyup okuyup yazverecağınızı kontrol edin.

## <a name="importexport-jobs"></a>İşleri içeri/dışarı aktarma

### <a name="can-i-cancel-my-job"></a>İşimi iptal edebilir miyim?
Evet. Durumu **oluştururken** veya **teslim**edildiğinde bir işi iptal edebilirsiniz. Bu aşamaların ötesinde iş iptal edilemez ve son aşamaya kadar devam eder.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Azure portal tamamlanan işlerin durumunu ne kadar görüntüleyebilirim?
Tamamlanan işlerin durumunu 90 güne kadar görüntüleyebilirsiniz. Tamamlanan İşler 90 gün sonra silinir.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>10 ' dan fazla sürücü içeri veya dışarı aktarmak istersem ne yapmam gerekir?
Bir içeri veya dışarı aktarma işi tek bir işte yalnızca 10 sürücüye başvurabilir. 10 ' dan fazla sürücü teslim etmek için birden çok iş oluşturmanız gerekir. Aynı işle ilişkili Sürücüler aynı pakette birlikte sevk edilmelidir. Veri kapasitesi birden çok disk içeri aktarma işine yayıldığında daha fazla bilgi ve rehberlik için Microsoft Desteği başvurun. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Karşıya yüklenen blob, durumu "kira süresinin dolması" olarak gösterir. Ne yapmalıyım?
"Kira süresinin dolması" alanını yoksayabilirsiniz. İçeri/dışarı aktarma, blob 'u karşıya yükleme sırasında, başka hiçbir işlemin paralel olarak güncelleştiremezse emin olmak için kira alır. Kira süresinin dolması, Içeri/dışarı aktarmanın artık bu dosyaya yükleme olmadığını ve BLOB 'u kullanım için kullanılabilir hale geçtiğini gösterir. 

## <a name="shipping-disks"></a>Diskler teslim etme

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Tek bir sevkiyatta en fazla HDD sayısı nedir?
Bir sevkiyatta HDD sayısı için bir sınır yoktur. Diskler birden çok iş öğesine aitse şunları yapmanızı öneririz: 
- diskleri ilgili iş adlarıyla etiketleyin.
- -1,-2 vb. ile düzeltilen bir izleme numarası olan işleri güncelleştirin.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Paketimde HDD dışında bir şey mi içersin?
Yalnızca sabit sürücülerinizi sevkiyat paketine gönderin. Güç kaynağı kabloları veya USB kabloları gibi öğeleri eklemeyin.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>FedEx veya DHL kullanarak Sürücülerimi göndermem gerekir mi?
FedEx, DHL, UPS veya US posta hizmeti gibi bilinen bir taşıyıcısı kullanarak Azure veri merkezine sürücü gönderebilirsiniz. Ancak, veri merkezinizden size sürücülerin iade sevk irsaliyesi için şunu sağlamanız gerekir:

- ABD ve AB 'de FedEx hesap numarası veya
- Asya ve Avustralya bölgelerindeki bir DHL hesap numarası.

> [!NOTE]
> Hindistan 'daki veri merkezleri, sürücüleri döndürmek için antetin (teslim Challan) üzerinde bir bildirim harfi gerektirir. Gerekli giriş geçişini düzenlemek için, seçtiğiniz taşıyıcınızla birlikte çekmeyi de ve ayrıntıları veri merkezi ile paylaşmanız gerekir.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Sürücüne uluslararası gönderi ile ilgili herhangi bir kısıtlama var mı?
Lütfen sevk ettiğiniz fiziksel medyanın Uluslararası kenarlıkların çapraz olması gerekebileceğini unutmayın. Fiziksel medyanızın ve verilerinizin, ilgili yasalar doğrultusunda içeri aktarıldığından ve/veya aktarıldığından emin olmanız sizin sorumluluğunuzdadır. Fiziksel medyayı teslim etmeden önce, medya ve verilerinizin tanımlanan veri merkezine yasal bir şekilde gönderilbildiğini doğrulamak için danışmanlarınız ile görüşün. Bu, Microsoft 'un zamanında ulaşmasını sağlamaya yardımcı olur.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Disklerimi bir veri merkezine teslim etmek için özel gereksinimler var mı?

Gereksinimler, belirli Azure veri merkezi kısıtlamalarına bağlıdır.
- Güvenlik nedenleriyle, paket üzerinde bir Microsoft veri merkezi gelen KIMLIK numarası yazılmasını gerektiren birkaç site vardır. Sürücülerinizi veya disklerinizi veri merkezine göndermeden önce, bu sayıyı almak için Azure Data Box işlemlerine (adbops@microsoft.com) başvurun. Bu sayı olmadan paket reddedilir.
- Hindistan 'daki veri merkezleri, sürücünün kamu KIMLIK kartı veya kanıt No gibi kişisel ayrıntılarını gerektirir. (örneğin, PAN, AADHAR, DL), ad, kişi ve bir kapı girişi geçişi almak için otomobil levha numarası. Teslim gecikmelerinden kaçınmak için, bu gereksinimlere ilişkin taşıyıcınızın bilgilerini bildirin.


### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Bir iş oluştururken, sevkiyat adresi depolama hesabı konumumdaki farklı bir konum olur. Ne yapmalıyım?

Bazı depolama hesabı konumları alternatif sevkiyat konumlarına eşlenir. Daha önce kullanılabilir kargo konumları, alternatif konumlara geçici olarak eşleştirilebilir. Sürücülerinizi teslim etmeden önce iş oluşturma sırasında verilen sevkiyat adresini her zaman denetleyin.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Sürücüne gönderim yaparken, taşıyıcı veri merkezi iletişim adresini ve telefon numarasını ister. Neleri sağlamam gerekir?

Telefon numarası ve DC adresi iş oluşturma işleminin parçası olarak sağlanır.


## <a name="miscellaneous"></a>Çeşitli

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Yanlışlıkla desteklenen gereksinimlere uymayan bir HDD gönderiyorsanız ne olur?

Azure veri merkezi, desteklenen gereksinimlere uymayan sürücüyü döndürür. Paketteki sürücülerin yalnızca bir kısmı destek gereksinimlerini karşılıyorsa, bu sürücüler işlenir ve gereksinimleri karşılamayan sürücüler size döndürülür.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Hizmet, sürücüleri döndürmeden önce biçimlendirir mi?

Hayır. Tüm sürücüler BitLocker ile şifrelenir.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Bu hizmet tarafından içeri aktarılan verilere nasıl erişebilirim?

Azure depolama hesabınızın altındaki verilere erişmek için Azure portalını veya [Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) kullanın.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>İçeri aktarma işlemi tamamlandıktan sonra, verileri depolama hesabında nasıl görünür? Dizin hiyerarşim korunuyor mu?

Bir sabit sürücüyü içeri aktarma işi için hazırlarken, hedef veri kümesi CSV içindeki DstBlobPathOrPrefix alanı tarafından belirtilir. Bu, depolama hesabındaki, sabit sürücüdeki verilerin kopyalandığı hedef kapsayıcıdır. Bu hedef kapsayıcıda, sanal dizinler sabit sürücüden klasörler için oluşturulur ve dosyalar için Bloblar oluşturulur. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Bir sürücüde depolama hesabmda zaten bulunan dosyalar varsa, hizmet mevcut Blobların veya dosyaların üzerine yazar mı?

Şekline. Sürücüyü hazırlarken, veri kümesi CSV dosyasında disposition adı verilen alan kullanılarak hedef dosyaların üzerine yazılıp yazılmayacağını veya yoksayılmayacağını belirtebilirsiniz: < yeniden adlandır | Hayır-üzerine yaz | üzerine yaz >. Varsayılan olarak hizmet, mevcut Blobların veya dosyaların üzerine yazmak yerine yeni dosyaları yeniden adlandırır.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Waımportexport Aracı, 32 bitlik işletim sistemleriyle uyumludur mi?
Hayır. Waımportexport aracı yalnızca 64 bitlik Windows işletim sistemleriyle uyumludur. Desteklenen işletim SISTEMININ tüm listesi için [desteklenen Işletim sistemlerine](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements)gidin. 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Azure Içeri/dışarı aktarma tarafından desteklenen maksimum Blok Blobu ve Sayfa Blobu boyutu nedir?

Maksimum Blok Blobu boyutu yaklaşık 4.768 TB veya 5.000.000 MB 'dir.
En fazla sayfa blobu boyutu 8TB 'tır.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Azure Içeri/dışarı aktarma, AES-256 şifrelemesini destekliyor mu?
Azure Içeri/dışarı aktarma hizmeti varsayılan olarak AES-128 BitLocker şifrelemesini kullanır. Veri kopyalanmadan önce BitLocker ile el ile şifreleme yaparak bunu AES-256 olarak değiştirebilirsiniz. 

- [Waımportexport v1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip)kullanılıyorsa, aşağıdaki örnek bir komuttur
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- [Waımportexport v2](https://www.microsoft.com/download/details.aspx?id=55280) kullanılıyorsa "AlreadyEncrypted" belirtin ve anahtarı DRIVESET CSV ' de girin.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Içeri/dışarı aktarma nedir?](storage-import-export-service.md)


