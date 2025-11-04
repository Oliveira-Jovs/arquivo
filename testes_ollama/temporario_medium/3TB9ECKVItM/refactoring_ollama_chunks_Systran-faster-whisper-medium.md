Seja bem-vindo a mais uma aula do curso de linguagem de programação específica para IA. Hoje iremos explorar mais alguns conceitos de scheduling. O caso do conceito de hoje será o Unroll.

Ele, como vimos no exemplo com a geração de números de Fibonacci, pode ser utilizado para diminuir o número de interações a custo do tamanho do corpo do loop, certo? Então nós vamos aumentar o corpo do loop a fim de diminuir o número de interações. Aí vimos que ele tem o efeito de como se executasse múltiplas interações em o que seria um a só no loop transformado.

Ele pode ser útil para quando temos pixels que manipulam dados incomunes e um padrão específico que vamos ver frequentemente que às vezes é útil utilizar o Unroll é quando temos mais de um canal e quer se pre-executar o assign desses canais, certo? Em sequência, então um atrás do outro sem uma criação de um novo loop. Ele tem duas sintaxes, a primeira delas é só passando um AVAR para dentro desse Unroll e a outra AVAR em um fator.

Ele vai ser utilizado com Bound ou com Split. No caso aqui, quando queremos fazer um Unroll, por exemplo, de um AVAR que não sejam os canais, normalmente precisamos fazer um Split dessa AVAR, certo? E fazemos o Split com o fator e depois aplicamos um Unroll nessa AVAR que vai estar ali bem interna no alinhamento do loop, certo?

Uma outra opção é você fazer essa sintaxe aqui, Unroll, passando o AVAR e depois o fator. A diferença é que você vai ficar escondido o Split, certo? Ele vai esconder esse Split.

Para o exemplo dos canais, o que vamos estar utilizando é esse Bound aqui. Então, nós vamos, na verdade, utilizar tanto o Reorder como o Bound para mover os canais para a parte mais interna do loop, certo? O Bound, ele vai dizer para o Relight que a minha variável do canal é limitada entre 0 a 3, certo? Então ali RGB. Sempre que eu ir trabalhar em Relight com o mínimo e o extente, que é a extensão daquela dimensão.

Então vamos passar para o Bound a variável, o mínimo que vai ser 0 e o extente que vai ser 3, que são 3 canais. E a partir disso, diferentemente da sintaxe com o Split, sabemos os limites que esse loop vai percorrer, sabemos qual é os limites da minha variável. Então utilizamos o Bound para passar essa informação para o Unroll depois.

No caso onde não sabemos a dimensão do loop, que vai ser nos casos onde temos uma variável que não seja aquela dos canais, por exemplo, vamos estar utilizando essa sintaxe com o Splitting. Iremos agora verificar na prática a utilização do Unroll.

Eu tenho aqui um exemplo onde estou aplicando um efeito sepia em uma imagem, certo? Então temos um efeito de falta antiga sendo aplicado. Primeiramente mostrando um protótipo de Python para termos um guia da temporização, certo? Do runtime dessa pipeline.

Eu vou rodar o protótipo aqui. E temos em Python a velocidade ali de 20, no caso está demorando 23 milissegundos, no caso 24 milissegundos, para completar a execução da pipeline na média. No pior caso, está em 46 milissegundos e no melhor caso, 20 milissegundos.

A pipeline é simples, ela só envolve multiplicar os canais da imagem por diversos fatores, certo? Cada canal novo ele vai ter um conjunto de três fatores associados a ele, certo? Então cada novo canal da imagem, cada novo RGB, é uma ponderação dos RGB anteriores, iniciais.

Após isso faço o merging desses canais, com o vésito para a Uinti8 a versão clipada dele, ou seja, mantida ali entre 0 e 55. Eu tenho aqui também um protótipo implementado em C++ com OpenCV, então é a nossa segunda referência de tempo e nós esperamos um tempo um pouco menor, de execução em relação ao Python.

Já o termos aqui, o resultado do OpenCV, está igualzinho ao do Python, e no caso temos aqui 0,3 milissegundos na média, em comparação ao Python, com seus 20 e 30 milissegundos, então temos aqui 0,3 milissegundos no melhor caso temos 0,19 milissegundos e no pior caso 4 milissegundos, certo? Então está bem mais rápido que o Python.

Vamos então ver se conseguimos chegar mais rápido com o RayLighter. Então eu tenho aqui a pipeline implementada no RayLighter, com um Generator e com um parâmetro aqui para selecionar o Schedule customizado, certo?

E aí o que temos aqui é esse MOOCs, e aí no caso vamos poder observar uma das maiores utilidades do Unroll, que é a eliminação do Select para fazer esse Assign dos canais, certo? Então temos um MOOCs aqui e conseguimos converter, ao invés de ter essa multiplexação, vamos converter isso daqui para executar o Assign um depois do outro ali, dentro do loop mais interno da minha pipeline.

Então eu tenho aqui primeiro a utilização com o paralelismo apenas sendo aplicado no y desse lado dessa imagem. Então vamos verificar ainda sem o Unroll, certo? Uma aplicação mais simples de Skylark.

Ao executar isso aqui obtém um código também bem mais rápido que o Python, mas mais lento que eu vou perceber. Inclusive o melhor tempo dessa execução em RayLight é estar assinando a média em relação ao OpenCV, certo?

