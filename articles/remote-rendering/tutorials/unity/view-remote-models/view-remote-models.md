---
title: Uzaktan işlenmiş bir modeli görüntüleme
description: Azure uzaktan Işleme 'nin "Merhaba Dünya", öğretici, Azure tarafından uzaktan oluşturulan bir modelin nasıl görüntüleneceğini gösterir
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: f43e5b77580b7071ce48b39190c26a53f99f8cf5
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740158"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Öğretici: uzaktan işlenmiş bir modeli görüntüleme

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure uzaktan Işleme (ARR) örneği sağlama
> * İşleme oturumu oluşturma ve durdurma
> * Mevcut bir işleme oturumunu yeniden kullanma
> * Oturumlara bağlanma ve bağlantıyı kesme
> * Modelleri bir işleme oturumuna yükleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide şunlar gerekir:

* Etkin bir Kullandıkça Öde Azure aboneliği [Hesap oluşturma](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(İndir)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Visual Studio 2019 ' un en son sürümü [(indirme)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(İndir)](https://git-scm.com/downloads)
* Unity (desteklenen sürümler için [sistem gereksinimlerine](../../../overview/system-requirements.md#unity) bakın)
* Unity ve C# dilinin ara bilgisi (örneğin: komut dosyaları ve nesneler oluşturma, Prefabs kullanma, Unity olaylarını yapılandırma vb.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Azure uzaktan Işleme (ARR) örneği sağlama

Azure uzaktan Işleme hizmetine erişim sağlamak için önce [bir hesap oluşturmanız](../../../how-tos/create-an-account.md#create-an-account)gerekir.

## <a name="create-a-new-unity-project"></a>Yeni Unity projesi oluşturma

> [!TIP]
> [ARR örnekleri deposu](https://github.com/Azure/azure-remote-rendering) , tüm öğreticilerin tamamlandığı bir proje içerir, başvuru olarak kullanılabilir. Tüm Unity projesi için *Unity\Tutorial-Complete* bakın.

Unity hub 'ında yeni bir proje oluşturun.
Bu örnekte, projenin **RemoteRendering** adlı bir klasörde oluşturulduğunu varsayacağız.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Yeni Unity projesi":::

## <a name="include-the-azure-remote-rendering-package"></a>Azure uzaktan Işleme paketini dahil et

Azure uzaktan Işleme paketini bir Unity projesine ekleme [yönergelerini izleyin](../../../how-tos/unity/install-remote-rendering-unity-package.md) .


## <a name="configure-the-camera"></a>Kamerayı yapılandırma

1. **Ana kamera** düğümünü seçin.

1. *Dönüştür* bileşenine sağ tıklayarak bağlam menüsünü açın ve **sıfırlama** seçeneğini belirleyin:

    ![Kamera dönüşümünü Sıfırla](./media/camera-reset-transform.png)

1. **Clear bayraklarını** *düz renge* ayarla

1. Tamamen saydam (0) Alfa (A) ile **arka planı** *siyaha* (#000000) ayarlayın

    ![Renk tekerleği](./media/color-wheel-black.png)

1. **Kırpma düzlemleri** *Near = 0,3* ve *Far = 20* olarak ayarlayın. Bu, işlemenin 30 cm 'den daha yakın veya 20 ölçümden daha büyük olan bir geometriyi kırpmak anlamına gelir.

    ![Unity kamera özellikleri](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Proje ayarlarını ayarla

1. *> proje ayarlarını Düzenle 'yi aç...*
1. Sol taraftaki liste menüsünden **kalite** ' yi seçin
1. Tüm platformların **Varsayılan kalite düzeyini** *düşük* olarak değiştirin. Bu ayar, yerel içeriğin daha verimli işlemesini etkinleştirir ve uzaktan işlenmiş içeriğin kalitesini etkilemez.

    ![Proje kalitesi ayarlarını değiştir](./media/settings-quality.png)

1. Sol liste menüsünden **grafik** seçin
1. **Komut dosyası oluşturma Işlem hattı** ayarını *Hybridrenderingpipeline*. \ olarak değiştirin
    ![Komut dosyası oluşturma işlem hattı ayarını HybridRenderingPipeline olarak değiştirdiğiniz yere işaret eden ekran görüntüsü.](./media/settings-graphics-render-pipeline.png)\
    Bazen kullanıcı arabirimi kullanılabilir işlem hattı türleri listesini paketlerden doldurmaz. Bu durum oluşursa *Hybridrenderingpipeline* varlığı alana el ile sürüklenmesi gerekir: \
    ![Proje grafik ayarlarını değiştirme](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > *Hybridrenderingpipeline* varlığını, Işleme ardışık düzen varlık alanına sürükleyip bırakamazsınız (Belki de alan mevcut olmadığından), paket yapılandırmanızın paketi içerdiğinden emin olun `com.unity.render-pipelines.universal` .

1. Sol taraftaki liste menüsünden **oynatıcı** ' yı seçin
1. Windows simgesi olarak temsil edilen **Evrensel Windows platformu ayarları** sekmesini seçin.
1. **XR ayarlarını** aşağıda gösterildiği gibi Windows Mixed Reality 'yi destekleyecek şekilde değiştirin:
    1. **Sanal Reality** 'Yi etkinleştirme destekleniyor
    1. ' + ' Düğmesine basın ve **Windows Mixed Reality** 'yi ekleyin
    1. **Derinlik biçimini** *16 bit derinliğe* ayarla
    1. **Derinlik arabelleği paylaşımının** etkinleştirildiğinden emin olun
    1. **Stereo Işleme modunu** tek bir *geçiş* için ayarla

    ![Oynatıcı ayarları](./media/xr-player-settings.png)

1. Aynı pencerede, **XR ayarları**' nda, **Yayımlama ayarları** ' nı genişletin.
1. **Özellikleri** aşağı kaydırın ve şunları seçin:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*isteğe bağlı*). Unity uzaktan hata ayıklayıcısını cihazınıza bağlamak istiyorsanız bu seçeneği belirleyin.

1. **Desteklenen cihaz aileleri** altında **holographic** ve **Desktop** 'ı etkinleştirin
1. **Proje ayarları** panelini kapatma veya yerleştirme
1. *Dosya >derleme ayarlarını* aç
1. **Evrensel Windows platformu** seçin
1. Ayarlarınızı aşağıda bulunanlarla eşleşecek şekilde yapılandırın
1. **Platformu Değiştir** düğmesine basın. \
![derleme ayarları](./media/build-settings.png)
1. Unity değişikliklerinden sonra, yapı panelini kapatın.

## <a name="validate-project-setup"></a>Proje kurulumunu doğrula

Proje ayarlarının doğru olduğunu doğrulamak için aşağıdaki adımları gerçekleştirin.

1. Unity Düzenleyicisi araç çubuğundaki **RemoteRendering** menüsünden **validateproject** girişini seçin.
1. Hatalar için **Validateproject** penceresini gözden geçirin ve gerektiğinde proje ayarlarını onarın.

    ![Unity Düzenleyicisi proje doğrulaması](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Azure uzaktan Işleme bağlantısını ve durumunu koordine etmek için bir betik oluşturun

Aşağıdaki akış çizelgesinde özetlenen, uzaktan işlenmiş modelleri göstermek için dört temel aşama vardır. Her aşama sırasıyla gerçekleştirilmelidir. Sonraki adım, uygulama durumunu yönetecek ve gereken her aşamada devam edecek bir komut dosyası oluşturmaktır.

![ARR yığını 0](./media/remote-render-stack-0.png)

1. *Proje* bölmesinde, **varlıklar** altında, *remoterenderingcore* adlı yeni bir klasör oluşturun. Ardından, *Remoterenderingcore* içinde *betikler* adlı başka bir klasör oluşturun.

1. **Remoterenderingcoordinator** adlı [Yeni bir C# betiği](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) oluşturun.
Projeniz şuna benzemelidir:

    ![Proje hiyerarşisi](./media/project-structure.png)

    Bu düzenleyici betiği uzaktan işleme durumunu izleyip yönetecektir. Not, bu kodun bir kısmı durumu korumak, diğer bileşenlere işlevsellik göstermek, olayları tetiklemek ve *doğrudan* Azure uzaktan işleme ile ilişkili olmayan uygulamaya özgü verileri depolamak için kullanılır. Aşağıdaki kodu bir başlangıç noktası olarak kullanın ve öğreticide daha sonra belirli Azure uzaktan Işleme kodunu adresleyeceğiz ve uygulayacağız.

1. Kod Düzenleyicinizde **Remoterenderingcoordinator** ' yı açın ve tüm içeriğini aşağıdaki kodla değiştirin:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // Account
    // RemoteRenderingDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    [SerializeField]
    private string remoteRenderingDomain = "westus2.mixedreality.azure.com";
    public string RemoteRenderingDomain
    {
        get => remoteRenderingDomain.Trim();
        set => remoteRenderingDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountDomain = "<enter your account domain here>";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }    

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.LogFormat(LogType.Log, LogOption.NoStacktrace, null, "{0}", $"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountDomain, RemoteRenderingDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        bool sessionAvailable = false;
        try
        {
            RenderingSessionPropertiesArrayResult result = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
            if (result.ErrorCode == Result.Success)
            {
                RenderingSessionProperties[] properties = result.SessionProperties;
                if (properties != null)
                {
                    sessionAvailable = properties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
                }
            }
            else
            {
                Debug.LogError($"Failed to get current rendering sessions. Error: {result.Context.ErrorMessage}");
            }
        }
        catch (RRException ex)
        {
            Debug.LogError($"Failed to get current rendering sessions. Error: {ex.Message}");
        }
        return sessionAvailable;
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Azure uzaktan Işleme Oyunnesnesi oluşturma

Uzaktan işleme Düzenleyicisi ve gerekli betiği (*ARRServiceUnity*), sahnedeki bir gameobject 'e eklenmesi gereken MonoBehaviours. *ARRServiceUnity* betiği ARR tarafından, uzak oturumlara bağlanmak ve bunları yönetmek için ARR 'nin işlevselliğinin çoğunu göstermek üzere sağlanır.

1. Sahnede yeni bir Oyunobject oluşturun (CTRL + SHIFT + N veya *gameobject->boş oluştur*) ve bunu **Remoterenderingcoordinator** olarak adlandırın.
1. *Remoterenderingcoordinator* **oyunobject** . \ dosyasına
![RemoteRenderingCoordinator bileşeni ekleme](./media/add-coordinator-script.png)
1. Denetçisinde *hizmet* olarak görünen *ARRServiceUnity* betiğini onaylayın, otomatik olarak gameobject öğesine eklenir. Merak ediyorsanız, bu durum, `[RequireComponent(typeof(ARRServiceUnity))]` **Remoterenderingcoordinator** betiğinin en üstünde yer aldığı bir sonuçdır.
1. Azure uzaktan Işleme kimlik bilgilerinizi, hesap etki alanınızı ve uzaktan Işleme etki alanınızı düzenleyici betiğine ekleyin: \
![Kimlik bilgilerinizi ekleyin](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Azure uzaktan Işlemesini Başlat

Artık düzenleyicimiz için bir çerçevemiz olduğuna göre, **Uzaktan Işlemeyi başlatma** ile başlayan dört aşamanın her birini uygulayacağız.

![ARR yığını 1](./media/remote-render-stack-1.png)

**Initialize** , Azure 'un işleme için hangi kamera nesnesinin kullanılacağını ve durum makinesinin **NotAuthorized**'e Ilerlediğini bildiren bir uzaktan işleme söyler. Bu, başlatılmış olduğu ancak henüz bir oturuma bağlanmaya yetkili olmadığı anlamına gelir. Bir ARR oturumunun başlatılması bir maliyet oluşturduğundan, kullanıcının devam etmek istediğini doğrulamamız gerekir.

**NotAuthorized** durumunu girerken, **onay yetkilendirme** olayını çağıran ve kullanılacak hesap kimlik bilgilerini belirleyen (**AccountInfo** , sınıfın üst kısmına yakın bir şekilde tanımlanır ve yukarıdaki adımda Unity denetçisi aracılığıyla tanımladığınız kimlik bilgilerini kullanır) **checkauthorleştirme** çağırılır.

   > [!NOTE]
   > Çalışma zamanı yeniden derleme, ARR tarafından desteklenmiyor. Komut dosyasını değiştirme ve yürütme modu etkin durumdayken kaydetme, Unity 'nin donmasına neden olabilir ve Görev Yöneticisi aracılığıyla kapanmaya zorlamaya gerek yoktur. Komut dosyalarınızı düzenlemeden önce her zaman yürütme modunu durdurduğumdan emin olun.

1. **Initializearr** ve **ınitializesessionservice** içeriğini aşağıdaki tamamlanan kodla değiştirin:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

**NotAuthorized** 'Den **nosession**'a ilerleyebilmek için genellikle kullanıcıya bir kalıcı iletişim kutusu sunuruz ve bu kullanıcıların seçebilecekleri (yalnızca başka bir bölümde yapacağız). Şimdilik, **Requestingauthorization** olayı tetiklendiğinde **ByPassAuthentication** çağırarak yetkilendirme denetimini otomatik olarak atlayacağız.

1. **Remoterenderingcoordinator** oyunobject ' i seçin ve **remoterenderingcoordinator** bileşeninin denetçisinde sunulan **Onrequestingauthorization** Unity olayını bulun.

1. Sağ alt köşedeki ' + ' tuşuna basarak yeni bir olay ekleyin.
1. Bileşeni kendisine başvurmak için kendi olayına sürükleyin. \
![Kimlik doğrulamasını atla](./media/bypass-authorization-add-event.png)\
1. Açılan kutuda, **Remoterenderingcoordinator-> Bypassauthorleştirme**. \ ' ı seçin.
![Seçili RemoteRenderingCoordinator. Bypassauthorleştirme seçeneğini gösteren ekran görüntüsü.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Uzak oturum oluşturma veya bir oturum ekleme

İkinci aşama, uzaktan Işleme oturumu oluşturmak veya birleştirmek (daha fazla bilgi için bkz. [Uzaktan Işleme oturumları](../../../concepts/sessions.md) ).

![ARR yığını 2](./media/remote-render-stack-2.png)

Uzak oturum, modellerin nerede işleneceğini görürsünüz. **Joinremotesession ()** yöntemi, **Lastusedsessionıd** özelliğiyle izlenen mevcut bir oturuma katılmayı dener veya **sessionidoverride** üzerinde atanmış bir etkin oturum kimliği vardır. **Sessionidoverride** yalnızca hata ayıklama amacınıza yöneliktir, yalnızca oturumun var olduğunu bildiğiniz ve kendisine açıkça bağlanmak istediğiniz durumlarda kullanılmalıdır.

Kullanılabilir oturum yoksa yeni bir oturum oluşturulur. Ancak, yeni bir oturum oluşturma, zaman alan bir işlemdir. Bu nedenle, yalnızca gerekli olduğunda oturum oluşturmayı ve mümkün olduğunda yeniden kullanmayı denemeniz gerekir (oturumları yönetme hakkında daha fazla bilgi için bkz. [Ticari Basım: oturum kuyruğu oluşturma, zamanlama ve en iyi uygulamalar](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) ).

> [!TIP]
> **Stopremotesession ()** etkin oturumu sona erdirmek için kullanılır. Gereksiz ücretleri engellemek için, artık gerekli olmayan oturumları her zaman durdurmanız gerekir.

Durum makinesi şimdi, kullanılabilir oturumlara bağlı olarak **Connectingtonewremotesession** veya **Connectingtoexistingremotesession** olarak devam eder. Hem mevcut bir oturum açma ya da yeni bir oturum oluşturma, **ARRSessionService. OnSessionStatusChanged** olayını tetikleyip **onremotesessionstatuschanged** yöntemini tetikler. İdeal olarak, bu durum makinesi **Remotesessionready olarak ilerletmeye** neden olur.

1. Yeni bir oturuma katmak için, kodu aşağıdaki tamamlanan örneklerle birlikte **Joinremotesession ()** ve **stopremotesession ()** yöntemlerini değiştirecek şekilde değiştirin:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Oturumları yeniden kullandığınızda zaman kazanmak istiyorsanız, *ARRServiceUnity* bileşeninde **otomatik durdurma oturumu** seçeneğini devre dışı bıraktığınızdan emin olun. Bu, kendisine bağlı bir bağlantı olmadığında bile oturumları çalışır durumda bırakacağını aklınızda bulundurun. Oturumunuz, sunucu tarafından kapatılmadan önce *maxleasetime* 'niz için çalıştırılabilir ( *yeni oturum Varsayılanları* altında *Maxleasetime* değeri uzaktan işleme Düzenleyicisi 'nde değiştirilebilir). Öte yandan, bağlantı kesilirken her oturumu otomatik olarak kapatırsanız, yeni bir oturumun her seferinde başlamasını beklemeniz gerekir, bu da uzun bir işlem olabilir.

> [!NOTE]
> Bir oturumu durdurmak hemen etkili olur ve geri alınamaz. Durdurulduktan sonra, aynı başlangıç yüküyle yeni bir oturum oluşturmanız gerekir.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Yerel çalışma zamanını uzak oturuma bağlama

Sonra, uygulamanın yerel çalışma zamanını uzak oturuma bağlanması gerekir.

![ARR yığını 3](./media/remote-render-stack-3.png)

Uygulamanın Ayrıca, çalışma zamanı ve geçerli oturum arasındaki bağlantıyla ilgili olayları dinlemesi gerekir; Bu durum değişiklikleri **OnLocalRuntimeStatusChanged** içinde işlenir. Bu kod, eyaletimizi **Connectingtoruntime**'a ilerlecektir. **OnLocalRuntimeStatusChanged**' ye bağlandıktan sonra durum, **runtimeconnected**' a ilerlecektir. Çalışma zamanına bağlanmak, düzenleyicinin kendisini ile ilgilendiren son durumdur. Bu, uygulamanın tüm ortak yapılandırma ile yapıldığı ve model yükleme ve işleme modelinin işe özgü çalışmasına başlamaya hazırlanmasıdır.

 1. **ConnectRuntimeToRemoteSession ()** ve **DisconnectRuntimeFromRemoteSession ()** yöntemlerini aşağıdaki tamamlanmış sürümlerle değiştirin.
 1. Unity yöntemi **Lateupdate** ' i ve geçerli etkin oturumu güncelleştirdiğini göz önünde bulundurulmalıdır. Bu, geçerli oturumun ileti göndermesini/almasını ve çerçeve arabelleğini uzak oturumdan alınan çerçevelerle güncelleştirmesini sağlar. Doğru şekilde çalışması için kritik öneme sahiptir.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> Yerel çalışma zamanını uzak bir oturuma bağlamak, şu anda etkin olan oturumda çağırılan **güncelleştirmeye** bağlıdır. Uygulamanızın hiçbir zaman **Connectingtoruntime** durumundan ilerlediğini görürseniz, **güncelleştirme** 'yi etkin oturumda düzenli olarak çağırtığınızdan emin olun.

## <a name="load-a-model"></a>Model yükleme

Gerekli olan temel ile, uzak oturuma bir model yüklemeye ve çerçeveler almaya başlamaya hazırız.

![Bir modeli yüklemeyi ve görüntülemeyi hazırlamaya yönelik işlem akışını gösteren diyagram.](./media/remote-render-stack-4.png)

**LoadModel** yöntemi bir model yolu, ilerleme işleyicisi ve üst dönüşüm kabul etmek için tasarlanmıştır. Bu bağımsız değişkenler, bir modeli uzak oturuma yüklemek, yükleme işlemindeki kullanıcıyı güncelleştirmek ve ana dönüşümü temel alarak uzaktan işlenmiş modeli yönlendirmek için kullanılacaktır.

1. **LoadModel** yöntemini tamamen aşağıdaki kodla değiştirin:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

Yukarıdaki kod aşağıdaki adımları gerçekleştiriyor:

1. Uzak bir [varlık](../../../concepts/entities.md)oluşturun.
1. Uzak varlığı temsil eden yerel bir oyun nesnesi oluşturun.
1. Yerel oyun nesnesini, durumunu (dönüşüm) her karede uzak varlıkla eşitlenecek şekilde yapılandırın.
1. Bir ad belirleyin ve bir [**Korldanchor**](https://docs.unity3d.com/550/Documentation/ScriptReference/VR.WSA.WorldAnchor.html) ekleyin.
1. BLOB depolama alanından uzak varlığa model verilerini yükleyin.
1. Üst varlığı daha sonra başvurmak üzere döndürün.

## <a name="view-the-test-model"></a>Test modelini görüntüle

Artık uzaktan işlenmiş bir modeli görüntülemek için gereken tüm kod, uzaktan işleme için gereken her dört aşamanın tamamı tamamlanmıştır. Artık model yükleme işlemini başlatmak için küçük bir kod eklememiz gerekiyor.

![ARR yığını 4](./media/remote-render-stack-5.png)

1. Aşağıdaki kodu, bir **LoadModel** yönteminin hemen altında bulunan **Remoterenderingcoordinator** sınıfına ekleyin:

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Bu kod, test modelinin üst öğesi olarak davranacak bir GameObject oluşturur. Ardından, işleme testi amacıyla Azure uzaktan Işleme içinde yerleşik bir varlık olan "builtin://Engine" modelini yüklemek için **LoadModel** yöntemini çağırır.

1. Kodunuzu kaydedin.
1. Azure uzaktan Işleme bağlanma ve yeni bir oturum oluşturma işlemini başlatmak için Unity Düzenleyicisi 'ndeki Oynat düğmesine basın.
1. Oyun görünümünde çok fazla görmezsiniz, ancak konsol değişen uygulamanın durumunu gösterir. Büyük olasılıkla `ConnectingToNewRemoteSession` en fazla beş dakika boyunca devam eder.
1. Inspector 'daki ekli komut dosyalarını görmek için **Remoterenderingcoordinator** oyunobject öğesini seçin. Başlatma ve bağlantı adımlarında ilerlerken **hizmet** bileşeni güncelleştirmesini izleyin.
1. Konsol çıkışını izleme-durumun **Runtimeconnected** olarak değiştirilmesi bekleniyor.
1. Çalışma zamanı bağlandıktan sonra, bağlam menüsünü kullanıma sunmak için denetçisindeki **Remoterenderingcoordinator** öğesine sağ tıklayın. Daha sonra, yukarıdaki kodumuz bölümü tarafından eklenen bağlam menüsündeki **Yük testi modeli** seçeneğine tıklayın `[ContextMenu("Load Test Model")]` .

    ![Bağlam menüsünden yükle](./media/load-test-model.png)

1. **LoadModel** metoduna geçirdiğimiz **ProgressHandler** çıkışı için konsolu izleyin.
1. Uzaktan işlenen modele bakın!

> [!NOTE]
> Uzak model, yalnızca oyun görünümünde sahne görünümünde görünür olmayacaktır. Bu, ARR 'nin çerçeveleri oyun görünümü kamerasının perspektifine özel olarak uzak şekilde işlemesi ve düzenleyici kamerasının (sahne görünümünü işlemek için kullanılır) farkında olmamasıdır.

## <a name="next-steps"></a>Sonraki adımlar

![Model yüklendi](./media/test-model-rendered.png)

Tebrikler! Azure uzaktan Işleme kullanarak uzaktan işlenmiş modelleri görüntüleyebilecek temel bir uygulama oluşturdunuz. Sonraki öğreticide, MRTK 'ı tümleştireceğiz ve kendi modellerimizi içeri aktaracağız.

> [!div class="nextstepaction"]
> [Sonraki: arabirimler ve özel modeller](../custom-models/custom-models.md)
