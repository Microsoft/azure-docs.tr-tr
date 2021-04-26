---
title: include dosyası
description: include dosyası
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 02/08/2021
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 3ee44509997a16fc7f06fd5a24e473d7011bde40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99974472"
---
#### <a name="process-automation"></a>Süreç otomasyonu

| Kaynak | Sınır |Notlar|
| --- | --- |---|
| Her Azure Otomasyonu hesabı (zamanlanmamış iş değil) için her 30 saniyede gönderilen en fazla yeni iş sayısı |100 |Bu sınıra ulaşıldığında, bir iş oluşturmaya yönelik sonraki istekler başarısız olur. İstemci bir hata yanıtı alır.|
| Otomasyon hesabı başına aynı zamanda aynı anda çalışan iş sayısı üst sınırı (zamanlanmamış iş) |200 |Bu sınıra ulaşıldığında, bir iş oluşturmaya yönelik sonraki istekler başarısız olur. İstemci bir hata yanıtı alır.|
| 30 günlük bir dönem için iş meta verilerinin maksimum depolama alanı boyutu | 10 GB (yaklaşık 4.000.000 iş)|Bu sınıra ulaşıldığında, bir iş oluşturmaya yönelik sonraki istekler başarısız olur. |
| En fazla iş akışı sınırı|1 MIB|Tek bir akış 1 MiB 'den büyük olamaz.|
| Otomasyon hesabı başına her 30 saniyede bir içeri aktarılabilen en fazla modül sayısı |5 ||
| Modülün en büyük boyutu |100 MB ||
| Bir düğüm yapılandırma dosyasının en büyük boyutu | 1 MB | Durum yapılandırması için geçerlidir |
| İş çalışma süresi, ücretsiz katman |her takvim ayında abonelik başına 500 dakika ||
| Sandbox<sup>1</sup> başına izin verilen maksimum disk alanı miktarı |1 GB |Yalnızca Azure korumalı alanlar için geçerlidir.|
| Bir korumalı alan<sup>1</sup> ' e verilen maksimum bellek miktarı |400 MB |Yalnızca Azure korumalı alanlar için geçerlidir.|
| Sandbox başına izin verilen en fazla ağ yuvası sayısı<sup>1</sup> |1.000 |Yalnızca Azure korumalı alanlar için geçerlidir.|
| Runbook başına izin verilen en fazla çalışma zamanı<sup>1</sup> |3 saat |Yalnızca Azure korumalı alanlar için geçerlidir.|
| Bir abonelikteki en fazla Otomasyon hesabı sayısı |Sınır yok ||
| Otomasyon hesabı başına en fazla karma çalışan grubu sayısı|4.000||
|Tek bir karma runbook çalışanı üzerinde çalıştırılabilen maksimum eşzamanlı iş sayısı|50 ||
| Maksimum runbook işi parametre boyutu   | 512 kilobayt||
| En fazla runbook parametreleri   | 50|50 parametre sınırına ulaşırsanız, bir parametreye JSON veya XML dizesi geçirebilir ve runbook ile ayrıştırılabilir.|
| En fazla Web kancası yükü boyutu |  512 kilobayt|
| İş verilerinin tutulacağı maksimum gün sayısı|30 gün|
| En yüksek PowerShell iş akışı durum boyutu |5 MB| Checkişaretleme iş akışı sırasında PowerShell iş akışı runbook 'ları için geçerlidir.|

<sup>1</sup> Korumalı alan, birden çok iş tarafından kullanılabilen paylaşılan bir ortamdır. Aynı korumalı alanı kullanan işler, korumalı alanın kaynak sınırlamalarına göre bağlanır.

#### <a name="change-tracking-and-inventory"></a>Değişiklik İzleme ve Stok

Aşağıdaki tabloda değişiklik izleme için makine başına izlenen öğe sınırları gösterilmektedir.

| **Kaynak** | **Sınırlı**| **Notlar** |
|---|---|---|
|Dosya|500||
|Kayıt Defteri|250||
|Windows yazılımı|250|Yazılım güncelleştirmelerini içermez.|
|Linux paketleri|1.250||
|Hizmetler|250||
|İnin|250||

#### <a name="update-management"></a>Güncelleştirme Yönetimi

Aşağıdaki tabloda Güncelleştirme Yönetimi sınırları gösterilmektedir.

| **Kaynak** | **Sınırlı**| **Notlar** |
|---|---|---|
|Güncelleştirme dağıtımı başına makine sayısı|1000||