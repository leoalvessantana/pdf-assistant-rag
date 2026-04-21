# Assistente Virtual para PDFs com RAG

Sistema de **Retrieval-Augmented Generation (RAG)** que transforma documentos PDF em uma base de conhecimento interativa. Faça perguntas em linguagem natural e obtenha respostas precisas com citação das fontes — sem necessidade de treinar nenhum modelo.

---

## Como Funciona

```
PDF(s) → Chunks → Embeddings → ChromaDB
                                   ↓
          Pergunta → Retriever (MMR) → Contexto → GPT-4o-mini → Resposta + Fontes
```

1. Os PDFs são divididos em trechos (chunks) e convertidos em vetores pelo modelo de embeddings
2. A pergunta do usuário é comparada aos vetores para encontrar os trechos mais relevantes
3. Esses trechos são injetados no prompt e enviados ao GPT-4o-mini, que gera a resposta final

---

## Diferenciais

| Recurso | Detalhe |
|---|---|
| **Arquitetura híbrida** | Embeddings locais (HuggingFace, custo zero) + raciocínio via GPT-4o-mini |
| **Busca com MMR** | *Maximum Marginal Relevance* garante diversidade nos trechos recuperados, evitando redundância |
| **Anti-alucinação** | Prompt customizado instrui o modelo a responder apenas com base no contexto fornecido |
| **Persistência inteligente** | O banco ChromaDB é criado uma vez e reutilizado nas execuções seguintes, sem reprocessar os PDFs |

---

## Tecnologias

- **Linguagem:** Python 3.10+
- **Orquestração:** LangChain
- **LLM:** GPT-4o-mini (OpenAI)
- **Embeddings:** all-MiniLM-L6-v2 (HuggingFace) — roda localmente
- **Banco Vetorial:** ChromaDB (persistência em disco)

---

## Instalação e Uso

### 1. Clone o repositório e crie o ambiente virtual

```bash
git clone https://github.com/seu-usuario/seu-repositorio.git
cd seu-repositorio

python -m venv .venv
source .venv/bin/activate       # Linux/Mac
.venv\Scripts\activate          # Windows

pip install -r requirements.txt
```

### 2. Configure a chave da OpenAI

Crie um arquivo `.env` na raiz do projeto:

```env
OPENAI_API_KEY=sua_chave_aqui
```

### 3. Adicione seus PDFs

Coloque os arquivos na pasta `files/` e atualize a lista no notebook:

```python
PDF_PATHS = ["files/seu_arquivo.pdf"]
```

### 4. Execute o notebook

Na **primeira execução**, o sistema indexa os PDFs e salva o banco vetorial em `arquivos/chat_retrieval_db/`. Nas execuções seguintes, o banco é carregado automaticamente.

---

## Exemplo de Uso

```python
pergunta = "O que é Hugging Face e como faço para acessá-lo?"
resposta = chat_chain.invoke({"query": pergunta})

print(resposta["result"])
# --- FONTES CONSULTADAS ---
# • LLM.pdf (chunk ID: 73)
# • apostila.pdf (chunk ID: 46)
```

---

## Estrutura do Projeto

```
├── files/                    # PDFs de entrada
├── arquivos/
│   └── chat_retrieval_db/    # Banco vetorial persistido (ChromaDB)
├── notebook.ipynb            # Pipeline principal
├── .env                      # Chave da OpenAI 
├── .gitignore
└── requirements.txt
```

