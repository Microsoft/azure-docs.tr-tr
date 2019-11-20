---
title: Project Acoustics genel bakış
titlesuffix: Azure Cognitive Services
description: Project Acoustics, etkileşimli tasarım denetimleriyle, bakmış dalga fizik benzetimini tümleştirerek, 3B etkileşimli deneyimler için bir Acoustics altyapısıdır.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351142"
---
# <a name="what-is-project-acoustics"></a>Project Acoustics nedir?
Project Acoustics, 3B etkileşimli deneyimler için bir Wave Acoustics altyapısıdır. El ile bölge işaretlemesi veya CPU yoğun rayizlemenin gerekli olması gerekmeden, karmaşık sahneler içindeki occlusiyon, düşüşe, portallama ve geri alma etkileri gibi dalga efektlerini modellemez. Ayrıca oyun altyapısı ve ses ara yazılım tümleştirmesi de içerir. Project Acoustics ' fanlar, statik aydınlatmaya benzerdir: fiziksel bir taban çizgisi sağlamak için bakmalı ve sanal dünyadaki Acoustics hedeflerinizi karşılamak için açıklayıcı tasarım denetimleriyle basit bir çalışma zamanı kullanın.

![War 4 Acoustics voxars gösteren Gears 'ın ekran görüntüsü](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Etkileşimli Acoustics için Wave fizik kullanma
Ray tabanlı Acoustics yöntemleri, tek bir kaynaktan dinleyici Ray cast kullanarak tahmini veya birkaç ışına sahip yerel sahne birimini tahmin ederek sürücü yankı düzeyini kontrol edebilir. Ancak, bir Pebble OCC, bir Boulder kadar olduğu için bu teknikler güvenilir olmayabilir. Işın, nesnelerin etrafında ses çıkarımları için bir hesap değil, korkma olarak bilinirdi. Project Acoustics ' simülasyonu, bu etkileri bir dalga tabanlı benzetim kullanarak yakalar. Acoustics daha öngörülebilir, doğru ve sorunsuz.

Project Acoustics ' anahtar yeniliği, geleneksel ses tasarımı kavramlarıyla birlikte gerçek ses dalgası tabanlı akustik simülasyonu sağlar. Simülasyon sonuçlarını occlusiyon, portallama ve Reverb için geleneksel ses DSP parametrelerine dönüştürür. Tasarımcı bu çeviri işlemi üzerinde denetimleri kullanır. Project Acoustics 'ın arkasındaki temel teknolojiler hakkında daha fazla bilgi için [Araştırma Projesi sayfasını](https://www.microsoft.com/en-us/research/project/project-triton/)ziyaret edin.

![Sahne aracılığıyla yatay 2B bir dalga yayma dilimini gösteren animasyon](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>GDC 2019 'den video sunumu (~ 30 dakika)
[![Project Acoustics videosu](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Videoyu oynatmak için tıklayın")

## <a name="setup"></a>Kurulum
[Project Acoustics Unity tümleştirmesi](unity-integration.md) , sürükle ve bırak Ile bir Unity ses altyapısı eklentisi içerir. Her ses nesnesine bir Project Acoustics C# Controls bileşeni ekleyerek Unity ses kaynağı denetimlerini güçlendirin.

[Project Acoustics Unreal Integration](unreal-integration.md) , Unreal için düzenleyici ve oyun eklentileri ve bir Wwise karıştırıcı eklentisi içerir. Özel bir ses bileşeni, canlı Acoustics tasarım denetimleriyle gerçek olmayan tanıdık Wwise işlevselliğini genişletir. Tasarım denetimleri, karıştırıcı eklentisi üzerinde Wwise 'ta da sunulur.

## <a name="workflow"></a>İş akışı
* **Ön Bake:** Acoustics 'e hangi geometrinin yanıt verdiğini seçerek, örneğin, Light shafts yok sayarak başlayın. Sonra otomatik malzeme atamalarını düzenleyin ve dinleyici örneklemesini kılavuza kadar gezinme alanı seçin. Reverb/Portal/Oda bölgeleri için el ile biçimlendirme yoktur.
* **Hazırlama** Bir çözümleme adımı yerel olarak çalıştırılır, bu, yukarıdaki seçimlere bağlı olarak sahne üzerinde voxelileştirme ve diğer geometrik analizler yapar. Sonuçlar, sahne kurulumunun doğrulanması için düzenleyicide görselleştirilir. Hazırlama gönderimi sırasında, Voxel verileri Azure 'a gönderilir ve bir Acoustics oyun varlığını geri alırsınız.
* **Çalışma zamanı** Varlığı, düzeyinize yükleyin ve Acoustics dinlemek için hazırsınız demektir. Kaynak başına ayrıntılı denetimleri kullanarak Acoustics canlı düzenleyici 'yi tasarlayın. Denetimler Ayrıca düzey komut dosyası içinden de yönetilebilir.

## <a name="runtime-platforms"></a>Çalışma zamanı platformları
Project Acoustics çalışma zamanı eklentileri şu anda aşağıdaki platformlara dağıtılabilir:
* Windows
* macOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Düzenleyici platformları
Project Acoustics Düzenleyicisi eklentisi aşağıdaki platformlar için kullanılabilir:
* Windows
* MacOS (yalnızca Unity)

## <a name="download"></a>İndirme
* [Project Acoustics Unity eklentisi ve örnekleri](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Proje Acoustics Unreal & Wwise eklentileri ve örnekleri](https://www.microsoft.com/download/details.aspx?id=58090)
  * Xbox ikilileri ve diğer destek için [Forum](https://github.com/microsoft/ProjectAcoustics/issues)aracılığıyla bizimle iletişim kurun.

## <a name="contact-us"></a>Bizimle iletişim kurun
* [Project Acoustics tartışma ve sorun raporlama](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Sonraki adımlar
* [Unity Için proje Acoustics hızlı başlangıç](unity-quickstart.md) veya [Unreal](unreal-quickstart.md) için deneme
* [Project Acoustics 'in ses tasarımı felsefesini](design-process.md) keşfet

