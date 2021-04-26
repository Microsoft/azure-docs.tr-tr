---
title: Komut satırından Java işlevi oluşturma-Azure Işlevleri
description: Komut satırından Java işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 910e19137873ed5c34584b3293ebd4248c5fe2f8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831915"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: komut satırından Azure 'da Java işlevi oluşturma

> [!div class="op_single_selector" title1="İşlev dilinizi seçin: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

HTTP isteklerine yanıt veren bir Java işlevi oluşturmak için komut satırı araçlarını kullanın. Kodu yerel olarak test edin ve daha sonra Azure Işlevlerinin sunucusuz ortamına dağıtın.

Bu hızlı başlangıcı tamamlamak, birkaç ABD Doları veya daha kısa bir ücret doğurur <abbr title="Azure kullanımı için fatura bilgilerini tutan profil.">Azure hesabı</abbr>.

Maven, tercih ettiğiniz geliştirme aracınız değilse, [Gradle](./functions-create-first-java-gradle.md), [IntelliJ fikir](/azure/developer/java/toolkit-for-intellij/quickstart-functions) ve [Visual Studio Code](create-first-function-vs-code-java.md)kullanarak Java geliştiricileri için benzer öğreticilerimize göz atın.

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin bir Azure hesabı <abbr title="Azure 'da, genellikle bir kuruluş içindeki bir birey veya departmanla ilişkili kaynakları yönettiğiniz temel kuruluş yapısıdır.">aboneliği</abbr>. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x.

+ [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya sonraki bir sürümü.

+ [Java geliştirici seti](/azure/developer/java/fundamentals/java-jdk-long-term-support), sürüm 8 veya 11. `JAVA_HOME`Ortam değişkeni, JDK 'nin doğru sürümünün yükleneceği konum olarak ayarlanmalıdır.

+ [Apache Maven](https://maven.apache.org), sürüm 3,0 veya üzeri.

### <a name="prerequisite-check"></a>Önkoşul denetimi

+ Bir Terminal veya komut `func --version` penceresinde, <abbr title="Yerel bilgisayarınızda Azure Işlevleri ile çalışmaya yönelik komut satırı araçları kümesi.">Azure Functions Core Tools</abbr> sürüm 3. x ' dir.

+ `az --version`Azure CLI sürümünün 2,4 veya üzeri olduğunu denetlemek için ' i çalıştırın.

+ `az login`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. yerel bir işlev projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. <abbr title="Bir HTTP isteği, kuyruk iletisi veya belirli bir zaman gibi işlevin kodunu çağıran olay türü.">Tetikleyicinin</abbr>. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, tek bir işlev içeren bir işlev projesi oluşturursunuz.

1. İşlevler projesini bir [Maven arketipinden](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) oluşturmak için boş bir klasörde aşağıdaki komutu çalıştırın. 

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```

    ---

    <br/>
    <details>
    <summary><strong>Java 11 ' de işlevleri çalıştırmak için</strong></summary>

    İşlevlerinizin Java 11 üzerinde çalışmasını istiyorsanız `-DjavaVersion=11` ifadesini kullanın. Daha fazla bilgi için bkz. [Java sürümleri](functions-reference-java.md#java-versions).
    </details>

1. Maven, dağıtımda projenin oluşturulmasını tamamlaması için gereken değerleri ister.
    İstendiğinde aşağıdaki değerleri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Java için [paket adlandırma kurallarını](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) takip eden tüm projelerde projenizi benzersiz bir şekilde tanımlayan bir değer. |
    | **ArtifactId** | `fabrikam-functions` | Bir sürüm numarası olmadan jar adı olan bir değer. |
    | **Sürüm** | `1.0-SNAPSHOT` | Varsayılan değeri seçin. |
    | **leyebilir** | `com.fabrikam` | Oluşturulan işlev kodu için Java paketi olan bir değer. Varsayılan değeri kullanın. |

1. `Y`Onaylamak için yazın veya ENTER tuşuna basın.

    Maven, proje dosyalarını, bu örnekte olduğu gibi, _ArtifactId_ adında yeni bir klasörde oluşturur `fabrikam-functions` . 

1. Proje klasörüne gidin:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>LocalFunctionProj klasöründe oluşturulan özellikler nelerdir?</strong></summary>

Bu klasör, proje için *function. Java*, *functiontest. Java* ve *pom.xml* gibi çeşitli dosyaları içerir. Ayrıca, [ üzerindelocal.settings.js](functions-run-local.md#local-settings-file) adlı ve [host.js](functions-host-json.md)yapılandırma dosyaları da vardır. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.
</details>

<br/>
<details>
<summary><strong>Function. Java için kod</strong></summary>

*Function. Java* , `run` değişkende istek verilerini alan bir yöntem içerir `request` . Bu, tetikleme davranışını tanımlayan [httptrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) ek açıklaması ile donatılmış bir [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) 'dir. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Yanıt iletisi [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API 'si tarafından oluşturulur.

Ayrıca, bu, işleviniz için bir birim testi de oluşturur. Bağlama eklemek veya projeye yeni işlevler eklemek için işlevinizi değiştirdiğinizde, *Functiontest. Java* dosyasındaki testleri de değiştirmeniz gerekir.
</details>

<br/>
<details>
<summary><strong>pom.xmliçin kod </strong></summary>

Uygulamanızı barındırmak üzere oluşturulan Azure kaynakları için ayarlar, eklentinin **yapılandırma** öğesinde oluşturulanpom.xmldosyasında bir **GroupID** ile tanımlanmıştır `com.microsoft.azure` .  Örneğin, aşağıdaki yapılandırma öğesi, bir Maven tabanlı dağıtıma `java-functions-group` , kaynak grubunda bir işlev uygulaması oluşturmak için `westus` <abbr title="Kaynakların ayrıldığı belirli bir Azure veri merkezine coğrafi başvuru.">region</abbr>. İşlev uygulaması, planda barındırılan Windows üzerinde çalışır `java-functions-app-service-plan` ; Bu, varsayılan olarak sunucusuz bir tüketim plandır.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Bu ayarları, kaynakların Azure 'da nasıl oluşturulduğunu denetlemek için, `runtime.os` `windows` ilk dağıtımdan önce ' dan önce ' ye geçiş yaparak yapabilirsiniz `linux` . Maven eklentisi tarafından desteklenen ayarların tam listesi için [yapılandırma ayrıntılarına](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)bakın.
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. işlevi yerel olarak çalıştırın

1. Yerel Azure Işlevleri çalışma zamanı konağını *Localfunctionproj* klasöründen başlatarak **işlevinizi çalıştırın** :

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    Çıktının sonuna doğru, aşağıdaki satırlar görünmelidir:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    HttpExample yukarıda gösterildiği gibi görünmezse, büyük olasılıkla ana bilgisayarı projenin kök klasörü dışından başlatmış olursunuz. Bu durumda, Konağı durdurmak için <kbd>CTRL + C</kbd> tuşlarını kullanın, projenin kök klasörüne gidin ve önceki komutu yeniden çalıştırın.

1.  `HttpExample` İşlevinizin URL 'sini bu çıktıdan tarayıcıya kopyalayın ve sorgu DIZESINI ekleyerek `?name=<YOUR_NAME>` tam URL 'yi yapın `http://localhost:7071/api/HttpExample?name=Functions` . Tarayıcı şöyle bir ileti görüntülemelidir `Hello Functions` :

    ![İşlevin sonuçları tarayıcıda yerel olarak çalıştırıldı](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Projenizi başlattığınız Terminal Ayrıca, istek yaptığınız sürece günlük çıktısını gösterir.

1. İşiniz bittiğinde, <kbd>CTRL + C</kbd> tuşlarını kullanın ve işlevler ana bilgisayarını durdurmak için <kbd>y</kbd> 'yi seçin.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. işlev projesini Azure 'a dağıtma

İşlevler projenizi ilk kez dağıttığınızda Azure 'da bir işlev uygulaması ve ilgili kaynaklar oluşturulur. Uygulamanızı barındırmak üzere oluşturulan Azure kaynakları için ayarlar *pom.xml* dosyasında tanımlanmıştır. Bu makalede, Varsayılanları kabul edersiniz.

<br/>
<details>
<summary><strong>Linux üzerinde çalışan bir işlev uygulaması oluşturmak için</strong></summary>

Windows yerine Linux üzerinde çalışan bir işlev uygulaması oluşturmak için, `runtime.os` *pom.xml* dosyasındaki öğesini `windows` olarak değiştirin `linux` . [Bu bölgelerde](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)Linux çalıştırmak bir tüketim planında desteklenir. Linux ve uygulamalar üzerinde çalışan ve aynı kaynak grubunda Windows üzerinde çalışan uygulamalar olamaz.
</details>

1. Dağıtım yapabilmeniz için önce Azure CLı veya Azure PowerShell kullanarak Azure aboneliğinizde oturum açın. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [Az Login](/cli/azure/reference-index#az_login) komutu sizi Azure hesabınızda oturum açar.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'i Azure hesabınızda oturum açar.

    ---

1. Projenizi yeni bir işlev uygulamasına dağıtmak için aşağıdaki komutu kullanın.

    ```console
    mvn azure-functions:deploy
    ```

    Dağıtım proje dosyalarını paketler ve bunları [ZIP dağıtımı](functions-deployment-technologies.md#zip-deploy)kullanarak yeni işlev uygulamasına dağıtır. Kod, Azure 'daki dağıtım paketinden çalışır.

<br/>
<details>
<summary><strong>Azure 'da oluşturulan özellikler nelerdir?</strong></summary>

+ Kaynak grubu. _Java-Functions-Group_ olarak adlandırılır.
+ Depolama hesabı. Işlevleri için gereklidir. Ad, depolama hesabı adı gereksinimlerine göre rastgele oluşturulur.
+ Barındırma planı. _Westus_ bölgesindeki işlev uygulamanız için sunucusuz barındırma. Ad, _Java-Functions-App-Service-plan_' dır.
+ İşlev uygulaması. İşlev uygulaması, işlevleriniz için dağıtım ve yürütme birimidir. Ad, rastgele oluşturulmuş bir sayı eklenerek, _ArtifactId_ temel alınarak rastgele oluşturulur.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Azure 'da işlevi çağırın

İşleviniz bir HTTP tetikleyicisi kullandığından, tarayıcıda URL 'sine veya şunun gibi bir araçla **http isteği yaparak bu uygulamayı çağırabilirsiniz** . <abbr title="URL 'ye HTTP istekleri oluşturmak için bir komut satırı aracı; bakýn https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Tarayıcı](#tab/browser)

Komutun çıktısında gösterilen tüm **ÇAĞıRMA URL** 'sini, `publish` sorgu parametresini ekleyerek bir tarayıcı adres çubuğuna kopyalayın `&name=Functions` . , İşlevi yerel olarak çalıştırdığınızda tarayıcı benzer bir çıktı görüntülemelidir.

![Azure üzerinde çalışan işlevin çıktısı bir tarayıcıda](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

[`curl`](https://curl.haxx.se/)Parametresini ekleyerek **Invoke URL 'si** ile çalıştırın `&name=Functions` . Komutun çıktısı, "Hello Functions" metni olmalıdır.

![İşlevin çıktısı, Azure 'da kıvrımlı kullanarak çalıştırıldı](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Kaynakları Temizleme

Bir [sonraki adımla](#next-steps) devam ederseniz ve bir Azure depolama alanı eklerseniz <abbr title="Azure depolama 'da bir işlevi bir depolama kuyruğu ile ilişkilendirmekte, bu sayede sırada ileti oluşturabilmesi anlamına gelir.">kuyruk çıkış bağlaması</abbr>, daha önce yapmış olduğunuz yerde oluşturacağınız sürece tüm kaynaklarınızı saklayın.

Aksi takdirde, daha fazla maliyet ödemeden kaçınmak için kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
