---
title: Azure DevOps ile sürekli dağıtım (Önizleme)
titleSuffix: Azure Cognitive Services
description: Bu makalede, özel komutlar uygulamalarınız için sürekli dağıtımı ayarlamayı öğreneceksiniz. Sürekli dağıtım iş akışlarını desteklemek için betikleri oluşturun.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98927602"
---
# <a name="continuous-deployment-with-azure-devops"></a>Azure DevOps ile Sürekli Dağıtım

Bu makalede, özel komutlar uygulamalarınız için sürekli dağıtımı ayarlamayı öğreneceksiniz. CI/CD iş akışını desteklemeye yönelik betikler size sağlanır.

## <a name="prerequisite"></a>Önkoşul
> [!div class = "checklist"]
> * Geliştirme için özel bir komutlar uygulaması (geliştirme)
> * Üretim için özel bir komutlar uygulaması (üretim)
> * [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up) için kaydolun

## <a name="exportimportpublish"></a>Dışarı/Içeri aktarma/yayımlama

Betikler bilişsel [Hizmetler ses Yardımcısı-özel komutlarda](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)barındırılır. Bash dizinindeki betikleri deponuza kopyalayın. Aynı yolu koruduğunuzdan emin olun.

### <a name="set-up-a-pipeline"></a>İşlem hattı ayarlama 

1. **Azure DevOps-Işlem hatları** ' na gidin ve "yeni işlem hattı" na tıklayın
1. **Bağlan** bölümünde, Bu betiklerin bulunduğu deponun konumunu seçin
1. Bölüm **Seç** bölümünde deponuzu seçin
1. **Yapılandır** bölümünde "başlatıcı işlem hattı" öğesini seçin.
1. Daha sonra bir YAML dosyası içeren bir düzenleyici alacaksınız, "Steps" bölümünü bu komut dosyasıyla değiştirin.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Bu betiklerin, bu `westus2` durumda görevin bağımsız değişkenlerini güncelleştirdiğine ilişkin bölgeyi kullandığınızı varsaydığını unutmayın.

    > [!div class="mx-imgBorder"]
    > ![Bağımsız değişkenlerdeki bölge değerini vurgulayan ekran görüntüsü.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. "Kaydet ve Çalıştır" düğmesinde, açılan menüyü açın ve "Kaydet" e tıklayın

### <a name="hook-up-the-pipeline-with-your-application"></a>İşlem hattını uygulamanızla bağlama

1. İşlem hattının ana sayfasına gidin.
1. Sağ üst köşedeki açılan pencerede, işlem hattını **Düzenle**' yi seçin. Sizi bir YAML düzenleyicisine getirir. 
1. Sağ üst köşedeki "Çalıştır" düğmesinin yanındaki **değişkenler**' i seçin. **Yeni değişken**' e tıklayın.
1. Şu değişkenleri ekleyin:
    
    | Değişken | Açıklama |
    | ------- | --------------- | ----------- |
    | Sourceappıd | GELIŞTIRME uygulamasının KIMLIĞI |
    | Targetappıd | ÜRETIM uygulaması KIMLIĞI |
    | SubscriptionKey | Her iki uygulama için kullanılan abonelik anahtarı |
    | Kültür | Uygulamaların kültürü (yani, en-US) |

    > [!div class="mx-imgBorder"]
    > ![Etkinlik yükünü gönder](media/custom-commands/cicd-edit-pipeline-variables.png)

1. "Çalıştır" a tıklayın ve sonra çalışan "Iş" düğmesine tıklayın. 

    Çalıştıran görevlerin bir listesini görmeniz gerekir: "kaynak uygulamayı dışarı aktar", "hedef uygulamaya aktar" & "hedef uygulamayı eğitme ve yayımlama"

## <a name="deploy-from-source-code"></a>Kaynak koddan dağıt

Uygulamanızın tanımını bir depoda tutmak istiyorsanız, kaynak kodundaki dağıtımlar için betikler sağlıyoruz. Betikler Bash içinde olduğundan, Windows kullanıyorsanız, [Linux alt sistemini](/windows/wsl/install-win10)yüklemeniz gerekir.

Betikler bilişsel [Hizmetler ses Yardımcısı-özel komutlarda](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)barındırılır. Bash dizinindeki betikleri deponuza kopyalayın. Aynı yolu koruduğunuzdan emin olun.

### <a name="prepare-your-repository"></a>Deponuzu hazırlayın

1. Uygulamanız için bir dizin oluşturun, bizim örneğimizde "uygulamalar" adlı bir dizin oluşturun.
1. Aşağıdaki Bash betiğinin bağımsız değişkenlerini güncelleştirin ve çalıştırın. Uygulamanızın iletişim kutusu modelini myapp.jsdosyasına aktarır.
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Bağımsız değişkenler | Description |
    | ------- | --------------- | ----------- |
    | region | uygulamanın bölgesi (örn. westus2). |
    | subscriptionkey | konuşma kaynağınızın abonelik anahtarı. |
    | AppID | dışarı aktarmak istediğiniz özel komutların uygulama KIMLIĞI. |

1. Bu değişiklikleri deponuza gönderin.

### <a name="set-up-a-pipeline"></a>İşlem hattı ayarlama 

1. **Azure DevOps-Işlem hatları** ' na gidin ve "yeni işlem hattı" na tıklayın
1. **Bağlan** bölümünde, Bu betiklerin bulunduğu deponun konumunu seçin
1. Bölüm **Seç** bölümünde deponuzu seçin
1. **Yapılandır** bölümünde "başlatıcı işlem hattı" öğesini seçin.
1. Daha sonra bir YAML dosyası içeren bir düzenleyici alacaksınız, "Steps" bölümünü bu komut dosyasıyla değiştirin.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > Bu betikler, bu durumda görevlerin bağımsız değişkenlerini uygun şekilde güncelleştirmediğinden, westus2 bölgesini kullandığınızı varsayar.

1. "Kaydet ve Çalıştır" düğmesinde, açılan menüyü açın ve "Kaydet" e tıklayın

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>İşlem hattını hedef uygulamalarınızla bağlama

1. İşlem hattının ana sayfasına gidin.
1. Sağ üst köşedeki açılan pencerede, işlem hattını **Düzenle**' yi seçin. Sizi bir YAML düzenleyicisine getirir. 
1. Sağ üst köşedeki "Çalıştır" düğmesinin yanındaki **değişkenler**' i seçin. **Yeni değişken**' e tıklayın.
1. Şu değişkenleri ekleyin:

    | Değişken | Açıklama |
    | ------- | --------------- | ----------- |
    | Targetappıd | ÜRETIM uygulaması KIMLIĞI |
    | SubscriptionKey | Her iki uygulama için kullanılan abonelik anahtarı |
    | Kültür | Uygulamaların kültürü (yani, en-US) |

1. "Çalıştır" a tıklayın ve sonra çalışan "Iş" düğmesine tıklayın.
    Çalıştıran görevlerin bir listesini görmeniz gerekir: "uygulama alma" & "uygulamayı eğitme ve yayımlama"

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da örneklere bakın](https://aka.ms/speech/cc-samples)
