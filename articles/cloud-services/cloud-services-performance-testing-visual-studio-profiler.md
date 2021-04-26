---
title: Işlem öykünücüsünde yerel olarak bir bulut hizmeti (klasik) profili oluşturma | Microsoft Docs
description: Visual Studio Profiler ile bulut hizmetlerindeki performans sorunlarını araştırın
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2f924d84967c1a1928a47b59fd3a8c28da091130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743568"
---
# <a name="testing-the-performance-of-a-cloud-service-classic-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Visual Studio Profiler kullanarak Azure Işlem öykünücüsünde bir bulut hizmeti (klasik) performansını yerel olarak test etme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bulut hizmetlerinin performansını test etmek için çeşitli araçlar ve teknikler mevcuttur.
Azure 'Da bir bulut hizmeti yayımladığınızda, Visual Studio 'Nun profil oluşturma verilerini toplamasını ve sonra [Azure uygulamasında profil oluşturma][1]bölümünde açıklandığı gibi yerel olarak çözümlemenizi sağlayabilirsiniz.
Ayrıca, [Azure 'da performans sayaçlarını kullanma][2]bölümünde açıklandığı gibi çeşitli performans sayaçlarını izlemek için tanılamayı da kullanabilirsiniz.
Ayrıca, Uygulamanızı buluta dağıtmadan önce işlem öykünücüsünde yerel olarak profili de isteyebilirsiniz.

Bu makale, Öykünücüde yerel olarak yapılabilen profil oluşturma için CPU örnekleme yöntemini ele almaktadır. CPU örnekleme, profil oluşturmanın çok zorsız bir yöntemdir. Belirlenmiş bir örnekleme aralığı içinde profil oluşturucu, çağrı yığınının anlık görüntüsünü alır. Veriler bir süre içinde toplanır ve bir raporda gösterilir. Bu profil oluşturma yöntemi, CPU işinin büyük bir bölümünü yoğun şekilde yoğun bir uygulamada nerede yapıldığını belirtmek için eğilimi gösterir.  Bu, uygulamanızın en çok zaman harcaacağı "etkin yol" üzerine odaklanma fırsatı sağlar.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Visual Studio 'Yu profil oluşturma için yapılandırma
İlk olarak, profil oluşturma sırasında yararlı olabilecek birkaç Visual Studio yapılandırma seçeneği vardır. Profil oluşturma raporlarının anlamlı olması için, uygulamanız için semboller (. pdb dosyaları) ve ayrıca sistem kitaplıkları için semboller gerekir. Kullanılabilir sembol sunucularına başvurduğunuzdan emin olmak isteyeceksiniz. Bunu yapmak için, Visual Studio 'daki **Araçlar** menüsünde **Seçenekler**' i ve ardından **hata ayıklama** ve **semboller**' i seçin. Microsoft symbol sunucularının **sembol dosyası (. pdb) konumları** altında listelendiğinden emin olun.  Ayrıca https://referencesource.microsoft.com/symbols , ek sembol dosyalarına sahip olabilecek başvuru de oluşturabilirsiniz.

![Sembol seçenekleri][4]

İsterseniz, profil oluşturucunun oluşturduğu raporları Yalnızca kendi kodum ayarlayarak basitleştirebilirsiniz. Yalnızca kendi kodum etkinken, işlev çağrısı yığınları basitleştirilerek tamamen iç kitaplıklara çağrı yapılır ve .NET Framework raporlardan gizlenir. **Araçlar** menüsünde **Seçenekler**' i seçin. Ardından **performans araçları** düğümünü genişletin ve **genel**' i seçin. **Profil Oluşturucu raporları için yalnızca kendi kodum etkinleştir** onay kutusunu seçin.

![Yalnızca kendi kodum seçenekleri][17]

Bu yönergeleri mevcut bir projeyle veya yeni bir projeyle birlikte kullanabilirsiniz.  Aşağıda açıklanan teknikleri denemek için yeni bir proje oluşturursanız, bir C# **Azure bulut hizmeti** projesi seçin ve bir **Web rolü** ve bir **çalışan rolü** seçin.

![Azure bulut hizmeti proje rolleri][5]

Örneğin, projenize çok zaman alan ve bazı açık performans sorunları gösteren bazı kodlar ekleyin. Örneğin, aşağıdaki kodu bir çalışan rolü projesine ekleyin:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Çalışan rolünün RoleEntryPoint-Derived sınıfındaki RunAsync yönteminden bu kodu çağırın. (Zaman uyumlu olarak çalışan yöntemi hakkındaki uyarıyı yoksayın.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Çözüm yapılandırması **yayın** olarak ayarlanmış şekilde, bulut hizmetinizi hata ayıklamadan (CTRL + F5) yerel olarak derleyin ve çalıştırın. Bu, uygulamayı yerel olarak çalıştırmak için tüm dosya ve klasörlerin oluşturulmasını sağlar ve tüm öykünücülerin başlatılmasını sağlar. Çalışan rolünüzün çalıştığını doğrulamak için görev çubuğundan Işlem öykünücüsü Kullanıcı arabirimini başlatın.

## <a name="2-attach-to-a-process"></a>2: bir işleme iliştirme
Visual Studio 2010 IDE 'den başlatarak uygulamanın profilini oluşturmak yerine, profil oluşturucuyu çalışan bir işleme bağlamanız gerekir. 

Profil oluşturucuyu bir işleme iliştirmek için, **Çözümle** menüsünde **Profil Oluşturucu** ve **Ekle/ayır**' ı seçin.

![Profil ekle seçeneği][6]

Bir çalışan rolü için WaWorkerHost.exe işlemini bulun.

![WaWorkerHost işlemi][7]

Proje klasörünüz bir ağ sürücüsündeyse profil oluşturucu, profil oluşturma raporlarının kaydedileceği başka bir konum sağlamanızı ister.

 Ayrıca, WaIISHost.exe ekleyerek bir Web rolüne de iliştirebilirsiniz.
Uygulamanızda birden çok çalışan rolü işlemi varsa, bunları ayırt etmek için Işlem işlemini kullanmanız gerekir. Işlem nesnesine erişerek ProcessId 'yi programlı bir şekilde sorgulayabilirsiniz. Örneğin, bu kodu bir roldeki RoleEntryPoint-Derived sınıfının Run yöntemine eklerseniz, hangi işlemin bağlanacağı hakkında bilgi edinmek için Işlem öykünücüsü Kullanıcı arabirimindeki günlüğe bakabilirsiniz.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Günlüğü görüntülemek için, Işlem öykünücüsü Kullanıcı arabirimini başlatın.

![Işlem öykünücüsü Kullanıcı arabirimini başlatma][8]

Konsol penceresinin başlık çubuğuna tıklayarak, Işlem öykünücüsü Kullanıcı arabirimindeki çalışan rolü günlüğü konsol penceresini açın. İşlem KIMLIĞINI günlükte görebilirsiniz.

![İşlem KIMLIĞINI görüntüle][9]

Eklediğiniz bir tane, senaryonun yeniden oluşturulması için uygulamanızın kullanıcı arabirimindeki adımları gerçekleştirin (gerekirse).

Profil oluşturmayı durdurmak istediğinizde, **profil oluşturmayı durdur** bağlantısını seçin.

![Profil oluşturma seçeneğini durdur][10]

## <a name="3-view-performance-reports"></a>3: performans raporlarını görüntüleme
Uygulamanız için performans raporu görüntülenir.

Bu noktada, profil oluşturucu yürütmeyi sonlandırır, verileri bir. vsp dosyasına kaydeder ve bu verilerin analizini gösteren bir rapor görüntüler.

![Profil Oluşturucu raporu][11]

Etkin yolda String. wstrcpy görürseniz, görünümü yalnızca Kullanıcı kodunu gösterecek şekilde değiştirmek için Yalnızca kendi kodum ' a tıklayın.  String. Concat görürseniz, tüm kodu göster düğmesine basmayı deneyin.

Birleştirme yöntemi ve dize. Concat, yürütme süresinin büyük bir kısmını gerçekleştirerek.

![Raporun Analizi][12]

Bu makaleye dize birleştirme kodu eklediyseniz, bunun için Görev Listesi bir uyarı görmeniz gerekir. Ayrıca, oluşturulan ve bırakılan dizelerin sayısından kaynaklanan aşırı miktarda çöp toplama işlemi olduğunu belirten bir uyarı da görebilirsiniz.

![Performans uyarıları][14]

## <a name="4-make-changes-and-compare-performance"></a>4: değişiklik yapma ve performansı karşılaştırma
Ayrıca, bir kod değişikliğinden önceki ve sonraki performansı da karşılaştırabilirsiniz.  Çalışan işlemi durdurun ve dize birleştirme işlemini, StringBuilder 'ın kullanımıyla değiştirecek şekilde kodu düzenleyin:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Başka bir performans çalıştırması yapın ve ardından performansı karşılaştırın. Performans Gezgini, çalıştırmalar aynı oturumdaydaysanız, her iki raporu da seçebilir, kısayol menüsünü açabilir ve **performans raporlarını karşılaştır**' ı seçebilirsiniz. Başka bir performans oturumunda bir çalıştırmaya göre karşılaştırmak istiyorsanız, **Çözümle** menüsünü açın ve **performans raporlarını karşılaştır**' ı seçin. Görüntülenen iletişim kutusunda her iki dosyayı da belirtin.

![Performans raporlarını karşılaştır seçeneği][15]

Raporlar iki çalıştırma arasındaki farkları vurgular.

![Karşılaştırma raporu][16]

Tebrikler! Profil Oluşturucu ile çalışmaya başlayın.

## <a name="troubleshooting"></a>Sorun giderme
* Yayın derlemesinin profilini oluşturmadan ve hata ayıklamadan başladığınızdan emin olun.
* Profil Oluşturucu menüsünde Iliştirme/Ayır seçeneği etkinleştirilmemişse, performans sihirbazını çalıştırın.
* Uygulamanızın durumunu görüntülemek için Işlem öykünücüsü Kullanıcı arabirimini kullanın. 
* Öykünücüdeki uygulamaları başlatırken veya profil oluşturucuyu iliştirirken sorun yaşıyorsanız, işlem öykünücüsünü kapatın ve yeniden başlatın. Bu sorunu çözmezse, yeniden başlatmayı deneyin. Bu sorun, çalışan dağıtımları askıya almak ve kaldırmak için Işlem öykünücüsünü kullanırsanız ortaya çıkabilir.
* Komut satırından profil oluşturma komutlarından birini, özellikle de genel ayarları kullandıysanız, VSPerfClrEnv/globaloff ' ın çağrıldığından ve VsPerfMon.exe kapatıldığından emin olun.
* Örnekleme yapıldığında, "PRF0025: veri toplanmadı" iletisini görürsünüz, bağlı olduğunuz işlemin CPU etkinliği olduğunu kontrol edin. Herhangi bir hesaplama çalışması yapmamayan uygulamalar, hiçbir örnekleme verisi üretmeyebilir.  Herhangi bir örnekleme yapılmadan önce işlemden çıkılması da mümkündür. Profil oluşturduğunuz bir rolün run yönteminin sonlanmadığından emin olun.

## <a name="next-steps"></a>Sonraki Adımlar
Öykünücüdeki Azure ikililerini düzenleme işlemi Visual Studio Profiler 'da desteklenmez, ancak bellek ayırmayı test etmek istiyorsanız, profil oluştururken bu seçeneği belirleyebilirsiniz. Ayrıca, iş parçacıklarının, bir uygulamanın katmanları arasında etkileşim kurarken, genellikle veri katmanı ve bir çalışan rolü arasındaki performans sorunlarını izlemenize yardımcı olan eşzamanlılık profili oluşturma işlemini de tercih edebilirsiniz.  Uygulamanızın oluşturduğu veritabanı sorgularını görüntüleyebilir ve veritabanının kullanımını geliştirmek için profil oluşturma verilerini kullanın. Katman etkileşimi profili oluşturma hakkında bilgi için bkz. blog gönderisi Kılavuzu [: Visual Studio Team System 2010 ' de katman etkileşimi profil oluşturucuyu kullanma][3].

[1]: ../azure-monitor/app/profiler.md
[2]: /previous-versions/azure/hh411542(v=azure.100)
[3]: /archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png