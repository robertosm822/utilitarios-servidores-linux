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
``
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
}``
