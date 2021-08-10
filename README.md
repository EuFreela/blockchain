# LAMECKCOIN
#### Curso Blockchain

Instruções:

### Servers
1. Os arquivos nomeados com a porta representam os "nós" da rede P2P. Devem ser iniciados.

Obs: use o POSTMAN ou equivalente.

### Métodos

#### get_chain (GET)
Usado para iniciar a conexão com a rede.<br>
Exemplo: http://127.0.0.1:5001/get_chain

resposta é o bloco **genesis** de cada nó:
```
{
    "chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:20:32.862463",
            "transactions": []
        }
    ],
    "length": 1
}
```

Para cada nós, as datas estarão erradas. Isso é um problema. O próximo método atualiza sincroniza os blocos..

<hr>

#### connect_node (POST)
Para cada nó é feito a sincronia com a rede.<br>
Exemplo: http://127.0.0.1:5001/connect_node

O corpo varia com o nó em execução. Se estiver nó 5001 execute em relação aos demais, neste caso 5002 e 5003..
Body:
```
{
    "nodes": ["http://127.0.0.1:5002","http://127.0.0.1:5003"]
}
```

resposta:
```
{
    "message": "Todos nos conectados, blockchain contem os seguintes nos:",
    "total_nodes": [
        "127.0.0.1:5003",
        "127.0.0.1:5002"
    ]
}
```
<hr>

#### mine_block (GET)
Ação que permite a mineração dos blocos. Note que o nonce esta baixo (complexidade baixa) o que permite ser rápido sua execução.
<br>
Exemplo: http://127.0.0.1:5001/mine_block

resposta:
```
{
    "index": 2,
    "message": "Parabens voce acabou de minerar um bloco!",
    "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
    "proof": 533,
    "timestamp": "2021-08-09 22:32:11.799649",
    "transaction": [
        {
            "amount": 1,
            "receiver": "Fernando",
            "sender": "df2ae8444476495f8a032490c0b4855f"
        }
    ]
}
```

Após, chame o método **get_chain (GET)**, - no nó que foi executado o método, e observe a cadeia:
```
{
    "chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:09.073914",
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
            "proof": 533,
            "timestamp": "2021-08-09 22:32:11.799649",
            "transactions": [
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        }
    ],
    "length": 2
}
```

Veja agora o mesmo método nos demais nós, sem executar o **mine_block**:

```
{
    "chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:12.019917",
            "transactions": []
        }
    ],
    "length": 1
}
```

Eles permanecem no bloco **genesis**. iSSO acontece porque o sistema precisa aplicar o **PROTOCOLO DE CONSENSO**

<hr>

#### replace_chain (GET)
Este é o protocolo de consenso que vai sincronizar com os demais nós...
<br>
Exemplo: http://127.0.0.1:5002/replace_chain

```
{
    "message": "Os nos tinham cadeias diferentes então foi substituido ",
    "new_chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:09.073914",
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
            "proof": 533,
            "timestamp": "2021-08-09 22:32:11.799649",
            "transactions": [
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        }
    ]
}
```

A primeira execução irá atualizar o blockchain. Se executar novamente, terá essa resposta dizendo que ja foi atualizado..

```
{
    "actual_chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:09.073914",
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
            "proof": 533,
            "timestamp": "2021-08-09 22:32:11.799649",
            "transactions": [
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        }
    ],
    "message": "Tudo certo, nao houve substituicao"
}
```

#### add_transaction (POST)
Este método é responsável pelo envio de transações.
<br>
Exemplo: http://127.0.0.1:5001/add_transaction

Meu arquivo 5001 tem um nome no código chamado Fernando. Ele esta enviando uma transação de 7.777 para Xana (arquivo 5002), outro nó na rede.

O corpo é escrito com:
request:
```
{
    "sender": "Fernando",
    "receiver": "Xana",
    "amount": 7777
}
```

Response:
```
{
    "message": "Esta transacao sera adicionada ao bloco 3"
}
```

A transação, segundo o princípio do conceito do blockchain, ainda será adicionada - ela não esta no bloco. Para que ele chegue até o bloco é preciso **MINERAR ESSA TRANSAÇÃO**
De fato, qualquer criptomoeda **depende dos mineradores**. Execute: http://127.0.0.1:5001/mine_block


response
```
{
    "index": 3,
    "message": "Parabens voce acabou de minerar um bloco!",
    "previous_hash": "554cbf389c32043d67b5820002b39a71554d25ce5cd54cc7e99d786945406477",
    "proof": 45293,
    "timestamp": "2021-08-09 22:51:47.235547",
    "transaction": [
        {
            "amount": 7777,
            "receiver": "Xana",
            "sender": "Fernando"
        },
        {
            "amount": 1,
            "receiver": "Fernando",
            "sender": "df2ae8444476495f8a032490c0b4855f"
        }
    ]
}
```

Temos um novo bloco com duas transações com os dados de quem fez a mineração e de quem fez a transação e o remetente. Execute o médoto **get_chain (GET)**

