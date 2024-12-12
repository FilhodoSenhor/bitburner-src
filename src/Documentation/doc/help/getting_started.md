# Guia de introdução para programadores iniciantes

_Nota_: Os [Scripts](../basic/scripts.md) e estratégias neste guia não são necessariamente ideais ou abrangentes.
Este guia é personalizado para ajudar aqueles com conhecimento mínimo de programação a experimentar o Bitburner durante os estágios iniciais do jogo.

Se você está confuso ou sobrecarregado pelo jogo, especialmente os aspectos de codificação e script, este guia é perfeito para você!

## Introdução

O Bitburner é um RPG incremental com tema cyberpunk.
Você progredirá aumentando suas [Stats](../basic/stats.md), ganhando dinheiro e, com a prática, aprimorando suas habilidades de codificação no mundo real.
Após atingir certos critérios, você receberá convites de [Facções](../basic/factions.md) do jogo.
Juntar-se a [Facções](../basic/factions.md) e trabalhar para elas desbloqueará vários [Aumentos](../basic/augmentations.md),
que são comprados e "instalados", adicionando um bônus persistente a [estatísticas](../basic/stats.md) e outras habilidades. Trabalhar com Facções e instalar Aumentos é um passo básico para progredir no Bitburner.

O jogo tem um enredo aberto e minimalista que pode ser jogado de várias maneiras para atingir seus objetivos.
Como este guia foi escrito como uma introdução básica ao Bitburner, ele não exporá todo o escopo ou enredo disponível.

## Primeiros Passos

Vou assumir que você seguiu o tutorial introdutório quando começou o jogo.
Neste tutorial introdutório, você criou um [Script](../basic/scripts.md) chamado `n00dles.js` e o executou no servidor `n00dles`.
Agora, vamos matar este [Script](../basic/scripts.md). Há duas maneiras de fazer isso:

- Você pode ir ao Terminal e digitar: `$ kill n00dles.js`
- Você pode ir à página `Active Scripts` (Alt + s) e pressionar o botão `Kill Script` para `n00dles.js`.

Se você pulou o tutorial introdutório, ignore a parte acima.
Em vez disso, vá para a página `Hacknet Nodes` (Alt + h) e compre um [Hacknet Node](../basic/hacknet_nodes.md) para começar a gerar alguma renda passiva.

## Criando nosso primeiro script

Agora, criaremos um [script](../basic/hacking.md) genérico de [hacking](../basic/hacking.md) [script](../basic/scripts.md) que pode ser usado no início do jogo (ou durante todo o jogo, se você quiser).

Antes de escrevermos o [script](../basic/scripts.md), aqui estão algumas coisas com as quais você vai querer se familiarizar:

- `hacking`
- `security`
- `hack`
- `grow`
- `weaken`
- `brutessh`
- `nuke`

Para resumir brevemente: Cada [Servidor](../basic/servers.md) tem um nível de segurança que afeta o quão difícil é hackear.
Cada [Servidor](../basic/servers.md) também tem uma certa quantia de dinheiro, bem como uma quantia máxima de dinheiro que pode conter.
[Hacking](../basic/hacking.md) um [Servidor](../basic/servers.md) rouba uma porcentagem do dinheiro daquele [Servidor](../basic/servers.md).
A função `hack()` é usada para hackear um [Servidor](../basic/servers.md).
A função `grow()` é usada para aumentar a quantia de dinheiro disponível em um [Servidor](../basic/servers.md).
A função `weaken()` é usada para diminuir o nível de segurança de um [Servidor](../basic/servers.md).

Agora vamos prosseguir para realmente criar o [Script](../basic/scripts.md).
Vá para o seu computador de casa e crie um [Script](../basic/scripts.md) chamado `early-hack-template.js` indo para [Terminal](../basic/terminal.md) e inserindo os dois comandos a seguir:

    $ home
    $ nano early-hack-template.js

Isso o levará ao editor [Script](../basic/scripts.md), que você pode usar para codificar e criar [Scripts](../basic/scripts.md).

Insira o seguinte código no editor [Script](../basic/scripts.md):

    /** @param {NS} ns */
    export async function main(ns) {
        // Defines the "target server", which is the server
        // that we're going to hack. In this case, it's "n00dles"
        const target = "n00dles";

        // Defines how much money a server should have before we hack it
        // In this case, it is set to the maximum amount of money.
        const moneyThresh = ns.getServerMaxMoney(target);

        // Defines the minimum security level the target server can
        // have. If the target's security level is higher than this,
        // we'll weaken it before doing anything else
        const securityThresh = ns.getServerMinSecurityLevel(target);

        // If we have the BruteSSH.exe program, use it to open the SSH Port
        // on the target server
        if (ns.fileExists("BruteSSH.exe", "home")) {
            ns.brutessh(target);
        }

        // Get root access to target server
        ns.nuke(target);

        // Infinite loop that continously hacks/grows/weakens the target server
        while(true) {
            if (ns.getServerSecurityLevel(target) > securityThresh) {
                // If the server's security level is above our threshold, weaken it
                await ns.weaken(target);
            } else if (ns.getServerMoneyAvailable(target) < moneyThresh) {
                // If the server's money is less than our threshold, grow it
                await ns.grow(target);
            } else {
                // Otherwise, hack it
                await ns.hack(target);
            }
        }
    }

O [Script](../basic/scripts.md) acima contém comentários que documentam o que ele faz, mas vamos analisá-lo passo a passo de qualquer maneira.

    const target = "n00dles";

