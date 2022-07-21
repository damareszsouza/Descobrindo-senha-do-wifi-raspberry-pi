# Descobrindo-senha-do-wifi-raspberry-pi
Descrição da aula


Vídeo Descobrindo a senha do roteador
“aircrack-ng --help” apresenta aqui todas as opções e formatos do Aircrack-NG. 
Opcionais entre colchetes e os arquivos de entrada.

Exemplo…
Antes do exemplo, para que serve o Aircrack-NG?
Aircrack-NG pode ser utilizado para descobrir a senha de um ponto de acesso e para isso podem ser usados esses dois arquivos aqui “wordlist.txt”, depois da opção “-w” e o “fcapture-02.cap”. 
Este primeiro arquivo (“wordlist.txt”) remete à uma outra ferramenta que vamos ver e este aqui (“fcapture-02.cap”) aponta para outra ferramenta, que já vimos, mas que vamos conhecer uma nova opção que gera esse arquivo aqui.  
Qual é a lógica? Qual é o funcionamento?
Este arquivo aqui (“fcapture-02.cap”) possui pacotes que são capturados de uma determinada rede e este arquivo aqui (“wordlist.txt”), depois da opção “-w”, contém uma lista de possíveis senha para este ponto de acesso.
Estas possíveis senhas são processadas contra esse arquivo de pacotes para ver se ele encontra um MET que seria a senha do ponto de acesso.
Bom, vamos ao comando que gera as possíveis senhas (professor digita “man crunch” seguido de enter no terminal) com o Crunch. Este gera uma lista de palavras, essas palavras podem ser senhas, a partir de um conjunto de caracteres. 
E aí tem uma série de exemplos aqui de uso no Crunch com todo o comando Linux, com toda ferramenta Linux. A quantidade de opções é grande (professor está visualizando o retorno do comando “ man crunch” no terminal) que explica bem como ele funciona.
Por exemplo, “crunch 1 8 ” quer dizer que as palavras terão de 1 a 8 caracteres e essas palavras irão de “a” até 8 “z ” (zzzzzzzz). 
Vamos rodar isso aqui, “crunch 1 8” e vejam o número de convenções que são geradas (professor aponta para o retorno do comando onde indica a quantidade de retornos sendo o número 217.180.147.158) um número bastante significativo. Para verificar todas essas senhas o tempo seria grande (enquanto o professor fala, uma lista de possibilidades está sendo exibida no prompt linha a linha). Vejam que só para lista na tela aqui leva um bom tempo e vou interromper aqui.
O ideal para descobrir a senha seja encontrar algumas pistas da senha de acesso ao roteador e a partir destas pistas criar uma lista de palavras.
Eu posso, por exemplo, ficar vigiando um usuário que se conectam naquela rede, posso descobrir que a senha tem o nome da empresa ou nome do estabelecimento seguido do ano de fundação (professor digita no terminal um exemplo de senha “ficticio1999”). 
Então, por observação cheguei a esta conclusão, só não sei quais seriam os últimos 3 caracteres desta senha. Só sei que vi o usuário se conectando ao ponto de acesso onde ele digitou o nome da empresa e talvez o ano de fundação. Vi que começava com 1 por isso achei que poderia ser o ano de fundação mas não ouvi os outros 3 caracteres da sequência.
Então eu sei que essa é uma senha com 12 caracteres.
Dos 12 caracteres os 9 primeiros eu sei quais são. Não sei os últimos 3 mas sei que são números.
Então com o crunch posso fazer o seguinte, “crunch 12 12” caracteres, aqui é o mínimo de caracteres e o máximo, sei que tem 12 caracteres eu repito o mínimo e o máximo (professor digitou “crunch 12 12” no terminal). 
E aí vem os possíveis caracteres que serão tentados naqueles locais da senha que eu não conheço o conteúdo. São dígitos numéricos que estão naqueles 3 últimos caracteres “-t” do template e aí vem o que eu conheço da senha “-t ficticio1” e uso esse caractere aqui para sinalizar aqueles que devem ser descobertos (o comando ficou da seguinte forma “crunch 12 12 0123456789 -t ficticio1%%%”). 
Pronto!
Ele vai gerar mil combinações bem rapidinho.
Eu posso gravar isso em um arquivo de texto (professor complementa o comando anterior ficando assim “crunch 12 12 0123456789 -t ficticio1%%% > senhas.txt”).
“wc -l senhas.txt” mil linhas (professor recebeu como retorno do comando “wc -l senhas.txt” a quantidade de linhas do arquivo).
Voltando ao Aircrack que é o utilitário utilizado para descobrir a senha do ponto de acesso, o formato dele é “aircrack-ng -w", agora tenho um arquivo de senhas, o “ senhas.txt” e preciso do arquivo com os pacotes capturados.

