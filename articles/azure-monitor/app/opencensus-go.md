---
title: OpenCensus ile Azure Application Insights izleme | Microsoft Docs
description: OpenCensus go izlemeyi yerel iletici ve Application Insights tümleştirme hakkında yönergeler sağlar
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.openlocfilehash: 99f26bb2b89ef9642a36aa2be2037d04aafcdcd4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819274"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Go 'dan dağıtılmış izlemeler toplayın (Önizleme)

Application Insights artık [Opencensus](https://opencensus.io) ve yeni [Yerel ileticimiz](./opencensus-local-forwarder.md)Ile tümleştirme aracılığıyla dağıtılmış go uygulamalarının izlenmesini desteklemektedir. Bu makalede,, Go için OpenCensus ayarlama ve izleme verilerinizi Application Insights alma sürecinde adım adım yönergeler sunulacaktır.

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure Aboneliğine sahip olmanız gerekir.
- Git 'in yüklü olması gerekir, bu makalede 1,11 sürümüne [Git indirme](https://golang.org/dl/)sürümü kullanılmaktadır.
- [Yerel ileticisini bir Windows hizmeti olarak](./opencensus-local-forwarder.md)yüklemek için yönergeleri izleyin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/)’ında oturum açın.

## <a name="create-application-insights-resource"></a>Application Insights kaynağı oluşturma

İlk olarak, bir izleme anahtarı (Ikey) oluşturacak bir Application Insights kaynağı oluşturmanız gerekir. Daha sonra Ikey, OpenCensus tarafından izlenen uygulamanızdaki dağıtılmış izlemeleri Application Insights olarak göndermek üzere yerel ileticisini yapılandırmak için kullanılır.   

1. **Application Insights** > **Geliştirici Araçları** > **kaynak oluştur** ' u seçin.

   ![Application Insights Kaynağı ekleme](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >İlk kez bir Application Insights kaynağı oluşturuyorsanız [Application Insights kaynak oluştur](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) makalesini ziyaret ederek daha fazla bilgi edinebilirsiniz.

   Bir yapılandırma kutusu görünür. Giriş alanlarını doldurmak için aşağıdaki tabloyu kullanın.

    | Ayarlar        | Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Adı**      | Genel Olarak Benzersiz Değer | İzlemekte olduğunuz uygulamayı tanımlayan ad |
   | **Kaynak Grubu**     | myResourceGroup      | App Insights verilerini barındıran yeni kaynak grubunun adı |
   | **Konum** | Doğu ABD | Yakınınızda bulunan veya uygulamanızın barındırıldığı konumun yakınında olan bir konum seçin |

2. **Oluştur**’a tıklayın.

## <a name="configure-local-forwarder"></a>Yerel ileticisi yapılandırma

1. **Genel Bakış** > **Temel Bilgiler**’i seçin > Uygulamanızın **İzleme Anahtarı**’nı kopyalayın.

   ![İzleme anahtarının ekran görüntüsü](./media/opencensus-Go/0003-instrumentation-key.png)

2. `LocalForwarder.config` dosyanızı düzenleyin ve izleme anahtarınızı ekleyin. [Önkoşul](./opencensus-local-forwarder.md) ' deki yönergeleri izlediyseniz, dosyanın bulunduğu `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Uygulama **Yerel ileticisi** hizmetini yeniden başlatın.

## <a name="opencensus-go-packages"></a>OpenCensus go paketleri

1. Komut satırından Go için açık Census paketlerini yükler:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Aşağıdaki kodu bir. go dosyasına ekleyin ve oluşturup çalıştırın. (Bu örnek, yerel ileticiyle tümleştirmeyi kolaylaştıran eklenen kodla resmi OpenCensus kılavuzlarından türetilir)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Basit Go uygulaması çalışmaya başladıktan sonra `http://localhost:50030`gidin. Her tarayıcıyı yenileme işlemi, yerel iletici tarafından çekilen ilgili yayılmış veriler ile birlikte "Hello World" metnini oluşturur.

4. **Yerel ileticinin** izlemelerin kullanıma hazır olduğunu doğrulamak için `LocalForwarder.config` dosyasına bakın. [Önkoşulun](https://docs.microsoft.com/azure/application-insights/local-forwarder)içindeki adımları izlediyseniz `C:\LF-WindowsServiceHost`' de yer alır.

    Günlük dosyasının altındaki görüntüde, `OpenCensus input BatchesReceived` bir dışarı aktarma eklediğimiz ikinci betiği çalıştırmadan önce 0 idi. Güncelleştirilmiş betiği çalıştırmaya başladığımızda, girdiğimiz değer sayısına eşit olarak artırılır `BatchesReceived`:
    
    ![Yeni App Insights kaynağı formu](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Azure portalında izlemeyi başlatma

1. Artık çalışmakta olan uygulamanızla ilgili ayrıntıları görüntülemek için Azure portal Application Insights **genel bakış** sayfasını yeniden açabilirsiniz. **Canlı ölçüm akışı**' nı seçin.

   ![Kırmızı kutuda seçili canlı ölçüm akışının bulunduğu genel bakış bölmesinin ekran görüntüsü](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. İkinci Go uygulamasını yeniden çalıştırırsanız ve `http://localhost:50030`için tarayıcıyı yenilemeyi başlatırsanız, canlı izleme verilerini yerel iletici hizmetinden Application Insights ulaştığı şekilde görürsünüz.

   ![Performans verileri görüntülenirken canlı ölçüm akışının ekran görüntüsü](./media/opencensus-go/0006-stream.png)

3. **Genel bakış** sayfasına geri dönün ve bağımlılık ilişkilerinin görsel düzeni ve uygulama bileşenleriniz arasındaki çağrı zamanlaması Için **uygulama Haritası** ' nı seçin.

    ![Temel uygulama eşlemesinin ekran görüntüsü](./media/opencensus-go/0007-application-map.png)

    Yalnızca bir yöntem çağrısını izliyor olduğundan, uygulama haritamız ilginç değildir. Ancak uygulama Haritası, daha fazla Dağıtılmış uygulamayı görselleştirmek için ölçeklendirebilir:

   ![Uygulama Eşlemesi](media/opencensus-go/application-map.png)

4. Ayrıntılı performans analizi gerçekleştirmek ve performansının yavaşlamasına neden olan performansı belirlemek için **performansı araştır** ' ı seçin.

    ![Performans bölmesinin ekran görüntüsü](./media/opencensus-go/0008-performance.png)

5. **Örnekler** ' i seçip sağ bölmede görüntülenen örneklerden birine tıkladığınızda, uçtan uca işlem ayrıntıları deneyimi başlatılır. Örnek uygulamamız yalnızca tek bir olay gösterirken, daha karmaşık bir uygulama, tek bir olayın çağrı yığınının düzeyine kadar uçtan uca işlemleri araştırmanıza olanak sağlar.

     ![Uçtan uca işlem arabiriminin ekran görüntüsü](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Go için OpenCensus izlemesi

Yalnızca OpenCensus ile yerel iletici ve Application Insights ile ilgili olarak tümleştirme hakkında temel bilgiler ele alınmıştır. [Resmi OpenCensus go kullanım kılavuzu](https://godoc.org/go.opencensus.io) , daha gelişmiş konular içerir.

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama eşlemesi](./../../azure-monitor/app/app-map.md)
* [Uçtan uca performans izleme](./../../azure-monitor/learn/tutorial-performance.md)
