---
title: Windows üzerinde ses yardımcıları-yukarıdaki kilit uygulama yönergeleri
titleSuffix: Azure Cognitive Services
description: Ses etkinleştirme ve daha üst düzey kilit özelliklerini bir ses Aracısı uygulaması için uygulama yönergeleri.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939860"
---
# <a name="implementing-voice-assistants-on-windows"></a>Windows 'da sesli yardımcılar uygulama

Bu kılavuzda, Windows üzerinde bir ses Yardımcısı oluşturmaya yönelik önemli uygulama ayrıntıları açıklanmaktadır.

## <a name="implementing-voice-activation"></a>Ses etkinleştirme uygulama

[Ortamınızı ayarladıktan](how-to-windows-voice-assistants-get-started.md) ve [Ses etkinleştirmenin nasıl çalıştığını](windows-voice-assistants-overview.md#how-does-voice-activation-work)öğrendikten sonra, kendi ses Yardımcısı uygulamanız için ses etkinleştirme uygulamaya başlayabilirsiniz.

### <a name="registration"></a>Kayıt

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Mikrofonun kullanılabilir ve erişilebilir olduğundan emin olun, sonra durumunu izleyin

MVA 'nın, bir ses etkinleştirmesini algılayabilmesi için bir mikrofonun mevcut ve erişilebilir olması gerekir. Sırasıyla mikrofon gizlilik erişimi, cihaz varlığı ve cihaz durumu (birim ve sessiz gibi) için denetim sağlamak üzere [Appcapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [Deviceizleyici](/uwp/api/windows.devices.enumeration.devicewatcher)ve [MediaCapture](/uwp/api/windows.media.capture.mediacapture) sınıflarını kullanın.

### <a name="register-the-application-with-the-background-service"></a>Uygulamayı arka plan hizmetine kaydetme

MVA 'nın uygulamayı arka planda başlatması için, uygulamanın arka plan hizmetine kayıtlı olması gerekir. Arka plan hizmeti kaydı için [buradaki](/windows/uwp/launch-resume/register-a-background-task)tam kılavuza bakın.

### <a name="unlock-the-limited-access-feature"></a>Sınırlı erişim özelliğinin kilidini aç

Ses Yardımcısı özelliğinin kilidini açmak için Microsoft tarafından sağlanmış sınırlı erişim özelliği anahtarınızı kullanın. Bunu yapmak için Windows SDK ' den [Limitedaccessfeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) sınıfını kullanın.

### <a name="register-the-keyword-for-the-application"></a>Uygulamanın anahtar sözcüğünü kaydetme

Uygulamanın kendisini, anahtar sözcük modelini ve dilini Windows ile kaydetmesi gerekir.

Anahtar sözcük algılayıcısı ' nı alarak başlayın. Bu örnek kodda, ilk algılayıcısının alınması, ancak içinden seçim yaparak belirli bir algılayıcı seçebilirsiniz `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Activationsignaldetektörü nesnesini aldıktan sonra, `ActivationSignalDetector.CreateConfigurationAsync` anahtar sözcüğünü kaydetmek ve uygulamanızı almak için kendi metodunu SINYAL kimliği, model kimliği ve görünen ad ile çağırın `ActivationSignalDetectionConfiguration` . Sinyal ve model kimlikleri, geliştirici tarafından karar alan GUID 'ler olmalıdır ve aynı anahtar sözcük için tutarlı kalır.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Ses etkinleştirme ayarının etkin olduğunu doğrulama

Ses etkinleştirme 'yi kullanmak için bir kullanıcının, sistemi için ses etkinleştirmeyi etkinleştirmesi ve uygulamaları için ses etkinleştirmesini etkinleştirmesi gerekir. Ayarı Windows ayarları 'nda "ses etkinleştirme gizlilik ayarları" altında bulabilirsiniz. Uygulamanızdaki ses etkinleştirme ayarının durumunu denetlemek için, `ActivationSignalDetectionConfiguration` anahtar sözcüğünü kaydeden öğesinin örneğini kullanın. ' Deki [kullanılabilirlik bilgisi](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) alanı, `ActivationSignalDetectionConfiguration` ses etkinleştirme ayarının durumunu açıklayan bir sabit listesi değeri içerir.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Uygulamayı MVA sistemiyle kaydetmek için bir konuşma oturumu alın

, `ConversationalAgentSession` Uygulamanızın Windows 'u uygulama durumuyla (boşta, algılama, dinleme, çalışma, konuşma) güncelleştirmesini ve ekran kilitleme gibi etkinleştirme algılaması ve sistem durumu değişiklikleri gibi olayları almasını sağlayan bir Windows SDK sınıftır. AgentSession 'un bir örneğini almak, uygulamayı etkinleştirilebilir as Voice ile Windows 'a kaydetmeye da olanak sağlar. İçin bir başvuru sürdürmek en iyi uygulamadır `ConversationalAgentSession` . Oturumu almak için API 'yi kullanın `ConversationalAgentSession.GetCurrentSessionAsync` .

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>İki etkinleştirme sinyalini dinleyin: OnBackgroundActivated ve Onsignalalgılandı

Windows, iki şekilde bir anahtar sözcük algıladığında uygulamanızı işaret eder. Uygulama etkin değilse (yani, atılmış olmayan bir örneğine başvurunuz yoksa `ConversationalAgentSession` ), uygulamanızı başlatır ve uygulamanızın App. xaml. cs dosyasında OnBackgroundActivated yöntemini çağırır. Olay bağımsız değişkenleri ' `BackgroundActivatedEventArgs.TaskInstance.Task.Name` alanı "AgentBackgroundTrigger" dizesiyle eşleşiyorsa, uygulama başlatması ses etkinleştirme tarafından tetiklendi. Uygulama, bu yöntemi geçersiz kılmalıdır ve şimdi etkin olan Windows 'a işaret etmek için bir konuşma bir örnek alın. Uygulama etkin olduğunda, Windows, olayı kullanarak bir ses etkinleştirme oluşumuna işaret eder `ConversationalAgentSession.OnSignalDetected` . ' İ alır almaz bu olaya bir olay işleyicisi ekleyin `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Anahtar sözcük doğrulama

Ses Aracısı uygulaması sesle etkinleştirildikten sonra, bir sonraki adım anahtar sözcük algılamanın geçerli olduğunu doğrulamasıdır. Windows anahtar sözcük doğrulaması için bir çözüm sağlamaz, ancak sesli yardımcılar 'ın varsayılan etkinleştirmesinden sese erişmesine ve kendi kendine doğrulama işleminin tamamlanmasına izin verir.

### <a name="retrieve-activation-audio"></a>Etkinleştirme sesini al

Bir [Audiograph](/uwp/api/windows.media.audio.audiograph) oluşturun ve ' a geçirin `CreateAudioDeviceInputNodeAsync` `ConversationalAgentSession` . Bu işlem *, anahtar sözcüğünün algılanmadan önce*, sesin ses arabelleğini yaklaşık 3 saniye sonra başlayacak şekilde yükler. Bu ek önde gelen ses, çok sayıda anahtar sözcük uzunluğu ve hoparlör hızını karşılamak için eklenmiştir. Ardından ses grafiğindeki miktar ' dan [başlatılan](/uwp/api/windows.media.audio.audiograph.quantumstarted) olayı işleyin.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Note: ses arabelleğine eklenen önde gelen ses, anahtar sözcük doğrulamanın başarısız olmasına neden olabilir. Bu sorunu onarmak için, ses arabelleğinin başlangıcını, anahtar sözcük doğrulaması için göndermeden önce kırpın. Bu ilk kırpma, her bir yardımcı için uygun olmalıdır.

### <a name="launch-in-the-foreground"></a>Ön planda Başlat

Anahtar sözcük doğrulaması başarılı olduğunda, uygulamanın kullanıcı arabirimini görüntülemesi için ön plana taşınması gerekir. `ConversationalAgentSession.RequestForegroundActivationAsync`Uygulamanızı ön plana taşımak için API 'yi çağırın.

### <a name="transition-from-compact-view-to-full-view"></a>Kompakt görünümden tam görünüme geçiş

Uygulamanız ses tarafından ilk kez etkinleştirildiğinde, kompakt bir görünümde başlatılır. Lütfen Windows 'daki sesli Yardımcılar için farklı görünümler ve bunlar arasında geçiş [yapmak üzere ses etkinleştirme önizlemesinin tasarım kılavuzunu](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) okuyun.

Kompakt görünümden tam uygulama görünümüne geçiş yapmak için, ApplicationView API 'sini kullanın `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Kilit etkinleştirmeyi yukarıdaki uygulama

Aşağıdaki adımlarda, uygulama yaşam döngüsünü yönetmeye yönelik örnek kod ve yönergelere başvurular da dahil olmak üzere Windows üzerinde bir ses Yardımcısı 'nı daha önce çalıştırmak için gereken gereksinimler ele alınmaktadır.

Kilit deneyimlerinin üzerinde tasarlanmasına ilişkin yönergeler için [en iyi yöntemler kılavuzunu](windows-voice-assistants-best-practices.md)ziyaret edin.

Bir uygulama kilidin üzerine bir görünüm gösterdiğinde, "bilgi noktası modunda" olduğu kabul edilir. Bilgi noktası modu kullanan bir uygulamayı uygulama hakkında daha fazla bilgi için bilgi [noktası modu belgelerine](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)bakın.

### <a name="transitioning-above-lock"></a>Kilidin üzerine geçme

Kilidi yukarıdaki bir etkinleştirme, aşağıdaki kilit ile benzerdir. Uygulamanın etkin bir örneği yoksa, arka planda ve App 'te yeni bir örnek başlatılır. `OnBackgroundActivated` xaml. cs çağrılacaktır. Uygulamanın bir örneği varsa, bu örnek olay aracılığıyla bir bildirim alır `ConversationalAgentSession.SignalDetected` .

Uygulama daha önce kilidin üzerinde görünmüyorsa, çağrısı gerekir `ConversationalAgentSession.RequestForegroundActivationAsync` . Bu işlem, `OnLaunched` kilit üzerinde gösterilecek görünüme gitmeniz gereken App. xaml. cs içindeki yöntemi tetikler.

### <a name="detecting-lock-screen-transitions"></a>Kilit ekranı geçişleri algılanıyor

Windows SDK konuşma ekranı durumunu ve kilit ekranı durumunda yapılacak değişiklikleri kolay erişilebilir hale getirmek için bir API sağlar. Geçerli kilit ekranı durumunu algılamak için `ConversationalAgentSession.IsUserAuthenticated` alanını denetleyin. Kilit durumundaki değişiklikleri algılamak için, nesnenin olayına bir olay işleyicisi ekleyin `ConversationalAgentSession` `SystemStateChanged` . Ekran kilidi açık iken kilitli veya tam tersi olduğunda harekete geçer. Olay bağımsız değişkenlerinin değeri ise `ConversationalAgentSystemStateChangeType.UserAuthentication` , kilit ekranı durumu değişmiştir.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Kilit etkinleştirme Kullanıcı tercihi üzerinde algılama

Ses etkinleştirme gizlilik ayarları sayfasındaki uygulama girişi, kilit işlevselliği üzerine bir geçiş yapar. Uygulamanızın kilidini açmak için kullanıcının bu ayarı açması gerekir. Kilit izinlerinin yukarıdaki durumu ActivationSignalDetectionConfiguration nesnesinde de depolanır. Kullanılabilirliği, kullanıcının kilitleme izninin üzerine verilip verilmediğini gösterir. Bu ayar devre dışı bırakılırsa, bir ses uygulaması kullanıcıdan "MS-Settings: Gizlilik-voiceactivation" bağlantısındaki uygun ayarlar sayfasına gitmesini isteyebilir ve uygulama için yukarıdaki kilit etkinleştirmeyi nasıl etkinleştirebileceğiniz hakkında yönergeler sağlar.

## <a name="closing-the-application"></a>Uygulamayı kapatma

Kilidi önceki veya sonraki bir sırada kullanarak uygulamayı programlı bir şekilde kapatmak için API 'yi kullanın `WindowService.CloseWindow()` . Bu, OnSuspend dahil olmak üzere tüm UWP yaşam döngüsü yöntemlerini tetikleyip uygulamanın `ConversationalAgentSession` kapatılmadan önce örneğinin atılarak çalışmasına izin verir.

> [!NOTE]
> Uygulama, [aşağıdaki kilit örneğinin](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)kapanmadan kapatılabilir. Bu durumda, yukarıdaki kilit görünümünün "temizlenmesi" gerekir. Bu, ekranın kilidinin açılması durumunda, yukarıdaki kilit görünümünü işlemeyi deneyecek olay işleyicileri veya görevler olmamasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Örnekler ve kod izlenecek yol için UWP Voice Yardımcısı örnek uygulamasını ziyaret edin-kılavuzlarına](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
