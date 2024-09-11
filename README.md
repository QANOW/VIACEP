**a) Criação da Collection no Postman**

1. Abra o **Postman**.
2. Clique em **Create a collection** e nomeie como **ViaCEP API**.
3. Adicione uma nova requisição à coleção:
   - Nome: **Buscar CEP Porto Alegre - Domingos**
   - Método: **GET**
   - URL: `https://viacep.com.br/ws/RS/Porto%20Alegre/Domingos/json/`
   - Salve dentro da coleção **ViaCEP API**.

Agora você tem uma collection configurada para realizar buscas de endereços por logradouro na cidade de Porto Alegre.

### **b) Cenários de Teste para a API**

Os cenários de teste da API para o endpoint `https://viacep.com.br/ws/RS/Porto Alegre/Domingos/json/` podem incluir:

#### **Cenários Positivos**
1. **Validação do retorno com logradouro existente**:
   - Enviar uma requisição com uma rua válida ("Domingos") em Porto Alegre e verificar se os dados retornados estão corretos.
2. **Validação do retorno com diferentes letras maiúsculas/minúsculas**:
   - Testar o endpoint com variações como "domingos", "DOMINGOS" e validar o comportamento da API.
3. **Validação de múltiplos logradouros**:
   - Verificar se a API retorna corretamente múltiplos endereços que correspondem ao nome "Domingos" no estado do Rio Grande do Sul.

#### **Cenários Negativos**
1. **Logradouro não existente**:
   - Tentar buscar um endereço que não existe e validar se a API retorna uma mensagem de erro ou uma lista vazia.
2. **Cidade inexistente**:
   - Testar com uma cidade inexistente no estado do Rio Grande do Sul e verificar se a API lida adequadamente com essa situação.
3. **Estado inválido**:
   - Testar com um estado inválido (por exemplo, "ZZ") e observar o retorno da API.
4. **Erros de rede e timeouts**:
   - Simular falhas de rede, como erro de timeout, e verificar o comportamento da API.

### **c) Automação de Testes de Performance**

#### **Usando Postman para Testes de Performance com Newman**
1. Exporte a collection **ViaCEP API**.
2. Instale o **Newman** (CLI do Postman) para rodar testes automatizados:

   ```bash
   npm install -g newman
   ```

3. Execute a collection utilizando o Newman para realizar o teste de performance:

   ```bash
   newman run ViaCEP-API.postman_collection.json --iteration-count 1000
   ```

   Esse comando executa a coleção 1000 vezes, simulando várias requisições e medindo o desempenho da API.

#### **Usando JMeter para Testes de Performance**

1. Instale o **JMeter**.
2. Crie um **HTTP Request** no JMeter para o endpoint `https://viacep.com.br/ws/RS/Porto%20Alegre/Domingos/json/`.
3. Configure um **Thread Group** com um número alto de usuários (por exemplo, 1000 threads) para simular múltiplas requisições simultâneas.
4. Execute o teste e analise os resultados, como tempo de resposta, erros, e throughput.

### **Automatização com Cypress**

```javascript
describe('Teste da API ViaCEP', () => {
  it('Deve retornar os dados corretos para uma rua válida em Porto Alegre', () => {
    cy.request({
      method: 'GET',
      url: 'https://viacep.com.br/ws/RS/Porto%20Alegre/Domingos/json/',
    })
      .its('body')
      .should((response) => {
        expect(response).to.be.an('array').that.is.not.empty;
        expect(response[0]).to.have.property('logradouro').that.includes('Domingos');
        expect(response[0]).to.have.property('localidade', 'Porto Alegre');
        expect(response[0]).to.have.property('uf', 'RS');
      });
  });

  it('Deve retornar erro ao consultar uma cidade inválida', () => {
    cy.request({
      method: 'GET',
      url: 'https://viacep.com.br/ws/ZZ/InvalidCity/Domingos/json/',
      failOnStatusCode: false,
    })
      .its('status')
      .should('eq', 400);  // Verifica se retorna erro 400 para cidade inválida
  });
});
```
