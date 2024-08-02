+++
categories = ["machine-learning", "perceptron", "pt", "Javascript"]
date = 2019-11-03T03:04:13Z
description = ""
draft = false
image = "https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/perceptron-cover.png"
slug = "comecando-com-machine-learning-escrevendo-um-perceptron"
tags = ["machine-learning", "perceptron", "pt", "Javascript"]
title = "Começando com Machine Learning - Escrevendo um Perceptron"

+++


Você sabe o que é um **Perceptron**? 

Perceptron é um dos ==building blocks== (componentes) da **rede neural feed foward**. Uma rede neural é essencialmente baseada em N Perceptrons.

Segundo wikipedia:
> In machine learning, the *perceptron* is an algorithm for supervised learning of binary classifiers. A binary classifier is a function which can decide whether or not an input, represented by a vector of numbers, belongs to some specific class.

Ou seja, é um programa que com base em **entradas** (números), capaz de **classificá-los** em classes (números).

Vamos entender mais essa frase quando desenvolvermos um pouco de código.

Neste tutorial, você irá:

> * Desenvolver um **Perceptron** utilizando a linguagem **Javascript**

> * Entender o que é um Perceptron

## <a name="pre-reqs"></a> Pré-requisitos

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Chrome](https://www.google.com/intl/pt-BR/chrome/)
* Conhecimentos em Javascript

## <a name="getting-started-with-teori"></a> Um pouco de teoria

![neuron](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/neuron.png)

O **Perceptron** é uma representação, através de código, de um neurônio. Não é uma representação não exata, mas uma representação através de código do que é um neurônio.

Passos do neurônio:

1. Receber entradas através dos *detritos*
2. Processar as entradas dentro do neurônio (algoritmo)
3. Enviar uma saída através do axônio

![perceptron](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/perceptron.png)

Bem mais simples que o biológico, pois se trata de um programa de computador com alusão ao neurônio biológico.

Ok. Mas e a rede neural?

![multi-layer-perceptron](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/multi-layer-perceptron.png)

Bem, a rede neural (ou *neural network* em inglês), temos a combinação de vários *perceptrons* (vários neurônios), com alguns ajustes, ou muitos ajustes :) 

Mas a ideia é basicamente a combinação de vários *perceptrons*.

## <a name="getting-the-problem"></a>Elaborando melhor nosso problema - O *Perceptron*

Ele deve receber duas entradas, vamos chamá-las de `X1` e `X2` (inputs), processá-las e retornar uma saída `Y1` (classe).

Ou seja, nosso *perceptron* deve resolver um problema de classificação.

Dado duas entradas (pode ser mais, para simplificação do problema somente duas agora) ele deve responder uma saída que corresponde a uma *classe*, de prefêrencia a correta.

![classification-problem-2](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/classification-problem-2.png)

Nosso problema é a classificação dos círculos.

>Lembre-se do plano cartesiado, onde a linha azul é o `Y` e a linha vermelha é o `X`. 

Nosso *perceptron* deve classificar os círculos entre abaixo da seta (classe 1) ou acima da seta (classe 2).

Vamos aos passos do algoritmo:

1. Pense em um dos desses pontos (qualquer um), ele é representado no plano por dois números (X e Y). 
Esses pontos serão as entradas do *perceptron*.

2. A saída do perceptron deverá ser a um número que represente sua classificação (se pertence ao grupo dos *acima da linha* ou do grupo dos *abaixo da linha*).

3. Caso nosso perceptron responda errado, iremos informar a ele o valor correto para ele se auto ajustar (algoritmo de Gradient Descent, já chegamos lá).

4. Caso ele responda certo, também iremos informá-lo para se auto ajustar.

Não se preocupe, você vai entender melhor com o decorrer da leitura desse artigo.

### Componentes do Perceptron

![perceptron-1](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/perceptron-1.png)

Além de nossas entradas `X` e `Y`, nosso perceptron também tem `weight x` e `weight y`.

Esses dois componentes são utilizados para *pesar* nossas entradas. 

Eles serão os itens passíveis de ajustes conforme o resultado e feedback do nosso programa.

