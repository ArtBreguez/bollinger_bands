# 🧪 Desafio Técnico: Bandas de Bollinger com WebSocket

## ✅ Objetivo

Você deverá **fazer um fork deste repositório** e implementar uma **classe chamada `BollingerBands`**. Essa classe será responsável por:

1. Receber candles em tempo real.
2. Calcular e exibir as **Bandas de Bollinger** com base nos últimos **N candles**.
3. Exibir os valores calculados no terminal junto com cada candle recebido.

## 📘 Sobre as Bandas de Bollinger

As Bandas de Bollinger são compostas por:

- **Média móvel simples (SMA)** de N períodos.
- **Desvio padrão (σ)** dos preços de fechamento.
- **Banda superior**: `SMA + (K * σ)`
- **Banda inferior**: `SMA - (K * σ)`

> Onde:  
> • `N` = número de períodos (ex: 20)  
> • `K` = fator multiplicador do desvio padrão (ex: 2)

## 🔗 Conexão com o WebSocket

Use o código abaixo para se conectar ao servidor WebSocket, receber os candles e alimentá-los à sua classe `BollingerBands`:

```python
import asyncio
import websockets
import json
import os
from bollinger_bands import BollingerBands  # ← Sua classe aqui

token = "USER_TOKEN"  # Consultar o dono do repo para saber o token

async def test_client():
    uri = "wss://leveling-tool.fly.dev"

    async with websockets.connect(uri) as websocket:
        print("✅ Connected to WebSocket server")
        await websocket.send(json.dumps({"token": token}))

        subscribe_message = {
            "action": "subscribe",
            "tickers": ["EURUSD"],
            "leveling_tool": False
        }

        await websocket.send(json.dumps(subscribe_message))
        print("📡 Subscribed to tickers")

        bb = BollingerBands(period=20, k=2)  # Criação da instância

        while True:
            message = await websocket.recv()
            data = json.loads(message)

            if "candle" in data:
                close_price = data["candle"]["close"]
                bb.add_price(close_price)
                upper, middle, lower = bb.calculate()
                print(f"📊 Close: {close_price} | Upper: {upper:.4f} | SMA: {middle:.4f} | Lower: {lower:.4f}")
```

## 🧠 Requisitos da Classe `BollingerBands`

A classe deve conter:

### ✅ Parâmetros

- `period` (int): quantidade de candles usados no cálculo.
- `k` (float): multiplicador do desvio padrão.

### ✅ Métodos obrigatórios

```python
def add_price(self, price: float) -> None:
    """Adiciona o preço de fechamento da candle atual."""

def calculate(self) -> tuple[float, float, float]:
    """Retorna (banda superior, média móvel, banda inferior)."""
```

### ✅ Exemplo de uso

```python
bb = BollingerBands(period=20, k=2)
bb.add_price(1.1234)
upper, middle, lower = bb.calculate()
```

## 🧪 Critérios de Avaliação

| Critério                             | Descrição                                     |
|-------------------------------------|-----------------------------------------------|
| ✅ Código limpo e organizado         | Clareza, legibilidade e estrutura do código   |
| ✅ Boas práticas com classes         | Uso adequado de OOP                           |
| ✅ Cálculo correto das bandas        | Resultados matematicamente consistentes       |
| ✅ Funciona com o WebSocket          | Recebe e processa dados corretamente          |
| ✅ Exibição clara no terminal        | Resultado visível e formatado corretamente    |
| ✅ Documentação clara (README)       | Instruções de instalação e execução presentes |

---

## 📝 Entrega

1. Faça o **fork** deste repositório.
2. Implemente a solução conforme instruções.
3. Adicione um `README.md` com instruções simples de execução.
4. Envie o link do seu repositório forkado com a solução implementada.
