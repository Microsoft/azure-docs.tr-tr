---
title: Hızlı başlangıç-Azure Iletişim hizmetlerini kullanarak bir iOS uygulamasına takımlar toplantısı ekleme
description: Bu hızlı başlangıçta iOS için Azure Communication Services ekipleri ekleme kitaplığı 'nı nasıl kullanacağınızı öğreneceksiniz.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104804071"
---
Bu hızlı başlangıçta, iOS için Azure Communication Services ekipleri ekleme kitaplığı 'nı kullanarak bir takımlar toplantısına nasıl katılacağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Anahtarınıza yüklenmiş geçerli bir geliştirici sertifikasıyla birlikte [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)çalıştıran bir Mac.
- Dağıtılan bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Azure Iletişim hizmetiniz için bir [Kullanıcı erişim belirteci](../../access-tokens.md) .
- Derleme ortamınız için [Cocoapods](https://cocoapods.org/) yüklendi.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="creating-the-xcode-project"></a>Xcode projesi oluşturma

Xcode 'da yeni bir iOS projesi oluşturun ve **uygulama** şablonunu seçin. Uııt film şeritleri kullanacağız. Bu hızlı başlangıç sırasında test oluşturuyoruz. **Testleri dahil etme** seçeneğinin işaretini kaldırın.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Xcode 'da yeni proje şablonu seçimini gösteren ekran görüntüsü.":::

Projeyi adlandırın `TeamsEmbedGettingStarted` .

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Xcode içindeki yeni proje ayrıntılarını gösteren ekran görüntüsü.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods ile paketi ve bağımlılıkları yükler

1. Uygulamanız için bir pod dosyası oluşturun:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. `pod install` öğesini çalıştırın.
3. Oluşturulan `.xcworkspace` Xcode 'u açın.

### <a name="request-access-to-the-microphone-camera-etc"></a>Mikrofona, kameraya vb. erişim isteyin

Cihazın donanımına erişmek için uygulamanızın bilgi Özellik listesini güncelleştirin. İlişkili değeri `string` , sistemin kullanıcıdan erişim istemek için kullandığı iletişim kutusunda yer alacak şekilde ayarlayın.

`Info.plist`Proje ağacının girişine sağ tıklayın ve kaynak kodu **olarak aç**' ı seçin  >  . Aşağıdaki satırları en üst düzey bölümüne ekleyin `<dict>` ve dosyayı kaydedin.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Ekip ekleme çerçevesini ekleyin

1. Framework 'ü indirin.
2. `Frameworks`Proje kökünde bir klasör oluşturun. Örn. `\TeamsEmbedGettingStarted\Frameworks\`
3. İndirilen `TeamsAppSDK.framework` ve `MeetingUIClient.framework` çerçeveleri bu klasöre kopyalayın.
4. `TeamsAppSDK.framework` `MeetingUIClient.framework` Genel sekmesinin altındaki proje hedefine ve öğesini ekleyin. `Add Other`  ->  `Add Files...` çerçeve dosyalarına gitmek ve bunları eklemek için kullanın.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Xcode 'da eklenen çerçeveleri gösteren ekran görüntüsü.":::

5. Henüz yoksa, `$(PROJECT_DIR)/Frameworks` `Framework Search Paths` Proje hedefi derleme ayarları sekmesi altına öğesine ekleyin. Ayarı bulmak için, filtresini `basic` olarak değiştirin `all` , sağ taraftaki arama çubuğunu da kullanabilirsiniz.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Xcode 'da çerçeve arama yolunu gösteren ekran görüntüsü.":::

### <a name="turn-off-bitcode"></a>Bitcode 'u kapat

`Enable Bitcode` `No` Proje derleme ayarlarında seçeneğini olarak ayarlayın. Ayarı bulmak için, filtresini `basic` olarak değiştirin `all` , sağ taraftaki arama çubuğunu da kullanabilirsiniz.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Xcode 'da BitCode seçeneğini gösteren ekran görüntüsü.":::

### <a name="add-framework-signing-script"></a>Çerçeve imzalama betiği Ekle

Uygulama hedefini seçin ve `Build Phases` sekmeyi seçin.  Ardından, ve ardından öğesine tıklayın `+` `New Run Script Phase` . Bu yeni aşamanın evreden sonra gerçekleşdiğinden emin olun `Embed Frameworks` .



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Xcode 'da derleme betiği eklemeyi gösteren ekran görüntüsü.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>IP arka plan modu üzerinden sesi açın.

Uygulama hedefini seçin ve yetenekler sekmesine tıklayın.

Üstteki öğesine `Background Modes` tıklayarak açın `+ Capabilities` ve öğesini seçin `Background Modes` .

Onay kutusunu seçin `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Xcode 'da etkin VOıP gösteren ekran görüntüsü.":::

### <a name="add-a-window-reference-to-appdelegate"></a>AppDelegate 'e bir pencere başvurusu ekleyin

Projenizin **Appdelegate. Swift** dosyasını açın ve ' Window ' için bir başvuru ekleyin.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>ViewController 'a düğme ekleme

`viewDidLoad` **ViewController. Swift**'ta geri aramada bir düğme oluşturun.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

**ViewController. Swift** içindeki düğme için bir çıkış oluşturun.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Projenizin **ViewController. Swift** dosyasını açın ve `import` dosyasının en üstüne ve öğesini içeri aktarmak için bir bildirim ekleyin `AzureCommunication library` `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

`ViewController`Kullanıcının toplantıya katılmasına izin vermek için sınıfın uygulamasını basit bir düğmeyle değiştirin. Bu hızlı başlangıçtaki düğmeye iş mantığı ekleyeceğiz.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Azure Iletişim Hizmetleri ekiplerinin ekleme kitaplığının bazı önemli özelliklerinden bazılarını işler:

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Meeting ınguiclient | Meeting ınguiclient, takımlar ekleme kitaplığına ana giriş noktasıdır. |
| Meeting ınguiclientdelegate | Meeting ınguiclientdelegate, çağrı durumundaki değişiklikler gibi olayları almak için kullanılır. |
| Meeting ınjoinoptions | Meeting ınjoinoptions, görünen ad gibi yapılandırılabilir seçenekler için kullanılır. | 
| Çağrı durumu | Çağrı durumu değişikliklerini raporlamak için CallState kullanılır. Seçenekler şunlardır: bağlantı, Waitingınlob, bağlı ve sona erdi. |

## <a name="create-and-authenticate-the-client"></a>İstemci oluşturma ve kimlik doğrulama

Bir `MeetingUIClient` Kullanıcı erişim belirtecine sahip bir örnek başlatın, bu da toplantılara katılımmızı sağlar. Aşağıdaki kodu `viewDidLoad` **ViewController. Swift** içindeki geri aramaya ekleyin:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

`<USER_ACCESS_TOKEN>`Kaynağınız için geçerli bir Kullanıcı erişim belirteci ile değiştirin. Kullanılabilir bir belirteciniz yoksa, [Kullanıcı erişim belirteci](../../access-tokens.md) belgelerine başvurun.

### <a name="setup-token-refreshing"></a>Kurulum belirtecini yenileme

Bir `fetchTokenAsync` yöntemi oluşturun. Ardından `fetchToken` kullanıcı belirtecini almak için mantığınızı ekleyin.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

`<USER_ACCESS_TOKEN>`Kaynağınız için geçerli bir Kullanıcı erişim belirteci ile değiştirin.

## <a name="join-a-meeting"></a>Toplantıya katılarak

`joinMeeting`Yöntemi, *toplantıya Birleştir* düğmesine dokunduğunda gerçekleştirilecek eylem olarak ayarlanır. Uygulamayı bir toplantıya katılacak şekilde güncelleştirin `MeetingUIClient` :

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

`<MEETING URL>`Bir takımlar toplantı bağlantısı ile değiştirin.

### <a name="get-a-teams-meeting-link"></a>Bir takımlar toplantı bağlantısı alın

Bir takımlar toplantı bağlantısı, Graph API 'Leri kullanılarak alınabilir. Bu, [Graph belgelerinde](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)ayrıntılıdır.
SDK 'Yı çağıran Iletişim Hizmetleri, bir tam takımlar toplantısı bağlantısını kabul eder. Bu bağlantı, kaynağın bir parçası olarak döndürülür `onlineMeeting` , [ `joinWebUrl` özelliğin](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) altında erişilebilir, toplantı davetini davet eden ekipteki **Toplantı** URL 'sinden gerekli toplantı bilgilerini de alabilirsiniz.

## <a name="run-the-code"></a>Kodu çalıştırma

**Ürün**  >  **çalıştırması** ' nı seçerek veya (&#8984;-R) klavye kısayolunu kullanarak uygulamanızı iOS simülatörü üzerinde oluşturabilir ve çalıştırabilirsiniz.

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Hızlı başlangıç uygulamasına yönelik nihai görünüm":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Toplantının birleştirilme sonrasında nihai görünüm":::

> [!NOTE]
> Bir toplantıya ilk katılırsanız, sistem mikrofona erişim ister. Bir üretim uygulamasında izin `AVAudioSession` [durumunu denetlemek](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) ve izin verilmediği zaman uygulamanızın davranışını dikkatlice güncelleştirmek için API 'yi kullanmanız gerekir.

## <a name="add-localization-support-based-on-your-app"></a>Uygulamanıza göre yerelleştirme desteği ekleyin

Microsoft ekipleri SDK 'Sı 100 ' den fazla dizeyi ve kaynağı destekler. Çerçeve paketi, temel ve Ingilizce dilleri içerir. Geri kalanı, `Localizations.zip` pakete dahil edilen dosyaya dahil edilmiştir.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Uygulamanızın neleri desteklediğine göre SDK 'ya localmeler ekleyin

1. Uygulamanızın Xcode projesi > bilgi > yerelleştirmeler listesinden ne tür yerellerin destekleyeceğini belirleme
2. Pakete eklenen Localizations.zip sıkıştırmasını açın
3. Yerelleştirme klasörlerini, uygulamanızın, TeamsAppSDK. Framework köküne göre neleri desteklediğine bağlı olarak, sıkıştırunzip dışı klasörden kopyalayın

## <a name="preparation-for-app-store-upload"></a>App Store yükleme hazırlığı

Arşivlenmek üzere çerçeveler ve x86_64 mimarilerini çerçevelerden kaldırın.

`i386` `x86_64` Uygulamanızı arşivlemek istiyorsanız, Framework kod imzalama aşamasından önce derleme aşamalarına betiği kaldırma ve mimariler ekleyin.

Proje Gezgini 'nde projenizi seçin. Düzenleyici bölmesinde, derleme aşamaları → ' na gidin ve + imzala → yeni bir çalıştırma betiği oluştur aşamasına tıklayın.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Örnek Kod

Örnek uygulamayı [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started) 'dan indirebilirsiniz
