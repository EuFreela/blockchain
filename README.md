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

#### add_transaction


