Koneksi.php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db   = "latihan1";

$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false)
{
    echo "Koneksi ke server gagal.";
    die();
} #else echo "Koneksi berhasil";
?>



index.php
<?php
include("koneksi.php");

// query untuk menampilkan data
$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);

?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Data Barang</title>
</head>
<body>
    <div class="container">
        <h1>Data Barang</h1>
        <div class="main">
            <table>
            <tr>
                <th>Gambar</th>
                <th>Nama Barang</th>
                <th>Kategori</th>
                <th>Harga Jual</th>
                <th>Harga Beli</th>
                <th>Stok</th>
                <th colspan="2">Aksi</th>
            </tr>
            <?php if($result): ?>
            <?php while($row = mysqli_fetch_array($result)): ?>
            <tr>
                <td><img src="gambar/<?= $row['gambar'];?>" alt="<?=$row['nama'];?>"></td>
                <td><?=$row['nama'];?></td>
                <td><?=$row['kategori'];?></td>
                <td><?=$row['harga_beli'];?></td>
                <td><?=$row['harga_jual'];?></td>
                <td><?=$row['stok'];?></td>
                <td><?=$row['id_barang'];?></td>
                <td><a href='? kode=$tampil[id_barang]'>hapus</td>
            </tr>
            <?php endwhile; else: ?>
            <tr>
                <td colspan="7">Belum ada data</td>
            </tr>
            <?php endif; ?>
            </table>

            <?php
            if(isset($_GET['kode'])){

                mysqli_query($id_barang,"delete from barang where id_barang='$_GET[kode]'");

                echo "data telah terhapus";
                echo "<meta http-equiv=refresh content=2;URL='index.php'>";

            }
            ?>
        </div>
    </div>
</body>
</html>



tambah.php
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

if (isset($_POST['submit']))
{

    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];
    $gambar = null;
    if ($file_gambar['error'] == 0)
    {
        $filename = str_replace(' ', '_',$file_gambar['name']);
        $destination = dirname(__FILE__) . '/gambar/' . $filename;
        if(move_uploaded_file($file_gambar['tmp_name'], $destination))
        {
            $gambar = 'gambar/' . $filename;;
        }
    }
$sql = 'INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) ';
$sql = "VALUE ('{$nama}', '{$kategori}', '{$harga_jual}', '{$harga_beli}', '{$stok}', '{$gambar}')";
$result = mysqli_query($conn, $sql);
header('location: index.php');
}

?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Tambah Barang</title>
</head>
<body>
<div class="container">
    <h1>Tambah Barang</h1>
    <div class="main">
        <form method="post" action="tambah.php" enctype="multipart/form-data">
        <div class="input">
            <label>Nama Barang</label>
            <input type="text" name="nama" />
        </div>
        <div class="input">
            <label>Kategori</label>
            <select name="kategori">
                <option value="Komputer">Komputer</option>
                <option value="Elektronik">Elektronik</option>
                <option value="Handphone">Handphone</option>
            </select>
        </div>
        <div class="input">
            <label>Harga jual</label>
            <input type="text" name="harga_jual" />
        </div>
        <div class="input">
            <label>Harga beli</label>
            <input type="text" name="harga_beli" />
        </div>
        <div class="input">
            <label>Stok</label>
            <input type="text" name="stok" />
        </div>
        <div class="input">
            <label>File Gambar</label>
            <input type="file" name="file_gambar" />
        </div>
        <div class="submit">
            <input type="submit" name="submit" value="simpan" />
        </div>
    </form>
</div>
</div>
</body>
</html>


ubah.php
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

if (isset($_POST['submit']))
{
    $id  = $_POST['id'];
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];
    $gambar = null;

    if ($file_gambar['error'] == 0)
    {
        $filename = str_replace(' ', '_', $file_gambar['name']);
        $destination = dirname(__FILE__) . '/gambar/' . $filename;
        if (move_uploaded_file($file_gambar['tmp_name'], $destination))
        {
            $gambar = 'gambar/' . $filename;;
        }
    }

    $sql = 'UPDATE data_barang SET ';
    $sql .= "nama = '{$nama}', kategori = '{kategori}', ";
    $sql .= "harga_jual = '{$harga_jual}', harga_beli = '{$harga_beli}', stok = '{$stok}' ";
    if(!empty($gambar))
        $sql .= ",gambar = '{gambar}' ";
    $sql .= "WHERE id_barang = '{id}' ";
    $result = mysqli_query($conn, $sql);

    header('location: index.php');
}

$id = $_GET['id'];
$sql = "SELECT * FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
if (!$result) die('error: Data tidak tersedia');
$data = mysqli_fetch_array($result);

function is_select($var, $val) {
    if($var == $val) return 'selected="selected"';
    return false;
}

?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Ubah Barang</title>
</head>
<body>
<div class="container">
    <h1>Ubah Barang</h1>
    <div class="main">
        <from method="post" action="ubah.php" enctype="multipart/form-data">
        <div class="input">
            <label>Nama Barang</label>
            <input type="text" name="nama" value="<?php echo $data['nama'];?>" />
        </div>
        <div class="input">
            <label>Kategori</label>
            <select name="kategori">
                <option <?php echo is_select('Komputer', $data['kategori']);?> value="komputer">Komputer</option>
                <option <?php echo is_select('komputer', $data['kategori']);?> value="Elektronik">Elektronik</option>
                <option <?php echo is_select('komputer', $data['kategori']);?> value="HandPhone">HandPhone</option>
            </select>
        </div>
        <div class="input">
            <label>Harga Jual</label>
            <input type="text" name="harga_jual" value="<?php echo $data['harga_jual'];?>" />
        </div>
        <div class="input">
            <label>Harga Beli<label>
            <input type="text" name="harga_beli" value="<?php echo $data['harga_beli'];?>" />
        </div>
        <div class="input">
            <label>Stok</label>
            <input type="text" name="stok" value="<?php echo $data['stok'];?>" />
        </div>
        <div class="input">
            <label>File Gambar</label>
            <input type="file" name="file_gambar" />
        </div>
        <div class="submit">
        <input type="hidden" name="id" value="<?php echo $data['id_barang'];?>" />
            <input type="submit" name="submit" value="simpan" />
        </div>
    </from>
</div>
</div>
</body>
</html>



hapus.php
<?php
include_once 'koneksi.php';
$id = $_GET['id_barang'];
$sql = "DELETE FROM Data_Barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
header('location: index.php');
?>


<?php 
        if(isset($_POST['simpan']))
        {
            $nama           = $_POST['nama'];
            $kategori       = $_POST['kategori'];
            $harga_jual     = $_POST['harga_jual'];
            $harga_beli     = $_POST['harga_beli'];
            $stok           = $_POST['stok'];
            $id_barang      = $_POST['id_barang'];

            mysqli_query($koneksi, "UPDATE barang
            SET nama='$nama', kategori='$kategori', harga_jual='$harga_jual', harga_beli='$harga_beli', stok='$stok', id_barang='$id_barang'
            WHERE id_barang='$id'") or die(mysql_error($koneksi));

            echo "<div align='center'><h5> Silahkan tunggu, data sedang di update... </h5></div>";
            echo "<meta http-equiv='refresh' content='2;url=http://localhost/lab8_php_database/index.php'>";
        }
?>
