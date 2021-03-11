# PHP-Crypto
Originally designed to Import and Export data securely via a single GET request parameter called payload, it has now become a simple example of encryption and decryption using PHP 7 >. 
  
The original concept was to challenge the free hosting restrictions on AwardSpace to allow data transmission inward and outward in a secure manner, i found to use get request via `header('Location: $url."?payload=");` to import data and export data between two scripts using the browser - the test was successful and i now have a secure email backend for my account.   

   
   
```
<?php
// --------------------
//  PHP Crypto
// --------------------


// input payload
$payload = "bkhgghvghvvhuvhgyyhh";
// set scripts hidden password
// This password is secured will only be used in conjunction with a secure random byte salt.
$password = "Password";


// start ms consumption timer
timer(true);

// encrypt payload example
$encrypted_payload = crypto($payload,$password);

// decrypt payload example
$decrypted_payload = crypto($encrypted_payload,$password,true);

// stop timer & get ms consumption
$consumption =  timer(false);
 
// return time consumption of operation
echo 'Time comsumption = '.$consumption.' ms';



// Crypto 2-Way Random Encrypt/Decrypt Function
function crypto($payload, $password, $doDecrypt = false)
{
$encryption_protocol = ('aes-256-cbc');
$output = '';
if($doDecrypt)
{
    $payload = urlDecode($payload);
    list($aes256iv, $encrypted_payload, $decryption_salt) = explode('::', base64_decode($payload), 3);
  $encryption_key = base64_decode($password.$decryption_salt);
    $output = openssl_decrypt($encrypted_payload, $encryption_protocol, $encryption_key, 0, $aes256iv);
}
else
{
$encryption_salt = openssl_random_pseudo_bytes(10);
$encryption_key = base64_decode($password.$encryption_salt);
    $aes256iv = openssl_random_pseudo_bytes(openssl_cipher_iv_length($encryption_protocol));
    $encrypted_payload = openssl_encrypt($payload, $encryption_protocol, $encryption_key, 0, $aes256iv);
    $output = base64_encode($aes256iv . '::' . $encrypted_payload. '::'.$encryption_salt);
}
return urlEncode($output);
}


// Timer Function & Global Time Start Variable
$milliStart = 0;
function timer($start)
{
global $milliStart;
$out = 0;
if ($start)
{
$milliStart = round(microtime(true)*1000);
}
else
{
  $out = round(microtime(true)*1000);
  $out = $out - $milliStart;
  $milliStart = 0;
}
 return $out;
}

?>
```
