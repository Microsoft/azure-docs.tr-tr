---
title: AzCopy ile v10 arasındaki kullanarak Azure depolama 'ya veri kopyalama veya taşıma | Microsoft Docs
description: AzCopy, depolama hesaplarına veri kopyalamak için kullanabileceğiniz bir komut satırı yardımcı programıdır. Bu makale AzCopy programını indirme, depolama hesabınıza bağlama ve dosyaları aktarma aşamalarında size yardımcı olur.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34d3bd45d2c0bf0260a4f8524cff6f8ac03b746c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501731"
---
# <a name="get-started-with-azcopy"></a>AzCopy’yi kullanmaya başlama

AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır. Bu makale AzCopy programını indirme, depolama hesabınıza bağlama ve dosyaları aktarma aşamalarında size yardımcı olur.

> [!NOTE]
> AzCopy **ile v10 arasındaki** , şu anda desteklenen AzCopy sürümüdür.
>
> AzCopy 'in önceki bir sürümünü kullanmanız gerekiyorsa, bu makalenin [AzCopy 'in önceki sürümünü kullanma](#previous-version) bölümüne bakın.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy'yi indirin

İlk olarak, AzCopy Ile v10 arasındaki çalıştırılabilir dosyasını bilgisayarınızdaki herhangi bir dizine indirin. AzCopy Ile v10 arasındaki yalnızca yürütülebilir bir dosyadır, bu nedenle yüklenecek bir şey yoktur.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Bu dosyalar bir ZIP dosyası (Windows ve Mac) veya bir tar dosyası (Linux) olarak sıkıştırılır. IK dosyasını Linux üzerinde indirip sıkıştırmasını açmak için Linux dağıtımına yönelik belgelere bakın.

> [!NOTE]
> [Azure Tablo depolama](../tables/table-storage-overview.md) hizmetinize veri kopyalamak Istiyorsanız, [azcopy sürüm 7,3](https://aka.ms/downloadazcopynet)' yi de yükleyebilirsiniz.

## <a name="run-azcopy"></a>AzCopy programını çalıştırma

Kolaylık sağlamak için AzCopy yürütülebilir dosyanızın bulunduğu dizini sistem yolunuza ekleyebilirsiniz. Bu şekilde, `azcopy` sisteminizdeki herhangi bir dizinden yazabilirsiniz.

Yolunuza AzCopy dizinini eklememayı seçerseniz, dizinleri AzCopy yürütülebilir dosyanızın konumuyla değiştirmeniz ve `azcopy` `.\azcopy` Windows PowerShell komut istemleri ' nde yazmanız gerekir.

Azure depolama hesabınızın sahibi olarak, verilere erişim için otomatik olarak izinler atanmamıştır. AzCopy ile anlamlı bir işlem yapabilmeniz için, depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayacağınıza karar vermeniz gerekir. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy yetkilendir

Yetkilendirme kimlik bilgilerini Azure Active Directory (AD) kullanarak veya paylaşılan erişim Imzası (SAS) belirteci kullanarak sağlayabilirsiniz.

Bu tabloyu kılavuz olarak kullan:

| Depolama türü | Şu anda desteklenen yetkilendirme yöntemi |
|--|--|
|**Blob depolama** | Azure AD ve SAS |
|**BLOB depolama (hiyerarşik ad alanı)** | Azure AD ve SAS |
|**Dosya depolama** | Yalnızca SAS |

#### <a name="option-1-use-azure-active-directory"></a>Seçenek 1: Azure Active Directory kullanın

Bu seçenek yalnızca BLOB depolama için kullanılabilir. Azure Active Directory kullanarak, her komuta bir SAS belirteci eklemek yerine kimlik bilgilerini bir kez sağlayabilirsiniz.  

> [!NOTE]
> Geçerli sürümde, blob 'ları depolama hesapları arasında kopyalamayı planlıyorsanız, her kaynak URL 'ye bir SAS belirteci eklemeniz gerekir. SAS belirtecini yalnızca hedef URL 'den atlayabilirsiniz. Örnekler için bkz. [depolama hesapları arasında blobları kopyalama](#transfer-data).

Azure AD 'yi kullanarak erişim yetkisi vermek için bkz. [AzCopy ve Azure Active Directory (Azure AD) ile bloblara erişim yetkisi verme](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>2. seçenek: SAS belirteci kullanma

AzCopy komutlarınız içinde kullanılan her kaynak veya hedef URL 'ye bir SAS belirteci ekleyebilirsiniz.

Bu örnek komut, verileri bir yerel dizinden bir blob kapsayıcısına özyinelemeli olarak kopyalar. Kapsayıcı URL 'sinin sonuna kurgusal bir SAS belirteci eklenir.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS belirteçleri ve nasıl elde edileceği hakkında daha fazla bilgi edinmek için bkz. [paylaşılan erişim Imzalarını kullanma (SAS)](./storage-sas-overview.md).

> [!NOTE]
> Depolama hesabının [Güvenli aktarım gerekli](storage-require-secure-transfer.md) ayarı, bir depolama hesabı bağlantısının aktarım katmanı GÜVENLIĞI (TLS) ile güvenli olup olmadığını belirler. Bu ayar varsayılan olarak etkindir.   

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Veri aktarma

Kimliğinizi yetkilendirdikten veya bir SAS belirteci elde ettikten sonra, veri aktarmaya başlayabilirsiniz.

Örnek komutları bulmak için Bu makalelerden birine bakın.

| Hizmet | Makale |
|--------|-----------|
|Azure Blob Depolama|[Azure Blob depolama alanına dosya yükleme](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Depolama|[Blob 'ları Azure Blob depolamadan indirin](storage-use-azcopy-blobs-download.md)|
|Azure Blob Depolama|[Blob 'ları Azure depolama hesapları arasında kopyalama](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Depolama|[Azure Blob depolama ile eşitlemeyi](storage-use-azcopy-blobs-synchronize.md)|
|Azure Dosyaları |[AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)|
|Amazon S3|[Amazon S3 'ten Azure Storage 'a veri kopyalama](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Google Cloud Storage 'daki verileri Azure depolama 'ya kopyalama (Önizleme)](storage-use-azcopy-google-cloud.md)|
|Azure Stack depolama|[AzCopy ve Azure Stack Storage ile veri aktarımı](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Komut yardımını al

Komutların listesini görmek için yazın `azcopy -h` ve ENTER tuşuna basın.

Belirli bir komut hakkında bilgi edinmek için, yalnızca komutun adını ekleyin (örneğin: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Satır içi yardım](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Komutların listesi

Aşağıdaki tabloda tüm AzCopy ile v10 arasındaki komutları listelenmektedir. Her komut bir başvuru makalesine bağlanır. 

|Komut|Açıklama|
|---|---|
|[azcopy ortamı](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Belirli bir konumdan veya belirtilen bir konumdan test verilerini karşıya yükleyerek ya da indirerek bir performans kıyaslaması çalıştırır.|
|[azcopy kopya](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Kaynak verileri bir hedef konumuna kopyalar|
|[azcopy belge](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Markın biçimindeki araç için belgeler oluşturur.|
|[azcopy ortamı](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|AzCopy 'in davranışını yapılandırabileceği ortam değişkenlerini gösterir.|
|[azcopy işleri](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|İşlerin yönetimiyle ilgili alt komutlar.|
|[azcopy işleri temizleme](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Tüm işler için tüm günlük ve plan dosyalarını kaldırın.|
|[azcopy iş listesi](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Tüm işlere ilişkin bilgileri görüntüler.|
|[azcopy işleri kaldırma](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Verilen iş KIMLIĞIYLE ilişkili tüm dosyaları kaldırın.|
|[azcopy işleri sürdürme](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Verilen iş KIMLIĞI ile mevcut işi sürdürür.|
|[azcopy işleri gösterme](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Verilen iş KIMLIĞI için ayrıntılı bilgileri gösterir.|
|[azcopy load](storage-ref-azcopy-load.md)|Belirli biçimlerdeki verileri aktarmaya ilişkin alt komutlar.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Yerel verileri bir kapsayıcıya aktarır ve Microsoft 'un avere Cloud FileSystem (CLFS) biçiminde depolar.|
|[azcopy listesi](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Belirli bir kaynaktaki varlıkları listeler.|
|[azcopy oturum açma](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Azure depolama kaynaklarına erişmek için Azure Active Directory 'de oturum açar.|
|[azcopy oturumu kapatma](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Kullanıcıyı günlüğe kaydeder ve Azure depolama kaynaklarına erişimi sonlandırır.|
|[azcopy oluşturma](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Bir kapsayıcı veya dosya paylaşma oluşturur.|
|[azcopy kaldırma](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Blob 'ları veya dosyaları bir Azure Storage hesabından silin.|
|[azcopy eşitleme](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Kaynak konumunu hedef konuma çoğaltır.|

## <a name="use-in-a-script"></a>Betikte kullanma

#### <a name="obtain-a-static-download-link"></a>Statik indirme bağlantısı alma

Zaman içinde, AzCopy [indirme bağlantısı](#download-and-install-azcopy) , AzCopy 'in yeni sürümlerini işaret edecektir. Betiğinizin AzCopy 'i İndirmeleri durumunda AzCopy 'in daha yeni bir sürümü betiğinizin bağımlı olduğu özellikleri değiştirirse betik çalışmayı durdurabilir.

Bu sorunlardan kaçınmak için, AzCopy 'in geçerli sürümüne bir statik (değişmeyen) bağlantı alın. Bu şekilde, komut dosyası her çalıştığında AzCopy 'in aynı tam sürümünü indirir.

Bağlantıyı almak için şu komutu çalıştırın:

| İşletim sistemi  | Komut |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux için `--strip-components=1` `tar` komutta, sürüm adını içeren en üst düzey klasörü kaldırır ve bunun yerine ikili dosya doğrudan geçerli klasöre ayıklar. Bu, betiğin `azcopy` yalnızca URL 'yi güncelleştirerek yeni bir sürümüyle güncelleştirilmesini sağlar `wget` .

URL Bu komutun çıktısında görüntülenir. Komut dosyası daha sonra bu URL 'YI kullanarak AzCopy 'i indirebilir.

| İşletim sistemi  | Komut |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>SAS belirteçlerinde kaçış özel karakterleri

Uzantısı olan toplu iş dosyalarında `.cmd` `%` SAS belirteçlerinde görünen karakterleri atlamanız gerekir. Bunu, `%` SAS belirteç dizesindeki mevcut karakterlerin yanına ek bir karakter ekleyerek yapabilirsiniz `%` .

#### <a name="run-scripts-by-using-jenkins"></a>Jenkins kullanarak betikleri çalıştırma

Komut dosyalarını çalıştırmak için [Jenkins](https://jenkins.io/) kullanmayı planlıyorsanız, aşağıdaki komutu betiğin başına yerleştirdiğinizden emin olun.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Azure Depolama Gezgini ' de kullanın

[Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) , tüm veri aktarımı işlemlerini gerçekleştirmek Için AzCopy kullanır. AzCopy 'in performans avantajlarından yararlanmak istiyorsanız [Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanabilirsiniz, ancak dosyalarınızda etkileşim kurmak için komut satırı yerine bir grafik kullanıcı arabirimi kullanmayı tercih edersiniz.

Depolama Gezgini, işlemleri gerçekleştirmek için hesap anahtarınızı kullanır, bu nedenle Depolama Gezgini oturum açtıktan sonra, ek yetkilendirme kimlik bilgileri sağlamanız gerekmez.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Yapılandırma, iyileştirme ve çözme

Aşağıdaki kaynaklardan herhangi birine bakın:

- [AzCopy yapılandırma ayarları](storage-ref-azcopy-configuration-settings.md)

- [AzCopy performansını iyileştirin](storage-use-azcopy-optimize.md)

- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Önceki bir sürümü kullan

AzCopy 'in önceki sürümünü kullanmanız gerekirse, aşağıdaki bağlantılardan birine bakın:

- [Windows üzerinde AzCopy (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [Linux üzerinde AzCopy (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Sonraki adımlar

Sorularınız, sorunlarınız veya genel geri bildiriminiz varsa bunları [GitHub](https://github.com/Azure/azure-storage-azcopy) sayfasına gönderin.