### O Algoritmos do Perceptron

Passo-a-passo do algoritmo

1. A formula: Y = X0 * W0 + X1 * W1
2. Activation Function: *Sign(n)*
 1. if (n >= 0) return +1;
 2. else return -1;
 3. Caso número seja positivo ou igual a zero = **+1**
 4. Caso contrário será **-1**
3. Inicialização dos *weights* randomicamente com valores entre `0` e `1`

> Obs.: Existem outros tipos de **Activation Function**, mas esse é o mais fácil para entendermos no momento.

##Chega de papo, vamos ao **código**.

1 - Vamos criar uma pasta vazia chamada de `perceptron`, nela vamos criar três arquivos.

>1. `index.html` (onde iremos depurar e observar resultados no *google chrome* -- eu prefiro e gosto do *google chrome* para depurar o javascript)
2. `sketch.js` onde irá rodar nosso programa, inicialização de variáveis, *main()*, etc.
3. `perceptron.js`, onde iremos escrever o código do *perceptron*
4. `point.js` será nossa representação lógica do ponto no plano cartesiado

![schema](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/schema.png)

Aqui estou usando o **Visual Studio Code**, mas você pode utilizar qualquer editor de texto (também é minha preferência para editor de texto).

2 - Vamos começar pelo nosso arquivo `index.html`. Vamos colocar tudo que precisamos para começar.

```html
<!--index.html-->
<!DOCTYPE html>
<html>
  <head>
      <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.6.0/p5.min.js"></script>
      <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.6.0/addons/p5.dom.min.js"></script>
      <script src="point.js"></script>
      <script src="perceptron.js"></script>
      <script src="sketch.js"></script>
  </head>
  <body>
  </body>
</html>
```

As primeiras duas dependências são necessárias para algumas funções de cálculo como o método `random()` e `map()` e também para nos ajudar a desenhar na tela. 

Assim também teremos um feedback visual (ajuda no entendimento da solução).

3 - Agora ao que interessa, vamos para o arquivo `perceptron.js`.

Como descrito acima, nosso perceptron precisa de `weights` (pesos), um para cada entrada, no nosso caso duas entradas (X1 e X2).

```javascript
//perceptron.js
class Perceptron {
    weights = [0, 0];

    constructor() {
        //inicializando os weights com valores random
        for(let i = 0; i < this.weights.length; i++) {
            //random funcion vem das nossas dependências
            this.weights[i] = random(-1, 1)
        }
    }   
}
```

Temos uma classe `Perceptron`, com uma variáveis `weigths` (array com duas posições) e a inicializamos com valores randômicos.

4 - Agora ao nosso método `guess()` (adivinhar), vamos aplicar a formula descrita acima *(Y = X1 * W1 + X2 * W2)*.

```javascript
//perceptron.js
    //inputs --> entrada array de duas posiçoes (X1 e X2)
    guess(inputs) {
        let sum = 0;

        for(let i = 0; i < this.weights.length; i++) {
            sum += inputs[i] * this.weights[i];
        }

        const output = sign(sum);
        return output;
    }
```

Coloque dentro da classe `Perceptron`.

5 - O método `sign()`. Este vai fora da classe `Perceptron`, porém no mesmo arquivo.

```javascript
//perceptron.js
function sign(num) {
    return num >= 0 ? 1 : -1;
}
```

Ótimo, agora vamos ver como ficou nosso arquivo `perceptron.js`.

```javascript
//perceptron.js

//the activation function
function sign(num) {
    return num >= 0 ? 1 : -1;
}

class Perceptron {
    weights = [0, 0];
    lr = 0.1;

    constructor() {
        for(let i = 0; i < this.weights.length; i++) {
            this.weights[i] = random(-1, 1)
        }
    }   

    guess(inputs) {
        let sum = 0;

        for(let i = 0; i < this.weights.length; i++) {
            sum += inputs[i] * this.weights[i];
        }

        const output = sign(sum);
        return output;
    }
}
```

Ótimo, hora de testar o funcionamento.

###<a name="testing-the-perceptron"></a> Testando o perceptron

