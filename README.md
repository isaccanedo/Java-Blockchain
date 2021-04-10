## Implementando um Blockchain simples em Java

# 1. Visão Geral
Neste tutorial, aprenderemos os conceitos básicos da tecnologia blockchain. Também implementaremos um aplicativo básico em Java que enfoca os conceitos.

Além disso, discutiremos alguns conceitos avançados e aplicações práticas desta tecnologia.

# 2. O que é Blockchain?
Então, vamos primeiro entender o que exatamente é o blockchain ...

Bem, sua origem remonta ao white paper publicado por Satoshi Nakamoto no Bitcoin, em 2008.

Blockchain é um livro-razão descentralizado de informações. Consiste em blocos de dados conectados por meio de criptografia. Pertence a uma rede de nós conectados na rede pública. Entenderemos isso melhor quando tentarmos construir um tutorial básico posteriormente.

Existem alguns atributos importantes que devemos entender, então vamos examiná-los:

- À prova de violação: Em primeiro lugar, os dados como parte de um bloco são à prova de violação. Cada bloco é referenciado por um resumo criptográfico, comumente conhecido como hash, tornando o bloco à prova de violação;
- Descentralizado: todo o blockchain é completamente descentralizado na rede. Isso significa que não há um nó mestre e todos os nós da rede têm a mesma cópia.
- Transparente: cada nó participante da rede valida e adiciona um novo bloco à sua cadeia por consenso com outros nós. Portanto, cada nó tem visibilidade completa dos dados.

# 3. Como funciona o Blockchain?
Agora, vamos entender como funciona o blockchain.

As unidades fundamentais de um blockchain são blocos. Um único bloco pode encapsular várias transações ou outros dados valiosos:

### 3.1. Minerando um bloco
Representamos um bloco por um valor hash. A geração do valor hash de um bloco é chamada de “mineração” do bloco. A mineração de um bloco é normalmente dispendiosa em termos computacionais, pois serve como uma “prova de trabalho”.

### 3.2. Adicionando um Bloco ao Blockchain
Embora minerar um bloco seja caro do ponto de vista computacional, verificar se um bloco é legítimo é relativamente muito mais fácil. Todos os nós da rede participam da verificação de um bloco recém-extraído.

Assim, um bloco recém-extraído é adicionado ao blockchain no consenso dos nós.

Agora, existem vários protocolos de consenso disponíveis que podemos usar para verificação. Os nós da rede usam o mesmo protocolo para detectar ramificações maliciosas da cadeia. Conseqüentemente, um branch malicioso, mesmo se introduzido, logo será rejeitado pela maioria dos nós.

# 4. Blockchain básico em Java
Agora temos contexto suficiente para começar a construir um aplicativo básico em Java.

Nosso exemplo simples aqui ilustrará os conceitos básicos que acabamos de ver. Um aplicativo de nível de produção envolve muitas considerações que estão além do escopo deste tutorial. Iremos, no entanto, tocar em alguns tópicos avançados mais tarde.

4.1. Implementando um Bloco
Em primeiro lugar, precisamos definir um POJO simples que conterá os dados para o nosso bloco:

```
public class Block {
    private String hash;
    private String previousHash;
    private String data;
    private long timeStamp;
    private int nonce;
 
    public Block(String data, String previousHash, long timeStamp) {
        this.data = data;
        this.previousHash = previousHash;
        this.timeStamp = timeStamp;
        this.hash = calculateBlockHash();
    }
    // standard getters and setters
}
```

Vamos entender o que empacotamos aqui:

- Hash do bloco anterior, parte importante para a construção da corrente;
- Os dados reais, qualquer informação com valor, como um contrato;
- O carimbo de data / hora da criação deste bloco;
- Um nonce, que é um número arbitrário usado em criptografia;
- Finalmente, o hash deste bloco, calculado com base em outros dados.

### 4.2. Calculando o Hash
Agora, como calculamos o hash de um bloco? Usamos um método calculBlockHash, mas ainda não vimos uma implementação. Antes de implementar esse método, vale a pena gastar algum tempo para entender o que exatamente é um hash.

Um hash é uma saída de algo conhecido como função hash. Uma função hash mapeia dados de entrada de tamanho arbitrário para dados de saída de tamanho fixo. O hash é bastante sensível a qualquer alteração nos dados de entrada, por menor que seja.

Além disso, é impossível obter os dados de entrada de volta apenas de seu hash. Essas propriedades tornam a função hash bastante útil em criptografia.

Então, vamos ver como podemos gerar o hash do nosso bloco em Java:

```
public String calculateBlockHash() {
    String dataToHash = previousHash 
      + Long.toString(timeStamp) 
      + Integer.toString(nonce) 
      + data;
    MessageDigest digest = null;
    byte[] bytes = null;
    try {
        digest = MessageDigest.getInstance("SHA-256");
        bytes = digest.digest(dataToHash.getBytes(UTF_8));
    } catch (NoSuchAlgorithmException | UnsupportedEncodingException ex) {
        logger.log(Level.SEVERE, ex.getMessage());
    }
    StringBuffer buffer = new StringBuffer();
    for (byte b : bytes) {
        buffer.append(String.format("%02x", b));
    }
    return buffer.toString();
}
```

