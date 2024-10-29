# Recruits Task - Week #2
- ## [git para noobs](https://hackmd.io/@PedroRomao/HJ0GJSae1x)
- ## Add this file to your local git repository create a new branch and work on it, when you are done or as you complete the questions merge the branch into the main branch (remote main branch will have your final work, this means main branch on GitHub, please make the repository public for the next weeks).
- ## Perform the following tasks to the best of your hability, sometimes, in the questions, there are multiple answers just tell us what you think, feel free to use the group to ask questions.

### 1
**1.** Check out the [el-sw repository](https://github.com/fs-feup/el-sw/tree/main) code and documentation  and try to generally understand what the software does in each device (there is no need to understand all the little details).
### 2
When we read values from the brake sensor (C1) and the apps (C3) we do not use the most recent reading and use instead a different approach. Explain the approach and why you think it is used.

**Answer:** *Pelo que entendi, em vez de usar os valores lidos diretamente, o código implementado, para ambos C1 e C3, guarda esse valor e coloca-o em um vetor, que contem mais outras N amostras, e em seguida faz a média desse vetor. Essa média será então o valor de saída, o qual eventualmente vai ser usado noutras funções. Pelo que entendi, este sistema é uma média móvel, ou seja, a cada amostra lida, faz a média do valor recebido e de N valores anteriores. Este sistema permite suavizar possíveis oscilações no sistema. Por exemplo, caso um valor lido esteja distorcido de alguma forma, o valor de saída do sistema naquele instante vai atenuar esse valor distorcido.*


### 3
Check out the R2D(Ready To Drive) code on the C3 state machine. In the condition below we use a timer (R2DTimer) to check the brake was engaged instead of just checking the brake pressure received from can, why?
```c++
        if ((r2dButton.fell() and TSOn and R2DTimer < R2D_TIMEOUT) or R2DOverride)
        {
            playR2DSound();
            initBamocarD3();
            request_dataLOG_messages();
            R2DStatus = DRIVING;
            break;
        }
```

**Answer:** *Quando o sensor do travão é pressionado, na caixa C1, o valor do sensor vai ser medido e mandado para a CAN BUS. Após recebida essa informação, na caixa C3, vai ser confirmado se o valor de pressão do travão é maior do que 165, e se isso for positivo, a variável R2DTimer vai dar "reset", o seu valor vai ser igual a 0, logo sendo 0<R2D_TIMEOUT, o carro vai entrar no modo R2D. Esta implementação além de permitir ver se o pedal do travão foi pressionado, vai permitir reduzir possíveis erros de "delay".*


### 4
What is the ID of the can message sent to the bamocar to request torque?
**Answer:** *0x201 - Source Device: Teensy C3 / AS CU - Intended Destination: Bamocar - Comments: Torque Request to Bamocar*

### 5 
The code below is not amazing, tell us some things you would change to improve it, you can write them down in text or correct the code:
```c++
class mycar {
private:
    int N=8;
    int sensores[N];
    //em vez de usar várias variáveis, é preferivel usar apenas um vetor, cuja dimensão será o número de sensores usados N
    //não contabilizei o sensor 9, já que não é mais usado, logo N=8

public:
    mycar(){
        for(int i=0; i<N; i++){
            sensores[i]=0;
        }
    }
    //em vez de inicializar cada um dos leitores por extenso, é mais compacto utilizar um ciclo for
    //este percorre o vetor sensores, inicializando cada um dos sensores

    void updateprint() {
        for(int i=0; i<N; i++){
            sensores[i]=analogRead(i);
        }
    //em vez de atualizar os leitores um a um, por extenso, fica mais compacto utilizar um ciclo for
    //este percorre o vetor sensores, e atualiza os seus valores conforme a função "analogRead()"
        func(sensores,N);
    //para dar print aos valores é preferível apenas passar como argumentos o vetor com os sensores e a sua dimensão N
    //a função func é criada á parte, o que ajuda, por exemplo, caso seja preciso usá-la de novo, com o mesmo objetivo, e então não é preciso reescrever o código da mesma
    }

    void func(int vec[], int M) {
        for(int i=0; i<M; i++){
            Serial.print("Sensor Reading ");
            Serial.print(i);
            Serial.print(": ");
            Serial.println(sensores[i]);
        }
    //em vez de dár print de cada um dos sensores por extenso, podemos usar um ciclo for
    //a função "Serial.print()", pelo que entendi, é uma forma de dár print em código c++, quando se utiliza arduinos
    //ainda daria para escrever tudo na mesma linha, algo do tipo: "Serial.print("Sensor Reading "); Serial.print(i); Serial.print(": "); Serial.println(sensores[i]);"
    //mas apesar de ficar ainda mais compacto, acho que fica um pouco mais confuso, por isso prefiro como ficou
    //a função "Serial.println()", escreve o conteúdo e passa para a próxima linha
    }
};
```