1 - Abra o arquivo `sketch.js` e cole o seguinte conteúdo.

```javascript
let perceptron;

function setup() {
    createCanvas(550, 550);

    perceptron = new Perceptron();

    const inputs = [-1, 0.5];

    const guess = perceptron.guess(inputs);

    console.log(`nosso resultado ${guess}`);
}

function draw() {
}
```

Abra o `browser` e o `console de desenvolvedor` com nosso arquivo `index.html` aberto.

![result-1](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/result-1.png)

De *refresh* no browser por algumas vezes até ver o resultado mudar.

Isso ocorre devido a nosso *weights* serem inicializados randomicamente a cada `construtor`, ou a cada **refresh de página**.

Perfeito. Chegamos até aqui. Nosso *perceptron* já está funcionando. Não muito para ser ver aqui, mas avançamos bastante.

###<a name="getting-ready-to-test-perceptron"></a> Preparando para testar nosso perceptron com uma massa maior - O **Dataset**. 

Nosso dataset (ou massa de dados) terá uma série de círculos, já com label (vamos saber de ante mão a classificação correta) e passar para nosso perceptron classificar - **guess()**.

####1 - Point.js - Representação do ponto

Primeiro abra o arquivo `point.js` e declare-o com o seguinte conteúdo. Ele será nossa representação de ponto no espaço.

```javascript
//point.js
class Point {
    x = 0;
    y = 0;
    label = 0;

    constructor(x, y) {
        this.x = x;
        this.y = y;

        this.label = this.getLabel();
    }

    getLabel() {
        if(this.x > this.y) {
            return 1;
        }
        else {
            return -1;
        }
    }

    getPixelX() {
        const px = map(this.x, -1, 1, 0, width);
        return px;
    }

    getPixelY() {
        const py = map(this.y, -1, 1, height, 0);
        return py;
    }

    show() {
        stroke(0);

        if(this.label === 1) {
            fill(0);
        }
        else {
            fill(255);
        }

        const px = this.getPixelX();
        const py = this.getPixelY();
        ellipse(px, py, 22, 22);
    }

    debug() {
        console.log(`label: ${this.label} x ${this.x} y ${this.y}`)
    }
}
```

Vamos entender um pouco da nossa classe.

`Construtor` recebe dois argumentos, `X` e `Y`. Dados os argumentos o próprio ponto se classifica (somente para comparação com o resultado do `perceptron`).

`getLabel` bem simples, se `x` > `y` é 1, senão é 0.

`show()` irá se auto desenhar na tela utilizando métodos do `p5.js`.

`debug()` escreve no console do browser informações de debug (X, Y e Label).

`getPixelX()` e `getPixelY()` convertem o valor de `X` e `Y` do plano cartesiado para valores de tela (width e height). Ou seja, caso entrada seja 0.5, ele irá mapear para o valor correspondente na tela de acordo com nossa área de desenho.

Ah, e antes que me esqueça. Nossas entradas estão entre `-1` e `1`, por exemplo `-1`, `0.2`, `0.993` e assim por diante.

####2 - Preparando o sketch.js
Legal, vamos criar uma serie de pontos e colocarmos na tela.

```javascript
//sketch.js
let points = new Array(100);

function setup() {
    createCanvas(550, 550);

    for(let i = 0; i < points.length; i++) {
        points[i] = new Point(random(-1, 1), random(-1, 1));
    }
}

function draw() {
    background(255);

    for(let i = 0; i < points.length; i++) {
        points[i].show();
    }
}
```

Utilizando a biblioteca `p5.js` (uma de nossas dependências) só precisamos declarar dois métodos `setup()` e `draw()`.

`setup()` é onde iremos inicializar nossas variáveis.

`draw()` é onde manipulamos nossas variáveis e realizamos o desenho na tela.

Pronto, agora um **refresh** no navegador `index.html` do nosso programa.

![output-1](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/output-1.png)

O que vemos aqui? Um série de *círculos*, alguns **brancos** outros **pretos**. Agora, perceba o padrão, veja que podemos traçar uma linha separando os círculos de cada cor.

