---
title: Deneme-Özel Karar Alma Hizmeti
titlesuffix: Azure Cognitive Services
description: Bu makale, Özel Karar Alma Hizmeti ile deneme için bir kılavuzdur.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: e6e8e7d0d5b969464ba9183ccae9080f58f786a0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707292"
---
# <a name="experimentation"></a>Deneme

[Bağlamsal Bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/)teorik ' i takip eden bir bağlamda sürekli olarak özel karar alma hizmeti, bir işlem yapar ve seçilen eylem için bir ödül sunar. İçerik kişiselleştirmesi örneği: bağlam bir kullanıcıyı açıklar, eylemler aday öykülerdir ve kullanıcının önerilen hikayeyi ne kadar beğendiğini ölçer.

Özel Karar Alma Hizmeti bağlamlardan eylemlere eşlendiğinden bir ilke üretir. Belirli bir hedef ilkeyle, beklenen Reward değerini bilmeniz gerekir. Yeniden bir ilkeyi tahmin etmenin bir yolu, bir ilkenin çevrimiçi olarak kullanılması ve eylemler (örneğin, kullanıcılara hikaye önerin) seçemesidir. Ancak, bu tür çevrimiçi değerlendirme iki nedenden dolayı maliyetli olabilir:

* Kullanıcıları test edilmemiş, deneysel bir ilkeye sunar.
* Birden çok hedef ilkeyi değerlendirmek için ölçeklendirmez.

İlke dışı değerlendirme, alternatif bir paradigma sahiptir. Günlüğe kaydetme ilkesini izleyen mevcut bir çevrimiçi sistem günlüklerinizin varsa, ilke dışı değerlendirme, yeni hedef ilkelerin beklenen yeniden sürümlerini tahmin edebilir.

Deneme günlük dosyasını kullanarak en yüksek tahmini ve beklenen bir şekilde ilkeyi bulmak için arar. Hedef ilkeler [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) bağımsız değişkenlerine göre parametrelenir. Varsayılan modda betik, öğesine `--base_command`ekleyerek çeşitli Vowpal Wabbit bağımsız değişkenlerini sınar. Komut aşağıdaki eylemleri gerçekleştirir:

* Giriş dosyasının ilk `--auto_lines` satırlarından Özellikler ad alanlarını otomatik olarak algılar.
* Hyper-Parameters üzerinde (`learning rate`, `L1 regularization`, ve `power_t`) ilk tarama gerçekleştirir.
* İlke değerlendirmesini `--cb_type` (ters eğilimini Score (`ips`) veya her ikisi de sağlam (`dr`) test eder. Daha fazla bilgi için bkz. [bağlamsal bandıt örneği](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Test marjları.
* İkinci dereceden etkileşim özelliklerini sınar:
   * **deneme yanılma-zorlama aşaması**: Tüm birleşimleri çiftler veya `--q_bruteforce_terms` daha az olacak şekilde sınar.
   * **doyumsuz aşaması**: Her bir iyileştirene `--q_greedy_stop` kadar en iyi çifti ekler.
* Hyper-Parameters üzerinde ikinci bir tarama gerçekleştirir (`learning rate`, `L1 regularization`, ve `power_t`).

Bu adımları denetleyen parametreler bazı Vowpal Wabbit bağımsız değişkenlerini içerir:
- Örnek Işleme seçenekleri:
  - paylaşılan ad alanları
  - eylem ad alanları
  - marguinal ad alanları
  - ikinci dereceden Özellikler
- Kural seçeneklerini Güncelleştir
  - öğrenme oranı
  - L1 düzenleme
  - t güç değeri

Yukarıdaki bağımsız değişkenlerin derinlemesine bir açıklaması için bkz. [Vowpal Wabbit komut satırı bağımsız değişkenleri](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Önkoşullar
- Vowpal Wabbit: Yolunuza yüklendi.
  - Windows: [Yükleyiciyi kullanın`.msi` ](https://github.com/eisber/vowpal_wabbit/releases).
  - Diğer platformlar: [Kaynak kodunu alın](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Yolunuza yüklendi.
- Sayısal tuş: Seçtiğiniz paket yöneticisini kullanın.
- *Microsoft/MWT-DS* deposu: Depoyu [kopyalayın](https://github.com/Microsoft/mwt-ds).
- Karar hizmeti JSON günlük dosyası: Varsayılan olarak, temel komutu içerir `--dsjson`ve bu, giriş veri dosyasının karar verme hizmeti JSON ayrıştırması etkinleştirilir. [Bu biçime bir örnek alın](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Kullanım
Aşağıdaki kodda açıklandığı gibi `Experimentation.py` ilgili bağımsız değişkenlerle adresine gidin veçalıştırın:`mwt-ds/DataScience`

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Sonuçların günlüğü *MWT-DS/DataScience/denemeleri. csv* dosyasına eklenir.

### <a name="parameters"></a>Parametreler
| Girdi | Açıklama | Varsayılan |
| --- | --- | --- |
| `-h`, `--help` | Yardım iletisini göster ve çıkış. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Veri dosyası yolu (`.json` veya `.json.gz` biçim-her satır bir `dsjson`' dir). | Gerekli |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Base Vowpal Wabbit komutu.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Kullanılacak paralel işlem sayısı. | Mantıksal işlemciler |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Paylaşılan özellik ad alanları (örneğin, `abc` `b`ad alanları `a`, ve `c`).  | Veri dosyasından otomatik algıla |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Eylem özelliği ad alanları. | Veri dosyasından otomatik algıla |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Marguinal Özellik ad alanları. | Veri dosyasından otomatik algıla |  
| `--auto_lines AUTO_LINES` | Özellikler ad alanlarını otomatik olarak algılamak için taranacak veri dosyası satırı sayısı. | `100` |  
| `--only_hp` | Yalnızca Hyper-Parameters üzerinde tarama (`learning rate`, `L1 regularization`, ve `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Öğrenme oranı aralığı pozitif değerler `min,max,steps`olarak. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | L1 düzenleme aralığı pozitif değerler `min,max,steps`olarak değişir. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Pozitif değerler `min,max,step`olarak Power_t aralığı. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Deneme yanılma aşamasından test etmek için ikinci dereceden çifte çift sayısı. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | İyileştirmesiz, karesel doyumsuz arama aşamasının durdurulmasından sonra, iyileştirmeler olmadan yuvarlar. | `3` |  

### <a name="examples"></a>Örnekler
Önceden ayarlanmış varsayılan değerleri kullanmak için:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Equivalently, Vowpal Wabbit `.json.gz` dosyaları da alabilir:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Yalnızca Hyper-Parameters üzerinde tarama yapmak için`learning rate`( `L1 regularization`, ve `power_t`, adım 2 ' den sonra durduruluyor):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
