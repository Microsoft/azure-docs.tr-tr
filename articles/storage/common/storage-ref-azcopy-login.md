---
title: AzCopy oturum açma | Microsoft Docs
description: Bu makale, AzCopy login komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e7998ea0753ba7ab5d97142c34dc9e333f8b4f5d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034071"
---
# <a name="azcopy-login"></a>azcopy oturum açma

Azure depolama kaynaklarına erişmek için Azure Active Directory 'de oturum açar.

## <a name="synopsis"></a>Özeti

Azure depolama kaynaklarına erişmek için Azure Active Directory oturum açın.

Azure depolama hesabınıza yetki vermek için, depolama alanı, üst kaynak grubu veya üst abonelik bağlamında, Kullanıcı hesabınıza **Depolama Blobu veri katılımcısı** rolünü atamanız gerekir.

Bu komut, işletim sistemi yerleşik mekanizmalarını kullanarak geçerli kullanıcı için şifrelenmiş oturum açma bilgilerini önbelleğe alacak.

Daha fazla bilgi için lütfen örneklere bakın.

> [!IMPORTANT]
> Bir ortam değişkenini komut satırını kullanarak ayarlarsanız, bu değişken komut satırı geçmişinizde okunabilir olacaktır. Komut satırı geçmişinizden kimlik bilgilerini içeren değişkenleri temizlemeyi göz önünde bulundurun. Değişkenlerin geçmişinizde görünmesini önlemek için, kullanıcıya kimlik bilgilerini istemek ve ortam değişkenini ayarlamak için bir komut dosyası kullanabilirsiniz.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
- [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
- [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

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

İstemci parolasını kullanarak hizmet sorumlusu olarak oturum açın. Ortam değişkenini gizli tabanlı hizmet sorumlusu kimlik doğrulaması için istemci parolası AZCOPY_SPA_CLIENT_SECRET ayarlayın.

```azcopy
azcopy login --service-principal
```

Bir sertifika ve parola kullanarak hizmet sorumlusu olarak oturum açın. Ortam değişkenini sertifika tabanlı hizmet sorumlusu yetkilendirmesi için sertifikanın parolasıyla AZCOPY_SPA_CERT_PASSWORD ayarlayın.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

/Path/to/My/CERT ' i bir PEM veya PKCS12 dosyasının yolu olarak kabul ettiğinizden emin olun. AzCopy, sertifikanızı almak için sistem sertifika deposuna ulaşmıyor.

Sertifika tabanlı hizmet sorumlusu kimlik doğrulaması yapılırken sertifika-yol zorunludur.

## <a name="options"></a>Seçenekler

|Seçenek|Açıklama|
|--|--|
|--Uygulama kimliği dizesi|Kullanıcı tarafından atanan kimliğin uygulama KIMLIĞI. Hizmet sorumlusu kimlik doğrulaması için gereklidir.|
|--Sertifika-yol dizesi|SPN kimlik doğrulaması için sertifika yolu. Sertifika tabanlı hizmet sorumlusu kimlik doğrulaması için gereklidir.|
|-h,--yardım|Oturum açma komutu için yardım içeriğini göster.|
|--kimlik|yönetilen hizmet kimliği (MSI) olarak da bilinen sanal makinenin kimliğini kullanarak oturum açın.|
|--Identity-Client-id dizesi|Kullanıcı tarafından atanan kimliğin istemci KIMLIĞI.|
|--Identity-Object-id dizesi|Kullanıcı tarafından atanan kimliğin nesne KIMLIĞI.|
|--Identity-Resource-id dizesi|Kullanıcı tarafından atanan kimliğin kaynak KIMLIĞI.|
|--hizmet-asıl|Bir sertifika veya gizli dizi kullanarak SPN (hizmet sorumlusu adı) aracılığıyla oturum açın. İstemci parolası veya sertifika parolasının uygun ortam değişkenine yerleştirilmesi gerekir. Ortam değişkenlerinin adlarını ve açıklamalarını görmek için `AzCopy env` yazın.|
|--Kiracı kimliği dizesi| OAuth cihaz etkileşimli oturum açma için kullanılacak Azure Active Directory kiracı KIMLIĞI.|

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps uint32|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