Os brancos pertencem a **classificação 0** e os pretos a **classificação 1**.

![classification-problem-3](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/classification-problem-3.png)

###3 - Feedback e classificação para nosso perceptron

Voltando ao nosso desenho inicial.

![perceptron-2](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/perceptron-2.png)

Agora que temos os valores corretos, o que temos que fazer é informar ao *perceptron* se ele **errou**, e quão errado foi, para se autocorrigir (mais código claro).

Para tal façanha, o algoritmo será:

>ERROR = DESIRED OUTPUT - GUESS OUTPUT

![error-table](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/error-table.png)

Em nossa tabela, onde *deseired* é o desejado, *guess* foi o retornado pelo *perceptron* e *error* é o cálculo do erro (quão errado foi).

Lembre-se, a ideia é encontrar os **weights** ótimos, com essa formula podemos ajustar para chegar lá. 

Uma das maneiras é a utilização do algoritmo de **gradient descent**, mas vamos utilizar um algoritmo um pouco mais simples nesse momento.

Nossa formula:

> W0 = error * X0

Nosso processo será:

> 1 - Prover ao perceptron inputs que já sabemos a resposta

> 2 - Perguntar ao perceptron a resposta

> 3 - Computar o erro (acertou ou error)

> 4 - Ajustar o **Weigths** de acordo com o erro

> 5 - Retornar ao primeiro passo e repetir

Vamos programar nosso método **train()**. 

####4 - Método Train()

```javascript
//perceptron.js
    train(inputs, target) {
        const guess = this.guess(inputs);

        const error = target - guess;
        //tune all the weights
        for(let i = 0; i < this.weights.length; i++) {
            this.weights[i] += error * inputs[i] * this.lr;
        }
    }
```

Mas espere, o que é essa variável **this.lr**? Não tínhamos isso antes.

Bom, esse é nosso **Learning Rate**. Isso resolve o problema de **over shoot** (ajustar demais). 

> O que é ótimo para uma **entrada* não é o ótimo para **outra**.

Essa variável **Learning rate** irá nos ajudar a ajustar de maneira mais assertiva os nossos *weights*.

```javascript
//perceptron.js

function sign(num) {
    return num >= 0 ? 1 : -1;
}

class Perceptron {
    weights = [];
    //learning rate
    lr = 0.1;

    constructor(numberWeigths) {
        this.weights = new Array(numberWeigths);

        for(let i = 0; i < this.weights.length; i++) {
            this.weights[i] = random(-1, 1)
        }
    }   

    guess(inputs) {
        let sum = 0;

        for(let i = 0; i < this.weights.length; i++) {
            sum += inputs[i] * this.weights[i];
        }

        let output = sign(sum);
        return output;
    }

    train(inputs, target) {
        const guess = this.guess(inputs);

        const error = target - guess;

        for(let i = 0; i < this.weights.length; i++) {
            this.weights[i] += error * inputs[i] * this.lr;
        }
    }
}
```

Nosso `perceptron.js` por completo. Agora vamos ajustar nossa `sketch.js` para utilizamos o método **train()**.


####5 - Ajustando sketch.js para utilizar o método Train()

```javascript
//sketch.js
let perceptron;

let points = new Array(100);

function setup() {  
    createCanvas(550, 550);

    perceptron = new Perceptron(2);

    for(let i = 0; i < points.length; i++) {
        points[i] = new Point(random(-1, 1), random(-1, 1));
        //points[i].debug();
    }
}

function draw() {  
    background(255);

    for(let i = 0; i < points.length; i++) {
        points[i].show();
    }

    noStroke();

    for(let i = 0; i < points.length; i++) {
        const pt = points[i];

        const inputs = [pt.x, pt.y];
        const target = pt.label;

        const guess = perceptron.guess(inputs);
        if(guess == target) {
            fill(0, 255, 00);
        }
        else {
            fill(255, 0, 0);
        }

        ellipse(pt.getPixelX(), pt.getPixelY(), 15, 15);
    }

    drawLine();

    trainSinglePoint();
}

function drawLine() {  
    stroke(0);
    line(0, height, width, 0);
}

let trainningIndex = 0;  
function trainSinglePoint() {  
    const pt = points[trainningIndex];

    const inputs = [pt.x, pt.y];
    perceptron.train(inputs, pt.label);

    trainningIndex++;

    if(trainningIndex == points.length) {
        trainningIndex = 0;
    }
}
```

