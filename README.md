# Projeto: Explorando os Recursos de IA Generativa com Copilot e OpenAI

## Introdução
Este projeto demonstra como utilizar recursos de IA generativa, especificamente o GitHub Copilot e a API da OpenAI, para criar um sistema de reconhecimento e análise de texto em imagens. Combinando o poder da programação assistida por IA e serviços de visão computacional, é possível automatizar a extração e interpretação de conteúdo textual de diversas fontes visuais.

## Estrutura do Repositório
- `/inputs`: Imagens utilizadas para os testes de reconhecimento
- `/outputs`: Resultados da extração e análise de texto
- `image_analyzer.py`: Script para processamento de imagens (assistido por Copilot)
- `openai_text_analysis.py`: Script para análise semântica dos textos extraídos
- `README.md`: Documentação do projeto

## Ferramentas e Tecnologias
- **GitHub Copilot**: Utilizado para assistir na escrita de código
- **OpenAI API**: Empregada para análise avançada do texto extraído
- **Python**: Linguagem principal do projeto
- **Azure Computer Vision** (alternativa): Para reconhecimento de texto em imagens
- **Bibliotecas auxiliares**: Requests, Pillow, JSON, entre outras

## Processo de Desenvolvimento

### 1. Configuração do Ambiente com GitHub Copilot
![Configuração do Copilot](placeholder-for-copilot-setup-screenshot.png)

O GitHub Copilot foi configurado no ambiente de desenvolvimento para auxiliar na escrita do código. Através de prompts bem estruturados, o Copilot conseguiu gerar funções completas para:
- Processamento de imagens
- Comunicação com APIs externas
- Formatação de resultados

**Exemplo de prompt utilizado com Copilot:**
```
# Write a function to extract text from an image using Azure Computer Vision API
# The function should accept an image path and return the extracted text
```

### 2. Extração de Texto de Imagens
Para a extração de texto, foram utilizadas duas abordagens:

#### Abordagem 1: Utilizando a API da OpenAI
A API da OpenAI (GPT-4 com capacidade de visão) foi utilizada para extrair e interpretar texto diretamente das imagens.

**Código gerado com assistência do Copilot:**
```python
import requests
import base64
import json
import os
from dotenv import load_dotenv

load_dotenv()  # Carrega as variáveis de ambiente do arquivo .env

def extract_text_with_openai(image_path):
    """
    Extrai texto de uma imagem usando OpenAI GPT-4 Vision
    
    Args:
        image_path: Caminho para a imagem
    
    Returns:
        Texto extraído da imagem
    """
    # Codificar a imagem em base64
    with open(image_path, "rb") as image_file:
        base64_image = base64.b64encode(image_file.read()).decode('utf-8')
    
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {os.getenv('OPENAI_API_KEY')}"
    }
    
    payload = {
        "model": "gpt-4-vision-preview",
        "messages": [
            {
                "role": "user",
                "content": [
                    {
                        "type": "text",
                        "text": "Extraia todo o texto visível nesta imagem. Apresente apenas o texto extraído sem comentários adicionais."
                    },
                    {
                        "type": "image_url",
                        "image_url": {
                            "url": f"data:image/jpeg;base64,{base64_image}"
                        }
                    }
                ]
            }
        ],
        "max_tokens": 300
    }
    
    response = requests.post("https://api.openai.com/v1/chat/completions", headers=headers, json=payload)
    response_data = response.json()
    
    # Extrair o texto da resposta
    extracted_text = response_data['choices'][0]['message']['content']
    return extracted_text
```

#### Abordagem 2: Azure Computer Vision (alternativa)
Como alternativa, também foi implementada uma solução usando Azure Computer Vision.

### 3. Análise Semântica do Texto Extraído
Após a extração, o texto é enviado para análise semântica usando a API da OpenAI:

```python
def analyze_text_with_openai(extracted_text, analysis_type="general"):
    """
    Analisa semanticamente o texto extraído usando OpenAI API
    
    Args:
        extracted_text: Texto extraído da imagem
        analysis_type: Tipo de análise (general, sentiment, key_points)
    
    Returns:
        Resultado da análise
    """
    prompts = {
        "general": f"Analise o seguinte texto extraído de uma imagem e forneça um resumo do conteúdo principal: {extracted_text}",
        "sentiment": f"Analise o sentimento do seguinte texto extraído de uma imagem. Classifique como positivo, negativo ou neutro e explique por quê: {extracted_text}",
        "key_points": f"Extraia os pontos-chave e informações mais importantes do seguinte texto: {extracted_text}"
    }
    
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {os.getenv('OPENAI_API_KEY')}"
    }
    
    payload = {
        "model": "gpt-4",
        "messages": [
            {
                "role": "user",
                "content": prompts[analysis_type]
            }
        ],
        "temperature": 0.3
    }
    
    response = requests.post("https://api.openai.com/v1/chat/completions", headers=headers, json=payload)
    response_data = response.json()
    
    analysis_result = response_data['choices'][0]['message']['content']
    return analysis_result
```

