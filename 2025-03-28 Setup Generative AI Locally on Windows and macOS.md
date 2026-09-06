# 2025-03-28 Setup Generative AI Locally on Windows and macOS

Migrated from Appflowy: Original link: https://appflowy.com/41518cd2-22c3-48b9-bd3e-9ffeac63d8d0/Setup-generative-AI-locally-on-Windows-and-macOS-bb216062-5cd1-4e5b-877c-d8703e8e93db

<!-- toc-gitlab:start mode=full -->
## Contents<br>
1. [Background](#background)
2. [Machine](#machine)
3. [Used apps](#used-apps)
4. [Setup Ollama](#setup-ollama)
5. [Setup Open WebUI](#setup-open-webui)
	1. [Install Python 3.11](#install-python-311)
	2. [Install Open WebUI](#install-open-webui)
	3. [Launch Open WebUI](#launch-open-webui)
6. [Tips](#tips)
<!-- toc-gitlab:end -->

## Background

If you want to use generative AI ("genAI"), typically you would visit [ChatGPT](https://chatgpt.com/)'s, [Gemini](https://gemini.google.com/app)'s, [DeepSeek](https://chat.deepseek.com/)'s, or [Qwen](https://chat.qwen.ai/)'s websites.

In doing so, your data is sent to genAI service providers. If your work involves sensitive data, you might be hesitant to send such data. Furthermore, those services might experience outages or downtime when you need them.

That's where local genAI comes to play. By doing so, all your data is processed directly on your machine, using its own computational capabilities. That means: 

1. ✅ **Maximum privacy and security**: Your data is not sent to genAI service providers. Furthermore, if there are data breaches to the genAI service providers, your data won't be impacted.
2. ✅ **Customization**: You can choose which genAI model best suits your needs. Furthermore, you can integrate your own documents to the genAI model for better relevance for your work.
3. ✅ **Offline functionality**: It can operate without internet access.
4. ✅ **Free integration cost**: You can integrate genAI with your application without paying for API access to genAI service providers.
5. ✅ **Works even on aging machines**: You don't need state-of-the art machine to set this up.

This article provides a guideline on setting up generative AI locally on Windows and macOS machines.

## Machine

The local genAI is set up in two aging machines: 

|                       | Windows                                                                                                                                               | macOS                                                                                                                                                           |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Operating system**  | Windows 10 Pro version 22H2                                                                                                                           | macOS Sequoia version 15.1                                                                                                                                      |
| **Year**              | 2017                                                                                                                                                  | 2020                                                                                                                                                            |
| **Age as of writing** | 8 years                                                                                                                                               | 5 years                                                                                                                                                         |
| **Model**             | Custom-built PC                                                                                                                                       | [MacBook Pro (13-inch, 2020, Four Thunderbolt 3 ports)](https://support.apple.com/en-us/111339)                                                                 |
| **CPU**               | [Intel Pentium G4560](https://www.intel.com/content/www/us/en/products/sku/97143/intel-pentium-processor-g4560-3m-cache-3-50-ghz/specifications.html) | [Intel Core i5-1038NG7](https://www.intel.com/content/www/us/en/products/sku/196594/intel-core-i51038ng7-processor-6m-cache-up-to-3-80-ghz/specifications.html) |
| **RAM**               | 16 GB                                                                                                                                                 | 16 GB                                                                                                                                                           |
| **GPU**               | [NVIDIA GeForce GTX 1050 Ti](https://www.nvidia.com/en-us/geforce/10-series/) (discrete)                                                              | Intel Iris Plus Graphics (integrated)                                                                                                                           |
| **VRAM**              | 4 GB                                                                                                                                                  | Unified memory                                                                                                                                                  |

## Used apps

1. Local genAI platform: [Ollama](https://ollama.com/) (v0.6.2 as of writing)
2. User interface: [Open WebUI](https://openwebui.com/) (v0.5.20 as of writing)

## Setup Ollama

[Ollama](https://ollama.com/) is a local genAI platform. With this, you can download [various genAI models](https://ollama.com/library) and run them locally.

| Step                                                                                                            | Windows                                                                                                          | macOS                                                                                                      |
| --------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| 1. Download Ollama from https://ollama.com/download                                                             | ![](attachments/2025-03-28-localgenai-4-1-w.png)                                                                 | ![](attachments/2025-03-28-localgenai-4-1-m.png)                                                           |
| 2. Install Ollama                                                                                               | ![](attachments/2025-03-28-localgenai-4-2-w.png)                                                                 | ![](attachments/2025-03-28-localgenai-4-2-m.png)                                                           |
| 3. Verify Ollama is installed and accessible                                                                    | Open Command Prompt, then use command:<br>`ollama --version`<br>![](attachments/2025-03-28-localgenai-4-3-w.png) | Open Terminal, then use command:<br>`ollama --version`<br>![](attachments/2025-03-28-localgenai-4-3-m.png) |
| 4. Download a genAI model. For this article, we'll use DeepSeek-R1[^1] (https://ollama.com/library/deepseek-r1) | Command:<br>`ollama pull deepseek-r1`<br>![](attachments/2025-03-28-localgenai-4-4-w.png)                        | Command:<br>`ollama pull deepseek-r1`<br>![](attachments/2025-03-28-localgenai-4-4-m.png)                  |
| 5. Verify the model is installed                                                                                | Command:<br>`ollama list`<br>![](attachments/2025-03-28-localgenai-4-5-w.png)                                    | Command:<br>`ollama list`<br>![](attachments/2025-03-28-localgenai-4-5-m.png)                              |
| 6. Run the model                                                                                                | Command:<br>`ollama run deepseek-r1`<br>![](attachments/2025-03-28-localgenai-4-6-w.png)                         | Command:<br>`ollama run deepseek-r1`<br>![](attachments/2025-03-28-localgenai-4-6-m.png)                   |
| 7. Exit the model                                                                                               | Command:<br>`/bye`<br>![](attachments/2025-03-28-localgenai-4-7-w.png)                                           | Command:<br>`/bye`<br>![](attachments/2025-03-28-localgenai-4-7-m.png)                                     |

Congratulations! With this step, you've actually managed to install and run DeepSeek-R1[^1] on Command Prompt (Windows) or Terminal (macOS).

## Setup Open WebUI

[Open WebUI](https://openwebui.com/) is a user interface for genAI models. With this, you can run your models on your browser, as well as integrate your own documents and perform web searches. Here's the complete list of [Open WebUI features](https://docs.openwebui.com/features/).

### Install Python 3.11

Open WebUI v0.5.20 (the latest as of writing) [requires Python 3.11](https://github.com/open-webui/open-webui?tab=readme-ov-file#installation-via-python-pip-), so we'll begin by installing Python 3.11.

| Step                               | Windows                                                                                                                                                                          | macOS                                                                                                           |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| 1. Download Python 3.11            | Download from https://www.python.org/downloads/windows/<br>![](attachments/2025-03-28-localgenai-5-1-1-w.png)                                                                    | Download from https://www.python.org/downloads/macos/<br>![](attachments/2025-03-28-localgenai-5-1-1-m.png)     |
| 2. Install Python 3.11             | Make sure to check "Add python.exe to PATH", then click "Install Now"<br>![](attachments/2025-03-28-localgenai-5-1-2-w.png)                                                      | Open the installer package, then follow through the steps<br>![](attachments/2025-03-28-localgenai-5-1-2-m.png) |
| 3. Verify Python 3.11 is installed | Command:<br>`python --version`: to check the default Python version, or<br>`py -0`: to check all installed Python versions<br>![](attachments/2025-03-28-localgenai-5-1-3-w.png) | Command:<br>`python3 --version`<br>![](attachments/2025-03-28-localgenai-5-1-3-m.png)                           |

### Install Open WebUI

The next step is to install Open WebUI itself inside [Python virtual environment](https://realpython.com/python-virtual-environments-a-primer/).

| Step                                                                                             | Windows                                                                                             | macOS                                                                                              |
| ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| 1. Create the virtual environment using Python 3.11. In this example, we'll name it `open-webui` | Command:<br>`py -3.11 -m venv open-webui`<br>![](attachments/2025-03-28-localgenai-5-2-1-w.png)     | Command:<br>`python3.11 -m venv open-webui`<br>![](attachments/2025-03-28-localgenai-5-2-1-m.png)  |
| 2. Activate the virtual environment                                                              | Command:<br>`open-webui\Scripts\activate`<br>![](attachments/2025-03-28-localgenai-5-2-2-w.png)     | Command:<br>`source open-webui/bin/activate`<br>![](attachments/2025-03-28-localgenai-5-2-2-m.png) |
| 3. Verify the Python version in the virtual environment                                          | Command:<br>`python --version`<br>![](attachments/2025-03-28-localgenai-5-2-3-w.png)                | Command:<br>`python3 --version`<br>![](attachments/2025-03-28-localgenai-5-2-3-m.png)              |
| 4. Upgrade pip                                                                                   | Command:<br>`py -m pip install --upgrade pip`<br>![](attachments/2025-03-28-localgenai-5-2-4-w.png) | Command:<br>`pip3 install --upgrade pip`<br>![](attachments/2025-03-28-localgenai-5-2-4-m.png)     |
| 5. Install Open WebUI                                                                            | Command:<br>`py -m pip install open-webui`<br>![](attachments/2025-03-28-localgenai-5-2-5-w.png)    | Command:<br>`pip3 install open-webui`<br>![](attachments/2025-03-28-localgenai-5-2-5-m.png)        |
| 6. Verify Open WebUI is installed                                                                | Command:<br>`py -m pip show open-webui`<br>![](attachments/2025-03-28-localgenai-5-2-6-w.png)       | Command:<br>`pip3 show open-webui`<br>![](attachments/2025-03-28-localgenai-5-2-6-m.png)           |
| 7. Deactivate the virtual environment                                                            | Command:<br>`deactivate`<br>![](attachments/2025-03-28-localgenai-5-2-7-w.png)                      | Command:<br>`deactivate`<br>![](attachments/2025-03-28-localgenai-5-2-7-m.png)                     |

### Launch Open WebUI

Now that Open WebUI has been installed, each time you want to launch Open WebUI, you can go directly to this section.

| Step                                                                                               | Windows                                                                                                                    | macOS                                                                                                                   |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| 1. Activate the virtual environment                                                                | Command:<br>`open-webui\Scripts\activate`                                                                                  | Command:<br>`source open-webui/bin/activate`                                                                            |
| 2. Launch Open WebUI                                                                               | Command:<br>`open-webui serve`<br>![](attachments/2025-03-28-localgenai-5-3-2-w.png)                                       | Command:<br>`open-webui serve`<br>![](attachments/2025-03-28-localgenai-5-3-2-m.png)                                    |
| 3. After a while, open the UI on your browser                                                      | Open: `http://localhost: 8080/auth`<br>![](attachments/2025-03-28-localgenai-5-3-3-w.png)                                  | Open: `http://localhost: 8080/auth`<br>![](attachments/2025-03-28-localgenai-5-3-3-m.png)                               |
| 4. For first use, create admin account. You can fill in anything because it will be stored locally | ![](attachments/2025-03-28-localgenai-5-3-4-w.png)                                                                         | ![](attachments/2025-03-28-localgenai-5-3-4-m.png)                                                                      |
| 5. You can now use genAI locally with Open WebUI                                                   | ![](attachments/2025-03-28-localgenai-5-3-5-w.png)                                                                         | ![](attachments/2025-03-28-localgenai-5-3-5-m.png)                                                                      |
| 6. After you finish using genAI locally, you can stop Open Web UI                                  | Command:<br>While on Command Prompt, hit `Ctrl+C` with your keyboard<br>![](attachments/2025-03-28-localgenai-5-3-6-w.png) | Command:<br>While on Terminal, hit `Control+C` with your keyboard<br>![](attachments/2025-03-28-localgenai-5-3-6-m.png) |
| 7. Deactivate the virtual environment                                                              | Command:<br>`deactivate`<br>![](attachments/2025-03-28-localgenai-5-3-7-w.png)                                             | Command:<br>`deactivate`<br>![](attachments/2025-03-28-localgenai-5-3-7-m.png)                                          |

Congratulations! With this step, you've actually managed to run a local generative AI on a familiar user interface on Windows or macOS.

## Tips

1. If you use another version of Windows and macOS, don't worry—the steps are mostly the same.
2. If your generative AI doesn't provide output or provides output very slowly, then the model might be too heavy for your machine. Switch to a lighter model. For example, [DeepSeek-R1](https://ollama.com/library/deepseek-r1)[^1] used in this article is a 4.7 GB model. If it's too heavy, consider switching to [Llama3.2](https://ollama.com/library/llama3.2), which is a 2.0 GB model.
3. Close heavy applications (like browsers with many tabs) to free up RAM and VRAM.

[^1]: All references to DeepSeek-R1 in this article is actually [DeepSeek-R1-Distill-Qwen-7B](https://github.com/deepseek-ai/DeepSeek-R1?tab=readme-ov-file#deepseek-r1-distill-models). It's not exactly the same as DeepSeek-R1. For simplicity, we'll treat it as the smaller version of DeepSeek-R1 that can run on consumer-grade hardware.

