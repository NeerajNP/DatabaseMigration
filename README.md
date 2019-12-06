# DatabaseMigration
Migrate on premises 100 GB database from aws mysql rds to managed mysql digital ocean database.


##Source Code is provide in backup.php


<?php

ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);
error_reporting(E_ALL);

//Credentials of Source Database

$username = "username";
$password = "password";
$hostname = "host";
$database = "database";
$username =escapeshellcmd($username);
$password =escapeshellcmd($password);
$hostname =escapeshellcmd($hostname);
$database =escapeshellcmd($database);


//Credentials Of Target Database

$username2 = "username2";
$password2 = "Password2";
$hostname2 = "host2";
$database2 = "database2";
$username2 =escapeshellcmd($username2);
$password2 =escapeshellcmd($password2);
$hostname2 =escapeshellcmd($hostname2);
 
	

	$con=mysqli_connect($hostname,$username,$password,$database);
	if(mysqli_connect_errno()){
	  	echo "Failed to connect to MySQL: " . mysqli_connect_error();
	}
	//$tablesArray = array('get tabels name in array');
		$tablesArray = array('table1','table2','table3');
		
		foreach($tablesArray as $tables){

			$txt = $tables." -- started at -- ".date('Y-m-d H:i:s')."<br>";
 			$myfile = file_put_contents('logs1.txt', $txt.PHP_EOL , FILE_APPEND | LOCK_EX);

	 		$slot=1;
			$path = "/mnt/db_migrate_volume/".$tables; //set path for backup 
			mkdir($path);
		
			$backupFile= $path.'/'.$tables.'.sql';
			$command = "mysqldump -u$username -p$password -h$hostname $database ".$tables." --quick --single-transaction --max_allowed_packet=512M --compress --opt --no-create-info --skip-triggers   > $backupFile";
			exec($command, $result);

			$txt = $tables." -- middle stage at -- ".date('Y-m-d H:i:s')."<br>";
			$myfile = file_put_contents('logs1.txt', $txt.PHP_EOL , FILE_APPEND | LOCK_EX);

			$command2 = "mysql -u$username2 -p$password2 -h $hostname2 -P 25060 $database2  -f < $backupFile";
	 		exec($command2, $output, $return_var);
	 		if($return_var){
	 			$txt = $tables." -- Complate at -- ".date('Y-m-d H:i:s')."<br>";
				$myfile = file_put_contents('logs1.txt', $txt.PHP_EOL , FILE_APPEND | LOCK_EX);
	 		}
		}
?>