Muitas coisas acontecendo aqui, vamos entendê-las em detalhes:

- Primeiro, concatenamos diferentes partes do bloco para gerar um hash;
- Então, obtemos uma instância da função hash SHA-256 de MessageDigest;
- Em seguida, geramos o valor hash de nossos dados de entrada, que é uma matriz de bytes;
- Por fim, transformamos a matriz de bytes em uma string hexadecimal, um hash normalmente é representado como um número hexadecimal de 32 dígitos.

### 4.3. Já mineramos o bloco?
Tudo parece simples e elegante até agora, exceto pelo fato de que ainda não exploramos o bloco. Então, o que exatamente envolve a mineração de um bloco, que já capturou a imaginação dos desenvolvedores há algum tempo!

Bem, minerar um bloco significa resolver uma tarefa computacionalmente complexa para o bloco. Embora calcular o hash de um bloco seja algo trivial, encontrar o hash começando com cinco zeros não é. Ainda mais complicado seria encontrar um hash começando com dez zeros, e teremos uma ideia geral.

Então, como exatamente podemos fazer isso? Honestamente, a solução é muito menos sofisticada! É com força bruta que tentamos atingir esse objetivo. Usamos o nonce aqui:

```
public String mineBlock(int prefix) {
    String prefixString = new String(new char[prefix]).replace('\0', '0');
    while (!hash.substring(0, prefix).equals(prefixString)) {
        nonce++;
        hash = calculateBlockHash();
    }
    return hash;
}
```

Vamos ver o que estamos tentando fazer aqui:

- Começamos definindo o prefixo que desejamos encontrar;
- Depois verificamos se encontramos a solução;
- Caso contrário, incrementamos o nonce e calculamos o hash em um loop;
- O ciclo continua até que acertemos o jackpot.

Estamos começando com o valor padrão de nonce aqui e incrementando-o em um. Mas existem estratégias mais sofisticadas para iniciar e incrementar um nonce em aplicativos do mundo real. Além disso, não estamos verificando nossos dados aqui, o que normalmente é uma parte importante.

### 4.4. Vamos executar o exemplo
Agora que definimos nosso bloco junto com suas funções, podemos usá-lo para criar um blockchain simples. Vamos armazenar isso em um ArrayList:

```
List<Block> blockchain = new ArrayList<>();
int prefix = 4;
String prefixString = new String(new char[prefix]).replace('\0', '0');
```

Além disso, definimos um prefixo de quatro, o que efetivamente significa que queremos que nosso hash comece com quatro zeros.

Vamos ver como podemos adicionar um bloco aqui:

```
@Test
public void givenBlockchain_whenNewBlockAdded_thenSuccess() {
    Block newBlock = new Block(
      "The is a New Block.", 
      blockchain.get(blockchain.size() - 1).getHash(),
      new Date().getTime());
    newBlock.mineBlock(prefix);
    assertTrue(newBlock.getHash().substring(0, prefix).equals(prefixString));
    blockchain.add(newBlock);
}
```

### 4.5. Verificação de Blockchain
Como pode um nó validar que um blockchain é válido? Embora isso possa ser bastante complicado, vamos pensar em uma versão simples:

```
@Test
public void givenBlockchain_whenValidated_thenSuccess() {
    boolean flag = true;
    for (int i = 0; i < blockchain.size(); i++) {
        String previousHash = i==0 ? "0" : blockchain.get(i - 1).getHash();
        flag = blockchain.get(i).getHash().equals(blockchain.get(i).calculateBlockHash())
          && previousHash.equals(blockchain.get(i).getPreviousHash())
          && blockchain.get(i).getHash().substring(0, prefix).equals(prefixString);
            if (!flag) break;
    }
    assertTrue(flag);
}
```

Então, aqui estamos fazendo três verificações específicas para cada bloco:

- O hash armazenado do bloco atual é realmente o que ele calcula;
- O hash do bloco anterior armazenado no bloco atual é o hash do bloco anterior;
- O bloco atual foi minado.

# 5. Alguns conceitos avançados
Embora nosso exemplo básico traga os conceitos básicos de um blockchain, certamente não está completo. Para colocar essa tecnologia em uso prático, várias outras considerações precisam ser levadas em consideração.

Embora não seja possível detalhar todos eles, vamos examinar alguns dos mais importantes:

### 5.1. Verificação de transação

Calcular o hash de um bloco e encontrar o hash desejado é apenas uma parte da mineração. Um bloco consiste em dados, geralmente na forma de várias transações. Estes devem ser verificados antes que possam ser parte de um bloco e minerados.

Uma implementação típica de blockchain define uma restrição sobre a quantidade de dados que pode fazer parte de um bloco. Ele também define regras sobre como uma transação pode ser verificada. Vários nós na rede participam do processo de verificação.

