# Conecta Voluntário

Protótipo desenvolvido no CodeDay 2026 (tema: voluntariado), onde ficou em 2º lugar. O voluntário escreve com as próprias palavras como gostaria de ajudar, uma IA identifica a intenção e o sistema mostra a ONG compatível.

**Protótipo online:** https://codeday-project-2.onrender.com
**Imagem Docker:** https://hub.docker.com/r/otaconta/conecta-voluntario

> O protótipo está no plano gratuito do Render, então a primeira abertura após um período parado pode levar cerca de 1 minuto. É o comportamento normal desse plano.

## O problema

Quem quer ajudar muitas vezes não sabe por onde começar nem quais organizações da cidade precisam de ajuda. Do outro lado, as ONGs têm dificuldade de alcançar novos voluntários. O Conecta Voluntário aproxima os dois: a pessoa não precisa conhecer categorias ou filtros, basta dizer o que gostaria de fazer.

## Como funciona

1. O voluntário escreve livremente como quer ajudar (por exemplo: "Tenho ração para doar e gosto de animais").
2. A IA lê o texto e identifica uma categoria de atuação: `animais`, `pessoas com deficiencia` ou `combate ao cancer`. Se o texto mostra vontade de ajudar mas não diz para quem, a IA retorna `indefinido` e o sistema pergunta a qual causa a pessoa quer se dedicar. Textos sem relação com voluntariado retornam `fora do escopo`.
3. O sistema mostra a ONG compatível com essa categoria, com a descrição e as necessidades cadastradas.

Se a API de IA estiver indisponível (por exemplo, por limite de requisições), o sistema usa uma classificação local por palavras-chave, para que o protótipo continue funcionando.

## ONGs do protótipo

O protótipo usa ONGs reais de Santana do Livramento (RS), cadastradas de forma fixa no código e tratadas como **dados demonstrativos**, baseados em informações públicas. Não representam as necessidades atuais de cada instituição.

- APAE: Associação de Pais e Amigos dos Excepcionais
- ASPA: Associação Santanense de Proteção aos Animais
- LIGA: Liga Feminina de Combate ao Câncer de Sant'Ana do Livramento

## Tecnologias

- **Backend:** Java puro, usando o `HttpServer` e o `HttpClient` do JDK (sem Maven nem frameworks)
- **Inteligência artificial:** API Gemini (Interactions API, modelo `gemini-3.6-flash`), chamada diretamente pelo backend
- **Frontend:** HTML, CSS e JavaScript puros, servidos pelo próprio `Servidor.java`
- **Infraestrutura:** Docker, com deploy no Render

## Estrutura do projeto

```text
CodeDAY-project/
├── src/
│   ├── Servidor.java        # servidor HTTP: serve o frontend e expõe POST /classificar
│   ├── ClassificadorIA.java # comunicação com a API Gemini (com fallback local)
│   ├── Recomendador.java    # categoria → ONG
│   ├── ONG.java
│   └── ...
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js
└── Dockerfile
```

## Como executar

### Opção 1: Docker

```bash
docker pull otaconta/conecta-voluntario:latest
docker run -p 8080:8080 -e GEMINI_API_KEY=sua_chave otaconta/conecta-voluntario:latest
```

### Opção 2: Localmente (precisa do JDK 25, que é a mesma usada no Dockerfile)

1. Clone o repositório:

   ```bash
   git clone https://github.com/vanessa-lauz/CodeDAY-project.git
   cd CodeDAY-project
   ```

2. Configure a chave da API Gemini como variável de ambiente (nunca coloque a chave no código nem envie para o GitHub):

   ```bash
   # Linux/macOS
   export GEMINI_API_KEY=sua_chave

   # Windows (PowerShell)
   $env:GEMINI_API_KEY="sua_chave"
   ```

3. Compile e execute, a partir da pasta raiz do projeto:

   ```bash
   javac -encoding UTF-8 -d out src/*.java
   java -cp out Servidor
   ```

4. Acesse no navegador: http://localhost:8080

### API

`POST /classificar`

```json
{ "texto": "Tenho ração para doar e gosto de animais" }
```

Resposta (exemplo):

```json
{
  "categoria": "animais",
  "ong": "ASPA - Associação Santanense de Proteção aos Animais",
  "descricao": "Atuação voltada à proteção e ao cuidado de animais",
  "necessidades": ["ração", "produtos de limpeza"]
}
```

## Limitações e próximos passos

Como o prazo era curto, o protótipo faz algumas simplificações:

- O cruzamento é feito por uma única categoria por pedido, e não por vários critérios (como disponibilidade ou localização).
- As ONGs são fixas no código; não há cadastro de novas organizações.
- Os dados das ONGs são demonstrativos.
- A localização é fixa em Santana do Livramento, sem geolocalização.
- A API Gemini usada no plano gratuito tem limite de requisições por minuto.

Ideias de evolução:

- Cadastro de ONGs, que atualizariam as próprias necessidades, e de oportunidades de voluntariado.
- Cruzamento por mais de um critério (categoria, bairro).
- Geolocalização para encontrar organizações próximas.
- Mais de uma sugestão por pedido, ordenadas por compatibilidade.

## Equipe

Projeto desenvolvido em equipe de 3 pessoas no CodeDay 2026.

- Ana Gabriela Fagundes Carvalho: frontend em HTML, CSS e JavaScript
- Vanessa Lauz Rodrigues: backend em Java
- Wanda Ocanha da Costa: testes
