# Robo gradiente linear
Exemplo de como implementar uma estratégia de gradiente linear de 50 em 50 pontos para operar comprado ou vendido utilizando Easy Language, NTSL

```
inputs:
    gradient_size(50),  // tamanho do gradiente em pontos
    entry_threshold(3); // distância em pontos do preço de entrada para ativar a ordem
    
vars:
    direction(0),
    entry_price(0);

if MarketPosition = 0 then
begin
    // cálculo do gradiente linear
    var gradient = LinearRegressionSlope(Close, gradient_size);

    // se o gradiente é positivo, entramos comprados
    if gradient > 0 then
    begin
        direction = 1;
        entry_price = Round2NthTick(MinList(Close[0] + entry_threshold * TickSize, High[1]), TickSize);
        Buy("Buy") next bar at entry_price stop;
    end
    // se o gradiente é negativo, entramos vendidos
    else if gradient < 0 then
    begin
        direction = -1;
        entry_price = Round2NthTick(MaxList(Close[0] - entry_threshold * TickSize, Low[1]), TickSize);
        SellShort("Sell") next bar at entry_price stop;
    end
end
else if MarketPosition = direction then
begin
    // saída da posição se o preço retorna ao ponto de entrada
    if (direction = 1 and Close <= entry_price) or (direction = -1 and Close >= entry_price) then
    begin
        ExitLong("ExitBuy") next bar at entry_price stop;
        ExitShort("ExitSell") next bar at entry_price stop;
    end
end
```

Este código define um tamanho de gradiente de 50 pontos e um limite de entrada de 3 pontos. Em cada nova barra, o robô calcula o gradiente linear das últimas 50 barras usando a função `LinearRegressionSlope()`. Se o gradiente é positivo, o robô entra comprado com uma ordem de stop de compra definida acima do preço atual mais o limite de entrada. Se o gradiente é negativo, o robô entra vendido com uma ordem de stop de venda definida abaixo do preço atual menos o limite de entrada.

Se o robô já tiver uma posição aberta, ele verifica se a posição é a mesma direção do gradiente e, se for, verifica se o preço retornou ao ponto de entrada. Se isso acontecer, o robô fecha a posição com uma ordem de stop.

