---
title: include dosyası
description: include dosyası
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 08/16/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 2684f89088854ecf05c547c77852155168baa38d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67188606"
---
### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

Visual Studio'da yeni bir **Konsol Uygulaması (.NET Framework)** projesi oluşturun.

### <a name="add-the-relay-nuget-package"></a>Geçiş NuGet paketini ekleme

1. Yeni oluşturulan projeye sağ tıklayıp **NuGet Paketlerini Yönet**'i seçin.
2. **Ön sürümü dahil et** seçeneğini işaretleyin. 
3. **Göz at**'ı seçip **Microsoft.Azure.Relay** araması yapın. Arama sonuçlarında **Microsoft Azure Geçişi**'ni seçin.
4. Sürüm alanında **2.0.0-preview1-20180523** girişini seçin. 
5. Yüklemeyi tamamlamak için **Yükle**'yi seçin. İletişim kutusunu kapatın.

### <a name="write-code-to-receive-messages"></a>İleti almak için kod yazma

1. Program.cs dosyasının üst tarafındaki `using` deyimlerini aşağıdaki `using` deyimleriyle değiştirin:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net;
    using Microsoft.Azure.Relay;
    ```
2. Karma bağlantı ayrıntıları için sabitleri `Program` sınıfına ekleyin. Köşeli ayraçlar içindeki yer tutucuları, karma bağlantıyı oluştururken aldığınız değerlerle değiştirin. Tam ad alanı adını kullandığınızdan emin olun.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. `ProcessMessagesOnConnection` yönetimini `Program` sınıfına ekleyin:
   
    ```csharp
    // The method initiates the connection.
    private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
    {
        Console.WriteLine("New session");
   
        // The connection is a fully bidrectional stream. 
        // Put a stream reader and a stream writer over it.  
        // This allows you to read UTF-8 text that comes from 
        // the sender, and to write text replies back.
        var reader = new StreamReader(relayConnection);
        var writer = new StreamWriter(relayConnection) { AutoFlush = true };
        while (!cts.IsCancellationRequested)
        {
            try
            {
                // Read a line of input until a newline is encountered.
                var line = await reader.ReadLineAsync();
   
                if (string.IsNullOrEmpty(line))
                {
                    // If there's no input data, signal that 
                    // you will no longer send data on this connection,
                    // and then break out of the processing loop.
                    await relayConnection.ShutdownAsync(cts.Token);
                    break;
                }
   
                // Write the line on the console.
                Console.WriteLine(line);
   
                // Write the line back to the client, prepended with "Echo:"
                await writer.WriteLineAsync($"Echo: {line}");
            }
            catch (IOException)
            {
                // Catch an I/O exception. This likely occurred when
                // the client disconnected.
                Console.WriteLine("Client closed connection");
                break;
            }
        }
   
        Console.WriteLine("End session");
   
        // Close the connection.
        await relayConnection.CloseAsync(cts.Token);
    }
    ```
4. `RunAsync` yönetimini `Program` sınıfına ekleyin:
   
    ```csharp
    private static async Task RunAsync()
    {
        var cts = new CancellationTokenSource();
   
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Subscribe to the status events.
        listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
        listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
        listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
        // Opening the listener establishes the control channel to
        // the Azure Relay service. The control channel is continuously 
        // maintained, and is reestablished when connectivity is disrupted.
        await listener.OpenAsync(cts.Token);
        Console.WriteLine("Server listening");
   
        // Provide callback for the cancellation token that will close the listener.
        cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
        // Start a new thread that will continuously read the console.
        new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
        // Accept the next available, pending connection request. 
        // Shutting down the listener allows a clean exit. 
        // This method returns null.
        while (true)
        {
            var relayConnection = await listener.AcceptConnectionAsync();
            if (relayConnection == null)
            {
                break;
            }
   
            ProcessMessagesOnConnection(relayConnection, cts);
        }
   
        // Close the listener after you exit the processing loop.
        await listener.CloseAsync(cts.Token);
    }
    ```
5. Aşağıdaki kod satırını `Program` sınıfındaki `Main` yöntemine ekleyin:
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Tamamlanmış Program.cs dosyası aşağıdaki gibi görünmelidir:
   
    ```csharp
    namespace Server
    {
        using System;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Relay;
   
        public class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            public static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                var cts = new CancellationTokenSource();
   
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Subscribe to the status events.
                listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
                listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
                listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
                // Opening the listener establishes the control channel to
                // the Azure Relay service. The control channel is continuously 
                // maintained, and is reestablished when connectivity is disrupted.
                await listener.OpenAsync(cts.Token);
                Console.WriteLine("Server listening");
   
                // Provide callback for a cancellation token that will close the listener.
                cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
                // Start a new thread that will continuously read the console.
                new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
                // Accept the next available, pending connection request. 
                // Shutting down the listener allows a clean exit. 
                // This method returns null.
                while (true)
                {
                    var relayConnection = await listener.AcceptConnectionAsync();
                    if (relayConnection == null)
                    {
                        break;
                    }
   
                    ProcessMessagesOnConnection(relayConnection, cts);
                }
   
                // Close the listener after you exit the processing loop.
                await listener.CloseAsync(cts.Token);
            }
   
            private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
            {
                Console.WriteLine("New session");
   
                // The connection is a fully bidrectional stream. 
                // Put a stream reader and a stream writer over it.  
                // This allows you to read UTF-8 text that comes from 
                // the sender, and to write text replies back.
                var reader = new StreamReader(relayConnection);
                var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                while (!cts.IsCancellationRequested)
                {
                    try
                    {
                        // Read a line of input until a newline is encountered.
                        var line = await reader.ReadLineAsync();
   
                        if (string.IsNullOrEmpty(line))
                        {
                            // If there's no input data, signal that 
                            // you will no longer send data on this connection.
                            // Then, break out of the processing loop.
                            await relayConnection.ShutdownAsync(cts.Token);
                            break;
                        }
   
                        // Write the line on the console.
                        Console.WriteLine(line);
   
                        // Write the line back to the client, prepended with "Echo:"
                        await writer.WriteLineAsync($"Echo: {line}");
                    }
                    catch (IOException)
                    {
                        // Catch an I/O exception. This likely occurred when
                        // the client disconnected.
                        Console.WriteLine("Client closed connection");
                        break;
                    }
                }
   
                Console.WriteLine("End session");
   
                // Close the connection.
                await relayConnection.CloseAsync(cts.Token);
            }
        }
    }
    ```

