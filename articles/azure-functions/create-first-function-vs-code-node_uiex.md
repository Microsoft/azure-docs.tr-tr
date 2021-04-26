---
title: Visual Studio Code-Azure Işlevleri kullanarak bir JavaScript işlevi oluşturma
description: Bir JavaScript işlevi oluşturmayı öğrenin ve ardından Visual Studio Code Azure Işlevleri uzantısını kullanarak Azure Işlevleri 'nde sunucusuz barındırma için yerel Node.js projesini yayımlayın.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom:
- devx-track-js
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cf47d04da51db898e667ef8b31d42d79c9f354e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748849"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code kullanarak Azure 'da JavaScript işlevi oluşturma

> [!div class="op_single_selector" title1="İşlev dilinizi seçin: "]
> - [JavaScript](create-first-function-vs-code-node.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Diğer (Go/Rust)](create-first-function-vs-code-other.md)

HTTP isteklerine yanıt veren bir JavaScript işlevi oluşturmak için Visual Studio Code kullanın. Kodu yerel olarak test edin ve daha sonra Azure Işlevlerinin sunucusuz ortamına dağıtın.

Bu hızlı başlangıcı tamamlamak, birkaç ABD Doları veya daha kısa bir ücret doğurur <abbr title="Azure hesabı, Azure hizmetlerine ve Azure aboneliklerinize erişmenizi sağlayan küresel bir benzersiz varlıktır.">Azure hesabı</abbr>.

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

Başlamadan önce, aşağıdaki gereksinimlerin yerinde olduğundan emin olun:

+ ' A sahip bir Azure hesabı <abbr title="Azure aboneliği, Azure’da kaynakların sağlanması için kullanılan mantıksal bir kapsayıcıdır. Sanal makineler (VM) ve veritabanları gibi tüm kaynaklarınızın ayrıntılarını içerir.">etkin abonelik</abbr>. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1 +](https://nodejs.org/)

