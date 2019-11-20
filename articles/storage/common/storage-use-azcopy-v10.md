---
title: AzCopy ile v10 arasındaki kullanarak Azure depolama 'ya veri kopyalama veya taşıma | Microsoft Docs
description: AzCopy, depolama hesaplarına veri kopyalamak için kullanabileceğiniz bir komut satırı yardımcı programıdır. Bu makale, AzCopy indirmenize, depolama hesabınıza bağlanmanıza ve sonra dosyaları aktarmaya yardımcı olur.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: af24a6b6d165ba60a0d88a4ddf74a4f18836e813
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111763"
---
# <a name="get-started-with-azcopy"></a>AzCopy’i kullanmaya başlama

AzCopy, bir depolama hesabına blob veya dosya kopyalamak için kullanabileceğiniz bir komut satırı yardımcı programıdır. Bu makale, AzCopy indirmenize, depolama hesabınıza bağlanmanıza ve sonra dosyaları aktarmaya yardımcı olur.

> [!NOTE]
> AzCopy **ile v10 arasındaki** , şu anda desteklenen AzCopy sürümüdür.
>
> AzCopy **v 8.1**kullanmanız gerekiyorsa, bu makalenin [önceki AzCopy sürümünü kullanma](#previous-version) bölümüne bakın.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy indirin

İlk olarak, AzCopy Ile v10 arasındaki çalıştırılabilir dosyasını bilgisayarınızdaki herhangi bir dizine indirin. AzCopy Ile v10 arasındaki yalnızca yürütülebilir bir dosyadır, bu nedenle yüklenecek bir şey yoktur.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Bu dosyalar bir ZIP dosyası (Windows ve Mac) veya bir tar dosyası (Linux) olarak sıkıştırılır.

Bu komutları, Linux üzerinde tar dosyasını indirmek ve açmak için kullanabilirsiniz.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopy-v10-linux
tar -xf azcopy.tar.gz
```

> [!NOTE]
> [Azure Tablo depolama](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) hizmetinize veri kopyalamak Istiyorsanız, [azcopy sürüm 7,3](https://aka.ms/downloadazcopynet)' yi de yükleyebilirsiniz.


## <a name="run-azcopy"></a>AzCopy Çalıştır

Kolaylık sağlaması için, AzCopy yürütülebilir dosyasının dizin konumunu, kullanım kolaylığı için sistem yolunuza eklemeyi göz önünde bulundurun. Böylece, sisteminizdeki herhangi bir dizinden `azcopy` yazabilirsiniz.

Yolunuza AzCopy dizinini eklememayı seçerseniz, dizinleri AzCopy yürütülebilir dosyanızın konumuyla değiştirmeniz ve `azcopy` ya da Windows PowerShell komut istemleri ' nde `.\azcopy` yazmanız gerekir.

Komutların listesini görmek için `azcopy -h` yazın ve ENTER tuşuna basın.

Belirli bir komut hakkında bilgi edinmek için, yalnızca komutun adını ekleyin (örneğin: `azcopy list -h`).

![Satır içi yardım](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Her komut ve komut parametresine ilişkin ayrıntılı başvuru belgelerini bulmak için bkz. [AzCopy](storage-ref-azcopy.md)

> [!NOTE] 
> Azure depolama hesabınızın sahibi olarak, verilere erişim için otomatik olarak izinler atanmamıştır. AzCopy ile anlamlı bir işlem yapabilmeniz için, depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayacağınıza karar vermeniz gerekir. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Yetkilendirme kimlik bilgilerini nasıl sağlayabileceklerini seçin

Yetkilendirme kimlik bilgilerini Azure Active Directory (AD) kullanarak veya paylaşılan erişim Imzası (SAS) belirteci kullanarak sağlayabilirsiniz.

Bu tabloyu kılavuz olarak kullan:

| Depolama türü | Şu anda desteklenen yetkilendirme yöntemi |
|--|--|
|**Blob depolama** | Azure AD & SAS |
|**BLOB depolama (hiyerarşik ad alanı)** | Azure AD & SAS |
|**Dosya depolama** | Yalnızca SAS |

### <a name="option-1-use-azure-active-directory"></a>Seçenek 1: Azure Active Directory kullanın

Azure Active Directory kullanarak, her komuta bir SAS belirteci eklemek yerine kimlik bilgilerini bir kez sağlayabilirsiniz.  

> [!NOTE]
> Geçerli sürümde, blob 'ları depolama hesapları arasında kopyalamayı planlıyorsanız, her kaynak URL 'ye bir SAS belirteci eklemeniz gerekir. SAS belirtecini yalnızca hedef URL 'den atlayabilirsiniz. Örnekler için bkz. [depolama hesapları arasında blobları kopyalama](storage-use-azcopy-blobs.md).

İhtiyacınız olan yetkilendirme düzeyi, dosyaları karşıya yüklemeyi veya yalnızca indirmeyi planladığınızı temel alır.

Yalnızca dosyaları indirmek istiyorsanız, [Depolama Blobu veri okuyucusunun](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) Kullanıcı Kimliğiniz, yönetilen kimlik veya hizmet sorumlusuna atandığını doğrulayın.

> Kullanıcı kimlikleri, Yönetilen kimlikler ve hizmet sorumluları her bir *güvenlik sorumlusu*türü olduğundan, bu makalenin geri kalanında *güvenlik sorumlusu* terimini kullanacağız.

Dosyaları karşıya yüklemek isterseniz, bu rollerden birinin güvenlik sorumlusuna atandığını doğrulayın:

- [Depolama Blobu veri Katılımcısı](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Depolama Blobu veri sahibi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Bu roller, bu kapsamların hiçbirinde güvenlik sorumlusuna atanabilir:

- Kapsayıcı (dosya sistemi)
- Depolama hesabı
- Kaynak grubu
- Abonelik

Rolleri doğrulama ve atamayı öğrenmek için bkz. [Azure Portal Azure Blob 'a erişim Izni verme ve VERILERI RBAC ile sıraya](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)alma.

> [!NOTE]
> RBAC rol atamalarının yaymanın beş dakika sürebileceğini aklınızda bulundurun.

Güvenlik sorumlunuz Hedef kapsayıcının veya dizinin erişim denetim listesine (ACL) eklendiyse güvenlik sorumlusuna bu rollerden birine sahip olmanız gerekmez. ACL 'de güvenlik sorumlunuz hedef dizinde yazma iznine sahip olmalıdır ve kapsayıcıda ve her üst dizinde yürütme iznini gerektirir.

Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Access Control](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Kullanıcı kimliğinin kimliğini doğrulama

Kullanıcı kimliğinize gerekli yetkilendirme düzeyi verildiğini doğruladıktan sonra, bir komut istemi açın, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login
```

Birden fazla kuruluşa aitseniz, depolama hesabının ait olduğu kuruluşun kiracı KIMLIĞINI de ekleyin.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` yer tutucusunu, depolama hesabının ait olduğu kuruluşun kiracı KIMLIĞI ile değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin.

Bu komut, bir kimlik doğrulama kodu ve bir Web sitesinin URL 'sini döndürür. Web sitesini açın, kodu sağlayın ve sonra **İleri** düğmesini seçin.

![Bir kapsayıcı oluşturma](media/storage-use-azcopy-v10/azcopy-login.png)

Oturum açma penceresi görüntülenir. Bu pencerede, Azure hesabı kimlik bilgilerinizi kullanarak Azure hesabınızda oturum açın. Başarıyla oturum açtıktan sonra, tarayıcı penceresini kapatabilir ve AzCopy kullanmaya başlayabilirsiniz.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Hizmet sorumlusu kimlik doğrulaması

Bu, özellikle de şirket içinde çalışırken kullanıcı etkileşimi olmadan çalışan bir betiğin içinde AzCopy kullanmayı planlıyorsanız harika bir seçenektir. AzCopy 'i Azure 'da çalışan VM 'lerde çalıştırmayı planlıyorsanız, yönetilen bir hizmet kimliğinin yönetilmesi daha kolaydır. Daha fazla bilgi edinmek için bu makalenin [yönetilen kimlik doğrulama](#managed-identity) bölümüne bakın.

Bir betiği çalıştırmadan önce, hizmet sorumlunuzu kimlik bilgileriyle AzCopy sağlayabilmeniz için, etkileşimli olarak en az bir kez oturum açmanız gerekir.  Bu kimlik bilgileri güvenli ve şifreli bir dosyada depolanır, böylece komut dosyanız bu hassas bilgileri sağlamalıdır.

Bir istemci gizli anahtarını veya hizmet sorumlusunun uygulama kaydıyla ilişkili bir sertifikanın parolasını kullanarak hesabınızda oturum açabilirsiniz.

Hizmet sorumlusu oluşturma hakkında daha fazla bilgi edinmek için bkz. [nasıl yapılır: portalı kullanarak kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Genel olarak hizmet sorumluları hakkında daha fazla bilgi için, bkz. [Azure Active Directory uygulama ve hizmet sorumlusu nesneleri](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>İstemci parolası kullanma

`AZCOPY_SPA_CLIENT_SECRET` ortam değişkenini, hizmet sorumlusunun uygulama kaydının istemci gizli anahtarı olarak ayarlayarak başlayın.

> [!NOTE]
> Bu değeri, işletim sisteminizin ortam değişkeni ayarlarında değil, komut isteminizden ayarladığınızdan emin olun. Bu şekilde, değer yalnızca geçerli oturum için kullanılabilir.

Bu örnek, PowerShell 'de bunu nasıl yapakullanabileceğinizi gösterir.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Bu örnekte gösterildiği gibi bir istem kullanmayı düşünün. Bu şekilde, parolanız konsolunuzun komut geçmişinde görünmez.  

Sonra, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

`<application-id>` yer tutucusunu, hizmet sorumlusunun uygulama kaydının uygulama KIMLIĞIYLE değiştirin. `<tenant-id>` yer tutucusunu, depolama hesabının ait olduğu kuruluşun kiracı KIMLIĞI ile değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin. 

##### <a name="using-a-certificate"></a>Sertifika kullanma

Yetkilendirme için kendi kimlik bilgilerinizi kullanmayı tercih ediyorsanız, uygulama kaydınıza bir sertifika yükleyebilir ve sonra bu sertifikayı kullanarak oturum açabilirsiniz.

Sertifikanızı uygulama kaydıza yüklemeye ek olarak, AzCopy 'in çalıştığı makineye veya VM 'ye kaydedilmiş sertifikanın bir kopyasına sahip olmanız da gerekir. Sertifikanın bu kopyası içinde olmalıdır. PFX veya. PEK biçimi ve özel anahtarı içermelidir. Özel anahtar parola korumalı olmalıdır. Windows kullanıyorsanız ve sertifikanız yalnızca bir sertifika deposunda mevcutsa, sertifikayı bir PFX dosyasına (özel anahtar dahil) aktardığınızdan emin olun. Rehberlik için bkz. [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Sonra, `AZCOPY_SPA_CERT_PASSWORD` ortam değişkenini sertifika parolası olarak ayarlayın.

> [!NOTE]
> Bu değeri, işletim sisteminizin ortam değişkeni ayarlarında değil, komut isteminizden ayarladığınızdan emin olun. Bu şekilde, değer yalnızca geçerli oturum için kullanılabilir.

Bu örnek, PowerShell 'de bu görevi nasıl yapakullanabileceğinizi gösterir.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Sonra, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>` yer tutucusunu, sertifika dosyasının göreli veya tam yoluyla değiştirin. AzCopy, bu sertifikaya olan yolu kaydeder ancak sertifikanın bir kopyasını kaydetmez, bu nedenle bu sertifikayı yerinde sakladığınızdan emin olun. `<tenant-id>` yer tutucusunu, depolama hesabının ait olduğu kuruluşun kiracı KIMLIĞI ile değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin.

> [!NOTE]
> Bu örnekte gösterildiği gibi bir istem kullanmayı düşünün. Bu şekilde, parolanız konsolunuzun komut geçmişinde görünmez. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Yönetilen kimliğin kimliğini doğrulama

Bu, Kullanıcı etkileşimi olmadan çalışan bir betiğin içinde AzCopy kullanmayı planlıyorsanız ve betik bir Azure sanal makineden (VM) çalışıyorsa harika bir seçenektir. Bu seçeneği kullanırken, sanal makinede herhangi bir kimlik bilgisi depolamanız gerekmez.

VM 'niz üzerinde etkinleştirdiğiniz sistem genelinde yönetilen kimliği kullanarak hesabınızda oturum açabilir veya sanal makinenize atadığınız kullanıcı tarafından atanan yönetilen kimliğin istemci KIMLIĞINI, nesne KIMLIĞINI veya kaynak KIMLIĞINI kullanabilirsiniz.

Sistem genelinde yönetilen bir kimliği etkinleştirme veya Kullanıcı tarafından atanan yönetilen kimlik oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure Portal kullanarak BIR VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Sistem genelinde yönetilen kimlik kullanma

İlk olarak, VM 'niz üzerinde sistem genelinde yönetilen bir kimlik etkinleştirdiğinizden emin olun. Bkz. [sistem tarafından atanan yönetilen kimlik](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Ardından, komut konsolunuza aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik kullanma

İlk olarak, VM 'niz üzerinde kullanıcı tarafından atanan bir yönetilen kimliği etkinleştirdiğinizden emin olun. Bkz. [Kullanıcı tarafından atanan yönetilen kimlik](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Ardından, komut konsolunuza aşağıdaki komutlardan birini yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>` yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin istemci KIMLIĞI ile değiştirin.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>` yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin nesne KIMLIĞIYLE değiştirin.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>` yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin kaynak KIMLIĞI ile değiştirin.

### <a name="option-2-use-a-sas-token"></a>2\. seçenek: SAS belirteci kullanma

AzCopy komutlarınız içinde kullanılan her kaynak veya hedef URL 'ye bir SAS belirteci ekleyebilirsiniz.

Bu örnek komut, verileri bir yerel dizinden bir blob kapsayıcısına özyinelemeli olarak kopyalar. Kapsayıcı URL 'sinin sonuna kurgusal bir SAS belirteci eklenir.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS belirteçleri ve nasıl elde edileceği hakkında daha fazla bilgi edinmek için bkz. [paylaşılan erişim Imzalarını kullanma (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Dosyaları aktar

Kimliğinizi doğruladıktan veya bir SAS belirteci edindikten sonra, dosyaları aktarmaya başlayabilirsiniz.

Örnek komutları bulmak için Bu makalelerden birine bakın.

- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)

- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)

- [AzCopy ve Amazon S3 demetleri ile veri aktarma](storage-use-azcopy-s3.md)

- [AzCopy ve Azure Stack Storage ile veri aktarımı](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Betikte AzCopy kullanma

### <a name="obtain-a-static-download-link"></a>Statik indirme bağlantısı alma

Zaman içinde, AzCopy [indirme bağlantısı](#download-and-install-azcopy) , AzCopy 'in yeni sürümlerini işaret edecektir. Betiğinizin AzCopy 'i İndirmeleri durumunda AzCopy 'in daha yeni bir sürümü betiğinizin bağımlı olduğu özellikleri değiştirirse betik çalışmayı durdurabilir.

Bu sorunlardan kaçınmak için, AzCopy 'in geçerli sürümüne bir statik (değişiklik kaldırma) bağlantısı alın. Bu şekilde, komut dosyası her çalıştığında AzCopy 'in aynı tam sürümünü indirir.

Bağlantıyı almak için şu komutu çalıştırın:

| İşletim sistemi  | Komut |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux için, `tar` komutunda `--strip-components=1`, sürüm adını içeren en üst düzey klasörü kaldırır ve bunun yerine ikilisini doğrudan geçerli klasöre ayıklar. Bu, betiğin yalnızca `wget` URL 'sini güncelleştirerek `azcopy` yeni bir sürümüyle güncelleştirilmesini sağlar.

URL Bu komutun çıktısında görüntülenir. Komut dosyası daha sonra bu URL 'YI kullanarak AzCopy 'i indirebilir.

| İşletim sistemi  | Komut |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>SAS belirteçlerinde kaçış özel karakterleri

`.cmd` uzantısı olan toplu iş dosyalarında, SAS belirteçlerinde görünen `%` karakterlerinin atlamanız gerekir. Bunu, SAS belirteç dizesindeki mevcut `%` karakterlerinin yanına bir ekleme `%` karakteri ekleyerek yapabilirsiniz.

### <a name="run-scripts-by-using-jenkins"></a>Jenkins kullanarak betikleri çalıştırma

Komut dosyalarını çalıştırmak için [Jenkins](https://jenkins.io/) kullanmayı planlıyorsanız, aşağıdaki komutu betiğin başına yerleştirdiğinizden emin olun.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Azure Depolama Gezgini AzCopy kullanın

[Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) , tüm veri aktarımı işlemlerini gerçekleştirmek Için AzCopy kullanır. AzCopy 'in performans avantajlarından yararlanmak istiyorsanız [Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanabilirsiniz, ancak dosyalarınızda etkileşim kurmak için komut satırı yerine bir grafik kullanıcı arabirimi kullanmayı tercih edersiniz.

Depolama Gezgini, işlemleri gerçekleştirmek için hesap anahtarınızı kullanır, bu nedenle Depolama Gezgini oturum açtıktan sonra, ek yetkilendirme kimlik bilgileri sağlamanız gerekmez.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>AzCopy 'in önceki sürümünü kullan

AzCopy 'in önceki sürümünü (AzCopy v 8.1) kullanmanız gerekirse, aşağıdaki bağlantılardan birine bakın:

- [Windows üzerinde AzCopy (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Linux üzerinde AzCopy (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 'i yapılandırma, iyileştirme ve sorun giderme

Bkz. [AzCopy 'ı yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Sonraki adımlar

Sorularınız, sorunlarınız veya genel geri bildiriminiz varsa bunları [GitHub](https://github.com/Azure/azure-storage-azcopy) sayfasına gönderin.
