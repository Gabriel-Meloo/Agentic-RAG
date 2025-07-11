# 🔎 LangGraph RAG Workflow — Document Retrieval & Answer Validation

Este projeto implementa um pipeline completo de RAG (Retrieval-Augmented Generation) utilizando a biblioteca [LangGraph](https://python.langchain.com/docs/langgraph), com validações automáticas usando LLMs para garantir que os documentos recuperados são relevantes e que as respostas geradas são úteis e baseadas no contexto.

## 📌 Objetivo

Criar um sistema de perguntas e respostas robusto que:

1. **Recupere documentos** de uma base vetorizada (Vectorstore)
2. **Filtre documentos irrelevantes** com um LLM-grader
3. **Gere respostas** baseadas no contexto dos documentos
4. **Valide as respostas** quanto à confiabilidade (sem alucinação) e utilidade

---

## 📁 Estrutura do Pipeline

O fluxo é implementado como um grafo com os seguintes nós:

Usuário ➜ route_question
├── websearch ─┐
│ ↓
└── retrieve ➜ grade_documents ──┐
│ ↓
└─> websearch → generate → grade_generation_v_documents_and_question
├── useful → END
├── not useful → websearch
└── not supported → generate

yaml
Copiar
Editar

---

## ⚙️ Tecnologias e Bibliotecas

- [LangChain](https://www.langchain.com/)
- [LangGraph](https://python.langchain.com/docs/langgraph)
- [OpenAI GPT Models](https://platform.openai.com/)
- Tipos do Python: `TypedDict`, `List`, etc.

---

## 📦 Requisitos

- Python 3.10+
- API Key da OpenAI
- Vectorstore configurado (Ex: FAISS, Pinecone, Chroma, etc.)
- Um modelo LLM para:
  - Gerar resposta (`rag_chain`)
  - Avaliar relevância (`retrieval_grader`)
  - Detectar alucinação (`hallucination_grader`)
  - Avaliar se a resposta responde à pergunta (`answer_grader`)

---

## 🧪 Como rodar

1. Instale os pacotes necessários:
```bash
pip install langchain langgraph openai tiktoken
Configure suas variáveis de ambiente:

bash
Copiar
Editar
export OPENAI_API_KEY="sua-chave-aqui"
Invoque o workflow:

python
Copiar
Editar
from langgraph.graph import END

# Inicialize o app
app = workflow.compile()

# Estado inicial
state = {
    "question": "How do agents use memory in LangChain?",
    "documents": [],
    "web_search": "No",
    "generation": ""
}

# Execute o grafo
result = app.invoke(state)
print(result)
🧠 Componentes Chave
retrieve(state)
Recupera documentos do vectorstore com base na pergunta.

grade_documents(state)
Filtra os documentos que não são relevantes usando um LLM com prompt de validação.

generate(state)
Gera uma resposta com base nos documentos relevantes (RAG).

grade_generation_v_documents_and_question(state)
Valida se a resposta está fundamentada nos documentos e se responde adequadamente à pergunta.

web_search(state)
Busca resultados adicionais na web, caso o vectorstore falhe.

route_question(state)
Decide se a busca deve começar pela web ou pelo vectorstore.

✅ Exemplo de uso do retrieval_grader
python
Copiar
Editar
result = retrieval_grader.invoke({
  "question": "agent memory",
  "document": "LangChain agents can store short-term context using memory tools."
})
print(result)  # {"score": "yes"}
🧩 Extensões possíveis
Integração com Pinecone ou ChromaDB como vectorstore

Uso de agentes com memória para manter o contexto entre rodadas

Logging e métricas de qualidade das respostas
