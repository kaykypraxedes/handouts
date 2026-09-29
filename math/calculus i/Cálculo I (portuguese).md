```
 _____      _  _               _          _____ 
/  __ \    |/ | |             | |        |_   _|
| /  \/  __ _ | |  ___  _   _ | |  ___     | |  
| |     / _` || | / __|| | | || | / _ \    | |  
| \__/\| (_| || || (__ | |_| || || (_) |  _| |_ 
 \____/ \__,_||_| \___| \__,_||_| \___/   \___/ 
```

# 01. Limites e Continuidade

## Limite em um Ponto:

$f(a)$ indica o valor da função $f(x)$ quando $x = a$. $\displaystyle \lim_{x \to a} f(x)$ vê a tendência da função quando $x$ se aproxima de $a$.

![Limite de 1/|x| quando x se aproxima de zero](images/screenshot001.png)  
*Fonte: Elaborado pelo autor (2025).*

Para $f(x)=1/\lvert x\rvert$, $f(0)$ não existe. $\displaystyle \lim_{x \to 0} f(x) = \infty$ (quando $x$ se aproxima de $0$, $y$ tende ao infinito).

### Limites Laterais:

**Para um limite bilateral existir, quando podemos nos aproximar pelos dois lados, os limites laterais devem ser iguais** (o mesmo valor com a função se aproximando pela esquerda e direita).

$$
\lim_{x \to a^-} f(x) = \lim_{x \to a^+} f(x) = L \Rightarrow \lim_{x \to a} f(x) = L
$$

![Limites laterais de 1/x em zero](images/screenshot002.png)  
*Fonte: Elaborado pelo autor (2025).*

$$
\lim_{x \to 0^-} \frac{1}{x} = -\infty \quad \text{e} \quad \lim_{x \to 0^+} \frac{1}{x} = \infty \quad \Rightarrow \quad \nexists\lim_{x \to 0} \frac{1}{x}
$$

## Propriedades dos Limites:

Para limites finitos, as operações abaixo podem ser realizadas separadamente, respeitando as condições indicadas:

| Propriedade | Relação | Exemplo |
|---|---|---|
| Constante | $\lim_{x\to a}c=c$ | $\lim_{x\to\infty}2=2$ |
| Identidade | $\lim_{x\to a}x=a$ | $\lim_{x\to7}x=7$ |
| Multiplicação por constante | $\lim_{x\to a}[\alpha f(x)]=\alpha\lim_{x\to a}f(x)$ | $\lim_{x\to7}4x=4\cdot7=28$ |
| Soma | $\lim_{x\to a}[f(x)+g(x)]=\lim_{x\to a}f(x)+\lim_{x\to a}g(x)$ | $\lim_{x\to7}(x+2)=7+2=9$ |
| Produto | $\lim_{x\to a}[f(x)g(x)]=\left(\lim_{x\to a}f(x)\right)\left(\lim_{x\to a}g(x)\right)$ | $\lim_{x\to2}[x^2(2x+1)]=4\cdot5=20$ |
| Quociente | $\lim_{x\to a}\frac{f(x)}{g(x)}=\frac{\lim_{x\to a}f(x)}{\lim_{x\to a}g(x)}$, se o limite do denominador não for zero. | $\lim_{x\to2}\frac{x^2}{2x+1}=\frac45$ |

Para uma composição, se $g(x)\to L$ e $f$ é contínua em $L$, podemos aplicar a função externa ao limite da interna:

$$
\lim_{x\to a}f(g(x))=f\left(\lim_{x\to a}g(x)\right).
$$

Seja $f(x)=x^2$ e $g(x)=2x+1$. Então:

$$
\lim_{x\to2}(2x+1)^2=\left(\lim_{x\to2}(2x+1)\right)^2=5^2=25.
$$

### Continuidade em um Ponto:

Se $f$ é contínua em $a$, o valor para o qual a função tende é o próprio valor da função no ponto. Isso permite calcular o limite por substituição direta:

$$
\lim_{x\to a}f(x)=f(a).
$$

Para essa igualdade fazer sentido, $f(a)$ precisa existir, assim como o limite. A existência do limite, sozinha, não exige que a função esteja definida no ponto.

## Limites no Infinito:

Quando aplicamos um limite no infinito, a função pode se aproximar de um número real, crescer ou decrescer sem limite, ou não se fixar em um único comportamento, como ocorre em uma oscilação:

| Comportamento | Exemplo |
|---|---|
| Convergência para um número real | $\lim_{x\to\infty}\left(1+\frac1x\right)=1+0=1$ |
| Tendência a $+\infty$ ou $-\infty$ | $\lim_{x\to\infty}x^3=+\infty$ |
| Oscilação sem limite | $\lim_{x\to\infty}\sin x$ não existe. |

> Escrever que um limite é infinito descreve um crescimento ou decrescimento sem limite; não significa que o infinito seja um número real atingido pela função.

## Limites Indeterminados:

Ao substituir os valores ou analisar separadamente as partes de uma expressão, podemos encontrar uma **forma indeterminada**: ela, sozinha, não permite concluir o valor do limite. Alguns casos são:

| Forma | Exemplo |
|---|---|
| $0/0$ | $\lim_{x\to1}\frac{x^2-1}{x-1}$ |
| $\infty/\infty$ | $\lim_{x\to\infty}\frac{3x^2-1}{x^2+4}$ |
| $\infty-\infty$ | $\lim_{x\to\infty}(x^2-x)$ |
| $0\cdot\infty$ | $\lim_{x\to\infty}\left(\frac1x e^x\right)$ |
| $1^\infty$ | $\lim_{x\to\infty}\left(1+\frac1x\right)^x$ |
| $\infty^0$ | $\lim_{x\to\infty}(1+x)^{1/x}$ |

Para resolver essas indeterminações existem algumas ferramentas, envolvendo tanto manipulação algébrica quanto análise das relações entre funções. As mais comuns são:

### Polinômios em Evidência:

Principalmente em polinômios tendendo ao infinito, o crescimento é mais acelerado nos polinômios de maior grau, sendo eles dominantes:

$$
\begin{aligned}
\lim_{x\to\infty}\frac{3x^2-1}{x^2+4}
&=\lim_{x\to\infty}\frac{x^2(3-1/x^2)}{x^2(1+4/x^2)}\\
&=\lim_{x\to\infty}\frac{3-1/x^2}{1+4/x^2}\\
&=\frac31=3.
\end{aligned}
$$

### Fatoração:

$$
\lim_{x \to 1} \frac{x^2 - 1}{x - 1} = \lim_{x \to 1} \frac{(x+1)(x-1)}{x - 1} = \lim_{x \to 1} [x + 1] = 2
$$

### Substituição de Variáveis:

Com $h=x-1$, temos $h\to0$ quando $x\to1$:

$$
\begin{aligned}
\lim_{x\to1}\frac{x^2-1}{x-1}
&=\lim_{h\to0}\frac{(h+1)^2-1}{h}\\
&=\lim_{h\to0}\frac{h^2+2h}{h}\\
&=\lim_{h\to0}(h+2)=2.
\end{aligned}
$$

### Racionalização:

$$
\begin{aligned}
\lim_{x\to0}\frac{\sqrt{x^2+9}-3}{x^2}
&=\lim_{x\to0}\frac{(\sqrt{x^2+9}-3)(\sqrt{x^2+9}+3)}{x^2(\sqrt{x^2+9}+3)}\\
&=\lim_{x\to0}\frac{x^2+9-9}{x^2(\sqrt{x^2+9}+3)}\\
&=\lim_{x\to0}\frac1{\sqrt{x^2+9}+3}\\
&=\frac1{\sqrt9+3}=\frac16.
\end{aligned}
$$

A ideia é multiplicar por uma expressão conveniente para formar um produto notável e simplificar a indeterminação. Essa manipulação não se limita a raízes quadradas.

### Teorema do Confronto (Sanduíche):

Mesmo que ainda não saibamos calcular o limite de $f(x)$, podemos compará-la com outras duas funções. Se $g(x)\le f(x)\le h(x)$ para os pontos suficientemente próximos de $a$, exceto possivelmente o próprio $a$, e **as duas funções externas tendem ao mesmo valor**, a função intermediária também tende a esse valor:

$$
\lim_{x\to a}g(x)=\lim_{x\to a}h(x)=L
\quad\Rightarrow\quad
\lim_{x\to a}f(x)=L.
$$

Para calcular $\lim_{x\to0}x^2\sin(1/x)$, mesmo que $\lim_{x\to0}\sin(1/x)$ não exista, tem-se que:

$$
-1\le\sin\left(\frac1x\right)\le1
\quad\Rightarrow\quad
-x^2\le x^2\sin\left(\frac1x\right)\le x^2.
$$

Como os limites de $-x^2$ e $x^2$ são zero, a função fica “espremida” entre valores que se aproximam de zero. Logo:

$$
\lim_{x\to0}x^2\sin\left(\frac1x\right)=0.
$$

![Teorema do confronto para x^2 sen(1/x)](images/screenshot003.png)  
*Fonte: Elaborado pelo autor (2025).*

<!-- Na imagem original, o ponto preenchido na origem deve ser removido ou explicado como extensão contínua: x² sen(1/x) não está definida em x = 0. -->

## Teorema do Valor Intermediário (TVI):

Se $f$ for uma função contínua em um intervalo $[a, b]$, se $d$ está entre $f(a)$ e $f(b)$, então existe um valor $c$, tal que $f(c) = d$.

$$
\min\{f(a),f(b)\}\le d\le\max\{f(a),f(b)\}
\quad\Rightarrow\quad
\exists c\in[a,b]:\ f(c)=d.
$$

## Formalizando o Conceito de Limite:

$$
\forall \varepsilon > 0\ \exists \delta > 0 \text{ tal que, }\forall x \in D_f \text{: }
0 < |x-a| < \delta \Rightarrow |f(x) - L| < \varepsilon
$$

![Relação entre épsilon, delta e a proximidade do limite](images/screenshot004.png)  
*Fonte: Elaborado pelo autor (2025).*

<!-- Antes da captura, trocar o rótulo L = f(a) por L. As duas margens verticais devem representar a mesma distância épsilon até L. -->

**Pode-se tornar o valor de $f(x)$ arbitrariamente próximo de $L$ aproximando $x$ de $a$, sem exigir $x=a$.** Para cada margem de erro $\varepsilon>0$ escolhida em $y$, existe uma distância $\delta>0$ em $x$ que garante essa proximidade. O limite $L$ não precisa ser igual a $f(a)$.

---

# 02. Derivadas

## Derivada em um Ponto:

A taxa de crescimento de uma função qualquer em 2 pontos pode ser obtida por $\displaystyle \frac{\Delta y}{\Delta x} =  \frac{y_2 - y_1}{x_2 - x_1}$, sendo esse o coeficiente angular de uma reta secante ao gráfico de $f$ nos pontos $(x_1, f(x_1))$, $(x_2, f(x_2))$.

Mantendo um ponto fixo e aproximando o outro dele, quando a razão possui limite finito, encontra-se o coeficiente angular de uma reta tangente ao gráfico de $f$ no ponto $(a, f(a))$, sendo essa a derivada (taxa de variação instantânea).

$$
\left.\frac{dy}{dx}\right|_{x=a}
=\left.\frac{d}{dx}f(x)\right|_{x=a}
=f'(a)
=\lim_{x\to a}\frac{f(x)-f(a)}{x-a}.
$$

Se $h=x-a$, então $h\to0$ quando $x\to a$, e a mesma definição fica:

$$
f'(a)=\lim_{h\to0}\frac{f(a+h)-f(a)}h.
$$

Para $f(x) = x^3$, a taxa de variação entre $x_1 = 1$, $x_2 = 3$, e a derivada em $x = 2$ são:

$$
\frac{\Delta y}{\Delta x} = \frac{27-1}{3-1}=\frac{26}{2}=13
$$

$$
\displaystyle \frac{dy}{dx}\Big|_{x = 2} = \lim_{x\to2}\frac{x^3-2^3}{x-2}=\lim_{x\to2}\frac{(x-2)(x^2+2x+4)}{x-2}=\lim_{x\to2}[x^2+2x+4]=12
$$

![Retas secante e tangente ao gráfico de x³](images/screenshot005.png)  
*Fonte: Elaborado pelo autor (2025).*

### Diferenciabilidade:

Uma função $f$, diferenciável em um ponto interior $a$ do domínio, tem as seguintes características:

- **Valor no ponto:** $f(a)$ existe.
- **Continuidade no ponto:** $\lim_{x\to a}f(x)=f(a)$.
- **Derivadas laterais:** $f'_-(a)$ e $f'_+(a)$ existem, são finitas e iguais.

A ideia de não ter uma “quina” ajuda a visualizar a terceira condição. Entretanto, continuidade e ausência de quina, sozinhas, não garantem uma derivada finita: uma tangente vertical também exige cuidado.

### Derivada Como uma Função:

Ao invés de considerar a derivada de uma função em um ponto específico é considerado um ponto qualquer.

Para $f(x)=x^2$:

$$
\begin{aligned}
f'(a)&=\lim_{x\to a}\frac{x^2-a^2}{x-a}\\
&=\lim_{x\to a}\frac{(x+a)(x-a)}{x-a}\\
&=\lim_{x\to a}(x+a)=2a.
\end{aligned}
$$

Como $a$ é um ponto qualquer, escrevemos $f'(x)=2x$, para todo $x\in\mathbb R$.

## Aproximação Linear:

$$
f'(x)=\lim_{h\to0}\frac{f(x+h)-f(x)}h.
$$

Para $h$ pequeno e diferente de zero:

$$
f'(x)\approx\frac{f(x+h)-f(x)}h
\quad\Rightarrow\quad
f(x+h)\approx f(x)+hf'(x).
$$

Para valores de $h$ pequenos, a variação da função é praticamente igual ao valor correspondente na reta tangente.

![Aproximação linear de sen(x) em x = 1](images/screenshot006.png)  
*Fonte: Elaborado pelo autor (2025).*

A diferença dos valores é aproximadamente $0{,}04$. Perto do ponto de tangência, a aproximação acompanha a função; neste exemplo, reduzir $h$ melhora a precisão.

## Propriedades das Derivadas:

Operações entre derivadas que podem ser generalizadas. As funções envolvidas devem ser diferenciáveis nos pontos considerados, e $\alpha$ é constante. As [deduções estão no apêndice](#deducoes-regras).

$$
\frac{d}{dx}[f(x)+g(x)]=f'(x)+g'(x)
$$

$$
\frac{d}{dx}[\alpha f(x)]=\alpha f'(x)
$$

### Regra do Produto:

$$
\frac{d}{dx}[f(x)g(x)]=f'(x)g(x)+f(x)g'(x)
$$

### Regra do Quociente:

Para $g(x)\ne0$:

$$
\frac{d}{dx}\frac{f(x)}{g(x)}=\frac{f'(x)g(x)-f(x)g'(x)}{g^2(x)}
$$

### Regra da Cadeia:

$$
\frac{d}{dx}f(g(x))=f'(g(x))g'(x)
$$

## Derivada de Funções Inversas:

Considerando funções inversas, nos respectivos domínios, com $f(g(x)) = g(f(x)) = x$, tais quais $x^3$ e $\displaystyle \sqrt[3]{x}$, $e^x$ e $\ln{x}$, $\sin x$ e $\arcsin x$ (restringindo o seno a $[-\pi/2,\pi/2]$), etc. A fórmula abaixo vale nos pontos em que a função tem inversa diferenciável; em particular, exige $f'(f^{-1}(x))\ne0$.

$$
f(f^{-1}(x))=x
\quad\Rightarrow\quad
f'(f^{-1}(x))(f^{-1})'(x)=1.
$$

Assim:

$$
(f^{-1})'(x)=\frac1{f'(f^{-1}(x))}.
$$

Considerando $\displaystyle f^{-1}(x) = \ln x \Rightarrow f(x) = e^x$, sendo $f'(x) = e^x$: $\displaystyle \dfrac{d}{dx} \ln x = \dfrac{1}{e^{\ln{x}}} = \frac{1}{x}$.

## Derivadas Importantes:

Algumas das derivadas mais comuns e importantes, sendo base para o cálculo de funções mais complexas. As [deduções estão no apêndice](#deducoes-derivadas).

| Função | Derivada | Condições |
|---|---|---|
| $c$ | $0$ | $c$ constante real. |
| $\sin x$ | $\cos x$ | Ângulo em radianos. |
| $\cos x$ | $-\sin x$ | Ângulo em radianos. |
| $a^x$ | $a^x\ln a$ | $a>0$. |
| $e^x$ | $e^x$ | $x\in\mathbb R$. |
| $\log_a x$ | $1/(x\ln a)$ | $x>0$, $a>0$ e $a\ne1$. |
| $\ln x$ | $1/x$ | $x>0$. |

### Regra do Tombo:

$$
\frac{d}{dx}x^n=nx^{n-1}.
$$

O expoente “desce” multiplicando e diminui uma unidade. Para expoentes reais, a regra vale em $x>0$; em outros pontos, é preciso respeitar o domínio e a diferenciabilidade da potência. Para expoentes inteiros positivos, vale em toda a reta. O caso constante $x^0=1$ tem derivada zero.

## Derivada de Ordem Superior:

Trata-se apenas do processo de realizar a derivada de uma função que já tinha sido derivada anteriormente.

$$
\frac{d}{dx} \left( \frac{d}{dx} f(x) \right) = \frac{d^2}{dx^2}f(x) = f''(x) \text{ ou } f^{(2)}(x)
$$

$$
\frac{d}{dx} \left( \frac{d}{dx} \left( \cdots \left( \frac{d}{dx} f(x) \right) \right) \right) = \frac{d^n }{dx^n}f(x) = f^{(n)}(x)
$$

$$
\begin{aligned}
\frac{d^3}{dx^3}\sin x
&=\frac{d}{dx}\left[\frac{d}{dx}\left(\frac{d}{dx}\sin x\right)\right]\\
&=\frac{d}{dx}\left(\frac{d}{dx}\cos x\right)\\
&=\frac{d}{dx}(-\sin x)=-\cos x.
\end{aligned}
$$

## Derivada de uma Função Implícita:

Em uma função explícita, a variável dependente $y$ está expressa diretamente em termos da variável independente $x$, $y = f(x)$, enquanto em uma relação implícita não é necessário ou não é fácil isolar $y$ explicitamente como função de $x$, tendo-se a relação $F(x,y) = 0$.

O processo para achar a taxa de variação de uma variável em relação à outra é basicamente derivando tudo e manipulando algebricamente a parte referente à variável de interesse até achar o resultado.

Com $F(x,y) = x^2 + y^2 - 25 = 0$, para achar $\dfrac{dy}{dx}$:

$$
\frac{d}{dx}(x^2+y^2-25)=\frac{d}{dx}0
$$

$$
\frac{d}{dx}x^2+\frac{d}{dx}y^2-\frac{d}{dx}25=0
\quad\Rightarrow\quad
2x+\frac{d}{dx}y^2=0.
$$

Como $y$ depende de $x$, aplicamos a regra da cadeia: $\frac{d}{dx}[y(x)^2]=2y\frac{dy}{dx}$. Assim:

$$
2x + 2y \dfrac{dy}{dx} = 0 \Rightarrow 2y \dfrac{dy}{dx} = -2x \Rightarrow \dfrac{dy}{dx} = -\dfrac{2x}{2y} = -\dfrac{x}{y},\quad y\ne0
$$

![Tangentes horizontal e vertical à circunferência x² + y² = 25](images/screenshot007.png)  
*Fonte: Elaborado pelo autor (2025).*

<!-- Antes da captura, corrigir a anotação da tangente vertical em (5,0): dy/dx não existe como número real finito nesse ponto. Remover a referência incorreta a x = 0⁺. -->

Em $(0,5)$, a inclinação é zero. Em $(5,0)$, a tangente é vertical e $dy/dx$ não existe como número real finito.

## Derivação Logarítmica:

Dada uma função muito complexa, tal qual $\displaystyle f(x) = y = \frac{x^3 \sqrt{x^2 + 1}}{(3x + 2)^5}$, para encontrar $f'(x)$ teriam de ser usadas a regra do produto, a regra do quociente e a regra da cadeia, tornando-se um processo extremamente longo.

Uma abordagem interessante pode ser trabalhar com logaritmos. Neste desenvolvimento, consideramos $x>0$, de modo que todos os logaritmos escritos estejam definidos. Pela regra da cadeia:

$$
\dfrac{d}{dx} \ln y = \frac{dy}{dx} \dfrac{d}{dy} \ln y = \frac{dy}{dx} \dfrac{1}{y} \Rightarrow \frac{dy}{dx} = y\,\frac{d}{dx}\ln y
$$

$$
\begin{aligned}
\ln y
&=\ln\left(\frac{x^3\sqrt{x^2+1}}{(3x+2)^5}\right)\\
&=\ln(x^3)+\ln\sqrt{x^2+1}-\ln((3x+2)^5)\\
&=3\ln x+\frac12\ln(x^2+1)-5\ln(3x+2).
\end{aligned}
$$

$$
\frac{d}{dx} \ln y = \frac{d}{dx} \left[ 3\ln x + \frac{1}{2} \ln(x^2 + 1) - 5 \ln(3x + 2) \right] = \frac{3}{x} + \frac{x}{x^2 + 1} - \frac{15}{3x+2}
$$

Como $\dfrac{dy}{dx} = y \dfrac{d}{dx} \ln y$:

$$
\frac{d}{dx} \left[ \dfrac{x^3 \sqrt{x^2 + 1}}{(3x + 2)^5} \right] = \dfrac{x^3 \sqrt{x^2 + 1}}{(3x + 2)^5}\left( \frac{3}{x} + \frac{x}{x^2 + 1} - \frac{15}{3x+2}\right)
$$

## Diferenciais:

Relembrando a aproximação linear:

$$
f(x+h) - f(x) \approx h f'(x)
$$

$h$ pode ser substituído por $\Delta x$, ficando:

$$
f(x+\Delta x) - f(x) \approx \Delta x\,f'(x) \Rightarrow \Delta y \approx \frac{dy}{dx} \, \Delta x \Rightarrow \frac{\Delta y}{\Delta x} \approx \frac{dy}{dx}.
$$

Escolhemos $dx=\Delta x$ e definimos $dy=f'(x)\,dx$. Assim, $dy$ é a variação na reta tangente, enquanto $\Delta y$ é a variação real da função. Para incrementos pequenos, $\Delta y\approx dy$.

Isso faz sentido, já que $\displaystyle \frac{\Delta y}{\Delta x} \approx \frac{dy}{dx}$ para $\Delta x$ próximo de 0.

Portanto, para encontrar um valor na função, ao invés de seguir o caminho por $f$, seguimos pela reta tangente a um ponto conhecido ($y + \Delta y \approx y + dy$).

![Diferença entre a variação real Δy e o diferencial dy](images/screenshot008.png)  
*Fonte: Elaborado pelo autor (2025).*

Dessa maneira é possível aproximar funções que seriam complicadas de calcular.

Tendo $\displaystyle f(x) = \sqrt{x} \Rightarrow f'(x) = \dfrac{1}{2\sqrt{x}}$, tem-se, por exemplo, $\displaystyle f(4) = \sqrt{4} =2$. $f(4{,}02)=2{,}004993\dots$, sendo difícil de calcular manualmente, enquanto, por diferenciais, $\displaystyle f(4{,}02) \approx \sqrt{4} + \dfrac{0{,}02}{2\sqrt{4}} = 2 + \frac{0{,}02}{4} = 2+0{,}005 = 2{,}005$ (uma ótima aproximação, visto que o erro é menor que $0{,}000006$).

## Aplicações das Derivadas:

### Regra de L’Hôpital:

É uma ferramenta que permite resolver limites indeterminados do tipo $0/0$ ou $\infty/\infty$ de maneira simples. Derivamos o numerador e o denominador separadamente:

$$
\lim_{x\to a}\frac{f(x)}{g(x)}=\lim_{x\to a}\frac{f'(x)}{g'(x)}.
$$

Para aplicar a regra, $f$ e $g$ devem ser diferenciáveis perto do ponto considerado, exceto possivelmente no próprio ponto, com $g'(x)\ne0$ nessa região. É necessário que exista o limite da razão das derivadas, finito ou infinito. A regra também pode ser aplicada a limites laterais e no infinito, nas condições correspondentes.

**Caso de $0/0$.** Pode-se utilizar a intuição dada pelos diferenciais: perto de um ponto em que as duas funções se anulam, suas variações são aproximadas pelas respectivas retas tangentes. Comparar essas variações sugere comparar as derivadas. Essa é uma motivação intuitiva; a validade da regra depende das condições acima.

$$
\lim_{x\to0}\frac{\sin x}{x}
=\lim_{x\to0}\frac{\cos x}{1}=1.
$$

O exemplo parte da forma $0/0$. A dedução da derivada do seno, no apêndice, obtém esse limite por geometria, sem depender de L’Hôpital.

**Caso de $\infty/\infty$.** É bastante intuitivo comparar o crescimento de duas funções. Quando uma se torna cada vez maior em proporção à outra, a razão pode tender ao infinito; a razão inversa, a zero.

Para $\lim_{x\to\infty}\ln(x)/x$, uma análise simples mostra:

$$
\frac{\ln10}{10}\approx0{,}230,\qquad
\frac{\ln100}{100}\approx0{,}046,
$$

$$
\frac{\ln1000}{1000}\approx0{,}007,\qquad
\frac{\ln1000000}{1000000}\approx1{,}38\cdot10^{-5}.
$$

$x$ cresce muito mais rápido que $\ln x$ quando $x\to\infty$. Mesmo que ambas tendam ao infinito, a razão $\ln(x)/x$ tende a zero:

$$
\lim_{x\to\infty}\frac{\ln x}{x}
=\lim_{x\to\infty}\frac{1/x}{1}=0.
$$

**Outras indeterminações.** Através de manipulação algébrica é possível transformar outras formas em um quociente adequado à regra. No exemplo abaixo, reunimos as frações e obtemos $0/0$:

$$
\lim_{x\to0}\left(\frac1x-\frac1{\sin x}\right)
=\lim_{x\to0}\frac{\sin x-x}{x\sin x}.
$$

A primeira aplicação de L’Hôpital ainda produz $0/0$. Aplicando a regra novamente:

$$
\begin{aligned}
\lim_{x\to0}\frac{\sin x-x}{x\sin x}
&=\lim_{x\to0}\frac{\cos x-1}{\sin x+x\cos x}\\
&=\lim_{x\to0}\frac{-\sin x}{2\cos x-x\sin x}=0.
\end{aligned}
$$

### Crescimento e Decrescimento:

A primeira derivada indica a taxa de variação instantânea. Em um ponto $a$, se $f'(a)<0$, essa taxa é negativa; se $f'(a)=0$, ela é nula; e, se $f'(a)>0$, é positiva.

Para determinar o comportamento em um intervalo, analisamos o sinal da derivada nesse intervalo: $f'(x)>0$ indica que a função é **crescente**, enquanto $f'(x)<0$ indica que ela é **decrescente**. Ter derivada zero em um ponto não significa que a função seja constante ao redor dele.

### Concavidade e Inflexão:

A segunda derivada permite analisar a concavidade. Em um intervalo, se $f''(x)>0$, a função é **convexa**, com concavidade para cima; se $f''(x)<0$, a concavidade é para baixo.

Um **ponto de inflexão** é um ponto em que a concavidade muda. Encontrar $f''(a)=0$ não basta para concluir que há inflexão: em $f(x)=x^4$, temos $f''(0)=12\cdot0^2=0$, mas a concavidade continua para cima dos dois lados. Uma função afim também tem segunda derivada zero, sem mudança de concavidade.

### Máximos e Mínimos:

Um **máximo local** é um valor da função maior ou igual aos valores próximos; um **mínimo local** é menor ou igual aos valores próximos. Se o extremo ocorre em um ponto interior $a$ no qual a função é diferenciável, então $f'(a)=0$.

Quando $f'(a)=0$ e a função é duas vezes diferenciável perto de $a$, podemos usar o teste da segunda derivada:

| Resultado | Conclusão |
|---|---|
| $f''(a)<0$ | Máximo local. |
| $f''(a)>0$ | Mínimo local. |
| $f''(a)=0$ | Teste inconclusivo. |

O teste não é uma condição obrigatória para existir extremo. A função $x^4$, por exemplo, tem mínimo em zero, embora sua segunda derivada seja zero nesse ponto. Também podem existir extremos em pontos onde a derivada não existe.

Para ser um **máximo ou mínimo global**, o valor deve ser o maior ou o menor em todo o domínio considerado. Se a função é contínua em um intervalo fechado $[a,b]$, esses extremos existem. Para encontrá-los, comparamos os valores nos candidatos interiores — onde a derivada é zero ou não existe — e nas extremidades $f(a)$ e $f(b)$.

![Máximos, mínimos e ponto de inflexão](images/screenshot009.png)  
*Fonte: ilustração da apostila original.*

### Assíntotas:

Trata-se do comportamento que algumas funções têm de se aproximarem de uma reta para certos valores.

**Horizontais:**

A reta $y=c$ é uma assíntota horizontal quando $\lim_{x\to+\infty}f(x)=c$ ou $\lim_{x\to-\infty}f(x)=c$, com $c\in\mathbb R$. Cada direção é analisada separadamente.

$\displaystyle \lim_{x \to -\infty} 2+\frac{1}{x} = \lim_{x \to \infty} 2+\frac{1}{x} = 2$, logo, $f$ tem uma assíntota horizontal em $y=2$.

![Assíntota horizontal de 2 + 1/x](images/screenshot010.png)  
*Fonte: ilustração da apostila original.*

**Verticais:**

A reta $x=c$ é uma assíntota vertical quando pelo menos um dos limites laterais de $f(x)$ em $c$ é $+\infty$ ou $-\infty$.

$\displaystyle \lim_{x \to (\pi/2)^-} \tan x = \infty$ e $\displaystyle \lim_{x \to (\pi/2)^+} \tan x = -\infty$, logo, $f$ tem uma assíntota vertical em $x = \pi/2$.

![Assíntota vertical da tangente em π/2](images/screenshot011.png)  
*Fonte: ilustração da apostila original.*

**Oblíquas:**

Quando uma função $f$ se aproxima de uma reta $g(x)=ax+b$, com $a\ne0$, de modo que $\lim_{x\to\infty}[f(x)-g(x)]=0$, essa reta é uma assíntota oblíqua. A mesma análise pode ser feita em $x\to-\infty$.

Para encontrá-la, podemos escrever $f(x)=m(x)+h(x)$, com $m(x)$ afim, e verificar se o restante $h(x)$ tende a zero. Assim, $f(x)\approx m(x)$ para valores suficientemente grandes de $x$.

Sendo $\displaystyle f(x) = \frac{2x^2-3x-\ln{x}}{x+1}$, para valores $x$ muito grandes:

$$
\frac{2x^2-3x-\ln{x}}{x+1} =
\frac{2x^2-3x}{x+1} - \frac{\ln{x}}{x+1} \approx \frac{2x^2-3x}{x+1}=2x-5+\frac{5}{x+1}\approx2x-5
$$

Como $\displaystyle \lim_{x\to \infty}\left[\frac{2x^2-3x-\ln{x}}{x+1}-(2x-5)\right] = 0$, então $2x-5$ é uma assíntota oblíqua de $f$.

![Assíntota oblíqua y = 2x − 5](images/screenshot012.png)  
*Fonte: Elaborado pelo autor (2025).*

---

# 03. Integrais

## Primitivas:

Encontrar primitivas é o processo inverso de derivar: $F$ é primitiva de $f$ em um intervalo se:

$$
F'(x)=f(x)\quad\Longleftrightarrow\quad\int f(x)\,dx=F(x)+c.
$$

A constante $c$ representa o termo constante que é perdido na derivada.

$$
\frac{d}{dx}(2x+4)=2
\quad\Longleftrightarrow\quad
\int2\,dx=2x+c.
$$

Para recuperar a mesma função, basta assumir $c=4$, obtendo $2x+4$. Toda função contínua em um intervalo possui uma primitiva nesse espaço.

### Primitivas Comuns:

Realizando o processo inverso do cálculo da derivada:

| Integral | Primitiva | Condições |
|---|---|---|
| $\int a\,dx$ | $ax+c$ | $a$ constante. |
| $\int\sin x\,dx$ | $-\cos x+c$ | Ângulo em radianos. |
| $\int\cos x\,dx$ | $\sin x+c$ | Ângulo em radianos. |
| $\int a^x\,dx$ | $a^x/\ln a+c$ | $a>0$ e $a\ne1$. |
| $\int e^x\,dx$ | $e^x+c$ | $x\in\mathbb R$. |
| $\int\frac{dx}{x}$ | $\ln\lvert x\rvert+c$ | Em intervalos que não contenham zero. |
| $\int\frac{dx}{x\ln a}$ | $\frac{\ln\lvert x\rvert}{\ln a}+c=\log_a\lvert x\rvert+c$ | $x\ne0$, $a>0$ e $a\ne1$. |
| $\int x^n\,dx$ | $\frac{x^{n+1}}{n+1}+c$ | $n\ne-1$, em intervalos onde a potência e a fórmula estejam definidas. |

### Propriedades:

A integral permite separar somas e colocar fatores constantes em evidência:

$$
\int \alpha f(x)\,dx=\alpha\int f(x)\,dx
$$

$$
\int[f(x)+g(x)]\,dx=\int f(x)\,dx+\int g(x)\,dx.
$$

As constantes das primitivas são reunidas em uma constante final $c$. No caso $\alpha=0$, a integral é diretamente $\int0\,dx=c$.

Não podemos integrar produtos, quocientes ou composições simplesmente integrando cada parte separadamente. Para integrais de maior complexidade, são necessários métodos como substituição e integração por partes, que aproveitam as regras da cadeia e do produto da derivação.

## Integrais Definidas:

As integrais são ferramentas que permitem calcular a **área com sinal** entre o gráfico e o eixo $x$ no intervalo $a\le x\le b$: acima do eixo, as contribuições são positivas; abaixo, são negativas.

Essa lógica vem da aproximação através da soma de retângulos de largura $\Delta x=(b-a)/n$ e altura $f(x_i)$, em que $x_i$ é um ponto escolhido no respectivo subintervalo:

$$
A\approx\sum_{i=1}^n f(x_i)\Delta x.
$$

Para uma função contínua, aumentando o número de retângulos, suas larguras diminuem e a soma se aproxima da integral:

$$
A=\lim_{n\to\infty}\sum_{i=1}^n f(x_i)\Delta x=\int_a^b f(x)\,dx.
$$

> A área geométrica total não considera parcelas negativas. Por isso, quando o gráfico cruza o eixo, a integral pode ser diferente dessa área total.

![Aproximação da integral definida por retângulos](images/screenshot013.png)  
*Fonte: Elaborado pelo autor (2025).*

### Teorema Fundamental do Cálculo:

Relaciona integrais definidas e primitivas. Se $f$ é contínua em $[a,b]$ e $F$ é uma primitiva de $f$, então ([dedução no apêndice](#deducao-tfc)):

$$
\int_a^b f(x)\,dx = F(b) - F(a)
$$

### Integrais Impróprias:

São integrais em que o intervalo é infinito ou a função se torna ilimitada perto de algum ponto do intervalo. Nesses casos, o cálculo é definido por limites.

**Intervalo infinito.** Se apenas uma extremidade é infinita:

$$
\int_a^{\infty}f(x)\,dx
=\lim_{b\to\infty}\int_a^b f(x)\,dx
=\lim_{b\to\infty}[F(b)-F(a)].
$$

$$
\int_{-\infty}^b f(x)\,dx
=\lim_{a\to-\infty}\int_a^b f(x)\,dx
=\lim_{a\to-\infty}[F(b)-F(a)].
$$

Para integrar de $-\infty$ a $+\infty$, escolhemos um ponto finito $c$ e separamos:

$$
\int_{-\infty}^{\infty}f(x)\,dx
=\int_{-\infty}^c f(x)\,dx+\int_c^{\infty}f(x)\,dx.
$$

**As duas integrais precisam convergir separadamente para valores finitos.** Uma parte infinita não pode ser compensada pela outra.

**Função ilimitada.** Se o problema está em um ponto interior $c\in(a,b)$, separamos os dois lados:

$$
\int_a^b f(x)\,dx
=\lim_{u\to c^-}\int_a^u f(x)\,dx
+\lim_{v\to c^+}\int_v^b f(x)\,dx.
$$

Cada limite deve existir e ser finito. Se o problema estiver apenas em uma extremidade, usamos somente o limite correspondente, aproximando-nos por dentro do intervalo.

> Nem toda descontinuidade torna uma integral imprópria. Um salto finito ou um ponto isolado sem valor definido não significa, por si só, que a função cresça sem limite.

## Métodos de Integração:

### Substituição de Variáveis:

A substituição utiliza a regra da cadeia no sentido inverso:

$$
\frac{d}{dx}f(g(x))=f'(g(x))g'(x)
\quad\Rightarrow\quad
\int f'(g(x))g'(x)\,dx=f(g(x))+c.
$$

Para resolver $\int x\sqrt{x^2+1}\,dx$, fazemos $u=x^2+1$ e $du=2x\,dx$. Assim, $x\,dx=du/2$:

$$
\begin{aligned}
\int x\sqrt{x^2+1}\,dx
&=\frac12\int u^{1/2}\,du\\
&=\frac12\frac{u^{3/2}}{3/2}+c\\
&=\frac{(x^2+1)^{3/2}}3+c.
\end{aligned}
$$

### Integral por Partes:

$$
\frac{d}{dx} [f(x)\,g(x)] = f'(x)\,g(x) + f(x)\,g'(x) \Rightarrow
\int\frac{d}{dx} [f(x)\,g(x)] \, dx = \int f'(x)\,g(x)\, dx + \int f(x)\,g'(x) \, dx
$$

$$
f(x)\,g(x) = \int f'(x)\,g(x)\, dx + \int f(x)\,g'(x) \, dx \Rightarrow
\int f(x)\,g'(x) \, dx=f(x)\,g(x) - \int f'(x)\,g(x)\, dx
$$

De maneira simplificada, considerando $f(x) = u$, $f'(x)\,dx = du$, $g(x) = v$ e $g'(x)\,dx = dv$:

$$
\int u\,dv =u\,v -\int v\,du
$$

Se $u=x^3$, $du=3x^2\,dx$, $dv=e^x\,dx$ e $v=e^x$:

$$
\int x^3e^x\,dx=x^3e^x-\int3x^2e^x\,dx
=x^3e^x-3\int x^2e^x\,dx.
$$

Repetindo o processo com $u=x^2$, $du=2x\,dx$, $dv=e^x\,dx$ e $v=e^x$:

$$
\int x^3e^x\,dx
=x^3e^x-3\left(x^2e^x-2\int xe^x\,dx\right).
$$

Por fim, com $u=x$, $du=dx$, $dv=e^x\,dx$ e $v=e^x$:

$$
\int x^3e^x\,dx
=x^3e^x-3\left[x^2e^x-2\left(xe^x-\int e^x\,dx\right)\right].
$$

$$
\displaystyle \int x^3 e^x \, dx = x^3 e^x -3x^2 e^x + 6x\,e^x - 6 e^x + c = e^x (x^3 - 3x^2 + 6x - 6) + c
$$

Um método eficiente de resolver integrais por partes repetidas é montar duas colunas: derivamos sucessivamente a expressão escolhida como $u$ e integramos sucessivamente a expressão que acompanha $dx$ em $dv$. Multiplicamos os termos pelas diagonais, alternando os sinais + e −. Quando a coluna das derivadas chega a zero, o processo termina, como no exemplo abaixo.

$$
\int x^3 e^x \, dx
$$

![Integração por partes pelo método tabular para x³ eˣ](images/screenshot014.png)  
*Fonte: Elaborado pelo autor (2025).*

<!-- As colunas representam derivadas sucessivas de u e primitivas sucessivas da expressão de dv; os diferenciais são omitidos no esquema. -->

$$
\int x^3 e^x \, dx = x^3e^x - 3x^2e^x + 6xe^x - 6e^x + \int 0\cdot e^x \,dx = e^x (x^3 - 3x^2 + 6x - 6) + c
$$

Nesse caso, a integração por partes chegava a uma integral conhecida. Em outros casos, a integral original reaparece. Quando obtemos esse termo repetido, podemos isolá-lo para resolver a equação.

$$
\int e^x \sin{x} \, dx
$$

![Integração por partes com reaparecimento da integral de eˣ sen(x)](images/screenshot015.png)  
*Fonte: Elaborado pelo autor (2025).*

<!-- As colunas representam derivadas sucessivas de u e primitivas sucessivas da expressão de dv; os diferenciais são omitidos no esquema. -->

$$
\int e^x \sin{x} \, dx=e^x\sin{x}-e^x\cos{x}-\int e^x\sin{x} \,dx \Rightarrow 2\int e^x \sin{x} \, dx=e^x\sin{x}-e^x\cos{x} + c
$$

$$
\int e^x \sin{x} \, dx=\frac{e^x(\sin{x}-\cos{x})}{2} + c
$$

### Integrais Trigonométricas:

O método depende dos fatores e dos expoentes presentes. Nos casos de paridade abaixo, os expoentes são inteiros não negativos.

**Uma potência acompanhada da derivada da função.** Para as formas:

$$
\int\sin^m x\cos x\,dx
\qquad\text{ou}\qquad
\int\cos^m x\sin x\,dx,
$$

substituímos $u=\sin x$, com $du=\cos x\,dx$, ou $u=\cos x$, com $du=-\sin x\,dx$, respectivamente.

**Potências pares de seno ou cosseno.** Para $\int\sin^m x\,dx$ ou $\int\cos^m x\,dx$, com $m$ par, usamos:

$$
\sin^2 x=\frac{1-\cos(2x)}2,
\qquad
\cos^2 x=\frac{1+\cos(2x)}2.
$$

**Potências ímpares de seno ou cosseno.** Se $m$ é ímpar, separamos um fator de seno ou cosseno e usamos $\sin^2 x+\cos^2 x=1$, transformando a integral no primeiro caso.

**Produto de potências.** Em $\int\sin^m x\cos^n x\,dx$, se algum expoente for ímpar, separamos um fator da função correspondente e usamos $\sin^2 x+\cos^2 x=1$. Se ambos forem pares, usamos as identidades de redução de potência acima.

**Produtos com argumentos diferentes.** Para $\sin(ax)\sin(bx)$, $\sin(ax)\cos(bx)$ ou $\cos(ax)\cos(bx)$, usamos as identidades de produto em soma:

$$
\sin A\sin B=\frac12[\cos(A-B)-\cos(A+B)]
$$

$$
\cos A\cos B=\frac12[\cos(A-B)+\cos(A+B)]
$$

$$
\sin A\cos B=\frac12[\sin(A+B)+\sin(A-B)].
$$

**Potências de tangente ou secante.** Para $\int\tan^m x\,dx$ ou $\int\sec^n x\,dx$, usamos $\tan^2 x+1=\sec^2 x$ e/ou integração por partes, a depender do caso.

### Substituição Trigonométrica:

Com as identidades $\sin^2\theta+\cos^2\theta=1$ e $\tan^2\theta+1=\sec^2\theta$, utilizamos um triângulo retângulo para relacionar a expressão da raiz a uma razão trigonométrica. No exemplo abaixo, o desenho representa $x>0$; a substituição algébrica também permite tratar os demais valores reais de $x$.

$$
\int \frac{dx}{\sqrt{4 + x^2}}
$$

![Triângulo de referência para a substituição x = 2 tan(θ)](images/screenshot016.png)  
*Fonte: Elaborado pelo autor (2025).*

Analisando o triângulo:

$$
\tan\theta=\frac x2
\quad\Rightarrow\quad
x=2\tan\theta,\qquad dx=2\sec^2\theta\,d\theta.
$$

$$
\begin{aligned}
\int\frac{dx}{\sqrt{4+x^2}}
&=\int\frac{2\sec^2\theta}{\sqrt{4+4\tan^2\theta}}\,d\theta\\
&=\int\frac{2\sec^2\theta}{\sqrt{4(1+\tan^2\theta)}}\,d\theta.
\end{aligned}
$$

$$
\int\frac{2\sec^2\theta}{\sqrt{4\sec^2\theta}}\,d\theta
=\int\frac{2\sec^2\theta}{2\lvert\sec\theta\rvert}\,d\theta.
$$

Como $\theta=\arctan(x/2)$, temos $-\pi/2<\theta<\pi/2$. Como, nesse intervalo, para todo $\theta$, $\sec \theta > 0$, então $|\sec \theta| = \sec \theta$. Logo:

$$
\begin{aligned}
\int\frac{\sec^2\theta}{\lvert\sec\theta\rvert}\,d\theta
&=\int\frac{\sec^2\theta}{\sec\theta}\,d\theta\\
&=\int\sec\theta\,d\theta\\
&=\int\sec\theta\frac{\sec\theta+\tan\theta}{\sec\theta+\tan\theta}\,d\theta\\
&=\int\frac{\sec^2\theta+\sec\theta\tan\theta}{\sec\theta+\tan\theta}\,d\theta.
\end{aligned}
$$

Se $u=\sec\theta+\tan\theta$, então:

$$
du=(\sec^2\theta+\sec\theta\tan\theta)\,d\theta.
$$

$$
\begin{aligned}
\int\frac{\sec^2\theta+\sec\theta\tan\theta}{u}\,
\frac{du}{\sec^2\theta+\sec\theta\tan\theta}
&=\int\frac{du}{u}\\
&=\ln\lvert u\rvert+c.
\end{aligned}
$$

Voltando às variáveis anteriores:

$$
\ln\lvert u\rvert+c
=\ln\lvert\sec\theta+\tan\theta\rvert+c
=\ln\left|\frac{\sqrt{4+x^2}}2+\frac x2\right|+c.
$$

$$
\displaystyle \int \frac{dx}{\sqrt{4 + x^2}} = \ln \left|\frac{\sqrt{4 + x^2}}{2} + \frac{x}{2}\right| + c
$$

### Frações Parciais:

Trata-se de separar uma fração com denominador polinomial em uma soma de frações mais simples.

$$
\int\frac{5x - 3}{x^2 - 2x - 3}\,dx= \int \frac{5x - 3}{(x+1)(x-3)}\,dx = \int\left(\frac{A}{x+1} + \frac{B}{x-3}\right)\,dx
$$

$$
5x - 3= A(x-3) + B(x+1) \Rightarrow (A + B - 5)x +(-3A + B +3) = 0
$$

$$
\begin{cases}
A+B=5\\
-3A+B=-3
\end{cases}
\quad\Rightarrow\quad A=2,\ B=3.
$$

Portanto:

$$
\int\left(\frac2{x+1}+\frac3{x-3}\right)\,dx
=2\ln\lvert x+1\rvert+3\ln\lvert x-3\rvert+c.
$$

Caso o polinômio do numerador $f(x)$ tenha grau maior ou igual ao do denominador $g(x)$, realizamos primeiro a divisão polinomial:

$$
\frac{f(x)}{g(x)} = m(x)+\frac{h(x)}{g(x)},\qquad \deg h<\deg g
$$

Caso exista um fator linear repetido no denominador, incluímos todas as potências até sua multiplicidade. Por exemplo:

$$
\frac{2x}{(x+1)^2}\text{, tem-se que: }\frac{2x}{(x+1)^2}=\frac{A}{x+1}+\frac{B}{(x+1)^2}
$$

Caso exista um fator quadrático irredutível nos reais, seu numerador é da forma $Bx+C$. Por exemplo:

$$
\frac{7x+2}{(x+2)(x^2+5)}\text{, tem-se que: } \frac{7x+2}{(x+2)(x^2+5)}= \frac{A}{x+2}+\frac{Bx+C}{x^2+5}
$$

---

# Apêndice A. Deduções Complementares

As deduções abaixo complementam as regras usadas na apostila. Podem ser consultadas separadamente, sem interromper a sequência dos capítulos.

<a id="deducoes-regras"></a>

## Propriedades das Derivadas:

### Soma de Derivadas:

Pela definição de derivada, separamos a variação de cada função:

$$
\begin{aligned}
\frac{d}{dx}[f(x)+g(x)]
&=\lim_{h\to0}\frac{[f(x+h)+g(x+h)]-[f(x)+g(x)]}{h}\\
&=\lim_{h\to0}\left[\frac{f(x+h)-f(x)}h+\frac{g(x+h)-g(x)}h\right]\\
&=f'(x)+g'(x).
\end{aligned}
$$

### Produto de Derivada por Escalar:

Como $\alpha$ é constante, podemos colocá-la em evidência:

$$
\begin{aligned}
\frac{d}{dx}[\alpha f(x)]
&=\lim_{h\to0}\frac{\alpha f(x+h)-\alpha f(x)}h\\
&=\alpha\lim_{h\to0}\frac{f(x+h)-f(x)}h\\
&=\alpha f'(x).
\end{aligned}
$$

### Regra do Produto:

A noção de aproximação linear, $f(x+h)\approx f(x)+hf'(x)$ para $h$ pequeno, ajuda a visualizar por que surgem duas parcelas. Para acompanhar a conta por igualdades, somamos e subtraímos o termo $f(x)g(x+h)$:

$$
\begin{aligned}
\frac{d}{dx}[f(x)g(x)]
&=\lim_{h\to0}\frac{f(x+h)g(x+h)-f(x)g(x)}h\\
&=\lim_{h\to0}\left[
\frac{f(x+h)-f(x)}h g(x+h)
+f(x)\frac{g(x+h)-g(x)}h
\right].
\end{aligned}
$$

Como $g$ é diferenciável, também é contínua: $g(x+h)\to g(x)$. Assim:

$$
\frac{d}{dx}[f(x)g(x)]=f'(x)g(x)+f(x)g'(x).
$$

### Regra do Quociente:

Com $g(x)\ne0$, reunimos as frações e reorganizamos o numerador:

$$
\begin{aligned}
\frac{d}{dx}\frac{f(x)}{g(x)}
&=\lim_{h\to0}\frac{\frac{f(x+h)}{g(x+h)}-\frac{f(x)}{g(x)}}h\\
&=\lim_{h\to0}\frac{f(x+h)g(x)-f(x)g(x+h)}{h\,g(x+h)g(x)}\\
&=\lim_{h\to0}
\frac{g(x)\frac{f(x+h)-f(x)}h-f(x)\frac{g(x+h)-g(x)}h}
{g(x+h)g(x)}\\
&=\frac{f'(x)g(x)-f(x)g'(x)}{g^2(x)}.
\end{aligned}
$$

### Regra da Cadeia:

Na composição $f(g(x))$, uma pequena mudança em $x$ altera primeiro $g$ e, em seguida, $f$. Pela aproximação linear:

$$
\Delta g=g(x+h)-g(x)\approx g'(x)h,
$$

$$
f(g(x+h))-f(g(x))\approx f'(g(x))\Delta g.
$$

Juntando as duas relações, a variação da composição é aproximada por $f'(g(x))g'(x)h$. Dividindo por $h$, obtemos a intuição para a regra:

$$
\frac{d}{dx}f(g(x))=f'(g(x))g'(x).
$$

> Essa explicação usa aproximações para mostrar a ideia da regra. Quando $f$ e $g$ são diferenciáveis nos pontos envolvidos, os erros dessas aproximações, divididos por $h$, tendem a zero; por isso o limite fornece a igualdade indicada.

<a id="deducoes-derivadas"></a>

## Derivadas Importantes:

### Constante:

Para qualquer constante real $c$:

$$
\frac{d}{dx}c=\lim_{h\to0}\frac{c-c}h=\lim_{h\to0}\frac0h=0.
$$

### Seno:

Usando a fórmula do seno da soma:

$$
\begin{aligned}
\frac{d}{dx}\sin x
&=\lim_{h\to0}\frac{\sin(x+h)-\sin x}h\\
&=\lim_{h\to0}\frac{\sin x\cos h+\cos x\sin h-\sin x}h\\
&=\sin x\lim_{h\to0}\frac{\cos h-1}h
+\cos x\lim_{h\to0}\frac{\sin h}h.
\end{aligned}
$$

Para definir esses limites, podemos usar trigonometria. Consideramos um círculo de raio 1, com um ângulo $\theta$ em radianos, e comparamos as áreas de um setor circular e de dois triângulos:

![Círculo unitário e comparação de áreas para o limite de sen(θ)/θ](images/screenshot017.png)  
*Fonte: Elaborado pelo autor (2025).*

<!-- O argumento usa o triângulo maior O-A-T. Se necessário, acrescentar o segmento O-T à captura; o segmento P-T do desenho original não é a sua hipotenusa. -->

Para $0<\theta<\pi/2$, as áreas do triângulo menor, do setor circular e do triângulo maior são, respectivamente, $\frac12\cos\theta\sin\theta$, $\frac12\theta$ e $\frac12\tan\theta$. Assim:

$$
\frac12\cos\theta\sin\theta\le\frac12\theta\le\frac12\tan\theta.
$$

Dividindo e invertendo as razões positivas:

$$
\cos\theta\le\frac{\theta}{\sin\theta}\le\frac1{\cos\theta}
\quad\Rightarrow\quad
\cos\theta\le\frac{\sin\theta}{\theta}\le\frac1{\cos\theta}.
$$

As duas funções externas tendem a 1. Pelo teorema do confronto, o limite pela direita é 1; como $\sin(-\theta)/(-\theta)=\sin\theta/\theta$, o limite pela esquerda é o mesmo:

$$
\lim_{\theta\to0}\frac{\sin\theta}{\theta}=1.
$$

Para o outro limite, racionalizamos:

$$
\begin{aligned}
\frac{\cos\theta-1}{\theta}
&=\frac{(\cos\theta-1)(\cos\theta+1)}{\theta(\cos\theta+1)}\\
&=-\frac{\sin^2\theta}{\theta(\cos\theta+1)}\\
&=-\frac{\sin\theta}{\theta}\frac{\sin\theta}{\cos\theta+1}.
\end{aligned}
$$

Logo:

$$
\lim_{\theta\to0}\frac{\cos\theta-1}{\theta}
=-1\cdot\frac02=0.
$$

Voltando à derivada:

$$
\frac{d}{dx}\sin x=\sin x\cdot0+\cos x\cdot1=\cos x.
$$

### Cosseno:

Usando a fórmula do cosseno da soma e os limites anteriores:

$$
\begin{aligned}
\frac{d}{dx}\cos x
&=\lim_{h\to0}\frac{\cos(x+h)-\cos x}h\\
&=\lim_{h\to0}\frac{\cos x\cos h-\sin x\sin h-\cos x}h\\
&=\cos x\lim_{h\to0}\frac{\cos h-1}h
-\sin x\lim_{h\to0}\frac{\sin h}h\\
&=\cos x\cdot0-\sin x\cdot1=-\sin x.
\end{aligned}
$$

### Exponencial:

Para $a>0$, colocamos $a^x$ em evidência:

$$
\frac{d}{dx}a^x
=\lim_{h\to0}\frac{a^{x+h}-a^x}h
=a^x\lim_{h\to0}\frac{a^h-1}h.
$$

Usamos aqui o limite fundamental da exponencial $\lim_{u\to0}(e^u-1)/u=1$. Tomando esse limite como conhecido, escrevemos $a^h=e^{h\ln a}$. Para $a\ne1$, com $u=h\ln a$:

$$
\begin{aligned}
\lim_{h\to0}\frac{a^h-1}h
&=\lim_{h\to0}\frac{e^{h\ln a}-1}{h\ln a}\ln a\\
&=1\cdot\ln a=\ln a.
\end{aligned}
$$

Portanto:

$$
\frac{d}{dx}a^x=a^x\ln a.
$$

Se $a=1$, a função é constante e a fórmula também fornece zero.

**Muito usada:** $\frac{d}{dx}e^x=e^x\ln e=e^x$.

### Logaritmo:

O logaritmo $\log_a x$ é a função inversa de $f(x)=a^x$, com $a>0$, $a\ne1$ e $x>0$. Aplicando a derivada da inversa:

$$
\frac{d}{dx}\log_a x
=\frac1{f'(f^{-1}(x))}
=\frac1{a^{\log_a x}\ln a}
=\frac1{x\ln a}.
$$

**Muito usada:** $\frac{d}{dx}\ln x=1/x$.

### Regra do Tombo:

A dedução pelo binômio de Newton abaixo considera $n$ inteiro positivo. Começamos com:

$$
\frac{d}{dx}x^n=\lim_{h\to0}\frac{(x+h)^n-x^n}h.
$$

O binômio de Newton permite expandir:

$$
(x+h)^n=\sum_{k=0}^{n}\binom nk x^{n-k}h^k,
\qquad
\binom nk=\frac{n!}{k!(n-k)!}.
$$

Os coeficientes são:

$$
\binom n0=\frac{n!}{0!n!}=1,
\qquad
\binom n1=\frac{n!}{1!(n-1)!}=n,
$$

$$
\binom n2=\frac{n(n-1)}2,
\qquad\cdots\qquad
\binom n{n-2}=\frac{n(n-1)}2,
$$

$$
\binom n{n-1}=n,
\qquad
\binom nn=1.
$$

Os termos com índice 2 pressupõem $n\ge2$. Aplicando a expansão à derivada:

$$
\begin{aligned}
\frac{d}{dx}x^n
&=\lim_{h\to0}
\frac{x^n+nx^{n-1}h+\binom n2x^{n-2}h^2+\cdots+h^n-x^n}h\\
&=\lim_{h\to0}
\left[nx^{n-1}+\binom n2x^{n-2}h+\cdots+h^{n-1}\right]\\
&=nx^{n-1}.
\end{aligned}
$$

Para $n=1$, a conta é diretamente $\lim_{h\to0}h/h=1$. Para expoentes reais em $x>0$, podemos usar $x^n=e^{n\ln x}$ e as regras já apresentadas:

$$
\frac{d}{dx}x^n=e^{n\ln x}\frac nx=nx^{n-1}.
$$

<a id="deducao-tfc"></a>

## Teorema Fundamental do Cálculo:

### Teorema do Valor Médio para Integrais:

Dada uma função $f$ contínua em $[a,b]$, com $a<b$, sua integral pode ser igualada à contribuição de um retângulo de base $b-a$ e altura $f(c)$, para algum $c\in[a,b]$:

$$
\int_a^b f(x)\,dx=f(c)(b-a).
$$

Essa comparação considera a área com sinal: uma altura negativa representa uma contribuição negativa.

![Teorema do valor médio para integrais e área com sinal](images/screenshot018.png)  
*Fonte: Elaborado pelo autor (2025).*

### Relação Entre Integrais e Primitivas:

Definindo $G(x)=\int_a^x f(t)\,dt$, podemos entender $G(x)$ como a área com sinal acumulada a partir de $a$. Usamos $t$ dentro da integral para distingui-lo da extremidade variável $x$:

$$
\begin{aligned}
G'(x)
&=\lim_{h\to0}\frac{G(x+h)-G(x)}h\\
&=\lim_{h\to0}
\frac{\int_a^{x+h}f(t)\,dt-\int_a^x f(t)\,dt}{h}.
\end{aligned}
$$

![Variação da área acumulada entre x e x + h](images/screenshot019.png)  
*Fonte: ilustração da apostila original.*

<!-- Na captura, preferir t como variável de integração dentro dos rótulos para distinguir a variável auxiliar da extremidade x. -->

Como visto pela imagem, a diferença corresponde ao trecho entre $x$ e $x+h$:

$$
\int_a^{x+h}f(t)\,dt-\int_a^x f(t)\,dt
=\int_x^{x+h}f(t)\,dt.
$$

De acordo com o teorema do valor médio para integrais, existe um ponto $c_h$ entre $x$ e $x+h$ tal que:

$$
\int_x^{x+h}f(t)\,dt=f(c_h)h.
$$

Assim:

$$
G'(x)=\lim_{h\to0}\frac{f(c_h)h}h=\lim_{h\to0}f(c_h).
$$

Quando $h\to0$, o intervalo em que $c_h$ está contido encolhe e $c_h\to x$, tanto para $h>0$ quanto para $h<0$. Pela continuidade de $f$, concluímos que $G'(x)=f(x)$.

Sendo $F$ uma primitiva de $f$, tem-se:

$$
\frac{d}{dx}[F(x)-G(x)]=f(x)-f(x)=0.
$$

Logo, $F(x)-G(x)$ é constante no intervalo. Chamando essa constante de $C$:

$$
F(x)-G(x)=C
\quad\Rightarrow\quad
G(x)=F(x)-C.
$$

Como $G(a)=\int_a^a f(t)\,dt=0$, temos $C=F(a)$. Portanto:

$$
G(x)=F(x)-F(a).
$$

Definindo uma extremidade $b\ge a$, obtemos:

$$
\int_a^b f(x)\,dx=F(b)-F(a).
$$

---

# Fontes:

- STEWART, James. *Cálculo: volume 1*. 7. ed. São Paulo: Cengage Learning, 2013.

- THOMAS, George B.; WEIR, Maurice D.; HASS, Joel. *Cálculo: volume 1*. 12. ed. São Paulo: Pearson, 2012.

- THOMAS, George B.; WEIR, Maurice D.; HASS, Joel. *Cálculo: volume 2*. 12. ed. São Paulo: Pearson, 2012.

- LIMA, Elon Lages. *Análise real: volume 1*. 8. ed. Rio de Janeiro: IMPA, 2006.

- TAKHE. *Cálculo 1: aulas e exercícios resolvidos*. [YouTube], 2023. Disponível em: [https://www.youtube.com/playlist?list=PLmAu9dltGZtp1apl5ib_uL7UkP4fDwy4m](https://www.youtube.com/playlist?list=PLmAu9dltGZtp1apl5ib_uL7UkP4fDwy4m). Acesso em: 10 set. 2025.

- BARROS, Tatiana Leal. *Disciplina: Cálculo com funções de uma variável real*. Curso de graduação em Engenharia de Computação — Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2024.

- CAMARGO JUNIOR, Fausto de. *Disciplina: Integração e séries*. Curso de graduação em Engenharia de Computação — CEFET-MG, 2024.
