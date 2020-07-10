# Anket-Uygulamas-
PHP İle Son Derece Profesyonel ve Güvenlikli Anket Uygulaması


//Burası Anketin Tasarım Sayfasıdır.

<?php
require_once("dbbaglantisi.php");
?>
<!DOCTYPE html>
<html lang="tr-TR">
<head>
	<meta http-equiv="Content-Type" content="text/html ; charset=utf-8">
	<meta http-equiv="Content-Language" content="tr">
	<meta charset="utf-8">
	<meta name="description" content="Free Web tutorials">
    <meta name="keywords" content="HTML, CSS, JavaScript">
    <meta name="author" content="John Doe">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">

	<script type="text/javascript" src="javaeklentisi.js" language="javascript"></script>
	<title></title> 
</head>

<body>

<?php
$sorgu 			= $db->prepare("SELECT * FROM anket LIMIT 1");
$sorgu->execute();
$kayitsayisi 	= $sorgu ->rowCount();
$kayitlar 		= $sorgu->fetch(PDO::FETCH_ASSOC);
if($kayitsayisi > 0){
?>
<form action="oyver.php" method="post">
	<div class="card" style="width: 250px ; margin-left: 550px;">
		<table width="250px">
				<tr class="card-header">
					<td colspan="2"><?php echo $kayitlar["soru"]; ?></td>
				</tr>
				<tr>
					<td align="center" width="35px"><input type="radio" name="cevap" value="1"></td>
					<td><?php echo $kayitlar["cevapbir"]; ?></td>
				</tr>
				<tr>
					<td align="center"><input type="radio" name="cevap" value="2"></td>
					<td><?php echo $kayitlar["cevapiki"]; ?></td>
				</tr>
				<tr>
					<td align="center"><input type="radio" name="cevap" value="3"></td>
					<td><?php echo $kayitlar["cevapuc"]; ?></td>
				</tr>
				<tr>
					<td align="right" colspan="2"><button class="btn btn-success" type="submit">Gönder</button></td>
				</tr>
				<tr>
					<td class="card-footer" align="right" colspan="2"><a class="text-muted" style="text-decoration: none; font-size: 14px" href="sonuclar.php">Anket Sonuçlarını Göster</a></td>
				</tr>
		</table>
	</div>
</form>







<?php
}else{
?>
<h4 align="center"><b><i>Anket Bulunamadı.</i></b></h4>
<?php
}
?>














<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
<script src="js/bootstrap.min.js"></script>
</body>
</html>



//Burası Anketin DataBase(Veritabanı) Baglantısıdır.




<?php

try {
	$db = new PDO("mysql:host=localhost;dbname=hkpau;charset=utf8" , "root" , "");
} catch (PDOException $error) {
	echo "Baglantı Hatası.<br/>";
	echo "Hata Kodu : " . $error.getMessage();
	die();
}


function filtrele($deger){
	$bir = trim($deger);
	$iki = strip_tags($bir);
	$uc  = htmlspecialchars($iki , ENT_QUOTES);
	return $uc;
}


?>



//Burası Anketin Oy Verme İşleminin Yapıldıgı Yerdir.



<?php
require_once("dbbaglantisi.php");

$ipadresi 			= $_SERVER["REMOTE_ADDR"];
$zamandamgasi 		= time();
$oykullanmasiniri	= 86400;//sn cinsinden 1 gün...
$zamanigerial 		= $zamandamgasi - $oykullanmasiniri;//Kullanıcının Sadece bir kere ankete katılması için yazılan bir kod.....


if(empty($_POST["cevap"])){
?>
<!DOCTYPE html>
<html lang="tr-TR">
<head>
	<meta http-equiv="Content-Type" content="text/html ; charset=utf-8">
	<meta http-equiv="Content-Language" content="tr">
	<meta charset="utf-8">
	<meta name="description" content="Free Web tutorials">
    <meta name="keywords" content="HTML, CSS, JavaScript">
    <meta name="author" content="John Doe">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">

	<script type="text/javascript" src="javaeklentisi.js" language="javascript"></script>
	<title></title> 
</head>
<body>
<div align="center">
	<div class="card" style="width: 450px">
		<div class="card-header">
			<h4>Lütfen Boş Deger Göndermeyiniz!!!</h4>
		</div>
		<div class="card-body">
			<p>Ana Sayfaya Dönmek için <a href="anket.php">Tıklayınız</a></p>
		</div>
	</div>
</div>


<?php
die();
}else{
$gelencevap 		= $_POST["cevap"];


	$kontrolsorgusu 	= $db->prepare("SELECT * FROM oykullananlar WHERE ipadresi = ? AND tarih >= ?");
	$kontrolsorgusu->execute([$ipadresi , $zamanigerial]);
	$kayitsayisi 		= $kontrolsorgusu->rowCount();
	if($kayitsayisi > 0){
		echo "Hata.<br/>";
		echo "Daha Önceden Oy Kullandıgınız için Oy verme işleminiz gerçekleştirilemiyor.Bir sonraki oy verme işlemi için en az  güç geçmesi gerekmektedir.";
	}else{
		if($gelencevap == 1){
			$guncelle = $db->query("UPDATE anket SET oysayisibir = oysayisibir + 1 ,toplamoysayisi = toplamoysayisi + 1");
			$guncelle->execute();
		}elseif ($gelencevap == 2) {
			$guncelle = $db->query("UPDATE anket SET oysayisiiki = oysayisiiki + 1 ,toplamoysayisi = toplamoysayisi + 1");
			$guncelle->execute();
		}elseif ($gelencevap == 3) {
			$guncelle = $db->query("UPDATE anket SET oysayisiuc = oysayisiuc + 1 ,toplamoysayisi = toplamoysayisi + 1");
			$guncelle->execute();
		}else{
			echo "Hata.<br/>";
			echo "Cevap Bulunamadı.\t<a href='anket.php'>Ana Sayfaya Dönmek için Tıklayınız.</a>";
		}
	}
}


