# utilitarios-servidores-linux
Utilitários para servidores.

## Instalar Mysql no Ubuntu 19 (e administrar):
 - https://support.rackspace.com/how-to/install-mysql-server-on-the-ubuntu-operating-system/

## Conectar ao banco no Shell:
 - sudo /usr/bin/mysql -u root -p
 
 Primeira senha do Sistema, depois a senha root Mysql.

## Instalar Phpmyadmin no Ubuntu 19:
 - https://www.liquidweb.com/kb/install-phpmyadmin-ubuntu-18-04/
 
## Deinstalar Mysql no Ubuntu 19:
 - https://help.cloud66.com/maestro/how-to-guides/databases/shells/uninstall-mysql.html

## Anotações rápidas sobre dúvidas na API MOIP:
```
$token ='O3KGMYCIUCJOHKXWBQMK0PSEXA3KIJHT';
$key = 'NMDSJ2NWIKTLYI6OYCUUTLUJXU02AWKVLGCPTSFZ';

// Source Developer Kit
$moip = new Moip(new BasicAuth($token, $key), Moip::ENDPOINT_SANDBOX);

function gravarLog($texto){
    //Variável arquivo armazena o nome e extensão do arquivo.
    $arquivo = "log_clientes.txt";
     
    //Variável $fp armazena a conexão com o arquivo e o tipo de ação.
    $fp = fopen($arquivo, "a+");
 
    //Escreve no arquivo aberto.
    fwrite($fp, $texto.",");
     
    //Fecha o arquivo.
    fclose($fp);
}
function lerlog($arquivo){
    //Variável arquivo armazena o nome e extensão do arquivo.
    //$arquivo = "meu_arquivo.txt";
     
    //Variável $fp armazena a conexão com o arquivo e o tipo de ação.
    $fp = fopen($arquivo, "r");
 
    //Lê o conteúdo do arquivo aberto.
    $conteudo = fread($fp, filesize($arquivo));
     
    //Fecha o arquivo.
    fclose($fp);
     
    //retorna o conteúdo.
    return $conteudo;
}


function createCustomerOne($moip, $data)
{
    // Criando o primeiro cliente, com email unico
    $customer = $moip->customers()->setOwnId(uniqid())
        ->setFullname($data['fullname'])
        ->setEmail($data['email'])
        ->setBirthDate($data['data_nascimento'])
        ->setTaxDocument($data['cpf'])
        ->setPhone(12,$data['telefone'])
        ->addAddress('BILLING', $data['logradouro'], $data['numero'], $data['bairro'], $data['cidade'], $data['uf'], $data['cep'])
        ->create();
    // GRAVAR LOG DE ID DE CUSTOMERS
    gravarLog($customer->getId());
    
    return $customer->getId();
}

$clienteDados = [
    'fullname'          => 'Severina Xique',
    'email'             => 'severinax13@email.com',
    'data_nascimento'    => '1969-06-15',
    'cpf'               => '05144536489',
    'telefone'          => '21 98653265',
    'logradouro'        => 'Rua Larga',
    'numero'            => 156,
    'bairro'            => 'Vila Verde',
    'cidade'            => 'Recife',
    'uf'                => 'PE',
    'cep'               => '55155070',
    'exp_month'         => '08',
    'exp_year'          => '2021',
    'card_number'       => '4222222222222222',
    'cvc'               => '456',
    'telefone_number'   => '98653265',
    'ddd'               => '45'


];

//gravarLog("CUS-C2VAXPT0G47L");
//gravarLog("CUS-1C4468YLOKH3");
echo lerlog("./log_clientes.txt");

$ids = lerlog("./log_clientes.txt");
$idsUnit = explode(',', $ids);
echo $idsUnit[0];

// testando - gerdo ID Cutomer: CUS-C2VAXPT0G47L
$clienteTest = createCustomerOne($moip, $clienteDados);
echo ($clienteTest);

exit();
// $teste = createCustomerOne($moip, $clienteDados);

/*
// consultando dados do cliente criado
*/
function getCustomerById($moip, $idConsumer)
{
    $customer = $moip->customers()->get($idConsumer);
    return $customer->getFullname();
}
// testando retorno de dados dos Cliente por ID: CUS-WTMW97XOD4VU
//echo getCustomerById($moip, 'CUS-C2VAXPT0G47L'); exit();

/*
// Consultar cartao de credito de exemplo do cliente
*/
function generateCreditCardByIdCustomer($moip, $dados=[], $idConsumer)
{
    $customer = $moip->customers()->get($idConsumer);
    return $customer->creditCard()
            ->setExpirationMonth($dados['exp_month'])
            ->setExpirationYear($dados['exp_year'])
            ->setNumber($dados['card_number']) /* 16 digitos */
            ->setCvc($dados['cvc']) /* 3 digitos */
            ->setFullname($dados['fullname'])
            ->setBirthdate($dados['data_nascimento'])
            ->setTaxDocument('CPF',$dados['cpf'])
            ->setPhone('55',$dados['ddd'],$dados['telefone_number'])
            ->create($idConsumer);
}   

//teste gerar cartao associado a Cliente
    // $cartaoAssociado = generateCreditCardByIdCustomer($moip, $clienteDados, 'CUS-WTMW97XOD4VU');
    // var_dump($cartaoAssociado); exit();

// Gerar um pedido
$customer = $moip->customers()->get('CUS-WTMW97XOD4VU');
/*
$order = $moip->orders()->setOwnId(uniqid())
        ->addItem('Notebook Lenovo', 2, 'SKH1', 350000)
        ->addItem('TV Smarth', 1, 'SKTV', 150000)
        ->setShippingAmount(4500)
        ->setAddition(500)
        ->setDiscont(3000)
        ->setCustomer($customer)
        ->create();
*/

function orderByCustomerId($moip, $customer, $item)
{
    //'CUS-WTMW97XOD4VU'
    $uniqid = uniqid();
    $ordens = $moip->orders()->setOwnId($uniqid);
    $ordens->addItem($item['nome'], $item['qtd'], $item['SKU'], $item['amount']);
    
    return  $ordens->setShippingAmount(4500)
                    ->setAddition(500)
                    ->setDiscont(3000)
                    ->setCustomer($customer)
                    ->create();
        
}
// testando array de dados no pedido
$items = [
    [
        'nome'=>'Notebook Lenovo',
        'qtd'=> 1,
        'SKU'=> 'SKH1',
        'amount'=> 350000
    ],
    [
        'nome'=>'TV Smarth',
        'qtd'=> 1,
        'SKU'=> 'SKTV',
        'amount'=> 150000
    ]
];
/*
foreach($items as $item)    
{
    $ordem = orderByCustomerId($moip, $customer, $item);
}

var_dump($ordem);
exit();
*/
// Consultar resultado dos Pedidos
// Gerado ID Pedido: ORD-Z3LU1VW9DE61 | ORD-XSO19FQS237R
    // $oderById = $moip->orders()->get('ORD-XSO19FQS237R');

$filter = new \Moip\Helper\Filters;
$pagination = new \Moip\Helper\Pagination(1,0);
$filter->greaterThanOrEqual(\Moip\Resource\OrdersList::CREATED_AT, '2020-03-10');

$oderById = $moip->orders()->getList(null, $filter);
$paginar = $moip->orders()->getList($pagination);
print_r($paginar);```
