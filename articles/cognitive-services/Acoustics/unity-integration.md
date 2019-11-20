---
title: Project Acoustics Unity tümleştirmesi ve dağıtımı
titlesuffix: Azure Cognitive Services
description: Bu makalede, Project Acoustics Unity eklentisinin Unity projenize nasıl tümleştirileceği açıklanır.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243006"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity tümleştirmesi
Bu makalede, Project Acoustics Unity eklentisinin Unity projenize nasıl tümleştirileceği açıklanır.

Yazılım gereksinimleri:
* Windows için [Unity 2018.2 +](https://unity3d.com)
* [Project Acoustics Unity paketi](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Eklentiyi içeri aktarma
1. Acoustics UnityPackage 'ı projenize aktarın. 
 Unity 'de **varlıklar**' a  > **paketi içeri aktar** > **özel paket**' e gidin.

    ![Unity Içeri aktarma paketi menüsü](media/import-package.png)  

1. **Projectacoustik. unitypackage**' ı seçin.

1. Unity paketini projenize bütünleştirmek için **Içeri aktar** düğmesini seçin.

    ![Unity Içeri aktarma paketi iletişim kutusu](media/import-dialog.png)  

Eklentiyi mevcut bir projeye içeri aktarıyorsanız, projeniz proje kökünde bir *MCS. rsp* dosyasına sahip olabilir. Bu dosya C# derleyicinin seçeneklerini belirtir. Bu dosyanın içeriğini Project Acoustics eklentisiyle birlikte gelen MCS. rsp dosyası ile birleştirin.

## <a name="enable-the-plug-in"></a>Eklentiyi etkinleştirin
Acoustics araç setinin hazırlama kısmı .NET 4 gerektirir. *x* Scripting Runtime sürümü. Paket içeri aktarma Unity oynatıcı ayarlarınızı güncelleştirir. Bu ayarın etkili olması için Unity 'yi yeniden başlatın.

![Unity oynatıcı ayarları paneli](media/player-settings.png)

![.NET 4,5 ile Unity oynatıcı ayarları paneli seçili](media/net45.png)

## <a name="set-up-audio-dsp"></a>Ses DSP 'si ayarlama
Project Acoustics, Unity Audio Engine spatializer çerçevesi ile tümleşen ses çalışma zamanı DSP 'sini içerir. Hem HRTF tabanlı hem de birlikte bulunan istenmeyen kullanım özelliğini içerir. Proje Acoustics DSP 'sini etkinleştirmek için, Unity ses ayarlarını açmak üzere  > **Proje ayarlarını** **Düzenle** > **Ses** seçeneğine gidin. Projeniz için **Spatializer eklentisi** olarak **Project Acoustics** ' ı seçin. **DSP arabellek boyutunun** *en iyi performansa*ayarlandığından emin olun.

![Unity proje ayarları menüsü](media/project-settings.png)  

![Project Acoustics spatializer seçiliyken Unity Spatializer ayarları paneli](media/choose-spatializer.png)

Ardından, ses karıştırıcı (**pencere** > **Ses karıştırıcı**) öğesini açın. Tek bir grup ile en az bir karıştırıcıya sahip olduğunuzdan emin olun. Bir tane yoksa, **karıştırıcılar**' nin sağındaki **+** düğmesini seçin. Etkiler bölümünde kanal şeridinin altına sağ tıklayın ve **Microsoft Acoustics karıştırıcı** efektini ekleyin. Tek seferde yalnızca bir Project Acoustics Mixer desteklenir.

![Project Acoustics Mixer 'i barındıran Unity ses karıştırıcı](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Acoustics 'i ses kaynaklarında etkinleştir
Ses kaynağı oluşturma: AudioSource Inspector panelinin altındaki **maça** onay kutusunu seçin. **Uzamsal Blend** 'In tam *3B*olarak ayarlandığından emin olun.  

![Unity ses kaynağı paneli](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akustik tasarımı etkinleştir
Ek kaynak Tasarım parametrelerini etkinleştirmek için sahnedeki bir ses kaynağına *AcousticsAdjust* betiğini ekleyin: **Bileşen Ekle** ' yi seçin ve **betikler** > **Acoustics ayarla ' yı**seçin.

![Unity AcousticsAdjust betiği](media/acoustics-adjust.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Unity Için Project Acoustics ile sahne bakın](unity-baking.md).
* [Bir Azure Batch hesabı oluşturarak](create-azure-account.md) , sahneye buluta bakın.
* [Project Acoustics Unity tasarım sürecini](unity-workflow.md)gezin.
