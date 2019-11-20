---
title: Azure FileUpload Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. Common. FileUpload Kullanıcı arabirimi öğesini açıklar. Kullanıcıların yönetilen bir uygulamayı dağıttığı sırada dosyaları karşıya yüklemesi gereksinimini sağlar.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 03eff6afb22ea3306bf7f8191f4eca3ccad39938
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151556"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft. Common. FileUpload Kullanıcı arabirimi öğesi

Kullanıcının karşıya yüklenecek bir veya daha fazla dosya belirtmesini sağlayan denetim.

## <a name="ui-sample"></a>UI örneği

![Microsoft. Common. FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

Options. Multiple değeri false ve Options. uploadMode dosyası ise çıktı, dosyanın içeriğine JSON dizesi olarak sahiptir:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Options. Multiple değeri true ve 'options. uploadMode dosyası ise çıktı, dosyaların içeriğini JSON dizisi olarak içerir:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Options. Multiple değeri false ve Options. uploadMode, URL ise çıkışın JSON dizesi olarak bir URL 'SI vardır:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Options. Multiple değeri true ve Options. uploadMode URL ise, çıktıda JSON dizisi olarak URL 'Lerin bir listesi bulunur:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Bir Createuıdefinition test edilirken, bazı tarayıcılar (Google Chrome gibi) tarayıcı konsolundaki Microsoft. Common. FileUpload öğesi tarafından oluşturulan URL 'Leri keser. Tam URL 'Leri kopyalamak için tek tek bağlantılara sağ tıklamanız gerekebilir.

## <a name="remarks"></a>Açıklamalar

- `constraints.accept` tarayıcının dosya iletişim kutusunda gösterilen dosya türlerini belirtir. İzin verilen değerler için [HTML5 belirtimine](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) bakın. Varsayılan değer **null**.
- `options.multiple` **true**olarak ayarlanırsa, kullanıcının tarayıcının dosya iletişim kutusunda birden fazla dosya seçmesini sağlar. Varsayılan değer **false**.
- Bu öğe, `options.uploadMode`değerine göre iki modda dosya yüklemeyi destekler. **Dosya** belirtilmişse, çıkış dosyanın içeriğini blob olarak içerir. **URL** belirtilmişse, dosya geçici bir konuma yüklenir ve çıktının blob URL 'si vardır. Geçici Bloblar 24 saat sonra temizlenir. Varsayılan değer **Dosya**' dır.
- Karşıya yüklenen bir dosya korunuyor. Çıkış URL 'SI, dağıtım sırasında dosyaya erişmek için bir [SAS belirteci](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) içerir.
- `options.openMode` değeri, dosyanın nasıl okunacağını belirler. Dosyanın düz metin olması bekleniyorsa, **metin**belirtin; Aksi takdirde, **ikili**belirtin. Varsayılan değer **metindir**.
- `options.uploadMode` **Dosya** olarak ayarlandıysa ve `options.openMode` **ikili**olarak ayarlandıysa, çıkış Base64 kodlamalı olur.
- `options.encoding` dosya okunurken kullanılacak kodlamayı belirtir. Varsayılan değer **UTF-8**' dir ve yalnızca `options.openMode` **metin**olarak ayarlandığında kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
