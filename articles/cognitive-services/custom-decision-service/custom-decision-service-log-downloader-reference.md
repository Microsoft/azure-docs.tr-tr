---
title: LogDownloader-Özel Karar Alma Hizmeti
titlesuffix: Azure Cognitive Services
description: Azure Özel Karar Alma Hizmeti tarafından üretilen günlük dosyalarını indirin.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: 33cc0d0dcf16ff82ac128507566427e123020236
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707238"
---
# <a name="logdownloader"></a>LogDownloader

Azure Özel Karar Alma Hizmeti tarafından üretilen günlük dosyalarını indirin ve deneme tarafından kullanılan *. gz* dosyalarını oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Python 3: Yolunuza yüklendi. Büyük dosyaları işlemek için 64 bitlik bir sürüm önerilir.
- *Microsoft/MWT-DS* deposu: Depoyu [kopyalayın](https://github.com/Microsoft/mwt-ds).
- *Azure-Storage-blob* paketi: Yükleme ayrıntıları için [Python için Microsoft Azure depolama kitaplığı](https://github.com/Azure/azure-storage-python#option-1-via-pypi)' na gidin.
- *MWT-DS/DataScience/DS. config dosyasına*Azure Storage Bağlantı dizenizi girin: *My_app_id: my_connectionString* şablonunu izleyin. Birden çok `app_id`belirtebilirsiniz. Çalıştırdığınızda `LogDownloader.py`, giriş `app_id` içinde `ds.config` bulunmazsa`LogDownloader.py` bağlantı dizesini`$Default` kullanır.

## <a name="usage"></a>Kullanım

Aşağıdaki kodda açıklandığı gibi `LogDownloader.py` ilgili bağımsız değişkenlerle adresine gidin veçalıştırın:`mwt-ds/DataScience`

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametreler

| Girdi | Açıklama | Varsayılan |
| --- | --- | --- |
| `-h`, `--help` | Yardım iletisini göster ve çıkış. | |
| `-a APP_ID`, `--app_id APP_ID` | Uygulama KIMLIĞI (yani, Azure Storage blob kapsayıcı adı). | Gerekli |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Veri indirmek için temel dizin (bir alt klasör oluşturulur).  | Gerekli |
| `-s START_DATE`, `--start_date START_DATE` | Yükleme başlangıç tarihi (dahil), *yyyy-aa-gg* biçiminde. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | İndirme bitiş tarihi (dahil), *yyyy-aa-gg* biçiminde. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Kullanılacak üzerine yazma modu. | |
| | `0`: Hiçbir şekilde üzerine yazma; kullanıcıdan Blobların Şu anda kullanılıp kullanılmadığını sorun. | Varsayılan |
| | `1`: Dosyalar farklı boyutlarda olduğunda veya Blobların Şu anda kullanıldığı durumlarda kullanıcıdan nasıl devam edeceğine sorun. | |
| | `2`: Her zaman üzerine yaz; kullanılmakta olan Blobları indirin. | |
| | `3`: Hiçbir şekilde üzerine yazılmaz ve boyut daha büyükse, sormadan ekleyin; kullanılmakta olan Blobları indirin. | |
| | `4`: Hiçbir şekilde üzerine yazılmaz ve boyut daha büyükse, sormadan ekleyin; kullanılmakta olan Blobları atlayın. | |
| `--dry_run` | İndirilmeden hangi Blobların indirileceğini yazdır. | `False` |
| `--create_gzip` | Vowpal Wabbit için bir *gzip* dosyası oluşturun. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Bir dosyanın şu anda kullanımda olup olmadığını algılamak için saniye cinsinden zaman penceresi. | `3600`sn (`1` saat) |
| `--verbose` | Daha fazla ayrıntı yazdır. | `False` |
| `-v VERSION`, `--version VERSION` | Kullanılacak günlük yükleyici sürümü. | |
| | `1`: Açıklanmamış Günlükler için (yalnızca geriye dönük uyumluluk için). | Kullanım Dışı |
| | `2`: Tanıtım günlükleri için. | Varsayılan |

### <a name="examples"></a>Örnekler

Azure Storage blob kabınızda tüm verilerin indiriyinin bir kuru çalıştırması için aşağıdaki kodu kullanın:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Yalnızca 1 Ocak 2018 `overwrite_mode=4`' den itibaren oluşturulan günlükleri indirmek için aşağıdaki kodu kullanın:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

İndirilen tüm dosyaları birleştiren bir *gzip* dosyası oluşturmak için aşağıdaki kodu kullanın:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
