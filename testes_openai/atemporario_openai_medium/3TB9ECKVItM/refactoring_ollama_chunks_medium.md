Olá, seja muito bem-vindos a mais uma aula do curso de linguagem de programação específica para IA. Hoje iremos explorar mais alguns conceitos de scheduling. O caso do conceito de hoje será o Unroll.

Ele, como a gente viu naquele exemplo idático com a geração de números de Fibonacci, pode ser utilizado para diminuir o número de interações a custo do tamanho do corpo do loop. Então nós vamos aumentar o corpo do loop a fim de diminuir o número de interações. A gente viu que ele tem o efeito de como se executasse múltiplas interações em o que seria um a só no loop transformado.

Ele pode ser útil para quando a gente tem pixels que manipulam dados incomunes e um padrão específico que a gente vai ver frequentemente que às vezes é útil utilizar o Unroll é quando a gente tem mais de um canal e quer se pre-executar o assigne desses canais em sequência, então um atrás do outro sem uma criação de um novo loop.

Ele tem duas sintaxes, a primeira delas é só passando um Avar para dentro desse Unroll e a outra Avar em um Fator. Ele vai ser utilizado com Bound ou com Split. No caso aqui, quando a gente quer fazer um Unroll por exemplo de um Avar que não seja um dos canais, a gente vai normalmente precisar fazer um Split dessa Avar, certo? E a gente faz o Split com o Fator e depois aplica um Unroll nessa Avar que vai estar ali bem interna no alinhamento do loop, certo?

Uma outra opção é você fazer essa sintaxe aqui, Unroll, passando o Avar e depois o Fator. A diferença é que você vai ficar escondido o Split, certo? Ele vai esconder esse Split.

Para o exemplo dos canais, o que a gente vai utilizar é esse Bound aqui. Então, nós vamos na verdade utilizar tanto o Reorder como o Bound para mover os canais para a parte mais interna do loop, certo? O Bound vai dizer para o Relight que a minha variável do canal é limitada entre 0 a 3, certo? Então ali RGB, sempre que eu trabalhar em Relight com o mínimo e o Extent, que é a extensão daquela dimensão.

Então a gente vai passar para o Bound a variável, o mínimo que vai ser 0 e o Extent que vai ser 3, que são três canais. E a partir disso, né, diferentemente da sintaxe com o Split, a gente sabe os limites que esse loop vai percorrer, a gente sabe qual é os limites da minha variável.

Então a gente utiliza o Bound para passar essa informação para o Unroll depois. No caso onde a gente não sabe a dimensão do loop, que vai ser nos casos onde a gente tem uma variável que não seja aquela dos canais, por exemplo, a gente vai estar utilizando essa sintaxe com o Split.

Iremos agora verificar na prática a utilização do Unroll. Eu tenho aqui um exemplo onde eu estou aplicando um efeito Sap em uma imagem, certo? Então nós temos um efeito de falta antiga sendo aplicado.

Primeiramente mostrando o protótipo de Python para a gente ter um guia da temporização, certo, do runtime dessa pipeline. Eu vou rodar o protótipo aqui. E nós temos em Python a velocidade ali de 20, no caso está demorando 23 milisegundos, no caso 24 milisegundos, para completar a execução da pipeline na média.

No pior caso está em 46 milisegundos e no melhor caso 20 milisegundos. A pipeline é simples, ela só envolve multiplicar os canais da imagem por diversos fatores, certo? Cada canal novo vai ter um conjunto de três fatores associados a ele, certo?

Então cada novo canal da imagem, cada novo RGIB é uma ponderação dos RGIB anteriores, iniciais. Após isso eu faço o merging desses canais, converto para a Uinti8 a versão clipada dele, ou seja, mantida ali entre 0 e 55.

Eu tenho aqui também um protótipo implementado em C++ com OpenCV. Então é a nossa segunda referência de tempo e nós esperamos um tempo um pouco menor de execução em relação ao Python. Já temos aqui o resultado do OpenCV, está igualzinho ao do Python e no caso a gente tem aqui 0,3 milisegundos na média, certo?

Em comparação ao Python com seus 23 milisegundos. No melhor caso 0,19 milisegundos e no pior caso 4 milisegundos, certo? Então está bem mais rápido que o Python.

Vamos então ver se a gente consegue chegar mais rápido com o Relight. Então eu tenho aqui a pipeline implementada no Relight com o generator e com um parâmetro para selecionar o schedule customizado. E aí o que a gente tem aqui é esse mox e aí no caso a gente vai poder observar uma das maiores utilidades do Unroll, que é a eliminação do select para fazer a assign dos canais.

Então a gente tem um mox aqui e a gente consegue converter, ao invés de ter essa multiplexação, a gente vai converter isso daqui para executar o assign um depois do outro ali dentro do loop mais interno da minha pipeline. Então eu tenho aqui primeiro a utilização com um canalismo apenas sendo aplicado no Y, se você não sabe dessa imagem.

Então nós vamos verificar ainda sem o Unroll, certo? Uma aplicação mais simples de Skylark. Ao executar isso aqui a gente obtém um código também bem mais rápido que o Python, mas mais lento que o OpenCV. Inclusive o melhor tempo dessa execução em Relight é assinando a média em relação ao OpenCV.