Nós podemos então agora verificar o statement file, que eu tenho já carregado aqui, eu só preciso recarregar essa página. Vamos fechar o assembly e verificar aqui que está esse MOOCs aqui dentro, internamente no... temos aqui o y sendo executado em paralelo e dentro do x, na parte mais interna temos esse MOOCs aqui, certo?

Então, vamos remover esse MOOCs. Como vimos nos slides, precisamos utilizar algumas diretivas junto do meu Unroll para poder ter o comportamento que nós esperamos.

A primeira delas, como mencionado, é o Reorder. Nós precisamos mover os canais para a parte mais interna do loop, certo? Temos que garantir que eles estejam na parte mais interna do alinhamento do loop.

Seguinte a isso, utilizamos o Bound para estabelecer os limites da minha var, que é os canais. E aí eu tenho o mínimo, que é zero, e a extensão dessa minha dimensão, que vai ser o 3, então vamos de zero até 2, né? Zero, um e dois.

E seguinte a isso executamos o Unroll. Eu posso remover isso aqui para que veja um efeito que pode ser observado quando vocês estiverem utilizando essas diretivas, certo?

Então, ao rodar o caso 1, temos ainda uma papelada um pouco mais lenta, inclusive, e ao verificar a atualização aqui do Statement File, vou aumentar um pouco, temos o seguinte, são três versões aqui da minha execução, certo?Uma para cada um daqueles canais. Então, é como se o relator estivesse considerando o C, o canal, como o loop mais externo. Estamos executando agora com o Computing Root, para ver o que o relator está fazendo por padrão com essa nossa Plattline.

Eu vou recarregar o meu Statement File, e como esperado, o relator vai estar em um lugar que eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, eu vou fazer o que eu quero, vamos então remover e passar a fazer o E-Order junto com as duas outras diretivas.

Ok, a gente tem um pouco mais rápido do que o nosso teste lá inicial com aqueles três loops, certo? Mas ainda não tão rápido quanto o Parallel e também não tão rápido quanto o OpenCV. Vamos então prosseguir com o Scheduling dessa Pipeline, mas antes passando para verificar a nova estrutura obtida no Statement File.

Então, nós temos aqui que temos o loop mais externo em meio de output pelo Y, depois pelo X e os três canais sendo feito o Assign em sequência, um após o outro. Iremos então passar para o caso dois, que é a aplicação do Parallelism após esse Unroll, dessa forma com o E-Order e o Bounding.

Achamos agora um aumento na velocidade em comparação inclusive ao Schedule 0 aqui, onde a gente chegou a 0.29 milissegundos, com o OpenCV aqui já na média, já atingindo um valor menor, certo? Inclusive o pior caso também já tem uma boa diferença.

Nós podemos prosseguir com o nosso Scheduling fazendo, testando o Splitting e executando o Parallelismo a partir desse Splitting do Y, certo? Então ao invés de paralelizar a var Y pura, a gente vai fazer um Splitting nela antes de aplicar o Parallelism.

E novamente, como antes, eu estou utilizando o Vector Size obtido com a função Natural Vector Size do Relight como uma guia para minha experimentação. É só um valor que eu posso rapidamente experimentar vários fatores dele como uma guia, certo? No caso de aplicar ele por 8.

Então nós vamos executar aqui o caso 3. Ele é um pouco mais lento que a versão anterior. A gente pode experimentar e modificar esse Vector Size e ver se a gente obtém um tempo um pouco melhor. Ele está um pouco mais rápido, mas ainda assim não superou o nosso Schedule 2.

Nós iremos observar o Schedule 4 onde a gente encadeia o Splitting com a Vectorização, certo? O Parallelismo com a Vectorização. Nós executamos o nosso Unroll, fazemos o Splitting do Y e aplicamos o Parallelismo na variável mais externa e fazemos o Splitting do X e aplicamos a Vectorização na variável mais interna, certo?

Novamente a gente pode experimentar com diversos fatores desse número ou simplesmente ir variando manualmente os números, certo? Com isso nós obtemos 0,1 milissegundos. Nós podemos então rodar novamente o Prototype C++, como você vê.

Certo? E em comparação nós temos aí metade do tempo na média com o Relight. E aí, para observar o que acontece se você tenta executar essa mesma operação, você tenta executar essa mesma operação sem o Unroll, certo?

Eu tenho o caso 5 aqui. Ok? Então sem o Unroll a gente consegue chegar abaixo do OpenCV na média, certo? Mas não tão rápido quanto fazendo o Unroll. Inclusive, é também ali no range de metade do tempo em relação a esse Schedule sem o Unroll.

A gente pode testar também a Reordenação, nesse caso 5. E aí também é um pouco mais rápido, mas o melhor tempo está na mesma ordem ali do nosso Schedule, ou 4. Aqui na média, no caso.

Então nós aplicamos o Unroll para tanto possibilitar algumas otimizações do nosso Regal, como também eliminar o Moops dentro do Loop mais interno da nossa Pipeline. Como letra recomendada, eu deixo para vocês tanto a lição de Scheduling do Relight, como também, novamente, o ponto em que Relight define ali definições relacionadas às Funcs, que vai conter as definições do Relight, as duas definições do Relight, para o Unroll.

Muito obrigado pela atenção de vocês e até a próxima.