Execute novamente nosso programa no browser e veja que agora os circulos *verdes* indicam acerto do *perceptron* e os círculos *vermelhos* indicam erro.

E a cada loop do método **draw()** executamos o método **train()** do perceptron.

Por isso os círculos ficam mudando de cor para eventualmente parar até todos estarem certos.

![perceptron-3-1](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/perceptron-3-1.png)

Faça um experimente, comente o método **trainSinglePoint()** no `sketch.js*, você verá o resultado sem nenhum treinamento.

![perceptron-4](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/perceptron-4.png)

###O *Bias* (terceiro elemento)

Mas nosso perceptron tem um grande problema. O que acontece quando enviamos o ponto 0,0 para ele?

> X0 = 0

> X1 = 0

Segundo nosso algoritmo o resultado será sempre 0.

> Y0 = X0 * W0 + X1 * W1

Mas isso é um problema por que? Você me pergunta.

Considere a seguinte linha. (formula da reta --> y = mx + b).

![bias-sample](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/bias-sample.png)

O nosso algoritmo nunca irá acertar, porque a linha não passa pelo centro do plano (0,0). Nunca!

Para evitar esse dilema, nosso perceptron exigirá uma terceira entrada, geralmente chamada de entrada de **bias**. Uma entrada de o valor de 1 e que também é ponderada. 

Aqui está o nosso *perceptron* com a adição do **bias**:

![perceptron-5](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/perceptron-5.png)

###Adicionamento o Bias ao código

Primeiro vamos utilizar uma formula genérica para calcular nossa linha de classificação.

1-No arquivo `point.js` e adicione a seguinte função fora da classe.

```javascript
//point.js
function f(x) {
    //y = mx + b
    return 0.3 * x + 0.2;
}
```

2 - No mesmo arquivo modifique a função **getLabel()**. Isso irá obter a classificação de acordo com nossa função de reta.

```javascript
getLabel() {
    const lineY = f(this.x);
    if(this.y > lineY) {
        return 1;
    }
    else {
        return -1;
    }
}
```

2- Arquivo `sketch.js` vamos modificar a escrita da linha para utilizar-se da nova função.
```javascript
//scketh.js
function drawLine() {
    stroke(0);
    //código antigo
    //line(0, height, width, 0);

    //construindo o primeiro ponto (-1 e -1)
    const p1 = new Point(-1, f(-1));

    //construindo o segundo ponto (1 e 1)
    const p2 = new Point(1, f(1));

    //desenhando a nova linha
    line(p1.getPixelX(), p1.getPixelY(), p2.getPixelX(), p2.getPixelY());
}
```

Rode novamente no browser para ver a nova linha.

![new-line](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/new-line.png)

Intencionalmente não passamos pelo centro da tela (0, 0 no plano cartesiado).

Perceba que se você deixar rodando o programa nunca vai chegar ao correto resultado.

###Adicionando o bias ao algoritmo

O bias no perceptron.

> Y = W1 / W2 * X + B

Onde B é o Bias.

1 - No arquivo `perceptron.js`. Vamos modificá-lo para receber pelo construtor a quantidade de `weigths` (um deles será o bias).

```javascript
...
class Perceptron {
    weights = [];
    lr = 0.1;

    constructor(numberWeigths) {
        this.weights = new Array(numberWeigths);

        for(let i = 0; i < this.weights.length; i++) {
            this.weights[i] = random(-1, 1)
        }
    } 
    ...
```

2 - Arquivo `point.js` vamos adicionar o **bias**. Cada ponto irá carregar o próprio **bias**, mesmo que seja sempre o mesmo valor :)

```javascript
//point.js
class Point {
    x = 0;
    y = 0;
    bias = 1;
    label = 0;