Este primeiro comando define uma string que contém nosso [Servidor](../basic/servers.md) alvo.
Esse é o [Servidor](../basic/servers.md) que vamos [hackear](../basic/hacking.md).
Por enquanto, está definido como `"n00dles"` porque esse é o único [Servidor](../basic/servers.md) com um nível de hacking necessário de `1`.
Se você quiser [hackear](../basic/hacking.md) um [Servidor](../basic/servers.md) diferente, simplesmente altere esta variável para ser o nome do host de outro [Servidor](../basic/servers.md).

    const moneyThresh = ns.getServerMaxMoney(target);

Este segundo comando define um valor numérico que representa a quantia mínima de dinheiro que deve estar disponível no [Servidor](../basic/servers.md) de destino para que nosso [Script](../basic/scripts.md) o [hackeie](../basic/hacking.md).
Se o dinheiro disponível no [Servidor](../basic/servers.md) de destino for menor que este valor, então nosso [Script](../basic/scripts.md) fará `grow()` no [Servidor](../basic/servers.md) em vez de [hackeá-lo](../basic/hacking.md).
Ele é definido como a quantia máxima de dinheiro que pode estar disponível no [Servidor](../basic/servers.md).
A função `getServerMaxMoney()` é usada para encontrar este valor:

    const securityThresh = ns.getServerMinSecurityLevel(target);

Este terceiro comando define um valor numérico que representa o nível mínimo de segurança que o [Servidor](../basic/servers.md) de destino pode ter.
Se o nível de segurança do [Servidor](../basic/servers.md) de destino for maior que este valor, então nosso [Script](../basic/scripts.md) irá `weaken()` o servidor antes de fazer qualquer outra coisa.

    if (ns.fileExists("BruteSSH.exe", "home")) {
        ns.brutessh(target);
    }

    ns.nuke(target);

Esta seção de código é usada para obter acesso root no [Servidor](../basic/servers.md) de destino.
Isso é necessário para [hackear](../basic/hacking.md).

    while (true) {
        if (ns.getServerSecurityLevel(target) > securityThresh) {
            // If the server's security level is above our threshold, weaken it
            await ns.weaken(target);
        } else if (ns.getServerMoneyAvailable(target) < moneyThresh) {
            // Otherwise, if the server's money is less than our threshold, grow it
            await ns.grow(target);
        } else {
            // Otherwise, hack it
            await ns.hack(target);
        }
    }

Esta é a seção principal que direciona nosso [Script](../basic/scripts.md).
Ela dita a lógica do [Script](../basic/scripts.md) e executa as operações de [hacking](../basic/hacking.md).
O `while (true)` cria um loop infinito que executará continuamente a lógica de [hacking](../basic/hacking.md) até que o [Script](../basic/scripts.md) seja encerrado.

A palavra-chave await é necessária para `hack()` / `grow()` / `weaken()` porque esses comandos levam tempo para serem executados, diferentemente dos outros.
Se você esquecer de aguardar esses comandos, receberá uma exceção dizendo que tentou fazer várias coisas ao mesmo tempo, porque seu código finalizará imediatamente a chamada de função sem esperar que a operação seja concluída.
Também é importante que await só pode ser usado em funções marcadas como `async` (note que `main()` é marcado como `async`).

## Executando nossos scripts

Agora queremos começar a executar nosso [script](../basic/hacking.md) [script](../basic/scripts.md) para que ele possa começar a nos render dinheiro e experiência.
Nosso computador doméstico tem apenas 8 GB de [RAM](../basic/ram.md), e o usaremos para outra coisa mais tarde.
Em vez disso, aproveitaremos a [RAM](../basic/ram.md) em outras máquinas.

Vá em `Terminal` e digite o seguinte comando:

    $ scan-analyze 2

Isso mostrará informações detalhadas sobre alguns [Servidores](../basic/servers.md) na rede.
A **rede é aleatória, então será diferente para cada pessoa**.
Aqui está o que o meu mostrou no momento em que fiz isso:

    [home ~]> scan-analyze 2
    ┕ home
      ┃   Root Access: YES, Required hacking skill: 1
      ┃   Number of open ports required to NUKE: 5
      ┃   RAM: 8.00GB
      ┣ n00dles
      ┃ ┃   Root Access: YES, Required hacking skill: 1
      ┃ ┃   Number of open ports required to NUKE: 0
      ┃ ┃   RAM: 4.00GB
      ┃ ┕ nectar-net
      ┃       Root Access: NO, Required hacking skill: 20
      ┃       Number of open ports required to NUKE: 0
      ┃       RAM: 16.00GB
      ┣ foodnstuff
      ┃ ┃   Root Access: NO, Required hacking skill: 1
      ┃ ┃   Number of open ports required to NUKE: 0
      ┃ ┃   RAM: 16.00GB
      ┃ ┕ zer0
      ┃       Root Access: NO, Required hacking skill: 75
      ┃       Number of open ports required to NUKE: 1
      ┃       RAM: 32.00GB
      ┣ sigma-cosmetics
      ┃ ┃   Root Access: NO, Required hacking skill: 5
      ┃ ┃   Number of open ports required to NUKE: 0
      ┃ ┃   RAM: 16.00GB
      ┃ ┕ max-hardware
      ┃       Root Access: NO, Required hacking skill: 80
      ┃       Number of open ports required to NUKE: 1
      ┃       RAM: 32.00GB
      ┣ joesguns
      ┃     Root Access: NO, Required hacking skill: 10
      ┃     Number of open ports required to NUKE: 0
      ┃     RAM: 16.00GB
      ┣ hong-fang-tea
      ┃     Root Access: NO, Required hacking skill: 30
      ┃     Number of open ports required to NUKE: 0
      ┃     RAM: 16.00GB
      ┣ harakiri-sushi
      ┃     Root Access: NO, Required hacking skill: 40
      ┃     Number of open ports required to NUKE: 0
      ┃     RAM: 16.00GB
      ┕ iron-gym
        ┃   Root Access: NO, Required hacking skill: 100
        ┃   Number of open ports required to NUKE: 1
        ┃   RAM: 32.00GB
        ┕ CSEC
              Root Access: NO, Required hacking skill: 55
              Number of open ports required to NUKE: 1
              RAM: 8.00GB