### 5.2. Protocolo de Consenso Alternativo
Vimos que um algoritmo de consenso como “Prova de Trabalho” é usado para minerar e validar um bloco. No entanto, este não é o único algoritmo de consenso disponível para uso.

Existem vários outros algoritmos de consenso para escolher, como Prova de Participação, Prova de Autoridade e Prova de Peso. Todos esses têm seus prós e contras. Qual usar depende do tipo de aplicativo que pretendemos projetar.

### 5.3. Recompensa de mineração
Uma rede blockchain normalmente consiste em nós voluntários. Agora, por que alguém iria querer contribuir para este processo complexo e mantê-lo legítimo e crescente?

Isso ocorre porque os nós são recompensados por verificar as transações e minerar um bloco. Essas recompensas são normalmente na forma de moedas associadas ao aplicativo. Mas um aplicativo pode decidir se a recompensa é algo de valor.

### 5.4. Tipos de Nó
Um blockchain depende totalmente de sua rede para operar. Em teoria, a rede é completamente descentralizada e todos os nós são iguais. No entanto, na prática, uma rede consiste em vários tipos de nós.

Enquanto um nó completo possui uma lista completa de transações, um nó leve possui apenas uma lista parcial. Além disso, nem todos os nós participam da verificação e validação.

### 5.5. Comunicação Segura
Uma das marcas da tecnologia blockchain é sua abertura e anonimato. Mas como ele fornece segurança para as transações realizadas internamente? Isso é baseado em criptografia e infraestrutura de chave pública.

O iniciador de uma transação usa sua chave privada para protegê-la e anexá-la à chave pública do destinatário. Os nós podem usar as chaves públicas dos participantes para verificar as transações.

# 6. Aplicações práticas do Blockchain
Portanto, o blockchain parece ser uma tecnologia interessante, mas também deve ser útil. Essa tecnologia já existe há algum tempo e - nem é preciso dizer - ela provou ser prejudicial em muitos domínios.

Sua aplicação em muitas outras áreas está sendo ativamente buscada. Vamos entender os aplicativos mais populares:

- Moeda: este é de longe o uso mais antigo e conhecido de blockchain, graças ao sucesso do Bitcoin. Eles fornecem dinheiro seguro e sem atrito para pessoas em todo o mundo, sem qualquer autoridade central ou intervenção governamental;
- Identidade: a identidade digital está se tornando rapidamente a norma no mundo atual. No entanto, isso é afetado por problemas de segurança e adulteração. O Blockchain é inevitável para revolucionar essa área com identidades totalmente seguras e à prova de violação;
- Saúde: o setor de saúde está repleto de dados, a maioria gerenciados pelas autoridades centrais. Isso diminui a transparência, a segurança e a eficiência no tratamento desses dados. A tecnologia Blockchain pode fornecer um sistema sem terceiros para fornecer a confiança tão necessária;
- Governo: esta é talvez uma área que está bem aberta a interrupções pela tecnologia de blockchain. O governo está normalmente no centro de vários serviços ao cidadão, que muitas vezes estão carregados de ineficiências e corrupção. O Blockchain pode ajudar a estabelecer relações governo-cidadão muito melhores.

# 7. Ferramentas do comércio
Embora nossa implementação básica aqui seja útil para extrair os conceitos, não é prático desenvolver um produto em blockchain do zero. Felizmente, este espaço amadureceu agora, e temos algumas ferramentas bastante úteis para começar.

Vamos examinar algumas das ferramentas populares para trabalhar neste espaço:

- Solidity: Solidity é uma linguagem de programação estaticamente tipada e orientada a objetos projetada para escrever contratos inteligentes. Ele pode ser usado para escrever contratos inteligentes em várias plataformas de blockchain como Ethereum;
- Remix IDE: Remix é uma ferramenta de código aberto poderosa para escrever contratos inteligentes no Solidity. Isso permite que o usuário escreva contratos inteligentes diretamente do navegador;
- Truffle Suite: Truffle fornece um monte de ferramentas para que um desenvolvedor comece a desenvolver aplicativos distribuídos. Isso inclui trufa, ganache e garoa;
- Ethlint / Solium: Solium permite que os desenvolvedores garantam que seus contratos inteligentes escritos no Solidity estejam livres de questões de estilo e segurança. Solium também ajuda a corrigir esses problemas.
Paridade: a paridade ajuda a configurar o ambiente de desenvolvimento para o contrato inteligente no Etherium. Ele fornece uma maneira rápida e segura de interagir com o blockchain.


# 8. Conclusão
Para resumir, neste tutorial, passamos pelos conceitos básicos da tecnologia blockchain. Nós entendemos como uma rede mina e adicionamos um novo bloco no blockchain. Além disso, implementamos os conceitos básicos em Java. Também discutimos alguns dos conceitos avançados relacionados a essa tecnologia.

Finalmente, concluímos com algumas aplicações práticas do blockchain e também com as ferramentas disponíveis.