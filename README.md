# CadastraCliente.php
Criação do cadastracliente.php

<?php
// Inclui arquivos de segurança, cabeçalho e conexão com o banco de dados //
include('segurancadez.php');
include('cabecalho.php');
include('conn.php');
// Verifica se a requisição foi enviada via método POST //
if($_SERVER['REQUEST_METHOD']=='POST'){
    // Captura os dados do formulário //
    $nome = $_POST['nome'];
    $apelido = $_POST['apelido'];
    $cpf = $_POST['cpf'];
    $email = $_POST['email'];
    $cep = $_POST['cep'];
    $rua = $_POST['rua'];
    $numero = $_POST['numero'];
    $bairro = $_POST['bairro'];
    $cidade = $_POST['cidade'];
    $uf = $_POST['uf'];
    $nascimento = $_POST['dt_nascimento'];
    $telefone = $_POST['telefone'];
    $nivel = $_POST['nivel'];
    $senha = $_POST['senha'];
    $senha2 = $_POST['senha2'];

    // Verifique se as senhas são iguais //
    if($senha != $senha2){
        header('Location:cadastracliente.php?msg=As senhas devem ser iguais');
        exit();
    }

    // Criação do tempero para segurança da senha //
    $tempero = rand(100000000,999999999) . date("Y-m-d H:i:s") . strrev($senha);
    $tempero = md5($tempero);
    $senha = md5($senha . $tempero);

    // Verifica se o e-mail já está cadastrado //
    $sql = "SELECT COUNT(*) FROM tb_usuarios WHERE email_usuario = '$email'";
    $result = mysqli_query($link, $sql);
    $quant = mysqli_fetch_array($result);
    // Se o e-mail já existir, retorna erro e interrompe o cadastro //
    if($quant[0] == 1){
        header('Location: cadastracliente.php?msg=Email já cadastrado');
        exit();
    }

    // Inserção do novo usuário no banco de dados //
    $sql ="INSERT INTO `tb_usuarios`(`nome_usuario`, `apelido_usuario`, `cpf_usuario`, `email_usuario`, `cep_usuario`, 
    `rua_usuario`, `numero_rua_usuario`, `bairro_usuario`, `cidade_usuario`, `uf_usuario`, `nascimento_usuario`, `senha_usuario`, 
    `telefone_usuario`, `nivel_usuario`, `tempero_usuario`) VALUES ('$nome','$apelido'
    ,'$cpf','$email','$cep','$rua','$numero','$bairro','$cidade','$uf','$nascimento','$senha',
    '$telefone','$nivel', '$tempero')";
    // Executa a inserção e fecha a conexão com o banco de dados //
    mysqli_query($link,$sql);
    mysqli_close($link);
    // Redireciona o usuário para a página de listagem de usuários //
    header('location: listausuarios.php');
    exit();
}
?>
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="cadastra.css">
    <title>Cadastra Usuario</title>
</head>
<body>
    <h1>Cadastra Usuario</h1>
    <br>
    <?php
include('msg_user.php');
?>
    <form action="cadastracliente.php" method="post">
        <label for="nome">Nome:</label>
        <input type="text" name="nome" id="nome" maxlength="50" required>
        <br>
        <label for="apelido">Alias</label>
        <input type="text" name="apelido" id="alias" maxlength="15" required>
        <br>
        <label for="cpf">CPF:</label>
        <input type="text" name="cpf" id="cpf" maxlength="14" required>
        <br>
        <label for="email">Email:</label>
        <input type="email" name="email" id="email" maxlength="50" required>
        <br>
        <label for="cep">CEP:</label>
        <input type="text" name="cep" id="cep" maxlength="9" required>
        <br>
        <label for="rua">Logradouro:</label>
        <input type="text" name="rua" id="rua" maxlength="50" required>
        <br>
        <label for="numero">Numero:</label>
        <input type="text" name="numero" id="numero" maxlength="5" required>
        <br>
        <label for="bairro">Bairro:</label>
        <input type="text" name="bairro" id="bairoo" maxlength="30" required>
        <br>
        <label for="cidade">Cidade:</label>
        <input type="text" name="cidade" id="cidade" maxlength="30" required>
        <br>
        <label for="uf">UF:</label>
        <input type="text" name="uf" id="uf" maxlength="2" required>
        <br>
        <label for="dt_nascimento">Data de Nascimento:</label>
        <input type="date" name="dt_nascimento" id="dt_nascimento" required>
        <br>
        <label for="telefone">Telefone:</label>
        <input type="text" name="telefone" id="telefone" maxlength="11" required>
        <br>
        <label for="nivel">Nivel:</label>
        <select id="nivel" name="nivel">
            <option value="1">Usuario</option>
            <option value="10">Administrador</option>
        </select>
        <br>
        <label for="senha">Senha:</label>
        <input type="password" name="senha" id="senha" required>
        <br>
        <label for="senha2">Repita a senha</label>
        <input type="password" name="senha2" id="senha2" required>
        <br>
        <br>
        <input type="submit" value="enviar">
    </form>
</body>
</html>
<!-- JavaScript com comentários para preenchimento automático do endereço via CEP -->
<script>
    // Aguarda o carregamento do DOM //
    document.addEventListener("DOMContentLoaded", function() {
        const cepInput = document.getElementById("cep");

        // Quando o campo de CEP perde o foco //
cepInput.addEventListener("blur", function() {
            let cep = cepInput.value.replace(/\D/g, ''); // Remove tudo que não for número //

            // Se o CEP tiver exatamente 8 dígitos //
if (cep.length === 8) {
                fetch(`https://viacep.com.br/ws/${cep}/json/`)
                    .then(response => {
                        if (!response.ok) {
                            throw new Error('Erro ao buscar o CEP');
                        }
                        return response.json(); // Converte a resposta em JSON //
                    })
                    .then(data => {
                        if (data.erro) {
                            alert("CEP não encontrado.");
                            return;
                        }
                        // Preenche os campos do formulário com os dados recebidos //
                        document.getElementById("rua").value = data.logradouro;
                        document.getElementById("bairro").value = data.bairro;
                        document.getElementById("cidade").value = data.localidade;
                        document.getElementById("uf").value = data.uf;
                    })
                    .catch(error => {
                        console.error("Erro na busca do CEP: ", error);
                        alert("Não foi possível buscar o endereço.");
                    });
            } else {
                alert("Formato de CEP inválido. Deve conter 8 dígitos numéricos.");
            }
        });
    });
</script>