Tome nota dos seguintes servidores:

- `sigma-cosmetics`
- `joesguns`
- `nectar-net`
- `hong-fang-tea`
- `harakiri-sushi`

Todos esses servidores têm 16 GB de [RAM](../basic/ram.md).
Além disso, todos esses servidores não exigem nenhuma porta aberta para NUKE.
Em outras palavras, podemos obter acesso root a todos esses servidores e então executar [Scripts](../basic/scripts.md) neles.

Primeiro, vamos determinar quantos threads do nosso [Script](../basic/hacking.md) [Script](../basic/scripts.md) podemos executar.
(Veja a página em [scripts](../basic/scripts.md) para mais informações sobre multithreading.)

O [Script](../basic/scripts.md) que escrevemos usa 2,6 GB de [RAM](../basic/ram.md).
Você pode verificar isso usando o seguinte comando `Terminal`:

$ mem early-hack-template.js

Isso significa que podemos executar 6 threads em um servidor de 16 GB.
Agora, para executar nossos [Scripts](../basic/scripts.md) em todos esses servidores, temos que fazer o seguinte:

1. Use o comando `scp` para copiar nosso [Script](../basic/scripts.md) para cada servidor.
2. Use o comando `connect` para conectar a um servidor.
3. Use o comando `run` para executar o programa `NUKE.exe` e obter acesso root.
4. Use o comando `run` novamente para executar nosso [Script](../basic/scripts.md).
5. Repita as etapas 2 a 4 para cada servidor.

Esta é a sequência de comandos `Terminal` que usei para fazer isso:

    $ home
    $ scp early-hack-template.js n00dles
    $ scp early-hack-template.js sigma-cosmetics
    $ scp early-hack-template.js joesguns
    $ scp early-hack-template.js nectar-net
    $ scp early-hack-template.js hong-fang-tea
    $ scp early-hack-template.js harakiri-sushi
    $ connect n00dles
    $ run NUKE.exe
    $ run early-hack-template.js -t 1
    $ home
    $ connect sigma-cosmetics
    $ run NUKE.exe
    $ run early-hack-template.js -t 6
    $ home
    $ connect joesguns
    $ run NUKE.exe
    $ run early-hack-template.js -t 6
    $ home
    $ connect hong-fang-tea
    $ run NUKE.exe
    $ run early-hack-template.js -t 6
    $ home
    $ connect harakiri-sushi
    $ run NUKE.exe
    $ run early-hack-template.js -t 6
    $ home
    $ connect hong-fang-tea
    $ connect nectar-net
    $ run NUKE.exe
    $ run early-hack-template.js -t 6

Pressionar a tecla `Tab` no meio de um comando do Terminal tentará completar o comando automaticamente.
Por exemplo, se você digitar `scp ea` e então pressionar `Tab`, o resto do nome do [Script](../basic/scripts.md) deve ser preenchido automaticamente.
Isso funciona para a maioria dos comandos do jogo!

O comando `home` é usado para conectar ao computador doméstico. Ao executar nossos [Scripts](../basic/scripts.md) com o comando `run early-hack-template.js -t 6`, o `-t 6` especifica que o [Script](../basic/scripts.md) deve ser executado com 6 threads.

Observe que o `nectar-net` [Server](../basic/servers.md) não está na rede imediata do computador doméstico.
Isso significa que você não pode se conectar diretamente a ele de casa. Você terá que procurá-lo dentro da rede.
Os resultados do comando `scan-analyze 2` que executamos antes mostrarão onde ele está.
No meu caso, eu poderia conectar-me a ele indo de `hong-fang-tea` -> `nectar-net`.
No entanto, isso provavelmente será diferente para você.

Depois de executar todos esses comandos `Terminal`, nossos [Scripts](../basic/scripts.md) agora estão ativos e funcionando.
Eles renderão dinheiro e experiência de hacking ao longo do tempo.
Esses ganhos serão muito lentos agora, mas aumentarão quando nossa habilidade de hacking aumentar e começarmos a executar mais [Scripts](../basic/scripts.md).

## Aumentando o nível de hacking

Existem muitos [Servers](../basic/servers.md) além de `n00dles` que podem ser hackeados, mas eles têm níveis de hacking mais altos.
Portanto, devemos aumentar nosso nível de hacking.
Isso não só nos permitirá hackear mais [Servers](../basic/servers.md), mas também aumentará a eficácia do nosso [hacking](../basic/hacking.md) contra `n00dles`.

A maneira mais fácil de treinar seu nível de hacking é visitar a Rothman University.
Você pode fazer isso na aba `City` (Alt + w) no menu de navegação à esquerda.
A Rothman University deve ser o "U" próximo ao canto inferior direito.
Clique no "U" para ir para o local.

Quando você for para a Rothman University, deverá ver uma tela com várias opções.
Essas opções descrevem diferentes cursos que você pode fazer.
Você deve clicar no primeiro botão, que diz: `Study Computer Science (free)`.

Após clicar no botão, você começará a estudar e ganhar experiência de hacking.
Enquanto estiver fazendo isso, você não poderá interagir com nenhuma outra parte do jogo até clicar em `Stop taking course` ou `Do something else simultaneously`.