$ekle = $db->prepare("INSERT INTO oykullananlar (ipadresi , tarih) values ('$ipadresi' , '$zamandamgasi')");
$ekle->execute();
$eklemekontrolu = $ekle->rowCount();
if($eklemekontrolu > 0){
	echo "Oy Verdiginiz Icin Tesekkürler.\t <a href='anket.php'>Ana Sayfaya Dönmek İçin Tıklayınız.</a>";
}
else{
	echo "Oy Verme Sırasında Bir HATA Meydana Geldi.<br />";
	echo "<a href='anket.php'>Ana Sayfaya Dönmek İçin Tıklayınız.</a>";
}
?>


<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
<script src="js/bootstrap.min.js"></script>
</body>
</html>




//Burası Anketin Gelen Cevaplar Sonucu Oluşan Yüzdesel Dilimlerin Oldugu Alandır.



<?php
require_once("dbbaglantisi.php");
?>
<!DOCTYPE html>
<html lang="tr-TR">
<head>
	<meta http-equiv="Content-Type" content="text/html ; charset=utf-8">
	<meta http-equiv="Content-Language" content="tr">
	<meta charset="utf-8">
	<meta name="description" content="Free Web tutorials">
    <meta name="keywords" content="HTML, CSS, JavaScript">
    <meta name="author" content="John Doe">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">

	<script type="text/javascript" src="javaeklentisi.js" language="javascript"></script>
	<title></title> 
</head>

<body>

<?php

$oku = $db->query("SELECT * FROM anket");
$kayit = $oku->fetch(PDO::FETCH_ASSOC);
$birincioysayisi 	= $kayit["oysayisibir"];
$ikincioysayisi 	= $kayit["oysayisiiki"];
$ucuncuoysayisi 	= $kayit["oysayisiuc"];
$toplamoy 		 	= $kayit["toplamoysayisi"];

$birincioyyuzde 			= ($birincioysayisi / $toplamoy) * 100;
$birincioyyüzdesiformat 	= number_format($birincioyyuzde , 2 , "," , "");
$ikincioyyuzde 				= ($ikincioysayisi / $toplamoy) * 100;
$ikincioyyüzdesiformat 		= number_format($ikincioyyuzde , 2 , "," , "");
$ucuncuoyyuzde 				= ($ucuncuoysayisi / $toplamoy) * 100;
$ucuncuoyyüzdesiformat 		= number_format($ucuncuoyyuzde , 2 , "," , "");

?>

	<table align="center">
		<tr>
			<td class="card-header" colspan="2">Anket Sonuclarımız</td>
		</tr>
		<tr>
			<td><?php echo $kayit["cevapbir"]; ?></td>
			<td><?php echo $birincioyyüzdesiformat; ?></td>
		</tr>
		<tr>
			<td><?php echo $kayit["cevapiki"]; ?></td>
			<td><?php echo $ikincioyyüzdesiformat; ?></td>
		</tr>
		<tr>
			<td><?php echo $kayit["cevapuc"]; ?></td>
			<td><?php echo $ucuncuoyyüzdesiformat ?></td>
		</tr>
		<tr>
			<td><?php echo $kayit["toplamoysayisi"]; ?></td>
			<td><?php echo $toplamoy; ?></td>
		</tr>
		<tr class="card-footer">
			<td colspan="2"><a class="text-muted" style="text-decoration: none" href="anket.php">Ankete Dönmek için Tıklayınız</a></td>
		</tr>
	</table>


<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
<script src="js/bootstrap.min.js"></script>
</body>
</html>
