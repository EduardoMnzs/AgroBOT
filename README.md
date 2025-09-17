# Workflow n8n: Analisador de Doenças em Plantas com IA (Hugging Face + Gemini)

Este repositório contém um fluxo de trabalho (workflow) para a plataforma de automação n8n, projetado para analisar imagens de folhas de plantas e fornecer um diagnóstico detalhado usando Inteligência Artificial.

## Descrição

O workflow é acionado pelo envio de uma imagem para um endpoint de Webhook. A imagem é então processada por um modelo de classificação de imagens do Hugging Face, treinado para identificar doenças em plantas. O resultado da classificação é enviado para a API do Google Gemini, que gera um resumo explicativo e amigável sobre a doença, seus sintomas e possíveis ações.

## Funcionalidades

  - **Entrada via Webhook:** Recebe imagens de folhas de plantas através de uma requisição HTTP POST.
  - **Classificação com IA:** Utiliza o modelo `linkanjarad/mobilenet_v2_1.0_224-plant-disease-identification` do Hugging Face para identificar a doença mais provável.
  - **Resumo Detalhado:** Conecta-se à API do Google Gemini (`gemini-2.5-flash`) para gerar uma explicação completa em português.
  - **Resposta em JSON:** Retorna o texto gerado pelo Gemini como resposta da requisição.

## Pré-requisitos

Antes de importar e utilizar este workflow, certifique-se de que você possui:

1.  Uma instância do **n8n** (seja na nuvem ou auto-hospedada - Self-Hosted).
2.  Uma **API Key do Hugging Face** com permissões para usar a Inference API.
3.  Uma **API Key do Google AI Studio (Gemini)**.

## Como Importar o Workflow

1.  Com sua interface do n8n aberta, vá em **"Import"**.
2.  Selecione **"From File"**.
3.  Escolha o arquivo `AgroBOT.json` deste repositório e clique em **"Import"**.

O workflow aparecerá no seu canvas. Não se esqueça de salvá-lo.

## Configuração Pós-Importação

Após importar, você **precisa** configurar suas chaves de API para que o fluxo funcione.

### 1\. Credencial do Hugging Face

1.  Encontre o nó `HTTP Request` renomeado para algo como **"Analisar Imagem com Hugging Face"**.
2.  Nas configurações do nó, vá para **Authentication** -\> **Header Auth**.
3.  Clique em **"Create New"** para criar uma nova credencial.
      - **Name:** `Hugging Face API Key` (ou um nome de sua preferência).
      - **Header Name:** `Authorization`
      - **Header Value:** `Bearer hf_SUA_CHAVE_API_AQUI` (substitua `hf_...` pela sua chave do Hugging Face).
4.  Salve a credencial.

### 2\. Credencial da API Gemini

1.  Encontre o nó `HTTP Request` renomeado para **"Gerar Resumo com Gemini"**.
2.  Nas configurações, localize o campo **URL**.
3.  Na URL, substitua o texto `SUA_API_KEY_AQUI` pela sua chave de API do Google Gemini.
    ```
    https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-latest:generateContent?key=SUA_API_KEY_AQUI
    ```

## Como Usar o Workflow

1.  **Ative o workflow** no canto superior direito da tela do n8n.
2.  Copie a **URL de Produção (Production URL)** do nó de **Webhook**.
3.  Envie uma imagem para essa URL usando uma requisição `POST` com `form-data`.

### Exemplo usando cURL:

Abra um terminal, navegue até a pasta onde está sua imagem (ex: `folha_doente.jpg`) e execute o comando abaixo, substituindo a URL e o nome do arquivo:

```bash
curl -X POST \
-F "data=@folha_doente.jpg" \
"URL_DE_PRODUCAO_DO_SEU_WEBHOOK_AQUI"
```

A resposta do comando será um JSON contendo o resumo gerado pelo Gemini.