Agora, queremos um nível de hacking de 10.
Você precisa de aproximadamente 174 de experiência de hacking para atingir o nível 10.
Você pode verificar quanta experiência de hacking você tem indo para a aba `Stats` (Alt + c) no menu de navegação à esquerda.
Como estudar na Rothman University lhe dá 1 experiência por segundo, isso levará 174 segundos, ou aproximadamente 3 minutos.
Sinta-se à vontade para fazer algo enquanto isso!

## Editando nosso Script de Hacking

Agora que temos um nível de hacking de 10, podemos hackear o `joesguns` [Server](../basic/servers.md).
Este [Server](../basic/servers.md) será um pouco mais lucrativo que `n00dles`.
Portanto, queremos mudar nosso [Script](../basic/hacking.md) [hacking](../basic/scripts.md) para atingir `joesguns` em vez de `n00dles`.

Vá para `Terminal` e edite o [Script](../basic/hacking.md) [hacking](../basic/scripts.md) inserindo:

    $ home
    $ nano early-hack-template.js

No topo do [Script](../basic/scripts.md), altere a variável `target` para `"joesguns"`:

    const target = "joesguns";

Observe que isso **NÃO** afetará nenhuma instância do [Script](../basic/scripts.md) que já esteja em execução.
Isso afetará apenas instâncias do [Script](../basic/scripts.md) que forem executadas deste ponto em diante.

## Criando um novo script para comprar novos servidores

A seguir, criaremos um [Script](../basic/scripts.md) que compra automaticamente [Servers](../basic/servers.md) adicionais.
Esses [Servers](../basic/servers.md) serão usados ​​para executar muitos [Scripts](../basic/scripts.md).
Executar este [Script](../basic/scripts.md) inicialmente será muito caro, pois comprar um [Server](../basic/servers.md) custa dinheiro, mas valerá a pena no longo prazo.

Para criar este [Script](../basic/scripts.md), você deve se familiarizar com as seguintes funções:

- `purchaseServer()`
- `getPurchasedServerCost()`
- `getPurchasedServerLimit()`
- `getServerMoneyAvailable()`
- `scp()`
- `exec()`

Crie o [Script](../basic/scripts.md) indo em `Terminal` e digitando:

    $ home
    $ nano purchase-server-8gb.js

Cole o seguinte código no editor [Script](../basic/scripts.md):

    /** @param {NS} ns */
    export async function main(ns) {
        // How much RAM each purchased server will have. In this case, it'll
        // be 8GB.
        const ram = 8;

        // Iterator we'll use for our loop
        let i = 0;

        // Continuously try to purchase servers until we've reached the maximum
        // amount of servers
        while (i < ns.getPurchasedServerLimit()) {
            // Check if we have enough money to purchase a server
            if (ns.getServerMoneyAvailable("home") > ns.getPurchasedServerCost(ram)) {
                // If we have enough money, then:
                //  1. Purchase the server
                //  2. Copy our hacking script onto the newly-purchased server
                //  3. Run our hacking script on the newly-purchased server with 3 threads
                //  4. Increment our iterator to indicate that we've bought a new server
                let hostname = ns.purchaseServer("pserv-" + i, ram);
                ns.scp("early-hack-template.js", hostname);
                ns.exec("early-hack-template.js", hostname, 3);
                ++i;
            }
            //Make the script wait for a second before looping again.
            //Removing this line will cause an infinite loop and crash the game.
            await ns.sleep(1000);
        }
    }

Este código usa um loop while para comprar a quantidade máxima de [Servers](../basic/servers.md) usando a função `purchaseServer()`.
Cada um desses [Servers](../basic/servers.md) terá 8 GB de [RAM](../basic/ram.md), conforme definido na variável `ram`.
Observe que o [Script](../basic/scripts.md) usa o comando `getServerMoneyAvailable("home")` para obter a quantidade de dinheiro que você tem atualmente.
Isso é usado para verificar se você pode comprar um [Server](../basic/servers.md).

Sempre que o script compra um novo [Server](../basic/servers.md), ele usa a função `scp()` para copiar nosso [Script](../basic/scripts.md) para esse novo [Server](../basic/servers.md), e então ele usa a função `exec()` para executá-lo nesse [Server](../basic/servers.md).

Para executar esse [Script](../basic/scripts.md), vá para `Terminal` e digite:

    $ run purchase-server-8gb.js

Esta compra será executada continuamente até que tenha adquirido o número máximo de [Servers](../basic/servers.md).
Quando isso acontecer, significa que você tem um monte de novos [Servers](../basic/servers.md) que estão todos executando [hacking](../basic/hacking.md) [Scripts](../basic/scripts.md) contra o [Server](../basic/servers.md) `joesguns`!

A razão pela qual estamos usando tantos [Scripts](../basic/scripts.md) para hackear `joesguns` em vez de mirar em outros [Servers](../basic/servers.md) é porque é mais eficaz.
Tão cedo no jogo, não temos [RAM](../basic/ram.md) suficiente para hackear vários alvos com eficiência, e tentar fazer isso seria lento, pois estaríamos muito dispersos.
Você definitivamente deve fazer isso mais tarde, no entanto!

Observe que comprar um [Server](../basic/servers.md) é bastante caro, e comprar a quantidade máxima de [Servers](../basic/servers.md) ainda mais.
No momento em que este guia foi escrito, o [Script](../basic/scripts.md) acima requer \$11 milhões para concluir a compra de todos os 8 GB [Servers](../basic/servers.md).
Portanto, precisamos encontrar maneiras adicionais de ganhar dinheiro para acelerar o processo!
Elas são abordadas na próxima seção.

## Fontes Adicionais de Renda

Existem outras maneiras de ganhar dinheiro neste jogo além de [Scripts](../basic/scripts.md) e [hacking](../basic/hacking.md).

## Hacknet Nodes