+ [Visual Studio Code](https://code.visualstudio.com/)

+ Visual Studio Code için [Azure işlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .

+ [Azure Işlevleri temel araçları](functions-run-local.md?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)

<hr/>
<br/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> yerel işlevler projenizi oluşturun

1. İçindeki Azure simgesini seçin <abbr title="">Etkinlik çubuğu</abbr>, ardından **Azure: Functions** alanında **Yeni proje oluştur...** simgesini seçin.

    ![Yeni proje oluştur'u seçin](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Proje çalışma alanınız için **bir dizin konumu seçin** ve ardından **Seç**' i seçin. 

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **İşlev projeniz için bir dil seçin**: `JavaScript` öğesini seçin.

    + **Projenizin ilk işlevi için bir şablon seçin**: `HTTP trigger` öğesini seçin.

    + **Bir işlev adı sağlayın**: `HttpExample` yazın.

    + **Yetkilendirme düzeyi**: `Anonymous` herhangi bir kişinin işlev uç noktanızı çağırmasını sağlayan öğesini seçin.

    + **Projenizi nasıl açmak istediğinizi seçin**: `Add to workspace` öğesini seçin.




<br/>
<details>
<summary><strong>Bir işlev projesi oluşturulamıyor mu?</strong></summary>

Yerel Işlevler projesi oluştururken çözülecek en yaygın sorunlar şunlardır:
* Azure Işlevleri uzantısı yüklü değil. 
</details>

<hr/>
<br/>

## <a name="3-run-the-function-locally"></a>3. işlevi yerel olarak çalıştırın


1. İşlev uygulaması projesini başlatmak için <kbd>F5</kbd> tuşuna basın. 

1. **Terminalde**, işlevinizin yerel olarak çalıştırıldığı URL uç noktasına bakın.

    ![Yerel işlev VS Code çıkışı](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Aşağıdaki URL 'YI kopyalayın ve bir Web tarayıcısına yapıştırın ve ENTER tuşuna basın.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Döndürülen yanıtı görüntüleyin.


    ![Tarayıcı-localhost örnek çıktısı](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. **Terminal** panelinde istekle ilgili bilgileri görüntüleyin.

    ![Görev ana bilgisayarı başlatma-VS Code Terminal çıkışı](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Temel araçları durdurmak ve hata ayıklayıcının bağlantısını kesmek için <kbd>CTRL + C</kbd> tuşlarına basın.

<br/>
<details>
<summary><strong>İşlev yerel olarak çalıştırılamıyor musunuz?</strong></summary>

Yerel Işlevler projesi çalıştırılırken çözülmesi için en yaygın sorunlar şunlardır:
* Temel araçlar yüklü değil. 
*  Windows üzerinde çalışırken sorun yaşıyorsanız, Visual Studio Code için varsayılan Terminal kabuğunun WSL Bash olarak ayarlandığından emin olun. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Azure 'da oturum açın

Uygulamanızı yayımlamak için Azure 'da oturum açın. Zaten oturum açtıysanız sonraki bölüme gidin.

1. Etkinlik çubuğunda Azure simgesini seçin, sonra **Azure: işlevler** alanında **Azure 'da oturum aç ' ı seçin...**

    ![VS Code içinde Azure 'da oturum açın](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Tarayıcıda istendiğinde, **Azure hesabınızı seçin** ve Azure hesabı kimlik bilgilerinizi kullanarak **oturum açın** .

1. Başarıyla oturum açtıktan sonra, yeni tarayıcı penceresini kapatıp Visual Studio Code ' a geri dönün. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. projeyi Azure 'da yayımlayın

Kodunuzun ilk dağıtımı, Azure aboneliğinizde bir Işlev kaynağı oluşturmayı içerir. 

1. Etkinlik çubuğunda Azure simgesini seçin, sonra **Azure: işlevler** alanında, **işlev uygulamasına dağıt uygulama...** düğmesini seçin.

    ![Projenizi Azure'da yayımlama](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **Klasör seçin**: işlev uygulamanızı içeren klasörü seçin. 

    + **Abonelik seçin**: kullanılacak aboneliği seçin. Yalnızca bir aboneliğiniz varsa bunu göremezsiniz.

    + **Azure'daki işlev uygulamasını seçin**: `+ Create new Function App` öğesini seçin.

    + **İşlev uygulaması için genel olarak benzersiz bir ad girin**: bir URL yolunda Azure genelinde benzersiz bir ad yazın. Yazdığınız ad, genel benzersizlik sağlamak için onaylanır.

    + **Çalışma zamanı seçin**: yerel olarak çalıştırdığınız Node.js sürümünü seçin. `node --version`Sürümünüzü denetlemek için komutunu kullanabilirsiniz.

    + **Yeni kaynaklar için bir konum seçin**: daha iyi performans için, size yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. 

1. İşlev uygulamanız oluşturulduktan sonra bir bildirim görüntülenir ve dağıtım paketi uygulanır. Oluşturma ve dağıtım sonuçlarını görmek için **çıktıyı görüntüle** ' yi seçin. 
    
    ![Tüm bildirimi oluştur](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>İşlev yayımlanamıyor mu?</strong></summary>

Bu bölüm, Azure kaynaklarını oluşturdu ve yerel kodunuzu Işlev uygulamasına dağıttı. Bu başarılı olmadıysa:

* Hata bilgileri için çıktıyı gözden geçirin. Sağ alt köşedeki zil simgesi çıktıyı görüntülemenin başka bir yoludur. 
* Var olan bir işlev uygulamasına yayımladınız mı? Bu eylem, Azure 'daki uygulamanın içeriğinin üzerine yazar.
</details>


<br/>
<details>
<summary><strong>Hangi kaynaklar oluşturuldu?</strong></summary>

Tamamlandığında, aşağıdaki Azure kaynakları, işlev uygulamanızın adına göre adlar kullanılarak aboneliğinizde oluşturulur: 
* **Kaynak grubu**: kaynak grubu, aynı bölgedeki ilgili kaynaklar için bir mantıksal kapsayıcıdır.
* **Azure depolama hesabı**: bir depolama kaynağı, projeniz hakkındaki durumu ve diğer bilgileri korur.
* **Tüketim planı**: bir tüketim planı, sunucusuz işlev uygulamanız için temel ana bilgisayarı tanımlar.
* **İşlev uygulaması**: bir işlev uygulaması, işlev kodunuzu ve grup işlevlerinizi mantıksal birim olarak yürütmek için ortam sağlar.
* **Application Insights**: Application Insights sunucusuz işlevinizin kullanımını izler.

</details>





<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. işlevi Azure 'da çalıştırın
1. **Azure: Functions** yan çubuğunda yeni işlev uygulamasını genişletin. 
1. **İşlevler**' i genişletin, ardından **httpexample** öğesine sağ tıklayın ve ardından **işlevi Şimdi Çalıştır...** seçeneğini belirleyin.

    ![Yeni HTTP tetikleyicisinin işlev URL 'sini kopyalayın](../../includes/media/functions-vs-code-run-remote/execute-function-now.png)

1. İşlevinizin varsayılan istek iletisini göndermek için **ENTER tuşuna basın** . 

1. Yürütme tamamlandığında Visual Studio Code bir bildirim oluşturulur.

<br/>
<details>
<summary><strong>Bulut tabanlı Işlev uygulaması çalıştırılamadı mi?</strong></summary>

* URL 'nin sonuna QueryString eklemeyi hatırladınız mı?

</details>

<hr/>
<br/>

## <a name="7-clean-up-resources"></a>7. Kaynakları Temizleme

Daha fazla maliyet ödemeden kaçınmak için işlev uygulamasını ve kaynaklarını silin.

1. Visual Studio Code ' de, etkinlik çubuğundan Azure simgesini seçin ve ardından yan çubukta Işlevler alanını seçin. 
1. İşlev uygulamasını seçin, sonra sağ tıklayıp **işlev uygulamasını Sil...** seçeneğini belirleyin.

<hr/>
<br/>

## <a name="next-steps"></a>Sonraki adımlar

Şunu ekleyerek işlevi genişletin <abbr title="İşleve bağlama, başka bir kaynağı işleve bildirimli olarak bağlama yöntemidir.">çıkış bağlama</abbr>. Bu bağlama, HTTP isteğinden dizeyi bir Azure kuyruk depolama kuyruğundaki bir iletiye yazar. 

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
