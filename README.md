# UNDEAD RISING: Sobreviva ao Apocalipse

![Java](https://img.shields.io/badge/Java-17+-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Swing](https://img.shields.io/badge/Swing-GUI-blue?style=for-the-badge&logo=oracle&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-3.8+-C71A36?style=for-the-badge&logo=apache-maven&logoColor=white)

<p align="center">
  <img src="<img width="584" height="499" alt="tela-inicial" src="https://github.com/user-attachments/assets/726cc870-c16e-4466-bbcb-d8b5c2f100d5" />
"/>
</p>

<p align="center">
  <img src="<img width="874" height="1010" alt="tela-personagem" src="https://github.com/user-attachments/assets/8eb5547a-f01c-4fe0-87ed-9fc4e350aa1b" />
"/>
</p>

<p align="center">
  <img src="<img width="986" height="696" alt="tela principal" src="https://github.com/user-attachments/assets/70ed120b-3b81-4158-bfab-6e09e05ca095" />
/>
</p>

**UNDEAD RISING** é um jogo de sobrevivência e estratégia em turnos desenvolvido em Java com a biblioteca Swing. Criado como um projeto da disciplina de Programação Orientada a Objetos, o jogo desafia o jogador a sobreviver por 7 dias em um mundo devastado por um apocalipse zumbi. Gerencie recursos, explore locais perigosos, combata hordas de mortos-vivos e lute pela sua vida até o confronto final.

## 👥 Desenvolvedores

| Nome | GitHub | LinkedIn |
| :--- | :---: | :---: |
| **Aline Mees** |
| **Arthur Bona** | 
| **João Vitor Monteiro** | 
| **Pedro Paulo Damasceno Muniz** | **[@Damasceno11](https://github.com/Damasceno11)** | **[LinkedIn](https://www.linkedin.com/in/pedro-damasceno-23b330150/)** |
## ✨ Features Principais

* **Linguagem de Design Apocalíptica**: Interface gráfica totalmente personalizada com Java Swing, usando uma paleta de cores em preto e vermelho para criar uma atmosfera de tensão e imersão.
* **Sobrevivência por Turnos**: Avance dia após dia, gerenciando três períodos (Manhã, Tarde e Noite) e decidindo quando explorar, usar itens ou descansar.
* **Combate Estratégico**: Sistema de combate por turnos com ações de ataque (rápido e forte), uso de itens, habilidades especiais e fuga. A iniciativa é baseada na agilidade dos combatentes.
* **Personagens com Habilidades Únicas**: Escolha entre diferentes sobreviventes, cada um com habilidades exclusivas que oferecem vantagens estratégicas distintas.
* **Progressão de Dificuldade**: A ameaça zumbi cresce a cada dia, com inimigos mais numerosos, fortes e de tipos variados.
* **Narrativa Imersiva**: Acompanhe o estado emocional do seu personagem e enfrente um confronto final com decisões morais que impactam o desfecho da sua jornada.

## 🧠 Arquitetura e Decisões de Design

O projeto foi estruturado seguindo as melhores práticas de POO para garantir um código limpo, coeso e extensível.

### O Padrão Model-View-Controller (MVC) em Ação

A arquitetura central é o padrão **MVC**, que separa as responsabilidades da aplicação de forma clara, como pode ser visto na estrutura de pacotes do projeto:

* **Model (`br.com.pedrodamasceno.model`)**: Contém toda a lógica de negócios, estado do jogo e regras de combate. As classes como `ModeloJogo`, `Personagem`, `Zumbi` e `Item` residem aqui. O Model é completamente independente da interface.
* **View (`br.com.pedrodamasceno.view`)**: Responsável por toda a apresentação visual e interação com o usuário. Classes como `TelaPrincipal` e `TelaCombate` constroem a UI com Swing e exibem os dados providos pelo Model.
* **Controller (`br.com.pedrodamasceno.controller`)**: O cérebro da aplicação. O `ControladorJogo` atua como intermediário, recebendo eventos da View (ex: clique no botão "Explorar") e orquestrando as atualizações no Model e na View.

<p align="center">
  <img src="https://i.imgur.com/8QG9e8t.png" alt="Project Structure"/>
</p>
### Desafios Técnicos e Soluções Implementadas

> #### 1. Desafio: Sincronização da UI em Tempo Real com Swing
> **Problema:** Diferente de frameworks modernos, o Swing não possui um sistema de *data binding* nativo. Como garantir que cada alteração no `ModeloJogo` (perda de vida, ganho de item) seja refletida instantaneamente na `TelaPrincipal` sem criar um acoplamento forte?
>
> **Solução:** Implementamos um padrão que chamamos de **"Observable Manual"**. O `ControladorJogo` orquestra o fluxo: após qualquer ação que modifique o Model, ele explicitamente comanda a View a se redesenhar através do método `atualizarInterface()`. Este método centraliza a lógica de "puxar" os dados mais recentes do Model e atualizar todos os componentes da UI.
>
> ```java
> // Em ControladorJogo.java
> public void explorar() {
>     // ... Lógica complexa de exploração que modifica o modelo ...
>     Map<String, Object> resultados = modelo.explorar();
>
>     // ... Processa os resultados e atualiza o estado do modelo ...
>     modelo.setMensagem("Você não encontrou nada útil.");
>
>     // Gatilho final: comanda a View a se redesenhar com os novos dados
>     telaPrincipal.atualizarInterface();
> }
>
> // Em TelaPrincipal.java
> public void atualizarInterface() {
>     // A View puxa os dados mais recentes do Model para atualizar seus componentes
>     lblSaude.setText("Saúde: " + modelo.getJogador().getSaude() + "/" + modelo.getJogador().getSaudeMaxima());
>     barraVida.setValue(modelo.getJogador().getSaude());
>     areaStatus.setText(modelo.getMensagem());
>     // ... atualiza todos os outros componentes ...
> }
> ```
> Este fluxo (`Ação -> Controller -> Model -> View`) garante previsibilidade e mantém as camadas desacopladas.

> #### 2. Desafio: Progressão de Dificuldade Dinâmica
> **Problema:** Um jogo de sobrevivência precisa se tornar progressivamente mais difícil para manter o jogador engajado. Como criar um sistema que aumentasse o desafio de forma balanceada a cada dia?
>
> **Solução:** Criamos um algoritmo de geração de zumbis no `ModeloJogo` que leva em consideração o `diaAtual` e o `nivelPerigo` do local. Nos primeiros dias, apenas zumbis comuns aparecem. Conforme os dias passam, novos tipos (Corredores, Tóxicos, Tanques) são introduzidos e a quantidade máxima de inimigos por encontro aumenta.
>
> ```java
> // Em ModeloJogo.java
> private List<Zumbi> gerarZumbisPorDificuldade(int dia, int nivelPerigoAtual) {
>     // ...
>     maxZumbis += (dia - 1) / 2; // Aumenta o número máximo de zumbis a cada 2 dias
>     // ...
>
>     // Chance geral de encontrar zumbis aumenta com o dia e perigo
>     if (random.nextInt(100) < chanceEncontroBase) {
>         int numZumbis = 1 + random.nextInt(maxZumbis);
>
>         for (int i = 0; i < numZumbis; i++) {
>             int tipoZumbiChance = random.nextInt(100);
>
>             if (dia == 1) { // Dia 1: Apenas Zumbi Comum
>                 zumbis.add(new ZumbiComum());
>             } else if (dia == 2) { // Dia 2: Introduz ZumbiCorredor
>                 zumbis.add(random.nextBoolean() ? new ZumbiComum() : new ZumbiCorredor());
>             } else if (dia >= 3) { // Dia 3+: Introduz ZumbiToxico, etc.
>                 // ... lógica mais complexa para sortear zumbis mais perigosos
>             }
>         }
>     }
>     return zumbis;
> }
> ```
> Isso cria uma curva de dificuldade natural e obriga o jogador a adaptar suas estratégias.

### 🏛️ Princípios de POO em Ação

* **Herança:** A classe `Personagem` serve como base para `Zumbi` e os diferentes tipos de `Sobrevivente`, permitindo o reuso de atributos e métodos comuns como `saude`, `atacar()` e `receberDano()`.
* **Polimorfismo:** As habilidades especiais dos personagens são um exemplo claro. Cada sobrevivente implementa sua habilidade de forma única, mas o sistema de combate as invoca através de uma interface comum, sem precisar saber qual personagem específico está agindo.
* **Encapsulamento:** Todos os atributos das classes de modelo são privados (`private`), e o acesso é controlado por meio de métodos `getters` e `setters`. Isso protege a integridade do estado do jogo e evita modificações inesperadas.
* **Abstração:** Classes como `Item` e `Local` abstraem conceitos complexos. Não precisamos saber os detalhes de como uma `ArmaDeFogo` funciona internamente, apenas que podemos `equipar()` e `atacar()` com ela.

## 🚀 Como Executar

Para rodar o projeto localmente, siga os passos abaixo.

**Pré-requisitos:**
* **JDK 17** ou superior.
* **Apache Maven** instalado e configurado.

**Passos:**

1.  Clone o repositório:
    ```bash
    git clone [https://github.com/Damasceno11/undead-rising.git](https://github.com/Damasceno11/undead-rising.git)
    ```
    2.  Navegue até o diretório do projeto e compile:
    ```bash
    cd undead-rising
    mvn clean package
    ```

3.  Execute o arquivo `.jar` gerado na pasta `target`:
    ```bash
    java -jar target/undead-rising-1.0.jar
    ```
    ## 📈 Melhorias Futuras

-   [ ] **Sistema de Save/Load**: Implementar a capacidade de salvar e carregar o progresso do jogo.
-   [ ] **Expansão de Conteúdo**: Adicionar novos tipos de zumbis, itens, habilidades e locais exploráveis.
-   [ ] **Eventos Aleatórios**: Introduzir eventos inesperados durante a exploração para aumentar a rejogabilidade.
-   [ ] **Áudio e Efeitos Sonoros**: Adicionar uma trilha sonora e efeitos para aumentar a imersão.

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE.md) para mais detalhes.