Se você concluiu o tutorial introdutório, já foi apresentado a este método: [Hacknet Nodes](../basic/hacknet_nodes.md).
Assim que tiver dinheiro suficiente, você pode começar a atualizar seus [Hacknet Nodes](../basic/hacknet_nodes.md) para aumentar seu fluxo de renda passiva.
Isso é completamente opcional.
Como cada atualização de [Hacknet Node](../basic/hacknet_nodes.md) leva um certo tempo para "se pagar", pode não ser necessariamente do seu interesse usá-los.

No entanto, [Hacknet Nodes](../basic/hacknet_nodes.md) são uma boa fonte de renda no começo do jogo, embora sua eficácia diminua mais tarde.
Se você acabar comprando e atualizando [Hacknet Nodes](../basic/hacknet_nodes.md), eu sugeriria apenas atualizar seus níveis por enquanto.
Eu não me incomodaria com [RAM](../basic/ram.md) e atualizações de Core até mais tarde.

## Crime

A melhor fonte de renda agora é de [crimes](../basic/crimes.md).
Isso porque não só lhe dá uma grande quantia de dinheiro, mas também aumenta seu nível de hacking.
Para cometer [crimes](../basic/crimes.md), vá para a aba `City` (Alt + w).
Então, clique no link que diz `The Slums`.

Nas Slums, você pode tentar cometer uma variedade de [crimes](../basic/crimes.md), cada um dos quais dá certos tipos de experiência e dinheiro se for bem-sucedido.
Veja [crimes](../basic/crimes.md) para mais detalhes.

Você nem sempre tem sucesso quando tenta cometer um crime.
Nada de ruim acontece se você falhar em um [crime](../basic/crimes.md), mas você não ganhará dinheiro e a experiência adquirida será reduzida.
Aumentar suas estatísticas aumenta sua chance de cometer um [crime](../basic/crimes.md) com sucesso.

No momento, a melhor opção é o [crime](../basic/crimes.md) `Rob Store`.
Isso leva 60 segundos para tentar, dá \$400k se for bem-sucedido e dá experiência de hacking (o que é muito importante agora).

Como alternativa, você também pode usar o [crime](../basic/crimes.md) `Shoplift`.
Isso leva 2 segundos para tentar e dá \$15k se for bem-sucedido.
Este [crime](../basic/crimes.md) é um pouco mais fácil e mais lucrativo do que `Rob Store`, mas não dá experiência de hacking.

## Trabalhe para uma empresa

Se você não quer cometer [crimes](../basic/crimes.md), há outra opção - trabalhar para uma [empresa](../basic/companies.md).
Isso não será nem de longe tão lucrativo quanto [crimes](../basic/crimes.md), mas dará à [empresa](../basic/companies.md) [reputação](../basic/reputation.md).

Vá para a aba `City` no menu de navegação à esquerda e depois vá para `Joe's Guns`.
Em `Joe's Guns`, haverá uma opção que diz `Apply to be an Employee`.
Clique aqui para obter o emprego.
Então, uma nova opção aparecerá que diz simplesmente `Work`.
Clique aqui para começar a trabalhar.
Trabalhar na `Joe's Guns` rende \$110 por segundo e também concede alguma experiência para cada estatística, exceto hacking.

Trabalhar para uma [company](../basic/companies.md), como [crime](../basic/crimes.md), é completamente passivo.
Você pode escolher se concentrar no seu trabalho, fazer outra coisa simultaneamente ou alternar entre os dois.
Enquanto você se concentra no trabalho, não poderá fazer mais nada no jogo.
Se você fizer outra coisa simultaneamente, não ganhará [reputation](../basic/reputation.md) na mesma velocidade.
Você pode cancelar o trabalho a qualquer momento.

Quando seu hacking atingir o nível 75, você pode visitar `Carmichael Security` na cidade e conseguir um emprego de software lá.
Este emprego oferece um salário mais alto e também lhe dá experiência de hacking.

Há muitas outras empresas na aba `City` que oferecem mais pagamento e também mais recursos de jogabilidade.
Sinta-se à vontade para explorar!

## Depois de comprar seus novos servidores

Depois de ganhar um total de \$11 milhões, seu [Script](../basic/scripts.md) de compra automática de [Server](../basic/servers.md) deve terminar de rodar.
Isso liberará um pouco de [RAM](../basic/ram.md) no seu computador doméstico.
Não queremos que esta [RAM](../basic/ram.md) seja desperdiçada, então faremos uso dela.
Vá para `Terminal` e digite os seguintes comandos:

    $ home
    $ run early-hack-template.js -t 3

## Alcançando um nível de hacking de 50

Quando você alcança um nível de hacking de 50, duas novas partes importantes do jogo se abrem.

## Criando seu primeiro programa: BruteSSH.exe

No menu de navegação à esquerda, você notará uma aba `Create Program` (Alt + p) com um ícone de notificação vermelho.
Isso indica que há programas disponíveis para serem criados.
Vá para essa aba e você verá uma lista de todos os programas que você pode criar no momento.
Passar o mouse sobre um programa dará uma breve descrição de sua função.
Basta clicar em um programa para começar a criá-lo.

No momento, o programa que queremos criar é `BruteSSH.exe`.
Este programa é usado para abrir portas SSH em [Servers](../basic/servers.md).
Isso permitirá que você hackeie mais [Servers](../basic/servers.md), pois muitos [Servers](../basic/servers.md) no jogo exigem um certo número de portas abertas para que `NUKE.exe` obtenha acesso root.

Sinta-se à vontade para cancelar seu trabalho de criação de um programa a qualquer momento, pois seu progresso será salvo e poderá ser retomado mais tarde.
`BruteSSH.exe` leva cerca de 10 minutos para ser concluído.