Como é que eu capturo os pacotes?
Usando o nosso, já conhecido, Airodump (professor colou o comando “sudo airodump-ng --dssid 00:25:9c:85:31:c6 -w fcapture wlp3s0” no prompt de comando) está aqui o formato.

“airodump-ng” seguido do MAC do ponto de acesso, “-w” e o nome do arquivo que quero gerar, vamos mudar para “fcaptura” ao invés de “fcapture”, e a interface de rede wifi. 
Vou copiar esse comando pois lembram que antes de executar o airodump preciso matar os processos auxiliares que podem prejudicar.
“airmon-ng check kill”...
Pronto! 
Agora posso rodar aquele comando (o comando final ficou assim “sudo airodump-ng --dssid 00:25:9c:85:31:c6 -w fcaptura wlp3s0”).
Já coloquei duas estações nesta rede e agora vou colocar uma terceira estação.
Todos os pacotes que estão trafegando na rede estão sendo gravados no “fcaptura”.
Vamos ver o tamanho que ele está ficando (professor digitou o comando “ls -l fcaptura-01.cap”), 754300 e a cada novo “ls -l” ele vai aumentado pois está capturado todos os pacotes que estão trafegando na rede.
Vamos aguardar o arquivo encorpar mais um pouco.
Ok. Vou interromper.
Agora temos o arquivo “senhas.txt” e remos o “fcaptura-01.cap”.
Na verdade temos mais arquivos.
Ele cria 10 arquivos, mas o que utilizamos no Aircrack é o “.cap”.
Então vamos montar a linha de comando do Aircrack-NG para tentar descobrir a senha do ponto de acesso.
“sudo aircrack-ng -w senhas.txt fcaptura-01.cap” e enter.
Ele não encontrou a senha mas vamos fazer alguma coisa diferente. 
Eu sei que a senha deste ponto de acesso tem “curso” no início e depois tem o ano e o ano começa com “2”.
Então a senha é “curso2” depois “xxx” ficando assim “ curso2xxx”.
Vamos gerar esse arquivo, “crunch 9 9 0123456789 -t curso20%% > senhas2.txt”. 
Pronto!
Agora executar o Aircrack com o senhas2.txt “sudo aircrack-ng -w senhas2.txt fcaptura-01.cap”.
Olha, senha encontrada (o programa retornou várias informações sendo que uma delas é a senha descoberta e está informada da seguinte forma na lista de retornos “KEY FOUND! [curso2020]").
Vamos dar uma olhadinha no arquivo senhas2.txt para ver o que que ele comparou com o arquivo dos pacotes capturados (professor digitou "cat senhas2.txt" no prompt de comando para visualizar o conteúdo do arquivo sem a necessidade de abri-lo).
Todas essas strings foram comparadas até que ele teve um MET com “curso2020” (professor mostra inúmeras linhas que foram testadas até que a senha fosse descoberta). 
Vamos novamente (professor rodou novamente o comando e obteve o mesmo retorno).
Podemos fazer um “crunch 9 9 0123456789 -t curso%%%% > senhas3.txt” supondo que eu não sei os últimos 4 dígitos.
Agora temos 10 mil combinações aqui.
Já vai levar um tempo, já vai levar um tempo maior.
Agora foi gravado em “senhas3.txt ”.
Vamos ver a quantidade de processamento agora com menos informação (professor executa o comando “sudo aircrack-ng -w senhas3.txt fcaptura-01.cap”.
Vejam que ele descobre mas já demora um pouco mais demonstrando que está processando.
Bom é isso então.
O uso do Aircrack-ng para descobrir a senha de um ponto de acesso a partir de um arquivo com pacotes capturados daquela rede.
