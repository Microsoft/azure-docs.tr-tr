---
title: AzCopy oturum açma | Microsoft Docs
description: Bu makale, AzCopy login komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503244"
---
# <a name="azcopy-login"></a>azcopy oturum açma

Azure depolama kaynaklarına erişmek için Azure Active Directory 'de oturum açar.

## <a name="synopsis"></a>Özeti

Azure depolama kaynaklarına erişmek için Azure Active Directory oturum açın.

Azure depolama hesabınıza yetki vermek için, depolama alanı, üst kaynak grubu veya üst abonelik bağlamında, Kullanıcı hesabınıza **Depolama Blobu veri katılımcısı** rolünü atamanız gerekir.

Bu komut, işletim sistemi yerleşik mekanizmalarını kullanarak geçerli kullanıcı için şifrelenmiş oturum açma bilgilerini önbelleğe alacak.

> [!IMPORTANT]
> Bir ortam değişkenini komut satırını kullanarak ayarlarsanız, bu değişken komut satırı geçmişinizde okunabilir olacaktır. Komut satırı geçmişinizden kimlik bilgilerini içeren değişkenleri temizlemeyi göz önünde bulundurun. Değişkenlerin geçmişinizde görünmesini önlemek için, kullanıcıya kimlik bilgilerini istemek ve ortam değişkenini ayarlamak için bir komut dosyası kullanabilirsiniz.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [Öğretici: AzCopy ile şirket içi verileri bulut depolamaya geçirme](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

## <a name="examples"></a>Örnekler

Varsayılan AAD kiracı KIMLIĞI ortak olarak ayarlanan şekilde etkileşimli olarak oturum açın:

```azcopy
azcopy login
```

Belirtilen kiracı KIMLIĞIYLE etkileşimli olarak oturum açın:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Bir sanal makinenin (VM) sistem tarafından atanan kimliğini kullanarak oturum açın:

```azcopy
azcopy login --identity
```

Bir VM 'nin Kullanıcı tarafından atanan kimliğini ve hizmet kimliğinin Istemci KIMLIĞINI kullanarak oturum açın:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Bir VM 'nin Kullanıcı tarafından atanan kimliğini ve hizmet kimliğinin nesne KIMLIĞINI kullanarak oturum açın:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Bir VM 'nin Kullanıcı tarafından atanan kimliğini ve hizmet kimliğinin kaynak KIMLIĞINI kullanarak oturum açın:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Bir istemci parolası kullanarak hizmet sorumlusu olarak oturum açın: ortam AZCOPY_SPA_CLIENT_SECRET değişkenini, gizli tabanlı hizmet sorumlusu kimlik doğrulaması için istemci parolası olarak ayarlayın.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Bir sertifika ve parola kullanarak bir hizmet sorumlusu olarak oturum açın:

Ortam değişkenini sertifika tabanlı hizmet sorumlusu kimlik doğrulaması için sertifikanın parolasına AZCOPY_SPA_CERT_PASSWORD ayarlayın:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

`/path/to/my/cert`Pek veya Pkcs12 dosyasının yolunu olarak değerlendirin. AzCopy, sertifikanızı almak için sistem sertifika deposuna ulaşmıyor.

`--certificate-path` , sertifika tabanlı hizmet sorumlusu kimlik doğrulaması yapılırken zorunludur.

## <a name="options"></a>Seçenekler

**--AAD-bitiş noktası** dizesi kullanılacak Azure Active Directory uç noktası. Varsayılan değer ( https://login.microsoftonline.com) Genel Azure bulutu için doğrudur.) Ulusal bir bulutta kimlik doğrulanırken bu parametreyi ayarlayın. Yönetilen Hizmet Kimliği için gerekli değildir.

**--uygulama kimliği** DIZE uygulama kimliği kullanıcı tarafından atanan kimlik. Hizmet sorumlusu kimlik doğrulaması için gereklidir.

**--** SPN kimlik doğrulaması için sertifika yolu dize yolu. Sertifika tabanlı hizmet sorumlusu kimlik doğrulaması için gereklidir.

**--**   komut yardımı Yardımı `azcopy login` .

**--kimlik**   Yönetilen hizmet kimliği (MSI) olarak da bilinen sanal makinenin kimliğini kullanarak oturum açın.

**--Identity-Client-id** DIZE istemci kimliği kullanıcı tarafından atanan kimlik.

**--Identity-Object-id** DIZE nesne kimliği kullanıcı tarafından atanan kimlik.

**--Identity-Resource-id** DIZE kaynak kimliği kullanıcı tarafından atanan kimlik.

**--hizmet-asıl**   Bir sertifika veya gizli dizi kullanarak hizmet asıl adı (SPN) aracılığıyla oturum açın. İstemci parolası veya sertifika parolasının uygun ortam değişkenine yerleştirilmesi gerekir. Ortam değişkenlerinin adlarını ve açıklamalarını görmek için AzCopy env yazın.

**--Kiracı kimliği** dizesi OAuth cihaz etkileşimli oturum açma için kullanılacak Azure ACTIVE DIRECTORY Kiracı kimliği.

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps float|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|
|--Güvenilen-Microsoft-sonekler dizesi   |Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