## Opcional: Criar AutoLink.exe

Na página `Criar programas`, você notará outro programa que pode criar chamado `AutoLink.exe`.
Se não se importar em esperar mais 10-15 minutos, você deve prosseguir e criar este programa.
Isso torna muito menos tedioso conectar-se a outros [Servidores](../basic/servers.md), mas não é necessário para a progressão.

## Juntando-se à sua primeira facção: CyberSec

Pouco depois de atingir o nível 50 de hacking, você deve ter recebido uma mensagem que dizia isso:

    Message received from unknown sender:

    We've been watching you. Your skills are very impressive. But you're wasting your talents.
    If you join us, you can put your skills to good use and change the world for the better.
    If you join us, we can unlock your full potential.

    But first, you must pass our test. Find and install the backdoor on our server.

    -CyberSec

    This message was saved as csec-test.msg onto your home computer.

Se você não fez isso, ou se você acidentalmente fechou, tudo bem!
As mensagens são salvas no seu computador de casa.
Digite os seguintes comandos `Terminal` para visualizar a mensagem:

    $ home
    $ cat csec-test.msg

Esta mensagem faz parte da principal "quest-line" do jogo.
É uma mensagem da [faction](../basic/factions.md) `CyberSec` que está pedindo para você passar no teste.
Passar no teste é simples, você só precisa encontrar o [Server](../basic/servers.md), hackea-lo e instalar um backdoor pelo `Terminal`.
O [Server](../basic/servers.md) deles é chamado de `CSEC`.
Para fazer isso, usaremos o comando Terminal `scan-analyze`, assim como fizemos antes:

    $ home
    $ scan-analyze 2

Isso mostrará a rede para todos os [Servers](../basic/servers.md) que estão até 2 "nós" de distância do seu computador doméstico.
Lembre-se de que a rede é gerada aleatoriamente, então ela parecerá diferente para todos.
Aqui está a parte relevante dos meus resultados de `scan-analyze`:

    ┕ home
      ┃   Root Access: YES, Required hacking skill: 1
      ┃   Number of open ports required to NUKE: 5
      ┃   RAM: 8.00GB
      ┣ harakiri-sushi
      ┃     Root Access: NO, Required hacking skill: 40
      ┃     Number of open ports required to NUKE: 0
      ┃     RAM: 16.00GB
      ┕ iron-gym
        ┃   Root Access: NO, Required hacking skill: 100
        ┃   Number of open ports required to NUKE: 1
        ┃   RAM: 32.00GB
        ┕ CSEC
                  Root Access: NO, Required hacking skill: 55
              Number of open ports required to NUKE: 1
              RAM: 8.00GB

Isso me diz que posso chegar a `CSEC` passando por `iron-gym`:

    $ connect iron-gym
    $ connect CSEC

Se você criou o programa `AutoLink.exe` anteriormente, então há um método mais fácil de se conectar ao `CSEC`.
Você notará que nos resultados do `scan-analyze`, todos os nomes de host [Server](../basic/servers.md) estão em branco e sublinhados.
Você pode simplesmente clicar em um dos nomes de host [Server](../basic/servers.md) para se conectar a ele.
Então, simplesmente clique em `CSEC`!

Certifique-se de observar a habilidade de hacking necessária para o `CSEC` [Server](../basic/servers.md).
Este é um valor aleatório entre 51 e 60.
Embora você receba a mensagem do CSEC quando atingir 50 de hacking, você não pode realmente passar no teste deles até que seu hacking seja alto o suficiente para instalar um backdoor no [Server](../basic/servers.md) deles.

Após se conectar ao [Server](../basic/servers.md) `CSEC`, você pode fazer um backdoor nele.
Observe que este [Server](../basic/servers.md) requer uma porta aberta para obter acesso root.
Podemos abrir a porta SSH usando o programa `BruteSSH.exe` que criamos anteriormente.
Em `Terminal`:

    $ run BruteSSH.exe
    $ run NUKE.exe
    $ backdoor

Após instalar o backdoor com sucesso, você deve receber um convite de [faction](../basic/factions.md) do `CyberSec` logo em seguida.
Aceite.
Se você rejeitar o convite acidentalmente, tudo bem.
Basta ir para a aba `Factions` (Alt + f) e você deve ver uma opção que permite aceitar o convite.

Parabéns!
Você acabou de se juntar à sua primeira [faction](../basic/factions.md).
Não se preocupe em fazer nada com esta [faction](../basic/factions.md) ainda, podemos voltar a isso mais tarde.

## Usando servidores adicionais para hackear Joesguns

Depois de ter o programa `BruteSSH`, você poderá obter acesso root a vários [Servers](../basic/servers.md) adicionais.
Esses [Servers](../basic/servers.md) têm mais [RAM](../basic/ram.md) que você pode usar para executar [Scripts](../basic/scripts.md).
Usaremos a [RAM](../basic/ram.md) nesses [Servers](../basic/servers.md) para executar mais [Scripts](../basic/scripts.md) que têm como alvo `joesguns`.

## Copiando nossos Scripts

Os [Servers](../basic/servers.md) que usaremos para executar nossos [Scripts](../basic/scripts.md) são:

- `neo-net`
- `zer0`
- `max-hardware`
- `iron-gym`

Todos esses [Servers](../basic/servers.md) têm 32 GB de [RAM](../basic/ram.md).
Você pode usar o comando `Terminal` `scan-analyze 3` para ver por si mesmo.
Para copiar nossos [Scripts](../basic/hacking.md) [hacking](../basic/scripts.md) nesses [Servers](../basic/servers.md), vá para `Terminal` e execute:

    $ home
    $ scp early-hack-template.js neo-net
    $ scp early-hack-template.js zer0
    $ scp early-hack-template.js max-hardware
    $ scp early-hack-template.js iron-gym

