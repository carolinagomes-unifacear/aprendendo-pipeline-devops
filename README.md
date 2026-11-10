# Primeiros passos GitHub Acions -  DevOps: Construindo uma Esteira de CI/CD e DevSecOps

Bem-vindo à nossa oficina prática de DevOps! Hoje você vai aprender como automatizar a verificação de segurança e os testes de uma aplicação utilizando o **GitHub Codespaces** (ambiente de desenvolvimento em nuvem) e o **GitHub Actions** (nossa esteira de automação/CI-CD).

Nossa aplicação é uma Calculadora Interativa que muda de cor. Vamos garantir que nenhuma alteração quebrada ou insegura chegue ao usuário final!

--

## Antes de Começar: Preparando o seu Ambiente (Usando este respositório como Template)

Para realizar esta prática, você precisa gerar uma cópia deste repositório na sua conta do GitHub e abrir o seu próprio ambiente virtual. Siga os passos abaixo:

1. **Fazer o Fork (Criar sua Cópia):** 
   * No topo desta página do GitHub (no canto superior direito), clique no botão **Fork**.
   * Na tela seguinte, certifique-se de que a sua conta pessoal está selecionada e clique no botão verde **Create Fork**. Isso criará uma cópia idêntica deste projeto dentro do seu perfil.

2. **Abrir o Codespaces:**
   * Agora que você está na página do *seu* repositório clonado, clique no botão verde **<> Code**.
   * Acesse a aba **Codespaces** e clique no botão azul **Create codespace on main** (ou *master*).
   * Uma nova aba do navegador será aberta simulando o VS Code na nuvem. O ambiente levará cerca de 1 a 2 minutos carregando porque ele está instalando o Node.js, os navegadores e as ferramentas de segurança automaticamente em segundo plano para você!


---

## O Fluxo da nossa Esteira (Pipeline)

Toda vez que você enviar um código para o GitHub (`git push`), o GitHub Actions criará uma máquina virtual limpa e executará 6 passos:
1. **Checkout:** Baixa o seu código atualizado.
2. **Setup Node.js:** Prepara o ambiente de execução do sistema.
3. **Instalação:** Baixa as bibliotecas necessárias do projeto.
4. **🛡️ DevSecOps (`npm audit`):** Analisa se existem falhas de segurança conhecidas nas dependências.
5. **Instalação do Browser:** Prepara o navegador em modo oculto (headless).
6. **🚀 Testes Automatizados (Playwright):** Simula um usuário clicando na calculadora e testando as cores.

---

## 🛠️ Passo 1: Preparando o seu Ambiente (Fork e Codespaces)

Para não alterar o projeto original do professor, você criará a sua própria cópia.

1. No topo desta página do GitHub, clique no botão **Fork** e depois em **Create Fork**. Isso criará uma cópia idêntica deste repositório na sua conta.
2. No seu repositório clonado, clique no botão verde **Code**, acesse a aba **Codespaces** e clique em **Create codespace on main**.
3. Uma aba do VS Code será aberta diretamente no seu navegador. O ambiente levará cerca de 1 a 2 minutos para configurar todas as ferramentas necessárias automaticamente em segundo plano.

---

## ❌ Passo 2: O Cenário Vermelho (Simulando uma Falha de Lógica)

Vamos ver a esteira DevOps funcionando ao bloquear um código que quebra a aplicação.

1. Na barra lateral esquerda do seu Codespaces, abra o arquivo `index.html`.
2. Vá até a linha **125** do arquivo e altere o símbolo do botão de soma de `+` para `X`:
   ```html
   <!-- Mude de + para X -->
   <button class="operator" onclick="adicionar('X')">X</button>
   ```
3. Salve o arquivo (`Ctrl + S`).
4. Abra o terminal na parte inferior do Codespaces e envie essa alteração para o GitHub digitando os comandos abaixo:
   ```bash
   git add .
   git commit -m "feat: alterando layout do botao de soma"
   git push origin main
   ```
5. **Acompanhe a Esteira:** Volte para a página do seu repositório no site do GitHub e clique na aba **Actions**. Clique no pipeline que acabou de iniciar.
6. **O Resultado esperado:** O pipeline vai avançar, mas vai **ficar vermelho (Falhar)** no passo **6. Executar os Testes Automatizados**. O Playwright tentará somar `7 + 5`, mas como o botão agora digita um `X`, a conta vai falhar e a esteira impedirá a publicação desse código quebrado!

---

## 🛡️ Passo 3: O Cenário DevSecOps (Bloqueio por Falha de Segurança)

Mesmo que o código funcione, o DevOps moderno precisa garantir a segurança (DevSecOps). Vamos simular a instalação de uma biblioteca antiga que contém vulnerabilidades perigosas.

1. Primeiro, vamos consertar o botão no `index.html` para voltar a ser `+`:
   ```html
   <button class="operator" onclick="adicionar('+')">+</button>
   ```
2. Agora, no terminal do seu Codespaces, digite o comando abaixo para instalar uma versão antiga e intencionalmente vulnerável da biblioteca `lodash`:
   ```bash
   npm install lodash@4.17.4 --save-exact
   ```
3. Salve e envie essa alteração de segurança para o GitHub:
   ```bash
   git add .
   git commit -m "chore: adicionando biblioteca de utilitarios"
   git push origin main
   ```
4. **Acompanhe a Esteira:** Vá até a aba **Actions** no seu GitHub e abra a execução atual.
5. **O Resultado esperado:** Desta vez, o pipeline vai quebrar muito mais cedo! Ele vai **ficar vermelho (Falhar)** no passo **🛡️ 4. Teste de Segurança (Verificar Vulnerabilidades)**. O comando `npm audit` detectou que o projeto está vulnerável e barrou a esteira imediatamente, protegendo nossa aplicação antes mesmo de rodar os testes visuais.

---

## ✅ Passo 4: O Cenário Verde (Tudo Seguro e Validado)

Para finalizar a prática, vamos remover a ameaça de segurança e ver a esteira aprovar nosso projeto.

1. No terminal do seu Codespaces, remova a biblioteca vulnerável executando:
   ```bash
   npm uninstall lodash
   ```
2. Envie a correção final para o GitHub:
   ```bash
   git add .
   git commit -m "fix: removendo biblioteca vulneravel do projeto"
   git push origin main
   ```
3. **Acompanhe a Esteira:** Abra a aba **Actions** pela última vez.
4. **O Resultado esperado:** Parabéns! Todos os 6 passos ficarão com o **check verde (✅ Success)**. Seu código foi baixado, analisado volumetricamente contra falhas de segurança, testado funcionalmente no navegador e está 100% pronto e seguro para ir para o ar!
