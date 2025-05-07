# SLOTH
عمل موجه للكسول
<?php

// Initialize result variables
$mes1 = "";
$result = "";

// Setup database connection (PDO)
$host = 'localhost';
$db = 'new_database';
$user = 'root';
$pass = '';
$charset = 'utf8mb4';

$dsn = "mysql:host=$host;dbname=$db;charset=$charset";

$options = [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
];

try {
    $pdo = new PDO($dsn, $user, $pass, $options);
} catch (\PDOException $e) {
    die("Database connection failed: " . $e->getMessage());
}

// Check if the form is submitted
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    // Get values from the form
    $name = trim($_POST['name']);
    $height = floatval($_POST['height']);
    $weight = floatval($_POST['weight']);
    $age = intval($_POST['age']);

    // Check if any field is empty
    if ($name === "" || $height === 0 || $weight === 0 || $age === 0) {
        $result = " <p style='color: red ; font-size:33px ;'> Please fill in all the information correctly.</p>";
    } else {
        // Validate the height, weight, and age
        if ($height <= 0 || $weight <= 0 || $age <= 0) {
            $result = " <p style='color: red ; font-size:33px ;'> ERROR: Invalid (negative or zero) values for height, weight, or age. </p> ";
        } else {
            // Insert into database
            $sql = "INSERT INTO persons (name, height, weight, age) VALUES (?, ?, ?, ?)";
            $stmt = $pdo->prepare($sql);
            $stmt->execute([$name, $height, $weight, $age]);

            // Display success message
            $result = " <p font-size:33px ;'> Hello, $name, your height is: $height meters, your weight is: $weight kg, and your age is: $age years.</p>";
        }

        // Analyze weight
        if ($weight <= 25) {
            $mes1 = " <p '> Underweight – You may need to gain weight. Consult with a healthcare provider for proper nutrition.</p>";
        } elseif ($weight > 25 && $weight <= 50) {
            $mes1 = "<p '> Normal weight – You have a healthy weight. Keep maintaining a balanced diet and regular exercise. </p>";
        } elseif ($weight > 50 && $weight <= 80) {
            $mes1 = "<p '> Overweight – You are slightly overweight. Consider regular physical activity and a balanced diet.</p>";
        } else {
            $mes1 = "<p '>Obesity – You should consult with a healthcare provider about a weight loss plan and fitness regime.</p>";
        }
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Find Name, Height, Weight, and Age</title>
</head>
<script>
        function gets_in() {
            window.location.href="http://localhost/phpmyadmin/index.php?route=/sql&pos=0&db=new_database&table=persons";
        }
    </script>
<body >

<h1>Enter Your Information:</h1>
    <hr>
    <br>
    <form method="POST">
        <label for="name">Enter your name:</label>
        <input type="text" name="name" style="border-radius: 33px ; border-width: 4px" required><br><br>

        <label for="height">Enter your height (in meters):</label>
        <input type="number" step="any" name="height" style="border-radius: 33px ; border-width: 4px" required><br><br>

        <label for="weight">Enter your weight (in kg):</label>
        <input type="number" step="any" name="weight" style="border-radius: 33px ; border-width: 4px" required><br><br>

        <label for="age">Enter your age (in years):</label>
        <input type="number" name="age" style="border-radius: 33px ; border-width: 4px" required><br><br>
        <br>
        <br>
        <input type="submit" value="Submit" >
        <br>
        <br>
        <button onclick="gets_in()">
        <h1> History </h1>
    </button> 
    </form>

    <p><?php echo $result; ?></p>
    <i><p><?php echo $mes1; ?></p></i>
</body>
</html>
