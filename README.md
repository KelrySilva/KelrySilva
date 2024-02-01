Arquivo: index.php.

<html>
	<head>
	<meta charset="utf-8" />
    <title>App Mail Send</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

	</head>

<body>
	<div class="container">  

		<div class="py-3 text-center">
			<img class="d-block mx-auto mb-2" src="logo.png" alt="" width="72" height="72">
			<h2>Send Mail</h2>
			<p class="lead">Seu app de envio de e-mails particular!</p>
		</div>

    <div class="row">
        <div class="col-md-12">
	        <div class="card-body font-weight-bold">
				<form action="processa_envio.php" method="post">
					<div class="form-group">
						<label for="para">Para</label>
							<input name="para" type="text" class="form-control" id="para" placeholder="joao@dominio.com.br">
					</div>

		<div class="form-group">
			<label for="assunto">Assunto</label>
				<input name="assunto" type="text" class="form-control" id="assunto" placeholder="Assundo do e-mail">
		</div>

		<div class="form-group">
			<label for="mensagem">Mensagem</label>
				<textarea name="mensagem" class="form-control" id="mensagem"></textarea>
		</div>

		<button type="submit" class="btn btn-primary btn-lg">Enviar Mensagem</button>

		
</form>
</div>
</div>
</div>
</div>
</body>
</html>

Arquivo: processa_envio.php

<?php

	require "./biblioteca/phpMailer/Exception.php";
	//require "./src/OAuth.php";
	require "./biblioteca/phpMailer/PHPMailer.php";
	require "./biblioteca/phpMailer/POP3.php";
	require "./biblioteca/phpMailer/SMTP.php";

	use PHPMailer\PHPMailer\PHPMailer;
	use PHPMailer\PHPMailer\Exception;

	//print_r($_POST);

	class Mensagem {
		private $para = null;
		private $assunto = null;
		private $mensagem = null;
		public $status = array( 'codigo_status' => null, 'descricao_status' => '' );

		public function __get($atributo) {
			return $this->$atributo;
		}

		public function __set($atributo, $valor) {
			$this->$atributo = $valor;
		}

		public function mensagemValida() {
			if(empty($this->para) || empty($this->assunto) || empty($this->mensagem)) {
				return false;
			}

			return true;
		}
	}

	$mensagem = new Mensagem();

	$mensagem->__set('para', $_POST['para']);
	$mensagem->__set('assunto', $_POST['assunto']);
	$mensagem->__set('mensagem', $_POST['mensagem']);

	//print_r($mensagem);

	if(!$mensagem->mensagemValida()) {
		echo 'Mensagem não é válida';
		die();
	}

	$mail = new PHPMailer(true);
	try {
			//Server settings
			$mail->SMTPDebug = false;                      //Enable verbose debug output
			$mail->isSMTP();                                            //Send using SMTP
			$mail->Host       = 'smtp.gmail.com';                     //Set the SMTP server to send through
			$mail->SMTPAuth   = true;                                   //Enable SMTP authentication
			$mail->Username   = 'silvakelry714@gmail.com';                       //SMTP username
			$mail->Password   =  'pzvh zgju pxuleykw';                                    //SMTP password
			$mail->SMTPSecure = PHPMailer::ENCRYPTION_STARTTLS;         //Enable TLS encryption; PHPMailer::ENCRYPTION_SMTPS encouraged
			$mail->Port       = 587;                                    //TCP port to connect to, use 465 for PHPMailer::ENCRYPTION_SMTPS above

			//Recipients
			$mail->setFrom('silvakelry714@gmail.com', 'Web Completo Remetente');
			$mail->addAddress($mensagem->__get('para'));     //Add a recipient
			//$mail->addReplyTo('info@example.com', 'Information');
			//$mail->addCC('cc@example.com');
			//$mail->addBCC('bcc@example.com');

			//Attachments
			//$mail->addAttachment('/var/tmp/file.tar.gz');         //Add attachments
			//$mail->addAttachment('/tmp/image.jpg', 'new.jpg');    //Optional name

			//Content
			$mail->isHTML(true);                                  //Set email format to HTML
			$mail->Subject = $mensagem->__get('assunto');
			$mail->Body    = $mensagem->__get('mensagem');
			$mail->AltBody = 'É necessario utilizar um client que suporte HTML para ter acesso total ao conteúdo dessa mensagem';

			$mail->send();

			$mensagem->status['codigo_status'] = 1;
			$mensagem->status['descricao_status'] = 'E-mail enviado com sucesso';

	} catch (Exception $e) {
			
			$mensagem->status['codigo_status'] = 2;
			$mensagem->status['descricao_status'] = 'Não foi possivel enviar este e-mail! Por favor tente novamente mais tarde. Detalhes do erro: ' . $mail->ErrorInfo;
	}
?>

<html>
	<head>
		<meta charset="utf-8" />
    	<title>App Mail Send</title>

    	<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
	</head>

	<body>

		<div class="container">
			<div class="py-3 text-center">
				<img class="d-block mx-auto mb-2" src="logo.png" alt="" width="72" height="72">
				<h2>Send Mail</h2>
				<p class="lead">Seu app de envio de e-mails particular!</p>
			</div>

			<div class="row">
				<div class="col-md-12">

					<?php if($mensagem->status['codigo_status'] == 1) { ?>

						<div class="container">
							<h1 class="display-4 text-success">Sucesso</h1>
							<p><?= $mensagem->status['descricao_status'] ?></p>
							<a href="index.php" class="btn btn-success btn-lg mt-5 text-white">Voltar</a>
						</div>

					<?php } ?>

					<?php if($mensagem->status['codigo_status'] == 2) { ?>

						<div class="container">
							<h1 class="display-4 text-danger">Ops!</h1>
							<p><?= $mensagem->status['descricao_status'] ?></p>
							<a href="index.php" class="btn btn-success btn-lg mt-5 text-white">Voltar</a>
						</div>

					<?php } ?>

				</div>
			</div>
		</div>

	</body>
</html>

CRIE UMA PASTA CHAMADA BIBLIOTECA E NESSA PASTA COLOQUE OS ARQUIVOS DA PASTA phpMailer .




