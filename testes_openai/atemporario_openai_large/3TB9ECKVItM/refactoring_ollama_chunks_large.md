Sejam bem-vindos a mais uma aula do curso de linguagem de programação específica para IA. Hoje iremos explorar mais alguns conceitos de scheduling. O caso conceito de hoje será o unroll.

Ele, como vimos naquele exemplo didático com a geração de números de Fibonacci, pode ser utilizado para diminuir o número de iterações a custo do tamanho do corpo do loop. Então nós vamos aumentar o corpo do loop a fim de diminuir o número de iterações. Vimos que ele tem o efeito de como se executasse múltiplas iterações em o que seria uma só no loop transformado.

Ele pode ser útil para quando temos pixels, ou seja, para os criadores que manipulam dados incomuns. E um padrão específico que vamos ver frequentemente, que às vezes é útil utilizar o unroll, é quando temos mais de um canal e queremos sempre executar o assign desses canais em sequência. Então um atrás do outro sem uma criação de um novo loop.

Ele tem duas sintaxes: a primeira delas é só passando uma var para dentro desse unroll; e a outra, a var e um fator. Ele vai ser utilizado com bound ou com split. No caso aqui, quando queremos fazer um unroll, por exemplo, de uma var que não sejam esses canais, normalmente precisamos fazer um split dessa var.

E fazemos o split com o fator e depois aplicamos um unroll nessa var que vai estar ali bem interna no alinhamento do loop. Certo? Uma outra opção é você simplesmente fazer essa sintaxe aqui, unroll, passando var e depois o fator. A diferença é que você vai ficar escondido o split; certo? Ele vai esconder esse split.

Para o exemplo dos canais, o que vamos estar utilizando é esse bound aqui. Então nós vamos, na verdade, utilizar tanto o reorder como o bound para mover os canais para a parte mais interna do loop. O bound vai dizer para o relay que a minha variável do canal é limitada entre 0 a 3; certo? Então ali, RGB. Sempre vamos trabalhar em relay com o mínimo e o extent, que é a extensão daquela dimensão.

Então vamos passar para o bound a variável, o mínimo que vai ser 0 e o extent que vai ser 3; e aqui são 3 canais. E a partir disso, diferentemente da sintaxe com o split, sabemos os limites que esse loop vai percorrer. Sabemos quais os limites da minha variável. Então utilizamos o bound para passar essa informação para o unroll depois.

No caso onde não sabemos a dimensão do loop, que vai ser nos casos onde temos uma variável que não seja aquela dos canais, por exemplo, vamos ter que estar utilizando essa sintaxe com o splitting. E vamos verificar na prática a utilização do unroll. 

Eu tenho aqui um exemplo onde estou aplicando um efeito sepia em uma imagem; certo? Então temos um efeito de foto antiga sendo aplicado. Primeiramente mostrando um protótipo em Python para termos um guia da temporização, certo, do runtime dessa pipeline.

Eu vou rodar o protótipo aqui; e temos em Python a velocidade de 20; no caso ele está demorando 23 milissegundos; no caso 24 milissegundos para completar a execução da pipeline na média. No pior caso está em 46 milissegundos e no melhor caso 20 milissegundos.

A pipeline é simples: ela só envolve multiplicar os canais da imagem por diversos fatores; certo? Cada canal novo vai ter um conjunto de 3 fatores associados a ele, certo? Então cada novo canal da minha imagem, cada novo RGB é uma ponderação dos RGB anteriores, iniciais.

Após isso eu faço o merging desses canais, converto para a Uint8 a versão clipada dele; e depois eu faço o merging desses canais. Converto para a Uint8 a versão clipada dele; e depois eu faço o merging desses canais. Converto para a Uint8 a versão clipada dele, ou seja, mantido ali entre 0 e 55.

Eu tenho aqui também um protótipo implementado em C++ com OpenCV. Eu tenho aqui também um protótipo implementado em C++ com OpenCV; então é a nossa segunda referência de tempo. E nós esperamos um tempo um pouco menor de execução em relação ao Python.

Já obtemos aqui o resultado do OpenCV: está igual ao Python; e no caso temos aqui 0,3 ms que está na média, certo, em comparação ao Python, com seus 23 milissegundos. No melhor caso ele tem 0,19 milissegundos e no pior caso 4 milissegundos.

Certo? Então ele vai bem mais rápido que o Python. Vamos então ver se conseguimos chegar mais rápido com o RayLight. Eu tenho aqui a Pipeline implementada no RayLight com o Generator e com um parâmetro aqui para selecionar o Schedule customizado; certo?

E aí o que temos aqui é esse Mux; e aí no caso vamos poder observar uma das maiores utilidades do Amplifier, que é a eliminação do Select para fazer esse Assign dos canais. Certo? Então temos um Mux aqui e conseguimos converter essa multiplexação.

Vamos converter isso daqui para executar o Assign um depois do outro ali dentro do loop mais interno da minha Pipeline. Eu tenho aqui primeiro a utilização, certo, com o paralelismo apenas sendo aplicado no Y dessa imagem; então vamos verificar ainda sem o Unroll.

Certo? Uma aplicação mais simples de Skylog: ao executar isso aqui obtemos um código também bem mais rápido que o Python, mas mais lento que o OpenCV. Inclusive o melhor tempo dessa execução em RayLight é... Está assinando a média em relação ao OpenCV.

Certo? Nós podemos então agora verificar o StateWinFile: eu tenho já carregado aqui; eu só preciso recarregar essa página. Vamos fechar o Assembly e verificar aqui que temos esse Mux aqui dentro, internamente no... Temos aqui o Y sendo executado em paralelo.

