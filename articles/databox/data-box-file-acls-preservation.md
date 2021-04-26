---
title: Azure Data Box ile dosya ACL 'Lerini, öznitelikleri ve zaman damgalarını koruma
description: Azure Data Box SMB aracılığıyla veri kopyalama sırasında ACL 'Ler, zaman damgaları ve öznitelikler korunur. Windows ve Linux veri kopyalama araçlarıyla meta verileri kopyalama.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950321"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Azure Data Box ile dosya ACL 'Lerini, öznitelikleri ve zaman damgalarını koruma

Azure Data Box, Azure 'a veri gönderirken erişim denetim listelerini (ACL), zaman damgalarını ve dosya özniteliklerini korumanıza olanak sağlar. Bu makalede, verileri Azure dosyalarına yüklemek için sunucu Ileti bloğu (SMB) üzerinden Data Box veri kopyalarken aktarabileceğiniz meta veriler açıklanmaktadır. 

Windows ve Linux veri kopyalama araçlarıyla meta verileri kopyalamak için özel adımlar sağlanmaktadır. Veriler blob depolamaya aktarılırken korunmaz.

Bu makalede, aktarılan ACL 'Ler, zaman damgaları ve dosya öznitelikleri topluca *meta veriler* olarak adlandırılır.

## <a name="transferred-metadata"></a>Aktarılan meta veriler

Data Box veriler Azure dosyalarına yüklendiğinde aşağıdaki meta veriler aktarılır.

#### <a name="timestamps"></a>Zaman damgaları

Aşağıdaki zaman damgaları aktarılır:
- CreationTime
- LastWriteTime

Aşağıdaki zaman damgası aktarılmaz:
- LastAccessTime
  
#### <a name="file-attributes"></a>Dosya öznitelikleri

Aksi belirtilmedikçe her iki dosya ve dizindeki dosya öznitelikleri aktarılır.

Aşağıdaki dosya öznitelikleri aktarılır:
- FILE_ATTRIBUTE_READONLY (yalnızca dosya)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (yalnızca dizin)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (yalnızca dosya)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Aşağıdaki dosya öznitelikleri aktarılmaz:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Dizinlerde salt okuma öznitelikleri aktarılmaz.

#### <a name="acls"></a>Cacls

SMB üzerinden Data Box kopyaladığınız dizinlerin ve dosyaların tüm ACL 'Leri kopyalanır ve aktarılır. Aktarımlar, hem isteğe bağlı ACL 'Leri (DACL) hem de sistem ACL 'Lerini (SACL) içerir Linux için yalnızca Windows NT ACL 'Leri aktarılır.

ACL 'Ler ağ dosya sistemi (fer) üzerinden veri kopyaları sırasında ve verilerinizi aktarmak için veri kopyalama hizmetini kullandığınızda aktarılmaz. Veri kopyalama hizmeti, verileri doğrudan paylaşımınızdan okur ve ACL 'Leri okuyamıyorum.

Veri kopyalama aracınız ACL 'Leri kopyalamasa bile, dizin ve dosyalardaki varsayılan ACL 'Ler Azure dosyalarına aktarılır. Varsayılan ACL 'Ler, yerleşik yönetici hesabı, SISTEM hesabı ve Data Box verileri takmak ve kopyalamak için kullanılan SMB Share Kullanıcı hesabı için izinlere sahiptir.

ACL 'Ler, şu özelliklere sahip güvenlik tanımlayıcıları içeriyor: ACL 'Ler, sahip, Grup, SACL.

ACL 'lerin aktarımı varsayılan olarak etkindir. Data Box yerel Web Kullanıcı arabiriminde bu ayarı devre dışı bırakmak isteyebilirsiniz. Daha fazla bilgi için bkz. [Data Box ve Data Box Heavy yönetmek için yerel Web Kullanıcı arabirimini kullanma](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Koşullu erişim denetimi girişi (ACE) dizelerini içeren ACL 'Leri olan dosyalar kopyalanmaz. Bu bilinen bir sorundur. Bu sorunu çözmek için, paylaşıma bağlanarak ve ACL 'Leri kopyalamayı destekleyen bir kopyalama aracı kullanarak bu dosyaları Azure dosyaları paylaşımında el ile kopyalayın.

## <a name="copying-data-and-metadata"></a>Verileri ve meta verileri kopyalama

Verilerinize ilişkin ACL 'Leri, zaman damgalarını ve öznitelikleri aktarmak için, Data Box verileri kopyalamak üzere aşağıdaki yordamları kullanın. 

### <a name="windows-data-copy-tool"></a>Windows veri kopyalama aracı

SMB aracılığıyla Data Box verileri kopyalamak için, gibi bir SMB uyumlu dosya kopyalama aracı kullanın `robocopy` . Aşağıdaki örnek komut, tüm dosyaları ve dizinleri kopyalar, verileri verilerle birlikte veri aktarımı.

`/copyall`Veya `/dcopy:DAT` seçeneğini kullanırken, gerekli yedekleme işlecinin ayrıcalıkların devre dışı bırakılmadığından emin olun. Daha fazla bilgi için bkz. [Data Box ve Data Box Heavy yönetmek için yerel Web Kullanıcı arabirimini kullanma](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

konum

|Seçenek |Açıklama |
|------------------- | ----- |
|`/copyall` |Tüm öznitelikleri kopyalar.|
|`/e`      |Boş dizinler dahil olmak üzere alt dizinleri kopyalar.         |
|`/dcopy:DAT`  |Verileri, öznitelikleri ve zaman damgalarını kopyalar. Note: dizinleri aktarmak için/DCOPY: DAT seçeneği kullanılmalıdır `CreationTime` . |
|`/r:3`    |Başarısız kopyalarda 3 yeniden deneme belirtir.         |
|`/w:60`   |Yeniden denemeler arasındaki 60 saniyelik bekleme süresini belirtir.         |
|`/is`     |Aynı dosyaları dahil eder.         |
|`/nfl`    |Dosya adlarını günlüğe eklemez.         |
|`/ndl`    |Dizin adlarını günlüğe eklemez.        |
|`/np`     |Kopyalama işleminin ilerlemesini göstermez.         |
|`/MT:32 or 64`  |32 veya 64 iş parçacığı ile çoklu iş parçacığı kullanımı kullanır.           |
|`/fft`    |Herhangi bir dosya sistemi için zaman damgası ayrıntı düzeyini azaltır.        |
|`/log+:<LogFile>`  |Çıkışı var olan günlük dosyasına ekler.|

Bu parametreler hakkında daha fazla bilgi için `robocopy` bkz [. ÖĞRETICI: SMB aracılığıyla Azure Data Box verileri kopyalama](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Linux veri kopyalama aracı

Linux 'ta meta verilerin aktarılması iki adımlı bir işlemdir. İlk olarak, meta verileri kopyalamadıkları gibi bir araç kullanarak kaynak verileri kopyalayın `rsync` . Verileri kopyaladıktan sonra, veya gibi bir araç kullanarak meta verileri kopyalayabilirsiniz `smbcacls` `cifsacl` . 

Aşağıdaki örnek komutlar, kullanarak verileri kopyalayarak ilk adımı yapılır `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Sonraki adımlar

- [SMB aracılığıyla Azure Data Box verileri kopyalama](./data-box-deploy-copy-data.md)