    constructor(x, y) {
        this.x = x;
        this.y = y;
        this.bias = 1;

        this.label = this.getLabel();
    }
...
```

3 - no arquivo `sketch.js` vamos modificar o método **draw()** para passarmos o bias quando mandarmos treinar.

```javascript
//sketch.js
function draw() {
    background(255);

    for(let i = 0; i < points.length; i++) {
        points[i].show();
    }

    for(let i = 0; i < points.length; i++) {
        const pt = points[i];

        //antigo método de train
        //perceptron.train(inputs, pt.label);

        //input com o bias
        const inputs = [pt.x, pt.y, pt.bias];
        const target = pt.label;

        const guess = perceptron.guess(inputs);
        if(guess == target) {
            fill(0, 255, 00);
        }
        else {
            fill(255, 0, 0);
        }
        noStroke();
        ellipse(pt.getPixelX(), pt.getPixelY(), 15, 15);
    }

    drawLine();

    trainSinglePoint();
}
```

4 - Agora na função **setup()** vamos ajustar o construtor do **perceptron**.

```javascript
function setup() {
    createCanvas(550, 550);

    perceptron = new Perceptron(3);

    for(let i = 0; i < points.length; i++) {
        points[i] = new Point(random(-1, 1), random(-1, 1));
        //points[i].debug();
    }
}
```

5 - E agora na função **trainSinglePoint()**.

```javascript
let trainningIndex = 0;  
function trainSinglePoint() {  
    const pt = points[trainningIndex];

    const inputs = [pt.x, pt.y, pt.bias];
    perceptron.train(inputs, pt.label);

    trainningIndex++;

    if(trainningIndex == points.length) {
        trainningIndex = 0;
    }
}
```

Tudo pronto. Rode novamente no browser. Tudo deve estar funcionando e o *perceptron* sendo capaz de determinar todos os pontos corretamente.

### Visualizando o que o perceptron acha que é a linha de classificação

#### 1 - Adicionando o método *guessY()** ao perceptron

Abra arquivo `perceptron.js` e adicione o código do método **guessY()** a classe **Perceptron**.

```javascript
guessY(x) {
    const w0 = this.weights[0];
    const w1 = this.weights[1];
    const w2 = this.weights[2];

    return -(w2 / w1) - (w0 / w1) * x;
}
```

Agora abra o arquivo `sketch.js` novamente no método **drawLine()** e substitua por.

```javascript
function drawLine() {
    stroke(0);
    //line(0, height, width, 0);

    const p1 = new Point(-1, f(-1));
    const p2 = new Point(1, f(1));

    line(p1.getPixelX(), p1.getPixelY(), p2.getPixelX(), p2.getPixelY());

    const guessP1 = new Point(-1, perceptron.guessY(-1));
    const guessP2 = new Point(1, perceptron.guessY(1));

    line(guessP1.getPixelX(), guessP1.getPixelY(), guessP2.getPixelX(), guessP2.getPixelY());
}
```

Perceba que agora estamos construindo os pontos X1, Y1 e X2 e Y2 através do método **guessY()**. Isso irá nos dizer o que o **Perceptron** acha que é o correto.

Pronto. Chegamos ao fim. *Refresh* na tela de novo.

Agora te recomendo *brincar* um pouco com a variável **learning rate**, aumente e diminua ela para entender como ela funciona.

## Próximas etapas

* Adicione círculos para o *perceptron* resolver que não façam parte do dataset inicial
* Evolua o programa para resolver o problema XOR

## Referências

* [Youtube's Daniel Shiffman I - 10.2: Neural Networks: Perceptron Part 1 - The Nature of Code](https://www.youtube.com/watch?v=ntKn5TPHHAk)
* [Youtube's Daniel Shiffman II - 10.1: Introduction to Neural Networks - The Nature of Code](https://www.youtube.com/watch?v=XJ7HLz9VYz0&list=PLRqwX-V7Uu6Y7MdSCaIfsxc561QI0U0Tb)
* [Wikipedia - Perceptron](https://en.wikipedia.org/wiki/Perceptron)
* [Nature of Code E-Book](https://natureofcode.com/)

