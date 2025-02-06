<?php
// DTF Gang Sheet Creator - Basic PHP Version

// Define canvas dimensions (default to 22" x 24")
$canvasWidth = isset($_POST['canvas_width']) ? intval($_POST['canvas_width']) : 22 * 300; // 300 DPI
$canvasHeight = isset($_POST['canvas_height']) ? intval($_POST['canvas_height']) : 24 * 300;
$spacing = isset($_POST['spacing']) ? intval($_POST['spacing']) : 10; // Default spacing

// Create blank canvas
$image = imagecreatetruecolor($canvasWidth, $canvasHeight);
$white = imagecolorallocate($image, 255, 255, 255);
imagefill($image, 0, 0, $white);

if (!empty($_FILES['uploads'])) {
    $x = $spacing;
    $y = $spacing;
    
    foreach ($_FILES['uploads']['tmp_name'] as $key => $tmpName) {
        $uploadedImage = imagecreatefrompng($tmpName);
        if (!$uploadedImage) continue;
        
        $imgWidth = imagesx($uploadedImage);
        $imgHeight = imagesy($uploadedImage);
        
        if ($x + $imgWidth > $canvasWidth) {
            $x = $spacing;
            $y += $imgHeight + $spacing;
        }
        
        if ($y + $imgHeight > $canvasHeight) {
            break;
        }
        
        imagecopy($image, $uploadedImage, $x, $y, 0, 0, $imgWidth, $imgHeight);
        imagedestroy($uploadedImage);
        
        $x += $imgWidth + $spacing;
    }
}

// Output the final gang sheet
header("Content-Type: image/png");
imagepng($image);
imagedestroy($image);
?>
