---
title: Azure yönetilen HSM için tam yedekleme/geri yükleme ve seçmeli geri yükleme
description: Bu belgede tam yedekleme/geri yükleme ve seçmeli geri yükleme açıklanmaktadır
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e926dcd4b05d137c7927bdfe5221923d25d4670c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093497"
---
# <a name="full-backup-and-restore"></a>Tam yedekleme ve geri yükleme

> [!NOTE]
> Bu özellik yalnızca yönetilen HSM kaynak türü için kullanılabilir.

Yönetilen HSM, tüm anahtarlar, sürümler, öznitelikler, Etiketler ve rol atamaları dahil olmak üzere HSM 'nin tüm içeriğinin tam yedeklemesini oluşturmayı destekler. Yedekleme, HSM 'nin güvenlik etki alanıyla ilişkili şifreleme anahtarlarıyla şifrelenir.

Yedekleme bir veri düzlemi işlemidir. Yedekleme işleminin başlatılmasına yönelik çağıran, **Microsoft. Keykasası/managedHsm/Backup/start/Action** veri eylemini gerçekleştirme iznine sahip olmalıdır.

Yalnızca aşağıdaki yerleşik rollerin tam yedekleme gerçekleştirme izni vardır:
- Yönetilen HSM Yöneticisi
- Yönetilen HSM Yedeklemesi

Tam yedeklemeyi yürütmek için aşağıdaki bilgileri sağlamalısınız:
- HSM adı veya URL'si
- Depolama hesabı adı
- Depolama hesabı blob depolama kapsayıcısı
- İzinlerle depolama kapsayıcısı SAS belirteci `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Tam yedekleme

Yedekleme uzun süredir çalışan bir işlemdir, ancak hemen bir Iş KIMLIĞI döndürülür. Bu Iş KIMLIĞINI kullanarak yedekleme işleminin durumunu kontrol edebilirsiniz. Yedekleme işlemi, aşağıdaki adlandırma düzeniyle belirtilen kapsayıcı içinde bir klasör oluşturur **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** ; burada HSM_NAME yedeklenen YÖNETILEN HSM 'nin adı, yyyy, aa, gg, hh, mm, mm, SS ise, Yedekleme komutunun alındığı tarih, ay, tarih, saat, dakika ve UTC 'de tarih/saat şeklindedir.

Yedekleme devam ederken, bazı HSM bölümleri yedekleme işlemini gerçekleştirirken meşgul olacağı için HSM tam üretilen iş üzerinde çalışmayabilir.

```azurecli-interactive
# time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Tam geri yükleme

Tam geri yükleme, tüm anahtarlar, sürümler, öznitelikler, Etiketler ve rol atamaları dahil olmak üzere önceki bir yedeklemeyle HSM 'nin içeriğini tamamen geri yüklemenize olanak tanır. HSM 'de Şu anda depolanan her şey silinecek ve kaynak yedeklemenin oluşturulduğu sırada bulunduğu duruma geri döndürülecek.

> [!IMPORTANT]
> Tam geri yükleme çok zararlı ve kesintiye uğratan bir işlemdir. Bu nedenle, bir işlem gerçekleştirilmeden önce son 30 dakika içinde tam bir yedeklemenin tamamlanması zorunludur `restore` .

Restore bir veri düzlemi işlemidir. Geri yükleme işlemini başlatan çağıranın, **Microsoft. Keykasası/managedHsm/restore/start/Action** veri eylemini gerçekleştirme izni olmalıdır. Yedeklemenin oluşturulduğu kaynak HSM ve geri yüklemenin gerçekleştirileceği hedef HSM 'nin aynı güvenlik etki alanına sahip **olması gerekir** . [YÖNETILEN HSM güvenlik etki alanı hakkında](security-domain.md)daha fazla bilgi.

Tam geri yükleme yürütmek için aşağıdaki bilgileri sağlamanız gerekir:
- HSM adı veya URL'si
- Depolama hesabı adı
- Depolama hesabı blobu kapsayıcısı
- İzinlerle depolama kapsayıcısı SAS belirteci `rl`
- Kaynak yedeklemenin depolandığı depolama kapsayıcısı klasörü adı

Restore uzun süredir çalışan bir işlemdir, ancak hemen bir Iş KIMLIĞI döndürülür. Bu Iş KIMLIĞINI kullanarak geri yükleme işleminin durumunu kontrol edebilirsiniz. Geri yükleme işlemi devam ederken, HSM bir geri yükleme moduna girer ve tüm veri düzlemi komutu (geri yükleme durumunu denetle hariç) devre dışı bırakılır.

```azurecli-interactive
#### time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>HSM 'yi geri yükle

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Sonraki Adımlar
- Bkz. [Azure CLI kullanarak yönetilen BIR HSM 'Yi yönetme](key-management.md).
- [YÖNETILEN HSM güvenlik etki alanı](security-domain.md) hakkında daha fazla bilgi edinin
