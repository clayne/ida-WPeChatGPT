**English | [中文](./README.ZH_CN.md)**  

# WPeChatGPT
- **IDA plugin** based on the same model as ChatGPT, using the gpt-3.5-turbo model released by OpenAI, can help analysts quickly analyze binary files.

- **Features** currently supported by *WPeChatGPT* include:
    - Analyze the usage environment, intended purpose, and function of the function.
    - Rename variables of functions.
    - Attempt to restore the function with python3, this function is mainly for functions of smaller blocks (such as an XOR decryption function).
    - Look for vulnerabilities in the current function.
    - Try to use python to generate the corresponding EXP for the vulnerable function.
    - Utilize GPT **Automatically analyze binary files**, see section ***Auto-WPeGPT*** for details.
- The *WPeChatGPT* plugin uses OpenAI's **text-davinci-003** model trained on GPT.
   After *v2.0* use OpenAI's latest **gpt-3.5-turbo** model (The same as **ChatGPT**).

ChatGPT's analysis results **for reference only**, otherwise we analysts would be out of work on the spot. XD
## Update History
|Version|Date|Comment|
|----|----|----|
|1.0|2023-02-28|Based on Gepetto.|
|1.1|2023-03-02|1. Delete the function of analyzing encryption and decryption. <br>2. Increase the function of python restore function. <br>3. Modified some details.|
|1.2|2023-03-03|1. Added the function of finding binary vulnerabilities in functions. <br>2. Increase the function of trying to automatically generate the corresponding EXP. <br>3. Modified some details. <br>(The upload was not tested due to the OpenAI server lag)|
|2.0|2023-03-06|1. Complete the testing of *v1.2* version vulnerability related functions. <br>2. Switch to the latest **gpt-3.5-turbo** model released by OpenAI.|
|2.1|2023-03-07|Fix the timed out issue of OpenAI-API. (See section ***About OpenAI-API Error Reporting***)|
|2.3|2023-04-23|Add the **Auto-WPeGPT v0.1** to support automatic analysis of binary files.<br>(Package *anytree* needs to be added from this version, use *requirements.txt* or *pip install anytree*)|
|2.4|2023-11-10|1. Changed some display details.<br>2. Update **Auto-WPeGPT v0.2**.|
|2.5|2024-08-07|1. Add support for other models, you can set this using the *MODEL* variable. @tpsnt<br>2. Support for the new version of the python openai package. (Need to update your openai package)|
|2.6|2025-02-17|Add support for DeepSeek, you need to set the variable *PLUGIN_NAME* to WPeChat-DeepSeek and fill the API KEY into variable *model_api_key*.<br>(The default model is DeepSeek-V3. If you want to use the R1 model, modify variable **MODEL** = *'deepseek-reasoner'*.)|
## Install
1. Run the following command to install the required packages.
```
pip install -r ./requirements.txt
```
2. Modify the script `WPeChatGPT.py`, add your API key to the variable ***openai.api_key***, change the variable ***ZH_CN*** to False. (Default Chinese)
3. Copy the script file `WPeChatGPT.py` and the folder `Auto-WPeGPT_WPeace` to the plugins folder of IDA, and finally restart IDA to use it.

**`! NOTE`**: You need to set the **IDA environment** to **python3**, and you need to use the **latest OpenAI Python package** after WPeChatGPT *2.0* version.
## Usage
Supports using any of the **right click, menu bar or shortcut keys** in IDA.
- hot key:  
  `Function analysis = "Ctrl-Alt-G"`  
  `Rename function variables = "Ctrl-Alt-R"`  
  `Vulnerability finding = "Ctrl-Alt-E"`  

- Right click on the pseudocode window:

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/menuInPseudocode.png" width="788"/>

- Menu bar: Edit $\Rightarrow$ WPeChatGPT

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/menuInEdit.png" width="360"/>

## Example
How to use:

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/useExample.gif" width="790"/>

Function analysis effect display:

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/resultExample.gif" width="790"/>

Vulnerability finding effect display:

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/vulnExample.gif" width="790"/>

## Auto-WPeGPT
**Update History:**
|Version|Date|Comment|
|----|----|----|
|0.1|2023-04-23|Initial release.|
|0.2|2023-11-10|1. Improve the recognition of valid strings.<br>2. Improve the analysis of function call trees.<br>3. Add recognition for import functions.|

**How to use:** Find Auto-WPeGPT in the menu bar and click it. After the output is complete, you can find the analysis results in the corresponding folder (*"WPe_+IDB name"*).
- Menu bar: Edit $\Rightarrow$ WPeChatGPT $\Rightarrow$ Auto-WPeGPT

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/auto-wpegpt_menu.png" width="788"/>

The meaning of each file in the output folder:
```
GPT-Result.txt -> Auto-WPeGPT analysis results
funcTree.txt -> function call tree structure
mainFuncTree.txt -> main function tree structure
effectiveStrings.txt -> Suspicious strings in the binary
```

**Show results:** 

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/autogptExample.gif" width="788"/>

After testing, the v0.1 version has a better analysis effect on files with fewer functions. In case of binary files with a large number of functions, tokens will exceed the range. We will try to improve it in the next version.

## About OpenAI-API error reporting
&emsp;&emsp;From March 2, 2023, I often encounter API errors, and I thought it was a problem of server instability (because I have ups and downs here), but because there are too many feedbacks that I have encountered related errors, so I I first went to OpenAI to check the API Status and found that it was running well, so I found that it might not be the server problem I thought, so I searched and debugged related problems. The following is how I dealt with the OpenAI API connection problem:

&emsp;&emsp;First of all, the plugin has been running under the conditions of **Scientific Online**.
- Under the condition of scientific Internet access, if you find that the plug-in fails to connect to the API after many attempts, you need to check the urllib3 version of python (version 1.26 has a proxy problem).
    - You can use the following commands to perform a fallback fix for urllib3:
    ```
    pip uninstall urllib3
    pip install urllib3==1.25.11
    ```
- You can set forward or reverse proxies for the plugin:
    - Fill in the proxy address and port information into the ***proxy*** variable (forward-proxy):
    ```
    # Set your forward-proxy if necessary. (e.g. Clash = http://127.0.0.1:7890)
    proxy = ""
    ```
    - Fill in the reverse-proxy-url into the ***proxy_address*** variable (reverse-proxy):
    ```
    # Set reverse-proxy URL if you need. (e.g. Azure OpenAI)
    proxy_address = ""
    ```
## Contact me
If you encounter problems or have any questions when using the plugin, please leave a message or send me an email.
## Acknowledgment
The project is based on *Gepetto* and inspired by it, you can visit https://github.com/JusticeRage/Gepetto to learn about the original method.
