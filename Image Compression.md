# **Compressão de Imagens**

Reduz a quantidade de dados necessário para representar uma 
imagem, podendo ser sem perdas(Lossless Compression) ou com 
perdas(Lossy Compression).

## **Sem perdas(Lossless Compression)**

- Explora a redundância presente na imagem;
- A imagem recuperada é identica à original.

## **Com perdas(Lossy Compression)**

- Modifica a imagem original de maneira sutil na medida do 
possível para que, a mudança não seja perceptível ao olho humano;
- A imagem recuperada é uma aproximação da original.

## **Tipos de redundância**

### **Codificação**

Ocorre quando se utiliza de forma ineficiente uma 
representação para os valores de pixel.
Ex.: Usar 8 bits para representar cada pixel em uma
imagem contendo apenas 16 tons, nesse caso bastariam 4 pixels. 

### **Interpixel**

Valores de pixels vizinhos tendem a ser bastante similares.

### **Psico-visual**

Diferentes níveis de informação se encontram observados em 
uma imagem, e possuem importâncias diferentes para o sistema 
visual humano.

### **Interframe**

Frames adjacentes de um vídeo tendo a possuir uma alta 
coerencia temporal.

## **Critérios de Fidelidade**

- Mensura as diferenças entre a imagem original(f), e a descomprimida(f');
- Podem ser critérios objetivos ou subjetivos;
- Exemplo de critério objetivo: root-mean-square(RMS) error
  
Para uma imagem MxN:

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/RMS(root-mean-square)%20Formula.png" alt="RMS Formula" />

O resultado da fórmula não tem uma correlação com o sistema 
visual humano.

- Exemplo de critério subjetivo: Percepção Humana.

## **Algorítmos para compressão sem perdas(Lossless Compression)**

- Delta Compression;
- Run Length Encoding (RLE);
- Statical Coding: Ex.: Huffman Coding(Codificação de Huffman).

### **Delta Compression**

Codificação Delta ou Codificação diferencial, ela consiste em
representar uma imagem em termos das diferenças entre os valores de pixels vizinhos com relação aos seus anteriores em uma mesma linhas.

- Explora a redundância Interpixel;
- O primeiro pixel de cada linha precisa ser mantido para que se inicie o processo de codificação diferencial;
- Pequenas diferenças são codificadas utilizando apenas um número reduzido de bits.

### **Run Length Encoding (RLE)**

Representa um grupo de pixels adjacentes(em uma mesma linha) que 
tenham valores similares, e então são substituídos por um tuple (n, i), n é o número consecutivo de pixels e i é a intensidade;
Ex: A sequência { 32, 32, 32, 32, 32 } é representada como 5, 32.

Não é adequado para imagens ruidosas ou com alto teor de textura, tende a gerar muitas sequências com tamanho 1.

### **Statical Coding (Foco no Huffman Coding)**

Utiliza de conceitos da teoria da informação, a quantidade 
de informações contida em um pixel(cor ou tom) é
inversamente proporcional à sua frequência.

Substitui pixels por "codeworks" ou seja códigos de tamanhos variáveis. 
Utilizar códigos ótimos aqueles que fazem uso de uma quantidade menor de bits para elementos mais frequentes.

#### **Huffman Coding**

Ao utilizar a codificação de Huffman nós assumimos algumas coisas:

- A sequência a ser comprimida é formada por uma
sequência de símbolos: S = { s1, ..., sn}, na qual n > 1;
- A frequência(fi) de cada símbolo(si) é conhecida;
- Nenhum código que vamos gerar pode ser prefixo de outro código;
- O código otimizado será representado na forma de um árvore binária completa(Aquela em que todo nó não folha possuí dois símbolos).
  - Cada símbolo(si) será associado à uma folha do nó.
  - O código associado ao símbolo(si) é uma sequência binária
  definida pelo caminho da raíz da árvore até a folha nó contendo o si.

**Construindo uma árvore de Huffman**

Comece definindo n sub-trees cada uma contendo um único nó correspondente à um símbolo. A frequência de cada nó é a frequência do símbolo correspondente.

**Passos gerais:**

- Selecione dois nós T' e T'' com pequenas frequências;
- Junte esses dois nós formando uma nova árvore e crie um novo nó do qual as raízes da esquerda e direita são raízes de T' e T'';
- f(T) = f(T') + f(T'');
- O algorítmo termina quando tivermos apenas uma sub-árvore;
- O custo do algorítmo é cost O(n log n).

Exemplo:

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/Huffman%20Coding/Huffman%20Tree%20Example.png" alt="Huffman Tree Example">

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/Huffman%20Coding/Huffman%20Tree.png" alt="Huffman Tree" />

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/Huffman%20Coding/Huffman%20Tree%20Values.png" alt="Huffman Tree Values" />

## **Compressão JPEG**

Join Photographic Experts Group(JPEG)
Define 3 tipos de codificação de sistemas:

- Sistema de Codificação Independente, para compressão sem perdas;
- Sistema de Codificação Estendida, para compressões de alta precisão, ou para aplicações que fazem reconstruções incrementais;
- Sistema de Codificação de Linha de Base com Perdas, apropriado para a maioria das aplicações e é baseada na transformação discreta do cosseno(ou DCT).

### **Sistema de Codificação de Linha de Base com Perdas**

- Algoritmo com perdas baseado em uma codificação de transformação;
- Cria uma representação baseada em frequência;
- Descarta alguns dos coeficientes de alta frequência para obter redundância;
- Baseado na transformação discreta do cosseno(ou DCT).

**Transformação Discreta do Cosseno**

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/JPEG/Discrete%20Cossine%20Transform(DCT).png" alt="Transformação Discreta do Cosseno" />

**Inversão da Transformação Discreta do Cosseno**

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/JPEG/Inverse%20DCT.png" alt="DCT Inverso">

### **O Algorítmo JPEG**


- Ele força a ocorrência de redundâncias, e então as elimina. 
- Quebra a imagem original em pequenas imagens de pixels 8x8
e opera sobre elas para manter os efeitos dessas perdas localmente.

#### **Para imagens monocromáticas 8x8:**

```
{
  Subdivida a imagem em blocos de pixels 8x8*;
  para cada um dos blocos de pixels faça:
    subtraia 128 do valor de cada pixel;
    calcular o DCT para o bloco;
    quantize os coeficientes DCT;
    organize os coeficientes quantizados em um array 1D;
    aplique Delta encoding para o primeiro(zero frequency) coeficiente;
    aplique RLE para comprimir o coeficiente de valor zero;
    aplique Huffman coding para os coeficientes;
    retornar os coeficientes codificados para o bloco;
  fim_faça
}
```
*Se necessário, padding é usado replicando o último pixel relacionado à linha ou coluna.

#### **Para imagens coloridas:**

- Converta as imagens de RGB para YCbCr;
  - Y (luminance) corresponde ao brilho do pixel;
  - Cb e Cr representam a informação cromática.

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/JPEG/YCbCr.png" alt="YCbCr" />

- Os canais Y, Cb, e Cr são processados pelo algorítmo JPEG independentemente, mas de forma similar;
- O sistema visual humano é mais sensível para variações de luminância do que de crominância;
- Compressão do Cb e Cr(chroma subsampling).

#### **Compressão de Cb e Cr(chroma subsampling)**

- 4:4:4 (Sem compressão);
- 4:4:0 (Redução na vertical de 2x);
- 4:2:2 (Redução na horizontal de 2x);
- 4:2:0 (Redução de 2x na horizontal e vertical).
  
Em 4:s1:s2, s1 e s2 são os números de amostras de croma por bloco de amostras de luminância 4x2 nas linhas 1 e 2, respectivamente.

### **Quantização de Coeficientes DCT**

- O DCT produz coeficientes reais;
  - Deixar dessa forma Consumiria mais espaço do que guardar os valores originais dos pixels.
- Os coeficientes são quantizados como:

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/JPEG/DCT%20Coefficient%20Quantization.png" alt="DCT Coefficient Quantization" />  

  - u e v são os parâmetros de frequência espacial variando de 0 à 7.
  
#### **Tabelas de quantização**

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/JPEG/Quantization%20Tables.jpg" alt="Tabelas de Quantização">

Uma tabela diferente é utilizada para a quantização de crominância.

É o processo de quantização que introduz perdas adicionais,
a justificativa para a quantização é que o sistema visual humano é sensível para variações na iluminação, mas dentro de certos limites variações sutís de magnitude não são percebidas.

- Ao aumentar os valores em Q, aumentamos também o número de coeficientes zero T'(u, v), o que pode causar perda de informação, embora auxilie na compressão;

- A taxa de compressão em arquivos JPEG, é controlada pela escala da tabela de quantização;

- Melhor qualidade -> Menos coeficientes zero T'(u, v).

#### **Efeitos da quantização**

- Em regiões relativamente homogêneas muitos coeficientes DCT são suficientemente pequenos, e não comprometem a reconstrução da imagem se zerados;
  
- Em regiões com alta frequência os elementos de alta frequência tendem a ter magnitudes mais altas, e sobreviveram ao processo de quantização.

- O algorítmo JPEG discarta algorítmos relacionados à altas frequências e tende a preservar os coeficientes de regiões com mais detalhes.

### **Reordenação dos coeficientes DCT**

Após a quantização, os coeficientes DCT de cada bloco 8x8 são organizados em um array 1D(ordenada por frequência).

<img src="https://raw.githubusercontent.com/LordLuch/images/main/ImageCompression/JPEG/Reordering%20of%20DCT%20Coefficients.png" alt="Reordenação de coeficientes DCT" />

- Aplica-se Delta Encoding para o primeiro coeficiente entre os blocos adjacentes(Atual e anterior);
- Usa-se Run Length Encoding para comprimir sequências de zero;
- Aplica-se Huffman Coding para compressão adicional;
- A imagem descomprimida é obtida ao reverter o processo incluindo o coeficiente de quantização e DCT.