resposta:
```
{
    "chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:09.073914",
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
            "proof": 533,
            "timestamp": "2021-08-09 22:32:11.799649",
            "transactions": [
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        },
        {
            "index": 3,
            "previous_hash": "554cbf389c32043d67b5820002b39a71554d25ce5cd54cc7e99d786945406477",
            "proof": 45293,
            "timestamp": "2021-08-09 22:51:47.235547",
            "transactions": [
                {
                    "amount": 7777,
                    "receiver": "Xana",
                    "sender": "Fernando"
                },
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        }
    ],
    "length": 3
}
```

Temos toda a descrição do bloco. Execute o método **get_chain** nos demais blocos:

```
{
    "chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:09.073914",
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
            "proof": 533,
            "timestamp": "2021-08-09 22:32:11.799649",
            "transactions": [
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        }
    ],
    "length": 2
}
```

Eles estão desatualizados. Novamente, executamos o protocolo de consenso com o método **replace_chain**:

```
{
    "message": "Os nos tinham cadeias diferentes então foi substituido ",
    "new_chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:09.073914",
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
            "proof": 533,
            "timestamp": "2021-08-09 22:32:11.799649",
            "transactions": [
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        },
        {
            "index": 3,
            "previous_hash": "554cbf389c32043d67b5820002b39a71554d25ce5cd54cc7e99d786945406477",
            "proof": 45293,
            "timestamp": "2021-08-09 22:51:47.235547",
            "transactions": [
                {
                    "amount": 7777,
                    "receiver": "Xana",
                    "sender": "Fernando"
                },
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        }
    ]
}
```

Novamente:

```
{
    "actual_chain": [
        {
            "index": 1,
            "previous_hash": "0",
            "proof": 1,
            "timestamp": "2021-08-09 22:21:09.073914",
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "f3c4bdf8d1be1fc7217cffaf79e114c9d941d9757838707a65c0189ac56b3198",
            "proof": 533,
            "timestamp": "2021-08-09 22:32:11.799649",
            "transactions": [
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        },
        {
            "index": 3,
            "previous_hash": "554cbf389c32043d67b5820002b39a71554d25ce5cd54cc7e99d786945406477",
            "proof": 45293,
            "timestamp": "2021-08-09 22:51:47.235547",
            "transactions": [
                {
                    "amount": 7777,
                    "receiver": "Xana",
                    "sender": "Fernando"
                },
                {
                    "amount": 1,
                    "receiver": "Fernando",
                    "sender": "df2ae8444476495f8a032490c0b4855f"
                }
            ]
        }
    ],
    "message": "Tudo certo, nao houve substituicao"
}
```

<hr>

## Recapitulação:

Blockchain pode ser aplicado em diversos conceitos e não apenas ao tema de criptomoedas.

***1. Qual das opções abaixo descreve a diferença entre como as transações funcionam em bancos tradicionais e no blockchain?***

Bancos tem o conceito de balanço. Transações retiram ou incluem neste balanço.
No blockchain cada transação tem um ciclo de vida próprio e deve começar de outra transação e terminar em outra trnasação.
Blockchain não tem conceito de balanço. O que uma carteira de criptomoeda mostra é simplesmente uma soma de todas as transações
que existem no blockchain onde você aparece enviando ou recebendo transações

***2. Existem taxas de transações? Se sim de onde vem elas vem?***

Sim. Vem das diferenças entre as entradas e saídas. Tudo o que não é usado é dado ao minerador e é assimq ue você escolhe quanto quer pagar de taxas

***3. O que não é preciso para verificar a validade de uma mensagem (ou transação)?***

A chave privada.

***4. Qual é a diferença entre chave pública e o endereço?***

São parecidas. O endereço é a versãod o SHA256 criptografada da chave publica. Isso é feito para segurança adicional em caso de
hackeamento.

***5. Em qual ordem, de cima para baixo, funcionam as três camadas do Blockchain?***

Tecnologia, Protocolo/Moeda, Token

***6. Qual será o valor máximo de Bitcoins que estará em circulação e quando isso vai acontecer?***

21 milhões em 2140

***7. Supondo que o nível de dificuldade de mineração requeira 18 zeros a esquerda para o hash ser válido, qual é a probabilidade que um hash selecionado ao acaso seja válido?***

0,0(x10^18)2%

***8. O que é um menpool?***

É uma área de armazenamento de transações antes de setem adicionada a um bloco. Cada participante da rede P2P distribuída tem seu 
próprio menpool no seu computador.

***9. O que é o ataque de 51% e como o operador do ataque se beneficia dele?***

Equivale a um ataque na rede onde 51% dos computadores devem ser comprometidos ao mesmo tempo para um efeito positivo. A hipótese baixa é quando um grupo oculto de participanes com maior capacidade computacional minera sem disponibilizar a sua ve~são do blockchain para ocultorestante da rede. O operador se beneficia através do problema do gasto dupicado.