E dentro do X, na parte mais interna, temos esse Mux aqui; certo? Então vamos remover esse Mux: como vimos nos slides, precisamos utilizar algumas diretivas junto do meu Unroll para poder ter o comportamento que esperamos. A primeira delas, como mencionado, é o Reorder.

Precisamos mover os canais para a parte mais interna do loop; certo? Precisamos garantir que eles estejam na parte mais interna do alinhamento de loops. Seguinte aí: para isso, utilizamos o Bound para estabelecer os limites da minha VAR, que é os canais.

E aí eu tenho o mínimo, que é 0; e a extensão dessa minha dimensão, que vai ser o 3; então vamos de 0 até 2: 0, 1 e 2. E seguinte a isso, executamos o Unroll. Eu posso remover isso aqui.Para que vejamos um efeito que pode ser observado, quando você estiver utilizando essas diretivas, certo? Então, ao rodar o caso 1, temos ainda uma pipeline um pouco mais lenta, inclusive. E ao verificar a atualização aqui do Statement File, vou aumentar um pouco. E aí nós temos o seguinte: são três versões aqui da minha execução, certo? Uma para cada um daqueles canais.

Então é como se o relator estivesse considerando o canal como o loop mais externo. Estamos executando agora com um computeroot para ver o que ele está fazendo por padrão com essa nossa pipeline. Eu vou recarregar o meu Statement File, o meu Statement File, e como esperado, o canal está aqui no loop mais externo.

Então, ao fazer o unroll, estabelecemos esse extent aqui com o bound, certo? E ao fazer o unroll sem o reorder, ele vai desenrolar essa computação em três loops diferentes. Então, três loops por toda a imagem, diferentes. Vamos então remover e passar a fazer o reorder junto com as duas outras diretivas.

Ok, temos um pouco mais rápido do que o nosso teste inicial com aqueles três loops, certo? Mas ainda não tão rápido quanto o paralelo e também não tão rápido quanto o OpenCV. Vamos então prosseguir com o scheduling dessa pipeline. Mas antes, passando para verificar a nova estrutura obtida no statement file.

Então nós temos aqui que temos o loop mais externo, image output pelo Y, depois pelo X. E os três canais sendo feito o assign em sequência, um após o outro. Iremos então passar para o caso 2, que é a aplicação do paralelismo após esse unroll, certo? Dessa forma, com o reorder e o bounding.

Certo? Mais certificado com a úmida bakery também pode substituir a opção kelnar, muito menos com a sua categoria não permitimos que novamente tenha de roads e Jewels em que é o EXTERNO douyor que já tem nghĩ. 0,50ms. Assim conosco termina seus testes.

Ahtará também um unbliness, a third step. Nós podemos prosseguir com o nosso scheduling, testando um splitting e executando o paralelismo a partir desse splitting do y. Então, ao invés de paralelizar a var y pura, vamos fazer um splitting nela antes de aplicar o paralelismo.

E novamente, como antes, estou utilizando o vector size obtido com a função naturalVectorSize do RayLight como uma guia para minha experimentação. Ele é só um valor que eu posso rapidamente experimentar vários fatores dele como uma guia, certo? No caso aqui, estou multiplicando ele por 8.

Então nós vamos executar aqui o caso 3. Ele é um pouco mais lento que a versão anterior. A gente pode experimentar também modificar esse vector size e ver se a gente obtém um tempo um pouco melhor. Ele está um pouco mais rápido, mas ainda assim não superou o nosso schedule 2.

Nós iremos então observar o schedule 4, onde a gente encadeia o splitting com a vectorização, certo? O paralelismo com a vectorização. Nós executamos o nosso unroll, fazemos o splitting do y e aplicamos o paralelismo na variável mais externa e fazemos o splitting do x e aplicamos a vectorização na variável mais interna, certo?

Normalmente a gente pode experimentar com diversos tipos de variáveis, mas eu acho que o que eu vou fazer é fazer um parâmetro de cada um dos parâmetros. Então eu vou usar os fatores desse número ou simplesmente ir variando manualmente os números, certo? Com isso, obtemos 0,1 milissegundos.

Nós podemos então rodar novamente o protótipo C++ com o OpenCV. Vamos a 1 milissegundo. E em comparação, nos temos aí metade do tempo na média com o blooming. O que você pode ver é que a receita é mais rápida. Afinal de contas, oito horas são bastante bem feitas para a Party e para bater uma entrada muito rápida.

Certo? E em comparação, nós temos aí metade do tempo na média com o Link. O que acontece se você tentar executar essa mesma operação sem o unroll, certo? Eu tenho o caso 5 aqui. Ok? Então, sem o unroll, a gente consegue chegar abaixo do OpenCV na média, certo?

Mas não tão rápido quanto fazendo o unroll. Inclusive, é também ali no range de metade do tempo em relação a esse schedule sem o unroll. A gente pode testar também a reordenação. Nesse caso, sim. E aí também é um pouco mais rápido, mas o melhor tempo está na mesma ordem ali do nosso schedule 4.

Aqui na média, no caso. Então, nós aplicamos o unroll para tanto possibilitar algumas otimizações do nosso código, como também eliminar o MUX dentro do loop mais interno para a nossa pipelining. Como leitura recomendada, eu deixo para vocês tanto a lição de scheduling do RayLight, como também, novamente, o ponto em que RayLight define ali definições relacionadas às funks, certo?

Que vai conter as definições do RayLight, as duas definições do RayLight para o unroll. Muito obrigado pela atenção