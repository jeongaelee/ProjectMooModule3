# ProjectMoo Module3 - 실습 가이드

## Hands-on Lab 개요

본 실습 과정은 한국 마이크로소프트와 파트너의 기술 협력 프로그램 중 하나인 "Project Moo"의 세번째 실습 과정입니다. 본 실습에서는 Azure OpenAI를 Azure API Management와 연동하여 Token Rate Limiting, Load Balancing등 API 호출에 대한 제어 기능을 구현 해 봅니다.

## 사전 요구 사항 (필요 도구)

* Azure 구독: Azure OpenAI, Azure API Management등의 Azure 리소스를 배포하게 됩니다. 권한이 있으신지 확인해주세요.
* [Python 3.8 or later version](https://www.python.org/) 설치
* [Visual Studio Code 설치](https://code.visualstudio.com/)
* Visual Studio Code에 [Jupyter notebook extension](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter) 설치
* [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) 설치
* [Sign in to Azure with Azure CLI](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli-interactively)

## 사용 Azure 리소스 및 환경

* Azure OpenAI
* Azure API Management
* Azure Storage Account
* Azure AI Search

## 실습 순서

* [Step1. Azure API Management를 통하여 Azure OpenAI 액세스 하기](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step1.md)
* [Step2. Azure API Management로 Token rate limiting](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step2.md)
* [Step3. Azure API Management의 Backend Load Balancing](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step3.md)
<!-- * [Step4. HTTP Header로 Backend를 선택하여 Azure OpenAI On Your Data를 적용한 Azure OpenAI 서비스 호출](https://github.com/jeongaelee/ProjectMooModule3/blob/main/Step4.md)-->

## 참고 및 인용된 Repository

* [APIM ❤️ OpenAI - 🧪 Labs for the GenAI Gateway capabilities of Azure API Management](https://github.com/Azure-Samples/AI-Gateway)