Como cada um desses [Servers](../basic/servers.md) tem 32 GB de [RAM](../basic/ram.md), podemos executar nosso script [hacking](../basic/hacking.md) com 12 threads em cada [Server](../basic/servers.md).
Agora, você deve saber como se conectar a [Servers](../basic/servers.md).
Então, encontre e conecte-se a cada um dos [Servers](../basic/servers.md) acima usando o comando `scan-analyze 3` `Terminal`.
Em seguida, use o seguinte comando `Terminal` para executar nosso script [hacking](../basic/hacking.md) com 12 threads:

$ run early-hack-template.js -t 12

Lembre-se de que se você tiver o programa `AutoLink`, você pode simplesmente clicar no nome do host de um [Servidor](../basic/servers.md) após executar `scan-analyze` para se conectar a ele.

## Lucrando com scripts e ganhando reputação com CyberSec

Agora é hora de jogar o jogo da espera.
Levará algum tempo para seus [Scripts](../basic/scripts.md) começarem a ganhar dinheiro.
Lembre-se de que a maioria dos seus [Scripts](../basic/scripts.md) estão mirando em `joesguns`.
Levará um tempo para eles `grow()` e `weaken()` o [Server](../basic/servers.md) para os valores apropriados antes de começarem a [hackear](../basic/hacking.md) ele.
Uma vez que o fizerem, no entanto, os [Scripts](../basic/scripts.md) serão muito lucrativos.

Para referência, em cerca de duas horas após iniciar meu primeiro [Script](../basic/scripts.md), meus [Scripts](../basic/scripts.md) tiveram uma taxa de produção de \$20k por segundo e ganharam um total de \$70 milhões.
(Você pode ver essas estatísticas na aba `Active Scripts`).

Após outros 15 minutos, a taxa de produção aumentou para \$25k por segundo e os [Scripts](../basic/scripts.md) ganharam um adicional de \$55 milhões.

Seus resultados variam com base na rapidez com que você ganhou dinheiro com [crime](../basic/crimes.md)/[working](../basic/companies.md)/[hacknet nodes](../basic/hacknet_nodes.md), mas isso deve lhe dar uma boa indicação de quanto os [Scripts](../basic/scripts.md) podem ganhar.

Enquanto isso, vamos ganhar reputação com a facção `CyberSec` [faction](../basic/factions.md).
Vá para a aba `Factions` (Alt + f) no menu de navegação à esquerda e, de lá, selecione `CyberSec`.
No meio da página, deve haver um botão para `Hacking Contracts`.
Clique nele para começar a ganhar [reputation](../basic/reputation.md) para a facção `CyberSec` [faction](../basic/factions.md) (assim como alguma experiência de hacking).
Quanto maior seu nível de hacking, mais [reputation](../basic/reputation.md) você ganhará.
Note que enquanto você estiver trabalhando para uma [faction](../basic/factions.md), você pode escolher não interagir com o resto do jogo de nenhuma forma para ganhar [reputation](../basic/reputation.md) a toda velocidade.
Você também pode selecionar fazer outra coisa simultaneamente, ganhando [reputation](../basic/reputation.md) um pouco mais lentamente, até se concentrar novamente.
Você pode cancelar seu trabalho de [facção](../basic/factions.md) a qualquer momento sem nenhuma penalidade para sua [reputation](../basic/reputation.md) ganha até agora.

## Comprando Upgrades e Aumentos

Como mencionei antes, em 1-2 horas eu tinha ganhado mais de \$200 milhões.
Agora, é hora de gastar todo esse dinheiro em alguns upgrades persistentes para ajudar no progresso!

## Atualizando a RAM no computador doméstico

A coisa mais importante para atualizar agora é a [RAM](../basic/ram.md) no seu computador doméstico.
Isso permitirá que você execute mais [Scripts](../basic/scripts.md).

Para atualizar sua [RAM](../basic/ram.md), vá para a aba `City` e visite a empresa `Alpha Enterprises`.
Haverá um botão que diz `Upgrade 'home' RAM (8.00GB -> 16.00GB) - $1.010m`.
Clique nele para atualizar sua [RAM](../basic/ram.md).

Eu recomendo obter a [RAM](../basic/ram.md) do seu computador doméstico para **pelo menos** 128 GB.
Obter ainda mais seria melhor.

## Comprando seus primeiros aumentos

Depois de obter ~1000 [reputation](../basic/reputation.md) com a [faction](../basic/factions.md) `CyberSec`, você pode comprar seu primeiro [Augmentations](../basic/augmentations.md) deles.

Para fazer isso, vá para a aba `Factions` no menu de navegação à esquerda (Alt + f) e selecione `CyberSec`.
Há um botão perto do final que diz `Purchase Augmentations`.
Isso abrirá uma página que exibe todos os [Augmentations](../basic/augmentations.md) disponíveis na `CyberSec`.
Alguns deles podem estar bloqueados agora.
Para desbloqueá-los, você precisará ganhar mais [reputation](../basic/reputation.md) com `CyberSec`.

[Augmentations](../basic/augmentations.md) dão atualizações persistentes na forma de multiplicadores.
Eles não são muito poderosos no início do jogo porque os multiplicadores são pequenos.
No entanto, os efeitos dos [Augmentations](../basic/augmentations.md) se acumulam multiplicativamente **uns com os outros**, então, conforme você continua a instalar muitos [Augmentations](../basic/augmentations.md), seus efeitos aumentarão significativamente.

