---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı 'nı sınama | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının test komutunun nasıl çalıştırılacağını açıklar.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632815"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracını test etme (Önizleme)

Bu makalede, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının test komutunun nasıl çalıştırılacağı açıklanmaktadır.

## <a name="introduction"></a>Giriş

Yapılandırma testi komut kullanılarak yapılır `azacsnap -c test` .

## <a name="command-options"></a>Komut seçenekleri

`-c test`Komut aşağıdaki seçeneklere sahiptir:

- `--test hana`  SAP HANA örneğiyle bağlantıyı sınar.

- `--test storage` yapılandırılan tüm birimlerde geçici bir depolama anlık görüntüsü oluşturup bunları kaldırarak, temel alınan depolama arabirimiyle iletişimi sınar `data` . 

- `--test all` sırayla hem hem de `hana` `storage` testlerini gerçekleştirir.

- `[--configfile <config filename>]` , özel yapılandırma dosyası adlarına izin veren isteğe bağlı bir parametredir.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>SAP HANA ile bağlantıyı denetle `azacsnap -c test --test hana`

Bu komut, yapılandırma dosyasındaki tüm HANA örnekleri için HANA bağlantısını denetler. Bu, SYSTEMDB 'ye bağlanmak için HDBuserstore kullanır ve SID bilgilerini getirir.

SSL için bu komut, aşağıdaki isteğe bağlı bağımsız değişkeni alabilir:

- `--ssl=` veritabanına şifreli bir bağlantı zorlar ve SAP HANA, ya da ile iletişim kurmak için kullanılan şifreleme yöntemini tanımlar `openssl` `commoncrypto` . Tanımlanmışsa, bu komut aynı dizinde iki dosya bulmayı bekler, bu dosyaların karşılık gelen SID 'den sonra adlandırılması gerekir. [SAP HANA iletişim IÇIN SSL kullanma](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)bölümüne bakın.

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>`azacsnap -c test --test hana`Komutun çıkışı

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Depolama ile bağlantıyı denetle `azacsnap -c test --test storage`

`azacsnap`Komutu, her bir SAP HANA örneğinin birimlerine doğru erişimi olduğunu doğrulamak üzere yapılandırılan tüm veri birimleri için bir anlık görüntü alır. Her bir dosya sistemi için anlık görüntü erişimini doğrulamak üzere her bir veri birimi için geçici bir anlık görüntü oluşturulur ve kaldırılır.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>`azacsnap -c test --test storage`Komutun çıkışı

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Azure büyük örneği için, `azacsnap -c test --test storage` komut, depolama oluşturma ve HLI SKU 'yu temsil edebilir.  Daha sonra bu bilgilere bağlı olarak, ' önyükleme ' anlık görüntülerini yapılandırma hakkında rehberlik sağlar (çıkış ile başlayan çizgiye bakın `Action:` ).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure uygulamayla tutarlı anlık görüntü aracı ile anlık görüntü yedeklemesi](azacsnap-cmd-ref-backup.md)