## Exemplos de Uso e Resultados

### Exemplo 1: Extração de Texto de um Documento
**Imagem Original:**
![Documento de exemplo](placeholder-for-document-image.png)

**Texto Extraído:**
```
CERTIFICADO
Certificamos que JOÃO SILVA participou do workshop 
"Inteligência Artificial Aplicada" realizado nos dias 
10 e 11 de fevereiro de 2023, com carga horária total 
de 16 horas.

São Paulo, 11 de fevereiro de 2023
```

**Análise da OpenAI:**
```
Este é um certificado de participação concedido a João Silva por sua presença 
no workshop "Inteligência Artificial Aplicada". O evento ocorreu em 10 e 11 
de fevereiro de 2023, com duração total de 16 horas, em São Paulo.
```

### Exemplo 2: Texto em Placa
**Imagem Original:**
![Placa de rua](placeholder-for-sign-image.png)

**Texto Extraído:**
```
PROIBIDO ESTACIONAR
Segunda a Sexta
das 7h às 19h
```

**Análise da OpenAI:**
```
A placa comunica uma restrição de estacionamento que se aplica nos dias úteis 
(segunda a sexta-feira) durante o horário comercial estendido, das 7 horas da 
manhã até as 19 horas (7h-19h).
```

## Insights e Aprendizados

### Vantagens da Utilização de IA Generativa no Projeto:

1. **Produtividade no Desenvolvimento:**
   - O GitHub Copilot reduziu significativamente o tempo de codificação
   - Sugestões de código reduziram a necessidade de consultas à documentação

2. **Capacidades de Extração de Texto:**
   - A API GPT-4 Vision demonstrou excelente capacidade de extração de texto, mesmo em imagens com:
     - Texto com rotação ou em perspectiva
     - Fontes estilizadas
     - Imagens com baixo contraste

3. **Análise Contextual:**
   - Diferente de OCRs tradicionais, a IA generativa:
     - Compreende o contexto do texto extraído
     - Consegue ignorar elementos irrelevantes
     - Formata automaticamente o texto de maneira lógica

4. **Adaptabilidade:**
   - O sistema funcionou bem em diferentes tipos de imagens sem necessidade de configurações específicas para cada caso

### Desafios Encontrados:

1. **Processamento de Imagens Complexas:**
   - Imagens com muitos elementos gráficos ainda apresentam desafios
   - Textos sobrepostos a fundos complexos podem ser interpretados incorretamente

2. **Limitações de Token:**
   - Imagens com muito texto podem exceder os limites de token da API

3. **Custo Computacional:**
   - O uso intensivo das APIs tem implicações de custo para projetos em larga escala

## Possibilidades Futuras

1. **Assistente de Acessibilidade:**
   - Desenvolvimento de uma ferramenta para pessoas com deficiência visual que descreve textos em ambientes

2. **Automação de Processamento Documental:**
   - Criação de fluxos automatizados para digitalização e categorização de documentos físicos

3. **Tradução Visual em Tempo Real:**
   - Aplicativo que traduz textos em imagens instantaneamente

4. **Análise de Conteúdo em Mídias Sociais:**
   - Ferramenta para detectar e analisar texto em imagens compartilhadas em plataformas sociais

5. **Integração com Outros Serviços:**
   - Combinação com APIs de geração de imagens para criar sistemas que podem tanto interpretar quanto gerar conteúdo visual

## Conclusão

Este projeto demonstra o potencial transformador da combinação de ferramentas de IA generativa como GitHub Copilot e OpenAI para o desenvolvimento de soluções de visão computacional. As capacidades demonstradas vão além da simples extração de texto, permitindo uma compreensão contextual mais profunda do conteúdo visual.

A experiência adquirida neste projeto revela que estamos apenas começando a explorar as possibilidades dessas tecnologias, e há um vasto campo para inovações que combinem visão computacional e processamento de linguagem natural através de modelos generativos avançados.
