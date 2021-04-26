---
title: Visual Studio Code-Azure Işlevleri kullanarak Go veya Rust 'da işlev oluşturma
description: Azure Işlevleri özel işleyicisi olarak go işlevi oluşturmayı öğrenin ve ardından Visual Studio Code içindeki Azure Işlevleri uzantısını kullanarak yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma olarak yayımlayın.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: 8b53031315cce3651a2de581d71da6ef6fe909c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470379"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code kullanarak Azure 'da Go veya Rust işlevi oluşturma

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Bu makalede, HTTP isteklerine yanıt veren [özel bir işleyici](functions-custom-handlers.md) işlevi oluşturmak için Visual Studio Code kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Özel işleyiciler, bir HTTP sunucu işlemi çalıştırarak herhangi bir dilde veya çalışma zamanında işlevler oluşturmak için kullanılabilir. Bu makalede hem [Go](create-first-function-vs-code-other.md?tabs=go) hem de [Rust](create-first-function-vs-code-other.md?tabs=rust)desteklenir.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

## <a name="configure-your-environment"></a>Ortamınızı yapılandırma

Başlamadan önce, aşağıdaki gereksinimlerin yerinde olduğundan emin olun:

# <a name="go"></a>[Git](#tab/go)

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms) birinde çalışan [Visual Studio Code](https://code.visualstudio.com/).

+ Visual Studio Code için [Azure İşlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

+ [Azure Functions Core Tools](./functions-run-local.md#v2) sürüm 3. x. `func --version`Doğru yüklenip yüklenmediğini denetlemek için komutunu kullanın.

+ [Git](https://golang.org/doc/install), en son sürüm önerilir. `go version`Sürümünüzü denetlemek için komutunu kullanın.

# <a name="rust"></a>[Rust](#tab/rust)

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms) birinde çalışan [Visual Studio Code](https://code.visualstudio.com/).

+ Visual Studio Code için [Azure İşlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

+ [Azure Functions Core Tools](./functions-run-local.md#v2) sürüm 3. x. `func --version`Doğru yüklenip yüklenmediğini denetlemek için komutunu kullanın.

+ [Rusdrın](https://www.rust-lang.org/tools/install)kullanarak Rust araç zinciri. `rustc --version`Sürümünüzü denetlemek için komutunu kullanın.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Yerel projenizi oluşturma

Bu bölümde, yerel bir Azure Işlevleri özel işleyiciler projesi oluşturmak için Visual Studio Code kullanırsınız. Bu makalenin ilerleyen bölümlerinde işlev kodunu Azure'da yayımlayacaksınız.

1. Etkinlik çubuğundan Azure simgesini seçtikten sonra **Azure: İşlevler** alanında **Yeni proje oluştur...** simgesini seçin.

    ![Yeni proje oluştur'u seçin](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Projenizin çalışma alanı için bir dizin konumu seçtikten sonra **Seç**'i belirleyin.

    > [!NOTE]
    > Bu adımlar, bir çalışma alanının dışında tamamlanacak şekilde tasarlandı. Bu örnekte, bir çalışma alanının parçası olan bir proje klasörünü seçmeyin.

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **İşlev projeniz için bir dil seçin**: `Custom` öğesini seçin.

    + **Projenizin ilk işlevi için bir şablon seçin**: `HTTP trigger` öğesini seçin.

    + **Bir işlev adı sağlayın**: `HttpExample` yazın.

    + **Yetkilendirme düzeyi**: `Anonymous` herhangi bir kişinin işlev uç noktanızı çağırmasını sağlayan öğesini seçin. Yetkilendirme düzeyi hakkında bilgi edinmek için bkz. [Yetkilendirme anahtarları](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Projenizi nasıl açmak istediğinizi seçin**: `Add to workspace` öğesini seçin.

1. Bu bilgileri kullanarak, Visual Studio Code bir HTTP tetikleyici işlevi ile bir Azure Işlevleri projesi oluşturur. Yerel proje dosyalarını Gezgin içinde görüntüleyebilirsiniz. Oluşturulan dosyalar hakkında daha fazla bilgi edinmek için bkz. [oluşturulan proje dosyaları](functions-develop-vs-code.md#generated-project-files). 

## <a name="create-and-build-your-function"></a>İşlevinizi oluşturma ve oluşturma

*Httpexample* klasöründeki dosya *function.js* bir http tetikleyici işlevi bildirir. Bir işleyici ekleyip bir çalıştırılabilire derleyerek işlevi tamamlayabilirsiniz.

# <a name="go"></a>[Git](#tab/go)

1. Yeni bir dosya oluşturmak için <kbd>CTRL + n</kbd> (MacOS üzerinde<kbd>cmd + n</kbd> ) tuşuna basın. Bunu işleyici olarak kaydedin. app root ( *host.js* ile aynı klasörde) işlevine *gidin* .

1. *Handler. go* içinde aşağıdaki kodu ekleyin ve dosyayı kaydedin. Bu, go özel işleyiciniz.

    ```go
    package main
    
    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )
    
    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }
    
    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. VS Code ' de yeni bir tümleşik Terminal açmak için <kbd>CTRL + SHIFT + '</kbd> tuşlarına basın veya *Terminal* menüsünden *yeni Terminal* ' ı seçin.

1. Aşağıdaki komutu kullanarak özel işleyicinizi derleyin. (Windows üzerinde) adlı yürütülebilir bir dosya, `handler` `handler.exe` işlev uygulaması kök klasöründe çıktıdır.

    ```bash
    go build handler.go
    ```

    ![VS Code-derleme git özel işleyicisi](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. VS Code ' de yeni bir tümleşik Terminal açmak için <kbd>CTRL + SHIFT + '</kbd> tuşlarına basın veya *Terminal* menüsünden *yeni Terminal* ' ı seçin.

1. İşlev uygulaması kökünde ( *host.jsüzerinde* aynı klasör), adlı bir Rust projesi başlatın `handler` .

    ```bash
    cargo init --name handler
    ```

1. *Cargo. TOML*'de, bu hızlı başlangıcı gerçekleştirmek için gerekli olan aşağıdaki bağımlılıkları ekleyin. Örnek, [warp](https://docs.rs/warp/) Web sunucusu çerçevesini kullanır.

    ```toml
    [dependencies]
    warp = "0.3"
    tokio = { version = "1", features = ["rt", "macros", "rt-multi-thread"] }
    ```

1. *Src/main. RS* dosyasında aşağıdaki kodu ekleyin ve dosyayı kaydedin. Bu, Rust özel işleyiciniz.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Özel işleyiciniz için bir ikili derleyin. (Windows üzerinde) adlı yürütülebilir bir dosya, `handler` `handler.exe` işlev uygulaması kök klasöründe çıktıdır.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![VS Code-yapılandırma Rust özel işleyicisi](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>İşlev uygulamanızı yapılandırma

İşlev konağının, başlatıldığında özel işleyici ikilisini çalıştıracak şekilde yapılandırılması gerekir.

1. *Üzerindehost.js* açın.

1. `customHandler.description`Bölümünde, değerini `defaultExecutablePath` olarak ayarlayın `handler` (Windows 'ta, olarak ayarlayın `handler.exe` ).

1. `customHandler`Bölümünde adlı bir özellik ekleyin `enableForwardingHttpRequest` ve değerini olarak ayarlayın `true` . Yalnızca bir HTTP tetikleyicisinden oluşan işlevlerde, bu ayar, özel işleyici [istek yükü](functions-custom-handlers.md#request-payload)yerine tıpık bir HTTP isteğiyle çalışmanıza olanak sağlayarak programlamayı basitleştirir.

1. `customHandler`Bölümün bu örneğe benzer göründüğünü onaylayın. Dosyayı kaydedin.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

İşlev uygulaması, özel işleyicinizin yürütülebilir dosyanızı başlatacak şekilde yapılandırılmıştır.

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Bu projeyi, Azure 'da yayımlamadan önce yerel geliştirme bilgisayarınızda çalıştırabilirsiniz.

1. Tümleşik terminalde Azure Functions Core Tools kullanarak işlev uygulamasını başlatın.

    ```bash
    func start
    ```

1. Temel araçlar çalışırken, sorgu dizesi içeren bir GET isteği yürütmek için aşağıdaki URL 'ye gidin `?name=Functions` .

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Bir tarayıcıda aşağıdaki gibi görünen bir yanıt döndürülür:

    ![Tarayıcı-localhost örnek çıktısı](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. İstek hakkındaki bilgiler, **Terminal** panelinde gösterilir.

    ![Görev ana bilgisayarı başlatma-VS Code Terminal çıkışı](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Temel araçları durdurmak için <kbd>CTRL + C</kbd> tuşlarına basın.

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi doğrudan Azure 'da yayımlamak için Visual Studio Code kullanma zamanı vardır.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>Azure için özel işleyiciyi derle

Bu bölümde, bir Linux çalıştıran bir işlev uygulamasında projenizi Azure 'da yayımlayın. Çoğu durumda, ikilinizi yeniden derlemelidir ve yapılandırmanızı Azure 'a yayımlamadan önce hedef platformla eşleşecek şekilde ayarlamanız gerekir.

# <a name="go"></a>[Git](#tab/go)

1. Tümleşik terminalde, işleyiciyi Linux/x64 için derleyin. App root işlevinde adlı bir ikili dosya `handler` oluşturulur.

    # <a name="macos"></a>[macOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build handler.go
    ```

    `defaultExecutablePath`İçindeki *host.js* öğesini `handler.exe` olarak değiştirin `handler` . Bu, işlev uygulamasını Linux ikilisini çalıştıracak şekilde yönlendirir.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. *. Cargo/config* konumunda bir dosya oluşturun. Aşağıdaki içerikleri ekleyin ve dosyayı kaydedin.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. Tümleşik terminalde, işleyiciyi Linux/x64 için derleyin. Adında bir ikili `handler` oluşturulur. Bunu işlev uygulaması köküne kopyalayın.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. Windows kullanıyorsanız, ' dekihost.js' yi `defaultExecutablePath` olarak  değiştirin `handler.exe` `handler` . Bu, işlev uygulamasını Linux ikilisini çalıştıracak şekilde yönlendirir.

1. *. Funcignore* dosyasına aşağıdaki satırı ekleyin:

    ```
    target
    ```

    Bu, *hedef* klasörün içeriklerinin yayımlanmasını engeller.

---

## <a name="publish-the-project-to-azure"></a>Projeyi Azure'da yayımlama

Bu bölümde, Azure aboneliğinizde bir işlev uygulaması ve ilgili kaynaklar oluşturup kodunuzu dağıtırsınız. 

> [!IMPORTANT]
> Varolan bir işlev uygulamasına yayımladığınızda Azure’daki uygulamanın içeriğinin üzerine yazılır. 


1. Etkinlik çubuğunda Azure simgesini seçin, sonra **Azure: işlevler** alanında, **işlev uygulamasına dağıt uygulama...** düğmesini seçin.

    ![Projenizi Azure'da yayımlama](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **Klasör seçin**: çalışma alanınızdan bir klasör seçin veya işlev uygulamanızı içeren bir klasöre gidin. Zaten açılmış geçerli bir işlev uygulamanız varsa bunu göremezsiniz.

    + **Abonelik seçin**: kullanılacak aboneliği seçin. Yalnızca bir aboneliğiniz varsa bunu göremezsiniz.

    + **Azure'daki işlev uygulamasını seçin**: `+ Create new Function App (advanced)` öğesini seçin. 
    
        > [!IMPORTANT]
        > `advanced`Seçeneği, işlev uygulamanızın Azure 'da çalıştığı belirli işletim sistemini seçmenizi sağlar ve bu durumda Linux olur.

        ![VS Code-gelişmiş yeni işlev uygulaması oluştur ' u seçin](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **İşlev uygulaması için genel olarak benzersiz bir ad girin**: URL yolunda geçerli olan bir ad yazın. Yazdığınız ad, Azure Işlevlerinde benzersiz olduğundan emin olmak için onaylanır.

    + **Çalışma zamanı yığını seçin**: seçin `Custom Handler` .

    + **Bir işletim sistemi seçin**: Seç `Linux` .

    + **Bir barındırma planı seçin**: seçin `Consumption` .

    + **Bir kaynak grubu seçin**: Seç `+ Create new resource group` . Kaynak grubu için bir ad girin. Bu ad, Azure aboneliğiniz dahilinde benzersiz olmalıdır. İstem içinde önerilen adı kullanabilirsiniz.

    + **Bir depolama hesabı seçin**: seçin `+ Create new storage account` . Bu ad, Azure 'da genel olarak benzersiz olmalıdır. İstem içinde önerilen adı kullanabilirsiniz.

    + **Bir Application Insights kaynağı seçin**: seçin `+ Create Application Insights resource` . Bu ad, Azure 'da genel olarak benzersiz olmalıdır. İstem içinde önerilen adı kullanabilirsiniz.

    + **Yeni kaynaklar için bir konum seçin**: daha iyi performans için, size yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. Uzantı, bildirim alanında Azure 'da oluşturulmakta olan ayrı kaynakların durumunu gösterir.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure kaynak oluşturma bildirimi":::

1. Tamamlandığında, aboneliğinizde aşağıdaki Azure kaynakları oluşturulur:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    İşlev uygulamanız oluşturulduktan sonra bir bildirim görüntülenir ve dağıtım paketi uygulanır. 

4. Oluşturduğunuz Azure kaynakları dahil olmak üzere oluşturma ve dağıtım sonuçlarını görüntülemek için bu bildirimde **çıktıyı görüntüle** ' yi seçin. Bildirimi kaçırırsanız, sağ alt köşedeki zil simgesini seçerek tekrar görüntüleyin.

    ![Tüm bildirimi oluştur](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Işlevleri özel işleyicileri hakkında bilgi edinin](functions-custom-handlers.md)