Por isso, eu recomendaria investir mais em atualizações de [RAM](../basic/ram.md) para seu computador doméstico em vez de [Augmentations](../basic/augmentations.md) no início.
Ter [RAM](../basic/ram.md) suficiente para executar muitos [Scripts](../basic/scripts.md) permitirá que você ganhe muito mais dinheiro, e então você pode voltar mais tarde e obter todos esses [Augmentations](../basic/augmentations.md).

Agora, sugiro comprar pelo menos o `Neurotrainer I` [Augmentations](../basic/augmentations.md) da `CyberSec`.
Se você tiver dinheiro sobrando, eu também sugeriria obter `BitWire` e vários níveis do `NeuroFlux Governor` (`NFG`) [Augmentations](../basic/augmentations.md).
Note que cada vez que você compra um [Augmentation](../basic/augmentations.md), **o preço de compra de outro aumenta em 90%**, então certifique-se de comprar o [Augmentation](../basic/augmentations.md) mais caro primeiro.
Não se preocupe, uma vez que você escolher instalar [Augmentations](../basic/augmentations.md), seus preços serão redefinidos para seus valores originais.

## Próximos passos

Este é o fim da parte de passo a passo deste guia!
Você deve continuar explorando o que o jogo tem a oferecer.
Há alguns recursos que não são abordados ou mencionados neste guia, e ainda mais que são desbloqueados conforme você continua jogando!

Além disso, confira a documentação da API para ver o que ela tem a oferecer.
Escrever [Scripts](../basic/scripts.md) para executar e automatizar várias tarefas é de onde vem a maior parte da diversão do jogo (na minha opinião)!

A seguir estão algumas coisas que você pode querer considerar fazer em um futuro próximo.

## Instalando Aumentos (e Redefinindo)

Se você comprou algum [Augmentations](../basic/augmentations.md), você precisará instalá-lo antes de realmente ganhar seus efeitos.
Instalar [Augmentations](../basic/augmentations.md) é a mecânica de "soft-reset" ou "prestige" do jogo.

Para instalar seus [Augmentations](../basic/augmentations.md), vá para a aba `Augmentations` (Alt + a) no menu de navegação à esquerda.
Você verá uma lista de todos os [Augmentations](../basic/augmentations.md) que você comprou.
Abaixo disso, você verá um botão que diz `Install Augmentations`.
Esteja avisado, depois de clicar aqui não há como desfazer (a menos que você carregue um salvamento anterior).

## Automatizando o Processo de Inicialização do Script

Sempre que você instala [Augmentations](../basic/augmentations.md), todos os seus [Scripts](../basic/scripts.md) são encerrados e você terá que executá-los novamente.
Fazer isso toda vez que você instala [Augmentations](../basic/augmentations.md) seria muito tedioso e irritante, então você deve escrever um [Script](../basic/scripts.md) para automatizar o processo.
Aqui está um exemplo simples para um [Script](../basic/scripts.md) de inicialização.
Sinta-se à vontade para ajustá-lo ao seu gosto.

    /** @param {NS} ns */
    export async function main(ns) {
        // Array of all servers that don't need any ports opened
        // to gain root access. These have 16 GB of RAM
        const servers0Port = ["sigma-cosmetics",
                            "joesguns",
                            "nectar-net",
                            "hong-fang-tea",
                            "harakiri-sushi"];

        // Array of all servers that only need 1 port opened
        // to gain root access. These have 32 GB of RAM
        const servers1Port = ["neo-net",
                            "zer0",
                            "max-hardware",
                            "iron-gym"];

        // Copy our scripts onto each server that requires 0 ports
        // to gain root access. Then use nuke() to gain admin access and
        // run the scripts.
        for (let i = 0; i < servers0Port.length; ++i) {
            const serv = servers0Port[i];

            ns.scp("early-hack-template.js", serv);
            ns.nuke(serv);
            ns.exec("early-hack-template.js", serv, 6);
        }

        // Wait until we acquire the "BruteSSH.exe" program
        while (!ns.fileExists("BruteSSH.exe")) {
            await ns.sleep(60000);
        }

        // Copy our scripts onto each server that requires 1 port
        // to gain root access. Then use brutessh() and nuke()
        // to gain admin access and run the scripts.
        for (let i = 0; i < servers1Port.length; ++i) {
            const serv = servers1Port[i];

            ns.scp("early-hack-template.js", serv);
            ns.brutessh(serv);
            ns.nuke(serv);
            ns.exec("early-hack-template.js", serv, 12);
        }
    }

## Dicas aleatórias

- No início do jogo, é melhor gastar seu dinheiro atualizando [RAM](../basic/ram.md) e comprando novos [Serves](../basic/servers.md) em vez de gastá-lo em [Augmentations](../basic/augmentations.md)
- Quanto mais dinheiro disponível em um [Server](../basic/servers.md), mais eficazes serão as funções `hack()` e `grow()`.
Isso ocorre porque ambas as funções usam porcentagens em vez de valores fixos.
`hack()` rouba uma porcentagem do dinheiro total disponível de um [Server](../basic/servers.md) e `grow()` aumenta o dinheiro de um [Server](../basic/servers.md) em X%.
- Há um limite para a quantidade de dinheiro que pode existir em um [Server](../basic/servers.md).
Este valor é diferente para cada [Server](../basic/servers.md).
A função `getServerMaxMoney()` dirá a você este valor máximo.
- Nesta fase do jogo, suas estatísticas de combate (força, defesa, etc.) não são tão úteis quanto suas estatísticas de hacking.
Não invista muito tempo ou dinheiro para ganhar exp de estatísticas de combate.
- Como regra geral, seu alvo de hacking deve ser o [Server](../basic/servers.md) com o maior dinheiro máximo que o nível de hacking necessário esteja abaixo de 1/2 do seu nível de hacking.