Nós podemos então agora verificar o statement file que eu tenho já carregado aqui, eu só preciso ligar essa página, vamos fechar o assembly e verificar aqui que a gente tem esse mox aqui dentro, internamente no... a gente tem aqui o Y sendo executado em paralelo e dentro do do Y, a parte mais interna tem esse mox aqui, certo?

Então vamos remover esse mox. Como a gente viu nos slides, a gente tem que utilizar algumas diretivas junto do meu Unroll para poder ter o comportamento que nós esperamos. A primeira que a gente precisa fazer como mencionado é o Reorder, nós precisamos mover os canais para a parte mais interna do loop, certo?

A gente tem que garantir que eles estejam na parte mais interna do alinhamento do loop. Seguinte a isso, utilizamos o Bound para estabelecer os limites da minha var, que são os canais, e aí eu tenho o mínimo que é zero e a extensão dessa minha dimensão, que é 6, então a gente vai de zero até 2, né? Zero, um e dois.

E seguinte a isso nós executamos o Unroll. Eu posso remover isso aqui para que a gente veja um efeito que pode ser observado quando vocês estiverem utilizando essas diretivas, certo?

Então ao rodar o caso 1, a gente tem ainda uma papelada um pouco mais lenta inclusive, e ao verificar a atualização aqui do Statement File, vou aumentar um pouco, nós temos o seguinte, são três versões aqui da minha observação, certo? Uma para cada um daqueles canais, então é como se o rodador estivesse considerando o C, o canal.Como o loop mais externo. Estamos executando agora com o Compute Root, para ver o que o Lila está fazendo por padrão com essa nossa plato-line. Eu vou recarregar o meu Statement File, e como esperado o C, o canal, ele está aqui no loop mais externo, então ao fazer o Unroll, a gente estabelece esse extente aqui com o Bound, certo? E ao fazer o Unroll sem o Reorder, ele vai desenrolar essa computação em três loops diferentes, então três loops por toda a imagem diferente.

Vamos então remover e passar a fazer o Reorder junto com as duas outras diretivas. A gente tem um pouco mais rápido do que o nosso teste inicial com aqueles três loops, certo? Mas ainda não tão rápido quanto o Parallel e também não tão rápido quanto o OpenCV. Vamos então prosseguir com o scheduling dessa plato-line, mas antes passando para verificar a nova estrutura obtida no Statement File.

Então nós temos aqui que temos o loop mais externo, e meio de output pelo Y, depois pelo X, e os três canais sendo feito o Assign em sequência, um após o outro. E iremos então passar para o caso dois, que é a aplicação do Parallelismo após esse Unroll, certo? Dessa forma com o Reorder e o Bound.

Achamos agora um aumento na velocidade em comparação inclusive ao Schedule 0 aqui, onde a gente chegou a 0.29 milissegundos, com o OpenCV aqui já na média, já atingindo um valor menor, certo? Inclusive o pior caso também já tem uma boa diferença.

Nós podemos prosseguir com o nosso Schedule fazendo, testando o Splitting e executando o Parallelismo a partir desse Splitting do Y, certo? Então ao invés de paralisar a VAR Y pura, a gente vai fazer o Splitting nela antes de aplicar o Parallelismo. E novamente, como antes, eu estou utilizando o Vector Size, obtido com a função Natural Vector Size do Relight, como uma guia para minha experimentação.

É só um valor que eu posso rapidamente experimentar vários fatores dele como uma guia, certo? No caso aqui eu estou multiplicando ele por 8. Então nós vamos executar aqui o caso 3. Ele é um pouco mais lento que a versão anterior. A gente pode experimentar também modificar esse Vector Size e ver se a gente obtém um tempo um pouco melhor.

Ele está um pouco mais rápido, mas ainda assim ele não superou o nosso Schedule 2. Nós iremos então observar o Schedule 4, onde a gente encadeia o Splitting com a Vectorização, certo? O Parallelismo com a Vectorização. Nós executamos o nosso Unroll, fazemos o Splitting do Y e aplicamos o Parallelismo na variável mais externa, e fazemos o Splitting do X e aplicamos a Vectorização na variável mais interna, certo?

Novamente a gente pode experimentar com diversos fatores desse número ou simplesmente ir variando manualmente os números, certo? Com isso nós obtemos 0,1 milissegundos. Nós podemos então rodar novamente o Prototype em C++, como você vê. Certo? E em comparação nós temos aí metade do tempo na média com o Relight.

E aí para observar o que acontece se você tenta executar essa mesma operação sem o Unroll, certo? Eu tenho caso 5 aqui. Então sem o Unroll a gente consegue chegar abaixo do OpenCV na média, certo? Mas não tão rápido quanto fazendo o Unroll. Inclusive, também ali no range de metade do tempo em relação a esse Schedule sem o Unroll.

A gente pode testar também a Reordenação, nesse caso 5. E aí também um pouco mais rápido, mas o melhor tempo está na mesma ordem ali do nosso Schedule, 4. Aqui na média, no caso. Então nós aplicamos o Unroll para tanto possibilitar algumas otimizações do nosso código como também eliminar o MOOCs dentro do loop mais interno da nossa pipeline.

Como letra recomendada, eu deixo para vocês tanto a lição de Scheduling do Relight como também novamente o ponto em que Relight define ali definições relacionadas às Funcs, certo? Que vai conter as definições do Relight, as duas definições do Relight para o Unroll. Muito obrigado pela atenção de vocês e até a próxima.