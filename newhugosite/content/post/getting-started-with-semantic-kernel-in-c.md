+++
date = 2024-07-05T19:57:48Z
description = ""
draft = true
slug = "getting-started-with-semantic-kernel-in-c"
title = "Create AI Bot with Semantic Kernel in C#"

+++


Semantic Kernel is a powerful tool that allows you to build AI agents capable of interacting with AI services and running your code. AI services such as Open AI, Azure AI, Ollama offline agent, among others.L

Whether you're a Python or C# developer, this guide will help you take your first steps with Semantic Kernel. Let's dive in!

## Installing the SDK

Before we begin, you'll need to install the necessary packages. For most scenarios, you'll typically only need the `Microsoft.SemanticKernel` NuGet package.

‌ `dotnet add package Microsoft.SemanticKernel`  bash

## Quickly Get Started with Notebooks

If you're a Python or C# developer, Semantic Kernel provides step-by-step guides through notebooks. These notebooks walk you through building AI agents using Semantic Kernel. Here's how to get started:

**Create a Kernel**:

* Import the necessary packages:

‌ `using Microsoft.SemanticKernel; using Microsoft.SemanticKernel.ChatCompletion; using Microsoft.SemanticKernel.Connectors.OpenAI;`  csharp

* ‌
* Create a kernel with Azure OpenAI chat completion:

‌ `var builder = Kernel.CreateBuilder().AddAzureOpenAIChatCompletion(modelId, endpoint, apiKey); var kernel = builder.Build();`  csharp

* ‌

**Add Plugins**:

* You can add custom plugins to your kernel. For example:

‌ `kernel.Plugins.AddFromType<LightsPlugin>("Lights");`  csharp

* ‌

**Enable Planning**:

* Configure prompt execution settings:

‌ `var openAIPromptExecutionSettings = new OpenAIPromptExecutionSettings { ToolCallBehavior = ToolCallBehavior.AutoInvokeKernelFunctions };`  csharp

* ‌

**Chat with the AI**:

* Create a chat history:

‌ `var history = new ChatHistory();`  csharp

* ‌
* Initiate a back-and-forth chat:

‌ `string? userInput; do { Console.Write("User > "); userInput = Console.ReadLine(); history.AddUserMessage(userInput); var result = await chatCompletionService.GetChatMessageContentAsync( history, executionSettings: openAIPromptExecutionSettings, kernel: kernel); Console.WriteLine("Assistant > " + result); history.AddMessage(result.Role, result.Content ?? string.Empty); } while (userInput is not null);`  csharp

* ‌

## Conclusion

In this guide, you learned how to quickly get started with Semantic Kernel by building a simple AI agent that can interact with an AI service and run your code. To explore more examples and build more complex AI agents, check out our in-depth samples.

Happy coding! 🚀

‌             ‌

*Published on rmauro.dev on May 14, 2024.*¹⁵

Source: Conversation with Copilot, 7/5/2024‌‌(1) How to quickly start with Semantic Kernel | Microsoft Learn. [https://learn.microsoft.com/en-us/semantic-kernel/get-started/quick-start-guide](https://learn.microsoft.com/en-us/semantic-kernel/get-started/quick-start-guide).‌‌(2) How to Get Started using Semantic Kernel .NET | Semantic Kernel. [https://devblogs.microsoft.com/semantic-kernel/how-to-get-started-using-semantic-kernel-net/](https://devblogs.microsoft.com/semantic-kernel/how-to-get-started-using-semantic-kernel-net/).‌‌(3) GitHub - microsoft/semantic-kernel: Integrate cutting-edge LLM .... [https://github.com/microsoft/semantic-kernel](https://github.com/microsoft/semantic-kernel).‌‌(4) semantic-kernel-docs/semantic-kernel/get-started/quick-start ... - GitHub. [https://github.com/MicrosoftDocs/semantic-kernel-docs/blob/main/semantic-kernel/get-started/quick-start-guide.md](https://github.com/MicrosoftDocs/semantic-kernel-docs/blob/main/semantic-kernel/get-started/quick-start-guide.md).‌‌(5) semantic-kernel/README.md at main · microsoft/semantic-kernel. [https://github.com/microsoft/semantic-kernel/blob/main/README.md](https://github.com/microsoft/semantic-kernel/blob/main/README.md).

